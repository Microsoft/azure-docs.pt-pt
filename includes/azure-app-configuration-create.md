---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 089a199da08503e13dbd42518970c3b7ed8a984d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "77619307"
---
1. Para criar uma nova loja de Configuração de Aplicações, inscreva-se no [portal Azure.](https://portal.azure.com) No canto superior esquerdo da página inicial, selecione **Criar um recurso**. Na **caixa 'Pesquisar o Mercado',** insira **a Configuração da Aplicação** e selecione Enter.

    ![Pesquisa rumo à Configuração de Aplicações](media/azure-app-configuration-create/azure-portal-search.png)

1. Selecione **a Configuração** da Aplicação a partir dos resultados da pesquisa e, em seguida, selecione **Criar**.

    ![Selecione Criar](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. Na **configuração**da aplicação  >  **Criar** painel, introduza as seguintes definições:

    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome de recurso** | Nome globalmente exclusivo | Introduza um nome de recurso único para usar para o recurso da loja de configuração de aplicações. O nome deve ser uma corda entre 5 e 50 caracteres e conter apenas números, letras e o `-` personagem. O nome não pode começar ou terminar com o `-` personagem.  |
    | **Subscrição** | A sua subscrição | Selecione a subscrição Azure que pretende utilizar para testar a Configuração da Aplicação. Se a sua conta tiver apenas uma subscrição, é selecionada automaticamente e a lista **de Subscrição** não é apresentada. |
    | **Grupo de recursos** | *AppConfigTestResources* | Selecione ou crie um grupo de recursos para o seu recurso de loja de configuração de aplicações. Este grupo é útil para organizar múltiplos recursos que você pode querer eliminar ao mesmo tempo, eliminando o grupo de recursos. Para obter mais informações, veja [Utilizar grupos de recursos para gerir os recursos do Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Localização** | *E.U.A. Central* | Utilize a **Localização** para especificar a localização geográfica em que a sua loja de configuração de aplicações está hospedada. Para obter o melhor desempenho, crie o recurso na mesma região que outros componentes da sua aplicação. |
    | **Escalão de preço** | *Gratuito* | Selecione o nível de preços pretendido. Para mais detalhes, consulte a [página de preços da Configuração da Aplicação.](https://azure.microsoft.com/pricing/details/app-configuration/)

    ![Criar um recurso de loja de configuração de aplicativos](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Selecione **Criar**. A implantação pode demorar alguns minutos.

1. Depois de terminar a implementação, selecione **Definições**  >  **Teclas de acesso**. Tome nota da ligação principal apenas para leitura. Utilizará esta cadeia de ligação mais tarde para configurar a sua aplicação para comunicar com a loja de Configuração de Aplicações que criou.
