---
title: Gestão de chumbo com um ponto final HTTPS - Mercado comercial da Microsoft
description: Aprenda a utilizar o Power Automate e um ponto final HTTPS para gerir os leads a partir do Microsoft AppSource e Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 5bea2cf256e30bd896957bbee0e0ad824057a569
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247187"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>Utilize um ponto final HTTPS para gerir os leads de marketplace comercial

Se o seu sistema de gestão de relacionamento com o cliente (CRM) não for suportado explicitamente no Partner Center para receber os leads microsoft AppSource e Azure Marketplace, pode utilizar um ponto final HTTPS em [Power Automamate](https://powerapps.microsoft.com/automate-processes/) para lidar com estes leads. Com um ponto final HTTPS, os leads de marketplace comercial podem ser enviados como uma notificação por e-mail ou podem ser escritos para um sistema CRM suportado pela Power Automamate.

Este artigo explica como criar um novo fluxo no Power Automamate para gerar o URL HTTP POST que utilizará para configurar leads no Partner Center. Também inclui passos para testar o seu fluxo com [o Carteiro.](https://www.getpostman.com/downloads/)

>[!NOTE]
>O conector Power Automamate utilizado nestas instruções requer uma subscrição paga da Power Automamate. Certifique-se de que o faz antes de configurar este fluxo.

## <a name="create-a-flow-by-using-power-automate"></a>Criar um fluxo utilizando o PowerUto

1. Abra a página web [Power Automamate.](https://flow.microsoft.com/) Selecione **Iniciar sessão**. Se ainda não tiver uma conta, **selecione Inscreva-se gratuitamente** para criar uma conta de Automatismo gratuito.

1. Inscreva-se e selecione **Os meus fluxos** no menu.

    ![Assine nos meus fluxos](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. Em **+ Novo**, selecione + Instantâneo — a partir de **branco.**

    ![Os meus fluxos + automatizados-- de branco](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Nomeie o seu fluxo e, em seguida, em **Escolha como desencadear este fluxo**, selecione Quando receber um pedido **HTTP**.

    ![Construa um botão de salto de janela de fluxo automatizado](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Clique no passo de fluxo para expandi-lo.

    ![Expandir o passo de fluxo](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. Utilize um dos seguintes métodos para configurar o **Corpo de Pedido JSON Schema:**

    - Copie o esquema JSON na caixa de texto **do Corpo de Pedido JSON Schema.**
    - Selecione **Utilizar o payload de exemplo para gerar esquema**. No **Enter ou colou uma caixa de texto de carga útil JSON,** cole no exemplo JSON. Selecione **Feito** para criar o esquema.

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
        "Description": {
          "id": "/properties/Description",
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

    **Exemplo JSON**
    
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
>Neste ponto da configuração, pode selecionar para ligar a um sistema CRM ou configurar uma notificação de e-mail. Siga as instruções restantes com base na sua escolha.

### <a name="connect-to-a-crm-system"></a>Ligar-se a um sistema CRM

1. Selecione **+Novo passo**.
1. Escolha o sistema CRM à sua escolha procurando-o onde diz **Conectores e ações de pesquisa.** Selecione-o no **separador Ações** com a ação para criar um novo registo. O seguinte ecrã mostra **Criar um novo disco (Dynamics 365)** como exemplo.

    ![Criar um novo registo](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. Fornecer o **Nome de Organização** associado ao sistema CRM. Selecione **Leads** da lista de drop-down do Nome da **Entidade.**

    ![Selecione pistas](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. Power Automamate mostra um formulário para fornecer informações de chumbo. Pode mapear itens a partir do pedido de entrada, optando por adicionar conteúdo dinâmico. O seguinte ecrã mostra **o OfferTitle** como exemplo.

    ![Adicionar conteúdo dinâmico](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. Mapear os campos que deseja e, em seguida, **selecione Guardar** para salvar o seu fluxo. Um URL HTTP POST é criado e está acessível na janela **Quando um pedido HTTP é recebido.** Copie este URL utilizando o controlo de cópias, que está localizado à direita do URL HTTP POST. A utilização do controlo de cópias é importante para que não perca nenhuma parte de toda a URL. Guarde este URL porque vai precisar quando configurar a gestão de chumbo no portal de publicação.

    ![Quando um pedido HTTP é recebido](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>Configurar notificação de e-mail

1. Agora que terminou o esquema JSON, selecione **+ Novo passo.**
1. Em **Escolha uma ação**, selecione **Ações**.
1. No separador **Ações,** selecione **Enviar um e-mail (Office 365 Outlook)**.

    >[!NOTE]
    >Se quiser utilizar um fornecedor de e-mail diferente, procure e selecione **Enviar por email** o artigo 200.

    ![Adicione uma ação de e-mail](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. Na janela Enviar uma janela **de e-mail,** configurar os seguintes campos necessários:

   - **Para :** Insira pelo menos um endereço de e-mail válido onde os condutores serão enviados.
   - **Assunto**: O Power Automate dá-lhe a opção de adicionar conteúdo dinâmico, como o **LeadSource** mostrado no ecrã seguinte. Comece por inserir um nome de campo. Em seguida, selecione a lista de recolha de conteúdo dinâmico da janela pop-up. 

        >[!NOTE] 
        > Quando adiciona nomes de campo, pode seguir cada nome com um cólon (:) e, em seguida, **selecione Enter** para criar uma nova linha. Depois de adicionar os seus nomes de campo, pode adicionar cada parâmetro associado da lista de escolha dinâmica.

        ![Adicione uma ação de e-mail usando conteúdo dinâmico](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Corpo**: Da lista de recolha de conteúdos dinâmicos, adicione a informação que pretende no corpo do e-mail. Por exemplo, use LastName, FirstName, Email e Company. Quando termina a configuração da notificação por e-mail, parece o exemplo no ecrã seguinte.


       ![Exemplo de notificação por e-mail](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. **Selecione Guardar** para terminar o seu fluxo. Um URL HTTP POST é criado e está acessível na janela **Quando um pedido HTTP é recebido.** Copie este URL utilizando o controlo de cópias, que está localizado à direita do URL HTTP POST. A utilização deste controlo é importante para que não perca nenhuma parte de toda a URL. Guarde este URL porque vai precisar quando configurar a gestão de chumbo no portal de publicação.

   ![HTTP POST URL](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testar

Pode testar a sua configuração com [o Carteiro.](https://app.getpostman.com/app/download/win64) Um download on-line do Carteiro está disponível para Windows. 

1. Inicie o Carteiro e selecione **Novo**  >  **Pedido** para configurar a sua ferramenta de teste. 

   ![Pedido para configurar a sua ferramenta de teste](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Preencha o formulário **'Pedido de Salvamento'** e, em seguida, guarde para a pasta que criou.

   ![Formulário de Pedido de Poupança](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. Selecione **POST** da lista de drop-down. 

   ![Teste o meu fluxo](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Cole o URL HTTP POST do fluxo que criou no Power Automamate onde diz **Enter request URL**.

   ![Cole o URL HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Volte para o [Power Automamate.](https://flow.microsoft.com/) Encontre o fluxo que criou para enviar leads indo para **My Flows** a partir da barra de menus Power Automamate. Selecione a elipse ao lado do nome de fluxo para ver mais opções e **selecione Editar**.


1. Selecione **Teste** no canto superior direito, selecione **I'll executar a ação do gatilho** e, em seguida, selecione **Test**. Verá uma indicação no topo do ecrã de que o teste começou.

   ![Vou executar a opção de ação do gatilho](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Volte para a sua aplicação Do Carteiro e selecione **Enviar.**

   ![Enviar botão](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Volte ao seu fluxo e verifique o resultado. Se tudo funcionar como esperado, verá uma mensagem que indica que o fluxo foi bem sucedido.

   ![Verificar resultados](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. Também deveria ter recebido um e-mail. Verifique a sua caixa de entrada de e-mail. 

    >[!NOTE] 
    >Se não vir um e-mail do teste, verifique as pastas de spam e lixo. No ecrã seguinte, notará apenas as etiquetas de campo que adicionou quando configurar a notificação por e-mail. Se este fosse um chumbo real gerado pela sua oferta, você também veria a informação real do contacto de chumbo no corpo e na linha de assunto.

   ![E-mail recebido](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configure a sua oferta para enviar leva ao ponto final HTTPS

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal de publicação, siga estes passos.

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecione a sua oferta e vá ao separador **Configuração oferta.**

1. Na secção **de comandos do Cliente,** selecione **Connect**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-https/customer-leads.png" alt-text="Ligações ao cliente":::

1. Na janela pop-up **de detalhes da Ligação,** selecione **HTTPS Endpoint** para o **destino principal**. Cole o URL HTTP POST a partir do fluxo que criou seguindo passos anteriores no campo **URL do ponto final HTTPS.**
    ![Detalhes de conexão Enviar por email de contacto](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. No **e-mail de Contato**, insira endereços de e-mail para pessoas da sua empresa que devem receber notificações de e-mail quando um novo chumbo é recebido. Pode fornecer vários e-mails separando-os com um ponto e vírgula.

1. Selecione **OK**.

Para se certificar de que está ligado com sucesso a um destino de chumbo, selecione o botão **Validate.** Se for bem sucedido, terá uma pista de teste no destino principal.

>[!NOTE] 
>Tem de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.

Quando os cabos são gerados, a Microsoft envia leva ao fluxo. Os leads são encaminhados para o sistema CRM ou endereço de e-mail configurado.
