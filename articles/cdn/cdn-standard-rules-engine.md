---
title: Usar um mecanismo de regras para impor HTTPS na CDN do Azure padrão | Microsoft Docs
description: Use o mecanismo de regras para a rede de distribuição de conteúdo (CDN) do Azure padrão da Microsoft para personalizar como a CDN do Azure lida com solicitações HTTP, incluindo o bloqueio da entrega de determinados tipos de conteúdo, a definição de uma política de cache e a modificação de cabeçalhos HTTP. Neste artigo, saiba como criar uma regra para redirecionar os usuários para HTTPS.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 724861305d7a25db409072200ac2bc3bd83f0682
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171579"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Configurar o mecanismo de regras padrão para a CDN do Azure

Este artigo descreve como configurar e usar o mecanismo de regras padrão para a rede de distribuição de conteúdo do Azure (CDN do Azure).

## <a name="standard-rules-engine"></a>Mecanismo de regras padrão

Você pode usar o mecanismo de regras padrão para a CDN do Azure para personalizar como as solicitações HTTP são tratadas. Por exemplo, você pode usar o mecanismo de regras para impor a entrega de conteúdo para usar protocolos específicos, para definir uma política de cache ou para modificar um cabeçalho HTTP. Este artigo demonstra como criar uma regra que redireciona automaticamente os usuários para HTTPS. 

> [!NOTE]
> O mecanismo de regras descrito neste artigo está disponível apenas para a CDN do Azure padrão da Microsoft. 

## <a name="redirect-users-to-https"></a>Redirecionar usuários para HTTPS

1. Em seus perfis da Microsoft, vá para rede de distribuição de conteúdo do Azure.

1. Na página **perfil CDN** , selecione o ponto de extremidade para o qual você deseja criar regras.
  
1. Selecione a guia **mecanismo de regras** .
   
    O painel **mecanismo de regras** é aberto e exibe a lista de regras globais disponíveis. 
   
    [página de novas regras do ![CDN do Azure](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > A ordem na qual várias regras são listadas afeta a forma como as regras são tratadas. As ações especificadas em uma regra podem ser substituídas por uma regra subsequente.
   >

1. Selecione **Adicionar regra** e insira um nome de regra. Os nomes de regra devem começar com uma letra e podem conter apenas números e letras.

1. Para identificar o tipo de solicitações às quais a regra se aplica, crie uma condição de correspondência:
    1. Selecione **Adicionar condição**e, em seguida, selecione a condição de correspondência **protocolo de solicitação** .
    1. Para **operador**, selecione **Equals**.
    1. Para **valor**, selecione **http**.
   
   [![condição de correspondência da regra da CDN do Azure](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Você pode selecionar entre várias condições de correspondência na lista suspensa **Adicionar condição** . Para obter uma lista detalhada das condições de correspondência, consulte [condições de correspondência no mecanismo de regras padrão](cdn-standard-rules-engine-match-conditions.md).
   
1. Selecione a ação a ser aplicada às solicitações que atendem à condição de correspondência:
   1. Selecione **Adicionar ação**e, em seguida, selecione **redirecionamento de URL**.
   1. Para **tipo**, selecione **encontrado (302)** .
   1. Para **protocolo**, selecione **https**.
   1. Deixe todos os outros campos em branco para usar os valores de entrada.
   
   [![ação de regra da CDN do Azure](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Você pode selecionar entre várias ações na lista suspensa **Adicionar ação** . Para obter uma lista detalhada de ações, consulte [ações no mecanismo de regras padrão](cdn-standard-rules-engine-actions.md).

6. Selecione **salvar** para salvar a nova regra. A regra agora está disponível para uso.
   
   > [!IMPORTANT]
   > As alterações de regra podem levar até 15 minutos para serem propagadas por meio da CDN do Azure.
   >
   

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da CDN do Azure](cdn-overview.md)
- [Referência do mecanismo de regras padrão](cdn-standard-rules-engine-reference.md)
- [Condições de correspondência no mecanismo de regras padrão](cdn-standard-rules-engine-match-conditions.md)
- [Ações no mecanismo de regras padrão](cdn-standard-rules-engine-actions.md)
