---
title: Recuperação de desastres para tópicos personalizados na Grelha de Eventos De Azure
description: Este tutorial irá acompanhá-lo através de como configurar a sua arquitetura de eventos para recuperar se o serviço Da Grelha de Eventos se tornar insalubre numa região.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 87f8f79e2cf125fa5735653153d8fcaa781f5200
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76511523"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Construa a sua própria recuperação de desastres para tópicos personalizados em Event Grid
A recuperação de desastres centra-se na recuperação de uma grave perda da funcionalidade de aplicação. Este tutorial irá acompanhá-lo através de como configurar a sua arquitetura de eventos para recuperar se o serviço Da Grelha de Eventos se tornar insalubre numa determinada região.

Neste tutorial, você vai aprender a criar uma arquitetura de failover passivo ativo para tópicos personalizados em Event Grid. Você vai realizar o failover espelhando os seus tópicos e subscrições em duas regiões e, em seguida, gerindo uma falha quando um tópico torna-se insalubre. A arquitetura neste tutorial falha em todo o novo tráfego. é importante estar ciente, com esta configuração, que os eventos já em voo não serão recuperados até que a região comprometida esteja novamente saudável.

> [!NOTE]
> A Rede de Eventos suporta a recuperação automática de desastres geográficos (GeoDR) no lado do servidor agora. Ainda pode implementar a lógica de recuperação de desastres do lado do cliente se quiser um maior controlo sobre o processo de failover. Para mais detalhes sobre geoDR automático, consulte a recuperação de [desastres geográficos do lado do Servidor na Grelha de Eventos Azure](geo-disaster-recovery.md).

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Para testar a sua configuração de failover, você precisará de um ponto final para receber os seus eventos em. O ponto final não faz parte da sua infraestrutura de failover, mas funcionará como o nosso manipulador de eventos para facilitar o teste.

