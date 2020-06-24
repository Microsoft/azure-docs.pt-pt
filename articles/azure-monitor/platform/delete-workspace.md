---
title: Apagar e recuperar o espaço de trabalho do Azure Log Analytics Microsoft Docs
description: Saiba como eliminar o seu espaço de trabalho Log Analytics se criou um numa subscrição pessoal ou reestruturar o seu modelo de espaço de trabalho.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 0fad77f4704b9cbd8c2a37e39c09a334b08766ef
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2020
ms.locfileid: "85193549"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Apagar e recuperar o espaço de trabalho do Azure Log Analytics

Este artigo explica o conceito de Azure Log Analytics workspace soft-delete e como recuperar espaço de trabalho apagado.

## <a name="considerations-when-deleting-a-workspace"></a>Considerações ao apagar um espaço de trabalho

Quando elimina um espaço de trabalho Log Analytics, é realizada uma operação de eliminação suave para permitir a recuperação do espaço de trabalho, incluindo os seus dados e agentes ligados no prazo de 14 dias, quer a eliminação tenha sido acidental ou intencional. Após o período de eliminação suave, o recurso do espaço de trabalho e os seus dados não são recuperáveis – os seus dados são postos em fila para eliminação permanente e completamente purgados no prazo de 30 dias. O nome do espaço de trabalho é 'lançado' e pode usá-lo para criar um novo espaço de trabalho.

