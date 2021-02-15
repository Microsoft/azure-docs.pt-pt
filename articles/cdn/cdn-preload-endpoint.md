---
title: Pré-carregar os ativos num ponto final da Azure CDN | Microsoft Docs
description: Saiba como pré-carregar o conteúdo em cache num ponto final da Rede de Entrega de Conteúdos Azure. Esta funcionalidade encontra-se disponível em certas versões do produto.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2018
ms.author: allensu
ms.openlocfilehash: 186ded90b504420a2f315d054551d97821cf8465
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385052"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Pré-carregar recursos num ponto final da CDN do Azure
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Por defeito, os ativos só são em cache quando são solicitados. Como os servidores de borda ainda não cached o conteúdo e precisam de encaminhar o pedido para o servidor de origem, o primeiro pedido de cada região pode demorar mais do que os pedidos subsequentes. Para evitar esta primeira latência, pré-carregue os seus bens. Além de proporcionar uma melhor experiência ao cliente, pré-carregar os seus ativos em cache pode reduzir o tráfego de rede no servidor de origem.

> [!NOTE]
> Os ativos pré-carregamentos são úteis para grandes eventos ou conteúdos que ficam simultaneamente disponíveis para muitos utilizadores, como um novo lançamento de filme ou uma atualização de software.
> 
> 

Este tutorial acompanha-o através do pré-carregamento de conteúdo em cache em todos os nós de borda Azure CDN.

## <a name="to-pre-load-assets"></a>Para pré-carregar ativos
1. No [portal Azure,](https://portal.azure.com)navegue no perfil CDN contendo o ponto final que pretende pré-carregar. O painel de perfil abre-se.
    
2. Clique no ponto final da lista. O painel de ponto final abre.
3. A partir do painel de pontos finais cdN, selecione **Load**.
   
    ![Painel de ponto final CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    O **painel de carga** abre-se.
   
    ![Painel de carga CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Para **o caminho do conteúdo,** insira o caminho completo de cada ativo que pretende carregar (por exemplo, `/pictures/kitten.png` ).
   
   > [!TIP]
   > Depois de começar a introduzir texto, mais caixas de texto **de trajetória** de conteúdo aparecerão para que possa construir uma lista de múltiplos ativos. Para eliminar os ativos da lista, selecione o botão elipse (...) e, em seguida, selecione **Delete**.
   > 
   > Cada caminho de conteúdo deve ser um URL relativo que se enquadre nas [seguintes expressões regulares:](/dotnet/standard/base-types/regular-expression-language-quick-reference)  
   > - Carregue um único caminho de arquivo: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Carregue um único ficheiro com cadeia de consulta: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Como cada ativo deve ter o seu próprio caminho, não há nenhuma funcionalidade wildcard para pré-carregamento de ativos.
   > 
   > 
   
    ![Botão de carga](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Quando terminar de entrar nos caminhos de conteúdo, selecione **Load**.
   

> [!NOTE]
> Há um limite de 10 pedidos de carga por minuto por perfil cdn e 50 caminhos simultâneos podem ser processados ao mesmo tempo. Cada caminho tem um limite de comprimento de caminho de 1024 caracteres.
> 
> 

## <a name="see-also"></a>Ver também
* [Purgue um ponto final Azure CDN](cdn-purge-endpoint.md)
* [Referência Azure CDN REST API: Pré-carregamento de conteúdo num ponto final](/rest/api/cdn/cdn/endpoints/loadcontent)
* [Referência Azure CDN REST API: Purgar o conteúdo de um ponto final](/rest/api/cdn/cdn/endpoints/purgecontent)