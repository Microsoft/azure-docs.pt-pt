---
title: Integrar com um pipeline de integração e entrega contínuo com a configuração de aplicações do Azure | Documentos da Microsoft
description: Saiba como gerar um ficheiro de configuração a utilizar os dados na configuração de aplicações do Azure durante a integração e entrega contínuas
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 0e6b24175ffa28b2a0f361bc46fd6c41e09cae72
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885439"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrar com um pipeline CI/CD

Para melhorar a resiliência da sua aplicação contra a remota possibilidade de não ser capaz de alcançar a configuração de aplicações do Azure, deve empacotar os dados de configuração atual num arquivo que é implementado com a aplicação e carregá-lo localmente durante o arranque do seu . Essa abordagem garante que seu aplicativo terão valores de definição do padrão pelo menos. Estes valores serão substituídos por quaisquer alterações mais recentes feitas num arquivo de configuração de aplicação quando estiver disponível.

## <a name="automate-configuration-data-retrieval"></a>Automatizar a recuperação de dados de configuração

Utilizar o [ **exportar** ](./howto-import-export-data.md#export-data) função de configuração de aplicações do Azure, pode automatizar o processo de obtenção de dados de configuração atual como um único arquivo. Pode, em seguida, inclua esse arquivo numa etapa de compilação ou implementação na sua integração contínua e um pipeline de implementação contínua.

## <a name="next-steps"></a>Passos Seguintes

* [Quickstart: Criar uma aplicação web ASP.NET](quickstart-aspnet-core-app.md)  
