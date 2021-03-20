---
title: Gestão de chumbo em Salesforce - Mercado comercial da Microsoft
description: Saiba como utilizar a Salesforce para configurar leads para o Microsoft AppSource e para o Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 73caf848ab5c6f8e973469066ce4612a075a52f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94489323"
---
# <a name="configure-lead-management-for-salesforce"></a>Configure a gestão de chumbo para a Salesforce

Este artigo descreve como configurar o seu sistema Salesforce para processar os leads de vendas das suas ofertas no Microsoft AppSource e no Azure Marketplace.

> [!NOTE]
> O Azure Marketplace não suporta listas pré-epulsificadas, como uma lista de valores para o **campo Country.** Certifique-se de que não existem listas definidas antes de continuar. Em alternativa, pode configurar um [ponto final HTTPS](./commercial-marketplace-lead-management-instructions-https.md) ou uma [tabela Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) para receber leads.

## <a name="set-up-your-salesforce-system"></a>Configurar o seu sistema Salesforce

1. Inscreva-se na Salesforce.
1. Navegue para as definições **Web-to-Lead.** 
    
    Se estiver a utilizar a experiência de iluminação Salesforce
    1. Selecione **Configuração** na página inicial do Salesforce.

       ![Configuração salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Na página **configuração,** aceda a Definições de Funcionalidade **de Ferramentas** de Plataforma  >    >  **Marketing**  >  **Web-to-Lead**.

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Se estiver a utilizar a experiência clássica da Salesforce:

    1. Selecione **Configuração** na página inicial do Salesforce.

       ![Configuração clássica da Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Na página **Configuração,** selecione **Build**  >  **Customize**  >  **Leads**  >  **Web-to-Lead**.

        ![Salesforce clássico Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   Os passos restantes são os mesmos para ambas as experiências da Salesforce.

1. Na página **De Configuração Web-to-Lead,** selecione o botão **'Criar Formulário Web-to-Lead'.**
1. Na **configuração Web-to-Lead**, selecione **Criar um formulário Web-to-Lead**.

    ![Configuração Web-to-Lead da Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. No **Criar um formulário Web-to-Lead, certifique-se** de que a `Include reCAPTCHA in HTML` definição está limpa e selecione **Gerar**.

    ![Salesforce Criar um painel de formulário web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Será apresentado com algum texto HTML. Procure o texto "oid" e copie o **valor "oid"** do texto HTML (apenas o texto entre as aspas) e guarde-o. Você vai colar este valor no campo **Identifier organização** no portal de publicação.

    ![Salesforce Criar um formulário web-to-lead mostrando o valor "oid" HTML](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Selecione **Terminado**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Configure a sua oferta de envio leva à Salesforce

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal de publicação, siga estes passos.

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecione a sua oferta e vá ao separador **Configuração oferta.**

1. Na secção **de comandos do Cliente,** selecione **Connect**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="Ligações ao cliente":::

1. Na janela pop-up de detalhes de **Ligação,** selecione **Salesforce** para o **destino Lead** e cole o `oid` valor do Formulário Web-to-Lead que criou no campo identificador da **Organização.**

    ![Detalhes de conexão janela pop-up Validar caixa de e-mail de contato](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. No **e-mail de Contato**, insira endereços de e-mail para pessoas da sua empresa que devem receber notificações de e-mail quando um novo chumbo é recebido. Pode fornecer vários e-mails separando-os com um ponto e vírgula.

1. Selecione **OK**.

Para se certificar de que está ligado com sucesso a um destino de chumbo, **selecione Validate**. Se for bem sucedido, terá uma pista de teste no destino principal.

>[!NOTE]
>Tem de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.
