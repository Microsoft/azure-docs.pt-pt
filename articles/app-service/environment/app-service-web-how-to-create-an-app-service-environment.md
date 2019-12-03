---
title: Criar um ASE v1
description: Descrição do fluxo de criação para um ambiente do serviço de aplicativo v1. Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 752334e3d594b1f95786aecaca134b74c4e264d5
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688701"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Como criar um Ambiente do Serviço de Aplicativo v1 

> [!NOTE]
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1. Há uma versão mais recente do Ambiente do Serviço de Aplicativo que é mais fácil de usar e é executada em uma infraestrutura mais potente. Para saber mais sobre a nova versão, comece com a [introdução ao ambiente do serviço de aplicativo](intro.md).
> 

### <a name="overview"></a>Visão geral
O Ambiente do Serviço de Aplicativo (ASE) é uma opção de serviço Premium do serviço de Azure App que fornece um recurso de configuração avançada que não está disponível nos carimbos multilocatários. Essencialmente, o recurso ASE implanta o serviço Azure App na rede virtual de um cliente. Para obter uma compreensão maior dos recursos oferecidos pelos ambientes do serviço de aplicativo, leia a documentação [o que é uma ambiente do serviço de aplicativo][WhatisASE] .

### <a name="before-you-create-your-ase"></a>Antes de criar seu ASE
É importante estar ciente das coisas que você não pode alterar. Esses aspectos que você não pode alterar sobre seu ASE após sua criação são:

* Localização
* Subscrição
* Grupo de Recursos
* VNet usada
* Sub-rede usada 
* Tamanho da sub-rede

Ao escolher uma VNet e especificar uma sub-rede, verifique se ela é grande o suficiente para acomodar qualquer crescimento futuro. 

### <a name="creating-an-app-service-environment-v1"></a>Criando um Ambiente do Serviço de Aplicativo v1
Para criar um Ambiente do Serviço de Aplicativo v1, você pode pesquisar o Azure Marketplace para ***ambiente do serviço de aplicativo v1***ou passar por **criar um recurso** -> **Web + celular** ** -> ambiente do serviço de aplicativo.** Para criar um ASEv1:

1. Forneça o nome do seu ASE. O nome que você especificar para o ASE será usado para os aplicativos criados no ASE. Se o nome do ASE for appsvcenvdemo, o nome do subdomínio será: *appsvcenvdemo.p.azurewebsites.net*. Se, portanto, você criou um aplicativo chamado *mytestapp*, ele seria endereçável em *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Não é possível usar o espaço em branco no nome do seu ASE. Se você usar caracteres em letras maiúsculas no nome, o nome de domínio será a versão total em minúsculas desse nome. Se você usar um ILB, o nome do ASE não será usado no subdomínio, mas, em vez disso, será declarado explicitamente durante a criação do ASE.
   
    ![][1]
2. Selecione a sua subscrição. A assinatura que você usa para o ASE também se aplicará a todos os aplicativos que você criar nesse ASE. Não é possível posicionar o ASE em uma VNet que está em outra assinatura.
3. Selecione ou especifique um novo grupo de recursos. O grupo de recursos usado para seu ASE deve ser o mesmo usado para sua VNet. Se você selecionar uma VNet pré-existente, a seleção do grupo de recursos para o ASE será atualizada para refletir a de sua VNet.
   
    ![][2]
4. Faça suas seleções de rede virtual e local. Você pode optar por criar uma nova VNet ou selecionar uma VNet já existente. Se você selecionar uma nova VNet, poderá especificar um nome e um local. A nova VNet terá o intervalo de endereços 192.168.250.0/23 e uma sub-rede denominada **Default** que é definida como 192.168.250.0/24. Você também pode simplesmente selecionar uma VNet clássica ou do Resource Manager preexistente. A seleção do tipo de VIP determina se o ASE pode ser acessado diretamente da Internet (externo) ou se ele usa um Load Balancer interno (ILB). Para saber mais sobre eles, leia [usando um Load balancer interno com um ambiente do serviço de aplicativo][ILBASE]. Se você selecionar um tipo de VIP externo, poderá selecionar quantos endereços IP externos o sistema será criado para fins de IPSSL. Se você selecionar interno, será necessário especificar o subdomínio que o seu ASE usará. *O* ases pode ser implantado em redes virtuais que usam intervalos de endereços públicos *ou* espaços de endereço RFC1918 (ou seja, endereços privados). Para usar uma rede virtual com um intervalo de endereços públicos, você precisará criar a VNet antes do tempo. Ao selecionar uma VNet pré-existente, você precisará criar uma nova sub-rede durante a criação do ASE. **Você não pode usar uma sub-rede criada previamente no Portal. Você pode criar um ASE com uma sub-rede pré-existente se criar seu ASE usando um modelo do Resource Manager.** Para criar um ASE a partir de um modelo, use as informações aqui, [criando um ambiente do serviço de aplicativo do modelo][ILBAseTemplate] e aqui, [criando um ambiente do serviço de aplicativo de ILB do modelo][ASEfromTemplate].

### <a name="details"></a>Detalhes
Um ASE é criado com 2 front-ends e 2 trabalhadores. Os front-ends atuam como os pontos de extremidade HTTP/HTTPS e enviam o tráfego para os trabalhadores que são as funções que hospedam seus aplicativos. Você pode ajustar a quantidade após a criação do ASE e pode até mesmo configurar regras de dimensionamento automático nesses pools de recursos. Para obter mais detalhes sobre o dimensionamento manual, o gerenciamento e o monitoramento de um Ambiente do Serviço de Aplicativo vão aqui: [como configurar um ambiente do serviço de aplicativo][ASEConfig] 

Apenas um ASE pode existir na sub-rede usada pelo ASE. A sub-rede não pode ser usada para nada além do ASE

### <a name="after-app-service-environment-v1-creation"></a>Após a criação do Ambiente do Serviço de Aplicativo v1
Após a criação do ASE, você pode ajustar:

* Quantidade de front-ends (mínimo: 2)
* Quantidade de trabalhos (mínimo: 2)
* Quantidade de endereços IP disponíveis para IP SSL
* Tamanhos de recursos de computação usados pelos front-ends ou trabalhos (o tamanho mínimo de front-end é P2)

Há mais detalhes sobre o dimensionamento manual, o gerenciamento e o monitoramento de ambientes do serviço de aplicativo aqui: [como configurar um ambiente do serviço de aplicativo][ASEConfig] 

Para obter informações sobre o dimensionamento automático, há um guia aqui: [como configurar a AutoEscala para um ambiente do serviço de aplicativo][ASEAutoscale]

Há dependências adicionais que não estão disponíveis para personalização, como o banco de dados e o armazenamento. Eles são tratados pelo Azure e acompanham o sistema. O armazenamento do sistema dá suporte a até 500 GB para todo o Ambiente do Serviço de Aplicativo e o banco de dados é ajustado pelo Azure conforme necessário pela escala do sistema.

## <a name="getting-started"></a>Introdução
Para começar a usar o Ambiente do Serviço de Aplicativo v1, consulte [introdução ao ambiente do serviço de aplicativo v1][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
