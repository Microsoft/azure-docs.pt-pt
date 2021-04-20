---
title: Integração contínua e entrega para espaço de trabalho Synapse
description: Aprenda a usar a integração contínua e a entrega para implementar mudanças no espaço de trabalho de um ambiente (desenvolvimento, teste, produção) para outro.
author: liudan66
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 833478d956560c981bd6cc3ba03b48bb602f563c
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739679"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Integração contínua e entrega para o espaço de trabalho Azure Synapse

## <a name="overview"></a>Descrição Geral

Integração Contínua (CI) é o processo de automatização da construção e teste do código sempre que um membro da equipa comete alterações ao controlo da versão. A Implementação Contínua (CD) é o processo de construção, teste, configuração e implantação de múltiplos ambientes de teste ou de encenação para um ambiente de produção.

Num espaço de trabalho Azure Synapse Analytics, a integração e entrega contínuas (CI/CD) desloca todas as entidades de um ambiente (desenvolvimento, teste, produção) para outro. Para promover o seu espaço de trabalho para outro espaço de trabalho, existem duas partes. Em primeiro lugar, utilize um [modelo de Gestor de Recursos Azure (modelo ARM)](../../azure-resource-manager/templates/overview.md) para criar ou atualizar recursos do espaço de trabalho (piscinas e espaço de trabalho). Em seguida, migrar artefactos (scripts SQL, caderno, definição de trabalho spark, oleodutos, conjuntos de dados, fluxos de dados, e assim por diante) com ferramentas CI/CD Azure Synapse Analytics em Azure DevOps. 

Este artigo descreve como usar um oleoduto de libertação Azure DevOps para automatizar a implantação de um espaço de trabalho Azure Synapse para vários ambientes.

## <a name="prerequisites"></a>Pré-requisitos

Estes pré-requisitos e configurações devem estar no lugar para automatizar a implantação de um espaço de trabalho Azure Synapse para vários ambientes.

### <a name="azure-devops"></a>Azure DevOps

- Um projeto Azure DevOps foi preparado para executar o oleoduto de lançamento.
- [Conceda aos utilizadores que verifiquem o código "Basic" de acesso ao nível da organização,](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page)para que possam ver o repo.
- Grant Owner Rights to the Azure Synapse repo.
- Certifique-se de que criou um agente VM Azure DevOps auto-hospedado ou utilize um agente hospedado da Azure DevOps.
- Permissões para [criar uma ligação de serviço Azure Resource Manager para o grupo de recursos](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml).
- Um administrador do Azure Ative Directory (Azure AD) deve [instalar a extensão do Agente de Implantação do Espaço de Trabalho Azure DevOpse na organização Azure DevOps](/azure/devops/marketplace/install-extension).
- Criar ou nomear uma conta de serviço existente para que o gasoduto possa funcionar como. Pode utilizar um token de acesso pessoal em vez de uma conta de serviço, mas os seus oleodutos não funcionarão depois de a conta do utilizador ser eliminada.

### <a name="azure-active-directory"></a>Azure Active Directory

- Em Azure AD, crie um principal de serviço para usar para implantação. A tarefa de implantação do espaço de trabalho synapse não suporta a utilização de uma identidade gerida em verion 1* e mais cedo.
- Os direitos de administração da Admin da Azure são necessários para esta ação.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> Pode automatizar e implementar estes pré-requisitos utilizando o mesmo oleoduto, um modelo ARM ou o CLI Azure, mas o processo não está descrito neste artigo.

