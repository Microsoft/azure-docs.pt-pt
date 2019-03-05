---
title: Configurar informações de segurança (pré-visualização) para utilizar mensagens de texto - Azure Active Directory | Documentos da Microsoft
description: Como configurar as informações de segurança para verificar a sua identidade com mensagens de texto e o seu dispositivo móvel.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea9e4ae21ecc6538b33aed1566c10ddcd22b86c7
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341039"
---
# <a name="set-up-security-info-preview-to-use-text-messaging"></a>Definir informações de segurança (pré-visualização) para utilizar mensagens de texto
Pode seguir estes passos para adicionar a verificação de dois fatores e métodos de reposição de palavra-passe. Depois de ter configurado isso pela primeira vez, pode retornar para o **informações de segurança** página para adicionar, atualizar ou eliminar as suas informações de segurança.

Se lhe for pedido para configurá-la imediatamente depois de iniciar sessão sua conta escolar ou profissional, consulte os passos detalhados no [configurar as suas informações de segurança no prompt de página de início de sessão de](security-info-setup-signin.md) artigo.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Se não vir uma opção para telemóveis, é possível que a sua organização não permite a utilização de um número de telefone para efeitos de verificação. Neste caso, precisará escolher outro método ou contacte o administrador para obter mais ajuda.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Configurar mensagens de texto da página de informações de segurança
Dependendo das definições da sua organização, poderá utilizar como um dos métodos de informações de segurança de mensagens de texto. A opção de mensagem de texto é uma parte da opção para telemóveis, portanto, definirá todas as opções da mesma forma que faria para o número de telefone, mas em vez de ter o Microsoft contactá-lo, irá optar por utilizar uma mensagem de texto.

>[!Note]
>Se pretender receber uma chamada telefónica, em vez de uma mensagem de texto, siga os passos a [definir informações de segurança para utilizar chamadas telefónicas](security-info-setup-phone-number.md) artigo.

### <a name="to-set-up-text-messages"></a>Para configurar mensagens de texto

1. Inicie sessão na sua conta escolar ou profissional e, em seguida, aceda ao seu https://myprofile.microsoft.com/ página.

    ![Minha página de perfil, que apresenta ligações de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** no painel de navegação esquerdo ou na ligação presente o **informações de segurança** bloquear e, em seguida, selecione **Adicionar método** do **informações de segurança**  página.

    ![Página de informações de segurança com a opção de método adicionar realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Sobre o **adicione um método** página, selecione **telefone** na lista pendente e, em seguida, selecione **Add**.

    ![Adicionar caixa do método, com o telefone selecionado](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Sobre o **telefone** página, escreva o número de telefone para o seu dispositivo móvel, escolha **texto-me um código**e, em seguida, selecione **seguinte**.

    ![Adicione o número de telefone e escolha as mensagens de texto](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Escreva o código é enviado a por meio de mensagem de texto para o seu dispositivo móvel e, em seguida, selecione **seguinte**.

    ![Adicione o número de telefone e escolha as mensagens de texto](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    As alterações de página para mostrar o seu sucesso.

    ![Número de notificação de êxito, ligar o telefone, a opção de receber messags de texto e a sua conta](media/security-info/securityinfo-myprofile-phonetext-success.png)

    As informações de segurança é atualizada e pode usar para verificar a sua identidade quando utilizar a reposição de palavra-passe ou a verificação em dois passos de mensagens de texto. Se quiser tornar o seu método de padrão de mensagens de texto, consulte a [alterar o seu método de informações de segurança padrão](#change-your-default-security-info-method) seção deste artigo.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Eliminar a partir de seus métodos de informações de segurança de mensagens de texto
Se já não quiser utilizar mensagens de texto como um método de informações de segurança, pode removê-lo a partir da **informações de segurança** página.

>[!Important]
>Se excluir por engano de mensagens de texto, não é possível-lo. Terá que adicionar o método novamente, seguindo os passos a [configurar mensagens de texto](#set-up-text-messages-from-the-security-info-page) seção deste artigo.

### <a name="to-delete-text-messaging"></a>Eliminar mensagens de texto

1. Sobre o **informações de segurança** página, selecione o **eliminar** ligação junto ao **Phone** opção.

    ![Ligação para eliminar o telefone e o método a partir de informações de segurança de mensagens de texto](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selecione **Sim** na caixa de confirmação para eliminar a **Phone** número. Depois do número de telefone é eliminado, este é removido do suas informações de segurança e, este desaparece dos **informações de segurança** página. Se **Phone** é o método predefinido, a predefinição será alterado para outro método disponível.

## <a name="change-your-default-security-info-method"></a>Alterar o seu método de informações de segurança padrão
Se pretender que o texto de mensagens para ser o método predefinido utilizado quando iniciar sessão na sua conta escolar ou profissional com a verificação de dois fatores ou para pedidos de reposição de palavra-passe, pode defini-lo a partir da **informações de segurança** página.

### <a name="to-change-your-default-security-info-method"></a>Para alterar o método de informações de segurança padrão

1. No **informações de segurança** página, selecione a **alteração** ligação junto aos **predefinido do método de início de sessão** informações.

    ![Alterar ligação para o método de início de sessão predefinido](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selecione **Phone - texto (*_your_phone_number_*)** na lista pendente de métodos disponíveis e, em seguida, selecione **confirmar**.

    ![Escolha o método predefinido para início de sessão](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    O método predefinido utilizado para alterações de início de sessão para **Phone - texto (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Métodos de informações de segurança adicionais
Tem opções adicionais para como os contactos de organização para verificar a sua identidade, com base no que é que está tentando fazer. As opções incluem:

- **Aplicação de autenticador.** Transferir e utilizar uma aplicação de autenticação para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para a reposição de palavra-passe ou a verificação em dois passos. Para obter instruções passo a passo sobre como configurar e utilizar a aplicação Microsoft Authenticator, consulte [definir informações de segurança para utilizar uma aplicação de autenticação](security-info-setup-auth-app.md).

- **Dispositivo móvel ou de trabalho de chamadas telefónicas.** Introduza o seu número de dispositivos móveis e obtenha uma chamada telefónica para a reposição de palavra-passe ou a verificação em dois passos. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [definir informações de segurança para utilizar chamadas telefónicas](security-info-setup-phone-number.md).

- **Endereço de e-mail.** Introduza o seu e-mail profissional ou escolar para receber um e-mail para a reposição de palavra-passe. Esta opção não está disponível para verificação de dois passos. Para obter instruções passo a passo sobre como configurar o seu e-mail, consulte [definir informações de segurança para utilizar o e-mail](security-info-setup-email.md).

- **Perguntas de segurança.** Responder a algumas perguntas de segurança criadas pelo seu administrador para a sua organização. Esta opção só está disponível para a reposição de palavra-passe e não para verificação de dois passos. Para obter instruções passo a passo sobre como configurar a suas perguntas de segurança, consulte a [definir informações de segurança para utilizar perguntas de segurança](security-info-setup-questions.md) artigo.
    
    >[!Note]
    >Se algumas dessas opções estão em falta, é mais provável porque a sua organização não permite que esses métodos. Se for este o caso, terá de escolher um método disponível ou contacte o administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos Seguintes

- Repor a palavra-passe, se tiver perdido ou esquecidas, do [portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/) ou siga os passos a [repor a palavra-passe profissional ou escolar](user-help-reset-password.md) artigo.

- Obter solução de problemas dicas e ajuda para problemas de início de sessão no [não pode iniciar sessão na sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.