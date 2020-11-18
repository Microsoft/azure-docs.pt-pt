---
title: Azure AD Multi-Factor Autenticação FAQ - Diretório Ativo Azure
description: Perguntas e respostas frequentes relacionadas com a Autenticação Multi-Factor AD Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 909f4b71e07a20abf4af76fecaaf93bb08e4d6b4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837911"
---
# <a name="frequently-asked-questions-about-azure-ad-multi-factor-authentication"></a>Perguntas frequentes sobre autenticação multi-factor Azure AD

Esta FAQ responde a perguntas comuns sobre a autenticação multi-factor Azure AD e a utilização do serviço de autenticação multi-factor. Está dividido em questões sobre o serviço em geral, modelos de faturação, experiências de utilizador e resolução de problemas.

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft deixará de oferecer o MFA Server para novas implementações. Os novos clientes que gostariam de exigir a autenticação de vários fatores dos seus utilizadores devem utilizar a autenticação multi-factor Azure AD baseada na nuvem. Os clientes existentes que tenham ativado o MFA Server antes de 1 de julho poderão descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.
>
> As informações partilhadas abaixo relativamente ao Azure Multi-Factor Authentication Server são aplicáveis apenas para utilizadores que já tenham o servidor MFA em funcionamento.
>
> O licenciamento baseado no consumo já não está disponível para novos clientes a partir de 1 de setembro de 2018.
> A partir de 1 de setembro de 2018, os novos fornecedores de auth já não podem ser criados. Os fornecedores de auth existentes podem continuar a ser utilizados e atualizados. A autenticação multi-factor continuará a ser uma funcionalidade disponível nas licenças Azure AD Premium.

## <a name="general"></a>Geral

