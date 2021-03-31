---
title: 'Quickstart: Crie uma aplicação boot de mola no tecido de serviço Azure'
description: Neste início rápido, vai implementar uma aplicação Spring Boot para o Azure Service Fabric, utilizando um exemplo de aplicação Spring Boot.
ms.topic: quickstart
ms.date: 01/29/2019
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 84ce5920af95113801f468e3149421f3b9bd8901
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91530005"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>Quickstart: Implementar uma aplicação Java Spring Boot no Tecido de Serviço Azure

Neste arranque rápido, você implementa uma aplicação Java Spring Boot para O Tecido de Serviço Azure usando ferramentas familiares de linha de comando em Linux ou MacOS. O Azure Service Fabric é uma plataforma de sistemas distribuídos par implementar e gerir microsserviços e contentores. 

## <a name="prerequisites"></a>Pré-requisitos

#### <a name="linux"></a>[Linux](#tab/linux)

- [Ambiente java](./service-fabric-get-started-linux.md#set-up-java-development) e [Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Interface de linha de comando de tecido de serviço de tecido & de serviço de serviço (CLI)](./service-fabric-get-started-linux.md#installation-methods)
- [Git](https://git-scm.com/downloads)

#### <a name="macos"></a>[MacOS](#tab/macos)

- [Ambiente java e Yeoman](./service-fabric-get-started-mac.md#create-your-application-on-your-mac-by-using-yeoman)
- [Interface de linha de comando de tecido de serviço de tecido & de serviço de serviço (CLI)](./service-fabric-cli.md#cli-mac)
- [Git](https://git-scm.com/downloads)

--- 

## <a name="download-the-sample"></a>Transferir o exemplo

Numa janela terminal, executar o seguinte comando para clonar a aplicação de amostra de Mola [Boot Getting Started](https://github.com/spring-guides/gs-spring-boot) para a sua máquina local.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Criar a aplicação Spring Boot 
Dentro do *gs-spring-boot/diretório completo,* executar o comando abaixo para construir a aplicação 

```bash
./gradlew build
``` 

## <a name="package-the-spring-boot-application"></a>Empacotar a aplicação Spring Boot 
1. Dentro do *diretório gs-spring-boot* no seu clone, executar o `yo azuresfguest` comando. 

1. Introduza os detalhes seguintes para cada pedido.

    ![Entradas de Yeoman de Bota de primavera](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. Na pasta *SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code,* crie um ficheiro chamado *entryPoint.sh*. Adicione o seguinte código ao ficheiro *entryPoint.sh.* 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar *spring-boot*.jar
    ```

1. Adicione o recurso **Endpoints** no ficheiro *gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml*

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    O *ServiceManifest.xml* terá agora este aspeto: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

Nesta fase, já criou uma aplicação de Service Fabric para o exemplo da Introdução ao Spring Boot que pode implementar no Service Fabric.

## <a name="run-the-application-locally"></a>Executar a aplicação localmente

1. Inicie o seu cluster local em máquinas do Ubuntu, com o seguinte comando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Se estiver a utilizar um Mac, inicie o cluster local da imagem do Docker (isto é, partindo do princípio de que seguiu os [pré-requisitos](./service-fabric-get-started-mac.md#create-a-local-container-and-set-up-service-fabric) para configurar o seu cluster local para Mac). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    O arranque do cluster local demora algum tempo. Para confirmar que o cluster está totalmente operacional, aceda ao Service Fabric Explorer em `http://localhost:19080`. Os cinco nós em bom estado indicam que o cluster local está a funcionar. 
    
    ![Service Fabric Explorer mostra nómada saudável](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. Abra a pasta *gs-spring-boot/SpringServiceFabric.*
1. Execute o seguinte comando para ligar ao cluster local.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Executar o *install.sh* guião.

    ```bash
    ./install.sh
    ```

1. Abra o browser favorito e aceda à aplicação em `http://localhost:8080`.

    ![Amostra de tecido de serviço de botas de mola](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

Agora pode aceder à aplicação de Spring Boot que foi implementada num cluster do Service Fabric.

Para mais informações, consulte a amostra de Mola [Boot Getting Started](https://spring.io/guides/gs/spring-boot/) no site da primavera.

## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicações e serviços num cluster

Os serviços podem ser facilmente dimensionados num cluster para se prepararem para alterações à carga nos serviços. Para dimensionar um serviço, tem de alterar o número de instâncias em execução no cluster. Existem várias formas de dimensionar os seus serviços, por exemplo, pode utilizar scripts ou comandos da CLI do Service Fabric (sfctl). Nos passos seguintes, é utilizado o Service Fabric Explorer.

O Service Fabric Explorer é executado em todos os clusters do Service Fabric e pode ser acedido num browser, navegando para a porta (19080) de gestão HTTP dos clusters; por exemplo `http://localhost:19080`.

Para dimensionar o serviço de front-end da Web, faça o seguinte:

1. Abra o Service Fabric Explorer no seu cluster - por exemplo, `http://localhost:19080`.
1. Selecione a elipse **(...**) ao lado do **tecido:/SpringServiceFabric/SpringGettingStarted** no nó treeview e selecione **Scale Service**.

    ![Amostra de serviço de serviço de escala do Explorador de Tecido de Serviço](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    Agora, pode optar por dimensionar o número de instâncias do serviço.

1. Altere o número para **3** e selecione **o Serviço de Escala**.

    Segue-se uma maneira alternativa de dimensionar o serviço utilizando a linha de comandos.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Selecione o **nó de tecido:/SpringServiceFabric/SpringGettingStarted** no nó da árvore e expanda o nó de partição (representado por um GUID).

    ![Serviço de escala de explorador de tecido de serviço completo](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    O serviço tem três instâncias e a vista de árvore mostra em que nós as instâncias são executadas.

Através desta simples tarefa de gestão, duplicou os recursos disponíveis para o serviço de front-end processar a carga de utilizador. É importante compreender que não precisa de várias instâncias de um serviço para que o mesmo seja executado de forma fiável. Se um serviço falhar, o Service Fabric certifica-se de que uma nova instância do serviço é executada no cluster.

## <a name="fail-over-services-in-a-cluster"></a>Serviços de ativação pós-falha num cluster

Para demonstrar a ativação pós-falha do serviço, simula-se um reinício do nó utilizando o Service Fabric Explorer. Certifique-se de que apenas uma instância do seu serviço está em execução.

1. Abra o Service Fabric Explorer no seu cluster - por exemplo, `http://localhost:19080`.
1. Selecione a elipse **(...**) ao lado do nó que executa a instância do seu serviço e reinicie o nó.

    ![Nó de reinício do Explorador de Tecido de Serviço](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. A instância do seu serviço é movida para um nó diferente e a sua aplicação não tem períodos de indisponibilidade.

    ![Serviço Fabric Explorer reinicia o nó de sucesso](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

* Implementar uma Aplicação Spring Boot no Service Fabric
* Implementar a aplicação no seu cluster local
* Escalar horizontalmente a aplicação em vários nós
* Efetuar ativação pós-falha do seu serviço com nenhum resultado de disponibilidade

Para saber mais sobre como trabalhar com aplicações Java no Service Fabric, avance para o tutorial para aplicações Java.

> [!div class="nextstepaction"]
> [Implementar uma aplicação Java](./service-fabric-tutorial-create-java-app.md)
