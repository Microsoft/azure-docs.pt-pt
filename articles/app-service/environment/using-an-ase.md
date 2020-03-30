---
title: Utilizar e gerir um Ambiente de Serviço de Aplicações
description: Aprenda a criar, publicar e escalar aplicações num Ambiente de Serviço de Aplicações. Encontre todas as tarefas comuns neste artigo.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7ca6882aea7225dcb97c9f913267b2543de07fef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133128"
---
# <a name="use-an-app-service-environment"></a>Utilizar um Ambiente do Serviço de Aplicações

Um Ambiente de Serviço de Aplicações (ASE) é uma implantação do Azure App Service numa subnet na instância de rede virtual Azure de um cliente. Uma ASE consiste em:

- **Extremidades dianteiras**: Sempre que HTTP ou HTTPS terminam num Ambiente de Serviço de Aplicações.
- **Trabalhadores**: Os recursos que acolhem as suas apps.
- **Base de Dados**: Contém informações que definem o ambiente.
- **Armazenamento**: Utilizado para acolher as aplicações publicadas pelo cliente.

Pode implementar uma ASE com um IP virtual externo ou interno (VIP) para acesso à aplicação. Uma implantação com um VIP externo é geralmente chamada de *ASE Externa*. Uma implantação com um VIP interno é chamada de *ILB ASE* porque utiliza um equilibrista interno de carga (ILB). Para saber mais sobre o ILB ASE, consulte [Criar e utilizar um ILB ASE][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Criar uma aplicação numa ASE

Para criar uma aplicação numa ASE, utiliza-se o mesmo processo que quando normalmente cria uma aplicação, mas com algumas pequenas diferenças. Quando cria um novo plano de Serviço de Aplicações:

- Em vez de escolher uma localização geográfica para implementar a sua aplicação, escolha uma ASE como localização.
- Todos os planos de Serviço de Aplicações criados numa ASE só podem estar num nível de preços isolado.

Se não tiver uma ASE, pode criar uma seguindo as instruções em [Create a App Service Environment][MakeExternalASE].

Para criar uma aplicação numa ASE:

1. Selecione **Criar uma** > Web de recursos +**Aplicação Web****móvel** > .

1. Insira um nome para a aplicação. Se já selecionou um plano de Serviço de Aplicações numa ASE, o nome de domínio da aplicação reflete o nome de domínio da ASE:

    ![Seleção de nomes de aplicativos][1]

1. Selecione uma subscrição.

1. Introduza um nome para um novo grupo de recursos ou selecione **Use existente** e selecione um da lista de lançamentos.

1. Selecione o seu SO.

1. Selecione um plano de Serviço de Aplicações existente na sua ASE ou crie um novo seguindo estes passos:

    a. A partir do menu do lado esquerdo do portal Azure, selecione **Criar um recurso > Web App**.

    b. Selecione uma subscrição.

    c. Selecione ou crie o grupo de recursos.

    d. Insira o nome da sua aplicação web.

    e. Selecione **Código** ou **DockerContainer**.

    f. Selecione uma pilha de tempo de execução.

    g. Selecione **Linux** ou **Windows**. 

    h. Selecione a sua lista de abandono seletiva da **Região.** 

    i. Selecione ou crie um novo plano de Serviço de Aplicações. Se criar um novo plano de Serviço de Aplicações, selecione o tamanho SKU **isolado** apropriado.

    ![Níveis de preços isolados][2]

    > [!NOTE]
    > As aplicações Linux e as aplicações Windows não podem estar no mesmo plano de Serviço de Aplicações, mas podem estar no mesmo App Service Environment.
    >

1. Selecione **Rever + criar,** certifique-se de que a informação está correta e, em seguida, selecione **Criar**.

## <a name="how-scale-works"></a>Como funciona a escala

Todas as aplicações do App Service executam um plano de Serviço de Aplicações. Os Ambientes de Serviço de Aplicações possuem planos de Serviço de Aplicações e os planos do App Service possuem aplicações. Ao escalar uma aplicação, também escala o plano de Serviço de Aplicações e todas as aplicações nesse mesmo plano.

Ao escalar um plano de Serviço de Aplicações, a infraestrutura necessária é adicionada automaticamente. Há um atraso de tempo para escalar as operações enquanto a infraestrutura está a ser adicionada. Se fizer várias operações em escala em sequência, o primeiro pedido de escala de infraestrutura é feito e os outros estão em fila. Quando a operação de primeira escala termina, os outros pedidos de infraestrutura funcionam todos em conjunto. E quando a infraestrutura é adicionada, os planos do Serviço de Aplicações são atribuídos como apropriado. A criação de um novo plano de Serviço de Aplicações é, em si mesma, uma operação de escala porque solicita hardware adicional.

No Serviço de Aplicações multiarrendatárias, a escala é imediata porque um conjunto de recursos está prontamente disponível para apoiá-lo. Numa ASE, não existe tal tampão, e os recursos são atribuídos com base na necessidade.

Numa ASE, pode escalar um plano de Serviço de Aplicações até 100 instâncias. Uma ASE pode ter até 201 instâncias totais em todos os planos do Serviço de Aplicações nessa ASE.

## <a name="ip-addresses"></a>Endereços IP

O Serviço de Aplicações pode atribuir um endereço IP dedicado a uma aplicação. Esta capacidade está disponível depois de configurar o SSL baseado em IP, conforme descrito em [Bind um certificado SSL personalizado existente para o Serviço de Aplicações Azure][ConfigureSSL]. Num ILB ASE, não é possível adicionar mais endereços IP a serem utilizados para o SSL baseado em IP.

Com uma ASE Externa, pode configurar o SSL baseado em IP para a sua aplicação da mesma forma que no Serviço de Aplicações multiarrendatárias. Há sempre um endereço sobressalente na ASE, até 30 endereços IP. Cada vez que se usa um, outro é adicionado para que um endereço esteja sempre disponível. É necessário um atraso de tempo para atribuir outro endereço IP. Este atraso impede a adição de endereços IP numa rápida sucessão.

## <a name="front-end-scaling"></a>Escala frontal

Quando você escala os seus planos de Serviço de Aplicações, os trabalhadores são automaticamente adicionados para apoiá-los. Cada ASE é criada com duas extremidades dianteiras. As extremidades dianteiras escalam automaticamente a uma taxa de uma extremidade frontal para cada conjunto de 15 instâncias de plano de app service. Por exemplo, se tiver três planos de Serviço de Aplicações com cinco instâncias cada, terá um total de 15 instâncias e três frentes. Se escalar para um total de 30 casos, tem quatro frentes. Este padrão continua à medida que se escala.

O número de extremidades dianteiras que são atribuídas por defeito é bom para uma carga moderada. Pode baixar a razão para uma frente tão pequena como uma frente por cada cinco casos. Também pode alterar o tamanho das extremidades dianteiras. Por defeito, são um único núcleo. No portal Azure, pode mudar o seu tamanho para dois ou quatro núcleos.

Há uma taxa para alterar o rácio ou os tamanhos da frente. Para mais informações, consulte os preços do Serviço de [Aplicações Azure][Pricing]. Se quiser melhorar a capacidade de carga da sua ASE, obterá mais melhorias ao escalonar primeiro para duas extremidades dianteiras de dois núcleos antes de ajustar a relação de escala. Alterar o tamanho do núcleo das suas extremidades dianteiras causará uma atualização da sua ASE e deve ser feito fora do horário normal de trabalho.

Os recursos front-end são o ponto final HTTP/HTTPS para a ASE. Com a configuração frontal padrão, o uso da memória por extremidade frontal é consistentemente de cerca de 60 por cento. A principal razão para escalar as extremidades dianteiras é o uso de CPU, que é essencialmente conduzido pelo tráfego HTTPS.

## <a name="app-access"></a>Acesso da aplicação

Numa ASE Externa, o sufixo de domínio utilizado para a criação de aplicações é *.&lt; nome de&gt;asename .p.azurewebsites.net*. Se a sua ASE for nomeada _externa e_ acolher uma aplicação chamada _contoso_ nesse ASE, você alcança-a nestes URLs:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Para obter informações sobre como criar uma ASE Externa, consulte Criar um Ambiente de Serviço de [Aplicações.][MakeExternalASE]

Num ILB ASE, o sufixo de domínio utilizado para a criação de aplicações é *.&lt; nome de&gt;asename .appserviceenvironment.net.* Se a sua ASE for nomeada _ilb-ase_ e acolher uma aplicação chamada _contoso_ nesse ASE, você alcança-a nestes URLs:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Para obter informações sobre como criar um ILB ASE, consulte [Criar e utilizar um ILB ASE][MakeILBASE].

O URL SCM é utilizado para aceder à consola Kudu ou para publicar a sua aplicação utilizando o Web Deploy. Para obter informações sobre a consola Kudu, consulte a [consola Kudu para o Azure App Service][Kudu]. A consola Kudu dá-lhe um UI web para depurar, carregar ficheiros, editar ficheiros e muito mais.

## <a name="publishing"></a>Publicar

Numa ASE, tal como com o Serviço de Aplicações multiarrendatárias, pode publicar através destes métodos:

- Implantação web
- FTP
- Integração contínua (CI)
- Arraste e deixe cair na consola Kudu
- Um IDE, como Visual Studio, Eclipse ou IntelliJ IDEA

Com uma ASE Externa, estas opções editoriais funcionam da mesma forma. Para mais informações, consulte a Implantação no Serviço de [Aplicações Azure][AppDeploy].

A publicação é significativamente diferente com um ILB ASE, para o qual os pontos finais de publicação estão todos disponíveis apenas através do ILB. O ILB encontra-se num IP privado na sub-rede ASE na rede virtual. Se não tiver acesso à rede ao ILB, não pode publicar nenhuma aplicação nessa ASE. Como se nota no [Create e utilize um ILB ASE,][MakeILBASE]tem de configurar o DNS para as aplicações no sistema. Este requisito inclui o ponto final do SCM. Se os pontos finais não forem definidos corretamente, não pode publicar. Os seus IDEs também devem ter acesso à rede ao ILB para publicar diretamente no mesmo.

Sem alterações adicionais, sistemas ci baseados na Internet como gitHub e Azure DevOps não funcionam com um ILB ASE porque o ponto final de publicação não é acessível à Internet. Pode ativar a publicação de um ILB ASE da Azure DevOps instalando um agente de libertação auto-hospedado na rede virtual que contém o ILB ASE. Em alternativa, também pode utilizar um sistema CI que utiliza um modelo de pull, como o Dropbox.

Os pontos finais de publicação para aplicações num ASE de ILB utilizam o domínio com o qual o ASE de ILB foi criado. Pode vê-lo no perfil de publicação da app e no painel do portal da app (em **Overview** > **Essentials** e também em **Propriedades).**

## <a name="storage"></a>Storage

A ASE tem 1 TB de armazenamento para todas as aplicações da ASE. Um plano de serviço de aplicações no SKU de preços isolados tem um limite de 250 GB por padrão. Se tiver cinco ou mais planos de Serviço de Aplicações, tenha cuidado para não exceder o limite de 1-TB da ASE. Se precisar de mais do que o limite de 250-GB num plano de Serviço de Aplicações, contacte o suporte para ajustar o limite do plano do Serviço de Aplicações para um máximo de 1 TB. Quando o limite do plano é ajustado, ainda há um limite de 1 TB em todos os planos do Serviço de Aplicações na ASE.

## <a name="logging"></a>Registo

Pode integrar a Sua ASE com o Monitor Azure para enviar registos sobre a ASE para o Armazenamento Azure, Hubs de Eventos Azure ou Log Analytics. Estes itens estão registados hoje:

| Situação | Mensagem |
|---------|----------|
| A SEA não é saudável | A ASE especificada não é saudável devido a uma configuração de rede virtual inválida. A ASE será suspensa se o estado pouco saudável continuar. Certifique-se de que https://docs.microsoft.com/azure/app-service/environment/network-infoas diretrizes aqui definidas são seguidas: . |
| A subnet ASE está quase fora do espaço | A ASE especificada está numa sub-rede que está quase fora do espaço. Ainda {0} restam endereços. Uma vez esgotados estes endereços, a ASE não poderá escalar.  |
| A SeE aproxima-se do limite total de instâncias | A ASE especificada aproxima-se do limite total de instância da ASE. Atualmente contém {0} instâncias do Plano de Serviço de Aplicações de um máximo de 201 casos. |
| A ASE é incapaz de alcançar uma dependência | A ASE especificada não {0}é capaz de alcançar .  Certifique-se de que https://docs.microsoft.com/azure/app-service/environment/network-infoas diretrizes aqui definidas são seguidas: . |
| ASE está suspensa | A ASE especificada está suspensa. A suspensão DaSe pode ser devido a um défice de conta ou a uma configuração de rede virtual inválida. Resolva a causa principal e retome a ASE para continuar a servir o tráfego. |
| Atualização da ASE já começou | Iniciou-se uma atualização da plataforma para a ASE especificada. Espere atrasos nas operações de escala. |
| Atualização da ASE já concluída | Uma atualização da plataforma para a ASE especificada terminou. |
| As operações à escala já começaram | Um plano de{0}serviço de aplicações () começou a escalar. Estado desejado: {1} {2} eu trabalhadores.
| As operações à escala já terminaram | Um plano de{0}serviço de aplicações () terminou a escala. Estado {1} atual:{2} I trabalhadores. |
| As operações de escala falharam | Um plano{0}de serviço de aplicações falhou na escala. Estado {1} atual:{2} I trabalhadores. |

Para permitir o registo na sua ASE:

1. No portal, vá às **definições**de Diagnóstico .
1. **Selecione Adicionar definição de diagnóstico**.
1. Forneça um nome para a integração de registos.
1. Selecione e configure os destinos de registo que deseja.
1. Selecione **AppServiceEnvironmentPlatformLogs**.

![Definições de registo de diagnóstico da ASE][4]

Se integrar o Log Analytics, pode ver os registos selecionando **Registos** do portal ASE e criando uma consulta contra o **AppServiceEnvironmentPlatformLogs**.

## <a name="upgrade-preference"></a>Preferência de upgrade

Se tiver vários ASEs, pode querer que alguns ASE sejam atualizados antes de outros. Dentro do objeto ASE **HostingEnvironment Resource Manager,** pode definir um valor para **upgradePreferência**. A definição **de upgradePreferencial** pode ser configurada https://resources.azure.comutilizando um modelo, ARMClient ou . Os três valores possíveis são:

- **Nenhum**: O Azure atualizará a sua ASE em nenhum lote específico. este valor é a predefinição.
- **Early**: Your ASE will be upgrade in the first half of the App Service upgrades.
- **Tarde**: A Sua ASE será atualizada na segunda metade das atualizações do Serviço de Aplicações.

Se estiver a https://resources.azure.comutilizar, siga estes passos para definir o valor **de upgradePreferências:**

1. Vá ao resources.azure.com e inscreva-se na sua conta Azure.
1. Passe pelos recursos para\/\[\]\/subscrições\/\[nome de\]\/recursos\/Recursos\/Recursos Grupos fornecedores de nome de grupo Microsoft.Web hostingEnvironments\/\[Nome ASE\].
1. Selecione **Ler/Escrever** no topo.
1. Selecione **Editar**.
1. Definir **upgradePreferência** para qualquer um dos três valores que desejar.
1. Selecione **Patch**.

![recursos azure com exibição][5]

A funcionalidade **upgradePreferencials** faz mais sentido quando tiver vários ASEs porque o seu ASEs "Early" será atualizado antes do seu ASEs "Late". Quando tiver vários ASEs, deve definir o seu desenvolvimento e teste ases para ser "Early" e a sua produção de ASEs para ser "Tarde".

## <a name="pricing"></a>Preços

O sKU de preços chamado *Isolado* é para uso apenas com ASEs. Todos os planos do Serviço de Aplicações que estão hospedados na ASE estão no SKU de preços isolados. As tarifas isoladas para os planos do Serviço de Aplicações podem variar por região.

Além do preço dos seus planos de Serviço de Aplicações, há uma taxa fixa para a própria ASE. A taxa fixa não muda com o tamanho da sua ASE. Paga a infraestrutura ASE a uma taxa de escala padrão de uma extremidade frontal adicional para cada 15 casos de plano de app service.

Se a taxa de escala padrão de uma extremidade frontal para cada 15 instâncias do plano de app Service não for suficientemente rápida, pode ajustar a razão a que as extremidades dianteiras são adicionadas ou o tamanho das extremidades dianteiras. Quando ajusta o rácio ou tamanho, paga-se pelos núcleos frontais que não seriam adicionados por defeito.

Por exemplo, se ajustar a relação de escala para 10, uma extremidade frontal é adicionada para cada 10 instâncias nos seus planos de Serviço de Aplicações. A taxa fixa cobre uma taxa de escala de uma extremidade frontal para cada 15 instâncias. Com um rácio de escala de 10, paga-se uma taxa pela terceira parte frontal que é adicionada para os 10 casos do plano de aplicações. Não é preciso pagar por isso quando se chega a 15 casos porque foi adicionado automaticamente.

Se ajustar o tamanho das extremidades dianteiras para dois núcleos mas não ajustar a relação, paga-se pelos núcleos extra. Uma ASE é criada com duas extremidades dianteiras, por isso mesmo abaixo do limiar de escala automática pagaria por dois núcleos extra se aumentasse o tamanho para duas extremidades dianteiras.

Para mais informações, consulte os preços do Serviço de [Aplicações Azure][Pricing].

## <a name="delete-an-ase"></a>Eliminar uma ASE

Para eliminar uma ASE:

1. **Selecione Eliminar** na parte superior do painel ambiente de serviço de **aplicação.**

1. Insira o nome da sua ASE para confirmar que pretende apagá-la. Ao eliminar uma ASE, também elimina todos os conteúdos que lhe são atribuídos.

    ![Eliminação da ASE][3]

1. Selecione **OK**.

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
