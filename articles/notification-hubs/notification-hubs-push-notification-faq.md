---
title: 'Hubs de notificação do Azure: Perguntas mais frequentes sobre (FAQ) | Documentos da Microsoft'
description: Perguntas frequentes sobre a criação/implementação de soluções em Hubs de notificação
services: notification-hubs
documentationcenter: mobile
author: jwargo
manager: patniko
editor: spelluru
keywords: notificação push, notificações push, notificações de push de iOS, notificações android push, ios push, android push
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 03/11/2019
ms.author: jowargo
ms.openlocfilehash: 863b380d4f36e24119e64fbd4a59e8cd66c911e1
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779424"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Enviar notificações push com Notification Hubs do Azure: Perguntas mais frequentes

## <a name="general"></a>Geral

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>O que é a estrutura de recursos dos Hubs de notificação?

Os Hubs de notificação do Azure tem dois níveis de recursos: hubs e espaços de nomes. Um hub é um recurso de push única que pode conter as informações de push de plataforma cruzada de uma aplicação. Um espaço de nomes é uma coleção de hubs numa única região.

Mapeamento recomendado corresponde a um espaço de nomes com uma aplicação. Dentro de um espaço de nomes, pode ter um concentrador de produção que funciona com a sua aplicação de produção, um hub de teste que funciona com a sua aplicação de teste e assim por diante.

### <a name="what-is-the-price-model-for-notification-hubs"></a>O que é o modelo de preço para os Hubs de notificação?

Os detalhes de preços mais recentes podem ser encontrados no [Preços dos Hubs de notificação] página. Os Hubs de notificação é faturado ao nível do espaço de nomes. (Para a definição de um espaço de nomes, consulte a "Qual é a estrutura de recursos dos Hubs de notificação?") Os Hubs de notificação oferece três escalões:

* **Gratuito**: Esta camada é um bom ponto de partida para explorar os recursos de push. Não é recomendado para aplicações de produção. Obtém 500 dispositivos e 1 milhão de pushes incluído por espaço de nomes por mês, com nenhuma garantia de contrato de nível (SLA) do serviço.
* **Básico**: Este escalão (ou o escalão Standard) é recomendado para aplicações de produção mais pequenas. Obtém 200 000 dispositivos e 10 milhões de pushes incluídos por espaço de nomes por mês como uma linha de base.
* **Padrão**: Esta camada é recomendada para médio para aplicações de produção de grandes dimensões. Obtém 10 milhões de dispositivos e 10 milhões de pushes incluídos por espaço de nomes por mês como uma linha de base. Inclui a telemetria avançada (dados adicionais sobre o estado de push fornecido).

Funcionalidades do escalão Standard:

* **Telemetria avançada**: Pode utilizar Notification Hubs da telemetria por mensagem para acompanhar todos os pedidos de push e comentários do sistema de notificação de plataforma para depuração.
* **Arquitetura "multitenancy"**: Pode trabalhar com as credenciais do sistema de notificação de plataforma num nível de espaço de nomes. Esta opção permite-lhe facilmente divididas inquilinos em hubs dentro do mesmo espaço de nomes.
* **Agendado push**: Pode agendar notificações para serem enviados a qualquer momento.
* **Em massa operações**: Ativa a funcionalidade de exportação/importação de registos, conforme descrito no [Exportação/importação de registos] documento.

### <a name="what-is-the-notification-hubs-sla"></a>O que é o SLA de Hubs de notificação?

