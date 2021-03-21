---
title: Push configurações para configuração de aplicativos com pipelines Azure
description: Aprenda a usar pipelines Azure para empurrar valores-chave para uma Loja de Configuração de Aplicações
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 02/23/2021
ms.author: alkemper
ms.openlocfilehash: 7d343e07414dd1c3f9786c1684eb6f14d5f45e51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101718187"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Push configurações para configuração de aplicativos com pipelines Azure

A tarefa [Azure App Configuration Push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) empurra os valores-chave de um ficheiro de configuração para a sua loja de Configuração de Aplicações. Esta tarefa permite a funcionalidade de círculo completo dentro do pipeline, uma vez que agora é capaz de puxar as definições da loja de Configuração de Aplicações, bem como empurrar as definições para a loja de Configuração de Aplicações.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- Recurso de Configuração de aplicativos - crie um gratuitamente no [portal Azure](https://portal.azure.com).
- Projeto Azure DevOps - [crie um de graça](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure App Configuration Push task - descarregue gratuitamente a partir do [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push).

## <a name="create-a-service-connection"></a>Criar uma ligação de serviço

Uma [ligação](/azure/devops/pipelines/library/service-endpoints) de serviço permite-lhe aceder a recursos na sua subscrição Azure a partir do seu projeto Azure DevOps.

1. Em Azure DevOps, vá ao projeto que contenha o seu pipeline alvo e abra as **definições** do Projeto na parte inferior esquerda.
1. Em **Pipelines** selecione **as ligações de serviço** e selecione Nova **ligação** de serviço no direito superior.
1. Selecione **Azure Resource Manager**.
1. Selecione **o principal do serviço (automático)**.
1. Preencha a sua subscrição e recurso. Dê um nome à sua ligação de serviço.

Agora que a sua ligação de serviço foi criada, encontre o nome do principal de serviço que lhe foi atribuído. Vai adicionar uma nova tarefa a este diretor de serviço no próximo passo.

1. Aceda às ligações de serviço **de definições**  >  **de projeto**.
1. Selecione a ligação de serviço que criou na secção anterior.
1. **Selecione Manage Service Principal**.
1. Note o **nome do Visor** listado.

## <a name="add-role-assignment"></a>Adicionar atribuição de função

Atribua as atribuições de funções de configuração de aplicação adequadas às credenciais que estão a ser utilizadas na tarefa para que a tarefa possa aceder à loja de Configuração de Aplicações.

1. Navegue para a sua loja de configuração de aplicativos alvo. 
1. À esquerda, selecione **Access control (IAM)**.
1. No topo, selecione **+ Adicionar** e escolher Adicionar a atribuição **de funções**.
1. Under **Role**, selecione **App Configuration Data Owner**. Esta função permite que a tarefa leia e escreva para a loja de Configuração de Aplicações. 
1. Selecione o principal serviço associado à ligação de serviço que criou na secção anterior.
  
## <a name="use-in-builds"></a>Uso em construções

Esta secção abrangerá como utilizar a tarefa Azure App Configuration Push num pipeline de construção de Azure DevOps.

1. Navegue para a página do gasoduto de construção clicando em  >  **Pipelines Pipelines**. A documentação para a construção de gasodutos pode ser [consultada aqui.](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2)
      - Se estiver a criar um novo pipeline de construção, selecione **Mostrar assistente** no lado direito do oleoduto e procure a tarefa **Azure App Configuration Push.**
      - Se estiver a utilizar um pipeline de construção existente, navegue no separador **Tarefas** ao editar o pipeline e procure a Tarefa de Push de Configuração da **Aplicação Azure.**
2. Configure os parâmetros necessários para a tarefa de empurrar os valores-chave do ficheiro de configuração para a loja de Configuração de Aplicações. O parâmetro **Caminho do Ficheiro de Configuração** começa na raiz do repositório de ficheiros.
3. Poupe e faça fila. O registo de construção apresentará quaisquer falhas que ocorreram durante a execução da tarefa.

## <a name="use-in-releases"></a>Utilização em lançamentos

Esta secção abrangerá como utilizar a tarefa Azure App Configuration Push num pipeline de lançamento Azure DevOps.

1. Navegue para lançar a página do gasoduto selecionando **lançamentos** de  >  **pipelines**. A documentação para os gasodutos de libertação pode ser [consultada aqui.](/azure/devops/pipelines/release)
1. Escolha um gasoduto de desbloqueio existente. Se não tiver um, selecione **+ Novo** para criar um novo.
1. Selecione o botão **Editar** no canto superior direito para editar o pipeline de desbloqueio.
1. Escolha o **Palco** para adicionar a tarefa. Mais informações sobre as etapas podem ser [encontradas aqui.](/azure/devops/pipelines/release/environments)
1. Selecione **+** para esse Trabalho e, em seguida, adicione a tarefa **Azure App Configuration Push** no **separador Implementar.**
1. Configure os parâmetros necessários na tarefa de empurrar os valores-chave do seu ficheiro de configuração para a sua loja de Configuração de Aplicações. As explicações dos parâmetros estão disponíveis na secção **Parâmetros** abaixo e nas pontas das ferramentas ao lado de cada parâmetro.
1. Poupe e faça fila para uma libertação. O registo de desbloqueio apresentará quaisquer falhas encontradas durante a execução da tarefa.

## <a name="parameters"></a>Parâmetros

Os seguintes parâmetros são utilizados pela tarefa De pressão de configuração da aplicação:

- **Subscrição Azure**: Uma entrega contendo as suas ligações de serviço Azure disponíveis. Para atualizar e atualizar a sua lista de ligações de serviço Azure disponíveis, prima o botão **de subscrição Refresh Azure** à direita da caixa de texto.
- **Nome de configuração da aplicação**: Um drop-down que carrega as suas lojas de configuração disponíveis sob a subscrição selecionada. Para atualizar e atualizar a sua lista de lojas de configuração disponíveis, prima o botão 'Atualizar o **Nome de Configuração da aplicação'** à direita da caixa de texto.
- **Caminho do ficheiro de configuração**: O caminho para o seu ficheiro de configuração. Pode navegar através do seu artefacto de construção para selecionar um ficheiro de configuração. `...`(botão à direita da caixa de texto). Os formatos de ficheiro suportado são: yaml, json, propriedades.
- **Separador**: O separador que é usado para achatar ficheiros .json e .yml.
- **Profundidade**: A profundidade a que os ficheiros .json e .yml serão achatados.
- **Prefixo**: Uma corda anexada ao início de cada tecla empurrada para a loja de Configuração de Aplicações.
- **Etiqueta**: Uma cadeia que é adicionada a cada valor-chave como a etiqueta dentro da loja de configuração de aplicações.
- **Tipo de Conteúdo**: Uma cadeia que é adicionada a cada valor-chave como o tipo de conteúdo dentro da loja de Configuração de Aplicações.
- **Tags**: Um objeto JSON no formato de `{"tag1":"val1", "tag2":"val2"}` , que define tags que são adicionadas a cada valor-chave empurrado para a sua loja de Configuração de Aplicações.
- **Elimine todas as outras Key-Values na loja com o prefixo e etiqueta especificados**: O valor predefinido não é **verificado**.
  - **Verificado**: Remove todos os valores-chave na loja de configuração da aplicação que correspondem tanto ao prefixo especificado como à etiqueta antes de empurrar novos valores-chave do ficheiro de configuração.
  - **Desmarcado**: Empurra todos os valores-chave do ficheiro de configuração para a loja de Configuração de Aplicações e deixa tudo o resto intacto na loja de Configuração de Aplicações.

Depois de preencher os parâmetros necessários, escocha o gasoduto. Todos os valores-chave no ficheiro de configuração especificado serão enviados para a Configuração da Aplicação.

## <a name="troubleshooting"></a>Resolução de problemas

Se ocorrer um erro inesperado, os registos de depurados podem ser ativados definindo a variável do gasoduto `system.debug` para `true` .

## <a name="faq"></a>FAQ

**Como posso carregar vários ficheiros de configuração?**

Crie várias instâncias da tarefa Azure App Configuration Push dentro do mesmo pipeline para empurrar vários ficheiros de configuração para a loja de Configuração de Aplicações.

**Porque estou a receber um erro 409 ao tentar empurrar valores-chave para a minha loja de configuração?**

Ocorrerá uma mensagem de erro de conflito 409 se a tarefa tentar remover ou substituir um valor-chave que está bloqueado na loja de Configuração de Aplicações.
