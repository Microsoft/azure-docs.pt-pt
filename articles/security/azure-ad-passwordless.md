---
title: Compreendendo um mundo sem senhas com Azure Active Directory | Microsoft Docs
description: Este guia ajuda CEOs, CIOs, CISOss, arquitetos de identidade, arquitetos empresariais e tomadores de decisões de ti responsáveis por escolher um método de autenticação com senha para sua implementação de Azure Active Directory.
services: active-directory
keywords: com senha, azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: c1f1fc4a09cba469edfea060afea47053cb87ac4
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302193"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>Um mundo sem senhas com Azure Active Directory

É hora de dividir seu relacionamento com senhas. As senhas foram boas para nós no passado, mas no local de trabalho digital de hoje eles se tornaram um vetor de ataque relativamente fácil para hackers. Os hackers adoram as senhas e não é difícil perceber por que quando você considera que as senhas mais comumente rejeitadas no Azure Active Directory (AD do Azure) incluem termos como o ano, o mês, a temporada ou uma equipe esportiva local. Além disso, a [pesquisa mostrou](https://aka.ms/passwordguidance) que as recomendações tradicionais para o gerenciamento de senhas, como requisitos de comprimento, requisitos de complexidade e frequências de alteração, são comprodutivas por vários motivos relacionados à natureza humana.

Três tipos de ataques normalmente usados para comprometer as contas de usuário são a irrigação de senha, o phishing e a repetição de violação. Recursos do Azure AD, como [bloqueio inteligente](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout), [senhas banidas](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)e [proteção por senha](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) , podem ajudar a proteger contra esses tipos de ataques. Da mesma forma, implementar a MFA ( [autenticação](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) multifator) ou a verificação em duas etapas fornece segurança adicional, exigindo uma segunda forma de autenticação. Mas, a longo prazo, uma solução sem senha é a melhor solução para garantir o método de autenticação mais seguro.

Este artigo é o início de sua jornada para ajudá-lo a entender e implementar soluções com senha da Microsoft e ajudá-lo a escolher entre uma ou mais das seguintes opções:

* **Windows Hello para empresas**. No Windows 10, o Windows Hello para empresas substitui senhas com autenticação forte de dois fatores em PCs e dispositivos móveis. Essa autenticação consiste em um novo tipo de credencial de usuário que está vinculado a um dispositivo e usa uma biometria ou um PIN.

* **Entrada sem senha com Microsoft Authenticator**. O aplicativo Microsoft Authenticator pode ser usado para entrar em uma conta do Azure AD sem usar uma senha. Semelhante à tecnologia do Windows Hello para empresas, o Microsoft Authenticator usa a autenticação baseada em chave para habilitar uma credencial de usuário vinculada a um dispositivo e usa uma biométrica ou um PIN.

* **FIDO2 chaves de segurança**. O FIDO2 fornece credenciais de logon criptográfico que são exclusivas em todos os sites e são armazenadas em um dispositivo local, como o Windows Hello ou as chaves de segurança externas. Essas chaves de segurança são resistentes aos riscos de phishing, roubo de senha e ataques de repetição. Combinado com a verificação de usuário por meio de biometria ou PIN, a solução é a verificação de dois fatores que atende às necessidades de segurança modernas.

## <a name="the-future-of-passwordless-authentication"></a>O futuro da autenticação com senha

Atualmente, bancos, empresas de cartões de crédito e outras organizações e serviços online geralmente protegem sua conta, exigindo que você verifique sua identidade duas vezes: uma vez usando sua senha e, novamente, por telefone, texto ou um aplicativo. Embora a autenticação multifator resolva o problema de segurança de senhas sendo compartilhadas, roubadas ou adivinhadas, ela não resolve o fator de inconveniência de tentar se lembrar delas. O que os usuários e as organizações desejam na era da nuvem de hoje são métodos de autenticação sem senha que são altamente seguros *e* convenientes.

![Conveniência vs segurança](./media/azure-ad/azure-ad-pwdless-image1.png)

O Windows Hello para empresas, a entrada sem senha com o Microsoft Authenticator e as chaves de segurança FIDO2 compartilham uma arquitetura simples e comum que fornece aos usuários um método de autenticação que é altamente seguro e conveniente de usar. Todos os três são baseados em tecnologia pública/privada-chave, precisam de um gesto local, como biométrica ou PIN, e chaves privadas ligadas a um único dispositivo e armazenados com segurança e nunca compartilhados.

## <a name="windows-hello-for-business"></a>Windows Hello para Empresas

No Windows 10, o Windows Hello para empresas substitui senhas com autenticação forte de dois fatores em computadores e dispositivos. A autenticação consiste em um novo tipo de credencial de usuário que está vinculado a um dispositivo e usa um gesto ou PIN biométrico que permite que os usuários se autentiquem no Azure AD, bem como em um Active Directory local. Simplesmente entrar em um dispositivo usando o Windows Hello para empresas é fácil. Você usa um gesto de PIN ou biométrica, como uma impressão digital ou um reconhecimento facial.

### <a name="windows-hello-for-business-scenarios"></a>Cenários do Windows Hello para empresas

O Windows Hello para empresas é ideal para os operadores de informações que têm seu próprio computador Windows designado. A biométrica e as credenciais estão diretamente ligadas ao computador do usuário, o que impede o acesso de qualquer pessoa que não seja o proprietário. Com a integração PKI e o suporte interno para SSO (logon único), o Windows Hello para empresas fornece um método simples e conveniente para acessar diretamente os recursos corporativos locais e na nuvem.

### <a name="windows-hello-for-business-deployment-considerations"></a>Considerações de implantação do Windows Hello para empresas

O Windows Hello for Business é um sistema distribuído que usa vários componentes para realizar o registro, o provisionamento e a autenticação de dispositivos. Portanto, a implantação requer um planejamento adequado em várias equipes na organização. O [Guia de planejamento](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) do Windows Hello para empresas pode ser usado para ajudá-lo a tomar decisões sobre o tipo de implantação do Windows Hello para empresas e as opções que você precisará considerar.

Há muitas opções que você pode escolher ao implantar o Windows Hello para empresas. Fornecer várias opções garante que quase todas as organizações possam implantar o Windows Hello para empresas. Considere os seguintes tipos de implantações com suporte:

* [Implantação de confiança de chave unida do Azure AD híbrido](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Implantação de confiança de certificado ingressado no Azure AD híbrido](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Guias de implantação de logon único do ingresso no Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [Implantação de confiança de chave local](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [Implantação de confiança de certificado local](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Fornecer muitas opções faz com que a implantação pareça complexa. No entanto, a maioria das organizações provavelmente determinará que já implementaram a maior parte da infraestrutura na qual a implantação do Windows Hello para empresas depende. Independentemente disso, é importante entender que o Windows Hello for Business é um sistema distribuído e um planejamento adequado é recomendado.

Recomendamos que você leia [planejando uma implantação do Windows Hello para empresas](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) para ajudá-lo a decidir sobre o modelo de implantação mais adequado para sua organização em particular. Em seguida, com base no planejamento que você faz, consulte o [Guia de implantação do Windows Hello para empresas](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) para ajudar a garantir uma implantação bem-sucedida do Windows Hello para empresas em seu ambiente existente.

### <a name="how-windows-hello-for-business-works"></a>Como funciona o Windows Hello para empresas

#### <a name="user-sets-up-windows-hello-for-business"></a>O usuário configura o Windows Hello para empresas

Após uma verificação inicial em duas etapas do usuário durante o registro, o Windows Hello é configurado no dispositivo do usuário e o Windows solicita que o usuário defina um gesto, que pode ser uma biométrica, como uma impressão digital ou um reconhecimento facial ou um PIN. Uma vez definido, o usuário fornece o gesto para verificar sua identidade. Em seguida, o Windows usa o Windows Hello para autenticar usuários.

Com base nos recursos do seu dispositivo Windows 10, você terá um enclave seguro interno, conhecido como um TPM (Trusted Platform Module) de hardware ou um TPM de software. O TPM armazena a chave privada, que requer sua face, impressão digital ou PIN para desbloqueá-la. Os dados biométricos não são transferidos por roaming e nunca são enviados a dispositivos ou servidores externos. Não há nenhum ponto de coleção único que um invasor possa comprometer para roubar dados biométricos, pois o Windows Hello armazena apenas dados de identificação biométricas no dispositivo.

> [!TIP]
> Na superfície, um PIN parece uma senha, mas é realmente mais seguro. Uma diferença importante entre uma senha e um PIN é que o PIN está vinculado ao dispositivo específico no qual ele foi configurado. Alguém que rouba sua senha pode entrar em sua conta de qualquer lugar. Mas se eles roubarem seu PIN, precisarão roubar seu dispositivo físico também! Além disso, como um PIN é local para o dispositivo, ele não é transmitido em nenhum lugar para que não possa ser interceptado na transmissão ou roubado de um servidor.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Usuário usando o Windows Hello para empresas para entrar

A biometria e os PINs do Windows Hello para empresas usam criptografia assimétrica (chave pública/privada) para autenticação. Durante a autenticação, a criptografia é vinculada à chave de sessão TLS, que protege o processo de autenticação de forma que um ataque man-in-the-Middle (MiTM) não possa roubar o artefato de segurança resultante ou a reprodução de outros locais.

O Windows Hello para empresas fornece uma experiência de entrada conveniente que autentica o usuário no Azure AD e Active Directory recursos. Os dispositivos ingressados no Azure AD são autenticados no Azure durante a entrada e, opcionalmente, podem se autenticar no Active Directory. Dispositivos ingressados no Azure Active Directory híbrido autenticam para Active Directory durante a entrada e se autenticam para Azure Active Directory em segundo plano.

![Consulte a imagem de origem](./media/azure-ad/azure-ad-pwdless-image2.jpeg)

As etapas a seguir ilustram a autenticação de entrada para Azure Active Directory.

![Tela de bloqueio do Windows 10](./media/azure-ad/azure-ad-pwdless-image3.png)

1. O usuário entra no Windows usando biométrica ou gesto de PIN. O gesto desbloqueia a chave privada do Windows Hello para empresas e é enviado para o provedor de suporte de segurança de autenticação de nuvem, chamado de provedor de AP de nuvem.

2. O provedor de AP de nuvem solicita um nonce de Azure Active Directory.

3. O Azure AD retorna um nonce válido por 5 minutos.

4. O provedor de AP de nuvem assina o nonce usando a chave privada do usuário e retorna o nonce assinado para a Azure Active Directory.

5. Azure Active Directory valida o nonce assinado usando a chave pública registrada com segurança do usuário em relação à assinatura de nonce. Depois de validar a assinatura, o Azure AD validará o nonce assinado retornado. Depois de validar o nonce, o Azure AD cria um PRT com a chave de sessão que é criptografada para a chave de transporte do dispositivo e a retorna para o provedor de AP de nuvem.

6. O provedor de AP de nuvem recebe o PRT criptografado com chave de sessão. Usando a chave de transporte privada do dispositivo, o provedor de AP de nuvem descriptografa a chave de sessão e protege a chave de sessão usando o TPM do dispositivo.

7. O provedor de AP de nuvem retorna uma resposta de autenticação bem-sucedida para o Windows após o qual o usuário é capaz de acessar o Windows, bem como aplicativos locais e na nuvem sem a necessidade de autenticar novamente (SSO).

Para obter uma análise mais profunda do processo de autenticação em outros cenários que envolvem o Windows Hello para empresas, consulte [Windows Hello para empresas e autenticação](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#Azure-AD-join-authentication-to-Active-Directory-using-a-Key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>O usuário gerencia suas credenciais do Windows Hello para empresas

Os [Serviços](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset) de redefinição de PIN da Microsoft são um recurso do Azure AD que permite que os usuários redefinam seu PIN, se necessário. Usando a política de grupo, Microsoft Intune ou um MDM compatível, um administrador pode configurar dispositivos Windows 10 para usar com segurança o serviço de redefinição de PIN da Microsoft que permite que os usuários redefinam seu PIN esquecido por meio de configurações ou acima da tela de bloqueio sem exigir novo registro.

Às vezes, os usuários precisam fazer fallback para usar senhas. [Redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) (SSPR) é outro recurso do Azure AD que permite que os usuários redefinam suas senhas sem precisar entrar em contato com a equipe de ti. Os usuários devem se registrar no ou ser registrados para redefinição de senha de autoatendimento antes de usar o serviço. Durante o registro, o usuário escolhe um ou mais métodos de autenticação habilitados pela organização. O SSPR permite que os usuários sejam desbloqueados rapidamente e continuem trabalhando independentemente de onde estiverem ou a hora do dia. Ao permitir que os usuários se desbloqueiem, sua organização pode reduzir o tempo não produtivo e os altos custos de suporte para os problemas mais comuns relacionados a senhas.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Entrada sem senha com o Microsoft Authenticator

A conexão sem senha com o Microsoft Authenticator é outra solução sem senha que pode ser usada para entrar em contas do Azure AD usando a entrada pelo telefone. Você ainda deve verificar sua identidade fornecendo algo que você conhece e algo que você tem, mas a entrada pelo telefone permite que você ignore a inserção da sua senha e execute toda a verificação de identidade em seu dispositivo móvel usando sua impressão digital, face ou PIN.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator cenários com senha

O aplicativo Microsoft Authenticator permite que os usuários verifiquem sua identidade e se autentiquem em suas contas corporativas ou pessoais. Ele também pode ser usado para ampliar uma senha com senha de uso único ou de envio por Push ou substituir a necessidade de uma senha completamente. Em vez de usar uma senha, os usuários confirmam sua identidade usando seu telefone celular por meio de verificação de impressão digital, reconhecimento facial ou íris ou PIN. Baseado em tecnologia segura semelhante ao que o Windows Hello usa, essa ferramenta é empacotada em um aplicativo simples em um dispositivo móvel, tornando-a uma opção conveniente para os usuários. O aplicativo Microsoft Authenticator está disponível para Android e iOS.

### <a name="microsoft-authenticator-deployment-considerations"></a>Microsoft Authenticator considerações sobre implantação

Os pré-requisitos para usar o aplicativo Microsoft Authenticator para fazer logon sem senha no Azure AD incluem o seguinte:

* Os usuários finais estão habilitados para a autenticação multifator do Azure

* A capacidade para os usuários registrarem seus dispositivos usando Microsoft Intune ou uma solução de MDM (gerenciamento de dispositivo móvel) de terceiros

Supondo que esses requisitos sejam atendidos, os administradores habilitam a entrada por telefone sem senha no locatário usando o [Windows PowerShell.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) Quando a entrada pelo telefone está habilitada no locatário, os usuários finais podem optar por entrar usando seu telefone selecionando sua conta corporativa ou de estudante na tela **contas** do aplicativo e, em seguida, selecionando **Habilitar entrada pelo telefone**.

Supondo que a entrada sem senha seja habilitada por um administrador, os usuários finais precisarão atender aos seguintes requisitos:

* Registrado na autenticação multifator do Azure

* Versão mais recente do Microsoft Authenticator instalada em dispositivos que executam o iOS 8,0 ou superior, ou Android 6,0 ou superior

* Conta corporativa ou de estudante com notificações por push adicionadas ao aplicativo

Para evitar o potencial de ser bloqueado da sua conta ou de recriar contas em um novo dispositivo, é recomendável que você use Microsoft Authenticator para [fazer backup de suas credenciais de conta](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-backup-recovery) na nuvem. Após o backup, você também pode usar o aplicativo para recuperar suas informações em um novo dispositivo, potencialmente evitando o bloqueio ou a recriação de contas.

Como a maioria dos usuários está acostumado a usar apenas senhas para autenticar, é importante que sua organização instrua os usuários sobre esse processo. A conscientização pode reduzir a probabilidade de que os usuários liguem para o suporte técnico para quaisquer [problemas](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#known-issues) relacionados à entrada usando o aplicativo Microsoft Authenticator.

> [!NOTE]
> Um possível ponto de falha para essa solução é quando um usuário móvel está em um local em que não há conectividade com a Internet. As chaves de segurança do FIDO2 e do Windows Hello para empresas não estão sujeitas à mesma limitação.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Como a entrada sem senha com o Microsoft Authenticator funciona

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>O usuário configura a entrada sem senha com o Microsoft Authenticator

Antes que o aplicativo Microsoft Authenticator possa ser usado como uma solução sem senha para entrar em uma conta do Azure AD, as etapas devem ser executadas tanto pelo administrador quanto pelos usuários finais.

Primeiro, um administrador precisará [habilitar o uso do aplicativo como uma credencial](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) no locatário usando o Windows PowerShell. O administrador também precisará habilitar os usuários finais para a autenticação multifator do Azure (Azure MFA) e configurar o aplicativo Microsoft Authenticator como um dos [métodos de verificação](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods).

Os usuários finais precisarão [baixar e instalar](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) o aplicativo Microsoft Authenticator e [configurar sua conta](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app) para usar o aplicativo Microsoft Authenticator como um dos métodos de verificação.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Usuário usando Microsoft Authenticator para entrada sem senha

O aplicativo Microsoft Authenticator pode ser usado para entrar em qualquer conta do Azure AD sem usar uma senha. Embora a tela de bloqueio do Windows 10 não inclua o aplicativo Microsoft Authenticator como uma opção de entrada, os usuários ainda podem inserir seu nome de usuário e receber uma notificação por push em seu dispositivo móvel para verificar a presença. Os usuários confirmam sua presença combinando um número na tela de entrada e, em seguida, fornecendo uma verificação facial, impressão digital ou PIN para desbloquear a chave privada e concluir a autenticação. Esse método de verificação multifator é mais seguro do que uma senha e mais conveniente do que inserir uma senha e um código.

![Entrada no autenticador](./media/azure-ad/azure-ad-pwdless-image4.png)

A autenticação sem senha usando Microsoft Authenticator segue o mesmo padrão básico do Windows Hello para empresas, mas é um pouco mais complicada, já que o usuário precisa ser identificado para que o Azure AD possa encontrar a versão do aplicativo Microsoft Authenticator utiliza.

![Processo do autenticador](./media/azure-ad/azure-ad-pwdless-image5.png)

1. O usuário insere seu nome de usuário.

2. O Azure AD detecta que o usuário tem uma credencial forte e inicia o fluxo de credenciais forte.

3. A notificação é enviada ao aplicativo via Apple Push Notification Service (APNS) em dispositivos iOS ou via firebase Cloud Messaging (FCM) em dispositivos Android.

4. O usuário recebe a notificação por push e abre o aplicativo.

5. O aplicativo chama o Azure AD e recebe um desafio de prova de presença e um nonce.

6. O usuário conclui o desafio inserindo sua biométrica ou PIN para desbloquear a chave privada.

7. O nonce é assinado com a chave privada e enviado de volta para o Azure AD.

8. O Azure AD executa a validação de chave pública/privada e retorna um token.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>O usuário gerencia sua entrada sem senha com credenciais de Microsoft Authenticator

Com o [registro combinado](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined), os usuários podem registrar e obter os benefícios da autenticação multifator do Azure e da redefinição de senha de autoatendimento. Os usuários registram e gerenciam essas configurações navegando até a [página meu perfil](https://aka.ms/mysecurityinfo). Além de habilitar o SSPR, o registro combinado dá suporte a vários métodos e ações de autenticação.

## <a name="fido2-security-keys"></a>Chaves de segurança do FIDO2

FIDO2 é a versão mais recente do FIDO Alliance Standard e tem dois componentes – W3C's padrão de autenticação na Web (webauthn) e o CTAP2 (protocolo para autenticação) do cliente de aliança FIDO correspondente. Os padrões FIDO2 permitem que os usuários aproveitem autenticadores baseados em hardware, móveis e biométricos para autenticar facilmente com muitos aplicativos e sites em ambientes móveis e de área de trabalho.

Os parceiros da Microsoft e do setor trabalham juntos em dispositivos de segurança FIDO2 para o Windows Hello para permitir a autenticação segura e fácil em dispositivos compartilhados. As chaves de segurança do FIDO2 permitem que você carregue suas credenciais com você e autentique-se com segurança em um dispositivo Windows 10 ingressado no [Azure ad](https://aka.ms/azuread418)que faz parte de sua organização.

Webauthn define uma API que permite o desenvolvimento e a implementação de autenticação forte e com senha por aplicativos Web e serviços. O protocolo CTAP permite que dispositivos externos, como chaves de segurança compatíveis com FIDO, trabalhem com o webauthn e sirvam como autenticadores. Com a autenticação da Web, os usuários podem entrar no serviços online com as chaves de segurança de face, impressão digital, PIN ou FIDO2 portáteis, utilizando credenciais de chave pública forte em vez de senhas. Atualmente, há suporte para webauthn no Microsoft Edge e o suporte para Chrome e Firefox está em desenvolvimento.

Os dispositivos e tokens que aderem aos protocolos FIDO2, webauthn e CTAP trazem uma solução de plataforma cruzada de autenticação forte sem usar senhas. Os parceiros da Microsoft estão trabalhando em uma variedade de fatores forma de chave de segurança, como chaves de segurança USB e cartões inteligentes habilitados para NFC.

### <a name="fido2-security-keys-scenarios"></a>Cenários de chaves de segurança FIDO2

As chaves de segurança do FIDO2 podem ser usadas para entrar no Azure AD escolhendo a chave de segurança como o provedor de credenciais na tela de bloqueio do Windows 10. Um nome de usuário ou senha não é necessário, o que o torna uma solução ideal para os funcionários de primeira linha que compartilham PCs entre vários usuários. Eles também são uma excelente opção de autenticação quando as políticas corporativas ditam que as credenciais de um usuário devem ser fisicamente separadas do seu dispositivo. Os usuários também podem optar por entrar em sites usando sua chave de segurança FIDO2 dentro do navegador Microsoft Edge no Windows 10 versão 1809 ou superior.

### <a name="fido2-security-keys-deployment-considerations"></a>Considerações de implantação de chaves de segurança FIDO2

Os administradores podem habilitar o suporte do FIDO2 no Azure AD e atribuir a capacidade a usuários ou grupos. As políticas também podem ser criadas para o modo como as chaves são provisionadas e configurar restrições, como permitir ou bloquear um conjunto específico de chaves de segurança de hardware. As chaves devem ser fisicamente distribuídas aos usuários finais.

**Os requisitos para habilitar a entrada sem senha no Azure AD e sites usando chaves de segurança FIDO2 incluem o seguinte:**

* Azure AD

* Multi-Factor Authentication do Azure

* Visualização de registro combinado

* A visualização da chave de segurança do FIDO2 requer uma chave de segurança FIDO2 compatível

* A autenticação da Web (webauthn) requer o Microsoft Edge no Windows 10 versão 1809 ou superior

* O logon do Windows baseado em FIDO2 requer que o Azure AD tenha ingressado no Windows 10 versão 1809 ou superior (a melhor experiência é no Windows 10 versão 1903 ou superior)

Os fatores de forma compatíveis com FIDO2 incluem dispositivos USB, NFC e Bluetooth. Recomendamos que você escolha o fator forma que atenda às suas necessidades específicas, já que algumas plataformas e navegadores ainda não estão em conformidade com o FIDO2.

Também recomendamos que cada organização crie um protocolo para que os usuários e os administradores sigam se uma chave de segurança for perdida ou roubada. Os usuários devem relatar a chave perdida ou roubada para que os administradores ou o usuário possam excluir suas chaves de segurança do perfil do usuário e provisionar uma nova.

### <a name="how-fido2-security-keys-works"></a>Como funcionam as chaves de segurança do FIDO2

#### <a name="user-sets-up-fido2-security-key"></a>O usuário configura a chave de segurança FIDO2

Embora os administradores possam provisionar [manualmente as chaves](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) e distribuí-las aos usuários finais, o provisionamento e a habilitação do provedor de credenciais FIDO2 na tela de bloqueio do Windows 10 terão suporte por meio do [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). Os administradores também precisarão usar o [portal do Azure](https://portal.azure.com/) para habilitar dispositivos de token de hardware como um método de autenticação com senha.

A implantação de chaves de segurança do FIDO2 também exige que os usuários registrem suas chaves usando o [registro combinado](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined). Com o registro combinado, os usuários se registram uma vez e obtêm os benefícios da autenticação multifator do Azure e da SSPR (redefinição de senha de logon único).

Além de selecionar o token de hardware como o método de autenticação multifator padrão, é recomendável que você também selecione uma opção de verificação adicional.

* Microsoft Authenticator--notificação

* Aplicativo autenticador ou token de hardware--código

* Chamada telefônica

* Mensagem de texto

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Usuário usando a chave de segurança FIDO2 para entrar

O FIDO2 fornece uma camada de abstração entre o fator forma usado como o autenticador e a criptografia pública/privada-Key para habilitar autenticadores de plataforma internos, como o Windows Hello e as chaves de segurança para resolver para uma chave privada e entregar uma chave pública Isso pode ser usado como um identificador para acessar recursos externos. As chaves de segurança do FIDO2 são equipadas com suas próprias enclave seguras internas que armazenam a chave privada e exigem a biométrica ou o PIN para desbloqueá-la. As credenciais não podem ser reutilizadas, reproduzidas ou compartilhadas entre serviços e não estão sujeitas a ataques de phishing e MiTM ou violações do servidor.

![Entrada FIDO2](./media/azure-ad/azure-ad-pwdless-image6.png)

As chaves de segurança FIDO2 fornecem autenticação segura, independentemente do fator forma. A chave de segurança contém a credencial e deve ser protegida com um segundo fator adicional, como uma impressão digital (integrada à chave de segurança) ou um PIN a ser inserido na entrada do Windows. Os parceiros da Microsoft estão trabalhando em uma variedade de fatores forma de chave de segurança. Alguns exemplos incluem chaves de segurança USB e cartões inteligentes habilitados para NFC.

> [!NOTE]
> Uma chave de segurança deve implementar determinados recursos e extensões do protocolo FIDO2 CTAP para ser [compatível](https://aka.ms/fido2securitykeys)com a Microsoft. A Microsoft testou essas soluções para compatibilidade com o Windows 10 e o Azure Active Directory.

![Processo de entrada do FIDO2](./media/azure-ad/azure-ad-pwdless-image9.png)

1. O usuário conecta o dispositivo FIDO2 ao computador.

2. O Windows detecta a chave de segurança FIDO2.

3. O Windows envia uma solicitação de autenticação.

4. O Azure AD envia de volta um nonce.

5. O usuário conclui seu gesto para desbloquear a chave privada armazenada na enclave segura da chave de segurança do FIDO2.

6. A chave de segurança FIDO2 assina o nonce com a chave privada.

7. A solicitação de token de PRT com nonce assinado é enviada ao Azure AD.

8. O Azure AD verifica o nonce assinado usando a chave pública FIDO2.

9. O Azure AD retorna o PRT para habilitar o acesso a recursos locais.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>O usuário gerencia suas credenciais de chave de segurança do FIDO2

Semelhante ao aplicativo Microsoft Authenticator, o gerenciamento de credenciais para chaves de segurança FIDO2 depende da experiência de registro combinada para os usuários finais.

## <a name="deciding-a-passwordless-method"></a>Decidindo um método com senha

A escolha entre essas três opções de senha depende dos requisitos de segurança, plataforma e aplicativo da sua empresa.

Aqui estão alguns fatores a serem considerados ao escolher a tecnologia sem senha da Microsoft:

||**Windows Hello para empresas**|**Entrada sem senha com o aplicativo Microsoft Authenticator**|**Chaves de segurança do FIDO2**|
|:-|:-|:-|:-|
|**Pré-requisito**| Windows 10, versão 1809 ou posterior<br>Azure Active Directory| Aplicação Microsoft Authenticator<br>Telefone (dispositivos iOS e Android que executam o Android 6,0 ou superior.)|Windows 10, versão 1809 ou posterior<br>Azure Active Directory|
|**Moda**|Plataforma|Software|Hardware|
|**Sistemas e dispositivos**|PC com um Trusted Platform Module interno (TPM)<br>Reconhecimento de PIN e Biometria |Reconhecimento de PIN e biometria no telefone|Dispositivos de segurança FIDO2 que são compatíveis com a Microsoft|
|**Experiência do usuário**|Entre usando um PIN ou um reconhecimento biométrico (facial, íris ou impressão digital) com dispositivos Windows.<br>A autenticação do Windows Hello está vinculada ao dispositivo; o usuário precisa do dispositivo e de um componente de entrada, como um PIN ou um fator biométrico para acessar recursos corporativos.|Entre usando um telefone celular com verificação de impressão digital, reconhecimento facial ou íris ou PIN.<br>Os usuários entram na conta corporativa ou pessoal de seu PC ou telefone celular.|Entrar usando o dispositivo de segurança FIDO2 (biometria, PIN e NFC)<br>O usuário pode acessar o dispositivo com base nos controles da organização e autenticar com base no PIN, a biometria usando dispositivos como chaves de segurança USB e cartões inteligentes, chaves ou wearables habilitados para NFC.|
|**Cenários habilitados**| Experiência sem senha com o dispositivo Windows.<br>Aplicável a um PC de trabalho dedicado com capacidade de logon único para dispositivos e aplicativos.|Solução sem senha em qualquer lugar usando o telefone celular.<br>Aplicável para acessar aplicativos pessoais ou de trabalho na Web de qualquer dispositivo.|Experiência sem senha para trabalhadores que usam biometria, PIN e NFC.<br>Aplicável a PCs compartilhados e onde um telefone celular não é uma opção viável (por exemplo, para pessoal de suporte técnico, quiosque público ou equipe de hospital)|

Use a tabela a seguir para escolher qual método dará suporte a seus requisitos e usuários.

|Persona|Cenário|Ambiente|Tecnologia com senha|
|:-|:-|:-|:-|
|**ADM**|Proteger o acesso a um dispositivo para tarefas de gerenciamento|Dispositivo Windows 10 atribuído|Chave de segurança do Windows Hello para empresas e/ou FIDO2|
|**ADM**|Tarefas de gerenciamento em dispositivos que não são do Windows| Dispositivo móvel ou não Windows|Entrada sem senha com o aplicativo Microsoft Authenticator|
|**Operador de informações**|Trabalho de produtividade|Dispositivo Windows 10 atribuído|Chave de segurança do Windows Hello para empresas e/ou FIDO2|
|**Operador de informações**|Trabalho de produtividade| Dispositivo móvel ou não Windows|Entrada sem senha com o aplicativo Microsoft Authenticator|
|**Frente Worker**|Quiosques em uma fábrica, fábrica, varejo ou entrada de dados|Dispositivos Windows 10 compartilhados|Chaves de segurança do FIDO2|

## <a name="getting-started"></a>Introdução

A autenticação sem senha é a onda do futuro e o caminho para um ambiente mais seguro. É recomendável que as organizações comecem a planejar essa alteração e reduza suas dependências em senhas. Para começar, considere os seguintes objetivos:

* Habilitar usuários para MFA + Microsoft Authenticator aplicativo + acesso condicional.

* Distribuição de proteção de senha do Azure AD + políticas de atualização.

* Habilite usuários para SSPR com o registro combinado.

* Implante Microsoft Authenticator aplicativo para mobilidade.

* Implante o Windows Hello para empresas (1903: Mantenha-se atualizado).

* Implantar dispositivos FIDO2 para usuários que não podem usar telefones.

* Quando possível, desabilite a autenticação baseada em senha.

Para atingir essas metas, recomendamos a seguinte abordagem:

1. Habilite Azure Active Directory para aproveitar ao máximo os recursos, como o Azure MFA e o acesso condicional.

2. Habilite a autenticação multifator para fornecer proteção adicional.

3. Habilite a redefinição de senha de autoatendimento no evento que os usuários precisam retornar para usar uma senha.

4. Implante Microsoft Authenticator entrada no telefone para a mobilidade adicionada.

5. Implante o Windows Hello para empresas em todos os seus dispositivos Windows 10.

6. Preparar-se para as chaves de segurança do FIDO2.

> [!NOTE]
> Consulte a página de [licenciamento](https://azure.microsoft.com/pricing/details/active-directory/) Azure Active Directory para obter detalhes sobre os requisitos de licenciamento para métodos com senha.

## <a name="conclusion"></a>Conclusão

Nos últimos anos, a Microsoft continuou seu compromisso com a habilitação de um mundo sem senhas. Com o Windows 10, a Microsoft introduziu o Windows Hello para empresas, uma credencial de dois fatores protegida por hardware forte que habilita o logon único para Azure Active Directory e Active Directory. Semelhante à tecnologia do Windows Hello para empresas, o aplicativo Microsoft Authenticator usa a autenticação baseada em chave para habilitar uma credencial de usuário vinculada a um dispositivo móvel e usa uma biometria ou um PIN. Agora, as chaves de segurança FIDO2 permitem que você carregue sua credencial com você e entre no Azure AD escolhendo a chave de segurança como o provedor de credenciais na tela de bloqueio do Windows 10. Todas as três soluções sem senha reduzem o risco de ataques de phishing, de irrigação de senha e de repetição e fornecem aos usuários uma maneira altamente segura e conveniente de entrar e acessar dados de qualquer lugar.

A adoção de tecnologias de autenticação multifator modernas, como biometria e criptografia de chave pública em dispositivos amplamente acessíveis, é uma das etapas mais impactantes que podem reduzir de forma significativa o risco de identidade de uma empresa. A passagem de senha é uma abordagem de longo prazo para autenticação segura e ainda está em evolução. Dadas os requisitos emergentes, as organizações podem se preparar fazendo um plano para começar a migrar para tecnologias com senha.

## <a name="next-steps"></a>Passos Seguintes

* Uma visão geral do [que é com senha?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
* [Como habilitar senhas no Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)
