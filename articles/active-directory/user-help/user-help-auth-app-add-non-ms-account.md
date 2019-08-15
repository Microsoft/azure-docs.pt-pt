---
title: Adicione suas contas que não sejam da Microsoft ao Microsoft Authenticator app-Azure Active Directory | Microsoft Docs
description: Como adicionar suas contas que não são da Microsoft, como para o Google, Facebook ou GitHub, ao aplicativo Microsoft Authenticator para verificação de dois fatores.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1521d2b3d47667466636a1ac15f107826e40942
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68942936"
---
# <a name="add-your-non-microsoft-accounts"></a>Adicionar suas contas que não são da Microsoft

Adicione suas contas que não sejam da Microsoft, como para o Google, Facebook ou GitHub ao aplicativo Microsoft Authenticator para verificação de dois fatores. O aplicativo Microsoft Authenticator funciona com qualquer aplicativo que usa a verificação de dois fatores e qualquer conta que dê suporte aos padrões TOTP (senha de uso único) com base no tempo.

>[!Important]
>Para poder adicionar sua conta, você deve baixar e instalar o aplicativo Microsoft Authenticator. Se ainda não tiver feito isso, siga as etapas no artigo [baixar e instalar o aplicativo](user-help-auth-app-download-install.md) .

## <a name="add-personal-accounts"></a>Adicionar contas pessoais

Em geral, para todas as suas contas pessoais, você deve:

1. Entre em sua conta e ative a verificação de dois fatores usando o dispositivo ou o computador.

2. Adicione a conta ao aplicativo Microsoft Authenticator. Você pode ser solicitado a digitalizar um código QR como parte desse processo.

    >[!Note]
    >Se esta for a primeira vez que você estiver configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou permitir que o aplicativo tire fotos e grave vídeo (Android). Você deve selecionar **permitir** para que o aplicativo autenticador possa acessar sua câmera para tirar uma imagem do código QR na próxima etapa. Se você não permitir a câmera, ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, consulte [adicionar manualmente uma conta ao aplicativo](user-help-auth-app-add-account-manual.md).

Estamos fornecendo o processo aqui para suas contas do Facebook, Google, GitHub e Amazon, mas esse processo é o mesmo para qualquer outro aplicativo, como Instagram, Netflix ou Adobe.

## <a name="add-your-google-account"></a>Adicionar sua conta do Google

Adicione sua conta do Google ativando a verificação de dois fatores e, em seguida, adicionando a conta ao aplicativo.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. Em seu computador, vá para https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome, selecione **introdução**e, em seguida, verifique sua identidade.

2. Siga as etapas na página para ativar a verificação em duas etapas para sua conta pessoal do Google.

### <a name="add-your-google-account-to-the-app"></a>Adicionar sua conta do Google ao aplicativo

1. Na página do Google em seu computador, vá para a seção **Configurar a segunda etapa alternativa** , escolha **Configurar** na seção **aplicativo autenticador** .

2. Na página **obter códigos do aplicativo autenticador** , selecione **Android** ou **iPhone** com base em seu tipo de telefone e, em seguida, selecione **Avançar**.

    Você tem um código QR que pode ser usado para associar automaticamente sua conta ao aplicativo Microsoft Authenticator. Não feche esta janela.

3. Abra o aplicativo Microsoft Authenticator, selecione **adicionar conta** no ícone **Personalizar e controlar** no canto superior direito e, em seguida, selecione **outra conta (Google, Facebook, etc.)** .

4. Use a câmera do dispositivo para digitalizar o código QR na página **Configurar autenticador** em seu computador.

    >[!Note]
    >Se a câmera não estiver funcionando corretamente, você poderá inserir o código QR e a URL manualmente.

5. Examine a página **contas** do aplicativo Microsoft Authenticator em seu dispositivo, para verificar se as informações da conta estão corretas e se há um código de verificação de seis dígitos associado.

    Para segurança adicional, o código de verificação é alterado a cada 30 segundos, impedindo que alguém use um código várias vezes.

