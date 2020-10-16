---
title: Confise o seu número de telefone como o seu método de verificação - Azure AD
description: Como configurar a sua página de informações de Segurança (pré-visualização) para verificar a sua identidade usando o seu número de telefone e dispositivo móvel como método de verificação.
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
ms.openlocfilehash: 7500b6b146a627dcdebacd9d2e2c7c61bc43c105
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83744457"
---
# <a name="set-up-a-phone-number-as-your-verification-method"></a>Configurar um número de telefone como o seu método de verificação

Pode seguir estes passos para adicionar os seus métodos de verificação de dois fatores e de reset de palavra-passe. Depois de configurar isto pela primeira vez, pode voltar à página **de informações de Segurança** para adicionar, atualizar ou apagar as suas informações de segurança.

Se for solicitado que o mesmo o faça imediatamente após iniciar sôms na sua conta de trabalho ou na escola, consulte os passos detalhados na Configuração da sua informação de segurança a partir do artigo [de pedido de inscrição na página.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> As informações de segurança não suportam a utilização de extensões telefónicas. Mesmo que adicione o formato adequado, +1 4255551234X12345, as extensões são removidas antes da chamada ser feita.
>
> Se não vir uma opção de telefone, é possível que a sua organização não permita que use esta opção para verificação. Neste caso, terá de escolher outro método ou contactar o balcão de ajuda da sua organização para obter mais assistência.

## <a name="security-verification-versus-password-reset-authentication"></a>Verificação de segurança versus autenticação de reset de palavra-passe

Os métodos de informação de segurança são utilizados tanto para a verificação de segurança de dois fatores como para o reset da palavra-passe. No entanto, nem todos os métodos podem ser utilizados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| App autenticador | Verificação de dois fatores e autenticação de redefinição de password. |
| Mensagens de texto | Verificação de dois fatores e autenticação de redefinição de password. |
| Chamadas telefónicas | Verificação de dois fatores e autenticação de redefinição de password. |
| Chave de segurança | Verificação de dois fatores e autenticação de redefinição de password. |
| Conta de e-mail | Apenas a autenticação de redetofinição de palavra-passe. Terá de escolher outro método para a verificação de dois fatores. |
| Perguntas de segurança | Apenas a autenticação de redetofinição de palavra-passe. Terá de escolher outro método para a verificação de dois fatores. |

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Configurar chamadas telefónicas a partir da página de informações de Segurança

Dependendo das configurações da sua organização, poderá utilizar chamadas telefónicas como um dos seus métodos de informação de segurança.

>[!Note]
>Se quiser receber uma mensagem de texto em vez de uma chamada telefónica, siga os passos na [informação de segurança Configurar para usar](security-info-setup-text-msg.md) o artigo de mensagens de texto.

### <a name="to-set-up-phone-calls"></a>Para configurar chamadas telefónicas

1. Inscreva-se na sua conta de trabalho ou escola e depois vá para a sua https://myaccount.microsoft.com/ página.

    ![A minha página de perfil, mostrando links de informações de segurança destacados](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações** de segurança a partir do painel de navegação à esquerda ou do link no bloco **de informações de Segurança** e, em seguida, selecione Adicionar **método** a partir da página **de informações de Segurança.**

    ![Página de informações de segurança com opção de método adicionar realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página de método Adicionar uma página de **método,** selecione **Telefone** da lista de drop-down e, em seguida, **selecione Adicionar**.

    ![Adicionar caixa de métodos, com telefone selecionado](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na página **'Telefone',** digite o número de telefone do seu dispositivo móvel, escolha **Ligue-me**e, em seguida, selecione **Seguinte**.

    ![Adicione o número de telefone e escolha chamadas telefónicas](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Atende a chamada telefónica de verificação, enviada para o número de telefone que inseriu, e siga as instruções.

    A página muda para mostrar o seu sucesso.

    ![Notificação de sucesso, ligação do número de telefone, escolha de receber chamadas telefónicas e sua conta](media/security-info/securityinfo-myprofile-phonetext-success.png)

    A sua informação de segurança é atualizada e pode utilizar chamadas telefónicas para verificar a sua identidade quando utilizar a verificação em duas etapas ou o reset da palavra-passe. Se pretender estoque as chamadas telefónicas o seu método padrão, consulte a secção de método de informação de [segurança predefinido](#change-your-default-security-info-method) deste artigo.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Elimine as chamadas telefónicas dos seus métodos de informação de segurança

Se já não pretender utilizar chamadas telefónicas como método de informação de segurança, pode removê-lo da página de informações de **Segurança.**

>[!Important]
>Se apagares as chamadas por engano, não há como desfazê-lo. Terá de voltar a adicionar o método, seguindo os passos na secção de [chamadas telefónicas](#set-up-phone-calls-from-the-security-info-page) deste artigo.

### <a name="to-delete-phone-calls"></a>Para apagar chamadas telefónicas

1. Na página **de informações de Segurança,** selecione o link **Eliminar** ao lado da opção **Telefone.**

    ![Link para eliminar o método do telefone a partir de informações de segurança](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selecione **Sim** na caixa de confirmação para eliminar o número **de telefone.** Depois de o seu número de telefone ser apagado, é removido da sua informação de segurança e desaparece da página de informações de **Segurança.** Se **o Telefone** for o seu método predefinido, o padrão mudará para outro método disponível.

## <a name="change-your-default-security-info-method"></a>Altere o seu método de informação de segurança padrão

Se pretender que as chamadas telefónicas sejam o método predefinido utilizado quando iniciar sing-in na sua conta de trabalho ou escola utilizando a verificação de dois fatores ou para pedidos de reset de palavra-passe, pode descrevê-la a partir da página **de informações de Segurança.**

### <a name="to-change-your-default-security-info-method"></a>Para alterar o seu método de informação de segurança padrão

1. Na página **de informações de Segurança,** selecione o link **Alterar** ao lado das informações **do método de inscrição predefinido.**

    ![Alterar link para o método de inscrição predefinido](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selecione **Telefone - ligue*_(your_phone_number_*)** da lista de métodos disponíveis e, em seguida, selecione **Confirmar**.

    ![Escolha o método para o padrão de inscrição](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    O método predefinido utilizado para alterações de inscrição no **Telefone - chamada*_(your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Métodos adicionais de informação de segurança

Tem opções adicionais para saber como a sua organização o contacta para verificar a sua identidade, com base no que está a tentar fazer. As opções incluem:

- **Aplicativo de autenticador.** Descarregue e utilize uma aplicação autenticadora para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e utilizar a aplicação Microsoft Authenticator, consulte [configurar as informações de segurança para utilizar uma aplicação autenticadora](security-info-setup-auth-app.md).

- **Texto do dispositivo móvel.** Introduza o número do seu dispositivo móvel e obtenha um texto de um código que utilizará para verificação em duas etapas ou redefinição de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma mensagem de texto (SMS), consulte [Configurar informações de segurança para utilizar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Chave de segurança.** Registe a sua chave de segurança compatível com a Microsoft e utilize-a juntamente com um PIN para verificação em duas etapas ou redefinição de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma chave de segurança, consulte [Configurar informações de segurança para utilizar uma chave de segurança](security-info-setup-security-key.md).

- **Endereço de e-mail.** Insira o seu endereço de e-mail de trabalho ou escola para obter um e-mail para reset de senha. Esta opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar o seu e-mail, consulte [Configurar informações de segurança para utilizar o e-mail](security-info-setup-email.md).

- **Perguntas de segurança.** Responda a algumas questões de segurança criadas pelo seu administrador para a sua organização. Esta opção só está disponível para reset de palavra-passe e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar as suas questões de segurança, consulte a informação de [segurança Configurar para utilizar](security-info-setup-questions.md) o artigo questões de segurança.

    >[!Note]
    >Se algumas destas opções estão em falta, é mais provável porque a sua organização não permite esses métodos. Se for esse o caso, terá de escolher um método disponível ou contactar o seu administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Reinicie a sua palavra-passe se a tiver perdido ou esquecido, a partir do [portal de reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de Reposição do seu trabalho ou [palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da Conta Da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
