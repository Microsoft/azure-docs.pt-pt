---
title: Gerenciar pacotes python 2 na automação do Azure
description: Este artigo descreve como gerenciar pacotes do Python 2 na automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 05d892edf20cda228bc566b30b0b693ea7c4a184
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417648"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gerenciar pacotes python 2 na automação do Azure

A automação do Azure permite que você execute runbooks do Python 2 no Azure e em Hybrid runbook Workers do Linux. Para ajudar na simplificação de runbooks, você pode usar pacotes do Python para importar os módulos de que precisa. Este artigo descreve como gerenciar e usar pacotes do Python na automação do Azure.

## <a name="import-packages"></a>Importar pacotes

Em sua conta de automação, selecione **pacotes python 2** em **recursos compartilhados**. Clique em **+ Adicionar um pacote Python 2**.

![Adicionar pacote do Python](media/python-packages/add-python-package.png)

Na página **Adicionar pacote Python 2** , selecione um pacote local para carregar. O pacote pode ser um arquivo `.whl` ou `.tar.gz` arquivo. Quando selecionado, clique em **OK** para carregar o pacote.

![Adicionar pacote do Python](media/python-packages/upload-package.png)

Depois que um pacote tiver sido importado, ele será listado na página **pacotes do Python 2** em sua conta de automação. Se você precisar remover um pacote, selecione o pacote e escolha **excluir** na página pacote.

![Lista de pacotes](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importar pacotes com dependências

A automação do Azure não resolve dependências de pacotes do Python durante o processo de importação. Há duas maneiras de importar um pacote com todas as suas dependências. Somente uma das etapas a seguir precisa ser usada para importar os pacotes para sua conta de automação.

### <a name="manually-download"></a>Baixar manualmente

Em um computador com Windows de 64 bits com [Python 2.7](https://www.python.org/downloads/release/latest/python2) e [Pip](https://pip.pypa.io/en/stable/) instalado, execute o seguinte comando para baixar um pacote e todas as suas dependências:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Depois que os pacotes forem baixados, você poderá importá-los para sua conta de automação.

### <a name="runbook"></a>Runbook

Importe os pacotes python [2 de importação do runbook do Python do PyPI para a conta de automação do Azure](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) da galeria para sua conta de automação. Verifique se as configurações de execução estão definidas como **Azure** e inicie o runbook com os parâmetros. O runbook requer uma conta Executar como para que a conta de automação funcione. Para cada parâmetro, certifique-se de iniciá-lo com a opção conforme mostrado na seguinte lista e imagem:

* -s \<SubscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![Lista de pacotes](media/python-packages/import-python-runbook.png)

O runbook permite que você especifique qual pacote baixar, por exemplo, `Azure` (o quarto parâmetro) baixará todos os módulos do Azure e todas as suas dependências, que é de cerca de 105.

Depois que o runbook for concluído, você poderá verificar a página de **pacotes do Python 2** em **recursos compartilhados** em sua conta de automação para verificar se eles foram importados corretamente.

## <a name="use-a-package-in-a-runbook"></a>Usar um pacote em um runbook

Depois de importar um pacote, agora você pode usá-lo em um runbook. O exemplo a seguir usa o [pacote do utilitário de automação do Azure](https://github.com/azureautomation/azure_automation_utility). Esse pacote torna mais fácil usar o Python com a automação do Azure. Para usar o pacote, siga as instruções no repositório do GitHub e adicione-o ao runbook usando `from azure_automation_utility import get_automation_runas_credential`, por exemplo, para importar a função para recuperar a conta Executar como.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Desenvolva e teste runbooks offline

Para desenvolver e testar seus runbooks do Python 2 offline, você pode usar o módulo de [ativos emulados do Python de automação do Azure](https://github.com/azureautomation/python_emulated_assets) no github. Esse módulo permite que você faça referência a seus recursos compartilhados, como credenciais, variáveis, conexões e certificados.

## <a name="next-steps"></a>Passos seguintes

Para começar com runbooks do Python 2, consulte [meu primeiro runbook do Python 2](automation-first-runbook-textual-python2.md)
