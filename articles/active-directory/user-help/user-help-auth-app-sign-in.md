---
title: Iniciar sessão nas suas contas com a aplicação Microsoft Authenticator - Azure Active Directory | Documentos da Microsoft
description: Utilize a aplicação Microsoft Authenticator para iniciar sessão no seu trabalho ou a conta escolar ou seu Microsoft pessoa e contas de não-Microsoft, com verificação de dois fatores ou o início de sessão no telefone.
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
ms.openlocfilehash: 0f5f190eee473d5eb483f901ada80cb67b4ffedd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58110516"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Iniciar sessão nas suas contas com a aplicação Microsoft Authenticator
A aplicação Microsoft Authenticator ajuda-o início de sessão para as suas contas se usar a verificação de dois fatores. Verificação de dois fatores ajuda-o a aceder às suas contas de forma mais segura, especialmente enquanto vê informações confidenciais. Uma vez que as palavras-passe podem ser esquecidas, roubado ou comprometido, dois fatores verificação é uma etapa adicional de segurança que ajuda a proteger a sua conta, tornando mais difícil para outras pessoas a entrar na.

Pode utilizar a aplicação Microsoft Authenticator de várias formas, incluindo:

- Fornecendo uma linha de comandos para um segundo método de verificação após entrar com seu nome de utilizador e palavra-passe.

