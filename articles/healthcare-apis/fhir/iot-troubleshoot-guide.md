---
title: Conector Azure IoT para FHIR (pré-visualização) - Guia de resolução de problemas e como fazer
description: Como resolver problemas comum do Conector IoT do FHIR (pré-visualização) mensagens e condições de erro e cópia de ficheiros de mapeamento
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 3eef7354f7197f60e8abd1b5522393bf00a6203f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020591"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Conector Azure IoT para guia de resolução de problemas FHIR (pré-visualização)

Este artigo fornece passos para a resolução de problemas comum do Conector Azure IoT para Recursos de Interoperabilidade de Cuidados rápidos de Saúde (FHIR&#174;)* mensagens e condições de erro.  

Também aprenderá a criar cópias do Conector Azure IoT para mapeamentos de conversão de FHIR JSON (por exemplo: Dispositivo e FHIR).  

Pode utilizar as cópias JSON de mapeamento de conversão para edição e arquivamento fora do portal Azure.  

> [!TIP]
> Se vai abrir um bilhete [de Suporte Técnico Azure](https://azure.microsoft.com/support/create-ticket/) para o Conector Azure IoT para FHIR, certifique-se de incluir cópias do seu mapeamento de conversão JSON para ajudar no processo de resolução de problemas.

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>Validações do modelo JSON de Mapeamento de Dispositivo e FHIR para Conector Azure IoT para FHIR (pré-visualização)
Nesta secção, você vai aprender sobre o processo de validação que O Conector Azure IoT para FHIR executa para validar os modelos de mapeamento de JSON de conversão de dispositivo e FHIR antes de permitir que sejam guardados para uso.  Estes elementos são necessários no Mapeamento de Conversão de Dispositivo e FHIR JSON.

**Mapeamento de dispositivos**

|Elemento|Necessário|
|:-------|:------|
|Nome tipo|Verdadeiro|
|TipoMatchExpression|Verdadeiro|
|DeviceIdExpression|Verdadeiro|
|TempotampExpressão|Verdadeiro|
|Valores[]. ValorName|Verdadeiro|
|Valores[]. ValueExpression|Verdadeiro|

> [!NOTE]
> Valores[]. ValorName e Valores[]. ValueExpression
>
> Estes elementos só são necessários se tiver uma entrada de valor na matriz - é válido não ter valores mapeados. Isto é usado quando a telemetria que está a ser enviada é um evento. Por exemplo: Quando um dispositivo IoMT wearable é colocado ou removido. Os elementos(s) não têm quaisquer valores exceto um nome que Azure IoT Connector para partidas fHIR e emite. Na conversão FHIR, o Conector Azure IoT para FHIR mapeia-o para um conceito codificante baseado no tipo semântico - não são povoados valores reais.

**Mapeamento FHIR**

|Elemento|Necessário|
|:------|:-------|
|Nome tipo|Verdadeiro|

> [!NOTE]
> Este é o único elemento de mapeamento FHIR necessário validado neste momento.

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>Mensagens de erro e correções para O Conector Azure IoT para FHIR (pré-visualização)

|Mensagem|Exibido|Condição|Correção| 
|-------|---------|---------|---|
|Nome de mapeamento inválido, nome de mapeamento deve ser dispositivo ou FHIR.|API|O tipo de mapeamento fornecido não é dispositivo ou FHIR.|Utilize um dos dois tipos de mapeamento suportados (por exemplo: Dispositivo ou FHIR).|
|A validação falhou. A informação requerida está em falta ou não é válida.|Portal API e Azure|Tentar salvar uma conversão mapeamento em falta de informação ou elemento necessário.|Adicione informações ou elementos de mapeamento de conversão em falta e tente guardar novamente o mapeamento de conversão.|
|Regenerar parâmetros-chave não definidos.|API|Regenerar o pedido de chave.|Incluir os parâmetros no pedido chave de regeneração.|
|Atingiu o número máximo de instâncias do Conector IoT que podem ser a provisionadas nesta subscrição.|Portal API e Azure|Azure IoT Connector para quota de subscrição FHIR alcançado (Predefinido é (2) por subscrição).|Elimine um dos casos existentes de Conector Azure IoT para FHIR.  Use uma subscrição diferente que não tenha atingido a quota de subscrição.  Solicite um aumento da quota de subscrição.|
|O recurso move não é suportado para o IoT Connector ativado Azure API para o recurso FHIR.|Portal API e Azure|Tentar fazer uma operação de movimento numa API Azure para recurso FHIR que tenha uma ou mais instâncias do Conector Azure IoT para FHIR.|Eliminar os casos existentes do Conector Azure IoT para o FHIR fazer a operação de movimento.|
|IoT Connector não provisido.|API|A tentativa de utilização de serviços infantis (ligações & mapeamentos) quando o progenitor (Azure IoT Connector for FHIR) não foi a provisionado.|Provisionar um Conector Azure IoT para FHIR.|
|O pedido não é apoiado.|API|O pedido específico da API não é apoiado.|Use o pedido de API correto.|
|A conta não existe.|API|Não existe uma tentativa de adicionar um Conector Azure IoT para FHIR e a API Azure para recurso FHIR.|Crie a AZure API para o recurso FHIR e, em seguida, re-apropte a operação.|
|A Azure API para a versão FHIR de recursos FHIR não é suportada para o Conector IoT.|API|Tentar utilizar um Conector Azure IoT para FHIR com uma versão incompatível da API Azure para recurso FHIR.|Crie um novo API Azure para recurso FHIR (versão R4) ou utilize um API Azure existente para recurso FHIR (versão R4).

## <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Porque é que o meu Conector Azure IoT para os dados FHIR (pré-visualização) não aparece na Azure API para o FHIR?

|Questões potenciais|Correções|
|----------------|-----|
|Os dados ainda estão a ser tratados.|Os dados são divulgados à Azure API para FHIR em lotes (a cada ~15 minutos).  É possível que os dados ainda estejam a ser tratados e seja necessário um tempo adicional para que os dados sejam persistidos na API Azure para fHIR.|
|O mapeamento da conversão do dispositivo JSON não foi configurado.|Configure e guarde o mapeamento de conversão do dispositivo em conformidade JSON.|
|O mapeamento de conversão FHIR JSON não foi configurado.|Configure e guarde o mapeamento de conversão FHIR em conformidade com o JSON.|
|A mensagem do dispositivo não contém uma expressão esperada definida no mapeamento do dispositivo.|Verifique as expressões JsonPath definidas nos tokens de correspondência de mapeamento do dispositivo definidos na mensagem do dispositivo.|
|Não foi criado um recurso de dispositivo na API Azure para FHIR (Tipo de Resolução: Apenas para procurar)*.|Crie um recurso de dispositivo válido na API Azure para FHIR. Certifique-se de que o Recurso do Dispositivo contém um identificador que corresponde ao identificador do dispositivo fornecido na mensagem de entrada.|
|Não foi criado um recurso para o doente na API Azure para FHIR (Tipo de Resolução: Apenas para procurar)*.|Crie um Recurso de Paciente válido na API Azure para FHIR.|
|A referência dispositivo.paciente não está definida, ou a referência é inválida (Tipo de Resolução: Apenas procurar)*.|Certifique-se de que o Recurso do Dispositivo contém uma [referência](https://www.hl7.org/fhir/device-definitions.html#Device.patient) válida a um recurso do paciente.| 

*Início rápido de [referência: Implementar o Conector Azure IoT (pré-visualização) utilizando o portal Azure](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) para uma descrição funcional do Conector Azure IoT para tipos de resolução FHIR (por exemplo: Procurar ou Criar).

## <a name="use-metrics-to-troubleshoot-issues-in-azure-iot-connector-for-fhir-preview"></a>Utilize métricas para resolver problemas no Conector Azure IoT para FHIR (pré-visualização)

O Conector Azure IoT para FHIR gera várias métricas para fornecer informações sobre o processo de fluxo de dados. Uma das métricas suportadas chama-se *Erro* Total, que fornece a contagem de todos os erros que ocorrem num caso de Conector Azure IoT para FHIR.

Cada erro é registado com uma série de propriedades associadas. Cada propriedade fornece um aspeto diferente sobre o erro, o que pode ajudá-lo a identificar e resolver problemas. Esta secção lista diferentes propriedades capturadas para cada erro na métrica *Erros Totais,* e possíveis valores para estas propriedades.

> [!NOTE]
> Pode navegar para a métrica *de Erros Totais* para um exemplo de Conector Azure IoT para FHIR (pré-visualização) conforme descrito na [página de métricas Azure IoT para FHIR (pré-visualização).](iot-metrics-display.md)

Clique no gráfico *De Erros Totais* e, em seguida, clique no botão *de filtro Adicionar* para cortar e picar a métrica de erro usando qualquer uma das propriedades mencionadas abaixo.

### <a name="the-operation-performed-by-the-azure-iot-connector-for-fhir-preview"></a>A operação realizada pelo Conector Azure IoT para FHIR (pré-visualização)

Esta propriedade representa a operação que está a ser realizada pelo IoT Connector quando ocorreu o erro. Uma operação geralmente representa a fase de fluxo de dados durante o processamento de uma mensagem do dispositivo. Aqui está a lista de valores possíveis para este imóvel.

> [!NOTE]
> Pode ler mais sobre diferentes fases do fluxo de dados no Conector Azure IoT para FHIR (pré-visualização) [aqui](iot-data-flow.md).

|Fase de fluxo de dados|Description|
|---------------|-----------|
|Configuração|Operação específica para configurar o seu caso de Conector IoT|
|Normalização|Fase de fluxo de dados onde os dados do dispositivo são normalizados|
|Agrupamento|Fase de fluxo de dados onde os dados normalizados são agrupados|
|FHIRConversão|Fase de fluxo de dados onde os dados normalizados agrupados são transformados num recurso FHIR|
|Desconhecido|O tipo de operação é desconhecido quando ocorreu um erro|

### <a name="the-severity-of-the-error"></a>A gravidade do erro

Esta propriedade representa a gravidade do erro ocorrido. Aqui está a lista de valores possíveis para este imóvel.

|Gravidade|Descrição|
|---------------|-----------|
|Aviso|Existe um pequeno problema no processo de fluxo de dados, mas o processamento da mensagem do dispositivo não para|
|Erro|O processamento de uma mensagem específica do dispositivo desagravou-se e outras mensagens podem continuar a ser executadas como esperado.|
|Crítico|Existe algum problema de nível do sistema com o Conector IoT e não se espera que as mensagens sejam processadas|

### <a name="the-type-of-the-error"></a>O tipo de erro

Esta propriedade significa uma categoria para um dado erro, que basicamente representa um agrupamento lógico para tipo de erros semelhantes. Aqui está a lista de possíveis valor para este imóvel.

|Tipo de erro|Description|
|----------|-----------|
|DeviceTemplateError|Erros relacionados com modelos de mapeamento de dispositivos|
|DeviceMessageError|Ocorreram erros ao processar uma mensagem específica do dispositivo|
|FHIRTemplateError|Erros relacionados com modelos de mapeamento FHIR|
|FHIRConversionError|Erros ocorreram ao transformar uma mensagem num recurso FHIR|
|FHIRResourceError|Erros relacionados com os recursos existentes no servidor FHIR que são referenciados pelo Conector IoT|
|FHIRServerError|Erros que ocorrem ao comunicar com o servidor FHIR|
|GeneralError|Todos os outros tipos de erros|

### <a name="the-name-of-the-error"></a>O nome do erro

Esta propriedade fornece o nome para um erro específico. Aqui está a lista de todos os nomes de erro com a sua descrição e tipo de erro associado, gravidade e fase de fluxo de dados.

|Nome de erro|Description|Tipo de erro(s)|Gravidade do erro|Fase de fluxo de dados|
|----------|-----------|-------------|--------------|------------------|
|MultipleResourceFoundException|Erro ocorreu quando vários recursos do paciente ou dispositivo são encontrados no servidor FHIR para os respetivos identificadores presentes na mensagem do dispositivo|FHIRResourceError|Erro|FHIRConversão|
|ModeloNotFoundException|Um dispositivo ou modelo de mapeamento FHIR não está configurado com a instância do Conector IoT|DeviceTemplateError, FHIRTemplateError|Crítico|Normalização, FHIRConversão|
|CorrelaIdNotDefinedExcepção|O ID de correlação não é especificado no modelo de mapeamento do dispositivo. CorrelationIdNotDefinedExcepção é um erro condicional que ocorreria apenas quando a observação de FHIR deve agrupar as medições do dispositivo usando um ID de correlação, mas não está configurado corretamente|DeviceMessageError|Erro|Normalização|
|PacienteDeviceMismatchExcepção|Este erro ocorre quando o recurso do dispositivo no servidor FHIR tem uma referência a um recurso do paciente, que não corresponde ao identificador do paciente presente na mensagem|FHIRResourceError|Erro|FHIRConversionError|
|Experiência PacienteNotFoundException|Nenhum recurso FHIR do paciente é referenciado pelo recurso FHIR do dispositivo associado ao identificador do dispositivo presente na mensagem do dispositivo. Note que este erro só ocorrerá quando a instância do Conector IoT estiver configurada com o tipo de resolução *Lookup*|FHIRConversionError|Erro|FHIRConversão|
|DeviceNotFoundException|Não existe qualquer recurso de dispositivo no Servidor FHIR associado ao identificador do dispositivo presente na mensagem do dispositivo|DeviceMessageError|Erro|Normalização|
|PatientIdentityNotDefinedException|Este erro ocorre quando a expressão para analisar o identificador do paciente a partir da mensagem do dispositivo não está configurada no modelo de mapeamento do dispositivo ou o identificador do paciente não está presente na mensagem do dispositivo. Note que este erro ocorre apenas quando o tipo de resolução do Conector IoT é definido para *Criar*|DeviceTemplateError|Crítico|Normalização|
|DeviceIdentityNotDefinedException|Este erro ocorre quando a expressão para o identificador do dispositivo de análise a partir da mensagem do dispositivo não está configurada no modelo de mapeamento do dispositivo ou no identificador do dispositivo não está presente na mensagem do dispositivo|DeviceTemplateError|Crítico|Normalização|
|NotSupportedException|Erro ocorreu quando a mensagem do dispositivo com formato não suportado é recebida|DeviceMessageError|Erro|Normalização|

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>Criação de cópias do Conector Azure IoT para mapeamento de conversão JSON (pré-visualização) de FHIR (pré-visualização)

A cópia do Conector Azure IoT para ficheiros de mapeamento FHIR pode ser útil para edição e arquivamento fora do site do portal Azure.

As cópias do ficheiro de mapeamento devem ser fornecidas ao Suporte Técnico do Azure ao abrir um bilhete de apoio para ajudar na resolução de problemas.

> [!NOTE]
> JSON é o único formato suportado para ficheiros de mapeamento de Dispositivos e FHIR neste momento.

> [!TIP]
> Saiba mais sobre o Conector Azure IoT para dispositivo FHIR [e mapeamento de conversão FHIR JSON](iot-mapping-templates.md)

1. Selecione **"IoT Connector (pré-visualização)"** no lado inferior esquerdo da AZure API para o painel de recursos FHIR na secção **"Add-ins".**

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Conector IoT1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Selecione o **"Conector"** do qual estará a copiar o mapeamento de conversão JSON.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Conector IoT2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Este processo também pode ser utilizado para copiar e guardar o conteúdo do **"Configure FHIR mapping"** JSON.

3. Selecione **"Configure o mapeamento do dispositivo".**

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Conector IoT3" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Selecione o conteúdo do JSON e faça uma operação de cópia (por exemplo: Selecione Ctrl + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Conector IoT4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Faça uma operação de pasta (por exemplo: Selecione Ctrl + v) num novo ficheiro dentro de um editor (por exemplo: Visual Studio Code, Notepad) e guarde o ficheiro com uma extensão *.json.

> [!TIP]
> Se vai abrir um bilhete [de Suporte Técnico Azure](https://azure.microsoft.com/support/create-ticket/) para o Conector Azure IoT para FHIR, certifique-se de incluir cópias do seu mapeamento de conversão JSON para ajudar no processo de resolução de problemas.

## <a name="next-steps"></a>Passos seguintes

Confira frequentemente perguntas sobre o Conector Azure IoT para FHIR.

>[!div class="nextstepaction"]
>[Conector Azure IoT para FAQs FHIR](fhir-faq.md)

*No portal Azure, o Conector Azure IoT para FHIR é referido como Conector IoT (pré-visualização). FHIR é uma marca registada do HL7 e é usada com a permissão do HL7.