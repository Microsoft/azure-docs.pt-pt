---
title: Criar um ASE externo
description: Aprenda a criar um ambiente de Serviço de Aplicações com uma aplicação ou crie um ASE autónomo (vazio).
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c953c31792b8d01199d409cbd91124138a6ebb15
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927452"
---
# <a name="create-an-external-app-service-environment"></a>Criar um ambiente de Serviço de Aplicações Externas

O Ambiente de Serviço de Aplicações do Azure é uma implementação do Serviço de Aplicações do Azure numa sub-rede de uma rede virtual do Azure (VNet).

> [!NOTE]
> Cada Ambiente de Serviço de Aplicações tem um IP virtual (VIP), que pode ser usado para contactar o Ambiente de Serviço de Aplicações.

Existem duas formas de implementar um Ambiente de Serviço de Aplicações (ASE):

- Com um VIP num endereço IP externo, muitas vezes chamado ASE Externo.
- Com o VIP num endereço IP interno, muitas vezes chamado de ILB ASE porque o ponto final interno é um Balanceador de Carga Interna (ILB).

Este artigo mostra-lhe como criar um ASE Externo. Para uma visão geral do ASE, consulte [uma introdução ao Ambiente de Serviço de Aplicações.][Intro] Para obter informações sobre como criar um ILB ASE, consulte [Criar e utilizar um ILB ASE][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Antes de criar o seu ASE

Depois de criar o seu ASE, não pode alterar o seguinte:

- Localização
- Subscrição
- Grupo de recursos
- VNet usado
- Sub-rede utilizada
- Tamanho da sub-rede

> [!NOTE]
> Quando escolher um VNet e especificar uma sub-rede, certifique-se de que é grande o suficiente para acomodar necessidades futuras de crescimento e escala. Recomendamos um tamanho `/24` de 256 endereços.
>

## <a name="three-ways-to-create-an-ase"></a>Três formas de criar um ASE

Há três formas de criar um ASE:

- **Ao criar um plano de Serviço de Aplicações.** Este método cria o plano ASE e o Serviço de Aplicações num só passo.
- **Como uma ação autónoma.** Este método cria um ASE autónomo, que é um ASE sem nada nele. Este método é um processo mais avançado para criar um ASE. Usa-se para criar um ASE com um ILB.
- **A partir de um modelo de Gestor de Recursos Azure.** Este método destina-se a utilizadores avançados. Para obter mais informações, consulte [Criar um ASE a partir de um modelo.][MakeASEfromTemplate]

Um ASE externo tem um VIP público, o que significa que todo o tráfego HTTP/HTTPS para as aplicações no ASE atinge um endereço IP acessível à Internet. Um ASE com um ILB tem um endereço IP a partir da sub-rede utilizada pelo ASE. As aplicações hospedadas num ILB ASE não estão expostas diretamente à internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Criar um plano ASE e um App Service em conjunto

O plano de Serviço de Aplicações é um recipiente de aplicações. Quando cria uma aplicação no Serviço de Aplicações, escolhe ou cria um plano de Serviço de Aplicações. Os Ambientes de Serviço de Aplicações detêm planos de Serviço de Aplicações e os planos do Serviço de Aplicações detêm aplicações.

Para criar um ASE enquanto cria um plano de Serviço de Aplicações:

1. No [portal Azure,](https://portal.azure.com/)selecione **Criar um recurso** Web +  >  **Mobile**  >  **Web App** .

    ![Screenshot do portal Azure mostrando Web + Mobile selecionado no Azure Marketplace e o ecrã para criar uma nova Web App aberta à direita.][1]

2. Selecione a sua subscrição. A aplicação e o ASE são criados nas mesmas subscrições.

3. Selecione ou crie um grupo de recursos. Com grupos de recursos, pode gerir recursos Azure relacionados como uma unidade. Os grupos de recursos também são úteis quando estabelece as regras Role-Based Controlo de Acesso para as suas apps. Para obter mais informações, veja a [Descrição geral do Azure Resource Manager][ARMOverview].

4. Selecione o seu SISTEMA (Windows, Linux ou Docker). 

5. Selecione o plano de Serviço de Aplicações e, em seguida, **selecione Criar Novo** . As aplicações web Linux e as aplicações web do Windows não podem estar no mesmo Plano de Serviço de Aplicações, mas podem estar no mesmo Ambiente de Serviço de Aplicações. 

    ![Screenshot do portal Azure mostrando o painel de aplicações web, o painel de plano de serviço da App e o painel do Novo Plano de Serviço de Aplicações aberto.][2]

6. Na lista de pontos de entrega da **Localização,** selecione a região onde pretende criar o ASE. Se selecionar um ASE existente, não é criado um novo ASE. O plano de Serviço de Aplicações é criado no ASE que selecionou. 

7. Selecione **o nível de preços** , e escolha um dos SKUs de preços **isolados.** Se escolher um cartão SKU **isolado** e um local que não seja um ASE, um novo ASE é criado nesse local. Para iniciar o processo para criar um ASE, **selecione Select** . O SKU **isolado** só está disponível em conjunto com um ASE. Também não pode utilizar qualquer outro SKU de preços num ASE diferente do **Isolado.** 

    ![Seleção de preços][3]

8. Insira o nome para o seu ASE. Este nome é utilizado no nome endereçada para as suas aplicações. Se o nome do ASE for _appsvcenvdemo,_ o nome de domínio é *.appsvcenvdemo.p.azurewebsites.net* . Se criar uma aplicação chamada *MyTestApp,* é endereçada a mytestapp.appsvcenvdemo.p.azurewebsites.net. Não pode usar espaço branco no nome. Se utilizar caracteres maiúsculas, o nome de domínio é a versão maiúscula total desse nome.

    ![Nome do novo plano do novo serviço de aplicações][4]

9. Especifique os seus dados de rede virtual Azure. Selecione **Criar Novo** ou **Selecione Existing.** A opção de selecionar um VNet existente só está disponível se tiver um VNet na região selecionada. Se selecionar **Create New,** insira um nome para o VNet. É criado um novo Gestor de Recursos VNet com esse nome. Utiliza o espaço de endereço `192.168.250.0/23` na região selecionada. Se selecionar **Selecione Existir,** tem de o fazer:

    a. Selecione o bloco de endereços VNet, se tiver mais de um.

    b. Introduza um novo nome de sub-rede.

    c. Selecione o tamanho da sub-rede. *Lembre-se de selecionar um tamanho suficientemente grande para acomodar o crescimento futuro do seu ASE.* `/24`Recomendamos, que tem 256 endereços e pode manusear um ASE de tamanho máximo. Não `/28` recomendamos, por exemplo, porque apenas 16 endereços estão disponíveis. A infraestrutura utiliza pelo menos sete endereços e a Azure Networking utiliza outros 5. Numa `/28` sub-rede, fica com uma escala máxima de 4 instâncias do plano de aplicações para um ASE Externo e apenas 3 instâncias do plano de Serviço de Aplicações para um ILB ASE.

    d. Selecione o intervalo IP da sub-rede.

10. Selecione **Criar** para criar o ASE. Este processo também cria o plano de Serviço de Aplicações e a app. O ase, o plano de Serviço de Aplicações e a aplicação estão todos sob a mesma subscrição e também no mesmo grupo de recursos. Se o seu ASE precisar de um grupo de recursos separado ou se precisar de um ILB ASE, siga os passos para criar um ASE por si só.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Crie uma aplicação web ASE e Linux usando uma imagem personalizada do Docker em conjunto

1. No [portal Azure](https://portal.azure.com/), Crie uma Web **de Recursos** +  >  Aplicação Web **Móvel**  >  **para Contentores.** 

    ![Screenshot do portal Azure mostrando Web + Mobile selecionado no Azure Marketplace e o painel web para contentores aberto à direita.][7]

1. Selecione a sua subscrição. A aplicação e o ASE são criados nas mesmas subscrições.

1. Selecione ou crie um grupo de recursos. Com grupos de recursos, pode gerir recursos Azure relacionados como uma unidade. Os grupos de recursos também são úteis quando estabelece as regras Role-Based Controlo de Acesso para as suas apps. Para obter mais informações, veja a [Descrição geral do Azure Resource Manager][ARMOverview].

1. Selecione o plano de Serviço de Aplicações e, em seguida, **selecione Criar Novo** . As aplicações web Linux e as aplicações web do Windows não podem estar no mesmo Plano de Serviço de Aplicações, mas podem estar no mesmo Ambiente de Serviço de Aplicações. 

    ![Screenshot do portal Azure mostrando a aplicação web para o painel de contentores, o painel de plano de serviço da App e o painel do Novo Plano de Serviço de Aplicações abertos.][8]

1. Na lista de pontos de entrega da **Localização,** selecione a região onde pretende criar o ASE. Se selecionar um ASE existente, não é criado um novo ASE. O plano de Serviço de Aplicações é criado no ASE que selecionou. 

1. Selecione **o nível de preços** , e escolha um dos SKUs de preços **isolados.** Se escolher um cartão SKU **isolado** e um local que não seja um ASE, um novo ASE é criado nesse local. Para iniciar o processo para criar um ASE, **selecione Select** . O SKU **isolado** só está disponível em conjunto com um ASE. Também não pode utilizar qualquer outro SKU de preços num ASE diferente do **Isolado.** 

    ![Seleção de preços][3]

1. Insira o nome para o seu ASE. Este nome é utilizado no nome endereçada para as suas aplicações. Se o nome do ASE for _appsvcenvdemo,_ o nome de domínio é *.appsvcenvdemo.p.azurewebsites.net* . Se criar uma aplicação chamada *MyTestApp,* é endereçada a mytestapp.appsvcenvdemo.p.azurewebsites.net. Não pode usar espaço branco no nome. Se utilizar caracteres maiúsculas, o nome de domínio é a versão maiúscula total desse nome.

    ![Nome do novo plano do novo serviço de aplicações][4]

1. Especifique os seus dados de rede virtual Azure. Selecione **Criar Novo** ou **Selecione Existing.** A opção de selecionar um VNet existente só está disponível se tiver um VNet na região selecionada. Se selecionar **Create New,** insira um nome para o VNet. É criado um novo Gestor de Recursos VNet com esse nome. Utiliza o espaço de endereço `192.168.250.0/23` na região selecionada. Se selecionar **Selecione Existir,** tem de o fazer:

    a. Selecione o bloco de endereços VNet, se tiver mais de um.

    b. Introduza um novo nome de sub-rede.

    c. Selecione o tamanho da sub-rede. *Lembre-se de selecionar um tamanho suficientemente grande para acomodar o crescimento futuro do seu ASE.* `/24`Recomendamos, que tem 128 endereços e pode manusear um ASE de tamanho máximo. Não `/28` recomendamos, por exemplo, porque apenas 16 endereços estão disponíveis. A infraestrutura utiliza pelo menos sete endereços e a Azure Networking utiliza outros 5. Numa `/28` sub-rede, fica com uma escala máxima de 4 instâncias do plano de aplicações para um ASE Externo e apenas 3 instâncias do plano de Serviço de Aplicações para um ILB ASE.

    d. Selecione o intervalo IP da sub-rede.

1.  Selecione "Configure Container".
    * Insira o seu nome de imagem personalizado (pode utilizar o Registo de Contentores Azure, o Docker Hub e o seu próprio registo privado). Se não quiser utilizar o seu próprio recipiente personalizado, pode simplesmente trazer o seu código e usar uma imagem incorporada com o Serviço de Aplicações no Linux, utilizando as instruções acima. 

    ![Recipiente de configuração][9]

1. Selecione **Criar** para criar o ASE. Este processo também cria o plano de Serviço de Aplicações e a app. O ase, o plano de Serviço de Aplicações e a aplicação estão todos sob a mesma subscrição e também no mesmo grupo de recursos. Se o seu ASE precisar de um grupo de recursos separado ou se precisar de um ILB ASE, siga os passos para criar um ASE por si só.


## <a name="create-an-ase-by-itself"></a>Criar um ASE por si só

Se criar um AUTÓNOMO ASE, não tem nada. Um ASE vazio ainda incorre numa taxa mensal para a infraestrutura. Siga estes passos para criar um ASE com um ILB ou para criar um ASE no seu próprio grupo de recursos. Depois de criar o seu ASE, pode criar aplicações no mesmo utilizando o processo normal. Selecione o seu novo ASE como a localização.

1. Pesse no Mercado Azure para **o Ambiente de Serviço de Aplicações,** ou selecione Criar um Ambiente de Serviço de Aplicações Web Mobile de **Create a resource**  >  **Web Mobile**  >  **App Service Environment** recursos. 

1. Insira o nome do seu ASE. Este nome é utilizado para as aplicações criadas na ASE. Se o nome for *mynewdemoase,* o nome do subdomínio é *.mynewdemoase.p.azurewebsites.net* . Se criar uma aplicação chamada *MyTestApp,* é endereçada a mytestapp.mynewdemoase.p.azurewebsites.net. Não pode usar espaço branco no nome. Se utilizar caracteres maiúsculas, o nome de domínio é a versão maiúscula total do nome. Se utilizar um ILB, o seu nome ASE não é usado no seu subdomínio, mas é explicitamente indicado durante a criação da ASE.

    ![Nomeação ASE][5]

1. Selecione a sua subscrição. Esta subscrição é também a que todas as aplicações no ase usam. Não pode colocar o seu ASE num VNet que está em outra subscrição.

1. Selecione ou especifique um novo grupo de recursos. O grupo de recursos utilizado para o seu ASE deve ser o mesmo que é usado para o seu VNet. Se selecionar um VNet existente, a seleção do grupo de recursos para o seu ASE é atualizada para refletir a do seu VNet. *Pode criar um ASE com um grupo de recursos diferente do grupo de recursos VNet se utilizar um modelo de Gestor de Recursos.* Para criar um ASE a partir de um modelo, consulte [criar um ambiente de Serviço de Aplicação a partir de um modelo][MakeASEfromTemplate].

    ![Seleção de grupos de recursos][6]

1. Selecione o seu VNet e a sua localização. Pode criar um novo VNet ou selecionar um VNet existente: 

    * Se selecionar uma nova VNet, pode especificar um nome e a localização. 
    
    * O novo VNet tem o intervalo de endereços 192.168.250.0/23 e um sub-redes nomeado predefinido. A sub-rede é definida como 192.168.250.0/24. Só é possível selecionar um VNet do Gestor de Recursos. A seleção **do Tipo VIP** determina se o seu ASE pode ser acedido diretamente a partir da internet (Externa) ou se utiliza um ILB. Para saber mais sobre estas opções, consulte [Criar e utilizar um equilibrador de carga interno com um ambiente de Serviço de Aplicação.][MakeILBASE] 

      * Se selecionar **External** para o **Tipo VIP,** pode selecionar quantos endereços IP externos o sistema foi criado para fins SSL baseados em IP. 
    
      * Se selecionar **Interno** para o **Tipo VIP,** tem de especificar o domínio que o seu ASE utiliza. Pode colocar um ASE num VNet que utilize intervalos de endereços públicos ou privados. Para utilizar um VNet com um alcance de endereço público, é necessário criar o VNet com antecedência. 
    
    * Se selecionar um VNet existente, é criada uma nova sub-rede quando o ASE é criado. *Não pode usar uma sub-rede pré-criada no portal. Pode criar um ASE com uma sub-rede existente se utilizar um modelo de Gestor de Recursos.* Para criar um ASE a partir de um modelo, consulte [Criar um Ambiente de Serviço de Aplicação a partir de um modelo][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicações v1

Ainda pode criar instâncias da primeira versão do App Service Environment (ASEv1). Para iniciar este processo, pesse o Marketplace for **App Service Environment v1** . Cria-se o ASE da mesma forma que cria o ASE autónomo. Quando estiver terminado, o seu ASEv1 tem duas extremidades dianteiras e dois trabalhadores. Com o ASEv1, tens de gerir as extremidades dianteiras e os trabalhadores. Não são automaticamente adicionados quando cria os seus planos de Serviço de Aplicações. As extremidades dianteiras funcionam como pontos finais HTTP/HTTPS e enviam tráfego para os trabalhadores. Os trabalhadores são os papéis que acolhem as suas apps. Pode ajustar a quantidade de extremidades dianteiras e trabalhadores depois de criar o seu ASE. 

Para saber mais sobre o ASEv1, consulte [Introdução ao Ambiente de Serviço de Aplicações v1][ASEv1Intro]. Para obter mais informações sobre dimensionamento, gestão e monitorização do ASEv1, consulte [Como configurar um Ambiente de Serviço de Aplicações][ConfigureASEv1].

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
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
