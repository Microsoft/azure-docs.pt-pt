---
title: Protegendo seus recursos de rede na central de segurança do Azure | Microsoft Docs
description: Este documento aborda as recomendações na central de segurança do Azure que ajudam a proteger os recursos de rede do Azure e a manter a conformidade com as políticas de segurança.
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
ms.author: v-mohabe
ms.openlocfilehash: 3683550045699a24c27a3226fba2e90a671f80e1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101017"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>Proteger seus recursos de rede na central de segurança do Azure
A central de segurança do Azure analisa continuamente o estado de segurança de seus recursos do Azure para práticas recomendadas de segurança de rede. Quando a central de segurança identifica possíveis vulnerabilidades de segurança, ele cria recomendações que orientam você pelo processo de configuração dos controles necessários para proteger e proteger seus recursos.

Este artigo aborda as recomendações que se aplicam aos recursos do Azure de uma perspectiva de segurança de rede. O centro de recomendações de rede em relação aos firewalls da próxima geração, grupos de segurança de rede, acesso à VM JIT por regras de tráfego de entrada excessivamente permissivos e muito mais. Para obter uma lista de recomendações de rede e ações de correção, consulte [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md).

> [!NOTE]
> A página **rede** permite aprofundar-se na integridade de recursos do Azure de uma perspectiva de rede. O mapa de rede e os controles de rede adaptáveis estão disponíveis somente para a camada Standard da central de segurança do Azure. [Se você usar a camada gratuita, poderá clicar no botão para **exibir a rede herdada** e receber recomendações de recursos de rede](#legacy-networking).
>

A página **rede** fornece uma visão geral das seções que você pode aprofundar para obter mais informações sobre a integridade de seus recursos de rede:

- Mapa de rede (somente camada Standard da central de segurança do Azure)
- Proteção de Rede Ajustável
- Recomendações de segurança de rede.
- Folha de **rede** herdada (a folha rede anterior) 
 
![Painel de rede](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>Mapa de rede
O mapa de rede interativo fornece uma exibição gráfica com sobreposições de segurança, fornecendo recomendações e ideias para proteger seus recursos de rede. Usando o mapa, você pode ver a topologia de rede de suas cargas de trabalho do Azure, conexões entre suas máquinas virtuais e sub-redes e a capacidade de fazer uma busca detalhada do mapa em recursos específicos e as recomendações para esses recursos.

Para abrir o mapa de rede:

1. Na central de segurança, em higiene de segurança de recursos, selecione **rede**.
2. Em **mapa de rede** , clique em **Ver topologia**.
 
A exibição padrão do mapa de topologia exibe:

- Assinaturas que você selecionou no Azure. O mapa dá suporte a várias assinaturas.
- VMs, sub-redes e VNets do tipo de recurso do Resource Manager (não há suporte para recursos clássicos do Azure)
- VNets emparelhados
- Somente recursos que têm [recomendações de rede](security-center-recommendations.md) com severidade alta ou média  
- Recursos voltados para a Internet
- O mapa é otimizado para as assinaturas que você selecionou no Azure. Se você modificar sua seleção, o mapa será recalculado e reotimizado com base nas novas configurações.  

![Mapa de topologia de rede](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>Noções básicas sobre o mapa de rede

O mapa de rede pode mostrar os recursos do Azure em uma exibição de **topologia** e uma exibição de **tráfego** .

### <a name="the-topology-view"></a>A exibição de topologia

Na exibição **topologia** do mapa de rede, você pode exibir as seguintes informações sobre os recursos de rede:

- No círculo interno, você pode ver todos os Vnets em suas assinaturas selecionadas, o próximo círculo é todas as sub-redes, o círculo externo é todas as máquinas virtuais.
- As linhas que conectam os recursos no mapa permitem que você saiba quais recursos estão associados entre si e como sua rede do Azure é estruturada. 
- Use os indicadores de severidade para obter rapidamente uma visão geral de quais recursos têm recomendações abertas na central de segurança.
- Você pode clicar em qualquer um dos recursos para fazer uma busca detalhada deles e exibir os detalhes desse recurso e suas recomendações diretamente e no contexto do mapa de rede.  
- Se houver muitos recursos sendo exibidos no mapa, a central de segurança do Azure usará seu algoritmo proprietário para o cluster inteligente seus recursos, destacando os recursos que estão no estado mais crítico e terá as recomendações de severidade mais altas. 

Como o mapa é interativo e dinâmico, cada nó é clicável e o modo de exibição pode mudar com base nos filtros:

1. Você pode modificar o que vê no mapa de rede usando os filtros na parte superior. Você pode concentrar o mapa com base em:

   -  **Integridade da segurança**: Você pode filtrar o mapa com base na gravidade (alta, média, baixa) de seus recursos do Azure.
   - **Recomendações**: Você pode selecionar quais recursos são exibidos com base nas recomendações que estão ativas nesses recursos. Por exemplo, você pode exibir apenas os recursos para os quais a central de segurança recomenda que você habilite grupos de segurança de rede.
   - **Zonas de rede**: Por padrão, o mapa exibe apenas os recursos voltados para a Internet, você também pode selecionar VMs internas.
 
2. Você pode clicar em **Redefinir** no canto superior esquerdo a qualquer momento para retornar o mapa ao seu estado padrão.

Para fazer uma busca detalhada em um recurso:

1. Quando você seleciona um recurso específico no mapa, o painel direito é aberto e fornece informações gerais sobre o recurso, soluções de segurança conectadas, se houver algum, e as recomendações relevantes para o recurso. É o mesmo tipo de comportamento para cada tipo de recurso selecionado. 
2. Ao passar o mouse sobre um nó no mapa, você pode exibir informações gerais sobre o recurso, incluindo assinatura, tipo de recurso e grupo de recursos.
3. Use o link para ampliar a dica de ferramenta e refocar o mapa nesse nó específico. 
4. Para refocar o mapa fora de um nó específico, reduza o zoom.

### <a name="the-traffic-view"></a>A exibição de tráfego

O modo de exibição de **tráfego** fornece um mapa de todo o tráfego possível entre seus recursos. Isso fornece um mapa visual de todas as regras que você configurou que definem quais recursos podem se comunicar com quem. Isso permite que você veja a configuração existente dos grupos de segurança de rede, bem como identifique rapidamente possíveis configurações arriscadas em suas cargas de trabalho.

### <a name="uncover-unwanted-connections"></a>Descobrir conexões indesejadas

A força dessa exibição é a sua capacidade de mostrar essas conexões permitidas junto com as vulnerabilidades que existem, para que você possa usar essa seção cruzada de dados para executar a proteção necessária em seus recursos. 

Por exemplo, você pode detectar que dois computadores que não estavam cientes pudessem se comunicar, permitindo que você Isole melhor as cargas de trabalho e sub-redes.

### <a name="investigate-resources"></a>Investigar recursos

Para fazer uma busca detalhada em um recurso:

1. Quando você seleciona um recurso específico no mapa, o painel direito é aberto e fornece informações gerais sobre o recurso, soluções de segurança conectadas, se houver algum, e as recomendações relevantes para o recurso. É o mesmo tipo de comportamento para cada tipo de recurso selecionado. 
2. Clique em **tráfego** para ver a lista de possíveis tráfegos de entrada e saída no recurso – esta é uma lista abrangente de quem pode se comunicar com o recurso e com quem ele pode se comunicar e por quais protocolos e portas. Por exemplo, quando você seleciona uma VM, todas as VMs com as quais ela pode se comunicar são mostradas e, quando você seleciona uma sub-rede, todas as sub-redes com as quais ela pode se comunicar são mostradas.

**Esses dados se baseiam na análise dos grupos de segurança de rede, bem como nos algoritmos avançados de aprendizado de máquina que analisam várias regras para entender seus cruzamentos e interações.** 

![Mapa de tráfego de rede](./media/security-center-network-recommendations/network-map-traffic.png)

## Rede herdada<a name ="legacy-networking"></a>

Se você não tiver a camada Standard da central de segurança, esta seção explicará como exibir as recomendações de rede gratuita.

Para acessar essas informações, na folha rede, clique em **Exibir rede herdada**. 

![Rede herdada](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>Secção pontos finais com acesso à Internet
Na seção **pontos de extremidade voltados** para a Internet, você pode ver as máquinas virtuais atualmente configuradas com um ponto de extremidade voltado para a Internet e seu status.

Essa tabela tem o nome do ponto de extremidade, o endereço IP voltado para a Internet e o status de severidade atual do grupo de segurança de rede e as recomendações de NGFW. A tabela é classificada por severidade.

### <a name="networking-topology-section"></a>Secção Topologia de redes
A seção **topologia de rede** tem uma exibição hierárquica dos recursos.

Essa tabela é classificada (máquinas virtuais e sub-redes) por severidade.

Nesse modo de exibição de topologia, o primeiro nível exibe Vnets. O segundo exibe as sub-redes e o terceiro nível exibe as máquinas virtuais que pertencem a essas sub-redes. A coluna da direita mostra o status atual das recomendações do grupo de segurança de rede para esses recursos.

O terceiro nível exibe as máquinas virtuais, que é semelhante ao que é descrito anteriormente. Você pode clicar em qualquer recurso para saber mais ou aplicar a configuração ou o controle de segurança necessário.

## <a name="network-recommendations"></a>Recomendações de rede

|Nome da recomendação|Descrição|Severity|Classificação de segurança|Tipo de recurso|
|----|----|----|----|----|----|
|Os grupos de segurança de rede no nível de sub-rede devem ser habilitados|Habilite grupos de segurança de rede para controlar o acesso à rede de recursos implantados em suas sub-redes.|Alta/média|30|Subnet|
|As máquinas virtuais devem ser associadas a um grupo de segurança de rede|Habilite grupos de segurança de rede para controlar o acesso à rede de suas máquinas virtuais.|Alta/média|30|Máquina virtual|
|O acesso deve ser restrito para grupos de segurança de rede permissivos com VMs voltadas para a Internet|Proteja os grupos de segurança de rede de suas VMs voltadas para a Internet restringindo o acesso de suas regras de permissão existentes.|Alta|20|Máquina virtual|
|As regras para aplicativos Web em IaaS NSGs devem ser protegidas|Proteger o NSG (grupo de segurança de rede) de suas máquinas virtuais que estão executando aplicativos Web, com regras do NSG que são excessivamente permissivas com relação às portas do aplicativo Web.|Alta|20|Máquina virtual|
|O acesso aos serviços de aplicativos deve ser restrito|Restrinja o acesso aos serviços de aplicativos alterando a configuração de rede, para negar o tráfego de entrada de intervalos muito amplos.|Alta|10|Serviço de aplicações|
|As portas de gerenciamento devem ser fechadas em suas máquinas virtuais|Proteger o grupo de segurança de rede de suas máquinas virtuais para restringir o acesso às portas de gerenciamento.|Alta|10|Máquina virtual|
A proteção contra DDoS Standard deve ser habilitada|Proteja redes virtuais que contêm aplicativos com IPs públicos habilitando o padrão de serviço de proteção contra DDoS. A proteção contra DDoS permite a mitigação de ataques de volumétricos de rede e de protocolo.|Alta|10|Rede virtual|
|O encaminhamento IP em sua máquina virtual deve ser desabilitado|Desabilite o encaminhamento de IP. Quando o encaminhamento de IP está habilitado na NIC de uma máquina virtual, o computador pode receber o tráfego endereçado a outros destinos. O encaminhamento de IP raramente é necessário (por exemplo, ao usar a VM como uma solução de virtualização de rede) e, portanto, isso deve ser revisado pela equipe de segurança de rede.|Médio|10|Máquina virtual|
|Aplicação Web só deve estar acessível através de HTTPS|Habilite o acesso "somente HTTPS" para aplicativos Web. O uso de HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede.|Médio|20|Aplicação Web|
|O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais|Aplique o controle de acesso de máquina virtual (JIT) just-in-time para bloquear permanentemente o acesso às portas selecionadas e habilite os usuários autorizados a abri-los, por meio do JIT, apenas por uma quantidade limitada de tempo.|Alta|20|Máquina virtual|
|Os aplicativos de funções só devem ser acessíveis via HTTPS|Habilite o acesso "somente HTTPS" para aplicativos de funções. O uso de HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito de ataques de interceptação de camada de rede.|Médio|20|Function app|
|A transferência segura para contas de armazenamento deve ser habilitada|Habilite a transferência segura para contas de armazenamento. Transferência segura é uma opção que força sua conta de armazenamento a aceitar solicitações somente de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camada de rede, como Man-in-the-Middle, espionagem e seqüestro de sessão.|Alta|20|Conta de armazenamento|

## <a name="see-also"></a>Consulte também
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

* [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Proteger o seu serviço do SQL do Azure no Centro de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
