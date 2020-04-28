---
title: FaQ de autenticação multi-factor Azure - Diretório Ativo Azure
description: Perguntas e respostas frequentemente feitas relacionadas com a autenticação de multifactor estoiro Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d28f93f316ac2a63be6b3a8eb0b80678bd7607f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81271408"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Perguntas mais frequentes sobre o Multi-Factor Authentication do Azure

Este FAQ responde a perguntas comuns sobre a autenticação de multi-factores Azure e utilizando o serviço de autenticação multi-factor. É dividido em perguntas sobre o serviço em geral, modelos de faturação, experiências de utilizador e resolução de problemas.

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft deixará de oferecer o MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores dos seus utilizadores devem utilizar a autenticação multi-factor Azure baseada na nuvem. Os clientes existentes que ativaram o MFA Server antes do dia 1 de julho poderão descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.
>
> As informações partilhadas abaixo relativas ao Servidor de Autenticação Multi-Factor Azure só são aplicáveis aos utilizadores que já tenham o servidor MFA em funcionamento.
>
> O licenciamento baseado no consumo já não está disponível para novos clientes a partir de 1 de setembro de 2018.
> A partir de 1 de setembro de 2018, os novos fornecedores de auth podem deixar de ser criados. Os fornecedores auth existentes podem continuar a ser utilizados e atualizados. A autenticação multifactor continuará a ser uma funcionalidade disponível nas licenças Azure AD Premium.

## <a name="general"></a>Geral

