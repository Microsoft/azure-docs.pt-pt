---
title: Gestão de chumbo no Marketo - Mercado comercial da Microsoft
description: Saiba como utilizar um sistema DE CRM do Marketo para gerir os leads a partir do Microsoft AppSource e do Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: cbd12481312d310add15bf3d41e21e9c416f1c39
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491080"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>Use o Marketo para gerir os líderes do mercado comercial

Este artigo descreve como configurar o seu sistema DemS de Marketo para processar os leads de vendas das suas ofertas no Microsoft AppSource e no Azure Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Configurar o seu sistema Desemis de CRM do Marketo

1. Inscreva-se no Marketo.

1. Selecione **Design Studio**.

    ![Estúdio de Design de Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Selecione **novo formulário**.

    ![Novo formulário de Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Preencha os campos necessários na caixa de diálogo **New Form** e, em seguida, selecione **Criar**.

    ![Marketo criar nova forma](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  Na página Detalhes de **Campo,** **selecione Terminar**.

    ![Forma de acabamento marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Aprovar e fechar.

1. No **separador MarketplaceLeadBackend,** **selecione Código Incorporado**. 

    ![Código De Incorporação de Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. O Código Incorporado do Marketo apresenta código semelhante ao seguinte exemplo.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Copie os valores para os seguintes campos indicados no formulário Código Incorporado. Você usará estes valores para configurar a sua oferta para receber pistas no próximo passo. Use o próximo exemplo como guia para obter os IDs de que precisa no exemplo do Código De Incorporação de Marketo.

    - ID do servidor = **ys12**
    - ID munchkin = **123-PQR-789**
    - ID do formulário = **1179**

    Outra forma de descobrir estes valores:

    - O ID do servidor encontra-se no URL do seu exemplo marketo, por exemplo, `serverID.marketo.com` .
    - Obtenha o ID munchkin da sua subscrição indo para o seu menu **Admin**  >  **Munchkin** no campo **de ID da conta Munchkin,** ou a partir da primeira parte do subdomínio de anfitrião da API Marketo REST: `https://{Munchkin ID}.mktorest.com` .
    - O ID do formulário é o ID do formulário de Código Incorporado que criou no passo 7 para rotas de condutores do mercado.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Configure a sua oferta de envio leva ao Marketo

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal de publicação, siga estes passos. 

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecione a sua oferta e vá ao separador **Configuração oferta.**

1. Na secção **de comandos do Cliente,** selecione **Connect**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-marketo/customer-leads.png" alt-text="Ligações ao cliente":::

1. Na janela pop-up **de detalhes da Ligação,** selecione **Marketo** para o **destino Lead**.

    ![Escolha um destino de chumbo](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Forneça o **ID** do servidor, o ID da **conta de Munchkin** e **o ID do formulário**.

    > [!NOTE]
    > Tem de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta. 

1. No **e-mail de Contato** , insira endereços de e-mail para pessoas da sua empresa que devem receber notificações de e-mail quando um novo chumbo é recebido. Pode fornecer vários endereços de e-mail separando-os com um ponto e vírgula.

1. Selecione **OK**.

   Para se certificar de que está ligado com sucesso a um destino de chumbo, **selecione Validate**. Se for bem sucedido, terá uma pista de teste no destino principal.

   ![Janela pop-up de detalhes de conexão](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
