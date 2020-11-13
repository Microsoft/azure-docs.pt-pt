---
title: Utilizar o tipo de conteúdo JSON para chaves-valores
titleSuffix: Azure App Configuration
description: Saiba como utilizar o tipo de conteúdo JSON para valores-chave
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: ee262c0eb2431085e71d8ee0035bcdab9833d1cf
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565777"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>Alavancar o tipo de conteúdo para armazenar valores-chave JSON na Configuração de Aplicações

Os dados são armazenados na Configuração da Aplicação como valores-chave, onde os valores são tratados como o tipo de corda por padrão. No entanto, pode especificar um tipo personalizado aproveitando a propriedade do tipo de conteúdo associada a cada valor-chave, para que possa preservar o tipo original dos seus dados ou ter a sua aplicação a comportar-se de forma diferente com base no tipo de conteúdo.


## <a name="overview"></a>Descrição geral

Na Configuração de Aplicações, pode utilizar o tipo de mídia JSON como o tipo de conteúdo dos seus valores-chave para obter benefícios como:
- **Gestão de dados mais simples** : Gerir valores-chave, como matrizes, tornar-se-á muito mais fácil no portal Azure.
- **Exportação de dados melhorados** : Tipos primitivos, matrizes e objetos JSON serão preservados durante a exportação de dados.
- **Suporte nativo com o fornecedor de Configuração** de Aplicações : Os valores-chave com o tipo de conteúdo JSON funcionarão bem quando consumidos pelas bibliotecas do fornecedor de Configuração de Aplicações nas suas aplicações.

#### <a name="valid-json-content-type"></a>Tipo de conteúdo válido JSON

