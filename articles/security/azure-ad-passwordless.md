---
title: Noções básicas sobre um mundo sem palavras-passe no Azure Active Directory | Documentos da Microsoft
description: Este guia ajuda CEOs, Diretores de informática, officers, Arquitetos de identidade do diretor, Enterprise Architects e segurança e tomadores de decisão de TI responsáveis por escolher um método de autenticação de início de sua implementação do Azure Active Directory.
services: active-directory
keywords: passwordless, azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 00f66b6010bead3de131095a47ba1e419d2511c0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723444"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>Um mundo sem palavras-passe no Azure Active Directory

É hora de dividir a sua relação com palavras-passe. As palavras-passe tenham sido boas para nós no passado, mas, na área de trabalho digital de hoje eles se tornaram um vetor de ataque relativamente fácil para hackers. Os hackers gosta de usar as palavras-passe e não é difícil ver por que quando considera que mais frequentemente rejeitadas palavras-passe no Azure Active Directory (Azure AD) incluem termos como o ano, mês, a temporada de ou local tem de equipe. Além disso, [pesquisas mostram](https://aka.ms/passwordguidance) que tradicional recomendações para a gestão de palavra-passe como requisitos de comprimento, requisitos de complexidade e frequências de alteração são contraproducentes por uma série de motivos relacionados com natureza humana.

Três tipos de ataques que normalmente utilizados para comprometer as contas de utilizador são spray de palavra-passe, phishing e violações de repetição. O Azure AD funcionalidades como [bloqueio inteligente](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout), [palavras-passe banidas](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), e [proteção de palavra-passe](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) pode ajudar a proteger contra esses tipos de ataques. Da mesma forma, implementando [multi-factor authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) (MFA), ou a verificação de dois passos, fornece segurança adicional, exigindo que uma segunda forma de autenticação. Mas a longo prazo, uma solução de início é a melhor solução para garantir que o método mais seguro de autenticação.

Este artigo é o início da sua jornada para o ajudar a compreender e implementar soluções de início da Microsoft e o ajudaremos a que escolher entre uma ou mais das seguintes opções:

* **Windows Hello para empresas**. No Windows 10, Windows Hello para empresas substitui as palavras-passe com autenticação de dois fatores forte em PCs e dispositivos móveis. Esta autenticação é composta por um novo tipo de credencial do usuário que está associado a um dispositivo e utiliza um biometria ou PIN.

* **Passe início de sessão com o Microsoft Authenticator**. A aplicação Microsoft Authenticator pode ser utilizada para iniciar sessão a uma conta do Azure AD sem utilizar uma palavra-passe. Assim como a tecnologia do Windows Hello para empresas, o Microsoft Authenticator utiliza a autenticação baseada em chave para ativar uma credencial de utilizador que está associada a um dispositivo e utiliza um biometria ou PIN.

* **Chaves de segurança de FIDO2**. FIDO2 fornece as credenciais de início de sessão criptográfica que são exclusivos em cada site e são armazenadas num dispositivo local, como o Windows Hello ou chaves de segurança externas. Essas chaves de segurança são resistentes aos riscos de phishing, roubo de palavra-passe e ataques de repetição. Combinado com verificação de utilizadores através de Biometria ou PIN, a solução é duas necessidades de segurança moderno de reunião de verificação de fator.

## <a name="the-future-of-passwordless-authentication"></a>O futuro da autenticação de início

Hoje em dia, bancos, empresas de cartão de crédito e outras organizações e serviços online, muitas vezes, protegem sua conta, exigindo-lhe verificar a sua identidade duas vezes: uma vez, utilizando a palavra-passe, novamente, em seguida, por telefone, texto ou uma aplicação. Embora a autenticação multifator resolve o problema de segurança de palavras-passe que está a ser partilhado, roubados ou que adivinhou, não atende o fator de inconveniência de tentar lembrá-los. O que os utilizadores e organizações querem na era da cloud de hoje é métodos de autenticação de início que sejam altamente seguros *e* conveniente.

![Segurança de vs conveniência](./media/azure-ad/azure-ad-pwdless-image1.png)

Windows Hello comercial, passe para início de sessão com o Microsoft Authenticator e chaves de segurança de FIDO2 todos compartilham uma arquitetura comum simple que fornecem aos utilizadores um método de autenticação que é altamente segura e conveniente para utilizar. Todos os três baseiam-se na tecnologia de chave pública/privada, necessidade de um gesto de local, como um biometria ou PIN e chaves privadas vinculado a um único dispositivo e em segurança armazenada e nunca partilhados.

## <a name="windows-hello-for-business"></a>Windows Hello para Empresas

No Windows 10, Windows Hello para empresas substitui as palavras-passe com autenticação de dois fatores forte em PCs e dispositivos. A autenticação consiste num novo tipo de credencial do usuário que está associado a um dispositivo e utiliza um gesto de Biometria ou PIN que permite aos utilizadores autenticar para o Azure AD, bem como um Active Directory no local. Simplesmente assinatura num dispositivo usando o Windows Hello para empresas é fácil. Utiliza um PIN ou biométrico gesto, como reconhecimento de impressões digitais ou facial.

### <a name="windows-hello-for-business-scenarios"></a>Windows Hello para cenários de negócios

Windows Hello para empresas é ideal para os operadores de informações que possuem seus próprios PCs Windows designado. O biométrica e as credenciais estão diretamente associadas a PC do utilizador, o que impede o acesso a partir de qualquer pessoa que não seja o proprietário. Com integração de PKI e suporte incorporado para o início de sessão único (SSO), Windows Hello para empresas fornece um método simples e conveniente de forma totalmente integrada aceder a recursos da empresa no local e na cloud.

### <a name="windows-hello-for-business-deployment-considerations"></a>Windows Hello para considerações de implementação de negócios

Windows Hello para empresas é um sistema distribuído que utiliza vários componentes para efetuar o registo do dispositivo, o aprovisionamento e a autenticação. Portanto, a implementação requer um planeamento adequado por várias equipes na organização. O Windows Hello para empresas [guia de planeamento](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) podem ser utilizados para ajudar a tomar decisões sobre o tipo do Windows Hello para a implantação de negócios e as opções que precisará considerar.

Para implementações híbridas ou no local, espera-se que tem:

* Uma rede de trabalho bem conectada,

* Acesso à Internet

* Servidor do multi-factor Authentication para suportar MFA durante o Windows Hello para empresas de aprovisionamento

* Resolução de nome adequada, nomes internos e externos

* Um número adequado de controladores de domínio por site e do Active Directory para suportar a autenticação

* Serviços de certificados do Active Directory 2012 ou posteriores

* Um ou mais computadores de estação de trabalho com o Windows 10, versão 1903

Desde que todos os tipos do Windows Hello para implementações de negócios utilizem certificados emitidos pela empresa para controladores de domínio como uma raiz de confiança, vai querer renovar automaticamente certificados expirados por [Configurar inscrição automática de servidor e utilizador certificados](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/configure-server-certificate-autoenrollment). Para implementações no local, o fornecedor de identidade é o servidor no local a executar a função do Windows Server Active Directory Federation Services (AD FS). Sistemas federados normalmente exigem uma matriz com balanceamento de carga dos servidores, conhecido como um farm. Este farm está configurado numa rede interna e a topologia de rede de perímetro para garantir a elevada disponibilidade para pedidos de autenticação.

Ao definir uma política de grupo para exigir Windows Hello para empresas no local de trabalho, pode querer preparar utilizadores na sua organização ao explicar como utilizar o Windows Hello. Por exemplo, quando alguém configura um novo dispositivo, é-lhes pedido para escolher entre **este dispositivo pertence à minha organização** ou **este é o meu próprio dispositivo pessoal**. Para dispositivos da empresa, eles devem selecionar o primeiro. Os utilizadores também terá de ser informado de que devem selecionar que opção de ligação: **Associação ao Azure AD** ou **configurar uma conta local (associação a um domínio mais tarde)** .

É comum para os utilizadores que estão habituados a utilizar um gesto de biométrico para autenticar dia após dia eventualmente se esquecer o PIN. Para evitar chamadas ao suporte técnico, recomenda-se que fornece aos utilizadores a capacidade de repor esquecido [palavras-passe](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) e [PINs](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset).

Existem muitas opções a partir da qual pode escolher quando implementar o Windows Hello para empresas. Com múltiplas opções garante a quase todas as organizações podem implementar o Windows Hello para empresas. Considere os seguintes tipos de implementações que são suportadas:

* [Azure AD híbrido associou a implementação de chave de fidedignidade](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Azure AD híbrido associou a implementação de certificados de confiança](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Guias de implementação de início de sessão única de associação do Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [Na implementação de confiança de chave no local](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [Implementação de confiança de certificados local no](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Fornece muitas opções torna a implementação são apresentados complexos. No entanto, a maioria das organizações provavelmente irá determinar que já tenha implementado maior parte da infraestrutura de que o Windows Hello para implementação de negócios depende. Seja como for, é importante compreender que o Windows Hello para empresas é um sistema distribuído e planejamento apropriado é recomendado.

Recomendamos que leia [planear um Windows Hello para empresas implementação](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) para o ajudar a decidir qual o melhor de modelo de implementação adequada para a organização específica. Em seguida, com base no planeamento fizer, consulte a [Windows Hello para o guia de implantação do Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) para ajudar a garantir uma implantação bem-sucedida do Windows Hello para empresas no seu ambiente existente.

### <a name="how-windows-hello-for-business-works"></a>Como Windows Hello para empresas funciona

#### <a name="user-sets-up-windows-hello-for-business"></a>Utilizador configura o Windows Hello para empresas

Depois de uma verificação de dois passos iniciais do utilizador durante a inscrição, Hello do Windows está configurada no dispositivo do utilizador e Windows pede ao utilizador para definir um gesto, que pode ser um biométrica, como um reconhecimento de impressões digitais ou facial, ou um PIN. Uma vez definido, o utilizador fornece o gesto para verificar sua identidade. Windows, em seguida, utiliza o Windows Hello para autenticar os utilizadores.

Com base nos recursos do seu dispositivo Windows 10, terá um enclave seguro interno, conhecido como um trusted platform module do hardware (TPM) ou um software de TPM. O TPM armazena a chave privada, o que requer o rosto, impressão digital ou PIN para desbloqueá-lo. Os dados biométricos não se movem e nunca são enviados para dispositivos externos ou servidores. Não existe nenhum ponto de única coleção que um invasor pode comprometer para roubar dados biométricos, porque o Windows Hello só armazena dados de identificação biométrica no dispositivo.

> [!TIP]
> À primeira vista, um PIN é como uma palavra-passe, mas é realmente mais segura. Uma diferença importante entre uma palavra-passe e um PIN é que o PIN é associado ao dispositivo específico em que foi configurada. Alguém que rouba a palavra-passe pode iniciar sessão na sua conta em qualquer lugar. Mas se eles roubam o PIN, que é preciso que roubar o dispositivo físico demasiado! Além disso, como um PIN é local no dispositivo, ele não é transmitido em qualquer lugar, de modo a não pode ser intercetado na transmissão ou roubado de um servidor.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Utilizar o Windows Hello para empresas para início de sessão do utilizador

Utilizar o Windows Hello para biometria de negócios e PINs assimétrica (pública/privada chave) criptografia para autenticação. Durante a autenticação, a encriptação está associada à chave de sessão de TLS, que protege o processo de autenticação para que um ataque de (MiTM) man-in-the-middle não pode roubar o token de segurança resultante ou o artefacto e reproduzi-lo a partir de outros locais.

Windows Hello para empresas fornece uma conveniente início de sessão experiência que autentica o utilizador para o Azure AD e recursos do Active Directory. Dispositivos associados ao AD Azure autenticar para o Azure durante o início de sessão e, opcionalmente, podem autenticar para o Active Directory. Híbrido do Azure Active Directory associados a um dispositivos autentique no Active Directory durante o início de sessão e autenticar-se ao Azure Active Directory em segundo plano.

![Ver a imagem de origem](./media/azure-ad/azure-ad-pwdless-image2.jpeg)

Os passos seguintes mostram a autenticação de início de sessão para o Azure Active Directory.

![Ecrã de bloqueio do Windows 10](./media/azure-ad/azure-ad-pwdless-image3.png)

1. Utilizador iniciar sessão no Windows usando biométrica ou gesto PIN. O gesto desbloqueia o Windows Hello para a chave privada de negócios e é enviado para o fornecedor de suporte de segurança da autenticação na nuvem, conhecido como o fornecedor de Cloud AP.

2. O fornecedor de Cloud AP solicita um nonce do Azure Active Directory.

3. O Azure AD devolve um valor de uso único que é válido durante 5 minutos.

4. O fornecedor de Cloud AP assina o valor de uso único utilizando a chave particular do usuário e retorna o valor de uso único assinado para o Azure Active Directory.

5. O Azure Active Directory valida o nonce assinado com a chave pública com segurança registados em relação a assinatura de valor de uso único do utilizador. Após validar a assinatura, do Azure AD, em seguida, valida o nonce assinado retornado. Depois de confirmar o valor de uso único, do Azure AD cria um PRT com chave de sessão que está encriptada para a chave de transporte do dispositivo e a devolve para o fornecedor de Cloud AP.

6. O fornecedor de Cloud AP recebe PRT encriptado com a chave de sessão. Utilizar a chave de transporte privada do dispositivo, o fornecedor de Cloud AP descriptografa a chave de sessão e protege a chave de sessão com TPM o dispositivo.

7. O fornecedor de Cloud AP devolve uma resposta de autenticação com êxito ao Windows após o qual o usuário pode acessar o Windows, bem como na cloud e aplicações sem a necessidade de autenticar novamente no local (SSO).

Para obter uma visão mais aprofundada do processo de autenticação em outros cenários que envolvem Windows Hello para empresas, consulte [Windows Hello para empresas e autenticação](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#Azure-AD-join-authentication-to-Active-Directory-using-a-Key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>Utilizador gerencia seus Windows Hello para credenciais de empresa

O [serviços de reposição do PIN da Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset) é uma funcionalidade no Azure AD que permite aos utilizadores para repor o PIN, se necessário. Usando a diretiva de grupo, Microsoft Intune ou um MDM compatível, um administrador pode configurar em segurança a utilizar o serviço de reposição do PIN da Microsoft que permite aos utilizadores para repor o PIN esquecido através das definições ou superior do ecrã de bloqueio sem a necessidade dos dispositivos Windows 10 efetuar novamente a inscrição.

Por vezes, os utilizadores têm de voltar a usar as palavras-passe. [Reposição de palavra-passe self-service](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) (SSPR) é outro recurso do Azure AD que permite que os utilizadores reponham as palavras-passe sem a necessidade de contactar o departamento de TI. Os utilizadores tem de se registar para ou ser registados para o self-service reposição palavra-passe antes de utilizar o serviço. Durante o registo, o utilizador escolhe um ou mais métodos de autenticação ativados pela sua organização. SSPR permite aos utilizadores rapidamente obter desbloqueado e continuar a trabalhar, independentemente de onde eles estão ou a hora do dia. Ao permitir que os utilizadores desbloquear a próprios, a sua organização pode reduzir o tempo não produtivos e os custos de suporte de elevada para problemas mais comuns relacionados com a palavra-passe.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Passe início de sessão com o Microsoft Authenticator

Passe início de sessão com o Microsoft Authenticator é outra solução de início que pode ser utilizada para iniciar sessão nas contas do Azure AD com o início de sessão no telefone. Ainda tem de verificar sua identidade ao fornecer algo que conhece e algo que têm, mas início de sessão permite-lhe ignorar a introduzir a palavra-passe e executa sua verificação de identidade no seu dispositivo móvel com a impressão digital, face ou o PIN de telefone.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Cenários de início de Microsoft Authenticator

A aplicação Microsoft Authenticator permite aos utilizadores verificar a respetiva identidade e autenticar-se à sua conta pessoal ou profissional. Também pode ser utilizado para aumentar uma palavra-passe com um código de acesso único ou notificação push ou substituir a necessidade de uma palavra-passe completamente. Em vez de utilizar uma palavra-passe, os utilizadores confirmam a respetiva identidade com o seu telemóvel através de análise de impressões digitais, reconhecimento facial ou íris ou PIN. Criado sobre tecnologia segura semelhante para o Windows Hello utiliza, essa ferramenta é empacotada num aplicativo simple num dispositivo móvel, tornando-o uma opção conveniente para os utilizadores. A aplicação Microsoft Authenticator está disponível para Android e iOS.

### <a name="microsoft-authenticator-deployment-considerations"></a>Considerações de implantação do Microsoft Authenticator

Pré-requisitos para utilizar a aplicação Microsoft Authenticator para fazer o início de sessão para o Azure AD incluem o seguinte:

* Os utilizadores finais estão ativados para multi-factor Authentication do Azure

* A capacidade dos utilizadores a inscreverem os dispositivos com o Microsoft Intune ou uma solução de gestão (MDM) de dispositivos móveis de terceiros

Partindo do princípio de que estes requisitos são cumpridos, os administradores ativar início de sessão no telefone no inquilino utilizando [Windows PowerShell.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) Assim que o início de sessão no telefone está ativado no inquilino, os utilizadores finais podem optar por início de sessão com o seu telefone ao selecionar a conta profissional ou escolar no **contas** ecrã da aplicação, em seguida, selecionar **ativar início de sessão no telefone** .

Partindo do princípio de que o início de sessão passe está ativado por um administrador, os utilizadores finais terá de cumprir os seguintes requisitos:

* Inscrito na autenticação Multifator do Azure

* Versão mais recente do Microsoft Authenticator instalada em dispositivos com iOS 8.0 ou superior, ou o Android 6.0 ou superior

* Conta escolar ou profissional com notificações push adicionado à aplicação

Para evitar o potencial da introdução bloqueado fora de sua conta ou ter de recriar a contas num novo dispositivo, é recomendado que utilize o Microsoft Authenticator para [as credenciais da conta de cópia de segurança](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-backup-recovery) para a cloud. Após a cópia de segurança, também pode utilizar a aplicação para recuperar as informações num novo dispositivo, evitando potencialmente obter bloqueado out ou ter de recriar as contas.

Uma vez que a maioria dos usuários estão acostumados a utilizar apenas as palavras-passe para autenticar, é importante que sua organização educa os usuários sobre esse processo. Reconhecimento pode reduzir a probabilidade de que os utilizadores chamam o suporte técnico para qualquer [problemas](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#known-issues) relacionados ao iniciar sessão utilizando a aplicação Microsoft Authenticator.

> [!NOTE]
> Um potencial ponto de falha para esta solução é roaming um utilizador estiver num local onde não existe nenhuma conectividade com a Internet. Chaves de segurança de FIDO2 e Windows Hello para empresas estão isentas a limitação do mesmo.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Passe como início de sessão com o Microsoft Authenticator funciona

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>Utilizador configura o passe início de sessão com o Microsoft Authenticator

Antes da aplicação Microsoft Authenticator pode ser utilizada como uma solução de início para iniciar sessão a uma conta do Azure AD, os passos devem ser executados por um administrador e os usuários finais.

Em primeiro lugar, um administrador deverá [ativar a utilização da aplicação como uma credencial](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) no inquilino com o Windows PowerShell. O administrador também terá de ativar os utilizadores finais para o Azure multi-factor Authentication (MFA do Azure) e configurar a aplicação Microsoft Authenticator como um da [métodos de verificação](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods).

Os utilizadores finais terá [transfira e instale](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) a aplicação Microsoft Authenticator e [configurado a respetiva conta](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app) para utilizar a aplicação Microsoft Authenticator como um dos métodos de verificação.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Usando o Microsoft Authenticator para o início de sessão passe de utilizador

A aplicação Microsoft Authenticator pode ser utilizada para iniciar sessão em qualquer conta do Azure AD sem utilizar uma palavra-passe. Embora o ecrã de bloqueio do Windows 10 não inclui a aplicação Microsoft Authenticator como uma opção de início de sessão, os utilizadores podem ainda introduzir o respetivo nome de utilizador e, em seguida, receber uma notificação push no seu dispositivo móvel para verificar a presença. Os utilizadores para confirmar sua presença que correspondam um número no ecrã de início de sessão, em seguida, fornecendo uma verificação de rostos, a identificação digital ou o PIN para desbloquear a chave privada e concluir a autenticação. Este método de validação multifator é mais conveniente para introduzir uma palavra-passe e um código e mais seguro do que uma palavra-passe.

![Autenticador início de sessão](./media/azure-ad/azure-ad-pwdless-image4.png)

Passe autenticação com o Microsoft Authenticator segue o mesmo padrão básico, como o Windows Hello para empresas, mas é um pouco mais complicado, já que o utilizador tem de ser identificados para que o Azure AD possa encontrar o sendo da versão de aplicação do Microsoft Authenticator utilizado.

![Processo de autenticação](./media/azure-ad/azure-ad-pwdless-image5.png)

1. O utilizador introduz o nome de utilizador.

2. Azure AD Deteta que o usuário tem uma credencial forte e inicia o fluxo de credenciais de forte.

3. Notificação é enviada para a aplicação através do Apple Push Notification Service (APNS) em dispositivos iOS, ou através do Firebase Cloud Messaging (FCM) em dispositivos Android.

4. O utilizador recebe a notificação push e abre a aplicação.

5. A aplicação chama do Azure AD e recebe um desafio de prova de presença e o valor de uso único.

6. O utilizador conclui o desafio ao introduzir o respetivo biometria ou PIN para desbloquear a chave privada.

7. O valor de uso único é assinado com a chave privada e enviado para o Azure AD.

8. Azure AD realiza a validação da chave pública/privada e devolve um token.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>Utilizador gerencia seus passe início de sessão com credenciais de Microsoft Authenticator

Com o [combinados registo](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined), os utilizadores podem registar e obter os benefícios do Azure multi-factor Authentication e reposição de palavra-passe self-service. Os utilizadores registar e gerir estas definições ao navegar para seus [página do meu perfil](https://aka.ms/mysecurityinfo). Além de ativar a SSPR, combinados registo suporta vários métodos de autenticação e ações.

## <a name="fido2-security-keys"></a>Chaves de segurança de FIDO2

FIDO2 é a versão mais recente do padrão de FIDO Alliance e tem dois componentes - padrão de autenticação na Web (WebAuthN) da W3C e o protocolo de cliente para autenticação FIDO Alliance correspondente (CTAP2). Os padrões de FIDO2 permitem aos utilizadores tirar partido do hardware - mobile- e com base em biometria autenticadores para autenticar facilmente com várias aplicações e Web sites em ambientes móveis e de Desktops.

Parceiros Microsoft e do setor têm trabalhado em conjunto em dispositivos de segurança de FIDO2 para Windows Hello ativar a autenticação simples e segura em dispositivos partilhados. Chaves de segurança de FIDO2 permitem-lhe executar a sua credencial consigo e autenticar em segurança para um [do Azure AD](https://aka.ms/azuread418)-associados a um dispositivo Windows 10 que faz parte da sua organização.

WebAuthN define uma API que permite o desenvolvimento e implementação de fortes, passe autenticação por aplicações e serviços web. O protocolo CTAP permite que os dispositivos externos, como chaves de segurança FIDO compatíveis para trabalhar com WebAuthN e servir como autenticadores. Com a autenticação de Web, os utilizadores podem iniciar sessão nos serviços online com suas face, impressão digital, PIN ou chaves de segurança de FIDO2 portátil, tirar partido de credenciais seguras de chave pública em vez de palavras-passe. Atualmente WebAuthN é suportada no Microsoft Edge e suporte para o Chrome e Firefox está em desenvolvimento.

Dispositivos e tokens que aderem aos protocolos FIDO2 WebAuthN e CTAP ocasionam uma solução de várias plataformas de autenticação forte sem utilizar palavras-passe. Parceiros da Microsoft estão a trabalhar numa variedade de fatores de forma principais de segurança, como chaves de segurança USB e ativado para NFC de cartões inteligentes.

### <a name="fido2-security-keys-scenarios"></a>Cenários de chaves de segurança de FIDO2

Chaves de segurança de FIDO2 podem ser utilizadas para iniciar sessão com o Azure AD, escolhendo a chave de segurança como o provedor de credenciais no ecrã de bloqueio do Windows 10. Um nome de utilizador ou palavra-passe não é necessário que a torna uma solução ideal para trabalhadores de linha primeiro que compartilham PCs entre vários utilizadores. Também são uma opção de autenticação excelente quando as políticas empresariais ditam que as credenciais de um utilizador tem de estar fisicamente separadas do respetivo dispositivo. Os utilizadores também podem optar por iniciar sessão nos sites da web, utilizando a respetiva chave de segurança FIDO2 dentro do browser Microsoft Edge no Windows 10 versão 1809 ou superior.

### <a name="fido2-security-keys-deployment-considerations"></a>Considerações sobre a implementação de chaves de segurança de FIDO2

Os administradores podem ativar o suporte de FIDO2 no Azure AD e atribuir a capacidade para utilizadores ou grupos. As políticas também podem ser criados para como as chaves são aprovisionadas e configurar as restrições, como permitir ou bloquear um conjunto específico de chaves de segurança de hardware. Chaves tem de ser distribuídas fisicamente aos utilizadores finais.

**Os requisitos para ativar o início sessão Azure AD e web sites com chaves de segurança de FIDO2 incluem o seguinte:**

* Azure AD

* Multi-Factor Authentication do Azure

* Combinados pré-visualização do registo

* Pré-visualização de principais de segurança do FIDO2 requer uma chave de segurança FIDO2 compatível

* Autenticação na Web (WebAuthN) requer o Microsoft Edge no Windows 10 versão 1809 ou superior

* FIDO2 baseada em Windows início de sessão requer o Azure AD associado ao Windows 10 versão 1809 ou superior (é a melhor experiência no Windows 10 versão 1903 ou superior)

Fatores de formulário compatíveis com FIDO2 incluem dispositivos USB, NFC e Bluetooth. Recomendamos que escolha o fator de forma que atenda às suas necessidades específicas, desde algumas plataformas e navegadores ainda não estão em conformidade FIDO2.

Recomendamos também que cada organização criar um protocolo para os utilizadores e os administradores sigam devem de segurança chave ser perdida ou roubado. Os utilizadores devem reportar a chave perdida ou roubada, para que os administradores ou o utilizador pode eliminar as chaves de segurança do perfil do usuário e aprovisionar um novo.

### <a name="how-fido2-security-keys-works"></a>Como funciona a FIDO2 chaves de segurança

#### <a name="user-sets-up-fido2-security-key"></a>Utilizador configura a chave de segurança de FIDO2

Embora os administradores podem [aprovisionar manualmente as chaves](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) e distribuí-los aos utilizadores finais, aprovisionamento e permitindo que o provedor de credenciais FIDO2 no ecrã de bloqueio do Windows 10 serão dado por [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). Os administradores também terão de utilizar o [portal do Azure](https://portal.azure.com/) para permitir que os dispositivos de token de hardware como um método de autenticação de início.

A implementação de chaves de segurança de FIDO2 também requer que os utilizadores registem as chaves a utilizar [combinados registo](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined). Com o registo de combinado, os utilizadores registar uma vez e obtém os benefícios do Azure multi-factor Authentication e o único início de sessão em Repor palavra-passe (SSPR).

Além de selecionar o token de hardware como o método de autenticação multifator predefinido, recomenda-se que também selecionar uma opção de verificação adicional.

* Microsoft Authenticator – notificação

* Aplicação de autenticação ou do hardware do token – de código

* Chamada telefónica

* Mensagem de texto

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Usando a chave de segurança FIDO2 para início de sessão do utilizador

FIDO2 fornece uma camada de abstração entre o fator de formulário a ser utilizado como a autenticação e criptografia de chave pública/privada para ativar autenticadores de plataforma interna, como o Windows Hello e segurança de chaves resolver para uma chave privada e fornecer uma chave pública que pode ser utilizado como um identificador para aceder a recursos externos. Chaves de segurança de FIDO2 estão equipadas com seus próprios incorporado enclave seguro que armazene a chave privada e requer a biometria ou PIN para desbloqueá-lo. Credenciais não podem ser reutilizadas, repetido, ou partilhadas entre serviços e não estão sujeitas a phishing e ataques MiTM ou servidor violações.

![FIDO2 início de sessão](./media/azure-ad/azure-ad-pwdless-image6.png)

Chaves de segurança de FIDO2 fornecem autenticação segura, independentemente do fator de formulário. A chave de segurança possui a credencial e deve ser protegida com um segundo fator adicional, como uma impressão digital (integrados a chave de segurança) ou um PIN de ser introduzido no Windows-sessão. Parceiros da Microsoft estão a trabalhar numa variedade de fatores de forma principais de segurança. Alguns exemplos incluem as chaves de segurança USB e NFC ativado cartões inteligentes.

> [!NOTE]
> Uma chave de segurança tem de implementar determinadas funcionalidades e extensões do protocolo FIDO2 CTAP [compatível com o Microsoft](https://aka.ms/fido2securitykeys). Microsoft foi testado essas soluções para compatibilidade com Windows 10 e o Azure Active Directory.

![Processo de início de sessão FIDO2](./media/azure-ad/azure-ad-pwdless-image9.png)

1. O usuário se conecta o dispositivo FIDO2 o computador.

2. Windows Deteta a chave de segurança FIDO2.

3. Windows envia um pedido de autenticação.

4. O Azure AD envia de volta um valor de uso único.

5. O utilizador conclui o gesto para desbloquear a chave privada armazenada no enclave de proteger a chave de segurança de FIDO2.

6. A chave de segurança FIDO2 assina o valor de uso único com a chave privada.

7. O pedido de token PRT com nonce assinado é enviado para o Azure AD.

8. O Azure AD verifica o valor de uso único assinado usando a chave pública FIDO2.

9. O Azure AD devolve PRT para ativar o acesso a recursos no local.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>Utilizador gerencia suas credenciais de principais de segurança de FIDO2

Assim como a aplicação Microsoft Authenticator, gestão de credenciais para as chaves de segurança de FIDO2 baseia-se na experiência de registo combinado para os utilizadores finais.

## <a name="deciding-a-passwordless-method"></a>Decidir um método de início

Escolher entre estas três opções de início depende de segurança da sua empresa, plataforma e requisitos de aplicações.

Eis alguns fatores que deve considerar ao escolher a tecnologia da Microsoft sem palavra-passe:

||**Windows Hello para empresas**|**Passe início de sessão com a aplicação Microsoft Authenticator**|**Chaves de segurança de FIDO2**|
|:-|:-|:-|:-|
|**Pré-requisitos**| Windows 10, versão 1809 ou posterior<br>Azure Active Directory| Aplicação Microsoft Authenticator<br>Telefone (iOS e Android dispositivos que executam o Android 6.0 ou superior.)|Windows 10, versão 1809 ou posterior<br>Azure Active Directory|
|**modo**|Plataforma|Software|Hardware|
|**Sistemas e dispositivos**|PC com uma incorporada Trusted Platform Module (TPM)<br>Reconhecimento de PIN e de Biometria |Reconhecimento de PIN e biometria no telemóvel|Dispositivos de segurança de FIDO2 são Microsoft compatível|
|**Experiência de utilizador**|Inicie sessão com um PIN ou biométrico reconhecimento (facial, íris ou impressão digital) com dispositivos Windows.<br>Autenticação do Windows Hello é vinculada ao dispositivo; o utilizador tem do dispositivo e um componente de início de sessão como um PIN ou biométrico fator acedam aos recursos empresariais.|Inicie sessão com um telefone celular com reconhecimento de análise, facial íris ou leitura de impressões digitais ou AFIXAR.<br>Utilizadores iniciam sessão no trabalho ou a conta pessoal do seu PC ou o número de telemóvel.|Inicie sessão com o dispositivo de segurança de FIDO2 (biometria, PIN e NFC)<br>Utilizador pode aceder ao dispositivo com base nos controlos da organização e autenticar com base no PIN, dispositivos, como chaves de segurança USB e cartões inteligentes com NFC ativado, chaves ou wearables de Biometria.|
|**Cenários ativados**| Experiência de sem palavra-passe com o dispositivo do Windows.<br>Aplicável a PC de trabalho dedicado com capacidade para início de sessão único para dispositivos e aplicações.|Sem palavra-passe em qualquer lugar solução através do telefone celular.<br>Aplicável para aceder ao trabalho ou aplicações pessoais na web a partir de qualquer dispositivo.|Experiência de sem palavra-passe para os funcionários que usam o NFC, biometria e PIN.<br>Aplicável a PCs partilhados e em que um telefone celular não é uma opção viável (por exemplo, para equipe de suporte técnico, quiosque público ou equipe hospital)|

Utilize a tabela seguinte para escolher que método irá suportar os requisitos e os utilizadores.

|Persona|Cenário|Ambiente|Tecnologia de início|
|:-|:-|:-|:-|
|**administrador**|Proteger o acesso a um dispositivo para tarefas de gestão|Dispositivo atribuído do Windows 10|Windows Hello para empresas e/ou FIDO2 a chave de segurança|
|**administrador**|Tarefas de gestão em dispositivos não Windows| Dispositivo móvel ou não-windows|Passe início de sessão com a aplicação Microsoft Authenticator|
|**Operador de informações**|Trabalho de produtividade|Dispositivo atribuído do Windows 10|Windows Hello para empresas e/ou FIDO2 a chave de segurança|
|**Operador de informações**|Trabalho de produtividade| Dispositivo móvel ou não-windows|Passe início de sessão com a aplicação Microsoft Authenticator|
|**Função de trabalho de malwares enviados por**|Quiosques numa entrada de fábrica, de fábrica, de varejo ou de dados|Dispositivos partilhados do Windows 10|Chaves de segurança de FIDO2|

## <a name="getting-started"></a>Introdução

Autenticação de início é a onda do futuro e o caminho para um ambiente mais seguro. Recomenda-se que as organizações a começam a planear para que esta alteração e reduzindo as respetivas dependências em palavras-passe. Para começar a utilizar, considere os seguintes objetivos:

* Permitir que os utilizadores do MFA + aplicação Microsoft Authenticator + acesso condicional.

* Proteção de palavra-passe do Azure AD de implementação + políticas de atualização.

* Permitir que os utilizadores para SSPR com registo combinado.

* Implemente a aplicação de autenticador da Microsoft para a mobilidade.

* Implementar o Windows Hello para empresas (1903: mantenha-se atualizado).

* Implemente FIDO2 dispositivos para os utilizadores que não é possível utilizar os telemóveis.

* Sempre que possível, desative a autenticação baseada em palavra-passe.

Para atingir esses objetivos, recomendamos a seguinte abordagem:

1. Ative o Azure Active Directory para tirar partido das funcionalidades, como o MFA do Azure e o acesso condicional.

2. Ative a autenticação multifator proporcionar proteção adicional.

3. Ative a reposição de palavra-passe Self-Service no caso dos utilizadores têm de voltar a usar uma palavra-passe.

4. Implemente o Microsoft Authenticator início de sessão no telefone para a mobilidade foi adicionado.

5. Implemente o Windows Hello para empresas para todos os dispositivos Windows 10.

6. Prepare para FIDO2 chaves de segurança.

> [!NOTE]
> Veja o Azure Active Directory [licenciamento página](https://azure.microsoft.com/pricing/details/active-directory/) para obter detalhes sobre os requisitos de licenciamento para métodos de início.

## <a name="conclusion"></a>Conclusão

Nos últimos anos, a Microsoft continuou seu compromisso para ativar um mundo sem palavras-passe. Com o Windows 10, a Microsoft apresentou o Windows Hello para empresas, um forte, hardware protegidos credencial de dois fatores, que permite o início de sessão único para o Azure Active Directory e do Active Directory. Assim como a tecnologia do Windows Hello para empresas, a aplicação Microsoft Authenticator utiliza a autenticação baseada em chave para ativar uma credencial de utilizador que está associada a um dispositivo móvel e utiliza um biometria ou PIN. Agora, chaves de segurança de FIDO2 permitem-lhe executar a sua credencial consigo e inicie sessão para o Azure AD, escolhendo a chave de segurança como o provedor de credenciais no ecrã de bloqueio do Windows 10. Todos os três dessas soluções passe reduzem o risco de phishing, spray de palavra-passe e ataques de repetição e fornecer aos utilizadores uma forma altamente segura e conveniente para iniciar sessão e aceder aos dados a partir de qualquer lugar.

A adoção de tecnologias de autenticação moderna do multi-factor, como biométrica e criptografia de chave pública nos dispositivos bastante acessíveis é uma das etapas mais influenciam que forma significativa podem reduzir o risco de identidade de uma empresa. Vai passe é uma abordagem de longo prazo para a autenticação segura, e ainda está em evolução. Tendo em conta requisitos emergentes, as organizações podem preparar-se ao fazer um plano para começar a mudar para tecnologias de início.

## <a name="next-steps"></a>Passos Seguintes

* Uma visão geral do [What ' s passe?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
* [Como ativar o início no Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)
