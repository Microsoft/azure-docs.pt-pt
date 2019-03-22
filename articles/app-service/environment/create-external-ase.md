---
title: Criar um ambiente de serviço externo da aplicação - Azure
description: Explica como criar um ambiente de serviço de aplicações, enquanto cria uma aplicação ou autónomo
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
ms.openlocfilehash: 4a2c90accaafea0c17456f8e6c5eae41199b17ed
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105170"
---
> [!NOTE]
> Cada ambiente de serviço de aplicações tem um IP Virtual (VIP), que pode ser utilizado para contactar o ambiente de serviço de aplicações.
> 
> # <a name="create-an-external-app-service-environment"></a>Criar um ambiente de serviço de aplicações externo #

O Ambiente de Serviço de Aplicações do Azure é uma implementação do Serviço de Aplicações do Azure numa sub-rede de uma rede virtual do Azure (VNet). Existem duas formas de implementar um Ambiente de Serviço de Aplicações (ASE):

- Com um VIP num endereço IP externo, muitas vezes chamado ASE Externo.
- Com o VIP num endereço IP interno, muitas vezes chamado ASE de ILB porque o ponto final interno é um balanceador de carga interno (ILB).

Este artigo mostra-lhe como criar um ASE externo. Para obter uma descrição geral do ASE, veja [uma introdução ao ambiente de serviço de aplicações][Intro]. Para obter informações sobre como criar um ASE de ILB, veja [criar e utilizar um ASE de ILB][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Antes de criar o ASE ##

Depois de criar o ASE, não é possível alterar o seguinte:

- Localização
- Subscrição
- Grupo de recursos
- VNet utilizada
- Sub-rede utilizada
- Tamanho da sub-rede

> [!NOTE]
> Quando escolher uma VNet e especifique uma sub-rede, certifique-se de que é suficientemente grande para acomodar o crescimento futuro e as necessidades de dimensionamento. Recomendamos um tamanho de `/24` com 256 endereços.
>

## <a name="three-ways-to-create-an-ase"></a>Três formas de criar um ASE ##

Existem três formas de criar um ASE:

- **Ao criar um plano do serviço de aplicações**. Este método cria o ASE e o plano de serviço de aplicações num único passo.
- **Como uma ação de autónomo**. Este método cria uma autónoma ASE, que é um ASE sem nada nela. Esse método é um processo mais avançado para criar um ASE. Utilizá-lo para criar um ASE com um ILB.
- **A partir de um modelo Azure Resource Manager**. Este método destina-se os utilizadores avançados. Para obter mais informações, consulte [criar um ASE a partir de um modelo][MakeASEfromTemplate].

Um ASE externo tem um VIP público, o que significa que todo o tráfego HTTP/HTTPS para as aplicações no ASE atinge um endereço IP acessível pela internet. Um ASE com um ILB tem um endereço IP na sub-rede utilizada pelo ASE. As aplicações alojadas num ASE de ILB não são expostas diretamente à internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Criar um ASE e um plano do serviço de aplicações em conjunto ##

O plano do serviço de aplicações é um contentor de aplicações. Quando cria uma aplicação no serviço de aplicações, escolha ou crie um plano do serviço de aplicações. Ambientes de serviço de aplicações manter planos de serviço de aplicações e planos do App Service manter aplicações.

Para criar um ASE enquanto pode criar um plano de serviço de aplicações:

1. Na [portal do Azure](https://portal.azure.com/), selecione **criar um recurso** > **Web + móvel** > **aplicação Web**.

    ![Criação de aplicações Web][1]

2. Selecione a sua subscrição. A aplicação e o ASE são criadas nas mesmas subscrições.

3. Selecione ou crie um grupo de recursos. Com grupos de recursos, pode gerir os recursos relacionados do Azure como uma unidade. Grupos de recursos também são úteis quando estabelecer as regras de controlo de acesso baseado em funções para as suas aplicações. Para obter mais informações, consulte a [descrição geral do Azure Resource Manager][ARMOverview].

4. Selecione seu sistema operacional (Windows, Linux ou Docker). 

5. Selecione o plano de serviço de aplicações e, em seguida, selecione **criar novo**. Aplicações web do Linux e aplicações de web do Windows não podem estar no mesmo plano serviço de aplicações, mas podem ser no mesmo ambiente de serviço de aplicações. 

    ![Novo plano do Serviço de Aplicações][2]

6. Na **localização** na lista pendente, selecione a região onde pretende criar o ASE. Se selecionar um ASE existente, não é criado um novo ASE. O plano do serviço de aplicações é criado no ASE que selecionou. 

7. Selecione **escalão de preço**e escolha uma da **Isolated** SKUs de preços. Se escolher uma **Isolated** cartão SKU e uma localização que não é um ASE, um novo ASE é criado nessa localização. Para iniciar o processo de criar um ASE, selecione **selecione**. O **Isolated** SKU está disponível apenas em conjunto com um ASE. Também não é possível utilizar nenhum SKU preço num ASE que **Isolated**. 

    ![Seleção do escalão de preço][3]

8. Introduza o nome para o seu ASE. Este nome é utilizado o nome endereçável para as suas aplicações. Se o nome do ASE for _appsvcenvdemo_, o nome de domínio é *. appsvcenvdemo.p.azurewebsites.net*. Se criar uma aplicação com o nome *mytestapp*, é acessível em mytestapp.appsvcenvdemo.p.azurewebsites.net. Não é possível utilizar o espaço em branco no nome. Se utilizar carateres maiúsculos, o nome de domínio é a versão em minúsculas total esse nome.

    ![Novo nome do plano do serviço de aplicações][4]

9. Especifique os detalhes da rede virtual do Azure. Selecione **criar novo** ou **selecionar existente**. A opção de selecionar uma VNet existente está disponível apenas se tiver uma VNet na região selecionada. Se selecionou **criar novo**, introduza um nome para a VNet. É criada uma nova VNet do Resource Manager com esse nome. Ele usa o espaço de endereços `192.168.250.0/23` na região selecionada. Se selecionou **selecionar existente**, terá de:

    a. Selecione o bloco de endereços da VNet, se tiver mais do que um.

    b. Introduza um novo nome de sub-rede.

    c. Selecione o tamanho da sub-rede. *Não se esqueça de selecionar um tamanho grande o suficiente para acomodar o crescimento futuro do seu ASE.* Recomendamos que `/25`, que tem 128 endereços e pode processar um ASE de tamanho máximo. Não é recomendada a `/28`, por exemplo, uma vez que apenas 16 endereços estão disponíveis. Infraestrutura utiliza endereços, pelo menos, sete e redes do Azure utiliza outra 5. Num `/28` sub-rede, fica apenas com um máximo dimensionamento de 4 instâncias de plano de serviço de aplicações para um ASE externo e apenas 3 instâncias do plano do serviço de aplicações para um ASE de ILB.

    d. Selecione o intervalo IP de sub-rede.

10. Selecione **criar** para criar o ASE. Este processo também cria o plano do serviço de aplicações e a aplicação. O ASE, plano do serviço de aplicações e aplicação são todos na mesma subscrição e também no mesmo grupo de recursos. Se o ASE tem um grupo de recursos separado, ou se precisar de um ASE de ILB, siga os passos para criar um ASE por si só.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Criar um ASE e uma aplicação web do Linux utilizar uma imagem personalizada do Docker em conjunto

1. Na [portal do Azure](https://portal.azure.com/), **criar um recurso** > **Web + móvel** > **aplicação Web para contentores.** 

    ![Criação de aplicações Web][7]

1. Selecione a sua subscrição. A aplicação e o ASE são criadas nas mesmas subscrições.

1. Selecione ou crie um grupo de recursos. Com grupos de recursos, pode gerir os recursos relacionados do Azure como uma unidade. Grupos de recursos também são úteis quando estabelecer as regras de controlo de acesso baseado em funções para as suas aplicações. Para obter mais informações, consulte a [descrição geral do Azure Resource Manager][ARMOverview].

1. Selecione o plano de serviço de aplicações e, em seguida, selecione **criar novo**. Aplicações web do Linux e aplicações de web do Windows não podem estar no mesmo plano serviço de aplicações, mas podem ser no mesmo ambiente de serviço de aplicações. 

    ![Novo plano do Serviço de Aplicações][8]

1. Na **localização** na lista pendente, selecione a região onde pretende criar o ASE. Se selecionar um ASE existente, não é criado um novo ASE. O plano do serviço de aplicações é criado no ASE que selecionou. 

1. Selecione **escalão de preço**e escolha uma da **Isolated** SKUs de preços. Se escolher uma **Isolated** cartão SKU e uma localização que não é um ASE, um novo ASE é criado nessa localização. Para iniciar o processo de criar um ASE, selecione **selecione**. O **Isolated** SKU está disponível apenas em conjunto com um ASE. Também não é possível utilizar nenhum SKU preço num ASE que **Isolated**. 

    ![Seleção do escalão de preço][3]

1. Introduza o nome para o seu ASE. Este nome é utilizado o nome endereçável para as suas aplicações. Se o nome do ASE for _appsvcenvdemo_, o nome de domínio é *. appsvcenvdemo.p.azurewebsites.net*. Se criar uma aplicação com o nome *mytestapp*, é acessível em mytestapp.appsvcenvdemo.p.azurewebsites.net. Não é possível utilizar o espaço em branco no nome. Se utilizar carateres maiúsculos, o nome de domínio é a versão em minúsculas total esse nome.

    ![Novo nome do plano do serviço de aplicações][4]

1. Especifique os detalhes da rede virtual do Azure. Selecione **criar novo** ou **selecionar existente**. A opção de selecionar uma VNet existente está disponível apenas se tiver uma VNet na região selecionada. Se selecionou **criar novo**, introduza um nome para a VNet. É criada uma nova VNet do Resource Manager com esse nome. Ele usa o espaço de endereços `192.168.250.0/23` na região selecionada. Se selecionou **selecionar existente**, terá de:

    a. Selecione o bloco de endereços da VNet, se tiver mais do que um.

    b. Introduza um novo nome de sub-rede.

    c. Selecione o tamanho da sub-rede. *Não se esqueça de selecionar um tamanho grande o suficiente para acomodar o crescimento futuro do seu ASE.* Recomendamos que `/25`, que tem 128 endereços e pode processar um ASE de tamanho máximo. Não é recomendada a `/28`, por exemplo, uma vez que apenas 16 endereços estão disponíveis. Infraestrutura utiliza endereços, pelo menos, sete e redes do Azure utiliza outra 5. Num `/28` sub-rede, fica apenas com um máximo dimensionamento de 4 instâncias de plano de serviço de aplicações para um ASE externo e apenas 3 instâncias do plano do serviço de aplicações para um ASE de ILB.

    d. Selecione o intervalo IP de sub-rede.

1.  Selecione "Configurar o contentor".
    * Introduza o nome de imagem personalizada (pode usar o registo de contentor do Azure, o Docker Hub e o seu próprio registo privado). Se não quiser utilizar o seu próprio contentor personalizado, pode apenas traga o seu código e utilizar uma imagem incorporada com o serviço de aplicações no Linux, com as instruções descritas acima. 

    ![Configurar o contentor][9]

1. Selecione **criar** para criar o ASE. Este processo também cria o plano do serviço de aplicações e a aplicação. O ASE, plano do serviço de aplicações e aplicação são todos na mesma subscrição e também no mesmo grupo de recursos. Se o ASE tem um grupo de recursos separado, ou se precisar de um ASE de ILB, siga os passos para criar um ASE por si só.


## <a name="create-an-ase-by-itself"></a>Criar um ASE por si só ##

Se criar um autónomo do ASE, ele não tem nada nele. Um ASE vazio ainda incorre um encargo mensal da infraestrutura. Siga estes passos para criar um ASE com um ILB ou criar um ASE no seu próprio grupo de recursos. Depois de criar o ASE, pode criar aplicações no mesmo utilizando o processo normal. Selecione o seu ASE novo como a localização.

1. Procurar no Azure Marketplace **ambiente do serviço de aplicações**, ou selecione **criar um recurso** > **Web móveis** > **aplicação Ambiente de serviço**. 

1. Introduza o nome do seu ASE. Este nome é utilizado para as aplicações que criou no ASE. Se o nome for *mynewdemoase*, é o nome de subdomínio *. mynewdemoase.p.azurewebsites.net*. Se criar uma aplicação com o nome *mytestapp*, é acessível em mytestapp.mynewdemoase.p.azurewebsites.net. Não é possível utilizar o espaço em branco no nome. Se utilizar carateres maiúsculos, o nome de domínio é a total versão em minúsculas do nome. Se usar um ILB, o nome do ASE não utilizado no subdomínio, mas em vez disso, é declarado explicitamente durante a criação do ASE.

    ![Nomenclatura do ASE][5]

1. Selecione a sua subscrição. Esta subscrição é também um que utilizam todas as aplicações no ASE. Não é possível colocar o seu ASE numa VNet que está em outra subscrição.

1. Selecione ou especifique um novo grupo de recursos. O grupo de recursos utilizado para o seu ASE tem de ser o mesmo que é utilizado para a sua VNet. Se selecionar uma VNet já existente, a seleção de grupo de recursos para o seu ASE é atualizada para refletir que da sua VNet. *Pode criar um ASE com um grupo de recursos que é diferente do grupo de recursos de VNet, se utilizar um modelo do Resource Manager.* Para criar um ASE a partir de um modelo, veja [criar um ambiente de serviço de aplicações a partir de um modelo][MakeASEfromTemplate].

    ![Seleção de grupos de recursos][6]

1. Selecione o seu VNet e a localização. Pode criar uma nova VNet ou selecionar uma VNet existente: 

    * Se selecionar uma nova VNet, pode especificar um nome e a localização. 
    
    * A nova VNet tem o endereço intervalo 192.168.250.0/23 e uma sub-rede com o nome predefinido. A sub-rede é definida como 192.168.250.0/24. Só pode selecionar uma VNet do Resource Manager. O **tipo de VIP** seleção determina se o seu ASE pode ser acedido diretamente da internet (externo) ou use um ILB. Para saber mais sobre estas opções, veja [criar e utilizar um balanceador de carga interno com um ambiente de serviço de aplicações][MakeILBASE]. 

      * Se selecionou **externo** para o **tipo de VIP**, pode selecionar quantos endereços IP externos o sistema é criado com finalidades de SSL baseado em IP. 
    
      * Se selecionou **interno** para o **tipo de VIP**, tem de especificar o domínio que utiliza o seu ASE. Pode implementar um ASE numa VNet que utiliza os intervalos de endereços públicos ou privados. Para utilizar uma VNet com um intervalo de endereços públicos, terá de criar a VNet antes do tempo. 
    
    * Se selecionar uma VNet já existente, é criada uma nova sub-rede quando a criação do ASE. *Não é possível utilizar uma sub-rede previamente criada no portal. Pode criar um ASE com uma sub-rede existente, se utilizar um modelo do Resource Manager.* Para criar um ASE a partir de um modelo, veja [criar um ambiente de serviço de aplicações a partir de um modelo][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicações v1 ##

Pode ainda criar instâncias da primeira versão do ambiente de serviço de aplicações (ASEv1). Para iniciar o processo, pesquisar no Marketplace **ambiente do serviço de aplicações v1**. Criar o ASE da mesma forma que criar o ASE de autónomo. Quando estiver concluído, o seu ASEv1 tem dois front-ends e duas funções de trabalho. Com o ASEv1, tem de gerir os front-ends e trabalhos. Eles não serão adicionados automaticamente quando cria seus planos de serviço de aplicações. Os front-ends atuam como pontos finais de HTTP/HTTPS e enviar o tráfego para os operadores. As funções de trabalho são as funções que alojam as suas aplicações. Pode ajustar a quantidade de front-ends e trabalhos depois de criar o seu ASE. 

Para saber mais sobre o ASEv1, veja [introdução para a v1 do ambiente de serviço de aplicações][ASEv1Intro]. Para obter mais informações sobre como aumentar, gerir e monitorizar o ASEv1, veja [como configurar um ambiente de serviço de aplicações][ConfigureASEv1].

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
