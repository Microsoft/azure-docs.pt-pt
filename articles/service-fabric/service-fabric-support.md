---
title: Saiba mais sobre as opções de suporte do Azure Service Fabric
description: Versões de Cluster Service Fabric do Azure com suporte e links para tíquetes de suporte de arquivo
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 7494f0072f27f2c9b00db7070f19dfc05627eacf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465631"
---
# <a name="azure-service-fabric-support-options"></a>Opções de suporte do Azure Service Fabric

Para fornecer o suporte apropriado para seus clusters Service Fabric em que você está executando as cargas de trabalho do aplicativo, configuramos várias opções para você. Dependendo do nível de suporte necessário e da gravidade do problema, você terá que escolher as opções corretas. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Relatar problemas de produção ou solicitar suporte pago para o Azure

Para relatar problemas em seu cluster Service Fabric implantado no Azure, abra um tíquete para suporte [no portal do Azure ou no](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) [portal de suporte da Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Saiba mais sobre:
 
- [Suporte da Microsoft para o Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Suporte Premier da Microsoft](https://support.microsoft.com/en-us/premier).

> [!Note]
> Clusters em execução em uma camada de confiabilidade bronze ou cluster de nó único permitirão que você execute somente cargas de trabalho de teste. Se você tiver problemas com um cluster em execução em confiabilidade bronze ou cluster de nó único, a equipe de suporte da Microsoft ajudará você a mitigar o problema, mas não executará uma análise da causa raiz. Consulte [as características de confiabilidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) para obter mais detalhes.
>
> Para obter mais informações sobre o que é necessário para um cluster pronto para produção, consulte a [lista de verificação de preparação de produção](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Relatar problemas de produção ou solicitar suporte pago para clusters de Service Fabric autônomos

Para relatar problemas em seu cluster Service Fabric implantado localmente ou em outras nuvens, abra um tíquete para suporte profissional no [portal de suporte da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Saiba mais sobre:

- [Suporte profissional da Microsoft para o local](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Suporte Premier da Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Relatar problemas de Service Fabric do Azure

Configuramos um repositório GitHub para relatar problemas de Service Fabric.  Também estamos monitorando ativamente os fóruns a seguir.

### <a name="github-repo"></a>Repositório do GitHub 

Relatar problemas do Azure Service Fabric no [repositório git de problemas do Service Fabric](https://github.com/Azure/service-fabric-issues). Este repositório destina-se a emitir relatórios e controlar problemas com o Azure Service Fabric e para fazer pequenas solicitações de recursos. Não **use isso para relatar problemas de site ativo**.

### <a name="stackoverflow-and-msdn-forums"></a>Fóruns do StackOverflow e do MSDN

A [marca de Service Fabric em StackOverflow][stackoverflow] e o [Fórum de Service Fabric no MSDN][msdn-forum] são mais bem usadas para fazer perguntas sobre como a plataforma funciona e como você pode realizar determinadas tarefas com ela.

### <a name="azure-feedback-forum"></a>Fórum de comentários do Azure

O [Fórum de comentários do Azure para Service Fabric][uservoice-forum] é o melhor lugar para enviar ideias de grandes recursos que você tem para o produto à medida que analisamos as solicitações mais populares fazem parte de nosso planejamento de média a longo prazo. Incentivamos você a Rally o suporte para suas sugestões na Comunidade.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Versões de visualização do Service Fabric-sem suporte para uso em produção

De tempos em tempos, lançamos versões que têm recursos significativos dos quais queremos comentários, que são lançadas como visualizações. Essas versões de visualização só devem ser usadas para fins de teste. O cluster de produção deve estar sempre executando uma versão suportada, estável e Service Fabric. Uma versão de visualização sempre começa com um número de versão principal e secundária de 255. Por exemplo, se você vir uma Service Fabric versão 255.255.5703.949, essa versão de lançamento só será usada em clusters de teste e estará em versão prévia. Essas versões de visualização também são anunciadas no [blog da equipe Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) e terão detalhes sobre os recursos incluídos.
Não há nenhuma opção de suporte pago para essas versões de visualização. Use uma das opções listadas em [relatar problemas do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) para fazer perguntas ou fornecer comentários.

## <a name="next-steps"></a>Passos seguintes

[Versões Service Fabric com suporte](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
