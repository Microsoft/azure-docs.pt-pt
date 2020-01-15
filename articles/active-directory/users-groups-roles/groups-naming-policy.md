---
title: Impor a política de nomenclatura de grupo no Azure Active Directory | Microsoft Docs
description: Como configurar a política de nomenclatura para grupos do Office 365 no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9136ce26f0070c8822292c741be59de537d3667
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941064"
---
# <a name="enforce-a-naming-policy-on-office-365-groups-in-azure-active-directory"></a>Impor uma política de nomenclatura em grupos do Office 365 no Azure Active Directory

Para impor convenções de nomenclatura consistentes para grupos do Office 365 criados ou editados por seus usuários, configure uma política de nomenclatura de grupo para seus locatários no Azure Active Directory (Azure AD). Por exemplo, você pode usar a política de nomenclatura para comunicar a função de um grupo, associação, região geográfica ou quem criou o grupo. Você também pode usar a política de nomenclatura para ajudar a categorizar grupos no catálogo de endereços. Você pode usar a política para impedir que palavras específicas sejam usadas em nomes de grupo e aliases.

> [!IMPORTANT]
> Usar a política de nomenclatura do Azure AD para grupos do Office 365 requer que você possua, mas não necessariamente, atribua uma licença Azure Active Directory Premium P1 ou Azure AD Basic licença EDU para cada usuário exclusivo que seja membro de um ou mais grupos do Office 365.

A política de nomenclatura é aplicada à criação ou edição de grupos criados entre cargas de trabalho (por exemplo, Outlook, Microsoft Teams, SharePoint, Exchange ou Planner). Ele é aplicado ao nome do grupo e ao alias do grupo. Se você configurar sua política de nomenclatura no Azure AD e tiver uma política de nomenclatura de grupo do Exchange existente, a política de nomenclatura do Azure AD será imposta em sua organização.

## <a name="naming-policy-features"></a>Recursos de política de nomenclatura

Você pode impor a política de nomenclatura para grupos de duas maneiras diferentes:

- **Política de nomenclatura de sufixo de prefixo** Você pode definir prefixos ou sufixos que são adicionados automaticamente para impor uma Convenção de nomenclatura em seus grupos (por exemplo, no nome do grupo "GRP\_o Japão\_meu grupo\_engenharia", GRP\_Japão\_ é o prefixo e \_engenharia é o sufixo). 

- **Palavras bloqueadas personalizadas** Você pode carregar um conjunto de palavras bloqueadas específicas para sua organização para serem bloqueadas em grupos criados por usuários (por exemplo, "CEO, Payroll, HR").

### <a name="prefix-suffix-naming-policy"></a>Política de nomenclatura de sufixo de prefixo

A estrutura geral da Convenção de nomenclatura é ' prefixo [GroupName] sufixo '. Embora seja possível definir vários prefixos e sufixos, você só pode ter uma instância do [GroupName] na configuração. Os prefixos ou sufixos podem ser cadeias de caracteres fixas ou atributos de usuário, como \[departamento\] que são substituídos com base no usuário que está criando o grupo. O número total permitido de caracteres para suas cadeias de prefixo e sufixo combinados é de 53 caracteres. 

Os prefixos e sufixos podem conter caracteres especiais com suporte no nome do grupo e no alias do grupo. Todos os caracteres no prefixo ou sufixo que não têm suporte no alias do grupo ainda são aplicados no nome do grupo, mas removidos do alias do grupo. Devido a essa restrição, os prefixos e sufixos aplicados ao nome do grupo podem ser diferentes daqueles aplicados ao alias do grupo. 

#### <a name="fixed-strings"></a>Cadeias de caracteres fixas

Você pode usar cadeias de caracteres para facilitar a verificação e a diferenciação de grupos na lista de endereços global e nos links de navegação à esquerda de cargas de trabalho de grupo. Alguns dos prefixos comuns são palavras-chave como ' GRP\_Name ', '\#Name ', '\_Name '

