---
title: Controlar o comportamento com cadeias de caracteres de consulta, o escalão premium de cache de CDN do Azure | Documentos da Microsoft
description: Cadeia de consulta do Azure CDN controles como ficheiros são colocados em cache quando uma solicitação da web contém uma cadeia de consulta de colocação em cache. Este artigo descreve a colocação em cache na CDN do Azure Premium da Verizon produto de cadeia de consulta.
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
ms.openlocfilehash: 2bea8aa06daef5d119b4cbfc4853a2d6ab07ddb7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593505"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Comportamento com cadeias de caracteres de consulta, o escalão premium de cache de CDN do Azure de controlo
> [!div class="op_single_selector"]
> * [Escalão Standard](cdn-query-string.md)
> * [Escalão Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Descrição geral
Com o Azure conteúdo rede de entrega (CDN), pode controlar a forma como os ficheiros são colocados em cache para um pedido web que contém uma cadeia de consulta. Numa solicitação da web com uma cadeia de consulta, a cadeia de consulta é essa parte do pedido que ocorre depois de um ponto de interrogação (?). Uma cadeia de consulta pode conter um ou mais pares de chave-valor, em que o nome do campo e o respetivo valor são separados por um sinal de igual (=). Cada par de chave-valor é separado por um e comercial (&). Por exemplo, http:\//www.contoso.com/content.mov?field1=value1 & My:field2 = value2. Se existir mais do que um par de chave-valor numa cadeia de consulta de um pedido, a ordem não importa. 

> [!IMPORTANT]
> Os produtos CDN standard e premium fornecem a mesma cadeia de consulta, funcionalidade de colocação em cache, mas a interface do usuário é diferente. Este artigo descreve a interface para **CDN do Azure Premium da Verizon**. Para a cadeia de consulta de colocação em cache com produtos standard da CDN do Azure, consulte [comportamento com cadeias de caracteres de consulta, o escalão standard de cache de CDN do Azure de controle](cdn-query-string.md).
>


Três modos de cadeia de caracteres de consulta estão disponíveis:

- **standard-cache**: Modo predefinido. Neste modo, o nó do CDN pontos de presença (POP) passa as cadeias de caracteres de consulta do requerente para o servidor de origem na primeira solicitação e as coloca em cache o recurso. Todas as solicitações subseqüentes para o elemento atendidos a partir do servidor POP ignoram as cadeias de caracteres de consulta até que o elemento em cache expire.

    >[!IMPORTANT] 
    > Se a autorização de token está ativada para qualquer caminho nesta conta, o modo de cache padrão é o único modo que pode ser utilizado. 

- **no-cache**: Neste modo, pedidos com cadeias de consulta não estão em cache no nó POP da CDN. O nó POP obtém o elemento diretamente a partir do servidor de origem e passa-o para o requerente com cada solicitação.

- **unique-cache**: Neste modo, cada solicitação com uma URL exclusiva, incluindo a cadeia de consulta é tratada como um recurso exclusivo com seu próprio cache. Por exemplo, a resposta do servidor de origem para um pedido para example.ashx?q=test1 é armazenado em cache no nó POP e devolvida para as caches subsequentes com a mesma cadeia de consulta. Um pedido para example.ashx?q=test2 é colocado em cache como um recurso separado com sua própria definição de tempo de vida.
   
    >[!IMPORTANT] 
    > Não utilize este modo quando a cadeia de consulta contém parâmetros que irão alterar com cada solicitação, como um ID de sessão ou um nome de utilizador, uma vez que irá resultar numa taxa de acertos na cache baixa.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Alterar as definições de perfis de CDN premium a colocação em cache de cadeia de consulta
1. Abra um perfil da CDN, em seguida, clique em **gerir**.
   
    ![Botão de gerir do perfil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    É aberto o portal de gestão da CDN.
2. Paire o rato sobre o **HTTP grandes** separador, em seguida, coloque o cursor sobre o **definições de Cache** menu flutuante. Clique em **colocação em cache de cadeia de consulta**.
   
    Opções de colocação em cache de cadeia de caracteres de consulta são apresentadas.
   
    ![Opções de colocação em cache de cadeia de consulta de CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Selecione um modo de cadeia de caracteres de consulta, em seguida, clique em **atualização**.

> [!IMPORTANT]
> Dado que demora algum tempo para que o registo propagar pela CDN, alterações de definições de cadeia de caracteres de cache podem não ser imediatamente visíveis. Propagação normalmente fica concluída em 10 minutos.
 

