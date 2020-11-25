---
title: Recuperação de desastres para tópicos personalizados na Grelha de Eventos do Azure
description: Este tutorial irá acompanhá-lo como configurar a sua arquitetura de eventos para recuperar se o serviço de Grade de Eventos se tornar insalubre numa região.
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e37cb6a0679ee2e249de4ed8fa31c40d5082ea4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020148"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Construa a sua própria recuperação de desastres para tópicos personalizados na Grade de Eventos
A recuperação de desastres centra-se na recuperação de uma grave perda de funcionalidade de aplicação. Este tutorial irá acompanhá-lo como configurar a sua arquitetura de eventos para recuperar se o serviço de Grade de Eventos se tornar insalubre numa determinada região.

Neste tutorial, você aprenderá a criar uma arquitetura de failover ativa-passiva para tópicos personalizados em Event Grid. Você vai realizar failover espelhando seus tópicos e subscrições em duas regiões e, em seguida, gerir um failover quando um tópico se torna insalubre. A arquitetura neste tutorial falha em todo o tráfego novo. é importante estar ciente, com esta configuração, os eventos já em voo não serão recuperados até que a região comprometida esteja novamente saudável.

> [!NOTE]
> A Grelha de Eventos suporta a recuperação automática de geo-desastres (GeoDR) no lado do servidor agora. Você ainda pode implementar a lógica de recuperação de desastres do lado do cliente se você quiser um maior controle sobre o processo de failover. Para obter mais informações sobre o GeoDR automático, consulte [a recuperação de geo-desastres do lado do Servidor na Grelha de Eventos Azure](geo-disaster-recovery.md).

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Para testar a sua configuração de failover, precisará de um ponto final para receber os seus eventos. O ponto final não faz parte da sua infraestrutura de failover, mas funcionará como o nosso manipulador de eventos para facilitar o teste.

