---
title: Azure Notification Hubs Frequentemente Perguntas (FAQs) | Microsoft Docs
description: Perguntas frequentes sobre a conceção e implementação de soluções nos Hubs de Notificação Azure.
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
keywords: notificações push, notificações push, notificações push iOS, notificações push android, ios push, push android push
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: e34fbdca51e7680a80c768e49bae891cb56dfa9d
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546453"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Notificações push com a Azure Notification Hubs: Perguntas frequentes

## <a name="general"></a>Geral

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Qual é a estrutura de recursos dos Centros de Notificação?

O Azure Notification Hubs tem dois níveis de recursos: centros e espaços de nome. Um hub é um único recurso de push que pode conter a informação de push cross-platform de uma aplicação. Um espaço com nomes é uma coleção de centros numa região. O mapeamento recomendado corresponde a um espaço com um nome. Dentro de um espaço de nome, você pode ter um centro de produção que funciona com a sua app de produção, um centro de testes que funciona com a sua app de teste, e assim por diante.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Qual é o modelo de preço dos Centros de Notificação?

Os mais recentes detalhes sobre preços podem ser encontrados na página de preços dos [Centros de Notificação.] Os Centros de Notificação são faturados ao nível do espaço de nome. (Para a definição de um espaço de nome, consulte "Qual é a estrutura de recursos dos Centros de Notificação?") Os Centros de Notificação oferecem três níveis:

* **Grátis**: Este nível é um bom ponto de partida para explorar as capacidades de pressão. Não é recomendado para aplicações de produção. Obtém 500 dispositivos e 1 milhão de pushs incluídos por espaço de identificação por mês, sem garantia de acordo de nível de serviço (SLA).
* **Básico**: Este nível (ou o nível Standard) é recomendado para aplicações de produção mais pequenas. Obtém-se 200.000 dispositivos e 10 milhões de pushs incluídos por espaço de identificação por mês como base.
* **Standard**: Este nível é recomendado para aplicações de produção médias a grandes. Obtém-se 10 milhões de dispositivos e 10 milhões de impulsos incluídos por espaço de identificação por mês como base. Inclui telemetria rica (dados adicionais sobre o estado do impulso fornecido).

Características de nível padrão:

* **Telemetria rica**: Pode utilizar os Centros de Notificação por Telemetria de Mensagens para acompanhar quaisquer pedidos de pressão e feedback do sistema de notificação da plataforma para depurar.
* **Multi-arrendamento**: Pode trabalhar com credenciais do Sistema de Notificação de Plataforma num nível de espaço de nome. Esta opção permite-lhe dividir facilmente os inquilinos em centros dentro do mesmo espaço de nome.
* **Push agendado**: Pode agendar notificações a serem enviadas a qualquer momento.
* **Operações a granel**: Permite registos A funcionalidade de exportação/importação, tal como descrito no documento [de exportação/importação de registos.]

### <a name="what-is-the-notification-hubs-sla"></a>O que é o Centro de Notificação SLA?

