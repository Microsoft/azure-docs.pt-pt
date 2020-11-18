---
title: Ligue os dados do DNS no Azure Sentinel. Microsoft Docs
description: Aprenda a ligar qualquer servidor de nome de domínio (DNS) em execução no Windows ao Azure Sentinel instalando um agente na máquina DNS.
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
ms.openlocfilehash: 7701fc6d90fd9ebc7ec29f0ffdd7d050c58c036c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655668"
---
# <a name="connect-your-domain-name-server"></a>Ligue o servidor de nome de domínio

> [!IMPORTANT]
> O conector de dados DNS em Azure Sentinel encontra-se atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode ligar qualquer Servidor de Nome de Domínio (DNS) em execução no Windows ao Azure Sentinel. Isto é feito através da instalação de um agente na máquina DNS. Utilizando registos DNS, pode obter informações de segurança, desempenho e operações relacionadas com a infraestrutura de DNS da sua organização, recolhendo, analisando e correlacionando registos de auditoria e auditoria e outros dados relacionados com os servidores DNS.

Quando ativa a ligação de registo DNS, pode:
- Identificar clientes que tentam resolver nomes de domínio malicioso
- Identificar registos de recursos obsoletos
- Identificar nomes de domínio frequentemente questionados e clientes DNS falantes
- Ver carga de pedido nos servidores DNS
- Ver falhas dinâmicas de registo de DNS

## <a name="connected-sources"></a>Origens ligadas

O quadro que se segue descreve as fontes ligadas suportadas por esta solução:

| **Origem ligada** | **Suporte** | **Descrição** |
| --- | --- | --- |
| [Agentes do Windows](../azure-monitor/platform/agent-windows.md) | Sim | A solução recolhe informações sobre DNS de agentes do Windows. |
| [Agentes do Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | Não | A solução não recolhe informações de DNS de agentes linux diretos. |
| [Grupo de gestão do System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Sim | A solução recolhe informações de DNS de agentes de um grupo de gestão de Gestores de Operações conectado. Não é necessária uma ligação direta do agente gestor de operações ao Azure Monitor. Os dados são reencaminhados do grupo de gestão para o espaço de trabalho Log Analytics. |
| [Conta de armazenamento Azure](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) | Não | O armazenamento Azure não é usado pela solução. |

### <a name="data-collection-details"></a>Detalhes da recolha de dados

A solução recolhe o inventário DNS e dados relacionados com eventos DNS dos servidores DNS onde está instalado um agente Do Registo De Análise. Os dados relacionados com o inventário, como o número de servidores, zonas e registos de recursos dns, são recolhidos executando os cmdlets DNS PowerShell. Os dados são atualizados uma vez a cada dois dias. Os dados relacionados com o evento são recolhidos perto de tempo real a partir dos [registos analíticos e de auditoria](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) fornecidos por registos dns melhorados e diagnósticos no Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Ligue o seu aparelho DNS

1. No portal Azure Sentinel, selecione **os conectores de dados** e escolha o azulejo **DNS (Preview).**
1. Se as suas máquinas DNS estiverem em Azure:
    1. Clique **em Instalar o agente na máquina virtual Azure Windows**.
    1. Na lista **de máquinas Virtuais,** selecione a máquina DNS que pretende transmitir para O Azure Sentinel. Certifique-se de que este é um VM Windows.
    1. Na janela que se abre para o VM, clique **em Connect**.  
    1. Clique em **Ativar** na janela do **conector DNS.** 

2. Se a sua máquina DNS não for um Azure VM:
    1. Clique **em Instalar o agente em máquinas não-Azure**.
    1. Na janela **do agente Direct,** selecione **o agente do Windows (64 bit)** ou **o agente do Windows de descarregamento (32 bits).**
    1. Instale o agente na sua máquina DNS. Copie o ID do **espaço de trabalho,** **a tecla primária** e a **tecla secundária** e use-as quando solicitada durante a instalação.

3. Para utilizar o esquema relevante no Log Analytics para os registos DNS, procure **por DnsEvents**.

## <a name="validate"></a>Validação 

No Log Analytics, procure o esquema **DnsEvents** e certifique-se de que existem eventos.

## <a name="troubleshooting"></a>Resolução de problemas

Se as consultas de procuração não aparecerem em Azure Sentinel, siga estes passos para que as consultas sejam exibidas corretamente:
1. Ligue os [registos DNS Analytics nos seus servidores](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Certifique-se de que os DNSEvents aparecem na sua lista de recolhas de Log Analytics.
3. Ligue [a azure DNS Analytics](../azure-monitor/insights/dns-analytics.md).
4. No Azure DNS Analytics, em **Configuração,** altere qualquer uma das definições, guarde-as e, em seguida, volte a alterá-la se precisar e guarde-a novamente.
5. Verifique a análise do Azure DNS para se certificar de que as consultas estão agora a ser apresentadas.

## <a name="next-steps"></a>Próximos passos

Neste documento, aprendeu a ligar os aparelhos DNS no local ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)