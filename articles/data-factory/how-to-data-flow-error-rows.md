---
title: Manuseie linhas de erro com fluxos de dados de mapeamento na Fábrica de Dados Azure
description: Aprenda a lidar com erros de truncação SQL na Azure Data Factory utilizando fluxos de dados de mapeamento.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: makromer
ms.openlocfilehash: 8225143bb75118620b45c2520bb62ea30501a617
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732692"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Manuseie as linhas de erro de truncação SQL nos fluxos de dados de mapeamento da Fábrica de Dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Um cenário comum na Data Factory ao utilizar fluxos de dados de mapeamento, é escrever os seus dados transformados numa base de dados Azure SQL. Neste cenário, uma condição de erro comum que deve prevenir é possível truncação de coluna. Siga estes passos para fornecer o registo de colunas que não se encaixam numa coluna de cordas-alvo, permitindo que o fluxo de dados continue nesses cenários.

## <a name="scenario"></a>Cenário

1. Temos uma tabela de dados Azure SQL alvo que tem uma ```nvarchar(5)``` coluna chamada "nome".

2. Dentro do nosso fluxo de dados, queremos mapear títulos de filmes da nossa pia para aquela coluna de "nome" alvo.

    ![Fluxo de dados do filme 1](media/data-flow/error4.png)
    
3. O problema é que o título do filme não caberá todos dentro de uma coluna de pia que só pode conter 5 caracteres. Quando executar este fluxo de dados, receberá um erro como este:```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

Este vídeo percorre um exemplo de configuração da lógica de manipulação de linhas de erro no fluxo de dados:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Como projetar em torno desta condição

1. Neste cenário, o comprimento máximo da coluna "nome" é de cinco caracteres. Assim, vamos adicionar uma transformação de divisão condicional que nos permitirá registar linhas com "títulos" que são mais de cinco caracteres, permitindo ao mesmo tempo o resto das linhas que podem caber nesse espaço para escrever para a base de dados.

    ![divisão condicional](media/data-flow/error1.png)

2. Esta transformação de divisão condicional define o comprimento máximo do "título" para cinco. Qualquer linha que seja inferior ou igual ```GoodRows``` a cinco irá para o riacho. Qualquer linha maior que cinco irá ```BadRows``` para o riacho.

3. Agora temos de registar as linhas que falharam. Adicione uma transformação ```BadRows``` de pia ao fluxo para a exploração madeireira. Aqui, vamos "mapear automaticamente" todos os campos para que tenhamos registo do registo completo de transações. Esta é uma saída de ficheiro CSV delimitada por texto para um único ficheiro no Blob Storage. Vamos chamar o ficheiro de registo de "badrows.csv".

    ![Filas más](media/data-flow/error3.png)
    
4. O fluxo de dados concluído é mostrado abaixo. Agora somos capazes de dividir as linhas de erro para evitar os erros de truncação SQL e colocar essas entradas num ficheiro de registo. Entretanto, linhas bem sucedidas podem continuar a escrever para a nossa base de dados de alvos.

    ![fluxo completo de dados](media/data-flow/error2.png)

## <a name="next-steps"></a>Passos seguintes

* Construa o resto da sua lógica de fluxo de dados utilizando transformações de [fluxos](concepts-data-flow-overview.md)de dados de mapeamento.
