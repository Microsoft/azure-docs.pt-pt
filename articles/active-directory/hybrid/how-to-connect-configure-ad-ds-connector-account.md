---
title: 'Azure AD Connect: Configurar as permissões de conta do AD DS conector | Documentos da Microsoft'
description: Este documento fornece detalhes sobre como configurar a conta do conector do AD DS com o novo módulo do PowerShell de ADSyncConfig
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6510105af8c019b1aca5333f516a10667edaadb5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58000861"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: Configurar as permissões de conta do AD DS conector 

O módulo do PowerShell com o nome [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) foi introduzida com compilação 1.1.880.0 (lançado em Agosto de 2018), que inclui um conjunto de cmdlets para o ajudar a configurar as permissões corretas do Active Directory para o Azure AD Ligue-se a implementação. 

## <a name="overview"></a>Descrição geral 
Os seguintes cmdlets PowerShell pode ser utilizado para configurar permissões do Active Directory da conta do conector do AD DS, para cada funcionalidade que optar por ativar no Azure AD Connect. Para evitar quaisquer problemas, deve preparar permissões do Active Directory com antecedência sempre que deseja instalar o Azure AD Connect com uma conta de domínio personalizado para ligar à sua floresta. Este módulo de ADSyncConfig também pode ser utilizado para configurar permissões após a implementação do Azure AD Connect.

