---
title: Gerir pacotes Python 3 em Azure Automation
description: Este artigo diz como gerir pacotes Python 3 (pré-visualização) na Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3f39f49ff47b935da7ffc777ee45bd219f5740b5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734306"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Gerir pacotes Python 3 (pré-visualização) em Azure Automation

A Azure Automation permite-lhe executar livros python 3 (pré-visualização) no Azure e no Linux Hybrid Runbook Workers. Para ajudar na simplificação dos livros de execução, pode utilizar pacotes Python para importar os módulos de que necessita. Este artigo descreve como gerir e utilizar pacotes Python 3 (pré-visualização) na Azure Automation.

## <a name="import-packages"></a>Importar pacotes

Na sua conta Automation, selecione **pacotes Python** em Recursos **Partilhados.** selecionar **+ Adicionar um pacote Python**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Screenshot da página de pacotes Python 3 mostra pacotes Python 3 no menu esquerdo e Adicionar um pacote Python 2 em destaque.":::

Na página **'Adicionar Pacote Python',** selecione Python 3 para a **versão** e selecione um pacote local para carregar. O pacote pode ser um ficheiro **.whl** ou **.tar.gz.** Quando a embalagem for selecionada, selecione **OK** para o carregar.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="A screenshot mostra a página do Pacote Add Python 3 com um ficheiro de alcatrão .gz carregado selecionado.":::

Uma vez importado um pacote, está listado na página de pacotes Python na sua conta Automation, no separador **Python 3 (pré-visualização).** Se precisar de remover uma embalagem, selecione a embalagem e clique em **Eliminar**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="A screenshot mostra a página de pacotes Python 3 depois de um pacote ter sido importado.":::

## <a name="import-packages-with-dependencies"></a>Pacotes de importação com dependências

A Azure Automation não resolve as dependências dos pacotes Python durante o processo de importação. No entanto, existe uma forma de importar um pacote com todas as suas dependências.

### <a name="manually-download"></a>Download manual

Numa máquina Windows 64-bit com [Python 3.8](https://www.python.org/downloads/release/python-380/) e [pip](https://pip.pypa.io/en/stable/) instalado, executar o seguinte comando para descarregar um pacote e todas as suas dependências:

```cmd
C:\Python38\Scripts\pip3.8.exe download -d <output dir> <package name>
```

Assim que os pacotes forem descarregados, pode importá-los para a sua conta Automation.

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
