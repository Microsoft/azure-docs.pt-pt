---
title: Configurar questões de segurança como o seu método de verificação - Azure AD
description: Como configurar a sua página de informações de Segurança (pré-visualização) para verificar a sua identidade utilizando questões de segurança pré-definidas como método de verificação.
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
ms.openlocfilehash: d4714a78ed03a9b861f3a266cfd0d7bca456b3c4
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628355"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Configurar questões de segurança como o seu método de verificação

Pode seguir estes passos para adicionar o método de reset da palavra-passe. Depois de configurar isto pela primeira vez, pode voltar à página de **informações** de Segurança para adicionar, atualizar ou apagar as suas informações de segurança.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Se não vir a opção de questões de segurança, é possível que a sua organização não lhe permita utilizar esta opção para o seu método de reset de palavra-passe. Neste caso, terá de escolher outro método ou contactar o balcão de ajuda da sua organização para obter mais ajuda.

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

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Configurar as suas questões de segurança a partir da página de informações de Segurança

Dependendo das configurações da sua organização, poderá escolher e responder a algumas questões de segurança como um dos seus métodos de informação de segurança. O seu administrador configura o número de perguntas de segurança que é obrigado a escolher e responder.

Se utilizar questões de segurança, recomendamos usá-las em conjunto com outro método. As questões de segurança podem ser menos seguras do que outros métodos, porque algumas pessoas podem saber as respostas às suas perguntas.

> [!Note]
> As questões de segurança são armazenadas de forma privada e segura num objeto de utilizador no diretório e só podem ser respondidas por si durante o registo. Não há forma de o seu administrador ler ou modificar as suas perguntas ou respostas.
>
> Se não vir a opção de questões de segurança, é possível que a sua organização não lhe permita usar questões de segurança para verificação. Se for esse o caso, terá de escolher outro método ou contactar o seu administrador para obter mais ajuda.
>
> As contas do administrador não estão autorizadas a utilizar questões de segurança como método de redefinição de palavra-passe. Se estiver registado como uma conta de nível de administrador, não verá estas opções.

### <a name="to-set-up-your-security-questions"></a>Para configurar as suas questões de segurança

1. Inscreva-se na sua conta de trabalho https://myaccount.microsoft.com/ ou escola e depois vá para a sua página.

    ![A minha página de Perfil, mostrando links de informação de segurança destacados](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** a partir do painel de navegação esquerdo ou do link no bloco de **informações** de Segurança e, em seguida, selecione **adicionar método** a partir da página de informações de **Segurança.**

    ![Página de informações de segurança com opção de método de adição realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar uma página de método,** selecione **questões** de segurança da lista de lançamento e, em seguida, **selecione Adicionar**.

    ![Adicionar caixa de método, com questões de segurança selecionadas](media/security-info/securityinfo-myprofile-addquestions.png)

4. Na página de perguntas de **Segurança,** escolha e responda às suas questões de segurança e, em seguida, selecione **Guardar**.

    ![Adicione o número de telefone e escolha chamadas telefónicas](media/security-info/securityinfo-myprofile-securityquestions.png)

    A sua informação de segurança é atualizada e pode utilizar as suas questões de segurança para verificar a sua identidade ao utilizar o reset da palavra-passe.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Elimine questões de segurança dos seus métodos de informação de segurança

Se já não quiser usar as suas questões de segurança como um método de informação de segurança, pode removê-las da página de informações de **Segurança.**

>[!Important]
>Se apagar as suas questões de segurança por engano, não há como desfazê-lo. Terá de adicionar o método novamente, seguindo os passos na secção de perguntas de [segurança](#set-up-your-security-questions-from-the-security-info-page) deste artigo.

### <a name="to-delete-your-security-questions"></a>Para apagar as suas questões de segurança

1. Na página de informações de **Segurança,** selecione o link **Eliminar** ao lado da opção de perguntas de **segurança.**

    ![Link para eliminar o método do telefone a partir de informações de segurança](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Selecione **Sim** da caixa de confirmação para eliminar as suas **questões de Segurança**. Após a eliminação das suas questões de segurança, o método é removido das suas informações de segurança e desaparece da página de informações de **Segurança.**

## <a name="additional-security-info-methods"></a>Métodos adicionais de informação de segurança

Tem opções adicionais para como a sua organização o contacta para verificar a sua identidade, com base no que está a tentar fazer. As opções incluem:

- **Aplicação autenticadora.** Descarregue e use uma aplicação autenticadora para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como configurar e utilizar a aplicação Microsoft Authenticator, consulte [Configurar informações de segurança para utilizar uma aplicação autenticadora](security-info-setup-auth-app.md).

- **Texto do dispositivo móvel.** Insira o número do seu dispositivo móvel e obtenha um código de texto que utilizará para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma mensagem de texto (SMS), consulte Configurar informações de segurança para utilizar mensagens de [texto (SMS)](security-info-setup-text-msg.md).

- **Dispositivo móvel ou chamada telefónica de trabalho.** Insira o número do seu dispositivo móvel e obtenha uma chamada telefónica para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [Configurar informações](security-info-setup-phone-number.md)de segurança para utilizar chamadas telefónicas .

- **Chave de segurança.** Registe a sua chave de segurança compatível com a Microsoft e utilize-a juntamente com um PIN para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma chave de segurança, consulte [Configurar informações](security-info-setup-security-key.md)de segurança para utilizar uma chave de segurança .

- **Endereço de e-mail.** Insira o seu endereço de e-mail de trabalho ou escola para obter um e-mail para reset de palavra-passe. Esta opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar o seu e-mail, consulte [Configurar informações](security-info-setup-email.md)de segurança para utilizar o e-mail .

    >[!Note]
    >Se algumas destas opções estão em falta, é muito provável porque a sua organização não permite esses métodos. Se for esse o caso, terá de escolher um método disponível ou contactar o seu administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Redefinir a sua palavra-passe se a perdeu ou esqueceu, a partir do portal de [reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de [reset do seu trabalho ou palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da conta da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
