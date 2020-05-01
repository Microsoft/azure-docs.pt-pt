---
title: Descrição geral
description: Saiba como o App Service do Azure o ajuda a desenvolver e alojar Web Apps
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 04/30/2020
ms.custom: mvc, seodec18
ms.openlocfilehash: 7db55a420a9789ef15a5296a6b0200d6b8910ec6
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597863"
---
# <a name="app-service-overview"></a>Descrição geral do Serviço de Aplicações

*O Azure App Service* é um serviço baseado em HTTP para hospedar aplicações web, APIs REST e back ends móveis. Pode desenvolver no seu idioma favorito, seja .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python. As aplicações funcionam e escalam com facilidade tanto em ambientes baseados em Windows como em Linux. Para ambientes baseados em Linux, veja [Serviço de Aplicações no Linux](containers/app-service-linux-intro.md). 

O Serviço de Aplicações não só adiciona a potência do Microsoft Azure à sua aplicação, como segurança, equilíbrio de carga, autoscalcificação e gestão automatizada. Também pode tirar partido das suas capacidades de DevOps, tais como a implantação contínua da Azure DevOps, GitHub, Docker Hub, e outras fontes, gestão de pacotes, ambientes de encenação, domínio personalizado e certificados TLS/SSL. 

Com o Serviço de Aplicações, paga pelos recursos de computação do Azure que utilizar. Os recursos computacionais que utiliza são determinados pelo plano de Serviço de _Aplicações_ em que executa as suas aplicações. Para mais informações, consulte a visão geral dos planos do [Azure App Service.](overview-hosting-plans.md)

## <a name="why-use-app-service"></a>Porquê utilizar o App Service?

Aqui estão algumas funcionalidades chave do Serviço de Aplicações:

* **Múltiplas línguas e quadros** - O App Service tem suporte de primeira classe para ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP ou Python. Também pode executar o [PowerShell e outros scripts ou executáveis](webjobs-create.md) como serviços em segundo plano.
* **Ambiente de produção gerido** - O Serviço de Aplicações [remende e mantém automaticamente o SISTEMA e os quadros linguísticos](overview-patch-os-runtime.md) para si. Passe o tempo escrevendo grandes apps e deixe o Azure preocupar-se com a plataforma.
* **Otimização de DevOps** – Configure a [integração e implementação contínuas](deploy-continuous-deployment.md) com o Azure DevOps, GitHub, BitBucket, Docker Hub ou Azure Container Registry. Promova atualizações através de [ambientes de teste](deploy-staging-slots.md). Faça a gestão das suas aplicações no App Service utilizando o [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou a [interface de linha de comandos (CLI) de várias plataformas](/cli/azure/install-azure-cli).
* **Dimensionamento global com elevada disponibilidade** – aumente [verticalmente](manage-scale-up.md) ou [horizontalmente](../monitoring-and-diagnostics/insights-how-to-scale.md) de forma manual ou automática. Aloje as aplicações em qualquer lugar da infraestrutura do datacenter global da Microsoft e o App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promete elevada disponibilidade.
* **Ligações a plataformas SaaS e dados no local** – escolha entre mais de 50 [conectores](../connectors/apis-list.md) para sistemas empresariais (como SAP), serviços SaaS (como o Salesforce) e serviços Internet (como o Facebook). Aceda a dados no local ao utilizar [Ligações Híbridas](app-service-hybrid-connections.md) e [Azure Virtual Networks](web-sites-integrate-with-vnet.md).
* **Segurança e conformidade** – o App Service está [em conformidade com ISO, SOC e PCI](https://www.microsoft.com/en-us/trustcenter). Autentique os utilizadores com o [Azure Active Directory](configure-authentication-provider-aad.md) ou com início de sessão social ([Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) e [ Microsoft](configure-authentication-provider-microsoft.md)). Crie [restrições de endereço IP](app-service-ip-restrictions.md) e [faça a gestão de identidades de serviço](overview-managed-identity.md).
* **Modelos de aplicação** – escolha entre uma lista extensa de modelos de aplicação no [Azure Marketplace](https://azure.microsoft.com/marketplace/), como o WordPress, o Joomla e o Drupal.
* **Integração do Visual Studio** – as ferramentas dedicadas do Visual Studio simplificam o trabalho de criar, implementar e depurar.
* **Funcionalidades API e mobile** - O App Service fornece suporte CORS chave-na-mão para cenários restful API, e simplifica cenários de aplicações móveis permitindo a autenticação, sincronização de dados offline, notificações push e muito mais.
* **Código sem servidor** - Execute um fragmento de código ou script a pedido, sem ter de aprovisionar ou gerir a infraestrutura explicitamente, e pague apenas o tempo de computação que o seu código utiliza (veja [Azure Functions (Funções do Azure)](/azure/azure-functions/)).

Além do Serviço de Aplicações, o Azure oferece outros serviços que podem ser utilizados para hospedar websites e aplicações web. Para a maioria dos cenários, o App Service é a melhor escolha.  Para uma arquitetura de microsserviço, considere o [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Se precisar de mais controlo sobre as VMs em que o seu código é executado, considere as [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para obter mais informações sobre como escolher entre estes serviços do Azure, consulte a [Comparação entre o App Service do Azure, as Virtual Machines, o Service Fabric e os Cloud Services](overview-compare.md).

## <a name="next-steps"></a>Passos seguintes

Crie a sua primeira aplicação Web.

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby (no Linux)](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python (no Linux)](containers/quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
