---
title: Configurar informações de segurança (pré-visualização) para usar chamadas de telefone - Azure Active Directory | Documentos da Microsoft
description: Como configurar as informações de segurança para verificar a sua identidade através de chamadas de telefone.
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
ms.openlocfilehash: 382a32d0ff9de0a7b372e3b7ff2de7170a791aab
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339254"
---
# <a name="set-up-security-info-preview-to-use-phone-calls"></a>Definir informações de segurança (pré-visualização) para utilizar chamadas telefónicas
Pode seguir estes passos para adicionar a verificação de dois fatores e métodos de reposição de palavra-passe. Depois de ter configurado isso pela primeira vez, pode retornar para o **informações de segurança** página para adicionar, atualizar ou eliminar as suas informações de segurança.

Se lhe for pedido para configurá-la imediatamente depois de iniciar sessão sua conta escolar ou profissional, consulte os passos detalhados no [configurar as suas informações de segurança no prompt de página de início de sessão de](security-info-setup-signin.md) artigo.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Informações de segurança não suportam a utilização de extensões de telefone. Mesmo que adicione o formato correto, 4255551234 + 1 X 12345, as extensões são removidas antes da chamada é feita.

>Se não vir uma opção para telemóveis, é possível que a sua organização não permite a utilização de um número de telefone para efeitos de verificação. Neste caso, precisará escolher outro método ou contacte o administrador para obter mais ajuda.

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Configurar chamadas de telefone da página de informações de segurança
Dependendo das definições da sua organização, poderá usar chamadas de telefone como um dos métodos de informações de segurança.

>[!Note]
>Se pretender receber uma mensagem de texto em vez de uma chamada telefónica, siga os passos a [definir informações de segurança para utilizar mensagens de texto](security-info-setup-text-msg.md) artigo.

### <a name="to-set-up-phone-calls"></a>Para configurar chamadas telefónicas

1. Inicie sessão na sua conta escolar ou profissional e, em seguida, aceda ao seu https://myprofile.microsoft.com/ página.

    ![Minha página de perfil, que apresenta ligações de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** no painel de navegação esquerdo ou na ligação presente o **informações de segurança** bloquear e, em seguida, selecione **Adicionar método** do **informações de segurança**  página.

    ![Página de informações de segurança com a opção de método adicionar realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Sobre o **adicione um método** página, selecione **telefone** na lista pendente e, em seguida, selecione **Add**.

    ![Adicionar caixa do método, com o telefone selecionado](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Sobre o **telefone** página, escreva o número de telefone para o seu dispositivo móvel, escolha **telefonar-me**e, em seguida, selecione **seguinte**.

    ![Adicione o número de telefone e escolha chamadas telefónicas](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Atender a chamada verificação, enviada para o número de telefone que introduziu, e siga as instruções.

    As alterações de página para mostrar o seu sucesso.

    ![Número de notificação de êxito, ligar o telefone, a opção de receber chamadas telefónicas e a sua conta](media/security-info/securityinfo-myprofile-phonetext-success.png)

    As informações de segurança é atualizada e pode usar chamadas de telefone para verificar a sua identidade quando utilizar a reposição de palavra-passe ou a verificação em dois passos. Se pretender efetuar chamadas telefónicas seu método padrão, consulte a [alterar o seu método de informações de segurança padrão](#change-your-default-security-info-method) seção deste artigo.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Eliminar chamadas telefónicas de seus métodos de informações de segurança
Se já não quiser usar chamadas de telefone como um método de informações de segurança, pode removê-lo a partir da **informações de segurança** página.

>[!Important]
>Se excluir por engano chamadas telefónicas, não é possível-lo. Terá que adicionar o método novamente, seguindo os passos a [configurar chamadas telefónicas](#set-up-phone-calls-from-the-security-info-page) seção deste artigo.

### <a name="to-delete-phone-calls"></a>Para eliminar as chamadas telefónicas

1. Sobre o **informações de segurança** página, selecione o **eliminar** ligação junto ao **Phone** opção.

    ![Ligação para eliminar o método de telefone de informações de segurança](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selecione **Sim** na caixa de confirmação para eliminar a **Phone** número. Depois do número de telefone é eliminado, este é removido do suas informações de segurança e, este desaparece dos **informações de segurança** página. Se **Phone** é o método predefinido, a predefinição será alterado para outro método disponível.
    
## <a name="change-your-default-security-info-method"></a>Alterar o seu método de informações de segurança padrão
Se pretender que as chamadas de telefone para ser o método predefinido utilizado quando iniciar sessão na sua conta escolar ou profissional com a verificação de dois fatores ou para pedidos de reposição de palavra-passe, pode defini-lo a partir da **informações de segurança** página.

### <a name="to-change-your-default-security-info-method"></a>Para alterar o método de informações de segurança padrão

1. No **informações de segurança** página, selecione a **alteração** ligação junto aos **predefinido do método de início de sessão** informações.

    ![Alterar ligação para o método de início de sessão predefinido](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selecione **Phone - chamada (*_your_phone_number_*)** na lista pendente de métodos disponíveis e, em seguida, selecione **confirmar**.

    ![Escolha o método predefinido para início de sessão](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    O método predefinido utilizado para alterações de início de sessão para **Phone - chamada (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Métodos de informações de segurança adicionais
Tem opções adicionais para como os contactos de organização para verificar a sua identidade, com base no que é que está tentando fazer. As opções incluem:

- **Aplicação de autenticador.** Transferir e utilizar uma aplicação de autenticação para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para a reposição de palavra-passe ou a verificação em dois passos. Para obter instruções passo a passo sobre como configurar e utilizar a aplicação Microsoft Authenticator, consulte [definir informações de segurança para utilizar uma aplicação de autenticação](security-info-setup-auth-app.md).

- **Texto de dispositivos móveis.** Introduza o seu número de dispositivos móveis e obtenha um código que irá utilizar para verificação de dois passos ou palavra-passe de uma mensagem de reposição. Para obter instruções passo a passo sobre como verificar a sua identidade com uma mensagem de texto (SMS), consulte [definir informações de segurança para utilizar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Endereço de e-mail.** Introduza o seu e-mail profissional ou escolar para receber um e-mail para a reposição de palavra-passe. Esta opção não está disponível para verificação de dois passos. Para obter instruções passo a passo sobre como configurar o seu e-mail, consulte [definir informações de segurança para utilizar o e-mail](security-info-setup-email.md).

- **Perguntas de segurança.** Responder a algumas perguntas de segurança criadas pelo seu administrador para a sua organização. Esta opção só está disponível para a reposição de palavra-passe e não para verificação de dois passos. Para obter instruções passo a passo sobre como configurar a suas perguntas de segurança, consulte a [definir informações de segurança para utilizar perguntas de segurança](security-info-setup-questions.md) artigo.
    
    >[!Note]
    >Se algumas dessas opções estão em falta, é mais provável porque a sua organização não permite que esses métodos. Se for este o caso, terá de escolher um método disponível ou contacte o administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos Seguintes

- Repor a palavra-passe, se tiver perdido ou esquecidas, do [portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/) ou siga os passos a [repor a palavra-passe profissional ou escolar](user-help-reset-password.md) artigo.

- Obter solução de problemas dicas e ajuda para problemas de início de sessão no [não pode iniciar sessão na sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.