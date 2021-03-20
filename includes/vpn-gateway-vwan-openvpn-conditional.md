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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "77471563"
---
O Acesso Condicional permite um controlo de acesso fino por aplicação. Para utilizar o Acesso Condicional, deverá ter o Azure AD Premium 1 ou um licenciamento superior aplicado aos utilizadores que estarão sujeitos às regras de Acesso Condicional.

1. Navegue para as **aplicações Da Enterprise - Todas as aplicações** página e clique em **Azure VPN**.

   - Clique **em Acesso Condicional**.
   - Clique **em Nova política** para abrir o Painel **Novo.**
2. No **novo** painel, navegue para **atribuições -> utilizadores e grupos**. Nos **Utilizadores e grupos ->** **Incluir** o separador:

   - Clique **em Selecionar utilizadores e grupos.**
   - Verifique **os Utilizadores e grupos.**
   - Clique **em Selecionar** para selecionar um grupo ou conjunto de utilizadores a serem afetados por MFA.
   - Clique em **Concluído**.

   ![Atribuições](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. No **painel New,** navegue para os **controlos de acesso -> Grant** Pane:

   - Clique **no acesso ao Grant**.
   - Clique **Em Requerer a autenticação de vários fatores.**
   - Clique **Em Exigir todos os controlos selecionados.**
   - Clique em **Selecionar**.
   
   ![Acesso a subvenção - MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Na secção **de política ativa:**

   - **Selecione on**.
   - Clique em **Criar**.

   ![Ativar a Política](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)