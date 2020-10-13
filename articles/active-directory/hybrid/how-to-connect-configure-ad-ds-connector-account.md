---
title: 'Azure AD Connect: Configure AD DS Connector Account Permisses Microsoft Docs'
description: Este documento detalha como configurar a conta AD DS Connector com o novo módulo ADSyncConfig PowerShell
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a082270e2c113bcdf31ed6bd6db3d38a7117500
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279300"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: Configure AD DS Connector Account Permissions (Azure AD Connect: Configurar as Permissões da Conta do Conector do AD DS) 

O Módulo PowerShell chamado [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) foi introduzido com a construção 1.1.880.0 (lançado em agosto de 2018) que inclui uma coleção de cmdlets para ajudá-lo a configurar as permissões corretas do Ative Directory para a sua implementação AZURE AD Connect. 

## <a name="overview"></a>Descrição geral 
Os cmdlets PowerShell a seguir podem ser utilizados para configurar permissões de Ative Directory da conta AD DS Connector, para cada recurso que seleciona para ativar no Azure AD Connect. Para evitar problemas, deve preparar permissões de Ative Directory com antecedência sempre que pretender instalar o Azure AD Connect utilizando uma conta de domínio personalizada para se ligar à sua floresta. Este módulo ADSyncConfig também pode ser usado para configurar permissões após a implementação do Azure AD Connect.

