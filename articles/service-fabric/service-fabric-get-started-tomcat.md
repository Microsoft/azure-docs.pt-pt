---
title: Crie um recipiente para Apache Tomcat em Linux
description: Crie o recipiente Linux para expor uma aplicação em execução no servidor Apache Tomcat no Tecido de Serviço Azure. Construa uma imagem Docker com a sua aplicação e o servidor Apache Tomcat, empurre a imagem para um registo de contentores, construa e implemente uma aplicação de recipiente sinuoso de serviço.
ms.topic: conceptual
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 1a699f3b35970270a9800162a6d8717682a168ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614422"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Criar recipiente de tecido de serviço que executa o servidor Apache Tomcat no Linux
Apache Tomcat é uma implementação popular e de código aberto das tecnologias Java Servlet e Java Server. Este artigo mostra-lhe como construir um recipiente com Apache Tomcat e uma aplicação Web simples, implementar o recipiente para um cluster de tecido de serviço que executa o Linux, e ligar-se à aplicação Web.  

Para saber mais sobre Apache Tomcat, consulte a página inicial do [Apache Tomcat.](https://tomcat.apache.org/) 

## <a name="prerequisites"></a>Pré-requisitos
* Um computador de programação com:
  * [SDK e ferramentas do Service Fabric](service-fabric-get-started-linux.md).
  * [Docker CE para Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [CLI do Service Fabric](service-fabric-cli.md)

* Um registo de contentores no Registo de Contentores De Azure. Pode criar um registo de contentores na sua subscrição Azure utilizando [o portal Azure](../container-registry/container-registry-get-started-portal.md) ou [o Azure CLI](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Construa uma imagem tomcat e execute-a localmente
Siga os passos nesta secção para construir uma imagem do Docker com base numa imagem Apache Tomcat e numa simples aplicação Web e, em seguida, executá-la num recipiente no seu sistema local. 
 
1. Clone o [Tecido de Serviço começando com amostras java](https://github.com/Azure-Samples/service-fabric-java-getting-started) repositório no seu computador de desenvolvimento.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Altere os diretórios para o diretório da amostra do servidor Apache Tomcat *(service-fabric-java-getting-started/container-apache-tomcat-web-server-sample):*

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Crie um ficheiro Docker com base na imagem oficial do [Tomcat](https://hub.docker.com/_/tomcat/) localizada no Docker Hub e na amostra do servidor Tomcat. No diretório de amostrade servidor de *serviço-tecido-java-inicia-iniciação/contentor-apache-tomcat-web-servidor,* crie um ficheiro chamado *Dockerfile* (sem extensão de ficheiro). Adicione o seguinte ao *Dockerfile* e guarde as alterações:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Consulte a referência do [Dockerfile](https://docs.docker.com/engine/reference/builder/) para mais informações.


4. Executar `docker build` o comando para criar a imagem que executa a sua aplicação web:

   ```bash
   docker build . -t tomcattest
   ```

   Este comando constrói a nova imagem utilizando as instruções no Dockerfile, `tomcattest`nomeando (-t marcação) a imagem . Para construir uma imagem de contentor, a imagem base é primeiramente descarregada a partir do Docker Hub e a aplicação é adicionada a ela. 

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
   
   * `--name`nomeia o recipiente, para que possa referir-se a ele usando um nome amigável em vez do seu ID.
   * `-p`especifica o mapeamento da porta entre o contentor e o hospedeiro. 

   > [!Note]
   > A porta que `-p` abre com o parâmetro deve ser a porta onde a sua aplicação Tomcat ouve pedidos. No exemplo atual, existe um Connector configurado no ficheiro *ApacheTomcat/conf/server.xml* para ouvir na porta 8080 para pedidos HTTP. Este porto está mapeado para o porto 8080 no hospedeiro. 

   Para saber sobre outros parâmetros, consulte a [documentação de execução do Docker.](https://docs.docker.com/engine/reference/commandline/run/)

1. Para testar o seu recipiente, abra um browser e introduza um dos seguintes URLs. Verá uma variante do "Hello World!" ecrã de boas-vindas para cada URL.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Olá mundo /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Pare o recipiente e elimine-o do seu computador de desenvolvimento:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Empurre a imagem tomcat para o seu registo de contentores
Agora que verificou que a imagem tomcat corre num contentor no seu computador de desenvolvimento, empurre-a para um repositório num registo de contentores. Este artigo utiliza o Registo de Contentores Azure para armazenar a imagem, mas, com alguma alteração de etapas, pode utilizar qualquer registo de contentores que escolha. Neste artigo, presume-se que o nome do registo é *meu registo* e o nome completo do registo é myregistry.azurecr.io. Mude-os adequadamente para o seu cenário. 

1. Corra `docker login` para iniciar sessão no seu registo de contentores com as suas [credenciais de registo](../container-registry/container-registry-authentication.md).

   O exemplo seguinte transmite o ID e a palavra-passe de um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md) do Azure Active Directory. Por exemplo, poderá ter atribuído um principal de serviço ao seu registo no âmbito de um cenário de automatização. Ou pode iniciar sessão utilizando o seu nome de utilizador e senha de registo.

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

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Construir e implementar a aplicação de contentores De Tecido de Serviço
Agora que empurrou a imagem tomcat para um registo de contentores, pode construir e implementar uma aplicação de contentor de tecido de serviço que puxa a imagem Tomcat do seu registo e a executa como um serviço contentorizado no seu cluster. 

1. Crie um novo diretório fora do seu clone local (fora da árvore de diretório de *serviço-tecido-java-iniciando-se).* Mude para ele e use Yeoman para criar um andaime para uma aplicação de recipiente: 

   ```bash
   yo azuresfcontainer 
   ```
   Introduza os seguintes valores quando solicitado:

   * Nomeie a sua aplicação: ServiceFabricTomcat
   * Nome do serviço de aplicação: TomcatService
   * Insera o Nome de Imagem: Forneça o URL para a imagem do recipiente no registo do seu contentor; por exemplo, myregistry.azurecr.io/samples/tomcattest.
   * Comandos: Deixe isto em branco. Uma vez que esta imagem tem um ponto de entrada de carga de trabalho definido, não tem de especificar explicitamente comandos de entrada (os comandos são executados dentro do contentor, o que faz com que este continue em execução após o arranque).
   * Número de casos de pedido de contentor de hóspedes: 1

   ![Gerador Yeoman do Service Fabric para contentores](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. No manifesto de serviço *(ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml),* adicione o seguinte XML sob a etiqueta **Root ServiceManfest** para abrir a porta em que a sua aplicação está a ouvir pedidos. A etiqueta **Endpoint** declara o protocolo e a porta para o ponto final. Para este artigo, o serviço contentorizado ouve no porto 8080: 

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

11. No manifesto de aplicação *(ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml),* sob a etiqueta **ServiceManifestImport,** adicione o seguinte XML. Substitua o Nome de **Conta** e **a Palavra-Passe** na etiqueta **RepositoryCredenciais** com o nome do seu registo de contentores e a palavra-passe necessária para iniciar sessão no mesmo.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   A etiqueta **ContainerHostPolicies** especifica as políticas de ativação dos anfitriões de contentores.
    
   * A etiqueta **PortBinding** configura a política de mapeamento portuário porta-a-anfitrião. O atributo **ContainerPort** está definido para 8080 porque o recipiente expõe a porta 8080, conforme especificado no Dockerfile. O atributo **EndpointRef** está definido para "endpointTest", o ponto final definido no manifesto de serviço no escalão anterior. Assim, os pedidos de entrada no serviço no porto 8080 são mapeados para o porto 8080 no contentor. 
   * A etiqueta **RepositoryCredenciais** especifica as credenciais de que o recipiente precisa para autenticar com o repositório (privado) de onde retira a imagem. Não precisa desta apólice se a imagem for retirada de um repositório público.
    

12. Na pasta *ServiceFabricTomcat,* ligue-se ao seu cluster de tecido de serviço. 

   * Para ligar ao cluster de tecido de serviço local, corra:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Para se ligar a um cluster Azure seguro, certifique-se de que o certificado de cliente está presente como um ficheiro .pem no diretório *ServiceFabricTomcat,* e executar: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     No comando anterior, `your-certificate.pem` substitua-o pelo nome do seu ficheiro de certificado de cliente. Em ambientes de desenvolvimento e teste, o certificado de cluster é frequentemente usado como certificado de cliente. Se o seu certificado não for auto-assinado, omita o `-no-verify` parâmetro. 
       
     Os certificados de cluster são normalmente descarregados localmente como ficheiros .pfx. Se ainda não tiver o seu certificado em formato PEM, pode executar o seguinte comando para criar um ficheiro .pem a partir de um ficheiro .pfx:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Se o seu ficheiro .pfx não `-passin pass:` estiver protegido por palavra-passe, utilize para o último parâmetro.


13. Executar o script de instalação fornecido no modelo para implementar a aplicação para o seu cluster. O script copia o pacote de aplicação para a loja de imagens do cluster, regista o tipo de aplicação e cria uma instância da aplicação.

     ```bash
     ./install.sh
     ```

   Depois de executar o script de instalação, abra um navegador e navegue para service Fabric Explorer:
    
   * Num cluster local, `http://localhost:19080/Explorer` utilize (substitua o *hospedeiro local* pelo IP privado do VM se utilizar vagrant no Mac OS X).
   * Num cluster Azure seguro, utilize. `https://PublicIPorFQDN:19080/Explorer` 
    
   Expanda o nó de **Aplicações** e note que existe agora uma entrada para o seu tipo de aplicação, **ServiceFabricTomcatType,** e outra para a primeira instância desse tipo. Pode levar alguns minutos para a aplicação ser totalmente implementada, por isso seja paciente.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Para aceder à aplicação no servidor Tomcat, abra uma janela do navegador e introduza qualquer um dos seguintes URLs. Se foi implantado no cluster local, utilize *o local de acolhimento* para *PublicIPorFQDN*. Verá uma variante do "Hello World!" ecrã de boas-vindas para cada URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Limpeza
Utilize o script de desinstalação fornecido no modelo para eliminar a instância de aplicação do seu cluster e desregistar o tipo de aplicação.

```bash
./uninstall.sh
```

Depois de enviar a imagem para o registo de contentor, pode eliminar a imagem local do seu computador de programação:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Passos seguintes
* Para dar passos rápidos em funcionalidades adicionais do recipiente Linux, leia [Create your first Service Fabric container application on Linux](service-fabric-get-started-containers-linux.md).
* Para passos mais detalhados nos recipientes Linux, leia o tutorial tutorial de aplicação de [contentores Create a Linux.](service-fabric-tutorial-create-container-images.md)
* Saiba mais sobre como executar [contentores no Service Fabric](service-fabric-containers-overview.md).


