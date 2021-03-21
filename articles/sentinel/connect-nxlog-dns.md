---
title: Ligue os dados de registos DNS do NXLog Windows ao Azure Sentinel | Microsoft Docs
description: Saiba como utilizar o conector de dados NXLog DNS Logs para puxar eventos DNS do Windows para o Azure Sentinel. Veja os dados dns do Windows nos livros, crie alertas e melhore a investigação.
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
ms.openlocfilehash: 880aad438d98605d11e5a2a7c314d89bd8beb5c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101747079"
---
# <a name="connect-your-nxlog-windows-dns-logs-to-azure-sentinel"></a>Ligue os seus Registos DNS do NXLog Windows ao Azure Sentinel

> [!IMPORTANT]
> O conector NXLog DNS Logs encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

O conector [NXLog DNS Logs](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html) permite-lhe exportar facilmente todos os seus eventos do Windows DNS Server para OZure Sentinel em tempo real, dando-lhe informações sobre a atividade do servidor de nomes de domínio em toda a sua organização. Emprega rastreio de eventos de alto desempenho [para windows (ETW)](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html#dns_windows_etw) para recolher eventos de Auditoria e DNS Server analíticos em tempo real e suporta o enriquecimento de eventos com campos personalizados. A integração entre os Registos DNS NXLog e o Azure Sentinel é facilitada através da REST API.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-nxlog-dns-logs"></a>Configure e ligue registos DNS do NXLog

O NXLog pode ser configurado para enviar eventos em formato JSON diretamente para Azure Sentinel.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione o conector **de registos NXLog DNS.**

1. Selecione **Abrir a página do conector**.

1. Siga as instruções passo a passo no tópico de integração do Guia do *Utilizador NXLog,* [o Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) para configurar o encaminhamento através da REST API.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Registos** sob a secção **Registos Personalizados,** na tabela *DNS_Logs_CL.*

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a usar o NXLog para ingerir registos DNS do Windows no Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
