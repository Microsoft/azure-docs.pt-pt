---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: f79081d506db6225b9ebef25674aad136e342ecf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467787"
---
Para os dados em voo:

- A Standard Transport Layer Security (TLS) 1.2 é utilizada para dados que viajam entre o dispositivo e o Azure. Não há recuo para tLS 1.1 e mais cedo. A comunicação do agente será bloqueada se o TLS 1.2 não for suportado. TLS 1.2 também é necessário para a gestão do portal e da SDK.
- Quando os clientes acedem ao seu dispositivo através da UI web local de um browser, o padrão TLS 1.2 é usado como o protocolo de segurança predefinido.

  - A melhor prática é configurar o seu navegador para usar o TLS 1.2.
  - O seu dispositivo suporta apenas o TLS 1.2 e não suporta versões mais antigas TLS 1.1 ou TLS 1.0.
- Recomendamos que utilize o SMB 3.0 com encriptação para proteger os dados quando os copiar dos seus servidores de dados.
