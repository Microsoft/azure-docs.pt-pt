---
title: Gerir o Padrão de Proteção Azure DDoS utilizando o portal Azure
titlesuffix: Azure Virtual Network
description: Aprenda a usar a telemetria Standard de Proteção Azure DDoS no Azure Monitor para mitigar um ataque.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 706379649b47846b5c020dc76493a98e346c4a8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317689"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gerir o Padrão de Proteção Azure DDoS utilizando o portal Azure

Aprenda a ativar e desativar a proteção de negação de serviço distribuída (DDoS) e utilize a telemetria para mitigar um ataque DDoS com a Norma de Proteção DDoS do Azure. A DDoS Protection Standard protege os recursos do Azure, tais como máquinas virtuais, equiliblos de carga e gateways de aplicações que têm um [endereço IP público](virtual-network-public-ip-address.md) Azure atribuído a ele. Para saber mais sobre a Norma de Proteção DDoS e as suas capacidades, consulte [a visão geral do DDoS Protection Standard](ddos-protection-overview.md).

Antes de completar quaisquer etapas neste tutorial, inicie sessão no portal Azure https://portal.azure.com com uma conta atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações [apropriadas listadas](#permissions-and-restrictions)em Permissões .

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-ddos-protection-plan"></a>Criar um plano de proteção DDoS

Um plano de proteção DDoS define um conjunto de redes virtuais que têm o padrão de proteção DDoS ativado, através de subscrições. Pode configurar um plano de proteção DDoS para a sua organização e ligar redes virtuais de várias subscrições ao mesmo plano. O próprio Plano de Proteção DDoS está também associado a uma subscrição, que seleciona durante a criação do plano. O Plano de Proteção do DDoS funciona em regiões e assinaturas. Exemplo - você pode criar o plano na Região Leste-EUA e ligar-se à subscrição #1 no seu inquilino. O mesmo plano pode ser ligado a redes virtuais de outras subscrições em diferentes regiões, através do seu inquilino. A subscrição do plano está associada a incorrer na fatura mensal recorrente do plano, bem como taxas de sobrecarga, caso o número de endereços IP públicos protegidos exceda 100. Para obter mais informações sobre os preços do DDoS, consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/ddos-protection/)

