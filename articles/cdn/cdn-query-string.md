---
title: Controlar o comportamento de cache da CDN do Azure com cadeias de consulta-camada Standard
description: O cache da cadeia de consulta da CDN do Azure controla como os arquivos são armazenados em cache quando uma solicitação da Web contém uma cadeia de caracteres de consulta. Este artigo descreve o cache de cadeia de caracteres de consulta em produtos padrão da CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 6471241527dd9b594eaaca20ebc75cacb27f8f72
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083032"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Controlar o comportamento de cache da CDN do Azure com cadeias de consulta-camada Standard
> [!div class="op_single_selector"]
> * [Escalão Standard](cdn-query-string.md)
> * [Escalão Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Descrição geral
Com a CDN (rede de distribuição de conteúdo) do Azure, você pode controlar como os arquivos são armazenados em cache para uma solicitação da Web que contém uma cadeia de caracteres de consulta. Em uma solicitação da Web com uma cadeia de caracteres de consulta, a cadeia de caracteres de consulta é aquela parte da solicitação que ocorre após um ponto de interrogação (?). Uma cadeia de caracteres de consulta pode conter um ou mais pares chave-valor, nos quais o nome do campo e seu valor são separados por um sinal de igual (=). Cada par chave-valor é separado por um e comercial (&). Por exemplo, http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Se houver mais de um par chave-valor em uma cadeia de caracteres de consulta de uma solicitação, sua ordem não importa. 

> [!IMPORTANT]
> Os produtos Standard e Premium da CDN do Azure fornecem a mesma funcionalidade de cache de cadeia de caracteres de consulta, mas a interface do usuário é diferente. Este artigo descreve a interface do **Azure CDN Standard da Microsoft**, a **CDN standard do Azure da AKAMAI** e a **CDN standard do Azure da Verizon**. Para cache de cadeia de caracteres de consulta com a **CDN Premium do Azure da Verizon**, consulte [controlar o comportamento de cache da CDN do Azure com cadeias de consulta-camada Premium](cdn-query-string-premium.md).

Três modos de cadeia de caracteres de consulta estão disponíveis:

- **Ignorar cadeias de caracteres de consulta**: modo padrão. Nesse modo, o nó do ponto de presença (POP) da CDN passa as cadeias de caracteres de consulta do solicitante para o servidor de origem na primeira solicitação e armazena o ativo em cache. Todas as solicitações subsequentes para o ativo que são servidas do POP ignoram as cadeias de caracteres de consulta até que o ativo em cache expire.

- **Ignorar cache para cadeias de caracteres de consulta**: nesse modo, as solicitações com cadeias de caracteres de consulta não são armazenadas em cache no nó pop do CDN. O nó POP recupera o ativo diretamente do servidor de origem e o passa para o solicitante com cada solicitação.

- **Armazenar em cache cada URL exclusiva**: nesse modo, cada solicitação com uma URL exclusiva, incluindo a cadeia de caracteres de consulta, é tratada como um ativo exclusivo com seu próprio cache. Por exemplo, a resposta do servidor de origem para uma solicitação, por exemplo,. ashx? q = Test1 é armazenada em cache no nó POP e retornada para caches subsequentes com a mesma cadeia de caracteres de consulta. Uma solicitação, por exemplo,. ashx? q = test2, é armazenada em cache como um ativo separado com sua própria configuração de vida útil.
   
    >[!IMPORTANT] 
    > Não use esse modo quando a cadeia de caracteres de consulta contiver parâmetros que serão alterados com cada solicitação, como uma ID de sessão ou um nome de usuário, pois isso resultará em uma taxa de acertos de cache baixo.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Alterando configurações de cache de cadeia de caracteres de consulta para perfis CDN padrão
1. Abra um perfil CDN e selecione o ponto de extremidade da CDN que você deseja gerenciar.
   
   ![Pontos de extremidade de perfil CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. No painel esquerdo, em configurações, clique em **regras de cache**.
   
    ![Botão Regras de colocação em cache da CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Na lista **comportamento de cache da cadeia de caracteres de consulta** , selecione um modo de cadeia de consulta e, em seguida, clique em **salvar**.
   
   ![Opções de cache de cadeia de consulta CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Como leva tempo para que o registro seja propagado por meio da CDN do Azure, as alterações nas configurações de cadeia de caracteres do cache podem não estar visíveis imediatamente:
> - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
> - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
> - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída em 10 minutos. 



