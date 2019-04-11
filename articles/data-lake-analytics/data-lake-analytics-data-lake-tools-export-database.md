---
title: Exportar uma base de dados U-SQL com o Azure Data Lake Tools para Visual Studio
description: Saiba como utilizar o Azure Data Lake Tools para Visual Studio para exportar uma base de dados U-SQL e importá-lo automaticamente para uma conta local.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: fe28aa8b88f557d4bbcdabf1de1c4bc6491743ce
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471414"
---
# <a name="export-a-u-sql-database"></a>Exportar uma base de dados U-SQL

Neste artigo, saiba como utilizar [do Azure Data Lake Tools para Visual Studio](https://aka.ms/adltoolsvs) para exportar uma base de dados U-SQL como um único script de U-SQL e de recursos transferidos. Pode importar a base de dados exportada para uma conta local no mesmo processo.

Os clientes geralmente mantém vários ambientes para desenvolvimento, teste e produção. Estes ambientes estão alojados em ambos os uma conta local, no computador local de um desenvolvedor e numa conta do Azure Data Lake Analytics no Azure. 

Quando desenvolve e otimização das consultas U-SQL em ambientes de desenvolvimento e teste, os desenvolvedores frequentemente precisam voltar a criar seu trabalho num banco de dados de produção. Assistente para exportar a base de dados ajuda a acelerar esse processo. Ao utilizar o assistente, os desenvolvedores podem clonar o ambiente de base de dados existente e os dados de exemplo para outras contas do Data Lake Analytics.

## <a name="export-steps"></a>Etapas de exportação

### <a name="step-1-export-the-database-in-server-explorer"></a>Passo 1: Exportar a base de dados no Explorador de servidores

Todas as contas de Data Lake Analytics, que tem permissões para estão listadas no Explorador de servidores. Para exportar a base de dados:

1. No Explorador de servidores, expanda a conta que contém a base de dados que pretende exportar.
2. A base de dados com o botão direito e, em seguida, selecione **exportar**. 
   
    ![Explorador de servidores - exportar uma base de dados](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Se o **exportar** opção de menu não estiver disponível, terá [a ferramenta de atualização para a versão lasted](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Passo 2: Configurar os objetos que pretende exportar

Se tiver apenas uma pequena parte de uma base de dados grandes, pode configurar um subconjunto de objetos que pretende exportar no Assistente de exportação. 

A ação de exportação é concluída ao executar uma tarefa U-SQL. Por conseguinte, a exportação de uma conta do Azure implica alguns custos.

![Assistente de exportação de base de dados - selecione Exportar objetos](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Passo 3: Verifique a lista de objetos e outras configurações

Neste passo, pode verificar os objetos selecionados no **lista de objetos de exportação** caixa. Se existirem erros, selecione **Previous** para voltar atrás e configurar corretamente os objetos que pretende exportar.

Pode também configurar outras definições para o destino de exportação. Descrições de configuração estão listadas na tabela a seguir:

|Configuração|Descrição|
|-------------|-----------|
|Nome de destino|Este nome indica onde pretende guardar os recursos de base de dados exportada. Os exemplos são assemblagens, ficheiros adicionais e dados de exemplo. É criada uma pasta com este nome na sua pasta de raiz de dados local.|
|Diretório do projeto|Este caminho define onde pretende guardar o script de U-SQL exportado. Todas as definições de objetos de base de dados são guardadas nesta localização.|
|Apenas esquema|Se selecionar esta opção, apenas as definições de base de dados e recursos (como assemblies e arquivos adicionais) são exportados.|
|Esquema e os dados|Se selecionar esta opção, as definições de base de dados, recursos e dados são exportados. As primeiras linhas de N das tabelas são exportadas.|
|Importar automaticamente a base de dados Local|Se selecionar esta opção, a base de dados exportada é importado automaticamente para a base de dados local quando exportar é concluído.|

![Assistente de exportação de base de dados - lista de objetos de exportação e outras configurações](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Passo 4: Verificar os resultados de exportação

Quando a exportação for concluída, pode ver os resultados exportados na janela de registo no assistente. O exemplo seguinte mostra como encontrar exportados U-SQL script e a base de dados recursos, incluindo os assemblies, ficheiros adicionais e dados de exemplo:

![Assistente de exportação de base de dados - exportar resultados](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importar a base de dados exportada para uma conta local

A forma mais fácil de importar a base de dados exportada é selecionar o **importar para o Local da base de dados automaticamente** caixa de verificação durante o processo de exportação na etapa 3. Se não selecionar esta caixa, primeiro, encontre o script de U-SQL exportado no registo de exportação. Em seguida, execute o script de U-SQL localmente para importar a base de dados à sua conta local.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importar a base de dados exportada para uma conta do Data Lake Analytics

Para importar a base de dados para outra conta do Data Lake Analytics:

1. Carregar os recursos exportados, incluindo os assemblies, ficheiros adicionais e dados de exemplo, para a conta do Azure Data Lake Store predefinida da conta do Data Lake Analytics que pretende importar para o. Pode encontrar a pasta de recurso exportado na pasta raiz de dados local. Carregar toda a pasta na raiz da conta de Data Lake Store predefinida.
2. Quando o carregamento estiver concluído, submeta o script de U-SQL exportado para a conta do Data Lake Analytics que pretende importar a base de dados.

## <a name="known-limitations"></a>Limitações conhecidas

Atualmente, se selecionar a **esquema e os dados** opção no passo 3, a ferramenta executa uma tarefa de U-SQL para exportar os dados armazenados em tabelas. Por este motivo, os dados a exportar o processo podem ser lentos e pode implicar custos. 

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre bases de dados U-SQL](/u-sql/data-definition-language-ddl-statements) 
* [Testar e depurar tarefas U-SQL utilizando a execução local e o SDK do Azure Data Lake U-SQL](data-lake-analytics-data-lake-tools-local-run.md)


