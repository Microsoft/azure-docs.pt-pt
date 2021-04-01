---
title: Configurar mensagens de texto como o seu método de verificação - Azure AD
description: Como configurar a sua página de informações de Segurança (pré-visualização) para verificar a sua identidade usando mensagens de texto como método de verificação.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: c68e01e0eb7c926f47c99b16efa87d23a10b6711
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91537038"
---
# <a name="set-up-text-messaging-as-your-verification-method"></a>Configurar mensagens de texto como o seu método de verificação

Pode seguir estes passos para adicionar os seus métodos de verificação de dois fatores e de reset de palavra-passe. Depois de configurar isto pela primeira vez, pode voltar à página **de informações de Segurança** para adicionar, atualizar ou apagar as suas informações de segurança.

Se for solicitado que o mesmo o faça imediatamente após iniciar sôms na sua conta de trabalho ou na escola, consulte os passos detalhados na Configuração da sua informação de segurança a partir do artigo [de pedido de inscrição na página.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Se não vir uma opção de telefone, é possível que a sua organização não permita que use esta opção para verificação. Neste caso, terá de escolher outro método ou contactar o balcão de ajuda da sua organização para obter mais assistência.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Configurar mensagens de texto a partir da página de informações de Segurança

Dependendo das definições da sua organização, poderá utilizar mensagens de texto como um dos seus métodos de informação de segurança. A opção de mensagem de texto faz parte da opção de telefone, por isso vai configurar tudo da mesma forma que o seu número de telefone, mas em vez de ter a Microsoft a ligar-lhe, optará por utilizar uma mensagem de texto.

>[!Note]
>Se quiser receber uma chamada telefónica em vez de uma mensagem de texto, siga os passos na [informação de segurança Configurar para usar o](security-info-setup-phone-number.md) artigo de chamadas telefónicas.

### <a name="to-set-up-text-messages"></a>Para configurar mensagens de texto

1. Inscreva-se na sua conta de trabalho ou escola e depois vá para a sua https://myaccount.microsoft.com/ página.

    ![A minha página de perfil, mostrando links de informações de segurança destacados](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações** de segurança a partir do painel de navegação à esquerda ou do link no bloco **de informações de Segurança** e, em seguida, selecione Adicionar **método** a partir da página **de informações de Segurança.**

    ![Página de informações de segurança com opção de método adicionar realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página de método Adicionar uma página de **método,** selecione **Telefone** da lista de drop-down e, em seguida, **selecione Adicionar**.

    ![Adicionar caixa de métodos, com telefone selecionado](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na página **'Telefone',** digite o número de telefone do seu dispositivo móvel, escolha **Enviar-me um código** e, em seguida, selecione **Seguinte**.

    ![Screenshot que mostra a página "Telefone", com "Envie-me um código" selecionado.](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Digite o código que lhe é enviado através de mensagem de texto para o seu dispositivo móvel e, em seguida, selecione **Seguinte**.

    ![Adicione o número de telefone e escolha mensagens de texto](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    A página muda para mostrar o seu sucesso.

    ![Notificação de sucesso, ligação do número de telefone, escolha de receber mensagens de texto e sua conta](media/security-info/securityinfo-myprofile-phonetext-success.png)

    A sua informação de segurança é atualizada e pode utilizar mensagens de texto para verificar a sua identidade ao utilizar a verificação em duas etapas ou o reset da palavra-passe. Se pretender fazer mensagens de texto o seu método padrão, consulte a secção de método de informação de [segurança predefinido](#change-your-default-security-info-method) deste artigo.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Eliminar mensagens de texto dos seus métodos de informação de segurança

Se já não pretender utilizar mensagens de texto como método de informação de segurança, pode removê-la da página de informações de **Segurança.**

>[!Important]
>Se apagar mensagens de texto por engano, não há como desfazê-la. Terá de voltar a adicionar o método, seguindo os passos na secção de mensagens de [texto configuradas](#set-up-text-messages-from-the-security-info-page) deste artigo.

### <a name="to-delete-text-messaging"></a>Para eliminar mensagens de texto

1. Na página **de informações de Segurança,** selecione o link **Eliminar** ao lado da opção **Telefone.**

    ![Link para eliminar o método de mensagens de telefone e texto a partir de informações de segurança](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selecione **Sim** na caixa de confirmação para eliminar o número **de telefone.** Depois de o seu número de telefone ser apagado, é removido da sua informação de segurança e desaparece da página de informações de **Segurança.** Se **o Telefone** for o seu método predefinido, o padrão mudará para outro método disponível.

## <a name="change-your-default-security-info-method"></a>Altere o seu método de informação de segurança padrão

Se pretender que as mensagens de texto sejam o método predefinido utilizado quando iniciar serção na sua conta de trabalho ou escola utilizando a verificação de dois fatores ou para pedidos de reset de palavra-passe, pode descrevê-la a partir da página **de informações de Segurança.**

### <a name="to-change-your-default-security-info-method"></a>Para alterar o seu método de informação de segurança padrão

1. Na página **de informações de Segurança,** selecione o link **Alterar** ao lado das informações **do método de inscrição predefinido.**

    ![Alterar link para o método de inscrição predefinido](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selecione **Telefone - texto *_(your_phone_number_*)** da lista de down-down dos métodos disponíveis e, em seguida, selecione **Confirmar**.

    ![Escolha o método para o padrão de inscrição](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    O método predefinido utilizado para as alterações de inscrição no **Telefone - texto *_(your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Métodos adicionais de informação de segurança

Tem opções adicionais para saber como a sua organização o contacta para verificar a sua identidade, com base no que está a tentar fazer. As opções incluem:

- **Aplicativo de autenticador.** Descarregue e utilize uma aplicação autenticadora para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e utilizar a aplicação Microsoft Authenticator, consulte [configurar as informações de segurança para utilizar uma aplicação autenticadora](security-info-setup-auth-app.md).

- **Dispositivo móvel ou chamada telefónica de trabalho.** Introduza o número do seu dispositivo móvel e receba uma chamada telefónica para verificação em duas etapas ou redefinição de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [Configurar informações de segurança para utilizar chamadas telefónicas](security-info-setup-phone-number.md).

- **Chave de segurança.** Registe a sua chave de segurança compatível com a Microsoft e utilize-a juntamente com um PIN para verificação em duas etapas ou redefinição de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma chave de segurança, consulte [Configurar informações de segurança para utilizar uma chave de segurança](security-info-setup-security-key.md).

- **Endereço de e-mail.** Insira o seu endereço de e-mail de trabalho ou escola para obter um e-mail para reset de senha. Esta opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar o seu e-mail, consulte [Configurar informações de segurança para utilizar o e-mail](security-info-setup-email.md).

- **Perguntas de segurança.** Responda a algumas questões de segurança criadas pelo seu administrador para a sua organização. Esta opção só está disponível para reset de palavra-passe e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar as suas questões de segurança, consulte a informação de [segurança Configurar para utilizar](security-info-setup-questions.md) o artigo questões de segurança.

    >[!Note]
    >Se algumas destas opções estão em falta, é mais provável porque a sua organização não permite esses métodos. Se for esse o caso, terá de escolher um método disponível ou contactar o seu administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Reinicie a sua palavra-passe se a tiver perdido ou esquecido, a partir do [portal de reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de Reposição do seu trabalho ou [palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da Conta Da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
