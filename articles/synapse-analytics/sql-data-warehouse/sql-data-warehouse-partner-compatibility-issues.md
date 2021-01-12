---
title: Problemas de compatibilidade com aplicações de terceiros e Azure Synapse Analytics
description: Descreve problemas conhecidos que aplicações de terceiros podem encontrar com Azure Synapse
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: ''
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: a1031656eaa5125d07ae078773379270b26625e7
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121384"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>Problemas de compatibilidade com aplicações de terceiros e Azure Synapse Analytics

As aplicações construídas para o SQL Server funcionarão perfeitamente com piscinas SQL dedicadas Azure Synapse. Em alguns casos, no entanto, funcionalidades e elementos linguísticos que são geralmente utilizados no SQL Server podem não estar disponíveis em Azure Synapse, ou podem comportar-se de forma diferente.

Este artigo lista problemas conhecidos que pode encontrar ao utilizar aplicações de terceiros com a Azure Synapse Analytics. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Erro do Tableau: "Uma tentativa de concluir uma transação falhou. Não foi encontrada nenhuma transação correspondente"

A partir da versão dedicada ao pool SQL 10.0.11038.0 da Azure Synapse, algumas consultas do Tableau que fazem chamadas de procedimento armazenadas podem falhar com a seguinte mensagem de erro: "**[Microsoft][Controlador ODBC 17 para SQL Server][SQL Server]111214; Uma tentativa de concluir uma transação falhou. Nenhuma transação correspondente encontrada.**

### <a name="cause"></a>Causa

Este é um problema na piscina SQL dedicada Azure Synapse causada pela introdução de novos procedimentos armazenados do sistema que são automaticamente chamados pelos controladores ODBC e JDBC. Um desses procedimentos armazenados no sistema pode fazer com que as transações abertas sejam abortadas se falharem na execução. Este problema pode acontecer dependendo da lógica de aplicação do cliente.

### <a name="solution"></a>Solução
Os clientes que vêem este problema em particular ao utilizarem o Tableau ligado às piscinas SQL dedicadas da Azure Synapse devem definir FMTONLY para SIM na ligação SQL. Para o Tableau Desktop e Tableau Server, deverá utilizar um ficheiro de personalização de fonte de dados tableau (TDC) para garantir que o Tableau passa este parâmetro para o controlador.  

> [!NOTE] 
> A Microsoft não fornece suporte para ferramentas de terceiros. Embora tenhamos testado que esta solução funciona com tableau Desktop 2020.3.2, você deve usar esta solução na sua própria capacidade.
>

* [Para aprender a fazer personalizações globais com um ficheiro TDC no Tableau Desktop, consulte a documentação do Tableau Desktop.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Para aprender a fazer personalizações globais com um ficheiro TDC no Tableau Server, consulte a Utilização de um . Ficheiro TDC com Tableau Server.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

O exemplo abaixo mostra um ficheiro Tableau TDC que passa o parâmetro FMTONLY=SIM para a cadeia de ligação SQL:

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
Para mais detalhes sobre a utilização de ficheiros TDC, contacte o suporte do Tableau. 

## <a name="see-also"></a>Veja também

* [Elementos linguísticos T-SQL para piscina SQL dedicada em Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-language-elements.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
* [Declarações T-SQL apoiadas para piscina SQL dedicada em Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-statements.md)