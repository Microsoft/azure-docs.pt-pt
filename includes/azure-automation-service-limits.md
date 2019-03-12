---
title: incluir ficheiro
description: incluir ficheiro
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: 61f82771e53ac9bb594484b29bb109a03cee674b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554203"
---
#### <a name="process-automation"></a>Automatização de processos

| Recurso | Limite máximo |Notas|
| --- | --- |---|
| Número máximo de novas tarefas que podem ser submetidos a cada 30 segundos por conta de automatização do Azure (nonscheduled tarefas) |100 |Quando este limite for atingido, os pedidos subsequentes para criar uma tarefa falharem. O cliente recebe uma resposta de erro.|
| Número máximo de tarefas em execução em simultâneo na mesma instância de tempo por conta de automatização (nonscheduled tarefas) |200 |Quando este limite for atingido, os pedidos subsequentes para criar uma tarefa falharem. O cliente recebe uma resposta de erro.|
| Tamanho máximo de armazenamento de metadados da tarefa durante um período sem interrupção de 30 dias | 10 GB (cerca de 4 milhões de tarefas)|Quando este limite for atingido, os pedidos subsequentes para criar uma tarefa falharem. |
| Número máximo de módulos que podem ser importados a cada 30 segundos por conta de automatização |5 ||
| Tamanho máximo de um módulo |100 MB ||
| Tarefa de tempo de execução, escalão gratuito |500 minutos por subscrição por mês ||
| Quantidade máxima de espaço em disco permitida por sandbox<sup>1</sup> |1 GB |Aplica-se apenas uma áreas de segurança do Azure.|
| Quantidade máxima de memória para uma área de segurança<sup>1</sup> |400 MB |Aplica-se apenas uma áreas de segurança do Azure.|
| Número máximo de soquetes de rede, permitidos por sandbox<sup>1</sup> |1,000 |Aplica-se apenas uma áreas de segurança do Azure.|
| Tempo de execução máximo permitido por runbook<sup>1</sup> |3 horas |Aplica-se apenas uma áreas de segurança do Azure.|
| Número máximo de contas de automatização numa subscrição |Sem limite ||
|Número máximo de tarefas simultâneas que podem ser executadas num único Runbook Worker híbrido|50 ||
| Tamanho do parâmetro de tarefa de runbook máximo   | 512 quilobits||
| Parâmetros do runbook máximo   | 50|Se atingir o limite de parâmetro de 50, pode passar uma cadeia de caracteres do JSON ou XML para um parâmetro e analisá-lo com o runbook.|
| Tamanho da carga máxima de webhook |  512 quilobits|
| Máximo de dias que os dados da tarefa são retidos|30 dias|
| Tamanho máximo do Estado de fluxo de trabalho do PowerShell |5 MB| Aplica-se a runbooks do fluxo de trabalho do PowerShell ao fluxo de trabalho do ponto de verificação.|

<sup>1</sup>uma área de segurança é um ambiente compartilhado que pode ser utilizado por várias tarefas. Tarefas que utilizam a mesma área de segurança são ligadas pelas limitações de recursos da área de segurança.

#### <a name="change-tracking-and-inventory"></a>Controlo de alterações e inventário

A tabela seguinte mostra os limites de item controladas por máquina para o controlo de alterações.

| **Recurso** | **Limite**| **Notas** |
|---|---|---|
|Ficheiro|500||
|Registo|250||
|Software do Windows|250|Não inclui as atualizações de software.|
|Pacotes de Linux|1,250||
|Serviços|250||
|Daemon|250||
