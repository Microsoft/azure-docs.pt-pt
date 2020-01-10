---
title: Gerenciar a proteção contra DDoS do Azure Standard usando o portal do Azure
titlesuffix: Azure Virtual Network
description: Saiba como usar a telemetria padrão de proteção contra DDoS do Azure em Azure Monitor para atenuar um ataque.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 786b21e7571ed173d2da90f587a5b76d8c92a13d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450890"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gerenciar a proteção contra DDoS do Azure Standard usando o portal do Azure

Saiba como habilitar e desabilitar a proteção contra DDoS (negação de serviço distribuído) e usar a telemetria para atenuar um ataque de DDoS com a proteção contra DDoS do Azure Standard. A proteção contra DDoS Standard protege recursos do Azure, como máquinas virtuais, balanceadores de carga e gateways de aplicativo que têm um [endereço IP público](virtual-network-public-ip-address.md) do Azure atribuído a ele. Para saber mais sobre a proteção contra DDoS Standard e seus recursos, consulte [visão geral da proteção contra DDoS Standard](ddos-protection-overview.md).

Antes de concluir as etapas deste tutorial, faça logon no portal do Azure em https://portal.azure.com com uma conta atribuída à função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuída às ações apropriadas listadas em [permissões](#permissions).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-ddos-protection-plan"></a>Criar um plano de proteção contra DDoS

Um plano de proteção contra DDoS define um conjunto de redes virtuais que têm a proteção contra DDoS padrão habilitada, entre assinaturas. Você pode configurar um plano de proteção contra DDoS para sua organização e vincular redes virtuais de várias assinaturas ao mesmo plano. O plano de proteção contra DDoS em si também está associado a uma assinatura, que você seleciona durante a criação do plano. O plano de proteção contra DDoS funciona em regiões e assinaturas. Exemplo – você pode criar o plano na região leste-EUA e vincular à assinatura #1 em seu locatário. O mesmo plano pode ser vinculado a redes virtuais de outras assinaturas em regiões diferentes, em seu locatário. A assinatura à qual o plano está associado incorre a fatura mensal recorrente para o plano, bem como encargos excedentes, caso o número de endereços IP públicos protegidos exceda 100. Para obter mais informações sobre preços de DDoS, consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/ddos-protection/).

