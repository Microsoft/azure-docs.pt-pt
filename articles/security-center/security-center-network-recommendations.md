---
title: Proteger os seus recursos de rede no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento trata recomendações no Centro de segurança do Azure que o ajudam a proteger os seus recursos de rede do Azure e mantenha-se em conformidade com as políticas de segurança.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: monhaber
ms.openlocfilehash: 3d9658a6b7501714a6aad0e8eaa30ce587284bfe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60906351"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>Proteger os seus recursos de rede no Centro de segurança do Azure
Centro de segurança do Azure analisa continuamente o estado de segurança dos seus recursos do Azure para práticas recomendadas de segurança de rede. Quando o Centro de segurança identifica potenciais vulnerabilidades de segurança, cria recomendações que o guiar ao longo do processo de configuração de controlos necessários para proteger e proteger os seus recursos.

Este artigo aborda recomendações que se aplicam aos recursos do Azure a partir de uma perspectiva de segurança de rede. Rede do Centro de recomendações em torno de firewalls de próxima geração, grupos de segurança de rede, as regras de tráfego de entrada excessivamente permissivo do acesso JIT da VM e muito mais. Para obter uma lista de recomendações de rede e ações de remediação, consulte [recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md).

> [!NOTE]
> O **redes** página permite-lhe mergulhar no seu estado de funcionamento de recursos do Azure de uma perspectiva de rede. O mapa de rede e adaptável controlos de rede estão disponíveis para o escalão standard do Centro de segurança do Azure apenas. [Se utilizar o escalão gratuito, pode clicar no botão para **exibir rede legado** e receba recomendações de recursos de rede](#legacy-networking).
>

O **redes** página fornece uma descrição geral das seções profunda pode aprofundar para obter mais informações sobre o estado de funcionamento dos seus recursos de rede:

- Mapa de rede (apenas no escalão Standard de centro de segurança do Azure)
- Proteção de Rede Ajustável
- Recomendações de segurança de rede.
- Legado **redes** painel (o painel rede anterior) 
 
![Painel de sistema de rede](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>Mapa de rede
O mapa de rede interativo fornece uma visão gráfica sobreposições de segurança, fornecendo informações e recomendações para o sistema de proteção de seus recursos de rede. Usando o mapa pode ver a topologia de rede das cargas de trabalho do Azure, as ligações entre as máquinas virtuais e sub-redes e a capacidade de desagregar do mapa de recursos específicos e as recomendações para esses recursos.

Para abrir o mapa de rede:

1. No Centro de segurança, em higienização de segurança de recursos, selecione **redes**.
2. Sob **mapa de rede** clique em **ver a topologia**.
 
Apresenta a vista predefinida do mapa de topologia:
- Assinaturas que selecionou no Azure. O map oferece suporte a várias subscrições.
- As VMs, sub-redes e VNets do tipo de recurso do Resource Manager (Azure clássico recursos não são suportados)
- VNets em modo de peering
- Apenas os recursos que tenham [recomendações de rede](security-center-recommendations.md) com uma gravidade média ou alta  
- Recursos com acesso à Internet
- O mapa está otimizado para subscrições selecionadas no Azure. Se modificar a seleção, o mapa é recalculado e novamente otimizado com base em suas novas definições.  

![Mapa de topologia de rede](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>Noções básicas sobre o mapa de rede

O mapa de rede pode apresentar-lhe os recursos do Azure num **topologia** modo de exibição e uma **tráfego** vista.

### <a name="the-topology-view"></a>A vista de topologia

Na **topologia** vista de mapa do funcionamento em rede, pode ver as seguintes informações sobre os recursos de rede:
- O círculo interno, pode ver todas as Vnets nas suas subscrições selecionadas, o círculo seguinte representa todas as sub-redes, círculo exterior é todas as máquinas virtuais.
- As linhas que conectam os recursos no mapa permitem-lhe saber quais recursos estão associados si e como é estruturada de rede do Azure. 
- Utilize os indicadores de gravidade para obter rapidamente uma visão geral dos quais recursos têm abertas recomendações do Centro de segurança.
- Pode clicar em qualquer um dos recursos para fazer busca detalhada nos-las e ver os detalhes desse recurso e as recomendações diretamente e no contexto da rede do mapa.  
- Se existirem demasiados que recursos a ser apresentados no mapa, o Centro de segurança do Azure utiliza o seu algoritmo proprietário inteligente para seus recursos, destacando os recursos que estão no estado mais crítico e tem as recomendações de gravidade mais elevadas de cluster. 

Uma vez que o mapa é interativo e dinâmica, cada nó é clicável e pode alterar o modo de exibição com base nos filtros:

1. Pode modificar o que vê no mapa de rede, ao usar os filtros na parte superior. Pode concentrar-se o mapa com base em:
   -  **Estado de funcionamento de segurança**: Pode filtrar o mapa com base na gravidade (alta, média, baixa) de recursos do Azure.
   - **Recomendações**: Pode selecionar quais os recursos que são apresentados com base no qual recomendações estão ativas na exceção esses recursos. Por exemplo, pode ver apenas os recursos para o qual o Centro de segurança recomenda que ativar grupos de segurança de rede.
   - **Zonas de rede**: Por predefinição, o mapa apresenta apenas recursos com acesso de Internet, pode selecionar também VMs internas.
 
2. Pode clicar em **repor** no canto superior esquerdo, em qualquer altura para retornar o mapa no estado predefinido.

Para desagregar um recurso:
1. Quando seleciona um recurso específico no mapa, o painel da direita é aberto e fornece informações gerais sobre o recurso, soluções de segurança ligadas se existirem e as recomendações relevantes para o recurso. É o mesmo tipo de comportamento para cada tipo de recurso que selecionar. 
2. Quando paira o rato sobre um nó no mapa, pode ver informações gerais sobre o recurso, incluindo a subscrição, o tipo de recurso e o grupo de recursos.
3. Utilize a ligação para ampliar a dica de ferramenta e refocus mapa nesse nó específico. 
4. Para refocus o mapa para fora de um nó específico, reduzir.

### <a name="the-traffic-view"></a>A vista de tráfego

O **tráfego** vista fornece-lhe um mapa de todo o tráfego possíveis entre os seus recursos. Esta opção fornece um mapa visual de todas as regras que configurou que define quais os recursos que podem comunicar com os quais. Isto permite-lhe ver a configuração existente dos grupos de segurança de rede, bem como para identificar rapidamente as configurações de risco possíveis dentro de suas cargas de trabalho.

### <a name="uncover-unwanted-connections"></a>Descobrir ligações indesejadas

A força desta vista está em sua capacidade para mostrar estas ligações em conjunto com as vulnerabilidades que existe, pelo que pode utilizar este grupo representativo de dados para executar o sistema de proteção necessário nos seus recursos. 

Por exemplo, pode detetar duas máquinas que não ciente foi possível comunicar, permitindo-lhe para ajudar a isolar as cargas de trabalho e sub-redes.

### <a name="investigate-resources"></a>Investigar recursos

Para desagregar um recurso:
1. Quando seleciona um recurso específico no mapa, o painel da direita é aberto e fornece informações gerais sobre o recurso, soluções de segurança ligadas se existirem e as recomendações relevantes para o recurso. É o mesmo tipo de comportamento para cada tipo de recurso que selecionar. 
2. Clique em **tráfego** para ver a lista de possíveis tráfego de saída e entrada do recurso - esta é uma lista abrangente de que consegue comunicar com o recurso e que este possa comunicar com e através das quais protocolos e portas. Por exemplo, quando seleciona uma VM, todas as VMs que consiga comunicar com são apresentados e ao selecionar uma sub-rede, todas as sub-redes que este possa comunicar com são apresentadas.

**Estes dados baseia-se na análise de grupos de segurança de rede, bem como algoritmos que analisam várias regras para compreender suas crossovers e interações de aprendizagem automática avançada.** 

![Mapa de tráfego de rede](./media/security-center-network-recommendations/network-map-traffic.png)

## Sistema de rede legado <a name ="legacy-networking"></a>

Se não tiver o escalão Standard do Centro de segurança, esta secção explica como ver recomendações gratuitas do funcionamento em rede.

Para aceder a estas informações, no painel redes, clique em **exibir rede legado**. 

![Sistema de rede legado](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>Secção pontos finais com acesso à Internet
Na **pontos de extremidade de com acesso à Internet** seção, pode ver as máquinas virtuais que estão atualmente configuradas com um ponto final e o respetivo estado de acesso à Internet.

Esta tabela tem o nome do ponto final, o endereço IP com acesso à Internet e o estado de gravidade atual do grupo de segurança de rede e as recomendações da NGFW. A tabela está ordenada por gravidade.

### <a name="networking-topology-section"></a>Secção Topologia de redes
O **topologia de redes** secção tem uma vista hierárquica dos recursos.

Esta tabela está ordenada (máquinas virtuais e sub-redes) por gravidade.

Esta vista de topologia, o primeiro nível apresenta Vnets. O segundo apresenta tem sub-redes e o terceiro nível apresenta as máquinas virtuais que pertencem a essas sub-redes. A coluna da direita mostra o estado atual das recomendações de grupo de segurança de rede para esses recursos.

O terceiro nível apresenta as máquinas virtuais, que é semelhante ao que foi descrito anteriormente. Pode clicar em qualquer recurso para obter mais informações ou aplicar a configuração ou o controlo de segurança necessário.

## <a name="network-recommendations"></a>Recomendações de rede

|Tipo de recurso|Classificação de segurança|Recomendação|Descrição|
|----|----|----|----|
|Máquina|40|Ativar Grupos de Segurança de Rede em máquinas virtuais|Ative grupos de segurança de rede controlar o acesso de rede das suas máquinas virtuais.|
|Subrede|35|Ativar grupos de segurança de rede em sub-redes |Ative grupos de segurança de rede controlar o acesso de rede de recursos implementados nas suas sub-redes.|
|Máquina|30|Aplicar um controlo de acesso à rede Just-In-Time|Aplicam-se apenas no controlo de acesso VM de tempo permanentemente bloquear o acesso a portas selecionadas e permitir que os utilizadores autorizados para abri-los via o mesmo mecanismo e durante um período limitado de tempo.|
|Máquina|20|Restringir o acesso através de um ponto final com acesso à Internet|Proteger os grupos de segurança de rede de sua Internet voltada para as VMs ao restringir o acesso de sua existente permitem regras.|
|Máquina|10|Adicionar uma firewall da próxima geração|Adicione uma solução de próxima geração Firewall (NGFW) para melhor proteger VMs de com acesso à internet.|
|Máquina|5|Encaminhar o tráfego através da firewall do gateway de rede apenas|Para concluir a implementação da sua solução de firewall próxima geração, o tráfego para VMs de com acesso à internet de protegido deve de ser encaminhado apenas através da solução de firewall de geração seguinte.|
|VNet|5|Ativar a norma de proteção DDoS|Aplicativos com IPs públicos nestas redes virtuais não estão protegidos com o serviço de proteção contra DDOS padrão. Recomenda-se para ativá-la ativar a atenuação da rede volumetric e ataques de protocolo.|
|Máquina|10|Adicionar uma firewall da próxima geração|dd uma solução de próxima geração Firewall (NGFW) para melhor proteger VMs de com acesso à Internet.|
|Máquina|5|Encaminhar o tráfego através da firewall do gateway de rede apenas|Para concluir a implementação da sua solução de firewall próxima geração, o tráfego para VMs de com acesso à Internet de protegido deve de ser encaminhado apenas através da solução de firewall de geração seguinte.|
Vnet|5|Ativar a norma de proteção DDoS|Aplicações com endereços IP públicos nestas redes virtuais não estão protegidas com o serviço de proteção contra DDOS padrão. Recomenda-se para ativá-la ativar a atenuação da rede volumetric e ataques de protocolo.|
## <a name="see-also"></a>Consulte também
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

* [Proteger as máquinas virtuais no Centro de Segurança do Azure](security-center-virtual-machine-recommendations.md)
* [Proteger as aplicações no Centro de Segurança do Azure](security-center-application-recommendations.md)
* [Proteger o seu serviço do SQL do Azure no Centro de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
