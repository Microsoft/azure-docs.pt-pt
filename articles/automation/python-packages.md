---
title: Gerir pacotes de Python 2 na automatização do Azure
description: Este artigo descreve como gerir pacotes de Python 2 na automatização do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 28ddecb20944893b23b54775e22f19644f0afbf0
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816509"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gerir pacotes de Python 2 na automatização do Azure

A automatização do Azure permite-lhe executar runbooks de Python 2 no Azure e sobre os Runbook Workers híbridos de Linux. Para ajudar na simplificação de runbooks, pode utilizar pacotes de Python para importar os módulos que precisa. Este artigo descreve como gerir e utilizar pacotes de Python na automatização do Azure.

## <a name="import-packages"></a>Importar pacotes

Na sua conta de automatização, selecione **pacotes de Python 2** sob **recursos partilhados**. Clique em **+ adicionar um pacote de Python 2**.

![Adicionar o pacote do Python](media/python-packages/add-python-package.png)

Sobre o **Adicionar pacote do Python 2** , selecione um pacote local para carregar. O pacote pode ser um `.whl` arquivo ou `.tar.gz` ficheiro. Quando selecionado, clique em **OK** para carregar o pacote.

![Adicionar o pacote do Python](media/python-packages/upload-package.png)

Assim que tiver sido importado um pacote, estão listadas no **pacotes de Python 2** página na conta de automatização. Se tiver de remover um pacote, selecione o pacote e escolha **eliminar** na página do pacote.

![Lista de pacotes](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importar pacotes de dependências

A automatização do Azure não resolve as dependências de pacotes python durante o processo de importação. Existem duas formas de importar um pacote com todas as respetivas dependências. Apenas um dos seguintes passos tem de ser utilizadas para importar os pacotes para sua conta de automatização.

### <a name="manually-download"></a>Transferir manualmente

No Windows de 64 bits do computador com [python2.7](https://www.python.org/download/releases/2.7/) e [pip](https://pip.pypa.io/stable/installing/) instalado, execute o seguinte comando para transferir um pacote e todas as suas dependências:

```
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Assim que os pacotes são transferidos, pode importá-los para sua conta de automatização.

### <a name="runbook"></a>Runbook

Importe o runbook de python [pacotes de Python 2 da importação de pypi na conta de automatização do Azure](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) partir da galeria na sua conta de automatização. Certifique-se de que estão definidas para as definições de execução **Azure** e iniciar o runbook com os parâmetros. O runbook necessitar de uma conta Run As para a conta de automatização trabalhar. Para cada parâmetro, certifique-se de que inicie-o com o comutador como mostra a lista e a imagem seguinte:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![Lista de pacotes](media/python-packages/import-python-runbook.png)

O runbook permite-lhe especificar o que o pacote para transferir, por exemplo, `Azure` (o quarto parâmetro) irá transferir todos os módulos do Azure e todas as suas dependências, de que está prestes a 105.

Depois de concluída ao runbook pode verificar o **pacotes de Python 2** página sob **recursos partilhados** na conta de automatização para verificar se foram importada corretamente.

## <a name="use-a-package-in-a-runbook"></a>Utilizar um pacote num runbook

Depois de importar um pacote, pode usá-lo agora num runbook. O exemplo seguinte utiliza a [ pacote de utilitário de automatização do Azure](https://github.com/azureautomation/azure_automation_utility). Este pacote torna mais fácil de utilizar o Python com a automatização do Azure. Utilizar o pacote, siga as instruções no repositório do GitHub e adicioná-lo para o runbook utilizando `from azure_automation_utility import get_automation_runas_credential` por exemplo, para importar a função para a conta RunAs a obter.

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

## <a name="develop-and-test-runbooks-offline"></a>Desenvolver e testar runbooks offline

Para desenvolver e testar os runbooks de Python 2 offline, pode utilizar o [python de automatização do Azure emulada ativos](https://github.com/azureautomation/python_emulated_assets) módulo no GitHub. Este módulo permite-lhe fazer referência os recursos partilhados, como credenciais, variáveis, ligações e certificados.

## <a name="next-steps"></a>Passos Seguintes

Para começar com runbooks de Python 2, consulte [meu primeiro runbook de Python 2](automation-first-runbook-textual-python2.md)
