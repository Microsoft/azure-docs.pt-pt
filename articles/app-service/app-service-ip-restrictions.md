---
title: Restringir acesso-serviço de Azure App | Microsoft Docs
description: Como usar restrições de acesso com o serviço Azure App
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: cee6fc9fb5cc10a2b3442e146ef5688ed74290bb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088444"
---
# <a name="azure-app-service-access-restrictions"></a>Azure App restrições de acesso de serviço #

As restrições de acesso permitem que você defina uma lista de permissão/negação ordenada de prioridade que controla o acesso à rede para seu aplicativo. A lista pode incluir endereços IP ou sub-redes de rede virtual do Azure. Quando há uma ou mais entradas, há então um "negar tudo" implícito que existe no final da lista.

A funcionalidade de restrições de acesso funciona com todas as cargas de trabalho hospedadas do serviço de aplicativo, incluindo; aplicativos Web, aplicativos de API, aplicativos do Linux, aplicativos de contêiner do Linux e funções.

Quando uma solicitação é feita ao seu aplicativo, o endereço de é avaliado em relação às regras de endereço IP em sua lista de restrições de acesso. Se o endereço de estiver em uma sub-rede configurada com pontos de extremidade de serviço para Microsoft. Web, a sub-rede de origem será comparada com as regras de rede virtual em sua lista de restrições de acesso. Se o endereço não tiver permissão de acesso com base nas regras na lista, o serviço responderá com um código de status [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) .

O recurso de restrições de acesso é implementado nas funções de front-end do serviço de aplicativo, que são upstream dos hosts de trabalho em que seu código é executado. Portanto, as restrições de acesso são efetivamente ACLs de rede.

A capacidade de restringir o acesso ao seu aplicativo Web de uma VNet (rede virtual) do Azure é chamada de [pontos de extremidade de serviço][serviceendpoints]. Os pontos de extremidade de serviço permitem restringir o acesso a um serviço multilocatário de sub-redes selecionadas. Ele deve ser habilitado tanto no lado da rede quanto no serviço com o qual está sendo habilitado. Ele não funciona para restringir o tráfego para aplicativos hospedados em um Ambiente do Serviço de Aplicativo.  Se você estiver em um Ambiente do Serviço de Aplicativo, poderá controlar o acesso ao seu aplicativo com regras de endereço IP.

