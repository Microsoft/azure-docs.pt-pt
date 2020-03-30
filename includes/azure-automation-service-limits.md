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
ms.openlocfilehash: d700dfcf5a7b6e9ada2a755335689ffa571e4c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334804"
---
#### <a name="process-automation"></a>Automatização de processos

| Recurso | Limite |Notas|
| --- | --- |---|
| Número máximo de novos postos de trabalho que podem ser submetidos a cada 30 segundos por conta Azure Automation (empregos não programados) |100 |Quando este limite é atingido, os pedidos subsequentes para criar um emprego falham. O cliente recebe uma resposta de erro.|
| Número máximo de postos de trabalho correntes simultâneos no mesmo período de tempo por conta automation (empregos não programados) |200 |Quando este limite é atingido, os pedidos subsequentes para criar um emprego falham. O cliente recebe uma resposta de erro.|
| Tamanho máximo de armazenamento de metadados de trabalho para um período de 30 dias de rolamento | 10 GB (aproximadamente 4 milhões de empregos)|Quando este limite é atingido, os pedidos subsequentes para criar um emprego falham. |
| Limite máximo de fluxo de emprego|1 MB|Um único fluxo não pode ser maior que 1 MB.|
| Número máximo de módulos que podem ser importados a cada 30 segundos por conta de Automação |5 ||
| Tamanho máximo de um módulo |100 MB ||
| Tempo de execução de emprego, nível livre |500 minutos por subscrição por mês ||
| Quantidade máxima de espaço em disco permitido por caixa de areia<sup>1</sup> |1 GB |Aplica-se apenas às caixas de areia Azure.|
| Quantidade máxima de memória dada a uma caixa de areia<sup>1</sup> |400 MB |Aplica-se apenas às caixas de areia Azure.|
| Número máximo de tomadas de rede permitidas por caixa de areia<sup>1</sup> |1,000 |Aplica-se apenas às caixas de areia Azure.|
| Tempo máximo de execução permitido por livro<sup>de corridas 1</sup> |3 horas |Aplica-se apenas às caixas de areia Azure.|
| Número máximo de contas de Automação numa subscrição |Sem limite ||
| Número máximo de Grupos de Trabalhadores Híbridos por Conta de Automação|4000||
|Número máximo de empregos simultâneos que podem ser executados num único Trabalhador híbrido|50 ||
| Tamanho máximo do parâmetro de trabalho do livro de corridas   | 512 quilobits||
| Parâmetros máximos de escadirinco   | 50|Se atingir o limite de 50 parâmetros, pode passar uma corda JSON ou XML a um parâmetro e analisá-la com o livro de execução.|
| Tamanho máximo da carga útil do webhook |  512 quilobits|
| Máximo sem dias que os dados do trabalho são retidos|30 dias|
| Tamanho máximo do estado do fluxo de trabalho PowerShell |5 MB| Aplica-se aos livros de fluxo de trabalho powerShell ao checkpoint do fluxo de trabalho.|

<sup>1</sup> Uma caixa de areia é um ambiente partilhado que pode ser usado por vários trabalhos. Os empregos que utilizam a mesma caixa de areia estão ligados às limitações de recursos da caixa de areia.

#### <a name="change-tracking-and-inventory"></a>Controlo de Alterações e Inventário

A tabela seguinte mostra os limites de item rastreados por máquina para o rastreio de alterações.

| **Recurso** | **Limite**| **Notas** |
|---|---|---|
|Ficheiro|500||
|Registo|250||
|Software windows|250|Não inclui atualizações de software.|
|Pacotes Linux|1,250||
|Serviços|250||
|Daemon|250||

#### <a name="update-management"></a>Gestão de Atualizações

O quadro seguinte mostra os limites para a Gestão de Atualizações.

| **Recurso** | **Limite**| **Notas** |
|---|---|---|
|Número de máquinas por implementação de atualização|1000||