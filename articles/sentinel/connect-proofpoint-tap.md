---
title: Conecte os dados de Proteção de Ataque direcionados para o ponto de prova (TAP) ao Azure Sentinel| Microsoft Docs
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
ms.openlocfilehash: 86018bafaa42eac01e5dccf8da1d290b64e2475c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100092983"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>Ligue o seu Ponto de Revisão TAP ao Azure Sentinel com a Função Azure

> [!IMPORTANT]
> O conector de dados proofpoint TAP em Azure Sentinel encontra-se atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Proofpoint TAP ao Azure Sentinel utilizando apps de função Azure. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
