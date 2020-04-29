---
title: Purgue um ponto final do CDN Azure [ Microsoft Docs
description: Aprenda a expurgar todos os conteúdos em cache de um ponto final do Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: allensu
ms.openlocfilehash: ebbb0dd059ce2bcf4a3bc260ed6d426d5be09dfe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81260263"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Purgue um ponto final de CDN Azure
## <a name="overview"></a>Descrição geral
Os nós de borda Azure CDN irão cache ativos até que o tempo de vida do ativo (TTL) expire.  Após a expiração do TTL do ativo, quando um cliente solicita o ativo a partir do nó de borda, o nó de borda recuperará uma nova cópia atualizada do ativo para servir o pedido do cliente e armazenar refrescar o cache.

A melhor prática para garantir que os seus utilizadores obtenham sempre a cópia mais recente dos seus ativos é versonizar os seus ativos para cada atualização e publicá-los como novos URLs.  A CDN recuperará imediatamente os novos ativos para os próximos pedidos de clientes.  Por vezes, pode querer expurgar o conteúdo em cache de todos os nós de borda e forçá-los a todos a recuperar novos ativos atualizados.  Isto pode ser devido a atualizações à sua aplicação web, ou a atualizar rapidamente os ativos que contêm informações incorretas.

> [!TIP]
> Note que a purga apenas limpa o conteúdo em cache nos servidores de borda CDN.  Quaisquer caches a jusante, tais como servidores proxy e caches de navegador locais, ainda podem conter uma cópia em cache do ficheiro.  É importante lembrar-se disto quando se define o tempo de viver de um ficheiro.  Pode forçar um cliente a jusante a solicitar a versão mais recente do seu ficheiro, dando-lhe um nome único sempre que o atualiza, ou aproveitando a consulta de [cordas](cdn-query-string.md).  
> 
> 

Este tutorial acompanha-o através da purga de ativos de todos os nós de borda de um ponto final.

## <a name="walkthrough"></a>Instruções
1. No [Portal Azure,](https://portal.azure.com)navegue para o perfil CDN contendo o ponto final que pretende purgar.
2. A partir da lâmina de perfil CDN, clique no botão de purga.
   
    ![Lâmina de perfil CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    A lâmina purga abre- se.
   
    ![Lâmina de purga CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. Na lâmina Purga, selecione o endereço de serviço que pretende expurgar da queda de URL.
   
    ![Forma de purga](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Também pode chegar à lâmina purga, clicando no botão **Purga** na lâmina de ponta CDN.  Nesse caso, o campo **URL** será pré-povoado com o endereço de serviço desse ponto final específico.
   > 
   > 
4. Selecione quais os ativos que pretende expurgar dos nódosos de borda.  Se desejar limpar todos os ativos, clique em **purgar todas as** caixas de verificação.  Caso contrário, digite o caminho de cada ativo que pretende expurgar na caixa de texto **Path.** Os formatos abaixo são suportados no caminho.
    1. **Purga única do URL**: Expurgar o ativo individual especificando o URL`/pictures/strasbourg.png`completo, com ou sem a extensão do ficheiro, por exemplo, ;`/pictures/strasbourg`
    2. **Purga wildcard**: Asterisco ()\*pode ser usado como um wildcard. Expurgue todas as pastas, subpastas `/*` e ficheiros sob um ponto final com o caminho ou expurgue todas as subpastas e ficheiros sob uma pasta específica, especificando a pasta seguida por `/*`, por exemplo,`/pictures/*`.  Note que a purga wildcard não é suportada pelo Azure CDN da Akamai atualmente. 
    3. **Purga**do domínio raiz : Expurgar a raiz do ponto final com "/" no caminho.
   
   > [!TIP]
   > Os caminhos devem ser especificados para purgar e devem ser um URL relativo que se encaixe na seguinte [expressão regular](/dotnet/standard/base-types/regular-expression-language-quick-reference). **Purgue tudo** e **a purga wildcard** não apoiada pelo **Azure CDN da Akamai** atualmente.
   > > Purga única de URL`@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Corda de consulta`@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Purga `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`wildcard. 
   > 
   > Mais caixas de texto **Path** aparecerão depois de introduzir texto para permitir construir uma lista de vários ativos.  Pode eliminar os ativos da lista clicando no botão elipse (...) e depois.
   > 
5. Clique no botão **Purga.**
   
    ![Botão de purga](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Os pedidos de purga demoram aproximadamente 10 minutos a processar com o **Azure CDN da Microsoft**, aproximadamente 2 minutos com **o Azure CDN da Verizon** (standard e premium), e aproximadamente 10 segundos com o **Azure CDN da Akamai.**  O Azure CDN tem um limite de 50 pedidos simultâneos de purga a qualquer momento ao nível do perfil. 
> 
> 

## <a name="see-also"></a>Consulte também
* [Pré-carregar recursos num ponto final da CDN do Azure](cdn-preload-endpoint.md)
* [Referência API do CDN DE AZURE - Purga ou Pré-Carregar um Ponto Final](/rest/api/cdn/endpoints)

