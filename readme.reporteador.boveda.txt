Reporteador para Boveda SAIT

La forma cfdiboveda nos permite crear el formato que usaremos para Boveda SAIT

Instalacion
===========
	Descargar www.sait.com.mx/download/reporteador.boveda.zip
	Instalar los archivos incluidos en el directorio de la empresa

Aqui vamos a agregar algunos comentarios de este cambios
Ademas queremos mencionar que aqui y alla se puede hacer esto

Instrucciones de Uso
====================
	Descargar e instalar el reporteador
	Copiar el formato actual a cfdiboveda.frt
		Seleccionar el formato actual de la empresa Utilerias / Formatos de Factura / 
		Dar clic a modificar formato
		Estando en el reporteador de VFP seleccionar SaveAs... Guardar Como...
		Guardar el formato como cfdiboveda
		Esto creara el archivo cfdiboveda.frt y cfdiboveda.frx
	Empezar a mofificar el formato cfdiboveda
		Ir a Utilerias / Modulos Especiales
		Seleccionar "Reportador de Boveda"
		Se abre la ventana de "Reporteador Boveda"
		Dar clic en <Modificar Formato>
		Se abre el reporteador de VFP
		Cambiar todas las expresiones VFP por expresiones PHP (Ver tabla abajo)
		Grabar cambios
	Probar formato
		Estando en la ventana de "Reporteador Boveda"
		Dar clic en <Ver PDF / Probar PDF >
		El sisteman enviara el formato cfdiboveda.frt al webservice de boveda, para ser analizado
		Y regresara un PDF con el resultado
		En caso de error se guardarn en el archivo cfdiboveda.txt

OK Doky

Tabla de Expresiones para Reporteador de Boveda
================================================
Datos del Emisor
	this->cfd->Emisor->nombre
	this->cfd->Emisor->rfc
	this->cfd->Emisor->DomicilioFiscal->calle . ' ' . this->cfd->Emisor->DomicilioFiscal->noExterior . ' ' . this->cfd->Emisor->DomicilioFiscal->colonia
	this->cfd->Emisor->DomicilioFiscal->municipio . ' ' . this->cfd->Emisor->DomicilioFiscal->estado. ' ' . this->cfd->Emisor->DomicilioFiscal->codigoPostal

Datos del Receptor                                                                                
	this->cfd->Receptor->nombre
	this->cfd->Receptor->rfc
	this->cfd->Receptor->Domicilio->calle . ' ' . this->cfd->Receptor->Domicilio->noExterior . ' ' . this->cfd->Receptor->Domicilio->colonia
	this->cfd->Receptor->Domicilio->municipio. ' ' . this->cfd->Receptor->Domicilio->estado. ' ' . this->cfd->Receptor->Domicilio->codigoPostal

Fecha del CFDI y Fecha de timbrado
	mexdate2(this->cfd->fecha) . '  ' . substr(this->cfd->fecha,11)
	mexdate2(this->cfd->Complemento->TimbreFiscalDigital->FechaTimbrado) . '  ' . substr(this->cfd->Complemento->TimbreFiscalDigital->FechaTimbrado,11)


Partidas:
	this->partida->descripcion
	this->partida->descripcion . ' ' . this->infoAduana()	// SI MANEJA ADUANAS
	this->partida->cantidad
	number_format(this->partida->valorUnitario,2)
	number_format(this->partida->valorUnitario * this->partida->cantidad,2)
	this->partida->unidad
	this->partida->noIdentificacion

Totales
	number_format(this->cfd->total,2)
	number_format(this->cfd->subTotal,2)
	this->cfd->Impuestos->Traslados[0]->impuesto . ' ' . this->cfd->Impuestos->Traslados[0]->tasa . '%'
	this->cfd->Impuestos->Traslados[1]->impuesto . ' ' . this->cfd->Impuestos->Traslados[1]->tasa . '%'
	number_format(this->cfd->Impuestos->Traslados[0]->importe,2)
	number_format(this->cfd->Impuestos->Traslados[1]->importe,2)

Datos del sello, cadena, cantidad con letra
	'Sello CFDI: ' . this->cfd->Complemento->TimbreFiscalDigital->selloCFD
	this->cfd->Complemento->TimbreFiscalDigital->UUID
	numeroLetra(this->cfd->total, this->cfd->Moneda)
	'Sello SAT: ' . this->cfd->Complemento->TimbreFiscalDigital->selloSAT
	'Cadena Original: ' . str_replace(' ', '', this->cfd->getCadenaOriginal())
	'Cadena Timbre Fiscal Digital: ' . this->cfd->getCadenaTfd()

Codigo Bidimensional
	this->cfd->getqrcodeinfo() 


Datos en Ultima Pagina
----------------------
Los totales, los sellos, cantidad con letra y el codigo bidimensional,
SOLO DEBEN APARECER en la ultima pagina por tanto a todas esas expresiones,
es necesario agregar la condicion de PrintWhen usando la funcion:
	isLastPage()

En el caso del Total, la expresion es: 	number_format(this->cfd->total,2)
Pero en la condicion PrintWhen, pondremos: isLastPage()

En el caso de la leyenda del IEPS (Impuesto2) la condicion en PrintWhen sera:
	isLastPage() and isset(this->cfd->Impuestos->Traslados[1])

