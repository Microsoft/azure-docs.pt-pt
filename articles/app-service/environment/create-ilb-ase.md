---
title: Criar Balanceador de carga interno com o ambiente de serviço de aplicações - Azure
description: Detalhes sobre como criar e utilizar um Ambiente de Serviço de Aplicações do Azure isolado da Internet
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5b05755502ad5836a21080a122d2e1721825f10c
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734691"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Criar e utilizar um balanceador de carga interno ambiente de serviço de aplicações 

O ambiente de serviço de aplicações do Azure é uma implementação do serviço de aplicações do Azure numa sub-rede numa rede virtual do Azure (VNet). Existem duas formas de implementar um Ambiente de Serviço de Aplicações (ASE): 

- Com um VIP num endereço IP externo, muitas vezes chamado ASE Externo.
- Com um VIP num endereço IP interno, muitas vezes chamado ASE de ILB porque o ponto final interno é um balanceador de carga interno (ILB). 

Este artigo mostra como criar um ASE de ILB. Para obter uma descrição geral sobre o ASE, veja [Introdução aos Ambientes de Serviço de Aplicações][Intro]. Para saber como criar um ASE externo, veja [Criar um ASE Externo][MakeExternalASE].

## <a name="overview"></a>Descrição geral 

Pode implementar um ASE com um ponto final acessível pela Internet ou com um endereço IP na sua VNet. Para definir o endereço IP para um endereço VNet, o ASE tem de ser implementado com um ILB. Ao implementar o ASE com um ILB, tem de fornecer o nome do seu ASE. O nome do seu ASE é utilizado no sufixo de domínio para as aplicações no ASE.  O sufixo de domínio para o seu ASE de ILB for &lt;nome do ASE&gt;. appservicewebsites.net. Aplicações que são feitas num ASE de ILB não são colocadas no DNS público. 

Versões anteriores do ASE de ILB precisa de fornecer um sufixo de domínio e um certificado de predefinida para ligações HTTPS. O sufixo de domínio já não é recolhido durante a criação do ASE de ILB e também é coletado já não é um certificado predefinido. Ao criar um ASE de ILB agora, o certificado predefinido é fornecido pela Microsoft e é considerado fidedigno pelo navegador. É ainda possível definir nomes de domínio personalizados em aplicações no ASE e defina os certificados nesses nomes de domínio personalizado. 

Com um ASE de ILB, pode fazer coisas como:

-   Alojar aplicações da intranet em segurança na cloud, que pode aceder através de um site a site ou ExpressRoute.
-   Proteger aplicações com um dispositivo WAF
-   Alojar aplicações na cloud que não estão listadas em servidores DNS públicos.
-   Criar aplicações back-end isoladas da Internet, nas quais as suas aplicações front-end podem ser integradas em segurança.

### <a name="disabled-functionality"></a>Funcionalidades desativadas ###

Existem algumas coisas que não é possível fazer quando utiliza um ASE de ILB:

-   Utilizar SSL baseado em IP.
-   Atribuir endereços IP a aplicações específicas.
-   Comprar e utilizar um certificado com uma aplicação através do portal do Azure. Pode obter certificados a partir diretamente de uma autoridade de certificação e utilizá-los com as suas aplicações. Não pode obtê-los através do portal do Azure.

## <a name="create-an-ilb-ase"></a>Criar um ASE de ILB ##

Para criar um ASE de ILB:

1. No portal do Azure, selecione **Criar um recurso** > **Web** > **Ambiente de Serviço de Aplicações**.

2. Selecione a sua subscrição.

3. Selecione ou crie um grupo de recursos.

4. Introduza o nome do seu ambiente de serviço de aplicações.

