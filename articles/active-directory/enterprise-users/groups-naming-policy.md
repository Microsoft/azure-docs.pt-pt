---
title: Impor a política de nomeação de grupos no Azure Ative Directory | Microsoft Docs
description: Como estabelecer uma política de nomeação para os grupos Microsoft 365 no Azure Ative Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00291907ddea75ba4507779d1226502dc8343aea
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365706"
---
# <a name="enforce-a-naming-policy-on-microsoft-365-groups-in-azure-active-directory"></a>Impor uma política de nomeação em grupos Microsoft 365 no Azure Ative Directory

Para impor convenções de nomeação consistentes para grupos Microsoft 365 criados ou editados pelos seus utilizadores, crie uma política de nomeação de grupo para as suas organizações no Azure Ative Directory (Azure AD). Por exemplo, você poderia usar a política de nomeação para comunicar a função de um grupo, membro, região geográfica, ou que criou o grupo. Você também pode usar a política de nomeação para ajudar a categorizar grupos no livro de endereços. Pode utilizar a política para impedir que palavras específicas sejam usadas em nomes de grupo e pseudónimos.

> [!IMPORTANT]
> A utilização da política de nomeação Azure AD para grupos Microsoft 365 requer que possua, mas não necessariamente, atribua uma licença Azure Ative Directory Premium P1 ou uma licença Azure AD Basic EDU para cada utilizador único que seja membro de um ou mais grupos Microsoft 365.

A política de nomeação é aplicada à criação ou edição de grupos criados através de cargas de trabalho (por exemplo, Outlook, Microsoft Teams, SharePoint, Exchange ou Planner). Aplica-se tanto ao nome do grupo como ao pseudónimo do grupo. Se configurar a sua política de nomeação em Azure AD e tiver uma política de nomeação de grupos de intercâmbio existente, a política de nomeação Azure AD é aplicada na sua organização.

Quando a política de nomeação do grupo estiver configurada, a política será aplicada aos novos grupos Microsoft 365 criados pelos utilizadores finais. A política de nomeação não se aplica a determinadas funções de diretório, tais como Administrador Global ou Administrador de Utilizador (consulte abaixo para a lista completa de funções isentas da política de nomeação de grupos). Para os grupos Microsoft 365 existentes, a política não será imediatamente aplicada no momento da configuração. Uma vez que o proprietário do grupo edite o nome de grupo para estes grupos, a política de nomeação será aplicada.

## <a name="naming-policy-features"></a>Características da política de nomeação

Pode impor a política de nomeação de grupos de duas maneiras diferentes:

- **Política de nomeação de prefixos-sufixos** Pode definir prefixos ou sufixos que são adicionados automaticamente para impor uma convenção de nomeação nos seus grupos (por exemplo, no nome de grupo "GRP \_ JAPAN \_ My Group \_ Engineering", GRP \_ JAPAN é o \_ prefixo, e \_ Engenharia é o sufixo). 

- **Palavras bloqueadas personalizadas** Pode carregar um conjunto de palavras bloqueadas específicas da sua organização para serem bloqueadas em grupos criados pelos utilizadores (por exemplo, "CEO, Payroll, HR").

### <a name="prefix-suffix-naming-policy"></a>Política de nomeação de prefixos-sufixos

A estrutura geral da convenção de nomeação é "Prefix[GroupName]Suffix". Embora possa definir vários prefixos e sufixos, só pode ter uma instância do [Nome do Grupo] na definição. Os prefixos ou sufixos podem ser cordas fixas ou atributos do utilizador, tais como \[ Departamento \] que são substituídos com base no utilizador que está a criar o grupo. O número total admissível de caracteres para o seu prefixo e cordas de sufixo, incluindo o nome de grupo, é de 53 caracteres. 

Prefixos e sufixos podem conter caracteres especiais que são suportados em nome de grupo e pseudónimos de grupo. Quaisquer caracteres no prefixo ou sufixo que não sejam suportados no pseudónimo do grupo ainda são aplicados no nome de grupo, mas removidos do pseudónimo do grupo. Devido a esta restrição, os prefixos e sufixos aplicados à nomeação de grupo podem ser diferentes dos aplicados ao pseudónimo do grupo. 

#### <a name="fixed-strings"></a>Cordas fixas

Pode utilizar cordas para facilitar a digitalização e diferenciar grupos na lista de endereços globais e nas ligações de navegação à esquerda das cargas de trabalho do grupo. Alguns dos prefixos comuns são palavras-chave como \_ 'Grp Name', \# 'Name', \_ 'Name'

#### <a name="user-attributes"></a>Atributos do utilizador

