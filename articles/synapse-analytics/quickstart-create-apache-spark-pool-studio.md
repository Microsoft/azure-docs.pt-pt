---
title: Quickstart - Crie uma piscina Apache Spark (pré-visualização) usando o Estúdio Synapse
description: Crie uma nova piscina Apache Spark utilizando o Estúdio Synapse seguindo os passos deste guia.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 3/19/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 01b85f937516f24aeb50f76c71c89bc47e52abf0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83666433"
---
# <a name="quickstart-create-an-apache-spark-pool-preview-using-synapse-studio"></a>Quickstart: Crie uma piscina Apache Spark (pré-visualização) usando o Estúdio Synapse

A Azure Synapse Analytics oferece vários motores de análise para o ajudar a ingerir, transformar, modelar, analisar e servir os seus dados. O Apache Spark pool oferece capacidades de computação de dados grandes de código aberto. Depois de criar uma piscina Apache Spark no seu espaço de trabalho Synapse, os dados podem ser carregados, modelados, processados e servidos para obter insights.  

Este quickstart descreve os passos para criar uma piscina Apache Spark num espaço de trabalho Synapse usando o Estúdio Synapse.

> [!IMPORTANT]
> A faturação dos casos Spark é pronunciada por minuto, quer esteja a usá-las ou não. Certifique-se de que encerra a sua instância Spark depois de terminar de usá-la, ou demarcar um curto prazo. Para obter mais informações, consulte a secção **Limpar recursos** deste artigo.

Se não tiver uma subscrição Azure, [crie uma conta gratuita antes](https://azure.microsoft.com/free/)de começar .

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Espaço de trabalho sinapse](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navegue para o espaço de trabalho synapse

1. Navegue para o espaço de trabalho Synapse onde a piscina Apache Spark será criada digitando o nome do serviço (ou nome de recurso diretamente) na barra de pesquisa.
![Barra de pesquisa do portal Azure com espaços de trabalho Synapse digitados.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. A partir da lista de espaços de trabalho, digite o nome (ou parte do nome) do espaço de trabalho para abrir. Para este exemplo, usaremos um espaço de trabalho chamado **contosoanalytics.**
![Listagem de espaços de trabalho synapse filtrados para mostrar aqueles que contêm o nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Lançar Estúdio Synapse 

1. A partir da visão geral do espaço de trabalho, selecione **Launch Synapse Studio** para abrir o local onde será criada a piscina Apache Spark. Digite o nome do serviço ou o nome do recurso diretamente na barra de pesquisa.
![Visão geral do espaço de trabalho do portal Azure Synapse com o Launch Synapse Studio em destaque.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-the-apache-spark-pool-in-synapse-studio"></a>Crie a piscina Apache Spark no Estúdio Synapse

1. Na página inicial do Estúdio Synapse, navegue para o Centro de **Gestão** na navegação à esquerda selecionando o ícone **Manage.**
![Página inicial do Estúdio Synapse com secção Management Hub em destaque.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Uma vez no Centro de Gestão, navegue até à secção **de piscinas Apache Spark** para ver a lista atual de piscinas Apache Spark que estão disponíveis no espaço de trabalho.
![Centro de gestão do Estúdio Synapse com navegação apache Spark piscinas selecionado](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-22.png)

1. Selecione **+ Novo** e aparecerá o novo assistente de criação de Apache Spark. 
![Synapse Studio Management Hub listando de piscinas Spark.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-23.png)

1. Introduza os seguintes detalhes no separador **Basics:**

    | Definição | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome da piscina Apache Spark** | contosospark | Este é o nome que a piscina Apache Spark terá. |
    | **Tamanho do nó** | Pequeno (4 vCPU / 32 GB) | Desloque-o ao menor tamanho para reduzir os custos para este arranque rápido |
    | **Dimensionamento Automático** | Desativado | Não vamos precisar de escala automática neste arranque rápido. |
    | **Número de nós** | 8 | Use um tamanho pequeno para limitar os custos neste arranque rápido|
    
    ![Estúdio Synapse novo formade piscina Apache Spark.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-24.png)
    > [!IMPORTANT]
    > Note que existem limitações específicas para os nomes que as piscinas Apache Spark podem usar. Os nomes devem conter apenas letras ou números, devem ser 15 ou menos caracteres, devem começar com uma letra, não conter palavras reservadas, e ser únicos no espaço de trabalho.

1. No separador seguinte (Definições adicionais), deixe todas as predefinições e prima **Review + criar** (não adicionaremos nenhuma etiqueta).
 ![Estúdio Synapse novo formade piscina Apache Spark.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-25.png)

1. Por enquanto, não adicionaremos nenhuma tag, por isso selecione **Review + criar**.

1. No separador **Review + criar,** certifique-se de que os detalhes parecem corretos com base no que foi previamente introduzido e pressione **a criação**. 
 ![Estúdio Synapse novo formade piscina Apache Spark.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-26.png)

1. A piscina Apache Spark iniciará o processo de provisionamento.
![Estúdio Synapse novo formade piscina Apache Spark.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-27.png)

1. Uma vez concluída a disposição, a nova piscina Apache Spark aparecerá na lista.
![Estúdio Synapse novo formade piscina Apache Spark.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-28.png)

## <a name="clean-up-apache-spark-pool-resources-using-synapse-studio"></a>Limpe os recursos da piscina Apache Spark usando o Estúdio Synapse

Siga os passos abaixo para eliminar a piscina Apache Spark do espaço de trabalho utilizando o Estúdio Synapse.
> [!WARNING]
> A eliminação de uma piscina Spark removerá o motor de análise do espaço de trabalho. Deixará de ser possível ligar-se à piscina, e todas as consultas, oleodutos e cadernos que utilizam esta piscina spark deixarão de funcionar.

Se quiser eliminar a piscina Apache Spark, faça o seguinte:

1. Navegue para as piscinas Apache Spark no Centro de Gestão no Estúdio Synapse.
1. Selecione a elipse junto à piscina Apache a eliminar (neste caso, **contosospark)** para mostrar os comandos para a piscina Apache Spark.
![Listagem de piscinas Apache Spark, com a piscina recentemente criada selecionada.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-29.png)
1. Prima **apagar**.
1. Confirme a eliminação e prima **Apagar** o botão.
 ![Diálogo de confirmação para eliminar a piscina Apache Spark selecionada.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-30.png)
1. Quando o processo terminar com sucesso, a piscina Apache Spark deixará de estar listada nos recursos do espaço de trabalho. 

## <a name="next-steps"></a>Próximos passos

- Ver [Quickstart: Crie uma piscina Apache Spark no Estúdio Synapse utilizando ferramentas web](quickstart-apache-spark-notebook.md).
- Ver [Quickstart: Crie uma piscina Apache Spark utilizando o portal Azure](quickstart-create-apache-spark-pool-portal.md).