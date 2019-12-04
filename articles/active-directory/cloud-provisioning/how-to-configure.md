---
title: Configuração de novo agente de provisionamento de Azure AD Connect nuvem
description: Este tópico descreve como instalar o provisionamento de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 956962503dcb3a6355d455ff96744143c5965d1e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794305"
---
# <a name="azure-ad-connect-cloud-provisioning-new-configuration"></a>Azure AD Connect a nova configuração de provisionamento de nuvem

Depois de instalar o agente, você precisará entrar no portal do Azure e configurar o provisionamento.  Use as etapas a seguir para habilitar o agente.

## <a name="configure-provisioning"></a>Configurar provisionamento
Para configurar o provisionamento, use as seguintes etapas:

1.  No portal do AD do Azure, clique em **Azure Active Directory**
2.  Clique em **Azure ad Connect**
3.  Selecione **gerenciar provisionamento (visualização)** 
![](media/how-to-configure/manage1.png)

4.  Clique em **nova configuração**.
5.  Na tela de configuração, o domínio local é preenchido previamente
6. Insira um **email de notificação**. Este email será notificado 
7. Quando o provisionamento não está íntegro.  
8. Mova o seletor para **habilitar** e clique em **salvar**.
![](media/tutorial-single-forest/configure2.png)

## <a name="scoping-provisioning-to-specific-users-and-groups"></a>Escopo de provisionamento para usuários e grupos específicos
Se você quiser fazer o escopo do agente para sincronizar apenas usuários e grupos específicos, poderá fazê-lo. Você pode usar o escopo usando grupos do AD locais ou unidades organizacionais. Você não pode configurar grupos e unidades organizacionais em uma configuração. 

1.  No portal do AD do Azure, clique em **Azure Active Directory**
2.  Clique em **Azure ad Connect**
3.  Selecionar **gerenciar provisionamento (visualização)**
4.  Em **configuração** , clique em sua configuração.  
![](media/how-to-configure/scope1.png)

5.  Em **Configurar**, selecione **todos os usuários** para alterar o escopo da regra de configuração.
![](media/how-to-configure/scope2.png)

6. À direita, você pode alterar o escopo para incluir apenas grupos de segurança inserindo o nome distinto do grupo e clicando em **Adicionar**.
![](media/how-to-configure/scope3.png)

7. Ou altere-o para incluir apenas UOs específicas. Clique em **concluído** e em **salvar**.
![](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Reiniciar o provisionamento 
Se não quiser aguardar a próxima execução agendada, você poderá disparar a execução de provisionamento usando o botão reiniciar provisionamento. 
1.  No portal do AD do Azure, clique em **Azure Active Directory**
2.  Clique em **Azure ad Connect**
3.  Selecionar **gerenciar provisionamento (visualização)**
4.  Em **configuração** , clique em sua configuração.  
![](media/how-to-configure/scope1.png)

5.  Na parte superior, clique em **reiniciar provisionamento**.

## <a name="removing-a-configuration"></a>Removendo uma configuração
Se você quiser excluir uma configuração, poderá fazer isso usando as etapas a seguir.

1.  No portal do AD do Azure, clique em **Azure Active Directory**
2.  Clique em **Azure ad Connect**
3.  Selecionar **gerenciar provisionamento (visualização)**
4.  Em **configuração** , clique em sua configuração.  
![](media/how-to-configure/scope1.png)

5.  Na parte superior, clique em **excluir**.
![](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Não há nenhuma confirmação antes de excluir uma configuração, portanto, certifique-se de que essa é a ação que você deseja executar antes de clicar em **excluir**.


## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)
