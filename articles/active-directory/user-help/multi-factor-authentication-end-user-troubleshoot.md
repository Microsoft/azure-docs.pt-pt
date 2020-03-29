---
title: Problemas comuns com a autenticação de fator dois - Azure AD
description: Soluções para alguns dos problemas de verificação de dois fatores mais comuns e a sua conta de trabalho ou escola.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: c28b63749cfdbcd16b94cbd3ca7dd4023f46a351
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897733"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Problemas comuns com verificação de dois fatores e sua conta de trabalho ou escola

Quando a sua organização Azure Ative Directory (Azure AD) ativa a verificação de dois fatores, o seu registo de trabalho ou conta escolar requer uma combinação do seu nome de utilizador, da sua palavra-passe e de um dispositivo móvel ou telefone. É mais seguro do que apenas uma senha, confiando em duas formas de autenticação: algo que sabe, e algo que tem consigo. A verificação de dois fatores pode ajudar a impedir que hackers maliciosos finjam ser você, porque mesmo que eles tenham a sua senha, as probabilidades são que eles não têm o seu dispositivo também.

<center>

![Imagem de métodos de autenticação conceptual](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Existem alguns problemas comuns de verificação de dois fatores que parecem acontecer com mais frequência do que qualquer um de nós gostaria. Reunimos este artigo para resolver os problemas mais comuns e algumas possíveis correções.

>[!Important]
>Se for administrador, pode encontrar mais informações sobre como configurar e gerir o seu ambiente Azure AD na [documentação da AD Azure](https://docs.microsoft.com/azure/active-directory).
>
>Este conteúdo destina-se também apenas a ser utilizado com o seu trabalho ou alain@contoso.comconta escolar, que é a conta que lhe é prestada pela sua organização (por exemplo). Se tiver problemas com a verificação de dois fatores e a sua conta pessoal danielle@outlook.comda Microsoft, que é uma conta que configura para si (por exemplo, por exemplo), consulte [a verificação de dois fatores para a sua conta Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Não tenho o meu dispositivo móvel comigo.

Acontece. Deixou o seu dispositivo móvel em casa e agora não pode usar o telemóvel para verificar quem é. Se adicionou anteriormente outro método para iniciar sessão na sua conta, como o seu telefone de escritório, deverá poder usar esse método agora. Se nunca adicionou um método adicional de verificação, terá de contactar o balcão de Ajuda da sua organização e pedir-lhe que o ajude a voltar à sua conta.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Para iniciar sessão no seu trabalho ou conta escolar usando outro método de verificação

1. Inscreva-se na sua conta, mas selecione o **link De outra forma** na página de **verificação de dois fatores.**

    ![Alterar sinal no método de verificação](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Se não vir o Sign de **outra forma,** significa que não criou outros métodos de verificação. Terá de contactar o seu administrador para pedir ajuda para assinar a sua conta.

2. Escolha o seu método de verificação alternativo e continue com o processo de verificação de dois fatores.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Perdi o meu dispositivo móvel ou foi roubado.

Se perdeu ou teve o seu dispositivo móvel roubado, pode iniciar sessão utilizando um método diferente ou pode pedir ao balcão de ajuda da sua organização para limpar as suas definições. Recomendamos vivamente que o balcão de Ajuda da sua organização saiba se o seu telefone foi perdido ou roubado, para que as atualizações apropriadas possam ser feitas na sua conta. Depois de as suas definições serem apuradas, será solicitado a [registar-se para verificação de dois fatores](multi-factor-authentication-end-user-first-time.md) da próxima vez que iniciar a sua inscrição.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Não vou enviar o código de verificação para o meu dispositivo móvel.

Não obter o seu código de verificação é um problema comum e está tipicamente relacionado com o seu dispositivo móvel e as suas definições. Algumas coisas possíveis para tentar:

Experimente isto. | Informações de orientação
--------- | ------------
Reinicie o seu dispositivo móvel | Às vezes, o seu dispositivo só precisa de uma atualização. Reiniciar o seu dispositivo acaba com quaisquer processos ou serviços de fundo que estejam atualmente em execução e possam causar problemas, juntamente com refrescar os componentes do seu dispositivo, reiniciando-os caso estes se despenhem em algum momento.
Verifique se as suas informações de segurança estão corretas | Certifique-se de que a informação do seu método de verificação de segurança é correta, especialmente os números de telefone. Se colocar o número de telefone errado, todos os seus alertas irão para esse número incorreto. Felizmente, esse utilizador não poderá fazer nada com os alertas, mas também não o ajudará a inscrever-se na sua conta. Para se certificar de que as suas informações estão corretas, consulte as instruções no artigo de definições do [método de verificação de dois fatores.](multi-factor-authentication-end-user-manage-settings.md)
Verifique se as suas notificações estão ligadas | Certifique-se de que o seu dispositivo móvel tem notificações ligadas e que selecionou um método de notificação que permite chamadas telefónicas, a sua aplicação de autenticação e a sua aplicação de mensagens de texto (para mensagens de texto) enviar notificações de alerta visíveis para o seu dispositivo móvel.
Certifique-se de que tem um sinal de dispositivo e ligação à Internet | Certifique-se de que as suas chamadas telefónicas e mensagens de texto estão a chegar ao seu dispositivo móvel. Peça a um amigo para lhe ligar e enviar-lhe uma mensagem de texto para se certificar de que recebe os dois. Se não o fizer, verifique primeiro se o seu dispositivo móvel está ligado. Se o seu dispositivo estiver ligado, mas ainda não receber a chamada ou o texto, é provavelmente um problema com a sua rede e terá de falar com o seu fornecedor. Se tiver frequentemente problemas relacionados com o sinal, recomendamos que instale e utilize a [aplicação Microsoft Authenticator](user-help-auth-app-download-install.md) no seu dispositivo móvel. A aplicação autenticadora pode gerar códigos de segurança aleatórios para iniciar sessão, sem necessitar de qualquer sinal celular ou ligação à Internet.
Desligue Não perturbe | Certifique-se de que não ligou a funcionalidade **Não perturbe** a funcionalidade Não perturbe o seu dispositivo móvel. Quando esta funcionalidade é ativada, as notificações não podem alertá-lo no seu dispositivo móvel. Consulte o manual do seu dispositivo móvel para obter instruções sobre como desligar esta funcionalidade.
Desbloquear números de telefone | Nos Estados Unidos, as chamadas de voz da Microsoft provêm dos seguintes números: +1 (866) 539 4191, +1 (855) 330 8653 e +1 (877) 668 6536.
Verifique as definições relacionadas com a bateria | Este parece um pouco estranho na superfície, mas se configurar a otimização da bateria para impedir que as aplicações menos utilizadas permaneçam ativas em segundo plano, o seu sistema de notificação foi provavelmente afetado. Para tentar corrigir este problema, desligue a otimização da bateria para a sua aplicação de autenticação e para a sua aplicação de mensagens e, em seguida, tente iniciar sessão na sua conta novamente.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Não estou a ser solicitado para a minha segunda informação de verificação.

Se inscreveu no seu trabalho ou na sua conta escolar usando o seu nome de utilizador e senha, mas ainda não foi solicitado sobre as suas informações adicionais de verificação de segurança, talvez ainda não tenha configurado o seu dispositivo. O seu dispositivo móvel deve ser configurado para funcionar com o seu método adicional de verificação de segurança. Para se certificar de que ligou o seu dispositivo móvel e que está disponível para utilizar com o seu método de verificação, consulte o artigo de [definições do método de verificação de dois fatores.](multi-factor-authentication-end-user-manage-settings.md) Se sabe que não configura o seu dispositivo ou a sua conta, pode fazê-lo agora seguindo os passos na Configuração da minha conta para artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>Tenho um novo número de telefone e quero adicioná-lo.

Se tiver um novo número de telefone, terá de atualizar os detalhes do seu método de verificação de segurança para que as suas solicitações de verificação se desloquem ao local certo. Para atualizar o seu método de verificação, siga os passos no **Add ou altere** a secção de números de telefone do artigo de definições do [método de verificação de dois fatores.](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>Tenho um novo dispositivo móvel e quero adicioná-lo.

Se tiver um novo dispositivo móvel, terá de o configurar para funcionar com verificação de dois fatores. Esta é uma solução em várias etapas:

1. Configurar o seu dispositivo para trabalhar com o seu trabalho ou conta escolar seguindo os passos na Configuração da minha conta para artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)

1. Atualize as informações da sua conta e do dispositivo na página adicional de verificação de **segurança,** apagando o seu antigo dispositivo e adicionando o seu novo. Para mais informações, consulte o artigo de [definições do método de verificação de dois fatores.](multi-factor-authentication-end-user-manage-settings.md)

Passos opcionais:

- Descarregue, instale e instale a aplicação Microsoft Authenticator no seu dispositivo móvel seguindo os passos no Download e instale o artigo [da aplicação Microsoft Authenticator.](user-help-auth-app-download-install.md)

- Turn on two-factor verification for your trusted devices by following the steps in the **Turn on two-factor verification prompts on a trusted device** section of the [Manage your two-factor verification method settings](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) article.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Estou com problemas em iniciar sessão no meu dispositivo móvel enquanto viajo.

Você pode ter mais dificuldade em usar um método de verificação relacionado com dispositivos móveis, como uma mensagem de texto, enquanto você está em um local internacional. Também é possível que o seu dispositivo móvel possa causar-lhe taxas de roaming. Para esta situação, recomendamos que utilize a aplicação Microsoft Authenticator, com a opção de se ligar a um hotspot Wi-Fi. Para obter mais informações sobre como descarregar, instalar e configurar a aplicação Microsoft Authenticator no seu dispositivo móvel, consulte o Download e instale o artigo [da aplicação Microsoft Authenticator.](user-help-auth-app-download-install.md)

## <a name="i-cant-get-my-app-passwords-to-work"></a>Não consigo que as minhas palavras-passe funcionem.

As palavras-passe da aplicação substituem a sua senha normal por aplicações de ambiente de trabalho mais antigas que não suportam a verificação de dois fatores. Primeiro, certifique-se de que escreveu corretamente a palavra-passe. Se isso não o corrigir, tente criar uma nova senha de aplicação para a app, seguindo os passos na Create e elimine as **palavras-passe** da aplicação utilizando a secção portal My Apps da [aplicação Manage passwords para artigo de verificação em duas etapas.](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)

## <a name="i-cant-turn-two-factor-verification-off"></a>Não posso desligar a verificação de dois fatores.

Se estiver a usar a verificação de dois fatores alain@contoso.comcom o seu trabalho ou conta escolar (por exemplo), o mais provável significa que a sua organização decidiu que deve usar esta funcionalidade de segurança adicional. Como a sua organização decidiu que deve usar esta funcionalidade, não há como desligá-la individualmente. Se, no entanto, estiver a utilizar a verificação de dois fatores com uma conta pessoal, como, alain@outlook.comtem a capacidade de ligar e desligar a funcionalidade. Para obter instruções sobre como controlar a verificação de dois fatores para as suas contas pessoais, consulte [a verificação de dois fatores ligado ou desligado para](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)a sua conta Microsoft .

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Não encontrei uma resposta para o meu problema.

Se já tentou estes passos, mas ainda está com problemas, contacte o balcão de ajuda da sua organização para obter assistência.

## <a name="related-articles"></a>Artigos relacionados

- [Gerencie as definições do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Configurar a minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

- [FAQ da aplicação Microsoft Authenticator](user-help-auth-app-faq.md)