6. Selecione **Avançar** na página **Configurar autenticador** em seu computador, digite o código de verificação de seis dígitos fornecido no aplicativo para sua conta do Google e, em seguida, selecione **verificar**.

7. Sua conta é verificada e você pode selecionar **concluído** para fechar a página **Configurar autenticador** .

    >[!NOTE]
    >Para obter mais informações sobre verificação de dois fatores e sua conta do Google, consulte [ativar a verificação em duas etapas](https://support.google.com/accounts/answer/185839) e [saber mais sobre a verificação](https://www.google.com/landing/2step/help.html)em duas etapas.

## <a name="add-your-facebook-account"></a>Adicionar sua conta do Facebook

Adicione sua conta do Facebook ativando a verificação de dois fatores e, em seguida, adicionando a conta ao aplicativo.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No computador, abra o Facebook, selecione o menu suspenso no canto superior direito e vá para **configurações** > **segurança e logon**.

    A página **segurança e logon** é exibida.

2. Vá para a opção **usar autenticação de dois fatores** na seção **autenticação de dois fatores** e, em seguida, selecione **Editar**.

    A página **autenticação de dois fatores** é exibida.

3. Selecione **Ativar**.

### <a name="add-your-facebook-account-to-the-app"></a>Adicionar sua conta do Facebook ao aplicativo

1. Na página do Facebook em seu computador, vá para a seção **Adicionar um backup** e, em seguida, escolha **instalação** na área **aplicativo de autenticação** .

    Você tem um código QR que pode ser usado para associar automaticamente sua conta ao aplicativo Microsoft Authenticator. Não feche esta janela.

2. Abra o aplicativo Microsoft Authenticator, selecione **adicionar conta** no ícone **Personalizar e controlar** no canto superior direito e, em seguida, selecione **outra conta (Google, Facebook, etc.)** .

3. Use a câmera do dispositivo para digitalizar o código QR da página de **autenticação de dois fatores** no computador.

    >[!Note]
    >Se a câmera não estiver funcionando corretamente, você poderá inserir o código QR e a URL manualmente.

4. Examine a página **contas** do aplicativo Microsoft Authenticator em seu dispositivo, para verificar se as informações da conta estão corretas e se há um código de verificação de seis dígitos associado.

    Para segurança adicional, o código de verificação é alterado a cada 30 segundos, impedindo que alguém use um código várias vezes.

5. Selecione **Avançar** na página **autenticação de dois fatores** no computador e digite o código de verificação de seis dígitos fornecido no aplicativo para sua conta do Facebook.

    Sua conta é verificada e agora você pode usar o aplicativo para verificar sua conta.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e sua conta do Facebook, consulte [o que é a autenticação de dois fatores e como ela funciona?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>Adicionar sua conta do GitHub

Adicione sua conta do GitHub ativando a verificação de dois fatores e, em seguida, adicionando a conta ao aplicativo.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No seu computador, abra o GitHub, selecione sua imagem no canto superior direito e, em seguida, selecione **configurações**.

    A página **autenticação de dois fatores** é exibida.

2. Selecione **segurança** na barra lateral **configurações pessoais** e, em seguida, selecione **habilitar a autenticação de dois fatores** na área de autenticação de **dois fatores** .

### <a name="add-your-github-account-to-the-app"></a>Adicionar sua conta do GitHub ao aplicativo

1. Na página **autenticação de dois fatores** no computador, selecione **Configurar usando um aplicativo**.

2. Salve seus códigos de recuperação para que você possa voltar à sua conta se perder o acesso e, em seguida, selecione **Avançar**. 

    Você pode salvar seus códigos baixando-os em seu dispositivo, imprimindo uma cópia impressa ou copiando-os em uma ferramenta de Gerenciador de senhas.

3. Na página **autenticação de dois fatores** , selecione **Configurar usando um aplicativo**.

    A página é alterada para mostrar um código QR. Não feche esta página.

4. Abra o aplicativo Microsoft Authenticator, selecione **adicionar conta** no ícone **Personalizar e controlar** no canto superior direito, selecione **outra conta (Google, Facebook, etc.)** e, em seguida, selecione **Inserir este código de texto** do texto na parte superior de a página.

    O aplicativo Microsoft Authenticator não pode verificar o código QR, portanto, você deve inserir manualmente o código.

5. Insira um **nome de conta** (por exemplo, github) e digite a **chave secreta** da etapa 4 e selecione **concluir**.

6. Na página **autenticador de dois fatores** em seu computador, digite o código de verificação de seis dígitos fornecido no aplicativo para sua conta do GitHub e, em seguida, selecione **habilitar**.

    A página **contas** do aplicativo mostra o nome da sua conta e um código de verificação de seis dígitos. Para segurança adicional, o código de verificação é alterado a cada 30 segundos, impedindo que alguém use um código várias vezes.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e sua conta do GitHub, consulte [sobre a autenticação de dois fatores](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Adicionar sua conta do Amazon

Adicione sua conta do Amazon ativando a verificação de dois fatores e, em seguida, adicionando a conta ao aplicativo.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. Em seu computador, abra o Amazon, selecione o menu suspenso **conta & listas** e, em seguida, selecione **sua conta**.

2. Selecione **logon & segurança**, entre em sua conta do Amazon e, em seguida, selecione **Editar** na área **configurações de segurança avançadas** .

    A página **configurações de segurança avançadas** é exibida.

3. Selecione **introdução**.

4. Selecione **aplicativo autenticador** na página **escolher como você receberá códigos** .

    A página é alterada para mostrar um código QR. Não feche esta página.

5. Abra o aplicativo Microsoft Authenticator, selecione **adicionar conta** no ícone **Personalizar e controlar** no canto superior direito e, em seguida, selecione **outra conta (Google, Facebook, etc.)** .

6. Use a câmera do dispositivo para digitalizar o código QR na página **escolher como você receberá códigos** no seu computador.

    >[!Note]
    >Se a câmera não estiver funcionando corretamente, você poderá inserir o código QR e a URL manualmente.

7. Examine a página **contas** do aplicativo Microsoft Authenticator em seu dispositivo, para verificar se as informações da conta estão corretas e se há um código de verificação de seis dígitos associado.

    Para segurança adicional, o código de verificação é alterado a cada 30 segundos, impedindo que alguém use um código várias vezes.

8. Na página **escolher como você receberá códigos** em seu computador, digite o código de verificação de seis dígitos fornecido no aplicativo para sua conta do Amazon e, em seguida, selecione **verificar código e continuar**.

9. Conclua o restante do processo de inscrição, incluindo a adição de um método de verificação de backup, como uma mensagem de texto, e selecione **enviar código**.

10. Na página **Adicionar um método de verificação de backup** em seu computador, digite o código de verificação de seis dígitos fornecido pelo método de verificação de backup para sua conta do Amazon e, em seguida, selecione **verificar código e continuar**.

11. Na página **quase pronto** , decida se deseja tornar seu computador um dispositivo confiável e, em seguida, **selecione o obteve. Ative a verificação**em duas etapas.

    A página **configurações de segurança avançadas** é exibida, mostrando os detalhes de verificação de dois fatores atualizados.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e sua conta do Amazon, consulte [sobre a verificação em duas etapas e como](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) [entrar com a verificação em duas etapas](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).

## <a name="next-steps"></a>Passos Seguintes

- Depois de adicionar suas contas ao aplicativo, você pode entrar usando o aplicativo autenticador em seu dispositivo. Para obter mais informações, consulte [entrar usando o aplicativo](user-help-auth-app-sign-in.md).

- Para dispositivos que executam o iOS, você também pode fazer backup de suas credenciais de conta e configurações de aplicativo relacionadas, como a ordem de suas contas, para a nuvem. Para obter mais informações, consulte [fazer backup e recuperar com Microsoft Authenticator aplicativo](user-help-auth-app-backup-recovery.md).
