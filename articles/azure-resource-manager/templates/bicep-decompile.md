---
title: Converter modelos entre JSON e Bicep
description: Descreve comandos para converter modelos de Gestor de Recursos Azure de Bicep para JSON e de JSON para Bicep.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 6d242f5846996cd0f5b9510a1a2b9f2bf063a0c7
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103422229"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>Conversão de modelos ARM entre JSON e Bicep

Este artigo descreve como converte os modelos Azure Resource Manager (modelos ARM) de JSON a Bicep e de Bicep a JSON.

Tem de ter o [Bicep CLI instalado](bicep-install.md) para executar os comandos de conversão.

Os comandos de conversão produzem modelos que são funcionalmente equivalentes. No entanto, podem não ser exatamente os mesmos na sua implementação. Converter um modelo de JSON para Bicep e, em seguida, de volta para JSON provavelmente resulta em um modelo com sintaxe diferente do modelo original. Quando implantados, os modelos convertidos produzem os mesmos resultados.

## <a name="convert-from-json-to-bicep"></a>Converter de JSON para Bicep

O Bicep CLI fornece um comando para decompiler qualquer modelo JSON existente para um ficheiro Bicep. Para descompiler um ficheiro JSON, utilize:

```azurecli
bicep decompile mainTemplate.json
```

Este comando fornece um ponto de partida para a autoria bicep. O comando não funciona para todos os modelos. Atualmente, os modelos aninhados só podem ser descompilados se usarem o âmbito de avaliação de expressão 'interior'. Os modelos que usam laços de cópia não podem ser descompiliados.

## <a name="convert-from-bicep-to-json"></a>Converter de Bicep para JSON

O Bicep CLI também fornece um comando para converter Bicep para JSON. Para construir um ficheiro JSON, utilize:

```azurecli
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>Modelo de exportação e conversão

Pode exportar o modelo para um grupo de recursos e, em seguida, passá-lo diretamente para o comando decompile. O exemplo a seguir mostra como descompilar um modelo exportado.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Exporte o modelo](export-template-portal.md) através do portal.

Use `bicep decompile <filename>` no ficheiro descarregado.

---

## <a name="side-by-side-view"></a>Vista lado a lado

O [recreio Bicep](https://aka.ms/bicepdemo) permite-lhe visualizar ficheiros JSON e Bicep equivalentes lado a lado. Pode selecionar um modelo de amostra para ver ambas as versões. Ou, selecione `Decompile` para carregar o seu próprio modelo JSON e ver o ficheiro Bicep equivalente.

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre o Bicep, consulte o [tutorial de Bicep.](./bicep-tutorial-create-first-bicep.md)
