---
title: Quickstart - Crie uma piscina SQL Synapse (pré-visualização) usando o Estúdio Synapse
description: Crie uma nova piscina SQL Synapse utilizando o Estúdio Synapse seguindo os passos deste guia.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 3/19/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: da034479f110607e878a0ed26b9d487e41438e16
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83666289"
---
# <a name="quickstart-create-a-synapse-sql-pool-preview-using-synapse-studio"></a>Quickstart: Criar uma piscina SQL Synapse (pré-visualização) usando o Estúdio Synapse

A Azure Synapse Analytics oferece vários motores de análise para o ajudar a ingerir, transformar, modelar e analisar os seus dados. Um pool SQL oferece capacidades de computação e armazenamento baseadas em T-SQL. Depois de criar um pool SQL no seu espaço de trabalho Synapse, os dados podem ser carregados, modelados, processados e entregues para uma visão analítica mais rápida.

Este quickstart descreve os passos para criar uma piscina SQL em um espaço de trabalho Synapse usando o Estúdio Synapse.

Se não tiver uma subscrição Azure, [crie uma conta gratuita antes](https://azure.microsoft.com/free/)de começar .


## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Espaço de trabalho sinapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navegue para o espaço de trabalho synapse

1. Navegue para o espaço de trabalho Synapse onde a piscina SQL será criada digitando o nome do serviço (ou nome de recurso diretamente) na barra de pesquisa.
![Barra de pesquisa do portal Azure com espaços de trabalho Synapse digitados.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. A partir da lista de espaços de trabalho, digite o nome (ou parte do nome) do espaço de trabalho para abrir. Para este exemplo, usaremos um espaço de trabalho chamado **contosoanalytics.**
![Listagem de espaços de trabalho synapse filtrados para mostrar aqueles que contêm o nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Lançar Estúdio Synapse

1. A partir da visão geral do espaço de trabalho, selecione **Launch Synapse Studio** para abrir o local onde será criada a piscina SQL. Digite o nome do serviço ou o nome do recurso diretamente na barra de pesquisa.
![Visão geral do espaço de trabalho do portal Azure Synapse com o Launch Synapse Studio em destaque.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-sql-pool-in-synapse-studio"></a>Crie uma piscina SQL no Estúdio Synapse

1. Na página inicial do Estúdio Synapse, navegue para o Centro de **Gestão** na navegação à esquerda selecionando o ícone **Manage.**
![Página inicial do Estúdio Synapse com secção Management Hub em destaque.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Uma vez no Centro de Gestão, navegue até à secção de **piscinas SQL** para ver a lista atual de piscinas SQL que estão disponíveis no espaço de trabalho.
![Centro de gestão do Estúdio Synapse com navegação de piscinas SQL selecionado](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Selecione **+ Novo** comando e aparecerá o novo assistente de criação de piscina SQL. 
![Synapse Studio Management Hub listando piscinas SQL.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Introduza os seguintes detalhes no separador **Basics:**

    | Definição | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome da piscina SQL** | contosoedw | Este é o nome que a piscina SQL terá. |
    | **Nível de desempenho** | DW100c | Desloque-o ao menor tamanho para reduzir os custos para este arranque rápido |

    ![As piscinas SQL criam fluxo - separador básico.](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Note que existem limitações específicas para os nomes que as piscinas SQL podem usar. Os nomes não podem conter caracteres especiais, devem ter 15 ou menos caracteres, não conter palavras reservadas, e ser únicos no espaço de trabalho.

4. No separador seguinte, **Definições adicionais,** **selecione nenhuma** para fornecer o pool SQL sem dados. Deixe a colagem padrão selecionada.
![Piscina SQL cria fluxo - separador de configurações adicionais.](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Por enquanto, não adicionaremos nenhuma tag, por isso selecione **Next: Review + create**.

1. No separador **Review + criar,** certifique-se de que os detalhes parecem corretos com base no que foi previamente introduzido e pressione **a criação**. 
![Piscina SQL cria fluxo - separador de definições de revisão.](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. Neste momento, o fluxo de fornecimento de recursos começará.

1. Após o fornecimento completo, navegar de volta para o espaço de trabalho mostrará uma nova entrada para a recém-criada piscina SQL.
 ![A piscina SQL cria fluxo - fornecimento de recursos.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Uma vez criada a piscina SQL, estará disponível no espaço de trabalho para carregar dados, processamento de fluxos, leitura do lago, etc.

## <a name="clean-up-sql-pools-using-synapse-studio"></a>Limpe as piscinas SQL usando o Estúdio Synapse    

Siga os passos abaixo para eliminar a piscina SQL do espaço de trabalho utilizando o Estúdio Synapse.
> [!WARNING]
> A eliminação de uma piscina SQL removerá o motor de análise do espaço de trabalho. Deixará de ser possível ligar-se à piscina, e todas as consultas, oleodutos, scripts que usam esta piscina SQL deixarão de funcionar.

Se pretender eliminar a piscina SQL, faça o seguinte:

1. Navegue para as piscinas SQL no Centro de Gestão no Estúdio Synapse.
1. Selecione a elipse na piscina SQL a eliminar (neste caso, **contosoedw)** para mostrar os comandos para a piscina SQL: ![ Listagem de piscinas SQL, com a piscina recentemente criada selecionada.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Prima **apagar**.
1. Confirme a eliminação e prima **Apagar** o botão.
 ![Diálogo de confirmação para eliminar a piscina SQL selecionada.](media/quickstart-create-sql-pool/create-sql-pool-studio-29.png)
1. Quando o processo terminar com sucesso, o pool SQL deixará de ser listado nos recursos do espaço de trabalho.

## <a name="next-steps"></a>Próximos passos 
- Ver [Quickstart: Crie um caderno Apache Spark](quickstart-apache-spark-notebook.md).
- Ver [Quickstart: Criar uma piscina SQL synapse utilizando o portal Azure](quickstart-create-sql-pool-portal.md).