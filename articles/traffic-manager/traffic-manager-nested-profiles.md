---
title: Perfis de Gestor de Tráfego Aninhado em Azure
titleSuffix: Azure Traffic Manager
description: Este artigo explica a funcionalidade 'Nested Profiles' do Gestor de Tráfego azure
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: rohink
ms.openlocfilehash: 282099cb274c1ea872a0df9c2753a939ef31421f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76938577"
---
# <a name="nested-traffic-manager-profiles"></a>Perfis do Gestor de Tráfego aninhados

O Traffic Manager inclui uma gama de métodos de encaminhamento de tráfego que lhe permitem controlar a forma como o Traffic Manager escolhe qual o ponto final que deve receber tráfego de cada utilizador final. Para obter mais informações, consulte [os métodos de encaminhamento de tráfego do Gestor de Tráfego](traffic-manager-routing-methods.md).

Cada perfil do Gestor de Tráfego especifica um único método de encaminhamento de tráfego. No entanto, existem cenários que requerem um encaminhamento de tráfego mais sofisticado do que o encaminhamento fornecido por um único perfil do Gestor de Tráfego. Você pode nidificar perfis do Traffic Manager para combinar os benefícios de mais do que um método de encaminhamento de tráfego. Os perfis aninhados permitem-lhe anular o comportamento do Gestor de Tráfego padrão para suportar implementações de aplicações maiores e mais complexas.

Os exemplos seguintes ilustram como usar perfis aninhados do Traffic Manager em vários cenários.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exemplo 1: Combinação de tráfego 'Performance' e 'Ponderado'

Suponha que tenha implementado uma aplicação nas seguintes regiões de Azure: Oeste dos EUA, Europa Ocidental e Ásia Oriental. Utiliza o método de encaminhamento de tráfego 'Performance' do Gestor de Tráfego para distribuir o tráfego pela região mais próxima do utilizador.

![Perfil do Gestor único de Tráfego][4]

Agora, suponha que deseja testar uma atualização ao seu serviço antes de a lançar mais amplamente. Pretende utilizar o método de encaminhamento de tráfego "ponderado" para direcionar uma pequena percentagem de tráfego para a sua implantação de testes. Montou a implantação do teste ao lado da implantação de produção existente na Europa Ocidental.

Não é possível combinar tanto "Ponderado" como "Encaminhamento de tráfego de desempenho num único perfil. Para apoiar este cenário, cria-se um perfil de Gestor de Tráfego utilizando os dois pontos finais da Europa Ocidental e o método de encaminhamento de tráfego "ponderado". Em seguida, adicione este perfil 'criança' como um ponto final para o perfil 'pai'. O perfil dos pais ainda utiliza o método de encaminhamento de tráfego de desempenho e contém as outras implementações globais como pontos finais.

O diagrama que se segue ilustra este exemplo:

![Perfis do Gestor de Tráfego aninhados][2]

Nesta configuração, o tráfego direcionado através do perfil dos pais distribui normalmente o tráfego por regiões. Na Europa Ocidental, o perfil aninhado distribui o tráfego para os pontos finais de produção e teste de acordo com os pesos atribuídos.

