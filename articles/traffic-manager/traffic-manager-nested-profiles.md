---
title: Perfis aninhados do Gestor de tráfego no Azure
titlesuffix: Azure Traffic Manager
description: Este artigo explica a funcionalidade de 'Perfis aninhados' do Gestor de tráfego do Azure
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
ms.openlocfilehash: 3c5459d0474ecd45501e634c4777fa178386183c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071146"
---
# <a name="nested-traffic-manager-profiles"></a>Perfis do Gestor de Tráfego aninhados

O Gestor de tráfego inclui uma variedade de métodos de encaminhamento de tráfego de mensagens em fila que permitem-lhe controlar a forma como o Gestor de tráfego escolhe qual ponto de extremidade deve receber o tráfego de cada utilizador final. Para obter mais informações, consulte [métodos de encaminhamento de tráfego do Gestor de tráfego](traffic-manager-routing-methods.md).

Cada perfil do Traffic Manager Especifica um único método de encaminhamento de tráfego. No entanto, há cenários que exigem que o encaminhamento fornecidos por um único perfil do Gestor de tráfego a encaminhamento de tráfego mais sofisticados. Pode aninhar perfis do Gestor de tráfego para combinar os benefícios de mais de um método de encaminhamento de tráfego. Perfis aninhados permitem-lhe substituir o comportamento de Gestor de tráfego predefinido para suporte maior e implementações de aplicações mais complexas.

Os exemplos seguintes mostram como utilizar perfis aninhados do Gestor de tráfego em vários cenários.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exemplo 1: A combinação de encaminhamento de tráfego "Desempenho" e 'Ponderado'

Suponha que implementou uma aplicação nas seguintes regiões do Azure: E.U.A. oeste, Europa Ocidental e Sudeste asiático. Utilize o método de encaminhamento de tráfego do Gestor de tráfego "Performance" para distribuir o tráfego para a região mais próximo do utilizador.

![Perfil do Gestor de tráfego de único][4]

Agora, suponha que desejar testar uma atualização para o seu serviço antes de distribuí-lo mais amplamente. Que pretende utilizar o método de encaminhamento de tráfego de mensagens em fila 'ponderado' para direcionar numa pequena percentagem de tráfego para a implementação de teste. Configurar a implementação de teste em conjunto com a implementação de produção existente na Europa Ocidental.

Não é possível combinar os dois ponderada e "tráfego de encaminhamento de desempenho num único perfil. Para suportar este cenário, crie um perfil do Gestor de tráfego usando os dois pontos finais da Europa Ocidental e o método de encaminhamento de tráfego de 'Ponderado'. Em seguida, adicione este perfil de "subordinado" como um ponto final para o perfil de 'parent'. O perfil de principal ainda usa o método de encaminhamento de tráfego de desempenho e contém outras implementações globais como pontos finais.

O diagrama seguinte ilustra este exemplo:

![Perfis do Gestor de Tráfego aninhados][2]

Nesta configuração, direcionado através do perfil de principal de tráfego distribui o tráfego entre regiões normalmente. Dentro da Europa Ocidental, o perfil aninhado distribui o tráfego para os pontos finais de produção e teste, de acordo com os pesos atribuídos.