Pode utilizar atributos que podem ajudá-lo e aos seus utilizadores a identificar em que departamento, escritório ou região geográfica foi criado. Por exemplo, se definir a sua política de nomeação como `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"` , `User’s department = Engineering` e, então, um nome de grupo forçado pode ser "GRP My Group Engineering". Os atributos AD suportados são \[ Department , Company , Office , \] \[ \] \[ \] \[ StateOrProvince \] , \[ CountryOrRegion \] , Title \[ \] . Os atributos do utilizador não suportados são tratados como cordas fixas; por exemplo, " \[ postalCode \] ". Os atributos de extensão e os atributos personalizados não são suportados.

Recomendamos que utilize atributos que tenham valores preenchidos para todos os utilizadores da sua organização e não utilize atributos que tenham valores longos.

### <a name="custom-blocked-words"></a>Palavras bloqueadas personalizadas

Uma lista de palavras bloqueada é uma lista separada por vírgulas de frases a serem bloqueadas em nomes de grupo e pseudónimos. Não são realizadas pesquisas de sub-cordas. É necessária uma correspondência exata entre o nome de grupo e uma ou mais das palavras bloqueadas personalizadas para desencadear uma falha. A pesquisa de sub-cadeias não é realizada para que os utilizadores possam usar palavras comuns como 'Class' mesmo que 'lass' seja uma palavra bloqueada.

Regras da lista de palavras bloqueadas:

- Palavras bloqueadas não são sensíveis a casos.
- Quando um utilizador introduz uma palavra bloqueada como parte de um nome de grupo, vê uma mensagem de erro com a palavra bloqueada.
- Não há restrições de carácter em palavras bloqueadas.
- Há um limite superior de 5000 frases que podem ser configuradas na lista de palavras bloqueadas. 

### <a name="roles-and-permissions"></a>Funções e permissões

Para configurar a política de nomeação, é necessária uma das seguintes funções:
- Administrador global
- Administrador de grupo
- Escritor de diretório


Os administradores selecionados podem ser isentos destas políticas, em todas as cargas de trabalho do grupo e pontos finais, para que possam criar grupos usando palavras bloqueadas e com as suas próprias convenções de nomeação. Segue-se a lista das funções de administrador isentas da política de nomeação de grupos.

- Administrador global
- Administrador de utilizadores

