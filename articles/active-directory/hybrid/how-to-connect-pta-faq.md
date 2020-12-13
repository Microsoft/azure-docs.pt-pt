---
title: 'Azure AD Connect: Autenticação pass-through - Perguntas frequentes Microsoft Docs'
description: Respostas a perguntas frequentes sobre autenticação passativa de diretório ativo Azure
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
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75f797c40a276323cea9983c5340d2d854160c83
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368482"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Autenticação pass-through do Azure Ative Directory: Perguntas frequentes

Este artigo aborda perguntas frequentes sobre a autenticação pass-through do Azure Ative Directory (Azure AD). Continue a verificar se há conteúdo atualizado.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Qual dos métodos para iniciar sedutação em Azure AD, Autenticação Pass-through, sincronização de hash de palavra-passe e Serviços da Federação de Diretório Ativo (AD FS), devo escolher?

[Reveja este guia](./choose-ad-authn.md) para uma comparação dos vários métodos de inscrição Azure AD e como escolher o método de inscrição certo para a sua organização.

## <a name="is-pass-through-authentication-a-free-feature"></a>A Autenticação Pass-through é uma funcionalidade gratuita?

A autenticação pass-through é uma funcionalidade gratuita. Não precisa de edições pagas da Azure AD para usá-lo.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>O [Acesso Condicional](../conditional-access/overview.md) funciona com autenticação pass-through?

Sim. Todas as capacidades de Acesso Condicional, incluindo a autenticação multi-factor Azure AD, funcionam com a autenticação pass-through.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>O Substituto autenticação suporta "ID alternativo" como nome de utilizador, em vez de "userPrincipalName"?
Sim, o sing-in utilizando um valor não UPN, como um e-mail alternativo, é suportado tanto para a autenticação pass-through (PTA) como para a sincronização de haxixe de palavra-passe (PHS). Para mais informações sobre [o ID de Login Alternativo.](../authentication/howto-authentication-use-email-signin.md)

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>A sincronização de hash de palavra-passe funciona como um recuo para a autenticação pass-through?

