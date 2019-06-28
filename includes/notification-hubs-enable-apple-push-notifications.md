---
title: incluir ficheiro
description: incluir ficheiro
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 3e4549a21ec32f1a2c1c869c3b2e0bd8c2e4204e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446574"
---
## <a name="generate-the-certificate-signing-request-file"></a>Gerar o ficheiro de pedido de assinatura de certificado

Serviço Apple Push Notification (APNs) utiliza certificados para autenticar as notificações push. Siga estas instruções para criar o certificado push necessário para enviar e receber notificações. Para obter mais informações sobre estes conceitos, veja a documentação oficial do [Serviço Apple Push Notification](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Gere o ficheiro de certificado de assinatura do pedido (CSR), que utiliza a Apple para gerar um certificado push assinado.

1. No Mac, execute a ferramenta de Acesso Keychain. Pode ser aberta a partir da **utilitários** pasta ou o **outros** pasta em que o ponto de partida.

1. Selecione **acesso Keychain**, expanda **Assistente de certificado**e, em seguida, selecione **pedir um certificado de uma autoridade de certificação**.

    ![Utilizar o Acesso Keychain para pedir um novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. Selecione seu **endereço de E-Mail do utilizador**, introduza o seu **nome comum** valor, certifique-se de que especifica **guardar no disco**e, em seguida, selecione **continuar**. Deixe **endereço de E-Mail de AC** em branco como não é necessária.

    ![Informações de certificado obrigatórias](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Introduza um nome para o ficheiro CSR **guardar como**, selecione a localização na **onde**e, em seguida, selecione **guardar**.

    ![Escolha um nome de ficheiro para o certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Esta ação guarda o ficheiro CSR na localização selecionada. A localização predefinida é **Desktop**. Memorize a localização escolhida para o ficheiro.

Em seguida, registar a sua aplicação com a Apple, ative as notificações push e carregar o CSR exportado para criar um certificado push.

## <a name="register-your-app-for-push-notifications"></a>Registar a aplicação para notificações push

Enviar notificações push para aplicações iOS, registar a sua aplicação com a Apple e também se registrar para notificações push.  

1. Se ainda não registou a aplicação, navegue para o [Portal de aprovisionamento do iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) no Apple Developer Center. Depois disso, inicie sessão com o seu ID Apple, selecione **identificadores**, selecione **IDs de aplicações**e, finalmente, selecione **+** para registar uma nova aplicação.

    ![Página de IDs de Aplicação do Portal de Aprovisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Atualizar os seguintes três valores para a nova aplicação e, em seguida, selecione **continuar**:

   * **Nome**: Escreva um nome descritivo para a sua aplicação no **Name** caixa **descrição do ID da aplicação** secção.

   * **Identificador do pacote**: Na **ID de aplicação explícito** , digite um **identificador do pacote** do formulário `<Organization Identifier>.<Product Name>` conforme mencionado na [guia de distribuição de aplicações](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). O *identificador de organização* e *nome do produto* valores têm de corresponder o nome de produto e o identificador de organização que utiliza quando cria o projeto Xcode. Captura de ecrã seguinte, o *NotificationHubs* valor é utilizado como um identificador de organização e o *GetStarted* valor é utilizado como o nome do produto. Certifique-se de que o **identificador de pacote** valor corresponde ao valor no projeto Xcode, para que o Xcode irá utilizar o perfil de publicação correto.

   * **Notificações push**: Verifique os **notificações Push** opção a **dos serviços de aplicações** secção.

     ![Formulário para registar um novo ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     Esta ação gera o ID da aplicação e que está a confirmar as informações de pedidos. Selecione **registar** para confirmar o novo ID de aplicação.

     Depois de selecionar **registar**, verá o **registo concluído** ecrã conforme mostrado na imagem seguinte. Selecione **Done** (Concluído).

     ![Registo de ID de aplicação completo que mostra as elegibilidades](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

1. No Centro de programadores, sob **IDs de aplicações**, localize o ID da aplicação que criou e selecione a sua linha.

    ![Lista de ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Selecione o ID da aplicação para apresentar os detalhes da aplicação e, em seguida, selecione o **editar** na parte inferior.

    ![Editar a página de ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Desloque-se para a parte inferior do ecrã e selecione o **Criar certificado** botão sob os **certificado SSL do Push desenvolvimento** secção.

    ![Criar certificado para o botão de ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Verá o **adicionar certificado iOS** assistente.

    > [!NOTE]
    > Este tutorial utiliza um certificado de programação. É utilizado o mesmo processo ao registar um certificado de produção. Não se esqueça de verificar se está a utilizar o mesmo tipo de certificado ao enviar notificações.

1. Selecione **Escolher ficheiro**, navegue até à localização onde guardou o ficheiro CSR na primeira tarefa e, em seguida, selecione **gerar**.

    ![Página de carregamento do CSR de certificado gerado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

1. Depois do portal cria o certificado, selecione o **baixe** e, em seguida, selecione **feito**.

    ![Página de transferência do certificado gerado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    O certificado é transferido e guardado no seu computador na sua **Downloads** pasta.

    ![Localizar o ficheiro de certificado na pasta Transferências](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Por predefinição, o certificado de desenvolvimento transferido é denominado **aps_development**.

1. Selecione o certificado push transferido **aps_development**.

    Esta ação instala o novo certificado na Keychain, conforme apresentado na imagem seguinte:

    ![Lista de certificados de Acesso Keychain que mostra o novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Embora o nome do seu certificado poderá ser diferente, o nome terá o prefixo **Apple Development iOS Push Services**.

1. No Acesso Keychain, clique com o botão direito do rato no novo certificado push que criou na categoria **Certificados**. Selecione **exportar**, dê um nome de ficheiro, selecione a **p12** formatar e, em seguida, selecione **guardar**.

    ![Exportar o certificado no formato p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Tome nota do nome do ficheiro e da localização do certificado .p12 exportado. Eles são usados para ativar a autenticação com APNs.

    > [!NOTE]
    > Este tutorial cria um ficheiro denominado **QuickStart.p12**. O nome do ficheiro e a localização poderão ser diferentes.

## <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de aprovisionamento para a aplicação

1. Na [Portal de aprovisionamento do iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecione **perfis de aprovisionamento**, selecione **todos os**e, em seguida, selecione **+** para criar um novo perfil. Verá o **adicionar perfil de aprovisionamento do iOS** assistente.

    ![Lista de perfis de aprovisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Selecione **desenvolvimento de aplicações iOS** sob **desenvolvimento** como o aprovisionamento de tipo de perfil e selecione **continuar**.

1. Em seguida, selecione o ID da aplicação que criou a partir da **ID da aplicação** na lista pendente e selecione **continuar**.

    ![Selecionar o ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Na **selecionar certificados** janela, selecione o seu certificado de programação habitual utilizado para assinatura de código e selecione **continuar**. Este certificado não é o certificado de push que criou.

    ![Selecionar o certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

1. Em seguida, selecione os dispositivos para utilizar em testes e selecione **continuar**.

    ![Selecionar os dispositivos](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

1. Por fim, escolha um nome para o perfil em **nome do perfil**e selecione **gerar**.

    ![Escolher um nome de perfil de aprovisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Quando é criado o novo perfil de aprovisionamento, optar por baixar e instalá-lo no seu computador de desenvolvimento do Xcode. Em seguida, selecione **Done** (Concluído).

    ![Transferir o perfil de aprovisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

Nesta secção, criar um hub de notificação e configurar a autenticação com APNs utilizando o certificado de push. p12 que criou anteriormente. Se pretender utilizar um notification hub que já tenha criado, pode avançar para o passo 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Configurar o notification hub com informações do APNs

1. Em **Serviços de Notificação**, selecione **Apple (APNS)** .

1. Selecione **Certificado**.

1. Selecione o ícone de ficheiro.

1. Selecione o ficheiro. p12 que exportou anteriormente.

1. Especifique a palavra-passe correta.

1. Selecione o modo **Sandbox**. Utilize o modo **Produção** apenas se quiser enviar notificações push a utilizadores que já tenham adquirido a aplicação na loja.

    ![Configurar certificação APNs no portal do Azure](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

Agora, configurou o hub de notificação com o APNs. Também tem as cadeias de ligação para registar a sua aplicação e enviar notificações push.
