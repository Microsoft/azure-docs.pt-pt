---
title: WebJob, tarefas de fundo, em Azure
description: Saiba mais sobre webJobs.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1cb5e99558d6bf1a5baa21d05d45415855c61cb5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746698"
---
# <a name="webjobs-run-background-tasks-in-azure-app-service"></a>WebJobs executam tarefas de fundo no Azure App Service

Este artigo mostra como implementar WebJobs utilizando o [portal Azure](https://portal.azure.com) para carregar um executável ou script. Para obter informações sobre como desenvolver e implementar WebJobs utilizando o Visual Studio, consulte [Implementar WebJobs utilizando o Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Descrição Geral
WebJobs é uma funcionalidade do [Azure App Service](index.yml) que lhe permite executar um programa ou script no mesmo caso que uma aplicação web, app API ou aplicativo móvel. Não há custo adicional para usar WebJobs.

> [!IMPORTANT]
> O WebJobs ainda não está suportado para o Serviço de Aplicações em Linux.

O Azure WebJobs SDK pode ser usado com WebJobs para simplificar muitas tarefas de programação. Para mais informações, consulte [o que é o WebJobs SDK.](https://github.com/Azure/azure-webjobs-sdk/wiki)

As Funções Azure fornecem outra forma de executar programas e scripts. Para uma comparação entre WebJobs e Funções, consulte [Escolha entre Fluxo, Aplicações Lógicas, Funções e WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Tipos webJob

A tabela seguinte descreve as diferenças entre WebJobs *contínuos* e *desencadeados.*


|Contínuo  |Desencadeado  |
|---------|---------|
| Começa imediatamente quando o WebJob é criado. Para evitar que o trabalho termine, o programa ou o script normalmente faz o seu trabalho dentro de um ciclo interminável. Se o trabalho acabar, pode reiniciá-lo. | Só começa quando acionado manualmente ou com um horário. |
| Executa em todos os casos que a aplicação web executa. Pode, opcionalmente, restringir o WebJob a um único caso. |Executa num único caso que o Azure seleciona para o equilíbrio de carga.|
| Suporta depurações remotas. | Não suporta depurar remotamente.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="add-webjob-to-source-control"></a>Adicione WebJob ao controlo de origem

Se tiver o controlo de origem configurado com a sua aplicação, os Trabalhos Web devem ser implantados como parte da integração do controlo de origem. Uma vez configurado o controlo de origem com a sua aplicação, um WebJob não pode ser adicionado a partir do Portal Azure.

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Tipos de ficheiros suportados para scripts ou programas

São suportados os seguintes tipos de ficheiro:

* .cmd, .bat, .exe (usando o Windows cmd)
* .ps1 (usando PowerShell)
* .sh (usando Bash)
* .php (utilizando PHP)
* .py (usando Python)
* .js (utilizando Node.js)
* .jar (usando Java)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar um WebJob](./webjobs-create-ieux.md)
* Ver histórico de registos do WebJobs[./webjobs-create-ieux-view-log.md)