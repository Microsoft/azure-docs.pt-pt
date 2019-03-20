---
title: O meu primeiro runbook de Python na automatização do Azure
description: Tutorial explica-lhe a criação, teste e da publicação de um runbook de Python simples.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e79f4b58582ab6643a7a13ffee25503060a2208c
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226747"
---
# <a name="my-first-python-runbook"></a>O meu primeiro runbook de Python

> [!div class="op_single_selector"]
> - [Gráficos](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Este tutorial explica-lhe a criação de um [runbook de Python](automation-runbook-types.md#python-runbooks) na automatização do Azure. Comece com um runbook simples que testar e publicar. Em seguida, modifique o runbook para gerir recursos do Azure, neste caso, iniciar uma máquina virtual do Azure. Por último, tornar o runbook mais robusto ao adicionar parâmetros do runbook.

> [!NOTE]
> Não é suportada a utilizar um webhook para iniciar um runbook de Python.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

- Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Conta de automatização](automation-offering-get-started.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
- Uma máquina virtual do Azure. O utilizador para e inicia esta máquina, pelo que não deve ser uma VM de produção.

## <a name="create-a-new-runbook"></a>Criar um novo runbook

Comece por criar um runbook simples que produz o texto *Olá, mundo*.

1. No portal do Azure, abra a sua conta da Automatização.

    A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria destes ativos são os módulos que estão incluídos automaticamente na nova conta da Automatização. Também deverá ter o recurso da Credencial que está mencionado nos [Pré-requisitos](#prerequisites).<br>

1. Selecione **Runbooks** em **GESTÃO DE PROCESSOS** para abrir a lista de runbooks.
1. Selecione **+ adicionar um runbook** para criar um novo runbook.
1. Atribua ao runbook o nome *MyFirstRunbook-Python*.
1. Neste caso, vamos criar um [runbook de Python](automation-runbook-types.md#python-runbooks) por isso, selecione **Python 2** para **tipo de Runbook**.
1. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="add-code-to-the-runbook"></a>Adicionar código ao runbook

Agora adicionar um comando simples para imprimir o texto "Hello World":

```python
print("Hello World!")
```

Clique em **guardar** para guardar o runbook.

## <a name="test-the-runbook"></a>Testar o runbook

Antes de publicar o runbook para o disponibilizar na produção, deve testá-lo para garantir que funciona corretamente. Quando testa um runbook, executa a versão de **Rascunho** e vê o resultado de forma interativa.

1. Clique em **Painel de teste** para abrir o Painel de teste.
1. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.
1. Uma [tarefa do runbook](automation-runbook-execution.md) é criada e o respetivo estado é apresentado.
   O estado da tarefa começa como *Em fila* com a indicação de que está à espera que uma função de trabalho de runbook na cloud fique disponível. Move para *inicial* quando uma função de trabalho reivindicar a tarefa e, em seguida *em execução* quando o runbook começa a ser executado.
1. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. Neste caso, verá *Olá, mundo*.
1. Feche o painel de Teste para voltar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o runbook

O runbook que criou ainda está no modo de rascunho. Tem de publicá-lo antes de pode executá-lo em produção.
Quando publica um runbook, substitui a versão publicada existente pela versão de rascunho.
Neste caso, ainda não tem uma versão publicada porque acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.
1. Se se deslocar para a esquerda para ver o runbook no **Runbooks** painel, ele mostra agora uma **estado de criação** de **publicada**.
1. Desloque-se para a direita para ver o painel para **MyFirstRunbook-Python**.
   As opções na parte superior permitem-nos iniciar o runbook, ver o runbook ou agendá-la para iniciar em algum momento no futuro.
2. Pretende iniciar o runbook, por isso, clique em **começar** e, em seguida, clique em **Ok** quando o Painel Iniciar Runbook for aberto.
3. É aberto um painel de tarefas para a tarefa de runbook que criou. Pode fechar este painel, mas neste caso, deixá-lo aberto para que possa ver o progresso da tarefa.
1. O estado da tarefa é mostrado na **resumo da tarefa** e corresponde aos Estados que vimos quando testado o runbook.
2. Quando o estado de runbook mostrar *Concluído*, clique em **Resultado**. É aberto o painel de resultados, e pode ver seus *Olá, mundo*.
3. Feche o painel Resultado.
4. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Apenas deve conseguir ver *Hello World* no fluxo de saída, mas isto pode mostrar outros fluxos de uma tarefa de runbook, tais como Verboso e Erro se o runbook escrever nos mesmos.
5. Feche o painel de fluxos e o painel de tarefas para regressar ao painel MyFirstRunbook-Python.
6. Clique em **Tarefas** para abrir o painel Tarefas para este runbook. Isto apresenta uma lista de todas as tarefas criadas por este runbook. Apenas deve conseguir ver uma tarefa listada, uma vez que apenas executou a tarefa uma vez.
7. Pode clicar nesta tarefa para abrir o mesmo painel Tarefas que visualizou quando iniciou o runbook. Isto permite-lhe voltar atrás no tempo e ver os detalhes de qualquer tarefa que foi criada para um determinado runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Adicionar autenticação para gerir recursos do Azure

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure.
Para gerir recursos do Azure, o script tem de autenticar com as credenciais da sua conta de automatização. Para ajudá-lo, pode utilizar o [pacote de utilitário de automatização do Azure](https://github.com/azureautomation/azure_automation_utility) para facilitar a autenticar e interagir com os recursos do Azure.

> [!NOTE]
> A conta de automatização necessário tenham sido criada com a funcionalidade principal de serviço de haver um executar como certificado.
> Se a sua conta de automatização não foi criada com o principal de serviço, pode autenticar ao utilizar o método descrito em [autenticar com as bibliotecas de gestão do Azure para Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Abra o editor de texto ao clicar em **editar** no painel MyFirstRunbook-Python.

2. Adicione o seguinte código para autenticar para o Azure:

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
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
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Adicionar código para criar o cliente Python de computação e iniciar a VM

Para trabalhar com VMs do Azure, crie uma instância do [cliente de computação do Azure para Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python).

Utilize o cliente de computação para iniciar a VM. Adicione o seguinte código ao runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Em que _MyResourceGroup_ é o nome do grupo de recursos que contém a VM, e _TestVM_ é o nome da VM que pretende iniciar.

Testar e executar o runbook novamente para ver o que ela começa a VM.

## <a name="use-input-parameters"></a>Usar parâmetros de entrada

Atualmente, o runbook utiliza os valores codificados para os nomes de grupo de recursos e a VM.
Agora vamos adicionar código que obtém estes valores de parâmetros de entrada.

Utilizar o `sys.argv` variável para obter os valores de parâmetro.
Adicione o seguinte código ao runbook imediatamente após o outro `import` instruções:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Esta ação importa o `sys` módulo e cria duas variáveis para armazenar os nomes de grupo de recursos e a VM.
Tenha em atenção que o elemento da lista de argumentos, `sys.argv[0]`, é o nome do script e não é a entrada pelo utilizador.

Agora pode modificar as duas últimas linhas do runbook para utilizar os valores de parâmetro de entrada em vez de usar valores embutidos:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

Quando inicia um runbook de Python (no **teste** página ou como um runbook publicado), pode introduzir os valores para parâmetros na **iniciar Runbook** página sob **parâmetros** .

Depois de começar a introduzir um valor na primeira caixa, um segundo irá aparecer e assim por diante, para que pode inserir tantos valores de parâmetro conforme necessário.

Os valores estão disponíveis para o script como o `sys.argv` matriz como no código que acabou de adicionar.

Introduza o nome do seu grupo de recursos como o valor para o primeiro parâmetro e o nome da VM para começar a como o valor do segundo parâmetro.

![Introduza os valores de parâmetro](media/automation-first-runbook-textual-python/runbook-python-params.png)

Clique em **OK** para iniciar o runbook. O runbook é executado e inicia a VM que especificou.

## <a name="next-steps"></a>Passos Seguintes

- Para começar com runbooks do PowerShell, consulte [O meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
- Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
- Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
- Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
- Para saber mais sobre o desenvolvimento do Azure com Python, veja [do Azure para programadores de Python](https://docs.microsoft.com/python/azure/?view=azure-python)
- Para ver os runbooks de exemplo do Python 2, consulte o [GitHub de automatização do Azure](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
