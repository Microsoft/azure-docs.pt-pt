---
title: Recupere os pares de valor-chave de um ponto no tempo
titleSuffix: Azure App Configuration
description: Recupere os antigos pares de valores-chave utilizando instantâneos pontuais na Configuração da Aplicação Azure, que mantém um registo de alterações nos valores-chave.
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: fa2dbb11b3b8b9afd90c7f6fe3ffe77e2e57c4e6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929902"
---
# <a name="point-in-time-snapshot"></a>Instantâneo para um ponto anterior no tempo

A Azure App Configuration mantém um registo de alterações feitas aos valores-chave. Este registo fornece uma linha temporal de alterações de valor-chave. Pode reconstruir a história de qualquer valor-chave e fornecer o seu valor passado a qualquer momento dentro do período de história chave (7 dias para lojas de nível livre ou 30 dias para lojas standard). Utilizando esta função, pode "viajar no tempo" para trás e recuperar um valor-chave antigo. Por exemplo, pode recuperar as definições de configuração utilizadas antes da implementação mais recente, de modo a reverter a aplicação para a configuração anterior.

## <a name="key-value-retrieval"></a>Recuperação do valor-chave

Pode utilizar o portal Azure ou o CLI para recuperar valores-chave passados. No Azure CLI, `az appconfig revision list` utilize, adicionando parâmetros apropriados para recuperar os valores necessários.  Especifique a instância de Configuração da Aplicação Azure fornecendo o nome da loja `--name <app-config-store-name>` () ou utilizando uma cadeia de ligação `--connection-string <your-connection-string>` (). Restringir a saída especificando um ponto específico no tempo ( `--datetime` ) e especificando o número máximo de itens a devolver `--top` ().

Se não tiver o Azure CLI instalado localmente, pode utilizar opcionalmente [a Azure Cloud Shell](../cloud-shell/overview.md).

Recupere todas as alterações registadas nos seus valores-chave.

```azurecli
az appconfig revision list --name <your-app-config-store-name>.
```

Recupere todas as alterações registadas para a chave `environment` e para as etiquetas e `test` `prod` .

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

Recupere todas as alterações registadas no espaço de chave hierárquica. `environment:prod`

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

Recupere todas as alterações registadas para a chave `color` num ponto específico no tempo.

```azurecli
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

Recupere as últimas 10 alterações registadas nos valores-chave e devolva apenas os valores para `key` , e a hora de `label` `last_modified` marcação.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web ASP.NET Core](./quickstart-aspnet-core-app.md)