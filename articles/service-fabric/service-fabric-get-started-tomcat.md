---
title: Criar um contentor do Azure Service Fabric para o servidor Apache Tomcat no Linux | Documentos da Microsoft
description: Crie o contentor do Linux para expor um aplicativo em execução no servidor Apache Tomcat no Azure Service Fabric. Crie uma imagem de Docker com a sua aplicação e o servidor Apache Tomcat, envie a imagem para um registo de contentor, crie e implemente uma aplicação de contentor do Service Fabric.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/08/2018
ms.author: v-jamebr
ms.openlocfilehash: 3e93e822c5764a23bba124152ef5dfabf2d3f94f
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223874"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Criar o contentor do Service Fabric com o servidor Apache Tomcat no Linux
Apache Tomcat é uma implementação open source popular das tecnologias Java Servlet e o servidor de Java. Este artigo mostra-lhe como criar um contentor com o Apache Tomcat e uma aplicação Web simples, implementar o contentor para um cluster do Service Fabric em execução no Linux e ligar à aplicação Web.  

Para saber mais sobre o Apache Tomcat, veja a [home page do Apache Tomcat](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Pré-requisitos
* Um computador de programação com:
  * [SDK e ferramentas do Service Fabric](service-fabric-get-started-linux.md).
  * [Docker CE para Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [CLI do Service Fabric](service-fabric-cli.md)

* Um registo de contentor no Azure Container Registry. Pode criar um registo de contentor na sua subscrição do Azure com [do portal do Azure](../container-registry/container-registry-get-started-portal.md) ou [a CLI do Azure](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Crie uma imagem do Tomcat e executá-lo localmente
Siga os passos nesta secção para criar uma imagem do Docker com base numa imagem do Apache Tomcat e uma aplicação Web simples e, em seguida, executá-lo num contentor no seu sistema local. 
 
1. Clone o [introdução ao Java do Service Fabric](https://github.com/Azure-Samples/service-fabric-java-getting-started) repositório de exemplos no seu computador de desenvolvimento.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Altere os diretórios para o diretório de exemplo do servidor Apache Tomcat (*service-fabric-java-getting-started/container-apache-tomcat-web-server-sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Crie um ficheiro de Docker com base em oficial [imagem Tomcat](https://hub.docker.com/_/tomcat/) localizado no Docker Hub e o exemplo de servidor Tomcat. Na *service-fabric-java-getting-started/container-apache-tomcat-web-server-sample* diretório, crie um ficheiro denominado *Dockerfile* (com nenhuma extensão de ficheiro). Adicione o seguinte ao *Dockerfile* e guarde as alterações:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Consulte a [referência do Dockerfile](https://docs.docker.com/engine/reference/builder/) para obter mais informações.


4. Execute o `docker build` comando para criar a imagem que executa a sua aplicação web:

   ```bash
   docker build . -t tomcattest
   ```

   Este comando cria a nova imagem ao seguir as instruções no Dockerfile, atribuição de nomes (-t marcação) a imagem `tomcattest`. Para criar uma imagem de contentor, a imagem base é primeiro transferida do Hub do Docker e o aplicativo é adicionado a ele. 

   Quando o comando de criação concluir, execute o comando `docker images` para ver informações sobre a nova imagem:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Certifique-se de que a sua aplicação contentorizada é executada localmente antes de a enviar o registo de contentor:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` os nomes de contentor, para que possa consultá-la utilizando um nome amigável, em vez de sua ID.
   * `-p` Especifica o mapeamento de portas entre o contentor e o sistema operacional host. 

   > [!Note]
   > A porta é abrir com o `-p` parâmetro deve ser a porta de escuta de seu aplicativo Tomcat a pedidos. No exemplo atual, existe um conector configurado no *ApacheTomcat/conf/server.xml* ficheiro para escutar na porta 8080 para pedidos HTTP. Esta porta é mapeada para a porta 8080 no anfitrião. 

   Para saber mais sobre outros parâmetros, consulte a [documentação de execução do Docker](https://docs.docker.com/engine/reference/commandline/run/).

1. Para testar o seu contentor, abra um browser e introduza um dos seguintes URLs. Verá uma variante de "Hello World!" ecrã de boas-vindas para cada URL.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Olá mundo /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Parar o contentor e eliminá-lo a partir do seu computador de desenvolvimento:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Enviar a imagem do Tomcat para o registo de contentor
Agora que verificar que a imagem do Tomcat é executada num contentor no seu computador de desenvolvimento, envie-o para um repositório num registo de contentor. Este artigo utiliza o Azure Container Registry para armazenar a imagem, mas, com algumas modificações de passos, pode utilizar qualquer registo de contentor que escolher. Neste artigo, o nome do registo é considerado como *myregistry* e o nome do registo completo é myregistry.azurecr.io. Alterá-las adequadamente para o seu cenário. 

1. Execute `docker login` para iniciar sessão no registo de contentores com as suas [credenciais do registo](../container-registry/container-registry-authentication.md).

   O exemplo seguinte transmite o ID e a palavra-passe de um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md) do Azure Active Directory. Por exemplo, poderá ter atribuído um principal de serviço ao seu registo no âmbito de um cenário de automatização. Em alternativa, pode iniciar sessão com o nome de utilizador e a palavra-passe do registo.

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

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Criar e implementar a aplicação de contentor do Service Fabric
Agora que já enviou a imagem de Tomcat para um registo de contentor, pode criar e implementar uma aplicação de contentor do Service Fabric que extrai a imagem do Tomcat do seu registo e executa-o como um serviço em contentores no seu cluster. 

1. Criar um novo diretório fora do seu clone local (fora dos *service-fabric-java-getting-started* árvore de diretórios). Mude para o mesmo e utilizar o Yeoman para criar uma estrutura para uma aplicação de contentor: 

   ```bash
   yo azuresfcontainer 
   ```
   Introduza os valores seguintes quando lhe for pedido:

   * Nome à aplicação: ServiceFabricTomcat
   * Nome do serviço de aplicações: TomcatService
   * Introduza o nome da imagem: Forneça o URL da imagem de contentor no seu registo de contentor; Por exemplo, myregistry.azurecr.io/samples/tomcattest.
   * Comandos: Deixe em branco. Uma vez que esta imagem tem um ponto de entrada de carga de trabalho definido, não tem de especificar explicitamente comandos de entrada (os comandos são executados dentro do contentor, o que faz com que este continue em execução após o arranque).
   * Número de instâncias da aplicação de contentor de convidado: 1

   ![Gerador Yeoman do Service Fabric para contentores](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. No manifesto do serviço (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*), adicione o seguinte XML na raiz **ServiceManfest** tag para abrir a porta a aplicação está a escutar para pedidos. O **ponto final** marca declara o protocolo e a porta para o ponto final. Neste artigo, o serviço contentorizado escuta na porta 8080: 

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

11. No manifesto do aplicativo (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*), sob o **ServiceManifestImport** marcar, adicione o seguinte XML. Substitua a **AccountName** e **palavra-passe** no **RepositoryCredentials** etiqueta com o nome do seu registo de contentor e a palavra-passe necessária para iniciar sessão para o mesmo.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   O **ContainerHostPolicies** marca especifica políticas para activar anfitriões de contentor.
    
   * O **PortBinding** marca configura a política de mapeamento de portas porta do contentor-a-anfitrião. O **ContainerPort** atributo estiver definido para 8080, uma vez que o contentor expõe a porta 8080, conforme especificado no Dockerfile. O **EndpointRef** atributo está definido como "endpointTest", o ponto final definido no manifesto do serviço no passo anterior. Assim, os pedidos de entrada para o serviço na porta 8080 são mapeados para a porta 8080 no contentor. 
   * O **RepositoryCredentials** marca Especifica as credenciais que o contentor tem de autenticar com o repositório (privado) em que ele recebe a imagem a partir. Esta política não é necessário se a imagem será obtida a partir de um repositório público.
    

12. Na *ServiceFabricTomcat* pasta, ligue-se ao seu cluster do service fabric. 

   * Para ligar ao cluster do Service Fabric local, execute:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Para ligar a um cluster do Azure seguro, certifique-se de que o certificado de cliente está presente como um ficheiro. pem no *ServiceFabricTomcat* diretório e execute: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     No comando anterior, substitua `your-certificate.pem` com o nome do seu ficheiro de certificado de cliente. Em ambientes de desenvolvimento e teste, o certificado de cluster é frequentemente utilizado como o certificado de cliente. Se o certificado não é autoassinado, omita o `-no-verify` parâmetro. 
       
     Certificados de cluster são geralmente transferidos localmente como arquivos. pfx. Se ainda não tiver o certificado no formato PEM, pode executar o seguinte comando para criar um ficheiro. pem a partir de um ficheiro. pfx:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Se o ficheiro. pfx não estiver protegido de palavra-passe, utilize `-passin pass:` para o último parâmetro.


13. Execute o script de instalação fornecido no modelo para implementar a aplicação ao seu cluster. O script copia o pacote de aplicação para o armazenamento de imagens do cluster, registra o tipo de aplicativo e cria uma instância da aplicação.

     ```bash
     ./install.sh
     ```

   Depois de executar o script de instalação, abra um browser e navegue para o Service Fabric Explorer:
    
   * Num local cluster, utilize `http://localhost:19080/Explorer` (substitua *localhost* pelo IP privado da VM se estiver a utilizar Vagrant em Mac OS X).
   * Num cluster do Azure seguro, utilize `https://PublicIPorFQDN:19080/Explorer`. 
    
   Expanda a **aplicativos** nó e tenha em atenção que agora existe uma entrada para o tipo de aplicação **ServiceFabricTomcatType**e outro para a primeira instância desse tipo. Poderá demorar alguns minutos para a aplicação para a implantação completa, portanto, seja paciente.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Para aceder à aplicação no servidor do Tomcat, abra uma janela do browser e introduza qualquer um dos seguintes URLs. Se tiver implementado no cluster local, utilize *localhost* para *PublicIPorFQDN*. Verá uma variante de "Hello World!" ecrã de boas-vindas para cada URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Limpeza
Utilize o script de desinstalação fornecido no modelo para eliminar a instância da aplicação do seu cluster e anular o registo do tipo de aplicação.

```bash
./uninstall.sh
```

Depois de enviar a imagem para o registo de contentor, pode eliminar a imagem local do seu computador de programação:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter passos rápidos sobre os recursos de contentor do Linux adicionais, leia [criar a sua primeira aplicação de contentor do Service Fabric no Linux](service-fabric-get-started-containers-linux.md).
* Para obter mais passos em contentores de Linux, leia os [criar um tutorial de aplicação de contentor do Linux](service-fabric-tutorial-create-container-images.md) tutorial.
* Saiba mais sobre como executar [contentores no Service Fabric](service-fabric-containers-overview.md).


