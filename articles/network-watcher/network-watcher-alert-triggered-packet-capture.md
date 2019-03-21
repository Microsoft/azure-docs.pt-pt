---
title: Utilizar a captura de pacotes para efetuar a monitorização de rede proativa com alertas e as funções do Azure | Documentos da Microsoft
description: Este artigo descreve como criar uma captura de pacotes acionadas alerta com o observador de rede do Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 71e71b417f12b58fc03c581826c0e5c2412e684b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57876651"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Utilizar a captura de pacotes de monitorização de rede proativa com alertas e as funções do Azure

Captura de pacotes do observador de rede cria sessões de captura para controlar o tráfego que entra e sai de máquinas virtuais. O arquivo de captura pode ter um filtro que é definido para controlar apenas o tráfego que pretende monitorizar. Estes dados, em seguida, são armazenados num blob de armazenamento ou localmente na máquina convidada.

Esta capacidade pode ser iniciada remotamente a partir de outros cenários de automatização, como as funções do Azure. Captura de pacotes dá-lhe a capacidade de executar captures proativa com base em anomalias de rede definida pelo. Outras utilizações incluem a recolha de estatísticas de rede, obter informações sobre invasões de rede, depuração comunicações de cliente-servidor e muito mais.

Os recursos que são implementados no Azure executados 24x7. E sua equipe ativamente não é possível monitorizar o estado de todos os recursos 24x7. Por exemplo, o que acontece se ocorrer um problema às 2H?

Ao utilizar o observador de rede, alertas e funções de dentro do ecossistema do Azure, pode reagir de forma proativa com os dados e ferramentas para resolver problemas na sua rede.

