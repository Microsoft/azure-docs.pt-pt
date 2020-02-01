---
title: Práticas recomendadas de configuração do Azure App | Microsoft Docs
description: Saiba como usar melhor a configuração de Azure App
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
ms.openlocfilehash: 37f93099027f810e8089119536e089e07080d0bc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898630"
---
# <a name="azure-app-configuration-best-practices"></a>Práticas recomendadas de configuração do Azure App

Este artigo aborda padrões comuns e práticas recomendadas quando você estiver usando Azure App configuração.

## <a name="key-groupings"></a>Agrupamentos de chaves

A configuração do aplicativo fornece duas opções para organizar as chaves:

* Prefixos de chave
* Etiquetas

Você pode usar uma ou ambas as opções para agrupar suas chaves.

Os *prefixos de chave* são as partes iniciais das chaves. Você pode agrupar logicamente um conjunto de chaves usando o mesmo prefixo em seus nomes. Os prefixos podem conter vários componentes conectados por um delimitador, como `/`, semelhante a um caminho de URL, para formar um namespace. Essas hierarquias são úteis quando você está armazenando chaves para muitos aplicativos, serviços de componentes e ambientes em um repositório de configurações de aplicativo.

Uma coisa importante a ter em mente é que as chaves são as referências do código do aplicativo para recuperar os valores das configurações correspondentes. As chaves não devem ser alteradas ou, caso contrário, você precisará modificar seu código sempre que isso acontecer.

Os *Rótulos* são um atributo nas chaves. Eles são usados para criar variantes de uma chave. Por exemplo, você pode atribuir rótulos a várias versões de uma chave. Uma versão pode ser uma iteração, um ambiente ou algumas outras informações contextuais. Seu aplicativo pode solicitar um conjunto totalmente diferente de valores de chave especificando outro rótulo. Como resultado, todas as referências de chave permanecem inalteradas no seu código.

## <a name="key-value-compositions"></a>Composições de valor-chave

A configuração de aplicativo trata todas as chaves armazenadas com ela como entidades independentes. A configuração do aplicativo não tenta inferir nenhuma relação entre as chaves ou para herdar valores de chave com base em sua hierarquia. Você pode agregar vários conjuntos de chaves, no entanto, usando rótulos acoplados ao empilhamento de configuração adequado no código do aplicativo.

Vamos examinar um exemplo. Suponha que você tenha uma configuração chamada **Asset1**, cujo valor pode variar com base no ambiente de desenvolvimento. Você cria uma chave chamada "Asset1" com um rótulo vazio e um rótulo chamado "desenvolvimento". No primeiro rótulo, você coloca o valor padrão para **Asset1**e coloca um valor específico para "desenvolvimento" no último.

No seu código, você primeiro recupera os valores de chave sem rótulos e, em seguida, recupera o mesmo conjunto de valores de chave pela segunda vez com o rótulo "desenvolvimento". Quando você recupera os valores da segunda vez, os valores anteriores das chaves são substituídos. O sistema de configuração do .NET Core permite que você "empilhe" vários conjuntos de dados de configuração em cima um do outro. Se existir uma chave em mais de um conjunto, o último conjunto que a contém será usado. Com uma estrutura de programação moderna, como o .NET Core, você obterá essa capacidade de empilhamento gratuitamente se usar um provedor de configuração nativo para acessar a configuração do aplicativo. O trecho de código a seguir mostra como você pode implementar o empilhamento em um aplicativo .NET Core:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Inicialização da configuração do aplicativo

Para acessar um repositório de configuração de aplicativo, você pode usar sua cadeia de conexão, que está disponível no portal do Azure. Como as cadeias de conexão contêm informações de credenciais, elas são consideradas segredos. Esses segredos precisam ser armazenados em Azure Key Vault e seu código deve se autenticar no Key Vault para recuperá-los.

Uma opção melhor é usar o recurso identidades gerenciadas no Azure Active Directory. Com identidades gerenciadas, você precisa apenas da URL de ponto de extremidade de configuração de aplicativo para inicializar o acesso ao seu repositório de configuração de aplicativo. Você pode inserir a URL no código do aplicativo (por exemplo, no arquivo *appSettings. JSON* ). Consulte [integrar com identidades gerenciadas do Azure](howto-integrate-azure-managed-service-identity.md) para obter detalhes.

## <a name="app-or-function-access-to-app-configuration"></a>Acesso de aplicativo ou função à configuração de aplicativo

Você pode fornecer acesso à configuração de aplicativo para aplicativos Web ou funções usando qualquer um dos seguintes métodos:

* Por meio do portal do Azure, insira a cadeia de conexão para o repositório de configurações do aplicativo nas configurações do aplicativo do serviço de aplicativo.
* Armazene a cadeia de conexão em seu repositório de configuração de aplicativo no Key Vault e [referencie-a do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Use identidades gerenciadas do Azure para acessar o repositório de configuração de aplicativo. Para obter mais informações, consulte [integrar com identidades gerenciadas do Azure](howto-integrate-azure-managed-service-identity.md).
* Enviar por push a configuração de aplicativo para o serviço de aplicativo. A configuração de aplicativo fornece uma função de exportação (em portal do Azure e a CLI do Azure) que envia dados diretamente para o serviço de aplicativo. Com esse método, você não precisa alterar o código do aplicativo.

## <a name="next-steps"></a>Passos seguintes

* [Chaves e valores](./concept-key-value.md)
