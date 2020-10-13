---
title: Ligue os dados do CEF à pré-visualização do Azure Sentinel. Microsoft Docs
description: Ligue uma solução externa que envia mensagens Common Event Format (CEF) ao Azure Sentinel utilizando uma máquina Linux como procuração.
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
ms.openlocfilehash: d63893ab219854a270652da38c474e3ccad83abc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91630513"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Ligue a sua solução externa utilizando o Formato Comum de Eventos

Quando liga uma solução externa que envia mensagens CEF, existem três passos para a ligação com o Azure Sentinel:

PASSO 1: [Ligue o CEF implantando um reencaminhador Syslog/CEF](connect-cef-agent.md) PASSO 2: [Executar passos específicos da solução](connect-cef-solution-config.md) PASSO 3: [Verificar conectividade](connect-cef-verify.md)

Este artigo descreve como a ligação funciona, fornece pré-requisitos e fornece os passos para a implementação do agente em soluções de segurança que enviam mensagens common Event Format (CEF) em cima do Syslog. 

> [!NOTE] 
> Os dados são armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

Para escoar esta ligação, é necessário utilizar um servidor Syslog Forwarder para suportar a comunicação entre o aparelho e o Azure Sentinel.  O servidor é composto por uma máquina Linux dedicada (VM ou no local) com o agente Log Analytics para o Linux instalado. 

O diagrama seguinte descreve a configuração no caso de um Linux VM em Azure:

 ![CEF em Azure](./media/connect-cef/cef-syslog-azure.png)

Alternativamente, esta configuração existirá se utilizar um VM noutra nuvem ou uma máquina no local: 

 ![CEF nas instalações](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Considerações de segurança

Certifique-se de configurar a segurança da máquina de acordo com a política de segurança da sua organização. Por exemplo, pode configurar a sua rede para alinhar com a sua política de segurança da rede corporativa e alterar as portas e protocolos no daemon para se alinhar com os seus requisitos. Pode utilizar as seguintes instruções para melhorar a configuração de segurança da máquina:  [Secure VM in Azure,](../virtual-machines/security-policy.md) [Best Practices for Network security](../security/fundamentals/network-best-practices.md).

Para utilizar a comunicação TLS entre a fonte Syslog e o Syslog Forwarder, terá de configurar o daemon Syslog (rsyslog ou syslog-ng) para comunicar em TLS: [Encriptar o tráfego de syslog com TLS -rsyslog,](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html) [encriptar as mensagens de registo com TLS -syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).
 
## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que a máquina Linux que utiliza como representante está a executar um dos seguintes sistemas operativos:

- 64 bits
  - CentOS 7 e sub-versões, e mais altos (não 6)
  - Amazon Linux 2017.09
  - Oracle Linux 6 e 7
  - Red Hat Enterprise Linux (RHEL) Servidor 7 e sub-versões, e mais alto (não 6)
  - Debian GNU/Linux 8 e 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32 bits
   - CentOS 7
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 7
   - Debian GNU/Linux 8 e 9
   - Ubuntu Linux 14.04 LTS e 16.04 LTS
 
 - Versões Daemon
   - Syslog-ng: 2.1 - 3.22.1
   - Rsyslog: v8
  
 - Syslog RFCs apoiados
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Certifique-se de que a sua máquina também cumpre os seguintes requisitos: 
- Permissões
    - Deve ter permissões elevadas (sudo) na sua máquina. 
- Requisitos de software
    - Certifique-se de que tem Python (2.7 ou superior) a funcionar na sua máquina

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar os aparelhos CEF ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)

