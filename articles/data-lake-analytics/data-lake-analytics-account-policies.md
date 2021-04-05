---
title: Gerir políticas de conta Azure Data Lake Analytics
description: Saiba como usar as políticas de conta para controlar o uso de uma conta Data Lake Analytics, como a máxima UA e o máximo de empregos.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: ba69098f32e131714a15923aef64c3f6ba17e18f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013314"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Gerir a Azure Data Lake Analytics usando políticas de conta

As políticas de conta ajudam-no a controlar a forma como são utilizados recursos uma conta Azure Data Lake Analytics. Estas políticas permitem-lhe controlar o custo da utilização do Azure Data Lake Analytics. Por exemplo, com estas políticas pode prevenir picos de custos inesperados limitando quantas UA a conta pode simultaneamente usar.## Políticas de nível de conta

Estas políticas aplicam-se a todos os empregos numa conta data lake analytics.

## <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Número máximo de AUs numa conta data lake analytics

Uma política controla o número total de Unidades de Analytics (AUs) que a sua conta Data Lake Analytics pode utilizar. Por predefinição, o valor é definido para 250. Por exemplo, se este valor for fixado em 250 AUs, pode ter um emprego a funcionar com 250 AUs atribuídos a ele, ou 10 postos de trabalho a funcionar com 25 AUs cada. Os postos de trabalho adicionais que são apresentados são postos em fila até que os postos de trabalho estejam concluídos. Quando os trabalhos de gestão estão terminados, as AUs são libertadas para que os trabalhos em fila de trabalhos sejam executados.

Para alterar o número de AUs para a sua conta Data Lake Analytics:

1. No portal Azure, aceda à sua conta Data Lake Analytics.
2. Clique **em Limites e políticas.**
3. Em **AUs máximos,** mova o slider para selecionar um valor, ou introduza o valor na caixa de texto.
4. Clique em **Guardar**.

   > [!NOTE]
   > Se precisar de mais do que as AUs predefinidos (250) no portal, clique em **Help+Support** para submeter um pedido de suporte. O número de AUs disponíveis na sua conta Data Lake Analytics pode ser aumentado.

## <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Número máximo de empregos que podem ser executados simultaneamente

Esta política limita o número de postos de trabalho que podem ser executados simultaneamente. Por predefinição, este valor é definido para 20. Se o seu Data Lake Analytics tiver AUs disponíveis, estão previstos novos postos de trabalho imediatamente até que o número total de postos de trabalho em execução atinja o valor desta política. Quando se atinge o número máximo de postos de trabalho que podem ser executados simultaneamente, os postos de trabalho subsequentes são postos de trabalho em ordem prioritária até que um ou mais postos de trabalho em funcionamento estejam concluídos (dependendo dos 20 ºC disponíveis).

Para alterar o número de postos de trabalho que podem ser executados simultaneamente:

1. No portal Azure, aceda à sua conta Data Lake Analytics.
2. Clique **em Limites e políticas.**
3. Em **Número Máximo de Trabalhos em Execução,** mova o slider para selecionar um valor ou introduza o valor na caixa de texto.
4. Clique em **Guardar**.

   > [!NOTE]
   > Se precisar de executar mais do que o número de empregos predefinido (20) no portal, clique em **Help+Support** para submeter um pedido de apoio. O número de empregos que podem ser executados simultaneamente na sua conta Data Lake Analytics pode ser aumentado.

## <a name="how-long-to-keep-job-metadata-and-resources"></a>Quanto tempo para manter metadados e recursos de emprego

Quando os seus utilizadores gerem empregos U-SQL, o serviço Data Lake Analytics mantém todos os ficheiros relacionados. Estes ficheiros incluem o script U-SQL, os ficheiros DLL referenciados no script U-SQL, recursos compilados e estatísticas. Os ficheiros encontram-se na /sistema/pasta da conta de armazenamento padrão do Azure Data Lake. Esta política controla quanto tempo estes recursos são armazenados antes de serem automaticamente eliminados (o padrão é de 30 dias). Podes usar estes ficheiros para depurar, e para afinar o desempenho de empregos que irás repetir no futuro.

Para alterar quanto tempo para manter metadados e recursos de emprego:

1. No portal Azure, aceda à sua conta Data Lake Analytics.
2. Clique **em Limites e políticas.**
3. Em **Dias para Reter Consultas de Trabalho,** mova o slider para selecionar um valor, ou introduza o valor na caixa de texto.  
4. Clique em **Guardar**.

## <a name="job-level-policies"></a>Políticas de nível de emprego

As políticas de nível de trabalho permitem-lhe controlar as AUs máximas e a máxima prioridade que os utilizadores individuais (ou membros de grupos de segurança específicos) podem definir em postos de trabalho que submetam. Esta política permite controlar os custos incorridos pelos utilizadores. Também permite controlar o efeito que os empregos programados podem ter em empregos de produção de alta prioridade que estão a funcionar na mesma conta Data Lake Analytics.

Data Lake Analytics tem duas políticas que pode definir ao nível do trabalho:

- **Limite de AU por trabalho**: Os utilizadores só podem submeter empregos que tenham até este número de IA. Por padrão, este limite é o mesmo que o limite máximo de AU para a conta.

- **Prioridade**: Os utilizadores só podem submeter empregos que tenham uma prioridade inferior ou igual a este valor. Um número mais alto indica uma prioridade menor. Por padrão, este limite é definido para 1, que é a maior prioridade possível.

Há uma política de incumprimento definida em cada conta. A política por defeito aplica-se a todos os utilizadores da conta. Pode criar políticas adicionais para utilizadores e grupos específicos.

> [!NOTE]
> As políticas ao nível da conta e as políticas de nível de emprego aplicam-se simultaneamente.

## <a name="add-a-policy-for-a-specific-user-or-group"></a>Adicionar uma política para um utilizador ou grupo específico

1. No portal Azure, aceda à sua conta Data Lake Analytics.

2. Clique **em Limites e políticas.**

3. Nos **limites de submissão de** emprego, clique no botão **Adicionar Política.** Em seguida, selecione ou introduza as seguintes definições:

   1. **Nome da Política de Cálculo**: Insira um nome de política, para lembrá-lo do propósito da apólice.

   2. **Selecione Utilizador ou Grupo**: Selecione o utilizador ou grupo a que esta política se aplica.

   3. **Definir o limite de AU de trabalho**: Definir o limite de AU que se aplica ao utilizador ou grupo selecionado.

   4. **Definir o limite de prioridade**: Definir o limite de prioridade que se aplica ao utilizador ou grupo selecionado.

4. Clique em **OK**.

5. A nova política está listada na tabela de política **predefinido,** nos **limites de submissão de emprego.**

## <a name="delete-or-edit-an-existing-policy"></a>Eliminar ou editar uma política existente

1. No portal Azure, aceda à sua conta Data Lake Analytics.

2. Clique **em Limites e políticas.**

3. Nos **limites de submissão de emprego,** encontre a política que pretende editar.

4. Para ver as opções **Eliminar** e **Editar,** na coluna mais à direita da tabela, clique em `...` .## Recursos adicionais para políticas de emprego

- [Publicação de blog de visão geral da política](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-overview)
- [Post de blog de políticas de nível de conta](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy)
- [Post de blog de políticas de nível de emprego](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy)

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Começar com data lake analytics usando o portal Azure](data-lake-analytics-get-started-portal.md)
- [Gerir a Azure Data Lake Analytics utilizando a Azure PowerShell](data-lake-analytics-manage-use-powershell.md)