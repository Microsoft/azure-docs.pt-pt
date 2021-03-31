---
title: 'Azure AD Connect: Começar a utilizar as definições rápidas | Microsoft Docs'
description: Saiba como transferir, instalar e executar o assistente de configuração do Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.assetid: b6ce45fd-554d-4f4d-95d1-47996d561c9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a655f355bb77d937f4daff2f8987769416ebd8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279674"
---
# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>Introdução ao Azure AD Connect utilizando as definições rápidas
Utilizam-se as **Definições Rápidas** do Azure AD Connect se tiver uma topologia de floresta única e a [sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md) para autenticação. **Definições Rápidas** é a opção predefinida e é utilizada para o cenário de implementação mais comum. Estará a apenas alguns cliques da expansão do seu diretório no local para a nuvem.

Antes de começar a instalar o Azure AD Connect, certifique-se de que [transferiu o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) e concluiu os pré-requisitos indicados em [Azure AD Connect: Hardware e pré-requisitos](how-to-connect-install-prerequisites.md).

Se as definições rápidas não coincidirem com a topologia, consulte [documentação relacionada](#related-documentation) para obter outros cenários.

## <a name="express-installation-of-azure-ad-connect"></a>Instalação rápida do Azure AD Connect
Pode ver estes passos em ação na secção [vídeos](#videos).

1. Inicie sessão como administrador local no servidor onde pretende instalar o Azure AD Connect. Deve fazê-lo no servidor que pretende que venha a ser o servidor de sincronização.
2. Navegue até **AzureADConnect.msi** e faça duplo clique.
3. No ecrã de boas-vindas, selecione a caixa em que aceita os termos de licenciamento e clique em **Continuar**.  
4. No ecrã de definições rápidas, clique em **Utilizar definições rápidas**.  
   ![Bem-vindo ao Azure AD Connect](./media/how-to-connect-install-express/express.png)
5. No ecrã Ligar ao Azure AD, introduza o nome de utilizador e palavra-passe de um administrador global do Azure AD. Clique em **Seguinte**.  
   ![Ligar ao Azure AD](./media/how-to-connect-install-express/connectaad.png)  
   Se receber um erro e tiver problemas com a conectividade, veja [Resolver problemas de conectividade](tshoot-connect-connectivity.md).
6. No ecrã Ligar ao AD DS, introduza o nome de utilizador e palavra-passe para uma conta de administrador da empresa. Pode introduzir a parte do domínio no formato NetBios ou FQDN, ou seja, FABRIKAM\administrator ou fabrikam.com\administrator. Clique em **Seguinte**.  
   ![Ligar ao AD DS](./media/how-to-connect-install-express/connectad.png)
7. A página [**Configuração do início de sessão do Azure AD**](plan-connect-user-signin.md#azure-ad-sign-in-configuration) só é mostrada caso não tenha concluído a [verificação dos domínios](../fundamentals/add-custom-domain.md) nos [pré-requisitos](how-to-connect-install-prerequisites.md).
   ![Domínios não verificados](./media/how-to-connect-install-express/unverifieddomain.png)  
   Se esta página for apresentada, reveja todos os domínios marcados como **Não Adicionado** e **Não Verificado**. Certifique-se de que os domínios que utiliza foram verificados no Azure AD. Quando tiver verificado os domínios, clique no símbolo de atualização.
8. No ecrã Preparado para configurar, clique em **Instalar**.
   * Opcionalmente, na página Preparado para configurar, pode desmarcar a caixa de verificação **Inicie o processo de sincronização assim que a configuração for concluída**. Deve desmarcar esta caixa de verificação se pretender efetuar qualquer configuração adicional, como [filtragem](how-to-connect-sync-configure-filtering.md). Se desmarcar esta opção, o assistente configura a sincronização, mas deixa o agendador desativado. Não é executado até que o ative manualmente, [voltando a executar o assistente de instalação](how-to-connect-installation-wizard.md).
   * Deixar a caixa de verificação **Iniciar o processo de sincronização logo que a configuração termine** aciona imediatamente uma sincronização completa para o Azure AD de todos os utilizadores, grupos e contactos.
   * Se tiver o Exchange no Active Directory no local, terá também uma opção para ativar a [**Implementação Híbrida do Exchange**](/exchange/exchange-hybrid). Ative esta opção se planear ter caixas de correio do Exchange simultaneamente na nuvem e no local.
     ![Pronto para configurar o Azure AD Connect](./media/how-to-connect-install-express/readytoconfigure.png)
9. Quando concluir a instalação, clique em **Sair**.
10. Uma vez concluída a instalação, termine e inicie novamente sessão antes de utilizar o Synchronization Service Manager ou Editor de Regras de Sincronização.

## <a name="videos"></a>Vídeos
Para obter um vídeo sobre como utilizar a instalação rápida, consulte:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Express-Settings/player]
>
>

## <a name="next-steps"></a>Passos seguintes
Agora que já tem o Azure AD Connect instalado, pode [verificar a instalação e atribuir licenças](how-to-connect-post-installation.md).

Saiba mais acerca destas funcionalidades que foram ativadas com a instalação: [Atualização automática](how-to-connect-install-automatic-upgrade.md), [Impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](how-to-connect-health-sync.md).

Saiba mais acerca destes tópicos comuns: [agendador e como acionar a sincronização](how-to-connect-sync-feature-scheduler.md).

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).

## <a name="related-documentation"></a>Documentação relacionada

| Tópico | Ligação |
| --- | --- |
| Visão geral do Azure AD Connect | [Integrar os diretórios no local com o Azure Active Directory](whatis-hybrid-identity.md)
| Instalar utilizando as definições personalizadas | [Instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md) |
| Atualização do DirSync | [Atualizar da ferramenta de sincronização do Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md)|
| Contas utilizadas para a instalação | [Mais informações acerca das credenciais e permissões do Azure AD Connect](reference-connect-accounts-permissions.md) |