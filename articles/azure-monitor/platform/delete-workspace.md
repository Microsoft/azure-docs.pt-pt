---
title: Excluir e recuperar o espaço de trabalho do Azure Log Analytics | Microsoft Docs
description: Saiba como excluir seu espaço de trabalho Log Analytics se você tiver criado um em uma assinatura pessoal ou reestruturar seu modelo de espaço de trabalho.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: fabb2524547bd7837d3644d79f0023311ddccdfc
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845548"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Excluir e restaurar o espaço de trabalho do Azure Log Analytics

Este artigo explica o conceito de exclusão reversível do espaço de trabalho do Azure Log Analytics e como recuperar o espaço de trabalho excluído. 

## <a name="considerations-when-deleting-a-workspace"></a>Considerações ao excluir um espaço de trabalho

Quando você exclui um espaço de trabalho Log Analytics, uma operação de exclusão reversível é executada para permitir a recuperação do espaço de trabalho, incluindo seus dados e agentes conectados dentro de 14 dias, se a exclusão foi acidental ou intencional. Após o período de exclusão reversível, o recurso de espaço de trabalho e seus dados são não recuperáveis – seus dados são enfileirados para exclusão permanente e completamente limpos dentro de 30 dias. O nome do espaço de trabalho é ' liberado ' e você pode usá-lo para criar um novo espaço de trabalho.

> [!NOTE]
> Se pretender anular o comportamento de apagar suavemente e eliminar permanentemente o seu espaço de trabalho, siga os passos no espaço de [trabalho Permanente](#Permanent workspace delete).

Você deseja ter cuidado ao excluir um espaço de trabalho porque pode haver dados e configurações importantes que podem afetar negativamente a operação do serviço. Examine quais agentes, soluções e outros serviços e fontes do Azure que armazenam seus dados em Log Analytics, como:

* Soluções de gestão
* Automatização do Azure
* Agentes em execução em máquinas virtuais Windows e Linux
* Agentes em execução em computadores Windows e Linux em seu ambiente
* System Center Operations Manager

A operação de exclusão reversível exclui o recurso de espaço de trabalho e a permissão de qualquer usuário associado é interrompida. Se os usuários estiverem associados a outros espaços de trabalho, eles poderão continuar usando Log Analytics com esses outros espaços de trabalho.

## <a name="soft-delete-behavior"></a>Comportamento de exclusão reversível

A operação de exclusão do espaço de trabalho remove o recurso do Gerenciador de recursos de espaço de trabalho, mas sua configuração e seus dados são mantidos por 14 dias, enquanto dá a aparência de que o espaço de trabalho é excluído. Todos os agentes e grupos de gerenciamento de System Center Operations Manager configurados para relatar ao espaço de trabalho permanecem em um estado órfão durante o período de exclusão reversível. O serviço fornece ainda mais um mecanismo para recuperar o espaço de trabalho excluído, incluindo seus dados e recursos conectados, essencialmente desfazendo a exclusão.

> [!NOTE] 
> As soluções instaladas e os serviços vinculados, como sua conta de automação do Azure, são removidos permanentemente do espaço de trabalho no momento da exclusão e não podem ser recuperados. Eles devem ser reconfigurados após a operação de recuperação para colocar o espaço de trabalho em seu estado configurado anteriormente.

Você pode excluir um espaço de trabalho usando o [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), a [API REST](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)ou na [portal do Azure](https://portal.azure.com).

### <a name="azure-portal"></a>Portal do Azure

1. Para entrar, vá para a [portal do Azure](https://portal.azure.com). 
2. No portal do Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log Analytics espaços de trabalho**.
3. Na lista de espaços de trabalho do Log Analytics, selecione um espaço de trabalho e clique em **excluir** na parte superior do painel central.
   ![Excluir a opção das propriedades do Espaço de Trabalho painel](media/delete-workspace/log-analytics-delete-workspace.png)
4. Quando a janela mensagem de confirmação for exibida solicitando que você confirme a exclusão do espaço de trabalho, clique em **Sim**.
   ![Confirmar a supressão do espaço de trabalho](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Exclusão de espaço de trabalho permanente
O método de exclusão reversível pode não se ajustar em alguns cenários, como desenvolvimento e teste, onde você precisa repetir uma implantação com as mesmas configurações e o nome do espaço de trabalho. Nesses casos, você pode excluir permanentemente seu espaço de trabalho e "substituir" o período de exclusão reversível. A operação de exclusão de espaço de trabalho permanente libera o nome do local de trabalho e você pode criar um novo espaço de trabalho usando o mesmo nome.


> [!IMPORTANT]
> Tenha cuidado ao excluir permanentemente seu espaço de trabalho, pois a operação é irreversível, e seu espaço de trabalho e seus dados não serão recuperáveis.

No momento, a exclusão de espaço de trabalho permanente pode ser executada via API REST.

> [!NOTE]
> Qualquer solicitação de API deve incluir um token de autorização de portador no cabeçalho da solicitação.
>
> Você pode adquirir o token usando:
> - [Registos das aplicações](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Navegue até portal do Azure usando o console do desenvolvedor (F12) no navegador. Examine uma das instâncias do **lote?** para a cadeia de caracteres de autenticação em **cabeçalhos de solicitação**. Isso estará na autorização de padrão *: portador <token>* . Copie e adicione isso à sua chamada à API, conforme mostrado nos exemplos.
> - Navegue até o site de documentação REST do Azure. Pressione **Experimente** em qualquer API, copie o token de portador e adicione-o à sua chamada à API.
Para excluir permanentemente seu espaço de trabalho, use os [espaços de trabalho – excluir]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) chamada à API REST com uma marca de força:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```

## <a name="recover-workspace"></a>Recuperar espaço de trabalho

Se você tiver permissões de colaborador para a assinatura e o grupo de recursos em que o espaço de trabalho foi associado antes da operação de exclusão reversível, poderá recuperá-lo durante seu período de exclusão reversível, incluindo seus dados, configuração e agentes conectados. Após o período de exclusão reversível, o espaço de trabalho é não recuperável e atribuído para exclusão permanente. Os nomes dos espaços de trabalho excluídos são preservados durante o período de exclusão reversível e não podem ser usados durante a tentativa de criar um novo espaço de trabalho.  

Você pode recuperar um espaço de trabalho recriando-o usando o seguinte espaço de trabalho criar métodos: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) ou [API REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) , contanto que as propriedades a seguir sejam preenchidas com os detalhes do espaço de trabalho excluído:

* ID de Subscrição
* Nome do grupo de recursos
* Nome do espaço de trabalho
* Região

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

O espaço de trabalho e todos os seus dados são colocados novamente após a operação de recuperação. Soluções e serviços vinculados foram removidos permanentemente do espaço de trabalho quando ele foi excluído e devem ser reconfigurados para colocar o espaço de trabalho no estado configurado anteriormente. Alguns dos dados podem não estar disponíveis para consulta após a recuperação do espaço de trabalho até que as soluções associadas sejam reinstaladas e seus esquemas sejam adicionados ao espaço de trabalho.

> [!NOTE]
> * A recuperação do espaço de trabalho não tem suporte no [portal do Azure](https://portal.azure.com). 
> * Recriar um espaço de trabalho durante o período de exclusão reversível dá uma indicação de que esse nome de espaço de trabalho já está em uso. 
> 
