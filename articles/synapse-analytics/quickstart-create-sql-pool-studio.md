---
title: 'Quickstart: Criar uma piscina SQL dedicada dedicada usando o Synapse Studio'
description: Crie uma piscina SQL dedicada utilizando o Synapse Studio seguindo os passos deste guia.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 10/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 6ffa7bb07ddb42946acdcef6ee3b7f2aa804f774
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324321"
---
# <a name="quickstart-create-a-dedicated-sql-pool-using-synapse-studio"></a>Quickstart: Criar uma piscina SQL dedicada usando o Synapse Studio

O Azure Synapse Analytics oferece vários motores de análise para ajudá-lo a ingerir, transformar, modelar e analisar os seus dados. Um pool SQL dedicado oferece capacidades de computação e armazenamento baseadas em T-SQL. Depois de criar uma piscina DE SQL dedicada no seu espaço de trabalho Synapse, os dados podem ser carregados, modelados, processados e entregues para uma visão analítica mais rápida.

Este quickstart descreve os passos para criar uma piscina SQL dedicada num espaço de trabalho synapse usando o Synapse Studio.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Espaço de trabalho sinapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navegue até ao espaço de trabalho da Sinapse

1. Navegue até ao espaço de trabalho synapse onde a piscina dedicada SQL será criada digitando o nome de serviço (ou nome de recurso diretamente) na barra de pesquisa.

    ![Barra de pesquisa de portal Azure com espaços de trabalho synapse digitado.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Da lista de espaços de trabalho, digite o nome (ou parte do nome) do espaço de trabalho para abrir. Para este exemplo, usaremos um espaço de trabalho chamado **contosoanalytics.**

    ![Listagem de espaços de trabalho do Synapse filtrados para mostrar aqueles que contêm o nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Iniciar o Synapse Studio

1. A partir da visão geral do espaço de trabalho, selecione o **URL web workspace** para lançar o Synapse Studio.

    ![A visão geral do espaço de trabalho do portal Azure Synapse com URL web workspace em destaque.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-dedicated-sql-pool-in-synapse-studio"></a>Crie uma piscina SQL dedicada no Synapse Studio

1. Na página inicial do Synapse Studio, navegue para o **Centro de Gestão** na navegação à esquerda selecionando o ícone **Manage.**

    ![Página inicial do Synapse Studio com secção Management Hub em destaque.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Uma vez no Centro de Gestão, navegue até à secção de **piscinas SQL** para ver a lista atual de piscinas SQL que estão disponíveis no espaço de trabalho.

    ![Centro de gestão do Synapse Studio com navegação de piscinas SQL selecionada](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Selecione **+ Novo** comando e aparecerá o novo assistente de criação de pool SQL. 

    ![Listagem do Synapse Studio Management Hub de piscinas SQL.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Introduza os seguintes detalhes no separador **Básicos:**

    | Definição | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome da piscina SQL** | contosoedw | Este é o nome que a piscina dedicada SQL terá. |
    | **Nível de desempenho** | DW100c | Deslote isto para o menor tamanho para reduzir os custos para este arranque rápido |

    ![Piscinas SQL criam fluxo - separador básico.](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Note que existem limitações específicas para os nomes que piscinas SQL dedicadas podem usar. Os nomes não podem conter caracteres especiais, devem ter 15 ou menos caracteres, não conter palavras reservadas e ser únicos no espaço de trabalho.

4. No separador seguinte, **definições adicionais,** selecione **nenhuma** para a provisionar o pool SQL sem dados. Deixe a colagem predefinida como selecionado.

    ![Piscina SQL cria fluxo - separador de configurações adicionais.](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Não adicionaremos nenhuma etiqueta por enquanto, por isso, a próxima seleção **Review + create**.

1. No **separador Review + create,** certifique-se de que os detalhes ficam corretos com base no que foi previamente introduzido, e **pressione criar**. 

    ![Piscina SQL cria fluxo - separador de definições de revisão.](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. Neste momento, o fluxo de fornecimento de recursos começará.

1. Após o a provisionamento concluído, navegar de volta para o espaço de trabalho mostrará uma nova entrada para a recém-criada piscina SQL.

    ![Piscina SQL cria fluxo - fornecimento de recursos.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Assim que a piscina de SQL dedicada for criada, estará disponível no espaço de trabalho para carregamento de dados, fluxos de processamento, leitura do lago, etc.

## <a name="clean-up-dedicated-sql-pool-using-synapse-studio"></a>Limpe a piscina SQL dedicada usando o Synapse Studio    

Siga os passos abaixo para eliminar a piscina SQL dedicada do espaço de trabalho utilizando o Synapse Studio.
> [!WARNING]
> A eliminação de uma piscina SQL dedicada removerá o motor de análise do espaço de trabalho. Não será mais possível ligar-se à piscina, e todas as consultas, oleodutos, scripts que utilizam esta piscina dedicada SQL deixarão de funcionar.

Se pretender eliminar a piscina SQL dedicada, faça o seguinte:

1. Navegue para as piscinas SQL no Centro de Gestão no Estúdio Synapse.
1. Selecione a elipse na piscina dedicada SQL a eliminar (neste caso, **contosoedw** ) para mostrar os comandos da piscina SQL dedicada:

    ![Listagem de piscinas SQL, com a piscina recentemente criada selecionada.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Prima **para apagar**.
1. Confirme a eliminação e prima O botão **Eliminar.**
1. Quando o processo estiver concluído com sucesso, a piscina dedicada SQL deixará de estar listada nos recursos do espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes 
- Ver [Quickstart: Criar um caderno Apache Spark](quickstart-apache-spark-notebook.md).
- Consulte [Quickstart: Crie uma piscina SQL dedicada utilizando o portal Azure](quickstart-create-sql-pool-portal.md).
