---
title: Gestão de chumbo no Marketo - Mercado comercial da Microsoft
description: Aprenda a utilizar um sistema Marketo CRM para gerir os leads do Microsoft AppSource e do Azure Marketplace.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 1f3a097629f8c5f4a3f0ecefa5ee50f2d3e62162
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789883"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>Utilizar o Marketo para gerir os líderes do mercado comercial

Este artigo descreve como configurar o seu sistema Marketo CRM para processar os leads de vendas das suas ofertas no Microsoft AppSource e No Azure Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Crie o seu sistema de CRM Marketo

1. Inscreva-se no Marketo.

1. Selecione **Design Studio**.

    ![Estúdio de Design Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Selecione **Novo Formulário**.

    ![Nova forma de Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Preencha os campos necessários na caixa de diálogo **New Form** e, em seguida, selecione **Criar**.

    ![Marketo cria nova forma](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  Na página Detalhes de **Campo,** selecione **Terminar**.

    ![Forma de acabamento Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Aprove e feche.

1. No separador **MarketplaceLeadBackend,** selecione **Código Incorporado**. 

    ![Código Incorporado marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Marketo Embed Code apresenta código semelhante ao seguinte exemplo.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Copie os valores para os seguintes campos mostrados no formulário Código incorporado. Utilizará estes valores para configurar a sua oferta para receber pistas no próximo passo. Use o exemplo seguinte como um guia para obter as identificações de que precisa do exemplo do Código Marketo Incorporado.

    - ID do servidor = **ys12**
    - Id munchkin = **123-PQR-789**
    - ID do formulário = **1179**

    Outra forma de descobrir estes valores:

    - O ID do servidor encontra-se no URL `serverID.marketo.com`da sua instância Marketo, por exemplo, .
    - Obtenha o ID Munchkin da sua subscrição indo para o seu menu **Admin** > **Munchkin** no campo de ID `https://{Munchkin ID}.mktorest.com`da conta De **Munchkin,** ou a partir da primeira parte do seu subdomínio de hospedar Marketo REST API: .
    - O id do formulário é o id do formulário Código Incorporado que criou no passo 7 para direcionar os cabos do mercado.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Configure a sua oferta para enviar pistas para Marketo

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal editorial, siga estes passos. 

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecione a sua oferta e vá ao separador **oferta.**

1. Sob a secção de **gestão de chumbo,** selecione **Connect**. 

    ![Secção de gestão de chumbo Botão de ligação](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Na janela pop-up de detalhes da **Ligação,** selecione **Marketo** para o **destino Lead**.

    ![Escolha um destino de chumbo](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Forneça o ID do **Servidor,** **o ID da conta Munchkin**e o ID do **formulário**.

    > [!NOTE]
    > Tem de terminar de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta. 

1. Em **contato e-mail**, insira endereços de e-mail para pessoas da sua empresa que devem receber notificações de e-mail quando um novo chumbo for recebido. Pode fornecer vários endereços de e-mail separando-os com um ponto e vírgula.

1. Selecione **OK**.

   Para se certificar de que se ligou com sucesso a um destino de chumbo, selecione **Validate**. Se for bem sucedido, terá um teste de chumbo no destino principal.

   ![Detalhes de ligação janela pop-up](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
