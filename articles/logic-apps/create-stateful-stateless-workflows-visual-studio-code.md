---
title: Criar aplicativos de lógica pré-visualizar fluxos de trabalho em Código de Estúdio Visual
description: Construa e execute fluxos de trabalho para cenários de automação e integração no Código do Estúdio Visual com a extensão Azure Logic Apps (Preview).
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: a74244cc45e1a3d2116f98ac73290d184f4affc0
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632065"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Criar fluxos de trabalho apátridas e apátridas no Código do Estúdio Visual com a extensão Azure Logic Apps (Preview)

> [!IMPORTANT]
> Esta capacidade está disponível em pré-visualização pública, é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Com [a Azure Logic Apps Preview,](logic-apps-overview-preview.md)pode construir soluções de automação e integração através de apps, dados, serviços na nuvem e sistemas, criando e executando aplicações lógicas que incluem [fluxos de trabalho *apátridas* e *apátridas*](logic-apps-overview-preview.md#stateful-stateless) no Código do Estúdio Visual utilizando a extensão Azure Logic Apps (Preview). Ao utilizar este novo tipo de aplicação lógica, pode construir múltiplos fluxos de trabalho que são alimentados pelo redesenhado Azure Logic Apps Preview tempo de funcionamento, que proporciona portabilidade, melhor desempenho e flexibilidade para implantar e executar em vários ambientes de hospedagem, não só Azure, mas também recipientes Docker. Para saber mais sobre o novo tipo de aplicação lógica, consulte [a Visão geral para pré-visualização de apps Azure Logic.](logic-apps-overview-preview.md)

![Screenshot que mostra Visual Studio Code, projeto de aplicações lógicas e fluxo de trabalho.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

No Visual Studio Code, pode começar por criar um projeto que constrói e executa *localmente* os fluxos de trabalho da sua aplicação lógica no seu ambiente de desenvolvimento utilizando a extensão Azure Logic Apps (Preview). Embora também possa começar por [criar um novo recurso de **Aplicação Lógica (Preview)** no portal Azure](create-stateful-stateless-workflows-azure-portal.md), ambas as abordagens fornecem a capacidade para implementar e executar a sua aplicação lógica nos mesmos tipos de ambientes de hospedagem.

Entretanto, ainda pode criar o tipo de aplicação lógica original. Embora as experiências de desenvolvimento no Código do Estúdio Visual diferam entre os tipos de aplicações lógicas originais e novas, a sua subscrição Azure pode incluir ambos os tipos. Pode ver e aceder a todas as aplicações lógicas implementadas na sua subscrição Azure, mas as aplicações estão organizadas nas suas próprias categorias e secções.

Este artigo mostra como criar a sua aplicação lógica e um fluxo de trabalho no Código do Estúdio Visual utilizando a extensão Azure Logic Apps (Preview) e executando estas tarefas de alto nível:

* Crie um projeto para a sua aplicação lógica e fluxo de trabalho.

* Adicione um gatilho e uma ação.

* Executar, testar, depurar e rever a história localmente.

* Implementar para a Azure, que inclui opcionalmente habilitar insights de aplicação.

* Gerencie a sua aplicação lógica implementada no Código do Estúdio Visual e no portal Azure.

* Permitir a história da execução para fluxos de trabalho apátridas.

* Ativar ou abrir os Insights de Aplicação após a implementação.

* Desloque-se para um contentor Docker que pode correr para qualquer lugar.

> [!NOTE]
> Para obter informações sobre os problemas atuais conhecidos, reveja a [página de Questões Conhecidas de Pré-Visualização Pública de Aplicações Lógicas no GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="access-and-connectivity"></a>Acesso e conectividade

* Aceda à internet para que possa descarregar os requisitos, ligar do Código do Estúdio Visual à sua conta Azure e publicar de Visual Studio Code para Azure, um recipiente Docker ou outro ambiente.

* Uma conta e subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Para construir a mesma aplicação lógica de exemplo neste artigo, precisa de uma conta de e-mail do Office 365 Outlook que utilize uma conta de trabalho ou escola da Microsoft para iniciar scontabilidade.

  Se optar por utilizar um [conector de e-mail diferente que seja suportado por Azure Logic Apps](/connectors/)– como Outlook.com ou [Gmail](../connectors/connectors-google-data-security-privacy-policy.md)– ainda pode seguir o exemplo, e os passos gerais gerais são os mesmos, mas a interface e as opções do utilizador podem diferir de alguma forma. Por exemplo, se utilizar o conector Outlook.com, use a sua conta pessoal da Microsoft para iniciar scontabilidade.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Requisitos de armazenamento

#### <a name="windows-and-linux"></a>Windows e Linux

1. Descarregue e instale [o Emulador de Armazenamento Azure 5.10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Para executar o emulador, é necessário ter uma instalação DB SQL local, como o [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658)gratuito. Para obter mais informações, consulte [o emulador de armazenamento Azure para desenvolvimento e teste.](../storage/common/storage-use-emulator.md)

   > [!IMPORTANT]
   > Antes de abrir o designer para construir o seu fluxo de trabalho, certifique-se de que inicia o emulador. Caso contrário, recebe-se uma mensagem de `Workflow design time could not be started` que.
   >
   > ![Screenshot que mostra o Emulador de Armazenamento Azure a funcionar.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos"></a>macOS

1. Inscreva-se no [portal Azure,](https://portal.azure.com)e [crie uma conta de Armazenamento Azure](../storage/common/storage-account-create.md?tabs=azure-portal), que é um [pré-requisito para funções azures](../azure-functions/storage-considerations.md).

1. [Encontre e copie o fio de ligação da conta de armazenamento,](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)por exemplo:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Screenshot que mostra o portal Azure com chaves de acesso à conta de armazenamento e fio de ligação copiado.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

1. Guarde a cadeia em algum lugar seguro para que possa adicionar a cadeia ao **local.settings.jsem** ficheiros do projeto que utiliza para criar a sua aplicação lógica no Código do Estúdio Visual.

Quando mais tarde tentar abrir o designer para um fluxo de trabalho na sua aplicação lógica, recebe uma mensagem de que `Workflow design time could not be started` . Depois de esta mensagem aparecer, tem de adicionar a cadeia de ligação da conta de armazenamento aos dois **local.settings.jsem** ficheiros do projeto e voltar a tentar abrir o designer novamente.

### <a name="tools"></a>Ferramentas

* [Visual Studio Code 1.30.1 (janeiro 2019) ou superior](https://code.visualstudio.com/), que é gratuito. Além disso, descarregue e instale estas ferramentas adicionais para Visual Studio Code, se ainda não as tiver:

  * [Extensão da Conta Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), que fornece uma única experiência comum de visualização e filtragem de subscrição de Azure para todas as outras extensões Azure no Código do Estúdio Visual.

  * [C# para a extensão Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), que permite que a funcionalidade F5 execute a sua aplicação lógica.

  * [Funções Azure Core Tools 3.0.2931 ou mais tarde](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) utilizando o Instalador microsoft (MSI).

    Estas ferramentas incluem uma versão do mesmo tempo de execução que alimenta o tempo de execução das Funções Azure, que a extensão de pré-visualização utiliza no Código do Estúdio Visual.

    > [!IMPORTANT]
    > Se tiver uma instalação mais cedo do que estas versões, desinstale essa versão primeiro, ou certifique-se de que a variável do ambiente PATH aponta para a versão que descarrega e instala.

  * [Extensão Azure Logic Apps (Preview) para Código de Estúdio Visual](https://go.microsoft.com/fwlink/p/?linkid=2143167). Esta extensão fornece a capacidade para criar apps lógicas onde pode construir fluxos de trabalho apátridas e apátridas que funcionam localmente no Código do Estúdio Visual e, em seguida, implementar essas aplicações lógicas diretamente para Azure ou para os contentores Docker.

    Atualmente, pode ter a extensão original das Apps Azure Logic e a extensão de visualização pública instalada no Código do Estúdio Visual. Embora as experiências de desenvolvimento diferam de alguma forma entre as extensões, a sua subscrição do Azure pode incluir ambos os tipos de aplicações lógicas que cria com as extensões. O Visual Studio Code mostra todas as aplicações lógicas implementadas na sua subscrição Azure, mas organiza-as em diferentes secções por nomes de extensões, **Apps Lógicas** e **Apps Azure Logic (Preview)**.

    > [!IMPORTANT]
    > Se criou aplicações lógicas com a extensão anterior da Pré-visualização Privada, estas aplicações lógicas não funcionarão com a extensão de Visualização Pública. No entanto, pode migrar estas aplicações lógicas depois de desinstalar a extensão de Pré-visualização Privada, eliminar os ficheiros associados e instalar a extensão de Visualização Pública. Em seguida, cria um novo projeto no Visual Studio Code e copia o ficheiro **workflow.definition** da sua aplicação lógica anteriormente criado no seu novo projeto. Para obter mais informações, consulte [Migrar a partir da extensão de pré-visualização privada](#migrate-private-preview).

    Para instalar a extensão **Azure Logic Apps (Preview),** siga estes passos:

    1. No Código do Estúdio Visual, na barra de ferramentas esquerda, selecione **Extensões**.

    1. Na caixa de pesquisa de extensões, insira `azure logic apps preview` . Na lista de resultados, selecione **Azure Logic Apps (Preview)** **>** **Install**.

       Após a conclusão da instalação, a extensão de pré-visualização aparece na lista **de extensões: Lista instalada.**

       ![Screenshot que mostra a lista de extensões instaladas do Visual Studio Code com a extensão "Azure Logic Apps (Preview)" sublinhada.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* Para executar localmente os gatilhos e ações baseados em webhook, como o [gatilho HTTP Webhook incorporado,](../connectors/connectors-native-webhook.md)no Código do Estúdio Visual, é necessário [configurar o reencaminhamento para o URL de retorno](#webhook-setup).

* Para testar a aplicação lógica de exemplo que cria neste artigo, precisa de uma ferramenta que possa enviar chamadas para o gatilho 'Pedido', que é o primeiro passo na aplicação lógica de exemplo. Se não tiver tal ferramenta, pode descarregar, instalar e utilizar [o Carteiro](https://www.postman.com/downloads/).

* Se criar a sua aplicação lógica e implementar com definições que suportem a utilização de [Application Insights,](../azure-monitor/app/app-insights-overview.md)pode opcionalmente ativar o registo de diagnósticos e o rastreio da sua aplicação lógica. Pode fazê-lo quando implementar a sua aplicação lógica a partir do Código do Estúdio Visual ou após a implementação. Precisa de ter uma instância de Insights de Aplicação, mas pode criar este recurso [com antecedência](../azure-monitor/app/create-workspace-resource.md), quando implementa a sua aplicação lógica ou após a implementação.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>Migrar da extensão de pré-visualização privada

Quaisquer aplicações lógicas que tenha criado com a extensão **Azure Logic Apps (Private Preview)** não funcionarão com a extensão PublicPreview. No entanto, pode migrar estas aplicações lógicas para um novo projeto visual Studio Code seguindo estes passos:

1. Desinstale a extensão de pré-visualização privada.

1. Elimine o pacote de extensão associado e as pastas de pacotes NuGet nestes locais:

   * A pasta **Microsoft.Azure.Functions.ExtensionBundle.Workflows,** que contém pacotes de extensão anteriores e está localizada ao longo de ambos os caminhos aqui:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}.azure-functions-core-tools\Functions\ExtensionBundles`

   * A pasta **microsoft.azure.workflows.webjobs.extension,** que é a cache [NuGet](/nuget/what-is-nuget) para a extensão de pré-visualização privada e está localizada ao longo deste caminho:

     `C:\Users\{userName}\.nuget\packages`

1. Instale a extensão **Azure Logic Apps (Preview).**

1. Criar um novo projeto no Código do Estúdio Visual.

1. Copie o ficheiro **workflow.definition** da sua aplicação lógica previamente criada para o seu novo projeto.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Configurar o Visual Studio Code

1. Para se certificar de que todas as extensões estão corretamente instaladas, recarregue ou reinicie o Código do Estúdio Visual.

1. Ative ou confirme que o Visual Studio Code encontra e instala automaticamente atualizações de extensão para que a extensão PublicPreview receba as últimas atualizações. Caso contrário, terá de desinstalar manualmente a versão desatualizada e instalar a versão mais recente.

   Para verificar esta definição, siga estes passos:

   1. No menu **'Ficheiro',** aceda às **Definições de** **>** **Preferências.**

   1. No separador **Utilizador,** aceda às **Extensões de** **>** **Funcionalidades.**

   1. Confirme que estão **selecionadas atualizações** de verificação automática e **atualização automática.**

1. Ativar ou confirmar que estas definições de extensão têm as seleções corretas:

   * **Azure Logic Apps V2: Modo painel**
   * **Azure Logic Apps V2: Project Runtime**

   1. No menu **'Ficheiro',** aceda às **Definições de** **>** **Preferências.**

   1. No **separador Utilizador,** aceda a **>** **Extensões** **>** **Azure Logic Apps (Preview)**.

   1. Confirme estas definições de extensão:

      * No **âmbito das Azure Logic Apps V2: Modo painel**, confirme que o modo de painel **Enable** está selecionado.

      * No âmbito **das Azure Logic Apps V2: Project Runtime,** confirme que a versão está definida para **~3**.

        > [!IMPORTANT]
        > Para utilizar [as ações inline Code Operations](../logic-apps/logic-apps-add-run-inline-code.md), que atualmente não se encontra disponível para macOS e Linux, a definição de Tempo de **Execução** do Projeto requer a versão 3.

      ![Screenshot que mostra definições de Código do Estúdio Visual para extensão "Azure Logic Apps (Preview)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

1. Na Barra de Atividade do Código do Estúdio Visual, selecione o ícone Azure.

   ![Screenshot que mostra Visual Studio Code Activity Bar e ícone Azure selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. No painel Azure, em **Azure: Logic Apps (Preview)**, selecione **Iniciar sedura no Azure**. Quando aparecer a página de autenticação do Código do Estúdio Visual, inscreva-se na sua conta Azure.

   ![Screenshot que mostra painel Azure e link selecionado para o sinal de Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Depois de iniciar sação, o painel Azure mostra as subscrições na sua conta Azure. Se também tiver a extensão publicamente lançada, pode encontrar quaisquer aplicações lógicas que tenha criado com essa extensão na secção **De Aplicações Lógicas,** e não na secção **De Aplicações Lógicas (Preview).**
   
   Se as subscrições esperadas não aparecerem, ou se pretender que o painel apresente apenas subscrições específicas, siga estes passos:

   1. Na lista de subscrições, mova o ponteiro ao lado da primeira subscrição até que apareça o botão **'Selecionar assinaturas'** (ícone do filtro). Selecione o ícone do filtro.

      ![Screenshot que mostra painel Azure e ícone de filtro selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Ou, na barra de estado do Código do Estúdio Visual, selecione a sua conta Azure. 

   1. Quando aparecer outra lista de subscrições, selecione as subscrições que pretende e, em seguida, certifique-se de que seleciona **OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Criar um projeto local

Antes de poder criar a sua aplicação lógica, crie um projeto local para que possa gerir e implementar a sua aplicação lógica a partir do Código do Estúdio Visual. O projeto subjacente é semelhante a um projeto Azure Functions, também conhecido como um projeto de aplicação de função. No entanto, estes tipos de projetos são separados uns dos outros, pelo que aplicações lógicas e aplicações de função não podem existir no mesmo projeto.

1. No seu computador, crie uma pasta local *vazia* para usar para o projeto que mais tarde irá criar no Código do Estúdio Visual.

1. No Código do Estúdio Visual, feche todas as pastas abertas.

1. No painel Azure, ao lado de **Azure: Logic Apps (Preview)**, selecione **Create New Project** (ícone que mostra uma pasta e um relâmpago).

   ![Screenshot que mostra a barra de ferramentas do painel Azure com "Create New Project" selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Se o Windows Defender Firewall lhe pedir para conceder acesso à rede para `Code.exe` , que é Código do Estúdio Visual, e para , que é o `func.exe` Azure Functions Core Tools, selecione **redes privadas, como a minha casa ou rede de trabalho** Permitir o **>** **acesso**.

1. Navegue para o local onde criou a pasta do projeto, selecione essa pasta e continue.

   ![Screenshot que mostra a caixa de diálogo "Select Folder" com uma pasta de projeto recém-criada e o botão "Selecione" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. A partir da lista de modelos que aparecem, selecione **o Fluxo de Trabalho Stateful** ou o Fluxo de Trabalho **Apátrida**. Este exemplo seleciona **o Fluxo de Trabalho Stateful**.

   ![Screenshot que mostra a lista de modelos de fluxo de trabalho com "Stateful Workflow" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Forneça um nome para o seu fluxo de trabalho e prima Enter. Este exemplo usa `Fabrikam-Stateful-Workflow` como nome.

   ![Screenshot que mostra a caixa "Criar novo fluxo de trabalho stateful (3/3)" e "Fabrikam-Stateful-Workflow" como o nome do fluxo de trabalho.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   O Visual Studio Code termina a criação do seu projeto e abre o **workflow.jsem** arquivo para o seu fluxo de trabalho.

1. A partir da barra de ferramentas Visual Studio, abra o painel Explorer, se não já estiver aberto.

   O painel Explorer mostra o seu projeto, que agora inclui ficheiros de projeto gerados automaticamente. Por exemplo, o projeto tem uma pasta que mostra o nome do seu fluxo de trabalho. Dentro desta pasta, o **workflow.jsem** ficheiro contém a definição de JSON subjacente ao seu fluxo de trabalho.

   ![Screenshot que mostra o painel explorer com pasta de projeto, pasta de fluxo de trabalho e ficheiro "workflow.jsligado".](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>Abra o ficheiro de definição de fluxo de trabalho no designer

1. Verifique as versões instaladas no seu computador executando este comando:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Se tiver .NET Core SDK 5.x, esta versão poderá impedir-te de abrir a definição de fluxo de trabalho subjacente da aplicação lógica no designer. Em vez de desinstalar esta versão, ao nível da raiz do seu projeto, criar uma **global.jsno** ficheiro que faz referência à versão .NET Core runtime 3.x que tem que é mais tarde do que 3.1.201, por exemplo:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   Certifique-se de que adiciona explicitamente o **global.jsno** ficheiro ao nível de raiz do seu projeto a partir do Código do Estúdio Visual. Caso contrário, o designer não abre.

1. Se estiver a executar o Código do Estúdio Visual no Windows ou linux, certifique-se de que o Emulador de Armazenamento Azure está em funcionamento. Para mais informações, reveja os [Pré-Requisitos.](#prerequisites) Se estiver a executar o Código do Estúdio Visual no macOS, continue para o próximo passo.

1. Expanda a pasta do projeto para o seu fluxo de trabalho. Abra a **workflow.jsno** menu de atalho do ficheiro e selecione Abrir **no Designer**.

   ![Screenshot que mostra painel explorer e janela de atalho para o workflow.jsem arquivo com "Open in Designer" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   Se receber a mensagem de erro de que o `Workflow design time could not be started` , verifique as seguintes condições:

   * **Janelas ou Linux**: Certifique-se de que o Emulador de Armazenamento Azure está em funcionamento. Caso contrário, consulte [problemas e erros de resolução de problemas](#troubleshooting).

   * **macOS**: Experimente a seguinte solução e, se não for bem sucedida, consulte [problemas e erros de resolução de problemas](#troubleshooting).

     1. No seu projeto, abra a **local.settings.jsem** ficheiros, que pode encontrar na pasta raiz do seu projeto e na pasta de tempo de design de fluxo **de trabalho.**

        ![Screenshot que mostra o painel explorer e ficheiros 'local.settings.json' no seu projeto.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

     1. Em cada ficheiro, encontre a `AzureWebJobsStorage` propriedade, por exemplo:

        ```json
        {
           "IsEncrypted": false,
           "Values": {
              "AzureWebJobsStorage": "UseDevelopmentStorage=true",
              "FUNCTIONS_WORKER_RUNTIME": "dotnet"
            }
        }
        ```

      1. Substitua o `AzureWebJobsStorage` valor da propriedade pela cadeia de ligação que guardou anteriormente da sua conta de armazenamento, por exemplo:

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet"
            }
         }
         ```

      1. Guarde as suas alterações e tente reabrir o **workflow.jsno** arquivo no designer.

1. A partir dos **conectores Ativar na lista Azure,** selecione **Utilize conectores da Azure,** que se aplica a todos os conectores geridos que estão disponíveis e implantados no Azure, e não apenas conectores para serviços Azure.

   ![Screenshot que mostra o painel explorer com lista "Enable connectors in Azure" aberto e "Use conectores do Azure" selecionados.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Atualmente, os fluxos de trabalho apátridas suportam apenas *ações* para [conectores geridos](../connectors/apis-list.md#managed-api-connectors), que são implantados em Azure, e não gatilhos. Embora tenha a opção de ativar conectores em Azure para o seu fluxo de trabalho apátrida, o designer não mostra nenhum gatilho de conector gerido para que possa selecionar.

1. A partir da lista **de subscrições Select,** selecione a subscrição Azure para usar para o seu projeto de aplicação lógica.

   ![Screenshot que mostra o painel explorer com a caixa "Select subscription" e a sua subscrição selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. A partir da lista de grupos de recursos, **selecione Criar novo grupo de recursos**.

   ![Screenshot que mostra o painel do Explorer com lista de grupos de recursos e "Criar novo grupo de recursos" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Forneça um nome para o grupo de recursos e prima Enter. Este exemplo utiliza `Fabrikam-Workflows-RG`.

   ![Screenshot que mostra painel explorer e caixa de nome de grupo de recursos.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. A partir da lista de locais, encontre e selecione a região de Azure para utilizar ao criar o seu grupo de recursos e recursos. Este exemplo utiliza **a West Central US.**

   ![Screenshot que mostra o painel explorer com lista de localizações e "West Central US" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Depois de realizar este passo, o Visual Studio Code abre o designer de fluxos de trabalho.

   > [!NOTE]
   > Quando o Código do Estúdio Visual inicia a API de tempo de fluxo de trabalho, aparece uma mensagem de que o arranque pode demorar alguns segundos. Pode ignorar esta mensagem ou selecionar **OK**.

   Depois do designer aparecer, o pedido **de operação Escolha** aparece no designer e é selecionado por padrão, o que mostra o **painel de ação Add.**

   ![Screenshot que mostra o designer de fluxo de trabalho.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Em seguida, [adicione um gatilho e ações](#add-trigger-actions) ao seu fluxo de trabalho.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Adicione um gatilho e ações

Depois de abrir o designer, o pedido **de operação Escolha** aparece no designer e é selecionado por padrão. Pode agora começar a criar o seu fluxo de trabalho adicionando um gatilho e ações.

O fluxo de trabalho neste exemplo utiliza este gatilho e estas ações:

* O gatilho [de pedido](../connectors/connectors-native-reqres.md)incorporado , **Quando é recebido um pedido HTTP**, que recebe chamadas ou pedidos de entrada e cria um ponto final que outros serviços ou aplicações lógicas podem ligar.

* A [ação Do Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), Enviar um **e-mail**.

* A ação [Resposta](../connectors/connectors-native-reqres.md)incorporada, que utiliza para enviar uma resposta e devolver dados ao chamador.

### <a name="add-the-request-trigger"></a>Adicione o gatilho do pedido

1. Ao lado do designer, no painel **de gatilho Adicionar um painel de gatilho,** sob a caixa de pesquisa de **operação,** **certifique-se de** que o Incorporado é selecionado para que possa selecionar um gatilho que é executado de forma nativa.

1. Na caixa de pesquisa de **operação,** insira `when a http request` e selecione o gatilho de pedido incorporado que está nomeado **Quando um pedido HTTP é recebido**.

   ![Screenshot que mostra o designer de fluxo de trabalho e **Adicione um painel de gatilho** com o gatilho "Quando um pedido HTTP é recebido" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Quando o gatilho aparece no designer, o painel de detalhes do gatilho abre-se para mostrar as propriedades, configurações e outras ações do gatilho.

   ![Screenshot que mostra o designer de fluxo de trabalho com o gatilho "Quando um pedido HTTP é recebido" selecionado e detona detalhes abertos.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Se o painel de detalhes não aparecer, certifique-se de que o gatilho é selecionado no designer.

1. Se precisar de eliminar um item do designer, [siga estes passos para eliminar itens do designer.](#delete-from-designer)

### <a name="add-the-office-365-outlook-action"></a>Adicione a ação do Office 365 Outlook

1. No designer, sob o gatilho que adicionou, selecione **Novo passo**.

   O pedido **de operação Escolha** aparece no designer e o **painel de ação Add abre** para que possa selecionar a próxima ação.

1. No painel de ação Adicionar um painel de **ação,** sob a caixa de pesquisa de **operação,** selecione **Azure** para que possa encontrar e selecionar uma ação para um conector gerido que esteja implantado no Azure.

   Este exemplo seleciona e utiliza a ação Do Office 365 Outlook, **Envie um e-mail (V2)**.

   ![Screenshot que mostra o designer de fluxo de trabalho e **Adicione um painel de ação** com a ação do Office 365 Outlook "Envie um e-mail" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. No painel de detalhes da ação, selecione **Iniciar sessão** para que possa criar uma ligação à sua conta de e-mail.

   ![Screenshot que mostra o designer de fluxo de trabalho e **Enviar um painel de e-mail (V2)** com "Iniciar" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Quando o Código do Estúdio Visual lhe pedir o consentimento para aceder à sua conta de e-mail, selecione **Open**.

   ![Screenshot que mostra o pedido do Código do Estúdio Visual para permitir o acesso.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Para evitar futuras solicitações, selecione **Configure Domínios Fidedignos** para que possa adicionar a página de autenticação como um domínio fidedigno.

1. Siga as instruções subsequentes para iniciar singing, permitir o acesso e permitir o regresso ao Código do Estúdio Visual.

   > [!NOTE]
   > Se passar demasiado tempo antes de completar as indicações, o processo de autenticação acaba e falha. Neste caso, volte ao designer e volte a inscrever-se para criar a ligação.

1. Quando a extensão Azure Logic Apps (Preview) lhe pedir o consentimento para aceder à sua conta de e-mail, selecione **Open**. Siga o pedido subsequente para permitir o acesso.

   ![Screenshot que mostra o pedido de extensão de pré-visualização para permitir o acesso.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Para evitar futuras solicitações, selecione **Não peça novamente esta extensão**.

   Depois de o Código do Estúdio Visual criar a sua ligação, alguns conectores mostram a mensagem de que `The connection will be valid for {n} days only` . Este prazo aplica-se apenas à duração enquanto autoriza a sua aplicação lógica no Código do Estúdio Visual. Após a implementação, este limite já não se aplica porque a sua aplicação lógica pode autenticar-se no tempo de execução utilizando a sua [identidade gerida automaticamente atribuída ao sistema.](../logic-apps/create-managed-service-identity.md) Esta identidade gerida difere das credenciais de autenticação ou da cadeia de ligação que utiliza quando cria uma ligação. Se desativar esta identidade gerida atribuída pelo sistema, as ligações não funcionarão em tempo de execução.

1. No designer, se o Enviar uma ação **de e-mail** não aparecer selecionado, selecione essa ação.

1. No painel de pormenores da ação, no separador **Parâmetros,** forneça as informações necessárias para a ação, por exemplo:

   ![Screenshot que mostra o designer de fluxo de trabalho com detalhes para a ação do Office 365 Outlook "Enviar um e-mail".](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Para** | Yes | <*seu endereço de e-mail*> | O destinatário do e-mail, que pode ser o seu endereço de e-mail para efeitos de teste. Este exemplo utiliza o e-mail fictício, `sophiaowen@fabrikam.com` . |
   | **Assunto** | Yes | `An email from your example workflow` | O assunto do e-mail |
   | **Corpo** | Yes | `Hello from your example workflow!` | O conteúdo do corpo de e-mail |
   ||||

   > [!NOTE]
   > Se pretender escoar quaisquer alterações no painel de detalhes no painel **de definições**, **resultado estático** ou separador **Executar depois,** certifique-se de que seleciona **Feito** para escoar essas alterações antes de mudar de separador ou alterar o foco para o designer. Caso contrário, o Código do Estúdio Visual não manterá as alterações. Para mais informações, reveja a [página de Visualização Pública de Aplicações Lógicas conhecidas no GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

1. No designer, **selecione Save**.

> [!IMPORTANT]
> Para executar localmente um fluxo de trabalho que utilize um gatilho ou ações baseados em webhook, como o [gatilho ou ação http webhook incorporado,](../connectors/connectors-native-webhook.md)você deve ativar esta [capacidade, configurando o encaminhamento para o URL de retorno de webhook](#webhook-setup).

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Ativar webhooks de funcionamento local

Quando utiliza um gatilho ou ação baseado em webhook, como **HTTP Webhook,** com uma aplicação lógica em execução em Azure, o tempo de execução das Aplicações Lógicas subscreve o ponto final do serviço, gerando e registando um URL de retorno com esse ponto final. O gatilho ou ação aguarda então que o ponto final de serviço ligue para o URL. No entanto, quando está a trabalhar no Visual Studio Code, o URL de retorno gerado começa por `http://localhost:7071/...` . Este URL é para o seu servidor local, que é privado para que o ponto final de serviço não possa chamar este URL.

Para executar localmente os gatilhos e ações baseados no webhook no Código do Estúdio Visual, é necessário configurar um URL público que exponha o servidor local e encaminhe as chamadas do ponto final do serviço para o URL de retorno webhook. Pode utilizar um serviço de reencaminhamento e uma ferramenta como [**o Ngrok,**](https://ngrok.com/)que abre um túnel HTTP para a porta local, ou pode utilizar a sua própria ferramenta.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Configurar reencaminhamento de chamada usando **ngrok**

1. [Inscreva-se numa conta **Ngrok**](https://dashboard.ngrok.com/signup) se não tiver uma. Caso contrário, [inscreva-se na sua conta.](https://dashboard.ngrok.com/login)

1. Obtenha o seu token de autenticação pessoal, que o seu cliente **ngrok** precisa para ligar e autenticar o acesso à sua conta.

   1. Para encontrar a sua [página de token de autenticação](https://dashboard.ngrok.com/auth/your-authtoken), no menu do painel de instrumentos da sua conta, expanda **a autenticação** e selecione **O Seu Authtoken**.

   1. Da **caixa Authtoken,** copie o símbolo para um local seguro.

1. A partir da [página de descarregamento **ngrok**](https://ngrok.com/download) ou [do painel de instrumentos](https://dashboard.ngrok.com/get-started/setup)da sua conta, descarregue a versão **ngrok** que pretende e extraia o ficheiro .zip. Para mais informações, consulte [o passo 1: Desaperte a roupa para instalar](https://ngrok.com/download).

1. No seu computador, abra a ferramenta de solicitação de comando. Navegue para o local onde tem o ficheiro **ngrok.exe.**

1. Ligue o cliente **ngrok** à sua conta **ngrok** executando o seguinte comando. Para mais informações, consulte [o Passo 2: Ligue a sua conta.](https://ngrok.com/download)

   `ngrok authtoken <your_auth_token>`

1. Abra o túnel HTTP até ao porto local 7071, executando o seguinte comando. Para mais informações, consulte [o Passo 3: Despeça-o.](https://ngrok.com/download)

   `ngrok http 7071`

1. A partir da saída, encontre a seguinte linha:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Copiar e guardar o URL que tem este formato: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>Configurar o URL de encaminhamento nas definições da sua aplicação

1. No Código do Estúdio Visual, ao nível da raiz do seu projeto, abra a **local.settings.jsem** arquivo.

1. No `Values` objeto, adicione uma propriedade `Workflows.WebhookRedirectHostUri` nomeada, e desaje assim o valor para o URL de encaminhamento que criou anteriormente, por exemplo:

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

A primeira vez que inicia uma sessão de depuração local ou execute o fluxo de trabalho sem depuração, o tempo de funcionamento da Logic Apps regista o fluxo de trabalho com o ponto final do serviço e subscreve esse ponto final para notificar as operações do webhook. Da próxima vez que o seu fluxo de trabalho funcionar, o tempo de funcionaamento não se registará ou resubscreverá porque o registo de subscrição já existe no armazenamento local.

Quando para a sessão de depurar para uma execução de fluxo de trabalho que utiliza gatilhos ou ações baseados em webhooks geridos localmente, os registos de subscrição existentes não são eliminados. Para não registar, tem de remover ou eliminar manualmente os registos de subscrição.

> [!NOTE]
> Depois de o seu fluxo de trabalho começar a funcionar, a janela do terminal pode mostrar erros como este exemplo:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> Neste caso, abra a **local.settings.jsem** arquivo ao nível da raiz do seu projeto, e certifique-se de que a propriedade está definida `true` para:
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Gerir pontos de rutura para depuração

Antes de executar e testar o fluxo de trabalho da sua aplicação lógica iniciando uma sessão de [depuração,](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) pode definir pontos de rutura dentro do **workflow.jsem** ficheiro para cada fluxo de trabalho. Não é necessária outra configuração. 

Neste momento, os pontos de rutura são suportados apenas para ações, não para gatilhos. Cada definição de ação tem estes locais de breakpoint:

* Desaponte o ponto de partida na linha que mostra o nome da ação. Quando este breakpoint chegar durante a sessão de depuração, pode rever as entradas da ação antes de serem avaliadas.

* Coloque o ponto de rutura final na linha que mostra a cinta de fecho da ação **(}**. Quando este breakpoint chegar durante a sessão de depuração, pode rever os resultados da ação antes que a ação termine de funcionar.

Para adicionar um ponto de rutura, siga estes passos:

1. Abra a **workflow.jsno** ficheiro para o fluxo de trabalho que pretende depurar.

1. Na linha onde pretende definir o ponto de rutura, na coluna esquerda, selecione dentro dessa coluna. Para remover o ponto de rutura, selecione o ponto de rutura.

   Quando iniciar a sessão de depurar, a vista Run aparece no lado esquerdo da janela de código, enquanto a barra de ferramentas Debug aparece perto da parte superior.

   > [!NOTE]
   > Se a vista 'Executar' não aparecer automaticamente, prima Ctrl+Shift+D.

1. Para rever a informação disponível quando um breakpoint atinge, na vista Run, examine o painel **de Variáveis.**

1. Para continuar a execução do fluxo de trabalho, na barra de ferramentas Debug, selecione **Continue** (botão de reprodução). 

Pode adicionar e remover pontos de rutura a qualquer momento durante o fluxo de trabalho. No entanto, se atualizar o **workflow.jsno** ficheiro após o início da execução, os pontos de rutura não atualizam automaticamente. Para atualizar os pontos de rutura, reinicie a aplicação lógica.

Para obter informações [gerais, consulte Breakpoints - Código de Estúdio Visual](https://code.visualstudio.com/docs/editor/debugging#_breakpoints).

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Corra, teste e depure localmente

Para testar a sua aplicação lógica, siga estes passos para iniciar uma sessão de depuração e encontre o URL para o ponto final criado pelo gatilho Request. Você precisa deste URL para que você possa mais tarde enviar um pedido para esse ponto final.

1. Para depurar mais facilmente um fluxo de trabalho apátrida, pode [permitir a história da execução para esse fluxo de trabalho.](#enable-run-history-stateless)

1. Na Barra de Atividade do Código do Estúdio Visual, abra o menu **Executar** e selecione **Start Debugging** (F5).

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

   | Estado de execução | Description |
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

   | Estado de ação | Ícone | Description |
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

## <a name="return-a-response"></a>Devolva uma resposta

Para retornar uma resposta ao chamador que enviou um pedido para a sua aplicação lógica, pode utilizar a [ação de Resposta](../connectors/connectors-native-reqres.md) incorporada para um fluxo de trabalho que começa com o gatilho Do Pedido.

1. No designer de fluxo de trabalho, no âmbito do Enviar uma ação **de e-mail,** selecione **Novo passo**.

   O pedido **de operação Escolha** aparece no designer e o **painel de ação Add abre** para que possa selecionar a próxima ação.

1. No painel de ação Adicionar um painel de **ação,** sob a caixa de pesquisa de **ação,** **certifique-se de** que o Incorporado está selecionado. Na caixa de pesquisa, insira `response` e selecione a ação **Resposta.**

   ![Screenshot que mostra o designer de fluxo de trabalho com a ação Response selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Quando a ação **Response** aparece no designer, o painel de detalhes da ação abre-se automaticamente.

   ![Screenshot que mostra o designer de fluxo de trabalho com o painel de detalhes da ação "Resposta" aberto e a propriedade "Body" definida para o valor da propriedade "Enviar um e-mail" da ação "Body".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

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

Depois de fazer atualizações para a sua aplicação lógica, pode realizar outro teste repetindo o depurador no Visual Studio e enviando outro pedido para ativar a sua app lógica atualizada, semelhante aos passos em [Run, teste e depurar localmente.](#run-test-debug-locally)

1. Na Barra de Atividade do Código do Estúdio Visual, abra o menu **Executar** e selecione **Start Debugging** (F5).

1. No Carteiro ou na sua ferramenta para criar e enviar pedidos, envie outro pedido para desencadear o seu fluxo de trabalho.

1. Se criou um fluxo de trabalho imponente, na página geral do fluxo de trabalho, verifique o estado da execução mais recente. Para visualizar o estado, entradas e saídas para cada passo nessa corrida, selecione o botão elipses **(...**) para essa execução e selecione **Show Run**.

   Por exemplo, aqui está o estado passo a passo para uma corrida após o fluxo de trabalho da amostra ter sido atualizado com a ação resposta.

   ![Screenshot que mostra o estado de cada passo no fluxo de trabalho atualizado mais as entradas e saídas na ação expandida "Resposta".](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Para parar a sessão de depurar, no menu **Executar,** selecione **Stop Debugging** (Shift + F5).

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Implementar no Azure

A partir do Código do Estúdio Visual, pode publicar diretamente o seu projeto para o Azure, que implementa a sua aplicação lógica utilizando o novo tipo de recurso **Logic App (Preview).** Semelhante ao recurso de aplicação de função em Azure Functions, a implementação para este novo tipo de recurso requer que selecione um [plano de hospedagem e nível de preços](../app-service/overview-hosting-plans.md), que pode configurar durante a implementação. Para obter mais informações sobre planos de hospedagem e preços, reveja estes tópicos:

* [Dimensione um serviço de aplicações Azure](../app-service/manage-scale-up.md)
* [Dimensionamento e alojamento de Funções do Azure](../azure-functions/functions-scale.md)

Pode publicar a sua aplicação lógica como um novo recurso, que cria automaticamente quaisquer recursos necessários adicionais, como uma [conta de Armazenamento Azure, semelhante aos requisitos de aplicações de função.](../azure-functions/storage-considerations.md) Ou, pode publicar a sua aplicação lógica para um recurso de **Aplicação Lógica (Preview)** previamente implantado, que substitui essa aplicação lógica.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>Publicar para um novo recurso Logic App (Preview)

1. Na Barra de Atividade do Código do Estúdio Visual, selecione o ícone Azure.

1. No Azure: Barra de ferramentas **de painéis de aplicações lógicas (preview),** selecione **Implementar para a Aplicação Lógica**.

   ![Screenshot que mostra o painel "Azure: Logic Apps (Preview)" e a barra de ferramentas do painel com "Implementar para a Aplicação Lógica" selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Na lista que o Código do Estúdio Visual abre, selecione a partir destas opções:

   * **Criar nova Aplicação Lógica (Pré-visualização) em Azure** (rápido)
   * **Criar nova App Lógica (Pré-visualização) em Azure Advanced**
   * Um recurso **de Aplicação Lógica (Preview)** previamente implantado, se existir

   Este exemplo continua com **a Criação de nova App Lógica (Preview) no Azure Advanced**.

   ![Screenshot que mostra o painel "Azure: Logic Apps (Preview)" com uma lista com "Create new Logic App (Preview) in Azure" selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Para criar o seu novo recurso **Logic App (Preview),** siga estes passos:

   1. Forneça um nome globalmente único para a sua nova aplicação lógica, que é o nome a usar para o recurso **Logic App (Preview).** Este exemplo utiliza `Fabrikam-Workflows-App`.

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

   1. Se as configurações de criação e implementação da sua aplicação lógica suportam usando [o Application Insights,](../azure-monitor/app/app-insights-overview.md)pode opcionalmente permitir o registo de diagnósticos e o rastreio da sua aplicação lógica. Pode fazê-lo quando implementar a sua aplicação lógica a partir do Código do Estúdio Visual ou após a implementação. Precisa de ter uma instância de Insights de Aplicação, mas pode criar este recurso [com antecedência](../azure-monitor/app/create-workspace-resource.md), quando implementa a sua aplicação lógica ou após a implementação.

      Para ativar o registo e o rastreio agora, siga estes passos:

      1. Selecione um recurso de Insights de Aplicação existente ou **Crie um novo recurso de Insights de Aplicação**.

      1. No [portal Azure, aceda](https://portal.azure.com)ao seu recurso Application Insights.

      1. No menu de recursos, selecione **Overview**. Encontre e copie o valor **da chave de instrumentação.**

      1. No Código do Estúdio Visual, ao nível da raiz do seu projeto, abra a **local.settings.jsem** arquivo.

      1. No `Values` objeto, adicione a `APPINSIGHTS_INSTRUMENTATIONKEY` propriedade e desajei o valor à chave de instrumentação, por exemplo:

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > Pode verificar se os nomes do gatilho e da ação aparecem corretamente na sua instância De Insights de Aplicação.
         >
         > 1. No portal Azure, aceda ao seu recurso Application Insights.
         >
         > 2. No menu de recursos, em **'Investigar',** selecione **Mapa de Aplicação**.
         >
         > 3. Reveja os nomes de operação que aparecem no mapa.
         >
         > Alguns pedidos de entrada de gatilhos incorporados podem aparecer como duplicados no Mapa de Aplicações. 
         > Em vez de utilizarem o `WorkflowName.ActionName` formato, estes duplicados utilizam o nome do fluxo de trabalho como nome de funcionamento e são originários do anfitrião Azure Functions.

      1. Em seguida, pode ajustar opcionalmente o nível de gravidade para os dados de rastreio que a sua aplicação lógica recolhe e envia para a sua instância De Insights de Aplicação.

         Cada vez que um evento relacionado com o fluxo de trabalho acontece, como quando um fluxo de trabalho é desencadeado ou quando uma ação é executada, o tempo de execução emite vários vestígios. Estes vestígios cobrem a vida útil do fluxo de trabalho e incluem, mas não se limitam a, os seguintes tipos de eventos:

         * Atividade de serviço, como início, paragem e erros.
         * Empregos e atividade de despachantes.
         * Atividade de fluxo de trabalho, como gatilho, ação e execução.
         * Atividade de pedido de armazenamento, como sucesso ou falha.
         * HTTP solicitar atividade, como entrada, saída, sucesso e insucesso.
         * Vestígios de desenvolvimento ad hoc, como mensagens de depurar.

         Cada tipo de evento é atribuído a um nível de gravidade. Por exemplo, o `Trace` nível captura as mensagens mais detalhadas, enquanto o `Information` nível captura a atividade geral no seu fluxo de trabalho, como quando a sua aplicação lógica, fluxo de trabalho, gatilho e ações começam e param. Esta tabela descreve os níveis de gravidade e os seus tipos de vestígios:

         | Nível de gravidade | Tipo de traço |
         |----------------|------------|
         | Crítico | Registos que descrevem uma falha irrecuperável na sua aplicação lógica. |
         | Depurar | Registos que pode utilizar para investigação durante o desenvolvimento, por exemplo, chamadas HTTP de entrada e saída. |
         | Erro | Registos que indicam uma falha na execução do fluxo de trabalho, mas não uma falha geral na sua aplicação lógica. |
         | Informações | Registos que rastreiam a atividade geral na sua aplicação lógica ou fluxo de trabalho, por exemplo: <p><p>- Quando um gatilho, ação ou corrida começa e termina. <br>- Quando a sua aplicação lógica começa ou termina. |
         | Rastreio | Registos que contêm as mensagens mais detalhadas, por exemplo, pedidos de armazenamento ou atividade de despachante, além de todas as mensagens relacionadas com a atividade de execução do fluxo de trabalho. |
         | Aviso | Registos que realçam um estado anormal na sua aplicação lógica, mas não impedem o seu funcionamento. |
         |||

         Para definir o nível de gravidade, ao nível da raiz do seu projeto, abra a **host.jsno** ficheiro e encontre o `logging` objeto. Este objeto controla a filtragem de registos para todos os fluxos de trabalho da sua aplicação lógica e segue o [layout ASP.NET Core para filtragem do tipo de registo](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering).

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         Se o `logging` objeto não contiver um `logLevel` objeto que inclua a `Host.Triggers.Workflow` propriedade, adicione esses itens. Desacione a propriedade ao nível de severidade para o tipo de vestígio que deseja, por exemplo:

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

   Quando termina as etapas de implementação, o Visual Studio Code começa a criar e a implementar os recursos necessários para a publicação da sua aplicação lógica.

1. Para rever e monitorizar o processo de implementação, no menu **Ver,** selecione **Output**. A partir da lista de barras de ferramentas da janela de saída, selecione **Azure Logic Apps**.

   ![Screenshot que mostra a janela de saída com as "Azure Logic Apps" selecionadas na lista de barras de ferramentas juntamente com o progresso e o estado de implementação.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Quando o Código do Estúdio Visual terminar de implementar a sua aplicação lógica para o Azure, aparece a seguinte mensagem:

   ![Screenshot que mostra uma mensagem que a implementação para Azure completou com sucesso.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Parabéns, a sua aplicação lógica está agora ao vivo no Azure e ativada por padrão.

Em seguida, pode aprender a executar estas tarefas:

* [Adicione um fluxo de trabalho em branco ao seu projeto.](#add-workflow-existing-project)

* [Gerir aplicações lógicas implementadas no Código do Estúdio Visual](#manage-deployed-apps-vs-code) ou utilizando o portal [Azure](#manage-deployed-apps-portal).

* [Permitir a história da execução em fluxos de trabalho apátridas.](#enable-run-history-stateless)

* [Permitir visualizar a visualização no portal Azure para uma aplicação lógica implementada](#enable-monitoring).

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Adicione fluxo de trabalho em branco ao projeto

Pode ter vários fluxos de trabalho no seu projeto de aplicações lógicas. Para adicionar um fluxo de trabalho em branco ao seu projeto, siga estes passos:

1. Na Barra de Atividade do Código do Estúdio Visual, selecione o ícone Azure.

1. No painel Azure, ao lado do **Azure: Logic Apps (Preview)**, selecione **Create Workflow** (ícone para Apps Lógicas Azure).

1. Selecione o tipo de fluxo de trabalho que pretende adicionar, **Stateful** ou **Stateless**.

1. Forneça um nome para o seu fluxo de trabalho.

Quando terminar, uma nova pasta de fluxo de trabalho aparece no seu projeto juntamente com uma **workflow.jsem** arquivo para a definição de fluxo de trabalho.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Gerir aplicativos lógicos implementados no Código do Estúdio Visual

No Código do Estúdio Visual, pode ver todas as aplicações lógicas implementadas na sua subscrição Azure, sejam as **aplicações lógicas** originais ou o tipo de recurso **Logic App (Preview)** e selecionar tarefas que o ajudam a gerir essas aplicações lógicas. No entanto, para aceder a ambos os tipos de recursos, precisa tanto das **aplicações Azure Logic Apps** como das **extensões Azure Logic Apps (Preview)** para o Código do Estúdio Visual.

1. Na barra de ferramentas esquerda, selecione o ícone Azure. No painel **Azure: Logic Apps (Preview),** expanda a sua subscrição, que mostra todas as aplicações lógicas implementadas para essa subscrição.

1. Encontre e selecione a aplicação lógica que pretende gerir. Abra o menu de atalho da aplicação lógica e selecione a tarefa que pretende executar.

   Por exemplo, pode selecionar tarefas como parar, iniciar, reiniciar ou eliminar a aplicação lógica implementada.

   ![Screenshot que mostra Visual Studio Code com o painel de extensão "Azure Logic Apps (Preview)" e o fluxo de trabalho implantado.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Para ver todos os fluxos de trabalho na aplicação lógica, expanda a sua aplicação lógica e, em seguida, expanda o nó **workflows.**

1. Para visualizar um fluxo de trabalho específico, abra o menu de atalho do fluxo de trabalho e selecione **Open in Designer**, que abre o fluxo de trabalho no modo apenas de leitura.

   Para editar o fluxo de trabalho, tem estas opções:

   * No Visual Studio Code, abra asworkflow.jsdo seu projeto em arquivo **no** designer de fluxo de trabalho, faça as suas edições e reimplante a sua aplicação lógica para a Azure.

   * No portal Azure, [encontre e abra a sua aplicação lógica.](#manage-deployed-apps-portal) Encontre, edite e guarde o fluxo de trabalho.

1. Para abrir a aplicação lógica implementada no portal Azure, abra o menu de atalho da aplicação lógica e selecione **Abrir no Portal**.

   O portal Azure abre no seu navegador, assina-o automaticamente no portal se estiver a assinar o Código do Estúdio Visual e mostrar a sua aplicação lógica.

   ![Screenshot que mostra a página do portal Azure para a sua aplicação lógica no Código do Estúdio Visual.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Também pode iniciar sação separadamente no portal Azure, utilizar a caixa de pesquisa do portal para encontrar a sua aplicação lógica e, em seguida, selecionar a sua aplicação lógica na lista de resultados.

   ![Screenshot que mostra o portal Azure e a barra de pesquisa com resultados de pesquisa para aplicação lógica implementada, que aparece selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Gerir aplicações lógicas implementadas no portal

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

   O designer de workflow abre e mostra o fluxo de trabalho que construiu no Código do Estúdio Visual. Pode agora fazer alterações a este fluxo de trabalho no portal Azure.

   ![Screenshot que mostra o designer de fluxo de trabalho e fluxo de trabalho implementado a partir do Código do Estúdio Visual.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Adicione mais um fluxo de trabalho no portal

Através do portal Azure, pode adicionar fluxos de trabalho em branco a um recurso **da Logic App (Preview)** que implementou a partir do Código do Estúdio Visual e construir esses fluxos de trabalho no portal Azure.

1. No [portal Azure,](https://portal.azure.com)encontre e selecione o recurso **'Preview'** da Sua Aplicação Lógica implementada.

1. No menu de aplicativos lógicos, selecione **Workflows**. No painel **workflows,** **selecione Adicionar**.

   ![Screenshot que mostra o painel de "Workflows" e a barra de ferramentas selecionadas pela aplicação lógica selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. No **painel de trabalho Novo,** forneça o nome para o fluxo de trabalho. Selecione **Stateful** ou **Stateless** **>** **Create**.

   Depois de o Azure implementar o seu novo fluxo de trabalho, que aparece no painel **workflows,** selecione o fluxo de trabalho para que possa gerir e executar outras tarefas, tais como a abertura do designer ou a visualização de código.

   ![Screenshot que mostra o fluxo de trabalho selecionado com opções de gestão e revisão.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Por exemplo, a abertura do designer para um novo fluxo de trabalho mostra uma tela em branco. Agora pode construir este fluxo de trabalho no portal Azure.

   ![Screenshot que mostra o designer de fluxo de trabalho e um fluxo de trabalho em branco.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Permitir a história de execução para fluxos de trabalho apátridas

Para depurar mais facilmente um fluxo de trabalho apátrida, pode ativar a história de execução para esse fluxo de trabalho e, em seguida, desativar o histórico de execução quando terminar. Siga estes passos para o Código do Estúdio Visual, ou se estiver a trabalhar no portal Azure, consulte [Criar fluxos de trabalho imponentes e apátridas no portal Azure](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless).

1. No seu projeto Visual Studio Code, expanda a pasta **de tempo de design de fluxo de trabalho** e abra alocal.settings.js **em** arquivo.

1. Adicione o `Workflows.{yourWorkflowName}.operationOptions` imóvel e desateia o valor `WithStatelessRunHistory` para, por exemplo:

   **Janelas ou Linux**

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

   **macOS**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Para desativar o histórico de execução quando terminar, ou desativar a `Workflows.{yourWorkflowName}.OperationOptions` propriedade ou apagar o `None` imóvel e o seu valor.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Permitir visualização de monitorização no portal Azure

Depois de implementar um recurso **de Aplicação Lógica (Preview)** do Código do Estúdio Visual para OZure, pode rever qualquer histórico de execução disponível e detalhes para um fluxo de trabalho nesse recurso utilizando o portal Azure e a experiência **Monitor** para esse fluxo de trabalho. No entanto, primeiro tem de ativar a capacidade de visualização do **Monitor** nesse recurso de aplicação lógica.

1. No [portal Azure,](https://portal.azure.com)encontre e selecione o recurso **'Preview'** da App Lógica implementada.

1. No menu desse recurso, em **API,** selecione **CORS**.

1. No painel **CORS,** em **"Origens Permitidas",** adicione o caractere wildcard (*).

1. Quando terminar, na barra de **ferramentas CORS,** **selecione Save**.

   ![Screenshot que mostra o portal Azure com um recurso de Aplicação Lógica (Preview) implantado. No menu de recursos, "CORS" é selecionado com uma nova entrada para "Origens Permitidas" definida para o caracteres wildcard "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Ativar ou abrir Insights de Aplicação após a implementação

Durante a execução do fluxo de trabalho, a sua aplicação lógica emite telemetria juntamente com outros eventos. Você pode usar esta telemetria para obter uma melhor visibilidade sobre como o seu fluxo de trabalho funciona bem e como o tempo de funcionamento das Apps Lógicas funciona de várias maneiras. Pode monitorizar o seu fluxo de trabalho utilizando [o Application Insights,](../azure-monitor/app/app-insights-overview.md)que fornece telemetria em tempo real (métricas vivas). Esta capacidade pode ajudá-lo a investigar falhas e problemas de desempenho mais facilmente quando utiliza estes dados para diagnosticar problemas, configurar alertas e construir gráficos.

Se as configurações de criação e implementação da sua aplicação lógica suportam usando [o Application Insights,](../azure-monitor/app/app-insights-overview.md)pode opcionalmente permitir o registo de diagnósticos e o rastreio da sua aplicação lógica. Pode fazê-lo quando implementar a sua aplicação lógica a partir do Código do Estúdio Visual ou após a implementação. Precisa de ter uma instância de Insights de Aplicação, mas pode criar este recurso [com antecedência](../azure-monitor/app/create-workspace-resource.md), quando implementa a sua aplicação lógica ou após a implementação.

Para ativar o Application Insights numa aplicação lógica implementada ou para rever os dados do Application Insights quando já está ativado, siga estes passos:

1. No portal Azure, encontre a sua aplicação lógica implementada.

1. No menu de aplicativos lógicos, em **Definições,** selecione **Application Insights**.

1. Se os Insights de Aplicação não estiverem ativados, no painel **de Insights de Aplicação,** selecione **Ligue os Insights de Aplicação**. Depois de atualizar o painel, na parte inferior, **selecione Aplicar**.

   Se os Insights de Aplicação estiverem ativados, no painel **de Insights de Aplicação,** selecione **Ver Dados de Insights de Aplicação**.

Após a abertura do Application Insights, pode rever várias métricas para a sua aplicação lógica. Para obter mais informações, consulte [Azure Logic Apps Running Anywhere - Monitor with Application Insights - parte 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849).

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Implementar para Docker

Pode implementar a sua aplicação lógica num [recipiente Docker](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) como ambiente de hospedagem utilizando o [.NET CLI](/dotnet/core/tools/). Com estes comandos, pode construir e publicar o projeto da sua aplicação lógica. Em seguida, pode construir e executar o seu contentor Docker como destino para implementar a sua aplicação lógica.

Se não está familiarizado com o Docker, reveja estes tópicos:

* [O que é o Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [Introdução a Contentores e Estivador](/dotnet/architecture/microservices/container-docker-introduction/)
* [Introdução a .NET e Docker](/dotnet/core/docker/introduction)
* [Contentores, imagens e registos](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [Tutorial: Começa com o Docker (Código do Estúdio Visual)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>Requisitos

* A conta de Armazenamento Azure que a sua aplicação lógica utiliza para implantação

* Um ficheiro Docker para um fluxo de trabalho .NET que usa ao construir o seu contentor Docker

   Por exemplo, esta amostra Ficheiro Docker implementa uma aplicação lógica com um fluxo de trabalho imponente. O ficheiro especifica a cadeia de ligação e a chave de acesso para a conta Azure Storage que foi utilizada para a publicação da aplicação lógica para o portal Azure.

   ```text
   FROM mcr.microsoft.com/dotnet/core/sdk3.1 AS installer-env

   COPY . /src/dotnet-function-app
   RUN cd /src/dotnet-function-app && \
       mkdir -p /home/site/wwwroot && \
       dotnet publish *.csproj --output /home/site/wwwroot

   FROM mcr.microsoft.com/azure-functions/dotnet:3.0
   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
   ```

   Para obter mais informações, consulte [as melhores práticas para escrever ficheiros Docker](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

### <a name="build-and-publish-your-app"></a>Construa e publique a sua app

1. Para construir o projeto da sua aplicação lógica localmente, abra um pedido de linha de comando e execute este comando:

   `dotnet build -c release`

   Para mais informações, consulte a página de referência [da construção do pontonet.](/dotnet/core/tools/dotnet-build/)

1. Publique a construção do seu projeto numa pasta para utilizar para implantação no ambiente de hospedagem, executando este comando:

   `dotnet publish -c release`

   Para mais informações, consulte a página de referência da [publicação do dotnet.](/dotnet/core/tools/dotnet-publish/)

### <a name="access-to-your-storage-account"></a>Acesso à sua conta de armazenamento

Antes de construir e executar o seu recipiente Docker, precisa de obter a cadeia de ligação que contém as chaves de acesso à sua conta de armazenamento.

1. No portal Azure, no menu da conta de armazenamento, em **Definições**, selecione **Teclas de acesso**. 

   ![Screenshot que mostra o portal Azure com chaves de acesso à conta de armazenamento e fio de ligação copiado.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

1. No **fio De Ligação,** copie a cadeia de ligação da sua conta de armazenamento. A cadeia de ligação é semelhante a esta amostra:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey={access-key};EndpointSuffix=core.windows.net`

   Para obter mais informações, [reveja gerir as chaves da conta de armazenamento](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

1. Guarde a cadeia de ligação em algum lugar seguro. No seu projeto de aplicação lógica, precisa adicionar esta cadeia a ambos **oslocal.settings.jsem** ficheiros. Também tens de adicionar esta corda ao teu ficheiro Docker.

### <a name="build-and-run-your-docker-container-image"></a>Construa e execute a sua imagem de contentor Docker

1. Construa a sua imagem de contentor Docker utilizando o seu ficheiro Docker e executando este comando:

   `docker build --tag local/workflowcontainer .`

   Para mais informações, consulte [a construção do docker.](https://docs.docker.com/engine/reference/commandline/build/)

1. Guarde a cadeia em algum lugar seguro para que possa adicionar a cadeia ao **local.settings.jsem** ficheiros do projeto que utiliza para criar a sua aplicação lógica no Código do Estúdio Visual.

1. Executar o recipiente localmente utilizando este comando:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Para mais informações, consulte [o estivador.](https://docs.docker.com/engine/reference/commandline/run/)

### <a name="get-callback-url-for-request-trigger"></a>Obtenha URL de retorno para pedido de gatilho

Para obter o URL de retorno para o gatilho do pedido, envie este pedido:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

O valor de> *de <-chave principal* é definido na conta de Armazenamento Azure que definiu `AzureWebJobsStorage` no ficheiro, **azure-webjobs-secrets/{deployment-name}/host.jsem**, onde pode encontrar o valor nesta secção:

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

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Eliminar itens do designer

Para eliminar um item no seu fluxo de trabalho do designer, siga qualquer um destes passos:

* Selecione o item, abra o menu de atalho do item (Shift+F10) e selecione **Delete**. Para confirmar, selecione **OK**.

* Selecione o item e prima a tecla de eliminação. Para confirmar, selecione **OK**.

* Selecione o item de modo a que os detalhes abram o painel para esse item. No canto superior direito do painel, abra o menu de elipses **(...**) e selecione **Delete**. Para confirmar, selecione **OK**.

  ![Screenshot que mostra um item selecionado no designer com o painel de detalhes abertos mais o botão de elipses selecionado e o comando "Delete".](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Se o menu de elipses não for visível, expanda a janela do Código do Estúdio Visual suficientemente larga para que o painel de detalhes mostre o botão elipses **(...**) no canto superior direito.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Erros e problemas de resolução de problemas

<a name="designer-fails-to-open"></a>

### <a name="opening-designer-fails-with-error-workflow-design-time-could-not-be-started"></a>Designer de abertura falha com erro: "O tempo de design do fluxo de trabalho não pôde ser iniciado"

1. No Código do Estúdio Visual, abra a janela de saída. No menu **Ver,** selecione **Output**.

1. A partir da lista na barra de título da janela Output, selecione **Azure Logic Apps (Preview)** para que possa rever a saída a partir da extensão, por exemplo:

   ![Screenshot que mostra a janela de saída com "Azure Logic Apps" selecionados.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

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

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Faltam novos gatilhos e ações do selecionador de designers para fluxos de trabalho previamente criados

A Azure Logic Apps Preview suporta ações incorporadas para operações de função Azure, Operações Líquidas e Operações XML, tais como **validação XML** e **Transform XML**. No entanto, para aplicações lógicas previamente criadas, estas ações podem não aparecer no selecionador de designers para selecionar se o Visual Studio Code utiliza uma versão desatualizada do pacote de extensão, `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Além disso, o conector e as ações **das Operações de Função Azure** não aparecem no picker do designer a não ser que tenha ativado ou selecionado **os conectores de utilização do Azure** quando criou a sua aplicação lógica. Se não ativou os conectores implantados no Azure na hora de criação de aplicações, pode habilirá-los a partir do seu projeto no Código do Estúdio Visual. Abra a **workflow.jsno** menu de atalho e selecione **Use Connectors do Azure**.

Para corrigir o pacote desatualizado, siga estes passos para eliminar o pacote desatualizado, o que faz com que o Visual Studio Code atualize automaticamente o pacote de extensão para a versão mais recente.

> [!NOTE]
> Esta solução aplica-se apenas às aplicações lógicas que cria e implementa utilizando o Código do Estúdio Visual com a extensão Azure Logic Apps (Preview), e não as aplicações lógicas que criou utilizando o portal Azure. Veja [os gatilhos suportados e faltam ações do designer no portal Azure](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions).

1. Guarde qualquer trabalho que não queira perder e feche o Visual Studio.

1. No seu computador, navegue para a seguinte pasta, que contém pastas em versão para o pacote existente:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Elimine a pasta da versão para o pacote anterior, por exemplo, se tiver uma pasta para a versão 1.1.3, elimine essa pasta.

1. Agora, navegue para a seguinte pasta, que contém pastas em versão para o pacote NuGet necessário:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Elimine a pasta da versão para o pacote anterior, por exemplo, se tiver uma pasta para a versão 1.0.0.8 pré-visualização, elimine essa pasta.

1. Reabre o Código do Estúdio Visual, o seu projeto e o **workflow.jsarquivado no** designer.

Os gatilhos e ações desaparecidos aparecem agora no designer.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>"400 Mau Pedido" aparece num gatilho ou ação

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

Gostaríamos de ouvir falar de si sobre as suas experiências com a extensão Azure Logic Apps (Preview).

* Para bugs ou problemas, [crie os seus problemas no GitHub](https://github.com/Azure/logicapps/issues).
* Para perguntas, pedidos, comentários e outros comentários, [utilize este formulário de feedback](https://aka.ms/lafeedback).