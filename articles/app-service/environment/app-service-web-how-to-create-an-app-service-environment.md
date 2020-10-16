---
title: Criar um ASE v1
description: Descrição do fluxo de criação para um ambiente de serviço de aplicações v1. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 543050bc899c257c4ad5e0d0c399a1de6f0f58f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86220580"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Como criar um Ambiente de Serviço de Aplicações v1 

> [!NOTE]
> Este artigo é sobre o App Service Environment v1. Existe uma versão mais recente do App Service Environment que é mais fácil de usar e funciona em infraestruturas mais poderosas. Para saber mais sobre a nova versão comece com a [Introdução ao Ambiente de Serviço de Aplicações.](intro.md)
> 

### <a name="overview"></a>Descrição geral
O App Service Environment (ASE) é uma opção de serviço Premium do Azure App Service que oferece uma capacidade de configuração melhorada que não está disponível nos selos multi-inquilinos. A funcionalidade ASE implanta essencialmente o Serviço de Aplicações Azure na rede virtual de um cliente. Para obter uma maior compreensão das capacidades oferecidas pela App Service Environments leia a documentação [What is a App Service Environment.][WhatisASE]

### <a name="before-you-create-your-ase"></a>Antes de criar o seu ASE
É importante estar atento às coisas que não se pode mudar. Os aspetos que não pode alterar sobre o seu ASE após a sua criação são:

* Localização
* Subscrição
* Grupo de Recursos
* VNet usado
* Sub-rede utilizada 
* Tamanho da sub-rede

Ao escolher um VNet e especificar uma sub-rede, certifique-se de que é grande o suficiente para acomodar qualquer crescimento futuro. 

### <a name="creating-an-app-service-environment-v1"></a>Criação de um Ambiente de Serviço de Aplicações v1
Para criar um App Service Environment v1, pode pesquisar o Azure Marketplace for ***App Service Environment v1,*** ou passar por **Create a resource**Web  ->  **+ Mobile**  ->  **App Service Environment**. Para criar um ASEv1:

1. Forneça o nome do seu ASE. O nome que especificar para o ASE será utilizado para as aplicações criadas na ASE. Se o nome do ASE for appsvcenvdemo, o nome do subdomínio seria: *appsvcenvdemo.p.azurewebsites.net*. Se assim criasse uma aplicação chamada *mytestapp,* seria endereçada *a mytestapp.appsvcenvdemo.p.azurewebsites.net*. Não pode usar o espaço branco em nome do seu ASE. Se utilizar caracteres maiúsculas no nome, o nome de domínio será a versão maiúscula total desse nome. Se utilizar um ILB, o seu nome ASE não é utilizado no seu subdomínio, mas é explicitamente indicado durante a criação da ASE.
   
    ![Screenshot que mostra como criar um Ambiente de Serviço de Aplicações (ASE).][1]
2. Selecione a sua subscrição. A subscrição que utiliza para o seu ASE também se aplicará a todas as aplicações que criar nesse ASE. Não é possível colocar o seu ASE num VNet que esteja noutra subscrição.
3. Selecione ou especifique um novo grupo de recursos. O grupo de recursos utilizado para o seu ASE deve ser o mesmo que é utilizado para o seu VNet. Se selecionar um VNet pré-existente, a seleção do grupo de recursos para o seu ASE será atualizada para refletir a do seu VNet.
   
    ![Screenshot que mostra como selecionar ou modificar um novo grupo de recursos.][2]
4. Faça as suas seleções de Rede Virtual e Localização. Pode optar por criar um novo VNet ou selecionar um VNet pré-existente. Se selecionar um novo VNet, poderá especificar um nome e localização. O novo VNet terá o intervalo de endereços 192.168.250.0/23 e um sub-redes nomeado **padrão** que é definido como 192.168.250.0/24. Também pode simplesmente selecionar um VNet clássico ou gestor de recursos pré-existente. A seleção do Tipo VIP determina se o seu ASE pode ser acedido diretamente a partir da internet (Externa) ou se utiliza um Balançador de Carga Interna (ILB). Para saber mais sobre eles leia [Usando um Balanceador de Carga Interna com um Ambiente de Serviço de Aplicações.][ILBASE] Se selecionar um tipo VIP de External, poderá selecionar quantos endereços IP externos o sistema foi criado para fins IP SSL. Se selecionar Internal, então tem de especificar o subdomínio que o seu ASE irá utilizar. AsEs podem ser implantadas em redes virtuais que *utilizam* intervalos de endereços públicos *ou* espaços de endereço RFC1918 (isto é, endereços privados). Para utilizar uma rede virtual com uma gama de endereços públicos, terá de criar o VNet com antecedência. Quando selecionar um VNet pré-existente, terá de criar uma nova sub-rede durante a criação do ASE. **Não é possível utilizar uma sub-rede pré-criada no portal. Pode criar um ASE com uma sub-rede pré-existente se criar o seu ASE utilizando um modelo de gestor de recursos.** Para criar um ASE a partir de um modelo use as informações aqui, [Criando um Ambiente de Serviço de Aplicação a partir do modelo][ILBAseTemplate] e aqui, [criando um Ambiente de Serviço de Aplicações ILB a partir do modelo][ASEfromTemplate].

### <a name="details"></a>Detalhes
Um ASE é criado com 2 Front Ends e 2 Trabalhadores. As Extremidades Front Terminais funcionam como pontos finais HTTP/HTTPS e enviam tráfego para os Trabalhadores que são os papéis que acolhem as suas apps. Pode ajustar a quantidade após a criação da ASE e até pode configurar regras de autoescalação nestes conjuntos de recursos. Para mais detalhes sobre o dimensionamento manual, gestão e monitorização de um Ambiente de Serviço de Aplicações, vá aqui: [Como configurar um Ambiente de Serviço de Aplicações][ASEConfig] 

Apenas o ASE pode existir na sub-rede utilizada pela ASE. A sub-rede não pode ser usada para nada além do ASE

### <a name="after-app-service-environment-v1-creation"></a>Após a criação do App Service Environment v1
Após a criação da ASE pode ajustar:

* Quantidade de Extremidades Frontais (mínimo: 2)
* Quantidade de Trabalhadores (mínimo: 2)
* Quantidade de endereços IP disponíveis para IP SSL
* Tamanhos de recursos computacional utilizados pelas extremidades dianteiras ou trabalhadores (o tamanho mínimo da extremidade dianteira é P2)

Há mais detalhes em torno da escala manual, gestão e monitorização de Ambientes de Serviço de Aplicações aqui: [Como configurar um Ambiente de Serviço de Aplicações][ASEConfig] 

Para obter informações sobre autoscaling existe um guia aqui: [Como configurar autoescala para um Ambiente de Serviço de Aplicações][ASEAutoscale]

Existem dependências adicionais que não estão disponíveis para personalização, como a base de dados e armazenamento. Estes são tratados pelo Azure e vêm com o sistema. O armazenamento do sistema suporta até 500 GB para todo o Ambiente de Serviço de Aplicações e a base de dados é ajustada pelo Azure conforme necessário pela escala do sistema.

## <a name="getting-started"></a>Introdução
Para começar com App Service Environment v1, consulte [Introdução ao Ambiente de Serviço de Aplicações v1][WhatisASE]

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
