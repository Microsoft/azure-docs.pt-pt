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
ms.openlocfilehash: 2fc4f26f187301ea7a7a1e3051038f75da728547
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125369"
---
## <a name="generate-the-certificate-signing-request-file"></a>Gerar o ficheiro de pedido de assinatura de certificado

O Serviço Apple Push Notification (APNS) utiliza certificados para autenticar as suas notificações push. Siga estas instruções para criar o certificado push necessário para enviar e receber notificações. Para obter mais informações sobre estes conceitos, veja a documentação oficial do [Serviço Apple Push Notification](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Crie o ficheiro de Solicitação de Assinatura do Certificado (CSR), que é utilizado pela Apple para gerar um certificado push assinado.

1. No Mac, execute a ferramenta **Acesso Keychain**. Pode ser aberta a partir da pasta **Utilitários** ou da pasta **Outros** no painel de arranque.
2. Clique em **Acesso Keychain**, expanda **Assistente de Certificado** e, em seguida, clique em **Pedir um Certificado a uma Autoridade de Certificação...**

    ![Utilizar o Acesso Keychain para pedir um novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Selecione o **Endereço de E-mail do Utilizador** e o **Nome Comum**. Verifique se **Guardar no disco** está selecionado e clique em **Continuar**. Deixe o campo **Endereço de E-mail da AC** em branco, uma vez que não é necessário.

    ![Informações de certificado obrigatórias](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Escreva um nome para o ficheiro de Solicitação de Assinatura do Certificado (CSR) em **Guardar Como**, selecione a localização em **Onde** e clique em **Guardar**.

    ![Escolher um nome de ficheiro para o certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Esta ação guarda o ficheiro CSR na localização selecionada; a localização predefinida é no Ambiente de Trabalho. Memorize a localização escolhida para o ficheiro.

Em seguida, registe a aplicação na Apple, ative as notificações push e carregue o CSR exportado para criar um certificado push.

## <a name="register-your-app-for-push-notifications"></a>Registar a aplicação para notificações push

Para poder enviar notificações push para uma aplicação iOS, tem de registar a sua aplicação na Apple e registar-se também para notificações push.  

1. Se ainda não registou a aplicação, navegue até ao [Portal de Aprovisionamento do iOS ](https://go.microsoft.com/fwlink/p/?LinkId=272456) no Centro de Programadores da Apple, inicie a sessão com o seu ID da Apple, clique em **Identificadores**, clique em **IDs de Aplicações** e, finalmente, clique no sinal **+** para registar uma nova aplicação.

    ![Página de IDs de Aplicação do Portal de Aprovisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Atualize os três campos seguintes para a nova aplicação e clique em **Continuar**:

   * **Nome**: Escreva um nome descritivo para a sua aplicação no **Name** campo na **descrição do ID da aplicação** secção.
   * **Identificador do pacote**: Sob o **ID de aplicação explícito** , digite um **identificador do pacote** sob a forma `<Organization Identifier>.<Product Name>` conforme mencionado na [guia de distribuição de aplicações](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). O *Identificador da Organização* e o *Nome do Produto* que utilizar têm de corresponder ao identificador da organização e ao nome do produto que irá utilizar quando criar o projeto XCode. Na captura de ecrã seguinte, utiliza-se *NotificationHubs* como identificador de uma organização e *GetStarted* como o nome do produto. A utilização do mesmo valor que irá utilizar no projeto XCode permite utilizar o perfil de publicação correto com o XCode.
   * **Notificações push**: Verifique os **notificações Push** opção a **dos serviços de aplicações** secção.

     ![Formulário para registar um novo ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     Esta ação gera o ID da Aplicação e pede-lhe que confirme as informações. Clique em **Registar** para confirmar o novo ID da Aplicação.

     Assim que clicar em **Registar**, verá o ecrã **Registo concluído**, conforme apresentado na imagem seguinte. Clique em **Concluído**.

     ![Registo de ID de aplicação completo que mostra as elegibilidades](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

3. No Centro de Programadores, em IDs de Aplicações, localize o ID da aplicação que criou e clique na respetiva linha.

    ![Lista de ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Clicar no ID da aplicação apresenta os detalhes da mesma. Clique no botão **Editar** na parte inferior.

    ![Editar a página de ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. Desloque-se para a parte inferior do ecrã e clique no botão **Criar Certificado…** na secção **Certificado SSL do Push de Programação**.

    ![Criar certificado para o botão de ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Será apresentado o assistente **Adicionar Certificado iOS**.

    > [!NOTE]
    > Este tutorial utiliza um certificado de programação. É utilizado o mesmo processo ao registar um certificado de produção. Não se esqueça de verificar se está a utilizar o mesmo tipo de certificado ao enviar notificações.

5. Clique em **Escolher Ficheiro**, navegue até à localização onde guardou o ficheiro CSR que criou na primeira tarefa e, em seguida, clique em **Gerar**.

    ![Página de carregamento do CSR de certificado gerado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. Uma vez criado o certificado pelo portal, clique no botão **Transferir** e em **Concluído**.

    ![Página de transferência do certificado gerado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Esta ação transfere o certificado e guarda-o no seu computador na pasta **Transferências**.

    ![Localizar o ficheiro de certificado na pasta Transferências](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Por predefinição, o ficheiro transferido, um certificado de desenvolvimento, tem o nome **aps_development.cer**.

7. Faça duplo clique no certificado push **aps_development.cer** transferido.

    Esta ação instala o novo certificado na Keychain, conforme apresentado na imagem seguinte:

    ![Lista de certificados de Acesso Keychain que mostra o novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > O nome do seu certificado poderá ser diferente, mas terá como prefixo **Apple Development iOS Push Services**.

8. No Acesso Keychain, clique com o botão direito do rato no novo certificado push que criou na categoria **Certificados**. Clique em **Exportar**, dê um nome ao ficheiro, selecione o formato **.p12** e clique em **Guardar**.

    ![Exportar o certificado no formato p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Tome nota do nome do ficheiro e da localização do certificado .p12 exportado. Será utilizado para ativar a autenticação com o APNs.

    > [!NOTE]
    > Este tutorial mostra como criar um ficheiro QuickStart.p12. O nome do ficheiro e a localização poderão ser diferentes.

## <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de aprovisionamento para a aplicação

1. De volta ao [Portal de Aprovisionamento do iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecione **Perfis de Aprovisionamento**, selecione **Todos** e clique no botão **+** (mais) para criar um novo perfil. Será apresentado o assistente **Adicionar Perfil de Aprovisionamento do iOS**:

    ![Lista de perfis de aprovisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Selecione **Desenvolvimento de Aplicações iOS**, em **Programação**, como o tipo de perfil de aprovisionamento e clique em **Continuar**.

3. Em seguida, selecione o ID da aplicação que criou na lista pendente **ID da Aplicação** e clique em **Continuar**.

    ![Selecionar o ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. No ecrã **Selecionar certificados**, selecione o seu certificado de programação habitual utilizado para a assinatura de código e clique em **Continuar**. Este não é o certificado push que criou.

    ![Selecionar o certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. Em seguida, selecione os **Dispositivos** a utilizar para os testes e clique em **Continuar**

    ![Selecionar os dispositivos](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. Por fim, escolha um nome para o perfil em **Nome do Perfil** e clique em **Gerar**.

    ![Escolher um nome de perfil de aprovisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

7. Após criar o novo perfil de aprovisionamento, clique para o transferir e instalar no seu computador de programação do Xcode. Em seguida, clique em **Guardar**.

    ![Transferir o perfil de aprovisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
