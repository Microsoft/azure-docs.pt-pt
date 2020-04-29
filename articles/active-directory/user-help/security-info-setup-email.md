---
title: Configurar um endereço de e-mail como o seu método de verificação - Azure AD
description: Como configurar a sua página de Informações de Segurança (pré-visualização) para verificar a sua identidade usando um endereço de e-mail como método de verificação.
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
ms.openlocfilehash: 9488b96dda15b0ffb794f83826dc299a5f397b77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77064007"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>Configurar um endereço de e-mail como o seu método de verificação

Pode seguir estes passos para adicionar o método de reset da palavra-passe. Depois de configurar isto pela primeira vez, pode voltar à página de **informações** de Segurança para adicionar, atualizar ou apagar as suas informações de segurança.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Se não vir uma opção de e-mail, é possível que a sua organização não lhe permita utilizar esta opção para o seu método de reset de palavra-passe. Neste caso, terá de escolher outro método ou contactar o balcão de ajuda da sua organização para obter mais ajuda.

## <a name="security-vs-password-reset-verification"></a>Verificação de segurança vs redefinição de palavra-passe

Os métodos de informação de segurança são utilizados tanto para a verificação de segurança de dois fatores como para o reset de palavras-passe. No entanto, nem todos os métodos podem ser utilizados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| Aplicação de autenticação | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Mensagens de texto | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Telefonemas | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Chave de segurança | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Conta de e-mail | Apenas a autenticação de reset da palavra-passe. Terá de escolher outro método para verificação de dois fatores. |
| Perguntas de segurança | Apenas a autenticação de reset da palavra-passe. Terá de escolher outro método para verificação de dois fatores. |

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Configurar o seu endereço de e-mail a partir da página de informações de Segurança

Dependendo das definições da sua organização, poderá utilizar o seu endereço de e-mail como um dos seus métodos de informação de segurança.

>[!Note]
>Recomendamos a utilização de um endereço de e-mail que não exija o acesso da sua senha de rede. Se não vir a opção de e-mail, é possível que a sua organização não lhe permita usar um e-mail para verificação. Se for esse o caso, terá de escolher outro método ou contactar o seu administrador para obter mais ajuda.

### <a name="to-set-up-your-email-address"></a>Para configurar o seu endereço de e-mail

1. Inscreva-se na sua conta de trabalho https://myprofile.microsoft.com/ ou escola e depois vá para a sua página.

    ![A minha página de Perfil, mostrando links de informação de segurança destacados](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** a partir do painel de navegação esquerdo ou do link no bloco de **informações** de Segurança e, em seguida, selecione **adicionar método** a partir da página de informações de **Segurança.**

    ![Página de informações de segurança com opção de método de adição realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar uma página de método,** selecione **Email** da lista de drop-down e, em seguida, selecione **Adicionar**.

    ![Adicionar caixa de método, com e-mail selecionado](media/security-info/securityinfo-myprofile-addemail.png)

4. Na página **de E-mail,** digite alain@gmail.como seu endereço de e-mail (por exemplo, e depois selecione **Seguinte**.

    ![Adicione o número de telefone e escolha chamadas telefónicas](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Este email não pode ser o seu trabalho ou e-mail escolar.

5. Digite o código enviado para o seu endereço de e-mail especificado e, em seguida, selecione **Next**.

    ![Adicione o número de telefone e escolha mensagens de texto](media/security-info/securityinfo-myprofile-emailcode.png)

    A sua informação de segurança é atualizada e pode utilizar o seu endereço de e-mail para verificar a sua identidade ao utilizar o reset da palavra-passe.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Elimine o seu endereço de e-mail dos seus métodos de informação de segurança

Se já não quiser utilizar o seu endereço de e-mail como um método de informação de segurança, pode removê-lo da página de informações de **Segurança.**

>[!Important]
>Se apagar o seu endereço de e-mail por engano, não há como desfazê-lo. Terá de adicionar o método novamente, seguindo os passos na secção de [endereço de e-mail](#set-up-your-email-address-from-the-security-info-page) deste artigo.

### <a name="to-delete-your-email-address"></a>Para eliminar o seu endereço de e-mail

1. Na página de informações de **Segurança,** selecione o link **Eliminar** ao lado da opção **Email.**

    ![Link para eliminar o método do telefone a partir de informações de segurança](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Selecione **Sim** a partir da caixa de confirmação para eliminar a conta **de e-mail.** Após a eliminação da conta de e-mail, é removida das suas informações de segurança e desaparece da página de informações de **Segurança.**

## <a name="additional-security-info-methods"></a>Métodos adicionais de informação de segurança

Tem opções adicionais para como a sua organização o contacta para verificar a sua identidade, com base no que está a tentar fazer. As opções incluem:

- **Aplicação autenticadora.** Descarregue e use uma aplicação autenticadora para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como configurar e utilizar a aplicação Microsoft Authenticator, consulte [Configurar informações de segurança para utilizar uma aplicação autenticadora](security-info-setup-auth-app.md).

- **Texto do dispositivo móvel.** Insira o número do seu dispositivo móvel e obtenha um código de texto que utilizará para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma mensagem de texto (SMS), consulte Configurar informações de segurança para utilizar mensagens de [texto (SMS)](security-info-setup-text-msg.md).

- **Dispositivo móvel ou chamada telefónica de trabalho.** Insira o número do seu dispositivo móvel e obtenha uma chamada telefónica para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [Configurar informações](security-info-setup-phone-number.md)de segurança para utilizar chamadas telefónicas .

- **Chave de segurança.** Registe a sua chave de segurança compatível com a Microsoft e utilize-a juntamente com um PIN para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma chave de segurança, consulte [Configurar informações](security-info-setup-security-key.md)de segurança para utilizar uma chave de segurança .

- **Questões de segurança.** Responda a algumas questões de segurança criadas pelo seu administrador para a sua organização. Esta opção só está disponível para reset de palavra-passe e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar as suas questões de segurança, consulte a informação de [segurança configurar para utilizar](security-info-setup-questions.md) o artigo de questões de segurança.

    >[!Note]
    >Se algumas destas opções estão em falta, é muito provável porque a sua organização não permite esses métodos. Se for esse o caso, terá de escolher um método disponível ou contactar o seu administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Redefinir a sua palavra-passe se a perdeu ou esqueceu, a partir do portal de [reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de [reset do seu trabalho ou palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da conta da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
