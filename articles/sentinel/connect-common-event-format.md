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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: 51e6c74a8b80b94ca552645cfbb76bd4e162a62b
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650065"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Ligue a sua solução externa utilizando o Formato Comum de Eventos


Quando liga uma solução externa que envia mensagens CEF, existem três passos para a ligação com o Azure Sentinel:

PASSO 1: [Ligue o CEF implantando o agente](connect-cef-agent.md) STEP 2: Executar [passos específicos da solução](connect-cef-solution-config.md) PASSO 3: [Verificar conectividade](connect-cef-verify.md)

Este artigo descreve como a ligação funciona, fornece pré-requisitos e dá-lhe os passos para implantar o agente em soluções de segurança que enviam mensagens common Event Format (CEF) em cima do Syslog. 

> [!NOTE] 
> Os dados são armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

Para fazer esta ligação, é necessário colocar um agente numa máquina Linux dedicada (VM ou no local) para apoiar a comunicação entre o aparelho e o Azure Sentinel. O diagrama seguinte descreve a configuração no caso de um Linux VM em Azure.

 ![CEF em Azure](./media/connect-cef/cef-syslog-azure.png)

Alternativamente, esta configuração existirá se utilizar um VM noutra nuvem ou uma máquina no local. 

 ![CEF nas instalações](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Considerações de segurança

Certifique-se de configurar a segurança da máquina de acordo com a política de segurança da sua organização. Por exemplo, pode configurar a sua rede para alinhar com a sua política de segurança da rede corporativa e alterar as portas e protocolos no daemon para se alinhar com os seus requisitos. Pode utilizar as seguintes instruções para melhorar a configuração de segurança da máquina:  [Secure VM in Azure,](../virtual-machines/security-policy.md) [Best Practices for Network security](../security/fundamentals/network-best-practices.md).

Para utilizar a comunicação TLS entre a solução de segurança e a máquina Syslog, terá de configurar o daemon Syslog (rsyslog ou syslog-ng) para comunicar em TLS: [Encriptar o tráfego de syslog com TLS -rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [encriptar as mensagens de registo com TLS -syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que a máquina Linux que utiliza como representante está a executar um dos seguintes sistemas operativos:

- 64 bits
  - CentOS 6 e 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 e 7
  - Red Hat Enterprise Linux Server 6 e 7
  - Debian GNU/Linux 8 e 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32 bits
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
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
    - Certifique-se de que tem Python a funcionar na sua máquina



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos CEF ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)

