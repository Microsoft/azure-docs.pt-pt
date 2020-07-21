---
title: Ligar dados da Proteção de Ataques Direcionados (TAP) ao Azure Sentinel Microsoft Docs
description: Saiba como ligar os dados de Proteção de Ataques Direcionados para o Ponto de Prova (TAP) ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: e4185de879fa6136531e6d4c64908befa1d3bc45
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531976"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>Ligue o seu Ponto de Revisão TAP ao Azure Sentinel com a Função Azure

O conector de proteção de ataque direcionado proofpoint (TAP) permite-lhe ligar facilmente todos os registos da solução de segurança [Proofpoint TAP](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. A integração entre o Proofpoint TAP e o Azure Sentinel faz uso das Funções Azure para retirar dados de registo utilizando a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-proofpoint-tap"></a>Configure e ligue o ponto de pesca TAP

As Funções Azure podem integrar e puxar eventos e troncos diretamente da Proofpoint TAP e encaminhá-los para Azure Sentinel.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione o conector **De ponto de prova TAP.**

1. Selecione **Abrir a página do conector**.

1. Siga as instruções na página **Proofpoint TAP.**

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics sob as tabelas **ProofpointTAPMessagesBlocked_CL**, **ProofpointTAPMessagesDelivered_CL,** **ProofpointTAPClicksPermitted_CL** e **ProofpointTAPClicksBlocked_CL.**

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Próximos passos

Neste documento, aprendeu a ligar o Proofpoint TAP ao Azure Sentinel utilizando apps de função Azure. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
