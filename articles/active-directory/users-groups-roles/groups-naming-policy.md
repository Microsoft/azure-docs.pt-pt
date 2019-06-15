---
title: Impor a política de nomes de grupo nos grupos do Office 365 - Azure Active Directory | Documentos da Microsoft
description: Como configurar a política de nomenclatura para grupos do Office 365 no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/06/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c13b95028975c5463217455c940bb84c3867899
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734792"
---
# <a name="enforce-a-naming-policy-on-office-365-groups-in-azure-active-directory"></a>Impor uma política de nomes em grupos do Office 365 no Azure Active Directory

Para impor as convenções de nomenclatura consistentes de grupos do Office 365 criados ou editados por seus usuários, configure um grupo de política de nomenclatura para os inquilinos no Azure Active Directory (Azure AD). Por exemplo, poderia usar a política de nomes para comunicar a função de um grupo, a associação, a região geográfica ou quem criou o grupo. Também pode utilizar a política de nomes para o ajudar a categorizar os grupos no livro de endereços. Pode utilizar a política para bloquear palavras específicas de que está a ser utilizado em nomes de grupos e aliases.

> [!IMPORTANT]
> A utilização de política de nomes do Azure AD para grupos do Office 365 requer que possui, mas não necessariamente atribuir uma licença do Azure Active Directory Premium P1 ou EDU do Azure AD básico para cada utilizador exclusivo que é um membro de um ou mais grupos do Office 365.

É aplicada a política de nomenclatura para criar ou editar grupos criados em cargas de trabalho (por exemplo, Outlook, Microsoft Teams, SharePoint, Exchange ou o planeador de implementações). Esta é aplicada para o nome do grupo e o alias de grupo. Se configurar a sua política de nomenclatura no Azure AD e tiver um política de nomes de grupos de Exchange existente, o Azure AD, atribuição de nomes de política é imposto na sua organização.

## <a name="naming-policy-features"></a>Recursos de diretiva de nomenclatura

Pode impor a política de nomes para grupos de duas formas diferentes:

- **Política de nomes de sufixo de prefixo** pode definir prefixos ou sufixos, em seguida, são adicionados automaticamente para impor uma convenção de nomenclatura nos seus grupos (por exemplo, no nome do grupo "GRP\_Japão\_meu grupo\_ Engenharia", GRP\_Japão\_ é o prefixo e \_engenharia é o sufixo). 

- **Custom bloqueado palavras** pode carregar um conjunto de palavras bloqueadas específicas para sua organização a ser bloqueados em grupos criados pelos utilizadores (por exemplo, "CEO, folhas de pagamento, RH").

### <a name="prefix-suffix-naming-policy"></a>Política de nomes de sufixo de prefixo

A estrutura geral da Convenção de nomenclatura é "Prefixo [GroupName] sufixo". Enquanto pode definir vários prefixos e sufixos, só pode ter uma instância do [GroupName] na definição. Os prefixos ou sufixos podem ser cadeias de caracteres fixas ou atributos de utilizador, tal como \[departamento\] que são substituídos com base no utilizador que está a criar o grupo. O número total de carateres para as prefixo e sufixo cadeias de caracteres combinadas permitido é de 53 carateres. 

Os prefixos e sufixos podem conter carateres especiais que são suportadas no nome do grupo e alias de grupo. Qualquer caractere no prefixo ou sufixo que não é suportada no alias de grupo ainda é aplicada no nome do grupo, mas removido o alias de grupo. Devido a esta restrição, os prefixos e sufixos aplicados para o nome do grupo podem ser diferentes das aplicadas para o alias de grupo. 

#### <a name="fixed-strings"></a>Cadeias de caracteres fixas

Pode usar cadeias de caracteres para facilitar a análise e a distinguir a grupos na lista de endereços global e nas ligações de navegação à esquerda das cargas de trabalho de grupo. Alguns dos prefixos de comuns são palavras-chave como "Grp\_Name', '\#nome ','\_nome"

#### <a name="user-attributes"></a>Atributos de utilizador

Pode usar os atributos que podem ajudá-lo e os seus utilizadores identificam quais departamento, o office ou a região geográfica para a qual o grupo foi criado. Por exemplo, se definir a política de nomenclatura como `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"`, e `User’s department = Engineering`, em seguida, um nome do grupo de imposto pode ser "GRP My Group Engineering." Suporte do Azure AD atributos são \[departamento\], \[empresa\], \[Office\], \[StateOrProvince\], \[CountryOrRegion \], \[Title\]. Atributos de utilizador não suportados são tratados como cadeias de caracteres fixas; Por exemplo, "\[postalCode\]". Atributos de extensão e os atributos personalizados não são suportados.

Recomendamos que utilize atributos que têm valores preenchidos para todos os utilizadores na sua organização e não utilizarem atributos que têm valores longos.

