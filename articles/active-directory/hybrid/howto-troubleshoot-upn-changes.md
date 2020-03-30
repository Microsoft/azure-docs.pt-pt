---
title: Alterações no nome do princípio do utilizador azure (UPN)
description: Compreender questões conhecidas e mitigações para alterações da UPN
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 695773da624bc8d4ccff09119d64fc43319ff488
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246437"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Plano e resolução de problemas O nome principal do utilizador muda no Diretório Ativo do Azure

Um nome principal do utilizador (UPN) é um atributo que é um padrão de comunicação na Internet para as contas dos utilizadores. A UPN é constituída por um prefixo UPN (nome da conta de utilizador) e um sufixo UPN (um nome de domínio DNS). O prefixo une o sufixo utilizando o símbolo "@". Por exemplo, someone@example.com. A UPN deve ser única entre todos os objetos principais de segurança dentro de uma floresta de diretórios. 

> [!NOTE]
> Para os desenvolvedores, recomendamos que utilize o objectID do utilizador como identificador imutável, em vez de UPN. Se as suas aplicações estiverem atualmente a utilizar a UPN, recomendamos que a UPN corresponda ao endereço de e-mail primário do utilizador para melhorar a sua experiência.<br> **Num ambiente híbrido, é importante que a UPN para um utilizador seja idêntica no diretório no local e no Diretório Ativo Azure.**

**Este artigo pressupõe que está a usar a UPN como identificador de utilizador. Aborda o planeamento das alterações da UPN e a recuperação de problemas que podem resultar de alterações da UPN.**

## <a name="learn-about-upns-and-upn-changes"></a>Conheça as alterações das UPNs e UPN
As páginas de entrada frequentemente levam os utilizadores a introduzir o seu endereço de e-mail quando o valor exigido é na verdade a sua UPN. Por isso, deve alterar a UPN dos utilizadores sempre que o seu endereço de e-mail primário se altere.

Os principais endereços de e-mail dos utilizadores podem mudar por muitas razões:

* rebranding da empresa

* funcionários movendo-se para diferentes divisões de empresas 

* fusões e aquisições

* alterações no nome do empregado

### <a name="types-of-upn-changes"></a>Tipos de alterações UPN

Pode alterar uma UPN alterando o prefixo, o sufixo ou ambos.

* **Alterar o prefixo**.

   *  Por exemplo, se o nome de uma pessoa mudar, pode alterar o nome da conta:  
BSimon@contoso.com paraBJohnson@contoso.com

   * Também pode alterar o padrão corporativo para prefixos:  
Bsimon@contoso.com paraBritta.Simon@contoso.com

* **Alterando o sufixo.** <br>

    Por exemplo, se uma pessoa mudar de divisão, pode mudar o seu domínio: 

   * Britta.Simon@contoso.comParaBritta.Simon@contosolabs.com <br>
     Ou<br>
    *   Britta.Simon@corp.contoso.comParaBritta.Simon@labs.contoso.com 

Altere a UPN do utilizador sempre que o endereço de e-mail principal para um utilizador for atualizado. Não importa a razão da mudança de e-mail, a UPN deve ser sempre atualizada para corresponder.

Durante a sincronização inicial do Ative Directory para a AD Azure, certifique-se de que os e-mails dos utilizadores são idênticos às suas UPNs

### <a name="upns-in-active-directory"></a>UPNs em Diretório Ativo

No Diretório Ativo, o sufixo padrão UPN é o nome DNS do domínio onde criou a conta de utilizador. Na maioria dos casos, este é o nome de domínio que regista como domínio da empresa na internet. Se criar a conta de utilizador no domínio contoso.com, a UPN padrão é

username@contoso.com

 No entanto, pode [adicionar mais sufixos UPN](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) utilizando domínios e fidedignidades do Diretório Ativo. 

Por exemplo, pode querer adicionar labs.contoso.com e ter as UPNs dos utilizadores e o e-mail a refletir isso. Tornar-se-iam então

username@labs.contoso.com.

>[!IMPORTANT]
> Se as UPNs no diretório Ativo e no Diretório Ativo azure não corresponderem, surgirão questões. Se estiver [a alterar o sufixo no Diretório Ativo,](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)deve certificar-se de que foi adicionado e verificado um nome de domínio personalizado correspondente no [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain). 

