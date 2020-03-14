---
title: Configure as definições de grupo utilizando powerShell - Azure AD / Microsoft Docs
description: Como gerir as configurações para grupos que usam cmdlets de Diretório Ativo Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90669ebde9537fdf597fccd621caa54deaed68a6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253133"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlets do Azure Active Directory para configurar definições de grupo

Este artigo contém instruções para utilizar o Diretório Ativo Azure (Azure AD) PowerShell cmdlets para criar e atualizar grupos. Este conteúdo aplica-se apenas aos grupos do Office 365 (por vezes chamados grupos unificados).

> [!IMPORTANT]
> Algumas configurações requerem uma licença Azure Ative Directory Premium P1. Para mais informações, consulte a tabela de definições do [Modelo.](#template-settings)

Para obter mais informações sobre como impedir que os utilizadores não administradores criem grupos de segurança, detete `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` conforme descrito em [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

As definições dos grupos do Office 365 são configuradas utilizando um objeto de definições e um objeto de definiçõesModelo. Inicialmente, não vê nenhum objeto de Definição no seu diretório, porque o seu diretório está configurado com as definições predefinidas. Para alterar as definições predefinidas, tem de criar um novo objeto de definições utilizando um modelo de definições. Os modelos de definições são definidos pela Microsoft. Existem vários modelos de configurações diferentes. Para configurar as definições de grupo do Office 365 para o seu diretório, utiliza o modelo denominado "Group.Uniified". Para configurar as definições de grupo do Office 365 num único grupo, utilize o modelo denominado "Group.Unified.Guest". Este modelo é usado para gerir o acesso dos hóspedes a um grupo do Office 365. 

Os cmdlets fazem parte do módulo PowerShell V2 do Diretório Ativo Azure. Para obter instruções sobre como descarregar e instalar o módulo no seu computador, consulte o artigo [Azure Ative Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/). Pode instalar a versão 2 do módulo a partir [da galeria PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-powershell-cmdlets"></a>Instalar cmdlets do PowerShell

Certifique-se de desinstalar qualquer versão mais antiga do Diretório Ativo Azure PowerShell para grafmodule para Windows PowerShell e [instale o PowerShell de Diretório Ativo Azure para graph - Lançamento de pré-visualização pública (mais de 2.0.0.137)](https://www.powershellgallery.com/packages/AzureADPreview) antes de executar os comandos PowerShell.

1. Abra a aplicação Windows PowerShell como administrador.
2. Desinstale qualquer versão anterior do AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. Instale a versão mais recente do AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>Criar configurações ao nível do diretório
Estes passos criam configurações a nível de diretório, que se aplicam a todos os grupos do Office 365 no diretório. O modelo de modelo get-AzureADDirectorySettingSetting está disponível apenas no módulo de [pré-visualização Da AD PowerShell Azure para gráfico](https://www.powershellgallery.com/packages/AzureADPreview).

1. Nos cmdlets de Definições de Diretório, deve especificar o ID do Modelo de Definições que pretende utilizar. Se não conhece este ID, este cmdlet devolve a lista de todos os modelos de definições:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   Esta chamada cmdlet devolve todos os modelos disponíveis:
  
   ``` PowerShell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Para adicionar um URL de linha de utilização, primeiro precisa de obter o objeto 'Definição Modelo' que define o valor URL da linha de utilização; ou seja, o modelo unificado do Grupo:
  
   ```powershell
   $TemplateId = (Get-AzureADDirectorySettingTemplate | where { $_.DisplayName -eq "Group.Unified" }).Id
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value $TemplateId -EQ
   ```
3. Em seguida, crie um novo objeto de definições com base nesse modelo:
  
   ```powershell
   $Setting = $Template.CreateDirectorySetting()
   ```  
4. Em seguida, atualize o valor da orientação de utilização:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Em seguida, aplique a definição:
  
   ```powershell
   New-AzureADDirectorySetting -DirectorySetting $Setting
   ```
6. Pode ler os valores usando:

   ```powershell
   $Setting.Values
   ```
   
## <a name="update-settings-at-the-directory-level"></a>Definições de atualização ao nível do diretório
Para atualizar o valor do UsageGuideLinesUrl no modelo de definição, leia as definições atuais a partir de Azure AD, caso contrário poderíamos acabar por sobrepor as definições existentes para além do UsageGuideLinesUrl.

1. Obtenha as definições atuais do Modelo de Definições Unificadas do Grupo:
   
   ```powershell
   $Setting = Get-AzureADDirectorySetting | ? { $_.DisplayName -eq "Group.Unified"}
   ```  
2. Verifique as definições atuais:
   
   ```powershell
   $Setting.Values
   ```
   
   Saída:
   ```powershell
    Name                          Value
    ----                          -----
    EnableMIPLabels               false
    CustomBlockedWordsList
    EnableMSStandardBlockedWords  False
    ClassificationDescriptions
    DefaultClassification
    PrefixSuffixNamingRequirement
    AllowGuestsToBeGroupOwner     False
    AllowGuestsToAccessGroups     True
    GuestUsageGuidelinesUrl
    GroupCreationAllowedGroupId
    AllowToAddGuests              True
    UsageGuidelinesUrl            https://guideline.example.com
    ClassificationList
    EnableGroupCreation           True
    ```
3. Para remover o valor do UsageGuideLinesUrl, edite o URL como uma cadeia vazia:
   
   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
4. Guarde a atualização para o diretório:
   
   ```powershell
   Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
   ```  

## <a name="template-settings"></a>Definições de modelo
Aqui estão as definições definidas no Modelo de Definições Unificadas do Grupo. Salvo indicação em contrário, estas funcionalidades requerem uma licença Azure Ative Directory Premium P1. 

| **Definição** | **Descrição** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Tipo: Boolean<li>Padrão: Verdadeiro |A bandeira indicando se a criação do grupo Office 365 é permitida no diretório por utilizadores não administradores. Esta definição não requer uma licença Azure Ative Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Tipo: Cadeia<li>Padrão: "" |GUID do grupo de segurança para o qual os membros são autorizados a criar grupos office 365 mesmo quando enableGroupCreation == falso. |
|  <ul><li>UsageGuidelinesUrl<li>Tipo: Cadeia<li>Padrão: "" |Um link para as Diretrizes de Utilização do Grupo. |
|  <ul><li>ClassificaçõesDescrições<li>Tipo: Cadeia<li>Padrão: "" | Uma lista de descrições de classificação delimitada. O valor das Descrições de Classificações só é válido neste formato:<br>$setting["Descrições de classificações"] ="Classificação:Descrição,Classificação:Descrição".<br>onde a classificação corresponde a uma entrada na Lista de Classificações.<br>Esta definição não se aplica quando enableMIPLabels == Verdadeiro.|
|  <ul><li>Classificação por incumprimento<li>Tipo: Cadeia<li>Padrão: "" | A classificação que deve ser usada como classificação padrão para um grupo se nenhuma foi especificada.<br>Esta definição não se aplica quando enableMIPLabels == Verdadeiro.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Tipo: Cadeia<li>Padrão: "" | Cadeia de um comprimento máximo de 64 caracteres que define a convenção de nomeação configurada para grupos do Office 365. Para mais informações, consulte Impor uma política de [nomeação para os grupos do Office 365](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Tipo: Cadeia<li>Padrão: "" | Série de frases separadas pela vírposta que os utilizadores não serão autorizados a usar em nomes de grupo ou pseudónimos. Para mais informações, consulte Impor uma política de [nomeação para os grupos do Office 365](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Tipo: Boolean<li>Padrão: "Falso" | Não utilizar
|  <ul><li>AllowGuestsToBeGroupOwner<li>Tipo: Boolean<li>Padrão: Falso | Boolean indicando se um utilizador convidado pode ou não ser proprietário de grupos. |
|  <ul><li>AllowGuestsToAccessGroups<li>Tipo: Boolean<li>Padrão: Verdadeiro | Boolean indicando se um utilizador convidado pode ou não ter acesso ao conteúdo dos grupos do Office 365.  Esta definição não requer uma licença Azure Ative Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Tipo: Cadeia<li>Padrão: "" | A url de um link para as diretrizes de utilização do hóspede. |
|  <ul><li>AllowToAddGuests<li>Tipo: Boolean<li>Padrão: Verdadeiro | Um boolean o que indica se é ou não permitido adicionar convidados a este diretório. <br>Esta definição pode ser ultrapassada e tornar-se apenas de leitura se a *EnableMIPLabels* for definida para *True* e uma política de hóspedes estiver associada ao rótulo de sensibilidade atribuído ao grupo. |
|  <ul><li>Lista de Classificações<li>Tipo: Cadeia<li>Padrão: "" | Uma lista de valores de classificação válidos que podem ser aplicadas aos Grupos 365 do Office. <br>Esta definição não se aplica quando enableMIPLabels == Verdadeiro.|
|  <ul><li>Rótulos EnableMIP<li>Tipo: Boolean<li>Padrão: "Falso" |A bandeira indicando se as etiquetas de sensibilidade publicadas no Microsoft 365 Compliance Center podem ser aplicadas aos Grupos do Office 365. Para mais informações, consulte etiquetas de sensibilidade para o [Office 365 grupos](groups-assign-sensitivity-labels.md). |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Exemplo: Configure a política de hóspedes para grupos ao nível do diretório
1. Obtenha todos os modelos de definição:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Para definir a política de hóspedes para grupos ao nível do diretório, você precisa de modelo de grupo.Unificado
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "62375ab9-6b52-47ed-826b-58e47e0e304b" -EQ
   ```
3. Em seguida, crie um novo objeto de definições com base nesse modelo:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Em seguida, atualizar a definição de AllowToAddGuests
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Em seguida, aplique a definição:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Pode ler os valores usando:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Ler as definições ao nível do diretório

Se souber o nome da definição que pretende recuperar, pode utilizar o cmdlet abaixo para recuperar o valor das definições atuais. Neste exemplo, estamos a recuperar o valor de uma definição chamada "UsageGuidelinesUrl". 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
Estes passos lêem as configurações ao nível do diretório, que se aplicam a todos os grupos de Escritórios do diretório.

1. Leia todas as definições de diretório existentes:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Este cmdlet devolve uma lista de todas as definições de diretório:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Leia todas as definições para um grupo específico:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Leia todos os valores de definições de diretório de um objeto de definições específicas de diretório, utilizando o ID GUID de Definições:
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

## <a name="remove-settings-at-the-directory-level"></a>Remover as definições ao nível do diretório
Este passo remove as definições ao nível do diretório, que se aplicam a todos os grupos de Escritórios no diretório.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Criar configurações para um grupo específico

1. Procure o modelo de definições chamado "Groups.Unified.Guest"
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
2. Recupere o objeto de modelo para o modelo de grupos.Unificado.Modelo de hóspedes:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "08d542b9-071f-4e16-94b0-74abb372e3d9" -EQ
   ```
3. Criar um novo objeto de definições a partir do modelo:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Definir a definição para o valor exigido:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. Obtenha a identificação do grupo a que pretende aplicar esta definição a:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Crie a nova definição para o grupo necessário no diretório:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Para verificar as definições, execute este comando:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Configurações de atualização para um grupo específico
1. Obtenha a identificação do grupo cuja definição pretende atualizar:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Recuperar a configuração do grupo:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Atualize a definição do grupo conforme necessário, por exemplo.
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. Em seguida, obtenha a identificação da configuração para este grupo específico:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   Terá uma resposta semelhante a esta:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Em seguida, pode definir o novo valor para esta definição:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. Pode ler o valor da definição para se certificar de que foi atualizado corretamente:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Referência da sintaxe cmdlet
Pode encontrar mais documentação do Diretório Ativo Azure PowerShell na [Azure Ative Directory Cmdlets](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Leitura adicional

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
