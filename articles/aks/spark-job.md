---
title: Executar um trabalho apache Spark com o Serviço Azure Kubernetes (AKS)
description: Utilize o Serviço Azure Kubernetes (AKS) para criar e executar um trabalho da Apache Spark para o processamento de dados em larga escala.
author: lenadroid
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: 2e399c1a7b0f9bbc2aac375fe8af969a2b9e0e48
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80877632"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Executar empregos apache spark na AKS

[Apache Spark][apache-spark] é um motor rápido para o processamento de dados em larga escala. A partir do lançamento da [Spark 2.3.0,][spark-latest-release]a Apache Spark apoia a integração nativa com os clusters kubernetes. Azure Kubernetes Service (AKS) é um ambiente gerido pela Kubernetes em Funcionamento em Azure. Este documento detalha a preparação e execução de empregos da Apache Spark num cluster do Serviço Azure Kubernetes (AKS).

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos dentro deste artigo, precisa dos seguintes.

* Compreensão básica de Kubernetes e [Apache Spark.][spark-quickstart]
* [Conta Docker Hub,][docker-hub] ou um registo de [contentores Azure.][acr-create]
* Azure CLI [instalado][azure-cli] no seu sistema de desenvolvimento.
* [JDK 8][java-install] instalado no seu sistema.
* SBT[(Scala Build Tool][sbt-install]) instalado no seu sistema.
* Ferramentas de linha de comando Git instaladas no seu sistema.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

A faísca é usada para o processamento de dados em larga escala e requer que os nódoskubernetes sejam dimensionados para satisfazer os requisitos de recursos da Spark. Recomendamos um tamanho `Standard_D3_v2` mínimo para os seus nós de Serviço Azure Kubernetes (AKS).

Se precisar de um cluster AKS que cumpra esta recomendação mínima, execute os seguintes comandos.

Crie um grupo de recursos para o cluster.

```azurecli
az group create --name mySparkCluster --location eastus
```

Crie um Diretor de Serviço para o cluster. Depois de criado, necessitará da appId do Diretor de Serviço e da palavra-passe para o próximo comando.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Crie o cluster AKS com nós `Standard_D3_v2`de tamanho , e valores de appId e password passados como parâmetros principais de serviço e segredo de cliente.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Ligue-se ao cluster AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Se estiver a utilizar o Registo de Contentores Azure (ACR) para armazenar imagens de contentores, configure a autenticação entre AKS e ACR. Consulte a documentação de [autenticação ACR][acr-aks] para estas etapas.

## <a name="build-the-spark-source"></a>Construa a fonte de faísca

Antes de executar trabalhos spark em um cluster AKS, você precisa construir o código fonte Spark e embalá-lo em uma imagem de recipiente. A fonte Spark inclui scripts que podem ser usados para completar este processo.

Clone o repositório do projeto Spark para o seu sistema de desenvolvimento.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Mude para o diretório do repositório clonado e guarde o caminho da fonte de faísca para uma variável.

```bash
cd spark
sparkdir=$(pwd)
```

Se tiver várias versões JDK instaladas, desloque-se `JAVA_HOME` para utilizar a versão 8 para a sessão atual.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Executar o seguinte comando para construir o código fonte Spark com suporte kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Os seguintes comandos criam a imagem do recipiente Spark e empurram-na para um registo de imagem de recipiente. Substitua-o `registry.example.com` pelo nome do `v1` seu registo de contentores e pela etiqueta que prefere utilizar. Se utilizar o Docker Hub, este valor é o nome do registo. Se utilizar o Registo de Contentores Azure (ACR), este valor é o nome do servidor de login ACR.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Empurre a imagem do recipiente para o registo de imagem do recipiente.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Prepare um trabalho de Faísca

