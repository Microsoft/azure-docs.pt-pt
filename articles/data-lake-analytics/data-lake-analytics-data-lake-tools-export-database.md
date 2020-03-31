---
title: Exportação de bases de dados U-SQL - Ferramentas do Lago de Dados Azure para Estúdio Visual
description: Aprenda a usar ferramentas de lago de dados Azure para o Estúdio Visual para exportar uma base de dados U-SQL e importe-a automaticamente para uma conta local.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: e5a52de0342e864cb108d8d590583fe64f72e3b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315799"
---
# <a name="export-a-u-sql-database"></a>Exportar uma base de dados U-SQL

Neste artigo, aprenda a usar ferramentas de lago de [dados Azure para estúdio visual](https://aka.ms/adltoolsvs) para exportar uma base de dados U-SQL como um único script U-SQL e recursos descarregados. Pode importar a base de dados exportada para uma conta local no mesmo processo.

Os clientes geralmente mantêm múltiplos ambientes para desenvolvimento, teste e produção. Estes ambientes estão alojados tanto numa conta local, no computador local de um desenvolvedor, como numa conta Azure Data Lake Analytics em Azure. 

Quando desenvolve e sintoniza consultas U-SQL em ambientes de desenvolvimento e teste, os desenvolvedores muitas vezes precisam recriar o seu trabalho numa base de dados de produção. O Assistente de Exportação de Bases de Dados ajuda a acelerar este processo. Ao utilizar o assistente, os desenvolvedores podem clonar o ambiente de base de dados existente e recolher dados para outras contas do Data Lake Analytics.

## <a name="export-steps"></a>Etapas de exportação

### <a name="step-1-export-the-database-in-server-explorer"></a>Passo 1: Exportar a base de dados no Server Explorer

Todas as contas data Lake Analytics para as suas permissões estão listadas no Server Explorer. Para exportar a base de dados:

1. No Server Explorer, expanda a conta que contém a base de dados que pretende exportar.
2. Clique na base de dados e, em seguida, selecione **Export .** 
   
    ![Server Explorer - Exportar uma base de dados](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Se a opção de menu **Export** não estiver disponível, precisa de [atualizar a ferramenta para o lançamento mais dura](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Passo 2: Configure os objetos que pretende exportar

Se necessitar apenas de uma pequena parte de uma grande base de dados, pode configurar um subconjunto de objetos que pretende exportar no assistente de exportação. 

A ação de exportação é concluída com a execução de um trabalho u-SQL. Por conseguinte, a exportação de uma conta Azure incorre em algum custo.

![Assistente de Exportação de Bases de Dados - Selecione objetos de exportação](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Passo 3: Verifique a lista de objetos e outras configurações

Neste passo, pode verificar os objetos selecionados na caixa da lista de **objetos de exportação.** Se houver algum erro, selecione **Anterior** para voltar atrás e configurar corretamente os objetos que pretende exportar.

Também pode configurar outras definições para o alvo de exportação. As descrições de configuração estão listadas na tabela seguinte:

|Configuração|Descrição|
|-------------|-----------|
|Nome do destino|Este nome indica onde pretende poupar os recursos de base de dados exportados. Exemplos são conjuntos, ficheiros adicionais e dados de amostras. Uma pasta com este nome é criada sob a sua pasta de raiz de dados local.|
|Diretório de Projetos|Este caminho define onde pretende salvar o roteiro U-SQL exportado. Todas as definições de objetos de base de dados são guardadas neste local.|
|Apenas Schema|Se selecionar esta opção, apenas são exportadas definições e recursos de base de dados (como conjuntos e ficheiros adicionais).|
|Schema e Dados|Se selecionar esta opção, as definições de base de dados, os recursos e os dados são exportados. As primeiras fileiras n de mesas são exportadas.|
|Importar para base de dados local automaticamente|Se selecionar esta opção, a base de dados exportada é automaticamente importada para a sua base de dados local quando a exportação estiver concluída.|

![Assistente de Exportação de Bases de Dados - Lista de objetos de exportação e outras configurações](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Passo 4: Verifique os resultados da exportação

Quando a exportação estiver concluída, pode visualizar os resultados exportados na janela de registo do assistente. O exemplo que se segue mostra como encontrar recursos de script e base de dados u-SQL exportados, incluindo conjuntos, ficheiros adicionais e dados de amostras:

![Assistente de Exportação de Bases de Dados - Resultados da exportação](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importar a base de dados exportada para uma conta local

A forma mais conveniente de importar a base de dados exportada é selecionar a caixa de importação para base **de dados local Verifique automaticamente** a caixa durante o processo de exportação no passo 3. Se não verificou esta caixa, primeiro, encontre o script U-SQL exportado no registo de exportação. Em seguida, execute o script U-SQL localmente para importar a base de dados para a sua conta local.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importar a base de dados exportada para uma conta data lake analytics

Para importar a base de dados para diferentes contas data lake analytics:

1. Faça o upload dos recursos exportados, incluindo conjuntos, ficheiros adicionais e dados de amostra, para a conta padrão da Loja de Lagos De dados da conta Data Lake Analytics que pretende importar. Pode encontrar a pasta de recursos exportados sob a pasta de raiz de dados local. Faça upload de toda a pasta para a raiz da conta padrão data lake store.
2. Quando o upload estiver concluído, envie o script U-SQL exportado para a conta Data Lake Analytics para a que pretende importar a base de dados.

## <a name="known-limitations"></a>Limitações conhecidas

Atualmente, se selecionar a opção **Schema e Data** no Passo 3, a ferramenta executa um trabalho U-SQL para exportar os dados armazenados em tabelas. Por isso, o processo de exportação de dados pode ser lento e poderá incorrer em custos. 

## <a name="next-steps"></a>Passos seguintes

* [Conheça as bases de dados U-SQL](/u-sql/data-definition-language-ddl-statements) 
* [Testar e depurar tarefas U-SQL utilizando a execução local e o SDK U-SQL do Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)


