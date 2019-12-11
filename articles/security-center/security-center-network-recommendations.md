---
title: Protegendo seus recursos de rede na central de segurança do Azure | Microsoft Docs
description: Este documento aborda as recomendações na central de segurança do Azure que ajudam a proteger os recursos de rede do Azure e a manter a conformidade com as políticas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: 5d13e944cbc083b314c71936d181101ec13037b4
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997144"
---
# <a name="protect-your-network-resources"></a>Proteja seus recursos de rede
A central de segurança do Azure analisa continuamente o estado de segurança de seus recursos do Azure para práticas recomendadas de segurança de rede. Quando a central de segurança identifica possíveis vulnerabilidades de segurança, ele cria recomendações que orientam você pelo processo de configuração dos controles necessários para proteger e proteger seus recursos.

Este artigo explica a página de **rede** da seção segurança de recursos da central de segurança do Azure e algumas das recomendações que você verá lá.

Para obter uma lista completa das recomendações de rede, consulte [recomendações de rede](recommendations-network.md).

Este artigo aborda as recomendações que se aplicam aos recursos do Azure de uma perspectiva de segurança de rede. Centro de recomendações de rede sobre firewalls da próxima geração, grupos de segurança de rede, acesso de VM JIT, regras de tráfego de entrada excessivamente permissivas e muito mais. Para obter uma lista de recomendações de rede e ações de correção, consulte [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md).

> [!NOTE]
> A página **rede** permite aprofundar-se na integridade de recursos do Azure de uma perspectiva de rede. O mapa de rede e os controles de rede adaptáveis estão disponíveis somente para a camada Standard da central de segurança do Azure. [Se você usar a camada gratuita, poderá clicar no botão para **exibir a rede herdada** e receber recomendações de recursos de rede](#legacy-networking).
>

A página **rede** fornece uma visão geral das seções que você pode aprofundar para obter mais informações sobre a integridade de seus recursos de rede:

- Mapa de rede (somente camada Standard da central de segurança do Azure)
- Proteção de Rede Ajustável
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

   -  **Integridade da segurança**: você pode filtrar o mapa com base na severidade (alta, média, baixa) de seus recursos do Azure.
   - **Recomendações**: você pode selecionar quais recursos são exibidos com base nas recomendações que estão ativas nesses recursos. Por exemplo, você pode exibir apenas os recursos para os quais a central de segurança recomenda que você habilite grupos de segurança de rede.
   - **Zonas de rede**: por padrão, o mapa exibe somente os recursos voltados para a Internet, você também pode selecionar VMs internas.
 
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

Nesse modo de exibição de topologia, o primeiro nível exibe Vnets. O segundo exibe sub-redes e o terceiro nível exibe as máquinas virtuais que pertencem a essas sub-redes. A coluna da direita mostra o status atual das recomendações do grupo de segurança de rede para esses recursos.

O terceiro nível exibe as máquinas virtuais, que é semelhante ao que é descrito anteriormente. Você pode clicar em qualquer recurso para saber mais ou aplicar a configuração ou o controle de segurança necessário.

## <a name="see-also"></a>Ver também
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

* [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Proteger o seu serviço do SQL do Azure no Centro de segurança do Azure](security-center-sql-service-recommendations.md)