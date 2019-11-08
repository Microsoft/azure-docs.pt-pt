---
title: Ponto de extremidade HTTPS | Azure Marketplace
description: Configure o gerenciamento de Lead para um ponto de extremidade HTTPS.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: pabutler
ms.openlocfilehash: 817e431f5386b10345d414190e8bda0954ef2aca
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825216"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configurar o gerenciamento de leads usando um ponto de extremidade HTTPS

Você pode usar um ponto de extremidade HTTPS para lidar com leads do Azure Marketplace e AppSource. Esses leads podem ser gravados no que podem ser gravados em um sistema CRM (gerenciamento de relacionamento com o cliente) ou enviados como uma notificação por email. Este artigo descreve como configurar o gerenciamento de leads usando o serviço de automação de [Microsoft Flow](https://powerapps.microsoft.com/automate-processes/) .

## <a name="create-a-flow-using-microsoft-flow"></a>Criar um fluxo usando Microsoft Flow

1. Abra a página da Web do [Flow](https://flow.microsoft.com/) . Selecione **entrar** ou selecione **inscrever-se gratuitamente** para criar uma conta de fluxo livre.

2. Entre e selecione **meus fluxos** na barra de menus.

    ![Meus fluxos](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. Selecione **+ criar em branco**.

    ![criar a partir de em branco](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. Selecione **criar em branco**.

    ![criar a partir de em branco](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. No campo **conectores de pesquisa e gatilhos** , digite "solicitação" para localizar o conector de solicitação.
6. Em **gatilhos**, selecione **quando uma solicitação HTTP é recebida**. 

    ![Selecione o gatilho solicitação HTTP recebida](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. Use uma das seguintes etapas para configurar o **esquema JSON do corpo da solicitação**:

   - Copie o [esquema JSON](#json-schema) no final deste artigo na caixa de texto **esquema JSON do corpo da solicitação** .
   - Selecione **Utilizar o payload de exemplo para gerar esquema**. Na caixa de texto **Inserir ou colar um exemplo de carga JSON** , Cole o [exemplo de JSON](#json-example). Selecione **concluído** para criar o esquema.

   >[!Note]
   >Neste ponto do fluxo, você pode se conectar a um sistema CRM ou configurar uma notificação por email.

### <a name="to-connect-to-a-crm-system"></a>Para se conectar a um sistema CRM

1. Selecione **+ nova etapa**.
2. Escolha o sistema CRM de sua escolha com a ação para criar um novo registro. A captura de tela a seguir mostra o **Dynamics 365 – criar um novo registro** como um exemplo.

    ![Criar um novo registro](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Forneça o **nome da organização** que são as entradas de conexão para o conector. Selecione **clientes potenciais** na lista suspensa **nome da entidade** .

    ![Selecionar clientes potenciais](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Flow mostra um formulário para fornecer informações de Lead. Você pode mapear itens da solicitação de entrada escolhendo adicionar conteúdo dinâmico. A captura de tela a seguir mostra **OfferTitle** como um exemplo.

    ![Adicionar conteúdo dinâmico](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Mapeie os campos desejados e, em seguida, selecione **salvar** para salvar o fluxo.

6. Uma URL HTTP POST é criada na solicitação. Copie essa URL e use-a como o ponto de extremidade HTTPS.

    ![URL HTTP post](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Para configurar a notificação por email

1. Selecione **+ nova etapa**.
2. Em **escolher uma ação**, selecione **ações**.
3. Em **Ações**, selecione **Enviar e-mail**.

    ![Adicionar uma ação de email](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. Em **enviar um email**, configure os seguintes campos obrigatórios:

   - **Para** inserir pelo menos um endereço de email válido.
   - O fluxo de **assunto** oferece a opção de adicionar conteúdo dinâmico, como o **leadname** na captura de tela a seguir.

     ![Adicionar uma ação de email usando conteúdo dinâmico](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Corpo** -na lista de conteúdo dinâmico, adicione as informações desejadas no corpo do email. Por exemplo, LastName, FirstName, email e Company.

   Quando você terminar de configurar a notificação por email, ele se parecerá com o exemplo na captura de tela a seguir.

   ![Adicionar uma ação de email](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Selecione **salvar** para concluir o fluxo.
6. Uma URL HTTP POST é criada na solicitação. Copie essa URL e use-a como o ponto de extremidade HTTPS.

    ![URL HTTP post](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configurar sua oferta para enviar clientes potenciais ao ponto de extremidade HTTPS

Ao configurar as informações de gerenciamento de Lead para sua oferta, selecione **ponto de extremidade https** para o **destino do cliente potencial** e cole a URL http post que você copiou na etapa anterior.  

![Adicionar conteúdo dinâmico](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Quando os clientes potenciais são gerados, a Microsoft envia os clientes potenciais para o fluxo, que são roteados para o sistema CRM ou endereço de email que você configurou.

## <a name="json-schema-and-example"></a>Esquema JSON e exemplo

O exemplo de teste JSON usa o esquema a seguir:

### <a name="json-schema"></a>Esquema JSON

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

Você pode copiar e editar o exemplo JSON a seguir para usar como um teste em seu MS Flow.

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

## <a name="next-steps"></a>Passos seguintes

Se você ainda não fez isso, configure [clientes potenciais](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) no portal do Cloud Partner.
