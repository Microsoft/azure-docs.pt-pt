---
title: Configurar um ambiente dev Windows para malha de tecido de serviço
description: Configure o seu ambiente de desenvolvimento Windows para que possa criar uma aplicação do Service Fabric Mesh e implementá-la no Azure Service Fabric Mesh.
author: georgewallace
ms.author: gwallace
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: fc234f6954cf263423cc517bb3dda2ba2efa3358
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625740"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Configurar o ambiente de desenvolvimento Windows para compilar aplicações do Service Fabric Mesh

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

Para construir e executar aplicações de malha de tecido de serviço Azure na sua máquina de desenvolvimento Windows, você precisará de:

* Docker
* Estúdio Visual 2017 ou mais tarde
* Tempo de funcionação da malha de tecido de serviço
* Rede de malha de tecido de serviço SDK, e ferramentas.

E uma das seguintes versões do Windows:

* Versões 1709 (atualização de Criadores de outono) ou 1803 (atualização do Windows 10 abril 2018)
* Versão 1709 do Windows Server
* Versão 1803 do Windows Server

As seguintes instruções irão ajudá-lo a instalar tudo com base na versão do Windows que está a executar.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

O Visual Studio 2017 ou mais tarde é necessário para implementar aplicações de Malha de Tecido de Serviço. [Instale a versão 15.6.0][download-visual-studio] ou superior e ative as seguintes cargas de trabalho:

* Desenvolvimento ASP.NET e Web
* Desenvolvimento do Azure

## <a name="install-docker"></a>Instalar o Docker

Se já tiver o Docker instalado, certifique-se de que tem a versão mais recente. O Docker pode pedir-lhe quando uma nova versão estiver fora, mas verifique manualmente para garantir que tem a versão mais recente.

#### <a name="install-docker-on-windows-10"></a>Instalar Docker no Windows 10

Transfira e instale a versão mais recente do [Docker Community Edition para Windows][download-docker] para suportar as aplicações do Service Fabric em contentores utilizadas pelo Service Fabric Mesh.

Durante a instalação, selecione **Utilizar contentores do Windows em vez de contentores do Linux** quando lhe for pedido.

Se o Hiper-V não estiver ativado na sua máquina, o instalador do Docker oferecer-se-á para o ativar. Clique em **OK** para fazer isso, se lhe for pedido.

#### <a name="install-docker-on-windows-server-2016"></a>Instalar Docker no Windows Server 2016

Se não tiver a função do Hyper-V ativada, abra o PowerShell como administrador e execute o seguinte comando para ativar o Hyper-V e, em seguida, reinicie o computador. Para obter mais informações, veja [Docker Enterprise Edition para Windows Server][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Reinicie o computador.

Abra o PowerShell como administrador e execute os seguintes comandos para instalar o Docker:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK e ferramentas

Instale o runtime, o SDK e as ferramentas do Service Fabric Mesh pela ordem seguinte.

1. Instale o [SDK do Service Fabric Mesh][download-sdkmesh] através do Instalador de Plataforma Web. Também será instalado o SDK e o runtime do Microsoft Azure Service Fabric.
2. Instale a [extensão Ferramentas do Service Fabric Mesh para Visual Studio (pré-visualização)][download-tools] a partir do Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Compilar um cluster

> [!IMPORTANT]
> O Docker **tem** estar em execução antes de poder compilar um cluster.
> Teste se o Docker está em execução ao abrir uma janela de terminal e ao executar `docker ps` para ver se ocorre um erro. Se a resposta não indicar um erro, o Docker está a ser executado e está pronto para compilar um cluster.

> [!Note]
> Se estiver a desenvolver na máquina de atualização de Criadores de outono do Windows (versão 1709), só pode utilizar imagens de docker da versão 1709 do Windows.
> Se estiver a desenvolver na máquina de atualização do Windows 10 de abril de 2018 (versão 1803), pode utilizar imagens de estivadores do Windows 1709 ou 1803.

Se estiver a usar o Visual Studio, pode saltar esta secção porque o Visual Studio criará um cluster local para si se não tiver um.

Para obter o melhor desempenho de depuração quando estiver a criar e executar uma única aplicação de Tecido de Serviço de cada vez, crie um cluster de desenvolvimento local de nó único. Se estiver a executar várias aplicações de cada vez, crie um cluster de desenvolvimento local de cinco nós. O cluster deve estar em funcionamento sempre que implementar ou depurar um projeto de malha de tecido de serviço.

Depois de instalar o tempo de execução, SDKs, ferramentas de Estúdio Visual, Docker, e ter Docker em execução, criar um cluster de desenvolvimento.

1. Feche a janela do PowerShell.
2. Abra uma nova janela do PowerShell elevada como administrador. Este passo é necessário para carregar os módulos do Service Fabric instalados recentemente.
3. Execute o seguinte comando do PowerShell para criar um cluster de desenvolvimento:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. Para iniciar a ferramenta de gestor de clusters locais, execute o seguinte comando do PowerShell:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. Uma vez que a ferramenta de gestor de cluster de serviço está em funcionamento (aparece na bandeja do seu sistema), clique com o botão direito e clique no **Start Local Cluster**.

![Figura 1 - Inicie o cluster local](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Está agora pronto para criar aplicações do Service Fabric Mesh!

## <a name="next-steps"></a>Passos seguintes

Leia o tutorial [Criar uma aplicação do Azure Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md).

Encontre respostas a [perguntas comuns e questões conhecidas.](service-fabric-mesh-faq.md)

[azure-cli-install]: /cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/
