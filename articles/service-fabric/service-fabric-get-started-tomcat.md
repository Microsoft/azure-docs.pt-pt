---
title: Criar um recipiente para Apache Tomcat em Linux
description: Crie um recipiente Linux para expor uma aplicação em execução no servidor Apache Tomcat no Azure Service Fabric. Construa uma imagem Docker com a sua aplicação e servidor Apache Tomcat, empurre a imagem para um registo de contentores, construa e implemente uma aplicação de recipiente de tecido de serviço.
ms.topic: conceptual
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 1a699f3b35970270a9800162a6d8717682a168ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75614422"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Criar recipiente de tecido de serviço executando o servidor Apache Tomcat em Linux
Apache Tomcat é uma implementação popular e aberta das tecnologias Java Servlet e Java Server. Este artigo mostra-lhe como construir um recipiente com Apache Tomcat e uma simples aplicação Web, colocar o recipiente num cluster de Tecido de Serviço que funciona o Linux e ligar-se à aplicação Web.  

Para saber mais sobre Apache Tomcat, consulte a página inicial do [Apache Tomcat.](https://tomcat.apache.org/) 

## <a name="prerequisites"></a>Pré-requisitos
* Um computador de programação com:
  * [SDK e ferramentas do Service Fabric](service-fabric-get-started-linux.md).
  * [Docker CE para Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [CLI do Service Fabric](service-fabric-cli.md)

* Um registo de contentores no Registo de Contentores de Azure. Pode criar um registo de contentores na sua assinatura Azure utilizando [o portal Azure](../container-registry/container-registry-get-started-portal.md) ou [o Azure CLI](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Construa uma imagem de Tomcat e execute-a localmente
Siga os passos nesta secção para construir uma imagem docker baseada numa imagem Apache Tomcat e numa simples aplicação Web e, em seguida, executá-la em um recipiente no seu sistema local. 
 
1. Clone the [Service Fabric começando com](https://github.com/Azure-Samples/service-fabric-java-getting-started) repositório de amostras java no seu computador de desenvolvimento.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Alterar diretórios para o diretório de amostras do servidor Apache Tomcat *(service-fabric-java-getting-start-start/container-apache-tomcat-web-server-sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Crie um ficheiro Docker baseado na imagem oficial do [Tomcat](https://hub.docker.com/_/tomcat/) localizada no Docker Hub e na amostra do servidor Tomcat. No *diretório de amostra de amostra de tecido-tecido-java-tecido-java-start-start-in-container-apache-tomcat-web-server,* crie um ficheiro chamado *Dockerfile* (sem extensão de ficheiro). Adicione o seguinte ao *Dockerfile* e guarde as alterações:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Consulte a [referência Dockerfile](https://docs.docker.com/engine/reference/builder/) para obter mais informações.


4. Executar o `docker build` comando para criar a imagem que executa a sua aplicação web:

   ```bash
   docker build . -t tomcattest
   ```

   Este comando constrói a nova imagem utilizando as instruções no Dockerfile, nomeando (-t marcando) a imagem `tomcattest` . Para construir uma imagem de contentor, a imagem base é descarregada pela primeira vez a partir do Docker Hub e a aplicação é adicionada à sua imagem. 

   Quando o comando de criação concluir, execute o comando `docker images` para ver informações sobre a nova imagem:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Verifique se a sua aplicação contentorizada funciona localmente antes de a empurrar para o registo do contentor:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` nomeia o recipiente, para que possa recorrer a ele usando um nome amigável em vez do seu ID.
   * `-p` especifica o mapeamento da porta entre o recipiente e o so hospedeiro. 

   > [!Note]
   > A porta que abre com o `-p` parâmetro deve ser a porta onde a sua aplicação Tomcat ouve os pedidos. No exemplo atual, existe um Conector configurado no ficheiro *ApacheTomcat/conf/server.xml* para ouvir na porta 8080 para pedidos HTTP. Esta porta está mapeada para o porto 8080 no hospedeiro. 

   Para conhecer outros parâmetros, consulte a documentação do [Docker.](https://docs.docker.com/engine/reference/commandline/run/)

1. Para testar o seu recipiente, abra um browser e introduza um dos urls seguintes. Verá uma variante do "Hello World!" ecrã de boas-vindas para cada URL.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Olá mundo /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Pare o recipiente e elimine-o do seu computador de desenvolvimento:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Empurre a imagem de Tomcat para o seu registo de contentores
Agora que verificou que a imagem do Tomcat funciona num contentor no seu computador de desenvolvimento, empurre-a para um repositório num registo de contentores. Este artigo utiliza o Registo do Contentor Azure para armazenar a imagem, mas, com alguma modificação de etapas, pode utilizar qualquer registo de contentores que escolher. Neste artigo presume-se que o nome do registo é *miognositry* e o nome completo do registo é myregistry.azurecr.io. Mude-as adequadamente para o seu cenário. 

1. Corra `docker login` para iniciar sôms no registo do seu contentor com [as suas credenciais de registo](../container-registry/container-registry-authentication.md).

   O exemplo seguinte transmite o ID e a palavra-passe de um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md) do Azure Active Directory. Por exemplo, poderá ter atribuído um principal de serviço ao seu registo no âmbito de um cenário de automatização. Ou, pode iniciar sômeduísta usando o seu nome de utilizador e senha de registo.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. O comando seguinte cria uma etiqueta, ou alias, da imagem, com um caminho completamente qualificado para o seu registo. Este exemplo coloca a imagem no espaço de nomes `samples` para evitar sobrepovoar a raiz do registo.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. Envie a imagem para o seu registo de contentor:

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Construa e implemente a aplicação do contentor de tecido de serviço
Agora que empurrou a imagem do Tomcat para um registo de contentores, pode construir e implantar uma aplicação de contentores de Tecido de Serviço que retira a imagem do Tomcat do seu registo e executa-a como um serviço contentorizado no seu cluster. 

1. Crie um novo diretório fora do seu clone local (fora do *serviço-tecido-java-getting-insificador-lalagem).* Mude para ele e use Yeoman para criar um andaime para uma aplicação de recipiente: 

   ```bash
   yo azuresfcontainer 
   ```
   Introduza os seguintes valores quando solicitado:

   * Nomeie a sua aplicação: ServiceFabricTomcat
   * Nome do serviço de aplicação: TomcatService
   * Inserir o Nome de Imagem: Forneça o URL para a imagem do recipiente no registo do seu contentor; por exemplo, myregistry.azurecr.io/samples/tomcattest.
   * Comandos: Deixe isto em branco. Uma vez que esta imagem tem um ponto de entrada de carga de trabalho definido, não tem de especificar explicitamente comandos de entrada (os comandos são executados dentro do contentor, o que faz com que este continue em execução após o arranque).
   * Número de casos de pedido de contentor de hóspedes: 1

   ![Gerador Yeoman do Service Fabric para contentores](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. No manifesto de serviço (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml), *adicione o seguinte XML sob a etiqueta Root **ServiceManfest** para abrir a porta onde a sua aplicação está a ouvir pedidos. A etiqueta **Endpoint** declara o protocolo e a porta para o ponto final. Para este artigo, o serviço contentorizado escuta no porto 8080: 

   ```xml
   <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
       listen. Please note that if your service is partitioned, this port is shared with 
       replicas of different partitions that are placed in your code. -->
      <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
    </Endpoints>
   </Resources>
   ```

11. No manifesto de aplicação (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml), *sob a etiqueta **ServiceManifestImport,** adicione o seguinte XML. Substitua o **Nome** de Conta e **a Palavra-passe** na etiqueta **RepositoryCredentials** pelo nome do seu registo de contentores e pela palavra-passe necessária para o iniciar.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   A etiqueta **ContainerHostPolicies** especifica políticas de ativação dos hospedeiros de contentores.
    
   * A etiqueta **PortBinding** configura a política de mapeamento porta-a-anfitrião do contentor. O atributo **ContainerPort** é definido para 8080 porque o contentor expõe a porta 8080, conforme especificado no Dockerfile. O atributo **EndpointRef** é definido como "endpointTest", o ponto final definido no manifesto de serviço no passo anterior. Assim, os pedidos de entrada no serviço no porto 8080 estão mapeados para o porto 8080 no contentor. 
   * A etiqueta **RepositoryCredentials** especifica as credenciais que o recipiente precisa para autenticar com o repositório (privado) de onde retira a imagem. Não precisa desta política se a imagem for retirada de um repositório público.
    

12. Na pasta *ServiceFabricTomcat,* ligue-se ao seu conjunto de tecidos de serviço. 

   * Para ligar ao cluster local de tecido de serviço, corra:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Para ligar a um cluster Azure seguro, certifique-se de que o certificado do cliente está presente como um ficheiro .pem no diretório *ServiceFabricTomcat,* e executar: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     No comando anterior, `your-certificate.pem` substitua-o pelo nome do seu ficheiro de certificado de cliente. Em ambientes de desenvolvimento e teste, o certificado de cluster é frequentemente usado como certificado de cliente. Se o seu certificado não for auto-assinado, omita o `-no-verify` parâmetro. 
       
     Os certificados de cluster são normalmente descarregados localmente como ficheiros .pfx. Se ainda não tiver o seu certificado em formato PEM, pode executar o seguinte comando para criar um ficheiro .pem a partir de um ficheiro .pfx:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Se o ficheiro .pfx não estiver protegido por palavra-passe, utilize `-passin pass:` para o último parâmetro.


13. Execute o script de instalação fornecido no modelo para implementar a aplicação no seu cluster. O script copia o pacote de aplicações para a loja de imagens do cluster, regista o tipo de aplicação e cria uma instância da aplicação.

     ```bash
     ./install.sh
     ```

   Depois de executar o script de instalação, abra um browser e navegue para Service Fabric Explorer:
    
   * Num cluster local, utilize `http://localhost:19080/Explorer` (substitua o *local* local pelo IP privado do VM se utilizar vagrant no Mac OS X).
   * Num aglomerado Azure seguro, utilize `https://PublicIPorFQDN:19080/Explorer` . 
    
   Expanda o nó **de Aplicações** e note que existe agora uma entrada para o seu tipo de aplicação, **ServiceFabricTomcatType**, e outra para a primeira instância desse tipo. Pode levar alguns minutos para a aplicação ser totalmente implantada, por isso tenha paciência.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Para aceder à aplicação no servidor Tomcat, abra uma janela do navegador e introduza qualquer um dos URLs seguintes. Se se deslocar para o cluster local, utilize o *local local* para *publicIPorFQDN.* Verá uma variante do "Hello World!" ecrã de boas-vindas para cada URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Limpeza
Utilize o script de desinstalação fornecido no modelo para eliminar a instância de aplicação do seu cluster e não registar o tipo de aplicação.

```bash
./uninstall.sh
```

Depois de enviar a imagem para o registo de contentor, pode eliminar a imagem local do seu computador de programação:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Passos seguintes
* Para passos rápidos sobre as funcionalidades adicionais do recipiente Linux, leia [Criar a sua primeira aplicação de recipiente de tecido de serviço no Linux](service-fabric-get-started-containers-linux.md).
* Para passos mais detalhados em recipientes Linux, leia o tutorial tutorial da aplicação de [recipientes Create a Linux.](service-fabric-tutorial-create-container-images.md)
* Saiba mais sobre como executar [contentores no Service Fabric](service-fabric-containers-overview.md).


