---
title: Criar e utilizar o Balanceador de carga interno com o ambiente de serviço de aplicações - Azure | Documentos da Microsoft
description: Criar e utilizar um ASE com um ILB
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 88f100bc780d8df0202cfcce9b390085a71fc905
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130607"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Utilizar um balanceador de carga interno com um ambiente de serviço de aplicações

> [!NOTE] 
> Este artigo é sobre o ambiente de serviço de aplicações v1. Existe uma versão mais recente do ambiente de serviço de aplicação que é mais fácil de usar e é executado numa infraestrutura mais poderosa. Para saber mais sobre o novo início de versão com o [introdução ao ambiente de serviço de aplicações](intro.md).
>

A funcionalidade de ambiente de serviço de aplicações (ASE) é uma opção de serviço Premium do serviço de aplicações do Azure que fornece um recurso de configuração avançada que não está disponível nos carimbos multi-inquilino. A funcionalidade do ASE, essencialmente, implementa o serviço de aplicações do Azure na sua rede Virtual do Azure. Para obter uma maior compreensão das capacidades disponibilizadas pela leitura de ambientes do App Service do [o que é um ambiente de serviço de aplicações] [ WhatisASE] documentação. Se não sabe os benefícios de operar numa leitura de VNet a [FAQ da rede Virtual do Azure][virtualnetwork]. 

## <a name="overview"></a>Descrição geral
Um ASE pode ser implementado com um ponto de final de acesso de internet ou com um endereço IP na sua VNet. Para definir o endereço IP para um endereço VNet terá de implementar o ASE com um Balancer(ILB) de carga interno. Quando o seu ASE está configurado com um ILB, fornece:

* o seu domínio ou subdomínio. Para facilitar, este documento assume o subdomínio, mas pode configurá-lo de qualquer forma. 
* o certificado utilizado para HTTPS
* Gestão de DNS para o subdomínio. 

Em contrapartida, pode fazer coisas como:

* aplicativos de intranet do Host, como aplicações de linha de negócio, com segurança na cloud que aceder através de um Site a Site ou a VPN do ExpressRoute
* alojar aplicações na cloud que não estão listado nos servidores DNS públicos
* criar aplicações de back-end de internet isolada com a qual as suas aplicações de front-end com segurança podem integrar

#### <a name="disabled-functionality"></a>Funcionalidades desativadas
Existem algumas coisas que não pode fazer ao utilizar um ASE de ILB. Esses elementos incluem:

* usando IPSSL
* atribuir endereços IP a aplicações específicas
* comprar e utilizar um certificado com uma aplicação através do portal. É claro, ainda pode obter certificados diretamente com uma autoridade de certificação e utilizá-lo com as suas aplicações, mas não através do portal do Azure.

## <a name="creating-an-ilb-ase"></a>Criar um ASE de ILB
Criar um ASE de ILB não é muito diferente da criação de um ASE normalmente. Para ver um debate mais aprofundado sobre a criação de um ASE, veja [como criar um ambiente de serviço de aplicações][HowtoCreateASE]. O processo para criar um ASE de ILB é o mesmo entre criar uma VNet durante a criação do ASE ou ao selecionar uma VNet já existente. Para criar um ASE de ILB: 

1. No portal do Azure, selecione **criar um recurso -> Web + Mobile-ambiente de serviço de aplicações >**.
2. Selecione a sua subscrição.
3. Selecione ou crie um grupo de recursos.
4. Selecione ou crie uma VNet.
5. Crie uma sub-rede, se selecionar uma VNet.
6. Selecione **rede Virtual/localização-> Configuração de VNet** e defina o tipo de VIP como interno.
7. Forneça um nome de subdomínio (este nome é o subdomínio utilizado para as aplicações criadas neste ASE).
8. Selecione **OK** e, em seguida **criar**.

![][1]

No painel de rede Virtual, há uma configuração de VNet a opção que lhe permite selecionar entre um VIP externo ou um VIP interno. A predefinição é externo. Se tiver definido como externo, o seu ASE utiliza um VIP acessível da internet. Se selecionar interno, o ASE está configurado com um ILB num endereço IP na sua VNet. 

