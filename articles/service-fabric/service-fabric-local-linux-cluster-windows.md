---
title: Configurar o cluster do Azure Service Fabric Linux no Windows
description: Este artigo aborda como configurar Service Fabric clusters do Linux em execução em computadores de desenvolvimento do Windows. Isso é particularmente útil para o desenvolvimento de plataforma cruzada.
author: suhuruli
ms.topic: conceptual
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 806e77a928d25e30aed24147525f74507bc32795
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462987"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Configurar um cluster de Service Fabric do Linux no computador do desenvolvedor do Windows

Este documento aborda como configurar um Service Fabric Linux local em computadores de desenvolvimento do Windows. A configuração de um cluster local do Linux é útil para testar rapidamente os aplicativos direcionados a clusters do Linux, mas são desenvolvidos em um computador Windows.

## <a name="prerequisites"></a>Pré-requisitos
Os clusters Service Fabric baseados em Linux não são executados nativamente no Windows. Para executar um cluster de Service Fabric local, uma imagem de contêiner pré-configurada do Docker é fornecida. Antes de começar, vai precisar do:

* De, pelo menos, 4 GB de RAM
* Da versão mais recente do [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* O Docker deve estar em execução no modo Linux

>[!TIP]
> * Você pode seguir as etapas mencionadas na [documentação](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) oficial do Docker para instalar o Docker no Windows. 
> * Depois de concluída a instalação, execute os passos mencionados [aqui](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine) para confirmar que foi instalada corretamente.


## <a name="create-a-local-container-and-setup-service-fabric"></a>Criar um contentor local e configurar o Service Fabric
Para configurar um contêiner do Docker local e ter um cluster do Service Fabric em execução, execute as seguintes etapas no PowerShell:


1. Atualize a configuração do daemon do Docker no seu anfitrião com o seguinte e reinicie-o: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    A maneira recomendável de atualizar é-ir para o ícone do Docker > Configurações > daemon > avançado e atualizá-lo lá. Em seguida, reinicie o daemon do Docker para que as alterações entrem em vigor. 

2. Num novo diretório, crie um ficheiro com o nome `Dockerfile` para criar a Imagem do Service Fabric:

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >Pode adaptar este ficheiro para adicionar mais programas ou dependências ao contentor.
    >Por exemplo, se adicionar `RUN apt-get install nodejs -y` irá permitir o suporte para aplicações `nodejs` como convidado executáveis.
    
    >[!TIP]
    > Por predefinição, isto irá extrair a imagem com a versão mais recente do Service Fabric. Para revisões específicas, visite a página do [Hub do Docker](https://hub.docker.com/r/microsoft/service-fabric-onebox/)

3. Para criar a imagem reutilizável a partir do `Dockerfile`, abra um terminal e `cd` para o diretório que contém o `Dockerfile` e, em seguida, execute:

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Esta operação irá demorar algum tempo, mas só é necessária uma vez.

4. Agora, pode iniciar rapidamente uma cópia local do Service Fabric, sempre que necessário, ao executar:

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Forneça um nome para a instância do contentor para poder processá-la de forma mais legível. 
    >
    >Se a aplicação estiver a escutar em determinadas portas, as portas têm de ser especificada através da utilização de etiquetas `-p` adicionais. Por exemplo, se a sua aplicação estiver em escuta na porta 8080, adicione a seguinte etiqueta `-p`:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. O cluster irá demorar pouco tempo a iniciar. Pode ver registos com o comando seguinte ou avançar para o dashboard para ver o estado de funcionamento dos clusters [http://localhost:19080](http://localhost:19080):

    ```powershell 
    docker logs sftestcluster
    ```

6. Depois que a etapa 5 for concluída com êxito, você poderá ir para ``http://localhost:19080`` de seu Windows e poderá ver o Service Fabric Explorer. Neste ponto, você pode se conectar a esse cluster usando qualquer ferramenta do seu computador de desenvolvedor do Windows e implantar o aplicativo destinado a clusters do Linux Service Fabric. 

    > [!NOTE]
    > O plug-in do Eclipse não é atualmente suportado no Windows. 

7. Quando terminar, pare e limpe o contêiner com este comando:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limitações Conhecidas 
 
 Seguem-se as limitações conhecidas do cluster local em execução num contentor para Mac: 
 
 * O serviço DNS não é executado e não é suportado [Problema #132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Passos seguintes
* Introdução ao [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Confira outros [exemplos de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
