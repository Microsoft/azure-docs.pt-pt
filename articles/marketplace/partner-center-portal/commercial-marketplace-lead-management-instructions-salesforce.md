---
title: Configurar gerenciamento de leads para Salesforce | Azure Marketplace
description: Configure o gerenciamento de leads no Salesforce para clientes do Azure Marketplace.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: d941a7902dd374a931147bdaa8f015c46268ee8a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69901990"
---
# <a name="configure-lead-management-for-salesforce"></a>Configurar o gerenciamento de leads para o Salesforce

Este artigo descreve como configurar seu sistema Salesforce para processar clientes potenciais da sua oferta do Marketplace.

## <a name="set-up-your-salesforce-system"></a>Configurar seu sistema Salesforce

1. Entre no Salesforce.
2. Se você estiver usando a experiência de iluminação do Salesforce.
    1. Selecione **instalação** na home page do Salesforce.
    ![Configuração do Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Na página de instalação, navegue pelo painel de navegação esquerdo para **ferramentas de plataforma-> configurações de recurso-> marketing-> Web-to-Lead**.
    ![Web-to-Lead do Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Se você estiver usando a experiência clássica do Salesforce:
    1. Selecione **instalação** na home page do Salesforce.
    ![Configuração do Salesforce clássico](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Na página de instalação, navegue pelo painel de navegação à esquerda para **Build-> Custom-> leads-> Web-to-Lead**.
    ![Web-to-Lead clássico do Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

O restante das instruções é o mesmo, independentemente da experiência do Salesforce que você está usando.

4. Na **página configuração da Web para o cliente potencial**, selecione o botão **criar formulário da Web para o Lead** .
5. Na **configuração da Web para o Lead**, selecione **criar formulário da Web para o Lead**.
    ![Salesforce-configuração da Web para o Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. No **formulário criar um Web-to-Lead**, verifique se `the Include reCAPTCHA in HTML` a configuração está desmarcada e selecione **gerar**. 
    ![Salesforce-criar um formulário da Web para o Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Você verá um texto HTML. Procure o texto "OID" e copie o **valor de OID** do texto HTML (somente o texto entre aspas) e salve-o. Você colará esse valor no campo **identificador da organização** no portal de publicação.
    ![Salesforce-criar um formulário da Web para o Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. **Concluído**selecionado.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Configurar sua oferta para enviar clientes potenciais ao Salesforce

Quando você estiver pronto para configurar as informações de gerenciamento de Lead para sua oferta no portal de publicação, siga as etapas abaixo:

1. Navegue até a página de **instalação da oferta** para sua oferta.
1. Selecione **conectar** na seção Gerenciamento de leads.
    ![Gerenciamento de Lead-conectar](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Na janela pop-up detalhes da conexão, selecione **Salesforce** para o **destino do cliente potencial** e `oid` Cole no formulário da Web para o Lead que você criou seguindo as etapas anteriores no campo **identificador da organização** .

1. Selecione **Guardar**. 

    >[!Note]
    >Você deve concluir a configuração do restante da oferta e publicá-la antes de receber clientes potenciais para a oferta.

    ![Detalhes da conexão-escolher um destino de cliente potencial](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![Detalhes da conexão-escolher um destino de cliente potencial](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)