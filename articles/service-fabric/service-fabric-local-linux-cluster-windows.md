---
title: Configurar o cluster do Linux do Azure Service Fabric no Windows | Documentos da Microsoft
description: Este artigo aborda como configurar clusters do Service Fabric do Linux em execução em computadores de desenvolvimento do Windows. Isso é particularmente útil para cruzada de desenvolvimento da plataforma.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: e700250a6ebcdb82f99c1b460a510811d7ceb96c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819740"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Configurar um cluster do Service Fabric do Linux no seu computador de desenvolvedor do Windows

Este documento aborda como configurar um local Service Fabric do Linux em máquinas de desenvolvimento do Windows. Como configurar um cluster do Linux local é útil para testar rapidamente aplicações direcionadas para os clusters do Linux, mas são desenvolvidas num computador Windows.

## <a name="prerequisites"></a>Pré-requisitos
Clusters do Service Fabric baseados em Linux não são executados nativamente no Windows. Para executar um cluster do Service Fabric local, é fornecida uma imagem de contentor do Docker pré-configurada. Antes de começar, vai precisar do:

* De, pelo menos, 4 GB de RAM
* Da versão mais recente do [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker tem de ser executado no modo de Linux

>[!TIP]
> * Pode seguir os passos mencionados na oficial do Docker [documentação](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) para instalar o Docker no seu Windows. 
> * Depois de concluída a instalação, execute os passos mencionados [aqui](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine) para confirmar que foi instalada corretamente.


## <a name="create-a-local-container-and-setup-service-fabric"></a>Criar um contentor local e configurar o Service Fabric
Para configurar um contentor de Docker local e executar um cluster do service fabric em execução no mesmo, execute os seguintes passos no PowerShell:


1. Atualize a configuração do daemon do Docker no seu anfitrião com o seguinte e reinicie-o: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    A forma recomendada de atualizar é ir a ícone do Docker > Definições > Daemon > Avançadas e atualizar aí. Em seguida, reinicie o daemon do Docker para que as alterações entrem em vigor. 

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

6. Quando o passo 5 estiver devidamente concluído, pode ir para ``http://localhost:19080`` desde o Windows e seria capaz de ver o Explorador do Service Fabric. Neste momento, pode ligar a este cluster utilizar quaisquer ferramentas a partir do seu computador de desenvolvedor do Windows e implementar a aplicação direcionada para os clusters do Service Fabric do Linux. 

    > [!NOTE]
    > O plug-in do Eclipse não é atualmente suportado no Windows. 

7. Quando tiver terminado, parar e limpar o contentor com este comando:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limitações Conhecidas 
 
 Seguem-se as limitações conhecidas do cluster local em execução num contentor para Mac: 
 
 * O serviço DNS não é executado e não é suportado [Problema #132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Passos Seguintes
* Introdução ao [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Veja outros [exemplos de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
