---
author: yegu
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 12/03/2019
ms.openlocfilehash: ceeeb5ee155624e050f36e733a464c2cb21db88c
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750280"
---
1. Para criar um novo repositório de configuração de aplicativo, entre no [portal do Azure](https://portal.azure.com). No canto superior esquerdo da home page, selecione **criar um recurso**. Na caixa **Pesquisar no Marketplace** , insira **configuração de aplicativo** e selecione Enter.

    ![Pesquisar configuração de aplicativo](media/azure-app-configuration-create/azure-portal-search.png)

1. Selecione **configuração de aplicativo** nos resultados da pesquisa e, em seguida, selecione **criar**.

    ![Selecione Criar](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. No painel **configuração do aplicativo** > **criar** , insira as seguintes configurações:

    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome do recurso** | Nome globalmente exclusivo | Insira um nome de recurso exclusivo a ser usado para o recurso de repositório de configuração de aplicativo. O nome tem de ser uma cadeia entre 1 e 63 carateres e conter apenas números, letras e o caráter `-`. O nome não pode começar ou terminar com o caractere de `-` e os caracteres de `-` consecutivos não são válidos.  |
    | **Subscrição** | A sua subscrição | Selecione a assinatura do Azure que você deseja usar para testar a configuração do aplicativo. Se sua conta tiver apenas uma assinatura, ela será selecionada automaticamente e a lista de **assinaturas** não será exibida. |
    | **Grupo de recursos** | *AppConfigTestResources* | Selecione ou crie um grupo de recursos para o recurso de repositório de configuração do aplicativo. Esse grupo é útil para organizar vários recursos que você pode querer excluir ao mesmo tempo excluindo o grupo de recursos. Para obter mais informações, veja [Utilizar grupos de recursos para gerir os recursos do Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Localização** | *E.U.A. Central* | Use **local** para especificar a localização geográfica na qual seu repositório de configuração de aplicativo está hospedado. Para obter o melhor desempenho, crie o recurso na mesma região que outros componentes do seu aplicativo. |

    ![Criar um recurso de repositório de configuração de aplicativo](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Selecione **Criar**. A implantação pode levar alguns minutos.

1. Após a conclusão da implantação, selecione **configurações** > **chaves de acesso**. Anote a cadeia de conexão de chave somente leitura primária. Você usará essa cadeia de conexão posteriormente para configurar seu aplicativo para se comunicar com o repositório de configuração de aplicativo que você criou.
