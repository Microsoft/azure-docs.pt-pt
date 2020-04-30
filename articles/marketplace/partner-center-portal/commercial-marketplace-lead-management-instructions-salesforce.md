---
title: Configure a gestão de chumbo para a Salesforce [ Mercado Azure
description: Configure a gestão de chumbo na Salesforce para clientes do Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: e0fbb09370e198772b4fc485b3c0fe8a56da4226
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133640"
---
# <a name="configure-lead-management-for-salesforce"></a>Configure a gestão de chumbo para a Salesforce

Este artigo descreve como configurar o seu sistema Salesforce para processar pistas de vendas a partir da sua oferta de mercado comercial.

> [!NOTE]
> O Azure Marketplace não suporta listas pré-povoadas, como uma lista de valores para o campo **Country.** Certifique-se de que não há listas definidas antes de continuar. Em alternativa, pode configurar um [ponto final HTTPS](./commercial-marketplace-lead-management-instructions-https.md) ou uma tabela [Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) para receber leads.

## <a name="set-up-your-salesforce-system"></a>Configurar o seu sistema Salesforce

1. Inscreva-se na Salesforce.
1. Se estiver a utilizar a experiência de iluminação salesforce:
    1. **Selecione Configuração** na página inicial do Salesforce.

       ![Configuração da Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Na página **de Configuração,** vá às**definições** >  > de funcionalidades de **funcionalidades**de**plataforma marketing** > **Web-to-Lead**.

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

1. Se estiver a usar a experiência clássica da Salesforce:

    1. **Selecione Configuração** na página inicial do Salesforce.

       ![Configuração clássica da Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Na página **de Configuração, selecione** **Build** > **Customize** > **Leads** > **Web-to-Lead**.

        ![Salesforce clássico Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

O resto das instruções são as mesmas, não importa qual a experiência da Salesforce que estejas a usar.

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

1. Vá à página de **configuração** da Oferta para a sua oferta.
1. Selecione **Ligar** sob a secção de gestão de **chumbo.**

    ![Secção de gestão de chumbo Botão de ligação](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Na janela pop-up de detalhes da **Ligação,** selecione **Salesforce** para o **destino Lead** e colá o `oid` valor do Formulário Web-to-Lead que criou no campo de identificador da **Organização.**

1. Em **contato e-mail**, insira endereços de e-mail para pessoas da sua empresa que devem receber notificações de e-mail quando um novo chumbo for recebido. Pode fornecer vários e-mails separando-os com um ponto evío.

1. Selecione **OK**.

Para se certificar de que se ligou com sucesso a um destino de chumbo, selecione o botão **Validate.** Se for bem sucedido, terá um teste de chumbo no destino principal.

>[!NOTE]
>Tem de terminar de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.

![Detalhes de ligação janela pop-up Escolha uma caixa de destino de chumbo](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![Detalhes de ligação janela pop-up Validate Contact box](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
