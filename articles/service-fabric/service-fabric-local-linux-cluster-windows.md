---
title: Configurar o cluster Linux de Tecido de Serviço Azure no Windows
description: Este artigo abrange como configurar clusters de Service Fabric Linux em funcionamento em máquinas de desenvolvimento Windows. Isto é particularmente útil para o desenvolvimento de plataformas cruzadas.
ms.topic: conceptual
ms.date: 11/20/2017
ms.openlocfilehash: 83d494d777a4a1e1586707c8848056ca8fe9780a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537077"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Crie um cluster de tecido de serviço Linux na sua máquina de desenvolvedores Windows

Este documento abrange como configurar um tecido de serviço Linux local em máquinas de desenvolvimento Windows. A criação de um cluster Linux local é útil para testar rapidamente aplicações direcionadas para clusters Linux, mas são desenvolvidas numa máquina Windows.

## <a name="prerequisites"></a>Pré-requisitos
Os clusters de tecido de serviço baseados em Linux não funcionam de forma nativa no Windows. Para executar um cluster de tecido de serviço local, é fornecida uma imagem pré-configurada do recipiente Docker. Antes de começar, vai precisar do:

* De, pelo menos, 4 GB de RAM
* Da versão mais recente do [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* O Docker deve estar a correr no modo Linux.

>[!TIP]
> * Pode seguir os passos mencionados na [documentação](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) oficial do Docker para instalar o Docker no seu Windows. 
> * Depois de concluída a instalação, execute os passos mencionados [aqui](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine) para confirmar que foi instalada corretamente.


## <a name="create-a-local-container-and-setup-service-fabric"></a>Criar um contentor local e configurar o Service Fabric
Para instalar um recipiente local do Docker e ter um conjunto de tecido de serviço a funcionar nele, execute os seguintes passos em PowerShell:


1. Atualize a configuração do daemon do Docker no seu anfitrião com o seguinte e reinicie-o: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    A forma aconselhada de atualizar é - vá a Docker Icon > Settings > Daemon > Advanced e atualize-a lá. Em seguida, reinicie o daemon do Docker para que as alterações entrem em vigor. 

2. Num novo diretório, crie um ficheiro com o nome `Dockerfile` para criar a Imagem do Service Fabric:

    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:latest
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
    > Por predefinição, isto irá extrair a imagem com a versão mais recente do Service Fabric. Para revisões específicas, visite a página [do Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/)

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
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest`
    >

5. O cluster irá demorar pouco tempo a iniciar. Pode ver registos com o comando seguinte ou avançar para o dashboard para ver o estado de funcionamento dos clusters `http://localhost:19080`:

    ```powershell 
    docker logs sftestcluster
    ```

6. Após o fim do passo 5 concluído com sucesso, pode ir para ``http://localhost:19080`` a partir do seu Windows e poderá ver o explorador de Tecido de Serviço. Neste momento, pode ligar-se a este cluster utilizando quaisquer ferramentas da sua máquina de desenvolvedores Windows e implementar uma aplicação direcionada para clusters de Tecido de Serviço Linux. 

    > [!NOTE]
    > O plug-in do Eclipse não é atualmente suportado no Windows. 

7. Quando terminar, pare e cleanup o recipiente com este comando:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limitações Conhecidas 
 
 Seguem-se as limitações conhecidas do cluster local em execução num contentor para Mac: 
 
 * O serviço DNS não é executado e não é suportado [Problema #132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Passos seguintes
* Começa com o [Eclipse](./service-fabric-get-started-eclipse.md)
* Confira outras [amostras de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
