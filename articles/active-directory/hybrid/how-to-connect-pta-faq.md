---
title: 'Azure AD Connect: Autenticação pass-through - Perguntas frequentes / Microsoft Docs'
description: Respostas a perguntas frequentes sobre autenticação de passagem de diretório ativo azure
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
ms.date: 03/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd923a47c49bfa7a6ab16e822a80c8e7f4f9a3e0
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096055"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Autenticação de passagem por passe por diretório ativo azure: Perguntas frequentes

Este artigo aborda frequentemente perguntas sobre a Autenticação Pass-through do Azure Ative Directory (Azure AD). Continue a verificar se há conteúdo atualizado.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Qual dos métodos para iniciar sessão no Azure AD, Na autenticação pass-through, na sincronização de hash de senha e nos Serviços da Federação de Diretório Ativo (AD FS), devo escolher?

Reveja [este guia](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) para uma comparação dos vários métodos de entrada em Anúncios Azure e como escolher o método de entrada certo para a sua organização.

## <a name="is-pass-through-authentication-a-free-feature"></a>A Autenticação Pass-through é uma funcionalidade gratuita?

A Autenticação Pass-through é uma funcionalidade gratuita. Não precisa de edições pagas de Azure AD para usá-lo.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloud-and-the-microsoft-azure-government-cloud"></a>A autenticação pass-through está disponível na nuvem da [Microsoft Azure Germany](https://www.microsoft.de/cloud-deutschland) e na [nuvem do Governo Microsoft Azure?](https://azure.microsoft.com/features/gov/)

Não. A Autenticação Pass-through só está disponível na instância mundial de Azure AD.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>O [Acesso Condicional](../active-directory-conditional-access-azure-portal.md) funciona com a Autenticação Pass-through?

Sim. Todas as capacidades de Acesso Condicional, incluindo a Autenticação Multi-Factor Azure, trabalham com a Autenticação Pass-through.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>A autenticação pass-through suporta "ID alternativo" como nome de utilizador, em vez de "userPrincipalName"?
O signo utilizando um valor não UPN, como um e-mail alternativo, está atualmente a ser testado em pré-visualização privada para autenticação pass-through (PTA) e sincronização de hash password (PHS).

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>A sincronização de hash de palavra-passe funciona como um recuo para a Autenticação Pass-through?

