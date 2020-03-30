---
title: Utilização de Laboratórios Azure DevTest em vários laboratórios e subscrições
description: Saiba como reportar o uso do Azure DevTest Labs em vários laboratórios e subscrições.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: takamath
ms.openlocfilehash: 912f510f6380c0ba1eb92b7c485091801123558e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169188"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Reportar o uso de Laboratórios Azure DevTest em vários laboratórios e subscrições

A maioria das grandes organizações quer acompanhar o uso de recursos para ser mais eficaz com esses recursos visualizando tendências e outliers no uso. Com base no uso de recursos, os proprietários ou gestores de laboratóriopodem personalizar os laboratórios para melhorar o [uso e os custos](https://docs.microsoft.com/azure/billing/billing-getting-started)dos recursos. Em Azure DevTest Labs, você pode baixar o uso de recursos por laboratório, permitindo um olhar histórico mais profundo sobre os padrões de uso. Estes padrões de utilização podem ajudar a identificar alterações para melhorar a eficiência. A maioria das empresas quer tanto o uso individual de laboratório como o uso geral em [vários laboratórios e subscrições.](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) 

Este artigo discute como lidar com informações de uso de recursos em vários laboratórios e subscrições.

![Comunicar utilização](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Utilização individual do laboratório

Esta secção discute como exportar o uso de recursos para um único laboratório.

Antes de poder exportar o uso de recursos da DevTest Labs, tem de configurar uma conta de Armazenamento Azure para permitir que os diferentes ficheiros que contenham os dados de utilização sejam armazenados. Existem duas formas comuns de executar a exportação de dados:

* [DevTest Labs REST API](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* O módulo PowerShell Az.Resource [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) com a ação de , o ID de recurso de `exportResourceUsage`laboratório, e os parâmetros necessários. 

    O artigo [de exportação ou eliminação](personal-data-delete-export.md) de dados pessoais contém um script powerShell da amostra com informações detalhadas sobre os dados que são exportados. 

    > [!NOTE]
    > O parâmetro da data não inclui um carimbo de tempo, por isso os dados incluem tudo a partir da meia-noite com base no fuso horário onde o laboratório está localizado.

Uma vez concluída a exportação, haverá vários ficheiros CSV no armazenamento de blob com as diferentes informações de recursos.
  
Atualmente existem dois ficheiros CSV:

* *virtualmachines.csv* - contém informações sobre as máquinas virtuais no laboratório
* *disks.csv* - contém informações sobre os diferentes discos no laboratório 

Estes ficheiros são armazenados no recipiente blob de uso de *labresource* sob o nome de laboratório, identificação única do laboratório, data executada, e ou completa ou a data de início que foi baseada no pedido de exportação. Um exemplo de estrutura blob seria:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Exportação de utilização para todos os laboratórios

Para exportar a informação de uso para vários laboratórios considere usar 

* [Funções Azure](https://docs.microsoft.com/azure/azure-functions/), disponíveis em muitos idiomas, incluindo PowerShell, ou 
* O livro de [execução Azure Automation](https://docs.microsoft.com/azure/automation/), use PowerShell, Python ou um designer gráfico personalizado para escrever o código de exportação.

Usando estas tecnologias, você pode executar as exportações individuais de laboratório em todos os laboratórios em uma data e hora específicas. 

A sua função Azure deve empurrar os dados para o armazenamento a mais longo prazo. Ao exportar dados para vários laboratórios, a exportação pode demorar algum tempo. Para ajudar no desempenho e reduzir a possibilidade de duplicação de informação, recomendamos executar cada laboratório em paralelo. Para realizar o paralelismo, executar funções Azure assincronicamente. Aproveite também o gatilho do temporizador que as Funções Azure oferecem.

## <a name="using-a-long-term-storage"></a>Usando um armazenamento a longo prazo

Um armazenamento a longo prazo consolida a informação de exportação de diferentes laboratórios numa única fonte de dados. Outro benefício da utilização do armazenamento a longo prazo é conseguir remover os ficheiros da conta de armazenamento para reduzir a duplicação e o custo. 

O armazenamento a longo prazo pode ser usado para fazer qualquer manipulação de texto, por exemplo: 

* adicionando nomes amigáveis
* criando agrupamentos complexos
* agregando os dados.

Algumas soluções comuns de armazenamento são: [SQL Server,](https://azure.microsoft.com/services/sql-database/) [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)e [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Escolher qual a solução de armazenamento a longo prazo que escolher, depende da preferência. Pode considerar escolher a ferramenta dependendo do que oferece em termos de disponibilidade de interação ao visualizar os dados.

## <a name="visualizing-data-and-gathering-insights"></a>Visualizar dados e recolher insights

Utilize uma ferramenta de visualização de dados à sua escolha para se ligar ao seu armazenamento a longo prazo para mostrar os dados de utilização e recolher insights para verificar a eficiência de utilização. Por exemplo, o [Power BI](https://docs.microsoft.com/power-bi/power-bi-overview) pode ser usado para organizar e exibir os dados de utilização. 

Pode utilizar a [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para criar, ligar e gerir os seus recursos numa única interface de localização. Se for necessário um maior controlo, o recurso individual pode ser criado dentro de um único grupo de recursos e gerido independentemente do serviço data Factory.  

## <a name="next-steps"></a>Passos Seguintes

Uma vez que o sistema é configurado e os dados estão se movendo para o armazenamento a longo prazo, o próximo passo é chegar às perguntas que os dados precisam responder. Por exemplo: 

-   Qual é o uso do tamanho vm?

    Os utilizadores estão a selecionar tamanhos VM de alto desempenho (mais caros) ?
-   Que imagens do Marketplace estão a ser usadas?

    São imagens personalizadas a base VM mais comum, caso uma loja de imagem comum seja construída como Galeria de [Imagem Partilhada](../virtual-machines/windows/shared-image-galleries.md) ou Fábrica [de Imagem.](image-factory-create.md)
-   Que imagens personalizadas estão a ser usadas ou não?
