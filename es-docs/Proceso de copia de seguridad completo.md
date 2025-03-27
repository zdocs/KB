# Proceso de Copia de Seguridad Completo

## Copia de Seguridad Completa (Full Backup)

El proceso de copia de seguridad completo sigue los siguientes pasos para realizar una copia de seguridad del buzón, la base de datos, los índices y el directorio LDAP:

1. Realiza una copia de seguridad de los datos del sistema global, incluidas las tablas del sistema y localconfig.xml.

2. Recorre en iteración cada cuenta de la que se va a hacer una copia de seguridad y realiza una copia de seguridad de las entradas LDAP de esas cuentas.

3. Coloca el buzón de la cuenta en modo de mantenimiento para bloquear temporalmente la entrega de correo y el acceso de los usuarios a ese buzón.

4. Realiza una copia de seguridad del buzón:
   - Crea un vertedero de MariaDB para todos los datos relacionados con ese buzón.
   - Realiza una copia de seguridad del directorio de mensajes de ese buzón.
   - Crea una copia de seguridad del directorio de índice de ese buzón.

5. Reactiva el buzón y pasa al siguiente.

6. Realiza una copia de seguridad del directorio LDAP.

Por lo general, una copia de seguridad completa se ejecuta de forma asíncrona. Cuando comienza la copia de seguridad completa, la etiqueta del proceso de copia de seguridad en curso se muestra inmediatamente. La copia de seguridad continúa en segundo plano. Puede utilizar el comando `zmbackupquery` para comprobar el estado de la copia de seguridad en ejecución en cualquier momento.

Los archivos de copia de seguridad se guardan como archivos zip sin compresión. Para cambiar la opción zip predeterminada, consulte el [Apéndice A: Utilidades de línea de comandos](https://zimbra.github.io/documentation/zimbra-10/adminguide.html#command_line_utilities), sección zmbackup.

## Copia de Seguridad Incremental (Incremental Backup)

Las copias de seguridad incrementales se ejecutan mediante el comando de la CLI, **zmbackup**. El proceso para la copia de seguridad incremental es el siguiente:

1. Realiza una copia de seguridad de los datos del sistema global, incluidas las tablas del sistema y localconfig.xml.

2. Recorre en iteración cada cuenta de la que se va a hacer una copia de seguridad y realiza una copia de seguridad de las entradas LDAP de esas cuentas.

3. Mueve los registros de puesta (redo logs) al día de archivo, creados desde la última copia de seguridad, a la carpeta `<backup_target>/redologs`.

   > Los registros archivados que tienen menos de una hora de antigüedad en el momento de la copia de seguridad incremental se copian en la copia de seguridad y no se eliminan. Estos redologs se eliminan una hora después de la copia de seguridad. El intervalo se establece mediante la clave localconfig `backup_archived_redolog_keep_time`. El valor predeterminado es 3600 segundos.
   >
   > Si no se encuentra ninguna copia de seguridad completa para esta cuenta, el proceso de copia de seguridad realiza una copia de seguridad completa en esta cuenta, incluso si solo se especificó una copia de seguridad incremental.

4. Realiza una copia de seguridad del directorio LDAP.

## Realización de Copias de Seguridad Manuales

Utilice el mandato `zmbackup` para realizar las siguientes operaciones de copia de seguridad:

- Realice una copia de seguridad manual de todos los buzones de correo en el servidor1:
  ```
  zmbackup -f -s server1.domain.com -a all
  ```

- Realizar una copia de seguridad manual e incremental de todos los buzones de correo del servidor1 desde la última copia de seguridad completa:
  ```
  zmbackup -i -s server1.domain.com -a all
  ```

- Realice una copia de seguridad manual y completa solo del buzón de correo del usuario1 en el servidor1:
  ```
  zmbackup -f -s server1.domain.com -a user1@domain.com
  ```

## Eliminación de Sesiones de Copia de Seguridad

Puede eliminar sesiones de copia de seguridad por etiqueta o por fecha:

- Al eliminar por etiqueta, se elimina esa sesión y todas las sesiones de copia de seguridad anteriores a esa sesión.
- Al eliminar por fecha, se eliminan todas las sesiones de copia de seguridad anteriores a la fecha especificada.

Por ejemplo, `zmbackup -del 7d` elimina las copias de seguridad de más de 7 días a partir de ahora. Puede especificar el día (d), el mes (m) o el año (y).

## Encontrar Copias de Seguridad Específicas

Cada copia de seguridad completa o incremental es una sesión de copia de seguridad.

Cada sesión de copia de seguridad está etiquetada con fecha y hora. Por ejemplo, la etiqueta `full-20210712.155951.123` dice que se trata de una copia de seguridad del 12 de julio de 2021 a las 3:59:51.123.

> **Nota:** Las horas establecidas en la etiqueta de la sesión son GMT, no la hora local. GMT se utiliza en lugar de la hora local para conservar el orden visual en las transiciones de horario de verano.

Utilice el comando `zmbackupquery` para buscar sesiones de copia de seguridad completas:

- Para encontrar una sesión de copia de seguridad completa específica:
  ```
  zmbackupquery -lb full-20210712.155951.123
  ```

- Para encontrar una sesión de copia de seguridad completa desde una fecha específica:
  ```
  zmbackupquery --type full --from "2021/01/01 12:45:45"
  ```

- Para encontrar todas las sesiones de copia de seguridad completas en el directorio de copia de seguridad:
  ```
  zmbackupquery --type full
  ```

- Para encontrar el mejor punto en el tiempo para restaurar una cuenta, especifique una ventana de tiempo:
  ```
  zmbackupquery -a user1@example.com --type full --from "2021/07/05 12:01:15" --to "2021/07/12 17:01:45"
  ```
