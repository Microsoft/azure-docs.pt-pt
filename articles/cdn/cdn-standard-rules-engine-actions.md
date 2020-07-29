---
title: Ações no Standard regras motor para Azure CDN / Microsoft Docs
description: Documentação de referência para ações no motor de regras standard para a Rede de Entrega de Conteúdos Azure (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 29138b4fc6716ae5361cc4d7f97ceba41b90c2da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81259957"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Ações no motor standard para Azure CDN

No [motor de regras Standard](cdn-standard-rules-engine.md) para Azure Content Delivery Network (Azure CDN), uma regra consiste numa ou mais condições de correspondência e numa ação. Este artigo fornece descrições detalhadas das ações que pode usar no motor de regras Standard para Azure CDN.

A segunda parte de uma regra é uma ação. Uma ação define o comportamento que é aplicado ao tipo de pedido que uma condição de correspondência ou conjunto de condições de jogo identifica.

## <a name="actions"></a>Ações

As seguintes ações estão disponíveis para utilizar no motor de regras Standard para Azure CDN. 

### <a name="cache-expiration"></a>Expiração da cache

Utilize esta ação para substituir o valor do tempo de vida (TTL) do ponto final para pedidos que as regras correspondem às condições especificam.

#### <a name="required-fields"></a>Campos necessários

Comportamento da cache |  Descrição              
---------------|----------------
Cache de bypass | Quando esta opção é selecionada e a regra coincide, o conteúdo não está em cache.
Substituição | Quando esta opção é selecionada e a regra coincide, o valor TTL devolvido da sua origem é substituído com o valor especificado na ação.
Conjunto se faltar | Quando esta opção é selecionada e a regra coincide, se nenhum valor TTL foi devolvido da sua origem, a regra define o TTL ao valor especificado na ação.

#### <a name="additional-fields"></a>Campos adicionais

Dias | Horas | Minutos | Segundos
-----|-------|---------|--------
int | int | int | int 

### <a name="cache-key-query-string"></a>Cadeia de consulta chave cache

Utilize esta ação para modificar a chave cache com base em cadeias de consulta.

#### <a name="required-fields"></a>Campos necessários

Comportamento | Descrição
---------|------------
Incluir | Quando esta opção é selecionada e a regra coincide, as cadeias de consulta especificadas nos parâmetros são incluídas quando a tecla de cache é gerada. 
Colocar em cache todos os URL exclusivos | Quando esta opção é selecionada e a regra coincide, cada URL único tem a sua própria chave cache. 
Excluir | Quando esta opção é selecionada e a regra coincide, as cadeias de consulta especificadas nos parâmetros são excluídas quando a tecla de cache é gerada.
Ignorar cadeias de consulta | Quando esta opção é selecionada e a regra coincide, as cadeias de consulta não são consideradas quando a tecla de cache é gerada. 

### <a name="modify-request-header"></a>Modificar o cabeçalho do pedido

Utilize esta ação para modificar os cabeçalhos que estão presentes nos pedidos enviados para a sua origem.

#### <a name="required-fields"></a>Campos necessários

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome cabeçalho** é adicionado ao pedido com o valor especificado. Se o cabeçalho já estiver presente, o valor é anexado ao valor existente. | String
Overwrite | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome cabeçalho** é adicionado ao pedido com o valor especificado. Se o cabeçalho já estiver presente, o valor especificado substitui o valor existente. | String
Eliminar | Quando esta opção é selecionada, a regra corresponde e o cabeçalho especificado na regra está presente, o cabeçalho é eliminado do pedido. | String

### <a name="modify-response-header"></a>Modificar o cabeçalho de resposta

Utilize esta ação para modificar cabeçalhos que estão presentes nas respostas devolvidas aos seus clientes.

#### <a name="required-fields"></a>Campos necessários

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome cabeçalho** é adicionado à resposta utilizando o **Valor**especificado . Se o cabeçalho já estiver presente, **o valor** é anexado ao valor existente. | String
Overwrite | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome cabeçalho** é adicionado à resposta utilizando o **Valor**especificado . Se o cabeçalho já estiver presente, **o valor** substitui o valor existente. | String
Eliminar | Quando esta opção é selecionada, a regra corresponde e o cabeçalho especificado na regra está presente, o cabeçalho é eliminado da resposta. | String

### <a name="url-redirect"></a>Redirecionamento de URL

Use esta ação para redirecionar os clientes para um novo URL. 

#### <a name="required-fields"></a>Campos necessários

Campo | Descrição 
------|------------
Tipo | Selecione o tipo de resposta para voltar ao solicitador: Encontrado (302), Movido (301), Redirecionamento Temporário (307) e Redirecionamento Permanente (308).
Protocolo | PEDIDO DE CORRESPONDÊNCIA, HTTP, HTTPS.
Nome de anfitrião | Selecione o nome de anfitrião para o quais pretende que o pedido seja redirecionado. Deixe em branco para preservar o hospedeiro que está a chegar.
Caminho | Defina o caminho a utilizar no redirecionamento. Deixe em branco para preservar o caminho de entrada.  
Cadeias de consulta | Defina a cadeia de consulta utilizada no redirecionamento. Deixe em branco para preservar a cadeia de consulta de entrada. 
Fragment | Defina o fragmento para utilizar no redirecionamento. Deixe em branco para preservar o fragmento de entrada. 

Recomendamos vivamente que utilize uma URL absoluta. A utilização de um URL relativo pode redirecionar ursas Azure CDN para um caminho inválido. 

### <a name="url-rewrite"></a>Reescrever URL

Use esta ação para reescrever o caminho de um pedido que está a caminho da sua origem.

#### <a name="required-fields"></a>Campos necessários

Campo | Descrição 
------|------------
Padrão de origem | Defina o padrão de origem no caminho URL para substituir. Atualmente, o padrão de origem usa uma correspondência baseada em prefixos. Para combinar todos os caminhos de URL, utilize um corte para a frente **/** () como o valor do padrão de origem.
Destino | Defina o caminho de destino a utilizar na reescrita. O caminho de destino substitui o padrão de origem.
Preservar caminho inigualável | Se definido para **Sim,** o caminho restante após o padrão de origem é anexado à nova rota de destino. 

## <a name="next-steps"></a>Próximos passos

- [Visão geral do Azure CDN](cdn-overview.md)
- [Referência do Motor de regras standard](cdn-standard-rules-engine-reference.md)
- [Condições de jogo no motor de regras standard](cdn-standard-rules-engine-match-conditions.md)
- [Impor HTTPS com o Motor de regras standard](cdn-standard-rules-engine.md)
