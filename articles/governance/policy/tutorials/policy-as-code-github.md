---
title: 'Tutorial: Implementar a política do Azure como código com o GitHub'
description: Neste tutorial, implementa-se uma Política Azure como fluxo de trabalho de Código com exportação, ações do GitHub e fluxos de trabalho do GitHub
ms.date: 10/20/2020
ms.topic: tutorial
ms.openlocfilehash: 76a46adc3fc8efab4f7a2d6e656e83c2537dd037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92326157"
---
# <a name="tutorial-implement-azure-policy-as-code-with-github"></a>Tutorial: Implementar a política do Azure como código com o GitHub

Uma Política Azure como fluxo de trabalho de código permite gerir as definições e atribuições políticas como código, controlar o ciclo de vida da atualização dessas definições e automatizar a validação dos resultados de conformidade. Neste tutorial, aprende-se a usar as funcionalidades da Azure Policy com o GitHub para construir um processo de ciclo de vida. Estas tarefas incluem:

> [!div class="checklist"]
> - Definições e atribuições da política de exportação ao GitHub
> - Empurre objetos de política atualizados no GitHub para Azure
> - Desencadear uma verificação de conformidade da ação do GitHub

Se quiser atribuir uma política para identificar o estado atual de conformidade dos seus recursos existentes, os artigos de arranque rápido explicam como fazê-lo.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Reveja a Política de Azure como fluxo de trabalho de código](../concepts/policy-as-code.md) para ter uma compreensão dos padrões de design utilizados neste tutorial.

### <a name="export-azure-policy-objects-from-the-azure-portal"></a>Exportar objetos da Política Azure a partir do portal Azure

Para exportar uma definição de política do portal Azure, siga estes passos:

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

1. Selecione **Definições** no lado esquerdo da página Política Azure.

1. Utilize o botão **de definições de exportação** ou selecione a elipse na linha de uma definição de política e, em seguida, selecione **a definição de Exportação**.

