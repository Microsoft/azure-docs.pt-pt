---
title: Criar um ASE v1
description: Descrição do fluxo de criação para um ambiente de serviço de aplicações v1. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89dc96370f65ff20d7f8be38ff78d6c1664305d3
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477790"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Como criar um ambiente de serviço de aplicações v1 

> [!NOTE]
> Este artigo é sobre o App Service Environment v1. Existe uma versão mais recente do App Service Environment que é mais fácil de usar e funciona em infraestruturas mais poderosas. Para saber mais sobre a nova versão comece com a Introdução ao Ambiente de Serviço de [Aplicações.](intro.md)
> 

### <a name="overview"></a>Descrição geral
O App Service Environment (ASE) é uma opção de serviço Premium do Serviço de Aplicações Azure que oferece uma capacidade de configuração melhorada que não está disponível nos selos multi-inquilinos. A funcionalidade ASE implanta essencialmente o Serviço de Aplicações Azure na rede virtual de um cliente. Para obter uma maior compreensão das capacidades oferecidas pelos Ambientes de Serviço de Aplicação, leia a documentação [What is a App Service Environment.][WhatisASE]

### <a name="before-you-create-your-ase"></a>Antes de criar a sua ASE
É importante estar atento às coisas que não pode mudar. Esses aspetos que não pode mudar sobre a sua ASE depois de criado são:

* Localização
* Subscrição
* Grupo de Recursos
* VNet usado
* Subnet utilizado 
* Tamanho da subnet

Ao escolher um VNet e especificar uma subneta, certifique-se de que é grande o suficiente para acomodar qualquer crescimento futuro. 

### <a name="creating-an-app-service-environment-v1"></a>Criação de um App Service Environment v1
Para criar um App Service Environment v1, pode pesquisar o Azure Marketplace para ***App Service Environment v1,*** ou passar por **Create a resource** -> **Web + Mobile** -> **App Service Environment**. Para criar um ASEv1:

1. Forneça o nome da sua ASE. O nome que especifica para a ASE será utilizado para as aplicações criadas na ASE. Se o nome da ASE for appsvcenvdemo, o nome do subdomínio seria: *appsvcenvdemo.p.azurewebsites.net*. Se assim criasse uma aplicação chamada *mytestapp,* seria endereçada em *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Não pode usar espaço branco em nome da sua ASE. Se utilizar caracteres maiúsculos no nome, o nome de domínio será a versão minúscula total desse nome. Se utilizar um ILB, o seu nome ASE não é utilizado no seu subdomínio, mas é explicitamente indicado durante a criação da ASE.
   
    ![][1]
2. Selecione a sua subscrição. A subscrição que utilizar para a sua ASE também se aplicará a todas as aplicações que criar nessa ASE. Não é possível colocar a Sua ASE numa VNet que se encontra noutra subscrição.
3. Selecione ou especifique um novo grupo de recursos. O grupo de recursos utilizado para a sua ASE deve ser o mesmo que é utilizado para o seu VNet. Se selecionar um VNet pré-existente, a seleção do grupo de recursos para a sua ASE será atualizada para refletir a do seu VNet.
   
    ![][2]
4. Faça as suas seleções de Rede Virtual e Localização. Pode optar por criar um novo VNet ou selecionar um VNet pré-existente. Se selecionar um novo VNet, então pode especificar um nome e localização. O novo VNet terá o intervalo de endereços 192.168.250.0/23 e uma subnet denominada **padrão** que é definida como 192.168.250.0/24. Também pode simplesmente selecionar um VNet Clássico ou Gestor de Recursos pré-existente. A seleção do Tipo VIP determina se a sua ASE pode ser diretamente acedida a partir da internet (Externa) ou se utiliza um Balancer de Carga Interna (ILB). Para saber mais sobre eles ler [Usando um Balancer de Carga Interna com um Ambiente de Serviço][ILBASE]de Aplicações . Se selecionar um tipo VIP de Externo, pode selecionar quantos endereços IP externos o sistema é criado para fins IP SSL. Se selecionar Interno, então precisa especificar o subdomínio que a Sua ASE utilizará. AsEs podem ser implantados em redes virtuais que *utilizam* gamas de endereços públicos, *ou* espaços de endereçoS RFC1918 (isto é, endereços privados). Para utilizar uma rede virtual com uma gama de endereços públicos, terá de criar o VNet com antecedência. Quando selecionar um VNet pré-existente, terá de criar uma nova subrede durante a criação da ASE. **Não é possível utilizar uma sub-rede pré-criada no portal. Pode criar uma ASE com uma subrede pré-existente se criar a sua ASE utilizando um modelo de gestor de recursos.** Para criar uma ASE a partir de um modelo, use a informação aqui, criando um ambiente de serviço de [aplicação a partir do modelo][ILBAseTemplate] e aqui, criando um Ambiente de Serviço de [Aplicações ILB a partir do modelo][ASEfromTemplate].

### <a name="details"></a>Detalhes
Uma ASE é criada com 2 Front Ends e 2 Trabalhadores. As Front Ends funcionam como os pontos finais HTTP/HTTPS e enviam tráfego para os Trabalhadores, que são as funções que acolhem as suas apps. Pode ajustar a quantidade após a criação da ASE e até pode configurar regras de escala automática nestes conjuntos de recursos. Para mais detalhes em torno da escala manual, gestão e monitorização de um Ambiente de Serviço de Aplicações, vá aqui: [Como configurar um Ambiente][ASEConfig] de Serviço de Aplicações 

Só a ASE pode existir na sub-rede utilizada pela ASE. A sub-rede não pode ser utilizada para outra coisa que não a ASE

### <a name="after-app-service-environment-v1-creation"></a>Após a criação do App Service Environment v1
Após a criação da ASE, pode ajustar:

* Quantidade de Extremidades Frontais (mínimo: 2)
* Quantidade de Trabalhadores (mínimo: 2)
* Quantidade de endereços IP disponíveis para IP SSL
* Tamanhos de recursos computacionais utilizados pelas extremidades dianteiras ou trabalhadores (o tamanho mínimo frontend é P2)

Existem mais detalhes em torno da escala manual, gestão e monitorização de Ambientes de Serviço de Aplicações aqui: [Como configurar um Ambiente][ASEConfig] de Serviço de Aplicações 

Para obter informações sobre autoscalcificação existe aqui um guia: [Como configurar a escala automática para um Ambiente de Serviço][ASEAutoscale] de Aplicações

Existem dependências adicionais que não estão disponíveis para personalização, como a base de dados e armazenamento. Estes são tratados pelo Azure e vêm com o sistema. O armazenamento do sistema suporta até 500 GB para todo o Ambiente de Serviço de Aplicações e a base de dados é ajustada pelo Azure conforme necessário pela escala do sistema.

## <a name="getting-started"></a>Introdução
Para começar com o App Service Environment v1, consulte Introdução ao Ambiente de [Serviço de Aplicações v1][WhatisASE]

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
