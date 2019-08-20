---
title: Entre em suas contas usando o Microsoft Authenticator app-Azure Active Directory | Microsoft Docs
description: Use o aplicativo Microsoft Authenticator para entrar em sua conta corporativa ou de estudante ou em suas contas pessoais da Microsoft e não Microsoft, usando a verificação de dois fatores ou a entrada pelo telefone.
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
ms.openlocfilehash: 9af3549984bd29a6e896e498bf4a2e6c67d7e0e2
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616020"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Entre em suas contas usando o aplicativo Microsoft Authenticator

O aplicativo Microsoft Authenticator ajuda você a entrar em suas contas se usar a verificação de dois fatores. A verificação de dois fatores ajuda você a acessar suas contas com mais segurança, especialmente ao mesmo tempo em que exibe informações confidenciais. Como as senhas podem ser esquecidas, roubadas ou comprometidas, a verificação de dois fatores é uma etapa de segurança adicional que ajuda a proteger sua conta, dificultando a interrupção de outras pessoas.

Você pode usar o aplicativo Microsoft Authenticator de várias maneiras, incluindo:

- Fornecer um prompt para um segundo método de verificação depois de entrar com seu nome de usuário e senha.

- Fornecendo entrada sem a necessidade de uma senha, usando seu nome de usuário e seu dispositivo móvel com sua impressão digital, face ou PIN.

  >[!Important]
  >Esse método de entrada do telefone funciona apenas com suas contas corporativas ou de estudante e da Microsoft. Suas contas que não são da Microsoft exigem que você use o processo de verificação padrão de dois fatores.

## <a name="prerequisites"></a>Pré-requisitos

