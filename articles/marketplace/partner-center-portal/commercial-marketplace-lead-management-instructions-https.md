---
title: Ponto de extremidade HTTPS | Azure Marketplace
description: Configure o gerenciamento de Lead para um ponto de extremidade HTTPS.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: evansma
ms.openlocfilehash: 6a34bdcab5a13af682515bbae96e9a1800ccc37f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902198"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configurar o gerenciamento de leads usando um ponto de extremidade HTTPS

Se o seu sistema de gerenciamento de relacionamento com o cliente (CRM) não tiver suporte explícito no Partner Center para receber leads do Azure Marketplace e do AppSource, você poderá usar um ponto de extremidade HTTPS no MS Flow para lidar com esses leads. Com um ponto de extremidade HTTPS, esses clientes potenciais podem ser enviados como uma notificação por email ou podem ser gravados em um sistema de CRM (gerenciamento de relacionamento com o cliente) com suporte no MS Flow. As instruções neste artigo explicarão o processo básico para criar um novo fluxo usando Microsoft Flow, que gerará a URL HTTP POST que você inserirá no portal de publicação para o campo de URL de ponto de **extremidade HTTPS** > de gerenciamento de Lead. Além disso, estão incluídas instruções sobre como você pode testar seu fluxo com a ajuda de uma ferramenta chamada [postmaster](https://www.getpostman.com/downloads/) que pode ser encontrada online.

## <a name="create-a-flow-using-microsoft-flow"></a>Criar um fluxo usando Microsoft Flow

1. Abra a página da Web do [Flow](https://flow.microsoft.com/) . Selecione **entrar**ou, se você ainda não tiver uma conta, selecione **inscrever-se livre** para criar uma conta de fluxo livre.

2. Entre e selecione **meus fluxos** na barra de menus.

3. Selecione **+ automatizado – de em branco**.

    ![Meus fluxos + automatizados de em branco](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. Na janela *criar um fluxo automatizado* , selecione **ignorar**. 

    ![Criar fluxo automatizado-ignorar](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. No campo **conectores de pesquisa e gatilhos** , digite "solicitação" para localizar o conector de solicitação.
6. Em *gatilhos*, selecione **quando uma solicitação HTTP é recebida**. 

    ![solicitar conector-gatilhos](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. Na janela *quando uma solicitação HTTP é recebida* , copie e cole o esquema JSON abaixo na caixa de texto **esquema JSON do corpo da solicitação** . Esse esquema é usado pela Microsoft para conter seus dados de Lead.

    ![solicitar conector-gatilhos](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

    **Esquema JSON**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

>[!Note]
>Neste ponto da configuração, você pode selecionar para se conectar a um sistema CRM ou configurar uma notificação por email. Siga as instruções restantes com base em sua escolha.

### <a name="to-connect-to-a-crm-system"></a>Para se conectar a um sistema CRM

1. Selecione **+ nova etapa**.
2. Escolha o sistema CRM de sua escolha pesquisando-o onde ele diz *conectores de pesquisa e ações*e selecione-o na seção *ações* com a ação para criar um novo registro. A captura de tela a seguir mostra o **Dynamics 365 – criar** um novo registro como um exemplo.

    ![Criar uma nova palavra-passe](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Forneça o **nome da organização** associada ao sistema CRM. Selecione **clientes potenciais** na lista suspensa **nome da entidade** .

    ![Selecionar clientes potenciais](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow mostra um formulário para fornecer informações de Lead. Você pode mapear itens da solicitação de entrada escolhendo adicionar conteúdo dinâmico. A captura de tela a seguir mostra **OfferTitle** como um exemplo.

    ![Adicionar conteúdo dinâmico](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Mapeie os campos desejados e, em seguida, selecione **salvar** para salvar o fluxo. Uma URL HTTP POST é criada e pode ser acessada na janela *quando uma solicitação HTTP é recebida* . Copie essa URL usando o controle de cópia que está localizado à direita da URL POST HTTP – isso é importante para que você não perca erroneamente nenhuma parte da URL inteira. Salve essa URL, pois será necessária quando você estiver configurando o gerenciamento de Lead no portal de publicação.

    ![Quando uma solicitação HTTP é recebida.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>Para configurar a notificação por email

1. Agora que você concluiu o esquema JSON, selecione **+ nova etapa**.
2. Em **escolher uma ação**, selecione **ações**.
3. Em **ações**, selecione **enviar um email (Outlook do Office 365)** .

    >[!Note]
    >Se você quiser usar uma pesquisa de provedor de email diferente e selecionar *Enviar uma notificação por email (email)* como a ação em vez disso.

    ![Adicionar uma ação de email](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. Na janela **enviar um email** , configure os seguintes campos obrigatórios:

   - **Para** inserir pelo menos um endereço de email válido, no qual os clientes potenciais serão enviados.
   - O fluxo de **assunto** oferece a opção de adicionar conteúdo dinâmico, como o leadname na captura de tela a seguir. Comece digitando um nome de campo seguido clicando na lista de seleção de conteúdo dinâmico na janela pop-up. 

        >[!Note] 
        > Ao adicionar nomes de campo, você pode seguir cada um com um ":" e, em seguida, inserir para criar uma nova linha. Depois de adicionar os nomes de campo, você poderá adicionar cada parâmetro associado da lista de seleção dinâmica.

        ![Adicionar uma ação de email usando conteúdo dinâmico](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Corpo** -na lista de seleção de conteúdo dinâmico, adicione as informações desejadas no corpo do email. Por exemplo, LastName, FirstName, email e Company. <br> <br> Quando você terminar de configurar a notificação por email, ele se parecerá com o exemplo na captura de tela a seguir.


       ![Adicionar uma ação de email](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Selecione **salvar** para concluir o fluxo. Uma URL HTTP POST é criada e pode ser acessada na janela *quando uma solicitação HTTP é recebida* . Copie essa URL usando o controle de cópia que está localizado à direita da URL POST HTTP – isso é importante para que você não perca erroneamente nenhuma parte da URL inteira. Salve essa URL, pois será necessária quando você estiver configurando o gerenciamento de Lead no portal de publicação.

   ![URL do HTTP POST ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testes

Você pode testar se tudo funciona conforme o esperado usando as etapas a seguir usando uma ferramenta chamada [postmaster](https://app.getpostman.com/app/download/win64), que pode ser baixada online. Isso está disponível para o Windows. 

1. Inicie o postmaster e selecione **nova** > **solicitação** para configurar sua ferramenta de teste. 

   ![Solicitação para configurar sua ferramenta de teste](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Preencha o formulário de *solicitação de salvamento* e, em seguida, **salve** na pasta que você criou.

   ![Salvar solicitação](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Selecione **postar** na lista suspensa. 

   ![Testar meu fluxo](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Cole a URL HTTP POST do fluxo que você criou no MS Flow onde ele diz *Inserir URL de solicitação*.

   ![Cole a URL HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Volte para o [Flow](https://flow.microsoft.com/) e localize o fluxo que você criou para enviar leads, acessando **meus fluxos** na barra de menus do fluxo.  Selecione os 3 pontos ao lado do nome do fluxo e selecione **Editar**.

   ![Meus fluxos-editar](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Selecione **teste** no canto superior direito, selecione "Eu executarei a ação do gatilho" e, em seguida, selecione **testar**. Você verá uma indicação na parte superior da tela, indicando que o teste foi iniciado

   ![Fluxo de teste-gatilho](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Volte ao seu aplicativo de postmaster e selecione **Enviar** ao lado de onde você colou a URL https.

   ![Testar meu fluxo-enviar](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Volte para o fluxo e verifique o resultado. Se tudo funcionar conforme o esperado, você verá uma mensagem indicando que foi bem-sucedida.

   ![Fluxo-verificar resultados](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Você também deve ter recebido um email. Verifique sua caixa de entrada de email. 

    >[!Note] 
    >Se você não vir um email do teste, verifique suas pastas de spam e lixo eletrônico. A seguir, você observará apenas os rótulos de campo que adicionou ao configurar a notificação por email. Se esse for um cliente potencial real gerado por meio de sua oferta, você também verá as informações reais do contato do cliente potencial no corpo e na linha de assunto.

   ![Email recebido](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configurar sua oferta para enviar clientes potenciais ao ponto de extremidade HTTPS

Quando você estiver pronto para configurar as informações de gerenciamento de Lead para sua oferta no portal de publicação, siga as etapas abaixo:

1. Navegue até a página de **instalação da oferta** para sua oferta.
2. Selecione **conectar** na seção Gerenciamento de leads.
3. Na janela pop-up detalhes da conexão, selecione **ponto de extremidade https** para o **destino do cliente potencial** e cole a URL http post do fluxo criado seguindo as etapas anteriores no campo **URL do ponto de extremidade https** .
4. Selecione **Guardar**. 

>[!Note] 
>Você deve concluir a configuração do restante da oferta e publicá-la antes de receber clientes potenciais para a oferta.

Quando os clientes potenciais são gerados, a Microsoft envia os clientes potenciais para o fluxo, que são roteados para o sistema CRM ou endereço de email que você configurou.

![Gerenciamento de Lead-conectar](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Detalhes da ligação](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Detalhes da ligação](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

