---
title: Gerir o Azure Data Lake Analytics com o portal do Azure
description: Este artigo descreve como utilizar o portal do Azure para gerir contas do Data Lake Analytics, origens de dados, os utilizadores e tarefas.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8b2f16f45be1d095e9be8042611de328af36f064
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813449"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Gerir o Azure Data Lake Analytics com o portal do Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Este artigo descreve como gerir contas do Azure Data Lake Analytics, origens de dados, os utilizadores e tarefas com o portal do Azure.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Gerir contas do Data Lake Analytics

### <a name="create-an-account"></a>Criar uma conta

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **criar um recurso** > **inteligência + análise** > **do Data Lake Analytics**.
3. Selecione os valores para os seguintes itens: 
   1. **Nome**: O nome da conta do Data Lake Analytics.
   2. **Subscrição**: A subscrição do Azure utilizada para a conta.
   3. **Grupo de recursos**: O grupo de recursos do Azure na qual pretende criar a conta. 
   4. **Localização**: O datacenter do Azure para a conta do Data Lake Analytics. 
   5. **Data Lake Store**: O arquivo predefinido a ser utilizado para a conta do Data Lake Analytics. A conta do Azure Data Lake Store e a conta do Data Lake Analytics tem de ser na mesma localização.
4. Clique em **Criar**. 

### <a name="delete-a-data-lake-analytics-account"></a>Eliminar uma conta do Data Lake Analytics

Antes de eliminar uma conta do Data Lake Analytics, elimine a sua conta do Data Lake Store predefinida.

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **Eliminar**.
3. Escreva o nome da conta.
4. Clique em **Eliminar**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Gerir origens de dados

O Data Lake Analytics suporta as seguintes origens de dados:

* Data Lake Store
* Storage do Azure

Pode utilizar o Data Explorer para procurar origens de dados e realizar operações de gestão de arquivo básico. 

### <a name="add-a-data-source"></a>Adicionar uma origem de dados

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **origens de dados**.
3. Clique em **Adicionar origem de dados**.
    
   * Para adicionar uma conta do Data Lake Store, precisa do nome da conta e o acesso à conta de possam consultá-los.
   * Para adicionar armazenamento de Blobs do Azure, terá da conta de armazenamento e a chave da conta. Para encontrá-los, vá para a conta de armazenamento no portal.

## <a name="set-up-firewall-rules"></a>Configurar regras de firewall

Pode utilizar o Data Lake Analytics para bloquear melhor acesso à sua conta do Data Lake Analytics ao nível da rede. Pode ativar uma firewall, especifique um endereço IP ou definir um intervalo de endereços IP para os seus clientes fidedignos. Depois de ativar estas medidas, podem ligar apenas os clientes que tenham os endereços IP no intervalo definido para o arquivo.

Se outros serviços do Azure, como o Azure Data Factory ou VMs, ligarem à conta do Data Lake Analytics, certifique-se de que **permitir que os serviços do Azure** está ativada **no**. 

### <a name="set-up-a-firewall-rule"></a>Configurar uma regra de firewall

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. No menu à esquerda, clique em **Firewall**.

## <a name="add-a-new-user"></a>Adicionar um novo utilizador

Pode utilizar o **Assistente para adicionar utilizador** para provisionar facilmente novos usuários do Data Lake.

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. No lado esquerdo, sob **introdução**, clique em **Assistente para adicionar utilizador**.
3. Selecionar um utilizador e, em seguida, clique em **selecione**.
4. Selecione uma função e, em seguida, clique em **selecione**. Para definir um novo programador para utilizar o Azure Data Lake, selecione o **programador do Data Lake Analytics** função.
5. Selecione as listas de controlo de acesso (ACLs) para as bases de dados U-SQL. Quando estiver satisfeito com as suas opções, clique em **selecione**.
6. Selecione as ACLs para ficheiros. Para o armazenamento predefinido, não altere as ACLs para a pasta raiz "/" e para a pasta. cmd /system. Clique em **Selecionar**.
7. Reveja todas as alterações selecionadas e, em seguida, clique em **executar**.
8. Quando o assistente for concluído, clique em **feito**.

## <a name="manage-role-based-access-control"></a>Gerir controlo de acesso baseado em funções

À semelhança de outros serviços do Azure, pode utilizar o controlo de acesso baseado em funções (RBAC) para controlar como os utilizadores interagem com o serviço.

