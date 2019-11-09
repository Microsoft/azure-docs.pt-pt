---
title: Gerenciar Azure Data Lake Analytics usando o portal do Azure
description: Este artigo descreve como usar o portal do Azure para gerenciar contas de Data Lake Analytics, fontes de dados, usuários & trabalhos.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8a6b7cb3fd45e17b84519efcaa826b569083e156
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839464"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Gerenciar Azure Data Lake Analytics usando o portal do Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Este artigo descreve como gerenciar contas de Azure Data Lake Analytics, fontes de dados, usuários e trabalhos usando o portal do Azure.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Gerir contas do Data Lake Analytics

### <a name="create-an-account"></a>Criar uma conta

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **criar um recurso** > **inteligência + análise** > **Data Lake Analytics**.
3. Selecione os valores para os seguintes itens: 
   1. **Nome**: o nome da conta de data Lake Analytics.
   2. **Assinatura**: a assinatura do Azure usada para a conta.
   3. **Grupo de recursos**: o grupo de recursos do Azure no qual criar a conta. 
   4. **Local**: o datacenter do Azure para a conta de data Lake Analytics. 
   5. **Data Lake Store**: o repositório padrão a ser usado para a conta de data Lake Analytics. A conta de Azure Data Lake Store e a conta de Data Lake Analytics devem estar no mesmo local.
4. Clique em **Criar**. 

### <a name="delete-a-data-lake-analytics-account"></a>Excluir uma conta de Data Lake Analytics

Antes de excluir uma conta de Data Lake Analytics, exclua sua conta de Data Lake Store padrão.

1. Na portal do Azure, vá para sua conta do Data Lake Analytics.
2. Clique em **Eliminar**.
3. Digite o nome da conta.
4. Clique em **Eliminar**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Gerenciar fontes de dados

O Data Lake Analytics dá suporte às seguintes fontes de dados:

* Data Lake Store
* Storage do Azure

Você pode usar Data Explorer para procurar fontes de dados e executar operações básicas de gerenciamento de arquivos. 

### <a name="add-a-data-source"></a>Adicionar uma fonte de dados

1. Na portal do Azure, vá para sua conta do Data Lake Analytics.
2. Clique em **fontes de dados**.
3. Clique em **Adicionar fonte de dados**.
    
   * Para adicionar uma conta de Data Lake Store, você precisa do nome da conta e do acesso à conta para poder consultá-la.
   * Para adicionar o armazenamento de BLOBs do Azure, você precisa da conta de armazenamento e da chave de conta. Para encontrá-los, vá para a conta de armazenamento no Portal.

## <a name="set-up-firewall-rules"></a>Configurar regras de firewall

Você pode usar Data Lake Analytics para bloquear ainda mais o acesso à sua conta do Data Lake Analytics no nível da rede. Você pode habilitar um firewall, especificar um endereço IP ou definir um intervalo de endereços IP para seus clientes confiáveis. Depois de habilitar essas medidas, somente os clientes que têm os endereços IP dentro do intervalo definido podem se conectar ao repositório.

Se outros serviços do Azure, como Azure Data Factory ou VMs, se conectarem à conta de Data Lake Analytics, verifique se a **permissão permitir serviços do Azure** **está ativada.** 

### <a name="set-up-a-firewall-rule"></a>Configurar uma regra de firewall

1. Na portal do Azure, vá para sua conta do Data Lake Analytics.
2. No menu à esquerda, clique em **Firewall**.

## <a name="add-a-new-user"></a>Adicionar um novo usuário

Você pode usar o **Assistente para Adicionar usuário** para provisionar facilmente novos usuários do data Lake.

1. Na portal do Azure, vá para sua conta do Data Lake Analytics.
2. À esquerda, em **introdução**, clique em **Assistente para Adicionar usuário**.
3. Selecione um usuário e clique em **selecionar**.
4. Selecione uma função e, em seguida, clique em **selecionar**. Para configurar um novo desenvolvedor para usar Azure Data Lake, selecione a função de **desenvolvedor de data Lake Analytics** .
5. Selecione as listas de controle de acesso (ACLs) para os bancos de dados U-SQL. Quando estiver satisfeito com suas escolhas, clique em **selecionar**.
6. Selecione as ACLs para arquivos. Para o repositório padrão, não altere as ACLs para a pasta raiz "/" e para a pasta/estado Clique em **Selecionar**.
7. Examine todas as alterações selecionadas e clique em **executar**.
8. Quando o assistente for concluído, clique em **concluído**.

## <a name="manage-role-based-access-control"></a>Gerenciar o controle de acesso baseado em função

Assim como outros serviços do Azure, você pode usar o RBAC (controle de acesso baseado em função) para controlar como os usuários interagem com o serviço.

