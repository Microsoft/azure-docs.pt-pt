---
title: Quickstart - Crie uma piscina Synapse SQL
description: Crie uma nova piscina SQL Synapse para um Espaço de Trabalho Synapse Synapse Azure Synapse, seguindo os passos deste guia.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a50f203b8057dd4ea51eff1cf09fc97fb598cb3e
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096440"
---
# <a name="quickstart-create-a-synapse-sql-pool"></a>Quickstart: Criar uma piscina SQL Synapse

A Azure Synapse Analytics oferece vários motores de análise para o ajudar a ingerir, transformar, modelar, analisar e servir os seus dados. Um pool SQL oferece capacidades de computação e armazenamento baseadas em T-SQL. Depois de criar um pool SQL no seu Espaço de Trabalho Synapse, os dados podem ser carregados, modelados, processados e servidos para obter insights.

Neste arranque rápido, você aprende a criar uma piscina SQL em um espaço de trabalho Synapse usando o portal Azure.

Se não tiver uma subscrição Azure, [crie uma conta gratuita antes](https://azure.microsoft.com/free/)de começar .

## <a name="prerequisites"></a>Pré-requisitos

[Espaço de trabalho sinapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal Azure](https://portal.azure.com/)

## <a name="create-new-sql-pool"></a>Criar uma nova piscina SQL

1. No espaço de trabalho Synapse onde pretende criar a piscina SQL, clique no comando da **piscina New SQL** na barra superior.
![Visão geral do Espaço de Trabalho Synapse com uma caixa vermelha em torno do comando para criar uma nova piscina SQL.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-01.png)
2. Introduza os seguintes detalhes no separador **Basics:**

    | Definição | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome da piscina SQL** | Qualquer nome válido | Nome da piscina SQL. |
    | **Nível de desempenho** | DW100c | Despleida para o menor tamanho para reduzir custos para este arranque rápido |
    ||||
  
    ![Piscina SQL cria fluxo - separador básico.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-02.png)
    > [!IMPORTANT]
    > Note que existem limitações específicas para os nomes que as piscinas SQL podem usar. Os nomes não podem conter caracteres especiais, devem ter 15 ou menos caracteres, não conter palavras reservadas, e ser únicos no espaço de trabalho.

3. Clique **em Seguinte: Definições adicionais**.
4. Selecione **Nenhum** para fornecer o pool SQL sem dados. Deixe a colagem padrão selecionada.
![Piscina SQL cria fluxo - separador de configurações adicionais.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-03.png)

5. Clique em **Rever + criar**.
6. Certifique-se de que os detalhes parecem corretos com base no que foi previamente introduzido e clique em **Criar**.
![Piscina SQL cria fluxo - separador de definições de revisão.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-04.png)

7. Neste momento, o fluxo de fornecimento de recursos começará.
 ![A piscina SQL cria fluxo - fornecimento de recursos.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-06.png)

8. Após o fornecimento completo, navegar de volta para o espaço de trabalho mostrará uma nova entrada para a recém-criada piscina SQL.
 ![A piscina SQL cria fluxo - fornecimento de recursos.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-07.png)

## <a name="clean-up-resources"></a>Limpar recursos

Siga os passos abaixo para eliminar a piscina SQL do espaço de trabalho.
> [!WARNING]
> A eliminação de um pool SQL removerá tanto o motor de análise como os dados armazenados na base de dados do pool SQL eliminado do espaço de trabalho. Deixará de ser possível ligar-se à piscina SQL, e todas as consultas, oleodutos e cadernos que lêem ou escrevem para esta piscina SQL deixarão de funcionar.

Se pretender eliminar a piscina SQL, complete os seguintes passos:

1. Navegue para a lâmina das piscinas SQL na lâmina do espaço de trabalho
1. Selecione o pool SQL a eliminar (neste caso, **contosoedw)**
1. Selecione-o e prima **para apagar**.
1. Confirme a eliminação e prima **Apagar** o botão.
 ![Visão geral do pool SQL - realçando a confirmação de eliminar.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-11.png)
1. Quando o processo terminar com sucesso, o pool SQL deixará de ser listado nos recursos do espaço de trabalho.

Após a criação da piscina SQL, estará disponível no espaço de trabalho para carregar dados, processamento de fluxos, leitura do lago, etc.

## <a name="next-steps"></a>Passos seguintes

- Ver [Quickstart: Crie uma piscina Apache Spark no Estúdio Synapse utilizando ferramentas web](quickstart-apache-spark-notebook.md).
- Ver [Quickstart: Crie uma piscina Apache Spark utilizando o portal Azure](quickstart-create-apache-spark-pool.md).
