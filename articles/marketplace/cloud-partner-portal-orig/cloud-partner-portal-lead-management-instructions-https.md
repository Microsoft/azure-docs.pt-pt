---
title: Configure a gestão de chumbo utilizando um ponto final HTTPS / Mercado Azure
description: Aprenda a utilizar um ponto final HTTP para lidar com os cabos Microsoft AppSource e Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770208"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configure a gestão de chumbo utilizando um ponto final HTTPS

Pode utilizar um ponto final HTTPS para lidar com os cabos Microsoft AppSource e Azure Marketplace. Estes cabos podem ser escritos para um sistema de Gestão de Relacionamento com o Cliente (CRM) ou enviados como uma notificação de e-mail. Este artigo descreve como usar o serviço de automação [Microsoft Power Automate](https://powerapps.microsoft.com/automate-processes/) para configurar a gestão de chumbo.

## <a name="create-a-flow-using-microsoft-power-automate"></a>Criar um fluxo usando o Microsoft Power Automate

1. Abra a página power [automate.](https://flow.microsoft.com/) Selecione **Iniciar sessão** ou selecione **Iniciar sessão gratuitamente** para criar uma conta Flow gratuita.

1. Inscreva-se e selecione **Os meus fluxos** na barra de menus.
    > [!div class="mx-imgBorder"]
    > ![Os meus fluxos](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. Abaixo **+ Novo**, selecione + Instantâneo — a partir de **branco**.
    > [!div class="mx-imgBorder"]
    > ![Criar a partir de branco](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Nomeie o seu fluxo e, em seguida, **escolha como acionar este fluxo,** selecione **Quando um pedido HTTP for recebido**.

    > [!div class="mx-imgBorder"]
    > ![Selecione o gatilho recebido do pedido HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Clique no passo de fluxo para expandi-lo.

    > [!div class="mx-imgBorder"]
    > ![Expandir o passo de fluxo](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. Utilize um dos seguintes métodos para configurar o **Órgão de Pedido JSON Schema:**

   - Copie o [esquema JSON](#json-schema) no final deste artigo na caixa de texto **Do Corpo de Pedido JSON Schema.**
   - Selecione **Utilizar o payload de exemplo para gerar esquema**. No Enter ou pasta uma caixa de texto **de carga útil JSON,** cola no [exemplo JSON](#json-example). Selecione **Feito** para criar o esquema.

   >[!Note]
   >Nesta altura do fluxo pode ligar-se a um sistema CRM ou configurar uma notificação por e-mail.

### <a name="to-connect-to-a-crm-system"></a>Para ligar a um sistema CRM

1. Selecione **+Novo passo**.
2. Escolha o sistema CRM à sua escolha com a ação para criar um novo disco. A captura de ecrã seguinte mostra **Dynamics 365 - Criar um novo disco** como exemplo.

    ![Criar um novo registo](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Forneça o Nome da **Organização** que são as inputs de ligação para o seu conector. Selecione **Leads** da lista de abandono do Nome da **Entidade.**

    ![Selecione os cabos](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. O fluxo mostra um formulário para fornecer informações de chumbo. Pode mapear itens a partir do pedido de entrada, optando por adicionar conteúdo dinâmico. A captura de ecrã que se segue mostra o **OfferTitle** como um exemplo.

    ![Adicionar conteúdo dinâmico](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Mapeie os campos que deseja e, em seguida, selecione **Guardar** para salvar o seu fluxo.

6. Um URL HTTP POST é criado no pedido. Copie este URL e use-o como ponto final HTTPS.

    ![HTTP Post URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Para configurar a notificação por e-mail

1. Selecione **+Novo passo**.
2. Em **'Escolha uma ação**,selecione **Ações**.
3. Em **Ações**, selecione **Enviar e-mail**.

    ![Adicione uma ação de e-mail](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. Enviar **um e-mail,** configure os seguintes campos necessários:

   - **Para** - Insira pelo menos um endereço de e-mail válido.
   - **Objeto** - O fluxo dá-lhe a opção de adicionar conteúdo Dinâmico, como **leadSource** na captura do ecrã seguinte.

     ![Adicione uma ação de e-mail usando conteúdo dinâmico](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Corpo** - A partir da lista de conteúdos Dinâmicos, adicione a informação que deseja no corpo do e-mail. Por exemplo, LastName, FirstName, Email e Empresa.

   Quando terminar de configurar a notificação de e-mail, será o exemplo na seguinte captura do ecrã.

   ![Adicione uma ação de e-mail](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Selecione **Guardar** para terminar o seu fluxo.

6. Um URL HTTP POST é criado no pedido. Copie este URL e use-o como ponto final HTTPS.

    ![HTTP Post URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configure a sua oferta para enviar pistas para o ponto final HTTPS

Quando configurar as informações de gestão de chumbo para a sua oferta, selecione **HTTPS Endpoint** para o **Destino de Chumbo** e cola no URL HTTP POST copiado no passo anterior.  

![Adicionar conteúdo dinâmico](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Quando os cabos são gerados, a Microsoft envia pistas para o fluxo power automate, que são encaminhados para o sistema CRM ou endereço de e-mail configurado.

## <a name="json-schema-and-example"></a>JSON esquema e exemplo

O exemplo de teste JSON utiliza o seguinte esquema:

### <a name="json-schema"></a>Esquema jSON

``` json
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

Pode copiar e editar o seguinte exemplo JSON para utilizar como teste no seu fluxo.

### <a name="json-example"></a>Exemplo jSON

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

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, configure [os leads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) do cliente no Portal do Parceiro cloud.
