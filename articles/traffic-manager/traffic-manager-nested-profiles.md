---
title: Perfis de gerente de tráfego aninhados em Azure
titleSuffix: Azure Traffic Manager
description: Este artigo explica a funcionalidade 'Perfis Aninhados' do Gestor de Tráfego da Azure
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: duau
ms.openlocfilehash: 5f2aa3d05d349880b5eb2d35a2c58af0741b9855
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185585"
---
# <a name="nested-traffic-manager-profiles"></a>Perfis do Gestor de Tráfego aninhados

O Traffic Manager inclui uma gama de métodos de encaminhamento de tráfego que permitem controlar a forma como o Traffic Manager escolhe qual o ponto final que deve receber tráfego de cada utilizador final. Para obter mais informações, consulte [os métodos de encaminhamento de tráfego do Gestor de Tráfego](traffic-manager-routing-methods.md).

Cada perfil do Gestor de Tráfego especifica um único método de encaminhamento de tráfego. No entanto, existem cenários que requerem um encaminhamento de tráfego mais sofisticado do que o encaminhamento fornecido por um único perfil de Gestor de Tráfego. Pode nidificar os perfis do Traffic Manager para combinar os benefícios de mais de um método de encaminhamento de tráfego. Os perfis aninhados permitem-lhe anular o comportamento padrão do Gestor de Tráfego para suportar implementações de aplicações maiores e mais complexas.

Os exemplos a seguir ilustram como usar perfis de Gestor de Tráfego aninhados em vários cenários.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exemplo 1: Combinação de encaminhamento de tráfego 'Performance' e 'Ponderado'

Suponha que tenha implementado uma aplicação nas seguintes regiões de Azure: EUA Ocidentais, Europa Ocidental e Ásia Oriental. Utiliza o método de encaminhamento de tráfego 'Performance' do Traffic Manager para distribuir o tráfego para a região mais próxima do utilizador.

![Perfil de Gestor de Tráfego Único][4]

Agora, suponha que deseja testar uma atualização ao seu serviço antes de o lançar mais amplamente. Pretende utilizar o método de encaminhamento de tráfego "ponderado" para direcionar uma pequena percentagem do tráfego para a sua colocação de teste. Instalou a colocação de testes ao lado da implantação de produção existente na Europa Ocidental.

Não é possível combinar o 'Weighted' e o 'Performance traffic-encaminhamento' num único perfil. Para apoiar este cenário, cria-se um perfil de Gestor de Tráfego utilizando os dois pontos finais da Europa Ocidental e o método de encaminhamento de tráfego "ponderado". Em seguida, adicione este perfil de "criança" como ponto final ao perfil de 'pai'. O perfil principal ainda utiliza o método de encaminhamento de tráfego de desempenho e contém as outras implementações globais como pontos finais.

O seguinte diagrama ilustra este exemplo:

![Perfis do Gestor de Tráfego aninhados][2]

Nesta configuração, o tráfego direcionado através do perfil principal distribui normalmente o tráfego pelas regiões. Na Europa Ocidental, o perfil aninhado distribui o tráfego para os pontos finais de produção e teste de acordo com os pesos atribuídos.