5. Selecione o tipo de IP virtual de interno.

    ![Criação do ASE](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. Selecione o sistema de rede

7. Selecione ou crie uma rede Virtual. Se criar uma nova VNet aqui, ela será definida com um intervalo de endereços de 192.168.250.0/23. Para criar uma VNet com um intervalo de endereços diferentes ou num grupo de recursos diferente do que o ASE, utilize o portal de criação de rede Virtual do Azure. 

8. Selecione ou crie uma sub-rede de vazio. Se pretender selecionar uma sub-rede, tem de ser vazio e não delegado. O tamanho da sub-rede não pode ser alterado após a criação do ASE. Recomendamos um tamanho de `/24`, que tem 256 endereços e pode processar um ASE de tamanho máximo e qualquer necessidade de dimensionamento. 

    ![Sistema de rede do ASE][1]

7. Selecione **revisão e Create** , em seguida, selecione **criar**.

## <a name="create-an-app-in-an-ilb-ase"></a>Criar uma aplicação num ASE de ILB ##

Uma aplicação num ASE de ILB é criada da mesma forma que cria uma aplicação num ASE normalmente.

1. No portal do Azure, selecione **criar um recurso** > **Web** > **aplicação Web**.

1. Introduza o nome da aplicação.

1. Selecione uma subscrição.

1. Selecione ou crie um grupo de recursos.

1. Selecione sua publicação, a pilha de Runtime e o sistema operativo.

1. Selecione uma localização onde a localização é um ASE de ILB existente.  Também pode criar um novo ASE durante a criação de aplicações ao selecionar um plano do serviço de aplicações isolado. Se desejar criar um novo ASE, selecione a região em que pretende que o ASE para ser criado no.

1. Selecione ou crie um plano do Serviço de Aplicações. 

1. Selecione **revisão e Create** , em seguida, selecione **criar** quando estiver pronto.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>WebJobs, Funções e o ASE de ILB 

Tanto as Funções como os WebJobs são suportados num ASE de ILB, mas para o portal funcionar com os mesmos, tem de ter acesso de rede ao site SCM.  Isto significa que o browser tem de estar num anfitrião que esteja na rede virtual ou ligado à mesma. Se o ASE de ILB tem um nome de domínio que não termina em *appserviceenvironment.net*, terá de obter o seu browser para confiar no certificado HTTPS que está a ser utilizado pelo seu site do scm.

## <a name="dns-configuration"></a>Configuração do DNS 

Quando utiliza um VIP Externo, o DNS é gerido pelo Azure. Qualquer aplicação criada no seu ASE é adicionada automaticamente ao DNS do Azure, que é um DNS público. Num ASE de ILB, tem de gerir o seu próprio DNS. O sufixo de domínio utilizado com um ASE de ILB depende do nome do ASE. O sufixo de domínio for  *&lt;nome do ASE&gt;. appserviceenvironment.net*. O endereço IP para o ILB está no portal em **endereços IP**. 

Para configurar o seu DNS:

- criar uma zona para  *&lt;nome do ASE&gt;. appserviceenvironment.net*
- criar um registo nessa zona que aponta * para o endereço IP do ILB 
- criar uma zona no  *&lt;nome do ASE&gt;. scm.appserviceenvironment.net* com o nome scm
- criar um registo na zona scm que aponta para o endereço IP do ILB

## <a name="publish-with-an-ilb-ase"></a>Publicar com um ASE de ILB

Para cada aplicação criada, existem dois pontos finais. Num ASE de ILB, tiver *&lt;nome da aplicação&gt;.&lt; Domínio do ASE de ILB&gt;* e  *&lt;nome da aplicação&gt;SCM.&lt; Domínio do ASE de ILB&gt;* . 

O nome do site SCM leva-o para a consola Kudu, denominada **Portal avançado**, no portal do Azure. A consola Kudu permite-lhe ver as variáveis de ambiente, explorar o disco, utilizar uma consola e muito mais. Para obter mais informações, veja [Consola Kudu para o Serviço de Aplicações do Azure][Kudu]. 

Os sistemas CI baseados na Internet, como o GitHub e o Azure DevOps, continuarão a funcionar com um ASE de ILB se o agente de compilação for acessível pela Internet e estiver na mesma rede que o ASE de ILB. Por isso, no caso do Azure DevOps, se o agente de compilação for criado na mesma VNET que o ASE de ILB (pode ser outra sub-rede), conseguirá obter o código do git do Azure DevOps e implementar no ASE de ILB. Se não quiser criar o seu próprio agente de compilação, terá de utilizar um sistema CI que utilize um modelo de extração, como o Dropbox.

Os pontos finais de publicação para aplicações num ASE de ILB utilizam o domínio com o qual o ASE de ILB foi criado. Este domínio aparece no perfil de publicação da aplicação e no painel do portal da aplicação (**Descrição geral** > **Informações Básicas** e também **Propriedades**). Se tiver um ASE de ILB com o sufixo de domínio  *&lt;nome do ASE&gt;. appserviceenvironment.net*e uma aplicação com o nome *mytest*, utilize *mytest.&lt; Nome do ASE&gt;. appserviceenvironment.net* FTP e *mytest.scm.contoso.net* para implementação na web.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Configurar o ASE de ILB com um dispositivo WAF ##

Pode combinar um dispositivo de firewall (WAF) de aplicação web com o ASE de ILB para expor somente as aplicações que pretende para a internet e mantenha o resto apenas acessível a partir da VNet. Isto permite-lhe criar aplicações de várias camadas seguras entre outras coisas.

Para saber mais sobre como configurar o ASE de ILB com um dispositivo WAF, veja [configurar uma firewall de aplicações web com o seu ambiente de serviço de aplicações][ASEWAF]. Este artigo mostra como utilizar uma aplicação virtual Barracuda com o seu ASE. Outra opção é utilizar o Gateway de Aplicação do Azure. O Gateway de Aplicação utiliza as regras de base da OWASP para proteger todas as aplicações colocadas atrás. Para obter mais informações sobre o Gateway de Aplicação, veja [Introdução à firewall de aplicações Web do Azure][AppGW].

## <a name="ilb-ases-made-before-may-2019"></a>ASEs do ILB feitas antes de Maio de 2019

ASEs do ILB que foram feitas antes de Maio de 2019 pedido que defina o sufixo de domínio durante a criação do ASE. Eles também necessário carregar um certificado de padrão baseada no sufixo desse domínio. Além disso, com um ASE de ILB mais antigos não é possível executar início de sessão único para a consola Kudu com aplicações no ILB ASE. Ao configurar o DNS para um ASE de ILB mais antigo, terá de definir o registo com carateres universais numa zona que corresponde ao seu sufixo de domínio. 

## <a name="get-started"></a>Introdução ##

* Para começar a utilizar ASEs, veja [Introdução aos ambientes de Serviço de Aplicações][Intro]. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

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
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
