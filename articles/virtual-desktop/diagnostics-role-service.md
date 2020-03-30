---
title: Problemas de diagnóstico do Windows Virtual Desktop - Azure
description: Como utilizar a funcionalidade de diagnóstico do Windows Virtual Desktop para diagnosticar problemas.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce85fb70e1480ad285eee78fe20faa8d77b9a147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254264"
---
# <a name="identify-and-diagnose-issues"></a>Identificar e diagnosticar problemas

O Windows Virtual Desktop oferece uma funcionalidade de diagnóstico que permite ao administrador identificar problemas através de uma única interface. As funções de Ambiente de Trabalho Virtual do Windows registam uma atividade de diagnóstico sempre que um utilizador interage com o sistema. Cada registo contém informações relevantes, tais como as funções de Ambiente de Trabalho Virtual do Windows envolvidas na transação, mensagens de erro, informações do arrendatário e informações do utilizador. As atividades de diagnóstico são criadas por ações de utilizador final e administrativas, e podem ser categorizadas em três baldes principais:

* Atividades de subscrição de feed: o utilizador final ativa estas atividades sempre que tenta ligar-se ao seu feed através de aplicações do Microsoft Remote Desktop.
* Atividades de ligação: o utilizador final ativa estas atividades sempre que tenta ligar-se a um ambiente de trabalho ou a um RemoteApp através de aplicações do Microsoft Remote Desktop.
* Atividades de gestão: o administrador desencadeia estas atividades sempre que realizam operações de gestão no sistema, tais como a criação de piscinas de anfitriões, a atribuição de utilizadores a grupos de aplicações e a criação de atribuições de funções.
  
As ligações que não chegam ao Windows Virtual Desktop não vão aparecer nos resultados de diagnóstico porque o próprio serviço de funções de diagnóstico faz parte do Windows Virtual Desktop. Os problemas de ligação ao Windows Virtual Desktop podem acontecer quando o utilizador final está a ter problemas de conectividade de rede.

Para começar, [descarregue e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) para utilizar na sua sessão PowerShell se ainda não o fez. Depois disso, execute o seguinte cmdlet para iniciar sessão na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Diagnosticar problemas com a PowerShell

O Windows Virtual Desktop Diagnostics utiliza apenas um cmdlet PowerShell, mas contém muitos parâmetros opcionais para ajudar a reduzir e isolar problemas. As seguintes secções listam as cmdlets que pode correr para diagnosticar problemas. A maioria dos filtros pode ser aplicada em conjunto. Os valores enumerados `<tenantName>`nos parênteses, tais como, devem ser substituídos com os valores aplicáveis à sua situação.

>[!IMPORTANT]
>A função de diagnóstico destina-se a resolução de problemas de um único utilizador. Todas as consultas que utilizem o PowerShell devem incluir os parâmetros *-UserName* ou *-ActivityID.* Para monitorizar as capacidades, utilize o Log Analytics. Consulte [o Use Log Analytics para](diagnostics-log-analytics.md) obter mais informações sobre como enviar dados de diagnóstico para o seu espaço de trabalho. 

### <a name="filter-diagnostic-activities-by-user"></a>Filtrar atividades de diagnóstico pelo utilizador

O parâmetro **-UserName** devolve uma lista de atividades de diagnóstico iniciadas pelo utilizador especificado, como mostra o seguinte exemplo cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

O parâmetro **-UserName** também pode ser combinado com outros parâmetros de filtragem opcionais.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrar atividades de diagnóstico no tempo

Pode filtrar a lista de atividades de diagnóstico devolvidas com os parâmetros **-StartTime** e **-EndTime.** O parâmetro **-StartTime** devolverá uma lista de atividades de diagnóstico a partir de uma data específica, como mostra o seguinte exemplo.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

O parâmetro **-EndTime** pode ser adicionado a um cmdlet com o parâmetro **-StartTime** para especificar um período de tempo específico para o qual pretende receber resultados. O exemplo seguinte da CMDLET devolverá uma lista de atividades de diagnóstico entre 1 de agosto e 10 de agosto.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Os parâmetros **-StartTime** e **-EndTime** também podem ser combinados com outros parâmetros de filtragem opcionais.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrar atividades de diagnóstico por tipo de atividade

