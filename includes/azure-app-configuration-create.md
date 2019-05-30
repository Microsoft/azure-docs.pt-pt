---
title: incluir ficheiro
description: incluir ficheiro
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 01/22/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: c98a17be394887ef4e008b079467c85d4ded7e09
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393318"
---
1. Para criar um novo arquivo de configuração de aplicações, inicie sessão para o [portal do Azure](https://portal.azure.com). No canto superior esquerdo do painel, selecione **+ criar um recurso**. Na **pesquisar no Marketplace** , introduza **configuração de aplicações** e selecione Enter.

    ![Pesquisa de configuração de aplicações](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

1. Selecione **configuração da aplicação** partir da linha de resultados da pesquisa e, em seguida, selecione **criar**.

1. Sobre o **configuração da aplicação** > **criar** painel, introduza as seguintes definições:

    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome do recurso** | Nome globalmente exclusivo | Introduza um nome de recurso exclusiva para utilizar para o recurso de arquivo de configuração de aplicações. O nome tem de ser uma cadeia entre 1 e 63 carateres e conter apenas números, letras e o caráter `-`. O nome não pode começar nem terminar com o `-` caráter e consecutivas `-` carateres não são válidos.  |
    | **Subscrição** | A sua subscrição | Selecione a subscrição do Azure que pretende utilizar para testar a configuração de aplicações. Se a sua conta tiver apenas uma subscrição, é selecionada automaticamente e o **subscrição** não é apresentada a lista. |
    | **Grupo de recursos** | *AppConfigTestResources* | Selecione ou crie um grupo de recursos para o seu recurso do arquivo de configuração de aplicações. Este grupo é útil para organizar os vários recursos que pode querer eliminar ao mesmo tempo, eliminando o grupo de recursos. Para obter mais informações, veja [Utilizar grupos de recursos para gerir os recursos do Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Localização** | *E.U.A. central* | Uso **localização** para especificar a localização geográfica na qual o seu arquivo de configuração de aplicação está alojado. Para obter o melhor desempenho, crie o recurso na mesma região que outros componentes da sua aplicação. |

    ![Criar um recurso do arquivo de configuração de aplicações](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

1. Selecione **Criar**. A implementação pode demorar alguns minutos a concluir.

1. Depois de concluída a implementação, selecione **configurações** > **chaves de acesso**. Tome nota da cadeia de caracteres de só de leitura ou primária de leitura-escrita ligação da chave primária. Usará esta cadeia de ligação mais tarde para configurar a sua aplicação para comunicar com o armazenamento de configuração de aplicação que criou.
