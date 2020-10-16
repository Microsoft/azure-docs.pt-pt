---
title: Visão geral da política de retenção de dados - Azure SQL Edge
description: Conheça a política de retenção de dados no Azure SQL Edge
keywords: SQL Edge, retenção de dados
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: bb059a946c03f41e5b65944eec67070f84ee6b08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90976338"
---
# <a name="data-retention-overview"></a>Descrição geral da retenção de dados

A recolha e armazenamento de dados de dispositivos IoT conectados é importante para impulsionar e obter insights operacionais e empresariais. No entanto, dado o volume de dados originários destes dispositivos, torna-se importante que as organizações planeiem cuidadosamente a quantidade de dados que pretendem reter e em que granularidade. Embora reter todos os dados em toda a granularidade é desejável, nem sempre é prático. Além disso, o volume de dados que podem ser retidos está limitado pela quantidade de armazenamento disponível nos dispositivos IoT ou Edge. 

Na base de dados Azure SQL Edge os administradores podem definir a política de retenção de dados numa base de dados SQL Edge e nas suas tabelas subjacentes. Uma vez definida a política de retenção de dados, uma tarefa do sistema de fundo será executada para expurgar quaisquer dados obsoletos (antigos) das tabelas de utilizadores. 

> [!Note]
> Dados uma vez purgados da mesa, não são recuperáveis. A única forma possível de recuperar os dados purgados é restaurar a base de dados a partir de uma cópia de segurança mais antiga.

Guias de Início Rápido:

- [Permitir e desativar políticas de retenção de dados](data-retention-enable-disable.md)
- [Gerir dados históricos com política de retenção](data-retention-cleanup.md)

## <a name="how-data-retention-works"></a>Como funciona a retenção de dados

Para configurar a retenção de dados, pode utilizar declarações DDL. Para obter mais informações, [Ative e desativar as políticas de retenção de dados](data-retention-enable-disable.md). Para a eliminação automática dos registos obsoletos, a retenção de dados deve primeiro ser ativada tanto para a base de dados como para as tabelas que pretende ser purgada nessa base de dados. 

Após a conservação de dados ser configurada para uma tabela, uma tarefa de fundo executa para identificar os registos obsoletos numa tabela e eliminar esses registos. Se, por alguma razão, a limpeza automática das tarefas não estiver em funcionamento ou não for capaz de acompanhar as eliminações, poderá ser efetuada uma operação de limpeza manual nestas tabelas. Para obter mais informações sobre limpezas automáticas e manuais, consulte [a Limpeza Automática e Manual.](data-retention-cleanup.md)

## <a name="limitations-and-restrictions"></a>Limitações e restrições

- A conservação de dados, se ativada, é automaticamente desativada quando a base de dados é restaurada a partir de uma cópia de segurança completa ou é recolocada. 
- A conservação de dados não pode ser ativada para uma Tabela de História Temporal
- O colomn do filtro de retenção de dados não pode ser alterado. Para alterar a coluna, desative a retenção de dados na tabela.  

## <a name="next-steps"></a>Passos Seguintes

- [Machine Learning e Inteligência Artificial com ONNX em SQL Edge](onnx-overview.md).
- [Construção de uma solução IoT de ponta a ponta com aresta SQL utilizando IoT Edge](tutorial-deploy-azure-resources.md).
- [Streaming de dados em Azure SQL Edge](stream-data.md)
