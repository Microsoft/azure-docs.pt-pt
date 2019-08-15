---
title: Criar um ambiente de serviço de aplicativo externo-Azure
description: Explica como criar um ambiente do serviço de aplicativo enquanto você cria um aplicativo ou autônomo
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e04dfa4148213e88aa46e464a31cdd9b6125e0bf
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67705769"
---
# <a name="create-an-external-app-service-environment"></a>Criar um ambiente de serviço de aplicativo externo

O Ambiente de Serviço de Aplicações do Azure é uma implementação do Serviço de Aplicações do Azure numa sub-rede de uma rede virtual do Azure (VNet).

> [!NOTE]
> Cada Ambiente do Serviço de Aplicativo tem um VIP (IP virtual), que pode ser usado para contatar o Ambiente do Serviço de Aplicativo.

Existem duas formas de implementar um Ambiente de Serviço de Aplicações (ASE):

- Com um VIP num endereço IP externo, muitas vezes chamado ASE Externo.
- Com o VIP em um endereço IP interno, geralmente chamado de ASE ILB porque o ponto de extremidade interno é um Load Balancer interno (ILB).

Este artigo mostra como criar um ASE externo. Para obter uma visão geral do ASE, consulte [uma introdução ao ambiente do serviço de aplicativo][Intro]. Para obter informações sobre como criar um ASE ILB, consulte [criar e usar um ase ILB][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Antes de criar seu ASE

Depois de criar seu ASE, você não pode alterar o seguinte:

- Location
- Subscription
- Resource group
- VNet usada
- Sub-rede usada
- Tamanho da sub-rede

> [!NOTE]
> Quando você escolhe uma VNet e especifica uma sub-rede, certifique-se de que ela seja grande o suficiente para acomodar as necessidades futuras de crescimento e dimensionamento. Recomendamos um tamanho de `/24` com 256 endereços.
>

## <a name="three-ways-to-create-an-ase"></a>Três maneiras de criar um ASE

Há três maneiras de criar um ASE:

- **Ao criar um plano do serviço de aplicativo**. Esse método cria o ASE e o plano do serviço de aplicativo em uma única etapa.
- **Como uma ação autônoma**. Esse método cria um ASE autônomo, que é um ASE com nada nele. Esse método é um processo mais avançado para criar um ASE. Você o usa para criar um ASE com um ILB.
- **De um modelo de Azure Resource Manager**. Esse método é para usuários avançados. Para obter mais informações, consulte [criar um ase a partir de um modelo][MakeASEfromTemplate].

Um ASE externo tem um VIP público, o que significa que todo o tráfego HTTP/HTTPS para os aplicativos no ASE atinge um endereço IP acessível pela Internet. Um ASE com um ILB tem um endereço IP da sub-rede usada pelo ASE. Os aplicativos hospedados em um ASE ILB não são expostos diretamente à Internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Criar um ASE e um plano do serviço de aplicativo juntos

O plano do serviço de aplicativo é um contêiner de aplicativos. Ao criar um aplicativo no serviço de aplicativo, você escolhe ou cria um plano do serviço de aplicativo. Os ambientes do serviço de aplicativo mantêm planos do serviço de aplicativo e os planos do serviço de aplicativo mantêm aplicativos.

Para criar um ASE enquanto você cria um plano do serviço de aplicativo:

1. Na [portal do Azure](https://portal.azure.com/), selecione **criar um recurso** > **Web + celular** > **aplicativo Web**.

    ![Criação de aplicativo Web][1]

2. Selecione a sua subscrição. O aplicativo e o ASE são criados nas mesmas assinaturas.

3. Selecione ou crie um grupo de recursos. Com os grupos de recursos, você pode gerenciar recursos do Azure relacionados como uma unidade. Os grupos de recursos também são úteis quando você estabelece regras de controle de acesso baseado em função para seus aplicativos. Para obter mais informações, veja a [Descrição geral do Azure Resource Manager][ARMOverview].

4. Selecione seu sistema operacional (Windows, Linux ou Docker). 

5. Selecione o plano do serviço de aplicativo e, em seguida, selecione **criar novo**. Os aplicativos Web do Linux e os aplicativos Web do Windows não podem estar no mesmo plano do serviço de aplicativo, mas podem estar na mesma Ambiente do Serviço de Aplicativo. 

    ![Novo plano do Serviço de Aplicações][2]

6. Na lista suspensa **local** , selecione a região onde você deseja criar o ase. Se você selecionar um ASE existente, um novo ASE não será criado. O plano do serviço de aplicativo é criado no ASE que você selecionou. 

7. Selecione **tipo de preço**e escolha uma das SKUs de preços isoladas. Se você escolher um Cartão SKU **isolado** e um local que não seja um ASE, um novo ase será criado nesse local. Para iniciar o processo para criar um ASE, selecione **selecionar**. O SKU **isolado** está disponível somente em conjunto com um ASE. Você também não pode usar nenhum outro SKU de preços em um ASE diferente de **isolado**. 

    ![Seleção do tipo de preço][3]

8. Insira o nome do seu ASE. Esse nome é usado no nome endereçável para seus aplicativos. Se o nome do ASE for _appsvcenvdemo_, o nome de domínio será *. appsvcenvdemo.p.azurewebsites.net*. Se você criar um aplicativo chamado *mytestapp*, ele será endereçável em mytestapp.appsvcenvdemo.p.azurewebsites.net. Não é possível usar espaço em branco no nome. Se você usar caracteres maiúsculos, o nome de domínio será a versão total em minúsculas desse nome.

    ![Novo nome do plano do serviço de aplicativo][4]

9. Especifique os detalhes da rede virtual do Azure. Selecione **criar novo** ou **selecionar existente**. A opção para selecionar uma VNet existente só estará disponível se você tiver uma VNet na região selecionada. Se você selecionar **criar novo**, insira um nome para a VNet. Uma nova VNet do Resource Manager com esse nome é criada. Ele usa o espaço `192.168.250.0/23` de endereço na região selecionada. Se selecionar **selecionar existente**, você precisará:

    a. Selecione o bloco de endereço da VNet, se você tiver mais de um.

    b. Insira um novo nome de sub-rede.

    c. Selecione o tamanho da sub-rede. *Lembre-se de selecionar um tamanho grande o suficiente para acomodar o crescimento futuro de seu ASE.* Recomendamos `/24`, que tem 128 endereços e pode lidar com um ase de tamanho máximo. Não recomendamos `/28`, por exemplo, porque apenas 16 endereços estão disponíveis. A infraestrutura usa pelo menos sete endereços e a rede do Azure usa outro 5. Em uma `/28` sub-rede, você tem um dimensionamento máximo de 4 instâncias do plano do serviço de aplicativo para um ase externo e apenas três instâncias do plano do serviço de aplicativo para um ase ILB.

    d. Selecione o intervalo de IPS de sub-rede.

10. Selecione **criar** para criar o ase. Esse processo também cria o plano do serviço de aplicativo e o aplicativo. O ASE, o plano do serviço de aplicativo e o aplicativo estão todos na mesma assinatura e também no mesmo grupo de recursos. Se seu ASE precisar de um grupo de recursos separado ou se você precisar de um ASE ILB, siga as etapas para criar um ASE sozinho.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Criar um ASE e um aplicativo Web do Linux usando uma imagem personalizada do Docker juntas

1. No [portal do Azure](https://portal.azure.com/), **crie um recurso** > **Web + celular** > **aplicativo Web para contêineres.** 

    ![Criação de aplicativo Web][7]

1. Selecione a sua subscrição. O aplicativo e o ASE são criados nas mesmas assinaturas.

1. Selecione ou crie um grupo de recursos. Com os grupos de recursos, você pode gerenciar recursos do Azure relacionados como uma unidade. Os grupos de recursos também são úteis quando você estabelece regras de controle de acesso baseado em função para seus aplicativos. Para obter mais informações, veja a [Descrição geral do Azure Resource Manager][ARMOverview].

1. Selecione o plano do serviço de aplicativo e, em seguida, selecione **criar novo**. Os aplicativos Web do Linux e os aplicativos Web do Windows não podem estar no mesmo plano do serviço de aplicativo, mas podem estar na mesma Ambiente do Serviço de Aplicativo. 

    ![Novo plano do Serviço de Aplicações][8]

1. Na lista suspensa **local** , selecione a região onde você deseja criar o ase. Se você selecionar um ASE existente, um novo ASE não será criado. O plano do serviço de aplicativo é criado no ASE que você selecionou. 

1. Selecione **tipo de preço**e escolha uma das SKUs de preços isoladas. Se você escolher um Cartão SKU **isolado** e um local que não seja um ASE, um novo ase será criado nesse local. Para iniciar o processo para criar um ASE, selecione **selecionar**. O SKU **isolado** está disponível somente em conjunto com um ASE. Você também não pode usar nenhum outro SKU de preços em um ASE diferente de **isolado**. 

    ![Seleção do tipo de preço][3]

1. Insira o nome do seu ASE. Esse nome é usado no nome endereçável para seus aplicativos. Se o nome do ASE for _appsvcenvdemo_, o nome de domínio será *. appsvcenvdemo.p.azurewebsites.net*. Se você criar um aplicativo chamado *mytestapp*, ele será endereçável em mytestapp.appsvcenvdemo.p.azurewebsites.net. Não é possível usar espaço em branco no nome. Se você usar caracteres maiúsculos, o nome de domínio será a versão total em minúsculas desse nome.

    ![Novo nome do plano do serviço de aplicativo][4]

1. Especifique os detalhes da rede virtual do Azure. Selecione **criar novo** ou **selecionar existente**. A opção para selecionar uma VNet existente só estará disponível se você tiver uma VNet na região selecionada. Se você selecionar **criar novo**, insira um nome para a VNet. Uma nova VNet do Resource Manager com esse nome é criada. Ele usa o espaço `192.168.250.0/23` de endereço na região selecionada. Se selecionar **selecionar existente**, você precisará:

    a. Selecione o bloco de endereço da VNet, se você tiver mais de um.

    b. Insira um novo nome de sub-rede.

    c. Selecione o tamanho da sub-rede. *Lembre-se de selecionar um tamanho grande o suficiente para acomodar o crescimento futuro de seu ASE.* Recomendamos `/24`, que tem 128 endereços e pode lidar com um ase de tamanho máximo. Não recomendamos `/28`, por exemplo, porque apenas 16 endereços estão disponíveis. A infraestrutura usa pelo menos sete endereços e a rede do Azure usa outro 5. Em uma `/28` sub-rede, você tem um dimensionamento máximo de 4 instâncias do plano do serviço de aplicativo para um ase externo e apenas três instâncias do plano do serviço de aplicativo para um ase ILB.

    d. Selecione o intervalo de IPS de sub-rede.

1.  Selecione "configurar contêiner".
    * Insira o nome da imagem personalizada (você pode usar o registro de contêiner do Azure, o Hub do Docker e seu próprio registro privado). Se você não quiser usar seu próprio contêiner personalizado, você pode simplesmente colocar seu código e usar uma imagem interna com o serviço de aplicativo no Linux, usando as instruções acima. 

    ![Configurar contêiner][9]

1. Selecione **criar** para criar o ase. Esse processo também cria o plano do serviço de aplicativo e o aplicativo. O ASE, o plano do serviço de aplicativo e o aplicativo estão todos na mesma assinatura e também no mesmo grupo de recursos. Se seu ASE precisar de um grupo de recursos separado ou se você precisar de um ASE ILB, siga as etapas para criar um ASE sozinho.


## <a name="create-an-ase-by-itself"></a>Criar um ASE sozinho

Se você criar um ASE autônomo, ele não terá nada nele. Um ASE vazio ainda incorre em um custo mensal para a infraestrutura. Siga estas etapas para criar um ASE com um ILB ou para criar um ASE em seu próprio grupo de recursos. Depois de criar seu ASE, você pode criar aplicativos nele usando o processo normal. Selecione o novo ASE como o local.

1. Pesquise **ambiente do serviço de aplicativo**do Azure Marketplace ou selecione **criar um recurso** > **ambiente do serviço de aplicativo** **Web móvel** > . 

1. Insira o nome do seu ASE. Esse nome é usado para os aplicativos criados no ASE. Se o nome for *mynewdemoase*, o nome do subdomínio será *. mynewdemoase.p.azurewebsites.net*. Se você criar um aplicativo chamado *mytestapp*, ele será endereçável em mytestapp.mynewdemoase.p.azurewebsites.net. Não é possível usar espaço em branco no nome. Se você usar caracteres maiúsculos, o nome de domínio será a versão total em minúsculas do nome. Se você usar um ILB, o nome do ASE não será usado no subdomínio, mas, em vez disso, será declarado explicitamente durante a criação do ASE.

    ![Nomenclatura do ASE][5]

1. Selecione a sua subscrição. Essa assinatura também é aquela que todos os aplicativos do ASE usam. Você não pode colocar seu ASE em uma VNet que está em outra assinatura.

1. Selecione ou especifique um novo grupo de recursos. O grupo de recursos usado para seu ASE deve ser o mesmo usado para sua VNet. Se você selecionar uma VNet existente, a seleção do grupo de recursos para o ASE será atualizada para refletir a de sua VNet. *Você pode criar um ASE com um grupo de recursos diferente do grupo de recursos de VNet se usar um modelo do Resource Manager.* Para criar um ASE a partir de um modelo, consulte [criar um ambiente do serviço de aplicativo por meio de um modelo][MakeASEfromTemplate].

    ![Seleção de grupos de recursos][6]

1. Selecione sua VNet e o local. Você pode criar uma nova VNet ou selecionar uma VNet existente: 

    * Se selecionar uma nova VNet, pode especificar um nome e a localização. 
    
    * A nova VNet tem o intervalo de endereços 192.168.250.0/23 e uma sub-rede denominada default. A sub-rede é definida como 192.168.250.0/24. Você só pode selecionar uma VNet do Resource Manager. A seleção do **tipo de VIP** determina se o ASE pode ser acessado diretamente da Internet (externo) ou se ele usa um ILB. Para saber mais sobre essas opções, confira [criar e usar um balanceador de carga interno com um ambiente do serviço de aplicativo][MakeILBASE]. 

      * Se você selecionar **externo** para o **tipo de VIP**, poderá selecionar quantos endereços IP externos o sistema será criado para fins de SSL baseado em IP. 
    
      * Se você selecionar **interno** para o **tipo de VIP**, deverá especificar o domínio que seu ase usa. Você pode implantar um ASE em uma VNet que usa intervalos de endereços públicos ou privados. Para usar uma VNet com um intervalo de endereços públicos, você precisa criar a VNet antes do tempo. 
    
    * Se você selecionar uma VNet existente, uma nova sub-rede será criada quando o ASE for criado. *Você não pode usar uma sub-rede criada previamente no Portal. Você pode criar um ASE com uma sub-rede existente se usar um modelo do Resource Manager.* Para criar um ASE a partir de um modelo, consulte [criar um ambiente do serviço de aplicativo de um modelo][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicações v1

Você ainda pode criar instâncias da primeira versão do Ambiente do Serviço de Aplicativo (ASEv1). Para iniciar esse processo, pesquise no Marketplace por **ambiente do serviço de aplicativo v1**. Você cria o ASE da mesma maneira que cria o ASE autônomo. Quando tiver terminado, seu ASEv1 terá dois front-ends e dois trabalhadores. Com o ASEv1, você deve gerenciar os front-ends e os trabalhos. Eles não são adicionados automaticamente quando você cria seus planos do serviço de aplicativo. Os front-ends atuam como os pontos de extremidade HTTP/HTTPS e enviam o tráfego para os trabalhadores. Os trabalhos são as funções que hospedam seus aplicativos. Você pode ajustar a quantidade de front-ends e trabalhadores depois de criar seu ASE. 

Para saber mais sobre o ASEv1, confira [introdução ao ambiente do serviço de aplicativo v1][ASEv1Intro]. Para obter mais informações sobre como dimensionar, gerenciar e monitorar ASEv1, consulte [como configurar um ambiente do serviço de aplicativo][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
