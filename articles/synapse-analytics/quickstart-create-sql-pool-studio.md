---
title: 'Quickstart: Criar uma piscina Sinapse SQL usando o Synapse Studio'
description: Crie uma nova piscina Synapse SQL utilizando o Synapse Studio seguindo os passos deste guia.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 10/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 806831ac5e965afcd076066f4baa498297a43a3e
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740528"
---
# <a name="quickstart-create-a-synapse-sql-pool-using-synapse-studio"></a>Quickstart: Criar uma piscina Sinapse SQL usando o Synapse Studio

O Azure Synapse Analytics oferece vários motores de análise para ajudá-lo a ingerir, transformar, modelar e analisar os seus dados. Uma piscina SQL oferece capacidades de computação e armazenamento baseadas em T-SQL. Depois de criar uma piscina SQL no seu espaço de trabalho Synapse, os dados podem ser carregados, modelados, processados e entregues para uma visão analítica mais rápida.

Este quickstart descreve os passos para criar uma piscina SQL num espaço de trabalho synapse usando o Synapse Studio.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Espaço de trabalho sinapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navegue até ao espaço de trabalho da Sinapse

1. Navegue até ao espaço de trabalho synapse onde a piscina SQL será criada digitando o nome de serviço (ou nome de recurso diretamente) na barra de pesquisa.

    ![Barra de pesquisa de portal Azure com espaços de trabalho synapse digitado.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Da lista de espaços de trabalho, digite o nome (ou parte do nome) do espaço de trabalho para abrir. Para este exemplo, usaremos um espaço de trabalho chamado **contosoanalytics.**

    ![Listagem de espaços de trabalho do Synapse filtrados para mostrar aqueles que contêm o nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Iniciar o Synapse Studio

1. A partir da visão geral do espaço de trabalho, selecione **Launch Synapse Studio** para abrir o local onde será criada a piscina SQL. Digite o nome de serviço ou nome do recurso diretamente na barra de pesquisa.

    ![A visão geral do espaço de trabalho do portal Azure Synapse com Launch Synapse Studio em destaque.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-sql-pool-in-synapse-studio"></a>Criar uma piscina SQL no Estúdio Synapse

1. Na página inicial do Synapse Studio, navegue para o **Centro de Gestão** na navegação à esquerda selecionando o ícone **Manage.**

    ![Página inicial do Synapse Studio com secção Management Hub em destaque.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Uma vez no Centro de Gestão, navegue até à secção de **piscinas SQL** para ver a lista atual de piscinas SQL que estão disponíveis no espaço de trabalho.

    ![Centro de gestão do Synapse Studio com navegação de piscinas SQL selecionada](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Selecione **+ Novo** comando e aparecerá o novo assistente de criação de pool SQL. 

    ![Listagem do Synapse Studio Management Hub de piscinas SQL.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Introduza os seguintes detalhes no separador **Básicos:**

    | Definição | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome da piscina SQL** | contosoedw | Este é o nome que a piscina SQL terá. |
    | **Nível de desempenho** | DW100c | Deslote isto para o menor tamanho para reduzir os custos para este arranque rápido |

    ![Piscinas SQL criam fluxo - separador básico.](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Note que existem limitações específicas para os nomes que as piscinas SQL podem usar. Os nomes não podem conter caracteres especiais, devem ter 15 ou menos caracteres, não conter palavras reservadas e ser únicos no espaço de trabalho.

4. No separador seguinte, **definições adicionais,** selecione **nenhuma** para a provisionar o pool SQL sem dados. Deixe a colagem predefinida como selecionado.

    ![Piscina SQL cria fluxo - separador de configurações adicionais.](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Não adicionaremos nenhuma etiqueta por enquanto, por isso, a próxima seleção **Review + create** .

1. No **separador Review + create,** certifique-se de que os detalhes ficam corretos com base no que foi previamente introduzido, e **pressione criar** . 

    ![Piscina SQL cria fluxo - separador de definições de revisão.](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. Neste momento, o fluxo de fornecimento de recursos começará.

1. Após o a provisionamento concluído, navegar de volta para o espaço de trabalho mostrará uma nova entrada para a recém-criada piscina SQL.

    ![Piscina SQL cria fluxo - fornecimento de recursos.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Uma vez criada a piscina SQL, estará disponível no espaço de trabalho para carregar dados, fluxos de processamento, leitura do lago, etc.

## <a name="clean-up-sql-pools-using-synapse-studio"></a>Limpe as piscinas SQL usando o Synapse Studio    

Siga os passos abaixo para eliminar a piscina SQL do espaço de trabalho utilizando o Synapse Studio.
> [!WARNING]
> A eliminação de uma piscina SQL removerá o motor de análise do espaço de trabalho. Não será mais possível ligar-se à piscina, e todas as consultas, oleodutos, scripts que usam esta piscina SQL deixarão de funcionar.

Se pretender apagar a piscina SQL, faça o seguinte:

1. Navegue para as piscinas SQL no Centro de Gestão no Estúdio Synapse.
1. Selecione a elipse na piscina SQL a eliminar (neste caso, **contosoedw)** para mostrar os comandos da piscina SQL:

    ![Listagem de piscinas SQL, com a piscina recentemente criada selecionada.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Prima **para apagar** .
1. Confirme a eliminação e prima O botão **Eliminar.**
1. Quando o processo estiver concluído com sucesso, a piscina SQL deixará de estar listada nos recursos do espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes 
- Ver [Quickstart: Criar um caderno Apache Spark](quickstart-apache-spark-notebook.md).
- Consulte [Quickstart: Crie uma piscina Sinapse SQL utilizando o portal Azure](quickstart-create-sql-pool-portal.md).
