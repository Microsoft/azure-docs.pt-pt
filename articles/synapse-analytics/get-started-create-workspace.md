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
ms.date: 10/07/2020
ms.openlocfilehash: 862d2a93058c63dbfad1db49346edcbfe3c02ad1
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592452"
---
# <a name="creating-a-synapse-workspace"></a>Criação de um espaço de trabalho sinapse

Neste tutorial, você aprenderá a criar um espaço de trabalho Synapse, uma piscina SQL dedicada e uma piscina Apache Spark sem servidor. 

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste tutorial, precisa de ter acesso a um grupo de recursos para o qual lhe é atribuída a função **Proprietário.** Crie o espaço de trabalho synapse neste grupo de recursos.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Criar um espaço de trabalho sinapse no portal Azure

1. Abra o [portal Azure](https://portal.azure.com)e na pesquisa superior para **Synapse**.
1. Nos resultados da pesquisa, em **Serviços,** selecione **Azure Synapse Analytics (pré-visualização de espaços de trabalho)**.
1. **Selecione Adicionar** para criar um espaço de trabalho.
1. No **Básico, insira** a sua **Assinatura** Preferida, **Grupo de Recursos,** **Região,** e, em seguida, escolha um nome de espaço de trabalho. Neste tutorial, usaremos **o meu espaço de trabalho.**
1. Navegue para **selecionar data lake storage Gen 2**. 
1. Clique **em Criar Novo** e nomeie-o **contosolake**.
1. Clique no **Sistema de Ficheiros** e nomeie os **utilizadores.** Isto irá criar um recipiente chamado **utilizadores**
1. O espaço de trabalho utilizará esta conta de armazenamento como a conta de armazenamento "primária" para as tabelas Spark e os registos de aplicações Spark.
1. Selecione **Rever + criar** > **Criar**. O seu espaço de trabalho está pronto em poucos minutos.

## <a name="open-synapse-studio"></a>Open Synapse Studio

Depois de criar o seu espaço de trabalho Azure Synapse, tem duas formas de abrir o Synapse Studio:

* Abra o seu espaço de trabalho sinapse no [portal Azure](https://portal.azure.com). No topo da secção **Overview,** selecione **Launch Synapse Studio**.
* Vá ao `https://web.azuresynapse.net` e inscreva-se no seu espaço de trabalho.

## <a name="create-a-dedicated-sql-pool"></a>Crie uma piscina SQL dedicada

1. No Synapse Studio, no painel do lado esquerdo, **selecione Gerir**  >  **as piscinas SQL**.
1. Selecione **Novo**
1. Para **o nome da piscina SQL** selecione **SQLPOOL1**
1. Para **nível de desempenho** escolha **DW100C**
1. Selecione **Rever + criar** > **Criar**. A sua piscina SQL dedicada estará pronta em poucos minutos. A sua piscina SQL dedicada está associada a uma base de dados dedicada à piscina SQL que também se chama **SQLPOOL1.**

Uma piscina SQL dedicada consome recursos faturados desde que esteja ativo. Pode fazer uma pausa na piscina mais tarde para reduzir custos.

## <a name="create-a-serverless-apache-spark-pool"></a>Crie uma piscina Apache Spark sem servidor

1. No Synapse Studio, no painel do lado esquerdo, **selecione Gerir**  >  **as piscinas Apache Spark**.
1. Selecione **Novo** 
1. Para **o nome da piscina Apache Spark** **insi spark1**.
1. Para **o tamanho do nó,** **introduza Pequeno**.
1. Para **o número de nós** Definir o mínimo para 3 e o máximo a 3
1. Selecione **Rever + criar** > **Criar**. A tua piscina Apache Spark estará pronta em alguns segundos.

A piscina Spark diz ao Azure Synapse quantos recursos de faíscas usar. Paga apenas os recursos que utilizar. Quando deixa de utilizar ativamente a piscina, os recursos são automaticamente eliminados e são reciclados.

## <a name="the-serverless-sql-pool"></a>A piscina SQL sem servidor

Cada espaço de trabalho vem com uma piscina pré-construída chamada **Built-in.** Esta piscina não pode ser apagada. A piscina SQL sem servidor permite-lhe trabalhar com o SQL sem ter de criar ou pensar em gerir uma piscina SQL sem servidor em Azure Synapse. Ao contrário das piscinas SQL dedicadas, a faturação de um pool SQL sem servidor baseia-se na quantidade de dados digitalizados para executar a consulta, e não no número de recursos utilizados para executar a consulta.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analise usando uma piscina SQL dedicada](get-started-analyze-sql-pool.md)