A criação de mais de um plano não é necessária para a maioria das organizações. Um plano não pode ser movido entre assinaturas. Se você quiser alterar a assinatura em que um plano está, será necessário [excluir o plano existente](#work-with-ddos-protection-plans) e criar um novo.

1. Selecione **criar um recurso** no canto superior esquerdo da portal do Azure.
2. Pesquise por *DDoS*. Quando o **plano de proteção contra DDoS** aparecer nos resultados da pesquisa, selecione-o.
3. Selecione **Criar**.
4. Insira ou selecione seus próprios valores, ou insira ou selecione os seguintes valores de exemplo e, em seguida, selecione **criar**:

    |Definição        |Valor                                              |
    |---------      |---------                                          |
    |Nome           | myDdosProtectionPlan                              |
    |Subscrição   | Selecione a sua subscrição.                         |
    |Grupo de recursos | Selecione **criar novo** e insira o *myresourceattribute* |
    |Localização       | Este dos E.U.A.                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Habilitar DDoS para uma nova rede virtual

1. Selecione **criar um recurso** no canto superior esquerdo da portal do Azure.
2. Selecione **Redes** e, em seguida, selecione **Rede virtual**.
3. Insira ou selecione seus próprios valores, em inserir ou selecione os valores de exemplo a seguir, aceite os padrões restantes e, em seguida, selecione **criar**:

    | Definição         | Valor                                                        |
    | ---------       | ---------                                                    |
    | Nome            | myVirtualNetwork                                             |
    | Subscrição    | Selecione a sua subscrição.                                    |
    | Grupo de recursos  | Selecione **Utilizar existente** e, em seguida, **myResourceGroup** |
    | Localização        | Este dos E.U.A.                                                      |
    | Proteção contra DDos | Selecione **padrão** e, em seguida, em **proteção contra DDoS**, selecione **myDdosProtectionPlan**. O plano selecionado pode estar no mesmo ou em uma assinatura diferente da rede virtual, mas ambas as assinaturas devem estar associadas ao mesmo locatário de Azure Active Directory.|

Não é possível mover uma rede virtual para outro grupo de recursos ou assinatura quando o padrão de DDoS está habilitado para a rede virtual. Se você precisar mover uma rede virtual com o padrão de DDoS habilitado, desabilite o padrão de DDoS primeiro, mova a rede virtual e, em seguida, habilite o padrão de DDoS. Após a movimentação, os limites de política ajustados automaticamente para todos os endereços IP públicos protegidos na rede virtual são redefinidos.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Habilitar DDoS para uma rede virtual existente

1. Crie um plano de proteção contra DDoS concluindo as etapas em [criar um plano de proteção contra DDoS](#create-a-ddos-protection-plan), se você não tiver um plano de proteção contra DDoS existente.
2. Selecione **criar um recurso** no canto superior esquerdo da portal do Azure.
3. Insira o nome da rede virtual para a qual você deseja habilitar a proteção contra DDoS Standard na **caixa Pesquisar recursos, serviços e documentos** na parte superior do Portal. Quando o nome da rede virtual aparecer nos resultados da pesquisa, selecione-o.
4. Selecione **proteção contra DDoS**, em **configurações**.
5. Selecione **padrão**. Em **plano de proteção contra DDoS**, selecione um plano de proteção contra DDoS existente ou o plano que você criou na etapa 1 e, em seguida, selecione **salvar**. O plano selecionado pode estar no mesmo ou em uma assinatura diferente da rede virtual, mas ambas as assinaturas devem estar associadas ao mesmo locatário de Azure Active Directory.

**Comandos** 
- CLI do Azure: [AZ Network DDoS-Protection Create](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create)
- PowerShell: [New-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>Desabilitar DDoS para uma rede virtual

1. Insira o nome da rede virtual para a qual você deseja desabilitar a proteção contra DDoS Standard para na **caixa Pesquisar recursos, serviços e documentos** na parte superior do Portal. Quando o nome da rede virtual aparecer nos resultados da pesquisa, selecione-o.
2. Selecione **proteção contra DDoS**, em **configurações**.
3. Selecione **básico** em **plano de proteção contra DDoS** e, em seguida, selecione **salvar**.

**Comandos** 
- CLI do Azure: [AZ Network DDoS-Protection Delete](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete)
- PowerShell: [Remove-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>Trabalhar com planos de proteção contra DDoS

1. Selecione **todos os serviços** na parte superior, à esquerda do Portal.
2. Digite *DDoS* na caixa de **filtro** . Quando os **planos de proteção contra DDoS** aparecerem nos resultados, selecione-os.
3. Selecione o plano de proteção que você deseja exibir na lista.
4. Todas as redes virtuais associadas ao plano são listadas.
5. Se você quiser excluir um plano, primeiro você deve desassociar todas as redes virtuais dele. Para dissociar um plano de uma rede virtual, consulte [desabilitar DDoS para uma rede virtual](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Configurar alertas para métricas de proteção contra DDoS

Você pode selecionar qualquer uma das métricas de proteção contra DDoS disponíveis para alertá-lo quando houver uma mitigação ativa durante um ataque, usando a configuração de alerta Azure Monitor. Quando as condições forem atendidas, o endereço especificado receberá um email de alerta:

1. Selecione **todos os serviços** na parte superior, à esquerda do Portal.
2. Insira *Monitor* na caixa de **filtro** . Quando o **Monitor** aparecer nos resultados, selecione-o.
3. Selecione **métricas** em **serviços compartilhados**.
4. Insira ou selecione seus próprios valores, ou insira os seguintes valores de exemplo, aceite os padrões restantes e, em seguida, selecione **OK**:

    |Definição                  |Valor                                                                                               |
    |---------                |---------                                                                                           |
    |Nome                     | myDdosAlert                                                                                        |
    |Subscrição             | Selecione a assinatura que contém o endereço IP público para o qual você deseja receber alertas.        |
    |Grupo de recursos           | Selecione o grupo de recursos que contém o endereço IP público para o qual você deseja receber alertas.      |
    |Recurso                 | Selecione o endereço IP público que contém o endereço IP público para o qual você deseja receber alertas. DDoS monitora endereços IP públicos atribuídos a recursos em uma rede virtual. Se você não tiver nenhum recurso com endereços IP públicos na rede virtual, primeiro deverá criar um recurso com um endereço IP público. Você pode monitorar o endereço IP público de todos os recursos implantados por meio do Resource Manager (não clássico) listados em [rede virtual para serviços do Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), exceto para ambientes de serviço Azure app e gateway de VPN do Azure. Para continuar com este tutorial, você pode criar rapidamente uma máquina virtual [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .                   |
    |Métrica                   | Sob ataque de DDoS ou não                                                                            |
    |Limiar                | 1- **1** significa que você está sob ataque. **0** significa que você não está sob ataque.                         |
    |Período                   | Selecione qualquer valor que você escolher.                                                                   |
    |Notificar por email         | marque a caixa de verificação                                                                                  |
    |Administrador adicional | Insira seu endereço de email se você não for um proprietário, colaborador ou leitor de email para a assinatura. |

    Em alguns minutos de detecção de ataque, você receberá um email de Azure Monitor métricas semelhante à imagem a seguir:

    ![Alerta de ataque](./media/manage-ddos-protection/ddos-alert.png)


Para simular um ataque de DDoS para validar seu alerta, consulte [validar detecção de DDoS](#validate-ddos-detection).

Você também pode aprender mais sobre a [configuração de WebHooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [aplicativos lógicos](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para a criação de alertas.

## <a name="use-ddos-protection-telemetry"></a>Usar telemetria de proteção contra DDoS

A telemetria de um ataque é fornecida por meio de Azure Monitor em tempo real. A telemetria está disponível apenas pela duração em que um endereço IP público está sob mitigação. Você não vê a telemetria antes ou depois que um ataque é mitigado.

1. Selecione **todos os serviços** na parte superior, à esquerda do Portal.
2. Insira *Monitor* na caixa de **filtro** . Quando o **Monitor** aparecer nos resultados, selecione-o.
3. Selecione **métricas**, em **serviços compartilhados**.
4. Selecione a **assinatura** e o **grupo de recursos** que contêm o endereço IP público para o qual você deseja a telemetria.
5. Selecione **endereço IP público** para **tipo de recurso**e selecione o endereço IP público específico para o qual você deseja a telemetria.
6. Uma série de **métricas disponíveis** aparece no lado esquerdo da tela. Essas métricas, quando selecionadas, estão grafas no **gráfico Azure monitor métricas** na tela Visão geral.
7. Selecione o tipo de **agregação** como **máximo**

Os nomes de métrica apresentam diferentes tipos de pacotes e bytes versus pacotes, com uma construção básica de nomes de marca em cada métrica da seguinte maneira:

- **Nome da marca Descartado** (por exemplo, **pacotes de entrada eliminados por DDoS**): o número de pacotes descartados/depurados pelo sistema de proteção contra DDoS.
- **Nome da marca encaminhada** (por exemplo, **pacotes de entrada encaminhados por DDoS**): o número de pacotes encaminhados pelo sistema de DDoS para o VIP de destino – tráfego que não foi filtrado.
- **Nenhum nome de marca** (por exemplo, **pacotes de entrada DDoS**): o número total de pacotes que vieram para o sistema de depuração – que representa a soma dos pacotes descartados e encaminhados.

Para simular um ataque de DDoS para validar a telemetria, consulte [validar detecção de DDoS](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Exibir políticas de mitigação de DDoS

A proteção contra DDoS Standard aplica três políticas de mitigação ajustadas automaticamente (TCP SYN, TCP & UDP) para cada endereço IP público do recurso protegido, na rede virtual com DDoS habilitado. Você pode exibir os limites da política selecionando os **pacotes TCP de entrada para disparar a mitigação de DDoS** e **pacotes UDP de entrada para disparar** métricas de mitigação de DDoS com o tipo de **agregação** como ' Max ', conforme mostrado na figura a seguir:

![Exibir políticas de mitigação](./media/manage-ddos-protection/view-mitigation-policies.png)

Os limites de política são configurados automaticamente por meio da criação de perfil de tráfego de rede baseada no Azure Machine Learning. Somente quando o limite da política for violado, a mitigação de DDoS ocorrerá para o endereço IP sob ataque.

## <a name="configure-ddos-attack-analytics"></a>Configurar a análise de ataque de DDoS
A proteção contra DDoS do Azure Standard fornece informações de ataque e visualização detalhadas com a análise de ataque de DDoS. Os clientes que protegem suas redes virtuais contra ataques de DDoS têm visibilidade detalhada do tráfego de ataque e das ações tomadas para mitigar o ataque por meio de relatórios de mitigação de ataques & logs de fluxo de mitigação. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Configurar relatórios de mitigação de ataque de DDoS
Os relatórios de mitigação de ataque usam os dados do protocolo NetFlow que são agregados para fornecer informações detalhadas sobre o ataque em seu recurso. A qualquer momento que um recurso de IP público estiver sob ataque, a geração de relatórios será iniciada assim que a mitigação for iniciada. Haverá um relatório incremental gerado a cada 5 minutos e um relatório de pós-mitigação para todo o período de mitigação. Isso é para garantir que, em um evento, o ataque de DDoS continue por mais tempo, você poderá exibir o instantâneo mais atual do relatório de mitigação a cada 5 minutos e um resumo completo quando a mitigação do ataque terminar. 

1. Selecione **todos os serviços** na parte superior, à esquerda do Portal.
2. Insira *Monitor* na caixa de **filtro** . Quando o **Monitor** aparecer nos resultados, selecione-o.
3. Em **configurações**, selecione **configurações de diagnóstico**.
4. Selecione a **assinatura** e o **grupo de recursos** que contêm o endereço IP público que você deseja registrar.
5. Selecione **endereço IP público** para **tipo de recurso**e, em seguida, selecione o endereço IP público específico para o qual você deseja registrar as métricas.
6. Selecione **Ativar diagnóstico para coletar o log DDoSMitigationReports** e, em seguida, selecione quantas opções a seguir forem necessárias:

    - **Arquivar em uma conta de armazenamento**: os dados são gravados em uma conta de armazenamento do Azure. Para saber mais sobre essa opção, consulte [arquivar logs de diagnóstico](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Transmitir para um hub de eventos**: permite que um receptor de log pegue os logs usando um hub de eventos do Azure. Os hubs de eventos habilitam a integração com o Splunk ou outros sistemas SIEM. Para saber mais sobre essa opção, consulte [transmitir logs de diagnóstico para um hub de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Enviar para log Analytics**: grava logs no serviço Azure monitor. Para saber mais sobre essa opção, consulte [coletar logs para uso em logs de Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Os relatórios incrementais & de mitigação após o ataque incluem os campos a seguir
- Vetores de ataque
- Estatísticas de tráfego
- Motivo para pacotes descartados
- Protocolos envolvidos
- 10 principais países ou regiões de origem
- 10 principais ASNs de origem

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Configurar logs de fluxo de mitigação de ataque de DDoS
Os logs de fluxo de mitigação de ataque permitem que você examine o tráfego descartado, o tráfego encaminhado e outros pontos de extremidade interessantes durante um ataque de DDoS ativo quase em tempo real. Você pode ingerir o fluxo constante desses dados em seus sistemas SIEM por meio do hub de eventos para monitoramento quase em tempo real, tomar ações em potencial e atender à necessidade de suas operações de defesa. 

1. Selecione **todos os serviços** na parte superior, à esquerda do Portal.
2. Insira *Monitor* na caixa de **filtro** . Quando o **Monitor** aparecer nos resultados, selecione-o.
3. Em **configurações**, selecione **configurações de diagnóstico**.
4. Selecione a **assinatura** e o **grupo de recursos** que contêm o endereço IP público que você deseja registrar.
5. Selecione **endereço IP público** para **tipo de recurso**e, em seguida, selecione o endereço IP público específico para o qual você deseja registrar as métricas.
6. Selecione **Ativar diagnóstico para coletar o log DDoSMitigationFlowLogs** e, em seguida, selecione quantas opções a seguir forem necessárias:

    - **Arquivar em uma conta de armazenamento**: os dados são gravados em uma conta de armazenamento do Azure. Para saber mais sobre essa opção, consulte [arquivar logs de diagnóstico](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Transmitir para um hub de eventos**: permite que um receptor de log pegue os logs usando um hub de eventos do Azure. Os hubs de eventos habilitam a integração com o Splunk ou outros sistemas SIEM. Para saber mais sobre essa opção, consulte [transmitir logs de diagnóstico para um hub de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Enviar para log Analytics**: grava logs no serviço Azure monitor. Para saber mais sobre essa opção, consulte [coletar logs para uso em logs de Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Para exibir os dados de logs de fluxo no painel de análise do Azure, você pode importar o painel de exemplo de https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Os logs de fluxo terão os seguintes campos: 
- IP de origem
- ID de destino
- Porta de origem 
- Porta de destino 
- Tipo de protocolo 
- Ação executada durante a mitigação



## <a name="validate-ddos-detection"></a>Validar detecção de DDoS

A Microsoft estabeleceu uma parceria com a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para criar uma interface na qual você pode gerar tráfego contra endereços IP públicos habilitados para proteção contra DDoS para simulações. A simulação de nuvem de ponto de interrupção permite que você:

- Validar como Microsoft Azure proteção contra DDoS protege os recursos do Azure contra ataques de DDoS
- Otimize seu processo de resposta a incidentes enquanto estiver sob ataque de DDoS
- Documentar a conformidade com DDoS
- Treine suas equipes de segurança de rede

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Exibir alertas de proteção contra DDoS na central de segurança do Azure

A central de segurança do Azure fornece uma lista de [alertas de segurança](/azure/security-center/security-center-managing-and-responding-alerts), com informações para ajudar a investigar e corrigir problemas. Com esse recurso, você obtém uma exibição unificada de alertas, incluindo alertas relacionados a ataques de DDoS e as ações tomadas para mitigar o ataque em tempo quase futuro.
Há dois alertas específicos que você verá para qualquer detecção e mitigação de ataque de DDoS:

- **Ataque de DDoS detectado para IP público**: esse alerta é gerado quando o serviço de proteção contra DDoS detecta que um de seus endereços IP públicos é o destino de um ataque de DDoS.
- **Ataque de DDoS mitigado para IP público**: esse alerta é gerado quando um ataque no endereço IP público é mitigado.
Para exibir os alertas, abra a **central de segurança** no portal do Azure. Em **proteção contra ameaças**, selecione **alertas de segurança**. A captura de tela a seguir mostra um exemplo dos alertas de ataque de DDoS.

![Alerta de DDoS na central de segurança do Azure](./media/manage-ddos-protection/ddos-alert-asc.png)

Os alertas incluem informações gerais sobre o endereço IP público que está sob ataque, informações de inteligência geográfica e contra ameaças e etapas de correção.

## <a name="permissions"></a>Permissões

Para trabalhar com planos de proteção contra DDoS, sua conta deve ser atribuída à função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas na tabela a seguir:

| Ação                                            | Nome                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Ler um plano de proteção contra DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Criar ou atualizar um plano de proteção contra DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Excluir um plano de proteção contra DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Ingressar em um plano de proteção contra DDoS              |

Para habilitar a proteção contra DDoS para uma rede virtual, sua conta também deve receber as [ações apropriadas para redes virtuais](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>Passos seguintes

- Criar e aplicar a [política do Azure](policy-samples.md) para redes virtuais