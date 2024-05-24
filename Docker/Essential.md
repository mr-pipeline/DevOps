<b>Backup:</b>
```
sudo docker commit a8f5c77816d4 cassandra/mosht:14010117
sudo docker save cassandra/mosht | gzip > cassandra_mosht.tar.gz
```
```
docker load < uniui_2_1_2_mosht.tar.gz
docker run -d -p 8080:80 uniui_2_1_2/mosht:14010117
```

<b>Network</b>
```
docker ps -q | xargs docker inspect --format '{{.Name}} {{range .NetworkSettings.Networks}}{{.NetworkID}} {{.IPAddress}} {{end}}' | awk '{print $1 "\t" $2 "\t" $3}'
```
```
docker ps -q | xargs docker inspect --format '{{.Name}} {{range $key, $value := .NetworkSettings.Networks}}{{$key}} {{$value.IPAddress}} {{end}}' | awk '{print $1 "\t" $2 "\t" $3}'
```
