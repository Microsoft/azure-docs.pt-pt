---
title: 'Quickstart: Get start - crie um espaço de trabalho synapse'
description: Neste tutorial, você aprenderá a criar um espaço de trabalho Synapse, uma piscina SQL dedicada e uma piscina Apache Spark sem servidor.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 94d069a283249f2880743ba911c32bf3821d28c8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102171488"
---
# <a name="creating-a-synapse-workspace"></a>Criação de um espaço de trabalho sinapse

Neste tutorial, você aprenderá a criar um espaço de trabalho Synapse, uma piscina SQL dedicada e uma piscina Apache Spark sem servidor. 

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste tutorial, precisa de ter acesso a um grupo de recursos para o qual lhe é atribuída a função **Proprietário.** Crie o espaço de trabalho synapse neste grupo de recursos.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Criar um espaço de trabalho sinapse no portal Azure

1. Abra o [portal Azure,](https://portal.azure.com)na barra de pesquisa entre em **Sinapse** sem bater na entrada.
1. Nos resultados da pesquisa, em **Serviços,** selecione **Azure Synapse Analytics**.
1. **Selecione Adicionar** para criar um espaço de trabalho.
1. **Separador básico,** em **detalhes do projeto,** preencha os seguintes campos:
      1. **Subscrição** - Escolha qualquer subscrição.
      2. **Grupo de recursos** - Utilize qualquer grupo de recursos.
      3. **Grupo de recursos** - Deixe este em branco.
1. No separador **Básicos,** em **detalhes do Espaço de Trabalho,** preencha os seguintes campos:
      1. **Nome do espaço de** trabalho - Escolha qualquer nome globalmente único. Neste tutorial, usaremos **o meu espaço de trabalho.**
      1. **Região** - Escolha qualquer região.
      1. **Selecione Data Lake Storage Gen 2**
        1. Clique no botão **de partir da subscrição**.
        1. Por **nome de conta,** clique em **Criar Novo** e nomeie a nova conta de armazenamento **ou** similar como este nome deve ser único.
        1. Por **nome do sistema de ficheiros,** clique em Criar **Novos** e nomeie **os utilizadores**. Isto criará um recipiente de armazenamento chamado **utilizadores.** O espaço de trabalho utilizará esta conta de armazenamento como a conta de armazenamento "primária" para as tabelas Spark e os registos de aplicações Spark.
        1. Verifique a função "Atribua-me a função de contribuinte de dados de armazenamento na conta Gen2 de armazenamento de dados". 
1. Selecione **Rever + criar** > **Criar**. O seu espaço de trabalho está pronto em poucos minutos.

> [!NOTE]
> Para permitir funcionalidades de espaço de trabalho a partir de uma piscina SQL dedicada existente (anteriormente SQL DW) consulte como [ativar um espaço de trabalho para a sua piscina DE SQL dedicada (anteriormente SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Open Synapse Studio

Depois de criar o seu espaço de trabalho Azure Synapse, tem duas formas de abrir o Synapse Studio:

* Abra o seu espaço de trabalho sinapse no [portal Azure,](https://portal.azure.com)na secção **visão geral** do espaço de trabalho synapse, selecione **Open** na caixa Open Synapse Studio.
* Vá ao `https://web.azuresynapse.net` e inscreva-se no seu espaço de trabalho.


## <a name="the-built-in-serverless-sql-pool"></a>A piscina SQL sem servidor incorporada

Cada espaço de trabalho vem com uma piscina SQL sem servidor pré-construída chamada **Built-in**. Esta piscina não pode ser apagada. As piscinas SQL sem servidor permitem-lhe utilizar o SQL sem ter de reservar capacidade com piscinas SQL dedicadas. Ao contrário das piscinas SQL dedicadas, a faturação de um pool SQL sem servidor baseia-se na quantidade de dados digitalizados para executar a consulta, e não no número de capacidade atribuída à piscina.


## <a name="create-a-dedicated-sql-pool"></a>Crie uma piscina SQL dedicada

1. No Synapse Studio, no painel do lado esquerdo, **selecione Gerir**  >  **as piscinas SQL**.
1. Selecione **Novo**
1. Para **o nome da piscina SQL** selecione **SQLPOOL1**
1. Para **nível de desempenho** escolha **DW100C**
1. Selecione **Rever + criar** > **Criar**. A sua piscina SQL dedicada estará pronta em poucos minutos. A sua piscina SQL dedicada está associada a uma base de dados dedicada à piscina SQL que também se chama **SQLPOOL1.**

Uma piscina SQL dedicada consome recursos faturados desde que esteja ativo. Pode fazer uma pausa na piscina mais tarde para reduzir custos.

> [!NOTE] 
> Ao criar uma nova piscina SQL dedicada (anteriormente SQL DW) no seu espaço de trabalho, a página dedicada de fornecimento de piscinas SQL abrirá. O provisionamento terá lugar no servidor lógico SQL.


## <a name="create-a-serverless-apache-spark-pool"></a>Crie uma piscina Apache Spark sem servidor

1. No Synapse Studio, no painel do lado esquerdo, **selecione Gerir**  >  **as piscinas Apache Spark**.
1. Selecione **Novo** 
1. Para **o nome da piscina Apache Spark** **insi spark1**.
1. Para **o tamanho do nó,** **introduza Pequeno**.
1. Para **o número de nós** Definir o mínimo para 3 e o máximo a 3
1. Selecione **Rever + criar** > **Criar**. A tua piscina Apache Spark estará pronta em alguns segundos.

A piscina Spark diz ao Azure Synapse quantos recursos de faíscas usar. Paga apenas os recursos que utilizar. Quando deixa de utilizar ativamente a piscina, os recursos são automaticamente eliminados e são reciclados.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analise usando uma piscina SQL dedicada](get-started-analyze-sql-pool.md)
