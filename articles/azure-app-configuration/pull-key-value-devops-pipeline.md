---
title: Puxe configurações da configuração da aplicação do ODese com Pipelines Azure
description: Aprenda a usar pipelines Azure para puxar valores-chave para uma Loja de Configuração de Aplicações
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 7bd163781203a277f4c9d6866a156c11e4d5d520
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979577"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Puxe as definições para configuração de aplicativos com gasodutos Azure

A tarefa [de Configuração de Aplicações Azure](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) retira valores-chave da sua loja de configuração de aplicações e define-os como variáveis de gasoduto Azure, que podem ser consumidas por tarefas subsequentes. Esta tarefa complementa a tarefa [Azure App Configuration Push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) que empurra valores-chave de um ficheiro de configuração para a sua loja de Configuração de Aplicações. Para obter mais informações, consulte [as definições push para configuração de aplicações com pipelines Azure](push-kv-devops-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- App Configuration store - crie uma gratuitamente no [portal Azure](https://portal.azure.com).
- Projeto Azure DevOps - [crie um de graça](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Tarefa de Configuração de AplicativoS Azure - descarregue gratuitamente a partir do [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.).  

## <a name="create-a-service-connection"></a>Criar uma ligação de serviço

Uma [ligação](/azure/devops/pipelines/library/service-endpoints) de serviço permite-lhe aceder a recursos na sua subscrição Azure a partir do seu projeto Azure DevOps.

1. Em Azure DevOps, vá ao projeto que contenha o seu pipeline alvo e abra as **definições** do Projeto na parte inferior esquerda.
1. Em **Pipelines** selecione **ligações de serviço**.
1. Se não tiver nenhuma ligação de serviço existente, clique no botão **de ligação de serviço Criar** no meio do ecrã. Caso contrário, clique em **Nova ligação de serviço** no direito superior da página.
1. Selecione **Azure Resource Manager**.
1. Selecione **o principal do serviço (automático)**.
1. Preencha a sua subscrição e recurso. Dê um nome à sua ligação de serviço.

Agora que a sua ligação de serviço foi criada, encontre o nome do principal de serviço que lhe foi atribuído. Vai adicionar uma nova tarefa a este diretor de serviço no próximo passo.

1. Aceda às ligações de serviço **de definições**  >  **de projeto**.
1. Selecione a ligação de serviço que criou na secção anterior.
1. **Selecione Manage Service Principal**.
1. Note o **nome do Visor** listado.

## <a name="add-role-assignment"></a>Adicionar atribuição de função

Atribua a função adequada de Configuração de Aplicação à ligação de serviço que está a ser utilizada na tarefa para que a tarefa possa aceder à loja de Configuração de Aplicações.

1. Navegue para a sua loja de configuração de aplicativos alvo. Para obter uma posição de configuração de uma loja de configuração de aplicações, consulte Criar uma loja de configuração de [aplicações](./quickstart-dotnet-core-app.md#create-an-app-configuration-store) numa das quickstarts de configuração de aplicações Azure.
1. À esquerda, selecione **Access control (IAM)**.
1. No topo, selecione **+ Adicionar** e escolher Adicionar a atribuição **de funções**.
1. Em **Função**, selecione **App Configuration Data Reader**. Esta função permite que a tarefa seja lida a partir da loja de Configuração de Aplicações. 
1. Selecione o principal serviço associado à ligação de serviço que criou na secção anterior.

> [!NOTE]
> Para resolver referências do Azure Key Vault dentro da Configuração da Aplicação, a ligação de serviço também deve ser autorizada a ler segredos nos cofres de chave Azure referenciados.
  
## <a name="use-in-builds"></a>Uso em construções

Esta secção abrangerá como utilizar a tarefa de Configuração de Aplicação Azure num pipeline de construção de Azure DevOps.

1. Navegue para a página do gasoduto de construção clicando em  >  **Pipelines Pipelines**. Para obter documentação sobre o gasoduto, consulte [Criar o seu primeiro oleoduto.](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser)
      - Se estiver a criar um novo oleoduto de construção, clique em **Novo oleoduto,** selecione o repositório para o seu oleoduto. Selecione **o assistente** do Show no lado direito do pipeline e procure a tarefa de Configuração da **Aplicação Azure.**
      - Se estiver a utilizar um pipeline de construção existente, **selecione Editar** para editar o pipeline. No separador **Tarefas,** procure a Tarefa de Configuração da **Aplicação Azure.**
1. Configure os parâmetros necessários para a tarefa de retirar os valores-chave da loja de Configuração de Aplicações. As descrições dos parâmetros estão disponíveis na secção **parâmetros** abaixo e nas pontas das ferramentas junto a cada parâmetro.
      - Desa esta medida de **subscrição Azure** ao nome da ligação de serviço que criou num passo anterior.
      - Desaprote o **nome de Configuração** da Aplicação para o nome de recurso da sua loja de Configuração de Aplicações.
      - Deixe os valores predefinidos para os parâmetros restantes.
1. Poupe e faça fila. O registo de construção apresentará quaisquer falhas que ocorreram durante a execução da tarefa.

## <a name="use-in-releases"></a>Utilização em lançamentos

Esta secção abrangerá como utilizar a tarefa de Configuração de Aplicação Azure num oleoduto de lançamento do Azure DevOps.

1. Navegue para lançar a página do gasoduto selecionando **lançamentos** de  >  **pipelines**. Para obter a documentação do gasoduto de libertação, consulte [os gasodutos release](/azure/devops/pipelines/release).
1. Escolha um gasoduto de desbloqueio existente. Se não tiver um, clique em **Novo oleoduto** para criar um novo.
1. Selecione o botão **Editar** no canto superior direito para editar o pipeline de desbloqueio.
1. Escolha o **Palco** para adicionar a tarefa. Para obter mais informações sobre etapas, consulte [Etapas de adicionar, dependências, condições &](/azure/devops/pipelines/release/environments).
1. Clique **+** em "Executar o agente", em seguida, adicione a tarefa de Configuração da **Aplicação Azure** no separador **'Adicionar tarefas'.**
1. Configure os parâmetros necessários na tarefa de retirar os valores-chave da sua loja de Configuração de Aplicações. As descrições dos parâmetros estão disponíveis na secção **parâmetros** abaixo e nas pontas das ferramentas junto a cada parâmetro.
      - Desa esta medida de **subscrição Azure** ao nome da ligação de serviço que criou num passo anterior.
      - Desaprote o **nome de Configuração** da Aplicação para o nome de recurso da sua loja de Configuração de Aplicações.
      - Deixe os valores predefinidos para os parâmetros restantes.
1. Poupe e faça fila para uma libertação. O registo de desbloqueio apresentará quaisquer falhas encontradas durante a execução da tarefa.

## <a name="parameters"></a>Parâmetros

Os seguintes parâmetros são utilizados pela tarefa de Configuração da Aplicação Azure:

- **Subscrição Azure**: Uma entrega contendo as suas ligações de serviço Azure disponíveis. Para atualizar e atualizar a sua lista de ligações de serviço Azure disponíveis, prima o botão **de subscrição Refresh Azure** à direita da caixa de texto.
- **Nome de configuração da aplicação**: Um drop-down que carrega as suas lojas de configuração disponíveis sob a subscrição selecionada. Para atualizar e atualizar a sua lista de lojas de configuração disponíveis, prima o botão 'Atualizar o **Nome de Configuração da aplicação'** à direita da caixa de texto.
- **Filtro chave**: O filtro pode ser utilizado para selecionar quais os valores-chave solicitados a partir da Configuração da Aplicação Azure. Um valor de * irá selecionar todos os valores-chave. Para obter mais informações, consulte [valores-chave de consulta](concept-key-value.md#query-key-values).
- **Etiqueta**: Especifica qual a etiqueta que deve ser utilizada ao selecionar valores-chave na loja de configuração de aplicações. Se não for fornecida nenhuma etiqueta, os valores-chave sem a etiqueta sem rótulo serão recuperados. Não são permitidos os seguintes caracteres: * .
- **Prefixo da chave de corte**: Especifica um ou mais prefixos que devem ser aparados a partir das teclas de Configuração da Aplicação antes de os definir como variáveis. Vários prefixos podem ser separados por um personagem de nova linha.

## <a name="use-key-values-in-subsequent-tasks"></a>Utilize valores-chave em tarefas subsequentes

Os valores-chave que são recolhidos a partir da Configuração de Aplicações são definidos como variáveis de pipeline, que são acessíveis como variáveis ambientais. A chave da variável ambiente é a chave do valor-chave que é recuperado a partir da Configuração da Aplicação após aparar o prefixo, se especificado.

Por exemplo, se uma tarefa subsequente executa um script PowerShell, pode consumir um valor-chave com a chave 'myBuildSetting' como esta:
```powershell
echo "$env:myBuildSetting"
```
E o valor será impresso na consola.

> [!NOTE]
> As referências do Cofre de Chaves Azure dentro da Configuração da Aplicação serão resolvidas e definidas como [variáveis secretas.](/azure/devops/pipelines/process/variables#secret-variables) Nos oleodutos Azure, as variáveis secretas são mascaradas do tronco. Não são passadas para tarefas como variáveis ambientais e devem, em vez disso, ser aprovadas como inputs. 

## <a name="troubleshooting"></a>Resolução de problemas

Se ocorrer um erro inesperado, os registos de depurados podem ser ativados definindo a variável do gasoduto `system.debug` para `true` .

## <a name="faq"></a>FAQ

**Como compono a minha configuração a partir de várias teclas e etiquetas?**

Há momentos em que a configuração pode ter de ser composta a partir de várias etiquetas, por exemplo, predefinidos e dev. Várias tarefas de Configuração de Aplicações podem ser usadas num único oleoduto para implementar este cenário. Os valores-chave recolhidos por uma tarefa num passo posterior substituirão quaisquer valores dos passos anteriores. No exemplo acima referido, pode ser utilizada uma tarefa para selecionar valores-chave com o rótulo predefinido, enquanto uma segunda tarefa pode selecionar valores-chave com o rótulo dev. As teclas com a etiqueta dev sobrepõem-se às mesmas teclas com a etiqueta predefinida.
