---
title: Definir informações de segurança (pré-visualização) para utilizar as perguntas de segurança - Azure Active Directory | Documentos da Microsoft
description: Como configurar as suas informações de segurança para verificar a sua identidade com definido previamente perguntas de segurança.
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
ms.openlocfilehash: 3e5d1546c658631911f25c43e94275f00c7a5140
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901887"
---
# <a name="set-up-security-info-preview-to-use-security-questions"></a>Definir informações de segurança (pré-visualização) para utilizar perguntas de segurança
Pode seguir estes passos para adicionar o que método de repor a palavra-passe. Depois de ter configurado isso pela primeira vez, pode retornar para o **informações de segurança** página para adicionar, atualizar ou eliminar as suas informações de segurança.

Depois de configurar o método de repor a sua palavra-passe, tem também de configurar o método de verificação de dois fatores, usando uma [aplicação de autenticador](security-info-setup-auth-app.md), [mensagens de texto](security-info-setup-text-msg.md), ou uma [chamada telefónica](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Configurar as perguntas de segurança a partir da página de informações de segurança
Dependendo das definições da sua organização, poderá escolher e responder a algumas perguntas de segurança como um dos métodos de informações de segurança. O administrador configura o número de perguntas de segurança que tem de escolher e a responder.

Se utilizar perguntas de segurança, recomendamos que utilize-os em conjunto com outro método. Perguntas de segurança podem ser menos seguras do que outros métodos, porque algumas pessoas podem saber as respostas para suas perguntas.

> [!Note]
> Perguntas de segurança são armazenadas em privado e segura num objeto de utilizador no diretório e apenas podem ser respondidas por si durante o registo. Não é possível para o administrador para ler ou modificar as suas perguntas ou respostas.
> 
> Se não vir a opção de perguntas de segurança, é possível que a sua organização não permite-lhe utilizar perguntas de segurança para a verificação. Se for este o caso, terá de escolher outro método ou contacte o administrador para obter mais ajuda.
> 
> As contas de administrador não têm permissão para utilizar perguntas de segurança como método de reposição de uma palavra-passe. Se estiver conectado como uma conta de nível de administrador que não verá estas opções.

### <a name="to-set-up-your-security-questions"></a>Para configurar as perguntas de segurança

1. Inicie sessão na sua conta escolar ou profissional e, em seguida, aceda ao seu https://myprofile.microsoft.com/ página.

    ![Minha página de perfil, que apresenta ligações de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** no painel de navegação esquerdo ou na ligação presente o **informações de segurança** bloquear e, em seguida, selecione **Adicionar método** do **informações de segurança**  página.

    ![Página de informações de segurança com a opção de método adicionar realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Sobre o **adicione um método** página, selecione **perguntas de segurança** na lista pendente e, em seguida, selecione **Add**.

    ![Adicionar a caixa de método, com perguntas de segurança selecionadas](media/security-info/securityinfo-myprofile-addquestions.png)

4. Sobre o **perguntas de segurança** página, escolha e responder a perguntas de segurança e, em seguida, selecione **guardar**.

    ![Adicione o número de telefone e escolha chamadas telefónicas](media/security-info/securityinfo-myprofile-securityquestions.png)

    As informações de segurança é atualizada e pode utilizar as perguntas de segurança para verificar a sua identidade quando utilizar a reposição de palavra-passe.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Eliminar perguntas de segurança de seus métodos de informações de segurança
Se já não pretender utilizar as perguntas de segurança como um método de informações de segurança, pode removê-los a partir da **informações de segurança** página.

>[!Important]
>Se eliminar as perguntas de segurança por engano, não é possível-lo. Terá que adicionar o método novamente, seguindo os passos a [configurar as perguntas de segurança](#set-up-your-security-questions-from-the-security-info-page) seção deste artigo.

### <a name="to-delete-your-security-questions"></a>Para eliminar as perguntas de segurança

1. Sobre o **informações de segurança** página, selecione a **eliminar** ligação junto ao **perguntas de segurança** opção.

    ![Ligação para eliminar o método de telefone de informações de segurança](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Selecione **Sim** na caixa de confirmação ao eliminar a sua **perguntas de segurança**. Depois das perguntas de segurança são eliminadas, o método é removido do suas informações de segurança e, este desaparece dos **informações de segurança** página.

## <a name="additional-security-info-methods"></a>Métodos de informações de segurança adicionais
Tem opções adicionais para como os contactos de organização para verificar a sua identidade, com base no que é que está tentando fazer. As opções incluem:

- **Aplicação de autenticador.** Transferir e utilizar uma aplicação de autenticação para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para a reposição de palavra-passe ou a verificação em dois passos. Para obter instruções passo a passo sobre como configurar e utilizar a aplicação Microsoft Authenticator, consulte [definir informações de segurança para utilizar uma aplicação de autenticação](security-info-setup-auth-app.md).

- **Texto de dispositivos móveis.** Introduza o seu número de dispositivos móveis e obtenha um código que irá utilizar para verificação de dois passos ou palavra-passe de uma mensagem de reposição. Para obter instruções passo a passo sobre como verificar a sua identidade com uma mensagem de texto (SMS), consulte [definir informações de segurança para utilizar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Dispositivo móvel ou de trabalho de chamadas telefónicas.** Introduza o seu número de dispositivos móveis e obtenha uma chamada telefónica para a reposição de palavra-passe ou a verificação em dois passos. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [definir informações de segurança para utilizar chamadas telefónicas](security-info-setup-phone-number.md).

- **Endereço de e-mail.** Introduza o seu e-mail profissional ou escolar para receber um e-mail para a reposição de palavra-passe. Esta opção não está disponível para verificação de dois passos. Para obter instruções passo a passo sobre como configurar o seu e-mail, consulte [definir informações de segurança para utilizar o e-mail](security-info-setup-email.md).
   
    >[!Note]
    >Se algumas dessas opções estão em falta, é mais provável porque a sua organização não permite que esses métodos. Se for este o caso, terá de escolher um método disponível ou contacte o administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos Seguintes

- Repor a palavra-passe, se tiver perdido ou esquecidas, do [portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/) ou siga os passos a [repor a palavra-passe profissional ou escolar](user-help-reset-password.md) artigo.

- Obter solução de problemas dicas e ajuda para problemas de início de sessão no [não pode iniciar sessão na sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.
