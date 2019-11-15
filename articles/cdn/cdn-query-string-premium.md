---
title: Controlar o comportamento de cache da CDN do Azure com cadeias de consulta-camada Premium
description: O cache da cadeia de consulta da CDN do Azure controla como os arquivos são armazenados em cache quando uma solicitação da Web contém uma cadeia de caracteres de consulta. Este artigo descreve o cache de cadeia de caracteres de consulta no produto Azure CDN Premium da Verizon.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 365c52840d281c0f48d17aacc358e4cce513e3b4
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083082"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Controlar o comportamento de cache da CDN do Azure com cadeias de consulta-camada Premium
> [!div class="op_single_selector"]
> * [Escalão Standard](cdn-query-string.md)
> * [Escalão Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Descrição geral
Com a CDN (rede de distribuição de conteúdo) do Azure, você pode controlar como os arquivos são armazenados em cache para uma solicitação da Web que contém uma cadeia de caracteres de consulta. Em uma solicitação da Web com uma cadeia de caracteres de consulta, a cadeia de caracteres de consulta é aquela parte da solicitação que ocorre após um ponto de interrogação (?). Uma cadeia de caracteres de consulta pode conter um ou mais pares chave-valor, nos quais o nome do campo e seu valor são separados por um sinal de igual (=). Cada par chave-valor é separado por um e comercial (&). Por exemplo, http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Se houver mais de um par chave-valor em uma cadeia de caracteres de consulta de uma solicitação, sua ordem não importa. 

> [!IMPORTANT]
> Os produtos CDN Standard e Premium fornecem a mesma funcionalidade de cache de cadeia de caracteres de consulta, mas a interface do usuário é diferente. Este artigo descreve a interface do **Azure CDN Premium da Verizon**. Para cache de cadeia de caracteres de consulta com produtos padrão da CDN do Azure, consulte [controlar o comportamento de cache da CDN do Azure com cadeias de consulta-camada Standard](cdn-query-string.md)
>


Três modos de cadeia de caracteres de consulta estão disponíveis:

- **Standard-cache**: modo padrão. Nesse modo, o nó do ponto de presença (POP) da CDN passa as cadeias de caracteres de consulta do solicitante para o servidor de origem na primeira solicitação e armazena o ativo em cache. Todas as solicitações subsequentes para o ativo que são servidas do servidor POP ignoram as cadeias de caracteres de consulta até que o ativo em cache expire.

    >[!IMPORTANT] 
    > Se a autorização de token estiver habilitada para qualquer caminho nessa conta, o modo de cache padrão será o único modo que pode ser usado. 

- no **-cache**: nesse modo, as solicitações com cadeias de caracteres de consulta não são armazenadas em cache no nó pop do CDN. O nó POP recupera o ativo diretamente do servidor de origem e o passa para o solicitante com cada solicitação.

- **cache exclusivo**: nesse modo, cada solicitação com uma URL exclusiva, incluindo a cadeia de caracteres de consulta, é tratada como um ativo exclusivo com seu próprio cache. Por exemplo, a resposta do servidor de origem para uma solicitação, por exemplo,. ashx? q = Test1 é armazenada em cache no nó POP e retornada para caches subsequentes com a mesma cadeia de caracteres de consulta. Uma solicitação, por exemplo,. ashx? q = test2, é armazenada em cache como um ativo separado com sua própria configuração de vida útil.
   
    >[!IMPORTANT] 
    > Não use esse modo quando a cadeia de caracteres de consulta contiver parâmetros que serão alterados com cada solicitação, como uma ID de sessão ou um nome de usuário, pois isso resultará em uma taxa de acertos de cache baixo.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Alterando configurações de cache de cadeia de caracteres de consulta para perfis de CDN Premium
1. Abra um perfil CDN e clique em **gerenciar**.
   
    ![Botão Gerenciar perfil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.
2. Passe o mouse sobre a guia **http grande** e passe o mouse sobre o menu de atalho **configurações de cache** . Clique em **cache de cadeia de consulta**.
   
    As opções de cache de cadeia de caracteres de consulta são exibidas.
   
    ![Opções de cache de cadeia de consulta CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Selecione um modo de cadeia de caracteres de consulta e clique em **Atualizar**.

> [!IMPORTANT]
> Como leva tempo para o registro se propagar por meio da CDN, as alterações nas configurações da cadeia de caracteres do cache podem não estar visíveis imediatamente. A propagação geralmente é concluída em 10 minutos.
 

