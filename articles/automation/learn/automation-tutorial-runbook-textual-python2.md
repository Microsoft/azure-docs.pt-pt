---
title: Criar um livro de corridas Python na Automação Azure
description: Tutorial mostrando como criar, testar e publicar um simples livro de corridas python.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: 116f2544b23ed5f9bc0fabbb945cb5cb2b51af96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81726285"
---
# <a name="tutorial-create-a-python-runbook"></a>Tutorial: Criar um livro de corridas python

Este tutorial acompanha-o através da criação de um [livro](../automation-runbook-types.md#python-runbooks) python em Automação Azure. Os livros de python compilam sob python 2. Pode editar diretamente o código do livro de execução utilizando o editor de texto no portal Azure.

> [!div class="checklist"]
> * Crie um simples livro de corridas Python
> * Teste e publique o livro de corridas
> * Executar e rastrear o estado do trabalho do livro de corridas
> * Atualize o livro de corridas para iniciar uma máquina virtual Azure com parâmetros de livro

> [!NOTE]
> Não é suportado o uso de um webhook para iniciar um livro de execução python.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

- Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Conta de automatização](../automation-offering-get-started.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
- Uma máquina virtual do Azure. O utilizador para e inicia esta máquina, pelo que não deve ser uma VM de produção.

## <a name="create-a-new-runbook"></a>Criar um novo livro de corridas

Começa-se por criar um livro simples que produz o texto *Hello World.*

1. No portal do Azure, abra a sua conta da Automatização.

    A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria destes ativos são os módulos que estão incluídos automaticamente na nova conta da Automatização. Também deverá ter o recurso da Credencial que está mencionado nos [Pré-requisitos](#prerequisites).

2. Selecione **Runbooks** em **Process Automation** para abrir a lista de livros de execução.

3. Selecione **Adicionar um livro de execução** para criar um novo livro de execução.

4. Dê ao livro de corridas o nome **MyFirstRunbook-Python**.

5. Selecione **Python 2** para **o tipo Derbook**.

6. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="add-code-to-the-runbook"></a>Adicione código ao livro de execução

Agora adicione um simples comando `Hello World`para imprimir o texto .

```python
print("Hello World!")
```

Clique em **Guardar** para guardar o livro de execução.

## <a name="test-the-runbook"></a>Testar o runbook

Antes de publicar o runbook para o disponibilizar na produção, deve testá-lo para garantir que funciona corretamente. Quando testa um runbook, executa a versão de Rascunho e vê o resultado de forma interativa.

1. Clique em **Painel de teste** para abrir o Painel de teste.

2. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.

3. Uma [tarefa do runbook](../automation-runbook-execution.md) é criada e o respetivo estado é apresentado.
   O estado de trabalho começa como Fila, indicando que está à espera que um trabalhador do livro na nuvem esteja disponível. Passa para começar quando um trabalhador reclama o trabalho, e depois correr quando o livro começa a funcionar.

4. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. Neste caso, deve `Hello World`ver.

5. Feche o painel de Teste para voltar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o livro de corridas

O runbook que criou ainda está no modo de Rascunho. Tens de publicá-lo antes de o conseguires executar em produção.
Quando publica um livro de execução, substitui a versão publicada existente com a versão do projeto.
Neste caso, ainda não tem uma versão publicada porque acabou de criar o livro de corridas.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.

2. Se rolar para a esquerda para ver o livro de execução na página **Runbooks,** deve ver um Estatuto de **Autor** de **Publicado**.

3. Percorra para a direita para ver o painel **myFirstRunbook-Python**.

   As opções em toda a parte superior permitem-lhe iniciar o livro de corridas, ver o livro de corridas ou agendar-o para começar em algum momento no futuro.

4. Clique em **Iniciar** e, em seguida, clique em **OK** quando a lâmina iniciar o livro de corridas abrir.

5. Um painel de trabalho está aberto para o trabalho de livro que criou. Pode fechar este painel, mas vamos deixá-lo aberto para que possa ver o progresso do trabalho.

6. O estado de trabalho é mostrado no Resumo de **Trabalho** e corresponde aos estados que viu quando testou o livro de execução.

7. Assim que o estado do livro de execução mostrar concluído, clique em **Saída**. O painel de saída está aberto, onde pode ver `Hello World`.

8. Feche o painel Resultado.

9. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Só deve `Hello World` ver na corrente de saída. No entanto, este painel pode mostrar outros fluxos para um trabalho de livro de corridas, como Verbose e Error, se o livro de execução lhes escrever.

10. Feche o painel de Streams e o painel de trabalho para voltar ao painel MyFirstRunbook-Python.

11. Clique em **Jobs** para abrir a página Jobs para este livro de execução. Esta página lista todos os empregos criados por este livro de corridas. Apenas deve conseguir ver uma tarefa listada, uma vez que apenas executou a tarefa uma vez.

12. Pode clicar nesta tarefa para abrir o mesmo painel Tarefas que visualizou quando iniciou o runbook. Este painel permite-lhe recuar no tempo e ver os detalhes de qualquer trabalho que tenha sido criado para um livro de corridas específico.

## <a name="add-authentication-to-manage-azure-resources"></a>Adicionar autenticação para gerir recursos Azure

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure.
Para isso, o script tem de autenticar utilizando as credenciais da sua conta Automation. Para o ajudar, pode utilizar o pacote de utilidade supor [A Automatização Azure](https://github.com/azureautomation/azure_automation_utility) para facilitar a autenticação e interagir com os recursos do Azure.

> [!NOTE]
> A conta Automation deve ter sido criada com a funcionalidade principal do serviço para que exista um certificado Run As.
> Se a sua conta De automação não foi criada com o diretor de serviço, pode autenticar como descrito em [Authenticate com as Bibliotecas de Gestão Azure para Python](/azure/python/python-sdk-azure-authenticate).

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

Use o cliente computacional para iniciar o VM. Adicione o seguinte código ao livro de execução:

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

Onde `MyResourceGroup` está o nome do grupo de recursos `TestVM` que contém o VM, e é o nome do VM que pretende iniciar.

Teste e volte a executar o livro de corridas para ver se inicia o VM.

## <a name="use-input-parameters"></a>Utilizar parâmetros de entrada

O livro de execução utiliza atualmente valores codificados para os nomes do grupo de recursos e do VM. Agora vamos adicionar código que obtém estes valores a partir de parâmetros de entrada.

Usa-se `sys.argv` a variável para obter os valores dos parâmetros. Adicione o seguinte código ao livro de `import` execução imediatamente após as outras declarações:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Isto importa `sys` o módulo, e cria duas variáveis para conter os nomes do Grupo de Recursos e VM. Note que o elemento `sys.argv[0]`da lista de argumentos, é o nome do script, e não é a entrada do utilizador.

Agora pode modificar as duas últimas linhas do livro de execução para utilizar os valores do parâmetro de entrada em vez de utilizar valores codificados:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Quando iniciar um livro de execução Python (seja no painel de teste ou como um livro de execução publicado), pode introduzir os valores para parâmetros na página Start Runbook em **Parâmetros**.

Depois de começar a inserir um valor na primeira caixa, aparece um segundo, e assim por diante, para que possa introduzir o maior número de valores de parâmetros necessários.

Os valores estão disponíveis `sys.argv` para o script na matriz como no código que acabou de adicionar.

Insira o nome do seu grupo de recursos como o valor do primeiro parâmetro, e o nome do VM comece como o valor do segundo parâmetro.

![Insira os valores dos parâmetros](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

Clique **em OK** para iniciar o livro de corridas. O livro de corridas funciona e inicia o VM que especificou.

## <a name="error-handling-in-python"></a>Manipulação de erros em Python

Também pode utilizar as seguintes convenções para recuperar vários fluxos dos seus livros de execução Python, incluindo avisos, ERROS e streams DEBUG.

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
> A Azure Automation `sys.stderr`não suporta.

## <a name="next-steps"></a>Passos seguintes

- Para começar com os livros de execução da PowerShell, consulte [Create a PowerShell runbook](automation-tutorial-runbook-textual-powershell.md).
- Para começar com livros gráficos, consulte [Criar um livro de execução gráfico](automation-tutorial-runbook-graphical.md).
- Para começar com os livros de fluxo de trabalho PowerShell, consulte [Create a PowerShell workflow runbook](automation-tutorial-runbook-textual.md).
- Para saber mais sobre tipos de livro de corridas, as suas vantagens e limitações, consulte os tipos de livro de [execução da Automação Azure](../automation-runbook-types.md).
- Para aprender sobre o desenvolvimento para Azure com Python, consulte [Azure para desenvolvedores python.](/azure/python/)
- Para visualizar a amostra de livros de execução Python 2, consulte o [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python).
