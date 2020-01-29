---
title: Use a captura de pacote para fazer o monitoramento de rede proativo com alertas-Azure Functions
titleSuffix: Azure Network Watcher
description: Este artigo descreve como criar uma captura de pacotes disparada por alerta com o observador de rede do Azure
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 26599776abdf7ecbb6c86c332a40e0c2b7d6e67e
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276124"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Usar a captura de pacotes para monitoramento de rede proativo com alertas e Azure Functions

A captura de pacotes do observador de rede cria sessões de captura para acompanhar o tráfego dentro e fora das máquinas virtuais. O arquivo de captura pode ter um filtro definido para rastrear apenas o tráfego que você deseja monitorar. Esses dados são armazenados em um blob de armazenamento ou localmente na máquina convidada.

Esse recurso pode ser iniciado remotamente de outros cenários de automação, como Azure Functions. A captura de pacotes oferece a capacidade de executar capturas pró-ativas com base em anomalias de rede definidas. Outros usos incluem a coleta de estatísticas de rede, obtenção de informações sobre invasões de rede, depuração de comunicações cliente-servidor e muito mais.

Os recursos que são implantados no Azure executam o 24/7. Você e sua equipe não podem monitorar ativamente o status de todos os recursos 24/7. Por exemplo, o que acontece se ocorrer um problema às 2?

Usando o observador de rede, alertas e funções de dentro do ecossistema do Azure, você pode responder proativamente com os dados e as ferramentas para resolver problemas em sua rede.

