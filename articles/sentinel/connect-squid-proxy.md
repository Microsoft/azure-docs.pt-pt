---
title: Ligue os dados do Proxy de Lulas ao Azure Sentinel| Microsoft Docs
description: Aprenda a usar o conector de dados Squid Proxy para puxar os registos squid proxy em Azure Sentinel. Veja os dados do Squid Proxy nos livros, crie alertas e melhore a investigação.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: b183abf8d42e6f4b1c43db2d87b2650721e0c2a9
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567955"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>Ligue o seu Proxy de Lula a Azure Sentinel

> [!IMPORTANT]
> O conector Squid Proxy está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar o seu aparelho Squid Proxy ao Azure Sentinel. O conector de dados Squid Proxy permite-lhe conectar facilmente os seus registos de Lula com o Azure Sentinel, para que possa ver os dados nos livros, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a investigação. A integração entre Squid Proxy e Azure Sentinel faz uso do Syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissão no espaço de trabalho do Azure Sentinel.

## <a name="forward-squid-proxy-logs-to-the-syslog-agent"></a>Registos de Proxy de Lula para a frente para o agente Syslog  

Configure o Proxy de Lula para encaminhar mensagens Syslog para o seu espaço de trabalho Azure através do agente Syslog.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. A partir da galeria **de conectores de dados,** selecione o conector **Squid Proxy (Preview)** e, em seguida, **Abra a página do conector**.

1. Siga as instruções na página do conector **Squid Proxy:**

    1. Instale e embarque no agente para Linux

        - Escolha um Azure Linux VM ou uma máquina linux não-Azure (física ou virtual).

    1. Configure os registos a recolher

        - Nas definições avançadas do espaço de trabalho, adicione um tipo de registo personalizado, carrefique um ficheiro de amostra e configufique conforme indicado.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Registos**, em **Registos Personalizados,** na `SquidProxy_CL` tabela.

Consulte o separador **passos seguintes** na página do conector para obter algumas consultas úteis de amostras.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics. 

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar squid proxy a Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
