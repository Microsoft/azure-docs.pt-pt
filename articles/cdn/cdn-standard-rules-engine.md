---
title: Impor HTTPS usando o mecanismo de regras padrão da CDN do Azure | Microsoft Docs
description: O mecanismo de regras padrão permite que você personalize como as solicitações HTTP são tratadas pela CDN do Azure da Microsoft, como o bloqueio da entrega de determinados tipos de conteúdo, a definição de uma política de cache e a modificação de cabeçalhos HTTP.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: b24c4a04e0c02258a918ee075066d90c22ea0c75
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615926"
---
# <a name="azure-cdn-standard-rules-engine"></a>Mecanismo de regras padrão da CDN do Azure

> [!NOTE]
> O mecanismo de regras padrão só está disponível para a CDN do Azure da Microsoft. 

O mecanismo de regras padrão da CDN do Azure permite que você personalize como as solicitações HTTP são tratadas. Por exemplo, impor a distribuição de conteúdo sobre protocolos específicos, definir uma política de cache ou modificar um cabeçalho HTTP. Este tutorial demonstra como criar uma regra que redirecionará automaticamente os usuários para HTTPS. 


## <a name="tutorial"></a>Tutorial

1. Na página **perfil CDN** na CDN do Azure de perfis da Microsoft, selecione o ponto de extremidade para o qual você deseja configurar regras.
  
2. Selecione a guia **mecanismo de regras** à esquerda.
   
    A folha mecanismo de regras é exibida, com a regra global. 
   
    [página de novas regras do ![CDN](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > A ordem na qual várias regras são listadas afeta como elas são tratadas. Uma regra subsequente pode substituir as ações especificadas por uma regra anterior.
   >

3. Clique no botão **Adicionar regra** e forneça um nome de regra. Os nomes de regra devem começar com uma letra e conter apenas números e letras.

4. Identifique o tipo de solicitações às quais a regra se aplica. Use a lista suspensa para selecionar a condição de correspondência de **protocolo de solicitação** e use o valor **igual** a **http**.
   
   [condição de correspondência de regra de CDN ![](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Várias condições de correspondência estão disponíveis na lista suspensa. Para obter uma lista detalhada das condições de correspondência, consulte [condições de correspondência do mecanismo de regras](cdn-standard-rules-engine-match-conditions.md).
   
5. Escolha a ação que será aplicada às solicitações identificadas. Use a lista suspensa para selecionar a ação de **redirecionamento de URL** e use o valor **encontrado (302)** para o tipo e **https** para protocolo. Deixe todos os outros campos em branco para usar os valores de entrada.
   
   [ação de regra de ![CDN](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Várias ações estão disponíveis na lista suspensa. Para obter uma lista detalhada de ações, consulte [ações do mecanismo de regras](cdn-standard-rules-engine-actions.md).

6. Selecione **salvar** para salvar a nova regra.  A nova regra será implantada agora.
   
   > [!IMPORTANT]
   > As alterações de regras podem levar até 15 minutos para serem propagadas por meio da CDN do Azure.
   >
   

## <a name="see-also"></a>Consultar também

- [Visão geral da CDN do Azure](cdn-overview.md)
- [Referência do mecanismo de regras padrão](cdn-standard-rules-engine-reference.md)
- [Condições de correspondência do mecanismo de regras padrão](cdn-standard-rules-engine-match-conditions.md)
- [Ações do mecanismo de regras padrão](cdn-standard-rules-engine-actions.md)
