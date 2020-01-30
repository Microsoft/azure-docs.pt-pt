---
title: Utilize a captura de pacotes para fazer monitorização proativa da rede com alertas - Funções Azure
titleSuffix: Azure Network Watcher
description: Este artigo descreve como criar um alerta desencadeado pela captura de pacotes com o Azure Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: ea506e137d71fc3124a4f93f1e97750a08dd4284
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842942"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Utilize a captura de pacotes para monitorização proactiva da rede com alertas e funções Azure

A captura de pacotes do Network Watcher cria sessões de captura para rastrear o tráfego dentro e fora de máquinas virtuais. O ficheiro de captura pode ter um filtro definido para rastrear apenas o tráfego que pretende monitorizar. Estes dados são então armazenados numa bolha de armazenamento ou localmente na máquina de hóspedes.

Esta capacidade pode ser iniciada remotamente a partir de outros cenários de automatização, como as Funções Azure. A captura de pacotes dá-lhe a capacidade de executar capturas proactivas com base em anomalias de rede definidas. Outros usos incluem recolher estatísticas de rede, obter informações sobre intrusões de rede, depurar comunicações de servidores de clientes e muito mais.

Os recursos que são implantados no Azure funcionam 24 horas por dia, 7 dias por semana. Você e o seu pessoal não podem monitorizar ativamente o estado de todos os recursos 24 horas por dia, 7 dias por semana. Por exemplo, o que acontece se um problema ocorrer às 2 da manhã?

Ao utilizar o Network Watcher, alertando e funcionando dentro do ecossistema Azure, pode responder proativamente com os dados e ferramentas para resolver problemas na sua rede.

