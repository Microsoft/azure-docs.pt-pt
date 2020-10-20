---
title: Exportação U-SQL base de dados - Azure Data Lake Tools for Visual Studio
description: Saiba como usar o Azure Data Lake Tools para o Visual Studio para exportar uma base de dados U-SQL e importá-la automaticamente para uma conta local.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 11/27/2017
ms.openlocfilehash: 0a768990a33f0d60a3222bd3ad323edcc53c9ec0
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92219093"
---
# <a name="export-a-u-sql-database"></a>Exportar uma base de dados U-SQL

Neste artigo, aprenda a usar a [Azure Data Lake Tools para o Visual Studio](https://aka.ms/adltoolsvs) para exportar uma base de dados U-SQL como um único script U-SQL e recursos descarregados. Pode importar a base de dados exportada para uma conta local no mesmo processo.

Os clientes geralmente mantêm múltiplos ambientes para desenvolvimento, teste e produção. Estes ambientes estão alojados numa conta local, no computador local de um desenvolvedor, e numa conta Azure Data Lake Analytics em Azure. 

Quando desenvolve e sintoniza as consultas U-SQL em ambientes de desenvolvimento e teste, os desenvolvedores muitas vezes precisam recriar o seu trabalho numa base de dados de produção. O Assistente de Exportação de Bases de Dados ajuda a acelerar este processo. Ao utilizar o assistente, os desenvolvedores podem clonar o ambiente de base de dados existente e recolher dados de amostra para outras contas do Data Lake Analytics.

## <a name="export-steps"></a>Etapas de exportação

### <a name="step-1-export-the-database-in-server-explorer"></a>Passo 1: Exportar a base de dados no Explorador de Servidores

Todas as contas do Data Lake Analytics para as que tem permissões estão listadas no Server Explorer. Para exportar a base de dados:

1. No Server Explorer, expanda a conta que contém a base de dados que pretende exportar.
2. Clique com o botão direito na base de dados e, em seguida, **selecione Export**. 
   
    ![Server Explorer - Exportar uma base de dados](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Se a opção **menu Exportação** não estiver disponível, tem de [atualizar a ferramenta para a versão mais dura](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Passo 2: Configurar os objetos que pretende exportar

Se precisar apenas de uma pequena parte de uma grande base de dados, pode configurar um subconjunto de objetos que pretende exportar no assistente de exportação. 

A ação de exportação é concluída com a execução de um trabalho U-SQL. Por conseguinte, a exportação de uma conta Azure incorre em algum custo.

![Assistente de exportação de bases de dados - Selecione objetos de exportação](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Passo 3: Verifique a lista de objetos e outras configurações

Neste passo, pode verificar os objetos selecionados na caixa de **lista de objetos Exportação.** Se houver erros, selecione **Anterior** para voltar e configurar corretamente os objetos que pretende exportar.

Também pode configurar outras definições para o objetivo de exportação. As descrições de configuração estão listadas na tabela seguinte:

|Configuração|Descrição|
|-------------|-----------|
|Nome do destino|Este nome indica onde pretende guardar os recursos de base de dados exportados. Exemplos são conjuntos, ficheiros adicionais e dados de amostras. Uma pasta com este nome é criada sob a pasta de raiz de dados local.|
|Diretório de Projetos|Este caminho define onde pretende salvar o script U-SQL exportado. Todas as definições de objetos de base de dados são guardadas neste local.|
|Só Schema|Se selecionar esta opção, apenas são exportadas definições e recursos de base de dados (como conjuntos e ficheiros adicionais).|
|Esquema e Dados|Se selecionar esta opção, são exportadas definições, recursos e dados de base de dados. As fileiras superiores n são exportadas.|
|Importação para Base de Dados Local Automaticamente|Se selecionar esta opção, a base de dados exportada é automaticamente importada para a sua base de dados local quando a exportação estiver concluída.|

![Assistente de exportação de bases de dados - Lista de objetos de exportação e outras configurações](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Passo 4: Verificar os resultados da exportação

Ao exportar, pode ver os resultados exportados na janela de registo do assistente. O exemplo a seguir mostra como encontrar os recursos de script e base de dados U-SQL exportados, incluindo conjuntos, ficheiros adicionais e dados de amostragem:

![Assistente de Exportação de Bases de Dados - Resultados da exportação](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importar a base de dados exportada para uma conta local

A forma mais conveniente de importar a base de dados exportada é selecionar a caixa **de verificação automática da Importação para** a Base de Dados Local durante o processo de exportação no passo 3. Se não verificou esta caixa, primeiro, encontre o script U-SQL exportado no diário de exportação. Em seguida, execute o script U-SQL localmente para importar a base de dados para a sua conta local.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importar a base de dados exportada para uma conta Data Lake Analytics

Para importar a base de dados para diferentes conta data lake analytics:

1. Faça o upload dos recursos exportados, incluindo conjuntos, ficheiros adicionais e dados de amostra, para a conta padrão da Azure Data Lake Store da conta Data Lake Analytics para a que pretende importar. Pode encontrar a pasta de recursos exportada sob a pasta de raiz de dados local. Faça o upload de toda a pasta para a raiz da conta padrão data lake store.
2. Quando o upload estiver terminado, envie o script U-SQL exportado para a conta Data Lake Analytics para a qual pretende importar a base de dados.

## <a name="known-limitations"></a>Limitações conhecidas

Atualmente, se selecionar a opção **Schema e Data** no Passo 3, a ferramenta executa um trabalho U-SQL para exportar os dados armazenados em tabelas. Por isso, o processo de exportação de dados pode ser lento e poderá incorrer em custos. 

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre bases de dados U-SQL](/u-sql/data-definition-language-ddl-statements) 
* [Testar e depurar tarefas U-SQL utilizando a execução local e o SDK U-SQL do Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)


