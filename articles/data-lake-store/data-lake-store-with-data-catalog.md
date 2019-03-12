---
title: Registar dados de geração 1 de armazenamento do Azure Data Lake no catálogo de dados do Azure | Documentos da Microsoft
description: Registar dados de geração 1 de armazenamento do Azure Data Lake no catálogo de dados do Azure
services: data-lake-store,data-catalog
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: bc9d6f8f078860000d7a2a38bf4aa1ce00ff450e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533460"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Registar dados de geração 1 de armazenamento do Azure Data Lake no catálogo de dados do Azure
Neste artigo, aprenderá como integrar Gen1 de armazenamento do Azure Data Lake com o catálogo de dados do Azure para tornar os dados detetável dentro de uma organização ao integrá-los no catálogo de dados. Para obter mais informações sobre dados de catalogação, consulte [catálogo de dados do Azure](../data-catalog/data-catalog-what-is-data-catalog.md). Para compreender os cenários em que pode utilizar o catálogo de dados, veja [cenários comuns do catálogo de dados do Azure](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ativar a sua subscrição do Azure** para geração 1 de armazenamento do Data Lake. Veja as [instruções](data-lake-store-get-started-portal.md).
* **Uma conta do Data Lake Storage Gen1**. Siga as instruções em [introdução ao Azure Data Lake Storage Gen1 através do Portal do Azure](data-lake-store-get-started-portal.md). Para este tutorial, criar uma conta de geração 1 de armazenamento do Data Lake denominada **datacatalogstore**.

    Depois de criar a conta, carregar um conjunto de dados de exemplo para o mesmo. Para este tutorial, vamos carregar todos os ficheiros. csv no **AmbulanceData** pasta na [repositório de Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Pode utilizar vários clientes, como [Explorador de armazenamento do Azure](https://storageexplorer.com/), para carregar dados para um contentor de Blobs.
* **Catálogo de dados do Azure**. Sua organização já tem de ter um catálogo de dados do Azure criado para a sua organização. Apenas um catálogo é permitido para cada organização.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Registar Gen1 de armazenamento do Data Lake como uma origem para o catálogo de dados

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Aceda a `https://azure.microsoft.com/services/data-catalog`e clique em **começar**.
1. Inicie sessão no portal do catálogo de dados do Azure e clique em **publicar dados**.

    ![Registar uma origem de dados](./media/data-lake-store-with-data-catalog/register-data-source.png "registar uma origem de dados")
1. Na página seguinte, clique em **iniciar aplicação**. Isto irá transferir o ficheiro de manifesto de aplicação no seu computador. Faça duplo clique no ficheiro de manifesto para iniciar o aplicativo.
1. Na página de boas-vindas, clique em **iniciar sessão**e introduza as suas credenciais.

    ![Ecrã de boas-vindas](./media/data-lake-store-with-data-catalog/welcome.screen.png "ecrã de boas-vindas")
1. Em selecionar uma página de origem de dados, selecione **do Azure Data Lake Store**e, em seguida, clique em **próxima**.

    ![Origem de dados selecione](./media/data-lake-store-with-data-catalog/select-source.png "origem de dados selecione")
1. Na página seguinte, forneça o nome de conta de geração 1 do Data Lake armazenamento que pretende registar no catálogo de dados. Deixe as outras opções como padrão e, em seguida, clique em **Connect**.

    ![Ligar à origem de dados](./media/data-lake-store-with-data-catalog/connect-to-source.png "ligar à origem de dados")
1. A próxima página pode ser dividida em segmentos de seguintes.

    a. O **hierarquia de servidores** caixa representa a estrutura de pastas de conta de geração 1 de armazenamento do Data Lake. **$Root** representa a raiz da conta de geração 1 de armazenamento do Data Lake, e **AmbulanceData** representa a pasta criada na raiz da conta do Data Lake Storage Gen1.

    b. O **objetos disponíveis** caixa de lista os ficheiros e pastas sob a **AmbulanceData** pasta.

    c. **Objetos a registar** caixa apresenta uma lista de ficheiros e pastas que pretende registar no catálogo de dados do Azure.

    ![Ver a estrutura de dados](./media/data-lake-store-with-data-catalog/view-data-structure.png "ver a estrutura de dados")
1. Para este tutorial, deve registrar todos os ficheiros no diretório. Para isso, clique na (![mover objetos](./media/data-lake-store-with-data-catalog/move-objects.png "mover objetos")) botão para passar todos os ficheiros para **objetos a registar** caixa.

    Como os dados serão registados no catálogo de dados em toda a organização, é uma abordagem recomendada para adicionar alguns metadados que pode utilizar mais tarde para localizar rapidamente os dados. Por exemplo, pode adicionar um endereço de email para o proprietário dos dados (por exemplo, um que está a carregar os dados) ou adicionar uma etiqueta para identificar os dados. Captura de ecrã abaixo mostra uma etiqueta que adicione os dados.

    ![Ver a estrutura de dados](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "ver a estrutura de dados")

    Clique em **registar**.
1. Captura de ecrã seguinte indica que os dados com êxito são registados no catálogo de dados.

    ![Registo concluído](./media/data-lake-store-with-data-catalog/registration-complete.png "ver a estrutura de dados")
1. Clique em **ver Portal** para voltar atrás para o portal do catálogo de dados e certifique-se de que agora pode aceder os dados registados no portal. Para pesquisar os dados, pode usar a marca que utilizou ao registar os dados.

     ![Pesquisar dados no catálogo](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "pesquisar dados no catálogo")
1. Agora pode efetuar operações como adicionar anotações e documentação para os dados. Para obter mais informações, consulte os links a seguir.

    * [Anotar origens de dados no catálogo de dados](../data-catalog/data-catalog-how-to-annotate.md)
    * [Documentar origens de dados no catálogo de dados](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Ver também
* [Anotar origens de dados no catálogo de dados](../data-catalog/data-catalog-how-to-annotate.md)
* [Documentar origens de dados no catálogo de dados](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrar a geração 1 de armazenamento do Data Lake com outros serviços do Azure](data-lake-store-integrate-with-other-services.md)
