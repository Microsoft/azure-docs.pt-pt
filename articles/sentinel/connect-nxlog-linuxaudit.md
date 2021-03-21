---
title: Ligue os dados do NXLog LinuxAudit ao | do Azure Sentinel Microsoft Docs
description: Saiba como utilizar o conector de dados NXLog LinuxAudit para puxar os registos LinuxAudit para o Azure Sentinel. Ver dados do LinuxAudit em livros, criar alertas e melhorar a investigação.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 2010b21a3cdb81f2b2aa4180f87857862cd02bf5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700941"
---
# <a name="connect-your-nxlog-linuxaudit-to-azure-sentinel"></a>Ligue o seu NXLog LinuxAudit ao Azure Sentinel

> [!IMPORTANT]
> O conector NXLog LinuxAudit está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

O conector [NXLog LinuxAudit](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) permite-lhe exportar facilmente eventos de segurança Linux para Azure Sentinel em tempo real, dando-lhe uma visão sobre a atividade do servidor de nome de domínio em toda a sua organização. O módulo NXLog LinuxAudit suporta regras de auditoria personalizadas e recolhe registos sem auditado ou qualquer outro software do espaço de utilizador. Os endereços IP e os IDs de grupo/utilizador são resolvidos com os respetivos nomes, tornando os registos [de auditoria do Linux](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html) mais inteligíveis para os analistas de segurança. A integração entre o NXLog LinuxAudit e o Azure Sentinel é facilitada através da REST API.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-nxlog-linuxaudit"></a>Configure e ligue o NXLog LinuxAudit

O NXLog pode ser configurado para enviar eventos em formato JSON diretamente para Azure Sentinel.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione o conector **NXLog LinuxAudit.**

1. Selecione **Abrir a página do conector**.

1. Siga as instruções passo a passo no tópico de integração do Guia do *Utilizador NXLog,* [o Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) para configurar o encaminhamento através da REST API.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Registos** sob a secção **Registos Personalizados,** na tabela *LinuxAudit_CL.*

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a usar o NXLog LinuxAudit para ingerir eventos de segurança linux em Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
