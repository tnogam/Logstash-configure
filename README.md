# Logstash-configure


Steps to complete

- Any indents are for input into the config files that open

sudo apt install curl -y
sudo apt install git -y
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch |sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg

echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list

sudo apt update -y

sudo apt install elasticsearch -y

sudo nano /etc/elasticsearch/elasticsearch.yml
	network.host: localhost

sudo systemctl start elasticsearch

sudo systemctl enable elasticsearch

curl -X GET "localhost:9200"

sudo apt install kibana

sudo systemctl enable kibana

sudo systemctl start kibana

sudo apt install logstash

echo "kibanaadmin:`openssl passwd -apr1`" | sudo tee -a /etc/nginx/htpasswd.users
##maybe enter password ???


sudo git clone https://github.com/coralogix-resources/logstash-syslog.git /etc/logstash/conf.d/logstash-syslog

sudo nano /etc/logstash/conf.d/logstash-syslog/syslog-tcp-forward.conf
	port =>514

sudo /usr/share/logstash/bin/logstash -f /etc/logstash/conf.d/logstash-syslog/syslog-tcp-forward.conf

#TESTING INGEST
Using local syslogs
sudo nano /etc/rsyslog.d/50-default.conf
	*.*                         	@@127.0.0.1:514
sudo systemctl restart rsyslog.service


#Ctrl + shift + t
curl -XGET "http://localhost:9200/syslog-received-on-tcp/_search?pretty" -H 'Content-Type: application/json' -d'{"size": 1}'


#CLEAN UP
curl -XDELETE "http://localhost:9200/syslog-received-on-tcp/"
curl -XDELETE "http://localhost:9200/syslog-monitor/"
sudo rm -r /etc/logstash/conf.d/logstash-syslog
