---
title: 'Porta frontal Azure: Conjunto de regras'
description: Este artigo fornece uma visão geral do conjunto de regras standard/premium da porta frontal Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 8e6ceebc9e92dabe66baeb9aeff0ae9692e2bdad
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099972"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>O que é um conjunto de regras para Azure Front Door Standard/Premium (Preview)?

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

Um Conjunto de Regras é um motor de regras personalizado que agru cria uma combinação de regras num único conjunto que pode associar a várias rotas. O Conjunto de Regras permite-lhe personalizar como os pedidos são processados no limite e como a Porta Frontal Azure lida com esses pedidos.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="common-supported-scenarios"></a>Cenários comuns apoiados

* Implementação de cabeçalhos de segurança para prevenir vulnerabilidades baseadas no navegador como HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy, X-Frame-Options e Access-Control-Allow-Origin para cenários de partilha de recursos de origem cruzada (CORS). Os atributos baseados em segurança também podem ser definidos com cookies.

* Pedidos de rota para versões móveis ou desktop da sua aplicação com base no tipo de dispositivo do cliente.

* Utilizando capacidades de redirecionamento para devolver 301, 302, 307 e 308 redirecionamentos para o cliente para direcioná-los para novos nomes de hospedeiros, caminhos, cadeias de consulta ou protocolos.

* Modificar dinamicamente a configuração do caching da sua rota com base nos pedidos de entrada.

* Reescreva o caminho URL do pedido e reencasse o pedido para a origem adequada no seu grupo de origem configurado.

* Adicione, modifique ou remova o cabeçalho de pedido/resposta para ocultar informações sensíveis ou capturar informações importantes através de cabeçalhos.

* Suporte variáveis de servidor para alterar dinamicamente os cabeçalhos de pedido/resposta ou os caminhos/cadeias de consulta URL, por exemplo, quando uma nova carga de página ou quando um formulário é publicado. A variável do servidor é suportada apenas em **[ações de Definição de Regras.](concept-rule-set-actions.md)**

## <a name="architecture"></a>Arquitetura

Regra Definida lida com pedidos na borda. Quando um pedido chega ao seu ponto final Azure Front Door Standard/Premium, o WAF é executado primeiro, seguido das definições configuradas na Rota. Essas definições incluem o Conjunto de Regras associado à Rota. Os conjuntos de regras são processados de cima para baixo na Rota. O mesmo se aplica às regras dentro de um Conjunto de Regras. Para que todas as ações de cada regra sejam executadas, todas as condições de jogo dentro de uma regra têm de ser satisfeitas. Se um pedido não corresponder a nenhuma das condições da configuração do Conjunto de Regras, apenas as configurações na Rota serão executadas.

Se **parar de avaliar as restantes regras** ser verificada, então todos os conjuntos de regras restantes associados à Rota não serão executados.  

### <a name="example"></a>Exemplo

No diagrama seguinte, as políticas da WAF são executadas primeiro. Um Conjunto de Regras é configurado para anexar um cabeçalho de resposta. Em seguida, o cabeçalho muda a idade máxima do controlo da cache se a condição de jogo for satisfeita.

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="Diagrama que mostra arquitetura do Conjunto de Regras." lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>Terminologia

Com o Conjunto de Regras da Porta Frontal Azure, pode criar uma combinação de configuração de Conjunto de Regras, cada uma composta por um conjunto de regras. As seguintes linhas de saída algumas terminologias úteis que encontrará ao configurar o seu Conjunto de Regras.

Para obter um limite de quota, consulte os [limites de subscrição e serviço da Azure, quotas e constrangimentos.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

* *Regras definidas*: Conjunto de regras que se associam a uma ou [múltiplas Rotas](concept-route.md). Cada configuração é limitada a 25 regras. Pode criar até 10 configurações.

* *Regras Definidas Regra :* Uma regra composta por até 10 condições de jogo e 5 ações. As regras são locais para um Conjunto de Regras e não podem ser exportadas para uso em todos os conjuntos de regras. Os utilizadores podem criar a mesma regra em vários conjuntos de regras.

* *Condição de jogo*: Existem muitas condições de jogo que podem ser utilizadas para analisar os seus pedidos de entrada. Uma regra pode conter até 10 condições de jogo. As condições de correspondência são avaliadas com um operador **E.** *A expressão regular é suportada em condições.* Uma lista completa das condições de jogo pode ser encontrada na [condição definida pela regra](concept-rule-set-match-conditions.md).

* *Ação*: As ações ditam como a AFD lida com os pedidos de entrada com base nas condições de correspondência. Pode modificar comportamentos de caching, modificar cabeçalhos de pedido/cabeçalhos de resposta, reescrever URL e redirecionar URL. *As variáveis do servidor são suportadas em Ação*. Uma regra pode conter até 10 condições de jogo. Uma lista completa de ações pode ser encontrada [Ações definidas por regras](concept-rule-set-actions.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar um Padrão/Premium da Porta da Frente.](create-front-door-portal.md)
* Saiba como configurar o seu primeiro [Conjunto de Regras](how-to-configure-rule-set.md).
 
