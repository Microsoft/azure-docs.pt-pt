---
title: Usar e gerenciar o ASE
description: Como criar, publicar e dimensionar aplicativos em um ambiente de serviço Azure App. Encontre as tarefas comuns em um documento.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 806d6ddb09cbaf14c9c488e3d3b39909c22ef284
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75374935"
---
# <a name="use-an-app-service-environment"></a>Usar um ambiente do serviço de aplicativo #

Ambiente do Serviço de Aplicativo do Azure é uma implantação do serviço de Azure App em uma sub-rede na rede virtual do Azure de um cliente. Ele consiste em:

- **Front-ends**: os front-ends são onde o http/https termina em um ase (ambiente do serviço de aplicativo).
- **Trabalhadores**: os trabalhos são os recursos que hospedam seus aplicativos.
- **Banco de dados**: o banco de dados contém informações que definem o ambiente.
- **Armazenamento**: o armazenamento é usado para hospedar os aplicativos publicados pelo cliente.

> [!NOTE]
> Há duas versões do Ambiente do Serviço de Aplicativo: ASEv1 e ASEv2. No ASEv1, você deve gerenciar os recursos antes de poder usá-los. Para saber como configurar e gerenciar o ASEv1, consulte [configurar um ambiente do serviço de aplicativo v1][ConfigureASEv1]. O restante deste artigo se concentra em ASEv2.
>
>

