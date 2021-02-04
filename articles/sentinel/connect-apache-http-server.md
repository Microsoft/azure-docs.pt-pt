---
title: Ligue o seu servidor Apache HTTP ao Azure Sentinel | Microsoft Docs
description: Aprenda a utilizar o conector Apache HTTP Server para puxar os registos Apache para o Azure Sentinel. Ver dados do Apache em livros de trabalho, criar alertas e melhorar a investigação.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 59ef34f9d6d42277d8e83bf4e0ebb8ee29096f52
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566869"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Ligue o seu Servidor APACHE HTTP ao Azure Sentinel

> [!IMPORTANT]
> O conector do servidor Apache HTTP está atualmente em **PRÉ-VISUALIZAÇÃO**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar o seu Servidor HTTP Apache ao Azure Sentinel. O conector do Servidor Apache HTTP permite-lhe ingerir facilmente os seus registos do Servidor APACHE HTTP ao Azure Sentinel, para que possa visualizar os dados nos livros de trabalho, consultar os mesmos para criar alertas personalizados e incorporá-lo para melhorar a investigação. A integração entre o Servidor HTTP Apache e o Azure Sentinel faz uso do processamento de ficheiros locais pelo agente Log Analytics.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter permissão para escrever no espaço de trabalho do Azure Sentinel.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Configure e integre os registos do Servidor Apache HTTP através do agente Log Analytics

Configurar a localização dos ficheiros do servidor Apache HTTP para encaminhar dados para o seu espaço de trabalho Azure através do agente Log Analytics.
Configure o agente Log Analytics para ler ficheiros de registo do servidor Apache HTTP.

1. Siga as instruções https://httpd.apache.org/docs/2.4/logs.html para configurar a localização dos ficheiros de registo no Servidor HTTP Apache.

1. No menu de navegação Azure Sentinel, selecione **os conectores de dados** e, em seguida, selecione **o Servidor HTTP Apache (Pré-visualização)**.

1. Selecione **Abrir a página do conector**.

1. Siga as instruções na página do Servidor Apache HTTP.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics ao abrigo ApacheHTTPServer_CL.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Servidor HTTP Do Apache ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
