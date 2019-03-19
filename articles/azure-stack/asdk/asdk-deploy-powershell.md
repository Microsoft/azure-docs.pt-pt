---
title: Implementar o Azure Stack - PowerShell | Documentos da Microsoft
description: Neste artigo, é possível instalar o ASDK da linha de comando com o PowerShell.
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
ms.custom: ''
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: e8d3653049b0160fac155ebc49329c17cb27224f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58014382"
---
# <a name="deploy-the-asdk-from-the-command-line"></a>Implementar o ASDK da linha de comando
O ASDK é um ambiente de teste e desenvolvimento que pode implementar para avaliar e demonstrar funcionalidades do Azure Stack e serviços. Para obtê-lo em execução, terá de preparar o hardware de ambiente e executar alguns scripts (vai demorar várias horas). Depois disso, pode iniciar sessão nos portais de administrador e utilizador para começar a utilizar o Azure Stack.

## <a name="prerequisites"></a>Pré-requisitos 
Prepare o computador de anfitrião do kit de desenvolvimento. Planear o hardware, software e rede. O computador que aloja o development kit (o anfitrião do kit de desenvolvimento) têm de cumprir os requisitos de rede, software e hardware. Também tem de escolher entre a utilização do Azure Active Directory (Azure AD) ou serviços de Federação do Active Directory (AD FS). Certifique-se de que está em conformidade com estes pré-requisitos antes de iniciar a implementação, para que o processo de instalação é executado sem problemas. 

Antes de implementar o ASDK, certifique-se de que hardware de desenvolvimento planeada kit anfitrião do seu computador, sistema operativo, conta e configurações de rede cumprem os requisitos mínimos para instalar o ASDK.

**[Reveja as considerações de planeamento de implementação de ASDK](asdk-deploy-considerations.md)**

> [!TIP]
> Pode utilizar o [ferramenta de verificação de requisitos de implementação do Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) depois de instalar o sistema operativo para confirmar que o hardware cumpre todos os requisitos.

## <a name="download-and-extract-the-deployment-package"></a>Baixe e extraia o pacote de implementação
Depois de assegurar que seu computador de anfitrião do kit de desenvolvimento cumpre os requisitos básicos para instalar o ASDK, a próxima etapa é baixar e extrair o pacote de implementação de ASDK. O pacote de implementação inclui o arquivo de Cloudbuilder.vhdx, que é uma unidade de disco rígida virtual que inclui um sistema operativo de arranque e os ficheiros de instalação do Azure Stack.

Pode transferir o pacote de implementação para o anfitrião do kit de desenvolvimento ou para outro computador. Os arquivos de implantação extraídos demorar até 60 GB de espaço livre em disco, pelo que utilizar outro computador pode ajudar a reduzir os requisitos de hardware para o anfitrião do kit de desenvolvimento.

