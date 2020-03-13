---
title: Conheça as opções de suporte de tecido de serviço Azure
description: Versões de cluster azure service fabric suportadas e links para arquivar bilhetes de suporte
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 7494f0072f27f2c9b00db7070f19dfc05627eacf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282097"
---
# <a name="azure-service-fabric-support-options"></a>Opções de suporte de tecido de serviço Azure

Para fornecer o suporte adequado para os seus clusters de Tecido de Serviço em que está a executar as suas cargas de trabalho de aplicação, estabelecemos várias opções para si. Dependendo do nível de apoio necessário e da gravidade da questão, pode escolher as opções certas. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Reportar questões de produção ou solicitar apoio pago ao Azure

Para reportar problemas no seu cluster Service Fabric implantado no Azure, abra um bilhete de suporte [no portal Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ou [no portal](https://support.microsoft.com/oas/default.aspx?prid=16146)de suporte da Microsoft .

Saiba mais sobre:
 
- [Suporte da Microsoft para o Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Suporte premier da Microsoft](https://support.microsoft.com/en-us/premier).

> [!Note]
> Os clusters que funcionam num nível de fiabilidade de bronze ou num cluster de nó único permitirão-lhe executar apenas trabalhos de trabalho de teste. Se tiver problemas com um cluster em execução de fiabilidade de bronze ou Cluster de Nó Único, a equipa de suporte da Microsoft irá ajudá-lo a mitigar o problema, mas não realizará uma Análise de Causa seletiva. Consulte [as características de fiabilidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) para obter mais detalhes.
>
> Para obter mais informações sobre o que é necessário para um cluster pronto para a produção, consulte a lista de verificação de [prontidão](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)de produção .

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Reportar problemas de produção ou solicitar apoio pago para clusters autónomos de tecidos de serviço

Para reportar problemas no seu cluster Service Fabric implantado no local ou em outras nuvens, abra um bilhete para suporte profissional no portal de suporte da [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Saiba mais sobre:

- [Suporte Profissional da Microsoft para as instalações.](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)
- [Suporte premier da Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Reporte problemas de tecido de serviço Azure

Criámos um repo GitHub para reportar problemas de Tecido de Serviço.  Estamos também a monitorizar ativamente os seguintes fóruns.

### <a name="github-repo"></a>Repo GitHub 

Reporte Problemas de tecido de serviço Azure sobre questões de [serviço-tecido git repo](https://github.com/Azure/service-fabric-issues). Este repo destina-se a reportar e rastrear problemas com a Azure Service Fabric e a fazer pequenos pedidos de funcionalidades. **Não utilize isto para relatar problemas no site ao vivo**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow e fóruns mSDN

A etiqueta de tecido de [serviço no StackOverflow][stackoverflow] e o fórum de tecido de [serviço na MSDN][msdn-forum] são mais utilizados para fazer perguntas sobre como a plataforma funciona e como poderá realizar certas tarefas com ela.

### <a name="azure-feedback-forum"></a>Fórum de Feedback de Azure

O [Azure Feedback Forum for Service Fabric][uservoice-forum] é o melhor local para apresentar grandes ideias de funcionalidades que você tem para o produto à medida que analisamos os pedidos mais populares fazem parte do nosso planeamento a médio e longo prazo. Encorajamo-lo a reunir apoio para as suas sugestões dentro da comunidade.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Versões de pré-visualização de tecido de serviço - sem suporte para uso de produção

De tempos a tempos, lançamos versões que têm funcionalidades significativas que queremos feedback, que são lançadas como pré-visualizações. Estas versões de pré-visualização só devem ser utilizadas para efeitos de teste. O seu cluster de produção deve estar sempre a executar uma versão suportada, estável e de Tecido de Serviço. Uma versão de pré-visualização começa sempre com um número de versão maior e menor de 255. Por exemplo, se vir uma versão 255.255.5703.949 do Service Fabric, essa versão de lançamento só deve ser utilizada em clusters de teste e está em pré-visualização. Estes lançamentos de pré-visualização também são anunciados no blog da [equipa service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) e terão detalhes sobre as funcionalidades incluídas.
Não existe uma opção de apoio remunerado para estes lançamentos de pré-visualização. Utilize uma das opções listadas em questões do [Report Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) para fazer perguntas ou fornecer feedback.

## <a name="next-steps"></a>Passos seguintes

[Versões de Tecido de Serviço Suportado](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
