---
title: Identificar problemas com a funcionalidade de diagnóstico do Windows Virtual Desktop Preview - Azure
description: Descreve a funcionalidade de diagnóstico do Windows Virtual Desktop Preview e como usá-lo.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 6b79a26d63c02dd06b62ea6ad09941f947704dc0
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418640"
---
# <a name="identify-issues-with-the-diagnostics-feature"></a>Identificar problemas com a funcionalidade de diagnóstico

Windows Virtual Desktop Preview oferece uma funcionalidade de diagnóstico que permite ao administrador identificar problemas através de uma única interface. As funções de área de Trabalho Virtual do Windows inicie uma atividade de diagnóstico sempre que um utilizador interage com o sistema. Cada registo contém informações relevantes, como as funções de área de Trabalho Virtual do Windows envolvidas na transação, mensagens de erro, informações de inquilino e informações de utilizador. Atividades de diagnóstico são criadas pelo utilizador final e ações administrativas e podem ser categorizadas em três partições principais:

* Feed de atividades de subscrição: o utilizador final aciona essas atividades, sempre que eles tentarem aceder ao respetivo feed através de aplicações de ambiente de trabalho remoto.
* Atividades de ligação: o utilizador final aciona essas atividades, sempre que eles tentam estabelecer ligação a uma área de trabalho ou o RemoteApp por meio de aplicativos de área de trabalho remota do Microsoft.
* Atividades de gestão: o administrador aciona essas atividades, sempre que eles executar operações de gestão no sistema, como criar conjuntos de anfitrião, atribuir utilizadores a grupos de aplicações e criar atribuições de funções.
  
Ligações que não chegam a área de Trabalho Virtual do Windows não aparecem em resultados de diagnóstico porque o serviço de função de diagnóstico em si faz parte da área de Trabalho Virtual do Windows. Problemas de ligação de área de Trabalho Virtual do Windows podem acontecer quando o utilizador final está a ter problemas de conectividade de rede.

