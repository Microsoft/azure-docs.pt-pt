---
title: Visão geral
description: Saiba como o App Service do Azure o ajuda a desenvolver e alojar Web Apps
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 01/04/2017
ms.custom: seodec18
ms.openlocfilehash: 849886eb03f46949301d76113109aae8188ff8fc
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687038"
---
# <a name="app-service-overview"></a>Descrição geral do Serviço de Aplicações

*Azure app serviço* é um serviço baseado em http para hospedar aplicativos Web, APIs REST e back-ends móveis. Pode desenvolver no seu idioma favorito, seja .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python. Os aplicativos são executados e dimensionados com facilidade em ambientes baseados no Windows e no Linux. Para ambientes baseados em Linux, veja [Serviço de Aplicações no Linux](containers/app-service-linux-intro.md). 

O serviço de aplicativo não apenas adiciona a potência do Microsoft Azure ao seu aplicativo, como segurança, balanceamento de carga, dimensionamento automático e gerenciamento automatizado. Também pode tirar partido das respetivas capacidades do DevOps, como a implementação contínua do Azure DevOps, do GitHub, do Docker Hub e de outras origens, gestão de pacotes, ambientes de teste, domínio personalizado e certificados SSL. 

Com o Serviço de Aplicações, paga pelos recursos de computação do Azure que utilizar. Os recursos de computação que você usa são determinados pelo _plano do serviço de aplicativo_ no qual você executa seus aplicativos. Para obter mais informações, consulte [visão geral dos planos de serviço Azure app](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Porquê utilizar o App Service?

Aqui estão alguns dos principais recursos do serviço de aplicativo:

* **Vários idiomas e estruturas** – o serviço de aplicativo tem suporte de primeira classe para ASP.NET, ASP.NET Core, Java, Ruby, Node. js, php ou Python. Também pode executar o [PowerShell e outros scripts ou executáveis](webjobs-create.md) como serviços em segundo plano.
* **Otimização de DevOps** – Configure a [integração e implementação contínuas](deploy-continuous-deployment.md) com o Azure DevOps, GitHub, BitBucket, Docker Hub ou Azure Container Registry. Promova atualizações através de [ambientes de teste](deploy-staging-slots.md). Faça a gestão das suas aplicações no App Service utilizando o [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou a [interface de linha de comandos (CLI) de várias plataformas](/cli/azure/install-azure-cli).
* **Dimensionamento global com elevada disponibilidade** – aumente [verticalmente](manage-scale-up.md) ou [horizontalmente](../monitoring-and-diagnostics/insights-how-to-scale.md) de forma manual ou automática. Aloje as aplicações em qualquer lugar da infraestrutura do datacenter global da Microsoft e o App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promete elevada disponibilidade.
* **Ligações a plataformas SaaS e dados no local** – escolha entre mais de 50 [conectores](../connectors/apis-list.md) para sistemas empresariais (como SAP), serviços SaaS (como o Salesforce) e serviços Internet (como o Facebook). Aceda a dados no local ao utilizar [Ligações Híbridas](app-service-hybrid-connections.md) e [Azure Virtual Networks](web-sites-integrate-with-vnet.md).
* **Segurança e conformidade** – o App Service está [em conformidade com ISO, SOC e PCI](https://www.microsoft.com/en-us/trustcenter). Autentique os utilizadores com o [Azure Active Directory](configure-authentication-provider-aad.md) ou com início de sessão social ([Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) e [ Microsoft](configure-authentication-provider-microsoft.md)). Crie [restrições de endereço IP](app-service-ip-restrictions.md) e [faça a gestão de identidades de serviço](overview-managed-identity.md).
* **Modelos de aplicação** – escolha entre uma lista extensa de modelos de aplicação no [Azure Marketplace](https://azure.microsoft.com/marketplace/), como o WordPress, o Joomla e o Drupal.
* **Integração do Visual Studio** – as ferramentas dedicadas do Visual Studio simplificam o trabalho de criar, implementar e depurar.
* **API e recursos móveis** – o serviço de aplicativo fornece suporte de CORS de chave para cenários de API RESTful e simplifica cenários de aplicativos móveis habilitando a autenticação, sincronização de dados offline, notificações por push e muito mais.
* **Código sem servidor** - Execute um fragmento de código ou script a pedido, sem ter de aprovisionar ou gerir a infraestrutura explicitamente, e pague apenas o tempo de computação que o seu código utiliza (veja [Azure Functions (Funções do Azure)](/azure/azure-functions/)).

Além do serviço de aplicativo, o Azure oferece outros serviços que podem ser usados para hospedar sites e aplicativos Web. Para a maioria dos cenários, o serviço de aplicativo é a melhor opção.  Para uma arquitetura de microsserviço, considere o [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Se precisar de mais controlo sobre as VMs em que o seu código é executado, considere as [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para obter mais informações sobre como escolher entre estes serviços do Azure, consulte a [Comparação entre o App Service do Azure, as Virtual Machines, o Service Fabric e os Cloud Services](overview-compare.md).

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