Quando o perfil dos pais utiliza o método de encaminhamento de tráfego 'Performance', cada ponto final deve ser atribuído a uma localização. A localização é atribuída quando configura r1e o ponto final. Escolha a região de Azure mais próxima da sua implantação. As regiões de Azure são os valores de localização suportados pela Tabela de Latência da Internet. Para mais informações, consulte o [método de encaminhamento de tráfego 'Performance' do Gestor de Tráfego](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exemplo 2: Monitorização de ponto final nos perfis aninhados

O Gestor de Tráfego monitoriza ativamente a saúde de cada ponto final do serviço. Se um ponto final não for saudável, o Traffic Manager direciona os utilizadores para pontos finais alternativos para preservar a disponibilidade do seu serviço. Este comportamento de monitorização e falha de pontofinal aplica-se a todos os métodos de encaminhamento de tráfego. Para mais informações, consulte a [Monitorização do Ponto final do Gestor de Tráfego](traffic-manager-monitoring.md). A monitorização do endpoint funciona de forma diferente para perfis aninhados. Com perfis aninhadas, o perfil dos pais não faz verificações de saúde na criança diretamente. Em vez disso, a saúde dos pontos finais do perfil da criança é utilizada para calcular a saúde geral do perfil da criança. Esta informação de saúde é propagada pela hierarquia do perfil aninhado. O perfil dos pais utiliza esta saúde agregada para determinar se deve direcionar o tráfego para o perfil da criança. Consulte as [FAQ](traffic-manager-FAQs.md#traffic-manager-nested-profiles) para obter detalhes completos sobre a monitorização da saúde dos perfis aninhados.

Voltando ao exemplo anterior, suponha que a implantação da produção na Europa Ocidental falhe. Por predefinição, o perfil 'criança' direciona todo o tráfego para a implementação do teste. Se a implementação do teste também falhar, o perfil dos pais determina que o perfil da criança não deve receber tráfego, uma vez que todos os pontos finais da criança não são saudáveis. Em seguida, o perfil dos pais distribui o tráfego para as outras regiões.

![Falha no perfil aninhada (comportamento padrão)][3]

Pode ficar feliz com este acordo. Ou talvez receie que todo o tráfego para a Europa Ocidental vá agora para a implantação dos testes em vez de um tráfego de subconjunto limitado. Independentemente da saúde da implantação dos testes, queremos falhar com as outras regiões quando a implantação da produção na Europa Ocidental falhar. Para ativar esta falha, pode especificar o parâmetro 'MinChildEndpoints' ao configurar o perfil da criança como um ponto final no perfil dos pais. O parâmetro determina o número mínimo de pontos finais disponíveis no perfil da criança. O valor padrão é '1'. Para este cenário, define o valor dos Pontos MinChildEndpoints para 2. Abaixo deste limiar, o perfil dos pais considera que todo o perfil da criança está indisponível e direciona o tráfego para os outros pontos finais.

A figura seguinte ilustra esta configuração:

![Falha no perfil aninhada com 'MinChildEndpoints' = 2][4]

> [!NOTE]
> O método de encaminhamento de tráfego "prioritário" distribui todo o tráfego para um único ponto final. Assim, há pouco propósito numa definição minChildEndpoints que não seja '1' para um perfil infantil.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exemplo 3: Regiões de failover prioritárias no itinerário de tráfego 'Performance'

O comportamento padrão para o método de encaminhamento de tráfego 'Performance' é quando você tem pontos finais em diferentes localizações geográficas os utilizadores finais são encaminhados para o ponto final "mais próximo" em termos da latência de rede mais baixa.

No entanto, suponha que prefere que o tráfego da Europa Ocidental falhe para os EUA Ocidentais, e apenas o tráfego direto para outras regiões quando ambos os pontos finais não estão disponíveis. Pode criar esta solução utilizando um perfil infantil com o método de encaminhamento de tráfego "Prioritário".

![Encaminhamento de tráfego 'performance' com falha preferencial][6]

Uma vez que o ponto final da Europa Ocidental tem uma prioridade maior do que o ponto final dos EUA Ocidentais, todo o tráfego é enviado para o ponto final da Europa Ocidental quando ambos os pontos finais estão em linha. Se a Europa Ocidental falhar, o seu tráfego é direcionado para os EUA Ocidentais. Com o perfil aninhado, o tráfego só é direcionado para a Ásia Oriental quando tanto a Europa Ocidental como os EUA Ocidentais falham.

Pode repetir este padrão para todas as regiões. Substitua os três pontos finais no perfil dos pais por três perfis infantis, cada um fornecendo uma sequência de failover priorizada.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exemplo 4: Controlar o tráfego de 'desempenho' entre vários pontos finais na mesma região

Suponha que o método de encaminhamento de tráfego "Desempenho" seja utilizado num perfil que tenha mais de um ponto final numa determinada região. Por predefinição, o tráfego direcionado para aquela região é distribuído uniformemente por todos os pontos finais disponíveis naquela região.

!["Desempenho" de tráfego na distribuição de tráfego na região (comportamento predefinido)][7]

Em vez de adicionar vários pontos finais na Europa Ocidental, esses pontos finais são incluídos num perfil infantil separado. O perfil da criança é adicionado ao progenitor como o único ponto final na Europa Ocidental. As configurações do perfil infantil podem controlar a distribuição de tráfego com a Europa Ocidental, permitindo o encaminhamento de tráfego baseado em prioridades ou ponderados nessa região.

![Encaminhamento de tráfego 'performance' com distribuição personalizada de tráfego na região][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exemplo 5: Definições de monitorização por ponto final

Suponha que está a usar o Traffic Manager para migrar suavemente o tráfego de um site legado no local para uma nova versão baseada em Cloud hospedada no Azure. Para o site do legado, você quer usar a página inicial URI para monitorizar a saúde do site. Mas para a nova versão baseada em Cloud, está a implementar uma página de monitorização personalizada (caminho '/monitor.aspx') que inclui verificações adicionais.

![Monitorização do ponto final do Gestor de Tráfego (comportamento predefinido)][9]

As definições de monitorização num perfil do Gestor de Tráfego aplicam-se a todos os pontos finais dentro de um único perfil. Com perfis aninhadas, utiliza um perfil infantil diferente por site para definir diferentes configurações de monitorização.

![Monitorização do ponto final do Gestor de Tráfego com definições de ponto final][10]

## <a name="faqs"></a>FAQs

* [Como configurar perfis aninhados?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#traffic-manager-endpoint-monitoring)

* [Quantas camadas de nidificação suporta manger de trânsito?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-layers-of-nesting-does-traffic-manger-support)

* [Posso misturar outros tipos de pontos finais com perfis infantis aninhados, no mesmo perfil do Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [Como é que o modelo de faturação se aplica aos perfis da Nested?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-billing-model-apply-for-nested-profiles)

* [Há algum impacto no desempenho para perfis aninhados?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-performance-impact-for-nested-profiles)

* [Como é que o Gestor de Tráfego calcula a saúde de um ponto final aninhado num perfil dos pais?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [os perfis do Gestor de Tráfego](traffic-manager-overview.md)

Saiba como [criar um perfil de Gestor](traffic-manager-create-profile.md) de Tráfego

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