Para começar, [transferir e importar o módulo do Windows PowerShell de ambiente de Trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a utilizar na sua sessão do PowerShell, se ainda não o fez.

## <a name="diagnose-issues-with-powershell"></a>Diagnosticar problemas com o PowerShell

Diagnóstico de ambiente de Trabalho Virtual do Windows usa apenas um cmdlet do PowerShell, mas contém muitos parâmetros opcionais para ajudar a limitar e isolar problemas. As secções seguintes listam os cmdlets, pode executar para diagnosticar problemas. A maioria dos filtros podem ser aplicados juntos. Valores listados entre parênteses Retos, tal como `<tenantName>`, devem ser substituídos pelos valores que se aplicam à sua situação.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>Obter diagnóstico atividades no seu inquilino

Pode recuperar as atividades de diagnóstico ao introduzir o **Get-RdsDiagnosticActivities** cmdlet. O cmdlet de exemplo seguinte irá devolver uma lista de atividades de diagnóstico, ordenadas da mais para menos recente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName>
```

Como acontece com outros cmdlets do Windows PowerShell de ambiente de Trabalho Virtual, tem de utilizar o **- TenantName** parâmetro para especificar o nome do inquilino que pretende utilizar na sua consulta. O nome do inquilino é aplicável para quase todas as consultas de atividade de diagnóstico.

### <a name="retrieve-detailed-diagnostic-activities"></a>Obter as atividades detalhadas de diagnóstico

O **-detalhadas** parâmetro fornece detalhes adicionais para cada atividade de diagnóstico devolvida. O formato para cada atividade varia consoante o tipo de atividade. O **-detalhadas** parâmetro pode ser adicionado a qualquer **Get-RdsDiagnosticActivities** consultar, conforme mostrado no exemplo a seguir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Obter um ID de atividade por atividade de diagnóstico específico

O **- ActivityId** parâmetro retorna um específicos da atividade de diagnóstico se existir, conforme mostrado no seguinte cmdlet de exemplo.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="filter-diagnostic-activities-by-user"></a>Filtrar atividades diagnóstico por utilizador

O **- o nome de utilizador** parâmetro retorna uma lista de atividades de diagnóstico iniciada pelo utilizador especificado, conforme mostrado no seguinte cmdlet de exemplo.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

O **- o nome de utilizador** parâmetro também pode ser combinado com outros parâmetros de filtragem opcionais.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrar atividades diagnóstico por hora

Pode filtrar a lista devolvida de atividade de diagnóstico com o **- StartTime** e **- EndTime** parâmetros. O **- StartTime** parâmetro irá devolver uma lista de atividade de diagnóstico a partir de uma data específica, conforme mostrado no exemplo a seguir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

O **- EndTime** parâmetro pode ser adicionado a um cmdlet com o **- StartTime** parâmetro para especificar um período de tempo que pretende receber resultados para específico. O cmdlet de exemplo seguinte irá devolver uma lista de atividades de diagnóstico from between 1 de Agosto e 10 de Agosto.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

O **- StartTime** e **- EndTime** parâmetros também podem ser combinados com outros parâmetros de filtragem opcionais.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrar atividades diagnóstico por tipo de atividade

Também pode filtrar atividades diagnóstico por tipo de atividade com o **- ActivityType** parâmetro. O seguinte cmdlet irá devolver uma lista de ligações de utilizador final:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

O seguinte cmdlet irá devolver uma lista de tarefas de gestão de administrador:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

O **Get-RdsDiagnosticActivities** cmdlet atualmente não suporta a especificação de Feed como o ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrar atividades diagnóstico por resultado

Pode filtrar a lista de atividade de diagnóstico retornado pelo resultado com o **-resultado** parâmetro. O cmdlet de exemplo seguinte irá devolver uma lista de atividades de diagnóstico com êxito.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

O cmdlet de exemplo seguinte irá devolver uma lista de atividades de diagnóstico com falha.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

O **-resultado** parâmetro também pode ser combinado com outros parâmetros de filtragem opcionais.

## <a name="common-error-scenarios"></a>Cenários de erro comuns

Cenários de erro são categorizados em interno para o serviço e externo a área de Trabalho Virtual do Windows.

* Problema interno: Especifica os cenários que não podem ser atenuados pelo administrador de inquilinos e precisam ser resolvida como o problema de suporte. Quando criar um pedido de fornecer o ID de atividade, o nome de inquilino e o período de tempo aproximado o problema ocorreu.
* Problema externo: estão relacionadas com a cenários que podem ser atenuados pelo administrador do sistema. Estes são externos à área de Trabalho Virtual do Windows.

A tabela seguinte lista erros comuns que os administradores podem ocorrer.

>[!NOTE]
>Esta pré-visualização não inclui uma categorização completa de erros e será atualizada regularmente. Para garantir que tem as informações mais atualizadas, certifique-se de que verificar neste artigo, pelo menos, uma vez por mês.

### <a name="external-management-error-codes"></a>Códigos de erro de gestão externo

|Código numérico|Código de erro|Solução de sugestão|
|---|---|---|
|3|UnauthorizedAccess|O utilizador que tentou executar o cmdlet do PowerShell administrativo não tem permissões para fazê-lo ou estar mal escrito o respetivo nome de utilizador.|
|1000|TenantNotFound|O nome de inquilino que introduziu não corresponde a quaisquer inquilinos existentes. Reveja o nome do inquilino para erros de digitação e tente novamente.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Não é possível eliminar um inquilino, porque há muito tempo contém objetos. Elimine primeiro os conjuntos de host de sessão e tente novamente.|
|2000|HostPoolNotFound|O nome do conjunto de anfitrião que introduziu não corresponde a quaisquer agrupamentos de anfitrião existente. Reveja o nome do conjunto de anfitrião para erros de digitação e tente novamente.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Não é possível eliminar um conjunto de anfitrião, desde que contém objetos. Remova todos os grupos de aplicações no agrupamento de anfitrião em primeiro lugar.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Remova todos os anfitriões de sessões primeiro antes a eliminar o conjunto de host de sessão.|
|5001|SessionHostNotFound|O anfitrião de sessões de que consultados pode estar offline. Verifique o estado do conjunto de anfitrião.|
|5008|SessionHostUserSessionsExist |Tem de terminar todos os utilizadores no anfitrião de sessão antes de executar a sua atividade de gestão pretendido.|
|6000|AppGroupNotFound|O nome do grupo de aplicações que introduziu não corresponde a quaisquer grupos de aplicações existente. Reveja o nome do grupo de aplicações para erros de digitação e tente novamente.|
|6022|RemoteAppNotFound|O nome do RemoteApp que introduziu não corresponde a qualquer RemoteApps. Reveja o nome de RemoteApp para erros de digitação e tente novamente.|
|6010|PublishedItemsExist|O nome do recurso que está a tentar publicar é o mesmo que um recurso que já existe. Altere o nome do recurso e tente novamente.|
|7002|NameNotValidWhiteSpace|Não utilize o espaço em branco no nome.|
|8000|InvalidAuthorizationRoleScope|O nome de função que introduziu não corresponde a quaisquer nomes de função existentes. Reveja o nome da função para erros de digitação e tente novamente. |
|8001|UserNotFound |O nome de utilizador que introduziu não corresponde a nenhum nome de utilizador existente. Reveja o nome para erros de digitação e tente novamente.|
|8005|UserNotFoundInAAD |O nome de utilizador que introduziu não corresponde a nenhum nome de utilizador existente. Reveja o nome para erros de digitação e tente novamente.|
|8008|TenantConsentRequired|Siga as instruções [aqui](tenant-setup-azure-active-directory.md#grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service) para fornecer o consentimento para o seu inquilino.|

### <a name="external-connection-error-codes"></a>Códigos de erro de ligação externa

|Código numérico|Código de erro|Solução de sugestão|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|O anfitrião de sessões não seja corretamente associado ao Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|As ligações falhou porque o anfitrião de sessões está disponível. Verifique o estado de funcionamento do anfitrião de sessões.|
|-2146233088|ConnectionFailedClientDisconnect|Se vir este erro com frequência, certifique-se de que o computador do usuário está ligado à rede.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|A sessão de que tentar estabelecer ligação ao utilizador o anfitrião não está em bom estado. Depure a máquina virtual.|
|-2146233088|ConnectionFailedUserNotAuthorized|O utilizador não tem permissão para aceder à aplicação publicada ou ao ambiente de trabalho. Pode aparecer o erro depois do administrador remover recursos publicados. Pedir ao utilizador para atualizar o feed no aplicativo de área de trabalho remota.|
|2|FileNotFound|Incorretamente o aplicativo que o utilizador tentou aceder está instalado ou definido como um caminho incorreto.|
|3|InvalidCredentials|O nome de utilizador ou palavra-passe de utilizador introduzido não corresponde a quaisquer nomes de utilizador ou palavras-passe existente. Reveja as credenciais para erros de digitação e tente novamente.|
|8|ConnectionBroken|Remover a ligação entre o cliente e o Gateway ou servidor. Nenhuma ação necessária, a menos que ele acontece inesperadamente.|
|14|UnexpectedNetworkDisconnect|Remover a ligação à rede. Pedir ao utilizador para ligar novamente.|
|24|ReverseConnectFailed|A máquina virtual do anfitrião não tem nenhuma linha de visão direta para o Gateway de RD. Certifique-se de que o endereço IP do Gateway pode ser resolvido.|

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as funções dentro do ambiente de Trabalho Virtual do Windows, veja [ambiente do Windows Virtual Desktop Preview](environment-setup.md).

Para ver uma lista de cmdlets do PowerShell disponíveis para a área de Trabalho Virtual do Windows, consulte a [referência do PowerShell](/powershell/windows-virtual-desktop/overview).
