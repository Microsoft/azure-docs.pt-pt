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
ms.openlocfilehash: 71e63de247e05a4712687354ed548219b36e8f2f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884864"
---
1. Para criar um novo arquivo de configuração de aplicação, primeiro inicie sessão para o [portal do Azure](https://aka.ms/azconfig/portal). No canto superior esquerdo da página, clique em **+ Criar um recurso**. Na **pesquisar no Marketplace** caixa de texto, tipo **configuração de aplicações** e pressione **Enter**.

    ![Pesquisa de configuração de aplicações](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Clique em **configuração da aplicação** nos resultados da pesquisa e, em seguida **criar**.

3. Na **configuração da aplicação** > **criar** página, introduza as seguintes definições:

    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome do recurso** | Nome globalmente exclusivo | Introduza um nome de recurso exclusiva para utilizar para o recurso de arquivo de configuração de aplicações. O nome tem de ser uma cadeia entre 1 e 63 carateres e conter apenas números, letras e o caráter `-`. O nome não pode começar nem terminar com o caráter `-` e os carateres `-` consecutivos não são válidos.  |
    | **Subscrição** | A sua subscrição | Selecione a subscrição do Azure que pretende utilizar para testar a configuração de aplicações. Se a sua conta tiver apenas uma subscrição, esta é selecionada automaticamente e não é apresentado o menu pendente **Subscrição**. |
    | **Grupo de Recursos** | *AppConfigTestResources* | Selecione ou crie um grupo de recursos para o seu recurso de arquivo de configuração de aplicação. Este grupo é útil para organizar os vários recursos que pretende eliminar em simultâneo, ao eliminar o grupo de recursos. Para obter mais informações, veja [Utilizar Grupos de recursos para gerir os recursos do Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Localização** | *E.U.A. central* | Uso **localização** para especificar a localização geográfica na qual o seu arquivo de configuração de aplicação está alojado. Para obter o melhor desempenho, recomendamos que crie o recurso na mesma região que os outros componentes da sua aplicação. |

    ![Criar um recurso de arquivo de configuração de aplicação](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Clique em **Criar**. A implementação pode demorar alguns minutos.

5. Depois de concluída a implementação, clique em **configurações** > **chaves de acesso**. Anote a cadeia só de leitura ou primária de leitura-escrita ligação da chave primária. O usará posteriormente para configurar a sua aplicação para comunicar com o armazenamento de configuração de aplicação que acabou de criar.

6. Clique em **Explorador de chave/valor** e **+ criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Value |
    |---|---|
    | TestApp:Settings:BackgroundColor | White |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Preto |
    | TestApp:Settings:Message | Dados de configuração de aplicações do Azure |

    Irá sair **rótulo** e **tipo de conteúdo** branco por agora.
