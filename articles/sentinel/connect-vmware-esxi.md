---
title: Ligue os dados ESXi da VMware ao | do Sentinela Azure Microsoft Docs
description: Saiba como utilizar o conector de dados VMware ESXi para puxar os registos ESXi para o Azure Sentinel. Ver dados ESXi em livros de trabalho, criar alertas e melhorar a investigação.
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
ms.date: 03/01/2021
ms.author: yelevin
ms.openlocfilehash: 3d478a9ac3cf91f3f6815859b8534efff88f07b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700931"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>Ligue o seu VMware ESXi ao Azure Sentinel

> [!IMPORTANT]
> O conector VMware ESXi encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar o seu aparelho VMware ESXi ao Azure Sentinel. O conector de dados VMware ESXi permite-lhe ingerir facilmente os seus registos VMware ESXi no Azure Sentinel, dando-lhe mais informações sobre as atividades ESXi da sua organização e ajudando a melhorar as suas capacidades de Operações de Segurança. A integração entre vMware ESXi e Azure Sentinel faz uso de um servidor Syslog com o agente Log Analytics instalado. Também usa um parser de log personalizado baseado numa função Kusto.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissão no espaço de trabalho do Azure Sentinel.

- A sua solução VMware ESXi deve ser configurada para exportar registos através do Syslog.

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>Enviar registos VMware ESXi para o agente Syslog  

Configure a VMware ESXi para encaminhar mensagens Syslog para o seu espaço de trabalho Azure Sentinel através do agente Syslog.
3. Siga as instruções na página **VMware ESXi.**


1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. A partir da galeria **de conectores de dados,** selecione o conector **VMware ESXi (Preview)** e, em seguida, **Abra a página do conector**.

1. Siga as instruções na página do conector **VMware ESXi:**

    1. Instale e embarque no agente para Linux

        - Escolha um Azure Linux VM ou uma máquina linux não-Azure (física ou virtual).

    1. Configure os registos a recolher

        - Selecione as instalações e as severidades na **configuração** dos agentes do espaço de trabalho .

    1. Configure e ligue o VMware ESXi

        - Siga estas instruções para configurar o VMware ESXi para encaminhar o syslog:
            - [VMware ESXi 3.5 e 4.x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5.0 e superior](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            Para o servidor remoto, utilize o endereço IP da máquina Linux em que instalou o agente Linux.

## <a name="validate-connectivity-and-find-your-data"></a>Validar a conectividade e encontrar os seus dados

Pode demorar até que os seus registos comecem a aparecer no Azure Sentinel. 

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs**, sob a secção *Gestão* de Registos, na tabela *Syslog.*

Este conector de dados depende de um parser baseado numa Função Kusto para funcionar como esperado. [Siga estes passos](https://aka.ms/sentinel-vmwareesxi-parser) para criar o pseudónimo de função **VMwareESXi** Kusto. Em seguida, pode digitar `VMwareESXi` qualquer janela de consulta em Azure Sentinel para consultar os dados.

Consulte o separador **passos seguintes** na página do conector para obter algumas amostras de consulta úteis.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar a VMware ESXi ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
