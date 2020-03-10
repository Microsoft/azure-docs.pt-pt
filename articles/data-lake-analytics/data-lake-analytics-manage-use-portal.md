---
title: Gerencie o Azure Data Lake Analytics utilizando o portal Azure
description: Este artigo descreve como usar o portal Azure para gerir contas data lake analytics, fontes de dados, utilizadores e empregos.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8a6b7cb3fd45e17b84519efcaa826b569083e156
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361312"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Gerir o Azure Data Lake Analytics utilizando o portal Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Este artigo descreve como gerir contas de Azure Data Lake Analytics, fontes de dados, utilizadores e empregos através do portal Azure.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Gerir contas do Data Lake Analytics

### <a name="create-an-account"></a>Criar uma conta

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique **em Criar um recurso** > Inteligência + **análise** > Data **Lake Analytics**.
3. Selecione os valores para os seguintes itens: 
   1. **Nome**: O nome da conta Data Lake Analytics.
   2. **Subscrição**: A subscrição Azure utilizada para a conta.
   3. **Grupo de Recursos**: O grupo de recursos Azure para criar a conta. 
   4. **Localização**: O datacenter Azure para a conta Data Lake Analytics. 
   5. **Data Lake Store**: A loja padrão a ser utilizada para a conta Data Lake Analytics. A conta Azure Data Lake Store e a conta Data Lake Analytics devem estar no mesmo local.
4. Clique em **Criar**. 

### <a name="delete-a-data-lake-analytics-account"></a>Eliminar uma conta Data Lake Analytics

Antes de eliminar uma conta Data Lake Analytics, elimine a sua conta padrão data lake store.

1. No portal Azure, vá à sua conta data Lake Analytics.
2. Clique em **Eliminar**.
3. Digite o nome da conta.
4. Clique em **Eliminar**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Gerir fontes de dados

Data Lake Analytics suporta as seguintes fontes de dados:

* Data Lake Store
* Storage do Azure

Pode utilizar o Data Explorer para navegar em fontes de dados e realizar operações básicas de gestão de ficheiros. 

### <a name="add-a-data-source"></a>Adicionar uma origem de dados

1. No portal Azure, vá à sua conta data Lake Analytics.
2. Clique em **Fontes de Dados**.
3. Clique em **Adicionar Fonte de Dados**.
    
   * Para adicionar uma conta data lake store, você precisa do nome da conta e acesso à conta para poder consultar.
   * Para adicionar armazenamento Azure Blob, precisa da conta de armazenamento e da chave da conta. Para encontrá-los, vá à conta de armazenamento no portal.

## <a name="set-up-firewall-rules"></a>Configurar regras de firewall

Pode utilizar o Data Lake Analytics para bloquear ainda mais o acesso à sua conta Data Lake Analytics ao nível da rede. Pode ativar uma firewall, especificar um endereço IP ou definir um intervalo de endereçoIP para os seus clientes de confiança. Depois de ativar estas medidas, apenas os clientes que possuem os endereços IP dentro da gama definida podem ligar-se à loja.

Se outros serviços azure, como a Azure Data Factory ou VMs, se conectarem à conta Data Lake Analytics, certifique-se de que os **serviços de navegação digital** estão **ligados**. 

### <a name="set-up-a-firewall-rule"></a>Configurar uma regra de firewall

1. No portal Azure, vá à sua conta data Lake Analytics.
2. No menu à esquerda, clique em **Firewall**.

## <a name="add-a-new-user"></a>Adicionar um novo utilizador

Pode utilizar o Assistente de **Utilizador adicionar** para fornecer facilmente novos utilizadores do Data Lake.

1. No portal Azure, vá à sua conta data Lake Analytics.
2. À esquerda, em **Iniciar-se,** clique em **Adicionar assistente de utilizador**.
3. Selecione um utilizador e, em seguida, clique em **Selecionar**.
4. Selecione uma função e, em seguida, clique em **Selecionar**. Para criar um novo desenvolvedor para utilizar o Lago de Dados Azure, selecione a função **data lake analytics developer.**
5. Selecione as listas de controlo de acesso (ACLs) para as bases de dados U-SQL. Quando estiver satisfeito com as suas escolhas, clique em **Select**.
6. Selecione os ACLs para ficheiros. Para a loja predefinida, não altere os ACLs para a pasta raiz "/" e para a pasta /sistema. Clique em **Selecionar**.
7. Reveja todas as alterações selecionadas e, em seguida, clique em **Executar**.
8. Quando o assistente estiver terminado, clique **em Done**.

## <a name="manage-role-based-access-control"></a>Gerir o controlo de acesso baseado em funções

À semelhança de outros serviços Azure, pode utilizar o Controlo de Acesso Baseado em Funções (RBAC) para controlar a forma como os utilizadores interagem com o serviço.

