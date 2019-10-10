---
title: Criar um contêiner de Service Fabric do Azure para o servidor Apache Tomcat no Linux | Microsoft Docs
description: Crie um contêiner do Linux para expor um aplicativo em execução no servidor Apache Tomcat no Azure Service Fabric. Crie uma imagem do Docker com o seu aplicativo e o servidor Apache Tomcat, envie a imagem por push para um registro de contêiner, crie e implante um aplicativo de contêiner de Service Fabric.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 7e14a027f17c15c83a4ce25a211ef6106f2d2eaa
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170608"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Criar Service Fabric contêiner executando o servidor Apache Tomcat no Linux
O Apache Tomcat é uma implementação popular e de software livre do servlet Java e das tecnologias de servidor Java. Este artigo mostra como criar um contêiner com o Apache Tomcat e um aplicativo Web simples, implantar o contêiner em um Service Fabric cluster que executa o Linux e conectar-se ao aplicativo Web.  

Para saber mais sobre o Apache Tomcat, consulte a [Home Page do Apache Tomcat](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Pré-requisitos
* Um computador de programação com:
  * [SDK e ferramentas do Service Fabric](service-fabric-get-started-linux.md).
  * [Docker CE para Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [CLI do Service Fabric](service-fabric-cli.md)

* Um registro de contêiner no registro de contêiner do Azure. Você pode criar um registro de contêiner em sua assinatura do Azure usando [o portal do Azure](../container-registry/container-registry-get-started-portal.md) ou [o CLI do Azure](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Criar uma imagem Tomcat e executá-la localmente
Siga as etapas nesta seção para criar uma imagem do Docker com base em uma imagem do Apache Tomcat e em um aplicativo Web simples e, em seguida, executá-la em um contêiner no sistema local. 
 
1. Clone o [Service Fabric introdução](https://github.com/Azure-Samples/service-fabric-java-getting-started) ao repositório de exemplos Java no seu computador de desenvolvimento.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Altere os diretórios para o diretório de exemplo do servidor Apache Tomcat (*Service-Fabric-Java-Getting-Started/contêiner-Apache-Tomcat-Web-Server-Sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Crie um arquivo do Docker com base na [imagem](https://hub.docker.com/_/tomcat/) oficial do Tomcat localizada no Hub do Docker e no exemplo do servidor Tomcat. No diretório *Service-Fabric-Java-Getting-Started/contêiner-Apache-Tomcat-Web-Server-Sample* , crie um arquivo chamado *Dockerfile* (sem nenhuma extensão de arquivo). Adicione o seguinte ao *Dockerfile* e guarde as alterações:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Consulte a [referência de Dockerfile](https://docs.docker.com/engine/reference/builder/) para obter mais informações.


4. Execute o comando `docker build` para criar a imagem que executa o aplicativo Web:

   ```bash
   docker build . -t tomcattest
   ```

   Esse comando cria a nova imagem usando as instruções no Dockerfile, nomeando (marcação-t) a imagem `tomcattest`. Para criar uma imagem de contêiner, a imagem base é baixada primeiro do Hub do Docker e o aplicativo é adicionado a ela. 

   Quando o comando de criação concluir, execute o comando `docker images` para ver informações sobre a nova imagem:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Verifique se o aplicativo em contêineres é executado localmente antes de enviá-lo ao registro de contêiner:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` nomeia o contêiner, para que você possa consultá-lo usando um nome amigável em vez de sua ID.
   * `-p` especifica o mapeamento de porta entre o contêiner e o sistema operacional do host. 

   > [!Note]
   > A porta que você abre com o parâmetro `-p` deve ser a porta em que seu aplicativo Tomcat ouve solicitações. No exemplo atual, há um conector configurado no arquivo *ApacheTomcat/conf/Server. xml* para escutar na porta 8080 para solicitações HTTP. Essa porta é mapeada para a porta 8080 no host. 

   Para saber mais sobre outros parâmetros, consulte a [documentação de execução do Docker](https://docs.docker.com/engine/reference/commandline/run/).

1. Para testar o contêiner, abra um navegador e insira uma das URLs a seguir. Você verá uma variante do "Olá, Mundo!" tela de boas-vindas para cada URL.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Olá, mundo/sayHi](./media/service-fabric-get-started-tomcat/hello.png)

2. Pare o contêiner e exclua-o do seu computador de desenvolvimento:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Enviar por push a imagem do Tomcat para o registro de contêiner
Agora que você verificou que a imagem Tomcat é executada em um contêiner em seu computador de desenvolvimento, envie-a por push para um repositório em um registro de contêiner. Este artigo usa o registro de contêiner do Azure para armazenar a imagem, mas, com alguma modificação de etapas, você pode usar qualquer registro de contêiner que escolher. Neste artigo, o nome do registro é considerado *myregistry* e o nome completo do registro é myregistry.azurecr.IO. Altere-os adequadamente para seu cenário. 

1. Execute `docker login` para entrar no registro de contêiner com suas [credenciais de registro](../container-registry/container-registry-authentication.md).

   O exemplo seguinte transmite o ID e a palavra-passe de um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md) do Azure Active Directory. Por exemplo, poderá ter atribuído um principal de serviço ao seu registo no âmbito de um cenário de automatização. Ou, você pode entrar usando o nome de usuário e a senha do registro.

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

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Criar e implantar o aplicativo de contêiner de Service Fabric
Agora que você enviou a imagem do Tomcat para um registro de contêiner, você pode criar e implantar um aplicativo de contêiner de Service Fabric que extrai a imagem do Tomcat do registro e a executa como um serviço em contêineres no cluster. 

1. Crie um novo diretório fora do seu clone local (fora da árvore de diretórios *Service-Fabric-Java-Getting-Started* ). Alterne para ele e use Yeoman para criar um Scaffold para um aplicativo de contêiner: 

   ```bash
   yo azuresfcontainer 
   ```
   Insira os seguintes valores quando solicitado:

   * Nomeie seu aplicativo: ServiceFabricTomcat
   * Nome do serviço de aplicativo: TomcatService
   * Insira o nome da imagem: forneça a URL para a imagem de contêiner no registro de contêiner; por exemplo, myregistry.azurecr.io/samples/tomcattest.
   * Comandos: deixe em branco. Uma vez que esta imagem tem um ponto de entrada de carga de trabalho definido, não tem de especificar explicitamente comandos de entrada (os comandos são executados dentro do contentor, o que faz com que este continue em execução após o arranque).
   * Número de instâncias do aplicativo de contêiner de convidado: 1

   ![Gerador Yeoman do Service Fabric para contentores](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. No manifesto do serviço (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/Service manifest. xml*), adicione o XML a seguir na marca raiz **ServiceManfest** para abrir a porta em que seu aplicativo está ouvindo solicitações. A marca de **ponto de extremidade** declara o protocolo e a porta para o ponto de extremidade. Para este artigo, o serviço em contêineres escuta na porta 8080: 

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

11. No manifesto do aplicativo (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest. xml*), na marca **ServiceManifestImport** , adicione o XML a seguir. Substitua o **AccountName** e a **senha** na marca **RepositoryCredentials** pelo nome do seu registro de contêiner e a senha necessária para entrar nele.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   A marca **ContainerHostPolicies** especifica políticas para ativar hosts de contêiner.
    
   * A marca **portbinding** configura a política de mapeamento de porta de contêiner de porta para host. O atributo **ContainerPort** é definido como 8080 porque o contêiner expõe a porta 8080, conforme especificado no Dockerfile. O atributo **EndpointRef** é definido como "endpointTest", o ponto de extremidade definido no manifesto do serviço na etapa anterior. Assim, as solicitações de entrada para o serviço na porta 8080 são mapeadas para a porta 8080 no contêiner. 
   * A marca **RepositoryCredentials** especifica as credenciais que o contêiner precisa para autenticar com o repositório (privado) onde ele extrai a imagem. Você não precisará dessa política se a imagem for extraída de um repositório público.
    

12. Na pasta *ServiceFabricTomcat* , conecte-se ao cluster do Service Fabric. 

   * Para se conectar ao cluster de Service Fabric local, execute:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Para se conectar a um cluster seguro do Azure, verifique se o certificado do cliente está presente como um arquivo. PEM no diretório *ServiceFabricTomcat* e execute: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     No comando anterior, substitua `your-certificate.pem` pelo nome do seu arquivo de certificado de cliente. Em ambientes de desenvolvimento e teste, o certificado do cluster geralmente é usado como o certificado do cliente. Se o certificado não for auto-assinado, omita o parâmetro `-no-verify`. 
       
     Os certificados de cluster normalmente são baixados localmente como arquivos. pfx. Se você ainda não tiver seu certificado no formato PEM, poderá executar o comando a seguir para criar um arquivo. pem a partir de um arquivo. pfx:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Se o arquivo. pfx não for protegido por senha, use `-passin pass:` para o último parâmetro.


13. Execute o script de instalação fornecido no modelo para implantar o aplicativo em seu cluster. O script copia o pacote de aplicativos para o repositório de imagens do cluster, registra o tipo de aplicativo e cria uma instância do aplicativo.

     ```bash
     ./install.sh
     ```

   Depois de executar o script de instalação, abra um navegador e navegue até Service Fabric Explorer:
    
   * Em um cluster local, use `http://localhost:19080/Explorer` (substitua *localhost* pelo IP privado da VM se estiver usando Vagrant em Mac os X).
   * Em um cluster seguro do Azure, use `https://PublicIPorFQDN:19080/Explorer`. 
    
   Expanda o nó **aplicativos** e observe que agora há uma entrada para o tipo de aplicativo, **ServiceFabricTomcatType**e outro para a primeira instância desse tipo. Pode levar alguns minutos para que o aplicativo seja completamente implantado, portanto, seja paciente.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Para acessar o aplicativo no servidor Tomcat, abra uma janela do navegador e insira qualquer uma das URLs a seguir. Se você implantou o no cluster local, use *localhost* para *PublicIPorFQDN*. Você verá uma variante do "Olá, Mundo!" tela de boas-vindas para cada URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Limpeza
Use o script de desinstalação fornecido no modelo para excluir a instância do aplicativo do cluster e cancelar o registro do tipo de aplicativo.

```bash
./uninstall.sh
```

Depois de enviar a imagem para o registo de contentor, pode eliminar a imagem local do seu computador de programação:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Passos seguintes
* Para obter etapas rápidas sobre recursos adicionais de contêiner do Linux, leia [criar seu primeiro aplicativo de contêiner de Service Fabric no Linux](service-fabric-get-started-containers-linux.md).
* Para obter etapas mais detalhadas sobre contêineres do Linux, leia o tutorial [criar um aplicativo de contêiner do Linux](service-fabric-tutorial-create-container-images.md) .
* Saiba mais sobre como executar [contentores no Service Fabric](service-fabric-containers-overview.md).


