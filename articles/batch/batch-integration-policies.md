---
title: Integração com políticas azure - Lote Azure / Microsoft Docs
description: ''
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/24/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9a306457f838fc79d12be3217d96cc8fb25c9c1b
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618376"
---
# <a name="integration-with-azure-policy"></a>Integração com a Política Azure

A Azure Policy é um serviço em Azure que utiliza para criar, atribuir e gerir políticas que apliquem regras sobre os seus recursos para garantir que esses recursos se mantenham em conformidade com os seus padrões corporativos e acordos de nível de serviço. A Política Azure avalia os seus recursos por incumprimento das políticas que atribui. 

O Azure Batch tem duas extensões incorporadas para ajudá-lo a gerir o cumprimento da política. 

|**Nome...**|   **Descrição**|**Efeitos(s)**|  **Versão**|    **Origem**
|----------------|----------|----------|----------------|---------------|
|Os registos de diagnóstico nas contas do Lote devem ser ativados|   Auditoria que permite registos de diagnóstico. Isto permite-lhe recriar trilhas de atividade para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida|AuditoriaIfNotExists, Deficiente|  2.0.0|  GitHub|
|As regras de alerta métrico devem ser configuradas nas contas do Lote| Configuração de auditoria das regras de alerta métrico na conta do Lote para ativar a métrica necessária|   AuditoriaIfNotExists, Deficiente| 1.0.0|  GitHub|

As definições políticas descrevem as condições que devem ser satisfeitas. Uma condição compara a propriedade do recurso a um valor exigido. Os campos de propriedade de recursos são acedidos usando pseudónimos pré-definidos. Você usa pseudónimos de propriedade para aceder a propriedades específicas para um tipo de recurso. Aliases permitem-lhe restringir quais valores ou condições são permitidas para uma propriedade num recurso. Cada alias mapeia para caminhos em diferentes versões de API para um tipo de recurso específico. Durante a avaliação da política, o mecanismo da diretiva obtém o caminho de propriedade para essa versão de API.

Os recursos exigidos pelo Batch incluem: conta, nó de cálculo, piscina, trabalho e tarefa. Assim, você usaria pseudónimos de propriedade para aceder a propriedades específicas para estes recursos. Saiba mais sobre [pseudónimos](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Para se certificar de que conhece os pseudónimos atuais e reveja os seus recursos e políticas, utilize a extensão da política Azure para o Código do Estúdio Visual. Pode ser instalado em todas as plataformas que são suportadas pelo Visual Studio Code. Este suporte inclui Windows, Linux e macOS. Consulte [as diretrizes](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode)de instalação .



