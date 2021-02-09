---
title: incluir ficheiro
description: incluir ficheiro
services: automation
author: mgoedtel
ms.service: automation
ms.topic: include
ms.date: 02/08/2021
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 3ee44509997a16fc7f06fd5a24e473d7011bde40
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99974655"
---
#### <a name="process-automation"></a>Automatização de processos

| Recurso | Limite |Notas|
| --- | --- |---|
| Número máximo de novos postos de trabalho que podem ser submetidos a cada 30 segundos por conta da Azure Automation (empregos não programados) |100 |Quando este limite é atingido, os pedidos subsequentes para criar um emprego falham. O cliente recebe uma resposta de erro.|
| Número máximo de postos de trabalho em funcionamento simultâneos no mesmo caso de tempo por conta de Automação (empregos não programados) |200 |Quando este limite é atingido, os pedidos subsequentes para criar um emprego falham. O cliente recebe uma resposta de erro.|
| Tamanho máximo de armazenamento de metadados de trabalho para um período de 30 dias de rolamento | 10 GB (aproximadamente 4 milhões de empregos)|Quando este limite é atingido, os pedidos subsequentes para criar um emprego falham. |
| Limite máximo de fluxo de trabalho|1 MiB|Um único fluxo não pode ser maior que 1 MiB.|
| Número máximo de módulos que podem ser importados a cada 30 segundos por conta de Automação |5 ||
| Tamanho máximo de um módulo |100 MB ||
| Tamanho máximo de um ficheiro de configuração de nó | 1 MB | Aplica-se à configuração do estado |
| Tempo de execução de trabalho, nível gratuito |500 minutos por subscrição por mês ||
| Quantidade máxima de espaço em disco permitido por caixa de areia<sup>1</sup> |1 GB |Aplica-se apenas a caixas de areia Azure.|
| Quantidade máxima de memória dada a uma caixa de areia<sup>1</sup> |400 MB |Aplica-se apenas a caixas de areia Azure.|
| Número máximo de tomadas de rede permitidas por caixa de areia<sup>1</sup> |1,000 |Aplica-se apenas a caixas de areia Azure.|
| Tempo máximo permitido por runbook<sup>1</sup> |3 horas |Aplica-se apenas a caixas de areia Azure.|
| Número máximo de contas de Automação numa subscrição |Sem limite ||
| Número máximo de grupos híbridos de trabalhadores por conta de automação|4000||
|Número máximo de empregos simultâneos que podem ser executados num único Trabalhador De Runbook Híbrido|50 ||
| Tamanho máximo do parâmetro de trabalho do runbook   | 512 quilobytes||
| Parâmetros máximos do runbook   | 50|Se atingir o limite de 50 parâmetros, pode passar uma corda JSON ou XML para um parâmetro e analisá-la com o livro de bordo.|
| Tamanho máximo da carga útil webhook |  512 quilobytes|
| Dias máximos para que os dados do emprego são mantidos|30 dias|
| Tamanho máximo do estado do fluxo de trabalho PowerShell |5 MB| Aplica-se aos livros de fluxo de trabalho PowerShell ao checkpoint o fluxo de trabalho.|

<sup>1</sup> Uma caixa de areia é um ambiente partilhado que pode ser usado por vários trabalhos. Os postos de trabalho que utilizam a mesma caixa de areia estão vinculados às limitações de recursos da caixa de areia.

#### <a name="change-tracking-and-inventory"></a>Controlo de Alterações e Inventário

A tabela a seguir mostra os limites de item rastreados por máquina para o rastreio de alterações.

| **Recurso** | **Limite**| **Notas** |
|---|---|---|
|Ficheiro|500||
|Registo|250||
|Software Windows|250|Não inclui atualizações de software.|
|Pacotes Linux|1,250||
|Serviços|250||
|Rio Daemon|250||

#### <a name="update-management"></a>Gestão de Atualizações

A tabela a seguir mostra os limites para a Gestão de Atualização.

| **Recurso** | **Limite**| **Notas** |
|---|---|---|
|Número de máquinas por atualização|1000||