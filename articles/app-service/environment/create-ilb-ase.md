---
title: Criar um balanceador de carga interno com o Ambiente do Serviço de Aplicativo-Azure
description: Detalhes sobre como criar e utilizar um Ambiente de Serviço de Aplicações do Azure isolado da Internet
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 07b47374484cf954b1fc4279c93dddcc6cec7e61
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470565"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Criar e usar um Load Balancer interno Ambiente do Serviço de Aplicativo 

A Ambiente do Serviço de Aplicativo do Azure é uma implantação do serviço de Azure App em uma sub-rede em uma VNet (rede virtual) do Azure. Existem duas formas de implementar um Ambiente de Serviço de Aplicações (ASE): 

- Com um VIP num endereço IP externo, muitas vezes chamado ASE Externo.
- Com um VIP num endereço IP interno, muitas vezes chamado ASE de ILB porque o ponto final interno é um balanceador de carga interno (ILB). 

Este artigo mostra como criar um ASE de ILB. Para obter uma visão geral do ASE, consulte [introdução aos ambientes do serviço de aplicativo][Intro]. Para saber como criar um ASE externo, consulte [criar um ase externo][MakeExternalASE].

## <a name="overview"></a>Descrição geral 

Pode implementar um ASE com um ponto final acessível pela Internet ou com um endereço IP na sua VNet. Para definir o endereço IP para um endereço VNet, o ASE tem de ser implementado com um ILB. Ao implantar seu ASE com um ILB, você deve fornecer o nome do seu ASE. O nome do seu ASE é usado no sufixo de domínio para os aplicativos em seu ASE.  O sufixo de domínio para seu ASE ILB é &lt;nome do ASE&gt;. appserviceenvironment.net. Os aplicativos que são feitos em um ASE ILB não são colocados no DNS público. 

As versões anteriores do ASE ILB exigiam que você fornecesse um sufixo de domínio e um certificado padrão para conexões HTTPS. O sufixo de domínio não é mais coletado na criação do ASE ILB e um certificado padrão também não é mais coletado. Quando você cria um ASE ILB agora, o certificado padrão é fornecido pela Microsoft e é confiável para o navegador. Você ainda pode definir nomes de domínio personalizados em aplicativos em seu ASE e definir certificados nesses nomes de domínio personalizados. 

Com um ASE ILB, você pode fazer coisas como:

-   Hospede aplicativos de intranet com segurança na nuvem, que você acessa por meio de um site a site ou ExpressRoute.
-   Proteger aplicativos com um dispositivo WAF
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

4. Insira o nome do seu Ambiente do Serviço de Aplicativo.

