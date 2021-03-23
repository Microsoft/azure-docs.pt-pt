---
title: Ligue os dados do CEF à pré-visualização do Sentinela Azure| Microsoft Docs
description: Ligue uma solução externa que envia mensagens Common Event Format (CEF) ao Azure Sentinel, utilizando uma máquina Linux como reencaminhador de registos.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 54fd6c0c085c0055f3114fde606f8f7d2f2e055e
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772064"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Ligue a sua solução externa utilizando o Formato Comum de Eventos

Quando liga uma solução externa que envia mensagens CEF, existem três passos para a ligação com o Azure Sentinel:

PASSO 1: [Ligue o CEF implantando um reencaminhador Syslog/CEF](connect-cef-agent.md) PASSO 2: [Executar passos específicos da solução](connect-cef-solution-config.md) PASSO 3: [Verificar conectividade](connect-cef-verify.md)

Este artigo descreve como a ligação funciona, lista pré-requisitos e mostra os passos para implementar um mecanismo de soluções de segurança para enviar mensagens common Event Format (CEF) em cima do Syslog. 

> [!NOTE] 
> Os dados são armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

Para escoar esta ligação, é necessário utilizar um servidor Syslog Forwarder para suportar a comunicação entre o aparelho e o Azure Sentinel.  O servidor é composto por uma máquina Linux dedicada (VM ou no local) com o agente Log Analytics para o Linux instalado. 

O diagrama seguinte descreve a configuração no caso de um Linux VM em Azure:

 ![CEF em Azure](./media/connect-cef/cef-syslog-azure.png)

Alternativamente, esta configuração existirá se utilizar um VM noutra nuvem ou uma máquina no local: 

 ![CEF nas instalações](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Considerações de segurança

Certifique-se de configurar a segurança da máquina de acordo com a política de segurança da sua organização. Por exemplo, pode configurar a sua rede para alinhar com a sua política de segurança da rede corporativa e alterar as portas e protocolos no daemon para se alinhar com os seus requisitos. Pode utilizar as seguintes instruções para melhorar a configuração de segurança da máquina:  [Secure VM in Azure,](../virtual-machines/security-policy.md) [Best Practices for Network security](../security/fundamentals/network-best-practices.md).

Para utilizar a comunicação TLS entre a fonte Syslog e o Syslog Forwarder, terá de configurar o daemon Syslog (rsyslog ou syslog-ng) para comunicar em TLS: [Encriptar o tráfego de syslog com TLS -rsyslog,](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html) [encriptar as mensagens de registo com TLS -syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).
 
## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que a máquina Linux que utiliza como reencaminhador de registos está a executar um dos seguintes sistemas operativos:

- 64 bits
  - CentOS 7 e 8, incluindo versões menores (não 6)
  - Amazon Linux 2017.09
  - Oráculo Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 e 8, incluindo versões menores (não 6)
  - Debian GNU/Linux 8 e 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
  - SUSE Linux Enterprise Server 12, 15

- 32 bits
  - CentOS 7 e 8, incluindo versões menores (não 6)
  - Oráculo Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 e 8, incluindo versões menores (não 6)
  - Debian GNU/Linux 8 e 9
  - Ubuntu Linux 14.04 LTS e 16.04 LTS
 
- Versões Daemon
  - Syslog-ng: 2.1 - 3.22.1
  - Rsyslog: v8
  
- Syslog RFCs apoiados
  - Syslog RFC 3164
  - Syslog RFC 5424
 
Certifique-se de que a sua máquina também cumpre os seguintes requisitos: 

- Capacidade
  - A sua máquina deve ter um mínimo de **4 núcleos CPU e 8 GB de RAM**.

    > [!NOTE]
    > - Uma única máquina de reencaminhamento de log utilizando o daemon **rsyslog** tem uma capacidade suportada de **até 8500 eventos por segundo (EPS) recolhidos.**

- Permissões
  - Deve ter permissões elevadas (sudo) na sua máquina. 

- Requisitos de software
  - Certifique-se de que tem a pitão 2.7 ou 3 a funcionar na sua máquina.

## <a name="next-steps"></a>Passos seguintes

Neste documento, ficou a saber como a Azure Sentinel recolhe registos CEF a partir de soluções de segurança e aparelhos. Para aprender a ligar a sua solução ao Azure Sentinel, consulte os seguintes artigos:

- PASSO 1: [Ligue o CEF implantando um reencaminhador Syslog/CEF](connect-cef-agent.md)
- PASSO 2: [Executar etapas específicas da solução](connect-cef-solution-config.md)
- PASSO 3: [Verificar conectividade](connect-cef-verify.md)

Para saber mais sobre o que fazer com os dados recolhidos no Azure Sentinel, consulte os seguintes artigos:

- Saiba mais sobre [o mapeamento de campo CEF e CommonSecurityLog](cef-name-mapping.md).
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](./tutorial-detect-threats-built-in.md)