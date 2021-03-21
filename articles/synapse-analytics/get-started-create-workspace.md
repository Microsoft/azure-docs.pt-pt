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
ms.date: 03/17/2021
ms.openlocfilehash: ee8a192bcfe20c77e47ee9c89dfc3286a979e358
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722298"
---
# <a name="creating-a-synapse-workspace"></a>Criação de um espaço de trabalho sinapse

Neste tutorial, você aprenderá a criar um espaço de trabalho Synapse, uma piscina SQL dedicada e uma piscina Apache Spark sem servidor. 

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste tutorial, precisa de ter acesso a um grupo de recursos para o qual lhe é atribuída a função **Proprietário.** Crie o espaço de trabalho synapse neste grupo de recursos.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Criar um espaço de trabalho sinapse no portal Azure

### <a name="start-the-process"></a>Inicie o processo
1. Abra o [portal Azure,](https://portal.azure.com)na barra de pesquisa entre em **Sinapse** sem bater na entrada.
1. Nos resultados da pesquisa, em **Serviços,** selecione **Azure Synapse Analytics**.
1. **Selecione Adicionar** para criar um espaço de trabalho.

## <a name="basics-tab--project-details"></a>Detalhes básicos do separador > projeto
1. **Separador básico,** em **detalhes do projeto,** preencha os seguintes campos:
      1. **Subscrição** - Escolha qualquer subscrição.
      2. **Grupo de recursos** - Utilize qualquer grupo de recursos.
      3. **Grupo de recursos** - Deixe este em branco.


## <a name="basics-tab--workspace-details"></a>Detalhes básicos do separador > do espaço de trabalho
1. No separador **Básicos,** em **detalhes do Espaço de Trabalho,** preencha os seguintes campos:
      1. **Nome do espaço de** trabalho - Escolha qualquer nome globalmente único. Neste tutorial, usaremos **o meu espaço de trabalho.**
      1. **Região** - Escolha qualquer região.
      1. **Selecione Data Lake Storage Gen 2**
        1. Clique no botão **de partir da subscrição**.
        1. Por **nome de conta,** clique em **Criar Novo** e nomeie a nova conta de armazenamento **ou** similar como este nome deve ser único.
        1. Por **nome do sistema de ficheiros,** clique em Criar **Novos** e nomeie **os utilizadores**. Isto criará um recipiente de armazenamento chamado **utilizadores.** O espaço de trabalho utilizará esta conta de armazenamento como a conta de armazenamento "primária" para as tabelas Spark e os registos de aplicações Spark.
        1. Verifique a função "Atribua-me a função de contribuinte de dados de armazenamento na conta Gen2 de armazenamento de dados". 

### <a name="completing-the-process"></a>Completar o processo
5. Selecione **Rever + criar** > **Criar**. O seu espaço de trabalho está pronto em poucos minutos.

> [!NOTE]
> Para permitir funcionalidades de espaço de trabalho a partir de uma piscina SQL dedicada existente (anteriormente SQL DW) consulte como [ativar um espaço de trabalho para a sua piscina DE SQL dedicada (anteriormente SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Open Synapse Studio

Depois de criar o seu espaço de trabalho Azure Synapse, tem duas formas de abrir o Synapse Studio:

* Abra o seu espaço de trabalho sinapse no [portal Azure,](https://portal.azure.com)na secção **visão geral** do espaço de trabalho synapse, selecione **Open** na caixa Open Synapse Studio.
* Vá ao `https://web.azuresynapse.net` e inscreva-se no seu espaço de trabalho.











## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analise usando uma piscina SQL sem servidor](get-started-analyze-sql-on-demand.md)
