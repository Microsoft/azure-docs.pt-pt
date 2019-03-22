---
title: Utilizar bases de dados SQL no Azure Stack | Documentos da Microsoft
description: Saiba como pode implementar bases de dados SQL como um serviço no Azure Stack e os passos rápidos para implementar o adaptador de fornecedor de recursos do SQL Server.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.lastreviewed: 03/18/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.openlocfilehash: a2f1321e5c6774c585353b9bd7602ecc1ccb8c5e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58177505"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Implementar o fornecedor de recursos do SQL Server no Azure Stack

Utilize o fornecedor de recursos do SQL Server do Azure Stack para expor bases de dados SQL como um serviço do Azure Stack. O fornecedor de recursos do SQL é executado como um serviço numa máquina virtual (VM) do Windows Server 2016 Server Core.

> [!IMPORTANT]
> O fornecedor de recursos é suportado para criar itens nos servidores desse host SQL ou MySQL. Itens criados num servidor de anfitrião que não são criados pelo fornecedor de recursos podem resultar num estado sem correspondência.

## <a name="prerequisites"></a>Pré-requisitos

Existem vários pré-requisitos que têm de ser cumpridos antes de poder implementar o fornecedor de recursos do SQL do Azure Stack. Para cumprir estes requisitos, conclua os passos seguintes num computador que pode aceder a VM de ponto de final com privilégios:

