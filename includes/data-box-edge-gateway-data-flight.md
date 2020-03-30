---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184896"
---
Para dados em voo:

- O Standard TLS 1.2 é utilizado para dados que viajam entre o dispositivo e o Azure. Não há recuo para tLS 1.1 e mais cedo. A comunicação do agente será bloqueada se o TLS 1.2 não for suportado. TLS 1.2 também é necessário para gestão de portal e SDK.
- Quando os clientes acedem ao seu dispositivo através da UI web local de um navegador, o TLS 1.2 padrão é usado como o protocolo de segurança padrão.

    - A melhor prática é configurar o seu navegador para utilizar o TLS 1.2.
    - Se o navegador não suportar TLS 1.2, pode utilizar TLS 1.1 ou TLS 1.0.
- Recomendamos que utilize o SMB 3.0 com encriptação para proteger os dados quando os copia dos seus servidores de dados.
