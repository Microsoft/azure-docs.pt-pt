---
title: Recupere os pares de valor-chave de um ponto no tempo
titleSuffix: Azure App Configuration
description: Recupere os antigos pares de valor-chave usando instantâneos pontuais na configuração da aplicação Azure
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77523667"
---
# <a name="point-in-time-snapshot"></a>Instantâneo para um ponto anterior no tempo

A Azure App Configuration mantém um registo de alterações feitas em pares de valor-chave. Este registo fornece uma linha temporal de alterações de valor-chave. Pode reconstruir a história de qualquer valor-chave e fornecer o seu valor passado a qualquer momento nos sete dias anteriores. Utilizando esta função, pode "viajar no tempo" para trás e recuperar um valor-chave antigo. Por exemplo, pode recuperar as definições de configuração utilizadas antes da implementação mais recente, de modo a reverter a aplicação para a configuração anterior.

## <a name="key-value-retrieval"></a>Recuperação do valor-chave

Pode utilizar a Azure PowerShell para recuperar valores de teclas anteriores.  Utilizar `az appconfig revision list` , adicionar parâmetros apropriados para recuperar os valores necessários.  Especifique a instância de Configuração da Aplicação Azure fornecendo o nome da loja `--name {app-config-store-name}` () ou utilizando uma cadeia de ligação `--connection-string {your-connection-string}` (). Restringir a saída especificando um ponto específico no tempo ( `--datetime` ) e especificando o número máximo de itens a devolver `--top` ().

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Recupere todas as alterações registadas nos seus valores-chave.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Recupere todas as alterações registadas para a chave `environment` e para as etiquetas e `test` `prod` .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Recupere todas as alterações registadas no espaço de chave hierárquica. `environment:prod`

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Recupere todas as alterações registadas para a chave `color` num ponto específico no tempo.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Recupere as últimas 10 alterações registadas nos valores-chave e devolva apenas os valores para `key` , e a hora de `label` `last-modified` marcação.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar uma aplicação Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