Para os níveis de Centros de Notificação Básica e Padrão, as aplicações devidamente configuradas podem enviar notificações push ou realizar operações de gestão de registo pelo menos 99,9% das vezes. Para saber mais sobre o SLA, aceda à página SLA dos [Centros de Notificação.](https://azure.microsoft.com/support/legal/sla/notification-hubs/)

> [!NOTE]
> Uma vez que as notificações push dependem de sistemas de notificação de plataforma de terceiros, como o Serviço de Notificações Push (APNs) da Apple e o Firebase Cloud Messaging (FCM) da Google, não existe nenhuma garantia de SLA para a entrega destas mensagens. Após a notificação, os Hubs enviam os lotes para sistemas de notificação da plataforma (SLA garantidos), é da responsabilidade dos Sistemas de Notificação da Plataforma entregar os pushes (sem SLA garantido).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Como faço o upgrade ou desclassifico o meu centro ou espaço de nome para um nível diferente?

Aceda ao **[portal Azure]**  >  **Notification Hubs Namespaces** ou **Centros de Notificação**. Selecione o recurso que pretende atualizar e vá para **o Nível de Preços**. Note os seguintes requisitos:

* O nível de preços atualizado aplica-se a *todos os* centros no espaço de nomes com os qual está a trabalhar.
* Se a contagem do dispositivo exceder o limite do nível para o qual está a degradar-se, tem de eliminar os dispositivos antes de reduzir.

## <a name="design-and-development"></a>Design e desenvolvimento

### <a name="which-server-side-platforms-do-you-support"></a>Que plataformas do lado do servidor suporta?

Os SDKs do servidor estão disponíveis para .NET, Java, Node.js, PHP e Python. As APIs de Notificação são baseadas em interfaces REST, para que possa trabalhar diretamente com ASE REST se estiver a utilizar plataformas diferentes ou não quiser dependência extra. Para mais informações, aceda à página [DE APIs do Centro de Notificação] REST.

### <a name="which-client-platforms-do-you-support"></a>Que plataformas de clientes apoia?

As notificações push são suportadas para [iOS](ios-sdk-get-started.md), [Android,](notification-hubs-android-push-notification-google-fcm-get-started.md) [Windows Universal,](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) [Windows Phone,](notification-hubs-windows-mobile-push-notifications-mpns.md) [Android China (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin [iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) e [Android](xamarin-notification-hubs-push-notifications-android-gcm.md), e [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Para mais informações, consulte a página [de tutoriais 'Iniciar' no Centro de Notificação.](ios-sdk-get-started.md)

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Suporta mensagens de texto, e-mail ou notificações web?

O Notification Hubs envia notificações para dispositivos que executam aplicações móveis. Não fornece capacidades de e-mail ou mensagem de texto. Os Centros de Notificação também não fornecem um serviço de entrega de notificações push no navegador fora da caixa. Os clientes podem implementar esta funcionalidade utilizando o SignalR em cima das plataformas suportadas do servidor. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Quantos dispositivos posso suportar se enviar notificações push através de Centros de Notificação?

Consulte a página de preços dos [Centros de Notificação] para obter mais informações sobre o número de dispositivos suportados.

Se precisar de suporte para mais de 10 milhões de dispositivos registados, tem de dividir os seus dispositivos em vários espaços de nome.

### <a name="how-many-push-notifications-can-i-send-out"></a>Quantas notificações de push posso enviar?

Dependendo do nível selecionado, os Hubs de Notificação Azure escalam automaticamente com base no número de notificações que fluem através do sistema.

> [!NOTE]
> O custo global de utilização pode aumentar com base no número de notificações push enviadas. Certifique-se de que está ciente dos limites de nível descritos na página de preços dos [Centros de Notificação.]

Os nossos clientes utilizam os Centros de Notificação para enviar milhões de notificações push diariamente. Não tem de fazer nada de especial para escalar o alcance das suas notificações push, desde que utilize os Hubs de Notificação Azure.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Quanto tempo demora a enviar notificações push para chegar ao meu dispositivo?

Num cenário de utilização normal, em que a carga de entrada é consistente e uniforme, os Hubs de Notificação Azure podem processar pelo menos *1 milhão de notificações push envios por minuto*. Esta taxa pode variar dependendo do número de tags, da natureza dos envios recebidos, e de outros fatores externos.

Durante o tempo estimado de entrega, o serviço calcula os alvos por plataforma e encaminha as mensagens para o Serviço de Notificação push (PNS) com base nas etiquetas registadas ou expressões de etiquetas. É da responsabilidade do PNS enviar notificações para o dispositivo.

O PNS não garante qualquer SLA para a entrega de notificações. No entanto, a maioria das notificações push são entregues aos dispositivos-alvo dentro de poucos minutos (normalmente dentro de 10 minutos) a partir do momento em que são enviados para Os Centros de Notificação. Algumas notificações podem levar mais tempo.

> [!NOTE]
> O Azure Notification Hubs tem uma política em vigor para deixar cair quaisquer notificações push que não sejam entregues ao PNS dentro de 30 minutos. Este atraso pode acontecer por uma série de razões, mas geralmente porque o PNS está a estrangular a sua aplicação.

### <a name="is-there-any-latency-guarantee"></a>Há alguma garantia de latência?

Devido à natureza das notificações push (são entregues por um PNS externo e específico da plataforma), não existe garantia de latência. Normalmente, a maioria das notificações push são entregues dentro de poucos minutos.

### <a name="where-does-azure-notification-hubs-store-data"></a>Onde é que o Azure Notification Hubs armazena dados?

A Azure Notification Hubs armazena dados de registo de clientes na região selecionada pelo cliente. Os Centros de Notificação fornecem cobertura de recuperação de desastres de metadados (o nome dos Centros de Notificação, a cadeia de ligação e outras informações críticas). Para todas as regiões, com exceção do Brasil Sul e Sudeste Asiático, o backup de metadados é hospedado numa região diferente (geralmente a região emparelhada Azure). Para as regiões do Sul e do Sudeste Asiático do Brasil, os backups são armazenados na mesma região para acomodar os requisitos de residência de dados para estas regiões.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>O que devo considerar ao desenhar uma solução com espaços de nome e centros de notificação?

#### <a name="mobile-appenvironment"></a>Aplicativo/ambiente móvel

* Use um hub de notificação por aplicação móvel, por ambiente.
* Em um cenário multi-inquilino, cada inquilino deve ter um centro separado.
* Nunca partilhe o mesmo centro de notificação para ambientes de produção e teste. Esta prática pode causar problemas no envio de notificações. (A Apple oferece pontos finais sandbox e Production Push, cada um com credenciais separadas.)
* Por predefinição, pode enviar notificações de teste para os seus dispositivos registados através do portal Azure ou do componente integrado Azure no Visual Studio. O limiar é definido para 10 dispositivos que são selecionados aleatoriamente a partir do conjunto de registo.

> [!NOTE]
> Se o seu hub foi originalmente configurado com um certificado apple sandbox e foi reconfigurado para usar um certificado de produção da Apple, os tokens originais do dispositivo são inválidos. Fichas inválidas fazem com que os impulsos falhem. Separe os seus ambientes de produção e teste e use diferentes centros para diferentes ambientes.

#### <a name="pns-credentials"></a>Credenciais PNS

Quando uma aplicação móvel é registada com o portal de desenvolvimento de uma plataforma (por exemplo, Apple ou Google), um identificador de aplicações e fichas de segurança são enviados. O backend da aplicação fornece estes tokens ao PNS da plataforma para que as notificações push possam ser enviadas para dispositivos. Os tokens de segurança podem ser sob a forma de certificados (por exemplo, Apple iOS ou Windows Phone) ou chaves de segurança (por exemplo, Google Android ou Windows). Devem ser configurados nos centros de notificação. A configuração é normalmente feita ao nível do centro de notificação, mas também pode ser feita ao nível do espaço de identificação num cenário multi-inquilino.

#### <a name="namespaces"></a>Espaços de nomes

Os espaços de nome podem ser utilizados para o agrupamento de implantação. Também podem ser usados para representar todos os centros de notificação para todos os inquilinos da mesma app num cenário multi-inquilino.

#### <a name="geo-distribution"></a>Geo-distribuição

A geo-distribuição nem sempre é crítica em cenários de notificação push. Vários PNSes (por exemplo, APNs ou FCM) que entregam notificações push aos dispositivos não são distribuídos uniformemente.

Se tiver uma aplicação que é usada globalmente, pode criar centros em diferentes espaços de nome, utilizando o serviço De notificação hubs em diferentes regiões do Azure em todo o mundo.

> [!NOTE]
> Não recomendamos este acordo porque aumenta o custo de gestão, particularmente para as inscrições. Só deve ser feito se houver uma necessidade explícita.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Devo fazer registos a partir do backend da aplicação ou diretamente através de dispositivos clientes?

As inscrições no backend da app são úteis quando tem de autenticar os clientes antes de criar o registo. Também são úteis quando tem etiquetas que devem ser criadas ou modificadas pelo backend da app com base na lógica da aplicação. Para mais informações, aceda à [orientação de Registo de Backend] e [à orientação de Registo de Backend 2] páginas.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Qual é o modelo de segurança de entrega de notificações push?

O Azure Notification Hubs utiliza um modelo de segurança baseado em assinaturas de [acesso partilhado.](../storage/common/storage-sas-overview.md) Pode utilizar os tokens de assinatura de acesso partilhado ao nível do espaço de nome raiz ou ao nível do centro de notificação granular. Os tokens de assinatura de acesso partilhado podem ser definidos para seguir diferentes regras de autorização, por exemplo, para enviar permissões de mensagens ou para ouvir permissões de notificação. Para mais informações, consulte o documento do [modelo de segurança Do Centro de Notificação.]

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Como devo lidar com a carga útil sensível em notificações push?

Todas as notificações são entregues aos dispositivos-alvo pelo PNS da plataforma. Quando uma notificação é enviada para os Hubs de Notificação do Azure, é processada e transmitida para os respetivos PNS.

Todas as ligações, desde o remetente até aos Hubs de Notificação Azure até ao PNS, utilize HTTPS.

> [!NOTE]
> Os Hubs de Notificação Azure não registam a carga útil de mensagens.

Para enviar cargas sensíveis, recomendamos a utilização de um padrão Secure Push. O remetente entrega uma notificação de ping com um identificador de mensagem para o dispositivo sem a carga útil sensível. Quando a aplicação do dispositivo recebe a carga útil, a aplicação chama uma API segura diretamente para recolher os detalhes da mensagem. Para obter um guia sobre como implementar este padrão, aceda à página tutorial de Aviso Seguro de Aviso de Aviso de [Notificação.]

## <a name="operations"></a>Operações

### <a name="what-support-is-provided-for-disaster-recovery"></a>Que apoio é dado à recuperação de catástrofes?

Fornecemos cobertura de recuperação de desastres de metadados no nosso final (o nome Dos Hubs de Notificação, a cadeia de ligação e outras informações críticas). Quando um cenário de recuperação de desastres é desencadeado, os dados de registo são o *único segmento* da infraestrutura de Centros de Notificação que se perde. Tem de implementar uma solução para repovoar estes dados no seu novo hub após a recuperação:

1. Crie um centro de notificações secundárias num centro de dados diferente. Recomendamos criar um desde o início para protegê-lo de um evento de recuperação de desastres que pode afetar as suas capacidades de gestão. Você também pode criar um no momento do evento de recuperação de desastres.

2. Mantenha o centro de notificação secundário em sintonia com o hub de notificação primário utilizando uma das seguintes opções:

   * Utilize um backend de aplicações que simultaneamente cria e atualiza instalações em ambos os centros de notificação. As instalações permitem especificar o seu próprio identificador de dispositivos único, tornando-o mais adequado para o cenário de replicação. Para mais informações, consulte este [código de amostra.](https://github.com/Azure/azure-notificationhubs-dotnet/tree/main/Samples/RedundantHubSample)
   * Use um backend de aplicativos que obtenha uma descarga regular de registos do centro de notificação primária como uma cópia de segurança. Em seguida, pode efetuar uma inserção a granel no centro de notificação secundária.

O centro de notificação secundária pode acabar com instalações/registos caducados. Quando o impulso é feito para uma pega expirada, os Centros de Notificação limpam automaticamente o registo de instalação/registo associado com base na resposta recebida do servidor PNS. Para limpar registos expirados de um centro de notificação secundário, adicione uma lógica personalizada que processa o feedback de cada envio. Em seguida, expire a instalação/registo no centro de notificação secundária.

Se não tiver um backend, quando a aplicação começa nos dispositivos-alvo, eles realizam um novo registo no centro de notificação secundária. Eventualmente, o centro de notificação secundária terá todos os dispositivos ativos registados.

Haverá um período de tempo em que os dispositivos com apps por abrir não receberão notificações.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Todos os meus dados são armazenados em formato encriptado?

O Azure Notification Hubs encripta todos os dados do cliente em repouso, com exceção das etiquetas de registo. Por esta razão, não deve armazenar dados pessoais ou confidenciais usando etiquetas.

### <a name="is-there-audit-log-capability"></a>Existe capacidade de registo de auditoria?

Sim. Todas as operações de gestão de Centros de Notificação atualizam o Registo de Atividades do Azure ao qual está exposto no [portal Azure]. O Azure Activity Log oferece informações sobre as operações realizadas sobre os recursos nas suas subscrições. Utilizando o Registo de Atividades, pode determinar o quê, quem e quando para quaisquer operações de escrita (PUT, POST, DELETE) feitas para os recursos na sua subscrição. Também pode compreender o estado das operações e outras propriedades relevantes. No entanto. o Registo de Atividades não inclui a operação de leitura (GET).

## <a name="monitoring-and-troubleshooting"></a>Monitorizar e resolver problemas

### <a name="what-troubleshooting-capabilities-are-available"></a>Que capacidades de resolução de problemas estão disponíveis?

O Azure Notification Hubs fornece várias funcionalidades para a resolução de problemas, especialmente para o cenário mais comum de notificações retiradas. Para mais informações, consulte o Documento Branco [de Resolução de Problemas dos Centros de Notificação.]

### <a name="what-telemetry-features-are-available"></a>Que funcionalidades de telemetria estão disponíveis?

Os Hubs de Notificação Azure permitem visualizar dados de telemetria no [portal Azure]. Os detalhes das métricas estão disponíveis na página [de Métricas de Centros de Notificação.]

Também pode aceder programáticamente às métricas. Para obter mais informações, veja os seguintes artigos:

- [Recuperar as métricas do Monitor Azure com .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). Esta amostra utiliza o nome de utilizador e a palavra-passe. Para utilizar um certificado, sobrecarregar o método FromServicePrincipal para fornecer um certificado como mostrado neste [exemplo](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs). 
- [Obtenção de métricas e registos de atividade para um recurso](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Instruções da API REST de Monitorização do Azure](../azure-monitor/platform/rest-api-walkthrough.md)

> [!NOTE]
> As notificações bem sucedidas significam simplesmente que foram entregues notificações push para os PNS externos (por exemplo, APNs para iOS e macOS ou FCM para dispositivos Android). É da responsabilidade do PNS entregar as notificações aos dispositivos-alvo. Normalmente, o PNS não expõe as métricas de entrega a terceiros.  

[Portal do Azure]: https://portal.azure.com
[Preços dos hubs de notificação]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[Centros de Notificação REST APIs]: /previous-versions/azure/reference/dn530746(v=azure.100)
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Orientação de registo de backend]: /previous-versions/azure/azure-services/dn743807(v=azure.100)
[Orientação de registo de backend 2]: /previous-versions/azure/azure-services/dn530747(v=azure.100)
[Modelo de segurança de Centros de Notificação]: /previous-versions/azure/azure-services/dn495373(v=azure.100)
[Tutorial de push seguro de centros de notificação]: ./notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md
[Resolução de problemas dos Hubs de Notificação]: ./notification-hubs-push-notification-fixer.md
[Métricas de Centros de Notificação]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Registos Exportação/Importação]: ./export-modify-registrations-bulk.md
[Portal do Azure]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/
