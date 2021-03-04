---
title: Log Analytics integração com Power BI e Excel
description: Como enviar resultados de Log Analytics para Power BI
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: 894b87bfa17a59db20b424199a4021ad75b182fc
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041064"
---
# <a name="log-analytics-integration-with-power-bi"></a>Log Analytics integração com Power BI

Este artigo centra-se em formas de alimentar dados do Log Analytics no Microsoft Power BI para criar relatórios e dashboards mais apelativos visualmente. 

## <a name="background"></a>Fundo 

O Azure Monitor Logs é uma plataforma que fornece uma solução de ponta a ponta para ingerir troncos. [Azure Monitor Log Analytics](../platform/data-platform.md#) é a interface para consultar estes registos. Para obter mais informações sobre toda a plataforma de dados do Azure Monitor, incluindo o Log Analytics, consulte [a plataforma de dados do Azure Monitor](../data-platform.md). 

O Microsoft Power BI é a plataforma de visualização de dados da Microsoft. Para obter mais informações sobre como começar, consulte a [página inicial do Power BI](https://powerbi.microsoft.com/). 


Em geral, pode utilizar funcionalidades power bi gratuitas para integrar e criar relatórios e dashboards visualmente apelativos.

Funcionalidades mais avançadas podem exigir a compra de uma conta Power BI Pro ou premium. Estas funcionalidades incluem: 
 - compartilhando o seu trabalho 
 - atualizações agendadas
 - Aplicações do Power BI 
 - fluxos de dados e atualização incremental 

Para mais informações, consulte [mais sobre preços e funcionalidades do Power BI](https://powerbi.microsoft.com/pricing/) 

## <a name="integrating-queries"></a>Integração de consultas  

Power BI usa a [linguagem de consulta M](/powerquery-m/power-query-m-language-specification/) como a sua principal linguagem de consulta. 

As consultas log Analytics podem ser exportadas para M e usadas diretamente no Power BI. Depois de executar uma consulta bem sucedida, selecione a **consulta Export to Power BI (consulta M)** a partir do botão **Exportação** na barra de ação superior do Log Analytics UI.


:::image type="content" source="media/log-powerbi/export-query2.png" alt-text="Consulta log Analytics mostrando a retirada do menu de opções de exportação" border="true":::

O Log Analytics cria um ficheiro .txt que contém o código M que pode ser utilizado diretamente no Power BI.

## <a name="connecting-your-logs-to-a-dataset"></a>Ligar os seus registos a um conjunto de dados 

Um conjunto de dados Power BI é uma fonte de dados prontos para reportar e visualizar. Para ligar uma consulta Log Analytics a um conjunto de dados, copie o código M exportado do Log Analytics numa consulta em branco no Power BI. 

Para obter mais informações, consulte [conjuntos de dados do Compreensão power bi](/power-bi/service-datasets-understand/). 

## <a name="collect-data-with-power-bi-dataflows"></a>Recolher dados com fluxos de dados do Power BI 

Os fluxos de dados do Power BI também permitem recolher e armazenar dados. Para obter mais informações, consulte [fluxos de dados power BI](/power-bi/service-dataflows-overview).

Um fluxo de dados é um tipo de "CLOUD ETL" projetado para ajudá-lo a recolher e preparar os seus dados. Um conjunto de dados é o "modelo" projetado para ajudá-lo a conectar diferentes entidades e modelá-las para as suas necessidades.

## <a name="incremental-refresh"></a>Atualização incremental 

Tanto os conjuntos de dados power BI como os fluxos de dados power BI têm uma opção de atualização incremental. Os fluxos de dados do Power BI e os conjuntos de dados power BI suportam esta funcionalidade, mas precisa do Power BI Premium para o utilizar.  


A atualização incremental executa pequenas consultas e atualiza quantidades menores de dados por execução em vez de ingerir todos os dados uma e outra vez quando executa a consulta. Tem a opção de guardar grandes quantidades de dados, mas adicione um novo incremento de dados sempre que a consulta é executada. Este comportamento é ideal para relatórios de execução mais longos.

A atualização incremental do Power BI baseia-se na existência de uma *data* registada no conjunto de resultados. Antes de configurar a atualização incremental, certifique-se de que o conjunto de resultados da consulta Log Analytics inclui pelo menos uma *data* arquivada. 

Para saber mais e como configurar a atualização incremental, consulte [os datasets power BI e atualização incremental](/power-bi/service-premium-incremental-refresh) e [fluxos de dados power bi e atualização incremental.](/power-bi/service-dataflows-incremental-refresh)

## <a name="reports-and-dashboards"></a>Relatórios e dashboards

Depois de os seus dados serem enviados para o Power BI, pode continuar a utilizar o Power BI para criar relatórios e dashboards.

Para mais informações, consulte [este guia sobre como criar o seu primeiro modelo Power BI e reportar.](/learn/modules/build-your-first-power-bi-report/)  

## <a name="excel-integration"></a>Integração do Excel

Pode utilizar a mesma integração M utilizada no Power BI para se integrar com uma folha de cálculo do Excel. Para mais informações, consulte este [guia sobre como se integrar com excel](https://support.microsoft.com/office/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) e, em seguida, colar a consulta M exportada da Log Analytics.

Informações adicionais podem ser encontradas em [Integração De Registos Analytics e Excel](log-excel.md)

## <a name="next-steps"></a>Passos seguintes

Começa com [consultas de Log Analytics](./log-query-overview.md).