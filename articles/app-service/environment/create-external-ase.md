---
title: Criar uma ASE externa
description: Aprenda a criar um ambiente de Serviço de Aplicações com uma aplicação no mesmo, ou crie uma ASE autónoma (vazia).
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6c4838e3226b91cbb5d6f86b83266a986418c120
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75430517"
---
# <a name="create-an-external-app-service-environment"></a>Criar um ambiente de serviço de aplicações externas

O Ambiente de Serviço de Aplicações do Azure é uma implementação do Serviço de Aplicações do Azure numa sub-rede de uma rede virtual do Azure (VNet).

> [!NOTE]
> Cada ambiente de serviço de aplicações tem um IP virtual (VIP), que pode ser usado para contactar o Ambiente de Serviço de Aplicações.

Existem duas formas de implementar um Ambiente de Serviço de Aplicações (ASE):

- Com um VIP num endereço IP externo, muitas vezes chamado ASE Externo.
- Com o VIP num endereço IP interno, muitas vezes chamado de ILB ASE porque o ponto final interno é um Balancer de Carga Interna (ILB).

Este artigo mostra-lhe como criar uma ASE Externa. Para uma visão geral da ASE, consulte Uma introdução ao Ambiente do Serviço de [Aplicações.][Intro] Para obter informações sobre como criar um ILB ASE, consulte [Criar e utilizar um ILB ASE][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Antes de criar a sua ASE

Depois de criar a sua ASE, não pode alterar o seguinte:

- Localização
- Subscrição
- Grupo de recursos
- VNet usado
- Subnet utilizado
- Tamanho da subnet

> [!NOTE]
> Quando escolher um VNet e especificar uma subnet, certifique-se de que é grande o suficiente para acomodar as necessidades de crescimento e escalação futuras. Recomendamos um `/24` tamanho de 256 endereços.
>

## <a name="three-ways-to-create-an-ase"></a>Três formas de criar uma ASE

Há três formas de criar uma ASE:

- Ao criar um plano de Serviço de **Aplicações.** Este método cria a ASE e o plano de Serviço de Aplicações num só passo.
- **Como uma ação autónoma.** Este método cria uma ASE autónoma, que é uma ASE sem nada. Este método é um processo mais avançado para criar uma ASE. Usa-se para criar uma ASE com um ILB.
- **A partir de um modelo de Gestor de Recursos Azure**. Este método destina-se a utilizadores avançados. Para mais informações, consulte [Criar uma ASE a partir de um modelo][MakeASEfromTemplate].

Uma ASE Externa tem um VIP público, o que significa que todo o tráfego HTTP/HTTPS para as aplicações na ASE atinge um endereço IP acessível à Internet. Um ASE com um ILB tem um endereço IP da sub-rede utilizado pela ASE. As aplicações hospedadas num ILB ASE não estão expostas diretamente à internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Criar um ASE e um plano de serviço de aplicações em conjunto

O plano de Serviço de Aplicações é um recipiente de aplicações. Quando cria uma aplicação no App Service, escolhe ou cria um plano de Serviço de Aplicações. Os Ambientes de Serviço de Aplicações possuem planos de Serviço de Aplicações e os planos do App Service possuem aplicações.

Para criar uma ASE enquanto cria um plano de Serviço de Aplicações:

1. No [portal Azure,](https://portal.azure.com/)selecione **Criar um recurso** > **Web + Mobile** > **Web App**.

    ![Criação de aplicativos web][1]

2. Selecione a sua subscrição. A aplicação e a ASE são criadas nas mesmas subscrições.

3. Selecione ou crie um grupo de recursos. Com grupos de recursos, você pode gerir os recursos azure relacionados como uma unidade. Os grupos de recursos também são úteis quando estabelece regras de Controlo de Acesso baseados em funções para as suas apps. Para obter mais informações, veja a [Descrição geral do Azure Resource Manager][ARMOverview].

4. Selecione o seu SISTEMA (Windows, Linux ou Docker). 

5. Selecione o plano de serviço de aplicações e, em seguida, selecione **Criar Novo**. As aplicações web do Linux e as aplicações web do Windows não podem estar no mesmo Plano de Serviço de Aplicações, mas podem estar no mesmo App Service Environment. 

    ![Novo plano de serviço de aplicações][2]

6. Na **Location** lista de desacato seletiva, selecione a região onde pretende criar a ASE. Se selecionar uma ASE existente, não é criada uma nova ASE. O plano de Serviço de Aplicações é criado na ASE que selecionou. 

7. Selecione **o nível**de preços , e escolha um dos Preços **Isolados** SKUs. Se escolher um cartão SKU **isolado** e um local que não seja uma ASE, uma nova ASE é criada nesse local. Para iniciar o processo para criar uma ASE, selecione **Select**. O SKU **isolado** está disponível apenas em conjunto com uma ASE. Também não pode utilizar outros preços SKU numa ASE que não seja **isolado**. 

    ![Seleção de níveis de preços][3]

8. Insira o nome para a sua ASE. Este nome é utilizado no nome endereçado para as suas apps. Se o nome da ASE for _appsvcenvdemo,_ o nome de domínio é *.appsvcenvdemo.p.azurewebsites.net*. Se criar uma aplicação chamada *mytestapp,* é endereçada em mytestapp.appsvcenvdemo.p.azurewebsites.net. Não pode usar o espaço branco no nome. Se utilizar caracteres maiúsculos, o nome de domínio é a versão minúscula total desse nome.

    ![Nome do novo plano do serviço de aplicações][4]

9. Especifique os detalhes de rede virtual Azure. Selecione **criar novo** ou **selecione existindo**. A opção de selecionar um VNet existente só está disponível se tiver um VNet na região selecionada. Se selecionar **Criar Novo,** introduza um nome para o VNet. É criado um novo Gestor de Recursos VNet com esse nome. Utiliza o espaço `192.168.250.0/23` de endereços na região selecionada. Se selecionar **selecione Existo,** tem de:

    a. Selecione o bloco de endereços VNet, se tiver mais de um.

    b. Introduza um novo nome de sub-rede.

    c. Selecione o tamanho da sub-rede. *Lembre-se de selecionar um tamanho grande o suficiente para acomodar o crescimento futuro da sua ASE.* Recomendamos `/24`, que tem 128 endereços e pode manusear uma ASE de tamanho máximo. Não recomendamos, `/28`por exemplo, porque apenas 16 endereços estão disponíveis. A infraestrutura utiliza pelo menos sete endereços e o Azure Networking utiliza mais 5. Numa `/28` sub-rede, fica com uma escala máxima de 4 instâncias de plano de app service para uma ASE Externa e apenas 3 instâncias de plano de app para um ILB ASE.

    d. Selecione a gama IP da sub-rede.

10. Selecione **Criar** para criar a ASE. Este processo também cria o plano de Serviço de Aplicações e a app. A ASE, o plano de Serviço de Aplicações e a app estão todos sob a mesma subscrição e também no mesmo grupo de recursos. Se a sua ASE precisar de um grupo de recursos separadoou se precisar de um ASE ILB, siga os passos para criar uma ASE por si só.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Crie uma ASE e uma aplicação web Linux usando uma imagem personalizada do Docker em conjunto

1. No [portal Azure, Crie](https://portal.azure.com/)uma Web de **recursos** > + aplicação**web móvel** > **para contentores.** 

    ![Criação de aplicativos web][7]

1. Selecione a sua subscrição. A aplicação e a ASE são criadas nas mesmas subscrições.

1. Selecione ou crie um grupo de recursos. Com grupos de recursos, você pode gerir os recursos azure relacionados como uma unidade. Os grupos de recursos também são úteis quando estabelece regras de Controlo de Acesso baseados em funções para as suas apps. Para obter mais informações, veja a [Descrição geral do Azure Resource Manager][ARMOverview].

1. Selecione o plano de serviço de aplicações e, em seguida, selecione **Criar Novo**. As aplicações web do Linux e as aplicações web do Windows não podem estar no mesmo Plano de Serviço de Aplicações, mas podem estar no mesmo App Service Environment. 

    ![Novo plano de serviço de aplicações][8]

1. Na **Location** lista de desacato seletiva, selecione a região onde pretende criar a ASE. Se selecionar uma ASE existente, não é criada uma nova ASE. O plano de Serviço de Aplicações é criado na ASE que selecionou. 

1. Selecione **o nível**de preços , e escolha um dos Preços **Isolados** SKUs. Se escolher um cartão SKU **isolado** e um local que não seja uma ASE, uma nova ASE é criada nesse local. Para iniciar o processo para criar uma ASE, selecione **Select**. O SKU **isolado** está disponível apenas em conjunto com uma ASE. Também não pode utilizar outros preços SKU numa ASE que não seja **isolado**. 

    ![Seleção de níveis de preços][3]

1. Insira o nome para a sua ASE. Este nome é utilizado no nome endereçado para as suas apps. Se o nome da ASE for _appsvcenvdemo,_ o nome de domínio é *.appsvcenvdemo.p.azurewebsites.net*. Se criar uma aplicação chamada *mytestapp,* é endereçada em mytestapp.appsvcenvdemo.p.azurewebsites.net. Não pode usar o espaço branco no nome. Se utilizar caracteres maiúsculos, o nome de domínio é a versão minúscula total desse nome.

    ![Nome do novo plano do serviço de aplicações][4]

1. Especifique os detalhes de rede virtual Azure. Selecione **criar novo** ou **selecione existindo**. A opção de selecionar um VNet existente só está disponível se tiver um VNet na região selecionada. Se selecionar **Criar Novo,** introduza um nome para o VNet. É criado um novo Gestor de Recursos VNet com esse nome. Utiliza o espaço `192.168.250.0/23` de endereços na região selecionada. Se selecionar **selecione Existo,** tem de:

    a. Selecione o bloco de endereços VNet, se tiver mais de um.

    b. Introduza um novo nome de sub-rede.

    c. Selecione o tamanho da sub-rede. *Lembre-se de selecionar um tamanho grande o suficiente para acomodar o crescimento futuro da sua ASE.* Recomendamos `/24`, que tem 128 endereços e pode manusear uma ASE de tamanho máximo. Não recomendamos, `/28`por exemplo, porque apenas 16 endereços estão disponíveis. A infraestrutura utiliza pelo menos sete endereços e o Azure Networking utiliza mais 5. Numa `/28` sub-rede, fica com uma escala máxima de 4 instâncias de plano de app service para uma ASE Externa e apenas 3 instâncias de plano de app para um ILB ASE.

    d. Selecione a gama IP da sub-rede.

1.  Selecione "Configure Container".
    * Introduza o seu nome de imagem personalizado (pode utilizar o Registo de Contentores Azure, o Docker Hub e o seu próprio registo privado). Se não quiser usar o seu próprio recipiente personalizado, pode simplesmente trazer o seu código e utilizar uma imagem incorporada com o Serviço de Aplicações no Linux, utilizando as instruções acima. 

    ![Configure recipiente][9]

1. Selecione **Criar** para criar a ASE. Este processo também cria o plano de Serviço de Aplicações e a app. A ASE, o plano de Serviço de Aplicações e a app estão todos sob a mesma subscrição e também no mesmo grupo de recursos. Se a sua ASE precisar de um grupo de recursos separadoou se precisar de um ASE ILB, siga os passos para criar uma ASE por si só.


## <a name="create-an-ase-by-itself"></a>Criar uma ASE por si só

Se criares uma ASE autónoma, não tem nada. Uma ASE vazia ainda incorre numa carga mensal para a infraestrutura. Siga estes passos para criar uma ASE com um ILB ou para criar uma ASE no seu próprio grupo de recursos. Depois de criar a sua ASE, pode criar aplicações no mesmo utilizando o processo normal. Selecione a sua nova ASE como localização.

1. Pesquise o Azure Marketplace para obter ambiente de serviço de **aplicações,** ou selecione **Criar um dispositivo** > **Web Mobile** > **App Service Environment**. 

1. Insira o nome da sua ASE. Este nome é utilizado para as aplicações criadas na ASE. Se o nome for *mynewdemoase,* o nome do subdomínio é *.mynewdemoase.p.azurewebsites.net*. Se criar uma aplicação chamada *mytestapp,* é endereçada em mytestapp.mynewdemoase.p.azurewebsites.net. Não pode usar o espaço branco no nome. Se utilizar caracteres maiúsculos, o nome de domínio é a versão minúscula total do nome. Se utilizar um ILB, o seu nome ASE não é utilizado no seu subdomínio, mas é explicitamente indicado durante a criação da ASE.

    ![Nomeação aASE][5]

1. Selecione a sua subscrição. Esta subscrição é também a que todas as aplicações na ASE utilizam. Não pode colocar a Sua ASE num VNet que está noutra subscrição.

1. Selecione ou especifique um novo grupo de recursos. O grupo de recursos utilizado para a sua ASE deve ser o mesmo que é usado para o seu VNet. Se selecionar um VNet existente, a seleção do grupo de recursos para a sua ASE é atualizada para refletir a do seu VNet. *Você pode criar uma ASE com um grupo de recursos que é diferente do grupo de recursos VNet se você usar um modelo de Gestor de Recursos.* Para criar uma ASE a partir de um modelo, consulte Criar um ambiente de Serviço de [Aplicações a partir de um modelo][MakeASEfromTemplate].

    ![Seleção de grupos de recursos][6]

1. Selecione o seu VNet e a sua localização. Pode criar um novo VNet ou selecionar um VNet existente: 

    * Se selecionar uma nova VNet, pode especificar um nome e a localização. 
    
    * O novo VNet tem o intervalo de endereços 192.168.250.0/23 e uma subnet chamada padrão. A sub-rede é definida como 192.168.250.0/24. Só pode selecionar um VNet gestor de recursos. A seleção do **Tipo VIP** determina se a sua ASE pode ser diretamente acedida a partir da internet (Externa) ou se utiliza um ILB. Para saber mais sobre estas opções, consulte [Criar e utilizar um equilibrador de carga interno com um ambiente de Serviço de Aplicações.][MakeILBASE] 

      * Se selecionar **External** para o **Tipo VIP,** pode selecionar quantos endereços IP externos o sistema é criado para fins SSL baseados em IP. 
    
      * Se selecionar **Internal** para o **Tipo VIP,** deve especificar o domínio que a sua ASE utiliza. Pode implantar uma ASE num VNet que utilize intervalos de endereços públicos ou privados. Para utilizar um VNet com um intervalo de endereços públicos, precisa de criar o VNet com antecedência. 
    
    * Se selecionar um VNet existente, é criada uma nova subnet quando a ASE é criada. *Não se pode usar uma sub-rede pré-criada no portal. Pode criar uma ASE com uma subrede existente se utilizar um modelo de Gestor de Recursos.* Para criar uma ASE a partir de um modelo, consulte Criar um Ambiente de Serviço de [Aplicações a partir de um modelo][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicações v1

Ainda pode criar instâncias da primeira versão do App Service Environment (ASEv1). Para iniciar este processo, procure no Marketplace o Ambiente de Serviço de **Aplicações v1**. Cria-se a ASE da mesma forma que cria a ASE autónoma. Quando estiver terminado, o seu ASEv1 tem duas frentes e dois trabalhadores. Com o ASEv1, tens de gerir as extremidades dianteiras e os trabalhadores. Não são adicionados automaticamente quando cria os seus planos de Serviço de Aplicações. As extremidades dianteiras funcionam como os pontos finais HTTP/HTTPS e enviam tráfego para os trabalhadores. Os trabalhadores são os papéis que acolhem as suas apps. Pode ajustar a quantidade de extremidades dianteiras e trabalhadores depois de criar a sua ASE. 

Para saber mais sobre o ASEv1, consulte Introdução ao Ambiente de [Serviço de Aplicações v1][ASEv1Intro]. Para obter mais informações sobre escala, gestão e monitorização asEv1, consulte [como configurar um Ambiente][ConfigureASEv1]de Serviço de Aplicação .

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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