1. Selecione o Iniciar sção com o botão **GitHub.** Se ainda não autenticou com o GitHub para autorizar a Azure Policy a exportar o recurso, reveja o acesso que a [Ação GitHub](https://github.com/features/actions) necessita na nova janela que abre e selecione **Authorize AzureGitHubActions** para continuar com o processo de exportação. Uma vez concluída, a nova janela fecha-se.

1. No separador **Básicos,** desa estale as seguintes opções e, em seguida, selecione o separador **Políticas** ou **Seguinte : Políticas** na parte inferior da página.

   - **Filtro repositório**: Desaça para _os meus repositórios_ para ver apenas repositórios que possui ou _todos os repositórios_ para ver tudo o que concedeu à Ação GitHub.
   - **Repositório**: Desconfie ao repositório para o que pretende exportar os recursos da Política Azure para.
   - **Ramo**: Coloque o ramo no repositório. Usar um ramo diferente do padrão é uma boa forma de validar as suas atualizações antes de se fundir ainda mais no seu código fonte.
   - **Diretório**: A _pasta de nível de raiz_ para exportar os recursos da Política Azure para. As sub-dobradeiras ao abrigo deste diretório são criadas com base nos recursos exportados.

1. No separador **Políticas,** desaprova o âmbito de pesquisa selecionando a elipse e escolhendo uma combinação de grupos de gestão, subscrições ou grupos de recursos.
   
1. Utilize o botão **(s) de definição de política adicionar** para pesquisar o âmbito para qual os objetos a exportar. Na janela lateral que se abre, selecione cada objeto para exportar. Filtrar a seleção pela caixa de pesquisa ou pelo tipo. Depois de selecionar todos os objetos para exportar, utilize o botão **Adicionar** na parte inferior da página.

1. Para cada objeto selecionado, selecione as opções de exportação desejadas, tais como _Apenas Definição_ ou _Definição e Atribuição para_ uma definição de política. Em seguida, selecione o **separador Rever + Exportação** ou **Seguinte : Rever + Exportar** na parte inferior da página.

   > [!NOTE]
   > Se forem escolhidas as _definições e atribuições de opções,_ apenas são exportadas as atribuições políticas dentro do âmbito definido pelo filtro quando a definição de política é adicionada.

1. No **separador 'Rever + Exportação',** verifique os detalhes correspondem e, em seguida, utilize o botão **Exportação** na parte inferior da página.

1. Verifique a sua pasta gitHub repo, branch e _root level_ para ver se os recursos selecionados são agora exportados para o seu controlo de origem.

Os recursos da Política Azure são exportados para a seguinte estrutura dentro da pasta selecionada do repositório e _do nível de raiz_ do GitHub:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

### <a name="push-policy-objects-updated-in-github-to-azure"></a>Empurre objetos de política atualizados no GitHub para Azure

1. Quando os objetos de política são exportados, um ficheiro [de fluxo de trabalho GitHub](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) nomeado também é criado para `.github/workflows/manage-azure-policy-<randomLetters>.yml` começar.

   > [!NOTE]
   > O ficheiro de fluxo de trabalho GitHub é criado sempre que a exportação é utilizada. Cada instância do processo é específica das opções durante essa ação de exportação.

1. Este ficheiro de fluxo de trabalho utiliza a ação ["Manage Azure Policy"](https://github.com/marketplace/actions/manage-azure-policy) para impulsionar as alterações introduzidas nos objetos de política exportadas no repositório gitHub de volta à Política Azure. Por padrão, a ação considera e sincroniza apenas os ficheiros que são diferentes dos existentes no Azure. Também pode utilizar o `assignments` parâmetro na ação para apenas sincronizar alterações feitas em ficheiros de atribuição específicos. Este parâmetro pode ser usado para aplicar atribuições políticas apenas para um ambiente específico. Para obter mais informações, consulte a leitura do [repositório da Política de Gestão.](https://github.com/Azure/manage-azure-policy)

1. Por predefinição, o fluxo de trabalho deve ser acionado manualmente. Para tal, utilize as **Ações** no GitHub, selecione o `manage-azure-policy-<randomLetters>` fluxo de trabalho, selecione **Executar fluxo de trabalho** e, em seguida, executar **novamente o fluxo de trabalho.**

   :::image type="content" source="../media/policy-as-code-github/manually-trigger-workflow.png" alt-text="Screenshot do separador Action, workflow e Executar botões de fluxo de trabalho na interface web GitHub.":::

   > [!NOTE]
   > O ficheiro de fluxo de trabalho deve estar no ramo predefinido para ser detetado e executado manualmente.

1. O fluxo de trabalho sincroniza as alterações feitas aos objetos de política com o Azure e dá-lhe o estado nos registos.

   :::image type="content" source="../media/policy-as-code-github/workflow-logging.png" alt-text="Screenshot do fluxo de trabalho em ação e registou detalhes nos registos.":::

1. O fluxo de trabalho também adiciona detalhes em objetos da Política Azure `properties.metadata` para que possa rastrear.

   :::image type="content" source="../media/policy-as-code-github/updated-definition-metadata.png" alt-text="Screenshot da definição de Política Azure no portal Azure atualizado com metadados específicos da ação do GitHub.":::

### <a name="trigger-compliance-scans-using-github-action"></a>Trigger compliance scans usando ação GitHub

Utilizando a [ação Azure Policy Compliance Scan,](https://github.com/marketplace/actions/azure-policy-compliance-scan) pode desencadear uma avaliação de conformidade a pedido do seu fluxo de [trabalho GitHub](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) em um ou vários recursos, grupos de recursos ou subscrições, e alterar a trajetória de fluxo de trabalho com base no estado de conformidade desses recursos. Também pode configurar o fluxo de trabalho para executar em uma hora programada para obter o estado de conformidade mais recente em um momento conveniente. Opcionalmente, esta ação do GitHub também pode gerar um relatório sobre o estado de conformidade dos recursos digitalizados para posterior análise ou para arquivamento.

O exemplo a seguir executa uma verificação de conformidade para uma subscrição. 

```yaml

on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

```

## <a name="review"></a>Revisão

Neste tutorial conseguiu realizar com êxito as seguintes tarefas:

> [!div class="checklist"]
> - Definições e atribuições de políticas exportadas para o GitHub
> - Objetos de política empurrados atualizados no GitHub para Azure
> - Desencadeou uma verificação de conformidade da ação do GitHub

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as estruturas de definições de política, veja este artigo:

> [!div class="nextstepaction"]
> [Estrutura de definição do Azure Policy](../concepts/definition-structure.md)