As funções do RBAC padrão tem as seguintes funcionalidades:
* **Proprietário**: Pode submeter tarefas, monitorizar tarefas, cancelar tarefas a partir de qualquer utilizador e configurar a conta.
* **Contribuinte**: Pode submeter tarefas, monitorizar tarefas, cancelar tarefas a partir de qualquer utilizador e configurar a conta.
* **Leitor**: Pode monitorizar tarefas.

Utilize a função de desenvolvedor do Data Lake Analytics para permitir que os desenvolvedores de U-SQL utilizar o serviço do Data Lake Analytics. Pode usar a função do programador do Data Lake Analytics:
* Submeta tarefas.
* Monitorizar o estado da tarefa e o progresso de tarefas submetidas por nenhum utilizador.
* Veja os scripts de U-SQL de tarefas submetidas por nenhum utilizador.
* Cancele apenas suas próprias tarefas.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Adicionar utilizadores ou grupos de segurança para uma conta do Data Lake Analytics

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **controlo de acesso (IAM)**  > **adicionar atribuição de função**.
3. Selecione uma função.
4. Adicione um utilizador.
5. Clique em **OK**.

>[!NOTE]
>Se um utilizador ou um grupo de segurança tem de submeter as tarefas, eles também precisam de permissão na conta de armazenamento. Para obter mais informações, consulte [proteger os dados armazenados no Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Gerir tarefas

### <a name="submit-a-job"></a>Submeter uma tarefa

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.

2. Clique em **nova tarefa**. Para cada tarefa, configure:

    1. **Nome da tarefa**: O nome da tarefa.
    2. **Prioridade**: Têm prioridade mais alta. Se duas tarefas são colocados em fila, aquele com o valor de prioridade inferior é executado primeiro.
    3. **Paralelismo**: O número máximo de processos de computação para reservar para esta tarefa.

3. Clique em **Submeter Tarefa**.

### <a name="monitor-jobs"></a>Monitorizar trabalhos

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **ver todas as tarefas**. É apresentada uma lista de todas as tarefas de Active Directory e recentemente concluídas na conta.
3. Opcionalmente, clique em **filtro** para ajudar a encontrar as tarefas por **intervalo de tempo**, **nome da tarefa**, e **autor** valores. 

### <a name="monitoring-pipeline-jobs"></a>Monitorizar tarefas de pipeline
Tarefas que fazem parte de um pipeline funcionam em conjunto, normalmente, sequencialmente, para realizar um cenário específico. Por exemplo, pode ter um pipeline que limpa, extrai, transforma, agrega a utilização de informações do cliente. Tarefas de pipeline são identificadas usando a propriedade de "Pipeline" quando a tarefa foi submetida. Tarefas agendadas com o ADF V2 terão automaticamente esta propriedade preenchida. 

Para ver uma lista de tarefas de U-SQL que fazem parte de pipelines: 

1. No portal do Azure, aceda às suas contas do Data Lake Analytics.
2. Clique em **informações da tarefa**. O separador de "Todas as tarefas" será predefinidas, que mostra uma lista de execução, colocados em fila e terminou de tarefas.
3. Clique nas **tarefas de Pipeline** separador. Será apresentada uma lista de tarefas de pipeline, juntamente com estatísticas agregadas para cada pipeline.

### <a name="monitoring-recurring-jobs"></a>Monitorizar tarefas periódicas
Uma tarefa periódica é aquele que tem a mesma lógica de negócios, mas utiliza dados de entrada diferentes, sempre que é executado. O ideal é que tarefas periódicas devem sempre ter êxito e ter tempo de execução relativamente estável; esses comportamentos de monitoramento ajudará a garantir que a tarefa está em bom estada. Tarefas periódicas são identificadas usando a propriedade "Recurrence". Tarefas agendadas com o ADF V2 terão automaticamente esta propriedade preenchida.

Para ver uma lista de tarefas de U-SQL que são recorrente: 

1. No portal do Azure, aceda às suas contas do Data Lake Analytics.
2. Clique em **informações da tarefa**. O separador de "Todas as tarefas" será predefinidas, que mostra uma lista de execução, colocados em fila e terminou de tarefas.
3. Clique nas **tarefas recorrentes** separador. Será apresentada uma lista de tarefas periódicas juntamente com estatísticas agregadas para cada tarefa periódica.

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Gerir o Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Gerir o Azure Data Lake Analytics, através de políticas](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-policies)
