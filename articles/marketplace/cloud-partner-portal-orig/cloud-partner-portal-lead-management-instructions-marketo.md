---
title: Configurar gerenciamento de leads no Marketo | Azure Marketplace
description: Configure o gerenciamento de leads para clientes do Marketo para o Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 7949507c8c7ef57cded25cde8579c1945aa93a81
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825207"
---
# <a name="configure-lead-management-in-marketo"></a>Configurar gerenciamento de leads no Marketo

Este artigo descreve como configurar o Marketo para lidar com leads de vendas da Microsoft.

1. Entre no Marketo.
2. Selecione **Design Studio**.
    ![Studio de design do Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Selecione **novo formulário**.
    novo formulário do ![Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Preencha os campos obrigatórios no novo formulário e, em seguida, selecione **criar**.
    ![Marketo criar novo formulário](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  Em detalhes do campo, selecione **concluir**.
    ](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png) formulário de conclusão ![Marketo

5.  Aprove e feche.

6.  Na guia MarketplaceLeadBacked, selecione **código de inserção**.
    ![opção de código de inserção do Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  O código de inserção do marketo exibe um código semelhante ao exemplo a seguir.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Copie os valores mostrados no código de inserção para que você possa configurar a **ID do servidor**, a **ID do Munchkin**e a ID do **formulário** nos campos do marketo no portal do Cloud Partner.

Use o exemplo a seguir como um guia para obter as IDs necessárias do exemplo de código de inserção do Marketo.

- ID do servidor = **ys12**
- ID de Munchkin = **123-PQR-789**
- ID do formulário = **1179**\
