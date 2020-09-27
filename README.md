# Cuidar iOS

Se publica aquí el código fuente de la versión iOS de la aplicación Cuidar. Este código fuente es el *trabajo en curso* hacia la versión 3.5.4. En el `Changelog.txt` se puede ver el historial de cambios y las novedades que contendrá esta versión.

Esta aplicación es el método elegido por millones de ciudadanos y ciudadanas de Argentina para mostrar sus certificados de circulación en el contexto de la pandemia de COVID-19, así como también para hacerse autodiagnósticos que les permitan saber si deben consultar al sistema de salud y ser derivados al Comité Operativo de Emergencia Provincial de su jurisdicción.

![Aplicación Cuidar](/CovidApp/CovidApp/Assets.xcassets/AppIcon.appiconset/Icon-180.png)

La aplicación puede descargarse [aquí](https://www.argentina.gob.ar/aplicaciones/coronavirus).

**¿Tenés dudas, comentarios, o sugerencias? ¿Encontraste algo que te parece que deberíamos arreglar, cambiar o corregir?** Te escuchamos [aquí](https://www.argentina.gob.ar/aplicaciones/coronavirus/contanos-sobre-la-app-cuidar-covid-19).

# Aclaración

Esta aplicación utiliza para su autenticación la combinación de DNI y número de trámite. Ese mecanismo es imperfecto, pero no es viable reemplazarlo sin incurrir en "alternativas" que resultan más invasivas, más "pesadas" o inviables en un contexto de pandemia donde es necesario desplegar rápidamente una aplicación que utilicen millones de ciudadanos y ciudadanas con diversa experiencia en tecnología.

Para proteger a la ciudadanía de versiones que imposten a la aplicación oficial o, que atenten contra la privacidad de datos resguardados por la misma utilizando listados conseguidos ilegalmente, la versión aquí expuesta no incluye las claves de autenticación reales contra el backend.

# Permisos

* Cámara: Utilizado para escanear el DNI.

* Ubicación: En caso de autodiagnóstico positivo, se solicita por única vez la geolocalización para determinar el Consejo Operativo de Emergencia Provincial apropiado para derivar al ciudadano. La Argentina tiene un sistema de salud federal, lo que significa que cada provincia tiene sus propios mecanismos de atención de salud.

    * Si le usuarie deniega el permiso, la aplicación funciona con normalidad utilizando la dirección declarada previamente.

    * Si aún así, alguien no desea usar la aplicación, puede portar el certificado de circulación en papel.

* Conexión de red: para poder enviar y recibir datos. El uso de los datos móviles de la aplicación Cuidar no tiene costo.

# Historia

La aplicación fue desarrollada originalmente en conjunto entre la Secretaría de Innovación Pública -siguiendo los requerimientos y necesidades del Ministerio de Salud-, el Ministerio de Ciencia y Tecnología de la Nación, la Fundación Sadosky, el Consejo Nacional de Investigaciones Científicas y Técnicas (CONICET) y la Cámara de la Industria Argentina del Software (CESSI), que nucleó a las empresas Hexacta, Globant, G&L Group, C&S, QServices, GestiónIT, Intive, Finnegans y Faraday. Asimismo, el equipo se complementó con el trabajo de Arsat, la empresa de telecomunicaciones del Estado, y los servicios brindados por Amazon Web Services, RedHat Argentina, Thinkly y Biodyn SAS. Las empresas antes mencionadas ofrecieron sus servicios como donación al Estado Argentino. Hoy en día el mantenimiento y mejora de la aplicación es realizado por el Estado Nacional a través de la Secretaría de Innovación Pública.

# Próximos pasos

Más allá de las mejoras funcionales que se incorporen por requerimiento de las autoridades sanitarias, estamos trabajando en automatizar una parte de nuestra batería de tests, lo que va a requerir cierto refactoring de las vistas principales y facilitar futuras extensiones. También trabajremos en homogeneizar el manejo de dependencias.

# Contribución

¿Querés contribuir con el proyecto? Dejanos tu PR en github. Por favor documentá claramente el objetivo del cambio y ayudanos a mantener la homogeneidad del código (estamos trabajando en hacerlo más homogéneo).

## Instalación

* Versión mínima de iOS: 11
* Última versión de iOS durante el desarrollo del proyecto: 13.4
* Versión mínima de Xcode: 11.3.1 (11C504)
* La aplicación [Swift Package Manager](https://swift.org/package-manager/) como sistema de manejo de dependencias. Para compilar el proyecto necesitás:

### NewRelic 

* Agregarle el paquete de NewRelic (en ese caso poner en `true` la variable `USAR_NEWRELIC` en `CovidApp/CovidApp/Dominio/Constantes/GlobalKeys.swift`) o eliminarlo del proyecto (Project Settings ▸ Build Phases ▸ eliminar New Relic, Build Settings ▸ Swift Compiler ▸ General ▸ Objective-C Bridging Header ▸ vaciarlo y eliminar -DNEWRELIC de Other Swift Flags).
* Tal vez necesites Project ▸ Clean Build Folder.

### Swift Package Manager
* Swift-tools-version: 5.1
* Para descargar y actualizar la última versión de los paquetes en la toolbar seguí estos pasos:
File ▸ Swift Packages ▸ Update to Latest Package Versions. Esto actualizará y descargará los paquetes de los frameworks.

## Arquitectura

La app sigue el patron arquitectonico [MVP](https://en.wikipedia.org/wiki/Model–view–presenter), la capa de domain (Model) se basa en el patrón [fachada](https://en.wikipedia.org/wiki/Facade_pattern) para simplificar y unificar la manera en que los presenters acceden a la información. El proyecto tiene la capacidad de usar fachadas Mock, únicamente en debug mode y con xcode corriendo, especificando en Arguments Passed On Launch en el scheme la flag  `"com.covid.usemockfachadas"`. En este caso todas las llamadas a las fachadas simpre son exitosas y retornan mock data. Esto es útil para debugging. Si el flag está desactivado las fachadas reales son usada haciendo las llamadas de servicio reales.

La información sensible es cacheada en la Keychain, usando el Security Framework de Apple. Muchas de las vistas son creadas dinámicamente usando el patrón [Visitor](https://en.wikipedia.org/wiki/Visitor_pattern). Así que si las fuentes de información cambian la UI se actualiza. Esta flexibilidad está dada para los síntomas y antecedentes en el flujo de autoevaluación.
