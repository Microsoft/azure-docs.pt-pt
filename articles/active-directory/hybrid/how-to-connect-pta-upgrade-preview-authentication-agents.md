---
title: Azure AD Connect - Autenticação Pass-through - Upgrade auth agents [ Microsoft Docs
description: Este artigo descreve como atualizar a configuração de autenticação pass-through do Seu Diretório Ativo Azure (Azure AD).
services: active-directory
keywords: Autenticação de passagem de ligação Azure AD, instala o Diretório Ativo, componentes necessários para Azure AD, SSO, Single Sign-on
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60386776"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Autenticação pass-through do Diretório Ativo Azure: Atualização para visualização agentes de autenticação

## <a name="overview"></a>Descrição geral

Este artigo destina-se a clientes que utilizem autenticação pass-through Azure AD através de pré-visualização. Atualizámos recentemente (e remarcamos) o software do Agente de Autenticação. É necessário atualizar _manualmente_ os agentes de autenticação instalados nos seus servidores no local. Esta atualização manual é apenas uma ação única. Todas as futuras atualizações para agentes de autenticação são automáticas. As razões para atualizar são as seguintes:

- As versões de pré-visualização dos Agentes de Autenticação não receberão mais nenhuma correção de segurança ou bugs.
-   As versões de pré-visualização dos Agentes de Autenticação não podem ser instaladas em servidores adicionais, para uma elevada disponibilidade.

## <a name="check-versions-of-your-authentication-agents"></a>Verifique versões dos seus Agentes de Autenticação

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Passo 1: Verifique onde estão instalados os seus Agentes de Autenticação

Siga estes passos para verificar onde estão instalados os seus Agentes de Autenticação:

1. Inscreva-se no Centro de [Administração do Diretório Ativo Azure](https://aad.portal.azure.com) com as credenciais do Administrador Global para o seu inquilino.
2. Selecione **Azure Ative Diretório** na navegação à esquerda.
3. Selecione **Azure AD Connect**. 
4. **Selecione autenticação pass-through**. Esta lâmina lista os servidores onde os seus Agentes de Autenticação estão instalados.

![Centro de administração de Diretório Ativo Azure - lâmina de autenticação pass-through](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Passo 2: Verifique as versões dos seus Agentes de Autenticação

Para verificar as versões dos seus Agentes de Autenticação, em cada servidor identificado na etapa anterior, siga estas instruções:

1. Vá ao Painel de **Controlo -> Programas - programas e funcionalidades >** no servidor no local.
2. Se houver uma entrada para "**Microsoft Azure AD Connect Authentication Agent**", não precisa de tomar qualquer medida neste servidor.
3. Se houver uma entrada para "**Microsoft Azure Application Proxy Connector**", precisa de fazer o upgrade manual neste servidor.

![Versão de pré-visualização do Agente de Autenticação](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>As melhores práticas a seguir antes de iniciar a atualização

Antes de atualizar, certifique-se de que tem os seguintes itens no lugar:

1. **Crie a conta**global de administrador apenas em nuvem : Não atualize sem ter uma conta global de administrador apenas na nuvem para usar em situações de emergência em que os seus Agentes de Autenticação Pass-through não estão a funcionar corretamente. Saiba adicionar uma conta global [de administrador apenas](../active-directory-users-create-azure-portal.md)em nuvem. Fazer este passo é fundamental e garante que não se fica trancado fora do seu inquilino.
2.  **Certifique-se de alta disponibilidade**: Se não estiver concluído anteriormente, instale um segundo Agente de Autenticação Autónoma para fornecer alta disponibilidade para pedidos de sessão, utilizando estas [instruções](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Atualizar o Agente de Autenticação no seu servidor Azure AD Connect

Precisa de atualizar o Azure AD Connect antes de atualizar o Agente de Autenticação no mesmo servidor. Siga estes passos tanto nos seus servidores Primários como em encenação do Azure AD Connect:

1. **Upgrade Azure AD Connect**: Siga este [artigo](how-to-upgrade-previous-version.md) e atualize para a mais recente versão Azure AD Connect.
2. **Desinstale a versão de pré-visualização do Agente de Autenticação**: Descarregue [este script PowerShell](https://aka.ms/rmpreviewagent) e execute-o como administrador no servidor.
3. **Faça o download da versão mais recente do Agente de Autenticação (versões 1.5.389.0 ou posterior)**: Inscreva-se no centro de administração do [Azure Ative Directory](https://aad.portal.azure.com) com as credenciais do Administrador Global do seu inquilino. Selecione **Azure Ative Directory -> Azure AD Connect -> agente de descarregamento**de > - Aceite os [termos de serviço](https://aka.ms/authagenteula) e descarregue a versão mais recente do Agente de Autenticação. Também pode descarregar o Agente de Autenticação a partir [daqui](https://aka.ms/getauthagent).
4. **Instale a versão mais recente do Agente de Autenticação**: Execute o executável descarregado no Passo 3. Forneça as credenciais do Administrador Global do seu inquilino quando solicitado.
5. **Verifique se a versão mais recente foi instalada**: Como mostrado anteriormente, vá ao Painel de Controlo **-> Programas - Programas e Funcionalidades >** e verifique se existe uma entrada para " Microsoft**Azure AD Connect Authentication Agent**".

>[!NOTE]
>Se verificar a lâmina de autenticação pass-through no centro de [administração do Diretório Ativo Azure](https://aad.portal.azure.com) depois de completar os passos anteriores, verá duas entradas do Agente de Autenticação por servidor - uma entrada mostrando o Agente de Autenticação como **Ativo** e a outra como **Inativo**. Espera-se que isto se _encontre._ A entrada **Inativa** é automaticamente abandonada após alguns dias.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Atualizar o Agente de Autenticação em outros servidores

Siga estes passos para atualizar agentes de autenticação noutros servidores (onde o Azure AD Connect não está instalado):

1. **Desinstale a versão de pré-visualização do Agente de Autenticação**: Descarregue [este script PowerShell](https://aka.ms/rmpreviewagent) e execute-o como administrador no servidor.
2. **Faça o download da versão mais recente do Agente de Autenticação (versões 1.5.389.0 ou posterior)**: Inscreva-se no centro de administração do [Azure Ative Directory](https://aad.portal.azure.com) com as credenciais do Administrador Global do seu inquilino. Selecione **Azure Ative Directory -> Azure AD Connect -> agente de descarregamento**de > - Aceite os termos de serviço e descarregue a versão mais recente.
3. **Instale a versão mais recente do Agente de Autenticação**: Execute o executável descarregado no Passo 2. Forneça as credenciais do Administrador Global do seu inquilino quando solicitado.
4. **Verifique se a versão mais recente foi instalada**: Como mostrado anteriormente, vá ao Painel de Controlo **-> Programas - programas e Funcionalidades >** e verifique se existe uma entrada chamada Microsoft **Azure AD Connect Authentication Agent**.

>[!NOTE]
>Se verificar a lâmina de autenticação pass-through no centro de [administração do Diretório Ativo Azure](https://aad.portal.azure.com) depois de completar os passos anteriores, verá duas entradas do Agente de Autenticação por servidor - uma entrada mostrando o Agente de Autenticação como **Ativo** e a outra como **Inativo**. Espera-se que isto se _encontre._ A entrada **Inativa** é automaticamente abandonada após alguns dias.

## <a name="next-steps"></a>Passos seguintes
- [**Troubleshoot**](tshoot-connect-pass-through-authentication.md) - Aprenda a resolver problemas comuns com a funcionalidade.