![](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Para a instalação do Azure AD Connect Express, uma conta gerada automaticamente (MSOL_nnnnnnnnnn) é criada no Active Directory com todas as permissões necessárias, para que não é necessário utilizar este módulo ADSyncConfig, exceto se o ter bloqueado permissões herança em unidades organizacionais ou em objetos específicos do Active Directory que pretende sincronizar com o Azure AD. 
 
### <a name="permissions-summary"></a>Resumo das permissões 
A tabela seguinte fornece um resumo das permissões necessárias em objetos do AD: 

| Funcionalidade | Permissões |
| --- | --- |
| funcionalidade do MS-DS-ConsistencyGuid |Permissões de escrita para o atributo ms-DS-ConsistencyGuid documentado em [conceitos de Design - usando o ms-DS-ConsistencyGuid como sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Sincronização de hash de palavra-passe |<li>Replicar as alterações de diretório</li>  <li>Diretório de replicar muda tudo |
| Implementação híbrida do Exchange |Permissões de escrita para os atributos documentados em [repetição de escrita do Exchange híbrido](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) para utilizadores, grupos e contactos. |
| Pasta de públicas de correio do Exchange |Permissões de leitura para os atributos documentados em [pastas públicas de correio Exchange](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) para as pastas públicas. | 
| Repetição de escrita de palavras-passe |Permissões de escrita para os atributos documentados em [introdução à gestão de palavra-passe](../authentication/howto-sspr-writeback.md) para os utilizadores. |
| Repetição de escrita do dispositivo |Permissões de escrita para objetos de dispositivo e os contentores documentados em [repetição de escrita do dispositivo](how-to-connect-device-writeback.md). |
| Repetição de escrita do grupo |Ler, criar, atualizar e eliminar grupo objetos para sincronizados **grupos do Office 365**.  Para obter mais informações, consulte [repetição de escrita do grupo](how-to-connect-preview.md#group-writeback).|

## <a name="using-the-adsyncconfig-powershell-module"></a>Com o módulo ADSyncConfig PowerShell 
O módulo de ADSyncConfig requer o [remoto administração ferramentas servidor (FARS) para o AD DS](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) , uma vez que ele depende do módulo do PowerShell do AD DS e ferramentas. Para instalar o FARS para AD DS, abra uma janela do Windows PowerShell com "Executar como administrador" e execute: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Configurar](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Também pode copiar o ficheiro **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** para um controlador de domínio que já tem as FARS para AD DS instalada e utilizar este módulo do PowerShell a partir daí.

Para começar a utilizar o ADSyncConfig terá de carregar o módulo numa janela do Windows PowerShell: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Para verificar todos os cmdlets incluídos neste módulo, que pode digitar:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Marcar](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Cada cmdlet tem os mesmos parâmetros de entrada a conta de conector do AD DS e um comutador de AdminSDHolder. Para especificar a sua conta de conector do AD DS, pode fornecer o nome da conta e domínio ou apenas a conta DN (nome distinto),

p. ex.: 

`Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName ADaccount -ADConnectorAccountDomain Contoso`

Ou; 

`Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADaccount,OU=AADconnect,DC=Contoso,DC=com'`

No caso de não pretender modificar as permissões no contêiner AdminSDHolder, use a opção `-SkipAdminSdHolders`. 

Por predefinição, todos os cmdlets de permissões do conjunto tentará definir permissões do AD DS na raiz de cada domínio na floresta, que significa que o utilizador a executar a sessão do PowerShell requer direitos de administrador de domínio em cada domínio na floresta.  Devido a esse requisito, é recomendado utilizar um administrador empresarial de raiz da floresta. Se a sua implementação do Azure AD Connect tem múltiplos conectores do AD DS, será necessário para executar o mesmo cmdlet em cada floresta que possua um conector do AD DS. 

Também pode definir permissões num objeto específico da UO ou o AD DS utilizando o parâmetro `-ADobjectDN` seguido o DN do objeto de destino em que pretende definir permissões. Quando utilizar um destino ADobjectDN, o cmdlet irá permissões u tohoto objektu nastavit apenas e não na raiz do domínio ou contêiner AdminSDHolder. Este parâmetro pode ser útil quando tem determinadas UOs ou objetos de AD DS que tenha a herança de permissões desativado (Consulte localizar AD DS objetos com a herança de permissão desativada) 

Exceções para estes parâmetros comuns são o `Set-ADSyncRestrictedPermissions` cmdlet, que é usado para definir as permissões da conta de conector do AD DS em si, e o `Set-ADSyncPasswordHashSyncPermissions` cmdlet, uma vez que as permissões necessárias para a sincronização de Hash de palavra-passe apenas estão definidas na raiz do domínio, por isso Este cmdlet não inclui a `-ObjectDN` ou `-SkipAdminSdHolders` parâmetros.

### <a name="determine-your-ad-ds-connector-account"></a>Determinar o conector do AD DS conta 
No caso do Azure AD Connect já está instalado e pretende verificar o que é a conta de conector do AD DS atualmente em uso pelo Azure AD Connect, pode executar o cmdlet: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Localizar objetos de AD DS com a herança de permissão desativada 
Caso queira verificar que se há qualquer objeto do AD DS com a herança de permissão desativada, pode executar: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Por predefinição, este cmdlet irá apenas procurar UOs com a herança desativada, mas pode especificar outras classes de objeto do AD DS no `-ObjectClass` parâmetro ou utilize "*" para todas as classes, de objeto da seguinte forma: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Ver permissões de AD DS de um objeto 
Pode utilizar o cmdlet abaixo para ver a lista de permissões atualmente definido num objeto do Active Directory, fornecendo o DistinguishedName: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Configurar as permissões de conta do AD DS conector 
 
### <a name="configure-basic-read-only-permissions"></a>Configurar permissões só de leitura básicas 
Para definir permissões só de leitura básicas para a conta do conector do AD DS quando não utilizar qualquer funcionalidade do Azure AD Connect, execute: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Ou; 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Este cmdlet irá definir as seguintes permissões: 
 

|Type |Name |Access |Aplica-se A| 
|-----|-----|-----|-----|
|Permitir |Conta do conector do AD DS |Ler todas as propriedades |Objetos de dispositivo subordinados| 
|Permitir |Conta do conector do AD DS|Ler todas as propriedades |Objetos de InetOrgPerson subordinados| 
|Permitir |Conta do conector do AD DS |Ler todas as propriedades |Objetos de computador subordinados| 
|Permitir |Conta do conector do AD DS |Ler todas as propriedades |Objetos de foreignSecurityPrincipal subordinados| 
|Permitir |Conta do conector do AD DS |Ler todas as propriedades |Objetos de grupo descendente| 
|Permitir |Conta do conector do AD DS |Ler todas as propriedades |Objetos de utilizador de subordinados| 
|Permitir |Conta do conector do AD DS |Ler todas as propriedades |Objetos de contato subordinados| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Configurar permissões de MS-DS-consistência-Guid 
Para definir permissões para a conta do conector do AD DS quando utiliza o atributo ms-Ds-consistência-Guid como a âncora de origem (também conhecido como opção "Permitir que o Azure gerir a âncora de origem para mim"), execute: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

Ou; 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Este cmdlet irá definir as seguintes permissões: 

|Type |Name |Access |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir|Conta do conector do AD DS|Propriedade de leitura/escrita|Objetos de utilizador de subordinados|

### <a name="permissions-for-password-hash-synchronization"></a>Permissões para a sincronização de Hash de palavra-passe 
Para definir permissões para a conta do conector do AD DS ao utilizar a sincronização de Hash de palavra-passe, execute: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


Ou; 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Este cmdlet irá definir as seguintes permissões: 

|Type |Name |Access |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta do conector do AD DS |Replicação de alterações de diretório |Apenas este objeto (raiz de domínio)| 
|Permitir |Conta do conector do AD DS |Replicar diretório muda tudo |Apenas este objeto (raiz de domínio)| 
  
### <a name="permissions-for-password-writeback"></a>Permissões para a repetição de escrita de palavra-passe 
Para definir permissões para a conta do conector do AD DS ao utilizar a repetição de escrita de palavra-passe, execute: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Ou;

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Este cmdlet irá definir as seguintes permissões: 

|Type |Name |Access |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta do conector do AD DS |Repor palavra-passe |Objetos de utilizador de subordinados| 
|Permitir |Conta do conector do AD DS |Escrever a propriedade lockoutTime |Objetos de utilizador de subordinados| 
|Permitir |Conta do conector do AD DS |Escrever a propriedade pwdLastSet |Objetos de utilizador de subordinados| 

### <a name="permissions-for-group-writeback"></a>Permissões para a repetição de escrita do grupo 
Para definir permissões para a conta do conector do AD DS ao utilizar a repetição de escrita do grupo, execute: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
Ou; 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Este cmdlet irá definir as seguintes permissões: 

|Type |Name |Access |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta do conector do AD DS |Leitura/gravação genérica |Todos os atributos do grupo de tipo de objeto e subobjetos| 
|Permitir |Conta do conector do AD DS |Criar/eliminar objeto subordinado |Todos os atributos do grupo de tipo de objeto e subobjetos| 
|Permitir |Conta do conector do AD DS |Objetos da árvore de eliminação/eliminação|Todos os atributos do grupo de tipo de objeto e subobjetos|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Permissões para implementação híbrida do Exchange 
Para definir permissões para a conta do conector do AD DS ao utilizar a implementação híbrida do Exchange, execute: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Ou; 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Este cmdlet irá definir as seguintes permissões:  
 

|Type |Name |Access |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta do conector do AD DS |Leitura/escrita todas as propriedades |Objetos de utilizador de subordinados| 
|Permitir |Conta do conector do AD DS |Leitura/escrita todas as propriedades |Objetos de InetOrgPerson subordinados| 
|Permitir |Conta do conector do AD DS |Leitura/escrita todas as propriedades |Objetos de grupo descendente| 
|Permitir |Conta do conector do AD DS |Leitura/escrita todas as propriedades |Objetos de contato subordinados| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Permissões para pastas públicas de correio do Exchange (pré-visualização) 
Para definir permissões para a conta do conector do AD DS quando utilizar a funcionalidade de pastas públicas de correio Exchange, execute: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Ou; 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Este cmdlet irá definir as seguintes permissões: 

|Type |Name |Access |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta do conector do AD DS |Ler todas as propriedades |Objetos de PublicFolder subordinados| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Restringir as permissões no conector AD DS conta 
Este script do PowerShell irá restringir permissões para a conta de conector AD fornecido como parâmetro. Fortalecendo permissões envolve os seguintes passos: 

- Desativar a herança do objeto especificado 
- Remova todas as ACEs de objeto específico, exceto ACEs específicos para o próprio que queremos manter as permissões predefinidas intactos quando se trata-se para o próprio. 
 
  O parâmetro - ADConnectorAccountDN é a conta do AD tem de ser fortalecida cujas permissões. Isso normalmente é a conta de domínio de MSOL_nnnnnnnnnnnn que está configurada no conector AD DS (consulte determinar sua conta de conector do AD DS). -Credential parâmetro é necessário especificar a conta de administrador que tem os privilégios necessários para restringir as permissões do Active Directory no objeto de destino AD. Isto é, normalmente, o Enterprise ou o administrador de domínio.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Por exemplo: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Este cmdlet irá definir as seguintes permissões: 

|Type |Name |Access |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |SISTEMA |Controlo Total |Este objeto 
|Permitir |Administradores da empresa |Controlo Total |Este objeto 
|Permitir |Admins do domínio |Controlo Total |Este objeto 
|Permitir |Administradores |Controlo Total |Este objeto 
|Permitir |Controladores de domínio de empresa |Listar conteúdo |Este objeto 
|Permitir |Controladores de domínio de empresa |Ler todas as propriedades |Este objeto 
|Permitir |Controladores de domínio de empresa |Permissões de Leitura |Este objeto 
|Permitir |Utilizadores Autenticados |Listar conteúdo |Este objeto 
|Permitir |Utilizadores Autenticados |Ler todas as propriedades |Este objeto 
|Permitir |Utilizadores Autenticados |Permissões de Leitura |Este objeto 

## <a name="next-steps"></a>Próximos Passos
- [Azure AD Connect: contas e permissões](reference-connect-accounts-permissions.md)
- [Instalação rápida](how-to-connect-install-express.md)
- [Instalação personalizada](how-to-connect-install-custom.md)
- [Referência de ADSyncConfig](reference-connect-adsyncconfig.md)

