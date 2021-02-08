---
title: Agregação de eventos (Pré-visualização)
titleSuffix: Azure Defender for IoT
description: O Defender para agentes de segurança IoT recolhe dados e eventos do sistema a partir do seu dispositivo local, e envia os dados para a nuvem Azure para processamento e análise.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: azure
ms.openlocfilehash: 9ce24a44e48f090a0dc7d355952b3cf50dd4318c
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809918"
---
# <a name="event-aggregation-preview"></a>Agregação de eventos (Pré-visualização)

O Defender para agentes de segurança IoT recolhe dados e eventos do sistema a partir do seu dispositivo local, e envia os dados para a nuvem Azure para processamento e análise. O micro-agente do Defender for IoT recolhe muitos tipos de eventos de dispositivos, incluindo novos processos, e todos os novos eventos de conexão. Tanto o novo processo como os novos eventos de ligação podem ocorrer frequentemente num dispositivo dentro de um segundo. Esta capacidade é importante para uma segurança abrangente, no entanto, o número de mensagens que os agentes de segurança enviam pode rapidamente cumprir, ou exceder a sua quota de IoT Hub e limites de custos. No entanto, estes eventos contêm informações de segurança altamente valiosas que são cruciais para proteger o seu dispositivo. 

Para reduzir a quota extra e os custos, mantendo os seus dispositivos protegidos, o Defender para agentes IoT agrega este tipo de eventos: 

- ProcessCreate (apenas Linux) 

- ConnectionCreate (apenas Azure RTOS) 

## <a name="how-does-event-aggregation-work"></a>Como funciona a agregação de eventos? 

Defender os agentes IoT agrega eventos para o período de intervalo, ou janela de tempo. Uma vez passado o período de intervalo, o agente envia os eventos agregados para a nuvem Azure para uma análise mais aprofundada. Os eventos agregados são armazenados na memória até serem enviados para a nuvem Azure. 

Quando o agente recolhe um evento idêntico ao que já está guardado na memória, o agente aumenta a contagem de impactos deste evento específico, de forma a reduzir a pegada de memória do agente. Quando a janela de tempo de agregação passa, o agente envia a contagem de cada tipo de evento que ocorreu. A agregação de eventos é simplesmente a agregação das contagens de sucesso de cada tipo de evento recolhido. 

## <a name="process-events"></a>Processar eventos 

Atualmente, os eventos de processo são apenas suportados nos sistemas operativos Linux. 

Os eventos de processo são considerados idênticos quando a *linha de comando*, e  *userid*   são idênticos. 

O tampão padrão para eventos de processo é 32 processos, após o qual o tampão vai pedalar, e os eventos de processo mais antigos são descartados de forma a dar espaço para novos eventos de processo.  

## <a name="network-connection-events"></a>Eventos de ligação à rede 

Atualmente, os eventos de Ligação de Rede são apenas suportados no Azure RTOS. 

Os eventos de Ligação de Rede são considerados idênticos quando a *porta local,* *porta remota,* *protocolo de transporte,* *endereço local* e  *endereço remoto* são idênticos. 

O tampão predefinido para eventos de ligação à rede é 64. Nenhum novo evento de rede será cached até o próximo ciclo de recolha. Será registado um aviso para aumentar o tamanho da cache.

## <a name="next-steps"></a>Passos seguintes

Verifique se o seu [Defender tem alertas de segurança IoT](concept-security-alerts.md).
