---
title: O meu primeiro livro python em Automação Azure
description: Tutorial que o acompanha através da criação, teste e publicação de um simples livro de corridas python.
services: automation
ms.subservice: process-automation
ms.date: 03/19/2019
ms.topic: conceptual
ms.openlocfilehash: b17a0403a3b2a3ff8c3586ed26a4b833db54922d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75365942"
---
# <a name="my-first-python-runbook"></a>O meu primeiro runbook do Python

> [!div class="op_single_selector"]
> - [Gráficos](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> - [Pitão](automation-first-runbook-textual-python2.md)

Este tutorial acompanha-o através da criação de um [livro](automation-runbook-types.md#python-runbooks) python em Automação Azure. Começa-se com um simples livro de corridas que testa e publica. Em seguida, modifique o runbook para gerir recursos do Azure, neste caso, iniciar uma máquina virtual do Azure. Por último, torna o livro de corridas mais robusto adicionando parâmetros de livro.

> [!NOTE]
> Não é suportado o uso de um webhook para iniciar um livro de execução python.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

- Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Conta de automatização](automation-offering-get-started.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
- Uma máquina virtual do Azure. O utilizador para e inicia esta máquina, pelo que não deve ser uma VM de produção.

## <a name="create-a-new-runbook"></a>Criar um novo livro de corridas

Começa-se por criar um livro simples que produz o texto *Hello World.*

1. No portal do Azure, abra a sua conta da Automatização.

    A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria destes ativos são os módulos que estão incluídos automaticamente na nova conta da Automatização. Também deverá ter o recurso da Credencial que está mencionado nos [Pré-requisitos](#prerequisites).<br>

1. Selecione **Runbooks** em **GESTÃO DE PROCESSOS** para abrir a lista de runbooks.
1. Selecione **+ Adicione um livro de execução** para criar um novo livro de execução.
1. Dê ao livro de corridas o nome *MyFirstRunbook-Python*.
1. Neste caso, você vai criar um livro de [execução Python](automation-runbook-types.md#python-runbooks) para selecionar **Python 2** para o **tipo Runbook**.
1. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="add-code-to-the-runbook"></a>Adicione código ao livro de execução

Agora adicione um simples comando para imprimir o texto "Hello World":

```python
print("Hello World!")
```

Clique em **Guardar** para guardar o livro de execução.

## <a name="test-the-runbook"></a>Testar o runbook

Antes de publicar o runbook para o disponibilizar na produção, deve testá-lo para garantir que funciona corretamente. Quando testa um runbook, executa a versão de **Rascunho** e vê o resultado de forma interativa.

1. Clique em **Painel de teste** para abrir o Painel de teste.
1. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.
1. Uma [tarefa do runbook](automation-runbook-execution.md) é criada e o respetivo estado é apresentado.
   O estado da tarefa começa como *Em fila* com a indicação de que está à espera que uma função de trabalho de runbook na cloud fique disponível. Passa para *começar* quando um trabalhador reclama o trabalho, e depois *correr* quando o livro começa a funcionar.
1. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. Neste caso, devias ver *a Hello World.*
1. Feche o painel de Teste para voltar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o livro de corridas

O livro de execução que criou ainda está em modo de projeto. Tens de publicá-lo antes de o conseguires executar em produção.
Quando publica um livro de execução, substitui a versão publicada existente com a versão Draft.
Neste caso, ainda não tem uma versão publicada porque acabou de criar o livro de corridas.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.
1. Se rolar para a esquerda para ver o livro de execução no painel **de Livros de Execução** agora, mostra um Estatuto de **Autor** de **Publicado**.
1. Percorra para a direita para ver o painel **myFirstRunbook-Python**.
   As opções em cima permitem-nos iniciar o livro de corridas, ver o livro de corridas ou programar-lo para começar em algum momento no futuro.
2. Se pretender iniciar o livro de execução, clique em **Iniciar** e, em seguida, clique em **Ok** quando a lâmina start Runbook abrir.
3. Está aberta uma vidraça para o trabalho de livro que criou. Pode fechar este painel, mas neste caso, deixe-o aberto para que possa ver o progresso do trabalho.
1. O estado de trabalho é mostrado no Resumo de **Trabalho** e corresponde aos estados que viu quando testou o livro de execução.
2. Quando o estado de runbook mostrar *Concluído*, clique em **Resultado**. O painel de saída está aberto e você pode ver o seu *Hello World*.
3. Feche o painel Resultado.
4. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Apenas deve conseguir ver *Hello World* no fluxo de saída, mas isto pode mostrar outros fluxos de uma tarefa de runbook, tais como Verboso e Erro se o runbook escrever nos mesmos.
5. Feche o painel de Streams e o painel de trabalho para voltar ao painel MyFirstRunbook-Python.
6. Clique em **Tarefas** para abrir o painel Tarefas para este runbook. Isto apresenta uma lista de todas as tarefas criadas por este runbook. Apenas deve conseguir ver uma tarefa listada, uma vez que apenas executou a tarefa uma vez.
7. Pode clicar nesta tarefa para abrir o mesmo painel Tarefas que visualizou quando iniciou o runbook. Isto permite-lhe voltar atrás no tempo e ver os detalhes de qualquer tarefa que foi criada para um determinado runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Adicionar autenticação para gerir recursos Azure

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure.
Para gerir os recursos do Azure, o script tem de autenticar utilizando as credenciais da sua Conta de Automação. Para o ajudar, pode utilizar o pacote de utilidade supor [A Automatização Azure](https://github.com/azureautomation/azure_automation_utility) para facilitar a autenticação e interagir com os recursos do Azure.

> [!NOTE]
> A conta Automation deve ter sido criada com a funcionalidade principal do serviço para que exista um Certificado De Execução Como Certificado.
> Se a sua conta de automação não foi criada com o diretor de serviço, pode autenticar utilizando o método descrito na [Authenticate com as Bibliotecas de Gestão Azure para Python](/azure/python/python-sdk-azure-authenticate).

1. Abra o editor textual clicando em **Editar** no painel MyFirstRunbook-Python.

2. Adicione o seguinte código para autenticar o Azure:

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

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Adicione código para criar o cliente Python Compute e inicie o VM

Para trabalhar com os VMs Azure, crie uma instância do [cliente azure compute para python](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Use o cliente Compute para iniciar o VM. Adicione o seguinte código ao livro de execução:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Onde o _MyResourceGroup_ é o nome do grupo de recursos que contém o VM, e _testVM_ é o nome do VM que pretende iniciar.

Teste e volte a executar o livro de corridas para ver se inicia o VM.

## <a name="use-input-parameters"></a>Utilizar parâmetros de entrada

O livro de execução utiliza atualmente valores codificados para os nomes do Grupo de Recursos e do VM.
Agora vamos adicionar código que obtém estes valores a partir de parâmetros de entrada.

Usa-se `sys.argv` a variável para obter os valores dos parâmetros.
Adicione o seguinte código ao livro de `import` execução imediatamente após as outras declarações:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Isto importa `sys` o módulo, e cria duas variáveis para conter os nomes do Grupo de Recursos e VM.
Note que o elemento `sys.argv[0]`da lista de argumentos, é o nome do script, e não é a entrada do utilizador.

Agora pode modificar as duas últimas linhas do livro de execução para utilizar os valores do parâmetro de entrada em vez de utilizar valores codificados:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Quando iniciar um livro de execução Python (na página **de Teste** ou como um livro de execução publicado), pode introduzir os valores para parâmetros na página **Start Runbook** em **Parâmetros**.

Depois de começar a introduzir um valor na primeira caixa, aparecerá um segundo, e assim por diante, para que possa introduzir o maior número de valores de parâmetros necessários.

Os valores estão disponíveis `sys.argv` para o script como a matriz como no código que acabou de adicionar.

Insira o nome do seu grupo de recursos como o valor do primeiro parâmetro, e o nome do VM comece como o valor do segundo parâmetro.

![Insira os valores dos parâmetros](media/automation-first-runbook-textual-python/runbook-python-params.png)

Clique **em OK** para iniciar o livro de corridas. O livro de corridas funciona e inicia o VM que especificou.

## <a name="error-handling-in-python"></a>Manipulação de erros em Python

Também pode utilizar as seguintes convenções para recuperar vários fluxos dos seus livros de execução Python, incluindo **AVISO,** **ERRO**e streams **DEBUG.**

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

O exemplo que se segue `try...except` mostra esta convenção utilizada num bloco.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> **sys.stderr** não é suportado na Automação Azure.

## <a name="next-steps"></a>Passos seguintes

- Para começar com runbooks do PowerShell, consulte [O meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
- Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
- Para começar com os livros de workflow powerShell, consulte o meu primeiro livro de corridas de fluxo de [trabalho PowerShell](automation-first-runbook-textual.md)
- Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
- Para aprender sobre o desenvolvimento para Azure com Python, consulte [Azure para desenvolvedores python](/azure/python/)
- Para ver a amostra de livros de execução Python 2, consulte o [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
