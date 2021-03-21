---
title: Criar um ILB ASE com ARM
description: Saiba como criar um ambiente de Serviço de Aplicações com um equilibrador de carga interno (ILB ASE) utilizando modelos de Gestor de Recursos Azure. Isole totalmente as suas aplicações da internet.
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 27c9198558a730d0af49077d6f5baa6db4789416
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009556"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Criar e utilizar um Ambiente de Serviço de Aplicação do Balanceador interno 

O Azure App Service Environment é uma implementação do Azure App Service numa sub-rede numa rede virtual Azure (VNet). Existem duas formas de implementar um Ambiente de Serviço de Aplicações (ASE): 

- Com um VIP num endereço IP externo, muitas vezes chamado ASE Externo.
- Com um VIP num endereço IP interno, muitas vezes chamado ASE de ILB porque o ponto final interno é um balanceador de carga interno (ILB). 

Este artigo mostra como criar um ASE de ILB. Para obter uma visão geral sobre o ASE, consulte [Introdução aos Ambientes de Serviço de Aplicações.][Intro] Para saber como criar um ASE externo, veja [Criar um ASE Externo][MakeExternalASE].

## <a name="overview"></a>Descrição geral 

Pode implementar um ASE com um ponto final acessível pela Internet ou com um endereço IP na sua VNet. Para definir o endereço IP para um endereço VNet, o ASE tem de ser implementado com um ILB. Quando colocar o seu ASE com um ILB, deve fornecer o nome do seu ASE. O nome do seu ASE é utilizado no sufixo de domínio para as aplicações no seu ASE.  O sufixo de domínio para o seu ILB ASE é &lt; o nome ASE &gt; .appserviceenvironment.net. As aplicações que são feitas num ILB ASE não são colocadas no DNS público. 

Versões anteriores do ILB ASE obrigavam-no a fornecer um sufixo de domínio e um certificado predefinido para ligações HTTPS. O sufixo de domínio já não é recolhido na criação do ILB ASE e um certificado por defeito também já não é recolhido. Quando cria agora um ILB ASE, o certificado predefinido é fornecido pela Microsoft e é fidedigno pelo navegador. Ainda é capaz de definir nomes de domínio personalizados em aplicações no seu ASE e definir certificados nesses nomes de domínio personalizados. 

Com um ILB ASE, você pode fazer coisas como:

-   Hospedar aplicações intranet de forma segura na nuvem, a que acede através de um site-site ou ExpressRoute.
-   Proteja aplicativos com um dispositivo WAF
-   Alojar aplicações na cloud que não estão listadas em servidores DNS públicos.
-   Criar aplicações back-end isoladas da Internet, nas quais as suas aplicações front-end podem ser integradas em segurança.

### <a name="disabled-functionality"></a>Funcionalidades desativadas ###

Existem algumas coisas que não é possível fazer quando utiliza um ASE de ILB:

-   Utilizar SSL baseado em IP.
-   Atribuir endereços IP a aplicações específicas.
-   Comprar e utilizar um certificado com uma aplicação através do portal do Azure. Pode obter certificados a partir diretamente de uma autoridade de certificação e utilizá-los com as suas aplicações. Não pode obtê-los através do portal do Azure.

## <a name="create-an-ilb-ase"></a>Criar um ASE de ILB ##

Para criar um ASE de ILB:

1. No portal Azure, selecione **Criar um ambiente de** serviço de  >    >  **aplicações** web de recurso.

2. Selecione a sua subscrição.

3. Selecione ou crie um grupo de recursos.

4. Insira o nome do seu Ambiente de Serviço de Aplicações.

