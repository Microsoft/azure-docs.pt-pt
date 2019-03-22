---
title: Ligar a uma origem de dados nas coleções de área de trabalho do Power BI | Documentos da Microsoft
description: Saiba como ligar a uma origem de dados em coleções de área de trabalho do Power BI.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: c67369edec2f3c84ada14e6c2a3a3ab8056b3e0c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57870100"
---
# <a name="connect-to-a-data-source"></a>Ligar a uma origem de dados

Com o **coleções de área de trabalho do Power BI**, pode incorporar relatórios na sua própria aplicação. Quando incorporar um relatório do Power BI na aplicação, o relatório liga-se aos dados subjacentes ao **importação** uma cópia dos dados ou pelo **ligar diretamente** para a origem de dados com **DirectQuery** .

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Estas são as diferenças entre a utilização de **Importar** e **DirectQuery**.

| Importar | DirectQuery |
| --- | --- |
| Tabelas, colunas *e os dados* são importadas ou copiadas para o conjunto de dados do relatório. Para ver as alterações que ocorreram aos dados subjacentes, é necessário atualizar ou importar um completo e atual conjunto de dados novamente. |Apenas *tabelas e colunas* são importadas ou copiadas para o conjunto de dados do relatório. Sempre a ver os dados mais recentes. |

Coleções de área de trabalho do Power BI, pode utilizar o DirectQuery com origens de dados na cloud, mas não no local origens de dados neste momento.

> [!NOTE]
> Neste momento, o Gateway de dados no local não é suportado com coleções de área de trabalho do Power BI. Isso significa que não é possível utilizar o DirectQuery com origens de dados no local.

## <a name="supported-data-sources"></a>Origens de dados suportadas

**DirectQuery**
* Base de dados SQL do Azure
* Azure SQL Data Warehouse

**Importar**

Pode importar usar todas as origens de dados disponíveis no Power BI Desktop. Irá **não** conseguir atualizar esses dados nas coleções de área de trabalho do Power BI. Terá de carregar as alterações ao ficheiro PBIX para coleções de área de trabalho do Power BI. Isso se deve nenhum gateway disponível. 

## <a name="benefits-of-using-directquery"></a>Benefícios da utilização do DirectQuery

Existem duas principais benefícios ao usar **DirectQuery**:

* **DirectQuery** permite-lhe criar visualizações de grandes conjuntos de dados, em que, caso contrário, seria impraticável importar primeiro todos os dados.
* Subjacente a alterações de dados pode exigir uma atualização de dados e para alguns relatórios, a necessidade de apresentar os dados atuais pode exigir transferências de dados grandes, tornando impraticável a voltar a importar dados. Por outro lado, **DirectQuery** relatórios utilizam sempre dados atuais.

## <a name="limitations-of-directquery"></a>Limitações do DirectQuery

Existem algumas limitações na utilização **DirectQuery**:

* Todas as tabelas têm provenientes de uma base de dados.
* Se a consulta é demasiado complexa, ocorrerá um erro. Para corrigir esse erro deve refatorar a consulta para que seja menos complexo. Se a consulta tem de ser complexa, terá de importar os dados em vez de usar **DirectQuery**.
* Filtragem de relação está limitada a uma única direção, em vez de ambas as direções.
* Não é possível alterar o tipo de dados de uma coluna.
* Por predefinição, as limitações são colocadas em expressões DAX permitidas em medidas. Ver [DirectQuery e medidas](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery e medidas
Para garantir consultas enviadas para a origem de dados subjacente têm um desempenho aceitável, são impostas limitações às medidas. Ao usar **Power BI Desktop**, avançado os utilizadores podem optar por ignorar esta limitação ao selecionar **ficheiro > Opções e definições > opções**. Na **opções** caixa de diálogo, escolha **DirectQuery**e selecione a opção **permitir medidas não restritas no modo DirectQuery**. Quando essa opção for selecionada, qualquer expressão DAX que é válido para uma medida pode ser utilizado. Os utilizadores têm de estar cientes; No entanto, que algumas expressões que funcionam bem quando os dados são importados podem resultar em lenta consulta para o back-end de origem quando estiver no **DirectQuery** modo. 

## <a name="see-also"></a>Consultar Também

* [Começar a utilizar coleções de área de trabalho do Microsoft Power BI](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Mais perguntas? [Tente a Comunidade do Power BI](https://community.powerbi.com/)

