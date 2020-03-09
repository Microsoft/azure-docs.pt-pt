---
title: Modelos de tokens Azure Blockchain
description: Os modelos de tokens Azure Blockchain são modelos padronizados e reutilizáveis que simplificam a criação e implementação de fichas baseadas em livros.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360746"
---
# <a name="azure-blockchain-tokens-templates"></a>Modelos de tokens Azure Blockchain

[!INCLUDE [Preview note](./includes/preview.md)]

Um modelo de Tokens Azure Blockchain é um modelo padronizado e reutilizável que simplifica a criação e implantação de fichas baseadas em livros. Um modelo consiste numa fórmula, que se baseia na gramática [token Taxonomy Framework (TTF).](overview.md#token-taxonomy-framework) A gramática engloba o tipo de símbolo base e o conjunto de comportamentos para o símbolo.  

Por exemplo, o modelo de ficha sid **{d,m,b,r}** descreve um símbolo de base fungível que é subdividável, mantéu, queimou e tem suporte para papéis.
  
## <a name="base-token-types"></a>Tipos de fichas de base

Ao definir e criar o símbolo baseado em livros para o seu ativo particular, é importante considerar qual o símbolo base a usar.

### <a name="fungible"></a>Fungible

As fichas fungíveis (τF) têm valor permutável entre si, desde que estejam na mesma classe ou série. Um token tem o mesmo valor que outro símbolo ou uma determinada quantidade de fichas tem o mesmo valor que outra quantidade igual. Por exemplo, um dólar é um símbolo fungível. Se duas pessoas tiverem uma nota de dólar, podem trocar estas notas de dólar sem consequências. As notas de dólar têm o mesmo valor. 

### <a name="non-fungible"></a>Não fungível

Tokens não fungíveis (τN) não são permutáveis com outras fichas do mesmo tipo que normalmente têm valores diferentes. Por exemplo, um título de propriedade é um símbolo não fungível. Os títulos de propriedade para dois apartamentos diferentes em um complexo de apartamentos não são necessariamente de igual valor, devido à localização da unidade ou em que piso a unidade está. O valor percebido das duas fichas do título de propriedade não é igual.

### <a name="hybrid"></a>Híbrido

Fichas híbridas são fichas que têm componentes de tokens fungíveis e tokens não fungíveis. Um símbolo híbrido é um tipo de símbolo de base que possui uma classe do outro tipo de símbolo.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Base híbrida não fungível com segmentos fungíveis

Uma base híbrida não fungível com segmentos fungíveis token tem uma base não fungível com segmentos de token fungíveis.
Por exemplo, um bilhete de concerto é um símbolo híbrido onde a data e a hora do concerto são o símbolo base não fungível. Os bilhetes em várias secções de assentos para o concerto dado são os segmentos com fichas fungíveis. Os bilhetes são permutáveis nas suas secções individuais de assentos, mas não em todas as secções.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Base fungível híbrida com segmentos não fungíveis

Uma base fungível híbrida com um token de segmentos não fungíveis tem uma base fungível com segmentos token não fungíveis. Por exemplo, uma garantia apoiada por hipotecas é um símbolo híbrido onde vários proprietários são a base fungível que é dividida entre muitos proprietários. A segurança é permutável. As hipotecas individuais são os segmentos não fungíveis que representam a garantia específica apoiada pela hipoteca.

## <a name="token-behaviors"></a>Comportamentos simbólicos

Um comportamento simbólico define capacidades ou restrições do símbolo. O comportamento inclui propriedades de suporte que fazem parte da definição simbólica. Os comportamentos podem ser aplicados em todos os tipos de token ou apenas um. Os comportamentos podem ser internos ou externos dependendo dos efeitos do comportamento. Um comportamento interno permite ou restringe propriedades no próprio símbolo. Um comportamento externo permite ou restringe a invocação do comportamento de um ator externo.

Para obter mais informações sobre os tokens da Azure Blockchain, apoiou comportamentos simbólicos token Taxonomy Framework (TTF), consulte a [composição simbólica](composability.md).

## <a name="pre-built-token-templates"></a>Modelos de fichapré-construídos

O Azure Blockchain Tokens fornece quatro modelos de fichapré-construídos que podem ser usados sem modificação. Você pode chamar estes modelos pré-construídos para a maioria dos casos de uso para começar a criar, implementar e gerir os seus tokens rapidamente.

### <a name="commodity-tokens"></a>Fichas de mercadoria

As fichas de mercadoria têm um valor consistente e são transferíveis. Por exemplo, um barril de petróleo ou uma unidade de energia.

**τF{~d,t,m,b,r}** - fungible, inteiro, transferível, mintável, queime, e tem suporte a papéis

Muitos cenários blockchain requerem transparência e visibilidade em toda a cadeia de fornecimento ou em várias organizações. As fichas de mercadoria baseiam-se nestes casos de uso comum. As fichas são permutáveis e consistentes. O modelo de ficha de mercadoria é flexível e personalizável com metadados.

### <a name="qualified-tokens"></a>Fichas qualificadas

Fichas qualificadas representam algo ganho e geralmente estão associados a uma entidade e não podem ser transferidos. Por exemplo, um diploma ou uma violação do estacionamento.

**τN{s,~t}** - não fungível, singleton e não transferível

Vários cenários de auditoria e atestação exigem que a propriedade do símbolo não possa ser alterada. Há um conjunto de casos de uso, que têm a necessidade de fornecer um símbolo qualificado se a associação é boa ou má.

### <a name="asset-tokens"></a>Fichas de ativos

As fichas de ativos têm um valor único dependente do item e não são commoditizadas. Por exemplo, um artefacto de museu ou um título de propriedade.

**τN{s,t}** - não fungível, singleton e transferível

Fichas de ativos podem ser confundidas com fichas de mercadoria. A grande diferença entre os dois tokens é que os tokens de ativos são inerentemente únicos, e o valor é independente do tipo de símbolo que é. Por exemplo, uma peça de arte como uma pintura a óleo de um artista estabelecido é um símbolo de mais-valia. No entanto, uma impressão artística da Mona Lisa é considerada um símbolo de mercadoria. Da mesma forma, um título de propriedade é um símbolo de ativo uma vez que o valor existe nas qualidades subjetivas da propriedade.

### <a name="ticket-tokens"></a>Fichas de bilhete

As fichas dos bilhetes têm um valor consistente, mas normalmente expiram. Por exemplo, um bilhete de avião.

**τN{m,b,r}** - não fungível, hortelã, queimável e ter suporte de papéis.

As fichas de bilhete normalmente têm uma data de validade que os torna diferentes de um símbolo de mercadoria regular. Por exemplo, um bilhete de avião, bilhete de concerto ou bilhete desportivo todos têm opções de lugares atribuídos com datas de utilização específicas. Não é possível trocar facilmente bilhetes entre datas ou áreas de estar.

## <a name="next-steps"></a>Passos seguintes

Se necessitar de mais flexibilidade para o seu cenário, aprenda a criar os seus próprios modelos simbólicos usando [a composição simbólica](composability.md).
