---
title: Controlar o comportamento com cadeias de caracteres de consulta, o escalão standard de cache de CDN do Azure | Documentos da Microsoft
description: Cadeia de consulta do Azure CDN controles como ficheiros são colocados em cache quando uma solicitação da web contém uma cadeia de consulta de colocação em cache. Este artigo descreve a cadeia de consulta de colocação em cache em produtos standard da CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: f0dab3dc81c626e3e7f8c79b4142e5eb4f2a1276
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60324785"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Comportamento com cadeias de caracteres de consulta, o escalão standard de cache de CDN do Azure de controlo
> [!div class="op_single_selector"]
> * [Escalão Standard](cdn-query-string.md)
> * [Escalão Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Descrição geral
Com o Azure conteúdo rede de entrega (CDN), pode controlar a forma como os ficheiros são colocados em cache para um pedido web que contém uma cadeia de consulta. Numa solicitação da web com uma cadeia de consulta, a cadeia de consulta é essa parte do pedido que ocorre depois de um ponto de interrogação (?). Uma cadeia de consulta pode conter um ou mais pares de chave-valor, em que o nome do campo e o respetivo valor são separados por um sinal de igual (=). Cada par de chave-valor é separado por um e comercial (&). Por exemplo, http:\//www.contoso.com/content.mov?field1=value1 & My:field2 = value2. Se existir mais do que um par de chave-valor numa cadeia de consulta de um pedido, a ordem não importa. 

> [!IMPORTANT]
> A CDN do Azure standard e produtos de premium fornecem a mesma cadeia de consulta, funcionalidade de colocação em cache, mas a interface do usuário é diferente. Este artigo descreve a interface para **CDN Standard do Microsoft Azure**, **CDN do Azure Standard da Akamai** e **CDN do Azure Standard da Verizon**. Para a cache de cadeia de caracteres de consulta com **CDN do Azure Premium da Verizon**, consulte [comportamento com cadeias de caracteres de consulta, o escalão premium de cache de CDN do Azure de controle](cdn-query-string-premium.md).

Três modos de cadeia de caracteres de consulta estão disponíveis:

- **Ignorar cadeias de consulta**: Modo predefinido. Neste modo, o nó do CDN pontos de presença (POP) passa as cadeias de caracteres de consulta do requerente para o servidor de origem na primeira solicitação e as coloca em cache o recurso. Todas as solicitações subseqüentes para o elemento atendidos a partir do POP ignoram as cadeias de caracteres de consulta até que o elemento em cache expire.

- **Ignorar a colocação em cache para cadeias de caracteres de consulta**: Neste modo, pedidos com cadeias de consulta não estão em cache no nó POP da CDN. O nó POP obtém o elemento diretamente a partir do servidor de origem e passa-o para o requerente com cada solicitação.

- **Colocar em cache todos os URL exclusivos**: Neste modo, cada solicitação com uma URL exclusiva, incluindo a cadeia de consulta é tratada como um recurso exclusivo com seu próprio cache. Por exemplo, a resposta do servidor de origem para um pedido para example.ashx?q=test1 é armazenado em cache no nó POP e devolvida para as caches subsequentes com a mesma cadeia de consulta. Um pedido para example.ashx?q=test2 é colocado em cache como um recurso separado com sua própria definição de tempo de vida.
   
    >[!IMPORTANT] 
    > Não utilize este modo quando a cadeia de consulta contém parâmetros que irão alterar com cada solicitação, como um ID de sessão ou um nome de utilizador, uma vez que irá resultar numa taxa de acertos na cache baixa.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Alterar as definições de perfis de CDN standard de colocação em cache de cadeia de consulta
1. Abra um perfil da CDN, em seguida, selecione o ponto final da CDN que pretende gerir.
   
   ![Pontos finais do perfil CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. No painel esquerdo em definições, clique em **regras de colocação em cache**.
   
    ![Botão Regras de colocação em cache da CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Na **comportamento de colocação em cache de cadeia de consulta** lista, selecione um modo de cadeia de caracteres de consulta, em seguida, clique em **guardar**.
   
   ![Opções de colocação em cache de cadeia de consulta de CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Dado que demora algum tempo para que o registo propagar através da CDN do Azure, as alterações de definições de cadeia de caracteres de cache podem não ser imediatamente visíveis:
> - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
> - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
> - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída em 10 minutos. 



