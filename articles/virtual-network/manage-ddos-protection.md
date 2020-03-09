---
title: Gerir a Norma de Proteção DDoS Azure utilizando o portal Azure
titlesuffix: Azure Virtual Network
description: Aprenda a usar a telemetria Azure DDoS Protection Standard no Monitor Azure para mitigar um ataque.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390735"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gerir a Norma de Proteção DDoS Azure utilizando o portal Azure

Aprenda a ativar e desativar a proteção de negação de serviço distribuída (DDoS) e use a telemetria para mitigar um ataque DDoS com a Norma de Proteção DDoS Azure. A DDoS Protection Standard protege os recursos do Azure, tais como máquinas virtuais, equilibradores de carga e gateways de aplicação que têm um [endereço IP público](virtual-network-public-ip-address.md) Azure atribuído ao mesmo. Para saber mais sobre o Padrão de Proteção DDoS e as suas capacidades, consulte a visão geral do Padrão de [Proteção dDoS](ddos-protection-overview.md).

Antes de concluir quaisquer etapas neste tutorial, inicie sessão no portal Azure em https://portal.azure.com com uma conta atribuída à função de colaborador da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações apropriadas listadas nas [Permissões.](#permissions)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-ddos-protection-plan"></a>Criar um plano de proteção DDoS

Um plano de proteção DDoS define um conjunto de redes virtuais que têm um padrão de proteção DDoS ativado, através de subscrições. Pode configurar um plano de proteção DDoS para a sua organização e ligar redes virtuais de várias subscrições ao mesmo plano. O Próprio Plano de Proteção dDoS está também associado a uma subscrição, que seleciona durante a criação do plano. O Plano de Proteção dDoS funciona em regiões e subscrições. Exemplo - você pode criar o plano na Região Leste-EUA e ligar-se a #1 de subscrição no seu inquilino. O mesmo plano pode estar ligado a redes virtuais de outras subscrições em diferentes regiões, em todo o seu inquilino. A subscrição do plano está associada a incorrer na fatura mensal recorrente para o plano, bem como encargos com excesso de idade, caso o número de endereços IP públicos protegidos exceda 100. Para obter mais informações sobre os preços do DDoS, consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/ddos-protection/)

