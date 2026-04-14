#base os
FROM almalinux:8
# installing or updating required packages
RUN yum update -y
# you are installing web server
RUN yum install nginx -y
RUN rm -rf /usr/share/nginx/html/index.html
# you are copying app code
COPY qi/ /usr/share/nginx/html/
# you are staring server
CMD [ "nginx", "-g", "daemon off;" ]