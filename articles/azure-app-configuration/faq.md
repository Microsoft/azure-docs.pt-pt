---
title: Perguntas frequentes sobre configuração do Azure App | Microsoft Docs
description: Perguntas frequentes sobre a configuração de Azure App
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 8d286cbab33a1fb6a2d2a2cb70caed11b21af735
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904099"
---
# <a name="azure-app-configuration-faq"></a>Perguntas frequentes sobre configuração do Azure App

Este artigo aborda as perguntas frequentes sobre a configuração do Azure App.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>De que forma é que o App Configuration é diferente do Azure Key Vault?

A configuração do aplicativo foi projetada para um conjunto distinto de casos de uso: ela ajuda os desenvolvedores a gerenciar configurações de aplicativos e controlar a disponibilidade de recursos. Ele visa simplificar muitas das tarefas de trabalhar com dados de configuração complexos.

A configuração do aplicativo dá suporte a:

- Namespaces hierárquicos
- rotulagem
- Consultas extensivas
- Recuperação em lote
- Operações de gerenciamento especializadas
- Uma interface do usuário de gerenciamento de recursos

A configuração do aplicativo é complementar à Key Vault e as duas devem ser usadas lado a lado na maioria das implantações de aplicativo.

## <a name="should-i-store-secrets-in-app-configuration"></a>Devo armazenar segredos na configuração do aplicativo?

Embora a configuração de aplicativo forneça segurança protegida, Key Vault ainda é o melhor lugar para armazenar os segredos do aplicativo. O Key Vault fornece criptografia no nível de hardware, políticas de acesso granulares e operações de gerenciamento, como a rotação de certificado.

Você pode criar valores de configuração de aplicativo que fazem referência a segredos armazenados em Key Vault. Para obter mais informações, consulte [usar referências de Key Vault em um aplicativo ASP.NET Core](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>A configuração do aplicativo criptografa meus dados?

Sim. A configuração de aplicativo criptografa todos os valores de chave que ele contém e criptografa a comunicação de rede. Os nomes de chave são usados como índices para recuperar dados de configuração e não são criptografados.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Como a configuração do aplicativo é diferente das configurações do Azure App Service?

Azure App serviço permite que você defina as configurações do aplicativo para cada instância do serviço de aplicativo. Essas configurações são passadas como variáveis de ambiente para o código do aplicativo. Você pode associar uma configuração a um slot de implantação específico, se desejar. Para obter mais informações, consulte [definir configurações de aplicativo](/azure/app-service/configure-common#configure-app-settings).

Por outro lado, Azure App configuração permite que você defina as configurações que podem ser compartilhadas entre vários aplicativos, incluindo aplicativos em execução no serviço de aplicativo. Essas configurações podem ser acessadas no código do aplicativo por meio dos provedores de configuração para .NET e Java, por meio do SDK do Azure ou diretamente por meio de APIs REST.

Você também pode importar e exportar configurações entre o serviço de aplicativo e a configuração de aplicativo. Isso permite que você configure rapidamente um novo repositório de configurações de aplicativo com base nas configurações existentes do serviço de aplicativo ou compartilhe facilmente a configuração com um aplicativo existente que se baseia nas configurações do serviço de aplicativo.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Há alguma limitação de tamanho em chaves e valores armazenados na configuração do aplicativo?

Há um limite de 10 KB para um único item de chave-valor.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Como devo armazenar configurações para vários ambientes (teste, preparo, produção e assim por diante)?

No momento, você pode controlar quem tem acesso à configuração do aplicativo em um nível por loja. Use um armazenamento separado para cada ambiente que exija permissões diferentes. Essa abordagem oferece o melhor isolamento de segurança.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quais são as maneiras recomendadas de usar a configuração de aplicativo?

Consulte [práticas recomendadas](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Quanto custa a configuração do aplicativo?

O serviço é gratuito para uso durante a visualização pública.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Como posso receber comunicados sobre novas versões e outras informações relacionadas à configuração do aplicativo?

Assine nosso [repositório de comunicados do GitHub](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Como posso relatar um problema ou dar uma sugestão?

Você pode contatá-los diretamente no [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Passos seguintes

* [Sobre a configuração de Azure App](./overview.md)
