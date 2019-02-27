---
title: Cópia de segurança e recuperar com a aplicação Microsoft Authenticator - Azure Active Directory | Documentos da Microsoft
description: Saiba como fazer backup e recuperar suas credenciais da conta a utilizar a aplicação Microsoft Authenticator.
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
ms.openlocfilehash: 3b8a06276bcaa989fa04f105ac7858c25ab4d0ef
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887510"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Cópia de segurança e recuperar credenciais de conta com a aplicação Microsoft Authenticator

**Aplica-se a:**

- dispositivos iOS

A aplicação Microsoft Authenticator faz uma cópia de segurança de credenciais da conta e as definições de aplicação relacionados, como a ordem das suas contas, para a cloud. Após a cópia de segurança, também pode utilizar a aplicação para recuperar as informações num novo dispositivo, evitando potencialmente obter bloqueado out ou ter de recriar as contas.

>[!IMPORTANT]
> Precisa de uma conta Microsoft pessoal e uma conta do iCloud para cada localização de armazenamento de cópia de segurança. Mas dentro desse local de armazenamento, pode criar cópias de segurança várias contas. Por exemplo, pode ter uma conta pessoal, uma conta da instituição de ensino e uma conta de terceiros, como o Facebook, Google e assim por diante.<br><br>Apenas as credenciais da conta pessoal e 3rd party são armazenadas, que inclui o nome de utilizador e o código de verificação de conta é necessária para provar a sua identidade. Não armazenamos quaisquer outras informações associadas com as suas contas, incluindo mensagens de correio eletrónico ou ficheiros. Também não associar nem partilhar as suas contas de qualquer forma, ou com qualquer outro produto ou serviço. E, finalmente, o administrador de TI não receberão qualquer informação sobre qualquer uma destas contas.

## <a name="back-up-your-account-credentials"></a>Criar cópias de segurança as credenciais da conta
Antes de pode criar cópias de segurança as suas credenciais, tem de ter ambos:

- Pessoal [conta Microsoft](https://account.microsoft.com/account) para atuar como a sua conta de recuperação.

- Uma [conta do iCloud](https://www.icloud.com/) para a localização de armazenamento real. 

Exigir que iniciar sessão ambas as contas em conjunto proporciona uma segurança reforçada para sua informação de cópia de segurança.

**Para ativar cópia de segurança na Cloud**
-   No seu dispositivo iOS, selecione **configurações**, selecione **cópia de segurança**e, em seguida, ative **cópia de segurança de iCloud**.

    As credenciais da conta são uma cópia de segurança à sua conta do iCloud.

    ![ecrã de definições do iOS, que mostra a localização do iCloud as definições de cópia de segurança](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Recuperar as credenciais da conta no seu dispositivo novo
Pode recuperar as credenciais da conta da conta do iCloud, com a mesma conta de recuperação do Microsoft que configurou quando criou cópias suas informações de segurança.

### <a name="to-recover-your-information"></a>Para recuperar as informações
1.  No seu dispositivo iOS, abra a aplicação Microsoft Authenticator e selecione **iniciar a recuperação** na parte inferior do ecrã.

    ![Aplicação Microsoft Authenticator, que mostra onde clicar Begin recuperação](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2.  Inicie sessão sua conta de recuperação, com a mesma conta Microsoft pessoal que utilizou durante o processo de cópia de segurança.

    As credenciais da conta são recuperadas para o novo dispositivo.

Depois de concluir a recuperação, pode observar que os códigos de verificação de conta Microsoft pessoas na aplicação Microsoft Authenticator são diferentes entre os telefones antigos e novos. Os códigos são diferentes porque cada dispositivo tem seu próprio credencial exclusivo, mas ambos são válidos e de trabalho ao iniciar sessão utilizando o telefone associado.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Recuperar contas adicionais da necessidade de mais confirmações
Se utilizar notificações push com o seu pessoal, contas profissionais ou escolares, receberá um alerta no ecrã que diz que tem de fornecer verificação adicional para poder recuperar suas informações. Uma vez que as notificações push requerem utilizando uma credencial que tenha associado ao seu dispositivo específico e nunca são enviadas através da rede, tem de provar sua identidade antes da credencial é criada no seu dispositivo.

Para contas Microsoft pessoais, pode provar a sua identidade ao introduzir a palavra-passe, juntamente com um número de e-mail ou telefone alternativo. Para contas profissionais ou escolares, tem de ler um código de QR que lhe foi fornecido pelo seu fornecedor de conta.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Para fornecer verificação adicional para as contas pessoais
1.  Na **contas** ecrã da aplicação Microsoft Authenticator, selecione o menu suspenso seta junto a conta que pretende recuperar.

    ![Aplicação Microsoft Authenticator, que mostra as contas disponíveis com suas setas de menu pendente associadas](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2.  Selecione **inicie sessão para recuperar**, escreva a palavra-passe e, em seguida, confirme o seu e-mail endereço ou número de telefone como verificação adicional.

    ![Aplicação Microsoft Authenticator, permitindo-lhe introduzir as suas informações de início de sessão](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Para fornecer verificação adicional para contas escolares ou profissionais
1.  Na **contas** ecrã da aplicação Microsoft Authenticator, selecione o menu suspenso seta junto a conta que pretende recuperar.

    ![Aplicação Microsoft Authenticator, que mostra as contas disponíveis com suas setas de menu pendente associadas](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2.  Selecione **código QR de análise para recuperar**e, em seguida, verifique o código QR.

    ![Aplicação Microsoft Authenticator, permitindo-lhe analisar o código QR](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Para mais informações sobre como obter um código QR, veja [começar com a aplicação Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) ou [definir informações de segurança para utilizar uma aplicação de autenticação](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app), com base nos se o seu administrador tiver ativado a informações de segurança.

## <a name="troubleshooting-backup-and-recovery-problems"></a>Resolução de problemas de cópia de segurança e recuperação
Há alguns motivos por que a cópia de segurança pode não estar disponível:

-   **Alterar os sistemas operativos.** A cópia de segurança é armazenada na opção de armazenamento na cloud fornecida pelo sistema de operativo do seu telemóvel, o que significa que a cópia de segurança não está disponível se alternar entre Android e iOS. Nesta situação, tem de recriar manualmente a sua conta na aplicação.

-   **Problemas de rede ou a palavra-passe.** Certifique-se de que está ligado a uma rede e iniciado sessão na sua conta iCloud usando o mesmo ID de Apple que utilizou no último iPhone.

-   **Eliminação acidental.** É possível que tenha eliminado a sua conta de cópia de segurança do seu dispositivo anterior ou ao gerir a sua conta de armazenamento na cloud. Nesta situação, tem de recriar manualmente a sua conta na aplicação.

-   **Contas existentes do Microsoft Authenticator.** Se já tiver configurado a contas na aplicação Microsoft Authenticator, a aplicação não será possível recuperar as suas contas de cópia de segurança. Recuperação impedindo que ajuda a garantir que os detalhes da sua conta não são substituídos com informações Desatualizadas. Nesta situação, tem de remover quaisquer informações de conta existente das contas existentes que configurar na sua aplicação de autenticador para poder recuperar a cópia de segurança.

## <a name="next-steps"></a>Passos Seguintes
Agora que ter uma cópia de segurança e recuperar as credenciais da conta para o seu dispositivo novo, pode continuar a utilizar a aplicação Microsoft Authenticator para verificar a sua identidade.

## <a name="related-topics"></a>Tópicos relacionados
- [Introdução à aplicação Microsoft Authenticator](user-help-auth-app-download-install.md)  
- [FAQ da aplicação Microsoft Authenticator](user-help-auth-app-faq.md)

- [Multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
