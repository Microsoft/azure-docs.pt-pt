---
title: Adicione contas não Microsoft à aplicação Microsoft Authenticator - Azure AD
description: Adicione contas não Microsoft, como para google ou Facebook à aplicação Microsoft Authenticator para verificar a sua identidade enquanto utiliza a verificação de dois fatores.
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
ms.openlocfilehash: da5e2f60334148be1206ce61a9c6397a3acf85e3
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704770"
---
# <a name="add-non-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Adicione contas não Microsoft à aplicação Microsoft Authenticator

Adicione as suas contas não Microsoft, como google, Facebook ou GitHub à aplicação Microsoft Authenticator para verificação de dois fatores. A aplicação Microsoft Authenticator funciona com qualquer aplicação que utilize verificação de dois fatores e qualquer conta que suporte os padrões de senha de uma vez (TOTP) baseados no tempo.

>[!Important]
>Para poder adicionar sua conta, você deve baixar e instalar o aplicativo Microsoft Authenticator. Se ainda não tiver feito isso, siga as etapas no artigo [baixar e instalar o aplicativo](user-help-auth-app-download-install.md) .

## <a name="add-personal-accounts"></a>Adicionar contas pessoais

Geralmente, para todas as suas contas pessoais, deve:

1. Inscreva-se na sua conta e, em seguida, ligue a verificação de dois fatores utilizando o seu dispositivo ou o seu computador.

2. Adicione a conta à aplicação Microsoft Authenticator. Pode ser-lhe pedido que escaneie um código QR como parte deste processo.

    >[!Note]
    >Se esta for a primeira vez que você estiver configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou permitir que o aplicativo tire fotos e grave vídeo (Android). Você deve selecionar **permitir** para que o aplicativo autenticador possa acessar sua câmera para tirar uma imagem do código QR na próxima etapa. Se você não permitir a câmera, ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, consulte [adicionar manualmente uma conta ao aplicativo](user-help-auth-app-add-account-manual.md).

Estamos a fornecer o processo aqui para as suas contas facebook, Google, GitHub e Amazon, mas este processo é o mesmo para qualquer outra aplicação, como o Instagram, Netflix ou Adobe.

## <a name="add-your-google-account"></a>Adicione a sua conta google

Adicione a sua conta google ligando a verificação de dois fatores e, em seguida, adicionando a conta à app.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No seu computador, vá a https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome, selecione **Get Started**e, em seguida, verifique a sua identidade.

2. Siga os passos na página para ativar a verificação em duas etapas para a sua conta pessoal do Google.

### <a name="add-your-google-account-to-the-app"></a>Adicione a sua conta google à aplicação

1. Na página do Google no seu computador, vá à secção alternativa de **segundo passo,** escolha **Configurar** a partir da secção de **aplicações Do Autenticador.**

2. No Código Get da página **de aplicações Authenticator,** selecione **android** ou **iPhone** com base no tipo de telefone e, em seguida, selecione **Next**.

    É-lhe dado um código QR que pode utilizar para associar automaticamente a sua conta à aplicação Microsoft Authenticator. Não feche esta janela.

3. Abra a aplicação Microsoft Authenticator, selecione **Adicionar conta** do ícone Personalizar **e controlar** no canto superior direito e, em seguida, selecione Outra **conta (Google, Facebook, etc.)** .

4. Utilize a câmara do seu dispositivo para digitalizar o código QR a partir da página **'Autenticador' configurar** no computador.

    >[!Note]
    >Se a sua câmara não estiver a funcionar corretamente, pode introduzir manualmente o código QR e o URL.

5. Reveja a página **de Contas** da aplicação Microsoft Authenticator no seu dispositivo, para se certificar de que as informações da sua conta estão corretas e que existe um código de verificação de seis dígitos associado.

    Para segurança adicional, o código de verificação é alterado a cada 30 segundos, impedindo que alguém use um código várias vezes.

6. Selecione **Next** na página **'Configurar Autenticador'** no seu computador, digite o código de verificação de seis dígitos fornecido na aplicação para a sua conta Google e, em seguida, selecione **Verificar**.