Não. A autenticação pass-through _não_ falha automaticamente a sincronização de hash por palavra-passe. Para evitar falhas de entrada no utilizador, deve configurar a Autenticação Pass-through para [uma elevada disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>O que acontece quando mudo de sincronização de hash de senha para autenticação pass-through?

Quando utiliza o Azure AD Connect para mudar o método de entrada de palavra-passe para a autenticação pass-through, a Autenticação Pass-through torna-se o método principal de entrada para os seus utilizadores em domínios geridos. Por favor, note que todas as hashes de senha de todos os utilizadores que foram previamente sincronizadas por sincronização de hash de palavra-passe permanecem armazenadas em Azure AD.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Posso instalar um conector de proxy de [aplicação AD Azure](../manage-apps/application-proxy.md) no mesmo servidor que um Agente de Autenticação Pass-through?

Sim. As versões remarcadas do Agente de Autenticação Pass-through, versão 1.5.193.0 ou posterior, suportam esta configuração.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Que versões do Azure AD Connect e do Agente de Autenticação Pass-through precisa?

Para que esta funcionalidade funcione, precisa da versão 1.1.750.0 ou posterior para o Azure AD Connect e 1.5.193.0 ou mais tarde para o Agente de Autenticação Pass-through. Instale todo o software nos servidores com o Windows Server 2012 R2 ou mais tarde.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>O que acontece se a palavra-passe do meu utilizador tiver expirado e tentarem iniciar sessão utilizando a Autenticação Pass-through?

Se tiver configurado a reescrita da [palavra-passe](../authentication/concept-sspr-writeback.md) para um utilizador específico, e se o utilizador fizer o sinal de inscrição utilizando a Autenticação Pass-through, pode alterar ou redefinir as suas palavras-passe. As palavras-passe são redigidas para o Diretório Ativo no local, como esperado.

Se não configurar a reescrita de palavra-passe para um utilizador específico ou se o utilizador não tiver uma licença Azure AD válida atribuída, o utilizador não pode atualizar a sua palavra-passe na nuvem. Não podem atualizar a sua palavra-passe, mesmo que a sua senha tenha expirado. Em vez disso, o utilizador vê esta mensagem: "A sua organização não lhe permite atualizar a sua palavra-passe neste site. Atualize-o de acordo com o método recomendado pela sua organização, ou pergunte ao seu administrador se precisa de ajuda." O utilizador ou o administrador devem redefinir a sua palavra-passe no Diretório Ativo no local.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Como é que a Autenticação Pass-through te protege contra ataques de senha seletos?

[Leia informações sobre smart lockout](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>O que comunicam os Agentes de Autenticação Pass-through através das portas 80 e 443?

- Os Agentes de Autenticação fazem pedidos HTTPS sobre a porta 443 para todas as operações de recurso.
- Os Agentes de Autenticação fazem pedidos http sobre a porta 80 para descarregar as listas de revogação de certificados SSL (CRLs).

     >[!NOTE]
     >As atualizações recentes reduziram o número de portas que a funcionalidade necessita. Se tiver versões mais antigas do Azure AD Connect ou do Agente de Autenticação, mantenha estas portas abertas também: 5671, 8080, 9090, 9091, 9350, 9352 e 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Os Agentes de Autenticação Pass-through podem comunicar através de um servidor de procuração web de saída?

Sim. Se o Web Proxy Auto-Discovery (WPAD) estiver ativado no seu ambiente no local, os Agentes de Autenticação tentam localizar e utilizar automaticamente um servidor de procuração web na rede.

Se não tiver WPAD no seu ambiente, pode adicionar informações de procuração (como mostrado abaixo) para permitir que um Agente de Autenticação Pass-through se comunique com a AD Azure:
- Configure informações de procuração no Internet Explorer antes de instalar o Agente de Autenticação Pass-through no servidor. Isto permitir-lhe-á concluir a instalação do Agente de Autenticação, mas continuará a aparecer como **Inativo** no portal Doin.
- No servidor, navegue para "C:\Program Files\Microsoft Azure AD Connect Authentication Agent".
- Editar o ficheiro de configuração "AzureADConnectAuthenticationAgentService" e adicionar as seguintes linhas (substitua "http\://contosoproxy.com:8080" com o seu endereço de procuração real):

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Posso instalar dois ou mais agentes de autenticação pass-through no mesmo servidor?

Não, só pode instalar um Agente de Autenticação Pass-through num único servidor. Se pretender configurar a Autenticação Pass-through para uma elevada disponibilidade, [siga as instruções aqui](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Tenho de renovar manualmente os certificados utilizados pelos Agentes de Autenticação Pass-through?

A comunicação entre cada Agente de Autenticação Pass-through e Azure AD é assegurada através da autenticação baseada em certificados. Estes [certificados são automaticamente renovados a cada poucos meses pela Azure AD](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents). Não há necessidade de renovar manualmente estes certificados. Pode limpar os certificados expirados mais antigos, conforme necessário.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Como posso remover um agente de autenticação pass-through?

Enquanto um Agente de Autenticação Pass-through estiver em execução, permanece ativo e continuamente lida com pedidos de inscrição do utilizador. Se pretender desinstalar um Agente de Autenticação, vá ao Painel de **Controlo -> Programas -> Programas e Funcionalidades** e desinstale tanto o **Microsoft Azure AD Connect Authentication Agent como** os programas de atualização do Agente de **Ligação AD Microsoft Azure.**

Se verificar a lâmina de autenticação pass-through no centro de [administração do Diretório Ativo Azure](https://aad.portal.azure.com) depois de completar o passo anterior, verá o Agente de Autenticação a mostrar-se **inativo**. Espera-se que isto se _encontre._ O Agente de Autenticação é automaticamente retirado da lista após alguns dias.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Já uso AD FS para entrar no Azure AD. Como o mudo para autenticação pass-through?

Se está a migrar de AD FS (ou outras tecnologias da federação) para a Autenticação Pass-through, recomendamos vivamente que siga o nosso guia de implementação detalhado publicado [aqui](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true).

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Posso usar a autenticação pass-through num ambiente de Diretório Ativo multiflorestal?

Sim. Ambientes multiflorestais são apoiados se houver fundos florestais entre as suas florestas de Diretório Ativo e se o encaminhamento do sufixo de nome estiver corretamente configurado.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>A Autenticação Pass-through fornece o equilíbrio de carga em vários Agentes de Autenticação?

Não, instalar vários agentes de autenticação pass-through garante apenas [uma elevada disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Não proporciona um equilíbrio determinístico de carga entre os Agentes de Autenticação. Qualquer Agente de Autenticação (aleatório) pode processar um pedido de inscrição do utilizador específico.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Quantos agentes de autenticação pass-through preciso de instalar?

Instalar vários agentes de autenticação pass-through garante [uma elevada disponibilidade.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) Mas não proporciona um equilíbrio determinístico de carga entre os Agentes de Autenticação.

Considere o pico e a carga média de pedidos de inscrição que espera ver no seu inquilino. Como referência, um único Agente de Autenticação pode lidar com 300 a 400 autenticações por segundo num CPU padrão de 4 núcleos, servidor RAM de 16 GB.

Para estimar o tráfego da rede, utilize as seguintes orientações de dimensionamento:
- Cada pedido tem um tamanho de carga útil de bytes (0,5K + 1K * num_of_agents) ; ou é, dados da AD Azure para o Agente de Autenticação. Aqui, "num_of_agents" indica o número de Agentes de Autenticação registados no seu inquilino.
- Cada resposta tem um tamanho de carga útil de bytes de 1K; ou é, dados do Agente de Autenticação para a AD Azure.

Para a maioria dos clientes, dois ou três Agentes de Autenticação no total são suficientes para uma elevada disponibilidade e capacidade. Deve instalar agentes de autenticação perto dos seus controladores de domínio para melhorar a latência de acesso.

>[!NOTE]
>Existe um limite de sistema de 40 Agentes de Autenticação por inquilino.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Posso instalar o primeiro Agente de Autenticação Pass-through num servidor que não seja aquele que executa o Azure AD Connect?

Não, este cenário _não_ é apoiado.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Por que preciso de uma conta de Administrador Global só para permitir a autenticação pass-through?

Recomenda-se que ative ou desative a Autenticação Pass-through utilizando uma conta global de administrador apenas na nuvem. Saiba adicionar uma conta global [de administrador apenas](../active-directory-users-create-azure-portal.md)em nuvem. Fazê-lo desta forma garante que não seja trancado fora do seu inquilino.

## <a name="how-can-i-disable-pass-through-authentication"></a>Como posso desativar a autenticação pass-through?

Reexecutar o assistente Azure AD Connect e alterar o método de entrada do utilizador da Autenticação Pass-through para outro método. Esta alteração desativa a autenticação pass-through no inquilino e desinstala o Agente de Autenticação do servidor. Tem de desinstalar manualmente os Agentes de Autenticação dos outros servidores.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>O que acontece quando desinstalo um agente de autenticação pass-through?

Se desinstalar um Agente de Autenticação Pass-through a partir de um servidor, faz com que o servidor deixe de aceitar pedidos de sessão. Para evitar quebrar a capacidade de inscrição do utilizador no seu inquilino, certifique-se de que tem outro Agente de Autenticação a funcionar antes de desinstalar um Agente de Autenticação Pass-through.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Tenho um inquilino mais velho que foi originalmente criado usando AD FS.  Recentemente migramos para ptA mas agora não estamos vendo as nossas mudanças UPN sincronizando para Azure AD.  Porque é que as nossas alterações da UPN não estão a ser sincronizadas?

R: Nas seguintes circunstâncias, as alterações da UPN no local não podem sincronizar se:

- O seu inquilino Da Azure AD foi criado antes de 15 de junho de 2015
- Inicialmente foi federado com o seu inquilino Azure AD usando AD FS para autenticação
- Passou a ter utilizadores geridos usando PTA como autenticação

Isto porque o comportamento predefinido dos inquilinos criados antes de 15 de junho de 2015 foi bloquear as alterações da UPN.  Se precisar de desbloquear alterações UPN, tem de executar o seguinte cmdlt PowerShell:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

Os inquilinos criados após 15 de junho de 2015 têm o comportamento padrão de sincronizar as mudanças da UPN.   



## <a name="next-steps"></a>Passos seguintes
- [Limitações atuais](how-to-connect-pta-current-limitations.md): Saiba quais os cenários suportados e quais não são.
- [Início rápido](how-to-connect-pta-quick-start.md): Levantar-se e correr na Autenticação de Passagem De AD Azure.
- [Migrar de AD FS para a Autenticação Pass-through](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) - Um guia detalhado para migrar de AD FS (ou outras tecnologias da federação) para a Autenticação Pass-through.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md): Aprenda a configurar a capacidade de bloqueio inteligente no seu inquilino para proteger as contas do utilizador.
- [Mergulho profundo técnico](how-to-connect-pta-how-it-works.md): Compreenda como funciona a função de Autenticação Pass-through.
- [Resolução de problemas](tshoot-connect-pass-through-authentication.md): Aprenda a resolver problemas comuns com a função de Autenticação Pass-through.
- [Mergulho profundo de segurança](how-to-connect-pta-security-deep-dive.md): Obtenha informações técnicas profundas sobre a função de autenticação pass-through.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Saiba mais sobre esta funcionalidade complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum de Diretório Ativo Azure para apresentar novos pedidos de funcionalidades.

