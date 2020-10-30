---
title: 'Quickstart: Criar uma piscina SQL Synapse (pré-visualização) utilizando o portal Azure'
description: Crie uma nova piscina Synapse SQL utilizando o portal Azure seguindo os passos deste guia.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 6c9d49d8d4d1951542268d35e22822ab62449278
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041228"
---
# <a name="quickstart-create-a-synapse-sql-pool-preview-using-the-azure-portal"></a>Quickstart: Criar uma piscina SQL Synapse (pré-visualização) utilizando o portal Azure

O Azure Synapse Analytics oferece vários motores de análise para ajudá-lo a ingerir, transformar, modelar e analisar os seus dados. Uma piscina SQL oferece capacidades de computação e armazenamento baseadas em T-SQL. Depois de criar uma piscina SQL no seu espaço de trabalho Synapse, os dados podem ser carregados, modelados, processados e entregues para uma visão analítica mais rápida.

Neste arranque rápido, você aprende a criar uma piscina SQL em um espaço de trabalho Synapse usando o portal Azure.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Espaço de trabalho sinapse](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navegue até ao espaço de trabalho da Sinapse

1. Navegue até ao espaço de trabalho synapse onde a piscina SQL será criada digitando o nome de serviço (ou nome de recurso diretamente) na barra de pesquisa.
![Barra de pesquisa de portal Azure com espaços de trabalho synapse dactilografado em. ](media/quickstart-create-sql-pool/create-sql-pool-00a.png) . 
1. Da lista de espaços de trabalho, digite o nome (ou parte do nome) do espaço de trabalho para abrir. Para este exemplo, usaremos um espaço de trabalho chamado **contosoanalytics.**
![Listagem de espaços de trabalho do Synapse filtrados para mostrar aqueles que contêm o nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-new-sql-pool"></a>Criar uma nova piscina SQL

1. No espaço de trabalho synapse onde pretende criar a piscina SQL, selecione Novo comando **de piscina SQL** na barra superior.
![Visão geral do espaço de trabalho synapse com uma caixa vermelha em torno do comando para criar uma nova piscina SQL.](media/quickstart-create-sql-pool/create-sql-pool-portal-01.png)
2. Introduza os seguintes detalhes no separador **Básicos:**

    | Definição | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome da piscina SQL** | Qualquer nome válido | Nome da piscina SQL. |
    | **Nível de desempenho** | DW100c | Definir para o menor tamanho para reduzir custos para este arranque rápido |

  
    ![Piscina SQL cria fluxo - separador básico.](media/quickstart-create-sql-pool/create-sql-pool-portal-02.png)
    > [!IMPORTANT]
    > Note que existem limitações específicas para os nomes que as piscinas SQL podem usar. Os nomes não podem conter caracteres especiais, devem ter 15 ou menos caracteres, não conter palavras reservadas e ser únicos no espaço de trabalho.

3. Selecione **Seguinte: Definições adicionais** .
4. Selecione **Nenhum** para providenciar o pool SQL sem dados. Deixe a colagem predefinida selecionada.
![Piscina SQL cria fluxo - separador de configurações adicionais.](media/quickstart-create-sql-pool/create-sql-pool-portal-03.png)

5. Selecione **Rever + criar** .
6. Certifique-se de que os detalhes parecem corretos com base no que foi previamente introduzido. Selecione **Criar** .
![Piscina SQL cria fluxo - separador de definições de revisão.](media/quickstart-create-sql-pool/create-sql-pool-portal-04.png)

7. Neste momento, o fluxo de fornecimento de recursos começará.
 ![Screenshot que mostra a página "A sua implementação está completa".](media/quickstart-create-sql-pool/create-sql-pool-portal-06.png)

8. Após o a provisionamento concluído, navegar de volta para o espaço de trabalho mostrará uma nova entrada para a recém-criada piscina SQL.
 ![Piscina SQL cria fluxo - fornecimento de recursos.](media/quickstart-create-sql-pool/create-sql-pool-portal-07.png)


Após a criação da piscina SQL, estará disponível no espaço de trabalho para carregamento de dados, fluxos de processamento, leitura do lago, etc.

## <a name="clean-up-resources"></a>Limpar recursos

Siga os passos abaixo para eliminar a piscina SQL do espaço de trabalho.
> [!WARNING]
> A eliminação de uma piscina SQL removerá o motor de análise e os dados armazenados na base de dados do pool SQL eliminado do espaço de trabalho. Deixará de ser possível ligar-se à piscina SQL, e todas as consultas, oleodutos e cadernos que leiam ou escrevem para esta piscina SQL deixarão de funcionar.

Se pretender eliminar a piscina SQL, complete os seguintes passos:

1. Navegue até à lâmina das piscinas SQL na lâmina do espaço de trabalho
1. Selecione a piscina SQL a ser eliminada (neste caso, **contosowdw)**
1. Uma vez selecionado, prima **eliminar**
1. Confirme a eliminação e prima **Eliminar** botão ![ SQL pool visão geral - realçando a confirmação de eliminar.](media/quickstart-create-sql-pool/create-sql-pool-portal-11.png)
1. Quando o processo estiver concluído com sucesso, a piscina SQL deixará de estar listada nos recursos do espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

- Consulte [Quickstart: Crie uma piscina Apache Spark no Estúdio Synapse utilizando ferramentas web](quickstart-apache-spark-notebook.md).
- Consulte [Quickstart: Crie uma piscina Apache Spark utilizando o portal Azure](quickstart-create-apache-spark-pool-portal.md).
