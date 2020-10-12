---
title: Gerir a Azure Data Lake Analytics utilizando o portal Azure
description: Este artigo descreve como usar o portal Azure para gerir contas data lake analytics, fontes de dados, utilizadores, & empregos.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: cbce903bc7463cd917d48e341614afbe18536262
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87531217"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Gerir o Azure Data Lake Analytics com o portal do Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Este artigo descreve como gerir as contas do Azure Data Lake Analytics, fontes de dados, utilizadores e empregos através do portal Azure.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Gerir contas do Data Lake Analytics

### <a name="create-an-account"></a>Criar uma conta

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique **em Criar um recurso**Intelligence +  >  **analytics**Data Lake  >  **Analytics**.
3. Selecione os valores para os seguintes itens: 
   1. **Nome**: O nome da conta Data Lake Analytics.
   2. **Subscrição**: A assinatura Azure utilizada para a conta.
   3. **Grupo de Recursos**: O grupo de recursos Azure para criar a conta. 
   4. **Localização**: O datacenter Azure para a conta Data Lake Analytics. 
   5. **Data Lake Store**: A loja predefinitiva a ser utilizada para a conta Data Lake Analytics. A conta Azure Data Lake Store e a conta Data Lake Analytics devem estar no mesmo local.
4. Clique em **Criar**. 

### <a name="delete-a-data-lake-analytics-account"></a>Apagar uma conta data lake analytics

Antes de eliminar uma conta Data Lake Analytics, elimine a sua conta padrão data lake store.

1. No portal Azure, aceda à sua conta Data Lake Analytics.
2. Clique em **Eliminar**.
3. Digite o nome da conta.
4. Clique em **Eliminar**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Gerir origens de dados

Data Lake Analytics suporta as seguintes fontes de dados:

* Data Lake Storage
* Storage do Azure

Pode utilizar o Data Explorer para navegar em fontes de dados e realizar operações básicas de gestão de ficheiros. 

### <a name="add-a-data-source"></a>Adicionar uma origem de dados

1. No portal Azure, aceda à sua conta Data Lake Analytics.
2. Clique em **Fontes de Dados**.
3. Clique **em Adicionar Fonte de Dados**.
    
   * Para adicionar uma conta data lake store, você precisa do nome da conta e acesso à conta para poder questioná-la.
   * Para adicionar o armazenamento Azure Blob, precisa da conta de armazenamento e da chave da conta. Para encontrá-los, vá à conta de armazenamento no portal.

## <a name="set-up-firewall-rules"></a>Configurar regras de firewall

Pode utilizar o Data Lake Analytics para bloquear ainda mais o acesso à sua conta Data Lake Analytics ao nível da rede. Pode ativar uma firewall, especificar um endereço IP ou definir um intervalo de endereço IP para os seus clientes de confiança. Depois de ativar estas medidas, apenas os clientes que tenham os endereços IP dentro do intervalo definido podem ligar-se à loja.

Se outros serviços da Azure, como a Azure Data Factory ou VMs, ligarem-se à conta Data Lake Analytics, certifique-se de que **os Serviços de Azure permitem** a **ligação**. 

### <a name="set-up-a-firewall-rule"></a>Configurar uma regra de firewall

1. No portal Azure, aceda à sua conta Data Lake Analytics.
2. No menu à esquerda, clique em **Firewall**.

## <a name="add-a-new-user"></a>Adicionar um novo utilizador

Pode utilizar o **'Add User Wizard'** para facilmente forabando novos utilizadores do Data Lake.

1. No portal Azure, aceda à sua conta Data Lake Analytics.
2. À esquerda, em **"Começar",** clique em **Adicionar Assistente de Utilizador**.
3. Selecione um utilizador e, em seguida, clique em **Selecionar**.
4. Selecione uma função e, em seguida, clique em **Selecionar**. Para configurar um novo desenvolvedor para usar o Lago de Dados Azure, selecione o papel **de Desenvolvedor de Análise de Data Lake.**
5. Selecione as listas de controlo de acesso (ACLs) para as bases de dados U-SQL. Quando estiver satisfeito com as suas escolhas, clique em **Select**.
6. Selecione os ACLs para ficheiros. Para a loja predefinida, não altere os ACLs para a pasta raiz "/" e para a pasta /sistema. Clique em **Selecionar**.
7. Reveja todas as alterações selecionadas e, em seguida, clique em **Executar**.
8. Quando o assistente estiver terminado, clique em **'Fazer'.**

## <a name="manage-role-based-access-control"></a>Gerir o Controlo de Acesso Role-Based

Tal como outros serviços Azure, pode utilizar Role-Based Access Control (RBAC) para controlar a forma como os utilizadores interagem com o serviço.

