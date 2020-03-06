---
title: Microsoft Graph API  Microsoft Docs
description: O Microsoft Graph API é um API web RESTful que lhe permite aceder aos recursos de serviço do Microsoft Cloud.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 65465c6897853617f7f33a989f97d01fb0518a5e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304423"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

O Microsoft Graph API é um API web RESTful que lhe permite aceder aos recursos de serviço do Microsoft Cloud. Depois de registar a sua aplicação e obter fichas de autenticação para um utilizador ou serviço, pode fazer pedidos para a Microsoft Graph API. Para mais informações, consulte a [visão geral do Microsoft Graph](https://docs.microsoft.com/graph/overview).

O Microsoft Graph expõe as APIs rest e as bibliotecas de clientes para aceder a dados sobre os seguintes serviços microsoft 365:
- Serviços do Office 365: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner e SharePoint
- Serviços de Mobilidade e Segurança Empresariais: Advanced Threat Analytics, Advanced Threat Protection, Azure Ative Directory, Identity Manager e Intune
- Serviços Windows 10: atividades, dispositivos, notificações
- Dinâmica 365 Business Central

## <a name="versions"></a>Versões

O Microsoft Graph suporta atualmente duas versões: v1.0 e beta. A versão v1.0 inclui APIs geralmente disponíveis. Utilize a versão v1.0 para todas as aplicações de produção. A beta inclui APIs que estão atualmente em pré-visualização. Como podemos introduzir alterações de quebra nas nossas APIs beta, recomendamos que utilize a versão beta apenas para testar aplicações que estão em desenvolvimento; não utilize APIs beta nas suas aplicações de produção. Para mais informações, consulte [A versão, suporte e quebra](https://docs.microsoft.com/graph/versioning-and-support)de políticas de mudança para o Microsoft Graph .

Para começar a utilizar as APIs beta, consulte a [referência beta do Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-beta)

Para começar a utilizar as APIs v1.0, consulte a [referência Microsoft Graph REST API v1.0](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)

## <a name="get-started"></a>Introdução

Para ler ou escrever a um recurso como um utilizador ou uma mensagem de e-mail, você constrói um pedido que se parece com o seguinte:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Para obter mais informações sobre os elementos do pedido construído, consulte [Utilize a API do Microsoft Graph](https://docs.microsoft.com/graph/use-the-api)

As amostras quickstart estão disponíveis para lhe mostrar como aceder ao poder da API do Microsoft Graph. As amostras disponíveis acedem a dois serviços com uma autenticação: conta Microsoft e Outlook. Cada quickstart acede a informações dos perfis dos utilizadores da conta da Microsoft e exibe eventos a partir do seu calendário.
Os quickstarts envolvem quatro passos:
- Selecione a sua plataforma
- Obtenha o seu ID de aplicação (ID do cliente)
- Construir a amostra
- Inscreva-se e veja eventos no seu calendário

Quando completar o arranque rápido, tem uma aplicação pronta a funcionar. Para mais informações, consulte o [Microsoft Graph quickstart FAQ](https://docs.microsoft.com/graph/quick-start-faq). Para começar com as amostras, consulte [o Microsoft Graph QuickStart](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Ferramentas

O Microsoft Graph Explorer é uma ferramenta baseada na Web que pode supor para construir e testar pedidos usando APIs do Microsoft Graph. Pode aceder ao Microsoft Graph Explorer em: `https://developer.microsoft.com/graph/graph-explorer`.

O carteiro é uma ferramenta que também pode usar para construir e testar pedidos utilizando as APIs do Microsoft Graph. Você pode baixar O Carteiro em: `https://www.getpostman.com/`. Para interagir com o Microsoft Graph em Postman, utiliza a coleção Microsoft Graph no Postman. Para mais informações, consulte [Use Postman com a Microsoft Graph API](https://docs.microsoft.comgraph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta).
