---
title: O que é Azure App configuração?
description: Uma visão geral do serviço de configuração do Azure App.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/24/2019
ms.openlocfilehash: 22ebfc53405149358b0a2a03cd256a78db5d1910
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899327"
---
# <a name="what-is-azure-app-configuration"></a>O que é Azure App configuração?

Azure App configuração fornece um serviço para gerenciar centralmente as configurações do aplicativo e os sinalizadores de recursos. Os programas modernos, especialmente os programas em execução em uma nuvem, geralmente têm muitos componentes que são distribuídos por natureza. A propagação de definições de configuração entre esses componentes pode levar a erros difíceis de solucionar problemas durante a implantação de um aplicativo. Use a configuração de aplicativo para armazenar todas as configurações para seu aplicativo e proteger seus acessos em um único local.

A configuração do aplicativo está atualmente em visualização pública. Ele é livre para ser usado durante o período de versão prévia. Pode inscrever-se no [portal Azure.](https://portal.azure.com)

## <a name="why-use-app-configuration"></a>Por que usar a configuração de aplicativo?

Os aplicativos baseados em nuvem geralmente são executados em várias máquinas virtuais ou contêineres em várias regiões e usam vários serviços externos. A criação de um aplicativo distribuído, robusto e escalonável, é um desafio.

Várias metodologias de programação ajudam os desenvolvedores a lidar com a complexidade crescente da criação de aplicativos. Por exemplo, a [App Twelve-Factor](https://12factor.net/) descreve muitos padrões arquitetónicos bem testados e boas práticas para uso com aplicações em nuvem. Uma recomendação importante deste guia é separar a configuração do código. Nesse caso, as definições de configuração de um aplicativo devem ser mantidas externas ao seu executável e lidas em seu ambiente de tempo de execução ou em uma fonte externa.

Embora qualquer aplicativo possa fazer uso da configuração do aplicativo, os seguintes exemplos são os tipos de aplicativo que se beneficiam do uso dele:

* Microserviços baseados no serviço kubernetes do Azure, Service Fabric do Azure ou outros aplicativos em contêineres implantados em uma ou mais geografias
* Aplicativos sem servidor, que incluem Azure Functions ou outros aplicativos de computação com estado baseado em evento
* Pipeline de implantação contínua

A configuração de aplicativo oferece os seguintes benefícios:

* Um serviço totalmente gerenciado que pode ser configurado em minutos
* Representações e mapeamentos de chave flexíveis
* Marcação com rótulos
* Replay pontual de configurações
* Interface do usuário dedicada para gerenciamento de sinalizador de recursos
* Comparação de dois conjuntos de configurações em dimensões definidas personalizadas
* Segurança aprimorada por meio de identidades gerenciadas pelo Azure
* Total de criptografias de dados em repouso ou em trânsito
* Integração nativa com estruturas populares

A Configuração de Aplicações complementa o [Cofre de Chaves Azure,](https://azure.microsoft.com/services/key-vault/)que é usado para armazenar segredos de aplicação. A configuração do aplicativo torna mais fácil implementar os seguintes cenários:

* Centralize o gerenciamento e a distribuição de dados de configuração hierárquica para diferentes ambientes e geografias
* Alterar dinamicamente as configurações do aplicativo sem a necessidade de reimplantar ou reiniciar um aplicativo
* Controle a disponibilidade de recursos em tempo real

## <a name="use-app-configuration"></a>Usar configuração de aplicativo

A maneira mais fácil de adicionar um repositório de configuração de aplicativo ao seu aplicativo é por meio de uma biblioteca de cliente que a Microsoft fornece. Com base na linguagem e na estrutura de programação, os melhores métodos a seguir estão disponíveis para você.

| Linguagem de programação e estrutura | Como se ligar |
|---|---|
| .NET Core e ASP.NET Core | Provedor de configuração de aplicativo para .NET Core |
| .NET Framework e ASP.NET | Construtor de configuração de aplicativo para .NET |
| Java Spring | Cliente de configuração de aplicativo para Spring Cloud |
| Outros | API REST de configuração de aplicativo |

## <a name="next-steps"></a>Passos seguintes

* [ASP.NET Core quickstart](./quickstart-aspnet-core-app.md)
* [.NET Core quickstart](./quickstart-dotnet-core-app.md)
* [.NET Quadro arranque rápido](./quickstart-dotnet-app.md)
* [Funções Azure arranque rápido](./quickstart-azure-functions-csharp.md)
* [Início rápido da primavera de Java](./quickstart-java-spring-app.md)
* [ASP.NET core característica bandeira quickstart](./quickstart-feature-flag-aspnet-core.md)
* [Porta-malas de primavera característica de bandeira quickstart](./quickstart-feature-flag-spring-boot.md)
