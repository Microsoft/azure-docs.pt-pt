---
title: Ligue os dados padrão de endpoint de nuvem preta de carbono VMware ao Azure Sentinel/ Microsoft Docs
description: Saiba como ligar os dados padrão de endpoint de nuvem preta de carbono VMware ao Azure Sentinel.
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
ms.openlocfilehash: e13b251c9bd95a5b52d63d8ea1bbf265c9c46fd8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023879"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>Ligue o seu VMware Carbon Black Cloud Endpoint Standard ao Azure Sentinel com a função Azure

> [!IMPORTANT]
> O conector de dados VMware Carbon Black Cloud Endpoint Standard em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O conector VMware Carbon Black Cloud Endpoint Standard permite-lhe ligar facilmente todos os registos de solução de segurança [VMware Carbon Black Endpoint Standard](https://www.carbonblack.com/products/endpoint-standard/) com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. A integração entre vMware Carbon Black e Azure Sentinel faz uso de Funções Azure para retirar dados de registo usando a API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>Configure e ligue VMware Carbon Black Cloud Endpoint Standard

As Funções Azure podem integrar e puxar eventos e troncos diretamente do VMware Carbon Black Cloud Endpoint Standard, e encaminhá-los para Azure Sentinel.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione o conector **VMware Carbon Black.**
2. Selecione **Abrir a página do conector**.
3. Siga as instruções na página **VMware Carbon Black.**


## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics sob as **tabelas CarbonBlackAuditLogs_CL**, **CarbonBlackNotifications_CL** e ****CarbonBlackEvents_CL*********************

## <a name="validate-connectivity"></a>Validar conectividade
Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 


## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o VMware Carbon Black Cloud Endpoint Standard ao Azure Sentinel usando Apps de Função Azure. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.