![Cenário][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Azure PowerShell](/powershell/azure/install-Az-ps).
* Uma instância existente do observador de rede. Se você ainda não tiver uma, [crie uma instância do observador de rede](network-watcher-create.md).
* Uma máquina virtual existente na mesma região que o observador de rede com a extensão do [Windows](../virtual-machines/windows/extensions-nwa.md) ou [extensão de máquina virtual do Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Cenário

Neste exemplo, sua VM está enviando mais segmentos TCP do que o normal e você deseja ser alertado. Os segmentos TCP são usados como um exemplo aqui, mas você pode usar qualquer condição de alerta.

Quando você for alertado, você deseja receber dados em nível de pacote para entender por que a comunicação aumentou. Em seguida, você pode tomar medidas para retornar a máquina virtual para comunicação regular.

Esse cenário pressupõe que você tenha uma instância existente do observador de rede e um grupo de recursos com uma máquina virtual válida.

A lista a seguir é uma visão geral do fluxo de trabalho que ocorre:

1. Um alerta é disparado em sua VM.
1. O alerta chama sua função do Azure por meio de um webhook.
1. Sua função do Azure processa o alerta e inicia uma sessão de captura de pacote do observador de rede.
1. A captura de pacote é executada na VM e coleta o tráfego.
1. O arquivo de captura de pacote é carregado em uma conta de armazenamento para análise e diagnóstico.

Para automatizar esse processo, criamos e conectamos um alerta em nossa VM para disparar quando o incidente ocorre. Também criamos uma função para chamar no observador de rede.

Esse cenário faz o seguinte:

* Cria uma função do Azure que inicia uma captura de pacote.
* Cria uma regra de alerta em uma máquina virtual e configura a regra de alerta para chamar a função do Azure.

## <a name="create-an-azure-function"></a>Criar uma função do Azure

A primeira etapa é criar uma função do Azure para processar o alerta e criar uma captura de pacote.

1. Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso** > **computação** > **aplicativo de funções**.

    ![Criando um aplicativo de funções][1-1]

2. Na folha **aplicativo de funções** , insira os seguintes valores e, em seguida, selecione **OK** para criar o aplicativo:

    |**Definição** | **Valor** | **Detalhes** |
    |---|---|---|
    |**Nome da aplicação**|PacketCaptureExample|O nome do aplicativo de funções.|
    |**Subscrição**|[Sua assinatura] A assinatura para a qual criar o aplicativo de funções.||
    |**Grupo de Recursos**|PacketCaptureRG|O grupo de recursos para conter o aplicativo de funções.|
    |**Plano de Alojamento**|Plano de Consumo| O tipo de plano usado pelo aplicativo de funções. As opções são o plano de serviço de consumo ou Azure App. |
    |**Localização**|E.U.A. Central| A região na qual criar o aplicativo de funções.|
    |**Storage Account**|{autogenerated}| A conta de armazenamento que Azure Functions precisa para armazenamento de uso geral.|

3. Na folha **aplicativos de função PacketCaptureExample** , selecione **funções** > **função personalizada** > **+** .

4. Selecione **HttpTrigger-PowerShell**e, em seguida, insira as informações restantes. Por fim, para criar a função, selecione **criar**.

    |**Definição** | **Valor** | **Detalhes** |
    |---|---|---|
    |**Cenário**|Experimental|Tipo de cenário|
    |**Dar um nome à função**|AlertPacketCapturePowerShell|Nome da função|
    |**Nível de autorização**|Função|Nível de autorização para a função|

![Exemplo de funções][functions1]

> [!NOTE]
> O modelo do PowerShell é experimental e não tem suporte completo.

As personalizações são necessárias para este exemplo e são explicadas nas etapas a seguir.

### <a name="add-modules"></a>Adicionar módulos

Para usar os cmdlets do PowerShell do observador de rede, carregue o módulo mais recente do PowerShell para o aplicativo de funções.

1. No computador local com os módulos de Azure PowerShell mais recentes instalados, execute o seguinte comando do PowerShell:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    Este exemplo fornece o caminho local de seus módulos de Azure PowerShell. Essas pastas são usadas em uma etapa posterior. Os módulos usados neste cenário são:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![Pastas do PowerShell][functions5]

1. Selecione **configurações do aplicativo de funções** > **ir para editor do serviço de aplicativo**.

    ![Definições da Aplicação de funções][functions2]

1. Clique com o botão direito do mouse na pasta **AlertPacketCapturePowershell** e crie uma pasta chamada **azuremodules**. 

4. Crie uma subpasta para cada módulo de que você precisa.

    ![Pasta e subpastas][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Clique com o botão direito do mouse na subpasta **AZ. Network** e selecione **carregar arquivos**. 

6. Vá para seus módulos do Azure. Na pasta **AZ. Network** local, selecione todos os arquivos na pasta. Em seguida, selecione **OK**. 

7. Repita essas etapas para **AZ. Accounts** e **AZ. Resources**.

    ![Carregar ficheiros][functions6]

1. Depois de concluir, cada pasta deve ter os arquivos de módulo do PowerShell do seu computador local.

    ![Arquivos do PowerShell][functions7]

### <a name="authentication"></a>Autenticação

Para usar os cmdlets do PowerShell, você deve se autenticar. Você configura a autenticação no aplicativo de funções. Para configurar a autenticação, você deve configurar variáveis de ambiente e carregar um arquivo de chave criptografado para o aplicativo de funções.

> [!NOTE]
> Esse cenário fornece apenas um exemplo de como implementar a autenticação com Azure Functions. Há outras maneiras de fazer isso.

#### <a name="encrypted-credentials"></a>Credenciais criptografadas

O script do PowerShell a seguir cria um arquivo de chave chamado **PassEncryptKey. Key**. Ele também fornece uma versão criptografada da senha fornecida. Essa senha é a mesma senha que é definida para o aplicativo Azure Active Directory usado para autenticação.

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

Na Editor do Serviço de Aplicativo do aplicativo de funções, crie uma pasta chamada **Keys** em **AlertPacketCapturePowerShell**. Em seguida, carregue o arquivo **PassEncryptKey. Key** que você criou no exemplo anterior do PowerShell.

![Chave de funções][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Recuperar valores para variáveis de ambiente

O requisito final é configurar as variáveis de ambiente que são necessárias para acessar os valores para autenticação. A lista a seguir mostra as variáveis de ambiente que são criadas:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

A ID do cliente é a ID do aplicativo em Azure Active Directory.

1. Se você ainda não tiver um aplicativo para usar, execute o exemplo a seguir para criar um aplicativo.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > A senha que você usa ao criar o aplicativo deve ser a mesma senha que você criou anteriormente ao salvar o arquivo de chave.

1. No portal do Azure, selecione **assinaturas**. Selecione a assinatura a ser usada e, em seguida, selecione **controle de acesso (iam)** .

    ![Funções IAM][functions9]

1. Escolha a conta a ser usada e, em seguida, selecione **Propriedades**. Copie a ID do aplicativo.

    ![ID do aplicativo de funções][functions10]

#### <a name="azuretenant"></a>AzureTenant

Obtenha a ID do locatário executando o seguinte exemplo do PowerShell:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

O valor da variável de ambiente AzureCredPassword é o valor que você obtém da execução do seguinte exemplo do PowerShell. Este exemplo é o mesmo mostrado na seção **credenciais criptografadas** anteriores. O valor necessário é a saída da variável `$Encryptedpassword`.  Essa é a senha da entidade de serviço que você criptografou usando o script do PowerShell.

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

### <a name="store-the-environment-variables"></a>Armazenar as variáveis de ambiente

1. Vá para o aplicativo de funções. Em seguida, selecione **configurações do aplicativo de funções** > **definir configurações do aplicativo**.

    ![Configurar as definições da aplicação][functions11]

1. Adicione as variáveis de ambiente e seus valores às configurações do aplicativo e, em seguida, selecione **salvar**.

    ![Configurações do aplicativo][functions12]

### <a name="add-powershell-to-the-function"></a>Adicionar o PowerShell à função

Agora é hora de fazer chamadas no observador de rede de dentro da função do Azure. Dependendo dos requisitos, a implementação dessa função pode variar. No entanto, o fluxo geral do código é o seguinte:

1. Processar parâmetros de entrada.
2. Consulte capturas de pacote existentes para verificar limites e resolver conflitos de nome.
3. Crie uma captura de pacote com os parâmetros apropriados.
4. Sondar a captura de pacotes periodicamente até que ela seja concluída.
5. Notifique o usuário de que a sessão de captura de pacote foi concluída.

O exemplo a seguir é o código do PowerShell que pode ser usado na função. Há valores que precisam ser substituídos por **SubscriptionId**, **resourceGroupName**e **storageAccountName**.

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
#### <a name="retrieve-the-function-url"></a>Recuperar a URL da função 
1. Depois de criar sua função, configure seu alerta para chamar a URL associada à função. Para obter esse valor, copie a URL da função do seu aplicativo de funções.

    ![Localizando a URL da função][functions13]

2. Copie a URL da função para seu aplicativo de funções.

    ![Copiando a URL da função][2]

Se você precisar de propriedades personalizadas na carga da solicitação de POSTAgem do webhook, consulte [configurar um webhook em um alerta de métrica do Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="configure-an-alert-on-a-vm"></a>Configurar um alerta em uma VM

Os alertas podem ser configurados para notificar pessoas quando uma métrica específica ultrapassar um limite atribuído a ela. Neste exemplo, o alerta está nos segmentos TCP que são enviados, mas o alerta pode ser disparado para muitas outras métricas. Neste exemplo, um alerta é configurado para chamar um webhook para chamar a função.

### <a name="create-the-alert-rule"></a>Criar a regra de alerta

Vá para uma máquina virtual existente e, em seguida, adicione uma regra de alerta. A documentação mais detalhada sobre a configuração de alertas pode ser encontrada em [criar alertas no Azure monitor para serviços do Azure-portal do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Insira os valores a seguir na folha **regra de alerta** e selecione **OK**.

  |**Definição** | **Valor** | **Detalhes** |
  |---|---|---|
  |**Nome**|TCP_Segments_Sent_Exceeded|Nome da regra de alerta.|
  |**Descrição**|Os segmentos TCP enviados excederam o limite|A descrição da regra de alerta.|
  |**Métricas**|Segmentos TCP enviados| A métrica a ser usada para disparar o alerta. |
  |**Problema**|Maior que| A condição a ser usada ao avaliar a métrica.|
  |**Os**|100| O valor da métrica que dispara o alerta. Esse valor deve ser definido como um valor válido para o seu ambiente.|
  |**Período**|Nos últimos cinco minutos| Determina o período no qual procurar o limite na métrica.|
  |**Webhook**|[URL do webhook do aplicativo de funções]| A URL do webhook do aplicativo de funções que foi criado nas etapas anteriores.|

> [!NOTE]
> A métrica de segmentos TCP não está habilitada por padrão. Saiba mais sobre como habilitar métricas adicionais visitando [habilitar monitoramento e diagnóstico](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Rever os resultados

Após os critérios para os gatilhos de alerta, uma captura de pacote é criada. Vá para observador de rede e selecione **captura de pacote**. Nessa página, você pode selecionar o link do arquivo de captura de pacote para baixar a captura de pacote.

![Exibir captura de pacote][functions14]

Se o arquivo de captura for armazenado localmente, você poderá recuperá-lo entrando na máquina virtual.

Para obter instruções sobre como baixar arquivos de contas de armazenamento do Azure, consulte Introdução [ao armazenamento de BLOBs do Azure usando o .net](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que você pode usar é [Gerenciador de armazenamento](https://storageexplorer.com/).

Depois que a captura for baixada, você poderá exibi-la usando qualquer ferramenta que possa ler um arquivo **. Cap** . A seguir estão os links para duas dessas ferramentas:

- [Analisador de mensagem da Microsoft](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Passos seguintes

Saiba como exibir suas capturas de pacote visitando a [análise de captura de pacote com o Wireshark](network-watcher-deep-packet-inspection.md).


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
