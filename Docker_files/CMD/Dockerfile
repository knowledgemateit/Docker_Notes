FROM almalinux:8
RUN yum update -y
RUN yum install nginx -y
RUN echo "Hello Kuberntes" > /usr/share/nginx/html/hello.html
#command to run nginx container in foreground
CMD ["nginx", "-g", "daemon off;"]