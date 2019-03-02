---
title: Gerir padrão do Azure DDoS Protection através do portal do Azure
titlesuffix: Azure Virtual Network
description: Saiba como utilizar o Azure DDoS Protection padrão telemetria no Azure Monitor para mitigar um ataque.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: jdial
ms.openlocfilehash: 32c4516c7dc68b04826b362d34841160936d682d
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244524"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gerir padrão do Azure DDoS Protection através do portal do Azure

Saiba como ativar e desativar ataques denial of protection service (DDoS) e utilizar a telemetria para mitigar um ataque de DDoS com proteção contra DDoS do Azure Standard. Padrão de proteção contra DDoS protege os recursos do Azure como máquinas virtuais, balanceadores de carga e gateways de aplicação que tem do Azure [endereço IP público](virtual-network-public-ip-address.md) atribuídos ao mesmo. Para saber mais sobre o padrão de proteção de DDoS e seus recursos, veja [descrição geral do padrão de proteção de DDoS](ddos-protection-overview.md).

Antes de concluir qualquer os passos neste tutorial, inicie sessão no portal do Azure em https://portal.azure.com com uma conta atribuída para o [Contribuidor de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que é atribuído o adequado ações listadas na [permissões](#permissions).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-ddos-protection-plan"></a>Criar um plano de proteção contra DDoS

