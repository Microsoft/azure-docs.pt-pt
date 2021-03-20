---
title: Ligue os alertas de segurança da Orca ao Azure Sentinel| Microsoft Docs
description: Saiba como ligar os dados de alerta de Segurança orca ao Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação.
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
ms.openlocfilehash: 18a50e3d06135f2c17b7e7c5b2969be33f408c62
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100093085"
---
# <a name="connect-your-orca-security-alerts-to-azure-sentinel"></a>Ligue os seus alertas de Segurança Orca ao Azure Sentinel 

> [!IMPORTANT]
> O conector de alerta de Segurança Orca em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O conector de alertas orca security permite-lhe trazer facilmente os alertas de segurança da [Sua Orca](https://orca.security/) para o Azure Sentinel, para que possa vê-los em livros de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. A integração entre os alertas de Segurança Orca e a Azure Sentinel faz uso da REST API.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-orca-security-alerts"></a>Configurar e ligar alertas de segurança da Orca

Os alertas de segurança da Orca podem integrar e exportar registos diretamente para a Azure Sentinel.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione **alertas de Segurança Orca** e, em seguida, **abra a página do conector**.

2. Consulte https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration a integração completa da plataforma Orca.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics em **CustomLogs** na tabela **OrcaAlerts_CL.**
Para utilizar o esquema relevante no Log Analytics para os alertas orca, procure `OrcaAlerts_CL` .

## <a name="validate-connectivity"></a>Validar conectividade
Pode demorar até que os seus registos comecem a aparecer no Log Analytics. 


## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os alertas de segurança da Orca ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.

