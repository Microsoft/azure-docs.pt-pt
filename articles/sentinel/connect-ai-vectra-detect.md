---
title: Ligar dados da AI Vectra Detect ao Azure Sentinel Microsoft Docs
description: Saiba como ligar os dados de deteção de AI Vectra ao Azure Sentinel.
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: 964bab2b81f6ce69e77fd69cab55f6d1894e4edb
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85368466"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>Ligue a AI Vectra Detect a Azure Sentinel

Este artigo explica como ligar o seu aparelho [AI Vectra Detect](https://www.vectra.ai/product/cognito-detect) ao Azure Sentinel. O conector de dados AI Vectra Detect permite-lhe facilmente trazer os seus dados de AI Vectra Detect para o Azure Sentinel, para que possa vê-lo em livros de trabalho, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a investigação.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-your-ai-vectra-detect-appliance-to-send-cef-messages"></a>Configure o seu aparelho AI Vectra Detect para enviar mensagens CEF  

Configure a AI Vectra Detect para encaminhar mensagens Syslog formatadas por CEF para o seu espaço de trabalho Log Analytics através do reencaminhador Syslog que criou no [PASSO 1: Desdobre o reencaminhador de registos](connect-cef-agent.md).

1. A partir da interface Vectra, navegue até Definições > notificações e escolha a configuração Edit Syslog. Siga as instruções abaixo para configurar a ligação:

    - Adicione um novo Destino (o nome de anfitrião do [reencaminhador](connect-cef-agent.md)de registos)
    - Definir o Porto como **514**
    - Definir o Protocolo como **UDP**
    - Definir o formato para **CEF**
    - Definir tipos de registo (selecione todos os tipos de registo disponíveis)
    - Clique em **Guardar**

2. Pode clicar no botão **Teste** para forçar o envio de alguns eventos de teste para o reencaminhador de registo.

3. Para utilizar o esquema relevante no Log Analytics para os eventos AI Vectra Detect, procure o **CommonSecurityLog**.

4. Continuar a [PASSO 3: Validar a conectividade](connect-cef-verify.md).

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar os aparelhos AI Vectra Detect ao Azure Sentinel. Para tirar o máximo partido das capacidades incorporadas neste conector de dados, clique no separador **passos seguintes** na página do conector de dados. Lá você encontrará algumas consultas de amostras prontas para que você possa começar a encontrar informações úteis.

Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
