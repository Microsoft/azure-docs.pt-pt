---
title: Envie notificações push para o iOS usando hubs de notificação Azure e o iOS SDK
description: Neste tutorial, você aprende a usar os Hubs de Notificação Azure e o serviço de Notificação Apple Push para enviar notificações push para dispositivos iOS.
author: sethmanheim
ms.author: sethm
ms.date: 10/30/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: c18814230eda6624e8ab1040797c66108f630e30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100581745"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Tutorial: Enviar notificações push para aplicações iOS usando hubs de notificação do Azure

Este tutorial mostra-lhe como configurar os Hubs de Notificação Azure e configurar credenciais para empurrar notificações para um dispositivo iOS através do [serviço de notificação apple push (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1). 

Executar este tutorial é um pré-requisito para os tutoriais do Objetivo C e swift iOS subsequentes, e abrange os seguintes passos:

- Gere o ficheiro de pedido de assinatura de certificado.
- Solicite a sua aplicação para notificações push.
- Crie um perfil de provisionamento para a aplicação.
- Criar um hub de notificação.
- Configure o centro de notificação com informações da APNS.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/free/).

Também vai precisar do seguinte:

- Uma conta apple [developer](https://developer.apple.com/) ativa.
- Um Mac que executa [o Xcode,](https://go.microsoft.com/fwLink/p/?LinkID=266532)juntamente com um certificado de desenvolvimento válido instalado no seu Keychain.
- Um iPhone ou iPad a correr a versão 10 ou posterior do iOS.
- O seu dispositivo físico registado no Portal da [Apple](https://developer.apple.com/) e associado ao seu certificado.

Leia a visão geral dos [Hubs de Notificação do Azure](notification-hubs-push-notification-overview.md) se não estiver familiarizado com o serviço.

> [!NOTE]
> O centro de notificação será configurado apenas para utilizar o modo de autenticação Sandbox. Não deve utilizar este modo de autenticação para cargas de trabalho de produção.

## <a name="generate-the-certificate-signing-request-file"></a>Gere o ficheiro de pedido de assinatura de certificado

O Serviço Apple Push Notification (APNS) utiliza certificados para autenticar as suas notificações push. Siga estas instruções para criar o certificado push necessário para enviar e receber notificações. Para obter mais informações sobre estes conceitos, veja a documentação oficial do [Serviço Apple Push Notification](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Gere o ficheiro De pedido de assinatura de certificado (CSR), que a Apple utiliza para gerar um certificado de pressão assinado:

1. No Mac, execute a ferramenta de Acesso Keychain. Pode ser aberto a partir da pasta **Utilities** ou da **Outra** pasta no Launchpad.

2. Selecione **Keychain Access**, expanda **o Certificate Assistant** e, em seguida, selecione **Solicite um Certificado a partir de uma Autoridade de Certificados**.

   :::image type="content" source="media/ios-sdk-get-started/image1.png" alt-text="Screenshot que destaca o Pedido de Um Certificado a partir de uma opção de menu da Autoridade de Certificado.":::

   > [!NOTE]
   > Por predefinição, o Keychain Access seleciona o primeiro item da lista. Isto pode ser um problema se estiver na categoria **certificados** e a **Apple Worldwide Developer Relations Certification Authority** não é o primeiro item da lista. Certifique-se de que tem um item não chave, ou a chave **da Apple Worldwide Developer Relations Certification Authority** é selecionada, antes de gerar o CSR (Pedido de Assinatura de Certificado).

3. Selecione o seu **Endereço de E-mail do utilizador,** insira o valor **nome comum,** certifique-se de que especifica **guardar para o disco** e, em seguida, selecione **Continue**. Deixe **o endereço de e-mail CA** em branco, uma vez que não é necessário.

   :::image type="content" source="media/ios-sdk-get-started/image2.png" alt-text="Informações de certificado obrigatórias":::

4. Introduza um nome para o ficheiro CSR em **Save As**, selecione a localização em **Onde** e, em seguida, selecione **Guardar**.

   :::image type="content" source="media/ios-sdk-get-started/image3.png" alt-text="Escolha o nome do ficheiro":::

   Esta ação guarda o ficheiro CSR no local selecionado. A localização **predefinida** é desktop . Memorize a localização escolhida para o ficheiro.

Em seguida, registe a sua aplicação com a Apple, permita notificações push e carregar a RSE exportada para criar um certificado push.

## <a name="register-your-app-for-push-notifications"></a>Registar a aplicação para notificações push

Para enviar notificações push para uma aplicação iOS, registe a sua aplicação com a Apple e registe-se também para notificações push.

1. Se ainda não registou a sua aplicação, consulte o [Portal de Provisionamento](https://go.microsoft.com/fwlink/p/?LinkId=272456) do iOS no Apple Developer Center. Inscreva-se no portal com o seu Apple ID e selecione **Identifiers**. Em seguida, selecione **+** para registar uma nova aplicação.

   :::image type="content" source="media/ios-sdk-get-started/image4.png" alt-text="Página de IDs de aplicativo":::

2. No Registo de um novo ecrã **identificador,** selecione o botão de rádio **App IDs.** Em seguida, selecione **Continuar**.

   :::image type="content" source="media/ios-sdk-get-started/image5.png" alt-text="Registar nova página de ID":::

3. Atualize os seguintes três valores para a sua nova aplicação e, em seguida, **selecione Continue**:

   - **Descrição**: Digite um nome descritivo para a sua aplicação.
   - **Bundle ID**: Introduza um ID do formulário **Organization Identifier.Product Name,** conforme mencionado no Guia de Distribuição de [Aplicações.](https://help.apple.com/xcode/mac/current/#/dev91fe7130a) Os valores **do Identificador da Organização** e do Nome do **Produto** devem corresponder ao identificador da organização e ao nome do produto que utiliza quando criar o seu projeto Xcode. Na imagem seguinte, o valor **NotificationHubs** é usado como identificador de organização e o valor **GetStarted** é usado como o nome do produto. Certifique-se de que o valor **do Identificador de Pacote** corresponde ao valor do seu projeto Xcode, de modo a que o Xcode utilize o perfil de publicação correto.

      :::image type="content" source="media/ios-sdk-get-started/image6.png" alt-text="Registar iD de aplicativo":::

   - **Notificações push**: Verifique a opção **De Notificações Push** na secção **Capacidades.**

      :::image type="content" source="media/ios-sdk-get-started/image7.png" alt-text="Registar novo ID de aplicação":::

      Esta ação gera o seu ID de aplicação e solicita que confirme a informação. **Selecione Continue,** em seguida, selecione **Registar-se** para confirmar o novo ID da aplicação.

      :::image type="content" source="media/ios-sdk-get-started/image8.png" alt-text="Confirme o novo ID da app":::

      Depois de selecionar **Registar,** vê o novo ID da aplicação como um item de linha na página **Certificados, Identificadores & Perfis.**

4. Na página **Certificados, Identifiers & Profiles,** em **Identificadores,** localizar o item da linha de ID da aplicação que acabou de criar e selecionar a sua linha para exibir o ecrã **de Configuração de ID da aplicação.**

## <a name="create-a-certificate-for-notification-hubs"></a>Criar um certificado para centros de notificação

> [!NOTE]
> Com o lançamento do iOS 13, só pode receber notificações silenciosas utilizando a autenticação baseada em token. Se estiver a utilizar a autenticação baseada em certificados para as suas credenciais APNS, deve mudar para a autenticação baseada em fichas.

É necessário um certificado para permitir que o centro de notificação trabalhe com a **APNS**. Isto pode ser feito de uma de duas maneiras:

- Crie um ficheiro **.p12** que pode ser enviado diretamente para Os Centros de Notificação.

- Crie um ficheiro **.p8** que pode ser utilizado [para a autenticação baseada em fichas](notification-hubs-push-notification-http2-token-authentication.md) (a abordagem mais recente).

A segunda opção tem uma série de benefícios em comparação com a utilização de certificados, conforme documentado na autenticação baseada em [Token (HTTP/2) para a APNS](notification-hubs-push-notification-http2-token-authentication.md). No entanto, são previstas medidas para ambas as abordagens.

### <a name="option-1-create-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hubs"></a>Opção 1: Criar um certificado de pressão .p12 que pode ser carregado diretamente para os Centros de Notificação

1. Desloque-se até à opção **de Notificações push verificadas** e, em seguida, selecione **Configure** para criar o certificado.

   :::image type="content" source="media/ios-sdk-get-started/image9.png" alt-text="ID da Aplicação":::

2. Aparece a janela **SSL Certificates do serviço de notificação apple push.** Selecione o botão **'Criar Certificado'** na secção **Certificado SSL de Desenvolvimento.**

   :::image type="content" source="media/ios-sdk-get-started/image10.png" alt-text="Criar certificado":::

   É apresentado o novo ecrã **de Certificado Criar.**

   > [!NOTE]
   > Este tutorial utiliza um certificado de programação. É utilizado o mesmo processo ao registar um certificado de produção. Certifique-se de que utiliza o mesmo tipo de certificado ao enviar notificações.

3. Selecione **Choose File**, navegue no local onde guardou o ficheiro CSR a partir da primeira tarefa e, em seguida, clique duas vezes no nome do certificado para o carregar. Em seguida, selecione **Continuar**.

4. Depois de o portal criar o certificado, selecione o botão **Descarregar.** Guarde o certificado e lembre-se do local para o qual está guardado.

   :::image type="content" source="media/ios-sdk-get-started/image11.png" alt-text="Certificado de descarregamento":::

   O certificado é descarregado e guardado na sua pasta **Downloads.**

   :::image type="content" source="media/ios-sdk-get-started/image12.png" alt-text="Localizar o arquivo de certificados":::

   Por predefinição, o certificado de desenvolvimento descarregado é nomeado **aps_development.cer**.

5. Clique duas vezes no desenvolvimento de aps de certificado de push **\_ descarregado.cer**. Esta ação instala o novo certificado na Keychain, conforme apresentado na imagem seguinte:

   :::image type="content" source="media/ios-sdk-get-started/image13.png" alt-text="Acesso ao chaveiro":::

   Embora o nome no seu certificado possa ser diferente, o nome será pré-fixado com **apple development iOS Push Services**.

6. No Acesso Keychain, clique com o botão direito do rato no novo certificado push que criou na categoria **Certificados**. Selecione **Export**, nomeie o ficheiro, selecione o formato **.p12** e, em seguida, selecione **Guardar**.

   :::image type="content" source="media/ios-sdk-get-started/image14.png" alt-text="Exportar o certificado":::

   Pode optar por proteger o certificado com uma senha, mas esta é opcional. Clique **em OK** se quiser contornar a criação de palavras-passe. Tome nota do nome do ficheiro e da localização do certificado .p12 exportado. São utilizados para permitir a autenticação com APNS.

   > [!NOTE]
   > O nome e localização do ficheiro .p12 podem ser diferentes do que está representado neste tutorial.

### <a name="option-2-create-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>Opção 2: Criar um certificado .p8 que pode ser usado para autenticação baseada em fichas

1. Tome nota dos seguintes detalhes:

   - **Prefixo ID da aplicação** (este é um **ID de equipa)**
   - **ID do Pacote**

2. De volta aos **certificados, identifitários & perfis,** clique em **Teclas**. Se já tiver uma chave configurada para **a APNS,** pode reutilizar o certificado .p8 que descarregou logo após a sua criação. Em caso afirmativo, pode ignorar os passos 3 a 5.

3. Clique no **+** botão (ou no botão **Criar uma chave)** para criar uma nova tecla.

4. Forneça um valor de **nome chave** adequado, verifique a opção apple **push notifications (APNS)** e, em seguida, clique em **Continuar**, seguido de **Registar** no ecrã seguinte.

5. Clique **em Baixar** e, em seguida, mova o ficheiro **.p8** (prefixado `AuthKey_` com) para um diretório local seguro e, em seguida, clique em **Fazer**.

   > [!IMPORTANT]
   > Certifique-se de que mantém o ficheiro .p8 num local seguro (e guarde uma cópia de segurança). Depois de descarregar a sua chave, não pode ser re-descarregada; a cópia do servidor é removida.

6. Em **Teclas,** clique na tecla que acabou de criar (ou uma chave existente se tiver optado por usá-la).

7. Tome nota do valor de identificação da **chave.**

8. Abra o seu certificado .p8 numa aplicação adequada à sua escolha, como [o Código do Estúdio Visual,](https://code.visualstudio.com/)e tome nota do valor da chave. Este é o valor entre **-----BEGIN PRIVATE KEY-----** e **-----END PRIVATE KEY-----** .

   ```p8
   -----BEGIN PRIVATE KEY-----
   <key_value>
   -----END PRIVATE KEY-----
   ```

   Este é o valor simbólico que será usado mais tarde para configurar os Centros de Notificação.

No final destes passos deverá ter as seguintes informações para utilização posterior no [Configure o seu centro de notificação com informações sobre APNS:](#configure-the-notification-hub-with-apns-information)

- **ID da equipa** (ver passo 1)
- **ID do pacote** (ver passo 1)
- **ID chave** (ver passo 7)
- **Valor simbólico** (o valor da chave .p8, ver passo 8)

## <a name="create-a-provisioning-profile"></a>Criar um perfil de provisionamento

1. Volte ao Portal de Provisionamento do [iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecione **Certificados, Identificadores & Perfis**, selecione **Perfis** do menu esquerdo e, em seguida, selecione para criar um **+** novo perfil. Aparece o novo ecrã **de perfil de provisionamento.**

2. Selecione **o desenvolvimento da aplicação iOS** em **desenvolvimento** como o tipo de perfil de provisionamento e, em seguida, selecione **Continue**.

   :::image type="content" source="media/ios-sdk-get-started/image15.png" alt-text="Lista de perfis de aprovisionamento":::

3. Em seguida, selecione o ID da aplicação que criou a partir da lista de drop-down do **App ID** e, em seguida, selecione **Continue**.

   :::image type="content" source="media/ios-sdk-get-started/image16.png" alt-text="Selecione ID de aplicativo":::

4. Na janela **de certificados Select,** selecione o certificado de desenvolvimento que utiliza para a assinatura de código e selecione **Continue**. Este certificado não é o certificado de pressão que criou. Se alguém não existe, deve criá-lo. Se existir um certificado, salte para o passo seguinte. Para criar um certificado de desenvolvimento se não existir:

   1. Se não vir **nenhum Certificado disponível,** selecione **Criar Certificado**.
   2. Na secção **de Software,** selecione **Apple Development**. Em seguida, selecione **Continuar**.
   3. No ecrã **Criar um novo certificado,** selecione **Escolha Ficheiro**.
   4. Navegue no certificado **de Pedido de Assinatura** de Certificado que criou anteriormente, selecione-o e, em seguida, selecione **Open**.
   5. Selecione **Continuar**.
   6. Descarregue o certificado de desenvolvimento e lembre-se do local onde está guardado.

5. Volte aos **Certificados, Identifiers & Profiles,** selecione **Perfis** do menu esquerdo e, em seguida, selecione para criar um **+** novo perfil. Aparece o novo ecrã **de perfil de provisionamento.**

6. Na janela **de certificados Select,** selecione o certificado de desenvolvimento que acabou de criar. Em seguida, selecione **Continuar**.

7. Em seguida, selecione os dispositivos a utilizar para testes e selecione **Continue**.

8. Por fim, escolha um nome para o perfil no **Nome do Perfil de Provisionamento,** em seguida, selecione **Gerar**.

   :::image type="content" source="media/ios-sdk-get-started/image17.png" alt-text="Escolha o nome do perfil de provisionamento":::

9. Quando o novo perfil de provisionamento for criado, selecione **Download**. Lembre-se do local onde está guardado.

10. Navegue pela localização do perfil de provisionamento e, em seguida, clique duas vezes nele para instalá-lo na sua máquina de desenvolvimento Xcode.

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

Nesta secção, cria-se um centro de notificação e configura a autenticação com a APNS utilizando o certificado de pressão .p12 ou a autenticação baseada em fichas. Se quiser utilizar um centro de notificação que já criou, pode saltar para o passo 5.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **todos os serviços** no menu esquerdo e, em seguida, selecione **'Centros de Notificação'** na secção **Mobile.** Selecione o ícone estrela ao lado do nome de serviço para adicionar o serviço à secção **FAVORITOS** no menu esquerdo. Depois de adicionar **Os Centros de Notificação** aos **FAVORITOS,** selecione-o.

   :::image type="content" source="media/ios-sdk-get-started/image18.png" alt-text="Portal do Azure":::

3. Na página **'Centros de Notificação',** selecione **Adicionar** na barra de ferramentas.

   :::image type="content" source="media/ios-sdk-get-started/image19.png" alt-text="Adicionar botão de barra de ferramentas":::

4. Na página **'Centros de Notificação',** faça os seguintes passos:

   1. Introduza um nome no **Centro de Notificação**.
   2. Introduza um nome em **Criar um novo espaço de nome.** Um espaço de nome contém um ou mais centros de notificação.
   3. Selecione um valor da lista de **localização.** Este valor especifica a localização em que pretende criar o centro de notificação.
   4. Selecione um grupo de recursos existente no **Grupo de Recursos** ou crie um novo grupo de recursos.
   5. Selecione **Criar**.

   :::image type="content" source="media/ios-sdk-get-started/image20.png" alt-text="Definir as propriedades":::

5. Selecione **Notificações** (o ícone da campainha) e, em seguida, selecione **Ir para o recurso**. Também pode atualizar a lista na página **'Centros de Notificação'** e selecionar o seu hub.

   :::image type="content" source="media/ios-sdk-get-started/image21.png" alt-text="Notificações do portal":::

6. Selecione **Políticas de Acesso** na lista. Note que as duas cordas de ligação estão disponíveis para si. Vai precisar deles mais tarde para lidar com notificações push.

   > [!IMPORTANT]
   > Não utilize a política **de assinatura DefaultFulFuldAccesss na** sua aplicação. Isto é para ser usado apenas na sua parte de trás.

   :::image type="content" source="media/ios-sdk-get-started/image22.png" alt-text="Cadeias de ligação":::

## <a name="configure-the-notification-hub-with-apns-information"></a>Configure o centro de notificação com informações da APNS

Nos **Serviços de Notificação**, selecione **Apple (APNS)**, siga os passos adequados com base na abordagem que escolheu anteriormente na secção [Criar um Certificado para Centros de Notificação.](#create-a-certificate-for-notification-hubs)

> [!NOTE]
> Utilize **a Produção** para **Modo de Aplicação** apenas se pretender enviar notificações push para os utilizadores que adquiriram a sua aplicação na loja.

### <a name="option-1-use-a-p12-push-certificate"></a>Opção 1: Utilize um certificado de pressão .p12

1. Selecione **Certificado**.

2. Selecione o ícone de ficheiro.

3. Selecione o ficheiro .p12 que exportou anteriormente e, em seguida, selecione **Abrir**.

4. Se necessário, especifique a senha correta.

5. Selecione o modo **Sandbox**.

   :::image type="content" source="media/ios-sdk-get-started/image23.png" alt-text="Configurar":::

6. Selecione **Guardar**.

### <a name="option-2-use-token-based-authentication"></a>Opção 2: Utilizar a autenticação baseada em fichas

1. Selecione **Token**.

2. Insira os seguintes valores que adquiriu anteriormente:

   - **ID chave**
   - **ID do Pacote**
   - **ID da equipa**
   - **Token**

3. Escolha **caixa de areia**

4. Selecione **Guardar**.

Agora configuraste o teu centro de notificação com a APNS. Também tem as cordas de ligação necessárias para registar a sua app e enviar notificações push.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou e configura um centro de notificação em Azure e configura-o para permitir que as notificações fossem enviadas para a sua aplicação através do Apple Push Notification Service (APNS). Em seguida, vamos criar uma aplicação de iOS de amostra e integrar o Azure Notifications Hubs SDK para que possa receber notificações push enviadas através do portal Azure. Avance para o seguinte tutorial com base na sua língua de eleição:

- [Tutorial: Enviar notificações push para aplicações iOS usando hubs de notificação do Azure](ios-sdk-current.md)
