---
title: O que é o Azure App Configuration?
description: Uma visão geral do serviço de configuração de aplicações azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: 1f1cec68813d33e7fa19a414a30adfc9a41df91f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77523480"
---
# <a name="what-is-azure-app-configuration"></a>O que é o Azure App Configuration?

A Configuração de Aplicações Azure fornece um serviço para gerir centralmente as definições de aplicações e as bandeiras de características. Os programas modernos, especialmente os programas que correm numa nuvem, geralmente têm muitos componentes que são distribuídos na natureza. A difusão de definições de configuração através destes componentes pode levar a erros de resolução de problemas durante uma implementação da aplicação. Utilize a Configuração da Aplicação para armazenar todas as definições para a sua aplicação e proteger os seus acessos num só local.

## <a name="why-use-app-configuration"></a>Porquê usar a configuração da aplicação?

Aplicações baseadas em nuvem muitas vezes funcionam em várias máquinas virtuais ou contentores em várias regiões e usam vários serviços externos. A criação de uma aplicação robusta e escalável num ambiente distribuído constitui um desafio significativo.

Várias metodologias de programação ajudam os desenvolvedores a lidar com a crescente complexidade das aplicações de construção. Por exemplo, a [App Twelve-Factor](https://12factor.net/) descreve muitos padrões arquitetónicos bem testados e boas práticas para uso com aplicações em nuvem. Uma recomendação chave deste guia é separar a configuração do código. As definições de configuração de uma aplicação devem ser mantidas externas ao seu executável e lidas a partir do seu ambiente de tempo de execução ou de uma fonte externa.

Embora qualquer aplicação possa utilizar a Configuração da App, os seguintes exemplos são os tipos de aplicação que beneficiam da sua utilização:

* Microserviços baseados no Serviço Azure Kubernetes, Tecido de Serviço Azure ou outras aplicações contentorizadas implantadas em uma ou mais geografias
* Aplicativos sem servidor, que incluem Funções Azure ou outras aplicações de computação apátrida orientadas por eventos
* Gasoduto de implantação contínua

A Configuração de Aplicações oferece os seguintes benefícios:

* Um serviço totalmente gerido que pode ser configurado em minutos
* Representações e mapeamentos flexíveis
* Marcação com etiquetas
* Repetição pontual das definições
* UI dedicado para gestão de bandeiras de recurso
* Comparação de dois conjuntos de configurações em dimensões definidas sob medida
* Segurança reforçada através de identidades geridas pelo Azure
* Encriptação de informações sensíveis em repouso e em trânsito
* Integração nativa com quadros populares

A Configuração de Aplicações complementa o [Cofre de Chaves Azure,](https://azure.microsoft.com/services/key-vault/)que é usado para armazenar segredos de aplicação. A Configuração da Aplicação facilita a implementação dos seguintes cenários:

* Centralizar a gestão e distribuição de dados de configuração hierárquica para diferentes ambientes e geografias
* Alterar dinamicamente as definições de aplicação sem a necessidade de reimplantar ou reiniciar uma aplicação
* Disponibilidade de funcionalidadede controlo em tempo real

## <a name="use-app-configuration"></a>Utilizar configuração de aplicativos

A forma mais fácil de adicionar uma loja de Configuração de Aplicações à sua aplicação é através de uma biblioteca de clientes fornecida pela Microsoft. Os seguintes métodos estão disponíveis para se conectar com a sua aplicação, dependendo da sua linguagem e enquadramento escolhidos

| Linguagem e enquadramento de programação | Como ligar |
|---|---|
| .NET Core e ASP.NET Core | Fornecedor de configuração de aplicativos para .NET Core |
| .QUADRO E ASP.NET | Construtor de configuração de aplicativos para .NET |
| Java Spring | Cliente de configuração de aplicativos para nuvem de primavera |
| Outros | Configuração de aplicativo REST API |

## <a name="next-steps"></a>Passos seguintes

* [ASP.NET Core quickstart](./quickstart-aspnet-core-app.md)
* [.NET Core quickstart](./quickstart-dotnet-core-app.md)
* [.NET Quadro arranque rápido](./quickstart-dotnet-app.md)
* [Funções Azure arranque rápido](./quickstart-azure-functions-csharp.md)
* [Início rápido da primavera de Java](./quickstart-java-spring-app.md)
* [ASP.NET core característica bandeira quickstart](./quickstart-feature-flag-aspnet-core.md)
* [Porta-malas de primavera característica de bandeira quickstart](./quickstart-feature-flag-spring-boot.md)
