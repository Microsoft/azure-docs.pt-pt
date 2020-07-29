---
title: Configuração de aplicativos Azure as melhores práticas Microsoft Docs
description: Saiba como utilizar melhor a configuração da aplicação Azure
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
ms.openlocfilehash: 4da024eb4eb3747b8e0d6b291ca5b00df12aaeab
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87367525"
---
# <a name="azure-app-configuration-best-practices"></a>Melhores práticas de Configuração de Aplicativos Azure

Este artigo discute padrões comuns e boas práticas quando está a usar a Configuração da App Azure.

## <a name="key-groupings"></a>Agrupamentos-chave

A Configuração da Aplicação oferece duas opções para organizar chaves:

* Prefixos de chaves
* Etiquetas

Pode utilizar uma ou ambas as opções para agrupar as suas chaves.

*Os prefixos das chaves* são as partes inicio das teclas. É logicamente possível agrupar um conjunto de chaves usando o mesmo prefixo nos seus nomes. Os prefixos podem conter vários componentes ligados por um delimiter, tais `/` como, semelhante a um caminho URL, para formar um espaço de nome. Tais hierarquias são úteis quando você está armazenando chaves para muitas aplicações, serviços de componentes e ambientes em uma loja de Configuração de Aplicações.

Uma coisa importante a ter em mente é que as teclas são o que o seu código de aplicação refere para recuperar os valores das definições correspondentes. As chaves não devem ser alteradas, senão terás de modificar o teu código sempre que isso acontecer.

*As etiquetas* são um atributo nas teclas. São usados para criar variantes de uma chave. Por exemplo, pode atribuir etiquetas a várias versões de uma chave. Uma versão pode ser uma iteração, um ambiente, ou alguma outra informação contextual. A sua aplicação pode solicitar um conjunto totalmente diferente de valores-chave especificando outro rótulo. Como resultado, todas as referências principais permanecem inalteradas no seu código.

## <a name="key-value-compositions"></a>Composições de valor-chave

A Configuração de Aplicativos trata todas as teclas armazenadas com ela como entidades independentes. A Configuração de Aplicações não tenta inferir qualquer relação entre teclas ou herdar valores-chave com base na sua hierarquia. No entanto, pode agregar vários conjuntos de teclas utilizando etiquetas juntamente com o empilhamento de configuração adequado no seu código de aplicação.

Vejamos um exemplo. Suponha que tenha uma definição chamada **Asset1,** cujo valor pode variar em função do ambiente de desenvolvimento. Cria-se uma chave chamada "Asset1" com uma etiqueta vazia e uma etiqueta chamada "Desenvolvimento". Na primeira etiqueta, coloca-se o valor padrão para **o Ativo1,** e coloca-se um valor específico para "Desenvolvimento" nesta última.

No seu código, primeiro recupera os valores-chave sem etiquetas e, em seguida, recupera o mesmo conjunto de valores-chave uma segunda vez com a etiqueta "Desenvolvimento". Quando recupera os valores da segunda vez, os valores anteriores das teclas são substituídos. O sistema de configuração .NET Core permite -lhe "empilhar" vários conjuntos de dados de configuração em cima uns dos outros. Se existir uma chave em mais de um conjunto, o último conjunto que a contém é utilizado. Com uma estrutura de programação moderna, como .NET Core, obtém-se esta capacidade de empilhamento gratuitamente se utilizar um fornecedor de configuração nativa para aceder à Configuração da Aplicação. O seguinte corte de código mostra como pode implementar o empilhamento numa aplicação .NET Core:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[A utilização de rótulos para permitir configurações diferentes para diferentes ambientes](./howto-labels-aspnet-core.md) fornece um exemplo completo.

## <a name="app-configuration-bootstrap"></a>Sapatilha de configuração de aplicativos

Para aceder a uma loja de Configuração de Aplicações, pode utilizar a sua cadeia de ligação, que está disponível no portal Azure. Como as cordas de ligação contêm informações credenciais, são consideradas segredos. Estes segredos precisam de ser guardados no Cofre da Chave Azure, e o seu código deve autenticar-se no Cofre de Chaves para os recuperar.

Uma melhor opção é utilizar as identidades geridas no Azure Ative Directory. Com identidades geridas, só precisa do URL de ponto final de configuração da aplicação para aceder à sua loja de Configuração de Aplicações. Pode incorporar o URL no seu código de aplicação (por exemplo, no *appsettings.jsno* ficheiro). Consulte [a Integração com identidades geridas pela Azure](howto-integrate-azure-managed-service-identity.md) para mais detalhes.

## <a name="app-or-function-access-to-app-configuration"></a>App ou função acesso à Configuração de Aplicações

Pode fornecer acesso à Configuração de Aplicações para aplicações web ou funções utilizando qualquer um dos seguintes métodos:

* Através do portal Azure, introduza o fio de ligação à sua loja de Configuração de Aplicações nas definições de Aplicação do Serviço de Aplicações.
* Guarde o fio de ligação à sua loja de configuração de aplicações no Cofre de Chaves e [refira-o a partir do Serviço de Aplicações.](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)
* Utilize identidades geridas pela Azure para aceder à loja de Configuração de Aplicações. Para obter mais informações, consulte [Integrar-se com identidades geridas pelo Azure.](howto-integrate-azure-managed-service-identity.md)
* Push configuração da Configuração de Aplicação para Serviço de Aplicações. A Configuração de Aplicativos fornece uma função de exportação (no portal Azure e no Azure CLI) que envia dados diretamente para o Serviço de Aplicações. Com este método, não é necessário alterar o código de aplicação.

## <a name="reduce-requests-made-to-app-configuration"></a>Reduzir pedidos feitos para configuração de aplicativos

Pedidos excessivos para a Configuração da Aplicação podem resultar em encargos de estrangulamento ou excesso de velocidade. Para reduzir o número de pedidos feitos:

* Aumente o tempo limite de atualização, especialmente se os valores de configuração não mudarem frequentemente. Especifique um novo tempo limite de atualização utilizando o [ `SetCacheExpiration` método](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration).

* Assista a uma única *chave sentinela,* em vez de observar as chaves individuais. Refresque todas as configurações apenas se a tecla sentinela mudar. Consulte [a configuração dinâmica do Uso numa aplicação Core ASP.NET,](enable-dynamic-configuration-aspnet-core.md) por exemplo.

* Utilize a Grelha de Eventos Azure para receber notificações quando a configuração muda, em vez de sondar constantemente quaisquer alterações. Para mais informações, consulte [os eventos de Configuração da Aplicação Route Azure para um ponto final web](./howto-app-configuration-event.md)

## <a name="importing-configuration-data-into-app-configuration"></a>Importar dados de configuração para configuração de aplicativos

A Configuração da Aplicação oferece a opção de importar em massa [as](https://aka.ms/azconfig-importexport1) suas definições de configuração a partir dos seus ficheiros de configuração atuais usando o portal Azure ou CLI. Também pode utilizar as mesmas opções para exportar valores a partir da Configuração de Aplicações, por exemplo entre lojas relacionadas. Se quiser configurar uma sincronização em curso com o seu repo GitHub, pode utilizar a nossa [Ação GitHub](https://aka.ms/azconfig-gha2) para que possa continuar a utilizar as suas práticas de controlo de origem existentes enquanto recebe os benefícios da Configuração de Aplicações.

## <a name="next-steps"></a>Passos seguintes

* [Chaves e valores](./concept-key-value.md)
