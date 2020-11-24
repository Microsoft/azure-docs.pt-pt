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
ms.openlocfilehash: 7b5034f2163e8478d7ddb7b9271402b094a809d7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557582"
---
## <a name="generate-the-certificate-signing-request-file"></a>Gere o ficheiro de pedido de assinatura de certificado

O Serviço de Notificação de Push apple (APNs) utiliza certificados para autenticar as suas notificações push. Siga estas instruções para criar o certificado push necessário para enviar e receber notificações. Para obter mais informações sobre estes conceitos, veja a documentação oficial do [Serviço Apple Push Notification](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Gere o ficheiro Pedido de Assinatura de Certificado (CSR), que a Apple utiliza para gerar um certificado de pressão assinado.

1. No Mac, execute a ferramenta de Acesso Keychain. Pode ser aberto a partir da pasta **Utilities** ou da **Outra** pasta no Launchpad.

1. Selecione **Keychain Access**, expanda **o Certificate Assistant** e, em seguida, selecione **Solicite um Certificado a partir de uma Autoridade de Certificados**.

    ![Utilizar o Acesso Keychain para pedir um novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Por predefinição, o Keychain Access seleciona o primeiro item da lista. Isto pode ser um problema se estiver na categoria **certificados** e a **Apple Worldwide Developer Relations Certification Authority** não é o primeiro item da lista. Certifique-se de que tem um item não chave, ou a chave **da Apple Worldwide Developer Relations Certification Authority** é selecionada, antes de gerar o CSR (Pedido de Assinatura de Certificado).

1. Selecione o seu **Endereço de E-mail do utilizador,** insira o valor **nome comum,** certifique-se de que especifica **guardar para o disco** e, em seguida, selecione **Continue**. Deixe **o endereço de e-mail CA** em branco, uma vez que não é necessário.

    ![Informações de certificado obrigatórias](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Introduza um nome para o ficheiro CSR em **Save As**, selecione a localização em **Onde** e, em seguida, selecione **Guardar**.

    ![Escolha um nome de ficheiro para o certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Esta ação guarda o ficheiro CSR no local selecionado. A localização **predefinida** é desktop . Memorize a localização escolhida para o ficheiro.

Em seguida, registe a sua aplicação com a Apple, permita notificações push e carregar a RSE exportada para criar um certificado push.

## <a name="register-your-app-for-push-notifications"></a>Registar a aplicação para notificações push

Para enviar notificações push para uma aplicação iOS, registe a sua aplicação com a Apple e registe-se também para notificações push.  

1. Se ainda não registou a sua aplicação, consulte o [Portal de Provisionamento](https://go.microsoft.com/fwlink/p/?LinkId=272456) do iOS no Apple Developer Center. Inscreva-se no portal com o seu Apple ID e selecione **Identifiers**. Em seguida, selecione **+** para registar uma nova aplicação.

    ![Página de IDs de Aplicação do Portal de Aprovisionamento do iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. No Registo de um novo ecrã **identificador,** selecione o botão de rádio **App IDs.** Em seguida, selecione **Continuar**.

    ![Portal de Provisionamento do iOS regista nova página de ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Atualize os seguintes três valores para a sua nova aplicação e, em seguida, **selecione Continue**:

   * **Descrição**: Digite um nome descritivo para a sua aplicação.

   * **Bundle ID**: Introduza um ID do formulário **Organization Identifier.Product Name,** conforme mencionado no Guia de Distribuição de [Aplicações.](https://help.apple.com/xcode/mac/current/#/dev91fe7130a) Os valores *do Identificador da Organização* e do Nome do *Produto* devem corresponder ao identificador da organização e ao nome do produto que utiliza quando criar o seu projeto Xcode. Na imagem seguinte, o valor **NotificationHubs** é usado como identificador de organização e o valor **GetStarted** é usado como o nome do produto. Certifique-se de que o valor **do Identificador de Pacote** corresponde ao valor do seu projeto Xcode, de modo a que o Xcode utilize o perfil de publicação correto.

      ![IOS Provisioning Portal registar iD](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Notificações push**: Verifique a opção **De Notificações Push** na secção **Capacidades.**

      ![Formulário para registar um novo ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Esta ação gera o seu ID de aplicação e solicita que confirme a informação. **Selecione Continue,** em seguida, selecione **Registar-se** para confirmar o novo ID da aplicação.

      ![Confirme o novo ID da app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Depois de selecionar **Registar,** vê o novo ID da aplicação como um item de linha na página **Certificados, Identificadores & Perfis.**

4. Na página **Certificados, Identifiers & Profiles,** em **Identificadores,** localizar o item da linha de ID da aplicação que acabou de criar e selecionar a sua linha para exibir o ecrã **de Configuração de ID da aplicação.**

## <a name="creating-a-certificate-for-notification-hubs"></a>Criação de um Certificado para Centros de Notificação
É necessário um certificado para permitir que o centro de notificação trabalhe com a **APNS**. Isto pode ser feito de uma de duas maneiras:

1. Crie um **.p12** que pode ser carregado diretamente para o Centro de Notificação.  
2. Crie um **.p8** que pode ser usado [para autenticação baseada em fichas](../articles/notification-hubs/notification-hubs-push-notification-http2-token-authentication.md) *(a abordagem mais recente).*

A abordagem mais recente tem uma série de benefícios (em comparação com a utilização de certificados) como documentado na autenticação baseada em [Token (HTTP/2) para a APNS](../articles/notification-hubs/notification-hubs-push-notification-http2-token-authentication.md). No entanto, foram previstas medidas para ambas as abordagens. 

### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>OPÇÃO 1: Criação de um certificado de pressão .p12 que pode ser carregado diretamente para o Centro de Notificação

1. Desloque-se até à opção **de Notificações push verificadas** e, em seguida, selecione **Configure** para criar o certificado.

    ![Editar a página de ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

2. Aparece a janela **SSL Certificates do serviço de notificação apple push.** Selecione o botão **'Criar Certificado'** na secção **Certificado SSL de Desenvolvimento.**

    ![Criar certificado para o botão de ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    É apresentado o novo ecrã **de Certificado Criar.**

    > [!NOTE]
    > Este tutorial usa um certificado de desenvolvimento, que a sua aplicação utiliza para gerar um token de dispositivo único. É utilizado o mesmo processo ao registar um certificado de produção. Não se esqueça de verificar se está a utilizar o mesmo tipo de certificado ao enviar notificações.

3. Selecione **Choose File**, navegue no local onde guardou o ficheiro CSR a partir da primeira tarefa e, em seguida, clique duas vezes no nome do certificado para o carregar. Em seguida, selecione **Continuar**.

4. Depois de o portal criar o certificado, selecione o botão **Descarregar.** Guarde o certificado e lembre-se do local para o qual está guardado.

    ![Página de transferência do certificado gerado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    O certificado é descarregado e guardado para o seu computador na sua pasta **Downloads.**

    ![Localizar o ficheiro de certificado na pasta Transferências](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Por predefinição, o certificado de desenvolvimento descarregado é nomeado **aps_development.cer**.

5. Faça duplo clique no certificado push **aps_development.cer** transferido. Esta ação instala o novo certificado na Keychain, conforme apresentado na imagem seguinte:

    ![Lista de certificados de Acesso Keychain que mostra o novo certificado](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Embora o nome no seu certificado possa ser diferente, o nome será pré-fixado com **apple development iOS Push Services**.

6. No Acesso Keychain, clique com o botão direito do rato no novo certificado push que criou na categoria **Certificados**. Selecione **Export**, nomeie o ficheiro, selecione o formato **.p12** e, em seguida, selecione **Guardar**.

    ![Exportar o certificado no formato p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Pode optar por proteger o certificado com uma senha, mas esta é opcional. Clique **em OK** se quiser contornar a criação de palavras-passe. Tome nota do nome do ficheiro e da localização do certificado .p12 exportado. São utilizados para permitir a autenticação com APNs.

    > [!NOTE]
    > O nome e localização do ficheiro .p12 podem ser diferentes do que está representado neste tutorial.

### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>OPÇÃO 2: Criação de um certificado .p8 que pode ser utilizado para autenticação baseada em fichas

1. Tome nota dos seguintes detalhes:

    - **Prefixo ID da aplicação** (este é um **ID de equipa)**
    - **ID do Pacote**
    
2. De volta aos **certificados, identifitários & perfis,** clique em **Teclas**.

   > [!NOTE]
   > Se já tiver uma chave configurada para **a APNS,** pode reutilizar o certificado .p8 que descarregou logo após a sua criação. Em caso afirmativo, pode ignorar os passos **3** a **5**.

3. Clique no **+** botão (ou no botão **Criar uma chave)** para criar uma nova tecla.
4. Forneça um valor de **nome chave** adequado, depois verifique a opção do serviço **de notificações apple push (APNs)** e, em seguida, clique em **Continuar**, seguido de **Registar** no ecrã seguinte.
5. Clique **em Baixar** e, em seguida, mova o ficheiro **.p8** (pré-fixado com *AuthKey_)* para um diretório local seguro e, em seguida, clique em **Fazer**.

   > [!NOTE] 
   > Certifique-se de que mantém o ficheiro .p8 num local seguro (e guarde uma cópia de segurança). Depois de descarregar a sua chave, esta não pode ser refi descarregada à medida que a cópia do servidor é removida.
  
6. Em **Teclas,** clique na tecla que acabou de criar (ou uma chave existente se tiver optado por usá-la).
7. Tome nota do valor de identificação da **chave.**
8. Abra o seu certificado .p8 numa aplicação adequada à sua escolha, como [**o Código do Estúdio Visual,**](https://code.visualstudio.com) e tome nota do valor da chave. Este é o valor entre **-----BEGIN PRIVATE KEY-----** e **-----END PRIVATE KEY-----** .

    ```
    -----BEGIN PRIVATE KEY-----
    <key_value>
    -----END PRIVATE KEY-----
    ```

    > [!NOTE]
    > Este é o **valor simbólico** que será usado mais tarde para configurar o Centro **de Notificação**. 

No final destes passos deverá ter as seguintes informações para utilização posterior no [Configure o seu centro de notificação com informações sobre APNs:](#configure-your-notification-hub-with-apns-information)

- **ID da equipa** (ver passo 1)
- **ID do pacote** (ver passo 1)
- **ID chave** (ver passo 7)
- **Valor simbólico,** ou seja, o valor da chave .p8 (ver passo 8)

## <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de aprovisionamento para a aplicação

1. Volte ao Portal de Provisionamento do [iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecione **Certificados, Identificadores & Perfis**, selecione **Perfis** do menu esquerdo e, em seguida, selecione para criar um **+** novo perfil. Aparece o novo ecrã **de perfil de provisionamento.**

1. Selecione **o desenvolvimento da aplicação iOS** em **desenvolvimento** como o tipo de perfil de provisionamento e, em seguida, selecione **Continue**.

    ![Lista de perfis de aprovisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Em seguida, selecione o ID da aplicação que criou a partir da lista de drop-down do **App E** selecione **Continue**.

    ![Selecionar o ID de Aplicação](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Na janela **de certificados Select,** selecione o certificado de desenvolvimento que utiliza para a assinatura de código e selecione **Continue**. Este certificado não é o certificado de pressão que criou. Se alguém não existe, deve criá-lo. Se existir um certificado, salte para o passo seguinte. Para criar um certificado de desenvolvimento se não existir:

    1. Se não vir **nenhum Certificado disponível,** selecione **Criar Certificado**.
    2. Na secção **de Software,** selecione **Apple Development**. Em seguida, selecione **Continuar**.
    3. No ecrã **Criar um novo certificado,** selecione **Escolha Ficheiro**.
    4. Navegue no certificado **de Pedido de Assinatura** de Certificado que criou anteriormente, selecione-o e, em seguida, selecione **Open**.
    5. Selecione **Continuar**.
    6. Descarregue o certificado de desenvolvimento e lembre-se do local para o qual está guardado.

1. Volte aos **Certificados, Identifiers & Profiles,** selecione **Perfis** do menu esquerdo e, em seguida, selecione para criar um **+** novo perfil. Aparece o novo ecrã **de perfil de provisionamento.**

1. Na janela **de certificados Select,** selecione o certificado de desenvolvimento que acabou de criar. Em seguida, selecione **Continuar**.

1. Em seguida, selecione os dispositivos a utilizar para testes e selecione **Continue**.

1. Por fim, escolha um nome para o perfil em **Nome do Perfil de Provisionamento,** e selecione **Gerar**.

    ![Escolher um nome de perfil de aprovisionamento](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Quando o novo perfil de provisionamento for criado, selecione **Download**. Lembre-se do local para o qual está guardado.

1. Navegue pela localização do perfil de provisionamento e, em seguida, clique duas vezes nele para instalá-lo na sua máquina de desenvolvimento Xcode.

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

Nesta secção, cria-se um centro de notificação e configura a autenticação com APNs utilizando o certificado de pressão .p12 ou a autenticação baseada em fichas. Se quiser utilizar um centro de notificação que já criou, pode saltar para o passo 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Configure o seu centro de notificação com informações sobre APNs

Nos **Serviços de Notificação,** selecione **Apple (APNS)** e siga os passos adequados com base na abordagem que escolheu anteriormente na secção [Criar um Certificado para Centros de Notificação.](#creating-a-certificate-for-notification-hubs)  

> [!NOTE]
> Se construir a sua aplicação com App Store ou perfil de distribuição Ad-Hoc, utilize a **Produção** para **Modo aplicação.** Isto permitirá que o seu dispositivo envie notificações push para os utilizadores que adquiriram a sua aplicação na loja.

### <a name="option-1-using-a-p12-push-certificate"></a>OPÇÃO 1: Utilização de um certificado de pressão .p12

1. Selecione **Certificado**.

1. Selecione o ícone de ficheiro.

1. Selecione o ficheiro .p12 que exportou anteriormente e, em seguida, selecione **Abrir**.

1. Se necessário, especifique a senha correta.

1. Selecione o modo **Sandbox**.

    ![Configurar certificação APNs no portal do Azure](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Selecione **Guardar**.

### <a name="option-2-using-token-based-authentication"></a>OPÇÃO 2: Utilização da autenticação baseada em fichas

1. Selecione **Token**.
1. Insira os seguintes valores que adquiriu anteriormente:

    - **ID chave**
    - **ID do Pacote**
    - **ID da equipa**
    - **Token** 

1. Escolha **caixa de areia**
1. Selecione **Guardar**. 

Agora configuraste o teu centro de notificação com as APNs. Também tem as cordas de ligação para registar a sua aplicação e enviar notificações push.