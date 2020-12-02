---
title: Criar espaços de trabalho no portal
titleSuffix: Azure Machine Learning
description: Aprenda a criar, ver e apagar espaços de trabalho de Aprendizagem automática Azure no portal Azure ou com o SDK for Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: 4425fdf488665ad555c73c59682041cb23a9ca66
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447334"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Criar e gerir espaços de trabalho de aprendizagem automática Azure 

Neste artigo, você vai criar, visualizar e eliminar [**espaços de trabalho de aprendizagem automática Azure**](concept-workspace.md) para [Azure Machine Learning,](overview-what-is-azure-ml.md)usando o portal Azure ou o [SDK para Python](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py)

À medida que as suas necessidades mudam ou os requisitos para a automatização aumentam, também pode criar e eliminar espaços de trabalho [utilizando o CLI,](reference-azure-machine-learning-cli.md)ou [através da extensão do Código VS](tutorial-setup-vscode-extension.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)
* Se utilizar o Python SDK, [instale o SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

## <a name="limitations"></a>Limitações

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="create-a-workspace"></a>Criar uma área de trabalho

# <a name="python"></a>[Python](#tab/python)

* **Especificação padrão.** Por padrão, os recursos dependentes, bem como o grupo de recursos serão criados automaticamente. Este código cria um espaço de trabalho nomeado `myworkspace` e um grupo de recursos nomeado em `myresourcegroup` `eastus2` .
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    Definir `create_resource_group` para Falso se tiver um grupo de recursos Azure existente que pretende utilizar para o espaço de trabalho.

* <a name="create-multi-tenant"></a>**Vários inquilinos.**  Se tiver várias contas, adicione o ID do inquilino do Diretório Ativo Azure que deseja utilizar.  Encontre a identificação do seu inquilino a partir do [portal Azure](https://portal.azure.com) sob **diretório ativo Azure, Identidades Externas.**

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **[Nuvem soberana.](reference-machine-learning-cloud-parity.md)** Vai precisar de um código extra para autenticar o Azure se estiver a trabalhar numa nuvem soberana.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **Utilize os recursos Azure existentes.**  Também pode criar um espaço de trabalho que utiliza os recursos Azure existentes com o formato de ID de recursos Azure. Encontre os IDs específicos de recursos Azure no portal Azure ou com o SDK. Este exemplo pressupõe que o grupo de recursos, a conta de armazenamento, o cofre-chave, o App Insights e o registo de contentores já existem.

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

Para mais informações, consulte [a referência SDK do Espaço de Trabalho.](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py)

Se tiver problemas em aceder à sua subscrição, consulte [Configurar a autenticação para recursos e fluxos de trabalho de Aprendizagem de Máquinas Azure,](how-to-setup-authentication.md)bem como a Autenticação no caderno [Azure Machine Learning.](https://aka.ms/aml-notebook-auth)

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando as credenciais para a sua subscrição Azure. 

1. No canto superior esquerdo do portal Azure, selecione **+ Criar um recurso**.

      ![Criar um novo recurso](./media/how-to-manage-workspace/create-workspace.gif)

1. Utilize a barra de pesquisa para encontrar **machine learning**.

1. Selecione **Machine Learning**.

1. No painel **de aprendizagem** automática, selecione **Criar** para começar.

1. Forneça as seguintes informações para configurar o seu novo espaço de trabalho:

   Campo|Descrição 
   ---|---
   Nome da área de trabalho |Insira um nome único que identifique o seu espaço de trabalho. Neste exemplo, **usamos docs-ws**. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de recordar e diferenciar dos espaços de trabalho criados por outros. O nome do espaço de trabalho é insensível a caso.
   Subscrição |Selecione a subscrição do Azure que pretende utilizar.
   Grupo de recursos | Utilize um grupo de recursos já existente na sua subscrição ou introduza um nome para criar um grupo de recursos novo. Um grupo de recursos detém recursos relacionados para uma solução Azure. Neste exemplo, **usamos docs-aml**. Precisa de *um colaborador* ou de um *papel proprietário* para utilizar um grupo de recursos existente.  Para obter mais informações sobre o acesso, consulte [Gerir o acesso a um espaço de trabalho Azure Machine Learning.](how-to-assign-roles.md)
   Região | Selecione a região Azure mais próxima dos seus utilizadores e os recursos de dados para criar o seu espaço de trabalho.
   | Conta de armazenamento | A conta de armazenamento predefinido para o espaço de trabalho. Por padrão, um novo é criado. |
   | Cofre de Chaves | O Cofre da Chave Azure usado pelo espaço de trabalho. Por padrão, um novo é criado. |
   | Application Insights | A aplicação insights exemplo para o espaço de trabalho. Por padrão, um novo é criado. |
   | Container Registry | O Registo do Contentor Azure para o espaço de trabalho. Por padrão, um novo _não_ é inicialmente criado para o espaço de trabalho. Em vez disso, é criado uma vez que você precisa quando se cria uma imagem Docker durante o treino ou implantação. |

   :::image type="content" source="media/how-to-manage-workspace/create-workspace-form.png" alt-text="Configure o seu espaço de trabalho.":::

1. Quando terminar de configurar o espaço de trabalho, selecione **Review + Create**. Opcionalmente, utilize as secções [networking](#networking) e [Advanced](#advanced) para configurar mais configurações para o espaço de trabalho.

1. Reveja as definições e escdave quaisquer alterações ou correções adicionais. Quando estiver satisfeito com as definições, **selecione Criar**.

   > [!Warning] 
   > Pode levar vários minutos para criar o seu espaço de trabalho na nuvem.

   Quando o processo está concluído, aparece uma mensagem de sucesso de implantação. 
 
 1. Para visualizar o novo espaço de trabalho, selecione **Ir para o recurso**.
 
---

### <a name="networking"></a>Rede  

> [!IMPORTANT]  
> Para obter mais informações sobre a utilização de um ponto final privado e rede virtual com o seu espaço de trabalho, consulte [o isolamento e privacidade da Rede.](how-to-network-security-overview.md)


# <a name="python"></a>[Python](#tab/python)

A Azure Machine Learning Python SDK fornece a classe [PrivateEndpointConfig,](/python/api/azureml-core/azureml.core.privateendpointconfig?preserve-view=true&view=azure-ml-py) que pode ser usada com [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-&preserve-view=true) para criar um espaço de trabalho com um ponto final privado. Esta classe requer uma rede virtual existente.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. A configuração de rede padrão é usar um __ponto final público__, que é acessível na internet pública. Para limitar o acesso ao seu espaço de trabalho a uma Rede Virtual Azure que criou, pode, em vez disso, selecionar __o ponto final privado__ (pré-visualização) como método de __Conectividade__, e depois utilizar __+ Adicionar__ para configurar o ponto final.   

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Seleção privada de pontos finais":::  

1. No formulário __de ponto final privado Create,__ desaponte a localização, nome e rede virtual para utilizar. Se quiser utilizar o ponto final com uma Zona DNS Privada, __selecione Integre com a zona privada de DNS__ e selecione a zona utilizando o campo __Zona DNS Privada.__ Selecione __OK__ para criar o ponto final.   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Criação de ponto final privado":::   

1. Quando terminar de configurar a rede, pode selecionar __Review + Create__ ou avançar para a configuração __avançada__ opcional.

---

> [!IMPORTANT]  
> A utilização de um ponto final privado com o espaço de trabalho Azure Machine Learning está atualmente em pré-visualização pública. Esta pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.     
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="multiple-workspaces-with-private-endpoint"></a>Múltiplos espaços de trabalho com ponto final privado

Quando cria um ponto final privado, é criada uma nova Zona Privada de DNS chamada __privatelink.api.azureml.ms.__ Isto contém uma ligação com a rede virtual. Se criar vários espaços de trabalho com pontos finais privados no mesmo grupo de recursos, apenas a rede virtual para o primeiro ponto final privado pode ser adicionada à zona de DNS. Para adicionar entradas para as redes virtuais utilizadas pelos espaços de trabalho adicionais/pontos finais privados, utilize os seguintes passos:

1. No [portal Azure,](https://portal.azure.com)selecione o grupo de recursos que contém o espaço de trabalho. Em seguida, selecione o recurso Private DNS Zone nomeado __privatelink.api.azureml.ms__
2. Nas __Definições__, selecione __links de rede virtual__.
3. Selecione __Adicionar__. A partir da página __de link de rede virtual Add,__ forneça um nome __link__ único e, em seguida, selecione a __rede Virtual__ a ser adicionada. Selecione __OK__ para adicionar a ligação de rede.

Para obter mais informações, consulte [a configuração do DNS do Ponto Final Privado Azure](../private-link/private-endpoint-dns.md).

### <a name="vulnerability-scanning"></a>Análise de vulnerabilidades

O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção contra ameaças avançada entre cargas de trabalho na cloud híbrida. Deve permitir que o Azure Security Center digitalize os seus recursos e siga as suas recomendações. Para mais informações, consulte  [a imagem do Registo de Contentores Azure pelo Security Center](../security-center/defender-for-container-registries-introduction.md) e a [integração dos Serviços Azure Kubernetes com o Security Center](../security-center/defender-for-kubernetes-introduction.md).

### <a name="advanced"></a>Avançado

Por predefinição, os metadados para o espaço de trabalho são armazenados num caso DB Azure Cosmos que a Microsoft mantém. Estes dados são encriptados utilizando as teclas geridas pela Microsoft.

Para limitar os dados que a Microsoft recolhe no seu espaço de trabalho, selecione __High business impact workspace__ no portal, ou definido `hbi_workspace=true ` em Python. Para obter mais informações sobre esta definição, consulte [a encriptação em repouso](concept-data-encryption.md#encryption-at-rest).

> [!IMPORTANT]  
> A seleção de um impacto de alto negócio só pode ser feita quando se cria um espaço de trabalho. Não é possível alterar esta definição após a criação do espaço de trabalho.   

#### <a name="use-your-own-key"></a>Use a sua própria chave

Pode fornecer a sua própria chave para encriptação de dados. Ao fazê-lo, cria a instância DB do Azure Cosmos que armazena metadados na sua subscrição Azure.

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Use os seguintes passos para fornecer a sua própria chave:

> [!IMPORTANT]  
> Antes de seguir estes passos, deve primeiro realizar as seguintes ações:   
>
> 1. Autorize a __App de Aprendizagem automática__ (em Gestão de Identidade e Acesso) com permissões de colaboradores na sua subscrição.  
> 1. Siga os passos em [Chaves geridas pelo cliente](../cosmos-db/how-to-setup-cmk.md) para:
>     * Registe-se no fornecedor Azure Cosmos DB
>     * Criar e configurar um Cofre de Chaves Azure
>     * Gerar uma chave
>   
>     Não precisa de criar manualmente o exemplo DB do Azure Cosmos, um deles será criado para si durante a criação do espaço de trabalho. Esta instância DB Azure Cosmos será criada num grupo de recursos separado usando um nome baseado neste padrão: `<your-workspace-resource-name>_<GUID>` .   
>   
> Não é possível alterar esta definição após a criação do espaço de trabalho. Se eliminar o Azure Cosmos DB utilizado pelo seu espaço de trabalho, também deve eliminar o espaço de trabalho que o está a utilizar.

# <a name="python"></a>[Python](#tab/python)

Utilize `cmk_keyvault` e `resource_cmk_uri` especifique a chave gerida pelo cliente.

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecione __as teclas geridas pelo Cliente__ e, em seguida, selecione Clique para __selecionar a tecla__.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Chaves geridas pelo cliente":::

1. Na __tecla Select a partir do formulário Azure Key Vault,__ selecione um Cofre de Chave Azure existente, uma chave que contém e a versão da chave. Esta chave é usada para encriptar os dados armazenados no Azure Cosmos DB. Por fim, utilize o botão __Select__ para utilizar esta tecla.

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Selecione a chave":::

---

### <a name="download-a-configuration-file"></a>Descarregue um ficheiro de configuração

Se estiver a criar uma [instância computacional,](tutorial-1st-experiment-sdk-setup.md#azure)ignore este passo.  A instância computacional já criou uma cópia deste ficheiro para si.

# <a name="python"></a>[Python](#tab/python)

Se pretender utilizar código no seu ambiente local que faz referência a este espaço de trabalho `ws` (), escreva o ficheiro de configuração:

```python
ws.write_config()
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Se pretender utilizar código no seu ambiente local que faça referência a este espaço de trabalho, selecione  **Baixar config.jsna** secção **visão geral** do espaço de trabalho.  

   ![Baixar config.jsem](./media/how-to-manage-workspace/configure.png)

---

Coloque o ficheiro na estrutura do diretório com os seus scripts Python ou Cadernos Jupyter. Pode estar no mesmo diretório, num subdiretório chamado *.azureml,* ou num diretório-mãe. Quando cria uma instância de computação, este ficheiro é adicionado ao diretório correto no VM para si.

## <a name="connect-to-a-workspace"></a>Ligar-se a um espaço de trabalho

No seu código Python, cria-se um objeto de espaço de trabalho para se ligar ao seu espaço de trabalho.  Este código irá ler o conteúdo do ficheiro de configuração para encontrar o seu espaço de trabalho.  Receberá um pedido para iniciar sôms se ainda não estiver autenticado.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**Vários inquilinos.**  Se tiver várias contas, adicione o ID do inquilino do Diretório Ativo Azure que deseja utilizar.  Encontre a identificação do seu inquilino a partir do [portal Azure](https://portal.azure.com) sob **diretório ativo Azure, Identidades Externas.**

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[Nuvem soberana.](reference-machine-learning-cloud-parity.md)** Vai precisar de um código extra para autenticar o Azure se estiver a trabalhar numa nuvem soberana.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
Se tiver problemas em aceder à sua subscrição, consulte [Configurar a autenticação para recursos e fluxos de trabalho de Aprendizagem de Máquinas Azure,](how-to-setup-authentication.md)bem como a Autenticação no caderno [Azure Machine Learning.](https://aka.ms/aml-notebook-auth)

## <a name="find-a-workspace"></a><a name="view"></a>Encontre um espaço de trabalho

Veja uma lista de todos os espaços de trabalho que pode utilizar.

# <a name="python"></a>[Python](#tab/python)

Encontre as suas subscrições na [página de Subscrições no portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).  Copie o ID e use-o no código abaixo para ver todos os espaços de trabalho disponíveis para essa subscrição.

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. No campo de pesquisa superior, **escreva Machine Learning**.  

1. Selecione **Machine Learning**.

   ![Pesquisa de espaço de trabalho para aprendizagem automática Azure](./media/how-to-manage-workspace/find-workspaces.png)

1. Veja a lista de espaços de trabalho encontrados. Pode filtrar com base em subscrições, grupos de recursos e locais.  

1. Selecione um espaço de trabalho para exibir as suas propriedades.

---


## <a name="delete-a-workspace"></a>Eliminar uma área de trabalho

Quando já não precisar de um espaço de trabalho, elimine-o.  

# <a name="python"></a>[Python](#tab/python)

Eliminar o espaço de `ws` trabalho:

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

A ação predefinida não é eliminar os recursos associados ao espaço de trabalho, ou seja, o registo de contentores, a conta de armazenamento, o cofre-chave e os insights de aplicação.  Definir `delete_dependent_resources` para True para apagar estes recursos também.

# <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal Azure,](https://portal.azure.com/) **selecione Delete**  na parte superior do espaço de trabalho que pretende eliminar.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Apagar espaço de trabalho":::

---

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="resource-provider-errors"></a>Erros do fornecedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Mover o espaço de trabalho

> [!WARNING]
> Mover o seu espaço de trabalho Azure Machine Learning para uma subscrição diferente, ou mover a subscrição proprietária para um novo inquilino, não é suportado. Fazê-lo pode causar erros.

### <a name="deleting-the-azure-container-registry"></a>Eliminação do Registo do Contentor de Azure

O espaço de trabalho Azure Machine Learning utiliza o Registo do Contentor Azure (ACR) para algumas operações. Criará automaticamente uma instância ACR quando precisa primeiro de uma.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="examples"></a>Exemplos

Exemplos de criação de um espaço de trabalho:
* Use o portal Azure para [criar um espaço de trabalho e uma instância computacional](tutorial-1st-experiment-sdk-setup.md)
* Use a Python SDK para [criar um espaço de trabalho no seu próprio ambiente](tutorial-1st-experiment-sdk-setup-local.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez que tenha um espaço de trabalho, aprenda a [treinar e implementar um modelo.](tutorial-train-models-with-aml.md)