Depois de selecionar interno, a capacidade de adicionar mais endereços IP para o seu ASE é removida e, em vez disso, tem de fornecer o subdomínio do ASE. Num ASE com um VIP externo, o nome do ASE é utilizado no subdomínio para as aplicações criadas nesse ASE. Se o seu ASE é denominado ***contosotest*** e a sua aplicação nesse ASE com o nome ***mytest***, o subdomínio está no formato ***contosotest.p.azurewebsites.net*** e o URL para essa aplicação ***mytest.contosotest.p.azurewebsites.net***. Se definir o tipo de VIP para interno, o nome do ASE não é utilizado no subdomínio para o ASE. Especificar explicitamente o subdomínio. Se for o subdomínio ***contoso.corp.net*** e que efetuou uma aplicação nesse ASE com o nome ***timereporting***, o URL para essa aplicação é ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Aplicações num ASE de ILB
Criar uma aplicação num ASE de ILB é igual a criação de uma aplicação num ASE normalmente. 

1. No portal do Azure, selecione **criar um recurso -> Web + Mobile -> Web** ou **Mobile** ou **aplicação API**.
2. Introduza o nome da aplicação.
3. Selecione a sua subscrição.
4. Selecione ou crie um grupo de recursos.
5. Selecione ou crie um Plan(ASP) do serviço de aplicações. Se criar um novo ASP, selecione o seu ASE como a localização e selecione o conjunto de trabalho que pretende que o ASP ser criado num. Quando cria o ASP, selecione o seu ASE como a localização e o conjunto de trabalho. Quando especificar o nome da aplicação, verá que o subdomínio no nome da aplicação é substituído pelo subdomínio para o seu ASE. 
6. Selecione **Criar**. Certifique-se de que selecione os **afixar ao dashboard** caixa de verificação se quiser que a aplicação a aparecer no seu dashboard. 

![][2]

Sob o nome da aplicação, o nome de subdomínio é atualizado para refletir o subdomínio do seu ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Publicar a validação da criação do ASE de ILB
Um ASE de ILB é ligeiramente diferente do ASE não ILB. Como já observado tem de gerir o seu próprio DNS e também tem de fornecer seu próprio certificado para ligações HTTPS. 

Depois de criar o ASE, irá reparar que o subdomínio mostra o subdomínio especificado e não há um novo item na **definição** menu chamado **certificado ILB**. A criação do ASE com um certificado autoassinado que torna mais fácil testar o HTTPS. O portal indica que precisa fornecer seu próprio certificado para HTTPS, mas isso é encorajá-lo a ter um certificado que vai com o subdomínio. 

![][3]

Se estiver apenas a experimentar coisas e não sabe como criar um certificado, pode utilizar o aplicativo de console IIS MMC para criar um certificado autoassinado. Depois de criado, pode exportá-lo como um ficheiro. pfx e, em seguida, carregue-o na IU de certificado ILB. Quando acessa um site protegido por um certificado autoassinado, o seu browser dá-lhe um aviso de que o site que está a aceder não é seguro devido à incapacidade de validar o certificado. Se quiser evitar esse aviso, terá um certificado de corretamente assinado que corresponde ao seu subdomínio e tem uma cadeia de fidedignidade que é reconhecida pelo seu browser.

![][6]

Se quiser tentar executar o fluxo com seus próprios certificados e testar o acesso HTTP e HTTPS para o seu ASE:

1. Aceda à IU do ASE após a criação do ASE **ASE -> Definições -> ILB certificados**.
2. Definir certificado ILB ao selecionar o ficheiro pfx de certificado e fornecer a palavra-passe. Este passo demora algum tempo a processar e é apresentada a mensagem que está em curso uma operação de dimensionamento.
3. Obtenha o endereço do ILB para o seu ASE (**ASE -> Properties -> endereço IP Virtual**).
4. Crie uma aplicação web no ASE após a criação. 
5. Crie uma VM, se não tiver uma nessa VNET (não na mesma sub-rede que a quebra de ASE ou as coisas).
6. Definir o DNS para o subdomínio. Pode utilizar um caráter universal com o subdomínio no DNS ou se quiser fazer alguns testes simples, edite o ficheiro de anfitriões na sua VM para definir o nome da aplicação web para o endereço de IP de VIP. Se seu ASE tiver o nome de subdomínio. ilbase.com e introduzidas mytestapp de aplicação web para que fossem atendido em mytestapp.ilbase.com, defini-lo no seu ficheiro de anfitriões. (No Windows, o ficheiro hosts está em C:\Windows\System32\drivers\etc\)
7. Utilize um browser nessa VM e aceda a https://mytestapp.ilbase.com (ou qualquer coisa que é o nome da aplicação web com o subdomínio).
8. Utilize um browser nessa VM e aceda a https://mytestapp.ilbase.com. Tem de aceitar a falta de segurança se utilizar um certificado autoassinado. 

O endereço IP para o ILB está listado nas suas propriedades como o endereço IP Virtual.

![][4]

## <a name="using-an-ilb-ase"></a>Utilizando um ILB de ASE
#### <a name="network-security-groups"></a>Grupos de Segurança de Rede
Um ASE de ILB permite que o isolamento de rede para as suas aplicações. As aplicações não são acessíveis ou até mesmo conhecidos pela internet. Essa abordagem é excelente para o alojamento de sites de intranet, como aplicações de linha de negócio. Quando precisar de restringir o acesso mais adiante, pode usar Groups(NSGs) de segurança de rede para controlar o acesso ao nível da rede. 

Se pretender utilizar NSGs para restringir mais o acesso, deve certificar-se de que não é interrompidas a comunicação que precisa, o ASE para operar. Mesmo que seja o acesso HTTP/HTTPS apenas por meio do utilizado pelo ASE de ILB, o ASE ainda depende de recursos fora da VNet. Para ver que acesso de rede ainda é necessário, consulte [controlar o tráfego de entrada para um ambiente de serviço de aplicações] [ ControlInbound] e [detalhes de configuração de rede para ambientes de serviço de aplicações com ExpressRoute][ExpressRoute]. 

Para configurar seus NSGs, tem de saber o endereço IP que é utilizado pelo Azure para gerir o seu ASE. Esse endereço IP também é o endereço IP de saída do ASE se faz solicitações da internet. O endereço IP de saída para o seu ASE permanece estático para o ciclo de vida do seu ASE. Se eliminar e recriar o ASE, obterá um novo endereço IP. Para encontrar o endereço IP, aceda a **definições -> propriedades** e localize a **endereço IP de saída**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Gestão do ASE de ILB geral
Gerir um ASE de ILB é basicamente o mesmo que a gestão de um ASE normalmente. Deve aumentar verticalmente os conjuntos de trabalho para alojar mais instâncias ASP e aumentar verticalmente os servidores front-end para lidar com maiores quantidades de tráfego HTTP/HTTPS. Para obter informações gerais sobre como gerir a configuração de um ASE, veja [configurando um ambiente de serviço de aplicações][ASEConfig]. 

Os itens de gestão adicionais são a gestão de DNS e gestão de certificados. Tem de obter e carregar o certificado utilizado para HTTPS, após a criação do ASE de ILB e substituí-lo antes de expirar. Como Azure possui o domínio base, ele pode fornecer certificados para os ASEs com um VIP externo. Uma vez que o subdomínio utilizado por um ASE de ILB pode ser qualquer coisa, tem de fornecer seu próprio certificado para HTTPS. 

#### <a name="dns-configuration"></a>Configuração de DNS
Quando utiliza um VIP externo, o DNS é gerido pelo Azure. Qualquer aplicação criada no seu ASE é adicionada automaticamente ao DNS do Azure, que é um DNS público. Num ASE de ILB, tem de gerir o seu próprio DNS. Para um determinado subdomínio, tais como contoso.corp.net, tem de criar um DNS registos que apontam para o seu endereço ILB para:

    * 
    publicar SCM.{0 ftp 


## <a name="getting-started"></a>Introdução
Para começar a utilizar com ambientes de serviço de aplicações, veja [introdução aos ambientes de serviço de aplicações][WhatisASE]

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
