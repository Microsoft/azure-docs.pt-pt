---
title: Agregação de eventos
description: Saiba mais sobre o Azure Security Center para agregação de eventos IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: f72ef8cc5161bd6f885249e7d39344a57fa2368e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311421"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>Centro de Segurança Azure para agregação de eventos IoT

O Azure Security Center para agentes de segurança IoT recolhe dados e eventos do sistema do seu dispositivo local e envia estes dados para a nuvem Azure para processamento e análise. O agente de segurança recolhe muitos tipos de eventos de dispositivos, incluindo novos processos e novos eventos de conexão. Tanto os novos processos como os novos eventos de conexão podem ocorrer com frequência num dispositivo dentro de um segundo, e embora importante para uma segurança robusta e abrangente, o número de mensagens que os agentes de segurança são forçados a enviar pode rapidamente atingir ou exceder a sua quota ioT Hub e limites de custos. No entanto, estes eventos contêm informações de segurança altamente valiosas que são cruciais para proteger o seu dispositivo.

Para reduzir a quota adicional e os custos mantendo os seus dispositivos protegidos, o Azure Security Center for IoT Agents agrega este tipo de eventos.

A agregação do evento é **ligada** por defeito, e embora não seja recomendada, pode ser **desligada** manualmente a qualquer momento.

A agregação está atualmente disponível para os seguintes tipos de eventos:

* ProcessCreate
* ConexãoCriar
* ProcessoTerminate (apenas Windows)

## <a name="how-does-event-aggregation-work"></a>Como funciona a agregação de eventos?

Quando a agregação de eventos é deixada **on**, O Centro de Segurança Azure para agentes IoT agrega eventos para o período de intervalo ou janela de tempo.
Uma vez ultrapassado o período de intervalo, o agente envia os eventos agregados para a nuvem Azure para uma análise mais aprofundada.
Os eventos agregados são armazenados na memória até serem enviados para a nuvem Azure.

Para reduzir a pegada de memória do agente, sempre que o agente recolhe um evento idêntico ao que já está a ser guardado na memória, o agente aumenta a contagem de sucesso deste evento específico. Quando a janela do tempo de agregação passa, o agente envia a contagem de batidas de cada tipo específico de evento que ocorreu. A agregação de eventos é simplesmente a agregação das contagens de sucesso de cada tipo de evento recolhido.

Os eventos só são considerados idênticos quando forem satisfeitas as seguintes condições:

* ProcessoCriar eventos - quando **commandLine**, **executável,** **username**, e **userid** são idênticos
* ConexãoCriar eventos - quando **commandLine**, **userId**, **direção,** **endereço local,** **endereço remoto,****protocolo e porta de **destino** são idênticos
* Eventos ProcessTerminate - quando o estado **executável** e **de saída** são idênticos

### <a name="working-with-aggregated-events"></a>Trabalhar com eventos agregados

Durante a agregação, as propriedades do evento que não são agregadas são descartadas e aparecem em log analytics com um valor de 0.

* ProcessoCriar eventos - **processId**, e **parentProcessId** estão definidos para 0
* ConexãoCriar eventos - **processId**, e **porta de origem** estão definidos para 0

## <a name="event-aggregation-based-alerts"></a>Alertas baseados na agregação de eventos

Após análise, o Azure Security Center for IoT cria alertas de segurança para eventos agregados suspeitos. Os alertas criados a partir de eventos agregados aparecem apenas uma vez para cada evento agregado.

Hora de início de agregação, hora final e contagem de sucesso para cada evento são registados no campo **ExtraDetails** dentro do Log Analytics para utilização durante as investigações.

Cada evento agregado representa um período de 24 horas de alertas recolhidos. Utilizando o menu de opções do evento na parte superior esquerda de cada evento, pode **descartar** cada evento agregado individual.

## <a name="event-aggregation-twin-configuration"></a>Configuração gémea de agregação de eventos

Faça alterações na configuração do Azure Security Center para agregação de eventos IoT dentro do objeto de [configuração](how-to-agent-configuration.md) do agente do módulo identidade twin do módulo de **segurança azureiot.**

| Nome de configuração | Valores possíveis | Detalhes | Observações |
|:-----------|:---------------|:--------|:--------|
| agregaçãoEnabledProcessCreate | boolean | Ativar/desativar a agregação de eventos para o processo criar eventos |
| agregaçãoIntervalProcessCreate | Cadeia ISO8601 Timespan | Intervalo de agregação para processo criar eventos |
| agregaçãoEnabledConnectionCreate | boolean| Ativar/desativar a agregação de eventos para a ligação criar eventos |
| agregaçãoIntervalConnectionCreate | Cadeia ISO8601 Timespan | Intervalo de agregação para ligação criar eventos |
| agregaçãoEnabledProcessTerminate | boolean | Ativar/desativar a agregação de eventos para eventos de terminação de processos | Apenas no Windows|
| agregaçãoIntervalProcessTerm | Cadeia ISO8601 Timespan | Intervalo de agregação para eventos de rescisão de processo | Apenas no Windows|
|

## <a name="default-configurations-settings"></a>Definições de configurações predefinidas

| Nome de configuração | Valores predefinidos |
|:-----------|:---------------|
| agregaçãoEnabledProcessCreate | true |
| agregaçãoIntervalProcessCreate | "PT1H"|
| agregaçãoEnabledConnectionCreate | true |
| agregaçãoIntervalConnectionCreate | "PT1H"|
| agregaçãoEnabledProcessTerminate | true |
| agregaçãoIntervalProcessTerm | "PT1H"|
|

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você soube sobre o Azure Security Center para agregação de agentes de segurança IoT, e as opções de configuração de eventos disponíveis.

Para continuar a começar com o Azure Security Center para a implantação de IoT, use os seguintes artigos:

- Compreender os métodos de [autenticação do agente de segurança](concept-security-agent-authentication-methods.md)
- Selecione e implante um [agente de segurança](how-to-deploy-agent.md)
- Rever o Centro de Segurança Azure para [os pré-requisitos](service-prerequisites.md) do serviço IoT
- Saiba como ativar o Centro de [Segurança Azure para o serviço IoT no seu Hub IoT](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do Centro de [Segurança Azure para as FAQ IoT](resources-frequently-asked-questions.md)
