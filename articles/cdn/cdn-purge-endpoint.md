---
title: Purgue um ponto final Azure CDN | Microsoft Docs
description: Saiba como limpar todos os conteúdos em cache a partir de um ponto final da Rede de Entrega de Conteúdos Azure. Os nós de bordas cache ativos até que o seu tempo de vida expire.
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
ms.topic: how-to
ms.date: 05/17/2019
ms.author: allensu
ms.openlocfilehash: 4fe4b99f9635ff254f1a75e03f13d7e6ffcb3c49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100366522"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Purgue um ponto final Azure CDN
## <a name="overview"></a>Descrição geral
Os nós de borda Azure CDN irão cache ativos até que o tempo de vida do ativo (TTL) expire.  Após o TTL do ativo expirar, quando um cliente solicita o ativo a partir do nó de borda, o nó de borda irá recuperar uma nova cópia atualizada do ativo para servir o pedido do cliente e armazenar a atualização da cache.

A melhor prática para garantir que os seus utilizadores obtenham sempre a cópia mais recente dos seus ativos é ver versão dos seus ativos para cada atualização e publicá-los como novos URLs.  A CDN irá imediatamente recuperar os novos ativos para os próximos pedidos de clientes.  Por vezes, pode querer expurgar o conteúdo em cache de todos os nós de borda e forçá-los a todos a recuperar novos ativos atualizados.  Isto pode ser devido a atualizações para a sua aplicação web, ou para atualizar rapidamente ativos que contenham informações incorretas.

> [!TIP]
> Note que a purga apenas limpa o conteúdo em cache nos servidores de borda CDN.  Quaisquer caches a jusante, tais como servidores proxy e caches de navegador locais, podem ainda conter uma cópia em cache do ficheiro.  É importante lembrar-se disto quando se define o tempo de vida de um ficheiro.  Pode forçar um cliente a jusante a solicitar a versão mais recente do seu ficheiro, dando-lhe um nome único sempre que o atualize, ou [aproveitando o caching](cdn-query-string.md)de cadeias de consulta .  
> 
> 

Este tutorial leva-o a purgar ativos de todos os nós de um ponto final.

## <a name="walkthrough"></a>Instruções
1. No [Portal Azure,](https://portal.azure.com)navegue no perfil CDN contendo o ponto final que pretende purgar.
2. A partir da lâmina de perfil CDN, clique no botão de purga.
   
    ![Lâmina de perfil CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    A lâmina de purga abre-se.
   
    ![Lâmina de purga CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. Na lâmina purgar, selecione o endereço de serviço que deseja limpar do dropdown URL.
   
    ![Forma de purga](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Também pode chegar à lâmina purgar clicando no botão **purgar** na lâmina de ponta final CDN.  Nesse caso, o campo **URL** será pré-povoado com o endereço de serviço desse ponto final específico.
   > 
   > 
4. Selecione quais os ativos que deseja purgar dos nós de borda.  Se desejar limpar todos os ativos, clique na **caixa de verificação Purgar.**  Caso contrário, digite o caminho de cada ativo que pretende purgar na caixa de texto **Path.** Os formatos abaixo são suportados no caminho.
    1. **Purga de URL única**: Purgue o ativo individual especificando o URL completo, com ou sem a extensão do ficheiro, por exemplo, `/pictures/strasbourg.png` ; `/pictures/strasbourg`
    2. **A purga wildcard**: Asterisco \* pode ser usado como um wildcard. Purgue todas as pastas, sub-pastas e ficheiros sob um ponto final com `/*` ou purgue todas as sub-pastas e ficheiros sob uma pasta específica especificando a pasta seguida por , por `/*` exemplo, `/pictures/*` .  Note que a purga wildcard não é suportada pela Azure CDN da Akamai atualmente. 
    3. **Purga do domínio raiz**: Purgue a raiz do ponto final com "/" no caminho.
   
   > [!TIP]
   > Os caminhos devem ser especificados para purga e devem ser um URL relativo que se enquadre na seguinte [expressão regular](/dotnet/standard/base-types/regular-expression-language-quick-reference). **Purgue tudo** e **a purga wildcard** não suportada pela **Azure CDN da Akamai** atualmente.
   > > Purga de URL única `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Cadeia de consulta `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Purga wildcard `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";` . 
   > 
   > Mais caixas de texto **path** aparecerão depois de introduzir texto para permitir a construção de uma lista de múltiplos ativos.  Pode eliminar os ativos da lista clicando no botão elipse (...) .
   > 
5. Clique no botão **Purga.**
   
    ![Botão de purga](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Os pedidos de purga demoram aproximadamente 10 minutos a processar com **a Azure CDN da Microsoft,** aproximadamente 2 minutos com **a Azure CDN da Verizon** (standard e premium), e aproximadamente 10 segundos com a **Azure CDN da Akamai.**  A Azure CDN tem um limite de 100 pedidos de purga simultânea em qualquer momento ao nível do perfil. 
> 
> 

## <a name="see-also"></a>Ver também
* [Pré-carregar recursos num ponto final da CDN do Azure](cdn-preload-endpoint.md)
* [Referência Azure CDN REST API - Purgar ou Pré-Carregar um ponto final](/rest/api/cdn/cdn/endpoints)

