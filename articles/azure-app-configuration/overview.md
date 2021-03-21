---
title: O que é o Azure App Configuration?
description: Leia uma visão geral do serviço de Configuração de Aplicações Azure. Compreenda por que gostaria de usar a Configuração de Aplicações e saiba como pode usá-la.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: 9786ff20c1d3f467e217d5fe34518fcc9494863e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930114"
---
# <a name="what-is-azure-app-configuration"></a>O que é o Azure App Configuration?

A Azure App Configuration fornece um serviço para gerir centralmente as definições de aplicações e as bandeiras de funcionalidades. Os programas modernos, especialmente os programas que estão a decorrer numa nuvem, geralmente têm muitos componentes que são distribuídos na natureza. A difusão das definições de configuração nestes componentes pode levar a erros de resolução de problemas difíceis durante a implementação da aplicação. Utilize a Configuração da Aplicação para armazenar todas as definições da sua aplicação e garantir os seus acessos num só local.

## <a name="why-use-app-configuration"></a>Porquê utilizar a Configuração da Aplicação?

Aplicações baseadas em nuvem são frequentemente executadas em várias máquinas virtuais ou contentores em várias regiões e usam vários serviços externos. A criação de uma aplicação robusta e escalável num ambiente distribuído representa um desafio significativo.

Várias metodologias de programação ajudam os desenvolvedores a lidar com a crescente complexidade das aplicações de construção. Por exemplo, a [App Doze Fator](https://12factor.net/) descreve muitos padrões arquitetónicos bem testados e as melhores práticas para uso com aplicações em nuvem. Uma recomendação chave deste guia é separar a configuração do código. As definições de configuração de uma aplicação devem ser mantidas externas ao seu ambiente de execução e lidas a partir do seu ambiente de tempo de funcionamento ou de uma fonte externa.

Embora qualquer aplicação possa utilizar a Configuração da Aplicação, os seguintes exemplos são os tipos de aplicação que beneficiam da sua utilização:

* Microserviços baseados no Serviço Azure Kubernetes, Tecido de Serviço Azure ou outras aplicações contentorizadas implantadas em uma ou mais geografias
* Aplicativos sem servidor, que incluem Funções Azure ou outras aplicações de computação apátridas orientadas por eventos
* Gasoduto de implantação contínua

A Configuração da Aplicação oferece os seguintes benefícios:

* Um serviço totalmente gerido que pode ser configurado em minutos
* Representações e mapeamentos de chaves flexíveis
* Marcação com etiquetas
* Repetição pontual das definições
* UI dedicado para gestão de bandeiras de recurso
* Comparação de dois conjuntos de configurações em dimensões personalizadas
* Segurança reforçada através de identidades geridas pelo Azure
* Encriptação de informação sensível em repouso e em trânsito
* Integração nativa com quadros populares

A Configuração de Aplicações complementa [o Azure Key Vault](https://azure.microsoft.com/services/key-vault/), que é usado para armazenar segredos de aplicações. A configuração da aplicação facilita a implementação dos seguintes cenários:

* Centralizar a gestão e distribuição de dados de configuração hierárquica para diferentes ambientes e geografias
* Alterar dinamicamente as definições de aplicação sem a necessidade de recolocar ou reiniciar uma aplicação
* Disponibilidade de recursos de controlo em tempo real

## <a name="use-app-configuration"></a>Use configuração de aplicativo

A forma mais fácil de adicionar uma loja de Configuração de Aplicações à sua aplicação é através de uma biblioteca de clientes fornecida pela Microsoft. Os seguintes métodos estão disponíveis para se conectar com a sua aplicação, dependendo da sua língua e enquadramento escolhidos

| Linguagem e enquadramento de programação | Como ligar |
|---|---|
| .NET Core e ASP.NET Core | Fornecedor de configuração de aplicativos para .NET Core |
| .NET Quadro e ASP.NET | Construtor de configuração de aplicativos para .NET |
| Java Spring | Cliente de configuração de aplicativo para Cloud de primavera |
| Outros | Configuração de aplicativos REST API |

## <a name="next-steps"></a>Passos seguintes

* [ASP.NET Início rápido do Núcleo](./quickstart-aspnet-core-app.md)
* [.NET Core quickstart](./quickstart-dotnet-core-app.md)
* [.NET Framework quickstart](./quickstart-dotnet-app.md)
* [Azure Funções quickstart](./quickstart-azure-functions-csharp.md)
* [Início rápido da primavera de Java](./quickstart-java-spring-app.md)
* [ASP.NET Core apresentam quickstart de bandeira](./quickstart-feature-flag-aspnet-core.md)
* [Início rápido da bandeira de mola](./quickstart-feature-flag-spring-boot.md)
