---
title: 'Azure AD Connect: autenticação de passagem-perguntas frequentes | Microsoft Docs'
description: Respostas para perguntas frequentes sobre Azure Active Directory autenticação de passagem
services: active-directory
keywords: Azure AD Connect autenticação de passagem, instalar Active Directory, componentes necessários para o Azure AD, SSO, logon único
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
ms.openlocfilehash: 06dfe1e76682d70170bfea104050b1000269c38f
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932395"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory autenticação de passagem: perguntas frequentes

Este artigo aborda perguntas frequentes sobre a autenticação de passagem do Azure Active Directory (Azure AD). Continue verificando o conteúdo atualizado.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Quais dos métodos para entrar no Azure AD, autenticação de passagem, sincronização de hash de senha e Serviços de Federação do Active Directory (AD FS) (AD FS), devo escolher?

Examine [este guia](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) para obter uma comparação dos vários métodos de entrada do Azure AD e como escolher o método de entrada correto para sua organização.

## <a name="is-pass-through-authentication-a-free-feature"></a>A autenticação de passagem é um recurso gratuito?

A autenticação de passagem é um recurso gratuito. Você não precisa de nenhuma edição paga do Azure AD para usá-lo.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpswwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>A autenticação de passagem está disponível no [Microsoft Azure Alemanha Cloud](https://www.microsoft.de/cloud-deutschland) e na [Microsoft Azure governamental Cloud](https://azure.microsoft.com/features/gov/)?

Não. A autenticação de passagem só está disponível na instância Mundial do Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>O [acesso condicional](../active-directory-conditional-access-azure-portal.md) funciona com a autenticação de passagem?

Sim. Todos os recursos de acesso condicional, incluindo a autenticação multifator do Azure, funcionam com a autenticação de passagem.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>A autenticação de passagem dá suporte à "ID alternativa" como o nome de usuário, em vez de "userPrincipalName"?
Para uma extensão limitada, a autenticação de passagem dá suporte à ID alternativa como o nome de usuário quando configurado no Azure AD Connect. Como pré-requisito, Azure AD Connect precisa sincronizar o atributo de `UserPrincipalName` de Active Directory local para o Azure AD. Isso faz com que as `UserPrincipalName` no AD local e no Azure AD se tornem idênticas. Se desejar usar outro atributo para sincronizar do AD local como o UPN para o Azure AD, você precisará usar a sincronização de hash de senha ou AD FS. Para obter mais informações, consulte [instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md). Nem todos os aplicativos do Office 365 dão suporte a `Alternate ID`. Consulte a declaração de suporte de documentação do aplicativo específico.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>A sincronização de hash de senha atua como um fallback para autenticação de passagem?

Não. A autenticação de passagem não _faz_ failover automaticamente para a sincronização de hash de senha. Para evitar falhas de conexão do usuário, você deve configurar a autenticação de passagem para [alta disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>O que acontece quando eu faço para alternar da sincronização de hash de senha para autenticação de passagem?

Quando você usa Azure AD Connect para alternar o método de entrada da sincronização de hash de senha para autenticação de passagem, a autenticação de passagem torna-se o método de entrada primário para seus usuários em domínios gerenciados. Observe que os hashes de senha de todos os usuários que foram sincronizados anteriormente pela sincronização de hash de senha permanecem armazenados no Azure AD.

## <a name="can-i-install-an-azure-ad-application-proxymanage-appsapplication-proxymd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Posso instalar um conector de [proxy de aplicativo do AD do Azure](../manage-apps/application-proxy.md) no mesmo servidor que um agente de autenticação de passagem?

Sim. As versões de marca do agente de autenticação de passagem, versão 1.5.193.0 ou posterior, dão suporte a essa configuração.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Quais versões do Azure AD Connect e do agente de autenticação de passagem você precisa?

Para que esse recurso funcione, você precisa da versão 1.1.750.0 ou posterior para Azure AD Connect e 1.5.193.0 ou posterior para o agente de autenticação de passagem. Instale todo o software em servidores com o Windows Server 2012 R2 ou posterior.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>O que acontecerá se a senha do meu usuário tiver expirado e tentar entrar usando a autenticação de passagem?

Se você configurou o [write-back de senha](../authentication/concept-sspr-writeback.md) para um usuário específico e, se o usuário entrar usando a autenticação de passagem, ele poderá alterar ou redefinir suas senhas. As senhas são gravadas de volta no local Active Directory conforme o esperado.

Se você não tiver configurado o Write-back de senha para um usuário específico ou se o usuário não tiver uma licença válida do Azure AD atribuída, o usuário não poderá atualizar sua senha na nuvem. Eles não podem atualizar sua senha, mesmo que sua senha tenha expirado. Em vez disso, o usuário vê esta mensagem: "sua organização não permite que você atualize sua senha neste site. Atualize-o de acordo com o método recomendado pela sua organização ou pergunte ao administrador se você precisar de ajuda ". O usuário ou o administrador deve redefinir sua senha no local Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Como a autenticação de passagem protege você contra ataques de senha de força bruta?

[Ler informações sobre o bloqueio inteligente](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>O que os agentes de autenticação de passagem comunicam pelas portas 80 e 443?

- Os agentes de autenticação fazem solicitações HTTPS pela porta 443 para todas as operações de recurso.
- Os agentes de autenticação fazem solicitações HTTP pela porta 80 para baixar as CRLs (listas de certificados revogados) SSL.

     >[!NOTE]
     >As atualizações recentes reduziram o número de portas exigidas pelo recurso. Se você tiver versões mais antigas do Azure AD Connect ou do agente de autenticação, mantenha essas portas abertas também: 5671, 8080, 9090, 9091, 9350, 9352 e 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Os agentes de autenticação de passagem podem se comunicar por um servidor proxy da Web de saída?

Sim. Se a descoberta automática de proxy da Web (WPAD) estiver habilitada no seu ambiente local, os agentes de autenticação tentarão automaticamente localizar e usar um servidor proxy da Web na rede.

Se você não tiver o WPAD em seu ambiente, poderá adicionar informações de proxy (conforme mostrado abaixo) para permitir que um agente de autenticação de passagem se comunique com o Azure AD:
- Configure as informações de proxy no Internet Explorer antes de instalar o agente de autenticação de passagem no servidor. Isso permitirá que você conclua a instalação do agente de autenticação, mas ele ainda aparecerá como **inativo** no portal de administração.
- No servidor, navegue até "C:\Arquivos de Programas\microsoft Azure AD Connect agente de autenticação".
- Edite o arquivo de configuração "AzureADConnectAuthenticationAgentService" e adicione as seguintes linhas (substitua "http\://contosoproxy.com:8080" pelo seu endereço de proxy real):

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

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Posso instalar dois ou mais agentes de autenticação de passagem no mesmo servidor?

Não, você só pode instalar um agente de autenticação de passagem em um único servidor. Se você quiser configurar a autenticação de passagem para alta disponibilidade, [siga as instruções aqui](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>É necessário renovar manualmente os certificados usados pelos agentes de autenticação de passagem?

A comunicação entre cada agente de autenticação de passagem e o Azure AD é protegida usando a autenticação baseada em certificado. Esses [certificados são renovados automaticamente a cada poucos meses pelo Azure ad](how-to-connect-pta-security-deep-dive.md#operational-security-of -the-authentication-agents). Não é necessário renovar manualmente esses certificados. Você pode limpar os certificados expirados mais antigos conforme necessário.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Como fazer remover um agente de autenticação de passagem?

Desde que um agente de autenticação de passagem esteja em execução, ele permanece ativo e processa continuamente as solicitações de entrada do usuário. Se você quiser desinstalar um agente de autenticação, vá para **painel de controle-> programas-> programas e recursos** e desinstale os programas **agente de autenticação do Microsoft Azure ad Connect** e agente do **Microsoft Azure ad Connect** .

Se você verificar a folha de autenticação de passagem no [centro de administração do Azure Active Directory](https://aad.portal.azure.com) depois de concluir a etapa anterior, você verá o agente de autenticação exibido como **inativo**. Isso é _esperado_. O agente de autenticação é descartado automaticamente da lista após alguns dias.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Eu já uso AD FS para entrar no Azure AD. Como fazer mudar para a autenticação de passagem?

Se você estiver migrando do AD FS (ou outras tecnologias de Federação) para a autenticação de passagem, é altamente recomendável que você siga nosso guia de implantação detalhado publicado [aqui](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true).

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Posso usar a autenticação de passagem em um ambiente de Active Directory de várias florestas?

Sim. Ambientes de várias florestas têm suporte se houver relações de confiança entre suas florestas de Active Directory e se o roteamento de sufixo de nome estiver configurado corretamente.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>A autenticação de passagem fornece balanceamento de carga entre vários agentes de autenticação?

Não, a instalação de vários agentes de autenticação de passagem garante apenas [alta disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Ele não fornece balanceamento de carga determinístico entre os agentes de autenticação. Qualquer agente de autenticação (aleatoriamente) pode processar uma solicitação de entrada de usuário específica.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Quantos agentes de autenticação de passagem preciso instalar?

A instalação de vários agentes de autenticação de passagem garante a [alta disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). No entanto, ele não fornece balanceamento de carga determinístico entre os agentes de autenticação.

Considere o pico e a carga média de solicitações de entrada que você espera ver em seu locatário. Como um benchmark, um único agente de autenticação pode lidar com 300 a 400 autenticações por segundo em uma CPU padrão de 4 núcleos, servidor de 16 GB de RAM.

Para estimar o tráfego de rede, use as seguintes diretrizes de dimensionamento:
- Cada solicitação tem um tamanho de carga de (0,5 K + 1K * num_of_agents) bytes; ou seja, dados do Azure AD para o agente de autenticação. Aqui, "num_of_agents" indica o número de agentes de autenticação registrados em seu locatário.
- Cada resposta tem um tamanho de payload de 1K bytes; ou seja, dados do agente de autenticação para o Azure AD.

Para a maioria dos clientes, dois ou três agentes de autenticação no total são suficientes para alta disponibilidade e capacidade. Você deve instalar agentes de autenticação próximos aos controladores de domínio para melhorar a latência de entrada.

>[!NOTE]
>Há um limite do sistema de 40 agentes de autenticação por locatário.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Posso instalar o primeiro agente de autenticação de passagem em um servidor que não seja aquele que executa Azure AD Connect?

Não, esse cenário _não_ tem suporte.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Por que preciso de uma conta de administrador global somente em nuvem para habilitar a autenticação de passagem?

É recomendável habilitar ou desabilitar a autenticação de passagem usando uma conta de administrador global somente em nuvem. Saiba mais sobre como [Adicionar uma conta de administrador global somente em nuvem](../active-directory-users-create-azure-portal.md). Fazer isso dessa forma garante que você não fique bloqueado do seu locatário.

## <a name="how-can-i-disable-pass-through-authentication"></a>Como posso desabilitar a autenticação de passagem?

Execute novamente o assistente de Azure AD Connect e altere o método de entrada do usuário da autenticação de passagem para outro método. Essa alteração desabilita a autenticação de passagem no locatário e desinstala o agente de autenticação do servidor. Você deve desinstalar manualmente os agentes de autenticação dos outros servidores.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>O que acontece quando eu desinstalo um agente de autenticação de passagem?

Se você desinstalar um agente de autenticação de passagem de um servidor, ele fará com que o servidor pare de aceitar solicitações de entrada. Para evitar a interrupção do recurso de entrada do usuário em seu locatário, verifique se você tem outro agente de autenticação em execução antes de desinstalar um agente de autenticação de passagem.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Tenho um locatário mais antigo que foi originalmente configurado usando AD FS.  Recentemente, migramos para PTA, mas agora não estamos vendo nossas alterações de UPN sincronizando com o Azure AD.  Por que nossas alterações de UPN não estão sendo sincronizadas?

R: sob as circunstâncias a seguir, suas alterações de UPN locais não poderão ser sincronizadas se:

- Seu locatário do Azure AD foi criado antes de 15 de junho de 2015
- Inicialmente, você foi federado com seu locatário do Azure AD usando AD FS para autenticação
- Você mudou para ter usuários gerenciados usando PTA como autenticação

Isso ocorre porque o comportamento padrão dos locatários criados antes de 15 de junho de 2015 foi bloquear as alterações de UPN.  Se você precisar desfazer o bloqueio das alterações de UPN, será necessário executar o seguinte cmdlt do PowerShell:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

Os locatários criados após 15 de junho de 2015 têm o comportamento padrão de sincronização de alterações de UPN.   



## <a name="next-steps"></a>Passos seguintes
- [Limitações atuais](how-to-connect-pta-current-limitations.md): saiba quais cenários têm suporte e quais não são.
- [Início rápido](how-to-connect-pta-quick-start.md): colocar em funcionamento a autenticação de passagem do Azure AD.
- [Migrar de AD FS para autenticação de passagem](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – um guia detalhado para migrar do AD FS (ou outras tecnologias de Federação) para a autenticação de passagem.
- [Bloqueio inteligente](../authentication/howto-password-smart-lockout.md): saiba como configurar o recurso de bloqueio inteligente em seu locatário para proteger as contas de usuário.
- [Aprofundamento técnico](how-to-connect-pta-how-it-works.md): entenda como funciona o recurso de autenticação de passagem.
- [Solução de problemas](tshoot-connect-pass-through-authentication.md): saiba como resolver problemas comuns com o recurso de autenticação de passagem.
- [Aprofundamento da segurança](how-to-connect-pta-security-deep-dive.md): Obtenha informações técnicas aprofundadas sobre o recurso de autenticação de passagem.
- [SSO contínuo do Azure ad](how-to-connect-sso.md): Saiba mais sobre esse recurso complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Use o fórum de Azure Active Directory para arquivar novas solicitações de recurso.