## <a name="configure-naming-policy-in-azure-portal"></a>Configure a política de nomeação no portal Azure

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com uma conta de administrador do Grupo.
1. Selecione **Grupos,** em seguida, **selecione a política de nomeação** para abrir a página de política de nomeação.

    ![abrir a página de política de nomeação no centro de administração](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Ver ou editar a política de nomeação de prefixo-sufixo

1. Na página **de política de nomeação,** selecione **a política de nomeação do Grupo**.
1. Pode visualizar ou editar as políticas de nomeação de prefixo ou sufixo atuais individualmente selecionando os atributos ou cordas que pretende aplicar como parte da política de nomeação.
1. Para remover um prefixo ou sufixo da lista, selecione o prefixo ou sufixo e, em seguida, selecione **Delete**. Vários itens podem ser eliminados ao mesmo tempo.
1. Guarde as alterações para que a nova política entre em vigor selecionando **Save**.

### <a name="edit-custom-blocked-words"></a>Editar palavras bloqueadas personalizadas

1. Na página de **política de nomeação,** selecione **palavras bloqueadas**.

    ![editar e carregar lista de palavras bloqueadas para a política de nomeação](./media/groups-naming-policy/blockedwords.png)

1. Ver ou editar a lista atual de palavras bloqueadas personalizadas selecionando **Download**.
1. Faça o upload da nova lista de palavras bloqueadas personalizadas selecionando o ícone do ficheiro.
1. Guarde as alterações para que a nova política entre em vigor selecionando **Save**.

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

   Se você for solicitado sobre o acesso a um repositório não fidedvo, insira **Y**. Pode levar alguns minutos para o novo módulo instalar.

## <a name="configure-naming-policy-in-powershell"></a>Configure a política de nomeação na PowerShell

1. Abra uma janela Windows PowerShell no seu computador. Podes abri-la sem privilégios elevados.

1. Execute os seguintes comandos para preparar a execução dos cmdlets.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   No ecrã **Iniciar sessão na sua conta** apresentado, introduza a conta de administrador e a palavra-passe para ligar ao serviço e selecione **Iniciar sessão**.

1. Siga os passos em [cmdlets do Azure Ative Directory para configurar as definições de grupo](../enterprise-users/groups-settings-cmdlets.md) para criar configurações de grupo para esta organização.

### <a name="view-the-current-settings"></a>Ver as definições atuais

1. Consulte a atual política de nomeação para ver as definições atuais.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Apresente as definições atuais do grupo.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Desa parte a política de nomeação e palavras bloqueadas personalizadas

1. Defina os prefixos e sufixos de nomes de grupo no Azure AD PowerShell. Para que a função funcione corretamente, [Nome do Grupo] deve ser incluído na definição.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Defina as palavras bloqueadas personalizadas que quer restringir. O exemplo seguinte ilustra como pode adicionar as suas próprias palavras personalizadas.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Guarde as definições para que a nova política entre em vigor, como no exemplo seguinte.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
Já está. Definiu a sua política de nomeação e acrescentou as suas palavras bloqueadas.

## <a name="export-or-import-custom-blocked-words"></a>Exportar ou importar palavras bloqueadas personalizadas

Para obter mais informações, consulte o artigo [Cmdlets Azure Ative Directory para configurar as definições do grupo](../enterprise-users/groups-settings-cmdlets.md).

Aqui está um exemplo de um script PowerShell para exportar várias palavras bloqueadas:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Aqui está um exemplo do script PowerShell para importar várias palavras bloqueadas:

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

## <a name="remove-the-naming-policy"></a>Remover a política de nomeação

### <a name="remove-the-naming-policy-using-azure-portal"></a>Remova a política de nomeação usando o portal Azure

1. Na página de **política de naming,** selecione **Eliminar a política**.
1. Depois de confirmar a supressão, a política de nomeação é removida, incluindo toda a política de nomeação de prefixos e quaisquer palavras bloqueadas personalizadas.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Remova a política de nomeação utilizando a Azure AD PowerShell

1. Esvazie os prefixos e sufixos de nome de grupo em Azure AD PowerShell.
  
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

## <a name="experience-across-microsoft-365-apps"></a>Experimente em aplicações microsoft 365

Depois de definir uma política de nomeação de grupo em Azure AD, quando um utilizador cria um grupo numa aplicação microsoft 365, eles vêem:

- Uma pré-visualização do nome de acordo com a sua política de nomeação (com prefixos e sufixos) assim que os tipos de utilizador no nome de grupo
- Se o utilizador introduzir palavras bloqueadas, verá uma mensagem de erro para que possam remover as palavras bloqueadas.

Carga de trabalho | Conformidade
----------- | -------------------------------
Portais do Diretório Ativo Azure | O portal AD AZure e o portal Access Panel mostram o nome da política de nomeação quando o utilizador escreve um nome de grupo ao criar ou editar um grupo. Quando um utilizador introduz uma palavra bloqueada personalizada, é apresentada uma mensagem de erro com a palavra bloqueada para que o utilizador possa removê-la.
Outlook Web Access (OWA) | O Outlook Web Access mostra o nome da política de nomeação quando o utilizador escreve um nome de grupo ou um pseudónimo de grupo. Quando um utilizador introduz uma palavra bloqueada personalizada, é mostrada uma mensagem de erro na UI juntamente com a palavra bloqueada para que o utilizador possa removê-la.
Outlook Desktop | Os grupos criados no ambiente de trabalho do Outlook estão em conformidade com as definições de política de nomeação. A aplicação de desktop Outlook ainda não mostra a pré-visualização do nome de grupo forçado e não devolve os erros de palavra bloqueados personalizados quando o utilizador introduz o nome de grupo. No entanto, a política de nomeação é aplicada automaticamente ao criar ou editar um grupo, e os utilizadores vêem mensagens de erro se houver palavras bloqueadas personalizadas no nome ou pseudónimo do grupo.
Microsoft Stream | O Microsoft Teams mostra o nome da política de nomeação do grupo quando o utilizador introduz um nome de equipa. Quando um utilizador introduz uma palavra bloqueada personalizada, é mostrada uma mensagem de erro juntamente com a palavra bloqueada para que o utilizador possa removê-la.
SharePoint  |  O SharePoint mostra o nome da política de nomeação quando o utilizador escreve um nome de site ou endereço de e-mail de grupo. Quando um utilizador introduz uma palavra bloqueada personalizada, é mostrada uma mensagem de erro, juntamente com a palavra bloqueada para que o utilizador possa removê-la.
Microsoft Stream | O Microsoft Stream mostra o nome da política de nomeação do grupo quando o utilizador escreve um nome de grupo ou um pseudónimo de e-mail de grupo. Quando um utilizador introduz uma palavra bloqueada personalizada, é mostrada uma mensagem de erro com a palavra bloqueada para que o utilizador possa removê-la.
Outlook iOS e Aplicativo Android | Os grupos criados nas aplicações do Outlook estão em conformidade com a política de nomeação configurada. A aplicação móvel Outlook ainda não mostra a pré-visualização do nome da política de nomeação, e não devolve erros de palavras bloqueados personalizados quando o utilizador introduz o nome de grupo. No entanto, a política de nomeação é aplicada automaticamente ao clicar em criar/editar e os utilizadores vêem mensagens de erro se houver palavras bloqueadas personalizadas no nome ou pseudónimo do grupo.
Aplicação móvel de grupos | Os grupos criados na aplicação móvel Grupos estão em conformidade com a política de nomeação. A aplicação móvel de grupos não mostra a pré-visualização da política de nomeação e não devolve erros de palavras bloqueados personalizados quando o utilizador introduz o nome de grupo. Mas a política de nomeação é aplicada automaticamente ao criar ou editar um grupo e os utilizadores são apresentados com erros apropriados se houver palavras bloqueadas personalizadas no nome ou pseudónimo do grupo.
Planner | O planejador está em conformidade com a política de nomeação. O planejador mostra a pré-visualização da política de nomeação ao introduzir o nome do plano. Quando um utilizador introduz uma palavra bloqueada personalizada, é mostrada uma mensagem de erro ao criar o plano.
Dynamics 365 for Customer Engagement | A Dynamics 365 para o Envolvimento com o Cliente está em conformidade com a política de nomeação. A Dynamics 365 mostra o nome da política de nomeação quando o utilizador escreve um nome de grupo ou um pseudónimo de e-mail de grupo. Quando o utilizador introduz uma palavra bloqueada personalizada, é mostrada uma mensagem de erro com a palavra bloqueada para que o utilizador possa removê-la.
Sincronização de dados escolares (SDS) | Os grupos criados através das SDS cumprem a política de nomeação, mas a política de nomeação não é aplicada automaticamente. Os administradores da SDS têm de anexar os prefixos e sufixos aos nomes de classe para os quais os grupos precisam de ser criados e depois enviados para sDS. Criar ou editar em grupo falharia de outra forma.
Aplicativo de sala de aula | Os grupos criados na aplicação Classroom cumprem a política de nomeação, mas a política de nomeação não é aplicada automaticamente, e a pré-visualização da política de nomeação não é mostrada aos utilizadores enquanto inserimos um nome de grupo em sala de aula. Os utilizadores devem introduzir o nome de grupo de sala de aula forçado com prefixos e sufixos. Caso contrário, o grupo de aulas cria ou edita a operação falha com erros.
Power BI | Os espaços de trabalho power BI estão em conformidade com a política de nomeação.    
Yammer | Quando um utilizador se inscreveu na Yammer com a sua conta Azure Ative Directory cria um grupo ou edita um nome de grupo, o nome do grupo cumprirá a política de nomeação. Isto aplica-se tanto aos grupos ligados à Microsoft 365 como a todos os outros grupos Yammer.<br>Se um grupo ligado à Microsoft 365 foi criado antes da política de nomeação estar em vigor, o nome do grupo não seguirá automaticamente as políticas de nomeação. Quando um utilizador edita o nome de grupo, será solicitado que adicione o prefixo e o sufixo.
StaffHub  | As equipas do StaffHub não seguem a política de nomeação, mas o grupo subjacente à Microsoft 365 segue. O nome da equipa do StaffHub não aplica os prefixos e sufixos e não verifica se há palavras bloqueadas personalizadas. Mas o StaffHub aplica os prefixos e sufixos e remove palavras bloqueadas do grupo Microsoft 365 subjacente.
Bolsa powershell | Os cmdlets De Câmbio PowerShell estão em conformidade com a política de nomeação. Os utilizadores recebem mensagens de erro apropriadas com prefixos e sufixos sugeridos e para palavras bloqueadas personalizadas se não seguirem a política de nomeação no nome do grupo e no pseudónimo do grupo (mailNickname).
Cmdlets powershell do diretório ativo Azure | Os cmdlets powershell do Diretório Ativo Azure estão em conformidade com a política de nomeação. Os utilizadores recebem mensagens de erro apropriadas com prefixos e sufixos sugeridos e para palavras bloqueadas personalizadas se não seguirem a convenção de nomeação em nomes de grupo e pseudónimos de grupo.
Centro de Administração de Intercâmbio | O centro de administração de intercâmbio está em conformidade com a política de nomeação. Os utilizadores recebem mensagens de erro apropriadas com prefixos e sufixos sugeridos e para palavras bloqueadas personalizadas se não seguirem a convenção de nomeação no nome de grupo e pseudónimos de grupo.
Microsoft 365 admin center (Centro de administração do Microsoft 365) | O Centro de Administração Microsoft 365 está em conformidade com a política de nomeação. Quando um utilizador cria ou edita nomes de grupo, a política de nomeação é aplicada automaticamente e os utilizadores recebem erros adequados quando introduzem palavras bloqueadas personalizadas. O centro de administração microsoft 365 ainda não mostra uma pré-visualização da política de nomeação e não devolve erros de palavras bloqueados personalizados quando o utilizador introduz o nome de grupo.

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais sobre os grupos AZURE AD.

- [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Política de expiração para grupos Microsoft 365](groups-lifecycle.md)
- [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir membros de um grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
