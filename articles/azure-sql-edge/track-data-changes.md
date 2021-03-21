---
title: Acompanhe as alterações de dados no Azure SQL Edge
description: Saiba mais sobre o rastreio de alterações e alterar a captura de dados em Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: dddaad3e171c757b353deb81ffcb77cfbe706340
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98108267"
---
# <a name="track-data-changes-in-azure-sql-edge"></a>Acompanhe as alterações de dados no Azure SQL Edge

O Azure SQL Edge suporta as duas funcionalidades do SQL Server que rastreiam as alterações aos dados numa base de dados: [alterar o rastreio](/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) e alterar a captura de [dados](/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Estas funcionalidades permitem que as aplicações determinem as alterações da linguagem de modificação de dados (inserir, atualizar e apagar operações) que foram feitas nas tabelas dos utilizadores numa base de dados. Pode ativar a captura de dados de alteração e alterar o rastreio na mesma base de dados. Não são necessárias considerações especiais.

A capacidade de consulta de dados que mudaram numa base de dados é um requisito importante para que algumas aplicações sejam eficientes. Normalmente, para determinar as alterações de dados, os desenvolvedores de aplicações devem implementar um método de rastreio personalizado nas suas aplicações, utilizando uma combinação de gatilhos, colunas de hora e tabelas adicionais. A criação destas aplicações envolve geralmente muito trabalho para implementar, leva a atualizações de esquemas, e muitas vezes carrega uma sobrecarga de alto desempenho.

No caso de uma solução IoT, onde é necessário mover periodicamente dados da borda para uma nuvem ou datacenter, o rastreio de alterações pode ser muito útil. Os utilizadores podem consultar de forma rápida e eficaz apenas as alterações a partir da última sincronização e carregar essas alterações para o alvo da nuvem ou datacenter. Para obter mais detalhes, consulte [os benefícios de utilizar a captura de dados de alteração ou alterar o rastreio.](/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking) 

Estas duas características não são as mesmas. Para obter mais informações, consulte [as diferenças de funcionalidades entre alterar a captura de dados e alterar o rastreio](/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>Captura de dados de alteração

Para compreender os detalhes de como esta funcionalidade funciona, consulte [Sobre a captura de dados de alteração.](/sql/relational-databases/track-changes/about-change-data-capture-sql-server)

Para compreender como ativar ou desativar esta funcionalidade, consulte [Ativar e desativar a captura de dados de alteração](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server).

Para administrar e monitorizar esta funcionalidade, consulte [Administrar e monitorizar a captura de dados de alteração](/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server).

Para entender como consultar e trabalhar com os dados alterados, consulte [Trabalhar com dados de alteração.](/sql/relational-databases/track-changes/work-with-change-data-sql-server)

> [!NOTE]
> As funções de Captura de Dados de Alteração que dependem do CLR não são suportadas na Borda SQL do Azure.

## <a name="change-tracking"></a>Monitorização de alterações

Para compreender os detalhes de como esta funcionalidade funciona, consulte [Sobre o tracking de alterações.](/sql/relational-databases/track-changes/about-change-tracking-sql-server)

Para compreender como ativar ou desativar esta funcionalidade, consulte [Ativar e desativar o rastreio de alterações](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server).

Para administrar, monitorizar e gerir esta funcionalidade, consulte [Administrar e monitorizar o rastreio de alterações](/sql/relational-databases/track-changes/manage-change-tracking-sql-server).

Para entender como consultar e trabalhar com os dados alterados, consulte [Trabalhar com dados de alteração.](/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)

## <a name="temporal-tables"></a>Tabelas temporais

O Azure SQL Edge também suporta a funcionalidade de tabelas temporais do SQL Server. Esta funcionalidade (também conhecida como *tabelas temporais versadas pelo sistema)* traz suporte incorporado para fornecer informações sobre dados armazenados na tabela a qualquer momento. A funcionalidade não fornece simplesmente informações sobre apenas os dados que estão corretos no momento atual.

Uma tabela temporal com a versão do sistema é um tipo de tabela de utilizadores projetada para manter um histórico completo de alterações de dados e para permitir uma análise fácil de pontos no tempo. Este tipo de tabela temporal é referido como uma tabela temporal com versão do sistema porque o período de validade para cada linha é gerido pelo sistema (isto é, o motor da base de dados).

Cada tabela temporal tem duas colunas explicitamente definidas, cada uma com um tipo de `datetime2` dado. Estas colunas são referidas como colunas de *época.* O sistema utiliza estas colunas de período exclusivamente, para registar o período de validade de cada linha sempre que uma linha é modificada.

Além destas colunas de período, uma tabela temporal também contém uma referência a outra tabela com um esquema espelhado. O sistema utiliza esta tabela para armazenar automaticamente a versão anterior da linha cada vez que uma linha na tabela temporal é atualizada ou eliminada. Esta tabela adicional é referida como a tabela *de história,* enquanto a tabela principal que armazena versões de linha atuais (reais) é referida como a tabela *atual,* ou simplesmente como a tabela temporal. Durante a criação de tabelas temporais, os utilizadores podem especificar uma tabela de histórico existente (deve estar em conformidade com o esquema), ou deixar o sistema criar a tabela de histórico padrão.

Para mais informações, consulte [as tabelas Temporal.](/sql/relational-databases/tables/temporal-tables)

## <a name="next-steps"></a>Passos seguintes

- [Streaming de dados em Azure SQL Edge ](stream-data.md)
- [Machine learning e IA com ONNX em Azure SQL Edge ](onnx-overview.md)
- [Configure a replicação para Azure SQL Edge](configure-replication.md)
- [Backup e restaurar bases de dados em Azure SQL Edge](backup-restore.md)
