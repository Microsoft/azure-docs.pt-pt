---
title: Gerir pacotes Python 2 em Azure Automation
description: Este artigo diz como gerir pacotes Python 2 na Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 1ab0b2def1a22470c1d0b6339e1525cd683b4a0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987573"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gerir pacotes Python 2 em Azure Automation

A Azure Automation permite-lhe executar livros Python 2 em Azure e em Linux Hybrid Runbook Workers. Para ajudar na simplificação dos livros de execução, pode utilizar pacotes Python para importar os módulos de que necessita. Este artigo descreve como gerir e utilizar pacotes Python na Azure Automation.

## <a name="import-packages"></a>Importar pacotes

Na sua conta Automation, selecione **Pacotes Python 2** em **Recursos Partilhados.** Clique **+ Adicione um pacote Python 2**.

:::image type="content" source="media/python-packages/add-python-package.png" alt-text="Screenshot da página de pacotes Python 2 mostra pacotes Python 2 no menu esquerdo e Adicionar um pacote Python 2 em destaque.":::

Na página 'Adicionar Python 2 Package', selecione um pacote local para carregar. A embalagem pode ser um ficheiro **.whl** ou **.tar.gz.** Quando o pacote for selecionado, clique **em OK** para o fazer o upload.

:::image type="content" source="media/python-packages/upload-package.png" alt-text="Screenshot da página de pacotes Python 2 mostra pacotes Python 2 no menu esquerdo e Adicionar um pacote Python 2 em destaque.":::

Uma vez importado um pacote, está listado na página de pacotes Python 2 na sua conta de Automação. Se precisar de remover uma embalagem, selecione a embalagem e clique em **Eliminar**.

:::image type="content" source="media/python-packages/package-list.png" alt-text="Screenshot da página de pacotes Python 2 mostra pacotes Python 2 no menu esquerdo e Adicionar um pacote Python 2 em destaque.":::

## <a name="import-packages-with-dependencies"></a>Pacotes de importação com dependências

A automação azul não resolve dependências para pacotes Python durante o processo de importação. Há duas formas de importar um pacote com todas as suas dependências. Apenas um dos seguintes passos deve ser usado para importar os pacotes para a sua conta Demôm automação.

### <a name="manually-download"></a>Download manual

Numa máquina Windows 64 bits com [Python2.7](https://www.python.org/downloads/release/latest/python2) e [pip](https://pip.pypa.io/en/stable/) instalado, executar o seguinte comando para descarregar um pacote e todas as suas dependências:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Assim que os pacotes forem descarregados, pode importá-los para a sua conta de automação.

### <a name="runbook"></a>Runbook

 Para obter um livro de bordo, [importe os pacotes Python 2 da conta pypi para a Azure Automation](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) da galeria para a sua conta Automation. Certifique-se de que as Definições de Execução estão definidas para **Azure** e inicie o livro de recortes com os parâmetros. O livro requer uma execução Como conta para a conta Dem automação funcionar. Para cada parâmetro certifique-se de que o inicia com o interruptor como visto na lista e imagem seguintes:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* - um \<automationAccount\>
* -m \<modulePackage\>

:::image type="content" source="media/python-packages/import-python-runbook.png" alt-text="Screenshot da página de pacotes Python 2 mostra pacotes Python 2 no menu esquerdo e Adicionar um pacote Python 2 em destaque.":::

O livro de aplicação permite especificar que pacote descarregar. Por exemplo, a utilização do `Azure` parâmetro descarrega todos os módulos Azure e todas as dependências (cerca de 105).

Uma vez que o livro de execução esteja completo, pode verificar os **pacotes Python 2** em **Recursos Partilhados** na sua conta de Automação para verificar se a embalagem foi importada corretamente.

## <a name="use-a-package-in-a-runbook"></a>Use um pacote em um livro de corridas

Com um pacote importado, pode usá-lo num livro de bordo. O exemplo a seguir utiliza o [pacote de utilitário Azure Automation](https://github.com/azureautomation/azure_automation_utility). Este pacote facilita a utilização de Python com Azure Automation. Para utilizar a embalagem, siga as instruções do repositório GitHub e adicione-a ao livro de recortes. Por exemplo, pode utilizar `from azure_automation_utility import get_automation_runas_credential` para importar a função para recuperar a conta Run As.

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

## <a name="develop-and-test-runbooks-offline"></a>Desenvolver e testar livros de corrida offline

Para desenvolver e testar os seus runbooks Python 2 offline, pode utilizar o módulo [de ativos emulado Azure Automation Python](https://github.com/azureautomation/python_emulated_assets) no GitHub. Este módulo permite-lhe fazer referência aos seus recursos partilhados, tais como credenciais, variáveis, ligações e certificados.

## <a name="next-steps"></a>Passos seguintes

Para preparar um livro de bordo python, consulte [Criar um livro de bordo Python](learn/automation-tutorial-runbook-textual-python2.md).
