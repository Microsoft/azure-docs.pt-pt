---
title: 'Conceitos: Fluxo de dados no Conector Azure IoT para FHIR (pré-visualização) recurso da API Azure para FHIR'
description: Compreenda o Conector Azure IoT para o fluxo de dados do FHIR (pré-visualização). Azure IoT Connector for FHIR (pré-visualização) ingere, normaliza, grupos, transforma e persiste dados IoMT para Azure API para FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: punagpal
ms.openlocfilehash: 3cae648e3c2bddbafec555621d97575a007cfeb4
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394871"
---
# <a name="azure-iot-connector-for-fhir-preview-data-flow"></a>Fluxo de dados do Conector IoT do Azure para o FHIR (pré-visualização)

Este artigo fornece uma visão geral do fluxo de dados no Conector Azure IoT para FHIR*. Você vai aprender sobre diferentes fases de processamento de dados dentro do Conector Azure IoT para FHIR que transformam dados do dispositivo em recursos de [observação](https://www.hl7.org/fhir/observation.html) baseados em FHIR.

![Conector Azure IoT para fluxo de dados FHIR](media/concepts-iot-data-flow/iot-connector-data-flow.png)

O diagrama acima mostra fluxos de dados comuns utilizando o Conector Azure IoT para FHIR. 

Abaixo estão diferentes fases que os dados passam uma vez recebidos pelo Azure IoT Connector para FHIR.

## <a name="ingest"></a>Ingerir
Ingest é a primeira fase em que os dados do dispositivo são recebidos no Conector Azure IoT para FHIR. O ponto final de ingestão para dados do dispositivo está hospedado num [Hub de Eventos Azure](../event-hubs/index.yml). A plataforma Azure Event Hub suporta alta escala e produção com capacidade de receber e processar milhões de mensagens por segundo. Também permite que o Conector Azure IoT para o FHIR consuma mensagens assíncronamente, removendo a necessidade de os dispositivos esperarem enquanto os dados do dispositivo são tratados.

> [!NOTE]
> JSON é o único formato suportado neste momento para os dados do dispositivo.

## <a name="normalize"></a>Normalizar
Normalizar é a fase seguinte onde os dados do dispositivo são recuperados a partir do Azure Event Hub acima e processados usando modelos de mapeamento de dispositivos. Este processo de mapeamento resulta na transformação de dados do dispositivo num esquema normalizado. 

O processo de normalização não só simplifica o processamento de dados em fases posteriores, como também fornece a capacidade de projetar uma mensagem de entrada em várias mensagens normalizadas. Por exemplo, um dispositivo pode enviar múltiplos sinais vitais para a temperatura corporal, a pulsação, a pressão arterial e a taxa de respiração numa única mensagem. Esta mensagem de entrada criaria quatro recursos FHIR separados. Cada recurso representaria um sinal vital diferente, com a mensagem de entrada projetada em quatro mensagens normalizadas diferentes.

## <a name="group"></a>Group
O grupo é a fase seguinte em que as mensagens normalizadas disponíveis na fase anterior são agrupadas utilizando três parâmetros diferentes: identidade do dispositivo, tipo de medição e período de tempo.

O agrupamento de modelos de identidade e medição do dispositivo permitem a utilização do tipo de medição [SampledData.](https://www.hl7.org/fhir/datatypes.html#SampledData) Este tipo fornece uma forma concisa de representar uma série de medições baseadas no tempo a partir de um dispositivo em FHIR. E o período de tempo controla a latência em que os recursos de observação gerados pelo Conector Azure IoT para FHIR são escritos à Azure API para fHIR.

> [!NOTE]
> O valor do período de tempo está em incumprimento de 15 minutos e não pode ser configurado para pré-visualização.

## <a name="transform"></a>Transformação
Na fase Transform, as mensagens normalizadas são processadas através de modelos de mapeamento FHIR. As mensagens que correspondem a um tipo de modelo são transformadas em recursos de observação baseados em FHIR, conforme especificado através do mapeamento.

Neste momento, o recurso [do Dispositivo,](https://www.hl7.org/fhir/device.html) juntamente com o seu recurso [paciente](https://www.hl7.org/fhir/patient.html) associado, também é recuperado do servidor FHIR utilizando o identificador do dispositivo presente na mensagem. Estes recursos são adicionados como referência à criação do recurso Observation.

> [!NOTE]
> Todos os look ups de identidade são em cache uma vez resolvidos para diminuir a carga no servidor FHIR. Se planeia reutilizar dispositivos com vários pacientes, é aconselhável criar um recurso de dispositivo virtual específico para o paciente e enviar o identificador de dispositivo virtual na carga útil da mensagem. O dispositivo virtual pode ser ligado ao recurso do dispositivo real como pai.

Se não existir qualquer recurso do Dispositivo para um determinado identificador de dispositivos no servidor FHIR, o resultado depende do valor do `Resolution Type` conjunto no momento da criação. Quando programado para `Lookup` , a mensagem específica é ignorada e o pipeline continuará a processar outras mensagens recebidas. Se estiver `Create` definido, o Conector Azure IoT para FHIR criará um dispositivo e recursos do paciente nus no servidor FHIR.  

## <a name="persist"></a>Persistir
Uma vez gerado o recurso FHIR de observação na fase Transform, o recurso é guardado na AZure API para FHIR. Se o recurso FHIR for novo, será criado no servidor. Se o recurso FHIR já existisse, será atualizado.

## <a name="next-steps"></a>Passos seguintes

Clique abaixo do próximo passo para aprender a criar modelos de mapeamento de dispositivos e FHIR.

>[!div class="nextstepaction"]
>[Conector Azure IoT para modelos de mapeamento FHIR](iot-mapping-templates.md)

*No portal Azure, o Conector Azure IoT para FHIR é referido como Conector IoT (pré-visualização).

FHIR é a marca registada do HL7 e é utilizada com a permissão do HL7.