---
title: incluir ficheiro
description: incluir ficheiro
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/21/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: ef2b98821b28d8a49e5f16bf1c6ac176eb8b5793
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407205"
---
## <a name="generate-the-certificate-signing-request-file"></a>Gerar o arquivo de solicitação de assinatura de certificado

O Apple Push Notification Service (APNs) usa certificados para autenticar suas notificações por push. Siga estas instruções para criar o certificado push necessário para enviar e receber notificações. Para obter mais informações sobre estes conceitos, veja a documentação oficial do [Serviço Apple Push Notification](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Gerar o arquivo CSR (solicitação de assinatura de certificado), que a Apple usa para gerar um certificado Push assinado.

1. No Mac, execute a ferramenta de Acesso Keychain. Ele pode ser aberto na pasta **Utilities** ou na **outra** pasta no Launchpad.

1. Selecione **acesso**ao conjunto de chaves, expanda **Assistente de certificado**e selecione **solicitar um certificado de uma autoridade de certificação**.

    ![Utilizar o Acesso Keychain para pedir um novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. Selecione seu **endereço de email do usuário**, insira seu valor de **nome comum** , certifique-se de especificar **salvo em disco**e selecione **continuar**. Deixe o **endereço de email da autoridade de certificação** em branco, pois ele não é necessário.

    ![Informações de certificado obrigatórias](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Insira um nome para o arquivo CSR em **salvar como**, selecione o local em **onde**e, em seguida, selecione **salvar**.

    ![Escolha um nome de arquivo para o certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Essa ação salva o arquivo CSR no local selecionado. O local padrão é **Desktop**. Memorize a localização escolhida para o ficheiro.

Em seguida, Registre seu aplicativo com a Apple, habilite notificações por push e carregue o CSR exportado para criar um certificado Push.

## <a name="register-your-app-for-push-notifications"></a>Registar a aplicação para notificações push

Para enviar notificações por push para um aplicativo iOS, Registre seu aplicativo com a Apple e também Registre-se para notificações por push.  

1. Se você ainda não registrou seu aplicativo, navegue até o [portal de provisionamento do IOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) no Apple Developer Center. Entre no portal com sua ID da Apple e selecione **identificadores**. Em seguida, selecione **+** para registrar um novo aplicativo.

    ![Página de IDs de Aplicação do Portal de Aprovisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Na tela **registrar um novo identificador** , selecione o botão de opção **IDs do aplicativo** . Em seguida, selecione **Continuar**.

    ![página registrar nova ID do portal de provisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Atualize os três valores a seguir para seu novo aplicativo e selecione **continuar**:

   * **Descrição**: digite um nome descritivo para seu aplicativo.

   * **ID do pacote**: Insira uma ID do pacote do formulário **identificador da organização. nome do produto** , conforme mencionado no guia de distribuição do [aplicativo](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Os valores do *identificador da organização* e do *nome do produto* devem corresponder ao identificador da organização e ao nome do produto que você usa ao criar seu projeto do Xcode. Na captura de tela a seguir, o valor de **NotificationHubs** é usado como um identificador de organização e o valor de **getstarted** é usado como o nome do produto. Verifique se o valor do **identificador do pacote** corresponde ao valor em seu projeto do Xcode, para que o Xcode use o perfil de publicação correto.

      ![página de ID do aplicativo de registro do portal de provisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Notificações por push**: marque a opção **notificações por push** na seção **recursos** .

      ![Formulário para registar um novo ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Essa ação gera a ID do aplicativo e solicita que você confirme as informações. Selecione **continuar**e, em seguida, selecione **registrar** para confirmar a nova ID do aplicativo.

      ![Confirmar nova ID do aplicativo](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Depois de selecionar **registrar**, você verá a nova ID do aplicativo como um item de linha na página **certificados, identificadores & perfis** .

4. Na página **certificados, identificadores & perfis** , em **identificadores**, localize o item de linha ID do aplicativo que você acabou de criar e selecione sua linha para exibir a tela **editar sua configuração de ID do aplicativo** .

5. Role para baixo até a opção de **notificações por push** selecionada e, em seguida, selecione **Configurar** para criar o certificado.

    ![Editar a página de ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

6. A janela **certificados SSL do serviço de notificação por push da Apple** é exibida. Selecione o botão **criar certificado** na seção **certificado SSL de desenvolvimento** .

    ![Criar certificado para o botão de ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    A tela **criar um novo certificado** é exibida.

    > [!NOTE]
    > Este tutorial utiliza um certificado de programação. É utilizado o mesmo processo ao registar um certificado de produção. Não se esqueça de verificar se está a utilizar o mesmo tipo de certificado ao enviar notificações.

1. Selecione **escolher arquivo**, navegue até o local em que você salvou o arquivo CSR da primeira tarefa e clique duas vezes no nome do certificado para carregá-lo. Em seguida, selecione **Continuar**.

1. Depois que o portal criar o certificado, selecione o botão **baixar** . Salve o certificado e lembre-se do local no qual ele foi salvo.

    ![Página de transferência do certificado gerado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    O certificado é baixado e salvo em seu computador na pasta **downloads** .

    ![Localizar o ficheiro de certificado na pasta Transferências](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Por padrão, o certificado de desenvolvimento baixado é denominado **aps_development. cer**.

1. Faça duplo clique no certificado push **aps_development.cer** transferido. Esta ação instala o novo certificado na Keychain, conforme apresentado na imagem seguinte:

    ![Lista de certificados de Acesso Keychain que mostra o novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Embora o nome em seu certificado possa ser diferente, o nome será prefixado com os **serviços de envio por push do Ios de desenvolvimento da Apple**.

1. No Acesso Keychain, clique com o botão direito do rato no novo certificado push que criou na categoria **Certificados**. Selecione **Exportar**, nomeie o arquivo, selecione o formato **. p12** e, em seguida, selecione **salvar**.

    ![Exportar o certificado no formato p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Você pode optar por proteger o certificado com uma senha, mas isso é opcional. Clique em **OK** se desejar ignorar a criação de senha. Tome nota do nome do ficheiro e da localização do certificado .p12 exportado. Eles são usados para habilitar a autenticação com o APNs.

    > [!NOTE]
    > O nome e o local do arquivo. p12 podem ser diferentes do que foi mostrado neste tutorial.

## <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de aprovisionamento para a aplicação

1. Retorne ao [portal de provisionamento do IOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecione **certificados, identificadores & perfis**, selecione **perfis** no menu à esquerda e, em seguida, selecione **+** para criar um novo perfil. A tela **registrar um novo perfil de provisionamento** é exibida.

1. Selecione **desenvolvimento de aplicativo do IOS** em **desenvolvimento** como o tipo de perfil de provisionamento e selecione **continuar**.

    ![Lista de perfis de aprovisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Em seguida, selecione a ID do aplicativo que você criou na lista suspensa **ID do aplicativo** e selecione **continuar**.

    ![Selecionar o ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Na janela **selecionar certificados** , selecione o certificado de desenvolvimento que você usa para a assinatura de código e selecione **continuar**. Esse certificado não é o certificado push que você criou. Se não houver um, você deverá criá-lo. Se houver um certificado, pule para a próxima etapa. Para criar um certificado de desenvolvimento, se ele não existir:

    1. Se você **não vir nenhum certificado disponível**, selecione **criar certificado**.
    2. Na seção **software** , selecione **Apple Development**. Em seguida, selecione **Continuar**.
    3. Na tela **criar um novo certificado** , selecione **escolher arquivo**.
    4. Navegue até o certificado de **solicitação de assinatura de certificado** criado anteriormente, selecione-o e, em seguida, selecione **abrir**.
    5. Selecione **Continuar**.
    6. Baixe o certificado de desenvolvimento e lembre-se do local no qual ele foi salvo.

1. Retorne à página **certificados, identificadores & perfis** , selecione **perfis** no menu à esquerda e, em seguida, selecione **+** para criar um novo perfil. A tela **registrar um novo perfil de provisionamento** é exibida.

1. Na janela **selecionar certificados** , selecione o certificado de desenvolvimento que você acabou de criar. Em seguida, selecione **Continuar**.

1. Em seguida, selecione os dispositivos a serem usados para teste e selecione **continuar**.

1. Por fim, escolha um nome para o perfil em **nome do perfil de provisionamento**e selecione **gerar**.

    ![Escolher um nome de perfil de aprovisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Quando o novo perfil de provisionamento for criado, selecione **baixar**. Lembre-se do local no qual ele foi salvo.

1. Navegue até o local do perfil de provisionamento e clique duas vezes nele para instalá-lo em seu computador de desenvolvimento do Xcode.

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

Nesta seção, você cria um hub de notificação e configura a autenticação com o APNs usando o certificado Push. p12 que você criou anteriormente. Se você quiser usar um hub de notificação que já tenha criado, poderá pular para a etapa 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Configurar o Hub de notificação com informações de APNs

1. Em **Serviços de Notificação**, selecione **Apple (APNS)** .

1. Selecione **Certificado**.

1. Selecione o ícone arquivo.

1. Selecione o arquivo. p12 que você exportou anteriormente e, em seguida, selecione **abrir**.

1. Se necessário, especifique a senha correta.

1. Selecione o modo **Sandbox**. Utilize o modo **Produção** apenas se quiser enviar notificações push a utilizadores que já tenham adquirido a aplicação na loja.

    ![Configurar certificação APNs no portal do Azure](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Selecione **Guardar**.

Agora você configurou seu hub de notificação com o APNs. Você também tem as cadeias de conexão para registrar seu aplicativo e enviar notificações por push.
