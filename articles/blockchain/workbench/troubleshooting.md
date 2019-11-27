---
title: Solução de problemas do Azure Blockchain Workbench
description: Como solucionar problemas de um aplicativo de visualização do Azure Blockchain Workbench.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324305"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Solução de problemas de visualização do Azure Blockchain Workbench

Um script do PowerShell está disponível para auxiliar na depuração ou suporte do desenvolvedor. O script gera um resumo e coleta logs detalhados para solução de problemas. Os logs coletados incluem:

* Blockchain rede, como Ethereum
* Microserviços do Blockchain Workbench
* Application Insights
* Monitoramento do Azure (logs de Azure Monitor)

Você pode usar as informações para determinar as próximas etapas e determinar a causa raiz dos problemas.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Script de solução de problemas

O script de solução de problemas do PowerShell está disponível no GitHub. [Transfira um ficheiro zip](https://github.com/Azure-Samples/blockchain/archive/master.zip) ou clone o exemplo a partir do GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Executar o script
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Execute o script `collectBlockchainWorkbenchTroubleshooting.ps1` para coletar logs e criar um arquivo ZIP contendo uma pasta de informações de solução de problemas. Por exemplo:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
O script aceita os seguintes parâmetros:

| Parâmetro  | Descrição | Necessário |
|---------|---------|----|
| SubscriptionID | SubscriptionId para criar ou localizar todos os recursos. | Sim |
| ResourceGroupName | Nome do grupo de recursos do Azure em que o Blockchain Workbench foi implantado. | Sim |
| OutputDirectory | Caminho para criar a saída. Arquivo ZIP. Se não for especificado, o padrão será o diretório atual. | Não |
| LookbackHours | Número de horas a serem usadas ao obter telemetria. O valor padrão é 24 horas. O valor máximo é de 90 horas | Não |
| OmsSubscriptionId | A ID da assinatura na qual os logs do Azure Monitor são implantados. Passe esse parâmetro somente se os logs de Azure Monitor para a rede blockchain forem implantados fora do grupo de recursos do Blockchain Workbench.| Não |
| OmsResourceGroup |O grupo de recursos em que os logs do Azure Monitor são implantados. Passe esse parâmetro somente se os logs de Azure Monitor para a rede blockchain forem implantados fora do grupo de recursos do Blockchain Workbench.| Não |
| OmsWorkspaceName | O nome do espaço de trabalho Log Analytics. Passe esse parâmetro somente se os logs de Azure Monitor para a rede blockchain forem implantados fora do grupo de recursos do Blockchain Workbench | Não |

## <a name="what-is-collected"></a>O que é coletado?

O arquivo ZIP de saída contém a seguinte estrutura de pastas:

| Pasta ou arquivo | Descrição  |
|---------|---------|
| \Summary.txt | Resumo do sistema |
| \Metrics\blockchain | Métricas sobre o blockchain |
| \Metrics\Workbench | Métricas sobre o Workbench |
| \Details\Blockchain | Logs detalhados sobre o blockchain |
| \Details\Workbench | Logs detalhados sobre o Workbench |

O arquivo de resumo fornece um instantâneo do estado geral do aplicativo e da integridade do aplicativo. O resumo fornece as ações recomendadas, realça os principais erros e os metadados sobre a execução de serviços.

A pasta de **métricas** contém métricas de vários componentes do sistema ao longo do tempo. Por exemplo, o arquivo de saída `\Details\Workbench\apiMetrics.txt` contém um resumo de códigos de resposta diferentes e tempos de resposta ao longo do período de coleta. A pasta **detalhes** contém logs detalhados para solucionar problemas específicos com o Workbench ou a rede blockchain subjacente. Por exemplo, `\Details\Workbench\Exceptions.csv` contém uma lista das exceções mais recentes que ocorreram no sistema, o que é útil para solucionar erros com contratos inteligentes ou interações com o blockchain. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Guia de solução de problemas Application Insights do Azure Blockchain Workbench](https://aka.ms/workbenchtroubleshooting)
