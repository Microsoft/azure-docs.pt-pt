---
title: Plano e resolução de problemas Alterações do nome principal do utilizador (UPN)
description: Compreender questões conhecidas e mitigações para alterações na UPN
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
ms.openlocfilehash: 19e40d135316c1c7cd270d2804fff1f487937685
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96858540"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Plano e resolução de problemas Alterações do Nome Principal do Utilizador no Diretório Ativo do Azure

Um Nome Principal do Utilizador (UPN) é um atributo que é um padrão de comunicação de internet para contas de utilizador. Um UPN é composto por um prefixo UPN (o nome da conta do utilizador) e um sufixo UPN (um nome de domínio DNS). O prefixo une o sufixo usando o símbolo " \@ " Por exemplo, someone@example.com. Um UPN deve ser único entre todos os objetos principais de segurança dentro de uma floresta de diretórios. 

**Este artigo pressupõe que está a usar a UPN como identificador de utilizador. Aborda o planeamento de alterações da UPN e a recuperação de problemas que podem resultar de alterações na UPN.**

> [!NOTE]
> Para os desenvolvedores, recomendamos que utilize o objectID do utilizador como identificador imutável, em vez de UPN ou endereços de e-mail, uma vez que os seus valores podem mudar.


## <a name="learn-about-upns-and-upn-changes"></a>Conheça as alterações da UPNs e da UPN
As páginas de entrada de sômedudas frequentemente levam os utilizadores a introduzir o seu endereço de e-mail quando o valor exigido é, na verdade, o seu UPN. Portanto, deve ter a certeza de alterar a UPN dos utilizadores sempre que o seu endereço de e-mail principal for alterado.

Os principais endereços de e-mail dos utilizadores podem mudar por muitas razões:

* rebranding empresa

* funcionários movendo-se para diferentes divisões da empresa 

* fusões e aquisições

* alterações de nome do empregado

### <a name="types-of-upn-changes"></a>Tipos de alterações upn

Pode alterar uma UPN alterando o prefixo, sufixo ou ambos.

* **Alterando o prefixo**.

   *  Por exemplo, se o nome de uma pessoa mudar, pode alterar o nome da conta:  
BSimon@contoso.comparaBJohnson@contoso.com

   * Pode também alterar o padrão corporativo para prefixos:  
Bsimon@contoso.comparaBritta.Simon@contoso.com

* **Mudando o sufixo.** <br>

    Por exemplo, se uma pessoa mudar de divisão, pode mudar o seu domínio: 

   * Britta.Simon@contoso.com Para Britta.Simon@contosolabs.com <br>
     Ou<br>
    * Britta.Simon@corp.contoso.com Para Britta.Simon@labs.contoso.com 

Recomendamos alterar a UPN dos utilizadores sempre que o seu endereço de e-mail principal seja atualizado.

Durante a sincronização inicial do Ative Directory para Azure AD, certifique-se de que os e-mails dos utilizadores são idênticos aos seus UPNs.

### <a name="upns-in-active-directory"></a>UPNs em Diretório Ativo

No Ative Directory, o sufixo UPN predefinido é o nome DNS do domínio onde criou a conta de utilizador. Na maioria dos casos, este é o nome de domínio que regista como domínio da empresa na internet. Se criar a conta de utilizador no domínio contoso.com, o UPN predefinido é

username@contoso.com

 No entanto, pode [adicionar mais sufixos UPN](../fundamentals/add-custom-domain.md) utilizando domínios e fidedignidades do Ative Directory. 

Por exemplo, pode querer adicionar labs.contoso.com e ter UPNs dos utilizadores e e-mail refletir isso. Tornar-se-iam então

username@labs.contoso.com.

>[!IMPORTANT]
> Se estiver [a alterar o sufixo no Ative Directory,](../fundamentals/add-custom-domain.md)deve certificar-se de que foi adicionado e verificado um nome de domínio personalizado correspondente no [AD AZure](../fundamentals/add-custom-domain.md). 

