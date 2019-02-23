---
title: Criar seu próprio recuperação após desastre para tópicos personalizados no Azure Event Grid | Documentos da Microsoft
description: Sobrevivem a interrupções regionais para manter o Azure Event Grid ligado.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: babanisa
ms.openlocfilehash: a77c208c208ef7e0df170733dbe89963fc5cb846
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727184"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Criar seu próprio recuperação após desastre para tópicos personalizados no Event Grid

Recuperação após desastre se concentra em recuperar de uma grave perda de funcionalidade do aplicativo. Este tutorial explica como configurar a sua arquitetura de eventos para recuperar, se o serviço do Event Grid fica em mau estado de funcionamento numa região específica.

Neste tutorial, irá aprender como criar uma arquitetura de ativação pós-falha do ativa-passiva para tópicos personalizados no Event Grid. Irá realizar a ativação pós-falha ao espelhamento seus tópicos e subscrições em duas regiões e, em seguida, gerir uma ativação pós-falha quando um tópico fica em mau estado de funcionamento. A arquitetura neste tutorial efetua a ativação pós-falha de todo o tráfego de novo. é importante ter em consideração, com esta configuração, a não ser recuperados eventos já em andamento, até que a região comprometida está em bom estada novamente.

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Para testar a configuração de ativação pós-falha, precisará para receber os seus eventos num ponto de extremidade. O ponto final não é parte da sua infraestrutura de ativação pós-falha, mas funcionará como nosso manipulador de eventos para que seja mais fácil de testar.