#### <a name="user-attributes"></a>Atributos de usuário

Você pode usar atributos que podem ajudá-lo e seus usuários a identificar qual departamento, escritório ou região geográfica para o qual o grupo foi criado. Por exemplo, se você definir sua política de nomenclatura como `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"`e `User’s department = Engineering`, um nome de grupo imposto poderá ser "GRP My Group Engineering". Os atributos do Azure AD com suporte são \[\]do departamento, \[\]da empresa, \[Office\], \[EstadoOuProvíncia\], \[Paísouregião\], \[título\]. Atributos de usuário sem suporte são tratados como cadeias de caracteres fixas; por exemplo, "\[postalCode\]". Não há suporte para atributos de extensão e atributos personalizados.

Recomendamos que você use atributos com valores preenchidos para todos os usuários em sua organização e não use atributos que tenham valores longos.

### <a name="custom-blocked-words"></a>Palavras bloqueadas personalizadas

Uma lista de palavras bloqueadas é uma lista separada por vírgulas de frases a serem bloqueadas em nomes de grupos e aliases. Nenhuma pesquisa de subcadeia de caracteres é executada. Uma correspondência exata entre o nome do grupo e uma ou mais palavras bloqueadas personalizadas é necessária para disparar uma falha. A pesquisa de subcadeia de caracteres não é executada para que os usuários possam usar palavras comuns como ' class ', mesmo se ' Lass ' for uma palavra bloqueada.

Regras de lista de palavras bloqueadas:

- As palavras bloqueadas não diferenciam maiúsculas de minúsculas.
- Quando um usuário insere uma palavra bloqueada como parte de um nome de grupo, ele vê uma mensagem de erro com a palavra bloqueada.
- Não há restrições de caracteres em palavras bloqueadas.
- Há um limite superior de 5000 frases que podem ser configuradas na lista de palavras bloqueadas. 

### <a name="roles-and-permissions"></a>Funções e permissões

Para configurar a política de nomenclatura, uma das funções seguir é necessária:
- Administrador global
- Administrador de grupo
- Administrador do usuário

Os administradores selecionados podem ser isentos dessas políticas, em todas as cargas de trabalho de grupo e pontos de extremidade, para que possam criar grupos usando palavras bloqueadas e com suas próprias convenções de nomenclatura. A seguir estão a lista de funções de administrador isentadas da política de nomenclatura de grupo.

- Administrador global
- Suporte da camada 1 do parceiro
- Suporte da camada 2 do parceiro
- Administrador do usuário
- Gravadores de diretório

