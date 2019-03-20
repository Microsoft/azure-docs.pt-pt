---
title: Registe o ASDK com o Azure | Documentos da Microsoft
description: Descreve como registar o Azure Stack com o Azure para permitir relatórios de utilização e distribuição de mercado.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: dc146c6e8877a99570aab25d198ba365abbe7c86
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58078181"
---
# <a name="azure-stack-registration"></a>Registo do Azure Stack
Pode registrar sua instalação do Azure Stack Development Kit (ASDK) com o Azure para transferir itens do marketplace do Azure e configurar relatórios de volta à Microsoft de dados de comércio. É necessário Registro para dar suporte a todas as funcionalidades do Azure Stack, incluindo a distribuição de mercado. Registro é necessário para que possa testar a funcionalidade importante do Azure Stack, como relatórios de utilização e distribuição de mercado. Depois de registar o Azure Stack, a utilização é comunicada ao Azure commerce. Pode vê-lo sob a subscrição utilizada para o registo. No entanto, os utilizadores ASDK para não são cobrados qualquer utilização que reportam.

Se não registar a sua ASDK, poderá ver uma **ativação necessária** alerta de aviso que indica que se Registre seu Kit de desenvolvimento do Azure Stack. Este comportamento é esperado.

## <a name="prerequisites"></a>Pré-requisitos
Antes de utilizar estas instruções para registar o ASDK com o Azure, certifique-se de que tem instalado o Azure Stack do PowerShell e baixado as ferramentas do Azure Stack, conforme descrito no [configuração pós-implantação](asdk-post-deploy.md) artigo.

Além disso, o modo de idioma do PowerShell deve ser definido como **FullLanguageMode** no computador utilizado para registar o ASDK no Azure. Para verificar se o modo de idioma atual está definido como completa, abra uma janela elevada do PowerShell e execute os seguintes comandos do PowerShell:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Certifique-se de que a saída devolve **FullLanguageMode**. Se qualquer outro modo de idioma é retornado, registo, terá de ser executado noutro computador ou o modo de idioma tem de ser definido como **FullLanguageMode** antes de continuar.

A conta do Azure AD utilizada para o registo tem de ter acesso à subscrição do Azure e tem permissões para criar aplicações de identidades e de principais de serviço no diretório associado a essa subscrição. Recomendamos que registe o Azure Stack com o Azure utilizando a administração de menor privilégio ao [criar uma conta de serviço para utilizar para o registo](../azure-stack-registration-role.md) em vez de utilizar credenciais de administrador global.

## <a name="register-azure-stack-with-azure"></a>Registar o Azure Stack com o Azure
Siga estes passos para registar o ASDK com o Azure.

> [!NOTE]
> Todos estes passos tem de ser executados a partir de um computador que tenha acesso ao ponto final com privilégios. Para o ASDK, que é o computador de anfitrião do kit de desenvolvimento.

1. Abra uma consola do PowerShell como administrador.  

