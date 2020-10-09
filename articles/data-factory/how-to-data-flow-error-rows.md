---
title: Lidar com linhas de erro com fluxos de dados de mapeamento na Azure Data Factory
description: Aprenda a lidar com erros de truncação SQL na Azure Data Factory utilizando fluxos de dados de mapeamento.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: makromer
ms.openlocfilehash: 3f8ac2d1434019548b01d8468015a543d89d0fba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85254417"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Lidar com linhas de erro de truncação SQL nos fluxos de dados de mapeamento da Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Um cenário comum na Data Factory ao utilizar fluxos de dados de mapeamento, é escrever os seus dados transformados numa base de dados na Base de Dados Azure SQL. Neste cenário, uma condição de erro comum contra a qual deve prevenir é possível a truncação da coluna. Siga estes passos para fornecer registo de colunas que não se encaixam numa coluna de cordas-alvo, permitindo que o fluxo de dados continue nesses cenários.

## <a name="scenario"></a>Cenário

1. Temos uma tabela de base de dados-alvo que tem uma ```nvarchar(5)``` coluna chamada "nome".

2. Dentro do nosso fluxo de dados, queremos mapear títulos de filmes da nossa pia para a coluna de "nome" alvo.

    ![Fluxo de dados de filme 1](media/data-flow/error4.png)
    
3. O problema é que o título do filme não cabe todos dentro de uma coluna de pia que só pode ter 5 caracteres. Quando executar este fluxo de dados, receberá um erro como este: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

Este vídeo percorre um exemplo de lógica de manipulação de linha de erro de configuração no fluxo de dados:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Como projetar em torno desta condição

1. Neste cenário, o comprimento máximo da coluna "nome" é de cinco caracteres. Assim, vamos adicionar uma transformação de divisão condicional que nos permitirá registar linhas com "títulos" que são mais de cinco caracteres, ao mesmo tempo que permite que o resto das linhas que podem caber nesse espaço para escrever na base de dados.

    ![divisão condicional](media/data-flow/error1.png)

2. Esta transformação de divisão condicional define o comprimento máximo de "título" para cinco. Qualquer linha que seja inferior ou igual a cinco irá para o ```GoodRows``` fluxo. Qualquer linha maior que cinco irá para o ```BadRows``` riacho.

3. Agora temos de registar as filas que falharam. Adicione uma transformação de pia ao ```BadRows``` fluxo para a exploração madeireira. Aqui, vamos "mapear" todos os campos para que tenhamos registo do registo completo de transações. Esta é uma saída de ficheiro CSV delimitada por texto para um único ficheiro no Blob Storage. Vamos chamar o ficheiro de registo de "badrows.csv".

    ![Más filas](media/data-flow/error3.png)
    
4. O fluxo de dados completo é mostrado abaixo. Agora somos capazes de separar linhas de erro para evitar os erros de truncação SQL e colocar essas entradas num ficheiro de registo. Entretanto, as filas bem sucedidas podem continuar a escrever para a nossa base de dados alvo.

    ![fluxo completo de dados](media/data-flow/error2.png)

## <a name="next-steps"></a>Passos seguintes

* Construa o resto da sua lógica de fluxo de dados utilizando [transformações](concepts-data-flow-overview.md)de fluxos de dados de mapeamento.
