---
title: Criar um ILB ASE v1
description: Criar e usar uma ASE com um ILB. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0c03905017629e28e41cce2adaa65eac347b8185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294724"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Usando um balancer de carga interna com um ambiente de serviço de aplicação

> [!NOTE] 
> Este artigo é sobre o App Service Environment v1. Existe uma versão mais recente do App Service Environment que é mais fácil de usar e funciona em infraestruturas mais poderosas. Para saber mais sobre a nova versão comece com a Introdução ao Ambiente de Serviço de [Aplicações.](intro.md)
>

A funcionalidade App Service Environment (ASE) é uma opção de serviço Premium do Serviço de Aplicações Azure que oferece uma capacidade de configuração melhorada que não está disponível nos selos multi-inquilinos. A funcionalidade ASE implanta essencialmente o Serviço de Aplicações Azure na sua Rede Virtual Azure(VNet). Para obter uma maior compreensão das capacidades oferecidas pelos Ambientes de Serviço de Aplicação, leia a documentação [What is a App Service Environment.][WhatisASE] Se não sabe os benefícios de operar num VNet, leia o [FAQ da Rede Virtual Azure][virtualnetwork]. 

## <a name="overview"></a>Descrição geral
Um ASE pode ser implantado com um ponto final acessível à Internet ou com um endereço IP no seu VNet. Para definir o endereço IP para um endereço VNet, é necessário implementar a Sua ASE com um Balancer de Carga Interna (ILB). Quando a sua ASE está configurada com um ILB, fornece:

* seu próprio domínio ou subdomínio. Para facilitar, este documento assume o subdomínio, mas pode configurá-lo de qualquer forma. 
* o certificado utilizado para HTTPS
* Gestão de DNS para o seu subdomínio. 

Em contrapartida, pode fazer coisas como:

* aplicações intranet hospedeiras, como aplicações de linha de negócio, de forma segura na nuvem a que acede através de um Site para site ou ExpressRoute VPN
* aplicações hospedeiras na nuvem que não estão listadas em servidores públicos do DNS
* criar aplicações de backend isoladas da Internet com as quais as suas aplicações front-end podem integrar-se de forma segura

#### <a name="disabled-functionality"></a>Funcionalidades desativadas
Há coisas que não se pode fazer quando se usa um ILB ASE. Estas coisas incluem:

* utilizando o IPSSL
* atribuição de endereços IP a aplicações específicas
* compra e utilização de um certificado com uma app através do portal. É claro que ainda pode obter certificados diretamente com uma Autoridade de Certificados e usá-lo com as suas apps, mas não através do portal Azure.

## <a name="creating-an-ilb-ase"></a>Criação de um ILB ASE
A criação de um ILB ASE não é muito diferente da criação normal de uma ASE. Para uma discussão mais profunda sobre a criação de uma ASE, veja como criar um ambiente de [serviço de aplicações.][HowtoCreateASE] O processo de criação de um ILB ASE é o mesmo entre criar uma VNet durante a criação da ASE ou selecionar um VNet pré-existente. Para criar um ASE de ILB: 

1. No portal Azure, selecione **Criar um recurso -> Web + Mobile -> App Service Environment**.
2. Selecione a sua subscrição.
3. Selecione ou crie um grupo de recursos.
4. Selecione ou crie uma VNet.
5. Crie uma sub-rede se selecionar um VNet.
6. Selecione **Rede Virtual/Localização -> Configuração VNet** e desloque o tipo VIP para Interno.
7. Forneça um nome de subdomínio (este nome é o subdomínio utilizado para as aplicações criadas nesta ASE).
8. Selecione **OK** e, em seguida, **Criar**.

![][1]

No painel rede virtual, existe uma opção de Configuração VNet que lhe permite selecionar entre um VIP externo ou UM VIP interno. A predefinição é Externo. Se o tiver definido para Externo, a sua ASE utiliza um VIP acessível à Internet. Se selecionar Interno, o seu ASE está configurado com um ILB num endereço IP na sua VNet. 