![Uma imagem de domínios verificados](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UPNs em Diretório Ativo Azure

Os utilizadores iniciaram sessão no Azure AD com o valor no seu atributo de userPrincipalName. 

Quando utiliza o Azure AD em conjunto com o seu Diretório Ativo no local, as contas de utilizador são sincronizadas utilizando o serviço Azure AD Connect. Por predefinição, o assistente Azure AD Connect utiliza o atributo do userPrincipalName a partir do Diretório Ativo no local como upn em Azure AD. Pode alterá-lo para um atributo diferente numa instalação personalizada.

É importante que tenha um processo definido quando atualizar um Nome Principal de Utilizador (UPN) de um único utilizador, ou para toda a sua organização. 

Consulte as questões e suposições conhecidas neste documento.

Quando estiver a sincronizar as contas dos utilizadores do Ative Directory para o Azure AD, certifique-se de que as UPNs em Diretório Ativo são para domínios verificados em Azure AD.

![Screenshot de domínios verificados](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Se o valor do atributo do userPrincipalName não corresponder a um domínio verificado em Azure AD, o processo de sincronização substitui o sufixo por um valor padrão .onmicrosoft.com.


### <a name="roll-out-bulk-upn-changes"></a>Alterações UPN a granel

Siga as[melhores práticas para um piloto](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) para alterações em massa da UPN. Também tenha um plano de reversão testado para reverter as UPNs se encontrar problemas que não podem ser resolvidos rapidamente. Uma vez que o seu piloto esteja em execução, você pode começar a direcionar pequenos conjuntos de utilizadores com várias funções organizacionais e seus conjuntos específicos de apps ou dispositivos.

Passar por este primeiro subconjunto de utilizadores vai dar-lhe uma boa ideia do que os utilizadores devem esperar como parte da mudança. Inclua esta informação nas comunicações do utilizador.

Criar um procedimento definido para a alteração das UPNs em utilizadores individuais como parte das operações normais. Recomendamos que tenha um procedimento testado que inclua documentação sobre questões conhecidas e suposições.

As secções seguintes detalham potenciais questões conhecidas e soluções de suposições quando as UPNs são alteradas.

## <a name="user-provisioning-known-issues-and-workarounds"></a>fornecimento de utilizadores questões conhecidas e suposições

As aplicações de [software como serviço (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) e Line of Business (LoB) dependem frequentemente das UPNs para encontrar informações sobre os utilizadores e armazenar informações sobre o perfil dos utilizadores, incluindo funções. As aplicações que utilizam [o provisionamento Just in Time](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) para criar um perfil de utilizador quando os utilizadores iniciarem sessão na aplicação pela primeira vez podem ser afetadas por alterações da UPN.

**Edição conhecida**<br>
Alterar a UPN de um utilizador pode quebrar a relação entre o utilizador da AD Azure e o perfil de utilizador criado na aplicação. Se a aplicação utilizar [o provisionamento Just in Time,](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)poderá criar um novo perfil de utilizador. Isto exigirá que o administrador de aplicação efaça alterações manuais para corrigir esta relação.

**Supor**<br>
O fornecimento automatizado de [utilizadores da Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) permite-lhe criar, manter e remover automaticamente as identidades dos seus utilizadores em aplicações em nuvem suportadas. Configurar o fornecimento automatizado de utilizadores nas suas aplicações atualiza automaticamente as UPNs nas aplicações. Teste as aplicações como parte do lançamento progressivo para validar que não são afetadas por alterações da UPN.

## <a name="managed-devices-known-issues-and-workarounds"></a>Dispositivos geridos conhecidos questões e sobras

Ao [trazer os seus dispositivos para o Azure AD,](https://docs.microsoft.com/azure/active-directory/devices/overview)maximiza a produtividade dos seus utilizadores através de um único sinal (SSO) através da sua nuvem e recursos no local.

### <a name="azure-ad-joined-devices"></a>Dispositivos associados ao Azure AD

Os dispositivos aderes à [Azure AD](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join) juntam-se diretamente à Azure AD e permitem que os utilizadores inscrevam o dispositivo utilizando a identidade da sua organização.

**Problemas conhecidos** <br>
Os utilizadores podem experimentar problemas de inscrição simples com aplicações que dependem da AD Azure para autenticação.

**Supor** <br>
Dê tempo suficiente para que a alteração UPN sincronize com a AD Azure. Assim que verificar se a nova UPN está refletida no Portal Azure AD, peça ao utilizador que selecione o azulejo "Outro utilizador" para iniciar sessão com a sua nova UPN. também pode verificar através [da PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0). Depois de iniciar sessão com a sua nova UPN, as referências à antiga UPN podem ainda aparecer na definição do Windows "Access work or school".

![Screenshot de domínios verificados](./media/howto-troubleshoot-upn-changes/other-user.png)

### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos híbridos associados ao Azure AD

Os dispositivos aderes à [Hybrid Azure AD](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid) juntam-se ao Ative Directory e ao Azure AD. Pode implementar a adesão do Hybrid Azure AD se o seu ambiente tiver uma pegada de Diretório Ativo no local e também quiser beneficiar das capacidades fornecidas pela Azure AD.

**Problemas conhecidos** 

Os dispositivos aderes ao Windows 10 Hybrid Azure AD são suscetíveis de sofrer reiniciações inesperadas e problemas de acesso.

Se os utilizadores iniciarem sessão no Windows antes da nova UPN ter sido sincronizada com a AD Azure, ou continuarem a utilizar uma sessão do Windows existente, poderão experimentar problemas de acesso único com aplicações que utilizam a AD Azure para autenticação se o Acesso Condicional tiver sido configurado para impor o uso de dispositivos Híbridos Unidos para aceder a recursos. 

Além disso, aparecerá a seguinte mensagem, forçando um reinício após um minuto. 

"O seu PC recomeçará automaticamente num minuto. O Windows teve um problema e precisa de ser reiniciado. Deve fechar esta mensagem agora e salvar o seu trabalho".

**Supor** 

O aparelho deve ser desacompanhado da AD Azure e reiniciado. Após o reinício, o dispositivo voltará a juntar-se automaticamente ao Azure AD e o utilizador deve iniciar o seu contrato com a nova UPN, selecionando o azulejo "Outro utilizador". Para desjuntar um dispositivo da Azure AD, execute o seguinte comando num pedido de comando:

**dsregcmd /leave**

O utilizador terá de [se reinscrever](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) para o Windows Hello for Business se estiver a ser utilizado. Os dispositivos Windows 7 e 8.1 não são afetados por este problema após alterações da UPN.

## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Autenticador conhecido seleções e seleções

A sua organização poderá exigir a utilização da [aplicação Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) para iniciar sessão e aceder a aplicações e dados organizacionais. Embora possa aparecer um nome de utilizador na aplicação, a conta não está configurada para funcionar como um método de verificação até que o utilizador complete o processo de registo.

A [aplicação Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) tem quatro funções principais:

* Autenticação de vários fatores através de um código de notificação ou verificação push

* Atuar como corretor de autenticação em dispositivos iOS e Android para fornecer um único sinal para aplicações que utilizem [autenticação intermediada](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth)

* Registo de dispositivos (também conhecido como Workplace Joined) para AD Azure, que é um requisito para outras funcionalidades como Intune App Protection e Device Registration/Management,

* Registo telefónico, que requer MFA e registo do dispositivo.

### <a name="multi-factor-authentication-with-android-devices"></a>Autenticação multi-factor com dispositivos Android

A aplicação Microsoft Authenticator oferece uma opção de verificação fora da banda. Em vez de colocar uma chamada telefónica ou SMS automatizada ao utilizador durante o check-in, a [Autenticação Multi-Factor (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) empurra uma notificação para a aplicação Microsoft Authenticator no smartphone ou tablet do utilizador. O utilizador simplesmente toca Em 'Approve' (ou introduz um PIN ou biométrico e toca em "Authenticate") na aplicação para completar o seu início de sessão.

Quando muda a UPN de um utilizador, os dispositivos móveis podem experimentar os seguintes problemas:

**Problemas conhecidos** 

A antiga UPN ainda apresenta na conta de utilizador e uma notificação pode não ser recebida. [Os códigos](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq) de verificação continuam a funcionar.

**Supor**

Se for recebida uma notificação, instrua o utilizador a rejeitar a notificação, abra a aplicação Autenticadora, toque na opção "Verificar notificações" e aprove o pedido de MFA. Depois disso, a UPN exibida na conta será atualizada. Note que a UPN atualizada pode ser apresentada como uma nova conta, isto deve-se à aplicação de outras funcionalidades do Autenticador. Para mais informações, mais informações conhecidas neste artigo.

### <a name="brokered-authentication"></a>Autenticação intermediada

Em corretores Android e iOS como o Microsoft Authenticator permitem:

* Inscrição única (SSO) - Os seus utilizadores não precisarão de iniciar sessão em cada aplicação.

* Identificação do dispositivo - O corretor acede ao certificado do dispositivo criado no dispositivo quando foi aderido no local de trabalho.

* Verificação de identificação de aplicações - Quando uma aplicação chama o corretor, passa o url de redirecionamento, e o corretor verifica-o.

Além disso, permite que as aplicações participem em funcionalidades mais avançadas, como o [Acesso Condicional,](https://docs.microsoft.com/azure/active-directory/conditional-access/)e suporta [cenários Microsoft Intune](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps).

**Problemas conhecidos**<br>
O utilizador é apresentado com solicitações de autenticação mais interativas em novas aplicações que utilizam o sessão assistido pelo corretor devido a uma incompatibilidade entre o login_hint passado pela aplicação e a UPN armazenada no corretor.

**Supor** <br> O utilizador precisa de remover manualmente a conta do Microsoft Authenticator e iniciar um novo início de sessão a partir de uma aplicação assistida por corretor. A conta será adicionada automaticamente após a autenticação inicial.

### <a name="device-registration"></a>Registo de dispositivo

A aplicação Microsoft Authenticator é responsável pelo registo do dispositivo na Azure AD. O registo do dispositivo permite que o dispositivo autentime à AD Azure e é um requisito para os seguintes cenários:

* Intune App Protection

* Inscrição de Dispositivo Intune

* Sinal de telefone dentro

**Problemas conhecidos**<br>
Quando muda a UPN, uma nova conta com a nova UPN aparece listada na aplicação Microsoft Authenticator, enquanto a conta com a antiga UPN ainda está listada. Além disso, os antigos ecrãs UPN na secção registo do dispositivo nas definições da aplicação. Não existe qualquer alteração na funcionalidade normal do Registo do Dispositivo ou nos cenários dependentes.

**Supor** <br> Para remover todas as referências à antiga UPN na aplicação Microsoft Authenticator, instrua o utilizador a remover manualmente as contas antigas e novas do Microsoft Authenticator, voltar a registar-se para o MFA e voltar a juntar-se ao dispositivo.

### <a name="phone-sign-in"></a>Registo telefónico

O registo telefónico permite que os utilizadores acedam ao Azure AD sem senha. Para ativar o registo do telefone, o utilizador tem de se registar para o MFA utilizando a aplicação Authenticator e, em seguida, ativar o registo do telefone diretamente no Autenticador. Como parte da configuração, o dispositivo regista-se com a AD Azure.

**Problemas conhecidos** <br>
Os utilizadores não podem utilizar o registo telefónico porque não recebem qualquer notificação. Se o utilizador tocar no Check for Notifications, obtém um erro.

**Supor**<br>
O utilizador tem de selecionar o menu de entrega na conta ativada para o registo do telefone e selecionar desativar o registo do telefone. Se desejar, o registo do telefone pode ser ativado novamente.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Chave de Segurança (FIDO2) questões conhecidas e salões

**Problemas conhecidos** <br>
Quando vários utilizadores estão registados na mesma tecla, o sinal no ecrã mostra uma página de seleção de conta onde a antiga UPN é exibida. As inscrições utilizando as Teclas de Segurança não são afetadas pelas alterações da UPN.  

**Supor**<br>
Para remover referências às nsupções antigas, os utilizadores devem redefinir a chave de [segurança e reregistar.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues)

## <a name="onedrive-known-issues-and-workarounds"></a>OneDrive conheceu questões e salões

Os utilizadores do OneDrive são conhecidos por experimentarem problemas após as alterações da UPN. Para obter mais informações, veja como as [alterações da UPN afetam as funcionalidades do URL oneDrive e do OneDrive](https://docs.microsoft.com/onedrive/upn-changes).

## <a name="next-steps"></a>Passos seguintes

Consulte estes recursos:
* [Azure AD Connect: Conceitos de design](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [População UserPrincipalName do Azure AD](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Fichas de ID da plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
