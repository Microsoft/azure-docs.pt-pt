---
title: Eliminar e recuperar o espaço de trabalho do Azure Log Analytics [ Microsoft Docs
description: Saiba como eliminar o seu espaço de trabalho Log Analytics se criou um numa subscrição pessoal ou reestruturar o seu modelo de espaço de trabalho.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: 1dceb3db4572ecdaf504745dba1099a5eccead43
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80395791"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Eliminar e recuperar o espaço de trabalho do Azure Log Analytics

Este artigo explica o conceito de eliminação suave do espaço de trabalho Azure Log Analytics e como recuperar o espaço de trabalho eliminado. 

## <a name="considerations-when-deleting-a-workspace"></a>Considerações ao apagar um espaço de trabalho

Ao eliminar um espaço de trabalho de Log Analytics, é executada uma operação de eliminação suave para permitir a recuperação do espaço de trabalho, incluindo os seus dados e agentes conectados no prazo de 14 dias, quer a eliminação tenha sido acidental ou intencional. Após o período de eliminação suave, o recurso espaço de trabalho e os seus dados não são recuperáveis – os seus dados estão em fila para eliminação permanente e purgados completamente no prazo de 30 dias. O nome do espaço de trabalho é 'lançado' e pode usá-lo para criar um novo espaço de trabalho.

> [!NOTE]
> Se pretender anular o comportamento de apagar suavemente e eliminar permanentemente o seu espaço de trabalho, siga os passos no espaço de [trabalho Permanente](#permanent-workspace-delete).

Deve ter cuidado ao eliminar um espaço de trabalho porque pode haver dados e configurações importantes que podem ter um impacto negativo no seu funcionamento do serviço. Reveja quais os agentes, soluções e outros serviços e fontes azure que armazenam os seus dados no Log Analytics, tais como:

* Soluções de gestão
* Automatização do Azure
* Agentes em execução em máquinas virtuais Windows e Linux
* Agentes que executam computadores Windows e Linux no seu ambiente
* System Center Operations Manager

A operação de eliminação suave elimina o recurso do espaço de trabalho e a permissão de qualquer utilizador associado é quebrada. Se os utilizadores estiverem associados a outros espaços de trabalho, podem continuar a utilizar o Log Analytics com esses outros espaços de trabalho.

## <a name="soft-delete-behavior"></a>Comportamento de eliminação suave

O espaço de trabalho elimina a operação remove o recurso do Gestor de Recursos do Espaço de Trabalho, mas a sua configuração e dados são mantidos durante 14 dias, ao mesmo tempo que dá a aparência de que o espaço de trabalho é eliminado. Quaisquer agentes e grupos de gestão do System Center Operations Manager configurados para reportar ao espaço de trabalho permanecem em estado órfão durante o período de eliminação suave. O serviço fornece ainda um mecanismo para recuperar o espaço de trabalho eliminado, incluindo os seus dados e recursos conectados, essencialmente desfazendo a eliminação.

> [!NOTE] 
> As soluções instaladas e os serviços ligados, como a sua conta Azure Automation, são permanentemente removidos do espaço de trabalho no momento da eliminação e não podem ser recuperados. Estes devem ser reconfigurados após a operação de recuperação para levar o espaço de trabalho ao seu estado previamente configurado.

Pode eliminar um espaço de trabalho utilizando [powerShell,](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0) [REST API,](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)ou no [portal Azure](https://portal.azure.com).

### <a name="azure-portal"></a>Portal do Azure

1. Para entrar, vá ao [portal Azure.](https://portal.azure.com) 
2. No portal Azure, selecione **Todos os serviços.** Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione espaços de **trabalho Log Analytics**.
3. Na lista de espaços de trabalho do Log Analytics, selecione um espaço de trabalho e clique em **Apagar** a partir da parte superior do painel médio.
   ![Eliminar a opção do painel de propriedades do Espaço de Trabalho](media/delete-workspace/log-analytics-delete-workspace.png)
4. Quando a janela da mensagem de confirmação aparecer pedindo-lhe que confirme a eliminação do espaço de trabalho, clique **em Sim**.
   ![Confirmar a supressão do espaço de trabalho](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

### <a name="troubleshooting"></a>Resolução de problemas

Deve ter permissões de 'Log Analytics Contributor' para eliminar o espaço de trabalho do Log Analytics.<br>
Se receber uma mensagem de erro '*Este nome de espaço de trabalho já está em uso*' ao criar um espaço de trabalho, pode ser desde então:
* O nome do espaço de trabalho não está disponível e está a ser usado por alguém da sua organização, ou por outro cliente.
* O espaço de trabalho foi eliminado nos últimos 14 dias e o seu nome manteve-se reservado para o período de eliminação suave. Para anular o soft-delete e eliminar imediatamente o seu espaço de trabalho e criar um novo espaço de trabalho com o mesmo nome, siga estes passos para recuperar primeiro o espaço de trabalho e execute a eliminação permanente:<br>
   1. [Recupere](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace) o seu espaço de trabalho.
   2. [Elimine permanentemente](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete) o seu espaço de trabalho.
   3. Crie um novo espaço de trabalho usando o mesmo nome de espaço de trabalho.


## <a name="permanent-workspace-delete"></a>Espaço de trabalho permanente apaga
O método soft-delete pode não caber em alguns cenários, tais como desenvolvimento e teste, onde você precisa repetir uma implementação com as mesmas configurações e nome do espaço de trabalho. Nesses casos, pode eliminar permanentemente o seu espaço de trabalho e "anular" o período de eliminação suave. O espaço de trabalho permanente elimina a operação liberta o nome do espaço de trabalho e pode criar um novo espaço de trabalho com o mesmo nome.


> [!IMPORTANT]
> Utilize o espaço de trabalho permanente para eliminar a operação com cuidado, uma vez que é irreversível e não poderá recuperar o seu espaço de trabalho e os seus dados.

O espaço de trabalho permanente pode ser atualmente realizado através da API REST.

> [!NOTE]
> Qualquer pedido da API deve incluir um símbolo de autorização do Portador no cabeçalho do pedido.
>
> Pode adquirir o símbolo usando:
> - [Registos de aplicações](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Navegue para o portal Azure utilizando a consola do desenvolvedor (F12) no navegador. Olhar num dos casos do **lote?** **Request Headers** Isto estará na *autorização padrão: <token>Bearer *. Copie e adicione isto à sua chamada API, como mostram os exemplos.
> - Navegue para o site de documentação Azure REST. pressione **Experimente** em qualquer API, copie o token do Portador e adicione-o à sua chamada API.
Para eliminar permanentemente o seu espaço de trabalho, utilize os espaços de [trabalho - Elimine]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) a chamada REST API com uma etiqueta de força:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
Onde 'eyJ0eXAiOiJKV1Qi...' representa o símbolo de autorização completa.

## <a name="recover-workspace"></a>Recuperar espaço de trabalho

Se tiver permissões do Colaborador para o grupo de subscrição e recursos onde o espaço de trabalho estava associado antes da operação de eliminação suave, pode recuperá-lo durante o seu período de eliminação suave, incluindo os seus dados, configuração e agentes conectados. Após o período de eliminação suave, o espaço de trabalho não é recuperável e atribuído para eliminação permanente. Os nomes dos espaços de trabalho eliminados são preservados durante o período de eliminação suave e não podem ser utilizados quando se tenta criar um novo espaço de trabalho.  

Pode recuperar um espaço de trabalho recriando-o utilizando os seguintes métodos de criação do espaço de trabalho: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) ou [REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) desde que as seguintes propriedades sejam povoadas com os detalhes do espaço de trabalho eliminados:

* ID da subscrição
* Nome do Grupo de Recursos
* Nome da área de trabalho
* Região

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

O espaço de trabalho e todos os seus dados são trazidos de volta após a operação de recuperação. As soluções e os serviços ligados foram permanentemente removidos do espaço de trabalho quando este foi eliminado e estes devem ser reconfigurados para levar o espaço de trabalho ao seu estado previamente configurado. Alguns dos dados podem não estar disponíveis para consulta após a recuperação do espaço de trabalho até que as soluções associadas sejam reinstaladas e os seus esquemos sejam adicionados ao espaço de trabalho.

> [!NOTE]
> * A recuperação do espaço de trabalho não é suportada no [portal Azure.](https://portal.azure.com) 
> * A recriação de um espaço de trabalho durante o período de eliminação suave dá uma indicação de que este nome do espaço de trabalho já está a ser utilizado. 
> 
