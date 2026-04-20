# Laboratorio 5 – Análisis de tráfico de red

## Descripción
En este laboratorio se realizó el análisis de tráfico de red utilizando un archivo `.pcap` y herramientas de análisis estadístico y detección de anomalías. El objetivo fue identificar comportamientos sospechosos mediante técnicas como Z-score e Isolation Forest, y posteriormente confirmar los hallazgos mediante inspección manual del payload.

El proceso simula el flujo de trabajo de un SOC (Security Operations Center), donde primero se detectan anomalías automáticamente y luego se investiga el contenido del tráfico para determinar si existe un posible ataque.

---

## Herramientas utilizadas

- Python
- Scapy
- Pandas
- Numpy
- Matplotlib
- Scikit-learn

---

## Procedimiento

### 1. Análisis inicial del pcap
Se cargó el archivo `.pcap` utilizando Scapy y se verificó su contenido mostrando los primeros paquetes, su longitud y tipo de datos.

Luego, el archivo fue convertido a un DataFrame con las siguientes columnas:

- src_ip
- dst_ip
- src_port
- dst_port
- payload_len
- pkt_len

Esto permitió realizar análisis estadísticos del tráfico de red.

---

### 2. Estadísticas básicas
Se identificó:

- IP origen más frecuente
- IP destino más frecuente
- Puertos más utilizados
- Comunicaciones entre direcciones IP

Se observó que la IP **10.1.10.53** se comunica frecuentemente con la IP **84.54.22.33** a través del puerto **53 (DNS)**.

---

### 3. Detección de anomalías con Z-score
Se aplicó Z-score sobre el tamaño del payload para identificar valores atípicos.

Inicialmente no se detectaron anomalías porque la distribución de los datos no era normal. El histograma mostró dos grupos de valores:

- payloads pequeños (~50 bytes), típicos de DNS
- payloads grandes (~900–1000 bytes)

Posteriormente, se aplicó Z-score utilizando conocimiento del protocolo DNS (media 50 bytes y desviación estándar 15 bytes), lo cual permitió identificar múltiples paquetes anómalos con tamaños excesivamente grandes.

Esto demuestra que el conocimiento del protocolo mejora la detección de anomalías.

---

### 4. Visualización de datos
Se generaron gráficas que muestran:

- payload total enviado por IP origen
- payload total recibido por IP destino
- payload por puerto origen
- payload por puerto destino

Estas visualizaciones permitieron identificar patrones de comunicación sospechosos.

---

### 5. Detección automática con Isolation Forest
Se utilizó el algoritmo Isolation Forest con las variables:

- payload_len
- pkt_len

El modelo detectó anomalías en la misma comunicación observada previamente entre:

**10.1.10.53 → 84.54.22.33**

Esto confirma la presencia de tráfico inusual.

---

### 6. Análisis manual del payload
Se examinó el contenido de los payloads sospechosos y se encontró la firma hexadecimal:

504e470d0a1a0a

Este patrón corresponde a un archivo PNG, lo cual no es normal en tráfico DNS.

El protocolo DNS normalmente transmite consultas pequeñas (~50 bytes), por lo que la presencia de datos grandes sugiere un posible uso indebido del protocolo.

---

## Posible ataque detectado
El comportamiento observado sugiere un posible:

**DNS tunneling o exfiltración de datos**

Este tipo de ataque utiliza el protocolo DNS para transportar información que no corresponde a consultas normales, permitiendo ocultar datos dentro del tráfico de red.

---

## Conclusiones

- El análisis estadístico permite identificar patrones inusuales en el tráfico de red.
- El Z-score puede no detectar anomalías si la distribución de los datos no es normal.
- El conocimiento del protocolo mejora la detección de tráfico sospechoso.
- Isolation Forest complementa el análisis estadístico.
- La inspección manual del payload permite confirmar el tipo de información transmitida.
- La combinación de técnicas automáticas y análisis manual permite identificar comportamientos maliciosos de manera más precisa.

---

## Estructura del proyecto

lab5/

│

├── lab5_analisis_trafico.ipynb

├── analisis_paquetes.pcap

└── README.md
