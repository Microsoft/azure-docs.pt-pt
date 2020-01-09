---
title: Configurar um ambiente de desenvolvimento do Windows para Service Fabric malha
description: Configure o seu ambiente de desenvolvimento Windows para que possa criar uma aplicação do Service Fabric Mesh e implementá-la no Azure Service Fabric Mesh.
author: dkkapur
ms.author: dekapur
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: a674047722d4deca02d8f4d38a0826e479065037
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75496370"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Configurar o ambiente de desenvolvimento Windows para compilar aplicações do Service Fabric Mesh

Para compilar e executar aplicativos de malha de Service Fabric do Azure em seu computador de desenvolvimento do Windows, você precisará de:

* Docker
* Visual Studio 2017 ou posterior
* Tempo de execução de malha Service Fabric
* SDK de malha Service Fabric e ferramentas.

E uma das seguintes versões do Windows:

* Windows 10 (Enterprise, Professional ou Education) versões 1709 (atualização para criadores de outono) ou 1803 (atualização do Windows 10 de abril de 2018)
* Windows Server versão 1709
* Windows Server versão 1803

As instruções a seguir o ajudarão a obter tudo instalado com base na versão do Windows que você está executando.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

O Visual Studio 2017 ou posterior é necessário para implantar Service Fabric aplicativos de malha. [Instale a versão 15.6.0][download-visual-studio] ou superior e habilite as seguintes cargas de trabalho:

* Desenvolvimento ASP.NET e Web
* Desenvolvimento do Azure

## <a name="install-docker"></a>Instalar o Docker

Se você já tiver o Docker instalado, verifique se você tem a versão mais recente. O Docker pode solicitar a você quando uma nova versão estiver fora, mas verificar manualmente para garantir que você tenha a versão mais recente.

#### <a name="install-docker-on-windows-10"></a>Instalar o Docker no Windows 10

Baixe e instale a versão mais recente da [Community Edition para Windows][download-docker] para dar suporte aos aplicativos Service Fabric em contêineres usados pela malha Service Fabric.

Durante a instalação, selecione **Utilizar contentores do Windows em vez de contentores do Linux** quando lhe for pedido.

Se o Hyper-V não estiver habilitado no seu computador, o instalador do Docker oferecerá para habilitá-lo. Clique em **OK** para fazer isso, se lhe for pedido.

#### <a name="install-docker-on-windows-server-2016"></a>Instalar o Docker no Windows Server 2016

Se não tiver a função do Hyper-V ativada, abra o PowerShell como administrador e execute o seguinte comando para ativar o Hyper-V e, em seguida, reinicie o computador. Para obter mais informações, consulte [Docker Enterprise Edition para Windows Server][download-docker-server].

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

1. Instale o [SDK do Service Fabric mesh][download-sdkmesh] usando Web Platform Installer. Também será instalado o SDK e o runtime do Microsoft Azure Service Fabric.
2. Instale a [extensão das ferramentas de malha de Service Fabric do Visual Studio (versão prévia)][download-tools] do Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Compilar um cluster

> [!IMPORTANT]
> O Docker **tem** estar em execução antes de poder compilar um cluster.
> Teste se o Docker está em execução ao abrir uma janela de terminal e ao executar `docker ps` para ver se ocorre um erro. Se a resposta não indicar um erro, o Docker está a ser executado e está pronto para compilar um cluster.

> [!Note]
> Se estiver desenvolvendo no computador Windows Outono Creators Update (versão 1709), você só poderá usar imagens do Docker da versão 1709 do Windows.
> Se estiver desenvolvendo na máquina de atualização do Windows 10 de abril de 2018 (versão 1803), você poderá usar as imagens do Docker da versão 1709 ou 1803 do Windows.

Se você estiver usando o Visual Studio, poderá ignorar esta seção porque o Visual Studio criará um cluster local para você, se você não tiver um.

Para obter o melhor desempenho de depuração quando você estiver criando e executando um único aplicativo de Service Fabric de cada vez, crie um cluster de desenvolvimento local de nó único. Se você estiver executando vários aplicativos por vez, crie um cluster de desenvolvimento local de cinco nós. O cluster deve estar em execução sempre que você implantar ou depurar um projeto de malha Service Fabric.

Depois de instalar o tempo de execução, os SDKs, as ferramentas do Visual Studio, o Docker e o Docker em execução, crie um cluster de desenvolvimento.

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
5. Depois que a ferramenta Gerenciador de cluster de serviço estiver em execução (ela aparece na bandeja do sistema), clique com o botão direito do mouse nela e clique em **Iniciar cluster local**.

![Figura 1-iniciar o cluster local](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Está agora pronto para criar aplicações do Service Fabric Mesh!

## <a name="next-steps"></a>Passos seguintes

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