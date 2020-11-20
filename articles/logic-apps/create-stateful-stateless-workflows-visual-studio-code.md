---
title: Criar fluxos de trabalho de automatização (pré-visualização) no Código do Estúdio Visual
description: Criar fluxos de trabalho de automação apátridas ou estatais com a extensão Azure Logic Apps (Preview) no Código do Estúdio Visual para integrar apps, dados, serviços na nuvem e sistemas de instalações
services: logic-apps
ms.suite: integration
ms.reviewer: deli, rohitha, vikanand, hongzili, sopai, absaafan, logicappspm
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 14809cb28870e88cfa584c4f02360d50beabf901
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981056"
---
# <a name="create-stateful-or-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Criar fluxos de trabalho com ou sem estado no Visual Studio Code com a extensão do Azure Logic Apps (Pré-visualização)

> [!IMPORTANT]
> Esta capacidade está disponível em pré-visualização pública, é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Para criar fluxos de trabalho de aplicações lógicas que se integram entre apps, dados, serviços na nuvem e sistemas, pode utilizar o Código do Estúdio Visual e a extensão Azure Logic Apps (Preview) para construir e executar localmente [fluxos de aplicações *lógicas stateful* e *apátridas*](#stateful-stateless) no seu ambiente de desenvolvimento.

![Screenshot que mostra Código de Estúdio Visual e fluxo de trabalho de aplicações lógicas.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

As aplicações lógicas que cria com a extensão de pré-visualização pública utilizam o novo tipo de recurso **Logic App (Preview)** e são alimentadas pelo tempo de funcionamento das [Funções Azure](../azure-functions/functions-overview.md) no seu ambiente local. Este novo tipo de recurso pode incluir vários fluxos de trabalho e é semelhante em algumas formas ao tipo de recurso **De aplicação de função,** que pode incluir múltiplas funções.

Entretanto, o tipo original de recursos **de Apps Lógicas** ainda existe para criar e utilizar no Código do Estúdio Visual e no portal Azure. No entanto, as experiências para o tipo de recurso original são separadas do novo tipo de recurso. Neste momento, tanto as **aplicações lógicas** como os tipos de recursos **da Logic App (Preview)** podem existir ao mesmo tempo no Código do Estúdio Visual e no portal Azure. Pode ver e aceder a todas as aplicações lógicas implementadas na sua subscrição Azure, mas elas aparecem e são mantidas separadamente nas suas próprias categorias e secções.

Este artigo fornece uma visão geral de alto nível [sobre esta pré-visualização pública](#whats-new), descreve vários aspetos sobre o tipo de recurso Logic App **(Preview)** e como criar este recurso utilizando o Código do Estúdio Visual:

* Como as aplicações [lógicas apátridas e apátridas](#stateful-stateless) diferem umas das outras.

* Como satisfazer os [requisitos de configuração](#prerequisites) e [configurar](#set-up) o Código do Estúdio Visual para a extensão de pré-visualização pública.

* Como construir novos fluxos de trabalho **da Logic App (Preview)** [criando um projeto e selecionando um modelo de fluxo de trabalho.](#create-project)

* Como executar localmente e depurar as suas novas aplicações lógicas no Código do Estúdio Visual.

* Como publicar estas novas aplicações lógicas diretamente do Código do Estúdio Visual [para OZure](#publish-azure) ou [para um recipiente Docker](#deploy-docker) que podes correr em qualquer lugar. Para mais informações sobre o Docker, [vês o que é o Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)

<a name="whats-new"></a>

## <a name="whats-in-this-public-preview"></a>O que há nesta pré-estreia pública?

A extensão Azure Logic Apps (Preview) traz muitas capacidades de Aplicações Lógicas atuais e adicionais à sua experiência de desenvolvimento local no Código do Estúdio Visual, por exemplo:

* Crie aplicativos lógicos para integração e automatização de fluxos de trabalho a partir de mais de [390 conectores](/connectors/connector-reference/connector-reference-logicapps-connectors) para aplicações e serviços de Software-as-a-Service (SaaS) e Platform-as-a-Service (PaaS) além de conectores para sistemas no local.

  * Alguns conectores geridos, tais como Azure Service Bus, Azure Event Hubs e SQL Server funcionam de forma semelhante a gatilhos nativos incorporados e ações como a ação HTTP.

  * Crie e implemente aplicações lógicas que podem ser executadas em qualquer lugar porque o serviço Azure Logic Apps gera cadeias de conexão Shared Access Signature (SAS) que estas aplicações lógicas podem usar para enviar pedidos para o ponto final de tempo de execução da ligação na nuvem. O serviço De aplicações lógicas guarda estas cadeias de ligação com outras definições de aplicação para que possa armazenar facilmente estes valores no Cofre da Chave Azure quando se implanta no Azure.

    > [!NOTE]
    > Por predefinição, um recurso **Logic App (Preview)** tem a sua [identidade gerida atribuída ao sistema](../logic-apps/create-managed-service-identity.md) automaticamente habilitada a autenticar ligações em tempo de execução. Esta identidade difere das credenciais de autenticação ou da cadeia de ligação que utiliza quando cria uma ligação. Se desativar esta identidade, as ligações não funcionarão em tempo de execução.

* Crie aplicações lógicas apátridas que só funcionam na memória para que terminem mais rapidamente, respondam mais rapidamente, tenham maior produção e custem menos para executar porque as histórias de execução e dados entre ações não persistem no armazenamento externo. Opcionalmente, pode ativar o histórico de execução para facilitar a depuragem. Para obter mais informações, consulte [aplicações lógicas stateful versus apátridas](#stateful-stateless).

* Executar e depurar as suas aplicações lógicas localmente no ambiente de desenvolvimento do Código do Estúdio Visual.

* Publique e implemente as suas aplicações lógicas do Código do Estúdio Visual diretamente para vários ambientes de hospedagem, tais como [O Serviço de Aplicações Azure](../app-service/environment/intro.md) e [contentores Docker.](/dotnet/core/docker/introduction)

> [!NOTE]
> Para obter informações sobre questões conhecidas atuais, reveja a [página GitHub da](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)extensão de pré-visualização .

<a name="stateful-stateless"></a>

## <a name="stateful-versus-stateless-logic-apps"></a>Aplicativos de lógica stateful versus apátrida

* *Com monitorização de estado*

  Crie aplicações lógicas estatals quando necessitar de guardar, rever ou referenciar dados de eventos anteriores. Estas aplicações lógicas mantêm a entrada e saída para cada ação e os seus estados de fluxo de trabalho em armazenamento externo, o que torna possível rever os detalhes de execução e o histórico após cada execução terminar. Aplicações lógicas imponentes fornecem alta resiliência se ou quando as interrupções acontecem. Após a restauração dos serviços e sistemas, é possível reconstruir a aplicação lógica interrompida que vai desde o estado guardado e refazer as aplicações lógicas até à conclusão. Fluxos de trabalho imponentes podem continuar a funcionar até um ano.

* *Sem estado*

  Crie aplicações lógicas apátridas quando não precisa de guardar, rever ou referenciar dados de eventos anteriores em armazenamento externo para posterior revisão. Estas aplicações lógicas mantêm a entrada e saída para cada ação e o seu fluxo de trabalho afirma apenas na memória, em vez de transferir esta informação para armazenamento externo. Como resultado, as aplicações lógicas apátridas têm percursos mais curtos que normalmente não são mais do que 5 minutos, desempenho mais rápido com tempos de resposta mais rápidos, maior produção e custos de funcionamento reduzidos porque os detalhes de execução e histórico não são mantidos em armazenamento externo. No entanto, se ou quando as interrupções ocorrerem, as execuções interrompidas não são automaticamente restauradas, pelo que o chamador precisa de reenviar as execuções interrompidas manualmente. Estas aplicações lógicas só podem ser executadas de forma sincronizada e para facilitar a depuragem, [podendo ativar o histórico de execução](#run-history)– o que tem algum impacto no desempenho.

  Atualmente, os fluxos de trabalho apátridas suportam apenas *ações* para [conectores geridos](../connectors/apis-list.md#managed-api-connectors), que são implantados em Azure, e não gatilhos. Para iniciar o seu fluxo de trabalho, selecione o [gatilho de Pedido, Evento ou Autocarro de Serviço incorporado,](../connectors/apis-list.md#built-ins)que funciona de forma nativa com o tempo de funcionaamento das Aplicações Lógicas. Para obter mais informações sobre gatilhos, ações e conectores não suportados, consulte [capacidades não suportadas ou indisponíveis](#unsupported).

Para obter informações sobre como as aplicações lógicas aninhadas se comportam de forma diferente entre aplicações lógicas stateful e [apátridas, consulte as diferenças de comportamento do Nested entre aplicações lógicas stateful e apátridas.](#nested-behavior)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modelo preços

Quando implementa o novo tipo de recurso **Logic App (Preview),** é-lhe solicitado que selecione um plano de hospedagem, especificamente o [plano de Serviço de Aplicações ou plano Premium](../azure-functions/functions-scale.md) para usar como modelo de preços. Se selecionar o plano de Serviço de Aplicações, também é solicitado a selecionar um [nível de preços](../app-service/overview-hosting-plans.md). Durante a pré-visualização pública, executar aplicações lógicas no Serviço de Aplicações não incorre em custos *adicionais* em cima do plano selecionado.

As aplicações lógicas imponentes utilizam [armazenamento externo](../azure-functions/functions-scale.md#storage-account-requirements), pelo que o modelo de preços do Azure Storage aplica-se às transações de armazenamento que as Apps Azure Logic executam. Por exemplo, as filas são usadas para agendamento, enquanto mesas e bolhas são usadas para armazenar estados de fluxo de trabalho.

Para obter mais informações sobre os modelos de preços aplicáveis a este novo tipo de recurso, reveja estes tópicos:

* [Dimensionamento e alojamento de Funções do Azure](../azure-functions/functions-scale.md)
* [Dimensione um serviço de aplicações Azure](../app-service/manage-scale-up.md)
* [Detalhes de preços de funções Azure](https://azure.microsoft.com/pricing/details/functions/)
* [Detalhes dos preços do Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/windows/)
* [Detalhes dos preços do Azure Storage](https://azure.microsoft.com/pricing/details/storage/)

<a name="unsupported"></a>

## <a name="unavailable-or-unsupported-capabilities"></a>Capacidades indisponíveis ou não apoiadas

Para esta pré-visualização pública, estas capacidades não estão disponíveis ou não suportadas:

* A criação do novo recurso **Logic App (Preview)** encontra-se atualmente indisponível no macOS.

* Nem todas as regiões de Azure são apoiadas ainda. Para as regiões atualmente disponíveis, consulte a [lista das regiões.](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)

* Para iniciar o seu fluxo de trabalho, utilize o [pedido incorporado, HTTP, Event Hubs ou Service Bus,](../connectors/apis-list.md)que funciona de forma nativa com o tempo de funcionaamento das Aplicações Lógicas. Atualmente, [os conectores de empresas](../connectors/apis-list.md#enterprise-connectors), [gatilhos de gateway de dados no local,](../connectors/apis-list.md#on-premises-connectors)gatilhos baseados na webhook, gatilho da janela deslizante, [conectores personalizados,](../connectors/apis-list.md#custom-apis-and-connectors)contas de integração, seus artefactos e [seus conectores](../connectors/apis-list.md#integration-account-connectors) não são suportados nesta pré-visualização. A capacidade de "chamar uma função Azure" não está disponível, por isso, por enquanto, utilize a *ação* HTTP para chamar o URL de pedido para a função Azure.

  Com exceção dos gatilhos previamente especificados, os fluxos de trabalho *estatais* podem utilizar tanto os gatilhos como as ações para [conectores geridos](../connectors/apis-list.md#managed-api-connectors), que são implantados em Azure versus gatilhos incorporados e ações que funcionam de forma nativa com o tempo de funcionamento das Aplicações Lógicas. No entanto, atualmente, os fluxos de trabalho *apátridas* suportam apenas *ações* para conectores geridos, e não gatilhos. Embora possa ativar os conectores em Azure para o seu fluxo de trabalho apátrida, o designer não mostra nenhum gatilho de conector gerido para que possa selecionar.

* Pode implementar o novo tipo de recurso **Logic App (Preview)** apenas para um [plano de hospedagem Premium ou App Service em Azure](#publish-azure) ou para um [recipiente Docker](#deploy-docker), e não [ambientes de serviço de integração (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). **Os** planos de hospedagem de consumo não são suportados nem disponíveis para a implementação deste tipo de recursos.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="access-and-connectivity"></a>Acesso e conectividade

* Aceda à internet para que possa descarregar os requisitos, ligar do Código do Estúdio Visual à sua conta Azure e publicar de Visual Studio Code para Azure, um recipiente Docker ou outro ambiente.

* Uma conta e subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Para construir a mesma aplicação lógica de exemplo neste artigo, precisa de uma conta de e-mail do Office 365 Outlook que utilize uma conta de trabalho ou escola da Microsoft para iniciar scontabilidade.

  Se optar por utilizar um [conector de e-mail diferente que seja suportado por Azure Logic Apps](/connectors/)– como Outlook.com ou [Gmail](../connectors/connectors-google-data-security-privacy-policy.md)– ainda pode seguir o exemplo, e os passos gerais gerais são os mesmos, mas a interface e as opções do utilizador podem diferir de alguma forma. Por exemplo, se utilizar o conector Outlook.com, use a sua conta pessoal da Microsoft para iniciar scontabilidade.

### <a name="storage-requirements"></a>Requisitos de armazenamento

1. Descarregue e instale [o Emulador de Armazenamento Azure 5.10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Para executar o emulador, é necessário ter uma instalação DB SQL local, como o [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658)gratuito. Para obter mais informações, consulte [o emulador de armazenamento Azure para desenvolvimento e teste.](../storage/common/storage-use-emulator.md)

   > [!IMPORTANT]
   > Antes de abrir o Logic App Designer para criar o seu fluxo de trabalho de aplicações lógicas, certifique-se de que inicia o emulador. Caso contrário, recebe-se uma mensagem de `Workflow design time could not be started` que.
   >
   > ![Screenshot que mostra o Emulador de Armazenamento Azure a funcionar.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

### <a name="tools"></a>Ferramentas

* [Visual Studio Code 1.30.1 (janeiro 2019) ou superior](https://code.visualstudio.com/), que é gratuito. Além disso, descarregue e instale estas ferramentas adicionais para Visual Studio Code, se ainda não as tiver:

  * [Extensão da Conta Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), que fornece uma única experiência comum de visualização e filtragem de subscrição de Azure para todas as outras extensões Azure no Código do Estúdio Visual.

  * [C# para a extensão Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), que permite que a funcionalidade F5 execute a sua aplicação lógica.

  * [Azure Functions Core Tools](../azure-functions/functions-run-local.md), versão [3.0.2931](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) ou [2.7.2936](https://github.com/Azure/azure-functions-core-tools/releases/tag/2.7.2936), através do Instalador microsoft (MSI). Estas ferramentas incluem uma versão do mesmo tempo de execução que alimenta o tempo de execução das Funções Azure que funciona no Código do Estúdio Visual.

    > [!IMPORTANT]
    > Se tiver uma instalação mais cedo do que estas versões, desinstale essa versão primeiro, ou certifique-se de que a variável do ambiente PATH aponta para a versão que descarrega e instala.
    >
    > Se pretender utilizar a ação [ **Código Inline**](../logic-apps/logic-apps-add-run-inline-code.md) para executar o código JavaScript, tem de utilizar a versão de tempo de execução 3x das Funções Azure, porque a ação não suporta a versão 2x. Além disso, esta ação não é suportada nos sistemas operativos Linux.

  * [Extensão Azure Logic Apps (Preview) para Código de Estúdio Visual](https://go.microsoft.com/fwlink/p/?linkid=2143167). Esta extensão de pré-visualização pública fornece a capacidade para criar aplicações lógicas apátridas e apátridas e executá-las localmente no Código do Estúdio Visual.

    Atualmente, pode ter a extensão original das **Apps Azure Logic** e a nova extensão **Azure Logic Apps (Preview)** instalada ao mesmo tempo no Código do Estúdio Visual. Ao selecionar o ícone Azure na barra de ferramentas Visual Studio Code, pode ver todas as aplicações lógicas implementadas no Azure, mas cada tipo de recurso aparece nas suas próprias secções de extensão, **Apps Lógicas** e **Apps Lógicas Azure (Preview)**.

    > [!IMPORTANT]
    > Se criou aplicações lógicas utilizando a extensão **Azure Logic Apps (Private Preview),** estas aplicações lógicas não funcionarão com a extensão de pré-visualização pública. No entanto, é possível migrar estas aplicações lógicas desinstalando a extensão de pré-visualização privada, realizando a limpeza necessária e instalando a extensão de pré-visualização pública. Pode então criar o seu novo projeto no Visual Studio Code e copiar o ficheiro **workflow.definition** da sua aplicação lógica anteriormente criado no seu novo projeto.
    >
    > Assim, antes de instalar a extensão de pré-visualização pública, certifique-se de desinstalar quaisquer versões anteriores e eliminar estes artefactos:
    >
    > * A pasta **Microsoft.Azure.Functions.ExtensionBundle.Workflows,** que contém pacotes de extensão anteriores e está localizada ao longo de ambos os caminhos aqui:
    >
    >   * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`
    >
    >   * `C:\Users\{userName}.azure-functions-core-tools\Functions\ExtensionBundles`
    >
    > * A pasta **microsoft.azure.workflows.webjobs.extension,** que é a cache [NuGet](/nuget/what-is-nuget) para a extensão de pré-visualização privada e está localizada ao longo deste caminho:
    >
    >   `C:\Users\{userName}\.nuget\packages`

    Para instalar a extensão **Azure Logic Apps (Preview),** siga estes passos:

    1. No Código do Estúdio Visual, na barra de ferramentas esquerda, selecione **Extensões**.

    1. Na caixa de pesquisa de extensões, insira `azure logic apps preview` . Na lista de resultados, selecione **Azure Logic Apps (Preview)** **>** **Install**.

       Após a conclusão da instalação, a extensão de pré-visualização pública aparece na lista **de extensões: Lista instalada.**

       ![Screenshot que mostra a lista de extensões instaladas do Visual Studio Code com a extensão "Azure Logic Apps (Preview)" sublinhada.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* Para testar a aplicação lógica de exemplo que cria neste artigo, precisa de uma ferramenta que possa enviar chamadas para o gatilho 'Pedido', que é o primeiro passo na aplicação lógica de exemplo. Se não tiver tal ferramenta, pode descarregar, instalar e utilizar [o Carteiro](https://www.postman.com/downloads/).

* Para uma capacidade de registo e rastreio de diagnósticos mais fácil, pode adicionar e utilizar um recurso [Application Insights.](../azure-monitor/app/app-insights-overview.md) Pode criar este recurso durante a implementação de aplicações lógicas ou no portal Azure depois de implementar a sua aplicação lógica.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Configurar o Visual Studio Code

1. Para se certificar de que todas as extensões estão corretamente instaladas, recarregue ou reinicie o Código do Estúdio Visual.

1. Ative ou verifique se o Código do Estúdio Visual encontra e instala automaticamente atualizações de extensão para que a extensão de pré-visualização pública receba as últimas atualizações.

   Para verificar esta definição, siga estes passos:

   1. No menu **'Ficheiro',** aceda às **Definições de** **>** **Preferências.**

   1. No separador **Utilizador,** aceda às **Extensões de** **>** **Funcionalidades.**

   1. Confirme que estão **selecionadas atualizações** de verificação automática e **atualização automática.**

1. Ativar ou verificar se estas definições de extensão **Azure Logic Apps (Preview)** são configuradas no Código do Estúdio Visual:

   * **Azure Logic Apps V2: Modo painel**
   * **Azure Logic Apps V2: Project Runtime**

   1. No menu **'Ficheiro',** aceda às **Definições de** **>** **Preferências.**

   1. No **separador Utilizador,** aceda a **>** **Extensões** **>** **Azure Logic Apps (Preview)**.

   1. No **âmbito das Azure Logic Apps V2: Modo painel**, confirme que o modo de painel **Enable** está selecionado. Em **Azure Logic Apps V2: Project Runtime**, defina a versão para **~3** ou **~2**, com base na [versão Azure Functions Core Tools](#prerequisites) que instalou anteriormente.

      > [!IMPORTANT]
      > Se pretender utilizar a ação [ **Código Inline**](../logic-apps/logic-apps-add-run-inline-code.md) para executar o código JavaScript, certifique-se de que utiliza a versão 3 do Project Runtime porque a ação não suporta a versão 2. Além disso, esta ação não é suportada nos sistemas operativos Linux.

      ![Screenshot que mostra definições de Código do Estúdio Visual para extensão "Azure Logic Apps (Preview)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

1. Na barra de ferramentas Visual Studio Code, selecione o ícone Azure.

   ![Screenshot que mostra barra de ferramentas Visual Studio Code e ícone Azure selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. No painel Azure, em **Azure: Logic Apps (Preview)**, selecione **Iniciar sedura no Azure**. Quando aparecer a página de autenticação do Código do Estúdio Visual, inscreva-se na sua conta Azure.

   ![Screenshot que mostra painel Azure e link selecionado para o sinal de Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Depois de iniciar sação, o painel Azure mostra as subscrições na sua conta Azure. Se tiver a extensão de Aplicações Lógicas publicamente lançada, pode encontrar quaisquer recursos originais de Aplicações Lógicas que criou usando a extensão original na secção **de Aplicações Lógicas** da extensão lançada, e não na secção **de Aplicações Lógicas (Preview)** da extensão de pré-visualização.
   
   Se as subscrições esperadas não aparecerem, ou se pretender que o painel apresente apenas subscrições específicas, siga estes passos:

   1. Na lista de subscrições, mova o ponteiro ao lado da primeira subscrição até que apareça o botão **Despassar as subscrições** (ícone do filtro). Selecione o ícone do filtro.

      ![Screenshot que mostra painel Azure e ícone de filtro selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Ou, na barra de estado do Código do Estúdio Visual, selecione a sua conta Azure. 

   1. Quando aparecer outra lista de subscrições, selecione as subscrições que pretende e, em seguida, certifique-se de que seleciona **OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Criar um projeto local

Antes de poder criar a sua aplicação lógica, crie um projeto local para que possa gerir e implementar a sua aplicação lógica a partir do Código do Estúdio Visual. O projeto subjacente é semelhante a um projeto Azure Functions, também conhecido como um projeto de aplicação de função. No entanto, estes tipos de projetos são separados uns dos outros, por isso fluxos de trabalho e funções de aplicações lógicas não podem existir no mesmo projeto.

1. No seu computador, crie uma pasta local *vazia* para usar para o projeto que mais tarde irá criar no Código do Estúdio Visual.

1. No Código do Estúdio Visual, feche todas as pastas abertas.

1. No painel Azure, ao lado de **Azure: Logic Apps (Preview)**, selecione **Create New Project** (ícone que mostra uma pasta e um relâmpago).

   ![Screenshot que mostra a barra de ferramentas do painel Azure com "Create New Project" selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Se o Windows Defender Firewall lhe pedir para conceder acesso à rede para `Code.exe` , que é Código do Estúdio Visual, e para , que é o `func.exe` Azure Functions Core Tools, selecione **redes privadas, como a minha casa ou rede de trabalho** Permitir o **>** **acesso**.

1. Navegue para o local onde criou a pasta do projeto, selecione essa pasta e continue.

   ![Screenshot que mostra a caixa de diálogo "Select Folder" com uma pasta de projeto recém-criada e o botão "Selecione" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. A partir da lista de modelos que aparecem, selecione **o Fluxo de Trabalho Stateful** ou o Fluxo de Trabalho **Apátrida**. Este exemplo seleciona **o Fluxo de Trabalho Stateful**.

   ![Screenshot que mostra a lista de modelos de fluxo de trabalho com "Stateful Workflow" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Forneça um nome para o fluxo de trabalho da sua aplicação lógica e prima Enter. Este exemplo usa `example-workflow` como nome.

   ![Screenshot que mostra a caixa "Criar um novo fluxo de trabalho stateful (3/4)" e "exemplo-fluxo de trabalho" como o nome do fluxo de trabalho.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

1. A partir da lista seguinte que aparece, selecione **Abrir na janela atual**.

   ![Screenshot que mostra lista com "Abrir na janela atual" selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-location.png)

   Visual Studio Code recarrega, abre o painel Explorer e mostra o seu projeto, que agora inclui ficheiros de projeto gerados automaticamente. Por exemplo, o projeto tem uma pasta que mostra o nome do fluxo de trabalho da sua aplicação lógica. Dentro desta pasta, o **workflow.jsem** ficheiro contém a definição de JSON subjacente ao fluxo de aplicações lógicas.

   ![Screenshot que mostra a janela do Explorer com pasta de projeto, pasta de fluxo de trabalho e ficheiro "workflow.jsligado".](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-logic-app-designer"></a>Abra o ficheiro de definição de fluxo de trabalho no Logic App Designer

1. Verifique as versões instaladas no seu computador executando este comando:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Se tiver .NET Core SDK 5.x, esta versão poderá impedir-te de abrir a definição de fluxo de trabalho subjacente da aplicação lógica no designer. Em vez de desinstalar esta versão, na localização raiz do seu projeto, crie uma **global.jsno** ficheiro que faz referência à versão .NET Core runtime 3.x que tem que é mais tarde do que 3.1.201, por exemplo:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   Certifique-se de que adiciona explicitamente esse **global.jsno** ficheiro ao seu projeto na localização raiz a partir do Código do Estúdio Visual. Caso contrário, o designer não abre.

1. Se o Código do Estúdio Visual estiver a funcionar no Windows ou linux, certifique-se de que o Emulador de Armazenamento Azure está em funcionamento. Para mais informações, reveja os [Pré-Requisitos.](#prerequisites)

1. Expanda a pasta do projeto para o seu fluxo de trabalho. Abra a **workflow.jsno** menu de atalho do ficheiro e selecione Abrir **no Designer**.

   ![Screenshot que mostra painel explorer e janela de atalho para o workflow.jsem arquivo com "Open in Designer" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   Se receber a mensagem de erro de que o `Workflow design time could not be started` azure Emulator está em funcionamento. Caso contrário, experimente estas outras sugestões de resolução de problemas:

   No Código do Estúdio Visual, verifique a saída da extensão de pré-visualização.

   1. No menu **Ver,** selecione **Output**.

   1. A partir da lista na barra de título **de saída,** selecione **Azure Logic Apps** para que possa ver a saída para a extensão de pré-visualização, por exemplo:

      ![Screenshot que mostra a janela de saída do Visual Studio Code com "Azure Logic Apps" selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

   1. Reveja a saída e verifique se esta mensagem de erro aparece:

      ```text
      A host error has occurred during startup operation '{operationID}'.
      System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
      ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.1\bin\
      DurableTask.AzureStorage.dll' already exists.
      Value cannot be null. (Parameter 'provider')
      Application is shutting down...
      Initialization cancellation requested by runtime.
      Stopping host...
      Host shutdown completed.
      ```

      Este erro pode ocorrer se já tentou abrir o designer e, em seguida, descontinuar ou apagar o seu projeto. Para resolver este erro, elimine a pasta **ExtensionBundles** neste **local...\Utilizadores \\ {seu nome de utilizador}\AppData\Local\Temp\Funções\ExtensionBundles**, e reprete a abertura do **workflow.jsno** ficheiro no designer.

1. A partir dos **conectores Ativar na lista Azure,** selecione **Utilize conectores da Azure,** que se aplica a todos os conectores geridos que estão disponíveis e implantados no Azure, e não apenas conectores para serviços Azure.

   ![Screenshot que mostra o painel explorer com lista "Enable connectors in Azure" aberto e "Use conectores do Azure" selecionados.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Atualmente, os fluxos de trabalho apátridas suportam apenas *ações* para [conectores geridos](../connectors/apis-list.md#managed-api-connectors), que são implantados em Azure, e não gatilhos. Embora tenha a opção de ativar conectores em Azure para o seu fluxo de trabalho apátrida, o designer não mostra nenhum gatilho de conector gerido para que possa selecionar.

1. A partir da lista de grupos de recursos, **selecione Criar novo grupo de recursos**.

   ![Screenshot que mostra painel do Explorer com lista de grupos de recursos e "Criar novo grupo de recursos" selecionado](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Forneça um nome para o grupo de recursos e prima Enter. Este exemplo `example-logic-app-preview-rg` utiliza.

   ![Screenshot que mostra painel explorer e caixa de nome de grupo de recursos.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. A partir da lista de locais, encontre e selecione uma [região Azure suportada](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions) para usar para criar o seu grupo de recursos e recursos. Este exemplo utiliza **a West Central US.**

   > [!IMPORTANT]
   > Nem todas as regiões são atualmente apoiadas, mas estão em curso atualizações para acrescentar mais regiões. A seleção de uma região não apoiada pode resultar em problemas, como a criação de ligações. Para as regiões atualmente apoiadas, reveja a [página GitHub da](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)extensão de pré-visualização .

   ![Screenshot que mostra o painel explorer com lista de localizações e "West Central US" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Depois de realizar este passo, o Visual Studio Code abre o Logic App Designer.

   > [!NOTE]
   > Quando o Código do Estúdio Visual inicia a API de tempo de fluxo de trabalho, aparece uma mensagem de que o arranque pode demorar alguns segundos. Pode ignorar esta mensagem ou selecionar **OK**.

   Depois de aparecer o Designer de Aplicações Lógicas, o pedido **de operação Escolha** aparece no designer e é selecionado por padrão, o que mostra o **painel de ação Add.**

   ![Screenshot que mostra Logic App Designer.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Em seguida, [adicione um gatilho e ações](#add-trigger-actions) ao seu fluxo de trabalho.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Adicione um gatilho e ações

Depois de abrir o Design de Aplicações Lógicas do seu **workflow.jsno** menu de atalho do ficheiro, o pedido **de operação Escolha** aparece no designer e é selecionado por padrão. Pode agora começar a criar o seu fluxo de trabalho adicionando um gatilho e ações.

O fluxo de trabalho de aplicações lógicas neste exemplo utiliza este gatilho e estas ações:

* O gatilho [de pedido](../connectors/connectors-native-reqres.md)incorporado , **Quando é recebido um pedido HTTP**, que recebe chamadas ou pedidos de entrada e cria um ponto final que outros serviços ou aplicações lógicas podem ligar.

* A [ação Do Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), Enviar um **e-mail**.

* A ação [Resposta](../connectors/connectors-native-reqres.md)incorporada, que utiliza para enviar uma resposta e devolver dados ao chamador.

### <a name="add-the-request-trigger"></a>Adicione o gatilho do pedido

1. Ao lado do designer, no painel **de gatilho Adicionar um painel de gatilho,** sob a caixa de pesquisa de **operação,** **certifique-se de** que o Incorporado é selecionado para que possa selecionar um gatilho que é executado de forma nativa.

1. Na caixa de pesquisa de **operação,** insira `when a http request` e selecione o gatilho de pedido incorporado que está nomeado **Quando um pedido HTTP é recebido**.

   ![Screenshot que mostra o Design de Aplicações Lógica e **Adicione um painel de gatilho** com o gatilho "Quando um pedido HTTP é recebido" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Quando o gatilho aparece no designer, o painel de detalhes do gatilho abre-se para mostrar as propriedades, configurações e outras ações do gatilho.

   ![Screenshot que mostra o Logic App Designer com o gatilho "Quando um pedido HTTP é recebido" selecionado e detona detalhes abertos.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Se o painel de detalhes não aparecer, certifique-se de que o gatilho é selecionado no designer.

1. Se tiver de apagar um item no designer, siga estes passos:

   1. No designer, selecione o item, que abre o painel de detalhes do item para o lado direito.

   1. Expanda a janela visual Studio Code suficientemente para que, ao lado do gatilho ou nome de ação, o botão elipses **(...**) apareça no canto superior direito. 

   1. Abra o menu de elipses **(...**) e selecione **Delete**. Para confirmar a eliminação, selecione **OK**.

      ![Screenshot que mostra item selecionado no designer com painel de detalhes abertos e com botão de elipses selecionado e opção "Excluir".](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

### <a name="add-the-office-365-outlook-action"></a>Adicione a ação do Office 365 Outlook

1. No designer, sob o gatilho que adicionou, selecione **Novo passo**.

   O pedido **de operação Escolha** aparece no designer e o **painel de ação Add abre** para que possa selecionar a próxima ação.

1. No painel de ação Adicionar um painel de **ação,** sob a caixa de pesquisa de **operação,** selecione **Azure** para que possa encontrar e selecionar uma ação para um conector gerido que esteja implantado no Azure.

   Este exemplo seleciona e utiliza a ação Do Office 365 Outlook, **Envie um e-mail (V2)**.

   ![Screenshot que mostra o Logic App Designer e **Adicione um painel de ação** com a ação do Office 365 Outlook "Envie um e-mail" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. No painel de detalhes da ação, selecione **Iniciar sessão** para que possa criar uma ligação à sua conta de e-mail.

   ![Screenshot que mostra Logic App Designer e **Enviar um painel de e-mail (V2)** com "Iniciar sê-lo" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

   > [!NOTE]
   > Se tiver o erro, `Failed to create connection...` pode ter escolhido uma região não suportada para a sua aplicação lógica. Estão em curso atualizações para acrescentar mais regiões. Entretanto, para as regiões atualmente apoiadas, reveja a [página GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)da extensão de pré-visualização .

1. Quando o Código do Estúdio Visual lhe pedir o consentimento para aceder à sua conta de e-mail, selecione **Open**.

   ![Screenshot que mostra o pedido do Código do Estúdio Visual para permitir o acesso.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Para evitar futuras solicitações, selecione **Configure Domínios Fidedignos** para que possa adicionar a página de autenticação como um domínio fidedigno.

1. Siga as instruções subsequentes para iniciar singing, permitir o acesso e permitir o regresso ao Código do Estúdio Visual.

   > [!NOTE]
   > Se passar demasiado tempo antes de completar as indicações, o processo de autenticação acaba e falha. Neste caso, volte ao designer e volte a inscrever-se para criar a ligação.

1. Quando a extensão de pré-visualização do Azure Logic Apps lhe pedir o consentimento para aceder à sua conta de e-mail, selecione **Open**. Siga o pedido subsequente para permitir o acesso.

   ![Screenshot que mostra o pedido de extensão de pré-visualização para permitir o acesso.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Para evitar futuras solicitações, selecione **Não peça novamente esta extensão**.

   Depois de o Código Do Estúdio Visual criar a sua ligação, alguns conectores mostram a mensagem de que `The connection will be valid for {n} days only.` este prazo se aplica apenas à duração enquanto autoriza a sua aplicação lógica no Código do Estúdio Visual. Após a implementação, este limite já não se aplica porque a sua aplicação lógica pode autenticar-se no tempo de execução utilizando a sua [identidade gerida automaticamente atribuída ao sistema.](../logic-apps/create-managed-service-identity.md) Esta identidade gerida difere das credenciais de autenticação ou da cadeia de ligação que utiliza quando cria uma ligação. Se desativar esta identidade gerida atribuída pelo sistema, as ligações não funcionarão em tempo de execução.

1. No designer, se o Enviar uma ação **de e-mail** não aparecer selecionado, selecione essa ação.

1. No painel de pormenores da ação, no separador **Parâmetros,** forneça as informações necessárias para a ação, por exemplo:

   ![Screenshot que mostra o Logic App Designer com detalhes para a ação do Office 365 Outlook "Enviar um e-mail".](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Para** | Yes | <*seu endereço de e-mail*> | O destinatário do e-mail, que pode ser o seu endereço de e-mail para efeitos de teste. Este exemplo utiliza o e-mail fictício, `sophiaowen@fabrikam.com` . |
   | **Assunto** | Yes | `An email from your example workflow` | O assunto do e-mail |
   | **Corpo** | Yes | `Hello from your example workflow!` | O conteúdo do corpo de e-mail |
   ||||

   > [!NOTE]
   > Se pretender escoar quaisquer alterações no painel de detalhes no painel **de definições**, **executar depois** ou no separador **Resultado Estático,** certifique-se de que seleciona **Feito** para escoar essas alterações antes de mudar de separador ou alterar o foco para o designer. Caso contrário, o Código do Estúdio Visual não manterá as alterações. Para mais informações, reveja a [página GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)da extensão de pré-visualização .

1. No designer, **selecione Save**.

Em seguida, corra e depure o seu fluxo de trabalho localmente no Código do Estúdio Visual.

<a name="debug-test-locally"></a>

## <a name="run-and-debug-locally"></a>Executar e depurar localmente

Para testar a sua aplicação lógica, siga estes passos para iniciar uma sessão de depuração e encontre o URL para o ponto final criado pelo gatilho Request. Você precisa deste URL para que você possa mais tarde enviar um pedido para esse ponto final.

1. Para ajudá-lo mais facilmente a depurar um fluxo de trabalho de aplicações lógicas apátridas, pode [ativar o histórico de execução para esse fluxo de trabalho](#run-history).

1. Na barra de ferramentas Visual Studio Code, abra o menu **'Executar'** e selecione **Start Debugging** (F5).

   A janela **do Terminal** abre para que possa rever a sessão de depuragem.

1. Agora, encontre o URL de retorno para o ponto final no gatilho do pedido.

   1. Reabra o painel Explorer para que possa ver o seu projeto.

   1. A partir do **workflow.jsno** menu de atalho do ficheiro, selecione **Overview**.

      ![Screenshot que mostra o painel explorer e a janela de atalho para o workflow.jsem ficheiro com "Visão Geral" selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Encontre o valor **URL callback,** que se parece com este URL para o exemplo Detonador de pedido:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Screenshot que mostra a página geral do seu fluxo de trabalho com URL de retorno](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Para testar o URL de retorno, desencadeando o fluxo de trabalho da aplicação lógica, abra o [Carteiro](https://www.postman.com/downloads/) ou a sua ferramenta preferida para criar e enviar pedidos.

   Este exemplo continua usando o Carteiro. Para mais informações, consulte [o Carteiro A Começar.](https://learning.postman.com/docs/getting-started/introduction/)

   1. Na barra de ferramentas do Carteiro, selecione **New**.

      ![Screenshot que mostra Carteiro com novo botão selecionado](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. No painel **Create New,** em **Blocos de Construção,** selecione **Request**.

   1. Na janela **Pedido de Salvamento,** no **nome Pedido,** forneça um nome para o pedido, por exemplo, `Test workflow trigger` .

   1. Em **Seleção de uma coleção ou pasta para guardar para**, selecione Create **Collection**.

   1. Em **Todas as Coleções,** forneça um nome para a coleção para a organização dos seus pedidos, prima Enter e selecione **Save to <*nome* > de coleção**. Este exemplo usa `Logic Apps requests` como nome de coleção.

      O painel de pedido do carteiro abre para que possa enviar um pedido para o URL de retorno para o gatilho do pedido.

      ![Screenshot que mostra carteiro com o painel de pedido aberto](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Regresso ao Código do Estúdio Visual. a partir da página geral do fluxo de trabalho, copie o valor da propriedade **DE URL callback.**

   1. Volte para o Carteiro. No painel de pedidos, em seguida, a lista de métodos, que atualmente mostra **GET** como o método de pedido predefinido, cole o URL de retorno que copiou anteriormente na caixa de endereços, e selecione **Enviar**.

      ![Screenshot que mostra Carteiro e URL de retorno na caixa de endereço com botão enviar selecionado](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      O fluxo de trabalho de aplicações lógicas exemplo envia um e-mail que aparece semelhante a este exemplo:

      ![Screenshot que mostra o email do Outlook como descrito no exemplo](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. No Código do Estúdio Visual, volte à página geral do seu fluxo de trabalho.

   Se criou um fluxo de trabalho imponente, após o pedido que enviou acionar o fluxo de trabalho, a página geral mostra o estado de funcionamento e o histórico do fluxo de trabalho.

   > [!TIP]
   > Se o estado de execução não aparecer, tente refrescar a página geral selecionando **Refresh**. Nenhuma corrida acontece para um gatilho que é ignorado devido a critérios não atendidos ou não encontrar dados.

   ![Screenshot que mostra a página geral do fluxo de trabalho com estado de execução e histórico](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Estado de execução | Descrição |
   |------------|-------------|
   | **Abortada** | A execução parou ou não terminou devido a problemas externos, por exemplo, uma falha do sistema ou subscrição do Azure caducado. |
   | **Cancelado** | A corrida foi desencadeada e iniciada, mas recebeu um pedido de cancelamento. |
   | **Com falhas** | Pelo menos uma ação na corrida falhou. Não foram criadas ações subsequentes no fluxo de trabalho para lidar com a falha. |
   | **Em Execução** | A corrida foi desencadeada e está em curso, mas este estatuto também pode aparecer para uma corrida que é acelerada devido a [limites](logic-apps-limits-and-config.md) de ação ou ao [atual plano de preços.](https://azure.microsoft.com/pricing/details/logic-apps/) <p><p>**Dica**: Se configurar [o registo de diagnósticos,](monitor-logic-apps-log-analytics.md)pode obter informações sobre quaisquer eventos de aceleração que ocorram. |
   | **Com êxito** | A corrida conseguiu. Se alguma ação falhou, uma ação subsequente no fluxo de trabalho tratou dessa falha. |
   | **Esgotado** | O tempo de execução foi esgotado porque a duração atual excedeu o limite de duração de execução, que é controlado pela [ **retenção**](logic-apps-limits-and-config.md#run-duration-retention-limits)do histórico run em dias de fixação . A duração de uma corrida é calculada utilizando o tempo de início da execução e o limite de duração da execução nessa hora de início. <p><p>**Nota:** Se a duração da execução também exceder o limite atual de retenção do histórico de *execução*, que também é controlado pela [ **retenção**](logic-apps-limits-and-config.md#run-duration-retention-limits)do histórico run em dias de fixação , a execução é limpa do histórico de execuções por um trabalho de limpeza diário. Quer os tempos de funcionação se esgotam ou completem, o período de retenção é sempre calculado utilizando o tempo de início da execução e o limite de retenção *atual.* Então, se reduzir o limite de duração para uma corrida de voo, o tempo de execução se esgota. No entanto, a execução permanece ou é retirada do histórico de corridas com base no facto de a duração da corrida ter excedido o limite de retenção. |
   | **Em espera** | A execução não começou ou está interrompida, por exemplo, devido a uma instância de fluxo de trabalho anterior que ainda está em funcionamento. |
   |||

1. Para rever os estatutos de cada passo numa corrida específica e as entradas e saídas do passo, selecione o botão elipses **(...**) para essa execução e selecione **Show Run**.

   ![Screenshot que mostra a linha de histórico de execução do seu fluxo de trabalho com botão de elipses e "Show Run" selecionado](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   O Código do Estúdio Visual abre a vista de monitorização e mostra o estado de cada passo na corrida.

   ![Screenshot que mostra cada passo na execução do fluxo de trabalho e o seu estado](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Se uma execução falhou e um passo na visão de monitorização mostra o `400 Bad Request` erro, este problema pode resultar de um nome de gatilho mais longo ou nome de ação que faz com que o identificador de recursos uniformes subjacente (URI) exceda o limite de caracteres padrão. Para mais informações, consulte ["400 Mau Pedido".](#400-bad-request)

   Aqui estão os possíveis estatutos que cada passo no fluxo de trabalho pode ter:

   | Estado de ação | Ícone | Descrição |
   |---------------|------|-------------|
   | Abortada | ![Ícone para estado de ação "abortado"][aborted-icon] | A ação parou ou não terminou devido a problemas externos, por exemplo, uma falha no sistema ou subscrição do Azure caducado. |
   | Cancelada | ![Ícone para estado de ação "cancelado"][cancelled-icon] | A ação estava em andamento, mas recebeu um pedido de cancelamento. |
   | Com falhas | ![Ícone para estado de ação "falhado"][failed-icon] | A ação falhou. |
   | Em Execução | ![Ícone para o estado de ação "Running"][running-icon] | A ação está em curso. |
   | Ignorado | ![Ícone para estado de ação "ignorado"][skipped-icon] | A ação foi ignorada porque a ação imediatamente anterior falhou. Uma ação tem uma `runAfter` condição que exige que a ação anterior termine com sucesso antes que a ação atual possa ser executada. |
   | Com êxito | ![Ícone para o estado de ação "Bem sucedido"][succeeded-icon] | A ação foi bem sucedida. |
   | Conseguiu com recauchutagens | ![Ícone para estatuto de ação "Bem sucedido com recauchutagens"][succeeded-with-retries-icon] | A ação foi bem sucedida, mas só depois de uma ou mais retrólmas. Para rever o histórico de relemisso, na visão de detalhes do histórico de execução, selecione essa ação para que possa ver as entradas e saídas. |
   | Esgotado | ![Ícone para estado de ação "Timed out"][timed-out-icon] | A ação parou devido ao limite de tempo especificado pelas definições dessa ação. |
   | Em espera | ![Ícone para estado de ação "espera"][waiting-icon] | Aplica-se a uma ação webhook que está à espera de um pedido de entrada de um chamador. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. Para rever as entradas e saídas de cada passo, selecione o passo que pretende inspecionar.

   ![Screenshot que mostra o estado de cada passo no fluxo de trabalho mais as entradas e saídas na ação expandida "Enviar um e-mail"](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Para rever ainda mais as entradas e saídas brutas para esse passo, selecione **Mostrar entradas brutas** ou **mostrar saídas brutas**.

1. Para parar a sessão de depurar, no menu **Executar,** selecione **Stop Debugging** (Shift + F5).

<a name="return-response"></a>

## <a name="return-a-response-to-the-caller"></a>Devolva uma resposta ao chamador

Para devolver uma resposta ao chamador que enviou um pedido para a sua aplicação lógica, pode utilizar a [ação de Resposta](../connectors/connectors-native-reqres.md) incorporada para um fluxo de trabalho que começa com o gatilho Do Pedido.

1. No Logic App Designer, no âmbito do Enviar uma ação **de e-mail,** selecione **Novo passo**.

   O pedido **de operação Escolha** aparece no designer e o **painel de ação Add abre** para que possa selecionar a próxima ação.

1. No painel de ação Adicionar um painel de **ação,** sob a caixa de pesquisa de **ação,** **certifique-se de** que o Incorporado está selecionado. Na caixa de pesquisa, insira `response` e selecione a ação **Resposta.**

   ![Screenshot que mostra o Logic App Designer com a ação Response selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Quando a ação **Response** aparece no designer, o painel de detalhes da ação abre-se automaticamente.

   ![Screenshot que mostra o Logic App Designer com o painel de detalhes da ação "Resposta" aberto e a propriedade "Body" definida para o valor da propriedade "Enviar um e-mail" da ação "Body".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. No **separador Parâmetros,** forneça as informações necessárias para a função que pretende ligar.

   Este exemplo devolve o valor da propriedade **Body** que é a saída do Enviar uma ação **de e-mail.**

   1. Clique dentro da caixa de propriedade **Body** para que a lista de conteúdos dinâmicos apareça e mostre os valores de saída disponíveis do gatilho anterior e as ações no fluxo de trabalho.

      ![Screenshot que mostra o painel de detalhes da ação "Resposta" com o ponteiro do rato dentro da propriedade "Body" para que a lista de conteúdos dinâmicos apareça.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Na lista de conteúdos dinâmicos, em **Enviar um e-mail,** selecione **Body**.

      ![Screenshot que mostra a lista de conteúdos dinâmicos abertos. Na lista, no cabeçalho "Enviar um e-mail", é selecionado o valor de saída "Corpo".](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Quando terminar, a propriedade Do **Corpo** da Ação resposta está agora definida para o Valor de Saída do **Corpo** de uma ação **de e-mail.**

      ![Screenshot que mostra o estado de cada passo no fluxo de trabalho mais as entradas e saídas na ação expandida "Resposta".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. No designer, **selecione Save**.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Reteste o seu aplicativo de lógica

Depois de fazer atualizações para a sua aplicação lógica, pode realizar outro teste repetindo o depurador no Visual Studio e enviando outro pedido para ativar a sua app lógica atualizada, semelhante aos passos em [Debug e testar a sua aplicação lógica.](#debug-test-locally)

1. Na barra de ferramentas Visual Studio Code, abra o menu **'Executar'** e selecione **Start Debugging** (F5).

1. No Carteiro ou na sua ferramenta para criar e enviar pedidos, envie outro pedido para desencadear o seu fluxo de trabalho.

1. Se criou um fluxo de trabalho imponente, na página geral do fluxo de trabalho, verifique o estado da execução mais recente. Para visualizar o estado, entradas e saídas para cada passo nessa corrida, selecione o botão elipses **(...**) para essa execução e selecione **Show Run**.

   Por exemplo, aqui está o estado passo a passo para uma corrida após o fluxo de trabalho da amostra ter sido atualizado com a ação resposta.

   ![Screenshot que mostra o estado de cada passo no fluxo de trabalho atualizado mais as entradas e saídas na ação expandida "Resposta".](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Para parar a sessão de depurar, no menu **Executar,** selecione **Stop Debugging** (Shift + F5).

<a name="publish-azure"></a>

## <a name="publish-to-azure"></a>Publicar no Azure

A partir do Código do Estúdio Visual, pode implementar o seu projeto diretamente para o Azure, que publica a sua aplicação lógica utilizando o novo tipo de recurso **Logic App (Preview).** Semelhante ao recurso de aplicação de função em Azure Functions, a implementação para este novo tipo de recurso requer que selecione um [plano de hospedagem e nível de preços](../app-service/overview-hosting-plans.md), que pode configurar durante a implementação. Para obter mais informações sobre planos de hospedagem e preços, reveja estes tópicos:

* [Dimensione um serviço de aplicações Azure](../app-service/manage-scale-up.md)
* [Dimensionamento e alojamento de Funções do Azure](../azure-functions/functions-scale.md)

Pode publicar a sua aplicação lógica como um novo recurso, que cria automaticamente quaisquer recursos necessários adicionais, como uma [conta de Armazenamento Azure, semelhante aos requisitos de aplicações de função.](../azure-functions/storage-considerations.md) Ou, pode publicar a sua aplicação lógica para um recurso de **Aplicação Lógica (Preview)** previamente implantado, que substitui essa aplicação lógica.

### <a name="publish-as-a-new-logic-app-preview-resource"></a>Publique como um novo recurso Logic App (Preview)

1. Na barra de ferramentas Visual Studio Code, selecione o ícone Azure.

1. No Azure: Barra de ferramentas **de painéis de aplicações lógicas (preview),** selecione **Implementar para a Aplicação Lógica**.

   ![Screenshot que mostra o painel "Azure: Logic Apps (Preview)" e a barra de ferramentas do painel com "Implementar para a Aplicação Lógica" selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Na lista que o Código do Estúdio Visual abre, selecione a partir destas opções:

   * **Criar nova Aplicação Lógica (Pré-visualização) em Azure** (rápido)
   * **Criar nova App Lógica (Pré-visualização) em Azure Advanced**
   * Um recurso **de Aplicação Lógica (Preview)** previamente implantado, se existir

   Este exemplo continua com **a Criação de nova App Lógica (Preview) no Azure Advanced**.

   ![Screenshot que mostra o painel "Azure: Logic Apps (Preview)" com uma lista com "Create new Logic App (Preview) in Azure" selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Para criar o seu novo recurso **Logic App (Preview),** siga estes passos:

   1. Forneça um nome globalmente único para a sua nova aplicação lógica, que é o nome a usar para o recurso **Logic App (Preview).** Este exemplo `example-logic-app-preview` utiliza.

      ![Screenshot que mostra o painel "Azure: Logic Apps (Preview)" e um pedido para fornecer um nome para a nova aplicação lógica para criar.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Selecione um plano de hospedagem para a sua nova aplicação lógica, seja [**o Plano de Serviço de Aplicações**](../azure-functions/functions-scale.md#app-service-plan) ou [**Premium.**](../azure-functions/functions-scale.md#premium-plan) Este exemplo seleciona **o Plano de Serviço de Aplicações.**

      ![Screenshot que mostra o painel "Azure: Logic Apps (Preview)" e um pedido para selecionar "App Service Plan" ou "Premium".](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Crie um novo plano de Serviço de Aplicações ou selecione um plano existente. Este exemplo **seleciona Criar novo Plano de Serviço de Aplicações.**

      ![Screenshot que mostra o painel "Azure: Logic Apps (Preview)" e um pedido para "Criar um novo Plano de Serviço de Aplicações" ou selecionar um plano de Serviço de Aplicações existente.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Forneça um nome para o seu plano de Serviço de Aplicações e, em seguida, selecione um [nível de preços](../app-service/overview-hosting-plans.md) para o plano. Este exemplo seleciona o plano **F1 Free.**

      ![Screenshot que mostra o painel "Azure: Logic Apps (Preview)" e um pedido para selecionar um nível de preços.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Para obter um desempenho ótimo, encontre e selecione o mesmo grupo de recursos que o seu projeto para a implantação.

      > [!NOTE]
      > Embora possa criar ou utilizar um grupo de recursos diferente, fazê-lo pode afetar o desempenho. Se criar ou escolher um grupo de recursos diferente, mas cancelar depois de aparecer o pedido de confirmação, a sua implementação também é cancelada.

   1. Para fluxos de trabalho estatais, selecione **Criar uma nova conta de armazenamento** ou uma conta de armazenamento existente.

      ![Screenshot que mostra o painel "Azure: Logic Apps (Preview)" e um pedido para criar ou selecionar uma conta de armazenamento.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Para obter uma capacidade de registo e rastreio de diagnósticos mais fácil, pode selecionar um recurso existente de Insights de Aplicação. Caso contrário, pode selecionar **Criar novos recursos de Insights de Aplicação** ou configurar o Application Insights no portal Azure depois de implementar a sua aplicação.

      Antes de implementar, certifique-se de que adiciona o `logLevel` objeto ao objeto nohost.js`logging` **no** ficheiro que existe ao nível da raiz do seu projeto, e desloque o `Host.Triggers.Workflow` , por `Information` exemplo:

      ```json
      "logLevel": {
         "Host.Triggers.Workflow": "Information"
      },
      ```

      Eis como pode parecer o **host.jsarquivado:**

      ```json
      {
         "version": "2.0",
         "logging": {
            "applicationInsights": {
               "samplingExcludedTypes": "Request",
               "samplingSettings": {
                  "isEnabled": true
               }
            },
            "logLevel": {
               "Host.Triggers.Workflow": "Information"
            }
         }
      }
      ``` 

   Quando terminar, o Visual Studio Code começa a criar e a implementar os recursos necessários para a publicação da sua aplicação lógica.

1. Para rever e monitorizar o processo de implementação, no menu **Ver,** selecione **Output**. A partir da lista de barras de ferramentas da janela de saída, selecione **Azure Logic Apps**.

   ![Screenshot que mostra a janela de saída com as "Azure Logic Apps" selecionadas na lista de barras de ferramentas juntamente com o progresso e o estado de implementação.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Quando o Código do Estúdio Visual terminar de implementar o fluxo de trabalho da sua aplicação lógica para o Azure, esta mensagem aparece:

   ![Screenshot que mostra uma mensagem que a implementação para Azure completou com sucesso.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Parabéns, a sua aplicação lógica está agora ao vivo no Azure e ativada por padrão.

Em seguida, pode aprender a executar estas tarefas:

* [Encontre a sua aplicação lógica implantada no portal Azure](#find-manage-deployed-workflows-portal) ou [no Código do Estúdio Visual.](#find-manage-deployed-workflows-vs-code)

* [Permitir executar o histórico em fluxos de trabalho de aplicativos de lógica apátrida](#run-history).

* [Ativar a monitorização num recurso de **Aplicação Lógica (Preview)** implantado.](#enable-monitoring)

<a name="find-manage-deployed-workflows-vs-code"></a>

## <a name="find-and-manage-deployed-logic-apps-in-visual-studio-code"></a>Localizar e gerir aplicações lógicas implementadas no Código do Estúdio Visual

No Código do Estúdio Visual, pode ver todas as aplicações lógicas implementadas na sua subscrição Azure, sejam as **aplicações lógicas** originais ou o tipo de recurso **Logic App (Preview)** e selecionar tarefas que o ajudam a gerir essas aplicações lógicas. No entanto, para aceder a ambos os tipos de recursos, precisa tanto das **aplicações Azure Logic Apps** como das **extensões Azure Logic Apps (Preview)** para o Código do Estúdio Visual.

1. Na barra de ferramentas esquerda, selecione o ícone Azure. No painel **Azure: Logic Apps (Preview),** expanda a sua subscrição, que mostra todas as aplicações lógicas implementadas para essa subscrição.

1. Encontre e selecione a aplicação lógica que pretende gerir. Abra o menu de atalho da aplicação lógica e selecione a tarefa que pretende executar.

   Por exemplo, pode selecionar tarefas como parar, iniciar, reiniciar ou eliminar a aplicação lógica implementada.

   ![Screenshot que mostra Visual Studio Code com o painel de extensão "Azure Logic Apps (Preview)" e o fluxo de trabalho implantado.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Para ver todos os fluxos de trabalho na aplicação lógica, expanda a sua aplicação lógica e, em seguida, expanda o nó **workflows.**

1. Para visualizar um fluxo de trabalho específico, abra o menu de atalho do fluxo de trabalho e selecione **Open in Designer**, que abre o fluxo de trabalho no modo apenas de leitura.

   Para editar o fluxo de trabalho, tem estas opções:

   * No Visual Studio Code, abra asworkflow.jsdo seu projeto em arquivo **no** Logic App Designer, faça as suas edições e reimplante a sua aplicação lógica para o Azure.

   * No portal Azure, [encontre e abra a sua aplicação lógica.](#find-manage-deployed-workflows-portal) Encontre, edite e guarde o fluxo de trabalho.

1. Para abrir a aplicação lógica implementada no portal Azure, abra o menu de atalho da aplicação lógica e selecione **Abrir no Portal**.

   O portal Azure abre no seu navegador, assina-o automaticamente no portal se estiver a assinar o Código do Estúdio Visual e mostrar a sua aplicação lógica.

   ![Screenshot que mostra a página do portal Azure para a sua aplicação lógica no Código do Estúdio Visual.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Também pode iniciar sação separadamente no portal Azure, utilizar a caixa de pesquisa do portal para encontrar a sua aplicação lógica e, em seguida, selecionar a sua aplicação lógica na lista de resultados.

   ![Screenshot que mostra o portal Azure e a barra de pesquisa com resultados de pesquisa para aplicação lógica implementada, que aparece selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="find-manage-deployed-workflows-portal"></a>

## <a name="find-and-manage-deployed-logic-apps-in-the-portal"></a>Localizar e gerir aplicações lógicas implementadas no portal

No portal Azure, é possível visualizar todas as aplicações lógicas implementadas que se encontrem na sua subscrição Azure, sejam elas o tipo original de recursos **de Aplicações Lógicas** ou o tipo de recurso **Logic App (Preview).** Atualmente, cada tipo de recurso é organizado e gerido como categorias separadas em Azure. Para encontrar aplicações lógicas que tenham o tipo de recurso **Logic App (Preview),** siga estes passos:

1. Na caixa de pesquisa do portal Azure, insira `logic app preview` . Quando a lista de resultados aparecer, em **Serviços**, selecione **Logic App (Preview)**.

   ![Screenshot que mostra a caixa de pesquisa do portal Azure com o texto de pesquisa de "pré-visualização de aplicações lógicas".](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. No painel **de aplicações lógicas (Preview),** encontre e selecione a aplicação lógica que implementou a partir do Código do Estúdio Visual.

   ![Screenshot que mostra o portal Azure e os recursos da Logic App (Preview) implantados no Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   O portal Azure abre a página de recursos individuais para a aplicação lógica selecionada.

   ![Screenshot que mostra a página de recursos do seu fluxo de trabalho de aplicação lógica no portal Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Para ver os fluxos de trabalho nesta aplicação lógica, no menu da aplicação lógica, selecione **Workflows**.

   O **painel workflows** mostra todos os fluxos de trabalho na aplicação lógica atual. Este exemplo mostra o fluxo de trabalho que criou no Código do Estúdio Visual.

   ![Screenshot que mostra uma página de recursos "Logic App (Preview)" com o painel de "Workflows" aberto e o fluxo de trabalho implantado](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Para visualizar um fluxo de trabalho, no **painel de fluxos de trabalho,** selecione esse fluxo de trabalho.

   O painel de trabalho abre e mostra mais informações e tarefas que pode executar nesse fluxo de trabalho.

   Por exemplo, para ver os passos no fluxo de trabalho, **selecione Designer**.

   ![Screenshot que mostra o painel "Visão Geral" do fluxo de trabalho selecionado, enquanto o menu de fluxo de trabalho mostra o comando "Designer" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   O Logic App Designer abre e mostra o fluxo de trabalho que construiu no Código do Estúdio Visual. Pode agora fazer alterações a este fluxo de trabalho no portal Azure.

   ![Screenshot que mostra o Designer de Aplicações Lógica e fluxo de trabalho implementado a partir do Código do Estúdio Visual.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflows"></a>

## <a name="add-a-workflow-to-deployed-logic-apps"></a>Adicione um fluxo de trabalho a aplicações lógicas implementadas

Através do portal Azure, pode adicionar fluxos de trabalho em branco a um recurso **da Logic App (Preview)** que implementou a partir do Código do Estúdio Visual e construir esses fluxos de trabalho no portal Azure.

1. No [portal Azure,](https://portal.azure.com)encontre e selecione o recurso **'Preview'** da Sua Aplicação Lógica implementada.

1. No menu da aplicação lógica, selecione **Workflows**. No painel **workflows,** **selecione Adicionar**.

   ![Screenshot que mostra o painel de "Workflows" e a barra de ferramentas selecionadas pela aplicação lógica selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. No **painel de trabalho Novo,** forneça o nome para o fluxo de trabalho. Selecione **Stateful** ou **Stateless** **>** **Create**.

   Depois de o Azure implementar o seu novo fluxo de trabalho, que aparece no painel **workflows,** selecione esse fluxo de trabalho para executar a gestão e outras tarefas, como a abertura do Logic App Designer ou a visão de código.

   ![Screenshot que mostra o fluxo de trabalho selecionado com opções de gestão e revisão.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Por exemplo, a abertura do designer para um novo fluxo de trabalho mostra uma tela em branco. Agora pode construir este fluxo de trabalho no portal Azure.

   ![Screenshot que mostra o Logic App Designer e um fluxo de trabalho em branco.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="run-history"></a>

## <a name="run-history-for-stateless-logic-app-workflows"></a>Executar história para fluxos de trabalho de aplicativos de lógica apátrida

Para depurar mais facilmente um fluxo de trabalho de aplicações lógicas apátridas, pode ativar o histórico de execução para esse fluxo de trabalho no Código do Estúdio Visual ou no portal Azure, e depois desativar o histórico de execução quando terminar.

### <a name="for-a-stateless-logic-app-workflow-in-visual-studio-code"></a>Para um fluxo de trabalho de aplicativos de lógica apátrida no Código do Estúdio Visual

Se estiver a trabalhar e a executar o fluxo de trabalho de aplicações de lógica apátrida localmente no Código do Estúdio Visual, siga estes passos:

1. No seu projeto, encontre e expanda a pasta **de tempo de design de fluxo de trabalho.** Encontre e abra a **local.settings.jsarquivada.**

1. Adicione o `Workflows.{yourWorkflowName}.operationOptions` imóvel e desateia o valor `WithStatelessRunHistory` para, por exemplo:

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Para desativar o histórico de execução quando terminar, ou elimine a propriedade e o `Workflows.{yourWorkflowName}.OperationOptions` seu valor, ou desative a propriedade para `None` .

### <a name="for-a-stateless-logic-app-workflow-in-the-azure-portal"></a>Para um fluxo de trabalho de aplicativos de lógica apátrida no portal Azure

Se já implementou o seu projeto no portal Azure, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)encontre e abra o seu recurso **Logic App (Preview).**

1. No menu da aplicação lógica, em **Definições,** selecione **Configuração**.

1. No **separador Definições de Aplicação,** selecione **nova definição de aplicação**.

1. No painel de definição de **aplicação Add/Edit,** na caixa **Nome,** insira este nome de opção de funcionamento: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. Na caixa **Valor,** insira o seguinte valor: `WithStatelessRunHistory`

   Por exemplo:

   ![Screenshot que mostra o portal Azure e o recurso Logic App (Preview) com a "Configuração" > "Nova definição de aplicação" < painel de "Adicionar/Editar a definição de aplicação" aberta e os "Fluxos de Trabalho". {yourWorkflowName}. Opção OperationOptions" definida como "WithStatelessRunHistory".](./media/create-stateful-stateless-workflows-visual-studio-code/stateless-operation-options-run-history.png)

1. Quando tiver terminado, selecione **OK**. No painel **de configuração,** selecione **Guardar**.

Para ativar a monitorização no recurso da Aplicação Lógica (Pré-visualização) implantado, continue para a secção seguinte.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-for-deployed-logic-app-preview-resources"></a>Permitir a monitorização dos recursos da App Lógica (Pré-visualização) implantados

Para permitir a monitorização num recurso **de Aplicação Lógica (Pré-visualização)** implantado, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)encontre e selecione o recurso **'Preview'** da App Lógica implementada.

1. No menu desse recurso, em **API,** selecione **CORS**.

1. No painel **CORS,** em **"Origens Permitidas",** adicione o caractere wildcard (*).

1. Quando terminar, na barra de **ferramentas CORS,** **selecione Save**.

   ![Screenshot que mostra o portal Azure com um recurso de Aplicações Lógicas implementadas (Preview). No menu de recursos, "CORS" é selecionado com uma nova entrada para "Origens Permitidas" definida para o caracteres wildcard "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker-container"></a>Implantar no contentor Docker

Ao utilizar a [ferramenta de interface de linha de comando .NET Core (CLI),](/dotnet/core/tools/)pode construir o seu projeto e, em seguida, publicar a sua construção. Em seguida, pode construir e utilizar um [recipiente Docker](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) como destino para implantar o seu fluxo de trabalho de aplicações lógicas. Para mais informações, reveja estes tópicos:

* [Introdução a Contentores e Estivador](/dotnet/architecture/microservices/container-docker-introduction/)
* [Introdução a .NET e Docker](/dotnet/core/docker/introduction)
* [Terminologia de estivador](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)
* [Tutorial: Começa com o Docker](/visualstudio/docker/tutorials/docker-tutorial)

1. Para construir o seu projeto, abra um pedido de linha de comando e execute este comando:

   `dotnet build -c release`

   Para mais informações, consulte a página de referência [da construção do pontonet.](/dotnet/core/tools/dotnet-build/)

1. Publique a sua construção executando este comando:

   `dotnet publish -c release`

   Para mais informações, consulte a página de referência da [publicação do dotnet.](/dotnet/core/tools/dotnet-publish/)

1. Construa um recipiente Docker utilizando um ficheiro Docker para um fluxo de trabalho .NET e executando este comando:

   `docker build --tag local/workflowcontainer .`

   Por exemplo, aqui está uma amostra do ficheiro Docker que implementa uma aplicação lógica estatal e especifica a cadeia de ligação para a conta de Armazenamento Azure que foi usada para publicar a aplicação lógica para o portal Azure. Para encontrar e copiar a cadeia de ligação da conta de armazenamento no portal Azure, [reveja as teclas da conta de armazenamento](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

   ![Screenshot que mostra o portal Azure com chaves de acesso à conta de armazenamento e fio de ligação copiado.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   A cadeia de ligação é semelhante a esta amostra:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageaccount;AccountKey={access-key};EndpointSuffix=core.windows.net`

   Aqui está o formato do ficheiro Docker:


   ```text
   FROM mcr.microsoft.com/azure-functions/dotnet:3.0.14492-appservice

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AZURE_FUNCTIONS_ENVIRONMENT Development
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot
   ENV AzureFunctionsJobHost__Logging__Console__IsEnabled=true
   ENV FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY ./bin/Release/netcoreapp3.1/publish/ /home/site/wwwroot
   ```

   Para mais informações, consulte [a construção do docker.](https://docs.docker.com/engine/reference/commandline/build/)

1. Guarde a cadeia em algum lugar seguro para que possa adicionar a cadeia ao **local.settings.jsem** ficheiros do projeto que utiliza para criar a sua aplicação lógica no Código do Estúdio Visual.

1. Executar o recipiente localmente utilizando este comando:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Para mais informações, consulte [o estivador.](https://docs.docker.com/engine/reference/commandline/run/)

1. Para obter o URL de retorno para o gatilho do pedido, envie este pedido:

   `POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2019-10-01-edge-preview&code={master-key}`

   O valor *de*> de <é definido na conta de armazenamento Azure que definiu `AzureWebJobsStorage` no ficheiro, **azure-webjobs-secrets/{deployment-name}/host.jsem**, onde pode encontrar o valor nesta secção:

   ```json
   {
     <...>
     "masterKey": {
        "name": "master",
        "value": "<master-key>",
        "encrypted": false
     },
     <...>
   }
   ```

<a name="nested-behavior"></a>

## <a name="nested-behavior-differences-between-stateful-and-stateless-logic-apps"></a>Diferenças de comportamento aninhadas entre aplicações lógicas apátridas e apátridas

Pode [tornar um fluxo de trabalho de aplicações lógicas chamado](../logic-apps/logic-apps-http-endpoint.md) a partir de outros fluxos de trabalho de aplicações lógicas que existem no mesmo recurso **Logic App (Preview)** utilizando o gatilho [do Pedido,](../connectors/connectors-native-reqres.md) o gatilho [HTTP Webhook](../connectors/connectors-native-webhook.md) ou os gatilhos de conector geridos que têm o [tipo ApiConnectionWehook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e podem receber pedidos HTTPS.

Aqui estão os padrões de comportamento que os fluxos de trabalho de aplicações de lógica aninhados podem seguir depois de um fluxo de trabalho dos pais chamar um fluxo de trabalho infantil:

* Padrão de sondagem assíncronos

  O progenitor não espera por uma resposta à sua chamada inicial, mas verifica continuamente o histórico da criança até que a criança termine de correr. Por predefinição, os fluxos de trabalho estatais seguem este padrão, que é ideal para fluxos de trabalho infantis de longa duração que podem exceder [os limites de tempo de pedido](../logic-apps/logic-apps-limits-and-config.md).

* Padrão sincronizado ("fogo e esquecimento")

  A criança reconhece o apelo devolvendo imediatamente uma `202 ACCEPTED` resposta, e o progenitor continua para a próxima ação sem esperar pelos resultados da criança. Em vez disso, o progenitor recebe os resultados quando a criança termina de correr. Os fluxos de trabalho de crianças que não incluem uma ação de resposta seguem sempre o padrão sincronizado. Para fluxos de trabalho de crianças, o histórico de execução está disponível para você rever.

  Para permitir este comportamento, na definição JSON do fluxo de trabalho, definir a `operationOptions` propriedade para `DisableAsyncPattern` . Para obter mais informações, consulte [o Gatilho e os tipos de ação - Opções de operação](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Desencadear e esperar

  Para um fluxo de trabalho apátrida infantil, o progenitor aguarda uma resposta que devolva os resultados da criança. Este padrão funciona semelhante ao uso do gatilho HTTP incorporado [ou ação](../connectors/connectors-native-http.md) para chamar um fluxo de trabalho infantil. Os fluxos de trabalho apátridas infantis que não incluem uma ação de resposta devolvem imediatamente uma `202 ACCEPTED` resposta, mas o progenitor espera que a criança termine antes de continuar para a próxima ação. Estes comportamentos aplicam-se apenas aos fluxos de trabalho apátridas infantis.

Esta tabela especifica o comportamento do fluxo de trabalho da criança com base no facto de os pais e a criança serem estatais, apátridas ou se são tipos de fluxo de trabalho mistos:

| Fluxo de trabalho dos pais | Fluxo de trabalho infantil | Comportamento infantil |
|-----------------|----------------|----------------|
| Com monitorização de estado | Com monitorização de estado | Assíncronos ou sincronizados com `"operationOptions": "DisableAsyncPattern"` a definição |
| Com monitorização de estado | Sem estado | Desencadear e esperar |
| Sem estado | Com monitorização de estado | Synchronous (Síncrono) |
| Sem estado | Sem estado | Desencadear e esperar |
||||

## <a name="limits"></a>Limites

Embora [muitos limites existentes para apps Azure Logic](../logic-apps/logic-apps-limits-and-config.md) sejam os mesmos para este tipo de recurso, aqui estão as diferenças nesta extensão de pré-visualização pública:

* Conectores geridos: 50 pedidos por minuto por ligação

* Para a ação do Código Inline para a ação [JavaScript,](../logic-apps/logic-apps-add-run-inline-code.md) estes limites mudaram:

  * O limite nos caracteres de código aumenta de 1.024 caracteres para 100.000 caracteres.

  * O limite de tempo para executar o código aumenta de 5 segundos para 15 segundos.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Erros e problemas de resolução de problemas

<a name="400-bad-request"></a>

### <a name="400-bad-request"></a>"400 Mau Pedido"

Quando uma execução falha e inspeciona a execução na visualização de monitorização, este erro pode aparecer num gatilho ou ação com um nome mais longo, o que faz com que o identificador de recursos uniformes subjacente (URI) exceda o limite de caracteres predefinido.

Para resolver este problema e ajustar para o URI mais longo, edite as `UrlSegmentMaxCount` `UrlSegmentMaxLength` teclas e registo no seu computador seguindo os passos abaixo. Os valores predefinidos desta chave são descritos neste tópico, [Http.sys definições de registo para o Windows](/troubleshoot/iis/httpsys-registry-windows).

> [!IMPORTANT]
> Antes de começar, certifique-se de salvar o seu trabalho. Esta solução requer que reinicie o computador depois de ter terminado para que as alterações possam produzir efeitos.

1. No seu computador, abra a janela **Run** e execute o `regedit` comando, que abre o editor de registo.

1. Na caixa **de Controlo da Conta de Utilizador,** selecione **Sim** para permitir as alterações no seu computador.

1. No painel esquerdo, sob **computador,** expanda os nós ao longo do caminho, **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters,** e, em seguida, selecione **Parâmetros**.

1. No painel direito, encontre as `UrlSegmentMaxCount` chaves e as chaves do `UrlSegmentMaxLength` registo.

1. Aumente estes valores-chave o suficiente para que os URIs possam acomodar os nomes que pretende utilizar. Se estas teclas não existirem, adicione-as à pasta **Parâmetros** seguindo estes passos:

   1. A partir do menu de atalhos **parâmetros,** selecione **Novo**  >  **DWORD (32-bit) Valor**.

   1. Na caixa de edição que aparece, introduza `UrlSegmentMaxCount` como o novo nome-chave.

   1. Abra o menu de atalho da nova chave e selecione **Modificar**.

   1. Na caixa **de cadeia de edição** que aparece, introduza o valor chave de **dados de valor** que deseja em formato hexadecimal ou decimal. Por exemplo, `400` no hexadecimal é equivalente `1024` ao decimal.

   1. Para adicionar o valor da `UrlSegmentMaxLength` chave, repita estes passos.

   Depois de aumentar ou adicionar estes valores-chave, o editor de registo parece este exemplo:

   ![Screenshot que mostra o editor de registo.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Quando estiver pronto, reinicie o computador para que as alterações possam produzir efeitos.

## <a name="next-steps"></a>Passos seguintes

Gostaríamos de ouvir a sua parte sobre as suas experiências com esta extensão de pré-visualização pública!

* Para bugs ou problemas, [crie os seus problemas no GitHub](https://github.com/Azure/logicapps/issues).
* Para perguntas, pedidos, comentários e outros comentários, [utilize este formulário de feedback](https://aka.ms/lafeedback).
