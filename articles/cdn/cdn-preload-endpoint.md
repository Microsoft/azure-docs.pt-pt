---
title: Pré-carregar recursos num ponto final da CDN do Azure | Documentos da Microsoft
description: Aprenda a pré-carregar conteúdo em cache num ponto final da CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: magattus
ms.openlocfilehash: d91507ad2cb271b23b588ef7da88e6e6712915b1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593585"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Pré-carregar recursos num ponto final da CDN do Azure
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Por predefinição, ativos são colocadas em cache apenas quando são solicitadas. Uma vez que os servidores de borda tem não ainda armazenado em cache o conteúdo e tem de reencaminhar o pedido para o servidor de origem, a primeira solicitação de cada região pode demorar mais do que as solicitações subseqüentes. Para evitar esta latência da primeira visita, carregue previamente os recursos. Além de fornecer uma melhor experiência de cliente, a pré-carregar os seus ativos em cache pode reduzir o tráfego de rede no servidor de origem.

> [!NOTE]
> Pré-carregar ativos é útil para eventos tão grandes ou conteúdo que torna-se ao mesmo tempo disponível para vários usuários, como uma nova versão de filme ou uma atualização de software.
> 
> 

Este tutorial orienta-o através da pré-carregar o conteúdo em cache em todos os nós de extremidade da CDN do Azure.

## <a name="to-pre-load-assets"></a>A pré-carregar ativos
1. Na [portal do Azure](https://portal.azure.com), navegue até ao perfil CDN que contém o ponto final a pré-carregar. É aberto o painel do perfil.
    
2. Clique no ponto de final na lista. É aberto o painel de ponto final.
3. No painel de ponto final da CDN, selecione **carga**.
   
    ![Painel de ponto final CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    O **carga** painel abre-se.
   
    ![Painel de carga CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Para **caminho de conteúdo**, introduza o caminho completo de cada ativo que quer carregar (por exemplo, `/pictures/kitten.png`).
   
   > [!TIP]
   > Depois de começar a inserir textos, mais **caminho de conteúdo** caixas de texto serão apresentado para permitir a criação de uma lista de vários recursos. Para eliminar ativos na lista, selecione o botão de reticências (...), em seguida, selecione **eliminar**.
   > 
   > Cada caminho de conteúdo tem de ser um URL relativo que se adeque às seguintes [expressões regulares](/dotnet/standard/base-types/regular-expression-language-quick-reference):  
   > - Carregar um único caminho de arquivo: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Carrega um único ficheiro com a cadeia de consulta: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Uma vez que cada recurso tem de ter seu próprio caminho, não há nenhuma funcionalidade de caráter universal para pré-carregar ativos.
   > 
   > 
   
    ![Botão de carga](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Quando tiver terminado de introduzir os caminhos de conteúdo, selecione **carga**.
   

> [!NOTE]
> Existe um limite de 10 pedidos de carga por minuto para cada perfil de CDN e 50 caminhos simultâneos podem ser processados em simultâneo. Cada caminho tem um limite de comprimento de caminho de 1024 carateres.
> 
> 

## <a name="see-also"></a>Consulte também
* [Remover um ponto de final de CDN do Azure](cdn-purge-endpoint.md)
* [Referência da API de REST do CDN do Azure: Pré-carregar o conteúdo num ponto final](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Referência da API de REST do CDN do Azure: Limpar o conteúdo a partir de um ponto final](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

