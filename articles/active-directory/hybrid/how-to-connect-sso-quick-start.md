---
title: 'Azure AD Connect: Totalmente integrado Single Sign-On - guia de introdução | Documentos da Microsoft'
description: Este artigo descreve como começar com o Azure Active Directory totalmente integrada início de sessão único
services: active-directory
keywords: o que é o Azure AD Connect, a instalação do Active Directory, necessário componentes para o Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 345c97a19f789bb3d850df000824d4c23989a81f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58086824"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>O Azure Active Directory totalmente integrada início de sessão único: Início rápido

## <a name="deploy-seamless-single-sign-on"></a>Implementar totalmente integrada início de sessão único

Azure Active Directory (Azure AD) totalmente integrada início de sessão único (SSO totalmente integrado) automaticamente inicia sessão dos utilizadores quando estão em seus desktops corporativos que estão ligadas à sua rede empresarial. SSO totalmente integrado fornece os seus utilizadores com acesso fácil aos seus aplicativos baseados na nuvem sem a necessidade de quaisquer componentes no local adicionais.

Para implementar o SSO totalmente integrado, siga estes passos.

## <a name="step-1-check-the-prerequisites"></a>Passo 1: Verificar os pré-requisitos

Certifique-se de que os seguintes pré-requisitos são cumpridos:

