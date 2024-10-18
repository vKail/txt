global_defs {
  # Keepalived process identifier
  router_id nginx # Se puede cambiar dependiendo del servicio.
}

# Script to check whether Nginx is running or not
vrrp_script check_nginx {
  script "/bin/check_nginx.sh" # Verifica que el servicio funcione.
  interval 2
  weight 50
}

# Virtual interface - The priority specifies the order in 
# which the assigned interface to take over in a failover
vrrp_instance VI_01 {
  state MASTER # Dependiento el nodo se configura MASTER o BACKUP
  interface enp0s3 # Agregar la interfaz de red eth0, enp0s3, etc.
  virtual_router_id 50 # De 0-255, el mismo en todos los nodos.
  priority 110 # 110 para MASTER o 100 para BACKUP

  # The virtual ip address shared between the two NGINX 
  # Web Server which will float
  virtual_ipaddress {
    192.168.100.167/24 # Esta IP debe ir en todos los nodos.
  }
  track_script {
    check_nginx
  }
  authentication {
    auth_type AH
    auth_pass secret
  }
}