Depois de selecionar Interno, a capacidade de adicionar mais endereços IP à sua ASE é removida e, em vez disso, deve fornecer o subdomínio da ASE. Numa ASE com um VIP externo, o nome da ASE é utilizado no subdomínio para aplicações criadas nessa ASE. Se a sua ASE for nomeada ***contosotest*** e a sua aplicação na aa a aque a ASE for nomeada ***mytest***, o subdomínio é do formato ***contosotest.p.azurewebsites.net*** e o URL dessa aplicação é ***mytest.contosotest.p.azurewebsites.net***. Se definir o tipo VIP para Interno, o seu nome ASE não é utilizado no subdomínio da ASE. Especifica explicitamente o subdomínio. Se o seu subdomínio for ***contoso.corp.net*** e tiver feito uma aplicação nesse ***nome***a ASE, o URL dessa aplicação é ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Aplicativos num ILB ASE
Criar uma aplicação num ILB ASE é o mesmo que criar uma aplicação numa ASE normalmente. 

1. No portal Azure, selecione **Criar um recurso -> Web + Mobile -> Web** ou **Mobile** ou **API App**.
2. Introduza o nome da aplicação.
3. Selecione a sua subscrição.
4. Selecione ou crie um grupo de recursos.
5. Selecione ou crie um Plano de Serviço de Aplicações (ASP). Se criar um novo ASP, selecione a sua ASE como localização e selecione o pool de trabalhadores em que pretende que o seu ASP seja criado. Quando criar o ASP, selecione a sua ASE como localização e a piscina dos trabalhadores. Quando especificar o nome da aplicação, verá que o subdomínio sob o nome da sua aplicação é substituído pelo subdomínio para a sua ASE. 
6. Selecione **Criar**. Certifique-se de selecionar o **Pin para dashboard** checkbox se quiser que a aplicação apareça no seu painel de instrumentos. 

![][2]

Sob o nome da aplicação, o nome do subdomínio é atualizado para refletir o subdomínio da sua ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Validação da criação pós-ILB ASE
Um ASE de ILB é ligeiramente diferente do ASE não ILB. Como já foi notado, precisa de gerir o seu próprio DNS e também tem de fornecer o seu próprio certificado para ligações HTTPS. 

Depois de criar a sua ASE, notará que o subdomínio mostra o subdomínio que especificou e há um novo item no menu **Definição** chamado **Certificado ILB**. A ASE é criada com um certificado auto-assinado que facilita o teste HTTPS. O portal diz-lhe que precisa de fornecer o seu próprio certificado para HTTPS, mas isto é para o incentivar a ter um certificado que vá com o seu subdomínio. 

![][3]

Se estiver simplesmente a experimentar as coisas e não souber como criar um certificado, pode utilizar a aplicação de consola IIS MMC para criar um certificado auto-assinado. Uma vez criado, pode exportá-lo como um ficheiro .pfx e, em seguida, carregá-lo no ILB Certificate UI. Ao aceder a um site protegido com um certificado auto-assinado, o seu navegador dá-lhe um aviso de que o site a que está a aceder não é seguro devido à incapacidade de validar o certificado. Se quiser evitar esse aviso, necessitará de um certificado devidamente assinado que corresponda ao seu subdomínio e tenha uma cadeia de confiança que é reconhecida pelo seu navegador.

![][6]

Se quiser experimentar o fluxo com os seus próprios certificados e testar o acesso http e HTTPS à sua ASE:

