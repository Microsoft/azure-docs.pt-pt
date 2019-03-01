---
title: Antes de implementar o serviço de aplicações no Azure Stack | Documentos da Microsoft
description: Passos para concluir antes de implementar o serviço de aplicações no Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 02/22/2019
ms.openlocfilehash: 01b0a86ede79187d8f180df0f2f71f6eaadb7428
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990540"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Antes de começar com o serviço de aplicações no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Antes de implementar o serviço de aplicações do Azure no Azure Stack, tem de concluir os passos de pré-requisitos neste artigo.

> [!IMPORTANT]
> Aplicar a atualização de 1901 seu sistema integrado do Azure Stack ou implementar a mais recente do Azure Stack Development Kit (ASDK) antes de implementar 1.5 de serviço de aplicações do Azure.

## <a name="download-the-installer-and-helper-scripts"></a>Transferir os instalador e do programa auxiliar de scripts

1. Transfira o [serviço de aplicações em scripts de programa auxiliar de implementação do Azure Stack](https://aka.ms/appsvconmashelpers).
2. Transfira o [serviço de aplicações no instalador do Azure Stack](https://aka.ms/appsvconmasinstaller).
3. Extraia os ficheiros do arquivo. zip de scripts do auxiliar. Os seguintes ficheiros e pastas são extraídas:

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Pasta de módulos
     - GraphAPI.psm1

## <a name="syndicate-the-custom-script-extension-from-the-marketplace"></a>Distribua a extensão de Script personalizado do Marketplace

Serviço de aplicações do Azure no Azure Stack requer v1.9.1 de extensão de Script personalizado.  A extensão tem de ser [distribuídos do Marketplace](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) antes de iniciar a implementação ou a atualização do serviço de aplicações do Azure no Azure Stack

## <a name="get-certificates"></a>Obter certificados

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Certificado de raiz do Gestor de recursos do Azure para o Azure Stack

Abra uma sessão elevada do PowerShell num computador que possa alcançar o ponto final com privilégios no sistema integrado do Azure Stack ou no anfitrião de Kit de desenvolvimento do Azure Stack.

Executar o *Get-AzureStackRootCert.ps1* script a partir da pasta onde extraiu os scripts auxiliares. O script cria um certificado de raiz na mesma pasta que o script que precisa do serviço de aplicações para a criação de certificados.

Ao executar o seguinte comando do PowerShell, terá de fornecer o ponto final com privilégios e as credenciais para o AzureStack\CloudAdmin.

```PowerShell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get-AzureStackRootCert.ps1 script parameters

| Parâmetro | Obrigatório ou opcional | Valor predefinido | Descrição |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Necessário | AzS-ERCS01 | Ponto final com privilégios |
| CloudAdminCredential | Necessário | AzureStack\CloudAdmin | Credencial da conta de domínio para os administradores de nuvem do Azure Stack |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Certificados necessários para a implementação de ASDK do serviço de aplicações do Azure

O *AppServiceCerts.ps1 criar* script funciona com a autoridade de certificação do Azure Stack para criar os certificados de quatro que precisa do serviço de aplicações.

| Nome de ficheiro | Utilizar |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Certificado SSL predefinido do Serviço de Aplicações |
| api.appservice.local.azurestack.external.pfx | Certificado SSL de API do serviço de aplicações |
| ftp.appservice.local.azurestack.external.pfx | Certificado SSL de publicador do serviço de aplicações |
| sso.appservice.local.azurestack.external.pfx | Certificado de aplicação de identidade do serviço de aplicações |

Para criar os certificados, siga estes passos:

1. Inicie sessão para o anfitrião de Kit de desenvolvimento do Azure Stack, utilizando a conta de AzureStack\AzureStackAdmin.
2. Abra uma sessão elevada do PowerShell.
3. Executar o *AppServiceCerts.ps1 criar* script a partir da pasta onde extraiu os scripts auxiliares. Este script cria quatro certificados na mesma pasta que o script que precisa do serviço de aplicações para a criação de certificados.
4. Introduza uma palavra-passe para proteger os ficheiros. pfx e tome nota do mesmo. Terá de introduzi-los no serviço de aplicações no programa de instalação do Azure Stack.

#### <a name="create-appservicecertsps1-script-parameters"></a>Parâmetros do script AppServiceCerts.ps1 criar

| Parâmetro | Obrigatório ou opcional | Valor predefinido | Descrição |
| --- | --- | --- | --- |
| pfxPassword | Necessário | Null | Palavra-passe que o ajuda a proteger a chave privada do certificado |
| DomainName | Necessário | local.azurestack.external | Sufixo do Azure de região e o domínio de pilha |

### <a name="certificates-required-for-azure-stack-production-deployment-of-azure-app-service"></a>Certificados necessários para a implementação de produção do Azure Stack do serviço de aplicações do Azure

Para executar o fornecedor de recursos na produção, tem de fornecer os seguintes certificados:

- Certificado de domínio predefinido
- Certificado de API
- Certificado de publicação
- Certificado de identidade

#### <a name="default-domain-certificate"></a>Certificado de domínio predefinido

O certificado de domínio predefinido é colocado na função de Front-End. Aplicações de utilizador para o pedido de domínio com carateres universais ou padrão para o serviço de aplicações do Azure utilizam este certificado. O certificado também é utilizado para operações de controle de origem (Kudu).

O certificado tem de estar no formato. pfx e deve ser um certificado de caráter universal de três assunto. Este requisito permite que um certificado cobrir o domínio predefinido e o ponto de extremidade do SCM para operações de controle de origem.

| Formato | Exemplo |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certificado de API

O certificado de API é colocado na função de gestão. O fornecedor de recursos utiliza-o para o ajudar a proteger chamadas de API. O certificado para a publicação tem de conter um assunto que corresponde a entrada DNS de API.

| Formato | Exemplo |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Certificado de publicação

O certificado para a função de publicador protege o tráfego FTPS para os proprietários dos aplicativos quando eles carregam conteúdo. O certificado para a publicação tem de conter um assunto que corresponde a entrada DNS de FTPS.

| Formato | Exemplo |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certificado de identidade

Permite que o certificado para a aplicação de identidade:

- Integração entre o Azure Active Directory (Azure AD) ou serviços de Federação do Active Directory (AD FS) directory, Azure Stack e o serviço de aplicações para suportar a integração com o fornecedor de recursos de computação.
- Únicos início de sessão em cenários para ferramentas de programação avançadas dentro do serviço de aplicações do Azure no Azure Stack.

O certificado para a identidade tem de conter um assunto que corresponde ao formato seguinte.

| Formato | Exemplo |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |


### <a name="validate-certificates"></a>Validar certificados
Antes de implementar o fornecedor de recursos do serviço de aplicações, deve [validar os certificados para ser utilizado](azure-stack-validate-pki-certs.md#perform-platform-as-a-service-certificate-validation) utilizando a ferramenta do Verificador de preparação do Azure Stack disponível a partir do [galeria do PowerShell](https://aka.ms/AzsReadinessChecker). A ferramenta de verificação da preparação do Azure Stack valida que os certificados PKI gerados são adequados para implementação de serviços de aplicações. 

Como melhor prática, ao trabalhar com todas as informações necessárias [certificados PKI de pilha do Azure](azure-stack-pki-certs.md), deve planear a deixe tempo suficiente para testar e volte a emitir certificados, se necessário. 

## <a name="virtual-network"></a>Rede virtual

> [!NOTE]
> A pré-criação de uma rede virtual personalizada é opcional, como o serviço de aplicações do Azure no Azure Stack pode criar a rede virtual necessária, mas, em seguida, será necessário comunicar com o SQL e o servidor de ficheiros através de endereços IP públicos.

Serviço de aplicações do Azure no Azure Stack permite-lhe implementar o fornecedor de recursos para uma rede virtual existente ou permite-lhe criar uma rede virtual como parte da implementação. Utilizar uma rede virtual existente permite o uso de IPs interno para ligar ao servidor de ficheiros e do SQL server exigidas pelo serviço de aplicações do Azure no Azure Stack. A rede virtual tem de ser configurada com o intervalo de endereços e sub-redes seguintes antes de instalar o serviço de aplicações do Azure no Azure Stack:

Rede virtual - /16

Sub-redes

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="prepare-the-file-server"></a>Preparar o servidor de ficheiros

Serviço de aplicações do Azure requer a utilização de um servidor de ficheiros. Para implementações de produção, o servidor de ficheiros tem de ser configurado para ser elevada disponibilidade e capacidade de processamento de falhas.

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>Modelo de início rápido para o servidor de ficheiros para implementações de serviço de aplicações do Azure no ASDK.

Para apenas para implementações do Azure Stack Development Kit, pode utilizar o [modelo de implementação Azure Resource Manager de exemplo](https://aka.ms/appsvconmasdkfstemplate) para implementar um servidor de ficheiro de nó único configurado. O servidor de ficheiros de nó único vai estar num grupo de trabalho.

### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>Modelo de início rápido para o servidor de ficheiros altamente disponíveis e o SQL Server

R [modelo de início rápido de arquitetura de referência](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha) estão agora disponíveis, que irá implementar o servidor de ficheiros, SQL Server, Active Directory de suporte infraestrutura numa rede Virtual configurado para suportar uma implementação de elevada disponibilidade do Serviço de aplicações do Azure no Azure Stack.  

### <a name="steps-to-deploy-a-custom-file-server"></a>Passos para implementar um servidor de ficheiros personalizada

>[!IMPORTANT]
> Se optar por implementar o serviço de aplicações numa rede Virtual existente, o servidor de ficheiros devem ser implementado numa sub-rede separada do serviço de aplicações.

#### <a name="provision-groups-and-accounts-in-active-directory"></a>Aprovisionar grupos e contas no Active Directory

1. Crie os seguintes grupos de segurança global do Active Directory:

   - FileShareOwners
   - FileShareUsers

2. Crie as seguintes contas do Active Directory como contas de serviço:

   - FileShareOwner
   - FileShareUser

   Como uma segurança melhor prática, os utilizadores para estas contas (e para todas as funções da web) deve ser exclusivo e ter forte nomes de utilizador e palavras-passe. Defina as palavras-passe com as seguintes condições:

   - Ativar **palavra-passe nunca expira**.
   - Ativar **o utilizador não é possível alterar a palavra-passe**.
   - Desativar **o utilizador deve alterar a palavra-passe no próximo início de sessão**.

3. Adicione as contas para as associações de grupo da seguinte forma:

   - Adicione **FileShareOwner** para o **FileShareOwners** grupo.
   - Adicione **FileShareUser** para o **FileShareUsers** grupo.

#### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Aprovisionar grupos e contas num grupo de trabalho

>[!NOTE]
> Quando estiver a configurar um servidor de ficheiros, execute todos os seguintes comandos de um **linha de comandos de administrador**. <br>***Não utilize o PowerShell.***

Quando utiliza o modelo Azure Resource Manager, os utilizadores já são criados.

1. Execute os seguintes comandos para criar as contas de FileShareOwner e FileShareUser. Substitua `<password>` pelos seus próprios valores.

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. Defina as palavras-passe para as contas para nunca expirar, executando os comandos WMIC seguintes:

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. Crie os grupos locais FileShareUsers e FileShareOwners e adicionar as contas no primeiro passo para eles:

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

#### <a name="provision-the-content-share"></a>Aprovisionar a partilha de conteúdo

A partilha de conteúdo contém conteúdo do Web site de inquilino. O procedimento para aprovisionar a partilha de conteúdo num único servidor de arquivos é o mesmo para ambientes do Active Directory e o grupo de trabalho. Mas é diferente para um cluster de ativação pós-falha no Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Aprovisionar a partilha de conteúdo no servidor de ficheiros único (Active Directory ou grupo de trabalho)

Num único servidor de arquivos, execute os seguintes comandos numa linha de comandos elevada. Substitua o valor para `C:\WebSites` com os caminhos correspondentes no seu ambiente.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>Configurar o controlo de acesso para as partilhas

Execute os seguintes comandos numa linha de comandos elevada no servidor de ficheiros ou no nó de cluster de ativação pós-falha, que é o proprietário do recurso de cluster atual. Substitua os valores em itálico pelos valores que são específicas para o seu ambiente.

#### <a name="active-directory"></a>Active Directory

```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Grupo de trabalho

```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server-instance"></a>Preparar a instância do SQL Server

Para o serviço de aplicações do Azure no Azure Stack de alojamento e de bases de dados de medição, tem de preparar uma instância do SQL Server para manter as bases de dados do serviço de aplicações.

Para implementações do Azure Stack Development Kit, pode usar o SQL Server Express 2014 SP2 ou posterior.

Para fins de elevada disponibilidade e de produção, deve utilizar uma versão completa do SQL Server 2014 SP2 ou posterior, ativar a autenticação de modo misto e implemente num [configuração de elevada disponibilidade](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Instância do SQL Server para o serviço de aplicações do Azure no Azure Stack tem de ser acessível a partir de todas as funções do serviço de aplicações. Pode implantar o SQL Server dentro da subscrição do fornecedor predefinido no Azure Stack. Pode ainda utilizar a infraestrutura existente na sua organização (desde que existe conectividade para o Azure Stack). Se estiver a utilizar uma imagem do Azure Marketplace, lembre-se configurar a firewall em conformidade.

>[!NOTE]
> Um número de imagens de máquinas virtuais de IaaS do SQL está disponível através da funcionalidade de gestão do Marketplace. Certifique-se de que sempre transferir a versão mais recente da extensão de IaaS do SQL antes de implementar uma VM com um item do mercado. As imagens do SQL são as mesmas que as VMs de SQL que estão disponíveis no Azure. Para VMs de SQL criado a partir destas imagens, a extensão de IaaS e correspondente aprimoramentos portais oferecem funcionalidades como a aplicação de patches automática e capacidades de cópia de segurança.
>
Para qualquer uma das funções do SQL Server, pode utilizar uma instância predefinida ou numa instância nomeada. Se utilizar uma instância nomeada, certifique-se de que iniciar o serviço do SQL Server Browser e abrir a porta 1434 manualmente.

O instalador do serviço de aplicações irá verificar para garantir que o SQL Server tem de contenção da base de dados ativada. Para ativar a contenção da base de dados no SQL Server que alojará as bases de dados do serviço de aplicações, execute estes comandos SQL:

```sql
sp_configure 'contained database authentication', 1;  
GO  
RECONFIGURE;  
GO
```


>[!IMPORTANT]
> Se optar por implementar o serviço de aplicações numa rede Virtual existente, o SQL Server devem ser implementado numa sub-rede separada do serviço de aplicações e o servidor de ficheiros.
>

## <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação do Azure Active Directory

Configure um principal de serviço do Azure AD para suportar as seguintes operações:

- Integração do conjunto de dimensionamento de máquina virtual em escalões de worker.
- SSO para as ferramentas de programação avançadas e portal de funções do Azure.

Estes passos aplicam-se aos ambientes do Azure Stack Azure protegida por AD apenas.

Os administradores tem de configurar SSO para:

- Ative as ferramentas de programação avançadas no serviço de aplicações (Kudu).
- Ative a utilização da experiência do portal de funções do Azure.

Siga estes passos.

1. Abra uma instância do PowerShell como azurestack\AzureStackAdmin.
2. Vá para a localização dos scripts que transferiu e extraiu no [passo dos pré-requisitos](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started).
3. [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md).
4. Executar o **AADIdentityApp.ps1 criar** script. Quando lhe for pedido, introduza o ID de inquilino do Azure AD que está a utilizar para a sua implementação do Azure Stack. Por exemplo, introduza **myazurestack.onmicrosoft.com**.
5. Na **credencial** janela, introduza a sua conta de administrador de serviço do Azure AD e a palavra-passe. Selecione **OK**.
6. Introduza o caminho do ficheiro de certificado e a palavra-passe de certificado para o [certificado criado anteriormente](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started). O certificado que criou para este passo por predefinição é **sso.appservice.local.azurestack.external.pfx**.
7. O script cria um novo aplicativo na instância do inquilino do Azure AD. Tome nota do ID da aplicação que é devolvido no resultado do PowerShell. Precisa estas informações durante a instalação.
8. Abra uma nova janela do browser e iniciar sessão para o [portal do Azure](https://portal.azure.com) como administrador de serviço de Azure Active Directory.
9. Abra o fornecedor de recursos do Azure AD.
10. Selecione **registos das aplicações**.
11. Procure o ID da aplicação retornado como parte do passo 7. Uma aplicação de serviço de aplicações está listada.
12. Selecione **aplicativo** na lista.
13. Selecione **definições**.
14. Selecione **permissões obrigatórias** > **conceder permissões** > **Sim**.

```PowerShell
    Create-AADIdentityApp.ps1
```

| Parâmetro | Obrigatório ou opcional | Valor predefinido | Descrição |
| --- | --- | --- | --- |
| DirectoryTenantName | Necessário | Null | ID de inquilino do Azure AD Forneça o GUID ou uma cadeia. Um exemplo é myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Necessário | Null | Ponto final de administração do Azure Resource Manager. Um exemplo é adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Necessário | Null | Ponto de extremidade do inquilino do Azure Resource Manager. Um exemplo é management.local.azurestack.external. |
| AzureStackAdminCredential | Necessário | Null | Credencial de administrador do serviço de AD do Azure. |
| CertificateFilePath | Necessário | Null | **Caminho completo do** para o ficheiro de certificado de aplicação de identidade gerado anteriormente. |
| CertificatePassword | Necessário | Null | Palavra-passe que o ajuda a proteger a chave privada do certificado. |
| Ambiente | Opcional | AzureCloud | O nome do ambiente de nuvem suportada em que o destino do Azure Active Directory Graph Service está disponível.  Valores permitidos: 'AzureCloud', 'AzureChinaCloud', 'AzureUSGovernment', 'AzureGermanCloud'.|

## <a name="create-an-active-directory-federation-services-application"></a>Criar uma aplicação de serviços de Federação do Active Directory

Para ambientes do Azure Stack protegidos pelo AD FS, tem de configurar um principal de serviço do AD FS para suportar as seguintes operações:

- Integração do conjunto de dimensionamento de máquina virtual em escalões de worker.
- SSO para as ferramentas de programação avançadas e portal de funções do Azure.

Os administradores tem de configurar SSO para:

- Configure um principal de serviço para a integração de conjunto de dimensionamento de máquina virtual em escalões de worker.
- Ative as ferramentas de programação avançadas no serviço de aplicações (Kudu).
- Ative a utilização da experiência do portal de funções do Azure.

Siga estes passos.

1. Abra uma instância do PowerShell como azurestack\AzureStackAdmin.
2. Vá para a localização dos scripts que transferiu e extraiu no [passo dos pré-requisitos](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started).
3. [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md).
4. Executar o **ADFSIdentityApp.ps1 criar** script.
5. Na **credencial** janela, introduza a sua conta de administrador do AD FS na cloud e a palavra-passe. Selecione **OK**.
6. Forneça o caminho do ficheiro de certificado e a palavra-passe de certificado para o [certificado criado anteriormente](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started). O certificado que criou para este passo por predefinição é **sso.appservice.local.azurestack.external.pfx**.

```PowerShell
    Create-ADFSIdentityApp.ps1
```

| Parâmetro | Obrigatório ou opcional | Valor predefinido | Descrição |
| --- | --- | --- | --- |
| AdminArmEndpoint | Necessário | Null | Ponto final de administração do Azure Resource Manager. Um exemplo é adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Necessário | Null | Ponto final com privilégios. Um exemplo é AzS-ERCS01. |
| CloudAdminCredential | Necessário | Null | Credencial de conta de domínio para os administradores de nuvem do Azure Stack. Um exemplo é Azurestack\CloudAdmin. |
| CertificateFilePath | Necessário | Null | **Caminho completo do** ao ficheiro PFX de certificado da aplicação de identidade. |
| CertificatePassword | Necessário | Null | Palavra-passe que o ajuda a proteger a chave privada do certificado. |

## <a name="next-steps"></a>Passos Seguintes

[Instalar o fornecedor de recursos do serviço de aplicações](azure-stack-app-service-deploy.md)