Também pode filtrar atividades de diagnóstico por tipo de atividade com o parâmetro **-ActivityType.** O seguinte cmdlet devolverá uma lista de ligações ao utilizador final:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

O seguinte cmdlet devolverá uma lista de tarefas de gestão de administradores:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

O **Get-RdsDiagnosticActivities** cmdlet não suporta especificar o Feed como o ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrar atividades de diagnóstico por resultado

Pode filtrar a lista de atividades de diagnóstico devolvidas pelo resultado com o parâmetro **-Resultado.** O exemplo seguinte cmdlet devolverá uma lista de atividades de diagnóstico bem sucedidas.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

O exemplo seguinte cmdlet devolverá uma lista de atividades de diagnóstico falhadas.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

O parâmetro **-Resultado** também pode ser combinado com outros parâmetros de filtragem opcionais.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Recuperar uma atividade de diagnóstico específica por ID de atividade

O parâmetro **-ActivityId** devolve uma atividade de diagnóstico específica se existir, como mostra o seguinte exemplo cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Ver mensagens de erro para uma atividade falhada por ID de atividade

Para visualizar as mensagens de erro para uma atividade falhada, deve executar o cmdlet com o parâmetro **-Detalhado.** Pode visualizar a lista de erros executando o cmdlet **Select-Object.**

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Recuperar atividades de diagnóstico detalhadas

O parâmetro **-Detalhado** fornece detalhes adicionais para cada atividade de diagnóstico devolvida. O formato para cada atividade varia consoante o seu tipo de atividade. O parâmetro **-Detalhado** pode ser adicionado a qualquer consulta **get-RdsDiagnosticActivities,** como mostra o seguinte exemplo.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Cenários de erros comuns

Os cenários de erro são categorizados internamente para o serviço e externos ao Windows Virtual Desktop.

* Questão Interna: especifica cenários que não podem ser atenuados pelo administrador do arrendatário e que precisam de ser resolvidos como uma questão de apoio. Ao fornecer feedback através da Comunidade de Tecnologia de Ambiente de [Trabalho Virtual do Windows,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)inclua o ID de atividade e o tempo aproximado de quando o problema ocorreu.
* Questão Externa: relacionar-se com cenários que podem ser atenuados pelo administrador do sistema. Estes são externos ao Windows Virtual Desktop.

A tabela que se segue enumera erros comuns que os seus administradores podem encontrar.

>[!NOTE]
>Esta lista inclui erros mais comuns e é atualizada numa cadência regular. Para garantir que tem as informações mais atualizadas, certifique-se de que volta a verificar este artigo pelo menos uma vez por mês.

### <a name="external-management-error-codes"></a>Códigos de erro de gestão externa

