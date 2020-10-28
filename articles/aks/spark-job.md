---
title: Executar um trabalho apache spark com o Serviço Azure Kubernetes (AKS)
description: Utilize o Serviço Azure Kubernetes (AKS) para criar e executar uma função Apache Spark para o processamento de dados em larga escala.
author: lenadroid
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e94d9d586f15c228436a8a9d48fb12c26369c449
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742351"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Executando apache spark empregos em AKS

[Apache Spark][apache-spark] é um motor rápido para o processamento de dados em larga escala. A partir do [lançamento da Spark 2.3.0,][spark-kubernetes-earliest-version]a Apache Spark apoia a integração nativa com os clusters Kubernetes. O Azure Kubernetes Service (AKS) é um ambiente gerido pela Kubernetes em funcionamento em Azure. Este documento detalha a preparação e execução de empregos apache spark num cluster Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos dentro deste artigo, você precisa do seguinte.

* Compreensão básica de Kubernetes e [Apache Spark.][spark-quickstart]
* [Conta Docker Hub,][docker-hub] ou registo [de contentores Azure.][acr-create]
* Azure CLI [instalado][azure-cli] no seu sistema de desenvolvimento.
* [JDK 8][java-install] instalado no seu sistema.
* [Apache Maven][maven-install] instalado no seu sistema.
* SBT ([Scala Build Tool)][sbt-install]instalado no seu sistema.
* Ferramentas de linha de comando Git instaladas no seu sistema.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

A faísca é usada para o processamento de dados em larga escala e requer que os nós Kubernetes sejam dimensionados para satisfazer os requisitos de recursos spark. Recomendamos um tamanho mínimo `Standard_D3_v2` para os seus nós do Serviço Azure Kubernetes (AKS).

Se precisar de um cluster AKS que cumpra esta recomendação mínima, execute os seguintes comandos.

Criar um grupo de recursos para o cluster.

```azurecli
az group create --name mySparkCluster --location eastus
```

Criar um diretor de serviço para o cluster. Depois de criado, você precisará do appId principal de serviço e senha para o comando seguinte.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Crie o cluster AKS com nós de `Standard_D3_v2` tamanho, e valores de appId e palavra-passe passados como parâmetros de serviço-principal e cliente-secreto.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Ligue-se ao cluster AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Se estiver a utilizar o Registo do Contentor Azure (ACR) para armazenar imagens de contentores, configurar a autenticação entre a AKS e a ACR. Consulte a [documentação de autenticação ACR][acr-aks] para estes passos.

## <a name="build-the-spark-source"></a>Construa a fonte de faísca

Antes de executar trabalhos de faísca num cluster AKS, você precisa construir o código fonte Spark e empacotá-lo em uma imagem de recipiente. A fonte Spark inclui scripts que podem ser usados para completar este processo.

Clone o repositório do projeto Spark para o seu sistema de desenvolvimento.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Mude para o diretório do repositório clonado e guarde o caminho da fonte de faísca para uma variável.

```bash
cd spark
sparkdir=$(pwd)
```

Se tiver várias versões JDK instaladas, pode `JAVA_HOME` utilizar a versão 8 para a sessão atual.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Executar o seguinte comando para construir o código fonte Spark com suporte Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Os seguintes comandos criam a imagem do recipiente Spark e empurram-na para um registo de imagem de contentor. `registry.example.com`Substitua-o pelo nome do seu registo de contentores e `v1` pela etiqueta que prefere utilizar. Se utilizar o Docker Hub, este valor é o nome do registo. Se utilizar o Registo do Contentor Azure (ACR), este valor é o nome do servidor de login ACR.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Empurre a imagem do recipiente para o registo de imagem do seu contentor.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Prepare um trabalho de faísca

Em seguida, prepare um trabalho de Faísca. Um ficheiro de frasco é usado para manter o trabalho de Faísca e é necessário para executar o `spark-submit` comando. O frasco pode ser tornado acessível através de um URL público ou pré-embalado dentro de uma imagem de recipiente. Neste exemplo, é criado um frasco de amostra para calcular o valor de Pi. Este frasco é então enviado para o armazenamento de Azure. Se tiver um jarro existente, sinta-se à vontade para substituir

Crie um diretório onde gostaria de criar o projeto para um trabalho de Spark.

```bash
mkdir myprojects
cd myprojects
```

