---
title: Utilizar um Ambiente do Serviço de Aplicações
description: Saiba como utilizar o seu Ambiente de Serviço de Aplicações para hospedar aplicações isoladas.
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d4cd673b5029d8379a699becd7339a265c787390
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586402"
---
# <a name="using-an-app-service-environment"></a>Usando um ambiente de serviço de aplicativo

O App Service Environment (ASE) é uma única implantação do Serviço de Aplicações Azure que injeta diretamente numa Rede Virtual Azure (VNet) à sua escolha. É um sistema que só é usado por um cliente. As aplicações implementadas na ASE estão sujeitas às funcionalidades de networking que são aplicadas na sub-rede ASE. Não existem funcionalidades adicionais que necessitem de ser ativadas nas suas apps para estarem sujeitas a essas funcionalidades de networking. 

## <a name="create-an-app-in-an-ase"></a>Criar uma app num ASE

Para criar uma aplicação num ASE, utiliza-se o mesmo processo que quando normalmente cria uma aplicação, mas com algumas pequenas diferenças. Quando cria um novo plano de Serviço de Aplicações:

- Em vez de escolher uma localização geográfica para implementar a sua app, escolha um ASE como sua localização.
- Todos os planos de Serviço de Aplicações criados num ASE só podem ser num nível de preços V2 isolado.

Se não tiver um ASE, pode criar um seguindo as instruções no [Criar um Ambiente de Serviço de Aplicações.][MakeASE]

Para criar uma aplicação num ASE:

1. Selecione **Criar uma**  >  **Web + Mobile** Web  >  **App**.

1. Selecione uma subscrição.

1. Introduza um nome para um novo grupo de recursos, ou selecione **Use existente** e selecione um da lista de drop-down.

1. Insira um nome para a aplicação. Se já selecionou um plano de Serviço de Aplicações num ASE, o nome de domínio da aplicação reflete o nome de domínio do ASE:

    ![criar uma app num ASE][1]

1. Selecione o seu tipo de Publicação, Stack e Sistema Operativo.

1.  Selecione região. Aqui precisa de selecionar um Ambiente de Serviço de Aplicações pré-existente v3.  Não pode fazer um ASEv3 durante a criação de aplicações 

1. Selecione um plano de Serviço de Aplicações existente no seu ASE ou crie um novo. Se criar uma nova aplicação, selecione o tamanho que deseja para o seu plano de Serviço de Aplicações. O único SKU que pode selecionar para a sua aplicação é um SKU de preços V2 isolado.

    ![Níveis de preços v2 isolados][2]

    > [!NOTE]
    > As aplicações Linux e Windows não podem estar no mesmo plano de Serviço de Aplicações, mas podem estar no mesmo Ambiente de Serviço de Aplicações.
    >

1. Selecione **Seguinte: Monitorização**  Se pretender ativar o App Insights com a sua aplicação, pode fazê-lo aqui durante o fluxo de criação. 

1.  Selecione **Seguinte: Tags** Adicionar todas as tags que quiser à aplicação  

1. Selecione **Rever + criar,** certifique-se de que a informação está correta e, em seguida, selecione **Criar**.

## <a name="how-scale-works"></a>Como a escala funciona

Todas as aplicações do Serviço de Aplicações são executados num plano de Serviço de Aplicações. Os Ambientes de Serviço de Aplicações detêm planos de Serviço de Aplicações e os planos do Serviço de Aplicações detêm aplicações. Ao escalar uma aplicação, também escala o plano de Serviço de Aplicações e todas as aplicações nesse mesmo plano.

Quando escala um plano de Serviço de Aplicações, a infraestrutura necessária é adicionada automaticamente. Há um atraso de tempo para escalar as operações enquanto a infraestrutura está a ser adicionada. Quando escalar um plano de Serviço de Aplicações, quaisquer outras operações de escala solicitadas com o mesmo SO e tamanho aguardarão até que o primeiro esteja concluído. Após o funcionamento da escala de bloqueio, todos os pedidos em fila são processados ao mesmo tempo. Uma operação de escala num tamanho e o SISTEMA não bloqueia a escala das outras combinações de tamanho e so. Por exemplo, se escalonou um plano de Serviço de Aplicações Windows I2v2, então, quaisquer outros pedidos para escalar o Windows I2v2 em que o ASE será feito em fila até que isso esteja concluído.   

No Multitenant App Service, o dimensionamento é imediato porque um conjunto de recursos está prontamente disponível para apoiá-lo. Numa ASE, não existe tal tampão, e os recursos são atribuídos com base na necessidade.

## <a name="app-access"></a>Acesso da aplicação

Numa ASE, o sufixo de domínio utilizado para a criação de aplicações é *. &lt; asename &gt; .appserviceenvironment.net*. Se o seu ASE for nomeado _my-ase_ e apresentar uma aplicação chamada _contoso_ nesse ASE, contacte-a nestes URLs:

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

Para obter informações sobre como criar um ASE, consulte [Criar um Ambiente de Serviço de Aplicações.][MakeASE]

O URL SCM é utilizado para aceder à consola Kudu ou para a publicação da sua aplicação utilizando o Web Deploy. Para obter informações sobre a consola Kudu, consulte a [consola Kudu para o Azure App Service][Kudu]. A consola Kudu dá-lhe uma UI web para depurar, carregar ficheiros, editar ficheiros e muito mais.

### <a name="dns-configuration"></a>Configuração do DNS 

O ASE utiliza pontos finais privados para o tráfego de entrada. Não é configurado automaticamente com zonas privadas Azure DNS. Se pretender utilizar o seu próprio servidor DNS, tem de adicionar os seguintes registos:

