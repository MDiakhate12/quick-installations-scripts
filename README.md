# quick-installations-scripts

## Apache Spark Standalone

Since there are export commands, execute with `source your_script.sh` instead of `./your_script.sh`

### Master

```bash
wget https://dlcdn.apache.org/spark/spark-3.2.1/spark-3.2.1-bin-hadoop3.2.tgz
ls
tar -xzvf spark-3.2.1-bin-hadoop3.2.tgz 
export SPARK_HOME=$PWD/spark-3.2.1-bin-hadoop3.2
echo $SPARK_HOME 
export PATH=$PATH:$SPARK_HOME/bin
export PATH=$PATH:$SPARK_HOME/sbin
sudo apt update -y
sudo apt install -y openjdk-11-jdk
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
start-master.sh
export MASTER_URL=$(grep "INFO Master: Starting Spark master at " $SPARK_HOME/logs/* | awk '{print $9}')
echo $MASTER_URL
```

### Worker 
```bash
wget https://dlcdn.apache.org/spark/spark-3.2.1/spark-3.2.1-bin-hadoop3.2.tgz
ls
tar -xzvf spark-3.2.1-bin-hadoop3.2.tgz 
export SPARK_HOME=$PWD/spark-3.2.1-bin-hadoop3.2
echo $SPARK_HOME 
export PATH=$PATH:$SPARK_HOME/bin
export PATH=$PATH:$SPARK_HOME/sbin
sudo apt update -y
sudo apt install -y openjdk-11-jdk
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
start-worker.sh $MASTER_URL
```

> Note: On Windows, Spark can complain about not knowing where HADOOP_HOME is. To solve this, download the [winutils repo]() as [explained here](https://cwiki.apache.org/confluence/display/HADOOP2/WindowsProblems) and set the HADOOP_HOME as system environment variable (be careful to not add 'bin' at the end of the path. This should be the parent folder of the bin directory). Finally, reload the IDE. <br><br>
> Note: Sometimes, you will need to add a spark maven package to your code at runtime for testing purposes. It's possible by simply adding to your SparkSession definition this: `.config("spark.jars.packages", "groupId:artefactId:version")` (you can specify multiple package separated by commas (,)). After that, refer to your package documentation for usage. Example with spark-excel package on pyspark:<br>

```python
from pyspark.sql import SparkSession

spark = (
    SparkSession
    .builder
    .appName("YOUR APP NAME")
    .master("local[*]") // because we want to run spark locally (remove this line if you want to run it with spark-submit)
    .config("spark.jars.packages", "com.crealytics:spark-excel_2.12:3.2.1_0.16.4")
    .getOrCreate()
)

df = spark.read.format("com.crealytics.spark.excel").load("/path/to/your/excel/file.xlsx")
```

## Docker

### Ubuntu
```bash
sudo apt-get update -y
sudo apt-get install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
sudo apt-get update -y 
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker

# Optionnal (docker-compose)
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

### Debian
```bash
sudo apt-get update -y
sudo apt-get install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update -y
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker

# Optionnal (docker-compose)
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```