![visão geral da conta de anúncios ds](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Para a instalação Azure AD Connect Express, é criada uma conta gerada automaticamente (MSOL_nnnnnnnnnn) em Ative Directory com todas as permissões necessárias, pelo que não há necessidade de utilizar este módulo ADSyncConfig a menos que tenha bloqueado permissões de herança em unidades organizacionais ou em objetos específicos do Ative Directory que pretende sincronizar com a Azure AD. 
 
### <a name="permissions-summary"></a>Resumo das permissões 
A tabela a seguir fornece um resumo das permissões exigidas em objetos AD: 

| Funcionalidade | Permissões |
| --- | --- |
| recurso ms-DS-ConsistênciaGuid |Leia e escreva permissões para o atributo ms-DS-ConsistênciaGuid documentado em [Conceitos de Design - Usando ms-DS-ConsistênciaGuid como fonteAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Sincronização de haxixe de palavra-passe |<li>Alterar o diretório de replicação</li>  <li>Replicar mudanças de diretório tudo |
| Implementação híbrida de troca |Leia e escreva permissões para os atributos documentados na [reversão híbrida do Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) para utilizadores, grupos e contactos. |
| Troca de Correio Público Pasta |Leia as permissões dos atributos documentados na [Pasta Pública do Correio da Manhã](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) para pastas públicas. | 
| Repetição de escrita de palavras-passe |Leia e escreva permissões para os atributos documentados em [Começar com a gestão de passwords](../authentication/tutorial-enable-sspr-writeback.md) para os utilizadores. |
| Repetição de escrita do dispositivo |Leia e escreva permissões para dispositivos e contentores documentados na [gravação do dispositivo](how-to-connect-device-writeback.md). |
| Repetição de escrita do grupo |Ler, criar, atualizar e eliminar objetos de grupo para grupos sincronizados **do Office 365**.|

## <a name="using-the-adsyncconfig-powershell-module"></a>Utilizando o módulo ADSyncConfig PowerShell 
O módulo ADSyncConfig requer as [Ferramentas de Administração de Servidor Remoto (RSAT) para DS AD,](/windows-server/remote/remote-server-administration-tools) uma vez que depende do módulo e ferramentas AD DS PowerShell. Para instalar o RSAT para DS AD, abra uma janela Windows PowerShell com 'Executar como administrador' e execute: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Configurar](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Também pode copiar o ficheiro **C:\Program Files\Microsoft Azure Ative Directory Connect\AdSyncConfig\ADSyncConfig.psm1** para um Controlador de Domínio que já tem RSAT para DS AD instalado e utilizar este módulo PowerShell a partir daí.

Para começar a utilizar o ADSyncConfig é necessário carregar o módulo numa janela Windows PowerShell: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Para verificar todos os cmdlets incluídos neste módulo pode escrever:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Marcar](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Cada cmdlet tem os mesmos parâmetros para inserir a Conta de Conector DS AD e um interruptor AdminSDHolder. Para especificar a sua Conta de Conector AD DS, pode fornecer o nome e o domínio da conta, ou apenas a conta Nome Distinto (DN),

Por exemplo,

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Ou;

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Certifique-se de que substitui `<ADAccountName>` , `<ADDomainName>` e pelos `<ADAccountDN>` valores adequados para o seu ambiente.

Caso não pretenda modificar permissões no recipiente AdminSDHolder, utilize o interruptor `-SkipAdminSdHolders` . 

Por predefinição, todas as permissões definidas tentarão definir permissões AD DS na raiz de cada Domínio na Floresta, o que significa que o utilizador que executa a sessão PowerShell requer direitos de Administrador de Domínio em cada domínio na Floresta.  Devido a este requisito, recomenda-se a utilização de um Administrador Empresarial a partir da raiz florestal. Se a sua implementação Azure AD Connect tiver vários Conectores AD DS, será necessário executar o mesmo cmdlet em cada floresta que tenha um Conector DS AD. 

Também pode definir permissões num objeto ou DS específico da OU ou AD, utilizando o parâmetro `-ADobjectDN` seguido pelo DN do objeto-alvo onde pretende definir permissões. Ao utilizar um ADobjectDN-alvo, o cmdlet definirá permissões apenas neste objeto e não na raiz de domínio ou no recipiente AdminSDHolder. Este parâmetro pode ser útil quando tiver certos objetos OUs ou DS AD que tenham a herança de permissão desativada (ver localizar objetos DS AD com a herança de permissão desativada) 

As exceções a estes parâmetros comuns são o `Set-ADSyncRestrictedPermissions` cmdlet que é utilizado para definir as permissões na própria Conta do Conector DS AD, e o cmdlet uma vez que `Set-ADSyncPasswordHashSyncPermissions` as permissões necessárias para o Password Hash Sync são apenas definidas na raiz do domínio, pelo que este cmdlet não inclui os `-ObjectDN` parâmetros ou `-SkipAdminSdHolders` parâmetros.

### <a name="determine-your-ad-ds-connector-account"></a>Determine a sua conta de conector AD DS 
Caso o Azure AD Connect já esteja instalado e pretender verificar o que é a Conta de Conector AD DS atualmente em utilização pelo Azure AD Connect, pode executar o cmdlet: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Localizar objetos DS AD com a herdade de permissão desativada 
Caso pretenda verificar se existe algum objeto DS AD com a permissão desativada, pode executar: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Por predefinição, este cmdlet só procurará OUs com herança desativada, mas pode especificar outras classes de objetos AD DS em `-ObjectClass` parâmetro ou usar '*' para todas as classes de objetos, da seguinte forma: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Ver permissões de DS AD de um objeto 
Pode utilizar o cmdlet abaixo para visualizar a lista de permissões atualmente definidas num objeto ative directory, fornecendo o seu Nome Distinto: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Configure AD DS Connector Account Permissions (Azure AD Connect: Configurar as Permissões da Conta do Conector do AD DS) 
 
### <a name="configure-basic-read-only-permissions"></a>Configure permissões básicas de Read-Only 
Para definir permissões básicas apenas de leitura para a conta AD DS Connector quando não utilizar qualquer função Azure AD Connect, execute: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ou; 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Este cmdlet definirá as seguintes permissões: 
 

|Tipo |Nome |Access |Aplica-se A| 
|-----|-----|-----|-----|
|Permitir |Conta de Conector AD DS |Ler todas as propriedades |Objetos de dispositivo descendente| 
|Permitir |Conta de Conector AD DS|Ler todas as propriedades |Objetos inetOrgperson descendentes| 
|Permitir |Conta de Conector AD DS |Ler todas as propriedades |Objetos de computador descendentes| 
|Permitir |Conta de Conector AD DS |Ler todas as propriedades |Objetos de Segurança Estrangeira Descendentes| 
|Permitir |Conta de Conector AD DS |Ler todas as propriedades |Objetos de grupo descendentes| 
|Permitir |Conta de Conector AD DS |Ler todas as propriedades |Objetos de utilizador descendentes| 
|Permitir |Conta de Conector AD DS |Ler todas as propriedades |Objetos de contacto descendentes| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Configure permissões MS-DS-Consistência-Guid 
Para definir permissões para a conta AD DS Connector ao utilizar o atributo MS-DS-Consistência-Guid como âncora de origem (também conhecido como "Deixe o Azure gerir a âncora de origem para mim") , executar: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

ou; 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Este cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Access |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir|Conta de Conector AD DS|Ler/Escrever propriedade|Objetos de utilizador descendentes|

### <a name="permissions-for-password-hash-synchronization"></a>Permissões para sincronização de hash de palavra-passe 
Para definir permissões para a conta AD DS Connector ao utilizar a Sincronização de Hash Password, execute: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


ou; 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Este cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Access |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta de Conector AD DS |Alterações de Diretório de Replicação |Este objeto apenas (raiz de domínio)| 
|Permitir |Conta de Conector AD DS |Replicando mudanças de diretório tudo |Este objeto apenas (raiz de domínio)| 
  
### <a name="permissions-for-password-writeback"></a>Permissões para writeback de palavra-passe 
Para definir permissões para a conta AD DS Connector ao utilizar o Writeback password, execute: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ou;

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Este cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Access |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta de Conector AD DS |Repor palavra-passe |Objetos de utilizador descendentes| 
|Permitir |Conta de Conector AD DS |Escrever bloqueio de propriedadeTime |Objetos de utilizador descendentes| 
|Permitir |Conta de Conector AD DS |Escrever propriedade pwdLastSet |Objetos de utilizador descendentes| 

### <a name="permissions-for-group-writeback"></a>Permissões para Writeback de Grupo 
Para definir permissões para a conta AD DS Connector ao utilizar o Writeback do Grupo, execute: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
ou; 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Este cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Access |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta de Conector AD DS |Leitura/Escrita Genérica |Todos os atributos do grupo de tipo de objeto e subobjecta| 
|Permitir |Conta de Conector AD DS |Criar/Eliminar objeto infantil |Todos os atributos do grupo de tipo de objeto e subobjecta| 
|Permitir |Conta de Conector AD DS |Eliminar/Eliminar objetos de árvores|Todos os atributos do grupo de tipo de objeto e subobjecta|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Permissões para a implementação híbrida de troca 
Para definir permissões para a conta AD DS Connector ao utilizar a implementação do Exchange Hybrid, execute: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ou; 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Este cmdlet definirá as seguintes permissões:  
 

|Tipo |Nome |Access |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta de Conector AD DS |Ler/Escrever todas as propriedades |Objetos de utilizador descendentes| 
|Permitir |Conta de Conector AD DS |Ler/Escrever todas as propriedades |Objetos inetOrgperson descendentes| 
|Permitir |Conta de Conector AD DS |Ler/Escrever todas as propriedades |Objetos de grupo descendentes| 
|Permitir |Conta de Conector AD DS |Ler/Escrever todas as propriedades |Objetos de contacto descendentes| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Permissões para Troca de Pastas Públicas de Correio (Pré-visualização) 
Para definir permissões para a conta AD DS Connector ao utilizar a função De pastas públicas Exchange Mail, execute: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


ou; 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Este cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Access |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |Conta de Conector AD DS |Ler todas as propriedades |Objetos de PublicFolder descendentes| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Restringir permissões na conta do conector DS AD 
Este script PowerShell irá apertar as permissões para a Conta de Conector AD fornecida como parâmetro. As permissões de aperto envolvem os seguintes passos: 

- Desativar a herança no objeto especificado 
- Remova todos os ACEs no objeto específico, exceto ACEs específicos de SELF, pois queremos manter as permissões padrão intactas quando se trata de SELF. 
 
  O parâmetro -ADConnectorAccountDN é a conta AD cujas permissões precisam de ser reforçadas. Esta é tipicamente a conta de domínio MSOL_nnnnnnnnnnnn que está configurada no Conector DS AD (ver Determine a sua Conta de Conector DS AD). O parâmetro -Credencial é necessário para especificar a conta do Administrador que tem os privilégios necessários para restringir permissões de Diretório Ativo no objeto AD alvo. Este é tipicamente o Enterprise ou Administrador de Domínio.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Por exemplo: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Este cmdlet definirá as seguintes permissões: 

|Tipo |Nome |Access |Aplica-se A|
|-----|-----|-----|-----| 
|Permitir |SISTEMA |Controlo Total |Este objeto 
|Permitir |Administradores da Empresa |Controlo Total |Este objeto 
|Permitir |Administradores do Domínio |Controlo Total |Este objeto 
|Permitir |Administradores |Controlo Total |Este objeto 
|Permitir |Controladores de domínio da empresa |Conteúdo da Lista |Este objeto 
|Permitir |Controladores de domínio da empresa |Ler todas as propriedades |Este objeto 
|Permitir |Controladores de domínio da empresa |Ler Permissões |Este objeto 
|Permitir |Utilizadores Autenticados |Conteúdo da Lista |Este objeto 
|Permitir |Utilizadores Autenticados |Ler todas as propriedades |Este objeto 
|Permitir |Utilizadores Autenticados |Ler Permissões |Este objeto 

## <a name="next-steps"></a>Passos Seguintes
- [Azure AD Connect: contas e permissões](reference-connect-accounts-permissions.md)
- [Instalação Express](how-to-connect-install-express.md)
- [Instalação personalizada](how-to-connect-install-custom.md)
- [Referência de ADSyncConfig](reference-connect-adsyncconfig.md)