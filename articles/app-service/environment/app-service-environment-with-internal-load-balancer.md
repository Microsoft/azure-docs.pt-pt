---
title: Criar um ILB ASE v1
description: Criar e utilizar um ASE com um ILB. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c4e5dedf2075a2e13cc91c5eed2c0f03ba498b97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021525"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Utilização de um balançador de carga interna com um ambiente de serviço de aplicações

> [!NOTE] 
> Este artigo é sobre o App Service Environment v1. Existe uma versão mais recente do App Service Environment que é mais fácil de usar e funciona em infraestruturas mais poderosas. Para saber mais sobre a nova versão comece com a [Introdução ao Ambiente de Serviço de Aplicações.](intro.md)
>

A funcionalidade App Service Environment (ASE) é uma opção de serviço Premium do Azure App Service que oferece uma capacidade de configuração melhorada que não está disponível nos selos multi-inquilinos. A funcionalidade ASE implementa essencialmente o Serviço de Aplicações Azure na sua Rede Virtual Azure (VNet). Para obter uma maior compreensão das capacidades oferecidas pela App Service Environments leia a documentação [What is a App Service Environment.][WhatisASE] Se não conhece os benefícios de operar num VNet, leia o [Azure Virtual Network FAQ][virtualnetwork]. 

## <a name="overview"></a>Descrição Geral
Um ASE pode ser implantado com um ponto final acessível à Internet ou com um endereço IP no seu VNet. Para definir o endereço IP num endereço VNet, é necessário implantar o seu ASE com um Balançador de Carga Interna (ILB). Quando o seu ASE está configurado com um ILB, fornece:

* seu próprio domínio ou subdomínio. Para facilitar, este documento assume subdomínio, mas pode configurá-lo de qualquer forma. 
* o certificado utilizado para HTTPS
* Gestão de DNS para o seu subdomínio. 

Em contrapartida, pode fazer coisas como:

* hospedar aplicações intranet, como aplicações de linha de negócio, de forma segura na nuvem que acede através de um Site para Site ou ExpressRoute VPN
* aplicativos hospedeiros na nuvem que não estão listados em servidores DNS públicos
* criar aplicativos de backend isolados na Internet com os quais as suas aplicações front-end podem integrar de forma segura

#### <a name="disabled-functionality"></a>Funcionalidades desativadas
Há algumas coisas que não se pode fazer quando se utiliza um ILB ASE. Estas coisas incluem:

* utilizando IPSSL
* atribuindo endereços IP a aplicações específicas
* comprando e usando um certificado com uma app através do portal. É claro que ainda pode obter certificados diretamente com uma Autoridade de Certificados e usá-lo com as suas apps, mas não através do portal Azure.

## <a name="creating-an-ilb-ase"></a>Criação de um ILB ASE
Criar um ILB ASE não é muito diferente de criar um ASE normalmente. Para uma discussão mais aprofundada sobre a criação de um ASE, consulte [Como Criar um Ambiente de Serviço de Aplicações.][HowtoCreateASE] O processo de criação de um ILB ASE é o mesmo entre criar um VNet durante a criação da ASE ou selecionar um VNet pré-existente. Para criar um ASE de ILB: 

1. No portal Azure, selecione Criar um ambiente de serviço de **aplicações web +> de recursos ->.**
2. Selecione a sua subscrição.
3. Selecione ou crie um grupo de recursos.
4. Selecione ou crie uma VNet.
5. Crie uma sub-rede se selecionar um VNet.
6. Selecione **Rede Virtual/Localização -> Configuração VNet** e desa estale o tipo VIP para interno.
7. Forneça um nome de subdomínio (este nome é o subdomínio utilizado para as aplicações criadas neste ASE).
8. Selecione **OK** e, em seguida, **Crie**.

![Mostra os ecrãs utilizados para criar um ILB ASE.][1]

No painel de rede virtual, existe uma opção de configuração VNet que permite selecionar entre um VIP externo ou VIP interno. A predefinição é Externo. Se o tiver definido para External, o seu ASE utiliza um VIP acessível à Internet. Se selecionar Interno, o seu ASE está configurado com um ILB num endereço IP na sua VNet. 

