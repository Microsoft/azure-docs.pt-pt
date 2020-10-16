---
title: Fazer back up e recuperar contas com a app Microsoft Authenticator - Azure AD
description: Aprenda a fazer cópias de serviço e recupere as credenciais de conta apoiadas, utilizando a aplicação Microsoft Authenticator.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: end-user-help
ms.date: 06/03/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: e7608196c962a6918a90b91457f856f3e11b04f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530906"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Fazer o back up e recuperar as credenciais de conta usando a app Microsoft Authenticator

**Aplica-se a:**

- dispositivos iOS, versão em execução 5.7.0 e posterior

- Dispositivos Android, versão 6.6.0 e posterior

A aplicação Microsoft Authenticator confirma as credenciais da sua conta e as definições de aplicações relacionadas, como a ordem das suas contas, para a nuvem. Depois de fazer cópia de segurança, também pode utilizar a app para recuperar as suas informações num novo dispositivo, evitando potencialmente ficar bloqueado ou ter de recriar contas.

Cada localização de armazenamento de backup requer que você tenha uma conta pessoal da Microsoft, enquanto o iOS também requer que você tenha uma conta iCloud. Pode ter várias contas armazenadas nesse único local. Por exemplo, pode ter uma conta pessoal, uma conta de trabalho ou escola, e uma conta pessoal, não-Microsoft, como para o Facebook, Google, e assim por diante.

> [!IMPORTANT]
> Apenas as suas credenciais pessoais e de conta de terceiros são armazenadas, o que inclui o seu nome de utilizador e o código de verificação da conta que é necessário para provar a sua identidade. Não armazenamos nenhuma outra informação associada às suas contas, incluindo e-mails ou ficheiros. Também não associamos ou partilhamos as suas contas de qualquer forma ou com qualquer outro produto ou serviço. E, finalmente, o seu administrador de TI não vai obter nenhuma informação sobre nenhuma destas contas.

## <a name="back-up-your-account-credentials"></a>Ressou as credenciais da sua conta

Antes de poder apoiar as suas credenciais, deve ter:

- Uma [conta](https://account.microsoft.com/account) pessoal da Microsoft para funcionar como a sua conta de recuperação.

- **Apenas para iOS,** você deve ter uma [conta iCloud](https://www.icloud.com/) para o local de armazenamento real.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Para ligar a cópia de segurança em nuvem para dispositivos iOS

- No seu dispositivo iOS, selecione **Definições**, selecione **Backup**e, em seguida, ligue **a cópia de segurança do iCloud**.

    As credenciais da sua conta são ressaradas na sua conta iCloud.

    ![Ecrã de definições do iOS, mostrando a localização das definições de backup do iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Para ligar a cópia de segurança em nuvem para dispositivos Android

- No seu dispositivo Android, selecione **Definições**, selecione **Backup**e, em seguida, ligue **a cópia de segurança cloud**.

    As credenciais da sua conta estão na sua conta na nuvem.

    ![Ecrã de definições do Android, mostrando a localização das definições de backup](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Recupere as credenciais da sua conta no seu novo dispositivo

Pode recuperar as credenciais da sua conta na nuvem, mas primeiro tem de se certificar de que a conta que está a recuperar não existe na aplicação Microsoft Authenticator. Por exemplo, se estiver a recuperar a sua conta pessoal da Microsoft, tem de se certificar de que não tem uma conta pessoal da Microsoft já criada na aplicação autenticadora. Este cheque é importante para termos a certeza de que não estamos a sobrepor-nos ou a apagar uma conta existente por engano.

### <a name="to-recover-your-information"></a>Para recuperar a sua informação

1. No seu dispositivo móvel, abra a aplicação Microsoft Authenticator e selecione Comece a **recuperar** a partir da parte inferior do ecrã.

    ![App Microsoft Authenticator, mostrando onde clicar Iniciar recuperação](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Faça o sômis na sua conta de recuperação, utilizando a mesma conta pessoal da Microsoft que utilizou durante o processo de backup.

    As credenciais da sua conta estão recuperadas para o novo dispositivo.

Depois de terminar a sua recuperação, poderá notar que os seus códigos pessoais de verificação da conta Microsoft na aplicação Microsoft Authenticator são diferentes entre os seus telemóveis antigos e novos. Os códigos são diferentes porque cada dispositivo tem a sua própria credencial única, mas ambos são válidos e funcionam durante a assinatura do telefone associado.

## <a name="recover-accounts-requiring-more-verification"></a>Recuperar contas que requerem mais verificação

Se utilizar notificações push com as suas contas pessoais ou de trabalho ou escolares, receberá um alerta no ecrã que diz que deve fornecer uma verificação adicional antes de poder recuperar as suas informações. Uma vez que as notificações push requerem a utilização de uma credencial que esteja ligada ao seu dispositivo específico e nunca seja enviada através da rede, tem de provar a sua identidade antes de a credencial ser criada no seu dispositivo.

Para contas pessoais da Microsoft, pode provar a sua identidade introduzindo a sua palavra-passe juntamente com um e-mail alternativo ou número de telefone. Para contas de trabalho ou escola, deve digitalizar um código QR que lhe foi dado pelo seu fornecedor de conta.

### <a name="to-provide-more-verification-for-personal-accounts"></a>Para fornecer mais verificação para contas pessoais

1. No ecrã **contas** da aplicação Microsoft Authenticator, toque na conta que pretende recuperar para abrir a vista completa do ecrã da conta.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png" alt-text="Screenshot que mostra a aplicação Microsoft Authenticator com os azulejos de conta disponíveis." border="true":::

1. Toque no azulejo para a conta que está a recuperar e toque na opção de iniciar sing para recuperar. Introduza a sua palavra-passe e, em seguida, confirme o seu endereço de e-mail ou número de telefone como verificação adicional.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-codes.png" alt-text="Screenshot que mostra a aplicação Microsoft Authenticator com os azulejos de conta disponíveis." border="true":::

### <a name="to-provide-more-verification-for-work-or-school-accounts"></a>Para fornecer mais verificação para contas de trabalho ou escola

1. No ecrã **contas** da aplicação Microsoft Authenticator, toque na conta que pretende recuperar para abrir a vista completa do ecrã da conta.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-arrow.png" alt-text="Screenshot que mostra a aplicação Microsoft Authenticator com os azulejos de conta disponíveis." border="true":::

1. Na vista completa do ecrã, toque na opção de digitalizar um código QR para recuperar totalmente.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-qr-code.png" alt-text="Screenshot que mostra a aplicação Microsoft Authenticator com os azulejos de conta disponíveis." border="true":::

>[!NOTE]
>Para obter mais informações sobre os códigos QR e como obter um, consulte [Começar com a aplicação Microsoft Authenticator](./user-help-auth-app-download-install.md) ou [configurar informações de segurança para utilizar uma aplicação autenticadora](./security-info-setup-auth-app.md), com base no facto de o seu administrador ter ligado as informações de segurança.
>
>Se esta for a primeira vez que está a configurar a aplicação Microsoft Authenticator, poderá receber uma solicitação a perguntar se permite que a app aceda à sua câmara (iOS) ou se permite que a app tire fotografias e grave vídeos (Android). Tem de selecionar **Permitir** que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no passo seguinte. Se não permitir a câmara, ainda pode configurar a aplicação autenticadora, mas terá de adicionar manualmente a informação de código. Para obter informações sobre como adicionar o código manualmente, consulte [manualmente adicionar uma conta à aplicação.](user-help-auth-app-add-account-manual.md)

## <a name="troubleshoot-backup-and-recovery-problems"></a>Problemas de backup e recuperação de resolução de problemas

Existem algumas razões pelas quais o seu backup pode não estar disponível

- **Alterar sistemas operativos**: A sua cópia de segurança é armazenada no iCloud para iOS e no fornecedor de armazenamento em nuvem da Microsoft para Android. Isto significa que a sua cópia de segurança não está disponível se trocar entre dispositivos Android e iOS. Se escoar o interruptor, tem de recriar manualmente as suas contas dentro da aplicação Microsoft Authenticator.

- **Problemas de rede**: Se estiver a passar por problemas relacionados com a rede, certifique-se de que está ligado à rede e que está devidamente inscrito na sua conta.

- **Problemas de conta**: Se estiver a passar por problemas relacionados com a conta, certifique-se de que está devidamente inscrito na sua conta. Para o iOS isto significa que você deve ser assinado no iCloud usando a mesma conta AppleID que o seu iPhone.

- **Eliminação acidental**: É possível que tenha apagado a sua conta de reserva do seu dispositivo anterior ou enquanto gere a sua conta de armazenamento na nuvem. Nesta situação, deve recriar manualmente a sua conta dentro da app.

- **Contas existentes no Microsoft Authenticator**: Se já tiver configurado contas na aplicação Microsoft Authenticator, a aplicação não conseguirá recuperar as suas contas de back-up. Prevenir a recuperação ajuda a garantir que os detalhes da sua conta não são substituídos com informações desatualizadas. Nesta situação, deve remover qualquer informação de conta existente das contas existentes criadas na sua aplicação Authenticator antes de poder recuperar a sua cópia de segurança.

- **A cópia de segurança está desatualizada**: Se as suas informações de backup estiverem desatualizadas, poderá ser-lhe pedido que reacmem as informações, insinuá-la novamente na sua conta de Recuperação da Microsoft. A sua conta de recuperação é a conta pessoal da Microsoft que usou inicialmente para armazenar a sua cópia de segurança. Se for necessário um início de sposição, verá um ponto vermelho no seu menu ou barra de ação, ou verá um ícone de marca de exclamação que o leva a iniciar sposição para terminar a restauração da cópia de segurança. Depois de selecionar o ícone apropriado, será solicitado que faça sedudas novamente para atualizar as suas informações.

## <a name="next-steps"></a>Passos seguintes

Agora que fez o back up e recuperou as credenciais da sua conta para o seu novo dispositivo, pode continuar a utilizar a aplicação Microsoft Authenticator para verificar a sua identidade. Para obter mais informações, consulte [iniciar sôm nas suas contas utilizando a aplicação Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-articles"></a>Artigos relacionados

- [O que é a aplicação Microsoft Authenticator?](user-help-auth-app-overview.md)

- [FAQ da aplicação Microsoft Authenticator](user-help-auth-app-faq.md)

- [Multi-factor Authentication](/azure/multi-factor-authentication/)