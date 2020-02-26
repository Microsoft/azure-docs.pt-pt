---
title: Ligue os dados do CEF à Pré-visualização do Azure Sentinel. Microsoft Docs
description: Saiba como ligar dados cef ao Azure Sentinel.
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
ms.openlocfilehash: 8314614616c6b8969832d52fc684d47ba1bf0fe3
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588353"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Conecte a sua solução externa utilizando o formato de evento comum


Quando conecta uma solução externa que envia mensagens CEF, existem três passos para se conectar com o Azure Sentinel:

PASSO 1: [Ligar o CEF implantando o agente](connect-cef-agent.md) STEP 2: Executar [passos específicos da solução](connect-cef-solution-config.md) PASSO 3: [Verificar a conectividade](connect-cef-verify.md)

Este artigo descreve como a ligação funciona, fornece pré-requisitos e dá-lhe os passos para implementar o agente em soluções de segurança que enviam mensagens de Formato De Evento Comum (CEF) em cima do Syslog. 

> [!NOTE] 
> Os dados são armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

Para efetuar esta ligação, é necessário colocar um agente numa máquina Linux dedicada (VM ou no local) para apoiar a comunicação entre o aparelho e o Azure Sentinel. O diagrama seguinte descreve a configuração no caso de um Linux VM em Azure.

 ![CEF em Azure](./media/connect-cef/cef-syslog-azure.png)

Em alternativa, esta configuração existirá se utilizar um VM noutra nuvem ou numa máquina no local. 

 ![CEF nas instalações](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Considerações de segurança

Certifique-se de configurar a segurança da máquina de acordo com a política de segurança da sua organização. Por exemplo, pode configurar a sua rede para alinhar com a sua política de segurança de rede corporativa e alterar as portas e protocolos do daemon para alinhar com os seus requisitos. Pode utilizar as seguintes instruções para melhorar a configuração de segurança da máquina:  [Secure VM in Azure](../virtual-machines/linux/security-policy.md), [As melhores práticas para a segurança da rede](../security/fundamentals/network-best-practices.md).

Para utilizar a comunicação TLS entre a solução de segurança e a máquina Syslog, terá de configurar o daemon Syslog (rsyslog ou syslog-ng) para comunicar em TLS: Encriptar tráfego [syslog com TLS -rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [encriptar mensagens de registo com TLS –syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que a máquina Linux que utiliza como procuração está a funcionar num dos seguintes sistemas operativos:

- 64 bits
  - CentOS 6 e 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 e 7
  - Red Hat Enterprise Linux Server 6 e 7
  - Debian GNU/Linux 8 e 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32 bits
   - Centos 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 e 9
   - Ubuntu Linux 14.04 LTS e 16.04 LTS
 
 - Versões Daemon
   - Syslog-ng: 2.1 - 3.22.1
   - Rsyslog: v8
  
 - Syslog RFCs suportados
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Certifique-se de que a sua máquina também satisfaz os seguintes requisitos: 
- Permissões
    - Deve ter permissões elevadas (sudo) na sua máquina. 
- Requisitos de software
    - Certifique-se de ter Python a funcionar na sua máquina



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)

