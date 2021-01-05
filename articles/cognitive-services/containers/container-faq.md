---
title: Os contentores dos Serviços Cognitivos fazem perguntas frequentes (FAQ)
titleSuffix: Azure Cognitive Services
description: Frequentemente fez perguntas e respostas.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 71b57eae1a66e6966f61123e638c4790410ef445
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862529"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Os contentores dos Serviços Cognitivos Azure fazem perguntas frequentes (FAQ)

## <a name="general-questions"></a>Perguntas gerais

**P: O que está disponível?**

**A:** Os recipientes Azure Cognitive Services permitem que os desenvolvedores utilizem as mesmas APIs inteligentes que estão disponíveis no Azure, mas com os [benefícios](../cognitive-services-container-support.md#features-and-benefits) da contentorização. Alguns contentores estão disponíveis como pré-visualização fechada que pode exigir uma aplicação de acesso. Outros recipientes estão disponíveis publicamente como pré-visualização não gata, ou estão geralmente disponíveis. Pode encontrar uma lista completa de contentores e a sua disponibilidade no suporte do Contentor no artigo [dos Serviços Cognitivos Azure.](../cognitive-services-container-support.md) Também pode ver os contentores no [Docker Hub.](https://hub.docker.com/_/microsoft-azure-cognitive-services)

**P: Existe alguma diferença entre a nuvem dos Serviços Cognitivos e os contentores?**

**A:** Os contentores dos Serviços Cognitivos são uma alternativa à nuvem dos Serviços Cognitivos. Os contentores oferecem as mesmas capacidades que os serviços de nuvem correspondentes. Os clientes podem implantar os contentores no local ou em Azure. A tecnologia de IA central, os níveis de preços, as chaves API e a assinatura API são os mesmos entre o contentor e os serviços de nuvem correspondentes. Aqui estão as [funcionalidades e benefícios](../cognitive-services-container-support.md#features-and-benefits) para escolher recipientes em vez do seu equivalente de serviço em nuvem.

**P: Como acesso e uso um recipiente de pré-visualização fechado?**

**A:** Anteriormente, os recipientes de pré-visualização fechados estavam alojados no `containerpreview.azurecr.io` repositório. A partir de 22 de setembro de 2020, estes contentores estão alojados no Registo de Contentores da Microsoft, e descarregá-los não requer que utilize o comando de login do docker. Poderá executar um recipiente de pré-visualização fechado se o seu recurso Azure tiver sido criado com o ID de assinatura Azure aprovado. Não poderá executar o contentor se a sua assinatura Azure não tiver sido aprovada após o preenchimento do [formulário de pedido](https://aka.ms/csgate).


**P: Estarão disponíveis contentores para todos os Serviços Cognitivos e quais são os próximos recipientes que devemos esperar?**

**A:** Gostaríamos de disponibilizar mais Serviços Cognitivos como ofertas de contentores. Contacte o seu gestor de conta local da Microsoft para obter atualizações sobre novas versões de contentores e outros anúncios de Serviços Cognitivos.

**P: Qual será o Acordo Service-Level (SLA) para os contentores dos Serviços Cognitivos?**

**A:** Os contentores dos Serviços Cognitivos não têm SLA.

As configurações de recursos dos serviços cognitivos são controladas pelos clientes, pelo que a Microsoft não oferecerá um SLA para disponibilidade geral (GA). Os clientes são livres de colocar contentores no local, assim definem os ambientes de acolhimento.

> [!IMPORTANT]
> Para saber mais sobre serviços cognitivos Service-Level Acordos, [visite a nossa página SLA](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/).

**P: Estes contentores estão disponíveis em nuvens soberanas?**

**A:** Nem todos conhecem o termo "nuvem soberana", por isso vamos começar com a definição:

> A "nuvem soberana" é constituída pelo [Governo](../../azure-government/documentation-government-welcome.md)azul , [Azure Alemanha](../../germany/germany-welcome.md), e pelas nuvens [Azure China 21Vianet.](/azure/china/overview-operations)

Infelizmente, os contentores dos Serviços Cognitivos *não* são suportados de forma nativa nas nuvens soberanas. Os contentores podem ser executados nestas nuvens, mas serão retirados da nuvem pública e precisam enviar dados de utilização para o ponto final público.

### <a name="versioning"></a>Controlo de versões

**P: Como é que os contentores são atualizados para a versão mais recente?**

**A:** Os clientes podem escolher quando atualizar os contentores que implantaram. Os contentores serão marcados com [etiquetas padrão do Docker,](https://docs.docker.com/engine/reference/commandline/tag/) de modo `latest` a indicar a versão mais recente. Encorajamos os clientes a puxarem a versão mais recente dos contentores à medida que são lançados, checkout [Azure Container Registry webhooks](../../container-registry/container-registry-webhook.md) para detalhes sobre como ser notificado quando uma imagem é atualizada.
 
**P: Que versões serão suportadas?**

**A:** A versão atual e última do recipiente será suportada. No entanto, encorajamos os clientes a manterem-se atuais para obterem a mais recente tecnologia.
 
**P: Como são versadas as atualizações?**

**A:** As principais alterações na versão indicam que há uma alteração na assinatura API. Prevemos que isso irá geralmente coincidir com as principais alterações de versão à oferta de nuvem do Serviço Cognitivo correspondente. Pequenas alterações na versão indicam correções de erros, atualizações de modelos ou novas funcionalidades que não fazem uma alteração de rutura na assinatura API.

## <a name="technical-questions"></a>Questões técnicas

**P: Como devo executar os contentores dos Serviços Cognitivos em dispositivos IoT?**

**A:** Se não tem uma ligação à Internet fiável, ou se quer economizar no custo da largura de banda. Ou se tiver requisitos de baixa latência, ou se estiver a lidar com dados sensíveis que precisam de ser analisados no local, [o Azure IoT Edge com os recipientes de Serviços Cognitivos](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) dá-lhe consistência com a nuvem.

**P: Estes recipientes são compatíveis com o OpenShift?** 

Não testamos contentores com OpenShift, mas geralmente, os contentores dos Serviços Cognitivos devem funcionar em qualquer plataforma que suporte imagens do Docker. Se estiver a utilizar o OpenShift, recomendamos que utilize os recipientes como `root-user` .

**P: Como fornecer feedback do produto e recomendações de recursos?**

**A:** Os clientes são encorajados a [expressar publicamente as suas preocupações](https://cognitive.uservoice.com/) e a votar em outros que fizeram o mesmo onde as questões potenciais se sobrepõem. A ferramenta de voz do utilizador pode ser utilizada tanto para o feedback do produto como para recomendações de funcionalidades.

**P: Que mensagens de estado e erros são devolvidos pelos contentores dos Serviços Cognitivos?**

**A:** Consulte a tabela seguinte para obter uma lista de mensagens de estado e erros.

|Estado  | Descrição  |
|---------|---------|
| `Valid` | A sua chave API é válida, não é necessária nenhuma ação. |
| `Invalid` |   A sua chave API é inválida. Deve fornecer uma chave API válida para executar o recipiente. Encontre a sua chave E região de serviço aPI na secção **Chaves e Endpoint** para o seu recurso Azure Cognitive Services, no portal Azure. |
| `Mismatch` | Forneceu uma Chave API ou ponto final para um tipo diferente de recurso de serviços cognitivos. Encontre a sua chave E região de serviço aPI na secção **Chaves e Endpoint** para o seu recurso Azure Cognitive Services. |
| `CouldNotConnect` | O contentor não conseguiu ligar-se ao ponto final de faturação. Verifique o `Retry-After` valor e aguarde que este período termine antes de fazer pedidos adicionais. |
| `OutOfQuota` | A chave da API está fora de quota. Pode atualizar o seu nível de preços ou esperar que a quota adicional seja disponibilizada. Encontre o seu nível na secção **De Preços** do seu recurso Azure Cognitive Service, no portal Azure. |
| `BillingEndpointBusy` | O ponto final da faturação está ocupado. Verifique o `Retry-After` valor e aguarde que este período termine antes de fazer pedidos adicionais. |
| `ContainerUseUnauthorized` | A chave API fornecida não está autorizada a ser utilizada com este recipiente. É provável que utilize um recipiente fechado, por isso certifique-se de que o seu ID de subscrição Azure é aprovado através da apresentação de um [pedido online](https://aka.ms/csgate). |
| `Unknown` | O servidor não consegue processar pedidos de faturação. |


**P: Quem contacto para apoio?**

**A:** Os canais de apoio ao cliente são os mesmos que a oferta de cloud dos Serviços Cognitivos. Todos os contentores de Serviços Cognitivos incluem funcionalidades de registo que nos ajudarão e os clientes de apoio à comunidade. Para obter apoio adicional, consulte as seguintes opções.

### <a name="customer-support-plan"></a>Plano de apoio ao cliente

Os clientes devem consultar o seu [plano de suporte Azure](https://azure.microsoft.com/support/plans/) para ver quem contactar para apoio.

### <a name="azure-knowledge-center"></a>Centro de conhecimento azul

O cliente é livre de explorar o [centro de conhecimento Azure](https://azure.microsoft.com/resources/knowledge-center/) para responder a questões e questões de suporte.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) é um site de perguntas e respostas para programadores profissionais e entusiastas.

Explore as seguintes tags para potenciais perguntas e respostas que se alinhem às suas necessidades.

* [Serviços Cognitivos do Azure](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft Cognitive](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**P: Como funciona a faturação?**

**A:** Os clientes são cobrados com base no consumo, semelhante à nuvem de Serviços Cognitivos. Os contentores precisam de ser configurados para enviar dados de medição para a Azure, e as transações serão faturadas em conformidade. Os recursos utilizados em todos os serviços alojados e no local irão adicionar a quota única com preços hierárquicos, contando com ambos os usos. Para mais detalhes, consulte a página de preços da oferta correspondente.

* [Detetor de Anomalias][ad-containers-billing]
* [Imagem Digitalizada][cv-containers-billing]
* [Face][fa-containers-billing]
* [Reconhecedor de Formato][fr-containers-billing]
* [Compreensão de Idiomas (LUIS)][lu-containers-billing]
* [API de Serviço de Voz][sp-containers-billing]
* [Análise de Texto][ta-containers-billing]

> [!IMPORTANT]
> Os recipientes dos Serviços Cognitivos não estão licenciados para funcionar sem estarem ligados ao Azure para a medição. Os clientes precisam de permitir que os contentores comuniquem informações de faturação com o serviço de medição em todos os momentos. Os recipientes de Serviços Cognitivos não enviam dados dos clientes para a Microsoft.
 
**P: Qual é a garantia de suporte atual para os recipientes?**

**A:** Não existe garantia para pré-visualizações. A garantia padrão da Microsoft para o software empresarial será aplicada quando os contentores forem anunciados formalmente como disponibilidade geral (GA).
 
**P: O que acontece aos recipientes dos Serviços Cognitivos quando a conectividade da Internet é perdida?**

**A:** Os recipientes dos Serviços Cognitivos não estão *licenciados* para funcionar sem estarem ligados ao Azure para a medição. Os clientes precisam de permitir que os contentores comuniquem sempre com o serviço de medição.

**P: Quanto tempo o recipiente pode funcionar sem estar ligado ao Azure?**

**A:** Os recipientes dos Serviços Cognitivos não estão *licenciados* para funcionar sem estarem ligados ao Azure para a medição. Os clientes precisam de permitir que os contentores comuniquem sempre com o serviço de medição.
 
**P: O que é o hardware atual necessário para executar estes contentores?**

**A:** Os recipientes de Serviços Cognitivos são recipientes de base x64 que podem executar qualquer nó Linux, VM e dispositivo de borda compatível que suporte x64 Recipientes Linux Docker. Todos precisam de processadores cpu. As configurações mínimas e recomendadas para cada oferta de contentores estão disponíveis abaixo:

* [Detetor de Anomalias][ad-containers-recommendations]
* [Imagem Digitalizada][cv-containers-recommendations]
* [Face][fa-containers-recommendations]
* [Reconhecedor de Formato][fr-containers-recommendations]
* [Compreensão de Idiomas (LUIS)][lu-containers-recommendations]
* [API de Serviço de Voz][sp-containers-recommendations]
* [Análise de Texto][ta-containers-recommendations]
 
**P: Estes contentores são atualmente suportados no Windows?**

**A:** Os contentores dos Serviços Cognitivos são contentores Linux, no entanto existe algum suporte para contentores Linux nas janelas. Para obter mais informações sobre os recipientes Linux no Windows, consulte [a documentação do Docker.](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/)
 
**P: Como descubro os contentores?**

**A:** Os contentores dos Serviços Cognitivos estão disponíveis em vários locais, tais como o portal Azure, o hub de Docker e os registos de contentores Azure. Para as localizações mais recentes do contentor, consulte as [imagens do contentor.](container-image-tags.md)

**P: Como é que os recipientes de Serviços Cognitivos se comparam às ofertas da AWS e do Google?**

**A:** A Microsoft é o primeiro fornecedor de cloud a mover os seus modelos de IA pré-treinados em contentores com faturação simples por transação como se os clientes estiverem a usar um serviço de cloud. A Microsoft acredita que uma nuvem híbrida dá mais escolha aos clientes.

**P: Que certificações de conformidade têm os contentores?**

**A:** Os contentores de serviços cognitivos não têm certificações de conformidade

**P: Em que regiões estão disponíveis os contentores dos Serviços Cognitivos?**

**A:** Os contentores podem ser executados em qualquer parte de qualquer região, no entanto precisam de uma chave e para chamar a Azure para medição. Todas as regiões apoiadas para o Serviço de Nuvens são suportadas para a chamada de medição de contentores.

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