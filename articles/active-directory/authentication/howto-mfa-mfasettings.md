---
title: Configurar autenticação multi-factor Azure - Diretório Ativo Azure
description: Este artigo descreve como configurar as definições de autenticação multi-factor Azure no portal Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd804937fbd98121f3242c6906b890183d1284bb
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2020
ms.locfileid: "84464373"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configurar Definições do Multi-Factor Authentication do Azure

Este artigo ajuda-o a gerir as definições de autenticação multi-factor no portal Azure. Abrange vários tópicos que o ajudam a tirar o máximo partido da Autenticação Multi-Factor do Azure. Nem todas as funcionalidades estão disponíveis em todas as versões da Autenticação Multi-Factor Azure.

Pode aceder a definições relacionadas com a Autenticação Multi-Factor Azure a partir do portal Azure, navegando para **Azure Ative Directory**  >  **Security**  >  **MFA**.

![Portal Azure - Azure AD Configurações de autenticação multi-factor](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Definições

Algumas destas definições aplicam-se ao MFA Server, Azure MFA ou ambos.

| Funcionalidade | Descrição |
| ------- | ----------- |
| Bloqueio de conta | Bloqueie temporariamente as contas no serviço de autenticação de vários fatores se houver demasiadas tentativas de autenticação negadas seguidas. Esta funcionalidade aplica-se apenas aos utilizadores que introduzam um PIN para autenticar. (MFA Server) |
| [Utilizadores de bloco/desbloqueio](#block-and-unblock-users) | Utilizado para impedir que utilizadores específicos possam receber pedidos de autenticação multi-factor. Todas as tentativas de autenticação de utilizadores bloqueados são automaticamente negadas. Os utilizadores permanecem bloqueados durante 90 dias a partir do momento em que são bloqueados. |
| [Alerta de fraudes](#fraud-alert) | Configurar definições relacionadas com a capacidade dos utilizadores de reportarem pedidos fraudulentos de verificação |
| [Notificações](#notifications) | Ativar notificações de eventos a partir do MFA Server. |
| [Fichas do OATH](concept-authentication-methods.md#oath-tokens) | Usado em ambientes Azure MFA baseados em nuvem para gerir tokens OATH para utilizadores. |
| [Definições de chamadas telefónicas](#phone-call-settings) | Configurar configurações relacionadas com chamadas telefónicas e saudações para ambientes em nuvem e no local. |
| Fornecedores | Isto irá mostrar quaisquer fornecedores de autenticação existentes que possa ter associado à sua conta. Novos fornecedores de autenticação não podem ser criados a partir de 1 de setembro de 2018 |

## <a name="manage-mfa-server"></a>Gerir o Servidor MFA

As definições nesta secção são apenas para O Servidor MFA.

| Funcionalidade | Descrição |
| ------- | ----------- |
| Definições do servidor | Descarregue o MFA Server e gere credenciais de ativação para inicializar o seu ambiente |
| [Bypass único](#one-time-bypass) | Permitir que um utilizador autentica sem efetuar a verificação em duas etapas por um tempo limitado. |
| [Regras de caching](#caching-rules) |  O caching é usado principalmente quando sistemas no local, como VPN, enviam múltiplos pedidos de verificação enquanto o primeiro pedido ainda está em curso. Esta funcionalidade permite que os pedidos subsequentes tenham sucesso automaticamente, depois de o utilizador ter sucesso na primeira verificação em curso. |
| Estado do servidor | Consulte o estado dos seus servidores MFA no local, incluindo versão, estado, IP e última hora e data de comunicação. |

## <a name="activity-report"></a>Relatório de atividades

O relatório disponível aqui é específico do MFA Server (no local). Para relatórios Azure MFA (nuvem) consulte o relatório de inscrições em Azure AD.

## <a name="block-and-unblock-users"></a>Bloquear e desbloquear utilizadores

Utilize o _bloco e desbloqueie_ a funcionalidade dos utilizadores para evitar que os utilizadores recebam pedidos de autenticação. Todas as tentativas de autenticação de utilizadores bloqueados são automaticamente negadas. Os utilizadores permanecem bloqueados durante 90 dias a partir do momento em que são bloqueados.

### <a name="block-a-user"></a>Bloquear um utilizador

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue para utilizadores do **Azure Ative Directory**  >  **Security**  >  **MFA**  >  **Block/desbloquear**.
3. **Selecione Adicionar** para bloquear um utilizador.
4. Selecione o **Grupo de Replicação**. Introduza o nome de utilizador para o utilizador bloqueado como **nome de utilizador \@ domain.com**. Insira um comentário no campo **Razão.**
5. **Selecione Adicionar** para terminar bloqueando o utilizador.

### <a name="unblock-a-user"></a>Desbloquear um utilizador

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue para utilizadores do **Azure Ative Directory**  >  **Security**  >  **MFA**  >  **Block/desbloquear**.
3. Selecione **Desbloqueie** na coluna **Ação** ao lado do utilizador para desbloquear.
4. Insira um comentário no **campo de desbloqueio.**
5. Selecione **Desbloqueie** para terminar desbloqueando o utilizador.

## <a name="fraud-alert"></a>Alerta de fraudes

Configure a funcionalidade _de alerta de fraude_ para que os seus utilizadores possam relatar tentativas fraudulentas de acesso aos seus recursos. Os utilizadores podem denunciar tentativas de fraude utilizando a aplicação móvel ou através do seu telemóvel.

### <a name="turn-on-fraud-alerts"></a>Ligue os alertas de fraude

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Consulte o alerta de fraude MFA de segurança **do Diretório Ativo Azure**  >  **Security**  >  **MFA**  >  **Fraud alert**.
3. Defina o **Permitir que os utilizadores apresentem alertas de fraude** definindo para **On**.
4. Selecione **Guardar**.

### <a name="configuration-options"></a>Opções de configuração

* **Bloquear o utilizador quando a fraude é reportada**: Se um utilizador reportar fraude, a sua conta fica bloqueada durante 90 dias ou até que um administrador desbloqueie a sua conta. Um administrador pode rever as inscrições utilizando o relatório de inscrição e tomar as medidas adequadas para evitar futuras fraudes. Um administrador pode então [desbloquear](#unblock-a-user) a conta do utilizador.
* **Código para denunciar fraude durante a saudação inicial**: Quando os utilizadores recebem uma chamada telefónica para efetuar uma verificação em duas etapas, normalmente pressionam **#** para confirmar a sua entrada. Para denunciar a fraude, o utilizador introduz um código antes de premir **#** . Este código é **0** por padrão, mas pode personalizá-lo.

   >[!NOTE]
   >As saudações de voz predefinidos da Microsoft instruem os utilizadores a **pressionar 0#** para apresentarem um alerta de fraude. Se pretender utilizar um código diferente de **0**, grave e carrere as suas próprias saudações de voz personalizadas com instruções apropriadas para os seus utilizadores.
   >

### <a name="view-fraud-reports"></a>Ver relatórios de fraude

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione Detalhes de autenticação de inscrição de **diretório ativo**  >  **Sign-ins**  >  **Azure**. O relatório de fraude faz agora parte do relatório padrão de inscrições da Azure AD e irá mostrar no **"Detalhe de Resultados"** como mFA negado, Código de Fraude introduzido.
 
## <a name="notifications"></a>Notificações

Configure aqui endereços de e-mail para **Azure Active Directory**utilizadores que receberão e-mails de alerta de fraude em  >  **Security**  >  Notificações**de Autenticação Multi-Factor de**Segurança Azure  >  **Notifications**Ative.

![Amostra de e-mail de alerta de fraude de notificação](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Definições de chamadas telefónicas

### <a name="caller-id"></a>ID de chamada

**Número de identificação do chamador MFA** - Este é o número que os seus utilizadores verão no seu telefone. Apenas números baseados nos EUA são permitidos.

>[!NOTE]
>Quando as chamadas de autenticação multi-factor são feitas através da rede telefónica pública, por vezes são encaminhadas através de uma transportadora que não suporta o ID do chamador. Por isso, o ID do chamador não é garantido, mesmo que o sistema de autenticação multi-factor o envie sempre.

Nos Estados Unidos, se ainda não configurar o ID do MFA Caller, as chamadas de voz da Microsoft vêm dos seguintes números: +1 (866) 539 4191, +1 (855) 330 8653 e +1 (877) 668 6536. Se utilizar filtros de spam, certifique-se de excluir estes números.

### <a name="custom-voice-messages"></a>Mensagens de voz personalizadas

Pode utilizar as suas próprias gravações ou saudações para uma verificação em duas etapas com a funcionalidade _de mensagens de voz personalizadas._ Estas mensagens podem ser utilizadas para além ou para substituir as gravações da Microsoft.

Antes de começar, esteja atento às seguintes restrições:

* Os formatos de ficheiro suportado são .wav e .mp3.
* O limite de tamanho do ficheiro é de 1 MB.
* As mensagens de autenticação devem ser inferiores a 20 segundos. Mensagens com mais de 20 segundos podem fazer com que a verificação falhe. O utilizador pode não responder antes de terminar a mensagem e os tempos de verificação.

### <a name="custom-message-language-behavior"></a>Comportamento personalizado da linguagem da mensagem

Quando uma mensagem de voz personalizada é reproduzida para o utilizador, o idioma da mensagem depende destes fatores:

* A linguagem do utilizador atual.
  * O idioma detetado pelo navegador do utilizador.
  * Outros cenários de autenticação podem comportar-se de forma diferente.
* O idioma de quaisquer mensagens personalizadas disponíveis.
  * Esta língua é escolhida pelo administrador, quando uma mensagem personalizada é adicionada.

Por exemplo, se houver apenas uma mensagem personalizada, com uma língua de alemão:

* Um utilizador que autentica em língua alemã ouvirá a mensagem alemã personalizada.
* Um utilizador que autentica em inglês ouvirá a mensagem inglesa padrão.

### <a name="set-up-a-custom-message"></a>Configurar uma mensagem personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
1. Navegue para as definições de chamada telefónica MFA de Segurança de Segurança **Ativa Azure**  >  **Security**  >  **MFA**  >  **Phone call settings**.
1. Selecione **Adicionar saudação**.
1. Escolha o tipo de saudação.
1. Escolha a língua.
1. Selecione um ficheiro de som .mp3 ou .wav para carregar.
1. Selecione **Adicionar**.

### <a name="custom-voice-message-defaults"></a>Padrão de mensagem de voz personalizado

Guiões de amostra para criar mensagens personalizadas.

| Nome da mensagem | Script |
| --- | --- |
| Sucesso da autenticação | O seu sinal foi verificado com sucesso. Até logo. |
| Pedido de extensão | Obrigado por utilizar o sistema de verificação de insusição da Microsoft. Por favor, pressione a chave da libra para continuar. |
| Confirmação de fraude | Foi apresentado um alerta de fraude. Para desbloquear a sua conta, contacte o balcão de apoio de TI da sua empresa. |
| Saudação por fraude (Standard) | Obrigado por utilizar o sistema de verificação de insusição da Microsoft. Por favor, pressione a chave da libra para terminar a sua verificação. Caso não tenha iniciado esta verificação, alguém poderá estar a tentar aceder à sua conta. Por favor, pressione zero libra para apresentar um alerta de fraude. Isto irá notificar a equipa de TI da sua empresa e bloquear novas tentativas de verificação. |
| Fraude reportada Um alerta de fraude foi enviado. | Para desbloquear a sua conta, contacte o balcão de apoio de TI da sua empresa. |
| Ativação | Obrigado por utilizar o sistema de verificação de insusição da Microsoft. Por favor, pressione a chave da libra para terminar a sua verificação. |
| Autenticação negada redando | Verificação negada. |
| Retry (Standard) | Obrigado por utilizar o sistema de verificação de insusição da Microsoft. Por favor, pressione a chave da libra para terminar a sua verificação. |
| Saudação (Padrão) | Obrigado por utilizar o sistema de verificação de insusição da Microsoft. Por favor, pressione a chave da libra para terminar a sua verificação. |
| Saudação (PIN) | Obrigado por utilizar o sistema de verificação de insusição da Microsoft. Introduza o seu PIN seguido da chave da libra para terminar a sua verificação. |
| Saudação de fraude (PIN) | Obrigado por utilizar o sistema de verificação de insusição da Microsoft.  Introduza o seu PIN seguido da chave da libra para terminar a sua verificação. Caso não tenha iniciado esta verificação, alguém poderá estar a tentar aceder à sua conta. Por favor, pressione zero libra para apresentar um alerta de fraude. Isto irá notificar a equipa de TI da sua empresa e bloquear novas tentativas de verificação. |
| Retrip(PIN) | Obrigado por utilizar o sistema de verificação de insusição da Microsoft. Introduza o seu PIN seguido da chave da libra para terminar a sua verificação. |
| Pedido de extensão após dígitos | Se já nesta extensão, pressione a chave da libra para continuar. |
| Autenticação negada | Desculpe, não podemos inscrevê-lo neste momento. Tente novamente mais tarde. |
| Saudação de ativação (Standard) | Obrigado por utilizar o sistema de verificação de insusição da Microsoft. Por favor, pressione a chave da libra para terminar a sua verificação. |
| Redação de ativação (Standard) | Obrigado por utilizar o sistema de verificação de insusição da Microsoft. Por favor, pressione a chave da libra para terminar a sua verificação. |
| Saudação de ativação (PIN) | Obrigado por utilizar o sistema de verificação de insusição da Microsoft. Introduza o seu PIN seguido da chave da libra para terminar a sua verificação. |
| Pedido de extensão antes dos dígitos | Obrigado por utilizar o sistema de verificação de insusição da Microsoft. Por favor, transfira esta chamada para a extensão... |

## <a name="one-time-bypass"></a>Bypass único

A funcionalidade _de bypass único_ permite ao utilizador autenticar uma única vez sem efetuar uma verificação em duas etapas. O bypass é temporário e expira após um número especificado de segundos. Em situações em que a aplicação móvel ou o telemóvel não esteja a receber uma notificação ou chamada telefónica, pode permitir um bypass único para que o utilizador possa aceder ao recurso pretendido.

### <a name="create-a-one-time-bypass"></a>Criar um bypass único

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue pelo **Azure Ative Directory**  >  **Security**  >  **MFA**  >  **um bypass único**.
3. Selecione **Adicionar**.
4. Se necessário, selecione o grupo de replicação para o bypass.
5. Introduza o nome de utilizador como **nome \@ de utilizador domain.com**. Introduza o número de segundos que o bypass deve durar. Insira a razão do bypass.
6. Selecione **Adicionar**. O prazo entra em vigor imediatamente. O utilizador tem de fazer o seu sedudo antes de expirar o bypass único.

### <a name="view-the-one-time-bypass-report"></a>Ver o relatório de bypass único

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue pelo **Azure Ative Directory**  >  **Security**  >  **MFA**  >  **um bypass único**.

## <a name="caching-rules"></a>Regras de caching

Pode definir um período de tempo para permitir tentativas de autenticação após a autenticação de um utilizador utilizando _a função de caching._ As tentativas de autenticação subsequentes para o utilizador dentro do período de tempo especificado sucedem-se automaticamente. O caching é usado principalmente quando sistemas no local, como VPN, enviam múltiplos pedidos de verificação enquanto o primeiro pedido ainda está em curso. Esta funcionalidade permite que os pedidos subsequentes tenham sucesso automaticamente, depois de o utilizador ter sucesso na primeira verificação em curso.

>[!NOTE]
>A função de caching não se destina a ser utilizada para iniciar sing-ins no Azure Ative Directory (Azure AD).

### <a name="set-up-caching"></a>Configurar o caching

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Consulte as regras **de**  >  **Security**  >  **MFA**  >  **Caching**MFA de Segurança do Diretório Ativo Azure .
3. Selecione **Adicionar**.
4. Selecione o **tipo de cache** da lista de drop-down. Introduza o número máximo de **segundos**de cache .
5. Se necessário, selecione um tipo de autenticação e especifique uma aplicação.
6. Selecione **Adicionar**.

## <a name="mfa-service-settings"></a>Definições de serviço MFA

As definições para palavras-passe de aplicações, IPs fidedignos, opções de verificação e lembre-se que a autenticação multi-factor para Azure Multi-Factor Authentication pode ser encontrada nas definições de serviço. As definições de serviço podem ser acedidas a partir do portal Azure através da navegação para **Azure Ative Directory**  >  **Security**  >  **MFA**  >  **Obter**  >  **Configure**  >  **configurações adicionais de MFA baseadas na nuvem**.

![Definições de serviço de autenticação multi-factor Azure](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

> [!NOTE]
> Os IPs fidedignos só podem incluir gamas IP privadas quando utilizar o MFA Server. Para autenticação multi-factor Azure baseada na nuvem, só pode utilizar intervalos de endereços IP públicos.

## <a name="app-passwords"></a>Palavras-passe da aplicação

Algumas aplicações, como o Office 2010 ou mais cedo e o Apple Mail antes do iOS 11, não suportam a verificação em duas etapas. As aplicações não estão configuradas para aceitar uma segunda verificação. Para utilizar estas aplicações, aproveite a funcionalidade de senhas de _aplicação._ Pode utilizar uma palavra-passe de aplicação no lugar da sua senha tradicional para permitir que uma aplicação ignore a verificação em duas etapas e continue a trabalhar.

A autenticação moderna é suportada para os clientes do Microsoft Office 2013 e mais tarde. Os clientes do Office 2013, incluindo o Outlook, suportam protocolos de autenticação modernos e podem ser habilitados a trabalhar com verificação em duas etapas. Após a ativação do cliente, as palavras-passe da aplicação não são necessárias para o cliente.

>[!NOTE]
>As palavras-passe da aplicação não funcionam com políticas de autenticação de vários fatores baseadas no Acesso Condicional e na autenticação moderna.

### <a name="considerations-about-app-passwords"></a>Considerações sobre senhas de aplicações

Ao utilizar palavras-passe de aplicações, considere os seguintes pontos importantes:

* As palavras-passe da aplicação só são inseridas uma vez por aplicação. Os utilizadores não têm de acompanhar as palavras-passe nem introduzi-las sempre.
* A palavra-passe real é gerada automaticamente e não é fornecida pelo utilizador. A palavra-passe gerada automaticamente é mais difícil para um intruso adivinhar e é mais segura.
* Existe um limite de 40 palavras-passe por utilizador.
* As aplicações que cache palavras-passe e as usam em cenários no local podem começar a falhar porque a palavra-passe da aplicação não é conhecida fora do trabalho ou da conta escolar. Um exemplo deste cenário são os e-mails da Exchange que estão no local, mas o correio arquivado está na nuvem. Neste cenário, a mesma palavra-passe não funciona.
* Depois de a Autenticação Multi-Factor ser ativada na conta de um utilizador, as palavras-passe das aplicações podem ser utilizadas com a maioria dos clientes não-navegador, como o Outlook e o Microsoft Skype para negócios. As ações administrativas não podem ser realizadas utilizando palavras-passe de aplicações através de aplicações não-navegador, como o Windows PowerShell. As ações não podem ser executadas mesmo quando o utilizador tem uma conta administrativa. Para executar scripts PowerShell, crie uma conta de serviço com uma senha forte e não ative a conta para verificação em duas etapas.

>[!WARNING]
>As palavras-passe das aplicações não funcionam em ambientes híbridos onde os clientes comunicam tanto no local como com pontos finais de descoberta automática em nuvem. As palavras-passe de domínio são necessárias para autenticar no local. As palavras-passe da aplicação são necessárias para autenticar com a nuvem.

### <a name="guidance-for-app-password-names"></a>Orientação para nomes de palavras-passe de aplicativos

Os nomes da palavra-passe da aplicação devem refletir o dispositivo em que são utilizados. Se tiver um portátil que tenha aplicações não-navegador como Outlook, Word e Excel, crie uma palavra-passe de aplicação chamada **Laptop** para estas aplicações. Crie outra palavra-passe de aplicação chamada **Desktop** para as mesmas aplicações que executam no seu computador de secretária.

>[!NOTE]
>Recomendamos que crie uma palavra-passe de aplicação por dispositivo, em vez de uma palavra-passe de aplicação por aplicação.

### <a name="federated-or-single-sign-on-app-passwords"></a>Senhas de aplicação federadas ou únicas

A Azure AD suporta a federação, ou um único sign-on (SSO), com serviços de domínio de diretório ativo do Windows Server (DS AD). Se a sua organização estiver federada com Azure AD e estiver a utilizar a Autenticação Multi-Factor Azure, considere os seguintes pontos sobre as palavras-passe da aplicação.

>[!NOTE]
>Os seguintes pontos aplicam-se apenas aos clientes federados (SSO).

* As palavras-passe da aplicação são verificadas pela Azure AD e, portanto, pela federação de bypass. A Federação só é utilizada ativamente na configuração de senhas de aplicação.
* O Fornecedor de Identidade (IdP) não é contactado para utilizadores federados (SSO), ao contrário do fluxo passivo. As palavras-passe da aplicação são armazenadas no trabalho ou na conta escolar. Se um utilizador deixar a empresa, a informação do utilizador flui para o trabalho ou conta escolar utilizando o **DirSync** em tempo real. O desativação/eliminação da conta pode demorar até três horas para sincronizar, o que pode atrasar o desativamento/eliminação da palavra-passe da aplicação em Azure AD.
* As definições de Controlo de Acesso do cliente no local não são honradas pela funcionalidade de palavras-passe da aplicação.
* Não existe capacidade de autenticação/auditoria no local para utilização com a funcionalidade de passwords da aplicação.
* Algumas arquiteturas avançadas requerem uma combinação de credenciais para verificação em duas etapas com os clientes. Estas credenciais podem incluir um nome de utilizador de conta de trabalho ou de uma conta escolar e senhas, e senhas de aplicação. Os requisitos dependem da forma como a autenticação é realizada. Para clientes que autenticam contra uma infraestrutura no local, um nome de utilizador de conta de trabalho ou de uma conta escolar é necessário. Para clientes que autenticam contra a Azure AD, é necessária uma senha de aplicação.

  Por exemplo, suponha que tenha a seguinte arquitetura:

  * O seu caso no local do Ative Directory é federado com Azure AD.
  * Estás a usar o Exchange online.
  * Estás a usar o Skype para negócios no local.
  * Está a usar a autenticação multi-factor Azure.

  Neste cenário, utiliza as seguintes credenciais:

  * Para iniciar sôm no Skype for Business, use o nome de utilizador e a palavra-passe da sua conta de trabalho ou da sua conta escolar.
  * Para aceder ao livro de endereços de um cliente do Outlook que se conecta ao Exchange online, utilize uma senha de aplicação.

### <a name="allow-users-to-create-app-passwords"></a>Permitir que os utilizadores criem senhas de aplicação

Por padrão, os utilizadores não podem criar senhas de aplicação. A funcionalidade de palavras-passe da aplicação deve ser ativada. Para dar aos utilizadores a capacidade de criar palavras-passe de aplicações, utilize o seguinte procedimento:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**.
3. Selecione **a autenticação multi-factor**.
4. Em Autenticação Multi-Factor, selecione **as definições de serviço**.
5. Na página Definições de **Serviço,** selecione o **Permitir que os utilizadores criem senhas de aplicação para iniciar sessão na opção de aplicações não-navegador.**

### <a name="create-app-passwords"></a>Criar senhas de aplicativo

Os utilizadores podem criar senhas de aplicação durante o seu registo inicial. O utilizador tem a opção de criar senhas de aplicação no final do processo de registo.

Os utilizadores também podem criar senhas de aplicação após o registo. Para obter mais informações e passos detalhados para os seus utilizadores, veja [o que são palavras-passe da aplicação na Autenticação Multi-Factor Azure?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>IPs Fidedignos

A funcionalidade _IPs Fidedigna_ da Autenticação Multi-Factor Azure é utilizada por administradores de um inquilino gerido ou federado. A funcionalidade contorna a verificação em duas etapas para os utilizadores que se inscrevam na intranet da empresa. A funcionalidade encontra-se disponível com a versão completa da Azure Multi-Factor Authentication, e não com a versão gratuita para administradores. Para obter mais informações sobre como obter a versão completa da Autenticação Multi-Factor Azure, consulte [a autenticação multi-factor Azure](multi-factor-authentication.md).

> [!TIP]
> As gamas IPv6 só são suportadas na interface [de localização nomeada (pré-visualização).](../conditional-access/location-condition.md#preview-features)

Se a sua organização implementar a extensão NPS para fornecer MFA a aplicações no local, note que o endereço IP de origem sempre parecerá ser o servidor NPS que a tentativa de autenticação flui através.

| Tipo de inquilino Azure AD | Opções de funcionalidade de IPs fidedignas |
|:--- |:--- |
| Geridos |**Gama específica de endereços IP**: Os administradores especificam uma gama de endereços IP que podem contornar a verificação em duas etapas para os utilizadores que se inscrevam na intranet da empresa. Um máximo de 50 gamas IP fidedignas podem ser configuradas.|
| Federados |**Todos os Utilizadores Federados**: Todos os utilizadores federados que inserem sedudas a partir de dentro da organização podem contornar a verificação em duas etapas. Os utilizadores contornam a verificação utilizando uma alegação emitida pelos Serviços da Federação de Diretórios Ativos (AD FS).<br/>**Gama específica de endereços IP**: Os administradores especificam uma gama de endereços IP que podem contornar a verificação em duas etapas para os utilizadores que se inscrevam na intranet da empresa. |

O bypass IPs fidedigno funciona apenas a partir de dentro da intranet da empresa. Se selecionar a opção **Todos os Utilizadores Federados** e um utilizador entrar na intranet da empresa, o utilizador tem de autenticar utilizando a verificação em duas etapas. O processo é o mesmo mesmo se o utilizador apresentar uma reclamação de FS AD. 

### <a name="end-user-experience-inside-of-corpnet"></a>Experiência de utilizador final dentro da corpnet

Quando a funcionalidade IPs Fidedigno é desativada, é necessária uma verificação em duas etapas para os fluxos do navegador. As palavras-passe da aplicação são necessárias para aplicações de clientes ricos em idosos.

Quando a funcionalidade IPs Fidedigno está ativada, *não* é necessária uma verificação em duas etapas para os fluxos do navegador. As palavras-passe da aplicação *não* são necessárias para aplicações de clientes ricos em idades mais antigas, desde que o utilizador não tenha criado uma palavra-passe de aplicação. Depois de uma palavra-passe da aplicação estar a ser utilizada, a palavra-passe continua a ser necessária. 

### <a name="end-user-experience-outside-corpnet"></a>Experiência de utilizador final fora da corpnet

Independentemente de a funcionalidade IPs Fidedigna estar ativada, é necessária uma verificação em duas etapas para os fluxos do navegador. As palavras-passe da aplicação são necessárias para aplicações de clientes ricos em idosos.

### <a name="enable-named-locations-by-using-conditional-access"></a>Ativar localizações nomeadas utilizando o Acesso Condicional

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Ative**  >  **Directy Security**  >  **Conditional Access**  >  **Indeissou .**
3. Selecione **Nova localização**.
4. Insira um nome para o local.
5. Selecione **Mark como localização fidedigna.**
6. Introduza o intervalo IP na notação CIDR como **40.77.182.32/27**.
7. Selecione **Criar**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Ativar a funcionalidade IPs fidedigno utilizando o Acesso Condicional

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Ative**  >  **Directy Security**  >   **Conditional Access**  >  **Indeissou .**
3. Selecione **Configurar IPs fidedignos MFA**.
4. Na página **Definições de Serviço,** em **IPs Fidedignos,** escolha entre qualquer uma das duas opções seguintes:

   * **Para pedidos de utilizadores federados originários da minha intranet**: Para escolher esta opção, selecione a caixa de verificação. Todos os utilizadores federados que entram na rede corporativa contornam a verificação em duas etapas utilizando uma alegação emitida pela AD FS. Certifique-se de que a AD FS tem uma regra para adicionar a alegação intranet ao tráfego apropriado. Se a regra não existir, crie a seguinte regra em AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Para pedidos de um leque específico de IPs públicos**: Para escolher esta opção, insira os endereços IP na caixa de texto utilizando a notação CIDR.
      * Para endereços IP que estão na gama xxx.xxx.xxx.1 através de xxx.xxx.xxx.xxx.254, utilize a notação como **xxx.xxx.xxx.0/24**.
      * Para um único endereço IP, utilize a notação como **xxx.xxx.xxx.xxx/32**.
      * Introduza até 50 intervalos de endereço IP. Os utilizadores que insinuem estes endereços IP contornam a verificação em duas etapas.

5. Selecione **Guardar**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Ativar a funcionalidade IPs Fidedigno utilizando as definições de serviço

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**.
3. Selecione **a autenticação multi-factor**.
4. Em Autenticação Multi-Factor, selecione **as definições de serviço**.
5. Na página **Definições de Serviço,** em **IPs Fidedignos,** escolha uma (ou ambas) das duas opções seguintes:

   * **Para pedidos de utilizadores federados na minha intranet**: Para escolher esta opção, selecione a caixa de verificação. Todos os utilizadores federados que entram na rede corporativa contornam a verificação em duas etapas utilizando uma alegação emitida pela AD FS. Certifique-se de que a AD FS tem uma regra para adicionar a alegação intranet ao tráfego apropriado. Se a regra não existir, crie a seguinte regra em AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Para pedidos a partir de um intervalo especificado de sub-redes de endereços IP**: Para escolher esta opção, insira os endereços IP na caixa de texto utilizando a notação CIDR.
      * Para endereços IP que estão na gama xxx.xxx.xxx.1 através de xxx.xxx.xxx.xxx.254, utilize a notação como **xxx.xxx.xxx.0/24**.
      * Para um único endereço IP, utilize a notação como **xxx.xxx.xxx.xxx/32**.
      * Introduza até 50 intervalos de endereço IP. Os utilizadores que insinuem estes endereços IP contornam a verificação em duas etapas.

6. Selecione **Guardar**.

## <a name="verification-methods"></a>Métodos de verificação

Pode escolher os métodos de verificação disponíveis para os seus utilizadores. A tabela a seguir fornece uma breve visão geral dos métodos.

Quando os seus utilizadores inscrevem as suas contas para a Autenticação Multi-Factor Azure, eles escolhem o seu método de verificação preferido a partir das opções que ativou. Orientação para o processo de inscrição do utilizador é fornecida na [Configuração da minha conta para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-first-time.md).

| Método | Descrição |
|:--- |:--- |
| Chamada para telefone |Coloca uma chamada de voz automatizada. O utilizador atende a chamada e prime # no teclado do telefone para se autenticar. O número de telefone não está sincronizado com o Ative Directory no local. |
| Mensagem de texto para telefone |Envia uma mensagem de texto que contém um código de verificação. Solicita-se ao utilizador que introduza o código de verificação na interface de entrada. Este processo chama-se SMS unidirecciona. SMS bidirecionais significa que o utilizador deve enviar um código específico. SMS bidireccionado é precotado e não apoiado após 14 de novembro de 2018. Os administradores devem permitir um outro método para os utilizadores que anteriormente utilizaram SMS bidirecionais.|
| Notificação através de aplicação móvel |Envia uma notificação push para o seu telefone ou dispositivo registado. O utilizador visualiza a notificação e seleciona **Verificar** para completar a verificação. A aplicação Microsoft Authenticator está disponível para [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)e [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |
| Código de verificação a partir de aplicativo móvel ou token de hardware |A aplicação Microsoft Authenticator gera um novo código de verificação do OATH a cada 30 segundos. O utilizador introduz o código de verificação na interface de entrada. A aplicação Microsoft Authenticator está disponível para [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)e [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |

### <a name="enable-and-disable-verification-methods"></a>Permitir e desativar métodos de verificação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**.
3. Selecione **a autenticação multi-factor**.
4. Em Autenticação Multi-Factor, selecione **as definições de serviço**.
5. Na página **Definições de Serviço,** nas **opções de verificação,** selecione/desescolte os métodos a fornecer aos seus utilizadores.
6. Clique em **Guardar**.

Detalhes adicionais sobre a utilização de métodos de autenticação podem ser encontrados no artigo [Quais são os métodos de autenticação](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Lembre-se da autenticação multi-factor

A funcionalidade _de autenticação multi-factor de relem aos_ dispositivos e navegadores que são fidedignos pelo utilizador é uma funcionalidade gratuita para todos os utilizadores da Autenticação Multi-Factor. Os utilizadores podem ignorar as verificações subsequentes por um número especificado de dias, após iniciarem sessão com sucesso num dispositivo através do Multi-Factor Authentication. A funcionalidade melhora a usabilidade minimizando o número de vezes que um utilizador tem de efetuar uma verificação em duas etapas no mesmo dispositivo.

>[!IMPORTANT]
>Se uma conta ou dispositivo estiver comprometido, lembrar a Autenticação Multi-Factor para dispositivos fidedignos pode afetar a segurança. Se uma conta corporativa ficar comprometida ou um dispositivo de confiança for perdido ou roubado, deverá [revogar as Sessões de MFA](howto-mfa-userdevicesettings.md).
>
>A ação de restauro revoga o estatuto de confiança de todos os dispositivos, e o utilizador é obrigado a efetuar novamente a verificação em duas etapas. Também pode instruir os seus utilizadores a restaurar a autenticação multi-factor nos seus próprios dispositivos com as instruções em [Gerir as suas definições para verificação em duas etapas.](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

### <a name="how-the-feature-works"></a>Como funciona a funcionalidade

A função de autenticação multi-factor de ressalsse que define um cookie persistente no navegador quando um utilizador seleciona a opção **X days** no início da súm. O utilizador não é solicitado novamente para autenticação multi-factor a partir desse mesmo navegador até que o cookie expire. Se o utilizador abrir um navegador diferente no mesmo dispositivo ou limpar os seus cookies, é solicitado novamente para verificar.

A opção **'Não' peça novamente por X days** não é mostrada em aplicações não-navegadores, independentemente de a app suportar a autenticação moderna. Estas aplicações usam _fichas de atualização_ que fornecem novos tokens de acesso a cada hora. Quando um token de atualização é validado, a Azure AD verifica se a última verificação em duas etapas ocorreu dentro do número especificado de dias.

A funcionalidade reduz o número de autenticações em aplicações web, que normalmente solicitam sempre. A funcionalidade aumenta o número de autenticações para clientes de autenticação moderna que normalmente solicitam a cada 90 dias. Pode também aumentar o número de autenticações quando combinadas com as políticas de Acesso Condicional.

>[!IMPORTANT]
>A funcionalidade **de autenticação multi-factor de resso para o remember** não é compatível com a funcionalidade **"Manter-me assinado em** funcionalidade de AD FS", quando os utilizadores efetuam a verificação em duas etapas para AD FS através do Azure Multi-Factor Authentication Server ou uma solução de autenticação multi-factor de terceiros.
>
>Se os seus utilizadores selecionarem **manter-me informado no** AD FS e também marcar o seu dispositivo como confiável para a Autenticação Multi-Factor, o utilizador não é verificado automaticamente após o fim do número de dias de autenticação de **vários fatores.** O Azure AD solicita uma nova verificação em duas etapas, mas a AD FS devolve um token com a reivindicação e data originais de autenticação multi-factor, em vez de realizar novamente a verificação em duas etapas. **Esta reação desencadeia um ciclo de verificação entre a Azure AD e a AD FS.**
>
>A funcionalidade **de autenticação multi-factor de resso para o remember** não é compatível com os utilizadores B2B e não será visível para os utilizadores B2B ao iniciar sessão nos inquilinos convidados.
>

### <a name="enable-remember-multi-factor-authentication"></a>Ativar a autenticação multi-factor

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**.
3. Selecione **a autenticação multi-factor**.
4. Em Autenticação Multi-Factor, selecione **as definições de serviço**.
5. Na página **'Definições de Serviço',** **gerencie a autenticação multi-factores**, selecione o **Permitir que os utilizadores se lembrem da autenticação de vários fatores nos dispositivos em que confiam na** opção.
6. Desafie o número de dias para permitir que dispositivos fidedignos contornem a verificação em duas etapas. O padrão é de 14 dias.
7. Selecione **Guardar**.

### <a name="mark-a-device-as-trusted"></a>Marque um dispositivo como confiável

Depois de ativar a funcionalidade de autenticação multi-factor de ressaltação, os utilizadores podem marcar um dispositivo como confiável quando iniciarem sação selecionando **Não volte a perguntar**.

## <a name="next-steps"></a>Próximos passos

[Modificar a marca de página de página AZure AD](../fundamentals/customize-branding.md)
