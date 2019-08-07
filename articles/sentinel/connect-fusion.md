---
title: Habilitar Fusion na visualização do Azure Sentinel | Microsoft Docs
description: Saiba como habilitar o Fusion no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f90bb86632886d8a2ca6c6e8c60d3b79f5d0b9e9
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780447"
---
# <a name="enable-fusion"></a>Ativar a fusão

> [!IMPORTANT]
> O Azure Sentinel está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Machine Learning no Azure Sentinel é integrado diretamente desde o início. Criamos um sistema de forma elaborada com inovações de ML destinadas a tornar os analistas de segurança, cientistas e engenheiros de dados de segurança produtivos. Uma dessas inovações é o Azure Sentinel Fusion criado especialmente para reduzir o alerta fadiga.

O Fusion usa algoritmos de aprendizado de máquina com grafo para correlacionar entre milhões de atividades anormais de fidelidade mais baixa de diferentes produtos, como Azure AD Identity Protection e Microsoft Cloud App Security, para combiná-los em um número gerenciável de incidentes de segurança interessantes.

## <a name="enable-fusion"></a>Ativar a fusão

1. Na portal do Azure, selecione o ícone a ser aberto Cloud Shell.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  Na **página Bem-vindo ao Cloud Shell** Windows que abre abaixo, selecione PowerShell.

3.  Escolha a assinatura na qual você implantou o Azure Sentinel e **crie o armazenamento**.

4. Depois que você estiver autenticado e a unidade do Azure for criada, no prompt de comando, execute os seguintes comandos:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Desabilitar Fusion

Siga o mesmo procedimento mostrado acima e execute o seguinte comando:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Exibir o status da fusão

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Passos Seguintes

Neste documento, você aprendeu a habilitar o Fusion no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).

