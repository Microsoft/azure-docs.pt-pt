---
title: Azure Front Door Rules Engine actions
description: Este artigo fornece uma lista das várias ações que pode fazer com o Motor das Regras da Porta Frontal Azure.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 7b5358ec6bf0f41c860501c70ad175d7cf4bfe97
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062850"
---
# <a name="azure-front-door-rules-engine-actions"></a>Ações do Motor de Regras do Azure Front Door

No [MOTOR DE Regras AFD,](front-door-rules-engine.md)uma regra consiste em zero ou mais condições e ações de correspondência. Este artigo fornece descrições detalhadas das ações que pode usar no MOTOR de Regras DA AFD.

Uma ação define o comportamento que é aplicado ao tipo de pedido que uma condição de correspondência ou conjunto de condições de jogo identifica. No MOTOR DE Regras AFD, uma regra pode conter até cinco ações. Apenas uma das quais pode ser uma ação de substituição da configuração da rota (para a frente ou redirecionamento).

As seguintes ações estão disponíveis para usar no motor de regras da Porta Frontal Azure.  

## <a name="modify-request-header"></a>Modificar o cabeçalho do pedido

Utilize esta ação para modificar os cabeçalhos que estão presentes nos pedidos enviados para a sua origem.

### <a name="required-fields"></a>Campos necessários

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome cabeçalho** é adicionado ao pedido com o valor especificado. Se o cabeçalho já estiver presente, o valor é anexado ao valor existente. | String
Overwrite | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome cabeçalho** é adicionado ao pedido com o valor especificado. Se o cabeçalho já estiver presente, o valor especificado substitui o valor existente. | String
Eliminar | Quando esta opção é selecionada com regras de correspondência e o cabeçalho especificado na regra está presente, o cabeçalho é eliminado do pedido. | String

## <a name="modify-response-header"></a>Modificar o cabeçalho de resposta

Utilize esta ação para modificar cabeçalhos que estão presentes nas respostas devolvidas aos seus clientes.

### <a name="required-fields"></a>Campos necessários

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome cabeçalho** é adicionado à resposta utilizando o **Valor** especificado . Se o cabeçalho já estiver presente, **o valor** é anexado ao valor existente. | String
Overwrite | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome cabeçalho** é adicionado à resposta utilizando o **Valor** especificado . Se o cabeçalho já estiver presente, **o valor** substitui o valor existente. | String
Eliminar | Quando esta opção é selecionada e a regra corresponde ao cabeçalho especificado na regra, o cabeçalho é eliminado da resposta. | String

## <a name="route-configuration-overrides"></a>Substituições da configuração de rotas 

### <a name="route-type-redirect"></a>Tipo de rota: Redirecionamento

Use esta ação para redirecionar os clientes para um novo URL. 

#### <a name="required-fields"></a>Campos necessários

Campo | Descrição 
------|------------
Tipo de redirecionamento | Selecione o tipo de resposta para voltar ao solicitador: Encontrado (302), Movido (301), Redirecionamento Temporário (307) e Redirecionamento Permanente (308).
Protocolo de redirecionamento | PEDIDO DE CORRESPONDÊNCIA, HTTP, HTTPS.
Anfitrião do destino | Selecione o nome de anfitrião para o quais pretende que o pedido seja redirecionado. Deixe em branco para preservar o hospedeiro que está a chegar.
Caminho de destino | Defina o caminho a utilizar no redirecionamento. Deixe em branco para preservar o caminho de entrada.  
Cadeias de consulta | Defina a cadeia de consulta utilizada no redirecionamento. Deixe em branco para preservar a cadeia de consulta de entrada. 
Fragmento de destino | Defina o fragmento para utilizar no redirecionamento. Deixe em branco para preservar o fragmento de entrada. 


### <a name="route-type-forward"></a>Tipo de rota: Para a frente

Use esta ação para encaminhar os clientes para um novo URL. Esta ação também contém sub-ações para reescritas de URL e Caching. 

Campo | Descrição 
------|------------
Conjunto de back-end | Selecione a piscina de backend para substituir e servir os pedidos, isto também mostrará todas as suas piscinas de backend pré-configuradas atualmente no seu perfil da porta da frente. 
Protocolo de encaminhamento | PEDIDO DE CORRESPONDÊNCIA, HTTP, HTTPS.
Reescrever URL | Use esta ação para reescrever o caminho de um pedido que está a caminho da sua origem. Se ativado, consulte os seguintes campos adicionais necessários
Colocação em cache | Ativado, incapacitado. Consulte os seguintes campos adicionais necessários se ativado. 

#### <a name="url-rewrite"></a>Reescrever URL

Utilize esta definição para configurar um Caminho de **Encaminhamento Personalizado** opcional para utilizar ao construir o pedido de encaminhamento para o backend.

Campo | Descrição 
------|------------
Caminho de encaminhamento personalizado | Defina o caminho para encaminhar os pedidos para. 

#### <a name="caching"></a>Colocação em cache

Utilize estas definições para controlar como os ficheiros ficam em cache para pedidos que contenham cadeias de consulta. Se cache o seu conteúdo com base em todos os parâmetros ou em parâmetros selecionados. Pode utilizar definições adicionais para substituir o valor do tempo de vida (TTL) para controlar quanto tempo o conteúdo permanece em cache. Para forçar o caching como uma ação, coloque o campo de caching em "Habilitado". Quando se força a caching, aparecem as seguintes opções: 

Comportamento da cache |  Description              
---------------|----------------
Ignorar cadeias de consulta | Uma vez que o ativo é em cache, todos os pedidos subsequentes ignoram as cordas de consulta até que o ativo em cache expire.
Colocar em cache todos os URLs exclusivos | Cada pedido com um URL único, incluindo a cadeia de consulta, é tratado como um ativo único com a sua própria cache.
Ignore as cadeias de consulta especificadas | Solicitam que as cadeias de consulta de URL listadas na definição "Parâmetros de consulta" sejam ignoradas para caching.
Incluir cadeias de consulta especificadas | Solicitam cadeias de consulta de URL listadas na definição "Parâmetros de consulta" para o caching.

Campos adicionais |  Description 
------------------|---------------
Compressão dinâmica | A Porta frontal pode comprimir dinamicamente o conteúdo na borda, resultando numa resposta menor e mais rápida.
Parâmetros de consulta | Uma lista separada por vírgula de parâmetros permitidos (ou não autorizados) para utilizar como base para o caching.
Duração da cache | Duração de validade da cache em Dias, Horas, Minutos, Segundos. Todos os valores devem ser Int. 

## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar o seu primeiro [Motor de Regras.](front-door-tutorial-rules-engine.md) 
- Saiba mais sobre [as condições de jogo do Motor de Regras](front-door-rules-engine-match-conditions.md)
- Saiba mais sobre [o motor das regras da porta da frente Azure](front-door-rules-engine.md)
