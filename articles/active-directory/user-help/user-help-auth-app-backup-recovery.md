---
title: Fazer backup e recuperar com Microsoft Authenticator app-Azure Active Directory | Microsoft Docs
description: Saiba como fazer backup e recuperar as credenciais de sua conta usando o aplicativo Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4148d8a3b44336677ba028807aadbae424b7223
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382515"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Fazer backup e recuperar credenciais de conta com o aplicativo Microsoft Authenticator

**Aplica-se a:**

- dispositivos iOS

O aplicativo Microsoft Authenticator faz backup de suas credenciais de conta e configurações de aplicativo relacionadas, como a ordem de suas contas, para a nuvem. Após o backup, você também pode usar o aplicativo para recuperar suas informações em um novo dispositivo, potencialmente evitando o bloqueio ou a recriação de contas.

> [!IMPORTANT]
> Você precisa de um conta Microsoft pessoal e uma conta do iCloud para cada local de armazenamento de backup. Mas, dentro desse local de armazenamento, você pode fazer backup de várias contas. Por exemplo, você pode ter uma conta pessoal, uma conta de estudante e uma conta de terceiros, como o Facebook, o Google e assim por diante.
>
> Somente suas credenciais de conta pessoal e de terceiros são armazenadas, o que inclui seu nome de usuário e o código de verificação de conta que é necessário para provar sua identidade. Não armazenamos nenhuma outra informação associada às suas contas, incluindo emails ou arquivos. Também não associamos ou compartilhamos suas contas de forma alguma ou com qualquer outro produto ou serviço. E, finalmente, o administrador de ti não obterá nenhuma informação sobre nenhuma dessas contas.

## <a name="back-up-your-account-credentials"></a>Fazer backup de suas credenciais de conta

Antes de você poder fazer backup de suas credenciais, o deve ter ambos:

- Um [conta Microsoft](https://account.microsoft.com/account) pessoal para atuar como sua conta de recuperação.

- Uma [conta do icloud](https://www.icloud.com/) para o local de armazenamento real.

Exigir que você entre em ambas as contas juntos fornece segurança mais forte para suas informações de backup.

### <a name="to-turn-on-cloud-backup"></a>Para ativar o backup na nuvem

- Em seu dispositivo iOS, selecione **configurações**, selecione **backup**e, em seguida, ative o **backup do icloud**.

    Suas credenciais de conta são submetidas a backup em sua conta do iCloud.

    ![tela de configurações do iOS, mostrando o local das configurações de backup do iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Recuperar suas credenciais de conta no novo dispositivo

Você pode recuperar suas credenciais de conta de sua conta do iCloud, usando a mesma conta de recuperação da Microsoft que você configurou ao fazer backup de suas informações.

### <a name="to-recover-your-information"></a>Para recuperar suas informações

1. Em seu dispositivo iOS, abra o aplicativo Microsoft Authenticator e selecione **Iniciar recuperação** na parte inferior da tela.

    ![Microsoft Authenticator aplicativo, mostrando onde clicar em Iniciar recuperação](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Entre em sua conta de recuperação usando as mesmas conta Microsoft pessoais usadas durante o processo de backup.

    Suas credenciais de conta são recuperadas para o novo dispositivo.

Depois de concluir a recuperação, você pode observar que seus códigos de verificação pessoais de conta Microsoft no aplicativo Microsoft Authenticator são diferentes entre os telefones novos e antigos. Os códigos são diferentes porque cada dispositivo tem sua própria credencial exclusiva, mas ambos são válidos e funcionam enquanto se conectam usando o telefone associado.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Recuperar contas adicionais que exigem mais verificação

Se você usar notificações por push com suas contas pessoais, corporativas ou de estudante, obterá um alerta na tela que diz que você deve fornecer verificação adicional antes de recuperar suas informações. Como as notificações por push exigem o uso de uma credencial vinculada ao seu dispositivo específico e nunca enviada pela rede, você deve provar sua identidade antes que a credencial seja criada em seu dispositivo.

Para contas pessoais da Microsoft, você pode provar sua identidade inserindo sua senha junto com um email ou número de telefone alternativo. Para contas corporativas ou de estudante, você deve digitalizar um código QR fornecido por seu provedor de conta.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Para fornecer verificação adicional para contas pessoais

1. Na tela **contas** do aplicativo Microsoft Authenticator, selecione a seta suspensa ao lado da conta que você deseja recuperar.

    ![Microsoft Authenticator aplicativo, mostrando as contas disponíveis com as setas suspensas associadas](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2. Selecione **entrar para recuperar**, digite sua senha e confirme seu endereço de email ou número de telefone como verificação adicional.

    ![Microsoft Authenticator aplicativo, permitindo que você insira suas informações de entrada](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Para fornecer verificação adicional para contas corporativas ou de estudante

1. Na tela **contas** do aplicativo Microsoft Authenticator, selecione a seta suspensa ao lado da conta que você deseja recuperar.

    ![Microsoft Authenticator aplicativo, mostrando as contas disponíveis com as setas suspensas associadas](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2. Selecione **digitalizar código QR para recuperar**e, em seguida, digitalizar o código QR.

    ![Microsoft Authenticator aplicativo, permitindo que você examine seu código QR](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Para obter mais informações sobre como obter um código QR, consulte Introdução ao [aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) ou [configurar informações de segurança para usar um aplicativo autenticador](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app), com base em se o administrador ativou as informações de segurança.

## <a name="troubleshooting-backup-and-recovery-problems"></a>Solucionando problemas de backup e recuperação

Há alguns motivos pelos quais o backup pode não estar disponível:

- **Alterando sistemas operacionais.** O backup é armazenado na opção de armazenamento em nuvem fornecida pelo sistema operacional do seu telefone, o que significa que o backup não estará disponível se você alternar entre Android e iOS. Nessa situação, você deve recriar manualmente sua conta no aplicativo.

- **Problemas de rede ou senha.** Verifique se você está conectado a uma rede e entrou em sua conta do iCloud usando o mesmo Appleid usado no seu último iPhone.

- **Exclusão acidental.** É possível que você tenha excluído sua conta de backup do dispositivo anterior ou enquanto gerencia sua conta de armazenamento em nuvem. Nessa situação, você deve recriar manualmente sua conta no aplicativo.

- **Contas existentes do Microsoft Authenticator.** Se você já configurou contas no aplicativo Microsoft Authenticator, o aplicativo não poderá recuperar suas contas de backup. Impedir a recuperação ajuda a garantir que os detalhes da conta não sejam substituídos por informações desatualizadas. Nessa situação, você deve remover todas as informações de conta existentes das contas existentes configuradas em seu aplicativo autenticador antes de recuperar o backup.

## <a name="next-steps"></a>Passos Seguintes

Agora que você fez backup e recuperou suas credenciais de conta para o novo dispositivo, você pode continuar a usar o aplicativo Microsoft Authenticator para verificar sua identidade. Para obter mais informações, consulte [entrar em suas contas usando o aplicativo Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-topics"></a>Tópicos relacionados

- [O que é o aplicativo Microsoft Authenticator?](user-help-auth-app-overview.md)

- [FAQ da aplicação Microsoft Authenticator](user-help-auth-app-faq.md)

- [Multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
