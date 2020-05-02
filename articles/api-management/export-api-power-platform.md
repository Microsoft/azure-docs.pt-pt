---
title: Exportação de APIs da Azure API Management para a Plataforma de Energia Microsoft Docs
description: Saiba exportar APIs da API Management para a Plataforma de Energia.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 9af20972a47e2d0ad20de62f1bb9d10e4d43563c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82725953"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Exportação de APIs da Azure API Management para a Plataforma de Energia 

Os desenvolvedores de cidadãos que utilizam a [Microsoft Power Platform](https://powerplatform.microsoft.com) muitas vezes precisam de atingir as capacidades de negócio que são desenvolvidas por desenvolvedores profissionais e implantadas no Azure. A [Azure API Management](https://aka.ms/apimrocks) permite que os desenvolvedores profissionais publiquem o seu serviço de backend como APIs, e facilmente exportem estas APIs para a Power Platform (Power Apps e Power Automate) como conectores personalizados para consumo por parte de desenvolvedores de cidadãos. 

Este artigo percorre os passos para exportar APIs da API Management para a Plataforma de Energia. 

## <a name="prerequisites"></a>Pré-requisitos

+ Complete o seguinte quickstart: Criar uma instância de [Gestão API Azure](get-started-create-service-instance.md)
+ Certifique-se de que existe uma API no seu caso de Gestão API que gostaria de exportar para a Plataforma de Energia
+ Certifique-se de que tem um power apps ou [ambiente](https://docs.microsoft.com/powerapps/powerapps-overview#power-apps-for-admins) power automate 

## <a name="export-an-api"></a>Exportar uma API

1. Navegue para o seu serviço de Gestão API no portal Azure e selecione **APIs** do menu.
2. Clique nos três pontos ao lado da API que pretende exportar. 
3. Selecione **Export** (Exportar).
4. Selecione **Power Apps e Power Automate**.
5. Escolha um ambiente para exportar a API para. 
6. Forneça um nome de exibição, que será usado como nome do conector personalizado.  
7. Opcional, se a API estiver protegida por um servidor OAuth 2.0, `Client ID` `Client secret`também `Authorization URL` `Token URL`terá `Refresh URL`de fornecer detalhes adicionais, incluindo, , e .  
8. Selecione **Export** (Exportar). 

Uma vez concluída a exportação, navegue para a sua App de Energia ou para o ambiente Power Automate. Verá a API como um conector personalizado.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre a Plataforma de Energia](https://powerplatform.microsoft.com/)
* [Aprenda tarefas comuns na Gestão da API seguindo os tutoriais](https://docs.microsoft.com/azure/api-management/import-and-publish)