Para poder usar o aplicativo Microsoft Authenticator, você deve:

 1. Baixe e instale o aplicativo Microsoft Authenticator. Se você ainda não fez isso, consulte [baixar e instalar o aplicativo](user-help-auth-app-download-install.md).

 2. Adicione suas contas corporativas, de estudante, pessoais e de terceiros ao aplicativo Microsoft Authenticator. Para obter as etapas detalhadas, consulte [adicionar sua conta corporativa ou de estudante](user-help-auth-app-add-work-school-account.md), [adicionar suas contas pessoais](user-help-auth-app-add-personal-ms-account.md)e [adicionar suas contas que não são da Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>Ativar e usar a entrada pelo telefone para sua conta corporativa ou de estudante

A entrada pelo telefone é um tipo de verificação em duas etapas. Você ainda deve verificar sua identidade fornecendo algo que você conhece e algo que você tem, mas entrar no telefone permite pular a inserção da senha da conta e executar toda a verificação de identidade em seu dispositivo móvel.

Antes de poder ativar a entrada pelo telefone, você deve ativar a verificação de dois fatores. Para obter mais informações sobre como ativar a verificação de dois fatores para uma conta, consulte [adicionar sua conta corporativa ou de estudante](user-help-auth-app-add-work-school-account.md) e [adicionar suas contas pessoais](user-help-auth-app-add-personal-ms-account.md).

A entrada pelo telefone só está disponível em dispositivos iOS e Android que executam o Android 6,0 ou superior.

### <a name="turn-on-phone-sign-in"></a>Ativar entrada pelo telefone

- Abra o aplicativo Microsoft Authenticator, vá para sua conta corporativa ou de estudante e ative a entrada pelo telefone:

    - **Se você vir esse ícone ![de](media/user-help-auth-app-sign-in/icon.png)ícone mostrando que está configurado.** Se esse ícone aparecer ao lado do nome de sua conta corporativa ou de estudante, isso significa que você já configurou a entrada pelo telefone para a conta. Você pode ser solicitado a adicionar notificações por push para sua conta, para que você possa ser notificado sobre solicitações de autenticação fora do aplicativo.

    - **Se você estiver usando o aplicativo para verificação de dois fatores.** Se já estiver usando o aplicativo e a verificação de dois fatores, você poderá escolher a seta suspensa ao lado do nome da conta e, em seguida, selecionar **Habilitar entrada pelo telefone**.

    - **Se você não conseguir localizar sua conta corporativa ou de estudante.** Se você não conseguir localizar sua conta corporativa ou de estudante na tela **contas** do aplicativo, isso significa que você ainda não a adicionou ao aplicativo. Adicione sua conta corporativa ou de estudante seguindo as etapas no artigo [adicionar sua conta corporativa ou de estudante](user-help-auth-app-add-work-school-account.md) .

Depois de ativar a entrada pelo telefone, você pode entrar usando apenas o aplicativo Microsoft Authenticator.

1. Entre em sua conta corporativa ou de estudante.

    Depois de digitar seu nome de usuário, uma tela **aprovar entrada** aparece mostrando um número de dois dígitos e solicitando que você entre no aplicativo Microsoft Authenticator. Se não quiser usar esse método de entrada, você poderá selecionar **usar sua senha**e entrar usando sua senha.

    ![Aprovar caixa de entrada no computador](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. Abra a notificação ou o aplicativo Microsoft Authenticator em seu dispositivo e, em seguida, toque no número que corresponde ao número que você vê na tela **aprovar entrada** do computador.

    ![Aprovar caixa de entrada no dispositivo](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. Escolha **aprovar** se você reconhece a tentativa de entrada. Caso contrário, escolha **negar**.

4. Use o PIN do seu telefone ou sua chave biométrica para concluir a autenticação.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>Ativar e usar a entrada pelo telefone para suas contas pessoais da Microsoft

Você pode ativar a entrada pelo telefone para seus conta Microsoft pessoais, como a conta usada para entrar no Outlook.com, Xbox ou Skype.

>[!NOTE]
>Para ajudar a proteger sua conta, o aplicativo Microsoft Authenticator requer um PIN ou um bloqueio biométrico em seu dispositivo. Se você mantiver seu telefone desbloqueado, o aplicativo exigirá que você configure um bloqueio de segurança antes de ativar a entrada pelo telefone.

### <a name="turn-on-phone-sign-in"></a>Ativar entrada pelo telefone 

- Abra o aplicativo Microsoft Authenticator, vá para sua conta Microsoft pessoal e ative a entrada pelo telefone:

    - **Se você vir esse ícone ![de](media/user-help-auth-app-sign-in/icon.png)ícone mostrando que está configurado.** Se esse ícone aparecer ao lado do nome da sua conta, isso significa que você já configurou a entrada pelo telefone para a conta. Você pode ser solicitado a adicionar notificações por push para sua conta, para que você possa ser notificado sobre solicitações de autenticação fora do aplicativo.

    - **Se você estiver usando o aplicativo para verificação de dois fatores.** Se já estiver usando o aplicativo e a verificação de dois fatores, você poderá escolher a seta suspensa ao lado do nome da conta e, em seguida, selecionar **Habilitar entrada pelo telefone**.

    - **Se você não conseguir localizar sua conta.** Se você não conseguir localizar sua conta na tela de **contas** do aplicativo, isso significa que você ainda não a adicionou ao aplicativo. Adicione seu conta Microsoft pessoal seguindo as etapas no artigo [Adicionar seu conta Microsoft pessoal](user-help-auth-app-add-personal-ms-account.md#add-your-personal-microsoft-account) .

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Entre em sua conta usando a entrada pelo telefone

1. Acesse sua página de entrada pessoal do conta Microsoft e, em vez de digitar sua senha, selecione o link **usar o aplicativo Microsoft Authenticator em vez disso** .

    A Microsoft envia uma notificação para seu telefone.

2. Aprove a notificação.

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Entre usando a verificação de dois fatores para sua conta

O método de verificação padrão de dois fatores exige que você insira seu nome de usuário e senha no dispositivo no qual você está fazendo logon e, em seguida, escolha se o aplicativo de Microsoft Authenticator envia uma notificação ou se você prefere copiar a verificação associada código da tela **contas** do aplicativo Microsoft Authenticator. Você ativa a verificação de dois fatores para sua conta como parte do processo para adicionar a conta ao aplicativo Microsoft Authenticator.

>[!Note]
>Se você não vir sua conta corporativa ou de estudante ou sua conta pessoal na tela **contas** do aplicativo Microsoft Authenticator, isso significa que você não adicionou a conta ao aplicativo Microsoft Authenticator. Para adicionar sua conta, consulte [adicionar sua conta corporativa ou de estudante](user-help-auth-app-add-work-school-account.md) ou [adicionar suas contas pessoais](user-help-auth-app-add-personal-ms-account.md).

Para as etapas necessárias para entrar em seu trabalho ou escola ou sua conta pessoal, usando os vários métodos de verificação de dois fatores, consulte [entrar usando a verificação em duas etapas ou informações de segurança](user-help-sign-in.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

| Pergunta | Solução |
| -------- | -------- |
| Como entrar com meu telefone é mais seguro do que digitar uma senha? | Hoje, a maioria das pessoas entra em sites ou aplicativos usando um nome de usuário e senha. Infelizmente, as senhas podem ser perdidas, roubadas ou adivinhadas por hackers.<br><br>Depois de configurar o aplicativo Microsoft Authenticator, ele cria uma chave em seu telefone para desbloquear sua conta protegida pelo PIN ou pelo bloqueio biométrico do telefone. Essa chave é usada para provar sua identidade durante a entrada.<br><br>**Importante**<br>Seus dados só são usados para proteger sua chave localmente. Ele nunca é enviado ou armazenado na nuvem. |
| A entrada pelo telefone substitui a verificação em duas etapas? Devo desativá-la? | A entrada pelo telefone é um tipo de verificação em duas etapas, em que as duas medidas acontecem no dispositivo móvel. Você deve manter a verificação em duas etapas ativada para ajudar a fornecer segurança adicional à sua conta. |
| Se eu mantiver a verificação em duas etapas ativada para minha conta, preciso aprovar duas notificações? | Não. Entrar em seu conta Microsoft usando seu telefone também conta como verificação em duas etapas, portanto, não há uma segunda aprovação necessária. |
| E se eu perder meu telefone ou não tiver? Como fazer acessar minha conta? | Você sempre pode selecionar o link usar uma senha no lugar da página de entrada para voltar a usar sua senha. No entanto, se você usar a verificação em duas etapas, ainda precisará usar um segundo método para verificar sua identidade.<br><br>**Importante**<br>É altamente recomendável que você verifique se você tem mais de um método de verificação atualizado associado à sua conta.<br><br>Você pode gerenciar seus métodos de verificação para contas pessoais na sua página de [configurações de segurança](https://account.live.com/proofs/manage) . Para contas corporativas ou de estudante, você pode ir para a página de [verificação de segurança adicional](https://aka.ms/MFASetup) da sua organização ou a página **manter sua conta segura** se o administrador tiver ativado as informações de segurança. Para obter mais informações sobre informações de segurança, consulte [visão geral de informações de segurança (versão prévia)](user-help-security-info-overview.md).<br><br>Se não for possível gerenciar seus métodos de verificação, você deverá contatar o administrador. |
| Como fazer parar de usar esse recurso e voltar a usar minha senha? | Para contas pessoais, selecione o link **usar uma senha em vez** de entrar. Sua escolha mais recente é lembrada e oferecida por padrão na próxima vez que você entrar. Se você quiser voltar a usar a entrada pelo telefone, selecione o link **usar um aplicativo em vez disso** durante o logon.<br><br>Para contas corporativas ou de estudante, você deve cancelar o registro do dispositivo na página **configurações** do aplicativo Microsoft Authenticator ou desabilitar o dispositivo na área de **atividade dispositivos &** do seu perfil. Para obter mais informações sobre como desabilitar seu dispositivo do seu perfil, consulte [atualizar suas informações de perfil e conta no portal meus aplicativos](my-apps-portal-end-user-update-profile.md#view-your-organization-related-profile-information). |
| Por que não posso usar mais de uma conta corporativa ou de estudante para entrar no telefone? | Um telefone deve ser registrado para uma única conta corporativa ou de estudante. Se você quiser ativar a entrada pelo telefone para uma conta corporativa ou de estudante diferente, deverá primeiro cancelar o registro do dispositivo antigo por meio da página **configurações** . |
| Posso entrar no meu computador usando meu telefone? | Para o seu computador, é recomendável entrar usando o Windows Hello no Windows 10. O Windows Hello permite que você use sua face, impressão digital ou PIN para entrar. |

## <a name="next-steps"></a>Passos Seguintes

- Se você estiver tendo problemas para obter seu código de verificação para seu conta Microsoft pessoal, consulte a seção Solucionando problemas de **código de verificação** do artigo [conta Microsoft informações de segurança & códigos de verificação](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- Se você tiver mais perguntas gerais sobre o aplicativo, consulte as [perguntas frequentes Microsoft Authenticator](user-help-auth-app-faq.md)

- Se você quiser obter mais informações sobre a verificação em duas etapas, consulte [configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

- Se você quiser obter mais informações sobre informações de segurança, consulte [visão geral de informações de segurança (versão prévia)](user-help-security-info-overview.md)
