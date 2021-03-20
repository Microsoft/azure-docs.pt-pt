---
title: Analise os dados em Azure Data Lake Storage Gen1 - Power BI
description: Aprenda a usar o Power BI Desktop para analisar e visualizar dados armazenados na Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bcc561cd5eea4372d798fff4580362ba0879c3a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91574199"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Analise os dados em Azure Data Lake Storage Gen1 utilizando o Power BI
Neste artigo, aprende a usar o Power BI Desktop para analisar e visualizar dados armazenados na Azure Data Lake Storage Gen1.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta gen1 de armazenamento de data lake.** Siga as instruções da [Azure Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md). Este artigo pressupõe que já criou uma conta Gen1 de Armazenamento de **Dados, chamada myadlsg1,** e enviou um ficheiro de dados de amostra **(Drivers.txt**) para o mesmo. Este ficheiro de amostra está disponível para download a partir do [Repositório Azure Data Lake Git.](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)
* **Power BI Desktop**. Pode fazer o download do [Microsoft Download Center.](https://www.microsoft.com/en-us/download/details.aspx?id=45331) 

## <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop
1. Lançar Power BI Desktop no seu computador.
2. A partir da fita **Home,** clique em **Obter Dados** e, em seguida, clique em Mais. Na caixa de diálogo **Get Data,** clique em **Azure,** clique em **Azure Data Lake Store** e, em seguida, clique em **Connect**.
   
    ![Screenshot da caixa de diálogo Get Data com a opção Azure Data Lake Store realçada e a opção Connect chamada.](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Ligue-se à Data Lake Storage Gen1")
3. Se vir uma caixa de diálogo sobre o conector estar em fase de desenvolvimento, opte por continuar.
4. Na caixa de diálogo **Azure Data Lake Store,** forneça o URL para a sua conta Desíduo De Armazenamento de Dados Gen1 e, em seguida, clique **em OK**.
   
    ![URL para data lake storage gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL para data lake storage gen1")
5. Na caixa de diálogo seguinte, clique **em Iniciar sôs para** iniciar sação na conta Desemanhse de Armazenamento de Dados Gen1. Será redirecionado para a página de sinscê-in da sua organização. Siga as instruções para assinar na conta.
   
    ![Inscreva-se na Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Inscreva-se na Data Lake Storage Gen1")
6. Depois de ter assinado com sucesso, clique em **Connect**.
   
    ![Screenshot da caixa de diálogo Azure Data Lake Store com a opção Connect chamada.](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Ligue-se à Data Lake Storage Gen1")
7. A próxima caixa de diálogo mostra o ficheiro que fez para a sua conta de Data Lake Storage Gen1. Verifique a informação e, em seguida, clique em **Carregar**.
   
    ![Carregar dados da Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Carregar dados da Data Lake Storage Gen1")
8. Depois de os dados terem sido carregados com sucesso no Power BI, verá os seguintes campos no separador **Campos.**
   
    ![Campos importados](./media/data-lake-store-power-bi/imported-fields.png "Campos importados")
   
    No entanto, para visualizar e analisar os dados, preferimos que os dados estejam disponíveis pelos seguintes campos
   
    ![Campos desejados](./media/data-lake-store-power-bi/desired-fields.png "Campos desejados")
   
    Nos próximos passos, atualizaremos a consulta para converter os dados importados no formato pretendido.
9. A partir da fita **Home,** clique **em Editar Consultas.**
   
    ![Screenshot da fita Home com a opção EditAr Consultas chamada.](./media/data-lake-store-power-bi/edit-queries.png "Editar consultas")
10. No Editor de Consulta, sob a coluna **Conteúdo,** clique em **Binário**.
    
    ![Screenshot do Editor de Consulta com a coluna de conteúdo chamado.](./media/data-lake-store-power-bi/convert-query1.png "Editar consultas")
11. Verá um ícone de ficheiro que representa o **ficheiroDrivers.txt** que carregou. Clique com o botão direito no ficheiro e clique em **CSV**.    
    
    ![Screenshot do Editor de Consulta com a opção CSV chamada.](./media/data-lake-store-power-bi/convert-query2.png "Editar consultas")
12. Deve ver uma saída como mostrado abaixo. Os seus dados estão agora disponíveis num formato que pode utilizar para criar visualizações.
    
    ![Screenshot do Editor de Consulta com a saída exibida como esperado.](./media/data-lake-store-power-bi/convert-query3.png "Editar consultas")
13. A partir da fita **Home,** clique **em Fechar e Aplicar,** e depois clique em **Fechar e Aplicar**.
    
    ![Screenshot da fita Home com a opção close and Apply chamada.](./media/data-lake-store-power-bi/load-edited-query.png "Editar consultas")
14. Uma vez que a consulta é atualizada, o separador **Fields** mostrará os novos campos disponíveis para visualização.
    
    ![Campos atualizados](./media/data-lake-store-power-bi/updated-query-fields.png "Campos atualizados")
15. Criemos um gráfico de tartes para representar os condutores de cada cidade para um determinado país/região. Para tal, faça as seguintes seleções.
    
    1. A partir do separador Visualizações, clique no símbolo para um gráfico de tartes.
       
        ![Criar gráfico de tortas](./media/data-lake-store-power-bi/create-pie-chart.png "Criar gráfico de tortas")
    2. As colunas que vamos usar são a **Coluna 4** (nome da cidade) e a **Coluna 7** (nome do país/região). Arraste estas colunas do separador **Fields** para **o separador Visualizações,** como mostrado abaixo.
       
        ![Criar visualizações](./media/data-lake-store-power-bi/create-visualizations.png "Criar visualizações")
    3. O gráfico de tartes deve agora assemelhar-se como o mostrado abaixo.
       
        ![Gráfico de tortas](./media/data-lake-store-power-bi/pie-chart.png "Criar visualizações")
16. Ao selecionar um país/região específico a partir dos filtros de nível de página, pode agora ver o número de condutores em cada cidade do país/região selecionado. Por exemplo, no separador **Visualizações,** nos **filtros de nível page**, selecione **Brasil**.
    
    ![Selecionar país/região](./media/data-lake-store-power-bi/select-country.png "Selecionar país/região")
17. O gráfico de tartes é atualizado automaticamente para mostrar os condutores nas cidades do Brasil.
    
    ![Motoristas num país/região](./media/data-lake-store-power-bi/driver-per-country.png "Condutores por país/região")
18. A partir do menu **Ficheiro,** clique em **Guardar** para guardar a visualização como ficheiro de ambiente de trabalho Power BI.

## <a name="publish-report-to-power-bi-service"></a>Publicar relatório ao serviço Power BI
Uma vez criadas as visualizações no Power BI Desktop, pode partilhá-la com outras, publicando-as no serviço Power BI. Para obter instruções sobre como fazê-lo, consulte [a Publicação do Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Ver também
* [Analise os dados em Data Lake Storage Gen1 usando data lake analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

