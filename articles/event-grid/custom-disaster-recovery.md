---
title: Recuperação de desastre para tópicos personalizados na grade de eventos do Azure
description: Saiba como sobreviver a interrupções regionais para manter a grade de eventos do Azure conectada.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: babanisa
ms.openlocfilehash: 7020fb167539e8ad16cc6c386f58e38326dec43b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790282"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Crie sua própria recuperação de desastre para tópicos personalizados na grade de eventos
A recuperação de desastres se concentra em recuperar-se de uma perda grave de funcionalidade do aplicativo. Este tutorial explicará como configurar sua arquitetura de eventos para recuperar se o serviço de grade de eventos se tornar não íntegro em uma região específica.

Neste tutorial, você aprenderá a criar uma arquitetura de failover ativo-passivo para tópicos personalizados na grade de eventos. Você realizará o failover espelhando seus tópicos e assinaturas em duas regiões e, em seguida, gerenciando um failover quando um tópico se tornar não íntegro. A arquitetura neste tutorial faz failover de todo o tráfego novo. é importante estar ciente de que, com essa configuração, os eventos que já estão em voo não serão recuperados até que a região comprometida esteja íntegra novamente.

> [!NOTE]
> A grade de eventos dá suporte à GeoDR (recuperação de desastre geográfica automática) no lado do servidor agora. Você ainda pode implementar a lógica de recuperação de desastre do lado do cliente se quiser um maior controle sobre o processo de failover. Para obter detalhes sobre o GeoDR automático, consulte [recuperação de desastre geográfica no lado do servidor na grade de eventos do Azure](geo-disaster-recovery.md).

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Para testar sua configuração de failover, você precisará de um ponto de extremidade para receber seus eventos em. O ponto de extremidade não faz parte da sua infraestrutura de failover, mas atuará como nosso manipulador de eventos para facilitar o teste.

