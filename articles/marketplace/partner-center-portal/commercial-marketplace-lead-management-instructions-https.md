---
title: HTTPS Endpoint [ Mercado Azure
description: Configure a gestão de chumbo para um ponto final HTTPS.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 33359883df86091120295b93618a13476e428d2f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262618"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configure a gestão de chumbo utilizando um ponto final HTTPS

>[!Note]
>O conector Power Automate utilizado nestas instruções requer uma subscrição paga ao Power Automate. Por favor, tenha em conta isto antes de seguir as instruções deste documento.

Se o seu sistema de Gestão de Relacionamento com o Cliente (CRM) não for explicitamente suportado no Partner Center para receber os leads Do Azure Marketplace e AppSource, pode utilizar um ponto final HTTPS no Power Automate para lidar com estes cabos. Com um ponto final HTTPS, estes leads podem ser enviados como uma notificação de e-mail, ou podem ser escritos para um sistema de Gestão de Relacionamento com O Cliente (CRM) suportado pela Power Automate. As instruções deste artigo irão levá-lo através do processo básico para criar um novo fluxo utilizando power automate, que gerará o URL HTTP POST que você vai introduzir no portal de publicação para o campo **DE URL HTTPS Endpoint** de Gestão de Chumbo >. Além disso, estão incluídas instruções sobre como pode testar o seu fluxo com a ajuda de uma ferramenta chamada [Carteiro](https://www.getpostman.com/downloads/) que pode ser encontrada online.

## <a name="create-a-flow-using-power-automate"></a>Criar um fluxo usando power automate

1. Abra a página do [Flow.](https://flow.microsoft.com/) Selecione **Iniciar sessão**, ou se ainda não tiver uma conta, selecione **Iniciar sessão gratuitamente** para criar uma conta Flow gratuita.

2. Inscreva-se e selecione **Os meus fluxos** na barra de menus.

3. Selecione **+Automatizado - a partir de branco**.

    ![Meus fluxos + Automatizado - de branco](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. Na *Construção de uma janela de fluxo automatizada,* selecione **Skip**. 

    ![Construir fluxo automatizado - Skip](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. No campo **de conectores e gatilhos de pesquisa,** escreva "pedido" para encontrar o conector Solicitar.
6. Em *'Gatilhos',* selecione **Quando for recebido um pedido HTTP**. 

    ![conector de pedido - Gatilhos](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. No *pedido de http é recebido* cópia da janela e cola o esquema JSON abaixo na caixa de texto Do Corpo de Pedido **JSON Schema.** Este esquema é utilizado pela Microsoft para conter os seus dados de chumbo.

    ![conector de pedido - Gatilhos](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

    **Esquema jSON**

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
>Nesta altura da configuração pode selecionar para ligar a um sistema CRM ou configurar uma notificação de e-mail. Siga as instruções restantes com base na sua escolha.

### <a name="to-connect-to-a-crm-system"></a>Para ligar a um sistema CRM

1. Selecione **+Novo passo**.
2. Escolha o sistema CRM à sua escolha, procurando-o onde diz *conectores e ações*de pesquisa, e selecione-o sob a secção *Ações* com a ação para criar um novo registo. A captura de ecrã seguinte mostra **Dynamics 365 - Criar** um novo disco como exemplo.

    ![Criar um novo registo](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Forneça o Nome da **Organização** associado ao sistema CRM. Selecione **Leads** da lista de abandono do Nome da **Entidade.**

    ![Selecione os cabos](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. O fluxo mostra um formulário para fornecer informações de chumbo. Pode mapear itens a partir do pedido de entrada, optando por adicionar Conteúdo Dinâmico. A captura de ecrã que se segue mostra o **OfferTitle** como um exemplo.

    ![Adicionar conteúdo dinâmico](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Mapeie os campos que deseja e, em seguida, selecione **Guardar** para salvar o seu fluxo. É criado um URL HTTP POST e é acessível na janela *Quando um pedido HTTP é recebido.* Copie este URL utilizando o controlo de cópia que está localizado à direita do URL HTTP POST - isto é importante para que não perca erradamente nenhuma parte de todo o URL. Guarde este URL como necessitará quando estiver a configurar a gestão de chumbo no portal de publicação.

    ![Quando um pedido HTTP é recebido.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>Para configurar a notificação por e-mail

1. Agora que completou o esquema JSON, selecione **+ Novo passo**.
2. Em **'Escolha uma ação**,selecione **Ações**.
3. Under **Actions**, selecione **Enviar um e-mail (Office 365 Outlook)**.

    >[!Note]
    >Se pretender utilizar um fornecedor de e-mail diferente, procure e selecione Enviar uma notificação por *e-mail (Mail)* como ação.

    ![Adicione uma ação de e-mail](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. Enviar uma janela de **e-mail,** configure os seguintes campos necessários:

   - **Para** - Insira pelo menos um endereço de e-mail válido, onde os cabos serão enviados.
   - **Objeto** - O fluxo dá-lhe a opção de adicionar conteúdo Dinâmico, como **leadSource** na captura do ecrã seguinte. Comece por escrever num nome de campo seguido clicando na lista de escolha seletiva do Conteúdo Dinâmico a partir da janela popup. 

        >[!Note] 
        > Ao adicionar nomes de campo, pode seguir cada um com um ":" e, em seguida, entrar para criar uma nova linha. Uma vez adicionados os nomes de campo, pode adicionar cada parâmetro associado da lista de escolha dinâmica.

        ![Adicione uma ação de e-mail usando conteúdo dinâmico](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Corpo** - Da lista de escolhas de Conteúdo Dinâmico, adicione a informação que pretende no corpo do e-mail. Por exemplo, LastName, FirstName, Email e Empresa. <br> <br> Quando terminar de configurar a notificação de e-mail, será o exemplo na seguinte captura do ecrã.


       ![Adicione uma ação de e-mail](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Selecione **Guardar** para terminar o seu fluxo. É criado um URL HTTP POST e acessível na janela *Quando um pedido HTTP é recebido.* Copie este URL utilizando o controlo de cópia que está localizado à direita do URL HTTP POST - isto é importante para que não perca erradamente nenhuma parte de todo o URL. Guarde este URL como necessitará quando estiver a configurar a gestão de chumbo no portal de publicação.

   ![HTTP POST URL ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testar

Pode testar que tudo funciona como esperado usando uma ferramenta chamada [Correio,](https://app.getpostman.com/app/download/win64)que pode ser descarregado online. Isto está disponível para Windows. 

1. Lance o Carteiro e selecione **New** > **Request** para configurar a sua ferramenta de teste. 

   ![Pedido para configurar a sua ferramenta de teste](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Preencha o formulário *'Pedido de Salvamento'* e, em seguida, **guarde** para a pasta que criou.

   ![Guardar o Pedido](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Selecione **POST** da lista de lançamentos. 

   ![Teste o meu fluxo](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Colar o URL HTTP POST do fluxo que criou no Power Automate onde diz *Enter request URL*.

   ![Colar o URL HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Volte para o [Flow](https://flow.microsoft.com/) e encontre o fluxo que criou para enviar pistas, indo para **My Flows** a partir da barra de menu Flow.  Selecione os 3 pontos ao lado do nome de fluxo e **selecione Editar**.

   ![Os meus fluxos - Editar](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Selecione **Teste** no canto superior direito, selecione "Vou executar a ação do gatilho" e, em seguida, selecione **Test**. Verá uma indicação na parte superior do ecrã indicando que o teste já começou

   ![Fluxo de teste - gatilho](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Volte para a sua aplicação Postman e selecione **Enviar** ao lado do local onde colou o URL HTTPS.

   ![Teste o meu fluxo - Enviar](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Volte para o seu fluxo e verifique o resultado. Se tudo funcionar como esperado, verá uma mensagem indicando que foi um sucesso.

   ![Fluxo - Verificar resultados](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Também deveria ter recebido um e-mail. Verifique a sua caixa de entrada de e-mail. 

    >[!Note] 
    >Se não vir um e-mail do teste, verifique as suas pastas de spam e lixo. Abaixo irá notar apenas as etiquetas de campo que adicionou ao configurar a notificação de e-mail. Se este fosse um chumbo real gerado pela sua oferta, também veria as informações reais do Contacto Principal no corpo e na linha Assunto.

   ![E-mail recebido](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configure a sua oferta para enviar pistas para o ponto final HTTPS

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal editorial, siga os passos abaixo:

1. Navegue na página de **configuração** da Oferta para a sua oferta.
2. Selecione **Ligar** sob a secção de Gestão de Chumbo.
3. Na janela pop-up de detalhes da Ligação, selecione **HTTPS Endpoint** para o **Destino de Chumbo** e cola no URL HTTP POST do fluxo que criou seguindo passos anteriores no campo DEURL do ponto final **HTTPS.**
4. **E-mail de contacto** - Forneça e-mails para pessoas da sua empresa que devam receber notificações de e-mail quando um novo chumbo for recebido. Pode fornecer vários e-mails separando-os com um ponto evío.
5. Selecione **OK**.

Para se certificar de que se ligou com sucesso a um destino de chumbo, clique no botão de validação. Se for bem sucedido, terá um chumbo de teste no destino principal.

>[!Note] 
>Tem de terminar de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.

Quando os cabos são gerados, a Microsoft envia pistas para o Flow, que são encaminhados para o sistema CRM ou endereço de e-mail que configuraste.

![Gestão de chumbo - conecte](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Detalhes da ligação](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Detalhes da ligação](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

