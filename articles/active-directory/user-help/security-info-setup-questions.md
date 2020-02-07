---
title: Configurar perguntas de segurança como seu método de verificação – Azure AD
description: Como configurar sua página de informações de segurança (versão prévia) para verificar sua identidade usando perguntas de segurança predefinidas como seu método de verificação.
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
ms.openlocfilehash: 236f1e4e41488b926d9fc2e5e717e68090a0ed7b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063973"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Configurar perguntas de segurança como seu método de verificação

Você pode seguir estas etapas para adicionar o método de redefinição de senha. Depois de configurar isto pela primeira vez, pode voltar à página de **informações** de Segurança para adicionar, atualizar ou apagar as suas informações de segurança.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Se você não vir a opção de perguntas de segurança, é possível que sua organização não permita que você use essa opção para o método de redefinição de senha. Nesse caso, você precisará escolher outro método ou entrar em contato com o suporte técnico da sua organização para obter mais ajuda.

## <a name="security-verification-versus-password-reset-authentication"></a>Verificação de segurança versus autenticação de redefinição de senha

Os métodos de informações de segurança são usados para a verificação de segurança de dois fatores e para a redefinição de senha. No entanto, nem todos os métodos podem ser usados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| Aplicativo autenticador | Verificação de dois fatores e autenticação de redefinição de senha. |
| Mensagens de texto | Verificação de dois fatores e autenticação de redefinição de senha. |
| Chamadas telefônicas | Verificação de dois fatores e autenticação de redefinição de senha. |
| Chave de segurança | Verificação de dois fatores e autenticação de redefinição de senha. |
| Conta de email | Somente autenticação de redefinição de senha. Você precisará escolher outro método para verificação de dois fatores. |
| Questões de segurança | Somente autenticação de redefinição de senha. Você precisará escolher outro método para verificação de dois fatores. |

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Configurar suas perguntas de segurança na página de informações de segurança

Dependendo das configurações da sua organização, você poderá escolher e responder algumas perguntas de segurança como um dos seus métodos de informações de segurança. O administrador configura o número de perguntas de segurança que você precisa escolher e responder.

Se você usar perguntas de segurança, é recomendável usá-las em conjunto com outro método. As perguntas de segurança podem ser menos seguras do que outros métodos, pois algumas pessoas podem saber as respostas para suas perguntas.

> [!Note]
> As perguntas de segurança são armazenadas de forma privada e segura em um objeto de usuário no diretório e só podem ser respondidas por você durante o registro. Não há como seu administrador ler ou modificar suas perguntas ou respostas.
>
> Se você não vir a opção de perguntas de segurança, é possível que sua organização não permita que você use perguntas de segurança para verificação. Se esse for o caso, você precisará escolher outro método ou entrar em contato com o administrador para obter mais ajuda.
>
> As contas de administrador não têm permissão para usar perguntas de segurança como um método de redefinição de senha. Se você estiver conectado como uma conta de nível de administrador, essas opções não serão exibidas.

### <a name="to-set-up-your-security-questions"></a>Para configurar suas perguntas de segurança

1. Inscreva-se na sua conta de trabalho ou escola e depois vá à sua página de https://myprofile.microsoft.com/.

    ![Minha página de perfil, mostrando links de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** a partir do painel de navegação esquerdo ou do link no bloco de **informações** de Segurança e, em seguida, selecione **adicionar método** a partir da página de informações de **Segurança.**

    ![Página informações de segurança com a opção Adicionar método realçado](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar uma página de método,** selecione **questões** de segurança da lista de lançamento e, em seguida, **selecione Adicionar**.

    ![Caixa Adicionar método, com perguntas de segurança selecionadas](media/security-info/securityinfo-myprofile-addquestions.png)

4. Na página de perguntas de **Segurança,** escolha e responda às suas questões de segurança e, em seguida, selecione **Guardar**.

    ![Adicionar número de telefone e escolher chamadas telefônicas](media/security-info/securityinfo-myprofile-securityquestions.png)

    Suas informações de segurança são atualizadas e você pode usar suas perguntas de segurança para verificar sua identidade ao usar a redefinição de senha.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Excluir perguntas de segurança de seus métodos de informações de segurança

Se já não quiser usar as suas questões de segurança como um método de informação de segurança, pode removê-las da página de informações de **Segurança.**

>[!Important]
>Se você excluir suas perguntas de segurança por engano, não há como desfazê-las. Terá de adicionar o método novamente, seguindo os passos na secção de perguntas de [segurança](#set-up-your-security-questions-from-the-security-info-page) deste artigo.

### <a name="to-delete-your-security-questions"></a>Para excluir suas perguntas de segurança

1. Na página de informações de **Segurança,** selecione o link **Eliminar** ao lado da opção de perguntas de **segurança.**

    ![Link para excluir o método de telefone das informações de segurança](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Selecione **Sim** da caixa de confirmação para eliminar as suas **questões de Segurança**. Após a eliminação das suas questões de segurança, o método é removido das suas informações de segurança e desaparece da página de informações de **Segurança.**

## <a name="additional-security-info-methods"></a>Métodos adicionais de informações de segurança

Você tem opções adicionais de como sua organização entra em contato com você para verificar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicação autenticadora.** Baixe e use um aplicativo autenticador para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e utilizar a aplicação Microsoft Authenticator, consulte [Configurar informações de segurança para utilizar uma aplicação autenticadora](security-info-setup-auth-app.md).

- **Texto do dispositivo móvel.** Insira o número do seu dispositivo móvel e obtenha um texto de um código que você usará para a verificação em duas etapas ou a redefinição de senha. Para obter instruções passo a passo sobre como verificar a sua identidade com uma mensagem de texto (SMS), consulte Configurar informações de segurança para utilizar mensagens de [texto (SMS)](security-info-setup-text-msg.md).

- **Dispositivo móvel ou chamada telefónica de trabalho.** Insira o número do seu dispositivo móvel e obtenha uma chamada telefônica para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [Configurar informações](security-info-setup-phone-number.md)de segurança para utilizar chamadas telefónicas .

- **Chave de segurança.** Registre sua chave de segurança compatível com a Microsoft e use-a junto com um PIN para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar a sua identidade com uma chave de segurança, consulte [Configurar informações](security-info-setup-security-key.md)de segurança para utilizar uma chave de segurança .

- **Endereço de e-mail.** Insira seu endereço de email corporativo ou de estudante para obter um email para redefinição de senha. Essa opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar o seu e-mail, consulte [Configurar informações](security-info-setup-email.md)de segurança para utilizar o e-mail .

    >[!Note]
    >Se algumas dessas opções estiverem ausentes, isso provavelmente ocorrerá porque sua organização não permite esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Redefinir a sua palavra-passe se a perdeu ou esqueceu, a partir do portal de [reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de [reset do seu trabalho ou palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da conta da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
