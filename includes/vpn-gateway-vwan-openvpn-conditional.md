---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471563"
---
O Acesso Condicional permite um controlo de acesso de grãos finos por aplicação. Para utilizar o Acesso Condicional, deverá ter o Azure AD Premium 1 ou um maior licenciamento aplicado aos utilizadores que estarão sujeitos às regras de Acesso Condicional.

1. Navegue para as **aplicações da Enterprise - Todas as aplicações** página e clique em **Azure VPN**.

   - Clique em **Acesso Condicional**.
   - Clique em **Nova política** para abrir o **novo** painel.
2. No **novo** painel, navegue para **Atribuições -> Utilizadores e grupos.** Nos **Utilizadores e grupos ->** **Inclua** o separador:

   - Clique em **Selecionar utilizadores e grupos**.
   - Verifique **os utilizadores e grupos**.
   - Clique em **Selecionar** para selecionar um grupo ou conjunto de utilizadores para serem afetados pelo MFA.
   - Clique em **Concluído**.

   ![Atribuições](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. No **novo** painel, navegue para os controlos de **acesso -> Grant** pane:

   - Clique no **acesso ao Grant**.
   - Clique **Exigir autenticação de vários fatores.**
   - Clique **Exigir todos os controlos selecionados**.
   - Clique em **Selecionar**.
   
   ![Acesso à concessão - MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Na secção **política Enable:**

   - Selecione **on**.
   - Clique em **Criar**.

   ![Ativar a Política](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)