---
title: Gerenciar políticas de conta de Azure Data Lake Analytics
description: Saiba como usar políticas de conta para controlar o uso de uma conta de Data Lake Analytics, como o máximo de AUs e máximo de trabalhos.
services: data-lake-analytics
ms.service: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 4689714073047e383a53a04bd0069a8a27afdf9d
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966440"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Gerenciar Azure Data Lake Analytics usando políticas de conta

As políticas de conta ajudam a controlar como os recursos de uma conta de Azure Data Lake Analytics são usados. Essas políticas permitem controlar o custo do uso de Azure Data Lake Analytics. Por exemplo, com essas políticas, você pode evitar picos de custo inesperados limitando o número de AUs que a conta pode usar simultaneamente.

## <a name="account-level-policies"></a>Políticas de nível de conta

Essas políticas se aplicam a todos os trabalhos em uma conta de Data Lake Analytics.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Número máximo de AUs em uma conta de Data Lake Analytics
Uma política controla o número total de unidades de análise (AUs) que sua conta de Data Lake Analytics pode usar. Por padrão, o valor é definido como 250. Por exemplo, se esse valor for definido como 250 AUs, você poderá ter um trabalho em execução com 250 AUs atribuído a ele ou 10 trabalhos em execução com 25 AUs cada. Trabalhos adicionais enviados são enfileirados até que os trabalhos em execução sejam concluídos. Quando os trabalhos em execução são concluídos, a AUs é liberada para que os trabalhos em fila sejam executados.

Para alterar o número de AUs para sua conta de Data Lake Analytics:

1. Na portal do Azure, vá para sua conta do Data Lake Analytics.
2. Clique em **limites e políticas**.
3. Em **AUS máximo**, mova o controle deslizante para selecionar um valor ou insira o valor na caixa de texto. 
4. Clique em **Guardar**.

> [!NOTE]
> Se você precisar de mais do que o padrão (250) AUs, no portal, clique em **ajuda + suporte** para enviar uma solicitação de suporte. O número de AUs disponíveis em sua conta de Data Lake Analytics pode ser aumentado.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Número máximo de trabalhos que podem ser executados simultaneamente
Essa política limita o número de trabalhos que podem ser executados simultaneamente. Por padrão, esse valor é definido como 20. Se o seu Data Lake Analytics tiver a AUs disponível, novos trabalhos serão agendados para serem executados imediatamente até que o número total de trabalhos em execução atinja o valor dessa política. Quando você atinge o número máximo de trabalhos que podem ser executados simultaneamente, os trabalhos subsequentes são enfileirados em ordem de prioridade até que um ou mais trabalhos em execução sejam concluídos (dependendo da AUs disponível).

Para alterar o número de trabalhos que podem ser executados simultaneamente:

1. Na portal do Azure, vá para sua conta do Data Lake Analytics.
2. Clique em **limites e políticas**.
3. Em **número máximo de trabalhos em execução**, mova o controle deslizante para selecionar um valor ou insira o valor na caixa de texto. 
4. Clique em **Guardar**.

> [!NOTE]
> Se você precisar executar mais do que o número de trabalhos padrão (20), no portal, clique em **ajuda + suporte** para enviar uma solicitação de suporte. O número de trabalhos que podem ser executados simultaneamente em sua conta de Data Lake Analytics pode ser aumentado.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Por quanto tempo manter os metadados e recursos do trabalho 
Quando os usuários executam trabalhos do U-SQL, o serviço de Data Lake Analytics mantém todos os arquivos relacionados. Esses arquivos incluem o script U-SQL, os arquivos DLL referenciados no script U-SQL, recursos compilados e estatísticas. Os arquivos estão na pasta/System/da conta de Azure Data Lake Storage padrão. Essa política controla por quanto tempo esses recursos são armazenados antes de serem excluídos automaticamente (o padrão é 30 dias). Você pode usar esses arquivos para depuração e para o ajuste de desempenho de trabalhos que serão executados novamente no futuro.

Para alterar por quanto tempo manter os metadados e os recursos do trabalho:

1. Na portal do Azure, vá para sua conta do Data Lake Analytics.
2. Clique em **limites e políticas**.
3. Em **dias para reter consultas de trabalho**, mova o controle deslizante para selecionar um valor ou insira o valor na caixa de texto.  
4. Clique em **Guardar**.

## <a name="job-level-policies"></a>Políticas de nível de trabalho

As políticas de nível de trabalho permitem que você controle o máximo de AUs e a prioridade máxima que os usuários individuais (ou membros de grupos de segurança específicos) podem definir em trabalhos que eles enviam. Essa política permite controlar os custos incorridos pelos usuários. Ele também permite que você controle o efeito que os trabalhos agendados podem ter em trabalhos de produção de alta prioridade em execução na mesma conta de Data Lake Analytics.

Data Lake Analytics tem duas políticas que podem ser definidas no nível do trabalho:

* **Limite de au por trabalho**: os usuários só podem enviar trabalhos que tenham até esse número de AUS. Por padrão, esse limite é o mesmo que o limite máximo de AU para a conta.
* **Prioridade**: os usuários só podem enviar trabalhos que tenham uma prioridade menor ou igual a esse valor. Um número mais alto indica uma prioridade mais baixa. Por padrão, esse limite é definido como 1, que é a prioridade mais alta possível.

Há uma política padrão definida em cada conta. A política padrão se aplica a todos os usuários da conta. Você pode criar políticas adicionais para usuários e grupos específicos. 

> [!NOTE]
> Políticas de nível de conta e políticas de nível de trabalho se aplicam simultaneamente.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Adicionar uma política para um usuário ou grupo específico

1. Na portal do Azure, vá para sua conta do Data Lake Analytics.
2. Clique em **limites e políticas**.
3. Em **limites de envio do trabalho**, clique no botão **Adicionar política** . Em seguida, selecione ou insira as seguintes configurações:
    1. **Nome da política de computação**: Insira um nome de política para lembrá-lo da finalidade da política.
    2. **Selecionar usuário ou grupo**: selecione o usuário ou grupo ao qual esta política se aplica.
    3. **Definir o limite de au do trabalho**: defina o limite de au que se aplica ao usuário ou grupo selecionado.
    4. **Definir o limite de prioridade**: defina o limite de prioridade que se aplica ao usuário ou grupo selecionado.

4. Clique em **OK**.

5. A nova política é listada na tabela de política **padrão** , em **limites de envio do trabalho**. 

### <a name="delete-or-edit-an-existing-policy"></a>Excluir ou editar uma política existente

1. Na portal do Azure, vá para sua conta do Data Lake Analytics.
2. Clique em **limites e políticas**.
3. Em **limites de envio de trabalho**, localize a política que você deseja editar.
4.  Para ver as opções **excluir** e **Editar** , na coluna mais à direita da tabela, clique em `...`.

## <a name="additional-resources-for-job-policies"></a>Recursos adicionais para políticas de trabalho
* [Postagem de blog de visão geral de política](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Postagem no blog de políticas no nível da conta](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Postagem no blog de políticas de nível de trabalho](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerenciar Azure Data Lake Analytics usando Azure PowerShell](data-lake-analytics-manage-use-powershell.md)