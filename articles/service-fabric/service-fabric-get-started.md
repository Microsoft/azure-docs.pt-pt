---
title: Configurar um ambiente de desenvolvimento Windows para microsserviços do Azure | Microsoft Docs
description: Instale o runtime, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir esta configuração, estará pronto para criar aplicações no Windows.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2018
ms.author: ryanwi
ms.openlocfilehash: 768b0288f6a6861315da809f05c1d74f655d320b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57891540"
---
# <a name="prepare-your-development-environment-on-windows"></a>Preparar o ambiente de desenvolvimento no Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
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
> - O Windows 7 inclui, por predefinição, apenas o Windows PowerShell 2.0. Os cmdlets do PowerShell do Service Fabric necessitam do PowerShell 3.0 ou superior. Pode [transferir o Windows PowerShell 5.0][powershell5-download] no Centro de Transferências da Microsoft.
> - O Proxy Inverso do Service Fabric não está disponível no Windows 7.
>

## <a name="install-the-sdk-and-tools"></a>Instalar o SDK e as ferramentas
Instalador de plataforma Web (WebPI) é a forma recomendada de instalar o SDK e ferramentas. Se receber erros de tempo de execução através de WebPI, também pode encontrar ligações diretas para os programas de instalação nas notas de versão para uma versão específica do Service Fabric. As notas de versão podem ser encontradas nos vários anúncios de lançamento no [blogue da equipa do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

> [!NOTE]
> Atualizações do cluster de desenvolvimento de Service Fabric locais não são suportadas.

### <a name="to-use-visual-studio-2017"></a>Para utilizar o Visual Studio 2017
As Ferramentas do Service Fabric fazem parte da carga de trabalho de Desenvolvimento do Azure no Visual Studio 2017. Ative esta carga de trabalho como parte da instalação do Visual Studio.
Para além disso, deve instalar o runtime e o Microsoft Azure Service Fabric SDK, utilizando o Instalador de Plataforma Web.

* [Instalar o SDK do Microsoft Azure Service Fabric][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Utilizar Visual Studio 2015 (requer a Atualização 2 do Visual Studio 2015 ou posterior)
Para o Visual Studio 2015, as ferramentas do Service Fabric são instaladas juntamente com o SDK e o runtime, utilizando o Instalador de Plataforma Web:

* [Instalar o SDK e as Ferramentas do Microsoft Azure Service Fabric][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Apenas instalação do SDK
Se apenas precisa do SDK, pode instalar este pacote:
* [Instalar o SDK do Microsoft Azure Service Fabric][core-sdk]

As versões atuais são:
* Service Fabric SDK e ferramentas 3.3.644
* Runtime do Service Fabric 6.4.644
* Ferramentas do Service Fabric para Visual Studio 2015 2.4.11116.1
* 15.9 de 2017 do Visual Studio inclui as ferramentas do Service Fabric para Visual Studio 2.4.11024.1 

Para obter uma lista de versões suportadas, consulte [versões de Service Fabric](service-fabric-versions.md)

> [!NOTE]
> Atualiza a única máquina clusters (OneBox) não são suportados para o aplicativo ou Cluster; eliminar o cluster OneBox e recrie-o se tiver de executar uma atualização de Cluster ou tem quaisquer problemas a efetuar uma atualização da aplicação. 

## <a name="enable-powershell-script-execution"></a>Ativar a execução do script do PowerShell
O Service Fabric utiliza scripts do Windows PowerShell para criar um cluster de desenvolvimento local e para implementação de aplicações do Visual Studio. Por predefinição, o Windows bloqueará a execução destes scripts. Para ativá-los, tem de modificar a política de execução do PowerShell. Abra o PowerShell como administrador e introduza o seguinte comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```
## <a name="install-docker-optional"></a>Instalar o Docker (opcional)
[O Service Fabric é um orquestrador de contentores](service-fabric-containers-overview.md) para implementar microsserviços em clusters de máquinas. Para executar aplicações de contentor do Windows no seu cluster de desenvolvimento local, primeiro tem de instalar o Docker para Windows. Obtenha [Docker CE para Windows (estável)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Depois de instalar e iniciar o Docker, clique com o botão direito do rato no ícone de tabuleiro e selecione **Mudar para os contentores do Windows**. Este passo é necessário para executar imagens do Docker baseadas no Windows.

## <a name="next-steps"></a>Passos Seguintes
Agora que o ambiente de desenvolvimento está configurado, pode começar a criar e executar aplicações.

* [Saiba como criar, implementar e gerir aplicações](service-fabric-tutorial-create-dotnet-app.md)
* [Saiba mais sobre os modelos de programação: Reliable Services e Reliable Actors](service-fabric-choose-framework.md)
* [Veja exemplos de códigos do Service Fabric em GitHub](https://aka.ms/servicefabricsamples)
* [Visualizar o cluster através do Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Página da campanha do Service Fabric"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Ligação VS 2015 WebPI"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Ligação Dev15 WebPI"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Ligação Core SDK WebPI"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
