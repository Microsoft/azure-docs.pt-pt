---
title: Definir informações de segurança (pré-visualização) para utilizar o seu endereço de e-mail - Azure Active Directory | Documentos da Microsoft
description: Como configurar as informações de segurança para verificar a sua identidade com o seu endereço de e-mail.
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
ms.openlocfilehash: 720aafac79a67f64b0974dba0dd60c6aa24a8c54
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342076"
---
# <a name="set-up-security-info-preview-to-use-your-email-address"></a>Definir informações de segurança (pré-visualização) para utilizar o seu endereço de e-mail
Pode seguir estes passos para adicionar o que método de repor a palavra-passe. Depois de ter configurado isso pela primeira vez, pode retornar para o **informações de segurança** página para adicionar, atualizar ou eliminar as suas informações de segurança.

Depois de configurar o método de repor a sua palavra-passe, tem também de configurar o método de verificação de dois fatores, usando uma [aplicação de autenticador](security-info-setup-auth-app.md), [mensagens de texto](security-info-setup-text-msg.md), ou uma [chamada telefónica](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Configurar o seu endereço de e-mail da página de informações de segurança
Dependendo das definições da sua organização, poderá utilizar o seu endereço de e-mail como um dos métodos de informações de segurança.

>[!Note]
>Recomendamos que utilize um endereço de e-mail que não requer a sua palavra-passe de rede para aceder. Se não vir a opção de e-mail, é possível que a sua organização não permite a utilização de uma mensagem de e-mail para a verificação. Se for este o caso, terá de escolher outro método ou contacte o administrador para obter mais ajuda.

### <a name="to-set-up-your-email-address"></a>Para configurar o seu endereço de e-mail

1. Inicie sessão na sua conta escolar ou profissional e, em seguida, aceda ao seu https://myprofile.microsoft.com/ página.

    ![Minha página de perfil, que apresenta ligações de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** no painel de navegação esquerdo ou na ligação presente o **informações de segurança** bloquear e, em seguida, selecione **Adicionar método** do **informações de segurança**  página.

    ![Página de informações de segurança com a opção de método adicionar realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Sobre o **adicione um método** página, selecione **E-Mail** na lista pendente e, em seguida, selecione **Add**.

    ![Adicionar a caixa de método, com o e-mail selecionado](media/security-info/securityinfo-myprofile-addemail.png)

4. Sobre o **E-Mail** página, escreva o seu endereço de e-mail (por exemplo, alain@gmail.com) e, em seguida, selecione **seguinte**.

    ![Adicione o número de telefone e escolha chamadas telefónicas](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Este endereço de e-mail não pode ser o seu e-mail escolar ou profissional.

5. Escreva o código que enviámos para o seu endereço de e-mail especificado e, em seguida, selecione **seguinte**.

    ![Adicione o número de telefone e escolha as mensagens de texto](media/security-info/securityinfo-myprofile-emailcode.png)

    As informações de segurança é atualizada e pode utilizar o seu endereço de e-mail para verificar a sua identidade quando utilizar a reposição de palavra-passe.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Eliminar o seu endereço de e-mail de seus métodos de informações de segurança
Se já não quiser utilizar o seu endereço de e-mail como um método de informações de segurança, pode removê-lo a partir da **informações de segurança** página.

>[!Important]
>Se eliminar o seu endereço de e-mail por engano, não é possível-lo. Terá que adicionar o método novamente, seguindo os passos a [configurar o seu endereço de e-mail](#set-up-your-email-address-from-the-security-info-page) seção deste artigo.

### <a name="to-delete-your-email-address"></a>Para eliminar o seu endereço de e-mail

1. Sobre o **informações de segurança** página, selecione a **eliminar** ligação junto ao **E-Mail** opção.

    ![Ligação para eliminar o método de telefone de informações de segurança](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Selecione **Sim** na caixa de confirmação para eliminar a **E-Mail** conta. Depois da conta de e-mail é eliminada, este é removido do suas informações de segurança e, este desaparece dos **informações de segurança** página.

## <a name="additional-security-info-methods"></a>Métodos de informações de segurança adicionais
Tem opções adicionais para como os contactos de organização para verificar a sua identidade, com base no que é que está tentando fazer. As opções incluem:

- **Aplicação de autenticador.** Transferir e utilizar uma aplicação de autenticação para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para a reposição de palavra-passe ou a verificação em dois passos. Para obter instruções passo a passo sobre como configurar e utilizar a aplicação Microsoft Authenticator, consulte [definir informações de segurança para utilizar uma aplicação de autenticação](security-info-setup-auth-app.md).

- **Texto de dispositivos móveis.** Introduza o seu número de dispositivos móveis e obtenha um código que irá utilizar para verificação de dois passos ou palavra-passe de uma mensagem de reposição. Para obter instruções passo a passo sobre como verificar a sua identidade com uma mensagem de texto (SMS), consulte [definir informações de segurança para utilizar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Dispositivo móvel ou de trabalho de chamadas telefónicas.** Introduza o seu número de dispositivos móveis e obtenha uma chamada telefónica para a reposição de palavra-passe ou a verificação em dois passos. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [definir informações de segurança para utilizar chamadas telefónicas](security-info-setup-phone-number.md).

- **Perguntas de segurança.** Responder a algumas perguntas de segurança criadas pelo seu administrador para a sua organização. Esta opção só está disponível para a reposição de palavra-passe e não para verificação de dois passos. Para obter instruções passo a passo sobre como configurar a suas perguntas de segurança, consulte a [definir informações de segurança para utilizar perguntas de segurança](security-info-setup-questions.md) artigo.
    
    >[!Note]
    >Se algumas dessas opções estão em falta, é mais provável porque a sua organização não permite que esses métodos. Se for este o caso, terá de escolher um método disponível ou contacte o administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos Seguintes

- Repor a palavra-passe, se tiver perdido ou esquecidas, do [portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/) ou siga os passos a [repor a palavra-passe profissional ou escolar](user-help-reset-password.md) artigo.

- Obter solução de problemas dicas e ajuda para problemas de início de sessão no [não pode iniciar sessão na sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.
