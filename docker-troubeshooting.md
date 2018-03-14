- Add line to host file docker images:
`ENTRYPOINT ["/bin/sh", "-c" , "echo 192.168.105.110 admindev.cybersales.vn >> /etc/hosts && echo 192.168.105.110 admin.cybersales.vn >> /etc/hosts && echo 192.168.105.110 publicdev.cybersales.vn >> /etc/hosts && echo 192.168.105.110 public.cybersales.vn >> /etc/hosts && exec python manage.py runserver 0.0.0.0:8000 " ]`
