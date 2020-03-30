---
title: Configure a gestão de chumbo para a Salesforce [ Mercado Azure
description: Configure a gestão de chumbo na Salesforce para clientes do Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: dsindona
ms.openlocfilehash: 1720026b4beff941b02a60cd1c755a043d66bdb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281498"
---
# <a name="configure-lead-management-for-salesforce"></a>Configure a gestão de chumbo para a Salesforce

Este artigo descreve como configurar o seu sistema Salesforce para processar pistas de vendas a partir da sua oferta de mercado.

## <a name="set-up-your-salesforce-system"></a>Configurar o seu sistema Salesforce

1. Inscreva-se na Salesforce.
2. Se estiver a utilizar a experiência de iluminação Salesforce.
    1. **Selecione Configuração** a partir da página inicial da Salesforce.
    ![Configuração da Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. A partir da página de Configuração, navegue através da navegação esquerda para **as definições de funcionalidades >de funcionalidades >>marketing->Web-to-Lead**.
    ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Se estiver a utilizar a experiência Salesforce Classic:
    1. **Selecione Configuração** a partir da página inicial da Salesforce.
    ![Configuração clássica da Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. A partir da página de Configuração, navegue através da navegação à esquerda para **construir ->personalize->leads->Web-to-Lead**.
    ![Salesforce clássico web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

As restantes instruções são as mesmas, independentemente da experiência da Salesforce que está a utilizar.

4. Na página de **configuração Web-to-Lead,** selecione o botão **"Formulário Web-a-Lead".**
5. Na **configuração Web-to-Lead,** selecione **Create Web-to-Lead Form**.
    ![Salesforce - Configuração Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. No **formulário Criar um Formulário Web-a-Lead, certifique-se**de que `the Include reCAPTCHA in HTML` a definição está desmarcada e selecione **Generate**. 
    ![Salesforce - Criar um formulário Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Será apresentado com um texto HTML. Procure o texto "oid" e copie o **valor oid** a partir do texto HTML (apenas o texto entre aspas) e guarde-o. Você vai colar este valor no campo de **identificador** de organização no portal editorial.
    ![Salesforce - Criar um formulário Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. **Acabamento Selecionado**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Configure a sua oferta para enviar pistas para a Salesforce

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal editorial, siga os passos abaixo:

1. Navegue na página de **configuração** da Oferta para a sua oferta.
1. Selecione **Ligar** sob a secção de Gestão de Chumbo.
    ![Gestão de chumbo - Connect](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Na janela pop-up de detalhes da Ligação, selecione `oid` **Salesforce** para o Destino De **Chumbo** e cola no formulário web-to-lead que criou seguindo passos anteriores no campo de identificador da **Organização.**

1. Selecione **Guardar**. 

    >[!Note]
    >Tem de terminar de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.

    ![Detalhes da ligação - Escolha um destino principal](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![Detalhes da ligação - Escolha um destino principal](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)
