---
title: Diferenças em versão anterior
titleSuffix: Azure Digital Twins
description: Entenda o que mudou na nova versão do Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d964312ea283fee4bb71e9a41b31f6b32de346be
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92327799"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-previous-version-2018"></a>Quais são as novas Gémeas Digitais Azure? Qual é a diferença da versão anterior (2018)?

A primeira pré-estreia pública da Azure Digital Twins foi lançada em outubro de 2018. Embora os conceitos fundamentais dessa versão anterior tenham passado para o novo serviço na pré-visualização pública, muitas das interfaces e detalhes de implementação mudaram para tornar o serviço mais flexível e acessível. Estas alterações foram motivadas pelo feedback do cliente.

> [!IMPORTANT]
> Tendo em conta as capacidades expandidas do novo serviço, o anterior serviço Azure Digital Twins será reformado até ao final do ano de 2020.

Se utilizou a primeira versão da Azure Digital Twins durante a pré-visualização do público anterior, utilize as informações e as melhores práticas deste artigo para aprender a trabalhar com o novo serviço e aproveitar as suas funcionalidades.

## <a name="differences-by-topic"></a>Diferenças por tópico

O gráfico abaixo fornece uma visão lado a lado de conceitos que mudaram entre a versão anterior do serviço e o novo serviço (atual).

| Tópico | Em versão anterior | Em nova versão |
| --- | --- | --- | --- |
| **Modelação**<br>*Mais flexível* | O lançamento anterior foi projetado em torno de espaços inteligentes, por isso veio com um vocabulário embutido para edifícios. | As novas Gémeas Digitais Azure são agnósticas de domínio. Pode definir o seu próprio vocabulário personalizado e modelos personalizados para a sua solução, para representar mais tipos de ambientes de forma mais flexível.<br><br>Saiba mais em [*Conceitos: Modelos personalizados.*](concepts-models.md) |
| **Topologia**<br>*Mais flexível*| O lançamento anterior suportava uma estrutura de dados de árvores, adaptada a espaços inteligentes. Gémeos digitais estavam ligados a relações hierárquicas. | Com o novo lançamento, os seus gémeos digitais podem ser ligados a topologias de gráficos arbitrárias, organizadas como quiser. Isto dá-lhe mais flexibilidade para expressar as complexas relações do mundo real.<br><br>Saiba mais em [*Conceitos: Gémeos Digitais e o gráfico gémeo.*](concepts-twins-graph.md) |
| **Computação**<br>*Mais rico, mais flexível* | Na versão anterior, a lógica para eventos de processamento e telemetria foi definida em funções definidas pelo utilizador JavaScript (UDFs). Depurar com UDFs era limitado. | O novo lançamento tem um modelo de computação aberto: fornece lógica personalizada anexando recursos de computação externos como [as Funções Azure](../azure-functions/functions-overview.md). Isto permite-lhe usar uma linguagem de programação à sua escolha, aceder a bibliotecas de códigos personalizadas sem restrições, e aproveitar o desenvolvimento e depurar recursos que o serviço externo pode ter.<br><br>Saiba mais em [*Como:Configurar uma função Azure para o processamento de dados*](how-to-create-azure-function.md). |
| **Gestão de dispositivos com IoT Hub**<br>*Mais acessível* | O lançamento anterior geriu dispositivos com uma instância de [IoT Hub](../iot-hub/about-iot-hub.md) que era interna no serviço Azure Digital Twins. Este hub integrado não era totalmente acessível aos desenvolvedores. | No novo lançamento, "traga o seu próprio" hub IoT, anexando uma instância IoT Hub criada de forma independente (juntamente com quaisquer dispositivos que já gere). Isto dá-lhe acesso total às capacidades do IoT Hub e coloca-o no controlo da gestão de dispositivos.<br><br>Saiba mais em [*Como-a-fazer: Ingerir telemetria a partir do IoT Hub*](how-to-ingest-iot-hub-data.md). |
| **Segurança**<br>*Mais padrão* | O lançamento anterior tinha funções pré-definidas que poderia utilizar para gerir o acesso ao seu caso. | A nova versão integra-se com o mesmo serviço de [back-end baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md) que outros serviços Azure utilizam. Isto pode tornar mais simples a autenticação entre outros serviços Azure na sua solução, como IoT Hub, Azure Functions, Event Grid, entre outros.<br>Com o Azure RBAC, ainda pode usar funções pré-definidas, ou pode construir e configurar funções personalizadas.<br><br>Saiba mais em [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md). |
| **Escalabilidade**<br>*Maior* | O lançamento anterior tinha limitações de escala para dispositivos, mensagens, gráficos e unidades de escala. Apenas um caso de Azure Digital Twins foi suportado por subscrição.  | O novo lançamento baseia-se numa nova arquitetura com melhoria da escalabilidade, e tem maior poder de computação. Apoia também 10 instâncias por região, por subscrição.<br><br>[*Ver Referência: Limites de serviço de pré-visualização público*](reference-service-limits.md) para detalhes dos limites em pré-visualização pública agora. |

## <a name="service-limits-in-public-preview"></a>Limites de serviço na visualização pública

Para obter uma lista dos limites das Gémeas Digitais Azure durante esta pré-visualização pública, consulte [*Referência: Limites de serviço de pré-visualização do público*](reference-service-limits.md).

## <a name="next-steps"></a>Passos seguintes

Em seguida, mergulhe em trabalhar com a Azure Digital Twins com o primeiro tutorial:

[*Tutorial: Código de uma aplicação de cliente*](tutorial-code.md)