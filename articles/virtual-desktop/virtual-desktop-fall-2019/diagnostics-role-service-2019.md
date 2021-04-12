---
title: Problemas de diagnóstico do Windows Virtual Desktop (clássico) - Azure
description: Como utilizar a funcionalidade de diagnóstico virtual do Windows Desktop (clássico) para diagnosticar problemas.
author: Heidilohr
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: e23a1e9a2a0118402df0d9b8869f170762a52284
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444960"
---
# <a name="identify-and-diagnose-issues-in-windows-virtual-desktop-classic"></a>Identificar e diagnosticar problemas no Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../diagnostics-role-service.md).

O Windows Virtual Desktop oferece uma funcionalidade de diagnóstico que permite ao administrador identificar problemas através de uma única interface. As funções de Desktop Virtual do Windows registam uma atividade de diagnóstico sempre que um utilizador interage com o sistema. Cada registo contém informações relevantes, tais como as funções de Desktop Virtual do Windows envolvidas na transação, mensagens de erro, informações do inquilino e informações do utilizador. As atividades de diagnóstico são criadas por ações administrativas e de utilizador final, e podem ser categorizadas em três baldes principais:

* Atividades de subscrição de feed: o utilizador final desencadeia estas atividades sempre que tenta ligar-se ao seu feed através de aplicações do Microsoft Remote Desktop.
* Atividades de conexão: o utilizador final desencadeia estas atividades sempre que tenta ligar-se a um ambiente de trabalho ou ao RemoteApp através de aplicações de Desktop remoto da Microsoft.
* Atividades de gestão: o administrador desencadeia estas atividades sempre que realiza operações de gestão no sistema, tais como criar piscinas de anfitriões, atribuir utilizadores a grupos de aplicações e criar atribuições de funções.

As ligações que não chegam ao Windows Virtual Desktop não aparecem nos resultados dos diagnósticos porque o próprio serviço de função de diagnóstico faz parte do Windows Virtual Desktop. Problemas de ligação virtual ao ambiente de trabalho do Windows podem ocorrer quando o utilizador final está a ter problemas de conectividade de rede.

Para começar, [descarregue e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) para utilizar na sua sessão PowerShell se ainda não o fez. Depois disso, execute o seguinte cmdlet para iniciar scontabilidade na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Diagnosticar problemas com PowerShell

O Windows Virtual Desktop Diagnostics utiliza apenas um cmdlet PowerShell, mas contém muitos parâmetros opcionais para ajudar a reduzir e isolar problemas. As secções seguintes listam os cmdlets que pode executar para diagnosticar problemas. A maioria dos filtros pode ser aplicada em conjunto. Os valores enumerados nos parênteses, tais `<tenantName>` como, devem ser substituídos pelos valores que se aplicam à sua situação.

>[!IMPORTANT]
>A funcionalidade de diagnóstico destina-se a resolver problemas de um único utilizador. Todas as consultas que utilizem o PowerShell devem incluir os parâmetros *-UserName* ou *-ActivityID.* Para monitorizar as capacidades, utilize o Log Analytics. Consulte [o Use Log Analytics para obter](diagnostics-log-analytics-2019.md) mais informações sobre como enviar dados de diagnóstico para o seu espaço de trabalho.

### <a name="filter-diagnostic-activities-by-user"></a>Filtrar as atividades de diagnóstico pelo utilizador

O parâmetro **-UserName** devolve uma lista de atividades de diagnóstico iniciadas pelo utilizador especificado, como mostra o cmdlet do exemplo seguinte.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

O parâmetro **-UserName** também pode ser combinado com outros parâmetros de filtragem opcionais.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrar as atividades de diagnóstico pelo tempo

Pode filtrar a lista de atividades de diagnóstico devolvidas com os parâmetros **-StartTime** e **-EndTime.** O parâmetro **-StartTime** retornará uma lista de atividades de diagnóstico a partir de uma data específica, como mostra o exemplo seguinte.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

O parâmetro **-EndTime** pode ser adicionado a um cmdlet com o parâmetro **-StartTime** para especificar um período específico de tempo para o qual deseja receber resultados. O exemplo seguinte, a CMDLET, devolverá uma lista de atividades de diagnóstico entre 1 de agosto e 10 de agosto.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Os parâmetros **-StartTime** e **-EndTime** também podem ser combinados com outros parâmetros de filtragem opcionais.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrar atividades de diagnóstico por tipo de atividade

