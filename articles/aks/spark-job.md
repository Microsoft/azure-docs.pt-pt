---
title: Executar uma tarefa de Apache Spark com o Azure Kubernetes Service (AKS)
description: Utilize o Azure Kubernetes Service (AKS) para executar uma tarefa de Apache Spark
services: container-service
author: lenadroid
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/15/2018
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: ddaff590fd493b430a72c30dd35cb1b891b80d84
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205340"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Executar tarefas do Apache Spark no AKS

[Apache Spark] [ apache-spark] é um mecanismo rápido para processamento de dados em grande escala. Como do [versão de Spark 2.3.0][spark-latest-release], Apache Spark suporta uma integração nativa com clusters do Kubernetes. O Azure Kubernetes Service (AKS) é um ambiente gerenciado do Kubernetes em execução no Azure. Este documento detalha preparar e executar tarefas do Apache Spark num cluster do Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, é necessário o seguinte.

* Noções básicas sobre do Kubernetes e [Apache Spark][spark-quickstart].
* [Hub do docker] [ docker-hub] conta, ou uma [Azure Container Registry][acr-create].
* CLI do Azure [instalados] [ azure-cli] no sistema de desenvolvimento.
* [JDK 8] [ java-install] instalado no seu sistema.
* SBT ([a ferramenta de compilação Scala][sbt-install]) instalado no seu sistema.
* Ferramentas da linha de comandos do Git instaladas no seu sistema.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Spark é utilizado para processamento de dados em grande escala e requer que nós do Kubernetes são dimensionados para cumprir os requisitos de recursos do Spark. Recomendamos um tamanho mínimo de `Standard_D3_v2` para os nós do Azure Kubernetes Service (AKS).

Se precisar de um cluster do AKS que cumpre esta recomendação mínima, execute os seguintes comandos.

Crie um grupo de recursos para o cluster.

```azurecli
az group create --name mySparkCluster --location eastus
```

Criar o cluster do AKS connosco de tamanho `Standard_D3_v2`.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

Ligue ao cluster do AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Se estiver a utilizar o Azure Container Registry (ACR) para armazenar imagens de contentor, configure a autenticação entre o ACR e o AKS. Consulte a [documentação de autenticação do ACR] [ acr-aks] para estes passos.

## <a name="build-the-spark-source"></a>Criar a origem do Spark

Antes de executar tarefas de Spark num cluster do AKS, terá de criar o Spark código-fonte e empacotá-lo numa imagem de contentor. A origem de Spark inclui scripts que podem ser utilizados para concluir este processo.

Clone o repositório de projeto do Spark no sistema de desenvolvimento.

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

Mude para o diretório do repositório clonado e guardar o caminho de origem do Spark a uma variável.

```bash
cd spark
sparkdir=$(pwd)
```

Se tiver várias versões do JDK instaladas, defina `JAVA_HOME` para utilizar a versão 8 para a sessão atual.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Execute o seguinte comando para criar o Spark de código-fonte com o suporte de Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Os seguintes comandos criar a imagem de contentor do Spark e enviá-la para um registo de imagem de contentor. Substitua `registry.example.com` com o nome do seu registo de contentor e `v1` com a marca preferir usar. Se utilizar o Docker Hub, este valor é o nome do registo. Se utilizar o Azure Container Registry (ACR), este valor é o nome de servidor de início de sessão do ACR.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Envie a imagem de contentor para o registo de imagem de contentor.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Preparar uma tarefa do Spark

Em seguida, prepare-se uma tarefa do Spark. Um ficheiro. jar é utilizado para armazenar a tarefa do Spark e é necessária ao executar o `spark-submit` comando. O jar pode ser tornado acessível através de um URL público ou previamente empacotado dentro de uma imagem de contentor. Neste exemplo, um jar do exemplo é criada para calcular o valor de Pi. Este jar, em seguida, é carregado para o armazenamento do Azure. Se tiver um jar existente, fique à vontade substituir

Crie um diretório em que gostaria de criar o projeto para uma tarefa do Spark.

