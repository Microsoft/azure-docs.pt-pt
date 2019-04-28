---
title: Depurar código do Azure Data Lake Analytics localmente
description: Saiba como utilizar o Azure Data Lake Tools para Visual Studio para depurar tarefas U-SQL na estação de trabalho local.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 0827311218202de447e5cf27356e00c4da020e94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472996"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Depurar código do Azure Data Lake Analytics localmente

Pode utilizar o Azure Data Lake Tools para Visual Studio para executar e depurar o código do Azure Data Lake Analytics na sua estação de trabalho local, tal como no serviço Azure Data Lake Analytics.

Saiba como [executar o script U-SQL no seu computador local](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Depurar scripts e assemblagens C# localmente

Pode depurar assemblagens c# sem as submeter e como registá-los para o serviço Azure Data Lake Analytics. Pode definir pontos de interrupção em ambos os o arquivo de code-behind e num projeto c# referenciado.

### <a name="debug-local-code-in-a-code-behind-file"></a>Depurar código local num arquivo de code-behind

1. Defina pontos de interrupção no arquivo code-behind.
2. Selecione **F5** para depurar o script localmente.

> [!NOTE]
   > O seguinte procedimento funciona apenas no Visual Studio 2015. Em versões mais antigas do Visual Studio, poderá ter de adicionar manualmente os **PDB** ficheiros.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Depurar código local num projeto c# referenciado

1. Criar um projeto de assemblagem c# e crie-o para gerar a saída **DLL** ficheiro.
2. Registe-se a **DLL** ficheiro ao utilizar uma instrução U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Defina pontos de interrupção no código C#.
4. Selecione **F5** para depurar o script referenciando o c# **DLL** ficheiro localmente.


## <a name="next-steps"></a>Passos Seguintes

- Para obter um exemplo de uma consulta mais complexa, consulte [analisar registos de sites com o Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Para ver os detalhes da tarefa, consulte [utilizar o Browser de trabalho e a vista de tarefas para tarefas de Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Para utilizar a vista de execução de vértice, consulte [utilizar a vista de execução de vértice no Data Lake Tools para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