Também pode filtrar atividades de diagnóstico por tipo de atividade com o parâmetro **-ActivityType.** O cmdlet seguinte devolverá uma lista das ligações do utilizador final:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

O cmdlet seguinte devolverá uma lista de tarefas de gestão de administradores:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

O **cmdlet Get-RdsDiagnosticActividades** não suporta atualmente especificar o Feed como o ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrar as atividades de diagnóstico por resultado

Pode filtrar a lista de atividades de diagnóstico devolvidas por resultado com o parâmetro **-Resultado.** O seguinte exemplo cmdlet devolverá uma lista de atividades de diagnóstico bem sucedidas.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

O seguinte exemplo cmdlet devolverá uma lista de atividades de diagnóstico falhadas.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

O parâmetro **-Resultado** também pode ser combinado com outros parâmetros de filtragem opcionais.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Recuperar uma atividade de diagnóstico específica por ID de atividade

O parâmetro **-ActivityId** devolve uma atividade de diagnóstico específica se existir, como mostra o cmdlet do exemplo seguinte.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Ver mensagens de erro para uma atividade falhada por ID de atividade

Para visualizar as mensagens de erro de uma atividade falhada, tem de executar o cmdlet com o parâmetro **-Detalhado.** Pode ver a lista de erros executando o **cmdlet Select-Object.**

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Recuperar atividades de diagnóstico detalhadas

O **parâmetro -Detalhado** fornece detalhes adicionais para cada atividade de diagnóstico devolvida. O formato para cada atividade varia consoante o seu tipo de atividade. O parâmetro **-Detalhado** pode ser adicionado a qualquer consulta **de Reativação Get-RdsDiagnostic,** como mostra o exemplo seguinte.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Cenários de erros comuns

Os cenários de erro são categorizados internamente ao serviço e externos ao Windows Virtual Desktop.

* Questão Interna: especifica cenários que não podem ser atenuados pelo administrador do inquilino e que precisam de ser resolvidos como uma questão de apoio. Ao fornecer feedback através do [Windows Virtual Desktop Tech Community,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)inclua o ID de atividade e o período de tempo aproximado de quando o problema ocorreu.
* Questão Externa: relaciona-se com cenários que podem ser atenuados pelo administrador do sistema. Estes são externos ao Windows Virtual Desktop.

A tabela que se segue enumera erros comuns que os seus administradores podem encontrar.

>[!NOTE]
>Esta lista inclui a maioria dos erros comuns e é atualizada numa cadência regular. Para garantir que tem as informações mais atualizadas, certifique-se de voltar a verificar este artigo pelo menos uma vez por mês.

### <a name="external-management-error-codes"></a>Códigos de erro de gestão externa

