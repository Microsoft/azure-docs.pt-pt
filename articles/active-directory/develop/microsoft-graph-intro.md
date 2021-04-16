---
title: Microsoft Graph API
description: A Microsoft Graph API é uma API web RESTful que lhe permite aceder aos recursos de serviço da Microsoft Cloud.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 2e689e620a5aeb7c5028f1a1b30dd6def8e447ab
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529982"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

A Microsoft Graph API é uma API web RESTful que lhe permite aceder aos recursos de serviço da Microsoft Cloud. Depois de registar a sua aplicação e obter fichas de autenticação para um utilizador ou serviço, pode escrupulesar os pedidos para a API do Gráfico da Microsoft. Para obter mais informações, consulte [a visão geral do Microsoft Graph](/graph/overview).

O Microsoft Graph expõe AS APIs e bibliotecas de clientes para aceder a dados sobre os seguintes serviços Microsoft 365:
- Serviços Microsoft 365: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner e SharePoint
- Serviços de Mobilidade e Segurança Empresarial: Advanced Threat Analytics, Advanced Threat Protection, Azure Ative Directory, Identity Manager e Intune
- Serviços do Windows 10: atividades, dispositivos, notificações
- Dynamics 365 Business Central

## <a name="versions"></a>Versões

O Microsoft Graph suporta atualmente duas versões: v1.0 e beta. A versão v1.0 inclui APIs geralmente disponíveis. Utilize a versão v1.0 para todas as aplicações de produção. A beta inclui APIs que estão atualmente em pré-visualização. Como podemos introduzir alterações de rutura nas nossas APIs beta, recomendamos que utilize a versão beta apenas para testar aplicações que estão em desenvolvimento; não utilize APIs beta nas suas aplicações de produção. Para obter mais informações, consulte [as políticas de versão, suporte e quebra de alterações para o Microsoft Graph](/graph/versioning-and-support).

Para começar a utilizar as APIs beta, consulte a [referência do ponto final beta do Microsoft Graph](/graph/api/overview?view=graph-rest-beta&preserve-view=true)

Para começar a utilizar as APIs v1.0, consulte a [referência API do Microsoft Graph REST v1.0](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)

## <a name="get-started"></a>Introdução

Para ler ou escrever para um recurso como um utilizador ou uma mensagem de e-mail, constrói um pedido que se parece com o seguinte:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Para obter mais informações sobre os elementos do pedido construído, consulte [utilizar a API do Gráfico da Microsoft](/graph/use-the-api)

As amostras Quickstart estão disponíveis para lhe mostrar como aceder à potência da API do Gráfico microsoft. As amostras disponíveis acedem a dois serviços com uma autenticação: conta Microsoft e Outlook. Cada quickstart acede a informações dos perfis dos utilizadores da conta da Microsoft e exibe eventos a partir do seu calendário.
Os arranques rápidos envolvem quatro passos:
- Selecione a sua plataforma
- Obtenha o seu ID de aplicação (ID do cliente)
- Construa a amostra
- Inscreva-se e veja eventos no seu calendário

Quando completar o arranque rápido, tem uma aplicação pronta a ser executada. Para obter mais informações, consulte o [Microsoft Graph quickstart FAQ](/graph/quick-start-faq). Para começar com as amostras, consulte o [Microsoft Graph QuickStart](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Ferramentas

O Microsoft Graph Explorer é uma ferramenta baseada na Web que pode usar para construir e testar pedidos usando APIs do Microsoft Graph. Pode aceder ao Microsoft Graph Explorer em: `https://developer.microsoft.com/graph/graph-explorer` .

O Carteiro é uma ferramenta que também pode usar para construir e testar pedidos utilizando as APIs do Gráfico microsoft. Você pode baixar o Carteiro em: `https://www.getpostman.com/` . Para interagir com o Microsoft Graph no Carteiro, utilize a coleção Microsoft Graph no Carteiro. Para obter mais informações, consulte [Use Postman com a Microsoft Graph API](/graph/use-postman).
