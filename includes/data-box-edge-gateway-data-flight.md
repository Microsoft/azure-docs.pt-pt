---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67184896"
---
Para os dados em voo:

- O Standard TLS 1.2 é utilizado para dados que viajam entre o dispositivo e o Azure. Não há recuo para tLS 1.1 e mais cedo. A comunicação do agente será bloqueada se o TLS 1.2 não for suportado. TLS 1.2 também é necessário para a gestão do portal e da SDK.
- Quando os clientes acedem ao seu dispositivo através da UI web local de um browser, o padrão TLS 1.2 é usado como o protocolo de segurança predefinido.

    - A melhor prática é configurar o seu navegador para usar o TLS 1.2.
    - Se o navegador não suportar TLS 1.2, pode utilizar TLS 1.1 ou TLS 1.0.
- Recomendamos que utilize o SMB 3.0 com encriptação para proteger os dados quando os copiar dos seus servidores de dados.
