---
title: Escrever funções R avançadas
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Saiba como escrever uma função de R para o cálculo estatístico avançada na base de dados do SQL Azure com serviços do Machine Learning (pré-visualização).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60702457"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Escrever funções avançadas do R na base de dados do SQL Azure com serviços do Machine Learning (pré-visualização)

Este artigo descreve como incorporar R matemático e procedimento armazenado de funções de utilitário no SQL. Funções de estatística avançadas que são de difícil implementação em T-SQL podem ser feitas em R com apenas uma única linha de código.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, [criar uma conta](https://azure.microsoft.com/free/) antes de começar.

- Para executar o código de exemplo Nestes exercícios, primeiro tem de ter uma SQL database do Azure com o Machine Learning Services (com R) ativada. Durante a pré-visualização pública, Microsoft irá carregar e ativar o machine learning para a base de dados nova ou existente. Siga os passos em [Inscreva-se na pré-visualização](sql-database-machine-learning-services-overview.md#signup).

- Certifique-se de que instalou a versão mais recente [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Pode executar scripts R com outro gestão de bases de dados ou ferramentas de consulta, mas este início rápido irá utilizar o SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Criar um procedimento armazenado para gerar números aleatórios

Para simplificar, vamos utilizar o R `stats` pacote que tenha instalado e carregados por padrão, a base de dados do SQL Azure com serviços do Machine Learning (pré-visualização). O pacote contém centenas de funções para tarefas comuns de estatísticas, entre eles o `rnorm` função. Esta função gera um número especificado de números aleatórios, com distribuição normal, com um desvio padrão e meio.

Por exemplo, o seguinte código de R retorna 100 números numa média de 50, tendo em conta um desvio-padrão de 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Para chamar esta linha de R a partir de T-SQL, execute `sp_execute_external_script` e adicione a função de R no parâmetro de script R, como este:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

E se pretende que seja mais fácil de gerar um conjunto diferente de números aleatórios?

Isso é fácil quando combinado com o SQL. Define um procedimento armazenado que obtém os argumentos do usuário, em seguida, passa os argumentos para o script R como variáveis.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- A primeira linha define cada um dos parâmetros de entrada SQL que são necessários quando o procedimento armazenado é executado.

- A linha que começa com `@params` define todas as variáveis utilizadas pelo código de R e os tipos de dados SQL correspondentes.

- As linhas que se seguem imediatamente mapeiam os nomes de parâmetro SQL para os nomes de variáveis de R correspondentes.

Agora que os encapsulei a função de R num procedimento armazenado, pode facilmente chamar a função e passar valores diferentes, como este:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Utilize as funções de utilitário de R para resolução de problemas

O `utils` pacote, instalado por padrão, fornece uma variedade de funções de utilitário para investigar o ambiente atual de R. Estas funções podem ser útil se considera discrepâncias na forma como seu código de R realiza em SQL e em ambientes externos. Por exemplo, poderá utilizar o R `memory.limit()` função para obter a memória para o ambiente atual de R.

Uma vez que o `utils` pacote está instalado mas não é carregado por predefinição, tem de utilizar o `library()` função carregá-lo primeiro.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> Que utilizadores gostam de usar as funções de temporização de sistema em R, tal como `system.time` e `proc.time`, para capturar a hora utilizada por processos de R e analisar problemas de desempenho.
