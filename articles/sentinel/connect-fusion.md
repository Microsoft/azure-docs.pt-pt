---
title: Ativar fusion sentinela na pré-visualização no Azure | Documentos da Microsoft
description: Saiba como ativar fusion no sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 55d569d4a993a725137d7bfab37c113147fe81ef
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242467"
---
# <a name="enable-fusion"></a>Ativar a fusão

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Machine Learning no Azure sentinela está incorporada diretamente a partir do início. Nós criamos o sistema thoughtfully com inovações de ML que tem como objetivo tornar os analistas de segurança, os cientistas de dados de segurança e engenheiros produtivos. Um desse inovação é que Fusion sentinela do Azure criada especialmente para reduzir fadiga alerta.

Fusion utiliza algoritmos de aprendizagem automática com tecnologia de gráfico para correlacionar entre milhões de inferior atividades anómalas de fidelidade de diferentes produtos como o Azure AD Identity Protection e o Microsoft Cloud App Security, ao combiná-los num número gerenciável de casos de segurança interessante.

## <a name="enable-fusion"></a>Ativar a fusão

1. No portal do Azure, selecione o ícone para abrir o Cloud Shell.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  Na **bem-vindo ao Cloud Shell** windows abre-se abaixo, selecione o PowerShell.

3.  Escolha a subscrição em que implementou sentinela do Azure, e **criar armazenamento**.

4. Depois que estiver autenticado e seu disco do Azure baseia-se, no prompt de comando, execute os seguintes comandos:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Desativar fusion

Siga o mesmo procedimento conforme apresentado acima e execute o seguinte comando:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Ver o estado de fusion

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Passos Seguintes

Neste documento, aprendeu a ativar Fusion no sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).