- Se ainda não fez isso, [registar o Azure Stack](azure-stack-registration.md) com o Azure para que possa transferir itens do Azure marketplace.
- Tem de instalar os módulos do Azure e o Azure Stack do PowerShell no sistema onde irá executar esta instalação. Esse sistema tem de ser uma imagem do Windows 10 ou Windows Server 2016 com a versão mais recente do runtime do .NET. Ver [instalar o PowerShell para o Azure Stack](./azure-stack-powershell-install.md).
- Adicionar o principal de Windows Server VM necessário para o mercado do Azure Stack baixando o **Windows Server 2016 Datacenter - Server Core** imagem.
- Transferir o fornecedor de recursos do SQL, binário e, em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário. O fornecedor de recursos tem um mínimo correspondente do Azure Stack criar.

  |Versão mínima do Azure Stack|Versão de RP de SQL|
  |-----|-----|
  |Versão 1808 (1.1808.0.97)|[SQL RP versão 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
  |Versão 1808 (1.1808.0.97)|[SQL RP versão 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
  |Versão 1804 (1.0.180513.1)|[SQL RP versão 1.1.24.0](https://aka.ms/azurestacksqlrp11240)
  |     |     |

- Certifique-se de que os pré-requisitos de integração do Centro de dados são cumpridos:

    |Pré-requisito|Referência|
    |-----|-----|
    |Reencaminhamento condicional de DNS está definido corretamente.|[Integração de datacenter do Azure Stack - DNS](azure-stack-integrate-dns.md)|
    |Portas de entrada para fornecedores de recursos estão abertas.|[Azure Stack integração no datacenter - publicar pontos de extremidade](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Requerente do certificado PKI e SAN está definida corretamente.|[O Azure Stack implementação PKI pré-requisitos obrigatórios](azure-stack-pki-certs.md#mandatory-certificates)[pré-requisitos de certificado de PaaS de implementação do Azure Stack](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Certificados

_Para instalações de sistemas integrados apenas_. Tem de fornecer o certificado PKI de PaaS de SQL descrito na secção de certificados de PaaS opcional das [requisitos de PKI de implementação do Azure Stack](./azure-stack-pki-certs.md#optional-paas-certificates). Coloque o ficheiro. pfx na localização especificada pela **DependencyFilesLocalPath** parâmetro. Não é fornece um certificado para sistemas ASDK.

## <a name="deploy-the-sql-resource-provider"></a>Implementar o fornecedor de recursos do SQL

Depois de instalar todos os pré-requisitos, pode executar o **DeploySqlProvider.ps1** script para implementar o fornecedor de recursos do SQL. O script de DeploySqlProvider.ps1 é extraído como parte do binário do fornecedor de recursos SQL que transferiu para a sua versão do Azure Stack.

 > [!IMPORTANT]
 > Antes de implementar o fornecedor de recursos, reveja as notas de versão para saber mais sobre novas funcionalidades, correções e os problemas conhecidos que podem afetar a sua implementação.
 
Para implementar o fornecedor de recursos do SQL, abra um **novo** elevados janela do PowerShell (ISE do PowerShell não) e para o diretório onde extraiu arquivos binários de fornecedor de recursos do SQL. Recomendamos que utilize uma nova janela do PowerShell para evitar possíveis problemas causados por módulos do PowerShell que já são carregados.

Execute o script de DeploySqlProvider.ps1, que conclui as seguintes tarefas:

- Carrega os certificados e outros artefatos de para uma conta de armazenamento no Azure Stack.
- Publica pacotes de galeria para que possa implementar bases de dados SQL utilizando a galeria.
- Publica um pacote de galeria para implantação de servidores de alojamento.
- Implementa uma VM com a imagem de núcleo do Windows Server 2016 que transferiu e, em seguida, instala o fornecedor de recursos do SQL.
- Registra um registo DNS local que mapeia para o seu fornecedor de recursos de VM.
- Regista o fornecedor de recursos com o local do Azure Resource Manager para a conta de operador.

> [!NOTE]
> Quando a implementação de fornecedor de recursos do SQL é iniciado, o **system.local.sqladapter** é criado o grupo de recursos. Pode demorar até 75 minutos a concluir as implementações necessárias para este grupo de recursos.

### <a name="deploysqlproviderps1-parameters"></a>Parâmetros de DeploySqlProvider.ps1

Pode especificar os seguintes parâmetros da linha de comando. Se não o fizer, ou se nenhuma validação de parâmetro falhar, lhe for pedido para fornecer os parâmetros necessários.

| Nome do parâmetro | Descrição | Comentário ou o valor predefinido |
| --- | --- | --- |
| **CloudAdminCredential** | A credencial para o administrador da nuvem, necessário para acessar o ponto final com privilégios. | _Necessário_ |
| **AzCredential** | As credenciais para a conta de administrador de serviço do Azure Stack. Utilize as mesmas credenciais que utilizou para a implementação do Azure Stack. | _Necessário_ |
| **VMLocalCredential** | As credenciais para a conta de administrador local do fornecedor de recursos SQL VM. | _Necessário_ |
| **PrivilegedEndpoint** | O endereço IP ou nome DNS do ponto final com privilégios. |  _Necessário_ |
| **AzureEnvironment** | O ambiente do Azure da conta de administrador de serviço utilizada para implementar o Azure Stack. Apenas necessário para implementações do Azure AD. Os nomes de ambiente com suporte são **AzureCloud**, **AzureUSGovernment**, ou se um China Azure Active Directory, a utilizar **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Para apenas sistemas integrados, o ficheiro. pfx de certificado deve ser colocado neste diretório. Opcionalmente, pode copiar um pacote de Windows Update MSU aqui. | _Opcional_ (_obrigatório_ para os sistemas integrados) |
| **DefaultSSLCertificatePassword** | A palavra-passe para o certificado. pfx. | _Necessário_ |
| **MaxRetryCount** | O número de vezes que pretende repetir a cada operação, se ocorrer uma falha.| 2 |
| **RetryDuration** | O intervalo de tempo limite entre repetições, em segundos. | 120 |
| **Desinstalar** | Remove o fornecedor de recursos e todos os recursos associados (veja as seguintes notas). | Não |
| **DebugMode** | Impede que a limpeza automática em caso de falha. | Não |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Implementar o fornecedor de recursos do SQL usando um script personalizado

Para eliminar qualquer configuração manual quando implementar o fornecedor de recursos, pode personalizar o script a seguir.  

Altere as informações de conta padrão e palavras-passe, conforme necessário para a sua implementação do Azure Stack.


```powershell
# Install the Azure and Azure Stack PowerShell modules as described in the prerequisites section above before running these commands.

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Clear the existing login information from the Azure PowerShell context.
Clear-AzureRMContext -Scope CurrentUser -Force
Clear-AzureRMContext -Scope Process -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Quando concluir o script de instalação do fornecedor de recursos, atualize o browser para se certificar de que pode ver as atualizações mais recentes.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verificar a implementação com o portal do Azure Stack

Pode utilizar os seguintes passos Certifique-se de que o fornecedor de recursos do SQL é implementado com êxito.

1. Inicie sessão no portal de administração como o administrador de serviços.
2. Selecione **grupos de recursos**.
3. Selecione o **system.\< localização\>.sqladapter** grupo de recursos.
4. Na página de resumo para o grupo de recursos de visão geral, não deve haver nenhum implementações falhadas.
      ![Verificar a implementação do fornecedor de recursos do SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)
5. Por fim, selecione **máquinas virtuais** no portal de administração para verificar que o fornecedor de recursos do SQL VM foi com êxito criada e está em execução.

## <a name="next-steps"></a>Passos Seguintes

[Adicionar servidores de alojamento](azure-stack-sql-resource-provider-hosting-servers.md)
