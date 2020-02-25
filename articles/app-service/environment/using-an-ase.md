---
title: Utilizar e gerir um Ambiente de Serviço de Aplicações
description: Como criar, publicar e escalar aplicações num ambiente de Serviço de Aplicações Azure. Encontre as tarefas comuns num documento.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7be1676c8949cd30d5e1fe93a73afd75a5a9b67f
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565688"
---
# <a name="use-an-app-service-environment"></a>Use um ambiente de serviço de aplicações #

O App Service Environment (ASE) é uma implantação do Serviço de Aplicações Azure numa subnet na rede virtual Azure de um cliente. Uma ASE consiste em:

- **Front-ends**: As extremidades dianteiras são onde HTTP/HTTPS termina em ambiente de Serviço de Aplicações.
- **Trabalhadores**: Os trabalhadores são os recursos que acolhem as suas apps.
- **Base de Dados**: A base de dados contém informações que definem o ambiente.
- **Armazenamento**: O armazenamento é utilizado para acolher as aplicações publicadas pelo cliente.

Pode implementar uma ASE com um VIP externo ou interno para acesso à aplicação. A implantação com um VIP externo é geralmente chamada de ASE Externa. A versão interna chama-se ILB ASE porque utiliza um equilibrista interno de carga (ILB). Para saber mais sobre o ILB ASE, consulte [Criar e utilizar um ILB ASE][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Criar uma aplicação numa ASE ##

Para criar uma aplicação numa ASE, utiliza-se o mesmo processo que quando a cria normalmente, mas com algumas pequenas diferenças. Quando cria um novo plano de Serviço de Aplicações (ASP):

- Em vez de escolher uma localização geográfica para implementar a sua aplicação, escolha uma ASE como localização.
- Todos os planos de Serviço de Aplicações criados numa ASE só podem estar num nível de preços isolado.

Se não tiver uma ASE, pode criar uma seguindo as instruções em Criar um ambiente de Serviço de [Aplicações][MakeExternalASE].

Para criar uma aplicação numa ASE:

1. Selecione **Criar um recurso** > Web + **Mobile** > **Web App**.

2. Insira um nome para a aplicação. Se já selecionou um plano de Serviço de Aplicações numa ASE, o nome de domínio da aplicação reflete o nome de domínio da ASE.

    ![Seleção de nomes de aplicativos][1]

1. Selecione uma subscrição.

1. Introduza um nome para um novo grupo de recursos ou selecione **Use existente** e selecione um da lista de lançamentos.

1. Selecione o seu SO. 

1. Selecione um plano de Serviço de Aplicações existente na sua ASE ou crie um novo seguindo estes passos:

    a. A partir do menu lateral esquerdo do portal Azure, selecione **Criar um recurso > Web App**.

    b. Selecione uma subscrição.
    
    c. Selecione ou crie o grupo de recursos.
    
    d. Forneça o nome da sua aplicação web.
    
    e. Selecione Código ou DockerContainer.
    
    f. Selecione stack Runtime.
    
    g. Selecione Linux ou Windows. 
    
    h. Selecione a sua lista de abandono seletiva da **Região.** 
    
    i. Selecione ou crie um novo plano de Serviço de Aplicações. Se criar um novo plano de Serviço de Aplicações, selecione o tamanho SKU **isolado** apropriado.
    
    ![Níveis de preços isolados][2]

    > [!NOTE]
    > As aplicações Linux e as aplicações Windows não podem estar no mesmo Plano de Serviço de Aplicações, mas podem estar no mesmo App Service Environment. 
    >

2. Selecione **Rever + criar** e, em seguida, selecione **Criar** se a informação estiver correta.

## <a name="how-scale-works"></a>Como funciona a escala ##

Todas as aplicações do App Service executam um plano de Serviço de Aplicações. Os Ambientes de Serviço de Aplicações possuem planos de Serviço de Aplicações e os planos do App Service possuem aplicações. Ao escalar uma aplicação, escala o plano de Serviço de Aplicações e, assim, escala todas as aplicações no mesmo plano.

Ao escalar um plano de Serviço de Aplicações, a infraestrutura necessária é adicionada automaticamente. Há um atraso de tempo para escalar as operações enquanto a infraestrutura é adicionada. Se fizer várias operações em escala em sequência, o primeiro pedido de escala de infraestrutura é acionado e os outros fazem fila. Quando a operação de primeira escala estiver concluída, os outros pedidos de infraestrutura funcionam todos em conjunto. Quando a infraestrutura é adicionada, os planos do Serviço de Aplicações são atribuídos conforme apropriado. A criação de um novo plano de Serviço de Aplicações é, em si mesma, uma operação de escala, uma vez que solicita hardware adicional. 

No Serviço de Aplicações multiarrendatárias, a escala é imediata porque um conjunto de recursos está prontamente disponível para apoiá-lo. Numa ASE, não existe tal tampão, e os recursos são afetados a quando necessários.

Numa ASE, pode escalar um plano de Serviço de Aplicações até 100 instâncias. Um ASE pode ter até 201 instâncias totais em todos os planos do Serviço de Aplicações que estão na ASE. 

## <a name="ip-addresses"></a>Endereços IP ##

O Serviço de Aplicações tem a capacidade de atribuir um endereço IP dedicado a uma aplicação. A capacidade de atribuir um IP dedicado a uma aplicação está disponível depois de configurar um SSL baseado em IP, como descrito em [Bind um certificado SSL personalizado existente para o Serviço de Aplicações Azure][ConfigureSSL]. Num ILB ASE, não é possível adicionar endereços IP adicionais para ser usado para um SSL baseado em IP.

Com uma ASE externa, pode configurar o SSL baseado em IP para a sua aplicação da mesma forma que faz no Serviço de Aplicações multiarrendatárias. Há sempre um endereço sobressalente na ASE até 30 endereços IP. Cada vez que utiliza um, outro é adicionado de modo a que um endereço esteja sempre disponível para utilização. É necessário um atraso de tempo para alocar outro endereço IP, o que impede a adição de endereços IP numa rápida sucessão.

## <a name="front-end-scaling"></a>Escala frontal ##

Quando você escala os seus planos de Serviço de Aplicações, os trabalhadores são automaticamente adicionados para apoiá-los. Cada ASE é criada com duas frentes. As extremidades dianteiras escalam automaticamente a uma taxa de uma das extremidades dianteiras para cada total de 15 casos de plano de app. Se tiver três planos de Serviço de Aplicações com cinco instâncias cada, terá um total de 15 instâncias e três frentes. Se escalar para um total de 30 casos, então tem quatro frentes, e assim por diante. 

O número de extremidades dianteiras que são atribuídas por defeito é bom para uma carga moderada. Pode alterar o rácio para um ponto mais baixo como uma frente para cada cinco casos. Também pode alterar o tamanho das extremidades dianteiras. Por defeito, são um único núcleo. Em vez disso, pode alterar o tamanho das extremidades dianteiras do portal para dois ou quatro tamanhos de núcleo. Há uma taxa para alterar o rácio ou os tamanhos das extremidades dianteiras. Para mais informações, consulte os preços do Serviço de [Aplicações Azure][Pricing]. Se procura melhorar a capacidade de carga da sua ASE, obterá uma melhoria melhor ao escalonar primeiro duas extremidades dianteiras centrais antes de ajustar a relação de escala. Alterar o tamanho do núcleo das suas extremidades dianteiras causará uma atualização da sua ASE e deve ser feito fora do horário normal de trabalho.

Os recursos front-endsão são o ponto final HTTP/HTTPS para a ASE. Com a configuração frontal padrão, o uso da memória por frente é consistentemente de cerca de 60 por cento. A principal razão para escalar as suas extremidades dianteiras é o CPU, que é essencialmente conduzido pelo tráfego HTTPS.

## <a name="app-access"></a>Acesso da aplicação ##

Numa ASE Externa, o sufixo de domínio utilizado para a criação de aplicações é&lt;o nome de *ssename&gt;.p.azurewebsites.net*. Se a sua ASE for nomeada _externa-ase_ e acolher uma aplicação chamada _contoso_ nesse ASE, você alcança-a nos seguintes URLs:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Para obter mais informações sobre como criar uma ASE Externa, consulte Criar um ambiente de Serviço de [Aplicações][MakeExternalASE]

Num ILB ASE, o sufixo de domínio utilizado para a criação de aplicações é *&lt;nome de séname&gt;.appserviceenvironment.net*. Se a sua ASE for nomeada _ilb-ase_ e acolher uma aplicação chamada _contoso_ nesse ASE, você alcança-a nos seguintes URLs:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Para obter mais informações sobre como criar um ILB ASE, consulte [Criar e utilizar um ILB ASE][MakeILBASE]. 

O URL scm é usado para aceder à consola Kudu ou para publicar a sua aplicação utilizando a implementação da web. Para obter informações sobre a consola Kudu, consulte a [consola Kudu para o Azure App Service][Kudu]. A consola Kudu dá-lhe um UI web para depurar, carregar ficheiros, editar ficheiros e muito mais.

## <a name="publishing"></a>A publicar ##

Tal como acontece com o Serviço de Aplicações multiarrendatárias, numa ASE pode publicar com:

- Implantação da web.
- FTP.
- Integração contínua.
- Arraste e deixe cair na consola Kudu.
- Um IDE, como Visual Studio, Eclipse ou IntelliJ IDEA.

Com uma ASE Externa, estas opções editoriais comportam-se todas da mesma forma. Para mais informações, consulte a Implantação no Serviço de [Aplicações Azure][AppDeploy]. 

A grande diferença com a publicação é no que diz respeito a um ILB ASE. Com um ILB ASE, os pontos finais de publicação estão todos disponíveis apenas através do ILB. O ILB encontra-se num IP privado na sub-rede ASE na rede virtual. Se não tiver acesso à rede ao ILB, não pode publicar nenhuma aplicação nessa ASE. Como notado no [Create e use um ILB ASE,][MakeILBASE]precisa configurar DNS para as aplicações no sistema. Isto inclui o ponto final do SCM. Se não forem definidos corretamente, não pode publicar. Os seus IDEs também precisam de ter acesso à rede ao ILB para poderem publicá-lo diretamente.

Fora da caixa, os sistemas ci baseados na Internet, como gitHub e Azure DevOps, não funcionam com um ILB ASE porque o ponto final de publicação não é acessível à Internet. Pode ativar a publicação de um ILB ASE da Azure DevOps instalando um agente de libertação auto-hospedado no VNet que contém o ILB ASE. Em alternativa, também pode utilizar um sistema CI que utiliza um modelo de pull, como o Dropbox.

Os pontos finais de publicação para aplicações num ASE de ILB utilizam o domínio com o qual o ASE de ILB foi criado. Pode vê-lo no perfil de publicação da aplicação e na lâmina do portal da aplicação (em **Visão Geral** > **Essentials** e também em **Propriedades).** 

## <a name="storage"></a>Armazenamento

A ASE tem 1 TB de armazenamento para todas as aplicações dentro da ASE. Um plano de serviço de aplicações SKU isolado tem um limite de 250 GB por padrão. Se tiver cinco ou mais planos de Serviço de Aplicações, tem de ter cuidado para não exceder o limite de 1 TB da ASE. Se necessitar de mais do limite de 250 GB num plano de Serviço de Aplicações, contacte o suporte para ajustar o limite do plano do Serviço de Aplicações para um máximo de 1 TB. Quando o limite do plano é ajustado, ainda existe um limite de 1 TB em todos os planos do Serviço de Aplicações na ASE. 

## <a name="logging"></a>Registo ##

Pode integrar a Sua ASE com o Monitor Azure para enviar registos sobre a ASE para armazenamento, Hub de Eventos ou Log Analytics. Os itens que hoje são registados são:

| Situação | Mensagem |
|---------|----------|
| A SEA não é saudável | A ASE especificada não é saudável devido a uma configuração de rede virtual inválida. A ASE será suspensa se o estado pouco saudável continuar. Certifique-se de que as orientações aqui definidas são seguidas: https://docs.microsoft.com/azure/app-service/environment/network-info |
| A subnet ASE está quase fora do espaço | A ASE especificada está numa sub-rede que está quase fora do espaço. Há {0} moradas restantes. Uma vez esgotados estes endereços, a ASE não será capaz de escalar  |
| A SeE aproxima-se do limite total de instâncias | A ASE especificada aproxima-se do limite total de instância da ASE. Atualmente contém {0} casos do Plano de Serviço de Aplicações de um máximo de 201 casos. |
| A ASE é incapaz de alcançar uma dependência | A ASE especificada não pode chegar {0}.  Certifique-se de que as orientações aqui definidas são seguidas: https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE está suspensa | A ASE especificada está suspensa. A suspensão DaSe pode ser devido a um défice de conta ou a uma configuração de rede virtual inválida. Resolver a causa principal e retomar a ASE para continuar a servir o tráfego |
| Atualização da ASE já começou | Iniciou-se uma atualização da plataforma para a ASE especificada. Espera-se atrasos nas operações de escala |
| Atualização da ASE já concluída | Uma atualização da plataforma para a ASE especificada terminou |
| As operações à escala já começaram | Um plano de Serviço de Aplicações ({0}) começou a escalar. Estado desejado: {1}{2} trabalhadores 
| As operações à escala já terminaram | Um plano de Serviço de Aplicações ({0}) terminou a escala. Estado atual: {1} eu{2} trabalhadores |
| As operações de escala falharam | Um plano de Serviço de Aplicações ({0}) falhou na escala. Estado atual: {1} eu{2} trabalhadores |

Para permitir o registo na sua ASE: 

1. Vá às definições de Diagnóstico no portal.  
1. Selecione Adicionar definição de diagnóstico.
1. Forneça um nome para a integração de registos
1. Verifique e configure os destinos de registo desejados. 
1. Verifique AppServiceEnvironmentPlatformLogs

![Definições de registo de diagnóstico da ASE][4]

Se integrar o Log Analytics, pode ver os registos selecionando Registos do portal ASE e criando uma consulta contra os AppServiceEnvironmentPlatformLogs. 

## <a name="upgrade-preference"></a>Preferência de upgrade ##

Se tiver vários ASEs, pode querer que alguns ASEs sejam atualizados antes de outros. Dentro do objeto ASE HostingEnvironment Resource Manager, pode definir um valor para UpgradePreference. A definição de upgradePreferencial pode ser configurada através de modelo, ARMClient ou https://resources.azure.com.  As três escolhas de valor são:

* Nenhum - Nenhum é o padrão e significa que o Azure irá atualizar a sua ASE em nenhum lote específico
* Early - Early significa que a sua ASE será atualizada na primeira metade das atualizações do Serviço de Aplicações
* Atrasado - Tarde significa que a sua ASE será atualizada na segunda metade das atualizações do Serviço de Aplicações

Se estiver a utilizar https://resources.azure.com, pode definir o valor de actualizaçãoPreferências por:

1. Ir resources.azure.com e assinar com a sua conta Azure
1. Navegue através de subscrições\/nome de subscrição \[\]\/recursosGroup\/\[nome de grupo de recursos\]\/fornecedores\/Microsoft.Web\/hostingEnvironments\/\[nome ASE\]
1. Selecionar Ler/Escrever no topo
1. Selecione Editar
1. Alterar o valor para upgradePreferência para o que for desejado das três opções.
1. Selecione Patch

![recursos azure com exibição][5]

A funcionalidade upgradePreferencials realmente faz mais sentido quando você tem vários ASEs como o seu ASEs atualizado "Early" será atualizado antes do seu ASEs "Late". Quando tiver vários ASEs, deve ter as suas ASEs de v/teste definidas como "Early" e as suas ASEs de produção a serem definidas como "Late".

## <a name="pricing"></a>Preços ##

O sKU de preços chamado **Isolado** é apenas para uso com ASE. Todos os planos do Serviço de Aplicações que estão hospedados na ASE estão no SKU de preços isolados. As tarifas do plano de serviço de aplicações isoladas podem variar por região. 

Além do preço dos seus planos de Serviço de Aplicações, existe uma taxa fixa para a própria ASE. A taxa fixa não muda com o tamanho da sua ASE e paga a infraestrutura ASE a uma taxa de escala padrão de uma frente adicional para cada 15 casos de plano de app service.  

Se a taxa de escala padrão de uma das extremidades dianteiras para cada 15 instâncias do plano de app Service não for suficientemente rápida, pode ajustar o rácio a que as extremidades dianteiras são adicionadas ou o tamanho das extremidades dianteiras.  Quando ajusta o rácio ou o tamanho, paga-se pelos núcleos frontais que não seriam adicionados por defeito.  

Por exemplo, se ajustar a relação de escala para 10, é adicionada uma parte frontal para cada 10 instâncias nos seus planos de Serviço de Aplicações. A taxa fixa cobre uma taxa de escala de uma frente para cada 15 instâncias. Com um rácio de escala de 10, você paga uma taxa pela terceira frente que é adicionada para os 10 casos do plano de App Service. Não é preciso pagar por isso quando se chega a 15 casos porque foi adicionado automaticamente.

Se ajustaro tamanho das extremidades dianteiras a dois núcleos mas não ajustar a relação, então paga os núcleos extra.  Uma ASE é criada com duas extremidades dianteiras, por isso mesmo abaixo do limiar de escala automática pagaria por dois núcleos extra se aumentasse o tamanho para duas extremidades dianteiras.

Para mais informações, consulte os preços do Serviço de [Aplicações Azure][Pricing].

## <a name="delete-an-ase"></a>Eliminar uma ASE ##

Para eliminar uma ASE: 

1. Utilize o **Delete** na parte superior da lâmina ambiente do serviço de **aplicações.** 

1. Insira o nome da sua ASE para confirmar que pretende apagá-la. Ao eliminar uma ASE, apaga-se todos os conteúdos que lhe são atribuídos. 

    ![Eliminação da ASE][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png


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