Para simplificar o teste, implante um [aplicativo Web predefinido](https://github.com/Azure-Samples/azure-event-grid-viewer) que exiba as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`
Certifique-se de anotar essa URL, pois você precisará dela mais tarde.

1. Vê o site, mas ainda não foram publicados eventos no mesmo.

   ![Ver novo site](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>Crie seus tópicos primários e secundários

Primeiro, crie dois tópicos de grade de eventos. Esses tópicos funcionarão como primário e secundário. Por padrão, seus eventos fluirão pelo seu tópico primário. Se houver uma interrupção de serviço na região primária, o secundário assumirá.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. No canto superior esquerdo do menu principal do Azure, escolha **todos os serviços** > Pesquisar a **grade de eventos** > selecionar **Tópicos da grade de eventos**.

   ![Menu de tópicos da grade de eventos](./media/custom-disaster-recovery/select-topics-menu.png)

    Selecione a estrela ao lado dos tópicos da grade de eventos para adicioná-lo ao menu de recursos para facilitar o acesso no futuro.

1. No menu tópicos da grade de eventos, selecione **+ Adicionar** para criar seu tópico primário.

   * Dê ao tópico um nome lógico e adicione "-Primary" como um sufixo para facilitar o rastreamento.
   * A região deste tópico será sua região primária.

     ![Caixa de diálogo criação primária do tópico da grade de eventos](./media/custom-disaster-recovery/create-primary-topic.png)

1. Depois que o tópico tiver sido criado, navegue até ele e copie o **ponto de extremidade do tópico**. Você precisará do URI mais tarde.

    ![Tópico primário da grade de eventos](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. Obtenha a chave de acesso para o tópico, que você também precisará mais tarde. Clique em **chaves de acesso** no menu de recursos e copie a chave 1.

    ![Obter chave do tópico primário](./media/custom-disaster-recovery/get-primary-access-key.png)

1. Na folha do tópico, clique em **+ assinatura de evento** para criar uma assinatura que conecta seu assinante do site Receptor de eventos que você fez nos pré-requisitos para o tutorial.

   * Dê à assinatura do evento um nome lógico e adicione "-Primary" como um sufixo para facilitar o rastreamento.
   * Selecione o tipo de ponto de extremidade Web Hook.
   * Defina o ponto de extremidade para a URL de evento do receptor de evento, que deve ser semelhante a: `https://<your-event-reciever>.azurewebsites.net/api/updates`

     ![Assinatura de evento principal da grade de eventos](./media/custom-disaster-recovery/create-primary-es.png)

1. Repita o mesmo fluxo para criar seu tópico secundário e sua assinatura. Desta vez, substitua o sufixo "-Primary" por "-Secondary" para facilitar o acompanhamento. Por fim, certifique-se de colocá-lo em uma região diferente do Azure. Embora você possa colocá-lo em qualquer lugar desejado, é recomendável usar as [regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md). Colocar o tópico secundário e a assinatura em uma região diferente garante que os novos eventos fluirão mesmo se a região primária falhar.

Agora você deve ter:

   * Um site Receptor de eventos para teste.
   * Um tópico primário em sua região primária.
   * Uma assinatura de evento principal conectando seu tópico primário ao site do receptor de eventos.
   * Um tópico secundário em sua região secundária.
   * Uma assinatura de evento secundário conectando seu tópico primário ao site do receptor de eventos.

## <a name="implement-client-side-failover"></a>Implementar aplicação pós-falha do lado do cliente

Agora que você tem um par de tópicos e assinaturas com redundância geográfica, você está pronto para implementar o failover do lado do cliente. Há várias maneiras de fazer isso, mas todas as implementações de failover terão um recurso comum: se um tópico não estiver mais íntegro, o tráfego será redirecionado para o outro tópico.

### <a name="basic-client-side-implementation"></a>Implementação básica do lado do cliente

O código de exemplo a seguir é um editor .NET simples que sempre tentará publicar primeiro seu tópico primário. Se não tiver sucesso, ele executará o failover do tópico secundário. Em ambos os casos, ele também verifica a API de integridade do outro tópico fazendo um GET no `https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health`. Um tópico íntegro deve sempre responder com **200 OK** quando um get é feito no ponto de extremidade **/API/Health** .

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

### <a name="try-it-out"></a>Experimentar

Agora que você tem todos os seus componentes em vigor, você pode testar sua implementação de failover. Execute o exemplo acima no Visual Studio Code ou seu ambiente favorito. Substitua os quatro valores a seguir pelos pontos de extremidade e chaves dos seus tópicos:

   * primaryTopic-o ponto de extremidade para seu tópico primário.
   * secondaryTopic-o ponto de extremidade para seu tópico secundário.
   * primaryTopicKey-a chave para seu tópico primário.
   * secondaryTopicKey-a chave para seu tópico secundário.

Tente executar o editor de eventos. Você deve ver seus eventos de teste no seu visualizador de grade de eventos, como abaixo.

![Assinatura de evento principal da grade de eventos](./media/custom-disaster-recovery/event-grid-viewer.png)

Para verificar se o failover está funcionando, você pode alterar alguns caracteres em sua chave de tópico primário para torná-lo não mais válido. Tente executar o Publicador novamente. Você ainda deve ver novos eventos exibidos no Visualizador de grade de eventos. no entanto, ao examinar seu console, você verá que agora eles estão sendo publicados por meio do tópico secundário.

### <a name="possible-extensions"></a>Extensões possíveis

Há várias maneiras de estender esse exemplo com base em suas necessidades. Para cenários de alto volume, talvez você queira verificar regularmente a API de integridade do tópico de forma independente. Dessa forma, se um tópico fosse desativado, você não precisará verificá-lo com cada publicação única. Depois de saber que um tópico não está íntegro, você pode padronizar a publicação no tópico secundário.

Da mesma forma, talvez você queira implementar a lógica de failback com base em suas necessidades específicas. Se a publicação na data center mais próxima for crítica para reduzir a latência, você poderá investigar periodicamente a API de integridade de um tópico que passou por failover. Quando ele estiver íntegro novamente, você saberá que é seguro fazer failback para o data center mais próximo.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [receber eventos em um ponto de extremidade http](./receive-events.md)
- Descubra como [rotear eventos para conexões híbridas](./custom-event-to-hybrid-connection.md)
- Saiba mais sobre a [recuperação de desastre usando o DNS do Azure e o Gerenciador de tráfego](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)
