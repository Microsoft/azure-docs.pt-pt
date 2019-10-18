---
title: 'Azure AD Connect: configurar permissões de conta do conector de AD DS | Microsoft Docs'
description: Este documento detalha como configurar a conta do conector de AD DS com o novo módulo ADSyncConfig do PowerShell
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeb80c3a94e63a886e4a16c0b8fa445b2a8a34e4
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515812"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: configurar permissões de conta do conector de AD DS 

O módulo do PowerShell chamado [ADSyncConfig. psm1](reference-connect-adsyncconfig.md) foi introduzido com Build 1.1.880.0 (lançado em agosto de 2018) que inclui uma coleção de cmdlets para ajudá-lo a configurar as permissões de Active Directory corretas para sua implantação de Azure ad Connect. 

## <a name="overview"></a>Visão geral 
Os cmdlets do PowerShell a seguir podem ser usados para configurar Active Directory permissões da conta do conector de AD DS, para cada recurso que você selecionar para habilitar no Azure AD Connect. Para evitar problemas, você deve preparar Active Directory permissões com antecedência sempre que desejar instalar Azure AD Connect usando uma conta de domínio personalizada para se conectar à sua floresta. Esse módulo ADSyncConfig também pode ser usado para configurar permissões depois que Azure AD Connect for implantado.

