---
title: Gerir pacotes Python 3 em Azure Automation
description: Este artigo diz como gerir pacotes Python 3 (pré-visualização) na Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/19/2021
ms.topic: conceptual
ms.openlocfilehash: fd4d8ee92b670bc2544619a0dce16a26d9342c13
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122039"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Gerir pacotes Python 3 (pré-visualização) em Azure Automation

A Azure Automation permite-lhe executar livros python 3 (pré-visualização) no Azure e no Linux Hybrid Runbook Workers. Para ajudar na simplificação dos livros de execução, pode utilizar pacotes Python para importar os módulos de que necessita. Para importar um único pacote, consulte [importar um pacote.](#import-a-package) Para importar um pacote com vários pacotes, consulte [Importar um pacote com dependências.](#import-a-package-with-dependencies) Este artigo descreve como gerir e utilizar pacotes Python 3 (pré-visualização) na Azure Automation.

## <a name="import-a-package"></a>Importar um pacote

Na sua conta Automation, selecione **pacotes Python** em Recursos **Partilhados.** selecionar **+ Adicionar um pacote Python**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Screenshot da página de pacotes Python 3 mostra pacotes Python 3 no menu esquerdo e Adicionar um pacote Python 2 em destaque.":::

Na página **'Adicionar Pacote Python',** selecione **Python 3** para a **versão** e selecione um pacote local para carregar. O pacote pode ser um ficheiro **.whl** ou **.tar.gz.** Quando a embalagem for selecionada, selecione **OK** para o carregar.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="A screenshot mostra a página do Pacote Add Python 3 com um ficheiro de alcatrão .gz carregado selecionado.":::

Uma vez importado um pacote, está listado na página de pacotes Python na sua conta Automation, no separador **Python 3 (pré-visualização).** Se precisar de remover uma embalagem, selecione a embalagem e clique em **Eliminar**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="A screenshot mostra a página de pacotes Python 3 depois de um pacote ter sido importado.":::

### <a name="import-a-package-with-dependencies"></a>Importar um pacote com dependências

Você pode importar um pacote Python 3 e suas dependências importando o seguinte script Python em um livro python 3, e depois executá-lo.

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>Importando o guião em um livro de bordo
Para obter informações sobre a importação do livro de bordo, consulte [Importar um livro de bordo do portal Azure.](manage-runbooks.md#import-a-runbook-from-the-azure-portal) Copie o ficheiro do GitHub para o armazenamento a que o portal pode aceder antes de executar a importação.

A página **De Importação de um livro de contas** predefine o nome do livro de execução para corresponder ao nome do script. Se tiver acesso ao campo, pode alterar o nome. **O tipo de runbook** pode passar por defeito para **Python 2**. Se o fizer, certifique-se de alterá-lo para **Python 3**.

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="A screenshot mostra a página de importação de livros de bordo Python 3.":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>Execução do livro de bordo para importar o pacote e dependências

Depois de criar e publicar o livro de bordo, execute-o para importar o pacote. Consulte [iniciar um livro de recortes na Azure Automation](start-runbooks.md) para obter mais informações sobre a execução do livro de recortes.

O script requer `import_py3package_from_pypi.py` os seguintes parâmetros.

| Parâmetro | Descrição |
|---------------|-----------------|
|subscription_id | ID de assinatura da conta Automation |
| resource_group | Nome do grupo de recursos que a conta Automation é definida em |
| automation_account | Nome da conta de automação |
| module_name | Nome do módulo para importar a partir de `pypi.org` |

Para obter mais informações sobre a utilização de parâmetros com livros de recortes, consulte [Trabalhar com parâmetros de runbook](start-runbooks.md#work-with-runbook-parameters).

## <a name="use-a-package-in-a-runbook"></a>Use um pacote em um livro de corridas

Com o pacote importado, pode usá-lo num livro de contas. Adicione o seguinte código para listar todos os grupos de recursos numa subscrição do Azure.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

## <a name="next-steps"></a>Passos seguintes

Para preparar um livro de bordo python, consulte [Criar um livro de bordo Python](learn/automation-tutorial-runbook-textual-python-3.md).
