---
title: Azure Service Bus premium e níveis padrão
description: Este artigo descreve os níveis standard e premium da Azure Service Bus. Compara estes níveis e proporciona diferenças técnicas.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: aa08a99009ef3d20e831e214ae5811059817d13c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607556"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Escalões de mensagens Standard e Premium do Service Bus

As Mensagens do Service Bus, que incluem entidades como filas e tópicos, combinam capacidades de mensagens empresariais com semântica de publicação-subscrição avançada à escala da cloud. As Mensagens do Service Bus são utilizadas como base de comunicação para várias soluções de cloud sofisticadas.

O nível *Premium* de Mensagens de Autocarro de Serviço aborda pedidos comuns de clientes em torno da escala, desempenho e disponibilidade para aplicações críticas da missão. O escalão Premium é recomendado para os cenários de produção. Embora os conjuntos de funcionalidades sejam praticamente idênticos, estes dois escalões de Mensagens do Service Bus foram concebidos para serem utilizados em situações diferentes.

Na tabela seguinte, destacam-se algumas das principais diferenças.

| Premium | Standard |
| --- | --- |
| Débito elevado |Débito variável |
| Desempenho previsível |Latência variável |
| Preços fixos |Preços variáveis de utilização |
| Possibilidade de aumentar e reduzir verticalmente a carga de trabalho |N/D |
| Tamanho da mensagem até 1 MB. Este limite poderá ser aumentado no futuro. Para obter as mais recentes atualizações importantes do serviço, consulte [Mensagens no blog Azure](https://techcommunity.microsoft.com/t5/messaging-on-azure/bg-p/MessagingonAzureBlog). |Tamanho da mensagem até 256 KB |

As **Mensagens Premium do Service Bus** fornecem isolamento de recursos no nível de CPU e memória para que cada carga de trabalho do cliente seja executada de forma isolada. Este contentor de recursos é designado por *unidade de mensagens*. A cada espaço de nomes premium é atribuído, pelo menos, uma unidade de mensagens. Você pode comprar 1, 2, 4, 8 ou 16 unidades de mensagens para cada espaço de nome Service Bus Premium. Uma única carga de trabalho ou entidade pode abranger várias unidades de mensagens e o número de unidades de mensagens pode ser alterado à vontade. O resultado é um desempenho previsível e repetível da sua solução com base no Service Bus.

Este desempenho não só é mais previsível e repetível, como também mais rápido. Com as Mensagens Premium, obter o máximo rendimento é muito mais rápido do que com o escalão Standard.

## <a name="premium-messaging-technical-differences"></a>Diferenças técnicas das Mensagens Premium

As seguintes secções abordam as diferenças entre as camadas de mensagens Standard e Premium.

### <a name="partitioned-queues-and-topics"></a>Filas e tópicos particionados

Filas e tópicos divididos não são suportados em Mensagens Premium. Para mais informações sobre a criação de partições, consulte o artigo [Filas e tópicos particionados](service-bus-partitioning.md).

### <a name="express-entities"></a>Entidades expressas

Como as mensagens Premium funcionam num ambiente isolado de tempo de execução, as entidades expressas não são suportadas em espaços de nome Premium. Uma entidade expressa guarda uma mensagem na memória temporariamente antes de escrevê-la para armazenamento persistente. Se tiver um código em execução sob mensagens Standard e quiser acordá-lo para o nível Premium, certifique-se de que a funcionalidade de entidade expressa está desativada.

## <a name="premium-messaging-resource-usage"></a>Uso de recursos de mensagens premium
Em geral, qualquer operação numa entidade pode causar a utilização da CPU e da memória. Eis algumas destas operações: 

- Operações de gestão como crud (Criar, Recuperar, Atualizar e Eliminar) operações em filas, tópicos e subscrições.
- Operações de tempo de execução (enviar e receber mensagens)
- Operações de monitorização e alertas

O uso adicional de CPU e memória não tem preço adicional. Para o nível de mensagens Premium, há um único preço para a unidade de mensagens.

O CPU e o uso da memória são rastreados e apresentados para si pelas seguintes razões: 

- Fornecer transparência nos internos do sistema
- Compreender a capacidade dos recursos adquiridos.
- Planeamento de capacidade que o ajuda a decidir escalar para cima ou para baixo.

## <a name="messaging-unit---how-many-are-needed"></a>Unidade de mensagens - Quantos são necessários?

Ao providenciar um espaço de nome Azure Service Bus Premium, deve ser especificado o número de unidades de mensagens atribuídas. Estas unidades de mensagens são recursos dedicados que são atribuídos ao espaço de nome.

O número de unidades de mensagens atribuídas ao espaço de nomes Service Bus Premium pode ser **ajustado dinamicamente** para ter em conta a mudança (aumento ou diminuição) das cargas de trabalho.

Há uma série de fatores a ter em conta na hora de decidir o número de unidades de mensagens para a sua arquitetura:

- Comece com ***1 ou 2 unidades de mensagens atribuídas*** ao seu espaço de identificação.
- Estude as métricas de utilização do CPU dentro das [métricas de utilização](service-bus-metrics-azure-monitor.md#resource-usage-metrics) do recurso para o seu espaço de identificação.
    - Se o uso do CPU for *** abaixo de 20%** _, poderá ser capaz de reduzir a *_escala_** o número de unidades de mensagens atribuídas ao seu espaço de nome.
    - Se a utilização do CPU for *** superior a 70%** _, a sua aplicação beneficiará de um *_aumento_* de escala * do número de unidades de mensagens atribuídas ao seu espaço de nome.

Para aprender a configurar um espaço de nomes de Service Bus para escalar automaticamente (aumentar ou diminuir as unidades de mensagens), consulte [atualizar automaticamente as unidades de mensagens](automate-update-messaging-units.md).

> [!NOTE]
> **A escala** dos recursos atribuídos ao espaço de nome pode ser preventiva ou reativa.
>
>  * **Preventiva**: Se for esperada carga de trabalho adicional (devido à sazonalidade ou tendências), pode proceder à atribuição de mais unidades de mensagens ao espaço de nome antes que as cargas de trabalho sejam atingidas.
>
>  * **Reativo**: Se forem identificadas cargas de trabalho adicionais através do estudo das métricas de utilização de recursos, então podem ser atribuídos recursos adicionais ao espaço de nomes para incorporar a procura crescente.
>
> Os contadores de faturação do Service Bus são de hora a hora. No caso de escalonamento, só se paga os recursos adicionais pelas horas que foram usadas.
>

## <a name="get-started-with-premium-messaging"></a>Introdução às Mensagens Premium

A introdução às Mensagens Premium é simples e o processo é semelhante ao das Mensagens Standard. Comece por [criar um espaço de nomes](service-bus-create-namespace-portal.md) no [portal do Azure](https://portal.azure.com). Certifique-se de que seleciona **Premium**, em **Escalão de preço**. Clique em **Ver informações completas de preços** para obter mais informações sobre cada escalão.

![create-premium-namespace][create-premium-namespace]

Também pode criar [espaços de nomes Premium com modelos do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre Mensagens do Service Bus, veja as ligações seguintes:

- [Atualize automaticamente as unidades de mensagens](automate-update-messaging-units.md).
- [Apresentando mensagens premium de autocarro do Azure Service (blog post)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introdução às Mensagens Premium do Azure Service Bus (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