* **Configurar o servidor do Azure AD Connect**: Se usar [autenticação pass-through](how-to-connect-pta.md) como método de início de sessão, não é necessária nenhuma verificação de pré-requisitos adicional. Se usar [sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md) como método de início de sessão e se existir uma firewall entre o Azure AD Connect e o Azure AD, certifique-se de que:
   - Utilizar a versão 1.1.644.0 ou posterior do Azure AD Connect. 
   - Se a sua firewall ou proxy permite listas de permissões de DNS, lista de permissões as conexões com o  **\*. msappproxy.net** URLs através da porta 443. Se não, permitir o acesso para o [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que é atualizado semanalmente. Este pré-requisito é aplicável apenas quando ativar a funcionalidade. Não é necessário para inícios de sessão de utilizador reais.

    >[!NOTE]
    >O Azure AD Connect as versões 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 têm um problema relacionado com a sincronização de hash de palavra-passe. Se _não_ tenciona utilizar a sincronização de hash de palavra-passe em conjunto com a autenticação pass-through, leia o [notas de versão do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) para saber mais.

* **Utilizar um suporte do Azure AD Connect topologia**: Certifique-se de que está a utilizar uma das topologias com suporte do Azure AD Connect descritas [aqui](plan-connect-topologies.md).

    >[!NOTE]
    >SSO totalmente integrado suporta várias florestas do AD, se existem AD relações de confiança entre eles ou não.

* **Configurar as credenciais de administrador de domínio**: Tem de ter credenciais de administrador de domínio para cada do Active Directory de floresta que:
    * Sincronizar com o Azure AD através do Azure AD Connect.
    * Contém utilizadores que pretende ativar o SSO totalmente integrado.
    
* **Ativar a autenticação moderna**: Tem de ativar [autenticação moderna](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) no seu inquilino para esta funcionalidade funcione.

* **Utilize as versões mais recentes de clientes do Office 365**: Para obter uma experiência silenciosa início de sessão com clientes do Office 365 (Outlook, Word, Excel e outras pessoas), os utilizadores precisam de utilizar versões 16.0.8730.xxxx ou superior.

## <a name="step-2-enable-the-feature"></a>Passo 2: Ativar a funcionalidade

Ativar o SSO totalmente integrado através de [do Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> Também pode [ativar o SSO totalmente integrado com o PowerShell](tshoot-connect-sso.md#manual-reset-of-the-feature) se o Azure AD Connect não cumprir os requisitos. Utilize esta opção se tiver mais de um domínio por floresta do Active Directory, e desejar mais ser direcionadas sobre o domínio que pretende ativar o SSO totalmente integrado para.

Se estiver fazendo uma instalação nova do Azure AD Connect, escolha o [caminho de instalação personalizada](how-to-connect-install-custom.md). Na **sessão do utilizador** página, selecione a **ativar o início de sessão único em** opção.

>[!NOTE]
> A opção estarão disponível para seleção apenas se o método de início de sessão for **sincronização de Hash de palavra-passe** ou **autenticação pass-through**.

![Azure AD Connect: Início de sessão do utilizador](./media/how-to-connect-sso-quick-start/sso8.png)

Se já tiver uma instalação do Azure AD Connect, selecione o **alterar utilizador inicie sessão** página no Azure AD Connect e, em seguida, selecione **próxima**. Se estiver a utilizar o Azure AD Connect versões 1.1.880.0 ou superior, o **ativar o início de sessão único em** opção será selecionada por predefinição. Se estiver a utilizar as versões mais antigas do Azure AD Connect, selecione o **ativar o início de sessão único em** opção.

![Azure AD Connect: Alterar o utilizador inicie sessão](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Continuar através do assistente até chegar à **ativar o início de sessão único em** página. Fornece credenciais de administrador de domínio para cada diretório do Active Directory de floresta que:

* Sincronizar com o Azure AD através do Azure AD Connect.
* Contém utilizadores que pretende ativar o SSO totalmente integrado.

Após a conclusão do assistente, o SSO totalmente integrado está ativada no seu inquilino.

>[!NOTE]
> As credenciais de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. Eles são usados apenas para ativar a funcionalidade.

Siga estas instruções para verificar se tiver habilitado SSO totalmente integrado corretamente:

1. Inicie sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de administrador global do seu inquilino.
2. Selecione **do Azure Active Directory** no painel esquerdo.
3. Selecione **do Azure AD Connect**.
4. Certifique-se de que o **totalmente integrada início de sessão único** funcionalidade é apresentado como **ativado**.

![Portal do Azure: Painel do Azure AD Connect](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> SSO totalmente integrado cria uma conta de computador com o nome `AZUREADSSOACC` (que representa o Azure AD) no seu local do Active Directory (AD) em cada floresta do AD. Esta conta de computador é necessária para a funcionalidade funcionar. Se estiver a utilizar as arquiteturas de Pass-the-Hash e atenuação de roubo de credenciais no seu ambiente no local, certifique-se de que o `AZUREADSSOACC` conta de computador não acaba no contentor de quarentena. Faça as alterações apropriadas para criar a conta de computador no contentor de computadores. Depois do SSO totalmente integrado é ativado com êxito no Assistente do Azure AD Connect, mover o `AZUREADSSOACC` conta de computador para uma unidade organizacional (UO) onde outras contas de computador são geridas para se certificar de que ele não é eliminado acidentalmente.

## <a name="step-3-roll-out-the-feature"></a>Passo 3: Implementar a funcionalidade

Pode implementar gradualmente o SSO totalmente integrado para os utilizadores com as instruções indicadas abaixo. Comece por adicionar o seguinte URL de AD do Azure para todos ou selecionar as definições de zona de Intranet dos utilizadores utilizando a política de grupo no Active Directory:

- `https://autologon.microsoftazuread-sso.com`

Além disso, tem de ativar uma política de zona de Intranet chamada **permitir atualizações à barra de estado por meio de script** por meio da diretiva de grupo. 

>[!NOTE]
> As instruções seguintes trabalham apenas para o Internet Explorer e Google Chrome no Windows (se ele compartilha um conjunto de URLs de sites fidedignos com o Internet Explorer). Leia a secção seguinte para obter instruções sobre como configurar o Mozilla Firefox e o Google Chrome em macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Por que precisa modificar as definições de zona de Intranet dos utilizadores?

Por predefinição, o navegador calcula automaticamente a zona correta, Internet ou Intranet, a partir de uma URL específica. Por exemplo, `http://contoso/` mapeia para a zona da Intranet, ao passo que `http://intranet.contoso.com/` mapeia para a zona de Internet (porque o URL contém um período). Browsers não irão enviar os tíquetes Kerberos para um ponto final da cloud, como o URL de AD do Azure, a menos que explicitamente, adicione o URL para a zona de Intranet do navegador.

Existem duas formas de modificar as definições de zona de Intranet dos utilizadores:

| Opção | Consideração de administrador | Experiência de utilizador |
| --- | --- | --- |
| Política de grupo | Administrador bloqueia o acesso de edição das definições de zona de Intranet | Os utilizadores não podem modificar suas próprias definições |
| Preferência de política de grupo |  Administração permite editar as definições de zona da Intranet | Os utilizadores podem modificar suas próprias definições |

### <a name="group-policy-option---detailed-steps"></a>Opção "Política de grupo" - os passos detalhados

1. Abra a ferramenta de Editor de gerenciamento de diretiva de grupo.
2. Editar a política de grupo é aplicada a alguns ou todos os seus utilizadores. Este exemplo utiliza **política de domínio predefinida**.
3. Navegue até **configuração do usuário** > **política** > **modelos administrativos** > **Windows Componentes** > **Internet Explorer** > **painel de controlo da Internet** > **página segurança**. Em seguida, selecione **Site para a lista de atribuições de zona**.
    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso6.png)
4. Ativar a política e, em seguida, introduza os seguintes valores na caixa de diálogo:
   - **Nome do valor**: O URL de AD do Azure onde os tíquetes Kerberos são reencaminhados.
   - **Valor** (dados): **1** indica a zona da Intranet.

     O resultado tem o seguinte aspeto:

     Nome do valor: `https://autologon.microsoftazuread-sso.com`
  
     Valor (dados): 1

   >[!NOTE]
   > Se quiser não permitir alguns usuários com SSO totalmente integrado (por exemplo, se estes utilizadores iniciarem sessão em quiosques partilhados), defina os valores anteriores **4**. Esta ação adiciona o URL de AD do Azure para a zona restrita e falha SSO totalmente integrado, o tempo todo.
   >

5. Selecione **OK**e, em seguida, selecione **OK** novamente.

    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso7.png)

6. Navegue até **configuração do usuário** > **modelos administrativos** **política** > * * > **Windows componentes**  >  **Do Internet Explorer** > **painel de controlo da Internet** > **página segurança**  >   **Zona da intranet**. Em seguida, selecione **permitir atualizações à barra de estado por meio de script**.

    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso11.png)

7. Ativar a definição de política e, em seguida, selecione **OK**.

    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Opção "Preferência de política de grupo" - os passos detalhados

1. Abra a ferramenta de Editor de gerenciamento de diretiva de grupo.
2. Editar a política de grupo é aplicada a alguns ou todos os seus utilizadores. Este exemplo utiliza **política de domínio predefinida**.
3. Navegue até **configuração do usuário** > **preferências** > **definições do Windows** > **registo**  >  **New** > **item do registo**.

    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso15.png)

4. Insira os seguintes valores nos campos adequados e clique em **OK**.
   - **Caminho da chave**: ***Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon***
   - **Nome do valor**: ***https***.
   - **Tipo de valor**: ***REG_DWORD***.
   - **Dados do valor**: ***00000001***.
 
     ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Considerações de browser

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (todas as plataformas)

Mozilla Firefox automaticamente não usa a autenticação Kerberos. Cada utilizador tem de adicionar manualmente o URL de AD do Azure para as definições do Firefox, utilizando os seguintes passos:
1. Execute o Firefox e introduza `about:config` na barra de endereço. Dispensa quaisquer notificações que vê.
2. Procure o **network.negotiate-auth.trusted-uris** preferência. Essa preferência apresenta uma lista de sites fidedignos do Firefox para a autenticação Kerberos.
3. Com o botão direito e selecione **modificar**.
4. Introduza `https://autologon.microsoftazuread-sso.com` no campo.
5. Selecione **OK** e, em seguida, volte a abrir o browser.

#### <a name="safari-macos"></a>Safari (macOS)

Certifique-se de que a máquina com o macOS está associada ao AD. Instruções para ingressar em AD o dispositivo macOS está fora do escopo deste artigo.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (todas as plataformas)

Se tiver substituído o [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) ou o [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) definições de política no seu ambiente, certifique-se de que adicione o URL do Azure AD (`https://autologon.microsoftazuread-sso.com`)-los também.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS e outras plataformas não Windows)

