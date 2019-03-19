---
title: Ponto final de HTTPS | Documentos da Microsoft
description: Configure a gestão de oportunidades potenciais para um ponto final HTTPS.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: pbutlerm
ms.openlocfilehash: 905ce1eca9996140da5f9d33aa7f9749a4ef7034
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894126"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configurar a gestão de oportunidades potenciais com um ponto final HTTPS

Pode utilizar um ponto final HTTPS para lidar com o Azure Marketplace e oportunidades potenciais do AppSource. Podem ser gravadas destas oportunidades potenciais que podem ser escritos para um sistema de gerenciamento de relação do cliente (CRM) ou enviados como uma notificação por e-mail. Este artigo descreve como configurar a gestão de oportunidades potenciais com o [Microsoft Flow](https://powerapps.microsoft.com/automate-processes/) serviço de automatização.

## <a name="create-a-flow-using-microsoft-flow"></a>Criar um fluxo com o Microsoft Flow

1. Abra o [fluxo](https://flow.microsoft.com/) página Web. Selecione **iniciar sessão** ou selecione **inscrever-se gratuitamente** para criar uma conta gratuita do fluxo.

2. Iniciar sessão e selecione **os meus fluxos** na barra de menus.

    ![Meus fluxos](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. Selecione **+ criar do zero**.

    ![Criar do zero](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. Selecione **criar do zero**.

    ![Criar do zero](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. Na **procurar conectores e acionadores** , digite "Pedir" para encontrar o conector do pedido.
6. Sob **Acionadores**, selecione **pedido de HTTP de quando é recebido**. 

    ![Selecione o acionador de pedido recebido de HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. Utilize um dos seguintes passos para configurar o **pedir esquema JSON do corpo**:

   - Copiar o [esquema JSON](#JSON-schema) no final deste artigo para o **pedir esquema JSON do corpo** caixa de texto.
   - Selecione **Utilizar o payload de exemplo para gerar esquema**. Na **introduza ou cole um payload JSON de exemplo** caixa de texto, cole a [exemplo de JSON](#JSON-example). Selecione **feito** para criar o esquema.

   >[!Note]
   >Neste ponto no fluxo pode ligar a um sistema CRM ou configurar uma notificação por e-mail.

### <a name="to-connect-to-a-crm-system"></a>Para ligar a um sistema CRM

1. Selecione **+ novo passo**.
2. Escolha o sistema CRM à sua escolha com a ação para criar um novo registo. Captura de ecrã seguinte mostra **Dynamics 365 - criar um novo registo** como exemplo.

    ![Criar uma nova palavra-passe](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Forneça o **nome da organização** é que as entradas de ligação para o conector. Selecione **leva** partir do **nome da entidade** lista suspensa.

    ![Selecionar oportunidades potenciais](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Fluxo mostra um formulário para fornecer informações de oportunidades potenciais. Pode mapear os itens no pedido de entrada ao optar por adicionar conteúdo dinâmico. Captura de ecrã seguinte mostra **OfferTitle** como exemplo.

    ![Adicionar conteúdo dinâmico](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Mapear os campos que pretende e, em seguida, selecione **guardar** para guardar o fluxo.

6. Um URL do HTTP POST é criado no pedido. Copie este URL e utilize-o como o ponto final HTTPS.

    ![URL de HTTP Post](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Para configurar a notificação por e-mail

1. Selecione **+ novo passo**.
2. Sob **escolher uma ação**, selecione **ações**.
3. Em **Ações**, selecione **Enviar e-mail**.

    ![Adicionar uma ação de e-mail](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. Na **enviar um e-mail**, configure os seguintes campos obrigatórios:

   - **Para** -Introduza pelo menos um endereço de e-mail válido.
   - **Assunto** -fluxo dá-lhe a opção para adicionar conteúdo dinâmico, como **LeadSource** na captura de ecrã seguinte.

     ![Adicione uma ação de e-mail com conteúdo dinâmico](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Corpo** – na lista de conteúdo dinâmico, adicione as informações que pretende no corpo do e-mail. Por exemplo, LastName, FirstName, E-Mail, e da empresa.

   Quando tiver terminado de configurar a notificação de e-mail, terá o aspeto semelhante ao exemplo na captura de ecrã seguinte.

   ![Adicionar uma ação de e-mail](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Selecione **guardar** para concluir o fluxo.
6. Um URL do HTTP POST é criado no pedido. Copie este URL e utilize-o como o ponto final HTTPS.

    ![URL de HTTP Post](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configure a sua oferta para enviar as oportunidades potenciais para o ponto final HTTPS

Quando configurar as informações de gestão de oportunidades potenciais para a sua oferta, selecione **ponto final de HTTPS** para o **originar destino** e cole o URL de POST de HTTP que copiou no passo anterior.  

![Adicionar conteúdo dinâmico](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Quando são gerados oportunidades potenciais, a Microsoft envia oportunidades potenciais para o fluxo, que são roteadas para o endereço de e-mail ou de sistema CRM que configurou.

## <a name="json-schema-and-example"></a>Esquema JSON e de exemplo

O exemplo de teste JSON utiliza o esquema seguinte:

### <a name="json-schema"></a>JSON schema

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

Pode copiar e editar o seguinte exemplo JSON para utilizar como um teste no seu MS Flow.

### <a name="json-example"></a>Exemplo de JSON

```json
{
"OfferTitle": "Test Microsoft",
"LeadSource": "Test run through MS Flow",
"UserDetails": {
"Company": "Contoso",
"Country": "USA",
"Email": "someone@contoso.com",
"FirstName": "Some",
"LastName": "One",
"Phone": "16175555555",
"Title": "Esquire"
}
}
```

## <a name="next-steps"></a>Passos Seguintes

Se ainda não o fez, configure o cliente [leva](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) no Portal de parceiros de nuvem.
