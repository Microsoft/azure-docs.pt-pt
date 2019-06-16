---
title: Configuração de aplicações do Azure FAQ | Documentos da Microsoft
description: Perguntas mais frequentes sobre a configuração de aplicações do Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: e321c0b473b110597b5b87a6e67666737116daa2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393353"
---
# <a name="azure-app-configuration-faq"></a>Configuração de aplicações do Azure FAQ

Este artigo aborda perguntas mais frequentes sobre a configuração de aplicações do Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>De que forma é que o App Configuration é diferente do Azure Key Vault?

Configuração de aplicações foi concebida para um conjunto diferente de casos de utilização: ajuda os desenvolvedores a gerir as definições da aplicação e controlar a disponibilidade de funcionalidades. Pretende simplificar muitas das tarefas do trabalho com dados de configuração complexa.

Suporta a configuração de aplicações:

- Espaços de nomes hierárquicos
- Etiquetagem
- Consultas extensas
- Obtenção de batch
- Operações de gestão especializadas
- Uma interface de utilizador de gestão de recursos

Configuração de aplicações é complementar ao Cofre de chaves e os dois devem ser utilizados lado a lado na maioria das implementações de aplicações.

## <a name="should-i-store-secrets-in-app-configuration"></a>Posso armazenar segredos na configuração de aplicações?

Embora a configuração de aplicações fornece segurança robusta, Key Vault ainda é o melhor local para armazenar segredos da aplicação. Key Vault fornece encriptação ao nível do hardware, as políticas de acesso granular e operações de gestão, como a rotação de certificados.

## <a name="does-app-configuration-encrypt-my-data"></a>Configuração de aplicação encripta os meus dados?

Sim. Configuração de aplicação encripta todos os valores da chave contém e, esta encripta a comunicação de rede. Nomes de chaves são utilizados como índices para recuperar dados de configuração e não estão encriptados.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Como posso armazenar a configurações para vários ambientes (teste, transição, produção e assim por diante)?

Atualmente pode controlar quem tem acesso à configuração de aplicações num nível por loja. Utilize um armazenamento separado para cada ambiente de que necessita de permissões diferentes. Esta abordagem dá-lhe o melhor isolamento de segurança.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quais são as formas recomendadas para utilizar a configuração de aplicações?

Ver [melhores práticas](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Quanto custa a configuração de aplicações?

O serviço é gratuito para utilizar durante a pré-visualização pública.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Como posso comunicar um problema ou dar uma sugestão?

Pode contactar-nos diretamente na [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Passos Seguintes

* [Sobre a configuração de aplicações do Azure](./overview.md)