Para simplificar o teste, implemente um [aplicação web pré-criados](https://github.com/Azure-Samples/azure-event-grid-viewer) que apresenta as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`
Certifique-se observar este URL, à medida que vai precisar dele mais tarde.

1. Vê o site, mas ainda não foram publicados eventos no mesmo.

   ![Ver novo site](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>Criar seus tópicos primários e secundários

Primeiro, crie dois tópicos do Event Grid. Estes tópicos atuará como seu principal e secundário. Por predefinição, os seus eventos irão fluir pelo seu tópico principal. Se houver uma interrupção de serviço na região primária, irá assumir a secundária.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. No canto superior esquerdo do menu principal do Azure, escolha **todos os serviços** > procure **Event Grid** > selecione **tópicos do Event Grid**.

   ![Menu de tópicos de grelha de eventos](./media/custom-disaster-recovery/select-topics-menu.png)

    Selecione a estrela junto tópicos do Event Grid para adicioná-lo ao menu de recursos para facilitar o acesso no futuro.

1. No Menu de tópicos do eventos Grid, selecione **+ adicionar** para criar um tópico principal.

    * Dê um nome lógico ao tópico e adicione "-primário" como sufixo torna mais fácil de controlar.
    * Região deste tópico será sua região primária.

    ![Tópico do Event Grid principal criar caixa de diálogo](./media/custom-disaster-recovery/create-primary-topic.png)

1. Depois do tópico ter sido criado, navegue para o mesmo e copie os **ponto final do tópico**. será necessário o URI mais tarde.

    ![Tópico do Event Grid principal](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. Obtenha a chave de acesso para o tópico, que também será necessário mais tarde. Clique em **chaves de acesso** no menu de recursos e copie chave 1.

    ![Obter a chave primária de tópico](./media/custom-disaster-recovery/get-primary-access-key.png)

1. No painel do tópico, clique em **+ subscrição de evento** para criar uma subscrição ligar-se de que sua assinatura o site do receptor de evento que tomou em pré-requisitos para o tutorial.

    * Dê um nome lógico à subscrição de evento e adicione "-primário" como sufixo torna mais fácil de controlar.
    * Selecione o Hook de Web do tipo de ponto final.
    * Definir o ponto final para o URL de eventos do receptor de eventos, o que deve ser algo como: `https://<your-event-reciever>.azurewebsites.net/api/updates`

    ![Subscrição de eventos primário do Event Grid](./media/custom-disaster-recovery/create-primary-es.png)

1. Repita o mesmo fluxo para criar o seu tópico secundário e a subscrição. Desta vez, substitua o "-primário" sufixo com "-secundário" para o controlo de mais fácil. Por fim, certifique-se de que colocá-la numa região do Azure diferente. Enquanto pode colocá-lo em qualquer lugar que desejar, recomenda-se que utilize o [regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md). Colocar o tópico secundário e a subscrição numa região diferente garante que os seus novos eventos irão fluir, mesmo que a região primária fica inativo.

Agora, deve ter:

   * Um site do receptor de evento para fins de teste.
   * Um tópico principal na sua região primária.
   * Uma subscrição de eventos primário ligar o seu tópico principal para o site do receptor de evento.
   * Um tópico secundário na sua região secundária.
   * Uma subscrição de evento secundário ligar o seu tópico principal para o site do receptor de evento.

## <a name="implement-client-side-failover"></a>Implementar aplicação pós-falha do lado do cliente

Agora que tem um par regional redundante do programa de configuração de tópicos e subscrições, está pronto para implementar a ativação pós-falha do lado do cliente. Existem várias formas de fazer isso, mas todas as implementações de ativação pós-falha terá um recurso comum: se um tópico já não está em bom estado, o tráfego será redirecionado para o outro tópico.

### <a name="basic-client-side-implementation"></a>Implementação básica do lado do cliente

O código de exemplo seguinte é um simples editor de .net que sempre irá tentar publicar para o seu tópico principal primeiro. Se não tiver êxito, irá, em seguida, ativação pós-falha do tópico secundário. Em ambos os casos, ele também verifica o estado de funcionamento api do outro tópico, fazendo um GET no `https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health`. Um tópico de bom estado de funcionamento sempre deverá responder com **200 OK** quando um GET é feito no **estado defuncionamento/api/** ponto final.

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

Agora que tem todos os componentes no local, pode testar a sua implementação de ativação pós-falha. Execute o exemplo acima no código do Visual Studio ou o seu ambiente de favorito. Substitua os seguintes quatro valores com os pontos finais e as chaves a partir dos seus tópicos:

   * primaryTopic - o ponto final para o seu tópico principal.
   * secondaryTopic - o ponto final para o seu tópico secundário.
   * primaryTopicKey - a chave para o seu tópico principal.
   * secondaryTopicKey - a chave para o seu tópico secundário.

Tente executar o publicador de eventos. Deverá ver a sua situação de eventos de teste em seu visualizador do Event Grid, como abaixo.

![Subscrição de eventos primário do Event Grid](./media/custom-disaster-recovery/event-grid-viewer.png)

Para certificar-se de que a ativação pós-falha está a funcionar, pode alterar alguns caracteres na sua chave de tópico principal para torná-la já não são válidas. Tente executar novamente o publicador. Ainda deverá ver novos eventos são apresentados no seu visualizador do Event Grid, no entanto, quando examinar seu console, verá que eles estão agora a ser publicados o tópico secundário.

### <a name="possible-extensions"></a>Extensões possíveis

Existem várias formas de estender esse exemplo com base nas suas necessidades. Para cenários de volume elevado, pode querer verificar regularmente o estado de funcionamento do tópico api de forma independente. Dessa forma, se um tópico foram descer, não precisa de verificá-lo com cada publicação única. Sabe que um tópico não está em bom estado, pode padrão para a publicação para o tópico secundário.

Da mesma forma, pode querer implementar a lógica de reativação pós-falha com base nas suas necessidades específicas. Se o Datacenter mais próximo a publicação é essencial para que possa reduzir a latência, pode de sonda periodicamente o estado de funcionamento da api de um tópico de ativação pós-falha do dispositivo. Assim que é bom estado de funcionamento novamente, saberá que é seguro para a reativação pós-falha para o Datacenter mais próximo.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [receber eventos num ponto final http](./receive-events.md)
- Descubra como [encaminhar eventos para as ligações híbridas](./custom-event-to-hybrid-connection.md)
- Saiba mais sobre [recuperação após desastre com o DNS do Azure e o Gestor de tráfego](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)
