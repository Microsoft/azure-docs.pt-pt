---
title: Criar um livro de bordo Python 3 (pré-visualização) em Azure Automation
description: Este artigo ensina-o a criar, testar e publicar um simples livro de bordo Python 3 (pré-visualização).
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: tutorial
ms.openlocfilehash: e03eba29d634fafa9302441b17ca3a6bf6598556
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104982"
---
# <a name="tutorial-create-a-python-3-runbook-preview"></a>Tutorial: Criar um livro de bordo Python 3 (pré-visualização)

Este tutorial acompanha-o através da criação de um [livro de bordo Python 3](../automation-runbook-types.md#python-runbooks) (pré-visualização) na Azure Automation. Os livros de python compilam-se sob os Python 2 e 3. Pode editar diretamente o código do livro de texto utilizando o editor de texto no portal Azure.

> [!div class="checklist"]
> * Crie um simples livro de bordo python
> * Teste e publique o livro de corridas
> * Correr e acompanhar o estado do trabalho de runbook
> * Atualize o runbook para iniciar uma máquina virtual Azure com parâmetros de runbook

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

- Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

- [Conta de automatização](../automation-security-overview.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.

- Uma máquina virtual do Azure. Durante este tutorial, iniciará e impedirá esta máquina, pelo que não deve ser um VM de produção.

- Você pode usar um Trabalhador de Runbook Linux ou Windows Hybrid para executar livros python 3.Siga as instruções para instalar um Trabalhador de Runbook [Híbrido Linux](../automation-linux-hrw-install.md)   ou [Windows.](../automation-windows-hrw-install.md)  Não há nenhum pré-requisito específico para um Trabalhador Híbrido Linux. No entanto, para utilizar um Trabalhador Híbrido do Windows para os livros python 3, configuure-se que é a máquina que hospeda o trabalhador do runbook baseado em se você só está suportando Python 3, ou se Python 2 e 3 precisam coexistir.

   * Se tiver python 2 ou Python 3 *apenas* instalado, precisa anexar o caminho da pasta onde python.exe está localizado na variável ambiental **PATH.** Por exemplo, se python.exe estiver no caminho de `C:\Python` instalação, esse caminho tem de ser anexado à variável ambiental **PATH.**

   * Se tiver ambos Python 2 e Python 3 instalados e quiser executar ambos os tipos de livros, então precisa configurar as seguintes variáveis ambientais:

     * Python 2 - Criar uma nova variável ambiental chamada `PYTHON_2_PATH` e especificar a pasta de instalação. Por exemplo, se a pasta de instalação for `C:\Python27` , então este caminho tem de ser adicionado à variável.
     
     * Python 3 - Criar uma nova variável ambiental chamada `PYTHON_3_PATH` e especificar a pasta de instalação. Por exemplo, se a pasta de instalação for `C:\Python3` , então este caminho tem de ser adicionado à variável.

## <a name="create-a-new-runbook"></a>Criar um novo livro de bordo

Começa-se por criar um simples runbook que produz o texto *Hello World*.

1. No portal do Azure, abra a sua conta da Automatização.

    A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria destes ativos são os módulos que estão incluídos automaticamente na nova conta da Automatização. Também deverá ter o recurso da Credencial que está mencionado nos [Pré-requisitos](#prerequisites).

2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.

3. **Selecione Adicionar um livro de execução** para criar um novo runbook.

4. Dê ao livro de bordo o nome **MyFirstRunbook-Python.**

5. Selecione **Python 3** para **o tipo Runbook**.

6. Selecione **Criar** para criar o livro de recortes e abrir o editor textual.

## <a name="add-code-to-the-runbook"></a>Adicione código ao livro de execução

Agora adiciona-se um simples comando para imprimir o texto `Hello World` .

```python
print("Hello World!")
```

**Selecione Guardar** para guardar o livro de recortes.

## <a name="test-the-runbook"></a>Testar o runbook

Antes de publicar o runbook para o disponibilizar na produção, deve testá-lo para garantir que funciona corretamente. Ao testar um livro de execução, executar a sua versão de projeto e ver a sua saída de forma interativa.

1. Selecione **Test pane** para abrir o painel **de teste.**

2. Selecione **Iniciar** o teste. Esta deve ser a única opção ativada.

3. Uma [tarefa do runbook](../automation-runbook-execution.md) é criada e o respetivo estado é apresentado.
   O estado de trabalho começa como **"Fila",** indicando que está à espera que um trabalhador da lista na nuvem esteja disponível. Muda para **Começar** quando um trabalhador reclama o trabalho, e depois **corre quando** o livro de corridas realmente começa a funcionar.

4. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. Neste caso, deveria `Hello World` ver.

5. Feche o painel **de teste** para voltar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o livro de corridas

O livro de execução que criou ainda está em modo de projeto. Tens de publicá-lo antes de o poderes executar em produção. Ao publicar um livro de bordo, substitui-se a versão publicada existente com a versão de projeto. Neste caso, ainda não tem uma versão publicada porque acabou de criar o runbook.

1. Selecione **Publicar** para publicar o livro de recortes e, em seguida, **Sim** quando solicitado.

2. Se deslocar para a esquerda para ver o livro de recortes na página **Runbooks,** deverá ver um Estado de **Autoria** de **Publicado**.

3. Percorra para a direita para ver o painel para **MyFirstRunbook-Python3**.

   As opções em toda a parte superior permitem-lhe iniciar o runbook, ver o runbook ou programar para começar em algum momento no futuro.

4. Selecione **Iniciar** e, em seguida, selecione **OK** quando o painel **start runbook** abrir.

5. Abre-se um **painel job** para o trabalho de runbook que criou. Pode fechar este painel, mas vamos deixá-lo aberto para que possa ver o progresso do trabalho.

6. O estado do trabalho é mostrado no **Resumo do Trabalho** e corresponde aos status que viu quando testou o livro de aplicação.

7. Assim que o estado do livro de execução aparecer **concluído,** selecione **Output**. O **painel de saída** está aberto, onde pode `Hello World` ver.

8. Feche o painel **de saída.**

9. Selecione **Todos os Registos** para abrir o **painel streams** para o trabalho de runbook. Só deve ver `Hello World` na corrente de saída. No entanto, este painel pode mostrar outros streams para um trabalho de runbook, como **Verbose** e **Error**, se o livro de bordo lhes escrever.

10. Feche o **painel streams** e o painel **job** para voltar ao painel **MyFirstRunbook-Python3.**

11. Selecione **Jobs** para abrir a página **Jobs** para este livro de recortes. Esta página lista todos os empregos criados por este runbook. Apenas deve conseguir ver uma tarefa listada, uma vez que apenas executou a tarefa uma vez.

12. Pode selecionar este trabalho para abrir o mesmo **painel de trabalho** que viu quando iniciou o livro de recortes. Este painel permite-lhe voltar no tempo e ver os detalhes de qualquer trabalho que foi criado para um determinado runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Adicionar autenticação para gerir recursos da Azure

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure.
Para isso, o script tem de autenticar utilizando as credenciais da sua conta Dem automação.

> [!NOTE]
> A conta Automation deve ter sido criada com a função principal do serviço para que exista um certificado Run As.
> Se a sua conta Automation não foi criada com o titular do serviço, pode autenticar como descrito na [Authenticate com as Bibliotecas de Gestão Azure para Python.](/azure/python/python-sdk-azure-authenticate)

1. Abra o editor textual selecionando **Editar** no **painel MyFirstRunbook-Python3.**

2. Adicione o seguinte código para autenticar ao Azure:

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

Para trabalhar com os VMs Azure, crie uma instância do [cliente Azure Compute para Python.](/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient)

Use o cliente computacional para iniciar o VM. Adicione o seguinte código ao livro de bordo:

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

Onde `MyResourceGroup` está o nome do grupo de recursos que contém o VM, e `TestVM` é o nome do VM que pretende iniciar.

Teste e volte a executar o livro de bordo para ver se começa o VM.

## <a name="use-input-parameters"></a>Utilize parâmetros de entrada

O livro de bordo utiliza atualmente valores codificados para os nomes do grupo de recursos e do VM. Agora vamos adicionar código que obtém estes valores a partir de parâmetros de entrada.

Usa-se a `sys.argv` variável para obter os valores dos parâmetros. Adicione o seguinte código ao livro imediatamente após as outras `import` declarações:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Isto importa o `sys` módulo, e cria duas variáveis para deter os nomes do Grupo de Recursos e VM. Note que o elemento da lista de argumentos, `sys.argv[0]` é o nome do script, e não é introduzido pelo utilizador.

Agora pode modificar as duas últimas linhas do livro de bordo para utilizar os valores do parâmetro de entrada em vez de utilizar valores codificados:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Quando iniciar um livro de bordo python, quer a partir do painel de **teste,** quer como um livro de execução publicado, pode introduzir os valores para parâmetros na página **Start Runbook** de acordo com **parâmetros**.

Depois de começar a introduzir um valor na primeira caixa, aparece uma segunda, e assim por diante, para que possa introduzir os valores de parâmetros necessários.

Os valores estão disponíveis para o script na `sys.argv` matriz como no código que acaba de adicionar.

Insira o nome do seu grupo de recursos como o valor para o primeiro parâmetro, e o nome do VM para começar como o valor do segundo parâmetro.

![Introduza os valores dos parâmetros](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

Selecione **OK** para iniciar o livro de recortes. O livro de execuções executa e inicia o VM que especificou.

## <a name="error-handling-in-python"></a>Manipulação de erros em Python

Também pode utilizar as seguintes convenções para recuperar vários fluxos dos seus livros python, incluindo streams DEPUR, ERROR e DEBUG.

```python
print("Hello World output")
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

O exemplo que se segue mostra esta convenção utilizada num `try...except` bloco.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print ('ERROR: Handling run-time error:', detail)
```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre os tipos de runbook, as suas vantagens e limitações, consulte [os tipos de runbook da Azure Automation](../automation-runbook-types.md).

- Para aprender a desenvolver para Azure com Python, consulte [Azure para desenvolvedores python.](/azure/python/)

- Para ver os livros de bordo da amostra Python 3, consulte o [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python).
