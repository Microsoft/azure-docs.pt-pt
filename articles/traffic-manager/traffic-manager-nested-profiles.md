---
title: Perfis aninhados do Gerenciador de tráfego no Azure
titleSuffix: Azure Traffic Manager
description: Este artigo explica o recurso "perfis aninhados" do Gerenciador de tráfego do Azure
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: allensu
ms.openlocfilehash: a5444c05b59196f53c670a2ae782f2bda5527c54
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227756"
---
# <a name="nested-traffic-manager-profiles"></a>Perfis do Gestor de Tráfego aninhados

O Gerenciador de tráfego inclui uma variedade de métodos de roteamento de tráfego que permitem controlar como o Gerenciador de tráfego escolhe qual ponto de extremidade deve receber tráfego de cada usuário final. Para obter mais informações, consulte [métodos de roteamento de tráfego do Traffic Manager](traffic-manager-routing-methods.md).

Cada perfil do Gerenciador de tráfego especifica um único método de roteamento de tráfego. No entanto, há cenários que exigem um roteamento de tráfego mais sofisticado do que o roteamento fornecido por um único perfil do Gerenciador de tráfego. Você pode aninhar perfis do Gerenciador de tráfego para combinar os benefícios de mais de um método de roteamento de tráfego. Os perfis aninhados permitem que você substitua o comportamento padrão do Gerenciador de tráfego para dar suporte a implantações de aplicativos maiores e mais complexas.

Os exemplos a seguir ilustram como usar perfis aninhados do Gerenciador de tráfego em vários cenários.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exemplo 1: combinando o roteamento de tráfego de "desempenho" e "ponderado"

Suponha que você implantou um aplicativo nas seguintes regiões do Azure: oeste dos EUA, Europa Ocidental e Ásia Oriental. Você usa o método de roteamento de tráfego de "desempenho" do Gerenciador de tráfego para distribuir o tráfego para a região mais próxima do usuário.

![Perfil do Gerenciador de tráfego único][4]

Agora, suponha que você deseja testar uma atualização para seu serviço antes de implantá-lo mais amplamente. Você deseja usar o método de roteamento de tráfego "ponderado" para direcionar uma pequena porcentagem de tráfego para a implantação de teste. Você configura a implantação de teste junto com a implantação de produção existente no Europa Ocidental.

Você não pode combinar o roteamento de tráfego de desempenho "ponderado" e "em um único perfil. Para dar suporte a esse cenário, você cria um perfil do Gerenciador de tráfego usando os dois pontos de extremidade Europa Ocidental e o método de roteamento de tráfego "ponderado". Em seguida, adicione este perfil ' Child ' como um ponto de extremidade ao perfil ' pai '. O perfil pai ainda usa o método de roteamento de tráfego de desempenho e contém as outras implantações globais como pontos de extremidade.

O diagrama a seguir ilustra este exemplo:

![Perfis do Gestor de Tráfego aninhados][2]

Nessa configuração, o tráfego direcionado por meio do perfil pai distribui o tráfego entre as regiões normalmente. Dentro de Europa Ocidental, o perfil aninhado distribui o tráfego para os pontos de extremidade de produção e de teste de acordo com os pesos atribuídos.

