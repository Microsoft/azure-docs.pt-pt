---
title: 'Tutorial: Começar a criar um espaço de trabalho sinapse'
description: Neste tutorial, você aprenderá a criar um espaço de trabalho Synapse, uma piscina SQL e uma piscina Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: b4d48dcc8f09ae8e2ec3bb198f8864de1c945682
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101860"
---
# <a name="create-a-synapse-workspace"></a>Criar um espaço de trabalho sinapse

Neste tutorial, você aprenderá a criar um espaço de trabalho Synapse, uma piscina SQL e uma piscina Apache Spark. 

## <a name="prepare-a-storage-account"></a>Preparar uma conta de armazenamento

1. Abra o [portal do Azure](https://portal.azure.com).
1. Criar uma nova conta de armazenamento que tenha as seguintes definições:

    |Tecla de Tabulação|Definição | Valor sugerido | Descrição |
    |---|---|---|---|
    |Informações básicas|**Nome da conta de armazenamento**| Escolha qualquer nome.| Neste documento, usaremos o nome **contosolake.**|
    |Informações básicas|**Tipo de conta**| **StorageV2** ||
    |Informações básicas|**Localização**|Escolha qualquer local.| Recomendamos que o seu espaço de trabalho Azure Synapse Analytics e a conta Azure Data Lake Storage Gen2 estejam na mesma região.|
    |Avançado|**Data Lake Storage Gen2**|**Ativado**| O Azure Synapse só funciona com contas de armazenamento que tenham esta definição ativada.|
    |||||

1. Depois de criar a conta de armazenamento, selecione **Access control (IAM)** no painel esquerdo. Em seguida, atribua as seguintes funções ou certifique-se de que já estão atribuídas:
    * Atribua-se ao papel **de Proprietário.**
    * Atribua-se à função **de Proprietário de Dados blob de armazenamento.**
1. No painel esquerdo, selecione **Recipientes** e crie um recipiente.
1. Pode dar ao recipiente qualquer nome. Neste documento, vamos nomear os **utilizadores**do contentor.
1. Aceite a definição predefinitiva **do nível de acesso público**e, em seguida, selecione **Criar**.

No passo seguinte, irá configurar o seu espaço de trabalho Azure Synapse para utilizar esta conta de armazenamento como a conta de armazenamento "primária" e o recipiente para armazenar dados do espaço de trabalho. O espaço de trabalho armazena dados em tabelas Apache Spark. Armazena registos de aplicações Spark sob uma pasta chamada **/sinapse/workspacename**.

## <a name="create-a-synapse-workspace"></a>Criar um espaço de trabalho sinapse

1. Abra o [portal Azure](https://portal.azure.com)e na pesquisa superior para **Synapse**.
1. Nos resultados da pesquisa, em **Serviços,** selecione **Azure Synapse Analytics (pré-visualização de espaços de trabalho)**.
1. **Selecione Adicionar** para criar um espaço de trabalho utilizando estas definições:

    |Tecla de Tabulação|Definição | Valor sugerido | Descrição |
    |---|---|---|---|
    |Informações básicas|**Nome da área de trabalho**|Pode dar-lhe o nome de qualquer coisa.| Neste documento, usaremos **o meu espaço de trabalho.**|
    |Informações básicas|**Região**|Combine com a região da conta de armazenamento.|

1. Em **Select Data Lake Storage Gen 2,** selecione a conta e o recipiente que criou anteriormente.
1. Selecione **Rever + criar** > **Criar**. O seu espaço de trabalho está pronto em poucos minutos.

## <a name="verify-access-to-the-storage-account"></a>Verifique o acesso à conta de armazenamento

As identidades geridas para o seu espaço de trabalho Azure Synapse podem já ter acesso à conta de armazenamento. Siga estes passos para se certificar de que:

1. Abra o [portal Azure](https://portal.azure.com) e a conta de armazenamento primária escolhida para o seu espaço de trabalho.
1. Selecione o controlo de **acesso (IAM)** a partir do painel esquerdo.
1. Atribua as seguintes funções ou certifique-se de que já estão atribuídas. Usamos o mesmo nome para a identidade do espaço de trabalho e o nome do espaço de trabalho.
    * Para a função **de Contribuinte de Dados blob de armazenamento** na conta de armazenamento, atribua o meu espaço de **trabalho** como identidade do espaço de trabalho.
    * Atribua **o meu espaço de trabalho** como o nome do espaço de trabalho.

1. Selecione **Guardar**.

## <a name="open-synapse-studio"></a>Open Synapse Studio

Depois de criar o seu espaço de trabalho Azure Synapse, tem duas formas de abrir o Synapse Studio:

* Abra o seu espaço de trabalho sinapse no [portal Azure](https://portal.azure.com). No topo da secção **Overview,** selecione **Launch Synapse Studio**.
* Vá ao `https://web.azuresynapse.net` e inscreva-se no seu espaço de trabalho.

## <a name="create-a-sql-pool"></a>Criar uma piscina SQL

1. No Synapse Studio, no painel do lado esquerdo, **selecione Gerir**  >  **as piscinas SQL**.
1. Selecione **Novo** e introduza estas definições:

    |Definição | Valor sugerido | 
    |---|---|---|
    |**Nome da piscina SQL**| **SQLDB1**|
    |**Nível de desempenho**|**DW100C**|
    |||

1. Selecione **Rever + criar** > **Criar**. A sua piscina SQL estará pronta em poucos minutos. A sua piscina SQL está associada a uma base de dados de piscinas SQL que também se chama **SQLDB1**.

Uma piscina SQL consome recursos faturados desde que esteja ativo. Pode fazer uma pausa na piscina mais tarde para reduzir custos.

## <a name="create-an-apache-spark-pool"></a>Criar uma piscina Apache Spark

1. No Synapse Studio, no painel do lado esquerdo, **selecione Gerir**  >  **as piscinas Apache Spark**.
1. Selecione **Novo** e introduza estas definições:

    |Definição | Valor sugerido | 
    |---|---|---|
    |**Nome da piscina Apache Spark**|**Faísca1**
    |**Tamanho do nó**| **Pequeno**|
    |**Número de nós**| Definir o mínimo para 3 e o máximo para 3|

1. Selecione **Rever + criar** > **Criar**. A tua piscina Apache Spark estará pronta em alguns segundos.

> [!NOTE]
> Apesar do nome, uma piscina Apache Spark não é como uma piscina SQL. São apenas alguns metadados básicos que usas para dizer ao espaço de trabalho do Azure Synapse como interagir com o Spark.

Por serem metadados, as piscinas de faíscas não podem ser iniciadas ou paradas.

Quando realizar atividade spark em Azure Synapse, especifique uma piscina spark para usar. A piscina diz ao Azure Synapse quantos recursos de faíscas usar. Paga apenas os recursos que utilizar. Quando deixa de utilizar ativamente a piscina, os recursos são automaticamente eliminados e são reciclados.

> [!NOTE]
> As bases de dados spark são criadas independentemente a partir de piscinas Spark. Um espaço de trabalho tem sempre uma base de dados Spark chamada **predefinição.** Pode criar bases de dados adicionais de Spark.

## <a name="the-sql-on-demand-pool"></a>A piscina a pedido do SQL

Cada espaço de trabalho vem com uma piscina pré-construída chamada **SQL on-demand**. Esta piscina não pode ser apagada. O pool a pedido do SQL permite-lhe trabalhar com o SQL sem ter de criar ou pensar em gerir uma piscina SQL em Azure Synapse.

Ao contrário dos outros tipos de piscinas, a faturação para o SQL a pedido baseia-se na quantidade de dados digitalizados para executar a consulta, e não no número de recursos utilizados para executar a consulta.

* A SQL on demand tem as suas próprias bases de dados SQL a pedido que existem independentemente de qualquer piscina a pedido do SQL.
* Um espaço de trabalho tem sempre exatamente uma piscina a pedido SQL chamada **SQL on-demand**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analise usando uma piscina SQL](get-started-analyze-sql-pool.md)
