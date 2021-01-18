---
title: Ligue a Plataforma Forense da Rede WireX (NFP) a Azure Sentinel | Microsoft Docs
description: Aprenda a utilizar o conector de dados NFP da WireX Systems para puxar os registos do WireX NFP para o Azure Sentinel. Ver dados do WireX NFP em livros de trabalho, criar alertas e melhorar a investigação.
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
ms.openlocfilehash: 9029b945eabd05b34306393b513e26ee9c1563f2
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541575"
---
# <a name="connect-your-wirex-network-forensics-platform-nfp-appliance-to-azure-sentinel"></a>Ligue o seu aparelho da Plataforma Forense da Rede WireX (NFP) ao Azure Sentinel

> [!IMPORTANT]
> O conector NFP dos sistemas WireX está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar o seu aparelho da WireX Systems Network Forensics Platform (NFP) ao Azure Sentinel. O conector de dados WireX NFP permite-lhe ligar facilmente os seus registos NFP com o Azure Sentinel, para que possa ver os dados nos livros, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. 

> [!NOTE] 
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no seu espaço de trabalho Azure Sentinel.

- Deve ter lido permissões para partilhar chaves para o espaço de trabalho.

## <a name="send-wirex-nfp-logs-to-azure-sentinel"></a>Enviar registos de NFP da WireX para Azure Sentinel

Para obter os seus logins no Azure Sentinel, configuure o seu aparelho NFP da WireX Systems para enviar mensagens Syslog em formato CEF para um servidor de reencaminhamento de registo baseado em Linux (executando rsyslog ou syslog-ng). Este servidor terá o agente Log Analytics instalado nele, e o agente encaminha os registos para o seu espaço de trabalho Azure Sentinel.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. A partir da galeria de **conectores de dados,** selecione **a Plataforma Forense da Rede WireX (Pré-visualização)** e, em seguida, Abra a página do **conector**.

1. Siga as instruções no separador **Instruções,** na **Configuração:**

    1. **1. Configuração do agente Linux Syslog** - Faça este passo se ainda não tiver um reencaminhador de log em funcionamento, ou se precisar de outro. Ver [PASSO 1: Desloque o reencaminhador](connect-cef-agent.md) de registo na documentação do Sentinela Azure para obter instruções e explicações mais detalhadas.

    1. **2. Forward Common Event Format (CEF) regista para o agente Syslog** - Contacte o [suporte da WireX](https://wirexsystems.com/contact-us/) para a configuração adequada da sua solução WireX NFP. Esta configuração deve incluir os seguintes elementos:
        - Destino de registo - o nome de anfitrião e/ou endereço IP do seu servidor de reencaminhamento de registo
        - Protocolo e porta – TCP 514 (se recomendado de outra forma, certifique-se de fazer a alteração paralela no daemon do syslog no seu servidor de reencaminhamento de log)
        - Formato de registo – CEF
        - Tipos de registos - todos recomendados pela WireX

    1. **3. Validar a ligação** - Verificar a ingestão de dados copiando o comando na página do conector e executando-o no seu reencaminhador de registo. Ver [PASSO 3: Validar a conectividade](connect-cef-verify.md) na documentação do Sentinela Azure para obter instruções e explicações mais detalhadas.

        Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** sob a secção **Azure Sentinel,** na tabela *CommonSecurityLog.*

Para consultar os dados do WireX NFP no Log Analytics, copie o seguinte na janela de consulta, aplicando outros filtros à medida que escolher:

```kusto
CommonSecurityLog 
| where DeviceVendor == "WireX"
```

Consulte o separador **passos seguintes** na página do conector para obter mais amostras de consulta.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o NFP da WireX Systems ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.