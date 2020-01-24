---
title: Backup e recuperar contas com a app Microsoft Authenticator - Azure AD
description: Aprenda a fazer back up e a recuperar as suas credenciais de conta apoiadas, utilizando a aplicação Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: 8d4c3d76a1ff7c3fcedbb4d2c22a699f4a2e77d0
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704787"
---
# <a name="backup-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Backup e recuperar credenciais de conta usando a aplicação Microsoft Authenticator

**Aplica-se a:**

- dispositivos iOS, executando a versão 5.7.0 e mais tarde

- Dispositivos Android, com a versão 6.6.0 e mais tarde

A aplicação Microsoft Authenticator repõe as credenciais da sua conta e as definições de aplicações relacionadas, como a ordem das suas contas, para a nuvem. Após o backup, você também pode usar o aplicativo para recuperar suas informações em um novo dispositivo, potencialmente evitando o bloqueio ou a recriação de contas.

Cada local de armazenamento de backup requer que tenha uma conta pessoal da Microsoft, enquanto o iOS também requer que tenha uma conta iCloud. Pode ter várias contas armazenadas nesse único local. Por exemplo, pode ter uma conta pessoal, uma conta de trabalho ou escola, e uma conta pessoal, não Microsoft, como para o Facebook, Google, e assim por diante.

> [!IMPORTANT]
> Apenas as credenciais de conta pessoal e de terceiros são armazenadas, o que inclui o seu nome de utilizador e o código de verificação da conta que é necessário para provar a sua identidade. Não armazenamos nenhuma outra informação associada às suas contas, incluindo e-mails ou ficheiros. Também não associamos ou partilhamos as suas contas de qualquer forma ou com qualquer outro produto ou serviço. E finalmente, o seu administrador de TI não vai obter nenhuma informação sobre nenhuma destas contas.

## <a name="back-up-your-account-credentials"></a>Recue as credenciais da sua conta

Antes de poder fazer o reforço das suas credenciais, deve ter:

