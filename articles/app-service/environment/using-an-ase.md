---
title: Utilizar o ambiente de serviço de aplicações - Azure
description: Como criar, publicar e dimensionar aplicações num ambiente de serviço de aplicações do Azure
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e9c34d3cfd5ce9bb3a8f9a9072f2843331065100
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66496510"
---
# <a name="use-an-app-service-environment"></a>Utilizar um ambiente de serviço de aplicações #

Ambiente de serviço de aplicações do Azure é uma implementação do serviço de aplicações do Azure numa sub-rede na rede virtual do Azure de um cliente. Ele consiste em:

- **Front-ends**: Os front-ends são onde HTTP/HTTPS termina num ambiente de serviço de aplicações (ASE).
- **Os operadores**: As funções de trabalho são os recursos que alojam as suas aplicações.
- **Base de dados**: A base de dados contém informações que define o ambiente.
- **Armazenamento**: O armazenamento é utilizado para alojar as aplicações publicadas do cliente.

> [!NOTE]
> Existem duas versões do ambiente de serviço de aplicações: ASEv1 e ASEv2. No ASEv1, tem de gerir os recursos antes de poder utilizá-los. Para saber como configurar e gerir o ASEv1, veja [configurar um ambiente de serviço de aplicações v1][ConfigureASEv1]. O restante deste artigo se concentra em ASEv2.
>
>