2. Execute os seguintes comandos do PowerShell para registar a sua instalação ASDK com o Azure. Terá de iniciar sessão no seu ID de subscrição de faturação do Azure e a instalação de ASDK local. Se não tiver um ID de subscrição de faturação do Azure, ainda, pode [criar uma conta gratuita do Azure aqui](https://azure.microsoft.com/free/?b=17.06). Registar o Azure Stack, incorre em sem custos na sua subscrição do Azure.<br><br>Defina um nome exclusivo para o registo ao executar o **Set-AzsRegistration** cmdlet. O **RegistrationName** parâmetro tem um valor padrão de **AzureStackRegistration**. No entanto, se utilizar o mesmo nome em mais de uma instância do Azure Stack, o script falhará.

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    # If you have multiple subscriptions, run the following command to select the one you want to use:
    # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription
    
    # Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```
3. Quando o script tiver concluído, deverá ver esta mensagem: **O ambiente está agora registado e ativado usando os parâmetros fornecidos.**

    ![O ambiente está agora registado](media/asdk-register/1.PNG)


## <a name="register-in-disconnected-environments"></a>Registe-se em ambientes desligados
Se estiver a registar Azure Stack num ambiente desligado (com nenhuma conectividade com a internet), terá de obter um registo de token do ambiente do Azure Stack e, em seguida, utilizar esse token num computador que pode ligar ao Azure para registar e criar uma ativação recursos para o seu ambiente de ASDK.
 
 > [!IMPORTANT]
 > Antes de utilizar estas instruções para registar o Azure Stack, certifique-se de que tem instalado o PowerShell para o Azure Stack e baixado as ferramentas do Azure Stack, conforme descrito no [configuração pós-implantação](asdk-post-deploy.md) artigo no anfitrião ASDK computador e o computador com acesso à internet utilizado para ligar ao Azure e registe-se.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obter um registo de token do ambiente do Azure Stack
No computador anfitrião ASDK, inicie o PowerShell como administrador e navegue para o **Registro** pasta na **AzureStack-Tools-mestre** diretório criado quando transferiu as ferramentas do Azure Stack. Utilize os seguintes comandos do PowerShell para importar os **RegisterWithAzure.psm1** módulo e, em seguida, utilize o **Get-AzsRegistrationToken** cmdlet para obter o token de registo:  

   ```PowerShell  
   # Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Create registration token
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```

Guarde este token de registo para utilização no computador ligado à internet. Pode copiar o ficheiro ou o texto do arquivo criado pelo parâmetro $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Ligar ao Azure e registe-se
Na internet computador ligado, utilize os seguintes comandos do PowerShell para importar o **RegisterWithAzure.psm1** módulo e, em seguida, utilize o **Register-AzsEnvironment** cmdlet para registar com a utilização do Azure o token de registo que acabou de criar e um nome de registo único:  

  ```PowerShell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Em alternativa, pode utilizar o **Get-Content** cmdlet para apontar para um ficheiro que contém o token de registo:

  ```PowerShell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Quando o registo estiver concluído, deverá ver uma mensagem semelhante à **ambiente sua do Azure Stack está agora registado com o Azure.**

> [!IMPORTANT]
> Não feche a janela do PowerShell. 

Guarde o token de registo e o nome de recurso de Registro para referência futura.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Obter uma chave de ativação do recurso de registo do Azure

Ainda a utilizar o computador ligado à internet **e a mesma janela de consola do PowerShell**, obter uma chave de ativação do recurso de registo criado quando se registou com o Azure.

Para obter a chave de ativação, execute os seguintes comandos do PowerShell, utilize o mesmo valor de nome de registo único que forneceu quando efetuar o registo com o Azure no passo anterior:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```
### <a name="create-an-activation-resource-in-azure-stack"></a>Criar um recurso de ativação no Azure Stack

Regressar ao ambiente do Azure Stack com o ficheiro ou criado a partir de texto da chave de ativação **Get-AzsActivationKey**. Execute os seguintes comandos do PowerShell para criar um recurso de ativação no Azure Stack com essa chave de ativação:   

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Em alternativa, pode utilizar o **Get-Content** cmdlet para apontar para um ficheiro que contém o token de registo:

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Quando a ativação estar concluída, deverá ver uma mensagem semelhante à **seu ambiente concluiu o processo de registro e ativação.**

## <a name="verify-the-registration-was-successful"></a>Certifique-se de que o registo foi concluída com êxito

Pode utilizar o **gestão da região** mosaico para verificar se o registo do Azure Stack foi concluída com êxito. Este mosaico não está disponível no dashboard predefinido no portal do administrador.

1. Inicie sessão para o [portal de administração do Azure Stack](https://adminportal.local.azurestack.external).

2. A partir do Dashboard, selecione **gestão da região**.

    [![Mosaico de gestão de região](media/asdk-register/admin1sm.png "mosaico de gestão da região")](media/asdk-register/admin1.png#lightbox)

3. Selecione **propriedades**. Este painel mostra o estado e os detalhes do seu ambiente. O estado pode ser **registada** ou **não registado**. Se registrado, ela também mostra o ID de subscrição do Azure que utilizou para registar o Azure Stack, juntamente com o grupo de recursos de registo e o nome.

## <a name="move-a-registration-resource"></a>Mover um recurso de registo
Mover um recurso de registro entre grupos de recursos na mesma subscrição **é** suportado. Para obter mais informações sobre como mover recursos para um novo grupo de recursos, consulte [mover recursos para um novo grupo de recursos ou subscrição](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Passos Seguintes

- [Adicionar um item do mercado do Azure Stack](../azure-stack-marketplace.md)
