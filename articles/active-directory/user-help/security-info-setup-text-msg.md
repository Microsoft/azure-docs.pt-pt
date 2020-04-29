---
title: Configurar mensagens de texto como o seu método de verificação - Azure AD
description: Como configurar a sua página de informações de Segurança (pré-visualização) para verificar a sua identidade utilizando mensagens de texto como método de verificação.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 0525ee6d0ac5df61113e5a97c9812394323ffb3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062358"
---
# <a name="set-up-text-messaging-as-your-verification-method"></a>Configurar mensagens de texto como o seu método de verificação

Pode seguir estes passos para adicionar os seus métodos de verificação de dois fatores e redefinição de palavra-passe. Depois de configurar isto pela primeira vez, pode voltar à página de **informações** de Segurança para adicionar, atualizar ou apagar as suas informações de segurança.

Se for solicitado que o instale imediatamente após iniciar sessão na sua conta de trabalho ou escola, consulte os passos detalhados no Conjunto de informações de segurança a partir do artigo de solicitação da página de início de [sessão.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Se não vir uma opção de telefone, é possível que a sua organização não lhe permita usar esta opção para verificação. Neste caso, terá de escolher outro método ou contactar o balcão de ajuda da sua organização para obter mais assistência.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Configurar mensagens de texto a partir da página de informações de Segurança

Dependendo das configurações da sua organização, poderá utilizar mensagens de texto como um dos seus métodos de informação de segurança. A opção de mensagem de texto faz parte da opção do telefone, por isso vai configurar tudo da mesma forma que faria para o seu número de telefone, mas em vez de ter a Microsoft a ligar-lhe, optará por usar uma mensagem de texto.

>[!Note]
>Se quiser receber uma chamada telefónica em vez de uma mensagem de texto, siga os passos na informação de [segurança configurar para utilizar](security-info-setup-phone-number.md) o artigo de chamadas telefónicas.

### <a name="to-set-up-text-messages"></a>Para configurar mensagens de texto

1. Inscreva-se na sua conta de trabalho https://myprofile.microsoft.com/ ou escola e depois vá para a sua página.

    ![A minha página de Perfil, mostrando links de informação de segurança destacados](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** a partir do painel de navegação esquerdo ou do link no bloco de **informações** de Segurança e, em seguida, selecione **adicionar método** a partir da página de informações de **Segurança.**

    ![Página de informações de segurança com opção de método de adição realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar uma página de método,** selecione **Telefone** a partir da lista de lançamentos e, em seguida, selecione **Adicionar**.

    ![Adicionar caixa de método, com telefone selecionado](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na página **do Telefone,** digite o número de telefone do seu dispositivo móvel, escolha um código por **Texto- Me**e, em seguida, selecione **Seguinte**.

    ![Adicione o número de telefone e escolha mensagens de texto](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Digite o código que lhe foi enviado através de mensagem de texto para o seu dispositivo móvel e, em seguida, selecione **Next**.

    ![Adicione o número de telefone e escolha mensagens de texto](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    A página muda para mostrar o seu sucesso.

    ![Notificação de sucesso, ligação do número de telefone, a escolha de receber mensagens de texto e a sua conta](media/security-info/securityinfo-myprofile-phonetext-success.png)

    A sua informação de segurança é atualizada e pode utilizar mensagens de texto para verificar a sua identidade ao utilizar a verificação em duas etapas ou o reset da palavra-passe. Se pretender fazer mensagens de texto no seu método predefinido, consulte a secção de métodos de [informação](#change-your-default-security-info-method) de segurança padrão deste artigo.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Eliminar mensagens de texto dos seus métodos de informação de segurança

Se já não quiser utilizar mensagens de texto como um método de informação de segurança, pode removê-la da página de informações de **Segurança.**

>[!Important]
>Se eliminar as mensagens de texto por engano, não há como desfazê-la. Terá de adicionar o método novamente, seguindo os passos na secção de mensagens de [texto Configurar](#set-up-text-messages-from-the-security-info-page) este artigo.

### <a name="to-delete-text-messaging"></a>Para eliminar mensagens de texto

1. Na página de informações de **Segurança,** selecione o link **Eliminar** ao lado da opção **Telefone.**

    ![Link para apagar o método de mensagens de telefone e texto a partir de informações de segurança](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selecione **Sim** da caixa de confirmação para apagar o número de **telefone.** Depois de o seu número de telefone ser apagado, é removido das suas informações de segurança e desaparece da página de informações de **Segurança.** Se o **Telefone** for o seu método predefinido, a predefinição mudará para outro método disponível.

## <a name="change-your-default-security-info-method"></a>Altere o seu método de informação de segurança padrão

Se pretender que as mensagens de texto sejam o método padrão utilizado quando iniciar sessão na sua conta de trabalho ou escolar utilizando verificação de dois fatores ou para pedidos de redefinição de palavras-passe, pode defini-lo a partir da página de informações de **Segurança.**

### <a name="to-change-your-default-security-info-method"></a>Para alterar o seu método de informação de segurança padrão

1. Na página de informações de **Segurança,** selecione o link **Alterar** ao lado da informação do método de **iniciar sessão Predefinido.**

    ![Alterar o link para o método de sessão predefinido](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selecione **Telefone - texto*_(your_phone_number_*)** da lista de métodos disponíveis e, em seguida, selecione **Confirmar**.

    ![Escolha o método de inscrição por defeito](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    O método predefinido utilizado para alterações de sessão no **Telefone - texto (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Métodos adicionais de informação de segurança

Tem opções adicionais para como a sua organização o contacta para verificar a sua identidade, com base no que está a tentar fazer. As opções incluem:

- **Aplicação autenticadora.** Descarregue e use uma aplicação autenticadora para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como configurar e utilizar a aplicação Microsoft Authenticator, consulte [Configurar informações de segurança para utilizar uma aplicação autenticadora](security-info-setup-auth-app.md).

- **Dispositivo móvel ou chamada telefónica de trabalho.** Insira o número do seu dispositivo móvel e obtenha uma chamada telefónica para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [Configurar informações](security-info-setup-phone-number.md)de segurança para utilizar chamadas telefónicas .

- **Chave de segurança.** Registe a sua chave de segurança compatível com a Microsoft e utilize-a juntamente com um PIN para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma chave de segurança, consulte [Configurar informações](security-info-setup-security-key.md)de segurança para utilizar uma chave de segurança .

- **Endereço de e-mail.** Insira o seu endereço de e-mail de trabalho ou escola para obter um e-mail para reset de palavra-passe. Esta opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar o seu e-mail, consulte [Configurar informações](security-info-setup-email.md)de segurança para utilizar o e-mail .

- **Questões de segurança.** Responda a algumas questões de segurança criadas pelo seu administrador para a sua organização. Esta opção só está disponível para reset de palavra-passe e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar as suas questões de segurança, consulte a informação de [segurança configurar para utilizar](security-info-setup-questions.md) o artigo de questões de segurança.

    >[!Note]
    >Se algumas destas opções estão em falta, é muito provável porque a sua organização não permite esses métodos. Se for esse o caso, terá de escolher um método disponível ou contactar o seu administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Redefinir a sua palavra-passe se a perdeu ou esqueceu, a partir do portal de [reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de [reset do seu trabalho ou palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da conta da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
