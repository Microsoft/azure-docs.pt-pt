---
title: Integração com políticas azure
description: A Azure Policy é um serviço em Azure que utiliza para criar, atribuir e gerir políticas que apliquem regras sobre os seus recursos.
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: e576c99e918410cb63743aaca32bdef78ae01d83
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726660"
---
# <a name="integration-with-azure-policy"></a>Integração com a Política Azure

A Azure Policy é um serviço em Azure que utiliza para criar, atribuir e gerir políticas que apliquem regras sobre os seus recursos para garantir que esses recursos se mantenham em conformidade com os seus padrões corporativos e acordos de nível de serviço. A Política Azure avalia os seus recursos por incumprimento das políticas que atribui. 

O Azure Batch tem duas extensões incorporadas para ajudá-lo a gerir o cumprimento da política. 

|**Nome...**|   **Descrição**|**Efeitos(s)**|  **Versão**|    **Origem**
|----------------|----------|----------|----------------|---------------|
|Os registos de diagnóstico nas contas do Lote devem ser ativados|   Auditoria que permite registos de diagnóstico. Isto permite-lhe recriar trilhas de atividade para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida|AuditoriaIfNotExists, Deficiente|  2.0.0|  GitHub|
|As regras de alerta métrico devem ser configuradas nas contas do Lote| Configuração de auditoria das regras de alerta métrico na conta do Lote para ativar a métrica necessária|   AuditoriaIfNotExists, Deficiente| 1.0.0|  GitHub|

As definições políticas descrevem as condições que devem ser satisfeitas. Uma condição compara a propriedade do recurso a um valor exigido. Os campos de propriedade de recursos são acedidos usando pseudónimos pré-definidos. Você usa pseudónimos de propriedade para aceder a propriedades específicas para um tipo de recurso. Os pseudónimos permitem-lhe restringir quais os valores ou condições permitidos para uma propriedade num recurso. Cada pseudónimo mapeia caminhos em diferentes versões API para um determinado tipo de recursos. Durante a avaliação política, o motor de política obtém o caminho da propriedade para a versão API.

Os recursos exigidos pelo Batch incluem: conta, nó de cálculo, piscina, trabalho e tarefa. Assim, você usaria pseudónimos de propriedade para aceder a propriedades específicas para estes recursos. Saiba mais sobre [pseudónimos](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Para se certificar de que conhece os pseudónimos atuais e reveja os seus recursos e políticas, utilize a extensão da Política Azure para o Código do Estúdio Visual. Pode ser instalado em todas as plataformas que são suportadas pelo Visual Studio Code. Este suporte inclui Windows, Linux e macOS. Consulte [as diretrizes](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode)de instalação .



