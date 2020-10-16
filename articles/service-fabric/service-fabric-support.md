---
title: Saiba mais sobre as opções de Suporte ao Tecido de Serviço Azure
description: Versões de cluster Azure Service Fabric suportadas e links para arquivar bilhetes de suporte
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: ae49a59c2629d9f9461d298ada555d314c0c9f22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86256973"
---
# <a name="azure-service-fabric-support-options"></a>Opções de suporte ao tecido de serviço Azure

Para fornecer o suporte adequado para os seus clusters de Tecido de Serviço em que está a executar as cargas de trabalho da sua aplicação, definimos várias opções para si. Dependendo do nível de apoio necessário e da gravidade da questão, pode escolher as opções certas. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Reportar problemas de produção ou solicitar apoio remunerado à Azure

Para reportar problemas no seu cluster de Tecido de Serviço implantado no Azure, abra um bilhete de apoio [no portal Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ou [no portal de suporte da Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Saiba mais sobre:
 
- [Suporte da Microsoft para O Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Suporte premier da Microsoft](https://support.microsoft.com/en-us/premier).

> [!Note]
> Os agrupamentos que funcionam num nível de fiabilidade de bronze ou num único aglomerado de nó permitir-lhe-ão executar apenas cargas de trabalho de teste. Se sentir problemas com um cluster a correr na fiabilidade do bronze ou no Single Node Cluster, a equipa de suporte da Microsoft irá ajudá-lo a mitigar o problema, mas não realizará uma Análise de Causas De Raiz. Consulte [as características de fiabilidade do cluster](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) para mais detalhes.
>
> Para obter mais informações sobre o que é necessário para um cluster pronto para a produção, consulte a [lista de verificação de prontidão de produção](./service-fabric-production-readiness-checklist.md).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Reportar problemas de produção ou solicitar apoio pago para clusters de tecidos de serviço autónomos

Para reportar problemas no seu cluster de Tecido de Serviço implantado no local ou em outras nuvens, abra um bilhete para apoio profissional no [portal de suporte da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Saiba mais sobre:

- [Suporte Profissional da Microsoft para as instalações.](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)
- [Suporte premier da Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Reportar problemas de tecido de serviço Azure

Criámos um repo GitHub para reportar problemas de Tecido de Serviço.  Estamos também a monitorizar ativamente os seguintes fóruns.

### <a name="github-repo"></a>GitHub repo 

Reportar problemas de tecido de serviço Azure sobre [questões de tecido de serviço git repo](https://github.com/Azure/service-fabric-issues). Este repo destina-se a reportar e rastrear problemas com a Azure Service Fabric e a fazer pequenos pedidos de funcionalidades. **Não o utilize para reportar problemas no local.**

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow e fóruns MSDN

A [etiqueta de Tecido de Serviço no StackOverflow][stackoverflow] e o fórum de tecido de serviço na [MSDN][msdn-forum] são mais utilizados para fazer perguntas sobre como a plataforma funciona e como pode realizar certas tarefas com ele.

### <a name="azure-feedback-forum"></a>Fórum de Feedback Azure

O [Azure Feedback Forum for Service Fabric][uservoice-forum] é o melhor local para submeter grandes ideias de recurso que tem para o produto, uma vez que analisamos os pedidos mais populares fazem parte do nosso planeamento a médio e longo prazo. Encorajamo-lo a reunir apoio para as suas sugestões dentro da comunidade.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Versões de pré-visualização do tecido de serviço - não suportado para uso de produção

De tempos a tempos, lançamos versões que têm funcionalidades significativas sobre as quais queremos feedback, que são lançados como pré-visualizações. Estas versões de pré-visualização só devem ser utilizadas para efeitos de teste. O seu cluster de produção deve estar sempre a executar uma versão suportada, estável, de Tecido de Serviço. Uma versão de pré-visualização começa sempre com um número de versão maior e menor de 255. Por exemplo, se vir uma versão 255.255.5703.949, esta versão de lançamento só deve ser utilizada em clusters de teste e está em pré-visualização. Estes lançamentos de pré-visualização também são anunciados no blog da equipa do [Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) e terão detalhes sobre as funcionalidades incluídas.
Não existe nenhuma opção de suporte pago para estes lançamentos de pré-visualização. Utilize uma das opções listadas nos [problemas do Report Azure Service Fabric](#report-azure-service-fabric-issues) para fazer perguntas ou fornecer feedback.

## <a name="next-steps"></a>Passos seguintes

[Versões de tecido de serviço suportado](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform
