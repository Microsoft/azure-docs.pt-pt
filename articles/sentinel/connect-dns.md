---
title: Conecte dados do DNS no Azure Sentinel. Microsoft Docs
description: Saiba como ligar dados do DNS no Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 6d43b82ecd13ac5e082d270ee44ce61fef763d2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588319"
---
# <a name="connect-your-domain-name-server"></a>Conecte o seu servidor de nome de domínio

> [!IMPORTANT]
> O conector de dados DNS em Azure Sentinel encontra-se atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Pode ligar qualquer servidor de nome de domínio (DNS) que esteja a funcionar no Windows ao Azure Sentinel. Isto é feito instalando um agente na máquina DNS. Utilizando registos DNS, pode obter informações relacionadas com segurança, desempenho e operações sobre a infraestrutura DNS da sua organização, recolhendo, analisando e correlacionados registos analíticos e de auditoria e outros dados relacionados dos servidores DNS.

Quando ativa a ligação de registo DNS, pode:
- Identifique clientes que tentem resolver nomes de domínio maliciosos
- Identificar registos de recursos estanos
- Identifique frequentemente nomes de domínio e clientes dNS falantes
- Ver carga de pedido em servidores DNS
- Ver falhas dinâmicas de registo dNS

## <a name="connected-sources"></a>Origens ligadas

A tabela seguinte descreve as fontes conectadas que são suportadas por esta solução:

| **Origem ligada** | **Suporte** | **Descrição** |
| --- | --- | --- |
| [Agentes do Windows](../azure-monitor/platform/agent-windows.md) | Sim | A solução recolhe informações dNS de agentes do Windows. |
| [Agentes do Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | Não | A solução não recolhe informações dNS de agentes diretos do Linux. |
| [Grupo de gestão do System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Sim | A solução recolhe informações do DNS de agentes de um grupo de gestão de Gestão de Operações conectados. Não é necessária uma ligação direta do agente do Gestor de Operações ao Monitor Azure. Os dados são encaminhados do grupo de gestão para o espaço de trabalho log Analytics. |
| [Conta de armazenamento azure](../azure-monitor/platform/collect-azure-metrics-logs.md) | Não | O armazenamento azure não é usado pela solução. |

### <a name="data-collection-details"></a>Detalhes da recolha de dados

A solução recolhe o inventário DNS e os dados relacionados com o evento DNS dos servidores DNS onde um agente Log Analytics está instalado. Os dados relacionados com o inventário, como o número de servidores, zonas e registos de recursos dNS, são recolhidos executando os cmdlets DNS PowerShell. Os dados são atualizados uma vez a cada dois dias. Os dados relacionados com o evento são recolhidos em quase tempo real a partir dos [registos analíticos e de auditoria](https://technet.microsoft.com/library/dn800669.aspx#enhanc) fornecidos por registos e diagnósticos dNS melhorados no Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Ligue o seu aparelho DNS

1. No portal Azure Sentinel, selecione **conectores** de dados e escolha o azulejo **DNS (Pré-visualização).**
1. Se as suas máquinas DNS estiverem em Azure:
    1. Clique **em instalar o agente na máquina virtual Do Windows .**
    1. Na lista de **máquinas Virtuais,** selecione a máquina DNS que pretende transmitir para O Sentinel a dispor. Certifique-se de que este é um VM do Windows.
    1. Na janela que se abre para esse VM, clique em **Connect**.  
    1. Clique em **ativar** na janela do **conector DNS.** 

2. Se a sua máquina DNS não for um VM Azure:
    1. Clique **em instalar o agente em máquinas não-Azure**.
    1. Na janela **do agente Direct,** selecione o **agente Descarregamento do Windows (64 bits)** ou **o agente Dowindows (32 bits)**.
    1. Instale o agente na sua máquina DNS. Copie o **ID workspace,** **a chave primária**e a tecla **secundária** e utilize-os quando solicitado durante a instalação.

3. Para utilizar o esquema relevante no Log Analytics para os registos DNS, procure **dnsEvents**.

## <a name="validate"></a>Validação 

No Log Analytics, procure o esquema **DnsEvents** e certifique-se de que existem eventos.

## <a name="troubleshooting"></a>Resolução de problemas

Se as consultas de lookup não aparecerem no Azure Sentinel, siga estes passos para que as consultas sejam exibidas corretamente:
1. LIGUE os [registos do DNS Analytics nos seus servidores](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Certifique-se de que os DNSEvents aparecem na sua lista de recolha de Log Analytics.
3. LIGUE [O Azure DNS Analytics](../azure-monitor/insights/dns-analytics.md).
4. No Azure DNS Analytics, em **Configuração,** altere qualquer uma das definições, guarde-a e mude-a de volta se for necessário e, em seguida, guarde-a novamente.
5. Consulte a análise do Azure DNS para se certificar de que as consultas estão agora a ser apresentadas.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar os aparelhos DNS no local ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