Depois de selecionar Internamente, a capacidade de adicionar mais endereços IP ao seu ASE é removida e, em vez disso, deve fornecer o subdomínio do ASE. Numa ASE com UM VIP Externo, o nome do ASE é usado no subdomínio para aplicações criadas naquela ASE. Se o seu ASE for nomeado ***contosotest** _ e a sua aplicação em que a ASE é nomeada _*_mytest_*_, o subdomínio é do formato _*_contosotest.p.azurewebsites.net_*_ e o URL para essa aplicação é _* _mytest.contosotest.p.azurewebsites.net_**. Se definir o Tipo VIP para Interno, o seu nome ASE não é utilizado no subdomínio para o ASE. Especificar explicitamente o subdomínio. Se o seu subdomínio for ***contoso.corp.net**_ e tiver feito uma aplicação na _*_comunicação cronometragem_*_ denominada ASE, o URL para essa aplicação é _*_timereporting.contoso.corp.net_**.

## <a name="apps-in-an-ilb-ase"></a>Aplicativos em um ILB ASE
Criar uma aplicação num ILB ASE é o mesmo que criar uma app num ASE normalmente. 

1. No portal Azure, selecione Criar uma aplicação **de > de recursos Web + Mobile -> Web** ou **Mobile** ou **API**.
2. Introduza o nome da aplicação.
3. Selecione a sua subscrição.
4. Selecione ou crie um grupo de recursos.
5. Selecione ou crie um Plano de Serviço de Aplicações (ASP). Se criar um novo ASP, selecione o seu ASE como local e selecione o pool de trabalhadores em que pretende que o seu ASP seja criado. Ao criar o ASP, selecione o seu ASE como local e piscina de trabalhadores. Quando especificar o nome da aplicação, verá que o subdomínio no nome da sua aplicação é substituído pelo subdomínio para o seu ASE. 
6. Selecione **Criar**. Certifique-se de selecionar o Pin para a caixa **de verificação do painel** de instrumentos se quiser que a aplicação apareça no seu painel de instrumentos. 

![Mostra como criar uma aplicação num ILB ASE no portal Azure.][2]

Sob o nome da aplicação, o nome do subdomínio é atualizado para refletir o subdomínio do seu ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Validação de criação pós-ILB ASE
Um ASE de ILB é ligeiramente diferente do ASE não ILB. Como já foi notado, precisa de gerir o seu próprio DNS e também tem de fornecer o seu próprio certificado para ligações HTTPS. 

Depois de criar o seu ASE, irá notar que o subdomínio mostra o subdomínio especificado e há um novo item no menu **Definição** chamado **Certificado ILB**. O ASE é criado com um certificado auto-assinado que facilita a testar HTTPS. O portal diz-lhe que precisa de fornecer o seu próprio certificado para HTTPS, mas isto é para o encorajar a ter um certificado que acompanha o seu subdomínio. 

![Mostra o subdomínio especificado quando criou o seu ASE.][3]

Se estiver simplesmente a experimentar coisas e não souber como criar um certificado, pode utilizar a aplicação de consola IIS MMC para criar um certificado auto-assinado. Uma vez criado, pode exportá-lo como um ficheiro .pfx e depois carregá-lo no ILB Certificate UI. Ao aceder a um site protegido com um certificado auto-assinado, o seu navegador dá-lhe um aviso de que o site a que está a aceder não é seguro devido à incapacidade de validar o certificado. Se quiser evitar esse aviso, necessitará de um certificado devidamente assinado que corresponda ao seu subdomínio e tenha uma cadeia de confiança que é reconhecida pelo seu navegador.

![Mostra como usar a aplicação de consola IIS MMC para criar um certificado auto-assinado.][6]

Se quiser experimentar o fluxo com os seus próprios certificados e testar o acesso http e HTTPS ao seu ASE:

1. Aceda à ASE UI após a criação **da ASE -> Configurações -> Certificados ILB**.
2. Desave o certificado ILB selecionando o ficheiro pfx do certificado e fornecendo senha. Este passo demora um pouco a processar e aparece a mensagem de que está em curso uma operação de escala.
3. Obtenha o endereço ILB para o seu **ase (ASE -> Propriedades -> Endereço IP Virtual**).
4. Crie uma aplicação web no ASE após a criação. 
5. Crie um VM se não tiver um nesse VNET (Não na mesma sub-rede que o ASE ou as coisas se partirem).
6. Deslo partilhe o DNS para o seu subdomínio. Pode utilizar um wildcard com o seu subdomínio no seu DNS ou se pretender fazer alguns testes simples, edite o ficheiro de anfitriões no seu VM para definir o nome da aplicação web para o endereço IP VIP. Se o seu ASE tivesse o nome de subdomínio .ilbase.com e fizesse o mytestapp da aplicação web para que fosse abordado em mytestapp.ilbase.com, despeça-o no ficheiro dos anfitriões. (No Windows, o ficheiro dos anfitriões está em C:\Windows\System32\drivers\etc\)
7. Use um browser nesse VM e vá para `https://mytestapp.ilbase.com` (ou qualquer que seja o nome da sua aplicação web com o seu subdomínio).
8. Utilize um browser nessa VM e aceda a `https://mytestapp.ilbase.com`. Deve aceitar a falta de segurança se utilizar um certificado auto-assinado. 

O endereço IP do seu ILB está listado nas suas Propriedades como Endereço IP Virtual.

![Mostra que o endereço IP do seu ILB está listado nas suas propriedades como o Endereço IP Virtual.][4]

## <a name="using-an-ilb-ase"></a>Usando um ILB ASE
#### <a name="network-security-groups"></a>Grupos de Segurança de Rede
Um ILB ASE permite o isolamento da rede para as suas aplicações. As aplicações não são acessíveis nem sequer conhecidas pela internet. Esta abordagem é excelente para hospedar sites intranet, como aplicações de linha de negócio. Quando precisa de restringir ainda mais o acesso, ainda pode utilizar grupos de segurança de rede (NSGs) para controlar o acesso ao nível da rede. 

Se pretender utilizar NSGs para restringir ainda mais o acesso, tem de se certificar de que não quebra a comunicação de que o ASE necessita para poder operar. Mesmo que o acesso HTTP/HTTPS seja apenas através do ILB utilizado pelo ASE, o ASE ainda depende de recursos fora do VNet. Para ver que acesso à rede ainda é necessário, consulte controlar o tráfego de entrada para um Ambiente de Serviço de [Aplicações][ControlInbound] e  [Detalhes de Configuração de Rede para Ambientes de Serviço de Aplicações com ExpressRoute][ExpressRoute]. 

Para configurar os seus NSGs, deve conhecer o endereço IP que é utilizado pela Azure para gerir o seu ASE. Esse endereço IP é também o endereço IP de saída do seu ASE se fizer pedidos de internet. O endereço IP de saída do seu ASE permanece estático durante toda a vida do seu ASE. Se eliminar e recriar o seu ASE, obterá um novo endereço IP. Para encontrar o endereço IP, vá a **Definições -> Propriedades** e encontre o **Endereço IP de saída**. 

![Mostra onde pode encontrar o endereço IP de saída para o seu ASE.][5]

#### <a name="general-ilb-ase-management"></a>Gestão geral da ILB ASE
Gerir um ILB ASE é em grande parte o mesmo que gerir normalmente um ASE. Tem de aumentar as suas piscinas de trabalhadores para hospedar mais instâncias ASP e escalar os seus servidores frontais para lidar com quantidades aumentadas de tráfego HTTP/HTTPS. Para obter informações gerais sobre a gestão da configuração de um ASE, consulte [Configurar um Ambiente de Serviço de Aplicações.][ASEConfig] 

Os itens de gestão adicionais são a gestão de certificados e a gestão de DNS. Deve obter e carregar o certificado utilizado para HTTPS após a criação do ILB ASE e substituí-lo antes de expirar. Como a Azure é proprietária do domínio base, pode fornecer certificados para ASEs com um VIP externo. Uma vez que o subdomínio utilizado por um ILB ASE pode ser qualquer coisa, deve fornecer o seu próprio certificado para HTTPS. 

#### <a name="dns-configuration"></a>Configuração DNS
Ao utilizar um VIP Externo, o DNS é gerido pela Azure. Qualquer aplicação criada no seu ASE é adicionada automaticamente ao DNS do Azure, que é um DNS público. Num ASE de ILB, tem de gerir o seu próprio DNS. Para um determinado subdomínio, como contoso.corp.net, deve criar registos DNS A que apontam para o seu endereço ILB para:

- \*
- *.scm
- ftp
- publicar

## <a name="getting-started"></a>Introdução
Para começar com ambientes de serviço de aplicações, consulte [Introdução aos Ambientes de Serviço de Aplicações][WhatisASE]

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
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md