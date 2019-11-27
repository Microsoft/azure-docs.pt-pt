---
title: Modelos de tokens do Azure Blockchain
description: Os modelos de tokens do Azure Blockchain são modelos padronizados e reutilizáveis que simplificam a criação e a implantação de tokens baseados em razão.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324974"
---
# <a name="azure-blockchain-tokens-templates"></a>Modelos de tokens do Azure Blockchain

[!INCLUDE [Preview note](./includes/preview.md)]

Um modelo de tokens Blockchain do Azure é um modelo padronizado e reutilizável que simplifica a criação e a implantação de tokens baseados em razão. Um modelo consiste em uma fórmula, que se baseia na gramática de [ttf (estrutura de taxonomia de token)](overview.md#token-taxonomy-framework) . A gramática abrange o tipo de token base e o conjunto de comportamentos para o token.  

Por exemplo, o modelo de token **τϜ {d, m, b, r}** descreve um token de base fungível que é dividable, mentable, gravável e tem suporte de função.
  
## <a name="base-token-types"></a>Tipos de token base

Ao definir e criar o token baseado em razão para seu ativo específico, é importante considerar qual token base usar.

### <a name="fungible"></a>Fungível

Os tokens fungível (τF) têm um valor intercambiável entre si, desde que estejam na mesma classe ou série. Um token tem o mesmo valor de outro token ou uma determinada quantidade de tokens tem o mesmo valor de outra quantidade igual. Por exemplo, um dólar é um token fungível. Se duas pessoas estiverem mantendo uma fatura de dólar, elas poderão trocar as faturas de dólar sem conseqüência. As notas de dólar têm valor igual. 

### <a name="non-fungible"></a>Não fungível

Os tokens não fungível (τN) não são intercambiáveis com outros tokens do mesmo tipo, pois normalmente têm valores diferentes. Por exemplo, um título de propriedade é um token não fungível. Os títulos de propriedade para dois Apartments diferentes em um apartamento complexo não são necessariamente de valor igual, devido ao local da unidade ou do chão da unidade. O valor percebido dos dois tokens de título de propriedade não é igual.

### <a name="hybrid"></a>Híbrido

Tokens híbridos são tokens que têm componentes de tokens fungível e tokens não fungível. Um token híbrido é um tipo de token base que possui uma classe do outro tipo de token.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Base não fungível híbrida com segmentos fungível

Uma base não fungível híbrida com token de segmentos fungível tem uma base diferente de fungível com segmentos de token fungível.
Por exemplo, um tíquete de concerto é um token híbrido em que a data e a hora do concerto são o token de base não fungível. Os tíquetes em várias seções de assentos para o conjunto fornecido são os segmentos com tokens fungível. Os tíquetes são intercambiáveis em suas seções individuais de assentos, mas não em seções.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Base fungível híbrida com segmentos não fungível

Uma base de fungível híbrida com um token de segmentos não fungível tem uma base fungível com segmentos de token não fungível. Por exemplo, uma segurança com suporte de hipoteca é um token híbrido em que vários proprietários são a base fungível que é dividida em vários proprietários. A segurança é intercambiável. As hipotecas individuais são os segmentos não fungível que representam a segurança específica da hipoteca com suporte.

## <a name="token-behaviors"></a>Comportamentos de token

Um comportamento de token define os recursos ou as restrições do token. O comportamento inclui propriedades de suporte que fazem parte da definição do token. Os comportamentos podem ser aplicados em todos os tipos de token ou apenas um. Os comportamentos podem ser internos ou externos, dependendo de quais são os efeitos do comportamento. Um comportamento interno habilita ou restringe as propriedades no próprio token. Um comportamento externo habilita ou restringe a invocação do comportamento de um ator externo.

Para obter mais informações sobre os comportamentos de token de geração de tokens (TTF) dos tokens de Blockchain do Azure com suporte, consulte [composição de token](composability.md).

## <a name="pre-built-token-templates"></a>Modelos de token pré-criados

Os tokens Blockchain do Azure fornecem quatro modelos de token pré-criados que podem ser usados sem modificação. Você pode chamar esses modelos predefinidos para a maioria dos casos de uso para começar a criar, implantar e gerenciar seus tokens rapidamente.

### <a name="commodity-tokens"></a>Tokens de mercadoria

Os tokens de mercadoria têm um valor consistente e são transferíveis. Por exemplo, um cilindro de óleo ou uma unidade de energia.

**τF {~ d, t, m, b, r}** -fungível, completo, transferível, mentable, gravável e tem suporte de função

Muitos cenários de blockchain exigem transparência e visibilidade na cadeia de suprimentos ou em várias organizações. Os tokens de mercadoria são baseados nesses casos de uso comuns. Os tokens são intercambiáveis e consistentes. O modelo de token de mercadoria é flexível e personalizável com metadados.

### <a name="qualified-tokens"></a>Tokens qualificados

Os tokens qualificados representam algo obtido e geralmente são associados a uma entidade e não podem ser transferidos. Por exemplo, um diploma ou uma violação de estacionamento.

**τN {s, ~ t}** -não fungível, singleton e não transferível

Vários cenários de auditoria e atestado exigem que a propriedade do token não possa ser alterada. Há um conjunto de casos de uso, que têm a necessidade de fornecer um token qualificado se a associação for boa ou ruim.

### <a name="asset-tokens"></a>Tokens de ativo

Os tokens de ativo têm um valor exclusivo dependente do item e não são mercadoria. Por exemplo, um artefato de museu ou um título de propriedade.

**τN {s, t}** -não fungível, singleton e transferível

Tokens de ativo podem ser confundidos com tokens de mercadoria. A principal diferença entre os dois tokens é que os tokens de ativo são inerentemente exclusivos e o valor é independente do tipo de token. Por exemplo, um pedaço de arte como uma pintura de óleo por um artista estabelecido é um token de ativo. No entanto, uma impressão de arte do Mona Lisa é considerada um token de mercadoria. Da mesma forma, um título de propriedade é um token de ativo desde que o valor exista nas qualidades subjetivas da propriedade.

### <a name="ticket-tokens"></a>Tokens de tíquete

Tokens de tíquete têm valor consistente, mas normalmente expiram. Por exemplo, um tíquete de plano.

**τN {m, b, r}** -não fungível, mentable, gravável e tem suporte de função.

Tokens de tíquete normalmente têm uma data de expiração que os torna diferentes de um token de mercadoria regular. Por exemplo, um bilhete de avião, um bilhete de concerto ou um bilhete esportivo têm opções de assentos atribuídos com datas específicas de uso. Você não pode trocar tíquetes entre datas ou áreas de assentos facilmente.

## <a name="next-steps"></a>Passos Seguintes

Se você precisar de mais flexibilidade para seu cenário, saiba como criar seus próprios modelos de token usando a [composição de tokens](composability.md).
