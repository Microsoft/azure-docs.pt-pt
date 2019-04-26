---
title: Remover um ponto de final de CDN do Azure | Documentos da Microsoft
description: Saiba como remover todo o conteúdo em cache a partir de um ponto de final de CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: magattus
ms.openlocfilehash: 76e7817be81a97c8d1a0b9ca2fea8378c3c733e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324916"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Remover um ponto de final de CDN do Azure
## <a name="overview"></a>Descrição geral
Nós de extremidade da CDN do Azure colocará em cache ativos até que expire time-to-live (TTL) o elemento.  Depois de TTL o elemento expira, quando um cliente solicita o elemento do nó de extremidade, o nó de extremidade irá obter uma nova cópia atualizada do recurso para atender à solicitação do cliente e store atualizar a cache.

A melhor prática para se certificar de que os utilizadores obtêm sempre a cópia mais recente dos seus bens é para a versão seus ativos de cada atualização e publique-os como novos URLs.  CDN imediatamente recuperará os novos recursos das próximas solicitações de cliente.  Por vezes, pode pretender remover o conteúdo em cache de todos os nós de extremidade e forçá-los todos para obter os novos recursos atualizados.  Isto pode dever-se a atualizações à sua aplicação web ou a rapidamente os recursos de atualização que contêm informações incorretas.

> [!TIP]
> Tenha em atenção que apenas remover limpa o conteúdo em cache em servidores de borda do CDN.  Todas as caches downstream, tais como servidores de proxy e as caches do navegador local, ainda podem manter uma cópia em cache do arquivo.  É importante lembrar-se ao definir um arquivo time-to-live.  Pode forçar um cliente jusante solicitar a versão mais recente do seu arquivo ao dar a ele um nome exclusivo sempre que efetua a atualização ou ao tirar partido da [colocação em cache de cadeia de caracteres de consulta](cdn-query-string.md).  
> 
> 

Este tutorial orienta-o através da remoção de recursos de todos os nós de extremidade de um ponto de extremidade.

## <a name="walkthrough"></a>Instruções
1. Na [Portal do Azure](https://portal.azure.com), navegue até ao perfil CDN que contém o ponto final que pretende remover.
2. No painel de perfil da CDN, clique no botão de remoção.
   
    ![Painel do perfil CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    É aberto o painel de remoção.
   
    ![Painel de remoção da CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. No painel de remoção, selecione o endereço do serviço que pretende remover no menu pendente do URL.
   
    ![Remover o formulário](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Também pode obter o painel de remoção clicando a **remover** botão no painel de ponto final da CDN.  Nesse caso, o **URL** campo será preenchido previamente com o endereço do serviço de ponto de extremidade específico.
   > 
   > 
4. Selecione quais recursos de que pretende remover a partir de nós de extremidade.  Se quiser limpar todos os recursos, clique nas **remover todos** caixa de verificação.  Caso contrário, escreva o caminho de cada ativo a limpar no **caminho** caixa de texto. Abaixo formatos são suportadas no caminho.
    1. **Remoção de URL única**: Remover recurso individual, especificando o URL completo, com ou sem a extensão de ficheiro, por exemplo,`/pictures/strasbourg.png`; `/pictures/strasbourg`
    2. **Remoção de carateres universais**: Asterisco (\*) pode ser utilizada como um caráter universal. Remover todas as pastas, subpastas e arquivos num ponto final com `/*` o caminho ou a remoção de todos os ficheiros numa pasta específica, especificando a pasta e subpastas seguido `/*`, por exemplo,`/pictures/*`.  Tenha em atenção que remoção de carateres universais não é suportada pelo Azure CDN da Akamai atualmente. 
    3. **Remoção de domínio de raiz**: Remover a raiz do ponto de extremidade com "/" no caminho.
   
   > [!TIP]
   > Tem de ser especificados para a remoção de caminhos e tem de ser um URL relativo que se ajustem o seguinte [expressão regular](/dotnet/standard/base-types/regular-expression-language-quick-reference). **Remover todos** e **remoção de carateres universais** não suportado pelo **CDN do Azure da Akamai** atualmente.
   > > Remoção de URL única `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Cadeia de consulta `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Remoção de carateres universais `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`. 
   > 
   > Obter mais **caminho** caixas de texto serão apresentado depois de introduzir texto para permitir a criação de uma lista de vários recursos.  Pode eliminar os recursos da lista ao clicar no botão de reticências (...).
   > 
5. Clique nas **remover** botão.
   
    ![Remover botão](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Os pedidos de remoção demoram aproximadamente 2 a 3 minutos para processar com **CDN do Azure da Verizon** (standard e premium) e aproximadamente 7 minutos com **CDN do Azure da Akamai**.  A CDN do Azure tem um limite de 50 simultâneas remover pedidos num determinado momento ao nível do perfil. 
> 
> 

## <a name="see-also"></a>Consulte também
* [Pré-carregar recursos num ponto final da CDN do Azure](cdn-preload-endpoint.md)
* [Referência da API de REST do CDN do Azure - remover ou pré-carregar um ponto final](/rest/api/cdn/endpoints)