As funções RBAC padrão têm os seguintes recursos:
* **Proprietário**: pode enviar trabalhos, monitorar trabalhos, Cancelar trabalhos de qualquer usuário e configurar a conta.
* **Colaborador**: pode enviar trabalhos, monitorar trabalhos, Cancelar trabalhos de qualquer usuário e configurar a conta.
* **Leitor**: pode monitorar trabalhos.

Use a função de desenvolvedor Data Lake Analytics para permitir que os desenvolvedores de U-SQL usem o serviço Data Lake Analytics. Você pode usar a função de desenvolvedor Data Lake Analytics para:
* Enviar trabalhos.
* Monitore o status do trabalho e o progresso dos trabalhos enviados por qualquer usuário.
* Consulte os scripts U-SQL de trabalhos enviados por qualquer usuário.
* Cancele apenas seus próprios trabalhos.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Adicionar usuários ou grupos de segurança a uma conta de Data Lake Analytics

1. Na portal do Azure, vá para sua conta do Data Lake Analytics.
2. Clique em **controle de acesso (iam)**  > **Adicionar atribuição de função**.
3. Selecione uma função.
4. Adicione um utilizador.
5. Clique em **OK**.

>[!NOTE]
>Se um usuário ou grupo de segurança precisar enviar trabalhos, eles também precisarão de permissão na conta da loja. Para obter mais informações, consulte [proteger dados armazenados em data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Gerir tarefas

### <a name="submit-a-job"></a>Enviar um trabalho

1. Na portal do Azure, vá para sua conta do Data Lake Analytics.

2. Clique em **novo trabalho**. Para cada trabalho, configure:

    1. **Nome do trabalho**: o nome do trabalho.
    2. **Prioridade**: os números inferiores têm prioridade mais alta. Se dois trabalhos forem enfileirados, aquele com o valor de prioridade mais baixo será executado primeiro.
    3. **Paralelismo**: o número máximo de processos de computação a ser reservado para esse trabalho.

3. Clique em **Submeter Tarefa**.

### <a name="monitor-jobs"></a>Monitorizar trabalhos

1. Na portal do Azure, vá para sua conta do Data Lake Analytics.
2. Clique em **Exibir todos os trabalhos**. Uma lista de todos os trabalhos ativos e concluídos recentemente na conta é mostrada.
3. Opcionalmente, clique em **Filtrar** para ajudá-lo a encontrar os trabalhos por **intervalo de tempo**, nome do **trabalho**e valores de **autor** . 

### <a name="monitoring-pipeline-jobs"></a>Monitoramento de trabalhos de pipeline
Os trabalhos que fazem parte de um pipeline trabalham juntos, geralmente em sequência, para realizar um cenário específico. Por exemplo, você pode ter um pipeline que limpa, extrai, transforma, agrega uso para o Customer insights. Os trabalhos de pipeline são identificados usando a propriedade "pipeline" quando o trabalho foi enviado. Os trabalhos agendados usando o ADF v2 terão automaticamente essa propriedade preenchida. 

Para exibir uma lista de trabalhos do U-SQL que fazem parte de pipelines: 

1. Na portal do Azure, acesse suas contas de Data Lake Analytics.
2. Clique em **insights de trabalho**. A guia "todos os trabalhos" será padronizada, mostrando uma lista de trabalhos em execução, em fila e finalizados.
3. Clique na guia **trabalhos de pipeline** . Uma lista de trabalhos de pipeline será mostrada junto com as estatísticas agregadas para cada pipeline.

### <a name="monitoring-recurring-jobs"></a>Monitorando trabalhos recorrentes
Um trabalho recorrente é aquele que tem a mesma lógica de negócios, mas usa dados de entrada diferentes toda vez que é executado. O ideal é que os trabalhos recorrentes sempre tenham sucesso e tenham um tempo de execução relativamente estável; o monitoramento desses comportamentos ajudará a garantir que o trabalho esteja íntegro. Os trabalhos recorrentes são identificados usando a propriedade "Recurrence". Os trabalhos agendados usando o ADF v2 terão automaticamente essa propriedade preenchida.

Para exibir uma lista de trabalhos do U-SQL que são recorrentes: 

1. Na portal do Azure, acesse suas contas de Data Lake Analytics.
2. Clique em **insights de trabalho**. A guia "todos os trabalhos" será padronizada, mostrando uma lista de trabalhos em execução, em fila e finalizados.
3. Clique na guia **trabalhos recorrentes** . Uma lista de trabalhos recorrentes será mostrada junto com as estatísticas agregadas para cada trabalho recorrente.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Gerenciar Azure Data Lake Analytics usando Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Gerenciar Azure Data Lake Analytics usando políticas](data-lake-analytics-account-policies.md)