- Uma [conta](https://account.microsoft.com/account) pessoal da Microsoft para funcionar como a sua conta de recuperação.

- **Apenas para iOS,** você deve ter uma [conta iCloud](https://www.icloud.com/) para o local de armazenamento real.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Para ativar a cópia de segurança em nuvem para dispositivos iOS

- No seu dispositivo iOS, selecione **Definições**, selecione **Backup**, e, em seguida, ligue a cópia de **segurança do iCloud**.

    As credenciais da sua conta estão apoiadas na sua conta iCloud.

    ![ecrã de definições do iOS, mostrando a localização das definições de backup do iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Para ativar a cópia de segurança em nuvem para dispositivos Android

- No seu dispositivo Android, selecione **Definições**, selecione **Backup**, e depois ligue a cópia de **segurança Cloud**.

    As credenciais da sua conta estão apoiadas na sua conta na nuvem.

    ![Ecrã de configurações do Android, mostrando a localização das definições de backup](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Recupere as credenciais da sua conta no seu novo dispositivo

Pode recuperar as credenciais da sua conta na nuvem, mas primeiro tem de se certificar de que a conta que está a recuperar não existe na aplicação Microsoft Authenticator. Por exemplo, se estiver a recuperar a sua conta pessoal da Microsoft, tem de se certificar de que não tem uma conta pessoal da Microsoft já configurada na aplicação autenticadora. Este cheque é importante para termos a certeza de que não estamos a sobrepor-nos ou a apagar uma conta existente por engano.

### <a name="to-recover-your-information"></a>Para recuperar a sua informação

1. No seu dispositivo móvel, abra a aplicação Microsoft Authenticator e selecione Iniciar a **recuperação** a partir da parte inferior do ecrã.

    ![Aplicação Microsoft Authenticator, mostrando onde clicar em Iniciar a recuperação](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Inscreva-se na sua conta de recuperação, utilizando a mesma conta pessoal da Microsoft que utilizou durante o processo de backup.

    As credenciais da sua conta são recuperadas para o novo dispositivo.

Depois de terminar a sua recuperação, poderá notar que os códigos pessoais de verificação da conta microsoft na aplicação Microsoft Authenticator são diferentes entre os seus antigos e novos telefones. Os códigos são diferentes porque cada dispositivo tem a sua própria credencial única, mas ambos são válidos e funcionam ao assinar na utilização do telefone associado.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Recuperar contas adicionais que requerem mais verificação

Se utilizar notificações push com as suas contas pessoais ou laborais ou escolares, receberá um alerta no ecrã que diz que deve fornecer verificação adicional antes de poder recuperar as suas informações. Como as notificações push requerem a utilização de uma credencial que está ligada ao seu dispositivo específico e nunca enviada através da rede, tem de provar a sua identidade antes de a credencial ser criada no seu dispositivo.

Para contas pessoais da Microsoft, pode provar a sua identidade inserindo a sua palavra-passe juntamente com um e-mail ou número de telefone alternativo. Para contas de trabalho ou de escola, deve digitalizar um código QR que lhe é dado pelo seu fornecedor de conta.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Para fornecer verificação adicional para contas pessoais

1. No ecrã **contas** da aplicação Microsoft Authenticator, selecione a seta de drop-down ao lado da conta que pretende recuperar.

    ![Aplicação Microsoft Authenticator, mostrando as contas disponíveis com as suas setas de entrega associadas](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2. Selecione **Iniciar sessão para recuperar,** escrever a sua palavra-passe e, em seguida, confirmar o seu endereço de e-mail ou número de telefone como verificação adicional.

    ![Aplicação Microsoft Authenticator, permitindo-lhe introduzir a sua informação de entrada](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Para fornecer verificação adicional para o trabalho ou contas escolares

1. No ecrã **contas** da aplicação Microsoft Authenticator, selecione a seta de drop-down ao lado da conta que pretende recuperar.

    ![Aplicação Microsoft Authenticator, mostrando as contas disponíveis com as suas setas de entrega associadas](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2. Selecione o **código QR de digitalização para recuperar**e, em seguida, digitalizar o código QR.

    ![Aplicação Microsoft Authenticator, permitindo-lhe digitalizar o seu código QR](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Para obter mais informações sobre códigos QR e como obter um, consulte Iniciar com [a aplicação Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) ou [configurar informações de segurança para utilizar uma aplicação autenticadora](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app), com base em se o seu administrador ligou informações de segurança.
    >
    >Se esta for a primeira vez que você estiver configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou permitir que o aplicativo tire fotos e grave vídeo (Android). Você deve selecionar **permitir** para que o aplicativo autenticador possa acessar sua câmera para tirar uma imagem do código QR na próxima etapa. Se você não permitir a câmera, ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, consulte [adicionar manualmente uma conta ao aplicativo](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Problemas de backup e recuperação

Existem algumas razões pelas quais o seu backup pode não estar disponível:

- **A mudar os sistemas operativos.** A sua cópia de segurança está armazenada no iCloud para iOS e no fornecedor de armazenamento em nuvem da Microsoft para Android. Isto significa que a sua cópia de segurança não está disponível se alternar entre dispositivos Android e iOS. Se fizer o interruptor, terá de recriar manualmente as suas contas dentro da aplicação Microsoft Authenticator.

- **Problemas de rede.** Se estiver a ter problemas relacionados com a rede, certifique-se de que está ligado à rede e inscreveu-se corretamente na sua conta.

- **Problemas de conta.** Se estiver com problemas relacionados com contas, certifique-se de que está devidamente inscrito na sua conta. Para iOS isto significa que você deve ser assinado no iCloud usando a mesma conta AppleID que o seu iPhone.

- **Supressão acidental.** É possível que tenha apagado a sua conta de backup do seu dispositivo anterior ou enquanto geria a sua conta de armazenamento na nuvem. Nesta situação, deve recriar manualmente a sua conta dentro da app.

- **As contas existentes do Autenticador microsoft.** Se já criou contas na aplicação Microsoft Authenticator, a aplicação não poderá recuperar as suas contas de back-up. Impedir a recuperação ajuda a garantir que os detalhes da sua conta não são substituídos com informações desatualizadas. Nesta situação, deve remover todas as informações de conta existentes das contas existentes configuradas na sua aplicação Autenticadorantes de poder recuperar a sua cópia de segurança.

- **O reforço está desatualizado.** Se as suas informações de backup estiverem desatualizadas, poderá ser-lhe pedido que refresque a informação, inserindo novamente na sua conta de Recuperação microsoft. A sua conta de recuperação é a conta pessoal da Microsoft que usou inicialmente para armazenar a sua cópia de segurança. Se for necessário um sessão, verá um ponto vermelho no seu menu ou barra de ação. Depois de selecionar o ponto vermelho, será solicitado que volte a iniciar sessão para atualizar as suas informações.

## <a name="next-steps"></a>Passos seguintes

Agora que já reduziu e recuperou as credenciais da sua conta para o seu novo dispositivo, pode continuar a utilizar a aplicação Microsoft Authenticator para verificar a sua identidade. Para mais informações, consulte [o Sessão nas suas contas utilizando a aplicação Microsoft Authenticator](user-help-sign-in.md).

## <a name="related-articles"></a>Artigos relacionados

- [O que é a aplicação Microsoft Authenticator?](user-help-auth-app-overview.md)

- [FAQ da aplicação Microsoft Authenticator](user-help-auth-app-faq.md)

- [Multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
