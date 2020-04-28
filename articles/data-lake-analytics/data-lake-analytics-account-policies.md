---
title: Gerir políticas de conta azure Data Lake Analytics
description: Aprenda a usar as políticas de conta para controlar o uso de uma conta Data Lake Analytics, como o máximo de UA e o máximo de empregos.
services: data-lake-analytics
ms.service: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 4689714073047e383a53a04bd0069a8a27afdf9d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72966440"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Gerir o Azure Data Lake Analytics usando políticas de conta

As políticas de conta ajudam-no a controlar como são utilizadas as recursos de uma conta Azure Data Lake Analytics. Estas políticas permitem controlar o custo da utilização do Azure Data Lake Analytics. Por exemplo, com estas políticas pode evitar aumentos de custos inesperados limitando quantas UsA a conta pode simultaneamente usar.

## <a name="account-level-policies"></a>Políticas de nível de conta

Estas políticas aplicam-se a todos os empregos numa conta data lake analytics.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Número máximo de UsA numa conta data lake analytics
Uma política controla o número total de Unidades De Análise (AUs) que a sua conta Data Lake Analytics pode usar. Por padrão, o valor é definido para 250. Por exemplo, se este valor for fixado para 250 Us, pode ter um emprego a funcionar com 250 UsA atribuídos a ele, ou 10 postos de trabalho a funcionar com 25 Us Cada. Os postos de trabalho adicionais que são submetidos ficam em fila até que os postos de trabalho em funcionamento estejam concluídos. Quando os postos de trabalho estão terminados, as UA são libertadas para que os empregos em fila sejam executados.

Para alterar o número de AUs para a sua conta Data Lake Analytics:

1. No portal Azure, vá à sua conta data Lake Analytics.
2. Clique em **Limites e políticas.**
3. Em **AUs Máximo,** mova o slider para selecionar um valor ou introduza o valor na caixa de texto. 
4. Clique em **Guardar**.

> [!NOTE]
> Se precisar de mais do que a AUs padrão (250) no portal, clique em **Help+Support** para submeter um pedido de suporte. O número de UsA disponíveis na sua conta Data Lake Analytics pode ser aumentado.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Número máximo de postos de trabalho que podem funcionar simultaneamente
Esta política limita o número de postos de trabalho que podem ser executados simultaneamente. Por padrão, este valor é definido para 20. Se o seu Data Lake Analytics tiver AUs disponíveis, estão previstos novos postos de trabalho para funcionar imediatamente até que o número total de postos de trabalho em funcionamento atinja o valor desta política. Quando se chega ao número máximo de postos de trabalho que podem ser executados simultaneamente, os postos de trabalho subsequentes são colocados em fila de forma prioritária até que um ou mais postos de trabalho em funcionamento estejam concluídos (dependendo das UA disponíveis).

Para alterar o número de postos de trabalho que podem funcionar simultaneamente:

1. No portal Azure, vá à sua conta data Lake Analytics.
2. Clique em **Limites e políticas.**
3. Em **Número Máximo de Trabalhos em Execução,** mova o slider para selecionar um valor ou introduza o valor na caixa de texto. 
4. Clique em **Guardar**.

> [!NOTE]
> Se precisar de executar mais do que o número de empregos padrão (20), no portal, clique em **Help+Support** para submeter um pedido de apoio. O número de empregos que podem funcionar simultaneamente na sua conta Data Lake Analytics pode ser aumentado.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Quanto tempo para manter metadados e recursos de emprego 
Quando os seus utilizadores executam empregos U-SQL, o serviço Data Lake Analytics mantém todos os ficheiros relacionados. Estes ficheiros incluem o script U-SQL, os ficheiros DLL referenciados no script U-SQL, recursos compilados e estatísticas. Os ficheiros estão na /sistema/pasta da conta de armazenamento do lago De do Lago De dados do Azure. Esta política controla o tempo que estes recursos são armazenados antes de serem automaticamente eliminados (o predefinido é de 30 dias). Podes usar estes ficheiros para depurar, e para afinar o desempenho dos empregos que irás refazer no futuro.

Para alterar quanto tempo para manter metadados e recursos de emprego:

1. No portal Azure, vá à sua conta data Lake Analytics.
2. Clique em **Limites e políticas.**
3. Em Dias para Reter Consultas de **Trabalho,** mova o slider para selecionar um valor ou introduza o valor na caixa de texto.  
4. Clique em **Guardar**.

## <a name="job-level-policies"></a>Políticas de nível de emprego

As políticas de nível de emprego permitem controlar as UA máximas e a prioridade máxima que os utilizadores individuais (ou membros de grupos de segurança específicos) podem definir nos postos de trabalho que submetem. Esta política permite controlar os custos incorridos pelos utilizadores. Permite-lhe também controlar o efeito que os empregos programados podem ter em postos de produção de alta prioridade que estão a funcionar na mesma conta data Lake Analytics.

Data Lake Analytics tem duas políticas que pode definir ao nível do trabalho:

* **Limite de UA por trabalho:** Os utilizadores só podem submeter postos de trabalho que tenham até este número de UA. Por predefinição, este limite é o mesmo que o limite máximo de UA para a conta.
* **Prioridade**: Os utilizadores só podem submeter empregos com uma prioridade inferior ou igual a este valor. Um número mais elevado indica uma prioridade menor. Por predefinição, este limite é definido para 1, que é a maior prioridade possível.

Há uma política de incumprimento definida em todas as contas. A política predefinida aplica-se a todos os utilizadores da conta. Pode criar políticas adicionais para utilizadores e grupos específicos. 

> [!NOTE]
> As políticas de nível de contas e as políticas de nível de emprego aplicam-se simultaneamente.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Adicione uma política para um utilizador ou grupo específico

1. No portal Azure, vá à sua conta data Lake Analytics.
2. Clique em **Limites e políticas.**
3. Sob **os limites**de submissão de emprego, clique no botão Adicionar **Política.** Em seguida, selecione ou introduza as seguintes definições:
    1. **Nome da política computacional**: Introduza um nome de política, para lembrá-lo do propósito da política.
    2. **Selecione Utilizador ou Grupo**: Selecione o utilizador ou grupo a que esta política se aplica.
    3. Definir o limite de **Trabalho AU**: Definir o limite de UA que se aplica ao utilizador ou grupo selecionado.
    4. **Definir o Limite prioritário**: Definir o limite prioritário que se aplica ao utilizador ou grupo selecionado.

4. Clique em **OK**.

5. A nova política está listada na tabela de políticas **de incumprimento,** nos limites de **submissão de emprego.** 

### <a name="delete-or-edit-an-existing-policy"></a>Eliminar ou editar uma política existente

1. No portal Azure, vá à sua conta data Lake Analytics.
2. Clique em **Limites e políticas.**
3. Sob limites de submissão de **emprego,** encontre a política que pretende editar.
4.  Para ver as opções **De eliminar** e **editar,** `...`na coluna mais à direita da tabela, clique em .

## <a name="additional-resources-for-job-policies"></a>Recursos adicionais para políticas de emprego
* [Post de blog de visão geral da política](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Post de blog de políticas de nível de conta](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Post de blog de políticas de nível de emprego](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Começar com data lake analytics usando o portal Azure](data-lake-analytics-get-started-portal.md)
* [Gerencie o Azure Data Lake Analytics utilizando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)