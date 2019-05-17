---
title: Analisar dados de geração 1 de armazenamento do Azure Data Lake ao utilizar o Power BI | Documentos da Microsoft
description: Utilizar o Power BI para analisar dados armazenados na geração 1 de armazenamento do Azure Data Lake
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: d8717b8f365e692b5f27bf8a04d65c5147b8f31b
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603207"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Analisar dados de geração 1 de armazenamento do Azure Data Lake ao utilizar o Power BI
Neste artigo, aprenderá como usar o ambiente de trabalho do Power BI para analisar e visualizar os dados armazenados na geração 1 de armazenamento do Azure Data Lake.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Data Lake Storage Gen1**. Siga as instruções em [introdução à geração 1 de armazenamento do Azure Data Lake com o portal do Azure](data-lake-store-get-started-portal.md). Este artigo pressupõe que já criou uma conta de geração 1 de armazenamento do Data Lake, denominada **myadlsg1**e carregado um ficheiro de dados de exemplo (**Drivers.txt**) ao mesmo. Este ficheiro de exemplo está disponível para transferência a partir [repositório de Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Pode transferir este componente a partir [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop
1. Inicie o ambiente de trabalho do Power BI no seu computador.
2. Partir do **home page** Friso, clique em **obter dados**e, em seguida, clique em muito mais. Na **obter dados** caixa de diálogo, clique em **Azure**, clique em **Azure Data Lake Store**e, em seguida, clique em **Connect**.
   
    ![Ligar ao Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account.png "ligar ao Data Lake Storage Gen1")
3. Se vir uma caixa de diálogo sobre o conector do que está a ser uma fase de desenvolvimento, optar por continuar.
4. Na **do Azure Data Lake Store** caixa de diálogo, forneça o URL para a sua conta do Data Lake Storage Gen1 e, em seguida, clique em **OK**.
   
    ![URL para o Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL para a geração 1 do Data Lake Storage")
5. Na caixa de diálogo seguinte, clique em **iniciar sessão** para iniciar sessão para a conta de geração 1 de armazenamento do Data Lake. Será redirecionado para a página de início de sessão da sua organização. Siga as instruções para iniciar sessão na conta.
   
    ![Inicie sessão no Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "inicie sessão no Data Lake Storage Gen1")
6. Após ter iniciado com êxito, clique em **Connect**.
   
    ![Ligar ao Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "ligar ao Data Lake Storage Gen1")
7. A caixa de diálogo seguinte mostra o ficheiro que carregou para a sua conta de geração 1 de armazenamento do Data Lake. Verifique as informações e, em seguida, clique em **carga**.
   
    ![Carregar dados de geração 1 de armazenamento do Data Lake](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "carregar dados de geração 1 de armazenamento do Data Lake")
8. Após os dados terem sido carregados com êxito para o Power BI, verá os campos seguintes no **campos** separador.
   
    ![Campos de importados](./media/data-lake-store-power-bi/imported-fields.png "importados campos")
   
    No entanto, para visualizar e analisar os dados, preferimos os dados estejam disponíveis pelos seguintes campos
   
    ![Campos de pretendido](./media/data-lake-store-power-bi/desired-fields.png "pretendido campos")
   
    Nos passos seguintes, iremos atualizar a consulta para converter os dados importados no formato desejado.
9. Partir do **home page** Friso, clique em **editar consultas**.
   
    ![Editar consultas](./media/data-lake-store-power-bi/edit-queries.png "editar consultas")
10. No Editor de consultas, sob o **conteúdo** coluna, clique em **binário**.
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query1.png "editar consultas")
11. Verá um ícone de arquivo, que representa a **Drivers.txt** ficheiro que carregou. O ficheiro com o botão direito e clique em **CSV**.    
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query2.png "editar consultas")
12. Deverá ver um resultado conforme mostrado abaixo. Seus dados estão agora disponíveis num formato que pode utilizar para criar visualizações.
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query3.png "editar consultas")
13. Partir do **home page** Friso, clique em **fechar e aplicar**e, em seguida, clique em **fechar e aplicar**.
    
    ![Editar consultas](./media/data-lake-store-power-bi/load-edited-query.png "editar consultas")
14. Assim que a consulta é atualizada, o **campos** guia irá mostrar os novos campos disponíveis para visualização.
    
    ![Atualizar campos](./media/data-lake-store-power-bi/updated-query-fields.png "atualizado campos")
15. Vamos crie um gráfico de pizza para representar os controladores no cada cidade para um determinado país/região. Para tal, faça as seleções seguintes.
    
    1. No separador visualizações, clique no símbolo para um gráfico de pizza.
       
        ![Criar o gráfico circular](./media/data-lake-store-power-bi/create-pie-chart.png "criar o gráfico circular")
    2. As colunas que vamos utilizar são **coluna 4** (nome da cidade) e **7 de coluna** (nome do país/região). Arraste estas colunas da **campos** separador para **visualizações** separador conforme mostrado abaixo.
       
        ![Criar visualizações](./media/data-lake-store-power-bi/create-visualizations.png "criar visualizações")
    3. O gráfico de pizza agora deve assemelhar-se, como o mostrado abaixo.
       
        ![Gráfico circular](./media/data-lake-store-power-bi/pie-chart.png "criar visualizações")
16. Ao selecionar um país/região dos filtros de nível de página, agora pode ver o número de controladores em cada cidade de país/região selecionada. Por exemplo, sob o **visualizações** separador, em **filtros de nível de página**, selecione **Brasil**.
    
    ![Selecione um país](./media/data-lake-store-power-bi/select-country.png "selecione um país/região")
17. O gráfico de pizza é atualizado automaticamente para apresentar os drivers em cidades do Brasil.
    
    ![Controladores num país](./media/data-lake-store-power-bi/driver-per-country.png "Drivers por país/região")
18. Do **arquivo** menu, clique em **guardar** para guardar a visualização como um ficheiro do Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publicar o relatório no serviço Power BI
Depois de criar as visualizações no Power BI Desktop, pode partilhá-lo com outras pessoas ao publicá-la no serviço Power BI. Para obter instruções sobre como fazer isso, consulte [publicar a partir do Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Consulte também
* [Analisar dados de geração 1 de armazenamento do Data Lake com o Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

