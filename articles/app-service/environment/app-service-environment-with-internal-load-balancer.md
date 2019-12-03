---
title: Criar um ASE v1 ILB
description: Criando e usando um ASE com um ILB. Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d8ed6b1806e1cbb0ca7419c5892a4a84bc62e541
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688731"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Usando um Load Balancer interno com um Ambiente do Serviço de Aplicativo

> [!NOTE] 
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1. Há uma versão mais recente do Ambiente do Serviço de Aplicativo que é mais fácil de usar e é executada em uma infraestrutura mais potente. Para saber mais sobre a nova versão, comece com a [introdução ao ambiente do serviço de aplicativo](intro.md).
>

O recurso do Ambiente do Serviço de Aplicativo (ASE) é uma opção de serviço Premium do serviço de Azure App que fornece um recurso de configuração avançada que não está disponível nos carimbos multilocatários. Essencialmente, o recurso ASE implanta o serviço de Azure App em sua VNet (rede virtual) do Azure. Para obter uma compreensão maior dos recursos oferecidos pelos ambientes do serviço de aplicativo, leia a documentação [o que é uma ambiente do serviço de aplicativo][WhatisASE] . Se você não souber os benefícios de operar em uma VNet, leia as [perguntas frequentes sobre a rede virtual do Azure][virtualnetwork]. 

## <a name="overview"></a>Visão geral
Um ASE pode ser implantado com um ponto de extremidade acessível pela Internet ou com um endereço IP em sua VNet. Para definir o endereço IP para um endereço de VNet, você precisa implantar seu ASE com um Load Balancer interno (ILB). Quando o ASE é configurado com um ILB, você fornece:

* seu próprio domínio ou subdomínio. Para facilitar, este documento pressupõe subdomínio, mas você pode configurá-lo de qualquer forma. 
* o certificado usado para HTTPS
* Gerenciamento de DNS para seu subdomínio. 

Em contrapartida, pode fazer coisas como:

* Hospede aplicativos de intranet, como aplicativos de linha de negócios, com segurança na nuvem que você acessa por meio de um site a site ou VPN de ExpressRoute
* hospedar aplicativos na nuvem que não estão listados em servidores DNS públicos
* Crie aplicativos de back-end isolados da Internet com os quais seus aplicativos front-ends podem se integrar com segurança

#### <a name="disabled-functionality"></a>Funcionalidades desativadas
Há algumas coisas que você não pode fazer ao usar um ASE ILB. Essas coisas incluem:

* usando IPSSL
* atribuindo endereços IP a aplicativos específicos
* comprar e usar um certificado com um aplicativo por meio do Portal. É claro que você ainda pode obter certificados diretamente com uma autoridade de certificação e usá-los com seus aplicativos, mas não por meio do portal do Azure.

## <a name="creating-an-ilb-ase"></a>Criando um ASE ILB
Criar um ASE ILB não é muito diferente de criar um ASE normalmente. Para obter uma discussão mais aprofundada sobre como criar um ASE, consulte [como criar um ambiente do serviço de aplicativo][HowtoCreateASE]. O processo para criar um ASE ILB é o mesmo entre a criação de uma VNet durante a criação do ASE ou a seleção de uma VNet já existente. Para criar um ASE de ILB: 

1. Na portal do Azure, selecione **criar um recurso-> Web + celular-> ambiente do serviço de aplicativo**.
2. Selecione a sua subscrição.
3. Selecione ou crie um grupo de recursos.
4. Selecione ou crie uma VNet.
5. Crie uma sub-rede se selecionar uma VNet.
6. Selecione **rede virtual/local-configuração de > VNet** e defina o tipo de VIP como interno.
7. Forneça um nome de subdomínio (esse nome é o subdomínio usado para os aplicativos criados neste ASE).
8. Selecione **OK** e **criar**.

![][1]

No painel rede virtual, há uma opção de configuração de VNet que permite selecionar entre um VIP externo ou um VIP interno. O padrão é External. Se você o tiver definido como externo, seu ASE usará um VIP acessível pela Internet. Se você selecionar interno, seu ASE será configurado com um ILB em um endereço IP em sua VNet. 

