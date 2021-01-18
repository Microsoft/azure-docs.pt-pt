---
title: Ligue melhor a defesa da ameaça móvel (MTD) a Azure Sentinel | Microsoft Docs
description: Saiba como utilizar o conector de dados BETTER Mobile Threat Defense (MTD) para puxar os registos MTD para o Azure Sentinel. Ver dados de MTD em livros de trabalho, criar alertas e melhorar a investigação.
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
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 4828e31b9d15f101740c158ee62c90c95673c9a7
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541563"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>Ligue a sua melhor defesa de ameaças móveis (MTD) a Azure Sentinel

> [!IMPORTANT]
> O conector better mobile threat defense (MTD) está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

O conector BETTER Mobile Threat Defense (MTD) permite-lhe ligar facilmente todos os seus registos de soluções de segurança MELHOR MTD com o seu Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. A integração entre a BETTER Mobile Threat Defense e a Azure Sentinel faz uso da REST API.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-better-mobile-threat-defense"></a>Configure e ligue melhor defesa de ameaças móveis

O MELHOR MTD pode integrar e exportar registos diretamente para a Azure Sentinel.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Na galeria de **conectores de dados,** selecione **BETTER Mobile Threat Defense (MTD) (Preview)** e, em seguida, Abra a página do **conector**.

1. Siga os passos na página do conector e [nesta página a partir da Melhor Documentação MTD](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration) para finalizar a integração na Melhor Consola MTD.

    Quando solicitado para introduzir os valores de ID e **Chave Primária** do **Espaço de Trabalho,** copie-os a partir da página do conector Azure Sentinel e cole-os na configuração MELHOR MTD.

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{ID do espaço de trabalho e chave primária}":::

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** na secção **PersonalLogs,** numa ou mais das seguintes tabelas:
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

Para consultar os registos DE MTD melhores em regras de análise, consultas de caça ou em qualquer outro lugar em Azure Sentinel, insira um dos nomes de tabela acima no topo da janela de consulta.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics. 

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar melhor defesa de ameaças móveis (MTD) a Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
