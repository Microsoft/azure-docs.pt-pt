---
title: Métodos de autenticação de telefone - Azure Ative Directory
description: Saiba como utilizar métodos de autenticação de telefone no Azure Ative Directory para ajudar a melhorar e garantir eventos de entrada
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a5e8b933f617d767f017f73fb6778a45b5a1ce3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725594"
---
# <a name="authentication-methods-in-azure-active-directory---phone-options"></a>Métodos de autenticação em Diretório Ativo Azure - opções de telefone

Para autenticação direta utilizando mensagem de texto, pode [configurar e ativar a autenticação por SMS dos utilizadores.](howto-authentication-sms-signin.md) O sin-in baseado em SMS é ótimo para os trabalhadores da Linha da Frente. Com o registo baseado em SMS, os utilizadores não precisam de saber um nome de utilizador e palavra-passe para aceder a aplicações e serviços. Em vez disso, o utilizador introduz o seu número de telemóvel registado, recebe uma mensagem de texto com um código de verificação e introduz-a na interface de entrada.

Os utilizadores também podem verificar-se usando um telemóvel ou telefone de escritório como forma secundária de autenticação usada durante a autenticação multi-factor AD AZure ou redefinição de senha de autosserviço (SSPR).

Para funcionar corretamente, os números de telefone devem estar no formato *+CountryCode PhoneNumber,* por exemplo, *+1 4251234567*.

> [!NOTE]
> Tem de haver um espaço entre o código país/região e o número de telefone.
>
> O reset da palavra-passe não suporta extensões de telefone. Mesmo no formato *+1 4251234567X12345,* as extensões são removidas antes da chamada ser feita.

## <a name="mobile-phone-verification"></a>Verificação do telemóvel

Para autenticação multi-factor AD Azure ou SSPR, os utilizadores podem optar por receber uma mensagem de texto com um código de verificação para introduzir na interface de entrada ou receber uma chamada telefónica.

Se os utilizadores não quiserem que o seu número de telemóvel seja visível no diretório, mas que queiram usá-lo para reset de palavra-passe, os administradores não devem preencher o número de telefone no diretório. Em vez disso, os utilizadores devem preencher o seu atributo **Authentication Phone** através do registo combinado de informações de segurança em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . Os administradores podem ver esta informação no perfil do utilizador, mas não são publicadas em outro lugar.

:::image type="content" source="media/concept-authentication-methods/user-authentication-methods.png" alt-text="Screenshot do portal Azure que mostra métodos de autenticação com um número de telefone preenchido":::

A Microsoft não garante uma entrega rápida de solicitação de dados por SMS ou por voz baseada em voz Azure AD pelo mesmo número. No interesse dos nossos utilizadores, podemos adicionar ou remover códigos curtos a qualquer momento à medida que fazemos ajustes de rota para melhorar a entrega de SMS. A Microsoft não suporta códigos curtos para países/regiões além dos Estados Unidos e Canadá.

### <a name="text-message-verification"></a>Verificação de mensagens de texto

Com a verificação de mensagens de texto durante a autenticação multi-factor SSPR ou Azure AD, é enviado um SMS para o número de telemóvel que contém um código de verificação. Para completar o processo de inscrição, o código de verificação fornecido é introduzido na interface de entrada.

### <a name="phone-call-verification"></a>Verificação de chamadas telefónicas

Com a verificação de chamadas telefónicas durante a Autenticação Multi-Factor SSPR ou Azure AD, é feita uma chamada de voz automatizada para o número de telefone registado pelo utilizador. Para completar o processo de inscrição, o utilizador é solicitado a pressionar # no teclado.

## <a name="office-phone-verification"></a>Verificação do telefone do escritório

Com a verificação de chamadas telefónicas durante a Autenticação Multi-Factor SSPR ou Azure AD, é feita uma chamada de voz automatizada para o número de telefone registado pelo utilizador. Para completar o processo de inscrição, o utilizador é solicitado a pressionar # no teclado.

## <a name="troubleshooting-phone-options"></a>Troubleshooting phone options (Resolução de problemas das opções de telefone)

Se tiver problemas com a autenticação do telefone para a Azure AD, reveja as seguintes etapas de resolução de problemas:

* "Atingiu o nosso limite em chamadas de verificação" ou "Atingiu o nosso limite em códigos de verificação de texto" mensagens de erro durante a sin-in
   * A Microsoft pode limitar as tentativas de autenticação repetidas que são efetuadas pelo mesmo utilizador num curto espaço de tempo. Esta limitação não se aplica ao Microsoft Authenticator ou código de verificação. Se tiver atingido estes limites, pode utilizar a App Autenticador, código de verificação ou tentar entrar novamente em alguns minutos.
* "Desculpe, estamos com problemas em verificar a sua conta" mensagem de erro durante a sing-in
   * A Microsoft pode limitar ou bloquear tentativas de autenticação de voz ou SMS que sejam realizadas pelo mesmo utilizador, número de telefone ou organização devido ao elevado número de tentativas de autenticação de voz ou SMS falhadas. Se estiver a experimentar este erro, pode tentar outro método, como a App Autenticador ou o código de verificação, ou contactar o seu administrador para obter apoio.
* Identificação de chamada bloqueada num único dispositivo.
   * Reveja quaisquer números bloqueados configurados no dispositivo.
* Número de telefone errado ou código de país/região incorreto, ou confusão entre o número de telefone pessoal versus número de telefone de trabalho.
   * Resolução de problemas do objeto do utilizador e métodos de autenticação configurados. Certifique-se de que os números de telefone corretos estão registados.
* PIN errado entrou.
   * Confirme que o utilizador utilizou o PIN correto como registado para a sua conta (apenas utilizadores do MFA Server).
* Chamada reencaminhada para a caixa postal.
   * Certifique-se de que o utilizador tem o telemóvel ligado e que o serviço está disponível na sua área, ou utilize um método alternativo.
* O utilizador está bloqueado
   * Tenha um administrador AD Azure desbloqueie o utilizador no portal Azure.
* O SMS não é subscrito no dispositivo.
   * Que o utilizador altere os métodos ou ative SMS no dispositivo.
* Fornecedores de telecomunicações defeituosos, tais como nenhuma entrada de telefone detetada, problemas de tons DTMF em falta, ID de chamada bloqueado em vários dispositivos ou SMS bloqueados em vários dispositivos.
   * A Microsoft utiliza vários fornecedores de telecomunicações para encaminhar chamadas telefónicas e mensagens SMS para autenticação. Se vir algum dos problemas acima, tenha um utilizador que tente utilizar o método pelo menos cinco vezes no prazo de 5 minutos e tenha as informações desse utilizador disponíveis quando contactar o suporte da Microsoft.

## <a name="next-steps"></a>Passos seguintes

Para começar, consulte o [tutorial para reset de senha de autosserviço (SSPR)][tutorial-sspr] e [autenticação multi-factor AD Azure][tutorial-azure-mfa].

Para saber mais sobre os conceitos SSPR, consulte [como funciona o reset da palavra-passe de autosserviço AZure AD][concept-sspr].

Para saber mais sobre os conceitos de MFA, consulte [como funciona a autenticação multi-factor Azure AD.][concept-mfa]

Saiba mais sobre a configuração dos métodos de autenticação utilizando a [beta API do Microsoft Graph REST](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