Um plano de proteção contra DDoS define um conjunto de redes virtuais que têm a norma de proteção DDoS ativada, entre subscrições. Pode configurar um plano de proteção de DDoS para sua organização e redes virtuais ligação de várias subscrições para o mesmo plano. O plano de proteção contra DDoS em si também está associado uma subscrição que selecionou durante a criação do plano. A subscrição o plano é associado a implica a fatura mensal recorrente para o plano, bem como os custos da utilização excessiva, no caso do número de endereços IP públicos protegidos exceder 100. Para obter mais informações sobre os preços de DDoS, consulte [os detalhes dos preços](https://azure.microsoft.com/pricing/details/ddos-protection/).

Criação de mais de um plano não é necessária para a maioria das organizações. Um plano não pode ser movido entre subscrições. Se pretender alterar a subscrição que pertença a um plano, precisa [eliminar o plano existente](#work-with-ddos-protection-plans) e criar um novo.

1. Selecione **criar um recurso** no canto superior esquerdo do portal do Azure.
2. Procure *DDoS*. Quando **plano de proteção DDos** aparecer nos resultados da pesquisa, selecione.
3. Selecione **Criar**.
4. Introduza ou selecione seus próprios valores, ou introduza, ou selecione os seguintes valores de exemplo e, em seguida, selecione **criar**:

    |Definição        |Valor                                              |
    |---------      |---------                                          |
    |Name           | myDdosProtectionPlan                              |
    |Subscrição   | Selecione a sua subscrição.                         |
    |Grupo de recursos | Selecione **criar novo** e introduza *myResourceGroup* |
    |Localização       | EUA Leste                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Ativar o DDoS para uma nova rede virtual

1. Selecione **criar um recurso** no canto superior esquerdo do portal do Azure.
2. Selecione **Redes** e, em seguida, selecione **Rede virtual**.
3. Introduza ou selecione seus próprios valores, de enter ou selecione os seguintes valores de exemplo, aceite as predefinições restantes e, em seguida, selecione **criar**:

    | Definição         | Valor                                                        |
    | ---------       | ---------                                                    |
    | Nome            | myVirtualNetwork                                             |
    | Subscrição    | Selecione a sua subscrição.                                    |
    | Grupo de recursos  | Selecione **Utilizar existente** e, em seguida, **myResourceGroup** |
    | Localização        | EUA Leste                                                      |
    | Proteção contra DDos | Selecione **padrão** e, em **proteção contra DDoS**, selecione **myDdosProtectionPlan**. O plano que selecionou pode ser na subscrição idêntica ou diferente do que a rede virtual, mas ambas as subscrições têm de estar associadas ao mesmo inquilino do Azure Active Directory.|

Não é possível mover uma rede virtual para outro grupo de recursos ou subscrição quando padrão DDoS está ativada para a rede virtual. Se precisar de mover uma rede virtual com o padrão de DDoS ativada, desativar o padrão de DDoS em primeiro lugar, mover a rede virtual e, em seguida, ativar o padrão de DDoS. Após a mudança, são repostos os limiares de política otimizadas para todos os protegido endereços IP públicos na rede virtual.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Ativar o DDoS para uma rede virtual existente

1. Criar um plano de proteção contra DDoS, concluindo os passos em [criar um plano de proteção DDoS](#create-a-ddos-protection-plan), se não tiver um plano de proteção contra DDoS existente.
2. Selecione **criar um recurso** no canto superior esquerdo do portal do Azure.
3. Introduza o nome da rede virtual que pretende ativar a DDoS Protection padrão para o **procurar recursos, serviços e caixa docs** na parte superior do portal. Quando o nome da rede virtual é apresentada nos resultados da pesquisa, selecione-o.
4. Selecione **proteção contra DDoS**, em **definições**.
5. Selecione **padrão**. Sob **plano de proteção DDoS**, selecione um plano de proteção contra DDoS existente, ou o plano que criou no passo 1 e, em seguida, selecione **guardar**. O plano que selecionou pode ser na subscrição idêntica ou diferente do que a rede virtual, mas ambas as subscrições têm de estar associadas ao mesmo inquilino do Azure Active Directory.

## <a name="disable-ddos-for-a-virtual-network"></a>Desativar o DDoS para uma rede virtual

1. Introduza o nome da rede virtual que pretende desativar a norma de proteção de DDoS no **procurar recursos, serviços e caixa docs** na parte superior do portal. Quando o nome da rede virtual é apresentada nos resultados da pesquisa, selecione-o.
2. Selecione **proteção contra DDoS**, em **definições**.
3. Selecione **básica** sob **plano de proteção DDoS** e, em seguida, selecione **guardar**.

## <a name="work-with-ddos-protection-plans"></a>Trabalhar com planos de proteção DDoS

1. Selecione **todos os serviços** na parte superior, à esquerda do portal.
2. Introduza *DDoS* no **filtro** caixa. Quando **planos de proteção DDoS** aparecer nos resultados, selecione-o.
3. Selecione o plano de proteção que pretende visualizar na lista.
4. São listadas todas as redes virtuais associadas ao plano.
5. Se quiser eliminar um plano, primeiro tem de dissociá todas as redes virtuais do mesmo. Para desassociar um plano de uma rede virtual, veja [desativar DDoS para uma rede virtual](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Configurar alertas de métricas de proteção DDoS

Pode selecionar qualquer uma das métricas de proteção contra DDoS disponíveis para o alertar quando existe uma atenuação ativada durante um ataque, usando a configuração de alerta do Monitor do Azure. Quando as condições forem cumpridas, o endereço especificado recebe um e-mail de alerta:

1. Selecione **todos os serviços** na parte superior, à esquerda do portal.
2. Introduza *Monitor* no **filtro** caixa. Quando **Monitor** aparecer nos resultados, selecione.
3. Selecione **métricas** sob **SHARED SERVICES**.
4. Introduza, ou selecione os seus próprios valores, ou introduza os valores de exemplo seguinte, aceite as predefinições restantes e, em seguida, selecione **OK**:

    |Definição                  |Valor                                                                                               |
    |---------                |---------                                                                                           |
    |Name                     | myDdosAlert                                                                                        |
    |Subscrição             | Selecione a subscrição que contém o endereço IP público que pretende receber alertas para.        |
    |Grupo de recursos           | Selecione o grupo de recursos que contém o endereço IP público que pretende receber alertas para.      |
    |Recurso                 | Selecione o endereço IP público que contém o endereço IP público que pretende receber alertas para. DDoS monitoriza os endereços IP públicos atribuídos aos recursos numa rede virtual. Se não tiver quaisquer recursos com endereços IP públicos na rede virtual, primeiro tem de criar um recurso com um endereço IP público. Pode monitorizar o endereço IP público de todos os recursos implementados através do Resource Manager (não clássica) listados na [rede Virtual para os serviços do Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), exceto para ambientes de serviço de aplicações do Azure e o Gateway de VPN do Azure. Para continuar com este tutorial, pode criar rapidamente um [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) máquina virtual.                   |
    |Métrica                   | Em DDoS de ataque ou não                                                                            |
    |Limiar                | 1 - **1** significa que está sob ataque. **0** significa que não estiver sob ataque.                         |
    |Período                   | Selecione qualquer valor que escolher.                                                                   |
    |Notificar por E-Mail         | Marque a caixa de verificação                                                                                  |
    |Administrador adicional | Se não tiver um proprietário do e-mail, contribuinte ou leitor para a subscrição, introduza o seu endereço de e-mail. |

    Em poucos minutos de detecção de ataque, receberá uma mensagem de e-mail de métricas do Azure Monitor, semelhante à imagem seguinte:

    ![Alerta de ataque](./media/manage-ddos-protection/ddos-alert.png)


Para simular um ataque de DDoS para validar o alerta, consulte [deteção de DDoS validar](#validate-ddos-detection).

Também pode saber mais sobre [configurar webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [aplicações lógicas](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para a criação de alertas.

## <a name="use-ddos-protection-telemetry"></a>Utilizar a telemetria de proteção DDoS

Telemetria para um ataque é fornecida através do Azure Monitor em tempo real. A telemetria está disponível apenas durante o período que um endereço IP público sob atenuação. Não ver a telemetria antes ou depois de um ataque seja reduzido.

1. Selecione **todos os serviços** na parte superior, à esquerda do portal.
2. Introduza *Monitor* no **filtro** caixa. Quando **Monitor** aparecer nos resultados, selecione.
3. Selecione **métricas**, em **SHARED SERVICES**.
4. Selecione o **subscrição** e **grupo de recursos** que contêm o endereço IP público que pretende que a telemetria.
5. Selecione **endereço IP público** para **tipo de recurso**, em seguida, selecione o endereço IP público específico que pretende que a telemetria.
6. Uma série de **métricas disponíveis** aparecem no lado esquerdo do ecrã. Estas métricas, quando selecionada, são representados no gráfico a **gráfico de métricas do Azure Monitor** no ecrã de descrição geral.

Os nomes de métrica apresentam tipos diferentes de pacote e bytes versus pacotes, com uma construção básica de nomes de etiqueta em cada métrica da seguinte forma:

- **Nome da etiqueta removido** (por exemplo, **DDoS de ignorados de pacotes de entrada**): O número de pacotes ignorados/limpa o sistema de proteção contra DDoS.
- **Nome da etiqueta forwarded** (por exemplo **DDoS de reencaminhados de pacotes de entrada**): O número de pacotes reencaminhados pelo sistema de DDoS para o VIP – o tráfego que não foi filtrado de destino.
- **Nenhum nome de tag** (por exemplo **DDoS de pacotes de entrada**): O número total de pacotes que surgiu no sistema limpeza – que representa a soma dos pacotes ignorados e reencaminhados.

Para simular um ataque de DDoS para validar a telemetria, consulte [deteção de DDoS validar](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Ver políticas de mitigação de DDoS

Padrão de proteção de DDoS aplica-se três políticas de atenuação otimizadas (TCP SYN, TCP e UDP) para cada endereço IP público do recurso protegido, na rede virtual que tenha o DDoS ativada. Pode ver os limiares de política, selecionando o **pacotes de TCP de entrada para acionar a mitigação de DDoS** e **pacotes UDP de entrada para acionar a mitigação de DDoS** métricas, como mostrado na imagem seguinte:

![Ver políticas de atenuação](./media/manage-ddos-protection/view-mitigation-policies.png)

Limiares de política são automaticamente configurados através do Azure machine learning com base em rede tráfego de criação de perfis. Apenas quando o limiar de política é quebrado mitigação de DDoS ocorrer para o endereço IP sob ataque.

## <a name="configure-ddos-attack-analytics"></a>Configure a análise de ataque de DDoS
Padrão de proteção contra DDoS do Azure fornece informações detalhadas de ataque e visualização com a análise de ataque de DDoS. Os clientes a proteger as suas redes virtuais contra ataques DDoS têm detalhados visibilidade sobre o tráfego de ataque e as ações executadas para mitigar o ataque através de relatórios de mitigação de ataque e os registos de fluxo de atenuação. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Configurar relatórios de mitigação de ataque de DDoS
Relatórios de mitigação de ataque usa os dados de protocolo de Netflow que são agregados para fornecer informações detalhadas sobre o ataque no seu recurso. Sempre que um recurso IP público sob ataque, a geração do relatório será iniciada assim que a atenuação é iniciado. Será gerado um relatório incremental cada 5 minutos e um relatório de pós-atenuação de para o período de atenuação de todo. Isso é para garantir que, no evento de um que ataque de DDoS continua durante um longo período de tempo, poderá ver o instantâneo mais recente do relatório de atenuação a cada 5 minutos e um resumo completo uma vez a mitigação de ataque é efetuada através de. 

1. Selecione **todos os serviços** na parte superior, à esquerda do portal.
2. Introduza *Monitor* no **filtro** caixa. Quando **Monitor** aparecer nos resultados, selecione.
3. Sob **configurações**, selecione **das definições de diagnóstico**.
4. Selecione o **subscrição** e **grupo de recursos** que contêm o endereço IP público para registrar em log.
5. Selecione **endereço IP público** para **tipo de recurso**, em seguida, selecione o endereço IP público específico, para registrar em log as métricas para.
6. Selecione **ativar os diagnósticos para recolher o registo de DDoSMitigationReports** e, em seguida, selecione o máximo das seguintes opções que forem necessárias:

    - **Arquivo para uma conta de armazenamento**: Dados são gravados para uma conta de armazenamento do Azure. Para saber mais sobre esta opção, veja [arquivar os registos de diagnóstico](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Stream para um hub de eventos**: Permite que um destinatário de registo recolher registos com um Hub de eventos do Azure. Os hubs de eventos permitem a integração com Splunk ou outros sistemas SIEM. Para saber mais sobre esta opção, veja [Stream registos de diagnóstico para um hub de eventos](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Enviar para o Log Analytics**: Escreve os registos para o serviço do Azure Monitor. Para saber mais sobre esta opção, veja [recolher registos para utilização nos registos do Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Os relatórios de atenuação de incremental e posteriores do ataque incluem os seguintes campos
- Vetores de ataque
- Estatísticas de tráfego
- Motivo de pacotes ignorados
- Protocolos envolvidos
- Principais 10 países de origem ou regiões
- Origem de 10 principais ASNs

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Configurar registos de fluxo de mitigação de ataque de DDoS
Registos de fluxo de mitigação de ataque permite-lhe rever o tráfego ignorado, reencaminhados de tráfego e outros datapoints interessante durante um ataque de DDoS ativo em tempo quase real. Ingestão de transmissão em fluxo constante destes dados nos sistemas SIEM através do hub de eventos para a monitorização em tempo quase real, efetuar ações possíveis e atender à necessidade de suas operações de defesa. 

1. Selecione **todos os serviços** na parte superior, à esquerda do portal.
2. Introduza *Monitor* no **filtro** caixa. Quando **Monitor** aparecer nos resultados, selecione.
3. Sob **configurações**, selecione **das definições de diagnóstico**.
4. Selecione o **subscrição** e **grupo de recursos** que contêm o endereço IP público para registrar em log.
5. Selecione **endereço IP público** para **tipo de recurso**, em seguida, selecione o endereço IP público específico, para registrar em log as métricas para.
6. Selecione **ativar os diagnósticos para recolher o registo de DDoSMitigationFlowLogs** e, em seguida, selecione o máximo das seguintes opções que forem necessárias:

    - **Arquivo para uma conta de armazenamento**: Dados são gravados para uma conta de armazenamento do Azure. Para saber mais sobre esta opção, veja [arquivar os registos de diagnóstico](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Stream para um hub de eventos**: Permite que um destinatário de registo recolher registos com um Hub de eventos do Azure. Os hubs de eventos permitem a integração com Splunk ou outros sistemas SIEM. Para saber mais sobre esta opção, veja [Stream registos de diagnóstico para um hub de eventos](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Enviar para o Log Analytics**: Escreve os registos para o serviço do Azure Monitor. Para saber mais sobre esta opção, veja [recolher registos para utilização nos registos do Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Para ver os dados de registos de fluxo no dashboard de análise do Azure, pode importar o dashboard de exemplo do https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Os registos de fluxo terá os seguintes campos: 
- IP de origem
- ID de destino
- Porta de origem 
- Porta de destino 
- Tipo de protocolo 
- Ação executada durante a atenuação



## <a name="validate-ddos-detection"></a>Validar a deteção de DDoS

Microsoft associou [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para criar uma interface onde pode gerar tráfego em relação a ativar a proteção de DDoS os endereços IP públicos para simulações. A simulação de ponto de interrupção Cloud permite-lhe:

- Validar a como o Microsoft Azure DDoS Protection protege os recursos do Azure contra ataques DDoS
- Otimizar seu processo de resposta a incidentes mesmo sob ataque de DDoS
- Conformidade de DDoS de documento
- Preparar as suas equipes de segurança de rede

## <a name="permissions"></a>Permissões

Para trabalhar com planos de proteção DDoS, deve ser atribuída a sua conta para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) função que é atribuída as ações adequadas listadas na tabela a seguir:

| Ação                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Ler um plano de proteção DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Criar ou atualizar um plano de proteção DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Eliminar um plano de proteção DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Junte-se a um plano de proteção DDoS              |

Para ativar a proteção de DDoS para uma rede virtual, a conta também deve ser atribuída a apropriado [ações para as redes virtuais](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>Passos Seguintes

- Criar e aplicar [o Azure policy](policy-samples.md) para redes virtuais