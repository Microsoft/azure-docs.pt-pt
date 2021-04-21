---
title: Descrição geral
description: Saiba como o App Service do Azure o ajuda a desenvolver e alojar Web Apps
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 771c79f56a31c83f2152886ad6cf68367776f83f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767233"
---
# <a name="app-service-overview"></a>Descrição geral do Serviço de Aplicações

*O Azure App Service* é um serviço baseado em HTTP para hospedar aplicações web, APIs REST e extremidades traseiras móveis. Pode desenvolver no seu idioma favorito, seja .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python. As aplicações funcionam e escalam com facilidade tanto em ambientes baseados no Windows como no [Linux.](#app-service-on-linux)

O Serviço de Aplicações não só adiciona o poder do Microsoft Azure à sua aplicação, como segurança, equilíbrio de carga, autoscalagem e gestão automatizada. Também pode tirar partido das suas capacidades DevOps, tais como a implementação contínua de Azure DevOps, GitHub, Docker Hub, e outras fontes, gestão de pacotes, ambientes de encenação, domínio personalizado e certificados TLS/SSL. 

Com o Serviço de Aplicações, paga pelos recursos de computação do Azure que utilizar. Os recursos computacionares que utiliza são determinados pelo _plano de Serviço de Aplicações_ em que executou as suas aplicações. Para mais informações, consulte [a visão geral dos planos do Azure App Service](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Porquê utilizar o App Service?

Aqui estão algumas características-chave do Serviço de Aplicações:

* **Várias línguas e quadros** - O Serviço de Aplicações tem suporte de primeira classe para ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP ou Python. Também pode executar o [PowerShell e outros scripts ou executáveis](webjobs-create.md) como serviços em segundo plano.
* **Ambiente de produção gerido** - O Serviço de Aplicações [remenda e mantém automaticamente o sistema operativo e os quadros linguísticos](overview-patch-os-runtime.md) para si. Passe o tempo escrevendo grandes apps e deixe o Azure preocupar-se com a plataforma.
* **Containerization e Docker** - Dockerize a sua app e hospede um recipiente personalizado Windows ou Linux no Serviço de Aplicações. Executar aplicativos multi-contentores com Docker Compose. Migrar as tuas habilidades Docker diretamente para o Serviço de Aplicações.
* **Otimização de DevOps** – Configure a [integração e implementação contínuas](deploy-continuous-deployment.md) com o Azure DevOps, GitHub, BitBucket, Docker Hub ou Azure Container Registry. Promova atualizações através de [ambientes de teste](deploy-staging-slots.md). Faça a gestão das suas aplicações no App Service utilizando o [Azure PowerShell](/powershell/azure/) ou a [interface de linha de comandos (CLI) de várias plataformas](/cli/azure/install-azure-cli).
* **Dimensionamento global com elevada disponibilidade** – aumente [verticalmente](manage-scale-up.md) ou [horizontalmente](../azure-monitor/autoscale/autoscale-get-started.md) de forma manual ou automática. Aloje as aplicações em qualquer lugar da infraestrutura do datacenter global da Microsoft e o App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promete elevada disponibilidade.
* **Ligações a plataformas SaaS e dados no local** – escolha entre mais de 50 [conectores](../connectors/apis-list.md) para sistemas empresariais (como SAP), serviços SaaS (como o Salesforce) e serviços Internet (como o Facebook). Aceda a dados no local ao utilizar [Ligações Híbridas](app-service-hybrid-connections.md) e [Azure Virtual Networks](web-sites-integrate-with-vnet.md).
* **Segurança e conformidade** – o App Service está [em conformidade com ISO, SOC e PCI](https://www.microsoft.com/en-us/trustcenter). Autenticar utilizadores com [Diretório Ativo Azure](configure-authentication-provider-aad.md), [Google,](configure-authentication-provider-google.md) [Facebook,](configure-authentication-provider-facebook.md) [Twitter](configure-authentication-provider-twitter.md)ou [conta Microsoft.](configure-authentication-provider-microsoft.md) Crie [restrições de endereço IP](app-service-ip-restrictions.md) e [faça a gestão de identidades de serviço](overview-managed-identity.md).
* **Modelos de aplicação** – escolha entre uma lista extensa de modelos de aplicação no [Azure Marketplace](https://azure.microsoft.com/marketplace/), como o WordPress, o Joomla e o Drupal.
* **Integração visual studio e visual studio code** - Ferramentas dedicadas em Visual Studio e Visual Studio Code dinamizam o trabalho de criação, implantação e depuragem.
* **API e funcionalidades móveis** - O Serviço de Aplicações fornece suporte CORS chave-na-turno para cenários DE API RESTful, e simplifica cenários de aplicações móveis, permitindo a autenticação, sincronização de dados offline, notificações push, e muito mais.
* **Código sem servidor** - Execute um fragmento de código ou script a pedido, sem ter de aprovisionar ou gerir a infraestrutura explicitamente, e pague apenas o tempo de computação que o seu código utiliza (veja [Azure Functions (Funções do Azure)](../azure-functions/index.yml)).

Além do Serviço de Aplicações, o Azure oferece outros serviços que podem ser usados para hospedar websites e aplicações web. Para a maioria dos cenários, o Serviço de Aplicações é a melhor escolha.  Para a arquitetura de microserviços, considere [Azure Spring-Cloud Service](../spring-cloud/index.yml) ou [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).  Se precisar de mais controlo sobre os VMs em que o seu código funciona, considere [as Máquinas Virtuais Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para obter mais informações sobre como escolher entre estes serviços do Azure, consulte a [Comparação entre o App Service do Azure, as Virtual Machines, o Service Fabric e os Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="app-service-on-linux"></a>Serviço de Aplicações no Linux

O Serviço de Aplicações também pode hospedar aplicações web de forma nativa no Linux para pilhas de aplicações suportadas. Também pode executar recipientes Linux personalizados (também conhecidos como Web App para Contentores).

### <a name="built-in-languages-and-frameworks"></a>Línguas e quadros incorporados

O Serviço de Aplicações no Linux suporta uma série de imagens incorporadas específicas do idioma. Basta implementar o seu código. As línguas suportadas incluem: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core e Ruby. Corra [`az webapp list-runtimes --linux`](/cli/azure/webapp#az_webapp_list_runtimes) para ver os mais recentes idiomas e versões suportadas. Se o tempo de execução que a sua aplicação necessita não for suportado nas imagens incorporadas, pode implantá-la com um recipiente personalizado.

Os tempos de execução desatualizados são periodicamente removidos das lâminas de criação e configuração de aplicações web no Portal. Estes tempos de execução são escondidos do Portal quando são depreciados pela organização de manutenção ou se constatam que têm vulnerabilidades significativas. Estas opções estão escondidas para guiar os clientes para os tempos mais recentes onde serão os mais bem sucedidos. 

Quando um tempo de execução desatualizado é escondido do Portal, qualquer um dos seus sites existentes usando essa versão continuará a ser executado. Se um tempo de execução for totalmente removido da plataforma do Serviço de Aplicações, o seu(s) proprietário de subscrição Azure receberá um aviso de e-mail antes da remoção.

Se precisar de criar outra aplicação web com uma versão desatualizada que já não seja mostrada no Portal consulte os guias de configuração de idiomas para obter instruções sobre como obter a versão de tempo de execução do seu site. Pode utilizar o CLI Azure para criar outro site com o mesmo tempo de funcionamento. Em alternativa, pode utilizar o botão **Modelo de Exportação** na lâmina da aplicação web no Portal para exportar um modelo ARM do site. Pode reutilizar este modelo para implementar um novo site com o mesmo tempo de funcionação e configuração.

### <a name="limitations"></a>Limitações

- O Serviço de Aplicações no Linux não é suportado no nível de preços [partilhados.](https://azure.microsoft.com/pricing/details/app-service/plans/) 
- Não é possível misturar aplicações Windows e Linux no mesmo plano de Serviço de Aplicações.  
- Historicamente, não é possível misturar apps Windows e Linux no mesmo grupo de recursos. No entanto, todos os grupos de recursos criados em ou depois de 21 de janeiro de 2021 apoiam este cenário. Para os grupos de recursos criados antes de 21 de janeiro de 2021, a capacidade de adicionar implantações de plataformas mistas será lançada em breve em regiões do Azure (incluindo regiões de nuvem Nacional).
- O portal Azure mostra apenas funcionalidades que atualmente funcionam para aplicações Linux. À medida que as funcionalidades estão ativadas, são ativadas no portal.
- Quando implementado para imagens incorporadas, o seu código e conteúdo são atribuídos um volume de armazenamento para conteúdo web, apoiado pelo Azure Storage. A latência do disco deste volume é maior e mais variável do que a latência do sistema de ficheiros do contentor. As aplicações que requerem acesso pesado apenas a ficheiros de conteúdo podem beneficiar da opção de contentores personalizados, que coloca ficheiros no sistema de ficheiros do contentor em vez de no volume de conteúdo.

## <a name="next-steps"></a>Passos seguintes

Crie a sua primeira aplicação Web.

> [!div class="nextstepaction"]
> [ASP.NET Core (no Windows ou Linux)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET (no Windows)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP (no Windows ou Linux)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby (no Linux)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js (no Windows ou Linux)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java (no Windows ou Linux)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python (no Linux)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML (no Windows ou Linux)](quickstart-html.md)

> [!div class="nextstepaction"]
> [Recipiente personalizado (Windows ou Linux)](tutorial-custom-container.md)