Quando o perfil de pai usa o método de encaminhamento de tráfego de "Performance", cada ponto de extremidade tem de atribuir uma localização. A localização é atribuída ao configurar o ponto final. Selecione a região do Azure mais próxima da sua implementação. Regiões do Azure são os valores de localização suportados pela tabela de latência de Internet. Para obter mais informações, consulte [Gestor de tráfego "Performance" método de encaminhamento de tráfego](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exemplo 2: Monitorização de pontos finais nos perfis aninhados

O Gestor de tráfego monitoriza ativamente o estado de funcionamento de cada ponto de extremidade de serviço. Se um ponto final está danificado, o Gestor de tráfego direcionará os usuários aos pontos finais alternativos para preservar a disponibilidade do seu serviço. Esse comportamento de monitorização e ativação pós-falha do ponto final se aplica a todos os métodos de encaminhamento de tráfego. Para obter mais informações, consulte [monitorização de ponto final do Gestor de tráfego](traffic-manager-monitoring.md). Monitorização de pontos finais funciona de forma diferente para aninhados perfis. Com perfis aninhados, o perfil de principal não executa verificações de estado de funcionamento no filho diretamente. Em vez disso, o estado de funcionamento dos pontos finais do perfil de subordinado é utilizado para calcular o estado de funcionamento geral do perfil subordinado. Estas informações de estado de funcionamento serão propagadas a hierarquia de perfil aninhada. O perfil de principal utiliza este estado de funcionamento agregado para determinar se deve direcionar o tráfego para o perfil de subordinados. Consulte a [FAQ](traffic-manager-FAQs.md#traffic-manager-nested-profiles) para obter detalhes completos sobre como monitorizar o estado de funcionamento de perfis aninhados.

Retornando ao exemplo anterior, suponha que a implementação de produção na Europa Ocidental falhar. Por predefinição, o perfil "subordinado" direciona todo o tráfego para a implementação de teste. Se a implementação de teste também falhar, o perfil de principal determina que o perfil de subordinado não deverá receber tráfego, uma vez que todos os pontos finais de subordinados estão danificados. Em seguida, o perfil de principal distribui o tráfego para as outras regiões.

![Aninhada perfil ativação pós-falha (comportamento predefinido)][3]

Pode estar satisfeito com essa organização. Ou poderá estar preocupado que todo o tráfego para a Europa Ocidental vai agora a implementação de teste, em vez de tráfego um subconjunto limitado. Independentemente do Estado de funcionamento a implementação de teste, que pretende efetuar a ativação pós-falha para as outras regiões quando ocorre uma falha de implementação de produção na Europa Ocidental. Para ativar esta ativação pós-falha, pode especificar o parâmetro de 'MinChildEndpoints' quando configurar o perfil de filho como um ponto final no perfil de principal. O parâmetro determina o número mínimo de pontos finais disponíveis no perfil subordinado. O valor predefinido é '1'. Para este cenário, definir o valor de MinChildEndpoints para 2. Abaixo deste limiar, o perfil de principal considera o perfil de subordinados inteira fique indisponível e direciona o tráfego para os outros pontos de extremidade.

A figura a seguir ilustra esta configuração:

![Aninhada de ativação pós-falha de perfil com 'MinChildEndpoints' = 2][4]

> [!NOTE]
> O método de encaminhamento de tráfego de 'Priority' distribui todo o tráfego para um único ponto final. Portanto há pouco finalidade numa definição MinChildEndpoints diferente de '1' para um perfil de subordinados.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exemplo 3: Regiões de ativação pós-falha com prioridade no encaminhamento de tráfego "Performance"

O comportamento predefinido para o método de encaminhamento de tráfego de "Performance" é quando tem pontos finais em diferentes localizações geográficas, que os utilizadores finais são encaminhados para o ponto final "mais próximo" em termos a menor latência de rede.

No entanto, suponha que prefere a ativação pós-falha de tráfego de Europa Ocidental para E.U.A. oeste e direcionar o tráfego para outras regiões apenas quando os dois pontos finais não estão disponíveis. Pode criar esta solução com um perfil de subordinados com o método de encaminhamento de tráfego de 'Priority'.

![Encaminhamento com preferencial de ativação pós-falha de tráfego de "Performance"][6]

Uma vez que o ponto de extremidade da Europa Ocidental tem prioridade mais alta do que o ponto de extremidade do Oeste dos E.U.A., todo o tráfego é enviado para o ponto de extremidade da Europa Ocidental, quando ambos os pontos finais estão online. Se falhar a Europa Ocidental, o tráfego é direcionado para E.U.A. oeste. Com o perfil aninhado, o tráfego é direcionado para a Ásia Oriental apenas quando a Europa Ocidental e E.U.A. oeste falhar.

Pode repetir esse padrão para todas as regiões. Substitua todos os três pontos de extremidade no perfil de principal três perfis de subordinados, cada uma oferecendo uma sequência de ativação pós-falha priorizada.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exemplo 4: Controlar o encaminhamento de tráfego "Performance" entre vários pontos de extremidade na mesma região

Suponha que o desempenho' ' é utilizado o método de encaminhamento de tráfego num perfil que tem mais de um ponto final numa região específica. Por predefinição, o tráfego direcionado para essa região é distribuído uniformemente por todos os pontos finais disponíveis nessa região.

![Distribuição de tráfego na região (comportamento predefinido) de encaminhamento de tráfego de "Performance"][7]

Em vez de adicionar vários pontos de extremidade na Europa Ocidental, esses pontos de extremidade encontram-se entre um perfil de subordinada em separado. O perfil de subordinado é adicionado ao principal como o único ponto de extremidade na Europa Ocidental. As definições no perfil subordinado podem controlar a distribuição de tráfego com a Europa Ocidental, permitindo que o encaminhamento de tráfego com base em prioridade ou ponderado dentro dessa região.

![Tráfego de "Performance" encaminhamento com a distribuição de tráfego de na região e personalizados][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exemplo 5: Definições de monitorização por ponto final

Suponha que estiver a utilizar o Gestor de tráfego para migrar sem problemas tráfego a partir de um legado web site local para uma nova versão com base na Cloud alojada no Azure. Para o site de legado, que pretende utilizar a home page do URI para monitorizar o estado de funcionamento do site. Mas para a nova versão com base na Cloud, estão a implementar uma página personalizada de monitorização (caminho "/ monitor.aspx") que inclui verificações adicionais.

![Ponto final do Gestor de tráfego de monitorização (comportamento predefinido)][9]

Aplicam as definições de monitorização num perfil de Gestor de tráfego para todos os pontos finais dentro de um único perfil. Com perfis aninhados, utilizar um perfil de filho diferentes por site para definir diferentes definições de monitorização.

![Ponto final do Gestor de tráfego de monitorização com as definições de ponto-final][10]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [perfis do Gestor de tráfego](traffic-manager-overview.md)

Saiba como [criar um perfil do Gestor de tráfego](traffic-manager-create-profile.md)

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
