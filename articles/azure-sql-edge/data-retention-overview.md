---
title: Visão geral da política de retenção de dados - Azure SQL Edge (Pré-visualização)
description: Conheça a política de retenção de dados em Azure SQL Edge (Preview)
keywords: SQL Edge, retenção de dados
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 3649d4f77e5b57ab14accacd87fbaa867ba2742f
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89550722"
---
# <a name="data-retention-policy-overview"></a>Visão geral da política de retenção de dados

A recolha e armazenamento de dados de dispositivos IoT conectados é importante para impulsionar e obter insights operacionais e empresariais. No entanto, dado o volume de dados originários destes dispositivos, torna-se importante que as organizações planeiem cuidadosamente a quantidade de dados que pretendem reter e em que granularidade. Embora reter todos os dados em toda a granularidade é desejável, nem sempre é prático. Além disso, o volume de dados que podem ser retidos está limitado pela quantidade de armazenamento disponível nos dispositivos IoT ou Edge. 

O Azure SQL Edge (Preview) CTP2.3 adiciona uma nova capacidade que permite aos administradores de bases de dados definir a política de retenção de dados numa base de dados SQL Edge e nas suas tabelas subjacentes. Uma vez definida a política de retenção de dados, uma tarefa do sistema de fundo será executada para expurgar quaisquer dados obsoletos (antigos) das tabelas de utilizadores. 

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

## <a name="next-steps"></a>Passos Seguintes

- [Machine Learning e Inteligência Artificial com ONNX em SQL Edge](onnx-overview.md).
- [Construção de uma solução IoT de ponta a ponta com aresta SQL utilizando IoT Edge](tutorial-deploy-azure-resources.md).
- [Streaming de dados em Azure SQL Edge](stream-data.md)
