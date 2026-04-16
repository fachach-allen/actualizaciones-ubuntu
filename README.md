/////Primero se crea el local.yml en github
---
- name: Mantenimiento total de Ubuntu
  hosts: localhost
  connection: local
  become: yes
  tasks:
    - name: 1. Ejecutar update y full-upgrade
      apt:
        update_cache: yes
        upgrade: full

    - name: 2. Ejecutar autoremove (limpieza)
      apt:
        autoremove: yes

    - name: 3. Revisar si el sistema necesita reiniciarse
      stat:
        path: /var/run/reboot-required
      register: requiere_reinicio

    - name: 4. Reiniciar la computadora solo si es necesario
      reboot:
        msg: "Mantenimiento completado. Reiniciando equipo..."
      when: requiere_reinicio.stat.exists

////////Preparar el equipo destino
El equipo se conecta al repositorio para jalar las instrucciones, para ellos se necesita Ansible instalado localmente

Bash
sudo apt update && sudo apt install ansible git -y

///////Paso 3 probar que si logra leer las instrucciones de github y ejecutarlas en si mismo Si en el paso 3 funciono 
(esto se puede saltar sin problemas por que ya esta probado)

sudo ansible-pull -U https://github.com/tu-usuario/actualizaciones-ubuntu.git local.yml

/////////Paso 4 Automatizar para siempre

sudo crontab -e (La terminal prguntara la opcion 1,2 o 3 escoger la que diga nano)
Se abrira un archivo y se pega el siguiente comando
0 6 * * 4 ansible-pull -U https://github.com/fachach-allen/actualizaciones-ubuntu.git local.yml > /var/log/ansible-pull.log 2>&1
guardar con Ctrl+O, Enter, CTRL+X

¿Qué significan estos nuevos números?
El sistema cron lee el tiempo en este orden: Minuto, Hora, Día del mes, Mes, Día de la semana.

0: El minuto exacto (0).

6: La hora en formato 24h (6:00 AM).

*: Cualquier día del mes.

*: Cualquier mes del año.

4: El día de la semana (4 es Jueves). En Linux, la cuenta empieza en 0 (Domingo) o 1 (Lunes), por lo que 4 siempre es jueves.




