---
title: Ligue ao Gestor de Recursos Azure no seu dispositivo GPU Azure Stack Edge Pro
description: Descreve como ligar-se ao Gestor de Recursos Azure em execução no seu GPU Azure Stack Edge Pro utilizando o Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: ebadfc889eb648b734747e5a2a45662e82aab643
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546810"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-pro-device"></a>Ligue ao Gestor de Recursos Azure no seu dispositivo Azure Stack Edge Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

O Azure Resource Manager proporciona uma camada de gestão que lhe permite criar, atualizar e eliminar recursos na subscrição do Azure. O dispositivo Azure Stack Edge Pro suporta as mesmas APIs do Gestor de Recursos Azure para criar, atualizar e eliminar VMs numa subscrição local. Este suporte permite-lhe gerir o dispositivo de uma forma consistente com a nuvem. 

Este tutorial descreve como ligar-se às APIs locais no seu dispositivo Azure Stack Edge Pro através do Azure Resource Manager utilizando a Azure PowerShell.

## <a name="about-azure-resource-manager"></a>Acerca do Azure Resource Manager

O Azure Resource Manager fornece uma camada de gestão consistente para ligar para o dispositivo Azure Stack Edge Pro e executar operações como criar, atualizar e eliminar VMs. A arquitetura do Gestor de Recursos Azure é detalhada no diagrama seguinte.

![Diagrama para Gestor de Recursos Azure](media/azure-stack-edge-j-series-connect-resource-manager/edge-device-flow.svg)


## <a name="endpoints-on-azure-stack-edge-pro-device"></a>Pontos finais no dispositivo Azure Stack Edge Pro

A tabela seguinte resume os vários pontos finais expostos no seu dispositivo, os protocolos suportados e as portas para aceder a esses pontos finais. Ao longo do artigo, encontrará referências a estes pontos finais.

| # | Ponto final | Protocolos suportados | Porto utilizado | Utilizado para |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | Para ligar ao Azure Resource Manager para automação |
| 2. | Serviço de ficha de segurança | https | 443 | Para autenticar através de acesso e atualização de fichas |
| 3. | Blobs | https | 443 | Para ligar ao armazenamento blob via REST |


## <a name="connecting-to-azure-resource-manager-workflow"></a>Ligação ao fluxo de trabalho do Gestor de Recursos Azure

O processo de ligação às APIs locais do dispositivo utilizando o Gestor de Recursos Azure requer as seguintes etapas:

