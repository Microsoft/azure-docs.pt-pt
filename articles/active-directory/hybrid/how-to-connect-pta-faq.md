---
title: 'Azure AD Connect: Autenticação pass-through - perguntas mais frequentes | Documentos da Microsoft'
description: Respostas às perguntas mais frequentes sobre a autenticação de pass-through do Azure Active Directory
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
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b00afa3d1001ee7c48997e41fd6042763bcc9aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60347622"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Autenticação pass-through do Azure Active Directory: Perguntas mais frequentes

Este artigo aborda perguntas mais frequentes sobre a autenticação de pass-through do Azure Active Directory (Azure AD). Manter a verificação de volta para o conteúdo atualizado.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Que os métodos para iniciar sessão Azure AD, autenticação pass-through, palavra-passe de hash sincronização e serviços de Federação do Active Directory (AD FS), devo escolher?

Revisão [este guia](https://docs.microsoft.com/azure/security/azure-ad-choose-authn) para uma comparação entre os vários Azure AD início de sessão métodos e como escolher o método certo início de sessão para a sua organização.

## <a name="is-pass-through-authentication-a-free-feature"></a>É uma funcionalidade gratuita de autenticação pass-through?

Autenticação pass-through é um recurso gratuito. Não é necessário qualquer nas edições pagas do Azure AD para utilizá-lo.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpswwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Está disponível na autenticação pass-through a [cloud do Microsoft Azure Alemanha](https://www.microsoft.de/cloud-deutschland) e o [cloud do Microsoft Azure Government](https://azure.microsoft.com/features/gov/)?

Não. Autenticação pass-through só está disponível na instância do Azure AD em todo o mundo.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>Faz [acesso condicional](../active-directory-conditional-access-azure-portal.md) funcionam com a autenticação pass-through?

Sim. Todas as capacidades de acesso condicional, incluindo multi-factor Authentication, trabalham com a autenticação pass-through.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Autenticação pass-through suporta "ID alternativo" como o nome de utilizador, em vez de "userPrincipalName"?

Sim, suporta a autenticação pass-through `Alternate ID` como o nome de utilizador quando configurado no Azure AD Connect. Como um pré-requisito, tem de sincronizar o Active Directory no local do Azure AD Connect `UserPrincipalName` de atributo para o Azure AD. Para obter mais informações, consulte [instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md). Nem todas as aplicações do Office 365 suportam `Alternate ID`. Consulte a declaração de suporte de documentação do aplicativo específico.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Sincronização de hash de palavra-passe atuar como contingência para autenticação pass-through?

Não. Autenticação pass-through _não_ automaticamente a ativação pós-falha para a sincronização de hash de palavra-passe. Para evitar falhas de início de sessão de utilizador, deve configurar a autenticação pass-through para [elevada disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>O que acontece quando eu mude de sincronização de hash de palavra-passe para autenticação pass-through?

Quando utiliza o Azure AD Connect para mudar o método início de sessão de sincronização de hash de palavra-passe para autenticação pass-through, autenticação pass-through torna-se o método primário início de sessão para os utilizadores em domínios geridos. Tenha em atenção que os hashes de palavra-passe de todos os utilizadores que anteriormente foram sincronizados com a sincronização de hash de palavra-passe permanecem armazenados no Azure AD.

## <a name="can-i-install-an-azure-ad-application-proxymanage-appsapplication-proxymd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Pode instalar um [Proxy de aplicações do Azure AD](../manage-apps/application-proxy.md) conector no mesmo servidor como um agente de autenticação pass-through?

Sim. As versões remodeladas do agente de autenticação pass-through, versão 1.5.193.0 ou posterior, suportar esta configuração.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Quais as versões do Azure AD Connect e o agente de autenticação pass-through precisa?

Para esta funcionalidade funcione, tem de versão 1.1.750.0 ou posterior para o Azure AD Connect e 1.5.193.0 ou posterior para o agente de autenticação pass-through. Instale todo o software em servidores com o Windows Server 2012 R2 ou posterior.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>O que acontece se a palavra-passe meu utilizador expirou e eles tentam iniciar sessão com a autenticação pass-through?

Se tiver configurado [repetição de escrita de palavra-passe](../authentication/concept-sspr-writeback.md) para um utilizador específico, e se o utilizador inicia sessão utilizando a autenticação pass-through, pode alterar ou repor as palavras-passe. As palavras-passe são escritas de volta para o Active Directory no local como esperado.

Se não tiver configurado a repetição de escrita de palavra-passe para um utilizador específico, ou se o utilizador não tem um Azure AD válido licença atribuída, o utilizador não é possível atualizar a palavra-passe na cloud. Eles não é possível atualizar a palavra-passe, mesmo que a palavra-passe expirou. O utilizador vê, em vez disso, esta mensagem: "Sua organização não permite que atualizar a palavra-passe neste site. A atualização, de acordo com o método recomendado pela sua organização, ou peça ao seu administrador se precisar de ajuda". O utilizador ou o administrador terá de repor a palavra-passe no Active Directory no local.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Como autenticação pass-through protege contra ataques de força bruta a senhas?

[Ler informações sobre o bloqueio inteligente](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>O que a agentes de autenticação pass-through a comunicar através das portas 80 e 443?

- Os agentes de autenticação fazer pedidos HTTPS através da porta 443 para todas as operações de funcionalidade.
- Os agentes de autenticação de fazer solicitações HTTP através da porta 80 para transferir as listas de revogação de certificado SSL (CRL).

     >[!NOTE]
     >Atualizações recentes reduzimos o número de portas de que a funcionalidade requer. Se tiver versões mais antigas do Azure AD Connect ou o agente de autenticação, mantém também estas portas abertas: 5671, 8080, 9090, 9091, 9350, 9352 e 10100 10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Os agentes de autenticação pass-through possam comunicar através de um servidor de proxy da web de saída?

Sim. Se a deteção automática de Proxy da Web (WPAD) estiver ativada no seu ambiente no local, os agentes de autenticação automaticamente tente localizar e utilizar um servidor web proxy na rede.

Se não tiver o WPAD no seu ambiente, pode adicionar informações de proxy (como mostrado abaixo) para permitir que um agente de autenticação pass-through comunicar com o Azure AD:
- Configure as informações de proxy no Internet Explorer antes de instalar o agente de autenticação pass-through no servidor. Isso permitirá que concluir a instalação do agente de autenticação, mas ainda será mostrado como **Inactive** no portal de administração.
- No servidor, navegue para "C:\Program Files\Microsoft Azure AD Connect o agente de autenticação".
- Edite o ficheiro de configuração de "AzureADConnectAuthenticationAgentService" e adicione as seguintes linhas (substitua "http\:/ / contosoproxy.com:8080" com o seu endereço de proxy real):

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

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Pode instalar dois ou mais agentes de autenticação pass-through no mesmo servidor?

Não, só pode instalar um agente de autenticação pass-through num único servidor. Se quiser configurar a autenticação pass-through para elevada disponibilidade, [siga as instruções aqui](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>É necessário que renovar manualmente certificados utilizados pelos agentes de autenticação pass-through?

A comunicação entre cada agente de autenticação pass-through e o Azure AD é protegida através da autenticação baseada em certificados. Estes [certificados são automaticamente renovados algumas vezes ao mês pelo Azure AD](how-to-connect-pta-security-deep-dive.md#operational-security-of -the-authentication-agents). Não é necessário para renovar manualmente estes certificados. Pode limpar os antigos certificados expirados, conforme necessário.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Como posso remover um agente de autenticação pass-through?

Desde que um agente de autenticação pass-through está em execução, permanece ativa e continuamente processa pedidos de início de sessão do utilizador. Se quiser desinstalar um agente de autenticação, aceda a **painel de controlo -> programas -> programas e funcionalidades** e desinstalar o **agente do Microsoft Azure AD Connect autenticação** e o  **Atualizador de agente ligar do Microsoft Azure AD** programas.

Se verificar o painel de autenticação pass-through a [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) depois de concluir o passo anterior, verá o agente de autenticação que mostra como **Inactive**. Isto é _esperado_. O agente de autenticação é automaticamente removido da lista após alguns dias.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Já posso utilizar o AD FS para iniciar sessão com o Azure AD. Como posso alterná-lo para a autenticação pass-through?

Se estiver a migrar do AD FS (ou outras tecnologias de Federação) para autenticação pass-through, recomendamos vivamente que siga o nosso guia de implementação detalhados publicado [aqui](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true).

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Pode utilizar autenticação pass-through no ambiente de várias florestas do Active Directory?

Sim. Ambientes de várias florestas são suportadas se existem relações de confiança de floresta entre florestas do Active Directory e se o encaminhamento de sufixo de nome está configurado corretamente.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>Autenticação pass-through fornece balanceamento de carga entre vários agentes de autenticação?

Não, instalar vários agentes de autenticação pass-through garante que apenas [elevada disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Ele não fornece determinística balanceamento de carga entre os agentes de autenticação. Qualquer agente de autenticação (aleatoriamente) pode processar um pedido de início de início de sessão do utilizador em particular.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Quantos agentes de autenticação pass-through é necessário instalar?

Instalar agentes de autenticação pass-through vários garante [elevada disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). No entanto, ele não fornece determinística balanceamento de carga entre os agentes de autenticação.

Considere as horas de pico e a carga média de pedidos de início de sessão que pretende ver no seu inquilino. Como um parâmetro de comparação, um único agente de autenticação pode processar autenticações de 300 a 400 por segundo numa CPU de 4 núcleos padrão, servidor de RAM de 16 GB.

Para estimar o tráfego de rede, utilize as seguintes orientações de dimensionamento:
- Cada pedido tem um tamanho de payload de (0,5 k + 1 K * num_of_agents) bytes; Por exemplo, dados do Azure AD para o agente de autenticação. Aqui, "num_of_agents" indica que o número de agentes de autenticação registrado no seu inquilino.
- Cada resposta tem um tamanho de payload de bytes de 1K; Por exemplo, dados do agente de autenticação para o Azure AD.

Para a maioria dos clientes, os agentes de autenticação de dois ou três no total são suficientes para elevada disponibilidade e capacidade. Deve instalar agentes de autenticação perto dos seus controladores de domínio para melhorar a latência de início de sessão.

>[!NOTE]
>Existe um limite de sistema de 40 agentes de autenticação por inquilino.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Pode instalar o agente de autenticação pass-through primeiro num servidor diferente daquele que é executado o Azure AD Connect?

Não, este cenário é _não_ suportado.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Por que razão necessito de uma conta de Administrador Global apenas na cloud para ativar a autenticação pass-through?

Recomenda-se que ative ou desative a autenticação pass-through com uma conta de Administrador Global apenas na cloud. Saiba mais sobre [adicionar uma conta de Administrador Global apenas na cloud](../active-directory-users-create-azure-portal.md). Fazê-lo desta forma, garante que não fica bloqueado fora do seu inquilino.

## <a name="how-can-i-disable-pass-through-authentication"></a>Como desativar a autenticação pass-through?

Executar novamente o Assistente do Azure AD Connect e altere o método de início de sessão do utilizador de autenticação pass-through para outro método. Esta alteração desativa a autenticação pass-through no inquilino e desinstala o agente de autenticação do servidor. Tem de desinstalar manualmente os agentes de autenticação de outros servidores.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>O que acontece quando posso desinstalar um agente de autenticação pass-through?

Se desinstalar um agente de autenticação pass-through a partir de um servidor, ele faz com que o servidor não aceitar pedidos de início de sessão. Para evitar interromper a capacidade de início de sessão utilizador no seu inquilino, certifique-se de que tenha outro agente de autenticação em execução antes de desinstalar um agente de autenticação pass-through.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Tenho um inquilino mais antigo que foi originalmente configurado com o AD FS.  Podemos migrou recentemente para o PTA, mas agora não está a ver as nossas alterações UPN sincronizar com o Azure AD.  Por que razão as nossas UPN muda não sincronizados?

R: Nas seguintes circunstâncias as alterações UPN no local podem não efetua a sincronização:

- Inquilino do Azure AD tiver sido criado antes de 15 de Junho de 2015
- Inicialmente foram federadas com o inquilino do Azure AD através do AD FS para autenticação
- Mudado para ter gerenciados usando o PTA como autenticação de utilizadores

Isto acontece porque o comportamento padrão de inquilinos criados antes de 15 de Junho de 2015, foi bloquear alterações UPN.  Se precisar de alterações UPN de anular a bloco que tem de executar o PowerShell cmdlt seguinte:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers-Enable $True`

Os inquilinos criados após 15 de Junho de 2015 tem o comportamento padrão de sincronização de alterações UPN.   



## <a name="next-steps"></a>Passos Seguintes
- [Limitações atuais](how-to-connect-pta-current-limitations.md): Saiba quais cenários são suportados e quais não são.
- [Guia de introdução](how-to-connect-pta-quick-start.md): Começar a trabalhar na autenticação pass-through do Azure AD.
- [Migrar do AD FS para autenticação pass-through](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) -um guia detalhado para migrar do AD FS (ou outras tecnologias de Federação) para autenticação pass-through.
- [Bloqueio do smart](../authentication/howto-password-smart-lockout.md): Saiba como configurar a capacidade de bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [Análise técnica aprofundada](how-to-connect-pta-how-it-works.md): Compreenda como funciona o recurso de autenticação pass-through.
- [Resolver problemas de](tshoot-connect-pass-through-authentication.md): Saiba como resolver problemas comuns com a funcionalidade de autenticação pass-through.
- [Detalhada da segurança](how-to-connect-pta-security-deep-dive.md): Obtenha informações técnicas detalhadas sobre a funcionalidade de autenticação pass-through.
- [SSO totalmente integrado do Azure AD](how-to-connect-sso.md): Saiba mais sobre esta funcionalidade complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum do Azure Active Directory para pedidos de novas funcionalidades de ficheiros.