Para simplificar os testes, implemente uma [aplicação web pré-construída](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens do evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Aquent." /></a>

1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`
Certifique-se de que nota este URL como vai precisar mais tarde.

1. Vê o site, mas ainda não foram publicados eventos no mesmo.

   ![Ver novo site](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>Crie os seus tópicos primários e secundários

Primeiro, crie dois tópicos de Grade de Eventos. Estes tópicos funcionarão como o seu primário e secundário. Por padrão, os seus eventos fluirão através do seu tópico principal. Se houver uma falha de serviço na região primária, o seu secundário assumirá o lugar.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. A partir do canto superior esquerdo do menu Azure principal, escolha **Todos os serviços** > procurar por **Event Grid** > selecione **Tópicos de Grelha de Eventos**.

   ![Menu tópicos da grelha de eventos](./media/custom-disaster-recovery/select-topics-menu.png)

    Selecione a estrela ao lado de Tópicos de Grelha de Eventos para adicioná-lo ao menu de recursos para um acesso mais fácil no futuro.

1. No Menu Tópicos da Grelha de Eventos, selecione **+ADD** para criar o seu tópico principal.

   * Dê ao tópico um nome lógico e adicione "primário" como um sufixo para facilitar a sua rastreação.
   * A região deste tópico será a sua região primária.

     ![Tópico de Grelha de Eventos primário criar diálogo](./media/custom-disaster-recovery/create-primary-topic.png)

1. Uma vez criado o tópico, navegue para ele e copie o **Ponto Final tópico**. Vai precisar do URI mais tarde.

    ![Tópico primário da grelha de evento](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. Obtenha a chave de acesso para o tópico, que também precisará mais tarde. Clique nas **teclas de acesso** no menu de recursos e copie a Chave 1.

    ![Obtenha a chave de tópico primário](./media/custom-disaster-recovery/get-primary-access-key.png)

1. Na lâmina tópico, clique em **+Subscrição de Evento** para criar uma subscrição que ligue a subscrição do site do recetor de eventos que escoou nos pré-requisitos para o tutorial.

   * Dê à subscrição do evento um nome lógico e adicione "primário" como um sufixo para facilitar a sua rastreação.
   * Selecione o gancho web do tipo ponto final.
   * Desaponte o ponto final para o URL do seu recetor de eventos, que deve parecer algo como: `https://<your-event-reciever>.azurewebsites.net/api/updates`

     ![Screenshot que mostra a página "Create Event Subscription - Basic" com os valores "Name", "Endpoint Type" e "Endpoint" em destaque.](./media/custom-disaster-recovery/create-primary-es.png)

1. Repita o mesmo fluxo para criar o seu tópico secundário e subscrição. Desta vez, substitua o sufixo "primário" por "secundário" para facilitar o rastreio. Finalmente, certifique-se de colocá-lo em uma região de Azure diferente. Enquanto pode colocá-lo onde quiser, recomenda-se que use as [Regiões Emparelhadas Azure.](../best-practices-availability-paired-regions.md) Colocar o tema secundário e a subscrição numa região diferente garante que os seus novos eventos fluirão mesmo que a região primária desça.

Deveria agora ter:

   * Um site de recetor de eventos para testes.
   * Um tema primário na sua região primária.
   * Uma subscrição de evento primário que liga o seu tópico principal ao site do recetor do evento.
   * Um tema secundário na sua região secundária.
   * Uma subscrição de eventos secundários que liga o seu tópico principal ao site do recetor do evento.

## <a name="implement-client-side-failover"></a>Implementar aplicação pós-falha do lado do cliente

Agora que tem um par de tópicos e subscrições regionalmente redundantes, está pronto para implementar o failover do lado do cliente. Existem várias formas de o conseguir, mas todas as implementações de failover terão uma característica comum: se um tópico deixar de ser saudável, o tráfego irá redirecionar para o outro tópico.

### <a name="basic-client-side-implementation"></a>Implementação básica do lado do cliente

O seguinte código de amostra é um simples editor .NET que tentará sempre publicar primeiro o seu tópico principal. Se não for bem sucedido, falhará no tema secundário. Em qualquer dos casos, também verifica a api de saúde do outro tópico, fazendo um GET on `https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health` . Um tópico saudável deve sempre responder com **200 OK** quando um GET é feito no ponto final **/api/health.**

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;
using Newtonsoft.Json;

namespace EventGridFailoverPublisher
{
    // This captures the "Data" portion of an EventGridEvent on a custom topic
    class FailoverEventData
    {
        [JsonProperty(PropertyName = "teststatus")]
        public string TestStatus { get; set; }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // TODO: Enter the endpoint each topic. You can find this topic endpoint value
            // in the "Overview" section in the "Event Grid Topics" blade in Azure Portal..
            string primaryTopic = "https://<primary-topic-name>.<primary-topic-region>.eventgrid.azure.net/api/events";
            string secondaryTopic = "https://<secondary-topic-name>.<secondary-topic-region>.eventgrid.azure.net/api/events";

            // TODO: Enter topic key for each topic. You can find this in the "Access Keys" section in the
            // "Event Grid Topics" blade in Azure Portal.
            string primaryTopicKey = "<your-primary-topic-key>";
            string secondaryTopicKey = "<your-secondary-topic-key>";

            string primaryTopicHostname = new Uri( primaryTopic).Host;
            string secondaryTopicHostname = new Uri(secondaryTopic).Host;

            Uri primaryTopicHealthProbe = new Uri("https://" + primaryTopicHostname + "/api/health");
            Uri secondaryTopicHealthProbe = new Uri("https://" + secondaryTopicHostname + "/api/health");

            var httpClient = new HttpClient();

            try
            {
                TopicCredentials topicCredentials = new TopicCredentials(primaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(primaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to primary Event Grid topic.");

                HttpResponseMessage health = httpClient.GetAsync(secondaryTopicHealthProbe).Result;
                Console.Write("\n\nSecondary Topic health " + health);
            }
            catch (Microsoft.Rest.Azure.CloudException e)
            {
                TopicCredentials topicCredentials = new TopicCredentials(secondaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(secondaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to secondary Event Grid topic. Reason for primary topic failure:\n\n" + e);

                HttpResponseMessage health = httpClient.GetAsync(primaryTopicHealthProbe).Result;
                Console.Write("\n\nPrimary Topic health " + health);
            }

            Console.ReadLine();
        }

        static IList<EventGridEvent> GetEventsList()
        {
            List<EventGridEvent> eventsList = new List<EventGridEvent>();

            for (int i = 0; i < 5; i++)
            {
                eventsList.Add(new EventGridEvent()
                {
                    Id = Guid.NewGuid().ToString(),
                    EventType = "Contoso.Failover.Test",
                    Data = new FailoverEventData()
                    {
                        TestStatus = "success"
                    },
                    EventTime = DateTime.Now,
                    Subject = "test" + i,
                    DataVersion = "2.0"
                });
            }

            return eventsList;
        }
    }
}
```

### <a name="try-it-out"></a>Experimente

Agora que tem todos os seus componentes no lugar, pode testar a sua implementação de failover. Execute a amostra acima no código Visual Studio, ou no seu ambiente favorito. Substitua os seguintes quatro valores pelos pontos finais e teclas dos seus tópicos:

   * primaryTopic - o ponto final para o seu tópico principal.
   * secondaryTopic - o ponto final para o seu tópico secundário.
   * primaryTopicKey - a chave para o seu tópico principal.
   * secondaryTopicKey - a chave para o seu tópico secundário.

Tente dirigir a editora do evento. Você deve ver seus eventos de teste pousar no seu espectador de Event Grid como abaixo.

![Assinatura do evento principal da grelha de evento](./media/custom-disaster-recovery/event-grid-viewer.png)

Para garantir que o seu fracasso está a funcionar, pode alterar alguns caracteres na sua chave principal para que deixe de ser válido. Tente dirigir o editor de novo. Ainda assim, deverás ver novos eventos a aparecer no teu espectador de Event Grid, no entanto, quando olhares para a tua consola, verás que estão agora a ser publicados através do tema secundário.

### <a name="possible-extensions"></a>Possíveis extensões

Há muitas maneiras de estender esta amostra com base nas suas necessidades. Para cenários de grande volume, pode querer verificar regularmente a api de saúde do tópico de forma independente. Assim, se um tópico fosse para baixo, não precisa verificar com cada publicação. Uma vez que você sabe que um tópico não é saudável, você pode por defeito publicar para o tópico secundário.

Da mesma forma, pode querer implementar uma lógica de failback baseada nas suas necessidades específicas. Se a publicação no centro de dados mais próximo é fundamental para reduzir a latência, pode sondar periodicamente a api de saúde de um tópico que falhou. Uma vez saudável de novo, saberá que é seguro falhar no centro de dados mais próximo.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [receber eventos num ponto final http](./receive-events.md)
- Descubra como [encaminhar eventos para Conexões Híbridas](./custom-event-to-hybrid-connection.md)
- Saiba mais sobre [a recuperação de desastres usando Azure DNS e Traffic Manager](../networking/disaster-recovery-dns-traffic-manager.md)