* [Como é que o Servidor de Autenticação Multi-Factor do Azure lida com os dados dos utilizadores?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Que códigos curtos de SMS são usados para enviar mensagens SMS aos meus utilizadores?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Como é que o Servidor de Autenticação Multi-Factor do Azure lida com os dados dos utilizadores?

Com o Servidor de Autenticação Multi-Factor, os dados do utilizador são armazenados apenas nos servidores no local. Nenhuns dados de utilizador persistentes são armazenados na nuvem. Quando o utilizador executa a verificação em duas etapas, o Servidor de Autenticação Multi-Factor envia dados para o serviço de nuvem de autenticação multi-factor Azure para autenticação. A comunicação entre o Servidor de Autenticação multi-Factor e o serviço de nuvem de autenticação multi-factor utiliza a Camada de Tomadas Seguras (SSL) ou a Segurança da Camada de Transporte (TLS) sobre a saída da porta 443.

Quando os pedidos de autenticação são enviados para o serviço de nuvem, os dados são recolhidos para autenticação e relatórios de utilização. Os seguintes campos de dados estão incluídos em registos de verificação em duas etapas:

* **ID exclusivo** (nome do utilizador ou id do servidor de autenticação multi-factor)
* **Primeiro e Último Nome** (opcional)
* **Endereço de e-mail** (opcional)
* **Número de telefone** (quando se utiliza uma chamada de voz ou autenticação SMS)
* **Token dispositivo** (quando utilizar a autenticação de aplicações móveis)
* **Modo de Autenticação**
* **Resultado da Autenticação**
* **Nome do servidor de autenticação de vários fatores**
* **IP do servidor de autenticação de vários fatores**
* **IP do cliente** (se disponível)

Os campos opcionais podem ser configurados no Servidor de Autenticação multi-factor.

O resultado de verificação (sucesso ou negação) e a razão se foi negado, é armazenado com os dados de autenticação. Estes dados estão disponíveis em relatórios de autenticação e utilização.

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Que códigos curtos de SMS são usados para enviar mensagens SMS aos meus utilizadores?

Nos Estados Unidos, utilizamos os seguintes códigos curtos de SMS:

* *97671*
* *69829*
* *51789*
* *99399*

No Canadá, utilizamos os seguintes códigos curtos SMS:

* *759731*
* *673801*

Não há garantia de entrega rápida de SMS consistente ou de autenticação multi-factor baseada em voz pelo mesmo número. No interesse dos nossos utilizadores, podemos adicionar ou remover códigos curtos a qualquer momento, à medida que fazemos ajustes de rotas para melhorar a entrega de SMS. Não apoiamos códigos curtos para países ou regiões além dos Estados Unidos e canadá.

## <a name="billing"></a>Faturação

A maioria das perguntas de faturação pode ser respondida referindo-se à página de preços de [autenticação multi-factor](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) ou à documentação para [versões de autenticação multi-factor Azure e planos de consumo.](concept-mfa-licensing.md)

* [A minha organização é acusada de enviar as chamadas telefónicas e mensagens de texto que são usadas para autenticação?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [O modelo de faturação por utilizador cobra-me por todos os utilizadores habilitados, ou apenas aqueles que realizaram a verificação em duas etapas?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Como funciona a faturação de autenticação multi-factor?](#how-does-multi-factor-authentication-billing-work)
* [Existe uma versão gratuita da Autenticação Multi-Factor Azure?](#is-there-a-free-version-of-azure-multi-factor-authentication)
* [A minha organização pode alternar entre os modelos de faturação de consumo por utilizador e por autenticação a qualquer momento?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [A minha organização pode alternar entre faturação baseada no consumo e subscrições (um modelo baseado em licença) a qualquer momento?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [A minha organização tem de usar e sincronizar identidades para usar a Autenticação Multi-Factor Azure?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>A minha organização é acusada de enviar as chamadas telefónicas e mensagens de texto que são usadas para autenticação?

Não, não é cobrado por chamadas individuais ou mensagens de texto enviadas aos utilizadores através da Autenticação Multi-Factor Azure. Se utilizar um fornecedor de MFA por autenticação, é cobrado por cada autenticação, mas não pelo método utilizado.

Os seus utilizadores podem ser cobrados pelas chamadas telefónicas ou mensagens de texto que recebem, de acordo com o seu serviço de telefone pessoal.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>O modelo de faturação por utilizador cobra-me por todos os utilizadores habilitados, ou apenas aqueles que realizaram a verificação em duas etapas?

A faturação baseia-se no número de utilizadores configurados para utilizar a Autenticação Multi-Factor, independentemente de terem realizado uma verificação em duas etapas nesse mês.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Como funciona a faturação de autenticação multi-factor?

Quando cria um fornecedor de MFA por utilizador ou por autenticação, a subscrição azure da sua organização é faturada mensalmente com base na utilização. Este modelo de faturação é semelhante ao modo como o Azure fatura para o uso de máquinas virtuais e Aplicações Web.

Ao adquirir uma subscrição para a Autenticação Multi-Factor Azure, a sua organização paga apenas a taxa anual de licença para cada utilizador. As licenças mfa e office 365, Azure AD Premium, ou Enterprise Mobility + Security bundles são cobrados desta forma.

Para mais informações, consulte Como obter a [autenticação de multi-factors Azure](concept-mfa-licensing.md).

### <a name="is-there-a-free-version-of-azure-multi-factor-authentication"></a>Existe uma versão gratuita da Autenticação Multi-Factor Azure?

Os incumprimentos de segurança podem ser ativados no nível Free Azure AD. Com falhas de segurança, todos os utilizadores estão habilitados para autenticação de vários fatores utilizando a aplicação Microsoft Authenticator. Não existe capacidade de usar mensagens de texto ou verificação de telefone com falhas de segurança, apenas a aplicação Microsoft Authenticator.

Para mais informações, consulte [o que são incumprimentos de segurança?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>A minha organização pode alternar entre os modelos de faturação de consumo por utilizador e por autenticação a qualquer momento?

Se a sua organização comprar MFA como um serviço autónomo com faturação baseada no consumo, você escolhe um modelo de faturação quando cria um fornecedor de MFA. Não é possível alterar o modelo de faturação depois de criado um fornecedor de MFA. 

Se o seu fornecedor de MFA *não* estiver ligado a um inquilino DaD Azure, ou ligar o novo fornecedor de MFA a um inquilino, configurações de utilizador e opções de configuração do Azure AD não são transferidos. Além disso, os Servidores MFA do Azure existentes têm de ser reativados com as credenciais de ativação geradas através do novo Fornecedor do MFA. Reativar os Servidores MFA para associá-los ao novo Fornecedor do MFA não afeta a autenticação por chamada telefónica e mensagem de texto, mas as notificações da aplicação móvel deixarão de funcionar para todos os utilizadores até que reativem a aplicação móvel.

Saiba mais sobre os fornecedores de MFA em [Obter iniciado com um Fornecedor De Auth Azure Multi-Factor.](concept-mfa-authprovider.md)

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>A minha organização pode alternar entre faturação baseada no consumo e subscrições (um modelo baseado em licença) a qualquer momento?

Em alguns casos, sim.

Se o seu diretório tiver um fornecedor *de* autenticação multi-factor Azure, pode adicionar licenças de MFA. Os utilizadores com licenças não são contabilizados na faturação por utilizador. Os utilizadores sem licença ainda podem ser ativados para o MFA através do fornecedor mfa. Se comprar e atribuir licenças a todos os utilizadores configurados para utilizar a Autenticação Multifactor, pode eliminar o fornecedor de autenticação multi-factor Azure. Pode sempre criar outro fornecedor de MFA por utilizador se tiver mais utilizadores do que licenças no futuro.

Se o seu diretório tiver *um* fornecedor de autenticação multi-factor Azure, é sempre cobrado por cada autenticação, desde que o fornecedor de MFA esteja ligado à sua subscrição. Pode atribuir licenças de MFA aos utilizadores, mas ainda assim será cobrado por cada pedido de verificação em duas etapas, quer venha de alguém com licença MFA atribuída ou não.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication"></a>A minha organização tem de usar e sincronizar identidades para usar a Autenticação Multi-Factor Azure?

Se a sua organização utilizar um modelo de faturação baseado no consumo, o Azure Ative Directory é opcional, mas não é necessário. Se o seu fornecedor de MFA não estiver ligado a um inquilino da AD Azure, só pode implantar o Azure Multi-Factor Authentication Server no local.

O Azure Ative Diretório é necessário para o modelo de licença porque as licenças são adicionadas ao inquilino da AD Azure quando compra e as atribui aos utilizadores do diretório.

## <a name="manage-and-support-user-accounts"></a>Gerir e suportar contas de utilizador

* [O que devo dizer aos meus utilizadores para fazerem se não receberem uma resposta no telemóvel?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [O que devo fazer se um dos meus utilizadores não conseguir entrar na conta?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [O que devo fazer se um dos meus utilizadores perder um telemóvel que está a usar senhas de aplicação?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [E se um utilizador não conseguir iniciar sessão em aplicações não-navegadoras?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Os meus utilizadores dizem que, por vezes, não recebem a mensagem de texto ou os tempos de verificação.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Posso alterar o tempo que os meus utilizadores têm para introduzir o código de verificação a partir de uma mensagem de texto antes do tempo de saída do sistema?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Posso usar fichas de hardware com o Servidor de Autenticação Multi-Factor Azure?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Posso usar o servidor de autenticação multi-factor Azure para proteger os serviços de terminal?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Configurei o ID do chamador no Servidor MFA, mas os meus utilizadores ainda recebem chamadas de Autenticação Multi-Factor de um chamador anónimo.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Porque é que os meus utilizadores estão a ser solicitados a registar as suas informações de segurança?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>O que devo dizer aos meus utilizadores para fazerem se não receberem uma resposta no telemóvel?

Os seus utilizadores tentam até cinco vezes em 5 minutos para obter uma chamada telefónica ou SMS para autenticação. A Microsoft utiliza vários fornecedores para entregar chamadas e mensagens SMS. Se esta abordagem não funcionar, abra um caso de apoio para resolver ainda mais os problemas.

As aplicações de segurança de terceiros também podem bloquear a mensagem de texto do código de verificação ou a chamada telefónica. Se utilizar uma aplicação de segurança de terceiros, tente desativar a proteção, então solicite que seja enviado outro código de verificação de MFA.

Se os passos acima não funcionarem, verifique se os utilizadores estão configurados para mais de um método de verificação. Tente iniciar sessão novamente, mas selecione um método de verificação diferente na página de inscrição.

Para mais informações, consulte o [guia de resolução de problemas do utilizador final](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>O que devo fazer se um dos meus utilizadores não conseguir entrar na conta?

Pode redefinir a conta do utilizador, fazendo-os passar pelo processo de registo novamente. Saiba mais sobre a gestão das definições de utilizador e dispositivo com a [autenticação de vários fatores Azure na nuvem](howto-mfa-userdevicesettings.md).

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>O que devo fazer se um dos meus utilizadores perder um telemóvel que está a usar senhas de aplicação?

Para evitar o acesso não autorizado, elimine todas as palavras-passe da aplicação do utilizador. Depois de o utilizador ter um dispositivo de substituição, podem recriar as palavras-passe. Saiba mais sobre a gestão das definições de utilizador e dispositivo com a [autenticação de vários fatores Azure na nuvem](howto-mfa-userdevicesettings.md).

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>E se um utilizador não conseguir iniciar sessão em aplicações não-navegadoras?

Se a sua organização ainda utilizar clientes legados, e [permitiu o uso de palavras-passe de apps,](howto-mfa-mfasettings.md#app-passwords)então os seus utilizadores não podem iniciar sessão nestes clientes legados com o seu nome de utilizador e senha. Em vez disso, precisam de [configurar senhas de aplicação.](../user-help/multi-factor-authentication-end-user-app-passwords.md) Os utilizadores devem limpar (apagar) as suas informações de início de sessão, reiniciar a aplicação e, em seguida, iniciar o seu sessão com o seu nome de utilizador e senha de *aplicação* em vez da sua senha regular.

Se a sua organização não tiver clientes legados, não deve permitir que os seus utilizadores criem senhas de aplicação.

> [!NOTE]
> **Autenticação moderna para clientes do Office 2013**
>
> As palavras-passe das aplicações só são necessárias para apps que não suportem a autenticação moderna. Os clientes do Office 2013 apoiam protocolos de autenticação modernos, mas precisam de ser configurados. A autenticação moderna está disponível para qualquer cliente que execute a atualização de março de 2015 ou posterior para o Office 2013. Para mais informações, consulte o post de blog [Updated Office 365 modern authentication](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Os meus utilizadores dizem que, por vezes, não recebem a mensagem de texto ou os tempos de verificação.

A entrega de mensagens SMS não é garantida porque existem fatores incontroláveis que podem afetar a fiabilidade do serviço. Estes fatores incluem o país ou região de destino, a transportadora de telemóveis e a força do sinal.

As aplicações de segurança de terceiros também podem bloquear a mensagem de texto do código de verificação ou a chamada telefónica. Se utilizar uma aplicação de segurança de terceiros, tente desativar a proteção, então solicite que seja enviado outro código de verificação de MFA.

Se os seus utilizadores tiverem frequentemente problemas em receber mensagens de texto de forma fiável, diga-lhes para utilizarem a aplicação Do Autenticador microsoft ou o método de chamada telefónica. O Autenticador Microsoft pode receber notificações tanto sobre ligações celulares como Wi-Fi. Além disso, a aplicação móvel pode gerar códigos de verificação mesmo quando o dispositivo não tem qualquer sinal. A aplicação Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) e [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Posso alterar o tempo que os meus utilizadores têm para introduzir o código de verificação a partir de uma mensagem de texto antes do tempo de saída do sistema?

Em alguns casos, sim.

Para SMS de ida e venda com O Servidor Azure MFA v7.0 ou superior, pode configurar a definição de tempo limite definindo uma chave de registo. Depois do serviço de nuvem MFA enviar a mensagem de texto, o código de verificação (ou senha única) é devolvido ao Servidor MFA. O Servidor MFA armazena o código na memória durante 300 segundos por padrão. Se o utilizador não introduzir o código antes de os 300 segundos terem passado, a sua autenticação é negada. Utilize estes passos para alterar a definição de tempo de saída por defeito:

1. Aceda a `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`.
2. Crie uma chave de registo **DWORD** chamada *pfsvc_pendingSmsTimeoutSeconds* e detetete a hora em segundos que pretende que o Servidor Azure MFA guarde códigos de acesso únicos.

>[!TIP]
>
> Se tiver vários Servidores MFA, apenas o que processou o pedido de autenticação original conhece o código de verificação que foi enviado ao utilizador. Quando o utilizador introduzir o código, o pedido de autenticação para o validar deve ser enviado para o mesmo servidor. Se a validação do código for enviada para um servidor diferente, a autenticação é negada.

Se os utilizadores não responderem ao SMS dentro do prazo definido, a sua autenticação é negada.

Para SMS de ida e redação com MFA Azure na nuvem (incluindo o adaptador AD FS ou a extensão do Servidor de Política de Rede), não é possível configurar a definição de tempo limite. A Azure AD armazena o código de verificação durante 180 segundos.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Posso usar fichas de hardware com o Servidor de Autenticação Multi-Factor Azure?

Se estiver a utilizar o Servidor de Autenticação Multi-Factor Azure, pode importar fichas de autenticação aberta (OATH) baseadas em tempo de terceiros, com base em uma única vez (TOTP) e, em seguida, usá-las para verificação em duas etapas.

Pode utilizar fichas ActiveIdentity que são tokens DE TOTP OATH se colocar a chave secreta num ficheiro CSV e importar para o Servidor de Autenticação Multi-Factor Azure. Pode utilizar tokens DE JURAMENTO com Serviços da Federação de Diretórios Ativos (ADFS), autenticação baseada em formulários do Internet Information Server (IIS) e Serviço de Utilizador de Acesso telefónico à Autenticação Remota (RADIUS), desde que o sistema de clientes possa aceitar a entrada do utilizador.

Pode importar tokens TOTP DE JURAMENTO de terceiros com os seguintes formatos:  

- Recipiente de chave simétrica portátil (PSKC)
- CSV se o ficheiro contiver um número de série, uma chave secreta no formato Base 32, e um intervalo de tempo

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Posso usar o servidor de autenticação multi-factor Azure para proteger os serviços de terminal?

Sim, mas se estiver a utilizar o Windows Server 2012 R2 ou mais tarde, só pode proteger os Serviços de Terminal utilizando o Gateway de Ambiente de Trabalho Remoto (RD Gateway).

As alterações de segurança no Windows Server 2012 R2 alteraram a forma como o Azure Multi-Factor Authentication Server se conecta ao pacote de segurança da Autoridade de Segurança Local (LSA) no Windows Server 2012 e versões anteriores. Para versões de Serviços De Terminais no Windows Server 2012 ou mais cedo, pode [garantir uma aplicação com autenticação do Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se estiver a utilizar o Windows Server 2012 R2, precisa de Gateway RD.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Configurei o ID do chamador no Servidor MFA, mas os meus utilizadores ainda recebem chamadas de Autenticação Multi-Factor de um chamador anónimo.

Quando as chamadas de autenticação multi-factor são colocadas através da rede telefónica pública, por vezes são encaminhadas através de uma transportadora que não suporta o ID do chamador. Devido a este comportamento do transportador, o ID do chamador não está garantido, mesmo que o sistema de autenticação multi-factor o envie sempre.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Porque é que os meus utilizadores estão a ser solicitados a registar as suas informações de segurança?

Existem várias razões para que os utilizadores possam ser solicitados a registar as suas informações de segurança:

- O utilizador foi ativado para mFA pelo seu administrador em Azure AD, mas ainda não tem informações de segurança registadas para a sua conta.
- O utilizador foi ativado para reposição de senha de autosserviço em Azure AD. As informações de segurança vão ajudá-los a redefinir a sua senha no futuro se alguma vez esquecerem.
- O utilizador acedeu a uma aplicação que tem uma política de Acesso Condicional para exigir MFA e não se registou previamente para o MFA.
- O utilizador está a registar um dispositivo com a Azure AD (incluindo o Azure AD Join), e a sua organização requer MFA para o registo do dispositivo, mas o utilizador não se registou previamente para o MFA.
- O utilizador está a gerar o Windows Hello for Business no Windows 10 (que requer MFA) e ainda não está registado para o MFA.
- A organização criou e permitiu uma política de registo de MFA que foi aplicada ao utilizador.
- O utilizador previamente registado para o MFA, mas escolheu um método de verificação que um administrador já desativou. Por isso, o utilizador deve passar novamente pelo registo de MFA para selecionar um novo método de verificação predefinido.

## <a name="errors"></a>Erros

* [O que devem os utilizadores fazer se virem uma mensagem de erro "Autenticação não é para uma conta ativada" ao utilizarem notificações de aplicações móveis?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [O que os utilizadores devem fazer se virem uma mensagem de erro 0x800434D4L ao iniciar em sessão numa aplicação não navegadora?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>O que devem os utilizadores fazer se virem uma mensagem de erro "Autenticação não é para uma conta ativada" ao utilizarem notificações de aplicações móveis?

Peça ao utilizador que complete o seguinte procedimento para remover a sua conta do Autenticador Microsoft e, em seguida, adicione-a novamente:

1. Vá ao [perfil do portal Azure](https://account.activedirectory.windowsazure.com/profile/) e inscreva-se com uma conta organizacional.
2. Selecione **Verificação adicional de segurança**.
3. Remova a conta existente da aplicação Microsoft Authenticator.
4. Clique em **Configurar**e, em seguida, siga as instruções para reconfigurar o Autenticador microsoft.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>O que os utilizadores devem fazer se virem uma mensagem de erro 0x800434D4L ao iniciar em sessão numa aplicação não navegadora?

O erro *0x800434D4L* ocorre quando se tenta iniciar sessão numa aplicação não-navegadora, instalada num computador local, que não funciona com contas que requerem verificação em duas etapas.

Uma suposições para este erro é ter contas de utilizador separadas para operações relacionadas com administração e não administradores. Mais tarde, pode ligar caixas de correio entre a sua conta de administração e a conta não administradora para que possa iniciar sessão no Outlook utilizando a sua conta não administradora. Para mais detalhes sobre esta solução, saiba como dar a um administrador a capacidade de [abrir e ver o conteúdo da caixa de correio de um utilizador](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Passos seguintes

Se a sua pergunta não for respondida aqui, as seguintes opções de suporte estão disponíveis:

* Procure na Base de Conhecimento de Suporte da [Microsoft](https://support.microsoft.com) soluções para questões técnicas comuns.
* Procure e navegue em questões técnicas e respostas da comunidade, ou faça a sua própria pergunta no [Diretório Ativo Azure Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html).
* Contacte o profissional da Microsoft através do suporte do Servidor de [Autenticação Multi-Factor Azure](https://support.microsoft.com/oas/default.aspx?prid=14947). Ao contactar-nos, é útil se puder incluir o máximo de informação possível sobre o seu problema. A informação que pode fornecer inclui a página onde viu o erro, o código de erro específico, o ID específico da sessão e o ID do utilizador que viu o erro.
* Se é um cliente legacy PhoneFactor e tem dúvidas ou precisa [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) de ajuda para redefinir uma palavra-passe, use o endereço de e-mail para abrir um caso de suporte.
