---
title: O que é a configuração de aplicações do Azure? | Microsoft Docs
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
ms.openlocfilehash: 8c2c31b4d87ac2db123c0cae63679e3773734b4f
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226288"
---
# <a name="what-is-azure-app-configuration"></a>O que é a configuração de aplicações do Azure?

Configuração de aplicações do Azure fornece um serviço para gerir centralmente as definições da aplicação. Programas modernos, especialmente os programas em execução numa nuvem, geralmente têm vários componentes que são distribuídos por natureza. Propagação de definições de configuração nesses componentes pode levar a erros de resolução de problemas é difícil durante a implementação de uma aplicação. Utilize configuração de aplicações para armazenar todas as definições para a sua aplicação e proteger seus acessos num único local.

Configuração de aplicações é gratuita durante o período de pré-visualização. Se quiser experimentar, [registar](https://aka.ms/azconfig/register) na pré-visualização.

## <a name="why-use-app-configuration"></a>Por que usar a configuração de aplicações?

Aplicativos baseados na nuvem, muitas vezes, executar em várias máquinas virtuais ou contentores em várias regiões e utilizam múltiplos serviços externos. Criar uma aplicação distribuída que é robusta e escalonável é um desafio. 

Várias metodologias de programação ajudam os desenvolvedores a lidar com a crescente complexidade da criação de aplicativos. Por exemplo, a aplicação de 12 fatores descreve vários padrões de arquitetura bem testados e melhores práticas para utilização com aplicações na cloud. Uma recomendação de chave deste guia é separar a configuração a partir do código. Neste caso, as definições de configuração de um aplicativo devem ser mantidas fora do seu executável e ler a partir do seu ambiente de tempo de execução ou de uma origem externa.

Embora qualquer aplicativo pode fazer uso de configuração de aplicações, os exemplos seguintes são os tipos de aplicações que tiram partido da utilização do mesmo:

* Microsserviços com base no serviço Kubernetes do Azure, Azure Service Fabric ou outras aplicações em contentores implementadas em localizações geográficas de um ou mais
* Aplicações sem servidor, que inclui as funções do Azure ou outras aplicações orientadas por eventos computação sem monitoração de estado
* Pipeline de implementação contínua

Configuração de aplicações oferece as seguintes vantagens:

* Um serviço totalmente gerido que pode configurar em minutos
* Representações de chave flexíveis e mapeamentos
* Marcação com etiquetas
* Repetição de ponto no tempo de definições
* Comparação de dois conjuntos de configurações em dimensões personalizado
* Maior segurança por meio de identidades geridas do Azure
* Dados completos encriptações, Inativos ou em trânsito
* Integração nativa com estruturas populares

Configuração de aplicações complementa [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/), que é utilizado para armazenar segredos da aplicação. Configuração de aplicações torna mais fácil de implementar os seguintes cenários:

* Gestão centralizada e a distribuição dos dados de configuração hierárquica para ambientes diferentes e localizações geográficas
* Alterações de configuração dinâmica sem a necessidade de voltar a implementar ou reiniciar uma aplicação
* Gestão de recursos

## <a name="use-app-configuration"></a>Utilizar configuração de aplicações

A maneira mais fácil de adicionar um arquivo de configuração de aplicação à sua aplicação é através de uma biblioteca de cliente que a Microsoft fornece. Com base na linguagem de programação e framework, os seguintes métodos de melhor disponibilizadas para.

| Linguagem de programação e framework | Como ligar |
|---|---|
| .NET core e o ASP.NET Core | Fornecedor de configuração de aplicações de .NET Core |
| .NET e ASP.NET | Construtor de configuração de aplicação para .NET |
| Java Spring | Cliente de configuração de aplicação de Spring Cloud |
| Outros | Configuração de aplicações REST API |

## <a name="next-steps"></a>Passos Seguintes

* [Quickstart: Criar uma aplicação web ASP.NET](quickstart-aspnet-core-app.md) 
