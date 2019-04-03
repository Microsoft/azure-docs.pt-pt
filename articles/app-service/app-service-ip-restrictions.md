---
title: Restringir o cliente IPs - serviço de aplicações do Azure | Documentos da Microsoft
description: Como utilizar as restrições de acesso com o serviço de aplicações do Azure
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 07/30/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 805de614246028bc75268e83991fa7831b990325
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882332"
---
# <a name="azure-app-service-static-access-restrictions"></a>Restrições de acesso estático do serviço de aplicações do Azure #

Restrições de acesso permitem-lhe definir uma prioridade ordenadas permitir/recusar a lista de endereços IP que têm permissão para aceder à sua aplicação. Lista de permissões pode incluir endereços IPv4 e IPv6. Quando há uma ou mais entradas, existe, em seguida, um implícita Negar tudo o que existe no final da lista.

A capacidade de restrições de acesso funciona com todos os que serviço de aplicações alojadas cargas de trabalho, que incluem; aplicações Web, aplicações API, as aplicações do Linux, aplicações de contentor do Linux e as funções.

Quando é efetuado um pedido à sua aplicação, o endereço de IP é avaliado relativamente a lista de restrições de acesso. Se o endereço não é permitido o acesso com base nas regras na lista, o serviço responde com uma [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) código de estado.

A capacidade de restrições de acesso é implementada nas funções de front-end de serviço de aplicações, que são a montante, os anfitriões de trabalho em que o seu código é executado. Por conseguinte, restrições de acesso são, efetivamente, ACLs de rede.  

![fluxo de restrições de acesso](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Por um período, a capacidade de restrições de acesso no portal do era uma camada sobre a capacidade de ipSecurity no IIS. A capacidade de restrições de acesso atual é diferente. Ainda é possível configurar ipSecurity dentro de sua Web. config do aplicativo, mas as regras de restrições de acesso baseada em front-end serão aplicadas antes que qualquer tráfego atinja o IIS.

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Adicionar e editar regras de restrição de acesso no portal ##

Para adicionar uma regra de restrição de acesso à sua aplicação, utilize o menu para abrir **rede**>**restrições de acesso** e clique em **configurar restrições de acesso**

![Opções de rede do serviço de aplicações](media/app-service-ip-restrictions/ip-restrictions.png)  

Desde a interface do Usuário de restrições de acesso, pode rever a lista de regras de restrição de acesso definidas para a sua aplicação.

![restrições de acesso de lista](media/app-service-ip-restrictions/ip-restrictions-browse.png)

Se as regras foram configuradas conforme esta imagem, a aplicação só seria aceitar o tráfego de 131.107.159.0/24 e poderia ser negada a partir de qualquer outro endereço IP.

Pode clicar em **[+] adicionar** para adicionar uma nova regra de restrição de acesso. Depois de adicionar uma regra, ele entrarão em vigor imediatamente. As regras são impostas por ordem de prioridade a partir do número mais baixo e a aumentar. Há uma implícita Negar tudo o que está em vigor depois de adicionar até mesmo uma única regra.

![Adicionar uma regra de restrição de acesso](media/app-service-ip-restrictions/ip-restrictions-add.png)

A notação de endereço IP tem de ser especificada na notação CIDR para endereços IPv4 e IPv6. Para especificar um endereço exato, pode usar algo como 1.2.3.4/32 onde os quatro primeiros octetos representam o seu endereço IP e /32 é a máscara. A notação de CIDR de IPv4 para todos os endereços é 0.0.0.0/0. Para saber mais sobre a notação CIDR, leia [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

Pode clicar em qualquer linha para editar uma regra de restrição de acesso existente. As edições entram em vigor de imediato, incluindo alterações na ordem de prioridade.

![Editar uma regra de restrição de acesso](media/app-service-ip-restrictions/ip-restrictions-edit.png)

Para eliminar uma regra, clique no **...**  na sua regra e clique em **remover**.

![Eliminar regra de restrição de acesso](media/app-service-ip-restrictions/ip-restrictions-delete.png)

Também pode restringir o acesso de implementação no separador seguinte. Para Adicionar/Editar/eliminar cada regra, siga o mesmo passo conforme apresentado acima.

![restrições de acesso de lista](media/app-service-ip-restrictions/ip-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Manipulação programática de regras de restrição de acesso ##

Atualmente não há CLI ou PowerShell para a nova capacidade de restrições de acesso, mas os valores podem ser definidos manualmente, com uma operação PUT na configuração da aplicação no Gestor de recursos. Por exemplo, pode utilizar resources.azure.com e edite o bloco de ipSecurityRestrictions para adicionar o JSON necessário.

A localização para estas informações no Gestor de recursos é:

Management.Azure.com/subscriptions/**ID de subscrição**/resourceGroups/**grupos de recursos**/providers/Microsoft.Web/sites/**nome da aplicação web**  /config/web? a API-version = 2018-02-01

A sintaxe JSON de exemplo anterior é:

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],
