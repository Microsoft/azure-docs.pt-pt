---
title: Práticas recomendadas de configuração de aplicações do Azure | Documentos da Microsoft
description: Saiba como utilizar melhor a configuração de aplicações do Azure
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
ms.openlocfilehash: d1275a48de5cad9321186ba20860d853b8ce55ad
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413629"
---
# <a name="azure-app-configuration-best-practices"></a>Práticas recomendadas de configuração de aplicações do Azure

Este artigo aborda os padrões e práticas comuns ao utilizar a configuração de aplicações do Azure.

## <a name="key-groupings"></a>Agrupamentos de chave

Configuração de aplicações fornece duas opções para organizar as chaves: prefixos ou etiquetas da chave. Pode utilizar um ou ambos.

Prefixos de chaves são as partes de início de chaves. Pode agrupar logicamente um conjunto de chaves utilizando o mesmo prefixo nos respetivos nomes. Prefixos podem conter vários componentes ligados em conjunto por um delimitador como `/`, semelhante a um caminho de URL, para formar um espaço de nomes. Essas hierarquias são úteis quando estiver a armazenar as chaves para muitos aplicativos, serviços de componentes e ambientes num arquivo de configuração de aplicações. Uma coisa importante a lembrar é que as chaves são o que referencia o código da aplicação para obter os valores das definições correspondentes. Uma chave não deve alterar, caso contrário, terá de modificar o código de cada vez que acontece.

Etiquetas é um atributo em chaves. Eles são usados para criar variantes de uma chave. Por exemplo, pode atribuir as etiquetas para várias versões de uma chave. Uma versão pode ser uma iteração, ambiente ou outras informações contextuais. Seu aplicativo pode solicitar um conjunto completamente diferente de valores de chave especificando outra etiqueta. Todas as referências de chave podem permanecer inalteradas.

## <a name="key-value-compositions"></a>Compositions de chave-valor

Configuração de aplicações trata todas as chaves armazenadas com ele, como entidades independentes. Ele não tenta inferir qualquer relação entre chaves ou herdar de valores de chave com base na respetiva hierarquia. Pode agregar vários conjuntos de chaves, no entanto, utilização de etiquetas juntamente com a configuração adequada empilhamento no código da aplicação.

Vamos examinar um exemplo. Tem uma definição **Asset1** cujo valor pode variar para o ambiente de "Desenvolvimento". Pode criar uma chave denominada "Asset1" com uma etiqueta vazia e uma etiqueta denominada "Desenvolvimento". Coloca o valor predefinido para **Asset1** no primeiro e qualquer valor específico para "Desenvolvimento" no segundo. Em seu código, primeiro, recupera os valores de chave sem qualquer etiqueta e, em seguida, com uma etiqueta de "Desenvolvimento" para substituir quaisquer valores anteriores das mesmas chaves. Se usar uma estrutura moderna de programação, como o .NET Core, pode obter esta capacidade empilhamento gratuitamente se usar um fornecedor de configuração nativo para acessar a configuração de aplicações. O fragmento de código seguinte mostra como pode implementar o empilhamento numa aplicação .NET Core.

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Use(KeyFilter.Any, LabelFilter.Null)
           .Use(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Arranque de configuração de aplicação

Para aceder a um arquivo de configuração de aplicações, pode utilizar a cadeia de ligação, o que está disponível no portal do Azure. Cadeias de ligação contém informações de credenciais e são consideradas como segredos. Eles precisam ser armazenados num cofre de chave. A melhor opção é utilizar o Azure identidade gerida. Com esse método, só precisa URL de ponto final de configuração de aplicações para inicializar o acesso ao seu arquivo de configuração. Pode incorporar o URL no código da aplicação (por exemplo, no *appSettings* ficheiro). Ver [integrar com o Azure managed identidades](howto-integrate-azure-managed-service-identity.md) para obter mais detalhes.

## <a name="web-app-or-function-access-to-app-configuration"></a>Acesso de aplicação Web ou uma função a configuração de aplicações

Pode introduzir a cadeia de ligação ao seu arquivo de configuração de aplicações para as definições da aplicação do serviço de aplicações através do portal do Azure. Pode também armazená-lo no Key Vault e [referenciá-lo a partir do serviço de aplicações](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references). Também pode utilizar o Azure identidade para aceder ao arquivo de configuração gerida. Ver [integrar com o Azure managed identidades](howto-integrate-azure-managed-service-identity.md) para obter mais detalhes.

Em alternativa, pode enviar a configuração da configuração de aplicação no serviço de aplicações. Configuração de aplicações fornece uma função de exportação (no portal do Azure e CLI) que envia dados diretamente no serviço de aplicações. Com esse método, não é necessário alterar o código do aplicativo em todos os.

## <a name="next-steps"></a>Passos Seguintes

* [As chaves e valores](./concept-key-value.md)