Os tipos de mídia, tal como [definidos aqui,](https://www.iana.org/assignments/media-types/media-types.xhtml)podem ser atribuídos ao tipo de conteúdo associado a cada valor-chave.
Um tipo de mídia é composto por um tipo e um subtipo. Se o tipo for `application` e o subtipo (ou sufixo) `json` for, o tipo de mídia será considerado um tipo de conteúdo JSON válido.
Alguns exemplos de tipos de conteúdo JSON válidos são:

- application/json
- aplicação/atividade+json
- aplicação/vnd.foobar+json;charset=utf-8

#### <a name="valid-json-values"></a>Valores válidos de JSON

Quando um valor-chave tem o tipo de conteúdo JSON, o seu valor deve estar em formato JSON válido para que os clientes o processem corretamente. Caso contrário, os clientes podem falhar ou recuar e tratá-lo como formato de corda.
Alguns exemplos de valores JSON válidos são:

- "John Doe"
- 723
- false
- nulo
- "2020-01-01T12:34:56.789Z"
- [1, 2, 3, 4]
- {"ObjectSetting":{"Targeting":{"Default":true,"Level":"Information"}}}

> [!NOTE]
> Para o resto deste artigo, qualquer valor-chave na Configuração de Aplicações que tenha um tipo de conteúdo JSON válido e um valor JSON válido será referido como **valor-chave JSON**. 

Neste tutorial, irá aprender a:
> [!div class="checklist"]
> * Criar valores-chave JSON na Configuração de Aplicações.
> * Importar valores-chave JSON a partir de um ficheiro JSON.
> * Exporte os valores-chave JSON para um ficheiro JSON.
> * Consuma os valores-chave JSON nas suas aplicações.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requer a versão 2.10.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>Criar valores-chave JSON na Configuração de Aplicações

Os valores-chave JSON podem ser criados usando o portal Azure, O Azure CLI ou importando a partir de um ficheiro JSON. Nesta secção, encontrará instruções para criar os mesmos valores-chave JSON utilizando os três métodos.

### <a name="create-json-key-values-using-azure-portal"></a>Criar valores-chave JSON utilizando o portal Azure

Navegue na sua loja de configuração de **aplicações** e selecione Configuration Explorer  >  **Crie**  >  **valor-chave** para adicionar os seguintes pares de valor-chave:

| Chave | Valor | Tipo de Conteúdo |
|---|---|---|
| Definições:BackgroundColor | "Verde" | application/json |
| Definições:FontSize | 24 | application/json |
| Definições:UseDefaultRouting | false | application/json |
| Definições:Bloqueados | nulo | application/json |
| Definições:ReleaseDate | "2020-08-04T12:34:56.789Z" | application/json |
| Definições:RolloutPercentage | [25,50,75,100] | application/json |
| Definições:Registar | {"Test":{"Level":"Debug"}""Prod":{"Level":"Warning"}} | application/json |

Deixe **a etiqueta** vazia e selecione **Aplicar**.

### <a name="create-json-key-values-using-azure-cli"></a>Criar valores-chave JSON utilizando O Azure CLI

Os seguintes comandos criarão valores-chave JSON na sua loja de Configuração de Aplicações. `<appconfig_name>`Substitua-o pelo nome da sua loja de Configuração de Aplicações.

```azurecli-interactive
appConfigName=<appconfig_name>
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BackgroundColor --value \"Green\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:FontSize --value 24
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:UseDefaultRouting --value false
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BlockedUsers --value null
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:ReleaseDate --value \"2020-08-04T12:34:56.789Z\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:RolloutPercentage --value [25,50,75,100]
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:Logging --value {\"Test\":{\"Level\":\"Debug\"},\"Prod\":{\"Level\":\"Warning\"}}
```

> [!IMPORTANT]
> Se estiver a utilizar o Azure CLI ou a Azure Cloud Shell para criar valores-chave JSON, o valor fornecido deve ser uma cadeia JSON em fuga.

### <a name="import-json-key-values-from-a-file"></a>Importar valores-chave JSON de um ficheiro

Crie um ficheiro JSON chamado `Import.json` com o seguinte conteúdo e importe como valores-chave para a Configuração da Aplicação:

```json
{
  "Settings": {
    "BackgroundColor": "Green",
    "BlockedUsers": null,
    "FontSize": 24,
    "Logging": {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}},
    "ReleaseDate": "2020-08-04T12:34:56.789Z",
    "RolloutPercentage": [25,50,75,100],
    "UseDefaultRouting": false
  }
}
```

```azurecli-interactive
az appconfig kv import -s file --format json --path "~/Import.json" --content-type "application/json" --separator : --depth 2
```

> [!Note]
> O `--depth` argumento é usado para aplainar dados hierárquicos de um ficheiro em pares de valor-chave. Neste tutorial, a profundidade é especificada para demonstrar que também pode armazenar objetos JSON como valores na Configuração da Aplicação. Se a profundidade não for especificada, os objetos JSON serão achatados ao nível mais profundo por defeito.

Os valores-chave JSON que criou devem ser assim na Configuração da Aplicação:

![Loja Config contendo valores-chave JSON](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>Exportar valores-chave JSON para um ficheiro

Um dos principais benefícios da utilização de valores-chave JSON é a capacidade de preservar o tipo de dados original dos seus valores durante a exportação. Se um valor-chave na Configuração de Aplicações não tiver o tipo de conteúdo JSON, o seu valor será tratado como string. 

Considere estes valores-chave sem o tipo de conteúdo JSON:

| Chave | Valor | Tipo de Conteúdo |
|---|---|---|
| Definições:FontSize | 24 | |
| Definições:UseDefaultRouting | false | |

Quando exportar estes valores-chave para um ficheiro JSON, os valores serão exportados como cordas:
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

No entanto, ao exportar valores-chave JSON para um ficheiro, todos os valores preservarão o seu tipo de dados original. Para verificar isto, exporte valores-chave da configuração da sua aplicação para um ficheiro JSON. Verá que o ficheiro exportado tem o mesmo conteúdo que o `Import.json` ficheiro que já importou.

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> Se a sua loja de Configuração de Aplicações tiver alguns valores-chave sem o tipo de conteúdo JSON, também serão exportados para o mesmo ficheiro em formato de cadeia. Se pretender exportar apenas os valores-chave JSON, atribua uma etiqueta ou prefixo único aos valores-chave JSON e utilize a etiqueta ou a filtragem do prefixo durante a exportação.


## <a name="consuming-json-key-values-in-applications"></a>Consumir valores-chave JSON nas aplicações

A forma mais fácil de consumir os valores-chave JSON na sua aplicação é através das bibliotecas do fornecedor de Configuração de Aplicações. Com as bibliotecas do fornecedor, não precisa de implementar um manuseamento especial dos valores-chave JSON na sua aplicação. São sempre desseserizados para a sua aplicação da mesma forma que outras bibliotecas de fornecedores de configuração JSON fazem. 

> [!Important]
> O suporte nativo para valores-chave JSON está disponível na versão 4.0.0 (ou posterior). Consulte a secção [*de passos seguintes*](#next-steps) para mais detalhes.

Se estiver a utilizar a API SDK ou REST para ler valores-chave da Configuração da Aplicação, com base no tipo de conteúdo, a sua aplicação é responsável por desseeciar o valor de um valor-chave JSON utilizando qualquer deserializador JSON padrão.


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que sabe trabalhar com os valores-chave JSON na sua loja de Configuração de Aplicações, crie uma aplicação para consumir estes valores-chave:

* [ASP.NET Início rápido do Núcleo](./quickstart-aspnet-core-app.md)
    * Pré-requisito: [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) pacote v4.0.0 ou mais tarde.

* [.NET Core quickstart](./quickstart-dotnet-core-app.md)
    * Pré-requisito: [Microsoft.Extensions.Configuration. Pacote AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) v4.0.0 ou mais tarde.