7. A sua conta está verificada e pode selecionar **Feito** para fechar a página **'Autenticador' Configurar.**

    >[!NOTE]
    >Para mais informações sobre a verificação de dois fatores e a sua conta Google, consulte [a Verificação 2-Passo](https://support.google.com/accounts/answer/185839) e saiba mais sobre a [verificação de 2 passos](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Adicione a sua conta de Facebook

Adicione a sua conta de Facebook ligando a verificação de dois fatores e, em seguida, adicionando a conta à app.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No seu computador, abra o Facebook, selecione o menu suspenso no canto superior direito e, em seguida, vá para **Definições** > **Segurança e Login**.

    A página **de Segurança e Login** aparece.

2. Desça até à opção de autenticação de **dois fatores use** na secção de **autenticação de dois fatores** e, em seguida, selecione **Editar**.

    A página **de autenticação de dois fatores** aparece.

3. Selecione **Ligar**.

### <a name="add-your-facebook-account-to-the-app"></a>Adicione a sua conta de Facebook à aplicação

1. Na página de Facebook do seu computador, vá à secção **adicionar uma cópia** de segurança e, em seguida, escolha **Configuração** da área da **aplicação De autenticação.**

    É-lhe dado um código QR que pode utilizar para associar automaticamente a sua conta à aplicação Microsoft Authenticator. Não feche esta janela.

2. Abra a aplicação Microsoft Authenticator, selecione **Adicionar conta** do ícone Personalizar **e controlar** no canto superior direito e, em seguida, selecione Outra **conta (Google, Facebook, etc.)** .

3. Utilize a câmara do seu dispositivo para digitalizar o código QR a partir da página de **autenticação de dois fatores** no seu computador.

    >[!Note]
    >Se a sua câmara não estiver a funcionar corretamente, pode introduzir manualmente o código QR e o URL.

4. Reveja a página **de Contas** da aplicação Microsoft Authenticator no seu dispositivo, para se certificar de que as informações da sua conta estão corretas e que existe um código de verificação de seis dígitos associado.

    Para segurança adicional, o código de verificação é alterado a cada 30 segundos, impedindo que alguém use um código várias vezes.

5. Selecione **Next** na página de **autenticação de dois fatores** no seu computador e, em seguida, digite o código de verificação de seis dígitos fornecido na aplicação para a sua conta de Facebook.

    A sua conta está verificada e agora pode usar a app para verificar a sua conta.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e a sua conta de Facebook, consulte o que é a [autenticação de dois fatores e como funciona?](https://www.facebook.com/help/148233965247823)

## <a name="add-your-github-account"></a>Adicione a sua conta GitHub

Adicione a sua conta GitHub ligando a verificação de dois fatores e, em seguida, adicionando a conta à app.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No seu computador, abra o GitHub, selecione a sua imagem do canto superior direito e, em seguida, selecione **Definições**.

    A página **de autenticação de dois fatores** aparece.

2. Selecione **Segurança** a partir da barra lateral de **configurações pessoais** e, em seguida, selecione **Ativar a autenticação de dois fatores** a partir da área **de autenticação de dois fatores.**

### <a name="add-your-github-account-to-the-app"></a>Adicione a sua conta GitHub à app

1. Na página **de autenticação de dois fatores** no seu computador, selecione **Configurar utilizando uma aplicação**.

2. Guarde os seus códigos de recuperação para que possa voltar à sua conta se perder acesso e, em seguida, selecione **Next**. 

    Pode guardar os seus códigos descarregando-os para o seu dispositivo, imprimindo uma cópia em papel ou copiando-os numa ferramenta de gestor de passwords.

3. Na página **de autenticação de dois fatores,** selecione **Configurar utilizando uma aplicação**.

    A página muda para lhe mostrar um código QR. Não feche esta página.

4. Abra a aplicação Microsoft Authenticator, selecione **Adicionar conta** a partir do ícone Personalizar **e controlar** no canto superior direito, selecione Outra conta **(Google, Facebook, etc.)** e, em seguida, selecione **introduzir este código** de texto a partir do texto na parte superior da página.

    A aplicação Microsoft Authenticator não consegue digitalizar o código QR, pelo que tem de introduzir manualmente o código.

5. Introduza um **nome de conta** (por exemplo, GitHub) e escreva a tecla **Segredo** a partir do Passo 4 e, em seguida, selecione **Terminar**.

6. Na página de **autenticador de dois fatores** no seu computador, escreva o código de verificação de seis dígitos fornecido na aplicação para a sua conta GitHub e, em seguida, selecione **Enable**.

    A página **Contas** da aplicação mostra-lhe o seu nome de conta e um código de verificação de seis dígitos. Para segurança adicional, o código de verificação é alterado a cada 30 segundos, impedindo que alguém use um código várias vezes.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e a sua conta GitHub, consulte sobre a [autenticação de dois fatores](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Adicione a sua conta Amazon

Adicione a sua conta Amazon ligando a verificação de dois fatores e, em seguida, adicionando a conta à app.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No seu computador, abra a Amazon, selecione o menu de entrega de **Conta e Listas** e, em seguida, selecione **a Sua Conta**.

2. Selecione **Login e segurança,** inicie sessão na sua conta Amazon e, em seguida, selecione **Editar** na área de Definições de **Segurança Avançada.**

    Aparece a página De **Definições de Segurança Avançada.**

3. Selecione **introdução**.

4. Selecione **App Autenticador** a partir da **página Escolha como receberá códigos.**

    A página muda para lhe mostrar um código QR. Não feche esta página.

5. Abra a aplicação Microsoft Authenticator, selecione **Adicionar conta** do ícone Personalizar **e controlar** no canto superior direito e, em seguida, selecione Outra **conta (Google, Facebook, etc.)** .

6. Utilize a câmara do seu dispositivo para digitalizar o código QR a partir do "Escolha como receberá a página de **códigos"** no seu computador.

    >[!Note]
    >Se a sua câmara não estiver a funcionar corretamente, pode introduzir manualmente o código QR e o URL.

7. Reveja a página **de Contas** da aplicação Microsoft Authenticator no seu dispositivo, para se certificar de que as informações da sua conta estão corretas e que existe um código de verificação de seis dígitos associado.

    Para segurança adicional, o código de verificação é alterado a cada 30 segundos, impedindo que alguém use um código várias vezes.

8. Na **escolha de como receberá a** página de códigos no seu computador, digite o código de verificação de seis dígitos fornecido na aplicação para a sua conta Amazon e, em seguida, selecione **'Verificar código' e continuar**.

9. Complete o resto do processo de inscrição, incluindo a adição de um método de verificação de cópia de segurança, como uma mensagem de texto, e, em seguida, selecione **Enviar código**.

10. Na página adicionar uma página de método de **verificação** de cópia de segurança no seu computador, digite o código de verificação de seis dígitos fornecido pelo seu método de verificação de cópia de segurança para a sua conta Amazon e, em seguida, selecione **'Verificar código' e continuar**.

11. Na página **Quase feita,** decida se faz do computador um dispositivo de confiança e, em seguida, selecione **Tenho-o. Ligue a verificação de dois passos.**

    A página **De definições** de segurança avançada aparece, mostrando os detalhes de verificação de dois fatores atualizados.

    >[!NOTE]
    >Para mais informações sobre a verificação de dois fatores e a sua conta Amazon, consulte [a Verificação de Duas Etapas e inscreva-se](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) com a [Verificação em Duas Etapas](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).

## <a name="next-steps"></a>Passos seguintes

- Depois de adicionar suas contas ao aplicativo, você pode entrar usando o aplicativo autenticador em seu dispositivo. Para obter mais informações, consulte [entrar usando o aplicativo](user-help-auth-app-sign-in.md).

- Para dispositivos que executam o iOS, você também pode fazer backup de suas credenciais de conta e configurações de aplicativo relacionadas, como a ordem de suas contas, para a nuvem. Para obter mais informações, consulte [fazer backup e recuperar com Microsoft Authenticator aplicativo](user-help-auth-app-backup-recovery.md).