```bash
mkdir myprojects
cd myprojects
```

Crie um novo projeto de Scala a partir de um modelo.

```bash
sbt new sbt/scala-seed.g8
```

Quando lhe for pedido, introduza `SparkPi` para o nome do projeto.

```bash
name [Scala Seed Project]: SparkPi
```

Navegue para o diretório de projeto criado recentemente.

```bash
cd sparkpi
```

Execute os seguintes comandos para adicionar um plug-in SBT, que permite a empacotar o projeto como um ficheiro jar.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")' >> project/assembly.sbt
```

Execute estes comandos para copiar o código de exemplo no projeto recém-criado e adicione todas as dependências necessárias.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11",/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Para empacotar o projeto num jar, execute o seguinte comando.

```bash
sbt assembly
```

Depois de empacotamento com êxito, deverá ver um resultado semelhante ao seguinte.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Tarefa de cópia para o armazenamento

Crie uma conta de armazenamento do Azure e o contentor para conter o ficheiro jar.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Carregar o ficheiro jar para a conta de armazenamento do Azure com os seguintes comandos.

```bash
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

Variável `jarUrl` agora contém o caminho acessível ao público para o ficheiro jar.

## <a name="submit-a-spark-job"></a>Submeter uma tarefa do Spark

Inicie o kube proxy num separado da linha de comandos com o código a seguir.

```bash
kubectl proxy
```

Navegue de volta para a raiz do repositório de Spark.

```bash
cd $sparkdir
```

Submeter a tarefa com `spark-submit`.

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Esta operação inicia a tarefa do Spark, o que transmite o estado da tarefa à sua sessão de shell. Enquanto a tarefa está em execução, pode ver o pod de controlador do Spark e pods de executor usando o kubectl obtém pods de comando. Abra uma sessão de terminal em segundo lugar para executar esses comandos.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Enquanto a tarefa está em execução, também pode acessar a interface do Usuário do Spark. A sessão de terminal em segundo lugar, utilize o `kubectl port-forward` comando fornecer acesso à interface do Usuário do Spark.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Para acessar a interface do Usuário do Spark, abra o endereço `127.0.0.1:4040` num browser.

![Spark da interface do Usuário](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Obter resultados da tarefa e registos

Depois da tarefa foi concluída, o pod de driver estará num estado "Concluído". Obtenha o nome de pod com o seguinte comando.

```bash
kubectl get pods --show-all
```

Saída:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Utilize o `kubectl logs` comando para obter registos do pod de controlador do spark. Substitua o nome de pod com o nome do seu pod de driver.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

Dentro destes registos, pode ver o resultado da tarefa do Spark, o que é o valor de Pi.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Pacote de jar com imagem de contentor

No exemplo acima, o ficheiro jar do Spark foi carregado para o armazenamento do Azure. Outra opção é empacotar o ficheiro jar em imagens personalizadas do Docker.

Para tal, localize a `dockerfile` para a imagem de Spark localizado em `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` diretório. Adicionar estou `ADD` instrução para a tarefa do Spark `jar` em algum lugar entre `WORKDIR` e `ENTRYPOINT` declarações.

Atualize o caminho de jar para a localização do `SparkPi-assembly-0.1.0-SNAPSHOT.jar` arquivo no sistema de desenvolvimento. Também pode utilizar o seu próprio ficheiro jar personalizado.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Crie e envie a imagem com os scripts incluídos do Spark.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Quando executar a tarefa, em vez de indicar um URL de jar remoto, o `local://` esquema pode ser utilizado com o caminho para o ficheiro. jar na imagem do Docker.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Do Spark [documentação][spark-docs]: "O agendador de Kubernetes está atualmente experimental. Em versões futuras, pode haver alterações comportamentais em termos de configuração, imagens de contentor e entrypoints".

## <a name="next-steps"></a>Passos Seguintes

Consulte a documentação do Spark para obter mais detalhes.

> [!div class="nextstepaction"]
> [Documentação do Spark][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://aka.ms/azure-jdks
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
