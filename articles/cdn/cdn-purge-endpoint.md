---
title: Limpar um ponto de extremidade da CDN do Azure | Microsoft Docs
description: Saiba como limpar todo o conteúdo armazenado em cache de um ponto de extremidade da CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: magattus
ms.openlocfilehash: 1bfbc1b730811e1111a08a957db3a747f90fb587
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546207"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Limpar um ponto de extremidade da CDN do Azure
## <a name="overview"></a>Descrição geral
Os nós de borda da CDN do Azure armazenarão ativos em cache até o tempo de vida (TTL) do ativo expirar.  Depois que a TTL do ativo expira, quando um cliente solicita o ativo do nó de borda, o nó de borda recupera uma nova cópia atualizada do ativo para atender à solicitação do cliente e armazenar a atualização do cache.

A prática recomendada para garantir que os usuários sempre obtenham a cópia mais recente de seus ativos é fazer a versão de seus ativos para cada atualização e publicá-los como novas URLs.  A CDN recuperará imediatamente os novos ativos para as próximas solicitações de cliente.  Às vezes, você pode desejar limpar o conteúdo em cache de todos os nós de borda e forçá-los a recuperar novos ativos atualizados.  Isso pode ser devido a atualizações em seu aplicativo Web ou para atualizar ativos rapidamente que contenham informações incorretas.

> [!TIP]
> Observe que a limpeza apenas limpa o conteúdo armazenado em cache nos servidores de borda da CDN.  Todos os caches downstream, como servidores proxy e caches do navegador local, ainda podem manter uma cópia armazenada em cache do arquivo.  É importante lembrar-se disso quando você definir a vida útil de um arquivo.  Você pode forçar um cliente downstream a solicitar a versão mais recente do arquivo, dando a ele um nome exclusivo sempre que atualizá-lo ou tirando proveito do [cache de cadeia de caracteres de consulta](cdn-query-string.md).  
> 
> 

Este tutorial orienta você durante a limpeza de ativos de todos os nós de borda de um ponto de extremidade.

## <a name="walkthrough"></a>Instruções
1. No [portal do Azure](https://portal.azure.com), navegue até o perfil CDN que contém o ponto de extremidade que você deseja limpar.
2. Na folha perfil CDN, clique no botão limpar.
   
    ![Folha do perfil CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    A folha limpar é aberta.
   
    ![Folha de limpeza da CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. Na folha limpar, selecione o endereço do serviço que você deseja limpar na lista suspensa URL.
   
    ![Limpar formulário](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Você também pode acessar a folha de limpeza clicando no botão **limpar** na folha ponto de extremidade da CDN.  Nesse caso, o campo **URL** será preenchido previamente com o endereço do serviço desse ponto de extremidade específico.
   > 
   > 
4. Selecione quais ativos você deseja limpar dos nós de borda.  Se você quiser limpar todos os ativos, clique na caixa de seleção **limpar tudo** .  Caso contrário, digite o caminho de cada ativo que você deseja limpar na caixa de texto **caminho** . Os formatos a seguir têm suporte no caminho.
    1. **Limpeza de URL única**: Limpe o ativo individual ESPECIFICANDO a URL completa, com ou sem a extensão de arquivo, por exemplo,`/pictures/strasbourg.png`; `/pictures/strasbourg`
    2. **Limpeza de curinga**: asterisco (\*) pode ser usado como um curinga. Limpe todas as pastas, subpastas e arquivos em um ponto de extremidade com `/*` no caminho ou limpe todas as subpastas e arquivos em uma pasta específica, especificando a pasta seguida por `/*`, por exemplo,`/pictures/*`.  Observe que a limpeza de curinga não tem suporte da CDN do Azure do Akamai no momento. 
    3. **Limpeza de domínio raiz**: Limpe a raiz do ponto de extremidade com "/" no caminho.
   
   > [!TIP]
   > Os caminhos devem ser especificados para limpeza e devem ser uma URL relativa que se encaixe na [expressão regular](/dotnet/standard/base-types/regular-expression-language-quick-reference)a seguir. **Limpar tudo** e a **limpeza de curinga** não tem suporte **da CDN do Azure do Akamai** no momento.
   > > `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";` de limpeza de URL única  
   > > Cadeia de caracteres de consulta `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`de limpeza de curinga. 
   > 
   > Mais caixas de texto de **caminho** serão exibidas depois que você inserir o Text para permitir que você crie uma lista de vários ativos.  Você pode excluir ativos da lista clicando no botão de reticências (...).
   > 
5. Clique no botão **limpar** .
   
    ![Botão limpar](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> As solicitações de limpeza levam cerca de 10 minutos para serem processadas com a **CDN do Azure da Microsoft**, aproximadamente 2 minutos com a **CDN do Azure da Verizon** (Standard e Premium) e aproximadamente 10 segundos com a **CDN do Azure da Akamai**.  A CDN do Azure tem um limite de 50 solicitações de limpeza simultâneas em um determinado momento no nível do perfil. 
> 
> 

## <a name="see-also"></a>Consulte também
* [Pré-carregar recursos num ponto final da CDN do Azure](cdn-preload-endpoint.md)
* [Referência da API REST da CDN do Azure-limpar ou pré-carregar um ponto de extremidade](/rest/api/cdn/endpoints)