Não. A autenticação pass-through _não_ falha automaticamente na sincronização de hash de palavra-passe. Para evitar falhas de inscrição no utilizador, deve configurar a autenticação pass-through para [uma elevada disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>O que acontece quando mudo de sincronização de hash de palavra-passe para autenticação pass-through?

Quando utilizar o Azure AD Connect para mudar o método de entrada de palavras-passe da sincronização de hash de palavra-passe para a autenticação pass-through, a Autenticação Pass-through torna-se o principal método de entrada para os seus utilizadores em domínios geridos. Por favor, note que todos os hashes de palavra-passe dos utilizadores que foram previamente sincronizados por sincronização de hash de palavra-passe permanecem armazenados no Azure AD.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Posso instalar um conector [proxy de aplicação AD AZure](../manage-apps/application-proxy.md) no mesmo servidor que um Agente de Autenticação Pass-through?

Sim. As versões remarcadas do Agente de Autenticação Pass-through, versão 1.5.193.0 ou posterior, suportam esta configuração.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Que versões do Azure AD Connect e do Agente de Autenticação Pass-through precisa?

Para que esta funcionalidade funcione, precisa da versão 1.1.750.0 ou posterior para Azure AD Connect e 1.5.193.0 ou mais tarde para o Agente de Autenticação Pass-through. Instale todo o software em servidores com o Windows Server 2012 R2 ou mais tarde.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>O que acontece se a palavra-passe do meu utilizador tiver expirado e tentarem entrar usando a Autenticação Pass-through?

Se tiver configurado [a gravação de palavra-passe](../authentication/concept-sspr-writeback.md) para um utilizador específico, e se o utilizador entrar usando a Autenticação Pass-through, pode alterar ou redefinir as suas palavras-passe. As palavras-passe são escritas de volta para o Ative Directory no local, como esperado.

Se não tiver configurado a gravação de palavra-passe para um utilizador específico ou se o utilizador não tiver uma licença AD Azure válida atribuída, o utilizador não pode atualizar a sua palavra-passe na nuvem. Não podem atualizar a palavra-passe, mesmo que a palavra-passe tenha expirado. Em vez disso, o utilizador vê esta mensagem: "A sua organização não permite que atualize a sua palavra-passe neste site. Atualize-o de acordo com o método recomendado pela sua organização, ou pergunte ao seu administrador se precisa de ajuda." O utilizador ou o administrador devem redefinir a sua palavra-passe no Ative Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Como é que a Autenticação Pass-through o protege contra ataques de senhas de força bruta?

[Leia informações sobre o Smart Lockout](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>O que comunicam os Agentes de Autenticação Pass-through nas portas 80 e 443?

- Os Agentes de Autenticação estão a fazer pedidos HTTPS sobre a porta 443 para todas as operações de recurso.
- Os Agentes de Autenticação fazem pedidos HTTP sobre a porta 80 para descarregar as listas de revogação de certificados TLS/SSL (CRLs).

     >[!NOTE]
     >As recentes atualizações reduziram o número de portas que a funcionalidade necessita. Se tiver versões mais antigas do Azure AD Connect ou do Agente de Autenticação, mantenha estas portas abertas também: 5671, 8080, 9090, 9091, 9350, 9352 e 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Os Agentes de Autenticação Pass-through podem comunicar através de um servidor de procuração web de saída?

Sim. Se o Web Proxy Auto-Discovery (WPAD) estiver ativado no seu ambiente no local, os Agentes de Autenticação tentam automaticamente localizar e utilizar um servidor de procuração web na rede.

Se não tiver WPAD no seu ambiente, pode adicionar informações de procuração (como mostrado abaixo) para permitir que um Agente de Autenticação Pass-through comunique com a Azure AD:
- Configure informações de procuração no Internet Explorer antes de instalar o Agente de Autenticação Pass-through no servidor. Isto permitir-lhe-á completar a instalação do Agente de Autenticação, mas continuará a aparecer como **Inativo** no portal Admin.
- No servidor, navegue para "C:\Program Files\Microsoft Azure AD Connect Authentication Agent".
- Editar o ficheiro de configuração "AzureADConnectAuthenticationAgentService" e adicionar as seguintes linhas (substituir \: "http//contosoproxy.com:8080" pelo seu endereço de procuração real):

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

Não, só é possível instalar um Agente de Autenticação Pass-through num único servidor. Se pretender configurar a autenticação pass-through para uma elevada disponibilidade, [siga as instruções aqui](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Tenho de renovar manualmente os certificados utilizados pelos Agentes de Autenticação Pass-through?

A comunicação entre cada Agente de Autenticação Pass-through e Azure AD é assegurada através da autenticação baseada em certificados. Estes [certificados são automaticamente renovados a cada poucos meses pela Azure AD](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents). Não há necessidade de renovar manualmente estes certificados. Pode limpar os certificados expirados mais antigos, conforme necessário.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Como posso remover um Agente de Autenticação Pass-through?

Enquanto um Agente de Autenticação Pass-through estiver em funcionamento, permanece ativo e lida continuamente com os pedidos de inscrição do utilizador. Se pretender desinstalar um Agente de Autenticação, aceda a programas e programas de > > de controlo do Painel de Controlo e funcionalidades de > e **desinstale** tanto o Agente de **Autenticação AD AD do Microsoft Azure** como os programas **do Microsoft Azure AD Connect Agent Updater.**

Se verificar a lâmina de autenticação pass-through no centro de administração do [Diretório Ativo Azure](https://aad.portal.azure.com) após completar o passo anterior, verá o Agente de Autenticação a mostrar-se **inativo**. Isto é _esperado._ O Agente de Autenticação é automaticamente retirado da lista após 10 dias.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Já uso AD FS para entrar no Azure AD. Como posso trocá-lo por autenticação pass-through?

Se estiver a migrar de FS AD (ou outras tecnologias da federação) para a Autenticação Pass-through, recomendamos vivamente que siga o nosso guia de implementação detalhado publicado [aqui.](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true)

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Posso usar a autenticação pass-through num ambiente multi-forest Ative Directory?

Sim. Ambientes multi-florestais são apoiados se existirem fundos florestais (bidirecionais) entre as suas florestas de Diretório Ativo e se o encaminhamento de sufixo de nome estiver corretamente configurado.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>A autenticação pass-through fornece o equilíbrio de carga entre vários agentes de autenticação?

Não, a instalação de vários Agentes de Autenticação Pass-through garante apenas [uma elevada disponibilidade.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) Não proporciona um equilíbrio de carga determinístico entre os Agentes de Autenticação. Qualquer Agente de Autenticação (aleatoriamente) pode processar um determinado pedido de inscrição do utilizador.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Quantos agentes de autenticação pass-through preciso de instalar?

A instalação de vários Agentes de Autenticação Pass-through garante [uma elevada disponibilidade.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) Mas não proporciona um equilíbrio de carga determinista entre os Agentes de Autenticação.

Considere o pico e a carga média de pedidos de inscrição que espera ver no seu inquilino. Como referência, um único Agente de Autenticação pode lidar com 300 a 400 autenticações por segundo num CPU padrão de 4 núcleos, servidor RAM de 16 GB.

Para estimar o tráfego da rede, utilize as seguintes orientações de dimensionamento:
- Cada pedido tem um tamanho de carga útil de bytes (0,5K + 1K * num_of_agents) ; ou seja, dados da Azure AD ao Agente de Autenticação. Aqui, "num_of_agents" indica o número de Agentes de Autenticação registados no seu inquilino.
- Cada resposta tem um tamanho de carga útil de 1K bytes; ou seja, dados do Agente de Autenticação para Azure AD.

Para a maioria dos clientes, dois ou três Agentes de Autenticação no total são suficientes para uma elevada disponibilidade e capacidade. Deve instalar agentes de autenticação perto dos seus controladores de domínio para melhorar a latência de inscrição.

>[!NOTE]
>Existe um limite de sistema de 40 Agentes de Autenticação por inquilino.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Por que preciso de uma conta de Administrador Global só em nuvem para ativar a autenticação pass-through?

Recomenda-se que ative ou desative a autenticação pass-through utilizando uma conta de Administrador Global apenas na nuvem. Saiba mais [sobre a adição de uma conta de Administrador Global apenas na nuvem.](../fundamentals/add-users-azure-active-directory.md) Fazê-lo desta forma garante que não fiques trancado fora do teu inquilino.

## <a name="how-can-i-disable-pass-through-authentication"></a>Como posso desativar a autenticação pass-through?

Repercuta o assistente Azure AD Connect e altere o método de inscrição do utilizador de Autenticação Pass-through para outro método. Esta alteração desativa a autenticação pass-through no arrendatário e desinstala o Agente de Autenticação do servidor. Tem de desinstalar manualmente os Agentes de Autenticação dos outros servidores.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>O que acontece quando desinstalo um Agente de Autenticação Pass-through?

Se desinstalar um Agente de Autenticação Pass-through a partir de um servidor, faz com que o servidor deixe de aceitar pedidos de inscrição. Para evitar quebrar a capacidade de inscrição do utilizador no seu inquilino, certifique-se de que tem outro Agente de Autenticação a funcionar antes de desinstalar um Agente de Autenticação Pass-through.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Tenho um inquilino mais velho que foi originalmente configurado usando AD FS.  Recentemente migramos para a PTA, mas agora não estamos a ver as nossas alterações da UPN sincronizadas com a Azure AD.  Porque é que as nossas alterações na UPN não estão a ser sincronizadas?

R: Nas seguintes circunstâncias, as alterações da UPN no local podem não sincronizar se:

- Seu inquilino Azure AD foi criado antes de 15 de junho de 2015
- Inicialmente foi federado com o seu inquilino Azure AD usando AD FS para autenticação
- Mudou para ter utilizadores geridos usando PTA como autenticação

Isto porque o comportamento padrão dos inquilinos criados antes de 15 de junho de 2015 foi bloquear as alterações da UPN.  Se precisar de desmesuar alterações upn, tem de executar o seguinte cmdlt PowerShell:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

Os inquilinos criados após 15 de junho de 2015 têm o comportamento padrão de sincronização das alterações da UPN.   



## <a name="next-steps"></a>Passos seguintes
- [Limitações atuais](how-to-connect-pta-current-limitations.md): Saiba quais os cenários que são apoiados e quais não são.
- [Início rápido](how-to-connect-pta-quick-start.md): Levante-se e corra na Autenticação Pass-through Azure.
- [Migrar de AD FS para Autenticação Pass-through](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) - Um guia detalhado para migrar de FS AD (ou outras tecnologias da federação) para a Autenticação Pass-through.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md): Saiba como configurar a capacidade de Bloqueio Inteligente no seu inquilino para proteger as contas do utilizador.
- [Mergulho técnico profundo](how-to-connect-pta-how-it-works.md): Compreenda como funciona a função de autenticação pass-through.
- [Resolução de problemas](tshoot-connect-pass-through-authentication.md): Aprenda a resolver problemas comuns com a função De autenticação Pass-through.
- [Mergulho profundo de](how-to-connect-pta-security-deep-dive.md)segurança : Obtenha informações técnicas profundas sobre a função de autenticação pass-through.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Saiba mais sobre esta característica complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Azure Ative Directory Forum para apresentar novos pedidos de funcionalidades.