Você pode implantar um ASE (ASEv1 e ASEv2) com um VIP externo ou interno para acesso ao aplicativo. A implantação com um VIP externo é normalmente chamada de ASE externo. A versão interna é chamada de ASE ILB porque usa um ILB (balanceador de carga interno). Para saber mais sobre o ASE ILB, confira [criar e usar um ase ILB][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Criar um aplicativo em um ASE ##

Para criar um aplicativo em um ASE, você usa o mesmo processo que ao criá-lo normalmente, mas com algumas pequenas diferenças. Quando você cria um novo plano do serviço de aplicativo:

- Em vez de escolher um local geográfico no qual implantar seu aplicativo, você escolhe um ASE como seu local.
- Todos os planos do serviço de aplicativo criados em um ASE devem estar em um tipo de preço isolado.

Se você não tiver um ASE, poderá criar um seguindo as instruções em [criar um ambiente de serviço de aplicativo][MakeExternalASE].

Para criar um aplicativo em um ASE:

1. Selecione **criar um recurso** > **Web + celular** > **aplicativo Web**.

2. Insira um nome para o aplicativo. Se você já tiver selecionado um plano do serviço de aplicativo em um ASE, o nome de domínio do aplicativo refletirá o nome de domínio do ASE.

    ![Seleção de nome do aplicativo][1]

1. Selecionar uma subscrição.

1. Insira um nome para um novo grupo de recursos ou selecione **usar existente** e selecione um na lista suspensa.

1. Selecione o seu SO. 

1. Selecione um plano do serviço de aplicativo existente em seu ASE ou crie um novo seguindo estas etapas:

    a. Selecione **criar novo**.

    b. Insira o nome para o plano do serviço de aplicativo.

    c. Selecione seu ASE na lista suspensa **local** . 
    
    d. Selecione um tipo de preço **isolado** . Selecione **Selecionar**.

    e. Selecione **OK**.
    
    ![Tipos de preço isolados][2]

    > [!NOTE]
    > Os aplicativos do Linux e os aplicativos do Windows não podem estar no mesmo plano do serviço de aplicativo, mas podem estar na mesma Ambiente do Serviço de Aplicativo. 
    >

2. Selecione **Criar**.

## <a name="how-scale-works"></a>Como funciona a escala ##

Cada aplicativo do serviço de aplicativo é executado em um plano do serviço de aplicativo. O modelo de contêiner é ambientes que mantêm planos do serviço de aplicativo e os planos do serviço de aplicativo mantêm aplicativos. Ao dimensionar um aplicativo, você dimensiona o plano do serviço de aplicativo e, portanto, dimensiona todos os aplicativos no mesmo plano.

No ASEv2, quando você dimensiona um plano do serviço de aplicativo, a infraestrutura necessária é adicionada automaticamente. Há um atraso de tempo para dimensionar as operações enquanto a infraestrutura é adicionada. No ASEv1, a infraestrutura necessária deve ser adicionada antes que você possa criar ou escalar horizontalmente seu plano do serviço de aplicativo. 

No serviço de aplicativo multilocatário, o dimensionamento geralmente é imediato porque um pool de recursos está prontamente disponível para dar suporte a ele. Em um ASE, não há tal buffer e os recursos são alocados após a necessidade.

Em um ASE, você pode escalar verticalmente até 100 instâncias. Essas instâncias de 100 podem estar todas em um único plano do serviço de aplicativo ou distribuídas entre vários planos do serviço de aplicativo.

## <a name="ip-addresses"></a>Endereços IP ##

O serviço de aplicativo tem a capacidade de alocar um endereço IP dedicado a um aplicativo. Esse recurso está disponível depois de configurar um SSL baseado em IP, conforme descrito em [associar um certificado SSL personalizado existente ao serviço de Azure app][ConfigureSSL]. No entanto, em um ASE, há uma exceção notável. Você não pode adicionar mais endereços IP a serem usados para um SSL baseado em IP em um ASE ILB.

No ASEv1, você precisa alocar os endereços IP como recursos antes de poder usá-los. No ASEv2, você os utiliza do seu aplicativo da mesma forma que no serviço de aplicativo multilocatário. Há sempre um endereço de reposição no ASEv2 até 30 endereços IP. Cada vez que você usa um, outro é adicionado para que um endereço sempre esteja prontamente disponível para uso. É necessário um atraso de tempo para alocar outro endereço IP, o que impede a adição de endereços IP em sucessão rápida.

## <a name="front-end-scaling"></a>Dimensionamento de front-end ##

No ASEv2, quando você dimensiona horizontalmente os planos do serviço de aplicativo, os trabalhadores são adicionados automaticamente para dar suporte a eles. Todo ASE é criado com dois front-ends. Além disso, os front-ends são dimensionados automaticamente a uma taxa de um front-end para cada 15 instâncias em seus planos do serviço de aplicativo. Por exemplo, se você tiver 15 instâncias, terá três front-ends. Se você dimensionar para 30 instâncias, terá quatro front-ends e assim por diante.

Esse número de front-ends deve ser mais do que o suficiente para a maioria dos cenários. No entanto, você pode escalar horizontalmente com uma taxa mais rápida. Você pode alterar a taxa para o máximo de um front-end para cada cinco instâncias. Há um encargo para alterar a taxa. Para obter mais informações, consulte [Azure app preço do serviço][Pricing].

Os recursos de front-end são o ponto de extremidade HTTP/HTTPS para o ASE. Com a configuração de front-end padrão, o uso de memória por front-end é consistentemente em cerca de 60%. As cargas de trabalho do cliente não são executadas em um front-end. O fator chave para um front-end em relação à escala é a CPU, que é controlada principalmente pelo tráfego HTTPS.

## <a name="app-access"></a>Acesso da aplicação ##

Em um ASE externo, o domínio que é usado quando você cria aplicativos é diferente do serviço de aplicativo multilocatário. Ele inclui o nome do ASE. Para obter mais informações sobre como criar um ASE externo, consulte [criar um ambiente do serviço de aplicativo][MakeExternalASE]. O nome de domínio em um ASE externo é semelhante a *.&lt;asename&gt;. p.azurewebsites.net*. Por exemplo, se seu ASE for chamado de _ase externo_ e você hospedar um aplicativo chamado _contoso_ nesse ASE, você o atingirá nas seguintes URLs:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

A URL contoso.scm.external-ase.p.azurewebsites.net é usada para acessar o console do kudu ou para publicar seu aplicativo usando a implantação da Web. Para obter informações sobre o console do kudu, consulte [console do kudu para Azure app Service][Kudu]. O console do kudu fornece uma interface do usuário da Web para depuração, carregamento de arquivos, edição de arquivos e muito mais.

Em um ASE ILB, você determina o domínio no momento da implantação. Para obter mais informações sobre como criar um ASE ILB, consulte [criar e usar um ase ILB][MakeILBASE]. Se você especificar o nome de domínio _ILB-ASE.info_, os aplicativos nesse ase usarão esse domínio durante a criação do aplicativo. Para o aplicativo chamado _contoso_, as URLs são:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publicação ##

Assim como com o serviço de aplicativo multilocatário, em um ASE, você pode publicar com:

- Implantação da Web.
- FTP.
- Integração contínua.
- Arraste e solte no console do kudu.
- Um IDE, como Visual Studio, Eclipse ou IntelliJ IDEA.

Com um ASE externo, essas opções de publicação se comportam da mesma forma. Para obter mais informações, consulte [Deployment in Azure app Service][AppDeploy]. 

A principal diferença com a publicação é em relação a um ASE ILB. Com um ASE ILB, os pontos de extremidade de publicação estão disponíveis apenas por meio do ILB. O ILB está em um IP privado na sub-rede do ASE na rede virtual. Se você não tiver acesso à rede para o ILB, não poderá publicar nenhum aplicativo nesse ASE. Conforme observado em [criar e usar um ase ILB][MakeILBASE], você precisa configurar o DNS para os aplicativos no sistema. Isso inclui o ponto de extremidade SCM. Se eles não estiverem definidos corretamente, você não poderá publicar. Seus IDEs também precisam ter acesso à rede para o ILB para publicar diretamente nele.

Os sistemas de CI baseados na Internet, como GitHub e Azure DevOps, não funcionam com um ASE ILB porque o ponto de extremidade de publicação não é acessível pela Internet. Para o Azure DevOps, você pode contornar isso instalando um agente de liberação auto-hospedado em sua rede interna, onde ele pode alcançar o ILB. Como alternativa, você também pode usar um sistema de CI que usa um modelo de pull, como o dropbox.

Os pontos finais de publicação para aplicações num ASE de ILB utilizam o domínio com o qual o ASE de ILB foi criado. Você pode vê-lo no perfil de publicação do aplicativo e na folha do portal do aplicativo (em **visão geral** > **Essentials** e também em **Propriedades**). 

## <a name="pricing"></a>Preços ##

O SKU de preços chamado **isolado** foi criado para uso somente com ASEv2. Todos os planos do serviço de aplicativo hospedados no ASEv2 estão no SKU de preços isolado. As taxas do plano do serviço de aplicativo isolado podem variar por região. 

Além do preço dos planos do serviço de aplicativo, há uma taxa fixa para o ASE em si. A taxa fixa não muda com o tamanho do seu ASE e paga pela infraestrutura do ASE em uma taxa de dimensionamento padrão de 1 front-end adicional para cada 15 instâncias do plano do serviço de aplicativo.  

Se a taxa de escala padrão de 1 front-end para cada 15 instâncias do plano do serviço de aplicativo não for rápida o suficiente, você poderá ajustar a taxa na qual os front-ends são adicionados ou o tamanho dos front-ends.  Ao ajustar a taxa ou o tamanho, você paga pelos núcleos de front-end que não seriam adicionados por padrão.  

Por exemplo, se você ajustar a taxa de escala para 10, um front-end será adicionado para cada 10 instâncias nos planos do serviço de aplicativo. A taxa fixa cobre uma taxa de escala de um front-end para cada 15 instâncias. Com uma taxa de escala de 10, você paga uma taxa para o terceiro front-end que é adicionado para as 10 instâncias do plano do serviço de aplicativo. Você não precisa pagar por ela quando atingir 15 instâncias porque ela foi adicionada automaticamente.

Se você ajustou o tamanho dos front-ends para 2 núcleos, mas não ajustar a taxa, você paga pelos núcleos extras.  Um ASE é criado com dois front-ends, portanto, mesmo abaixo do limite de dimensionamento automático, você pagaria por 2 núcleos extras se aumentar o tamanho para dois front-ends principais.

Para obter mais informações, consulte [Azure app preço do serviço][Pricing].

## <a name="delete-an-ase"></a>Excluir um ASE ##

Para excluir um ASE: 

1. Use **excluir** na parte superior da folha **ambiente do serviço de aplicativo** . 

1. Insira o nome do seu ASE para confirmar que você deseja excluí-lo. Ao excluir um ASE, você também exclui todo o conteúdo dentro dele. 

    ![Exclusão do ASE][3]

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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
