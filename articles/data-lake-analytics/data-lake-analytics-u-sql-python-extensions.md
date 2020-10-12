---
title: Estender scripts U-SQL com Python em Azure Data Lake Analytics
description: Saiba como executar o código Python em scripts U-SQL usando Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/20/2017
ms.custom: devx-track-python
ms.openlocfilehash: b15ab268433e4220d499f3e1fe7cb90ffac2a1be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87876022"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Estender scripts U-SQL com código Python em Azure Data Lake Analytics

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que as extensões Python estão instaladas na sua conta Azure Data Lake Analytics.

* Navegue para si Conta de Análise de Lagos de Dados no portal Azure
* No menu esquerdo, em **"COMEÇAR"** clique em **Scripts de Amostra**
* Clique **em instalar extensões U-SQL** e depois **OK**

## <a name="overview"></a>Descrição geral

As extensões python para U-SQL permitem aos desenvolvedores executar uma execução massivamente paralela do código Python. O exemplo a seguir ilustra os passos básicos:

* Utilize a `REFERENCE ASSEMBLY` declaração para permitir extensões Python para o Script U-SQL
* Utilizando a `REDUCE` operação para dividir os dados de entrada numa chave
* As extensões Python para U-SQL incluem um redutor incorporado `Extension.Python.Reducer` () que executa o código Python em cada vértice atribuído ao redutor
* O script U-SQL contém o código Python incorporado que tem uma função chamada `usqlml_main` que aceita um DataFrame pandas como entrada e devolve um DataFrame de pandas como saída.

```usql
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
       ) AS date, time, author, tweet );
@m  =
    REDUCE @t ON date
    PRODUCE date string, mentions string
    USING new Extension.Python.Reducer(pyScript:@myScript);
OUTPUT @m
    TO "/tweetmentions.csv"
    USING Outputters.Csv();
```

## <a name="how-python-integrates-with-u-sql"></a>Como python se integra com U-SQL

### <a name="datatypes"></a>Tipos de dados

* As colunas de cordas e numéricas da U-SQL são convertidas como-é entre Pandas e U-SQL
* Os nulos U-SQL são convertidos de e para os valores dos Pandas `NA`

### <a name="schemas"></a>Esquemas

* Os vetores de índice em Pandas não são suportados em U-SQL. Todos os quadros de dados de entrada na função Python têm sempre um índice numérico de 64 bits de 0 até ao número de linhas menos 1.
* Conjuntos de dados U-SQL não podem ter nomes de colunas duplicados
* U-SQL datasets nomes de colunas que não são cordas.

### <a name="python-versions"></a>Versões Python

Apenas o Python 3.5.1 (compilado para Windows) é suportado.

### <a name="standard-python-modules"></a>Módulos Python padrão

Todos os módulos Python padrão estão incluídos.

### <a name="additional-python-modules"></a>Módulos Python adicionais

Além das bibliotecas pitão padrão, várias bibliotecas de python comumente usadas estão incluídas:

* pandas
* numpy
* numexpr

### <a name="exception-messages"></a>Mensagens de Exceção

Atualmente, uma exceção no código Python aparece como falha genérica do vértice. No futuro, as mensagens de erro U-SQL Job mostrarão a mensagem de exceção Python.

### <a name="input-and-output-size-limitations"></a>Limitações do tamanho da entrada e da saída

Cada vértice tem uma quantidade limitada de memória atribuída a ele. Atualmente, esse limite é de 6 GB para uma AU. Como os DataFrames de entrada e saída devem existir na memória no código Python, o tamanho total da entrada e saída não pode exceder 6 GB.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio | Azure](data-lake-analytics-data-lake-tools-get-started.md)
* [Utilização de funções de janela U-SQL para trabalhos de Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
* [Utilizar Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
