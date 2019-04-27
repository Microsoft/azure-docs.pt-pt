---
title: Adicionar as contas de terceiros para a aplicação Microsoft Authenticator - Azure Active Directory | Documentos da Microsoft
description: Como adicionar as contas de não-Microsoft, tal como para o Google, Facebook ou GitHub para a aplicação Microsoft Authenticator para verificação de dois fatores.
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
ms.openlocfilehash: e6f94ba30c06fc6975ab212c895cecefe5d383fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60473911"
---
# <a name="add-your-non-microsoft-accounts"></a>Adicionar as contas de não-Microsoft
Adicione as contas de não-Microsoft, como para o Google, Facebook ou GitHub para a aplicação Microsoft Authenticator para verificação de dois fatores. A aplicação Microsoft Authenticator funciona com qualquer aplicação que utiliza a verificação de dois fatores e de qualquer conta que suporte os padrões baseados no tempo palavra-passe Monouso (TOTP).

>[!Important]
>Antes de poder adicionar a sua conta, tem de transferir e instalar a aplicação Microsoft Authenticator. Se ainda não tiver feito, siga os passos a [transferir e instalar a aplicação](user-help-auth-app-download-install.md) artigo.

## <a name="add-personal-accounts"></a>Adicionar contas pessoais
Em geral, para todas as suas contas pessoais, faça o seguinte:

1. Inicie sessão na sua conta e, em seguida, ativar a verificação de dois fatores usando o seu dispositivo ou o seu computador em.

2. Adicione a conta para a aplicação Microsoft Authenticator. Poderá ser-lhe pedido para ler um código QR como parte deste processo.

Fornecemos aqui o processo para as suas contas do Facebook, Google, GitHub e Amazon, mas este processo é o mesmo para qualquer outra aplicação, como no Instagram, Netflix ou Adobe.

## <a name="add-your-google-account"></a>Adicionar a sua conta do Google
Adicione a sua conta do Google ao ativar a verificação de dois fatores e, em seguida, adicionar a conta para a aplicação.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores em

1. No seu computador, aceda a https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome, selecione **começar**e, em seguida, verificar a sua identidade.

2. Siga os passos na página para ativar a verificação de dois passos para a sua conta pessoal do Google.

### <a name="add-your-google-account-to-the-app"></a>Adicionar a sua conta do Google para a aplicação

1. Na página do Google no seu computador, vá para o **configurar alternativa segunda etapa** secção, escolha **configurar** do **aplicação de autenticador** secção.

2. Sobre o **obter códigos a partir da aplicação de autenticador** página, selecione **Android** ou **iPhone** com base no seu tipo de telefone e, em seguida, selecione **próxima**.

    É-lhe fornecido um código QR que pode usar para associar automaticamente a sua conta com a aplicação Microsoft Authenticator. Não feche esta janela.

3. Abra a aplicação Microsoft Authenticator, selecione **adicionar conta** partir a **personalizar e controle** ícone no canto superior direito e, em seguida, selecione **outra conta (Google, Facebook, etc.)** .

4. Utilizar a câmara do dispositivo para analisar o código QR a partir da **configurar Authenticator** página no seu computador.

    >[!Note]
    >Se a câmara não está a funcionar corretamente, pode introduzir o código QR e o URL manualmente.

5. Reveja os **contas** página da aplicação Microsoft Authenticator no seu dispositivo, para certificar-se de que as informações da conta é a direita e de que existe um código de verificação de seis dígitos associados.

    Para segurança adicional, as alterações de código de verificação a cada 30 segundos a impedir que alguém usando um código várias vezes.

6. Selecione **próxima** no **configurar Authenticator** página no seu computador, escreva o código de verificação de seis dígitos fornecido na aplicação para a sua conta do Google e, em seguida, selecione **verificar**.