### <a name="custom-blocked-words"></a>Palavras bloqueadas personalizadas

Uma lista de palavra bloqueada é uma lista separada por vírgulas das expressões bloqueio em nomes de grupo e aliases. Não existem pesquisas de subcadeia são executadas. Uma correspondência exata entre o nome do grupo e um ou mais das palavras bloqueadas personalizadas é necessário para acionar uma falha. Não é efetuada a pesquisa de subcadeia, para que os utilizadores podem utilizar palavras comuns, como "Classe", mesmo que 'lass' é uma palavra bloqueada.

Regras da lista de palavra bloqueada:

- Palavras bloqueadas não são maiúsculas de minúsculas.
- Quando um utilizador introduzir uma palavra bloqueada como parte de um nome de grupo, verão uma mensagem de erro com a palavra bloqueada.
- Não existem não existem restrições de carateres no palavras bloqueadas.
- Existe um limite superior de 5000 expressões que podem ser configurados na lista de palavras bloqueadas. 

### <a name="administrator-override"></a>Substituição de administrador

Os administradores selecionados podem ser dispensados destas políticas, em todas as cargas de trabalho de grupo e os pontos finais, para que podem criar grupos com palavras bloqueadas e com suas próprias convenções de nomenclatura. Seguem-se a lista de funções de administrador excluídos do grupo de política de atribuição de nomes.

- Administrador global
- Parceiro de suporte de escalão 1
- Parceiro de suporte de camada 2
- Administrador de utilizadores
- Gravadores de diretórios

## <a name="configure-naming-policy-in-azure-portal"></a>Configurar política de nomes no portal do Azure

