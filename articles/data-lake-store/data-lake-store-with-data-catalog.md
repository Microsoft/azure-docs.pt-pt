---
title: Integre data lake storage Gen1 com catálogo de dados Azure
description: Saiba como registar dados da Azure Data Lake Storage Gen1 no Azure Data Catalog para tornar os dados detetáveis na sua organização.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 02544489816f5711ca6e599c2bce03737c747934
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106625"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Registar dados do Azure Data Lake Storage Gen1 no Catálogo de Dados Azure
Neste artigo, você vai aprender como integrar a Azure Data Lake Storage Gen1 com o Azure Data Catalog para tornar os seus dados detetáveis dentro de uma organização, integrando-os com o Catálogo de Dados. Para obter mais informações sobre a catalogação de dados, consulte [o Catálogo de Dados do Azure.](../data-catalog/overview.md) Para entender os cenários em que pode utilizar o Catálogo de Dados, consulte [cenários comuns do Catálogo de Dados do Azure.](../data-catalog/data-catalog-common-scenarios.md)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ative a sua subscrição Azure** para data lake storage gen1. Consulte [as instruções.](data-lake-store-get-started-portal.md)
* **Uma conta gen1 de armazenamento de data lake.** Siga as instruções da [Azure Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md). Para este tutorial, crie uma conta Desarma de Armazenamento de Dados Gen1 chamada **datacatalogstore**.

    Depois de ter criado a conta, faça o upload de um conjunto de dados de amostra. Para este tutorial, façamos o upload de todos os ficheiros .csv sob a pasta **AmbulanceData** no [Repositório Azure Data Lake Git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Pode utilizar vários clientes, como [o Azure Storage Explorer,](https://storageexplorer.com/)para enviar dados para um recipiente de bolhas.
* **Catálogo de Dados Azure.** A sua organização já deve ter um Catálogo de Dados Azure criado para a sua organização. Apenas um catálogo é permitido para cada organização.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Registar data lake storage Gen1 como fonte para catálogo de dados

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Vá a `https://azure.microsoft.com/services/data-catalog` , e clique Em **Começar**.
1. Inicie sessão no portal do Catálogo de Dados do Azure e clique em **Publicar dados**.

    ![Registar origens de dados](./media/data-lake-store-with-data-catalog/register-data-source.png "Registar origens de dados")
1. Na página seguinte, clique em **Aplicação de Lançamento.** Isto irá descarregar o ficheiro manifesto de aplicação no seu computador. Clique duas vezes no ficheiro manifesto para iniciar a aplicação.
1. Na página Welcome, clique **em Iniciar s-screva**e insira as suas credenciais.

    ![Ecrã de Boas-Vindas](./media/data-lake-store-with-data-catalog/welcome.screen.png "Ecrã de Boas-Vindas")
1. Na página Select a Data Source, selecione **Azure Data Lake Store**e, em seguida, clique em **Seguinte**.

    ![Selecionar origem de dados](./media/data-lake-store-with-data-catalog/select-source.png "Selecionar origem de dados")
1. Na página seguinte, forneça o nome da conta Gen1 de armazenamento de dados que pretende registar no Catálogo de Dados. Deixe as outras opções como padrão e, em seguida, clique em **Connect**.

    ![Ligar a origem de dados](./media/data-lake-store-with-data-catalog/connect-to-source.png "Ligar a origem de dados")
1. A página seguinte pode ser dividida nos seguintes segmentos.

    a. A caixa **de hierarquia do servidor** representa a estrutura da pasta de conta Desequiramento de Armazenamento de Dados Gen1. **$Root** representa a raiz da conta Gen1 de armazenamento de dados, e o **AmbulanceData** representa a pasta criada na raiz da conta Desema de Armazenamento de Data Lake Gen1.

    b. A caixa **de objetos disponíveis** lista os ficheiros e pastas sob a pasta **AmbulanceData.**

    c. A caixa **de Objetos a registar** lista os ficheiros e pastas que pretende registar no Catálogo de Dados do Azure.

    ![Screenshot do Microsoft Azure Data Catalog - Caixa de diálogo de conta de loja.](./media/data-lake-store-with-data-catalog/view-data-structure.png "Ver estrutura de dados")
1. Para este tutorial, deve registar todos os ficheiros no diretório. Para isso, clique no botão![(mover objetos)](./media/data-lake-store-with-data-catalog/move-objects.png "Mover objetos")para mover todos os ficheiros para **Objects para serem registados.**

    Como os dados serão registados num catálogo de dados a nível da organização, é uma abordagem recomendada para adicionar alguns metadados que pode usar mais tarde para localizar rapidamente os dados. Por exemplo, pode adicionar um endereço de e-mail para o titular dos dados (por exemplo, um que está a carregar os dados) ou adicionar uma etiqueta para identificar os dados. A captura do ecrã abaixo mostra uma etiqueta que adiciona aos dados.

    ![Screenshot do Microsoft Azure Data Catalog - Store Account dialog box com a etiqueta que foi adicionada aos dados chamados.](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Ver estrutura de dados")

    Clique em **Registar**.
1. A seguinte captura de ecrã indica que os dados estão registados com sucesso no Catálogo de Dados.

    ![Inscrição completa](./media/data-lake-store-with-data-catalog/registration-complete.png "Ver estrutura de dados")
1. Clique **em Ver Portal** para voltar ao portal do Catálogo de Dados e verificar se já pode aceder aos dados registados a partir do portal. Para pesquisar os dados, pode utilizar a etiqueta que utilizou durante o registo dos dados.

     ![Pesquisar dados em catálogo](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Pesquisar dados em catálogo")
1. Agora pode realizar operações como adicionar anotações e documentação aos dados. Para mais informações, consulte os seguintes links.

    * [Anotar fontes de dados no Catálogo de Dados](../data-catalog/data-catalog-how-to-annotate.md)
    * [Fontes de dados do documento no Catálogo de Dados](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Ver também
* [Anotar fontes de dados no Catálogo de Dados](../data-catalog/data-catalog-how-to-annotate.md)
* [Fontes de dados do documento no Catálogo de Dados](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrar data lake storage Gen1 com outros serviços Azure](data-lake-store-integrate-with-other-services.md)