Para os escalões básico e Standard dos Hubs de notificação, as aplicações devidamente configuradas podem enviar notificações push ou efetuar operações de gestão de registo a, pelo menos, 99,9% do tempo. Para saber mais sobre o SLA, vá para o [SLA de Hubs de notificação](https://azure.microsoft.com/support/legal/sla/notification-hubs/) página.

> [!NOTE]
> Uma vez notificações push dependem de sistemas de notificação de plataforma de terceiros (por exemplo, o APNS da Apple e o Google FCM), não há nenhuma garantia de SLA para o fornecimento dessas mensagens. Depois dos Hubs de notificação envia os lotes para sistemas de notificação de plataforma (a garantia de SLA), é da responsabilidade dos sistemas de notificação de plataforma para fornecer os pushes (nenhum SLA garantido).

### <a name="which-customers-are-using-notification-hubs"></a>Quais os clientes estão a utilizar os Hubs de notificação?

Muitos clientes utilizam os Hubs de notificação. Alguns dos importantes estão listados aqui:

* Sochi 2014: Centenas de grupos de interesses, 3 + milhões de dispositivos e distribuídos nas duas semanas de 150 + milhões de notificações. [Estudo de caso: Sochi]
* Skanska: [Estudo de caso: Skanska]
* Tempos de Seattle: [Estudo de caso: Tempos de Seattle]
* Mural.ly: [Estudo de caso: Mural.ly]
* 7Digital: [Estudo de caso: 7Digital]
* Aplicações do Bing: Dezenas de milhões de dispositivos enviam notificações de 3 milhões por dia.

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Como posso atualizar ou mudar o meu hub ou espaço de nomes para um escalão diferente?

Vá para o  **[portal do Azure]** > **espaços de nomes de Hubs de notificação** ou **os Hubs de notificação**. Selecione o recurso que pretende atualizar e aceda a **escalão de preço**. Tenha em atenção os seguintes requisitos:

* O escalão de preço atualizado aplica-se ao *todos os* hubs num espaço de nomes que está a trabalhar.
* Se a contagem de dispositivos exceder o limite da camada que está a fazer downgrade, terá de eliminar dispositivos antes de mudar a versão.

## <a name="design-and-development"></a>Design e desenvolvimento

### <a name="which-server-side-platforms-do-you-support"></a>Que plataformas de servidor suporta?

SDKs do servidor estão disponíveis para .NET, Java, node. js, PHP e Python. APIs de Hubs de notificação baseiam-se nas interfaces do REST, para que possa trabalhar diretamente com as APIs REST se estiver a utilizar diferentes plataformas ou não pretender que a dependência extra. Para obter mais informações, vá para o [REST APIs dos Hubs de notificação] página.

### <a name="which-client-platforms-do-you-support"></a>Que plataformas de cliente suporta?

Notificações push são suportadas para [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Universal do Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China (por meio do Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) e Android, [aplicações do Chrome](notification-hubs-chrome-push-notifications-get-started.md), e [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Para obter mais informações, vá para o [Tutoriais de introdução aos Hubs de notificação] página.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Suporta a mensagem de texto, correio eletrónico ou notificações de web?

Os Hubs de notificação foi concebido principalmente para enviar notificações para aplicações móveis. Ele não fornece e-mail ou texto capacidades para mensagens. No entanto, as plataformas de terceiros que oferece esses recursos podem ser integradas com os Hubs de notificação para enviar notificações push nativas utilizando [Aplicações Móveis].

Os Hubs de notificação também não fornece um serviço de disponibilização de notificação push no navegador de imediato. Os clientes podem implementar esta funcionalidade utilizando o SignalR sobre as plataformas suportadas do lado do servidor. Se pretender enviar notificações para aplicações de browser na sandbox do Chrome, consulte a [tutorial de aplicações do Chrome].

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>Como são aplicações móveis e os Hubs de notificação do Azure relacionados e quando devo utilizá-los?

Se tiver um back-end de aplicações móveis existente e pretender adicionar apenas a capacidade de enviar notificações push, pode utilizar os Hubs de notificação do Azure. Se quiser configurar o seu back-end de aplicação móvel do zero, considere utilizar a funcionalidade de aplicações móveis do serviço de aplicações do Azure. Uma aplicação móvel Aprovisiona automaticamente um hub de notificação para que pode enviar facilmente notificações push do back-end da aplicação móvel. Preços de aplicações móveis incluem cobranças de base para um hub de notificação. Paga apenas quando exceder os pushes incluídos. Para obter mais detalhes sobre os custos, vá para o [Preços do serviço de aplicações] página.

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Quantos dispositivos posso suportar se posso enviar notificações push através dos Hubs de notificação?

Consulte a [Preços dos Hubs de notificação] página para obter detalhes sobre o número de dispositivos suportados.

Se precisar de suporte para mais de 10 milhões de dispositivos registados, [contacte-nos](https://azure.microsoft.com/overview/contact-us/) diretamente e iremos ajudá-lo a dimensionar a sua solução.

### <a name="how-many-push-notifications-can-i-send-out"></a>O número de notificações push pode envio?

Consoante o escalão selecionado, os Hubs de notificação do Azure automaticamente verticalmente com base no número de notificações que flui através do sistema.

> [!NOTE]
> O custo de utilização geral pode aumentar com base no número de notificações push que está a ser atendidos. Certifique-se de que está em consideração os limites da camada descritos no [Preços dos Hubs de notificação] página.

Os nossos clientes utilizam os Hubs de notificação para enviar milhões de notificações push diariamente. Não é necessário fazer nada de especial para aumentar o alcance das notificações push, desde que estiver a utilizar os Hubs de notificação do Azure.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Quanto tempo demora para enviar notificações push para alcançar o meu dispositivo?

Num cenário de utilização normal, em que a entrada de carga for consistente e até mesmo, os Hubs de notificação do Azure pode processar pelo menos *notificação push de 1 milhão envia um minuto*. Essa taxa pode variar consoante o número de tags, a natureza do envia de entrada e de outros fatores externos.

Durante o tempo de entrega estimado, o serviço calcula os destinos por plataforma e as rotas de mensagens para o serviço de notificação Push (PNS) com base no marcas registradas ou expressões de etiqueta. É da responsabilidade do PNS para enviar notificações para o dispositivo.

O PNS não garante que qualquer SLA para notificações push. No entanto, a maioria das notificações de push são entregues aos dispositivos de destino dentro de alguns minutos (normalmente dentro de 10 minutos) desde o momento em que são enviadas para os Hubs de notificação. Algumas notificações poderão demorar mais tempo.

> [!NOTE]
> Os Hubs de notificação do Azure tem uma política em vigor para remover quaisquer notificações push que não são entregues para o PNS dentro de 30 minutos. Este atraso pode ocorrer por diversas razões, mas a maioria das normalmente porque o PNS está a limitar o seu aplicativo.

### <a name="is-there-any-latency-guarantee"></a>Existe qualquer garantia de latência?

Devido à natureza de notificações push (que são entregues por um PNS externo, específicos da plataforma), não há nenhuma garantia de latência. Normalmente, a maioria das notificações push são entregues em poucos minutos.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>O que é necessário a ter em consideração quando conceber uma solução com os hubs de notificação e espaços de nomes?

#### <a name="mobile-appenvironment"></a>Ambiente/aplicações móvel

* Utilize um hub de notificação por aplicação móvel, por ambiente.
* Num cenário de multi-inquilino, cada inquilino deve ter um hub separado.
* Nunca compartilham o mesmo hub de notificação para produção e os ambientes de teste. Esta prática pode causar problemas ao enviar notificações. (A Apple oferece a emissão de produção e de Sandbox pontos de extremidade, cada um com credenciais separadas.)
* Por predefinição, pode enviar notificações de teste para os seus dispositivos registados através do portal do Azure ou o componente integrado do Azure no Visual Studio. O limiar é definido para 10 dispositivos que são selecionados aleatoriamente entre o conjunto de registo.

> [!NOTE]
> Se o seu hub foi originalmente configurado com um certificado de sandbox da Apple e, em seguida, foi reconfigurado para utilizar um certificado de produção da Apple, os tokens de dispositivo original são inválidos. Tokens inválidos fazer com que os pushes efetuar a ativação. Separar os seus ambientes de produção e teste e utilizar os hubs de diferentes para diferentes ambientes.

#### <a name="pns-credentials"></a>Credenciais PNS

Quando uma aplicação móvel é registrada com o portal do Programador de uma plataforma (por exemplo, da Apple ou da Google), um tokens de segurança e identificador de aplicação são enviados. O back-end de aplicação fornece estes tokens para PNS a plataforma para que as notificações push podem ser enviadas para dispositivos. Tokens de segurança podem ser na forma de certificados (por exemplo, Apple iOS ou Windows Phone) ou chaves de segurança (por exemplo, Google Android ou Windows). Têm de ser configuradas nos hubs de notificação. Configuração é geralmente feita no nível do hub de notificação, mas também pode ser feito ao nível do espaço de nomes num cenário de multi-inquilino.

#### <a name="namespaces"></a>Espaços de nomes

Espaços de nomes podem ser utilizados para o agrupamento de implementação. Eles também podem ser usados para representar todos os hubs de notificação para todos os inquilinos da mesma aplicação num cenário de multi-inquilino.

#### <a name="geo-distribution"></a>Distribuição geográfica

A distribuição geográfica nem sempre é fundamental para cenários de notificação push. Vários PNSes (por exemplo, o APNS ou o FCM) que envie notificações push para dispositivos não são distribuídos uniformemente.

Se tiver um aplicativo que é usado globalmente, pode criar hubs Namespaces diferentes com o serviço de Hubs de notificação em diferentes regiões do Azure em todo o mundo.

> [!NOTE]
> Não recomendamos esta disposição porque aumenta o custo de gestão, particularmente para registos. Deve ser feito apenas se há uma necessidade explícita.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Devo fazer registos do back-end da aplicação ou diretamente através do cliente de dispositivos?

O registo a partir do back-end de aplicação é úteis quando tiver de autenticar clientes antes de criar o registo. Eles também são úteis quando tem etiquetas que tem de ser criadas ou modificadas pelo back-end de aplicação com base numa lógica de aplicação. Para obter mais informações, vá para o [documentação de orientação do registo de back-end] e [Documentação de orientação do registo de back-end 2] páginas.

### <a name="what-is-the-push-notification-delivery-security-model"></a>O que é o modelo de segurança de entrega de notificações push?

Os Hubs de notificação do Azure utiliza um [assinatura de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md)-com base em modelo de segurança. Pode utilizar os tokens de assinatura de acesso partilhado ao nível do espaço de nomes de raiz ou ao nível do hub de notificação granular. Assinatura de acesso tokens podem ser definidos para seguir as regras de autorização diferentes, por exemplo, para enviar as permissões de mensagem ou para escutar as permissões de notificação de partilhado. Para obter mais informações, consulte a [modelo de segurança de Hubs de notificação] documento.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Como eu manipulo o payload confidencial em notificações push?

Todas as notificações são entregues aos dispositivos de destino pelo PNS da plataforma. Quando uma notificação é enviada para os Hubs de notificação do Azure, é processada e passado para o PNS respectivo.

Todas as ligações, do remetente para Hubs de notificação do Azure para o PNS, utilizam HTTPS.

> [!NOTE]
> Os Hubs de notificação do Azure não regista o payload de mensagens de qualquer forma.

Para enviar payloads confidenciais, recomendamos que utilize um padrão de proteger Push. O remetente fornece uma notificação de ping com um identificador de mensagem para o dispositivo sem o payload confidencial. Quando a aplicação no dispositivo recebe o payload, o aplicativo chama uma API segura diretamente para obter os detalhes da mensagem. Para obter um guia sobre como implementar este padrão, vá para o [Tutorial de proteger o Push dos Hubs de notificação] página.

## <a name="operations"></a>Operações

### <a name="what-support-is-provided-for-disaster-recovery"></a>Que suporte é fornecido para recuperação após desastre?

Fornecemos cobertura de recuperação após desastre de metadados do nosso lado (o nome de Hubs de notificação, a cadeia de ligação e outras informações críticas). Quando for acionado um cenário de recuperação após desastre, dados de registo são o *segmentar apenas* da infraestrutura dos Hubs de notificação que é perdida. Terá de implementar uma solução repreencher estes dados em seu novo pós-recuperação de hub:

1. Crie um hub de notificações secundário num centro de dados diferentes. Recomendamos que crie um desde o início para protegem de um evento de recuperação após desastre que pode afetar as funcionalidades de gestão. Também pode criar um no momento do evento de recuperação após desastre.

2. Preencha o hub de notificação secundário com os registros do seu hub de notificação primário. Não é recomendada a tentar manter os registos em ambos os hubs e mantê-los em sincronização conforme entram os registos. Esta prática não funciona bem a devido à tendência inerente de registos para expirar no lado do PNS. Os Hubs de notificação limpa-las à medida que ele recebe comentários PNS sobre registos inválidos ou expirados.  

Temos duas recomendações para o back-ends de aplicação:

* Utilize um back-end de aplicação que mantém um determinado conjunto de registos no final. Em seguida, ele pode executar uma inserção em massa para o hub de notificação secundário.
* Utilize um back-end de aplicação que obtém um despejo regular de registos do hub de notificação primário como uma cópia de segurança. Em seguida, ele pode executar uma inserção em massa para o hub de notificação secundário.

> [!NOTE]
> Está descrita com funcionalidade de exportação/importação de registos disponível no escalão Standard a [Exportação/importação de registos] documento.

Se não tiver um back-end, quando a aplicação for iniciada em dispositivos de destino, eles executar um novo registo no hub de notificações secundário. Eventualmente, o hub de notificação secundário terá todos os dispositivos ativos registados.

Haverá um período de tempo quando os dispositivos com aplicações não abertos não irão receber notificações.

### <a name="is-there-audit-log-capability"></a>Existe capacidade do registo de auditoria?

Sim. Atualização de operações de gerenciamento de Hubs de notificação todos os registo de atividades do Azure para o qual é exposto na [portal do Azure]. O registo de atividades do Azure disponibiliza informações aprofundadas sobre as operações executadas em recursos nas suas subscrições. Utilizar o registo de atividades, pode determinar a quê, quem e quando, por qualquer escrita (PUT, POST, DELETE) efetuadas para os recursos na sua subscrição. Também é possível compreender o estado de operações e outras propriedades relevantes. No entanto. o registo de Atividades não inclui a operação de leitura (GET).

## <a name="monitoring-and-troubleshooting"></a>Monitorização e resolução de problemas

### <a name="what-troubleshooting-capabilities-are-available"></a>Que capacidades de resolução de problemas estão disponíveis?

Os Hubs de notificação do Azure fornece vários recursos de resolução de problemas, especialmente para o cenário mais comum de notificações ignoradas. Para obter detalhes, consulte a [Hubs de notificação de resolução de problemas] white paper.

### <a name="what-telemetry-features-are-available"></a>Quais recursos de telemetria estão disponíveis?

Do Azure de Hubs de notificação de permite ver dados de telemetria na [portal do Azure]. Detalhes das métricas estão disponíveis na [métricas de Hubs de notificação] página.

Pode também acessar programaticamente métricas. Para obter mais informações, veja os artigos seguintes:

- [Obter métricas do Azure Monitor com o .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). Este exemplo utiliza o nome de utilizador e palavra-passe. Para utilizar um certificado, sobrecarregar o método FromServicePrincipal para fornecer um certificado, conforme mostrado na [neste exemplo](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs). 
- [Obter métricas e registos de atividades para um recurso](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Instruções de API do REST de monitorização do Azure](../azure-monitor/platform/rest-api-walkthrough.md)


> [!NOTE]
> Notificações com êxito significam simplesmente que as notificações push tem sido disponibilizadas para o PNS externo (por exemplo, o APNS da Apple) ou FCM para o Google. É da responsabilidade do PNS para entregar as notificações para dispositivos de destino. Normalmente, o PNS não expõe as métricas de entrega a terceiros.  

[Portal do Azure]: https://portal.azure.com
[Preços dos Hubs de notificação]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[Estudo de caso: Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[Estudo de caso: Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[Estudo de caso: Tempos de Seattle]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[Estudo de caso: Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[Estudo de caso: 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[REST APIs dos Hubs de notificação]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Tutoriais de introdução aos Hubs de notificação]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Tutorial de aplicações do Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Documentação de orientação do registo de back-end]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Documentação de orientação do registo de back-end 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Modelo de segurança de Hubs de notificação]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Tutorial de proteger o Push dos Hubs de notificação]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Hubs de notificação de resolução de problemas]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Métricas de Hubs de notificação]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Exportação/importação de registos]: https://msdn.microsoft.com/library/dn790624.aspx
[Portal do Azure]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Aplicações Móveis]: https://azure.microsoft.com/services/app-service/mobile/
[Preços do serviço de aplicações]: https://azure.microsoft.com/pricing/details/app-service/