| Passo # | Vai dar este passo... | .. neste local. |
| --- | --- | --- |
| 1. | [Configure o seu dispositivo Azure Stack Edge Pro](#step-1-configure-azure-stack-edge-pro-device) | Local web UI |
| 2. | [Criar e instalar certificados](#step-2-create-and-install-certificates) | Windows cliente/web uI local |
| 3. | [Rever e configurar os pré-requisitos](#step-3-install-powershell-on-the-client) | Cliente Windows |
| 4. | [Configurar a Azure PowerShell no cliente](#step-4-set-up-azure-powershell-on-the-client) | Cliente Windows |
| 5. | [Modificar o ficheiro do anfitrião para resolução de nomes de ponto final](#step-5-modify-host-file-for-endpoint-name-resolution) | Cliente windows ou servidor DNS |
| 6. | [Verifique se o nome do ponto final está resolvido](#step-6-verify-endpoint-name-resolution-on-the-client) | Cliente Windows |
| 7. | [Utilize cmdlets Azure PowerShell para verificar a ligação ao Gestor de Recursos Azure](#step-7-set-azure-resource-manager-environment) | Cliente Windows |

As secções seguintes detalham cada um dos passos acima na ligação ao Gestor de Recursos Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que o cliente utilizado para ligar ao dispositivo através do Azure Resource Manager está a utilizar o TLS 1.2. Para mais informações, aceda ao [Configure TLS 1.2 no cliente windows que acede ao dispositivo Azure Stack Edge Pro](azure-stack-edge-j-series-configure-tls-settings.md).

## <a name="step-1-configure-azure-stack-edge-pro-device"></a>Passo 1: Configurar o dispositivo Azure Stack Edge Pro 

Dê os seguintes passos na UI web local do seu dispositivo Azure Stack Edge Pro.

1. Preencha as definições de rede para o seu dispositivo Azure Stack Edge Pro. 

    ![Página local de ui "definições de rede"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    Tome nota do endereço IP do dispositivo. Usará este IP mais tarde.

2. Configure o nome do dispositivo e o domínio DNS da página **Dispositivo.** Tome nota do nome do dispositivo e do domínio DNS, uma vez que os utilizará mais tarde.

    ![Página local de UI "Dispositivo"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > O nome do dispositivo, domínio DNS será utilizado para formar os pontos finais que estão expostos.
    > Utilize o Gestor de Recursos Azure e os pontos finais blob da página **dispositivo** na UI web local.


## <a name="step-2-create-and-install-certificates"></a>Passo 2: Criar e instalar certificados

Os certificados garantem que a sua comunicação é fidedigna. No seu dispositivo Azure Stack Edge Pro, os certificados auto-assinados, blob e Azure Resource Manager são gerados automaticamente. Opcionalmente, pode trazer os seus próprios certificados de blob assinado e Azure Resource Manager também.

Quando você traz um certificado assinado por si mesmo, você também precisa da cadeia de assinatura correspondente do certificado. Para a cadeia de assinaturas, Azure Resource Manager, e os certificados blob no dispositivo, necessitará dos certificados correspondentes na máquina cliente também para autenticar e comunicar com o dispositivo.

Para se ligar ao Azure Resource Manager, terá de criar ou obter certificados de cadeia e ponto final, importar estes certificados no seu cliente Windows e, finalmente, carregar estes certificados no dispositivo.

### <a name="create-certificates-optional"></a>Criar certificados (Opcional)

Apenas para o teste e uso de desenvolvimento, pode utilizar o Windows PowerShell para criar certificados no seu sistema local. Ao criar os certificados para o cliente, siga estas orientações:

1. Primeiro precisa de criar um certificado de raiz para a cadeia de assinaturas. Para obter mais informações, consulte os passos para [criar certificados de cadeia de assinatura.](azure-stack-edge-gpu-manage-certificates.md#create-signing-chain-certificate)

2. Em seguida, pode criar os certificados de ponto final para o blob e O Gestor de Recursos Azure. Pode obter estes pontos finais da página **dispositivo** na UI web local. Consulte os passos para [criar certificados de ponto final.](azure-stack-edge-gpu-manage-certificates.md#create-signed-endpoint-certificates)

3. Para todos estes certificados, certifique-se de que o nome do sujeito e o nome alternativo do sujeito estão em conformidade com as seguintes orientações:

    |Tipo |Nome do sujeito (SN)  |Nome alternativo sujeito (SAN)  |Exemplo do nome do sujeito |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Armazenamento de blobs|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Certificado único multi-SAN para ambos os pontos finais|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

Para obter mais informações sobre certificados, aceda a como [Gerir certificados.](azure-stack-edge-gpu-manage-certificates.md)

### <a name="upload-certificates-on-the-device"></a>Carregar certificados no dispositivo

Os certificados que criou no passo anterior estarão na loja Personal do seu cliente. Estes certificados precisam de ser exportados no seu cliente para ficheiros de formato apropriados que podem ser depois enviados para o seu dispositivo.

1. O certificado raiz deve ser exportado como um ficheiro de formato DER com *.cer* extensão de ficheiro. Para obter etapas detalhadas, consulte [os certificados de exportação como um ficheiro de formato .cer](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-der-format).

2. Os certificados de ponto final devem ser exportados como *ficheiros .pfx* com chaves privadas. Para etapas detalhadas, consulte [os certificados de exportação como ficheiro .pfx com chaves privadas](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

3. Os certificados raiz e ponto final são então carregados no dispositivo utilizando a opção **de certificado +Adicionar** na página **certificados** na UI web local. Para fazer o upload dos certificados, siga os passos nos [certificados upload](azure-stack-edge-gpu-manage-certificates.md#upload-certificates).


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Certificados de importação no cliente que executa a Azure PowerShell

O cliente windows onde irá invocar as APIs do Gestor de Recursos Azure precisa de estabelecer confiança com o dispositivo. Para o efeito, os certificados que criou no passo anterior devem ser importados no seu cliente Windows para a loja de certificados apropriada.

1. O certificado de raiz que exportou como formato DER com *.cer* extensão deve agora ser importado nas Autoridades de Certificados de Raiz Fidedignas no seu sistema de clientes. Para obter etapas detalhadas, consulte [os certificados de importação na loja Trusted Root Certificate Authorities.](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format)

2. Os certificados de ponto final que exportou como *.pfx* devem ser exportados como *.cer*. Esta *.cer* é então importada na loja **de certificados pessoais** no seu sistema. Para obter etapas detalhadas, consulte [certificados de importação em loja pessoal.](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format)

## <a name="step-3-install-powershell-on-the-client"></a>Passo 3: Instalar PowerShell no cliente 

O seu cliente Windows deve cumprir os seguintes requisitos:

1. Executar PowerShell Versão 5.0. Deve ter a versão 5.0 ou superior do PowerShell. Para verificar a versão do PowerShell no seu sistema, execute o seguinte cmdlet:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Compare a versão **Major** e certifique-se de que é 5.0 ou mais tarde.

    Se tem uma versão desatualizada, veja [Atualizar o Windows PowerShell existente](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell).

    Se \' não tiver PowerShell 5.0, siga [instalar o Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true).

    É apresentada abaixo uma saída de exemplo.

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. Pode aceder à Galeria PowerShell.

    Executar powerShell como administrador. Verifique se o `PSGallery` está registado como repositório.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    É apresentada abaixo uma saída de exemplo.
    
    ```powershell
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
    
Se o seu repositório não for de confiança ou precisar de mais informações, consulte [validar a acessibilidade da PowerShell Gallery](/azure-stack/operator/azure-stack-powershell-install?view=azs-1908&preserve-view=true&preserve-view=true#2-validate-the-powershell-gallery-accessibility).

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>Passo 4: Configurar a Azure PowerShell no cliente 

<!--1. Verify the API profile of the client and identify which version of the Azure PowerShell modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. Irá instalar módulos Azure PowerShell no seu cliente que irão funcionar com o seu dispositivo.

    a. Execute o PowerShell como um administrador. Precisa de acesso à galeria PowerShell. 


    b. Para instalar os módulos Azure PowerShell necessários na PowerShell Gallery, execute o seguinte comando:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
   # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    Certifique-se de que tem a versão 2.5.0 do módulo Azure-RM a funcionar no final da instalação. 
    Se tiver uma versão existente do módulo Azure-RM que não corresponda à versão pretendida, desinstale utilizando o seguinte comando:

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    Agora terá de voltar a instalar a versão necessária.
   

Uma saída de amostra é mostrada abaixo que indica que os módulos AzureRM versão 2.5.0 foram instalados com sucesso.

```powershell
PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Loading Profile 2019-03-01-hybrid
PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
 
    Directory: C:\Program Files\WindowsPowerShell\Modules
 
ModuleType Version    Name                          ExportedCommands
---------- -------    ----                          ----------------
Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
Script     2.5.0      AzureRM
Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...

 
    Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
 
ModuleType Version    Name                            ExportedCommands
---------- -------    ----                           ----------------
Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
```


## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>Passo 5: Modificar o ficheiro do anfitrião para resolução de nomes de ponto final 

Irá agora adicionar o VIP consistente Azure que definiu na uI web local do dispositivo para:

- O ficheiro do anfitrião no cliente, OU,
- A configuração do servidor DNS

> [!IMPORTANT]
> Recomendamos que modifique a configuração do servidor DNS para resolução de nomes de ponto final.

No seu cliente Windows que está a utilizar para se ligar ao dispositivo, tome os seguintes passos:

1. Inicie **o Notepad** como administrador e, em seguida, abra o ficheiro **hostis** localizado em C:\Windows\System32\Drivers\etc.

    ![Ficheiro de anfitriões do Windows Explorer](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)

2. Adicione as seguintes entradas no ficheiro dos **anfitriões** substituindo os valores adequados para o seu dispositivo: 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > A entrada no ficheiro dos anfitriões deve corresponder exatamente à fornecida para ligar ao Azure Resource Manager numa fase posterior. Certifique-se de que a entrada do Domínio DNS aqui está toda na minúscula.

    Guardou o IP do dispositivo da UI web local num passo anterior.

    O \<appliance name\> login...\<DNS domain\> a entrada é o ponto final do Serviço token de segurança (STS). A STS é responsável pela criação, validação, renovação e cancelamento de fichas de segurança. O serviço de ficha de segurança é utilizado para criar o token de acesso e o token de atualização que são usados para comunicação contínua entre o dispositivo e o cliente.

3. Para referência, utilize a seguinte imagem. Guarde o ficheiro dos **anfitriões.**

    ![hospeda ficheiro no Bloco de Notas](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>Passo 6: Verificar a resolução do nome do ponto final no cliente

Verifique se o nome do ponto final está resolvido no cliente que está a usar para ligar ao VIP consistente Azure.

1. Pode utilizar o utilitário de linha de comando ping.exe para verificar se o nome do ponto final está resolvido. Dado um endereço IP, o comando ping devolverá o nome do anfitrião TCP/IP do computador que \' está a rastrear.

    Adicione o `-a` interruptor à linha de comando como mostrado no exemplo abaixo. Se o nome do anfitrião for retornável, também devolverá esta informação potencialmente valiosa na resposta.

    ![Ping no comando imediato](media/azure-stack-edge-j-series-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>Passo 7: Definir ambiente de gestor de recursos Azure

Defina o ambiente de Gestor de Recursos Azure e verifique se o seu dispositivo para comunicação do cliente através do Azure Resource Manager está a funcionar bem. Tome as seguintes medidas para esta verificação:


1. Utilize o cmdlet `Add-AzureRmEnvironment` para garantir que a comunicação através do Azure Resource Manager está a funcionar corretamente e as chamadas à API estão a passar pela porta dedicada ao Azure Resource Manager – 443.

    O cmdlet `Add-AzureRmEnvironment` adiciona pontos finais e metadados para permitir aos cmdlets do Azure Resource Manager ligarem a uma nova instância do Azure Resource Manager. 


    > [!IMPORTANT]
    > O URL do ponto final do Azure Resource Manager que fornece no cmdlet seguinte é sensível a casos. Certifique-se de que o URL do ponto final está todo em minúsculas e corresponde ao que forneceu no ficheiro dos anfitriões. Se o caso não coincidir, verá um erro.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    Abaixo é mostrada uma saída de amostra:
    
    ```powershell
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. Defina o ambiente como Azure Stack Edge Pro e a porta a utilizar nas chamadas do Azure Resource Manager como 443. Define o ambiente de duas formas:

    - Defina o ambiente. Escreva o seguinte comando:

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    Para mais informações, aceda ao [Set-AzureRMEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true).

    - Defina o ambiente em linha para cada cmdlet que executa. Isto garante que todas as chamadas da API estão a passar pelo ambiente correto. Por padrão, as chamadas passariam pelo público do Azure, mas quer que estas passem pelo ambiente que definiu para o dispositivo Azure Stack Edge Pro.

    - Consulte mais informações sobre [como mudar os ambientes AzureRM](#switch-environments).

2. Chame as APIs do dispositivo local para autenticar as ligações ao Azure Resource Manager. 

    1. Estas credenciais são para uma conta de máquina local e são exclusivamente utilizadas para o acesso à API.

    2. Pode ligar via `login-AzureRMAccount` ou via `Connect-AzureRMAccount` comando. 

        1. Para iniciar sinsumento, digite o seguinte comando. A identificação do inquilino neste caso é codificada duramente - c0257de7-538f-415c-993a-1b87a031879d. Utilize o seguinte nome de utilizador e senha.

            - **Nome de utilizador**  -  *EdgeArmUser*

            - **Senha**  -  [Descreva a palavra-passe para O Gestor de Recursos Azure](azure-stack-edge-j-series-set-azure-resource-manager-password.md) e use esta palavra-passe para iniciar sação. 

            ```powershell
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            Uma forma alternativa de fazer login é usar o `login-AzureRmAccount` cmdlet. 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name>` -TenantId c0257de7-538f-415c-993a-1b87a031879d 

            Aqui está uma amostra do comando. 
         
            ```powershell
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```



> [!IMPORTANT]
> A ligação ao Azure Resource Manager expira a cada 1,5 horas ou se o seu dispositivo Azure Stack Edge Pro recomeçar. Se isso acontecer, quaisquer cmdlets que execute, retornarão mensagens de erro no sentido de que já não está ligado ao Azure. Terá que assinar de novo.

## <a name="switch-environments"></a>Mudar de ambiente

Executar `Disconnect-AzureRmAccount` o comando para mudar para um diferente `AzureRmEnvironment` . 

Se utilizar `Set-AzureRmEnvironment` e `Login-AzureRmAccount` sem `Disconnect-AzureRmAccount` utilização, o ambiente não está realmente comutado.  

Os exemplos que se seguem mostram como alternar entre dois `AzDBE1` ambientes, e `AzDBE2` .

Primeiro, enuprova todos os ambientes existentes no seu cliente.


```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

Em seguida, obtenha qual o ambiente a que está atualmente ligado através do seu Gestor de Recursos Azure.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

Deve desligar-se do ambiente atual antes de mudar para outro ambiente.


```azurepowershell
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, A4257FDE-B946-4E01-ADE7-674760B8D1A3], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Entre no outro ambiente. A saída de exemplo é apresentada abaixo.

```azurepowershell
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Executar este cmdlet para confirmar a que ambiente está ligado.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
Mudou agora para o ambiente pretendido.

## <a name="next-steps"></a>Passos seguintes

[Coloque VMs no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).