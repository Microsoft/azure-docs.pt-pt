---
title: Padrões de consulta comuns - Gémeos Digitais Azure / Microsoft Docs
description: Aprenda vários padrões comuns de consulta de API para as APIs de gestão de Gémeos Digitais Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 133c0e0dcc07afb85a0f3af9ae51d2207abac293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589118"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Como consultar as APIs de Gémeos Digitais Azure para tarefas comuns

Este artigo mostra padrões de consulta para ajudá-lo a executar cenários comuns para a sua instância De Gémeos Digitais Azure. Isto pressupõe que a sua instância de Gémeos Digitais já está em execução. Pode usar qualquer cliente REST, como o Carteiro. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Consultas para espaços e tipos

Esta secção mostra consultas de amostra para obter mais informações sobre os seus espaços provisionados. Faça pedidos autenticados GET HTTP com as consultas de amostra, substituindo os espaços reservados por valores da sua configuração. 

- Obtenha espaços que são nósos de raiz.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Obtenha um espaço pelo nome e inclua dispositivos, sensores, valores computacionados e valores de sensores. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Obtenha espaços e informações do dispositivo/sensor, cujo progenitor é o ID espacial dado, e que estão nos níveis dois a cinco [em relação ao espaço dado](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Obtenha o espaço com o ID dado e inclua valores computacionados e sensores.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Obtenha chaves de propriedade para um espaço particular.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Obtenha espaços com chave de propriedade chamada *AreaInSqMeters* e o seu valor é de 30. Você também pode fazer operações de cordas, `name = X contains Y`por exemplo, obter espaços que contenham chave de propriedade com .

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Obtenha todos os nomes com nome *Temperatura* e dependências associadas e ontologias.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Consultas para papéis e atribuições de papéis

Esta secção mostra algumas consultas para obter mais informações sobre papéis e suas atribuições. 

- Obtenha todas as funções apoiadas pela Azure Digital Twins.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Obtenha todas as atribuições de papéis na sua instância de Gémeos Digitais. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Obtenha tarefas num caminho específico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Consultas para dispositivos

Esta secção mostra alguns exemplos de como pode utilizar as APIs de Gestão para obter informações específicas sobre os seus dispositivos. Todas as chamadas API precisam de ser autenticadas pedidos GET HTTP.

- Pegue todos os dispositivos.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Encontre todos os estados do dispositivo.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Arranja um dispositivo específico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Adere todos os dispositivos ao espaço radicular.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Colocar todos os dispositivos ligados aos espaços dos níveis 2 a 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Obtenha todos os dispositivos diretamente ligados a um determinado ID espacial.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Adere todos os dispositivos a um determinado espaço e aos seus descendentes.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Adexifique todos os dispositivos aos descendentes de um espaço, excluindo esse espaço.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Adere todos os dispositivos às crianças diretas de um espaço.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Adere todos os dispositivos a um dos ancestrais de um espaço.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Adite todos os dispositivos ligados aos descendentes de um espaço de nível inferior ou igual a 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Adexifique todos os dispositivos ligados a espaços que estejam ao mesmo nível do espaço com *id YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Obtenha a cadeia de ligação do dispositivo IoT Hub para o seu dispositivo.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Obtenha o dispositivo com o ID de hardware dado, incluindo sensores anexados.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Obtenha sensores para determinados tipos de dados, neste caso *Movimento* e *Temperatura*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Consultas para os matchers e funções definidas pelo utilizador 

- Arranja todos os fósforos e as suas identificações.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Obtenha detalhes sobre um determinado matcher, incluindo os espaços e função definida pelo utilizador associado seleções.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Avalie um matcher contra um sensor e ative o abate para fins de depuração. O retorno desta mensagem HTTP GET diz-lhe se o matcher e o sensor pertencem ao tipo de dados. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Obtenha a identificação das funções definidas pelo utilizador. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Obtenha o conteúdo de uma determinada função definida pelo utilizador 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Consultas para utilizadores

Esta secção mostra algumas consultas de API para gerir utilizadores em Azure Digital Twins. Faça um pedido HTTP GET substituindo os espaços reservados por valores da sua configuração. 

- Pegue todos os utilizadores. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Arranja um utilizador específico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Passos seguintes

Para aprender a autenticar com a sua API de Gestão, leia [Autenticação com APIs](./security-authenticating-apis.md).

Para saber mais sobre os seus pontos finais da API, leia [Como usar Gémeos Digitais Swagger](./how-to-use-swagger.md).
