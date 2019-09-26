---
title: Exportar banco de dados U-SQL-Ferramentas do Azure Data Lake para Visual Studio
description: Saiba como usar Ferramentas do Azure Data Lake para Visual Studio para exportar um banco de dados U-SQL e importá-lo automaticamente para uma conta local.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: e5a52de0342e864cb108d8d590583fe64f72e3b6
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315799"
---
# <a name="export-a-u-sql-database"></a>Exportar um banco de dados U-SQL

Neste artigo, saiba como usar [Ferramentas do Azure data Lake para Visual Studio](https://aka.ms/adltoolsvs) para exportar um banco de dados u-SQL como um único script u-SQL e recursos baixados. Você pode importar o banco de dados exportado para uma conta local no mesmo processo.

Os clientes geralmente mantêm vários ambientes para desenvolvimento, teste e produção. Esses ambientes são hospedados em uma conta local, no computador local de um desenvolvedor e em uma conta de Azure Data Lake Analytics no Azure. 

Quando você desenvolve e ajusta consultas U-SQL em ambientes de desenvolvimento e teste, os desenvolvedores geralmente precisam recriar seu trabalho em um banco de dados de produção. O assistente para exportação de banco de dados ajuda a acelerar esse processo. Ao usar o assistente, os desenvolvedores podem clonar o ambiente de banco de dados existente e obter exemplos para outras contas de Data Lake Analytics.

## <a name="export-steps"></a>Etapas de exportação

### <a name="step-1-export-the-database-in-server-explorer"></a>Passo 1: Exportar o banco de dados no Gerenciador de Servidores

Todas as contas de Data Lake Analytics às quais você tem permissões estão listadas em Gerenciador de Servidores. Para exportar o banco de dados:

1. Em Gerenciador de Servidores, expanda a conta que contém o banco de dados que você deseja exportar.
2. Clique com o botão direito do mouse no banco de dados e selecione **Exportar**. 
   
    ![Gerenciador de Servidores-exportar um banco de dados](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Se a opção de menu **Exportar** não estiver disponível, você precisará [atualizar a ferramenta para a versão duração](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Passo 2: Configurar os objetos que você deseja exportar

Se você precisar apenas de uma pequena parte de um banco de dados grande, poderá configurar um subconjunto de objetos que deseja exportar no assistente de exportação. 

A ação de exportação é concluída com a execução de um trabalho do U-SQL. Portanto, a exportação de uma conta do Azure provoca algum custo.

![Assistente para exportação de banco de dados – selecionar exportar objetos](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Passo 3: Verificar a lista de objetos e outras configurações

Nesta etapa, você pode verificar os objetos selecionados na caixa de **listagem exportar objeto** . Se houver erros, selecione **anterior** para voltar e configurar corretamente os objetos que você deseja exportar.

Você também pode definir outras configurações para o destino de exportação. As descrições de configuração são listadas na tabela a seguir:

|Configuração|Descrição|
|-------------|-----------|
|Nome do destino|Esse nome indica onde você deseja salvar os recursos de banco de dados exportados. Os exemplos são assemblies, arquivos adicionais e dados de exemplo. Uma pasta com esse nome é criada na pasta raiz de dados local.|
|Diretório do projeto|Esse caminho define onde você deseja salvar o script U-SQL exportado. Todas as definições de objeto de banco de dados são salvas neste local.|
|Somente esquema|Se você selecionar essa opção, somente as definições de banco de dados e os recursos (como assemblies e arquivos adicionais) serão exportados.|
|Esquema e dados|Se você selecionar essa opção, as definições de banco de dados, os recursos e os dados serão exportados. As N linhas principais das tabelas são exportadas.|
|Importar para o banco de dados local automaticamente|Se você selecionar essa opção, o banco de dados exportado será importado automaticamente para o banco de dados local quando a exportação for concluída.|

![Assistente de exportação de banco de dados – exportar lista de objetos e outras configurações](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Passo 4: Verificar os resultados da exportação

Quando a exportação for concluída, você poderá exibir os resultados exportados na janela log no assistente. O exemplo a seguir mostra como localizar o script U-SQL exportado e os recursos do banco de dados, incluindo assemblies, arquivos adicionais e exemplos de dado:

![Assistente para exportação de banco de dados – exportar resultados](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importar o banco de dados exportado para uma conta local

A maneira mais conveniente de importar o banco de dados exportado é marcar a caixa de seleção **importar para o banco de dados local automaticamente** durante o processo de exportação na etapa 3. Se você não marcou essa caixa, primeiro localize o script U-SQL exportado no log de exportação. Em seguida, execute o script U-SQL localmente para importar o banco de dados para sua conta local.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importar o banco de dados exportado para uma conta de Data Lake Analytics

Para importar o banco de dados para uma conta de Data Lake Analytics diferente:

1. Carregue os recursos exportados, incluindo assemblies, arquivos adicionais e dados de exemplo, para a conta de Azure Data Lake Store padrão da conta de Data Lake Analytics para a qual você deseja importar. Você pode encontrar a pasta de recursos exportados na pasta raiz de dados local. Carregue a pasta inteira na raiz da conta de Data Lake Store padrão.
2. Quando o carregamento for concluído, envie o script U-SQL exportado para a conta de Data Lake Analytics para a qual você deseja importar o banco de dados.

## <a name="known-limitations"></a>Limitações conhecidas

Atualmente, se você selecionar a opção **esquema e dados** na etapa 3, a ferramenta executará um trabalho do U-SQL para exportar os dados armazenados em tabelas. Por isso, o processo de exportação de dados pode ser lento e você pode incorrer em custos. 

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre bancos de dados U-SQL](/u-sql/data-definition-language-ddl-statements) 
* [Testar e depurar tarefas U-SQL utilizando a execução local e o SDK U-SQL do Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)


