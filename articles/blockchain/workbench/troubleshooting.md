---
title: Tiroteio na bancada azure Blockchain
description: Como resolver um problema com uma aplicação de pré-visualização da bancada de trabalho Azure Blockchain.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324305"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Azure Blockchain Workbench Preview resolução de problemas

Um script PowerShell está disponível para ajudar com depuração ou suporte do desenvolvedor. O script gera um resumo e recolhe registos detalhados para resolução de problemas. Os registos recolhidos incluem:

* Rede Blockchain, como ethereum
* Microserviços blockchain Workbench
* Application Insights
* Monitorização Azure (registos do Monitor Azure)

Pode utilizar a informação para determinar os próximos passos e determinar a causa principal dos problemas.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Roteiro de resolução de problemas

O script powerShell de resolução de problemas está disponível no GitHub. [Transfira um ficheiro zip](https://github.com/Azure-Samples/blockchain/archive/master.zip) ou clone o exemplo a partir do GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Executar o script
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Executar `collectBlockchainWorkbenchTroubleshooting.ps1` o script para recolher registos e criar um ficheiro ZIP contendo uma pasta de informações de resolução de problemas. Por exemplo:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
O guião aceita os seguintes parâmetros:

| Parâmetro  | Descrição | Necessário |
|---------|---------|----|
| Subscrição ID | Subscrição ID para criar ou localizar todos os recursos. | Sim |
| ResourceGroupName | Nome do Grupo de Recursos Azure onde a bancada blockchain workbench foi implantada. | Sim |
| Diretório de Saída | Caminho para criar a saída. Ficheiro ZIP. Se não especificado, predefinido para o diretório atual. | Não |
| LookbackHours | Número de horas para usar ao puxar a telemetria. O valor padrão é de 24 horas. Valor máximo é de 90 horas | Não |
| OmsSubscriptionId | O ID de subscrição onde os registos do Monitor Azure são implantados. Só passe este parâmetro se os registos do Monitor Azure para a rede blockchain forem implantados fora do grupo de recursos da Blockchain Workbench.| Não |
| OmsResourceGroup |O grupo de recursos onde os registos do Monitor Azure são implantados. Só passe este parâmetro se os registos do Monitor Azure para a rede blockchain forem implantados fora do grupo de recursos da Blockchain Workbench.| Não |
| OmsWorkspaceName | O nome do espaço de trabalho Log Analytics. Só passar este parâmetro se os registos do Monitor Azure para a rede blockchain forem implantados fora do grupo de recursos da Blockchain Workbench | Não |

## <a name="what-is-collected"></a>O que é recolhido?

O ficheiro ZIP de saída contém a seguinte estrutura de pasta:

| Pasta ou Arquivo | Descrição  |
|---------|---------|
| \Resumo.txt | Resumo do sistema |
| \Metrics\blockchain | Métricas sobre a blockchain |
| \Métricas\Bancada de trabalho | Métricas sobre a bancada |
| \Details\Blockchain | Registos detalhados sobre a blockchain |
| \Detalhes\Bancada de trabalho | Registos detalhados sobre a bancada |

O ficheiro resumo dá-lhe uma imagem do estado geral da aplicação e da saúde da aplicação. O resumo fornece ações recomendadas, destaca erros de topo e metadados sobre serviços de execução.

A pasta **Metrics** contém métricas de vários componentes do sistema ao longo do tempo. Por exemplo, o `\Details\Workbench\apiMetrics.txt` ficheiro de saída contém um resumo de diferentes códigos de resposta e tempos de resposta durante todo o período de recolha. A pasta **Details** contém registos detalhados para resolver problemas específicos com a Workbench ou a rede blockchain subjacente. Por exemplo, `\Details\Workbench\Exceptions.csv` contém uma lista das mais recentes exceções que ocorreram no sistema, o que é útil para erros de resolução de problemas com contratos inteligentes ou interações com a blockchain. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Guia de aplicação de aplicativos azure Blockchain Workbench Insights](https://aka.ms/workbenchtroubleshooting)
