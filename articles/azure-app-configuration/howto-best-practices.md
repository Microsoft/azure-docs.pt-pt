---
title: As melhores práticas de configuração de aplicações do Azure Microsoft Docs
description: Saiba como utilizar melhor a configuração da app Azure
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
ms.openlocfilehash: df56f53b64a35737700529b80c004efeb31eaabc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348668"
---
# <a name="azure-app-configuration-best-practices"></a>As melhores práticas de configuração de aplicações do Azure

Este artigo discute padrões comuns e boas práticas quando está a usar a Configuração de Aplicações Azure.

## <a name="key-groupings"></a>Agrupamentos-chave

A Configuração da Aplicação fornece duas opções para a organização de chaves:

* Principais prefixos
* Etiquetas

Pode utilizar uma ou ambas as opções para agrupar as suas chaves.

*Os principais prefixos* são as partes iniciantes das teclas. Pode agrupar logicamente um conjunto de chaves usando o mesmo prefixo nos seus nomes. Os prefixos podem conter múltiplos componentes ligados `/`por um delimitador, tais como, semelhante a um caminho DEURL, para formar um espaço de nome. Tais hierarquias são úteis quando você está armazenando chaves para muitas aplicações, serviços de componentes e ambientes em uma loja de Configuração de Aplicações.

Uma coisa importante a ter em mente é que as chaves são o que o seu código de aplicação refere para recuperar os valores das definições correspondentes. As chaves não devem mudar, senão terás de modificar o teu código sempre que isso acontecer.

*As etiquetas* são um atributo nas teclas. São usados para criar variantes de uma chave. Por exemplo, pode atribuir etiquetas a várias versões de uma chave. Uma versão pode ser uma iteração, um ambiente, ou alguma outra informação contextual. A sua aplicação pode solicitar um conjunto de valores-chave completamente diferentes, especificando outra etiqueta. Como resultado, todas as referências-chave permanecem inalteradas no seu código.

## <a name="key-value-compositions"></a>Composições de valor-chave

A Configuração da Aplicação trata todas as chaves armazenadas com ela como entidades independentes. A Configuração da Aplicação não tenta inferir qualquer relação entre chaves ou herdar valores-chave com base na sua hierarquia. No entanto, pode agregar vários conjuntos de teclas utilizando etiquetas associadas a um empilhamento de configuração adequado no seu código de aplicação.

Vejamos um exemplo. Suponha que tenha uma definição chamada **Asset1,** cujo valor pode variar em função do ambiente de desenvolvimento. Cria-se uma chave chamada "Asset1" com uma etiqueta vazia e uma etiqueta chamada "Desenvolvimento". Na primeira etiqueta, coloca-se o valor predefinido para **o Ativo1**e coloca-se um valor específico para "Desenvolvimento" neste último.

No seu código, primeiro recupera os valores-chave sem etiquetas e, em seguida, recupera o mesmo conjunto de valores-chave uma segunda vez com a etiqueta "Desenvolvimento". Quando recupera os valores da segunda vez, os valores anteriores das teclas são substituídos. O sistema de configuração .NET Core permite -lhe "empilhar" múltiplos conjuntos de dados de configuração em cima uns dos outros. Se existe uma chave em mais de um conjunto, o último conjunto que a contém é utilizado. Com um quadro de programação moderno, como .NET Core, obtém-se gratuitamente esta capacidade de empilhamento se utilizar um fornecedor de configuração nativo para aceder à Configuração da App. O seguinte código de corte mostra como pode implementar o empilhamento numa aplicação .NET Core:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Utilize etiquetas para permitir configurações diferentes para diferentes ambientes,](./howto-labels-aspnet-core.md) fornece um exemplo completo.

## <a name="app-configuration-bootstrap"></a>Armadilha de configuração de aplicativos

Para aceder a uma loja de Configuração de Aplicações, pode utilizar a sua cadeia de ligação, que está disponível no portal Azure. Como as cordas de ligação contêm informação credencial, são considerados segredos. Estes segredos precisam de ser guardados no Cofre de Chaves Azure, e o seu código tem de autenticar o Cofre chave para os recuperar.

Uma melhor opção é usar a funcionalidade de identidades geridas no Diretório Ativo Azure. Com identidades geridas, só precisa do URL final de configuração da aplicação para o acesso à sua loja de configuração de aplicações. Pode incorporar o URL no seu código de aplicação (por exemplo, no ficheiro *appsettings.json).* Consulte a Integração com o [Azure gerido identidades](howto-integrate-azure-managed-service-identity.md) para mais detalhes.

## <a name="app-or-function-access-to-app-configuration"></a>App ou acesso de função à configuração de apps

Pode fornecer acesso à Configuração de Aplicações para aplicações ou funções web utilizando qualquer um dos seguintes métodos:

* Através do portal Azure, introduza a cadeia de ligação à sua loja de configuração de aplicações nas definições de Aplicação do Serviço de Aplicações.
* Guarde a cadeia de ligação à sua loja de configuração de aplicações no Cofre de Chaves e [remecite-a a partir do Serviço de Aplicações](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Utilize identidades geridas pelo Azure para aceder à loja de configuração de aplicações. Para mais informações, consulte [Integrar com identidades geridas do Azure.](howto-integrate-azure-managed-service-identity.md)
* Flexão de impulso da configuração da app para o serviço de aplicações. A Configuração de Aplicações fornece uma função de exportação (no portal Azure e no Azure CLI) que envia dados diretamente para o Serviço de Aplicações. Com este método, não precisa de alterar o código de aplicação.

## <a name="reduce-requests-made-to-app-configuration"></a>Reduzir os pedidos feitos para a configuração de apps

Pedidos excessivos à Configuração de Aplicações podem resultar em taxas de estrangulamento ou excesso de idade. Para reduzir o número de pedidos feitos:

* Aumente o tempo de atualização, especialmente se os valores de configuração não mudarem frequentemente. Especifique um novo [ `SetCacheExpiration` ](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration)tempo de atualização utilizando o método .

* Assista a uma única *tecla sentinela,* em vez de ver as teclas individuais. Refresque todas as configurações apenas se a tecla sentinela mudar. Consulte a [configuração dinâmica use numa aplicação ASP.NET Core,](enable-dynamic-configuration-aspnet-core.md) por exemplo.

* Utilize a Grelha de Eventos Azure para receber notificações quando a configuração muda, em vez de votar constantemente para quaisquer alterações. Consulte os eventos de configuração de [aplicações route Azure para um ponto final web](./howto-app-configuration-event.md) para mais informações

## <a name="importing-configuration-data-into-app-configuration"></a>Importar dados de configuração para a configuração de apps

A Configuração da Aplicação oferece a opção de [importar](https://aka.ms/azconfig-importexport1) a granel as definições de configuração a partir dos seus ficheiros de configuração atuais, utilizando o portal Azure ou o CLI. Também pode utilizar as mesmas opções para exportar valores a partir da Configuração de Aplicações, por exemplo entre lojas relacionadas. Se quiser configurar uma sincronização contínua com o seu repo GitHub, pode utilizar a nossa [Ação GitHub](https://aka.ms/azconfig-gha2) para que possa continuar a utilizar as suas práticas de controlo de fonte existentes enquanto recebe os benefícios da Configuração da App.

## <a name="next-steps"></a>Passos seguintes

* [Chaves e valores](./concept-key-value.md)
