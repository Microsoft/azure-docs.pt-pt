---
title: CDN do Azure das ações do mecanismo de regras padrão da Microsoft | Microsoft Docs
description: Documentação de referência para a CDN do Azure das ações do mecanismo de regras padrão da Microsoft.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: dbde93cc7ffd21e341653407e6e4f910e4620974
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615991"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-actions"></a>Ações do mecanismo da CDN do Azure da Microsoft Standard Rules

Este artigo lista descrições detalhadas das ações disponíveis para a CDN (rede de distribuição de conteúdo) do Azure do [mecanismo de regras padrão](cdn-standard-rules-engine.md)da Microsoft.

A segunda parte de uma regra é uma ação. Uma ação define o comportamento que é aplicado ao tipo de solicitação que é identificado por um conjunto de condições de correspondência.

## <a name="actions"></a>Ações

As ações a seguir estão disponíveis para uso. 

## <a name="cache-expiration"></a>Expiração do cache

Essa ação permite que você substitua o TTL do ponto de extremidade para solicitações especificadas pelas condições de correspondência das regras.

**Campos obrigatórios**

Comportamento do cache |                
---------------|----------------
Ignorar cache | Quando essa opção for selecionada e a regra corresponder, o conteúdo não será armazenado em cache.
Substituição | Quando essa opção é selecionada e a regra corresponde, o valor de TTL retornado da origem será substituído pelo valor especificado na ação.
Definir se ausente | Quando essa opção for selecionada e a regra corresponder, se não houver nenhum valor TTL retornado da origem, a regra definirá o TTL como o valor especificado na ação.

**Campos adicionais**

Dias | Horas | Minutos | Segundos
-----|-------|---------|--------
inteiro | inteiro | inteiro | inteiro 

## <a name="cache-key-query-string"></a>Cadeia de consulta de chave de cache

Essa ação permite que você modifique a chave de cache com base em cadeias de caracteres de consulta.

**Campos obrigatórios**

Comportamento | Descrição
---------|------------
incluir | Quando essa opção for selecionada e a regra corresponder, as cadeias de caracteres de consulta especificadas nos parâmetros serão incluídas ao gerar a chave de cache. 
Colocar em cache todos os URL exclusivos | Quando essa opção for selecionada e a regra corresponder, cada URL exclusiva terá sua própria chave de cache. 
Excluí | Quando essa opção for selecionada e a regra corresponder, as cadeias de caracteres de consulta especificadas nos parâmetros serão excluídas ao gerar a chave de cache.
Ignorar cadeias de consulta | Quando essa opção é selecionada e a regra corresponde, as cadeias de caracteres de consulta não serão consideradas ao gerar a chave de cache. 

## <a name="modify-request-header"></a>Modificar cabeçalho de solicitação

Essa ação permite que você modifique os cabeçalhos presentes nas solicitações enviadas à sua origem.

**Campos obrigatórios**

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando essa opção for selecionada e a regra corresponder, o cabeçalho especificado no nome do cabeçalho será adicionado à solicitação com o valor especificado. Se o cabeçalho já estiver presente, o valor será anexado ao valor existente. | String
Gravado | Quando essa opção for selecionada e a regra corresponder, o cabeçalho especificado no nome do cabeçalho será adicionado à solicitação com o valor especificado. Se o cabeçalho já estiver presente, o valor substituirá o valor existente. | String
Eliminar | Quando essa opção for selecionada e a regra corresponder, e o cabeçalho especificado na regra estiver presente, ele será excluído da solicitação. | String

## <a name="modify-response-header"></a>Modificar cabeçalho de resposta

Essa ação permite que você modifique os cabeçalhos presentes nas respostas retornadas aos clientes finais

**Campos obrigatórios**

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando essa opção for selecionada e a regra corresponder, o cabeçalho especificado no nome do cabeçalho será adicionado à resposta com o valor especificado. Se o cabeçalho já estiver presente, o valor será anexado ao valor existente. | String
Gravado | Quando essa opção for selecionada e a regra corresponder, o cabeçalho especificado no nome do cabeçalho será adicionado à resposta com o valor especificado. Se o cabeçalho já estiver presente, o valor substituirá o valor existente. | String
Eliminar | Quando essa opção for selecionada e a regra corresponder, e o cabeçalho especificado na regra estiver presente, ele será excluído da resposta. | String

## <a name="url-redirect"></a>Redirecionamento de URL

Essa ação permite que você redirecione clientes finais para uma nova URL. 

**Campos obrigatórios**

Campo | Descrição 
------|------------
Tipo | Selecione o tipo de resposta que será retornado ao solicitante. As opções são-302 encontrado, 301 movido, 307 temporário redirecionamento e 308 redirecionamento permanente
Protocolo | Solicitação de correspondência, HTTP ou HTTPS
Nome de anfitrião | Selecione o nome do host para o qual a solicitação será redirecionada. Deixe em branco para preservar o host de entrada.
Caminho | Defina o caminho a ser usado no redirecionamento. Deixe em branco para preservar o caminho de entrada.  
Cadeias de consulta | Defina a cadeia de caracteres de consulta usada no redirecionamento. Deixe em branco para preservar a cadeia de caracteres de consulta de entrada. 
Fragmento | Defina o fragmento a ser usado no redirecionamento. Deixe em branco para preservar o fragmento de entrada. 

É altamente recomendável usar uma URL absoluta. O uso de uma URL relativa pode redirecionar URLs CDN para um caminho inválido. 

## <a name="url-rewrite"></a>Reescrita de URLs

Essa ação permite que você reescreva o caminho de uma solicitação de rota para sua origem.

**Campos obrigatórios**

Campo | Descrição 
------|------------
Padrão de origem | Defina o padrão de origem no caminho da URL a ser substituído. Atualmente, o padrão de origem usa uma correspondência baseada em prefixo. Para corresponder a todos os caminhos de URL, use "/" como o valor de padrão de origem.
Destino | Defina o caminho de destino para ser usado na regravação. Isso substituirá o padrão de origem
Preservar caminho sem correspondência | Em caso afirmativo, o caminho restante após o padrão de origem será anexado ao novo caminho de destino. 


[Voltar ao início](#actions)

</br>

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da rede de distribuição de conteúdo do Azure](cdn-overview.md)
- [Referência do motor de regras](cdn-standard-rules-engine-reference.md)
- [Condições de correspondência do motor de regras](cdn-standard-rules-engine-match-conditions.md)
- [Impor HTTPS usando o mecanismo de regras padrão](cdn-standard-rules-engine.md)
