---
title: Conector Azure IoT para FHIR (pré-visualização) - Guia de resolução de problemas e como fazer
description: Como resolver problemas comum O Conector IoT para mensagens e condições de erro FHIR (pré-visualização) e ficheiros de mapeamento de cópias
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: jasteppe
ms.openlocfilehash: b404fa5322d3afa8cbf71741382d44dd0433d49c
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553687"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Conector Azure IoT para guia de resolução de problemas FHIR (pré-visualização)

Este artigo fornece passos para a resolução de problemas do conector comum Azure IoT para mensagens e condições de erro FHIR*.  

Você também vai aprender a criar cópias do Conector Azure IoT para ficheiros de mapeamento JSON FHIR para edição e arquivamento fora do portal Azure ou para fornecer ao Suporte Técnico Azure ao abrir um bilhete de suporte. 

## <a name="error-messages-and-fixes"></a>Mensagens de erro e correções

|Mensagem   |Condição  |Correção         |
|----------|-----------|------------|
|Nome de mapeamento inválido, nome de mapeamento deve ser dispositivo ou FHIR|O tipo de mapeamento fornecido não é dispositivo ou FHIR|Utilize um dos dois tipos de mapeamento suportados (por exemplo: Dispositivo ou FHIR)|
|Regenerar parâmetros-chave não definidos|Regenerar pedido-chave|Incluir os parâmetros no pedido chave de regeneração|
|Atingiu o número máximo de instâncias IoT Connector* que podem ser a provisionadas nesta subscrição|Azure IoT Connector para quota de subscrição FHIR alcançado (Padrão é (2) por subscrição)|Eliminar um dos casos existentes de Azure IoT Connector para FHIR, utilizar uma subscrição diferente que não tenha atingido a quota de subscrição ou solicitar um aumento da quota de subscrição|
|O recurso move não é suportado para O Conector IoT ativado AZure API para recurso FHIR|Tentar fazer uma operação de movimento numa API Azure para recurso FHIR que tenha um ou mais casos do Conector Azure IoT para FHIR|Eliminar os casos existentes do Conector Azure IoT para o FHIR realizar e completar a operação de movimento|
|Conector IoT não previsto|Tentativa de utilização de serviços infantis (ligações & mapeamentos) quando o progenitor (Azure IoT Connector for FHIR) não foi a provisionado|Provisionar um Conector Azure IoT para FHIR|
|O pedido não é apoiado|Pedido específico da API não é suportado|Use o pedido de API correto|
|Conta não existe|A tentativa de adicionar um Conector Azure IoT para fHIR e a API Azure para recurso FHIR não existe|Crie a API Azure para o recurso FHIR e, em seguida, reencontre a operação|
|A Azure API para a versão FHIR de recursos FHIR não é suportado para IoT Connector|Tentar utilizar um Conector Azure IoT para FHIR com uma versão incompatível da API Azure para recurso FHIR|Crie um novo API Azure para recurso FHIR (versão R4) ou utilize um API Azure existente para recurso FHIR (versão R4)

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-mapping-files"></a>Criação de cópias do Conector Azure IoT para ficheiros de mapeamento FHIR (pré-visualização)
A cópia do Conector Azure IoT para ficheiros de mapeamento FHIR pode ser útil para a edição e arquivamento fora do site do portal Azure e para fornecer ao Suporte Técnico do Azure ao abrir um bilhete de suporte.

> [!NOTE]
> JSON é o único formato suportado para ficheiros de mapeamento de Dispositivos e FHIR neste momento.

> [!TIP]
> Saiba mais sobre o Conector Azure IoT para dispositivo FHIR [e ficheiros JSON de mapeamento FHIR](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Selecione **"IoT Connector (pré-visualização)"** no lado inferior esquerdo da AZure API para o painel de recursos FHIR na secção **"Add-ins".**

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Selecione o **"Conector"** do qual estará a copiar os ficheiros de mapeamento.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Este processo também pode ser utilizado para copiar e guardar o conteúdo do **"Configure FHIR mapping"** JSON.

3. Selecione **"Configure o mapeamento do dispositivo".**

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Selecione o conteúdo do JSON e faça uma operação de cópia (por exemplo: Selecione Ctrl + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Faça uma operação de pasta (por exemplo: Selecione Ctrl + v) num novo ficheiro dentro de um editor (por exemplo: Visual Studio Code, Notepad) e guarde o ficheiro com uma extensão *.json.

> [!TIP]
> Se vai abrir um bilhete [de Suporte Técnico Azure](https://azure.microsoft.com/support/create-ticket/) para o Conector Azure IoT para FHIR, certifique-se de incluir cópias dos seus ficheiros de mapeamento para ajudar no processo de resolução de problemas.

## <a name="next-steps"></a>Próximos passos

Confira frequentemente perguntas sobre o Conector Azure IoT para FHIR.

>[!div class="nextstepaction"]
>[Conector Azure IoT para FAQs FHIR](fhir-faq.md#azure-iot-connector-for-fhir-preview)

*No portal Azure, o Conector Azure IoT para FHIR é referido como Conector IoT (pré-visualização).

FHIR é a marca registada do HL7 e é utilizada com a permissão do HL7.