---
title: Gerir pacotes Python 2 na Automação Azure
description: Este artigo diz como gerir os pacotes Python 2 na Automação Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: cc709be7a7f0d46d105e73b0a105a3dec8cc793c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83746711"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gerir pacotes Python 2 na Automação Azure

A Azure Automation permite-lhe executar livros de corridas Python 2 no Azure e em Trabalhadores de Runbook Híbridos Linux. Para ajudar na simplificação dos livros de execução, pode utilizar pacotes Python para importar os módulos de que necessita. Este artigo descreve como gerir e usar pacotes Python na Automação Azure.

## <a name="import-packages"></a>Importar pacotes

Na sua conta de Automação, selecione **pacotes Python 2** em **Recursos Partilhados.** Clique **+ Adicione um pacote Python 2**.

![Adicione o pacote Python](media/python-packages/add-python-package.png)

Na página pacote Add Python 2, selecione um pacote local para fazer upload. O pacote pode ser um ficheiro **.whl** ou **.tar.gz.** Quando o pacote for selecionado, clique em **OK** para carregá-lo.

![Adicione o pacote Python](media/python-packages/upload-package.png)

Uma vez importado um pacote, está listado na página de pacotes Python 2 na sua conta de Automação. Se precisar de remover um pacote, selecione o pacote e clique em **Eliminar**.

![Lista de pacotes](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Pacotes de importação com dependências

A automação azure não resolve dependências para pacotes Python durante o processo de importação. Há duas formas de importar um pacote com todas as suas dependências. Apenas um dos seguintes passos deve ser usado para importar os pacotes para a sua conta Automation.

### <a name="manually-download"></a>Download manual

Numa máquina de 64 bits do Windows com [Python2.7](https://www.python.org/downloads/release/latest/python2) e [pip](https://pip.pypa.io/en/stable/) instalado, execute o seguinte comando para descarregar um pacote e todas as suas dependências:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Assim que os pacotes forem descarregados, pode importá-los para a sua conta de automação.

### <a name="runbook"></a>Runbook

 Para obter um livro de execução, [importe pacotes Python 2 da pipia para a Azure Automation da](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) galeria para a sua conta Automation. Certifique-se de que as Definições de Execução estão definidas para **Azure** e inicie o livro de execução com os parâmetros. O livro de execução requer uma Conta De execução Como conta para a conta de Automação funcionar. Para cada parâmetro, certifique-se de que o inicia com o interruptor, tal como visto na lista e imagem seguintes:

* -s \< subscriçãoId\>
* -g \< recursosGroup\>
* -a \< automatizaçãoAccount\>
* módulo \< -mPacote\>

![Lista de pacotes](media/python-packages/import-python-runbook.png)

O livro de recortes permite especificar qual o pacote a descarregar. Por exemplo, a utilização do `Azure` parâmetro descarrega todos os módulos Azure e todas as dependências (cerca de 105).

Uma vez que o livro de execução esteja completo, pode verificar os **pacotes Python 2** em **Recursos Partilhados** na sua conta Automation para verificar se o pacote foi importado corretamente.

## <a name="use-a-package-in-a-runbook"></a>Use um pacote em um livro de corridas

Com um pacote importado, pode usá-lo num livro de corridas. O exemplo seguinte utiliza o [pacote de utilidade supor Automação Azure](https://github.com/azureautomation/azure_automation_utility). Este pacote facilita a utilização da Python com a Automação Azure. Para utilizar a embalagem, siga as instruções no repositório GitHub e adicione-a ao livro de execução. Por exemplo, pode utilizar `from azure_automation_utility import get_automation_runas_credential` para importar a função para recuperar a conta Run As.

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

## <a name="develop-and-test-runbooks-offline"></a>Desenvolver e testar livros offline

Para desenvolver e testar os seus livros de execução Python 2 offline, pode utilizar o módulo de [ativos emulados da Azure Automation Python](https://github.com/azureautomation/python_emulated_assets) no GitHub. Este módulo permite-lhe fazer referência aos seus recursos partilhados, tais como credenciais, variáveis, ligações e certificados.

## <a name="next-steps"></a>Passos seguintes

[Criar um runbook de Python](learn/automation-tutorial-runbook-textual-python2.md)
