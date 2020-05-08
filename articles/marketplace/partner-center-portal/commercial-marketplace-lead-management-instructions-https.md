---
title: Gestão de chumbo com um ponto final HTTPS - Mercado comercial da Microsoft
description: Saiba como utilizar o Power Automate e um ponto final HTTPS para gerir os cabos do Microsoft AppSource e do Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 7a4fc57b3be8dd59997ef2bfc9624892cf726160
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790988"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>Utilize um ponto final HTTPS para gerir os leads do mercado comercial

Se o seu sistema de gestão de relacionamento com o cliente (CRM) não for explicitamente suportado no Partner Center para receber os leads Microsoft AppSource e Azure Marketplace, pode utilizar um ponto final HTTPS no [Power Automate](https://powerapps.microsoft.com/automate-processes/) para lidar com estes leads. Com um ponto final HTTPS, os leads de mercado comercial podem ser enviados como uma notificação de e-mail ou podem ser escritos para um sistema CRM suportado pela Power Automate.

Este artigo explica como criar um novo fluxo no Power Automate para gerar o URL HTTP POST que utilizará para configurar os leads no Partner Center. Também inclui passos para testar o seu fluxo com [o Carteiro.](https://www.getpostman.com/downloads/)

>[!NOTE]
>O conector Power Automate utilizado nestas instruções requer uma subscrição paga ao Power Automate. Certifique-se de que explica isto antes de configurar este fluxo.

## <a name="create-a-flow-by-using-power-automate"></a>Criar um fluxo usando power automate

1. Abra a página power [automate.](https://flow.microsoft.com/) Selecione **Iniciar sessão**. Se ainda não tiver uma conta, **selecione Iniciar sessão gratuita** para criar uma conta Power Automate gratuita.

1. Inscreva-se e selecione **Os meus fluxos** no menu.

    ![Assine os meus fluxos](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. Abaixo **+ Novo**, selecione + Instantâneo — a partir de **branco**.

    ![Meus fluxos + Automatizado-- de branco](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Nomeie o seu fluxo e, em seguida, **escolha como acionar este fluxo,** selecione **Quando um pedido HTTP for recebido**.

    ![Construa um botão de salto automatizado da janela de fluxo](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Clique no passo de fluxo para expandi-lo.

    ![Expandir o passo de fluxo](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. Utilize um dos seguintes métodos para configurar o **Órgão de Pedido JSON Schema:**

    - Copie o esquema JSON na caixa de texto **Do Corpo de Pedido JSON Schema.**
    - Selecione **Utilizar o payload de exemplo para gerar esquema**. No Enter ou pasta uma caixa de texto **de carga útil JSON de amostra,** cola no exemplo JSON. Selecione **Feito** para criar o esquema.

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

    **Exemplo jSON**
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
    }
    ```

>[!NOTE]
>Nesta altura da configuração, pode selecionar para ligar a um sistema CRM ou configurar uma notificação de e-mail. Siga as instruções restantes com base na sua escolha.

### <a name="connect-to-a-crm-system"></a>Ligar a um sistema CRM

1. Selecione **+Novo passo**.
1. Escolha o sistema CRM à sua escolha, procurando-o onde diz **conectores e ações de pesquisa**. Selecione-o no separador **Ações** com a ação para criar um novo disco. O ecrã seguinte mostra **Criar um novo disco (Dynamics 365)** como exemplo.

    ![Criar um novo registo](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. Forneça o Nome da **Organização** associado ao sistema CRM. Selecione **Leads** da lista de abandono do Nome da **Entidade.**

    ![Selecione os cabos](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. Power Automate mostra um formulário para fornecer informações de chumbo. Pode mapear itens a partir do pedido de entrada, optando por adicionar conteúdo dinâmico. O ecrã seguinte mostra **offerTitle** como um exemplo.

    ![Adicionar conteúdo dinâmico](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. Mapeie os campos que deseja e, em seguida, selecione **Save** para salvar o seu fluxo. É criado um URL HTTP POST e acessível na janela **Quando um pedido HTTP é recebido.** Copie este URL utilizando o controlo de cópia, que está localizado à direita do URL HTTP POST. A utilização do controlo de cópias é importante para que não perca nenhuma parte de toda a URL. Guarde este URL porque vai precisar dele quando configurar a gestão de chumbo no portal de publicação.

    ![Quando um pedido HTTP é recebido](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>Configurar notificação de e-mail

1. Agora que terminou o esquema JSON, selecione **+ Novo passo**.
1. Em **'Escolha uma ação**,selecione **Ações**.
1. No separador **Ações,** selecione **Enviar um e-mail (Office 365 Outlook)**.

    >[!NOTE]
    >Se pretender utilizar um fornecedor de e-mail diferente, procure e selecione Enviar uma notificação por **e-mail (Mail)** como ação.

    ![Adicione uma ação de e-mail](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. No Enviar uma janela de **e-mail,** configure os seguintes campos necessários:

   - **Para**: Insira pelo menos um endereço de e-mail válido para onde os cabos serão enviados.
   - **Objeto**: Power Automate dá-lhe a opção de adicionar conteúdo dinâmico, como **leadSource** mostrado no ecrã seguinte. Comece por inserir um nome de campo. Em seguida, selecione a lista de recolha de conteúdo dinâmico da janela pop-up. 

        >[!NOTE] 
        > Quando adicionar nomes de campo, pode seguir cada nome com um cólon (:) e, em seguida, selecione **Enter** para criar uma nova linha. Depois de adicionar os seus nomes de campo, pode adicionar cada parâmetro associado da lista de escolha dinâmica.

        ![Adicione uma ação de e-mail utilizando conteúdo dinâmico](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Corpo**: A partir da lista dinâmica de recolha de conteúdos, adicione a informação que pretende no corpo do e-mail. Por exemplo, utilize LastName, FirstName, Email e Empresa. Quando terminar de configurar a notificação de e-mail, parece ser o exemplo no ecrã seguinte.


       ![Exemplo de notificação de e-mail](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. Selecione **Guardar** para terminar o seu fluxo. É criado um URL HTTP POST e acessível na janela **Quando um pedido HTTP é recebido.** Copie este URL utilizando o controlo de cópia, que está localizado à direita do URL HTTP POST. Usar este controlo é importante para que não perca nenhuma parte de toda a URL. Guarde este URL porque vai precisar dele quando configurar a gestão de chumbo no portal de publicação.

   ![HTTP POST URL](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testar

Pode testar a sua configuração com [o Carteiro.](https://app.getpostman.com/app/download/win64) Um download online do Carteiro está disponível para Windows. 

1. Inicie o Carteiro e selecione **New** > **Request** para configurar a sua ferramenta de teste. 

   ![Pedido para configurar a sua ferramenta de teste](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Preencha o formulário **'Pedido de Salvamento'** e, em seguida, guarde para a pasta que criou.

   ![Formulário de Pedido de Salvamento](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. Selecione **POST** da lista de lançamentos. 

   ![Teste o meu fluxo](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Colar o URL HTTP POST do fluxo que criou no Power Automate onde diz **Enter request URL**.

   ![Colar o URL HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Volte para power [automate.](https://flow.microsoft.com/) Encontre o fluxo que criou para enviar pistas indo para **My Flows** a partir da barra de menuPower Automate. Selecione a elipse ao lado do nome de fluxo para ver mais opções e selecione **Editar**.


1. Selecione **Teste** no canto superior direito, selecione **i'll execute a ação do gatilho**, e, em seguida, selecione **Test**. Verá uma indicação no topo do ecrã de que o teste começou.

   ![Vou executar a opção de ação do gatilho.](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Volte para a sua aplicação Postman e selecione **Enviar**.

   ![Enviar botão](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Volte para o seu fluxo e verifique o resultado. Se tudo funcionar como esperado, verá uma mensagem que indica que o fluxo foi bem sucedido.

   ![Verificar resultados](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. Também deveria ter recebido um e-mail. Verifique a sua caixa de entrada de e-mail. 

    >[!NOTE] 
    >Se não vir um e-mail do teste, verifique as suas pastas de spam e lixo. No ecrã seguinte, notará apenas as etiquetas de campo que adicionou quando configurar a notificação de e-mail. Se este fosse um chumbo real gerado pela sua oferta, também veria a informação real do contacto com o chumbo no corpo e na linha de assunto.

   ![E-mail recebido](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configure a sua oferta para enviar pistas para o ponto final HTTPS

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal editorial, siga estes passos.

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecione a sua oferta e vá ao separador **oferta.**

1. Sob a secção de **gestão de chumbo,** selecione **Connect**. 
    ![Botão de ligação de gestão de chumbo](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

1. Na janela de detalhes da **Ligação,** selecione **HTTPS Endpoint** para o **Destino de Chumbo**. Colar o URL HTTP POST do fluxo que criou seguindo passos anteriores no campo URL do **ponto final HTTPS.**
    ![Detalhes de ligação Contacte e-mail](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. Em **contato e-mail**, insira endereços de e-mail para pessoas da sua empresa que devem receber notificações de e-mail quando um novo chumbo for recebido. Pode fornecer vários e-mails separando-os com um ponto evío.

1. Selecione **OK**.

Para se certificar de que se ligou com sucesso a um destino de chumbo, selecione o botão **Validate.** Se for bem sucedido, terá um teste de chumbo no destino principal.

>[!NOTE] 
>Tem de terminar de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.

Quando os cabos são gerados, a Microsoft envia pistas para o fluxo. Os cabos são encaminhados para o sistema CRM ou endereço de e-mail configurado.
