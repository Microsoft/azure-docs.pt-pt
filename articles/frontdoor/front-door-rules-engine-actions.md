---
title: Porta da frente azure [ Microsoft Docs
description: Este artigo apresenta uma descrição geral do Azure Front Door. Descubra se é a escolha certa para equilibrar o tráfego de utilizadores para a sua aplicação.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 3e7c9606a17736ea45b09a4d6981b4d55fa6dee6
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515560"
---
# <a name="azure-front-door-rules-engine-actions"></a>Ações de motor da porta da frente azure

No [Motor de Regras Da AFD,](front-door-rules-engine.md) uma regra consiste em zero ou mais condições e ações de correspondência. Este artigo fornece descrições detalhadas das ações que pode utilizar no Motor de Regras Da AFD.

Uma ação define o comportamento que é aplicado ao tipo de pedido que uma condição de correspondência ou conjunto de condições de correspondência identifica. No Motor de Regras Da AFD, uma regra pode conter até cinco ações, das quais apenas uma pode ser uma ação de substituição de configuração de rota (para a frente ou redirecionamento). 

As seguintes ações estão disponíveis para utilização no motor de regras da Porta Frontal Azure.  

## <a name="modify-request-header"></a>Modificar cabeçalho de pedido

Utilize esta ação para modificar os cabeçalhos presentes nos pedidos enviados para a sua origem.

### <a name="required-fields"></a>Campos necessários

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome header** é adicionado ao pedido com o valor especificado. Se o cabeçalho já estiver presente, o valor está anexado ao valor existente. | String
Overwrite | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome header** é adicionado ao pedido com o valor especificado. Se o cabeçalho já estiver presente, o valor especificado substitui o valor existente. | String
Eliminar | Quando esta opção é selecionada, a regra corresponde e o cabeçalho especificado na regra está presente, o cabeçalho é apagado do pedido. | String

## <a name="modify-response-header"></a>Modificar cabeçalho de resposta

Utilize esta ação para modificar os cabeçalhos presentes nas respostas devolvidas aos seus clientes.

### <a name="required-fields"></a>Campos necessários

Ação | Http Nome cabeçalho | Valor
-------|------------------|------
Acrescentar | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome header** é adicionado à resposta utilizando o **valor**especificado . Se o cabeçalho já estiver presente, o **Valor** está anexado ao valor existente. | String
Overwrite | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome header** é adicionado à resposta utilizando o **valor**especificado . Se o cabeçalho já estiver presente, o **Valor** substitui o valor existente. | String
Eliminar | Quando esta opção é selecionada, a regra corresponde e o cabeçalho especificado na regra está presente, o cabeçalho é eliminado da resposta. | String

## <a name="route-configuration-overrides"></a>Sobreposições de configuração de rotas 

### <a name="route-type-redirect"></a>Tipo de rota: Redirecionamento

Use esta ação para redirecionar os clientes para um novo URL. 

#### <a name="required-fields"></a>Campos necessários

Campo | Descrição 
------|------------
Tipo de redirecionamento | Selecione o tipo de resposta para devolver ao solicitador: Found (302), Moved (301), Redirectição Temporária (307) e Redirectivo Permanente (308).
Protocolo de redirecionamento | Pedido de Jogo, HTTP, HTTPS.
Anfitrião de destino | Selecione o nome de anfitrião para o quais pretende que o pedido seja redirecionado. Deixe em branco para preservar o hospedeiro que está a chegar.
Caminho de destino | Defina o caminho a utilizar no redirecionamento. Deixe em branco preservar o caminho de entrada.  
Cadeias de consulta | Defina a corda de consulta utilizada no redirecionamento. Deixe em branco para preservar a corda de consulta de entrada. 
Fragmento de destino | Defina o fragmento a utilizar no redirecionamento. Deixe em branco preservar o fragmento de entrada. 


### <a name="route-type-forward"></a>Tipo de rota: Para a frente

Use esta ação para encaminhar os clientes para um novo URL. Esta ação também contém subações para reescritas de URL e Caching. 

Campo | Descrição 
------|------------
Conjunto de back-end | Selecione a piscina de backend para anular e servir os pedidos de. Isto mostrará todas as suas piscinas de Backend reconfiguradas atualmente no seu perfil da Porta da Frente. 
Protocolo de encaminhamento | Pedido de Jogo, HTTP, HTTPS.
Reescrever URL | Use esta ação para reescrever o caminho de um pedido que está a caminho da sua origem. Se ativado, consulte abaixo os campos adicionais necessários
Colocação em cache | Habilitado, deficiente. Veja abaixo os campos adicionais necessários se estiver ativado. 

#### <a name="url-rewrite"></a>Reescrever URL

Utilize esta definição para configurar um caminho de **encaminhamento personalizado** opcional para utilizar ao construir o pedido para encaminhar para o backend.

Campo | Descrição 
------|------------
Caminho de encaminhamento personalizado | Defina o caminho para encaminhar os pedidos para. 

#### <a name="caching"></a>Colocação em cache

Utilize estas definições para controlar como os ficheiros são cached para pedidos que contenham cordas de consulta e se para cache o seu conteúdo com base em todos os parâmetros ou em parâmetros selecionados. Pode utilizar configurações adicionais para substituir o valor do tempo de vida (TTL) para controlar quanto tempo o conteúdo permanece em cache para pedidos que as regras correspondem às condições especificadas. Para forçar o cache como uma ação, coloque o campo de cache para "Enabled". Quando o fizer, surgem as seguintes opções: 

Comportamento de cache |  Descrição              
---------------|----------------
Ignorar cadeias de consulta | Uma vez que o ativo é cache, todos os pedidos subsequentes ignoram as cordas de consulta até que o ativo em cache expire.
Colocar em cache todos os URL exclusivos | Cada pedido com um URL único, incluindo a cadeia de consulta, é tratado como um ativo único com a sua própria cache.
Ignore as cordas de consulta especificadas | Solicitar cadeias de consulta de URL listadas na definição de "Parâmetros de consulta" são ignoradas para o cache.
Incluir cordas de consulta especificadas | Solicitar cadeias de consulta de URL listadas na definição de "Parâmetros de consulta" são usadas para o cache.

Campos adicionais |  Descrição 
------------------|---------------
Compressão dinâmica | A Porta frontal pode comprimir dinamicamente o conteúdo na borda, resultando numa resposta menor e mais rápida.
Parâmetros de consulta | Uma lista separada de parâmetros permitidos (ou não permitidos) de utilização como base para o cache.
Duração da cache | Duração da expiração da cache em Dias, Horas, Minutos, Segundos. Todos os valores devem ser Int. 

## <a name="next-steps"></a>Passos seguintes

- Aprenda a configurar a sua primeira [configuração do Motor de Regras](front-door-tutorial-rules-engine.md). 
- Saiba mais sobre [as condições](front-door-rules-engine-match-conditions.md) de jogo do motor de regras
- Saiba mais sobre o motor de [regras da porta da frente azure](front-door-rules-engine.md)
