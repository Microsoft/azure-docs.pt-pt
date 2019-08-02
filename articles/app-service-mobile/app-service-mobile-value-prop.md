---
title: Sobre Aplicações Móveis no Serviço de Aplicações do Azure
description: Conheça as vantagens que o Serviço de Aplicações traz para as suas aplicações móveis.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: yochayk
editor: ''
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 72614a7f804f404e3e560d944db7b3d2e496c383
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705676"
---
# <a name="getting-started"> </a>Sobre Aplicações Móveis no Serviço de Aplicações do Azure

> [!NOTE]
> Visual Studio App Center está investindo em serviços novos e integrados central para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem. Confira [app Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-value-prop) hoje.
>

O Serviço de Aplicações do Azure é uma oferta de [plataforma como um serviço](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) completamente gerida para programadores profissionais. O serviço oferece um conjunto avançado de capacidades para cenários Web, móveis e de integração. 

A funcionalidade Aplicações Móveis do Serviço de Aplicações do Azure oferece aos programadores de empresas e aos integradores de sistema uma plataforma de programação de aplicações móveis altamente dimensionável e globalmente disponível.

![Descrição geral visual das capacidades de Aplicações Móveis](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Porquê Mobile Apps?
Com a funcionalidade Aplicações Móveis, pode:

* **Crie aplicativos nativos e de plataforma cruzada**: Independentemente de você estar criando aplicativos nativos do iOS, Android e Windows ou aplicativos Xamarin ou Cordova (PhoneGap) de plataforma cruzada, você pode aproveitar o serviço de aplicativo usando SDKs nativos.
* **Conecte-se aos seus sistemas empresariais**: Com o recurso aplicativos móveis, você pode adicionar entrada corporativa em minutos e conectar-se a seus recursos corporativos locais ou de nuvem.
* **Crie aplicativos prontos para uso offline com sincronização de dados**: Torne sua força de trabalho móvel mais produtiva criando aplicativos que trabalham offline e use aplicativos móveis para sincronizar dados em segundo plano quando houver conectividade com qualquer uma das suas fontes de dados empresariais ou APIs SaaS (software como serviço).
* **Notificações por push para milhões em segundos**: Envolva seus clientes com notificações por push instantâneas em qualquer dispositivo, personalizada para suas necessidades e enviadas quando o tempo for certo.

## <a name="mobile-apps-features"></a>Funcionalidades das Aplicações Móveis
As seguintes funcionalidades são importantes para o desenvolvimento móvel preparados para nuvem:

* **Autenticação e autorização**: Suporte para provedores de identidade, incluindo Azure Active Directory para autenticação corporativa, além de provedores sociais, como o Facebook, Google, Twitter e contas da Microsoft. As Aplicações Móveis oferecem um serviço OAuth 2.0 para cada fornecedor. Também é possível integrar o SDK para o fornecedor de identidade para funcionalidades específicas do fornecedor.

    Saiba mais sobre as nossas [funcionalidades de autenticação].

* **Acesso a dados**: Os aplicativos móveis fornecem uma fonte de dados OData V3 para dispositivos móveis que está vinculada ao banco de dados SQL do Azure ou a um SQL Server local. Uma vez que este serviço pode ser baseado em Entity Framework, pode integrar facilmente com outros fornecedores de dados NoSQL e SQL, incluindo [Armazenamento de Tabelas do Azure], MongoDB, [BD do Cosmos para o Azure] e fornecedores de API SaaS, como o Office 365 e Salesforce.com.

* **Sincronização offline**: Os SDKs de cliente facilitam a criação de aplicativos móveis robustos e responsivos que operam com um conjunto de um DataSet offline. Pode sincronizar este conjunto de dados automaticamente com os dados de back-end, incluindo o suporte de resolução de conflitos.

  Saiba mais sobre as [funcionalidades de dados].

* **Notificações por push**: Os SDKs de cliente integram-se perfeitamente com os recursos de registro dos hubs de notificação do Azure, para que você possa enviar notificações por push a milhões de usuários simultaneamente.

  Saiba mais sobre as [funcionalidades de notificação push].

* **SDKs do cliente**: Há um conjunto completo de SDKs de cliente que abrangem o desenvolvimento nativo ([Ios], [Android]e [Windows]), desenvolvimento de plataforma cruzada ([Xamarin.iOS e Xamarin.Android], [Xamarin.Forms]) e aplicativo híbrido desenvolvimento ([Apache Cordova]). Cada SDK do Cliente está disponível com uma licença MIT e é open source.

## <a name="azure-app-service-features"></a>Funcionalidades do Serviço de Aplicações do Azure
As seguintes funcionalidades da plataforma são úteis para sites de produção móveis:

* **Dimensionamento**automático: Com o serviço de aplicativo, você pode aumentar ou reduzir rapidamente para lidar com qualquer carga de entrada do cliente. Selecione manualmente o número e tamanho das VMs ou configure a escala automática de forma a dimensionar o back-end da sua aplicação móvel com base na carga ou na agenda.

  Saiba mais sobre a [escala automática].

* **Ambientes de preparo**: O serviço de aplicativo pode executar várias versões do seu site, para que você possa executar testes A/B, testar em produção como parte de um plano de DevOps maior e fazer um preparo in-loco de um novo back-end.

  Saiba mais sobre os [ambientes de teste].

* **Implantação contínua**: O serviço de aplicativo pode ser integrado aos sistemas SCM ( _Gerenciamento de controle de origem_ ) comuns, permitindo que você implante facilmente uma nova versão do back-end.

  Saiba mais sobre as [opções de implementação](../app-service/deploy-local-git.md).

* **Rede virtual**: O serviço de aplicativo pode se conectar a recursos locais usando a rede virtual, o Azure ExpressRoute ou conexões híbridas.

  Saiba mais sobre [ligações híbridas], [redes virtuais] e [ExpressRoute].

* **Ambientes isolados e dedicados**: Para executar Azure App aplicativos de serviço com segurança, você pode executar o serviço de aplicativo em um ambiente totalmente isolado e dedicado. Este ambiente é ideal para cargas de trabalho de aplicações que exijam uma escala, isolamento ou um acesso de rede elevados.

  Saiba mais sobre os [ambientes do Serviço de Aplicações].

## <a name="next-steps"></a>Passos seguintes

Para começar a utilizar as Aplicações Móveis no Serviço de Aplicações do Azure, conclua o tutorial [introdução] . O tutorial abrange as noções básicas da produção de um back-end móvel e cliente da sua preferência. Inclui também a integração de autenticação, sincronização offline e notificações push. Pode concluir o tutorial várias vezes, uma vez para cada aplicação de cliente.

Para obter mais informações sobre as Aplicações Móveis, veja o nosso [mapa de aprendizagem].
Para obter mais informações sobre a plataforma do Serviço de Aplicações do Azure, veja [Serviço de Aplicações do Azure].

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[introdução]: app-service-mobile-ios-get-started.md
[Armazenamento de tabelas do Azure]:../cosmos-db/table-storage-how-to-use-dotnet.md
[BD do Cosmos para o Azure]: ../cosmos-db/sql-api-get-started.md
[funcionalidades de autenticação]: ./app-service-mobile-auth.md
[funcionalidades de dados]: ./app-service-mobile-offline-data-sync.md
[funcionalidades de notificação push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS e Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[escala automática]: ../app-service/web-sites-scale.md
[ambientes de teste]: ../app-service/deploy-staging-slots.md
[ligações híbridas]: ../biztalk-services/integration-hybrid-connection-overview.md
[redes virtuais]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[ambientes do Serviço de Aplicações]: ../app-service/environment/intro.md
[mapa de aprendizagem]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
[Serviço de Aplicações do Azure]: ../app-service/overview.md
