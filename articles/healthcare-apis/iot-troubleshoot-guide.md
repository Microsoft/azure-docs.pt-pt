---
title: Conector Azure IoT para FHIR (Conector IoT) - Guia de resolução de problemas e como fazer
description: Como resolver problemas com mensagens e condições comuns de erro do Conector IoT e copiar ficheiros de mapeamento
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jasteppe
ms.openlocfilehash: eff1272318413da7855134b0a8a44dd0a0711a6c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285582"
---
# <a name="iot-connector-preview-troubleshooting-guide"></a>Guia de resolução de problemas IoT Connector (pré-visualização)

Este artigo fornece passos para resolver mensagens e condições comuns de erro do Conector IoT (pré-visualização).  

Também aprenderá a criar cópias dos ficheiros de mapeamento JSON (pré-visualização) do IoT Connector (pré-visualização) para edição e arquivamento fora do portal Azure.

## <a name="error-messages-and-fixes"></a>Mensagens de erro e correções

|Mensagem   |Condição  |Correção         |
|----------|-----------|------------|
|Nome de mapeamento inválido, nome de mapeamento deve ser dispositivo ou FHIR|O tipo de mapeamento fornecido não é dispositivo ou FHIR|Utilize um dos dois tipos de mapeamento suportados (por exemplo: Dispositivo ou FHIR)|
|Regenerar parâmetros-chave não definidos|Regenerar pedido-chave|Incluir os parâmetros no pedido chave de regeneração|
|Atingiu o número máximo de instâncias do Conector IoT que podem ser a provisionadas nesta subscrição|Quota de subscrição do Conector IoT alcançada (O padrão é 2 por subscrição)|Eliminar um dos Conectores existentes, utilizar uma subscrição diferente que não tenha atingido os (2) Conectores por quota de subscrição ou solicitar um aumento da quota de subscrição|
|O recurso move não é suportado para O Conector IoT ativado AZure API para recurso FHIR|Tentar fazer uma operação de movimento numa API Azure para recurso FHIR que tenha um ou mais Conectores IoT|Eliminar os Conectores existentes para executar/completar a(s) operação de movimento(s)|
|A Azure API para recurso FHIR tem Ligação Privada ativada.  Private Link não é suportado com IoT Connector|Tentar adicionar um Conector IoT a uma API Azure para recurso FHIR que tenha ligação privada ativada|Selecione ou crie um API Azure para recurso FHIR (versão R4) que não tenha ligação privada ativada|
|Conector IoT não previsto|Tentativa de utilização de serviços infantis (ligações & mapeamentos) quando os pais (IoT Connector) não foram a provisionados|Provisionamento de um Conector IoT|
|O pedido não é apoiado|Pedido específico da API não é suportado|Use o pedido de API correto|
|Conta não existe|A tentativa de adicionar um Conector IoT e a API Azure para recurso FHIR não existe|Crie a API Azure para o recurso FHIR e, em seguida, reencontre a operação|
|A Azure API para a versão FHIR de recursos FHIR não é suportado para IoT Connector|Tentar utilizar um Conector IoT com uma versão incompatível da API Azure para recurso FHIR|Crie um novo API Azure para recurso FHIR (versão R4) ou utilize um API Azure existente para recurso FHIR (versão R4)

## <a name="creating-copies-of-the-iot-connector-preview-mapping-files"></a>Criação de cópias dos ficheiros de mapeamento do IoT Connector (pré-visualização)
> [!NOTE]
> JSON é o único formato suportado para ficheiros de mapeamento de Dispositivos e FHIR neste momento.

> [!TIP]
> A cópia dos ficheiros de mapeamento do IoT Connector pode ser útil para a edição e arquivamento fora do website do Portal Azure e para fornecer ao Suporte Técnico do Azure ao abrir um bilhete de suporte.
> 
> Saiba mais sobre o dispositivo de conector IoT [e os ficheiros JSON de mapeamento de FHIR](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Selecione **"IoT Connector (pré-visualização)"** no lado inferior esquerdo da AZure API para o painel de recursos FHIR na secção **"Add-ins".**

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Selecione o **"Conector"** do qual estará a copiar os ficheiros de mapeamento.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

3. Selecione **"Configure o mapeamento do dispositivo".**

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

> [!NOTE]
> Este processo também pode ser utilizado para copiar/guardar o conteúdo do **"Configure FHIR mapping"** JSON.

4. Selecione o conteúdo do JSON e faça uma operação de cópia (por exemplo: Selecione Ctrl + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Conector IoT" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Faça uma operação de pasta (por exemplo: Selecione Ctrl + v) num novo ficheiro dentro de um editor (por exemplo: Visual Studio Code, Notepad) e guarde o ficheiro com uma extensão *.json.

> [!TIP]
> Se vai abrir um bilhete [de Suporte Técnico Azure](https://azure.microsoft.com/support/create-ticket/) para o Conector IoT, certifique-se de incluir cópias dos seus ficheiros de mapeamento para ajudar no processo de resolução de problemas.

## <a name="next-steps"></a>Passos seguintes

Confira perguntas frequentes sobre o Conector IoT

>[!div class="nextstepaction"]
>[Perguntas de conector IoT](fhir-faq.md#iot-connector-preview)


FHIR é a marca registada do HL7 e é utilizada com a permissão do HL7.