A criação de mais de um plano não é necessária para a maioria das organizações. Um plano não pode ser movido entre assinaturas. Se quiser alterar a subscrição em que está um plano, tem de [apagar o plano existente](#work-with-ddos-protection-plans) e criar um novo.

1. Selecione **Criar um recurso** no canto superior esquerdo do portal Azure.
2. Pesquisa por *DDoS*. Quando o plano de **proteção DDos** aparecer nos resultados da pesquisa, selecione-o.
3. Selecione **Criar**.
4. Introduza ou selecione os seus próprios valores, ou introduza, ou selecione os seguintes valores de exemplo, e, em seguida, **selecione Criar:**

    |Definição        |Valor                                              |
    |---------      |---------                                          |
    |Nome           | myDdosProtectionPlan                              |
    |Subscrição   | Selecione a sua subscrição.                         |
    |Grupo de recursos | Selecione **Criar novo** e insira *o myResourceGroup* |
    |Localização       | E.U.A. Leste                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Ativar o DDoS para uma nova rede virtual

1. Selecione **Criar um recurso** no canto superior esquerdo do portal Azure.
2. Selecione **Redes** e, em seguida, selecione **Rede virtual**.
3. Introduza ou selecione os seus próprios valores, introduza ou selecione os seguintes valores exemplo, aceite os restantes predefinições e, em seguida, selecione **Criar:**

    | Definição         | Valor                                                        |
    | ---------       | ---------                                                    |
    | Nome            | myVirtualNetwork                                             |
    | Subscrição    | Selecione a sua subscrição.                                    |
    | Grupo de recursos  | Selecione **Utilizar existente** e, em seguida, **myResourceGroup** |
    | Localização        | E.U.A. Leste                                                      |
    | Proteção dDos | Selecione **Standard** e, em seguida, sob **a proteção DDoS,** selecione **myDdosProtectionPlan**. O plano que selecionar pode estar na mesma subscrição, ou subscrição diferente da rede virtual, mas ambas as subscrições devem ser associadas ao mesmo inquilino do Azure Ative Directory.|

Não é possível mover uma rede virtual para outro grupo de recursos ou subscrição quando o DDoS Standard está ativado para a rede virtual. Se precisar de mover uma rede virtual com dDoS Standard ativado, desative primeiro o DDoS Standard, mova a rede virtual e, em seguida, ative a norma DDoS. Após a mudança, os limiares de política afinados automaticamente para todos os endereços IP públicos protegidos na rede virtual são redefinidos.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Ativar o DDoS para uma rede virtual existente

1. Crie um plano de proteção DDoS completando os passos em Criar um plano de [proteção DDoS](#create-a-ddos-protection-plan), se não tiver um plano de proteção DDoS existente.
2. Selecione **Criar um recurso** no canto superior esquerdo do portal Azure.
3. Introduza o nome da rede virtual que pretende ativar o Padrão de Proteção DDoS nos **recursos de Pesquisa, serviços e caixa de docs** no topo do portal. Quando o nome da rede virtual aparecer nos resultados da pesquisa, selecione-o.
4. Selecione **a proteção DDoS**, em **DEFINIÇÕES**.
5. Selecione **Standard**. De acordo com o plano de **proteção DDoS,** selecione um plano de proteção DDoS existente ou o plano que criou no passo 1 e, em seguida, selecione **Save**. O plano que selecionar pode estar na mesma subscrição, ou subscrição diferente da rede virtual, mas ambas as subscrições devem ser associadas ao mesmo inquilino do Azure Ative Directory.

**Comandos** 
- Azure CLI: [az rede ddos-protection criar](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create)
- Powershell: [New-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>Desativar dDoS para uma rede virtual

1. Introduza o nome da rede virtual para a sua desativação do padrão de proteção DDoS nos **recursos de Pesquisa, serviços e caixa de docs** no topo do portal. Quando o nome da rede virtual aparecer nos resultados da pesquisa, selecione-o.
2. Selecione **a proteção DDoS**, em **DEFINIÇÕES**.
3. Selecione **Basic** ao abrigo do plano de **proteção DDoS** e, em seguida, selecione **Guardar**.

**Comandos** 
- Azure CLI: [eliminação da proteção de ddos da rede az](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete)
- Powershell: [Remove-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>Trabalhar com planos de proteção DDoS

1. Selecione **Todos os serviços** na parte superior, à esquerda do portal.
2. Introduza *DDoS* na caixa **de filtro.** Quando os planos de **proteção dDoS** aparecerem nos resultados, selecione-os.
3. Selecione o plano de proteção que pretende ver na lista.
4. Todas as redes virtuais associadas ao plano estão listadas.
5. Se quiser apagar um plano, primeiro deve dissociar todas as redes virtuais do mesmo. Para dissociar um plano de uma rede virtual, consulte [Desativar dDoS para uma rede virtual](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Configure alertas para métricas de proteção DDoS

Pode selecionar qualquer uma das métricas de proteção DDoS disponíveis para alertá-lo quando há uma mitigação ativa durante um ataque, utilizando a configuração de alerta Do Monitor Azure. Quando as condições são satisfeitas, o endereço especificado recebe um e-mail de alerta:

1. Selecione **Todos os serviços** na parte superior, à esquerda do portal.
2. Introduza o *Monitor* na caixa **de filtro.** Quando o **Monitor** aparecer nos resultados, selecione-o.
3. Selecione **Métricas** em **SERVIÇOS PARTILHADOS**.
4. Introduza, ou selecione os seus próprios valores, ou introduza os seguintes valores exemplo, aceite os restantes predefinições e, em seguida, selecione **OK:**

    |Definição                  |Valor                                                                                               |
    |---------                |---------                                                                                           |
    |Nome                     | myDdosAlert                                                                                        |
    |Subscrição             | Selecione a subscrição que contém o endereço IP público para o que pretende receber alertas.        |
    |Grupo de recursos           | Selecione o grupo de recursos que contém o endereço IP público para o que pretende receber alertas.      |
    |Recurso                 | Selecione o endereço IP público que contém o endereço IP público para o que pretende receber alertas. O DDoS monitoriza os endereços IP públicos atribuídos aos recursos dentro de uma rede virtual. Se não tiver recursos com endereços IP públicos na rede virtual, tem primeiro de criar um recurso com um endereço IP público. Pode monitorizar o endereço IP público de todos os recursos utilizados através do Gestor de Recursos (não clássico) listados na [rede Virtual para serviços Azure,](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)com exceção dos Ambientes de Serviço de Aplicações Azure e do Azure VPN Gateway. Para continuar com este tutorial, pode criar rapidamente uma máquina virtual [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)                   |
    |Métrica                   | Sob ataque DDoS ou não                                                                            |
    |Limiar                | 1 - **1** significa que está a ser atacado. **0** significa que não está saqueado.                         |
    |Período                   | Selecione o valor que escolher.                                                                   |
    |Notificar via E-mail         | Verifique a caixa de verificação                                                                                  |
    |Administrador adicional | Insira o seu endereço de e-mail se não for proprietário de e-mail, colaborador ou leitor para a subscrição. |

    Poucos minutos após a deteção do ataque, recebe um e-mail das métricas do Monitor Do Azure que se parece com a seguinte imagem:

    ![Alerta de ataque](./media/manage-ddos-protection/ddos-alert.png)


Para simular um ataque DDoS para validar o seu alerta, consulte [a deteção de DDoS validado](#validate-ddos-detection).

Também pode aprender mais sobre [configurar webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [aplicações lógicas](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criar alertas.

## <a name="use-ddos-protection-telemetry"></a>Utilize telemetria de proteção DDoS

A telemetria para um ataque é fornecida através do Monitor Azure em tempo real. A telemetria só está disponível durante a duração em que um endereço IP público esteja em mitigação. Não se vê telemetria antes ou depois de um ataque ser atenuado.

1. Selecione **Todos os serviços** na parte superior, à esquerda do portal.
2. Introduza o *Monitor* na caixa **de filtro.** Quando o **Monitor** aparecer nos resultados, selecione-o.
3. Selecione **Métricas,** em **serviços partilhados.**
4. Selecione o grupo **de Subscrição** e **Recursos** que contenha o endereço IP público para o que pretende telemetria.
5. Selecione **Endereço IP público** para o tipo de **Recurso**e, em seguida, selecione o endereço IP público específico para o que pretende telemetria.
6. Uma série de **Métricas Disponíveis** aparecem no lado esquerdo do ecrã. Estas métricas, quando selecionadas, são graficamente grafizadas no Gráfico de Métricas do **Monitor Azure** no ecrã geral.
7. Selecione o tipo **de agregação** como **Max**

Os nomes métricos apresentam diferentes tipos de pacotes, e bytes vs. pacotes, com uma construção básica de nomes de etiquetas em cada métrica da seguinte forma:

- **Nome de etiqueta largada** (por exemplo, **Pacotes de entrada caiu DDoS**): O número de pacotes caiu/esfregado pelo sistema de proteção DDoS.
- Nome de **etiqueta reencaminhado** (por **exemplo, Inbound Packets Forwarded DDoS**): O número de pacotes reencaminhados pelo sistema DDoS para o destino VIP – tráfego que não foi filtrado.
- **Sem nome** de etiqueta (por **exemplo, Pacotes de Entrada DDoS**): O número total de pacotes que entraram no sistema de esfregar – representando a soma dos pacotes caídos e encaminhadas.

Para simular um ataque DDoS para validar a telemetria, consulte [a deteção de DDoS validado](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Ver políticas de mitigação do DDoS

A Norma de Proteção DDoS aplica três políticas de mitigação auto-afinadas (TCP SYN, TCP & UDP) para cada endereço IP público do recurso protegido, na rede virtual que tem DDoS ativado. Pode visualizar os limiares de política selecionando os **pacotes TCP de entrada para desencadear a mitigação do DDoS** e **os pacotes UDP de entrada para desencadear** métricas de mitigação do DDoS com o tipo de **agregação** como 'Max', como mostra a seguinte imagem:

![Ver políticas de mitigação](./media/manage-ddos-protection/view-mitigation-policies.png)

Os limiares de política são configurados automaticamente através do perfil de tráfego de rede baseado em machine learning Azure. Só quando o limiar de política é violado é que a mitigação do DDoS ocorre para o endereço IP sob ataque.

## <a name="configure-ddos-attack-analytics"></a>Configure análise de ataque DDoS
A norma azure DDoS Protection fornece informações detalhadas de ataque e visualização com DDoS Attack Analytics. Os clientes que protegem as suas redes virtuais contra ataques dDoS têm visibilidade detalhada no tráfego de ataque e ações tomadas para mitigar o ataque através de relatórios de mitigação de ataques e registos de fluxo de mitigação. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Configure relatórios de mitigação de ataques dDoS
Os relatórios de mitigação de ataques utilizam os dados do protocolo Netflow que é agregado para fornecer informações detalhadas sobre o ataque ao seu recurso. Sempre que um recurso IP público estiver a ser atacado, a geração do relatório começará assim que a mitigação começar. Haverá um relatório incremental gerado a cada 5 minutos e um relatório pós-mitigação para todo o período de mitigação. Isto é para garantir que, em caso de o ataque do DDoS continuar por um período mais longo, poderá ver o mais atual relatório de mitigação a cada 5 minutos e um resumo completo assim que a mitigação do ataque terminar. 

1. Selecione **Todos os serviços** na parte superior, à esquerda do portal.
2. Introduza o *Monitor* na caixa **de filtro.** Quando o **Monitor** aparecer nos resultados, selecione-o.
3. Em **definições,** selecione **Definições de Diagnóstico**.
4. Selecione o grupo **de Subscrição** e **Recursos** que contenha o endereço IP público que pretende registar.
5. Selecione **Endereço IP público** para o tipo de **Recurso**e, em seguida, selecione o endereço IP público específico para o que pretende registar métricas.
6. Selecione **ligar os diagnósticos para recolher o log DDoSMitigationReports** e, em seguida, selecione quantas opções que necessitar:

    - **Arquivo para uma conta**de armazenamento : Os dados são escritos numa conta de Armazenamento Azure. Para saber mais sobre esta opção, consulte [registos de diagnóstico do Arquivo](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Stream para um centro de eventos**: Permite que um recetor de registo saqueie registos utilizando um Hub de Eventos Azure. Os centros de eventos permitem a integração com splunk ou outros sistemas SIEM. Para saber mais sobre esta opção, consulte [os registos de diagnóstico do Stream para um centro de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Enviar para Log Analytics**: Escreve registos para o serviço Azure Monitor. Para saber mais sobre esta opção, consulte [registos de recolha para utilização em registos do Monitor Azure](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Tanto os relatórios incrementais e pós-ataque incluem os seguintes campos
- Vetores de ataque
- Estatísticas do tráfego
- Razão para pacotes abandonados
- Protocolos envolvidos
- 10 países ou regiões do top 10
- Top 10 fonte ASNs

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Configure os registos de fluxo de mitigação de ataques DDoS
Os Registos de Fluxo de Mitigação de Ataque permitem-lhe rever o tráfego perdido, tráfego reencaminhado e outros pontos de dados interessantes durante um ataque ativo do DDoS em tempo quase real. Pode ingerir o fluxo constante destes dados nos seus sistemas SIEM através do centro de eventos para monitorização em tempo quase real, tomar potenciais ações e responder à necessidade das suas operações de defesa. 

1. Selecione **Todos os serviços** na parte superior, à esquerda do portal.
2. Introduza o *Monitor* na caixa **de filtro.** Quando o **Monitor** aparecer nos resultados, selecione-o.
3. Em **definições,** selecione **Definições de Diagnóstico**.
4. Selecione o grupo **de Subscrição** e **Recursos** que contenha o endereço IP público que pretende registar.
5. Selecione **Endereço IP público** para o tipo de **Recurso**e, em seguida, selecione o endereço IP público específico para o que pretende registar métricas.
6. Selecione **ligar os diagnósticos para recolher o log DDoSMitigationFlowLogs** e, em seguida, selecione quantas das seguintes opções conforme precisar:

    - **Arquivo para uma conta**de armazenamento : Os dados são escritos numa conta de Armazenamento Azure. Para saber mais sobre esta opção, consulte [registos de diagnóstico do Arquivo](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Stream para um centro de eventos**: Permite que um recetor de registo saqueie registos utilizando um Hub de Eventos Azure. Os centros de eventos permitem a integração com splunk ou outros sistemas SIEM. Para saber mais sobre esta opção, consulte [os registos de diagnóstico do Stream para um centro de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Enviar para Log Analytics**: Escreve registos para o serviço Azure Monitor. Para saber mais sobre esta opção, consulte [registos de recolha para utilização em registos do Monitor Azure](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Para ver os dados de registos de fluxo no painel de análise azure, pode importar o painel de dados da amostra a partir de https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Os registos de fluxo terão os seguintes campos: 
- IP de origem
- ID de destino
- Porta de origem 
- Porta de destino 
- Tipo de protocolo 
- Medidas tomadas durante a mitigação



## <a name="validate-ddos-detection"></a>Validar a deteção de DDoS

A Microsoft estabeleceu uma parceria com o [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para construir uma interface onde pode gerar tráfego contra endereços IP públicos ativados pela Proteção DDoS para simulações. A simulação breakPoint Cloud permite-lhe:

- Valide como a Proteção DDoS do Microsoft Azure protege os seus recursos Azure de ataques DDoS
- Otimize o seu processo de resposta a incidentes durante o ataque do DDoS
- Conformidade com documentods DDoS
- Treine as suas equipas de segurança da rede

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Ver alertas de proteção DDoS no Centro de Segurança Azure

O Azure Security Center fornece uma lista de alertas de [segurança,](/azure/security-center/security-center-managing-and-responding-alerts)com informações para ajudar a investigar e remediar problemas. Com esta funcionalidade, obtém-se uma visão unificada de alertas, incluindo alertas relacionados com ataques dDoS e as ações tomadas para mitigar o ataque em pouco tempo.
Existem dois alertas específicos que verá para qualquer deteção e mitigação de ataques DDoS:

- **DDoS Ataque detetado para IP Público**: Este alerta é gerado quando o serviço de proteção DDoS deteta que um dos seus endereços IP públicos é alvo de um ataque DDoS.
- **DDoS Ataque atenuado para IP Público**: Este alerta é gerado quando um ataque ao endereço IP público foi atenuado.
Para ver os alertas, abra o Centro de **Segurança** no portal Azure. Sob **Proteção contra Ameaças,** selecione **alertas de segurança**. A imagem que se segue mostra um exemplo dos alertas de ataque dDoS.

![Alerta DDoS no Centro de Segurança Azure](./media/manage-ddos-protection/ddos-alert-asc.png)

Os alertas incluem informações gerais sobre o endereço IP público que está sob ataque, informações de inteligência geoe ameaças, e medidas de reparação.

## <a name="permissions"></a>Permissões

Para trabalhar com os planos de proteção do DDoS, a sua conta deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações apropriadas enumeradas na tabela seguinte:

| Ação                                            | Nome                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Leia um plano de proteção DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Criar ou atualizar um plano de proteção DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Eliminar um plano de proteção DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Junte-se a um plano de proteção DDoS              |

Para permitir a proteção do DDoS para uma rede virtual, a sua conta também deve ser atribuída as ações adequadas [para redes virtuais](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>Passos seguintes

- Criar e aplicar [a política azure](policy-samples.md) para redes virtuais