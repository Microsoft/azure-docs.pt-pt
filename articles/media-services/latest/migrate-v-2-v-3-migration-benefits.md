---
title: Passo 1 - Compreender os benefícios da migração para a API V3 dos Serviços de Comunicação Social | Microsoft Docs
description: Este artigo enumera os benefícios da migração dos Serviços de Comunicação Social v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: c1e3fae35ff249b4435cf2fdcd2bf691bc393d56
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690673"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>Passo 1 - Compreender os benefícios da migração para a API V3 dos Serviços de Comunicação Social

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-1.svg)

Encorajamo-lo a começar a usar a versão 2020-05-01 do API AZure Media Services V3 agora para obter os benefícios porque novas funcionalidades, funcionalidades e otimizações de desempenho só estão disponíveis na atual API V3.

Pode alterar a versão API no portal, SDKs mais recentes, CLI mais recente e REST API com a cadeia de versão correta.

Houve melhorias significativas nos Serviços de Comunicação social com a V3.  

## <a name="benefits-of-media-services-v3"></a>Benefícios dos Serviços de Mídia v3

| **Recurso V3** | **Vantagem** |
| --- | --- |
| **Portal do Azure** | |
| Atualizações do portal Azure | O portal Azure foi atualizado para incluir a gestão de entidades da V3 API. Permite que os clientes utilizem o portal para iniciar o streaming ao vivo, submeter empregos de transformação V3, gerir políticas de proteção de conteúdos, transmitir pontos finais, ter acesso a API, gerir contas de armazenamento ligadas e executar tarefas de monitorização. |
| **Contas e Armazenamento** | |
| Controlo de acesso baseado em funções Azure (RBAC) | Os clientes podem agora definir as suas próprias funções e controlar o acesso a cada entidade na API dos Serviços de Comunicação Social. Isto ajuda a controlar o acesso aos recursos através de contas AAD. |
| Identidades geridas | Identidades geridas eliminam a necessidade de os desenvolvedores gerirem as credenciais fornecendo uma identidade para o recurso Azure em Azure AD. Consulte [aqui](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)detalhes sobre identidades geridas. |
| Suporte de ligação privada | Os clientes acederão aos pontos finais dos Serviços de Comunicação social para entrega de chaves, LiveEvents e StreamingEndpoints através de um PrivateEndpoint no seu VNet. |
| [Chaves de maneio do cliente](concept-use-customer-managed-keys-byok.md) ou traga o seu próprio suporte chave (BYOK) | Os clientes podem encriptar os dados na sua conta de Serviços de Mídia usando uma chave no seu Cofre chave Azure. |
| **Recursos** | |
| Um Ativo pode ter [vários localizadores de streaming](streaming-locators-concept.md) cada um com diferentes [configurações dinâmicas](dynamic-packaging-overview.md) de embalagem e encriptação dinâmica. | Há um limite de 100 localizadores de streaming permitidos em cada ativo. Os clientes podem armazenar uma única cópia do conteúdo dos meios de comunicação no ativo, mas partilhar diferentes URLs de streaming com diferentes políticas de streaming ou políticas de proteção de conteúdos que se baseiam num público-alvo.
| **Processamento de emprego** ||
| A V3 introduz o conceito de [Transforms](transforms-jobs-concept.md)   para processamento de job baseado em ficheiros. | Um Transform pode ser usado para construir configurações reutilizáveis, para criar modelos de gestor de recursos Azure e isolar configurações de processamento entre vários clientes ou inquilinos. |
| Para o processamento de trabalho baseado em ficheiros, pode utilizar um URL HTTP(S) como entrada. | Não precisa de ter conteúdo já armazenado no Azure, nem precisa de criar ativos de entrada. |
| **Eventos ao vivo** ||
| Premium 1080p Eventos ao Vivo | O Novo Live Event SKU permite que os clientes obtenham codificação em nuvem com uma saída até 1080p em resolução. |
| Novo suporte ao vivo de [baixa latência](live-event-latency.md) em Live Events. | Isto permite que os utilizadores assistam a eventos ao vivo mais perto do tempo real do que se não tivessem esta definição ativada. |
| O Live Event Preview suporta [embalagens dinâmicas](dynamic-packaging-overview.md)   e encriptação dinâmica. | Isto permite a proteção de conteúdos na pré-visualização e na embalagem DASH e HLS. |
| Saídas ao vivo substituem Programas | A saída ao vivo é mais simples de usar do que a entidade do programa nas APIs v2. |
| RTMP ingestão para Eventos Ao Vivo é melhorada, com apoio para mais codificadores | Aumenta a estabilidade e proporciona flexibilidade de codificadores de origem. |
| Eventos ao Vivo podem transmitir 24x7 | Pode realizar um Live Event e manter o seu público envolvido por períodos mais longos. |
| Transcrição ao vivo em Eventos Ao Vivo | A transcrição ao vivo permite que os clientes transcrevam automaticamente a língua falada em texto em tempo real durante a transmissão do evento ao vivo. Isto melhora significativamente a acessibilidade de eventos ao vivo. |
| [Modo stand-by](live-events-outputs-concept.md#standby-mode) em Eventos Ao Vivo | Eventos ao vivo que estão em estado de espera são menos dispendiosos do que executar eventos ao vivo. Isto permite que os clientes mantenham um conjunto de eventos ao vivo que estão prontos para começar em segundos a um custo mais baixo do que manter um conjunto de eventos ao vivo. A redução dos preços dos eventos em standby entrará em vigor em fevereiro de 2021 para a maioria das regiões, com o resto a seguir em abril de 2021.
|**Proteção de conteúdos** ||
| Proteção de [conteúdos](content-key-policy-concept.md)   suporta funcionalidades multi-chave. | Os clientes podem agora utilizar várias chaves de encriptação de conteúdo nos seus localizadores de streaming. |
| **Monitorização** | |
| Suporte de notificação [Azure EventGrid](reacting-to-media-services-events.md) | As notificações do EventGrid são mais ricas em funcionalidades. Existem mais tipos de notificações, suporte SDK mais amplo para receber as notificações na sua própria aplicação, e mais serviços Azure existentes que podem atuar como manipuladores de eventos. |
| [Apoio e integração do Azure Monitor no portal Azure](monitor-events-portal-how-to.md) | Isto permite que os clientes visualizem o uso da quota de conta dos Media Services, estatísticas em tempo real de pontos finais de streaming, e ingerir e arquivar estatísticas para eventos ao vivo. Os clientes podem agora definir alertas e executar as ações necessárias com base em dados métricos em tempo real. |

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
