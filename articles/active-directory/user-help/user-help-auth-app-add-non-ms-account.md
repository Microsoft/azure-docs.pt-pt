---
title: Adicionar contas não Microsoft à aplicação Microsoft Authenticator - Azure AD
description: Adicione contas não Microsoft, como por exemplo para o Google ou Facebook à aplicação Microsoft Authenticator para verificar a sua identidade enquanto utiliza a verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/02/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 21c8e75ac81a443b1dd9d4a0f43263bbf40bee88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359205"
---
# <a name="add-non-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Adicionar contas não Microsoft à aplicação Microsoft Authenticator

Se tiver contas não Microsoft, como para Google, Facebook ou GitHub, pode adicioná-las à aplicação Microsoft Authenticator para verificação de dois fatores. A aplicação Microsoft Authenticator funciona com qualquer aplicação que utilize verificação de dois fatores e qualquer conta que suporte os padrões de senha única (TOTP) baseados no tempo.

>[!Important]
>Antes de poder adicionar a sua conta, tem de descarregar e instalar a aplicação Microsoft Authenticator. Se ainda não o fez, siga os passos no Download e instale o artigo [da aplicação.](user-help-auth-app-download-install.md)

## <a name="add-personal-accounts"></a>Adicionar contas pessoais

Geralmente, para todas as suas contas pessoais, deve:

1. Inscreva-se na sua conta e, em seguida, ligue a verificação de dois fatores utilizando o seu dispositivo ou o seu computador.

2. Adicione a conta à aplicação Microsoft Authenticator. Pode ser-lhe pedido que digitalize um código QR como parte deste processo.

    >[!Note]
    >Se esta for a primeira vez que está a configurar a aplicação Microsoft Authenticator, poderá receber uma solicitação a perguntar se permite que a app aceda à sua câmara (iOS) ou se permite que a app tire fotografias e grave vídeos (Android). Tem de selecionar **Permitir** que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no passo seguinte. Se não permitir a câmara, ainda pode configurar a aplicação autenticadora, mas terá de adicionar manualmente a informação de código. Para obter informações sobre como adicionar o código manualmente, consulte [manualmente adicionar uma conta à aplicação.](user-help-auth-app-add-account-manual.md)

Estamos a fornecer o processo para as suas contas de Facebook, Google, GitHub e Amazon, mas o processo é o mesmo para outras apps, como o Instagram e a Adobe.

## <a name="add-your-google-account"></a>Adicione a sua conta Google

Adicione a sua conta Google ligando a verificação de dois fatores e adicionando a conta à app.

### <a name="turn-on-two-factor-verification"></a>Ligue a verificação de dois fatores

1. No seu computador, vá https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome para, **selecione Get Started**, e, em seguida, verifique a sua identidade.

2. Siga os passos na página para ligar a verificação em duas etapas para a sua conta pessoal do Google.

### <a name="add-your-google-account-to-the-app"></a>Adicione a sua conta Google à aplicação

1. Na página de segurança da conta google no seu computador https://myaccount.google.com/security) (, vá ao **Add mais segundos passos para verificar se é a** sua secção, escolha **Configurar a** partir da secção de **aplicações Authenticator.**

2. Na página **de aplicações "Obter" da página de aplicações Authenticator,** selecione **Android** ou **iPhone** com base no seu tipo de telefone e, em seguida, selecione **Next**.

    É-lhe dado um código QR que pode utilizar para associar automaticamente a sua conta à aplicação Microsoft Authenticator. Não feche esta janela.

3. Abra a aplicação Microsoft Authenticator, **selecione Adicionar a conta** a partir do ícone De **personalizar e controlar** no canto superior direito e, em seguida, selecione Outra conta **(Google, Facebook, etc.)**.

4. Utilize a câmara do seu dispositivo para digitalizar o código QR da página **"Configurar autenticador"** no seu computador.

    >[!Note]
    >Se a sua câmara não estiver a funcionar corretamente, pode introduzir manualmente o código QR e o URL.

5. Reveja a página **contas** da aplicação Microsoft Authenticator no seu dispositivo, para se certificar de que as informações da sua conta estão corretas e que existe um código de verificação de seis dígitos associado.

    Para uma segurança adicional, o código de verificação muda a cada 30 segundos impedindo alguém de usar um código várias vezes.

