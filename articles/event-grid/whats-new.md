---
title: Novidades? Notas de lançamento - Azure Event Grid
description: Saiba quais as novidades com a Azure Event Grid, como as últimas notas de lançamento, problemas conhecidos, correções de bugs, funcionalidades preprecadas e próximas alterações.
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: f135b25f28002c037dd24fa0cb3bb7476a06309f
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389592"
---
# <a name="whats-new-in-azure-event-grid"></a>Quais as novidades na Grelha de Eventos Azure?

>Seja notificado sobre quando revisitar esta página para obter atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us` no leitor de feed do leitor de feed ![ RSS. ](./media/whats-new/feed-icon-16x16.png)

A Azure Event Grid recebe melhorias continuadamente. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

- Os últimos lançamentos
- Problemas conhecidos
- Correções de erros
- Funcionalidade preterida
- Planos para alterações

## <a name="610-preview-2020-10"></a>6.1.0-pré-visualização (2020-10)
- [Identidades geridas para tópicos do sistema](enable-identity-system-topics.md)
- [Propriedades de entrega personalizada](delivery-properties.md)
- [Fila de armazenamento - tempo de vida da mensagem (TTL)](delivery-properties.md#configure-time-to-live-on-outgoing-events-to-azure-storage-queues)
- [Melhorias avançadas da filtragem](event-filtering.md#advanced-filtering)
    - Apoiar a filtragem dos dados da matriz em eventos de entrada
    - Permitir a filtragem nos atributos de contexto de extensões cloudEvents
    - Novos operadores
        - StringNotContains
        - StringNotBeginsWith
        - StringNotEndsWith
        - NúmeroInRange
        - NúmeroNotInRange
        - Isnull
        - IsNotNull
- [Permitir o esquema da Grelha de Eventos para cloudEvents 1.0 transformações de esquemas para tópicos e domínios personalizados](cloudevents-schema.md#configure-event-grid-for-cloudevents)
        

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- Adicione suporte à nova versão API de serviço geralmente disponível (GA) 2020-06-01.
- As novas funcionalidades que se tornaram GA:
    - [Mapeamentos de entrada](input-mappings.md)
    - [Esquema de entrada personalizado](input-mappings.md)
    - [Esquema de evento em nuvem V10](cloud-event-schema.md)
    - [Tópico de ônibus de serviço como destino](handler-service-bus.md)
    - [Função azul como destino](handler-functions.md)
    - [Loteamento webhook](./edge/delivery-output-batching.md)
    - [Webhook seguro (suporte ao Diretório Ativo Azure)](secure-webhook-delivery.md)
    - [Filtragem ip](configure-firewall.md)
    - [Suporte ao Serviço de Ligação Privada](configure-private-endpoints.md)
    - [Esquema de entrega de eventos](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2-pré-visualização (2020-05)
- Esta versão inclui correções adicionais de bugs para melhorar a qualidade.
- Como versão 5.3.1-pré-visualização, esta versão corresponde à versão API de pré-visualização 2020-04-01-Preview, que inclui as seguintes novas funcionalidades: 
    - [Suporte para filtragem ip ao publicar eventos em domínios e tópicos](configure-firewall.md)
    - [Tópicos para parceiros](./partner-events-overview.md)
    - [Tópicos do sistema como recursos rastreados no portal Azure](system-topics.md)
    - [Entrega de eventos com identidade de serviço gerido](managed-service-identity.md) 
    - [Suporte ao Serviço de Ligação Privada](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1-pré-visualização (2020-04)
- Esta versão inclui várias correções de bugs para melhorar a qualidade.
- Como versão 5.3.0-pré-visualização, esta versão corresponde à versão API de pré-visualização 2020-04-01-Preview, que inclui as seguintes novas funcionalidades: 
    - [Suporte para filtragem ip ao publicar eventos em domínios e tópicos](configure-firewall.md)
    - [Tópicos para parceiros](./partner-events-overview.md)
    - [Tópicos do sistema como recursos rastreados no portal Azure](system-topics.md)
    - [Entrega de eventos com identidade de serviço gerido](managed-service-identity.md) 
    - [Suporte ao Serviço de Ligação Privada](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0-pré-visualização (2020-03)
- Introduzimos novas funcionalidades no topo das funcionalidades já adicionadas na versão 5.2.0 de pré-visualização. 
- Como versão 5.2.0-pré-visualização, esta versão corresponde à versão API de pré-visualização 2020-04-01-Preview.
- Adiciona suportes às seguintes novas funcionalidades: 
    - [Suporte para filtragem ip ao publicar eventos em domínios e tópicos](configure-firewall.md)
    - [Tópicos para parceiros](./partner-events-overview.md)
    - [Tópicos do sistema como recursos rastreados no portal Azure](system-topics.md)
    - [Entrega de eventos com identidade de serviço gerido](managed-service-identity.md) 
    - [Suporte ao Serviço de Ligação Privada](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0-pré-visualização (2020-01)
- Esta versão corresponde à versão API de 2020-04-01-Preview.
- Adiciona suportes à seguinte nova funcionalidade:
    - [Suporte para filtragem ip ao publicar eventos em domínios e tópicos](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- Esta versão corresponde à `2019-06-01` versão API.
- Adiciona suporte às seguintes novas funcionalidades:
    * [Domínios](event-domains.md)
    * Paginação e filtro de pesquisa para operações de lista de recursos. Por exemplo, consulte [Tópicos - Lista por Subscrição](/rest/api/eventgrid/version2020-10-15-preview/partnernamespaces/listbysubscription).
    * [Fila de ônibus de serviço como destino](handler-service-bus.md)
    * [Filtragem avançada](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0-pré-visualização (2019-03)
- Esta versão corresponde à versão API de pré-visualização 2019-02-01.
- Adiciona suporte às seguintes novas funcionalidades:
    * Paginação e filtro de pesquisa para operações de lista de recursos. Por exemplo, consulte [Tópicos - Lista por Subscrição](/rest/api/eventgrid/version2020-10-15-preview/partnernamespaces/listbysubscription).
    * [Criação/eliminação manual de tópicos de domínio](how-to-event-domains.md)
    * [Fila de ônibus de serviço como destino](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- Esta versão corresponde à `2019-01-01` versão API estável.
- Suporta disponibilidade geral (GA) das seguintes funcionalidades relacionadas com subscrições de eventos:
    * [Destino carta morta](manage-event-delivery.md)
    * [Fila de armazenamento Azure como destino](handler-storage-queues.md)
    * [Azure Relay - Conexão Híbrida como destino](handler-relay-hybrid-connections.md)
    * [Validação manual do aperto de mão](webhook-event-delivery.md)
    * [Apoio às políticas de retíria](delivery-and-retry.md)
- Funcionalidades que ainda estão em pré-visualização (como [domínios de Grade](event-domains.md) de Eventos ou [suporte a filtros avançados](event-filtering.md#advanced-filtering) ainda podem ser acedidas usando a versão de pré-visualização 3.0.1 do SDK."

## <a name="301-preview-2018-10"></a>3.0.1-pré-visualização (2018-10)
- Assumindo a dependência [da versão 10.0.3 do pacote Newtonsoft NuGet](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3).

## <a name="300-preview-2018-10"></a>3.0.0-pré-visualização (2018-10)
- Este lançamento é um SDK de pré-visualização para as novas funcionalidades introduzidas na versão API de pré-visualização 2018-09-15. - Esta versão inclui suporte para:
    - [Tópicos de domínio e domínio](event-domains.md)
    - Introdução da [data de validade para a subscrição do evento](concepts.md#event-subscription-expiration)
    - Introdução de [filtragem avançada](event-filtering.md#advanced-filtering) para subscrições de eventos
    - A versão estável do SDK que visa a `2018-01-01` versão API continua a existir como versão 1.3.0

## <a name="next-steps"></a>Passos seguintes