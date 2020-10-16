---
title: Utilização da Azure DevTest Labs em vários laboratórios e subscrições
description: Saiba como reportar o uso da Azure DevTest Labs em vários laboratórios e subscrições.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1d2663113e929145308f5a5712b968f3551668c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87287272"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Reportar o uso da Azure DevTest Labs em vários laboratórios e subscrições

A maioria das grandes organizações quer acompanhar o uso de recursos para ser mais eficaz com esses recursos, visualizando tendências e outliers no uso. Com base no uso de recursos, os proprietários ou gestores de laboratório podem personalizar os laboratórios para melhorar o [uso de recursos e os custos.](../cost-management-billing/manage/getting-started.md) Em Azure DevTest Labs, você pode baixar o uso de recursos por laboratório permitindo um olhar histórico mais profundo sobre os padrões de uso. Estes padrões de utilização podem ajudar a identificar alterações para melhorar a eficiência. A maioria das empresas quer o uso individual do laboratório e o uso geral em [vários laboratórios e subscrições.](/azure/architecture/cloud-adoption/decision-guides/subscriptions/) 

Este artigo discute como lidar com informações de utilização de recursos em vários laboratórios e subscrições.

![Comunicar utilização](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Uso individual do laboratório

Esta secção discute como exportar o uso de recursos para um único laboratório.

Antes de poder exportar o uso de recursos da DevTest Labs, tem de criar uma conta de Armazenamento Azure para permitir que os diferentes ficheiros que contêm os dados de utilização sejam armazenados. Existem duas formas comuns de executar a exportação de dados:

* [DevTest Labs REST API](/rest/api/dtl/labs/exportresourceusage) 
* O módulo PowerShell Az.Resource [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) com a ação `exportResourceUsage` de, o ID de recursos de laboratório, e os parâmetros necessários. 

    O artigo [de exportação ou eliminação de dados pessoais](personal-data-delete-export.md) contém uma amostra do script PowerShell com informações detalhadas sobre os dados que são exportados. 

    > [!NOTE]
    > O parâmetro da data não inclui um carimbo de tempo, por isso os dados incluem tudo a partir da meia-noite com base no fuso horário onde o laboratório está localizado.

Uma vez concluída a exportação, haverá vários ficheiros CSV no armazenamento do blob com as diferentes informações de recursos.
  
Atualmente existem dois ficheiros CSV:

* *virtualmachines.csv* - contém informações sobre as máquinas virtuais no laboratório
* *disks.csv* - contém informações sobre os diferentes discos do laboratório 

Estes ficheiros são armazenados no recipiente de *bolhas labresourceusage* sob o nome do laboratório, ID exclusivo do laboratório, data executada, e ou na data de início que foi baseada no pedido de exportação. Uma estrutura de blob exemplo seria:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Exportação de utilização para todos os laboratórios

Para exportar a informação de uso para vários laboratórios considerar usar 

* [Funções Azure](../azure-functions/index.yml), disponíveis em muitos idiomas, incluindo PowerShell, ou 
* [Azure Automation runbook](../automation/index.yml), use PowerShell, Python ou um designer gráfico personalizado para escrever o código de exportação.

Usando estas tecnologias, você pode executar as exportações individuais de laboratório em todos os laboratórios em uma data e hora específicas. 

A sua função Azure deve empurrar os dados para o armazenamento a longo prazo. Ao exportar dados para vários laboratórios, a exportação pode demorar algum tempo. Para ajudar no desempenho e reduzir a possibilidade de duplicação de informação, recomendamos executar cada laboratório em paralelo. Para realizar o paralelismo, executar as Funções Azure assíncronamente. Aproveite também o gatilho do temporizador que as Funções Azure oferecem.

## <a name="using-a-long-term-storage"></a>Usando um armazenamento de longo prazo

Um armazenamento de longo prazo consolida a informação de exportação de diferentes laboratórios numa única fonte de dados. Outro benefício da utilização do armazenamento a longo prazo é conseguir remover os ficheiros da conta de armazenamento para reduzir a duplicação e o custo. 

O armazenamento a longo prazo pode ser usado para fazer qualquer manipulação de texto, por exemplo: 

* adicionar nomes amigáveis
* criando agrupamentos complexos
* agregação dos dados.

Algumas soluções de armazenamento comuns são: [SQL Server,](https://azure.microsoft.com/services/sql-database/) [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)e [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Escolher qual a solução de armazenamento de longo prazo que escolher, depende da preferência. Pode considerar escolher a ferramenta dependendo do que oferece em termos de disponibilidade de interação ao visualizar os dados.

## <a name="visualizing-data-and-gathering-insights"></a>Visualizar dados e recolher insights

Utilize uma ferramenta de visualização de dados à sua escolha para ligar ao seu armazenamento de longo prazo para exibir os dados de utilização e recolher informações para verificar a eficiência de utilização. Por exemplo, [o Power BI](/power-bi/power-bi-overview) pode ser usado para organizar e exibir os dados de utilização. 

Pode utilizar [a Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para criar, ligar e gerir os seus recursos dentro de uma única interface de localização. Se for necessário um maior controlo, o recurso individual pode ser criado dentro de um único grupo de recursos e gerido independentemente do serviço Data Factory.  

## <a name="next-steps"></a>Passos Seguintes

Uma vez que o sistema é configurado e os dados estão movendo-se para o armazenamento a longo prazo, o próximo passo é chegar a questões que os dados precisam de responder. Por exemplo: 

-   Qual é o uso do tamanho VM?

    Os utilizadores estão a selecionar tamanhos VM de alto desempenho (mais caros) ?
-   Que imagens do Marketplace estão a ser usadas?

    São imagens personalizadas a base VM mais comum, caso uma loja de Imagens comum seja construída como [A Galeria de Imagens Partilhada](../virtual-machines/windows/shared-image-galleries.md) ou a fábrica de [imagem.](image-factory-create.md)
-   Que imagens personalizadas estão a ser usadas ou não?
