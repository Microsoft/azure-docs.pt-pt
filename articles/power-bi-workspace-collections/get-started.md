---
title: Introdução às Coleções de Áreas de Trabalho do Microsoft Power BI
description: As Coleções de Áreas de Trabalho do Power BI são um serviço do Azure que permite aos programadores de aplicações adicionar relatórios de Power BI interativos às suas próprias aplicações.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: maghan
ms.openlocfilehash: 944f008d0243d974a5d439438a5a645b7f3ca248
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57856795"
---
# <a name="get-started-with-microsoft-power-bi-workspace-collections"></a>Introdução às Coleções de Áreas de Trabalho do Microsoft Power BI

**As Coleções de Áreas de Trabalho do Power BI** são um serviço do Azure que permite aos programadores de aplicações adicionar relatórios de Power BI interativos às suas próprias aplicações. As **Coleções de Áreas de Trabalho do Power BI** funcionam com as aplicações existentes sem precisar de recriar ou alterar a forma como os utilizadores iniciam sessão.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Os recursos das **Coleções de Áreas de Trabalho do Microsoft Power BI** são aprovisionados através das [APIs do Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). Neste caso, o recurso aprovisionado é uma **Coleção de Áreas de Trabalho do Power BI**.

![Fluxo geral das Coleções de Áreas de Trabalho do Power BI](media/get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Criar uma coleção de área de trabalho

Uma **Coleção de Área de Trabalho** é o recurso mais superior do Azure e um contentor para o conteúdo que será incorporado na aplicação. Uma **Coleção de Áreas de Trabalho** pode ser criada de duas formas:

* Manualmente pela utilização do portal do Azure
* Através de programação através das APIs do Azure Resource Manager

Vamos guiá-lo pelos passos para criar uma **Coleção de Áreas de Trabalho** através do portal do Azure.

1. Abra e Inicie sessão no **portal do Azure**: [https://portal.azure.com](https://portal.azure.com).
2. Selecione **+ Novo** no painel superior.
   
   ![+ Novo no portal do Azure](media/get-started/create-workspace-1.png)
3. Em **Dados + Análise**, selecione **Coleção de Áreas de Trabalho do Power BI**.
4. Na mensagem de introdução, se já tiver uma subscrição da Coleção de Áreas de Trabalho do Power BI existente, selecione **Criar uma coleção de áreas de trabalho** na parte inferior.

5. Em **Coleção de Áreas de Trabalho**, introduza as informações necessárias.
   
   ![Criação de uma coleção de áreas de trabalho](media/get-started/create-workspace-2.png)
1. Selecione **Criar**.

A **Coleção de Áreas de Trabalho** leva alguns minutos a aprovisionar. Quando concluída, é direcionado para a **Coleção de Áreas de Trabalho**.

   ![Coleção de áreas de trabalho no portal do Azure](media/get-started/create-workspace-3.png)

Os resultados de **Criação** contêm as informações de que precisa para chamar as APIs que criam áreas de trabalho e implementam conteúdo nas mesmas.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Ver chaves de acesso da API do Power BI

Uma das informações mais importantes necessárias para chamar as APIs REST do Power BI é são as **Teclas de Acesso**. Estas são utilizadas para gerar os **tokens de aplicação** utilizados para autenticar os pedidos de API. Para ver as suas **Chaves de Acesso**, clique em **Chaves de Acesso** em **Definições**. Para obter mais informações sobre **tokens de aplicação**, veja [Autenticação e autorização com Coleções de Áreas de Trabalho do Power BI](app-token-flow.md).

   ![Chaves de acesso nas definições da Coleção de Áreas de Trabalho no portal do Azure](media/get-started/access-keys.png)

Vai notar que tem duas chaves.

   ![Duas chaves nas chaves de acesso](media/get-started/access-keys-2.png)

Copie estas chaves e armazene-as de forma segura na aplicação. É importante que trate estas chaves como se fossem uma palavra-passe, porque dão acesso a todo o conteúdo na **Coleção de Áreas de Trabalho**.

Embora sejam apresentadas duas chaves, apenas é necessária uma de cada vez. A segunda chave é fornecida para periodicamente regenerar chaves sem interromper o acesso ao serviço.

Agora que tem uma instância do Power BI para a aplicação e as **Chaves de Acesso**, pode importar um relatório para a própria aplicação. Antes de saber como importar um relatório, a secção a seguir descreve como criar conjuntos de dados do Power BI e relatórios para incorporar numa aplicação.

## <a name="working-with-workspaces"></a>Trabalhar com áreas de trabalho

Depois de ter criado a sua coleção da área de trabalho, terá de criar uma área de trabalho que irá guardar os relatórios e conjuntos de dados. Para criar uma área de trabalho, tem de utilizar a [API REST de Área de Trabalho de Publicação](https://msdn.microsoft.com/library/azure/mt711503.aspx).

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Criar conjuntos de dados e relatórios do Power BI para incorporar numa aplicação com o Power BI Desktop

Agora que já criou uma instância do Power BI para a aplicação e tem **Chaves de Acesso**, tem de criar os conjuntos de dados do Power BI e os relatórios que quer incorporar. Os conjuntos de dados e os relatórios podem ser criados com o **Power BI Desktop**. Pode transferir o [Power BI Desktop gratuitamente](https://go.microsoft.com/fwlink/?LinkId=521662). Em alternativa, para começar rapidamente, pode transferir o [PBIX de exemplo de análise de revenda](https://go.microsoft.com/fwlink/?LinkID=780547).

> [!NOTE]
> Para saber mais sobre como utilizar **Power BI Desktop**, consulte o artigo [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Com o **Power BI Desktop** liga-se à fonte dos dados através da importação de uma cópia dos dados no **Power BI Desktop** ou ligando diretamente para a fonte de dados utilizando **DirectQuery**.

Estas são as diferenças entre a utilização de **Importar** e **DirectQuery**.

| Importar | DirectQuery |
| --- | --- |
| Tabelas, colunas *e dados* são importados ou copiados para o **Power BI Desktop**. À medida que trabalha com visualizações, o **Power BI Desktop** consulta uma cópia dos dados. Para ver as alterações ocorridas nos dados subjacentes, tem de atualizar ou importar novamente um conjunto de dados completo e atual. |Apenas *tabelas e colunas* são importadas ou copiadas para o **Power BI Desktop**. À medida que trabalha com visualizações, o **Power BI Desktop** consulta a fonte de dados subjacente, o que significa que está sempre a ver dados atuais. |

Para obter mais informações sobre como ligar a uma fonte de dados, consulte o artigo [Ligar a uma fonte de dados](connect-datasource.md).

Depois de guardar o trabalho no **Power BI Desktop**, é criado um ficheiro PBIX. Este ficheiro contém o relatório. Além disso, se importar dados, o PBIX conterá o conjunto de dados completo, mas se utilizar o **DirectQuery**, o PBIX conterá apenas um esquema de conjunto de dados. Implemente o PBIX através de programação na área de trabalho utilizando a [API de Importação do Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [!NOTE]
> As **Coleções de Áreas de Trabalho do Power BI** incluem APIs adicionais para alterar o servidor e a base de dados para a qual o conjunto de dados está a apontar, e para definir uma credencial de conta de serviço que o conjunto de dados utilizará para ligar à base de dados. Consulte o artigo [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) e [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx) (Correção de fonte de dados do Gateway).

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>Criar conjuntos de dados e relatórios do Power BI com APIs

### <a name="datasets"></a>Conjuntos de dados

Pode criar conjuntos de dados nas Coleções de Áreas de Trabalho do Power BI com a API REST. Em seguida, pode enviar os dados para o conjunto de dados. Isto permite trabalhar com os dados sem necessitar do Power BI Desktop. Para obter mais informações, veja [Pós-conjuntos de dados](https://msdn.microsoft.com/library/azure/mt778875.aspx).

### <a name="reports"></a>Relatórios

Pode criar um relatório a partir de um conjunto de dados diretamente na aplicação com a API JavaScript. Para obter mais informações, veja [Criar um novo relatório a partir de um conjunto de dados nas Coleções de Áreas de Trabalho do Power BI](create-report-from-dataset.md).

## <a name="see-also"></a>Consulte também

[Introdução com exemplo](get-started-sample.md)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[Incorporar um relatório](embed-report.md)  
[Criar um novo relatório a partir de um conjunto de dados nas Coleções de Áreas de Trabalho do Power BI](create-report-from-dataset.md)
[Guardar relatórios](save-reports.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Mais perguntas? [Tente a Comunidade do Power BI](https://community.powerbi.com/)

