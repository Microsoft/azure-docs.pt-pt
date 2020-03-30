---
title: Impor política de nomeação de grupo sintetizando no Diretório Ativo azure [ Microsoft Docs
description: Como criar a política de nomeação para os grupos do Office 365 no Diretório Ativo azure
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
ms.openlocfilehash: 141e83e21db18f21468113fd9927c2bdd2ed176d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497873"
---
# <a name="enforce-a-naming-policy-on-office-365-groups-in-azure-active-directory"></a>Impor uma política de nomeação dos grupos do Office 365 no Diretório Ativo de Azure

Para impor convenções de nomeação consistentes para os grupos office 365 criados ou editados pelos seus utilizadores, criar uma política de nomeação de grupo para os seus inquilinos em Azure Ative Directory (Azure AD). Por exemplo, poderia usar a política de nomeação para comunicar a função de um grupo, membro, região geográfica ou que criou o grupo. Também poderia usar a política de nomeação para ajudar a categorizar grupos no livro de endereços. Pode utilizar a política para impedir que palavras específicas sejam usadas em nomes de grupos e pseudónimos.

> [!IMPORTANT]
> A utilização da política de nomeação da Azure AD para os grupos Office 365 requer que possua, mas não necessariamente atribua uma licença Azure Ative Directory Premium P1 ou licença Azure AD Basic EDU para cada utilizador único que seja membro de um ou mais grupos do Office 365.

A política de nomeação é aplicada à criação ou edição de grupos criados em cargas de trabalho (por exemplo, Outlook, Microsoft Teams, SharePoint, Exchange ou Planner). Aplica-se tanto ao nome do grupo como ao pseudónimo do grupo. Se configurar a sua política de nomeação em Azure AD e tiver uma política de nomeação de grupo de intercâmbio existente, a política de nomeação da AD Azure é aplicada na sua organização.

Quando a política de nomeação em grupo estiver configurada, a política será aplicada aos novos grupos do Office 365 criados pelos utilizadores finais. A política de nomeação não se aplica a determinadas funções de diretório, tais como Administrador Global ou Administrador de Utilizador (consulte abaixo a lista completa de funções isentas da política de nomeação do grupo). Para os grupos existentes do Office 365, a política não se aplicará imediatamente no momento da configuração. Assim que o dono do grupo edita o nome de grupo para estes grupos, a política de nomeação será aplicada.

## <a name="naming-policy-features"></a>Características políticas de nomeação

Pode impor a política de nomeação de grupos de duas maneiras diferentes:

- **Política de nomeação prefixo-sufixo** Pode definir prefixos ou sufixos que são adicionados automaticamente para impor uma convenção de nomeação nos seus grupos (por exemplo, no nome de grupo "GRP\_\_JAPAN My\_Group Engineering", GRP\_JAPAN\_ é o prefixo, e \_engenharia é o sufixo). 

- **Palavras bloqueadas personalizadas** Pode fazer o upload de um conjunto de palavras bloqueadas específicas da sua organização para serem bloqueadas em grupos criados pelos utilizadores (por exemplo, "CEO, Payroll, HR").

### <a name="prefix-suffix-naming-policy"></a>Política de nomeação prefixo-sufixo

A estrutura geral da convenção de nomeação é "Prefixo[Nome de Grupo]Sufixo". Embora possa definir vários prefixos e sufixos, só pode ter uma instância do [Nome do Grupo] na definição. Os prefixos ou sufixos podem ser cordas fixas ou atributos de utilizador, tais como \[O Departamento\] que são substituídos com base no utilizador que está a criar o grupo. O número total admissível de caracteres para as cordas de prefixo e sufixo combinados é de 53 caracteres. 

Prefixos e sufixos podem conter caracteres especiais que são suportados em nome de grupo e pseudónimo de grupo. Quaisquer caracteres no prefixo ou sufixo que não sejam suportados no pseudónimo do grupo ainda são aplicados no nome do grupo, mas removidos do pseudónimo do grupo. Devido a esta restrição, os prefixos e sufixos aplicados ao nome de grupo podem ser diferentes dos aplicados ao pseudónimo do grupo. 

#### <a name="fixed-strings"></a>Cordas fixas

Pode utilizar cordas para facilitar a digitalização e diferenciação de grupos na lista de endereços globais e nas ligações de navegação à esquerda das cargas de trabalho em grupo. Alguns dos prefixos comuns são\_palavras-chave\#como 'Grp Name', 'Nome', '\_Nome'

#### <a name="user-attributes"></a>Atributos do utilizador

Pode utilizar atributos que o podem ajudar e aos seus utilizadores a identificar em que departamento, escritório ou região geográfica o grupo foi criado. Por exemplo, se definir a `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"`sua `User’s department = Engineering`política de nomeação como , e então um nome de grupo forçado pode ser "GRP My Group Engineering". Os atributos atributos AD \[\]Azure suportados \[são \[\]Department \[\] \[\], Company\], Office , \[StateOrProvince,\]CountryOrRegion , Title . Os atributos não suportados do utilizador são tratados como cordas fixas; por exemplo",\["Código\]postal". Os atributos de extensão e os atributos personalizados não são suportados.

