sudo docker commit a8f5c77816d4 cassandra/mosht:14010117

sudo docker save cassandra/mosht | gzip > cassandra_mosht.tar.gz

docker load < uniui_2_1_2_mosht.tar.gz

docker run -d -p 8080:80 uniui_2_1_2/mosht:14010117