|Código numérico|Código de erro|Solução sugerida|
|---|---|---|
|3|Acesso não autorizado|O utilizador que tentou executar o cmdlet administrativo PowerShell ou não tem permissões para o fazer ou enevoou o seu nome de utilizador.|
|1000|InquilinoNão Encontrado|O nome do inquilino que inscreveu não corresponde a nenhum inquilino existente. Reveja o nome do inquilino para os typos e tente de novo.|
|1006|TenantCanBeRemovedHasSessionHostPools|Não se pode excluir um inquilino desde que contenha objetos. Apague primeiro as piscinas de anfitriões da sessão e tente novamente.|
|2000|HostPoolNotFound|O nome da piscina anfitriã o que inscreveu não corresponde a nenhuma piscina de anfitriões existente. Reveja o nome da piscina anfitriã para os typos e tente novamente.|
|2005|HostpoolcanberemovedHasApplicationGroups|Não é possível apagar uma piscina de hospedeiro desde que contenha objetos. Remova primeiro todos os grupos de aplicações na piscina anfitriã.|
|2004|HostpoolCanBeRemovedHassessionHosts|Remova primeiro todas as sessões anfitriões antes de eliminar a piscina anfitriã da sessão.|
|5001|SessionHostNotFound|O anfitrião da sessão que quer saber pode estar offline. Verifique o estado da piscina anfitriã.|
|5008|Sessões de anfitriõesde sessõesExistem |Deve inscrever todos os utilizadores no anfitrião da sessão antes de executar a sua atividade de gestão pretendida.|
|6000|AppGroupNotFound|O nome do grupo de aplicações que inseriu não corresponde a nenhum grupo de aplicações existente. Reveja o nome do grupo de aplicações para os typos e tente novamente.|
|6022|RemoteAppNotFound|O nome RemoteApp que introduziu não corresponde a nenhum RemoteApps. Reveja o nome RemoteApp para os typos e tente novamente.|
|6010|PublicadoItemsExist|O nome do recurso que está a tentar publicar é o mesmo que um recurso que já existe. Mude o nome do recurso e tente de novo.|
|7002|NameNotValidWhiteSpace|Não use espaço branco no nome.|
|8000|InvalidaçãoRoleScope|O nome de papel que inscreveu não corresponde a nenhum nome de papel existente. Reveja o nome do papel para os typos e tente novamente. |
|8001|UserNotFound |O nome de utilizador introduzido não corresponde a nenhum nome de utilizador existente. Reveja o nome para os typos e tente de novo.|
|8005|usernotfoundinaad |O nome de utilizador introduzido não corresponde a nenhum nome de utilizador existente. Reveja o nome para os typos e tente de novo.|
|8008|InquilinoConsentRequired|Siga as instruções [aqui](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) para dar consentimento ao seu inquilino.|

### <a name="external-connection-error-codes"></a>Códigos de erro de ligação externa

|Código numérico|Código de erro|Solução sugerida|
|---|---|---|
|-2147467259|ConexãoFalhadaFalha da relação fidedigna|O anfitrião da sessão não está corretamente acompanhado ao Diretório Ativo.|
|-2146233088|ConexãoFalhadaUserHasValidSessionButRdshIsUnhealthy|As ligações falharam porque o anfitrião da sessão não está disponível. Verifique a saúde do anfitrião da sessão.|
|-2146233088|ConexãoFalhada Falhado ClientDisconnect|Se vir este erro com frequência, certifique-se de que o computador do utilizador está ligado à rede.|
|-2146233088|ConexãoFailedNoHealthyRdshDisponível|A sessão a que o utilizador anfitrião tentou ligar-se não é saudável. Depura a máquina virtual.|
|-2146233088|ConexãoFalhadaUserNotAutorizado|O utilizador não tem permissão para aceder à aplicação ou ambiente de trabalho publicado. O erro pode aparecer após o administrador ter removido os recursos publicados. Peça ao utilizador que refresque o feed na aplicação Remote Desktop.|
|2|FileNotFound|A aplicação a que o utilizador tentou aceder está incorretamente instalada ou definida para um caminho incorreto.|
|3|Credenciais inválidas|O nome de utilizador ou palavra-passe que o utilizador introduziu não corresponde a quaisquer nomes de utilizador ou palavras-passe existentes. Reveja as credenciais para os tipografias e tente novamente.|
|8|Conexão Quebrada|A ligação entre o Cliente e o Gateway ou o Servidor caiu. Não é necessária nenhuma ação a não ser que aconteça inesperadamente.|
|14|Rede InesperadaDisconnect|A ligação à rede caiu. Peça ao utilizador para voltar a ligar-se.|
|24|ReverseConnectFailed|A máquina virtual hospedeira não tem linha direta de visão para RD Gateway. Certifique-se de que o endereço IP gateway pode ser resolvido.|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre papéis dentro do Windows Virtual Desktop, consulte o ambiente de ambiente de [trabalho virtual do Windows](environment-setup.md).

Para ver uma lista de cmdlets PowerShell disponíveis para Windows Virtual Desktop, consulte a [referência PowerShell](/powershell/windows-virtual-desktop/overview).