5. Selecione o tipo de IP virtual de interno.

    ![Criação do ASE](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. Selecionar rede

7. Selecione ou crie uma rede virtual. Se você criar uma nova VNet aqui, ela será definida com um intervalo de endereços de 192.168.250.0/23. Para criar uma VNet com um intervalo de endereços diferente ou em um grupo de recursos diferente do ASE, use o portal de criação de rede virtual do Azure. 

8. Selecione ou crie uma sub-rede vazia. Se você quiser selecionar uma sub-rede, ela deverá estar vazia e não delegada. O tamanho da sub-rede não pode ser alterado após a criação do ASE. Recomendamos um tamanho de `/24`, que tem 256 endereços e pode processar um ASE de tamanho máximo e qualquer necessidade de dimensionamento. 

    ![Rede ASE][1]

7. Selecione **revisar e criar e** , em seguida, selecionar **criar**.

## <a name="create-an-app-in-an-ilb-ase"></a>Criar uma aplicação num ASE de ILB ##

Uma aplicação num ASE de ILB é criada da mesma forma que cria uma aplicação num ASE normalmente.

1. Na portal do Azure, selecione **criar um recurso** > **Web** > **aplicativo Web**.

1. Introduza o nome da aplicação.

1. Selecione uma subscrição.

1. Selecione ou crie um grupo de recursos.

1. Selecione sua publicação, pilha de tempo de execução e sistema operacional.

1. Selecione um local onde o local é um ASE ILB existente.  Você também pode criar um novo ASE durante a criação do aplicativo selecionando um plano do serviço de aplicativo isolado. Se você quiser criar um novo ASE, selecione a região na qual deseja que o ASE seja criado.

1. Selecione ou crie um plano do Serviço de Aplicações. 

1. Selecione **revisar e criar e** , em seguida, selecione **criar** quando estiver pronto.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>WebJobs, Funções e o ASE de ILB 

Tanto as Funções como os WebJobs são suportados num ASE de ILB, mas para o portal funcionar com os mesmos, tem de ter acesso de rede ao site SCM.  Isto significa que o browser tem de estar num anfitrião que esteja na rede virtual ou ligado à mesma. Se o ASE ILB tiver um nome de domínio que não termine em *appserviceenvironment.net*, você precisará obter o navegador para confiar no certificado HTTPS que está sendo usado pelo site do SCM.

## <a name="dns-configuration"></a>Configuração do DNS 

Quando utiliza um VIP Externo, o DNS é gerido pelo Azure. Qualquer aplicação criada no seu ASE é adicionada automaticamente ao DNS do Azure, que é um DNS público. Num ASE de ILB, tem de gerir o seu próprio DNS. O sufixo de domínio usado com um ASE ILB depende do nome do ASE. O sufixo de domínio é *&lt;nome do ASE&gt;. appserviceenvironment.net*. O endereço IP para seu ILB está no portal em **endereços IP**. 

Para configurar o DNS:

- Crie uma zona para *&lt;nome do ASE&gt;. appserviceenvironment.net*
- criar um registro A na zona que aponta para * para o endereço IP ILB
- criar um registro A na zona que aponta para @ para o endereço IP ILB
- Crie uma zona em *&lt;nome do ASE&gt;. appserviceenvironment.net* chamado SCM
- criar um registro A na zona SCM que aponta para * para o endereço IP ILB

## <a name="publish-with-an-ilb-ase"></a>Publicar com um ASE de ILB

Para cada aplicação criada, existem dois pontos finais. Em um ASE ILB, você tem *&lt;nome do aplicativo&gt;.&lt;domínio do ase ILB&gt;* e *&lt;nome do aplicativo&gt;. SCM.&lt;ILB o domínio do ase* &gt;. 

O nome do site SCM leva-o para a consola Kudu, denominada **Portal avançado**, no portal do Azure. A consola Kudu permite-lhe ver as variáveis de ambiente, explorar o disco, utilizar uma consola e muito mais. Para obter mais informações, consulte [kudu console for Azure app Service][Kudu]. 

Os sistemas CI baseados na Internet, como o GitHub e o Azure DevOps, continuarão a funcionar com um ASE de ILB se o agente de compilação for acessível pela Internet e estiver na mesma rede que o ASE de ILB. Por isso, no caso do Azure DevOps, se o agente de compilação for criado na mesma VNET que o ASE de ILB (pode ser outra sub-rede), conseguirá obter o código do git do Azure DevOps e implementar no ASE de ILB. Se não quiser criar o seu próprio agente de compilação, terá de utilizar um sistema CI que utilize um modelo de extração, como o Dropbox.

Os pontos finais de publicação para aplicações num ASE de ILB utilizam o domínio com o qual o ASE de ILB foi criado. Este domínio aparece no perfil de publicação da aplicação e no painel do portal da aplicação (**Descrição geral** > **Informações Básicas** e também **Propriedades**). Se você tiver um ASE ILB com o sufixo de domínio *&lt;nome do ase&gt;. appserviceenvironment.net*e um aplicativo chamado *myTest*, use *myTest.&lt;nome do ase&gt;. appserviceenvironment.net* para FTP e  *mytest.scm.contoso.net* para implantação na Web.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Configurar um ASE ILB com um dispositivo WAF ##

Você pode combinar um dispositivo WAF (firewall do aplicativo Web) com seu ASE ILB para expor apenas os aplicativos que você deseja para a Internet e manter o restante acessível somente do na VNet. Isso permite que você crie aplicativos de várias camadas seguros entre outras coisas.

Para saber mais sobre como configurar o ASE ILB com um dispositivo WAF, consulte [configurar um firewall do aplicativo Web com o ambiente do serviço de aplicativo][ASEWAF]. Este artigo mostra como utilizar uma aplicação virtual Barracuda com o seu ASE. Outra opção é utilizar o Gateway de Aplicação do Azure. O Gateway de Aplicação utiliza as regras de base da OWASP para proteger todas as aplicações colocadas atrás. Para obter mais informações sobre o gateway de aplicativo, consulte [introdução ao firewall do aplicativo Web do Azure][AppGW].

## <a name="ilb-ases-made-before-may-2019"></a>ILB ASEs fez antes de 2019 de maio

ILB ASEs que foram feitas antes de 2019 de maio exigiram que você definisse o sufixo de domínio durante a criação do ASE. Eles também exigiram que você carregue um certificado padrão baseado nesse sufixo de domínio. Além disso, com um ASE ILB mais antigo, você não pode executar o logon único no console do kudu com aplicativos nesse ILB ASE. Ao configurar o DNS para um ASE ILB mais antigo, você precisa definir o curinga A de um registro em uma zona que corresponda ao seu sufixo de domínio. 

## <a name="get-started"></a>Introdução ##

* Para começar a usar o ASEs, consulte [introdução aos ambientes do serviço de aplicativo][Intro]. 

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
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
