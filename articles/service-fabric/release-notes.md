---
title: Versões de Service Fabric do Azure
description: Notas de versão para as mais recentes funcionalidades e melhorias no Service Fabric.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 78fb96cae3d3d128da608183f37771b2ecf66dcf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165364"
---
# <a name="service-fabric-releases"></a>Versões de Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guias de resolução de problemas</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">acompanhamento de questões</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">as opções de suporte</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">versões suportadas</a>  
|  <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Exemplos de código</a>

Este artigo fornece mais informações sobre as atualizações para o runtime do Service Fabric e os SDKs e versões mais recentes.

## <a name="whats-new-in-service-fabric"></a>**O que há de novo no Service Fabric**

### <a name="service-fabric-65"></a>O Service Fabric 6.5

A versão mais recente do Service Fabric inclui a capacidade de suporte, a fiabilidade e melhorias de desempenho, novas funcionalidades, correções de erros e melhorias para facilitar o gerenciamento de ciclo de vida de aplicações e clusters.

> [!IMPORTANT]
> Service Fabric 6.5 é a versão final com o Service Fabric, ferramentas de suporte no Visual Studio 2015. Os clientes são aconselhados a mudar para [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) daqui em diante.

Eis o que há de novo no Service Fabric 6.5:

- Service Fabric Explorer inclui uma [Visualizador de imagem do Store](service-fabric-visualizing-your-cluster.md#image-store-viewer) para inspecionar os aplicativos que carregou para o armazenamento de imagens.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) versão [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) inclui vários aprimoramentos self-diagnostic. Os clientes de POA são recomendados para mover para esta versão.

- [Serviço de EventStore está ativado por predefinição](service-fabric-visualizing-your-cluster.md#event-store) para clusters do Service Fabric 6.5, exceto se tiver optado por.

- Adicionado [eventos de ciclo de vida de réplica](service-fabric-diagnostics-event-generation-operational.md#replica-events) para serviços com estado.

- [Uma melhor visibilidade do Estado do nó seed](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), incluindo os avisos de nível de cluster, se um nó de semente é mau estado de funcionamento (*baixo*, *foi removido* ou *desconhecido*).

- [Ferramenta de recuperação de desastres do Service Fabric Application](https://github.com/Microsoft/Service-Fabric-AppDRTool) permite que os serviços com monitoração de estado do Service Fabric para a recuperação rápida quando o cluster principal encontra um desastre. Dados do cluster principal continuamente são sincronizados no aplicativo em modo de espera secundário utilizando a cópia de segurança periódica e restauro.

- Suporte do Visual Studio para [publicação de aplicações de .NET Core para clusters baseados em Linux](service-fabric-how-to-publish-linux-app-vs.md).

- [O Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) serão instalados automaticamente para o Service Fabric 6.5 (e versões posteriores) quando atualizar ou criar um novo cluster do Linux no Azure.

- [O SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) é instalado por padrão nos clusters do MacOS/Linux OneBox.

Para obter mais detalhes, consulte a [notas de versão do Service Fabric 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

## <a name="previous-versions"></a>Versões anteriores

### <a name="service-fabric-64-releases"></a>Versões de Service Fabric 6.4

| Data da versão | Libertar | Mais informações |
|---|---|---|
| 30 de Novembro de 2018 | [6.4 do Azure Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 de Dezembro de 2018 | [Clusters do Service Fabric 6.4 atualizar versão para o Windows Azure](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 de Fevereiro de 2019 | [Versão de atualização do Fabric 6.4 de serviço do Azure](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 de Março de 2019 | [Versão de atualização do Fabric 6.4 de serviço do Azure](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 de Abril de 2019 | [Versão de atualização do Fabric 6.4 de serviço do Azure](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 de Maio de 2019 | [Versão de atualização do Fabric 6.4 de serviço do Azure](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 de Maio de 2019 | [Versão de atualização do Fabric 6.4 de serviço do Azure](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
