---
title: Configurar definições de grupo com o PowerShell - Azure Active Directory | Documentos da Microsoft
description: Como gerir as definições de grupos utilizando cmdlets do Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/26/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5773924e98b7ea13c180979dba1325eb8919ff3a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090632"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlets do Azure Active Directory para configurar definições de grupo
Este artigo contém instruções para utilizar cmdlets do PowerShell do Azure Active Directory (Azure AD) para criar e atualizar os grupos. Este conteúdo aplica-se apenas a grupos do Office 365 (por vezes denominados grupos unificados). 

> [!IMPORTANT]
> Algumas definições requerem uma licença do Azure Active Directory Premium P1. Para obter mais informações, consulte a [as definições do modelo](#template-settings) tabela.

Para obter mais informações sobre como impedir que usuários não-administrador criar grupos de segurança, defina `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` conforme descrito na [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

Definições de grupos do Office 365 são configuradas com um objeto de definições e um objeto de SettingsTemplate. Inicialmente, não vê quaisquer objetos de definições no seu diretório, porque o seu diretório está configurado com as predefinições. Para alterar as configurações padrão, tem de criar um novo objeto de definições através de um modelo de definições. Modelos de configurações são definidos pela Microsoft. Existem vários modelos de configurações diferentes. Para configurar as definições de grupo do Office 365 para o seu diretório, use o modelo com o nome "Group.Unified". Para configurar definições de grupo do Office 365 num único grupo, utilize o modelo com o nome "Group.Unified.Guest". Este modelo é utilizado para gerir o acesso de convidado para um grupo do Office 365. 

Os cmdlets são parte do módulo Azure Active Directory PowerShell V2. Para obter instruções sobre como transferir e instalar o módulo no seu computador, consulte o artigo [do Azure Active Directory PowerShell versão 2](https://docs.microsoft.com/powershell/azuread/). Pode instalar o lançamento da versão 2 do módulo partir [da galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="retrieve-a-specific-settings-value"></a>Recuperar um valor de definições específicas
Se souber o nome da definição que pretende recuperar, pode utilizar o cmdlet para obter o valor atual de definições abaixo. Neste exemplo, recuperando o valor para uma definição denominada "UsageGuidelinesUrl." Pode ler que mais sobre as definições de diretório e os respetivos nomes mais adiante neste artigo.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>Criar definições ao nível do diretório
Estes passos criam definições ao nível do diretório, que se aplicam a todos os grupos do Office 365 no diretório. O cmdlet Get-AzureADDirectorySettingTemplate está disponível apenas na [módulo de pré-visualização do Azure AD PowerShell para Graph](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. Os cmdlets de DirectorySettings, tem de especificar o ID de SettingsTemplate que pretende utilizar. Se não conhecer essa ID, este cmdlet devolve a lista de todos os modelos de definições:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
   Esta chamada de cmdlet devolve todos os modelos que estão disponíveis:
  
   ```powershell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Para adicionar um URL de orientação de utilização, primeiro tem de obter o objeto de SettingsTemplate que define o valor de URL de orientação de utilização; ou seja, o modelo de Group.Unified:
  
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Em seguida, crie um novo objeto de definições baseado nesse modelo:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Em seguida, atualize o valor de orientação de utilização:
  
   ```powershell
   $setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Por fim, aplique as definições:
  
   ```powershell
   New-AzureADDirectorySetting -DirectorySetting $setting
   ```

Após a conclusão bem-sucedida, o cmdlet retorna a ID do novo objeto de definições:

  ```powershell
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

## <a name="template-settings"></a>Definições do modelo
Seguem-se as configurações definidas no Group.Unified SettingsTemplate. A menos que indicado de outra forma, estas funcionalidades requerem uma licença do Azure Active Directory Premium P1. 

| **Definição** | **Descrição** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Escreva: Booleano<li>Predefinição: Verdadeiro |O sinalizador que indica se a criação do grupo do Office 365 é permitida no diretório por usuários não-administradores. Esta definição não requer uma licença do Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Escreva: String<li>Predefinição: "" |GUID do grupo de segurança para os quais os membros têm permissão para criar grupos do Office 365, mesmo quando EnableGroupCreation = = false. |
|  <ul><li>UsageGuidelinesUrl<li>Escreva: String<li>Predefinição: "" |Uma ligação para as diretrizes de utilização do grupo. |
|  <ul><li>ClassificationDescriptions<li>Escreva: String<li>Predefinição: "" | Uma lista delimitada por vírgulas de descrições de classificação. O valor de ClassificationDescriptions só é válido no seguinte formato:<br>$setting[“ClassificationDescriptions”] ="Classification:Description,Classification:Description"<br>onde classificação corresponde a cadeias de caracteres no ClassificationList.|
|  <ul><li>DefaultClassification<li>Escreva: String<li>Predefinição: "" | A classificação que está a ser utilizado como a classificação predefinida para um grupo se foi especificado nenhum.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Escreva: String<li>Predefinição: "" | A cadeia de caracteres de um comprimento máximo de 64 carateres, que define a Convenção de nomenclatura configurada para grupos do Office 365. Para obter mais informações, consulte [impor uma política de nomes para grupos do Office 365](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Escreva: String<li>Predefinição: "" | Cadeia separada por vírgulas das expressões que os utilizadores não terão permissão para utilizar em nomes de grupo ou aliases. Para obter mais informações, consulte [impor uma política de nomes para grupos do Office 365](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Escreva: Booleano<li>Predefinição: "False" | Não utilize
|  <ul><li>AllowGuestsToBeGroupOwner<li>Escreva: Booleano<li>Predefinição: Falso | Valor booleano que indica se é ou não um utilizador convidado pode ser um proprietário de grupos. |
|  <ul><li>AllowGuestsToAccessGroups<li>Escreva: Booleano<li>Predefinição: Verdadeiro | Valor booleano que indica se é ou não um utilizador convidado pode ter acesso ao conteúdo de grupos do Office 365.  Esta definição não requer uma licença do Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Escreva: String<li>Predefinição: "" | O url de uma ligação para as diretrizes de utilização do convidado. |
|  <ul><li>AllowToAddGuests<li>Escreva: Booleano<li>Predefinição: Verdadeiro | Um booleano que indica se é ou não tem permissão para adicionar convidados para este diretório.|
|  <ul><li>ClassificationList<li>Escreva: String<li>Predefinição: "" |Uma lista delimitada por vírgulas de valores de classificação válido que podem ser aplicadas a grupos do Office 365. |

## <a name="read-settings-at-the-directory-level"></a>Ler definições ao nível do diretório
Estes passos ler as configurações ao nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Ler todas as definições do diretório existente:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Este cmdlet devolve uma lista de todas as definições de diretório:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Ler todas as definições para um grupo específico:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Ler todos os valores de definições de diretório de um objeto de definições de diretório específico, usando o GUID de Id de definições:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   Este cmdlet devolve os nomes e valores neste objeto de definições para este grupo específico:
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowToAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="update-settings-for-a-specific-group"></a>Atualizar as definições para um grupo específico

1. Procure o modelo de configurações com o nome "Groups.Unified.Guest"
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Recuperar o objeto de modelo para o modelo de Groups.Unified.Guest:
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
   ```
3. Crie um novo objeto de definições do modelo:
   ```powershell
   $Setting = $Template.CreateDirectorySetting()
   ```

4. Defina a definição para o valor necessário:
   ```powershell
   $Setting["AllowToAddGuests"]=$False
   ```
5. Crie a nova definição para o grupo de necessário no diretório:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
   Id                                   DisplayName TemplateId                           Values
   --                                   ----------- ----------                           ------
   25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
   ```

## <a name="update-settings-at-the-directory-level"></a>Atualizar as definições ao nível do diretório

Estes passos atualizam as definições ao nível do diretório, que se aplicam a todos os grupos do Office 365 no diretório. Estes exemplos partem do princípio de que já existe um objeto de definições no seu diretório.

1. Encontre o objeto de definições existente:
   ```powershell
   $setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
2. Atualize o valor:
  
   ```powershell
   $Setting["AllowToAddGuests"] = "false"
   ```
3. Atualize a definição:
  
   ```powershell
   Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
   ```

## <a name="remove-settings-at-the-directory-level"></a>Remova as definições ao nível do diretório
Este passo remove as definições ao nível do diretório, que se aplicam a todos os grupos do Office no diretório.
  ```powershell
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Referência de sintaxe do cmdlet
Pode encontrar mais documentação do Azure Active Directory PowerShell em [Cmdlets do Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Leitura adicional

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