6. Selecione **seguinte** na página **de Autenticador configurado** no seu computador, digite o código de verificação de seis dígitos fornecido na aplicação para a sua conta Google e, em seguida, selecione **Verificar**.

7. A sua conta é verificada e pode selecionar **'Fazer'** para fechar a página **'Configurar autenticador'.**

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e a sua conta Google, consulte [Turn on 2-Step Verification](https://support.google.com/accounts/answer/185839) e Saiba mais sobre a [Verificação de 2 Etapas](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Adicione a sua conta de Facebook

Adicione a sua conta de Facebook ligando a verificação de dois fatores e adicionando a conta à app.

### <a name="turn-on-two-factor-verification"></a>Ligue a verificação de dois fatores

1. No seu computador, abra o Facebook, selecione o menu suspenso no canto superior direito e, em seguida, vá para **Definições**  >  **Segurança e Login**.

    Aparece a página **de Segurança e Início de Sessão.**

2. Desça para a opção **de autenticação de dois fatores** na secção **de autenticação de dois fatores** e, em seguida, selecione **Editar**.

    Aparece a página **de autenticação de dois fatores.**

3. Selecione **Ligar**.

### <a name="add-your-facebook-account-to-the-app"></a>Adicione a sua conta de Facebook à aplicação

1. Na página de Facebook do seu computador, vá à secção **Adicionar uma cópia de segurança** e, em seguida, escolha **Configuração** na área da **aplicação autenticação.**

    É-lhe dado um código QR que pode utilizar para associar automaticamente a sua conta à aplicação Microsoft Authenticator. Não feche esta janela.

2. Abra a aplicação Microsoft Authenticator, **selecione Adicionar a conta** a partir do ícone De **personalizar e controlar** no canto superior direito e, em seguida, selecione Outra conta **(Google, Facebook, etc.)**.

3. Utilize a câmara do seu dispositivo para digitalizar o código QR a partir da página de **autenticação de dois fatores** no seu computador.

    >[!Note]
    >Se a sua câmara não estiver a funcionar corretamente, pode introduzir manualmente o código QR e o URL.

4. Reveja a página **contas** da aplicação Microsoft Authenticator no seu dispositivo, para se certificar de que as informações da sua conta estão corretas e que existe um código de verificação de seis dígitos associado.

    Para uma segurança adicional, o código de verificação muda a cada 30 segundos impedindo alguém de usar um código várias vezes.

5. Selecione **Seguinte** na página **de autenticação de dois fatores** no seu computador e, em seguida, digite o código de verificação de seis dígitos fornecido na aplicação para a sua conta do Facebook.

    A sua conta é verificada e agora pode utilizar a app para verificar a sua conta.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e a sua conta de Facebook, veja [o que é a autenticação de dois fatores e como funciona?](https://www.facebook.com/help/148233965247823)

## <a name="add-your-github-account"></a>Adicione a sua conta GitHub

Adicione a sua conta GitHub ligando a verificação de dois fatores e adicionando a conta à app.

### <a name="turn-on-two-factor-verification"></a>Ligue a verificação de dois fatores

1. No seu computador, abra o GitHub, selecione a sua imagem do canto superior direito e, em seguida, selecione **Definições**.

    Aparece a página **de autenticação de dois fatores.**

2. Selecione **Segurança** a partir da barra lateral de **definições pessoais** e, em seguida, selecione **Ative a autenticação de dois fatores** a partir da área **de autenticação de dois fatores.**

### <a name="add-your-github-account-to-the-app"></a>Adicione a sua conta GitHub à aplicação

1. Na página **de autenticação de dois fatores** no seu computador, selecione **Configurar usando uma aplicação**.

2. Guarde os seus códigos de recuperação para que possa voltar à sua conta se perder o acesso e, em seguida, selecione **Seguinte**.

    Pode guardar os seus códigos descarregando-os para o seu dispositivo, imprimindo uma cópia impressa ou copiando-os numa ferramenta de gestor de passwords.

3. Na página **de autenticação de dois fatores,** selecione **Configurar usando uma aplicação**.

    A página muda para mostrar-lhe um código QR. Não feche esta página.

4. Abra a aplicação Microsoft Authenticator, selecione **Adicionar a conta** a partir do ícone **Deserção e controlo** no canto superior direito, selecione Outra conta **(Google, Facebook, etc.)** e, em seguida, selecione **introduzir este código** de texto a partir do texto no topo da página.

    A aplicação Microsoft Authenticator não consegue digitalizar o código QR, pelo que tem de introduzir manualmente o código.

5. Introduza um **nome de conta** (por exemplo, GitHub) e **digite** a chave Secreta do Passo 4 e, em seguida, **selecione Acabamento**.

6. Na página **autenticadora de dois fatores** no seu computador, digite o código de verificação de seis dígitos fornecido na aplicação para a sua conta GitHub e, em seguida, selecione **Enable**.

    A página **contas** da aplicação mostra-lhe o nome da sua conta e um código de verificação de seis dígitos. Para uma segurança adicional, o código de verificação muda a cada 30 segundos impedindo alguém de usar um código várias vezes.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e a sua conta GitHub, consulte [Sobre a autenticação de dois fatores.](https://help.github.com/articles/about-two-factor-authentication/)

## <a name="add-your-amazon-account"></a>Adicione a sua conta Amazon

Adicione a sua conta Amazon ligando a verificação de dois fatores e adicionando a conta à app.

### <a name="turn-on-two-factor-verification"></a>Ligue a verificação de dois fatores

1. No seu computador, abra a Amazon, selecione o menu **de entrega de listas & conta** e, em seguida, selecione A Sua **Conta**.

2. **Selecione Iniciar sessão & segurança,** inicie sessão na sua conta Amazon e, em seguida, selecione **Editar** na área **de Definições de Segurança Avançada.**

    Aparece a página **Definições avançadas de segurança.**

3. **Selecione Começar**.

4. Selecione **a App autenticador** a partir da página Escolha como receberá a página **de códigos.**

    A página muda para mostrar-lhe um código QR. Não feche esta página.

5. Abra a aplicação Microsoft Authenticator, **selecione Adicionar a conta** a partir do ícone De **personalizar e controlar** no canto superior direito e, em seguida, selecione Outra conta **(Google, Facebook, etc.)**.

6. Utilize a câmara do seu dispositivo para digitalizar o código QR do "Escolha como vai receber a página **de códigos"** no seu computador.

    >[!Note]
    >Se a sua câmara não estiver a funcionar corretamente, pode introduzir manualmente o código QR e o URL.

7. Reveja a página **contas** da aplicação Microsoft Authenticator no seu dispositivo, para se certificar de que as informações da sua conta estão corretas e que existe um código de verificação de seis dígitos associado.

    Para uma segurança adicional, o código de verificação muda a cada 30 segundos impedindo alguém de usar um código várias vezes.

8. Na **página Escolha como receberá códigos** no seu computador, digite o código de verificação de seis dígitos fornecido na aplicação para a sua conta Amazon e, em seguida, selecione **Verificar código e continuar**.

9. Preencha o resto do processo de inscrição, incluindo a adição de um método de verificação de cópias de segurança, como uma mensagem de texto, e, em seguida, selecione **Enviar código**.

10. Na página de **método de verificação de cópia de segurança** no seu computador, digite o código de verificação de seis dígitos fornecido pelo método de verificação de cópia de segurança para a sua conta Amazon e, em seguida, selecione Verificar código e **continuar**.

11. Na página **Quase pronto,** decida se faz do seu computador um dispositivo de confiança e, em seguida, selecione **Got it. Ligue Two-Step Verificação**.

    Aparece a página **Definições avançadas** de segurança, mostrando os detalhes de verificação de dois fatores atualizados.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e a sua conta Amazon, consulte [Sobre a Verificação Two-Step](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) e Iniciar [sessão com Two-Step Verificação](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).

## <a name="next-steps"></a>Passos seguintes

- Depois de adicionar as suas contas à aplicação, pode iniciar sôms usando a aplicação Authenticator no seu dispositivo. Para obter mais informações, consulte [Iniciar sção usando a aplicação.](user-help-auth-app-sign-in.md)

- Para dispositivos que executam o iOS, também pode fazer o back up das suas credenciais de conta e configurações de aplicações relacionadas, como a ordem das suas contas, para a nuvem. Para obter mais informações, consulte [Backup e recupere com a aplicação Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
