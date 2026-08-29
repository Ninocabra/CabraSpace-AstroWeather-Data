# AstroWeather — la noche de Nerpio, en abierto

Prevision de condiciones de observacion astronomica para
**AstroCamp (Nerpio, Albacete, MPC I79)**, a 1.650 m.

Este repositorio es la **salida publica** del motor
[CabraSpace-AstroWeather](https://github.com/Ninocabra/CabraSpace-AstroWeather),
que es privado. Aqui no hay codigo: hay un fichero que responde a las dos
preguntas por las que existe el sistema.

1. **Estaran abiertos los techos esta noche, o la siguiente?**
2. **Que condiciones hay, y que merece la pena apuntar?**

## El fichero

```
https://raw.githubusercontent.com/Ninocabra/CabraSpace-AstroWeather-Data/main/noche.json
```

Se reescribe **cada 6 horas**. La URL no cambia nunca, asi que se puede leer
desde una pagina sin servidor y sin CORS.

**El historial esta en git.** Como el fichero se sobrescribe siempre en la misma
ruta, el `git log` de `noche.json` es el registro completo de lo que dijimos y
cuando lo dijimos, diffeable. Es a proposito: una prevision que se puede
reescribir sin dejar rastro no se puede verificar.

## Lo que contiene

| Bloque | Que es |
|---|---|
| `sitio` | Coordenadas y cota del observatorio |
| `noches[]` | Una entrada por noche, etiquetada por el dia en que EMPIEZA |
| `noches[].probabilidad_de_abrir` | Probabilidad calibrada de que la noche sea utilizable |
| `noches[].horas[]` | Hora a hora: nubes previstas, probabilidad, que limita y si es seguro abrir |
| `noches[].cielo` | Fondo de cielo, seeing en terciles y transparencia, cada uno con su origen |
| `noches[].recomendaciones[]` | Objetos del catalogo, con horas equivalentes de SNR si despeja y esperadas |
| `confianza` | Con que se calibro, sobre cuantos pares, que BSS saca sobre climatologia, y los avisos vivos |

## La regla que no se salta

**Nada se publica sin su incertidumbre.** Por eso cada numero viaja con su
procedencia dentro del propio JSON — si el aerosol sale de CAMS o de un
supuesto, si el seeing sale del ECMWF o de una constante — y hay una lista
`confianza.avisos` con lo que hoy mismo no cuadra. Esa lista **no es
decorativa**: si dice que la curva de calibracion se esta aplicando a un modelo
distinto del que se entreno, la probabilidad vale menos de lo que dice su BSS.
Quien consuma esto deberia enseñar los avisos, no solo los numeros.

El seeing se publica **en terciles y nunca en arcosegundos sueltos**: el modelo
es experimental y no esta calibrado contra FWHM medido en el sitio.

## De donde salen los datos

El JSON es un producto DERIVADO. Los datos de partida son de terceros y cada uno
tiene sus propias condiciones:

- **[Open-Meteo](https://open-meteo.com/)** — modelos deterministas y el
  ensemble de 51 miembros del ECMWF. Datos bajo CC BY 4.0, derivados de
  servicios meteorologicos nacionales.
- **[CAMS](https://atmosphere.copernicus.eu/)** (Copernicus) — aerosol y polvo.
- **[AEMET OpenData](https://opendata.aemet.es/)** — estaciones cercanas.
- **AstroCamp** — sensores del sitio y estado de los techos.
- **[STARS4ALL / TESS](https://tess.stars4all.eu/)** — fotometro de fondo de
  cielo.

Los datos **en bruto de esas fuentes no se republican aqui**: lo que se publica
es nuestra prevision. Si necesitas la serie original, ve a la fuente.

## Licencia

Los datos DERIVADOS de este repositorio (el contenido de `noche.json`) se
publican bajo **CC BY 4.0** — ver [LICENSE](LICENSE). La atribucion de las
fuentes de partida es la de arriba y viaja tambien dentro del JSON.

## Aviso

Esto es un proyecto personal en desarrollo, no un servicio meteorologico.
**No lo uses para decisiones de seguridad.** El sistema tiene puertas de
seguridad (viento, humedad, hielo) que alimentan el veredicto de abrir, pero
la racha de viento — que es la variable que rompe cupulas — **no tiene ninguna
verdad terreno disponible en la zona** y por tanto no esta verificada.
