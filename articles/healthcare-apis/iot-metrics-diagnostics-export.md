---
title: Conector Azure IoT exportação para métricas FHIR (pré-visualização) através de definições de diagnóstico
description: Este artigo explica como exportar o Conector Azure IoT para métricas de FHIR (pré-visualização) através de definições de diagnóstico
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/16/2020
ms.author: jasteppe
ms.openlocfilehash: c81dcdd2e79f5d89a0766415b47ad118874e5ad2
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210032"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>Conector Azure IoT exportação para métricas FHIR (pré-visualização) através de definições de diagnóstico

Neste artigo, você vai aprender a exportar Azure IoT Connector para registos de métricas FHIR*. A funcionalidade que permite a registo métrico são as [**definições de Diagnóstico**](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) no portal Azure. 

> [!TIP]
> Siga as orientações em [Enable Diagnostic Logging in Azure API for FHIR and Azure IoT Connector for FHIR](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) to set audit logging.

## <a name="enable-metric-logging-for-the-azure-iot-connector-for-fhir-preview"></a>Ativar o registo métrico do Conector Azure IoT para FHIR (pré-visualização)
1. Para ativar o registo métrico para o Conector Azure IoT para FHIR, selecione a sua API Azure para o serviço FHIR no portal Azure 

2. Navegue para **definições de Diagnóstico** 

3. Selecione **+ Adicionar definição de diagnóstico**

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="Conector IoT1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Introduza um nome na caixa de diálogo de definição de **nome de diagnóstico.**

5. Selecione o método que pretende utilizar para aceder aos seus registos de diagnóstico:

    1. **Arquivar para uma conta de armazenamento** para auditoria ou inspeção manual. A conta de armazenamento que pretende utilizar já precisa de ser criada.
    2. **Transmita para o centro** de eventos para ingestão por um serviço de terceiros ou solução analítica personalizada. Você precisará criar um espaço de nome de eventos e política de centro de eventos antes de configurar este passo.
    3. **Transmita para o** espaço de trabalho Log Analytics no Azure Monitor. Terá de criar o seu Espaço de Trabalho de Analítica de Logs antes de poder selecionar esta opção.

6. Selecione **Erros, Tráfego e Latência** para o Conector Azure IoT para FHIR e quaisquer categorias métricas adicionais que pretenda capturar para a API Azure para FHIR.

7. Selecione **Guardar**

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="Conector IoT1" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Pode levar até 15 minutos para os primeiros registos métricos exibirem no repositório à sua escolha.  
 
Para obter mais informações sobre como trabalhar com registos de diagnóstico, consulte a documentação do [Registo de Recursos Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview)

## <a name="conclusion"></a>Conclusão 
Ter acesso a registos métricos é essencial para a monitorização e resolução de problemas.  O Conector Azure IoT para FHIR permite-lhe fazer estas ações através de registos métricos. 

## <a name="next-steps"></a>Passos seguintes

Confira frequentemente perguntas sobre o Conector Azure IoT para FHIR.

>[!div class="nextstepaction"]
>[Conector Azure IoT para FAQs FHIR](fhir-faq.md#azure-iot-connector-for-fhir-preview)

*No portal Azure, o Conector Azure IoT para FHIR é referido como Conector IoT (pré-visualização).

FHIR é a marca registada do HL7 e é utilizada com a permissão do HL7.
