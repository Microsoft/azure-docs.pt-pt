---
title: Ver e configurar telemetria de proteção DDoS para norma de proteção DDoS Azure
description: Saiba como visualizar e configurar a telemetria de proteção DDoS para norma de proteção DDoS Azure.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: a3f6c14b7ed2686a262f28510efb37068cfb9cb3
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787303"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Ver e configurar telemetria de proteção contra DDoS

A padrão de proteção DDoS Azure fornece informações detalhadas sobre ataque e visualização com DDoS Attack Analytics. Os clientes que protegem as suas redes virtuais contra ataques de DDoS têm uma visibilidade detalhada no tráfego de ataques e as ações tomadas para mitigar o ataque através de relatórios de mitigação de ataques & registos de fluxo de mitigação. A telemetria rica é exposta através do Azure Monitor, incluindo métricas detalhadas durante a duração de um ataque DDoS. O alerta pode ser configurado para qualquer uma das métricas do Monitor Azure expostas pela Proteção DDoS. O registo pode ser integrado com [Azure Sentinel,](../sentinel/connect-azure-ddos-protection.md)Splunk (Azure Event Hubs), OMS Log Analytics e Azure Storage para análise avançada através da interface Azure Monitor Diagnostics.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Ver telemetria de proteção DDoS
> * Ver políticas de mitigação do DDoS
> * Validar e testar telemetria de proteção DDoS

### <a name="metrics"></a>Métricas

> [!NOTE]
> Embora sejam apresentadas várias opções de **agregação** no portal Azure, apenas os tipos de agregação listados na tabela abaixo são suportados para cada métrica. Pedimos desculpa por esta confusão e estamos a trabalhar para a resolver.

As [seguintes métricas](../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses) estão disponíveis para a Norma de Proteção DDoS Azure. Estas métricas também são exportáveis através de configurações de diagnóstico (ver [e configurar registo de diagnóstico DDoS).](diagnostic-logging.md)


