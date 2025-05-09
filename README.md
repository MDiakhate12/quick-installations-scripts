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
> Note: Sometimes, you will need to add a spark maven package to your code at runtime for testing purposes. It's possible by simply adding to your SparkSession definition this: `.config("spark.jars.packages", "groupId:artefactId:version")` (you can specify multiple package separated by commas (,)). After that, refer to your package documentation for usage.<br>Example with spark-excel package on pyspark:<br>

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

df = spark.read \
    .format("com.crealytics.spark.excel") \
    .option("header", "true") \
    .load("/path/to/your/excel/file.xlsx")
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

# Mouse Jiggler 

> MouseJiggler Powershell Script <br>
> Written by AndrewDavis <br>
> https://gist.github.com/AndrewDavis

```bash
Clear-Host
Echo "Keep-alive with Scroll Lock..."

$WShell = New-Object -com "Wscript.Shell"

while ($true)
{
  $WShell.sendkeys("{SCROLLLOCK}")
  Start-Sleep -Milliseconds 100
  $WShell.sendkeys("{SCROLLLOCK}")
  Start-Sleep -Seconds 240
}
```

# Run loop
```python
import time
import ctypes

import stats

def press_key():
    RUN_KEY = 0x10
    ctypes.windll.user32.keybd_event(RUN_KEY, 0, 0, 0)
    time.sleep(0.05)
    ctypes.windll.user32.keybd_event(RUN_KEY, 0, 2, 0)


def test_run_with_interval(interval=60*10):
    print(f"Run stats check every {interval} seconds...")
    try:
        while True:
            press_key()
            print(f"\nChecking docker stats every {interval} seconds, ")
            # Afficher les statistiques
            docker_stats = stats.get_docker_memory_stats()
            for stat in docker_stats:
                print(f"Conteneur: {stat['container']}, Utilisation mémoire: {stat['memory_usage']} / {stat['memory_limit']}, "
                    f"Pourcentage: {stat['memory_percent']}%")

            time.sleep(interval)
    except KeyboardInterrupt:
        print("\nStopping...")

if __name__ == "__main__":
    # os.system("docker stats spark-iceberg2")

    test_run_with_interval(interval=60)
```
