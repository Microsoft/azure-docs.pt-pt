---
title: Alargar scripts U-SQL com Python no Azure Data Lake Analytics
description: Saiba como executar código Python em scripts U-SQL usando Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 0a49cbdb4caf474d0628fea3679ce712d37886e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60813413"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Alargar scripts U-SQL com código Python no Azure Data Lake Analytics

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que as extensões Python estão instaladas na sua conta Azure Data Lake Analytics.

* Navegue para você Data Lake Analytics Account no portal Azure
* No menu esquerdo, em START **INICIAR Clique** em **Scripts de Amostra**
* Clique **em instalar extensões U-SQL** e depois **OK**

## <a name="overview"></a>Descrição geral 

As extensões python para U-SQL permitem que os desenvolvedores realizem uma execução massivamente paralela do código Python. O exemplo que se segue ilustra os passos básicos:

* Use `REFERENCE ASSEMBLY` a declaração para ativar as extensões python para o Script U-SQL
* Utilização `REDUCE` da operação para dividir os dados de entrada numa chave
* As extensões Python para U-SQL incluem`Extension.Python.Reducer`um redutor incorporado que executa o código Python em cada vértice atribuído ao redutor
* O script U-SQL contém o código Python `usqlml_main` incorporado que tem uma função chamada que aceita um DataFrame pandas como entrada e devolve um DataFrame pandas como saída.

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

## <a name="how-python-integrates-with-u-sql"></a>Como python se integra com U-SQL

### <a name="datatypes"></a>Tipos de dados

* As colunas de cordas e numéricas da U-SQL são convertidas como está entre pandas e U-SQL
* Os Nulos U-SQL são convertidos de e para os valores dos Pandas `NA`

### <a name="schemas"></a>Esquemas

* Os vetores de índice em Pandas não são suportados em U-SQL. Todos os quadros de dados de entrada na função Python têm sempre um índice numérico de 64 bits de 0 ao número de linhas menos 1. 
* Conjuntos de dados U-SQL não podem ter nomes de colunas duplicados
* Os conjuntos de dados U-SQL nomes de colunas que não são cordas. 

### <a name="python-versions"></a>Versões Python
Apenas é suportado o Python 3.5.1 (compilado para Windows). 

### <a name="standard-python-modules"></a>Módulos Standard Python
Todos os módulos padrão python estão incluídos.

### <a name="additional-python-modules"></a>Módulos Python adicionais
Além das bibliotecas padrão python, várias bibliotecas de pitão comumente usadas estão incluídas:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Mensagens de Exceção
Atualmente, uma exceção no código Python aparece como falha genérica do vértice. No futuro, as mensagens de erro de trabalho U-SQL mostrarão a mensagem de exceção Python.

### <a name="input-and-output-size-limitations"></a>Limitações do tamanho de entrada e saída
Cada vértice tem uma quantidade limitada de memória atribuída a ele. Atualmente, esse limite é de 6 GB para uma UA. Dado que os DataFrames de entrada e de saída devem existir na memória no código Python, o tamanho total da entrada e saída não pode exceder 6 GB.

## <a name="see-also"></a>Consulte também
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio | Azure](data-lake-analytics-data-lake-tools-get-started.md)
* [Utilização de funções de janela U-SQL para trabalhos de Análise de Lago de Dados Azure](data-lake-analytics-use-window-functions.md)
* [Utilizar Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
