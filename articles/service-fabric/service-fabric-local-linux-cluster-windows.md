---
title: Configurar o cluster Linux de Tecido de Serviço Azure no Windows
description: Este artigo abrange como configurar clusters de Service Fabric Linux em funcionamento em máquinas de desenvolvimento Windows. Esta abordagem é útil para o desenvolvimento de plataformas cruzadas.
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 7b25a84e76773baea9f17430df1b7ba13aa661aa
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087082"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Crie um cluster de tecido de serviço Linux na sua máquina de desenvolvedores Windows

Este documento abrange como configurar um cluster de tecido de serviço Linux local numa máquina de desenvolvimento Windows. A criação de um cluster Linux local é útil para testar rapidamente aplicações direcionadas para clusters Linux, mas são desenvolvidas numa máquina Windows.

## <a name="prerequisites"></a>Pré-requisitos
Os clusters de tecido de serviço baseados em Linux não funcionam no Windows, mas para permitir a prototipagem de plataformas cruzadas, fornecemos um recipiente de estiva de rede de aglomerado de caixas Linux Service Fabric, que pode ser implantado via Docker para windows.

Antes de começar, vai precisar do:

* De, pelo menos, 4 GB de RAM
* Versão mais recente do [Docker para Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* O Docker deve estar a funcionar no modo de contentores Linux.

>[!TIP]
> Para instalar o Docker na sua máquina Windows, siga os passos na [documentação](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)do Docker . Depois de instalar, [verifique a instalação](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-setup-service-fabric"></a>Criar um contentor local e configurar o Service Fabric
Para instalar um contentor local do Docker e ter um cluster de tecido de serviço a funcionar nele, executar os seguintes passos:


1. Atualize a configuração do daemon do Docker no seu anfitrião com o seguinte e reinicie-o: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    A forma aconselhável de atualizar é ir para: 

    * Definições de > de ícones de estivador > motor Docker
    * Adicione os novos campos listados acima
    * Apply & Restart - reinicie o daemon do Docker para que as alterações entrem em vigor.

2. Inicie o cluster via PowerShell.<br/>
    <b>Ubuntu 18.04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16.04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Por predefinição, isto irá extrair a imagem com a versão mais recente do Service Fabric. Para revisões específicas, consulte a página [Onebox do Tecido de Serviço](https://hub.docker.com/_/microsoft-service-fabric-onebox) no Docker Hub.



3. Opcional: Construa a sua imagem de Tecido de Serviço alargado.

    Num novo diretório, crie um ficheiro chamado `Dockerfile` para construir a sua imagem personalizada:

    >[!NOTE]
    >Pode adaptar a imagem acima com um Dockerfile para adicionar programas ou dependências adicionais no seu recipiente.
    >Por exemplo, se adicionar `RUN apt-get install nodejs -y` irá permitir o suporte para aplicações `nodejs` como convidado executáveis.
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > Por predefinição, isto irá extrair a imagem com a versão mais recente do Service Fabric. Para revisões específicas, visite a página do [Hub do Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

    Para construir a sua imagem reutilizável a partir `Dockerfile` do, abra um terminal e `cd` para a segura direta da sua `Dockerfile` execução:

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Esta operação irá demorar algum tempo, mas só é necessária uma vez.

    Agora pode iniciar rapidamente uma cópia local do Service Fabric sempre que precisar, executando:

    ```powershell 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Forneça um nome para a instância do contentor para poder processá-la de forma mais legível. 
    >
    >Se a aplicação estiver a escutar em determinadas portas, as portas têm de ser especificada através da utilização de etiquetas `-p` adicionais. Por exemplo, se a sua aplicação estiver em escuta na porta 8080, adicione a seguinte etiqueta `-p`:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >


4. O cluster irá demorar pouco tempo a iniciar. Pode ver registos com o comando seguinte ou avançar para o dashboard para ver o estado de funcionamento dos clusters `http://localhost:19080`:

    ```powershell 
    docker logs sftestcluster
    ```

5. Depois de o cluster ser implantado com sucesso, como observado no passo 4, pode ir a partir da sua máquina Windows para encontrar o painel de ``http://localhost:19080`` instrumentos do Explorador de Tecidos de Serviço. Neste momento, pode ligar-se a este cluster utilizando ferramentas da sua máquina de desenvolvedores Windows e implementar aplicações direcionadas para clusters de Tecido de Serviço Linux. 

    > [!NOTE]
    > O plug-in do Eclipse não é atualmente suportado no Windows. 

6. Quando terminar, pare e limpe o recipiente com este comando:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limitações Conhecidas 
 
 Seguem-se as limitações conhecidas do cluster local em execução num contentor para Mac: 
 
 * O serviço DNS não funciona e não está atualmente suportado dentro do contentor. [Emissão #132](https://github.com/Microsoft/service-fabric/issues/132)
 * A execução de aplicações baseadas em contentores requer a execução de SF num anfitrião Linux. As aplicações de contentores aninhados não são suportadas atualmente.

## <a name="next-steps"></a>Passos seguintes
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* Começa com o [Eclipse](./service-fabric-get-started-eclipse.md)
* Confira outras [amostras de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
