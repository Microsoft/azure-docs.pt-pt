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
ms.date: 08/27/2020
ms.openlocfilehash: 78ec233e618511c748ed9f51b97161eddc5e8308
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707531"
---
# <a name="create-a-synapse-workspace"></a>Criar uma área de trabalho do Synapse

Neste tutorial, você aprenderá a criar um espaço de trabalho Synapse, uma piscina SQL e uma piscina Apache Spark. 

## <a name="create-a-synapse-workspace"></a>Criar uma área de trabalho do Synapse

1. Abra o [portal Azure](https://portal.azure.com)e na pesquisa superior para **Synapse**.
1. Nos resultados da pesquisa, em **Serviços,** selecione **Azure Synapse Analytics (pré-visualização de espaços de trabalho)**.
1. **Selecione Adicionar** para criar um espaço de trabalho.
1. No **Basics,** escolha um nome de espaço de trabalho. Neste tutorial, usaremos **o meu espaço de trabalho.**
1. Precisa de uma conta ADLSGEN2 para criar um espaço de trabalho. A escolha mais simples para criar uma nova. Se quiser reutilizar uma existente, terá de realizar alguma configuração adicional. 
1. OPÇÃO 1 Criação de uma nova conta ADLSGEN2 
    1. Navegue para **selecionar data lake storage Gen 2**. 
    1. Clique **em Criar Novo** e nomeie-o **contosolake**.
    1. Clique no **Sistema de Ficheiros** e nomeie os **utilizadores.**
1. OPÇÃO 2 Utilizando uma conta ADLSGEN2 existente. Consulte as instruções **da Conta de Armazenamento ADLSGEN2** na parte inferior deste documento.
1. O seu espaço de trabalho Azure Synapse utilizará esta conta de armazenamento como a conta de armazenamento "primária" e o recipiente para armazenar dados do espaço de trabalho. O espaço de trabalho armazena dados em tabelas Apache Spark. Armazena registos de aplicações Spark sob uma pasta chamada **/sinapse/workspacename**.
1. Selecione **Rever + criar** > **Criar**. O seu espaço de trabalho está pronto em poucos minutos.


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
    |**Tamanho do nó**| **Small**|
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

## <a name="preparing-a-adlsgen2-storage-account"></a>Preparar uma conta de armazenamento ADLSGEN2

### <a name="perform-the-following-steps-before-you-create-your-workspace"></a>Execute os seguintes passos ANTES de criar o seu espaço de trabalho

1. Abra o [portal do Azure](https://portal.azure.com).
1. Navegue para a sua conta de armazenamento existente
1. Selecione **o controlo de acesso (IAM)** no painel esquerdo. 
1. Atribua as seguintes funções ou certifique-se de que já estão atribuídas:
    * Atribua-se ao papel **de Proprietário.**
    * Atribua-se à função **de Proprietário de Dados blob de armazenamento.**
1. No painel esquerdo, selecione **Recipientes** e crie um recipiente.
1. Pode dar um nome ao contentor. Neste documento, utilizamos os  **utilizadores**de nomes.
1. Aceite a definição predefinitiva **do nível de acesso público**e, em seguida, selecione **Criar**.

### <a name="perform-the-following-steps-after-you-create-your-workspace"></a>Execute os seguintes passos DEPOIS de criar o seu espaço de trabalho

Configure o acesso à conta de armazenamento a partir do seu espaço de trabalho. As identidades geridas para o seu espaço de trabalho Azure Synapse podem já ter acesso à conta de armazenamento. Siga estes passos para se certificar de que:

1. Abra o [portal Azure](https://portal.azure.com) e a conta de armazenamento primária escolhida para o seu espaço de trabalho.
1. Selecione o controlo de **acesso (IAM)** a partir do painel esquerdo.
1. Atribua as seguintes funções ou certifique-se de que já estão atribuídas. Usamos o mesmo nome para a identidade do espaço de trabalho e o nome do espaço de trabalho.
    * Para a função **de Contribuinte de Dados blob de armazenamento** na conta de armazenamento, atribua o meu espaço de **trabalho** como identidade do espaço de trabalho.
    * Atribua **o meu espaço de trabalho** como o nome do espaço de trabalho.

1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analise usando uma piscina SQL](get-started-analyze-sql-pool.md)
