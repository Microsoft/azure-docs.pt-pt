---
title: Criar um ambiente de desenvolvimento do Windows
description: Instale o runtime, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir esta configuração, estará pronto para criar aplicações no Windows.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: sfrev, devx-track-azurepowershell
ms.openlocfilehash: 08abd9ff1134415e47923f22e753691225fc23b8
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164725"
---
# <a name="prepare-your-development-environment-on-windows"></a>Preparar o ambiente de desenvolvimento no Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)
>
>

Para compilar e executar [aplicações do Azure Service Fabric][1] no computador de desenvolvimento Windows, tem de instalar o runtime do Service Fabric, o SDK e as ferramentas. Também precisa de [ativar a execução dos scripts do Windows PowerShell](#enable-powershell-script-execution) incluídos no SDK.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="supported-operating-system-versions"></a>Versões de sistema operativo com suporte

As seguintes versões do sistema operativo são suportadas para desenvolvimento:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Suporte do Windows 7:
> - O Windows 7 inclui, por predefinição, apenas o Windows PowerShell 2.0. Os cmdlets do PowerShell do Service Fabric necessitam do PowerShell 3.0 ou superior. Pode [baixar o Windows PowerShell 5.1][powershell5-download] a partir do Microsoft Download Center.
> - O Proxy Inverso do Service Fabric não está disponível no Windows 7.

## <a name="install-the-sdk-and-tools"></a>Instalar o SDK e as ferramentas

O Instalador de Plataforma Web (WebPI) é a forma recomendada de instalar o SDK e as ferramentas. Se receber erros de tempo de execução utilizando o WebPI, também pode encontrar ligações diretas com os instaladores nas notas de lançamento para uma versão específica do Tecido de Serviço. As notas de lançamento podem ser encontradas nos vários anúncios de lançamento no blog da equipa de [Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

> [!NOTE]
> As atualizações do cluster de desenvolvimento do tecido de serviço local não são suportadas.

### <a name="to-use-visual-studio-2017-or-2019"></a>Utilizar o Visual Studio 2017 ou 2019

As Ferramentas de Tecido de Serviço fazem parte da carga de trabalho do Azure Development no Visual Studio 2017 e 2019. Ative esta carga de trabalho como parte da instalação do Visual Studio.
Para além disso, deve instalar o runtime e o Microsoft Azure Service Fabric SDK, utilizando o Instalador de Plataforma Web.

* [Instalar o SDK do Microsoft Azure Service Fabric][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Utilizar Visual Studio 2015 (requer a Atualização 2 do Visual Studio 2015 ou posterior)

Para o Visual Studio 2015, as ferramentas do Service Fabric são instaladas juntamente com o SDK e o runtime, utilizando o Instalador de Plataforma Web:

* [Instale o Microsoft Azure Service Fabric SDK e ferramentas][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Apenas instalação do SDK

Se apenas precisa do SDK, pode instalar este pacote:

* [Instalar o SDK do Microsoft Azure Service Fabric][core-sdk]

As versões atuais são:

* Serviço Fabric SDK e ferramentas 4.1.458
* Tempo de funcionação do tecido de serviço 7.1.458

Para obter uma lista de versões suportadas, consulte as [versões Service Fabric](service-fabric-versions.md)

> [!NOTE]
> Os agrupamentos de máquinas individuais (OneBox) não são suportados para atualizações de aplicações ou clusters; eliminar o cluster OneBox e recriá-lo se precisar de realizar uma atualização do Cluster, ou tiver quaisquer problemas a realizar uma atualização da Aplicação. 

## <a name="enable-powershell-script-execution"></a>Ativar a execução do script do PowerShell

O Service Fabric utiliza scripts do Windows PowerShell para criar um cluster de desenvolvimento local e para implementação de aplicações do Visual Studio. Por predefinição, o Windows bloqueará a execução destes scripts. Para ativá-los, tem de modificar a política de execução do PowerShell. Abra o PowerShell como administrador e introduza o seguinte comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="install-docker-optional"></a>Instalar Docker (opcional)

[Service Fabric é um orquestrador de contentores](service-fabric-containers-overview.md) para implantar microserviços através de um conjunto de máquinas. Para executar aplicações de contentores do Windows no seu cluster de desenvolvimento local, tem primeiro de instalar o Docker para o Windows. Obtenha [Docker CE para windows (estável)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Depois de instalar e iniciar o Docker, clique com o botão direito do rato no ícone de tabuleiro e selecione **Mudar para os contentores do Windows**. Este passo é necessário para executar imagens do Docker baseadas no Windows.

## <a name="next-steps"></a>Passos seguintes

Agora que o ambiente de desenvolvimento está configurado, pode começar a criar e executar aplicações.

* [Saiba como criar, implementar e gerir aplicações](service-fabric-tutorial-create-dotnet-app.md)
* [Saiba mais sobre os modelos de programação: Reliable Services e Reliable Actors](service-fabric-choose-framework.md)
* [Veja exemplos de códigos do Service Fabric em GitHub](https://aka.ms/servicefabricsamples)
* [Visualizar o cluster através do Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Prepare a Linux development environment on Windows](service-fabric-local-linux-cluster-windows.md) (Preparar um ambiente de desenvolvimento do Linux no Windows)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Página da campanha do Service Fabric"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Ligação VS 2015 WebPI"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Ligação Dev15 WebPI"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Ligação Core SDK WebPI"
[powershell5-download]:https://www.microsoft.com/download/details.aspx?id=54616