Para o Google Chrome em Mac OS e outras plataformas não Windows, consulte [lista de políticas de projeto do The Chromium](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) para obter informações sobre como a lista aprovada o URL do Azure AD para autenticação integrada.

A utilização de extensões de política de grupo do Active Directory de terceiros para implementar o URL de AD do Azure para Firefox e o Google Chrome nos utilizadores de Mac está fora do escopo deste artigo.

#### <a name="known-browser-limitations"></a>Limitações conhecidas do browser

SSO totalmente integrado não funciona no modo de navegação particular em navegadores Firefox e o Microsoft Edge. Também não funciona no Internet Explorer se o browser está em execução no modo protegido avançado.

## <a name="step-4-test-the-feature"></a>Passo 4: A funcionalidade de teste

Para testar a funcionalidade de um utilizador específico, certifique-se de que todas as condições seguintes são cumpridos:
  - O utilizador inicia sessão num dispositivo da empresa.
  - O dispositivo é associado ao domínio do Active Directory. O dispositivo _não_ têm de ser [do Azure AD associado](../active-directory-azureadjoin-overview.md).
  - O dispositivo tem uma ligação direta ao seu controlador de domínio (DC), na rede da empresa com ou sem fio ou através de uma ligação de acesso remoto, como uma ligação VPN.
  - Tiver [distribuiu o recurso](##step-3-roll-out-the-feature) a este utilizador através da política de grupo.

Para testar o cenário em que o usuário inserir apenas o nome de utilizador, mas não a palavra-passe:
   - Inicie sessão no `https://myapps.microsoft.com/` numa nova sessão de browser privada.

Para testar o cenário em que o utilizador não tem de introduzir o nome de utilizador ou a palavra-passe, utilize um destes passos: 
   - Inicie sessão no `https://myapps.microsoft.com/contoso.onmicrosoft.com` numa nova sessão de browser privada. Substitua *contoso* com o nome do inquilino.
   - Inicie sessão no `https://myapps.microsoft.com/contoso.com` numa nova sessão de browser privada. Substitua *contoso.com* com um domínio verificado (não um domínio Federado) no seu inquilino.

## <a name="step-5-roll-over-keys"></a>Passo 5: Reverter as chaves

No passo 2, o Azure AD Connect cria contas de computador (representar o Azure AD) em todas as florestas do Active Directory em que tiver ativado o SSO totalmente integrado. Para obter mais informações, consulte [do Azure Active Directory totalmente integrada início de sessão único: Análise técnica aprofundada](how-to-connect-sso-how-it-works.md).

>[!IMPORTANT]
>A chave de desencriptação do Kerberos numa conta de computador, se perdido, pode ser utilizada para gerar pedidos de Kerberos para qualquer utilizador na sua floresta do AD. Atores maliciosos, em seguida, podem representar o Azure AD-inícios de sessão de utilizadores comprometidos. É altamente recomendável que lançar periodicamente através destas chaves de desencriptação do Kerberos – pelo menos uma vez a cada 30 dias.

Para obter instruções sobre como fazer o rollover de chaves, consulte [do Azure Active Directory totalmente integrada início de sessão único: Perguntas mais frequentes sobre](how-to-connect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account). Estamos a trabalhar num recurso para apresentá-implementação automatizada através de chaves.

>[!IMPORTANT]
>Não precisa de efetuar este passo _imediatamente_ depois de ter ativado a funcionalidade. Rollover as chaves de desencriptação do Kerberos, pelo menos, uma vez a cada 30 dias.

## <a name="next-steps"></a>Passos Seguintes

- [Análise técnica aprofundada](how-to-connect-sso-how-it-works.md): Compreenda como funciona a funcionalidade de início de sessão único totalmente integrado.
- [Perguntas mais frequentes sobre](how-to-connect-sso-faq.md): Obtenha respostas às perguntas mais frequentes sobre o simples início de sessão único.
- [Resolver problemas de](tshoot-connect-sso.md): Saiba como resolver problemas comuns com a funcionalidade de início de sessão único totalmente integrado.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum do Azure Active Directory para pedidos de novas funcionalidades de ficheiros.
