---
title: Referência do mecanismo de regras padrão para a CDN do Azure | Microsoft Docs
description: Documentação de referência para condições de correspondência e ações no mecanismo de regras padrão para a rede de distribuição de conteúdo do Azure (CDN do Azure).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: aa401150ee7a0f02e809ad702b8247e18081c8a3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171569"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Referência do mecanismo de regras padrão para a CDN do Azure

No [mecanismo de regras padrão](cdn-standard-rules-engine.md) para a rede de distribuição de conteúdo do Azure (CDN do Azure), uma regra consiste em uma ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das condições de correspondência e recursos que estão disponíveis no mecanismo de regras padrão para a CDN do Azure.

O mecanismo de regras foi projetado para ser a autoridade final sobre como tipos específicos de solicitações são processados pela CDN do Azure padrão.

**Usos comuns para as regras**:

- Substituir ou definir uma política de cache Personalizada.
- Redirecionar solicitações.
- Modificar cabeçalhos de solicitação e resposta HTTP.

## <a name="terminology"></a>Terminologia

Para definir uma regra no mecanismo de regras, defina [condições de correspondência](cdn-standard-rules-engine-match-conditions.md) e [ações](cdn-standard-rules-engine-actions.md):

 ![Estrutura de regras da CDN do Azure](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Cada regra pode ter até quatro condições de correspondência e três ações. Cada ponto de extremidade da CDN do Azure pode ter até cinco regras. 

Incluído no limite de cinco regras atual para um ponto de extremidade da CDN do Azure é uma *regra global*padrão. A regra global não tem condições de correspondência e as ações definidas em uma regra global sempre são disparadas.

## <a name="syntax"></a>Sintaxe

A forma como os caracteres especiais são tratados em uma regra varia de acordo com a forma como as condições de correspondência e as ações diferentes manipulam valores de texto. Uma condição de correspondência ou ação pode interpretar o texto de uma das seguintes maneiras:

- [Valores literais](#literal-values)
- [Valores curinga](#wildcard-values)


### <a name="literal-values"></a>Valores literais

O texto que é interpretado como um valor literal trata todos os caracteres especiais *, exceto o símbolo%* , como parte do valor que deve ser correspondido em uma regra. Por exemplo, uma condição de correspondência literal definida como `'*'` é satisfeita somente quando o valor exato `'*'` é encontrado.

Um sinal de porcentagem é usado para indicar a codificação de URL (por exemplo, `%20`).

### <a name="wildcard-values"></a>Valores curinga

O texto que é interpretado como um valor de caractere curinga atribui significado adicional a caracteres especiais. A tabela a seguir descreve como os caracteres especiais específicos são interpretados no mecanismo de regras padrão:

Caráter | Descrição
----------|------------
\ | Uma barra invertida é usada para escapar qualquer um dos caracteres especificados nesta tabela. Uma barra invertida deve ser especificada diretamente antes do caractere especial que deve ter escape. Por exemplo, a sintaxe a seguir escapa um asterisco: `\*`
% | Um sinal de porcentagem é usado para indicar a codificação de URL (por exemplo, `%20`).
\* | Um asterisco é um caractere curinga que representa um ou mais caracteres.
disco | Um caractere de espaço indica que uma condição de correspondência pode ser satisfeita por qualquer um dos valores ou padrões especificados.
aspas simples | Uma aspa simples não tem significado especial. No entanto, um conjunto de aspas simples indica que um valor deve ser tratado como um valor literal. Aspas simples podem ser usadas das seguintes maneiras:<ul><li>Para permitir que uma condição de correspondência seja satisfeita sempre que o valor especificado corresponder a qualquer parte do valor de comparação.  Por exemplo, `'ma'` corresponderia a qualquer uma das seguintes cadeias de caracteres: <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template. **ma**p</li></ul><li>Para permitir que um caractere especial seja especificado como um caractere literal. Por exemplo, você pode especificar um caractere de espaço literal ao colocar um caractere de espaço em um conjunto de aspas simples (`' '` ou `'<sample value>'`).</li><li>Para permitir que um valor em branco seja especificado. Especifique um valor em branco especificando um conjunto de aspas simples ( **' '** ).</li></ul>**Importante**:<br /><ul><li>Se o valor especificado não contiver um caractere curinga, o valor será automaticamente considerado um valor literal. Você não precisa especificar um conjunto de aspas simples para um valor literal.</li><li>Se uma barra invertida não for usada para escapar de outro caractere nesta tabela, a barra invertida será ignorada quando for especificada em um conjunto de aspas simples.</li><li>Outra maneira de especificar um caractere especial como um caractere literal é fazer o escape dele usando uma barra invertida (`\`).</li></ul>

## <a name="next-steps"></a>Passos seguintes

- [Condições de correspondência no mecanismo de regras padrão](cdn-standard-rules-engine-match-conditions.md)
- [Ações no mecanismo de regras padrão](cdn-standard-rules-engine-actions.md)
- [Impor o HTTPS usando o mecanismo de regras padrão](cdn-standard-rules-engine.md)
- [Visão geral da CDN do Azure](cdn-overview.md)
