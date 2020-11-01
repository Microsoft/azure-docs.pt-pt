---
title: ApIs de exportação da Azure API Management para a Plataforma De Energia Microsoft Docs
description: Saiba como exportar APIs da API Management para a Plataforma de Energia.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 7c4d32dd63120a52fd7351977943574455e5cfad
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146659"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>APIs de exportação da Azure API Management para a Plataforma de Energia 

Os desenvolvedores de cidadãos que usam a Plataforma Microsoft [Power](https://powerplatform.microsoft.com) muitas vezes precisam de alcançar as capacidades de negócio que são desenvolvidas por desenvolvedores profissionais e implementadas no Azure. [A Azure API Management](https://aka.ms/apimrocks) permite que os desenvolvedores profissionais publiquem o seu serviço de backend como APIs, e facilmente exportem estas APIs para a Power Platform (Power Apps e Power Automamate) como conectores personalizados para consumo por desenvolvedores de cidadãos. 

Este artigo percorre os passos para exportar APIs da API Management para a Plataforma de Energia. 

## <a name="prerequisites"></a>Pré-requisitos

+ Complete o seguinte quickstart: [Criar uma instância de gestão API Azure](get-started-create-service-instance.md)
+ Certifique-se de que existe uma API no seu caso de Gestão de API que gostaria de exportar para a Plataforma de Energia
+ Certifique-se de que tem um [ambiente](/powerapps/powerapps-overview#power-apps-for-admins) power apps ou power automamate 

## <a name="export-an-api"></a>Exportar uma API

1. Navegue para o seu serviço de Gestão API no portal Azure e selecione **APIs** no menu.
2. Clique nos três pontos junto à API que pretende exportar. 
3. Selecione **Export** (Exportar).
4. Selecione **Power Apps e Power Automat .**
5. Escolha um ambiente para exportar a API para. 
6. Forneça um nome de exibição, que será usado como o nome do conector personalizado.  
7. Opcional, se a API estiver protegida por um servidor OAuth 2.0, também terá de fornecer detalhes `Client ID` adicionais, incluindo, `Client secret` e `Authorization URL` `Token URL` `Refresh URL` .  
8. Selecione **Export** (Exportar). 

Assim que a exportação estiver concluída, navegue para o seu ambiente Power App ou Power Automamate. Verá a API como um conector personalizado.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre a Plataforma Power](https://powerplatform.microsoft.com/)
* [Aprenda tarefas comuns na Gestão da API seguindo os tutoriais](./import-and-publish.md)
