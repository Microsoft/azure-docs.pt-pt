---
title: Novidades nas Coleções de Área de Trabalho do Power BI
description: Obter as informações mais recentes Novidades nas coleções de área de trabalho do Power BI
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: b29996b8e2cd9a95b9c1566036b1009b65c18bfa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60341986"
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>Novidades nas Coleções de Área de Trabalho do Power BI

Atualiza para **coleções de área de trabalho do Power BI** são lançadas regularmente. No entanto, nem toda versão inclui novas funcionalidades de destinada ao utilizador; Algumas versões estão focalizadas em capacidades do serviço de back-end. Nós destacamos aqui novas capacidades de destinada ao utilizador.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="march-2017"></a>Março de 2017

**Capacidades self-service**

* [Criar novo relatório](create-report-from-dataset.md)
* [SaveAs de relatório](save-reports.md)
* Incorporar relatório no modo de leitura/Editar/Criar novo 
* [Relatório de botão de alternar entre modos de edição/leitura](toggle-mode.md)

**Conectividade de dados com as APIs REST**

* [Criar conjunto de dados](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* Enviar dados por push 

**APIs de gestão**

* Clone de relatório e conjunto de dados
* Vincular o relatório a um conjunto de dados diferente

**Amostras**

* Atualizado [exemplo de incorporação de relatório de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo)

## <a name="december-2016"></a>Dezembro de 2016

* [Exemplo de incorporação de JavaScript novo](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>Outubro de 2016

* [Análise avançada com coleções do Power BI área de trabalho e do R](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>31 de Agosto de 2016
Esta versão inclui:

* Todos os novos SDK de JavaScript que suporta [advanced navegação de página e filtragem](interact-with-reports.md).
* As coleções de área de trabalho do Power BI são agora suportadas no Centro de dados Canadá Central. Verifique [estado de datacenter](https://azure.microsoft.com/status/).

## <a name="july-11-2016"></a>11 de julho de 2016
Esta versão inclui:

* **Temos ótimas notícias!** O serviço de coleções de área de trabalho do Power BI já não está em pré-visualização - GA seu agora (disponível em geral).  
* Todas as APIs de REST passaram a ter de **/beta** ao **/v1.0**.
* .NET e SDKs de JavaScript foi atualizado para o **v1.0**.
* Chamadas à API de BI Power agora podem ser autenticadas diretamente com chaves de API. Tokens de aplicação só são necessários para incorporar. Como parte desse processo, tokens de aprovisionamento e desenvolvimento foram preteridos na v1.0 APIs, mas estes continuarão a funcionar na versão beta até 30 de Dezembro de 2016. Para obter mais informações, consulte [autenticação e autorização com coleções de área de trabalho do Power BI](app-token-flow.md).
* Suporte de segurança ao nível (RLS) de linha para tokens de aplicação e os relatórios incorporados. Para obter mais informações, consulte [coleções de área de trabalho do Power BI a segurança ao nível da linha](row-level-security.md).
* Atualizado o aplicativo de exemplo para todos os **v1.0** chamadas de API.
* As coleções de área de trabalho do Power BI suporta para o SDK do Azure, PowerShell e CLI.
* Os utilizadores podem exportar dados de visualização para um **. csv**.
* As coleções de área de trabalho do Power BI agora são suportadas em todos os mesmos idiomas/locais como o Microsoft Azure. Para obter mais informações, consulte [Azure - idiomas](https://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).

