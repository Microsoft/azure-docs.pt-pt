---
title: Código Debug Azure Data Lake Analytics localmente
description: Saiba como usar o Azure Data Lake Tools para o Visual Studio para depurar empregos U-SQL na sua estação de trabalho local.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 1c7218deac9efba6df6c1284f2578a744e768284
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221031"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Código Debug Azure Data Lake Analytics localmente

Você pode usar Azure Data Lake Tools para o Visual Studio para executar e depurar o código Azure Data Lake Analytics na sua estação de trabalho local, assim como você pode no serviço Azure Data Lake Analytics.

Saiba como [executar o script U-SQL na sua máquina local.](data-lake-analytics-data-lake-tools-local-run.md)

## <a name="debug-scripts-and-c-assemblies-locally"></a>Depurar scripts e assemblagens C# localmente

Pode depurar as assembléias C# sem submeter e registar no serviço Azure Data Lake Analytics. Pode definir pontos de rutura tanto no ficheiro por trás do código como num projeto C# referenciado.

### <a name="debug-local-code-in-a-code-behind-file"></a>Depurar código local num ficheiro por trás do código

1. Desaponte pontos de rutura no ficheiro de detrás de código.
2. Selecione **F5** para depurar o script localmente.

> [!NOTE]
   > O procedimento a seguir funciona apenas no Visual Studio 2015. Nas versões mais antigas do Visual Studio, poderá ter de adicionar manualmente os ficheiros **PDB.**  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Debug código local em um projeto C# referenciado

1. Crie um projeto de montagem C# e construa-o para gerar o ficheiro **DLL** de saída.
2. Registe o ficheiro **DLL** utilizando uma declaração U-SQL:

   ```sql
   CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
   ```
   
3. Defina pontos de interrupção no código C#.
4. Selecione **F5** para depurar o script referindo-se ao ficheiro **C# DLL** localmente.


## <a name="next-steps"></a>Passos seguintes

- Para um exemplo de uma consulta mais complexa, consulte [os registos do site da Análise utilizando o Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Para ver detalhes do trabalho, consulte [use job browser e job view para trabalhos Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Para utilizar a vista de execução do vértice, consulte [a vista de execução do vertex em ferramentas do lago de dados para estúdio visual](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
