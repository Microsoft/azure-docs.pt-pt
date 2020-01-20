---
title: Executar um trabalho de Apache Spark com o serviço de kubernetes do Azure (AKS)
description: Usar o AKS (serviço kubernetes do Azure) para executar um trabalho de Apache Spark
author: lenadroid
ms.service: container-service
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: 72e87acf418d5085a76f18c595bf31d43b0d64ac
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274653"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Executando trabalhos de Apache Spark no AKS

[Apache Spark][apache-spark] é um mecanismo rápido para processamento de dados em larga escala. A partir da [versão 2.3.0 do Spark][spark-latest-release], o Apache Spark dá suporte à integração nativa com clusters kubernetes. O AKS (serviço kubernetes do Azure) é um ambiente kubernetes gerenciado em execução no Azure. Este documento detalha a preparação e a execução de trabalhos de Apache Spark em um cluster do AKS (serviço de kubernetes do Azure).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Noções básicas sobre kubernetes e [Apache Spark][spark-quickstart].
* Conta do [Hub do Docker][docker-hub] ou um [registro de contêiner do Azure][acr-create].
* CLI do Azure [instalado][azure-cli] no seu sistema de desenvolvimento.
* [JDK 8][java-install] instalado em seu sistema.
* SBT ([ferramenta de criação escalar][sbt-install]) instalada em seu sistema.
* Ferramentas de linha de comando git instaladas em seu sistema.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

O Spark é usado para processamento de dados em larga escala e requer que os nós kubernetes sejam dimensionados para atender aos requisitos de recursos do Spark. Recomendamos um tamanho mínimo de `Standard_D3_v2` para seus nós do AKS (serviço kubernetes do Azure).

Se você precisar de um cluster AKS que atenda a essa recomendação mínima, execute os comandos a seguir.

Crie um grupo de recursos para o cluster.

```azurecli
az group create --name mySparkCluster --location eastus
```

Crie uma entidade de serviço para o cluster. Depois que ele for criado, você precisará do appId e da senha da entidade de serviço para o próximo comando.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Crie o cluster AKS com nós de tamanho `Standard_D3_v2`e os valores de appId e senha passados como parâmetros de entidade de serviço e de segredo do cliente.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Conecte-se ao cluster AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Se você estiver usando o ACR (registro de contêiner do Azure) para armazenar imagens de contêiner, configure a autenticação entre AKS e ACR. Consulte a [documentação de autenticação do ACR][acr-aks] para essas etapas.

## <a name="build-the-spark-source"></a>Criar a origem do Spark

Antes de executar trabalhos do Spark em um cluster AKS, você precisa criar o código-fonte do Spark e empacotá-lo em uma imagem de contêiner. A origem do Spark inclui scripts que podem ser usados para concluir esse processo.

Clone o repositório do projeto do Spark para seu sistema de desenvolvimento.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Altere para o diretório do repositório clonado e salve o caminho da origem do Spark em uma variável.

```bash
cd spark
sparkdir=$(pwd)
```

Se você tiver várias versões do JDK instaladas, defina `JAVA_HOME` para usar a versão 8 para a sessão atual.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Execute o comando a seguir para compilar o código-fonte do Spark com suporte a kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Os comandos a seguir criam a imagem de contêiner do Spark e as enviam para um registro de imagem de contêiner. Substitua `registry.example.com` pelo nome do registro de contêiner e `v1` com a marca que você prefere usar. Se estiver usando o Hub do Docker, esse valor será o nome do registro. Se estiver usando o ACR (registro de contêiner do Azure), esse valor será o nome do servidor de logon do ACR.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Envie por push a imagem de contêiner para o registro de imagem de contêiner.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Preparar um trabalho do Spark

Em seguida, prepare um trabalho do Spark. Um arquivo JAR é usado para manter o trabalho do Spark e é necessário ao executar o comando `spark-submit`. O jar pode se tornar acessível por meio de uma URL pública ou previamente empacotado dentro de uma imagem de contêiner. Neste exemplo, um jar de exemplo é criado para calcular o valor de PI. Esse jar é então carregado no armazenamento do Azure. Se você tiver um jar existente, fique à vontade para substituir

