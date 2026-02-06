1. High CPU usage
Use the below commands to identify the CPU usage
a. top
b. htop
Look for %CPU and load average
Identify the process 
ps aux --sort=-%cpu | head
Kill the process
kill -9 <PID>

2. High Memory Usage
Check memory
free -m
vmstat 1 5
Identify the process consuming high memory
ps aux --sort=-%mem | head
Kill the process
kill -9 <PID>

3. Disk Full
Check usage: df -h
Simulate a large file: sudo fallocate -l 2G /var/log/bigfile.log
Check usage again: df -h
FInd the large file: sudo du -sh /var/log/* | sort -h
Fix issue by removing the file: sudo rm /var/log/bigfile.log 
Verify: df -h 

4. STUCK / MISBEHAVING PROCESS
Incident
App is running but not responding.
Find the process: ps aux | grep bash  or by port: ss -tulpn
Kill the process: kill <PID> or kill -9 <PID>

5. SERVICE FAILURE (systemd)
Incident
SSH / app service is down.
Check service status: systemctl status ssh
Stop and start service: systemctl restart ssh 
Read service logs: journalctl -u ssh -n 50
For live service logs: journalctl -u ssh -f
To jump the last error: journalctl -xe

6. NETWORK / PORT ISSUE
Incident: App is running but unreachable.
Check IP and interface: ip a
Check listening port: ss -tulpn
Check DNS is working: dig google.com +short, nslookup google.com, 
Test connectivity: ping google.com, curl -v https://www.google.com
Local service test: 
    Start a python web server: python3 -m http.server 8080
    From another terminal: curl -v http://localhost:8080


7. Create a systemd Service
    a. Create a simple app (the “service”)
       sudo nano /usr/local/bin/fake-app.sh
       #!/bin/bash
        while true; do
          echo "$(date) - fake-app running"
          sleep 5
        done
        sudo chmod +x /usr/local/bin/fake-app.sh
        /usr/local/bin/fake-app.sh
    b. Create the systemd service file
        sudo nano /etc/systemd/system/fake-app.service
        [Unit]
        Description=Fake App for SRE Lab
        After=network.target
        [Service]
        ExecStart=/usr/local/bin/fake-app.sh
        Restart=always
        RestartSec=5
        User=root
        [Install]
        WantedBy=multi-user.target
    c. Reload systemd and start service
        sudo systemctl daemon-reexec
        sudo systemctl daemon-reload --> reload systemd
        sudo systemctl start fake-app --> start the application
        sudo systemctl enable fake-app --> enable at boot
        systemctl status fake-app  --> check status
    d. View logs
        journalctl -u fake-app
        journalctl -u fake-app -n 20 --> last twently log entries
        journalctl -u fake-app -f
    e. Simulate a service failure
        ps aux | grep fake-app  --> check process ID
        kill <PID> --> Kill the process to shutdown the application
        CHeck systemctl status fake-app --> restarted automatically
        Break the service: sudo chmod -x /usr/local/bin/fake-app.sh 
                            systemctl restart fake-app
                            systemctl status fake-app
                            journalctl -u fake-app -xe
        Fix the service: sudo chmod +x /usr/local/bin/fake-app.sh
                         systemctl restart fake-app
                        systemctl status fake-app
    f. Stop, start and Disable 
        sudo systemctl stop fake-app
        sudo systemctl restart fake-app
        sudo systemctl disable fake-app