> [!NOTE]
> Se pretender anular o comportamento de eliminação suave e eliminar permanentemente o seu espaço de trabalho, siga os passos no [espaço de trabalho permanente para eliminar](#permanent-workspace-delete).

Deve ter cuidado ao eliminar um espaço de trabalho porque pode haver dados e configurações importantes que podem ter um impacto negativo no seu funcionamento do serviço. Reveja quais os agentes, soluções e outros serviços e fontes da Azure que armazenam os seus dados no Log Analytics, tais como:

* Soluções de gestão
* Automatização do Azure
* Agentes que correm em máquinas virtuais Windows e Linux
* Agentes que estão a correr em computadores Windows e Linux no seu ambiente
* System Center Operations Manager

A operação de eliminação suave elimina o recurso do espaço de trabalho e a permissão de qualquer utilizadores associados é quebrada. Se os utilizadores estiverem associados a outros espaços de trabalho, então podem continuar a usar o Log Analytics com esses outros espaços de trabalho.

## <a name="soft-delete-behavior"></a>Comportamento de eliminação suave

A operação de eliminação do espaço de trabalho remove o recurso do Gestor de Recursos do espaço de trabalho, mas a sua configuração e dados são mantidos durante 14 dias, ao mesmo tempo que dá a aparência de que o espaço de trabalho é eliminado. Quaisquer grupos de gestão de agentes e gestores de operações do System Center configurados para reportar ao espaço de trabalho permanecem em estado órfão durante o período de eliminação suave. O serviço fornece ainda um mecanismo para a recuperação do espaço de trabalho eliminado, incluindo os seus dados e recursos conectados, essencialmente desfazendo a supressão.

> [!NOTE] 
> As soluções instaladas e os serviços ligados como a sua conta Azure Automation são permanentemente removidos do espaço de trabalho em tempo de eliminação e não podem ser recuperados. Estes devem ser reconfigurados após a operação de recuperação para levar o espaço de trabalho ao seu estado previamente configurado.

Pode eliminar um espaço de trabalho utilizando [o PowerShell,](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0) [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)ou no [portal Azure](https://portal.azure.com).

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. No portal Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log analytics espaços de trabalho**.
3. Na lista de espaços de trabalho do Log Analytics, selecione um espaço de trabalho e, em seguida, clique em **Eliminar** a partir do topo do painel médio.
4. Aparece uma página de confirmação que mostra a ingestão de dados para o espaço de trabalho durante a semana passada. Digite o nome do espaço de trabalho para confirmar e, em seguida, clique em **Eliminar**.

   ![Confirmar a supressão do espaço de trabalho](media/delete-workspace/workspace-delete.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Espaço de trabalho permanente eliminação
O método de eliminação suave pode não se encaixar em alguns cenários, tais como desenvolvimento e testes, onde é necessário repetir uma implantação com as mesmas definições e nome do espaço de trabalho. Nesses casos, pode eliminar permanentemente o seu espaço de trabalho e "sobrepor-se" ao período de eliminação suave. A operação permanente de eliminação do espaço de trabalho liberta o nome do espaço de trabalho e pode criar um novo espaço de trabalho com o mesmo nome.


> [!IMPORTANT]
> Utilize o espaço de trabalho permanente para eliminar com cuidado uma vez que é irreversível e não poderá recuperar o seu espaço de trabalho e os seus dados.

Adicione a etiqueta "força" para eliminar permanentemente o seu espaço de trabalho:

```powershell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name" -Force
```

## <a name="recover-workspace"></a>Recuperar espaço de trabalho
Quando elimina um espaço de trabalho Log Analytics acidental ou intencionalmente, o serviço coloca o espaço de trabalho num estado de eliminação suave, tornando-o inacessível a qualquer operação. O nome do espaço de trabalho eliminado é preservado durante o período de eliminação suave e não pode ser usado para criar um novo espaço de trabalho. Após o período de eliminação suave, o espaço de trabalho não é recuperável, está programado para eliminação permanente e o seu nome é lançado e pode ser usado para criar um novo espaço de trabalho.

Pode recuperar o seu espaço de trabalho durante o período de eliminação suave, incluindo os seus dados, configuração e agentes conectados. É necessário dispor de permissões do Contribuinte para o grupo de subscrição e recursos onde o espaço de trabalho foi localizado antes da operação de eliminação suave. A recuperação do espaço de trabalho é realizada criando um espaço de trabalho Log Analytics com os detalhes do espaço de trabalho eliminado, incluindo:

- ID da subscrição
- Nome do Grupo de Recursos
- Nome da área de trabalho
- Região

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. No portal Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log analytics espaços de trabalho**. Vê a lista de espaços de trabalho que tem no âmbito selecionado.
3. Clique em **Recuperar** no menu superior esquerdo para abrir uma página com espaços de trabalho em estado de eliminação suave que pode ser recuperado.

   ![Recuperar espaço de trabalho](media/delete-workspace/recover-menu.png)

4. Selecione o espaço de trabalho e clique em **Recuperar** para recuperar o espaço de trabalho.

   ![Recuperar espaço de trabalho](media/delete-workspace/recover-workspace.png)


### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

O espaço de trabalho e todos os seus dados são devolvidos após a operação de recuperação. As soluções e os serviços ligados foram permanentemente removidos do espaço de trabalho quando este foi eliminado e estes devem ser reconfigurados para trazer o espaço de trabalho para o seu estado previamente configurado. Alguns dos dados podem não estar disponíveis para consulta após a recuperação do espaço de trabalho até que as soluções associadas sejam reinstaladas e os seus esquemas sejam adicionados ao espaço de trabalho.

> [!NOTE]
> * A recriação de um espaço de trabalho durante o período de eliminação suave dá uma indicação de que este nome do espaço de trabalho já está em uso. 
 
## <a name="troubleshooting"></a>Resolução de problemas
Deve ter pelo menos permissões *do Log Analytics Contributor* para eliminar um espaço de trabalho.<br>
Se receber uma mensagem de erro *Este nome do espaço de trabalho já está em uso* ou em *conflito* ao criar um espaço de trabalho, pode ser desde:
* O nome do espaço de trabalho não está disponível e é usado por alguém da sua organização, ou por outro cliente.
* O espaço de trabalho foi apagado nos últimos 14 dias e o seu nome manteve-se reservado para o período de eliminação suave. Para anular o soft-delete e eliminar permanentemente o seu espaço de trabalho para criar um novo espaço de trabalho com o mesmo nome, siga estes passos para recuperar primeiro o espaço de trabalho e realizar a eliminação permanente:<br>
   1. [Recupere](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace) o seu espaço de trabalho.
   2. [Elimine permanentemente](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete) o seu espaço de trabalho.
   3. Crie um novo espaço de trabalho usando o mesmo nome do espaço de trabalho.