![Cenário][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Azure PowerShell.](/powershell/azure/install-Az-ps)
* Um caso existente de Observador de Rede. Se ainda não tem uma, [crie uma instância de Observador de Rede.](network-watcher-create.md)
* Uma máquina virtual existente na mesma região que o Observador de Rede com a [extensão do Windows](../virtual-machines/windows/extensions-nwa.md) ou [extensão virtual da máquina Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Cenário

Neste exemplo, o seu VM está a enviar mais segmentos de TCP do que o habitual, e quer ser alertado. Os segmentos de TCP são usados como exemplo aqui, mas você pode usar qualquer condição de alerta.

Quando é alertado, pretende receber dados de nível de pacote para perceber porque é que a comunicação aumentou. Depois pode tomar medidas para devolver a máquina virtual à comunicação regular.

Este cenário pressupõe que você tem uma instância existente de Network Watcher e um grupo de recursos com uma máquina virtual válida.

A seguinte lista é uma visão geral do fluxo de trabalho que ocorre:

1. Um alerta é ativado no seu VM.
1. O alerta chama a sua função Azure através de um webhook.
1. A função Azure processa o alerta e inicia uma sessão de captura de pacotes do Observador de Rede.
1. A captura do pacote corre no VM e recolhe tráfego.
1. O ficheiro de captura de pacotes é enviado para uma conta de armazenamento para revisão e diagnóstico.

Para automatizar este processo, criamos e conectamos um alerta no nosso VM para desencadear quando o incidente ocorrer. Também criamos uma função para chamar para o Observador da Rede.

Este cenário faz o seguinte:

* Cria uma função Azure que inicia uma captura de pacotes.
* Cria uma regra de alerta sobre uma máquina virtual e configura a regra de alerta para chamar a função Azure.

## <a name="create-an-azure-function"></a>Criar uma função do Azure

O primeiro passo é criar uma função Azure para processar o alerta e criar uma captura de pacotes.

1. No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso** > **Compute** > **Function App**.

    ![Criação de uma aplicação de função][1-1]

2. Na lâmina **da App função,** introduza os seguintes valores e, em seguida, selecione **OK** para criar a aplicação:

    |**Definição** | **Valor** | **Detalhes** |
    |---|---|---|
    |**Nome da aplicação**|PacketCaptureExample|O nome da aplicação de funções.|
    |**Subscrição**|[A sua subscrição] A subscrição para a qual criar a aplicação de funções.||
    |**Grupo de Recursos**|PacketCaptureRG|O grupo de recursos para conter a aplicação de função.|
    |**Plano de Alojamento**|Plano de Consumo| O tipo de plano que a sua aplicação de função utiliza. As opções são o plano de Serviço de Consumo ou Aplicações Azure. |
    |**Localização**|E.U.A. Central| A região em que criar a aplicação de funções.|
    |**Storage Account**|{autogenerated}| A conta de armazenamento de que as Funções Azure precisam para o armazenamento geral.|

3. Na lâmina de aplicações de aplicações de **funções PacketCaptureExample,** selecione **Funções** > **função personalizada** > **+** .

4. Selecione **HttpTrigger-Powershell**e, em seguida, introduza as informações restantes. Finalmente, para criar a função, selecione **Criar**.

    |**Definição** | **Valor** | **Detalhes** |
    |---|---|---|
    |**Cenário**|Experimental|Tipo de cenário|
    |**Dar um nome à função**|AlertPacketCapturePowerShell|Nome da função|
    |**Nível de autorização**|Função|Nível de autorização para a função|

![Exemplo de funções][functions1]

> [!NOTE]
> O modelo PowerShell é experimental e não tem suporte total.

As personalizações são necessárias para este exemplo e são explicadas nos seguintes passos.

### <a name="add-modules"></a>Adicionar módulos

Para utilizar os cmdlets PowerShell do Observador da Rede, carregue o mais recente módulo PowerShell para a aplicação de funções.

1. Na sua máquina local com os mais recentes módulos Azure PowerShell instalados, execute o seguinte comando PowerShell:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    Este exemplo dá-lhe o caminho local dos seus módulos Azure PowerShell. Estas pastas são usadas num passo posterior. Os módulos que são utilizados neste cenário são:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![Pastas PowerShell][functions5]

1. Selecione **as definições** de aplicativo seletiva > vá para o Editor de Serviço sinuoso de **Aplicações**.

    ![Definições da Aplicação de funções][functions2]

1. Clique à direita na pasta **AlertPacketCapturePowershell** e, em seguida, crie uma pasta chamada **azuremodules**. 

4. Crie uma subpasta para cada módulo de que necessita.

    ![Pastas e subpastas][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Clique na subpasta **Az.Network** e, em seguida, selecione **Upload Files**. 

6. Vá aos seus módulos Azure. Na pasta **Az.Network** local, selecione todos os ficheiros da pasta. Em seguida, selecione **OK**. 

7. Repita estes passos para **Az.Contas** e **Recursos Az.Resources**.

    ![Carregar ficheiros][functions6]

1. Depois de terminar, cada pasta deve ter os ficheiros do módulo PowerShell da sua máquina local.

    ![Ficheiros PowerShell][functions7]

### <a name="authentication"></a>Autenticação

Para utilizar os cmdlets PowerShell, tem de autenticar. Configura a autenticação na aplicação de funções. Para configurar a autenticação, deve configurar variáveis ambientais e carregar um ficheiro de chave encriptado para a aplicação de função.

> [!NOTE]
> Este cenário fornece apenas um exemplo de como implementar a autenticação com as Funções Azure. Há outras formas de fazer isto.

#### <a name="encrypted-credentials"></a>Credenciais encriptadas

O seguinte script PowerShell cria um ficheiro de chave chamado **PassEncryptKey.key**. Também fornece uma versão encriptada da palavra-passe que é fornecida. Esta palavra-passe é a mesma palavra-passe definida para a aplicação Azure Ative Directory que é usada para autenticação.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

No Editor de Serviço de Aplicações da aplicação de funções, crie uma pasta chamada **chaves** em **AlertPacketCapturePowerShell**. Em seguida, faça o upload do ficheiro **passEncryptKey.key** que criou na amostra powerShell anterior.

![Chave de funções][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Recuperar valores para variáveis ambientais

O requisito final é configurar as variáveis ambientais necessárias para aceder aos valores de autenticação. A seguinte lista mostra as variáveis ambientais que são criadas:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

O ID do cliente é o ID de aplicação de uma aplicação no Diretório Ativo Azure.

1. Se ainda não tem uma aplicação para usar, faça o seguinte exemplo para criar uma aplicação.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > A palavra-passe que utiliza ao criar a aplicação deve ser a mesma palavra-passe que criou anteriormente ao guardar o ficheiro chave.

1. No portal Azure, selecione **Subscrições**. Selecione a subscrição para utilizar e, em seguida, selecione **o controlo de acesso (IAM)** .

    ![Funções IAM][functions9]

1. Escolha a conta para usar e, em seguida, selecione **Propriedades**. Copie o ID da aplicação.

    ![ID de aplicação de funções][functions10]

#### <a name="azuretenant"></a>AzureTenant

Obtenha o ID do inquilino executando a seguinte amostra PowerShell:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

O valor da variável ambiente AzureCredPassword é o valor que obtém ao executar a seguinte amostra PowerShell. Este exemplo é o mesmo que é mostrado na secção de **credenciais encriptadas** anteriores. O valor que é necessário é a produção da variável `$Encryptedpassword`.  Esta é a palavra-passe principal do serviço que encriptou utilizando o script PowerShell.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>Armazenar as variáveis ambientais

1. Vá ao aplicativo de funções. Em seguida, selecione as definições da **aplicação 'Função** > **Configurar as definições**da aplicação .

    ![Configurar as definições da aplicação][functions11]

1. Adicione as variáveis ambientais e os seus valores às definições da aplicação e, em seguida, selecione **Save**.

    ![Definições de aplicativos][functions12]

### <a name="add-powershell-to-the-function"></a>Adicione powerShell à função

Está na hora de fazer chamadas para o Observador da Rede de dentro da função Azure. Dependendo dos requisitos, a implementação desta função pode variar. No entanto, o fluxo geral do código é o seguinte:

1. Processe os parâmetros de entrada.
2. Consulta de pacotes existentes capturas para verificar limites e resolver conflitos de nomes.
3. Crie uma captura de pacote com parâmetros apropriados.
4. Captura de pacotes de sondagens periodicamente até estar completo.
5. Notifique o utilizador de que a sessão de captura do pacote está completa.

O exemplo seguinte é o código PowerShell que pode ser usado na função. Existem valores que precisam de ser substituídos por **subscriçãoId,** **resourceGroupName**, e **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Accounts\Az.Accounts.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Network\Az.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Resources\Az.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
            $packetcapturename = "PSAzureFunction"
            $packetCaptureLimit = 10
            $packetCaptureDuration = 10

            #Credentials
            $tenant = $env:AzureTenant
            $pw = $env:AzureCredPassword
            $clientid = $env:AzureClientId
            $keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

            #Authentication
            $secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
            $credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
            Connect-AzAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}

                #Get existing packetCaptures
                $packetCaptures = Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>Recuperar o URL da função 
1. Depois de ter criado a sua função, configure o seu alerta para ligar para o URL associado à função. Para obter este valor, copie o URL de função da sua aplicação de função.

    ![Encontrar o URL da função][functions13]

2. Copie o URL de função para a sua aplicação de função.

    ![Copiar o URL da função][2]

Se necessitar de propriedades personalizadas na carga útil do pedido de webhook POST, consulte [configurar um webhook num alerta métrico Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="configure-an-alert-on-a-vm"></a>Configure um alerta num VM

Os alertas podem ser configurados para notificar os indivíduos quando uma métrica específica atravessa um limiar que lhe é atribuído. Neste exemplo, o alerta está nos segmentos de TCP que são enviados, mas o alerta pode ser desencadeado para muitas outras métricas. Neste exemplo, é configurado um alerta para chamar um webhook para ligar para a função.

### <a name="create-the-alert-rule"></a>Criar a regra de alerta

Vá a uma máquina virtual existente e adicione uma regra de alerta. Documentação mais detalhada sobre a configuração de alertas pode ser encontrada em [alertas Create no Azure Monitor para serviços Azure - portal Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Introduza os seguintes valores na lâmina da **regra alerta** e, em seguida, selecione **OK**.

  |**Definição** | **Valor** | **Detalhes** |
  |---|---|---|
  |**Nome**|TCP_Segments_Sent_Exceeded|Nome da regra de alerta.|
  |**Descrição**|Segmentos de TCP enviados limiar ultrapassado|A descrição da regra do alerta.|
  |**Métricas**|Segmentos de TCP enviados| A métrica a usar para desencadear o alerta. |
  |**Condição**|Mais do que| A condição de usar ao avaliar a métrica.|
  |**Os**|100| O valor da métrica que desencadeia o alerta. Este valor deve ser definido para um valor válido para o seu ambiente.|
  |**Período**|Nos últimos cinco minutos| Determina o período em que procurar o limiar da métrica.|
  |**Webhook**|[WEBhook URL da aplicação de funções]| O URL webhook da aplicação de funções que foi criado nos passos anteriores.|

> [!NOTE]
> A métrica dos segmentos TCP não é ativada por padrão. Saiba mais sobre como permitir métricas adicionais visitando [a monitorização e diagnóstico enable.](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)

## <a name="review-the-results"></a>Rever os resultados

Após os critérios para os gatilhos de alerta, é criada uma captura de pacote. Vá ao Observador de Rede e, em seguida, selecione **a captura de Pacotes**. Nesta página, pode selecionar o link de ficheiro de captura de pacotes para descarregar a captura do pacote.

![Ver captura de pacote][functions14]

Se o ficheiro de captura for armazenado localmente, pode recuperá-lo insindo na máquina virtual.

Para obter instruções sobre o download de ficheiros das contas de armazenamento do Azure, consulte Começar com o [armazenamento do Azure Blob utilizando .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode utilizar é o [Storage Explorer.](https://storageexplorer.com/)

Depois de ter sido descarregada a sua captura, pode vê-la utilizando qualquer ferramenta que possa ler um ficheiro **.cap.** Seguem-se ligações a duas destas ferramentas:

- [Analisador de mensagens microsoft](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Passos seguintes

Saiba como ver as capturas do seu pacote visitando a análise de captura de [Pacotes com wireshark](network-watcher-deep-packet-inspection.md).


[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png