![Uma imagem de domínios verificados](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UPNs em Diretório Ativo Azure

Os utilizadores inserem-se no Azure AD com o valor no seu atributo UserPrincipalName. 

Quando utiliza o Azure AD em conjunto com o seu Ative Directory, as contas do utilizador são sincronizadas utilizando o serviço Azure AD Connect. Por predefinição, o assistente Azure AD Connect utiliza o atributo UserPrincipalName a partir do Diretório Ativo no local como UPN em Azure AD. Pode alterá-lo para um atributo diferente numa instalação personalizada.

É importante que tenha um processo definido quando atualiza um Nome Principal do Utilizador (UPN) de um único utilizador, ou para toda a sua organização. 

Consulte as questões e soluções alternativas conhecidas neste documento.

Quando estiver a sincronizar as contas dos utilizadores do Ative Directory para a Azure AD, certifique-se de que as UPNs no mapa do Ative Directory são para domínios verificados em Azure AD.

![Screenshot que mostra exemplos de UPNs mapeados para os domínios Azure A D verificados.](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Se o valor do atributo UserPrincipalName não corresponder a um domínio verificado em Ad Azure, o processo de sincronização substitui o sufixo por um valor padrão .onmicrosoft.com.


### <a name="roll-out-bulk-upn-changes"></a>Alterações de UPN em massa de lançamento

Siga as [melhores práticas para um piloto](../fundamentals/active-directory-deployment-plans.md) para alterações em massa da UPN. Também tem um plano de reversão testado para reverter as UPNs se encontrar problemas que não podem ser resolvidos rapidamente. Uma vez que o seu piloto está em execução, você pode começar a direcionar pequenos conjuntos de utilizadores com várias funções organizacionais e seus conjuntos específicos de apps ou dispositivos.

Passar por este primeiro subconjunto de utilizadores irá dar-lhe uma boa ideia do que os utilizadores devem esperar como parte da mudança. Inclua esta informação nas comunicações do seu utilizador.

Crie um procedimento definido para a alteração das UPNs em utilizadores individuais como parte das operações normais. Recomendamos ter um procedimento testado que inclua documentação sobre questões conhecidas e soluções alternativas.

As secções seguintes detalham potenciais problemas e soluções quando as UPNs são alteradas.

## <a name="apps-known-issues-and-workarounds"></a>Aplicativos conhecidos questões e soluções alternativas

As aplicações [de software como serviço (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) e Line of Business (LoB) dependem frequentemente de UPNs para encontrar utilizadores e armazenar informações sobre o perfil do utilizador, incluindo funções. As aplicações que utilizam [o fornecimento just in time](../app-provisioning/user-provisioning.md) para criar um perfil de utilizador quando os utilizadores iniciarem sessão na aplicação pela primeira vez podem ser afetadas por alterações na UPN.

**Problema conhecido**<br>
A alteração da UPN de um utilizador pode quebrar a relação entre o utilizador Azure AD e o perfil de utilizador criado na aplicação. Se a aplicação utilizar  [o provisionamento Just in Time,](../app-provisioning/user-provisioning.md)poderá criar um novo perfil de utilizador. Isto exigirá que o administrador de aplicação es faça alterações manuais para corrigir esta relação.

**Solução**<br>
[O Azure AD Automated User Provisioning](../app-provisioning/user-provisioning.md) permite-lhe criar, manter e remover automaticamente as identidades do utilizador em aplicações de nuvem suportadas. Configurar o fornecimento automatizado de utilizadores nas suas aplicações atualiza automaticamente as UPNs nas aplicações. Teste as aplicações como parte do rollout progressivo para validar que não são impactadas por alterações da UPN.
Se for um desenvolvedor, considere [adicionar suporte SCIM à sua aplicação](../app-provisioning/use-scim-to-provision-users-and-groups.md) para permitir o fornecimento automático de utilizadores a partir do Azure Ative Directory. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Dispositivos geridos conhecidos problemas e soluções alternativas

Ao [trazer os seus dispositivos para Azure AD,](../devices/overview.md)maximiza a produtividade dos seus utilizadores através de um único sign-on (SSO) através da sua nuvem e recursos no local.

### <a name="azure-ad-joined-devices"></a>Dispositivos associados ao Azure AD

Os dispositivos [aderidos a Azure AD](../devices/concept-azure-ad-join.md) são unidos diretamente ao Azure AD e permitem que os utilizadores inscrevam-se no dispositivo usando a identidade da sua organização.

**Problemas conhecidos** <br>
Os utilizadores podem experimentar problemas de súmuta único com aplicações que dependem do Azure AD para autenticação.

**Resolução** <br>
Os problemas mencionados nesta secção foram corrigidos na atualização do Windows 10 May 2020 (2004).

**Solução** <br>
Dê tempo suficiente para que a UPN mude para sincronizar com Azure AD. Assim que verificar se a nova UPN se reflete no Portal AD Azure, peça ao utilizador que selecione o azulejo "Outro utilizador" para iniciar súb9 com a sua nova UPN. Também pode verificar através [do PowerShell](/powershell/module/azuread/get-azureaduser). Depois de iniciar sessão com a sua nova UPN, as referências à antiga UPN podem ainda aparecer na definição do Windows "Access work ou school".

![Screenshot de domínios verificados](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos híbridos associados ao Azure AD

Os dispositivos [híbridos Azure AD juntam-se](../devices/concept-azure-ad-join-hybrid.md) ao Ative Directory e ao Azure AD. Pode implementar a ad Hybrid Azure se o seu ambiente tiver uma pegada de Ative Directory no local e também quiser beneficiar das capacidades fornecidas pela Azure AD.

**Problemas conhecidos** 

Os dispositivos AD Azure Ad híbridos do Windows 10 são suscetíveis de sofrer recomeçamentos inesperados e problemas de acesso.

Se os utilizadores iniciarem sessão no Windows antes de a nova UPN ter sido sincronizada com a AZure AD, ou continuar a utilizar uma sessão do Windows existente, poderão experimentar problemas de sinalização única com aplicações que utilizam Azure AD para autenticação se o Acesso Condicional tiver sido configurado para impor a utilização de dispositivos híbridos unidos para aceder aos recursos. 

Além disso, aparecerá a seguinte mensagem, forçando um recomeço após um minuto. 

"O seu PC reinicia automaticamente num minuto. O Windows teve um problema e precisa de reiniciar. Deve encerrar esta mensagem agora e salvar o seu trabalho".

**Resolução** <br>
Os problemas mencionados nesta secção foram corrigidos na atualização do Windows 10 May 2020 (2004).

**Solução** 

O aparelho deve ser desacompinado do Azure AD e reiniciado. Após o reinício, o dispositivo volta a juntar-se automaticamente ao Azure AD e o utilizador deve iniciar sação utilizando a nova UPN selecionando o azulejo "Outro utilizador". Para desacompir um dispositivo da Azure AD, executar o seguinte comando num pedido de comando:

**dsregcmd /leave**

O utilizador terá de [se reinscrevê-lo](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) para o Windows Hello for Business se estiver a ser utilizado. Os dispositivos Windows 7 e 8.1 não são afetados por este problema após alterações na UPN.


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Authenticator conhecido problemas e soluções alternativas

A sua organização poderá necessitar da utilização da [app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) para iniciar sômentou e aceder a aplicações e dados organizacionais. Apesar de um nome de utilizador poder aparecer na aplicação, a conta não está configurada para funcionar como um método de verificação até que o utilizador complete o processo de registo.

A [aplicação Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) tem quatro funções principais:

* Autenticação de vários fatores através de um código de notificação ou verificação de push

* Atuar como corretor de autenticação em dispositivos iOS e Android para fornecer um único sinal para aplicações que usam [a autenticação intermediada](../develop/msal-android-single-sign-on.md)

* Registo do dispositivo (também conhecido como Workplace Join) para Azure AD, que é um requisito para outras funcionalidades como Intune App Protection e Device Innd/Management,

* Sinal telefónico, que requer MFA e registo do dispositivo.

### <a name="multi-factor-authentication-with-android-devices"></a>Autenticação multi-factor com dispositivos Android

A aplicação Microsoft Authenticator oferece uma opção de verificação fora de banda. Em vez de colocar uma chamada telefónica automatizada ou SMS ao utilizador durante o sismo, [a Autenticação Multi-Factor (MFA)](../authentication/concept-mfa-howitworks.md) empurra uma notificação para a aplicação Microsoft Authenticator no smartphone ou tablet do utilizador. O utilizador simplesmente toca aprovação (ou introduz um PIN ou biométrico e toca "Authenticate") na aplicação para completar o seu insumo.

**Problemas conhecidos** 

Quando altera a UPN de um utilizador, a upn antiga ainda aparece na conta do utilizador e uma notificação pode não ser recebida. [Os códigos de verificação](../user-help/user-help-auth-app-faq.md) continuam a funcionar.

**Solução**

Se for recebida uma notificação, instrua o utilizador a dispensar a notificação, abra a aplicação Authenticator, toque na opção "Verificar as notificações" e aprove o pedido de MFA. Depois disso, a UPN exibida na conta será atualizada. Note que a UPN atualizada pode ser apresentada como uma nova conta, isto deve-se a outras funcionalidades autenticadoras que estão a ser utilizadas. Para mais informações consulte as questões adicionais conhecidas neste artigo.

### <a name="brokered-authentication"></a>Autenticação intermediada

Para corretores Android e iOS como o Microsoft Authenticator permitem:

* Único sinal de s-on (SSO) - Os seus utilizadores não precisarão de iniciar sina em cada aplicação.

* Identificação do dispositivo - O corretor acede ao certificado do dispositivo criado no dispositivo quando este foi aderido ao local de trabalho.

* Verificação de identificação de aplicação - Quando uma aplicação chama o corretor, passa o url de redirecionamento, e o corretor verifica-o.

Além disso, permite que as aplicações participem em funcionalidades mais avançadas, como [o Acesso Condicionado,](../conditional-access/index.yml)e suporta [cenários Microsoft Intune](../develop/msal-net-use-brokers-with-xamarin-apps.md).

**Problemas conhecidos**<br>
O utilizador é apresentado com pedidos de autenticação mais interativas em novas aplicações que utilizam o sign-in assistido por corretor devido a um desfasamento entre o login_hint passado pela aplicação e a UPN armazenadas no corretor.

**Solução** <br> O utilizador necessita de remover manualmente a conta do Microsoft Authenticator e iniciar um novo início de s início de uma aplicação assistida por corretor. A conta será automaticamente adicionada após a autenticação inicial.

### <a name="device-registration"></a>Registo de dispositivo

A aplicação Microsoft Authenticator é responsável por registar o dispositivo no Azure AD. O registo do dispositivo permite que o dispositivo autente para a Azure AD e é um requisito para os seguintes cenários:

* Intune App Protection

* Inscrição de dispositivo intune

* Sinal de telefone

**Problemas conhecidos**<br>
Quando muda a UPN, uma nova conta com a nova UPN aparece listada na aplicação Microsoft Authenticator, enquanto a conta com a antiga UPN ainda está listada. Além disso, a antiga UPN exibe na secção de Registo de Dispositivos nas definições da aplicação. Não há alterações na funcionalidade normal do Registo do Dispositivo ou nos cenários dependentes.

**Solução** <br> Para remover todas as referências à antiga UPN na aplicação Microsoft Authenticator, instrua o utilizador a remover manualmente as contas antigas e novas do Microsoft Authenticator, re-registar-se para MFA e voltar a juntar-se ao dispositivo.

### <a name="phone-sign-in"></a>Sindução de telefone

O s-in telefónico permite que os utilizadores entrem no Azure AD sem senha. Para ativar o registo do telefone, o utilizador precisa de se registar para MFA utilizando a aplicação Authenticator e, em seguida, ativar a entrada do telefone diretamente no Autenticador. Como parte da configuração, o dispositivo regista-se com Azure AD.

**Problemas conhecidos** <br>
Os utilizadores não podem utilizar o registo do Telefone porque não recebem qualquer notificação. Se o utilizador tocar no Check for Notifications, obtém-se um erro.

**Solução**<br>
O utilizador precisa de selecionar o menu suspenso na conta ativada para o sôm-in do Telefone e selecionar o registo do telefone desativado. Se desejar, o registo telefónico pode ser ativado novamente.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Chave de Segurança (FIDO2) questões conhecidas e soluções alternativas

**Problemas conhecidos** <br>
Quando vários utilizadores estão registados na mesma tecla, o sinal no ecrã mostra uma página de seleção de conta onde a antiga UPN é apresentada. As entradas de inscrição que utilizam Teclas de segurança não são afetadas por alterações da UPN.  

**Solução**<br>
Para remover referências a UPNs antigos, os utilizadores devem [redefinir a chave de segurança e voltar a registar- se](../authentication/howto-authentication-passwordless-security-key.md#known-issues).

## <a name="onedrive-known-issues-and-workarounds"></a>OneDrive conhecidos questões e soluções alternativas

Os utilizadores do OneDrive são conhecidos por experimentarem problemas após alterações na UPN. Para obter mais informações, consulte [como as alterações da UPN afetam as funcionalidades de URL e OneDrive OneDrive](/onedrive/upn-changes).

## <a name="next-steps"></a>Passos seguintes

Consulte estes recursos:
* [Azure AD Connect: Conceitos de design](./plan-connect-design-concepts.md)

* [População UserPrincipalName do Azure AD](./plan-connect-userprincipalname.md)

* [Fichas de ID da plataforma de identidade da Microsoft](../develop/id-tokens.md)