1. Vá à ASE UI depois de a ASE ser criada **ASE -> Definições -> Certificados ILB**.
2. Detete o certificado ILB selecionando o ficheiro pfx do certificado e forneça senha. Este passo demora um pouco a processar e aparece a mensagem de que está em curso uma operação de escalação.
3. Obtenha o endereço ILB para o seu ASE (**ASE -> Propriedades -> endereço IP virtual**).
4. Crie uma aplicação web na ASE após a criação. 
5. Crie um VM se não tiver um nesse VNET (Não na mesma sub-rede que a ASE ou as coisas quebram).
6. Desloque o DNS para o seu subdomínio. Pode utilizar um wildcard com o seu subdomínio no seu DNS ou se quiser fazer alguns testes simples, editar o ficheiro dos anfitriões no seu VM para definir o nome da aplicação web para o endereço IP VIP. Se a sua ASE tivesse o nome de subdomínio .ilbase.com e fizesse o mytestapp da aplicação web para que fosse endereçado em mytestapp.ilbase.com, detetete isso no ficheiro dos seus anfitriões. (No Windows, o ficheiro dos anfitriões está em C:\Windows\System32\drivers\etc\)
7. Use um navegador nesse VM `https://mytestapp.ilbase.com` e vá a (ou qualquer que seja o nome da sua aplicação web com o seu subdomínio).
8. Utilize um browser nessa VM e aceda a `https://mytestapp.ilbase.com`. Deve aceitar a falta de segurança se utilizar um certificado auto-assinado. 

O endereço IP do seu ILB está listado nas suas Propriedades como o Endereço IP Virtual.

![][4]

## <a name="using-an-ilb-ase"></a>Usando uma ASE ILB
#### <a name="network-security-groups"></a>Grupos de Segurança de Rede
Um ILB ASE permite o isolamento da rede para as suas apps. As aplicações não são acessíveis ou mesmo conhecidas pela internet. Esta abordagem é excelente para hospedar sites intranet, tais como aplicações de linha de negócio. Quando precisa de restringir ainda mais o acesso, ainda pode utilizar grupos de segurança de rede (NSGs) para controlar o acesso ao nível da rede. 

Se pretender utilizar NSGs para restringir ainda mais o acesso, deve certificar-se de que não quebra a comunicação de que a ASE necessita para funcionar. Mesmo que o acesso HTTP/HTTPS seja apenas através do ILB utilizado pela ASE, a ASE ainda depende de recursos fora da VNet. Para ver que acesso à rede ainda é necessário, consulte controlar o tráfego de entrada para um ambiente de serviço de [aplicação][ControlInbound] e detalhes de configuração de rede para ambientes de [serviço de aplicações com ExpressRoute][ExpressRoute]. 

Para configurar os seus NSGs, deve conhecer o endereço IP que é utilizado pelo Azure para gerir a sua ASE. Este endereço IP é também o endereço IP de saída da sua ASE se fizer pedidos de internet. O endereço IP de saída para a sua ASE permanece estático para a vida da sua ASE. Se eliminar e recriar a Sua ASE, receberá um novo endereço IP. Para encontrar o endereço IP, vá a **Definições -> Propriedades** e encontre o **Endereço IP de saída**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Gestão geral da ASE ILB
Gerir um ILB ASE é em grande parte o mesmo que gerir uma ASE normalmente. Tem de aumentar as suas piscinas de trabalhadores para acolher mais instâncias ASP e aumentar os seus servidores frontais para lidar com quantidades acrescidas de tráfego HTTP/HTTPS. Para obter informações gerais sobre a gestão da configuração de uma ASE, consulte [configurar um Ambiente][ASEConfig]de Serviço de Aplicações . 

Os itens de gestão adicionais são a gestão de certificados e a gestão do DNS. Deve obter e carregar o certificado utilizado para HTTPS após a criação da ILB ASE e substituí-lo antes de expirar. Uma vez que o Azure é dono do domínio base, pode fornecer certificados para ASEs com um VIP externo. Uma vez que o subdomínio utilizado por um ILB ASE pode ser qualquer coisa, deve fornecer o seu próprio certificado para HTTPS. 

#### <a name="dns-configuration"></a>Configuração do DNS
Ao utilizar um VIP externo, o DNS é gerido pelo Azure. Qualquer aplicação criada no seu ASE é adicionada automaticamente ao DNS do Azure, que é um DNS público. Num ASE de ILB, tem de gerir o seu próprio DNS. Para um determinado subdomínio, como contoso.corp.net, deve criar registos DNS A que apontem para o seu endereço ILB para:

    * 
    *.scm ftp publicar 


## <a name="getting-started"></a>Introdução
Para começar com ambientes de serviço de aplicações, consulte Introdução a Ambientes de [Serviço de Aplicações][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
