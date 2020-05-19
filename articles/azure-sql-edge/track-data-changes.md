---
title: Rastreio de alterações de dados em Azure SQL Edge (Pré-visualização)
description: Conheça o rastreio de alterações e alterar a captura de dados no Azure SQL Edge (Pré-visualização)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a91ed5d4763aa521c9f6ed913dc532b08e37039f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597269"
---
# <a name="tracking-data-changes-in-azure-sql-edge-preview"></a>Rastreio de alterações de dados em Borda SQL Azure (pré-visualização)

O Azure SQL Edge suporta as duas funcionalidades do Servidor SQL que acompanham as alterações aos dados numa base de dados:[Alterar o rastreio](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) e alterar a captura de [dados](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Estas funcionalidades permitem que as aplicações determinem as alterações do DML (inserir, atualizar e eliminar operações) que foram feitas às tabelas dos utilizadores numa base de dados. Alterar a captura de dados e alterar o rastreio pode ser ativado na mesma base de dados; não são necessárias considerações especiais.

A capacidade de consultar dados que mudaram numa base de dados é um requisito importante para que algumas aplicações sejam eficientes. Normalmente, para determinar as alterações de dados, os desenvolvedores de aplicações devem implementar um método de rastreio personalizado nas suas aplicações utilizando uma combinação de gatilhos, colunas de carimbos de tempo e tabelas adicionais. A criação destas aplicações envolve geralmente muito trabalho a implementar, leva a atualizações de esquemas, e muitas vezes carrega uma sobrecarga de alto desempenho. No caso da solução IoT, onde é necessário mover periodicamente os dados da borda para uma nuvem ou centro de dados, o rastreio de alterações pode ser muito útil. Isto permitiria que se consultasse rapidamente e eficazmente apenas as alterações delta a partir da última sincronização e carregaria essas alterações para o alvo da nuvem ou do centro de dados. Para mais detalhes sobre os benefícios de utilizar a captura de dados de rastreio de alterações e alterações, consulte [os benefícios de utilizar](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking)a captura de dados de alteração ou o rastreio de alterações . 

Para compreender as diferenças de funcionalidadeentre o rastreio de alterações e a captura de dados de alteração, consulte diferenças de [funcionalidadeentre a captura de dados](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking) de alteração e o rastreio de alterações

## <a name="change-data-capture"></a>Captura de Dados Alterados

Para compreender os detalhes de como a Change Data Capture funciona, consulte [a Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

Para entender como ativar ou desativar a captura de dados de alteração, consulte [a Captura de Dados de Ativação e Desativação](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)

Para administrar e monitorizar a captura de dados de alteração, consulte [a Administração e monitorize](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server) a captura de dados de alteração

Para entender como consultar e trabalhar com os dados alterados, consulte [Trabalhar com Dados](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server) de Alteração

## <a name="change-tracking"></a>Monitorização de Alterações

Para compreender os detalhes de como o Change Tracking funciona, consulte sobre o Rastreio de [Alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Para entender como ativar ou desativar o rastreio de alterações, consulte [o Rastreio de Alterações activae desativação](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)

Para administrar, monitorizar e gerir o Rastreio de Alterações, consulte [a Administração e o Monitor Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)

Para entender como consultar e trabalhar com os dados alterados, consulte [Trabalhar com Dados](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server) de Alteração

## <a name="temporal-tables"></a>Tabelas Temporais

Além de suportar funcionalidades de rastreio de mudança e de captura de dados de mudança do Servidor SQL, o Azure SQL Edge também suporta a funcionalidade tabelas temporais do Servidor SQL. As tabelas temporais (também conhecidas como tabelas temporais versárias do sistema) como uma característica de base de dados que traz suporte incorporado para fornecer informações sobre dados armazenados na tabela em qualquer momento e não apenas os dados que estão corretos no momento atual.

Uma tabela temporal versiva versionizada pelo sistema é um tipo de tabela de utilizadores projetada para manter um histórico completo de alterações de dados e permitir uma análise de tempo fácil. Este tipo de tabela temporal é referida como uma tabela temporal versítica do sistema porque o período de validade para cada linha é gerido pelo sistema (isto é, motor de base de dados).

Cada tabela temporal tem duas colunas explicitamente definidas, cada uma com um tipo de dados datetime2. Estas colunas são referidas como colunas de período. Estas colunas de período são utilizadas exclusivamente pelo sistema para registar o período de validade de cada linha sempre que uma linha é modificada.

Além destas colunas de período, uma tabela temporal também contém uma referência a outra tabela com um esquema espelhado. O sistema utiliza esta tabela para armazenar automaticamente a versão anterior da linha sempre que uma linha na tabela temporal é atualizada ou eliminada. Esta tabela adicional é referida como a tabela de história, enquanto a tabela principal que armazena versões atuais (reais) de linha é referida como a tabela atual ou simplesmente como a tabela temporal. Durante a criação de tabelatemporal, os utilizadores podem especificar a tabela de história existente (deve ser compatível com o esquema) ou deixar o sistema criar uma tabela de história padrão.

Para mais informações sobre tabelas temporais, consulte [as tabelas temporais](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)

## <a name="see-also"></a>Veja também

- [Streaming de dados em Borda SQL Azure (pré-visualização)](stream-data.md)
- [Machine learning e IA com ONNX em Borda SQL Azure (Pré-visualização)](onnx-overview.md)
- [Configure a replicação para Azure SQL Edge (Pré-visualização)](configure-replication.md)
- [Bases de dados de backup e restauro em Borda SQL (Pré-visualização)](backup-restore.md)



