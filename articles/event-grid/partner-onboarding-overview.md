---
title: Visão geral do parceiro no rodapé (Azure Event Grid)
description: Fornece uma visão geral de como você pode embarcar como um parceiro de Grade de Eventos.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 40d0afe0aaeb40412948eb304a36a3627566551b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869694"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>Visão geral do parceiro no rodapé (Azure Event Grid)

Este artigo descreve como utilizar em privado os recursos parceiros da Azure Event Grid e como se tornar um tipo de tópico de parceiro disponível ao público.

Não precisa de permissão especial para começar a usar os tipos de recursos da Grade de Eventos associados a eventos de publicação como parceiro de Grade de Eventos. Na verdade, pode usá-los hoje para publicar eventos em privado para as suas próprias subscrições Azure e para testar o modelo de recursos se estiver a considerar tornar-se um parceiro.

> [!NOTE]
> Para obter instruções passo a passo sobre como embarcar como parceiro de Grade de Eventos utilizando o portal Azure, consulte [Como embarcar como parceiro de Grade de Eventos (portal Azure)](onboard-partner.md). 

## <a name="how-partner-events-work"></a>Como funcionam os Eventos Parceiros
Os Eventos Parceiros levam a arquitetura existente que a Event Grid já utiliza para publicar eventos a partir de recursos da Azure, como o Azure Storage e o Azure IoT Hub, e disponibiliza essas ferramentas ao público para qualquer pessoa usar. A utilização destas ferramentas é, por defeito, privada apenas para a sua subscrição Azure. Para disponibilizar publicamente os seus eventos, preencha o formulário e [contacte a equipa da Grelha de Eventos.](mailto:gridpartner@microsoft.com)

A funcionalidade Eventos Parceiros permite-lhe publicar eventos na Azure Event Grid para consumo multitenant.

## <a name="onboarding-and-event-publishing-overview"></a>Visão geral de embarque e publicação de eventos

### <a name="partner-flow"></a>Fluxo de parceiros