Para simplificar os testes, implemente uma [aplicação web pré-construída](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens do evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`
Certifique-se de que nota este URL como vai precisar mais tarde.

1. Vê o site, mas ainda não foram publicados eventos no mesmo.

   ![Ver novo site](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>Crie os seus tópicos primários e secundários

Primeiro, crie dois tópicos da Grelha de Eventos. Estes tópicos serão os seus primários e secundários. Por defeito, os seus eventos irão fluir através do seu tópico principal. Se houver uma interrupção de serviço na região primária, o seu secundário assumirá o seu lugar.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. A partir do canto superior esquerdo do menu Principal Azure, escolha **Todos os serviços** > pesquisa por **Event Grid** > selecione Tópicos da Grelha de **Eventos**.

   ![Menu tópicos da grelha de eventos](./media/custom-disaster-recovery/select-topics-menu.png)

    Selecione a estrela ao lado dos Tópicos da Grelha de Eventos para adicioná-la ao menu de recursos para facilitar o acesso no futuro.

1. No Menu tópicos da Grelha de Eventos, selecione **+ADD** para criar o seu tópico principal.

   * Dê ao tópico um nome lógico e adicione "-primário" como um sufixo para facilitar o rastreio.
   * A região deste tema será a sua região primária.

     ![Tópico de Evento saem para o diálogo](./media/custom-disaster-recovery/create-primary-topic.png)

1. Uma vez criado o Tópico, navegue até ele e copie o **Ponto Final**tópico . Vai precisar do URI mais tarde.

    ![Tópico Primário da Grelha de Eventos](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. Obtenha a chave de acesso para o tópico, que também precisará mais tarde. Clique nas **teclas De acesso** no menu de recursos e copie a Chave 1.

    ![Obter chave de tópico primário](./media/custom-disaster-recovery/get-primary-access-key.png)

1. Na lâmina Tópico, clique em **+Assinatura de Evento** para criar uma subscrição que ligue a sua subscrição do site recetor de eventos que fez nos pré-requisitos para o tutorial.

   * Dê à subscrição do evento um nome lógico e adicione "-primary" como um sufixo para facilitar o rastreio.
   * Selecione Endpoint Type Type Web Hook.
   * Delineie o ponto final para o URL do seu recetor de eventos, que deve ser algo parecido com:`https://<your-event-reciever>.azurewebsites.net/api/updates`

     ![Assinatura do evento grid primary event](./media/custom-disaster-recovery/create-primary-es.png)

1. Repita o mesmo fluxo para criar o seu tópico secundário e subscrição. Desta vez, substitua o sufixo "primário" por "secundário" para um rastreio mais fácil. Por fim, certifique-se de colocá-lo numa região de Azure diferente. Enquanto pode colocá-lo em qualquer lugar que quiser, é aconselhável que use as [Regiões Emparelhadas Azure](../best-practices-availability-paired-regions.md). Colocar o tema secundário e a subscrição numa região diferente garante que os seus novos eventos irão fluir mesmo que a região primária desça.

Agora devia ter:

   * Um site recetor de eventos para testes.
   * Um tema primário na sua região primária.
   * Uma subscrição primária de eventos que liga o seu tópico principal ao site do recetor do evento.
   * Um tema secundário na sua região secundária.
   * Uma subscrição secundária de eventos que liga o seu tópico principal ao site recetor do evento.

## <a name="implement-client-side-failover"></a>Implementar aplicação pós-falha do lado do cliente

Agora que tem um par de tópicos e subscrições regionalmente redundantes, está pronto para implementar o fracasso do lado do cliente. Existem várias formas de o concretizar, mas todas as implementações de failover terão uma característica comum: se um tópico já não for saudável, o tráfego redirecionar-se-á para o outro tema.

### <a name="basic-client-side-implementation"></a>Implementação básica do lado do cliente

O seguinte código de amostra é um simples editor .NET que tentará sempre publicar primeiro o seu tópico principal. Se não for bem sucedido, falhará o tema secundário. Em qualquer dos casos, também verifica a api de `https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health`saúde do outro tema fazendo um GET on . Um tópico saudável deve sempre responder com **200 OK** quando um GET é feito no ponto final **/api/saúde.**

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

Agora que tem todos os seus componentes no lugar, pode testar a sua implementação de falhas. Execute a amostra acima no código do Estúdio Visual, ou o seu ambiente favorito. Substitua os seguintes quatro valores com os pontos finais e chaves dos seus tópicos:

   * primaryTopic - o ponto final para o seu tópico principal.
   * secundárioTópico - o ponto final para o seu tópico secundário.
   * primaryTopicKey - a chave para o seu tópico principal.
   * secondaryTopicKey - a chave para o seu tópico secundário.

Tente dirigir o editor do evento. Você deve ver os seus eventos de teste aterrar em seu espectador de Event Grid como abaixo.

![Assinatura do evento grid primary event](./media/custom-disaster-recovery/event-grid-viewer.png)

Para se certificar de que a sua falha está a funcionar, pode alterar alguns caracteres na sua chave de tópico principal para que deixe de ser válido. Tente dirigir o editor de novo. Ainda deve ver novos eventos aparecerem no seu espectador de Event Grid, no entanto, quando olhar para a sua consola, verá que estão agora a ser publicados através do tema secundário.

### <a name="possible-extensions"></a>Possíveis extensões

Há muitas maneiras de estender esta amostra com base nas suas necessidades. Para cenários de grande volume, é possível verificar regularmente a api de saúde do tema de forma independente. Assim, se um tópico fosse para baixo, não precisa verificar com todas as publicações. Assim que souberque um tópico não é saudável, pode não publicar o tema secundário.

Da mesma forma, pode querer implementar uma lógica de retrocesso com base nas suas necessidades específicas. Se a publicação no centro de dados mais próximo for fundamental para reduzir a latência, pode periodicamente sondar a api sanitária de um tópico que falhou. Uma vez saudável novamente, saberá que é seguro voltar ao centro de dados mais próximo.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [receber eventos num ponto final http](./receive-events.md)
- Descubra como [encaminhar eventos para Ligações Híbridas](./custom-event-to-hybrid-connection.md)
- Saiba mais sobre recuperação de [desastres usando DNS Azure e Gestor de Tráfego](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)
