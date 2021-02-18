---
title: Utilize e gere um Ambiente de Serviço de Aplicações
description: Aprenda a criar, publicar e escalar aplicações num Ambiente de Serviço de Aplicações. Encontre todas as tarefas comuns neste artigo.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 9/22/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a7fa9ece3728214fad31f0bae769e1e50206df7e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594055"
---
# <a name="use-an-app-service-environment"></a>Utilizar um Ambiente do Serviço de Aplicações

Um Ambiente de Serviço de Aplicações (ASE) é uma implementação do Azure App Service numa sub-rede na instância de Rede Virtual Azure de um cliente. Um ASE consiste em:

- **Extremidades frontais**: Quando HTTP ou HTTPS termina num Ambiente de Serviço de Aplicações
- **Trabalhadores**: Os recursos que acolhem as suas apps
- **Base de dados**: Detém informação que define o ambiente
- **Armazenamento**: Usado para hospedar as aplicações publicadas pelo cliente

Pode implementar um ASE com um IP virtual externo ou interno (VIP) para acesso a aplicações. Uma implantação com um VIP externo é comumente chamada de *ASE Externa*. Uma implantação com um VIP interno é chamada de *ILB ASE* porque utiliza um equilibrador de carga interno (ILB). Para saber mais sobre o ILB ASE, consulte [Criar e utilizar um ILB ASE][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Criar uma app num ASE

Para criar uma aplicação num ASE, utiliza-se o mesmo processo que quando normalmente cria uma aplicação, mas com algumas pequenas diferenças. Quando cria um novo plano de Serviço de Aplicações:

- Em vez de escolher uma localização geográfica para implementar a sua app, escolha um ASE como sua localização.
- Todos os planos de Serviço de Aplicações criados num ASE só podem estar num nível de preços isolado.

Se não tiver um ASE, pode criar um seguindo as instruções no [Criar um Ambiente de Serviço de Aplicações.][MakeExternalASE]

Para criar uma aplicação num ASE:

1. Selecione **Criar uma**  >  **Web + Mobile** Web  >  **App**.

1. Insira um nome para a aplicação. Se já selecionou um plano de Serviço de Aplicações num ASE, o nome de domínio da aplicação reflete o nome de domínio do ASE:

    ![Seleção de nomes de aplicativos][1]

1. Selecione uma subscrição.

1. Introduza um nome para um novo grupo de recursos, ou selecione **Use existente** e selecione um da lista de drop-down.

1. Selecione o seu SO.

1. Selecione um plano de Serviço de Aplicações existente no seu ASE, ou crie um novo seguindo estes passos:

    a. A partir do menu do portal Azure, selecione **Criar um recurso > Web App.**

    b. Selecione uma subscrição.

    c. Selecione ou crie o grupo de recursos.

    d. Insira o nome da sua aplicação web.

    e. Selecione **Código** ou **DockerContainer**.

    f. Selecione uma pilha de tempo de execução.

    exemplo, Selecione **Linux** ou **Windows**. 

    h. Selecione o seu ASE na lista de drop-down **da Região.** 

    i. Selecione ou crie um novo plano de Serviço de Aplicações. Se criar um novo plano de Serviço de Aplicações, selecione o tamanho **SKU isolado** apropriado.

    ![Níveis de preços isolados][2]

    > [!NOTE]
    > As aplicações Linux e Windows não podem estar no mesmo plano de Serviço de Aplicações, mas podem estar no mesmo Ambiente de Serviço de Aplicações.
    >

1. Selecione **Rever + criar,** certifique-se de que a informação está correta e, em seguida, selecione **Criar**.

## <a name="how-scale-works"></a>Como a escala funciona

Todas as aplicações do Serviço de Aplicações são executados num plano de Serviço de Aplicações. Os Ambientes de Serviço de Aplicações detêm planos de Serviço de Aplicações e os planos do Serviço de Aplicações detêm aplicações. Ao escalar uma aplicação, também escala o plano de Serviço de Aplicações e todas as aplicações nesse mesmo plano.

Quando escala um plano de Serviço de Aplicações, a infraestrutura necessária é adicionada automaticamente. Há um atraso de tempo para escalar as operações enquanto a infraestrutura está a ser adicionada. Se fizer várias operações de escala em sequência, o primeiro pedido de escala de infraestrutura é atutado e os outros ficam em fila. Quando a primeira operação de primeira escala termina, os outros pedidos de infraestrutura funcionam todos em conjunto. E quando a infraestrutura é adicionada, os planos do Serviço de Aplicações são atribuídos conforme apropriado. A criação de um novo plano de Serviço de Aplicações é em si uma operação de escala porque solicita hardware adicional.

No Multitenant App Service, o dimensionamento é imediato porque um conjunto de recursos está prontamente disponível para apoiá-lo. Numa ASE, não existe tal tampão, e os recursos são atribuídos com base na necessidade.

Numa ASE, pode escalar um plano de Serviço de Aplicações até 100 instâncias. Um ASE pode ter até 201 instâncias totais em todos os planos do Serviço de Aplicações naquela ASE.

## <a name="ip-addresses"></a>Endereços IP

O Serviço de Aplicações pode alocar um endereço IP dedicado a uma aplicação. Esta capacidade está disponível após configurar sSL baseado em IP, conforme descrito no [Bind um certificado TLS/SSL personalizado existente para o Azure App Service][ConfigureSSL]. Num ILB ASE, não é possível adicionar mais endereços IP para serem utilizados para SSL baseado em IP.

Com uma ASE Externa, pode configurar SSL baseado em IP para a sua aplicação da mesma forma que no Serviço de Aplicações multitenante. Há sempre um endereço extra no ASE, até 30 endereços IP. Cada vez que usa um, outro é adicionado para que um endereço esteja sempre disponível. É necessário um atraso de tempo para alocar outro endereço IP. Este atraso impede a adição de endereços IP numa rápida sucessão.

## <a name="front-end-scaling"></a>Escala frontal

Quando se escala os seus planos de Serviço de Aplicações, os trabalhadores são automaticamente adicionados para os suportar. Cada ASE é criado com duas extremidades dianteiras. As extremidades dianteiras escalam automaticamente a uma taxa de uma extremidade frontal para cada conjunto de 15 instâncias do plano de aplicações. Por exemplo, se tiver três planos de Serviço de Aplicações com cinco instâncias cada, terá um total de 15 instâncias e três frontais. Se escalar para um total de 30 casos, tem quatro extremidades dianteiras. Este padrão continua à medida que se escala.

O número de extremidades dianteiras que são atribuídas por defeito é bom para uma carga moderada. Pode baixar a proporção para um pouco como uma extremidade frontal para cada cinco instâncias. Também pode alterar o tamanho das extremidades dianteiras. Por defeito, são um núcleo único. No portal Azure, pode mudar o seu tamanho para dois ou quatro núcleos.

Há uma taxa para alterar o rácio ou os tamanhos frontais. Para mais informações, consulte [os preços do Azure App Service.][Pricing] Se quiser melhorar a capacidade de carga do seu ASE, obterá mais melhorias ao escalonar primeiro para as extremidades dianteiras de dois núcleos antes de ajustar a relação de escala. Alterar o tamanho central das suas extremidades dianteiras irá causar uma atualização do seu ASE e deve ser feito fora do horário normal de trabalho.

Os recursos frontais são o ponto final HTTP/HTTPS para o ASE. Com a configuração frontal padrão, o uso da memória por extremidade frontal é consistentemente de cerca de 60%. A principal razão para escalar as suas extremidades dianteiras é o uso do CPU, que é principalmente conduzido pelo tráfego HTTPS.

## <a name="app-access"></a>Acesso da aplicação

Numa ASE Externa, o sufixo de domínio utilizado para a criação de aplicações é *. &lt; asename &gt; .p.azurewebsites.net*. Se o seu ASE for nomeado _externo e_ tiver uma aplicação chamada _contoso_ nesse ASE, contacte-a nestes URLs:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Para obter informações sobre como criar um ASE Externo, consulte [Criar um Ambiente de Serviço de Aplicações.][MakeExternalASE]

Num ILB ASE, o sufixo de domínio utilizado para a criação de aplicações é *. &lt; asename &gt; .appserviceenvironment.net*. Se o seu ASE for nomeado _ilb-ase_ e tiver uma aplicação chamada _contoso_ nesse ASE, contacte-a nestes URLs:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Para obter informações sobre como criar um ILB ASE, consulte [Criar e utilizar um ILB ASE][MakeILBASE].

O URL SCM é utilizado para aceder à consola Kudu ou para a publicação da sua aplicação utilizando o Web Deploy. Para obter informações sobre a consola Kudu, consulte a [consola Kudu para o Azure App Service][Kudu]. A consola Kudu dá-lhe uma UI web para depurar, carregar ficheiros, editar ficheiros e muito mais.

### <a name="dns-configuration"></a>Configuração do DNS 

Quando utiliza um ASE Externo, as aplicações feitas no seu ASE estão registadas no Azure DNS. Não existem então passos adicionais numa ASE Externa para que as suas aplicações estejam disponíveis ao público. Com um ILB ASE, você deve gerir o seu próprio DNS. Pode fazê-lo no seu próprio servidor DNS ou nas zonas privadas Azure DNS.

Para configurar o DNS no seu próprio servidor DNS com o seu ILB ASE:

1. criar uma zona para &lt; o nome ASE &gt; .appserviceenvironment.net
1. criar um registo A naquela zona que aponta * para o endereço IP ILB
1. criar um registo A naquela zona que aponta @ para o endereço IP ILB
1. criar uma zona em &lt; nome ASE &gt; .appserviceenvironment.net nomeado scm
1. criar um registo A na zona scm que aponta * para o endereço IP ILB

Para configurar DNS em zonas privadas Azure DNS:

1. criar uma zona privada Azure DNS chamada &lt; ase nome &gt; .appserviceenvironment.net
1. criar um registo A naquela zona que aponta * para o endereço IP ILB
1. criar um registo A naquela zona que aponta @ para o endereço IP ILB
1. criar um registo A nessa zona que aponta *.scm para o endereço IP ILB

As definições DE DNS para o seu sufixo de domínio padrão ASE não restringem as suas aplicações a serem apenas acessíveis por esses nomes. Pode definir um nome de domínio personalizado sem qualquer validação nas suas aplicações num ILB ASE. Se então quiser criar uma zona denominada *contoso.net,* pode fazê-lo e apontá-la para o endereço IP ILB. O nome de domínio personalizado funciona para pedidos de aplicações, mas não para o site scm. O site scm só está disponível *&lt; no appname &gt; .scm. &lt; asename &gt; .appserviceenvironment.net*. 

A zona chamada *. &lt; asename &gt; .appserviceenvironment.net* é globalmente única. Antes de maio de 2019, os clientes puderam especificar o sufixo de domínio do ILB ASE. Se quisesse usar *.contoso.com* para o sufixo de domínio, poderia fazê-lo e isso incluiria o site scm. Houve desafios com este modelo, incluindo; gerir o certificado SSL predefinido, falta de um único sinal com o site scm e a obrigação de usar um certificado wildcard. O processo de atualização do certificado por defeito ILB ASE também foi disruptivo e causou o reinício da aplicação. Para resolver estes problemas, o comportamento do ILB ASE foi alterado para usar um sufixo de domínio baseado no nome do ASE e com um sufixo propriedade da Microsoft. A alteração ao comportamento do ILB ASE só afeta as ASEs do ILB feitas após maio de 2019. AsES ILB pré-existentes devem ainda gerir o certificado predefinido do ASE e a sua configuração de DNS.

## <a name="publishing"></a>Publicar

Numa ASE, tal como acontece com o Serviço de Aplicações Multitenant, pode publicar por estes métodos:

- Implementação web
- FTP
- Integração contínua (CI)
- Arrastar e largar na consola kudu
- Um IDE, como Visual Studio, Eclipse ou IntelliJ IDEA

Com uma ASE Externa, estas opções de publicação funcionam todas da mesma forma. Para obter mais informações, consulte [a Implementação no Serviço de Aplicações Azure.][AppDeploy]

Com um ILB ASE, os pontos finais de publicação só estão disponíveis através do ILB. O ILB encontra-se num IP privado na sub-rede ASE na rede virtual. Se não tiver acesso à rede ao ILB, não pode publicar nenhuma aplicação nesse ASE. Como indicado no [Create e use um ILB ASE,][MakeILBASE]tem de configurar DNS para as aplicações do sistema. Este requisito inclui o ponto final SCM. Se os pontos finais não forem definidos corretamente, não pode publicar. Os seus IDEs também devem ter acesso à rede ao ILB para publicar diretamente no mesmo.

Sem alterações adicionais, sistemas de CI baseados na Internet como GitHub e Azure DevOps não funcionam com um ILB ASE porque o ponto final de publicação não é acessível à Internet. Pode ativar a publicação de um ILB ASE da Azure DevOps instalando um agente de lançamento auto-hospedado na rede virtual que contém o ILB ASE. Em alternativa, também pode utilizar um sistema de CI que utiliza um modelo de pull, como o Dropbox.

Os pontos finais de publicação para aplicações num ASE de ILB utilizam o domínio com o qual o ASE de ILB foi criado. Pode vê-lo no perfil de publicação da aplicação e no painel de portais da aplicação (em **Overview**  >  **Essentials** e também em **Propriedades).**

## <a name="storage"></a>Armazenamento

Um ASE tem 1 TB de armazenamento para todas as aplicações no ASE. Um plano de Serviço de Aplicações no SKU de preços isolados tem um limite de 250 GB. Num ASE, 250 GB de armazenamento é adicionado por plano de Serviço de Aplicação até ao limite de 1 TB. Pode ter mais planos de Serviço de Aplicações do que apenas quatro, mas não há mais armazenamento adicionado para além do limite de 1 TB.

## <a name="logging"></a>Registo

Pode integrar o seu ASE com o Azure Monitor para enviar registos sobre o ASE para Azure Storage, Azure Event Hubs ou Log Analytics. Estes itens são registados hoje:

| Situação | Mensagem |
|---------|----------|
| ASE não é saudável | O ASE especificado não é saudável devido a uma configuração de rede virtual inválida. O ASE será suspenso se o estado pouco saudável continuar. Certifique-se de que as diretrizes aqui definidas são seguidas: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| Sub-rede ASE está quase fora do espaço | O ASE especificado está numa sub-rede que está quase fora do espaço. Há {0} endereços restantes. Uma vez esgotados estes endereços, o ASE não poderá escalar.  |
| ASE aproxima-se do limite total de instância | O ASE especificado aproxima-se do limite total de instância da ASE. Atualmente contém {0} instâncias do Plano de Serviço de Aplicações de um máximo de 201 instâncias. |
| ASE é incapaz de alcançar uma dependência | O ASE especificado não é capaz de alcançar {0} .  Certifique-se de que as diretrizes aqui definidas são seguidas: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| ASE está suspensa | O ASE especificado está suspenso. A suspensão ASE pode dever-se a um défice de conta ou a uma configuração de rede virtual inválida. Resolva a causa principal e retome a ASE para continuar a servir o tráfego. |
| Atualização ASE já começou | Iniciou-se uma atualização da plataforma para o ASE especificado. Espere atrasos nas operações de escala. |
| Atualização ASE concluída | Terminou uma atualização da plataforma para o ASE especificado. |
| As operações de escala já começaram | Um plano de Serviço de Aplicações {0} ( ) começou a escalonar. Estado desejado: {1} {2} trabalhadores.
| As operações de escala concluídas | Um plano de Serviço de Aplicações ( {0} ) terminou o dimensionamento. Estado atual: {1} eu {2} trabalhadores. |
| As operações de escala falharam | Um plano de Serviço de Aplicações {0} ( ) falhou na escala. Estado atual: {1} eu {2} trabalhadores. |

Para ativar o registo no seu ASE:

1. No portal, aceda às **definições de Diagnóstico**.
1. Selecione **Adicionar definição de diagnóstico**.
1. Fornecer um nome para a integração de registos.
1. Selecione e configure os destinos de registo que deseja.
1. Selecione **AppServiceEnvironmentPlatformLogs**.

![Definições de registo de diagnóstico ASE][4]

Se integrar-se com o Log Analytics, pode ver os registos selecionando **Registos** a partir do portal ASE e criando uma consulta contra **appServiceEnvironmentPlatformLogs**. Os registos só são emitidos quando o seu ASE tem um evento que irá desencadeá-lo. Se o seu ASE não tiver tal evento, não haverá registos. Para ver rapidamente um exemplo de registos no seu espaço de trabalho Log Analytics, execute uma operação de escala com um dos planos do Serviço de Aplicações no seu ASE. Em seguida, pode executar uma consulta contra **appServiceEnvironmentPlatformLogs** para ver esses registos. 

**Criar um alerta**

Para criar um alerta contra os seus registos, siga as instruções em [Criar, ver e gerir alertas de registo utilizando o Azure Monitor](../../azure-monitor/alerts/alerts-log.md). Em resumo:

* Abra a página Alertas no seu portal ASE
* Selecione **nova regra de alerta**
* Selecione o seu Recurso para ser o seu espaço de trabalho Log Analytics
* Desafie a sua condição com uma pesquisa de registo personalizado para usar uma consulta como: "AppServiceEnvironmentPlatformLogs | onde a ResultaDescription contém "começou a escalonar" ou o que quiser. Desa estale o limiar conforme apropriado. 
* Adicione ou crie um grupo de ação conforme desejado. O grupo de ação é onde define a resposta ao alerta, como o envio de um e-mail ou uma mensagem SMS
* Diga o seu alerta e guarde-o.

## <a name="upgrade-preference"></a>Preferência de upgrade

Se tiver vários ASEs, talvez queira que alguns ASEs sejam atualizados antes de outros. Dentro do objeto ASE **HostingEnvironment Resource Manager,** pode definir um valor para **upgradePreferência**. A **definição de actualizaçãoPreference** pode ser configurada utilizando um modelo, ARMClient ou https://resources.azure.com . Os três valores possíveis são:

- **Nenhum**: O Azure atualizará o seu ASE num lote específico. este valor é a predefinição.
- **Início**: O seu ASE será atualizado na primeira metade das atualizações do Serviço de Aplicações.
- **Tarde**: O seu ASE será atualizado na segunda metade das atualizações do Serviço de Aplicações.

Se estiver a https://resources.azure.com utilizar, siga estes passos para definir o valor das **actualizaçõesPreferences:**

1. Vá a resources.azure.com e inscreva-se na sua conta Azure.
1. Passe pelos recursos para subscrições \/ \[ nome de nome de grupo de \] \/ recursos Grupos \/ \[ fornecedores de nome \] \/ de grupo \/ Microsoft.Web \/ hospedando Nome \/ \[ ASE \] .
1. Selecione **Ler/Escrever** no topo.
1. Selecione **Editar**.
1. **DesatualizaçãoPreferência** para qual dos três valores deseja.
1. Selecione **Patch**.

![recursos azure com exibição][5]

A **funcionalidade de actualizaçãoPreferences** faz mais sentido quando tem várias ASEs porque as suas ASEs "Precoces" serão atualizadas antes das suas ASEs "Tardias". Quando tiver várias ASEs, deve definir o seu desenvolvimento e testar as ASEs como "Early" e as suas ASEs de produção para serem "Late".

## <a name="pricing"></a>Preços

O preço chamado SKU chamado *Isolado* é para uso apenas com ASEs. Todos os planos do Serviço de Aplicações que estão hospedados na ASE estão no SKU de preços isolados. As tarifas isoladas para os planos do Serviço de Aplicações podem variar por região.

Além do preço dos seus planos de Serviço de Aplicações, há uma taxa fixa para o próprio ASE. A taxa fixa não muda com o tamanho do seu ASE. Paga a infraestrutura ASE a uma taxa de escala padrão de uma extremidade frontal adicional para cada 15 instâncias do plano de aplicações.

Se a taxa de escala padrão de uma extremidade frontal para cada 15 instâncias do plano de aplicações não for suficientemente rápida, pode ajustar o rácio em que as extremidades dianteiras são adicionadas ou o tamanho das extremidades dianteiras. Quando ajusta o rácio ou o tamanho, paga-se pelos núcleos frontais que não seriam adicionados por defeito.

Por exemplo, se ajustar o rácio de escala para 10, é adicionada uma extremidade frontal para cada 10 instâncias nos seus planos de Serviço de Aplicações. A taxa fixa cobre uma taxa de escala de uma extremidade frontal para cada 15 instâncias. Com um rácio de escala de 10, você paga uma taxa pela terceira extremidade frontal que é adicionada para as 10 instâncias do plano de App Service. Não é preciso pagar quando se chega a 15 casos porque foi adicionado automaticamente.

Se ajustar o tamanho das extremidades dianteiras para dois núcleos, mas não ajustar a razão, paga pelos núcleos extras. Um ASE é criado com duas extremidades dianteiras, por isso mesmo abaixo do limiar de escala automático pagaria por dois núcleos extra se aumentasse o tamanho para extremidades dianteiras de dois núcleos.

Para mais informações, consulte [os preços do Azure App Service.][Pricing]

## <a name="delete-an-ase"></a>Eliminar um ASE

Para eliminar um ASE:

1. Selecione **Excluir** no topo do painel de Ambiente do Serviço de **Aplicações.**

1. Insira o nome do seu ASE para confirmar que pretende eliminá-lo. Quando elimina um ASE, também elimina todo o conteúdo dentro do mesmo.

    ![Eliminação de ASE][3]

1. Selecione **OK**.

## <a name="ase-cli"></a>ASE CLI

Existem capacidades de linha de comando para administrar a um ASE.  Os comandos az cli são notados abaixo.

```azurecli
C:\>az appservice ase --help

Group
    az appservice ase : Manage App Service Environments v2.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    create         : Create app service environment.
    delete         : Delete app service environment.
    list           : List app service environments.
    list-addresses : List VIPs associated with an app service environment.
    list-plans     : List app service plans associated with an app service environment.
    show           : Show details of an app service environment.
    update         : Update app service environment.

For more specific examples, use: az find "az appservice ase"
```



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
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
