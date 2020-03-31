---
title: Centros de Notificação Azure frequentemente questionados (PERGUNTAS) / Microsoft Docs
description: FaQs sobre a conceção e implementação de soluções em Hubs de Notificação Azure.
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
editor: jwargo
keywords: notificação push, notificações push, notificações push iOS, notificações push android, ios push, android push
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 11/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 3212520f37d33a2d8fb1b071506f688b9f75f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263834"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Notificações push com Hubs de Notificação Azure: Perguntas frequentes

## <a name="general"></a>Geral

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Qual é a estrutura de recursos dos Centros de Notificação?

O Azure Notification Hubs tem dois níveis de recursos: hubs e espaços de nome. Um hub é um único recurso push que pode conter a informação de impulso cross-platform de uma aplicação. Um espaço de nome é uma coleção de centros em uma região. O mapeamento recomendado corresponde a um espaço de nome com uma aplicação. Dentro de um espaço de nome, pode ter um centro de produção que funciona com a sua app de produção, um centro de testes que funciona com a sua app de testes, e assim por diante.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Qual é o modelo de preço para Centros de Notificação?

Os mais recentes detalhes sobre preços podem ser encontrados na página de Preços dos Centros de [Notificação.] O Notification Hubs é faturado ao nível do espaço de nome. (Para a definição de espaço de nome, ver "Qual é a estrutura de recursos dos Centros de Notificação?") O Notification Hubs oferece três níveis:

* **Grátis**: Este nível é um bom ponto de partida para explorar as capacidades de impulso. Não é recomendado para aplicações de produção. Obtém 500 dispositivos e 1 milhão de pushs incluídos por espaço de nome por mês, sem garantia de acordo de nível de serviço (SLA).
* **Básico**: Este nível (ou o nível Standard) é recomendado para aplicações de produção mais pequenas. Obtém-se 200.000 dispositivos e 10 milhões de empurrões incluídos por espaço de nome por mês como base.
* **Standard**: Este nível é recomendado para aplicações de produção média a grande. Obtém-se 10 milhões de dispositivos e 10 milhões de empurrões incluídos por espaço de nome por mês como base. Inclui telemetria rica (dados adicionais sobre o estado do empurrão fornecidos).

Características de nível padrão:

* **Telemetria rica:** Pode utilizar centros de notificação por telemetria de mensagens para rastrear quaisquer pedidos de push e feedback do sistema de notificação da plataforma para depuração.
* **Multi-arrendamento:** Pode trabalhar com credenciais do Sistema de Notificação da Plataforma a nível de espaço de nome. Esta opção permite-lhe dividir facilmente os inquilinos em centros dentro do mesmo espaço de nome.
* **Push agendado**: Pode agendar notificações a qualquer momento.
* **Operações**a granel : Permite registos A funcionalidade Export/Import, tal como descrito no documento de [exportação/importação de registos.]

### <a name="what-is-the-notification-hubs-sla"></a>O que é o Centro de Notificação SLA?

