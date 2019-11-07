---
title: Referência do mecanismo de regras padrão da CDN do Azure | Microsoft Docs
description: A documentação de referência para o mecanismo de regras padrão da CDN do Azure combina condições e ações.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 6fb7e704f3d33cff8c29386b8aba9d8289037cbb
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615939"
---
# <a name="azure-cdn-from-microsoft-rules-engine-reference"></a>Azure CDN da referência do mecanismo de regras da Microsoft

Este artigo lista descrições detalhadas das condições e dos recursos de correspondência disponíveis para o [mecanismo de regras padrão](cdn-standard-rules-engine.md)da CDN (rede de distribuição de conteúdo) do Azure.

O mecanismo de regras foi projetado para ser a autoridade final sobre como tipos específicos de solicitações são processados pela CDN.

**Usos comuns**:

- Substituir ou definir uma política de cache Personalizada.
- Redirecionar solicitações.
- Modificar cabeçalhos de solicitação e resposta HTTP

## <a name="terminology"></a>Terminologia

Uma regra é definida com o uso de [**condições de correspondência**](cdn-standard-rules-engine-match-conditions.md)e [**ações**](cdn-standard-rules-engine-actions.md). Esses elementos são realçados na ilustração a seguir:

 ![Estrutura de regras da CDN](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Cada regra pode ter até 4 condições de correspondência e 3 ações. Há um máximo de 5 regras por ponto de extremidade da CDN. Além disso, há uma regra em vigor, por padrão, chamada de **regra global**. Essa é uma regra sem condições de correspondência, em que as ações definidas no sempre serão disparadas. Essa regra está incluída no limite atual da regra 5.

## <a name="syntax"></a>Sintaxe

A maneira como os caracteres especiais são tratados varia de acordo com a forma como uma condição de correspondência ou actopm manipula valores de texto. Uma condição de correspondência ou recurso pode interpretar o texto de uma das seguintes maneiras:

1. [**Valores literais**](#literal-values)
2. [**Valores curinga**](#wildcard-values)


### <a name="literal-values"></a>Valores literais

O texto que é interpretado como um valor literal trata todos os caracteres especiais, com exceção do símbolo%, como parte do valor que deve ser correspondido. Em outras palavras, uma condição de correspondência literal definida como `\'*'\` só é satisfeita quando esse valor exato (ou seja, `\'*'\`) é encontrado.

Um sinal de porcentagem é usado para indicar a codificação de URL (por exemplo, `%20`).

### <a name="wildcard-values"></a>Valores curinga

O texto que é interpretado como um valor de caractere curinga atribui significado adicional a caracteres especiais. A tabela a seguir descreve como o seguinte conjunto de caracteres é interpretado:

Espaço | Descrição
----------|------------
\ | Uma barra invertida é usada para escapar qualquer um dos caracteres especificados nesta tabela. Uma barra invertida deve ser especificada diretamente antes do caractere especial que deve ter escape.<br/>Por exemplo, a sintaxe a seguir escapa um asterisco: `\*`
% | Um sinal de porcentagem é usado para indicar a codificação de URL (por exemplo, `%20`).
\* | Um asterisco é um caractere curinga que representa um ou mais caracteres.
Espaço | Um caractere de espaço indica que uma condição de correspondência pode ser satisfeita por um dos valores ou padrões especificados.
valor | Uma aspa simples não tem significado especial. No entanto, um conjunto de aspas simples é usado para indicar que um valor deve ser tratado como um valor literal. Ele pode ser usado das seguintes maneiras:<br><br/>-Permite que uma condição de correspondência seja satisfeita sempre que o valor especificado corresponder a qualquer parte do valor de comparação.  Por exemplo, `'ma'` corresponderia a qualquer uma das seguintes cadeias de caracteres: <br/><br/>/Business/**ma**rathon/Asset.htm<br/>r **ma**p. gif<br/>/business/template. **ma**p<br /><br />-Permite que um caractere especial seja especificado como um caractere literal. Por exemplo, você pode especificar um caractere de espaço literal ao colocar um caractere de espaço dentro de um conjunto de aspas simples (ou seja, `' '` ou `'sample value'`).<br/>-Permite que um valor em branco seja especificado. Especifique um valor em branco especificando um conjunto de aspas simples (ou seja, ' ').<br /><br/>**Importante:**<br/>-Se o valor especificado não contiver um caractere curinga, ele será automaticamente considerado como um valor literal, o que significa que não é necessário especificar um conjunto de aspas simples.<br/>-Se uma barra invertida não escapar de outro caractere nesta tabela, ela será ignorada quando for especificada dentro de um conjunto de aspas simples.<br/>-Outra maneira de especificar um caractere especial como um caractere literal é escapar dele usando uma barra invertida (ou seja, `\`).

## <a name="next-steps"></a>Passos seguintes

- [Condições de correspondência do mecanismo de regras padrão](cdn-standard-rules-engine-match-conditions.md)
- [Ações do mecanismo de regras padrão](cdn-standard-rules-engine-actions.md)
- [Impor HTTPS usando o mecanismo de regras padrão](cdn-standard-rules-engine.md)
- [Visão geral da CDN do Azure](cdn-overview.md)
