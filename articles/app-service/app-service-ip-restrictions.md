---
title: Restringir o acesso - serviço de aplicações do Azure | Documentos da Microsoft
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
ms.date: 05/23/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: be0de7e809565fce4171401760d11ef9de45724e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236108"
---
# <a name="azure-app-service-access-restrictions"></a>Restrições de acesso de serviço de aplicações do Azure #

Restrições de acesso permitem-lhe definir uma lista ordenada permitir/recusar de prioridade que controla o acesso de rede para a sua aplicação. A lista pode incluir endereços IP ou sub-redes de rede Virtual do Azure. Quando há uma ou mais entradas, não existe, em seguida, um implícita "Negar tudo" que existe no final da lista.

A capacidade de restrições de acesso funciona com todos os serviço de aplicações, cargas de trabalho alojado incluindo; aplicações Web, aplicações API, as aplicações do Linux, aplicações de contentor do Linux e as funções.

Quando é efetuado um pedido à sua aplicação, o endereço é comparado com as regras de endereços IP na sua lista de restrições de acesso. Se o endereço numa sub-rede que está configurada com pontos finais de serviço à Microsoft. Web, em seguida, a sub-rede de origem é comparada com as regras de rede virtual na sua lista de restrições de acesso. Se o endereço não é permitido o acesso com base nas regras na lista, o serviço responde com uma [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) código de estado.

A capacidade de restrições de acesso é implementada nas funções de front-end de serviço de aplicações, que são a montante, os anfitriões de trabalho em que o seu código é executado. Por conseguinte, restrições de acesso são, efetivamente, ACLs de rede.

A capacidade para restringir o acesso à sua aplicação web a partir de uma rede Virtual do Azure (VNet) é chamada [pontos finais de serviço][serviceendpoints]. Pontos finais de serviço permitem-lhe restringir o acesso a um serviço de multi-inquilino de sub-redes selecionadas. Tem de estar ativada no tanto do lado do funcionamento em rede, bem como o serviço que está a ser ativada com. Ele não funciona para restringir o tráfego para aplicações alojadas num ambiente de serviço de aplicações.  Se estiver num ambiente de serviço de aplicações, pode controlar o acesso à sua aplicação com regras de endereços IP.

![fluxo de restrições de acesso](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Adicionar e editar regras de restrição de acesso no portal ##

Para adicionar uma regra de restrição de acesso à sua aplicação, utilize o menu para abrir **rede**>**restrições de acesso** e clique em **configurar restrições de acesso**

![Opções de rede do serviço de aplicações](media/app-service-ip-restrictions/access-restrictions.png)  

Desde a interface do Usuário de restrições de acesso, pode rever a lista de regras de restrição de acesso definidas para a sua aplicação.

![restrições de acesso de lista](media/app-service-ip-restrictions/access-restrictions-browse.png)

A lista mostrará todas as restrições atuais que estão na sua aplicação. Se tiver uma restrição de VNet na sua aplicação, a tabela mostra se os pontos finais de serviço estão ativados para Microsoft. Web. Quando não existem sem restrições definidas na sua aplicação, a aplicação estará acessível de qualquer lugar.  

Pode clicar em **[+] adicionar** para adicionar uma nova regra de restrição de acesso. Depois de adicionar uma regra, ele entrarão em vigor imediatamente. As regras são impostas por ordem de prioridade a partir do número mais baixo e a aumentar. Há uma implícita Negar tudo o que está em vigor depois de adicionar até mesmo uma única regra.

![Adicionar uma regra de restrição de acesso de IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Ao criar uma regra, tem de selecionar permitir/recusar e também o tipo de regra. Também tem de fornecer o valor de prioridade e o que está a restringir o acesso a.  Opcionalmente, pode adicionar um nome e uma descrição para a regra.  

Para definir um endereço IP com base em regra, selecione um tipo de IPv4 ou IPv6. A notação de endereço IP tem de ser especificada na notação CIDR para endereços IPv4 e IPv6. Para especificar um endereço exato, pode usar algo como 1.2.3.4/32 onde os quatro primeiros octetos representam o seu endereço IP e /32 é a máscara. A notação de CIDR de IPv4 para todos os endereços é 0.0.0.0/0. Para saber mais sobre a notação CIDR, leia [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

![Adicionar uma regra de restrição de acesso de VNet](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Para restringir o acesso a sub-redes selecionadas, selecione um tipo de rede Virtual. Abaixo disso, poderá escolher a subscrição, a VNet e a sub-rede que pretende permitir ou negar o acesso com. Se a pontos finais de serviço não já foram ativados com o Microsoft. Web para a sub-rede que selecionou, ele será ativado automaticamente para, a menos que marque a caixa pedindo para não fazê-lo. A situação em que iria querer ativá-lo a aplicação, mas não a sub-rede está amplamente relacionada à se tiver as permissões para ativar os pontos finais de serviço na sub-rede ou não. Se precisar de obter alguém para ativar pontos finais de serviço na sub-rede, pode selecionar a caixa e ter a aplicação configurada para pontos finais de serviço em antecipação a ele que está a ser ativado mais tarde na sub-rede. 

Pontos finais de serviço não podem ser utilizados para restringir o acesso às aplicações que são executados num ambiente de serviço de aplicações. Quando a sua aplicação está num ambiente de serviço de aplicações, pode controlar o acesso à sua aplicação com regras de acesso IP. 

Pode clicar em qualquer linha para editar uma regra de restrição de acesso existente. As edições entram em vigor de imediato, incluindo alterações na ordem de prioridade.

![Editar uma regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Ao editar uma regra, não é possível alterar o tipo de entre uma regra de endereço IP e uma regra de rede Virtual. 

![Editar uma regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Para eliminar uma regra, clique no **...**  na sua regra e clique em **remover**.

![Eliminar regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-delete.png)

### <a name="scm-site"></a>Site do SCM 

Além de ser capaz de controlar o acesso à sua aplicação, também pode restringir o acesso ao site scm utilizado pela sua aplicação. O site do scm é web implementar ponto final e também a consola Kudu. Em separado pode atribuir as restrições de acesso ao scm site da aplicação ou utilizar o mesmo definido para a aplicação e o site do scm. Quando seleciona a caixa para têm as mesmas restrições que a sua aplicação, tudo o que é blanked horizontalmente. Se desmarcar a caixa, são aplicadas a quaisquer definições que tinha anteriormente no site do scm. 

![restrições de acesso de lista](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="blocking-a-single-ip-address"></a>Bloqueio de um único endereço IP ##

Ao adicionar a sua primeira regra de restrição de IP, o serviço irá adicionar explícita **recusar tudo** regra com prioridade 2147483647. Na prática, o explícita **recusar tudo** regra será a última regra executada e irá bloquear o acesso para qualquer endereço IP que não é explicitamente permitido utilizando um **permitir** regra.

Para o cenário em que os usuários querem bloquear explicitamente um único endereço IP ou o bloco de endereços IP, mas permitir tudo o que mais acesso, é necessário adicionar explícita **permitir todas as** regra.

![endereço de ip único bloco](media/app-service-ip-restrictions/block-single-address.png)

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

## <a name="function-app-ip-restrictions"></a>Restrições de IP da aplicação de função

Restrições de IP estão disponíveis para ambas as aplicações de função com a mesma funcionalidade que planos do serviço de aplicações. Ativar restrições de IP desabilitará o editor de código de portal para qualquer IPs não permitidos.

[Saiba mais aqui](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
