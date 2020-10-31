---
title: Ver e configurar O Conector Azure IoT para métricas FHIR (pré-visualização)
description: Este artigo explica a exibição e configuração do Conector Azure IoT para métricas de FHIR (pré-visualização)
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 76166fc5c525d36474a585179472e93b22dad647
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133628"
---
# <a name="view-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Ver e configurar O Conector Azure IoT para métricas FHIR (pré-visualização) 

Neste artigo, você vai aprender a ver e configurar O Conector Azure IoT para métricas FHIR*. 

> [!TIP]
> Siga as orientações em [Export Azure IoT Connector para Métricas FHIR (pré-visualização) através de definições de diagnóstico](https://docs.microsoft.com/azure/healthcare-apis/iot-metrics-diagnostics-export) para aprender a configurar a exportação de dados métricos.

## <a name="view-metrics-for-azure-iot-connector-for-fhir-preview"></a>Ver métricas para conector Azure IoT para FHIR (pré-visualização)
1. Para visualizar métricas de Conectores IoT, selecione a sua API AZure para o serviço FHIR no portal Azure. 

2. Navegar para **métricas** 

3. Selecione o **separador IoT Connector.**

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="Conector IoT1" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Selecione um Conector IoT para visualizar as suas Métricas (por exemplo: existem (4) Conectores IoT associados a este AZure API para o serviço FHIR).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="Conector IoT1" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

> [!NOTE]
> O separador **Personalizado** permite criar combinações específicas de tempo/data para visualização de Métricas do Conector IoT.

5. Selecione o período de tempo das Métricas do Conector IoT a visualizar (por exemplo: 1 hora, 24 horas, 7 dias ou personalizado).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="Conector IoT1" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metrics-types-for-azure-iot-connector-for-fhir-preview"></a>Tipos de métricas para Conector Azure IoT para FHIR (pré-visualização) 
As métricas do conector IoT apresentadas são as seguintes:

|Tipo de métricas|Propósito de métricas| 
|-----------|--------------|
|Número de Mensagens recebidas|O número de mensagens recebidas em bruto (por exemplo: os eventos do dispositivo).|
|Número de mensagens normalizadas|O número das mensagens normalizadas.|
|Número de grupos de mensagens|O número de grupos que têm mensagens agregadas na janela de tempo designada.|
|Latência média do estágio normalizado|Latência média da fase de normalização. Normalizar o estágio é realizar a normalização em mensagens de entrada brutas.|
|Latência média da fase de grupo|Latência média da fase de grupos. A fase de grupos é realizar tampão, agregação e agrupamento em mensagens normalizadas.| 
|Contagem total de erros|Número total de erros.| 

## <a name="focusing-and-configuring-azure-iot-connector-for-fhir-preview-metrics"></a>Focalização e configuração do Conector Azure IoT para métricas FHIR (pré-visualização)
Neste exemplo, estaremos focados no **Número de Mensagens Recebidas.**

1. Selecione um ponto no tempo em que queira concentrar-se.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="Conector IoT1" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. A partir deste ecrã, pode **adicionar métrica,** **adicionar filtro** e aplicar **a divisão** para mais personalizações. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="Conector IoT1" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Conclusão 
Ter acesso às métricas do plano de dados é essencial para a monitorização e resolução de problemas.  O Conector Azure IoT para FHIR ajuda-o a fazer estas ações através de Metrics. 

## <a name="next-steps"></a>Passos seguintes

Confira frequentemente perguntas sobre o Conector Azure IoT para FHIR.

>[!div class="nextstepaction"]
>[Conector Azure IoT para FAQs FHIR](fhir-faq.md)

*No portal Azure, o Conector Azure IoT para FHIR é referido como Conector IoT (pré-visualização).

FHIR é a marca registada do HL7 e é utilizada com a permissão do HL7.