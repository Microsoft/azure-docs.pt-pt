---
title: Sobre Aplicações Móveis no Serviço de Aplicações do Azure
description: Conheça as vantagens que o Serviço de Aplicações traz para as suas aplicações móveis.
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: yochayk
editor: ''
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: f3eb781e7f84e8cf03a975f7cb77f6a7ef074d44
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771814"
---
# <a name="getting-started"> </a>Sobre Aplicações Móveis no Serviço de Aplicações do Azure
O Serviço de Aplicações do Azure é uma oferta de [plataforma como um serviço](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) completamente gerida para programadores profissionais. O serviço oferece um conjunto avançado de capacidades para cenários Web, móveis e de integração. 

A funcionalidade Aplicações Móveis do Serviço de Aplicações do Azure oferece aos programadores de empresas e aos integradores de sistema uma plataforma de programação de aplicações móveis altamente dimensionável e globalmente disponível.

![Descrição geral visual das capacidades de Aplicações Móveis](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Porquê Mobile Apps?
Com a funcionalidade Aplicações Móveis, pode:

* **Crie aplicações nativas e multiplataformas**: Se estiver criando nativa no iOS, Android e Windows aplicações ou plataformas cruzadas Xamarin ou Cordova (PhoneGap) aplicações, pode tirar partido do serviço de aplicações com SDKs nativos.
* **Ligar aos seus sistemas empresariais**: Com a funcionalidade de aplicações móveis, pode adicionar o início de sessão empresarial em minutos e ligar a sua empresa no local ou recursos na cloud.
* **Crie aplicações prontas para offline com sincronização de dados**: Torne a sua força de trabalho móvel mais produtivos ao criar aplicações que funcionam offline e utilizam Mobile Apps para sincronizar dados em segundo plano quando existe conectividade com qualquer uma das suas origens de dados empresariais ou software como serviço (SaaS) e APIs.
* **Notificações push para milhões em segundos**: Cative os clientes com notificações push instantâneas em qualquer dispositivo, personalizadas para as suas necessidades e enviadas a hora ideal.

## <a name="mobile-apps-features"></a>Funcionalidades das Aplicações Móveis
As seguintes funcionalidades são importantes para o desenvolvimento móvel preparados para nuvem:

* **Autenticação e autorização**: Suporte para fornecedores de identidade, incluindo o Azure Active Directory, uma autenticação empresarial e fornecedores de redes sociais, como as contas do Facebook, Google, Twitter e Microsoft. As Aplicações Móveis oferecem um serviço OAuth 2.0 para cada fornecedor. Também é possível integrar o SDK para o fornecedor de identidade para funcionalidades específicas do fornecedor.

    Saiba mais sobre as nossas [funcionalidades de autenticação].

* **Acesso a dados**: Aplicações móveis fornece a origem de dados OData v3 uma adequada a dispositivos móveis que está ligada à base de dados do Azure SQL ou um servidor SQL no local. Uma vez que este serviço pode ser baseado em Entity Framework, pode integrar facilmente com outros fornecedores de dados NoSQL e SQL, incluindo [Armazenamento de Tabelas do Azure], MongoDB, [BD do Cosmos para o Azure] e fornecedores de API SaaS, como o Office 365 e Salesforce.com.

* **Sincronização offline**: O cliente SDKs facilitam a criação de aplicações móveis robustas e reativas que funcionam com um conjunto de dados offline. Pode sincronizar este conjunto de dados automaticamente com os dados de back-end, incluindo o suporte de resolução de conflitos.

  Saiba mais sobre as [funcionalidades de dados].

* **Notificações push**: Os SDKs de cliente totalmente integram com as capacidades de registo dos Notification Hubs do Azure, para que possa enviar notificações push para milhões de utilizadores em simultâneo.

  Saiba mais sobre as [funcionalidades de notificação push].

* **SDKs do cliente**: Existe um conjunto completo de SDKs de cliente que abrangem o desenvolvimento nativo ([iOS], [Android], e [Windows]), desenvolvimento de plataformas cruzadas ([Xamarin.iOS e Xamarin.Android], [Xamarin.Forms]) e o desenvolvimento de aplicações híbridas ([Apache Cordova]). Cada SDK do Cliente está disponível com uma licença MIT e é open source.

## <a name="azure-app-service-features"></a>Funcionalidades do Serviço de Aplicações do Azure
As seguintes funcionalidades da plataforma são úteis para sites de produção móveis:

* **Dimensionamento automático**: Com o serviço de aplicações, pode rapidamente aumentar verticalmente ou aumentar horizontalmente para lidar com qualquer carga de cliente recebidos. Selecione manualmente o número e tamanho das VMs ou configure a escala automática de forma a dimensionar o back-end da sua aplicação móvel com base na carga ou na agenda.

  Saiba mais sobre a [escala automática].

* **Ambientes de teste**: Serviço de aplicações pode executar várias versões do seu site, para que possa executar A / B, testar, testar na produção como parte de um plano DevOps maior e testes no local de um novo back-end.

  Saiba mais sobre os [ambientes de teste].

* **Implementação contínua**: Serviço de aplicações pode ser integrado comuns _gestão de controlo de origem_ sistemas (SCM), permitindo-lhe implementar facilmente uma nova versão do seu back-end.

  Saiba mais sobre as [opções de implementação](../app-service/deploy-local-git.md).

* **Redes virtuais**: Serviço de aplicações pode ligar a recursos no local com as ligações híbridas, Azure ExpressRoute ou rede virtual.

  Saiba mais sobre [ligações híbridas], [redes virtuais] e [ExpressRoute].

* **Ambientes isolados e dedicados**: Para execução segura de aplicações do App Service do Azure, pode executar o serviço de aplicações num ambiente completamente isolado e dedicado. Este ambiente é ideal para cargas de trabalho de aplicações que exijam uma escala, isolamento ou um acesso de rede elevados.

  Saiba mais sobre os [ambientes do Serviço de Aplicações].

## <a name="next-steps"></a>Passos Seguintes

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
