---
title: Marketo | Microsoft Docs
description: Configure a gestão de oportunidades potenciais para o Marketo.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 482d7a9662a79503bb2b197d5a6c63c9fa3c1c96
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113117"
---
# <a name="configure-lead-management-in-marketo"></a>Configurar a gestão de oportunidades potenciais no Marketo

Este artigo descreve como configurar o Marketo para lidar com oportunidades potenciais de venda de Microsoft.

1. Inicie sessão no Marketo.
2. Selecione **Design Studio**.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Selecione **novo formulário**.
    ![Novo formulário do Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Preencha os campos obrigatórios no novo formulário e, em seguida, selecione **criar**.
    ![Marketo criar novo formulário](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  No campo de detalhes, selecione **concluir**.
    ![Formulário de conclusão do Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Aprovar e fechar.

6.  No separador MarketplaceLeadBacked, selecione **incorporar código**.
    ![Opção de código de incorporação do Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Código de incorporação do Marketo apresenta o código semelhante ao seguinte exemplo.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Copie os valores mostrados no código de incorporação para que pode configurar o **Id do servidor**, **Munchkin Id**, e **Id de formulário** os campos do Marketo no Portal de parceiros de nuvem.

Utilize o exemplo seguinte como guia para obter os Ids de que precisa do exemplo de código de incorporação do Marketo.

- Server Id = **ys12**
- Munchkin Id = **123-PQR-789**
- Form Id = **1179**\
