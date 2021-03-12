---
title: Configure a partilha de recursos de origem cruzada na AZure API para o FHIR
description: Este artigo descreve como configurar a partilha de recursos de origem cruzada na Azure API para fHIR.
author: matjazl
ms.author: matjazl
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: dc4c034b0821f1fe5ecb940591fca77d61edc3ce
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019890"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>Configure a partilha de recursos de origem cruzada na AZure API para o FHIR

A Azure API for Fast Healthcare Interoperability Resources (FHIR) suporta [a partilha de recursos de origem cruzada (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing). O CORS permite-lhe configurar definições para que as aplicações de um domínio (origem) possam aceder a recursos de um domínio diferente, conhecido como um pedido de domínio transversal.

O CORS é frequentemente utilizado numa aplicação de uma página que deve chamar uma API RESTful a um domínio diferente.

Para configurar uma definição DE CORS na API Azure para FHIR, especifique as seguintes definições:

- **Origens (Acesso-Controlo-Permitir-Origem)**. Uma lista de domínios autorizados a fazer pedidos de origem cruzada à AZure API para o FHIR. Cada domínio (origem) deve ser introduzido numa linha separada. Pode introduzir um asterisco (*) para permitir chamadas de qualquer domínio, mas não recomendamos porque é um risco de segurança.

- **Cabeçalhos (Acesso-Control-Allow-Headers)**. Uma lista de cabeçalhos que o pedido de origem irá conter. Para permitir todos os cabeçalhos, introduza um asterisco (*).

- **Métodos (Acesso-Control-Allow-Methods)**. Os métodos permitidos (PUT, GET, POST, e assim por diante) numa chamada da API. Escolha **Selecione tudo** para todos os métodos.

- **Idade máxima (Acesso-Control-Max-Age)**. O valor em segundos para cache pré-voo resultados de pedido de acesso-controle-permitir-cabeçalhos e acesso-controlo-métodos.

- **Permitir credenciais (Acesso-Controlo-Permitir-Credenciais)**. Os pedidos do CORS normalmente não incluem cookies para evitar ataques [de falsificação de pedidos de trans-sites (CSRF).](https://en.wikipedia.org/wiki/Cross-site_request_forgery) Se selecionar esta definição, o pedido pode ser feito para incluir credenciais, como cookies. Não é possível configurar esta definição se já definir Origens com um asterisco (*).

![Definições de partilha de recursos de origem cruzada (CORS)](media/cors/cors.png)

>[!NOTE]
>Não é possível especificar diferentes configurações para diferentes origens de domínio. Todas as definições **(Cabeçalhos**, **Métodos,** **Idade Máxima** e Permitir **credenciais**) aplicam-se a todas as origens especificadas na definição Origens.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar a partilha de origem cruzada na Azure API para fHIR. Em seguida, implementar uma Azure API totalmente gerida para FHIR:
 
>[!div class="nextstepaction"]
>[Implementar o Azure API for FHIR](fhir-paas-portal-quickstart.md)