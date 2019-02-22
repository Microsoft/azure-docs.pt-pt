---
title: Sistemas integrados do registo do Azure para o Azure Stack | Documentos da Microsoft
description: Descreve o processo de registo do Azure para implementações de ligada ao Azure Stack Azure com vários nós.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2019
ms.author: jeffgilb
ms.reviewer: brbartle
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: ebf8066139df93aefe1cfa21f2dc80ab57ca84bb
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652455"
---
# <a name="register-azure-stack-with-azure"></a>Registar o Azure Stack com o Azure

Registar o Azure Stack com o Azure permite-lhe para transferir itens do marketplace do Azure e configurar relatórios de volta à Microsoft de dados de comércio. Depois de registar o Azure Stack, utilização é comunicada ao Azure commerce e pode vê-lo sob o ID de subscrição de faturação do Azure, utilizadas para registo.

As informações neste artigo descrevem o registo de integrado do Azure Stack de sistemas, com o Azure. Para obter informações sobre como registar o ASDK com o Azure, consulte [registo do Azure Stack](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-register) na documentação do ASDK.

> [!IMPORTANT]  
> É necessário Registro para dar suporte a todas as funcionalidades do Azure Stack, incluindo a oferta de itens no marketplace. Além disso, será estar em violação dos termos de licenciamento, se não registar ao utilizar o modelo de faturação de pagamento-como-utiliza do Azure Stack. Para saber mais sobre o Azure Stack, modelos de licenciamento, consulte a [como comprar página](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Pré-requisitos

É necessário o seguinte em vigor antes de registar:

 - Verifique as suas credenciais
 - Definir o modo de idioma do PowerShell
 - Instalar o PowerShell para o Azure Stack
 - Baixe as ferramentas do Azure Stack
 - Determinar o seu cenário de registo

### <a name="verify-your-credentials"></a>Verifique as suas credenciais

Antes de registar o Azure Stack com o Azure, tem de ter:

- O ID de subscrição para uma subscrição do Azure. Apenas EA, CSP ou CSP partilhado subscrições são suportadas para o registo de serviços. CSPs tem de decidir se pretende [utilizar uma subscrição do CSP ou APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>Para obter o ID, inicie sessão no Azure, clique em **todos os serviços**. Em seguida, no **gerais** categoria, selecione **subscrições**, clique na subscrição que pretende utilizar, e, em **Essentials** pode encontrar o ID de subscrição.

  > [!Note]  
  > Subscrições de cloud da Alemanha não são atualmente suportadas.

- O nome de utilizador e palavra-passe para uma conta que seja o proprietário da subscrição.

- A conta de utilizador tem de ter acesso à subscrição do Azure e ter permissões para criar aplicações de identidades e de principais de serviço no diretório associado a essa subscrição. Recomendamos que registe o Azure Stack com o Azure usando a administração de privilégio mínimo. Para obter mais informações sobre como criar uma definição de função personalizada que limite o acesso à sua subscrição para o registo, consulte [criar uma função de registo para o Azure Stack](azure-stack-registration-role.md).

- Registado o fornecedor de recursos do Azure Stack (consulte a secção de registar o fornecedor de recursos do Azure Stack seguinte para obter detalhes).

Após o registo, a permissão de administrador global do Azure Active Directory não é necessário. No entanto, algumas operações podem exigir a credencial de administrador global. Por exemplo, um script de instalador de fornecedor de recursos ou um novo recurso que requerem uma permissão para ser concedida. Pode temporariamente permissões de administrador global da conta de restabelecimento ou utilizar uma conta de administrador global separado que é proprietária dos *predefinido da subscrição do fornecedor*.

O utilizador que regista o Azure Stack é o proprietário do serviço principal no Azure Active Directory. Apenas o utilizador que registou o Azure Stack pode modificar o registo do Azure Stack. Se um utilizador não administrador que não é um proprietário ao principal de serviço de registo tentar registar ou volte a registar o Azure Stack, que se possam deparar uma resposta 403. Uma resposta 403 indica que o utilizador tem permissões insuficientes para concluir a operação.

Se não tiver uma subscrição do Azure que cumpra estes requisitos, pode [criar uma conta gratuita do Azure aqui](https://azure.microsoft.com/free/?b=17.06). Registar o Azure Stack, incorre em sem custos na sua subscrição do Azure.

### <a name="powershell-language-mode"></a>Modo de idioma do PowerShell

Para registar com êxito o Azure Stack, o modo de idioma do PowerShell deve ser definido como **FullLanguageMode**.  Para verificar se o modo de idioma atual está definido como completa, abra uma janela elevada do PowerShell e execute os seguintes cmdlets do PowerShell:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Certifique-se de que a saída devolve **FullLanguageMode**. Se qualquer outro modo de idioma é retornado, registo tem de ser executado em outra máquina ou o modo de idioma tem de ser definido como **FullLanguageMode** antes de continuar.

### <a name="install-powershell-for-azure-stack"></a>Instalar o PowerShell para o Azure Stack

Utilize o PowerShell mais recente para o Azure Stack para registar com o Azure.

Se não a versão mais recente ainda não estiver instalada, veja [instalar o PowerShell para o Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="download-the-azure-stack-tools"></a>Baixe as ferramentas do Azure Stack

O repositório de GitHub de ferramentas do Azure Stack contém módulos do PowerShell que suportam a funcionalidade do Azure Stack; incluindo a funcionalidade de registo. Durante o processo de registo, terá de importar e utilizar o **RegisterWithAzure.psm1** módulo do PowerShell, disponível no repositório de ferramentas do Azure Stack, para registar a sua instância do Azure Stack com o Azure.

Para garantir que está a utilizar a versão mais recente, deve excluir as versões existentes das ferramentas do Azure Stack e [transferir a versão mais recente a partir do GitHub](azure-stack-powershell-download.md) antes de registar com o Azure.

### <a name="determine-your-registration-scenario"></a>Determinar o seu cenário de registo

Pode ser a sua implementação do Azure Stack *ligados* ou *desligado*.

 - **Connected**  
 Ligar significa que ter implementado o Azure Stack para que possam ligar à Internet e para o Azure. Ter Azure Active Directory (Azure AD) ou serviços de Federação do Active Directory (AD FS) para seu armazenamento de identidade. Com uma implementação de ligado, pode escolher entre dois modelos de faturação: pay-as que use ou baseada em capacidade.
    - [Registar uma ligada do Azure Stack com o Azure com o **pay-as que use** modelo de faturação](#register-connected-with-pay-as-you-go-billing)
    - [Registar uma ligada do Azure Stack com o Azure com o **capacidade** modelo de faturação](#register-connected-with-capacity-billing)

 - **Desligado**  
 Com os que estão da opção de implementação do Azure, pode implementar e utilizar o Azure Stack sem uma ligação à Internet. No entanto, com uma implementação de desligado, está limitado a um repositório de identidades do AD FS e o modelo de faturação com base na capacidade.
    - [Registe-se de que um através do Azure Stack desligado a **capacidade** modelo de faturação ](#register-disconnected-with-capacity-billing)

### <a name="determine-a-unique-registration-name-to-use"></a>Determinar um nome de registo único para utilizar 
Quando registar o Azure Stack com o Azure, tem de fornecer um nome de registo único. Uma forma fácil de associar a sua subscrição do Azure Stack com um registo do Azure está a utilizar do Azure Stack **ID de Cloud**. 

> [!NOTE]
> Registos de pilha do Azure com o modelo de faturação com base na capacidade, terá de alterar o nome exclusivo ao registar novamente depois que essas subscrições anuais expirarem, a menos que [eliminar o registo expirado](azure-stack-registration.md#change-the-subscription-you-use) e voltar a registar com Azure.

Para determinar o ID de Cloud para a sua implementação do Azure Stack, abra o PowerShell como administrador no computador que pode aceder ao ponto final com privilégios, execute os seguintes comandos e registe os **CloudID** valor: 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: get-azurestackstampinformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>Registre-se conectado com a faturação pay as you go

Utilize estes passos para registar o Azure Stack com o Azure com o modelo de faturação de pagamento-como-utiliza.

> [!Note]  
> Todos estes passos tem de ser executados a partir de um computador que tenha acesso ao ponto final com privilégios (PEP). Para obter detalhes sobre o PEP, consulte [utilizando o ponto final com privilégios no Azure Stack](azure-stack-privileged-endpoint.md).

Podem aceder a ambientes conectados à internet e Azure. Para estes ambientes, terá de registar o fornecedor de recursos do Azure Stack com o Azure e, em seguida, configurar o modelo de faturação.

1. Para registar o fornecedor de recursos do Azure Stack com o Azure, inicie o ISE do PowerShell como administrador e utilize os seguintes cmdlets do PowerShell com o **EnvironmentName** parâmetro definido como o tipo de subscrição do Azure adequada (consulte a parâmetros abaixo).

2. Adicione a conta do Azure que utiliza para registar o Azure Stack. Para adicionar a conta, execute o **Add-AzureRmAccount** cmdlet. Lhe for pedido que introduza as credenciais de conta do Azure e poderá ter de utilizar a autenticação de 2 fatores com base na configuração da sua conta.

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parâmetro | Descrição |  
   |-----|-----|
   | EnvironmentName | O nome de ambiente de subscrição de cloud do Azure. Os nomes de ambiente com suporte são **AzureCloud**, **AzureUSGovernment**, ou se utilizar uma subscrição do Azure China **AzureChinaCloud**.  |

3. Se tiver várias subscrições, execute o seguinte comando para selecionar a que pretende utilizar:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Execute o seguinte comando para registar o fornecedor de recursos do Azure Stack na sua subscrição do Azure:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Inicie o ISE do PowerShell como administrador e navegue para o **registo** pasta na **AzureStack-Tools-mestre** diretório criado quando transferiu as ferramentas do Azure Stack. Importar os **RegisterWithAzure.psm1** módulo com o PowerShell:

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Em seguida, na mesma sessão do PowerShell, certifique-se que estiver conectado ao contexto correto de PowerShell do Azure. Esta é a conta do Azure que foi utilizada para registar o fornecedor de recursos do Azure Stack anteriormente. PowerShell para executar:

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parâmetro | Descrição |  
   |-----|-----|
   | EnvironmentName | O nome de ambiente de subscrição de cloud do Azure. Os nomes de ambiente com suporte são **AzureCloud**, **AzureUSGovernment**, ou se utilizar uma subscrição do Azure China **AzureChinaCloud**.  |

7. Na mesma sessão do PowerShell, execute o **Set-AzsRegistration** cmdlet. PowerShell para executar:  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Para obter mais informações sobre o cmdlet Set-AzsRegistration, consulte [referência de registo](#registration-reference).

  O processo demora entre 10 a 15 minutos. Quando o comando for concluído, verá a mensagem **"o seu ambiente está agora registado e ativado usando os parâmetros fornecidos."**

## <a name="register-connected-with-capacity-billing"></a>Registre-se conectado com a faturação de capacidade

Utilize estes passos para registar o Azure Stack com o Azure com o modelo de faturação de pagamento-como-utiliza.

> [!Note]  
> Todos estes passos tem de ser executados a partir de um computador que tenha acesso ao ponto final com privilégios (PEP). Para obter detalhes sobre o PEP, consulte [utilizando o ponto final com privilégios no Azure Stack](azure-stack-privileged-endpoint.md).

Podem aceder a ambientes conectados à internet e Azure. Para estes ambientes, terá de registar o fornecedor de recursos do Azure Stack com o Azure e, em seguida, configurar o modelo de faturação.

1. Para registar o fornecedor de recursos do Azure Stack com o Azure, inicie o ISE do PowerShell como administrador e utilize os seguintes cmdlets do PowerShell com o **EnvironmentName** parâmetro definido como o tipo de subscrição do Azure adequada (consulte a parâmetros abaixo).

2. Adicione a conta do Azure que utiliza para registar o Azure Stack. Para adicionar a conta, execute o **Add-AzureRmAccount** cmdlet. Lhe for pedido que introduza as credenciais de conta do Azure e poderá ter de utilizar a autenticação de 2 fatores com base na configuração da sua conta.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parâmetro | Descrição |  
   |-----|-----|
   | EnvironmentName | O nome de ambiente de subscrição de cloud do Azure. Os nomes de ambiente com suporte são **AzureCloud**, **AzureUSGovernment**, ou se utilizar uma subscrição do Azure China **AzureChinaCloud**.  |

3. Se tiver várias subscrições, execute o seguinte comando para selecionar a que pretende utilizar:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Execute o seguinte comando para registar o fornecedor de recursos do Azure Stack na sua subscrição do Azure:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Inicie o ISE do PowerShell como administrador e navegue para o **registo** pasta na **AzureStack-Tools-mestre** diretório criado quando transferiu as ferramentas do Azure Stack. Importar os **RegisterWithAzure.psm1** módulo com o PowerShell:

  ```PowerShell  
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  $RegistrationName = "<unique-registration-name>"
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
  ```
   > [!Note]  
   > Pode desativar a utilização de relatórios com o parâmetro UsageReportingEnabled para o **Set-AzsRegistration** cmdlet, definindo o parâmetro como false. 
   
  Para obter mais informações sobre o cmdlet Set-AzsRegistration, consulte [referência de registo](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>Registre-se desligado com a faturação de capacidade

Se estiver a registar Azure Stack num ambiente desligado (com nenhuma conectividade com a internet), terá de obter um registo de token do ambiente do Azure Stack e, em seguida, utilizar esse token num computador que pode ligar ao Azure e com o PowerShell para o Azure Stack instalado.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obter um registo de token do ambiente do Azure Stack

1. Inicie o ISE do PowerShell como administrador e navegue para o **registo** pasta na **AzureStack-Tools-mestre** diretório criado quando transferiu as ferramentas do Azure Stack. Importar os **RegisterWithAzure.psm1** módulo:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Para obter o token de registo, execute os seguintes cmdlets do PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Para obter mais informações sobre o cmdlet Get-AzsRegistrationToken, consulte [referência de registo](#registration-reference).

   > [!Tip]  
   > O token de registo é guardado no ficheiro especificado para *$FilePathForRegistrationToken*. Pode alterar o caminho do ficheiro ou o nome de ficheiro a seu critério.

3. Guarde este token de registo para utilização no Azure ligado máquina. Pode copiar o ficheiro ou o texto de $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Ligar ao Azure e registe-se

No computador que está ligado à Internet, execute os mesmos passos para importar o módulo de RegisterWithAzure.psm1 e inicie sessão para o contexto correto do Azure Powershell. Em seguida, chame AzsEnvironment Registre-se. Especifique o token de registo para registar com o Azure. Se estiver a registar mais de uma instância do Azure Stack com o mesmo ID de subscrição do Azure, especifique um nome de registo único. Execute o seguinte cmdlet:

  ```PowerShell  
  $registrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  -RegistrationName $RegistrationName
  ```

Opcionalmente, pode utilizar o cmdlet Get-Content para apontar para um ficheiro que contém o token de registo:

  ```PowerShell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken -RegistrationName $RegistrationName
  ```

  > [!Note]  
  > Guarde o nome do registo de recursos e o token de registo para referência futura.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Obter uma chave de ativação do recurso de registo do Azure

Em seguida, terá de obter uma chave de ativação do recurso de registo criado no Azure durante AzsEnvironment Registre-se.

Para obter a chave de ativação, execute os seguintes cmdlets do PowerShell:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > A chave de ativação é guardada no ficheiro especificado para *$KeyOutputFilePath*. Pode alterar o caminho do ficheiro ou o nome de ficheiro a seu critério.

### <a name="create-an-activation-resource-in-azure-stack"></a>Criar um recurso de ativação no Azure Stack

Regressar ao ambiente do Azure Stack com o ficheiro ou o texto da chave de ativação criada a partir de Get-AzsActivationKey. Em seguida, vai criar um recurso de ativação no Azure Stack com essa chave de ativação. Para criar um recurso de ativação, execute os seguintes cmdlets do PowerShell:  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Opcionalmente, pode utilizar o cmdlet Get-Content para apontar para um ficheiro que contém o token de registo:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Verifique se o registo do Azure Stack

Pode utilizar o **gestão da região** mosaico para verificar se o registo do Azure Stack foi concluída com êxito. Este mosaico não está disponível no dashboard predefinido no portal do administrador. O estado pode estar registado ou não registado. Se registrado, ela também mostra o ID de subscrição do Azure que utilizou para registar o Azure Stack, juntamente com o grupo de recursos de registo e o nome.

1. Inicie sessão para o [portal de administração do Azure Stack](https://adminportal.local.azurestack.external).

2. A partir do Dashboard, selecione **gestão da região**.

3. Selecione **propriedades**. Este painel mostra o estado e os detalhes do seu ambiente. O estado pode ser **registada** ou **não registado**.

    [ ![Mosaico de gestão de região](media/azure-stack-registration/admin1sm.png "mosaico de gestão da região") ](media/azure-stack-registration/admin1.png#lightbox)

    Se registrado, as propriedades incluem:
    
    - **ID de subscrição de registo**: O ID de subscrição do Azure, registado e associados ao Azure Stack
    - **Grupo de recursos de registo**: O grupo de recursos do Azure na subscrição associada que contém os recursos do Azure Stack.

4. Utilize o portal do Azure para ver os registos de aplicações do Azure Stack. Inicie sessão no portal do Azure com uma conta associada à subscrição utilizada para registar o Azure Stack. Mudar para o inquilino associado com o Azure Stack.
5. Navegue para **do Azure Active Directory > registos de aplicações > ver todas as aplicações**.

    ![Registos de aplicações](media/azure-stack-registration/app-registrations.png)

    Registos de aplicações do Azure Stack têm o prefixo **do Azure Stack**.

Em alternativa, pode verificar se o registo foi concluída com êxito ao utilizar a funcionalidade de gestão do Marketplace. Se vir uma lista de itens do marketplace no painel de gestão do Marketplace, o registo foi concluída com êxito. No entanto, em ambientes desligados, não será capaz de ver os itens do marketplace no gerenciamento do Marketplace. No entanto, pode utilizar a ferramenta de offline para verificar o registo.

> [!NOTE]
> Após a conclusão do registo, já não vai aparecer o aviso de Active Directory para não registar. Em cenários desconectados, verá uma mensagem na gestão de mercado que lhe pede para se registrar e ativar o Azure Stack, mesmo que se registrou com êxito.

## <a name="renew-or-change-registration"></a>Renovar ou alterar o registo

### <a name="renew-or-change-registration-in-connected-environments"></a>Renovar ou alterar o registo em ambientes ligados

Terá de atualizar ou renovar o registo nas seguintes circunstâncias:

- Depois de renovar sua assinatura anual baseado em capacidades.
- Quando altera o modelo de faturação.
- Quando dimensionar as alterações (adicionar ou remover nós) para faturação com base na capacidade.

#### <a name="change-the-subscription-you-use"></a>Alterar a subscrição que utilizar

Se desejar alterar a subscrição que utilizar, tem de executar primeiro o **Remove-AzsRegistration** cmdlet, em seguida, certifique-se estiver conectado ao contexto correto do Azure PowerShell e, finalmente execute **AzsRegistration de conjunto**  com quaisquer parâmetros alterados, incluindo \<modelo de faturação\>:

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Alterar o modelo de faturação ou como oferecer funcionalidades

Se quiser alterar o modelo de faturação ou como oferecer funcionalidades para a sua instalação, pode chamar a função de Registro para definir os valores novos. Não é necessário primeiro de remover o registo atual:

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Renovar ou alterar o registo em ambientes desligados

Terá de atualizar ou renovar o registo nas seguintes circunstâncias:

- Depois de renovar sua assinatura anual baseado em capacidades.
- Quando altera o modelo de faturação.
- Quando dimensionar as alterações (adicionar ou remover nós) para faturação com base na capacidade.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Remover o recurso de ativação do Azure Stack

Tem primeiro de remover o recurso de ativação do Azure Stack e, em seguida, o recurso de registo no Azure.  

Para remover o recurso de ativação no Azure Stack, execute os seguintes cmdlets do PowerShell no seu ambiente do Azure Stack:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Em seguida, para remover o recurso de registo no Azure, certifique-se estiver a utilizar um Azure ligado computador, inicie sessão para o contexto correto do Azure PowerShell e execute os cmdlets apropriados do PowerShell, conforme descrito abaixo.

Pode usar o token de registo utilizado para criar o recurso:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Em alternativa, pode utilizar o nome do registo:

  ```Powershell
  $registrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Voltar a registar utilizando passos desligados

Tem agora completamente registo anulado com êxito num cenário de desligado e terá de repetir os passos para registar um ambiente do Azure Stack num cenário de desligado.

### <a name="disable-or-enable-usage-reporting"></a>Desativar ou ativar o relatório de utilização

Para ambientes do Azure Stack que utilizam um modelo de faturação de capacidade, desativar a utilização de relatórios com o **UsageReportingEnabled** parâmetro através de um a **conjunto AzsRegistration** ou a  **Get-AzsRegistrationToken** cmdlets. O Azure Stack relatórios de métrica de utilização por predefinição. Operadores com utilizações de capacidade ou suporte a um ambiente desligado tem de desativar os relatórios de utilização.

#### <a name="with-a-connected-azure-stack"></a>Com um ligado do Azure Stack

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>Com um Azure Stack desligado

1. Para alterar o token de registo, execute os seguintes cmdlets do PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > O token de registo é guardado no ficheiro especificado para *$FilePathForRegistrationToken*. Pode alterar o caminho do ficheiro ou o nome de ficheiro a seu critério.

2. Guarde este token de registo para utilização no Azure ligado máquina. Pode copiar o ficheiro ou o texto de $FilePathForRegistrationToken.

## <a name="move-a-registration-resource"></a>Mover um recurso de registo
Mover um recurso de registro entre grupos de recursos na mesma subscrição **é** suportados para todos os ambientes. No entanto, mover um recurso de registro entre subscrições é apenas suportada para CSPs quando ambas as subscrições resolver para o mesmo ID de parceiro. Para obter mais informações sobre como mover recursos para um novo grupo de recursos, consulte [mover recursos para um novo grupo de recursos ou subscrição](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="registration-reference"></a>Referência de registo

### <a name="set-azsregistration"></a>Set-AzsRegistration

Pode usar o conjunto AzsRegistration para registar o Azure Stack com o Azure e ativar ou desativar a oferta de itens no marketplace e relatórios de utilização.

Para executar o cmdlet, terá de:
- Uma subscrição do Azure global de qualquer tipo.
- Deve também ter sessão iniciada Azure PowerShell com uma conta que seja proprietário ou contribuinte à subscrição.

```PowerShell
    Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
   ```

| Parâmetro | Type | Descrição |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | As credenciais utilizadas para [aceder ao ponto final com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). O nome de utilizador está no formato **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | String | Uma pré-configurado remota consola do PowerShell que fornece-lhe capacidades como a recolha de registos e outro post tarefas de implementação. Para obter mais informações, consulte a [utilizando o ponto final com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artigo. |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | O modelo de faturação que utiliza a sua subscrição. Valores permitidos para este parâmetro são: Capacidade, PayAsYouUse e desenvolvimento. |
| MarketplaceSyndicationEnabled | Verdadeiro/Falso | Determina se é ou não a funcionalidade de gestão do marketplace está disponível no portal. Definido como VERDADEIRO se registar com ligação à internet. Definido como FALSO se registar em ambientes desligados. Para registros desconectados, o [ferramenta de distribuição offline](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) pode ser utilizado para transferência de itens do marketplace. |
| UsageReportingEnabled | Verdadeiro/Falso | O Azure Stack relatórios de métrica de utilização por predefinição. Operadores com utilizações de capacidade ou suporte a um ambiente desligado tem de desativar os relatórios de utilização. Valores permitidos para este parâmetro são: VERDADEIRO, FALSO. |
| AgreementNumber | String |  |
| RegistrationName | String | Defina um nome exclusivo para o registo, se estiver a executar o script de Registro em mais de uma instância do Azure Stack com o mesmo Azure ID de subscrição. O parâmetro tem um valor predefinido de **AzureStackRegistration**. No entanto, se utilizar o mesmo nome em mais de uma instância do Azure Stack, o script falhará. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken gera um token de registo a partir os parâmetros de entrada.

```PowerShell  
    Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Parâmetro | Type | Descrição |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | As credenciais utilizadas para [aceder ao ponto final com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). O nome de utilizador está no formato **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | String |  Uma pré-configurado remota consola do PowerShell que fornece-lhe capacidades como a recolha de registos e outro post tarefas de implementação. Para obter mais informações, consulte a [utilizando o ponto final com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artigo. |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | O modelo de faturação que utiliza a sua subscrição. Valores permitidos para este parâmetro são: Capacidade, PayAsYouUse e desenvolvimento. |
| MarketplaceSyndicationEnabled | Verdadeiro/Falso |  |
| UsageReportingEnabled | Verdadeiro/Falso | O Azure Stack relatórios de métrica de utilização por predefinição. Operadores com utilizações de capacidade ou suporte a um ambiente desligado tem de desativar os relatórios de utilização. Valores permitidos para este parâmetro são: VERDADEIRO, FALSO. |
| AgreementNumber | String |  |

## <a name="registration-failures"></a>Falhas de registo

Poderá ver um dos erros abaixo ao tentar o registo do seu Azure Stack:
1. Não foi possível obter as informações de hardware obrigatório para $hostName. . Verifique o anfitrião físico e conectividade em seguida, tente executar novamente o registo.
2. Não é possível ligar ao $hostName para obter informações de hardware –. Verifique o anfitrião físico e conectividade em seguida, tente executar novamente o registo.

Causa: Normalmente, é porque estamos a tentar obter os detalhes de hardware, como o UUID, Bios e CPU dos anfitriões para tentar a ativação e não foi possível devido à incapacidade de ligar ao anfitrião físico.

## <a name="next-steps"></a>Passos Seguintes

[Transferir itens do marketplace do Azure](azure-stack-download-azure-marketplace-item.md)
