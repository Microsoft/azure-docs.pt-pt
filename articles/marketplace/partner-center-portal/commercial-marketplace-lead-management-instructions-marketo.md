---
title: Configure a gestão de chumbo no Marketo Mercado Azure
description: Configure a gestão de chumbo para marketo para clientes do mercado Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 04eae529efcf9509d8cd43a7629bb0b76b593cd0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252504"
---
# <a name="configure-lead-management-in-marketo"></a>Configure a gestão de chumbo em Marketo

Este artigo descreve como configurar o seu sistema de CRM Marketo para processar pistas de vendas a partir da sua oferta de mercado.

## <a name="set-up-your-marketo-crm-system"></a>Crie o seu sistema de CRM Marketo

1. Inscreva-se no Marketo.
2. Selecione **Design Studio**.
    ![Estúdio de Design Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Selecione **Novo Formulário**.
    ![Nova forma de Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Preencha os campos necessários no Formulário Novo e, em seguida, selecione **Criar**.
    ![Marketo cria nova forma](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  Em detalhes de campo, selecione **Terminar**.
    ![Forma de acabamento Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Aprovar e Fechar.

7. No separador *MarketplaceLeadBacked,* selecione **Código Incorporado**. 

    ![Código de incorporação](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo Embed Code apresenta código semelhante ao seguinte exemplo.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Copie os valores para os campos abaixo indicados no formulário Código Incorporado. Utilizará estes valores para configurar a sua oferta para receber pistas no próximo passo. Use o exemplo seguinte como um guia para obter os Ids que precisa do exemplo do Código Marketo Incorporado.

    - ID do servidor = **ys12**
    - Id munchkin = **123-PQR-789**
    - ID do formulário = **1179**

    **Outra forma de descobrir estes valores**

    - O ID do servidor encontra-se no URL da`serverID.marketo.com`sua instância marketo, por exemplo, ".
    - Obtenha o ID de Munching da sua subscrição indo ao seu menu de>Munchkin no campo "Munchkin Account `https://{Munchkin ID}.mktorest.com`ID", ou a partir da primeira parte do seu subdomínio de anfitrião Marketo REST API: .
    - O id do formulário é o id do formulário Código Incorporado que criou no passo 7 para direcionar os cabos do nosso mercado.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Configure a sua oferta para enviar pistas para Marketo

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal editorial, siga os passos abaixo: 

1. Navegue na página de **configuração** da Oferta para a sua oferta.
1. Selecione **Ligar** sob a secção de Gestão de Chumbo. 

    ![Gestão de chumbo - Connect](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Na janela pop-up de detalhes da Ligação, selecione **Marketo** para o Destino Principal.

    ![Escolha um destino de chumbo](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Forneça o ID do **Servidor,** **o ID da conta de mastigação,** e o ID do **formulário**.

    >[!Note]
    >Tem de terminar de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta. 

5. **E-mail de contacto** - Forneça e-mails para pessoas da sua empresa que devam receber notificações de e-mail quando um novo chumbo for recebido. Pode fornecer vários e-mails separando-os com um ponto evío.
6. Selecione **OK**.

Para se certificar de que se ligou com sucesso a um destino de chumbo, clique no botão de validação. Se for bem sucedido, terá um chumbo de teste no destino principal.

![Detalhes da ligação](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)