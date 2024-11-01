# AL FINALIZAR LA GUIA, DEBERIAS TENER LOS MISMOS ARCHIVOS CONTENIDOS EN ESTE REPOSITORIO



# 1.  CREACION DE DIRECTORIO Y ARCHIVO BASE.

##  Nos ubicamos en el directorio donde queremos trabajar.

```bash
cd ~/Desktop
```
	
## Creamos el directorio de trabajo.

```bash
mkdir TextFormats
```

## Nos Ubicamos en el directorio que creamos.

```bash
cd TextFormats
```

## Creamos el archivo "replica.txt" y lo rellenamos con el contenido que deseamos:

Creamos el archivo replica.txt.
```bash
touch replica.txt
```

Rellenamos el archivo replica.txt con el siguiente contenido:
```
Esta maquina posee seis diales de giratorios divididos cada 36. Es una antigüedad del año 1850,replica de la PASCALINA. Que bien conservada esta!
```

Si ejecutamos los comandos:
```bash
ls -lh replica.txt
file replica.txt
xxd replica.txt
```
Nos daremos cuenta que el archivo:
* Tiene tamaño 149 bytes.
* No posee el byte "OD que le corresponde al CR".
* Contiene "C3BC" para representar la letra "ü"
* Contiene "C3B1" para representar la letra " ñ"
* La codificacion es UTF-8 en vez de EASCII Code Page 850.
Referencia de Code Page 850:
>> https://www.ascii-codes.com/cp850.html

¿Como podemos solucionar esto? Haciendo lo siguiente:

Agregamos el caracter CR:
```bash
unix2dos replica.txt
```
Referencia comando unix2dos:
>> https://linux.die.net/man/1/unix2dos

Codificamos el texto en EASCII Code Page 850.
```bash
iconv -f UTF-8 -t CP850//TRANSLIT replica.txt > cp850.txt
```
Referencia comando iconv:
>> https://linux.die.net/man/1/iconv

Y solucionado ! 
Ahora solo falta reemplazar al archivo "replica.txt" por "cp850.txt"
```bash
rm replica.txt
```
```bash
mv cp850.txt replica.txt
```


# 2.  CREACION DE DIFERENTES CODIFICACIONES.

*  FANSI.txt (FORMATO ANSI)
Suponemos que se refiere a la siguiente tabla: 
>> https://www.medcalc.org/manual/ansi-character-set.php

ANSI es una codificación de un solo byte utilizada en sistemas Windows para idiomas europeos occidentales.

```bash
iconv -f CP850 -t WINDOWS-1252 replica.txt > FANSI.txt
```

Si realizamos un diff o un xxd de ambos archivos, podemos notar que, aunque ambos archivos tengan el mismo tamaño, Se cambio:
1. El byte "A4" que representaba a la "ñ" codificada en EASCII Code Page 850 por el byte "F1" que es la misma letra pero codificada en ANSI.
2.  El byte "81" que representaba a la "ü" codificada en EASCII Code Page 850 por el byte "FC" que es la misma letra pero codificada en ANSI.

*  FUTF16L.txt (FORMATO UNICODE)
Suponemos que se refiere a UNICODE representado en little endian (ya que posee una L al final)

```bash
iconv -f CP850 -t UTF-16LE replica.txt > FUTF16L.txt
```

Notemos que el tamaño del archivo se duplico ya que generalmente se utilizan dos bytes para representar a un caracter. Little Endian representa el byte menos significativo primero y luego el byte mas representativo (Big Endian hace lo contrario).

* FUTF16B.txt (FORMATO UNICODE BIG ENDIAN)

```bash
iconv -f CP850 -t UTF-16BE replica.txt > FUTF16B.txt
```

Notemos que el tamaño del archivo es el mismo que Little Endian pero si hacemos un diff o xxd de ambos archivos, podemos visualizar como cambio el orden de los bytes.

Referencia de BIG ENDIAN y LITTLE ENDIAN
>> https://www.baeldung.com/cs/big-endian-vs-little-endian

* FUTF8.txt (FORMATO UTF-8)

```bash
iconv -f CP850 -t UTF-8 replica.txt -o FUTF8.txt
```

Si realizamos un diff o un xxd a ambos archivos, podemos notar que UTF-8 tiene 2 bytes que el archivo "replica.txt" que esta codificado con EASCII Code Page 850.

¿A que se deben estos 2 bytes de mas?
Esto se debe a que UTF-8 es capaz de representar 1.1 millones de caracteres diferentes, cubriendo una amplia gama de idiomas, símbolos y emojis. UTF-8 utiliza entre 1 y 4 bytes por carácter, dependiendo del rango del carácter en Unicode.
En este caso, esta utilizando:
1.  El byte "C3" y el byte "BC" para representar la "ü" (EASCII Code Page utilizaba solo el byte "81" para representar esta letra).
2. El byte "C3" y el byte "B1" para representar la "ñ" (EASCII Code Page utilizaba solo el byte "A4" para representar esta letra).


# 3.  COMPLETAR LA TABLA.


| NOMBRE ARCHIVO | TAM. EN BYTES |
| -------------- | ------------- | 
| FANSI.txt      | 147 BYTES     |
| FUTF16L.txt    | 294 BYTES     |
| FUTF16B.txt    | 294 BYTES     |
| FUTF8.txt      | 149 BYTES     |
 

# 4. COMPLETAR LA TABLA.

| CARACTER | ANSI HEX | ANSI BYTES | UTF16B HEX | UTF16B BYTES | UTF16L HEX | UTF16L BYTES | UTF8 HEX | UTF8 BYTES |
|----------|----------|------------|------------|--------------|------------|--------------|----------|------------|
| A        | 41       | 1          | 0041       | 2            | 4100       | 2            | 41       | 1          |
| a        | 61       | 1          | 0061       | 2            | 6100       | 2            | 61       | 1          |
| 0        | 30       | 1          | 0030       | 2            | 3000       | 2            | 30       | 1          |
| 9        | 39       | 1          | 0039       | 2            | 3900       | 2            | 39       | 1          |
| SPACE    | 20       | 1          | 0020       | 2            | 2000       | 2            | 20       | 1          |
| ,        | 2C       | 1          | 002C       | 2            | 2C00       | 2            | 2C       | 1          |
| .        | 2E       | 1          | 002E       | 2            | 2E00       | 2            | 2E       | 1          |
| !        | 21       | 1          | 0021       | 2            | 2100       | 2            | 21       | 1          |
| ¡        | A1       | 1          | 00A1       | 2            | A100       | 2            | C2A1     | 2          |
| á        | E1       | 1          | 00E1       | 2            | E100       | 2            | C3A1     | 2          |
| ü        | FC       | 1          | 00FC       | 2            | FC00       | 2            | C3BC     | 2          |





