---
title: Conectar dados DNS no Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados DNS no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: c5e58f496176ec0f1b8317c8b862a8ef2ffa434d
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262736"
---
# <a name="connect-your-domain-name-server"></a>Conectar seu servidor de nome de domínio

> [!IMPORTANT]
> O conector de dados DNS no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode conectar qualquer DNS (servidor de nomes de domínio) em execução no Windows para o Azure Sentinel. Isso é feito por meio da instalação de um agente no computador DNS. Usando logs DNS, você pode obter informações relacionadas à segurança, ao desempenho e às operações na infraestrutura de DNS da sua organização, coletando, analisando e correlacionando logs analíticos e de auditoria e outros dados relacionados dos servidores DNS.

Ao habilitar a conexão de log DNS, você pode:
- Identificar clientes que tentam resolver nomes de domínio mal-intencionados
- Identificar registros de recursos obsoletos
- Identificar nomes de domínio consultados com frequência e clientes DNS comunicativos
- Exibir a carga de solicitação em servidores DNS
- Exibir falhas de registro de DNS dinâmico

## <a name="connected-sources"></a>Origens ligadas

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução:

| **Origem ligada** | **Suporte** | **Descrição** |
| --- | --- | --- |
| [Agentes do Windows](../azure-monitor/platform/agent-windows.md) | Sim | A solução recolhe informações de DNS de agentes do Windows. |
| [Agentes do Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | Não | A solução não recolhe informações de DNS de agentes diretos do Linux. |
| [Grupo de gestão do System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Sim | A solução recolhe informações de DNS de agentes num grupo de gestão do Operations Manager ligado. Não é necessária uma conexão direta do agente de Operations Manager para Azure Monitor. Os dados são reencaminhados do grupo de gestão para a área de trabalho do Log Analytics. |
| [Conta de armazenamento do Azure](../azure-monitor/platform/collect-azure-metrics-logs.md) | Não | O armazenamento do Azure não é utilizado pela solução. |

### <a name="data-collection-details"></a>Detalhes de recolha de dados

A solução recolhe dados de relacionados a eventos DNS de inventário DNS e dos servidores DNS onde está instalado um agente de Log Analytics. Dados relacionados com o inventário, como o número de servidores DNS, zonas e registos de recursos, são recolhidos ao executar os cmdlets do PowerShell de DNS. Os dados são atualizados uma vez a cada dois dias. Os dados relacionados a eventos são recolhidos quase em tempo real do [analíticas e registos de auditoria](https://technet.microsoft.com/library/dn800669.aspx#enhanc) fornecida pelo aprimorados registo e diagnósticos DNS no Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Conectar seu dispositivo DNS

1. No portal do Azure Sentinel, selecione **conectores de dados** e escolha o bloco **DNS (visualização)** .
1. Se os computadores DNS estiverem no Azure:
    1. Clique em **instalar agente na máquina virtual do Windows do Azure**.
    1. Na lista **máquinas virtuais** , selecione o computador DNS que você deseja transmitir para o Azure Sentinel. Verifique se esta é uma VM do Windows.
    1. Na janela que é aberta para essa VM, clique em **conectar**.  
    1. Clique em **habilitar** na janela do **conector DNS** . 

2. Se o computador DNS não for uma VM do Azure:
    1. Clique em **instalar agente em computadores não Azure**.
    1. Na janela **agente direto** , selecione baixar o **agente do Windows (64 bits)** ou **baixar o agente do Windows (32 bits)** .
    1. Instale o agente em seu computador DNS. Copie a **ID do espaço de trabalho**, a **chave primária**e a **chave secundária** e use-as quando solicitado durante a instalação.

3. Para usar o esquema relevante no Log Analytics para os logs DNS, procure **DnsEvents**.

## <a name="validate"></a>Validar 

Em Log Analytics, procure o esquema **DnsEvents** e verifique se há eventos.

## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar os dispositivos locais do DNS ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