Quando o perfil pai usa o método de roteamento de tráfego de "desempenho", cada ponto de extremidade deve ser atribuído a um local. O local é atribuído quando você configura o ponto de extremidade. Escolha a região do Azure mais próxima de sua implantação. As regiões do Azure são os valores de localização com suporte na tabela de latência da Internet. Para obter mais informações, consulte [método de roteamento de tráfego de "desempenho" do Gerenciador de tráfego](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exemplo 2: monitoramento de ponto de extremidade em perfis aninhados

O Gerenciador de tráfego monitora ativamente a integridade de cada ponto de extremidade de serviço. Se um ponto de extremidade não estiver íntegro, o Gerenciador de tráfego direcionará os usuários para pontos de extremidade alternativos para preservar a disponibilidade do serviço. Esse comportamento de monitoramento e failover do ponto de extremidade se aplica a todos os métodos de roteamento de tráfego. Para obter mais informações, consulte [monitoramento de ponto de extremidade do Traffic Manager](traffic-manager-monitoring.md). O monitoramento de ponto de extremidade funciona de forma diferente para perfis aninhados. Com perfis aninhados, o perfil pai não executa verificações de integridade diretamente no filho. Em vez disso, a integridade dos pontos de extremidade do perfil filho é usada para calcular a integridade geral do perfil filho. Essas informações de integridade são propagadas para a hierarquia de perfis aninhados. O perfil pai usa essa integridade agregada para determinar se o tráfego será direcionado para o perfil filho. Consulte as [perguntas frequentes](traffic-manager-FAQs.md#traffic-manager-nested-profiles) para obter detalhes completos sobre o monitoramento de integridade de perfis aninhados.

Retornando ao exemplo anterior, suponha que a implantação de produção no Europa Ocidental falhe. Por padrão, o perfil "filho" direciona todo o tráfego para a implantação de teste. Se a implantação de teste também falhar, o perfil pai determinará que o perfil filho não deve receber tráfego, já que todos os pontos de extremidade filho não estão íntegros. Em seguida, o perfil pai distribui o tráfego para as outras regiões.

![Failover de perfil aninhado (comportamento padrão)][3]

Você pode estar satisfeito com essa disposição. Ou você deve estar preocupado com o fato de que todo o tráfego para Europa Ocidental agora vai para a implantação de teste em vez de um tráfego de subconjunto limitado. Independentemente da integridade da implantação de teste, você deseja fazer failover para as outras regiões quando a implantação de produção no Europa Ocidental falhar. Para habilitar esse failover, você pode especificar o parâmetro ' MinChildEndpoints ' ao configurar o perfil filho como um ponto de extremidade no perfil pai. O parâmetro determina o número mínimo de pontos de extremidade disponíveis no perfil filho. O valor padrão é ' 1 '. Para este cenário, você define o valor de MinChildEndpoints como 2. Abaixo desse limite, o perfil pai considera o perfil filho inteiro como indisponível e direciona o tráfego para os outros pontos de extremidade.

A figura a seguir ilustra essa configuração:

![Failover de perfil aninhado com ' MinChildEndpoints ' = 2][4]

> [!NOTE]
> O método de roteamento de tráfego de "prioridade" distribui todo o tráfego para um único ponto de extremidade. Portanto, há pouca finalidade em uma configuração de MinChildEndpoints diferente de ' 1 ' para um perfil filho.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exemplo 3: regiões de failover priorizadas no roteamento de tráfego de "desempenho"

O comportamento padrão para o método de roteamento de tráfego de "desempenho" é quando você tem pontos de extremidade em diferentes localizações geográficas que os usuários finais são roteados para o Endpoint "mais próximo" em termos da menor latência de rede.

No entanto, suponha que você prefira o failover de tráfego Europa Ocidental para o oeste dos EUA e apenas o tráfego direto para outras regiões quando os dois pontos de extremidade estiverem indisponíveis. Você pode criar essa solução usando um perfil filho com o método de roteamento de tráfego de "prioridade".

![Roteamento de tráfego de "desempenho" com failover preferencial][6]

Como o ponto de extremidade Europa Ocidental tem prioridade mais alta do que o ponto de extremidade oeste dos EUA, todo o tráfego é enviado para o ponto de extremidade Europa Ocidental quando ambos os pontos de extremidade estão online. Se Europa Ocidental falhar, seu tráfego será direcionado para o oeste dos EUA. Com o perfil aninhado, o tráfego é direcionado para Ásia Oriental somente quando o Europa Ocidental e o oeste dos EUA falham.

Você pode repetir esse padrão para todas as regiões. Substitua todos os três pontos de extremidade no perfil pai por três perfis filho, cada um fornecendo uma sequência de failover priorizada.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exemplo 4: controlando o roteamento de tráfego de "desempenho" entre vários pontos de extremidade na mesma região

Suponha que o método de roteamento de tráfego de "desempenho" seja usado em um perfil que tenha mais de um ponto de extremidade em uma região específica. Por padrão, o tráfego direcionado para essa região é distribuído uniformemente entre todos os pontos de extremidade disponíveis nessa região.

![Roteamento de tráfego de "desempenho" para distribuição de tráfego na região (comportamento padrão)][7]

Em vez de adicionar vários pontos de extremidade em Europa Ocidental, esses pontos de extremidade são colocados em um perfil filho separado. O perfil filho é adicionado ao pai como o único ponto de extremidade no Europa Ocidental. As configurações no perfil filho podem controlar a distribuição de tráfego com Europa Ocidental habilitando o roteamento de tráfego ponderado ou baseado em prioridade nessa região.

![Roteamento de tráfego de "desempenho" com distribuição de tráfego na região personalizada][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exemplo 5: configurações de monitoramento por ponto de extremidade

Suponha que você esteja usando o Gerenciador de tráfego para migrar de forma tranqüila o tráfego de um site da Web local herdado para uma nova versão baseada em nuvem hospedada no Azure. Para o site herdado, você deseja usar o URI de home page para monitorar a integridade do site. Mas, para a nova versão baseada em nuvem, você está implementando uma página de monitoramento Personalizada (caminho '/monitor.aspx ') que inclui verificações adicionais.

![Monitoramento de ponto de extremidade do Gerenciador de tráfego (comportamento padrão)][9]

As configurações de monitoramento em um perfil do Gerenciador de tráfego se aplicam a todos os pontos de extremidade em um único perfil. Com os perfis aninhados, você usa um perfil filho diferente por site para definir configurações de monitoramento diferentes.

![Monitoramento de ponto de extremidade do Gerenciador de tráfego com configurações por ponto de extremidade][10]

## <a name="faqs"></a>FAQs

* [Como fazer configurar perfis aninhados?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#traffic-manager-endpoint-monitoring)

* [Quantas camadas de aninhamento o Gerenciador de tráfego dá suporte?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-layers-of-nesting-does-traffic-manger-support)

* [Posso misturar outros tipos de ponto de extremidade com perfis filho aninhados, no mesmo perfil do Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [Como o modelo de cobrança se aplica a perfis aninhados?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-billing-model-apply-for-nested-profiles)

* [Há um impacto no desempenho para perfis aninhados?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-performance-impact-for-nested-profiles)

* [Como o Gerenciador de tráfego computa a integridade de um ponto de extremidade aninhado em um perfil pai?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [perfis do Gerenciador de tráfego](traffic-manager-overview.md)

Saiba como [criar um perfil do Gerenciador de tráfego](traffic-manager-create-profile.md)

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
