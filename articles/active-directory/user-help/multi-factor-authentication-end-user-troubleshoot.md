---
title: Problemas comuns & soluções com autenticação de conta-Azure AD
description: Saiba mais sobre possíveis problemas e soluções para alguns dos problemas mais comuns de verificação de dois fatores e sua conta corporativa ou de estudante.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lizross
ms.reviewer: kexia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d998fafbf6b3ba0547991de6c3a8e71b66d91f7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231880"
---
# <a name="common-problems-and-solutions-with-two-factor-verification-and-your-work-or-school-account"></a>Problemas comuns e soluções com verificação de dois fatores e sua conta corporativa ou de estudante

Sua organização ativou a verificação de dois fatores, o que significa que sua entrada de conta corporativa ou de estudante agora requer uma combinação de seu nome de usuário, sua senha e um dispositivo móvel ou telefone. Sua organização ativou essa verificação extra porque é mais segura do que apenas uma senha, contando com duas formas de autenticação: algo que você conhece e algo que você tem com você. A verificação de dois fatores pode ajudar a impedir que hackers mal-intencionados se sobrepassem a você, porque mesmo que tenham sua senha, é provável que eles não tenham seu dispositivo também.

Há alguns problemas comuns de verificação de dois fatores que parecem ocorrer com mais frequência do que qualquer um de nós gostaríamos. Nós reunimos este artigo esperando abordar os problemas mais comuns e algumas correções possíveis.

