---
title: Configure a gestão de chumbo no Marketo Mercado Azure
description: Configure a gestão de chumbo para marketo para clientes do mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 9fa05eae2d297cbd6ae7243d191cae5a7a3f990e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80288534"
---
# <a name="configure-lead-management-in-marketo"></a>Configure a gestão de chumbo em Marketo

Este artigo descreve como configurar o Marketo para lidar com os leads de vendas da Microsoft.

1. Inscreva-se no Marketo.
2. Selecione **Design Studio**.
    ![Estúdio de Design Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Selecione **Novo Formulário**.
    ![Nova forma de Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Preencha os campos necessários no Formulário Novo e, em seguida, selecione **Criar**.
    ![Marketo cria nova forma](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  Em detalhes de campo, selecione **Terminar**.
    ![Forma de acabamento Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Aprovar e Fechar.

6.  No separador MarketplaceLeadBacked, selecione **Código Incorporado**.
    ![Opção de código incorporado marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Marketo Embed Code apresenta código semelhante ao seguinte exemplo.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Copie os valores mostrados no Código Incorporado para que possa configurar o **Id do Servidor,** **o Id de Munchkin**e **o Id de Formulário** nos campos Marketo no Portal do Parceiro cloud.

Use o exemplo seguinte como um guia para obter os Ids que precisa do exemplo do Código Marketo Incorporado.

- Id do servidor = **ys12**
- Munchkin Id = **123-PQR-789**
- Formulário Id = **1179**
