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
ms.openlocfilehash: 3d9a597e7ced631627a121f3f0757e472f9a4bae
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393578"
---
# <a name="azure-app-configuration-best-practices"></a>Práticas recomendadas de configuração de aplicações do Azure

Este artigo aborda os padrões comuns e as melhores práticas quando estiver usando a configuração de aplicações do Azure.

## <a name="key-groupings"></a>Agrupamentos de chave

Configuração de aplicações fornece duas opções para organizar as chaves:

* Prefixos de chave
* Etiquetas

Pode utilizar uma ou ambas as opções para agrupar as suas chaves.

*Prefixos de chave* são as partes de início de chaves. Pode agrupar logicamente um conjunto de chaves utilizando o mesmo prefixo nos respetivos nomes. Prefixos podem conter vários componentes ligados através de um delimitador, tais como `/`, semelhante a um caminho de URL, para formar um espaço de nomes. Essas hierarquias são úteis quando está armazenando chaves para muitos aplicativos, serviços de componentes e ambientes num arquivo de configuração de aplicações.

Uma coisa importante a lembrar é que as chaves são o que referencia o código da aplicação para obter os valores das definições correspondentes. Não devem alterar as chaves, caso contrário, precisará modificar seu código sempre que ocorre.

*Etiquetas* são um atributo em chaves. Eles são usados para criar variantes de uma chave. Por exemplo, pode atribuir as etiquetas para várias versões de uma chave. Uma versão pode ser uma iteração, um ambiente ou outras informações contextuais. Seu aplicativo pode solicitar um conjunto completamente diferente de valores de chave, especificando outra etiqueta. Como resultado, a todas as referências de chave permanecem inalteradas em seu código.

## <a name="key-value-compositions"></a>Compositions de chave-valor

Configuração de aplicações trata todas as chaves armazenadas com ele, como entidades independentes. Configuração de aplicações não tenta inferir qualquer relação entre chaves ou herdar de valores de chave com base na respetiva hierarquia. No entanto, pode agregar vários conjuntos de chaves, utilizando etiquetas juntamente com a configuração adequada empilhamento no código da aplicação.

Vamos examinar um exemplo. Suponhamos que tenha uma definição denominada **Asset1**, cujo valor pode variar com base no ambiente de desenvolvimento. Criar uma chave denominada "Asset1" com uma etiqueta vazia e uma etiqueta com o nome "Desenvolvimento". Na primeira etiqueta, coloca o valor predefinido para **Asset1**, e colocar um valor específico para "Desenvolvimento" no segundo.

Em seu código, primeiro, recupera os valores de chave sem quaisquer etiquetas e, em seguida, recupere o mesmo conjunto de valores de chave de uma segunda vez com a etiqueta de "Desenvolvimento". Quando recupera os valores na segunda vez, os valores anteriores das chaves são substituídos. O sistema de configuração do .NET Core permite-lhe "empilha" vários conjuntos de dados de configuração em cima do outro. Se existir uma chave em mais de um conjunto, é utilizado o último conjunto que o contém. Com uma estrutura de programação moderna, como o .NET Core, tem esta capacidade empilhamento gratuitamente se usar um fornecedor de configuração nativo para acessar a configuração de aplicações. O fragmento de código seguinte mostra como pode implementar o empilhamento num aplicativo do .NET Core:

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

Para aceder a um arquivo de configuração de aplicações, pode utilizar a cadeia de ligação, o que está disponível no portal do Azure. Como as cadeias de ligação contêm informações de credenciais, que sejam considerados segredos. Estes segredos tem de ser armazenados no Azure Key Vault e seu código tem de ser autenticado para o Key Vault para recuperá-los.

Uma opção melhor é usar a funcionalidade de identidades geridas no Azure Active Directory. Com identidades geridas, precisa apenas o URL de ponto final de configuração de aplicações para acesso de arranque de configuração ao seu arquivo de configuração de aplicações. Pode incorporar o URL no código da aplicação (por exemplo, no *appSettings* ficheiro). Ver [integrar com o Azure managed identidades](howto-integrate-azure-managed-service-identity.md) para obter detalhes.

## <a name="app-or-function-access-to-app-configuration"></a>Acesso de aplicação ou função a configuração de aplicações

Pode fornecer acesso à configuração de aplicação para aplicações web ou funções utilizando qualquer um dos seguintes métodos:

* Através do portal do Azure, introduza a cadeia de ligação ao seu arquivo de configuração de aplicações nas definições da aplicação do serviço de aplicações.
* Store a cadeia de ligação ao seu arquivo de configuração de aplicações no Key Vault e [referenciá-lo a partir do serviço de aplicações](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Use Azure geridos identidades para aceder ao arquivo de configuração de aplicações. Para obter mais informações, consulte [integrar com o Azure managed identidades](howto-integrate-azure-managed-service-identity.md).
* Configuração de push de configuração de aplicações no serviço de aplicações. Configuração de aplicações fornece uma função de exportação (no portal do Azure e a CLI do Azure) que envia dados diretamente no serviço de aplicações. Com esse método, não precisa alterar o código do aplicativo em todos os.

## <a name="next-steps"></a>Passos Seguintes

* [As chaves e valores](./concept-key-value.md)