Pode implementar um ASE (ASEv1 e ASEv2) com um VIP externo ou interno para aceder à aplicação. A implementação com um VIP externo é frequentemente chamada ASE externo. A versão interna é chamada o ASE de ILB porque utiliza um balanceador de carga interno (ILB). Para saber mais sobre o ASE de ILB, veja [criar e utilizar um ASE de ILB][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Criar uma aplicação num ASE ##

Para criar uma aplicação num ASE, use o mesmo processo que quando cria normalmente, mas com algumas pequenas diferenças. Quando cria um novo plano do serviço de aplicações:

- Em vez de selecionar uma localização geográfica na qual implementar a sua aplicação, é possível escolher um ASE como a sua localização.
- Todos os planos de serviço de aplicações criados num ASE tem de ser uma escalão de preços isolada.

Se não tiver um ASE, pode criar uma ao seguir as instruções em [criar um ambiente de serviço de aplicações][MakeExternalASE].

Para criar uma aplicação num ASE:

1. Selecione **criar um recurso** > **Web + móvel** > **aplicação Web**.

2. Introduza um nome para a aplicação. Se já tiver selecionado um plano do serviço de aplicações num ASE, o nome de domínio para a aplicação reflete o nome de domínio do ASE.

    ![Seleção do nome de aplicação][1]

1. Selecione uma subscrição.

1. Introduza um nome para um novo grupo de recursos ou selecione **utilizar existente** e selecione um na lista pendente.

1. Selecione o seu SO. 

1. Selecione um plano de serviço de aplicações existente no seu ASE, ou criar um novo ao seguir estes passos:

    a. Selecione **criar um novo**.

    b. Introduza o nome para o seu plano do serviço de aplicações.

    c. Selecione o seu ASE no **localização** na lista pendente. 
    
    d. Selecione um **Isolated** escalão de preço. Selecione **selecione**.

    e. Selecione **OK**.
    
    ![Escalões de preço isolados][2]

    > [!NOTE]
    > Aplicações do Linux e aplicações do Windows não podem estar no mesmo plano serviço de aplicações, mas podem ser no mesmo ambiente de serviço de aplicações. 
    >

2. Selecione **Criar**.

## <a name="how-scale-works"></a>Como dimensionar funciona ##

Todas as aplicações de serviço de aplicações é executada num plano do serviço de aplicações. O modelo de contentor é ambientes manter planos de serviço de aplicações e planos do App Service manter aplicações. Quando dimensionar uma aplicação, dimensionar o plano do serviço de aplicações e, portanto, dimensionar todas as aplicações no mesmo plano.

No ASEv2, quando dimensiona um plano do serviço de aplicações, a infraestrutura necessária é automaticamente adicionada. Não existe um atraso de tempo para operações de dimensionamento, enquanto a infraestrutura é adicionada. No ASEv1, a infraestrutura necessária tem de ser adicionada antes de poder criar ou ampliar o seu plano do serviço de aplicações. 

No serviço de aplicações multi-inquilino, o dimensionamento é imediata, normalmente, uma vez que um pool de recursos está prontamente disponível para oferecer suporte a ele. Num ASE, não há nenhum esse buffer e os recursos são alocados na necessidade.

Num ASE, pode dimensionar até 100 instâncias. Esses 100 instâncias podem ser todos num único plano do serviço de aplicações ou distribuídos por vários planos de serviço de aplicações.

## <a name="ip-addresses"></a>Endereços IP ##

Serviço de aplicações tem a capacidade de atribuir um endereço IP dedicado a uma aplicação. Esta capacidade está disponível depois de configurar o SSL baseado em IP, conforme descrito em [vincular um certificado SSL personalizado já existente para o serviço de aplicações do Azure][ConfigureSSL]. No entanto, num ASE, existe uma exceção notável. Não é possível adicionar endereços IP adicionais a utilizar para um SSL baseado em IP num ASE de ILB.

No ASEv1, terá de atribuir os endereços IP como recursos antes de poder utilizá-los. ASEv2, utilize-os da sua aplicação tal como acontece no serviço de aplicações multi-inquilino. Há sempre um endereço de reserva no ASEv2 até 30 endereços IP. Sempre que utilizar um, outra, é adicionada para que um endereço sempre está prontamente disponível para utilização. Um tempo de atraso é necessário atribuir outro endereço IP, que impede a adicionar IP de endereços numa rápida sucessão.

## <a name="front-end-scaling"></a>Dimensionamento de front-end ##

ASEv2, ao aumentar horizontalmente seus planos de serviço de aplicações, funções de trabalho são automaticamente adicionadas para suportá-los. Cada ASE é criado com dois front-ends. Além disso, o front-ends automaticamente de escalamento horizontal a um ritmo de um front-end para todos os 15 instâncias nos seus planos de serviço de aplicações. Por exemplo, se tiver 15 instâncias, em seguida, terá três front-ends. Se a dimensionar para 30 instâncias, em seguida, tenha quatro de front-ends e assim por diante.

Este número de front-ends deve ser mais do que o suficiente para a maioria cenários. No entanto, pode aumentar horizontalmente a uma taxa mais rápida. Pode alterar a relação entre tão baixo como um front end para todos os cinco instâncias. Existe um custo para alterar a proporção. Para obter mais informações, consulte [preços do serviço de aplicações do Azure][Pricing].

Recursos de front-end são o ponto final HTTP/HTTPS para o ASE. Com a configuração de front-end predefinido, utilização de memória por front-end é consistentemente cerca de 60 por cento. Cargas de trabalho do cliente não são executados num front-end. O fator principal para um front-end com respeito ao dimensionar é a CPU, que é controlado por principalmente pelo tráfego HTTPS.

## <a name="app-access"></a>Acesso da aplicação ##

Num ASE externo, o domínio que é utilizado quando cria aplicações é diferente do serviço de aplicações multi-inquilino. Ele inclui o nome do ASE. Para obter mais informações sobre como criar um ASE externo, consulte [criar um ambiente de serviço de aplicações][MakeExternalASE]. O nome de domínio num ASE externo é semelhante *.&lt; asename&gt;. p.azurewebsites.net*. Por exemplo, se o seu ASE é denominado _ase externo_ e é hospedar um aplicativo chamado _contoso_ em que o ASE, aceder ao mesmo com os seguintes URLs:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

O URL contoso.scm.external-ase.p.azurewebsites.net é utilizado para aceder à consola Kudu ou para publicar seu aplicativo usando o web implementar. Para obter informações sobre a consola Kudu, consulte [consola Kudu para o serviço de aplicações do Azure][Kudu]. A consola Kudu dá-lhe uma IU da web para depuração, carregar ficheiros, edição de arquivos e muito mais.

Num ASE de ILB, é possível determinar o domínio no momento da implementação. Para obter mais informações sobre como criar um ASE de ILB, veja [criar e utilizar um ASE de ILB][MakeILBASE]. Se especificar o nome de domínio _ilb ase.info_, as aplicações no ASE utilizam esse domínio durante a criação de aplicações. Para a aplicação com o nome _contoso_, os URLs são:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publicação ##

Tal como acontece com o serviço de aplicações multi-inquilino, num ASE, pode publicar com:

- Implementação na Web.
- FTP.
- Integração contínua.
- Arraste e largue na consola Kudu.
- Um IDE, como o Visual IDÉIA Studio, Eclipse e IntelliJ.

Com um ASE externo, todas estas opções de publicação se comportam da mesma. Para obter mais informações, consulte [implementação no serviço de aplicações do Azure][AppDeploy]. 

A principal diferença com a publicação é relativamente a um ASE de ILB. Com um ASE de ILB, os pontos finais de publicação estão disponíveis apenas através do ILB. O ILB é um IP privado na sub-rede do ASE na rede virtual. Se não tiver acesso à rede para o ILB, não é possível publicar todas as aplicações no ASE. Conforme observado na [criar e utilizar um ASE de ILB][MakeILBASE], terá de configurar o DNS para as aplicações no sistema. Que inclui o ponto de extremidade do SCM. Se não estão definidos corretamente, não é possível publicar. Seus IDEs também tem de ter acesso à rede para o ILB para publicar diretamente no mesmo.

Uso imediato, os sistemas CI baseados na Internet, como o GitHub e do Azure DevOps, não funcionam com um ASE de ILB porque o ponto final de publicação não está acessível pela Internet. Para o Azure DevOps, pode contornar isso ao instalar um agente de hospedagem interna de lançamento na sua rede interna em que ele possa alcançar o ILB. Em alternativa, também pode usar um sistema de CI que utilize um modelo de extração, como o Dropbox.

Os pontos finais de publicação para aplicações num ASE de ILB utilizam o domínio com o qual o ASE de ILB foi criado. Pode ver no perfil de publicação da aplicação e no painel da aplicação do portal (no **descrição geral** > **Essentials** e, também no **propriedades**). 

## <a name="pricing"></a>Preços ##

Os preços chamado de SKU **Isolated** foi criado para ser utilizado apenas com ASEv2. Todos os planos de serviço de aplicações que estão alojados numa ASEv2 estão na SKU de preços isolada. As tarifas de plano de serviço de aplicações isoladas podem variar por região. 

Para além do preço para os planos de serviço de aplicações, existe uma tarifa fixa para o ASE em si. A tarifa fixa não são alterados com o tamanho do seu ASE e paga a infraestrutura de ASE com uma predefinição a aumentar a taxa de 1 adicionais front-end para todos os 15 instâncias de plano de serviço de aplicações.  

Se a taxa de dimensionamento predefinido de 1 front-end para todos os 15 instâncias de plano de serviço de aplicações não for rápida o suficiente, pode ajustar a taxa na qual front-ends são adicionados ou o tamanho do front-ends.  Quando ajustar o rácio ou tamanho, paga os núcleos de front-end que não seriam adicionados por predefinição.  

Por exemplo, se ajustar o rácio de dimensionamento para 10, um front-end é adicionado para cada 10 instâncias nos seus planos de serviço de aplicações. A tarifa fixa abrange uma taxa de dimensionamento de um front-end para todas as instâncias de 15. Com um rácio de dimensionamento de 10, paga uma tarifa para o terceiro front-end que é adicionado para as 10 instâncias de plano de serviço de aplicações. Não precisa de pagar para o mesmo quando atingir 15 instâncias porque ele foi adicionado automaticamente.

Se ajustar o tamanho do front-ends para 2 núcleos, mas não a ajustar o rácio, em seguida, paga os núcleos extras.  Um ASE é criado com 2 front-ends, portanto, mesmo abaixo do limiar de dimensionamento automático, que pagaria para 2 núcleos Extras se aumentou o tamanho de 2 núcleos de front-ends.

Para obter mais informações, consulte [preços do serviço de aplicações do Azure][Pricing].

## <a name="delete-an-ase"></a>Eliminar um ASE ##

Para eliminar um ASE: 

1. Uso **eliminar** na parte superior a **ambiente de serviço de aplicações** painel. 

1. Introduza o nome do seu ASE para confirmar que pretende eliminá-lo. Quando elimina um ASE, elimine todo o conteúdo dentro dele também. 

    ![Eliminação do ASE][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


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
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