5. Selecione o tipo IP virtual de Internal.

    ![Criação do ASE](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

> [!NOTE]
> O nome Ambiente do Serviço de Aplicações não deve ter mais de 37 caracteres.

6. Selecione Networking

7. Selecione ou crie uma Rede Virtual. Se criar um novo VNet aqui, será definido com um intervalo de endereços de 192.168.250.0/23. Para criar um VNet com uma gama de endereços diferente ou num grupo de recursos diferente do ASE, utilize o portal de criação da Rede Virtual Azure. 

8. Selecione ou crie uma sub-rede vazia. Se quiser selecionar uma sub-rede, deve estar vazia e não delegada. O tamanho da sub-rede não pode ser alterado após a criação do ASE. Recomendamos um tamanho de `/24`, que tem 256 endereços e pode processar um ASE de tamanho máximo e qualquer necessidade de dimensionamento. 

    ![Rede ASE][1]

7. Selecione **Rever e criar** e, em seguida, selecione **Criar**.


## <a name="create-an-app-in-an-ilb-ase"></a>Criar uma aplicação num ASE de ILB ##

Uma aplicação num ASE de ILB é criada da mesma forma que cria uma aplicação num ASE normalmente.

1. No portal Azure, selecione **Criar uma** Web App  >    >  **de** recursos.

1. Introduza o nome da aplicação.

1. Selecione uma subscrição.

1. Selecione ou crie um grupo de recursos.

1. Selecione a sua Publish, Runtime Stack e Sistema Operativo.

1. Selecione um local onde a localização seja um ILB ASE existente.  Também pode criar um novo ASE durante a criação de aplicações selecionando um plano de Serviço de Aplicações Isolado. Se pretender criar um novo ASE, selecione a região em que pretende que o ASE seja criado.

1. Selecione ou crie um plano do Serviço de Aplicações. 

1. Selecione **'Rever e criar' e,** em seguida, selecione **Criar** quando estiver pronto.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>WebJobs, Funções e o ASE de ILB 

Tanto as Funções como os WebJobs são suportados num ASE de ILB, mas para o portal funcionar com os mesmos, tem de ter acesso de rede ao site SCM.  Isto significa que o browser tem de estar num anfitrião que esteja na rede virtual ou ligado à mesma. Se o seu ILB ASE tiver um nome de domínio que não termina em *appserviceenvironment.net,* terá de fazer com que o seu navegador confie no certificado HTTPS que está a ser utilizado pelo seu site scm.

## <a name="dns-configuration"></a>Configuração do DNS 

Quando utiliza um ASE Externo, as aplicações feitas no seu ASE estão registadas no Azure DNS. Não existem então passos adicionais numa ASE Externa para que as suas aplicações estejam disponíveis ao público. Com um ILB ASE, você deve gerir o seu próprio DNS. Pode fazê-lo no seu próprio servidor DNS ou nas zonas privadas Azure DNS.

Para configurar o DNS no seu próprio servidor DNS com o seu ILB ASE:

1. criar uma zona para &lt; o nome ASE &gt; .appserviceenvironment.net
2. criar um registo A naquela zona que aponta * para o endereço IP ILB
3. criar um registo A naquela zona que aponta @ para o endereço IP ILB
4. criar uma zona em &lt; nome ASE &gt; .appserviceenvironment.net nomeado scm
5. criar um registo A na zona scm que aponta * para o endereço IP ILB

Para configurar DNS em zonas privadas Azure DNS:

1. criar uma zona privada Azure DNS chamada &lt; ase nome &gt; .appserviceenvironment.net
2. criar um registo A naquela zona que aponta * para o endereço IP ILB
3. criar um registo A naquela zona que aponta @ para o endereço IP ILB
4. criar um registo A nessa zona que aponta *.scm para o endereço IP ILB

As definições DE DNS para o seu sufixo de domínio padrão ASE não restringem as suas aplicações a serem apenas acessíveis por esses nomes. Pode definir um nome de domínio personalizado sem qualquer validação nas suas aplicações num ILB ASE. Se então quiser criar uma zona denominada contoso.net, pode fazê-lo e apontá-la para o endereço IP ILB. O nome de domínio personalizado funciona para pedidos de aplicações, mas não para o site scm. O site scm só está disponível &lt; no appname &gt; .scm. &lt; asename &gt; .appserviceenvironment.net.

A zona chamada . &lt; asename &gt; .appserviceenvironment.net é globalmente única. Antes de maio de 2019, os clientes puderam especificar o sufixo de domínio do ILB ASE. Se quisesse usar .contoso.com para o sufixo de domínio, poderia fazê-lo e isso incluiria o site scm. Houve desafios com este modelo, incluindo; gerir o certificado SSL predefinido, falta de um único sinal com o site scm e a obrigação de usar um certificado wildcard. O processo de atualização do certificado por defeito ILB ASE também foi disruptivo e causou o reinício da aplicação. Para resolver estes problemas, o comportamento do ILB ASE foi alterado para usar um sufixo de domínio baseado no nome do ASE e com um sufixo propriedade da Microsoft. A alteração ao comportamento do ILB ASE só afeta as ASEs do ILB feitas após maio de 2019. AsES ILB pré-existentes devem ainda gerir o certificado predefinido do ASE e a sua configuração de DNS.

## <a name="publish-with-an-ilb-ase"></a>Publicar com um ASE de ILB

Para cada aplicação criada, existem dois pontos finais. Num ILB ASE, tem *&lt; o nome da &gt; aplicação. &lt; ILB ASE &gt; Domain* e *&lt; nome de aplicação &gt; .scm. &lt; Domínio &gt; ILB ASE*. 

O nome do site SCM leva-o para a consola Kudu, denominada **Portal avançado**, no portal do Azure. A consola Kudu permite-lhe ver as variáveis de ambiente, explorar o disco, utilizar uma consola e muito mais. Para obter mais informações, veja [Consola Kudu para o Serviço de Aplicações do Azure][Kudu]. 

Os sistemas CI baseados na Internet, como o GitHub e o Azure DevOps, continuarão a funcionar com um ASE de ILB se o agente de compilação for acessível pela Internet e estiver na mesma rede que o ASE de ILB. Por isso, no caso do Azure DevOps, se o agente de compilação for criado na mesma VNET que o ASE de ILB (pode ser outra sub-rede), conseguirá obter o código do git do Azure DevOps e implementar no ASE de ILB. Se não quiser criar o seu próprio agente de compilação, terá de utilizar um sistema CI que utilize um modelo de extração, como o Dropbox.

Os pontos finais de publicação para aplicações num ASE de ILB utilizam o domínio com o qual o ASE de ILB foi criado. Este domínio aparece no perfil de publicação da aplicação e na lâmina do portal da aplicação **(Overview**  >  **Essentials** e também **Properties).** Se tiver um ILB ASE com o sufixo de domínio *&lt; ASE &gt; .appserviceenvironment.net*, e uma aplicação chamada *mytest*, use *o meutest. &lt; Nome ASE &gt; .appserviceenvironment.net* para FTP e *mytest.scm.contoso.net* para implantação web.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Configure um ILB ASE com um dispositivo WAF ##

Pode combinar um dispositivo de firewall de aplicação web (WAF) com o seu ILB ASE para apenas expor as aplicações que deseja à internet e manter o resto apenas acessível a partir do VNet. Isto permite-lhe construir aplicações seguras de vários níveis, entre outras coisas.

Para saber mais sobre como configurar o seu ILB ASE com um dispositivo WAF, consulte [configurar uma firewall de aplicação web com o seu ambiente de Serviço de Aplicações.][ASEWAF] Este artigo mostra como utilizar uma aplicação virtual Barracuda com o seu ASE. Outra opção é utilizar o Gateway de Aplicação do Azure. O Gateway de Aplicação utiliza as regras de base da OWASP para proteger todas as aplicações colocadas atrás. Para obter mais informações sobre o Gateway de Aplicação, veja [Introdução à firewall de aplicações Web do Azure][AppGW].

## <a name="ilb-ases-made-before-may-2019"></a>ASES do ILB feitas antes de maio de 2019

AsEs ILB que foram feitas antes de maio de 2019 obrigaram-no a definir o sufixo de domínio durante a criação da ASE. Também obrigaram a carregar um certificado predefinido que se baseava nesse sufixo de domínio. Além disso, com um ILB ASE mais antigo não é possível realizar um único sign-on na consola Kudu com aplicações nesse ILB ASE. Ao configurar o DNS para um ILB ASE mais antigo, é necessário estabelecer o wildcard A numa zona que corresponda ao sufixo de domínio. 

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
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../overview.md#app-service-on-linux
