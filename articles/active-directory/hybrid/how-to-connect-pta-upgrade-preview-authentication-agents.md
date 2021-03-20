---
title: Azure AD Connect - Autenticação pass-through - Atualizar agentes auth | Microsoft Docs
description: Este artigo descreve como atualizar a configuração de autenticação pass-through do Azure Ative(Azure AD).
services: active-directory
keywords: Autenticação pass-through Azure AD Connect, instalar Diretório Ativo, componentes necessários para Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d0507f7751305af5e626cbd7dd6e0dfd1a63a74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95973053"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Autenticação passativa do Azure Ative Directory: Atualizar agentes de autenticação de pré-visualização

## <a name="overview"></a>Descrição geral

Este artigo destina-se a clientes que utilizem autenticação pass-through Azure AD através da pré-visualização. Atualizámos recentemente (e remarcamos) o software Do Agente de Autenticação. É necessário atualizar _manualmente_ os Agentes de autenticação de pré-visualização instalados nos seus servidores no local. Esta atualização manual é apenas uma ação única. Todas as futuras atualizações aos Agentes de Autenticação são automáticas. As razões para a atualização são as seguintes:

- As versões de pré-visualização dos Agentes de Autenticação não receberão mais segurança ou correções de bugs.
-   As versões de pré-visualização dos Agentes de Autenticação não podem ser instaladas em servidores adicionais, para uma elevada disponibilidade.

## <a name="check-versions-of-your-authentication-agents"></a>Versões dos seus Agentes de Autenticação

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Passo 1: Verifique onde estão instalados os seus Agentes de Autenticação

Siga estes passos para verificar onde estão instalados os seus Agentes de Autenticação:

1. Inscreva-se no [centro de administração Azure Ative Com](https://aad.portal.azure.com) as credenciais de Administrador Global para o seu inquilino.
2. Selecione **Azure Ative Directory** na navegação à esquerda.
3. Selecione **Azure Ad Connect**. 
4. Selecione **a autenticação pass-through**. Esta lâmina lista os servidores onde os seus Agentes de Autenticação estão instalados.

![Centro de administração Azure Ative Directory - Lâmina de autenticação pass-through](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Passo 2: Verifique as versões dos seus Agentes de Autenticação

Para verificar as versões dos seus Agentes de Autenticação, em cada servidor identificado no passo anterior, siga estas instruções:

1. Vá ao **Painel de Controlo -> Programas -> Programas e Funcionalidades** no servidor no local.
2. Se houver uma entrada para "**Microsoft Azure AD Connect Authentication Agent**", não precisa de tomar qualquer ação neste servidor.
3. Se houver uma entrada para "**Microsoft Azure AD Application Proxy Connector**", é necessário fazer uma atualização manual neste servidor.

![Versão de pré-visualização do Agente de Autenticação](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Boas práticas a seguir antes de iniciar a atualização

Antes de atualizar, certifique-se de que tem os seguintes itens no lugar:

1. **Criar uma conta global de administrador apenas** na nuvem : Não atualize sem ter uma conta de Administrador Global apenas na nuvem para usar em situações de emergência em que os seus Agentes de Autenticação Pass-through não estejam a funcionar corretamente. Saiba mais [sobre a adição de uma conta de Administrador Global apenas na nuvem.](../fundamentals/add-users-azure-active-directory.md) Fazer este passo é fundamental e garante que não fique trancado fora do seu inquilino.
2.  **Garantir uma elevada disponibilidade**: Se não estiver concluído anteriormente, instale um segundo Agente de Autenticação Autónomo para fornecer alta disponibilidade para pedidos de inscrição, utilizando estas [instruções](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Atualizar o Agente de Autenticação no seu servidor AD AD Ad

Precisa de atualizar o Azure AD Connect antes de atualizar o Agente de Autenticação no mesmo servidor. Siga estes passos tanto nos seus servidores primários como de realização Azure AD Connect:

1. **Upgrade Azure AD Connect**: Siga este [artigo](how-to-upgrade-previous-version.md) e atualize para a versão mais recente do Azure AD Connect.
2. **Desinstalar a versão de pré-visualização do Agente de Autenticação**: Descarregue [este script PowerShell](https://aka.ms/rmpreviewagent) e execute-o como Administrador no servidor.
3. **Descarregue a versão mais recente do Agente de Autenticação (versões 1.5.389.0 ou posterior)**: Inscreva-se no [centro de administração Azure Ative Com](https://aad.portal.azure.com) as credenciais de Administrador Global do seu inquilino. Selecione **Azure Ative Directory -> Azure AD Connect -> Agente de autenticação pass-through ->**. Aceite os [termos de serviço](https://aka.ms/authagenteula) e descarregue a versão mais recente do Agente de Autenticação. Também pode baixar o Agente de Autenticação a partir [daqui.](https://aka.ms/getauthagent)
4. **Instale a versão mais recente do Agente de Autenticação**: Execute o executável descarregado no Passo 3. Forneça as credenciais de Administrador Global do seu inquilino quando solicitado.
5. **Verifique se a versão mais recente foi instalada**: Como mostrado anteriormente, vá ao Control Panel **-> Programas -> Programas e Funcionalidades e** verifique se existe uma entrada para " Microsoft **Azure AD Connect Authentication Agent**".

>[!NOTE]
>Se verificar a lâmina de autenticação pass-through no centro de administração do [Diretório Ativo Azure](https://aad.portal.azure.com) após completar os passos anteriores, verá duas entradas do Agente de Autenticação por servidor - uma entrada que mostra o Agente de Autenticação como **Ativo** e a outra como **Inativa**. Isto é _esperado._ A entrada **Inativa** é automaticamente deixada após alguns dias.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Atualizar o Agente de Autenticação em outros servidores

Siga estes passos para atualizar agentes de autenticação em outros servidores (onde o Azure AD Connect não está instalado):

1. **Desinstalar a versão de pré-visualização do Agente de Autenticação**: Descarregue [este script PowerShell](https://aka.ms/rmpreviewagent) e execute-o como Administrador no servidor.
2. **Descarregue a versão mais recente do Agente de Autenticação (versões 1.5.389.0 ou posterior)**: Inscreva-se no [centro de administração Azure Ative Com](https://aad.portal.azure.com) as credenciais de Administrador Global do seu inquilino. Selecione **Azure Ative Directory -> Azure AD Connect -> Agente de autenticação pass-through ->**. Aceite os termos de serviço e descarregue a versão mais recente.
3. **Instale a versão mais recente do Agente de Autenticação**: Execute o executável descarregado no Passo 2. Forneça as credenciais de Administrador Global do seu inquilino quando solicitado.
4. **Verifique se a versão mais recente foi instalada**: Como mostrado anteriormente, vá ao Control Panel **-> Programas -> Programas e Funcionalidades e** verifique se existe uma entrada chamada Microsoft **Azure AD Connect Authentication Agent**.

>[!NOTE]
>Se verificar a lâmina de autenticação pass-through no centro de administração do [Diretório Ativo Azure](https://aad.portal.azure.com) após completar os passos anteriores, verá duas entradas do Agente de Autenticação por servidor - uma entrada que mostra o Agente de Autenticação como **Ativo** e a outra como **Inativa**. Isto é _esperado._ A entrada **Inativa** é automaticamente deixada após alguns dias.

## <a name="next-steps"></a>Passos seguintes
- [**Resolução de problemas**](tshoot-connect-pass-through-authentication.md) - Aprenda a resolver problemas comuns com a funcionalidade.