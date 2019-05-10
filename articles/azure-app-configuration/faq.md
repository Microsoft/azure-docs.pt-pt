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
ms.openlocfilehash: 3181609bf34a04de4e31b73429f9bc5fa3fe3408
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411900"
---
# <a name="azure-app-configuration-faq"></a>Configuração de aplicações do Azure FAQ

Este artigo aborda o frequentes perguntas sobre a configuração de aplicações do Azure.

## <a name="how-is-app-configuration-different-from-key-vault"></a>Como configuração de aplicações é diferente do Key Vault?

Configuração de aplicações foi concebida para um conjunto diferente de casos de utilização: ajuda os desenvolvedores a gerir as definições da aplicação e controlar a disponibilidade de funcionalidades. Pretende simplificar muitas das tarefas do trabalho com dados de configuração complexa. Ele oferece suporte espaço de nomes hierárquico, etiquetagem, consultas extensas, obtenção de lote e as operações de gestão especializadas e interfaces do usuário. Configuração de aplicações é complementar ao Cofre de chaves e os dois devem ser utilizados lado a lado na maioria das implementações de aplicações.

## <a name="should-i-store-secrets-in-app-configuration"></a>Posso armazenar segredos na configuração de aplicações?

Enquanto a configuração de aplicações fornece segurança robusta, Key Vault ainda é o melhor local para armazenar segredos da aplicação. Fornece encriptação ao nível do hardware, as políticas de acesso granular e operações de gestão, tais como a rotação de certificados.

## <a name="does-app-configuration-encrypt-my-data"></a>Configuração de aplicação encripta os meus dados?

Sim. Configuração de aplicação encripta todos os valores de chave que mantém e comunicação de rede. Nomes de chaves são utilizados como índices para recuperar dados de configuração e não estão encriptados.

## <a name="does-app-configuration-support-azure-virtual-network-vnet"></a>Configuração de aplicação suporta a rede Virtual do Azure (VNET)?

Ainda não. Suporte VNET está previsto para disponibilidade geral.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Como posso armazenar a configurações para vários ambientes (teste, transição, produção e assim por diante)?

Atualmente pode controlar quem tem acesso à configuração de aplicações num nível por loja. Deve usar um armazenamento separado para cada ambiente de que necessita de permissões diferentes. Esta abordagem dá-lhe o melhor isolamento de segurança.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quais são as formas recomendadas para utilizar a configuração de aplicações?

Ver [melhores práticas](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Quanto custa a configuração de aplicações?

O serviço é gratuito para utilizar durante a pré-visualização pública.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Como posso comunicar um problema ou dar uma sugestão?

Pode contactar-nos diretamente na [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Passos Seguintes

* [Sobre a configuração de aplicações do Azure](./overview.md)
