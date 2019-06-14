---
title: Eliminar uma área de trabalho do Log Analytics do Azure | Documentos da Microsoft
description: Saiba como eliminar a área de trabalho do Log Analytics, se tiver criado uma numa subscrição de pessoa ou reestruturar seu modelo de área de trabalho.
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
ms.date: 05/07/2018
ms.author: magoedte
ms.openlocfilehash: a6542838acba3143123dc90d96746179a2b4469b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60236099"
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Eliminar uma área de trabalho do Log Analytics do Azure com o portal do Azure
Este artigo mostra como utilizar o portal do Azure para eliminar uma área de trabalho do Log Analytics, poderá já não precisar. 

## <a name="to-delete-a-workspace"></a>Para eliminar uma área de trabalho 
Quando elimina uma área de trabalho do Log Analytics, todos os dados relacionados com a sua área de trabalho é eliminado do serviço dentro de 30 dias.  Quer tenha cuidado ao eliminar uma área de trabalho, porque pode haver dados importantes e de configuração que pode afetar negativamente as operações de serviço. Considere a outros serviços do Azure e origens que armazenam os dados no Log Analytics, tais como:

* Application Insights
* Centro de Segurança do Azure
* Automatização do Azure
* Agentes em execução em máquinas virtuais Windows e Linux
* Agentes de computadores em execução no Windows e Linux no seu ambiente
* System Center Operations Manager
* Soluções de gestão 

Quaisquer agentes e os grupos de gestão do System Center Operations Manager configurados para reportar para a área de trabalho continuam num Estado órfão.  Inventariar os agentes, soluções, e outros serviços do Azure estão integrados com a área de trabalho antes de continuar.   
 
Se for um administrador e existirem vários utilizadores associados à área de trabalho, a associação entre esses utilizadores e a área de trabalho é interrompida. Se os utilizadores estiverem associados a outras áreas de trabalho, podem continuar a utilizar o Log Analytics com essas áreas de trabalho. No entanto, se eles não estão associados a outras áreas de trabalho, em seguida, eles precisam criar uma área de trabalho para utilizar o Log Analytics. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
2. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **áreas de trabalho do Log Analytics**.
3. No painel de subscrições do Log Analytics, selecione uma área de trabalho e, em seguida, clique em **eliminar** da parte superior do painel do meio.<br><br> ![Opção de eliminar a partir do painel de propriedades da área de trabalho](media/delete-workspace/log-analytics-delete-workspace.png)<br>  
4. Quando for apresentada a janela de mensagem de confirmação que lhe pede para confirmar a eliminação da área de trabalho, clique em **Sim**.<br><br> ![Confirmar a eliminação da área de trabalho](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

