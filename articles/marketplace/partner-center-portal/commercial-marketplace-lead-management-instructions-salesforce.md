---
title: Gestão de chumbo na Salesforce - Mercado comercial da Microsoft
description: Saiba como usar o Salesforce para configurar os leads para o Microsoft AppSource e o Azure Marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 7d64b8914fa0b109dfc662a97a7f84d94e3491ec
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789713"
---
# <a name="configure-lead-management-for-salesforce"></a>Configure a gestão de chumbo para a Salesforce

Este artigo descreve como configurar o seu sistema Salesforce para processar os leads de vendas das suas ofertas no Microsoft AppSource e No Azure Marketplace.

> [!NOTE]
> O Azure Marketplace não suporta listas pré-povoadas, como uma lista de valores para o campo **Country.** Certifique-se de que não há listas definidas antes de continuar. Em alternativa, pode configurar um [ponto final HTTPS](./commercial-marketplace-lead-management-instructions-https.md) ou uma tabela [Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) para receber leads.

## <a name="set-up-your-salesforce-system"></a>Configurar o seu sistema Salesforce

1. Inscreva-se na Salesforce.
1. Navegue para as definições **Web-to-Lead.** 
    
    Se estiver a usar a experiência de iluminação Salesforce
    1. **Selecione Configuração** na página inicial do Salesforce.

       ![Configuração da Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Na página **de Configuração,** vá às**definições** >  > de funcionalidades de **funcionalidades**de**plataforma marketing** > **Web-to-Lead**.

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Se estiver a usar a experiência clássica da Salesforce:

    1. **Selecione Configuração** na página inicial do Salesforce.

       ![Configuração clássica da Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Na página **de Configuração, selecione** **Build** > **Customize** > **Leads** > **Web-to-Lead**.

        ![Salesforce clássico Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   Os passos restantes são os mesmos para ambas as experiências da Salesforce.

1. Na página de **configuração Web-to-Lead,** selecione o botão Criar formulário **Web-to-Lead.**
1. Na **configuração Web-to-Lead,** selecione **Criar um formulário Web-to-Lead**.

    ![Configuração Web-to-Lead salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. Em **Criar um formulário Web-to-Lead, certifique-se**de que a `Include reCAPTCHA in HTML` definição está limpa e selecione **Generate**.

    ![Salesforce Criar um painel de formulário web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Será apresentado com um texto HTML. Procure o texto "oid" e copie o **valor "oid"** do texto HTML (apenas o texto entre aspas) e guarde-o. Você vai colar este valor no campo de **identificador** de organização no portal editorial.

    ![Salesforce Criar um formulário Web-to-Lead mostrando o valor "oid" HTML](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Selecione **Finished**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Configure a sua oferta para enviar pistas para a Salesforce

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal editorial, siga estes passos.

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecione a sua oferta e vá ao separador **oferta.**

1. Sob a secção de **gestão de chumbo,** selecione **Connect**. 

    ![Secção de gestão de chumbo Botão de ligação](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Na janela pop-up de detalhes da **Ligação,** selecione **Salesforce** para o **destino Lead** e colá o `oid` valor do Formulário Web-to-Lead que criou no campo de identificador da **Organização.**

    ![Detalhes de ligação janela pop-up Validate Contact box](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. Em **contato e-mail**, insira endereços de e-mail para pessoas da sua empresa que devem receber notificações de e-mail quando um novo chumbo for recebido. Pode fornecer vários e-mails separando-os com um ponto evío.

1. Selecione **OK**.

Para se certificar de que se ligou com sucesso a um destino de chumbo, selecione **Validate**. Se for bem sucedido, terá um teste de chumbo no destino principal.

>[!NOTE]
>Tem de terminar de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.
