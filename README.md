Guía de Laboratorio: Ingeniería Social Asistido por IA y Análisis Defensivo
Objetivos del Aprendizaje
• Identificar técnicas de ingeniería social y estructuras de correos maliciosos
(phishing).
• Utilizar la Inteligencia Artificial como herramienta de auditoría y detección.
• Implementar medidas defensivas y de gestión de identidad en un entorno
controlado.
Requisitos Previos
• Computadora con acceso a Internet.
• Software de virtualización instalado (VirtualBox o VMware Workstation Player).
• Una cuenta de acceso a un modelo de IA (ChatGPT, Claude, Gemini, etc.).
Paso a Paso del Desarrollo
Sección A
Configuración del Entorno Seguro (Virtualización)
Para evitar interactuar con elementos potencialmente sospechosos en la máquina real,
todo el análisis se hará dentro de un entorno aislado.
1. Crear una Máquina Virtual (MV): Instala un sistema operativo básico (Windows
10/11 o Linux Ubuntu) en VirtualBox. (Aquí les recomiendo descargar una maquina
virtual ya instalada, y no instalarla desde cero) Pueden hacerlo desde este enlace:
https://open-devlabs.com/descargar-maquina-virtual/windows-10/
2. Configurar la Red: Asegúrate de que la MV tenga la red en modo NAT para que
disponga de Internet pero permanezca aislada de tu red local corporativa o
residencial.
3. Preparar las Herramientas: Dentro de la MV, abre el navegador web y asegúrate de
tener acceso a la herramienta de IA elegida.
Sección B
Paso 1: Fase Ofensiva (Entendiendo al Atacante con IA)
El objetivo de este paso es comprender cómo los ciberdelincuentes automatizan y
perfeccionan sus ataques usando IA.
1. Generación del Escenario: Solicita a la IA que redacte un correo electrónico
institucional simulado (por ejemplo, una alerta de actualización de datos de
soporte técnico o de credenciales).
o Nota pedagógica: Configura el prompt indicando explícitamente que es con
fines educativos de concientización.
2. Sofisticación del Phishing: Pide a la IA que modifique el texto para incluir
disparadores psicológicos de ingeniería social (urgencia, autoridad o miedo).
Paso 2: Preparación del ataque - Descargar e iniciar el Servidor de Correo Falso
(MailHog)
1. Dentro de la Máquina Virtual, accede al GitHub oficial de MailHog
https://github.com/mailhog/MailHog/releases y descarga el ejecutable
correspondiente al sistema operativo de la MV (ej. MailHog_windows_amd64.exe o
MailHog_linux_amd64).
2. Ejecuta el archivo. Verás que se abre una ventana de comandos (Terminal) que
indica que el servidor está corriendo.
o El puerto 1025 recibirá los correos (SMTP).
o El puerto 8025 será la bandeja de entrada visual (HTTP).
3. Abre el navegador web dentro de la misma MV e ingresa a: http://localhost:8025.
Verás una interfaz limpia que simula un correo electrónico (actualmente vacía). No
cierres la terminal de MailHog.
Paso 3: Envío Automatizado del Correo (Simulación de la Fase Ofensiva)
Para enviar el correo de forma técnica y entender cómo los atacantes automatizan el
proceso, deben crear un pequeño script de Python nativo. No necesitan instalar nada más.
1. Abre un editor de texto dentro de la MV y crea un archivo llamado
enviar_phishing.py.
2. Copia y pega el siguiente código básico (Deben rellenar los campos con el texto del
correo que les generó su IA):
=========================================================================
import smtplib
from email.mime.text import MIMEText
# 1. Configuración del mensaje falsificado
remitente_falso = "soporte-seguridad@institucion-alerta.com"
destinatario_prueba = "estudiante_test@laboratorio.local"
cuerpo_correo = """
ASUNTO: URGENTE - Actualización de credenciales obligatoria
[Pega aquí el texto generado por la IA con los disparadores psicológicos]
Haga clic aquí para resolver el problema: http://localhost/login_falso
"""
msg = MIMEText(cuerpo_correo)
msg['Subject'] = 'URGENTE: Validar cuenta institucional'
msg['From'] = remitente_falso
msg['To'] = destinatario_prueba
# 2. Envío al servidor MailHog local (Puerto 1025)
try:
 # Se conecta a sí mismo (localhost) sin peligro de salir a Internet
 server = smtplib.SMTP('localhost', 1025)
 server.sendmail(remitente_falso, [destinatario_prueba], msg.as_string())
 server.quit()
 print("¡Correo enviado exitosamente al laboratorio local!")
except Exception as e:
 print(f"Error al conectar: {e}")
=========================================================================
3. Guarda el archivo y ejecútalo desde la terminal de la MV con el comando: python
enviar_phishing.py
Paso 4: Fase de Detección y Análisis (Bandeja de Entrada)
1. Regresa al navegador web donde tenías abierto http://localhost:8025.
2. Verás que ha llegado instantáneamente el correo simulado mostrando el remitente
falsificado (soporte-seguridad@institucion-alerta.com).
3. A entregar: Tomar una captura de pantalla de ese correo en la bandeja de MailHog,
exportar el texto y, utilizando un editor de imágenes o texto, resaltar en rojo los tres
indicadores de engaño clave (la dirección sospechosa, las palabras de urgencia y el
enlace falso).
Sección C
Fase Defensiva (La IA como Analista de Seguridad)
Ahora cambiamos el rol a analista defensivo.
1. Análisis Técnico de Cabeceras (Headers):
o Cabecera de correo real:
De: Udemy Instructor: Jason Dion • 3 Million+ Enrollments Worldwide <noreply@e.udemymail.com>
Date: mar, 2 jun 2026 a las 9:43
Subject: $9.99 Cloud Deal — AZ-900 Starts Here
To: <prueba@gmail.com>
o Copiar la cabecera mostrada anteriormente subirla a la IA con la siguiente
instrucción:
"Actúa como un analista SOC de ciberseguridad. Analiza la siguiente cabecera de correo
electrónico e identifica: Dirección IP de origen, si pasa las verificaciones
SPF/DKIM/DMARC, y si el remitente real coincide con el mostrado."
2. Evaluación del Veredicto: Documentar el diagnóstico que entregó la IA y verificar
si el correo analizado representa un riesgo de fraude o robo de identidad.
Sección D
Mitigación y Buenas Prácticas
Para cumplir con la competencia general de salvaguardar la privacidad:
1. Configuración de Filtros: Dentro de la MV, configurar reglas de filtrado de correo
para enviar palabras clave sospechosas directamente a la carpeta de Spam.
2. Robustecimiento de Accesos: Utilizando un gestor de contraseñas (como
Bitwarden o KeePass), generar e implementar una contraseña fuerte (mínimo 14
caracteres, alfanumérica y con símbolos) simulando la protección de la cuenta
afectada en el ejercicio.
Entregable del Estudiante (Evaluación)
Para obtener la puntuación de la práctica, deben subir un informe en PDF a la plataforma
de educación permanente que contenga:
1. Captura de pantalla de la Máquina Virtual en funcionamiento con el ejercicio.
2. El prompt utilizado y el análisis del correo sospechoso realizado por la IA.
3. Los entregables que se indican en: Secccion B, paso 4, punto 3 y Secccion C, paso
1, punto 2.
4. Captura del gestor de contraseñas mostrando la creación de una clave segura para
mitigar el ataque analizado. 
