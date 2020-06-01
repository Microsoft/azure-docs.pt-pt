---
title: Alterações de dados de rastreio em Azure SQL Edge (Pré-visualização)
description: Saiba mais sobre o rastreio de alterações e alterar a captura de dados em Azure SQL Edge (Preview)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3e3a6c3e171383d1812f63b945735d1b82f70b9c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235091"
---
# <a name="tracking-data-changes-in-azure-sql-edge-preview"></a>Alterações de dados de rastreio em Azure SQL Edge (Pré-visualização)

O Azure SQL Edge suporta as duas funcionalidades do SQL Server que rastreiam as alterações aos dados numa base de dados:[Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) and Change [Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Estas funcionalidades permitem que as aplicações determinem as alterações de DML (inserir, atualizar e apagar operações) que foram feitas às tabelas dos utilizadores numa base de dados. Alterar a captura de dados e o rastreio de alterações pode ser ativado na mesma base de dados; não são necessárias considerações especiais.

A capacidade de consulta de dados que mudaram numa base de dados é um requisito importante para que algumas aplicações sejam eficientes. Normalmente, para determinar as alterações de dados, os desenvolvedores de aplicações devem implementar um método de rastreio personalizado nas suas aplicações, utilizando uma combinação de gatilhos, colunas de hora e tabelas adicionais. A criação destas aplicações envolve geralmente muito trabalho para implementar, leva a atualizações de esquemas, e muitas vezes carrega uma sobrecarga de alto desempenho. No caso da solução IoT, onde é necessário mover periodicamente os dados da borda para uma nuvem ou centro de dados, o rastreio de alterações pode ser muito útil. Isto permitiria que se consultasse rapidamente e eficazmente apenas as alterações delta a partir da última sincronização e carregaria essas alterações para o alvo da nuvem ou do centro de dados. Para obter mais detalhes sobre os benefícios da utilização do Change Tracking and Change Data Capture, consulte [os Benefícios da utilização da captura de dados de alteração ou do rastreio de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Para compreender as diferenças de funcionalidade entre alterar o rastreio e alterar a captura de [dados, consulte as diferenças de funcionalidades entre a captura de dados de alteração e o rastreio de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>Captura de Dados Alterados

Para compreender os detalhes de como funciona a Captura de Dados de Alteração, consulte [Sobre a Captura de Dados de Alteração](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

Para entender como ativar ou desativar a captura de dados de alteração, consulte [Ativar e desativar a captura de dados de alteração](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)

Para administrar e monitorizar a captura de dados de alteração, consulte [a Gestão e MonitorIze a Captura de Dados de Alteração](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)

Para entender como consultar e trabalhar com os dados alterados, consulte [o Trabalho com Os Dados de Alteração](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server)

## <a name="change-tracking"></a>Monitorização de Alterações

Para compreender os detalhes de como funciona o Change Tracking, consulte [About Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Para entender como ativar ou desativar o Rastreio de Alterações, consulte [Ativar e Desativar o Rastreio de Alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)

Para administrar, monitorizar e gerir o Tracking de Alterações, consulte [o Rastreio de Gestão e Alteração de Monitor](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)

Para entender como consultar e trabalhar com os dados alterados, consulte [o Trabalho com Os Dados de Alteração](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)

## <a name="temporal-tables"></a>Tabelas Temporais

Além de suportar funcionalidades de captura de dados de Change Tracking e Change do SQL Server, o Azure SQL Edge também suporta a funcionalidade Tabelas Temporais do SqL Server. Tabelas temporais (também conhecidas como tabelas temporais versadas pelo sistema) como uma funcionalidade de base de dados que traz suporte incorporado para fornecer informações sobre dados armazenados na tabela em qualquer momento do que apenas os dados que estão corretos no momento atual.

Uma tabela temporal com a versão do sistema é um tipo de tabela de utilizadores projetada para manter um histórico completo de alterações de dados e permitir uma análise fácil do tempo. Este tipo de tabela temporal é referido como uma tabela temporal com versão do sistema porque o período de validade para cada linha é gerido pelo sistema (isto é, motor de base de dados).

Cada tabela temporal tem duas colunas explicitamente definidas, cada uma com um tipo de dado data 2. Estas colunas são referidas como colunas de época. Estas colunas de período são utilizadas exclusivamente pelo sistema para registar um período de validade para cada linha sempre que uma linha é modificada.

Além destas colunas de período, uma tabela temporal também contém uma referência a outra tabela com um esquema espelhado. O sistema utiliza esta tabela para armazenar automaticamente a versão anterior da linha cada vez que uma linha na tabela temporal é atualizada ou eliminada. Esta tabela adicional é referida como a tabela de história, enquanto a tabela principal que armazena versões de linha atuais (reais) é referida como a tabela atual ou simplesmente como a tabela temporal. Durante a criação de tabelas temporais, os utilizadores podem especificar a tabela de histórico existente (deve ser compatível com esquemas) ou deixar o sistema criar uma tabela de histórico padrão.

Para obter mais informações sobre tabelas temporais, consulte [as tabelas temporais](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)

## <a name="see-also"></a>Veja também

- [Streaming de dados em Azure SQL Edge (Pré-visualização)](stream-data.md)
- [Machine learning e IA com ONNX em Azure SQL Edge (Pré-visualização)](onnx-overview.md)
- [Configure a replicação para Azure SQL Edge (Pré-visualização)](configure-replication.md)
- [Bases de dados de backup e restauro em Azure SQL Edge (Pré-visualização)](backup-restore.md)



