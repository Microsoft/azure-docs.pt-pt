---
title: Configurar um ambiente de v windows para malha de tecido de serviço
description: Configure o seu ambiente de desenvolvimento Windows para que possa criar uma aplicação do Service Fabric Mesh e implementá-la no Azure Service Fabric Mesh.
author: dkkapur
ms.author: dekapur
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: a674047722d4deca02d8f4d38a0826e479065037
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259204"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Configurar o ambiente de desenvolvimento Windows para compilar aplicações do Service Fabric Mesh

Para construir e executar aplicações de malha de tecido de serviço Azure na sua máquina de desenvolvimento Windows, você precisará:

* Docker
* Estúdio Visual 2017 ou mais tarde
* Tempo de execução da malha de tecido de serviço
* Malha de tecido de serviço SDK, e ferramentas.

E uma das seguintes versões do Windows:

* As versões 1709 (Atualização dos Criadores de outono) ou 1803 (Atualização do Windows 10 de abril de 2018)
* Versão 1709 do Windows Server
* Versão do Windows Server 1803

As seguintes instruções irão ajudá-lo a instalar tudo com base na versão do Windows que está a executar.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

O Visual Studio 2017 ou mais tarde é obrigado a implementar aplicações de malha de tecido de serviço. [Instale a versão 15.6.0][download-visual-studio] ou superior e ative as seguintes cargas de trabalho:

* Desenvolvimento ASP.NET e Web
* Desenvolvimento do Azure

## <a name="install-docker"></a>Instalar o Docker

Se já tem o Docker instalado, certifique-se de que tem a versão mais recente. O Docker pode pedir-lhe quando uma nova versão estiver fora, mas verifique manualmente para se certificar de que tem a versão mais recente.

#### <a name="install-docker-on-windows-10"></a>Instale docker no Windows 10

Descarregue e instale a versão mais recente da [Docker Community Edition para windows][download-docker] para suportar as aplicações de Tecido de Serviço contentorizadas utilizadas pela Malha de Tecido de Serviço.

Durante a instalação, selecione **Utilizar contentores do Windows em vez de contentores do Linux** quando lhe for pedido.

Se o Hyper-V não estiver ativado na sua máquina, o instalador do Docker oferecer-se-á para o ativar. Clique em **OK** para fazer isso, se lhe for pedido.

#### <a name="install-docker-on-windows-server-2016"></a>Instale o Docker no Windows Server 2016

Se não tiver a função do Hyper-V ativada, abra o PowerShell como administrador e execute o seguinte comando para ativar o Hyper-V e, em seguida, reinicie o computador. Para mais informações, consulte [docker Enterprise Edition para Windows Server][download-docker-server].

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

1. Instale o [SDK][download-sdkmesh] de malha de tecido de serviço utilizando o Instalador da Plataforma Web. Também será instalado o SDK e o runtime do Microsoft Azure Service Fabric.
2. Instale a extensão de malha de tecido de tecido de [estúdio visual (pré-visualização)][download-tools] do Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Compilar um cluster

> [!IMPORTANT]
> O Docker **tem** estar em execução antes de poder compilar um cluster.
> Teste se o Docker está em execução ao abrir uma janela de terminal e ao executar `docker ps` para ver se ocorre um erro. Se a resposta não indicar um erro, o Docker está a ser executado e está pronto para compilar um cluster.

> [!Note]
> Se estiver a desenvolver a atualização do Windows Fall Creators (versão 1709), só pode utilizar imagens de docker da versão 1709 do Windows.
> Se estiver a desenvolver a atualização do Windows 10 de abril de 2018 (versão 1803), pode utilizar imagens de Docker na versão 1709 ou 1803 do Windows.

Se estiver a usar o Visual Studio, pode saltar esta secção porque o Visual Studio criará um cluster local para si se não tiver um.

Para o melhor desempenho de depuração quando estiver a criar e executar uma única aplicação de Service Fabric de cada vez, crie um cluster de desenvolvimento local de um nó único. Se você estiver executando várias aplicações de cada vez, crie um cluster de desenvolvimento local de cinco nós. O cluster deve estar em funcionamento sempre que implemente ou depura um projeto de malha de tecido de serviço.

Depois de instalar o tempo de funcionamento, SDKs, ferramentas do Estúdio Visual, Docker, e ter o Docker a funcionar, criar um cluster de desenvolvimento.

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
5. Assim que a ferramenta gestora de cluster sinuosa estiver em execução (aparece na bandeja do sistema), clique à direita e clique em **Iniciar cluster local**.

![Figura 1 - Iniciar o cluster local](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Está agora pronto para criar aplicações do Service Fabric Mesh!

## <a name="next-steps"></a>Passos Seguintes

Leia o tutorial [Criar uma aplicação do Azure Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md).

Encontre respostas a [perguntas comuns e questões conhecidas.](service-fabric-mesh-faq.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/