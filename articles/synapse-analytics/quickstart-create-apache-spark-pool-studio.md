---
title: 'Quickstart: Criar uma piscina Apache Spark sem servidor usando o Synapse Studio'
description: Crie uma piscina Apache Spark sem servidor utilizando o Synapse Studio seguindo os passos deste guia.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 10/16/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: d280e96b354f38a86545ed5f6bcaa6c9a314c46b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462033"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-using-synapse-studio"></a>Quickstart: Criar uma piscina Apache Spark sem servidor usando o Synapse Studio

O Azure Synapse Analytics oferece vários motores de análise para ajudá-lo a ingerir, transformar, modelar, analisar e servir os seus dados. O conjunto Apache Spark oferece capacidades de computação de dados de código aberto. Depois de criar uma piscina Apache Spark no seu espaço de trabalho Synapse, os dados podem ser carregados, modelados, processados e servidos para obter insights.  

Este quickstart descreve os passos para criar uma piscina Apache Spark num espaço de trabalho da Synapse usando o Synapse Studio.

> [!IMPORTANT]
> A faturação de casos de Spark é prostimada por minuto, quer esteja a usá-las ou não. Certifique-se de que desliga a sua instância Spark depois de ter terminado de a utilizar ou de definir um curto período de tempo. Para obter mais informações, consulte a secção **Limpar recursos** deste artigo.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Espaço de trabalho sinapse](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navegue até ao espaço de trabalho da Sinapse

1. Navegue até ao espaço de trabalho da Sinaapse, onde a piscina Apache Spark será criada digitando o nome de serviço (ou nome do recurso diretamente) na barra de pesquisa.

    ![Barra de pesquisa de portal Azure com espaços de trabalho synapse digitado.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Da lista de espaços de trabalho, digite o nome (ou parte do nome) do espaço de trabalho para abrir. Para este exemplo, usaremos um espaço de trabalho chamado **contosoanalytics.**

    ![Listagem de espaços de trabalho do Synapse filtrados para mostrar aqueles que contêm o nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Iniciar o Synapse Studio 

A partir da visão geral do espaço de trabalho, selecione o **URL web workspace** para abrir o Synapse Studio.

![A visão geral do espaço de trabalho do portal Azure Synapse com Launch Synapse Studio em destaque.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-the-apache-spark-pool-in-synapse-studio"></a>Crie a piscina Apache Spark no Estúdio Synapse

1. Na página inicial do Synapse Studio, navegue para o **Centro de Gestão** na navegação à esquerda selecionando o ícone **Manage.**

    ![Página inicial do Synapse Studio com secção Management Hub em destaque.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Uma vez no Centro de Gestão, navegue até à secção de **piscinas Apache Spark** para ver a lista atual de piscinas Apache Spark que estão disponíveis no espaço de trabalho.
    
    ![Centro de gestão do Estúdio Synapse com navegação de piscinas Apache Spark selecionada](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-22.png)

1. Selecione **+ Novo** e aparecerá o novo assistente de criação de faíscas Apache Spark. 

1. Introduza os seguintes detalhes no separador **Básicos:**

    | Definição | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome da piscina Apache Spark** | contosospark | Este é o nome que a piscina Apache Spark terá. |
    | **Tamanho do nó** | Pequeno (4 vCPU / 32 GB) | Deslote isto para o menor tamanho para reduzir os custos para este arranque rápido |
    | **Dimensionamento Automático** | Desativado | Não vamos precisar de autoescala neste arranque rápido |
    | **Número de nós** | 8 | Use um pequeno tamanho para limitar os custos neste arranque rápido|
       
    ![Básicos para o Synapse Studio nova piscina Apache Spark](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-24.png)
    
    > [!IMPORTANT]
    > Note que existem limitações específicas para os nomes que as piscinas Apache Spark podem usar. Os nomes devem conter apenas letras ou números, devem ter 15 ou menos caracteres, devem começar com uma letra, não conter palavras reservadas, e ser únicos no espaço de trabalho.

1. No separador seguinte (Definições adicionais), deixe todas as definições como padrão.

1. Não adicionaremos nenhuma etiqueta por enquanto, por isso selecione **Review + create**.

1. No **separador 'Rever +' criar,** certifique-se de que os detalhes ficam corretos com base no que foi previamente introduzido e prima **Criar**. 

    ![Criar o Estúdio Synapse nova piscina Apache Spark](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-26.png)

1. A piscina Apache Spark iniciará o processo de provisionamento.

1. Uma vez que o provisionamento esteja completo, a nova piscina Apache Spark aparecerá na lista.
    
    ![Sinaapse Studio nova lista de piscinas Apache Spark](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-28.png)

## <a name="clean-up-apache-spark-pool-resources-using-synapse-studio"></a>Limpe os recursos da piscina Apache Spark usando o Synapse Studio

Siga os passos abaixo para eliminar a piscina Apache Spark do espaço de trabalho utilizando o Synapse Studio.
> [!WARNING]
> A eliminação de uma piscina spark removerá o motor de análise do espaço de trabalho. Não será mais possível ligar à piscina, e todas as consultas, oleodutos e cadernos que usam esta piscina Spark deixarão de funcionar.

Se pretender eliminar a piscina Apache Spark, faça o seguinte:

1. Navegue até às piscinas Apache Spark no Centro de Gestão no Estúdio Synapse.
1. Selecione a elipse ao lado da piscina Apache a eliminar (neste caso, **contosospark)** para mostrar os comandos para a piscina Apache Spark.

    ![Listagem de piscinas Apache Spark, com a piscina recentemente criada selecionada.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-29.png)

1. Prima **para apagar**.
1. Confirme a eliminação e prima O botão **Eliminar.**
1. Quando o processo estiver concluído com sucesso, a piscina Apache Spark deixará de estar listada nos recursos do espaço de trabalho. 

## <a name="next-steps"></a>Passos seguintes

- Consulte [Quickstart: Crie uma piscina Apache Spark no Estúdio Synapse utilizando ferramentas web](quickstart-apache-spark-notebook.md).
- Consulte [Quickstart: Crie uma piscina Apache Spark utilizando o portal Azure](quickstart-create-apache-spark-pool-portal.md).
