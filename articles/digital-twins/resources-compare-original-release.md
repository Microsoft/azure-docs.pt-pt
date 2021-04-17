---
title: Diferenças de lançamento original
titleSuffix: Azure Digital Twins
description: Entenda o que mudou na nova versão do Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 2bfd36b79ad800c14a68948aa8488e842d3f4def
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481179"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Quais são as novas Gémeas Digitais Azure? Qual é a diferença da versão original (2018)?

A primeira pré-estreia pública da Azure Digital Twins foi lançada em outubro de 2018. Embora os conceitos fundamentais dessa versão original tenham levado ao serviço atual, muitas das interfaces e detalhes de implementação mudaram para tornar o serviço mais flexível e acessível. Estas alterações foram motivadas pelo feedback do cliente.

> [!IMPORTANT]
> Tendo em conta as capacidades expandidas do novo serviço, o serviço original Azure Digital Twins foi reformado. A partir de janeiro de 2021, as suas APIs e dados associados deixaram de estar disponíveis.

Se utilizou a primeira versão da Azure Digital Twins durante a primeira pré-visualização pública, use as informações e as melhores práticas deste artigo para aprender a trabalhar com o serviço atual e tirar partido das suas funcionalidades.

## <a name="differences-by-topic"></a>Diferenças por tópico

O gráfico abaixo fornece uma visão lado a lado de conceitos que mudaram entre a versão original do serviço e o serviço atual.

| Tópico | Na versão original | Na versão atual |
| --- | --- | --- | --- |
| **Modelação**<br>*Mais flexível* | O lançamento original foi projetado em torno de espaços inteligentes, por isso veio com um vocabulário embutido para edifícios. | As atuais Gémeas Digitais Azure são agnósticas de domínio. Pode definir o seu próprio vocabulário personalizado e modelos personalizados para a sua solução, para representar mais tipos de ambientes de forma mais flexível.<br><br>Saiba mais em [*Conceitos: Modelos personalizados.*](concepts-models.md) |
| **Topologia**<br>*Mais flexível*| A versão original suportava uma estrutura de dados de árvores, adaptada a espaços inteligentes. Gémeos digitais estavam ligados a relações hierárquicas. | Com o lançamento atual, os seus gémeos digitais podem ser ligados a topologias de gráficos arbitrárias, organizadas como quiser. Isto dá-lhe mais flexibilidade para expressar as complexas relações do mundo real.<br><br>Saiba mais em [*Conceitos: Gémeos Digitais e o gráfico gémeo.*](concepts-twins-graph.md) |
| **Computação**<br>*Mais rico, mais flexível* | Na versão original, a lógica para eventos de processamento e telemetria foi definida em funções definidas pelo utilizador JavaScript (UDFs). Depurar com UDFs era limitado. | O lançamento atual tem um modelo de computação aberto: fornece lógica personalizada anexando recursos de computação externos como [as Funções Azure](../azure-functions/functions-overview.md). Isto permite-lhe usar uma linguagem de programação à sua escolha, aceder a bibliotecas de códigos personalizadas sem restrições, e aproveitar o desenvolvimento e depurar recursos que o serviço externo pode ter.<br><br>Saiba mais em [*Como:Configurar uma função Azure para o processamento de dados*](how-to-create-azure-function.md). |
| **Gestão de dispositivos com IoT Hub**<br>*Mais acessível* | O lançamento original geriu dispositivos com um exemplo de [IoT Hub](../iot-hub/about-iot-hub.md) que era interno para o serviço Azure Digital Twins. Este hub integrado não era totalmente acessível aos desenvolvedores. | No lançamento atual, você "traz o seu próprio" hub IoT, anexando uma instância IoT Hub criada independentemente (juntamente com quaisquer dispositivos que já gere). Isto dá-lhe acesso total às capacidades do IoT Hub e coloca-o no controlo da gestão de dispositivos.<br><br>Saiba mais em [*Como-a-fazer: Ingerir telemetria a partir do IoT Hub*](how-to-ingest-iot-hub-data.md). |
| **Segurança**<br>*Mais padrão* | A versão original tinha funções pré-definidas que poderia usar para gerir o acesso ao seu exemplo. | A versão atual integra-se com o mesmo serviço de [back-end baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md) que outros serviços Azure utilizam. Isto pode tornar mais simples a autenticação entre outros serviços Azure na sua solução, como IoT Hub, Azure Functions, Event Grid, entre outros.<br>Com o RBAC, ainda pode usar funções pré-definidas, ou pode construir e configurar funções personalizadas.<br><br>Saiba mais em [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md). |
| **Escalabilidade**<br>*Maior* | A versão original tinha limitações de escala para dispositivos, mensagens, gráficos e unidades de escala. Apenas um caso de Azure Digital Twins foi suportado por subscrição.  | O lançamento atual baseia-se numa nova arquitetura com uma escalabilidade melhorada, e tem maior poder de computação. Apoia também 10 instâncias por região, por subscrição.<br><br>Consulte os limites de [*serviço da Azure Digital Twins*](reference-service-limits.md) para obter detalhes sobre os limites da versão atual. |

## <a name="service-limits"></a>Limites do serviço

Para obter uma lista dos limites dos Gémeos Digitais Azure, consulte [*os limites de serviço da Azure Digital Twins*](reference-service-limits.md).

## <a name="next-steps"></a>Passos seguintes

* Mergulhe em trabalhar com o lançamento atual no quickstart: [*Quickstart: Explore um cenário de amostra*](quickstart-azure-digital-twins-explorer.md).

* Ou, comece a ler sobre conceitos-chave com [*Conceitos: Modelos personalizados.*](concepts-models.md)