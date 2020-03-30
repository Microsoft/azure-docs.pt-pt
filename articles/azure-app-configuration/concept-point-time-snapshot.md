---
title: Recuperar pares de valor-chave a partir de um ponto-em-tempo
titleSuffix: Azure App Configuration
description: Recupere os pares de valor-chave antigos usando instantâneos pontuais na configuração da app Azure
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523667"
---
# <a name="point-in-time-snapshot"></a>Instantâneo para um ponto anterior no tempo

A Configuração de Aplicações Azure mantém um registo de alterações feitas em pares de valor-chave. Este registo fornece uma cronologia de alterações de valor-chave. Pode reconstruir a história de qualquer valor-chave e fornecer o seu valor passado a qualquer momento nos sete dias anteriores. Utilizando esta funcionalidade, pode "viajar no tempo" para trás e recuperar um antigo valor-chave. Por exemplo, pode recuperar as definições de configuração utilizadas antes da implementação mais recente, a fim de reverter a aplicação para a configuração anterior.

## <a name="key-value-retrieval"></a>Recuperação de valor-chave

Pode utilizar o Azure PowerShell para recuperar valores-chave passados.  Utilizar, `az appconfig revision list`adicionando parâmetros adequados para recuperar os valores necessários.  Especifique a instância de configuração da`--name {app-config-store-name}`aplicação Azure fornecendo o nome da loja ( ) ou utilizando uma corda de ligação ().`--connection-string {your-connection-string}` Restringir a saída especificando um ponto`--datetime`específico no tempo e especificando o número`--top`máximo de itens para devolver ( ).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Recupere todas as alterações registadas nos seus valores-chave.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Recupere todas as `environment` alterações gravadas para a chave e as etiquetas `test` e `prod`.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Recupere todas as alterações registadas no espaço-chave `environment:prod`hierárquico.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Recupere todas as `color` alterações gravadas para a chave num determinado ponto no tempo.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Recupere as últimas 10 alterações gravadas nos `key` `label`seus `last-modified` valores-chave e devolva apenas os valores para, e carimbo de tempo.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
