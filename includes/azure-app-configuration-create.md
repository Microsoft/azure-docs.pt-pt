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
ms.openlocfilehash: 9b86f2e05e2cb42470061bd6398b4200607f2418
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012290"
---
1. Para criar um novo arquivo de configuração de aplicação, inicie sessão para o [portal do Azure](https://aka.ms/azconfig/portal). No canto superior esquerdo da página, selecione **+ criar um recurso**. Na **pesquisar no Marketplace** , introduza **configuração de aplicações** e prima Enter.

    ![Pesquisa de configuração de aplicações](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Selecione **configuração da aplicação** partir da linha de resultados da pesquisa e, em seguida, selecione **criar**.

3. Sobre o **configuração da aplicação** > **criar** página, introduza as seguintes definições.

    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome do recurso** | Nome globalmente exclusivo | Introduza um nome de recurso exclusiva para utilizar para o recurso de arquivo de configuração de aplicações. O nome tem de ser uma cadeia entre 1 e 63 carateres e conter apenas números, letras e o caráter `-`. O nome não pode começar nem terminar com o `-` caráter e consecutivas `-` carateres não são válidos.  |
    | **Subscrição** | A sua subscrição | Selecione a subscrição do Azure que pretende utilizar para testar a configuração de aplicações. Se a sua conta tiver apenas uma subscrição, é selecionada automaticamente e o **subscrição** não for apresentada a lista pendente. |
    | **Grupo de recursos** | *AppConfigTestResources* | Selecione ou crie um grupo de recursos para o seu recurso de arquivo de configuração de aplicação. Este grupo é útil para organizar os vários recursos que pode querer eliminar ao mesmo tempo, eliminando o grupo de recursos. Para obter mais informações, veja [Utilizar grupos de recursos para gerir os recursos do Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Localização** | *E.U.A. central* | Uso **localização** para especificar a localização geográfica na qual o seu arquivo de configuração de aplicação está alojado. Para obter o melhor desempenho, crie o recurso na mesma região que outros componentes da sua aplicação. |

    ![Criar um recurso de arquivo de configuração de aplicação](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Selecione **Criar**. A implementação pode demorar alguns minutos a concluir.

5. Depois de concluída a implementação, selecione **configurações** > **chaves de acesso**. Tome nota da cadeia de caracteres de só de leitura ou primária de leitura-escrita ligação da chave primária. Utilize esta cadeia de ligação mais tarde para configurar a sua aplicação para comunicar com o armazenamento de configuração de aplicação que criou.
