---
title: Resolução de problemas Synapse Studio (pré-visualização) conectividade com PowerShell
description: Resolução de problemas Azure Synapse Studio conectividade usando PowerShell
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 7cf9f18701317482284a3f1fa4b69a2c2927f64a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91287717"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>Diagnosticar problemas de conectividade do Azure Synapse Studio (pré-visualização) com script PowerShell

O Azure Synapse Studio (pré-visualização) depende de um conjunto de pontos finais da Web API para funcionar corretamente. Este guia irá ajudá-lo a identificar as causas dos problemas de conectividade quando:
- configurar a sua rede local (como a rede por trás de uma firewall corporativa) para aceder ao Azure Synapse Studio.
- experimentando problemas de conectividade usando Azure Synapse Studio.

## <a name="prerequisite"></a>Pré-requisito

* Versão PowerShell 5.0 ou superior no Windows, ou
* Versão PowerShell Core 6.0 ou superior no Windows ou Linux.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Clique com o botão direito no seguinte link e selecione "Guardar o alvo como":

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Em alternativa, pode abrir o link diretamente e guardar o ficheiro de script aberto. Não guarde o endereço do link acima, pois pode mudar no futuro.

No explorador de ficheiros, clique com o botão direito no ficheiro de script descarregado e selecione "Run with PowerShell".

![Executar ficheiro de script descarregado com PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Quando solicitado, insira o nome do espaço de trabalho Azure Synapse que está atualmente a passar por um problema ou que pretende testar para conectividade e prima a entrada.

![Insira o nome do espaço de trabalho](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

A sessão de diagnóstico será iniciada. Espere que esteja completo.

![Aguarde que o diagnóstico esteja concluído](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

No final, será mostrado um resumo do diagnóstico. Se o seu PC não conseguir ligar-se a um ou mais pontos finais, apresentará algumas sugestões na secção "Resumo".

![Rever resumo de diagnóstico](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Além disso, um ficheiro de registo de diagnóstico para esta sessão será gerado na mesma pasta que o script de resolução de problemas. A sua localização é mostrada na secção "Dicas Gerais" `D:\TestAzureSynapse_2020....log` (). Pode enviar este ficheiro para suporte técnico, se necessário.

Se for administrador de rede e afinar a configuração de firewall para o Azure Synapse Studio, os detalhes técnicos apresentados acima da secção "Resumo" podem ajudar.

* Todos os itens de teste (pedidos) marcados com "Passado" significam que passaram nos testes de conectividade, independentemente do código de estado HTTP.
 Para os pedidos falhados, a razão é mostrada em amarelo, como `NamedResolutionFailure` ou `ConnectFailure` . Estas razões podem ajudá-lo a descobrir se existem configurações erradas com o ambiente da sua rede.


## <a name="next-steps"></a>Passos seguintes
Se os passos anteriores não ajudarem a resolver o seu problema, [crie um bilhete de apoio](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
