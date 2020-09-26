---
title: O tamanho do trabalho excedeu o erro
description: Descreve como resolver erros quando o tamanho ou o modelo do trabalho são demasiado grandes.
ms.topic: troubleshooting
ms.date: 09/25/2020
ms.openlocfilehash: 06645561964d9634d93061b3be4d100a578cc7e7
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91373173"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Resolver erros para o tamanho do trabalho excedidos

Este artigo descreve como resolver os **erros de JobSizeException** e **DeploymentSizeExceededEdEdException.**

## <a name="symptom"></a>Sintoma

Ao implementar um modelo, recebe um erro indicando que a implementação excedeu os limites.

## <a name="cause"></a>Causa

Pode obter este erro quando o tamanho do seu modelo exceder 4 MB. O limite de 4-MB aplica-se ao estado final do modelo depois de ter sido expandido para definições de recursos que usam [cópia](copy-resources.md) para criar muitas instâncias. O estado final também inclui os valores resolvidos para variáveis e parâmetros.

O trabalho de implantação também inclui metadados sobre o pedido. Para modelos grandes, os metadados combinados com o modelo podem exceder o tamanho permitido para um trabalho.

Outros limites para o modelo são:

* 256 parâmetros
* 256 variáveis
* 800 recursos (incluindo a contagem de cópias)
* 64 valores de saída
* 24.576 caracteres em uma expressão de modelo

## <a name="solution-1---simplify-template"></a>Solução 1 - Modelo de simplificação

A sua primeira opção é simplificar o modelo. Esta opção funciona quando o seu modelo implementa vários tipos de recursos diferentes. Considere dividir o modelo em [modelos ligados.](linked-templates.md) Divida os seus tipos de recursos em grupos lógicos e adicione um modelo ligado para cada grupo. Por exemplo, se precisar de implementar muitos recursos de networking, pode mover esses recursos para um modelo ligado.

Pode definir outros recursos como dependentes do modelo ligado e [obter valores a partir da saída do modelo ligado](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---use-serial-copy"></a>Solução 2 - Utilize cópia em série

A segunda opção é alterar o seu ciclo de cópia de [processamento paralelo a série](copy-resources.md#serial-or-parallel). Utilize esta opção apenas quando suspeitar que o erro provém da implantação de um grande número de recursos através de cópia. Esta mudança pode aumentar significativamente o seu tempo de implantação porque os recursos não são implantados em paralelo.
