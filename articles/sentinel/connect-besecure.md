---
title: Connect Beyond Security beSECURE dados ao Azure Sentinel ; Microsoft Docs
description: Saiba como utilizar o conector de dados Beyond Security beSECURE para puxar os registos beSECURE no Azure Sentinel. Ver dados do BESECURE nos livros de trabalho, criar alertas e melhorar a investigação.
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
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: f6fd6920fafe4c1080cb5539e4e0222d9d6e18cd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93103026"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Ligue o seu beSECURE beyond Security ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados Beyond Security beSECURE em Azure Sentinel está atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um contrato de nível de serviço. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Além da Segurança, o conector beSECURE permite-lhe ligar facilmente todos os seus registos de solução de segurança beSECURE com o seu Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. A integração entre beSECURE e Azure Sentinel faz uso da REST API.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-besecure"></a>Configurar e ligar o BESECURE

o beSECURE pode integrar-se e exportar registos diretamente para o Azure Sentinel.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione **Beyond Security beSECURE (Preview)** e, em seguida, **Abra a página do conector** .

1. Siga os passos abaixo para configurar a sua solução BESECURE para enviar resultados de digitalização, estado de digitalização e registos de pistas de auditoria para Azure Sentinel.

    **Aceda ao menu Integração:**
    1. Clique na opção menu 'Mais'

    1. Selecione Servidor

    1. Selecione Integração

    1. Ativar o Azure Sentinel

    **Forneça as definições do Azure Sentinel.**
      - Copie os valores de *ID* e *Chave Primária* do Espaço de Trabalho a partir da página do conector Azure Sentinel, cole-os na configuração beSECURE e clique em **Modificar** .

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** na secção **PersonalLogs,** numa ou mais das seguintes tabelas:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Para consultar os registos beSECURE no Log Analytics, insira um dos nomes de tabela acima no topo da janela de consulta.

## <a name="validate-connectivity"></a>Validar conectividade
Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o BESECURE ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