![Cenário][scenario]

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [do Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
* Uma instância existente do observador de rede. Se não tiver um, já [criar uma instância do observador de rede](network-watcher-create.md).
* Uma máquina virtual existente na mesma região que o observador de rede com o [extensão do Windows](../virtual-machines/windows/extensions-nwa.md) ou [extensão da máquina virtual Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Cenário

Neste exemplo, a VM está enviando mais segmentos TCP que o habitual e pretende ser alertado. Segmentos TCP são utilizados como exemplo aqui, mas pode usar qualquer condição do alerta.

Quando for alertado, quer receber dados ao nível do pacote para compreender por que a comunicação aumentou. Em seguida, pode tomar medidas para retornar a máquina virtual para a comunicação regular.

Este cenário pressupõe que tem uma instância existente do observador de rede e um grupo de recursos com uma máquina virtual válida.

A lista seguinte é uma visão geral do fluxo de trabalho que também tem lugar:

1. É acionado um alerta na sua VM.
1. O alerta chamar sua função do Azure através de um webhook.
1. A função do Azure processa o alerta e inicia uma sessão de captura de pacotes do observador de rede.
1. A captura de pacotes é executado na VM e recolhe tráfego.
1. O arquivo de captura de pacotes é carregado para uma conta de armazenamento para revisão e diagnóstico.

Para automatizar este processo, crie e ligue-se um alerta na nossa VM acionar quando ocorrer o incidente. Também podemos criar uma função para chamar o observador de rede.

Este cenário faz o seguinte:

* Cria uma função do Azure, que inicia uma captura de pacotes.
* Cria uma regra de alerta numa máquina virtual e configura a regra de alerta para chamar a função do Azure.

## <a name="create-an-azure-function"></a>Criar uma função do Azure

A primeira etapa é criar uma função do Azure para processar o alerta e criar uma captura de pacotes.

1. Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso** > **computação** > **Function App**.

    ![Criar uma aplicação de funções][1-1]

2. Sobre o **aplicação de funções** painel, introduza os seguintes valores e, em seguida, selecione **OK** para criar a aplicação:

    |**Definição** | **Valor** | **Detalhes** |
    |---|---|---|
    |**Nome da aplicação** |PacketCaptureExample|O nome da aplicação de função.|
    |**Subscrição**|[A sua subscrição] A subscrição para o qual pretende criar a aplicação de funções.||
    |**Grupo de Recursos**|PacketCaptureRG|O grupo de recursos que contém a aplicação de funções.|
    |**Plano de Alojamento**|Plano de Consumo| O tipo de planear a sua aplicação de função utiliza. As opções são consumo ou plano de serviço de aplicações do Azure. |
    |**Localização**|EUA Central| A região na qual pretende criar a aplicação de funções.|
    |**Storage Account**|{autogenerated}| A conta de armazenamento que precisa de funções do Azure para armazenamento para fins gerais.|

3. Sobre o **aplicações de funções de PacketCaptureExample** painel, selecione **funções** > **função personalizada**  >  **+**.

4. Selecione **HttpTrigger-Powershell**e, em seguida, introduza as restantes informações. Por fim, para criar a função, selecione **criar**.

    |**Definição** | **Valor** | **Detalhes** |
    |---|---|---|
    |**Cenário**|Experimental|Tipo de cenário|
    |**Dar um nome à função**|AlertPacketCapturePowerShell|Nome da função|
    |**Nível de autorização**|Função|Nível de autorização para a função|

![Exemplo de funções][functions1]

> [!NOTE]
> O modelo do PowerShell é experimental e não tem suporte total.

As personalizações são necessárias para este exemplo e são explicadas nos passos seguintes.

### <a name="add-modules"></a>Adicionar módulos

Para utilizar cmdlets do PowerShell do observador de rede, carregar o módulo do PowerShell mais recente para a aplicação de funções.

1. No seu computador local com os módulos Azure PowerShell mais recente instalado, execute o seguinte comando do PowerShell:

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    Neste exemplo dá-lhe o caminho local de seus módulos do Azure PowerShell. Essas pastas são utilizadas num passo posterior. Os módulos utilizados neste cenário são:

   * AzureRM.Network

   * AzureRM.Profile

   * AzureRM.Resources

     ![Pastas de PowerShell][functions5]

1. Selecione **as definições da aplicação de função** > **Ir para o Editor de serviço de aplicações**.

    ![Definições da Aplicação de funções][functions2]

1. Com o botão direito a **AlertPacketCapturePowershell** pasta e, em seguida, crie uma pasta chamada **azuremodules**. 

4. Crie uma subpasta para cada módulo que precisa.

    ![Pasta e subpastas][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. Com o botão direito a **azurerm. Network** subpasta e, em seguida, selecione **carregar ficheiros**. 

6. Vá para os módulos do Azure. No local **azurerm. Network** pasta, selecione todos os ficheiros na pasta. Em seguida, selecione **OK**. 

7. Repita estes passos para **azurerm. profile** e **Azurerm**.

    ![Carregar ficheiros][functions6]

1. Depois de terminar, cada pasta deve ter os arquivos de módulo do PowerShell do seu computador local.

    ![Ficheiros PowerShell][functions7]

### <a name="authentication"></a>Authentication

Para utilizar os cmdlets do PowerShell, tem de autenticar. Configurar a autenticação na function app. Para configurar a autenticação, tem de configurar variáveis de ambiente e carregar um ficheiro de chave encriptado para a aplicação de funções.

> [!NOTE]
> Este cenário fornece apenas um exemplo de como implementar a autenticação com as funções do Azure. Existem outras maneiras de fazer isso.

#### <a name="encrypted-credentials"></a>Credenciais encriptadas

O seguinte script do PowerShell cria um ficheiro de chave chamado **PassEncryptKey.key**. Ele também fornece uma versão criptografada da palavra-passe fornecida. Esta palavra-passe é a mesma palavra-passe que está definido para a aplicação do Azure Active Directory que é utilizada para autenticação.

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

Na aplicação Editor do serviço da aplicação de função, crie uma pasta denominada **chaves** sob **AlertPacketCapturePowerShell**. Em seguida, carregue o **PassEncryptKey.key** ficheiro que criou no PowerShell de exemplo anterior.

![Chave de funções][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Obter valores de variáveis de ambiente

O requisito final é configurar as variáveis de ambiente que são necessárias para aceder a valores para a autenticação. A lista seguinte mostra as variáveis de ambiente que são criadas:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

O ID de cliente é o ID da aplicação de uma aplicação no Azure Active Directory.

1. Se ainda não tiver uma aplicação a utilizar, execute o exemplo a seguir para criar uma aplicação.

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > A palavra-passe que utilizou ao criar o aplicativo deve ser a mesma palavra-passe que criou anteriormente ao guardar o ficheiro de chave.

1. No portal do Azure, selecione **subscrições**. Selecione a subscrição a utilizar e, em seguida, selecione **controlo de acesso (IAM)**.

    ![Funções IAM][functions9]

1. Escolha a conta a utilizar e, em seguida, selecione **propriedades**. Copie o ID da aplicação.

    ![ID da aplicação de funções][functions10]

#### <a name="azuretenant"></a>AzureTenant

Obter o ID de inquilino ao executar o seguinte exemplo do PowerShell:

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

O valor da variável de ambiente AzureCredPassword é o valor que tem de executar o seguinte exemplo do PowerShell. Neste exemplo é o mesmo que é mostrado na precedente **encriptação das credenciais** secção. O valor que é necessário é o resultado do `$Encryptedpassword` variável.  Esta é a senha de principal de serviço encriptada utilizando o script do PowerShell.

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

### <a name="store-the-environment-variables"></a>Store as variáveis de ambiente

1. Vá para a aplicação de funções. Em seguida, selecione **as definições da aplicação de função** > **configurar definições da aplicação**.

    ![Configurar as definições da aplicação][functions11]

1. Adicione as variáveis de ambiente e os respetivos valores para as definições da aplicação e, em seguida, selecione **guardar**.

    ![Definições de aplicação][functions12]

### <a name="add-powershell-to-the-function"></a>Adicionar o PowerShell para a função

Agora é hora de fazer chamadas para o observador de rede a partir de dentro da função do Azure. Consoante os requisitos, a implementação desta função pode variar. No entanto, o fluxo geral do código é o seguinte:

1. Parâmetros de entrada do processo.
2. Capturas de pacotes de existente de consulta para verificar limites e resolver conflitos de nomes.
3. Crie uma captura de pacotes com parâmetros apropriados.
4. Captura de pacotes de consulta periodicamente até esta estar concluída.
5. Notificar o utilizador que a sessão de captura de pacotes está concluída.

O exemplo seguinte é o código do PowerShell que pode ser utilizado na função. Há valores que precisam ser substituídos para **subscriptionId**, **resourceGroupName**, e **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

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
            Connect-AzureRmAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
                $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

                #Get existing packetCaptures
                $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>Obter o URL da função 
1. Depois de criar sua função, configure o alerta para chamar o URL que está associada a função. Para obter este valor, copie o URL da função a partir da sua aplicação de função.

    ![Encontrar o URL da função][functions13]

2. Copie o URL da função para a sua aplicação de função.

    ![Copiar o URL de função][2]

Se necessitar de propriedades personalizadas no payload do pedido POST do webhook, veja [configurar um webhook num alerta de métrica do Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="configure-an-alert-on-a-vm"></a>Configurar um alerta numa VM

Alertas podem ser configurados para notificar os indivíduos quando uma métrica específica ultrapassa um limiar que está atribuído à mesma. Neste exemplo, o alerta é nos segmentos TCP que são enviados, mas é possível acionar o alerta para muitas outras métricas. Neste exemplo, um alerta está configurado para chamar um webhook para chamar a função.

### <a name="create-the-alert-rule"></a>Criar a regra de alerta

Vá para uma máquina virtual existente e, em seguida, adicione uma regra de alerta. Documentação mais detalhada sobre como configurar alertas pode ser encontrada em [criar alertas no Azure Monitor para serviços do Azure - portal do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Introduza os seguintes valores no **regra de alerta** painel e, em seguida, selecione **OK**.

  |**Definição** | **Valor** | **Detalhes** |
  |---|---|---|
  |**Nome**|TCP_Segments_Sent_Exceeded|Nome da regra de alerta.|
  |**Descrição**|Segmentos TCP enviado limiar excedido|A descrição para a regra de alerta.|
  |**Métricas**|Segmentos TCP enviados| A métrica para utilizar para acionar o alerta. |
  |**condição**|Maior que| A condição a utilizar ao avaliar a métrica.|
  |**Limiar**|100| O valor da métrica que aciona o alerta. Este valor deve ser definido para um valor válido para o seu ambiente.|
  |**Período**|Durante os últimos cinco minutos| Determina o período em que a procurar o limiar em que a métrica.|
  |**Webhook**|[URL do webhook da aplicação de funções]| O URL do webhook a partir da aplicação de função que foi criado nos passos anteriores.|

> [!NOTE]
> A métrica de segmentos TCP não está ativada por predefinição. Saiba mais sobre como ativar métricas adicionais, visite a página [ativar a monitorização e diagnóstico](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Rever os resultados

Depois dos critérios para os acionadores de alerta, é criada uma captura de pacotes. Vá para o observador de rede e, em seguida, selecione **captura de pacotes**. Nesta página, pode selecionar o link de arquivo de captura de pacotes para transferir a captura de pacotes.

![Captura de pacotes do Vista][functions14]

Se o arquivo de captura é armazenado localmente, pode recuperá-la ao iniciar sessão para a máquina virtual.

Para obter instruções sobre o download de arquivos de contas de armazenamento do Azure, consulte [introdução ao armazenamento de Blobs do Azure com o .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). É outra ferramenta que pode utilizar [Explorador de armazenamento](https://storageexplorer.com/).

Após a captura foi transferida, pode vê-lo usando qualquer ferramenta que pode ler um **. cap** ficheiro. Seguem-se ligações para duas dessas ferramentas:

- [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Passos Seguintes

Saiba como ver capturas de pacotes, visitando [análise de captura do pacote com o Wireshark](network-watcher-deep-packet-inspection.md).


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
