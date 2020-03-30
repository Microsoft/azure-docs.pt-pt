---
title: Sobre aplicativos móveis
description: Conheça as vantagens que o Serviço de Aplicações traz para as suas aplicações móveis.
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.tgt_pltfrm: mobile-multiple
ms.topic: overview
ms.date: 06/25/2019
ms.openlocfilehash: 33548f202046310b91fc79d38ac7d8fb18a8727e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79499419"
---
# <a name="about-mobile-apps-in-azure-app-service"></a><a name="getting-started"> </a>Sobre Aplicações Móveis no Serviço de Aplicações do Azure

O Serviço de Aplicações do Azure é uma oferta de [plataforma como um serviço](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) completamente gerida para programadores profissionais. O serviço oferece um conjunto avançado de capacidades para cenários Web, móveis e de integração. 

A funcionalidade Aplicações Móveis do Serviço de Aplicações do Azure oferece aos programadores de empresas e aos integradores de sistema uma plataforma de programação de aplicações móveis altamente dimensionável e globalmente disponível.

![Descrição geral visual das capacidades de Aplicações Móveis](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Porquê Mobile Apps?
Com a funcionalidade Aplicações Móveis, pode:

* **Compilar aplicações de plataformas nativas e cruzadas**: esteja a compilar aplicações nativas Windows, Android e iOS ou aplicações de plataformas cruzadas Xamarin ou Cordova (PhoneGap), pode tirar partido do Serviço de Aplicações com SDKs nativos.
* **Ligar aos seus sistemas empresariais**: com a funcionalidade Aplicações Móveis, pode adicionar um início de sessão empresarial em minutos e ligar-se à sua empresa no local ou aos recursos na cloud.
* **Compilar aplicações preparadas para trabalho offline com sincronização de dados**: torne a sua mão-de-obra móvel mais produtiva através da compilação de aplicações que funcionam offline e utilize Mobile Apps para sincronizar dados em segundo plano quando existe conectividade com qualquer uma das suas fontes de dados empresariais ou APIs software como um serviço (SaaS).
* **Empurre notificações para milhões em segundos**: Envolva os seus clientes com notificações instantâneas de push em qualquer dispositivo, personalizados às suas necessidades, e enviados quando for a hora certa.

## <a name="mobile-apps-features"></a>Funcionalidades das Aplicações Móveis
As seguintes funcionalidades são importantes para o desenvolvimento móvel preparados para nuvem:

* **Autenticação e autorização**: suporte para fornecedores de identidade, incluindo o Azure Active Directory, uma autenticação empresarial e fornecedores de redes sociais, como as contas do Facebook, do Google, do Twitter e da Microsoft. As Aplicações Móveis oferecem um serviço OAuth 2.0 para cada fornecedor. Também é possível integrar o SDK para o fornecedor de identidade para funcionalidades específicas do fornecedor.

    Saiba mais sobre as nossas [funcionalidades de autenticação].

* **Acesso a dados**: as Aplicações Móveis oferecem uma origem de dados OData v3 compatível com dispositivos móveis que está ligada à Base de Dados SQL do Azure ou a um SQL Server no local. Uma vez que este serviço pode ser baseado em Entity Framework, pode integrar facilmente com outros fornecedores de dados NoSQL e SQL, incluindo [Armazenamento de Tabelas do Azure], MongoDB, [Azure Cosmos DB] e fornecedores de API SaaS, como o Office 365 e Salesforce.com.

* **Sincronização offline**: os SDKs do cliente facilitam a compilação de aplicações móveis robustas e reativas que funcionam com um conjunto de dados offline. Pode sincronizar este conjunto de dados automaticamente com os dados de back-end, incluindo o suporte de resolução de conflitos.

  Saiba mais sobre as [funcionalidades de dados].

* **Notificações push**: os SDKs do cliente estão totalmente integrados com as capacidades de registo dos Hubs de Notificação do Azure, para que possa enviar notificações push para milhões de utilizadores em simultâneo.

  Saiba mais sobre as [funcionalidades de notificação push].

* **SDKs do Cliente**: existe um conjunto completo de SDKs do cliente que abrangem o desenvolvimento nativo ([iOS], [Android] e [Windows]), desenvolvimento de plataformas cruzadas ([Xamarin.iOS e Xamarin.Android], [Xamarin.Forms]) e desenvolvimento de aplicações híbridas ([Apache Cordova]). Cada SDK do Cliente está disponível com uma licença MIT e é open source.

## <a name="azure-app-service-features"></a>Funcionalidades do Serviço de Aplicações do Azure
As seguintes funcionalidades da plataforma são úteis para sites de produção móveis:

* **Escala automática**: com o Serviço de Aplicações pode aumentar verticalmente ou horizontalmente de forma rápida, para lidar com qualquer carga de cliente a receber. Selecione manualmente o número e tamanho das VMs ou configure a escala automática de forma a dimensionar o back-end da sua aplicação móvel com base na carga ou na agenda.

  Saiba mais sobre a [escala automática].

* **Ambientes de teste**: o Serviço de Aplicações pode executar várias versões do seu site, para que possa executar testes A/B, testar a produção como parte de um plano DevOps maior e realizar testes no local de um novo back-end.

  Saiba mais sobre os [ambientes de teste].

* **Implementação contínua**: o Serviço de Aplicações pode ser integrado com sistemas de _gestão de controlo de origem_ (SCM) comuns, permitindo-lhe facilmente implementar uma nova versão do seu back-end.

  Saiba mais sobre as [opções de implementação](../app-service/deploy-local-git.md).

* **Redes virtuais**: o Serviço de Aplicações pode ligar-se a recursos no local através de uma rede virtual, o Azure ExpressRoute ou ligações híbridas.

  Saiba mais sobre [ligações híbridas], [redes virtuais] e [ExpressRoute].

* **Ambientes isolados e dedicados**: para uma execução segura de aplicações do Serviço de Aplicações do Azure, pode executar o Serviço de Aplicações num ambiente completamente isolado e dedicado. Este ambiente é ideal para cargas de trabalho de aplicações que exijam uma escala, isolamento ou um acesso de rede elevados.

  Descubra mais sobre [ambientes de Serviço de Aplicações.]

## <a name="next-steps"></a>Passos seguintes

Para começar a utilizar as Aplicações Móveis no Serviço de Aplicações do Azure, conclua o tutorial [introdução] . O tutorial abrange as noções básicas da produção de um back-end móvel e cliente da sua preferência. Inclui também a integração de autenticação, sincronização offline e notificações push. Pode concluir o tutorial várias vezes, uma vez para cada aplicação de cliente.

Para obter mais informações sobre as Aplicações Móveis, veja o nosso [mapa de aprendizagem].
Para mais informações sobre a plataforma Azure App Service, consulte [o Azure App Service].

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[começar]: app-service-mobile-ios-get-started.md
[Armazenamento de mesa azure]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[funcionalidades de autenticação]: ./app-service-mobile-auth.md
[funcionalidades de dados]: ./app-service-mobile-offline-data-sync.md
[funcionalidades de notificação push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS e Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[autoscalcificação]: ../app-service/manage-scale-up.md
[ambientes de teste]: ../app-service/deploy-staging-slots.md
[conexões híbridas]: ../biztalk-services/integration-hybrid-connection-overview.md
[redes virtuais]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[Ambientes de serviço de aplicativos]: ../app-service/environment/intro.md
[mapa de aprendizagem]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
[Serviço de Aplicações do Azure]: ../app-service/overview.md
