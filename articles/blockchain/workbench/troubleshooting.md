---
title: Azure Blockchain Workbench resolução de problemas
description: Como resolver problemas com uma aplicação de pré-visualização da bancada de trabalho Azure Blockchain.
ms.date: 10/14/2019
ms.topic: troubleshooting
ms.reviewer: brendal
ms.openlocfilehash: 20c0f9bdd6f820a73b1ba6660de805268c0d8714
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85212858"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Azure Blockchain Workbench Pré-visualização resolução de problemas

Um script PowerShell está disponível para ajudar na depuragem ou suporte do desenvolvedor. O script gera um resumo e recolhe registos detalhados para resolução de problemas. Os registos recolhidos incluem:

* Rede Blockchain, como ethereum
* Microserviços blockchain Workbench
* Application Insights
* Monitorização do Azure (registos do Monitor Azure)

Pode utilizar as informações para determinar os próximos passos e determinar a causa principal dos problemas.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Roteiro de resolução de problemas

O script de resolução de problemas powerShell está disponível no GitHub. [Transfira um ficheiro zip](https://github.com/Azure-Samples/blockchain/archive/master.zip) ou clone o exemplo a partir do GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Executar o script
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Execute o `collectBlockchainWorkbenchTroubleshooting.ps1` script para recolher registos e crie um ficheiro ZIP contendo uma pasta de informações de resolução de problemas. Por exemplo:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
O script aceita os seguintes parâmetros:

| Parâmetro  | Descrição | Obrigatório |
|---------|---------|----|
| SubscriçãoID | SubscriçãoID para criar ou localizar todos os recursos. | Sim |
| ResourceGroupName | Nome do Grupo de Recursos Azure onde a blockchain Workbench foi implantada. | Sim |
| OutputDirectory | Caminho para criar a saída. Ficheiro ZIP. Se não for especificado, predefinições no diretório atual. | Não |
| LookbackHours | Número de horas para utilizar ao puxar a telemetria. O valor predefinido é de 24 horas. O valor máximo é de 90 horas | Não |
| OmsSubscriptionId | O ID de subscrição onde os registos do Azure Monitor são implantados. Só passe este parâmetro se os registos do Monitor Azure para a rede blockchain forem implantados fora do grupo de recursos da Blockchain Workbench.| Não |
| OmsResourceGroup |O grupo de recursos onde os registos do Azure Monitor são implantados. Só passe este parâmetro se os registos do Monitor Azure para a rede blockchain forem implantados fora do grupo de recursos da Blockchain Workbench.| Não |
| Nome OmsWorkspace | O nome do espaço de trabalho Log Analytics. Só passe este parâmetro se os registos do Monitor Azure para a rede blockchain forem implantados fora do grupo de recursos da Blockchain Workbench | Não |

## <a name="what-is-collected"></a>O que é recolhido?

O ficheiro ZIP de saída contém a seguinte estrutura de pasta:

| Pasta ou Arquivo | Descrição  |
|---------|---------|
| \Summary.txt | Resumo do sistema |
| \Métricas\blockchain | Métricas sobre a blockchain |
| \Métricas\Workbench | Métricas sobre a bancada de trabalho |
| \Detalhes\Blockchain | Registos detalhados sobre a blockchain |
| \Detalhes\Workbench | Registos detalhados sobre a bancada de trabalho |

O ficheiro sumário dá-lhe uma imagem do estado geral da aplicação e saúde do pedido. O resumo fornece ações recomendadas, destaca erros de topo e metadados sobre a execução de serviços.

A pasta **Métricas** contém métricas de vários componentes do sistema ao longo do tempo. Por exemplo, o ficheiro de saída `\Details\Workbench\apiMetrics.txt` contém um resumo de diferentes códigos de resposta e tempos de resposta ao longo do período de recolha. A pasta **Details** contém registos detalhados para resolver problemas específicos com a Workbench ou a rede blockchain subjacente. Por exemplo, `\Details\Workbench\Exceptions.csv` contém uma lista das mais recentes exceções que ocorreram no sistema, o que é útil para resolver erros com contratos inteligentes ou interações com a blockchain. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Guia de resolução de problemas da Azure Blockchain Workbench Insights](https://aka.ms/workbenchtroubleshooting)
