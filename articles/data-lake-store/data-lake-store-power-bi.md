---
title: Analise dados em Azure Data Lake Storage Gen1 usando Power BI / Microsoft Docs
description: Use o Power BI para analisar os dados armazenados no Azure Data Lake Storage Gen1
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "65603207"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Analise dados em Azure Data Lake Storage Gen1 usando Power BI
Neste artigo, aprenderá a usar o Power BI Desktop para analisar e visualizar dados armazenados no Azure Data Lake Storage Gen1.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta Gen1 de Armazenamento de Lago**de Dados. Siga as instruções no [Get started com Azure Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md). Este artigo assume que já criou uma conta Data Lake Storage Gen1, chamada **myadlsg1,** e fez o upload de um ficheiro de dados de amostra **(Drivers.txt)** para o mesmo. Este ficheiro de amostraestá disponível para download a partir do [Repositório Azure Data Lake Git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Pode descarregar isto no [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop
1. Inicie o power bi desktop no seu computador.
2. A partir da fita **Home,** clique em **Obter Dados**, e depois clique em Mais. Na caixa de diálogo **Get Data,** clique em **Azure,** clique em **Azure Data Lake Store,** e depois clique em **Connect**.
   
    ![Ligue-se ao Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Ligue-se ao Data Lake Storage Gen1")
3. Se vir uma caixa de diálogo sobre o conector estar numa fase de desenvolvimento, opte por continuar.
4. Na caixa de diálogo **Azure Data Lake Store,** forneça o URL à sua conta Data Lake Storage Gen1 e, em seguida, clique em **OK**.
   
    ![URL para Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL para Data Lake Storage Gen1")
5. Na caixa de diálogo seguinte, clique **em Iniciar** sessão para iniciar sessão na conta Data Lake Storage Gen1. Será redirecionado para o sinal da sua organização na página. Siga as instruções para assinar na conta.
   
    ![Assine o Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Assine o Data Lake Storage Gen1")
6. Depois de ter assinado com sucesso, clique em **Connect**.
   
    ![Ligue-se ao Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Ligue-se ao Data Lake Storage Gen1")
7. A próxima caixa de diálogo mostra o ficheiro que fez para a sua conta Data Lake Storage Gen1. Verifique a informação e, em seguida, clique em **Carregar**.
   
    ![Dados de carga do Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Dados de carga do Data Lake Storage Gen1")
8. Depois de os dados terem sido carregados com sucesso no Power BI, verá os seguintes campos no separador **Fields.**
   
    ![Campos importados](./media/data-lake-store-power-bi/imported-fields.png "Campos importados")
   
    No entanto, para visualizar e analisar os dados, preferimos que os dados estejam disponíveis nos seguintes campos
   
    ![Campos desejados](./media/data-lake-store-power-bi/desired-fields.png "Campos desejados")
   
    Nos próximos passos, atualizaremos a consulta para converter os dados importados no formato desejado.
9. A partir da fita **Home,** clique em **Editar Consultas**.
   
    ![Editar consultas](./media/data-lake-store-power-bi/edit-queries.png "Editar consultas")
10. No Editor de Consulta, sob a coluna **Content,** clique em **Binary**.
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query1.png "Editar consultas")
11. Verá um ícone de ficheiro que representa o ficheiro **Drivers.txt** que fez o upload. Clique no ficheiro e clique em **CSV**.    
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query2.png "Editar consultas")
12. Deve ver uma saída como mostrado abaixo. Os seus dados estão agora disponíveis num formato que pode utilizar para criar visualizações.
    
    ![Editar consultas](./media/data-lake-store-power-bi/convert-query3.png "Editar consultas")
13. A partir da fita **Home,** clique **em Fechar e Aplicar,** e depois clique **em Fechar e Aplicar**.
    
    ![Editar consultas](./media/data-lake-store-power-bi/load-edited-query.png "Editar consultas")
14. Uma vez atualizada a consulta, o separador **Fields** mostrará os novos campos disponíveis para visualização.
    
    ![Campos atualizados](./media/data-lake-store-power-bi/updated-query-fields.png "Campos atualizados")
15. Criemos um gráfico de tartes para representar os condutores de cada cidade para um determinado país/região. Para isso, faça as seguintes seleções.
    
    1. A partir do separador Visualizações, clique no símbolo para obter um gráfico de tartes.
       
        ![Criar gráfico de tartes](./media/data-lake-store-power-bi/create-pie-chart.png "Criar gráfico de tartes")
    2. As colunas que vamos usar são **colunas 4** (nome da cidade) e **Coluna 7** (nome do país/região). Arraste estas colunas do separador **Fields** para **visualizações,** como mostrado abaixo.
       
        ![Criar visualizações](./media/data-lake-store-power-bi/create-visualizations.png "Criar visualizações")
    3. O gráfico de tortas deve agora assemelhar-se ao que se segue.
       
        ![Gráfico circular](./media/data-lake-store-power-bi/pie-chart.png "Criar visualizações")
16. Ao selecionar um país/região específico a partir dos filtros de nível de página, pode agora ver o número de condutores em cada cidade do país/região selecionado. Por exemplo, sob o separador **Visualizações,** sob **filtros de nível de página,** selecione **Brasil**.
    
    ![Selecione um país](./media/data-lake-store-power-bi/select-country.png "Selecionar país/região")
17. O gráfico de tartes é automaticamente atualizado para exibir os condutores nas cidades do Brasil.
    
    ![Motoristas em um país](./media/data-lake-store-power-bi/driver-per-country.png "Condutores por país/região")
18. A partir do menu **'Ficheiro',** clique em **Guardar** para guardar a visualização como ficheiro Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publicar relatório ao serviço Power BI
Depois de ter criado as visualizações no Power BI Desktop, pode partilhá-la com outras publicando-as no serviço Power BI. Para obter instruções sobre como fazê-lo, consulte [Publicar a partir de Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Consulte também
* [Analise dados em Data Lake Storage Gen1 usando data lake analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

