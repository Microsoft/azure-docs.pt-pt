---
title: Código de Análise de Dados Debug Azure Data
description: Aprenda a usar ferramentas de lago de dados Azure para estúdio visual para depurar empregos u-SQL na sua estação de trabalho local.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61472996"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Código de Análise de Dados Debug Azure Data

Pode utilizar ferramentas de lago de dados Azure para o Estúdio Visual para executar e depurar o código Azure Data Lake Analytics na sua estação de trabalho local, tal como pode no serviço Azure Data Lake Analytics.

Aprenda a [executar o script U-SQL na sua máquina local](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Depurar scripts e assemblagens C# localmente

Pode depurar as assembleias C# sem as submeter e registar no serviço Azure Data Lake Analytics. Pode definir pontos de rutura no ficheiro code-behind e num projeto C# referenciado.

### <a name="debug-local-code-in-a-code-behind-file"></a>Depurar código local num ficheiro de código por trás

1. Detete pontos de rutura no ficheiro por trás do código.
2. Selecione **F5** para depurar o script localmente.

> [!NOTE]
   > O procedimento seguinte funciona apenas no Visual Studio 2015. Nas versões mais antigas do Visual Studio, poderá ser necessário adicionar manualmente os ficheiros **PDB.**  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Depurar código local em um projeto C# referenciado

1. Crie um projeto de montagem C# e construa-o para gerar o ficheiro **DLL** de saída.
2. Registe o ficheiro **DLL** utilizando uma declaração U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Defina pontos de interrupção no código C#.
4. Selecione **F5** para depurar o script fazendo referência ao ficheiro C# **DLL** localmente.


## <a name="next-steps"></a>Passos seguintes

- Para um exemplo de uma consulta mais complexa, consulte registos de [websites da Analyze utilizando o Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Para ver detalhes de trabalho, consulte [Use Job Browser e Job View para trabalhos de Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Para utilizar a vista de execução do vértice, consulte Use a vista de [execução vertex em ferramentas](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)de data lake para estúdio visual .
