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
ms.openlocfilehash: 2823a33b25812a69ad463433bacd9710655c9176
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67184666"
---
#### <a name="process-automation"></a>Automatização de processos

| Resource | Limite máximo |Notas|
| --- | --- |---|
| Número máximo de novos trabalhos que podem ser enviados a cada 30 segundos por conta de automação do Azure (trabalhos não agendados) |100 |Quando esse limite é atingido, as solicitações subsequentes para criar um trabalho falham. O cliente recebe uma resposta de erro.|
| Número máximo de trabalhos em execução simultâneos na mesma instância de tempo por conta de automação (trabalhos não agendados) |200 |Quando esse limite é atingido, as solicitações subsequentes para criar um trabalho falham. O cliente recebe uma resposta de erro.|
| Tamanho máximo de armazenamento dos metadados de trabalho para um período sem interrupção de 30 dias | 10 GB (aproximadamente 4 milhões trabalhos)|Quando esse limite é atingido, as solicitações subsequentes para criar um trabalho falham. |
| Limite máximo de fluxo de trabalho|1 MB|Um único fluxo não pode ser maior que 1 MB.|
| Número máximo de módulos que podem ser importados a cada 30 segundos por conta de automação |5 ||
| Tamanho máximo de um módulo |100 MB ||
| Tempo de execução do trabalho, camada gratuita |500 minutos por subscrição por mês ||
| Quantidade máxima de espaço em disco permitida por sandbox<sup>1</sup> |1 GB |Aplica-se somente a áreas restritas do Azure.|
| Quantidade máxima de memória fornecida para uma área restrita<sup>1</sup> |400 MB |Aplica-se somente a áreas restritas do Azure.|
| Número máximo de soquetes de rede permitidos por sandbox<sup>1</sup> |1,000 |Aplica-se somente a áreas restritas do Azure.|
| Tempo de execução máximo permitido por runbook<sup>1</sup> |3 horas |Aplica-se somente a áreas restritas do Azure.|
| Número máximo de contas de automação em uma assinatura |Sem limite ||
| Número máximo de grupos de Hybrid Worker por conta de automação|4,000||
|Número máximo de trabalhos simultâneos que podem ser executados em um único Hybrid Runbook Worker|50 ||
| Tamanho máximo do parâmetro de trabalho do runbook   | 512 kilobits||
| Parâmetros de runbook máximos   | 50|Se você alcançar o limite de 50 parâmetros, poderá passar uma cadeia de caracteres JSON ou XML para um parâmetro e analisá-lo com o runbook.|
| Tamanho máximo do conteúdo do webhook |  512 kilobits|
| Máximo de dias que os dados de trabalho são retidos|30 dias|
| Tamanho máximo do estado do fluxo de trabalho do PowerShell |5 MB| Aplica-se a runbooks de fluxo de trabalho do PowerShell ao aplicar o fluxo de trabalho.|

<sup>1</sup> Uma área restrita é um ambiente compartilhado que pode ser usado por vários trabalhos. Os trabalhos que usam a mesma área restrita são associados às limitações de recurso da área restrita.

#### <a name="change-tracking-and-inventory"></a>Alterar Controlo e Inventário

A tabela a seguir mostra os limites de item acompanhados por computador para o controle de alterações.

| **Recurso** | **Limite**| **Notas** |
|---|---|---|
|Ficheiro|500||
|Registo|250||
|Software do Windows|250|Não inclui atualizações de software.|
|Pacotes do Linux|1\.250||
|Serviços|250||
|Daemon|250||

#### <a name="update-management"></a>Gestão de Atualizações

A tabela a seguir mostra os limites para Gerenciamento de Atualizações.

| **Recurso** | **Limite**| **Notas** |
|---|---|---|
|Número de máquinas por implantação de atualização|1000||