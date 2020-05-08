---
title: Integrar data lake storage Gen1 com catálogo de dados Azure
description: Saiba como registar dados do Azure Data Lake Storage Gen1 no Azure Data Catalog para tornar os dados despáveis na sua organização.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 66191a52c6ef1f3d19afd2a47356487b07e9eff4
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692345"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Registe os dados do Azure Data Lake Storage Gen1 no Catálogo de Dados Azure
Neste artigo, você aprenderá a integrar o Azure Data Lake Storage Gen1 com o Azure Data Catalog para tornar os seus dados despáveis dentro de uma organização, integrando-os com data catalog. Para mais informações sobre a catalogação de dados, consulte o Catálogo de [Dados do Azure](../data-catalog/data-catalog-what-is-data-catalog.md). Para compreender cenários em que pode utilizar o Data Catalog, consulte [cenários comuns do Catálogo](../data-catalog/data-catalog-common-scenarios.md)de Dados do Azure .

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ative a subscrição azure** para Data Lake Storage Gen1. Veja as [instruções](data-lake-store-get-started-portal.md).
* **Uma conta Gen1 de Armazenamento de Lago**de Dados. Siga as instruções no [Get started com Azure Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md). Para este tutorial, crie uma conta Data Lake Storage Gen1 chamada **datacatalogstorestore**.

    Depois de ter criado a conta, faça o upload de um conjunto de dados de amostras para a sua conta. Para este tutorial, enviemos todos os ficheiros .csv sob a pasta **AmbulânciaData** no [Repositório Azure Data Lake Git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Pode utilizar vários clientes, como o [Azure Storage Explorer,](https://storageexplorer.com/)para fazer o upload de dados para um contentor de bolhas.
* **Catálogo de dados Azure.** A sua organização já deve ter um Catálogo de Dados Azure criado para a sua organização. Apenas um catálogo é permitido para cada organização.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Registe o Data Lake Storage Gen1 como fonte do Catálogo de Dados

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Vá `https://azure.microsoft.com/services/data-catalog`a , e clique **em Começar**.
1. Inicie sessão no portal do Catálogo de Dados do Azure e clique em **Publicar dados**.

    ![Registar origens de dados](./media/data-lake-store-with-data-catalog/register-data-source.png "Registar origens de dados")
1. Na página seguinte, clique em **Aplicação de Lançamento**. Isto irá descarregar o ficheiro manifesto da aplicação no seu computador. Clique duas vezes no ficheiro manifesto para iniciar a aplicação.
1. Na página Welcome, clique **em Iniciar sessão**e insira as suas credenciais.

    ![Ecrã de Boas-Vindas](./media/data-lake-store-with-data-catalog/welcome.screen.png "Ecrã de Boas-Vindas")
1. Na página Selecione uma página de Fonte de Dados, **selecione Azure Data Lake Store,** e depois clique **em Seguinte**.

    ![Selecionar origem de dados](./media/data-lake-store-with-data-catalog/select-source.png "Selecionar origem de dados")
1. Na página seguinte, forneça o nome da conta Data Lake Storage Gen1 que pretende registar no Data Catalog. Deixe as outras opções como padrão e, em seguida, clique em **Ligar**.

    ![Ligar a origem de dados](./media/data-lake-store-with-data-catalog/connect-to-source.png "Ligar a origem de dados")
1. A página seguinte pode ser dividida nos seguintes segmentos.

    a. A caixa **da Hierarquia** do Servidor representa a estrutura da pasta de contas Data Lake Storage Gen1. **$Root** representa a raiz da conta Data Lake Storage Gen1, e o **AmbulanceData** representa a pasta criada na raiz da conta Data Lake Storage Gen1.

    b. A caixa **de objetos disponíveis** lista os ficheiros e pastas sob a pasta **AmbulânciaData.**

    c. Os **Objetos a registar** listam os ficheiros e pastas que pretende registar no Catálogo de Dados do Azure.

    ![Ver estrutura de dados](./media/data-lake-store-with-data-catalog/view-data-structure.png "Ver estrutura de dados")
1. Para este tutorial, deve registar todos os ficheiros no diretório. Para isso, clique no botão![(mover objetos](./media/data-lake-store-with-data-catalog/move-objects.png "Mover objetos")) para mover todos os ficheiros para **Objetos para ser registado.**

    Como os dados serão registados num catálogo de dados em toda a organização, é uma abordagem recomendada para adicionar alguns metadados que mais tarde pode usar para localizar rapidamente os dados. Por exemplo, pode adicionar um endereço de e-mail para o titular dos dados (por exemplo, aquele que está a carregar os dados) ou adicionar uma etiqueta para identificar os dados. A captura do ecrã abaixo mostra uma etiqueta que adiciona aos dados.

    ![Ver estrutura de dados](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Ver estrutura de dados")

    Clique no **Registo**.
1. A seguinte captura de ecrã denota que os dados estão registados com sucesso no Catálogo de Dados.

    ![Inscrição completa](./media/data-lake-store-with-data-catalog/registration-complete.png "Ver estrutura de dados")
1. Clique no **Portal 'Ver'** para voltar ao portal data Catalog e verificar se já pode aceder aos dados registados a partir do portal. Para pesquisar os dados, pode utilizar a etiqueta utilizada durante o registo dos dados.

     ![Pesquisar dados no catálogo](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Pesquisar dados no catálogo")
1. Agora pode realizar operações como adicionar anotações e documentação aos dados. Para mais informações, consulte os seguintes links.

    * [Anote fontes de dados no Catálogo de Dados](../data-catalog/data-catalog-how-to-annotate.md)
    * [Documentar fontes de dados no Catálogo de Dados](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Consulte também
* [Anote fontes de dados no Catálogo de Dados](../data-catalog/data-catalog-how-to-annotate.md)
* [Documentar fontes de dados no Catálogo de Dados](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrar data lake storage Gen1 com outros serviços Azure](data-lake-store-integrate-with-other-services.md)