**[Baixe e extraia o Azure Stack Development Kit (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Preparar o computador de anfitrião do kit de desenvolvimento
Antes de poder instalar o ASDK no computador anfitrião, o ambiente tem de ser preparado e o sistema configurado para arranque a partir de VHD. Após este passo, o anfitrião do kit de desenvolvimento será inicializado para o Cloudbuilder.vhdx (um disco rígido virtual que inclui um sistema operativo de arranque e os ficheiros de instalação do Azure Stack).

Utilize o PowerShell para configurar o computador do anfitrião ASDK para arrancar a partir CloudBuilder.vhdx. Estes comandos configuram o computador de anfitrião ASDK para arrancar a partir do transferidos e extraído do Azure Stack virtual do disco rígido (CloudBuilder.vhdx). Depois de concluir estes passos, reinicie o computador do anfitrião ASDK.

Para configurar o computador do anfitrião ASDK para arrancar a partir CloudBuilder.vhdx:

  1. Inicie uma linha de comandos como administrador.
  2. Execute `bcdedit /copy {current} /d "Azure Stack"`
  3. Copiar (CTRL + C) o valor CLSID retornado, incluindo o necessário {}' s. Este valor é referido como {CLSID} e terá de ser colado em (CTRL + V ou o botão direito do mouse) nas etapas restantes.
  4. Execute `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
  5. Execute `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
  6. Execute `bcdedit /set {CLSID} detecthal on` 
  7. Execute `bcdedit /default {CLSID}`
  8. Para verificar as definições de arranque, execute `bcdedit`. 
  9. Certifique-se de que o CloudBuilder.vhdx ficheiro foi movido para a raiz da unidade C:\ (C:\CloudBuilder.vhdx) e reinicie o computador de anfitrião do kit de desenvolvimento. Quando o computador do anfitrião ASDK é reiniciado, ele deve inicializar a partir de unidade de disco rígido da máquina virtual CloudBuilder.vhdx para iniciar a implantação de ASDK. 

> [!IMPORTANT]
> Certifique-se de que tem física direta ou acesso KVM para o computador de anfitrião do kit de desenvolvimento antes de o reiniciar. Quando a VM é iniciado pela primeira vez, pede-lhe para concluir a instalação do Windows Server. Fornece as mesmas credenciais de administrador que utilizou para iniciar sessão no computador de anfitrião do kit de desenvolvimento. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Preparar o anfitrião do kit de desenvolvimento com o PowerShell 
Depois do development kit do computador anfitrião com êxito efetua o arranque em imagem CloudBuilder.vhdx, inicie sessão com as mesmas credenciais de administrador local que utilizou para iniciar sessão no computador de anfitrião do kit de desenvolvimento (e fornecida como parte da finalização do Windows Server Configure quando o computador anfitrião inicializado a partir de VHD). 

> [!NOTE]
> Opcionalmente, também pode configurar [definições de telemetria do Azure Stack](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *antes de* o ASDK a instalar.

Abra uma consola elevada do PowerShell e execute os comandos nesta secção para implementar o ASDK no anfitrião do kit de desenvolvimento.

> [!IMPORTANT] 
> Instalação de ASDK suporta exatamente uma placa de interface de rede (NIC) para funcionamento em rede. Se tiver várias NICs, certifique-se de que apenas uma está ativada (e todas as outras desativadas) antes de executar o script de implementação.

Pode implementar o Azure Stack com o Azure AD ou o Windows Server AD FS como o fornecedor de identidade. O Azure Stack, fornecedores de recursos e outros aplicativos funcionam da mesma maneira com ambos.

> [!TIP]
> Se não fornecer quaisquer parâmetros de configuração (consulte InstallAzureStackPOC.ps1 os parâmetros opcionais e os exemplos abaixo), lhe for pedido para os parâmetros necessários.

### <a name="deploy-azure-stack-using-azure-ad"></a>Implementar o Azure Stack com o Azure AD 
Para implementar o Azure Stack **utilizar o Azure AD como fornecedor de identidade**, tem de ter conectividade à internet diretamente ou através de um proxy transparente. 

Execute os seguintes comandos do PowerShell para implementar o kit de desenvolvimento com o Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Alguns minutos para instalação ASDK, que ser-lhe-á pedido para credenciais do Azure AD. Tem de fornecer credenciais de administrador global do seu inquilino do Azure AD. 

Após a implementação, a permissão de administrador global do Azure Active Directory não é necessário. No entanto, algumas operações podem exigir a credencial de administrador global. Por exemplo, um script de instalador de fornecedor de recursos ou um novo recurso que requerem uma permissão para ser concedida. Pode temporariamente instate novamente as permissões de administrador global da conta ou utilizar uma conta de administrador global separado que é proprietária dos *predefinido da subscrição do fornecedor*.

### <a name="deploy-azure-stack-using-ad-fs"></a>Implementar o Azure Stack através do AD FS 
Para implementar o development kit **através do AD FS como o fornecedor de identidade**, execute os seguintes comandos do PowerShell (apenas tem de adicionar o parâmetro - UseADFS): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

Nas implementações do AD FS, o serviço de diretório do carimbo de padrão é utilizado como o fornecedor de identidade. A conta predefinida para iniciar sessão com é azurestackadmin@azurestack.local, e a palavra-passe será definida como o que é fornecido como parte dos comandos de instalação do PowerShell.

O processo de implementação pode demorar algumas horas, período durante o qual o automaticamente reinicializações do sistema, uma vez. Quando a implementação com êxito, exibe a consola do PowerShell: **CONCLUA: A ação "Implementação"**. Se a implementação falhar, pode tentar executar o script novamente usando o - parâmetro de executar novamente. Também pode [Reimplementar ASDK](asdk-redeploy.md) do zero.

> [!IMPORTANT]
> Se quiser monitorizar o progresso da implementação, após o anfitrião ASDK é reiniciado, tem de iniciar sessão como AzureStack\AzureStackAdmin. Se iniciar sessão como administrador local depois do computador anfitrião é reiniciado (e associado ao domínio azurestack), não verá o progresso da implementação. Não execute novamente a implementação, em vez disso, inicie sessão como azurestack validar que está em execução.


#### <a name="azure-ad-deployment-script-examples"></a>Exemplos de script de implementação do Azure AD
Pode criar scripts de toda a implementação do Azure AD. Aqui estão alguns exemplos comentados que incluem alguns parâmetros opcionais.

Se a sua identidade do Azure AD está apenas associada **um** diretório do Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Se a sua identidade do Azure AD está associada **maior que um** diretório do Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Se o seu ambiente não tiver o DHCP ativado, tem de incluir os seguintes parâmetros adicionais para uma das opções acima (utilização de exemplo fornecidos): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>Parâmetros opcionais ASDK InstallAzureStackPOC.ps1

|Parâmetro|Obrigatório/opcional|Descrição|
|-----|-----|-----|
|AdminPassword|Necessário|Define a conta de administrador local e todas as outras contas de utilizador de todas as máquinas de virtuais criadas como parte da implementação do kit de desenvolvimento. Esta palavra-passe tem de corresponder a atual palavra-passe de administrador local no anfitrião.|
|InfraAzureDirectoryTenantName|Necessário|Define o diretório de inquilinos. Utilize este parâmetro para especificar um diretório específico em que a conta do AAD tem permissões para gerir vários diretórios. Total de nome de um diretório do inquilino do AAD no formato de. onmicrosoft.com ou um Azure AD verificar o nome de domínio personalizado.|
|TimeServer|Necessário|Utilize este parâmetro para especificar um servidor de tempo específico. Este parâmetro tem de ser fornecido como um endereço IP do servidor de tempo válida. Não são suportados nomes de servidor.|
|InfraAzureDirectoryTenantAdminCredential|Opcional|Define o nome de utilizador do Azure Active Directory e a palavra-passe. Estas credenciais do Azure tem de ser um ID da organização.|
|InfraAzureEnvironment|Opcional|Selecione o ambiente do Azure com a qual pretende registar esta implementação do Azure Stack. As opções incluem pública Azure, Azure - China, Azure - administração pública dos EUA.|
|DNSForwarder|Opcional|Um servidor DNS é criado como parte da implementação do Azure Stack. Para permitir que os computadores dentro da solução para resolver nomes fora o carimbo de data /, fornece o servidor DNS de infraestrutura existente. O servidor DNS no carimbo reencaminha os pedidos de resolução de nome desconhecido para este servidor.|
|Executar novamente|Opcional|Utilize este sinalizador para executar novamente a implementação. Todas as entradas anteriores é utilizada. Volte a introduzir dados fornecidos anteriormente não são suportados porque vários valores exclusivos são gerados e utilizados para a implementação.|


## <a name="perform-post-deployment-configurations"></a>Efetuar configurações de pós-implementação
Depois de instalar o ASDK, existem algumas verificações de pós-instalação recomendadas e alterações de configuração que devem ser feitas. Pode validar a instalação foi instalado com êxito com o cmdlet de teste AzureStack e instalar as ferramentas do Azure Stack do PowerShell e do GitHub. 

Também deverá repor a política de expiração de palavra-passe para se certificar de que a palavra-passe para o anfitrião do kit de desenvolvimento não expira antes de terminar o período de avaliação.

> [!NOTE]
> Opcionalmente, também pode configurar [definições de telemetria do Azure Stack](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *depois* o ASDK a instalar.

**[Publicar tarefas de implementação de ASDK](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registe-se com o Azure
Tem de registar Azure Stack com o Azure para que possa [transferir itens do marketplace do Azure](asdk-marketplace-item.md) ao Azure Stack.

**[Registar o Azure Stack com o Azure](asdk-register.md)**

## <a name="next-steps"></a>Passos Seguintes
Parabéns! Depois de concluir estes passos, terá um ambiente do kit de desenvolvimento com o [administrador](https://adminportal.local.azurestack.external) e [utilizador](https://portal.local.azurestack.external) portais. 

[Pós-instalação de ASDK tarefas de configuração](asdk-post-deploy.md)

