---
title: Ativos de pré-carga num ponto final do Azure CDN [ Microsoft Docs
description: Aprenda a pré-carregar conteúdo em cache num ponto final do Azure CDN.
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
ms.topic: article
ms.date: 02/12/2018
ms.author: allensu
ms.openlocfilehash: c45d0a9195a719d830753a9614cfa7efb6f1c23d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81260280"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Pré-carregar recursos num ponto final da CDN do Azure
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Por padrão, os ativos só são protegidos quando são solicitados. Como os servidores de borda ainda não cached o conteúdo e precisam de encaminhar o pedido para o servidor de origem, o primeiro pedido de cada região pode demorar mais do que os pedidos subsequentes. Para evitar esta latência de primeiro sucesso, pré-carregue os seus bens. Além de proporcionar uma melhor experiência ao cliente, o pré-carregamento dos seus ativos em cache pode reduzir o tráfego de rede no servidor de origem.

> [!NOTE]
> A pré-carregamento de ativos é útil para grandes eventos ou conteúdos que se tornam simultaneamente disponíveis para muitos utilizadores, como um novo lançamento de filme ou uma atualização de software.
> 
> 

Este tutorial acompanha-o através de conteúdo em cache pré-carregamento em todos os nós de borda Azure CDN.

## <a name="to-pre-load-assets"></a>Para pré-carregar ativos
1. No [portal Azure,](https://portal.azure.com)navegue para o perfil CDN contendo o ponto final que pretende pré-carregar. O painel de perfis abre.
    
2. Clique no ponto final da lista. O painel final abre.
3. A partir do painel de ponto final CDN, selecione **Carregar**.
   
    ![Painel de ponto final CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    O painel **de carga** abre.
   
    ![Painel de carga CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Para o **caminho do Conteúdo,** insira o caminho `/pictures/kitten.png`completo de cada ativo que deseja carregar (por exemplo, ).
   
   > [!TIP]
   > Depois de começar a introduzir texto, mais caixas de texto de **caminho de conteúdo** aparecerão para permitir construir uma lista de vários ativos. Para eliminar os ativos da lista, selecione o botão elipse (...) e, em seguida, selecione **Eliminar**.
   > 
   > Cada caminho de conteúdo deve ser um URL relativo que se adapte às [seguintes expressões regulares:](/dotnet/standard/base-types/regular-expression-language-quick-reference)  
   > - Carregue um único caminho de ficheiro:`^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Carregue um único ficheiro com corda de consulta:`^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Como cada ativo deve ter o seu próprio caminho, não há nenhuma funcionalidade wildcard para os ativos pré-carregamento.
   > 
   > 
   
    ![Botão de carga](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Quando terminar de introduzir os caminhos de conteúdo, selecione **Load**.
   

> [!NOTE]
> Há um limite de 10 pedidos de carga por minuto por perfil CDN e 50 caminhos simultâneos podem ser processados de uma só vez. Cada caminho tem um limite de comprimento de 1024 caracteres.
> 
> 

## <a name="see-also"></a>Consulte também
* [Purgue um ponto final de CDN Azure](cdn-purge-endpoint.md)
* [Referência Azure CDN REST API: Conteúdo pré-carregado num ponto final](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Referência Azure CDN REST API: Expurgar o conteúdo de um ponto final](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

