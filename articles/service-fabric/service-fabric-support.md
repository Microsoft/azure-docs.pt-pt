---
title: Saiba mais sobre as opções de suporte do Azure Service Fabric | Documentos da Microsoft
description: Pedidos de suporte de versões de cluster do Service Fabric suportadas e links para ficheiros do Azure
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: aa4fd391aef3acf8e72513d91b62c7f16a13062d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57889442"
---
# <a name="azure-service-fabric-support-options"></a>Opções de suporte do Azure Service Fabric

Para fornecer o suporte adequado para os seus clusters do Service Fabric que está a executar as cargas de trabalho do aplicativo, configuramos várias opções para. Consoante o nível de suporte necessário e a gravidade do problema, tem de escolher as opções certas. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Comunicar problemas de produção ou pedido de suporte pago para o Azure

Para problemas de relatórios no seu cluster do Service Fabric implementado no Azure, abra um pedido de suporte [no portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ou [portal de suporte da Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Saiba mais sobre:
 
- [Suporte da Microsoft para o Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [O suporte premier da Microsoft](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Comunicar problemas de produção ou pedido de suporte pago autónomo que clusters do Service Fabric

Para os relatórios de problemas no seu cluster do Service Fabric implementado no local ou noutras clouds, abra um pedido de suporte profissional no [portal de suporte da Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Saiba mais sobre:

- [Suporte profissional da Microsoft para on-premises](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [O suporte premier da Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Problemas de relatório do Azure Service Fabric

Configuramos um repositório do GitHub para os relatórios de problemas do Service Fabric.  Estamos também ativamente monitorando os seguintes fóruns.

### <a name="github-repo"></a>Repositório do GitHub 

Relatórios de problemas de recursos de infraestrutura do serviço do Azure sobre [repositório de git de problemas de recursos de infraestrutura de serviço](https://github.com/Azure/service-fabric-issues). Este repositório destina-se para reportar e acompanhar problemas com o Azure Service Fabric e para fazer solicitações de recursos pequenos. **Não utilize esta opção para comunicar problemas de sites ativos**.

### <a name="stackoverflow-and-msdn-forums"></a>Fóruns de Stack Overflow e do MSDN

O [tag do Service Fabric no Stack Overflow] [ stackoverflow] e o [fórum do Service Fabric no MSDN] [ msdn-forum] são melhor utilizado para fazer perguntas sobre como o plataforma funciona e como fazer determinadas tarefas com o mesmo.

### <a name="azure-feedback-forum"></a>Fórum de comentários do Azure

O [fórum de comentários do Azure para o Service Fabric] [ uservoice-forum] é o melhor local para o envio de ideias para funcionalidades grande tem para o produto como vamos rever os pedidos mais populares fazem parte do nosso médio para planejamento de longo prazo. Incentivamos a rally suporte para suas sugestões na Comunidade.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Versões de pré-visualização de recursos de infraestrutura do serviço - não suportadas para utilização em produção

Periodicamente, fazemos lançamentos de versões que tenham recursos significativos queremos os comentários, que são lançados como pré-visualizações. Estas versões de pré-visualização só devem ser utilizadas para fins de teste. O cluster de produção deve sempre executar uma versão de Service Fabric estável, e suportada. Uma versão de pré-visualização sempre começa com um número de versão principal e secundária de 255. Por exemplo, se vir uma versão 255.255.5703.949 do Service Fabric, essa versão de lançamento só deve ser usado em clusters de teste e está em pré-visualização. Estas versões de pré-visualização também sejam anunciadas no [blogue da equipa do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) e que serão tiver os detalhes sobre as funcionalidades incluídas.
Não existe nenhuma opção de suporte pagas para estas versões de pré-visualização. Utilize uma das opções listadas na [problemas de relatório do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) para colocar questões ou comentários.

## <a name="next-steps"></a>Passos Seguintes

[Versões suportadas do Service Fabric](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples