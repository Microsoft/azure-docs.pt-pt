---
title: Configurar um ambiente de desenvolvimento Windows para compilar aplicações do Service Fabric Mesh | Microsoft Docs
description: Configure o seu ambiente de desenvolvimento Windows para que possa criar uma aplicação do Service Fabric Mesh e implementá-la no Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: 70c32f5e54fa7e71c0884ceba48c84af782b3f41
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777198"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Configurar o ambiente de desenvolvimento Windows para compilar aplicações do Service Fabric Mesh

Para criar e executar aplicações de Mesh de recursos de infraestrutura de serviço do Azure no seu computador de desenvolvimento do Windows, terá de:

* Docker
* Visual Studio 2017
* Tempo de execução de malha do Service Fabric
* SDK do Service Fabric em malha e as ferramentas.

E uma das seguintes versões do Windows:

* Windows 10 (Enterprise, Professional ou Education) versões 1709 (Fall Creators update) ou versão 1803 (atualização Windows 10 de Abril de 2018)
* Versão 1709 do Windows Server
* Windows Server versão 1803

A seguinte irá instruções ajudará a se tudo instalado com base na versão do Windows estiver a executar.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

Precisa do Visual Studio 2017 para implementar aplicações do Service Fabric Mesh. [Instale a versão 15.6.0][download-visual-studio] ou superior e ative as seguintes cargas de trabalho:

* Desenvolvimento ASP.NET e Web
* Desenvolvimento do Azure

## <a name="install-docker"></a>Instalar Docker

Se já tiver o Docker instalado, certifique-se de que tem a versão mais recente. Docker pode solicitar-lhe quando uma nova versão já foi lançada, mas verifique manualmente para se certificar de que tem a versão mais recente.

#### <a name="install-docker-on-windows-10"></a>Instalar o Docker no Windows 10

Transfira e instale a versão mais recente do [Docker Community Edition para Windows][download-docker] para suportar as aplicações do Service Fabric em contentores utilizadas pelo Service Fabric Mesh.

Durante a instalação, selecione **Utilizar contentores do Windows em vez de contentores do Linux** quando lhe for pedido.

Se o Hyper-V não estiver ativado no seu computador, o instalador do Docker poderá ativá-lo. Clique em **OK** para fazer isso, se lhe for pedido.

#### <a name="install-docker-on-windows-server-2016"></a>Instalar o Docker no Windows Server 2016

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
> Se estiver a desenvolver o Windows Fall Creators update máquina (versão 1709), só pode utilizar imagens de docker de versão 1709 do Windows.
> Se estiver a desenvolver no Windows 10 de Abril de 2018 atualização máquina (versão 1803), pode usar qualquer uma das imagens do Windows versão 1709 ou versão 1803 docker.

Se estiver a utilizar o Visual Studio, pode ignorar esta secção porque o Visual Studio vai criar um cluster local, caso não tenha um.

Para o melhor desempenho de depuração quando estiver a criar e executar uma única aplicação de Service Fabric num momento, crie um cluster de nó único de desenvolvimento local. Se estiver a executar vários aplicativos ao mesmo tempo, crie um cluster de desenvolvimento local de cinco nós. Tem de executar o cluster sempre que implementa ou depurar um projeto de malha do Service Fabric.

Depois de instalar o tempo de execução, SDKs, ferramentas do Visual Studio, Docker e ter em execução do Docker, crie um cluster de desenvolvimento.

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
5. Assim que a ferramenta de Gestor de clusters do serviço está em execução (aparece na Bandeja do sistema), faça duplo clique nele e clique em **iniciar o Cluster Local**.

![Figura 1 - iniciar o cluster local](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Está agora pronto para criar aplicações do Service Fabric Mesh!

## <a name="next-steps"></a>Passos Seguintes

Leia o tutorial [Criar uma aplicação do Azure Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md).

Encontre respostas para [perguntas comuns e problemas conhecidos](service-fabric-mesh-faq.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/