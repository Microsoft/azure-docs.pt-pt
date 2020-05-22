---
title: A bordo como parceiro azure event grid
description: A bordo como um tipo de tópico de parceiro de grelha de eventos azure. Compreenda o modelo de recursos e o fluxo de publicação para Tópicos de Parceiros.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 2a1f35b86e21099c9fdd0397ae8a3b20aed3cd5d
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758831"
---
# <a name="onboard-as-an-azure-event-grid-partner"></a>A bordo como parceiro azure event grid

Este artigo descreve como usar privadamente os recursos parceiros da Event Grid e como se tornar um tipo de tópico de parceiro disponível publicamente.

Não precisa de autorização especial para começar a usar os tipos de recursos da Rede de Eventos associados a eventos editoriais como parceiro da Rede de Eventos. Na verdade, você pode usá-los hoje tanto para publicar eventos em particular para suas próprias Assinaturas Azure, e para testar o modelo de recursos se você estiver considerando se se tornar um parceiro.

## <a name="becoming-an-event-grid-partner"></a>Tornando-se um parceiro da Grelha de Eventos

Se você está interessado em se tornar um parceiro público da Grelha de Eventos, comece por preencher [este formulário](https://aka.ms/gridpartnerform), e depois contacte a equipe da Rede de Eventos em [GridPartner@microsoft.com](mailto:gridpartner@microsoft.com) .

## <a name="how-partner-topics-work"></a>Como os tópicos dos parceiros funcionam
Os Tópicos parceiros tomam a arquitetura existente que a Event Grid já utiliza para publicar eventos a partir de recursos do Azure, como O Armazenamento e O Hub IoT, e disponibiliza essas ferramentas publicamente para qualquer pessoa usar. A utilização destas ferramentas é, por padrão, privada apenas para a sua Subscrição Azure. Para disponibilizar os seus eventos publicamente, preencha o formulário acima e [contacte a equipa da Rede de Eventos.](mailto:gridpartner@microsoft.com)

Os tópicos dos parceiros permitem-lhe publicar eventos na Azure Event Grid para consumo multiarrendatário.

### <a name="onboarding-and-event-publishing-overview"></a>Visão geral de publicação de onboarding e eventos

#### <a name="partner-flow"></a>Fluxo de parceiros

1. Crie um inquilino Azure se ainda não tiver um.
1. A utilização do CLI cria uma nova Grelha de `partnerRegistration` Eventos. Este recurso inclui informações como nome de exibição, descrição, configuração URI e assim por diante.

    ![Criar tópico de parceiro](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Crie um ou mais `partnerNamespaces` em cada região que queira publicar eventos. Como parte deste, o serviço Event Grid fornecerá um ponto final de publicação (por exemplo, e chaves de `https://contoso.westus-1.eventgrid.azure.net/api/events` acesso.

    ![Criar espaço de nome de parceiro](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Forneça uma forma de os clientes se registarem no seu sistema que gostariam de ter um tópico de parceiro.
1. Contacte a equipa da Rede de Eventos para nos informar que gostaria que o seu Tipo de Tópico de Parceiro se tornasse público.

#### <a name="customer-flow"></a>Fluxo de cliente

1. O seu cliente visitará o portal Azure para observar o ID de Subscrição Azure e o Grupo de Recursos em que gostariam que o Tópico de Parceiro sintetizasse.
1. O cliente solicitará um Tópico de Parceiro através do seu sistema. Em resposta, você vai criar um Túnel de Eventos o seu Espaço nome do parceiro.
1. A Event Grid criará um Tópico de Parceiro **Pendente** no Grupo de Subscrição e Recursos Azure do cliente.

    ![Criar canal de eventos](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. O cliente ativa o Tópico do Parceiro através do portal Azure. Os eventos podem agora fluir do seu serviço para a Subscrição Azure do cliente.

    ![Ativar tópico de parceiro](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Modelo de Recursos

Abaixo está o modelo de recurso para Tópicos de Parceiros.

### <a name="partner-registrations"></a>Registos de Parceiros
* Recurso:`partnerRegistrations`
* Usado por: Parceiros
* Descrição: Captura os metadados globais do parceiro SaaS (por exemplo, nome, nome de exibição, descrição, configuração URI).
    
    Criar/atualizar o registo de um parceiro é uma operação de autosserviço para os parceiros. Esta capacidade de autosserviço permite aos parceiros construir e testar o fluxo final completo.
    
    Apenas os parceiros aprovados pela MicrosoftOs registos são detetáveis pelos clientes.
* Âmbito: Criado na subscrição azure do parceiro. Metadados visíveis para os clientes uma vez públicos.

### <a name="partner-namespaces"></a>Espaços de Nome de Parceiro
* Recurso: partnerNamespaces
* Usado por: Parceiros
* Descrição: Fornece um recurso regional para a publicação de eventos de clientes para. Cada Partner Namespace tem um ponto final de publicação e chaves auth. O espaço de nome é também como o parceiro solicita um Tópico de Parceiro para um determinado cliente e lista clientes ativos.
* Âmbito: Vive na subscrição do parceiro.

### <a name="event-channel"></a>Canal de Eventos
* Recurso:`partnerNamespaces/eventChannels`
* Usado por: Parceiros
* Descrição: Os Túneis de Eventos são um espelho do Tópico do Parceiro do cliente. Ao criar um Túnel de Eventos e especificar o Grupo de Subscrição e Recursos Azure do cliente nos metadados, está a sinalizar para a Rede de Eventos para criar um Tópico de Parceiro para o cliente. A Event Grid emitirá uma chamada DAA para criar um parceiro correspondenteTopic na subscrição do cliente. O tema do parceiro será criado num estado "pendente". Há uma ligação 1-1 entre cada eventoTunnel e um partnerTopic.
* Âmbito: Vive na subscrição do parceiro.

### <a name="partner-topics"></a>Tópicos de Parceiros
* Recurso:`partnerTopics`
* Usado por: Clientes
* Descrição: Os tópicos do parceiro são semelhantes ao tópico personalizado e tópico do sistema na Grelha de Eventos. Cada Tópico de Parceiro está associado a uma "fonte" específica (por exemplo, ) e a um tipo específico de tópico de `Contoso:myaccount` parceiro (por exemplo, "Contoso"). Os clientes criam Subscrições de Eventos no Tópico do Parceiro para direcionar eventos para vários manipuladores de eventos.

    Os clientes não podem criar diretamente este recurso. A única maneira de criar um Tópico de Parceiro é através de uma operação parceira criando um Túnel de Eventos.
* Âmbito: Vive na subscrição do cliente.

### <a name="partner-topic-types"></a>Tipos de tópicos de parceiro
* Recurso:`partnerTopicTypes`
* Usado por: Clientes
* Descrição: Os Tipos de Tópicos de Parceiro são tipos de recursos para o arrendamento que permitem aos clientes descobrir a lista de tipos de tópicos de parceiros aprovados. O URL será o seguinte:https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Âmbito: Global

## <a name="publishing-events-to-event-grid"></a>Publicar eventos para grelha de eventos
Quando criar um partnerNamespace numa região do Azure, terá um ponto final regional e chaves auth correspondentes. Publique lotes de eventos para este ponto final para todos os túneis de eventos do cliente nesse espaço de nome. Com base no campo "fonte" do evento, a Azure Event Grid irá mapear cada evento com o ou o tópico correspondente do parceiro.

### <a name="event-schema-cloudevents-v10"></a>Evento Schema: CloudEvents v1.0
Publique eventos para Azure Event Grid utilizando o esquema CloudEvents 1.0. A Grelha de Eventos suporta o modo estruturado e o modo em lotado. CloudEvents 1.0 é o único esquema de evento suportado para espaços de nome de parceiro.

### <a name="example-flow"></a>Fluxo de exemplo

1.  O serviço editorial faz um HTTP POST para `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .
2.  No pedido, inclua um valor cabeçalho denominado aeg-sas-key que contém uma chave para autenticação. Esta chave é disponibilizada durante a criação do partnerNamespace. Por exemplo, um valor de cabeçalho válido é aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==.
3.  Definir o cabeçalho do Tipo conteúdo para "application/cloudevents-batch+json; charset=UTF-8".
4.  Execute uma PUBLICAÇÃO HTTP para o URL de publicação acima com um lote de eventos correspondentes a essa região. Por exemplo:

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

Depois de publicar no ponto final do partnerNamespace, receberá uma resposta. A resposta é um código de resposta HTTP padrão. Algumas respostas comuns são:

| Resultado                             | Resposta              |
|------------------------------------|-----------------------|
| Êxito                            | 200 OK                |
| Os dados do evento têm formato incorreto    | 400 Mau Pedido       |
| Chave de acesso inválida                 | 401 Não autorizado      |
| Ponto final incorreto                 | 404 Não Encontrado         |
| Matriz ou evento excede limites de tamanho | 413 Carga útil demasiado grande |

## <a name="reference"></a>Referência

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [Modelo de braço](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)
  * [Esquema do modelo arm](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [APIs REST](https://docs.microsoft.com/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [Extensão CLI](https://docs.microsoft.com/cli/azure/ext/eventgrid/?view=azure-cli-latest)

### <a name="sdks"></a>SDKs
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Ir](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Passos Seguintes
- [Visão geral dos tópicos dos parceiros](partner-topics-overview.md)
- [Formulário de onboarding tópicos de parceiro](https://aka.ms/gridpartnerform)
- [Tópico parceiro auth0](auth0-overview.md)
- [Como usar o Tópico do Parceiro Auth0](auth0-how-to.md)
