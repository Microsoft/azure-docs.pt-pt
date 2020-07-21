---
title: Ligue os dados da Gestão de Vulnerabilidades qualys ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados de Gestão de Vulnerabilidades qualys ao Azure Sentinel.
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
ms.openlocfilehash: 44002a8f4ab3b644e3530ee2d2fc06a7af271fbe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531968"
---
# <a name="connect-your-qualys-vm-to-azure-sentinel-with-azure-function"></a>Ligue o seu VM Qualys ao Azure Sentinel com a Função Azure

O conector Qualys Vulnerability Management (VM) permite-lhe ligar facilmente todos os seus registos de soluções de segurança [Qualys VM](https://www.qualys.com/apps/vulnerability-management/) com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. A integração entre qualys VM e Azure Sentinel faz uso de Funções Azure para retirar dados de registo usando a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-qualys-vm"></a>Configure e ligue Qualys VM

As Funções Azure podem integrar e puxar eventos e troncos diretamente da Qualys VM e encaminhá-los para Azure Sentinel.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione o conector **qualys Vulnerability Management.**

1. Selecione **Abrir a página do conector**.

1. Siga as instruções na página **Qualys Vulnerability Management.**

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics sob a tabela **QualysHostDetection_CL.**

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Próximos passos

Neste documento, aprendeu a ligar qualys VM a Azure Sentinel usando Apps de Função Azure. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