>[!Important]
>Este conteúdo destina-se aos utilizadores. Se for um administrador, pode encontrar mais informações sobre como configurar e gerir o ambiente do Azure Active Directory (Azure AD) na [Documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory).
>
>Esse conteúdo também destina-se apenas ao uso com sua conta corporativa ou de estudante, a conta fornecida por sua organização (por exemplo, alain@contoso.com). Se você estiver tendo problemas com a verificação de dois fatores e sua conta Microsoft pessoal, a conta que você configurou para si mesmo (por exemplo, danielle@outlook.com), consulte [Ativar ou desativar a verificação de dois fatores para seu conta Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-forgot-my-mobile-device-at-home"></a>Esqueci meu dispositivo móvel em casa

Isso acontece. Você deixou seu dispositivo móvel em casa e agora não pode usar seu telefone para verificar se você está dizendo que está. Se você adicionou anteriormente outro método para entrar em sua conta, como seu telefone comercial, você deve ser capaz de usar esse método agora. Se você nunca adicionou um método de verificação adicional, precisará entrar em contato com o suporte técnico e fazer com que eles o ajudem a entrar em sua conta.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Para entrar em sua conta corporativa ou de estudante usando outro método de verificação

1. Entre em sua conta normalmente e escolha o link **entrar de outra maneira** na página **verificação de dois fatores** .

    ![Alterar método de verificação de entrada](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Se você não vir o link **entrar de outra maneira** , isso significa que você não configurou nenhum outro método de verificação. Você precisará entrar em contato com o administrador para obter ajuda para entrar em sua conta.

2. Escolha o método de verificação alternativo e continue com o processo de verificação de dois fatores.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Perdi meu dispositivo móvel ou ele foi roubado

Se você perdeu ou teve seu dispositivo móvel roubado, você pode entrar usando um método diferente ou pode pedir que seu suporte técnico Limpe suas configurações. É altamente recomendável permitir que o suporte técnico saiba se o seu telefone foi perdido ou roubado, para que as atualizações apropriadas possam ser feitas em sua conta. Depois que as configurações forem limpas, você será solicitado a [se registrar para a verificação de dois fatores](multi-factor-authentication-end-user-first-time.md) na próxima vez que entrar.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Não estou obtendo o código de verificação enviado ao meu dispositivo móvel

Não obter seu código de verificação é um problema comum e, em geral, está relacionado ao seu dispositivo móvel e suas configurações. Algumas coisas possíveis a serem experimentadas:

- **Reinicie o dispositivo móvel.** Às vezes, o dispositivo precisa apenas de uma atualização. Reiniciar o dispositivo encerra todos os processos em segundo plano ou serviços que estão em execução no momento e pode causar problemas, juntamente com a atualização dos componentes principais do dispositivo, reiniciando-os caso tenham falhado em algum momento.

- **Verifique se as informações de segurança estão corretas.** Verifique se as informações do método de verificação de segurança estão corretas, principalmente seus números de telefone. Se você colocar o número de telefone errado, todos os alertas vão para esse número incorreto. Felizmente, esse usuário não poderá fazer nada com os alertas, mas também não o ajudará a entrar em sua conta. Para verificar se suas informações estão corretas, consulte as instruções no artigo [gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md) .

- **Verifique se suas notificações estão ativadas.** Verifique se o dispositivo móvel tem notificações ativadas e se você selecionou um método de notificação que permite chamadas telefônicas, seu aplicativo de autenticação e seu aplicativo de mensagens (para mensagens de texto) enviar notificações de alerta visíveis para seu dispositivo móvel.

- **Verifique se você tem um sinal de dispositivo e uma conexão com a Internet.** Verifique se suas chamadas telefônicas e mensagens de texto estão sendo passadas para seu dispositivo móvel. Peça para um amigo chamá-lo e enviar uma mensagem de texto para certificar-se de receber ambos. Se não estiver, primeiro verifique se o dispositivo móvel está ligado. Se o dispositivo estiver ligado, mas você ainda não estiver recebendo a chamada ou o texto, provavelmente haverá um problema com a rede e você precisará conversar com seu provedor. Se você costuma ter problemas relacionados a sinais, recomendamos que você instale e use o [aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md) em seu dispositivo móvel. O aplicativo autenticador pode gerar códigos de segurança aleatórios para entrada, sem a necessidade de nenhum sinal de célula ou conexão com a Internet.

- **Desligar não incomodar.** Certifique-se de que você não ativou o recurso não **incomodar** para seu dispositivo móvel. Quando esse recurso é ativado, as notificações não têm permissão para alertá-lo em seu dispositivo móvel. Consulte o manual do seu dispositivo móvel para obter instruções sobre como desativar esse recurso.

- **Desbloquear números de telefone** No Estados Unidos, as chamadas de voz da Microsoft vêm dos seguintes números: + 1 (866) 539 4191, + 1 (855) 330 8653 e + 1 (877) 668 6536.

- **Verifique as configurações relacionadas à bateria.** Isso parece um pouco estranho na superfície, mas se você tiver configurado a otimização da bateria para impedir que aplicativos menos usados permaneçam ativos em segundo plano, o sistema de notificação provavelmente foi afetado. Para tentar corrigir esse problema, desative a otimização da bateria para seu aplicativo de autenticação e seu aplicativo de mensagens e tente entrar novamente na sua conta.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Não estou recebendo uma solicitação para minha segunda informação de verificação

Se você tiver entrado em sua conta corporativa ou de estudante usando seu nome de usuário e senha, mas não tiver sido avisado sobre suas informações adicionais de verificação de segurança, talvez você ainda não tenha configurado seu dispositivo. Seu dispositivo móvel deve ser configurado especificamente para funcionar com o método de verificação de segurança adicional. Para certificar-se de que você ativou seu dispositivo móvel e que ele está disponível para uso com seu método de verificação, consulte o artigo [gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md) . Se você souber que não configurou seu dispositivo ou sua conta, poderá fazer isso agora seguindo as etapas no artigo [configurar minha conta para a verificação em duas etapas](multi-factor-authentication-end-user-first-time.md) .

## <a name="i-got-a-new-phone-number-how-do-i-change-it-for-two-factor-verification"></a>Recebi um novo número de telefone, como posso alterá-lo para a verificação de dois fatores?

Se você obtiver um novo número de telefone, precisará atualizar os detalhes do método de verificação de segurança para que seus prompts de verificação vá para o local certo. Para atualizar seu método de verificação, siga as etapas na seção **Adicionar ou alterar seu número de telefone** do artigo [gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) .

## <a name="i-got-a-new-mobile-device-how-do-i-add-it"></a>Tenho um novo dispositivo móvel, como posso adicioná-lo?

Se você obtiver um novo dispositivo móvel, precisará configurá-lo para funcionar com a verificação de dois fatores. Esta é uma solução de várias etapas:

1. Configure seu dispositivo para trabalhar com sua conta corporativa ou de estudante seguindo as etapas no artigo [configurar minha conta para a verificação em duas etapas](multi-factor-authentication-end-user-first-time.md) .

2. Atualize suas informações de conta e dispositivo na página de **verificação de segurança adicional** , excluindo o dispositivo antigo e adicionando o novo. Para obter mais informações, consulte o artigo [gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md) .

3. Opcional. Baixe, instale e configure o aplicativo Microsoft Authenticator em seu dispositivo móvel seguindo as etapas no artigo [baixar e instalar o aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md) .

4. Opcional. Ative a verificação de dois fatores para seus dispositivos confiáveis seguindo as etapas na seção **Ativar prompts de verificação de dois fatores em um dispositivo confiável no** artigo [gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) .

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Estou tendo problemas para entrar no meu dispositivo móvel enquanto viaja

Você pode achar mais difícil usar um método de verificação relacionado a dispositivos móveis, como uma mensagem de texto, enquanto você está em um local internacional. Também é possível que seu dispositivo móvel possa fazer com que você incorra em tarifas de roaming. Para essa situação, recomendamos que você use o aplicativo Microsoft Authenticator, com a opção de conectar-se a um hotspot Wi-Fi. Para obter mais informações sobre como baixar, instalar e configurar o aplicativo Microsoft Authenticator em seu dispositivo móvel, consulte o artigo [baixar e instalar o aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md) .

## <a name="i-cant-get-my-app-passwords-to-work"></a>Não consigo fazer com que minhas senhas de aplicativo funcionem

As senhas de aplicativo substituem sua senha normal para aplicativos de área de trabalho mais antigos que não dão suporte à verificação de dois fatores. Primeiro, certifique-se de que você digitou a senha corretamente. Se isso não corrigir, tente criar uma nova senha de aplicativo para o aplicativo seguindo as etapas na seção **criar e excluir senhas de aplicativo usando o portal meus aplicativos** do artigo [gerenciar senhas de aplicativo para a verificação em duas etapas](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) .

## <a name="why-cant-i-turn-two-factor-verification-off"></a>Por que não posso ativar a verificação de dois fatores?

Se você estiver usando a verificação de dois fatores com sua conta corporativa ou de estudante (por exemplo, alain@contoso.com), provavelmente significa que sua organização decidiu que você deve usar esse recurso de segurança adicionado. Como sua organização decidiu que você deve usar esse recurso, não há como desativá-lo individualmente. Se, no entanto, você estiver usando a verificação de dois fatores com uma conta pessoal, como alain@outlook.com, você poderá ativar e desativar o recurso. Para obter instruções sobre como controlar a verificação de dois fatores para suas contas pessoais da Microsoft, consulte [Ativar ou desativar a verificação de dois fatores para sua conta Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Não encontrei uma resposta para o meu problema

Se você tiver tentado essas etapas, mas ainda estiver com problemas, entre em contato com o suporte técnico para obter ajuda.

## <a name="related-articles"></a>Artigos relacionados

- [Gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

- [FAQ da aplicação Microsoft Authenticator](user-help-auth-app-faq.md)