- Fornecer início de sessão sem exigir uma palavra-passe, utilizando o seu nome de utilizador e dispositivo móvel com a impressão digital, face ou o PIN.

  >[!Important]
  >Este método de início de sessão do telefone só funciona com o seu trabalho ou escola e contas Microsoft pessoais. As contas de não-Microsoft exigem que use o processo de verificação de dois fatores padrão.

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder utilizar a aplicação Microsoft Authenticator, faça o seguinte:

 1. Baixe e instale a aplicação Microsoft Authenticator. Se ainda não tiver feito isso ainda, veja [transferir e instalar a aplicação](user-help-auth-app-download-install.md).

 2. Adicione as suas contas escolares/pessoais e de terceiros para a aplicação Microsoft Authenticator. Para obter os passos detalhados, consulte [adicionar a sua conta escolar ou profissional](user-help-auth-app-add-work-school-account.md), [Adicione suas contas pessoais](user-help-auth-app-add-personal-ms-account.md), e [adicionar as contas de não-Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>Ativar e utilizar o início de sessão no telefone para a sua conta escolar ou profissional
Início de sessão no telefone é um tipo de verificação de dois passos. Ainda tem de verificar sua identidade, fornecendo uma coisa que sabe e uma coisa que tenha, mas permite início de sessão de telefone, ignore a introduzir a palavra-passe de conta e executa todos a verificação de identidade no seu dispositivo móvel.

Antes de pode ativar o início de sessão no telefone, tem de ativar a verificação de dois fatores. Para obter mais informações sobre como ativar a verificação de dois fatores para uma conta, consulte [adicionar a sua conta escolar ou profissional](user-help-auth-app-add-work-school-account.md) e [Adicione suas contas pessoais](user-help-auth-app-add-personal-ms-account.md).

Início de sessão no telefone só está disponível no iOS e Android dispositivos que executam o Android 6.0 ou superior.

### <a name="turn-on-phone-sign-in"></a>Ativar o início de sessão no telefone 

- Abra a aplicação Microsoft Authenticator, aceda ao seu trabalho ou conta escolar e ativar o início de sessão no telefone:

    - **Se vir este ícone ![ícone mostrando a configurá-lo](media/user-help-auth-app-sign-in/icon.png).** Se este ícone é apresentado ao lado de seu trabalho ou nome de conta da instituição de ensino, isso significa que já configurou início de sessão no telefone para a conta. Poderá ser-lhe pedido para adicionar notificações push para a sua conta, para que pode ser notificado sobre pedidos de autenticação fora da aplicação.

    - **Se tiver utilizado a aplicação para a verificação de dois fatores.** Se já tiver utilizado a aplicação e a verificação de dois fatores, pode escolher a seta de lista pendente junto ao nome da conta e, em seguida, selecione **ativar o início de sessão no telefone**.
    
    - **Se não conseguir encontrar a sua conta escolar ou profissional.** Se não conseguir localizar sua conta escolar ou profissional na **contas** ecrã da aplicação, significa que ainda não adicionou-lo para a aplicação ainda. Adicionar a sua conta escolar ou profissional, seguindo os passos a [adicionar a sua conta escolar ou profissional](user-help-auth-app-add-work-school-account.md) artigo.

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Inicie sessão na sua conta com o início de sessão no telefone
Depois de ativar início de sessão no telefone, pode iniciar sessão utilizando apenas a aplicação Microsoft Authenticator.

1. Inicie sessão na sua conta escolar ou profissional.

    Depois de escrever o seu nome de utilizador, uma **aprovar o início de sessão** é apresentado o ecrã que mostra um número de dois dígitos e que lhe pede para início de sessão através da aplicação Microsoft Authenticator. Se não pretender utilizar este início de sessão no método, pode selecionar **em alternativa, utilize a palavra-passe**e inicie sessão com a sua palavra-passe.

    ![Aprovar a caixa de início de sessão no computador](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. Abra a notificação ou a aplicação Microsoft Authenticator no seu dispositivo e, em seguida, toque no número que corresponda no número apresentado no seu computador **aprovar início de sessão** ecrã.

    ![Aprovar início de sessão caixa no dispositivo](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. Escolher **aprovar** se reconhecer a tentativa de início de sessão. Caso contrário, escolha **negar**.

4. Utilize a sua chave de Biometria ou PIN de seu telefone para concluir a autenticação.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>Ativar e utilizar o início de sessão no telefone para as suas contas Microsoft pessoais
Pode ativar o início de sessão no telefone para a sua conta Microsoft pessoal, como a conta que utiliza para iniciar sessão no Outlook.com, Xbox ou Skype.

>[!NOTE]
>Para ajudar a proteger a sua conta, a aplicação Microsoft Authenticator requer um PIN ou biométrico bloqueio no seu dispositivo. Se mantiver o seu telemóvel desbloqueado, a aplicação tem de configurar um bloqueio de segurança antes de ativar o início de sessão no telefone.

### <a name="turn-on-phone-sign-in"></a>Ativar o início de sessão no telefone 

- Abra a aplicação Microsoft Authenticator, aceda à sua conta Microsoft pessoal e ativar o início de sessão no telefone:

    - **Se vir este ícone ![ícone mostrando a configurá-lo](media/user-help-auth-app-sign-in/icon.png).** Se este ícone é apresentado junto ao nome da sua conta, significa que já configurou início de sessão no telefone para a conta. Poderá ser-lhe pedido para adicionar notificações push para a sua conta, para que pode ser notificado sobre pedidos de autenticação fora da aplicação.

    - **Se tiver utilizado a aplicação para a verificação de dois fatores.** Se já tiver utilizado a aplicação e a verificação de dois fatores, pode escolher a seta de lista pendente junto ao nome da conta e, em seguida, selecione **ativar o início de sessão no telefone**.
    
    - **Se não conseguir encontrar a sua conta.** Se não conseguir localizar sua conta no **contas** ecrã da aplicação, significa que ainda não adicionou-lo para a aplicação ainda. Adicionar a sua conta Microsoft pessoal, seguindo os passos a [adicionar a sua conta Microsoft pessoal](user-help-auth-app-add-personal-ms-account.md#add-your-personal-microsoft-account) artigo.

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Inicie sessão na sua conta com o início de sessão no telefone

1. Aceda ao seu pessoal Microsoft início de sessão-página de conta e, em seguida, em vez de digitar a palavra-passe, selecione o **em alternativa, utilize a aplicação Microsoft Authenticator** ligação. 

    A Microsoft envia uma notificação para o seu telefone. 

2. Aprove a notificação. 

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Inicie sessão com a verificação de dois fatores para a sua conta
O método de verificação de dois fatores padrão requer que introduza o seu nome de utilizador e palavra-passe no dispositivo que está fazendo logon e, em seguida, escolha se a aplicação Microsoft Authenticator envia uma notificação ou se desejar copiar a verificação associada código dos **contas** ecrã da aplicação Microsoft Authenticator. Ative a verificação de dois fatores para sua conta como parte do processo para adicionar a conta para a aplicação Microsoft Authenticator.

>[!Note]
>Se não vir o seu trabalho ou a conta escolar ou a sua conta pessoal sobre o **contas** ecrã da aplicação Microsoft Authenticator, significa que ainda não adicionou a conta para a aplicação Microsoft Authenticator. Para adicionar a sua conta, veja [adicionar a sua conta escolar ou profissional](user-help-auth-app-add-work-school-account.md) ou [Adicione suas contas pessoais](user-help-auth-app-add-personal-ms-account.md).

Para os passos necessários para iniciar sessão no seu trabalho ou escola ou sua conta pessoal, com os vários métodos de verificação de dois fatores, consulte [iniciar sessão utilizando as informações de segurança ou de verificação de dois passos](user-help-sign-in.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
|**Pergunta**|**Solução**|
|--------------|-------------|
|**Como é iniciar sessão com o meu telefone mais segura do que escrever uma palavra-passe?**|Hoje em dia a maioria das pessoas sessão para web sites ou aplicações com um nome de utilizador e palavra-passe. Infelizmente, as palavras-passe podem ser perdidas, roubadas ou que adivinhado por hackers.<br><br>Depois de configurar a aplicação Microsoft Authenticator, ele cria uma chave no seu telefone para desbloquear a conta que está protegida por PIN ou biométrico bloqueio do seu telemóvel. Esta chave, em seguida, é utilizada para provar a sua identidade ao iniciar sessão.<br><br>**Importante**<br>Os dados só são utilizados para proteger a sua chave localmente. Nunca tem enviado para ou armazenadas na cloud.|
|**Início de sessão no telefone substituir verificação em dois passos? Deve desativá-lo?**|Início de sessão no telefone é um tipo de verificação de dois passos em que os dois passos ambos os acontecem no dispositivo móvel. Deve manter dois passo passos ativada para o ajudar a fornecer segurança adicional para a sua conta.|
|**Se eu manter a verificação de dois passos ativada para a minha conta, é necessário que aprovar dois notificações?**| Não. Iniciar sessão na sua conta da Microsoft através do telefone também é contabilizado como verificação de dois passos, portanto, não há nenhum segundo aprovação necessária.|
|**E se eu perder o meu telefone ou não o tiver comigo? Como posso acessar minha conta?**| Sempre é possível selecionar a utilização de uma palavra-passe em vez disso, a ligação na página de início de sessão para mudar para utilizar a sua palavra-passe. No entanto, se usar a verificação de dois passos ainda terá de utilizar um segundo método para verificar a sua identidade.<br><br>**Importante**<br>Recomendamos vivamente que para se certificar de que tiver mais do que um, atualizado, o método de verificação associado à sua conta.<br><br>Pode gerenciar seus métodos de verificação para contas pessoais da sua [definições de segurança](https://account.live.com/proofs/manage) página. Para contas profissionais ou escolares, pode aceder à sua organização [verificação de segurança adicional](https://aka.ms/MFASetup) página ou o **manter a sua conta segura** página se o administrador tiver ativado a informações de segurança. Para obter mais informações sobre as informações de segurança, consulte [descrição geral de informações (pré-visualização) de segurança](user-help-security-info-overview.md).<br><br>Se não for possível gerir os seus métodos de verificação, tem de contactar o seu administrador.|
|**Como parar de utilizar esta funcionalidade e voltar atrás e utilizar a minha palavra-passe?**|Contas pessoais, selecione o **em alternativa, utilize uma palavra-passe** ligação durante o início de sessão. À sua escolha mais recente memorizada e disponibilizada por predefinição na próxima vez que iniciar sessão. Se alguma vez pretender voltar a utilizar o início de sessão no telefone, selecione o **em alternativa, utilize uma aplicação** ligação durante o início de sessão.<br><br>Para contas profissionais ou escolares, tem à sua página de verificação de segurança adicionais (como, por exemplo https://aka.ms/MFASetup) e remover a aplicação Microsoft Authenticator.|
|**Por que não é possível utilizar mais de uma conta escolar ou profissional para início de sessão no telefone?**| Um telefone tem de estar registado para uma única conta escolar ou profissional. Se pretender ativar o início de sessão no telefone para uma conta escolar ou profissional de diferente, deve primeiro anular o registo o dispositivo antigo por meio da **definições** página.|
|**Posso entrar no meu computador com o meu telefone?**| Para o seu computador, recomendamos que iniciar sessão utilizando Hello do Windows no Windows 10. Windows Hello permite-lhe utilizar face, a identificação digital ou o PIN para iniciar sessão.|

## <a name="next-steps"></a>Passos Seguintes

- Se estiver a ter dificuldades em seu código de verificação para a sua conta Microsoft pessoal, consulte a **resolução de problemas de código de verificação** secção a [as informações de segurança de conta Microsoft & verificação códigos de](https://support.microsoft.com/en-us/help/12428/microsoft-account-security-info-verification-codes) artigo.

- Se tiver perguntas mais gerais sobre a aplicação, veja o [FAQs do Microsoft Authenticator](user-help-auth-app-faq.md)

- Se pretender obter mais informações sobre a verificação de dois passos, veja [configurar a minha conta para verificação de dois passos](multi-factor-authentication-end-user-first-time.md)

- Se pretender obter mais informações sobre as informações de segurança, veja [descrição geral de informações (pré-visualização) de segurança](user-help-security-info-overview.md)