|Código numérico|Código de erro|Solução sugerida|
|---|---|---|
|1322|ConexãoFailedNomappingOfsiDinAD|O utilizador não é membro do Azure Ative Directory. Siga as instruções no [Ative Directory Administrative Center](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) para adicioná-las.|
|3|Não autorizadosAcesss|O utilizador que tentou executar o cmdlet administrativo powerShell ou não tem permissões para o fazer ou desmarcou o seu nome de utilizador.|
|1000|TenantNotFound|O nome do inquilino que inscreveu não corresponde a inquilinos existentes. Reveja o nome do inquilino para tipografias e tente novamente.|
|1006|TenantCannotBeRemovedHasessionHostPools|Não se pode apagar um inquilino enquanto contiver objetos. Elimine primeiro as piscinas dos anfitriões da sessão e, em seguida, tente novamente.|
|2000|HostPoolNotFound|O nome da piscina anfitriã que inseriu não corresponde a piscinas de anfitriões existentes. Reveja o nome da piscina do anfitrião para tipografias e tente novamente.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Não é possível apagar uma piscina de anfitriões desde que contenha objetos. Remova primeiro todos os grupos de aplicações na piscina anfitriã.|
|2004|HostPoolCannotBeRemovedHasessionHosts|Remova todas as sessões antes de apagar a piscina do anfitrião da sessão.|
|5001|SessionHostNotFound|O anfitrião da sessão que quis saber pode estar offline. Verifique o estado da piscina anfitriã.|
|5008|SessionHostUserSessionsExist |Deve assinar todos os utilizadores no anfitrião da sessão antes de executar a sua atividade de gestão pretendida.|
|6000|AppGroupNotFound|O nome do grupo de aplicações que inseriu não corresponde a nenhum grupo de aplicações existentes. Reveja o nome do grupo de aplicações para tipografias e tente novamente.|
|6022|RemoteAppNotFound|O nome RemoteApp que inseriu não corresponde a quaisquer RemoteApps. Reveja o nome Do RemoteApp para tipografias e tente novamente.|
|6010|PublishedItemsExist|O nome do recurso que está a tentar publicar é o mesmo que um recurso que já existe. Mude o nome do recurso e tente novamente.|
|7002|NomeNotValidWhiteSpace|Não use espaço branco no nome.|
|8000|InvalidAuthorizationRoleScope|O nome que inseriu não corresponde a nenhum nome existente. Reveja o nome do papel para tipografias e tente novamente. |
|8001|UserNotFound |O nome de utilizador introduzido não corresponde a nenhum nome de utilizador existente. Reveja o nome para tipografias e tente novamente.|
|8005|UserNotFoundInAAD |O nome de utilizador introduzido não corresponde a nenhum nome de utilizador existente. Reveja o nome para tipografias e tente novamente.|
|8008|TenantConsentRequired|Siga as instruções [aqui](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) para fornecer o consentimento para o seu inquilino.|

### <a name="external-connection-error-codes"></a>Códigos de erro de ligação externa

|Código numérico|Código de erro|Solução sugerida|
|---|---|---|
|-2147467259|ConexãoFailedAdErrorNoSuchMember|O utilizador não é membro do Ative Directory. Siga as instruções no [Ative Directory Administrative Center](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) para adicioná-las.|
|-2147467259|ConexãoFailedAdTrustedRelationshipFailure|O anfitrião da sessão não está corretamente associado ao Ative Directory.|
|-2146233088|ConexãoFailedUserHasValidSessionButRdshIsUnhealthy|As ligações falharam porque o anfitrião da sessão não está disponível. Verifique a saúde do anfitrião da sessão.|
|-2146233088|ConexãoFailedClientDisconnect|Se vir este erro com frequência, certifique-se de que o computador do utilizador está ligado à rede.|
|-2146233088|ConexãoFailedNoHealthyRdsh Disponível|A sessão a que o utilizador anfitrião tentou ligar não é saudável. Depurar a máquina virtual.|
|-2146233088|ConexãoFailedUserNotAuthorized|O utilizador não tem permissão para aceder à aplicação ou ambiente de trabalho publicado. O erro pode aparecer após o administrador ter removido os recursos publicados. Peça ao utilizador para refrescar o feed na aplicação Remote Desktop.|
|2|FileNotFound|A aplicação a que o utilizador tentou aceder está incorretamente instalada ou definida para um caminho incorreto.|
|3|InválidosCredis|O nome de utilizador ou palavra-passe que o utilizador introduziu não corresponde a nomes de utilizador ou palavras-passe existentes. Reveja as credenciais para tipografias e tente novamente.|
|8|ConexãoBroken|A ligação entre Cliente e Gateway ou Servidor caiu. Não é necessária ação a não ser que aconteça inesperadamente.|
|14|UnexpectedNetworkDisconnect|A ligação à rede caiu. Peça ao utilizador para voltar a ligar.|
|24|ReverseConnectFailed|A máquina virtual hospedeira não tem linha de visão direta para RD Gateway. Certifique-se de que o endereço IP gateway pode ser resolvido.|
|1322|ConexãoFailedNomappingOfsiDinAD|O utilizador não é membro do Ative Directory. Siga as instruções no [Ative Directory Administrative Center](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) para adicioná-las.|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funções dentro do Windows Virtual Desktop, consulte o [ambiente de trabalho virtual do Windows.](environment-setup-2019.md)

Para ver uma lista de cmdlets powerShell disponíveis para o Windows Virtual Desktop, consulte a [referência PowerShell](/powershell/windows-virtual-desktop/overview).
