---
title: 'Ligação Azure AD: Configure AD DS Conector De conta Permissões / Microsoft Docs'
description: Este documento detalha como configurar a conta de Conector AD DS com o novo módulo ADSyncConfig PowerShell
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72515812"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Ligação Azure AD: Configure As permissões da conta do conector AD DS 

O Módulo PowerShell denominado [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) foi introduzido com a build 1.1.880.0 (lançada em agosto de 2018) que inclui uma coleção de cmdlets para ajudá-lo a configurar as permissões de Diretório Ativo corretas para a sua implementação Azure AD Connect. 

## <a name="overview"></a>Descrição geral 
Os seguintes cmdlets PowerShell podem ser usados para configurar permissões de Diretório Ativo da conta AD DS Connector, para cada funcionalidade que selecionar para ativar no Azure AD Connect. Para evitar quaisquer problemas, deve preparar préviamente permissões de Diretório Ativo sempre que pretender instalar o Azure AD Connect utilizando uma conta de domínio personalizada para se ligar à sua floresta. Este módulo ADSyncConfig também pode ser usado para configurar permissões após a implementação do Azure AD Connect.

![visão geral da conta ad ds](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Para a instalação Azure AD Connect Express, uma conta gerada automaticamente (MSOL_nnnnnnnnnn) é criada em Diretório Ativo com todas as permissões necessárias, pelo que não há necessidade de usar este módulo ADSyncConfig a menos que tenha bloqueado permissões herança em unidades organizacionais ou em objetos de Diretório Ativo específicos que pretende sincronizar para Azure AD. 
 
### <a name="permissions-summary"></a>Resumo das permissões 
A tabela seguinte fornece um resumo das permissões exigidas em objetos AD: 

| Funcionalidade | Permissões |
| --- | --- |
| funcionalidade ms-DS-Consistência-Consistência |Leia e escreva permissões ao atributo ms-DS-ConsistênciaGuid documentado em Conceitos de [Design - Utilizando ms-DS-ConsistênciaGuid como fonteAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Sincronização de hash de palavra-passe |<li>Replicar alterações de diretório</li>  <li>Replicar mudanças de diretório todos |
| Troca de implantação híbrida |Leia e Escreva permissões aos atributos documentados no [Exchange hybrid writeback](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) para utilizadores, grupos e contactos. |
| Pasta Pública de Troca de Correio |Leia as permissões aos atributos documentados na Pasta Pública de [Troca de Correios](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) para pastas públicas. | 
| Repetição de escrita de palavras-passe |Leia e Escreva permissões aos atributos documentados em [Começar com a gestão](../authentication/howto-sspr-writeback.md) de passwords para os utilizadores. |
| Repetição de escrita do dispositivo |Leia e escreva permissões para dispositivos objetos e recipientes documentados na [reescrita](how-to-connect-device-writeback.md)do dispositivo . |
| Repetição de escrita do grupo |Ler, Criar, Atualizar e Eliminar objetos de grupo para grupos sincronizados **do Office 365**.  Para mais informações consulte o [Group Writeback](how-to-connect-preview.md#group-writeback).|

## <a name="using-the-adsyncconfig-powershell-module"></a>Utilização do Módulo PowerShell ADSyncConfig 
O módulo ADSyncConfig requer as Ferramentas de Administração do [Servidor Remoto (RSAT) para DS AD,](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) uma vez que depende do módulo e ferramentas AD DS PowerShell. Para instalar o RSAT para DS AD, abra uma janela Do Windows PowerShell com 'Executar como administrador' e executar: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Configurar](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Também pode copiar o ficheiro **C:\Program Files\Microsoft Azure Ative Directory Connect\AdSyncConfig\ADSyncConfig.psm1** para um Controlador de Domínio que já tem RSAT para DS AD instalado e utilizar este módulo PowerShell a partir daí.

Para começar a utilizar o ADSyncConfig é necessário carregar o módulo numa janela do Windows PowerShell: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Para verificar todos os cmdlets incluídos neste módulo pode digitar:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Marcar](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Cada cmdlet tem os mesmos parâmetros para inserir a Conta de Conector AD DS e um interruptor AdminSDHolder. Para especificar a sua Conta de Conector AD DS, pode fornecer o nome e domínio da conta, ou apenas o nome distinto da conta (DN),

Por exemplo,

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Ou;

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Certifique-se `<ADAccountName>`de `<ADDomainName>` `<ADAccountDN>` substituir, e com os valores adequados para o seu ambiente.

Caso não pretenda modificar permissões no recipiente 'AdministradorSDHolder', utilize o interruptor `-SkipAdminSdHolders`. 

Por predefinição, todas as permissões definidas tentarão definir permissões AD DS na raiz de cada Domínio na Floresta, o que significa que o utilizador que executa a sessão PowerShell requer direitos de Administrador de Domínio em cada domínio na Floresta.  Devido a este requisito, recomenda-se a utilização de um Administrador Empresarial da raiz florestal. Se a sua implantação Azure AD Connect tiver vários Conectores AD DS, será necessário executar o mesmo cmdlet em cada floresta que tenha um Conector AD DS. 

Também pode definir permissões num objeto ou ad dS `-ADobjectDN` específico utilizando o parâmetro seguido pelo DN do objeto alvo onde pretende definir permissões. Ao utilizar um ADobjectDN alvo, o cmdlet definirá permissões apenas neste objeto e não na raiz de domínio ou no recipiente AdminSDHolder. Este parâmetro pode ser útil quando tiver certos objetos OUs ou DS AD que tenham permissão desativada (ver Localizar objetos AD DS com permissão desativada) 

Exceções a estes parâmetros `Set-ADSyncRestrictedPermissions` comuns são o cmdlet que é usado para definir as permissões na própria Conta de Conector AD DS, e o `Set-ADSyncPasswordHashSyncPermissions` cmdlet `-ObjectDN` uma `-SkipAdminSdHolders` vez que as permissões necessárias para O Sync de Hash De palavra-passe são apenas definidas na raiz de domínio, pelo que este cmdlet não inclui os parâmetros ou parâmetros.

### <a name="determine-your-ad-ds-connector-account"></a>Determine a sua conta de conector AD DS 
Caso o Azure AD Connect já esteja instalado e deseja verificar qual é a conta de conector AD DS atualmente utilizada pelo Azure AD Connect, pode executar o cmdlet: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Localizar objetos AD DS com permissão de herança desativada 
Caso pretenda verificar se existe algum objeto AD DS com herança de permissão desativada, pode executar: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Por predefinição, este cmdlet só procurará OUs com herança desativada, `-ObjectClass` mas pode especificar outras classes de objetos AD DS no parâmetro ou usar '*' para todas as classes de objetos, da seguinte forma: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Ver permissões AD DS de um objeto 
Pode utilizar o cmdlet abaixo para visualizar a lista de permissões atualmente definidas num objeto de Diretório Ativo, fornecendo o seu Nome Distinto: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Configure AD DS Connector Account Permissions (Azure AD Connect: Configurar as Permissões da Conta do Conector do AD DS) 
 
### <a name="configure-basic-read-only-permissions"></a>Configurar permissões básicas de leitura 
Para definir permissões básicas de leitura apenas para a conta AD DS Connector quando não utilizar qualquer funcionalidade Azure AD Connect, executar: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ou; 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Este cmdlet definirá as seguintes permissões: 
 

|Tipo |Nome |Acesso |Aplica-se A| 
|-----|-----|-----|-----|
|Permitir |Conta de Conector AD DS |Ler todas as propriedades |Objetos de dispositivo descendente| 
|Permitir |Conta de Conector AD DS|Ler todas as propriedades |Objetos descendentes inetOrgPerson| 
|Permitir |Conta de Conector AD DS |Ler todas as propriedades |Objetos de computador descendentes| 
|Permitir |Conta de Conector AD DS |Ler todas as propriedades |Objetos descendentes de Segurança EstrangeiraPrincipais| 
|Permitir |Conta de Conector AD DS |Ler todas as propriedades |Objetos do Grupo Descendente| 
|Permitir |Conta de Conector AD DS |Ler todas as propriedades |Objetos de utilizador descendente| 
|Permitir |Conta de Conector AD DS |Ler todas as propriedades |Objetos de contacto descendentes| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Configure permissões mS-DS-Consistência-Guia 
Para definir permissões para a conta AD DS Connector ao utilizar o atributo ms-Ds-Consistência-Guia como âncora de origem (aka "Deixe O Azure gerir a âncora de origem para mim" 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

ou; 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Este cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Acesso |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir|Conta de Conector AD DS|Ler/Escrever propriedade|Objetos de utilizador descendente|

### <a name="permissions-for-password-hash-synchronization"></a>Permissões para sincronização de hash password 
Para definir permissões para a conta de Conector AD DS ao utilizar a Sincronização de Hash password, executar: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


ou; 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Este cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Acesso |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta de Conector AD DS |Replicando alterações de diretório |Este objeto apenas (raiz de domínio)| 
|Permitir |Conta de Conector AD DS |Replicando mudanças de diretório todos |Este objeto apenas (raiz de domínio)| 
  
### <a name="permissions-for-password-writeback"></a>Permissões para A Dispor de Palavras-passe 
Para definir permissões para a conta de Conector AD DS ao utilizar a Palavra-passe Writeback, executar: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ou;

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Este cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Acesso |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta de Conector AD DS |Repor palavra-passe |Objetos de utilizador descendente| 
|Permitir |Conta de Conector AD DS |Escreva lockout de propriedadeTime |Objetos de utilizador descendente| 
|Permitir |Conta de Conector AD DS |Escreva propriedade pwdLastSet |Objetos de utilizador descendente| 

### <a name="permissions-for-group-writeback"></a>Permissões para Reescrita de Grupo 
Para definir permissões para a conta AD DS Connector ao utilizar a Writeback do Grupo, executar: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
ou; 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Este cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Acesso |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta de Conector AD DS |Leitura/Escrita Genérica |Todos os atributos do grupo e subobjetos tipo objeto| 
|Permitir |Conta de Conector AD DS |Criar/Eliminar o objeto infantil |Todos os atributos do grupo e subobjetos tipo objeto| 
|Permitir |Conta de Conector AD DS |Eliminar/Eliminar objetos de árvores|Todos os atributos do grupo e subobjetos tipo objeto|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Permissões para troca de implantação híbrida 
Para definir permissões para a conta DeConector AD DS ao utilizar a implementação do Exchange Hybrid, executar: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ou; 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Este cmdlet definirá as seguintes permissões:  
 

|Tipo |Nome |Acesso |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta de Conector AD DS |Ler/Escrever todas as propriedades |Objetos de utilizador descendente| 
|Permitir |Conta de Conector AD DS |Ler/Escrever todas as propriedades |Objetos descendentes inetOrgPerson| 
|Permitir |Conta de Conector AD DS |Ler/Escrever todas as propriedades |Objetos do Grupo Descendente| 
|Permitir |Conta de Conector AD DS |Ler/Escrever todas as propriedades |Objetos de contacto descendentes| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Permissões para pastas públicas de troca de correio (pré-visualização) 
Para definir permissões para a conta DeD DS Quando utilizar a funcionalidade de pastas públicas de Correio de Troca, executar: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ou; 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Este cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Acesso |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta de Conector AD DS |Ler todas as propriedades |Objetos descendentes da pasta pública| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Restringir permissões na conta de conector AD DS 
Este script PowerShell irá apertar as permissões para a Conta de Conector AD fornecida como parâmetro. Permissões de aperto envolvem os seguintes passos: 

- Desativar a herança no objeto especificado 
- Remova todos os ACEs no objeto específico, exceto ACEs específicos de SELF, pois queremos manter as permissões padrão intactas quando se trata de SELF. 
 
  O parâmetro -ADConnectorAccountDN é a conta AD cujas permissões precisam de ser apertadas. Esta é tipicamente a conta de domínio MSOL_nnnnnnnnnnnn configurada no Conector AD DS (ver Determine a sua conta de conector AD DS). O parâmetro -credencial é necessário para especificar a conta do Administrador que tem os privilégios necessários para restringir permissões de Diretório Ativo no objeto ad.alvo. Este é tipicamente o Administrador de Empresa ou Domínio.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Por exemplo: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Este cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Acesso |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |SISTEMA |Controlo Total |Este objeto 
|Permitir |Administradores da Empresa |Controlo Total |Este objeto 
|Permitir |Administradores do Domínio |Controlo Total |Este objeto 
|Permitir |Administradores |Controlo Total |Este objeto 
|Permitir |Controladores de domínio empresarial |Conteúdos da lista |Este objeto 
|Permitir |Controladores de domínio empresarial |Ler todas as propriedades |Este objeto 
|Permitir |Controladores de domínio empresarial |Ler Permissões |Este objeto 
|Permitir |Utilizadores Autenticados |Conteúdos da lista |Este objeto 
|Permitir |Utilizadores Autenticados |Ler todas as propriedades |Este objeto 
|Permitir |Utilizadores Autenticados |Ler Permissões |Este objeto 

## <a name="next-steps"></a>Passos Seguintes
- [Azure AD Connect: contas e permissões](reference-connect-accounts-permissions.md)
- [Instalação Express](how-to-connect-install-express.md)
- [Instalação Personalizada](how-to-connect-install-custom.md)
- [Referência de ADSyncConfig](reference-connect-adsyncconfig.md)

