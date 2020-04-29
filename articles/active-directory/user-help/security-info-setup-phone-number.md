---
title: Configura risa o seu número de telefone como o seu método de verificação - Azure AD
description: Como configurar a sua página de Informações de Segurança (pré-visualização) para verificar a sua identidade utilizando o seu número de telefone e dispositivo móvel como método de verificação.
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
ms.openlocfilehash: e85be9140aabe5b66e63d3ccdd4a3ea907a1d6fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062409"
---
# <a name="set-up-a-phone-number-as-your-verification-method"></a>Configurar um número de telefone como o seu método de verificação

Pode seguir estes passos para adicionar os seus métodos de verificação de dois fatores e redefinição de palavra-passe. Depois de configurar isto pela primeira vez, pode voltar à página de **informações** de Segurança para adicionar, atualizar ou apagar as suas informações de segurança.

Se for solicitado que o instale imediatamente após iniciar sessão na sua conta de trabalho ou escola, consulte os passos detalhados no Conjunto de informações de segurança a partir do artigo de solicitação da página de início de [sessão.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> As informações de segurança não suportam usar extensões de telefone. Mesmo que adicione o formato adequado, +1 4255551234X12345, as extensões são removidas antes da chamada ser colocada.
>
> Se não vir uma opção de telefone, é possível que a sua organização não lhe permita usar esta opção para verificação. Neste caso, terá de escolher outro método ou contactar o balcão de ajuda da sua organização para obter mais assistência.

## <a name="security-verification-versus-password-reset-authentication"></a>Verificação de segurança versus autenticação de reset de palavra-passe

Os métodos de informação de segurança são utilizados tanto para a verificação de segurança de dois fatores como para o reset de palavras-passe. No entanto, nem todos os métodos podem ser utilizados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| Aplicação de autenticação | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Mensagens de texto | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Telefonemas | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Chave de segurança | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Conta de e-mail | Apenas a autenticação de reset da palavra-passe. Terá de escolher outro método para verificação de dois fatores. |
| Perguntas de segurança | Apenas a autenticação de reset da palavra-passe. Terá de escolher outro método para verificação de dois fatores. |

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Configurar chamadas telefónicas a partir da página de informações de Segurança

Dependendo das configurações da sua organização, poderá utilizar chamadas telefónicas como um dos seus métodos de informação de segurança.

>[!Note]
>Se quiser receber uma mensagem de texto em vez de uma chamada telefónica, siga os passos na configuração da informação de segurança para utilizar o artigo de mensagens de [texto.](security-info-setup-text-msg.md)

### <a name="to-set-up-phone-calls"></a>Para configurar chamadas telefónicas

1. Inscreva-se na sua conta de trabalho https://myprofile.microsoft.com/ ou escola e depois vá para a sua página.

    ![A minha página de Perfil, mostrando links de informação de segurança destacados](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** a partir do painel de navegação esquerdo ou do link no bloco de **informações** de Segurança e, em seguida, selecione **adicionar método** a partir da página de informações de **Segurança.**

    ![Página de informações de segurança com opção de método de adição realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar uma página de método,** selecione **Telefone** a partir da lista de lançamentos e, em seguida, selecione **Adicionar**.

    ![Adicionar caixa de método, com telefone selecionado](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na página **do Telefone,** digite o número de telefone do seu dispositivo móvel, escolha **Ligue-me**e, em seguida, selecione **Next**.

    ![Adicione o número de telefone e escolha chamadas telefónicas](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Atenda a chamada de verificação, enviada para o número de telefone que inseriu, e siga as instruções.

    A página muda para mostrar o seu sucesso.

    ![Notificação de sucesso, ligando o número de telefone, a escolha de receber chamadas telefónicas, e a sua conta](media/security-info/securityinfo-myprofile-phonetext-success.png)

    A sua informação de segurança é atualizada e pode utilizar chamadas telefónicas para verificar a sua identidade ao utilizar a verificação em duas etapas ou o reset da palavra-passe. Se pretender efazer chamadas telefónicas do seu método predefinido, consulte a secção de [métodos](#change-your-default-security-info-method) de segurança padrão alteração do artigo.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Eliminar chamadas telefónicas dos seus métodos de informação de segurança

Se já não quiser utilizar chamadas telefónicas como método de informação de segurança, pode removê-la da página de informações de **Segurança.**

>[!Important]
>Se apagar chamadas por engano, não há como desfazê-lo. Terá de adicionar o método novamente, seguindo os passos na secção de [chamadas telefónicas](#set-up-phone-calls-from-the-security-info-page) deste artigo.

### <a name="to-delete-phone-calls"></a>Para apagar chamadas telefónicas

1. Na página de informações de **Segurança,** selecione o link **Eliminar** ao lado da opção **Telefone.**

    ![Link para eliminar o método do telefone a partir de informações de segurança](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selecione **Sim** da caixa de confirmação para apagar o número de **telefone.** Depois de o seu número de telefone ser apagado, é removido das suas informações de segurança e desaparece da página de informações de **Segurança.** Se o **Telefone** for o seu método predefinido, a predefinição mudará para outro método disponível.

## <a name="change-your-default-security-info-method"></a>Altere o seu método de informação de segurança padrão

Se pretender que as chamadas telefónicas sejam o método predefinido utilizado quando iniciar sessão na sua conta de trabalho ou escolar utilizando verificação de dois fatores ou para pedidos de reset de palavra-passe, pode defini-lo a partir da página de informações de **Segurança.**

### <a name="to-change-your-default-security-info-method"></a>Para alterar o seu método de informação de segurança padrão

1. Na página de informações de **Segurança,** selecione o link **Alterar** ao lado da informação do método de **iniciar sessão Predefinido.**

    ![Alterar o link para o método de sessão predefinido](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selecione **Telefone - chamada*_(your_phone_number_*)** da lista de métodos disponíveis e, em seguida, selecione **Confirmar**.

    ![Escolha o método de inscrição por defeito](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    O método predefinido utilizado para alterações de sessão no **Telefone - chamada (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Métodos adicionais de informação de segurança

Tem opções adicionais para como a sua organização o contacta para verificar a sua identidade, com base no que está a tentar fazer. As opções incluem:

- **Aplicação autenticadora.** Descarregue e use uma aplicação autenticadora para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como configurar e utilizar a aplicação Microsoft Authenticator, consulte [Configurar informações de segurança para utilizar uma aplicação autenticadora](security-info-setup-auth-app.md).

- **Texto do dispositivo móvel.** Insira o número do seu dispositivo móvel e obtenha um código de texto que utilizará para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma mensagem de texto (SMS), consulte Configurar informações de segurança para utilizar mensagens de [texto (SMS)](security-info-setup-text-msg.md).

- **Chave de segurança.** Registe a sua chave de segurança compatível com a Microsoft e utilize-a juntamente com um PIN para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma chave de segurança, consulte [Configurar informações](security-info-setup-security-key.md)de segurança para utilizar uma chave de segurança .

- **Endereço de e-mail.** Insira o seu endereço de e-mail de trabalho ou escola para obter um e-mail para reset de palavra-passe. Esta opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar o seu e-mail, consulte [Configurar informações](security-info-setup-email.md)de segurança para utilizar o e-mail .

- **Questões de segurança.** Responda a algumas questões de segurança criadas pelo seu administrador para a sua organização. Esta opção só está disponível para reset de palavra-passe e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar as suas questões de segurança, consulte a informação de [segurança configurar para utilizar](security-info-setup-questions.md) o artigo de questões de segurança.

    >[!Note]
    >Se algumas destas opções estão em falta, é muito provável porque a sua organização não permite esses métodos. Se for esse o caso, terá de escolher um método disponível ou contactar o seu administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Redefinir a sua palavra-passe se a perdeu ou esqueceu, a partir do portal de [reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de [reset do seu trabalho ou palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da conta da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