A criação de mais do que um plano não é necessária para a maioria das organizações. Um plano não pode ser movido entre subscrições. Se quiser alterar a subscrição em que se encontra um plano, tem de [eliminar o plano existente](#work-with-ddos-protection-plans) e criar um novo.

1. **Selecione Criar um recurso** no canto superior esquerdo do portal Azure.
2. Procure *por DDoS*. Quando **o plano de proteção DDoS** aparecer nos resultados da pesquisa, selecione-o.
3. Selecione **Criar**.
4. Insira ou selecione os seus próprios valores, ou introduza, ou selecione os seguintes valores de exemplo e, em seguida, **selecione Criar**:

    |Definição        |Valor                                              |
    |---------      |---------                                          |
    |Nome           | myDdosProtectionPlan                              |
    |Subscrição   | Selecione a sua subscrição.                         |
    |Grupo de recursos | Selecione **Criar novo** e insira *o myResourceGroup* |
    |Localização       | E.U.A Leste                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Ativar o DDoS para uma nova rede virtual

1. **Selecione Criar um recurso** no canto superior esquerdo do portal Azure.
2. Selecione **Redes** e, em seguida, selecione **Rede virtual**.
3. Introduza ou selecione os seus próprios valores, de introduzir ou selecionar os seguintes valores de exemplo, aceitar os predefinidos restantes e, em seguida, selecionar **Criar**:

    | Definição         | Valor                                                        |
    | ---------       | ---------                                                    |
    | Nome            | myVirtualNetwork                                             |
    | Subscrição    | Selecione a sua subscrição.                                    |
    | Grupo de recursos  | Selecione **Utilizar a utilização existente**e, em seguida, selecione **myResourceGroup** |
    | Localização        | E.U.A Leste                                                      |
    | Padrão de Proteção DDos | Selecione **Ativar**. O plano que seleciona pode ser na mesma, ou subscrição diferente da rede virtual, mas ambas as subscrições devem estar associadas ao mesmo inquilino do Azure Ative Directory.|

Não é possível mover uma rede virtual para outro grupo de recursos ou subscrição quando o DDoS Standard estiver ativado para a rede virtual. Se precisar de mover uma rede virtual com o DDoS Standard ativado, desative primeiro a DDoS Standard, mova a rede virtual e, em seguida, ative a norma DDoS. Após a mudança, os limiares de política afinados automaticamente para todos os endereços IP públicos protegidos na rede virtual são reiniciados.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Ativar o DDoS para uma rede virtual existente

1. Crie um plano de proteção DDoS preenchendo as etapas no [Criar um plano de proteção DDoS,](#create-a-ddos-protection-plan)se não tiver um plano de proteção DDoS existente.
2. **Selecione Criar um recurso** no canto superior esquerdo do portal Azure.
3. Insira o nome da rede virtual que pretende ativar a Norma de Proteção DDoS para os **recursos de pesquisa, serviços e caixa** de docs no topo do portal. Quando o nome da rede virtual aparecer nos resultados da pesquisa, selecione-o.
4. Selecione **a proteção DDoS,** em **DEFINIÇÕES**.
5. Selecione **Standard**. No âmbito **do plano de proteção DDoS,** selecione um plano de proteção DDoS existente ou o plano que criou no passo 1 e, em seguida, selecione **Save**. O plano que seleciona pode ser na mesma, ou subscrição diferente da rede virtual, mas ambas as subscrições devem estar associadas ao mesmo inquilino do Azure Ative Directory.

**Comandos** 
- Azure CLI: [az rede ddos-proteção criar](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create)
- Powershell: [Plano de Proteção de Novos AzDdos](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>Desativar o DDoS para uma rede virtual

1. Introduza o nome da rede virtual para desativar o padrão de proteção DDoS na caixa de **recursos, serviços e docs de pesquisa** no topo do portal. Quando o nome da rede virtual aparecer nos resultados da pesquisa, selecione-o.
2. Selecione **de acordo com a norma de proteção DDoS,** selecione **Desativar**.

**Comandos** 
- Azure CLI: [az rede ddos-proteção eliminar](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete)
- Powershell: [Remove-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>Trabalhar com planos de proteção DDoS

1. Selecione **Todos os serviços** em cima, à esquerda do portal.
2. Introduza *o DDoS* na caixa **do filtro.** Quando **os planos de proteção DDoS** aparecerem nos resultados, selecione-os.
3. Selecione o plano de proteção que pretende visualizar na lista.
4. Todas as redes virtuais associadas ao plano estão listadas.
5. Se quiser eliminar um plano, tem primeiro de dissociar todas as redes virtuais do mesmo. Para dissociar um plano a partir de uma rede virtual, consulte [ODS de Desativação para uma rede virtual](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Alertas de configuração para métricas de proteção DDoS

Pode selecionar qualquer uma das métricas de proteção DDoS disponíveis para alertá-lo quando houver uma mitigação ativa durante um ataque, utilizando a configuração de alerta do Monitor Azure. Quando as condições são satisfeitas, o endereço especificado recebe um e-mail de alerta:

1. Selecione **Todos os serviços** em cima, à esquerda do portal.
2. Introduza o *Monitor* na caixa **do filtro.** Quando **o Monitor** aparecer nos resultados, selecione-o.
3. Selecione **métricas** em **SERVIÇOS PARTILHADOS**.
4. Insira, ou selecione os seus próprios valores, ou introduza os seguintes valores de exemplo, aceite os predefinidos restantes e, em seguida, selecione **OK**:

    |Definição                  |Valor                                                                                               |
    |---------                |---------                                                                                           |
    |Nome                     | myDdosAlert                                                                                        |
    |Subscrição             | Selecione a subscrição que contém o endereço IP público para o quais pretende receber alertas.        |
    |Grupo de recursos           | Selecione o grupo de recursos que contém o endereço IP público para o quais pretende receber alertas.      |
    |Recurso                 | Selecione o endereço IP público que contém o endereço IP público para o quais pretende receber alertas. O DDoS monitoriza endereços IP públicos atribuídos a recursos dentro de uma rede virtual. Se não tiver recursos com endereços IP públicos na rede virtual, tem primeiro de criar um recurso com um endereço IP público. Pode monitorizar o endereço IP público de todos os recursos implantados através do Gestor de Recursos (não clássico) listados na [rede Virtual para serviços Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), exceto para Azure App Service Environments e Azure VPN Gateway. Para continuar com este tutorial, pode rapidamente criar uma máquina virtual [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)                   |
    |Métrica                   | Sob o ataque do DDoS ou não                                                                            |
    |Limiar                | 1 - **1** significa que estás a ser atacado. **0** significa que não estás a ser atacado.                         |
    |Período                   | Selecione o valor que escolher.                                                                   |
    |Notificar via E-mail         | marque a caixa de verificação                                                                                  |
    |Administrador adicional | Insira o seu endereço de e-mail se não for proprietário de e-mail, colaborador ou leitor para a subscrição. |

    Em poucos minutos após a deteção de ataques, recebe um e-mail de métricas do Azure Monitor que se parece com a seguinte imagem:

    ![Alerta de ataque](./media/manage-ddos-protection/ddos-alert.png)


Para simular um ataque DDoS para validar o seu alerta, consulte [validar a deteção de DDoS](#validate-ddos-detection).

Também pode aprender mais sobre [configurar webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [aplicações lógicas](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criar alertas.

## <a name="use-ddos-protection-telemetry"></a>Utilizar telemetria de proteção DDoS

A telemetria para um ataque é fornecida através do Monitor Azure em tempo real. A telemetria só está disponível durante o período em que um endereço IP público esteja sob mitigação. Não se vê telemetria antes ou depois de um ataque ser atenuado.

1. Selecione **Todos os serviços** em cima, à esquerda do portal.
2. Introduza o *Monitor* na caixa **do filtro.** Quando **o Monitor** aparecer nos resultados, selecione-o.
3. Selecione **Métricas,** em **SERVIÇOS PARTILHADOS**.
4. Selecione o grupo **de Subscrição** e **Recursos** que contenha o endereço IP público para o quais deseja telemetria.
5. Selecione **Endereço IP Público** para o tipo de **recurso,** em seguida, selecione o endereço IP público específico para o quais deseja telemetria.
6. Uma série de **Métricas Disponíveis** aparecem no lado esquerdo do ecrã. Estas métricas, quando selecionadas, são grafadas na **Tabela de Métricas do Monitor Azure** no ecrã geral.
7. Selecione o tipo **de agregação** como **Max**

Os nomes métricos apresentam diferentes tipos de pacotes, e bytes vs. pacotes, com uma construção básica de nomes de etiquetas em cada métrica da seguinte forma:

- **Nome da etiqueta de queda** (por exemplo, **Pacotes de Entrada Deixou Cair DDoS**): O número de pacotes caídos/esfregados pelo sistema de proteção DDoS.
- **Nome de etiqueta reencaminhado** (por **exemplo, Pacotes de Entrada Reencaminhados DDoS**): Número de pacotes reencaminhados pelo sistema DDoS para o destino VIP – tráfego que não foi filtrado.
- **Sem nome de identificação** (por **exemplo, Pacotes de Entrada DDoS**): O número total de pacotes que entraram no sistema de esfregar – representando a soma dos pacotes caídos e reencaminhados.

Para simular um ataque DDoS para validar a telemetria, consulte [validar a deteção de DDoS](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Ver políticas de mitigação do DDoS

A Norma de Proteção DDoS aplica três políticas de mitigação auto-sintonizadas (TCP SYN, TCP & UDP) para cada endereço IP público do recurso protegido, na rede virtual que tem DDoS ativado. Pode ver os limiares de política selecionando os  **pacotes TCP de entrada para desencadear pacotes de mitigação de DDoS** e UDP de entrada para ativar métricas **de mitigação de DDoS** com tipo **de agregação** como 'Max', como mostra a seguinte imagem:

![Ver políticas de mitigação](./media/manage-ddos-protection/view-mitigation-policies.png)

Os limiares de política são configurados automaticamente através do perfil de tráfego de rede baseado em aprendizagem automática Azure. Só quando o limiar da apólice é violado é que a mitigação do DDoS ocorre para o endereço IP sob ataque.

## <a name="configure-ddos-attack-analytics"></a>Configurar a análise do ataque DDoS
A padrão de proteção DDoS Azure fornece informações detalhadas sobre ataque e visualização com DDoS Attack Analytics. Os clientes que protegem as suas redes virtuais contra ataques de DDoS têm uma visibilidade detalhada no tráfego de ataques e as ações tomadas para mitigar o ataque através de relatórios de mitigação de ataques & registos de fluxo de mitigação. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Configura relatórios de mitigação de ataques do DDoS
Os relatórios de mitigação de ataques utilizam os dados do protocolo Netflow que são agregados para fornecer informações detalhadas sobre o ataque ao seu recurso. Sempre que um recurso IP público estiver a ser atacado, a geração do relatório começará assim que a mitigação começar. Haverá um relatório incremental gerado a cada 5 minutos e um relatório pós-mitigação para todo o período de mitigação. Isto é para garantir que, caso o ataque do DDoS continue por um período mais longo, você será capaz de ver o instantâneo mais atual do relatório de atenuação a cada 5 minutos e um resumo completo uma vez que a mitigação do ataque termina. 

1. Selecione **Todos os serviços** em cima, à esquerda do portal.
2. Introduza o *Monitor* na caixa **do filtro.** Quando **o Monitor** aparecer nos resultados, selecione-o.
3. Em **DEFINIÇÕES**, selecione **Definições de diagnóstico**.
4. Selecione o grupo **de Subscrição** e **Recursos** que contenha o endereço IP público que pretende registar.
5. Selecione **o Endereço IP Público** para o tipo de **recurso,** em seguida, selecione o endereço IP público específico para o quais pretende registar métricas.
6. Selecione **Ligue os diagnósticos para recolher o registo DDoSMitigationReports** e, em seguida, selecione quantas opções for necessária:

    - **Arquivar para uma conta de armazenamento**: Os dados são escritos numa conta de Armazenamento Azure. Para saber mais sobre esta opção, consulte [os registos de recursos do Arquivo.](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - **Stream para um centro de eventos**: Permite que um recetor de log recolha registos usando um Azure Event Hub. Os centros de eventos permitem a integração com a Splunk ou outros sistemas SIEM. Para saber mais sobre esta opção, consulte [os registos de recursos do Stream para um centro de eventos.](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - **Enviar para Log Analytics**: Escreve registos para o serviço Azure Monitor. Para saber mais sobre esta opção, consulte [recolher registos para utilização nos registos do Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Tanto os relatórios incrementais de mitigação & pós-ataque incluem os seguintes campos
- Vetores de ataque
- Estatísticas de tráfego
- Razão para pacotes abandonados
- Protocolos envolvidos
- Top 10 países ou regiões de origem
- Top 10 asns de origem

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Configurar registos de fluxo de atenuação de ataques DDoS
Os Registos de Fluxo de Mitigação de Ataques permitem-lhe rever o tráfego desatado, tráfego reencaminhado e outros pontos de dados interessantes durante um ataque DDoS ativo em tempo quase real. Pode ingerir o fluxo constante destes dados nos seus sistemas SIEM através do centro de eventos para monitorização em tempo quase real, tomar potenciais ações e responder à necessidade das suas operações de defesa. 

1. Selecione **Todos os serviços** em cima, à esquerda do portal.
2. Introduza o *Monitor* na caixa **do filtro.** Quando **o Monitor** aparecer nos resultados, selecione-o.
3. Em **DEFINIÇÕES**, selecione **Definições de diagnóstico**.
4. Selecione o grupo **de Subscrição** e **Recursos** que contenha o endereço IP público que pretende registar.
5. Selecione **o Endereço IP Público** para o tipo de **recurso,** em seguida, selecione o endereço IP público específico para o quais pretende registar métricas.
6. Selecione **Ligue os diagnósticos para recolher o registo DDoSMitigationFlowLogs** e, em seguida, selecione quantas opções for necessária:

    - **Arquivar para uma conta de armazenamento**: Os dados são escritos numa conta de Armazenamento Azure. Para saber mais sobre esta opção, consulte [os registos de recursos do Arquivo.](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - **Stream para um centro de eventos**: Permite que um recetor de log recolha registos usando um Azure Event Hub. Os centros de eventos permitem a integração com a Splunk ou outros sistemas SIEM. Para saber mais sobre esta opção, consulte [os registos de recursos do Stream para um centro de eventos.](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - **Enviar para Log Analytics**: Escreve registos para o serviço Azure Monitor. Para saber mais sobre esta opção, consulte [recolher registos para utilização nos registos do Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Para visualizar os dados de registos de fluxo no Azure analytics Workbook, você pode importar o painel de amostras de https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook

Os registos de fluxo terão os seguintes campos: 
- IP de origem
- IP de destino
- Porta de origem 
- Porta de destino 
- Tipo de protocolo 
- Medidas tomadas durante a mitigação

A análise de ataque só funcionará se a Norma de Proteção DDoS estiver ativada na rede virtual do endereço IP público. 

## <a name="validate-ddos-detection"></a>Validar a deteção de DDoS

A Microsoft estabeleceu uma parceria com a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para construir uma interface onde pode gerar tráfego contra endereços IP públicos ativados pela Proteção DDoS para simulações. A simulação breakpoint cloud permite-lhe:

- Valide como a Microsoft Azure DDoS Protection protege os seus recursos Azure de ataques DDoS
- Otimize o seu processo de resposta a incidentes sob ataque DDoS
- Conformidade do DDoS do documento
- Treine as suas equipas de segurança de rede

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Ver alertas de proteção DDoS no Centro de Segurança Azure

O Azure Security Center fornece uma lista de alertas de [segurança,](/azure/security-center/security-center-managing-and-responding-alerts)com informações para ajudar a investigar e corrigir problemas. Com esta funcionalidade, obtém-se uma visão unificada dos alertas, incluindo alertas relacionados com ataques DDoS e as ações tomadas para mitigar o ataque em pouco tempo.
Existem dois alertas específicos que verá para qualquer deteção e mitigação de ataques DDoS:

- **Ataque DDoS detetado para IP Público**: Este alerta é gerado quando o serviço de proteção DDoS deteta que um dos seus endereços IP públicos é alvo de um ataque DDoS.
- **Ataque DDoS atenuado para IP público**: Este alerta é gerado quando um ataque ao endereço IP público foi atenuado.
Para ver os alertas, abra o **Centro de Segurança** no portal Azure. Sob **proteção contra**ameaças , selecione **alertas de segurança**. A imagem que se segue mostra um exemplo dos alertas de ataque do DDoS.

![Alerta DDoS no Centro de Segurança Azure](./media/manage-ddos-protection/ddos-alert-asc.png)

Os alertas incluem informações gerais sobre o endereço IP público que está sob ataque, informações de inteligência geo e ameaças, e medidas de reparação.

## <a name="permissions-and-restrictions"></a>Permissões e restrições

Para trabalhar com planos de proteção DDoS, a sua conta deve ser atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações apropriadas listadas no quadro seguinte:

| Ação                                            | Nome                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Leia um plano de proteção DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Criar ou atualizar um plano de proteção DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Eliminar um plano de proteção DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Aderir a um plano de proteção DDoS              |

Para permitir a proteção do DDoS para uma rede virtual, a sua conta também deve ser atribuída [às ações apropriadas para redes virtuais.](manage-virtual-network.md#permissions)

### <a name="azure-policy"></a>Azure Policy
Para clientes que tenham várias subscrições, e que queiram garantir um único plano para a Norma de Proteção DDoS Azure é implementado em todo o seu inquilino para controlo de custos, você pode usar a Azure Policy para restringir a [criação de planos Azure DDoS Protection Standard](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Restrict%20creation%20of%20Azure%20DDoS%20Protection%20Standard%20Plans%20with%20Azure%20Policy). Esta política bloqueará a criação de quaisquer planos DDoS, a menos que a subscrição tenha sido previamente marcada como uma exceção. Esta política também mostrará uma lista de todas as subscrições que têm um plano DDoS implementado mas não devem, marcando-as como fora do cumprimento. 

## <a name="next-steps"></a>Passos seguintes

- Criar e atribuir [definições de Política Azure](policy-samples.md) para redes virtuais
