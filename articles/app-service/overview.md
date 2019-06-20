---
title: Descrição geral do serviço de aplicações - Azure | Documentos da Microsoft
description: Saiba como o App Service do Azure o ajuda a desenvolver e alojar Web Apps
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5ce8abd08f82a2a8b69cc996bfd8f7f060bb8c23
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205039"
---
# <a name="app-service-overview"></a>Descrição geral do Serviço de Aplicações

*Serviço de aplicações do Azure* é um serviço baseado em HTTP para alojar aplicações web, REST APIs e back-ends móveis. Pode desenvolver no seu idioma favorito, seja .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python. Aplicativos executam e dimensionam com facilidade em ambientes baseados em Linux e Windows. Para ambientes baseados em Linux, veja [Serviço de Aplicações no Linux](containers/app-service-linux-intro.md). 

Serviço de aplicações não só adiciona o poder do Microsoft Azure à sua aplicação, como segurança, balanceamento de carga, dimensionamento automático e gestão automatizada. Também pode tirar partido das respetivas capacidades do DevOps, como a implementação contínua do Azure DevOps, do GitHub, do Docker Hub e de outras origens, gestão de pacotes, ambientes de teste, domínio personalizado e certificados SSL. 

Com o Serviço de Aplicações, paga pelos recursos de computação do Azure que utilizar. Os recursos de computação que utiliza é determinado pelos _plano do App Service_ que executa as suas aplicações. Para obter mais informações, consulte [descrição geral dos planos do App Service do Azure](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Porquê utilizar o App Service?

Aqui estão algumas das principais funcionalidades do serviço de aplicações:

* **Várias linguagens e arquiteturas** -serviço de aplicações tem suporte de primeira classe para ASP.NET, ASP.NET Core, Java, Ruby, node. js, PHP ou Python. Também pode executar o [PowerShell e outros scripts ou executáveis](webjobs-create.md) como serviços em segundo plano.
* **Otimização de DevOps** – Configure a [integração e implementação contínuas](deploy-continuous-deployment.md) com o Azure DevOps, GitHub, BitBucket, Docker Hub ou Azure Container Registry. Promova atualizações através de [ambientes de teste](deploy-staging-slots.md). Faça a gestão das suas aplicações no App Service utilizando o [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou a [interface de linha de comandos (CLI) de várias plataformas](/cli/azure/install-azure-cli).
* **Dimensionamento global com elevada disponibilidade** – aumente [verticalmente](web-sites-scale.md) ou [horizontalmente](../monitoring-and-diagnostics/insights-how-to-scale.md) de forma manual ou automática. Aloje as aplicações em qualquer lugar da infraestrutura do datacenter global da Microsoft e o App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promete elevada disponibilidade.
* **Ligações a plataformas SaaS e dados no local** – escolha entre mais de 50 [conectores](../connectors/apis-list.md) para sistemas empresariais (como SAP), serviços SaaS (como o Salesforce) e serviços Internet (como o Facebook). Aceda a dados no local ao utilizar [Ligações Híbridas](app-service-hybrid-connections.md) e [Azure Virtual Networks](web-sites-integrate-with-vnet.md).
* **Segurança e conformidade** – o App Service está [em conformidade com ISO, SOC e PCI](https://www.microsoft.com/en-us/trustcenter). Autentique os utilizadores com o [Azure Active Directory](configure-authentication-provider-aad.md) ou com início de sessão social ([Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) e [ Microsoft](configure-authentication-provider-microsoft.md)). Crie [restrições de endereço IP](app-service-ip-restrictions.md) e [faça a gestão de identidades de serviço](overview-managed-identity.md).
* **Modelos de aplicação** – escolha entre uma lista extensa de modelos de aplicação no [Azure Marketplace](https://azure.microsoft.com/marketplace/), como o WordPress, o Joomla e o Drupal.
* **Integração do Visual Studio** – as ferramentas dedicadas do Visual Studio simplificam o trabalho de criar, implementar e depurar.
* **Funcionalidades móveis e API** -serviço de aplicações fornece suporte CORS de chave na mão para cenários de RESTful API e simplifica cenários de aplicações móveis ao ativar a autenticação, sincronização de dados offline, notificações push e muito mais.
* **Código sem servidor** - Execute um fragmento de código ou script a pedido, sem ter de aprovisionar ou gerir a infraestrutura explicitamente, e pague apenas o tempo de computação que o seu código utiliza (veja [Azure Functions (Funções do Azure)](/azure/azure-functions/)).

Além de serviço de aplicações, o Azure oferece outros serviços que podem ser utilizados para alojar sites e aplicações web. Na maioria dos cenários, o serviço de aplicações é a melhor opção.  Para uma arquitetura de microsserviço, considere o [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Se precisar de mais controlo sobre as VMs em que o seu código é executado, considere as [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para obter mais informações sobre como escolher entre estes serviços do Azure, consulte a [Comparação entre o App Service do Azure, as Virtual Machines, o Service Fabric e os Cloud Services](overview-compare.md).

## <a name="next-steps"></a>Passos Seguintes

Crie a sua primeira aplicação Web.

> [!div class="nextstepaction"]
> [Núcleo do ASP.NET](app-service-web-get-started-dotnet.md)

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
