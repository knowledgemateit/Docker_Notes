FROM almalinux:8
RUN yum update -y
RUN yum install nginx -y
RUN rm -rf /usr/share/nginx/html/index.html
# This will not run when creator building the image.
ONBUILD ADD index.html /usr/share/nginx/html/
ENTRYPOINT [ "nginx", "-g", "daemon off;"]