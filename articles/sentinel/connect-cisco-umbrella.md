---
title: Ligue a Cisco Umbrella a Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados da Cisco Umbrella para puxar os dados da Umbrella para o Azure Sentinel. Veja os dados da Umbrella nos livros, crie alertas e melhore a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 22796134dae5c345e3f915e47bc1300affb9f60e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566901"
---
# <a name="connect-your-cisco-umbrella-to-azure-sentinel"></a>Ligue o seu Guarda-Chuva Cisco ao Azure Sentinel

> [!IMPORTANT]
> O conector Cisco Umbrella está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

O conector Cisco Umbrella permite-lhe conectar facilmente todos os seus registos de soluções de segurança Cisco Umbrella com o seu Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. A integração entre a Cisco Umbrella e a Azure Sentinel faz uso da REST API.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-cisco-umbrella"></a>Configure e ligue a Cisco Umbrella

A Cisco Umbrella pode integrar e exportar registos diretamente para o Azure Sentinel.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Na galeria **de conectores de dados,** selecione **Cisco Umbrella (Preview)** e, em seguida, **Abra a página do conector**.

1. Siga os passos descritos na secção **de Configuração** da página do conector.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** na secção **PersonalLogs,** numa ou mais das seguintes tabelas:
- `Cisco_Umbrella_dns_CL`
- `Cisco_Umbrella_proxy_CL`
- `Cisco_Umbrella_ip_CL`
- `Cisco_Umbrella_cloudfirewall_CL`

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar os dados da Cisco Umbrella ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