1. Inicie sessão para o [Centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta de administrador.
1. Selecione **grupos**, em seguida, selecione **política de nomenclatura** para abrir a página de política de nomenclatura.

    ![Abra a página de política de nomenclatura no Centro de administração](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Ver ou editar a política de nomenclatura de sufixo de prefixo

1. Sobre o **política de atribuição de nomes** página, selecione **nomenclatura de política de grupo**.
1. Pode ver ou editar o prefixo ou sufixo atual nomenclatura políticas individualmente ao selecionar os atributos ou cadeias de caracteres que deseja impor como parte da política de nomenclatura.
1. Para remover um prefixo ou sufixo na lista, selecione o prefixo ou sufixo, em seguida, selecione **eliminar**. Vários itens podem ser eliminados ao mesmo tempo.
1. Guardar as alterações para a nova política entrar em vigor, selecionando **guardar**.

### <a name="edit-custom-blocked-words"></a>Editar palavras bloqueadas personalizadas

1. Sobre o **política de atribuição de nomes** página, selecione **bloqueado palavras**.

    ![editar e carregar a lista de palavras bloqueadas para atribuir nomes a política](./media/groups-naming-policy/blockedwords.png)

1. Visualizar ou editar a lista atual de palavras bloqueadas personalizadas selecionando **transferir**.
1. Carregar a nova lista de palavras bloqueadas personalizadas ao selecionar o ícone de ficheiro.
1. Guardar as alterações para a nova política entrar em vigor, selecionando **guardar**.

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

   Se lhe for pedido sobre como acessar um repositório não fidedigno, introduza **Y**. Pode demorar alguns minutos a instalar o novo módulo.

## <a name="configure-naming-policy-in-powershell"></a>Configurar a política de nomes no PowerShell

1. Abra uma janela do Windows PowerShell no seu computador. Pode abri-lo sem privilégios elevados.

1. Execute os seguintes comandos para preparar a execução dos cmdlets.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   No ecrã **Iniciar sessão na sua conta** apresentado, introduza a conta de administrador e a palavra-passe para ligar ao serviço e selecione **Iniciar sessão**.

1. Siga os passos em [Cmdlets do Azure Active Directory para configurar definições de grupo](groups-settings-cmdlets.md) para criar definições de grupo para este inquilino.

### <a name="view-the-current-settings"></a>Ver as definições atuais

1. Obter a política de nomes atual para ver as definições atuais.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Apresente as definições atuais do grupo.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Defina a política de nomes e palavras bloqueadas personalizadas

1. Defina os prefixos e sufixos de nomes de grupo no Azure AD PowerShell. Para a funcionalidade funcione corretamente, [GroupName] têm de ser incluído na definição.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Defina as palavras bloqueadas personalizadas que quer restringir. O exemplo seguinte ilustra como pode adicionar as suas próprias palavras personalizadas.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Guarde as definições para a nova política tenha efeito, tal como no exemplo a seguir.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
Já está. Ter definido a política de nomes e adicionado sua palavras bloqueadas.

## <a name="export-or-import-custom-blocked-words"></a>Exportar ou importar palavras bloqueadas personalizadas

Para obter mais informações, consulte o artigo [cmdlets do Azure Active Directory para configurar definições de grupo](groups-settings-cmdlets.md).

Eis um exemplo de um script do PowerShell para exportar várias palavras bloqueadas:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Eis um exemplo de script do PowerShell para importar várias palavras bloqueadas:

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

## <a name="remove-the-naming-policy"></a>Remover a política de nomes

### <a name="remove-the-naming-policy-using-azure-portal"></a>Remover a política de nomes através do portal do Azure

1. Sobre o **política de atribuição de nomes** página, selecione **Eliminar política**.
1. Depois de confirmar a eliminação, a política de nomes for removida, incluindo todos os de sufixo de prefixo de nomenclatura de política e quaisquer palavras bloqueadas personalizadas.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Remover a política de nomes com o Azure AD PowerShell

1. Vazio os prefixos de nome de grupo e sufixos no Azure AD PowerShell.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Vazio as palavras bloqueadas personalizadas.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Guarde as definições.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-office-365-apps"></a>Experiência em aplicações do Office 365

Depois de definir uma política de nomes de grupo no Azure AD, quando um utilizador cria um grupo num aplicativo do Office 365, consulte:

- A pré-visualização do nome, de acordo com a política de nomes (com prefixos e sufixos) logo que o usuário digita no nome do grupo
- Se o usuário inserir palavras bloqueadas, verá uma mensagem de erro, de modo que podem remover as palavras bloqueadas.

Carga de trabalho | Conformidade
----------- | -------------------------------
Portais de Active Directory do Azure | Portal do Azure AD e portal do painel de acesso mostrarem o nome de política imposta nomenclatura quando o usuário digita um nome de grupo ao criar ou editar um grupo. Quando um usuário insere uma palavra bloqueada personalizada, é apresentada uma mensagem de erro com a palavra bloqueada para que o utilizador pode removê-lo.
Outlook Web Access (OWA) | Outlook Web Access mostra a política de nomes impostas nome quando o usuário digita um nome de grupo ou o alias de grupo. Quando um usuário insere uma palavra bloqueada personalizada, uma mensagem de erro é mostrada na interface do Usuário, juntamente com a palavra bloqueada, para que o utilizador pode removê-lo.
Outlook Desktop | Grupos criados no ambiente de trabalho do Outlook estão em conformidade com as definições de política de nomenclatura. Aplicação de ambiente de trabalho do Outlook ainda não mostra a pré-visualização do nome do grupo de imposto e não devolve os erros personalizados palavra bloqueada quando o usuário insere o nome do grupo. No entanto, a política de nomes é aplicada automaticamente quando criar ou editar um grupo e os utilizadores veem as mensagens de erro, se existirem palavras bloqueadas personalizadas no alias ou nome do grupo.
Microsoft Teams | Microsoft Teams mostra o nome da política imposta de nomes, quando o usuário insere um nome da equipa de grupos. Quando um usuário insere uma palavra bloqueada personalizada, uma mensagem de erro é apresentada juntamente com a palavra bloqueada para que o utilizador pode removê-lo.
SharePoint  |  SharePoint mostra o nome de política imposta nomenclatura quando o usuário digita um site dê um nome ou endereço de e-mail de grupo. Quando um usuário insere uma palavra bloqueada personalizada, uma mensagem de erro é apresentada, juntamente com a palavra bloqueada para que o utilizador pode removê-lo.
Microsoft Stream | Microsoft Stream mostra o nome da política imposta de nomes, quando o usuário digita um nome de grupo ou o alias de e-mail do grupo de grupos. Quando um usuário insere uma palavra bloqueada personalizada, uma mensagem de erro é mostrada com a palavra bloqueada para que o utilizador pode removê-lo.
IOS do Outlook e aplicações para Android | Grupos criados no Outlook aplicações estão em conformidade com a política de nomenclatura configurada. Aplicação móvel do Outlook ainda não mostra a pré-visualização do nome de política imposta nomenclatura e não devolve erros personalizados palavra bloqueada quando o usuário insere o nome do grupo. No entanto, a política de nomes é aplicada automaticamente ao clicar em criar/editar e os utilizadores veem as mensagens de erro, se existirem palavras bloqueadas personalizadas no alias ou nome do grupo.
Grupos de aplicações móveis | Grupos criados na aplicação móvel grupos estão em conformidade com a política de nomes. Grupos de aplicações móveis não mostram a pré-visualização da política de nomes e não devolvem erros personalizados palavra bloqueada quando o usuário insere o nome do grupo. Mas a política de nomes é aplicada automaticamente quando criar ou editar um grupo e é apresentada aos usuários erros apropriados se existirem palavras bloqueadas personalizadas no alias ou nome do grupo.
Planner | Planeador de implementações está em conformidade com a política de nomes. Planeador de implementações mostra a pré-visualização de política de nomenclatura ao introduzir o nome do plano. Quando um usuário insere uma palavra bloqueada personalizada, uma mensagem de erro é mostrada ao criar o plano.
Dynamics 365 for Customer Engagement | Dynamics 365 para envolvimento do cliente está em conformidade com a política de nomes. Dynamics 365 mostra o nome de política imposta nomenclatura quando o usuário digita um nome de grupo ou o alias de e-mail de grupo. Quando o usuário insere uma palavra bloqueada personalizada, uma mensagem de erro é mostrada com a palavra bloqueada para que o utilizador pode removê-lo.
School Data Sync (SDS) | Grupos criados através do SDS está em conformidade com a política de nomenclatura, mas a política de nomes não é aplicada automaticamente. Os administradores de SDS terá de acrescentar os prefixos e sufixos de nomes de classe para os quais grupos têm de ser criada e, em seguida, é carregado para o SDS. Criar grupo ou editar falharia caso contrário.
Outlook Customer Manager (OCM) | Outlook Customer Manager está em conformidade com a política de nomenclatura, que é aplicada automaticamente para o grupo criado no Outlook Customer Manager. Se for detetada uma palavra bloqueada personalizada, a criação de grupo no OCM está bloqueada e o utilizador está bloqueado de utilizar a aplicação OCM.
Aplicação de sala de aula | Grupos criados na aplicação de sala de aula está em conformidade com a política de nomenclatura, mas a política de nomes não é aplicada automaticamente e a nomenclatura pré-visualização de política não é mostrada aos utilizadores ao introduzir um nome de grupo de sala de aula. Os utilizadores tem de introduzir o nome do grupo de imposto em sala de aula com prefixos e sufixos. Caso contrário, o grupo de sala de aula criar ou editar a operação falhar com erros.
Power BI | Áreas de trabalho do Power BI estão em conformidade com a política de nomes.    
Yammer | Quando um utilizador tiver sessão iniciado no Yammer com uma conta do Azure Active Directory cria um grupo ou edita um nome de grupo, o nome do grupo está em conformidade com a política de atribuição de nomes. Isto aplica-se tanto para grupos do Office 365 ligado e todos os outros grupos do Yammer.<br>Se um grupo de acesso do Office 365 ligado tiver sido criado antes da política de nomes está em vigor, o nome do grupo não siga automaticamente as políticas de nomenclatura. Quando um utilizador edita o nome do grupo, ser-lhe pedido para adicionar o prefixo e sufixo.
StaffHub  | As equipes de StaffHub não siga a política de nomes, mas não de grupo do Office 365 subjacente. Nome da equipa de StaffHub não se aplica a prefixos e sufixos e não verifica se as palavras bloqueadas personalizadas. Mas StaffHub aplicam-se os prefixos e sufixos e remove palavras bloqueadas do grupo do Office 365 subjacente.
PowerShell do Exchange | Cmdlets do PowerShell do Exchange estão em conformidade com a política de nomes. Os utilizadores recebem mensagens de erro apropriada com prefixos sugeridos e sufixos de e para palavras bloqueadas personalizadas se eles não seguem a política de nomes no nome do grupo e alias de grupo (mailNickname).
Cmdlets do PowerShell do Active Directory do Azure | Cmdlets do PowerShell do Active Directory do Azure estão em conformidade com a política de atribuição de nomes. Os utilizadores recebem mensagens de erro apropriada com prefixos sugeridos e sufixos de e para palavras bloqueadas personalizadas se eles não seguem a Convenção de nomenclatura em nomes de grupos e alias de grupo.
Centro de administração do Exchange | Centro de administração do Exchange está em conformidade com a política de atribuição de nomes. Os utilizadores recebem mensagens de erro apropriada com prefixos sugeridos e sufixos de e para palavras bloqueadas personalizadas se eles não seguem a Convenção de nomenclatura no nome do grupo e alias de grupo.
Centro de administração do Microsoft 365 | Centro de administração do Microsoft 365 está em conformidade com a política de atribuição de nomes. Quando um utilizador cria ou edições de nomes de grupo, a política de nomes é aplicada automaticamente e os utilizadores recebem erros apropriados quando entram palavras bloqueadas personalizadas. O Centro de administração do Microsoft 365 ainda não mostra uma pré-visualização da política de nomes e não devolve erros personalizados palavra bloqueada quando o usuário insere o nome do grupo.

## <a name="next-steps"></a>Passos Seguintes

Estes artigos fornecem informações adicionais sobre os grupos do Azure AD.

- [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Política de expiração para grupos do Office 365](groups-lifecycle.md)
- [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir membros de um grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
