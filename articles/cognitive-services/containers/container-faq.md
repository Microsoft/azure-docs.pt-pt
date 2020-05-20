---
title: Recipientes de Serviços Cognitivos frequentemente questionados (FAQ)
titleSuffix: Azure Cognitive Services
description: Perguntas e respostas frequentes.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: bf30fc5e6ccfc0f59c1769245e58177428472156
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701806"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Os contentores dos Serviços Cognitivos Azure fazem frequentemente perguntas (FAQ)

## <a name="general-questions"></a>Perguntas gerais

**P: O que está disponível?**

**A:** Os recipientes dos Serviços Cognitivos Azure permitem aos desenvolvedores utilizar as mesmas APIs inteligentes que estão disponíveis em Azure, mas com os [benefícios](../cognitive-services-container-support.md#features-and-benefits) da contentorização. Alguns contentores estão disponíveis como pré-visualização fechada que pode exigir uma aplicação de acesso. Outros recipientes estão disponíveis ao público como pré-visualização ungida, ou estão geralmente disponíveis. Pode encontrar uma lista completa de contentores e a sua disponibilidade no suporte do Contentor no artigo dos [Serviços Cognitivos Azure.](../cognitive-services-container-support.md#container-availability-in-azure-cognitive-services) 

**P: Existe alguma diferença entre a nuvem dos Serviços Cognitivos e os contentores?**

**A:** Os recipientes dos Serviços Cognitivos são uma alternativa à nuvem de Serviços Cognitivos. Os contentores oferecem as mesmas capacidades que os serviços de nuvem correspondentes. Os clientes podem implantar os contentores no local ou no Azure. A tecnologia de IA central, os níveis de preços, as teclas API e a assinatura API são as mesmas entre o contentor e os serviços de nuvem correspondentes. Aqui estão as [funcionalidades e benefícios](../cognitive-services-container-support.md#features-and-benefits) para a escolha de recipientes em vez do seu equivalente de serviço na nuvem.

**P: Os contentores estarão disponíveis para todos os Serviços Cognitivos e quais são os próximos conjuntos de contentores que devemos esperar?**

**A:** Gostaríamos de disponibilizar mais Serviços Cognitivos como ofertas de contentores. Contacte o seu gestor de conta local da Microsoft para obter atualizações sobre novos lançamentos de contentores e outros anúncios de Serviços Cognitivos.

**P: Qual será o Acordo de Nível de Serviço (SLA) para os contentores dos Serviços Cognitivos?**

**A:** Os recipientes dos Serviços Cognitivos não têm um SLA.

As configurações de recursos dos serviços cognitivos são controladas pelos clientes, pelo que a Microsoft não oferecerá um SLA para disponibilidade geral (GA). Os clientes são livres de implantar contentores no local, pelo que definem os ambientes de acolhimento.

> [!IMPORTANT]
> Para saber mais sobre os Acordos de Nível de Serviço de Serviços Cognitivos, visite a [nossa página SLA](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/).

**P: Estes contentores estão disponíveis em nuvens soberanas?**

**A:** Nem todos estão familiarizados com o termo "nuvem soberana", então vamos começar com a definição:

> A "nuvem soberana" é constituída pelo [Governo Azure](../../azure-government/documentation-government-welcome.md), [Azure Germany,](../../germany/germany-welcome.md)e pelas nuvens [Azure China 21Vianet.](https://docs.microsoft.com/azure/china/overview-operations)

Infelizmente, os contentores dos Serviços Cognitivos *não* são apoiados de forma nativa nas nuvens soberanas. Os contentores podem ser executados nestas nuvens, mas serão retirados da nuvem pública e precisam de enviar dados de uso para o ponto final público.

### <a name="versioning"></a>Controlo de versões

**P: Como os contentores são atualizados para a versão mais recente?**

**A:** Os clientes podem escolher quando atualizar os recipientes que implementaram. Os contentores serão marcados com [etiquetas padrão do Docker,](https://docs.docker.com/engine/reference/commandline/tag/) como `latest` para indicar a versão mais recente. Encorajamos os clientes a puxarem a versão mais recente dos contentores à medida que são lançados, checkout [Azure Container Registry webhooks](../../container-registry/container-registry-webhook.md) para obter detalhes sobre como ser notificado quando uma imagem é atualizada.
 
**P: Que versões serão suportadas?**

**A:** A versão atual e última do contentor será suportada. No entanto, encorajamos os clientes a manterem-se atuais para obterem a mais recente tecnologia.
 
**P: Como são versões as atualizações?**

**A:** As principais alterações da versão indicam que há uma alteração de rutura na assinatura API. Prevemos que isto irá geralmente coincidir com as principais alterações de versão à correspondente oferta de nuvem do Serviço Cognitivo. Pequenas alterações na versão indicam correções de erros, atualizações de modelos ou novas funcionalidades que não alteram a assinatura API.

## <a name="technical-questions"></a>Questões técnicas

**P: Como devo executar os recipientes dos Serviços Cognitivos em dispositivos IoT?**

**A:** Quer não tenha uma ligação de internet fiável, ou se quer economizar no custo da largura de banda. Ou se tem requisitos de baixa latência, ou se está a lidar com dados sensíveis que precisam de ser analisados no local, [o Azure IoT Edge com os recipientes dos Serviços Cognitivos](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) dá-lhe consistência com a nuvem.

**P: Estes recipientes são compatíveis com o OpenShift?** 

Não testamos contentores com OpenShift, mas geralmente, os recipientes dos Serviços Cognitivos devem funcionar em qualquer plataforma que suporte imagens do Docker. Se estiver a utilizar o OpenShift, recomendamos que os recipientes estejam a executar os recipientes como `root-user` .

**P: Como fornecer feedback do produto e recomendações de recurso?**

**A:** Os clientes são encorajados a [expressar publicamente as suas preocupações](https://cognitive.uservoice.com/) e a votar em cima de outros que fizeram o mesmo em que potenciais questões se sobrepõem. A ferramenta de voz do utilizador pode ser utilizada tanto para o feedback do produto como para as recomendações de recurso.

**P: Quem contacto para apoio?**

**A:** Os canais de apoio ao cliente são os mesmos que a oferta de nuvem de Serviços Cognitivos. Todos os recipientes dos Serviços Cognitivos incluem funcionalidades de exploração madeireira que nos ajudarão e os clientes de apoio à comunidade. Para obter apoio adicional, consulte as seguintes opções.

### <a name="customer-support-plan"></a>Plano de apoio ao cliente

Os clientes devem consultar o seu plano de [suporte Azure](https://azure.microsoft.com/support/plans/) para ver quem contactar para obter apoio.

### <a name="azure-knowledge-center"></a>Centro de conhecimento azure

O cliente é livre de explorar o centro de [conhecimento Azure](https://azure.microsoft.com/resources/knowledge-center/) para responder a questões e questões de suporte.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) é um local de perguntas e respostas para programadores profissionais e entusiastas.

Explore as seguintes etiquetas para potenciais perguntas e respostas que se alinham com as suas necessidades.

* [Serviços Cognitivos do Azure](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft Cognitive](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**P: Como funciona a faturação?**

**A:** Os clientes são cobrados com base no consumo, semelhante à nuvem de Serviços Cognitivos. Os recipientes precisam de ser configurados para enviar dados de medição para o Azure, e as transações serão faturadas em conformidade. Os recursos utilizados em todos os serviços alojados e no local irão aumentar a quota única com preços diferenciados, contando com ambas as utilizações. Para mais detalhes, consulte a página de preços da oferta correspondente.

* [Detetor de Anomalias][ad-containers-billing]
* [Imagem Digitalizada][cv-containers-billing]
* [Rostos][fa-containers-billing]
* [Reconhecedor de Formato][fr-containers-billing]
* [Compreensão de Idiomas (LUIS)][lu-containers-billing]
* [API de Serviço de Voz][sp-containers-billing]
* [Análise de Texto][ta-containers-billing]

> [!IMPORTANT]
> Os recipientes dos Serviços Cognitivos não estão licenciados para funcionar sem serem ligados ao Azure para medição. Os clientes precisam de permitir que os contentores comuniquem sempre informações de faturação com o serviço de medição. Os recipientes dos Serviços Cognitivos não enviam dados dos clientes para a Microsoft.
 
**P: Qual é a garantia de suporte atual para contentores?**

**A:** Não há garantia para pré-visualizações. A garantia padrão da Microsoft para o software empresarial será aplicada quando os contentores forem formalmente anunciados como disponibilidade geral (GA).
 
**P: O que acontece aos recipientes dos Serviços Cognitivos quando a conectividade da Internet é perdida?**

**A:** Os recipientes dos Serviços Cognitivos não estão *licenciados* para funcionar sem serem ligados ao Azure para medição. Os clientes precisam de permitir que os recipientes se comuniquem sempre com o serviço de medição.

**P: Quanto tempo o recipiente pode funcionar sem estar ligado ao Azure?**

**A:** Os recipientes dos Serviços Cognitivos não estão *licenciados* para funcionar sem serem ligados ao Azure para medição. Os clientes precisam de permitir que os recipientes se comuniquem sempre com o serviço de medição.
 
**P: O que é necessário hardware atual para executar estes contentores?**

**A:** Os recipientes dos Serviços Cognitivos são recipientes à base de x64 que podem executar qualquer nó linux compatível, VM e dispositivo de borda que suporta x64 recipientes de Docker Linux. Todos eles requerem processadores CPU. As configurações mínimas e recomendadas para cada oferta de contentores estão disponíveis abaixo:

* [Detetor de Anomalias][ad-containers-recommendations]
* [Imagem Digitalizada][cv-containers-recommendations]
* [Rostos][fa-containers-recommendations]
* [Reconhecedor de Formato][fr-containers-recommendations]
* [Compreensão de Idiomas (LUIS)][lu-containers-recommendations]
* [API de Serviço de Voz][sp-containers-recommendations]
* [Análise de Texto][ta-containers-recommendations]
 
**P: Estes contentores são atualmente suportados no Windows?**

**A:** Os recipientes dos Serviços Cognitivos são recipientes Linux, no entanto existe algum suporte para recipientes Linux no Windows. Para obter mais informações sobre os contentores Linux no Windows, consulte a [documentação do Docker](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/).
 
**P: Como descubro os contentores?**

**A:** Os contentores dos Serviços Cognitivos estão disponíveis em vários locais, como o portal Azure, o hub de Docker e os registos de contentores Azure. Para os locais mais recentes do contentor, consulte [repositórios e imagens de contentores.](../cognitive-services-container-support.md#container-repositories-and-images)

**P: Como é que os recipientes dos Serviços Cognitivos se comparam às ofertas da AWS e do Google?**

**A:** A Microsoft é o primeiro fornecedor de nuvem a mover os seus modelos de IA pré-treinados em contentores com faturação simples por transação como se os clientes usassem um serviço na nuvem. A Microsoft acredita que uma nuvem híbrida dá aos clientes mais escolha.

**P: Que certificações de conformidade têm os contentores?**

**A:** Os recipientes de serviços cognitivos não têm certificações de conformidade

**P: Em que regiões estão disponíveis os recipientes dos Serviços Cognitivos?**

**A:** Os contentores podem ser executados em qualquer região, no entanto precisam de uma chave e de chamar de volta a Azure para medição. Todas as regiões apoiadas para o Serviço de Nuvem são suportadas para a chamada de medição de contentores.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