1. criar uma zona para &lt; o nome ASE &gt; .appserviceenvironment.net
1. criar um registo A nessa zona que aponta * para o endereço IP de entrada usado pelo seu ponto final privado ASE
1. criar um registo A nessa zona que aponta @ para o endereço IP de entrada usado pelo seu ponto final privado ASE
1. criar uma zona em &lt; nome ASE &gt; .appserviceenvironment.net nomeado scm
1. criar um registo A na zona scm que aponta * para o endereço IP usado pelo seu ponto final privado ASE

Para configurar DNS em zonas privadas Azure DNS:

1. criar uma zona privada Azure DNS chamada <ASE name> .appserviceenvironment.net
1. criar um registo A naquela zona que aponta * para o endereço IP ILB
1. criar um registo A naquela zona que aponta @ para o endereço IP ILB
1. criar um registo A nessa zona que aponta *.scm para o endereço IP ILB

As definições de DNS para o seu sufixo de domínio padrão ASE não restringem as suas aplicações a serem acessíveis apenas por esses nomes. Pode definir um nome de domínio personalizado sem qualquer validação nas suas apps num ASE. Se então pretender criar uma zona denominada *contoso.net,* pode fazê-lo e apontá-lo para o endereço IP de entrada. O nome de domínio personalizado funciona para pedidos de aplicações, mas não para o site scm. O site scm só está disponível *&lt; no appname &gt; .scm. &lt; asename &gt; .appserviceenvironment.net*. 

## <a name="publishing"></a>Publicar

Numa ASE, tal como acontece com o Serviço de Aplicações Multitenant, pode publicar por estes métodos:

- Implementação web
- Integração contínua (CI)
- Arrastar e largar na consola kudu
- Um IDE, como Visual Studio, Eclipse ou IntelliJ IDEA

Com uma ASE, os pontos finais de publicação só estão disponíveis através do endereço de entrada utilizado pelo ponto final privado. Se não tiver acesso à rede ao endereço de ponto final privado, não pode publicar nenhuma aplicação nesse ASE.  Os seus IDEs também devem ter acesso à rede ao ILB para publicar diretamente no mesmo.

Sem alterações adicionais, sistemas de CI baseados na Internet como GitHub e Azure DevOps não funcionam com um ILB ASE porque o ponto final de publicação não é acessível à Internet. Pode ativar a publicação de um ILB ASE da Azure DevOps instalando um agente de lançamento auto-hospedado na rede virtual que contém o ILB ASE. 

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

## <a name="internal-encryption"></a>Encriptação Interna

O App Service Environment funciona como um sistema de caixa preta onde não é possível ver os componentes internos ou a comunicação dentro do sistema. Para ativar uma produção mais elevada, a encriptação não é ativada por padrão entre componentes internos. O sistema é seguro, uma vez que o tráfego é completamente inacessível a ser monitorizado ou acedido. Se tiver um requisito de conformidade que exija encriptação completa do caminho dos dados de ponta a ponta, pode ativar isso no UI **de configuração** ASE.

![Ativar a encriptação interna][5]

Isto irá encriptar o tráfego interno da rede no seu ASE entre as extremidades dianteiras e os trabalhadores, encriptar o ficheiro de página e também encriptar os discos dos trabalhadores. Após o cluster de encriptação internaSeçãoSetting está ativado, pode haver um impacto no desempenho do seu sistema. Quando fizer a alteração para ativar o InternalEncrypation, o seu ASE ficará instável até que a alteração seja totalmente propagada. A propagação completa da alteração pode demorar algumas horas a ser concluída, dependendo do número de ocorrências que tem no seu ASE. Recomendamos vivamente que não o ative num ASE durante a sua utilização. Se precisar de o ativar num ASE ativamente utilizado, recomendamos vivamente que desvie o tráfego para um ambiente de reserva até que a operação esteja concluída.

## <a name="upgrade-preference"></a>Preferência de upgrade

Se tiver vários ASEs, talvez queira que alguns ASEs sejam atualizados antes de outros. Dentro do objeto ASE **HostingEnvironment Resource Manager,** pode definir um valor para **upgradePreferência**. A **definição de actualizaçãoPreference** pode ser configurada utilizando um modelo, ARMClient ou https://resources.azure.com . Os três valores possíveis são:

- **Nenhum**: O Azure atualizará o seu ASE num lote específico. este valor é a predefinição.
- **Início**: O seu ASE será atualizado na primeira metade das atualizações do Serviço de Aplicações.
- **Tarde**: O seu ASE será atualizado na segunda metade das atualizações do Serviço de Aplicações.

Para configurar a sua preferência de upgrade, vá ao UI **de configuração** ASE. 

A **funcionalidade de actualizaçãoPreferences** faz mais sentido quando tem várias ASEs porque as suas ASEs "Precoces" serão atualizadas antes das suas ASEs "Tardias". Quando tiver várias ASEs, deve definir o seu desenvolvimento e testar as ASEs como "Early" e as suas ASEs de produção para serem "Late".

## <a name="delete-an-ase"></a>Eliminar um ASE

Para eliminar um ASE:

1. Selecione **Excluir** no topo do painel de Ambiente do Serviço de **Aplicações.**

1. Insira o nome do seu ASE para confirmar que pretende eliminá-lo. Quando elimina um ASE, também elimina todo o conteúdo dentro do mesmo.

    ![Eliminação de ASE][3]

1. Selecione **OK**.

<!--Image references-->
[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