![fluxo de restrições de acesso](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Adicionando e editando regras de restrição de acesso no portal ##

Para adicionar uma regra de restrição de acesso ao seu aplicativo, use o menu para abrir**restrições de acesso** à **rede**>e clique em **Configurar restrições de acesso**

![Opções de rede do serviço de aplicativo](media/app-service-ip-restrictions/access-restrictions.png)  

Na interface do usuário de restrições de acesso, você pode examinar a lista de regras de restrição de acesso definidas para seu aplicativo.

![listar restrições de acesso](media/app-service-ip-restrictions/access-restrictions-browse.png)

A lista mostrará todas as restrições atuais que estão em seu aplicativo. Se você tiver uma restrição de VNet em seu aplicativo, a tabela mostrará se os pontos de extremidade de serviço estão habilitados para Microsoft. Web. Quando não houver nenhuma restrição definida em seu aplicativo, seu aplicativo poderá ser acessado de qualquer lugar.  

## <a name="adding-ip-address-rules"></a>Adicionando regras de endereço IP

Você pode clicar em **[+] Adicionar** para adicionar uma nova regra de restrição de acesso. Depois de adicionar uma regra, ela entrará em vigor imediatamente. As regras são impostas em ordem de prioridade a partir do número mais baixo e na saída. Há uma negação implícita de tudo que está em vigor quando você adiciona até mesmo uma única regra.

Ao criar uma regra, você deve selecionar permitir/negar e também o tipo de regra. Também é necessário fornecer o valor de prioridade e o que você está restringindo o acesso ao.  Opcionalmente, você pode adicionar um nome e uma descrição à regra.  

![Adicionar uma regra de restrição de acesso a IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Para definir uma regra baseada em endereço IP, selecione um tipo de IPv4 ou IPv6. A notação de endereço IP deve ser especificada na notação CIDR para endereços IPv4 e IPv6. Para especificar um endereço exato, você pode usar algo como 1.2.3.4/32, em que os primeiros quatro octetos representam seu endereço IP e/32 é a máscara. A notação CIDR do IPv4 para todos os endereços é 0.0.0.0/0. Para saber mais sobre a notação CIDR, você pode ler [Roteamento entre domínios sem classificação](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Pontos finais de serviço

Os pontos de extremidade de serviço permitem restringir o acesso às sub-redes selecionadas da rede virtual do Azure. Para restringir o acesso a uma sub-rede específica, crie uma regra de restrição com um tipo de rede virtual. Você pode escolher a assinatura, a VNet e a sub-rede que deseja permitir ou negar acesso. Se os pontos de extremidade de serviço ainda não estiverem habilitados com o Microsoft. Web para a sub-rede que você selecionou, ele será habilitado automaticamente para você, a menos que você marque a caixa pedindo para não fazer isso. A situação em que você deseja habilitá-la no aplicativo, mas não a sub-rede está amplamente relacionada a se você tiver as permissões para habilitar os pontos de extremidade de serviço na sub-rede ou não. Se você precisar fazer com que outra pessoa habilite os pontos de extremidade de serviço na sub-rede, marque a caixa e faça com que seu aplicativo seja configurado para pontos de extremidade de serviço na previsão de que ele está sendo habilitado posteriormente na sub-rede. 

![Adicionar uma regra de restrição de acesso VNet](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Os pontos de extremidade de serviço não podem ser usados para restringir o acesso a aplicativos executados em um Ambiente do Serviço de Aplicativo. Quando seu aplicativo estiver em um Ambiente do Serviço de Aplicativo, você poderá controlar o acesso ao seu aplicativo com as regras de acesso de IP. 

Com os pontos de extremidade de serviço, você pode configurar seu aplicativo com gateways de aplicativo ou outros dispositivos WAF. Você também pode configurar aplicativos de várias camadas com back-ends seguros. Para obter mais detalhes sobre algumas das possibilidades, leia [recursos de rede e serviço de aplicativo](networking-features.md).

## <a name="managing-access-restriction-rules"></a>Gerenciando regras de restrição de acesso

Você pode clicar em qualquer linha para editar uma regra de restrição de acesso existente. As edições entram em vigor imediatamente, incluindo alterações na ordenação de prioridade.

![editar uma regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Quando você edita uma regra, não pode alterar o tipo entre uma regra de endereço IP e uma regra de rede virtual. 

![editar uma regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Para excluir uma regra, clique em **...** em sua regra e, em seguida, clique em **remover**.

![excluir regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Bloqueando um único endereço IP ##

Ao adicionar sua primeira regra de restrição de IP, o serviço adicionará uma regra **negar tudo** explícita com uma prioridade de 2147483647. Na prática, a regra **negar tudo** explícita será executada pela última vez e bloqueará o acesso a qualquer endereço IP que não seja explicitamente permitido usando uma regra de **permissão** .

Para o cenário em que os usuários desejam bloquear explicitamente um único endereço IP ou bloco de endereço IP, mas permitir que todas as outras pessoas acessem, é necessário adicionar uma regra **permitir tudo** explícito.

![bloquear endereço IP único](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>Site do SCM 

Além de poder controlar o acesso ao seu aplicativo, você também pode restringir o acesso ao site do SCM usado pelo seu aplicativo. O site do SCM é o ponto de extremidade de implantação da Web e também o console do kudu. Você pode atribuir separadamente restrições de acesso ao site do SCM do aplicativo ou usar o mesmo conjunto para o aplicativo e o site do SCM. Quando você marca a caixa para ter as mesmas restrições que seu aplicativo, tudo fica em branco. Se você desmarcar a caixa, quaisquer configurações que você tenha anteriormente no site do SCM serão aplicadas. 

![listar restrições de acesso](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Manipulação programática de regras de restrição de acesso ##

Atualmente, não há uma CLI ou o PowerShell para o novo recurso de restrições de acesso, mas os valores podem ser definidos manualmente com uma operação Put da [API REST do Azure](https://docs.microsoft.com/rest/api/azure/) na configuração do aplicativo no Gerenciador de recursos. Por exemplo, você pode usar resources.azure.com e editar o bloco ipSecurityRestrictions para adicionar o JSON necessário.

O local para essas informações no Resource Manager é:

management.azure.com/subscriptions/**ID da assinatura**/resourceGroups/**grupos de recursos**/Providers/Microsoft.Web/sites/nome do**aplicativo Web**/config/Web? API-Version = 2018-02-01

A sintaxe JSON para o exemplo anterior é:

    {
      "properties": {
        "ipSecurityRestrictions": [
          {
            "ipAddress": "122.133.144.0/24",
            "action": "Allow",
            "tag": "Default",
            "priority": 100,
            "name": "IP example rule"
          }
        ]
      }
    }

## <a name="function-app-ip-restrictions"></a>Aplicativo de funções restrições de IP

As restrições de IP estão disponíveis para ambos os aplicativos de funções com a mesma funcionalidade que os planos do serviço de aplicativo. Habilitar restrições de IP desabilitará o editor de código do portal para qualquer IPs não permitido.

[Saiba mais aqui](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
