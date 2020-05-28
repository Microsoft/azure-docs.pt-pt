---
title: Escrever funções R avançadas
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Saiba como escrever uma função R para computação estatística avançada na Base de Dados Azure SQL utilizando serviços de aprendizagem automática (pré-visualização).
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
ROBOTS: NOINDEX
ms.openlocfilehash: a7d06b08ce5047c24dd09278a16b9646dcb4e1f7
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048218"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Escreva funções R avançadas na Base de Dados Azure SQL utilizando serviços de aprendizagem automática (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve como incorporar funções matemáticas e de utilidade R num procedimento armazenado por SQL. Funções estatísticas avançadas que são complicadas de implementar em T-SQL podem ser feitas em R com apenas uma linha de código.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

- Para executar o código de exemplo nestes exercícios, tem primeiro de ter a Base de [Dados Azure SQL com serviços de aprendizagem automática (com R)](machine-learning-services-overview.md) ativados.

- Certifique-se de que instalou o mais recente Estúdio de Gestão de [Servidores SQL](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Pode executar scripts R utilizando outras ferramentas de gestão de bases de dados ou consultas, mas neste arranque rápido irá utilizar SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Criar um procedimento armazenado para gerar números aleatórios

Para a simplicidade, vamos utilizar o pacote R que é instalado e carregado por padrão com a Base de `stats` Dados Azure SQL utilizando serviços de aprendizagem automática (pré-visualização). O pacote contém centenas de funções para tarefas estatísticas comuns, entre as quais a `rnorm` função. Esta função gera um número especificado de números aleatórios utilizando a distribuição normal, dado um desvio padrão e meios.

Por exemplo, o seguinte código R devolve 100 números numa média de 50, dado um desvio padrão de 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Para ligar para esta linha de R de T-SQL, corra `sp_execute_external_script` e adicione a função R no parâmetro do script R, assim:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

E se quiser tornar mais fácil gerar um conjunto diferente de números aleatórios?

É fácil quando combinado com SQL. Define um procedimento armazenado que obtém os argumentos do utilizador e, em seguida, passa esses argumentos para o script R como variáveis.

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

- A linha que começa com `@params` define todas as variáveis utilizadas pelo código R e os tipos de dados SQL correspondentes.

- As linhas que seguem imediatamente mapeiam os nomes dos parâmetros SQL para os nomes variáveis R correspondentes.

Agora que embrulhou a função R num procedimento armazenado, pode facilmente ligar para a função e passar em diferentes valores, como este:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Utilize funções de utilidade R para resolução de problemas

O `utils` pacote, instalado por padrão, fornece uma variedade de funções de utilidade para investigar o ambiente R atual. Estas funções podem ser úteis se encontrar discrepâncias na forma como o seu código R funciona em SQL e em ambientes externos. Por exemplo, pode utilizar a função R `memory.limit()` para obter memória para o ambiente R atual.

Como a `utils` embalagem está instalada mas não carregada por defeito, tem de utilizar a `library()` função para a carregar primeiro.

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
> Muitos utilizadores gostam de usar as funções de tempo do sistema em R, tais como `system.time` e , para capturar o tempo usado pelos `proc.time` processos R e analisar problemas de desempenho.
