---
title: 'Hubs de notificação do Azure: Perguntas frequentes (FAQs) | Microsoft Docs'
description: Perguntas frequentes sobre como projetar/implementar soluções em hubs de notificação
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
editor: jwargo
keywords: notificação por push, notificações por push, notificações por push do iOS, notificações por push do Android, envio por push do IOS, push do Android
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 03/11/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 5de8c9523e05411a4751766c836b8e99ebb977c1
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213142"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Notificações por push com hubs de notificação do Azure: Perguntas mais frequentes

## <a name="general"></a>Geral

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>O que é a estrutura de recursos dos hubs de notificação?

Os hubs de notificação do Azure têm dois níveis de recursos: hubs e namespaces. Um hub é um recurso de push único que pode conter as informações de push de plataforma cruzada de um aplicativo. Um namespace é uma coleção de hubs em uma região.

O mapeamento recomendado corresponde a um namespace com um aplicativo. Em um namespace, você pode ter um hub de produção que funciona com seu aplicativo de produção, um hub de teste que funciona com seu aplicativo de teste e assim por diante.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Qual é o modelo de preço para os hubs de notificação?

Os detalhes de preços mais recentes podem ser encontrados na página de [preços dos hubs de notificação] . Os hubs de notificação são cobrados no nível do namespace. (Para obter a definição de um namespace, consulte "o que é a estrutura de recursos dos hubs de notificação?") Os hubs de notificação oferecem três camadas:

* **Gratuito**: Essa camada é um bom ponto de partida para explorar os recursos de push. Não é recomendável para aplicativos de produção. Você recebe 500 dispositivos e 1 milhão Pushes incluídos por namespace por mês, sem garantia de contrato de nível de serviço (SLA).
* **Básico**: Essa camada (ou a camada Standard) é recomendada para aplicativos de produção menores. Você obtém dispositivos 200.000 e 10 milhões Pushes incluídos por namespace por mês como uma linha de base.
* **Padrão**: Essa camada é recomendada para aplicativos de produção de médio a grande porte. Você obtém dispositivos 10 milhões e 10 milhões Pushes incluídos por namespace por mês como uma linha de base. Inclui telemetria avançada (dados adicionais sobre o status de push fornecido).

Recursos da camada Standard:

* **Telemetria avançada**: Você pode usar os hubs de notificação por telemetria de mensagem para controlar as solicitações de envio por push e Sistema de Notificação de Plataforma comentários para depuração.
* **Multilocação**: Você pode trabalhar com Sistema de Notificação de Plataforma credenciais em um nível de namespace. Essa opção permite dividir facilmente os locatários em hubs dentro do mesmo namespace.
* **Push agendado**: Você pode agendar notificações para serem enviadas a qualquer momento.
* **Operações em massa**: Habilita a funcionalidade de exportação/importação de registros conforme descrito no documento de [exportação/importação de registros] .

### <a name="what-is-the-notification-hubs-sla"></a>O que é o SLA dos hubs de notificação?