Crie um novo projeto Scala a partir de um modelo.

```bash
sbt new sbt/scala-seed.g8
```

Quando solicitado, insira `SparkPi` o nome do projeto.

```bash
name [Scala Seed Project]: SparkPi
```

Navegue para o recém-criado diretório de projetos.

```bash
cd sparkpi
```

Executar os seguintes comandos para adicionar um plugin SBT, que permite embalar o projeto como um arquivo de jarro.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Executar estes comandos para copiar o código de amostra no projeto recém-criado e adicionar todas as dependências necessárias.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11"/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Para embalar o projeto em um jarro, executar o seguinte comando.

```bash
sbt assembly
```

Após uma embalagem bem sucedida, deverá ver uma saída semelhante à seguinte.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Copiar trabalho para armazenamento

Crie uma conta de armazenamento Azure e um recipiente para segurar o ficheiro do frasco.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Faça o upload do ficheiro do frasco para a conta de armazenamento Azure com os seguintes comandos.

```azurecli
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

A variável `jarUrl` agora contém o caminho acessível ao público para o ficheiro do frasco.

## <a name="submit-a-spark-job"></a>Submeter um trabalho de faísca

Inicie o kube-proxy numa linha de comando separada com o seguinte código.

```bash
kubectl proxy
```

Navegue de volta para a raiz do repositório de Faíscas.

```bash
cd $sparkdir
```

Crie uma conta de serviço que tenha permissões suficientes para gerir um trabalho.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Submeta o trabalho utilizando `spark-submit` .

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Esta operação inicia o trabalho spark, que transmite o estado do trabalho para a sua sessão de conchas. Enquanto o trabalho está em andamento, você pode ver spark driver pod e cápsulas executoras usando o comando kubectl get pods. Abra uma segunda sessão terminal para executar estes comandos.

```console
kubectl get pods
```

```output
NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Enquanto o trabalho está em execução, você também pode aceder ao Spark UI. Na segunda sessão terminal, utilize o `kubectl port-forward` comando para fornecer acesso à Spark UI.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Para aceder ao Spark UI, abra o endereço `127.0.0.1:4040` num browser.

![UI faísca](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Obtenha resultados de emprego e registos

Depois de terminado o trabalho, a cápsula de motorista estará em estado "concluído". Obtenha o nome da cápsula com o seguinte comando.

```bash
kubectl get pods --show-all
```

Resultado:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Use o `kubectl logs` comando para obter registos da cápsula do controlador de faíscas. Substitua o nome da cápsula pelo nome da sua cápsula de motorista.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

Dentro destes troncos, você pode ver o resultado do trabalho de Faísca, que é o valor de Pi.

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Jarro de pacote com imagem de recipiente

No exemplo acima, o ficheiro do frasco Spark foi enviado para o armazenamento do Azure. Outra opção é embalar o ficheiro do frasco em imagens personalizadas do Docker.

Para tal, encontre a `dockerfile` imagem Spark localizada no `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` diretório. Adicione uma `ADD` declaração para o trabalho de Spark em algum lugar entre e `jar` `WORKDIR` `ENTRYPOINT` declarações.

Atualize o caminho do frasco para a localização do `SparkPi-assembly-0.1.0-SNAPSHOT.jar` ficheiro no seu sistema de desenvolvimento. Você também pode usar seu próprio arquivo de jarro personalizado.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Construa e empurre a imagem com os scripts spark incluídos.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Ao executar o trabalho, em vez de indicar um URL de frasco remoto, o `local://` esquema pode ser usado com o caminho para o ficheiro do frasco na imagem do Docker.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Da [documentação][spark-docs]Spark : "O programador kubernetes é atualmente experimental. Em futuras versões, pode haver alterações comportamentais em torno da configuração, imagens de contentores e pontos de entrada".

## <a name="next-steps"></a>Passos seguintes

Consulte a documentação da Spark para mais detalhes.

> [!div class="nextstepaction"]
> [Documentação de faísca][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: /azure/developer/java/fundamentals/java-jdk-long-term-support
[maven-install]: https://maven.apache.org/install.html
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-kubernetes-earliest-version]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-quickstart]: ./index.yml
[azure-cli]: /cli/azure/?view=azure-cli-latest
[storage-account]: ../storage/blobs/storage-quickstart-blobs-cli.md
