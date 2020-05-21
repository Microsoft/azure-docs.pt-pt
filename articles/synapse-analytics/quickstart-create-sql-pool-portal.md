---
title: Quickstart - Criar uma piscina SQL Synapse (pré-visualização) utilizando o portal Azure
description: Crie uma nova piscina SQL Synapse utilizando o portal Azure seguindo os passos deste guia.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a040eb6db84c0e20be0f73a8e541ec824b2710c1
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83666397"
---
# <a name="quickstart-create-a-synapse-sql-pool-preview-using-the-azure-portal"></a>Quickstart: Criar uma piscina SQL Synapse (pré-visualização) utilizando o portal Azure

A Azure Synapse Analytics oferece vários motores de análise para o ajudar a ingerir, transformar, modelar e analisar os seus dados. Um pool SQL oferece capacidades de computação e armazenamento baseadas em T-SQL. Depois de criar um pool SQL no seu espaço de trabalho Synapse, os dados podem ser carregados, modelados, processados e entregues para uma visão analítica mais rápida.

Neste arranque rápido, você aprende a criar uma piscina SQL em um espaço de trabalho Synapse usando o portal Azure.

Se não tiver uma subscrição Azure, [crie uma conta gratuita antes](https://azure.microsoft.com/free/)de começar .

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Espaço de trabalho sinapse](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navegue para o espaço de trabalho synapse

1. Navegue para o espaço de trabalho Synapse onde a piscina SQL será criada digitando o nome do serviço (ou nome de recurso diretamente) na barra de pesquisa.
![Barra de pesquisa do portal Azure com espaços de trabalho Synapse digitados ](media/quickstart-create-sql-pool/create-sql-pool-00a.png) em. . 
1. A partir da lista de espaços de trabalho, digite o nome (ou parte do nome) do espaço de trabalho para abrir. Para este exemplo, usaremos um espaço de trabalho chamado **contosoanalytics.**
![Listagem de espaços de trabalho synapse filtrados para mostrar aqueles que contêm o nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-new-sql-pool"></a>Criar uma nova piscina SQL

1. No espaço de trabalho Synapse onde pretende criar a piscina SQL, selecione o comando de **piscina New SQL** no bar superior.
![Visão geral do espaço de trabalho synapse com uma caixa vermelha em torno do comando para criar uma nova piscina SQL.](media/quickstart-create-sql-pool/create-sql-pool-portal-01.png)
2. Introduza os seguintes detalhes no separador **Basics:**

    | Definição | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome da piscina SQL** | Qualquer nome válido | Nome da piscina SQL. |
    | **Nível de desempenho** | DW100c | Despleida para o menor tamanho para reduzir custos para este arranque rápido |

  
    ![Piscina SQL cria fluxo - separador básico.](media/quickstart-create-sql-pool/create-sql-pool-portal-02.png)
    > [!IMPORTANT]
    > Note que existem limitações específicas para os nomes que as piscinas SQL podem usar. Os nomes não podem conter caracteres especiais, devem ter 15 ou menos caracteres, não conter palavras reservadas, e ser únicos no espaço de trabalho.

3. Selecione **Seguinte: Configurações adicionais**.
4. Selecione **Nenhum** para fornecer o pool SQL sem dados. Deixe a colagem padrão selecionada.
![Piscina SQL cria fluxo - separador de configurações adicionais.](media/quickstart-create-sql-pool/create-sql-pool-portal-03.png)

5. Selecione **Rever + criar**.
6. Certifique-se de que os detalhes parecem corretos com base no que foi previamente introduzido. Selecione **Criar**.
![Piscina SQL cria fluxo - separador de definições de revisão.](media/quickstart-create-sql-pool/create-sql-pool-portal-04.png)

7. Neste momento, o fluxo de fornecimento de recursos começará.
 ![A piscina SQL cria fluxo - fornecimento de recursos.](media/quickstart-create-sql-pool/create-sql-pool-portal-06.png)

8. Após o fornecimento completo, navegar de volta para o espaço de trabalho mostrará uma nova entrada para a recém-criada piscina SQL.
 ![A piscina SQL cria fluxo - fornecimento de recursos.](media/quickstart-create-sql-pool/create-sql-pool-portal-07.png)


Após a criação da piscina SQL, estará disponível no espaço de trabalho para carregar dados, processamento de fluxos, leitura do lago, etc.

## <a name="clean-up-resources"></a>Limpar recursos

Siga os passos abaixo para eliminar a piscina SQL do espaço de trabalho.
> [!WARNING]
> A eliminação de um pool SQL removerá tanto o motor de análise como os dados armazenados na base de dados do pool SQL eliminado do espaço de trabalho. Deixará de ser possível ligar-se à piscina SQL, e todas as consultas, oleodutos e cadernos que lêem ou escrevem para esta piscina SQL deixarão de funcionar.

Se pretender eliminar a piscina SQL, complete os seguintes passos:

1. Navegue para a lâmina das piscinas SQL na lâmina do espaço de trabalho
1. Selecione a piscina SQL a eliminar (neste caso, **contosowdw)**
1. Uma vez selecionado, prima **eliminar**
1. Confirme a eliminação e prima **Eliminar** a visão geral do conjunto SQL do botão ![ - realçando a confirmação de eliminar.](media/quickstart-create-sql-pool/create-sql-pool-portal-11.png)
1. Quando o processo terminar com sucesso, o pool SQL deixará de ser listado nos recursos do espaço de trabalho.

## <a name="next-steps"></a>Próximos passos

- Ver [Quickstart: Crie uma piscina Apache Spark no Estúdio Synapse utilizando ferramentas web](quickstart-apache-spark-notebook.md).
- Ver [Quickstart: Crie uma piscina Apache Spark utilizando o portal Azure](quickstart-create-apache-spark-pool-portal.md).
