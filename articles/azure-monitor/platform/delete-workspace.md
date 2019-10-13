---
title: Excluir e restaurar o espaço de trabalho do Azure Log Analytics | Microsoft Docs
description: Saiba como excluir seu espaço de trabalho Log Analytics se você tiver criado um em uma assinatura pessoal ou reestruturar seu modelo de espaço de trabalho.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: magoedte
ms.openlocfilehash: fb6714a52a65ef5efe4725b99acb30cb67af20c3
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299278"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Excluir e restaurar o espaço de trabalho do Azure Log Analytics

Este artigo explica o conceito de exclusão reversível do espaço de trabalho do Azure Log Analytics e como recuperar o espaço de trabalho excluído. 

## <a name="considerations-when-deleting-a-workspace"></a>Considerações ao excluir um espaço de trabalho

Quando você exclui um espaço de trabalho Log Analytics, uma operação de exclusão reversível é executada para permitir a recuperação do espaço de trabalho, incluindo seus dados e agentes conectados dentro de 14 dias, se a exclusão foi acidental ou intencional. Após o período de exclusão reversível, o espaço de trabalho e seus dados são não recuperáveis e são colocados na fila para exclusão permanente dentro de 30 dias.

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

### <a name="delete-workspace-in-azure-portal"></a>Excluir espaço de trabalho no portal do Azure

1. Para entrar, vá para a [portal do Azure](https://portal.azure.com). 
2. No portal do Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log Analytics espaços de trabalho**.
3. Na lista de espaços de trabalho do Log Analytics, selecione um espaço de trabalho e clique em **excluir** na parte superior do painel central.
   opção ![Delete do painel Propriedades do espaço de trabalho @ no__t-1
4. Quando a janela mensagem de confirmação for exibida solicitando que você confirme a exclusão do espaço de trabalho, clique em **Sim**.
   exclusão de @no__t 0Confirm do espaço de trabalho @ no__t-1

## <a name="recover-workspace"></a>Recuperar espaço de trabalho

Se você tiver permissões de colaborador para a assinatura e o grupo de recursos em que o espaço de trabalho foi associado antes da operação de exclusão reversível, poderá recuperá-lo durante seu período de exclusão reversível, incluindo seus dados, configuração e agentes conectados. Após o período de exclusão reversível, o espaço de trabalho é não recuperável e atribuído para exclusão permanente. Os nomes dos espaços de trabalho excluídos são preservados durante o período de exclusão reversível e não podem ser usados durante a tentativa de criar um novo espaço de trabalho.  

Você pode recuperar um espaço de trabalho recriando o espaço de trabalho usando os métodos Create do espaço de trabalho [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) ou [REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) , contanto que essas propriedades sejam preenchidas com os detalhes do espaço de trabalho excluído, incluindo:

* ID de Subscrição
* Nome do grupo de recursos
* Nome do espaço de trabalho
* Região

> [!NOTE]
> * A recuperação do espaço de trabalho não tem suporte no [portal do Azure](https://portal.azure.com). 
> * Recriar um espaço de trabalho durante o período de exclusão reversível dá uma indicação de que esse nome de espaço de trabalho já está em uso. 
> 
