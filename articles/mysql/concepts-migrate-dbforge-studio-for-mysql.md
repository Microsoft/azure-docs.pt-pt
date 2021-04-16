---
title: Ligue à Base de Dados Azure para o MySQL utilizando o dbForge Studio para o MySQL
description: O artigo demonstra como ligar à Base de Dados Azure para o MySQL Server via dbForge Studio para o MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 942651aadf4113c1aca32e4e1d2c558b0d764421
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377230"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>Ligue à Base de Dados Azure para o MySQL utilizando o dbForge Studio para o MySQL

Para ligar à Base de Dados Azure para o MySQL utilizando [o dbForge Studio para o MySQL:](https://www.devart.com/dbforge/mysql/studio/)

1. No menu Base de Dados, selecione Nova Ligação.

2. Forneça um nome de anfitrião e credenciais de login.

3. Selecione o botão De Ligação de Teste para verificar a configuração.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Ligação do Azure":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Migrar uma base de dados usando a funcionalidade Backup e Restaurar

O Estúdio permite migrar bases de dados para Azure de muitas maneiras, a escolha que depende apenas das suas necessidades. Se precisar de mover toda a base de dados, o melhor é utilizar a funcionalidade de Backup e Restaurar. Neste exemplo, migramos a base de dados *sakila* que reside no servidor MySQL para Azure Database para o MySQL. A lógica por trás do processo de migração utilizando a funcionalidade Backup e Restore do DBForge Studio para o MySQL é criar uma cópia de segurança da base de dados MySQL e, em seguida, restaurá-la na Base de Dados Azure para o MySQL.

### <a name="back-up-the-database"></a>Fazer o back-up da base de dados

1. No menu Base de Dados, aponte para Fazer Backup e Restaurar e, em seguida, selecione Backup Database. O assistente de backup da base de dados aparece.

2. No separador de conteúdo de cópia de segurança do Assistente de Cópia de Segurança da Base de Dados, selecione os objetos de base de dados que pretende fazer cópias de segurança.

3. No separador Opções, configube o processo de backup para se adaptar aos seus requisitos.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Apoiar opções de Assistente":::

4. Em seguida, especifique erros de comportamento de processamento e opções de registo.

5. Selecione Backup.

### <a name="restore-the-database"></a>Restaurar a base de dados

1. Ligue-se ao Azure para a Base de Dados para o MySQL, conforme descrito acima.

2. Clique com o botão direito no corpo do Explorador de dados, aponte para o Back up e para o Restauro e, em seguida, selecione Restaurar a Base de Dados.

3. Na base de dados Restaurar o Assistente que abre, selecione um ficheiro com uma cópia de segurança da base de dados.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Passo de restauro":::

4. Selecione Restaurar.

5. Verifique o resultado.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Migrar uma base de dados utilizando a funcionalidade Copy Databases

A funcionalidade Copy Databases é semelhante à cópia de segurança e restauro, exceto que com ela não precisa de dois passos para migrar uma base de dados. Além disso, a funcionalidade permite transferir duas ou mais bases de dados de uma só vez. A funcionalidade Copy Databases só está disponível na edição enterprise do DbForge Studio para o MySQL.
Neste exemplo, migramos a base de *dados world_x* do servidor MySQL para a Base de Dados Azure para o MySQL.
Para migrar uma base de dados utilizando a funcionalidade Copy Databases:

1. No menu Base de Dados, selecione Copy Databases. 

2. No separador Copy Databases que aparece, especifique a ligação de origem e alvo e selecione a base de dados a migrar. Introduzimos a ligação Azure MySQL e selecionamos a base *de dados world_x.* Selecione a seta verde para iniciar o processo.

3. Verifique o resultado.

Como resultado dos nossos esforços de migração de bases de dados, a base de dados *world_x* apareceu com sucesso no Azure MySQL.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Resultado das bases de dados de cópias":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Migrar uma base de dados usando ferramentas de comparação de esquemas e dados

o dbForge Studio para o MySQL incorpora algumas ferramentas que permitem migrar bases de dados MySQL, esquemas MySQL e\ou dados para a Azure. A escolha da funcionalidade depende das suas necessidades e dos requisitos do seu projeto. Se precisar de mover seletivamente uma base de dados, ou seja, migrar certas tabelas MySQL para Azure, o melhor é usar a funcionalidade Schema e Data Compare.
Neste exemplo, migramos a base de dados *mundial* que reside no servidor MySQL para a Base de Dados Azure para o MySQL. A lógica por trás do processo de migração utilizando a funcionalidade Schema e Data Compare do DbForge Studio para o MySQL é criar uma base de dados vazia na Base de Dados Azure para o MySQL, sincronizá-la com a base de dados MySQL necessária primeiro usando a ferramenta Schema Compare e depois usar a ferramenta Data Compare. Desta forma, os esquemas e dados do MySQL são transferidos com precisão para Azure.

### <a name="step-1-connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Passo 1. Ligue-se à Base de Dados Azure para o MySQL e crie uma base de dados vazia

### <a name="step-2-schema-synchronization"></a>Passo 2. Sincronização de Schema

1. No menu Comparação, selecione New Schema Compare.
O Novo Assistente de Comparação de Esquemas aparece.

2. Selecione a Fonte e o Alvo e, em seguida, especifique as opções de comparação de esquemas. Selecione Compare.

3. Na grelha de resultados de comparação que aparece, selecione objetos para sincronização. Selecione o botão de seta verde para abrir o Assistente de Sincronização de Schema.

4. Caminhe pelos degraus da sincronização configurante do assistente. Selecione Synchronize para implementar as alterações.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Assistente de sincronização de Schema":::

### <a name="step-3-data-comparison"></a>Passo 3. Comparação de dados

1. No menu Comparação, selecione Nova Comparação de Dados. O novo assistente de comparação de dados aparece.

2. Selecione a Fonte e o Alvo, em seguida, especifique as opções de comparação de dados e altere os mapeamentos se necessário. Selecione Compare.

3. Na grelha de resultados de comparação que aparece, selecione objetos para sincronização. Selecione o botão de seta verde para abrir o Assistente de Sincronização de Dados.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Resultado da comp de dados":::

4. Caminhe pelos degraus da sincronização configurante do assistente. Selecione Synchronize para implementar as alterações.

5. Verifique o resultado.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Resultado sincronizado de dados":::

## <a name="summary"></a>Resumo

Hoje em dia, mais empresas movem as suas bases de dados para Azure Database para o MySQL, uma vez que este serviço de base de dados é fácil de configurar, gerir e escalar. Que a migração não precisa de ser dolorosa. o dbForge Studio para o MySQL possui ferramentas de migração imaculadas que podem facilitar significativamente o processo. O Estúdio permite que a transferência de bases de dados seja facilmente configurada, guardada, editada, automatizada e programada.

## <a name="next-steps"></a>Passos seguintes
- [Visão geral do MySQL](overview.md)