Depois de selecionar interno, a capacidade de adicionar mais endereços IP ao ASE é removida e, em vez disso, você deve fornecer o subdomínio do ASE. Em um ASE com um VIP externo, o nome do ASE é usado no subdomínio para aplicativos criados nesse ASE. Se seu ASE for chamado de ***ContosoTest*** e seu aplicativo nesse ase for chamado de ***myTest***, o subdomínio será do formato ***ContosoTest.p.azurewebsites.net*** e a URL desse aplicativo será ***myTest.ContosoTest.p.azurewebsites.net***. Se você definir o tipo de VIP como interno, o nome do ASE não será usado no subdomínio para o ASE. Você especifica o subdomínio explicitamente. Se o subdomínio for ***contoso.Corp.net*** e você tiver feito um aplicativo nesse ase chamado ***Reporting***, a URL desse aplicativo será ***timereporting.contoso.Corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Aplicativos em um ASE ILB
Criar um aplicativo em um ASE ILB é o mesmo que criar um aplicativo em um ASE normalmente. 

1. Na portal do Azure, selecione **criar um recurso-> Web + celular-> Web** ou **móvel** ou **aplicativo de API**.
2. Introduza o nome da aplicação.
3. Selecione a sua subscrição.
4. Selecione ou crie um grupo de recursos.
5. Selecione ou crie um plano do serviço de aplicativo (ASP). Se estiver criando um novo ASP, selecione seu ASE como o local e selecione o pool de trabalho no qual você deseja que o ASP seja criado. Ao criar o ASP, você seleciona seu ASE como o local e o pool de trabalho. Quando você especificar o nome do aplicativo, verá que o subdomínio no nome do aplicativo é substituído pelo subdomínio do ASE. 
6. Selecione **Criar**. Certifique-se de marcar a caixa de seleção **fixar no painel** se desejar que o aplicativo seja exibido no painel. 

![][2]

No nome do aplicativo, o nome do subdomínio é atualizado para refletir o subdomínio do ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Publicar a validação de criação do ASE ILB
Um ASE de ILB é ligeiramente diferente do ASE não ILB. Como já observamos, você precisa gerenciar seu próprio DNS e também precisa fornecer seu próprio certificado para conexões HTTPS. 

Depois de criar seu ASE, você observará que o subdomínio mostra o subdomínio especificado e que há um novo item no menu **configuração** chamado **certificado ILB**. O ASE é criado com um certificado autoassinado que torna mais fácil testar o HTTPS. O portal informa que você precisa fornecer seu próprio certificado para HTTPS, mas isso é para incentivar você a ter um certificado que acompanha seu subdomínio. 

![][3]

Se você estiver simplesmente experimentando coisas e não souber como criar um certificado, poderá usar o aplicativo de console MMC do IIS para criar um certificado autoassinado. Depois de criado, você pode exportá-lo como um arquivo. pfx e, em seguida, carregá-lo na interface do usuário do certificado ILB. Quando você acessa um site protegido com um certificado autoassinado, seu navegador fornece um aviso de que o site que você está acessando não é seguro devido à incapacidade de validar o certificado. Se desejar evitar esse aviso, você precisará de um certificado assinado corretamente que corresponda ao subdomínio e tenha uma cadeia de confiança reconhecida pelo seu navegador.

![][6]

Se você quiser experimentar o fluxo com seus próprios certificados e testar o acesso HTTP e HTTPS ao seu ASE:

1. Vá para a interface do usuário do ASE depois que o ASE for criado **ase-> configurações-> certificados ILB**.
2. Defina o certificado ILB selecionando o arquivo PFX de certificado e forneça a senha. Essa etapa demora um pouco para ser processada e a mensagem de que uma operação de dimensionamento está em andamento é exibida.
3. Obtenha o endereço ILB para seu ASE (**ase-> Propriedades-> endereço IP virtual**).
4. Crie um aplicativo Web no ASE após a criação. 
5. Crie uma VM se você não tiver uma nessa VNET (não na mesma sub-rede que a interrupção do ASE ou das coisas).
6. Defina o DNS para seu subdomínio. Você pode usar um curinga com seu subdomínio em seu DNS ou se desejar fazer alguns testes simples, editar o arquivo de hosts em sua VM para definir o nome do aplicativo Web como endereço IP VIP. Se seu ASE tivesse o nome de subdomínio. ilbase.com e você tornou o aplicativo Web mytestapp para que ele fosse endereçado em mytestapp.ilbase.com, defina-o em seu arquivo hosts. (No Windows, o arquivo de hosts está em C:\WINDOWS\system32\drivers\etc.\)
7. Use um navegador nessa VM e acesse https://mytestapp.ilbase.com (ou qualquer nome do seu aplicativo Web com seu subdomínio).
8. Utilize um browser nessa VM e aceda a https://mytestapp.ilbase.com. Você deve aceitar a falta de segurança se estiver usando um certificado autoassinado. 

O endereço IP para seu ILB é listado em suas propriedades como o endereço IP virtual.

![][4]

## <a name="using-an-ilb-ase"></a>Usando um ASE ILB
#### <a name="network-security-groups"></a>Grupos de Segurança de Rede
Um ASE ILB habilita o isolamento de rede para seus aplicativos. Os aplicativos não estão acessíveis ou até mesmo conhecidos pela Internet. Essa abordagem é excelente para hospedar sites de intranet, como aplicativos de linha de negócios. Quando você precisar restringir o acesso ainda mais, ainda poderá usar NSGs (grupos de segurança de rede) para controlar o acesso no nível da rede. 

Se você quiser usar o NSGs para restringir ainda mais o acesso, certifique-se de não interromper a comunicação que o ASE precisa para operar. Embora o acesso HTTP/HTTPS seja apenas por meio do ILB usado pelo ASE, o ASE ainda depende dos recursos fora da VNet. Para ver qual acesso à rede ainda é necessário, consulte [controlando o tráfego de entrada para um ambiente do serviço de aplicativo][ControlInbound] e [detalhes de configuração de rede para ambientes do serviço de aplicativo com o ExpressRoute][ExpressRoute]. 

Para configurar seu NSGs, você deve saber o endereço IP que é usado pelo Azure para gerenciar seu ASE. Esse endereço IP também é o endereço IP de saída do ASE se ele fizer solicitações da Internet. O endereço IP de saída para seu ASE permanece estático durante a vida útil do seu ASE. Se você excluir e recriar seu ASE, receberá um novo endereço IP. Para localizar o endereço IP, acesse **configurações-> Propriedades** e localize o **endereço IP de saída**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Gerenciamento geral do ASE ILB
O gerenciamento de um ASE ILB é basicamente o mesmo que gerenciar um ASE normalmente. Você deve escalar verticalmente seus pools de trabalho para hospedar mais instâncias ASP e escalar verticalmente seus servidores front-end para lidar com quantidades maiores de tráfego HTTP/HTTPS. Para obter informações gerais sobre como gerenciar a configuração de um ASE, consulte [Configurando um ambiente do serviço de aplicativo][ASEConfig]. 

Os itens de gerenciamento adicionais são gerenciamento de certificados e gerenciamento de DNS. Você deve obter e carregar o certificado usado para HTTPS após a criação do ASE ILB e substituí-lo antes de expirar. Como o Azure possui o domínio base, ele pode fornecer certificados para ASEs com um VIP externo. Como o subdomínio usado por um ASE ILB pode ser qualquer coisa, você deve fornecer seu próprio certificado para HTTPS. 

#### <a name="dns-configuration"></a>Configuração de DNS
Ao usar um VIP externo, o DNS é gerenciado pelo Azure. Qualquer aplicação criada no seu ASE é adicionada automaticamente ao DNS do Azure, que é um DNS público. Num ASE de ILB, tem de gerir o seu próprio DNS. Para um determinado subdomínio, como contoso.corp.net, você deve criar registros DNS A que apontem para o endereço do ILB para:

    * 
    publicação de FTP *. SCM 


## <a name="getting-started"></a>Introdução
Para começar a usar os ambientes do serviço de aplicativo, consulte [introdução aos ambientes do serviço de aplicativo][WhatisASE]

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
