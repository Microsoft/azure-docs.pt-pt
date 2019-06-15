---
title: Expandir scripts U-SQL com o Python no Azure Data Lake Analytics
description: Saiba como executar o código de Python em scripts U-SQL com o Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 0a49cbdb4caf474d0628fea3679ce712d37886e7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813413"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Expandir scripts U-SQL com o código de Python no Azure Data Lake Analytics

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que as extensões de Python são instaladas na sua conta do Azure Data Lake Analytics.

* Navegue para o que de uma conta do Data Lake Analytics no portal do Azure
* No menu à esquerda, em **introdução** clique em **Scripts de exemplo**
* Clique em **instalar as extensões U-SQL** , em seguida, **OK**

## <a name="overview"></a>Descrição geral 

Extensões de Python para U-SQL permitem aos programadores efetuar execução paralela em massa de código Python. O exemplo seguinte ilustra as etapas básicas:

* Utilize o `REFERENCE ASSEMBLY` instrução para ativar extensões Python para o Script de U-SQL
* Usando o `REDUCE` operação para os dados de entrada numa chave de partição
* As extensões de Python para U-SQL incluem um reducer incorporada (`Extension.Python.Reducer`) que executa o código de Python em cada vértice atribuído para o reducer
* O script de U-SQL contém o código de Python incorporado que tem uma função chamada `usqlml_main` que aceita um pandas DataFrame como entrada e retorna um pandas DataFrame como saída.

--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-python-integrates-with-u-sql"></a>Como o Python se integra com o U-SQL

### <a name="datatypes"></a>Tipos de dados

* Colunas de cadeia de caracteres e numéricos de U-SQL são convertidas como-entre Pandas e U-SQL
* U-SQL Nulls são convertidos de e para Pandas `NA` valores

### <a name="schemas"></a>Esquemas

* Índice de vetores em Pandas não são suportadas em U-SQL. Todos os quadros de dados de entrada na função Python sempre tem um índice numérico de 64 bits de 0 ao número de linhas menos 1. 
* Conjuntos de dados U-SQL não podem ter nomes de colunas duplicados
* U-SQL conjuntos de dados nomes de colunas que não são cadeias de caracteres. 

### <a name="python-versions"></a>Versões de Python
Apenas Python 3.5.1 (compilada para o Windows) é suportada. 

### <a name="standard-python-modules"></a>Módulos de Python padrão
Todos os módulos de Python padrão são incluídos.

### <a name="additional-python-modules"></a>Módulos de Python adicionais
Além de bibliotecas de Python padrão, várias bibliotecas python mais usados são incluídas:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Mensagens de exceção
Atualmente, uma exceção no código do Python é exibido como falha de vértice genérico. No futuro, as mensagens de erro da tarefa de U-SQL irão apresentar a mensagem de exceção de Python.

### <a name="input-and-output-size-limitations"></a>Entrada e limitações de tamanho de saída
Cada vértice tem uma quantidade limitada de memória atribuída à mesma. Atualmente, esse limite é de 6 GB para UA. Uma vez que os pacotes de entrada e saída têm de existir na memória no código de Python, o tamanho total de entrada e de saída não pode exceder 6 GB.

## <a name="see-also"></a>Consulte também
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Usando as funções de janela de U-SQL para tarefas de Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
* [Utilizar do Azure Data Lake Tools para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
