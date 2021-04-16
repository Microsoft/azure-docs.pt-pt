---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 6ed30ed2333393e9d8c0222500aaf3f17da6d33d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531640"
---
* **Um endereço [IPv4 público](../articles/virtual-network/public-ip-addresses.md#standard)Standard SKU,** se o seu cliente utilizar a versão API 2021-01-01 ou posterior. O recurso de endereço IP público é necessário ao configurar a rede virtual para acesso externo ou interno. Com uma rede virtual interna, o endereço IP público é utilizado apenas para operações de gestão. Saiba mais sobre [endereços IP da API Management.](../articles/api-management/api-management-howto-ip-addresses.md)

  * O endereço IP deve estar na mesma região e subscrição que a instância de Gestão da API e a rede virtual.

  * O valor do endereço IP é atribuído como o endereço IPv4 público virtual da instância de Gestão da API naquela região. 

  * Ao mudar de uma rede virtual externa para interna (ou vice-versa), alterar sub-redes na rede ou atualizar zonas de disponibilidade para a instância de Gestão da API, deve configurar um endereço IP público diferente. 

  > [!IMPORTANT]
  > Atualmente, o portal Azure utiliza a versão API 2021-01-01 ao criar ou atualizar uma instância de Gestão da API. Pode especificar esta versão API utilizando um modelo de Gestor de Recursos Azure ou a API Management REST API. O Azure CLI e a Azure PowerShell suportam atualmente a versão API 2020-12-01.
