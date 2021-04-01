---
title: Configurar um endereço de e-mail como o seu método de verificação - Azure AD
description: Como configurar a sua página de informações de Segurança (pré-visualização) para verificar a sua identidade usando um endereço de e-mail como método de verificação.
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
ms.openlocfilehash: 4f0ea55149fc8cfeeb6ef719d67346019b81078a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83744472"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>Configurar um endereço de e-mail como o seu método de verificação

Pode seguir estes passos para adicionar o método de reset da sua palavra-passe. Depois de configurar isto pela primeira vez, pode voltar à página **de informações de Segurança** para adicionar, atualizar ou apagar as suas informações de segurança.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Se não vir uma opção de e-mail, é possível que a sua organização não permita que use esta opção para o seu método de reset de palavra-passe. Neste caso, terá de escolher outro método ou contactar o balcão de ajuda da sua organização para obter mais ajuda.

## <a name="security-vs-password-reset-verification"></a>Verificação de redefinição de passwords de segurança vs

Os métodos de informação de segurança são utilizados tanto para a verificação de segurança de dois fatores como para o reset da palavra-passe. No entanto, nem todos os métodos podem ser utilizados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| App autenticador | Verificação de dois fatores e autenticação de redefinição de password. |
| Mensagens de texto | Verificação de dois fatores e autenticação de redefinição de password. |
| Chamadas telefónicas | Verificação de dois fatores e autenticação de redefinição de password. |
| Chave de segurança | Verificação de dois fatores e autenticação de redefinição de password. |
| Conta de e-mail | Apenas a autenticação de redetofinição de palavra-passe. Terá de escolher outro método para a verificação de dois fatores. |
| Perguntas de segurança | Apenas a autenticação de redetofinição de palavra-passe. Terá de escolher outro método para a verificação de dois fatores. |

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Configurar o seu endereço de e-mail a partir da página de informações de Segurança

Dependendo das definições da sua organização, poderá utilizar o seu endereço de e-mail como um dos seus métodos de informação de segurança.

>[!Note]
>Recomendamos a utilização de um endereço de e-mail que não exija o acesso da sua senha de rede. Se não vir a opção de e-mail, é possível que a sua organização não permita que use um e-mail para verificação. Se for esse o caso, terá de escolher outro método ou contactar o seu administrador para obter mais ajuda.

### <a name="to-set-up-your-email-address"></a>Para configurar o seu endereço de e-mail

1. Inscreva-se na sua conta de trabalho ou escola e depois vá para a sua https://myaccount.microsoft.com/ página.

    ![A minha página de perfil, mostrando links de informações de segurança destacados](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações** de segurança a partir do painel de navegação à esquerda ou do link no bloco **de informações de Segurança** e, em seguida, selecione Adicionar **método** a partir da página **de informações de Segurança.**

    ![Página de informações de segurança com opção de método adicionar realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **de método Adicionar um método,** selecione **e-mail** da lista de drop-down e, em seguida, selecione **Adicionar**.

    ![Adicionar caixa de método, com e-mail selecionado](media/security-info/securityinfo-myprofile-addemail.png)

4. Na página **de Email,** digite o seu endereço de e-mail (por exemplo, alain@gmail.com ) e, em seguida, selecione **Seguinte**.

    ![Adicione o número de telefone e escolha chamadas telefónicas](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Este endereço de e-mail não pode ser o seu trabalho ou e-mail escolar.

5. Digite o código enviado para o seu endereço de e-mail especificado e, em seguida, selecione **Seguinte**.

    ![Adicione o número de telefone e escolha mensagens de texto](media/security-info/securityinfo-myprofile-emailcode.png)

    A sua informação de segurança está atualizada e pode utilizar o seu endereço de e-mail para verificar a sua identidade ao utilizar o reset da palavra-passe.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Elimine o seu endereço de e-mail dos seus métodos de informação de segurança

Se já não pretender utilizar o seu endereço de e-mail como método de informação de segurança, pode removê-lo da página de informações de **Segurança.**

>[!Important]
>Se eliminar o seu endereço de e-mail por engano, não há como desfazê-lo. Terá de adicionar o método novamente, seguindo os passos na secção de endereço de [e-mail](#set-up-your-email-address-from-the-security-info-page) deste artigo.

### <a name="to-delete-your-email-address"></a>Para eliminar o seu endereço de e-mail

1. Na página **de informações de Segurança,** selecione o link **Eliminar** ao lado da opção **Email.**

    ![Link para eliminar o método do telefone a partir de informações de segurança](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Selecione **Sim** na caixa de confirmação para eliminar a conta **de e-mail.** Após a conta de e-mail ser eliminada, é removida da sua informação de segurança e desaparece da página de informações de **Segurança.**

## <a name="additional-security-info-methods"></a>Métodos adicionais de informação de segurança

Tem opções adicionais para saber como a sua organização o contacta para verificar a sua identidade, com base no que está a tentar fazer. As opções incluem:

- **Aplicativo de autenticador.** Descarregue e utilize uma aplicação autenticadora para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e utilizar a aplicação Microsoft Authenticator, consulte [configurar as informações de segurança para utilizar uma aplicação autenticadora](security-info-setup-auth-app.md).

- **Texto do dispositivo móvel.** Introduza o número do seu dispositivo móvel e obtenha um texto de um código que utilizará para verificação em duas etapas ou redefinição de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma mensagem de texto (SMS), consulte [Configurar informações de segurança para utilizar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Dispositivo móvel ou chamada telefónica de trabalho.** Introduza o número do seu dispositivo móvel e receba uma chamada telefónica para verificação em duas etapas ou redefinição de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [Configurar informações de segurança para utilizar chamadas telefónicas](security-info-setup-phone-number.md).

- **Chave de segurança.** Registe a sua chave de segurança compatível com a Microsoft e utilize-a juntamente com um PIN para verificação em duas etapas ou redefinição de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma chave de segurança, consulte [Configurar informações de segurança para utilizar uma chave de segurança](security-info-setup-security-key.md).

- **Perguntas de segurança.** Responda a algumas questões de segurança criadas pelo seu administrador para a sua organização. Esta opção só está disponível para reset de palavra-passe e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar as suas questões de segurança, consulte a informação de [segurança Configurar para utilizar](security-info-setup-questions.md) o artigo questões de segurança.

    >[!Note]
    >Se algumas destas opções estão em falta, é mais provável porque a sua organização não permite esses métodos. Se for esse o caso, terá de escolher um método disponível ou contactar o seu administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Reinicie a sua palavra-passe se a tiver perdido ou esquecido, a partir do [portal de reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de Reposição do seu trabalho ou [palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da Conta Da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
