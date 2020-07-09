---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72065ce602c6d29255a3500e26d8c6f36a19ebed
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081555"
---
### <a name="register-your-ios-app-for-push-notifications"></a>Registe a sua aplicação iOS para notificações push

Para enviar notificações push para uma aplicação iOS, registe a sua aplicação com a Apple e registe-se também para notificações push.  

1. Se ainda não registou a sua aplicação, consulte o [Portal de Provisionamento](https://go.microsoft.com/fwlink/p/?LinkId=272456) do iOS no Apple Developer Center. Inscreva-se no portal com o seu Apple ID, navegue para **Certificados, Identificadores & Perfis,** selecione **Identificadores**. Clique **+** para registar uma nova aplicação.

    ![Página de IDs de Aplicação do Portal de Aprovisionamento do iOS](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. No Registo de um novo ecrã **identificador,** selecione o botão de rádio **App IDs.** Em seguida, selecione **Continuar**.

    ![Portal de Provisionamento do iOS regista nova página de ID](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

1. Atualize os seguintes três valores para a sua nova aplicação e, em seguida, **selecione Continue**:

   * **Descrição**: Digite um nome descritivo para a sua aplicação.

   * **Bundle ID**: Introduza um ID do formulário **com.<organization_identifier>.<product_name>** como mencionado no Guia de Distribuição de [Aplicações](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Na imagem seguinte, o `mobcat` valor é usado como identificador de organização e o valor **PushDemo** é usado como o nome do produto.

      ![IOS Provisioning Portal registar iD](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Notificações push**: Verifique a opção **De Notificações Push** na secção **Capacidades.**

      ![Formulário para registar um novo ID de Aplicação](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Esta ação gera o seu ID de aplicação e solicita que confirme a informação. **Selecione Continue,** em seguida, selecione **Registar-se** para confirmar o novo ID da aplicação.

      ![Confirme o novo ID da app](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Depois de selecionar **Registar,** vê o novo ID da aplicação como um item de linha na página **Certificados, Identificadores & Perfis.**

1. Na página **'Certificados', Identifiers & Profiles,** em **Identificadores,** localizar o item da linha de ID da aplicação que criou. Em seguida, selecione a sua linha para exibir o ecrã **de configuração de ID da aplicação.**

#### <a name="creating-a-certificate-for-notification-hubs"></a>Criação de um certificado para centros de notificação

É necessário um certificado para permitir que o centro de notificação funcione com os Serviços de **Notificação apple push (APNS)** e pode ser fornecido de uma de duas maneiras:

1. [Criação de um certificado de pressão p12 que pode ser carregado diretamente para o Centro de Notificação](#option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub) *(a abordagem original)*

1. [Criação de um certificado p8 que pode ser usado para autenticação em base de token](#option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication) *(a abordagem mais recente e recomendada)*

A abordagem mais recente tem uma série de benefícios, tal como documentados na autenticação baseada em [Token (HTTP/2) para a APNS](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification). São necessários menos passos, mas também é mandatado para cenários específicos. No entanto, foram previstas medidas para ambas as abordagens, uma vez que ambas funcionarão para efeitos deste tutorial.

##### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>OPÇÃO 1: Criação de um certificado de pressão p12 que pode ser carregado diretamente para o Centro de Notificação

1. No Mac, execute a ferramenta de Acesso Keychain. Pode ser aberto a partir da pasta **Utilities** ou da **Outra** pasta no Launchpad.

1. Selecione **Keychain Access**, expanda **o Certificate Assistant**e, em seguida, selecione **Solicite um Certificado a partir de uma Autoridade de Certificados**.

    ![Utilizar o Acesso Keychain para pedir um novo certificado](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Por predefinição, o Keychain Access seleciona o primeiro item da lista. Isto pode ser um problema se estiver na categoria **certificados** e a **Apple Worldwide Developer Relations Certification Authority** não é o primeiro item da lista. Certifique-se de que tem um item não chave, ou a chave **da Apple Worldwide Developer Relations Certification Authority** é selecionada, antes de gerar o CSR (Pedido de Assinatura de Certificado).

1. Selecione o seu **Endereço de E-mail do utilizador,** insira o valor **nome comum,** certifique-se de que especifica **guardar para o disco**e, em seguida, selecione **Continue**. Deixe **o endereço de e-mail CA** em branco, uma vez que não é necessário.

    ![Informações de certificados esperadas](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Introduza um nome para o ficheiro pedido de assinatura de **certificado (CSR)** em **Save As**, selecione a localização em **Onde**e, em seguida, selecione **Guardar**.

    ![Escolha um nome de ficheiro para o certificado](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Esta ação guarda o **ficheiro CSR** no local selecionado. A localização **predefinida**é desktop . Memorize a localização escolhida para o ficheiro.

1. De volta aos **Certificados, os Identificadores &** a página perfis no [portal de provisionamento do iOS,](https://go.microsoft.com/fwlink/p/?LinkId=272456)desloque-se até à opção **de Notificações Push verificadas** e, em seguida, selecione **Configurar** para criar o certificado.

    ![Editar a página de ID de Aplicação](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Aparece a janela **de certificados do serviço de notificação Apple Push TLS/SSL.** Selecione o botão **'Criar Certificado'** na secção **de Certificado de Desenvolvimento TLS/SSL.**

    ![Criar certificado para o botão de ID de Aplicação](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    É apresentado o novo ecrã **de Certificado Criar.**

    > [!NOTE]
    > Este tutorial utiliza um certificado de programação. É utilizado o mesmo processo ao registar um certificado de produção. Não se esqueça de verificar se está a utilizar o mesmo tipo de certificado ao enviar notificações.

1. Selecione **Choose File,** navegue no local onde guardou o **ficheiro CSR**e, em seguida, clique duas vezes no nome do certificado para o carregar. Em seguida, selecione **Continuar**.

1. Depois de o portal criar o certificado, selecione o botão **Descarregar.** Guarde o certificado e lembre-se do local para o qual está guardado.

    ![Página de transferência do certificado gerado](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    O certificado é descarregado e guardado para o seu computador na sua pasta **Downloads.**

    ![Localizar o ficheiro de certificado na pasta Transferências](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Por predefinição, o certificado de desenvolvimento descarregado é nomeado **aps_development.cer**.

1. Faça duplo clique no certificado push **aps_development.cer** transferido. Esta ação instala o novo certificado na Keychain, conforme apresentado na imagem seguinte:

    ![Lista de certificados de Acesso Keychain que mostra o novo certificado](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Embora o nome no seu certificado possa ser diferente, o nome será pré-fixado com **apple Development iOS Push Services** e tem o identificador de pacote apropriado associado ao mesmo.

1. No Keychain **Control**Access,  +  **Clique** no novo certificado push que criou na categoria **Certificados.** Selecione **Export**, nomeie o ficheiro, selecione o formato **p12** e, em seguida, selecione **Guardar**.

    ![Exportar o certificado no formato p12](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Pode optar por proteger o certificado com uma senha, mas uma palavra-passe é opcional. Clique **em OK** se quiser contornar a criação de palavras-passe. Tome nota do nome do ficheiro e da localização do certificado p12 exportado. São usados para permitir a autenticação com APNs.

    > [!NOTE]
    > O nome e localização do seu ficheiro p12 podem ser diferentes do que está representado neste tutorial.

##### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>OPÇÃO 2: Criação de um certificado p8 que pode ser utilizado para a autenticação baseada em fichas

1. Tome nota dos seguintes detalhes:

    * **Prefixo de ID de aplicativo** **(Team ID)**
    * **ID do Pacote**

1. De volta aos **certificados, identifitários & perfis,** clique em **Teclas**.

   > [!NOTE]
   > Se já tiver uma chave configurada para **APNS,** pode reutilizar o certificado p8 que descarregou logo após a sua criação. Em caso afirmativo, pode ignorar os passos **3** a **5**.

1. Clique no **+** botão (ou no botão **Criar uma chave)** para criar uma nova tecla.
1. Forneça um valor de **nome chave** adequado, depois verifique a opção apple **push notifications (APNS)** e, em seguida, clique em **Continuar**, seguido de **Registar** no ecrã seguinte.
1. Clique **em Baixar** e, em seguida, mova o ficheiro **p8** (pré-fixado com *AuthKey_)* para um diretório local seguro e, em seguida, clique em **Fazer**.

   > [!NOTE]
   > Certifique-se de que mantém o seu ficheiro P8 num local seguro (e guarde uma cópia de segurança). Depois de descarregar a sua chave, esta não pode ser refi descarregada à medida que a cópia do servidor é removida.
  
1. Em **Teclas,** clique na tecla que criou (ou numa chave existente se tiver optado por utilizá-la).
1. Tome nota do valor de identificação da **chave.**
1. Abra o seu certificado p8 numa aplicação adequada à sua escolha, como [**Código de Estúdio Visual.**](https://code.visualstudio.com) Tome nota do valor-chave (entre **-----BEGIN PRIVATE KEY-----** e **-----END PRIVATE KEY-----**).

    -----BEGIN PRIVATE KEY-----  
    <key_value>  
    -----ENDA CHAVE PRIVADA-----

    > [!NOTE]
    > Este é o **valor simbólico** que será usado mais tarde para configurar o Centro **de Notificação**.

No final destes passos, deverá ter as seguintes informações para utilização posterior no [Configure o seu centro de notificação com informações sobre APNS:](#configure-your-notification-hub-with-apns-information)

* **ID da equipa** (ver passo 1)
* **ID do pacote** (ver passo 1)
* **ID chave** (ver passo 7)
* **Valor simbólico** (valor chave p8 obtido no passo 8)

### <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de aprovisionamento para a aplicação

1. Volte ao Portal de Provisionamento do [iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecione **Certificados, Identificadores & Perfis**, selecione **Perfis** do menu esquerdo e, em seguida, selecione para criar um **+** novo perfil. Aparece o novo ecrã **de perfil de provisionamento.**

1. Selecione **o desenvolvimento da aplicação iOS** em **desenvolvimento** como o tipo de perfil de provisionamento e, em seguida, selecione **Continue**.

    ![Lista de perfis de aprovisionamento](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Em seguida, selecione o ID da aplicação que criou a partir da lista de drop-down do **App E** selecione **Continue**.

    ![Selecionar o ID de Aplicação](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Na janela **de certificados Select,** selecione o certificado de desenvolvimento que utiliza para a assinatura de código e selecione **Continue**.

    > [!NOTE]
    > Este certificado não é o certificado de pressão que criou no [passo anterior](#creating-a-certificate-for-notification-hubs). Este é o seu certificado de desenvolvimento. Se alguém não existe, deve criá-lo, uma vez que este é um [pré-requisito](#prerequisites) para este tutorial. Os certificados de desenvolvimento podem ser criados no [Apple Developer Portal,](https://developer.apple.com)via [Xcode](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html) ou no [Visual Studio.](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/)

1. Volte aos **Certificados, Identifiers & Profiles,** selecione **Perfis** do menu esquerdo e, em seguida, selecione para criar um **+** novo perfil. Aparece o novo ecrã **de perfil de provisionamento.**

1. Na janela **de certificados Select,** selecione o certificado de desenvolvimento que criou. Em seguida, selecione **Continuar**.

1. Em seguida, selecione os dispositivos a utilizar para testes e selecione **Continue**.

1. Por fim, escolha um nome para o perfil em **Nome do Perfil de Provisionamento,** e selecione **Gerar**.

    ![Escolher um nome de perfil de aprovisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Quando o novo perfil de provisionamento for criado, selecione **Download**. Lembre-se do local para o qual está guardado.

1. Navegue pela localização do perfil de provisionamento e, em seguida, clique duas vezes nele para instalá-lo na sua máquina de desenvolvimento.
