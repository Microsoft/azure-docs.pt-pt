---
title: Como criar um serviço de aplicações v1 do ambiente - Azure
description: Descrição do fluxo de criação para um ambiente de serviço de aplicações v1
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 9bc796c4d0d449f72dc3234bc2825554eafaf77f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128889"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Como criar um serviço de aplicações v1 do ambiente 

> [!NOTE]
> Este artigo é sobre o ambiente de serviço de aplicações v1. Existe uma versão mais recente do ambiente de serviço de aplicação que é mais fácil de usar e é executado numa infraestrutura mais poderosa. Para saber mais sobre o novo início de versão com o [introdução ao ambiente de serviço de aplicações](intro.md).
> 

### <a name="overview"></a>Descrição geral
O ambiente de serviço de aplicações (ASE) é uma opção de serviço Premium do serviço de aplicações do Azure que fornece um recurso de configuração avançada que não está disponível nos carimbos multi-inquilino. A funcionalidade do ASE, essencialmente, implementa o serviço de aplicações do Azure numa rede virtual de um cliente. Para obter uma maior compreensão das capacidades disponibilizadas pela leitura de ambientes do App Service do [o que é um ambiente de serviço de aplicações] [ WhatisASE] documentação.

### <a name="before-you-create-your-ase"></a>Antes de criar o ASE
É importante ter em consideração as coisas que não é possível alterar. São esses aspectos que não é possível alterar sobre o ASE, depois de criado:

* Location
* Subscrição
* Grupo de Recursos
* VNet utilizada
* Sub-rede utilizada 
* Tamanho da sub-rede

Ao selecionar uma VNet e especificar uma sub-rede, certifique-se de que é grande o suficiente para contemplar qualquer crescimento futuro. 

### <a name="creating-an-app-service-environment-v1"></a>Criar uma aplicação v1 do ambiente de serviço
Para criar um ambiente de serviço de aplicações v1, pode pesquisar no Azure Marketplace ***ambiente de serviço de aplicações v1***, ou percorra **criar um recurso** -> **Web + móvel**  ->  **Ambiente de serviço de aplicações**. Para criar um ASEv1:

1. Forneça o nome do seu ASE. O nome que especificar para o ASE será utilizado para as aplicações que criou no ASE. Se o nome do ASE é appsvcenvdemo, seria o nome de subdomínio: *appsvcenvdemo.p.azurewebsites.net*. Se, portanto, criou uma aplicação com o nome *mytestapp*, que seria endereçável na *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Não é possível utilizar o espaço em branco no nome do seu ASE. Se utilizar carateres maiúsculos no nome, o nome de domínio será a versão em minúsculas total esse nome. Se usar um ILB, o nome do ASE não é utilizado em seu subdomínio, mas em vez disso, é declarado explicitamente durante a criação do ASE.
   
    ![][1]
2. Selecione a sua subscrição. A subscrição a que utilizar para o seu ASE também serão aplicadas a todas as aplicações que criar nesse ASE. Não é possível colocar o seu ASE numa VNet que está em outra subscrição.
3. Selecione ou especifique um novo grupo de recursos. O grupo de recursos utilizado para o seu ASE tem de ser o mesmo que é utilizado para a sua VNet. Se selecionar uma VNet já existente, a seleção de grupo de recursos para o seu ASE será atualizada para refletir que da sua VNet.
   
    ![][2]
4. Certifique as suas seleções de rede Virtual e localização. Pode optar por criar uma nova VNet ou selecionar uma VNet já existente. Se selecionar uma nova VNet, em seguida, pode especificar um nome e localização. A nova VNet terão o endereço intervalo 192.168.250.0/23 e uma sub-rede denominada **predefinição** que é definido como 192.168.250.0/24. Pode também simplesmente selecionar uma já existente clássica ou VNet do Resource Manager. A seleção do tipo de VIP determina se o seu ASE pode ser acedido diretamente da internet (externo) ou se este utilizar um balanceador de carga interno (ILB). Para saber mais sobre os mesmos, leia [com um balanceador de carga interno com um ambiente de serviço de aplicações][ILBASE]. Se selecionar um tipo de VIP externa, em seguida, pode selecionar quantos endereços IP externos o sistema é criado com finalidades IPSSL. Se selecionar interno, em seguida, tem de especificar o subdomínio que irá utilizar o seu ASE. Os ASEs podem ser implementados em redes virtuais que utilizam *ambos* intervalos de endereços públicos *ou* RFC1918 espaços de endereços (ou seja, endereços privados). Para utilizar uma rede virtual com um intervalo de endereços públicos, terá de criar a VNet antes do tempo. Ao selecionar uma VNet já existente terá de criar uma nova sub-rede durante a criação do ASE. **Não é possível utilizar uma sub-rede previamente criada no portal. Pode criar um ASE com uma sub-rede já existente se criar o seu ASE utilizando um modelo do resource manager.** Para criar um ASE a partir de uma utilização de modelo aqui, as informações [cria um ambiente de serviço de aplicações a partir de modelo] [ ILBAseTemplate] e, aqui, [cria um ambiente de serviço de aplicações ILB a partir de modelo] [ASEfromTemplate].

### <a name="details"></a>Detalhes
Um ASE é criado com 2 Front Ends e 2 funções de trabalho. Front Ends atuam como pontos finais de HTTP/HTTPS e enviar o tráfego para os trabalhadores que são as funções que alojam as suas aplicações. Pode ajustar a quantidade após a criação do ASE e pode até mesmo configurar regras de dimensionamento automático destes agrupamentos de recursos. Para obter mais detalhes em torno de dimensionamento manual, gestão e monitorização de um ambiente de serviço de aplicação aceda aqui: [Como configurar um ambiente de serviço de aplicações][ASEConfig] 

Apenas um ASE pode existir na sub-rede utilizada pelo ASE. A sub-rede não pode ser utilizada para tudo o que não seja o ASE

### <a name="after-app-service-environment-v1-creation"></a>Após a criação do ambiente de serviço de aplicações v1
Após a criação do ASE, pode ajustar:

* Quantidade de Front Ends (mínima: 2)
* Quantidade de trabalhadores (mínima: 2)
* Quantidade de endereços IP disponíveis para IP SSL
* Tamanhos de recurso utilizados pelo Front Ends ou trabalhos de computação (o tamanho mínimo de Front-End é P2)

Existem mais detalhes em torno do manual de dimensionamento, gestão e monitorização dos ambientes de serviço de aplicação aqui: [Como configurar um ambiente de serviço de aplicações][ASEConfig] 

Para obter informações sobre o dimensionamento automático, há um guia aqui: [Como configurar o dimensionamento automático para um ambiente de serviço de aplicações][ASEAutoscale]

Existem dependências adicionais que não estão disponíveis para personalização, como a base de dados e armazenamento. Estes são processados pelo Azure e são fornecidos com o sistema. O armazenamento do sistema suporta até 500 GB para todo o ambiente de serviço de aplicações e a base de dados é ajustado pelo Azure, conforme necessário, ao dimensionamento do sistema.

## <a name="getting-started"></a>Introdução
Para começar a utilizar com o ambiente de serviço de aplicações v1, veja [introdução para a v1 do ambiente de serviço de aplicações][WhatisASE]

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
