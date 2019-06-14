---
title: Agentes de atualização de autenticação do Azure AD Connect - autenticação-pass through - | Documentos da Microsoft
description: Este artigo descreve como atualizar a configuração de autenticação de pass-through do Azure Active Directory (Azure AD).
services: active-directory
keywords: Autenticação do Azure AD Connect pass-through, Active Directory, de instalação necessários componentes para o Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 494ccc3b90b8c249ee935087dcf0f0b5264b02ca
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60386776"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Autenticação pass-through do Azure Active Directory: Atualizar agentes de autenticação de pré-visualização

## <a name="overview"></a>Descrição geral

Este artigo destina-se a clientes que utilizam a autenticação pass-through do Azure AD através da pré-visualização. Podemos recentemente atualizado (e marca alterada para) o software do agente de autenticação. Precisa _manualmente_ atualização preview Authentication Agents instalados nos seus servidores no local. Esta atualização manual é apenas uma ação única. Todas as atualizações futuras aos agentes de autenticação são automáticas. Os motivos para atualizar são os seguintes:

- As versões de pré-visualização de agentes de autenticação não receberão qualquer ainda mais a segurança ou as correções de erros.
-   As versões de pré-visualização de agentes de autenticação não podem ser instaladas em servidores adicionais, para elevada disponibilidade.

## <a name="check-versions-of-your-authentication-agents"></a>Verifique as versões dos seus agentes de autenticação

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Passo 1: Verifique onde estão instalados os agentes de autenticação

Siga estes passos para verificar onde estão instalados os agentes de autenticação:

1. Inicie sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de Administrador Global do seu inquilino.
2. Selecione **do Azure Active Directory** no painel de navegação esquerda.
3. Selecione **do Azure AD Connect**. 
4. Selecione **autenticação pass-through**. Este painel apresenta uma lista de servidores onde estão instalados os agentes de autenticação.

![Centro de administração do Active Directory do Azure – painel de autenticação pass-through](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Passo 2: Verificar as versões dos seus agentes de autenticação

Para verificar as versões dos seus agentes de autenticação, em cada servidor identificado no passo anterior, siga estas instruções:

1. Aceda a **painel de controlo -> programas -> programas e funcionalidades** no servidor no local.
2. Se houver uma entrada para "**agente do Microsoft Azure AD Connect autenticação**", não precisa de tomar nenhuma ação neste servidor.
3. Se houver uma entrada para "**conector de Proxy de aplicações do Microsoft Azure AD**", terá de atualizar manualmente neste servidor.

![Versão de pré-visualização do agente de autenticação](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Melhores práticas a seguir antes de iniciar a atualização

Antes de atualizar, certifique-se de que tem os seguintes itens no local:

1. **Criar conta de Administrador Global apenas na cloud**: Não atualize sem ter uma conta de Administrador Global apenas na cloud para utilizar em situações de emergência, onde os agentes de autenticação pass-through não estão a funcionar corretamente. Saiba mais sobre [adicionar uma conta de Administrador Global apenas na cloud](../active-directory-users-create-azure-portal.md). Efetuar este passo é fundamental e garante que não fica bloqueado fora do seu inquilino.
2.  **Certifique-se de elevada disponibilidade**: Se não tenha sido feita, instalar uma segunda autónoma o agente de autenticação para proporcionar elevada disponibilidade para pedidos de início de sessão, usá-los [instruções](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Atualizar o agente de autenticação no seu servidor do Azure AD Connect

Tem de atualizar o Azure AD Connect antes de atualizar o agente de autenticação no mesmo servidor. Siga estes passos no seu principal e servidores do Azure AD Connect de teste:

1. **Atualização Azure AD Connect**: Siga este [artigo](how-to-upgrade-previous-version.md) e atualize para a versão mais recente do Azure AD Connect.
2. **Desinstale a versão de pré-visualização do agente de autenticação**: Baixe [este script do PowerShell](https://aka.ms/rmpreviewagent) e executá-lo como um administrador no servidor.
3. **Baixe a versão mais recente do agente de autenticação (versões 1.5.389.0 ou posterior)** : Inicie sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com credenciais de Administrador Global do seu inquilino. Selecione **do Azure Active Directory -> do Azure AD Connect -> autenticação pass-through -> Transferir agente**. Aceite o [termos de serviço](https://aka.ms/authagenteula) e transferir a versão mais recente do agente de autenticação. Também pode transferir o agente de autenticação a partir [aqui](https://aka.ms/getauthagent).
4. **Instalar a versão mais recente do agente de autenticação**: Execute o executável transferido no passo 3. Forneça as credenciais de Administrador Global do seu inquilino quando lhe for pedido.
5. **Certifique-se de que foi instalada a versão mais recente**: Conforme mostrado anteriormente, aceda a **painel de controlo -> programas -> programas e funcionalidades** e certifique-se de que existe uma entrada para "**agente do Microsoft Azure AD Connect autenticação**".

>[!NOTE]
>Se verificar o painel de autenticação pass-through a [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) depois de concluir a anterior etapas, verá duas entradas de agente de autenticação por servidor - uma entrada que mostra a autenticação Agente como **Active Directory** e outras como **Inativos**. Isto é _esperado_. O **Inactive** entrada é removida automaticamente após alguns dias.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Atualizar o agente de autenticação em outros servidores

Siga estes passos para atualizar agentes de autenticação em outros servidores (em que o Azure AD Connect não está instalado):

1. **Desinstale a versão de pré-visualização do agente de autenticação**: Baixe [este script do PowerShell](https://aka.ms/rmpreviewagent) e executá-lo como um administrador no servidor.
2. **Baixe a versão mais recente do agente de autenticação (versões 1.5.389.0 ou posterior)** : Inicie sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com credenciais de Administrador Global do seu inquilino. Selecione **do Azure Active Directory -> do Azure AD Connect -> autenticação pass-through -> Transferir agente**. Aceite os termos de serviço e transferir a versão mais recente.
3. **Instalar a versão mais recente do agente de autenticação**: Execute o executável transferido no passo 2. Forneça as credenciais de Administrador Global do seu inquilino quando lhe for pedido.
4. **Certifique-se de que foi instalada a versão mais recente**: Conforme mostrado anteriormente, aceda a **painel de controlo -> programas -> programas e funcionalidades** e certifique-se de que existe uma entrada chamada **agente do Microsoft Azure AD Connect autenticação**.

>[!NOTE]
>Se verificar o painel de autenticação pass-through a [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) depois de concluir a anterior etapas, verá duas entradas de agente de autenticação por servidor - uma entrada que mostra a autenticação Agente como **Active Directory** e outras como **Inativos**. Isto é _esperado_. O **Inactive** entrada é removida automaticamente após alguns dias.

## <a name="next-steps"></a>Passos Seguintes
- [**Resolução de problemas** ](tshoot-connect-pass-through-authentication.md) -Saiba como resolver problemas comuns com a funcionalidade.
