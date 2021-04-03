---
title: Auto-revisão de um pacote de acesso na gestão de direitos Azure AD
description: Saiba como rever o acesso dos utilizadores aos pacotes de acesso à gestão de direitos em avaliações de acesso ao Azure Ative Directory (Preview).
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31c44f2423cdc5c43638fe2515757bcb11a9814c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798448"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Auto-revisão de um pacote de acesso na gestão de direitos Azure AD

A gestão de direitos AZure AD simplifica a forma como as empresas gerem o acesso a grupos, aplicações e sites SharePoint. Este artigo descreve como um utilizador faz uma auto-revisão dos seus pacotes de acesso atribuídos.

## <a name="open-the-access-review"></a>Abra a revisão de acesso

Para fazer uma revisão de acesso, você deve primeiro abrir a revisão de acesso. Utilize o seguinte procedimento para encontrar e abrir a revisão de acesso:

1. Pode receber um e-mail da Microsoft que lhe pede para rever o acesso. Localize o e-mail para abrir a revisão de acesso. Aqui está um exemplo de um e-mail solicitando uma revisão de acesso: 
    
    ![Acesso a comentário auto-revisor e-mail](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Clique no link **de acesso do Review.**

1. Também pode ir diretamente https://myaccess.microsoft.com para encontrar as suas avaliações de acesso pendentes se não receber um e-mail.  (Para o Governo dos EUA, use `https://myaccess.microsoft.us` em vez disso.)

1. Clique em **comentários de Acesso** na barra de navegação esquerda para ver uma lista de avaliações de acesso pendentes atribuídas a si.


1.  Clique na análise que gostaria de iniciar.

## <a name="perform-the-access-review"></a>Realizar a revisão de acesso

Assim que abrir a revisão de acesso, poderá ver o seu acesso. Utilize o seguinte procedimento para fazer a revisão de acesso:

1.  Decida se ainda precisa de acesso ao pacote de acesso. Por exemplo, o projeto em que está a trabalhar não está completo, por isso ainda precisa de ter acesso para continuar a trabalhar no projeto.

1.  Clique **em Sim** para manter o seu acesso ou clique em **Não** para remover o seu acesso.
    >[!NOTE]
    >Se afirmou que já não precisa de acesso, não é imediatamente removido do pacote de acesso. Será removido do pacote de acesso quando a revisão terminar ou se um administrador parar a revisão.

1.  Se clicar em **Sim,** poderá ter de incluir uma declaração de justificação na caixa **Reason.**

1.  Clique **em Submeter.**

Pode voltar à revisão se mudar de ideias e decidir alterar a sua resposta antes do final da revisão.

## <a name="next-steps"></a>Passos seguintes

- [Rever o acesso aos pacotes de acesso](entitlement-management-access-reviews-review-access.md) 