Para os níveis basic e standard notification Hubs, as aplicações devidamente configuradas podem enviar notificações push ou executar operações de gestão de registo pelo menos 99,9 por cento do tempo. Para saber mais sobre o SLA, vá à página SLA dos Centros de [Notificação.](https://azure.microsoft.com/support/legal/sla/notification-hubs/)

> [!NOTE]
> Como as notificações push dependem de sistemas de notificação de plataforma de terceiros, como o Serviço de Notificação push (APNs) da Apple e o Firebase Cloud Messaging (FCM) da Google, não existe nenhuma garantia sla para a entrega destas mensagens. Após o Envio de Centros de Notificação para sistemas de notificação de plataforma (SLA garantido), é da responsabilidade dos Sistemas de Notificação da Plataforma entregar os pushs (sem SLA garantido).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Como melhoro ou desclassifico o meu hub ou espaço de nome para um nível diferente?

Vá ao **[portal]** > Azure Notification**Hubs Namespaces** ou **Centros de Notificação.** Selecione o recurso que pretende atualizar e vá para **o Nível**de Preços . Note os seguintes requisitos:

* O nível de preços atualizado aplica-se a *todos os* centros do espaço de nome com que está a trabalhar.
* Se a contagem do seu dispositivo exceder o limite do nível a que está a degradar-se, tem de eliminar os dispositivos antes de desvalorizar.

## <a name="design-and-development"></a>Design e desenvolvimento

### <a name="which-server-side-platforms-do-you-support"></a>Que plataformas do lado do servidor suporta?

Os SDKs do servidor estão disponíveis para .NET, Java, Node.js, PHP e Python. As APIs de Centros de Notificação baseiam-se em interfaces REST, para que possa trabalhar diretamente com APIs REST se estiver a utilizar plataformas diferentes ou se não quiser dependência extra. Para mais informações, vá à página DE APIs do Centro de [Notificação.]

### <a name="which-client-platforms-do-you-support"></a>Que plataformas de clientes apoia?

As notificações push são suportadas para [iOS,](notification-hubs-ios-apple-push-notification-apns-get-started.md) [Android,](notification-hubs-android-push-notification-google-fcm-get-started.md) [Windows Universal,](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) [Windows Phone,](notification-hubs-windows-mobile-push-notifications-mpns.md) [Android China (via Baidu),](notification-hubs-baidu-china-android-notifications-get-started.md)Xamarin[(iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) e Android, e [Safari.](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari) Para mais informações, vá à página de [tutoriais iniciar] centros de notificação.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Suporta mensagens de texto, e-mail ou notificações web?

O Notification Hubs envia notificações a dispositivos que executam aplicações móveis. Não fornece capacidades de e-mail ou mensagem de texto. O Notification Hubs também não fornece um serviço de notificação push no navegador fora da caixa. Os clientes podem implementar esta funcionalidade utilizando o SignalR em cima das plataformas suportadas do servidor. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Quantos dispositivos posso suportar se enviar notificações push via Notification Hubs?

Consulte a página de preços dos Centros de [Notificação] para obter informações sobre o número de dispositivos suportados.

Se precisar de suporte para mais de 10 milhões de dispositivos registados, tem de dividir os seus dispositivos em vários centros.

### <a name="how-many-push-notifications-can-i-send-out"></a>Quantas notificações de pressão posso enviar?

Dependendo do nível selecionado, o Azure Notification Hubs escala automaticamente com base no número de notificações que fluem através do sistema.

> [!NOTE]
> O custo global de utilização pode aumentar com base no número de notificações push enviadas. Certifique-se de que está ciente dos limites de nível descritos na página de Preços dos Centros de [Notificação.]

Os nossos clientes usam Centros de Notificação para enviar milhões de notificações push diariamente. Não tem de fazer nada de especial para escalar o alcance das suas notificações push desde que utilize hubs de notificação do Azure.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Quanto tempo demora a enviar notificações push para chegar ao meu dispositivo?

Num cenário de utilização normal, em que a carga de entrada é consistente e até mesmo, os Hubs de Notificação Azure podem processar pelo menos 1 milhão de *notificações push envia um minuto*. Esta taxa pode variar dependendo do número de tags, da natureza dos envios de entrada e de outros fatores externos.

Durante o tempo de entrega estimado, o serviço calcula os alvos por plataforma e encaminha as mensagens para o Serviço de Notificação push (PNS) com base nas etiquetas registadas ou expressões de etiquetas. É da responsabilidade do PNS enviar notificações para o dispositivo.

O PNS não garante qualquer SLA para a entrega de notificações. No entanto, a maioria das notificações push são entregues aos dispositivos-alvo dentro de poucos minutos (normalmente dentro de 10 minutos) a partir do momento em que são enviadas para centros de notificação. Algumas notificações podem levar mais tempo.

> [!NOTE]
> O Azure Notification Hubs tem uma política em vigor para deixar cair quaisquer notificações push que não sejam entregues ao PNS dentro de 30 minutos. Este atraso pode acontecer por uma série de razões, mas mais comummente porque o PNS está a estrangular a sua aplicação.

### <a name="is-there-any-latency-guarantee"></a>Há alguma garantia de latência?

Devido à natureza das notificações push (são entregues por um PNS externo, específico da plataforma), não existe garantia de latência. Normalmente, a maioria das notificações push são entregues em poucos minutos.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>O que devo considerar ao desenhar uma solução com espaços de nome e centros de notificação?

#### <a name="mobile-appenvironment"></a>Aplicativo/ambiente móvel

* Utilize um centro de notificação por aplicação móvel, por ambiente.
* Num cenário multi-inquilino, cada inquilino deve ter um centro separado.
* Nunca partilhe o mesmo centro de notificação para ambientes de produção e teste. Esta prática pode causar problemas no envio de notificações. (A Apple oferece pontos finais Sandbox e Production Push, cada um com credenciais separadas.)
* Por predefinição, pode enviar notificações de teste para os seus dispositivos registados através do portal Azure ou do componente integrado Azure no Estúdio Visual. O limiar é definido para 10 dispositivos que são selecionados aleatoriamente a partir do pool de registo.

> [!NOTE]
> Se o seu hub foi originalmente configurado com um certificado apple sandbox e depois foi reconfigurado para usar um certificado de produção da Apple, os tokens originais do dispositivo são inválidos. Fichas inválidas fazem com que os empurrões falhem. Separe os ambientes de produção e teste e use diferentes centros para diferentes ambientes.

#### <a name="pns-credentials"></a>Credenciais de PNS

Quando uma aplicação móvel é registada no portal de desenvolvimento de uma plataforma (por exemplo, Apple ou Google), é enviado um identificador de aplicações e fichas de segurança. O backend da aplicação fornece estes tokens para o PNS da plataforma para que as notificações push possam ser enviadas para dispositivos. Os tokens de segurança podem estar sob a forma de certificados (por exemplo, Apple iOS ou Windows Phone) ou chaves de segurança (por exemplo, Google Android ou Windows). Devem ser configurados em centros de notificação. A configuração é normalmente feita ao nível do centro de notificação, mas também pode ser feita ao nível do espaço de nome num cenário multi-inquilino.

#### <a name="namespaces"></a>Espaços de nomes

Espaços de nome podem ser usados para o agrupamento de implantação. Também podem ser usados para representar todos os centros de notificação para todos os inquilinos da mesma app num cenário multi-inquilino.

#### <a name="geo-distribution"></a>Geodistribuição

A geodistribuição nem sempre é crítica nos cenários de notificação push. Vários PNSes (por exemplo, APNs ou FCM) que entregam notificações push aos dispositivos não são distribuídos uniformemente.

Se tiver uma aplicação que é usada globalmente, pode criar centros em diferentes espaços de nome, utilizando o serviço Notification Hubs em diferentes regiões do Azure em todo o mundo.

> [!NOTE]
> Não recomendamos este acordo porque aumenta o seu custo de gestão, particularmente para inscrições. Só deve ser feito se houver uma necessidade explícita.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Devo fazer registos a partir do backend da app ou diretamente através de dispositivos clientes?

As inscrições do backend da aplicação são úteis quando tem de autenticar clientes antes de criar o registo. Também são úteis quando tem tags que devem ser criadas ou modificadas pelo backend da aplicação com base na lógica da aplicação. Para mais informações, vá à orientação de [registo backend] e orientação de [registo backend 2] páginas.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Qual é o modelo de segurança de entrega de notificação de push?

O Azure Notification Hubs utiliza um modelo de segurança baseado em assinaturas de [acesso partilhado.](../storage/common/storage-dotnet-shared-access-signature-part-1.md) Pode utilizar as fichas de assinatura de acesso partilhado ao nível do espaço de nome raiz ou ao nível do centro de notificação granular. Os tokens de assinatura de acesso partilhado podem ser definidos para seguir diferentes regras de autorização, por exemplo, para enviar permissões de mensagem ou para ouvir permissões de notificação. Para mais informações, consulte o documento do modelo de [segurança Notification Hubs.]

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Como devo lidar com carga útil sensível em notificações push?

Todas as notificações são entregues aos dispositivos-alvo pelo PNS da plataforma. Quando uma notificação é enviada para o Azure Notification Hubs, é processada e passada para os respetivos PNS.

Todas as ligações, desde o remetente até aos Centros de Notificação Azure até ao PNS, utilizem HTTPS.

> [!NOTE]
> Os Centros de Notificação Azure não registam a carga útil das mensagens.

Para enviar cargas sensíveis, recomendamos a utilização de um padrão de push seguro. O remetente entrega uma notificação de ping com um identificador de mensagem no dispositivo sem a carga útil sensível. Quando a aplicação do dispositivo recebe a carga útil, a aplicação chama diretamente uma API segura para recolher os detalhes da mensagem. Para obter um guia sobre como implementar este padrão, vá à página tutorial de Push Secure Push dos Centros de [Notificação.]

## <a name="operations"></a>Operações

### <a name="what-support-is-provided-for-disaster-recovery"></a>Que apoio é dado para a recuperação de desastres?

Fornecemos cobertura de recuperação de desastres de metadados na nossa extremidade (o nome Centros de Notificação, a cadeia de ligação e outras informações críticas). Quando um cenário de recuperação de desastres é desencadeado, os dados de registo são o *único segmento* da infraestrutura de Centros de Notificação que se perde. Tem de implementar uma solução para repovoar estes dados no seu novo hub pós-recuperação:

1. Crie um centro de notificações secundárias num centro de dados diferente. Recomendamos criar um desde o início para protegê-lo de um evento de recuperação de desastres que possa afetar as suas capacidades de gestão. Você também pode criar um no momento do evento de recuperação de desastres.

2. Povoe o centro de notificação secundária com as inscrições do seu centro de notificação principal. Não recomendamos tentar manter registos em ambos os centros e mantê-los sincronizados à medida que as inscrições chegam. Esta prática não funciona bem devido à tendência inerente de as inscrições expirarem do lado do PNS. Os Centros de Notificação limpa-os à medida que recebe feedback do PNS sobre registos expirados ou inválidos.  

Temos duas recomendações para backends de aplicações:

* Utilize um backend de aplicação que mantenha um determinado conjunto de inscrições no seu final. Pode então executar uma inserção a granel no centro de notificação secundária.
* Utilize um backend de aplicações que obtenha uma descarga regular de registos do centro de notificação primária como cópia de segurança. Pode então executar uma inserção a granel no centro de notificação secundária.

> [!NOTE]
> As inscrições A funcionalidade Exportação/Importação disponível no nível Standard é descrita no documento [de exportação/importação de registos.]

Se não tiver um backend, quando a aplicação começa nos dispositivos-alvo, eles realizam um novo registo no centro de notificação secundário. Eventualmente, o centro de notificação secundário terá todos os dispositivos ativos registados.

Haverá um período de tempo em que os dispositivos com aplicações não abertas não receberão notificações.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Todos os meus dados estão armazenados de forma encriptada?

O Azure Notification Hubs encripta todos os dados dos clientes em repouso, com exceção das etiquetas de registo. Por esta razão, não deve armazenar dados pessoais ou confidenciais utilizando etiquetas.

### <a name="is-there-audit-log-capability"></a>Existe capacidade de registo de auditoria?

Sim. Todas as operações de gestão de Centros de Notificação atualizam o Registo de Atividades do Azure ao qual está exposto no [portal Azure]. O Azure Activity Log oferece informações sobre as operações realizadas sobre os recursos nas suas subscrições. Utilizando o Registo de Atividades, pode determinar o quê, quem e quando para quaisquer operações de escrita (PUT, POST, DELETE) feitas para os recursos na sua subscrição. Também pode compreender o estado das operações e outros imóveis relevantes. No entanto. o Registo de Atividades não inclui a operação de leitura (GET).

## <a name="monitoring-and-troubleshooting"></a>Monitorização e resolução de problemas

### <a name="what-troubleshooting-capabilities-are-available"></a>Que capacidades de resolução de problemas estão disponíveis?

O Azure Notification Hubs fornece várias funcionalidades para resolução de problemas, especialmente para o cenário mais comum de notificações retiradas. Para mais detalhes, consulte o livro branco do Centro de [Notificação.]

### <a name="what-telemetry-features-are-available"></a>Que funcionalidades de telemetria estão disponíveis?

O Azure Notification Hubs permite visualizar dados de telemetria no [portal Azure.] Os detalhes das métricas estão disponíveis na página métrica [sinuosa] do Centro de Notificação.

Também pode aceder programáticamente às métricas. Para obter mais informações, veja os artigos seguintes:

- [Recuperar as métricas do Monitor Azure com .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). Esta amostra utiliza o nome de utilizador e a palavra-passe. Para utilizar um certificado, sobrecarregue o método FromServicePrincipal para fornecer um certificado, como mostra [este exemplo](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs). 
- [Obtenção de métricas e registos de atividade para um recurso](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Passagem da API de Monitorização Azure](../azure-monitor/platform/rest-api-walkthrough.md)

> [!NOTE]
> As notificações bem sucedidas significam simplesmente que as notificações push foram entregues aos PNS externos (por exemplo, APNs para iOS e macOS ou FCM para dispositivos Android). É da responsabilidade do PNS entregar as notificações aos dispositivos-alvo. Normalmente, o PNS não expõe métricas de entrega a terceiros.  

[Portal Azure]: https://portal.azure.com
[Preços dos Centros de Notificação]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[Centros de Notificação REST APIs]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Centros de notificação iniciando tutoriais]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Orientação de registo de backend]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Orientação de registo de backend 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Modelo de segurança de Centros de Notificação]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Tutorial de Push Seguro Hubs de Notificação]: https://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Resolução de problemas do Hubs de Notificação]: https://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Métricas de Centros de Notificação]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Registos Exportação/Importação]: https://docs.microsoft.com/azure/notification-hubs/export-modify-registrations-bulk
[Portal Azure]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/