Em seguida, prepare um trabalho de Spark. Um ficheiro de jarro é usado para manter `spark-submit` o trabalho de Spark e é necessário ao dirigir o comando. O jarro pode ser acessível através de um URL público ou pré-embalado dentro de uma imagem de recipiente. Neste exemplo, é criado um frasco de amostra para calcular o valor de Pi. Este frasco é então enviado para o armazém do Azure. Se você tem um jarro existente, sinta-se à vontade para substituir

Crie um diretório onde gostaria de criar o projeto para um trabalho de Spark.

```bash
mkdir myprojects
cd myprojects
```

Crie um novo projeto Scala a partir de um modelo.

```bash
sbt new sbt/scala-seed.g8
```

Quando solicitado, `SparkPi` insira o nome do projeto.

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

Execute estes comandos para copiar o código da amostra no projeto recém-criado e adicionar todas as dependências necessárias.

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

Para embalar o projeto num frasco, execute o seguinte comando.

```bash
sbt assembly
```

Após uma embalagem bem sucedida, deve ver uma saída semelhante à seguinte.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Trabalho de cópia para armazenamento

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

Variável `jarUrl` agora contém o caminho acessível ao público para o arquivo do jarro.

## <a name="submit-a-spark-job"></a>Submeta um trabalho de Faísca

Inicie o kube-proxy numa linha de comando separada com o seguinte código.

```bash
kubectl proxy
```

Navegue de volta à raiz do repositório Spark.

```bash
cd $sparkdir
```

Crie uma conta de serviço que tenha permissões suficientes para gerir um trabalho.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Submeter o `spark-submit`trabalho usando .

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

Esta operação inicia o trabalho spark, que transmite o estado de trabalho para a sua sessão de concha. Enquanto o trabalho está em execução, você pode ver a cápsula de motorista Spark e cápsulas executorusando o comando kubectl get pods. Abra uma segunda sessão terminal para executar estes comandos.

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

Enquanto o trabalho está em andamento, você também pode aceder à UI Spark. Na segunda sessão terminal, utilize o `kubectl port-forward` comando para fornecer acesso à Spark UI.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Para aceder à Spark UI, abra o endereço `127.0.0.1:4040` num browser.

![Faísca UI](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Obtenha resultados de emprego e registos

Depois de terminar o trabalho, a cápsula de condutor estará em estado "Concluído". Obtenha o nome da cápsula com o seguinte comando.

```bash
kubectl get pods --show-all
```

Saída:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Use `kubectl logs` o comando para obter registos da cápsula do condutor de faíscas. Substitua o nome da cápsula com o nome do seu casulo de condutor.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

Dentro destes registos, você pode ver o resultado do trabalho de Spark, que é o valor de Pi.

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Frasco de pacote com imagem de recipiente

No exemplo acima, o ficheiro spark jar foi enviado para o armazenamento Azure. Outra opção é embalar o ficheiro do frasco em imagens do Docker personalizadas.

Para isso, encontre `dockerfile` a imagem de `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` Spark localizada no diretório. Adicione `ADD` uma declaração para `jar` o `WORKDIR` `ENTRYPOINT` trabalho de Spark algures entre as declarações.

Atualize o caminho do `SparkPi-assembly-0.1.0-SNAPSHOT.jar` frasco para a localização do ficheiro no seu sistema de desenvolvimento. Também pode usar o seu próprio ficheiro de frascopersonalizado.

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

Ao executar o trabalho, em vez de `local://` indicar um URL de jarro remoto, o esquema pode ser usado com o caminho para o ficheiro do jarro na imagem do Docker.

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
> Da [documentação][spark-docs]spark : "O programador kubernetes é atualmente experimental. Em futuras versões, poderá haver alterações comportamentais em torno da configuração, imagens de contentores e pontos de entrada".

## <a name="next-steps"></a>Passos seguintes

Consulte a documentação da Spark para mais detalhes.

> [!div class="nextstepaction"]
> [Documentação de faíscas][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://aka.ms/azure-jdks
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