Para as camadas básica e padrão dos hubs de notificação, os aplicativos configurados corretamente podem enviar notificações por Push ou executar operações de gerenciamento de registro pelo menos 99,9% do tempo. Para saber mais sobre o SLA, vá para a página [SLA dos hubs de notificação](https://azure.microsoft.com/support/legal/sla/notification-hubs/) .

> [!NOTE]
> Como as notificações por push dependem de sistemas de notificação de plataforma de terceiros (como o Apple APNS e o Google FCM), não há garantia de SLA para a entrega dessas mensagens. Depois que os hubs de notificação enviam os lotes para os sistemas de notificação de plataforma (SLA garantido), é responsabilidade dos sistemas de notificação de plataforma fornecer os envios por push (sem garantia de SLA).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Como fazer atualizar ou fazer downgrade do meu Hub ou namespace para uma camada diferente?

Vá para o **[Azure portal]**  > **namespaces dos hubs de notificação** ou **hubs de notificação**. Selecione o recurso que você deseja atualizar e vá para **tipo de preço**. Observe os seguintes requisitos:

* O tipo de preço atualizado se aplica a *todos os* hubs no namespace com o qual você está trabalhando.
* Se a contagem de dispositivos exceder o limite da camada para a qual você está fazendo downgrade, você precisará excluir os dispositivos antes de fazer o downgrade.

## <a name="design-and-development"></a>Design e desenvolvimento

### <a name="which-server-side-platforms-do-you-support"></a>A quais plataformas do lado do servidor você dá suporte?

Os SDKs de servidor estão disponíveis para .NET, Java, Node. js, PHP e Python. As APIs de hubs de notificação se baseiam em interfaces REST, para que você possa trabalhar diretamente com as APIs REST se estiver usando plataformas diferentes ou não quiser dependência adicional. Para obter mais informações, vá para a página [APIs REST dos hubs de notificação] .

### <a name="which-client-platforms-do-you-support"></a>A quais plataformas de cliente você dá suporte?

As notificações por push têm suporte para [Ios](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Android China (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([Ios](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) e Android e [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Para obter mais informações, acesse a página de [tutoriais de introdução de hubs de notificação] .

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Há suporte para mensagens de texto, email ou notificações da Web?

Os hubs de notificação são projetados principalmente para enviar notificações aos aplicativos móveis. Ele não fornece recursos de email ou mensagens de texto. No entanto, plataformas de terceiros que fornecem esses recursos podem ser integradas a hubs de notificação para enviar notificações por push nativas usando [Aplicações Móveis].

Os hubs de notificação também não fornecem um serviço de entrega de notificação por push no navegador pronto para uso. Os clientes podem implementar esse recurso usando o Signalr sobre as plataformas do lado do servidor com suporte. 

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>Como os aplicativos móveis e os hubs de notificação do Azure estão relacionados e quando usá-los?

Se você tiver um back-end de aplicativo móvel existente e quiser adicionar apenas a capacidade de enviar notificações por push, poderá usar os hubs de notificação do Azure. Se você quiser configurar seu back-end de aplicativo móvel do zero, considere o uso do recurso aplicativos móveis do serviço Azure App. Um aplicativo móvel provisiona automaticamente um hub de notificação para que você possa enviar facilmente notificações por push do back-end do aplicativo móvel. Os preços dos aplicativos móveis incluem os encargos base de um hub de notificação. Você paga apenas quando excede os envios por push incluídos. Para obter mais detalhes sobre os custos, vá para a página de [Preços do Serviço de Aplicações] .

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>A quantos dispositivos posso dar suporte se eu enviar notificações por Push por meio de hubs de notificação?

Consulte a página de [preços dos hubs de notificação] para obter detalhes sobre o número de dispositivos com suporte.

Se precisar de suporte para mais de 10 milhões dispositivos registrados, você deverá particionar seus dispositivos em vários hubs.

### <a name="how-many-push-notifications-can-i-send-out"></a>Quantas notificações por push posso enviar?

Dependendo da camada selecionada, os hubs de notificação do Azure são dimensionados automaticamente com base no número de notificações que fluem pelo sistema.

> [!NOTE]
> O custo geral de uso pode aumentar com base no número de notificações por push atendidas. Verifique se você está ciente dos limites de camada descritos na página de preços dos [Preços dos hubs de notificação] .

Nossos clientes usam hubs de notificação para enviar milhões de notificações por push diariamente. Você não precisa fazer nada especial para dimensionar o alcance de suas notificações por push, desde que esteja usando os hubs de notificação do Azure.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Quanto tempo leva para que as notificações por push cheguem ao meu dispositivo?

Em um cenário de uso normal, em que a carga de entrada é consistente e mesmo, os hubs de notificação do Azure podem processar pelo menos *1 milhão notificações por push enviam um minuto*. Essa taxa pode variar dependendo do número de marcas, da natureza dos envios de entrada e de outros fatores externos.

Durante o tempo de entrega estimado, o serviço calcula os destinos por plataforma e roteia as mensagens para o serviço de notificação por push (PNS) com base nas marcas registradas ou expressões de marca. É responsabilidade do PNS enviar notificações para o dispositivo.

O PNS não garante nenhum SLA para entregar notificações. No entanto, a maioria das notificações por push é entregue aos dispositivos de destino dentro de alguns minutos (normalmente em 10 minutos) a partir do momento em que eles são enviados aos hubs de notificação. Algumas notificações podem levar mais tempo.

> [!NOTE]
> Os hubs de notificação do Azure têm uma política em vigor para descartar todas as notificações por push que não são entregues ao PNS dentro de 30 minutos. Esse atraso pode ocorrer por vários motivos, mas geralmente porque o PNS está limitando seu aplicativo.

### <a name="is-there-any-latency-guarantee"></a>Há alguma garantia de latência?

Devido à natureza das notificações por push (elas são entregues por um PNS externo, específico da plataforma), não há garantia de latência. Normalmente, a maioria das notificações por push é entregue em alguns minutos.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>O que é necessário considerar ao projetar uma solução com namespaces e hubs de notificação?

#### <a name="mobile-appenvironment"></a>Aplicativo/ambiente móvel

* Use um hub de notificação por aplicativo móvel, por ambiente.
* Em um cenário multilocatário, cada locatário deve ter um hub separado.
* Nunca compartilhe o mesmo Hub de notificação para ambientes de produção e de teste. Essa prática pode causar problemas ao enviar notificações. (A Apple oferece pontos de extremidade de área restrita e push de produção, cada um com credenciais separadas.)
* Por padrão, você pode enviar notificações de teste para seus dispositivos registrados por meio da portal do Azure ou do componente integrado do Azure no Visual Studio. O limite é definido como 10 dispositivos que são selecionados aleatoriamente do pool de registros.

> [!NOTE]
> Se o Hub foi originalmente configurado com um certificado de área restrita da Apple e, em seguida, foi reconfigurado para usar um certificado de produção da Apple, os tokens do dispositivo original são inválidos. Tokens inválidos fazem com que os Pushes falhem. Separe seus ambientes de produção e teste e use diferentes hubs para ambientes diferentes.

#### <a name="pns-credentials"></a>Credenciais do PNS

Quando um aplicativo móvel é registrado com o portal do desenvolvedor de uma plataforma (por exemplo, Apple ou Google), um identificador de aplicativo e tokens de segurança são enviados. O back-end do aplicativo fornece esses tokens para o PNS da plataforma para que as notificações por push possam ser enviadas aos dispositivos. Os tokens de segurança podem estar na forma de certificados (por exemplo, Apple iOS ou Windows Phone) ou chaves de segurança (por exemplo, Google Android ou Windows). Eles devem ser configurados em hubs de notificação. Normalmente, a configuração é feita no nível de Hub de notificação, mas também pode ser feita no nível de namespace em um cenário de multilocatário.

#### <a name="namespaces"></a>Espaços de nomes

Os namespaces podem ser usados para Agrupamento de implantação. Eles também podem ser usados para representar todos os hubs de notificação para todos os locatários do mesmo aplicativo em um cenário multilocatário.

#### <a name="geo-distribution"></a>Distribuição geográfica

A distribuição geográfica nem sempre é crítica em cenários de notificação por push. Vários PNS (por exemplo, APNS ou FCM) que fornecem notificações por push para dispositivos não são distribuídos uniformemente.

Se você tiver um aplicativo que é usado globalmente, poderá criar hubs em namespaces diferentes usando o serviço de hubs de notificação em diferentes regiões do Azure em todo o mundo.

> [!NOTE]
> Não recomendamos essa disposição porque aumenta o custo de gerenciamento, especialmente para registros. Isso deve ser feito somente se houver uma necessidade explícita.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Devo fazer registros do back-end do aplicativo ou diretamente por meio de dispositivos cliente?

Os registros do back-end do aplicativo são úteis quando você precisa autenticar clientes antes de criar o registro. Eles também são úteis quando você tem marcas que devem ser criadas ou modificadas pelo back-end do aplicativo com base na lógica do aplicativo. Para obter mais informações, acesse as páginas diretrizes de [Diretrizes de registro de back-end] e [Diretrizes de registro de back-end 2] .

### <a name="what-is-the-push-notification-delivery-security-model"></a>O que é o modelo de segurança de entrega de notificação por push?

Os hubs de notificação do Azure usam um modelo de segurança com base em [assinatura de acesso compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md). Você pode usar os tokens de assinatura de acesso compartilhado no nível do namespace raiz ou no nível do hub de notificação granular. Tokens de assinatura de acesso compartilhado podem ser definidos para seguir regras de autorização diferentes, por exemplo, para enviar permissões de mensagem ou para escutar permissões de notificação. Para obter mais informações, consulte o documento [modelo de segurança dos hubs de notificação] .

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Como devo lidar com a carga confidencial em notificações por push?

Todas as notificações são entregues aos dispositivos de destino pelo PNS da plataforma. Quando uma notificação é enviada aos hubs de notificação do Azure, ela é processada e passada para o respectivo PNS.

Todas as conexões, do remetente para os hubs de notificação do Azure para o PNS, usam HTTPS.

> [!NOTE]
> Os hubs de notificação do Azure não registram a carga de mensagens de forma alguma.

Para enviar cargas confidenciais, é recomendável usar um padrão de push seguro. O remetente entrega uma notificação de ping com um identificador de mensagem para o dispositivo sem a carga confidencial. Quando o aplicativo no dispositivo recebe a carga, o aplicativo chama uma API segura diretamente para buscar os detalhes da mensagem. Para obter um guia sobre como implementar esse padrão, vá para a página do [tutorial de push seguro de hubs de notificação] .

## <a name="operations"></a>Operações

### <a name="what-support-is-provided-for-disaster-recovery"></a>Qual suporte é fornecido para a recuperação de desastre?

Fornecemos cobertura de recuperação de desastre de metadados em nossa extremidade (o nome dos hubs de notificação, a cadeia de conexão e outras informações importantes). Quando um cenário de recuperação de desastre é disparado, os dados de registro são o *único segmento* da infraestrutura dos hubs de notificação que é perdida. Você precisará implementar uma solução para repopular esses dados em seu novo hub após a recuperação:

1. Crie um hub de notificações secundário em um data center diferente. É recomendável criar um desde o início para protegê-lo de um evento de recuperação de desastre que pode afetar seus recursos de gerenciamento. Você também pode criar um no momento do evento de recuperação de desastre.

2. Preencha o Hub de notificação secundário com os registros do seu hub de notificação principal. Não recomendamos a tentativa de manter registros em ambos os hubs e mantê-los em sincronia à medida que os registros entram. Essa prática não funciona bem devido à tendência inerente de registros para expirar no lado do PNS. Os hubs de notificação os limpam à medida que recebe comentários PNS sobre registros expirados ou inválidos.  

Temos duas recomendações para back-ends de aplicativo:

* Use um back-end de aplicativo que mantém um determinado conjunto de registros em seu final. Em seguida, ele pode executar uma inserção em massa no Hub de notificação secundário.
* Use um back-end de aplicativo que obtenha um despejo regular de registros do hub de notificação principal como um backup. Em seguida, ele pode executar uma inserção em massa no Hub de notificação secundário.

> [!NOTE]
> A funcionalidade de exportação/importação de registros disponível na camada Standard é descrita no documento de [exportação/importação de registros] .

Se você não tiver um back-end, quando o aplicativo for iniciado nos dispositivos de destino, eles executarão um novo registro no Hub de notificação secundário. Eventualmente, o Hub de notificação secundário terá todos os dispositivos ativos registrados.

Haverá um período de tempo quando os dispositivos com aplicativos não abertos não receberão notificações.

### <a name="is-there-audit-log-capability"></a>Há recurso de log de auditoria?

Sim. Todas as operações de gerenciamento de hubs de notificação atualizam o log de atividades do Azure para o que é exposto no [Azure portal]. O log de atividades do Azure oferece informações sobre as operações executadas nos recursos em suas assinaturas. Usando o log de atividades, você pode determinar o que, quem e quando para qualquer operação de gravação (PUT, POST, excluir) feita para os recursos em sua assinatura. Também é possível compreender o estado de operações e outras propriedades relevantes. Porém. o log de atividades não inclui a operação de leitura (GET).

## <a name="monitoring-and-troubleshooting"></a>Monitoramento e solução de problemas

### <a name="what-troubleshooting-capabilities-are-available"></a>Quais recursos de solução de problemas estão disponíveis?

Os hubs de notificação do Azure fornecem vários recursos para solução de problemas, especialmente para o cenário mais comum de notificações descartadas. Para obter detalhes, consulte os white paper de [Solução de problemas de hubs de notificação] .

### <a name="what-telemetry-features-are-available"></a>Quais recursos de telemetria estão disponíveis?

Os hubs de notificação do Azure permitem exibir dados de telemetria no [Azure portal]. Os detalhes das métricas estão disponíveis na página [Métricas de hubs de notificação] .

Você também pode acessar métricas programaticamente. Para obter mais informações, veja os artigos seguintes:

- [Recuperar métricas de Azure monitor com .net](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). Este exemplo usa o nome de usuário e a senha. Para usar um certificado, sobrecarregar o método FromServicePrincipal para fornecer um certificado, conforme mostrado neste [exemplo](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs). 
- [Obtendo métricas e logs de atividade para um recurso](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Instruções da API REST de monitoramento do Azure](../azure-monitor/platform/rest-api-walkthrough.md)


> [!NOTE]
> As notificações bem-sucedidas significam simplesmente que as notificações por push foram entregues ao PNS externo (por exemplo, APNS para Apple ou FCM para Google). É responsabilidade do PNS entregar as notificações aos dispositivos de destino. Normalmente, o PNS não expõe as métricas de entrega para terceiros.  

[Azure portal]: https://portal.azure.com
[Preços dos hubs de notificação]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[APIs REST dos hubs de notificação]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Tutoriais de Introdução de hubs de notificação]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Diretrizes de registro de back-end]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Diretrizes de registro de back-end 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Modelo de segurança dos hubs de notificação]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Tutorial de push seguro de hubs de notificação]: https://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Solução de problemas de hubs de notificação]: https://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Métricas de hubs de notificação]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Exportação/importação de registros]: https://docs.microsoft.com/azure/notification-hubs/export-modify-registrations-bulk
[Azure portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Aplicações Móveis]: https://azure.microsoft.com/services/app-service/mobile/
[Preços do Serviço de Aplicações]: https://azure.microsoft.com/pricing/details/app-service/