- O espaço de trabalho "source" que é usado para o desenvolvimento deve ser configurado com um repositório git no Synapse Studio. Para mais informações, consulte [o controlo de origem no Synapse Studio](source-control.md#configuration-method-2-manage-hub).

- Um espaço de trabalho em branco para implantar. Para configurar o espaço de trabalho em branco:

  1. Crie um novo espaço de trabalho Azure Synapse Analytics.
  1. Conceda ao agente VM e aos principais contribuintes do grupo de recursos em que o novo espaço de trabalho está hospedado.
  1. No novo espaço de trabalho, não configuure a ligação git repo.
  1. No portal Azure, encontre o novo espaço de trabalho Azure Synapse Analytics e conceda a si mesmo e a quem quer que coma o gasoduto Azure DevOps Azure Synapse Analytics direitos de proprietário do espaço de trabalho. 
  1. Adicione o agente VM Azure DevOps e o principal de serviço à função contribuinte para o espaço de trabalho (isto deveria ter herdado, mas verifique se é).
  1. No espaço de trabalho Azure Synapse Analytics, vá ao **Studio**  >  **Manage**  >  **IAM.** Adicione o agente VM Azure DevOps e o principal de serviço ao grupo de administradores do espaço de trabalho.
  1. Abra a conta de armazenamento que é usada para o espaço de trabalho. No IAM, adicione o agente VM e o principal de serviço à função Select Storage Blob Data Contributor.
  1. Crie um cofre chave na subscrição de suporte e certifique-se de que tanto o espaço de trabalho existente como o novo espaço de trabalho têm pelo menos permissões GET e LIST para o cofre.
  1. Para que a implementação automatizada funcione, certifique-se de que quaisquer cadeias de ligação especificadas nos seus serviços ligados estão no cofre de chaves.

### <a name="additional-prerequisites"></a>Pré-requisitos adicionais
 
 - Piscinas de faíscas e tempos de integração auto-hospedados não são criados num oleoduto. Se tiver um serviço ligado que utilize um tempo de integração auto-hospedado, crie manualmente isso no novo espaço de trabalho.
 - Se estiver a desenvolver cadernos e os ligar a uma piscina Spark, re-crie a piscina Spark no espaço de trabalho.
 - Os cadernos que estão ligados a uma piscina spark que não existe num ambiente não serão implantados.
 - Os nomes da piscina Spark devem ser os mesmos em ambos os espaços de trabalho.
 - Nomeie todas as bases de dados, piscinas SQL e outros recursos iguais em ambos os espaços de trabalho.
 - Se as suas piscinas SQL a provisionadas forem interrompidas quando tentar implantar, a implantação pode falhar.

Para obter mais informações, consulte [o CD CI em Azure Synapse Analytics Part 4 - The Release Pipeline](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434). 


## <a name="set-up-a-release-pipeline"></a>Criar um oleoduto de libertação

1.  Em [Azure DevOps,](https://dev.azure.com/)abra o projeto criado para o lançamento.

1.  No lado esquerdo da página, selecione **Pipelines** e, em seguida, selecione **Versões**.

    ![Selecione Pipelines, Lançamentos](media/create-release-1.png)

1.  Selecione **Novo oleoduto**, ou, se tiver os oleodutos existentes, selecione **Novo** e, em seguida, Novo oleoduto **de lançamento**.

1.  Selecione o modelo **de trabalho vazio.**

    ![Selecione trabalho vazio](media/create-release-select-empty.png)

1.  Na **caixa de nomes** de palco, insira o nome do seu ambiente.

1.  **Selecione Adicionar artefacto** e, em seguida, selecione o repositório de git configurado com o seu desenvolvimento Synapse Studio. Selecione o repositório de git que usou para gerir o modelo ARM de piscinas e espaço de trabalho. Se utilizar o GitHub como fonte, tem de criar uma ligação de serviço para a sua conta GitHub e retirar repositórios. Para mais informações sobre [a ligação de serviço](/azure/devops/pipelines/library/service-endpoints) 

    ![Adicionar ramo de publicação](media/release-creation-github.png)

1.  Selecione o ramo do modelo ARM para atualização de recursos. Para a **versão Predefinitiva**, selecione **mais recente do ramo predefinido**.

    ![Adicionar modelo ARM](media/release-creation-arm-branch.png)

1.  Selecione o ramo de [publicação](source-control.md#configure-publishing-settings) do repositório para o **ramo Predefinido**. Por defeito, esta sucursal de publicação é `workspace_publish` . Para a **versão Predefinitiva**, selecione **mais recente do ramo predefinido**.

    ![Adicionar um artefacto](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-resource"></a>Crie uma tarefa de fase para um modelo ARM para criar e atualizar recursos 

Se tiver um modelo ARM para implantar um recurso, como um espaço de trabalho Azure Synapse, piscinas Spark e SQL, ou um cofre chave, adicione uma tarefa de implementação do Gestor de Recursos Azure para criar ou atualizar esses recursos:

1. Na vista do palco, **selecione Ver as tarefas do palco**.

    ![Vista de palco](media/release-creation-stage-view.png)

1. Criar uma nova tarefa. Procure a **implementação do modelo ARM** e, em seguida, selecione **Adicionar**.

1. Na tarefa De implementação, selecione a subscrição, o grupo de recursos e a localização para o espaço de trabalho alvo. Fornecer credenciais, se necessário.

1. Na lista **de Ação,** selecione **Criar ou atualizar o grupo de recursos**.

1. Selecione o botão elipse **(...**) ao lado da caixa **Modelo.** Navegue para o modelo do Gestor de Recursos Azure do seu espaço de trabalho alvo

1. Selecione **...** ao lado da caixa **de parâmetros do modelo** para escolher o ficheiro de parâmetros.

1. Selecione **...** ao lado da caixa de **parâmetros do modelo de substituição** e introduza os valores de parâmetros desejados para o espaço de trabalho alvo. 

1. Selecione **Incremental** para o **modo de implementação**.
    
    ![espaço de trabalho e piscinas implantam](media/pools-resource-deploy.png)

1. (Opcional) Adicione **a Azure PowerShell** para a atribuição de funções de espaço de trabalho de concessão e atualização. Se utilizar o gasoduto de desbloqueio para criar um espaço de trabalho synapse, o principal de serviço do oleoduto é adicionado como administrador de espaço de trabalho predefinido. Pode executar o PowerShell para conceder a outras contas acesso ao espaço de trabalho. 
    
    ![conceder permissão](media/release-creation-grant-permission.png)

 > [!WARNING]
> No modo de implementação completo, os recursos que existem no grupo de recursos mas que não estão especificados no novo modelo de Gestor de Recursos serão **eliminados**. Para mais informações, consulte os [modos de implementação do Gestor de Recursos Azure](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-synapse-artifacts-deployment"></a>Configurar uma tarefa de palco para implantação de artefactos synapse 

Utilize a extensão [de implantação do espaço de trabalho synapse](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) para implantar outros itens no espaço de trabalho synapse, como conjunto de dados, script SQL, caderno, definição de trabalho de faísca, fluxo de dados, serviço ligado, serviço ligado e IR (Integration Runtime).  

1. Pesquisa e obtenha a extensão do **mercado Azure DevOps**(https://marketplace.visualstudio.com/azuredevops) 

     ![Obter extensão](media/get-extension-from-market.png)

1. Selecione uma organização para instalar extensão. 

     ![Instalar a extensão](media/install-extension.png)

1. Certifique-se de que o principal de serviço do gasoduto Azure DevOps recebeu a permissão de subscrição e também atribuído como administrador do espaço de trabalho para o espaço de trabalho alvo. 

1. Criar uma nova tarefa. Procure a **implementação do espaço de trabalho synapse** e, em seguida, selecione **Adicionar**.

     ![Adicionar extensão](media/add-extension-task.png)

1.  Na tarefa, selecione **...** ao lado da caixa **de modelo** para escolher o ficheiro do modelo.

1. Selecione **...** ao lado da caixa **de parâmetros do modelo** para escolher o ficheiro de parâmetros.

1. Selecione a ligação, o grupo de recursos e o nome do espaço de trabalho alvo. 

1. Selecione **...** ao lado da caixa de parâmetros do **modelo de substituição** e introduza os valores de parâmetros desejados para o espaço de trabalho alvo, incluindo cordas de ligação e chaves de conta que são usadas nos seus serviços ligados. [Clique aqui para mais informações] (https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)

    ![Implantação do espaço de trabalho da Sinapse](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> Nos cenários ci/CD, o tipo de execução de integração (IR) em diferentes ambientes deve ser o mesmo. Por exemplo, se tiver um IR auto-hospedado no ambiente de desenvolvimento, o mesmo IR também deve ser do tipo auto-hospedado em outros ambientes, como o teste e a produção. Da mesma forma, se estiver a partilhar tempos de integração em várias fases, tem de configurar os tempos de integração como auto-hospedados em todos os ambientes, tais como desenvolvimento, teste e produção.

## <a name="create-release-for-deployment"></a>Criar lançamento para implantação 

Depois de guardar todas as alterações, pode selecionar **Criar desbloqueio** para criar manualmente um desbloqueio. Para automatizar a criação de lançamentos, veja [os gatilhos de lançamento do Azure DevOps](/azure/devops/pipelines/release/triggers)

   ![Selecione Criar ver lançamento](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>Use parâmetros personalizados do modelo do espaço de trabalho 

Utiliza CI/CD automatizado e pretende alterar algumas propriedades durante a implementação, mas as propriedades não são parametrizadas por padrão. Neste caso, pode anular o modelo de parâmetro padrão.

Para anular o modelo de parâmetro padrão, é necessário criar um modelo de parâmetro personalizado, um ficheiro nomeado **template-parameters-definition.jsna** pasta raiz do seu ramo de colaboração git. Tens de usar esse nome exato do ficheiro. Ao publicar a partir do ramo de colaboração, o espaço de trabalho da Synapse irá ler este ficheiro e utilizar a sua configuração para gerar os parâmetros. Se não for encontrado nenhum ficheiro, o modelo de parâmetro padrão é utilizado.

### <a name="custom-parameter-syntax"></a>Sintaxe de parâmetro personalizado

Seguem-se algumas diretrizes para a criação do ficheiro de parâmetros personalizados:

* Insira o caminho da propriedade sob o tipo de entidade relevante.
* Definir um nome de propriedade indica `*` que pretende parametrizar todas as propriedades por baixo (apenas até ao primeiro nível, não recursivamente). Também pode fornecer exceções a esta configuração.
* Definir o valor de uma propriedade como uma corda indica que deseja parametrizar a propriedade. Utilize o formato `<action>:<name>:<stype>`.
   *  `<action>` pode ser um destes caracteres:
      * `=` significa manter o valor atual como o valor padrão para o parâmetro.
      * `-` significa não manter o valor padrão para o parâmetro.
      * `|` é um caso especial para segredos do Azure Key Vault para cordas de ligação ou chaves.
   * `<name>` é o nome do parâmetro. Se estiver em branco, tem o nome da propriedade. Se o valor começar com um `-` personagem, o nome é encurtado. Por exemplo, `AzureStorage1_properties_typeProperties_connectionString` seria encurtado para `AzureStorage1_connectionString` .
   * `<stype>` é o tipo de parâmetro. Se `<stype>` estiver em branco, o tipo predefinido é `string` . Valores apoiados: `string` , , , , e `securestring` `int` `bool` `object` `secureobject` `array` .
* Especificar uma matriz no ficheiro indica que a propriedade correspondente no modelo é uma matriz. A sinapse itera através de todos os objetos da matriz usando a definição especificada. O segundo objeto, uma corda, torna-se o nome da propriedade, que é usada como o nome para o parâmetro para cada iteração.
* Uma definição não pode ser específica para uma instância de recursos. Qualquer definição aplica-se a todos os recursos desse tipo.
* Por padrão, todas as cordas seguras, como os segredos do Cofre de Chaves, e cordas seguras, como cordas de ligação, chaves e fichas, são parametrizadas.

### <a name="parameter-template-definition-samples"></a>Amostras de definição de modelo de parâmetro 

Aqui está um exemplo de como é uma definição de modelo de parâmetro:

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

Aqui está uma explicação de como o modelo anterior é construído, dividido por tipo de recurso.

#### <a name="notebooks"></a>Notebooks 

* Qualquer propriedade no caminho `properties/bigDataPool/referenceName` é parametrizada com o seu valor padrão. Pode parametrizar a piscina de faísca anexa para cada ficheiro de caderno. 

#### <a name="sql-scripts"></a>Scripts SQL 

* Propriedades (poolName e base de dadosName) no caminho `properties/content/currentConnection` são parametrizadas como cordas sem os valores padrão no modelo. 

#### <a name="pipelines"></a>Pipelines

* Qualquer propriedade no caminho `activities/typeProperties/waitTimeInSeconds` é parametrizada. Qualquer atividade num oleoduto que tenha uma propriedade de nível de código denominada `waitTimeInSeconds` (por exemplo, a `Wait` atividade) é parametrizada como um número, com um nome padrão. Mas não terá um valor predefinido no modelo de Gestor de Recursos. Será uma entrada obrigatória durante a implementação do Gestor de Recursos.
* Da mesma forma, uma propriedade chamada `headers` (por exemplo, numa `Web` atividade) é parametrizada com o tipo `object` (Objeto). Tem um valor padrão, que é o mesmo valor da fábrica de origem.

#### <a name="integrationruntimes"></a>IntegraçãoRuntimes

* Todas as propriedades sob o caminho `typeProperties` são parametrizadas com os respetivos valores predefinidos. Por exemplo, existem duas propriedades sob `IntegrationRuntimes` propriedades do tipo: `computeProperties` e `ssisProperties` . Ambos os tipos de propriedade são criados com os respetivos valores e tipos predefinidos (Objeto).

#### <a name="triggers"></a>Acionadores

* Em `typeProperties` baixo, duas propriedades são parametrizadas. O primeiro é `maxConcurrency` , que é especificado para ter um valor padrão e é de tipo `string` . Tem o nome de parâmetro `<entityName>_properties_typeProperties_maxConcurrency` padrão.
* A `recurrence` propriedade também é parametrizada. Sob ele, todas as propriedades a esse nível são especificadas para serem parametrizadas como cordas, com valores padrão e nomes de parâmetros. Uma exceção é a `interval` propriedade, que é parametrizada como `int` tipo. O nome do parâmetro é sufixado com `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . Da mesma forma, a `freq` propriedade é uma corda e é parametrizada como uma corda. No entanto, a `freq` propriedade é parametrizada sem um valor padrão. O nome é encurtado e sufixado. Por exemplo, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Os serviços ligados são únicos. Como os serviços e conjuntos de dados ligados têm uma vasta gama de tipos, pode fornecer personalização específica do tipo. Neste exemplo, para todos os serviços de tipo `AzureDataLakeStore` ligados, será aplicado um modelo específico. Para todos os outros `*` (via), será aplicado um modelo diferente.
* A `connectionString` propriedade será parametrizada como um `securestring` valor. Não terá um valor padrão. Terá um nome de parâmetro encurtado que é sufixado com `connectionString` .
* A propriedade `secretAccessKey` passa a ser um `AzureKeyVaultSecret` (por exemplo, num serviço ligado ao Amazon S3). É automaticamente parametrizado como um cofre de chave Azure e recolhido do cofre de chaves configurado. Também pode parametrizar o cofre da chave em si.

#### <a name="datasets"></a>Conjuntos de dados

* Embora a personalização específica do tipo esteja disponível para conjuntos de dados, pode fornecer configuração sem ter explicitamente uma \* configuração de nível. No exemplo anterior, todas as propriedades do conjunto de `typeProperties` dados são parametrizadas.


## <a name="best-practices-for-cicd"></a>Melhores práticas para CI/CD

Se estiver a utilizar a integração do Git com o seu espaço de trabalho Azure Synapse e tiver um pipeline CI/CD que move as suas mudanças de desenvolvimento para teste e depois para a produção, recomendamos estas boas práticas:

-   **Integração de Git**. Configure apenas o seu desenvolvimento Azure Synapse espaço de trabalho com integração Git. As alterações aos espaços de trabalho de teste e produção são implementadas através de CI/CD e não necessitam de integração do Git.
-   **Prepare piscinas antes da migração de artefactos.** Se você tem script SQL ou caderno anexado a piscinas no espaço de trabalho de desenvolvimento, o mesmo nome de piscinas em diferentes ambientes são esperados. 
-   **Infraestrutura como Código (IAC)**. Gestão de infraestruturas (redes, máquinas virtuais, equilibradores de carga e topologia de ligação) num modelo descritivo, utilize a mesma versão que a equipa de DevOps utiliza para código fonte. 
-   **Outros.** Ver [as melhores práticas para artefactos ADF](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Implantação de artefactos de resolução de problemas 

### <a name="use-the-azure-synapse-analytics-workspace-deployment-task"></a>Utilize a tarefa de implantação do espaço de trabalho Azure Synapse Analytics

No Azure Synapse Analytics, há uma série de artefactos que não são recursos ARM. Isto difere da Azure Data Factory. A tarefa de implantação do modelo ARM não funcionará corretamente para implantar artefactos Azure Synapse Analytics.
 
### <a name="unexpected-token-error-in-release"></a>Erro inesperado do token no lançamento

Quando o seu ficheiro de parâmetros tem valores de parâmetros que não são escapados, o pipeline de libertação deixará de analisar o ficheiro e gerará o erro, "token inesperado". Sugerimos que sobreponha parâmetros ou use o Cofre da Chave Azure para recuperar os valores dos parâmetros. Também pode usar caracteres de fuga dupla como uma solução alternativa.