As funções RBAC padrão têm as seguintes capacidades:
* **Proprietário**: Pode apresentar empregos, monitorizar postos de trabalho, cancelar empregos de qualquer utilizador e configurar a conta.
* **Contributivo**: Pode apresentar postos de trabalho, monitorizar postos de trabalho, cancelar empregos de qualquer utilizador e configurar a conta.
* **Leitor:** Pode monitorizar os trabalhos.

Utilize a função data lake analytics developer para permitir que os desenvolvedores u-SQL utilizem o serviço Data Lake Analytics. Pode utilizar a função data lake analytics developer para:
* Submeta empregos.
* Monitorizar o estado do trabalho e o progresso dos postos de trabalho apresentados por qualquer utilizador.
* Consulte os scripts U-SQL a partir de trabalhos submetidos por qualquer utilizador.
* Cancele apenas os seus próprios empregos.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Adicione utilizadores ou grupos de segurança a uma conta Data Lake Analytics

1. No portal Azure, vá à sua conta data Lake Analytics.
2. Clique no controlo de **acesso (IAM)**  > **Adicionar a atribuição de funções**.
3. Selecione um papel.
4. Adicione um utilizador.
5. Clique em **OK**.

>[!NOTE]
>Se um utilizador ou um grupo de segurança precisar de apresentar postos de trabalho, também precisam de autorização na conta da loja. Para mais informações, consulte [os dados seguros armazenados na Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Gerir tarefas

### <a name="submit-a-job"></a>Submeter um emprego

1. No portal Azure, vá à sua conta data Lake Analytics.

2. Clique em **Novo Trabalho**. Para cada trabalho, configure:

    1. **Nome**do trabalho : O nome do trabalho.
    2. **Prioridade**: Números mais baixos têm maior prioridade. Se dois empregos estiverem na fila, o que tem menor valor prioritário é o primeiro.
    3. **Paralelismo**: O número máximo de processos de computação a reservar para este trabalho.

3. Clique em **Submeter Tarefa**.

### <a name="monitor-jobs"></a>Monitorizar trabalhos

1. No portal Azure, vá à sua conta data Lake Analytics.
2. Clique em **Ver Todos os Empregos**. É apresentada uma lista de todos os trabalhos ativos e recentemente concluídos na conta.
3. Opcionalmente, clique em **Filtro** para ajudá-lo a encontrar os trabalhos por **Time Range,** **Nome de Trabalho**e Valores **de Autor.** 

### <a name="monitoring-pipeline-jobs"></a>Monitorização de postos de trabalho em gasodutos
Empregos que fazem parte de um oleoduto trabalham em conjunto, geralmente sequencialmente, para realizar um cenário específico. Por exemplo, pode ter um oleoduto que limpa, extrai, transforma, agrega o uso para insights do cliente. Os trabalhos de gasoduto são identificados utilizando a propriedade "Pipeline" quando o trabalho foi submetido. Os postos de trabalho programados com a ADF V2 terão automaticamente esta propriedade povoada. 

Para ver uma lista de empregos U-SQL que fazem parte dos oleodutos: 

1. No portal Azure, vá às suas contas data Lake Analytics.
2. Clique em **Insights de Emprego**. O separador "All Jobs" será indefinido, mostrando uma lista de trabalhos de corrida, fila e de fim de emprego.
3. Clique no separador **Pipeline Jobs.** Será apresentada uma lista de postos de trabalho em pipeline, juntamente com estatísticas agregadas para cada gasoduto.

### <a name="monitoring-recurring-jobs"></a>Monitorização de postos de trabalho recorrentes
Um trabalho recorrente é aquele que tem a mesma lógica de negócio, mas usa diferentes dados de entrada sempre que funciona. Idealmente, os empregos recorrentes devem sempre ter êxito e ter um tempo de execução relativamente estável; monitorizar estes comportamentos ajudará a garantir que o trabalho é saudável. São identificados trabalhos recorrentes utilizando a propriedade "Recurrence". Os postos de trabalho programados com a ADF V2 terão automaticamente esta propriedade povoada.

Para ver uma lista de empregos U-SQL que são recorrentes: 

1. No portal Azure, vá às suas contas data Lake Analytics.
2. Clique em **Insights de Emprego**. O separador "All Jobs" será indefinido, mostrando uma lista de trabalhos de corrida, fila e de fim de emprego.
3. Clique no separador **Empregos Recorrentes.** Será apresentada uma lista de postos de trabalho recorrentes, juntamente com estatísticas agregadas para cada trabalho recorrente.

## <a name="next-steps"></a>Passos Seguintes

* [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Gerencie o Azure Data Lake Analytics utilizando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Gerir o Azure Data Lake Analytics usando políticas](data-lake-analytics-account-policies.md)
