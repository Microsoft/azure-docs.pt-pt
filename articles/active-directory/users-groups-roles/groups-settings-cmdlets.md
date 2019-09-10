---
title: Definir configurações de grupo usando o PowerShell-Azure Active Directory | Microsoft Docs
description: Como gerenciar as configurações de grupos usando cmdlets Azure Active Directory
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
ms.openlocfilehash: 73784afd9577d66850596056df1974accd62e4b4
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844457"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlets do Azure Active Directory para configurar definições de grupo
Este artigo contém instruções para usar os cmdlets do PowerShell do Azure Active Directory (AD do Azure) para criar e atualizar grupos. Esse conteúdo se aplica somente a grupos do Office 365 (às vezes chamados de grupos unificados). 

> [!IMPORTANT]
> Algumas configurações exigem uma licença Azure Active Directory Premium P1. Para obter mais informações, consulte a tabela de [configurações de modelo](#template-settings) .

Para obter mais informações sobre como impedir que usuários não administradores criem grupos de segurança, `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` defina conforme descrito em [set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

As configurações de grupos do Office 365 são definidas usando um objeto Settings e um objeto Settingstemplate. Inicialmente, você não vê nenhum objeto de configuração em seu diretório, porque seu diretório está configurado com as configurações padrão. Para alterar as configurações padrão, você deve criar um novo objeto de configurações usando um modelo de configurações. Os modelos de configurações são definidos pela Microsoft. Há vários modelos de configurações diferentes. Para definir as configurações de grupo do Office 365 para seu diretório, use o modelo chamado "Group. Unified". Para definir as configurações de grupo do Office 365 em um único grupo, use o modelo chamado "Group. Unified. Guest". Este modelo é usado para gerenciar o acesso de convidado a um grupo do Office 365. 

Os cmdlets fazem parte do módulo Azure Active Directory PowerShell v2. Para obter instruções sobre como baixar e instalar o módulo em seu computador, consulte o artigo [Azure Active Directory PowerShell versão 2](https://docs.microsoft.com/powershell/azuread/). Você pode instalar a versão 2 do módulo da galeria do [PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="create-settings-at-the-directory-level"></a>Criar configurações no nível do diretório
Essas etapas criam configurações no nível do diretório, que se aplicam a todos os grupos do Office 365 no diretório. O cmdlet Get-AzureADDirectorySettingTemplate está disponível somente no [módulo de visualização do PowerShell do Azure ad para Graph](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. Nos cmdlets DirectorySettings, você deve especificar a ID do Settingstemplate que deseja usar. Se você não souber essa ID, esse cmdlet retornará a lista de todos os modelos de configurações:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
   Essa chamada de cmdlet retorna todos os modelos disponíveis:
  
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
2. Para adicionar uma URL de diretriz de uso, primeiro você precisa obter o objeto Settingstemplate que define o valor da URL de orientação de uso; ou seja, o modelo Group. Unified:
  
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Em seguida, crie um novo objeto de configurações com base nesse modelo:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Em seguida, atualize o valor de diretriz de uso:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Em seguida, aplique a configuração:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Você pode ler os valores usando:

   ```powershell
   $Setting.Values
   ```  
## <a name="update-settings-at-the-directory-level"></a>Atualizar as configurações no nível do diretório
Para atualizar o valor de UsageGuideLinesUrl no modelo de configuração, basta editar a URL com a etapa 4 acima e, em seguida, executar a etapa 5 para definir o novo valor.

Para remover o valor de UsageGuideLinesUrl, edite a URL para ser uma cadeia de caracteres vazia usando a etapa 4 acima:

   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
Em seguida, execute a etapa 5 para definir o novo valor.

## <a name="template-settings"></a>Configurações de modelo
Aqui estão as configurações definidas no Group. Unified Settingstemplate. Salvo indicação em contrário, esses recursos exigem uma licença Azure Active Directory Premium P1. 

| **Definição** | **Descrição** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Escreva: Booleano<li>Os True |O sinalizador que indica se a criação do grupo do Office 365 é permitida no diretório por usuários não administradores. Essa configuração não requer uma licença Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Escreva: Cadeia<li>Padrão: "" |GUID do grupo de segurança para o qual os membros têm permissão para criar grupos do Office 365 mesmo quando EnableGroupCreation = = false. |
|  <ul><li>UsageGuidelinesUrl<li>Escreva: Cadeia<li>Padrão: "" |Um link para as diretrizes de uso do grupo. |
|  <ul><li>ClassificationDescriptions<li>Escreva: Cadeia<li>Padrão: "" | Uma lista delimitada por vírgulas de descrições de classificação. O valor de ClassificationDescriptions é válido somente neste formato:<br>$setting ["ClassificationDescriptions"] = "classificação: descrição, classificação: Descrição"<br>em que a classificação corresponde às cadeias de caracteres na classificação.|
|  <ul><li>Defaultclassização<li>Escreva: Cadeia<li>Padrão: "" | A classificação a ser usada como a classificação padrão para um grupo se nenhuma tiver sido especificada.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Escreva: Cadeia<li>Padrão: "" | Cadeia de caracteres de um comprimento máximo de 64 caracteres que define a Convenção de nomenclatura configurada para grupos do Office 365. Para obter mais informações, consulte [impor uma política de nomenclatura para grupos do Office 365](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Escreva: Cadeia<li>Padrão: "" | Cadeia de caracteres separada por vírgulas que os usuários não terão permissão para usar em nomes de grupo ou aliases. Para obter mais informações, consulte [impor uma política de nomenclatura para grupos do Office 365](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Escreva: Booleano<li>Os For | Não usar
|  <ul><li>AllowGuestsToBeGroupOwner<li>Escreva: Booleano<li>Os False | Booliano que indica se um usuário convidado pode ser um proprietário de grupos. |
|  <ul><li>AllowGuestsToAccessGroups<li>Escreva: Booleano<li>Os True | Booliano que indica se um usuário convidado pode ou não ter acesso ao conteúdo de grupos do Office 365.  Essa configuração não requer uma licença Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Escreva: Cadeia<li>Padrão: "" | A URL de um link para as diretrizes de uso do convidado. |
|  <ul><li>AllowToAddGuests<li>Escreva: Booleano<li>Os True | Um booliano que indica se há permissão para adicionar convidados a este diretório.|
|  <ul><li>Classificação da<li>Escreva: Cadeia<li>Padrão: "" |Uma lista delimitada por vírgulas de valores de classificação válidos que podem ser aplicados a grupos do Office 365. |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Exemplo: Configurar a política de convidado para grupos no nível do diretório
1. Obtenha todos os modelos de configuração:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Para definir a política de convidado para grupos no nível do diretório, você precisa de grupo. modelo unificado
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Em seguida, crie um novo objeto de configurações com base nesse modelo:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Em seguida, atualize a configuração AllowToAddGuests
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Em seguida, aplique a configuração:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Você pode ler os valores usando:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Ler configurações no nível do diretório

Se você souber o nome da configuração que deseja recuperar, poderá usar o cmdlet abaixo para recuperar o valor das configurações atuais. Neste exemplo, estamos recuperando o valor de uma configuração chamada "UsageGuidelinesUrl". 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
Essas etapas lêem as configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Ler todas as configurações de diretório existentes:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Esse cmdlet retorna uma lista de todas as configurações de diretório:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Ler todas as configurações de um grupo específico:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Leia todos os valores de configurações de diretório de um objeto de configurações de diretório específico, usando o GUID de ID de configurações:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   Esse cmdlet retorna os nomes e valores neste objeto de configurações para este grupo específico:
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

## <a name="remove-settings-at-the-directory-level"></a>Remover configurações no nível do diretório
Essa etapa remove as configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Criar configurações para um grupo específico

1. Procure o modelo de configurações chamado "groups. Unified. Guest"
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
2. Recupere o objeto de modelo para o modelo grupos. Unified. Guest:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
   ```
3. Crie um novo objeto de configurações a partir do modelo:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Defina a configuração para o valor necessário:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. Obtenha a ID do grupo ao qual você deseja aplicar essa configuração:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Crie a nova configuração para o grupo necessário no diretório:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Para verificar as configurações, execute este comando:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Atualizar configurações para um grupo específico
1. Obtenha a ID do grupo cuja configuração você deseja atualizar:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Recupere a configuração do Grupo:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Atualize a configuração do grupo conforme necessário, por exemplo,
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. Em seguida, obtenha a ID da configuração para este grupo específico:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   Você receberá uma resposta semelhante a esta:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Em seguida, você pode definir o novo valor para essa configuração:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. Você pode ler o valor da configuração para certificar-se de que ele foi atualizado corretamente:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Referência de sintaxe de cmdlet
Você pode encontrar mais Azure Active Directory documentação do PowerShell em [Azure Active Directory cmdlets](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Leitura adicional

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