7. Sua conta foi validada, e pode selecionar **feito** para fechar a **configurar Authenticator** página.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e a sua conta do Google, veja [ativar a verificação de 2 passos em](https://support.google.com/accounts/answer/185839) e [Saiba mais sobre a verificação de 2 passos](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Adicionar a sua conta do Facebook
Adicione a sua conta do Facebook, a ativação da verificação de dois fatores e, em seguida, adicionar a conta para a aplicação.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores em

1. No seu computador, abra o Facebook, selecione o menu pendente no canto superior direito e, em seguida, aceda a **configurações** > **segurança e de início de sessão**.

    O **segurança e de início de sessão** é apresentada a página.

2. Vá para baixo para o **utilizar a autenticação de dois fatores** opção a **autenticação de dois fatores** secção e, em seguida, selecione **editar**.

    O **autenticação de dois fatores** é apresentada a página.

3. Selecione **ativar o**.

### <a name="add-your-facebook-account-to-the-app"></a>Adicionar a sua conta do Facebook para a aplicação

1. Na página do Facebook no seu computador, vá para o **adicionar uma cópia de segurança** secção e, em seguida, escolha **configuração** partir o **aplicação de autenticação** área.

    É-lhe fornecido um código QR que pode usar para associar automaticamente a sua conta com a aplicação Microsoft Authenticator. Não feche esta janela.

2. Abra a aplicação Microsoft Authenticator, selecione **adicionar conta** partir a **personalizar e controle** ícone no canto superior direito e, em seguida, selecione **outra conta (Google, Facebook, etc.)** .

3. Utilizar a câmara do dispositivo para analisar o código QR a partir da **dois avaliar autenticação** página no seu computador.

    >[!Note]
    >Se a câmara não está a funcionar corretamente, pode introduzir o código QR e o URL manualmente.

4. Reveja os **contas** página da aplicação Microsoft Authenticator no seu dispositivo, para certificar-se de que as informações da conta é a direita e de que existe um código de verificação de seis dígitos associados.

    Para segurança adicional, as alterações de código de verificação a cada 30 segundos a impedir que alguém usando um código várias vezes.

5. Selecione **próxima** sobre o **dois avaliar autenticação** página no seu computador e, em seguida, escreva o código de verificação de seis dígitos fornecido na aplicação para a sua conta do Facebook.

    Sua conta foi validada e agora, pode utilizar a aplicação para verificar a sua conta.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e a sua conta do Facebook, consulte [o que é a autenticação de dois fatores e como ele funciona?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>Adicionar a sua conta do GitHub
Adicione a sua conta do GitHub, a ativação da verificação de dois fatores e, em seguida, adicionar a conta para a aplicação.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores em

1. No seu computador, abra o GitHub, selecione a sua imagem no canto superior direito e, em seguida, selecione **definições**.

    O **autenticação de dois fatores** é apresentada a página.

2. Selecione **segurança** partir a **configurações pessoais** barra lateral e, em seguida, selecione **ativar a autenticação de dois fatores** do **autenticação de dois fatores**  área.

### <a name="add-your-github-account-to-the-app"></a>Adicionar a sua conta do GitHub para a aplicação

1. Sobre o **autenticação de dois fatores** página no seu computador, selecione **configurada por meio de uma aplicação**.

2. Salvar os códigos de recuperação, para que pode voltar à sua conta se perder o acesso e, em seguida, selecione **seguinte**. 

    Pode salvar seus códigos ao transferi-los para o seu dispositivo, por imprimir uma cópia ou ao copiá-los numa ferramenta de Gestor de palavra-passe.

3. Sobre o **autenticação de dois fatores** página, selecione **configurada por meio de uma aplicação**.

    As alterações de página para mostrar a um código QR. Não feche esta página.

4. Abra a aplicação Microsoft Authenticator, selecione **adicionar conta** partir a **personalizar e controle** ícone no canto superior direito, selecione **outra conta (Google, Facebook, etc.)** e, em seguida, selecione **introduza este código de texto** do texto na parte superior da página.

    A aplicação Microsoft Authenticator não consegue analisar o código QR, pelo que tem de introduzir manualmente o código.

5. Introduza um **nome da conta** (por exemplo, o GitHub) e escreva o **chave secreta** do passo 4 e, em seguida, selecione **concluir**.

4. Sobre o **autenticação de dois fatores** página no seu computador, escreva o código de verificação de seis dígitos fornecido na aplicação para a sua conta do GitHub e, em seguida, selecione **ativar**.

    O **contas** página da aplicação mostra-lhe o nome da conta e um código de verificação de seis dígitos. Para segurança adicional, as alterações de código de verificação a cada 30 segundos a impedir que alguém usando um código várias vezes.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e a sua conta do GitHub, veja [prestes a autenticação de dois fatores](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Adicionar a sua conta Amazon
Adicione a sua conta Amazon, a ativação da verificação de dois fatores e, em seguida, adicionar a conta para a aplicação.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores em

1. No seu computador, Amazon aberta, selecione o **conta de & listas** menu pendente e, em seguida, selecione **Your Account**.

2. Selecione **início de sessão e de segurança**, inicie sessão na sua conta Amazon e, em seguida, selecione **editar** no **definições de segurança avançadas** área.

    O **definições de segurança avançadas** é apresentada a página.

3. Selecione **começar**.

4. Selecione **aplicação de autenticador** partir a **escolha como vai receber códigos de** página.

    As alterações de página para mostrar a um código QR. Não feche esta página.

5. Abra a aplicação Microsoft Authenticator, selecione **adicionar conta** partir a **personalizar e controle** ícone no canto superior direito e, em seguida, selecione **outra conta (Google, Facebook, etc.)** .

6. Utilizar a câmara do dispositivo para analisar o código QR a partir do **escolha como vai receber códigos** página no seu computador.

    >[!Note]
    >Se a câmara não está a funcionar corretamente, pode introduzir o código QR e o URL manualmente.

5. Reveja os **contas** página da aplicação Microsoft Authenticator no seu dispositivo, para certificar-se de que as informações da conta é a direita e de que existe um código de verificação de seis dígitos associados.

    Para segurança adicional, as alterações de código de verificação a cada 30 segundos a impedir que alguém usando um código várias vezes.

6. Na **escolha como vai receber códigos** página no seu computador, escreva o código de verificação de seis dígitos fornecido na aplicação para a sua conta Amazon e, em seguida, selecione **verificar o código e continuar**.

7. Concluir o processo de inscrição, incluindo a adição de um método de verificação de cópia de segurança, como uma mensagem de texto e, em seguida, selecione **enviar código**.

8. Sobre o **adicionar um método de verificação de cópia de segurança** página no seu computador, escreva o código de verificação de seis dígitos fornecido pelo seu método de verificação de cópia de segurança para a sua conta Amazon e, em seguida, selecione **verificar o código e continuar**.

9. O Almost página, e decida se pretende que o seu computador um dispositivo fidedigno e, em seguida, selecione **o tem. Ativar a verificação de dois passos em**.

    O **definições de segurança avançadas** é apresentada a página, que mostra os detalhes de verificação de dois fatores atualizado.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e a sua conta Amazon, consulte [sobre a verificação de dois passos](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) e [iniciar sessão com a verificação de dois passos](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).


## <a name="next-steps"></a>Passos Seguintes

- Depois de adicionar as suas contas para a aplicação, pode iniciar sessão com a aplicação de autenticador no seu dispositivo. Para obter mais informações, consulte [iniciar sessão com a aplicação](user-help-auth-app-sign-in.md).

- Para dispositivos iOS, também pode criar cópias de segurança as credenciais da conta e relacionados com as definições de aplicação, como a ordem das suas contas, a nuvem. Para obter mais informações, consulte [cópia de segurança e recuperação com a aplicação Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