1. Crie um inquilino azul se ainda não tiver um.
1. Utilize o CLI Azure para criar uma nova Grelha de Eventos `partnerRegistration` . Este recurso inclui informações como nome de exibição, descrição, configuração URI, e assim por diante.

    ![Criar um tópico de parceiro](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Crie um ou mais espaços de nomes parceiros em cada região onde pretende publicar eventos. O serviço Event Grid fornece um ponto final de publicação (por `https://contoso.westus-1.eventgrid.azure.net/api/events` exemplo,) e chaves de acesso.

    ![Criar um espaço de nome de parceiro](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Forneça uma forma de os clientes se registarem no seu sistema que querem um tópico de parceiro.
1. Contacte a equipa da Grelha de Eventos para que saibam que pretende que o tipo de tópico do seu parceiro se torne público.

### <a name="customer-flow"></a>Fluxo de clientes

1. O seu cliente visita o portal Azure para observar o ID de subscrição Azure e o grupo de recursos em que querem o tópico parceiro criado.
1. O cliente solicita um tópico de parceiro através do seu sistema. Em resposta, cria-se um túnel de eventos para o espaço de nome do seu parceiro.
1. A Event Grid cria um tópico de parceiro **pendente** no grupo de subscrição e recursos Azure do cliente.

    ![Criar um canal de eventos](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. O cliente ativa o tópico do parceiro através do portal Azure. Os eventos podem agora fluir do seu serviço para a subscrição Azure do cliente.

    ![Ativar um tópico de parceiro](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Modelo de recursos
O modelo de recursos a seguir é para Eventos Parceiros.

### <a name="partner-registrations"></a>Inscrições de parceiros
* Recurso: `partnerRegistrations`
* Usado por: Parceiros
* Descrição: Captura os metadados globais do software como parceiro de serviço (SaaS) (por exemplo, nome, nome de exibição, descrição, configuração URI).
    
    Criar ou atualizar um registo de parceiros é uma operação de autosserviço para os parceiros. Esta capacidade de autosserviço permite aos parceiros construir e testar o fluxo completo de ponta a ponta.
    
    Apenas as inscrições de parceiros aprovadas pela Microsoft são detetáveis pelos clientes.
* Âmbito: Criado na subscrição Azure do parceiro. Os metadados são visíveis para os clientes depois de tornados públicos.

### <a name="partner-namespaces"></a>Espaços de nome de parceiros
* Recurso: `partnerNamespaces`
* Usado por: Parceiros
* Descrição: Fornece um recurso regional para a publicação de eventos de clientes para. Cada espaço com nomes de parceiros tem um ponto final de publicação e teclas auth. O espaço de nome é também como o parceiro solicita um tópico de parceiro para um determinado cliente e lista clientes ativos.
* Âmbito: Vive na assinatura do parceiro.

### <a name="event-channel"></a>Canal de eventos
* Recurso: `partnerNamespaces/eventChannels`
* Usado por: Parceiros
* Descrição: Os canais do evento são um espelho do tópico parceiro do cliente. Ao criar um canal de eventos e especificar o grupo de subscrição e recursos Azure do cliente nos metadados, você sinaliza para a Grade de Eventos para criar um tópico de parceiro para o cliente. A Grade de Eventos emite uma chamada do Azure Resource Manager para criar um tópico de parceiro correspondente na subscrição do cliente. O tópico parceiro é criado num estado pendente. Há uma ligação um-para-um entre cada canal de evento e o tópico do parceiro.
* Âmbito: Vive na assinatura do parceiro.

### <a name="partner-topics"></a>Tópicos para parceiros
* Recurso: `partnerTopics`
* Usado por: Clientes
* Descrição: Os tópicos dos parceiros são semelhantes aos tópicos personalizados e tópicos do sistema na Grade de Eventos. Cada tópico parceiro está associado a uma fonte específica (por exemplo, `Contoso:myaccount` ) e a um tipo de tópico de parceiro específico (por exemplo, Contoso). Os clientes criam subscrições de eventos sobre o tópico do parceiro para encaminhar eventos para vários manipuladores de eventos.

    Os clientes não podem criar diretamente este recurso. A única forma de criar um tópico de parceiro é através de uma operação parceira que cria um canal de eventos.
* Âmbito: Vive na subscrição do cliente.

### <a name="partner-topic-types"></a>Tipos de tópicos de parceiros
* Recurso: `partnerTopicTypes`
* Usado por: Clientes
* Descrição: Os tipos de tópicos de parceiro são tipos de recursos de todo o inquilino que permitem aos clientes descobrir a lista de tipos de tópicos de parceiros aprovados. A URL parece https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Âmbito: Global

## <a name="publish-events-to-event-grid"></a>Publicar eventos para a Grade de Eventos
Quando cria um espaço de nome de parceiro numa região de Azure, obtém-se um ponto final regional e chaves auth correspondentes. Publique lotes de eventos neste ponto final para todos os canais de eventos do cliente nesse espaço de nome. Com base no campo de origem do evento, a Azure Event Grid mapeia cada evento com os tópicos parceiros correspondentes.

### <a name="event-schema-cloudevents-v10"></a>Esquema de eventos: CloudEvents v1.0
Publique eventos para a Azure Event Grid utilizando o esquema CloudEvents 1.0. O Event Grid suporta o modo estruturado e o modo em massa. CloudEvents 1.0 é o único esquema de evento suportado para espaços com nomes de parceiros.

### <a name="example-flow"></a>Fluxo de exemplos

1.  O serviço editorial faz um HTTP POST para `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .
1.  No pedido, inclua um valor de cabeçalho denominado aeg-sas-key que contém uma chave para a autenticação. Esta chave é prevista durante a criação do espaço de nome do parceiro. Por exemplo, um valor de cabeçalho válido é a chave aeg-sas: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg=.
1.  Desave o cabeçalho do tipo de conteúdo para "aplicação/eventos de nuvens+json; charset=UTF-8a".
1.  Execute uma consulta HTTP POST para o URL de publicação com um lote de eventos que correspondem a essa região. Por exemplo:

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

Depois de publicar no ponto final do espaço de nome do parceiro, recebe uma resposta. A resposta é um código de resposta HTTP padrão. Algumas respostas comuns são:

| Resultado                             | Resposta              |
|------------------------------------|-----------------------|
| Com êxito                            | 200 OK                |
| Os dados do evento têm formato incorreto    | 400 Pedido Incorreto       |
| Chave de acesso inválida                 | 401 Não Autorizado      |
| Ponto final incorreto                 | 404 Não Encontrado         |
| Matriz ou evento excede limites de tamanho | 413 Carga Útil Demasiado Grande |

## <a name="references"></a>Referências

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [Modelo ARM](/azure/templates/microsoft.eventgrid/allversions)
  * [Esquema de modelo de ARM](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [APIs REST](/azure/templates/microsoft.eventgrid/2020-04-01-preview/partnernamespaces)
  * [Extensão CLI](/cli/azure/)

### <a name="sdks"></a>SDKs
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Ir](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Passos seguintes
- [Visão geral dos tópicos do parceiro](partner-events-overview.md)
- [Formulário de tópicos de parceiros no formato de embarque](https://aka.ms/gridpartnerform)
- [Tópico parceiro Auth0](auth0-overview.md)
- [Como usar o tópico parceiro Auth0](auth0-how-to.md)
