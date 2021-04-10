---
title: 'Quickstart: criar um espaço de trabalho Synapse'
description: Crie um espaço de trabalho sinapse seguindo os passos deste guia.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: d38f1f294f60b73e8f1e69169a75333eb175c9f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600161"
---
# <a name="quickstart-create-a-synapse-workspace"></a>Quickstart: Criar um espaço de trabalho synapse
Este quickstart descreve os passos para criar um espaço de trabalho Azure Synapse utilizando o portal Azure.

## <a name="create-a-synapse-workspace"></a>Criar uma área de trabalho do Synapse

1. Abra o [portal Azure](https://portal.azure.com)e na pesquisa superior para **Synapse**.
1. Nos resultados da pesquisa, em **Serviços,** selecione **Azure Synapse Analytics**.
1. **Selecione Adicionar** para criar um espaço de trabalho.
1. No **separador Básicos,** dê ao espaço de trabalho um nome único. Vamos usar **o meu espaço de trabalho** neste documento
1. Precisa de uma conta ADLSGEN2 para criar um espaço de trabalho. A escolha mais simples é criar uma nova. Se quiser reutilizar uma existente, terá de realizar alguma configuração adicional. 
1. OPÇÃO 1 Criação de uma nova conta ADLSGEN2 
    1. Em **Select Data Lake Storage Gen 2,** clique em Criar **Novo** e nomeie-o **contosolake**.
    1. Em **Select Data Lake Storage Gen 2**, clique no Sistema de **Ficheiros** e nomeie os **utilizadores**.
1. OPÇÃO 2 Consulte as instruções **da Conta de Armazenamento** na parte inferior deste documento.
1. O seu espaço de trabalho Azure Synapse utilizará esta conta de armazenamento como a conta de armazenamento "primária" e o recipiente para armazenar dados do espaço de trabalho. O espaço de trabalho armazena dados em tabelas Apache Spark. Armazena registos de aplicações Spark sob uma pasta chamada **/sinapse/workspacename**.
1. Selecione **Rever + criar** > **Criar**. O seu espaço de trabalho está pronto em poucos minutos.

> [!NOTE]
> Depois de criar o seu espaço de trabalho Azure Synapse, não poderá mover o espaço de trabalho para outro inquilino do Azure Ative Directory. Se for feito através da migração de subscrição ou outras ações, o acesso aos artefatos na área de trabalho poderá ser perdido.
> Além disso, não é possível criar um espaço de trabalho Synapse Analytics numa subscrição [do Cloud Solution Provider (CSP).](/partner-center/csp-overview)

## <a name="open-synapse-studio"></a>Open Synapse Studio

Depois de criar o seu espaço de trabalho Azure Synapse, tem duas formas de abrir o Synapse Studio:

* Abra o seu espaço de trabalho sinapse no [portal Azure](https://portal.azure.com). No topo da secção **Overview,** selecione **Launch Synapse Studio**.
* Vá ao `https://web.azuresynapse.net` e inscreva-se no seu espaço de trabalho.

## <a name="prepare-an-existing-storage-account-for-use-with-azure-synapse-analytics"></a>Prepare uma conta de armazenamento existente para uso com a Azure Synapse Analytics

1. Abra o [portal do Azure](https://portal.azure.com).
1. Navegue para uma conta de armazenamento ADLSGEN2 existente
1. Selecione **o controlo de acesso (IAM)** no painel esquerdo. Em seguida, atribua as seguintes funções ou certifique-se de que já estão atribuídas:
    * Atribua-se ao papel **de Proprietário.**
    * Atribua-se à função **de Proprietário de Dados blob de armazenamento.**
1. No painel esquerdo, selecione **Recipientes** e crie um recipiente.
1. Pode dar ao recipiente qualquer nome. Neste documento, vamos nomear os **utilizadores** do contentor.
1. Aceite a definição predefinitiva **do nível de acesso público** e, em seguida, selecione **Criar**.

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>Configure o acesso à conta de armazenamento a partir do seu espaço de trabalho

As identidades geridas para o seu espaço de trabalho Azure Synapse podem já ter acesso à conta de armazenamento. Siga estes passos para se certificar de que:

1. Abra o [portal Azure](https://portal.azure.com) e a conta de armazenamento primária escolhida para o seu espaço de trabalho.
1. Selecione o controlo de **acesso (IAM)** a partir do painel esquerdo.
1. Atribua as seguintes funções ou certifique-se de que já estão atribuídas. Usamos o mesmo nome para a identidade do espaço de trabalho e o nome do espaço de trabalho.
    * Para a função **de Contribuinte de Dados blob de armazenamento** na conta de armazenamento, atribua o meu espaço de **trabalho** como identidade do espaço de trabalho.
    * Atribua **o meu espaço de trabalho** como o nome do espaço de trabalho.

1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

* [Crie uma piscina SQL dedicada](quickstart-create-sql-pool-studio.md) 
* [Crie uma piscina Apache Spark sem servidor](quickstart-create-apache-spark-pool-portal.md)
* [Utilizar conjunto de SQL sem servidor](quickstart-sql-on-demand.md)