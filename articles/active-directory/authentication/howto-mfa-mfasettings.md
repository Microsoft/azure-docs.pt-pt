---
title: Configurar autenticação multi-factor Azure - Diretório Ativo Azure
description: Saiba como configurar definições para autenticação multi-factor Azure no portal Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 17fbba605f6f20fa384d59a8c89ee536a9b121bf
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964440"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configurar Definições do Multi-Factor Authentication do Azure

Para personalizar a experiência do utilizador final para autenticação multi-factor Azure, pode configurar opções para configurações como os limiares de bloqueio de conta ou alertas de fraude e notificações. Algumas configurações estão diretamente no portal Azure para Azure Ative Directory (Azure AD), e algumas num portal separado de autenticação multi-factor Azure.

As seguintes definições de autenticação multi-factor Azure estão disponíveis no portal Azure:

| Funcionalidade | Descrição |
| ------- | ----------- |
| [Bloqueio de conta](#account-lockout) | Bloqueie temporariamente as contas de utilização da Autenticação Multi-Factor Azure se houver demasiadas tentativas de autenticação negadas seguidas. Esta funcionalidade aplica-se apenas aos utilizadores que introduzam um PIN para autenticar. (MFA Server) |
| [Utilizadores de bloco/desbloqueio](#block-and-unblock-users) | Impedir que utilizadores específicos possam receber pedidos de autenticação multi-factor Azure. Todas as tentativas de autenticação de utilizadores bloqueados são automaticamente negadas. Os utilizadores permanecem bloqueados durante 90 dias a partir do momento em que estão bloqueados ou desbloqueados manualmente. |
| [Alerta de fraudes](#fraud-alert) | Configurar configurações que permitem aos utilizadores reportar pedidos de verificação fraudulentos. |
| [Notificações](#notifications) | Ativar notificações de eventos a partir do MFA Server. |
| [Tokens OATH](concept-authentication-oath-tokens.md) | Usado em ambientes Azure MFA baseados em nuvem para gerir tokens OATH para utilizadores. |
| [Definições de chamadas telefónicas](#phone-call-settings) | Configurar configurações relacionadas com chamadas telefónicas e saudações para ambientes em nuvem e no local. |
| Fornecedores | Isto irá mostrar quaisquer fornecedores de autenticação existentes que possa ter associado à sua conta. Novos fornecedores de autenticação não podem ser criados a partir de 1 de setembro de 2018 |

![Portal Azure - Azure AD Configurações de autenticação multi-factor](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>Bloqueio de conta

Para evitar tentativas repetidas de MFA como parte de um ataque, as definições de bloqueio de conta permitem especificar quantas tentativas falhadas de permitir antes de a conta ficar bloqueada por um período de tempo. As definições de bloqueio de conta só são aplicadas quando um código pin é introduzido para a solicitação MFA.

Estão disponíveis as seguintes definições:

* Número de negações de MFA para desencadear bloqueio de conta
* Minutos até que o balcão de bloqueio de conta seja reposto
* Minutos até que a conta seja desbloqueada automaticamente

Para configurar as definições de bloqueio de conta, complete as seguintes definições:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
1. Navegue pelo bloqueio da conta MFA de segurança de segurança **ativa Azure**  >  **Security**  >  **MFA**  >  **Account lockout**Ative .
1. Introduza os valores necessários para o seu ambiente e, em seguida, **selecione Guardar**.

    ![Screenshot das definições de bloqueio de conta no portal Azure](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>Bloquear e desbloquear utilizadores

Se o dispositivo de um utilizador tiver sido perdido ou roubado, pode bloquear as tentativas de autenticação multi-factor do Azure para a conta associada. Quaisquer tentativas de autenticação multi-factor Azure para utilizadores bloqueados são automaticamente negadas. Os utilizadores permanecem bloqueados durante 90 dias a partir do momento em que são bloqueados.

### <a name="block-a-user"></a>Bloquear um utilizador

Para bloquear um utilizador, complete os seguintes passos:

1. Navegue para utilizadores do **Azure Ative Directory**  >  **Security**  >  **MFA**  >  **Block/desbloquear**.
1. **Selecione Adicionar** para bloquear um utilizador.
1. Selecione o **Grupo de Replicação**e, em seguida, escolha *Azure Default*.

    Introduza o nome de utilizador para o utilizador bloqueado como `username\@domain.com` , em seguida, forneça um comentário no campo *'Razão'.*
1. Quando estiver pronto, selecione **OK** para bloquear o utilizador.

### <a name="unblock-a-user"></a>Desbloquear um utilizador

Para desbloquear um utilizador, complete os seguintes passos:

1. Navegue para utilizadores do **Azure Ative Directory**  >  **Security**  >  **MFA**  >  **Block/desbloquear**.
1. Na coluna *Ação* ao lado do utilizador pretendido, selecione **Desbloqueie**.
1. Insira um comentário no *campo de desbloqueio.*
1. Quando estiver pronto, selecione **OK** para desbloquear o utilizador.

## <a name="fraud-alert"></a>Alerta de fraudes

A funcionalidade de alerta de fraude permite que os utilizadores relatem tentativas fraudulentas de acesso aos seus recursos. Quando uma solicitação de MFA desconhecida e suspeita é recebida, os utilizadores podem reportar a tentativa de fraude utilizando a aplicação Microsoft Authenticator ou através do seu telemóvel.

Estão disponíveis as seguintes opções de configuração de alerta de fraude:

* **Bloquear automaticamente os utilizadores que denunciam fraude**: Se um utilizador reportar fraude, as tentativas de autenticação Azure MFA para a conta de utilizador são bloqueadas durante 90 dias ou até que um administrador desbloqueie a sua conta. Um administrador pode rever as inscrições utilizando o relatório de inscrição e tomar as medidas adequadas para evitar futuras fraudes. Um administrador pode então [desbloquear](#unblock-a-user) a conta do utilizador.
* **Código para denunciar fraude durante a saudação inicial**: Quando os utilizadores recebem uma chamada telefónica para efetuar a autenticação de vários fatores, normalmente pressionam **#** para confirmar a sua entrada. Para denunciar a fraude, o utilizador introduz um código antes de premir **#** . Este código é **0** por padrão, mas pode personalizá-lo.

   > [!NOTE]
   > As saudações de voz predefinidos da Microsoft instruem os utilizadores a **pressionar 0#** para apresentarem um alerta de fraude. Se pretender utilizar um código diferente de **0**, grave e carrere as suas próprias saudações de voz personalizadas com instruções apropriadas para os seus utilizadores.

Para ativar e configurar alertas de fraude, complete as seguintes etapas:

1. Consulte o alerta de fraude MFA de segurança **do Diretório Ativo Azure**  >  **Security**  >  **MFA**  >  **Fraud alert**.
1. Defina o *Permitir que os utilizadores apresentem alertas de fraude* definindo para **On**.
1. Configure os *utilizadores que comuniquem automaticamente a fraude* ou código para denunciar fraude durante a configuração *inicial da saudação,* conforme pretendido.
1. Quando estiver pronto, **selecione Guardar**.

### <a name="view-fraud-reports"></a>Ver relatórios de fraude

Selecione Detalhes de autenticação de inscrição de **diretório ativo**  >  **Sign-ins**  >  **Azure**. O relatório de fraude faz agora parte do relatório padrão de inscrições da Azure AD e irá mostrar no **"Detalhe de Resultados"** como mFA negado, Código de Fraude introduzido.
 
## <a name="notifications"></a>Notificações

As notificações por e-mail podem ser configuradas quando os utilizadores reportam alertas de fraude. Estas notificações são normalmente enviadas para administradores de identidade, uma vez que as credenciais de conta do utilizador são provavelmente comprometidas. O exemplo a seguir mostra como é um e-mail de notificação de alerta de fraude:

![Exemplo de e-mail de notificação de alerta de fraude](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

Para configurar notificações de alerta de fraude, complete as seguintes definições:

1. Consulte notificações **Azure Active Directory**de  >  **Security**  >  **autenticação multi-factor de**segurança de segurança Azure  >  **Notifications**Ative.
1. Insira o endereço de e-mail para adicionar na caixa seguinte.
1. Para remover um endereço de e-mail existente, selecione a opção **...** ao lado do endereço de e-mail pretendido e, em seguida, selecione **Delete**.
1. Quando estiver pronto, **selecione Guardar**.

## <a name="oath-tokens"></a>Tokens OATH

A Azure AD suporta a utilização de fichas OATH-TOTP SHA-1 que atualizam códigos a cada 30 ou 60 segundos. Os clientes podem comprar estes tokens ao fornecedor à sua escolha.

Os tokens de hardware OATH TOTP normalmente vêm com uma chave secreta, ou sementes, pré-programadas no token. Estas teclas devem ser inseridas no AD Azure, conforme descrito nos passos seguintes. As teclas secretas estão limitadas a 128 caracteres, o que pode não ser compatível com todos os tokens. A chave secreta só pode conter os caracteres *a-z* ou *A-Z* e os dígitos *1-7*, e deve ser codificado na *Base32*.

As fichas de hardware do OATH TOTP programáveis que podem ser ressequidas também podem ser configuradas com Azure AD no fluxo de configuração do token do software.

As fichas de hardware da OATH são suportadas como parte de uma pré-visualização pública. Para obter mais informações sobre pré-visualizações, consulte  [Termos Complementares de Utilização para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![Upload de fichas de OATH para a lâmina de fichas de OATH do MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Uma vez adquiridas as fichas, devem ser carregadas num formato de ficheiro de valores separados por vírgula (CSV), incluindo a UPN, número de série, chave secreta, intervalo de tempo, fabricante e modelo como mostrado no seguinte exemplo:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Certifique-se de que inclui a linha do cabeçalho no seu ficheiro CSV.

Uma vez devidamente formatado como um ficheiro CSV, um administrador pode então iniciar sedutar no portal Azure, navegar para **o Azure Ative Directory > Security > MFA > tokens OATH**, e carregar o ficheiro CSV resultante.

Dependendo do tamanho do ficheiro CSV, pode levar alguns minutos a processar. Selecione o botão **'Renovar'** para obter o estado atual. Se houver algum erro no ficheiro, pode descarregar um ficheiro CSV que enumera quaisquer erros para resolver. Os nomes de campo no ficheiro CSV descarregado são diferentes da versão carregada.

Uma vez abordados quaisquer erros, o administrador pode então ativar cada tecla selecionando **ativar** para o token e introduzindo o OTP exibido no token.

Os utilizadores podem ter uma combinação de até cinco fichas de hardware OATH ou aplicações autenticadoras, como a aplicação Microsoft Authenticator, configurada para ser utilizada a qualquer momento.

## <a name="phone-call-settings"></a>Definições de chamadas telefónicas

Se os utilizadores receberem chamadas telefónicas para chamadas MFA, pode configurar a sua experiência, como iD de chamada ou saudação de voz que ouvem.

Nos Estados Unidos, se ainda não configuraste o ID do MFA Caller, as chamadas de voz da Microsoft vêm dos seguintes números. Se utilizar filtros de spam, certifique-se de excluir estes números:

* *+1 (866) 539 4191*
* *+1 (855) 330 8653*
* *+1 (877) 668 6536*

> [!NOTE]
> Quando as chamadas de autenticação multi-factor Azure são feitas através da rede telefónica pública, por vezes as chamadas são encaminhadas através de uma transportadora que não suporta o ID do chamador. Por isso, o ID do chamador não está garantido, mesmo que a autenticação multi-factor Azure sempre o envie. Isto aplica-se tanto a chamadas telefónicas como a mensagens de texto fornecidas pela Autenticação Multi-Factor Azure. Se precisar de validar que uma mensagem de texto é da Autenticação Multi-Factor Azure, consulte [que códigos curtos SMS são utilizados para o envio de mensagens?](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

Para configurar o seu próprio número de identificação de chamada, complete os seguintes passos:

1. Navegue para as definições de chamada telefónica MFA de Segurança de Segurança **Ativa Azure**  >  **Security**  >  **MFA**  >  **Phone call settings**.
1. Desfita o **número de identificação do chamador MFA** para o número que deseja que os utilizadores vejam no seu telefone. Apenas números baseados nos EUA são permitidos.
1. Quando estiver pronto, **selecione Guardar**.

### <a name="custom-voice-messages"></a>Mensagens de voz personalizadas

Pode utilizar as suas próprias gravações ou saudações para autenticação multi-factor Azure com a funcionalidade de mensagens de voz personalizadas. Estas mensagens podem ser utilizadas para além ou para substituir as gravações padrão da Microsoft.

Antes de começar, esteja atento às seguintes restrições:

* Os formatos de ficheiro suportado são *.wav* e *.mp3*.
* O limite de tamanho do ficheiro é de 1 MB.
* As mensagens de autenticação devem ser inferiores a 20 segundos. Mensagens com mais de 20 segundos podem fazer com que a verificação falhe. O utilizador pode não responder antes de terminar a mensagem e os tempos de verificação.

### <a name="custom-message-language-behavior"></a>Comportamento personalizado da linguagem da mensagem

Quando uma mensagem de voz personalizada é reproduzida para o utilizador, o idioma da mensagem depende dos seguintes fatores:

* A linguagem do utilizador atual.
  * O idioma detetado pelo navegador do utilizador.
  * Outros cenários de autenticação podem comportar-se de forma diferente.
* O idioma de quaisquer mensagens personalizadas disponíveis.
  * Esta língua é escolhida pelo administrador, quando uma mensagem personalizada é adicionada.

Por exemplo, se houver apenas uma mensagem personalizada, com uma língua de alemão:

* Um utilizador que autentica em língua alemã ouvirá a mensagem alemã personalizada.
* Um utilizador que autentica em inglês ouvirá a mensagem inglesa padrão.

### <a name="custom-voice-message-defaults"></a>Padrão de mensagem de voz personalizado

Os seguintes scripts de amostra podem ser usados para criar as suas próprias mensagens personalizadas. Estas frases são as predefinições se não configurar as suas próprias mensagens personalizadas:

| Nome da mensagem | Script |
| --- | --- |
| Sucesso da autenticação | O seu s-in foi verificado com sucesso. Até logo. |
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

### <a name="set-up-a-custom-message"></a>Configurar uma mensagem personalizada

Para utilizar as suas próprias mensagens personalizadas, complete os seguintes passos:

1. Navegue para as definições de chamada telefónica MFA de Segurança de Segurança **Ativa Azure**  >  **Security**  >  **MFA**  >  **Phone call settings**.
1. Selecione **Adicionar saudação**.
1. Escolha o **tipo** de saudação, como *Saudação (padrão)* ou *Autenticação bem sucedida.*
1. Selecione o **Idioma**, com base na secção anterior sobre o [comportamento personalizado do idioma da mensagem](#custom-message-language-behavior).
1. Navegue e selecione um ficheiro de som *.mp3* ou *.wav* para carregar.
1. Quando estiver pronto, **selecione Adicionar**e, em seguida, **Guardar**.

## <a name="mfa-service-settings"></a>Definições de serviço MFA

As definições para palavras-passe de aplicações, IPs fidedignos, opções de verificação e lembre-se que a autenticação multi-factor para Azure Multi-Factor Authentication pode ser encontrada nas definições de serviço. Este é mais um portal legado, e não faz parte do portal AD AZure regular.

As definições de serviço podem ser acedidas a partir do portal Azure através da navegação para **Azure Ative Directory**  >  **Security**  >  **MFA**  >  **Obter**  >  **Configure**  >  **configurações adicionais de MFA baseadas na nuvem**. Uma nova janela ou separador abre com opções adicionais *de definições de serviço.*

## <a name="trusted-ips"></a>IPs Fidedignos

A funcionalidade _IPs Fidedigna_ da Autenticação Multi-Factor Azure contorna as indicações de autenticação de vários fatores para os utilizadores que iniciarem sedutação a partir de um intervalo de endereços IP definido. Pode definir gamas IP fidedignas para os ambientes no local até quando os utilizadores estiverem num desses locais, não existe nenhuma solicitação de autenticação multi-factor Azure.

> [!NOTE]
> Os IPs fidedignos só podem incluir gamas IP privadas quando utilizar o MFA Server. Para autenticação multi-factor Azure baseada na nuvem, só pode utilizar intervalos de endereços IP públicos.
>
> As gamas IPv6 só são suportadas na interface [de localização nomeada (pré-visualização).](../conditional-access/location-condition.md#preview-features)

Se a sua organização implementar a extensão NPS para fornecer MFA a aplicações no local, note que o endereço IP de origem sempre parecerá ser o servidor NPS que a tentativa de autenticação flui através.

| Tipo de inquilino Azure AD | Opções de recurso IP fidedignas |
|:--- |:--- |
| Gerido |**Gama específica de endereços IP**: Os administradores especificam uma gama de endereços IP que podem contornar a autenticação de vários fatores para os utilizadores que se inscrevam na intranet da empresa. Um máximo de 50 gamas IP fidedignas podem ser configuradas.|
| Federados |**Todos os Utilizadores Federados**: Todos os utilizadores federados que inserem súmis a partir do interior da organização podem contornar a autenticação de vários fatores. Os utilizadores contornam a verificação utilizando uma alegação emitida pelos Serviços da Federação de Diretórios Ativos (AD FS).<br/>**Gama específica de endereços IP**: Os administradores especificam uma gama de endereços IP que podem contornar a autenticação de vários fatores para os utilizadores que se inscrevam na intranet da empresa. |

O bypass IP fidedigno funciona apenas a partir do interior da intranet da empresa. Se selecionar a opção **Todos os Utilizadores Federados** e um utilizador entrar na intranet da empresa, o utilizador tem de autenticar utilizando a autenticação multi-factor. O processo é o mesmo mesmo se o utilizador apresentar uma reclamação de FS AD.

### <a name="end-user-experience-inside-of-corpnet"></a>Experiência de utilizador final dentro da corpnet

Quando a funcionalidade IPs fidedigna é desativada, a autenticação de vários fatores é necessária para os fluxos do navegador. As palavras-passe da aplicação são necessárias para aplicações de clientes ricos em idosos.

Quando os IPs fidedignos são utilizados, a autenticação de vários fatores não é necessária para os fluxos do navegador. As palavras-passe das aplicações não são necessárias para aplicações de clientes ricos em idades mais antigas, desde que o utilizador não tenha criado uma palavra-passe de aplicação. Depois de uma palavra-passe da aplicação estar a ser utilizada, a palavra-passe continua a ser necessária.

### <a name="end-user-experience-outside-corpnet"></a>Experiência de utilizador final fora da corpnet

Independentemente de o IP fidedigno ser definido, a autenticação de vários fatores é necessária para os fluxos do navegador. As palavras-passe da aplicação são necessárias para aplicações de clientes ricos em idosos.

### <a name="enable-named-locations-by-using-conditional-access"></a>Ativar localizações nomeadas utilizando o Acesso Condicional

Pode utilizar regras de acesso condicional para definir locais nomeados utilizando os seguintes passos:

1. No portal Azure, procure e selecione **O Diretório Ativo Azure,** em seguida, navegue para **Security**  >  **Conditional Access**  >  **locais com**acesso condicional de segurança .
1. Selecione **Nova localização**.
1. Insira um nome para o local.
1. Selecione **Mark como localização fidedigna.**
1. Introduza o intervalo IP na notação CIDR para o seu ambiente, tais como *40.77.182.32/27*.
1. Selecione **Criar**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Ativar a funcionalidade IPs fidedigno utilizando o Acesso Condicional

Para permitir iPs fidedignos utilizando políticas de acesso condicional, complete os seguintes passos:

1. No portal Azure, procure e selecione **O Diretório Ativo Azure,** em seguida, navegue para **Security**  >   **Conditional Access**  >  **locais com**acesso condicional de segurança .
1. Selecione **Configurar IPs fidedignos MFA**.
1. Na página **Definições de Serviço,** em **IPs Fidedignos,** escolha entre qualquer uma das duas opções seguintes:

   * **Para pedidos de utilizadores federados originários da minha intranet**: Para escolher esta opção, selecione a caixa de verificação. Todos os utilizadores federados que inserem súmido da rede corporativa contornam a autenticação de vários fatores utilizando uma reivindicação emitida pela AD FS. Certifique-se de que a AD FS tem uma regra para adicionar a alegação intranet ao tráfego apropriado. Se a regra não existir, crie a seguinte regra em AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Para pedidos de um leque específico de IPs públicos**: Para escolher esta opção, insira os endereços IP na caixa de texto utilizando a notação CIDR.
      * Para endereços IP que estão na gama xxx.xxx.xxx.1 através de xxx.xxx.xxx.xxx.254, utilize a notação como **xxx.xxx.xxx.0/24**.
      * Para um único endereço IP, utilize a notação como **xxx.xxx.xxx.xxx/32**.
      * Introduza até 50 intervalos de endereço IP. Os utilizadores que iniciarem sação a partir destes endereços IP contornam a autenticação de vários fatores.

1. Selecione **Guardar**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Ativar a funcionalidade IPs Fidedigno utilizando as definições de serviço

Se não quiser utilizar políticas de Acesso Condicional para ativar IPs fidedignos, pode configurar as *definições* de serviço para autenticação multi-factor Azure utilizando os seguintes passos:

1. No portal Azure, procure e selecione **O Diretório Ativo Azure,** em seguida, escolha **Utilizadores**.
1. Selecione **Multi-Factor Authentication**.
1. Em Autenticação Multi-Factor, selecione **as definições de serviço**.
1. Na página **Definições de Serviço,** em **IPs Fidedignos,** escolha uma (ou ambas) das duas opções seguintes:

   * **Para pedidos de utilizadores federados na minha intranet**: Para escolher esta opção, selecione a caixa de verificação. Todos os utilizadores federados que inserem súmido da rede corporativa contornam a autenticação de vários fatores utilizando uma reivindicação emitida pela AD FS. Certifique-se de que a AD FS tem uma regra para adicionar a alegação intranet ao tráfego apropriado. Se a regra não existir, crie a seguinte regra em AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Para pedidos a partir de um intervalo especificado de sub-redes de endereços IP**: Para escolher esta opção, insira os endereços IP na caixa de texto utilizando a notação CIDR.
      * Para endereços IP que estão na gama xxx.xxx.xxx.1 através de xxx.xxx.xxx.xxx.254, utilize a notação como **xxx.xxx.xxx.0/24**.
      * Para um único endereço IP, utilize a notação como **xxx.xxx.xxx.xxx/32**.
      * Introduza até 50 intervalos de endereço IP. Os utilizadores que iniciarem sação a partir destes endereços IP contornam a autenticação de vários fatores.

1. Selecione **Guardar**.

## <a name="verification-methods"></a>Métodos de verificação

Pode escolher os métodos de verificação disponíveis para os seus utilizadores no portal de definições de serviço. Quando os seus utilizadores inscrevem as suas contas para a Autenticação Multi-Factor Azure, eles escolhem o seu método de verificação preferido a partir das opções que ativou. Orientação para o processo de inscrição do utilizador é fornecida na [Configuração da minha conta para a autenticação de vários fatores.](../user-help/multi-factor-authentication-end-user-first-time.md)

Estão disponíveis os seguintes métodos de verificação:

| Método | Descrição |
|:--- |:--- |
| Chamada para telefone |Coloca uma chamada de voz automatizada. O utilizador atende a chamada e prime # no teclado do telefone para se autenticar. O número de telefone não está sincronizado com o Ative Directory no local. |
| Mensagem de texto para telefone |Envia uma mensagem de texto que contém um código de verificação. Solicita-se ao utilizador que introduza o código de verificação na interface de entrada. Este processo chama-se SMS unidirecciona. SMS bidirecionais significa que o utilizador deve enviar um código específico. SMS bidireccionado é precotado e não apoiado após 14 de novembro de 2018. Os administradores devem permitir um outro método para os utilizadores que anteriormente utilizaram SMS bidirecionais.|
| Notificação através de aplicação móvel |Envia uma notificação push para o seu telefone ou dispositivo registado. O utilizador visualiza a notificação e seleciona **Verificar** para completar a verificação. A aplicação Microsoft Authenticator está disponível para [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)e [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |
| Código de verificação a partir de aplicativo móvel ou token de hardware |A aplicação Microsoft Authenticator gera um novo código de verificação do OATH a cada 30 segundos. O utilizador introduz o código de verificação na interface de entrada. A aplicação Microsoft Authenticator está disponível para [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)e [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |

Para mais informações, consulte [que métodos de autenticação e verificação estão disponíveis no Azure AD?](concept-authentication-methods.md)

### <a name="enable-and-disable-verification-methods"></a>Permitir e desativar métodos de verificação

Para permitir ou desativar os métodos de verificação, complete as seguintes etapas:

1. No portal Azure, procure e selecione **O Diretório Ativo Azure,** em seguida, escolha **Utilizadores**.
1. Selecione **Multi-Factor Authentication**.
1. Em Autenticação Multi-Factor, selecione **as definições de serviço**.
1. Na página **Definições de Serviço,** nas **opções de verificação,** selecione/desescolte os métodos a fornecer aos seus utilizadores.
1. Clique em **Guardar**.

## <a name="remember-multi-factor-authentication"></a>Lembre-se da autenticação multi-factor

A funcionalidade _de autenticação multi-factor de ressoem_ que permite que os utilizadores possam contornar as verificações subsequentes durante um determinado número de dias, depois de terem assinado com sucesso um dispositivo utilizando a Autenticação Multi-Factor. Para aumentar a usabilidade e minimizar o número de vezes que um utilizador tem de efetuar MFA no mesmo dispositivo, selecione uma duração igual ou superior a 90 dias.

> [!IMPORTANT]
> Se uma conta ou dispositivo estiver comprometido, lembrar a Autenticação Multi-Factor para dispositivos fidedignos pode afetar a segurança. Se uma conta corporativa ficar comprometida ou um dispositivo de confiança for perdido ou roubado, deverá [revogar as Sessões de MFA](howto-mfa-userdevicesettings.md).
>
> A ação de restauro revoga o estado de confiança de todos os dispositivos, e o utilizador é obrigado a efetuar novamente a autenticação de vários fatores. Também pode instruir os seus utilizadores a restaurar a Autenticação Multi-Factor nos seus próprios dispositivos, tal como indicado na [Gestão das suas definições para autenticação multi-factor](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

### <a name="how-the-feature-works"></a>Como funciona a funcionalidade

A função de autenticação multi-factor de ressalsse que define um cookie persistente no navegador quando um utilizador seleciona a opção **X days** no início da súm. O utilizador não é solicitado novamente para autenticação multi-factor a partir desse mesmo navegador até que o cookie expire. Se o utilizador abrir um navegador diferente no mesmo dispositivo ou limpar os seus cookies, é solicitado novamente para verificar.

A opção **'Não' peça novamente por X days** não é mostrada em aplicações não-navegadores, independentemente de a app suportar a autenticação moderna. Estas aplicações usam _fichas de atualização_ que fornecem novos tokens de acesso a cada hora. Quando um token de atualização é validado, a Azure AD verifica se a última autenticação multi-factor ocorreu dentro do número especificado de dias.

A funcionalidade reduz o número de autenticações em aplicações web, que normalmente solicitam sempre. A funcionalidade pode aumentar o número de autenticações para clientes de autenticação moderna que normalmente solicitam a cada 90 dias, se for configurada uma duração mais baixa. Pode também aumentar o número de autenticações quando combinadas com as políticas de Acesso Condicional.

> [!IMPORTANT]
> A funcionalidade **de autenticação multi-factor de resso para o remember** não é compatível com a funcionalidade **"Manter-me assinado em** funcionalidade de AD FS", quando os utilizadores realizam a autenticação multi-factor para AD FS através do Azure Multi-Factor Authentication Server ou uma solução de autenticação multi-factor de terceiros.
>
> Se os seus utilizadores selecionarem **manter-me informado no** AD FS e também marcar o seu dispositivo como confiável para a Autenticação Multi-Factor, o utilizador não é verificado automaticamente após o fim do número de dias de autenticação de **vários fatores.** O Azure AD solicita uma autenticação nova de vários fatores, mas a AD FS devolve um token com a reivindicação e data originais de autenticação multi-factor, em vez de realizar novamente a autenticação de vários fatores. **Esta reação desencadeia um ciclo de verificação entre a Azure AD e a AD FS.**
>
> A funcionalidade **de autenticação multi-factor de resso para o remember** não é compatível com os utilizadores B2B e não será visível para os utilizadores B2B ao iniciar sessão nos inquilinos convidados.
>

### <a name="enable-remember-multi-factor-authentication"></a>Ativar a autenticação multi-factor

Para permitir e configurar a opção de os utilizadores se lembrarem do seu estado de MFA e de solicitarem o bypass, completem os seguintes passos:

1. No portal Azure, procure e selecione **O Diretório Ativo Azure,** em seguida, escolha **Utilizadores**.
1. Selecione **Multi-Factor Authentication**.
1. Em Autenticação Multi-Factor, selecione **as definições de serviço**.
1. Na página **'Definições de Serviço',** sob **a autenticação de vários fatores**, selecione o **Permitir que os utilizadores se lembrem da autenticação de vários fatores nos dispositivos em que confiam na** opção.
1. Desafie o número de dias para permitir que dispositivos fidedignos contornem a autenticação de vários fatores. Para uma experiência de utilização ótima, prolongue a duração para *90* ou mais dias.
1. Selecione **Guardar**.

### <a name="mark-a-device-as-trusted"></a>Marque um dispositivo como confiável

Depois de ativar a funcionalidade de autenticação multi-factor de ressaltação, os utilizadores podem marcar um dispositivo como confiável quando iniciarem sação selecionando a opção para **Não voltar a perguntar**.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os métodos disponíveis para utilização na Autenticação Multi-Factor Azure, consulte que métodos de [autenticação e verificação estão disponíveis no Diretório Ativo Azure?](concept-authentication-methods.md)