![Visão geral da conta do AD DS](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Para Azure AD Connect instalação expressa, uma conta gerada automaticamente (MSOL_nnnnnnnnnn) é criada em Active Directory com todas as permissões necessárias, portanto, não é necessário usar esse módulo ADSyncConfig, a menos que você tenha permissões bloqueadas herança em unidades organizacionais ou em objetos Active Directory específicos que você deseja sincronizar com o Azure AD. 
 
### <a name="permissions-summary"></a>Resumo das permissões 
A tabela a seguir fornece um resumo das permissões necessárias nos objetos do AD: 

| Funcionalidade | Permissões |
| --- | --- |
| recurso ms-DS-ConsistencyGuid |Permissões de leitura e gravação para o atributo ms-DS-ConsistencyGuid documentado em [conceitos de design – usando MS-DS-ConsistencyGuid como sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Sincronização de hash de senha |<li>Replicar alterações de diretório</li>  <li>Replicar todas as alterações de diretório |
| Implantação híbrida do Exchange |Permissões de leitura e gravação para os atributos documentados no [write-back híbrido do Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) para usuários, grupos e contatos. |
| Pasta pública de email do Exchange |Permissões de leitura para os atributos documentados na [pasta pública do Exchange mail](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) para pastas públicas. | 
| Repetição de escrita de palavras-passe |Permissões de leitura e gravação para os atributos documentados em [introdução ao gerenciamento de senhas](../authentication/howto-sspr-writeback.md) para usuários. |
| Repetição de escrita do dispositivo |Permissões de leitura e gravação para objetos de dispositivo e contêineres documentados no [write-back do dispositivo](how-to-connect-device-writeback.md). |
| Repetição de escrita do grupo |Ler, criar, atualizar e excluir objetos de grupo para **grupos do Office 365**sincronizados.  Para obter mais informações, consulte [Group write-back](how-to-connect-preview.md#group-writeback).|

## <a name="using-the-adsyncconfig-powershell-module"></a>Usando o módulo do PowerShell do ADSyncConfig 
O módulo ADSyncConfig requer o [ferramentas de administração de servidor remoto (RSAT) para AD DS](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) já que depende do módulo e das ferramentas do AD DS PowerShell. Para instalar o RSAT para AD DS, abra uma janela do Windows PowerShell com ' Executar como administrador ' e execute: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Configurar](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Você também pode copiar o arquivo **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** para um controlador de domínio que já tem o RSAT para AD DS instalado e usar esse módulo do PowerShell a partir daí.

Para começar a usar o ADSyncConfig, você precisa carregar o módulo em uma janela do Windows PowerShell: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Para verificar todos os cmdlets incluídos neste módulo, você pode digitar:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Marcar](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Cada cmdlet tem os mesmos parâmetros para inserir a conta do conector de AD DS e uma opção AdminSDHolder. Para especificar sua conta do conector de AD DS, você pode fornecer o nome da conta e o domínio, ou apenas o DN (nome distinto da conta),

por exemplo:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Or

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Certifique-se de substituir `<ADAccountName>`, `<ADDomainName>` e `<ADAccountDN>` pelos valores adequados para o seu ambiente.

Caso você não queira modificar permissões no contêiner AdminSDHolder, use a opção `-SkipAdminSdHolders`. 

Por padrão, todos os cmdlets Set Permissions tentarão definir AD DS permissões na raiz de cada domínio na floresta, o que significa que o usuário que está executando a sessão do PowerShell requer direitos de administrador de domínio em cada domínio na floresta.  Devido a esse requisito, é recomendável usar um administrador corporativo da raiz da floresta. Se sua implantação de Azure AD Connect tiver vários conectores de AD DS, será necessário executar o mesmo cmdlet em cada floresta que tenha um conector de AD DS. 

Você também pode definir permissões em um objeto UO ou AD DS específico usando o parâmetro `-ADobjectDN` seguido pelo DN do objeto de destino onde você deseja definir permissões. Ao usar um ADobjectDN de destino, o cmdlet definirá permissões somente nesse objeto e não na raiz do domínio ou no contêiner AdminSDHolder. Esse parâmetro pode ser útil quando você tem determinadas UOs ou AD DS objetos que têm herança de permissão desabilitada (consulte localizar objetos AD DS com herança de permissão desabilitada) 

As exceções a esses parâmetros comuns são o cmdlet `Set-ADSyncRestrictedPermissions`, que é usado para definir as permissões na própria conta do conector de AD DS e o cmdlet `Set-ADSyncPasswordHashSyncPermissions`, pois as permissões necessárias para a sincronização de hash de senha são definidas apenas na raiz do domínio , portanto, esse cmdlet não inclui os parâmetros `-ObjectDN` ou `-SkipAdminSdHolders`.

### <a name="determine-your-ad-ds-connector-account"></a>Determinar sua conta do conector de AD DS 
Caso Azure AD Connect já esteja instalado e você queira verificar qual é a conta do conector de AD DS atualmente em uso pelo Azure AD Connect, você pode executar o cmdlet: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Localizar objetos de AD DS com herança de permissão desabilitada 
Caso você queira verificar se há algum objeto de AD DS com a herança de permissão desabilitada, você pode executar: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Por padrão, esse cmdlet só procurará UOs com herança desabilitada, mas você pode especificar outras classes de objeto AD DS no parâmetro `-ObjectClass` ou usar ' * ' para todas as classes de objeto, da seguinte maneira: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Exibir AD DS permissões de um objeto 
Você pode usar o cmdlet abaixo para exibir a lista de permissões definidas atualmente em um objeto Active Directory fornecendo seu DistinguishedName: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Configurar permissões de conta do conector de AD DS 
 
### <a name="configure-basic-read-only-permissions"></a>Configurar permissões básicas somente leitura 
Para definir permissões básicas somente leitura para a conta do conector de AD DS quando não estiver usando qualquer recurso de Azure AD Connect, execute: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Or 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Esse cmdlet definirá as seguintes permissões: 
 

|Tipo |Nome |Access |Aplica-se a| 
|-----|-----|-----|-----|
|Permitir |Conta do conector de AD DS |Ler todas as propriedades |Objetos de dispositivo descendentes| 
|Permitir |Conta do conector de AD DS|Ler todas as propriedades |Objetos InetOrgPerson descendentes| 
|Permitir |Conta do conector de AD DS |Ler todas as propriedades |Objetos de computador descendentes| 
|Permitir |Conta do conector de AD DS |Ler todas as propriedades |Objetos foreignSecurityPrincipal descendentes| 
|Permitir |Conta do conector de AD DS |Ler todas as propriedades |Objetos de grupo descendentes| 
|Permitir |Conta do conector de AD DS |Ler todas as propriedades |Objetos de usuário descendentes| 
|Permitir |Conta do conector de AD DS |Ler todas as propriedades |Objetos de contato descendentes| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Configurar as permissões do MS-DS-Consistency-GUID 
Para definir permissões para a conta do conector de AD DS ao usar o atributo ms-DS-Consistency-GUID como a âncora de origem (também conhecido como "permitir que o Azure gerencie a fonte âncora para mim"), execute: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

Or 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Esse cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Access |Aplica-se a|
|-----|-----|-----|-----| 
|Permitir|Conta do conector de AD DS|Propriedade de leitura/gravação|Objetos de usuário descendentes|

### <a name="permissions-for-password-hash-synchronization"></a>Permissões para sincronização de hash de senha 
Para definir permissões para a conta do conector de AD DS ao usar a sincronização de hash de senha, execute: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


Or 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Esse cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Access |Aplica-se a|
|-----|-----|-----|-----| 
|Permitir |Conta do conector de AD DS |Replicando alterações de diretório |Somente este objeto (raiz do domínio)| 
|Permitir |Conta do conector de AD DS |Replicando todas as alterações de diretório |Somente este objeto (raiz do domínio)| 
  
### <a name="permissions-for-password-writeback"></a>Permissões para Write-back de senha 
Para definir permissões para a conta do conector de AD DS ao usar o Write-back de senha, execute: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Or

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Esse cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Access |Aplica-se a|
|-----|-----|-----|-----| 
|Permitir |Conta do conector de AD DS |Repor palavra-passe |Objetos de usuário descendentes| 
|Permitir |Conta do conector de AD DS |Gravar locktime de propriedade |Objetos de usuário descendentes| 
|Permitir |Conta do conector de AD DS |PwdLastSet de propriedade de gravação |Objetos de usuário descendentes| 

### <a name="permissions-for-group-writeback"></a>Permissões para Write-back de grupo 
Para definir permissões para a conta do conector de AD DS ao usar o Write-back de grupo, execute: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
Or 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Esse cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Access |Aplica-se a|
|-----|-----|-----|-----| 
|Permitir |Conta do conector de AD DS |Leitura/gravação genérica |Todos os atributos do grupo de tipos de objeto e subobjetos| 
|Permitir |Conta do conector de AD DS |Criar/excluir objeto filho |Todos os atributos do grupo de tipos de objeto e subobjetos| 
|Permitir |Conta do conector de AD DS |Excluir/excluir objetos de árvore|Todos os atributos do grupo de tipos de objeto e subobjetos|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Permissões para a implantação híbrida do Exchange 
Para definir permissões para a conta do conector de AD DS ao usar a implantação híbrida do Exchange, execute: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Or 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Esse cmdlet definirá as seguintes permissões:  
 

|Tipo |Nome |Access |Aplica-se a|
|-----|-----|-----|-----| 
|Permitir |Conta do conector de AD DS |Ler/gravar todas as propriedades |Objetos de usuário descendentes| 
|Permitir |Conta do conector de AD DS |Ler/gravar todas as propriedades |Objetos InetOrgPerson descendentes| 
|Permitir |Conta do conector de AD DS |Ler/gravar todas as propriedades |Objetos de grupo descendentes| 
|Permitir |Conta do conector de AD DS |Ler/gravar todas as propriedades |Objetos de contato descendentes| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Permissões para pastas públicas do Exchange mail (versão prévia) 
Para definir permissões para a conta do conector de AD DS ao usar o recurso de pastas públicas do Exchange mail, execute: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Or 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Esse cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Access |Aplica-se a|
|-----|-----|-----|-----| 
|Permitir |Conta do conector de AD DS |Ler todas as propriedades |Objetos PublicFolder descendentes| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Restringir permissões na conta do conector de AD DS 
Esse script do PowerShell restringirá as permissões para a conta do AD Connector fornecida como um parâmetro. As permissões de estreitamento envolvem as seguintes etapas: 

- Desabilitar herança no objeto especificado 
- Remova todas as ACEs no objeto específico, exceto ACEs específicas da própria, pois queremos manter as permissões padrão intactas quando se trata de si mesma. 
 
  O parâmetro-ADConnectorAccountDN é a conta do AD cujas permissões precisam ser reforçadas. Normalmente, essa é a conta de domínio MSOL_nnnnnnnnnnnn configurada no conector de AD DS (consulte determinar sua conta de conector de AD DS). O parâmetro-Credential é necessário para especificar a conta de administrador que tem os privilégios necessários para restringir Active Directory permissões no objeto do AD de destino. Normalmente, esse é o administrador corporativo ou de domínio.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Por exemplo: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Esse cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Access |Aplica-se a|
|-----|-----|-----|-----| 
|Permitir |SISTEMA |Controle total |Este objeto 
|Permitir |Administradores corporativos |Controle total |Este objeto 
|Permitir |Administradores de domínio |Controle total |Este objeto 
|Permitir |Administradores |Controle total |Este objeto 
|Permitir |Controladores de domínio da empresa |Listar conteúdo |Este objeto 
|Permitir |Controladores de domínio da empresa |Ler todas as propriedades |Este objeto 
|Permitir |Controladores de domínio da empresa |Permissões de leitura |Este objeto 
|Permitir |Usuários autenticados |Listar conteúdo |Este objeto 
|Permitir |Usuários autenticados |Ler todas as propriedades |Este objeto 
|Permitir |Usuários autenticados |Permissões de leitura |Este objeto 

## <a name="next-steps"></a>Próximos Passos
- [Azure AD Connect: contas e permissões](reference-connect-accounts-permissions.md)
- [Instalação expressa](how-to-connect-install-express.md)
- [Instalação personalizada](how-to-connect-install-custom.md)
- [Referência de ADSyncConfig](reference-connect-adsyncconfig.md)