## <a name="configure-naming-policy-in-azure-portal"></a>Configurar a política de nomenclatura no portal do Azure

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta de administrador de grupo.
1. Selecione **grupos**e, em seguida, selecione **política de nomenclatura** para abrir a página de política de nomenclatura.

    ![abrir a página de política de nomenclatura no centro de administração](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Exibir ou editar a política de nomenclatura de sufixo de prefixo

1. Na página **política de nomenclatura** , selecione **política de nomenclatura de grupo**.
1. Você pode exibir ou editar o prefixo atual ou as políticas de nomenclatura de sufixo individualmente selecionando os atributos ou as cadeias de caracteres que deseja impor como parte da política de nomenclatura.
1. Para remover um prefixo ou sufixo da lista, selecione o prefixo ou sufixo e, em seguida, selecione **excluir**. Vários itens podem ser excluídos ao mesmo tempo.
1. Salve as alterações da nova política para entrar em vigor selecionando **salvar**.

### <a name="edit-custom-blocked-words"></a>Editar palavras bloqueadas personalizadas

1. Na página **política de nomenclatura** , selecione **palavras bloqueadas**.

    ![editar e carregar a lista de palavras bloqueadas para a política de nomenclatura](./media/groups-naming-policy/blockedwords.png)

1. Exiba ou edite a lista atual de palavras bloqueadas personalizadas selecionando **baixar**.
1. Carregue a nova lista de palavras bloqueadas personalizadas selecionando o ícone arquivo.
1. Salve as alterações da nova política para entrar em vigor selecionando **salvar**.

## <a name="install-powershell-cmdlets"></a>Instalar cmdlets do PowerShell

Certifique-se de que desinstala qualquer versão anterior do módulo Azure Active Directory PowerShell para Graph para o Windows PowerShell e instala o [Azure Active Directory PowerShell para Graph - Versão de Pré-visualização Pública 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) antes de executar os comandos do PowerShell.

1. Abra a aplicação Windows PowerShell como administrador.
2. Desinstale qualquer versão anterior do AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   ```

3. Instale a versão mais recente do AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```

   Se você receber uma solicitação sobre como acessar um repositório não confiável, digite **Y**. Pode levar alguns minutos para que o novo módulo seja instalado.

## <a name="configure-naming-policy-in-powershell"></a>Configurar a política de nomenclatura no PowerShell

1. Abra uma janela do Windows PowerShell no seu computador. Você pode abri-lo sem privilégios elevados.

1. Execute os seguintes comandos para preparar a execução dos cmdlets.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   No ecrã **Iniciar sessão na sua conta** apresentado, introduza a conta de administrador e a palavra-passe para ligar ao serviço e selecione **Iniciar sessão**.

1. Siga os passos em [Cmdlets do Azure Active Directory para configurar definições de grupo](groups-settings-cmdlets.md) para criar definições de grupo para este inquilino.

### <a name="view-the-current-settings"></a>Exibir as configurações atuais

1. Busque a política de nomenclatura atual para exibir as configurações atuais.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Apresente as definições atuais do grupo.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Definir a política de nomenclatura e as palavras bloqueadas personalizadas

1. Defina os prefixos e sufixos de nomes de grupo no Azure AD PowerShell. Para que o recurso funcione corretamente, [GroupName] deve ser incluído na configuração.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Defina as palavras bloqueadas personalizadas que quer restringir. O exemplo seguinte ilustra como pode adicionar as suas próprias palavras personalizadas.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Salve as configurações da nova política para entrar em vigor, como no exemplo a seguir.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
E já está. Você definiu sua política de nomenclatura e adicionou suas palavras bloqueadas.

## <a name="export-or-import-custom-blocked-words"></a>Exportar ou importar palavras bloqueadas personalizadas

Para obter mais informações, consulte o artigo [Azure Active Directory cmdlets para definir configurações de grupo](groups-settings-cmdlets.md).

Aqui está um exemplo de um script do PowerShell para exportar várias palavras bloqueadas:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Aqui está um exemplo de script do PowerShell para importar várias palavras bloqueadas:

``` PowerShell
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
```

## <a name="remove-the-naming-policy"></a>Remover a política de nomenclatura

### <a name="remove-the-naming-policy-using-azure-portal"></a>Remover a política de nomenclatura usando portal do Azure

1. Na página **política de nomenclatura** , selecione **excluir política**.
1. Depois de confirmar a exclusão, a política de nomenclatura é removida, incluindo toda a política de nomenclatura de sufixo de prefixo e quaisquer palavras bloqueadas personalizadas.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Remover a política de nomenclatura usando o PowerShell do Azure AD

1. Esvazie os prefixos e sufixos de nome de grupo no PowerShell do Azure AD.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Esvazie as palavras bloqueadas personalizadas.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Guarde as definições.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-office-365-apps"></a>Experiência em aplicativos do Office 365

Depois de definir uma política de nomenclatura de grupo no Azure AD, quando um usuário cria um grupo em um aplicativo do Office 365, ele vê:

- Uma visualização do nome de acordo com sua política de nomenclatura (com prefixos e sufixos) assim que o usuário digitar o nome do grupo
- Se o usuário inserir palavras bloqueadas, eles verão uma mensagem de erro para que possam remover as palavras bloqueadas.

Carga de trabalho | Conformidade
----------- | -------------------------------
Portais Azure Active Directory | O portal do AD do Azure e o portal do painel de acesso mostram o nome imposto da política de nomenclatura quando o usuário digita um nome de grupo ao criar ou editar um grupo. Quando um usuário insere uma palavra bloqueada personalizada, uma mensagem de erro com a palavra bloqueada é exibida para que o usuário possa removê-la.
Outlook Web Access (OWA) | O Outlook Acesso via Web mostra o nome imposto da política de nomenclatura quando o usuário digita um nome de grupo ou alias de grupo. Quando um usuário insere uma palavra bloqueada personalizada, uma mensagem de erro é mostrada na interface do usuário junto com a palavra bloqueada para que o usuário possa removê-la.
Área de trabalho do Outlook | Os grupos criados no Outlook desktop são compatíveis com as configurações de política de nomenclatura. O aplicativo de desktop do Outlook ainda não mostra a visualização do nome do grupo imposto e não retorna os erros de palavras bloqueadas personalizadas quando o usuário insere o nome do grupo. No entanto, a política de nomenclatura é aplicada automaticamente ao criar ou editar um grupo, e os usuários veem mensagens de erro se houver palavras bloqueadas personalizadas no nome do grupo ou alias.
Microsoft Teams | O Microsoft Teams mostra o nome imposto da política de nomenclatura de grupo quando o usuário insere um nome de equipe. Quando um usuário insere uma palavra bloqueada personalizada, uma mensagem de erro é mostrada junto com a palavra bloqueada para que o usuário possa removê-la.
SharePoint  |  O SharePoint mostra o nome imposto da política de nomenclatura quando o usuário digita um nome de site ou endereço de email de grupo. Quando um usuário insere uma palavra bloqueada personalizada, uma mensagem de erro é mostrada, juntamente com a palavra bloqueada para que o usuário possa removê-la.
Microsoft Stream | Microsoft Stream mostra o nome imposto da política de nomenclatura de grupo quando o usuário digita um nome de grupo ou alias de email de grupo. Quando um usuário insere uma palavra bloqueada personalizada, uma mensagem de erro é mostrada com a palavra bloqueada para que o usuário possa removê-la.
Aplicativo Android e iOS do Outlook | Os grupos criados nos aplicativos do Outlook são compatíveis com a política de nomenclatura configurada. O aplicativo móvel do Outlook ainda não mostra a visualização do nome imposto da política de nomenclatura e não retorna erros de palavras bloqueadas personalizadas quando o usuário insere o nome do grupo. No entanto, a política de nomenclatura é aplicada automaticamente ao clicar em criar/editar e os usuários veem mensagens de erro se houver palavras bloqueadas personalizadas no nome do grupo ou alias.
Aplicativo móvel de grupos | Os grupos criados no aplicativo móvel de grupos são compatíveis com a política de nomenclatura. O aplicativo móvel de grupos não mostra a visualização da política de nomenclatura e não retorna erros de palavras bloqueadas personalizadas quando o usuário insere o nome do grupo. Mas a política de nomenclatura é aplicada automaticamente ao criar ou editar um grupo, e os usuários são apresentados com erros apropriados se houver palavras bloqueadas personalizadas no nome do grupo ou alias.
Planner | O Planner é compatível com a política de nomenclatura. O Planner mostra a visualização da política de nomenclatura ao inserir o nome do plano. Quando um usuário insere uma palavra bloqueada personalizada, uma mensagem de erro é mostrada durante a criação do plano.
Dynamics 365 for Customer Engagement | O Dynamics 365 for Customer Engagement é compatível com a política de nomenclatura. O Dynamics 365 mostra o nome imposto da política de nomenclatura quando o usuário digita um nome de grupo ou alias de email de grupo. Quando o usuário insere uma palavra bloqueada personalizada, uma mensagem de erro é mostrada com a palavra bloqueada para que o usuário possa removê-la.
SDS (estudante Data Sync) | Grupos criados por meio de SDS estão em conformidade com a política de nomenclatura, mas a política de nomenclatura não é aplicada automaticamente. Os administradores do SDS precisam acrescentar os prefixos e sufixos aos nomes de classe para os quais os grupos precisam ser criados e, em seguida, carregados em SDS. Caso contrário, a criação ou edição de grupo falhará.
Gerenciador de clientes do Outlook (OCM) | O Gerenciador de clientes do Outlook está em conformidade com a política de nomenclatura, que é aplicada automaticamente ao grupo criado no Gerenciador de clientes do Outlook. Se uma palavra bloqueada personalizada for detectada, a criação de grupo em OCM será bloqueada e o usuário será impedido de usar o aplicativo OCM.
Aplicativo de sala de aula | Os grupos criados no aplicativo de sala de aula estão em conformidade com a política de nomenclatura, mas a política de nomenclatura não é aplicada automaticamente e a visualização da política de nomenclatura não é mostrada aos usuários durante a inserção de um nome de grupo de sala de aula. Os usuários devem inserir o nome de grupo de sala de aula imposto com prefixos e sufixos. Caso contrário, a operação criar ou editar do grupo de salas de aula falhará com erros.
Power BI | Power BI espaços de trabalho são compatíveis com a política de nomenclatura.    
Yammer | Quando um usuário conectado ao Yammer com sua conta de Azure Active Directory cria um grupo ou edita um nome de grupo, o nome do grupo será compatível com a política de nomenclatura. Isso se aplica tanto aos grupos conectados do Office 365 quanto a todos os outros grupos do Yammer.<br>Se um grupo conectado do Office 365 tiver sido criado antes da política de nomenclatura estar em vigor, o nome do grupo não seguirá automaticamente as políticas de nomenclatura. Quando um usuário edita o nome do grupo, ele será solicitado a adicionar o prefixo e o sufixo.
StaffHub  | As equipes do StaffHub não seguem a política de nomenclatura, mas o grupo subjacente do Office 365. O nome da equipe do StaffHub não aplica os prefixos e sufixos e não verifica se há palavras bloqueadas personalizadas. Mas StaffHub aplica os prefixos e sufixos e remove as palavras bloqueadas do grupo do Office 365 subjacente.
PowerShell do Exchange | Os cmdlets do PowerShell do Exchange são compatíveis com a política de nomenclatura. Os usuários recebem mensagens de erro apropriadas com prefixos e sufixos sugeridos e palavras bloqueadas personalizadas se não seguirem a política de nomenclatura no nome do grupo e no alias do grupo (mailNickname).
Azure Active Directory cmdlets do PowerShell | Azure Active Directory cmdlets do PowerShell estão em conformidade com a política de nomenclatura. Os usuários recebem mensagens de erro apropriadas com prefixos e sufixos sugeridos e palavras bloqueadas personalizadas se não seguirem a Convenção de nomenclatura em nomes de grupo e alias de grupo.
Centro de administração do Exchange | O centro de administração do Exchange está em conformidade com a política de nomenclatura. Os usuários recebem mensagens de erro apropriadas com prefixos e sufixos sugeridos e palavras bloqueadas personalizadas se não seguirem a Convenção de nomenclatura no nome do grupo e no alias do grupo.
Microsoft 365 admin center (Centro de administração do Microsoft 365) | Microsoft 365 centro de administração está em conformidade com a política de nomenclatura. Quando um usuário cria ou edita nomes de grupo, a política de nomenclatura é aplicada automaticamente e os usuários recebem erros apropriados quando inserem palavras bloqueadas personalizadas. O centro de administração do Microsoft 365 ainda não mostra uma visualização da política de nomenclatura e não retorna erros de palavras bloqueadas personalizadas quando o usuário insere o nome do grupo.

## <a name="next-steps"></a>Passos seguintes

Esses artigos fornecem informações adicionais sobre grupos do Azure AD.

- [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Política de expiração para grupos do Office 365](groups-lifecycle.md)
- [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir membros de um grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
