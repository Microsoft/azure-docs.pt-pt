---
title: incluir ficheiro
description: incluir ficheiro
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bf2596f5a8e287799285f97f3d1be9f3fe10f644
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77123138"
---
## <a name="generate-the-certificate-signing-request-file"></a>Gerar o ficheiro de pedido de assinatura de certificado

O Apple Push Notification Service (APNs) utiliza certificados para autenticar as suas notificações push. Siga estas instruções para criar o certificado push necessário para enviar e receber notificações. Para obter mais informações sobre estes conceitos, veja a documentação oficial do [Serviço Apple Push Notification](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Gere o ficheiro Certificate Signing Request (CSR), que a Apple utiliza para gerar um certificado de push assinado.

1. No Mac, execute a ferramenta de Acesso Keychain. Pode ser aberto a partir da pasta **Utilities** ou da **outra** pasta no Launchpad.

1. Selecione Acesso à corrente de **fechadura,** aumente **o Assistente de Certificado,** e, em seguida, selecione **Solicite um Certificado de uma Autoridade de Certificados**.

    ![Utilizar o Acesso Keychain para pedir um novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Por predefinição, o Keychain Access seleciona o primeiro item da lista. Isto pode ser um problema se estiver na categoria **certificados** e a **Apple Worldwide Developer Relations Certification Authority** não for o primeiro item da lista. Certifique-se de que tem um item não chave, ou a chave apple **worldwide developer relations certification Authority** é selecionada, antes de gerar o CSR (Pedido de Assinatura de Certificado).

1. Selecione o seu Endereço de **E-mail do Utilizador,** introduza o valor do **Nome Comum,** certifique-se de que especifica **o Saved para o disco**, e depois selecione **Continuar**. Deixe o endereço de **e-mail ca** em branco, uma vez que não é necessário.

    ![Informações de certificado obrigatórias](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Introduza um nome para o ficheiro CSR no **Save As**, selecione a localização em **Onde**, e, em seguida, selecione **Guardar**.

    ![Escolha um nome de ficheiro para o certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Esta ação guarda o ficheiro CSR no local selecionado. A localização predefinida é **Desktop**. Memorize a localização escolhida para o ficheiro.

Em seguida, registe a sua aplicação com a Apple, ative notificações push e carregue a RSE exportada para criar um certificado push.

## <a name="register-your-app-for-push-notifications"></a>Registar a aplicação para notificações push

Para enviar notificações push para uma aplicação iOS, registe a sua aplicação com a Apple e registe-se também para notificações push.  

1. Se ainda não registou a sua aplicação, navegue para o portal de [provisionamento do iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) no Apple Developer Center. Inscreva-se no portal com o seu APPLE ID e selecione **Identificadores**. Em seguida, selecione **+** para registar uma nova aplicação.

    ![Página de IDs de Aplicação do Portal de Aprovisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. No **ecrã Register um novo identificador,** selecione o botão de rádio **App IDs.** Em seguida, selecione **Continuar**.

    ![portal de provisionamento iOS regista nova página de ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Atualize os seguintes três valores para a sua nova aplicação e, em seguida, selecione **Continuar:**

   * **Descrição**: Digite um nome descritivo para a sua aplicação.

   * **Id do pacote**: Introduza um pacote de identificação do formulário **Identificador.Nome** do produto mencionado no Guia de Distribuição de [Aplicações](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Os valores do *Identificador* de Organização e *do Nome* do Produto devem corresponder ao identificador de organização e nome do produto que utiliza quando criar o seu projeto Xcode. Na seguinte imagem, o valor **notificationHubs** é usado como um identificador de organização e o valor **GetStarted** é usado como o nome do produto. Certifique-se de que o valor do **Identificador de Pacote** corresponde ao valor do seu projeto Xcode, de modo a que o Xcode utilize o perfil de publicação correto.

      ![iOS provisioning portal página de ID da aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Notificações push**: Verifique a opção **Notificações push** na secção **Capacidades.**

      ![Formulário para registar um novo ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Esta ação gera o seu ID da Aplicação e solicita que confirme a informação. Selecione **Continuar**e, em seguida, selecione **Registar** para confirmar o novo ID da aplicação.

      ![Confirme o novo ID da app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Depois de selecionar **O Registo,** vê o novo ID da aplicação como um item de linha na página **Certificados, Identificadores e Perfis.**

4. Na página **Certificados, Identificadores e Perfis,** em **'Identificadores',** localize o item da linha ID da Aplicação que acabou de criar e selecione a sua linha para exibir o ecrã de Configuração de ID da **Aplicação.**

5. Desloque-se até à opção **Notificações** push verificadas e, em seguida, selecione **Configure** para criar o certificado.

    ![Editar a página de ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

6. Aparece a janela **de certificados SSL do serviço** de notificação push da Apple. Selecione o botão **Criar Certificado** sob a secção **Certificado SSL** de Desenvolvimento.

    ![Criar certificado para o botão de ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    É apresentado **um novo ecrã De Certificado.**

    > [!NOTE]
    > Este tutorial utiliza um certificado de programação. É utilizado o mesmo processo ao registar um certificado de produção. Não se esqueça de verificar se está a utilizar o mesmo tipo de certificado ao enviar notificações.

1. Selecione **Escolher Ficheiro,** navegar para o local onde guardou o ficheiro CSR a partir da primeira tarefa e, em seguida, clique duas vezes no nome do certificado para carregá-lo. Em seguida, selecione **Continuar**.

1. Depois de o portal criar o certificado, selecione o botão **Descarregamento.** Guarde o certificado e lembre-se do local onde está guardado.

    ![Página de transferência do certificado gerado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    O certificado é descarregado e guardado para o seu computador na sua pasta **Downloads.**

    ![Localizar o ficheiro de certificado na pasta Transferências](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Por predefinição, o certificado de desenvolvimento descarregado **chama-se aps_development.cer**.

1. Faça duplo clique no certificado push **aps_development.cer** transferido. Esta ação instala o novo certificado na Keychain, conforme apresentado na imagem seguinte:

    ![Lista de certificados de Acesso Keychain que mostra o novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Embora o nome no seu certificado possa ser diferente, o nome será pré-fixado com os **Serviços push apple Development iOS**.

1. No Acesso Keychain, clique com o botão direito do rato no novo certificado push que criou na categoria **Certificados**. **Selecione Exportação,** nomeie o ficheiro, selecione o formato **.p12** e, em seguida, selecione **Guardar**.

    ![Exportar o certificado no formato p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Pode optar por proteger o certificado com uma palavra-passe, mas esta é opcional. Clique **em OK** se quiser contornar a criação de senha. Tome nota do nome do ficheiro e da localização do certificado .p12 exportado. São utilizados para permitir a autenticação com APNs.

    > [!NOTE]
    > O nome e a localização do ficheiro .p12 podem ser diferentes do que está representado neste tutorial.

## <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de aprovisionamento para a aplicação

1. Volte ao portal de [provisionamento do iOS,](https://go.microsoft.com/fwlink/p/?LinkId=272456)selecione **Certificados, Identificadores e Perfis,** selecione **Perfis** do menu esquerdo e, em seguida, selecione **+** para criar um novo perfil. Aparece **o ecrã Do Registo de Um Novo Perfil de Provisionamento.**

1. Selecione o desenvolvimento de **aplicações iOS** em **Desenvolvimento** como o tipo de perfil de provisionamento e, em seguida, selecione **Continuar**.

    ![Lista de perfis de aprovisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Em seguida, selecione o ID da aplicação que criou a partir da lista de lançamentos do ID da **aplicação** e selecione **Continuar**.

    ![Selecionar o ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Na janela **de certificados Select,** selecione o certificado de desenvolvimento que utiliza para a assinatura de código e selecione **Continuar**. Este certificado não é o certificado de pressão que criou. Se alguém não existe, tem de criá-lo. Se existir um certificado, salte para o próximo passo. Criar um certificado de desenvolvimento se não existir:

    1. Se vir **que não existem certificados disponíveis,** selecione **Create Certificate**.
    2. Na secção **Software,** selecione **Apple Development**. Em seguida, selecione **Continuar**.
    3. No ecrã **Criar um Novo Certificado,** selecione **Escolher Ficheiro**.
    4. Navegue no certificado de pedido de assinatura de **certificado** que criou anteriormente, selecione-o e, em seguida, selecione **Open**.
    5. Selecione **Continuar**.
    6. Descarregue o certificado de desenvolvimento e lembre-se do local onde está guardado.

1. Volte à página **Certificados, Identificadores e Perfis,** selecione **Perfis** do menu esquerdo e, em seguida, selecione **+** para criar um novo perfil. Aparece **o ecrã Do Registo de Um Novo Perfil de Provisionamento.**

1. Na janela **de certificados Select,** selecione o certificado de desenvolvimento que acabou de criar. Em seguida, selecione **Continuar**.

1. Em seguida, selecione os dispositivos a utilizar para testes e selecione **Continue**.

1. Por fim, escolha um nome para o perfil no **Nome do Perfil de Provisionamento**e selecione **Generate**.

    ![Escolher um nome de perfil de aprovisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Quando o novo perfil de provisionamento for criado, selecione **Download**. Lembre-se do local onde está guardado.

1. Navegue na localização do perfil de provisionamento e, em seguida, clique duas vezes nele para instalá-lo na sua máquina de desenvolvimento Xcode.

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

Nesta secção, cria um centro de notificação e configura a autenticação com APNs utilizando o certificado push .p12 que criou anteriormente. Se quiser usar um centro de notificação que já criou, pode saltar para o passo 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Configure o seu centro de notificação com informações apns

1. Em **Serviços de Notificação**, selecione **Apple (APNS)** .

1. Selecione **Certificado**.

1. Selecione o ícone do ficheiro.

1. Selecione o ficheiro .p12 que exportou anteriormente e, em seguida, **selecione Open**.

1. Se necessário, especifique a palavra-passe correta.

1. Selecione o modo **Sandbox**. Utilize o modo **Produção** apenas se quiser enviar notificações push a utilizadores que já tenham adquirido a aplicação na loja.

    ![Configurar certificação APNs no portal do Azure](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Selecione **Guardar**.

Já configuraste o teu centro de notificação com APNs. Também tem as cordas de ligação para registar a sua aplicação e enviar notificações push.