* [Como é que o Azure Multi-Factor Authentication Server lida com os dados do utilizador?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Que códigos curtos SMS são utilizados para o envio de mensagens SMS para os meus utilizadores?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Como é que o Azure Multi-Factor Authentication Server lida com os dados do utilizador?

Com o Servidor de Autenticação Multi-Factor, os dados do utilizador são armazenados apenas nos servidores do local. Nenhuns dados de utilizador persistentes são armazenados na nuvem. Quando o utilizador realiza a verificação em duas etapas, o Servidor de Autenticação Multi-Factor envia dados para o serviço de autenticação multi-factor AD Azure para a autenticação. A comunicação entre o Servidor de Autenticação Multi-Factor e o serviço de nuvem de autenticação multi-factor utiliza a Camada de Tomadas Seguras (SSL) ou a Segurança da Camada de Transporte (TLS) sobre a porta 443 de saída.

Quando os pedidos de autenticação são enviados para o serviço de nuvem, os dados são recolhidos para relatórios de autenticação e utilização. Os seguintes campos de dados estão incluídos em registos de verificação em duas etapas:

* **ID exclusivo** (nome de utilizador ou ID do servidor de autenticação multi-factor)
* **Primeiro e Último Nome** (opcional)
* **Endereço de e-mail** (opcional)
* **Número de telefone** (ao utilizar uma chamada de voz ou autenticação POR SMS)
* **Token do dispositivo** (ao utilizar a autenticação de aplicações móveis)
* **Modo de Autenticação**
* **Resultado da Autenticação**
* **Nome do servidor de autenticação multi-factor**
* **IP do servidor de autenticação multi-factor**
* **IP do cliente** (se disponível)

Os campos opcionais podem ser configurados no Servidor de Autenticação Multi-Factor.

O resultado da verificação (sucesso ou negação), e o motivo se foi negado, é armazenado com os dados de autenticação. Estes dados estão disponíveis em relatórios de autenticação e utilização.

Para obter mais informações, consulte [a residência de dados e os dados do cliente para autenticação multi-factor Azure AD.](concept-mfa-data-residency.md)

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Que códigos curtos SMS são utilizados para o envio de mensagens SMS para os meus utilizadores?

Nos Estados Unidos, utilizamos os seguintes códigos curtos SMS:

* *97671*
* *69829*
* *51789*
* *99399*

No Canadá, utilizamos os seguintes códigos curtos SMS:

* *759731*
* *673801*

Não há garantia de uma entrega rápida de SMS ou multi-factor baseada em voz pelo mesmo número. No interesse dos nossos utilizadores, podemos adicionar ou remover códigos curtos a qualquer momento à medida que fazemos ajustes de rota para melhorar a entrega de SMS.

Não apoiamos códigos curtos para países ou regiões além dos Estados Unidos e Canadá.

## <a name="billing"></a>Faturação

A maioria das questões de faturação pode ser respondida referindo-se à página de preços de [autenticação multi-factor](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) ou à documentação para [versões de autenticação multi-factor AD Azure.](concept-mfa-licensing.md)

* [A minha organização é acusada de enviar as chamadas telefónicas e mensagens de texto que são usadas para autenticação?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [O modelo de faturação por utilizador cobra-me para todos os utilizadores habilitados, ou apenas para aqueles que realizaram a verificação em duas etapas?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Como funciona a faturação de autenticação multi-factor?](#how-does-multi-factor-authentication-billing-work)
* [Existe uma versão gratuita da Autenticação Multi-Factor AZure AD?](#is-there-a-free-version-of-azure-ad-multi-factor-authentication)
* [A minha organização pode alternar entre os modelos de faturação por utilizador e por autenticação a qualquer momento?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [A minha organização pode alternar entre faturação baseada em consumo e subscrições (um modelo baseado em licença) a qualquer momento?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [A minha organização tem de usar e sincronizar identidades para usar a autenticação multi-factor AD Azure?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>A minha organização é acusada de enviar as chamadas telefónicas e mensagens de texto que são usadas para autenticação?

Não, não é cobrado por chamadas telefónicas individuais feitas ou mensagens de texto enviadas aos utilizadores através da Autenticação Multi-Factor AD Azure. Se utilizar um fornecedor de MFA por autenticação, é cobrado para cada autenticação, mas não para o método utilizado.

Os seus utilizadores podem ser cobrados pelas chamadas telefónicas ou mensagens de texto que recebem, de acordo com o seu serviço telefónico pessoal.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>O modelo de faturação por utilizador cobra-me para todos os utilizadores habilitados, ou apenas para aqueles que realizaram a verificação em duas etapas?

A faturação baseia-se no número de utilizadores configurados para utilizar a Autenticação Multi-Factor, independentemente de terem realizado uma verificação em duas etapas nesse mês.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Como funciona a faturação de autenticação multi-factor?

Quando cria um fornecedor de MFA por utilizador ou por autenticação, a subscrição Azure da sua organização é faturada mensalmente com base na utilização. Este modelo de faturação é semelhante ao modo como a Azure fatura para o uso de máquinas virtuais e Aplicações Web.

Ao adquirir uma subscrição para autenticação multi-factor Azure AD, a sua organização apenas paga a taxa anual de licença para cada utilizador. As licenças de MFA e os pacotes Microsoft 365, Azure AD Premium ou Enterprise Mobility + Security são cobrados desta forma.

Para mais informações, consulte [Como obter autenticação multi-factor Azure AD](concept-mfa-licensing.md).

### <a name="is-there-a-free-version-of-azure-ad-multi-factor-authentication"></a>Existe uma versão gratuita da Autenticação Multi-Factor AZure AD?

As falhas de segurança podem ser ativadas no nível Azure AD Free. Com os padrão de segurança, todos os utilizadores estão habilitados para a autenticação de vários fatores utilizando a aplicação Microsoft Authenticator. Não existe capacidade de utilizar mensagens de texto ou verificação de telefone com padrão de segurança, apenas a aplicação Microsoft Authenticator.

Para mais informações, veja [quais são os incumprimentos de segurança?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>A minha organização pode alternar entre os modelos de faturação por utilizador e por autenticação a qualquer momento?

Se a sua organização comprar o MFA como um serviço autónomo com faturação baseada no consumo, escolha um modelo de faturação quando criar um fornecedor de MFA. Não é possível alterar o modelo de faturação após a criação de um fornecedor de MFA. 

Se o seu fornecedor de MFA *não* estiver ligado a um inquilino Azure AD, ou se ligar o novo fornecedor de MFA a um inquilino AD AZure diferente, as definições do utilizador e as opções de configuração não são transferidas. Além disso, os Servidores MFA do Azure existentes têm de ser reativados com as credenciais de ativação geradas através do novo Fornecedor do MFA. Reativar os Servidores MFA para associá-los ao novo Fornecedor do MFA não afeta a autenticação por chamada telefónica e mensagem de texto, mas as notificações da aplicação móvel deixarão de funcionar para todos os utilizadores até que reativem a aplicação móvel.

Saiba mais sobre os fornecedores de MFA em [Começar com um Azure Multi-Factor Auth Provider](concept-mfa-authprovider.md).

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>A minha organização pode alternar entre faturação baseada em consumo e subscrições (um modelo baseado em licença) a qualquer momento?

Em alguns casos, sim.

Se o seu diretório tiver um fornecedor de autenticação multi-factor Azure *por utilizador,* pode adicionar licenças MFA. Os utilizadores com licenças não são contabilizados na faturação baseada no consumo por utilizador. Os utilizadores sem licença ainda podem ser habilitados para MFA através do provedor MFA. Se comprar e atribuir licenças para todos os seus utilizadores configurados para utilizar a Autenticação Multi-Factor, pode eliminar o fornecedor de autenticação multi-factor Azure. Pode sempre criar outro fornecedor de MFA por utilizador se tiver mais utilizadores do que licenças no futuro.

Se o seu diretório tiver um fornecedor de autenticação Azure Multi-Factor De *autenticação por autenticação,* é sempre cobrado para cada autenticação, desde que o fornecedor MFA esteja ligado à sua subscrição. Pode atribuir licenças de MFA aos utilizadores, mas ainda será cobrado por cada pedido de verificação em duas etapas, quer venha de alguém com uma licença MFA atribuída ou não.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication"></a>A minha organização tem de usar e sincronizar identidades para usar a autenticação multi-factor AD Azure?

Se a sua organização utilizar um modelo de faturação baseado no consumo, o Azure Ative Directory é opcional, mas não é necessário. Se o seu fornecedor de MFA não estiver ligado a um inquilino Azure AD, só pode implantar o Azure Multi-Factor Authentication Server no local.

O Azure Ative Directory é necessário para o modelo de licença porque as licenças são adicionadas ao inquilino Azure AD quando você compra e as atribui aos utilizadores no diretório.

## <a name="manage-and-support-user-accounts"></a>Gerir e suportar contas de utilizador

* [O que devo dizer aos meus utilizadores para fazerem se não receberem uma resposta no telemóvel?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [O que devo fazer se um dos meus utilizadores não conseguir entrar na sua conta?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [O que devo fazer se um dos meus utilizadores perder um telefone que está a usar senhas de aplicação?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [E se um utilizador não puder entrar em aplicações não-navegador?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Os meus utilizadores dizem que, por vezes, não recebem a mensagem de texto ou os tempos de verificação.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Posso alterar o tempo que os meus utilizadores têm para introduzir o código de verificação a partir de uma mensagem de texto antes do fim do tempo do sistema?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Posso usar fichas de hardware com servidor de autenticação multi-factor Azure?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Posso utilizar o Azure Multi-Factor Authentication Server para proteger os Serviços de Terminal?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Configurei o ID do chamador no MFA Server, mas os meus utilizadores ainda recebem chamadas de autenticação multi-factor de uma chamada anónima.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Porque é que os meus utilizadores estão a ser solicitados a registar as suas informações de segurança?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>O que devo dizer aos meus utilizadores para fazerem se não receberem uma resposta no telemóvel?

Os seus utilizadores tentem até cinco vezes em 5 minutos para receber uma chamada telefónica ou SMS para autenticação. A Microsoft utiliza vários fornecedores para entregar chamadas e mensagens SMS. Se esta abordagem não funcionar, abra um caso de apoio para resolver mais problemas.

As aplicações de segurança de terceiros também podem bloquear a mensagem de texto de código de verificação ou a chamada telefónica. Se utilizar uma aplicação de segurança de terceiros, tente desativar a proteção e, em seguida, solicite que seja enviado outro código de verificação MFA.

Se os passos acima não funcionarem, verifique se os utilizadores estão configurados para mais de um método de verificação. Tente iniciar sessão novamente, mas selecione um método de verificação diferente na página de inscrição.

Para obter mais informações, consulte o [guia de resolução de problemas do utilizador final](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>O que devo fazer se um dos meus utilizadores não conseguir entrar na sua conta?

Pode redefinir a conta do utilizador, fazendo-os passar novamente pelo processo de registo. Saiba mais sobre [a gestão das definições de utilizador e dispositivo com autenticação multi-factor Azure AD na nuvem.](howto-mfa-userdevicesettings.md)

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>O que devo fazer se um dos meus utilizadores perder um telefone que está a usar senhas de aplicação?

Para evitar o acesso não autorizado, elimine todas as palavras-passe da aplicação do utilizador. Depois de o utilizador ter um dispositivo de substituição, podem recriar as palavras-passe. Saiba mais sobre [a gestão das definições de utilizador e dispositivo com autenticação multi-factor Azure AD na nuvem.](howto-mfa-userdevicesettings.md)

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>E se um utilizador não puder entrar em aplicações não-navegador?

Se a sua organização ainda utilizar clientes antigos, e [permitir a utilização de senhas de aplicação,](howto-mfa-app-passwords.md)então os seus utilizadores não podem iniciar seducação a estes clientes legados com o seu nome de utilizador e senha. Em vez disso, precisam de [configurar senhas de aplicação.](../user-help/multi-factor-authentication-end-user-app-passwords.md) Os seus utilizadores devem limpar (apagar) as suas informações de inscrição, reiniciar a aplicação e, em seguida, iniciar sôs com o seu nome de utilizador e senha de *aplicação* em vez da sua senha de acesso regular.

Se a sua organização não tem clientes legados, não deve permitir que os seus utilizadores criem senhas de aplicação.

> [!NOTE]
> **Autenticação moderna para clientes do Office 2013**
>
> As palavras-passe das aplicações são apenas necessárias para apps que não suportam a autenticação moderna. Os clientes do Office 2013 suportam protocolos de autenticação modernos, mas precisam de ser configurados. A autenticação moderna está disponível para qualquer cliente que execute a atualização de março de 2015 ou posterior para o Office 2013. Para mais informações, consulte o post de blog [Updated Office 365 modern authentication.](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Os meus utilizadores dizem que, por vezes, não recebem a mensagem de texto ou os tempos de verificação.

A entrega de mensagens SMS não é garantida porque existem fatores incontroláveis que podem afetar a fiabilidade do serviço. Estes fatores incluem o país ou região de destino, a transportadora móvel e a força do sinal.

As aplicações de segurança de terceiros também podem bloquear a mensagem de texto de código de verificação ou a chamada telefónica. Se utilizar uma aplicação de segurança de terceiros, tente desativar a proteção e, em seguida, solicite que seja enviado outro código de verificação MFA.

Se os seus utilizadores tiverem frequentemente problemas em receber mensagens de texto de forma fiável, diga-lhes para utilizarem a aplicação microsoft Authenticator ou o método de chamada telefónica. O Autenticador microsoft pode receber notificações tanto sobre ligações celulares como Wi-Fi. Além disso, a aplicação móvel pode gerar códigos de verificação mesmo quando o dispositivo não tem qualquer sinal. A aplicação Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) e [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Posso alterar o tempo que os meus utilizadores têm para introduzir o código de verificação a partir de uma mensagem de texto antes do fim do tempo do sistema?

Em alguns casos, sim.

Para SMS unidirecional com Azure MFA Server v7.0 ou superior, pode configurar a definição de tempo limite definindo uma chave de registo. Após o serviço de nuvem MFA enviar a mensagem de texto, o código de verificação (ou senha única) é devolvido ao Servidor MFA. O MFA Server armazena o código na memória durante 300 segundos por defeito. Se o utilizador não introduzir o código antes dos 300 segundos passarem, a sua autenticação é negada. Utilize estes passos para alterar a definição de tempo limite padrão:

1. Aceda a `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`.
2. Crie uma chave de registo **DWORD** chamada *pfsvc_pendingSmsTimeoutSeconds* e desempenhe o tempo em segundos que pretende que o Servidor MFA Azure armazene códigos de acesso únicos.

>[!TIP]
>
> Se tiver vários Servidores MFA, apenas aquele que processou o pedido de autenticação original conhece o código de verificação que foi enviado ao utilizador. Quando o utilizador introduz o código, o pedido de autenticação para validá-lo deve ser enviado para o mesmo servidor. Se a validação do código for enviada para um servidor diferente, a autenticação é negada.

Se os utilizadores não responderem ao SMS dentro do período de tempo definido, a sua autenticação é negada.

Para SMS unidirecional com Azure AD MFA na nuvem (incluindo o adaptador AD FS ou a extensão do Servidor de Política de Rede), não é possível configurar a definição de tempo limite. A Azure AD armazena o código de verificação durante 180 segundos.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Posso usar fichas de hardware com servidor de autenticação multi-factor Azure?

Se estiver a utilizar o Azure Multi-Factor Authentication Server, pode importar fichas de senha de senha (TOTP) baseadas em terceiros e, em seguida, usá-las para verificação em duas etapas.

Pode utilizar fichas da ActiveIdentity que são tokens OATH TOTP se colocar a chave secreta num ficheiro CSV e importar para o Azure Multi-Factor Authentication Server. Pode utilizar fichas OATH com Serviços da Federação de Diretórios Ativos (ADFS), autenticação baseada em formulários do Internet Information Server (IIS) e Serviço de Acesso ao Utilizador de Autenticação Remota (RADIUS) desde que o sistema do cliente aceite a entrada do utilizador.

Pode importar fichas TOTP de terceiros com os seguintes formatos:  

- Recipiente chave simétrica portátil (PSKC)
- CSV se o ficheiro contiver um número de série, uma chave secreta no formato Base 32, e um intervalo de tempo

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Posso utilizar o Azure Multi-Factor Authentication Server para proteger os Serviços de Terminal?

Sim, mas se estiver a utilizar o Windows Server 2012 R2 ou mais tarde, só pode proteger os Serviços de Terminal utilizando o Gateway de Desktop Remoto (GATEWAY RD).

As alterações de segurança no Windows Server 2012 R2 alteraram a forma como o Azure Multi-Factor Authentication Server se conecta ao pacote de segurança da Autoridade de Segurança Local (LSA) no Windows Server 2012 e nas versões anteriores. Para versões de Serviços terminais no Windows Server 2012 ou mais cedo, pode [proteger uma aplicação com autenticação do Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se estiver a utilizar o Windows Server 2012 R2, precisa do RD Gateway.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Configurei o ID do chamador no MFA Server, mas os meus utilizadores ainda recebem chamadas de autenticação multi-factor de uma chamada anónima.

Quando as chamadas de autenticação multi-factor são feitas através da rede telefónica pública, por vezes são encaminhadas através de uma transportadora que não suporta o ID do chamador. Devido a este comportamento da transportadora, o ID do chamador não está garantido, mesmo que o sistema de autenticação multi-factor o envie sempre.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Porque é que os meus utilizadores estão a ser solicitados a registar as suas informações de segurança?

Existem várias razões para que os utilizadores possam ser solicitados a registar as suas informações de segurança:

- O utilizador foi ativado para MFA pelo seu administrador em Azure AD, mas ainda não tem informações de segurança registadas para a sua conta.
- O utilizador foi ativado para reiniciar a palavra-passe de autosserviço no Azure AD. As informações de segurança vão ajudá-los a redefinir a sua senha no futuro se alguma vez a esquecerem.
- O utilizador acedeu a uma aplicação que tem uma política de Acesso Condicional para exigir MFA e não se registou previamente para MFA.
- O utilizador está a registar um dispositivo com Azure AD (incluindo Azure AD Join), e a sua organização requer MFA para registo de dispositivos, mas o utilizador não se registou previamente para MFA.
- O utilizador está a gerar o Windows Hello for Business no Windows 10 (que requer MFA) e não se registou previamente para MFA.
- A organização criou e permitiu uma política de Registo de MFA que foi aplicada ao utilizador.
- O utilizador registou-se anteriormente para mFA, mas escolheu um método de verificação que um administrador já desativou. Por conseguinte, o utilizador deve recorrer ao registo MFA para selecionar um novo método de verificação por defeito.

## <a name="errors"></a>Erros

* [O que devem os utilizadores fazer se virem que um pedido de autenticação não é para uma conta ativada" mensagem de erro ao utilizar notificações de aplicações móveis?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [O que devem os utilizadores fazer se virem uma mensagem de erro 0x800434D4L ao iniciarem sessão numa aplicação não browser?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>O que devem os utilizadores fazer se virem que um pedido de autenticação não é para uma conta ativada" mensagem de erro ao utilizar notificações de aplicações móveis?

Peça ao utilizador para completar o seguinte procedimento para remover a sua conta do Autenticador Microsoft e, em seguida, adicione-a novamente:

1. Vá ao [perfil do portal Azure](https://account.activedirectory.windowsazure.com/profile/) e inscreva-se com uma conta organizacional.
2. Selecione **Verificação de Segurança Adicional.**
3. Remova a conta existente da aplicação Microsoft Authenticator.
4. Clique **em Configurar** e siga as instruções para reconfigurar o Autenticador Microsoft.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>O que devem os utilizadores fazer se virem uma mensagem de erro 0x800434D4L ao iniciarem sessão numa aplicação não browser?

O erro *0x800434D4L* ocorre quando se tenta iniciar sing a uma aplicação não browser, instalada num computador local, que não funciona com contas que requerem verificação em duas etapas.

Uma solução alternativa para este erro é ter contas de utilizador separadas para operações de administração e não administração. Mais tarde, pode ligar caixas de correio entre a sua conta de administração e a conta não administrada para que possa iniciar sação no Outlook utilizando a sua conta de não administração. Para obter mais detalhes sobre esta solução, aprenda a dar a [um administrador a capacidade de abrir e ver o conteúdo da caixa de correio de um utilizador.](https://help.outlook.com/141/gg709759.aspx?sl=1)

## <a name="next-steps"></a>Passos seguintes

Se a sua pergunta não for respondida aqui, as seguintes opções de suporte estão disponíveis:

* Procure na [Base de Conhecimento de Suporte](https://support.microsoft.com) do Microsoft soluções para problemas técnicos comuns.
* Procure e navegue em questões técnicas e respostas da comunidade, ou faça a sua própria pergunta no [Azure Ative Directory Q&A](/answers/topics/azure-active-directory.html).
* Contacte o profissional da Microsoft através do [suporte do Servidor de Autenticação Multi-Factor Azure](https://support.microsoft.com/oas/default.aspx?prid=14947). Ao contactar-nos, é útil se puder incluir o máximo de informação possível sobre o seu problema. A informação que pode fornecer inclui a página onde viu o erro, o código de erro específico, o ID de sessão específico e o ID do utilizador que viu o erro.
* Se é um cliente legado do PhoneFactor e tem dúvidas ou precisa de ajuda para redefinir uma palavra-passe, use o [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) endereço de e-mail para abrir um caso de suporte.