Quando o perfil dos pais utilizar o método de encaminhamento de tráfego 'Performance', cada ponto final deve ser atribuído a um local. A localização é atribuída quando configurar o ponto final. Escolha a região Azure mais próxima da sua implantação. As regiões de Azure são os valores de localização suportados pela Tabela de Latência da Internet. Para obter mais informações, consulte [o método de encaminhamento de tráfego 'Performance' do Gestor de Tráfego](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exemplo 2: Monitorização de pontos finais em perfis aninhados

O Gestor de Tráfego monitoriza ativamente a saúde de cada ponto final de serviço. Se um ponto final não for saudável, o Traffic Manager direciona os utilizadores para pontos finais alternativos para preservar a disponibilidade do seu serviço. Este comportamento de monitorização e falha no ponto final aplica-se a todos os métodos de encaminhamento de tráfego. Para obter mais informações, consulte [a Monitorização endpoint do Gestor de Tráfego.](traffic-manager-monitoring.md) A monitorização do ponto final funciona de forma diferente para perfis aninhados. Com perfis aninhados, o perfil dos pais não realiza verificações de saúde diretamente na criança. Em vez disso, a saúde dos pontos finais do perfil da criança é utilizada para calcular a saúde geral do perfil da criança. Esta informação de saúde é propagada pela hierarquia de perfil aninhado. O perfil dos pais utiliza esta saúde agregada para determinar se deve dirigir o tráfego para o perfil da criança. Consulte as [FAQ](traffic-manager-FAQs.md#traffic-manager-nested-profiles) para obter informações sobre a monitorização de saúde de perfis aninhados.

Voltando ao exemplo anterior, suponhamos que a implantação da produção na Europa Ocidental falhe. Por predefinição, o perfil 'criança' direciona todo o tráfego para a colocação do teste. Se a colocação do teste também falhar, o perfil dos pais determina que o perfil da criança não deve receber o tráfego, uma vez que todos os pontos finais da criança não são saudáveis. Depois, o perfil dos pais distribui o tráfego para as outras regiões.

![Falha no perfil aninhado (comportamento predefinido)][3]

Talvez esteja feliz com este acordo. Ou podem estar preocupados com o facto de todo o tráfego para a Europa Ocidental ir agora para a implantação de testes em vez de um tráfego limitado de subconjuntos. Independentemente da saúde da implantação dos testes, pretende-se falhar nas outras regiões quando a implantação da produção na Europa Ocidental falhar. Para ativar esta falha, pode especificar o parâmetro 'MinChildEndpoints' ao configurar o perfil da criança como um ponto final no perfil dos pais. O parâmetro determina o número mínimo de pontos finais disponíveis no perfil da criança. O valor predefinido é '1'. Para este cenário, define o valor minChildEndpoints para 2. Abaixo deste limiar, o perfil dos pais considera que todo o perfil da criança não está disponível e direciona o tráfego para os outros pontos finais.

A seguinte figura ilustra esta configuração:

![Falha no perfil aninhado com 'MinChildEndpoints' = 2][4]

> [!NOTE]
> O método de encaminhamento de tráfego "Prioritário" distribui todo o tráfego para um único ponto final. Assim, há pouco propósito numa definição de MinChildEndpoints que não seja '1' para um perfil infantil.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exemplo 3: Prioridades nas regiões de failover no encaminhamento de tráfego "Performance"

O comportamento padrão para o método de encaminhamento de tráfego 'Performance' é quando você tem pontos finais em diferentes locais geográficos os utilizadores finais são encaminhados para o ponto final "mais próximo" em termos da latência de rede mais baixa.

No entanto, suponhamos que preferem que o tráfego da Europa Ocidental falhe em direção aos EUA ocidentais, e que apenas o tráfego direto para outras regiões quando ambos os pontos finais não estejam disponíveis. Pode criar esta solução utilizando um perfil infantil com o método de encaminhamento de tráfego "Prioritário".

![Encaminhamento de tráfego 'Performance' com falha preferencial][6]

Uma vez que o ponto final da Europa Ocidental tem uma prioridade maior do que o ponto final dos EUA Ocidentais, todo o tráfego é enviado para o ponto final da Europa Ocidental quando ambos os pontos finais estão online. Se a Europa Ocidental falhar, o seu tráfego é direcionado para os EUA Ocidentais. Com o perfil aninhado, o tráfego só é direcionado para a Ásia Oriental quando a Europa Ocidental e os EUA ocidentais falham.

Pode repetir este padrão para todas as regiões. Substitua os três pontos finais no perfil dos pais por três perfis de criança, cada um fornecendo uma sequência de failover prioritária.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exemplo 4: Controlo do encaminhamento de tráfego "Performance" entre vários pontos finais na mesma região

Suponha que o método de encaminhamento de tráfego 'Performance' seja utilizado num perfil que tenha mais de um ponto final numa determinada região. Por defeito, o tráfego direcionado para aquela região é distribuído uniformemente por todos os pontos finais disponíveis naquela região.

!['Desempenho' de encaminhamento de tráfego na região distribuição de tráfego (comportamento padrão)][7]

Em vez de adicionar vários pontos finais na Europa Ocidental, esses pontos finais são incluídos num perfil infantil separado. O perfil da criança é adicionado ao progenitor como o único ponto final na Europa Ocidental. As definições no perfil da criança podem controlar a distribuição de tráfego com a Europa Ocidental, permitindo o encaminhamento de tráfego baseado em prioridades ou ponderados nessa região.

![Encaminhamento de tráfego 'Performance' com distribuição personalizada de tráfego na região][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exemplo 5: Definições de monitorização por ponto final

Suponha que está a usar o Traffic Manager para migrar suavemente o tráfego de um site antigo no local para uma nova versão baseada na Cloud hospedada em Azure. Para o site do legado, pretende utilizar a página inicial URI para monitorizar a saúde do local. Mas para a nova versão baseada na Cloud, está a implementar uma página de monitorização personalizada (caminho '/monitor.aspx') que inclui verificações adicionais.

![Monitorização do ponto final do Gestor de Tráfego (comportamento predefinido)][9]

As definições de monitorização num perfil do Gestor de Tráfego aplicam-se a todos os pontos finais num único perfil. Com perfis aninhados, utiliza-se um perfil infantil diferente por site para definir diferentes definições de monitorização.

![Monitorização do ponto final do Gestor de Tráfego com definições de ponto final][10]

## <a name="faqs"></a>FAQs

* [Como posso configurar perfis aninhados?](./traffic-manager-faqs.md#traffic-manager-nested-profiles)

* [Quantas camadas de nidificação a Traffic Manger suporta?](./traffic-manager-faqs.md#how-many-layers-of-nesting-does-traffic-manger-support)

* [Posso misturar outros tipos de pontos finais com perfis infantis aninhados, no mesmo perfil de Gerente de Tráfego?](./traffic-manager-faqs.md#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [Como é que o modelo de faturação se aplica aos perfis de Nested?](./traffic-manager-faqs.md#how-does-the-billing-model-apply-for-nested-profiles)

* [Existe um impacto de desempenho para perfis aninhados?](./traffic-manager-faqs.md#is-there-a-performance-impact-for-nested-profiles)

* [Como é que o Traffic Manager calcula a saúde de um ponto final aninhado num perfil de pai?](./traffic-manager-faqs.md#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [os perfis do Gestor de Tráfego](traffic-manager-overview.md)

Saiba como [criar um perfil de Gestor de Tráfego](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png