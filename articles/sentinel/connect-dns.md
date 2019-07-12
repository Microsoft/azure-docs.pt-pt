---
title: Ligue os dados DNS em pré-visualização de sentinela de Azure | Documentos da Microsoft
description: Saiba como ligar a dados DNS no Azure sentinela.
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
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 1c79aad557efb85a8797584c33c74983ef645d07
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611319"
---
# <a name="connect-your-domain-name-server"></a>Connect your domain name server

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode ligar-se a qualquer servidor de nomes de domínio (DNS) em execução no Windows para sentinela do Azure. Isso é feito ao instalar um agente na máquina de DNS. Registos de utilização do DNS, pode obter segurança, desempenho e informações de relacionados com operações de mensagens em fila para a infraestrutura DNS da sua organização ao recolher, analisar, e correlacionar analíticas e registos de auditoria e outros dados relacionados de servidores DNS.

Ao ativar a ligação de registo de DNS, pode:
- Identificar os clientes que tentam resolver os nomes de domínios maliciosos
- Identificar registos de recursos obsoletos
- Identificar os nomes de domínio consultados com frequência e clientes DNS conversador
- Carga de pedidos de vista nos servidores DNS
- Falhas de registo DNS dinâmicas do Vista

## <a name="connected-sources"></a>Origens ligadas

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução:

| **Origem ligada** | **Suporte** | **Descrição** |
| --- | --- | --- |
| [Agentes do Windows](../azure-monitor/platform/agent-windows.md) | Sim | A solução recolhe informações de DNS de agentes do Windows. |
| [Agentes do Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | Não | A solução não recolhe informações de DNS de agentes diretos do Linux. |
| [Grupo de gestão do System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Sim | A solução recolhe informações de DNS de agentes num grupo de gestão do Operations Manager ligado. Não é necessária uma conexão direta do agente do Operations Manager para o Azure Monitor. Os dados são reencaminhados do grupo de gestão para a área de trabalho do Log Analytics. |
| [Conta de armazenamento do Azure](../azure-monitor/platform/collect-azure-metrics-logs.md) | Não | O armazenamento do Azure não é utilizado pela solução. |

### <a name="data-collection-details"></a>Detalhes de recolha de dados

A solução recolhe dados de relacionados a eventos DNS de inventário DNS e dos servidores DNS onde está instalado um agente de Log Analytics. Dados relacionados com o inventário, como o número de servidores DNS, zonas e registos de recursos, são recolhidos ao executar os cmdlets do PowerShell de DNS. Os dados são atualizados uma vez a cada dois dias. Os dados relacionados a eventos são recolhidos quase em tempo real do [analíticas e registos de auditoria](https://technet.microsoft.com/library/dn800669.aspx#enhanc) fornecida pelo aprimorados registo e diagnósticos DNS no Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Ligar a sua aplicação de DNS

1. No portal do Azure sentinela, selecione **conectores de dados** e escolha o **DNS** mosaico.
1. Se as máquinas DNS no Azure:
    1. Clique em **instalar o agente na máquina de virtual do Windows Azure**.
    1. Na **máquinas virtuais** , selecione a máquina DNS que pretende transmitir para o Azure sentinela. Certifique-se de que esta é uma VM do Windows.
    1. Na janela que se abre para essa VM, clique em **Connect**.  
    1. Clique em **habilitar** no **conector do DNS** janela. 

2. Se a sua máquina DNS não é uma VM do Azure:
    1. Clique em **instalar o agente em máquinas não Azure**.
    1. Na **agente direto** janela, selecione **agente de transferir o Windows (64 bits)** ou **agente de transferir o Windows (32 bits)** .
    1. Instale o agente no seu computador DNS. Copiar o **ID da área de trabalho**, **chave primária**, e **chave secundária** e utilizá-los quando lhe for pedido durante a instalação.

3. Para utilizar o esquema relevante no Log Analytics para os registos DNS, procure **DnsEvents**.

## <a name="validate"></a>Validação 

No Log Analytics, procure o esquema **DnsEvents** e certifique-se de eventos.

## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar a aplicações no local de DNS para sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).
