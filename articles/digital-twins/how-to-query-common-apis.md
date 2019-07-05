---
title: Azure padrões de consulta de comuns de outros duplos Digital | Documentos da Microsoft
description: Saiba os padrões comuns de consultar as APIs de gestão de duplos Digital do Azure.
author: kingdomofends
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: v-adgera
ms.openlocfilehash: 57abb005f70661d23ea90eaf801b8e5407340bb7
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462039"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Como consultar APIs de duplos Digital do Azure para tarefas comuns

Este artigo mostra os padrões de consulta para o ajudar a executar cenários comuns para a sua instância de duplos Digital do Azure. Isso assume que sua instância de duplos Digital já está em execução. Pode utilizar qualquer cliente REST, como o Postman. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Consultas para espaços e tipos

Esta secção mostra exemplos de consultas para obter mais informações sobre os seus espaços aprovisionados. Verifique os pedidos GET HTTP autenticados com as consultas de exemplo, substitua os marcadores de posição pelos valores do seu programa de configuração. 

- Obtenha espaços que são nós raiz.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Para obter um espaço de nome e incluir dispositivos, sensores, valores computados e valores de sensor. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Obter espaços e suas informações de dispositivo/sensor, cujo principal é o ID de determinado espaço e que estão em níveis dois a cinco [relativo ao espaço de determinado](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Obter o espaço com o ID fornecido e incluir calculada e os valores de sensor.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Obter chaves de propriedade para um determinado espaço.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Obter espaços com a chave de propriedade com o nome *AreaInSqMeters* e seu valor é 30. Pode também operações de cadeias de caracteres, por exemplo, get espaços de chave de propriedade que contém com `name = X contains Y`.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Obter todos os nomes com o nome *temperatura* dependências e ontologies associados.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Consultas para funções e as atribuições de funções

Esta secção mostra algumas consultas para obter mais informações sobre funções e as respetivas atribuições. 

- Obtenha todas as funções suportadas pelo duplos Digital do Azure.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Obtenha todas as atribuições de função na sua instância de duplos Digital. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Obter atribuições de funções num caminho específico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Consultas para dispositivos

Esta secção mostra alguns exemplos de como pode usar as APIs de gestão para obter informações específicas sobre os seus dispositivos. Todas as chamadas de API tem de ser autenticados pedidos de HTTP de obter.

- Obter todos os dispositivos.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Encontre todos os Estados do dispositivo.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Obtenha um dispositivo específico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Obter todos os dispositivos ligados ao espaço de raiz.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Obter todos os dispositivos ligados a espaços em níveis 2 a 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Obter todos os dispositivos ligados diretamente a um ID de espaço em particular.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Obter todos os dispositivos ligados a um determinado espaço e respetivos descendentes.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Obter todos os dispositivos ligados a descendentes de um espaço, excluindo esse espaço.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Obter todos os dispositivos ligados a filhos diretos de um espaço.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Obter todos os dispositivos ligados a um de predecessores de um espaço.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Obter todos os dispositivos ligados a descendentes de um espaço de nível inferior ou igual a 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Obter todos os dispositivos ligados a espaços que estejam no mesmo nível, como o espaço com o ID *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Obter a cadeia de ligação de dispositivos do IoT Hub para o seu dispositivo.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Obtenha o dispositivo com o ID de hardware específico, incluindo sensores anexados.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Obter sensores para tipos de dados específico, neste caso *movimento* e *temperatura*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Consultas para matchers e funções definidas pelo utilizador 

- Obtenha tudo aprovisionados matchers e os respetivos IDs.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Obtenha detalhes sobre uma determinado na ferramenta de correspondência, incluindo os espaços e a função definida pelo utilizador associado ao mesmo.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Avalie na ferramenta de correspondência em relação a um sensor e ativar o registo para fins de depuração. O retorno desta mensagem HTTP GET indica se na ferramenta de correspondência e o sensor de pertencer ao tipo de dados. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Obtenha o ID das funções definidas pelo utilizador. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Obter o conteúdo de uma função definida pelo utilizador específico 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Consultas para os utilizadores

Esta secção mostra alguns exemplos de consultas de API para a gestão de utilizadores no duplos Digital do Azure. Fazer um pedido HTTP GET, substitua os marcadores de posição pelos valores do seu programa de configuração. 

- Obter todos os utilizadores. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Obter um utilizador específico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Passos Seguintes

Para saber como autenticar com a API de gestão, leia [autenticar com as APIs](./security-authenticating-apis.md).

Para ver todos os pontos finais da API, leia [como utilizar o Swagger de duplos Digital](./how-to-use-swagger.md).
