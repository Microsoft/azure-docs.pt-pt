---
title: Auto-revisão de um pacote de acesso na gestão de direitos da AD Azure
description: Saiba como rever o acesso dos utilizadores aos pacotes de acesso à gestão de direitos em avaliações de acesso ao Diretório Ativo do Azure (Pré-visualização).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45da1170705bab1206a98c59e02c7616c25ce502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967761"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Auto-revisão de um pacote de acesso na gestão de direitos da AD Azure

A gestão de direitos da Azure AD simplifica a forma como as empresas gerem o acesso a grupos, aplicações e sites SharePoint. Este artigo descreve como um utilizador faz uma auto-revisão do seu ou pacote de acesso atribuído.

## <a name="open-the-access-review"></a>Abra a revisão de acesso

Para fazer uma revisão de acesso, primeiro deve abrir a revisão de acesso. Utilize o seguinte procedimento para encontrar e abrir a revisão de acesso:

1. Pode receber um e-mail da Microsoft que lhe pede para rever o acesso. Localize o e-mail para abrir a revisão de acesso. Aqui está um exemplo de um e-mail solicitando uma revisão do acesso: 
    
    ![Access review self-reviewer email](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Clique no link de **acesso Rever.**

1. Também pode ir diretamente para https://myaccess.microsoft.com encontrar as suas avaliações de acesso pendentes se não receber um e-mail.  (Para o Governo `https://myaccess.microsoft.us` dos EUA, use em vez disso.)

1. Clique em comentários de **Acesso** na barra de navegação esquerda para ver uma lista de avaliações de acesso pendentes atribuídas a si.


1.  Clique na avaliação que gostaria de começar.

## <a name="perform-the-access-review"></a>Realizar a revisão de acesso

Assim que abrir a revisão de acesso, poderá ver o seu acesso. Utilize o seguinte procedimento para efazer a revisão de acesso:

1.  Decida se ainda precisa de acesso ao pacote de acesso. Por exemplo, o projeto em que está a trabalhar não está completo, por isso ainda precisa de acesso para continuar a trabalhar no projeto.

1.  Clique em **Sim** para manter o seu acesso ou clique **em Não** para remover o seu acesso.
    >[!NOTE]
    >Se afirmou que já não precisa de acesso, não foi imediatamente removido do pacote de acesso. Será removido do pacote de acesso quando a revisão terminar ou se um administrador parar a revisão.

1.  Se clicar em **Sim,** poderá ter de incluir uma declaração de justificação na caixa **Reason.**

1.  Clique em **Submeter**.

Pode voltar à revisão se mudar de ideia si e decidir mudar a sua resposta antes do final da revisão.

## <a name="next-steps"></a>Passos seguintes

- [Rever o acesso aos pacotes de acesso](entitlement-management-access-reviews-review-access.md) 
