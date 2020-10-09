---
title: Adicione dados aos registos de auditoria utilizando cabeçalhos personalizados - Azure API para FHIR
description: Este artigo descreve como adicionar dados a registos de auditoria utilizando cabeçalhos HTTP personalizados na Azure API para FHIR.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: 937be72bfec96119474e7effe9ba88a2cf253444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86081848"
---
# <a name="add-data-to-audit-logs-by-using-custom-http-headers"></a>Adicione dados aos registos de auditoria utilizando cabeçalhos HTTP personalizados

Na API Azure Fast Healthcare Interoperability Resources (FHIR), um utilizador pode querer incluir informações adicionais nos registos, que vêm do sistema de chamadas.

Por exemplo, quando o utilizador da API é autenticado por um sistema externo, esse sistema reencaminha a chamada para a API FHIR. Na camada FHIR API, a informação sobre o utilizador original foi perdida, porque a chamada foi reencaminhada. Pode ser necessário registar e reter estas informações do utilizador para fins de auditoria ou gestão. O sistema de chamadas pode fornecer a identidade do utilizador, a localização do chamador ou outras informações necessárias nos cabeçalhos HTTP, que serão transportados à medida que a chamada for reencaminhada.

Pode ver este fluxo de dados no seguinte diagrama:

:::image type="content" source="media/custom-headers/custom-headers-diagram.png" alt-text="Diagrama de cabeçalhos personalizados&quot;:::

Pode utilizar cabeçalhos personalizados para capturar vários tipos de informação. Por exemplo:

* Informações de identidade ou autorização
* Origem do chamador
* Organização originária
* Detalhes do sistema do cliente (registo eletrónico de saúde, portal do paciente)

> [!IMPORTANT]
> Esteja ciente de que as informações enviadas em cabeçalhos personalizados são armazenadas num sistema de registo interno da Microsoft durante 30 dias após estarem disponíveis no Azure Log Monitoring. Recomendamos encriptar qualquer informação antes de adicioná-la a cabeçalhos personalizados. Não deve passar nenhuma informação phi através de cabeçalhos de cliente.

Deve utilizar a seguinte convenção de nomeação para os seus cabeçalhos HTTP: X-MS-AZUREFHIR-AUDIT- \<name> .

Estes cabeçalhos HTTP estão incluídos num saco de propriedade que é adicionado ao registo. Por exemplo:

* X-MS-AZUREFHIR-AUDIT-USERID: 1234 
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: XXXX
* X-MS-AZUREFHIR-AUDIT-XYZ: 1234

Esta informação é então serializada para JSON quando é adicionada à coluna de propriedades no registo. Por exemplo:

```json
{ &quot;X-MS-AZUREFHIR-AUDIT-USERID&quot; : &quot;1234&quot;,
&quot;X-MS-AZUREFHIR-AUDIT-USERLOCATION&quot; : &quot;XXXX&quot;,
&quot;X-MS-AZUREFHIR-AUDIT-XYZ&quot; : &quot;1234&quot; }
```
 
Como em qualquer cabeçalho HTTP, o mesmo nome do cabeçalho pode ser repetido com valores diferentes. Por exemplo:

* X-MS-AZUREFHIR-AUDIT-USERLOCATION: HospitalA
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: Emergência

Quando adicionados ao registo, os valores são combinados com uma lista delimitada de vírgula. Por exemplo:

{ &quot;X-MS-AZUREFHIR-AUDIT-USERLOCATION&quot; : &quot;Hospitala, Emergência" }
 
Pode adicionar um máximo de 10 cabeçalhos únicos (repetições do mesmo cabeçalho com valores diferentes são contadas apenas como um). O comprimento máximo total do valor para um cabeçalho é de 2048 caracteres.

Se estiver a usar a biblioteca API do cliente Firefly C#, o código é mais ou menos assim:

```C#
FhirClient client;
client = new FhirClient(serverUrl);
client.OnBeforeRequest += (object sender, BeforeRequestEventArgs e) =>
{
    // Add custom headers to be added to the logs
    e.RawRequest.Headers.Add("X-MS-AZUREFHIR-AUDIT-UserLocation", "HospitalA");
};
client.Get("Patient");
```
## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a adicionar dados a registos de auditoria utilizando cabeçalhos personalizados na API Azure para FHIR. Em seguida, saiba mais sobre outras configurações adicionais que pode configurar na API Azure para FHIR.
 
>[!div class="nextstepaction"]
>[Definições adicionais](azure-api-for-fhir-additional-settings.md)
