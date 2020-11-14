---
title: Exportar Métricas do Conector IoT do Azure para FHIR (pré-visualização) através das definições Diagnóstico
description: Este artigo explica como exportar o Conector Azure IoT para métricas de FHIR (pré-visualização) através de definições de diagnóstico
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 14fd5378f37ebfc20b2d7084c08f15ea8f7a00b2
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630537"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>Exportar Métricas do Conector IoT do Azure para FHIR (pré-visualização) através das definições Diagnóstico

Neste artigo, você aprenderá a exportar O Conector Azure IoT para Recursos de Interoperabilidade de Cuidados rápidos de saúde (FHIR&#174;)* Registos de métricas. A funcionalidade que permite o registo de métricas são as [**definições de Diagnóstico**](../azure-monitor/platform/diagnostic-settings.md) no portal Azure. 

> [!TIP]
> Siga as orientações em [Enable Diagnostic Logging in Azure API for FHIR and Azure IoT Connector for FHIR](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) to set audit logging.

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>Ativar o registo de métricas para o Conector Azure IoT para FHIR (pré-visualização)
1. Para ativar o registo de métricas para o Conector Azure IoT para FHIR, selecione a sua API Azure para o serviço FHIR no portal Azure 

2. Navegue para **definições de Diagnóstico** 

3. Selecione **+ Adicionar definição de diagnóstico**

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="Conector IoT1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Introduza um nome na caixa de diálogo de definição de **nome de diagnóstico.**

5. Selecione o método que pretende utilizar para aceder aos seus registos de diagnóstico:

    1. **Arquivar para uma conta de armazenamento** para auditoria ou inspeção manual. A conta de armazenamento que pretende utilizar já precisa de ser criada.
    2. **Transmita para o centro** de eventos para ingestão por um serviço de terceiros ou solução analítica personalizada. Você precisará criar um espaço de nome de eventos e política de centro de eventos antes de configurar este passo.
    3. **Transmita para o** espaço de trabalho Log Analytics no Azure Monitor. Terá de criar o seu Espaço de Trabalho de Analítica de Logs antes de poder selecionar esta opção.

6. Selecione **Erros, Tráfego e Latência** para o Conector Azure IoT para FHIR.  Selecione quaisquer categorias métricas adicionais que pretenda capturar para a API Azure para FHIR.

7. Selecione **Guardar**

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="Conector IoT2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Pode levar até 15 minutos para os primeiros registos métricos exibirem no repositório à sua escolha.  
 
Para obter mais informações sobre como trabalhar com registos de diagnóstico, consulte a documentação do [Registo de Recursos Azure](../azure-monitor/platform/platform-logs-overview.md)

## <a name="conclusion"></a>Conclusão 
Ter acesso a registos de métricas é essencial para a monitorização e resolução de problemas.  O Conector Azure IoT para FHIR permite-lhe fazer estas ações através de registos métricos. 

## <a name="next-steps"></a>Passos seguintes

Confira frequentemente perguntas sobre o Conector Azure IoT para FHIR.

>[!div class="nextstepaction"]
>[Conector Azure IoT para FAQs FHIR](fhir-faq.md)

*No portal Azure, o Conector Azure IoT para FHIR é referido como Conector IoT (pré-visualização). FHIR é uma marca registada do HL7 e é usada com a permissão do HL7.