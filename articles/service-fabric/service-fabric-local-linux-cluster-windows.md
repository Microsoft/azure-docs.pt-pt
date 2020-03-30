---
title: Configurar o cluster Linux de tecido de serviço Azure no Windows
description: Este artigo abrange como configurar clusters de tecido de serviço Linux em execução em máquinas de desenvolvimento windows. Isto é particularmente útil para o desenvolvimento de plataformas cruzadas.
author: suhuruli
ms.topic: conceptual
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 806e77a928d25e30aed24147525f74507bc32795
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462987"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Instale um cluster de tecido de serviço Linux na sua máquina de desenvolvimento do Windows

Este documento cobre como configurar um tecido de serviço Linux local em máquinas de desenvolvimento windows. A criação de um cluster Linux local é útil para testar rapidamente aplicações direcionadas para clusters Linux, mas são desenvolvidas numa máquina windows.

## <a name="prerequisites"></a>Pré-requisitos
Os clusters de tecido de serviço baseadoem em Linux não funcionam de forma nativa no Windows. Para executar um cluster de tecido de serviço local, é fornecida uma imagem de recipiente Docker pré-configurada. Antes de começar, vai precisar do:

* De, pelo menos, 4 GB de RAM
* Da versão mais recente do [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* O Docker deve estar a correr no modo Linux.

>[!TIP]
> * Pode seguir os passos mencionados na [documentação](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) oficial do Docker para instalar o Docker no seu Windows. 
> * Depois de concluída a instalação, execute os passos mencionados [aqui](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine) para confirmar que foi instalada corretamente.


## <a name="create-a-local-container-and-setup-service-fabric"></a>Criar um contentor local e configurar o Service Fabric
Para configurar um recipiente Docker local e ter um cluster de tecido de serviço em funcionamento sobre ele, execute os seguintes passos na PowerShell:


1. Atualize a configuração do daemon do Docker no seu anfitrião com o seguinte e reinicie-o: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    A forma aconselhada de atualizar é - vá a Docker Icon > Definições > Daemon > Advanced e atualize-o lá. Em seguida, reinicie o daemon docker para que as alterações entrem em vigor. 

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
    > Por predefinição, isto irá extrair a imagem com a versão mais recente do Service Fabric. Para revisões particulares, visite a página [do Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/)

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

5. O cluster demorará um curto período de tempo a iniciar, pode ver os registos utilizando [http://localhost:19080](http://localhost:19080)o seguinte comando ou saltar para o painel de instrumentos para ver a saúde dos clusters:

    ```powershell 
    docker logs sftestcluster
    ```

6. Depois do passo 5 ser concluído com ``http://localhost:19080`` sucesso, pode ir a partir do Windows e poderá ver o explorador de Tecido de Serviço. Neste ponto, pode ligar-se a este cluster utilizando quaisquer ferramentas da sua máquina de desenvolvimento windows e implementar aplicações direcionadas para clusters de Tecido de Serviço Linux. 

    > [!NOTE]
    > O plug-in do Eclipse não é atualmente suportado no Windows. 

7. Quando terminar, pare e limpe o recipiente com este comando:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limitações Conhecidas 
 
 Seguem-se as limitações conhecidas do cluster local em execução num contentor para Mac: 
 
 * O serviço DNS não é executado e não é suportado [Problema #132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Passos seguintes
* Começar com [eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Confira [outras amostras de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