As funções padrão do Azure têm as seguintes capacidades:
* **Proprietário**: Pode apresentar empregos, monitorizar empregos, cancelar empregos de qualquer utilizador e configurar a conta.
* **Contribuinte:** Pode apresentar empregos, monitorizar postos de trabalho, cancelar empregos de qualquer utilizador e configurar a conta.
* **Leitor:** Pode monitorizar os trabalhos.

Utilize o papel de Desenvolvedor de Análise de Data Lake analytics para permitir que os desenvolvedores U-SQL utilizem o serviço Data Lake Analytics. Pode utilizar o papel de Desenvolvedor de Análise de Data Lake para:
* Apresentar empregos.
* Monitorize o estado do trabalho e o progresso dos postos de trabalho apresentados por qualquer utilizador.
* Consulte os scripts U-SQL de trabalhos submetidos por qualquer utilizador.
* Cancele apenas os seus próprios empregos.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Adicione utilizadores ou grupos de segurança a uma conta Data Lake Analytics

1. No portal Azure, aceda à sua conta Data Lake Analytics.
2. Clique **em Controlo de Acesso (IAM)** Adicione a atribuição de  >  **função**.
3. Selecione um papel.
4. Adicione um utilizador.
5. Clique em **OK**.

>[!NOTE]
>Se um utilizador ou um grupo de segurança precisar de apresentar postos de trabalho, também precisam de autorização na conta da loja. Para obter mais informações, consulte [dados seguros armazenados na Data Lake Store.](../data-lake-store/data-lake-store-secure-data.md)
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Gerir tarefas

### <a name="submit-a-job"></a>Submeter um emprego

1. No portal Azure, aceda à sua conta Data Lake Analytics.

2. Clique **em Novo Emprego**. Para cada trabalho, configurar:

    1. **Nome do trabalho**: O nome do trabalho.
    2. **Prioridade:** Os números mais baixos têm maior prioridade. Se dois postos de trabalho forem postos de trabalho, o que tem um valor de prioridade mais baixo é o primeiro.
    3. **Paralelismo**: O número máximo de processos de computação a reservar para este trabalho.

3. Clique em **Submeter Tarefa**.

### <a name="monitor-jobs"></a>Monitorizar trabalhos

1. No portal Azure, aceda à sua conta Data Lake Analytics.
2. Clique **em Ver Todos os Empregos**. É mostrada uma lista de todos os empregos ativos e recentemente acabados na conta.
3. Opcionalmente, clique em **Filtro** para ajudá-lo a encontrar os trabalhos por **Time Range,** **Job Name**e **Author** values. 

### <a name="monitoring-pipeline-jobs"></a>Monitorização de postos de trabalho em gasodutos
Empregos que fazem parte de um oleoduto trabalham em conjunto, geralmente sequencialmente, para realizar um cenário específico. Por exemplo, pode ter um oleoduto que limpa, extrai, transforma, agrega o uso para insights do cliente. Os trabalhos de gasoduto são identificados utilizando a propriedade "Pipeline" quando o trabalho foi submetido. Os postos de trabalho programados com a ADF V2 terão automaticamente esta propriedade povoada. 

Para ver uma lista de empregos U-SQL que fazem parte de oleodutos: 

1. No portal Azure, aceda às suas contas Data Lake Analytics.
2. Clique **em Insights de Emprego**. O separador "Todos os Empregos" será incumprido, mostrando uma lista de trabalhos em execução, filas e empregos terminados.
3. Clique no **separador Pipeline Jobs.** Será apresentada uma lista de postos de trabalho em gasodutos, juntamente com estatísticas agregadas para cada gasoduto.

### <a name="monitoring-recurring-jobs"></a>Monitorização de empregos recorrentes
Um trabalho recorrente é aquele que tem a mesma lógica de negócio, mas usa diferentes dados de entrada sempre que funciona. Idealmente, os empregos recorrentes devem sempre ter sucesso e ter um tempo de execução relativamente estável; monitorizar estes comportamentos ajudará a garantir que o trabalho é saudável. Os empregos recorrentes são identificados usando a propriedade "Recorrência". Os postos de trabalho programados com a ADF V2 terão automaticamente esta propriedade povoada.

Para ver uma lista de empregos U-SQL que são recorrentes: 

1. No portal Azure, aceda às suas contas Data Lake Analytics.
2. Clique **em Insights de Emprego**. O separador "Todos os Empregos" será incumprido, mostrando uma lista de trabalhos em execução, filas e empregos terminados.
3. Clique no **separador Empregos Recorrentes.** Será apresentada uma lista de postos de trabalho recorrentes, juntamente com estatísticas agregadas para cada trabalho recorrente.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Gerir a Azure Data Lake Analytics utilizando a Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Gerir o Azure Data Lake Analytics com políticas](data-lake-analytics-account-policies.md)