Recomendamos que utilize atributos que tenham valores preenchidos para todos os utilizadores da sua organização e não utilize atributos que tenham valores longos.

### <a name="custom-blocked-words"></a>Palavras bloqueadas personalizadas

Uma lista de palavras bloqueada é uma lista separada de vírpostas de frases a serem bloqueadas em nomes de grupos e pseudónimos. Não são realizadas buscas de sub-cordas. Uma correspondência exata entre o nome do grupo e uma ou mais palavras bloqueadas personalizadas é necessária para desencadear uma falha. A pesquisa de sub-cordas não é realizada para que os utilizadores possam usar palavras comuns como "Class" mesmo que 'las' seja uma palavra bloqueada.

Regras de lista de palavras bloqueadas:

- Palavras bloqueadas não são sensíveis ao caso.
- Quando um utilizador introduz uma palavra bloqueada como parte de um nome de grupo, vê uma mensagem de erro com a palavra bloqueada.
- Não existem restrições de carácter em palavras bloqueadas.
- Há um limite superior de 5000 frases que podem ser configuradas na lista de palavras bloqueadas. 

### <a name="roles-and-permissions"></a>Funções e permissões

Para configurar a política de nomeação, é necessária uma das funções de abrandamento:
- Administrador global
- Administrador do grupo
- Administrador de utilizadores

Os administradores selecionados podem ser isentos destas políticas, em todas as cargas de trabalho e pontos finais do grupo, para que possam criar grupos usando palavras bloqueadas e com as suas próprias convenções de nomeação. Seguem-se a lista das funções de administrador isentas da política de nomeação do grupo.

- Administrador global
- Suporte de nível 1 do parceiro
- Suporte de nível parceiro 2
- Administrador de utilizadores
- Escritores de diretórios

## <a name="configure-naming-policy-in-azure-portal"></a>Configure política de nomeação no portal Azure

