---
title: Gerir políticas do Azure Data Lake Analytics
description: Saiba como utilizar políticas para controlar a utilização de uma conta do Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 64095f6706bb978cd33b8fe7833fe4e65fc3b0f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813420"
---
# <a name="manage-azure-data-lake-analytics-using-policies"></a>Gerir o Azure Data Lake Analytics, através de políticas

Utilizar políticas de conta, pode controlar como recursos de uma conta do Azure Data Lake Analytics são utilizados. Estas políticas permitem-lhe controlar o custo de utilização do Azure Data Lake Analytics. Por exemplo, com estas políticas pode impedir que os picos de custos inesperados, limitando quantas UAS a conta pode utilizar em simultâneo.

## <a name="account-level-policies"></a>Políticas ao nível da conta

Estas políticas se aplicam a todas as tarefas numa conta do Data Lake Analytics.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Número máximo de UAS numa conta do Data Lake Analytics
Uma política controla o número total de unidades de análise (UA), pode utilizar a conta do Data Lake Analytics. Por predefinição, o valor é definido para 250. Por exemplo, se este valor está definido para 250 UAS, pode ter uma tarefa em execução com 250 UAS atribuídas a ele ou 10 tarefas em execução com 25 UAS cada. Tarefas adicionais que sejam submetidas são colocados em fila até que as tarefas em execução são concluídas. Quando terminar tarefas em execução, UAS são liberadas para as tarefas em fila para ser executado.

Para alterar o número de UA para a sua conta do Data Lake Analytics:

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Sob **AUs máximo**, mova o controlo de deslize para selecionar um valor ou introduza o valor na caixa de texto. 
4. Clique em **Guardar**.

> [!NOTE]
> Se precisar de mais do que a predefinição (250) UAS, no portal, clique em **ajuda + suporte** para submeter um pedido de suporte. O número de UAS disponíveis na sua conta do Data Lake Analytics pode ser aumentado.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Número máximo de tarefas que podem ser executadas em simultâneo
Uma política controla o número de tarefas podem executar ao mesmo tempo. Por predefinição, este valor é definido como 20. Se o Data Lake Analytics tem UAS disponíveis, novas tarefas são agendadas para serem executadas imediatamente até que o número total de tarefas em execução atinge o valor desta política. Quando atingir o número máximo de tarefas que podem ser executadas em simultâneo, as tarefas subsequentes são colocados em fila por ordem de prioridade até que um ou mais tarefas em execução concluir (dependendo da disponibilidade de AU).

Para alterar o número de tarefas que podem ser executadas em simultâneo:

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Sob **máximo número de executar tarefas**, mova o controlo de deslize para selecionar um valor ou introduza o valor na caixa de texto. 
4. Clique em **Guardar**.

> [!NOTE]
> Se tiver de executar mais do que o número predefinido (20) de tarefas, no portal, clique em **ajuda + suporte** para submeter um pedido de suporte. O número de tarefas que podem ser executadas em simultâneo na sua conta do Data Lake Analytics pode ser aumentado.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Tempo de metadados da tarefa de manter e recursos 
Quando os utilizadores executarem tarefas de U-SQL, o serviço Data Lake Analytics mantém os ficheiros de todas as respetivas. Ficheiros relacionados incluem o script de U-SQL, os arquivos DLL referenciados no script de U-SQL, recursos compilados e estatísticas. Os ficheiros estão na pasta /system/ da conta de armazenamento do Azure Data Lake predefinida. Esta política controla quanto esses recursos são armazenados antes que eles são automaticamente excluídos (a predefinição é 30 dias). Pode utilizar estes ficheiros para depuração e para ajuste de desempenho de tarefas que irá executar novamente no futuro.

Para alterar o período de tempo para manter metadados da tarefa e os recursos:

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Sob **dias para reter consultas de tarefa**, mova o controlo de deslize para selecionar um valor ou introduza o valor na caixa de texto.  
4. Clique em **Guardar**.

## <a name="job-level-policies"></a>Políticas ao nível da tarefa

Com as políticas ao nível da tarefa, pode controlar o AUs máximo e a prioridade máxima que os utilizadores individuais (ou membros dos grupos de segurança específicos), podem definir nas tarefas que submetem. Esta política permite-lhe controlar a custos incorridos por utilizadores. Ele também permite que controle o efeito que as tarefas agendadas pode ter nas tarefas de produção de alta prioridade que estão em execução na mesma conta do Data Lake Analytics.

Data Lake Analytics tem duas políticas que podem ser definidos ao nível da tarefa:

* **Limite de AU por tarefa**: Os utilizadores apenas podem submeter as tarefas que têm cópia de segurança para este número de UA. Por predefinição, este limite é o mesmo que o limite de AU máximo para a conta.
* **Prioridade**: Os utilizadores apenas podem submeter as tarefas que têm uma prioridade inferior ou igual a este valor. Um número mais elevado indica uma prioridade mais baixa. Por predefinição, este limite é definido como 1, o que é a prioridade mais alta possível.

Há uma predefinição da política definida em todas as contas. A política predefinida que se aplica a todos os utilizadores da conta. Pode definir políticas adicionais para utilizadores e grupos específicos. 

> [!NOTE]
> Políticas ao nível da conta e políticas ao nível da tarefa se aplicam ao mesmo tempo.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Adicionar uma política para um grupo ou utilizador específico

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Sob **limites de submissão da tarefa**, clique nas **Adicionar política** botão. Em seguida, selecione ou introduza as seguintes definições:
    1. **Nome da política de computação**: Introduza um nome de política, lembrá-lo da finalidade da política.
    2. **Selecionar utilizador ou grupo**: Selecione o utilizador ou grupo que se aplica esta política.
    3. **Definir o limite de AU de tarefa**: Defina o limite de AU que se aplica ao utilizador selecionado ou grupo.
    4. **Definir o limite de prioridade**: Defina o limite de prioridade que se aplica ao utilizador selecionado ou grupo.

4. Clique em **OK**.

5. A nova política está listada na **predefinido** tabela de política, em **limites de submissão de tarefa**. 

### <a name="delete-or-edit-an-existing-policy"></a>Eliminar ou editar uma política existente

1. No portal do Azure, aceda à sua conta do Data Lake Analytics.
2. Clique em **Propriedades**.
3. Sob **limites de submissão de tarefa**, localizar a política que pretende editar.
4.  Para ver os **elimine** e **editar** opções, na coluna mais à direita da tabela, clique em `...`.

## <a name="additional-resources-for-job-policies"></a>Recursos adicionais para políticas de tarefa
* [Mensagem de blogue de descrição geral de política](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Mensagem de blogue de políticas ao nível da conta](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Mensagem de blogue de políticas ao nível da tarefa](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerir o Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

