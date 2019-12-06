---
title: Meu primeiro runbook do Python na automação do Azure
description: Tutorial que orienta você durante a criação, o teste e a publicação de um runbook simples do Python.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 54b008939b3d083769756b9ac3c3c9e3f7aebea5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850283"
---
# <a name="my-first-python-runbook"></a>Meu primeiro runbook do Python

> [!div class="op_single_selector"]
> - [Gráficos](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Este tutorial orienta você durante a criação de um [runbook do Python](automation-runbook-types.md#python-runbooks) na automação do Azure. Você começa com um runbook simples que você testa e publica. Em seguida, modifique o runbook para gerir recursos do Azure, neste caso, iniciar uma máquina virtual do Azure. Por fim, você torna o runbook mais robusto adicionando parâmetros de runbook.

> [!NOTE]
> Não há suporte para o uso de um webhook para iniciar um runbook do Python.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

- Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Conta de automatização](automation-offering-get-started.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
- Uma máquina virtual do Azure. O utilizador para e inicia esta máquina, pelo que não deve ser uma VM de produção.

## <a name="create-a-new-runbook"></a>Criar um novo runbook

Você começa criando um runbook simples que gera o texto *Olá, mundo*.

1. No portal do Azure, abra a sua conta da Automatização.

    A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria destes ativos são os módulos que estão incluídos automaticamente na nova conta da Automatização. Também deverá ter o recurso da Credencial que está mencionado nos [Pré-requisitos](#prerequisites).<br>

1. Selecione **Runbooks** em **GESTÃO DE PROCESSOS** para abrir a lista de runbooks.
1. Selecione **+ Adicionar um runbook** para criar um novo runbook.
1. Dê ao runbook o nome *MyFirstRunbook-Python*.
1. Nesse caso, você criará um [runbook do Python](automation-runbook-types.md#python-runbooks) , portanto, selecione **Python 2** para **tipo de runbook**.
1. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="add-code-to-the-runbook"></a>Adicionar código ao runbook

Agora, você adiciona um comando simples para imprimir o texto "Olá, Mundo":

```python
print("Hello World!")
```

Clique em **salvar** para salvar o runbook.

## <a name="test-the-runbook"></a>Testar o runbook

Antes de publicar o runbook para o disponibilizar na produção, deve testá-lo para garantir que funciona corretamente. Quando testa um runbook, executa a versão de **Rascunho** e vê o resultado de forma interativa.

1. Clique em **Painel de teste** para abrir o Painel de teste.
1. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.
1. Uma [tarefa do runbook](automation-runbook-execution.md) é criada e o respetivo estado é apresentado.
   O estado da tarefa começa como *Em fila* com a indicação de que está à espera que uma função de trabalho de runbook na cloud fique disponível. Ele é movido para *iniciando* quando um trabalho alega o trabalho e, em seguida, é *executado* quando o runbook realmente começa a ser executado.
1. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. Nesse caso, você deve ver *Olá, mundo*.
1. Feche o painel de Teste para voltar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o runbook

O runbook que você criou ainda está no modo de rascunho. Você precisa publicá-lo antes de poder executá-lo em produção.
Ao publicar um runbook, você substitui a versão publicada existente pela versão de rascunho.
Nesse caso, você ainda não tem uma versão publicada porque acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.
1. Se você rolar para a esquerda para exibir o runbook no painel **Runbooks** agora, ele mostrará um **status de criação** **publicado**.
1. Role de volta à direita para exibir o painel para **MyFirstRunbook-Python**.
   As opções na parte superior nos permitem iniciar o runbook, exibir o runbook ou agendá-lo para iniciar em algum momento no futuro.
2. Você deseja iniciar o runbook, portanto, clique em **Iniciar** e em **OK** quando a folha iniciar runbook for aberta.
3. Um painel de trabalho é aberto para o trabalho de runbook que você criou. Você pode fechar esse painel, mas, nesse caso, deixá-lo aberto para que você possa observar o progresso do trabalho.
1. O status do trabalho é mostrado no **Resumo do trabalho** e corresponde aos status que você viu quando testou o runbook.
2. Quando o estado de runbook mostrar *Concluído*, clique em **Resultado**. O painel saída é aberto e você pode ver sua *Olá, mundo*.
3. Feche o painel Resultado.
4. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Apenas deve conseguir ver *Hello World* no fluxo de saída, mas isto pode mostrar outros fluxos de uma tarefa de runbook, tais como Verboso e Erro se o runbook escrever nos mesmos.
5. Feche o painel fluxos e o painel trabalho para retornar ao painel MyFirstRunbook-Python.
6. Clique em **Tarefas** para abrir o painel Tarefas para este runbook. Isto apresenta uma lista de todas as tarefas criadas por este runbook. Apenas deve conseguir ver uma tarefa listada, uma vez que apenas executou a tarefa uma vez.
7. Pode clicar nesta tarefa para abrir o mesmo painel Tarefas que visualizou quando iniciou o runbook. Isto permite-lhe voltar atrás no tempo e ver os detalhes de qualquer tarefa que foi criada para um determinado runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Adicionar autenticação para gerenciar recursos do Azure

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure.
Para gerenciar recursos do Azure, o script precisa se autenticar usando as credenciais de sua conta de automação. Para ajudá-lo, você pode usar o [pacote do utilitário de automação do Azure](https://github.com/azureautomation/azure_automation_utility) para facilitar a autenticação e a interação com os recursos do Azure.

> [!NOTE]
> A conta de automação deve ter sido criada com o recurso de entidade de serviço para que haja um certificado executar como.
> Se sua conta de automação não tiver sido criada com a entidade de serviço, você poderá autenticar usando o método descrito em [autenticar com as bibliotecas de gerenciamento do Azure para Python](/azure/python/python-sdk-azure-authenticate).

1. Abra o editor de texto clicando em **Editar** no painel MyFirstRunbook-Python.

2. Adicione o seguinte código para autenticar no Azure:

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

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Adicionar código para criar o cliente de computação do Python e iniciar a VM

Para trabalhar com VMs do Azure, crie uma instância do [cliente de computação do Azure para Python](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Use o cliente de computação para iniciar a VM. Adicione o seguinte código ao runbook:

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

Em que _MyResource_ Group é o nome do grupo de recursos que contém a VM e _TestVM_ é o nome da VM que você deseja iniciar.

Teste e execute o runbook novamente para ver que ele inicia a VM.

## <a name="use-input-parameters"></a>Usar parâmetros de entrada

Atualmente, o runbook usa valores embutidos em código para os nomes do grupo de recursos e da VM.
Agora, vamos adicionar o código que obtém esses valores dos parâmetros de entrada.

Você usa a variável `sys.argv` para obter os valores de parâmetro.
Adicione o seguinte código ao runbook imediatamente após as outras instruções `import`:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Isso importa o módulo `sys` e cria duas variáveis para armazenar os nomes do grupo de recursos e da VM.
Observe que o elemento da lista de argumentos, `sys.argv[0]`, é o nome do script e não é inserido pelo usuário.

Agora você pode modificar as duas últimas linhas do runbook para usar os valores de parâmetro de entrada em vez de usar valores embutidos em código:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Ao iniciar um runbook do Python (na página de **teste** ou como um runbook publicado), você pode inserir os valores dos parâmetros na página **Iniciar runbook** em **parâmetros**.

Depois que você começar a inserir um valor na primeira caixa, um segundo será exibido, e assim por diante, para que você possa inserir quantos valores de parâmetro forem necessários.

Os valores estão disponíveis para o script como a matriz de `sys.argv` como no código que você acabou de adicionar.

Insira o nome do seu grupo de recursos como o valor do primeiro parâmetro e o nome da VM para iniciar como o valor do segundo parâmetro.

![Inserir valores de parâmetro](media/automation-first-runbook-textual-python/runbook-python-params.png)

Clique em **OK** para iniciar o runbook. O runbook é executado e inicia a VM que você especificou.

## <a name="error-handling-in-python"></a>Tratamento de erros no Python

Você também pode usar as seguintes convenções para recuperar vários fluxos de seus runbooks do Python, incluindo fluxos de **aviso**, **erro**e **depuração** .

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

O exemplo a seguir mostra essa convenção usada em um bloco de `try...except`.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> Não há suporte para **Sys. stderr** na automação do Azure.

## <a name="next-steps"></a>Passos seguintes

- Para começar com runbooks do PowerShell, consulte [O meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
- Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
- Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
- Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
- Para saber mais sobre como desenvolver para o Azure com Python, confira [Azure para desenvolvedores de Python](/azure/python/)
- Para exibir os runbooks de exemplo do Python 2, consulte o [GitHub de automação do Azure](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