1. Inscreva-se no [centro de administração da Azure AD](https://aad.portal.azure.com) com uma conta de administrador do Grupo.
1. Selecione **Grupos**, em seguida, selecione **a política de nomeação** para abrir a página de política de Nomeação.

    ![abrir a página política de nomeação no centro de administração](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Ver ou editar a política de nomeação prefixo-sufixo

1. Na página política de **Naming,** selecione Política de **nomeação do Grupo**.
1. Pode visualizar ou editar as políticas de nomeação de prefixo ou sufixo sufixo sufixo sufixo sufixo individualmente, selecionando os atributos ou cordas que pretende aplicar como parte da política de nomeação.
1. Para remover um prefixo ou sufixo da lista, selecione o prefixo ou o sufixo e, em seguida, **selecione Delete**. Vários itens podem ser eliminados ao mesmo tempo.
1. Guarde as suas alterações para que a nova política entre em vigor selecionando **Save**.

### <a name="edit-custom-blocked-words"></a>Editar palavras bloqueadas personalizadas

1. Na página política de **Naming,** selecione **palavras bloqueadas**.

    ![editar e carregar lista de palavras bloqueadas para a política de nomeação](./media/groups-naming-policy/blockedwords.png)

1. Ver ou editar a lista atual de palavras bloqueadas personalizadas selecionando **Download**.
1. Faça upload da nova lista de palavras bloqueadas personalizadas selecionando o ícone do ficheiro.
1. Guarde as suas alterações para que a nova política entre em vigor selecionando **Save**.

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

   Se for solicitado a aceder a um repositório não confiável, insira **Y**. Pode levar alguns minutos para o novo módulo instalar.

## <a name="configure-naming-policy-in-powershell"></a>Configure a política de nomeação no PowerShell

1. Abra uma janela Do Windows PowerShell no seu computador. Pode abri-la sem privilégios elevados.

1. Execute os seguintes comandos para preparar a execução dos cmdlets.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   No ecrã **Iniciar sessão na sua conta** apresentado, introduza a conta de administrador e a palavra-passe para ligar ao serviço e selecione **Iniciar sessão**.

1. Siga os passos em [Cmdlets do Azure Active Directory para configurar definições de grupo](groups-settings-cmdlets.md) para criar definições de grupo para este inquilino.

### <a name="view-the-current-settings"></a>Ver as definições atuais

1. Pegue a política de nomeação atual para ver as definições atuais.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Apresente as definições atuais do grupo.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Desbloqueie a política de nomeação e as palavras bloqueadas personalizadas

1. Defina os prefixos e sufixos de nomes de grupo no Azure AD PowerShell. Para que a funcionalidade funcione corretamente, [Nome de grupo] deve ser incluído na definição.
  
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
  
Já está. Definiste a tua política de nomeação e adicionaste as tuas palavras bloqueadas.

## <a name="export-or-import-custom-blocked-words"></a>Exportar ou importar palavras bloqueadas personalizadas

Para mais informações, consulte o artigo [Azure Ative Directory cmdlets para configurar as definições](groups-settings-cmdlets.md)do grupo .

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

## <a name="remove-the-naming-policy"></a>Remova a política de nomeação

### <a name="remove-the-naming-policy-using-azure-portal"></a>Remova a política de nomeação usando o portal Azure

1. Na página de **política de Nomeação,** selecione **a política Eliminar**.
1. Depois de confirmar a eliminação, a política de nomeação é removida, incluindo toda a política de nomeação de prefixo-sufixo e quaisquer palavras bloqueadas personalizadas.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Remova a política de nomeação usando a Azure AD PowerShell

1. Esvazie os prefixos e sufixos do nome de grupo em Azure AD PowerShell.
  
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

## <a name="experience-across-office-365-apps"></a>Experiência em 365 apps do Office

Depois de definir uma política de nomeação de grupo em Azure AD, quando um utilizador cria um grupo numa aplicação do Office 365, eles vêem:

- Uma pré-visualização do nome de acordo com a sua política de nomeação (com prefixos e sufixos) logo que os tipos de utilizador no nome de grupo
- Se o utilizador introduzir palavras bloqueadas, verá uma mensagem de erro para que possa remover as palavras bloqueadas.

Carga de trabalho | Conformidade
----------- | -------------------------------
Portais de Diretório Ativo Azure | O portal Azure AD e o portal Access Panel mostram o nome imposto de nomeação quando o utilizador escreve em nome de grupo ao criar ou editar um grupo. Quando um utilizador introduz uma palavra bloqueada personalizada, é apresentada uma mensagem de erro com a palavra bloqueada para que o utilizador possa removê-la.
Outlook Web Access (OWA) | O Outlook Web Access mostra o nome imposto pela política de nomeação quando o utilizador escreve um nome de grupo ou pseudónimo de grupo. Quando um utilizador introduz uma palavra bloqueada personalizada, uma mensagem de erro é mostrada na UI juntamente com a palavra bloqueada para que o utilizador possa removê-la.
Outlook Desktop | Os grupos criados no ambiente de trabalho do Outlook estão em conformidade com as definições de política de nomeação. A aplicação outlook desktop ainda não mostra a pré-visualização do nome de grupo imposto e não devolve os erros de palavra bloqueados personalizados quando o utilizador introduz o nome de grupo. No entanto, a política de nomeação é aplicada automaticamente ao criar ou editar um grupo, e os utilizadores vêem mensagens de erro se houver palavras bloqueadas personalizadas no nome de grupo ou pseudónimo.
Microsoft Teams | As Equipas Microsoft mostram o nome imposto de nomeação do grupo quando o utilizador introduz um nome de equipa. Quando um utilizador introduz uma palavra bloqueada personalizada, é mostrada uma mensagem de erro juntamente com a palavra bloqueada para que o utilizador possa removê-la.
SharePoint  |  O SharePoint mostra o nome imposto pela política de nomeação quando o utilizador escreve um nome de site ou endereço de e-mail de grupo. Quando um utilizador introduz uma palavra bloqueada personalizada, é mostrada uma mensagem de erro, juntamente com a palavra bloqueada para que o utilizador possa removê-la.
Microsoft Stream | O Microsoft Stream mostra o nome imposto de nomeação do grupo quando o utilizador escreve um nome de grupo ou um pseudónimo de e-mail de grupo. Quando um utilizador introduz uma palavra bloqueada personalizada, é mostrada uma mensagem de erro com a palavra bloqueada para que o utilizador possa removê-la.
Outlook iOS e Android App | Os grupos criados nas aplicações do Outlook estão em conformidade com a política de nomeação configurada. A aplicação móvel Outlook ainda não mostra a pré-visualização do nome imposto pela política de nomeação, e não devolve erros de palavras bloqueados personalizados quando o utilizador introduz o nome de grupo. No entanto, a política de nomeação é aplicada automaticamente no clique de criar/editar e os utilizadores vêem mensagens de erro se houver palavras bloqueadas personalizadas no nome de grupo ou pseudónimo.
Aplicativo móvel de grupos | Os grupos criados na aplicação móvel Grupos estão em conformidade com a política de nomeação. A aplicação móvel de grupos não mostra a pré-visualização da política de nomeação e não devolve erros de palavras bloqueados personalizados quando o utilizador introduz o nome de grupo. Mas a política de nomeação é aplicada automaticamente ao criar ou editar um grupo e os utilizadores são apresentados com erros apropriados se houver palavras bloqueadas personalizadas no nome de grupo ou pseudónimo.
Planner | O planejador está em conformidade com a política de nomeação. O planejador mostra a pré-visualização da política de nomeação ao introduzir o nome do plano. Quando um utilizador introduz uma palavra bloqueada personalizada, uma mensagem de erro é mostrada ao criar o plano.
Dynamics 365 for Customer Engagement | A Dynamics 365 para o Envolvimento do Cliente está em conformidade com a política de nomeação. A Dynamics 365 mostra o nome imposto pela política de nomeação quando o utilizador escreve um nome de grupo ou um pseudónimo de e-mail de grupo. Quando o utilizador introduz uma palavra bloqueada personalizada, é mostrada uma mensagem de erro com a palavra bloqueada para que o utilizador possa removê-la.
Sincronização de dados escolares (SDS) | Os grupos criados através das FDS cumprem a política de nomeação, mas a política de nomeação não é aplicada automaticamente. Os administradores das SDS têm de anexar os prefixos e sufixos aos nomes de classe para os quais os grupos precisam de ser criados e depois enviados para SDS. Criar ou editar em grupo falharia de outra forma.
Outlook Customer Manager (OCM) | O Outlook Customer Manager está em conformidade com a política de nomeação, que é automaticamente aplicada ao grupo criado no Outlook Customer Manager. Se for detetada uma palavra bloqueada personalizada, a criação de grupo em OCM está bloqueada e o utilizador está impedido de usar a aplicação OCM.
App de sala de aula | Os grupos criados na aplicação Classroom cumprem a política de nomeação, mas a política de nomeação não é aplicada automaticamente, e a pré-visualização da política de nomeação não é mostrada aos utilizadores enquanto entram num nome de grupo de sala de aula. Os utilizadores devem introduzir o nome de grupo de sala de aula forçado com prefixos e sufixos. Caso contrário, o grupo de sala de aula cria ou edita a operação falha com erros.
Power BI | Os espaços de trabalho do Power BI estão em conformidade com a política de nomeação.    
Yammer | Quando um utilizador inscreveu na Yammer com a sua conta Azure Ative Directory cria um grupo ou edita um nome de grupo, o nome do grupo cumprirá a política de nomeação. Isto aplica-se tanto aos grupos ligados ao Office 365 como a todos os outros grupos Yammer.<br>Se um grupo ligado ao Office 365 foi criado antes da política de nomeação estar em vigor, o nome do grupo não seguirá automaticamente as políticas de nomeação. Quando um utilizador edita o nome de grupo, será solicitado a adicionar o prefixo e o sufixo.
StaffHub  | As equipas do StaffHub não seguem a política de nomeação, mas o grupo subjacente ao Office 365 segue. O nome da equipa StaffHub não aplica os prefixos e sufixos e não verifica as palavras bloqueadas personalizadas. Mas o StaffHub aplica os prefixos e sufixos e remove palavras bloqueadas do grupo subjacente ao Office 365.
Exchange PowerShell | Os cmdlets Exchange PowerShell estão em conformidade com a política de nomeação. Os utilizadores recebem mensagens de erro apropriadas com prefixos e sufixos sugeridos e para palavras bloqueadas personalizadas se não seguirem a política de nomeação no nome do grupo e pseudónimo de grupo (mailNickname).
Cindlets powershell de diretório ativo Azure | Os cmdlets de PowerShell do Diretório Ativo Azure estão em conformidade com a política de nomeação. Os utilizadores recebem mensagens de erro apropriadas com prefixos e sufixos sugeridos e para palavras bloqueadas personalizadas se não seguirem a convenção de nomeação em nomes de grupo e pseudónimode grupo.
Centro de administração de intercâmbio | O centro de administração de intercâmbio está em conformidade com a política de nomeação. Os utilizadores recebem mensagens de erro apropriadas com prefixos e sufixos sugeridos e para palavras bloqueadas personalizadas se não seguirem a convenção de nomeação no nome do grupo e pseudónimo de grupo.
Centro de administração do Microsoft 365 | O centro de administração do Microsoft 365 está em conformidade com a política de nomeação. Quando um utilizador cria ou edita nomes de grupo, a política de nomeação é aplicada automaticamente e os utilizadores recebem erros adequados quando introduzem palavras bloqueadas personalizadas. O centro de administração do Microsoft 365 ainda não mostra uma pré-visualização da política de nomeação e não devolve erros de palavras bloqueados personalizados quando o utilizador entra no nome do grupo.

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais sobre os grupos AD Azure.

- [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Política de expiração para os grupos do Office 365](groups-lifecycle.md)
- [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir membros de um grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
