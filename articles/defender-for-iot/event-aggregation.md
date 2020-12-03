---
title: Agregação de eventos
description: Saiba mais sobre a agregação de eventos defender para ioT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2020
ms.author: mlottner
ms.openlocfilehash: 7f7575697706363c082a4e6374b3df7a49e65cdf
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548856"
---
# <a name="defender-for-iot-event-aggregation"></a>Defender para agregação de eventos IoT

O Defender para os agentes de segurança IoT recolhe dados e eventos do sistema a partir do seu dispositivo local e envia estes dados para a nuvem Azure para processamento e análise. O agente de segurança recolhe muitos tipos de eventos de dispositivos, incluindo novos processos e novos eventos de conexão. Tanto novos eventos de processo como novos eventos de conexão podem ocorrer legitimamente frequentemente num dispositivo dentro de um segundo, e embora importante para uma segurança robusta e abrangente, o número de mensagens que os agentes de segurança são forçados a enviar pode rapidamente alcançar ou exceder a sua quota IoT Hub e limites de custos. No entanto, estes eventos contêm informações de segurança altamente valiosas que são cruciais para proteger o seu dispositivo.

Para reduzir a quota e os custos adicionais, mantendo os seus dispositivos protegidos, o Defender for IoT Agents agrega este tipo de eventos.

A agregação do evento está **ligada** por predefinição e, embora não recomendada, pode ser desligada manualmente **a** qualquer momento.

A agregação está atualmente disponível para os seguintes tipos de eventos:

* ProcessoCreate
* Conexão
* Processterminar (apenas Windows)

## <a name="how-does-event-aggregation-work"></a>Como funciona a agregação de eventos?

Quando a agregação do evento é deixada **On**, Defender para agentes IoT agrega eventos para o intervalo ou janela de tempo.
Uma vez passado o período de intervalo, o agente envia os eventos agregados para a nuvem Azure para uma análise mais aprofundada.
Os eventos agregados são armazenados na memória até serem enviados para a nuvem Azure.

Para reduzir a pegada de memória do agente, sempre que o agente recolhe um evento idêntico ao que já está a ser guardado na memória, o agente aumenta a contagem de impactos deste evento específico. Quando a janela de tempo de agregação passa, o agente envia a contagem de cada tipo específico de evento que ocorreu. A agregação de eventos é simplesmente a agregação das contagens de sucesso de cada tipo de evento recolhido.

Os acontecimentos são considerados idênticos apenas quando as seguintes condições são satisfeitas:

* Eventos ProcessCreate - quando **commandLine,** **executável,** **username**, e **userid** são idênticos
* Eventos De Conexão - quando **o commandLine**, **userId**, **direction,** **local address,** **endereço remoto,****protocolo e **porta de destino** são idênticos
* Eventos processterminados - quando **executáveis** e **o estado de saída** são idênticos

### <a name="working-with-aggregated-events"></a>Trabalhar com eventos agregados

Durante a agregação, as propriedades do evento que não são agregadas são descartadas, e aparecem em análise de log com um valor de 0.

* Eventos ProcessCreate - **processId**, e **parentProcessId** estão definidos para 0
* Eventos ConnectionCreate - **processId**, e **porta de origem** estão definidos para 0

## <a name="event-aggregation-based-alerts"></a>Alertas baseados na agregação de eventos

Após análise, o Defender for IoT cria alertas de segurança para eventos agregados suspeitos. Os alertas criados a partir de eventos agregados aparecem apenas uma vez para cada evento agregado.

A contagem de tempo de início, fim de tempo e contagem de batidas para cada evento são registados no campo **ExtraDetails** dentro do Log Analytics para uso durante as investigações.

Cada evento agregado representa um período de 24 horas de alertas recolhidos. Utilizando o menu de opções de eventos na parte superior esquerda de cada evento, pode **dispensar** cada evento agregado individual.

## <a name="event-aggregation-twin-configuration"></a>Configuração dupla agregação de eventos

Faça alterações na configuração da agregação de eventos Defender para IoT dentro do objeto de [configuração](how-to-agent-configuration.md) do agente do módulo twin identidade do módulo **de azureiotsecurity.**

| Nome de configuração | Valores possíveis | Detalhes | Observações |
|:-----------|:---------------|:--------|:--------|
| agregaçãoEnabledProcessCreate | boolean | Ativar /desativar a agregação de eventos para criar eventos |
| agregaçãoIntervalProcessCreate | Cadeia ISO8601 Timespan | Intervalo de agregação para processo criar eventos |
| agregaçãoEnabledConnectionCreate | boolean| Ativar /desativar a agregação de eventos para criar eventos de conexão |
| agregaçãoIntervalConnectionCreate | Cadeia ISO8601 Timespan | Intervalo de agregação para ligação criar eventos |
| agregaçãoEnabledProcessTerminar | boolean | Ativar /desativar a agregação de eventos para eventos de terminação de processos | Apenas no Windows|
| agregaçãoIntervalProcessTerminar | Cadeia ISO8601 Timespan | Intervalo de agregação para eventos de terminação de processos | Apenas no Windows|
|

## <a name="default-configurations-settings"></a>Configurações predefinidos

| Nome de configuração | Valores predefinidos |
|:-----------|:---------------|
| agregaçãoEnabledProcessCreate | true |
| agregaçãoIntervalProcessCreate | "PT1H"|
| agregaçãoEnabledConnectionCreate | true |
| agregaçãoIntervalConnectionCreate | "PT1H"|
| agregaçãoEnabledProcessTerminar | true |
| agregaçãoIntervalProcessTerminar | "PT1H"|
|

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre o Defender para agregação de agentes de segurança IoT, e as opções de configuração do evento disponíveis.

Para continuar a trabalhar com o Defender para a implementação de IoT, utilize os seguintes artigos:

- Compreender [os métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)
- Selecione e implemente um [agente de segurança](how-to-deploy-agent.md)
- Rever [Pré-requisitos do serviço](service-prerequisites.md) Defender para IoT
- Saiba como [ativar o serviço de IoT no seu IoT Hub](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do [Defender para IoT FAQ](resources-frequently-asked-questions.md)
