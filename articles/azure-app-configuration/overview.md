---
title: O que é a configuração de aplicações do Azure | Documentos da Microsoft
description: Uma visão geral do serviço de configuração de aplicações do Azure.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 11dd91039bb352e86800982d0a294f82622a56fe
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884832"
---
# <a name="what-is-azure-app-configuration"></a>O que é a configuração de aplicações do Azure

Configuração de aplicações do Azure fornece um serviço para gerir as definições da aplicação centralmente. Programas modernos, especialmente os que são executados numa nuvem, geralmente têm vários componentes que são distribuídos por natureza. Propagação de definições de configuração nesses componentes pode levar a erros de resolução de problemas é difícil durante a implementação de uma aplicação. Configuração de aplicações permite-lhe armazenar todas as definições para a sua aplicação e proteger seus acessos num único local.

## <a name="why-use-app-configuration"></a>Por que usar a configuração de aplicações

Aplicativos baseados na nuvem, muitas vezes, executar em várias máquinas virtuais ou contentores em várias regiões e utilizam múltiplos serviços externos. Criar uma aplicação distribuída que é robusta e escalonável é um verdadeiro desafio. Várias metodologias de programação tem risen para ajudar os desenvolvedores a lidar com cada vez maior complexidade de criar esses aplicativos. Por exemplo, fornece detalhes sobre a aplicação de 12 fatores muitos padrões de arquiteturais bem testados e práticas recomendadas para utilização com aplicações na cloud. Uma recomendação de chave deste guia é separar a configuração a partir do código. Isso significa que a configuração de uma aplicação, tais como definições deve ser mantida fora do seu executável e ler a partir do seu ambiente de tempo de execução ou de uma origem externa.

Embora qualquer aplicativo pode fazer usá-lo, o seguinte é bons exemplos dos tipos de aplicativo que deve utilizar a configuração de aplicações:

* Microsserviços com base no AKS, Service Fabric ou outras aplicações em contentores implementadas em localizações geográficas de um ou mais.
* Aplicações sem servidor, incluindo as funções do Azure ou a outras aplicações orientadas por eventos computação sem monitoração de estado.
* Pipeline de implementação contínua.

Configuração de aplicações oferece as seguintes vantagens:

* Um serviço totalmente gerido que pode configurar em minutos.
* Representações de chave flexíveis e mapeamentos.
* Marcação com etiquetas.
* Repetição de ponto no tempo de definições.
* Comparação de dois conjuntos de configurações em dimensões personalizado.
* Segurança aprimorada por meio de identidades geridas do Azure.
* Concluir encriptações de dados, Inativos ou em trânsito.
* Integração nativa com arquiteturas populares.

## <a name="how-to-use-app-configuration"></a>Como utilizar a configuração de aplicações

A maneira mais fácil de adicionar um arquivo de configuração de aplicação à sua aplicação é através de uma biblioteca de cliente que a Microsoft fornece. Consoante a estrutura e linguagem de programação, o seguinte mostra os melhores métodos disponíveis para si:

| Linguagem de programação e framework | Como ligar |
|---|---|
| **.NET core** e **ASP.NET Core** | Fornecedor de configuração de configuração de aplicações de .NET Core. |
| **.NET** e **ASP.NET** | Construtor de configuração de configuração de aplicação para o .NET. |
| **Java Spring** | Cliente de configuração de configuração de aplicação de Spring Cloud. |
| Outros | Configuração de aplicações REST API. |

## <a name="next-steps"></a>Passos Seguintes

* [Quickstart: Criar uma aplicação web ASP.NET](quickstart-aspnet-core-app.md)  
