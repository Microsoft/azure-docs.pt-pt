---
title: Configurar a verificação em duas etapas-Azure Active Directory | Microsoft Docs
description: Quando sua empresa configurar a autenticação multifator do Azure, você será solicitado a se inscrever para a verificação em duas etapas. Saiba como configurá-lo.
services: active-directory
keywords: como usar o diretório do Azure, Active Directory na nuvem, tutorial do Active Directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75bc067bfe8a98ef2337f368243b3221be1677d6
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949896"
---
# <a name="set-up-my-account-for-two-step-verification"></a>Configurar minha conta para verificação em duas etapas
A verificação em duas etapas é uma etapa de segurança adicional que ajuda a proteger sua conta, tornando mais difícil para outras pessoas invadirem. Se você estiver lendo este artigo, provavelmente receberá um email de seu administrador corporativo ou de estudante sobre a autenticação multifator. Ou talvez você tenha tentado entrar e recebeu uma mensagem solicitando que você configure a verificação de segurança adicional. Se esse for o caso, **você não poderá entrar até que tenha concluído o processo de registro automático**.

Este artigo ajuda você a configurar sua **conta corporativa ou de estudante**. Se você quiser habilitar a verificação em duas etapas para suas próprias conta Microsoft pessoais, consulte [sobre a verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>Configurar a sua conta

Quando o suporte de sua empresa exigir que você comece a usar a verificação em duas etapas, você verá uma tela que diz que **seu administrador exigiu que você configure essa conta para verificação de segurança adicional**:

![Configurar](./media/multi-factor-authentication-end-user-first-time/first.png)

Para começar, selecione **Configurar agora.**

Se você não vir uma tela como esta quando entrar, siga as instruções em [gerenciar suas configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md#using-the-additional-security-verification-page) para localizar a página de configurações onde você pode gerenciar suas opções de verificação.

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Decidir como você deseja verificar suas entradas

A primeira pergunta no processo de registro é como você deseja contatá-lo. Dê uma olhada nas opções na tabela e use os links para ir para as etapas de configuração de cada método.

| Método de contacto | Descrição |
| --- | --- |
| [Aplicativo móvel](#use-a-mobile-app-as-the-contact-method) |- **Receber notificações para verificação.** Essa opção envia uma notificação por push para o aplicativo autenticador em seu smartphone ou Tablet. Exiba a notificação e, se for legítima, selecione **autenticar** no aplicativo. Seu trabalho ou escola pode exigir que você insira um PIN antes de autenticar.<br>- **Use o código de verificação.** Nesse modo, o aplicativo autenticador gera um código de verificação que é atualizado a cada 30 segundos. Insira o código de verificação mais atual na interface de entrada.<br>O aplicativo Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594) e [Ios](https://go.microsoft.com/fwlink/?linkid=866594).|
| [Texto ou chamada de telefone celular](#use-your-mobile-phone-as-the-contact-method) |- **Chamada telefônica** coloca uma chamada de voz automática para o número de telefone que você fornecer. Responda à chamada e pressione # no teclado do telefone para autenticar.<br>- **Mensagem de texto** encerra uma mensagem de texto que contém um código de verificação. Após o prompt no texto, responda à mensagem de texto ou insira o código de verificação fornecido na interface de entrada. |
| [Chamada telefônica do Office](#use-your-office-phone-as-the-contact-method) |Faz uma chamada de voz automática para o número de telefone que você fornece. Responda à chamada e pressione # no teclado do telefone para autenticar. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Usar um aplicativo móvel como o método de contato
O uso desse método requer que você instale um aplicativo autenticador em seu telefone ou Tablet. As etapas neste artigo se baseiam no aplicativo Microsoft Authenticator, que está disponível para [Android](https://go.microsoft.com/fwlink/?Linkid=825072) e [Ios](https://go.microsoft.com/fwlink/?Linkid=825073).

>[!NOTE]
>Você não precisa usar o aplicativo Microsoft Authenticator. Se você já estiver usando outro aplicativo autenticador, poderá continuar a usá-lo.

1. Selecione **aplicativo móvel** na lista suspensa.
2. Selecione **receber notificações para verificação** ou **use o código de verificação**e selecione **Configurar**.

   ![Tela de verificação de segurança adicional](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. Em seu telefone ou Tablet, abra o aplicativo e selecione **+** para adicionar uma conta. (Em dispositivos Android, selecione os três pontos e, em seguida, **adicionar conta**.)
4. Especifique que você deseja adicionar uma conta corporativa ou de estudante. O scanner de código QR em seu telefone é aberto. Se a câmera não estiver funcionando corretamente, você poderá optar por inserir manualmente as informações da empresa. Para obter mais informações, consulte [Adicionar uma conta manualmente](#add-an-account-manually).  
5. Digitalize a imagem de código QR exibida com a tela para configurar o aplicativo móvel.  Selecione **concluído** para fechar a tela de código QR.  

   ![Tela de código QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Quando a ativação for concluída no telefone, selecione **entre em contato comigo**.  Esta etapa envia uma notificação ou um código de verificação para seu telefone. Selecione **verificar**.  
7. Se sua empresa exigir um PIN para aprovar a verificação de entrada, insira-a.

   ![Caixa para inserir um PIN](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. Depois que a entrada do PIN for concluída, selecione **fechar**. Neste ponto, sua verificação deve ser bem-sucedida.
9. Recomendamos que você insira seu número de celular caso você perca o acesso ao seu aplicativo móvel. Especifique seu país/região na lista suspensa e insira seu número de telefone celular na caixa ao lado do nome do país/região. Selecione **Seguinte**.
10. Neste ponto, você será solicitado a configurar senhas de aplicativo para aplicativos sem navegador, como o Outlook 2010 ou mais antigo, ou o aplicativo de email nativo em dispositivos Apple. Isso ocorre porque alguns aplicativos não dão suporte à verificação em duas etapas. Se você não usar esses aplicativos, clique em **concluído** e ignore o restante das etapas.
11. Se você estiver usando esses aplicativos, copie a senha de aplicativo fornecida e cole-a em seu aplicativo em vez de sua senha regular. Você pode usar a mesma senha de aplicativo para vários aplicativos. Para obter mais informações, [ajuda com senhas de aplicativo].
12. Clique em **Concluído**.

### <a name="add-an-account-manually"></a>Adicionar uma conta manualmente
Se você quiser adicionar uma conta ao aplicativo móvel manualmente, em vez de usar o leitor QR, siga estas etapas.

1. Selecione o botão **Inserir conta manualmente** .  
2. Insira o código e a URL que são fornecidos na mesma página que o mostra no código de barras. Essas informações são inseridas nas caixas **código** e **URL** do aplicativo móvel.

    ![Configurar](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Quando a ativação for concluída, selecione **entre em contato comigo**. Esta etapa envia uma notificação ou um código de verificação para seu telefone. Selecione **verificar**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Use seu celular como o método de contato
1. Selecione **telefone de autenticação** na lista suspensa.  

    ![Configurar](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Escolha seu país/região na lista suspensa e insira seu número de telefone celular.
3. Selecione o método que você prefere usar com seu telefone celular-texto ou chamada.
4. Selecione **entre em contato comigo** para verificar seu número de telefone. Dependendo do modo selecionado, enviaremos um texto ou você o chamará. Siga as instruções fornecidas na tela e, em seguida, selecione **verificar**.
5. Neste ponto, você será solicitado a configurar senhas de aplicativo para aplicativos sem navegador, como o Outlook 2010 ou mais antigo, ou o aplicativo de email nativo em dispositivos Apple. Isso ocorre porque alguns aplicativos não dão suporte à verificação em duas etapas. Se você não usar esses aplicativos, clique em **concluído** e ignore o restante das etapas.
6. Se você estiver usando esses aplicativos, copie a senha de aplicativo fornecida e cole-a em seu aplicativo em vez de sua senha regular. Você pode usar a mesma senha de aplicativo para vários aplicativos. Para obter mais informações, [ajuda com senhas de aplicativo].
7. Clique em **Concluído**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Use seu telefone comercial como o método de contato
1. Selecione **telefone comercial** na lista suspensa  

    ![Configurar](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. A caixa número de telefone é preenchida automaticamente com as informações de contato da sua empresa. Se o número estiver incorreto ou ausente, peça ao administrador para fazer alterações.
3. Selecione **entre em contato comigo** para verificar seu número de telefone e chamaremos seu número. Siga as instruções fornecidas na tela e, em seguida, selecione **verificar**.
4. Neste ponto, você será solicitado a configurar senhas de aplicativo para aplicativos sem navegador, como o Outlook 2010 ou mais antigo, ou o aplicativo de email nativo em dispositivos Apple. Isso ocorre porque alguns aplicativos não dão suporte à verificação em duas etapas. Se você não usar esses aplicativos, clique em **concluído** e ignore o restante das etapas.
5. Se você estiver usando esses aplicativos, copie a senha de aplicativo fornecida e cole-a em seu aplicativo em vez de sua senha regular. Você pode usar a mesma senha de aplicativo para vários aplicativos. Para obter mais informações, consulte [o que são senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md).
6. Clique em **Concluído**.

## <a name="next-steps"></a>Passos Seguintes
* Altere suas opções preferenciais e [gerencie suas configurações para verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md)
* Configure [senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md) para aplicativos de dispositivos nativos que não dão suporte à verificação em duas etapas.
* Confira o [aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md) para autenticação rápida e segura mesmo quando você não tiver o serviço de célula.
