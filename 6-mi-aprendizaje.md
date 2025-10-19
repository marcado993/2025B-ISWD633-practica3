# COMPLETAR  
Comparando sus conocimientos antes de hacer la práctica con sus conocimientos después de hacer la tarea, explicar los principales aprendizajes logrados para beneficio de su formación profesional.  
Si solucionó un problema presentado o utilizó otros comandos que no se mencionan al realizar la práctica también se debe documentar.

## Mi antes y después

Para empezar, antes de esta práctica ya tenía conocimientos para crear una red en Docker, poder listar los contenedores y conectar los contenedores a la misma red. Sin embargo, tenía un problema en la parte de consistencia de datos: podía levantar servidores de WordPress, pero al eliminarlos se perdía toda la información. En las empresas eso puede ser motivo de pérdida de datos críticos.

En esta práctica aprendí a mantener la consistencia de datos creando volúmenes, lo que me permite tener los archivos de forma persistente en el host y montarlos en una imagen (como fue el caso de nginx). Esto me da la ventaja de disponer de copias de seguridad en caso de pérdida de información sensible.

Con la práctica resolví el problema de que, al borrar la base de datos y el contenedor de WordPress, se perdiera la información; ahora los datos se guardan fuera del contenedor y se conservan al recrearlo.

Otro aspecto muy importante que aprendí fueron los volúmenes nombrados: Docker gestiona automáticamente las carpetas del volumen, lo que simplifica mucho su uso en lugar de crear manualmente directorios en el host.

Por último, también aprendí sobre los volúmenes anónimos. Observé que al eliminar un contenedor el uso de disco no siempre disminuía, lo que indicaba que había volúmenes persistentes no referenciados. Gracias a `docker volume prune` pude recuperar espacio de almacenamiento.

### Mis conclusiones

- Tener volúmenes nos permite persistir la información, pero el no poder removerlos de manera adecuada nos consumen mucho almacenamiento.

- El uso de docker **red** nos permite gestionar en una misma red todos los contenedores que tenemos.

- Poder gestionar los archivos localmente y que se apunte a esa carpeta nos permite tener modificabilidad y un backup

### Cositas interesantes (concretas y útiles)

- Comandos rápidos de diagnóstico:

```powershell
# Contenedores activos y puertos
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
# Volúmenes y uso
docker volume ls
docker system df
# Inspeccionar un volumen (ver mountpoint)
docker volume inspect vol-drupal-files --format '{{.Mountpoint}}'
```

- Cómo liberar espacio si hay volúmenes huérfanos:

```powershell
# Eliminar volúmenes no usados (prune)
docker volume prune -f
# Si un volumen está en uso, detener/eliminar el contenedor y luego borrar:
docker rm -f server-drupal; docker volume rm vol-drupal-themes
```

- Copia de seguridad de un volumen (ejemplo):

```powershell
# Crear un tar.gz del contenido del volumen en el host (usa una ruta válida en el host)
docker run --rm -v vol-drupal-files:/data -v C:\Users\INTEL\Documents\backup:/backup alpine \
  sh -c "cd /data && tar czf /backup/vol-drupal-files-$(date +%F).tar.gz ."
```

- Buenas prácticas resumidas:
  - Prefiere volúmenes nombrados para persistencia en entornos Docker sobre bind mounts en Windows cuando sea posible (mejor rendimiento y permisos con WSL2).
  - Documenta las rutas y credenciales en un archivo seguro (no en el repo público).
  - Automatiza con docker-compose para reproducibilidad (redes, volúmenes, dependencias entre servicios).

- Recursos rápidos (leer después):
  - Docker volumes: https://docs.docker.com/storage/volumes/
  - Docker Compose: https://docs.docker.com/compose/