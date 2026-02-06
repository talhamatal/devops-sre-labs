SCENARIO 1 — DNS FAILURE
Incident: “The service hostname doesn’t resolve.”
1. Test DNS resolution
   dig google.com +short
   nslookup google.com
2. Simulate DNS failure
   sudo nano /etc/resolv.conf
   Remove entry : nameserver 1.1.1.1 change to nameserver 127.0.0.1
   test DNS: dig google.com +short
3. Fix DNS 
   Add nameserver 1.1.1.1 or nameserver 8.8.8.8
   Test: dig google.com +short or nslookup google.com

SCENARIO 2 — PORT NOT LISTENING
Incident: “Connection refused.”
1. Check listening ports
   ss -tulpn
   ss -tulpn | grep 8080
2. Start a test service
   python3 -m http.server 8080
   ss -tulpn | grep 8080
3. Test connectivity
   curl http://localhost:8080

SCENARIO 3 — FIREWALL BLOCKING
Incident: Service is running but unreachable externally.
1. Enable firewall
   sudo ufw enable
   Block port: sudo ufw deny 8080
2. Start service and test connectivity
   python3 -m http.server 8080
   curl http://localhost:8080
3. Check firewall rules
   sudo ufw status
4. Fix Firewall
   sudo ufw allow 8080
   sudo ufw reload
   sudo ufw status
   test: curl http://localhost:8080

SCENARIO 4 — TLS / HTTPS FAILURE
Incident: HTTPS fails but HTTP works.
1. Test https
   curl https://expired.badssl.com
2. Debug TLS Handshake
   curl -v https://expired.badssl.com
3. Inspect certs directly
   openssl s_client -connect expired.badssl.com:443 -servername expired.badssl.com

SCENARIO 5 — curl DEBUGGING (MOST IMPORTANT)
Incident: “The API endpoint times out.”
1. Basic curl: curl https://www.google.com
2. Verbose mode: curl -v https://www.google.com
3. Connection timeout: curl --connect-timeout 2 https://10.255.255.1
4. headers only: curl -I https://www.google.com
5. Test IP: curl http://142.250.72.14 If IP works and name does not DNS issue