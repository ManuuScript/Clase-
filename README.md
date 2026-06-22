Aqui esta el archivo:


import smtplib
from email.mime.text import MIMEText

# 1. Configuración del mensaje falsificado
remitente_falso = "soporte-seguridad@institucion-alerta.com"
destinatario_prueba = "estudiante_test@laboratorio.local"

cuerpo_correo = """
ASUNTO: URGENTE - Actualización de credenciales obligatoria

Estimado colaborador / estudiante,

Le comunicamos que el Departamento de Soporte Técnico y Seguridad de la Información está realizando una migración obligatoria de los servidores de autenticación centralizada.

Due to this technical update, an inconsistency has been detected in your institutional access credentials. Si no valida su identidad en las próximas 2 horas, su cuenta será suspendida de forma permanente por motivos de seguridad y perderá acceso a todos los servicios asociados.

Para evitar la interrupción de sus actividades, haga clic en el siguiente enlace seguro y confirme sus datos de inmediato:

Haga clic aquí para resolver el problema: http://localhost/login_falso

Atentamente,
Dirección General de Tecnología y Seguridad de la Información
Dirección de Soporte Técnico Institucional
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
