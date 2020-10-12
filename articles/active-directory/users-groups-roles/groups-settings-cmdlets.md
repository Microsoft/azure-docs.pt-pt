---
title: Configurar configurações de grupo usando PowerShell - Azure AD / Microsoft Docs
description: Como gerir as definições para grupos que utilizam cmdlets do Azure Ative Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63f0c55823899be8eb4146860787aede2cd2d6b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797909"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlets do Azure Active Directory para configurar definições de grupo

Este artigo contém instruções para a utilização de cmdlets PowerShell (Azure AD) para criar e atualizar grupos. Este conteúdo aplica-se apenas aos grupos Microsoft 365 (por vezes chamados grupos unificados).

> [!IMPORTANT]
> Algumas configurações requerem uma licença Azure Ative Directory Premium P1. Para obter mais informações, consulte a tabela [de definições do modelo.](#template-settings)

Para obter mais informações sobre como evitar que os utilizadores não administradores criem grupos de segurança,  `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` definidos como descrito em [Set-MSOLCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

As definições de grupos da Microsoft 365 são configuradas utilizando um objeto Definições e um objeto SetsTemplate. Inicialmente, não vê nenhum objeto de Definições no seu diretório, porque o seu diretório está configurado com as definições predefinidas. Para alterar as definições predefinidos, tem de criar um novo objeto de definições utilizando um modelo de definições. Os modelos de definições são definidos pela Microsoft. Existem vários modelos de configurações diferentes. Para configurar as definições do grupo Microsoft 365 para o seu diretório, utilize o modelo denominado "Group.Unified". Para configurar as definições do grupo Microsoft 365 num único grupo, utilize o modelo denominado "Group.Unified.Guest". Este modelo é usado para gerir o acesso dos hóspedes a um grupo Microsoft 365. 

Os cmdlets fazem parte do módulo PowerShell V2 do Diretório Ativo Azure. Para obter instruções sobre como descarregar e instalar o módulo no seu computador, consulte o artigo [Azure Ative Directory PowerShell Version 2](/powershell/azure/active-directory/overview). Pode instalar a versão 2 do módulo a partir [da galeria PowerShell.](https://www.powershellgallery.com/packages/AzureAD/)

## <a name="install-powershell-cmdlets"></a>Instalar cmdlets do PowerShell

Certifique-se de desinstalar qualquer versão mais antiga do PowerShell do Diretório Ativo Azure para o Módulo de Gráfico para Windows PowerShell e instale [o PowerShell do Diretor Ativo Azure para o Graph - Public Preview Release (mais de 2.0.0.137)](https://www.powershellgallery.com/packages/AzureADPreview) antes de executar os comandos PowerShell.

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
   
## <a name="create-settings-at-the-directory-level"></a>Criar definições ao nível do diretório
Estes passos criam configurações ao nível do diretório, que se aplicam a todos os grupos Microsoft 365 no diretório. O Get-AzureADDirectorySettingTemplate cmdlet só está disponível no [módulo de pré-visualização Azure AD PowerShell para gráfico](https://www.powershellgallery.com/packages/AzureADPreview).

1. Nos cmdlets de definições de diretórios, deve especificar o ID do SetsTemplate que pretende utilizar. Se não conhece este ID, este cmdlet devolve a lista de todos os modelos de definições:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   Esta chamada de cmdlet devolve todos os modelos disponíveis:
  
   ``` PowerShell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Microsoft 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Para adicionar um URL de orientação de utilização, primeiro precisa de obter o objeto SettingsTemplate que define o valor URL da orientação de utilização; isto é, o modelo grupo.Unificado:
  
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
6. Pode ler os valores utilizando:

   ```powershell
   $Setting.Values
   ```
   
## <a name="update-settings-at-the-directory-level"></a>Atualizar definições ao nível do diretório
Para atualizar o valor para UsageGuideLinesUrl no modelo de definição, leia as definições atuais do AD AD, caso contrário poderíamos acabar por sobrepor as definições existentes para além do UsageGuideLinesUrl.

1. Obtenha as definições atuais do Grupo.Configurações UnificadasTemplate:
   
   ```powershell
   $Setting = Get-AzureADDirectorySetting | ? { $_.DisplayName -eq "Group.Unified"}
   ```  
2. Verifique as definições atuais:
   
   ```powershell
   $Setting.Values
   ```
   
   Resultado:
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
3. Para remover o valor de UsageGuideLinesUrl, edite o URL para ser uma cadeia vazia:
   
   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
4. Guarde a atualização do diretório:
   
   ```powershell
   Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
   ```  

## <a name="template-settings"></a>Configurações do modelo
Aqui estão as definições definidas no Grupo.Configurações UnificadasTemplate. Salvo indicação em contrário, estas funcionalidades requerem uma licença Azure Ative Directory Premium P1. 

| **Definição** | **Descrição** |
| --- | --- |
|  <ul><li>Ativar a Criação do Grupo<li>Tipo: Boolean<li>Padrão: Verdadeiro |A bandeira indica se a criação do grupo Microsoft 365 é permitida no diretório por utilizadores não administrativos. Esta definição não requer uma licença Azure Ative Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Tipo: Cadeia<li>Predefinição: "" |GUIA do grupo de segurança para o qual os membros podem criar grupos Microsoft 365 mesmo quando EnableGroupCreation == falso. |
|  <ul><li>UsageGuidelinesUrl<li>Tipo: Cadeia<li>Predefinição: "" |Uma ligação com as Diretrizes de Utilização do Grupo. |
|  <ul><li>ClassificaçãoDescriminações<li>Tipo: Cadeia<li>Predefinição: "" | Uma lista de descrições de classificação delimitadas por vírgulas. O valor das Classificações Descrições só é válido neste formato:<br>$setting["Classificação Descrições"] ="Classificação:Descrição,Classificação:Descrição"<br>onde a classificação corresponde a uma entrada na Lista de Classificação.<br>Esta definição não se aplica quando ativar o EnableMIPLabels == Verdadeiro.|
|  <ul><li>PadrãoClassificação<li>Tipo: Cadeia<li>Predefinição: "" | A classificação que deve ser usada como a classificação padrão para um grupo se nenhuma for especificada.<br>Esta definição não se aplica quando ativar o EnableMIPLabels == Verdadeiro.|
|  <ul><li>PrefixOs sufixoNamingRequirement<li>Tipo: Cadeia<li>Predefinição: "" | Cadeia de um comprimento máximo de 64 caracteres que define a convenção de nomeação configurada para grupos Microsoft 365. Para obter mais informações, consulte [Impor uma política de nomeação para os grupos Microsoft 365](groups-naming-policy.md). |
| <ul><li>Lista de Palavras Bloqueadas Personalizadas<li>Tipo: Cadeia<li>Predefinição: "" | Cadeia de frases separadas por vírgula que os utilizadores não serão autorizados a usar em nomes de grupo ou pseudónimos. Para obter mais informações, consulte [Impor uma política de nomeação para os grupos Microsoft 365](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlocked Palavras<li>Tipo: Boolean<li>Padrão: "Falso" | Não utilizar
|  <ul><li>AllowGuestsToBeGroupOwner<li>Tipo: Boolean<li>Padrão: Falso | Boolean indicando se um utilizador convidado pode ou não ser proprietário de grupos. |
|  <ul><li>AllowGuestsToAccessGroups<li>Tipo: Boolean<li>Padrão: Verdadeiro | Boolean indicando se um utilizador convidado pode ou não ter acesso ao conteúdo de grupos microsoft 365.  Esta definição não requer uma licença Azure Ative Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Tipo: Cadeia<li>Predefinição: "" | O url de um link para as diretrizes de utilização do hóspede. |
|  <ul><li>PermitirToAddGuests<li>Tipo: Boolean<li>Padrão: Verdadeiro | Um booleano indicando se é ou não permitido adicionar convidados a este diretório. <br>Esta definição pode ser ultrapassada e tornar-se apenas de leitura se *o EnableMIPLabels* estiver definido para *True* e uma política de hóspedes estiver associada à etiqueta de sensibilidade atribuída ao grupo.<br>Se a definição AllowToAddGuests for definida como Falsa a nível da organização, qualquer definição allowToAddGuests ao nível de grupo é ignorada. Se deseja ativar o acesso dos hóspedes a apenas alguns grupos, tem de definir AllowToAddGuests para ser verdadeiro a nível da organização e, em seguida, desativá-lo seletivamente para grupos específicos. |
|  <ul><li>Lista de Classificação<li>Tipo: Cadeia<li>Predefinição: "" | Uma lista delimitada por vírgula de valores de classificação válidos que pode ser aplicada aos grupos Microsoft 365. <br>Esta definição não se aplica quando ativar o EnableMIPLabels == Verdadeiro.|
|  <ul><li>EnableMIPLabels<li>Tipo: Boolean<li>Padrão: "Falso" |A bandeira indica se as etiquetas de sensibilidade publicadas no Microsoft 365 Compliance Center podem ser aplicadas a grupos Microsoft 365. Para obter mais informações, consulte [atribuir etiquetas de sensibilidade para grupos Microsoft 365](groups-assign-sensitivity-labels.md). |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Exemplo: Configurar a política do Convidado para grupos ao nível do diretório
1. Obtenha todos os modelos de definição:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Para definir a política de hóspedes para grupos ao nível do diretório, você precisa do grupo.Modelo unificado
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "62375ab9-6b52-47ed-826b-58e47e0e304b" -EQ
   ```
3. Em seguida, crie um novo objeto de definições com base nesse modelo:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Em seguida, atualize a definição de AllowToAddGuests
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Em seguida, aplique a definição:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Pode ler os valores utilizando:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Leia as definições ao nível do diretório

Se souber o nome da definição que pretende recuperar, pode utilizar o cmdlet abaixo para recuperar o valor das definições atuais. Neste exemplo, estamos a recuperar o valor para uma definição chamada "UsageGuidelinesUrl". 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
Estes passos lêem as definições ao nível do diretório, que se aplicam a todos os grupos de Escritórios do diretório.

1. Leia todas as definições de diretório existentes:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Este cmdlet devolve uma lista de todas as definições do diretório:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Leia todas as definições para um grupo específico:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Leia todos os valores de definições de diretório de um objeto específico de definições de diretório, utilizando definições ID GUID:
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

## <a name="remove-settings-at-the-directory-level"></a>Remova as definições ao nível do diretório
Este passo remove as definições ao nível do diretório, que se aplicam a todos os grupos de Escritórios no diretório.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Criar definições para um grupo específico

1. Procure o modelo de definições chamado "Groups.Unified.Guest"
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Microsoft 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Recupere o objeto do modelo para o modelo Grupos.Unificado.Guest):
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "08d542b9-071f-4e16-94b0-74abb372e3d9" -EQ
   ```
3. Crie um novo objeto de definições a partir do modelo:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Defina a definição para o valor exigido:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. Obtenha a ID do grupo que pretende aplicar esta definição para:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Criar a nova definição para o grupo requerido no diretório:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Para verificar as definições, execute este comando:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Definições de atualização para um grupo específico
1. Obtenha o ID do grupo cuja definição pretende atualizar:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Recupere a configuração do grupo:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Atualize a configuração do grupo conforme necessário, por exemplo.
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. Em seguida, obtenha a iD da configuração para este grupo específico:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   Obterá uma resposta semelhante a esta:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Em seguida, pode definir o novo valor para esta definição:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. Pode ler o valor da definição para se certificar de que foi corretamente atualizado:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Referência da sintaxe do Cmdlet
Pode encontrar mais documentação do Azure Ative Directory PowerShell na [Azure Ative Directory Cmdlets](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Leitura adicional

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)