Crie um diretório no qual você gostaria de criar o projeto para um trabalho do Spark.

```bash
mkdir myprojects
cd myprojects
```

Crie um novo projeto escalar a partir de um modelo.

```bash
sbt new sbt/scala-seed.g8
```

Quando solicitado, insira `SparkPi` para o nome do projeto.

```bash
name [Scala Seed Project]: SparkPi
```

Navegue até o diretório do projeto recém-criado.

```bash
cd sparkpi
```

Execute os comandos a seguir para adicionar um plug-in SBT, que permite empacotar o projeto como um arquivo jar.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Execute estes comandos para copiar o código de exemplo no projeto recém-criado e adicionar todas as dependências necessárias.

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

Para empacotar o projeto em um jar, execute o comando a seguir.

```bash
sbt assembly
```

Após o empacotamento bem-sucedido, você deverá ver uma saída semelhante à seguinte.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Copiar trabalho para armazenamento

Crie uma conta de armazenamento do Azure e um contêiner para armazenar o arquivo jar.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Carregue o arquivo JAR na conta de armazenamento do Azure com os comandos a seguir.

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

A variável `jarUrl` agora contém o caminho publicamente acessível para o arquivo jar.

## <a name="submit-a-spark-job"></a>Enviar um trabalho do Spark

Inicie o Kube-proxy em uma linha de comando separada com o código a seguir.

```bash
kubectl proxy
```

Navegue de volta para a raiz do repositório do Spark.

```bash
cd $sparkdir
```

Crie uma conta de serviço que tenha permissões suficientes para executar um trabalho.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Envie o trabalho usando `spark-submit`.

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

Essa operação inicia o trabalho do Spark, que transmite o status do trabalho para a sessão do Shell. Enquanto o trabalho estiver em execução, você poderá ver o Pod do driver do Spark e os pods do executor usando o comando kubectl Get pods. Abra uma segunda sessão de terminal para executar esses comandos.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Enquanto o trabalho estiver em execução, você também poderá acessar a interface do usuário do Spark. Na segunda sessão de terminal, use o comando `kubectl port-forward` fornecer acesso à interface do usuário do Spark.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Para acessar a interface do usuário do Spark, abra o endereço `127.0.0.1:4040` em um navegador.

![IU do Spark](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Obter resultados e logs de trabalho

Depois que o trabalho for concluído, o Pod do driver estará em um estado "concluído". Obtenha o nome do pod com o comando a seguir.

```bash
kubectl get pods --show-all
```

Saída:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Use o comando `kubectl logs` para obter logs do Pod do driver Spark. Substitua o nome do pod pelo nome do Pod do driver.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

Nesses logs, você pode ver o resultado do trabalho do Spark, que é o valor de PI.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Pacote jar com imagem de contêiner

No exemplo acima, o arquivo JAR do Spark foi carregado no armazenamento do Azure. Outra opção é empacotar o arquivo jar em imagens do Docker personalizadas.

Para fazer isso, localize o `dockerfile` para a imagem do Spark localizada no diretório `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/`. Adicione a instrução am `ADD` para o trabalho do Spark `jar` em algum lugar entre as declarações `WORKDIR` e `ENTRYPOINT`.

Atualize o caminho jar para o local do arquivo de `SparkPi-assembly-0.1.0-SNAPSHOT.jar` no sistema de desenvolvimento. Você também pode usar seu próprio arquivo JAR personalizado.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Crie e envie por push a imagem com os scripts do Spark incluídos.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Ao executar o trabalho, em vez de indicar uma URL jar remota, o esquema de `local://` pode ser usado com o caminho para o arquivo JAR na imagem do Docker.

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
> Na [documentação][spark-docs]do Spark: "o Agendador kubernetes é experimental no momento. Em versões futuras, pode haver alterações comportamentais em relação à configuração, às imagens de contêiner e aos entryPoints ".

## <a name="next-steps"></a>Passos seguintes

Confira a documentação do Spark para obter mais detalhes.

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
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
