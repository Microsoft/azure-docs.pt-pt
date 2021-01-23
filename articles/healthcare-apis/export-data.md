---
title: Execução da exportação invocando $export comando da Azure API para fHIR
description: Este artigo descreve como exportar dados de FHIR usando $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 48dbd0892c9ec02f203edba55d1104f1ab0118a8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737613"
---
# <a name="how-to-export-fhir-data"></a>Como exportar dados do FHIR


A funcionalidade de exportação a granel permite que os dados sejam exportados do Servidor FHIR de acordo com a [especificação FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Antes de utilizar $export, deverá certificar-se de que a API Azure para FHIR está configurada para a utilizar. Para configurar as definições de exportação e criar a conta de armazenamento Azure, consulte [a página de dados de exportação configurada](configure-export-data.md).

## <a name="using-export-command"></a>Usando o comando $export

Depois de configurar a API Azure para a exportação, pode utilizar o comando $export para exportar os dados para fora do serviço. Os dados serão armazenados na conta de armazenamento que especificou enquanto configura a exportação. Para aprender a invocar $export comando no servidor FHIR, leia a documentação sobre a [especificação de $export HL7 FHIR](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

A Azure API For FHIR suporta $export nos seguintes níveis:
* [Sistema:](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export)`GET https://<<FHIR service base URL>>/$export>>`
* [Paciente:](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients)`GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Grupo de doentes*](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) - A AZure API para a FHIR exporta todos os recursos conexos, mas não exporta as características do grupo: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`



> [!Note] 
> `Patient/$export` e `Group/[ID]/$export` pode exportar recursos duplicados se o recurso estiver num compartimento de mais de um recurso, ou se estiver em vários grupos.

Além disso, é apoiado o controlo do estado de exportação através do URL devolvido pelo cabeçalho de localização durante a fila, bem como o cancelamento do emprego de exportação efetivo.

## <a name="settings-and-parameters"></a>Definições e parâmetros

### <a name="headers"></a>Cabeçalhos
Há dois parâmetros de cabeça exigidos que devem ser definidos para $export postos de trabalho. Os valores são definidos pela [especificação atual $export](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers).
* **Aceitar** - candidatura/fhir+json
* **Prefere** - responder-async

### <a name="query-parameters"></a>Parâmetros de consulta
A Azure API para FHIR suporta os seguintes parâmetros de consulta. Todos estes parâmetros são opcionais:
|Parâmetro de consulta        | Definido pela Especificação FHIR?    |  Descrição|
|------------------------|---|------------|
| \_outputFormat | Sim | Atualmente suporta três valores para alinhar com a FHIR Spec: application/fhir+ndjson, application/ndjson, ou just ndjson. Todos os postos de trabalho de exportação regressarão `ndjson` e o valor passado não tem qualquer efeito sobre o comportamento do código. |
| \_desde | Sim | Permite-lhe apenas exportar recursos que foram modificados desde o tempo fornecido |
| \_tipo | Sim | Permite especificar quais tipos de recursos serão incluídos. Por exemplo, \_ type=Paciente devolveria apenas recursos do paciente|
| \_typefilter | Sim | Para solicitar uma filtragem de grãos mais finos, pode utilizar \_ o typefilter juntamente com o parâmetro do \_ tipo. O valor do parâmetro _typeFilter é uma lista separada por vírgula de consultas de FHIR que restringem ainda mais os resultados |
| \_recipiente | Não |  Especifica o contentor dentro da conta de armazenamento configurada onde os dados devem ser exportados. Se um recipiente for especificado, os dados serão exportados para esse recipiente numa nova pasta com o nome. Se o recipiente não for especificado, será exportado para um novo recipiente utilizando o tempotando e a identificação do trabalho. |


## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu a exportar recursos FHIR usando $export comando. Em seguida, saiba como exportar dados deses identificados:
 
>[!div class="nextstepaction"]
>[Exportação de dados não identificados](de-identified-export.md)