| Metric | Nome de exibição métrica | Unidade | Tipo de Agregação | Descrição |
| --- | --- | --- | --- | --- |
| ByteCount | Byte Count | de palavras | Total | Número total de Bytes transmitidos dentro do período de tempo |
| BytesDroppedDDoS | Bytes de entrada deixaram cair DDoS | BytesPerSecond | Máximo | Bytes de entrada deixaram cair DDoS| 
| BytesForwardedDDoS | Bytes de entrada reencaminhado DDoS | BytesPerSecond | Máximo | Bytes de entrada reencaminhado DDoS |
| BytesInDDoS | Bytes de entrada DDoS | BytesPerSecond | Máximo | Bytes de entrada DDoS |
| DDoSTriggerSYNPackets | Pacotes SYN de entrada para desencadear a mitigação do DDoS | CondePerSecond | Máximo | Pacotes SYN de entrada para desencadear a mitigação do DDoS |
| Pacotes DDoSTriggerTCP | Pacotes TCP de entrada para desencadear mitigação do DDoS | CondePerSecond | Máximo | Pacotes TCP de entrada para desencadear mitigação do DDoS |
| DDoSTriggerUDPPackets | Pacotes UDP de entrada para desencadear mitigação do DDoS | CondePerSecond | Máximo | Pacotes UDP de entrada para desencadear mitigação do DDoS |
| IfUnderDDosAttack | Sob o ataque do DDoS ou não | de palavras | Máximo | Sob o ataque do DDoS ou não |
| PacoteCount | Contagem de Pacotes | de palavras | Total | Número total de Pacotes transmitidos dentro do período de tempo |
| PacotesDroppedDDoS | Pacotes de entrada deixaram cair DDoS | CondePerSecond | Máximo | Pacotes de entrada deixaram cair DDoS |
| PacotesForwardedDDoS | Pacotes de entrada reencaminhados DDoS | CondePerSecond | Máximo | Pacotes de entrada reencaminhados DDoS |
| PacotesInDDoS | Pacotes de entrada DDoS | CondePerSecond | Máximo | Pacotes de entrada DDoS |
| SynCount | Contagem de SINA | de palavras | Total | Número total de pacotes SYN transmitidos dentro do período de tempo |
| TCPBytesDroppedDDoS | Bytes TCP de entrada deixaram cair DDoS | BytesPerSecond | Máximo | Bytes TCP de entrada deixaram cair DDoS |
| TCPBytesForwardedDDoS | Inbound TCP bytes reencaminhado DDoS | BytesPerSecond | Máximo | Inbound TCP bytes reencaminhado DDoS |
| TCPBytesInDDoS | Entrada TCP bytes DDoS | BytesPerSecond | Máximo | Entrada TCP bytes DDoS |
| TCPPacketsDroppedDDoS | Pacotes TCP de entrada deixaram cair DDoS | CondePerSecond | Máximo | Pacotes TCP de entrada deixaram cair DDoS |
| TCPPacketsForwardedDDoS | Pacotes TCP de entrada reencaminhados DDoS | CondePerSecond | Máximo | Pacotes TCP de entrada reencaminhados DDoS |
| TCPPacketsInDDoS | Pacotes TCP de entrada DDoS | CondePerSecond | Máximo | Pacotes TCP de entrada DDoS |
| UDPBytesDroppedDDoS | Bytes udp de entrada deixaram cair DDoS | BytesPerSecond | Máximo | Bytes udp de entrada deixaram cair DDoS |
| UDPBytesForwardedDDoS | Bytes de UDP de entrada reencaminhados DDoS | BytesPerSecond | Máximo | Bytes de UDP de entrada reencaminhados DDoS |
| UDPBytesInDDoS | UDP bytes DDoS de entrada | BytesPerSecond | Máximo | UDP bytes DDoS de entrada |
| UDPPacketsDroppedDDoS | Pacotes UDP de entrada deixaram cair DDoS | CondePerSecond | Máximo | Pacotes UDP de entrada deixaram cair DDoS |
| UDPPacketsForwardedDDoS | Pacotes UDP de entrada reencaminhados DDoS | CondePerSecond | Máximo | Pacotes UDP de entrada reencaminhados DDoS |
| UDPPacketsInDDoS | Pacotes de UDP de entrada DDoS | CondePerSecond | Máximo | Pacotes de UDP de entrada DDoS |
| VipSAdability | Disponibilidade do Caminho de Dados | de palavras | Média | Disponibilidade média de endereço IP por duração de tempo |

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Antes de poder completar os passos neste tutorial, tem primeiro de criar um [plano de proteção Azure DDoS Standard](manage-ddos-protection.md) e o DDoS Protection Standard deve ser ativado numa rede virtual.
- O DDoS monitoriza endereços IP públicos atribuídos a recursos dentro de uma rede virtual. Se não tiver recursos com endereços IP públicos na rede virtual, tem primeiro de criar um recurso com um endereço IP público. Pode monitorizar o endereço IP público de todos os recursos implantados através do Gestor de Recursos (não clássico) listados na [rede Virtual para serviços Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (incluindo Equiliadores de Carga Azure onde as máquinas virtuais de backend estão na rede virtual), exceto para Ambientes de Serviço de Aplicações Azure. Para continuar com este tutorial, pode rapidamente criar uma máquina virtual [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  

## <a name="view-ddos-protection-telemetry"></a>Ver telemetria de proteção DDoS

A telemetria para um ataque é fornecida através do Monitor Azure em tempo real. A telemetria só está disponível durante o período em que um endereço IP público esteja sob mitigação. Não se vê telemetria antes ou depois de um ataque ser atenuado.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e navegue no seu Plano de Proteção DDoS.
2. Em **Monitorização**, selecione **Métricas**.
3. Selecione **Âmbito**. Selecione a **Subscrição** que contém o endereço IP público que pretende registar, selecione **Endereço IP Público** para tipo de **recurso,** em seguida, selecione o endereço IP público específico para o quais pretende registar métricas e, em seguida, selecione **Aplicar**.
4. Selecione o tipo **de agregação** como **Max**.

Os nomes métricos apresentam diferentes tipos de pacotes, e bytes vs. pacotes, com uma construção básica de nomes de etiquetas em cada métrica da seguinte forma:

- **Nome da etiqueta de queda** (por exemplo, **Pacotes de Entrada Deixou Cair DDoS**): O número de pacotes caídos/esfregados pelo sistema de proteção DDoS.
- **Nome de etiqueta reencaminhado** (por **exemplo, Pacotes de Entrada Reencaminhados DDoS**): Número de pacotes reencaminhados pelo sistema DDoS para o destino VIP – tráfego que não foi filtrado.
- **Sem nome de identificação** (por **exemplo, Pacotes de Entrada DDoS**): O número total de pacotes que entraram no sistema de esfregar – representando a soma dos pacotes caídos e reencaminhados.

## <a name="view-ddos-mitigation-policies"></a>Ver políticas de mitigação do DDoS

A Norma de Proteção DDoS aplica três políticas de mitigação auto-sintonizadas (TCP SYN, TCP & UDP) para cada endereço IP público do recurso protegido, na rede virtual que tem DDoS ativado. Pode ver os limiares de política selecionando os  **pacotes TCP de entrada para desencadear pacotes de mitigação de DDoS** e UDP de entrada para ativar métricas **de mitigação de DDoS** com tipo **de agregação** como 'Max', como mostra a seguinte imagem:

![Ver políticas de mitigação](./media/manage-ddos-protection/view-mitigation-policies.png)

Os limiares de política são configurados automaticamente através do perfil de tráfego de rede baseado em aprendizagem automática Azure. Só quando o limiar da apólice é violado é que a mitigação do DDoS ocorre para o endereço IP sob ataque.

## <a name="validate-and-test"></a>Validar e testar

Para simular um ataque DDoS para validar a telemetria de proteção DDoS, consulte [a deteção de DDoS validada](test-through-simulations.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

- Alertas de configuração para métricas de proteção DDoS
- Ver telemetria de proteção DDoS
- Ver políticas de mitigação do DDoS
- Validar e testar telemetria de proteção DDoS

Para aprender a configurar relatórios de mitigação de ataques e registos de fluxo, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ver e configurar o registo de diagnósticos do DDoS](diagnostic-logging.md)