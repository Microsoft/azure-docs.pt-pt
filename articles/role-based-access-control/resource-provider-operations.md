---
title: Operações de fornecedor de recursos do Azure Resource Manager | Documentos da Microsoft
description: Lista as operações disponíveis para os fornecedores de recursos do Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 07c1a726e33eb8287634b63ef2e309483c05c3f3
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962109"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operações de fornecedor de recursos do Azure Resource Manager

Este artigo lista as operações disponíveis para cada fornecedor de recursos do Azure Resource Manager. Estas operações podem ser utilizadas em [funções personalizadas](custom-roles.md) para fornecer granular [controlo de acesso baseado em funções (RBAC)](overview.md) aos recursos no Azure. Cadeias de caracteres de operação tem o seguinte formato: `{Company}.{ProviderName}/{resourceType}/{action}`

As operações de fornecedor de recursos estão sempre a evoluir. Para obter as operações mais recente, utilize [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) ou [lista de operação de fornecedores de az](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.AAD/domainServices/delete | Eliminar o serviço de domínio |
> | Acção | Microsoft.AAD/domainServices/oucontainer/delete | Eliminar o contentor de UO |
> | Acção | Microsoft.AAD/domainServices/oucontainer/read | Leu os contentores de UO |
> | Acção | Microsoft.AAD/domainServices/oucontainer/write | Escrever o contentor de UO |
> | Acção | Microsoft.AAD/domainServices/read | Serviços de domínio de leitura |
> | Acção | Microsoft.AAD/domainServices/ReplicaSets/delete | Eliminar o Site do Cluster |
> | Acção | Microsoft.AAD/domainServices/ReplicaSets/read | Site do Cluster de leitura |
> | Acção | Microsoft.AAD/domainServices/ReplicaSets/write | Escrever o Site do Cluster |
> | Acção | Microsoft.AAD/domainServices/write | Escrever o serviço de domínio |
> | Acção | Microsoft.AAD/locations/operationresults/read |  |
> | Acção | Microsoft.AAD/Operations/read |  |
> | Acção | Microsoft.AAD/register/action | Registar o serviço de domínio |
> | Acção | Microsoft.AAD/unregister/action | Anular o registo do serviço de domínio |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | microsoft.aadiam/diagnosticsettings/delete | A eliminar uma definição de diagnóstico |
> | Acção | microsoft.aadiam/diagnosticsettings/read | Ler uma definição de diagnóstico |
> | Acção | microsoft.aadiam/diagnosticsettings/write | Escrever uma definição de diagnóstico |
> | Acção | microsoft.aadiam/diagnosticsettingscategories/read | Ler um categorias de definição de diagnóstico |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Addons/operations/read | Obtém suporte operações de RP. |
> | Acção | Microsoft.Addons/register/action | Registar a subscrição especificada com addons |
> | Acção | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Lista as informações de plano de suporte atual para a subscrição especificada. |
> | Acção | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Remove o plano de suporte Canonical especificado |
> | Acção | Microsoft.Addons/supportProviders/supportPlanTypes/read | Obter o estado de plano de suporte da Canonical especificado. |
> | Acção | Microsoft.Addons/supportProviders/supportPlanTypes/write | Adiciona o tipo de plano de suporte Canonical especificado. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ADHybridHealthService/addsservices/action | Crie uma nova floresta para o inquilino. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Obtém todos os servidores para o nome de serviço especificado. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/alerts/read | Obtém os detalhes de alertas para a data de floresta como alertid, alerta gerada, última detetada, alerta descrição do alerta, último atualizado, alerta nível, alerta Estado, alerta de resolução de problemas de ligações, etc. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/configuration/read | Obtém a configuração de serviço para a floresta. Exemplo - nome da floresta, nível funcional, a função FSMO de mestre de nomenclatura domínio, a função FSMO do mestre de esquema etc. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/delete | Elimina um serviço e isso servidores, juntamente com dados de estado de funcionamento. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Obtém os detalhes de domínios e sites para a floresta. Estado de funcionamento de exemplo, alertas ativos, resolver alertas, propriedades, como o nível funcional do domínio, floresta, mestre de infraestrutura, PDC, etc de mestre de RID.  |
> | Acção | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Obtém a definição da preferência de utilizador para a floresta.<br>Exemplo - MetricCounterName como ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Definições para o etc de gráficos de interface do Usuário. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Obtém floresta resumo de determinada floresta, como o nome da floresta, número de domínios nesta floresta, o número de sites e detalhes de sites etc. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Obtém a lista de métricas suportadas para um determinado serviço.<br>Para bloqueios de conta de Extranet de exemplo, Total de pedidos falhados, pedidos de Token pendentes (Proxy), Token pedidos por segundo etc para o serviço do AD FS.<br>NTLM autenticações/seg, LDAP bem-sucedida "binds" / seg, tempo de enlace de LDAP, Threads ativos do LDAP, Kerberos autenticações/seg, ATQ etc Total de Threads para ADDomainService.<br>Execute a latência de perfil, estabelecer ligações de TCP, Bytes privados do agente de informações, estatísticas de exportar para o Azure AD para o serviço ADSync. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Devido um serviço, esta API obtém as informações de métricas.<br>Por exemplo, esta API pode ser utilizada para obter informações relacionadas com a: Extranet bloqueios de conta, Total de pedidos falhados, Token pedidos pendentes (Proxy), Token pedidos por segundo etc para o serviço de ADFederation.<br>NTLM autenticações/seg, LDAP bem-sucedida "binds" / seg, tempo de enlace de LDAP, Threads ativos do LDAP, Kerberos autenticações/seg, ATQ etc Total de Threads para o serviço de ADDomain.<br>Latência de perfil de execução, o TCP ligações estabelecidas, Insights agente Private Bytes, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Esta API obtém a lista de todos os carregadas ADDomainServices para um inquilino de premium. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/read | Obtém os detalhes do serviço para o nome de serviço especificado. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Obtém os detalhes de replicação para todos os servidores para o nome de serviço especificado. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Obtém o número de controladores de domínio e os erros de replicação se houver. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Obtém conclua a lista de controlador de domínio, juntamente com detalhes de replicação para a floresta especificada. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Adicione uma instância de servidor para o serviço. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Durante o registo do servidor de ADDomainService, esta api é chamada para obter as credenciais para a integração de novos servidores. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Elimina um servidor para um determinado serviço e inquilino. |
> | Acção | Microsoft.ADHybridHealthService/addsservices/write | Cria ou atualiza a instância de ADDomainService para o inquilino. |
> | Acção | Microsoft.ADHybridHealthService/configuration/action | Atualiza a configuração de inquilino. |
> | Acção | Microsoft.ADHybridHealthService/configuration/read | Lê a configuração do inquilino. |
> | Acção | Microsoft.ADHybridHealthService/configuration/write | Cria uma configuração de inquilino. |
> | Acção | Microsoft.ADHybridHealthService/logs/contents/read | Obtém o conteúdo de instalação do agente e os registos de registo armazenados no blob. |
> | Acção | Microsoft.ADHybridHealthService/logs/read | Obtém os registos de registo e de instalação do agente para o inquilino. |
> | Acção | Microsoft.ADHybridHealthService/operations/read | Obtém a lista de operações suportadas pelo sistema. |
> | Acção | Microsoft.ADHybridHealthService/register/action | Regista o fornecedor de recursos do serviço de estado de funcionamento ADHybrid e permite a criação do recurso de serviço de estado de funcionamento de ADHybrid. |
> | Acção | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Obtém a lista de regiões disponíveis, utilizado por DevOps para oferecer suporte a incidentes de cliente. |
> | Acção | Microsoft.ADHybridHealthService/reports/badpassword/read | Obtém a lista de tentativas de palavra-passe incorreta para todos os utilizadores no serviço de Federação do Active Directory. |
> | Acção | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Obtém o URI de SAS do Blob que contém a Estado e eventual resultado da tarefa para relatório de colocados em fila recentemente para a frequência de nome de utilizador/palavra-passe incorreta tenta por ID de utilizador por IPAddress por dia para um determinado inquilino. |
> | Acção | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Obtém a lista de DevOps consentidas por inquilinos. Normalmente utilizado para suporte ao cliente. |
> | Acção | Microsoft.ADHybridHealthService/reports/isdevops/read | Obtém um valor que indica se o inquilino seja o consentimento de DevOps ou não. |
> | Acção | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Userid(objectid) de atualizações para o inquilino de ops dev selecionado. |
> | Acção | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Obtém a implementação selecionada para o inquilino especificado. |
> | Acção | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Tendo em conta um id de inquilino obtém a localização de armazenamento do inquilino. |
> | Acção | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Obtém a geo localização a partir do qual serão possível aceder aos dados. |
> | Acção | Microsoft.ADHybridHealthService/services/action | Atualiza uma instância de serviço no inquilino. |
> | Acção | Microsoft.ADHybridHealthService/services/alerts/read | Lê os alertas para um serviço. |
> | Acção | Microsoft.ADHybridHealthService/services/alerts/read | Lê os alertas para um serviço. |
> | Acção | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Devido um recurso nome verifica se um serviço tem tudo o que deve usar essa funcionalidade. |
> | Acção | Microsoft.ADHybridHealthService/services/delete | Elimina uma instância de serviço no inquilino. |
> | Acção | Microsoft.ADHybridHealthService/services/exporterrors/read | Obtém os erros de exportação para um serviço de sincronização especificada. |
> | Acção | Microsoft.ADHybridHealthService/services/exportstatus/read | Obtém o estado de exportação para um determinado serviço. |
> | Acção | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Obtém comentários de alertas para um determinado serviço e o servidor. |
> | Acção | Microsoft.ADHybridHealthService/services/metricmetadata/read | Obtém a lista de métricas suportadas para um determinado serviço.<br>Para bloqueios de conta de Extranet de exemplo, Total de pedidos falhados, pedidos de Token pendentes (Proxy), Token pedidos por segundo etc para o serviço do AD FS.<br>NTLM autenticações/seg, LDAP bem-sucedida "binds" / seg, tempo de enlace de LDAP, Threads ativos do LDAP, Kerberos autenticações/seg, ATQ etc Total de Threads para ADDomainService.<br>Execute a latência de perfil, estabelecer ligações de TCP, Bytes privados do agente de informações, estatísticas de exportar para o Azure AD para o serviço ADSync. |
> | Acção | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Devido um serviço, esta API obtém a média de métricas para um determinado serviço.<br>Por exemplo, esta API pode ser utilizada para obter informações relacionadas com a: Extranet bloqueios de conta, Total de pedidos falhados, Token pedidos pendentes (Proxy), Token pedidos por segundo etc para o serviço de ADFederation.<br>NTLM autenticações/seg, LDAP bem-sucedida "binds" / seg, tempo de enlace de LDAP, Threads ativos do LDAP, Kerberos autenticações/seg, ATQ etc Total de Threads para o serviço de ADDomain.<br>Latência de perfil de execução, o TCP ligações estabelecidas, Insights agente Private Bytes, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Acção | Microsoft.ADHybridHealthService/services/metrics/groups/read | Devido um serviço, esta API obtém as informações de métricas.<br>Por exemplo, esta API pode ser utilizada para obter informações relacionadas com a: Extranet bloqueios de conta, Total de pedidos falhados, Token pedidos pendentes (Proxy), Token pedidos por segundo etc para o serviço de ADFederation.<br>NTLM autenticações/seg, LDAP bem-sucedida "binds" / seg, tempo de enlace de LDAP, Threads ativos do LDAP, Kerberos autenticações/seg, ATQ etc Total de Threads para o serviço de ADDomain.<br>Latência de perfil de execução, o TCP ligações estabelecidas, Insights agente Private Bytes, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Acção | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Devido um serviço, esta API obtém a vista de agregados de métricas para um determinado serviço.<br>Por exemplo, esta API pode ser utilizada para obter informações relacionadas com a: Extranet bloqueios de conta, Total de pedidos falhados, Token pedidos pendentes (Proxy), Token pedidos por segundo etc para o serviço de ADFederation.<br>NTLM autenticações/seg, LDAP bem-sucedida "binds" / seg, tempo de enlace de LDAP, Threads ativos do LDAP, Kerberos autenticações/seg, ATQ etc Total de Threads para o serviço de ADDomain.<br>Latência de perfil de execução, o TCP ligações estabelecidas, Insights agente Private Bytes, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Acção | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Adicionar ou atualiza a configuração de monitorização para um serviço. |
> | Acção | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Obtém as configurações de monitorização para um determinado serviço. |
> | Acção | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Adicionar ou configurações de monitorização para um serviço de atualizações. |
> | Acção | Microsoft.ADHybridHealthService/services/premiumcheck/read | Esta API obtém a lista de todos os serviços de integração para um inquilino de premium. |
> | Acção | Microsoft.ADHybridHealthService/services/read | Lê as instâncias de serviço no inquilino. |
> | Acção | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Obtém o relatório de IP em risco todos os URIs dos últimos 7 dias. |
> | Acção | Microsoft.ADHybridHealthService/services/reports/details/read | Relatório obtém dos 50 principais utilizadores com erros de palavra-passe incorreta dos últimos 7 dias |
> | Acção | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Gera o relatório de IP em risco e devolve um URI apontando para ele. |
> | Acção | Microsoft.ADHybridHealthService/services/servicemembers/action | Cria uma instância de servidor no serviço. |
> | Acção | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Lê os alertas para um servidor. |
> | Acção | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Durante o registo do servidor, esta api é chamada para obter as credenciais para a integração de novos servidores. |
> | Acção | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Para um determinado servidor, esta API obtém uma lista de tipos de dados que estão a ser carregados, os servidores e a última hora para cada carregamento. |
> | Acção | Microsoft.ADHybridHealthService/services/servicemembers/delete | Elimina uma instância de servidor no serviço. |
> | Acção | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Obtém os detalhes do erro de exportação de sincronização para um determinado serviço de sincronização. |
> | Acção | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Devido um serviço, esta API obtém as informações de métricas.<br>Por exemplo, esta API pode ser utilizada para obter informações relacionadas com a: Extranet bloqueios de conta, Total de pedidos falhados, Token pedidos pendentes (Proxy), Token pedidos por segundo etc para o serviço de ADFederation.<br>NTLM autenticações/seg, LDAP bem-sucedida "binds" / seg, tempo de enlace de LDAP, Threads ativos do LDAP, Kerberos autenticações/seg, ATQ etc Total de Threads para o serviço de ADDomain.<br>Latência de perfil de execução, o TCP ligações estabelecidas, Insights agente Private Bytes, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Acção | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | Obtém a lista de conectores e nomes de perfil de execução para o determinado serviço e o membro de serviço. |
> | Acção | Microsoft.ADHybridHealthService/services/servicemembers/read | Lê a instância de servidor no serviço. |
> | Acção | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Obtém a configuração de serviço para um determinado inquilino. |
> | Acção | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Obtém o estado de listas de permissões de funcionalidade para um determinado inquilino. |
> | Acção | Microsoft.ADHybridHealthService/services/write | Cria uma instância de serviço no inquilino. |
> | Acção | Microsoft.ADHybridHealthService/unregister/action | Anula o registo da subscrição para o fornecedor de recursos de serviço de estado de funcionamento de ADHybrid. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Advisor/configurations/read | Obter configurações |
> | Acção | Microsoft.Advisor/configurations/write | Cria/atualiza a configuração |
> | Acção | Microsoft.Advisor/generateRecommendations/action | Gere recomendações |
> | Acção | Microsoft.Advisor/generateRecommendations/read | Obtém gerar estado de recomendações |
> | Acção | Microsoft.Advisor/operations/read | Obtém as operações para o Microsoft Advisor |
> | Acção | Microsoft.Advisor/recommendations/available/action | Nova recomendação está disponível no advisor da Microsoft |
> | Acção | Microsoft.Advisor/recommendations/read | Recomendações de leituras |
> | Acção | Microsoft.Advisor/recommendations/suppressions/delete | Elimina a supressão |
> | Acção | Microsoft.Advisor/recommendations/suppressions/read | Obtém a supressão |
> | Acção | Microsoft.Advisor/recommendations/suppressions/write | Cria/atualizações supressões |
> | Acção | Microsoft.Advisor/register/action | Regista a subscrição para o Microsoft Advisor |
> | Acção | Microsoft.Advisor/suppressions/delete | Elimina a supressão |
> | Acção | Microsoft.Advisor/suppressions/read | Obtém a supressão |
> | Acção | Microsoft.Advisor/suppressions/write | Cria/atualizações supressões |
> | Acção | Microsoft.Advisor/unregister/action | Anula o registo da subscrição para o Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.AlertsManagement/actionRules/delete | Elimine regra de ação numa determinada subscrição. |
> | Acção | Microsoft.AlertsManagement/actionRules/read | Obtenha todas as regras de ação para os filtros de entrada. |
> | Acção | Microsoft.AlertsManagement/actionRules/write | Criar ou atualizar regra de ação numa determinada subscrição |
> | Acção | Microsoft.AlertsManagement/alerts/changestate/action | Altere o estado do alerta. |
> | Acção | Microsoft.AlertsManagement/alerts/diagnostics/read | Obter todos os diagnósticos para o alerta |
> | Acção | Microsoft.AlertsManagement/alerts/history/read | Obter histórico do alerta |
> | Acção | Microsoft.AlertsManagement/alerts/read | Obter todos os alertas para os filtros de entrada. |
> | Acção | Microsoft.AlertsManagement/alertsList/read | Obter todos os alertas para os filtros de entrada entre subscrições |
> | Acção | Microsoft.AlertsManagement/alertsSummary/read | Obtenha o resumo de alertas |
> | Acção | Microsoft.AlertsManagement/alertsSummaryList/read | Obtenha o resumo de alertas entre subscrições |
> | Acção | Microsoft.AlertsManagement/Operations/read | Lê as operações fornecidas |
> | Acção | Microsoft.AlertsManagement/register/action | Regista a subscrição para a gestão de alertas da Microsoft |
> | Acção | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Eliminar regra de alerta inteligentes detetor numa determinada subscrição |
> | Acção | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Obter todas as detetor inteligente regras de alerta para os filtros de entrada |
> | Acção | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Criar ou atualizar regra de alerta inteligentes detetor numa determinada subscrição |
> | Acção | Microsoft.AlertsManagement/smartGroups/changestate/action | Alterar o estado do grupo inteligente |
> | Acção | Microsoft.AlertsManagement/smartGroups/history/read | Obter histórico do grupo inteligente |
> | Acção | Microsoft.AlertsManagement/smartGroups/read | Obter todos os grupos de inteligentes para os filtros de entrada |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Verifica se do Analysis Server fornecido nome é válido e não em utilização. |
> | Acção | Microsoft.AnalysisServices/locations/operationresults/read | Obtém as informações do resultado da operação especificado. |
> | Acção | Microsoft.AnalysisServices/locations/operationstatuses/read | Obtém as informações de estado da operação especificado. |
> | Acção | Microsoft.AnalysisServices/operations/read | Obtém as informações de operações |
> | Acção | Microsoft.AnalysisServices/register/action | Regista o fornecedor de recursos de Analysis Services. |
> | Acção | Microsoft.AnalysisServices/servers/delete | Elimina o Analysis Server. |
> | Acção | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Liste o estado do gateway associado ao servidor. |
> | Acção | Microsoft.AnalysisServices/servers/read | Obtém as informações de que o Analysis Server especificado. |
> | Acção | Microsoft.AnalysisServices/servers/resume/action | Retoma o Analysis Server. |
> | Acção | Microsoft.AnalysisServices/servers/skus/read | Obter informações do SKU disponíveis para o servidor |
> | Acção | Microsoft.AnalysisServices/servers/suspend/action | Suspende o Analysis Server. |
> | Acção | Microsoft.AnalysisServices/servers/write | Cria ou atualiza o Analysis Server especificado. |
> | Acção | Microsoft.AnalysisServices/skus/read | Obtém as informações dos Skus |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ApiManagement/checkNameAvailability/read | Verifica se for indicado o nome do serviço está disponível |
> | Acção | Microsoft.ApiManagement/operations/read | Ler todas as operações API disponíveis para o recurso de Microsoft.ApiManagement |
> | Acção | Microsoft.ApiManagement/register/action | Registar subscrição para o fornecedor de recursos de Microsoft.ApiManagement |
> | Acção | Microsoft.ApiManagement/reports/read | Obter relatórios agregados por períodos de tempo, região geográfica, desenvolvedores, produtos, APIs, operações, subscrição e byRequest. |
> | Acção | Microsoft.ApiManagement/service/apis/delete | Elimina a instância do serviço de gestão de API de API especificada. |
> | Acção | Microsoft.ApiManagement/service/apis/diagnostics/delete | Elimina o diagnóstico especificado a partir de uma API. |
> | Acção | Microsoft.ApiManagement/service/apis/diagnostics/read | Apresenta uma lista de todos os diagnósticos de uma API. ou obtém os detalhes do diagnóstico de uma API especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/apis/diagnostics/write | Cria um novo diagnóstico de uma API ou atualiza um já existente. ou os detalhes do diagnóstico de uma API especificados pelo respetivo identificador de atualizações. |
> | Acção | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Elimina o comentário especificado de um problema. |
> | Acção | Microsoft.ApiManagement/service/apis/issues/attachments/read | Listas de todos os anexos para o problema associado com a API especificada. ou obtém os detalhes do problema de anexo de uma API especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/apis/issues/attachments/write | Cria um novo anexo para o problema numa API ou atualiza um já existente. |
> | Acção | Microsoft.ApiManagement/service/apis/issues/comments/delete | Elimina o comentário especificado de um problema. |
> | Acção | Microsoft.ApiManagement/service/apis/issues/comments/read | Listas de todos os comentários para o problema associado com a API especificada. ou obtém os detalhes do problema de comentário para uma API especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/apis/issues/comments/write | Cria um novo comentário para o problema numa API ou atualiza um já existente. |
> | Acção | Microsoft.ApiManagement/service/apis/issues/delete | Elimina o problema especificado a partir de uma API. |
> | Acção | Microsoft.ApiManagement/service/apis/issues/read | Apresenta uma lista de todos os problemas associados com a API especificada. ou obtém os detalhes do problema de uma API especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/apis/issues/write | Cria um novo problema de uma API ou atualiza um já existente. ou atualizações existente emitir para uma API. |
> | Acção | Microsoft.ApiManagement/service/apis/operations/delete | Elimina a operação especificada na API. |
> | Acção | Microsoft.ApiManagement/service/apis/operations/policies/delete | Elimina a configuração da política na operação de Api. |
> | Acção | Microsoft.ApiManagement/service/apis/operations/policies/read | Obter a lista de configuração de política ao nível da operação de API. ou obtenha a configuração da política ao nível da operação de API. |
> | Acção | Microsoft.ApiManagement/service/apis/operations/policies/write | Cria ou atualiza a configuração da política para o nível de operação de API. |
> | Acção | Microsoft.ApiManagement/service/apis/operations/policy/delete | Eliminar a configuração de política ao nível da operação |
> | Acção | Microsoft.ApiManagement/service/apis/operations/policy/read | Obter a configuração de política ao nível da operação |
> | Acção | Microsoft.ApiManagement/service/apis/operations/policy/write | Criar configuração de política na operação de nível |
> | Acção | Microsoft.ApiManagement/service/apis/operations/read | Apresenta uma lista de uma coleção de operações para a API especificada. ou obtém os detalhes da operação de API especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/apis/operations/tags/delete | Anular a exposição a marca da operação. |
> | Acção | Microsoft.ApiManagement/service/apis/operations/tags/read | Apresenta uma lista de todas as marcas associadas com a operação. ou obtenha de etiqueta associada com a operação. |
> | Acção | Microsoft.ApiManagement/service/apis/operations/tags/write | Atribua etiqueta para a operação. |
> | Acção | Microsoft.ApiManagement/service/apis/operations/write | Cria uma nova operação na API ou atualiza um já existente. ou os detalhes da operação na API especificados pelo respetivo identificador de atualizações. |
> | Acção | Microsoft.ApiManagement/service/apis/operationsByTags/read | Apresenta uma lista de uma coleção de operações associadas a etiquetas. |
> | Acção | Microsoft.ApiManagement/service/apis/policies/delete | Elimina a configuração da política da API. |
> | Acção | Microsoft.ApiManagement/service/apis/policies/read | Obter a configuração de política ao nível da API. ou obtenha a configuração da política no nível da API. |
> | Acção | Microsoft.ApiManagement/service/apis/policies/write | Cria ou atualiza a configuração da política para a API. |
> | Acção | Microsoft.ApiManagement/service/apis/policy/delete | Eliminar a configuração de política ao nível da API |
> | Acção | Microsoft.ApiManagement/service/apis/policy/read | Obter a configuração de política ao nível da API |
> | Acção | Microsoft.ApiManagement/service/apis/policy/write | Criar configuração de política na API de nível |
> | Acção | Microsoft.ApiManagement/service/apis/products/read | Apresenta uma lista de todos os produtos, o que a API faz parte do. |
> | Acção | Microsoft.ApiManagement/service/apis/read | Apresenta uma lista de todas as APIs de instância de serviço de gestão de API. ou obtém os detalhes da API especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/apis/releases/delete | Remove todas as versões da API ou elimina a versão especificada na API. |
> | Acção | Microsoft.ApiManagement/service/apis/releases/read | Apresenta uma lista de todas as versões de uma API.<br>Uma versão de API é criada quando fazer uma revisão de API atual.<br>Versões também são utilizadas para reversão para revisões anteriores.<br>Os resultados serão paginar e podem ser limitados pelos parâmetros $top e $skip.<br>ou devolve os detalhes de uma API de versão. |
> | Acção | Microsoft.ApiManagement/service/apis/releases/write | Cria uma nova versão para a API. ou os detalhes da versão da API especificados pelo respetivo identificador de atualizações. |
> | Acção | Microsoft.ApiManagement/service/apis/revisions/delete | Remove todas as revisões de uma API |
> | Acção | Microsoft.ApiManagement/service/apis/revisions/read | Apresenta uma lista de todas as revisões de uma API. |
> | Acção | Microsoft.ApiManagement/service/apis/schemas/delete | Elimina a configuração de esquema de API. |
> | Acção | Microsoft.ApiManagement/service/apis/schemas/read | Obter a configuração de esquema no nível da API. ou obtenha a configuração de esquema no nível da API. |
> | Acção | Microsoft.ApiManagement/service/apis/schemas/write | Cria ou atualiza a configuração de esquema para a API. |
> | Acção | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Elimine descrição da etiqueta para a Api. |
> | Acção | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Apresenta uma lista de todas as descrições de etiquetas no âmbito da API. Modelar semelhante do swagger - tagDescription é definida no nível da API mas tag podem ser atribuídos às operações ou obter a descrição da etiqueta no âmbito da API |
> | Acção | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Criar/atualizar descrição da etiqueta no âmbito da Api. |
> | Acção | Microsoft.ApiManagement/service/apis/tags/delete | Anular a exposição a marca da Api. |
> | Acção | Microsoft.ApiManagement/service/apis/tags/read | Apresenta uma lista de todas as marcas associadas com a API. ou obtenha de etiqueta associada com a API. |
> | Acção | Microsoft.ApiManagement/service/apis/tags/write | Atribua etiqueta para a Api. |
> | Acção | Microsoft.ApiManagement/service/apis/write | Cria nova ou de atualizações de API especificada existente da instância de serviço de gestão de API. Atualizações ou a API especificada da instância de serviço de gestão de API. |
> | Acção | Microsoft.ApiManagement/service/apisByTags/read | Apresenta uma lista de uma coleção de apis associadas com etiquetas. |
> | Acção | Microsoft.ApiManagement/service/apiVersionSets/delete | Elimina o conjunto da versão de Api específica. |
> | Acção | Microsoft.ApiManagement/service/apiVersionSets/read | Apresenta uma lista de uma coleção de conjuntos da versão de API na instância do serviço especificado. ou obtém os detalhes do conjunto de versão de Api especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Obter lista de entidades de versão |
> | Acção | Microsoft.ApiManagement/service/apiVersionSets/write | Cria ou atualiza um conjunto de versão de Api. Atualizações ou os detalhes do VersionSet de Api especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Atualiza os recursos de Microsoft.ApiManagement executados na rede Virtual para escolher as definições de rede atualizado. |
> | Acção | Microsoft.ApiManagement/service/authorizationServers/delete | Elimina a instância de servidor de autorização específico. |
> | Acção | Microsoft.ApiManagement/service/authorizationServers/read | Apresenta uma lista de uma coleção de servidores de autorização definida dentro de uma instância de serviço. ou obtém os detalhes do servidor de autorização especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/authorizationServers/write | Cria o novo servidor de autorização ou atualiza um servidor de autorização existente. Atualizações ou os detalhes do servidor de autorização especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/backends/delete | Elimina o back-end especificado. |
> | Acção | Microsoft.ApiManagement/service/backends/read | Apresenta uma lista de uma coleção de back-ends na instância do serviço especificado. ou obtém os detalhes de back-end especificado pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/backends/reconnect/action | Notifica o proxy APIM para criar uma nova ligação para o back-end após o tempo limite especificado. Não se foi especificado nenhum tempo de limite, é utilizado o tempo limite de 2 minutos. |
> | Acção | Microsoft.ApiManagement/service/backends/write | Cria ou atualiza um back-end. ou atualiza um back-end existente. |
> | Acção | Microsoft.ApiManagement/service/backup/action | Serviço de gestão de API de cópia de segurança no contentor especificado num utilizador forneceu a conta de armazenamento |
> | Acção | Microsoft.ApiManagement/service/caches/delete | Elimina o Cache específico. |
> | Acção | Microsoft.ApiManagement/service/caches/read | Apresenta uma lista de uma coleção de todas as Caches externas na instância do serviço especificado. ou obtém os detalhes da Cache especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/caches/write | Cria ou atualiza um Cache externo a ser utilizado na instância de gestão de Api. Atualizações ou os detalhes da cache especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/certificates/delete | Elimina o certificado específico. |
> | Acção | Microsoft.ApiManagement/service/certificates/read | Apresenta uma lista de uma coleção de todos os certificados na instância do serviço especificado. ou obtém os detalhes do certificado especificado pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/certificates/write | Cria ou atualiza o certificado que está a ser utilizado para a autenticação com o back-end. |
> | Acção | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Remove especificado o item de conteúdo. |
> | Acção | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Devolve a lista de itens de conteúdo ou detalhes do item de conteúdo de devolve |
> | Acção | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Cria o novo item de conteúdo ou atualiza o item de conteúdo especificado |
> | Acção | Microsoft.ApiManagement/service/contentTypes/read | Devolve a lista de tipos de conteúdo |
> | Acção | Microsoft.ApiManagement/service/delete | Eliminar a instância de serviço de gestão de API |
> | Acção | Microsoft.ApiManagement/service/diagnostics/delete | Elimina o diagnóstico especificado. |
> | Acção | Microsoft.ApiManagement/service/diagnostics/read | Apresenta uma lista de todos os diagnósticos da instância de serviço de gestão de API. ou obtém os detalhes do diagnóstico especificado pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/diagnostics/write | Cria um novo diagnóstico ou atualiza um já existente. Atualizações ou os detalhes do diagnóstico especificado pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/getssotoken/action | Obtém o token de SSO que pode ser utilizado para iniciar sessão no portal de legado de serviço de gestão de API como administrador |
> | Acção | Microsoft.ApiManagement/service/groups/delete | Elimina o grupo específico de instância do serviço de gestão de API. |
> | Acção | Microsoft.ApiManagement/service/groups/read | Apresenta uma lista de uma coleção de grupos definidos dentro de uma instância de serviço. ou obtém os detalhes do grupo especificado pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/groups/users/delete | Remova utilizador existente do grupo existente. |
> | Acção | Microsoft.ApiManagement/service/groups/users/read | Apresenta uma lista de uma coleção de entidades de utilizador associado ao grupo. |
> | Acção | Microsoft.ApiManagement/service/groups/users/write | Adicionar utilizador existente para o grupo existente |
> | Acção | Microsoft.ApiManagement/service/groups/write | Cria ou atualiza um grupo. Atualizações ou os detalhes do grupo especificado pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/identityProviders/delete | Elimina a configuração do fornecedor de identidade especificada. |
> | Acção | Microsoft.ApiManagement/service/identityProviders/read | Apresenta uma lista de uma coleção de fornecedor de identidade configurado na instância do serviço especificado. Obtém os detalhes de configuração do fornecedor de identidade configurada ou numa instância de serviço especificado. |
> | Acção | Microsoft.ApiManagement/service/identityProviders/write | Cria ou atualiza a configuração de IdentityProvider. ou atualiza uma configuração de IdentityProvider existente. |
> | Acção | Microsoft.ApiManagement/service/issues/read | Apresenta uma lista de uma coleção de problemas na instância do serviço especificado. ou detalhes do problema é a gestão de API |
> | Acção | Microsoft.ApiManagement/service/locations/networkstatus/read | Obtém o estado de acesso de rede de recursos no qual o serviço depende na localização. |
> | Acção | Microsoft.ApiManagement/service/loggers/delete | Elimina o agente de log especificado. |
> | Acção | Microsoft.ApiManagement/service/loggers/read | Apresenta uma lista de uma coleção de agentes na instância do serviço especificado. ou obtém os detalhes de agente especificado pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/loggers/write | Cria ou atualiza um agente de log. ou atualiza um agente de log existente. |
> | Acção | Microsoft.ApiManagement/service/managedeployments/action | Alterar o SKU/unidades, adicionar ou remover implementações regionais do serviço de gestão de API |
> | Acção | Microsoft.ApiManagement/service/networkstatus/read | Obtém o estado de acesso de rede de recursos no qual o serviço depende. |
> | Acção | Microsoft.ApiManagement/service/notifications/action | Envia a notificação de um utilizador especificado |
> | Acção | Microsoft.ApiManagement/service/notifications/read | Apresenta uma lista de uma coleção de propriedades definidas dentro de uma instância de serviço. ou obtém os detalhes da notificação especificado pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Remove o e-mail da lista de notificação. |
> | Acção | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Obtém a lista de mensagens de E-Mail de destinatário da notificação subscrever uma notificação. |
> | Acção | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Adiciona o endereço de E-Mail para a lista de destinatários da notificação. |
> | Acção | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Remove o utilizador de gestão de API a partir da lista de notificação. |
> | Acção | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Obtém a lista do utilizador de destinatário da notificação subscrever a notificação. |
> | Acção | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Adiciona o utilizador de gestão de API à lista de destinatários da notificação. |
> | Acção | Microsoft.ApiManagement/service/notifications/write | Criar ou notificação de editor de gestão de API de atualização. |
> | Acção | Microsoft.ApiManagement/service/openidConnectProviders/delete | Elimina o OpenID Connect provedor específico da instância de serviço de gestão de API. |
> | Acção | Microsoft.ApiManagement/service/openidConnectProviders/read | Listas de todos os fornecedores de ligação do OpenId. ou obtém específico fornecedor de ligação OpenID. |
> | Acção | Microsoft.ApiManagement/service/openidConnectProviders/write | Cria ou atualiza o fornecedor de ligação OpenID. Atualizações ou o fornecedor de ligação do OpenID específico. |
> | Acção | Microsoft.ApiManagement/service/operationresults/read | Obtém o estado atual da operação de longa |
> | Acção | Microsoft.ApiManagement/service/policies/delete | Elimina a configuração de política global do serviço de gestão de Api. |
> | Acção | Microsoft.ApiManagement/service/policies/read | Apresenta uma lista de todas as definições de política Global do serviço de gestão de Api. ou obtenha a definição de política Global do serviço de gestão de Api. |
> | Acção | Microsoft.ApiManagement/service/policies/write | Cria ou atualiza a configuração da política global do serviço de gestão de Api. |
> | Acção | Microsoft.ApiManagement/service/policy/delete | Eliminar a configuração de política ao nível do inquilino |
> | Acção | Microsoft.ApiManagement/service/policy/read | Obter a configuração de política ao nível do inquilino |
> | Acção | Microsoft.ApiManagement/service/policy/write | Criar configuração de política de nível no inquilino |
> | Acção | Microsoft.ApiManagement/service/policySnippets/read | Apresenta uma lista de todos os fragmentos de política. |
> | Acção | Microsoft.ApiManagement/service/portalsettings/read | Obter o início de sessão nas definições para o Portal ou início de sessão de Get configurar definições para o Portal ou obter as definições de delegação para o Portal. |
> | Acção | Microsoft.ApiManagement/service/portalsettings/write | Atualize as definições de início de sessão. ou definições de criação ou atualização de início de sessão. ou as definições de atualização inscrever-se ou as definições de atualização inscrever-se ou as definições de delegação de atualização. ou definições de criação ou atualização de delegação. |
> | Acção | Microsoft.ApiManagement/service/products/apis/delete | Elimina a API especificada do produto especificado. |
> | Acção | Microsoft.ApiManagement/service/products/apis/read | Listas de uma coleção de APIs associadas com um produto. |
> | Acção | Microsoft.ApiManagement/service/products/apis/write | Adiciona uma API ao produto especificado. |
> | Acção | Microsoft.ApiManagement/service/products/delete | Elimine o produto. |
> | Acção | Microsoft.ApiManagement/service/products/groups/delete | Elimina a associação entre o grupo especificado e o produto. |
> | Acção | Microsoft.ApiManagement/service/products/groups/read | Lista a coleção de grupos de desenvolvedor associados com o produto especificado. |
> | Acção | Microsoft.ApiManagement/service/products/groups/write | Adiciona a associação entre o grupo de desenvolvedor especificado com o produto especificado. |
> | Acção | Microsoft.ApiManagement/service/products/policies/delete | Elimina a configuração de política, o produto. |
> | Acção | Microsoft.ApiManagement/service/products/policies/read | Obter a configuração de política ao nível do produto. ou obtenha a configuração da política ao nível do produto. |
> | Acção | Microsoft.ApiManagement/service/products/policies/write | Cria ou atualiza a configuração da política para o produto. |
> | Acção | Microsoft.ApiManagement/service/products/policy/delete | Eliminar a configuração de política ao nível do produto |
> | Acção | Microsoft.ApiManagement/service/products/policy/read | Obter a configuração de política ao nível do produto |
> | Acção | Microsoft.ApiManagement/service/products/policy/write | Criar configuração de política no produto nível |
> | Acção | Microsoft.ApiManagement/service/products/read | Apresenta uma lista de uma coleção de produtos na instância do serviço especificado. ou obtém os detalhes do produto especificado pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/products/subscriptions/read | Lista o conjunto de subscrições para o produto especificado. |
> | Acção | Microsoft.ApiManagement/service/products/tags/delete | Anular a exposição a marca do produto. |
> | Acção | Microsoft.ApiManagement/service/products/tags/read | Apresenta uma lista de todas as marcas associadas com o produto. ou obtenha de etiqueta associada ao produto. |
> | Acção | Microsoft.ApiManagement/service/products/tags/write | Atribua etiqueta para o produto. |
> | Acção | Microsoft.ApiManagement/service/products/write | Cria ou atualiza um produto. ou atualizar detalhes do produto existente. |
> | Acção | Microsoft.ApiManagement/service/productsByTags/read | Apresenta uma lista de uma coleção de produtos associados com etiquetas. |
> | Acção | Microsoft.ApiManagement/service/properties/delete | Elimina a propriedade específica da instância do serviço de gestão de API. |
> | Acção | Microsoft.ApiManagement/service/properties/read | Apresenta uma lista de uma coleção de propriedades definidas dentro de uma instância de serviço. ou obtém os detalhes da propriedade especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/properties/write | Cria ou atualiza uma propriedade. ou atualiza a propriedade específica. |
> | Acção | Microsoft.ApiManagement/service/quotas/periods/read | Obter o valor do contador de quota para o período |
> | Acção | Microsoft.ApiManagement/service/quotas/periods/write | Defina o valor atual de contador de quota |
> | Acção | Microsoft.ApiManagement/service/quotas/read | Obter os valores para a quota |
> | Acção | Microsoft.ApiManagement/service/quotas/write | Defina o valor atual de contador de quota |
> | Acção | Microsoft.ApiManagement/service/read | Ler metadados para uma instância de serviço de gestão de API |
> | Acção | Microsoft.ApiManagement/service/regions/read | Apresenta uma lista de todas as regiões do azure na qual o serviço existe. |
> | Acção | Microsoft.ApiManagement/service/reports/read | Obter relatório agregado por períodos de tempo ou de relatório de Get agregados por região geográfica ou relatório Get agregados por desenvolvedores.<br>ou obtenha relatórios agregados por produtos.<br>ou obtenha relatórios agregados pelo relatório APIs ou Get agregado por operações ou o relatório de Get agregados por subscrição.<br>ou obter pedidos de dados de relatórios |
> | Acção | Microsoft.ApiManagement/service/restore/action | Restaurar o serviço de gestão de API do contêiner especificado um utilizador fornecido a conta de armazenamento |
> | Acção | Microsoft.ApiManagement/service/subscriptions/delete | Elimina a subscrição especificada. |
> | Acção | Microsoft.ApiManagement/service/subscriptions/read | Apresenta uma lista de todas as subscrições da instância de serviço de gestão de API. ou obtém a entidade de subscrição especificada. |
> | Acção | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Regenera a chave primária de uma subscrição existente da instância de serviço de gestão de API. |
> | Acção | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Regenera a chave secundária de uma subscrição existente da instância de serviço de gestão de API. |
> | Acção | Microsoft.ApiManagement/service/subscriptions/write | Cria ou atualiza a subscrição de utilizador especificado para o produto especificado. Atualizações ou os detalhes de uma subscrição especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/tagResources/read | Apresenta uma lista de uma coleção de recursos associados com etiquetas. |
> | Acção | Microsoft.ApiManagement/service/tags/delete | Elimina uma etiqueta específica da instância de serviço de gestão de API. |
> | Acção | Microsoft.ApiManagement/service/tags/read | Apresenta uma lista de uma coleção de etiquetas definido dentro de uma instância de serviço. ou obtém os detalhes da etiqueta especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/tags/write | Cria uma etiqueta. Atualizações ou os detalhes da etiqueta especificada pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/templates/delete | Repor o modelo de correio eletrónico de gestão de API de predefinição |
> | Acção | Microsoft.ApiManagement/service/templates/read | Obtém todos os modelos de e-mail ou correio eletrónico de gestão de API obtém os detalhes do modelo |
> | Acção | Microsoft.ApiManagement/service/templates/write | Criar ou atualizar o modelo de correio eletrónico de gestão de API ou o modelo de correio eletrónico de gestão de API de atualizações |
> | Acção | Microsoft.ApiManagement/service/tenant/delete | Remover configuração de política para o inquilino |
> | Acção | Microsoft.ApiManagement/service/tenant/deploy/action | Executa uma tarefa de implementação para aplicar as alterações da ramificação git especificado para a configuração na base de dados. |
> | Acção | Microsoft.ApiManagement/service/tenant/operationResults/read | Obter lista de resultados da operação ou obter o resultado de uma operação específica |
> | Acção | Microsoft.ApiManagement/service/tenant/read | Obter a definição de política Global do serviço de gestão de Api. ou Obtenha detalhes de informações de acesso de inquilino |
> | Acção | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Regenerar chave de acesso primária |
> | Acção | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Regenerar chave de acesso secundária |
> | Acção | Microsoft.ApiManagement/service/tenant/save/action | Cria a consolidação com instantâneo de configuração para o ramo especificado no repositório |
> | Acção | Microsoft.ApiManagement/service/tenant/syncState/read | Obter o estado da última sincronização do git |
> | Acção | Microsoft.ApiManagement/service/tenant/validate/action | Valida as alterações da ramificação git especificado |
> | Acção | Microsoft.ApiManagement/service/tenant/write | Definir política de configuração para o inquilino ou detalhes de informações de acesso de inquilino de atualização |
> | Acção | Microsoft.ApiManagement/service/updatecertificate/action | Carregar o certificado SSL para um serviço de gestão de API |
> | Acção | Microsoft.ApiManagement/service/updatehostname/action | Configurar, atualizar ou remover nomes de domínio personalizado para um serviço de gestão de API |
> | Acção | Microsoft.ApiManagement/service/users/action | Registar um novo utilizador |
> | Acção | Microsoft.ApiManagement/service/users/confirmations/send/action | Envia a confirmação |
> | Acção | Microsoft.ApiManagement/service/users/delete | Elimina o utilizador específico. |
> | Acção | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Obtém um URL de redirecionamento que contém um token de autenticação para um determinado utilizador a iniciar sessão no portal do programador. |
> | Acção | Microsoft.ApiManagement/service/users/groups/read | Apresenta uma lista de todos os grupos de utilizadores. |
> | Acção | Microsoft.ApiManagement/service/users/identities/read | Lista de todas as identidades de utilizador. |
> | Acção | Microsoft.ApiManagement/service/users/keys/read | Obter chaves associadas a utilizador |
> | Acção | Microsoft.ApiManagement/service/users/read | Apresenta uma lista de uma coleção de utilizadores registados na instância do serviço especificado. ou obtém os detalhes de utilizador especificado pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/users/subscriptions/read | Lista o conjunto de subscrições do utilizador especificado. |
> | Acção | Microsoft.ApiManagement/service/users/token/action | Obtém a autorização de acesso partilhado Token para o utilizador. |
> | Acção | Microsoft.ApiManagement/service/users/write | Cria ou atualiza um utilizador. Atualizações ou os detalhes de utilizador especificado pelo respetivo identificador. |
> | Acção | Microsoft.ApiManagement/service/write | Criar uma nova instância do serviço de gestão de API |
> | Acção | Microsoft.ApiManagement/unregister/action | Anular o registo subscrição para o fornecedor de recursos de Microsoft.ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Authorization/classicAdministrators/delete | Remove o administrador da subscrição. |
> | Acção | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Obtém os estados de funcionamento do administrador da subscrição. |
> | Acção | Microsoft.Authorization/classicAdministrators/read | Lê os administradores da subscrição. |
> | Acção | Microsoft.Authorization/classicAdministrators/write | Adiciona ou modifica o administrador de uma subscrição. |
> | Acção | Microsoft.Authorization/denyAssignments/delete | Elimine uma atribuição de negação no âmbito especificado. |
> | Acção | Microsoft.Authorization/denyAssignments/read | Obtenha informações sobre uma atribuição de negação. |
> | Acção | Microsoft.Authorization/denyAssignments/write | Crie uma atribuição de negação no âmbito especificado. |
> | Acção | Microsoft.Authorization/elevateAccess/action | Garante ao chamador Administrador de Acesso dos Utilizadores acesso ao âmbito do inquilino |
> | Acção | Microsoft.Authorization/locks/delete | Elimina bloqueios no âmbito especificado. |
> | Acção | Microsoft.Authorization/locks/read | Obtém bloqueios no âmbito especificado. |
> | Acção | Microsoft.Authorization/locks/write | Adiciona bloqueios ao âmbito especificado. |
> | Acção | Microsoft.Authorization/operations/read | Obtém a lista de operações |
> | Acção | Microsoft.Authorization/permissions/read | Lista todas as permissões que o chamador tem num determinado âmbito. |
> | Acção | Microsoft.Authorization/policyAssignments/delete | Elimine uma atribuição de política no âmbito especificado. |
> | Acção | Microsoft.Authorization/policyAssignments/read | Obtenha informação sobre uma atribuição de política. |
> | Acção | Microsoft.Authorization/policyAssignments/write | Crie uma atribuição de política no âmbito especificado. |
> | Acção | Microsoft.Authorization/policyDefinitions/delete | Elimine uma definição de política. |
> | Acção | Microsoft.Authorization/policyDefinitions/read | Obtenha informação sobre uma definição de política. |
> | Acção | Microsoft.Authorization/policyDefinitions/write | Crie uma definição de política personalizada. |
> | Acção | Microsoft.Authorization/policySetDefinitions/delete | Elimine uma definição do conjunto de políticas. |
> | Acção | Microsoft.Authorization/policySetDefinitions/read | Obter informações sobre uma definição do conjunto de políticas. |
> | Acção | Microsoft.Authorization/policySetDefinitions/write | Crie uma definição do conjunto de políticas personalizada. |
> | Acção | Microsoft.Authorization/providerOperations/read | Obter operações para todos os fornecedores de recursos que possam ser utilizadas em definições de funções. |
> | Acção | Microsoft.Authorization/roleAssignments/delete | Elimina uma atribuição de função no âmbito especificado. |
> | Acção | Microsoft.Authorization/roleAssignments/read | Obtém informações sobre uma atribuição de função. |
> | Acção | Microsoft.Authorization/roleAssignments/write | Cria uma atribuição de função no âmbito especificado. |
> | Acção | Microsoft.Authorization/roleDefinitions/delete | Eliminar a definição de função personalizada especificada. |
> | Acção | Microsoft.Authorization/roleDefinitions/read | Obtém informação sobre uma definição de função. |
> | Acção | Microsoft.Authorization/roleDefinitions/write | Criar ou atualizar uma definição de função personalizada com permissões especificadas e âmbitos atribuíveis. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Ler as informações de registo do DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Escreve um pedido para voltar a gerar chaves de DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/certificates/delete | Elimina um recurso de certificado da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/certificates/getCount/action | Lê a contagem de certificados |
> | Acção | Microsoft.Automation/automationAccounts/certificates/read | Obtém um recurso de certificado da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/certificates/write | Cria ou atualiza um recurso de certificado da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/compilationjobs/read | Lê a compilação do DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/compilationjobs/read | Lê a compilação do DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/compilationjobs/write | Escreve a compilação do DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/compilationjobs/write | Escreve a compilação do DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/configurations/content/read | Lê o conteúdo do suporte de dados de configuração |
> | Acção | Microsoft.Automation/automationAccounts/configurations/delete | Elimina o conteúdo do DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/configurations/getCount/action | Lê a contagem de conteúdo do DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/configurations/read | Obtém o conteúdo do DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/configurations/write | Escreve o conteúdo do DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/connections/delete | Elimina um recurso de ligação da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/connections/getCount/action | Lê a contagem de ligações |
> | Acção | Microsoft.Automation/automationAccounts/connections/read | Obtém um recurso de ligação da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/connections/write | Cria ou atualiza um recurso de ligação da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/connectionTypes/delete | Elimina um recurso de tipo de ligação da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/connectionTypes/read | Obtém um recurso de tipo de ligação da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/connectionTypes/write | Cria um recurso de tipo de ligação da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/credentials/delete | Elimina um recurso de credencial da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/credentials/getCount/action | Lê a contagem de credenciais |
> | Acção | Microsoft.Automation/automationAccounts/credentials/read | Obtém um recurso de credencial da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/credentials/write | Cria ou atualiza um recurso de credenciais da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/delete | Elimina uma conta de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Elimina recursos de trabalho de Runbook híbrida |
> | Acção | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lê recursos de trabalho de Runbook híbrida |
> | Acção | Microsoft.Automation/automationAccounts/jobs/output/read | Obtém o resultado de uma tarefa |
> | Acção | Microsoft.Automation/automationAccounts/jobs/read | Obtém uma tarefa da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/jobs/resume/action | Retoma uma tarefa da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Obtém o conteúdo do runbook de automatização do Azure no momento da execução da tarefa |
> | Acção | Microsoft.Automation/automationAccounts/jobs/stop/action | Para uma tarefa da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtém um fluxo de tarefas de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtém um fluxo de tarefas de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende uma tarefa da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/jobs/write | Cria uma tarefa da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/jobSchedules/delete | Elimina um plano de trabalho de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/jobSchedules/read | Obtém um plano de trabalho de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/jobSchedules/write | Cria um plano de trabalho de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtém a área de trabalho ligada à conta de automatização |
> | Acção | Microsoft.Automation/automationAccounts/listKeys/action | Lê as chaves da conta de automatização |
> | Acção | Microsoft.Automation/automationAccounts/modules/activities/read | Obtém as actividades de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/modules/delete | Elimina um módulo do Powershell da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/modules/getCount/action | Obtém a contagem de módulos do Powershell na conta de automatização |
> | Acção | Microsoft.Automation/automationAccounts/modules/read | Obtém um módulo do Powershell da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/modules/write | Cria ou atualiza um módulo do Powershell da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Elimina a configuração do nó do DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Lê o conteúdo de configuração do nó do DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Lê a configuração do nó do DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Escreve a configuração do nó do DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/nodecounts/read | Lê o resumo de contagem de nós para o tipo especificado |
> | Acção | Microsoft.Automation/automationAccounts/nodes/delete | Elimina nós de DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/nodes/read | Lê nós de DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Lê o conteúdo do relatório de DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/nodes/reports/read | Lê os relatórios de DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/nodes/write | Cria ou atualiza nós de DSC de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Obtém TypeFields de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/python2Packages/delete | Elimina um pacote de Python 2 automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/python2Packages/read | Obtém um pacote de Python 2 automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/python2Packages/write | Cria ou atualiza um pacote de Python 2 automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/python3Packages/delete | Elimina um pacote de Python 3 automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/python3Packages/read | Obtém um pacote de Python 3 automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/python3Packages/write | Cria ou atualiza um pacote de Python 3 automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/read | Obtém uma conta de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/content/read | Obtém o conteúdo de um runbook da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/delete | Elimina um runbook da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Cria o conteúdo de um rascunho de runbook da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Obtém os resultados da operação de rascunho de runbook da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/draft/read | Obtém um rascunho de runbook da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Obtém uma tarefa de teste de rascunho de runbook de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Retoma uma tarefa de teste de rascunho de runbook de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Parar uma tarefa de teste de rascunho de runbook de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Suspende uma tarefa de teste de rascunho de runbook de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Cria uma tarefa de teste de rascunho de runbook de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Anular edições feitas um rascunho de runbook da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Obtém o número de runbooks da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publica um rascunho de runbook da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/read | Obtém um runbook da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/runbooks/write | Cria ou atualiza um runbook da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/schedules/delete | Elimina um recurso de agenda da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/schedules/getCount/action | Obtém a contagem de agendas da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/schedules/read | Obtém um recurso de agenda da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/schedules/write | Cria ou atualiza um recurso de agenda da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Elimina uma configuração de atualização de Software de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Obtém uma configuração de atualização de Software de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Cria ou atualiza a configuração de atualização de Software de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/statistics/read | Obtém estatísticas de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Obter um computador de implementação de atualização de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Obtém uma tarefa de patch de gestão de atualização de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/usages/read | Obtém a utilização da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/variables/delete | Elimina um recurso de variável da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/variables/read | Lê um recurso de variável da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/variables/write | Cria ou atualiza um recurso de variável da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/watchers/delete | Eliminar uma tarefa de observador de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/watchers/read | Obtém uma tarefa de observador de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/watchers/start/action | Iniciar uma tarefa de observador de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/watchers/stop/action | Parar uma tarefa de observador de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/watchers/streams/read | Obtém um fluxo de tarefas do observador de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Eliminar um ações de tarefas do observador de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Obtém um observador de automatização do Azure ações de tarefas |
> | Acção | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Criar ações de tarefas de um observador de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/watchers/write | Cria uma tarefa de observador de automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/webhooks/action | Gera um URI para um webhook da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/webhooks/delete | Elimina um webhook da automatização do Azure  |
> | Acção | Microsoft.Automation/automationAccounts/webhooks/read | Lê um webhook da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/webhooks/write | Cria ou atualiza um webhook da automatização do Azure |
> | Acção | Microsoft.Automation/automationAccounts/write | Cria ou atualiza uma conta de automatização do Azure |
> | Acção | Microsoft.Automation/operations/read | Obtém as operações disponíveis para os recursos de automatização do Azure |
> | Acção | Microsoft.Automation/register/action | Regista a subscrição da automatização do Azure |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Eliminar o recurso do Diretório B2C |
> | Acção | Microsoft.AzureActiveDirectory/b2cDirectories/read | Visualizar o recurso do Diretório B2C |
> | Acção | Microsoft.AzureActiveDirectory/b2cDirectories/write | Criar ou atualizar o recurso de diretório B2C |
> | Acção | Microsoft.AzureActiveDirectory/b2ctenants/read | Lista todos os inquilinos B2C em que o utilizador é membro |
> | Acção | Microsoft.AzureActiveDirectory/operations/read | Ler todas as operações de API disponíveis para o fornecedor de recursos de Microsoft.AzureActiveDirectory |
> | Acção | Microsoft.AzureActiveDirectory/register/action | Registar subscrição para o fornecedor de recursos de Microsoft.AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.AzureStack/Operations/read | Obtém as propriedades de uma operação de fornecedor de recursos |
> | Acção | Microsoft.AzureStack/register/action | Regista a subscrição no fornecedor de recursos de Microsoft.AzureStack |
> | Acção | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Elimina uma subscrição de cliente do Azure Stack |
> | Acção | Microsoft.AzureStack/registrations/customerSubscriptions/read | Obtém as propriedades de uma subscrição de cliente do Azure Stack |
> | Acção | Microsoft.AzureStack/registrations/customerSubscriptions/write | Cria ou atualiza uma subscrição de cliente do Azure Stack |
> | Acção | Microsoft.AzureStack/registrations/delete | Elimina um registo do Azure Stack |
> | Acção | Microsoft.AzureStack/registrations/getActivationKey/action | Obtém a chave de ativação mais recente do Azure Stack |
> | Acção | Microsoft.AzureStack/registrations/products/listDetails/action | Obtém estendido detalhes para um produto do Azure Stack Marketplace |
> | Acção | Microsoft.AzureStack/registrations/products/read | Obtém as propriedades de um produto do Azure Stack Marketplace |
> | Acção | Microsoft.AzureStack/registrations/read | Obtém as propriedades de um registo do Azure Stack |
> | Acção | Microsoft.AzureStack/registrations/write | Cria ou atualiza um registo do Azure Stack |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Batch/batchAccounts/applications/delete | Elimina uma aplicação |
> | Acção | Microsoft.Batch/batchAccounts/applications/read | Apresenta uma lista de aplicações ou obtém as propriedades de uma aplicação |
> | Acção | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Ativa um pacote de aplicação |
> | Acção | Microsoft.Batch/batchAccounts/applications/versions/delete | Elimina um pacote de aplicação |
> | Acção | Microsoft.Batch/batchAccounts/applications/versions/read | Obtém as propriedades de um pacote de aplicação |
> | Acção | Microsoft.Batch/batchAccounts/applications/versions/write | Cria um novo pacote de aplicações ou atualiza um pacote de aplicação existente |
> | Acção | Microsoft.Batch/batchAccounts/applications/write | Cria uma nova aplicação ou atualiza uma aplicação existente |
> | Acção | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Obtém os resultados de uma operação de certificado de longa execução numa conta do Batch |
> | Acção | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Cancela a falha na eliminação de um certificado de uma conta do Batch |
> | Acção | Microsoft.Batch/batchAccounts/certificates/delete | Elimina um certificado de uma conta do Batch |
> | Acção | Microsoft.Batch/batchAccounts/certificates/read | Apresenta uma lista de certificados numa conta do Batch ou obtém as propriedades de um certificado |
> | Acção | Microsoft.Batch/batchAccounts/certificates/write | Cria um novo certificado numa conta do Batch ou atualiza um certificado existente |
> | Acção | Microsoft.Batch/batchAccounts/delete | Elimina uma conta do Batch |
> | Acção | Microsoft.Batch/batchAccounts/listkeys/action | Listas de aceder a chaves para uma conta do Batch |
> | Acção | Microsoft.Batch/batchAccounts/operationResults/read | Obtém os resultados de uma longa operação de conta do Batch |
> | Acção | Microsoft.Batch/batchAccounts/poolOperationResults/read | Obtém os resultados de uma operação de agrupamento de longa execução numa conta do Batch |
> | Acção | Microsoft.Batch/batchAccounts/pools/delete | Elimina um agrupamento de uma conta do Batch |
> | Acção | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Desativa o dimensionamento automático para um conjunto de conta do Batch |
> | Acção | Microsoft.Batch/batchAccounts/pools/read | Apresenta uma lista de agrupamentos de uma conta do Batch ou obtém as propriedades de um agrupamento |
> | Acção | Microsoft.Batch/batchAccounts/pools/stopResize/action | Redimensionar de paragens em curso uma operação num conjunto de conta do Batch |
> | Acção | Microsoft.Batch/batchAccounts/pools/write | Cria um novo conjunto numa conta do Batch ou atualiza um conjunto existente |
> | Acção | Microsoft.Batch/batchAccounts/read | Apresenta uma lista de contas do Batch ou obtém as propriedades de uma conta do Batch |
> | Acção | Microsoft.Batch/batchAccounts/regeneratekeys/action | Regenera chaves para uma conta do Batch de acesso |
> | Acção | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Sincroniza as chaves de acesso para a conta de armazenamento automática configuradas para uma conta do Batch |
> | Acção | Microsoft.Batch/batchAccounts/write | Cria uma nova conta do Batch ou atualiza uma conta existente do Batch |
> | Acção | Microsoft.Batch/locations/accountOperationResults/read | Obtém os resultados de uma longa operação de conta do Batch |
> | Acção | Microsoft.Batch/locations/checkNameAvailability/action | Verifica se o nome da conta é válido e não está em utilização. |
> | Acção | Microsoft.Batch/locations/quotas/read | Obtém as quotas de lote da subscrição especificada na região do Azure especificada |
> | Acção | Microsoft.Batch/operations/read | Lista as operações disponíveis no fornecedor de recursos do batch |
> | Acção | Microsoft.Batch/register/action | Regista a subscrição para o fornecedor de recursos do Batch e permite a criação de contas do Batch |
> | Acção | Microsoft.Batch/unregister/action | Anula o registo da subscrição para o fornecedor de recursos do Batch que impede a criação de contas do Batch |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Billing/billingAccounts/departments/read | Lista de todos os departamentos num âmbito de conta de faturação |
> | Acção | Microsoft.Billing/billingAccounts/enrollmentAccounts/read | Listar todas as contas de inscrição num âmbito de conta de faturação |
> | Acção | Microsoft.Billing/billingAccounts/read | Listar todas as contas de faturas que utilizador tem acesso à |
> | Acção | Microsoft.Billing/billingPeriods/read | Apresenta uma lista de períodos de faturação disponíveis |
> | Acção | Microsoft.Billing/departments/read | Lista de todos os departamentos que utilizador tem acesso à |
> | Acção | Microsoft.Billing/invoices/read | Notas fiscais disponíveis de listas |
> | Acção | Microsoft.Billing/register/action | Regista a subscrição no fornecedor de recursos de Microsoft.Billing |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.BingMaps/mapApis/Delete | Operação de Eliminação |
> | Acção | Microsoft.BingMaps/mapApis/listSecrets/action | Listar os Segredos |
> | Acção | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Ler Token de Autorização de Início de Sessão Único para o Recurso |
> | Acção | Microsoft.BingMaps/mapApis/Read | Operação de Leitura |
> | Acção | Microsoft.BingMaps/mapApis/regenerateKey/action | Regenera a Chave |
> | Acção | Microsoft.BingMaps/mapApis/Write | Operação de Escrita |
> | Acção | Microsoft.BingMaps/Operations/read | Descrição da operação. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Blockchain/blockchainMembers/delete | Elimina um membro de Blockchain existente. |
> | Acção | Microsoft.Blockchain/blockchainMembers/read | Obtém ou lista existente de Blockchain de membro ou membros. |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Liga-se a um nó de transação de membro de Blockchain. |
> | Acção | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Elimina um nó de transação de membro de Blockchain existente. |
> | Acção | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Obtém ou lista de nós de transação de membro de Blockchain existente. |
> | Acção | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Cria ou atualiza um nó de transação de membro de Blockchain. |
> | Acção | Microsoft.Blockchain/blockchainMembers/write | Cria ou atualiza um membro de Blockchain. |
> | Acção | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Obtém os resultados da operação de membros de Blockchain. |
> | Acção | Microsoft.Blockchain/locations/checkNameAvailability/action | Verifica esse nome de recurso é válido e não está em utilização. |
> | Acção | Microsoft.Blockchain/operations/read | Liste todas as operações de fornecedor de recursos do Microsoft Blockchain. |
> | Acção | Microsoft.Blockchain/register/action | Regista a subscrição para o fornecedor de recursos de Blockchain. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | Ler quaisquer artefactos de esquemas |
> | Acção | Microsoft.Blueprint/blueprintAssignments/delete | Eliminar quaisquer artefactos de esquema |
> | Acção | Microsoft.Blueprint/blueprintAssignments/read | Ler quaisquer artefactos de esquemas |
> | Acção | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | Obtenha o ID de objeto do principal do serviço Azure Blueprints. |
> | Acção | Microsoft.Blueprint/blueprintAssignments/write | Criar ou atualizar artefactos de esquema |
> | Acção | Microsoft.Blueprint/blueprints/artifacts/delete | Eliminar quaisquer artefactos de esquema |
> | Acção | Microsoft.Blueprint/blueprints/artifacts/read | Ler quaisquer artefactos de esquemas |
> | Acção | Microsoft.Blueprint/blueprints/artifacts/write | Criar ou atualizar artefactos de esquema |
> | Acção | Microsoft.Blueprint/blueprints/delete | Eliminar quaisquer esquemas |
> | Acção | Microsoft.Blueprint/blueprints/read | Ler quaisquer esquemas |
> | Acção | Microsoft.Blueprint/blueprints/versions/artifacts/read | Ler quaisquer artefactos de esquemas |
> | Acção | Microsoft.Blueprint/blueprints/versions/delete | Eliminar quaisquer esquemas |
> | Acção | Microsoft.Blueprint/blueprints/versions/read | Ler quaisquer esquemas |
> | Acção | Microsoft.Blueprint/blueprints/versions/write | Criar ou atualizar quaisquer esquemas |
> | Acção | Microsoft.Blueprint/blueprints/write | Criar ou atualizar quaisquer esquemas |
> | Acção | Microsoft.Blueprint/register/action | Regista o Fornecedor de Recursos do Azure Blueprints |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.BotService/botServices/channels/delete | Eliminar um serviço de Bot |
> | Acção | Microsoft.BotService/botServices/channels/read | Ler um serviço de Bot |
> | Acção | Microsoft.BotService/botServices/channels/write | Escrever um serviço de Bot |
> | Acção | Microsoft.BotService/botServices/connections/delete | Eliminar um serviço de Bot |
> | Acção | Microsoft.BotService/botServices/connections/read | Ler um serviço de Bot |
> | Acção | Microsoft.BotService/botServices/connections/write | Escrever um serviço de Bot |
> | Acção | Microsoft.BotService/botServices/delete | Eliminar um serviço de Bot |
> | Acção | Microsoft.BotService/botServices/read | Ler um serviço de Bot |
> | Acção | Microsoft.BotService/botServices/write | Escrever um serviço de Bot |
> | Acção | Microsoft.BotService/locations/operationresults/read | Ler o estado de uma operação assíncrona |
> | Acção | Microsoft.BotService/Operations/read | Operações para todos os tipos de recursos de leitura |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Cache/checknameavailability/action | Verifica se um nome está disponível para utilização com uma nova Cache de Redis |
> | Acção | Microsoft.Cache/locations/operationresults/read | Obtém o resultado de uma operação de longa duração da qual o cabeçalho "Localização" foi devolvido anteriormente ao cliente |
> | Acção | Microsoft.Cache/operations/read | Lista as operações que o fornecedor "Microsoft.Cache" suporta. |
> | Acção | Microsoft.Cache/redis/delete | Eliminar a Cache de Redis completa |
> | Acção | Microsoft.Cache/redis/export/action | Exportar dados Redis para os blobs de armazenamento com prefixo no formato especificado |
> | Acção | Microsoft.Cache/redis/firewallRules/delete | Eliminar as regras de firewall do IP da Cache de Redis |
> | Acção | Microsoft.Cache/redis/firewallRules/read | Obter as regras de firewall do IP da Cache de Redis |
> | Acção | Microsoft.Cache/redis/firewallRules/write | Editar regras de firewall do IP de uma Cache de Redis |
> | Acção | Microsoft.Cache/redis/forceReboot/action | Forçar reinício de uma instância da cache, potencialmente com perda de dados. |
> | Acção | Microsoft.Cache/redis/import/action | Importar dados de um formato especificado a partir de vários blobs para Redis |
> | Acção | Microsoft.Cache/redis/linkedservers/delete | Eliminar Servidor Ligado de uma Cache de Redis |
> | Acção | Microsoft.Cache/redis/linkedservers/read | Obter Servidores Ligados associados a uma cache de redis. |
> | Acção | Microsoft.Cache/redis/linkedservers/write | Adicionar Servidor Ligado a uma Cache de Redis |
> | Acção | Microsoft.Cache/redis/listKeys/action | Ver o valor das chaves de acesso da Cache de Redis no portal de gestão |
> | Acção | Microsoft.Cache/redis/listUpgradeNotifications/read | Listar as Notificações de Atualização mais recentes para o inquilino da cache. |
> | Acção | Microsoft.Cache/redis/metricDefinitions/read | Obtem as métricas disponíveis para uma Cache de Redis |
> | Acção | Microsoft.Cache/redis/patchSchedules/delete | Eliminar a agenda de correção da Cache de Redis |
> | Acção | Microsoft.Cache/redis/patchSchedules/read | Obtém a agenda de correções de uma Cache de Redis |
> | Acção | Microsoft.Cache/redis/patchSchedules/write | Modificar a agenda de correções de uma Cache de Redis |
> | Acção | Microsoft.Cache/redis/read | Ver as definições e a configuração da Cache de Redis no portal de gestão |
> | Acção | Microsoft.Cache/redis/regenerateKey/action | Alterar o valor das chaves de acesso da Cache de Redis no portal de gestão |
> | Acção | Microsoft.Cache/redis/start/action | Iniciar uma instância de cache. |
> | Acção | Microsoft.Cache/redis/stop/action | Parar uma instância de cache. |
> | Acção | Microsoft.Cache/redis/write | Modificar as definições e a configuração da Cache de Redis no portal de gestão |
> | Acção | Microsoft.Cache/register/action | Regista o fornecedor de recursos "Microsoft.Cache" com uma subscrição |
> | Acção | Microsoft.Cache/unregister/action | Anula o registo do fornecedor de recursos "Microsoft.Cache" com uma subscrição |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Capacity/appliedreservations/read | Ler todas as reservas |
> | Acção | Microsoft.Capacity/calculateexchange/action | Calcula a quantidade de exchange e o preço de compra de novos e retorna a política de erros. |
> | Acção | Microsoft.Capacity/calculateprice/action | Calcular preço qualquer reserva |
> | Acção | Microsoft.Capacity/catalogs/read | Catálogo de leitura da reserva |
> | Acção | Microsoft.Capacity/checkoffers/action | Verifique quaisquer ofertas de subscrição |
> | Acção | Microsoft.Capacity/checkscopes/action | Verificar qualquer subscrição |
> | Acção | Microsoft.Capacity/commercialreservationorders/read | Obter as ordens de reserva criados em qualquer inquilino |
> | Acção | Microsoft.Capacity/exchange/action | Qualquer reserva do Exchange |
> | Acção | Microsoft.Capacity/operations/read | Qualquer operação de leitura |
> | Acção | Microsoft.Capacity/register/action | Regista o fornecedor de recursos de capacidade e ativa a criação de recursos de capacidade. |
> | Acção | Microsoft.Capacity/reservationorders/action | Atualizar qualquer reserva |
> | Acção | Microsoft.Capacity/reservationorders/availablescopes/action | Localizar qualquer âmbito disponível |
> | Acção | Microsoft.Capacity/reservationorders/calculaterefund/action | Calcula a quantidade de reembolso e o preço de compra de novos e retorna a política de erros. |
> | Acção | Microsoft.Capacity/reservationorders/delete | Eliminar qualquer reserva |
> | Acção | Microsoft.Capacity/reservationorders/merge/action | Intercalar qualquer reserva |
> | Acção | Microsoft.Capacity/reservationorders/read | Ler todas as reservas |
> | Acção | Microsoft.Capacity/reservationorders/reservations/action | Atualizar qualquer reserva |
> | Acção | Microsoft.Capacity/reservationorders/reservations/delete | Eliminar qualquer reserva |
> | Acção | Microsoft.Capacity/reservationorders/reservations/read | Ler todas as reservas |
> | Acção | Microsoft.Capacity/reservationorders/reservations/revisions/read | Ler todas as reservas |
> | Acção | Microsoft.Capacity/reservationorders/reservations/write | Criar reserva de qualquer |
> | Acção | Microsoft.Capacity/reservationorders/return/action | Devolver qualquer reserva |
> | Acção | Microsoft.Capacity/reservationorders/split/action | Dividir qualquer reserva |
> | Acção | Microsoft.Capacity/reservationorders/swap/action | Trocar qualquer reserva |
> | Acção | Microsoft.Capacity/reservationorders/write | Criar reserva de qualquer |
> | Acção | Microsoft.Capacity/tenants/register/action | Registar qualquer inquilino |
> | Acção | Microsoft.Capacity/unregister/action | Anular o registo de qualquer inquilino |
> | Acção | Microsoft.Capacity/validatereservationorder/action | Validar qualquer reserva |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Acção | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Acção | Microsoft.Cdn/edgenodes/delete |  |
> | Acção | Microsoft.Cdn/edgenodes/read |  |
> | Acção | Microsoft.Cdn/edgenodes/write |  |
> | Acção | Microsoft.Cdn/operationresults/delete |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Acção | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Acção | Microsoft.Cdn/operationresults/read |  |
> | Acção | Microsoft.Cdn/operationresults/write |  |
> | Acção | Microsoft.Cdn/operations/read |  |
> | Acção | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Acção | Microsoft.Cdn/profiles/delete |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/read |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Acção | Microsoft.Cdn/profiles/endpoints/write |  |
> | Acção | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Acção | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Acção | Microsoft.Cdn/profiles/read |  |
> | Acção | Microsoft.Cdn/profiles/write |  |
> | Acção | Microsoft.Cdn/register/action | Regista a subscrição para o fornecedor de recursos CDN e ativa a criação de perfis CDN. |
> | Acção | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Eliminar um certificado existente |
> | Acção | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Obter a lista de certificados |
> | Acção | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Adicionar um novo certificado ou atualizar um já existente |
> | Acção | Microsoft.CertificateRegistration/certificateOrders/Delete | Eliminar um AppServiceCertificate existente |
> | Acção | Microsoft.CertificateRegistration/certificateOrders/Read | Obter a lista de CertificateOrders |
> | Acção | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Volte a emitir uma existente certificateorder |
> | Acção | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Renovar um existente certificateorder |
> | Acção | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Reenviar correio eletrónico de certificado |
> | Acção | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Reenviar pedido e-mails para outro endereço de e-mail |
> | Acção | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Obter selar de site para um certificado de serviço de aplicações emitido |
> | Acção | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Obter a lista de ações de certificado |
> | Acção | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Obter histórico de e-mails de certificado |
> | Acção | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Verificar a propriedade do domínio |
> | Acção | Microsoft.CertificateRegistration/certificateOrders/Write | Adicionar um novo certificateOrder ou atualizar um já existente |
> | Acção | Microsoft.CertificateRegistration/operations/Read | Listar todas as operações de registo de certificados do serviço de aplicações |
> | Acção | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Principal de serviço de aprovisionamento para o principal da aplicação de serviço |
> | Acção | Microsoft.CertificateRegistration/register/action | Registar o fornecedor de recursos do Microsoft Certificates para a subscrição |
> | Acção | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Validar o objeto de compra do certificado sem enviá-lo |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ClassicCompute/capabilities/read | Mostra as capacidades |
> | Acção | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Verifica a disponibilidade de um nome de domínio fornecido. |
> | Acção | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Verifica a disponibilidade de um nome de domínio específico. |
> | Acção | Microsoft.ClassicCompute/domainNames/active/write | Define o nome do domínio ativo. |
> | Acção | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Mostra o conjunto de disponibilidade do recurso. |
> | Acção | Microsoft.ClassicCompute/domainNames/capabilities/read | Mostra as funções de nome de domínio |
> | Acção | Microsoft.ClassicCompute/domainNames/delete | Remove os nomes de domínio dos recursos. |
> | Acção | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Mostra os blocos de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Obter a função no bloco de implementação do nome de domínio |
> | Acção | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Obter a instância de função no bloco de implementação do nome de domínio |
> | Acção | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Obter o estado do bloco de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Adicionar o estado do bloco de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Obter o domínio de atualização para o bloco de implementação no nome de domínio |
> | Acção | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Atualizar domínio de atualização para o bloco de implementação no nome de domínio |
> | Acção | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Cria ou atualiza a implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/extensions/delete | Remova as extensões do nome de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Lê o estado da operação das extensões de nomes de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/extensions/read | Devolve as extensões do nome de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/extensions/write | Adicione as extensões do nome de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Remove um novo balanceador de carga interno. |
> | Acção | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Lê o estado da operação dos balanceadores de carga internos de nomes de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Obtém os balanceadores de carga internos. |
> | Acção | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Cria um novo balanceador de carga interno. |
> | Acção | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Lê o estado da operação dos conjuntos de pontos finais com balanceamento de carga de nomes de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Obter os conjuntos de pontos finais com balanceamento de carga. |
> | Acção | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Adicionar o conjunto de pontos finais com balanceamento de carga. |
> | Acção | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Obter o estado da operação do nome de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Lê o estado da operação das extensões de nomes de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/read | Devolve os nomes de domínio dos recursos. |
> | Acção | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Elimina os certificados de serviço utilizados. |
> | Acção | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Lê o estado da operação dos certificados de serviço de nomes de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Devolve os certificados de serviço utilizados. |
> | Acção | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Adiciona ou modifica os certificados de serviço utilizados. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Aborta a migração de um bloco de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Consolida a migração de um bloco de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/delete | Elimina um bloco de implementação fornecido. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Lê o estado da operação das ranhuras de nomes de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Prepara a migração de um bloco de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/read | Mostra os blocos de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Remova a referência da extensão para a função de bloco de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Lê o estado da operação das referências de extensão de funções de ranhuras de nomes de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Devolve a referência da extensão para a função de bloco de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Adicionar ou modificar a referência da extensão para a função de bloco de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Obter a definição de métrica de função para o nome de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Obter métrica de função para o nome de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Obter o estado de operação para a função de bloco de nomes de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Obtém as definições de diagnóstico. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Adiciona ou modifica as definições de diagnóstico. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Obtém as definições das métricas. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/read | Obtém a função do bloco de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Transfere o ficheiro de ligação de ambiente de trabalho remoto para a instância de função na função de bloco de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Obtém o estado de operação para a instância de função na função de bloco de nomes de domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Obtém a instância de função. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Recria a instância de função. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Recria a imagem da instância de função. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Reinicia instâncias de função. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Obter o SKU de função para o bloco de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/roles/write | Adicionar função para bloco de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/start/action | Inicia um bloco de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Altera o estado do bloco de implementação para parado. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Altera o estado do bloco de implementação para iniciado. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/stop/action | Suspende o bloco de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Percorre a atualização do domínio. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Valida a migração de um bloco de implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/slots/write | Cria ou atualiza a implementação. |
> | Acção | Microsoft.ClassicCompute/domainNames/swap/action | Troca o bloco de teste pelo bloco de produção. |
> | Acção | Microsoft.ClassicCompute/domainNames/write | Adiciona ou modifica os nomes de domínio dos recursos. |
> | Acção | Microsoft.ClassicCompute/moveSubscriptionResources/action | Mover todos os recursos clássicos para uma subscrição diferente. |
> | Acção | Microsoft.ClassicCompute/operatingSystemFamilies/read | Lista as famílias de sistemas operativos convidadas disponíveis no Microsoft Azure, e lista as versões de sistema operativo disponíveis para cada família. |
> | Acção | Microsoft.ClassicCompute/operatingSystems/read | Lista as versões dos sistemas operativos convidados que estão, de momento, disponíveis no Microsoft Azure. |
> | Acção | Microsoft.ClassicCompute/operations/read | Obtém a lista de operações. |
> | Acção | Microsoft.ClassicCompute/operationStatuses/read | Lê o estado da operação do recurso. |
> | Acção | Microsoft.ClassicCompute/quotas/read | Obtém a quota da subscrição. |
> | Acção | Microsoft.ClassicCompute/register/action | Registar na Computação Clássica |
> | Acção | Microsoft.ClassicCompute/resourceTypes/skus/read | Obtém a lista de SKUs para tipos de recurso suportados. |
> | Acção | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Valide a disponibilidade da subscrição para operação de movimentação clássica. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Elimina o grupo de segurança de rede associado à máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Lê o estado da operação dos grupos de segurança de rede associados a máquinas virtuais. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Obtém o grupo de segurança de rede associado à máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Obtém as operações assíncronas possíveis |
> | Acção | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Anexa um disco de dados a uma máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/capture/action | Capturar uma máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/delete | Remove máquinas virtuais. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Desanexa um disco de dados da máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Obter as definições de diagnóstico de máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/disks/read | Obtém a lista de discos de dados |
> | Acção | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Transfere o ficheiro RDP da máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Lê o estado da operação das extensões de máquinas virtuais. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/extensions/read | Obtém a extensão da máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/extensions/write | Coloca a extensão da máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Obter a definição de métrica da máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/metrics/read | Obtém as métricas. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Elimina o grupo de segurança de rede associado à interface de rede. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Lê o estado da operação dos grupos de segurança de rede associados a máquinas virtuais. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Obtém o grupo de segurança de rede associado à interface de rede. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à interface de rede. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Lê o estado da operação das máquinas virtuais. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Executa a manutenção na máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Obtém as definições de diagnóstico. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Adiciona ou modifica as definições de diagnóstico. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Obtém as definições das métricas. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/read | Obtém a lista de máquinas virtuais. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Reimplementa a máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/restart/action | Reinicia máquinas virtuais. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Encerra a máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/start/action | Inicia a máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/stop/action | Para a máquina virtual. |
> | Acção | Microsoft.ClassicCompute/virtualMachines/write | Adiciona ou modifica máquinas virtuais. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Obter um estado de operação de ligação cruzada do express route. |
> | Acção | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Eliminar o peering de ligação cruzada do express route. |
> | Acção | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Obter um estado de operação de peering de ligação cruzada do express route. |
> | Acção | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Obter peering de ligação cruzada do express route. |
> | Acção | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Adicionar peering de ligação cruzada do Express Route. |
> | Acção | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Obter ligações cruzadas do express route. |
> | Acção | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Adicionar ligações cruzadas do Express Route. |
> | Acção | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Obtém a lista de dispositivos suportados. |
> | Acção | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Elimina o grupo de segurança de rede. |
> | Acção | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Lê o estado da operação do grupo de segurança de rede. |
> | Acção | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Obtém as Definições de Diagnóstico dos Grupos de Segurança de Rede |
> | Acção | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza os grupos de segurança de rede das definições de diagnóstico, esta operação é complementada pelo fornecedor de recursos de informações. |
> | Acção | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Obtém os eventos do grupo de segurança de rede |
> | Acção | Microsoft.ClassicNetwork/networkSecurityGroups/read | Obtém o grupo de segurança de rede. |
> | Acção | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Elimina a regra de segurança. |
> | Acção | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Lê o estado da operação das regras de segurança do grupo de segurança de rede. |
> | Acção | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Obtém a regra de segurança. |
> | Acção | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Adiciona ou atualiza uma regra de segurança. |
> | Acção | Microsoft.ClassicNetwork/networkSecurityGroups/write | Adiciona um novo grupo de segurança de rede. |
> | Acção | Microsoft.ClassicNetwork/operations/read | Obter operações de rede clássica. |
> | Acção | Microsoft.ClassicNetwork/quotas/read | Obtém a quota da subscrição. |
> | Acção | Microsoft.ClassicNetwork/register/action | Registar na Rede Clássica |
> | Acção | Microsoft.ClassicNetwork/reservedIps/delete | Elimina um IP reservado. |
> | Acção | Microsoft.ClassicNetwork/reservedIps/join/action | Associar um IP reservado |
> | Acção | Microsoft.ClassicNetwork/reservedIps/link/action | Ligar um IP reservado |
> | Acção | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Lê o estado da operação dos IPs reservados. |
> | Acção | Microsoft.ClassicNetwork/reservedIps/read | Obtém os IPs reservados |
> | Acção | Microsoft.ClassicNetwork/reservedIps/write | Adiciona um novo IP reservado |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Aborta a migração de uma Rede Virtual |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Mostra as capacidades |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Verifica a disponibilidade de um determinado endereço IP numa rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Consolida a migração de uma Rede Virtual |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/delete | Elimina a rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Anula a revogação de um certificado de cliente. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Lê os certificados de cliente revogados. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Revoga um certificado de cliente. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Elimina o certificado de cliente do gateway da rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Transfere o certificado por thumbprint. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Lista o pacote de certificado do gateway da rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Localiza os certificados de raiz de cliente. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Carrega um novo certificado de raiz de cliente. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Estabelece uma ligação de gateway site para site. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Desliga uma ligação de gateway site para site. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Obtém a lista de ligações. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Testa uma ligação de gateway site para site. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Elimina o gateway da rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Transfere o script de configuração do dispositivo. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Transfere o diagnóstico do gateway. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Obtém a chave do serviço de circuito. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Lista o pacote do gateway da rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Lê o estado da operação dos gateways de redes virtuais. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Obtém o pacote do gateway da rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Obtém os gateways da rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Inicia o diagnóstico para o gateway de rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Interrompe o diagnóstico para o gateway de rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Adiciona um gateway da rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/join/action | Associa à rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Lê o estado da operação das redes virtuais. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Elementos de uma rede virtual com outra rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Prepara a migração de uma Rede Virtual |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/read | Obtém a rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Elimina o proxy de peering da rede virtual remota. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Obtém o proxy de peering da rede virtual remota. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Adiciona ou atualiza o proxy de peering da rede virtual remota. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Elimina o grupo de segurança de rede associado à sub-rede. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Lê o estado da operação para o grupo de segurança de rede associado de sub-rede de rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Obtém o grupo de segurança de rede associado à sub-rede. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à sub-rede. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Valida a migração de uma Rede Virtual |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Obtém o peering da rede virtual. |
> | Acção | Microsoft.ClassicNetwork/virtualNetworks/write | Adiciona uma nova rede virtual. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ClassicStorage/capabilities/read | Mostra as capacidades |
> | Acção | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Verifica a disponibilidade de uma conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Obter a disponibilidade de uma conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/disks/read | Devolve o disco de conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/images/operationstatuses/read | Obtém o Estado de Operação de Imagem. |
> | Acção | Microsoft.ClassicStorage/images/read | Devolve a imagem. |
> | Acção | Microsoft.ClassicStorage/operations/read | Obtém operações de armazenamento clássico |
> | Acção | Microsoft.ClassicStorage/osImages/read | Devolve a imagem de sistema operativo. |
> | Acção | Microsoft.ClassicStorage/osPlatformImages/read | Obtém a imagem de plataforma de sistema operativo. |
> | Acção | Microsoft.ClassicStorage/publicImages/read | Obtém a imagem pública da máquina virtual. |
> | Acção | Microsoft.ClassicStorage/quotas/read | Obtém a quota da subscrição. |
> | Acção | Microsoft.ClassicStorage/register/action | Registar para Armazenamento Clássico |
> | Acção | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Aborta a migração de uma conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Consolida a migração de uma conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/delete | Elimina a conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/disks/delete | Elimina um determinado disco de conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Lê o estado da operação do recurso. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/disks/read | Devolve o disco de conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/disks/write | Adiciona um disco de conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/images/delete | Elimina uma imagem de conta de armazenamento fornecida. (Preterido. Use 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Acção | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Devolve o estado de operação de imagem de conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/images/read | Devolve a imagem de conta de armazenamento. (Preterido. Use 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Acção | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Lê o estado da operação do recurso. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Elimina uma imagem do sistema operativo de uma conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/osImages/read | Devolve a imagem do sistema operativo da conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/osImages/write | Adiciona uma determinada imagem de sistema operativo de conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Prepara a migração de uma conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/read | Devolve a conta de armazenamento com a conta fornecida. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Volta a gerar as chaves de acesso existentes para a conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Obtém as definições de diagnóstico. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Adiciona ou modifica as definições de diagnóstico. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Obtém as definições das métricas. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Obtém as métricas. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/services/read | Obtém os serviços disponíveis. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Valida a migração de uma conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Elimina uma imagem da máquina virtual especificada. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Obtém um estado da operação de imagem numa máquina virtual especificada. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Devolve a imagem da máquina virtual. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Adiciona uma imagem da máquina virtual especificada. |
> | Acção | Microsoft.ClassicStorage/storageAccounts/write | Adiciona uma nova conta de armazenamento. |
> | Acção | Microsoft.ClassicStorage/vmImages/read | Lista imagens da máquina virtual. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.CognitiveServices/accounts/delete | Elimina contas da API |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/detect/action | Detete rostos humanos numa imagem, retorno retângulos e, opcionalmente, com faceIds, pontos de referência e atributos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | Elimine uma lista de rostos especificado. As imagens de face relacionados na lista de face serão eliminadas, demasiado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Elimine um rosto de uma lista de face ao faceListId especificado e persisitedFaceId. A imagem da face relacionados será eliminada, demasiado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Adicione um face a uma lista de rostos especificado, até 1000 rostos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Obter uma lista de face faceListId, nome, userData e rostos na lista de rostos. Listar listas de rostos faceListId, nome e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Crie uma lista vazia de rostos com faceListId especificado pelo utilizador, nome e uma userData opcional. Até 64 rostos listas são permitidas informações de atualização de uma lista de rostos, incluindo o nome e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | Dado o face ID do rosto de consulta, para procurar os rostos de aspeto semelhante de uma matriz de face ID, uma lista de face ou uma lista de face grandes. faceId |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/group/action | Divida o candidato rostos em grupos com base na semelhança de rostos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/identify/action | 1-para-muitos identificação para encontrar as correspondências mais próximas do Mostrador de pessoa da consulta específica a partir de um grupo de pessoas ou o grupo de pessoas grandes. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Elimine uma lista de face de grandes especificado. As imagens de face relacionados na lista de face de grandes dimensões serão eliminadas, demasiado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | Elimine um rosto de uma lista de grandes face ao largeFaceListId especificado e persisitedFaceId. A imagem da face relacionados será eliminada, demasiado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Obter rostos persistentes na lista de grandes face ao largeFaceListId e persistedFaceId. Lista dos rostos persistedFaceId e userData numa lista de face de grandes especificado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | Adicione um face a uma lista de face de grandes especificado, até 1 000 000 rostos. Atualize o campo de userData de um rosto especificado numa lista grande face ao seu persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Obter largeFaceListId de uma lista de face de grandes dimensões, o nome, userData. Liste informações de listas de rostos grandes de largeFaceListId, nome e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | Submeta uma tarefa de treinamento de lista de face de grandes dimensões. O treinamento é um passo crucial que pode utilizar apenas uma lista de face de grandes treinado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | Para verificar o estado de treinamento de lista face grandes conclusão ou ainda em curso. Treinamento de LargeFaceList é uma operação assíncrona |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Crie uma lista vazia face grandes com largeFaceListId especificado pelo utilizador, nome e uma userData opcional. Atualize as informações de uma lista de face de grandes dimensões, incluindo o nome e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | Elimine um grupo de pessoas grandes existente com personGroupId especificado. Dados persistentes neste grupo de pessoa grandes serão eliminados. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | Crie uma nova pessoa num grupo de pessoas grandes especificado. Para adicionar face a esta pessoa, chamar |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | Elimine uma pessoa existente de um grupo de pessoa grandes. Todos armazenados dados person e imagens de rosto na entrada de pessoa vão ser eliminadas. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Elimine um rosto de uma pessoa num grupo de pessoas grandes. Dados de rostos e relacionados com esta entrada de face de imagem também serão eliminados. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Obter as informações de face da pessoa. O mostrador da pessoa persistente é especificado pela respetiva largePersonGroupId, personId e persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | Adicione uma imagem da face a uma pessoa num grupo de pessoas grandes para identificação facial ou de verificação. Para lidar com a imagem da atualização de uma pessoa persistentes campo de userData do rosto. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Obtenha o nome de uma pessoa e userData e o faceIds persistente que representa a imagem de face da pessoa registada. Liste informações de todas as pessoas no grupo especificado pessoa grandes, incluindo personId, nome, userData e persistedFaceIds. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | Nome da atualização ou userData de uma pessoa. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | Obter as informações de um grupo de pessoas grandes, incluindo o respetivo nome e userData. Estas informações no grupo API de pessoa grandes devolve lista todos os existentes largePesonGroupId, o nome e userData de grupos de pessoas grandes. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | Submeta uma tarefa de treinamento do grupo de pessoa grandes. O treinamento é um passo crucial que pode utilizar apenas um grupo de pessoas de grandes treinado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | Para verificar o estado de treinamento para grupo de pessoa grandes conclusão ou ainda em curso. Treinamento de LargePersonGroup é uma operação assíncrona |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | Crie um novo grupo de pessoa grandes com largePersonGroupId especificado pelo utilizador, nome e userData opcional. Atualize o nome de um grupo grande de pessoa existente e userData. As propriedades mantenha inalteradas, se não estiverem no corpo do pedido. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | Elimine um grupo de pessoas existente com personGroupId especificado. Dados persistentes neste grupo de pessoas vão ser eliminados. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | Crie uma nova pessoa num grupo de pessoas especificado. Para adicionar face a esta pessoa, chamar |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | Elimine uma pessoa existente de um grupo de pessoas. Todos armazenados dados person e imagens de rosto na entrada de pessoa vão ser eliminadas. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | Elimine um rosto de uma pessoa num grupo de pessoas. Dados de rostos e relacionados com esta entrada de face de imagem também serão eliminados. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | Obter as informações de face da pessoa. O mostrador da pessoa persistente é especificado pela respetiva personGroupId, personId e persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | Adicione uma imagem da face a uma pessoa num grupo de pessoas para identificação facial ou de verificação. Para lidar com a imagem de uma pessoa de atualização vários persistentes campo de userData do rosto. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | Obtenha o nome de uma pessoa e userData e o faceIds persistente que representa a imagem de face da pessoa registada. Listar informações de todas as pessoas no grupo de pessoa específica, incluindo personId, nome, userData e persistedFaceIds de registado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | Nome da atualização ou userData de uma pessoa. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/read | Obter o nome do grupo de pessoa e userData. Para obter informações de person sob este personGroup, utilize pesonGroupId, o nome e o userData de grupos de pessoas a lista. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | Submeta uma tarefa de treinamento do grupo de pessoas. O treinamento é um passo crucial que pode utilizar apenas um grupo de pessoas treinado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | Para verificar o estado de treinamento para grupo de pessoa conclusão ou ainda em curso. Treinamento de PersonGroup é uma operação assíncrona acionada |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/write | Crie um novo grupo de pessoas com personGroupId especificado, o nome e userData fornecidos pelo usuário. Atualize o nome de um grupo pessoa existente e userData. As propriedades mantenha inalteradas, se não estiverem no corpo do pedido. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/verify/action | Certifique-se de que se dois rostos pertencerem a uma mesma pessoa ou se um rosto pertence a uma pessoa. |
> | Acção | Microsoft.CognitiveServices/accounts/listKeys/action | Listar Chaves |
> | Acção | Microsoft.CognitiveServices/accounts/read | Lê contas da API. |
> | Acção | Microsoft.CognitiveServices/accounts/regenerateKey/action | Regenerar Chave |
> | Acção | Microsoft.CognitiveServices/accounts/skus/read | Lê os SKUs disponíveis para um recurso existente. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | A API devolve uma lista de entidades conhecidas e geral com o nome entidades (\"pessoa\", \"localização\", \"organização\" etc) num determinado documento. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | A API devolve uma lista de cadeias que denotam os principais pontos de conversa no texto introduzido. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | A API devolve o idioma detetado e uma pontuação numérica entre 0 e 1. As pontuações próximas de 1 indicam uma certeza de 100% de que o idioma detetado é verdadeiro. É suportado um total de 120 idiomas. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | A API devolve uma pontuação numérica entre 0 e 1.<br>As pontuações próximas de 1 indicam um sentimento positivo, enquanto as pontuações próximas de 0 indicam um sentimento negativo.<br>Uma pontuação de 0,5 indica a falta de sensibilidade (por exemplo<br>uma declaração de factoid). |
> | Acção | Microsoft.CognitiveServices/accounts/usages/read | Obter a utilização de quota para um recurso existente. |
> | Acção | Microsoft.CognitiveServices/accounts/write | Escreve Contas da API. |
> | Acção | Microsoft.CognitiveServices/checkDomainAvailability/action | Lê SKUs disponíveis para uma subscrição. |
> | Acção | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Lê SKUs disponíveis para uma subscrição. |
> | Acção | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Notificação de Network da eliminação de redes virtuais ou sub-redes. |
> | Acção | Microsoft.CognitiveServices/Operations/read | Listar todas as operações disponíveis |
> | Acção | Microsoft.CognitiveServices/register/action | Regista a Subscrição nos Serviços Cognitivos |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Commerce/RateCard/read | Devolve oferece dados, os metadados de recurso/medidor e taxas para a subscrição fornecida. |
> | Acção | Microsoft.Commerce/UsageAggregates/read | Obtém o consumo do Microsoft Azure por uma subscrição. O resultado contém agrega dados de utilização, recursos e subscrição informações relacionadas com o, num intervalo de tempo específico. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Compute/availabilitySets/delete | Elimina o conjunto de disponibilidade |
> | Acção | Microsoft.Compute/availabilitySets/read | Obter as propriedades de um conjunto de disponibilidade |
> | Acção | Microsoft.Compute/availabilitySets/vmSizes/read | Listar tamanhos disponíveis para criar ou atualizar uma máquina virtual no conjunto de disponibilidade |
> | Acção | Microsoft.Compute/availabilitySets/write | Cria um novo conjunto de disponibilidade ou atualiza um já existente |
> | Acção | Microsoft.Compute/disks/beginGetAccess/action | Obter o URI de SAS do Disco para acesso do blob |
> | Acção | Microsoft.Compute/disks/delete | Elimina o Disco |
> | Acção | Microsoft.Compute/disks/endGetAccess/action | Revogar o URI de SAS do Disco |
> | Acção | Microsoft.Compute/disks/read | Obter as propriedades de um Disco |
> | Acção | Microsoft.Compute/disks/write | Cria um novo Disco ou atualiza um já existente |
> | Acção | Microsoft.Compute/galleries/delete | Elimina a Galeria |
> | Acção | Microsoft.Compute/galleries/images/delete | Elimina a Imagem de Galeria |
> | Acção | Microsoft.Compute/galleries/images/read | Obtém as propriedades da Imagem de Galeria |
> | Acção | Microsoft.Compute/galleries/images/versions/delete | Elimina a Versão da Imagem de Galeria |
> | Acção | Microsoft.Compute/galleries/images/versions/read | Obtém as propriedades da Versão da Imagem de Galeria |
> | Acção | Microsoft.Compute/galleries/images/versions/write | Cria uma nova Versão de Imagem de Galeria ou atualiza uma existente |
> | Acção | Microsoft.Compute/galleries/images/write | Cria uma nova Imagem de Galeria ou atualiza uma existente |
> | Acção | Microsoft.Compute/galleries/read | Obtém as propriedades da Galeria |
> | Acção | Microsoft.Compute/galleries/write | Cria uma nova Galeria ou atualiza uma existente |
> | Acção | Microsoft.Compute/hostGroups/delete | Elimina o grupo de anfitriões |
> | Acção | Microsoft.Compute/hostGroups/read | Obter as propriedades de um grupo de anfitriões |
> | Acção | Microsoft.Compute/hostGroups/write | Cria um novo grupo de anfitriões ou atualiza um grupo de anfitriões existentes |
> | Acção | Microsoft.Compute/hosts/delete | Elimina o anfitrião |
> | Acção | Microsoft.Compute/hosts/read | Obter as propriedades de um anfitrião |
> | Acção | Microsoft.Compute/hosts/write | Cria um novo anfitrião ou atualiza um anfitrião existente |
> | Acção | Microsoft.Compute/images/delete | Elimina a imagem |
> | Acção | Microsoft.Compute/images/read | Obter as propriedades da Imagem |
> | Acção | Microsoft.Compute/images/write | Cria uma nova Imagem ou atualiza uma existente |
> | Acção | Microsoft.Compute/locations/capsOperations/read | Obtém o estado de uma operação de Limite assíncrona |
> | Acção | Microsoft.Compute/locations/diskOperations/read | Obtém o estado de uma operação assíncrona do disco |
> | Acção | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Crie registos para mostrar o número total de pedidos por intervalo de tempo de modo a promover a limitação de diagnósticos. |
> | Acção | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Crie registos para mostrar agregados de pedidos limitados agrupados por NomeDoRecurso, NomeDaOperação ou pela Política de Limitação aplicada. |
> | Acção | Microsoft.Compute/locations/operations/read | Obtém o estado de uma operação assíncrona |
> | Acção | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Obter as propriedades de uma Oferta de Imagem da Plataforma |
> | Acção | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Obter as propriedades de uma SKU da Imagem de Plataforma |
> | Acção | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Obter as propriedades de uma Versão da Imagem de Plataforma |
> | Acção | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Obter as propriedades de um Tipo de VMExtension |
> | Acção | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Obter as propriedades de uma Versão do VMExtension |
> | Acção | Microsoft.Compute/locations/publishers/read | Obter as propriedades de um Publicador |
> | Acção | Microsoft.Compute/locations/runCommands/read | Lista os comandos de execução disponíveis na localização |
> | Acção | Microsoft.Compute/locations/usages/read | Obtém os limites de serviço e as quantidades de utilização atuais dos recursos de computação da subscrição numa localização |
> | Acção | Microsoft.Compute/locations/vmSizes/read | Lista os tamanhos de máquina virtual disponíveis numa localização |
> | Acção | Microsoft.Compute/operations/read | Lista as operações disponíveis no fornecedor de recursos Microsoft.Compute |
> | Acção | Microsoft.Compute/proximityPlacementGroups/delete | Elimina o Grupo de Colocação em Proximidade |
> | Acção | Microsoft.Compute/proximityPlacementGroups/read | Obter as Propriedades de um Grupo de Colocação em Proximidade |
> | Acção | Microsoft.Compute/proximityPlacementGroups/write | Cria um novo Grupo de Colocação em Proximidade ou atualiza um existente |
> | Acção | Microsoft.Compute/register/action | Regista a Subscrição no fornecedor de recursos Microsoft.Compute |
> | Acção | Microsoft.Compute/restorePointCollections/delete | Elimina a coleção de ponto de restauro e pontos de restauro contidos |
> | Acção | Microsoft.Compute/restorePointCollections/read | Obter as propriedades de uma coleção de ponto de restauro |
> | Acção | Microsoft.Compute/restorePointCollections/restorePoints/delete | Elimina o ponto de restauro |
> | Acção | Microsoft.Compute/restorePointCollections/restorePoints/read | Obter as propriedades de um ponto de restauro |
> | Acção | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Obter as propriedades de um ponto de restauro juntamente com os URIs de SAS do blob |
> | Acção | Microsoft.Compute/restorePointCollections/restorePoints/write | Cria um novo ponto de restauro |
> | Acção | Microsoft.Compute/restorePointCollections/write | Cria uma nova coleção de ponto de restauro ou atualiza uma existente |
> | Acção | Microsoft.Compute/sharedVMImages/delete | Elimina o SharedVMImage |
> | Acção | Microsoft.Compute/sharedVMImages/read | Obter as propriedades de um SharedVMImage |
> | Acção | Microsoft.Compute/sharedVMImages/versions/delete | Eliminar um SharedVMImageVersion |
> | Acção | Microsoft.Compute/sharedVMImages/versions/read | Obter as propriedades de um SharedVMImageVersion |
> | Acção | Microsoft.Compute/sharedVMImages/versions/replicate/action | Replicar uma SharedVMImageVersion para regiões de destino |
> | Acção | Microsoft.Compute/sharedVMImages/versions/write | Criar um novo SharedVMImageVersion ou atualizar um já existente |
> | Acção | Microsoft.Compute/sharedVMImages/write | Cria uma nova SharedVMImage ou atualiza uma existente |
> | Acção | Microsoft.Compute/skus/read | Obtém a lista das SKUs do Microsoft.Compute disponíveis para a sua Subscrição |
> | Acção | Microsoft.Compute/snapshots/beginGetAccess/action | Obter o URI de SAS do instantâneo para acesso do blob |
> | Acção | Microsoft.Compute/snapshots/delete | Eliminar um Instantâneo |
> | Acção | Microsoft.Compute/snapshots/endGetAccess/action | Revogar o URI de SAS do instantâneo |
> | Acção | Microsoft.Compute/snapshots/read | Obter as propriedades de um Instantâneo |
> | Acção | Microsoft.Compute/snapshots/write | Criar um novo Instantâneo ou atualizar um já existente |
> | Acção | Microsoft.Compute/unregister/action | Anula o Registo da Subscrição no fornecedor de recursos Microsoft.Compute |
> | Acção | Microsoft.Compute/virtualMachines/capture/action | Recolhe a máquina virtual, copiando os discos rígidos virtuais, e gera um modelo que pode ser utilizado para criar máquinas virtuais semelhantes |
> | Acção | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Converte os discos baseados em blob da máquina virtual em discos geridos |
> | Acção | Microsoft.Compute/virtualMachines/deallocate/action | Desliga a máquina virtual e liberta os recursos de computação |
> | Acção | Microsoft.Compute/virtualMachines/delete | Elimina a máquina virtual |
> | Acção | Microsoft.Compute/virtualMachines/extensions/delete | Elimina a extensão da máquina virtual |
> | Acção | Microsoft.Compute/virtualMachines/extensions/read | Obter as propriedades de uma extensão da máquina virtual |
> | Acção | Microsoft.Compute/virtualMachines/extensions/write | Cria uma nova extensão da máquina virtual ou atualiza uma já existente |
> | Acção | Microsoft.Compute/virtualMachines/generalize/action | Define o estado da máquina virtual como Generalizado e prepara-a para a captura |
> | Acção | Microsoft.Compute/virtualMachines/instanceView/read | Obtém o estado de runtime detalhado da máquina virtual e dos respetivos recursos |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Iniciar sessão numa máquina virtual como um utilizador normal |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Inicie sessão para uma máquina virtual com o administrador do Windows ou privilégios de utilizador de raiz do Linux |
> | Acção | Microsoft.Compute/virtualMachines/performMaintenance/action | Realiza a Operação de Manutenção na VM. |
> | Acção | Microsoft.Compute/virtualMachines/powerOff/action | Desliga a máquina virtual. Tenha em atenção que a máquina virtual irá continuar a ser faturados. |
> | Acção | Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
> | Acção | Microsoft.Compute/virtualMachines/redeploy/action | Reimplementa a máquina virtual |
> | Acção | Microsoft.Compute/virtualMachines/reimage/action | Recria a imagem da máquina virtual que está a utilizar o disco de diferenciação. |
> | Acção | Microsoft.Compute/virtualMachines/restart/action | Reinicia a máquina virtual |
> | Acção | Microsoft.Compute/virtualMachines/runCommand/action | Executa um script predefinido na máquina virtual |
> | Acção | Microsoft.Compute/virtualMachines/start/action | Inicia a máquina virtual |
> | Acção | Microsoft.Compute/virtualMachines/vmSizes/read | Lista os tamanhos disponíveis para os quais a máquina virtual pode ser atualizada |
> | Acção | Microsoft.Compute/virtualMachines/write | Cria uma nova máquina virtual ou atualiza uma máquina virtual existente |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Desliga e liberta os recursos de computação para as instâncias do Conjunto de Dimensionamento da Máquina Virtual  |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/delete | Elimina o Conjunto de Dimensionamento da Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/delete/action | Elimina as instâncias do Conjunto de Dimensionamento da Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Elimina a Extensão do Conjunto de Dimensionamento da Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Obtém as propriedades de uma Extensão do Conjunto de Dimensionamento da Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Cria uma nova Extensão do Conjunto de Dimensionamento da Máquina Virtual ou atualiza uma existente |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Percorrer manualmente os domínios de atualização da plataforma de um Conjunto de Dimensionamento de Máquinas Virtuais do Service Fabric para concluir uma atualização pendente que está parada |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Obtém a vista de instância do Conjunto de Dimensionamento da Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Atualiza manualmente as instâncias para o modelo mais recente do Conjunto de Dimensionamento da Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Obter as propriedades de todas as interfaces de rede de um Conjunto de Dimensionamento de Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Inicia uma atualização para mover todas as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais para a versão de SO de Imagem de Plataforma mais recente disponível. |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Obtém o histórico de atualizações do SO de um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Executa a manutenção planeada nas instâncias do Conjunto de Dimensionamento de Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Desliga as instâncias do Conjunto de Dimensionamento da Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Obter as propriedade de todos os endereços IP públicos de um Conjunto de Dimensionamento de Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/read | Obter as propriedades de um Conjunto de Dimensionamento da Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Implementar novamente as instâncias do Conjunto de Dimensionamento de Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Recria imagens de instâncias do Conjunto de Dimensionamento da Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Recria a imagem de todos os discos (Discos de SO e Discos de Dados) para as instâncias de um Conjunto de Dimensionamento de Máquinas Virtuais  |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/restart/action | Reinicia as instâncias do Conjunto de Dimensionamento da Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Cancela a atualização gradual de um Conjunto de Dimensionamento da Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Obtenha o estado mais recente da Atualização Gradual para um Conjunto de Dimensionamento da Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/scale/action | Verificar se um Conjunto de Dimensionamento da Máquina Virtual existente pode ser Reduzido/Aumentado Horizontalmente para a contagem de instâncias especificada |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/skus/read | Lista os SKUs válidos de um Conjunto de Dimensionamento da Máquina Virtual existente |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/start/action | Inicia as instâncias do Conjunto de Dimensionamento da Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Desativa e liberta os recursos de computação de uma Máquina Virtual num Conjunto de Dimensionamento da VM. |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Eliminar uma Máquina Virtual específica num Conjunto de Dimensionamento da VM. |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Obtém a vista de instância de uma Máquina Virtual num Conjunto de Dimensionamento da VM. |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Obter propriedades de endereço IP público criado através do conjunto de dimensionamento de Máquina Virtual. O conjunto de dimensionamento de máquinas virtuais, pode criar no máximo, um IP público por ipconfiguration (IP privado) |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Obter propriedades de uma ou todas as configurações de IP de uma interface de rede criada através do conjunto de dimensionamento de Máquina Virtual. Configurações de IP representam IPs privados |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Obter as propriedades de uma ou todas as interfaces de rede de uma máquina virtual criada através do Conjunto de Dimensionamento da Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Executa a manutenção planeada numa instância de Máquina Virtual num Conjunto de Dimensionamento de Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Desativa uma instância de Máquina Virtual num Conjunto de Dimensionamento da VM. |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Obtém as propriedades de uma Máquina Virtual num Conjunto de Dimensionamento da VM |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Implementa novamente uma instância de Máquina Virtual num Conjunto de Dimensionamento de Máquina Virtual |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Recria imagens de uma instância de Máquina Virtual num Conjunto de Dimensionamento de Máquina Virtual. |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Recria a imagem de todos os discos (Discos de SO e Discos de Dados) para a instância de Máquina Virtual num Conjunto de Dimensionamento de Máquinas Virtuais. |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Reinicia uma instância de Máquina Virtual num Conjunto de Dimensionamento da VM. |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Executa um script predefinido numa instância de Máquina Virtual num Conjunto de Dimensionamento de Máquina Virtual. |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Inicia uma instância de Máquina Virtual num Conjunto de Dimensionamento da VM. |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Atualiza as propriedades de uma Máquina Virtual num Conjunto de Dimensionamento de VM |
> | Acção | Microsoft.Compute/virtualMachineScaleSets/write | Cria um novo Conjunto de Dimensionamento da Máquina Virtual ou atualiza um já existente |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Consumption/balances/read | Liste a utilização de resumo para um período de faturação para um grupo de gestão. |
> | Acção | Microsoft.Consumption/budgets/delete | Elimine orçamentos por uma subscrição ou um grupo de gestão. |
> | Acção | Microsoft.Consumption/budgets/read | Liste os orçamentos por uma subscrição ou um grupo de gestão. |
> | Acção | Microsoft.Consumption/budgets/write | Cria e atualizar os orçamentos por uma subscrição ou um grupo de gestão. |
> | Acção | Microsoft.Consumption/charges/read | Custos de lista |
> | Acção | Microsoft.Consumption/credits/read | Créditos de lista |
> | Acção | Microsoft.Consumption/events/read | Listar os eventos |
> | Acção | Microsoft.Consumption/forecasts/read | Previsões de lista |
> | Acção | Microsoft.Consumption/lots/read | Muitos de lista |
> | Acção | Microsoft.Consumption/marketplaces/read | Liste os detalhes de utilização de recursos de mercado para um âmbito para subscrições EA e WebDirect. |
> | Acção | Microsoft.Consumption/operationresults/read | Lista operationresults |
> | Acção | Microsoft.Consumption/operations/read | Liste operações todos suportadas pelo fornecedor de recursos de Microsoft.Consumption. |
> | Acção | Microsoft.Consumption/operationstatus/read | Lista operationstatus |
> | Acção | Microsoft.Consumption/pricesheets/read | Liste os dados de Pricesheets para uma subscrição ou um grupo de gestão. |
> | Acção | Microsoft.Consumption/register/action | Registre-se para consumo RP |
> | Acção | Microsoft.Consumption/reservationDetails/read | Liste os detalhes de utilização de instâncias reservadas por grupos de gestão ou de ordem de reserva. Os dados de detalhes são por instância por nível do dia. |
> | Acção | Microsoft.Consumption/reservationRecommendations/read | Lista únicas ou partilhadas recomendações para as instâncias reservadas para uma subscrição. |
> | Acção | Microsoft.Consumption/reservationSummaries/read | Liste a utilização de resumo para as instâncias reservadas por grupos de gestão ou de ordem de reserva. Os dados de resumo estão no nível diário ou mensal. |
> | Acção | Microsoft.Consumption/reservationTransactions/read | Liste o histórico de transações para as instâncias reservadas por grupos de gestão. |
> | Acção | Microsoft.Consumption/tags/read | Listar etiquetas para subscrições e EA. |
> | Acção | Microsoft.Consumption/tenants/read | Inquilinos de lista |
> | Acção | Microsoft.Consumption/tenants/register/action | Registre-se a ação de âmbito do Microsoft.Consumption por um inquilino. |
> | Acção | Microsoft.Consumption/terms/read | Liste os termos de uma subscrição ou um grupo de gestão. |
> | Acção | Microsoft.Consumption/usageDetails/read | Liste os detalhes de utilização para um âmbito para subscrições EA e WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Exec para um contêiner específico. |
> | Acção | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Obtenha registos para um contentor específico. |
> | Acção | Microsoft.ContainerInstance/containerGroups/delete | Elimine o grupo de contentores específico. |
> | Acção | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição do diagnóstico para o grupo de contentores. |
> | Acção | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição de diagnóstico para o grupo de recursos. |
> | Acção | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para o grupo de contentores. |
> | Acção | Microsoft.ContainerInstance/containerGroups/read | Obter todos os grupos de contentores. |
> | Acção | Microsoft.ContainerInstance/containerGroups/restart/action | Reinicia um grupo de contentor específico. |
> | Acção | Microsoft.ContainerInstance/containerGroups/start/action | Inicia um grupo de contentores específico. |
> | Acção | Microsoft.ContainerInstance/containerGroups/stop/action | Deixa de um grupo de contentores específico. Serão possível desalocar a recursos e a faturação irá parar de computação. |
> | Acção | Microsoft.ContainerInstance/containerGroups/write | Crie ou atualize um grupo de contentores específico. |
> | Acção | Microsoft.ContainerInstance/locations/cachedImages/read | Obtém as imagens em cache para a subscrição numa região. |
> | Acção | Microsoft.ContainerInstance/locations/capabilities/read | Obtenha os recursos para uma região. |
> | Acção | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Notifica Microsoft.ContainerInstance que rede ou sub-rede virtual está a ser eliminado. |
> | Acção | Microsoft.ContainerInstance/locations/usages/read | Obter a utilização para uma região específica. |
> | Acção | Microsoft.ContainerInstance/register/action | Regista a subscrição do fornecedor de recursos de instância de contentor e ativa a criação de contas de contentores. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ContainerRegistry/checkNameAvailability/read | Verifica se o nome do container registry está disponível para utilização. |
> | Acção | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft. containerregistry que rede ou sub-rede virtual está a ser eliminada |
> | Acção | Microsoft.ContainerRegistry/locations/operationResults/read | Obtém um resultado da operação assíncrona |
> | Acção | Microsoft.ContainerRegistry/operations/read | Apresenta uma lista de todas as operações de API de REST de registo de contentor do Azure disponíveis |
> | Acção | Microsoft.ContainerRegistry/register/action | Regista a subscrição para o fornecedor de recursos de registo de contentor e permite a criação de registos de contentores. |
> | Acção | Microsoft.ContainerRegistry/registries/artifacts/delete | Elimine artefactos num registo de contentor. |
> | Acção | Microsoft.ContainerRegistry/registries/builds/cancel/action | Cancela uma compilação existente. |
> | Acção | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Obtém uma ligação para transferir os registos de compilação. |
> | Acção | Microsoft.ContainerRegistry/registries/builds/read | Obtém as propriedades da compilação especificada ou lista todas as compilações para o registo de contentor especificado. |
> | Acção | Microsoft.ContainerRegistry/registries/builds/write | Atualiza uma compilação para um registo de contentores com os parâmetros especificados. |
> | Acção | Microsoft.ContainerRegistry/registries/buildTasks/delete | Elimina uma tarefa de compilação de um registo de contentor. |
> | Acção | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Lista as propriedades de controle de origem para uma tarefa de compilação. |
> | Acção | Microsoft.ContainerRegistry/registries/buildTasks/read | Obtém as propriedades da tarefa de compilação especificado ou lista todas as tarefas de compilação para o registo de contentor especificado. |
> | Acção | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Elimina um passo de compilação a partir de uma tarefa de compilação. |
> | Acção | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Apresenta os argumentos de compilação para um passo de compilação, incluindo os argumentos secretos. |
> | Acção | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Obtém as propriedades do passo de compilação especificado ou lista todos os passos de compilação para a tarefa de compilação especificado. |
> | Acção | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Cria ou atualiza um passo de compilação para uma tarefa de compilação com os parâmetros especificados. |
> | Acção | Microsoft.ContainerRegistry/registries/buildTasks/write | Cria ou atualiza uma tarefa de compilação para um registo de contentores com os parâmetros especificados. |
> | Acção | Microsoft.ContainerRegistry/registries/delete | Elimina um registo de contentor. |
> | Acção | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Elimina um filtro de grelha de eventos a partir de um registo de contentor. |
> | Acção | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Obtém as propriedades do filtro de grelha de eventos especificado ou lista todos os filtros de grelha de eventos para o registo de contentor especificado. |
> | Acção | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Cria ou atualiza um filtro de grelha de eventos para um registo de contentores com os parâmetros especificados. |
> | Acção | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Obtém a localização de carregamento para o usuário seja capaz de carregar a origem. |
> | Acção | Microsoft.ContainerRegistry/registries/importImage/action | Importe imagem para o registo de contentor com os parâmetros especificados. |
> | Acção | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Obter origem de carregar a localização de url para um registo de contentor. |
> | Acção | Microsoft.ContainerRegistry/registries/listCredentials/action | Lista as credenciais de início de sessão para o registo de contentor especificado. |
> | Acção | Microsoft.ContainerRegistry/registries/listPolicies/read | Lista as políticas para o registo de contentor especificado |
> | Acção | Microsoft.ContainerRegistry/registries/listUsages/read | Lista as utilizações de quota para o registo de contentor especificado. |
> | Acção | Microsoft.ContainerRegistry/registries/metadata/read | Obtém os metadados de um repositório específico para um registo de contentor |
> | Acção | Microsoft.ContainerRegistry/registries/metadata/write | Atualiza os metadados de um repositório para um registo de contentor |
> | Acção | Microsoft.ContainerRegistry/registries/operationStatuses/read | Obtém o estado da operação de async um registo |
> | Acção | Microsoft.ContainerRegistry/registries/pull/read | Solicitar ou obter imagens a partir de um registo de contentor. |
> | Acção | Microsoft.ContainerRegistry/registries/push/write | Push ou escrever as imagens para um registo de contentor. |
> | Acção | Microsoft.ContainerRegistry/registries/quarantineRead/read | Solicitar ou obter imagens em quarentena do container registry |
> | Acção | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Escrita/modificar o estado de quarentena de imagens em quarentena |
> | Acção | Microsoft.ContainerRegistry/registries/queueBuild/action | Cria uma nova compilação com base nos parâmetros de pedido e adicioná-lo para a fila de compilação. |
> | Acção | Microsoft.ContainerRegistry/registries/read | Obtém as propriedades de registo de contentor especificado ou apresenta uma lista de todos os registos de contentores sob a subscrição ou grupo de recursos especificado. |
> | Acção | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Regenera um das credenciais de início de sessão para o registo de contentor especificado. |
> | Acção | Microsoft.ContainerRegistry/registries/replications/delete | Elimina uma replicação de um registo de contentor. |
> | Acção | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Obtém um Estado de operação de async de replicação |
> | Acção | Microsoft.ContainerRegistry/registries/replications/read | Obtém as propriedades da replicação especificada ou lista todas as replicações para o registo de contentor especificado. |
> | Acção | Microsoft.ContainerRegistry/registries/replications/write | Cria ou atualiza uma replicação para um registo de contentores com os parâmetros especificados. |
> | Acção | Microsoft.ContainerRegistry/registries/runs/cancel/action | Cancele uma execução existente. |
> | Acção | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Obtém o registo de URL de SAS para uma execução. |
> | Acção | Microsoft.ContainerRegistry/registries/runs/read | Obtém as propriedades de uma execução de um registo de contentor ou lista de execuções. |
> | Acção | Microsoft.ContainerRegistry/registries/runs/write | Atualiza uma execução. |
> | Acção | Microsoft.ContainerRegistry/registries/scheduleRun/action | Agende uma execução de um registo de contentor. |
> | Acção | Microsoft.ContainerRegistry/registries/sign/write | Push/Pull metadados de confiança de conteúdo para um registo de contentor. |
> | Acção | Microsoft.ContainerRegistry/registries/tasks/delete | Elimina uma tarefa para um registo de contentor. |
> | Acção | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Liste todos os detalhes de uma tarefa para um registo de contentor. |
> | Acção | Microsoft.ContainerRegistry/registries/tasks/read | Obtém uma tarefa para um registo de contentor ou uma lista de todas as tarefas. |
> | Acção | Microsoft.ContainerRegistry/registries/tasks/write | Cria ou atualiza uma tarefa para um registo de contentor. |
> | Acção | Microsoft.ContainerRegistry/registries/updatePolicies/write | Atualiza as políticas para o registo de contentor especificado |
> | Acção | Microsoft.ContainerRegistry/registries/webhooks/delete | Elimina um webhook a partir de um registo de contentor. |
> | Acção | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Obtém a configuração de URI de serviço e os cabeçalhos personalizados para o webhook. |
> | Acção | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Apresenta uma lista de eventos recentes do webhook especificado. |
> | Acção | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Obtém o estado da operação de async um webhook |
> | Acção | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Aciona um evento de ping para serem enviados para o webhook. |
> | Acção | Microsoft.ContainerRegistry/registries/webhooks/read | Obtém as propriedades do webhook especificado ou lista todos os webhooks para o registo de contentor especificado. |
> | Acção | Microsoft.ContainerRegistry/registries/webhooks/write | Cria ou atualiza um webhook para um registo de contentores com os parâmetros especificados. |
> | Acção | Microsoft.ContainerRegistry/registries/write | Cria ou atualiza um registo de contentores com os parâmetros especificados. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ContainerService/containerServices/delete | Elimina um serviço de contentor |
> | Acção | Microsoft.ContainerService/containerServices/read | Obter um serviço de contentor |
> | Acção | Microsoft.ContainerService/containerServices/write | Cria um novo serviço de contentor ou atualiza um existente |
> | Acção | Microsoft.ContainerService/locations/operationresults/read | Obtém o estado de um resultado de operação assíncrona |
> | Acção | Microsoft.ContainerService/locations/operations/read | Obtém o estado de uma operação assíncrona |
> | Acção | Microsoft.ContainerService/locations/orchestrators/read | Lista os orquestradores suportados |
> | Acção | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Obter um perfil de acesso de cluster gerido por nome de função com a credencial da lista |
> | Acção | Microsoft.ContainerService/managedClusters/accessProfiles/read | Obter um perfil de acesso de cluster gerido por nome de função |
> | Acção | Microsoft.ContainerService/managedClusters/agentPools/delete | Elimina um conjunto de agentes |
> | Acção | Microsoft.ContainerService/managedClusters/agentPools/read | Obtém um conjunto de agentes |
> | Acção | Microsoft.ContainerService/managedClusters/agentPools/write | Cria um novo conjunto de agentes ou atualiza um já existente |
> | Acção | Microsoft.ContainerService/managedClusters/delete | Elimina um cluster gerido |
> | Acção | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Listar a credencial clusterAdmin de um cluster gerido |
> | Acção | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Listar a credencial clusterUser de um cluster gerido |
> | Acção | Microsoft.ContainerService/managedClusters/read | Obter um cluster gerido |
> | Acção | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Repor o perfil do AAD de um cluster gerido |
> | Acção | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Repor o perfil de principal de serviço de um cluster gerido |
> | Acção | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | Obtém o perfil de atualização do cluster |
> | Acção | Microsoft.ContainerService/managedClusters/write | Cria um novo cluster gerido ou atualiza um existente |
> | Acção | Microsoft.ContainerService/openShiftClusters/delete | Eliminar um Cluster de Open Shift |
> | Acção | Microsoft.ContainerService/openShiftClusters/read | Obter um Cluster de Open Shift |
> | Acção | Microsoft.ContainerService/openShiftClusters/write | Cria um novo Cluster de Shift aberto ou atualiza um já existente |
> | Acção | Microsoft.ContainerService/openShiftManagedClusters/delete | Eliminar um Cluster gerido Open Shift |
> | Acção | Microsoft.ContainerService/openShiftManagedClusters/read | Obter um Cluster gerido Open Shift |
> | Acção | Microsoft.ContainerService/openShiftManagedClusters/write | Cria um novo Open Shift geridos Cluster ou atualiza um já existente |
> | Acção | Microsoft.ContainerService/operations/read | Lista as operações disponíveis no fornecedor de recursos Microsoft.ContainerService |
> | Acção | Microsoft.ContainerService/register/action | Regista a Subscrição no fornecedor de recursos Microsoft.ContainerService |
> | Acção | Microsoft.ContainerService/unregister/action | Anula a Subscrição com o fornecedor de recursos Microsoft.ContainerService |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ContentModerator/applications/delete | Operação de Eliminação |
> | Acção | Microsoft.ContentModerator/applications/listSecrets/action | Listar Segredos |
> | Acção | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Ler os Tokens de início de sessão único |
> | Acção | Microsoft.ContentModerator/applications/read | Operação de Leitura |
> | Acção | Microsoft.ContentModerator/applications/write | Operação de Escrita |
> | Acção | Microsoft.ContentModerator/applications/write | Operação de Escrita |
> | Acção | Microsoft.ContentModerator/listCommunicationPreference/action | Listar preferência de comunicação |
> | Acção | Microsoft.ContentModerator/operations/read | operações de leitura |
> | Acção | Microsoft.ContentModerator/updateCommunicationPreference/action | Atualizar preferência de comunicação |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.CostManagement/cloudConnectors/delete | Elimine o cloudConnector especificado. |
> | Acção | Microsoft.CostManagement/cloudConnectors/read | Liste cloudConnectors para o usuário autenticado. |
> | Acção | Microsoft.CostManagement/cloudConnectors/write | Criar ou atualizar o cloudConnector especificado. |
> | Acção | Microsoft.CostManagement/dimensions/read | Liste dimensões todos suportadas por um âmbito. |
> | Acção | Microsoft.CostManagement/exports/action | Execute a exportação especificada. |
> | Acção | Microsoft.CostManagement/exports/delete | Elimine a exportação especificada. |
> | Acção | Microsoft.CostManagement/exports/read | Liste as exportações pelo âmbito de aplicação. |
> | Acção | Microsoft.CostManagement/exports/run/action | Exporta a execução. |
> | Acção | Microsoft.CostManagement/exports/write | Criar ou atualizar a exportação especificada. |
> | Acção | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Liste externalSubscriptions dentro de um externalBillingAccount para o usuário autenticado. |
> | Acção | Microsoft.CostManagement/externalBillingAccounts/read | Liste externalBillingAccounts para o usuário autenticado. |
> | Acção | Microsoft.CostManagement/externalSubscriptions/read | Liste externalSubscriptions para o usuário autenticado. |
> | Acção | Microsoft.CostManagement/externalSubscriptions/write | Atualizar o grupo de gestão associadas de externalSubscription |
> | Acção | Microsoft.CostManagement/query/action | Consultar dados de utilização por um âmbito. |
> | Acção | Microsoft.CostManagement/query/read | Consultar dados de utilização por um âmbito. |
> | Acção | Microsoft.CostManagement/register/action | Registre-se a ação de âmbito do Microsoft.CostManagement por uma subscrição. |
> | Acção | Microsoft.CostManagement/reports/action | Agendar relatórios nos dados de utilização por um âmbito. |
> | Acção | Microsoft.CostManagement/reports/read | Agendar relatórios nos dados de utilização por um âmbito. |
> | Acção | Microsoft.CostManagement/tenants/register/action | Registre-se a ação de âmbito do Microsoft.CostManagement por um inquilino. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.CustomerInsights/hubs/adobemetadata/action | Criar ou atualizar quaisquer metadados de Adobe de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/adobemetadata/read | Ler quaisquer metadados de Adobe de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Eliminar qualquer política de assinatura de acesso partilhado do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Leia a que política de assinatura de acesso de partilhadas quaisquer informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Regenerar a chave primária da política de assinatura de acesso de partilhado informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Regenerar chave secundária da política de assinatura de acesso de partilhado informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Criar ou atualizar qualquer política de assinatura de acesso partilhado do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/connectors/activate/action | Ativar qualquer conector de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/connectors/activate/action | Ativar qualquer conector de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/connectors/delete | Eliminar qualquer conector de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Obter qualquer Estado de runtime do conector de informações de cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Ativar qualquer mapeamento de conector de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Eliminar qualquer mapeamento de conector de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Ler qualquer resultado da operação de mapeamento conector de informações de cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Ler qualquer mapeamento de conector de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Criar ou atualizar qualquer mapeamento de conector de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/connectors/operations/read | Ler qualquer resultado da operação de conector de informações de cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/connectors/read | Ler qualquer conector de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Criar ou atualizar qualquer as de informações de autenticação de conector de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/connectors/update/action | Atualizar qualquer conector de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/connectors/write | Criar ou atualizar qualquer conector de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/crmmetadata/action | Criar ou atualizar quaisquer metadados de Crm do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/crmmetadata/read | Ler quaisquer metadados de Crm do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/delete | Eliminar qualquer Hub de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/gdpr/delete | Eliminar qualquer Gdpr de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/gdpr/read | Ler qualquer Gdpr de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/gdpr/write | Criar ou atualizar qualquer Gdpr de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Obtenha créditos de faturação de Hub do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Obter histórico de faturação de Hub do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/images/delete | Eliminar qualquer imagem de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/images/read | Ler qualquer imagem de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/images/write | Criar ou atualizar qualquer imagem de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/interactions/delete | Eliminar qualquer interações de informações do cliente do azure |
> | Acção | Microsoft.CustomerInsights/hubs/interactions/operations/read | Ler qualquer resultado da operação de interação de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/interactions/read | Ler qualquer interação do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Sugerir Links de relação para qualquer interações de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/interactions/write | Criar ou atualizar qualquer interação do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/kpi/delete | Eliminar qualquer indicador de chave de desempenho do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/kpi/operations/read | Ler qualquer resultado da operação indicadores chave de desempenho do Azure Customer Insights |
> | Acção | Microsoft.CustomerInsights/hubs/kpi/read | Ler qualquer indicador de chave de desempenho do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Reprocessar qualquer indicadores de chave de desempenho do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/kpi/write | Criar ou atualizar qualquer indicador de chave de desempenho do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/links/delete | Eliminar todas as ligações dos clientes do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/links/operations/read | Ler qualquer resultado da operação de ligações de informações de cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/links/read | Leia as ligações do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/links/write | Criar ou atualizar as ligações de cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/msemetadata/action | Criar ou atualizar quaisquer metadados de Mse de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/msemetadata/read | Ler quaisquer metadados de Mse de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/operationresults/read | Obter resultados da operação do cliente do Azure Insights Hub |
> | Acção | Microsoft.CustomerInsights/hubs/predictions/delete | Eliminar qualquer previsões de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/predictions/operations/read | Ler qualquer resultado da operação de previsões de informações de cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/predictions/read | Ler qualquer previsões de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/predictions/write | Criar ou atualizar qualquer previsões de cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Eliminar quaisquer políticas de correspondência preditiva de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Ler qualquer resultado da operação de políticas de correspondência do Azure ao cliente informações Preditivas |
> | Acção | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Ler quaisquer políticas de correspondência preditiva de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Criar ou atualizar as políticas de correspondência preditiva de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/profiles/delete | Eliminar qualquer perfil de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/profiles/operations/read | Ler qualquer resultado de operação do perfil de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/profiles/read | Ler qualquer perfil de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/profiles/write | Escrever qualquer perfil de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Acção | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Acção | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para o recurso |
> | Acção | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para o recurso |
> | Acção | Microsoft.CustomerInsights/hubs/read | Ler qualquer Hub de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Eliminar as ligações de relação de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Ler qualquer resultado da operação de ligações de relação de informações de cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Leia as ligações de relação de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Criar ou atualizar quaisquer Links de relação do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/relationships/delete | Eliminar quaisquer relações de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/relationships/operations/read | Ler qualquer resultado da operação de relações de informações de clientes do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/relationships/read | Ler quaisquer relações de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/relationships/write | Criar ou atualizar as relações de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Eliminar qualquer atribuição de Rbac do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Ler qualquer resultado da operação de atribuição de Rbac do Azure Customer Insights |
> | Acção | Microsoft.CustomerInsights/hubs/roleAssignments/read | Ler qualquer atribuição de Rbac do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/roleAssignments/write | Criar ou atualizar qualquer atribuição de Rbac do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/roles/read | Ler quaisquer funções de Rbac do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Criar ou atualizar quaisquer metadados de SalesForce de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Ler quaisquer metadados de SalesForce de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/segments/delete | Eliminar qualquer segmento de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Segmentos de qualquer dinâmica de informações do cliente do Azure de gestão |
> | Acção | Microsoft.CustomerInsights/hubs/segments/read | Ler qualquer segmento de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/segments/static/action | Segmentos de qualquer estática de informações do cliente do Azure de gestão |
> | Acção | Microsoft.CustomerInsights/hubs/segments/write | Criar ou atualizar qualquer segmento de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Eliminar qualquer SqlConnectionStrings de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Ler qualquer SqlConnectionStrings de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Criar ou atualizar qualquer SqlConnectionStrings de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Gerar o esquema do tipo de sugerir a partir de dados de exemplo |
> | Acção | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Gerir as definições do hub de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/hubs/views/delete | Eliminar qualquer vista de aplicação do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/views/read | Ler qualquer vista de aplicação do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/views/write | Criar ou atualizar qualquer vista de aplicação do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/widgettypes/read | Ler quaisquer tipos de Widget de aplicação do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/hubs/write | Criar ou atualizar qualquer Hub de informações do cliente do Azure |
> | Acção | Microsoft.CustomerInsights/operations/read | Ler Metadatas de Api do cliente do Azure Insights |
> | Acção | Microsoft.CustomerInsights/register/action | Regista a subscrição para o fornecedor de recursos de informações do cliente e permite a criação de recursos de informações do cliente |
> | Acção | Microsoft.CustomerInsights/unregister/action | Anula o registo da subscrição para o fornecedor de recursos de informações do cliente |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Permite marcar uma recolha para envios de devolução. |
> | Acção | Microsoft.DataBox/jobs/cancel/action | Cancela uma encomenda em curso. |
> | Acção | Microsoft.DataBox/jobs/delete | Eliminar os pedidos |
> | Acção | Microsoft.DataBox/jobs/listCredentials/action | Lista as credenciais não encriptadas relativas à encomenda. |
> | Acção | Microsoft.DataBox/jobs/read | Listar ou obter os pedidos |
> | Acção | Microsoft.DataBox/jobs/write | Criar ou atualizar os pedidos |
> | Acção | Microsoft.DataBox/locations/availableSkus/action | Este método devolve a lista de SKUs disponíveis. |
> | Acção | Microsoft.DataBox/locations/operationResults/read | Listar ou obter os resultados da operação |
> | Acção | Microsoft.DataBox/locations/validateAddress/action | Valida o endereço de envio e fornece endereços alternativos, caso existam. |
> | Acção | Microsoft.DataBox/register/action | Registar fornecedor Microsoft.Databox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Apresenta uma lista ou obter alertas |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Apresenta uma lista ou obter alertas |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Elimina as agendas de largura de banda |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Apresenta uma lista ou obtém o resultado da operação |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Apresenta uma lista ou obtém as agendas de largura de banda |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Apresenta uma lista ou obtém as agendas de largura de banda |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Cria ou atualiza as agendas de largura de banda |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Elimina os dispositivos de limite de caixa de dados |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Transferir Atualizações no dispositivo |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/extendedInformation/action | Obtém as informações expandidas de recursos |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Obtém as informações expandidas de recursos |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Instalar Atualizações no dispositivo |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Apresenta uma lista ou obtém as tarefas |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Apresenta uma lista ou obtém as definições de rede do dispositivo |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Apresenta uma lista ou obtém o resultado da operação |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | Apresenta uma lista ou obtém o estado da operação |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Elimina os pedidos |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Apresenta uma lista ou obtém o resultado da operação |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Apresenta uma lista ou obtém os pedidos |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Apresenta uma lista ou obtém os pedidos |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Cria ou atualiza os pedidos |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Apresenta uma lista ou obtém os dispositivos de limite de caixa de dados |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Apresenta uma lista ou obtém os dispositivos de limite de caixa de dados |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Apresenta uma lista ou obtém os dispositivos de limite de caixa de dados |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Elimina as funções |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Apresenta uma lista ou obtém o resultado da operação |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Apresenta uma lista ou obtém as funções |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Apresenta uma lista ou obtém as funções |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Cria ou atualiza as funções |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Procurar atualizações |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Apresenta uma lista ou obtém o resultado da operação |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Atualizar definições de segurança |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Elimina as partilhas |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Apresenta uma lista ou obtém o resultado da operação |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Apresenta uma lista ou obtém as partilhas |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Apresenta uma lista ou obtém as partilhas |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Atualizar os metadados de partilha com os dados da cloud |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Cria ou atualiza as partilhas |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Elimina as credenciais da conta de armazenamento |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Apresenta uma lista ou obtém o resultado da operação |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Apresenta uma lista ou obtém as credenciais da conta de armazenamento |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Apresenta uma lista ou obtém as credenciais da conta de armazenamento |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Cria ou atualiza as credenciais da conta de armazenamento |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Elimina os disparadores |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Apresenta uma lista ou obtém o resultado da operação |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Apresenta uma lista ou obtém os disparadores |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Apresenta uma lista ou obtém os disparadores |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Cria ou atualiza os disparadores |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Apresenta uma lista ou obtém a atualização do resumo |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Carregar o certificado para o registo do dispositivo |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Elimina os utilizadores da partilha |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Apresenta uma lista ou obtém o resultado da operação |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Apresenta uma lista ou obtém os utilizadores da partilha |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Apresenta uma lista ou obtém os utilizadores da partilha |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Cria ou atualiza os utilizadores da partilha |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Cria ou atualiza os dispositivos de limite de caixa de dados |
> | Acção | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Cria ou atualiza os dispositivos de limite de caixa de dados |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Databricks/locations/getNetworkPolicies/action | Obter políticas de intenção de rede para uma sub-rede com base na localização utilizada pelo NRP |
> | Acção | Microsoft.Databricks/register/action | Registar-se na Databricks. |
> | Acção | Microsoft.Databricks/workspaces/delete | Remove uma área de trabalho da Databricks. |
> | Acção | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Define as definições de diagnósticos disponíveis para a área de trabalho do Databricks |
> | Acção | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Adiciona ou modifica as definições de diagnóstico. |
> | Acção | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Obtém as definições de registo disponíveis para a área de trabalho do Databricks |
> | Acção | Microsoft.Databricks/workspaces/read | Obtém uma lista de áreas de trabalho da Databricks. |
> | Acção | Microsoft.Databricks/workspaces/write | Cria uma área de trabalho da Databricks. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.DataCatalog/datacatalogs/delete | Elimine o recurso de DataCatalog para fornecedor de recursos do catálogo de dados. |
> | Acção | Microsoft.DataCatalog/datacatalogs/read | Ler recurso DataCatalog para fornecedor de recursos do catálogo de dados. |
> | Acção | Microsoft.DataCatalog/datacatalogs/write | Escreva DataCatalog recursos para o fornecedor de recursos do catálogo de dados. |
> | Acção | Microsoft.DataCatalog/operations/read | Lê todas as operações disponíveis no fornecedor de recursos do catálogo de dados. |
> | Acção | Microsoft.DataCatalog/register/action | Registre-se a subscrição para o fornecedor de recursos do catálogo de dados |
> | Acção | Microsoft.DataCatalog/unregister/action | Anular o registo da subscrição para o fornecedor de recursos do catálogo de dados |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Verifica se o nome da fábrica de dados está disponível para utilização. |
> | Acção | Microsoft.DataFactory/datafactories/activitywindows/read | Lê o Windows de atividade na fábrica de dados com os parâmetros especificados. |
> | Acção | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Lê o Windows de atividade para a atividade do Pipeline com os parâmetros especificados. |
> | Acção | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Lê o Windows de atividade para o Pipeline com os parâmetros especificados. |
> | Acção | Microsoft.DataFactory/datafactories/datapipelines/delete | Elimina qualquer Pipeline. |
> | Acção | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Coloca em pausa qualquer Pipeline. |
> | Acção | Microsoft.DataFactory/datafactories/datapipelines/read | Lê qualquer Pipeline. |
> | Acção | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Retoma qualquer Pipeline. |
> | Acção | Microsoft.DataFactory/datafactories/datapipelines/update/action | Atualiza qualquer Pipeline. |
> | Acção | Microsoft.DataFactory/datafactories/datapipelines/write | Cria ou atualiza qualquer Pipeline. |
> | Acção | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Lê o Windows de atividade para o conjunto de dados com os parâmetros especificados. |
> | Acção | Microsoft.DataFactory/datafactories/datasets/delete | Elimina a qualquer conjunto de dados. |
> | Acção | Microsoft.DataFactory/datafactories/datasets/read | Lê qualquer conjunto de dados. |
> | Acção | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Lê a execução de setor de dados para determinado conjunto de dados com a hora de início especificada. |
> | Acção | Microsoft.DataFactory/datafactories/datasets/slices/read | Obtém os setores de dados em determinado período. |
> | Acção | Microsoft.DataFactory/datafactories/datasets/slices/write | Atualize o estado do setor de dados. |
> | Acção | Microsoft.DataFactory/datafactories/datasets/write | Cria ou atualiza qualquer conjunto de dados. |
> | Acção | Microsoft.DataFactory/datafactories/delete | Elimina a fábrica de dados. |
> | Acção | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Lê as informações de ligação para qualquer Gateway. |
> | Acção | Microsoft.DataFactory/datafactories/gateways/delete | Elimina qualquer Gateway. |
> | Acção | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Lista as chaves de autenticação para qualquer Gateway. |
> | Acção | Microsoft.DataFactory/datafactories/gateways/read | Lê qualquer Gateway. |
> | Acção | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Regenera as chaves de autenticação para qualquer Gateway. |
> | Acção | Microsoft.DataFactory/datafactories/gateways/write | Cria ou atualiza qualquer Gateway. |
> | Acção | Microsoft.DataFactory/datafactories/linkedServices/delete | Elimina a qualquer serviço ligado. |
> | Acção | Microsoft.DataFactory/datafactories/linkedServices/read | Lê a qualquer serviço ligado. |
> | Acção | Microsoft.DataFactory/datafactories/linkedServices/write | Cria ou atualiza a qualquer serviço ligado. |
> | Acção | Microsoft.DataFactory/datafactories/read | Lê a fábrica de dados. |
> | Acção | Microsoft.DataFactory/datafactories/runs/loginfo/read | Lê um URI de SAS para um contentor de BLOBs que contém os registos. |
> | Acção | Microsoft.DataFactory/datafactories/tables/delete | Elimina a qualquer conjunto de dados. |
> | Acção | Microsoft.DataFactory/datafactories/tables/read | Lê qualquer conjunto de dados. |
> | Acção | Microsoft.DataFactory/datafactories/tables/write | Cria ou atualiza qualquer conjunto de dados. |
> | Acção | Microsoft.DataFactory/datafactories/write | Cria ou atualiza a fábrica de dados. |
> | Acção | Microsoft.DataFactory/factories/cancelpipelinerun/action | Cancela o pipeline de execução especificado pelo ID de execução. |
> | Acção | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Cria uma sessão de depuração do fluxo de dados. |
> | Acção | Microsoft.DataFactory/factories/dataflows/delete | Elimina o fluxo de dados. |
> | Acção | Microsoft.DataFactory/factories/dataflows/read | Lê o fluxo de dados. |
> | Acção | Microsoft.DataFactory/factories/dataflows/write | Criar ou atualizar o fluxo de dados |
> | Acção | Microsoft.DataFactory/factories/datasets/delete | Elimina a qualquer conjunto de dados. |
> | Acção | Microsoft.DataFactory/factories/datasets/read | Lê qualquer conjunto de dados. |
> | Acção | Microsoft.DataFactory/factories/datasets/write | Cria ou atualiza qualquer conjunto de dados. |
> | Acção | Microsoft.DataFactory/factories/delete | Elimina a fábrica de dados. |
> | Acção | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Elimina uma sessão de depuração do fluxo de dados. |
> | Acção | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Obtém acesso ao serviço de plano de dados do ADF. |
> | Acção | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Acesso de leitura ao serviço de plano de dados do ADF. |
> | Acção | Microsoft.DataFactory/factories/getFeatureValue/action | Obter o valor de funcionalidade de controlo de exposição para a localização específica. |
> | Acção | Microsoft.DataFactory/factories/getFeatureValue/read | Lê o valor de funcionalidade de controlo de exposição para a localização específica. |
> | Acção | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Dispõe do acesso de GitHub token. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/delete | Elimina a qualquer tempo de execução de integração. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Lê as informações de ligação do Runtime de integração. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Obter metadados de Runtime de integração do SSIS para o Runtime de integração especificado. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Lê o estado de Runtime de integração. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Crie a referência do Runtime de integração ligada no Runtime de integração partilhado especificado. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Lista as chaves de autenticação para qualquer Runtime de integração. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Obtém os dados de monitorização para qualquer Runtime de integração. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Elimina o nó para o Runtime de integração especificado. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Devolve o endereço IP para o nó especificado do Integration Runtime. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Lê o nó para o Runtime de integração especificado. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Atualiza um nó do Integration Runtime autoalojado. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/read | Lê qualquer Runtime de integração. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Atualize os metadados de Runtime de integração do SSIS para o Runtime de integração especificado. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Regenera as chaves de autenticação para o Runtime de integração especificado. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Remove ligado referências de Runtime de integração do Runtime de integração especificado. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/start/action | Inicia qualquer Runtime de integração. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Deixa de qualquer Runtime de integração. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Sincroniza as credenciais para o Runtime de integração especificado. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Atualiza o Runtime de integração especificado. |
> | Acção | Microsoft.DataFactory/factories/integrationruntimes/write | Cria ou atualiza qualquer Runtime de integração. |
> | Acção | Microsoft.DataFactory/factories/linkedServices/delete | Elimina o serviço ligado. |
> | Acção | Microsoft.DataFactory/factories/linkedServices/read | Leituras de serviço ligado. |
> | Acção | Microsoft.DataFactory/factories/linkedServices/write | Criar ou atualizar o serviço ligado |
> | Acção | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Lê que a atividade é executada para o ID de execução de pipeline especificado |
> | Acção | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Cancela o pipeline de execução especificado pelo ID de execução. |
> | Acção | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | A atividade é executada para o pipeline especificado de consultas executadas ID. |
> | Acção | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Lê que o resultado da atividade de consulta é executada para o ID de execução de pipeline especificado |
> | Acção | Microsoft.DataFactory/factories/pipelineruns/read | Lê as execuções de Pipeline. |
> | Acção | Microsoft.DataFactory/factories/pipelines/createrun/action | Cria uma execução do pipeline. |
> | Acção | Microsoft.DataFactory/factories/pipelines/delete | Elimina o Pipeline. |
> | Acção | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Obtém o progresso das execuções de atividade. |
> | Acção | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Lê a execução de Pipeline. |
> | Acção | Microsoft.DataFactory/factories/pipelines/read | Lê o Pipeline. |
> | Acção | Microsoft.DataFactory/factories/pipelines/write | Criar ou atualizar o Pipeline |
> | Acção | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Consulta as execuções de Pipeline de depuração. |
> | Acção | Microsoft.DataFactory/factories/querypipelineruns/action | Consulta as execuções de Pipeline. |
> | Acção | Microsoft.DataFactory/factories/querypipelineruns/read | Lê o resultado das execuções de Pipeline de consulta. |
> | Acção | Microsoft.DataFactory/factories/querytriggerruns/action | Consulta as execuções do acionador. |
> | Acção | Microsoft.DataFactory/factories/querytriggerruns/read | Lê o resultado de execuções do acionador. |
> | Acção | Microsoft.DataFactory/factories/read | Lê a fábrica de dados. |
> | Acção | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Inicia uma sessão de depuração do fluxo de dados. |
> | Acção | Microsoft.DataFactory/factories/triggerruns/read | Lê as execuções do acionador. |
> | Acção | Microsoft.DataFactory/factories/triggers/delete | Elimina qualquer acionador. |
> | Acção | Microsoft.DataFactory/factories/triggers/read | Lê qualquer acionador. |
> | Acção | Microsoft.DataFactory/factories/triggers/start/action | Inicia qualquer acionador. |
> | Acção | Microsoft.DataFactory/factories/triggers/stop/action | Deixa de qualquer acionador. |
> | Acção | Microsoft.DataFactory/factories/triggers/triggerruns/read | Lê as execuções do acionador. |
> | Acção | Microsoft.DataFactory/factories/triggers/write | Cria ou atualiza qualquer acionador. |
> | Acção | Microsoft.DataFactory/factories/write | Criar ou atualizar a fábrica de dados |
> | Acção | Microsoft.DataFactory/locations/configureFactoryRepo/action | Configura o repositório para a fábrica. |
> | Acção | Microsoft.DataFactory/locations/getFeatureValue/action | Obter o valor de funcionalidade de controlo de exposição para a localização específica. |
> | Acção | Microsoft.DataFactory/locations/getFeatureValue/read | Lê o valor de funcionalidade de controlo de exposição para a localização específica. |
> | Acção | Microsoft.DataFactory/operations/read | Lê todas as operações no fornecedor de fábrica de dados do Microsoft. |
> | Acção | Microsoft.DataFactory/register/action | Regista a subscrição para o fornecedor de recursos de fábrica de dados. |
> | Acção | Microsoft.DataFactory/unregister/action | Anula o registo da subscrição para o fornecedor de recursos de fábrica de dados. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Elimine uma política de computação. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Obtenha informações sobre uma política de computação. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Criar ou atualizar uma política de computação. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Desassocie a uma conta de DataLakeStore de uma conta no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Obtenha informações sobre uma conta ligada do DataLakeStore de uma conta no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Criar ou atualizar uma conta ligada do DataLakeStore de uma conta no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/delete | Elimine uma conta no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Elimine uma regra de firewall. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Obtenha informações sobre uma regra de firewall. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Criar ou atualizar uma regra de firewall. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Obtenha o resultado de uma operação de conta no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/read | Obtenha informações sobre uma conta no DataLakeAnalytics existente. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Tokens SAS de lista de contentores de armazenamento de uma conta de armazenamento ligada de uma conta no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Obter contentores de uma conta de armazenamento ligada de uma conta no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Desassocie a uma conta de armazenamento de uma conta no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Obtenha informações sobre contas de armazenamento ligadas de uma conta no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Criar ou atualizar uma conta de armazenamento ligada de uma conta no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Conceder permissões para cancelar as tarefas submetidas por outros utilizadores. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Transferi SystemMaxAnalyticsUnits entre contas no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Elimine uma regra de rede virtual. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Obtenha informações sobre uma regra de rede virtual. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Criar ou atualizar uma regra de rede virtual. |
> | Acção | Microsoft.DataLakeAnalytics/accounts/write | Criar ou atualizar uma conta no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/locations/capability/read | Obtenha informações de capacidade de uma subscrição relativamente à utilização no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Verifica a disponibilidade de um nome de conta no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/locations/operationResults/read | Obtenha o resultado de uma operação de conta no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/locations/usages/read | Obtenha informações de utilizações de quota de uma subscrição relativamente à utilização no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/operations/read | Obter operações disponíveis no DataLakeAnalytics. |
> | Acção | Microsoft.DataLakeAnalytics/register/action | Registe a subscrição no datalakeanalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.DataLakeStore/accounts/delete | Elimine uma conta do DataLakeStore. |
> | Acção | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Ative o Cofre de chaves para uma conta do DataLakeStore. |
> | Acção | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Elimine um filtro de EventGrid. |
> | Acção | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Obtenha um filtro de EventGrid. |
> | Acção | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Criar ou atualizar um filtro de EventGrid. |
> | Acção | Microsoft.DataLakeStore/accounts/firewallRules/delete | Elimine uma regra de firewall. |
> | Acção | Microsoft.DataLakeStore/accounts/firewallRules/read | Obtenha informações sobre uma regra de firewall. |
> | Acção | Microsoft.DataLakeStore/accounts/firewallRules/write | Criar ou atualizar uma regra de firewall. |
> | Acção | Microsoft.DataLakeStore/accounts/operationResults/read | Obtenha o resultado de uma operação de conta do DataLakeStore. |
> | Acção | Microsoft.DataLakeStore/accounts/read | Obtenha informações sobre uma conta existente do DataLakeStore. |
> | Acção | Microsoft.DataLakeStore/accounts/Superuser/action | Conceder Superutilizador no Data Lake Store quando concedido com Microsoft.Authorization/roleAssignments/write. |
> | Acção | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Elimine um fornecedor de identidade fidedignos. |
> | Acção | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Obtenha informações sobre um fornecedor de identidade fidedignos. |
> | Acção | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Criar ou atualizar um fornecedor de identidade fidedignos. |
> | Acção | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Elimine uma regra de rede virtual. |
> | Acção | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Obtenha informações sobre uma regra de rede virtual. |
> | Acção | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Criar ou atualizar uma regra de rede virtual. |
> | Acção | Microsoft.DataLakeStore/accounts/write | Criar ou atualizar uma conta do DataLakeStore. |
> | Acção | Microsoft.DataLakeStore/locations/capability/read | Obtenha informações de capacidade de uma subscrição relativamente à utilização DataLakeStore. |
> | Acção | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Verifica a disponibilidade de um nome de conta do DataLakeStore. |
> | Acção | Microsoft.DataLakeStore/locations/operationResults/read | Obtenha o resultado de uma operação de conta do DataLakeStore. |
> | Acção | Microsoft.DataLakeStore/locations/usages/read | Obtenha informações de utilizações de quota de uma subscrição relativamente à utilização DataLakeStore. |
> | Acção | Microsoft.DataLakeStore/operations/read | Obter operações disponíveis do DataLakeStore. |
> | Acção | Microsoft.DataLakeStore/register/action | Registe a subscrição no DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.DataMigration/locations/operationResults/read | Obter o estado de uma operação prolongada relacionada com uma resposta Aceite 202 |
> | Acção | Microsoft.DataMigration/locations/operationStatuses/read | Obter o estado de uma operação prolongada relacionada com uma resposta Aceite 202 |
> | Acção | Microsoft.DataMigration/register/action | Regista a subscrição com o Azure Database Migration Service |
> | Acção | Microsoft.DataMigration/services/addWorker/action | Adicionar um trabalhador de DMS aos trabalhadores disponíveis do Serviço |
> | Acção | Microsoft.DataMigration/services/checkStatus/action | Verificar se o serviço está implementado e em execução |
> | Acção | Microsoft.DataMigration/services/configureWorker/action | Configura um trabalhador de DMS para os trabalhadores disponíveis do Serviço |
> | Acção | Microsoft.DataMigration/services/delete | Elimina um recurso e respetivos subordinados |
> | Acção | Microsoft.DataMigration/services/projects/accessArtifacts/action | Gerar um URL que possa ser utilizado para OBTER ou COLOCAR artefactos de projeto |
> | Acção | Microsoft.DataMigration/services/projects/delete | Elimina um recurso e respetivos subordinados |
> | Acção | Microsoft.DataMigration/services/projects/files/delete | Elimina um recurso e respetivos subordinados |
> | Acção | Microsoft.DataMigration/services/projects/files/read | Ler informações sobre recursos |
> | Acção | Microsoft.DataMigration/services/projects/files/read/action | Obtenha um URL que pode ser utilizado para ler o conteúdo do ficheiro |
> | Acção | Microsoft.DataMigration/services/projects/files/readWrite/action | Obtenha um URL que pode ser utilizado para ler ou escrever o conteúdo do ficheiro |
> | Acção | Microsoft.DataMigration/services/projects/files/write | Criar ou atualizar recursos e respetivas propriedades |
> | Acção | Microsoft.DataMigration/services/projects/read | Ler informações sobre recursos |
> | Acção | Microsoft.DataMigration/services/projects/tasks/cancel/action | Cancelar a tarefa se estiver atualmente em execução |
> | Acção | Microsoft.DataMigration/services/projects/tasks/delete | Elimina um recurso e respetivos subordinados |
> | Acção | Microsoft.DataMigration/services/projects/tasks/read | Ler informações sobre recursos |
> | Acção | Microsoft.DataMigration/services/projects/tasks/write | Executar tarefas das tarefas do Azure Database Migration Service |
> | Acção | Microsoft.DataMigration/services/projects/write | Executar tarefas das tarefas do Azure Database Migration Service |
> | Acção | Microsoft.DataMigration/services/read | Ler informações sobre recursos |
> | Acção | Microsoft.DataMigration/services/removeWorker/action | Remove um trabalhador de DMS dos trabalhadores disponíveis do Serviço |
> | Acção | Microsoft.DataMigration/services/serviceTasks/cancel/action | Cancelar a tarefa se estiver atualmente em execução |
> | Acção | Microsoft.DataMigration/services/serviceTasks/delete | Elimina um recurso e respetivos subordinados |
> | Acção | Microsoft.DataMigration/services/serviceTasks/read | Ler informações sobre recursos |
> | Acção | Microsoft.DataMigration/services/serviceTasks/write | Executar tarefas das tarefas do Azure Database Migration Service |
> | Acção | Microsoft.DataMigration/services/slots/delete | Elimina um recurso e respetivos subordinados |
> | Acção | Microsoft.DataMigration/services/slots/read | Ler informações sobre recursos |
> | Acção | Microsoft.DataMigration/services/slots/write | Criar ou atualizar recursos e respetivas propriedades |
> | Acção | Microsoft.DataMigration/services/start/action | Inicie o serviço DMS para permitir que o mesmo processe migrações novamente |
> | Acção | Microsoft.DataMigration/services/stop/action | Pare o serviço DMS para minimizar os respetivos custos |
> | Acção | Microsoft.DataMigration/services/updateAgentConfig/action | Atualiza a configuração do agente DMS com valores fornecidos. |
> | Acção | Microsoft.DataMigration/services/write | Criar ou atualizar recursos e respetivas propriedades |
> | Acção | Microsoft.DataMigration/skus/read | Obtenha uma lista de SKUs suportados por recursos DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Devolver resultados da operação de servidor MariaDB |
> | Acção | Microsoft.DBforMariaDB/locations/operationResults/read | Retorno ResourceGroup com base em resultados da operação de servidor MariaDB |
> | Acção | Microsoft.DBforMariaDB/locations/operationResults/read | Devolver resultados da operação de servidor MariaDB |
> | Acção | Microsoft.DBforMariaDB/locations/performanceTiers/read | Devolve a lista de escalões de desempenho disponíveis. |
> | Acção | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Devolve a lista de resultado de operação da deteção de ameaças de servidor. |
> | Acção | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Devolve a lista de resultado de operação da deteção de ameaças de servidor. |
> | Acção | Microsoft.DBforMariaDB/operations/read | Devolve a lista de operações de MariaDB. |
> | Acção | Microsoft.DBforMariaDB/performanceTiers/read | Devolve a lista de escalões de desempenho disponíveis. |
> | Acção | Microsoft.DBforMariaDB/register/action | Registar fornecedor de recursos de MariaDB |
> | Acção | Microsoft.DBforMariaDB/servers/configurations/read | Devolve a lista de configurações para um servidor ou obtém as propriedades para a configuração especificada. |
> | Acção | Microsoft.DBforMariaDB/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Acção | Microsoft.DBforMariaDB/servers/databases/delete | Elimina uma base de dados existente do MariaDB. |
> | Acção | Microsoft.DBforMariaDB/servers/databases/read | Devolve a lista de bases de dados de MariaDB ou obtém as propriedades da base de dados especificado. |
> | Acção | Microsoft.DBforMariaDB/servers/databases/write | Cria uma base de dados da MariaDB com os parâmetros especificados ou atualizar as propriedades para a base de dados especificada. |
> | Acção | Microsoft.DBforMariaDB/servers/delete | Elimina um servidor existente. |
> | Acção | Microsoft.DBforMariaDB/servers/firewallRules/delete | Elimina uma regra de firewall existente. |
> | Acção | Microsoft.DBforMariaDB/servers/firewallRules/read | Devolve a lista de firewall de regras para um servidor ou obtém as propriedades para a regra de firewall especificada. |
> | Acção | Microsoft.DBforMariaDB/servers/firewallRules/write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Acção | Microsoft.DBforMariaDB/servers/logFiles/read | Devolve a lista de MariaDB LogFiles. |
> | Acção | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição disagnostic para o recurso |
> | Acção | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Acção | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para os servidores de MariaDB |
> | Acção | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados |
> | Acção | Microsoft.DBforMariaDB/servers/queryTexts/action | Devolver os textos para obter uma lista de consultas |
> | Acção | Microsoft.DBforMariaDB/servers/read | Devolve a lista de servidores ou obtém as propriedades para o servidor especificado. |
> | Acção | Microsoft.DBforMariaDB/servers/recoverableServers/read | Devolver as informações de servidor MariaDB recuperáveis |
> | Acção | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Obter os detalhes da política de deteção de ameaças do servidor configurada num determinado servidor |
> | Acção | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Alterar a política de deteção de ameaças do servidor para um determinado servidor |
> | Acção | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Devolve a lista de estatísticas de consulta para as consultas principais. |
> | Acção | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Configurações de atualização para o servidor especificado |
> | Acção | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Elimina uma regra de rede Virtual existente |
> | Acção | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Devolve a lista de rede virtual de regras ou obtém as propriedades para a regra de rede virtual especificada. |
> | Acção | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a regra de rede virtual especificada. |
> | Acção | Microsoft.DBforMariaDB/servers/waitStatistics/read | Devolver as estatísticas de espera para uma instância |
> | Acção | Microsoft.DBforMariaDB/servers/write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o servidor especificado. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Devolver resultados da operação de servidor MySQL |
> | Acção | Microsoft.DBforMySQL/locations/operationResults/read | Retorno ResourceGroup com base em resultados da operação de servidor MySQL |
> | Acção | Microsoft.DBforMySQL/locations/operationResults/read | Devolver resultados da operação de servidor MySQL |
> | Acção | Microsoft.DBforMySQL/locations/performanceTiers/read | Devolve a lista de escalões de desempenho disponíveis. |
> | Acção | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Devolve a lista de resultado de operação da deteção de ameaças de servidor. |
> | Acção | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Devolve a lista de resultado de operação da deteção de ameaças de servidor. |
> | Acção | Microsoft.DBforMySQL/operations/read | Devolve a lista de operações do MySQL. |
> | Acção | Microsoft.DBforMySQL/performanceTiers/read | Devolve a lista de escalões de desempenho disponíveis. |
> | Acção | Microsoft.DBforMySQL/register/action | Registar fornecedor de recursos do MySQL |
> | Acção | Microsoft.DBforMySQL/servers/configurations/read | Devolve a lista de configurações para um servidor ou obtém as propriedades para a configuração especificada. |
> | Acção | Microsoft.DBforMySQL/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Acção | Microsoft.DBforMySQL/servers/databases/delete | Elimina uma base de dados MySQL. |
> | Acção | Microsoft.DBforMySQL/servers/databases/read | Devolve a lista de bases de dados MySQL ou obtém as propriedades da base de dados especificado. |
> | Acção | Microsoft.DBforMySQL/servers/databases/write | Cria uma base de dados MySQL com os parâmetros especificados ou atualizar as propriedades para a base de dados especificada. |
> | Acção | Microsoft.DBforMySQL/servers/delete | Elimina um servidor existente. |
> | Acção | Microsoft.DBforMySQL/servers/firewallRules/delete | Elimina uma regra de firewall existente. |
> | Acção | Microsoft.DBforMySQL/servers/firewallRules/read | Devolve a lista de firewall de regras para um servidor ou obtém as propriedades para a regra de firewall especificada. |
> | Acção | Microsoft.DBforMySQL/servers/firewallRules/write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Acção | Microsoft.DBforMySQL/servers/logFiles/read | Devolve a lista de PostgreSQL LogFiles. |
> | Acção | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição disagnostic para o recurso |
> | Acção | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Acção | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para servidores MySQL |
> | Acção | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados |
> | Acção | Microsoft.DBforMySQL/servers/queryTexts/action | Devolver os textos para obter uma lista de consultas |
> | Acção | Microsoft.DBforMySQL/servers/read | Devolve a lista de servidores ou obtém as propriedades para o servidor especificado. |
> | Acção | Microsoft.DBforMySQL/servers/recoverableServers/read | Devolver as informações do servidor MySQL recuperáveis |
> | Acção | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Obter os detalhes da política de deteção de ameaças do servidor configurada num determinado servidor |
> | Acção | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Alterar a política de deteção de ameaças do servidor para um determinado servidor |
> | Acção | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Devolve a lista de estatísticas de consulta para as consultas principais. |
> | Acção | Microsoft.DBforMySQL/servers/updateConfigurations/action | Configurações de atualização para o servidor especificado |
> | Acção | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Elimina uma regra de rede Virtual existente |
> | Acção | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Devolve a lista de rede virtual de regras ou obtém as propriedades para a regra de rede virtual especificada. |
> | Acção | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a regra de rede virtual especificada. |
> | Acção | Microsoft.DBforMySQL/servers/waitStatistics/read | Devolver as estatísticas de espera para uma instância |
> | Acção | Microsoft.DBforMySQL/servers/write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o servidor especificado. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Devolver resultados da operação de servidor PostgreSQL |
> | Acção | Microsoft.DBforPostgreSQL/locations/operationResults/read | Retorno ResourceGroup com base em resultados da operação de servidor PostgreSQL |
> | Acção | Microsoft.DBforPostgreSQL/locations/operationResults/read | Devolver resultados da operação de servidor PostgreSQL |
> | Acção | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Devolve a lista de escalões de desempenho disponíveis. |
> | Acção | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Devolve a lista de resultado de operação da deteção de ameaças de servidor. |
> | Acção | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Devolve a lista de resultado de operação da deteção de ameaças de servidor. |
> | Acção | Microsoft.DBforPostgreSQL/operations/read | Devolve a lista de operações do PostgreSQL. |
> | Acção | Microsoft.DBforPostgreSQL/performanceTiers/read | Devolve a lista de escalões de desempenho disponíveis. |
> | Acção | Microsoft.DBforPostgreSQL/register/action | Registar fornecedor de recursos do PostgreSQL |
> | Acção | Microsoft.DBforPostgreSQL/servers/advisors/read | Devolver a lista de consultores |
> | Acção | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Devolver a lista de ações recomendadas |
> | Acção | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Fazer recomendações |
> | Acção | Microsoft.DBforPostgreSQL/servers/configurations/read | Devolve a lista de configurações para um servidor ou obtém as propriedades para a configuração especificada. |
> | Acção | Microsoft.DBforPostgreSQL/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Acção | Microsoft.DBforPostgreSQL/servers/databases/delete | Elimina uma base de dados PostgreSQL. |
> | Acção | Microsoft.DBforPostgreSQL/servers/databases/read | Devolve a lista de bases de dados PostgreSQL ou obtém as propriedades da base de dados especificado. |
> | Acção | Microsoft.DBforPostgreSQL/servers/databases/write | Cria uma base de dados do PostgreSQL com os parâmetros especificados ou atualizar as propriedades para a base de dados especificada. |
> | Acção | Microsoft.DBforPostgreSQL/servers/delete | Elimina um servidor existente. |
> | Acção | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Elimina uma regra de firewall existente. |
> | Acção | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Devolve a lista de firewall de regras para um servidor ou obtém as propriedades para a regra de firewall especificada. |
> | Acção | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Acção | Microsoft.DBforPostgreSQL/servers/logFiles/read | Devolve a lista de PostgreSQL LogFiles. |
> | Acção | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição disagnostic para o recurso |
> | Acção | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Acção | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para os servidores de Postgres |
> | Acção | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados |
> | Acção | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Devolver o texto de uma consulta |
> | Acção | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Devolver os textos para obter uma lista de consultas |
> | Acção | Microsoft.DBforPostgreSQL/servers/read | Devolve a lista de servidores ou obtém as propriedades para o servidor especificado. |
> | Acção | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Devolver as informações do servidor PostgreSQL recuperáveis |
> | Acção | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Obter os detalhes da política de deteção de ameaças do servidor configurada num determinado servidor |
> | Acção | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Alterar a política de deteção de ameaças do servidor para um determinado servidor |
> | Acção | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Devolve a lista de estatísticas de consulta para as consultas principais. |
> | Acção | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Configurações de atualização para o servidor especificado |
> | Acção | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Elimina uma regra de rede Virtual existente |
> | Acção | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Devolve a lista de rede virtual de regras ou obtém as propriedades para a regra de rede virtual especificada. |
> | Acção | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a regra de rede virtual especificada. |
> | Acção | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Devolver as estatísticas de espera para uma instância |
> | Acção | Microsoft.DBforPostgreSQL/servers/write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o servidor especificado. |
> | Acção | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Devolve a lista de configurações para um servidor ou obtém as propriedades para a configuração especificada. |
> | Acção | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Atualize o valor para a configuração especificada |
> | Acção | Microsoft.DBforPostgreSQL/serversv2/delete | Elimina um servidor existente. |
> | Acção | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Elimina uma regra de firewall existente. |
> | Acção | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Devolve a lista de firewall de regras para um servidor ou obtém as propriedades para a regra de firewall especificada. |
> | Acção | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Acção | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição disagnostic para o recurso |
> | Acção | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Acção | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para os servidores de Postgres |
> | Acção | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados |
> | Acção | Microsoft.DBforPostgreSQL/serversv2/read | Devolve a lista de servidores ou obtém as propriedades para o servidor especificado. |
> | Acção | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Configurações de atualização para o servidor especificado |
> | Acção | Microsoft.DBforPostgreSQL/serversv2/write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o servidor especificado. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Devices/checkNameAvailability/Action | Verifique se IotHub name está disponível |
> | Acção | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Verifique o nome do serviço de aprovisionamento se está disponível |
> | Acção | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Acção | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Elimina o certificado |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Obtém o certificado |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Verifique se o recurso de certificado |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Criar ou atualizar o certificado |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Eliminar o recurso de inquilino do IotHub |
> | Acção | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Acção | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Eliminar grupo de consumidor de EventHub |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Obter grupo de consumidor de EventHub (s) |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Criar grupo de consumidores do EventHub |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Exportar dispositivos |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Obtém o inquilino IotHub recurso de estatísticas |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importar dispositivos |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Obtém a chave de inquilino do IotHub |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Obter as tarefas detalhes submetidas numa determinada IotHub |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Obtém as chaves de inquilino do IotHub |
> | Acção | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Obtém as definições de registo disponíveis para o serviço do IotHub |
> | Acção | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Obtém as métricas disponíveis para o serviço do IotHub |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Obter métricas de Quota |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/Read | Obtém o recurso de inquilino do IotHub |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Testar uma mensagem em relação a todas as rotas existentes |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testar uma mensagem em relação a uma rota de teste fornecido |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Obtém o estado de funcionamento de todos os pontos finais de encaminhamento para um IotHub |
> | Acção | Microsoft.Devices/elasticPools/iotHubTenants/Write | Criar ou atualizar o recurso de inquilino do IotHub |
> | Acção | Microsoft.Devices/ElasticPools/metricDefinitions/read | Obtém as métricas disponíveis para o serviço do IotHub |
> | Acção | Microsoft.Devices/iotHubs/certificates/Delete | Elimina o certificado |
> | Acção | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Acção | Microsoft.Devices/iotHubs/certificates/Read | Obtém o certificado |
> | Acção | Microsoft.Devices/iotHubs/certificates/verify/Action | Verifique se o recurso de certificado |
> | Acção | Microsoft.Devices/iotHubs/certificates/Write | Criar ou atualizar o certificado |
> | Acção | Microsoft.Devices/iotHubs/Delete | Eliminar recurso do IotHub |
> | Acção | Microsoft.Devices/IotHubs/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Acção | Microsoft.Devices/IotHubs/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Acção | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Elimina o filtro do Event Grid |
> | Acção | Microsoft.Devices/iotHubs/eventGridFilters/Read | Obtém o filtro do Event Grid |
> | Acção | Microsoft.Devices/iotHubs/eventGridFilters/Write | Criar um novo ou atualizar o filtro do Event Grid existente |
> | Acção | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Eliminar grupo de consumidor de EventHub |
> | Acção | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Obter grupo de consumidor de EventHub (s) |
> | Acção | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Criar grupo de consumidores do EventHub |
> | Acção | Microsoft.Devices/iotHubs/exportDevices/Action | Exportar dispositivos |
> | Acção | Microsoft.Devices/iotHubs/importDevices/Action | Importar dispositivos |
> | Acção | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Obtenha a chave de IotHub para o nome fornecido |
> | Acção | Microsoft.Devices/iotHubs/iotHubStats/Read | Obter estatísticas do IotHub |
> | Acção | Microsoft.Devices/iotHubs/jobs/Read | Obter as tarefas detalhes submetidas numa determinada IotHub |
> | Acção | Microsoft.Devices/iotHubs/listkeys/Action | Obter todas as chaves de IotHub |
> | Acção | Microsoft.Devices/IotHubs/logDefinitions/read | Obtém as definições de registo disponíveis para o serviço do IotHub |
> | Acção | Microsoft.Devices/IotHubs/metricDefinitions/read | Obtém as métricas disponíveis para o serviço do IotHub |
> | Acção | Microsoft.Devices/iotHubs/operationresults/Read | Obter Resultado da operação (obsoleto API) |
> | Acção | Microsoft.Devices/iotHubs/quotaMetrics/Read | Obter métricas de Quota |
> | Acção | Microsoft.Devices/iotHubs/Read | Obtém os recursos do IotHub |
> | Acção | Microsoft.Devices/iotHubs/routing/$testall/Action | Testar uma mensagem em relação a todas as rotas existentes |
> | Acção | Microsoft.Devices/iotHubs/routing/$testnew/Action | Testar uma mensagem em relação a uma rota de teste fornecido |
> | Acção | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Obtém o estado de funcionamento de todos os pontos finais de encaminhamento para um IotHub |
> | Acção | Microsoft.Devices/iotHubs/skus/Read | Obter Skus válidos do IotHub |
> | Acção | Microsoft.Devices/iotHubs/Write | Criar ou atualizar o recurso do IotHub |
> | Acção | Microsoft.Devices/locations/operationresults/Read | Obter Resultado da operação do Location based |
> | Acção | Microsoft.Devices/operationresults/Read | Obter Resultado da Operação |
> | Acção | Microsoft.Devices/operations/Read | Obter todas as operações de ResourceProvider |
> | Acção | Microsoft.Devices/provisioningServices/certificates/Delete | Elimina o certificado |
> | Acção | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Acção | Microsoft.Devices/provisioningServices/certificates/Read | Obtém o certificado |
> | Acção | Microsoft.Devices/provisioningServices/certificates/verify/Action | Verifique se o recurso de certificado |
> | Acção | Microsoft.Devices/provisioningServices/certificates/Write | Criar ou atualizar o certificado |
> | Acção | Microsoft.Devices/provisioningServices/Delete | Eliminar recurso IotDps |
> | Acção | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Acção | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Acção | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Obter chaves de IotDps para o nome da chave |
> | Acção | Microsoft.Devices/provisioningServices/listkeys/Action | Obter todas as chaves de IotDps |
> | Acção | Microsoft.Devices/provisioningServices/logDefinitions/read | Obtém as definições de registo disponíveis para o serviço de aprovisionamento |
> | Acção | Microsoft.Devices/provisioningServices/metricDefinitions/read | Obtém as métricas disponíveis para o serviço de aprovisionamento |
> | Acção | Microsoft.Devices/provisioningServices/operationresults/Read | Obter Resultado da operação de DPS |
> | Acção | Microsoft.Devices/provisioningServices/Read | Obter recurso de IotDps |
> | Acção | Microsoft.Devices/provisioningServices/skus/Read | Obter Skus válidos de IotDps |
> | Acção | Microsoft.Devices/provisioningServices/Write | Criar recurso IotDps |
> | Acção | Microsoft.Devices/register/action | Registre-se a subscrição para o fornecedor de recursos de IotHub e permite a criação de recursos do IotHub |
> | Acção | Microsoft.Devices/register/action | Registre-se a subscrição para o fornecedor de recursos de IotHub e permite a criação de recursos do IotHub |
> | Acção | Microsoft.Devices/usages/Read | Obter detalhes de utilização de subscrição para este fornecedor. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.DevSpaces/controllers/delete | Eliminar os serviços de controlador de espaços de desenvolvimento do Azure e o plano de dados |
> | Acção | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Lista detalhes de ligação para a infraestrutura do Azure Dev espaços do controlador |
> | Acção | Microsoft.DevSpaces/controllers/read | Propriedades de leitura do Azure Dev espaços controlador |
> | Acção | Microsoft.DevSpaces/controllers/write | Criar ou propriedades de atualização do Azure Dev espaços controlador |
> | Acção | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Verifique o mapeamento de controlador existente para um anfitrião de contentor |
> | Acção | Microsoft.DevSpaces/locations/operationresults/read | Estado de leitura para uma operação assíncrona |
> | Acção | Microsoft.DevSpaces/register/action | Registar o fornecedor de recursos de espaços de desenvolvimento da Microsoft com uma subscrição |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.DevTestLab/labCenters/delete | Elimine centros do laboratório. |
> | Acção | Microsoft.DevTestLab/labCenters/read | Centros do laboratório de leitura. |
> | Acção | Microsoft.DevTestLab/labCenters/write | Adiciona ou modifica centros do laboratório. |
> | Acção | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Leia modelos do Gestor de recursos do azure. |
> | Acção | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Gera um modelo ARM para o artefacto de determinado, carrega os ficheiros necessários para uma conta de armazenamento e valida o artefacto gerado. |
> | Acção | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Ler artefactos. |
> | Acção | Microsoft.DevTestLab/labs/artifactSources/delete | Elimine origens de artefactos. |
> | Acção | Microsoft.DevTestLab/labs/artifactSources/read | Leia as origens de artefactos. |
> | Acção | Microsoft.DevTestLab/labs/artifactSources/write | Adiciona ou modifica as origens de artefactos. |
> | Acção | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Declarar uma máquina de virtual reclamáveis aleatória no laboratório. |
> | Acção | Microsoft.DevTestLab/labs/costs/read | Leia os custos. |
> | Acção | Microsoft.DevTestLab/labs/costs/write | Adiciona ou modifica os custos. |
> | Acção | Microsoft.DevTestLab/labs/CreateEnvironment/action | Crie máquinas virtuais num laboratório. |
> | Acção | Microsoft.DevTestLab/labs/customImages/delete | Elimine imagens personalizadas. |
> | Acção | Microsoft.DevTestLab/labs/customImages/read | Leia as imagens personalizadas. |
> | Acção | Microsoft.DevTestLab/labs/customImages/write | Adiciona ou modifica as imagens personalizadas. |
> | Acção | Microsoft.DevTestLab/labs/delete | Elimine laboratórios. |
> | Acção | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Certifique-se de que o utilizador atual tem um perfil válido no laboratório. |
> | Acção | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exporta a utilização de recursos de laboratório para uma conta de armazenamento |
> | Acção | Microsoft.DevTestLab/labs/formulas/delete | Elimine as fórmulas. |
> | Acção | Microsoft.DevTestLab/labs/formulas/read | Leia fórmulas. |
> | Acção | Microsoft.DevTestLab/labs/formulas/write | Adiciona ou modifica as fórmulas. |
> | Acção | Microsoft.DevTestLab/labs/galleryImages/read | Leia as imagens da galeria. |
> | Acção | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Gere um URI para carregar as imagens de disco personalizada a um laboratório. |
> | Acção | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importe uma máquina virtual para um laboratório diferente. |
> | Acção | Microsoft.DevTestLab/labs/ListVhds/action | Listar imagens de disco disponível para criação de imagem personalizada. |
> | Acção | Microsoft.DevTestLab/labs/notificationChannels/delete | Elimine notificationchannels. |
> | Acção | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Envie notificação de canal fornecida. |
> | Acção | Microsoft.DevTestLab/labs/notificationChannels/read | Leia notificationchannels. |
> | Acção | Microsoft.DevTestLab/labs/notificationChannels/write | Adiciona ou modifica notificationchannels. |
> | Acção | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Avalia a política de laboratório. |
> | Acção | Microsoft.DevTestLab/labs/policySets/policies/delete | Elimine políticas. |
> | Acção | Microsoft.DevTestLab/labs/policySets/policies/read | Leia as políticas. |
> | Acção | Microsoft.DevTestLab/labs/policySets/policies/write | Adicionar ou modificar as políticas. |
> | Acção | Microsoft.DevTestLab/labs/policySets/read | Leia os conjuntos de políticas. |
> | Acção | Microsoft.DevTestLab/labs/read | Laboratórios de leitura. |
> | Acção | Microsoft.DevTestLab/labs/schedules/delete | Elimine agendas. |
> | Acção | Microsoft.DevTestLab/labs/schedules/Execute/action | Execute uma agenda. |
> | Acção | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Apresenta uma lista de todas as agendas aplicável |
> | Acção | Microsoft.DevTestLab/labs/schedules/read | Agenda de leitura. |
> | Acção | Microsoft.DevTestLab/labs/schedules/write | Adicione ou modifique as Programações. |
> | Acção | Microsoft.DevTestLab/labs/serviceRunners/delete | Elimine concorrentes de serviço. |
> | Acção | Microsoft.DevTestLab/labs/serviceRunners/read | Leia concorrentes de serviço. |
> | Acção | Microsoft.DevTestLab/labs/serviceRunners/write | Adiciona ou modifica concorrentes de serviço. |
> | Acção | Microsoft.DevTestLab/labs/sharedGalleries/delete | Elimine galerias partilhadas. |
> | Acção | Microsoft.DevTestLab/labs/sharedGalleries/read | Leia galerias partilhadas. |
> | Acção | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Elimine Imagens partilhadas. |
> | Acção | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Leia Imagens partilhadas. |
> | Acção | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Adicionar ou modificar imagens partilhadas. |
> | Acção | Microsoft.DevTestLab/labs/sharedGalleries/write | Adiciona ou modifica as galerias partilhadas. |
> | Acção | Microsoft.DevTestLab/labs/users/delete | Elimine perfis de utilizador. |
> | Acção | Microsoft.DevTestLab/labs/users/disks/Attach/action | Anexar e criar a concessão do disco para a máquina virtual. |
> | Acção | Microsoft.DevTestLab/labs/users/disks/delete | Elimine discos. |
> | Acção | Microsoft.DevTestLab/labs/users/disks/Detach/action | Desanexar e interromper a concessão do disco anexado à máquina virtual. |
> | Acção | Microsoft.DevTestLab/labs/users/disks/read | Leitura de discos. |
> | Acção | Microsoft.DevTestLab/labs/users/disks/write | Adiciona ou modifica os discos. |
> | Acção | Microsoft.DevTestLab/labs/users/environments/delete | Elimine ambientes. |
> | Acção | Microsoft.DevTestLab/labs/users/environments/read | Ambientes de leitura. |
> | Acção | Microsoft.DevTestLab/labs/users/environments/write | Adiciona ou modifica os ambientes. |
> | Acção | Microsoft.DevTestLab/labs/users/read | Ler perfis de utilizador. |
> | Acção | Microsoft.DevTestLab/labs/users/secrets/delete | Elimine segredos. |
> | Acção | Microsoft.DevTestLab/labs/users/secrets/read | Leia os segredos. |
> | Acção | Microsoft.DevTestLab/labs/users/secrets/write | Adiciona ou modifica os segredos. |
> | Acção | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Elimine recursos de infraestrutura do serviço. |
> | Acção | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Lista as agendas de iniciar/parar aplicável, se aplicável. |
> | Acção | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Leia os recursos de infraestrutura do serviço. |
> | Acção | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Elimine agendas. |
> | Acção | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Execute uma agenda. |
> | Acção | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Agenda de leitura. |
> | Acção | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Adicione ou modifique as Programações. |
> | Acção | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Inicie o service fabric. |
> | Acção | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Zastaví service fabric. |
> | Acção | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Adiciona ou modifica os recursos de infraestrutura do serviço. |
> | Acção | Microsoft.DevTestLab/labs/users/write | Adicionar ou modificar perfis de utilizador. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Anexe um disco de dados nova ou existente à máquina virtual. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Aplicam-se de artefactos a máquina virtual. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Obter a propriedade de uma máquina virtual existente |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/delete | Elimine máquinas virtuais. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Desanexe o disco especificado da máquina virtual. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Obtém uma cadeia de caracteres que representa o conteúdo do ficheiro RDP para a máquina virtual |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Lista as agendas de iniciar/parar aplicável, se aplicável. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/read | Leia as máquinas virtuais. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Voltar a implementar uma máquina virtual |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Redimensione a Máquina Virtual. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Reinicie uma máquina virtual. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Elimine agendas. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Execute uma agenda. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Agenda de leitura. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Adicione ou modifique as Programações. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Inicie uma máquina virtual. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Parar uma máquina virtual |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Transfere todos os discos de dados ligados à máquina virtual que é detido pelo usuário atual. |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Propriedade de lançamento de uma máquina virtual existente |
> | Acção | Microsoft.DevTestLab/labs/virtualMachines/write | Adiciona ou modifica máquinas virtuais. |
> | Acção | Microsoft.DevTestLab/labs/virtualNetworks/delete | Elimine redes virtuais. |
> | Acção | Microsoft.DevTestLab/labs/virtualNetworks/read | Leia as redes virtuais. |
> | Acção | Microsoft.DevTestLab/labs/virtualNetworks/write | Adiciona ou modifica as redes virtuais. |
> | Acção | Microsoft.DevTestLab/labs/vmPools/delete | Elimine agrupamentos de máquinas virtuais. |
> | Acção | Microsoft.DevTestLab/labs/vmPools/read | Leia os agrupamentos de máquinas virtuais. |
> | Acção | Microsoft.DevTestLab/labs/vmPools/write | Adicionar ou modificar agrupamentos de máquinas virtuais. |
> | Acção | Microsoft.DevTestLab/labs/write | Adicionar ou modificar laboratórios. |
> | Acção | Microsoft.DevTestLab/locations/operations/read | Operações de leitura. |
> | Acção | Microsoft.DevTestLab/register/action | Regista a subscrição |
> | Acção | Microsoft.DevTestLab/schedules/delete | Elimine agendas. |
> | Acção | Microsoft.DevTestLab/schedules/Execute/action | Execute uma agenda. |
> | Acção | Microsoft.DevTestLab/schedules/read | Agenda de leitura. |
> | Acção | Microsoft.DevTestLab/schedules/Retarget/action | ID de recurso de destino de uma agenda de atualizações |
> | Acção | Microsoft.DevTestLab/schedules/write | Adicione ou modifique as Programações. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.DocumentDB/databaseAccountNames/read | Verifica a existência de disponibilidade do nome. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | Elimine uma coleção. Apenas aplicável a tipos de API: 'mongodb'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | Ler uma coleção ou lista de todas as coleções. Apenas aplicável a tipos de API: 'mongodb'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/throughput/read | Leia um débito de coleção. Apenas aplicável a tipos de API: 'mongodb'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/throughput/write | Atualize um débito de coleção. Apenas aplicável a tipos de API: 'mongodb'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | Criar ou atualizar uma coleção. Apenas aplicável a tipos de API: 'mongodb'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | Elimine um contentor. Apenas aplicável a tipos de API: 'sql'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | Ler um contentor ou listar todos os contentores. Apenas aplicável a tipos de API: 'sql'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/throughput/read | Leia um débito de contentores. Apenas aplicável a tipos de API: 'sql'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/throughput/write | Atualize um débito de contentores. Apenas aplicável a tipos de API: 'sql'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | Criar ou atualizar um contentor. Apenas aplicável a tipos de API: 'sql'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | Elimine uma base de dados. Apenas aplicável a tipos de API: 'sql', 'mongodb', 'gremlin'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Elimine um gráfico. Apenas aplicável a tipos de API: 'gremlin'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Ler um gráfico ou lista de todos os gráficos. Apenas aplicável a tipos de API: 'gremlin'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/throughput/read | Leia um débito de gráfico. Apenas aplicável a tipos de API: 'gremlin'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/throughput/write | Atualize um débito de gráfico. Apenas aplicável a tipos de API: 'gremlin'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Criar ou atualizar um gráfico. Apenas aplicável a tipos de API: 'gremlin'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Ler um banco de dados ou liste todas as bases de dados. Apenas aplicável a tipos de API: 'sql', 'mongodb', 'gremlin'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/throughput/read | Leia um débito de base de dados. Apenas aplicável a tipos de API: 'sql', 'mongodb', 'gremlin'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/throughput/write | Atualize um débito de base de dados. Apenas aplicável a tipos de API: 'sql', 'mongodb', 'gremlin'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Crie uma base de dados. Apenas aplicável a tipos de API: 'sql', 'mongodb', 'gremlin'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Elimine um keyspace. Apenas aplicável a tipos de API: 'cassandra'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | Ler um keyspace ou lista de todos os keyspaces. Apenas aplicável a tipos de API: 'cassandra'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/throughput/read | Leia um débito de keyspace. Apenas aplicável a tipos de API: 'cassandra'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/throughput/write | Atualize um débito de keyspace. Apenas aplicável a tipos de API: 'cassandra'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Elimine uma tabela. Apenas aplicável a tipos de API: 'cassandra'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Ler uma tabela ou lista de todas as tabelas. Apenas aplicável a tipos de API: 'cassandra'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/throughput/read | Leia um débito de tabela. Apenas aplicável a tipos de API: 'cassandra'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/throughput/write | Atualize um débito de tabela. Apenas aplicável a tipos de API: 'cassandra'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Criar ou atualizar uma tabela. Apenas aplicável a tipos de API: 'cassandra'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Crie um keyspace. Apenas aplicável a tipos de API: 'cassandra'. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Elimine uma tabela. Apenas aplicável a tipos de API: "tabela". |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Ler uma tabela ou lista de todas as tabelas. Apenas aplicável a tipos de API: "tabela". |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/throughput/read | Leia um débito de tabela. Apenas aplicável a tipos de API: "tabela". |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/throughput/write | Atualize um débito de tabela. Apenas aplicável a tipos de API: "tabela". |
> | Acção | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Criar ou atualizar uma tabela. Apenas aplicável a tipos de API: "tabela". |
> | Acção | Microsoft.DocumentDB/databaseAccounts/backup/action | Submeter um pedido para configurar a cópia de segurança |
> | Acção | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Alterar o grupo de recursos de uma conta de base de dados |
> | Acção | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Lê a coleção de definições de métricas. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Lê as métricas de coleção. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Ler a base de dados conta partição nível métricas-chave |
> | Acção | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Ler métricas ao nível da base de dados conta partição |
> | Acção | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Leia as partições de conta de base de dados de uma coleção |
> | Acção | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Ler conta da base de dados utilizações de nível de partição |
> | Acção | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Lê as utilizações de coleção. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Lê as definições de métricas de base de dados |
> | Acção | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Lê as métricas de base de dados. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Lê as utilizações de base de dados. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/delete | Elimina as contas de base de dados. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Alterar as prioridades de ativação pós-falha de regiões de uma conta de base de dados. Isto é utilizado para efetuar a operação de ativação pós-falha manual |
> | Acção | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Obter as cadeias de ligação para uma conta de base de dados |
> | Acção | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Listar chaves de uma conta de base de dados |
> | Acção | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Lê a base de dados de definições de métricas da conta. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/metrics/read | Lê as métricas da conta de base de dados. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Offline uma região de uma conta de base de dados.  |
> | Acção | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Online uma região de uma conta de base de dados. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Ler o status da operação assíncrona |
> | Acção | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Métricas de latência de leitura |
> | Acção | Microsoft.DocumentDB/databaseAccounts/percentile/read | Ler percentis das latências de replicação |
> | Acção | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Ler métricas de latência para uma região de origem e de destino específica |
> | Acção | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Métricas de latência de leitura para uma região de destino específico |
> | Acção | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Eliminar um proxy de ligação de ponto final privado da conta de base de dados |
> | Acção | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/read | Ler o Status de privateEndpointConnectionProxies operação assíncrona |
> | Acção | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Ler um proxy de ligação de ponto final privado da conta de base de dados |
> | Acção | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Validar um proxy de ligação de ponto final privado da conta de base de dados |
> | Acção | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Criar ou atualizar um proxy de ligação de ponto final privado da conta de base de dados |
> | Acção | Microsoft.DocumentDB/databaseAccounts/read | Lê uma conta de base de dados. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Lê a base de dados de chaves da conta só de leitura. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Lê a base de dados de chaves da conta só de leitura. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Rodar chaves de uma conta de base de dados |
> | Acção | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Lê as métricas de coleção regional. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Ler a base de dados regionais conta partição nível métricas-chave |
> | Acção | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Ler métricas ao nível da base de dados regionais conta partição |
> | Acção | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Leia as partições de conta de base de dados regionais numa coleção |
> | Acção | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Lê as métricas da conta de base de dados e região. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/restore/action | Submeter um pedido de restauro |
> | Acção | Microsoft.DocumentDB/databaseAccounts/usages/read | Lê a base de dados utilizações de conta. |
> | Acção | Microsoft.DocumentDB/databaseAccounts/write | Atualize um contas de base de dados. |
> | Acção | Microsoft.DocumentDB/locations/asyncoperations/read | Lê o estado das operações assíncronas |
> | Acção | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Notifica Microsoft.DocumentDB que VirtualNetwork ou sub-rede está a ser eliminada |
> | Acção | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Ler o Status de deleteVirtualNetworkOrSubnets operação assíncrona |
> | Acção | Microsoft.DocumentDB/operationResults/read | Ler o status da operação assíncrona |
> | Acção | Microsoft.DocumentDB/operations/read | Ler operações disponíveis para a Microsoft DocumentDB  |
> | Acção | Microsoft.DocumentDB/register/action |  Registar o fornecedor de recursos do Microsoft DocumentDB para a subscrição |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.DomainRegistration/checkDomainAvailability/Action | Verifique se um domínio está disponível para compra |
> | Acção | Microsoft.DomainRegistration/domains/Delete | Elimine um domínio existente. |
> | Acção | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Eliminar o identificador de propriedade |
> | Acção | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Identificadores de propriedade de lista |
> | Acção | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Obter o identificador de propriedade |
> | Acção | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Criar ou atualizar o identificador |
> | Acção | Microsoft.DomainRegistration/domains/operationresults/Read | Operação de obtenção de um domínio |
> | Acção | Microsoft.DomainRegistration/domains/Read | Obter a lista de domínios |
> | Acção | Microsoft.DomainRegistration/domains/Read | Obter o domínio |
> | Acção | Microsoft.DomainRegistration/domains/renew/Action | Renove um domínio existente. |
> | Acção | Microsoft.DomainRegistration/domains/Write | Adicionar um novo domínio ou atualizar um já existente |
> | Acção | Microsoft.DomainRegistration/generateSsoRequest/Action | Gere um pedido para iniciar sessão no Centro de controlo de domínio. |
> | Acção | Microsoft.DomainRegistration/listDomainRecommendations/Action | Obter as recomendações de domínio de lista com base em palavras-chave |
> | Acção | Microsoft.DomainRegistration/operations/Read | Listar todas as operações de registo de domínio do serviço de aplicações |
> | Acção | Microsoft.DomainRegistration/register/action | Registar o fornecedor de recursos do Microsoft Domains para a subscrição |
> | Acção | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Ação de contrato de lista |
> | Acção | Microsoft.DomainRegistration/topLevelDomains/Read | Obter toplevel domínios |
> | Acção | Microsoft.DomainRegistration/topLevelDomains/Read | Obter toplevel domínio |
> | Acção | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Validar o objeto de compra de domínio sem enviá-lo |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.EventGrid/domains/delete | Eliminar um domínio |
> | Acção | Microsoft.EventGrid/domains/listKeys/action | Listar chaves para um domínio |
> | Acção | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para os domínios |
> | Acção | Microsoft.EventGrid/domains/read | Ler um domínio |
> | Acção | Microsoft.EventGrid/domains/regenerateKey/action | Regenerar chave para um domínio |
> | Acção | Microsoft.EventGrid/domains/topics/read | Leia um tópico de domínio |
> | Acção | Microsoft.EventGrid/domains/write | Criar ou atualizar um domínio |
> | Acção | Microsoft.EventGrid/eventSubscriptions/delete | Eliminar um eventSubscription |
> | Acção | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Obter o url completo para a subscrição de evento |
> | Acção | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico para subscrições de eventos |
> | Acção | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição de diagnóstico para subscrições de eventos |
> | Acção | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para eventSubscriptions |
> | Acção | Microsoft.EventGrid/eventSubscriptions/read | Ler um eventSubscription |
> | Acção | Microsoft.EventGrid/eventSubscriptions/write | Criar ou atualizar um eventSubscription |
> | Acção | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico para tópicos |
> | Acção | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição de diagnóstico para os tópicos |
> | Acção | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para os tópicos |
> | Acção | Microsoft.EventGrid/extensionTopics/read | Leia um extensionTopic. |
> | Acção | Microsoft.EventGrid/locations/eventSubscriptions/read | Subscrições de eventos regionais de lista |
> | Acção | Microsoft.EventGrid/locations/operationResults/read | Leia o resultado de uma operação regional |
> | Acção | Microsoft.EventGrid/locations/operationsStatus/read | Ler o estado de uma operação regional |
> | Acção | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Subscrições de eventos regionais de lista por topictype |
> | Acção | Microsoft.EventGrid/operationResults/read | O resultado de uma operação de leitura |
> | Acção | Microsoft.EventGrid/operations/read | EventGrid operações de lista. |
> | Acção | Microsoft.EventGrid/operationsStatus/read | O estado de uma operação de leitura |
> | Acção | Microsoft.EventGrid/register/action | Regista a subscrição para o fornecedor de recursos de EventGrid. |
> | Acção | Microsoft.EventGrid/topics/delete | Eliminar um tópico |
> | Acção | Microsoft.EventGrid/topics/listKeys/action | Listar chaves para um tópico |
> | Acção | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico para tópicos |
> | Acção | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição de diagnóstico para os tópicos |
> | Acção | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para os tópicos |
> | Acção | Microsoft.EventGrid/topics/read | Leia um tópico |
> | Acção | Microsoft.EventGrid/topics/regenerateKey/action | Regenerar chave para um tópico |
> | Acção | Microsoft.EventGrid/topics/write | Criar ou atualizar um tópico |
> | Acção | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Lista de subscrições de eventos global por tipo de tópico |
> | Acção | Microsoft.EventGrid/topictypes/eventtypes/read | Ler eventtypes suportado por um topictype |
> | Acção | Microsoft.EventGrid/topictypes/read | Ler um topictype |
> | Acção | Microsoft.EventGrid/unregister/action | Anula o registo da subscrição para o fornecedor de recursos de EventGrid. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.EventHub/checkNameAvailability/action | Verifica a disponibilidade do espaço de nomes em determinada subscrição. |
> | Acção | Microsoft.EventHub/checkNamespaceAvailability/action | Verifica a disponibilidade do espaço de nomes em determinada subscrição. Esta API foi preterida utilize CheckNameAvailability em vez disso. |
> | Acção | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obter a lista de descrições de recursos do Cluster métricas |
> | Acção | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Elimina as regras de VNet no Fornecedor de Recursos de EventHub para a VNet especificada |
> | Acção | Microsoft.EventHub/namespaces/authorizationRules/action | Regra de autorização de espaço de nomes de atualizações. Esta API foi preterida. Utilize uma chamada PUT para atualizar a regra de autorização de espaço de nomes em vez disso,.... Esta operação não é suportada na versão 2017-04-01 de API. |
> | Acção | Microsoft.EventHub/namespaces/authorizationRules/delete | Elimine regra de autorização de espaço de nomes. Não é possível eliminar a regra de autorização de espaço de nomes predefinido.  |
> | Acção | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Obter Cadeia de Ligação para o Espaço de nomes |
> | Acção | Microsoft.EventHub/namespaces/authorizationRules/read | Obter a lista de descrição das Regras de Autorização de Espaços de nomes. |
> | Acção | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Acção | Microsoft.EventHub/namespaces/authorizationRules/write | Criar regras de autorização ao nível de espaço de nomes e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o principal e as chaves secundárias podem ser atualizadas. |
> | Acção | Microsoft.EventHub/namespaces/Delete | Eliminar Recurso de Espaço de nomes |
> | Acção | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtém as chaves das regras de autorização do espaço de nomes principal da Recuperação Após Desastre |
> | Acção | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter as Regras de Autorização do Espaço de Nomes Principal da Recuperação Após Desastre |
> | Acção | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Desativa a Recuperação Após Desastre e para a replicação de alterações de espaço de nomes principais para secundários. |
> | Acção | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica a disponibilidade do alias do espaço de nomes numa determinada subscrição. |
> | Acção | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Elimina a configuração de recuperação após desastre associada no espaço de nomes. Esta operação só pode ser chamada por meio do espaço de nomes primário. |
> | Acção | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Invoca uma ativação pós-falha GEO DR e reconfigura o alias de espaço de nomes para apontar para o espaço de nomes secundário. |
> | Acção | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Obtém a configuração da Recuperação Após Desastre associada ao espaço de nomes. |
> | Acção | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Cria ou Atualiza a configuração da Recuperação Após Desastre associada ao espaço de nomes. |
> | Acção | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Operação para atualizar o EventHub. Esta operação não é suportada na versão 2017-04-01 de API. Regras de autorização. Utilize uma chamada PUT para atualizar a regra de autorização. |
> | Acção | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Operação para eliminar regras de autorização de EventHub |
> | Acção | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Obter cadeia de ligação de EventHub |
> | Acção | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Obter a lista de regras de autorização de EventHub |
> | Acção | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Acção | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Criar regras de autorização de EventHub e atualizar as respetivas propriedades. Os direitos de acesso de regras de autorização podem ser atualizados. |
> | Acção | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operação para eliminar o recurso de ConsumerGroup |
> | Acção | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Obter lista de descrições de recursos de ConsumerGroup |
> | Acção | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Criar ou propriedades de ConsumerGroup de atualização. |
> | Acção | Microsoft.EventHub/namespaces/eventhubs/Delete | Operação para eliminar os recursos do EventHub |
> | Acção | Microsoft.EventHub/namespaces/eventhubs/read | Obter lista de descrições de recursos do EventHub |
> | Acção | Microsoft.EventHub/namespaces/eventhubs/write | Criar ou propriedades de EventHub de atualização. |
> | Acção | Microsoft.EventHub/namespaces/ipFilterRules/delete | Eliminar Recurso de Filtro de IP |
> | Acção | Microsoft.EventHub/namespaces/ipFilterRules/read | Obter o Recurso de Filtro de IP |
> | Acção | Microsoft.EventHub/namespaces/ipFilterRules/write | Criar Recurso de Filtro de IP |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | Receber mensagens |
> | DataAction | Microsoft.EventHub/namespaces/messages/send/action | Enviar mensagens |
> | Acção | Microsoft.EventHub/namespaces/messagingPlan/read | Obtém o plano de mensagens para um espaço de nomes.<br>Esta API foi preterida.<br>As propriedades expostas por intermédio do recurso MessagingPlan são movidas para o recurso de espaço de nomes em versões de API posteriores do (principal)....<br>Esta operação não é suportada na versão 2017-04-01 de API. |
> | Acção | Microsoft.EventHub/namespaces/messagingPlan/write | Atualiza o plano de mensagens para um espaço de nomes.<br>Esta API foi preterida.<br>As propriedades expostas por intermédio do recurso MessagingPlan são movidas para o recurso de espaço de nomes em versões de API posteriores do (principal)....<br>Esta operação não é suportada na versão 2017-04-01 de API. |
> | Acção | Microsoft.EventHub/namespaces/networkrulesets/delete | Eliminar Recurso de Regra de VNET |
> | Acção | Microsoft.EventHub/namespaces/networkrulesets/read | Obtém o recurso de NetworkRuleSet |
> | Acção | Microsoft.EventHub/namespaces/networkrulesets/write | Criar Recurso de Regra de VNET |
> | Acção | Microsoft.EventHub/namespaces/operationresults/read | Obter o estado da operação de Espaço de nomes |
> | Acção | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obter lista de descrições de recursos do espaço de nomes das definições de diagnóstico |
> | Acção | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obter lista de descrições de recursos do espaço de nomes das definições de diagnóstico |
> | Acção | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obter lista de registos de espaço de nomes descrições de recursos |
> | Acção | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de descrições de recursos do espaço de nomes métricas |
> | Acção | Microsoft.EventHub/namespaces/read | Obter a lista de Descrição de Recursos de Espaço de nomes |
> | Acção | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Remova o espaço de nomes do ACS |
> | Acção | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Eliminar Recurso de Regra de VNET |
> | Acção | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Obtém o Recurso de Regra de VNET |
> | Acção | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Criar Recurso de Regra de VNET |
> | Acção | Microsoft.EventHub/namespaces/write | Criar um recurso de espaço de nomes e Atualize as respetivas propriedades. As etiquetas e a capacidade do espaço de nomes são propriedades que podem ser atualizadas. |
> | Acção | Microsoft.EventHub/operations/read | Obter Operações |
> | Acção | Microsoft.EventHub/register/action | Regista a subscrição do fornecedor de recursos do EventHub e ativa a criação de recursos do EventHub |
> | Acção | Microsoft.EventHub/sku/read | Obter lista de descrições de recursos de Sku |
> | Acção | Microsoft.EventHub/sku/regions/read | Obter lista de descrições de recursos de SkuRegions |
> | Acção | Microsoft.EventHub/unregister/action | Regista o Fornecedor de Recursos do EventHub |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Features/features/read | Obtém as funcionalidades de uma subscrição. |
> | Acção | Microsoft.Features/operations/read | Obtém a lista de operações. |
> | Acção | Microsoft.Features/providers/features/read | Obtém a funcionalidade de uma subscrição de um fornecedor de recursos específico. |
> | Acção | Microsoft.Features/providers/features/register/action | Regista a funcionalidade para uma subscrição de um fornecedor de recursos específico. |
> | Acção | Microsoft.Features/providers/features/unregister/action | Anula o registo da funcionalidade para uma subscrição de um fornecedor de recursos específico. |
> | Acção | Microsoft.Features/register/action | Regista a funcionalidade de uma subscrição. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | A atribuição de configuração de convidado de GET. |
> | Acção | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Relatório de atribuição de configuração de convidado de GET. |
> | Acção | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Crie nova atribuição de configuração de convidado. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.HDInsight/clusters/applications/delete | Eliminar a aplicação para o Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/applications/read | Obter a aplicação para o Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/applications/write | Criar ou atualizar a aplicação para o Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/changerdpsetting/action | Alterar a definição de RDP para o Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/configurations/action | Atualizar a configuração de Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/configurations/action | Obter configurações de Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/configurations/read | Obter configurações de Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/delete | Eliminar um Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/getGatewaySettings/action | Obter as definições do gateway para o Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico para o recurso de Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição de diagnóstico para o recurso de Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para o Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/read | Obter detalhes sobre o Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/roles/resize/action | Redimensionar um Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Atualizar as definições do gateway para o Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/clusters/write | Criar ou atualizar o Cluster do HDInsight |
> | Acção | Microsoft.HDInsight/locations/capabilities/read | Obter capacidades de subscrição |
> | Acção | Microsoft.HDInsight/locations/checkNameAvailability/read | Verificar Disponibilidade do Nome |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ImportExport/jobs/delete | Elimina uma tarefa existente. |
> | Acção | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Obtém as chaves do BitLocker para o trabalho especificado. |
> | Acção | Microsoft.ImportExport/jobs/read | Obtém as propriedades para o trabalho especificado ou devolve a lista de tarefas. |
> | Acção | Microsoft.ImportExport/jobs/write | Cria uma tarefa com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o trabalho especificado. |
> | Acção | Microsoft.ImportExport/locations/read | Obtém as propriedades para a localização especificada ou devolve a lista de localizações. |
> | Acção | Microsoft.ImportExport/register/action | Regista a subscrição para o fornecedor de recursos de importação/exportação e permite a criação de tarefas de importação/exportação. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Insights/ActionGroups/Delete | Eliminar um grupo de ações |
> | Acção | Microsoft.Insights/ActionGroups/Read | Ler um grupo de ações |
> | Acção | Microsoft.Insights/ActionGroups/Write | Criar ou atualizar um grupo de ações |
> | Acção | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Alerta do Registo de Atividade ativado |
> | Acção | Microsoft.Insights/ActivityLogAlerts/Delete | Eliminar um alerta de registo de atividade |
> | Acção | Microsoft.Insights/ActivityLogAlerts/Read | Ler um alerta do registo de atividade |
> | Acção | Microsoft.Insights/ActivityLogAlerts/Write | Criar ou atualizar um alerta de registo de atividade |
> | Acção | Microsoft.Insights/AlertRules/Activated/Action | Alerta de métrica clássica ativado |
> | Acção | Microsoft.Insights/AlertRules/Delete | Eliminar um alerta de métrica clássico |
> | Acção | Microsoft.Insights/AlertRules/Incidents/Read | Ler um incidente de alerta de métrica clássica |
> | Acção | Microsoft.Insights/AlertRules/Read | Ler um alerta de métrica clássica |
> | Acção | Microsoft.Insights/AlertRules/Resolved/Action | Alerta de métrica clássica resolvido |
> | Acção | Microsoft.Insights/AlertRules/Throttled/Action | Regra de alerta de métrica clássica limitada |
> | Acção | Microsoft.Insights/AlertRules/Write | Criar ou atualizar um alerta de métrica clássico |
> | Acção | Microsoft.Insights/AutoscaleSettings/Delete | Eliminar uma definição de dimensionamento automático |
> | Acção | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Ler uma definição de diagnóstico do recurso |
> | Acção | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Criar ou atualizar uma definição de diagnóstico de recurso |
> | Acção | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Ler definições de registo |
> | Acção | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Ler definições de métricas |
> | Acção | Microsoft.Insights/AutoscaleSettings/Read | Ler uma definição de dimensionamento automático |
> | Acção | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Redução do Dimensionamento Automático iniciada |
> | Acção | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Redução do Dimensionamento Automático concluída |
> | Acção | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Aumento do dimensionamento automático iniciado |
> | Acção | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Aumento do dimensionamento automático concluído |
> | Acção | Microsoft.Insights/AutoscaleSettings/Write | Criar ou atualizar uma definição de dimensionamento automático |
> | Acção | Microsoft.Insights/Baseline/Read | Leia uma linha de base em métrica (pré-visualização) |
> | Acção | Microsoft.Insights/CalculateBaseline/Read | Calcular a linha de base para valores de métrica (pré-visualização) |
> | Acção | Microsoft.Insights/Components/AnalyticsItems/Delete | Eliminar um item de análise do Application Insights |
> | Acção | Microsoft.Insights/Components/AnalyticsItems/Read | A ler um item de análise do Application Insights |
> | Acção | Microsoft.Insights/Components/AnalyticsItems/Write | Escrever um item de análise do Application Insights |
> | Acção | Microsoft.Insights/Components/AnalyticsTables/Action | Ação de tabela de análise do Application Insights |
> | Acção | Microsoft.Insights/Components/AnalyticsTables/Delete | Eliminar um esquema de tabela de análise do Application Insights |
> | Acção | Microsoft.Insights/Components/AnalyticsTables/Read | A ler um esquema de tabela de análise do Application Insights |
> | Acção | Microsoft.Insights/Components/AnalyticsTables/Write | A escrever um esquema de tabela de análise do Application Insights |
> | Acção | Microsoft.Insights/Components/Annotations/Delete | Eliminar uma anotação do Application Insights |
> | Acção | Microsoft.Insights/Components/Annotations/Read | A ler uma anotação do Application Insights |
> | Acção | Microsoft.Insights/Components/Annotations/Write | A escrever uma anotação do Application Insights |
> | Acção | Microsoft.Insights/Components/Api/Read | A ler a API de dados de componente do Application Insights |
> | Acção | Microsoft.Insights/Components/ApiKeys/Action | Gerar uma chave de API do Application Insights |
> | Acção | Microsoft.Insights/Components/ApiKeys/Delete | Eliminar uma chave de API do Application Insights |
> | Acção | Microsoft.Insights/Components/ApiKeys/Read | A ler uma chave de API do Application Insights |
> | Acção | Microsoft.Insights/Components/BillingPlanForComponent/Read | A ler um plano de faturação para o componente do Application Insights |
> | Acção | Microsoft.Insights/Components/CurrentBillingFeatures/Read | A ler as atuais funcionalidades de faturação para componente do Application Insights |
> | Acção | Microsoft.Insights/Components/CurrentBillingFeatures/Write | A escrever as atuais funcionalidades de faturação para componente do Application Insights |
> | Acção | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | A ler uma configuração de integração de ALM predefinida do Application Insights |
> | Acção | Microsoft.Insights/Components/Delete | A eliminar uma configuração de componentes de informações da aplicação |
> | Acção | Microsoft.Insights/Components/Events/Read | Obter registos do Application Insights com o formato de consulta do OData |
> | Acção | Microsoft.Insights/Components/ExportConfiguration/Action | Ação de definições de exportação do Application Insights |
> | Acção | Microsoft.Insights/Components/ExportConfiguration/Delete | A eliminar definições de exportação do Application Insights |
> | Acção | Microsoft.Insights/Components/ExportConfiguration/Read | A ler configurações de exportação do Application Insights |
> | Acção | Microsoft.Insights/Components/ExportConfiguration/Write | A escrever definições de exportação do Application Insights |
> | Acção | Microsoft.Insights/Components/ExtendQueries/Read | A ler os resultados de consulta estendida de componente do Application Insights |
> | Acção | Microsoft.Insights/Components/Favorites/Delete | Eliminar um favorito do Application Insights |
> | Acção | Microsoft.Insights/Components/Favorites/Read | A ler um favorito do Application Insights |
> | Acção | Microsoft.Insights/Components/Favorites/Write | A escrever um favorito do Application Insights |
> | Acção | Microsoft.Insights/Components/FeatureCapabilities/Read | A ler as funcionalidades de componente do Application Insights |
> | Acção | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | A ler as funcionalidades de faturaçao disponíveis de componente do Application Insights |
> | Acção | Microsoft.Insights/Components/GetToken/Read | A ler um token de componente do Application Insights |
> | Acção | Microsoft.Insights/Components/MetricDefinitions/Read | A ler definições métricas de componente do Application Insights |
> | Acção | Microsoft.Insights/Components/Metrics/Read | A ler métricas de componente do Application Insights |
> | Acção | Microsoft.Insights/Components/Move/Action | Mover um Componente do Application Insights para outro grupo de recursos ou subscrição |
> | Acção | Microsoft.Insights/Components/MyAnalyticsItems/Delete | A eliminar um item de análise pessoal do Application Insights |
> | Acção | Microsoft.Insights/Components/MyAnalyticsItems/Read | A ler um item de análise pessoal do Application Insights |
> | Acção | Microsoft.Insights/Components/MyAnalyticsItems/Write | Escrever um item de análise pessoal do Application Insights |
> | Acção | Microsoft.Insights/Components/MyFavorites/Read | A ler um favorito pessoal do Application Insights |
> | Acção | Microsoft.Insights/Components/Operations/Read | Obter o estado de operações de execução longa no Application Insights |
> | Acção | Microsoft.Insights/Components/PricingPlans/Read | A ler um plano de preços de componentes do Application Insights |
> | Acção | Microsoft.Insights/Components/PricingPlans/Write | A escrever um plano de preços de componente do Application Insights |
> | Acção | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | A ler configuração de deteção proativa do Application Insights |
> | Acção | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | A escrever configuração de deteção proativa do Application Insights |
> | Acção | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Ler definições de métricas |
> | Acção | Microsoft.Insights/Components/Purge/Action | A remover os dados do Application Insights |
> | Acção | Microsoft.Insights/Components/Query/Read | Executar consultas aos registos do Application Insights |
> | Acção | Microsoft.Insights/Components/QuotaStatus/Read | A ler estado de quota de componente do Application Insights |
> | Acção | Microsoft.Insights/Components/Read | A ler uma configuração de componentes de informações da aplicação |
> | Acção | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | A ler as localizações de webtest do Application Insights |
> | Acção | Microsoft.Insights/Components/Webtests/Read | A ler uma configuração de webtest |
> | Acção | Microsoft.Insights/Components/WorkItemConfigs/Delete | Eliminar uma configuração de integração de ALM do Application Insights |
> | Acção | Microsoft.Insights/Components/WorkItemConfigs/Read | A ler uma configuração de integração de ALM do Application Insights |
> | Acção | Microsoft.Insights/Components/WorkItemConfigs/Write | A escrever uma configuração de integração de ALM predefinida do Application Insights |
> | Acção | Microsoft.Insights/Components/Write | A escrever para uma configuração de componentes de informações da aplicação |
> | Acção | Microsoft.Insights/DiagnosticSettings/Delete | Eliminar uma definição de diagnóstico de recursos |
> | Acção | Microsoft.Insights/DiagnosticSettings/Read | Ler uma definição de diagnóstico do recurso |
> | Acção | Microsoft.Insights/DiagnosticSettings/Write | Criar ou atualizar uma definição de diagnóstico de recurso |
> | Acção | Microsoft.Insights/EventCategories/Read | Ler as categorias disponíveis de eventos do Registo de Atividades |
> | Acção | Microsoft.Insights/eventtypes/digestevents/Read | Ler resumo do tipo de eventos de gestão |
> | Acção | Microsoft.Insights/eventtypes/values/Read | Ler eventos de Registo de Atividades |
> | Acção | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Eliminar uma definição do diagnóstico do registo do fluxo de rede |
> | Acção | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Ler uma definição do diagnóstico do registo do fluxo de rede |
> | Acção | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Criar ou atualizar uma definição do diagnóstico do registo de fluxo de rede |
> | Acção | Microsoft.Insights/ListMigrationDate/Action | Voltar a obter a data de migração da Subscrição |
> | Acção | Microsoft.Insights/ListMigrationDate/Read | Voltar a obter a data de migração da subscrição |
> | Acção | Microsoft.Insights/LogDefinitions/Read | Ler definições de registo |
> | Acção | Microsoft.Insights/LogProfiles/Delete | Eliminar um perfil de registo do Registo de Atividades |
> | Acção | Microsoft.Insights/LogProfiles/Read | Ler um perfil de registo do Registo de Atividades |
> | Acção | Microsoft.Insights/LogProfiles/Write | Criar ou atualizar um perfil de registo do Registo de Atividades |
> | Acção | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Ler dados da tabela ADAssessmentRecommendation |
> | Acção | Microsoft.Insights/Logs/ADReplicationResult/Read | Ler dados da tabela ADReplicationResult |
> | Acção | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Ler dados da tabela ADSecurityAssessmentRecommendation |
> | Acção | Microsoft.Insights/Logs/Alert/Read | Ler dados da tabela de Alertas |
> | Acção | Microsoft.Insights/Logs/AlertHistory/Read | Ler dados da tabela AlertHistory |
> | Acção | Microsoft.Insights/Logs/ApplicationInsights/Read | Ler dados da tabela ApplicationInsights |
> | Acção | Microsoft.Insights/Logs/AzureActivity/Read | Ler dados da tabela AzureActivity |
> | Acção | Microsoft.Insights/Logs/AzureMetrics/Read | Ler dados da tabela AzureMetrics |
> | Acção | Microsoft.Insights/Logs/BoundPort/Read | Ler dados da tabela BoundPort |
> | Acção | Microsoft.Insights/Logs/CommonSecurityLog/Read | Ler dados da tabela CommonSecurityLog |
> | Acção | Microsoft.Insights/Logs/ComputerGroup/Read | Ler dados da tabela ComputerGroup |
> | Acção | Microsoft.Insights/Logs/ConfigurationChange/Read | Ler dados da tabela ConfigurationChange |
> | Acção | Microsoft.Insights/Logs/ConfigurationData/Read | Ler dados da tabela ConfigurationData |
> | Acção | Microsoft.Insights/Logs/ContainerImageInventory/Read | Ler dados da tabela ContainerImageInventory |
> | Acção | Microsoft.Insights/Logs/ContainerInventory/Read | Ler dados da tabela ContainerInventory |
> | Acção | Microsoft.Insights/Logs/ContainerLog/Read | Ler dados da tabela ContainerLog |
> | Acção | Microsoft.Insights/Logs/ContainerServiceLog/Read | Ler dados da tabela ContainerServiceLog |
> | Acção | Microsoft.Insights/Logs/DeviceAppCrash/Read | Ler dados da tabela DeviceAppCrash |
> | Acção | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Ler dados da tabela DeviceAppLaunch |
> | Acção | Microsoft.Insights/Logs/DeviceCalendar/Read | Ler dados da tabela DeviceCalendar |
> | Acção | Microsoft.Insights/Logs/DeviceCleanup/Read | Ler dados da tabela DeviceCleanup |
> | Acção | Microsoft.Insights/Logs/DeviceConnectSession/Read | Ler dados da tabela DeviceConnectSession |
> | Acção | Microsoft.Insights/Logs/DeviceEtw/Read | Ler dados da tabela DeviceEtw |
> | Acção | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Ler dados da tabela DeviceHardwareHealth |
> | Acção | Microsoft.Insights/Logs/DeviceHealth/Read | Ler dados da tabela DeviceHealth |
> | Acção | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Ler dados da tabela DeviceHeartbeat |
> | Acção | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Ler dados da tabela DeviceSkypeHeartbeat |
> | Acção | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Ler dados da tabela DeviceSkypeSignIn |
> | Acção | Microsoft.Insights/Logs/DeviceSleepState/Read | Ler dados da tabela DeviceSleepState |
> | Acção | Microsoft.Insights/Logs/DHAppFailure/Read | Ler dados da tabela DHAppFailure |
> | Acção | Microsoft.Insights/Logs/DHAppReliability/Read | Ler dados da tabela DHAppReliability |
> | Acção | Microsoft.Insights/Logs/DHDriverReliability/Read | Ler dados da tabela DHDriverReliability |
> | Acção | Microsoft.Insights/Logs/DHLogonFailures/Read | Ler dados da tabela DHLogonFailures |
> | Acção | Microsoft.Insights/Logs/DHLogonMetrics/Read | Ler dados da tabela DHLogonMetrics |
> | Acção | Microsoft.Insights/Logs/DHOSCrashData/Read | Ler dados da tabela DHOSCrashData |
> | Acção | Microsoft.Insights/Logs/DHOSReliability/Read | Ler dados da tabela DHOSReliability |
> | Acção | Microsoft.Insights/Logs/DHWipAppLearning/Read | Ler dados da tabela DHWipAppLearning |
> | Acção | Microsoft.Insights/Logs/DnsEvents/Read | Ler dados da tabela DnsEvents |
> | Acção | Microsoft.Insights/Logs/DnsInventory/Read | Ler dados da tabela DnsInventory |
> | Acção | Microsoft.Insights/Logs/ETWEvent/Read | Ler dados da tabela ETWEvent |
> | Acção | Microsoft.Insights/Logs/Event/Read | Ler dados da tabela de Eventos |
> | Acção | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Ler dados da tabela ExchangeAssessmentRecommendation |
> | Acção | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Ler dados da tabela ExchangeOnlineAssessmentRecommendation |
> | Acção | Microsoft.Insights/Logs/Heartbeat/Read | Ler dados da tabela Heartbeat |
> | Acção | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Ler dados da tabela IISAssessmentRecommendation |
> | Acção | Microsoft.Insights/Logs/InboundConnection/Read | Ler dados da tabela InboundConnection |
> | Acção | Microsoft.Insights/Logs/KubeNodeInventory/Read | Ler dados da tabela KubeNodeInventory |
> | Acção | Microsoft.Insights/Logs/KubePodInventory/Read | Ler dados da tabela KubePodInventory |
> | Acção | Microsoft.Insights/Logs/LinuxAuditLog/Read | Ler dados da tabela LinuxAuditLog |
> | Acção | Microsoft.Insights/Logs/MAApplication/Read | Ler dados da tabela MAApplication |
> | Acção | Microsoft.Insights/Logs/MAApplicationHealth/Read | Ler dados da tabela MAApplicationHealth |
> | Acção | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Ler dados da tabela MAApplicationHealthAlternativeVersions |
> | Acção | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Ler dados da tabela MAApplicationHealthIssues |
> | Acção | Microsoft.Insights/Logs/MAApplicationInstance/Read | Ler dados da tabela MAApplicationInstance |
> | Acção | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Ler dados da tabela MAApplicationInstanceReadiness |
> | Acção | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Ler dados da tabela MAApplicationReadiness |
> | Acção | Microsoft.Insights/Logs/MADeploymentPlan/Read | Ler dados da tabela MADeploymentPlan |
> | Acção | Microsoft.Insights/Logs/MADevice/Read | Ler dados da tabela MADevice |
> | Acção | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Ler dados da tabela MADevicePnPHealth |
> | Acção | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | Acção | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Ler dados da tabela MADevicePnPHealthIssues |
> | Acção | Microsoft.Insights/Logs/MADeviceReadiness/Read | Ler dados da tabela MADeviceReadiness |
> | Acção | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Ler dados da tabela MADriverInstanceReadiness |
> | Acção | Microsoft.Insights/Logs/MADriverReadiness/Read | Ler dados da tabela MADriverReadiness |
> | Acção | Microsoft.Insights/Logs/MAOfficeAddin/Read | Ler dados da tabela MAOfficeAddin |
> | Acção | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Ler dados da tabela MAOfficeAddinHealth |
> | Acção | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Ler dados da tabela MAOfficeAddinHealthIssues |
> | Acção | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Ler dados da tabela MAOfficeAddinInstance |
> | Acção | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | Acção | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Ler dados da tabela MAOfficeAddinReadiness |
> | Acção | Microsoft.Insights/Logs/MAOfficeApp/Read | Ler dados da tabela MAOfficeApp |
> | Acção | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Ler dados da tabela MAOfficeAppHealth |
> | Acção | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Ler dados da tabela MAOfficeAppInstance |
> | Acção | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Ler dados da tabela MAOfficeAppReadiness |
> | Acção | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Ler dados da tabela MAOfficeBuildInfo |
> | Acção | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Ler dados da tabela MAOfficeCurrencyAssessment |
> | Acção | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Ler dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | Acção | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Ler dados da tabela MAOfficeDeploymentStatus |
> | Acção | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Ler dados da tabela MAOfficeMacroHealth |
> | Acção | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Ler dados da tabela MAOfficeMacroHealthIssues |
> | Acção | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Ler dados da tabela MAOfficeMacroIssueInstanceReadiness |
> | Acção | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Ler dados da tabela MAOfficeMacroIssueReadiness |
> | Acção | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Ler dados da tabela MAOfficeMacroSummary |
> | Acção | Microsoft.Insights/Logs/MAOfficeSuite/Read | Ler dados da tabela MAOfficeSuite |
> | Acção | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Ler dados da tabela MAOfficeSuiteInstance |
> | Acção | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Ler dados da tabela MAProposedPilotDevices |
> | Acção | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Ler dados da tabela MAWindowsBuildInfo |
> | Acção | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Ler dados da tabela MAWindowsCurrencyAssessment |
> | Acção | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Ler dados da tabela MAWindowsCurrencyAssessmentDailyCounts |
> | Acção | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Ler dados da tabela MAWindowsDeploymentStatus |
> | Acção | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | Acção | Microsoft.Insights/Logs/NetworkMonitoring/Read | Ler dados da tabela NetworkMonitoring |
> | Acção | Microsoft.Insights/Logs/OfficeActivity/Read | Ler dados da tabela OfficeActivity |
> | Acção | Microsoft.Insights/Logs/Operation/Read | Ler dados da tabela de Operação |
> | Acção | Microsoft.Insights/Logs/OutboundConnection/Read | Ler dados da tabela OutboundConnection |
> | Acção | Microsoft.Insights/Logs/Perf/Read | Ler dados da tabela de Desempenho |
> | Acção | Microsoft.Insights/Logs/ProtectionStatus/Read | Ler dados da tabela ProtectionStatus |
> | Acção | Microsoft.Insights/Logs/Read | A ler dados de todos os seus registos |
> | Acção | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Ler dados da tabela ReservedAzureCommonFields |
> | Acção | Microsoft.Insights/Logs/ReservedCommonFields/Read | Ler dados da tabela ReservedCommonFields |
> | Acção | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Ler dados da tabela SCCMAssessmentRecommendation |
> | Acção | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Ler dados da tabela SCOMAssessmentRecommendation |
> | Acção | Microsoft.Insights/Logs/SecurityAlert/Read | Ler dados da tabela SecurityAlert |
> | Acção | Microsoft.Insights/Logs/SecurityBaseline/Read | Ler dados da tabela SecurityBaseline |
> | Acção | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Ler dados da tabela SecurityBaselineSummary |
> | Acção | Microsoft.Insights/Logs/SecurityDetection/Read | Ler dados da tabela SecurityDetection |
> | Acção | Microsoft.Insights/Logs/SecurityEvent/Read | Ler dados da tabela SecurityEvent |
> | Acção | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Ler dados da tabela ServiceFabricOperationalEvent |
> | Acção | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Ler dados da tabela ServiceFabricReliableActorEvent |
> | Acção | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Ler dados da tabela ServiceFabricReliableServiceEvent |
> | Acção | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Ler dados da tabela SfBAssessmentRecommendation |
> | Acção | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Ler dados da tabela SfBOnlineAssessmentRecommendation |
> | Acção | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Ler dados da tabela SharePointOnlineAssessmentRecommendation |
> | Acção | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Ler dados da tabela SPAssessmentRecommendation |
> | Acção | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Ler dados da tabela SQLAssessmentRecommendation |
> | Acção | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Ler dados da tabela SQLQueryPerformance |
> | Acção | Microsoft.Insights/Logs/Syslog/Read | Ler dados da tabela Syslog |
> | Acção | Microsoft.Insights/Logs/SysmonEvent/Read | Ler dados da tabela SysmonEvent |
> | Acção | Microsoft.Insights/Logs/UAApp/Read | Ler dados da tabela UAApp |
> | Acção | Microsoft.Insights/Logs/UAComputer/Read | Ler dados da tabela UAComputer |
> | Acção | Microsoft.Insights/Logs/UAComputerRank/Read | Ler dados da tabela UAComputerRank |
> | Acção | Microsoft.Insights/Logs/UADriver/Read | Ler dados da tabela UADriver |
> | Acção | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Ler dados da tabela UADriverProblemCodes |
> | Acção | Microsoft.Insights/Logs/UAFeedback/Read | Ler dados da tabela UAFeedback |
> | Acção | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Ler dados da tabela UAHardwareSecurity |
> | Acção | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Ler dados da tabela UAIESiteDiscovery |
> | Acção | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Ler dados da tabela UAOfficeAddIn |
> | Acção | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Ler dados da tabela UAProposedActionPlan |
> | Acção | Microsoft.Insights/Logs/UASysReqIssue/Read | Ler dados da tabela UASysReqIssue |
> | Acção | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Ler dados da tabela UAUpgradedComputer |
> | Acção | Microsoft.Insights/Logs/Update/Read | Ler dados da tabela de Atualização |
> | Acção | Microsoft.Insights/Logs/UpdateRunProgress/Read | Ler dados da tabela UpdateRunProgress |
> | Acção | Microsoft.Insights/Logs/UpdateSummary/Read | Ler dados da tabela UpdateSummary |
> | Acção | Microsoft.Insights/Logs/Usage/Read | Ler dados da tabela de Utilização |
> | Acção | Microsoft.Insights/Logs/W3CIISLog/Read | Ler dados da tabela W3CIISLog |
> | Acção | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Ler dados da tabela WaaSDeploymentStatus |
> | Acção | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Ler dados da tabela WaaSInsiderStatus |
> | Acção | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Ler dados da tabela WaaSUpdateStatus |
> | Acção | Microsoft.Insights/Logs/WDAVStatus/Read | Ler dados da tabela WDAVStatus |
> | Acção | Microsoft.Insights/Logs/WDAVThreat/Read | Ler dados da tabela WDAVThreat |
> | Acção | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Ler dados da tabela WindowsClientAssessmentRecommendation |
> | Acção | Microsoft.Insights/Logs/WindowsFirewall/Read | Ler dados da tabela WindowsFirewall |
> | Acção | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Ler dados da tabela WindowsServerAssessmentRecommendation |
> | Acção | Microsoft.Insights/Logs/WireData/Read | Ler dados da tabela WireData |
> | Acção | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Ler dados da tabela WUDOAggregatedStatus |
> | Acção | Microsoft.Insights/Logs/WUDOStatus/Read | Ler dados da tabela WUDOStatus |
> | Acção | Microsoft.Insights/MetricAlerts/Delete | Eliminar um alerta de métrica |
> | Acção | Microsoft.Insights/MetricAlerts/Read | Escrever um alerta de métrica |
> | Acção | Microsoft.Insights/MetricAlerts/Status/Read | Ler o estado de alerta de métrica |
> | Acção | Microsoft.Insights/MetricAlerts/Write | Criar ou atualizar um alerta de métrica |
> | Acção | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Ler definições de métricas |
> | Acção | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Ler definições de métricas |
> | Acção | Microsoft.Insights/MetricDefinitions/Read | Ler definições de métricas |
> | Acção | Microsoft.Insights/Metricnamespaces/Read | Espaços de nomes de métrica de leitura |
> | Acção | Microsoft.Insights/Metrics/Action | Ações de métricas |
> | Acção | Microsoft.Insights/Metrics/Microsoft.Insights/Read | Ler métricas |
> | Acção | Microsoft.Insights/Metrics/providers/Metrics/Read | Ler métricas |
> | Acção | Microsoft.Insights/Metrics/Read | Ler métricas |
> | DataAction | Microsoft.Insights/Metrics/Write | Escrever métricas |
> | Acção | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrar subscrição para o novo modelo de preços |
> | Acção | Microsoft.Insights/Operations/Read | Operações de leitura |
> | Acção | Microsoft.Insights/Register/Action | Registar o fornecedor do Microsoft Insights |
> | Acção | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Anular subscrição para o modelo de preços legado |
> | Acção | Microsoft.Insights/ScheduledQueryRules/Delete | A eliminar uma regra de consulta agendada |
> | Acção | Microsoft.Insights/ScheduledQueryRules/Read | A ler uma regra de consulta agendada |
> | Acção | Microsoft.Insights/ScheduledQueryRules/Write | A escrever uma regra de consulta agendada |
> | Acção | Microsoft.Insights/Tenants/Register/Action | Inicia o fornecedor do Microsoft Insights |
> | Acção | Microsoft.Insights/Unregister/Action | Registar o fornecedor do Microsoft Insights |
> | Acção | Microsoft.Insights/Webtests/Delete | A eliminar uma configuração de webtest |
> | Acção | Microsoft.Insights/Webtests/GetToken/Read | A ler um token de webtest |
> | Acção | Microsoft.Insights/Webtests/MetricDefinitions/Read | A ler definições de métrica de webtest |
> | Acção | Microsoft.Insights/Webtests/Metrics/Read | A ler métricas de webtest |
> | Acção | Microsoft.Insights/Webtests/Read | A ler uma configuração de webtest |
> | Acção | Microsoft.Insights/Webtests/Write | A escrever para uma configuração de webtest |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Intune/diagnosticsettings/delete | A eliminar uma definição de diagnóstico |
> | Acção | Microsoft.Intune/diagnosticsettings/read | Ler uma definição de diagnóstico |
> | Acção | Microsoft.Intune/diagnosticsettings/write | Escrever uma definição de diagnóstico |
> | Acção | Microsoft.Intune/diagnosticsettingscategories/read | Ler um categorias de definição de diagnóstico |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.IoTCentral/appTemplates/action | Obtém todos os modelos de aplicação disponíveis no Azure IoT Central |
> | Acção | Microsoft.IoTCentral/checkNameAvailability/action | Verifica se um nome de aplicação de IoT Central está disponível |
> | Acção | Microsoft.IoTCentral/checkSubdomainAvailability/action | Verifica se um subdomínio de aplicação de IoT Central está disponível |
> | Acção | Microsoft.IoTCentral/IoTApps/delete | Elimina um aplicações de centro de IoT |
> | Acção | Microsoft.IoTCentral/IoTApps/read | Obtém um único aplicativo de Central de IoT |
> | Acção | Microsoft.IoTCentral/IoTApps/write | Cria ou atualiza um aplicações de centro de IoT |
> | Acção | Microsoft.IoTCentral/operations/read | Obtém todas as operações disponíveis em aplicações de IoT Central |
> | Acção | Microsoft.IoTCentral/register/action | Registar a subscrição para o fornecedor de recursos do Azure IoT Central |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.IoTSpaces/Graph/delete | Elimina o recurso de gráfico Microsoft.IoTSpaces |
> | Acção | Microsoft.IoTSpaces/Graph/read | Obtém os recursos de Microsoft.IoTSpaces Graph |
> | Acção | Microsoft.IoTSpaces/Graph/write | Criar recurso Microsoft.IoTSpaces Graph |
> | Acção | Microsoft.IoTSpaces/register/action | Registar subscrição para o fornecedor de recursos de gráfico Microsoft.IoTSpaces permitir a criação de recursos |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.KeyVault/checkNameAvailability/read | Verifica se um nome de cofre de chaves é válido e se não está em utilização |
> | Acção | Microsoft.KeyVault/deletedVaults/read | Visualizar as propriedades dos cofres de chaves eliminados de forma recuperável |
> | Acção | Microsoft.KeyVault/hsmPools/delete | Eliminar um conjunto de HSM |
> | Acção | Microsoft.KeyVault/hsmPools/joinVault/action | Associar um cofre de chaves a um conjunto de HSM |
> | Acção | Microsoft.KeyVault/hsmPools/read | Visualizar as propriedades de um conjunto HSM |
> | Acção | Microsoft.KeyVault/hsmPools/write | Criar um novo conjunto de HSM de atualização das propriedades de um conjunto de HSM existente |
> | Acção | Microsoft.KeyVault/locations/deletedVaults/purge/action | Remover um cofre de chaves eliminado de forma recuperável |
> | Acção | Microsoft.KeyVault/locations/deletedVaults/read | Visualizar as propriedades de um cofre de chaves eliminado de forma recuperável |
> | Acção | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft.KeyVault de que uma rede virtual ou sub-rede está a ser eliminada |
> | Acção | Microsoft.KeyVault/locations/operationResults/read | Verificar o resultado de uma operação de execução longa |
> | Acção | Microsoft.KeyVault/operations/read | Lista as operações disponíveis no fornecedor de recursos Microsoft.KeyVault |
> | Acção | Microsoft.KeyVault/register/action | Regista uma subscrição |
> | Acção | Microsoft.KeyVault/unregister/action | Cancela o registo de uma subscrição |
> | Acção | Microsoft.KeyVault/vaults/accessPolicies/write | Atualize uma política de acesso existente através da intercalação ou substituição, ou adicione uma nova política de acesso ao cofre. |
> | Acção | Microsoft.KeyVault/vaults/delete | Eliminar um cofre de chaves |
> | Acção | Microsoft.KeyVault/vaults/deploy/action | Permite o acesso a segredos num cofre de chaves ao implementar os recursos do Azure |
> | Acção | Microsoft.KeyVault/vaults/eventGridFilters/delete | Notifica Microsoft.KeyVault de que está a ser eliminada uma Subscrição EventGrid do Key Vault |
> | Acção | Microsoft.KeyVault/vaults/eventGridFilters/read | Notifica Microsoft.KeyVault de que está a ser visualizada uma Subscrição EventGrid do Key Vault |
> | Acção | Microsoft.KeyVault/vaults/eventGridFilters/write | Notifica Microsoft.KeyVault de que está a ser criada uma nova Subscrição EventGrid do Key Vault |
> | Acção | Microsoft.KeyVault/vaults/read | Ver as propriedades de um cofre de chaves |
> | Acção | Microsoft.KeyVault/vaults/secrets/read | Ver as propriedades de um segredo, mas não o seu valor |
> | Acção | Microsoft.KeyVault/vaults/secrets/write | Crie um novo segredo ou atualize o valor de um segredo existente |
> | Acção | Microsoft.KeyVault/vaults/write | Criar um novo cofre de chaves ou atualizar as propriedades de um cofre de chaves existente |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Kusto/Clusters/Activate/action | O início do cluster. |
> | Acção | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Verifica a disponibilidade de nome de cluster. |
> | Acção | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Adiciona entidades de base de dados. |
> | Acção | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | Verifica a disponibilidade de nome para um determinado tipo. |
> | Acção | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Elimina um recurso de ligações de dados. |
> | Acção | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Lê um recurso de ligações de dados. |
> | Acção | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Escreve um recurso de ligações de dados. |
> | Acção | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Valida a ligação de dados de base de dados. |
> | Acção | Microsoft.Kusto/Clusters/Databases/delete | Elimina um recurso de base de dados. |
> | Acção | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Elimina um recurso de ligações de Hub de eventos. |
> | Acção | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Lê um recurso de ligações de Hub de eventos. |
> | Acção | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Escreve um recurso de ligações de Hub de eventos. |
> | Acção | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Valida a ligação do Hub de eventos de base de dados. |
> | Acção | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Listas de base de dados principais. |
> | Acção | Microsoft.Kusto/Clusters/Databases/read | Lê um recurso de base de dados. |
> | Acção | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Remove de base de dados principais. |
> | Acção | Microsoft.Kusto/Clusters/Databases/write | Escreve um recurso de base de dados. |
> | Acção | Microsoft.Kusto/Clusters/Deactivate/action | Interrompe o cluster. |
> | Acção | Microsoft.Kusto/Clusters/delete | Elimina um recurso de cluster. |
> | Acção | Microsoft.Kusto/Clusters/read | Lê um recurso de cluster. |
> | Acção | Microsoft.Kusto/Clusters/SKUs/read | Lê um recurso SKU do cluster. |
> | Acção | Microsoft.Kusto/Clusters/Start/action | O início do cluster. |
> | Acção | Microsoft.Kusto/Clusters/Stop/action | Interrompe o cluster. |
> | Acção | Microsoft.Kusto/Clusters/write | Escreve um recurso de cluster. |
> | Acção | Microsoft.Kusto/Locations/CheckNameAvailability/action | Verifica a disponibilidade de nome de recurso. |
> | Acção | Microsoft.Kusto/locations/operationresults/read | Lê recursos de operações |
> | Acção | Microsoft.Kusto/Operations/read | Lê recursos de operações |
> | Acção | Microsoft.Kusto/Register/action | Regista a subscrição para o fornecedor de recursos do Kusto. |
> | Acção | Microsoft.Kusto/SKUs/read | Lê um recurso SKU. |
> | Acção | Microsoft.Kusto/Unregister/action | Anula o registo da subscrição para o fornecedor de recursos do Kusto. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.LabServices/labAccounts/CreateLab/action | Crie um laboratório numa conta do laboratório. |
> | Acção | Microsoft.LabServices/labAccounts/delete | Elimine contas de laboratório. |
> | Acção | Microsoft.LabServices/labAccounts/galleryImages/delete | Elimine imagens da galeria. |
> | Acção | Microsoft.LabServices/labAccounts/galleryImages/read | Leia as imagens da galeria. |
> | Acção | Microsoft.LabServices/labAccounts/galleryImages/write | Adiciona ou modifica as imagens da galeria. |
> | Acção | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Obtenha informações de disponibilidade regional para cada categoria de tamanho configurada com uma conta de laboratório |
> | Acção | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Adicionar utilizadores a um laboratório |
> | Acção | Microsoft.LabServices/labAccounts/labs/delete | Elimine laboratórios. |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Um ambiente aleatório para um utilizador nas definições de ambiente de afirmações |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Elimine definição do ambiente. |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | O ambiente de afirmações e o atribui ao utilizador |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Elimine ambientes. |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Ambientes de leitura. |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Repõe a palavra-passe do utilizador num ambiente |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Começa um ambiente a partir de todos os recursos dentro do ambiente. |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Um ambiente é interrompido devido ao parar todos os recursos dentro do ambiente |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Adiciona ou modifica os ambientes. |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Aprovisiona/desprovisiona necessários recursos para um definição de ambiente com base no estado atual da definição/ambiente de laboratório. |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Ler definição de ambiente. |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Salva a imagem de modelo atual para a Galeria partilhada na conta de laboratório |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Elimine agendas. |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Agenda de leitura. |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Adicione ou modifique as Programações. |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Começa um modelo a partir de todos os recursos dentro do modelo. |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Interrompe um modelo, parando todos os recursos dentro do modelo. |
> | Acção | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Adicionar ou modificar a definição do ambiente. |
> | Acção | Microsoft.LabServices/labAccounts/labs/read | Laboratórios de leitura. |
> | Acção | Microsoft.LabServices/labAccounts/labs/Register/action | Registre-se para o laboratório gerido. |
> | Acção | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Enviar um e-mail com a ligação de registo para o laboratório |
> | Acção | Microsoft.LabServices/labAccounts/labs/users/delete | Elimine utilizadores. |
> | Acção | Microsoft.LabServices/labAccounts/labs/users/read | Leia os utilizadores. |
> | Acção | Microsoft.LabServices/labAccounts/labs/users/write | Adiciona ou modifica os utilizadores. |
> | Acção | Microsoft.LabServices/labAccounts/labs/write | Adicionar ou modificar laboratórios. |
> | Acção | Microsoft.LabServices/labAccounts/read | Leia as contas de laboratório. |
> | Acção | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Elimine sharedgalleries. |
> | Acção | Microsoft.LabServices/labAccounts/sharedGalleries/read | Leia sharedgalleries. |
> | Acção | Microsoft.LabServices/labAccounts/sharedGalleries/write | Adiciona ou modifica sharedgalleries. |
> | Acção | Microsoft.LabServices/labAccounts/sharedImages/delete | Elimine sharedimages. |
> | Acção | Microsoft.LabServices/labAccounts/sharedImages/read | Leia sharedimages. |
> | Acção | Microsoft.LabServices/labAccounts/sharedImages/write | Adiciona ou modifica sharedimages. |
> | Acção | Microsoft.LabServices/labAccounts/write | Adiciona ou modifica contas de laboratório. |
> | Acção | Microsoft.LabServices/locations/operations/read | Operações de leitura. |
> | Acção | Microsoft.LabServices/register/action | Regista a subscrição |
> | Acção | Microsoft.LabServices/users/GetEnvironment/action | Obtém os detalhes da máquina virtual |
> | Acção | Microsoft.LabServices/users/GetOperationBatchStatus/action | Obter estado da operação de batch |
> | Acção | Microsoft.LabServices/users/GetOperationStatus/action | Obtém o estado da operação de longa |
> | Acção | Microsoft.LabServices/users/GetPersonalPreferences/action | Obter preferências pessoais de um utilizador |
> | Acção | Microsoft.LabServices/users/ListAllEnvironments/action | Lista de todos os ambientes para o utilizador |
> | Acção | Microsoft.LabServices/users/ListEnvironments/action | Ambientes de lista para o utilizador |
> | Acção | Microsoft.LabServices/users/ListLabs/action | Laboratórios de lista para o utilizador. |
> | Acção | Microsoft.LabServices/users/Register/action | Registe-se um utilizador a um laboratório gerido |
> | Acção | Microsoft.LabServices/users/ResetPassword/action | Repõe a palavra-passe do utilizador num ambiente |
> | Acção | Microsoft.LabServices/users/StartEnvironment/action | Começa um ambiente a partir de todos os recursos dentro do ambiente. |
> | Acção | Microsoft.LabServices/users/StopEnvironment/action | Um ambiente é interrompido devido ao parar todos os recursos dentro do ambiente |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | DataAction | Microsoft.LogAnalytics/logs/ADAssessmentRecommendation/read | Ler dados da tabela ADAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/ADReplicationResult/read | Ler dados da tabela ADReplicationResult |
> | DataAction | Microsoft.LogAnalytics/logs/ADSecurityAssessmentRecommendation/read | Ler dados da tabela ADSecurityAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/Alert/read | Ler dados da tabela de Alertas |
> | DataAction | Microsoft.LogAnalytics/logs/AlertHistory/read | Ler dados da tabela AlertHistory |
> | DataAction | Microsoft.LogAnalytics/logs/AppCenterError/read | Ler dados da tabela AppCenterError |
> | DataAction | Microsoft.LogAnalytics/logs/ApplicationInsights/read | Ler dados da tabela ApplicationInsights |
> | DataAction | Microsoft.LogAnalytics/logs/AuditLogs/read | Ler dados da tabela AuditLogs |
> | DataAction | Microsoft.LogAnalytics/logs/AzureActivity/read | Ler dados da tabela AzureActivity |
> | DataAction | Microsoft.LogAnalytics/logs/AzureMetrics/read | Ler dados da tabela AzureMetrics |
> | DataAction | Microsoft.LogAnalytics/logs/BoundPort/read | Ler dados da tabela BoundPort |
> | DataAction | Microsoft.LogAnalytics/logs/CommonSecurityLog/read | Ler dados da tabela CommonSecurityLog |
> | DataAction | Microsoft.LogAnalytics/logs/ComputerGroup/read | Ler dados da tabela ComputerGroup |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationChange/read | Ler dados da tabela ConfigurationChange |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationData/read | Ler dados da tabela ConfigurationData |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerImageInventory/read | Ler dados da tabela ContainerImageInventory |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerInventory/read | Ler dados da tabela ContainerInventory |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerLog/read | Ler dados da tabela ContainerLog |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerServiceLog/read | Ler dados da tabela ContainerServiceLog |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppCrash/read | Ler dados da tabela DeviceAppCrash |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppLaunch/read | Ler dados da tabela DeviceAppLaunch |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCalendar/read | Ler dados da tabela DeviceCalendar |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCleanup/read | Ler dados da tabela DeviceCleanup |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceConnectSession/read | Ler dados da tabela DeviceConnectSession |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceEtw/read | Ler dados da tabela DeviceEtw |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHardwareHealth/read | Ler dados da tabela DeviceHardwareHealth |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHealth/read | Ler dados da tabela DeviceHealth |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHeartbeat/read | Ler dados da tabela DeviceHeartbeat |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeHeartbeat/read | Ler dados da tabela DeviceSkypeHeartbeat |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeSignIn/read | Ler dados da tabela DeviceSkypeSignIn |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSleepState/read | Ler dados da tabela DeviceSleepState |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppFailure/read | Ler dados da tabela DHAppFailure |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppReliability/read | Ler dados da tabela DHAppReliability |
> | DataAction | Microsoft.LogAnalytics/logs/DHDriverReliability/read | Ler dados da tabela DHDriverReliability |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonFailures/read | Ler dados da tabela DHLogonFailures |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonMetrics/read | Ler dados da tabela DHLogonMetrics |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSCrashData/read | Ler dados da tabela DHOSCrashData |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSReliability/read | Ler dados da tabela DHOSReliability |
> | DataAction | Microsoft.LogAnalytics/logs/DHWipAppLearning/read | Ler dados da tabela DHWipAppLearning |
> | DataAction | Microsoft.LogAnalytics/logs/DnsEvents/read | Ler dados da tabela DnsEvents |
> | DataAction | Microsoft.LogAnalytics/logs/DnsInventory/read | Ler dados da tabela DnsInventory |
> | DataAction | Microsoft.LogAnalytics/logs/ETWEvent/read | Ler dados da tabela ETWEvent |
> | DataAction | Microsoft.LogAnalytics/logs/Event/read | Ler dados da tabela de Eventos |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeAssessmentRecommendation/read | Ler dados da tabela ExchangeAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeOnlineAssessmentRecommendation/read | Ler dados da tabela ExchangeOnlineAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/Heartbeat/read | Ler dados da tabela Heartbeat |
> | DataAction | Microsoft.LogAnalytics/logs/IISAssessmentRecommendation/read | Ler dados da tabela IISAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/InboundConnection/read | Ler dados da tabela InboundConnection |
> | DataAction | Microsoft.LogAnalytics/logs/KubeEvents/read | Ler dados da tabela KubeEvents |
> | DataAction | Microsoft.LogAnalytics/logs/KubeNodeInventory/read | Ler dados da tabela KubeNodeInventory |
> | DataAction | Microsoft.LogAnalytics/logs/KubePodInventory/read | Ler dados da tabela KubePodInventory |
> | DataAction | Microsoft.LogAnalytics/logs/KubeServices/read | Ler dados da tabela KubeServices |
> | DataAction | Microsoft.LogAnalytics/logs/LinuxAuditLog/read | Ler dados da tabela LinuxAuditLog |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplication/read | Ler dados da tabela MAApplication |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealth/read | Ler dados da tabela MAApplicationHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthAlternativeVersions/read | Ler dados da tabela MAApplicationHealthAlternativeVersions |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthIssues/read | Ler dados da tabela MAApplicationHealthIssues |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstance/read | Ler dados da tabela MAApplicationInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstanceReadiness/read | Ler dados da tabela MAApplicationInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationReadiness/read | Ler dados da tabela MAApplicationReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MADeploymentPlan/read | Ler dados da tabela MADeploymentPlan |
> | DataAction | Microsoft.LogAnalytics/logs/MADevice/read | Ler dados da tabela MADevice |
> | DataAction | Microsoft.LogAnalytics/logs/MADeviceNotEnrolled/read | Ler dados da tabela MADeviceNotEnrolled |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealth/read | Ler dados da tabela MADevicePnPHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthAlternativeVersions/read | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthIssues/read | Ler dados da tabela MADevicePnPHealthIssues |
> | DataAction | Microsoft.LogAnalytics/logs/MADeviceReadiness/read | Ler dados da tabela MADeviceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverInstanceReadiness/read | Ler dados da tabela MADriverInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverReadiness/read | Ler dados da tabela MADriverReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddin/read | Ler dados da tabela MAOfficeAddin |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealth/read | Ler dados da tabela MAOfficeAddinHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealthIssues/read | Ler dados da tabela MAOfficeAddinHealthIssues |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstance/read | Ler dados da tabela MAOfficeAddinInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstanceReadiness/read | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinReadiness/read | Ler dados da tabela MAOfficeAddinReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeApp/read | Ler dados da tabela MAOfficeApp |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppHealth/read | Ler dados da tabela MAOfficeAppHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppInstance/read | Ler dados da tabela MAOfficeAppInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppReadiness/read | Ler dados da tabela MAOfficeAppReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeBuildInfo/read | Ler dados da tabela MAOfficeBuildInfo |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessment/read | Ler dados da tabela MAOfficeCurrencyAssessment |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessmentDailyCounts/read | Ler dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeDeploymentStatus/read | Ler dados da tabela MAOfficeDeploymentStatus |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealth/read | Ler dados da tabela MAOfficeMacroHealth |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealthIssues/read | Ler dados da tabela MAOfficeMacroHealthIssues |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueInstanceReadiness/read | Ler dados da tabela MAOfficeMacroIssueInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueReadiness/read | Ler dados da tabela MAOfficeMacroIssueReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroSummary/read | Ler dados da tabela MAOfficeMacroSummary |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuite/read | Ler dados da tabela MAOfficeSuite |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuiteInstance/read | Ler dados da tabela MAOfficeSuiteInstance |
> | DataAction | Microsoft.LogAnalytics/logs/MAProposedPilotDevices/read | Ler dados da tabela MAProposedPilotDevices |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsBuildInfo/read | Ler dados da tabela MAWindowsBuildInfo |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessment/read | Ler dados da tabela MAWindowsCurrencyAssessment |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessmentDailyCounts/read | Ler dados da tabela MAWindowsCurrencyAssessmentDailyCounts |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsDeploymentStatus/read | Ler dados da tabela MAWindowsDeploymentStatus |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsSysReqInstanceReadiness/read | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | DataAction | Microsoft.LogAnalytics/logs/NetworkMonitoring/read | Ler dados da tabela NetworkMonitoring |
> | DataAction | Microsoft.LogAnalytics/logs/OfficeActivity/read | Ler dados da tabela OfficeActivity |
> | DataAction | Microsoft.LogAnalytics/logs/Operation/read | Ler dados da tabela de Operação |
> | DataAction | Microsoft.LogAnalytics/logs/OutboundConnection/read | Ler dados da tabela OutboundConnection |
> | DataAction | Microsoft.LogAnalytics/logs/Perf/read | Ler dados da tabela de Desempenho |
> | DataAction | Microsoft.LogAnalytics/logs/ProtectionStatus/read | Ler dados da tabela ProtectionStatus |
> | Acção | Microsoft.LogAnalytics/logs/read | A ler dados de todos os seus registos |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedAzureCommonFields/read | Ler dados da tabela ReservedAzureCommonFields |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedCommonFields/read | Ler dados da tabela ReservedCommonFields |
> | DataAction | Microsoft.LogAnalytics/logs/SCCMAssessmentRecommendation/read | Ler dados da tabela SCCMAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SCOMAssessmentRecommendation/read | Ler dados da tabela SCOMAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityAlert/read | Ler dados da tabela SecurityAlert |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaseline/read | Ler dados da tabela SecurityBaseline |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaselineSummary/read | Ler dados da tabela SecurityBaselineSummary |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityDetection/read | Ler dados da tabela SecurityDetection |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityEvent/read | Ler dados da tabela SecurityEvent |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricOperationalEvent/read | Ler dados da tabela ServiceFabricOperationalEvent |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableActorEvent/read | Ler dados da tabela ServiceFabricReliableActorEvent |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableServiceEvent/read | Ler dados da tabela ServiceFabricReliableServiceEvent |
> | DataAction | Microsoft.LogAnalytics/logs/SfBAssessmentRecommendation/read | Ler dados da tabela SfBAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SfBOnlineAssessmentRecommendation/read | Ler dados da tabela SfBOnlineAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SharePointOnlineAssessmentRecommendation/read | Ler dados da tabela SharePointOnlineAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SigninLogs/read | Ler dados da tabela SigninLogs |
> | DataAction | Microsoft.LogAnalytics/logs/SPAssessmentRecommendation/read | Ler dados da tabela SPAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SQLAssessmentRecommendation/read | Ler dados da tabela SQLAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/SQLQueryPerformance/read | Ler dados da tabela SQLQueryPerformance |
> | DataAction | Microsoft.LogAnalytics/logs/Syslog/read | Ler dados da tabela Syslog |
> | DataAction | Microsoft.LogAnalytics/logs/SysmonEvent/read | Ler dados da tabela SysmonEvent |
> | DataAction | Microsoft.LogAnalytics/logs/Tables.Custom/read | A ler dados de registos personalizados |
> | DataAction | Microsoft.LogAnalytics/logs/UAApp/read | Ler dados da tabela UAApp |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputer/read | Ler dados da tabela UAComputer |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputerRank/read | Ler dados da tabela UAComputerRank |
> | DataAction | Microsoft.LogAnalytics/logs/UADriver/read | Ler dados da tabela UADriver |
> | DataAction | Microsoft.LogAnalytics/logs/UADriverProblemCodes/read | Ler dados da tabela UADriverProblemCodes |
> | DataAction | Microsoft.LogAnalytics/logs/UAFeedback/read | Ler dados da tabela UAFeedback |
> | DataAction | Microsoft.LogAnalytics/logs/UAHardwareSecurity/read | Ler dados da tabela UAHardwareSecurity |
> | DataAction | Microsoft.LogAnalytics/logs/UAIESiteDiscovery/read | Ler dados da tabela UAIESiteDiscovery |
> | DataAction | Microsoft.LogAnalytics/logs/UAOfficeAddIn/read | Ler dados da tabela UAOfficeAddIn |
> | DataAction | Microsoft.LogAnalytics/logs/UAProposedActionPlan/read | Ler dados da tabela UAProposedActionPlan |
> | DataAction | Microsoft.LogAnalytics/logs/UASysReqIssue/read | Ler dados da tabela UASysReqIssue |
> | DataAction | Microsoft.LogAnalytics/logs/UAUpgradedComputer/read | Ler dados da tabela UAUpgradedComputer |
> | DataAction | Microsoft.LogAnalytics/logs/Update/read | Ler dados da tabela de Atualização |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateRunProgress/read | Ler dados da tabela UpdateRunProgress |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateSummary/read | Ler dados da tabela UpdateSummary |
> | DataAction | Microsoft.LogAnalytics/logs/Usage/read | Ler dados da tabela de Utilização |
> | DataAction | Microsoft.LogAnalytics/logs/VMBoundPort/read | Ler dados da tabela VMBoundPort |
> | DataAction | Microsoft.LogAnalytics/logs/VMConnection/read | Ler dados da tabela VMConnection |
> | DataAction | Microsoft.LogAnalytics/logs/W3CIISLog/read | Ler dados da tabela W3CIISLog |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSDeploymentStatus/read | Ler dados da tabela WaaSDeploymentStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSInsiderStatus/read | Ler dados da tabela WaaSInsiderStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSUpdateStatus/read | Ler dados da tabela WaaSUpdateStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVStatus/read | Ler dados da tabela WDAVStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVThreat/read | Ler dados da tabela WDAVThreat |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsClientAssessmentRecommendation/read | Ler dados da tabela WindowsClientAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsEvent/read | Ler dados da tabela WindowsEvent |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsFirewall/read | Ler dados da tabela WindowsFirewall |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsServerAssessmentRecommendation/read | Ler dados da tabela WindowsServerAssessmentRecommendation |
> | DataAction | Microsoft.LogAnalytics/logs/WireData/read | Ler dados da tabela WireData |
> | DataAction | Microsoft.LogAnalytics/logs/WorkloadMonitoringPerf/read | Ler dados da tabela WorkloadMonitoringPerf |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOAggregatedStatus/read | Ler dados da tabela WUDOAggregatedStatus |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOStatus/read | Ler dados da tabela WUDOStatus |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Logic/integrationAccounts/agreements/delete | Elimina o acordo na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Obtém o URL de chamada de retorno para o conteúdo do acordo na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/agreements/read | Lê o acordo na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/agreements/write | Cria ou atualiza o acordo na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/assemblies/delete | Elimina a assemblagem na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Obtém o URL de chamada de retorno para o conteúdo da assemblagem na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/assemblies/read | Lê a assemblagem na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/assemblies/write | Cria ou atualiza a assemblagem na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Elimina a configuração do lote na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Lê a configuração do lote na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Cria ou atualiza a configuração de lote na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/certificates/delete | Elimina o certificado na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/certificates/read | Lê o certificado na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/certificates/write | Cria ou atualiza o certificado na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/delete | Elimina a conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/join/action | Adere à Conta de Integração. |
> | Acção | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Obtém o URL de chamada de retorno para a conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Obtém as chaves no cofre de chaves. |
> | Acção | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Regista os eventos de rastreio na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/maps/delete | Elimina o mapa na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Obtém o URL de chamada de retorno para o conteúdo do mapa na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/maps/read | Lê o mapa na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/maps/write | Cria ou atualiza o mapa na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/partners/delete | Elimina o parceiro na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Obtém o URL de chamada de retorno para o conteúdo de parceiro na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/partners/read | Lê o parceiro na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/partners/write | Cria ou atualiza o parceiro na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Lê as definições de registo da Conta de Integração. |
> | Acção | Microsoft.Logic/integrationAccounts/read | Lê a conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Regenera os segredos da chave de acesso. |
> | Acção | Microsoft.Logic/integrationAccounts/schemas/delete | Elimina o esquema na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Obtém o URL de chamada de retorno para o conteúdo do esquema na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/schemas/read | Lê o esquema na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/schemas/write | Cria ou atualiza o esquema na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/sessions/delete | Elimina a sessão na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/sessions/read | Lê a configuração do lote na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/sessions/write | Cria ou atualiza a sessão na conta de integração. |
> | Acção | Microsoft.Logic/integrationAccounts/write | Cria ou atualiza a conta de integração. |
> | Acção | Microsoft.Logic/integrationServiceEnvironments/delete | Elimina o ambiente do serviço de integração. |
> | Acção | Microsoft.Logic/integrationServiceEnvironments/join/action | Associa o Ambiente de Serviço de Integração. |
> | Acção | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Lê a operação da API gerida pelo ambiente de serviço de integração |
> | Acção | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Lê a API gerida pelo ambiente do serviço de integração. |
> | Acção | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Lê os estados da operação do ambiente de serviço de integração. |
> | Acção | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Lê as definições de métrica do ambiente de serviço de integração. |
> | Acção | Microsoft.Logic/integrationServiceEnvironments/read | Lê o ambiente do serviço de integração. |
> | Acção | Microsoft.Logic/integrationServiceEnvironments/write | Cria ou atualiza o ambiente do serviço de integração. |
> | Acção | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Obtém os grupos de operação recomendados do fluxo de trabalho. |
> | Acção | Microsoft.Logic/locations/workflows/validate/action | Valida o fluxo de trabalho. |
> | Acção | Microsoft.Logic/operations/read | Obtém a operação. |
> | Acção | Microsoft.Logic/register/action | Regista o fornecedor de recursos de Microsoft.Logic para uma determinada subscrição. |
> | Acção | Microsoft.Logic/workflows/accessKeys/delete | Elimina a chave de acesso. |
> | Acção | Microsoft.Logic/workflows/accessKeys/list/action | Lista os segredos da chave de acesso. |
> | Acção | Microsoft.Logic/workflows/accessKeys/read | Lê a chave de acesso. |
> | Acção | Microsoft.Logic/workflows/accessKeys/regenerate/action | Regenera os segredos da chave de acesso. |
> | Acção | Microsoft.Logic/workflows/accessKeys/write | Cria ou atualiza a chave de acesso. |
> | Acção | Microsoft.Logic/workflows/delete | Elimina o fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/disable/action | Desativa o fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/enable/action | Ativa o fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/listCallbackUrl/action | Obtém o URL de chamada de retorno do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/listSwagger/action | Obtém as definições do swagger do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/move/action | Move o Fluxo de trabalho do ID de subscrição, grupo de recursos e/ou nome existentes para um ID de subscrição, grupo de recursos e/ou nome diferentes. |
> | Acção | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Lê as definições de diagnóstico do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza as definições de diagnóstico do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Lê as definições de registo do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Lê as definições de métrica do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/read | Lê o fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/regenerateAccessKey/action | Regenera os segredos da chave de acesso. |
> | Acção | Microsoft.Logic/workflows/run/action | Inicia a execução do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Obtém os rastreios de expressão da ação de execução do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/runs/actions/read | Lê a ação de execução do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Obtém os rastreios de expressão da repetição da ação de execução do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/runs/actions/repetitions/read | Lê a repetição da ação de execução do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Lê o histórico de pedidos de ação de repetição da execução do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Lê o histórico de pedidos de ação de execução do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Lê a repetição do âmbito da ação de execução do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/runs/cancel/action | Cancela a execução de um fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/runs/delete | Elimina uma execução de um fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/runs/operations/read | Lê o estado da operação de execução do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/runs/read | Lê a execução do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/suspend/action | Suspende o fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/triggers/histories/read | Lê o histórico do acionador. |
> | Acção | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Submete novamente o acionador do fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Obtém o URL de chamada de retorno para o acionador. |
> | Acção | Microsoft.Logic/workflows/triggers/read | Lê o acionador. |
> | Acção | Microsoft.Logic/workflows/triggers/reset/action | Repõe o acionador. |
> | Acção | Microsoft.Logic/workflows/triggers/run/action | Executa o acionador. |
> | Acção | Microsoft.Logic/workflows/triggers/setState/action | Define o estado do acionador. |
> | Acção | Microsoft.Logic/workflows/validate/action | Valida o fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/versions/read | Lê a versão de fluxo de trabalho. |
> | Acção | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Obtém o URL de chamada de retorno para o acionador. |
> | Acção | Microsoft.Logic/workflows/write | Cria ou atualiza o fluxo de trabalho. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Mover qualquer associação de plano de compromisso de aprendizagem |
> | Acção | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Ler qualquer associação de plano de compromisso de aprendizagem |
> | Acção | Microsoft.MachineLearning/commitmentPlans/delete | Eliminar qualquer compromisso de plano de Aprendizado de máquina |
> | Acção | Microsoft.MachineLearning/commitmentPlans/join/action | Junte-se a qualquer máquina compromisso plano de aprendizagem |
> | Acção | Microsoft.MachineLearning/commitmentPlans/read | Ler qualquer compromisso de plano de Aprendizado de máquina |
> | Acção | Microsoft.MachineLearning/commitmentPlans/write | Criar ou atualizar qualquer plano de compromisso do Machine Learning |
> | Acção | Microsoft.MachineLearning/locations/operationresults/read | Obter o resultado de uma operação do Machine Learning |
> | Acção | Microsoft.MachineLearning/locations/operationsstatus/read | Obter o estado de uma operação de Aprendizado de máquina em curso |
> | Acção | Microsoft.MachineLearning/operations/read | Obter operações de aprendizagem automática |
> | Acção | Microsoft.MachineLearning/register/action | Regista a subscrição para a fornecedor de recursos do serviço web de aprendizagem automática e permite a criação de serviços da web. |
> | Acção | Microsoft.MachineLearning/skus/read | Obter SKUs de plano de compromisso de aprendizagem automática |
> | Acção | Microsoft.MachineLearning/webServices/action | Criar as propriedades do serviço Web regionais para regiões suportadas |
> | Acção | Microsoft.MachineLearning/webServices/delete | Eliminar qualquer serviço Web do Machine Learning |
> | Acção | Microsoft.MachineLearning/webServices/listkeys/read | Obter chaves para um serviço Web do Machine Learning |
> | Acção | Microsoft.MachineLearning/webServices/read | Ler qualquer serviço Web do Machine Learning |
> | Acção | Microsoft.MachineLearning/webServices/write | Criar ou atualizar qualquer serviço Web do Machine Learning |
> | Acção | Microsoft.MachineLearning/Workspaces/delete | Eliminar qualquer área de trabalho de aprendizagem |
> | Acção | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Listar chaves para uma área de trabalho do Machine Learning |
> | Acção | Microsoft.MachineLearning/Workspaces/read | Ler qualquer área de trabalho de aprendizagem |
> | Acção | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Ressincronizar chaves de conta de armazenamento configurada para uma área de trabalho do Machine Learning |
> | Acção | Microsoft.MachineLearning/Workspaces/write | Criar ou atualizar qualquer área de trabalho de aprendizagem |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Verifique se as atualizações estão disponíveis para os serviços de sistema para o cluster de operacionalização |
> | Acção | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Eliminar qualquer conta de alojamento |
> | Acção | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Listar as chaves associadas com cluster de operacionalização |
> | Acção | Microsoft.MachineLearningCompute/operationalizationClusters/read | Ler qualquer conta de alojamento |
> | Acção | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Atualizar os serviços do sistema num cluster de operacionalização |
> | Acção | Microsoft.MachineLearningCompute/operationalizationClusters/write | Criar ou atualizar qualquer conta de alojamento |
> | Acção | Microsoft.MachineLearningCompute/register/action | Regista o ID de subscrição para o fornecedor de recursos e permite a criação de uma máquina de recursos de computação de aprendizagem |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.MachineLearningModelManagement/accounts/delete | Eliminar qualquer conta de alojamento |
> | Acção | Microsoft.MachineLearningModelManagement/accounts/read | Ler qualquer conta de alojamento |
> | Acção | Microsoft.MachineLearningModelManagement/accounts/write | Criar ou atualizar qualquer conta de alojamento |
> | Acção | Microsoft.MachineLearningModelManagement/register/action | Regista o ID de subscrição para o fornecedor de recursos e permite a criação de uma conta de alojamento |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Obtém o estado de uma operação de computação específica |
> | Acção | Microsoft.MachineLearningServices/locations/usages/read | Relatório de utilização para aml os recursos numa subscrição de computação |
> | Acção | Microsoft.MachineLearningServices/locations/vmsizes/read | Obter os tamanhos de vm suportadas |
> | Acção | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Obtém o estado de uma operação de espaço de trabalho específico |
> | Acção | Microsoft.MachineLearningServices/register/action | Regista a subscrição para o fornecedor de recursos de serviços do Machine Learning |
> | Acção | Microsoft.MachineLearningServices/workspaces/computes/delete | Elimina os recursos de computação nas áreas de trabalho do Machine Learning Services |
> | Acção | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Listar segredos para recursos de computação na área de trabalho do Machine Learning Services |
> | Acção | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Lista de nós para o recurso de computação na área de trabalho do Machine Learning Services |
> | Acção | Microsoft.MachineLearningServices/workspaces/computes/read | Obtém os recursos de computação nas áreas de trabalho do Machine Learning Services |
> | Acção | Microsoft.MachineLearningServices/workspaces/computes/write | Cria ou atualiza os recursos de computação nas áreas de trabalho do Machine Learning Services |
> | Acção | Microsoft.MachineLearningServices/workspaces/delete | Elimina o Machine Learning Services áreas de trabalho |
> | Acção | Microsoft.MachineLearningServices/workspaces/listKeys/action | Lista os segredos para uma área de trabalho de serviços do Machine Learning |
> | Acção | Microsoft.MachineLearningServices/workspaces/read | Obtém o Machine Learning Services áreas de trabalho |
> | Acção | Microsoft.MachineLearningServices/workspaces/write | Cria ou atualiza a áreas de trabalho de serviços do Machine Learning |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ManagedIdentity/register/action | Regista a subscrição para o fornecedor de recursos de identidade gerida |
> | Acção | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Ação de RBAC para atribuir um utilizador existente a um recurso a identidade atribuída |
> | Acção | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Elimina um identidade atribuída ao utilizador existente |
> | Acção | Microsoft.ManagedIdentity/userAssignedIdentities/read | Obtém um identidade atribuída ao utilizador existente |
> | Acção | Microsoft.ManagedIdentity/userAssignedIdentities/write | Cria um nova identidade atribuída ao utilizador ou atualiza as marcas associadas com um identidade atribuída ao utilizador existente |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ManagedLab/labAccounts/CreateLab/action | Crie um laboratório numa conta do laboratório. |
> | Acção | Microsoft.ManagedLab/labAccounts/delete | Elimine contas de laboratório. |
> | Acção | Microsoft.ManagedLab/labAccounts/labs/delete | Elimine laboratórios. |
> | Acção | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Elimine definição do ambiente. |
> | Acção | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Elimine ambientes. |
> | Acção | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Ambientes de leitura. |
> | Acção | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Adiciona ou modifica os ambientes. |
> | Acção | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Ler definição de ambiente. |
> | Acção | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Adicionar ou modificar a definição do ambiente. |
> | Acção | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Elimine máquinas virtuais do laboratório. |
> | Acção | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Leia as máquinas virtuais do laboratório. |
> | Acção | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Adiciona ou modifica máquinas virtuais do laboratório. |
> | Acção | Microsoft.ManagedLab/labAccounts/labs/read | Laboratórios de leitura. |
> | Acção | Microsoft.ManagedLab/labAccounts/labs/write | Adicionar ou modificar laboratórios. |
> | Acção | Microsoft.ManagedLab/labAccounts/read | Leia as contas de laboratório. |
> | Acção | Microsoft.ManagedLab/labAccounts/write | Adiciona ou modifica contas de laboratório. |
> | Acção | Microsoft.ManagedLab/locations/operations/read | Operações de leitura. |
> | Acção | Microsoft.ManagedLab/register/action | Regista a subscrição |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Management/checkNameAvailability/action | Verifica se o nome do grupo de gestão especificado é válido e exclusivo. |
> | Acção | Microsoft.Management/getEntities/action | Liste todas as entidades (grupos de gestão, subscrições, etc.) para o usuário autenticado. |
> | Acção | Microsoft.Management/managementGroups/delete | Elimine grupo de gestão. |
> | Acção | Microsoft.Management/managementGroups/read | Lista os grupos de gestão para o usuário autenticado. |
> | Acção | Microsoft.Management/managementGroups/subscriptions/delete | Associa anular a subscrição do grupo de gestão. |
> | Acção | Microsoft.Management/managementGroups/subscriptions/write | Associa existente subscrição com o grupo de gestão. |
> | Acção | Microsoft.Management/managementGroups/write | Criar ou atualizar um grupo de gestão. |
> | Acção | Microsoft.Management/register/action | Registar a subscrição especificada com Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Concede acesso de leitura de dados para uma conta de mapas. |
> | Acção | Microsoft.Maps/accounts/delete | Elimine uma conta de mapas. |
> | Acção | Microsoft.Maps/accounts/eventGridFilters/delete | Eliminar um filtro do Event Grid |
> | Acção | Microsoft.Maps/accounts/eventGridFilters/read | Obter um filtro do Event Grid |
> | Acção | Microsoft.Maps/accounts/eventGridFilters/write | Criar ou atualizar um filtro do Event Grid |
> | Acção | Microsoft.Maps/accounts/listKeys/action | Listar chaves de conta de mapas |
> | Acção | Microsoft.Maps/accounts/read | Obter uma conta de mapas. |
> | Acção | Microsoft.Maps/accounts/regenerateKey/action | Gerar nova chave de principal ou secundário de conta de mapas |
> | Acção | Microsoft.Maps/accounts/write | Criar ou atualizar uma conta de mapas. |
> | Acção | Microsoft.Maps/operations/read | Leia as operações de fornecedor |
> | Acção | Microsoft.Maps/register/action | Registar o fornecedor |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Devolve um Contrato. |
> | Acção | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Aceita um contrato assinado. |
> | Acção | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importa uma imagem para o ACR de utilizador final. |
> | Acção | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Devolve uma configuração. |
> | Acção | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Guarda uma configuração. |
> | Acção | Microsoft.Marketplace/register/action | Regista o fornecedor de recursos Microsoft.Marketplace na subscrição. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.MarketplaceApps/ClassicDevServices/delete | Faz uma operação de eliminação num recurso de serviço de dev clássicos. |
> | Acção | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Obtém um dev clássicos chaves de gestão de recursos de serviço. |
> | Acção | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Obtém o início de sessão único na URL para um serviço de dev clássicos. |
> | Acção | Microsoft.MarketplaceApps/ClassicDevServices/read | Faz uma operação de obtenção de um serviço de dev clássicos. |
> | Acção | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Gera um chaves de gestão de recursos de serviço de dev clássicos. |
> | Acção | Microsoft.MarketplaceApps/Operations/read | Operações para todos os tipos de recursos de leitura. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Cancelar um acordo para um item do mercado especificado |
> | Acção | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Devolver um acordo para um item do mercado especificado |
> | Acção | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Assinar um contrato para um item do mercado especificado |
> | Acção | Microsoft.MarketplaceOrdering/agreements/read | Devolver todos os contratos em determinada subscrição |
> | Acção | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Obter um acordo para um item de máquina virtual do mercado especificado |
> | Acção | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Inicie sessão ou cancelar um acordo para um item de máquina virtual do mercado especificado |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Media/checknameavailability/action | Verifica se um nome de conta de Media Services está disponível |
> | Acção | Microsoft.Media/locations/checkNameAvailability/action | Verifica se um nome de conta de Media Services está disponível |
> | Acção | Microsoft.Media/mediaservices/accountfilters/delete | Eliminar qualquer filtro de conta |
> | Acção | Microsoft.Media/mediaservices/accountfilters/read | Ler qualquer filtro de conta |
> | Acção | Microsoft.Media/mediaservices/accountfilters/write | Criar ou atualizar qualquer filtro de conta |
> | Acção | Microsoft.Media/mediaservices/assets/assetfilters/delete | Eliminar qualquer filtro de elemento |
> | Acção | Microsoft.Media/mediaservices/assets/assetfilters/read | Ler qualquer filtro de elemento |
> | Acção | Microsoft.Media/mediaservices/assets/assetfilters/write | Criar ou atualizar qualquer filtro de elemento |
> | Acção | Microsoft.Media/mediaservices/assets/delete | Eliminar qualquer recurso |
> | Acção | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Obter chave de encriptação ativo |
> | Acção | Microsoft.Media/mediaservices/assets/listContainerSas/action | URLs SAS de contentor de elemento de lista |
> | Acção | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Lista os localizadores de transmissão em fluxo para o recurso |
> | Acção | Microsoft.Media/mediaservices/assets/read | Ler qualquer recurso |
> | Acção | Microsoft.Media/mediaservices/assets/write | Criar ou atualizar qualquer recurso |
> | Acção | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Eliminar qualquer política de chave de conteúdo |
> | Acção | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Obter as propriedades de política de segredos |
> | Acção | Microsoft.Media/mediaservices/contentKeyPolicies/read | Ler qualquer política de chave de conteúdo |
> | Acção | Microsoft.Media/mediaservices/contentKeyPolicies/write | Criar ou atualizar qualquer política de chave de conteúdo |
> | Acção | Microsoft.Media/mediaservices/delete | Eliminar qualquer conta de Media Services |
> | Acção | Microsoft.Media/mediaservices/eventGridFilters/delete | Eliminar qualquer filtro do Event Grid |
> | Acção | Microsoft.Media/mediaservices/eventGridFilters/read | Ler qualquer filtro do Event Grid |
> | Acção | Microsoft.Media/mediaservices/eventGridFilters/write | Criar ou atualizar qualquer filtro do Event Grid |
> | Acção | Microsoft.Media/mediaservices/liveEventOperations/read | Ler qualquer operação de evento em direto |
> | Acção | Microsoft.Media/mediaservices/liveEvents/delete | Eliminar qualquer evento em direto |
> | Acção | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Eliminar quaisquer dados em direto |
> | Acção | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Ler quaisquer dados em direto |
> | Acção | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Criar ou atualizar qualquer saída em direto |
> | Acção | Microsoft.Media/mediaservices/liveEvents/read | Ler qualquer evento em direto |
> | Acção | Microsoft.Media/mediaservices/liveEvents/reset/action | Repor qualquer operação de evento em direto |
> | Acção | Microsoft.Media/mediaservices/liveEvents/start/action | Iniciar a qualquer operação de evento em direto |
> | Acção | Microsoft.Media/mediaservices/liveEvents/stop/action | Parar qualquer operação de evento em direto |
> | Acção | Microsoft.Media/mediaservices/liveEvents/write | Criar ou atualizar qualquer evento em direto |
> | Acção | Microsoft.Media/mediaservices/liveOutputOperations/read | Ler qualquer operação de saída em direto |
> | Acção | Microsoft.Media/mediaservices/read | Ler qualquer conta de Media Services |
> | Acção | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Ler qualquer operação de ponto final de transmissão em fluxo |
> | Acção | Microsoft.Media/mediaservices/streamingEndpoints/delete | Eliminar qualquer ponto de final de transmissão em fluxo |
> | Acção | Microsoft.Media/mediaservices/streamingEndpoints/read | Ler qualquer ponto de final de transmissão em fluxo |
> | Acção | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Dimensionar qualquer operação de ponto final de transmissão em fluxo |
> | Acção | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Iniciar qualquer operação de ponto final de transmissão em fluxo |
> | Acção | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Parar qualquer operação de ponto final de transmissão em fluxo |
> | Acção | Microsoft.Media/mediaservices/streamingEndpoints/write | Criar ou atualizar qualquer ponto de final de transmissão em fluxo |
> | Acção | Microsoft.Media/mediaservices/streamingLocators/delete | Eliminar qualquer localizador de transmissão em fluxo |
> | Acção | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Listar chaves de conteúdo |
> | Acção | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Caminhos de lista |
> | Acção | Microsoft.Media/mediaservices/streamingLocators/read | Ler qualquer localizador de transmissão em fluxo |
> | Acção | Microsoft.Media/mediaservices/streamingLocators/write | Criar ou atualizar qualquer localizador de transmissão em fluxo |
> | Acção | Microsoft.Media/mediaservices/streamingPolicies/delete | Eliminar qualquer política de transmissão em fluxo |
> | Acção | Microsoft.Media/mediaservices/streamingPolicies/read | Ler qualquer política de transmissão em fluxo |
> | Acção | Microsoft.Media/mediaservices/streamingPolicies/write | Criar ou atualizar qualquer política de transmissão em fluxo |
> | Acção | Microsoft.Media/mediaservices/syncStorageKeys/action | Sincronizar as chaves de armazenamento para uma conta de armazenamento do Azure anexada |
> | Acção | Microsoft.Media/mediaservices/transforms/delete | Eliminar qualquer transformação |
> | Acção | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Cancelar Tarefa |
> | Acção | Microsoft.Media/mediaservices/transforms/jobs/delete | Eliminar todas as tarefas |
> | Acção | Microsoft.Media/mediaservices/transforms/jobs/read | Ler qualquer tarefa |
> | Acção | Microsoft.Media/mediaservices/transforms/jobs/write | Criar ou atualizar todas as tarefas |
> | Acção | Microsoft.Media/mediaservices/transforms/read | Ler qualquer transformação |
> | Acção | Microsoft.Media/mediaservices/transforms/write | Criar ou atualizar qualquer transformação |
> | Acção | Microsoft.Media/mediaservices/write | Criar ou atualizar qualquer conta de Media Services |
> | Acção | Microsoft.Media/operations/read | Obter operações disponíveis |
> | Acção | Microsoft.Media/register/action | Regista a subscrição para o fornecedor de recursos de serviços de multimédia e permite a criação de contas de serviços de multimédia |
> | Acção | Microsoft.Media/unregister/action | Anula o registo da subscrição para o fornecedor de recursos de serviços de multimédia |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Migrate/locations/assessmentOptions/read | Obtém as opções de avaliação que estão disponíveis na localização especificada |
> | Acção | Microsoft.Migrate/locations/checknameavailability/action | Verifica a disponibilidade do nome do recurso para a subscrição fornecida na localização especificada |
> | Acção | Microsoft.Migrate/migrateprojects/read | Obtém as propriedades do projeto de migração. |
> | Acção | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Obtém a configuração da solução do projeto de migração |
> | Acção | Microsoft.Migrate/migrateprojects/solutions/read | Obtém as propriedades da solução do projeto de migração. |
> | Acção | Microsoft.Migrate/Operations/read | Lista as operações disponíveis no fornecedor de recursos Microsoft.Migrate |
> | Acção | Microsoft.Migrate/projects/assessments/read | Lista avaliações dentro de um projeto |
> | Acção | Microsoft.Migrate/projects/delete | Elimina o projeto |
> | Acção | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Obter as propriedades de uma máquina avaliada |
> | Acção | Microsoft.Migrate/projects/groups/assessments/delete | Elimina uma avaliação |
> | Acção | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Transfere um URL de um relatório de avaliação |
> | Acção | Microsoft.Migrate/projects/groups/assessments/read | Obtém as propriedades de uma avaliação |
> | Acção | Microsoft.Migrate/projects/groups/assessments/write | Cria uma nova avaliação ou atualiza uma existente |
> | Acção | Microsoft.Migrate/projects/groups/delete | Elimina um grupo |
> | Acção | Microsoft.Migrate/projects/groups/read | Obter as propriedades de um grupo |
> | Acção | Microsoft.Migrate/projects/groups/write | Cria um novo grupo ou atualiza um grupo existente |
> | Acção | Microsoft.Migrate/projects/keys/action | Obtém as chaves partilhadas para o projeto |
> | Acção | Microsoft.Migrate/projects/machines/read | Obter as propriedades de um computador |
> | Acção | Microsoft.Migrate/projects/read | Obtém as propriedades de um projeto |
> | Acção | Microsoft.Migrate/projects/write | Cria um novo projeto ou atualiza um existente |
> | Acção | Microsoft.Migrate/register/action | Regista a Subscrição no fornecedor de recursos Microsoft.Migrate |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.MixedReality/register/action | Regista uma subscrição para o fornecedor de recursos de realidade mista. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Eliminar as âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Detetar próximas âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obter propriedades de âncoras espaciais |
> | Acção | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico para Microsoft.MixedReality/SpatialAnchorsAccounts |
> | Acção | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição de diagnóstico para Microsoft.MixedReality/SpatialAnchorsAccounts |
> | Acção | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para Microsoft.MixedReality/SpatialAnchorsAccounts |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localize as âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Atualizar propriedades de âncoras espaciais |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.NetApp/locations/operationresults/read | Lê um recurso de resultado da operação. |
> | Acção | Microsoft.NetApp/locations/read | Leituras uma disponibilidade verifique recursos. |
> | Acção | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Elimina um recurso de conjunto. |
> | Acção | Microsoft.NetApp/netAppAccounts/capacityPools/read | Lê um recurso de conjunto. |
> | Acção | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Elimina um recurso de volume. |
> | Acção | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Lê um recurso de destino de montagem. |
> | Acção | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Lê um recurso de volume. |
> | Acção | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Elimina um recurso de instantâneo. |
> | Acção | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Lê um recurso de instantâneo. |
> | Acção | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Escreve um recurso de instantâneo. |
> | Acção | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Escreve um recurso de volume. |
> | Acção | Microsoft.NetApp/netAppAccounts/capacityPools/write | Escreve um recurso de conjunto. |
> | Acção | Microsoft.NetApp/netAppAccounts/delete | Elimina um recurso de conta. |
> | Acção | Microsoft.NetApp/netAppAccounts/read | Lê um recurso de conta. |
> | Acção | Microsoft.NetApp/netAppAccounts/write | Escreve um recurso de conta. |
> | Acção | Microsoft.NetApp/Operations/read | Lê uma operação de recursos. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Obter o Gateway de aplicação disponíveis cabeçalhos de pedido |
> | Acção | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Obter o cabeçalho de resposta do Gateway de aplicação disponíveis |
> | Acção | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Obter as variáveis de servidor disponíveis do Gateway de aplicação |
> | Acção | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Ssl de Gateway de aplicação predefinidas política |
> | Acção | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Opções de Ssl de disponíveis de Gateway de aplicação |
> | Acção | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Obtém conjuntos de regras disponível do Waf de Gateway de aplicação |
> | Acção | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Associa um conjunto de endereços de back-end de gateway de aplicação. Não. o alerta. |
> | Acção | Microsoft.Network/applicationGateways/backendhealth/action | Obtém um Estado de funcionamento de back-end de gateway de aplicação |
> | Acção | Microsoft.Network/applicationGateways/delete | Elimina um gateway de aplicação |
> | Acção | Microsoft.Network/applicationGateways/read | Obtém um gateway de aplicação |
> | Acção | Microsoft.Network/applicationGateways/start/action | Inicia um gateway de aplicação |
> | Acção | Microsoft.Network/applicationGateways/stop/action | Interrompe um gateway de aplicação |
> | Acção | Microsoft.Network/applicationGateways/write | Cria um gateway de aplicação ou atualiza um gateway de aplicação |
> | Acção | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Elimina uma política de WAF do Gateway de aplicação |
> | Acção | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Obtém uma política de WAF do Gateway de aplicação |
> | Acção | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Cria uma política de WAF do Gateway de aplicação ou atualiza uma política de WAF do Gateway de aplicação |
> | Acção | Microsoft.Network/applicationSecurityGroups/delete | Elimina um grupo de segurança de aplicações |
> | Acção | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Associa uma configuração de IP para grupos de segurança do aplicativo. Não. o alerta. |
> | Acção | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Associa uma regra de segurança para grupos de segurança do aplicativo. Não. o alerta. |
> | Acção | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Listas de configurações de IP no ApplicationSecurityGroup |
> | Acção | Microsoft.Network/applicationSecurityGroups/read | Obtém um ID de grupo de segurança de aplicações. |
> | Acção | Microsoft.Network/applicationSecurityGroups/write | Cria um grupo de segurança de aplicações ou atualiza um grupo de segurança de aplicação existente. |
> | Acção | Microsoft.Network/azureFirewallFqdnTags/read | Obtém as etiquetas FQDN de Firewall do Azure |
> | Acção | Microsoft.Network/azurefirewalls/delete | Eliminar a Firewall do Azure |
> | Acção | Microsoft.Network/azurefirewalls/read | Obter o Firewall do Azure |
> | Acção | Microsoft.Network/azurefirewalls/write | Cria ou atualiza uma Firewall do Azure |
> | Acção | Microsoft.Network/bastionHosts/delete | Elimina o anfitrião de bastião |
> | Acção | Microsoft.Network/bastionHosts/read | Obtém o anfitrião de bastião |
> | Acção | Microsoft.Network/bastionHosts/write | Criar ou atualizar o anfitrião de bastião |
> | Acção | Microsoft.Network/bgpServiceCommunities/read | Obter Comunidades de Bgp de serviço |
> | Acção | Microsoft.Network/checkFrontDoorNameAvailability/action | Verifica se um nome de porta de entrada está disponível |
> | Acção | Microsoft.Network/checkTrafficManagerNameAvailability/action | Verifica a disponibilidade de um nome DNS do Gestor de tráfego relativo. |
> | Acção | Microsoft.Network/connections/delete | Elimina o VirtualNetworkGatewayConnection |
> | Acção | Microsoft.Network/connections/read | Obtém o VirtualNetworkGatewayConnection |
> | Acção | Microsoft.Network/connections/revoke/action | Marca um Estado de ligação de Expressroute como revogadas |
> | Acção | Microsoft.Network/connections/sharedkey/action | Obter VirtualNetworkGatewayConnection SharedKey |
> | Acção | Microsoft.Network/connections/sharedKey/read | Obtém o VirtualNetworkGatewayConnection SharedKey |
> | Acção | Microsoft.Network/connections/sharedKey/write | Cria ou atualiza uma existente SharedKey de VirtualNetworkGatewayConnection |
> | Acção | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Obtém a configuração do dispositivo de Vpn de VirtualNetworkGatewayConnection |
> | Acção | Microsoft.Network/connections/write | Cria ou atualiza uma existente VirtualNetworkGatewayConnection |
> | Acção | Microsoft.Network/ddosCustomPolicies/delete | Elimina um DDoS de política personalizada |
> | Acção | Microsoft.Network/ddosCustomPolicies/read | Obtém um DDoS personalizados a definição de definição de política |
> | Acção | Microsoft.Network/ddosCustomPolicies/write | Cria uma política personalizada de DDoS ou atualiza uma política de DDoS personalizado existente |
> | Acção | Microsoft.Network/ddosProtectionPlans/delete | Elimina um plano de proteção DDoS |
> | Acção | Microsoft.Network/ddosProtectionPlans/join/action | Associa um plano de proteção contra DDoS. Não. o alerta. |
> | Acção | Microsoft.Network/ddosProtectionPlans/read | Obtém um plano de proteção DDoS |
> | Acção | Microsoft.Network/ddosProtectionPlans/write | Cria um plano de proteção de DDoS ou atualiza um plano de proteção de DDoS  |
> | Acção | Microsoft.Network/dnsoperationresults/read | Obtém os resultados de uma operação de DNS |
> | Acção | Microsoft.Network/dnsoperationstatuses/read | Obtém o estado de uma operação de DNS  |
> | Acção | Microsoft.Network/dnszones/A/delete | Remover o conjunto de registos de um determinado nome e tipo A partir de uma zona DNS. |
> | Acção | Microsoft.Network/dnszones/A/read | Obtenha o conjunto de registos do tipo "A", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o valor de TTL, marcas e etag. |
> | Acção | Microsoft.Network/dnszones/A/write | Criar ou atualizar um conjunto de registos do tipo "A" dentro de uma zona DNS. Os registos especificados substituirá os atuais registos no conjunto de registos. |
> | Acção | Microsoft.Network/dnszones/AAAA/delete | Remover o conjunto de registos de um determinado nome e tipo 'AAAA' de uma zona DNS. |
> | Acção | Microsoft.Network/dnszones/AAAA/read | Obtenha o conjunto de registos do tipo 'AAAA', no formato JSON. O conjunto de registos contém uma lista de registos, bem como o valor de TTL, marcas e etag. |
> | Acção | Microsoft.Network/dnszones/AAAA/write | Criar ou atualizar um conjunto de registos do tipo 'AAAA' dentro de uma zona DNS. Os registos especificados substituirá os atuais registos no conjunto de registos. |
> | Acção | Microsoft.Network/dnszones/all/read | Obtém conjuntos de registos DNS em todos os tipos |
> | Acção | Microsoft.Network/dnszones/CAA/delete | Remover o conjunto de registos de um determinado nome e tipo CAA de uma zona DNS. |
> | Acção | Microsoft.Network/dnszones/CAA/read | Obtenha o conjunto de registos do tipo "CAA", no formato JSON. O conjunto de registos contém o valor de TTL, marcas e etag. |
> | Acção | Microsoft.Network/dnszones/CAA/write | Criar ou atualizar um conjunto de registos do tipo "CAA" dentro de uma zona DNS. Os registos especificados substituirá os atuais registos no conjunto de registos. |
> | Acção | Microsoft.Network/dnszones/CNAME/delete | Remover o conjunto de registos de um determinado nome e tipo CNAME de uma zona DNS. |
> | Acção | Microsoft.Network/dnszones/CNAME/read | Obtenha o conjunto de registos do tipo "CNAME", no formato JSON. O conjunto de registos contém o valor de TTL, marcas e etag. |
> | Acção | Microsoft.Network/dnszones/CNAME/write | Criar ou atualizar um conjunto de registos do tipo "CNAME" dentro de uma zona DNS. Os registos especificados substituirá os atuais registos no conjunto de registos. |
> | Acção | Microsoft.Network/dnszones/delete | Elimine a zona DNS, no formato JSON. As propriedades de zona incluem marcas, etag, numberOfRecordSets e maxNumberOfRecordSets. |
> | Acção | Microsoft.Network/dnszones/MX/delete | Remover o conjunto de registos de um determinado nome e tipo "MX" de uma zona DNS. |
> | Acção | Microsoft.Network/dnszones/MX/read | Obtenha o conjunto de registos do tipo "MX", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o valor de TTL, marcas e etag. |
> | Acção | Microsoft.Network/dnszones/MX/write | Criar ou atualizar um conjunto de registos do tipo "MX" dentro de uma zona DNS. Os registos especificados substituirá os atuais registos no conjunto de registos. |
> | Acção | Microsoft.Network/dnszones/NS/delete | Elimina o conjunto de registos de DNS do tipo NS |
> | Acção | Microsoft.Network/dnszones/NS/read | Obtém o conjunto de registos de DNS do tipo NS |
> | Acção | Microsoft.Network/dnszones/NS/write | Cria ou atualiza o conjunto de registos de DNS do tipo NS |
> | Acção | Microsoft.Network/dnszones/PTR/delete | Remover o conjunto de registos de um determinado nome e tipo PTR de uma zona DNS. |
> | Acção | Microsoft.Network/dnszones/PTR/read | Obtenha o conjunto de registos do tipo "PTR", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o valor de TTL, marcas e etag. |
> | Acção | Microsoft.Network/dnszones/PTR/write | Criar ou atualizar um conjunto de registos do tipo "PTR" dentro de uma zona DNS. Os registos especificados substituirá os atuais registos no conjunto de registos. |
> | Acção | Microsoft.Network/dnszones/read | Obtenha a zona DNS, no formato JSON. As propriedades de zona incluem marcas, etag, numberOfRecordSets e maxNumberOfRecordSets. Tenha em atenção que este comando não obter conjuntos de registos contidos na zona. |
> | Acção | Microsoft.Network/dnszones/recordsets/read | Obtém conjuntos de registos DNS em todos os tipos |
> | Acção | Microsoft.Network/dnszones/SOA/read | Obtém o conjunto de registos de DNS do tipo SOA |
> | Acção | Microsoft.Network/dnszones/SOA/write | Cria ou atualiza o conjunto de registos de DNS do tipo SOA |
> | Acção | Microsoft.Network/dnszones/SRV/delete | Remover o conjunto de registos de um determinado nome e tipo SRV de uma zona DNS. |
> | Acção | Microsoft.Network/dnszones/SRV/read | Obtenha o conjunto de registos do tipo "SRV", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o valor de TTL, marcas e etag. |
> | Acção | Microsoft.Network/dnszones/SRV/write | Criar ou atualizar conjunto de registos do tipo SRV |
> | Acção | Microsoft.Network/dnszones/TXT/delete | Remover o conjunto de registos de um determinado nome e tipo TXT de uma zona DNS. |
> | Acção | Microsoft.Network/dnszones/TXT/read | Obtenha o conjunto de registos do tipo "TXT", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o valor de TTL, marcas e etag. |
> | Acção | Microsoft.Network/dnszones/TXT/write | Criar ou atualizar um conjunto de registos do tipo "TXT" dentro de uma zona DNS. Os registos especificados substituirá os atuais registos no conjunto de registos. |
> | Acção | Microsoft.Network/dnszones/write | Criar ou atualizar uma zona DNS dentro de um grupo de recursos.  Utilizado para atualizar as etiquetas num recurso de zona DNS. Tenha em atenção que este comando não pode ser utilizado para criar ou atualizar conjuntos de registos na zona. |
> | Acção | Microsoft.Network/expressRouteCircuits/authorizations/delete | Elimina uma autorização de circuito do Expressroute |
> | Acção | Microsoft.Network/expressRouteCircuits/authorizations/read | Obtém uma autorização de circuito do Expressroute |
> | Acção | Microsoft.Network/expressRouteCircuits/authorizations/write | Cria ou atualiza uma autorização de circuito do Expressroute existente |
> | Acção | Microsoft.Network/expressRouteCircuits/delete | Elimina um circuito do Expressroute |
> | Acção | Microsoft.Network/expressRouteCircuits/join/action | Associa um circuito do Expressroute. Não. o alerta. |
> | Acção | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Obtém um ArpTable de Peering do circuito do Expressroute |
> | Acção | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Elimina uma ligação do circuito do Expressroute |
> | Acção | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Obtém uma ligação do circuito do Expressroute |
> | Acção | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Cria ou atualiza um recurso de ligação do circuito do Expressroute existente |
> | Acção | Microsoft.Network/expressRouteCircuits/peerings/delete | Elimina um Peering de circuito do Expressroute |
> | Acção | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Obtém a ligação do circuito Express Route de ponto a ponto |
> | Acção | Microsoft.Network/expressRouteCircuits/peerings/read | Obtém um Peering de circuito do Expressroute |
> | Acção | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Obtém um RouteTable de Peering do circuito do Expressroute |
> | Acção | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Obtém um resumo de RouteTable do Peering de circuito do Expressroute |
> | Acção | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Obtém um Peering de circuito do Expressroute Stat |
> | Acção | Microsoft.Network/expressRouteCircuits/peerings/write | Cria ou atualiza um Peering de circuito do Expressroute existente |
> | Acção | Microsoft.Network/expressRouteCircuits/read | Obter um circuito do Expressroute |
> | Acção | Microsoft.Network/expressRouteCircuits/stats/read | Obtém uma estatística de circuito do Expressroute |
> | Acção | Microsoft.Network/expressRouteCircuits/write | Cria ou atualiza um circuito do Expressroute existente |
> | Acção | Microsoft.Network/expressRouteCrossConnections/join/action | Associações uma via expressa cruzam ligação. Não. o alerta. |
> | Acção | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Obtém uma via expressa entre a tabela de Arp Peering da ligação |
> | Acção | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Elimina uma via expressa cruzada ligação Peering |
> | Acção | Microsoft.Network/expressRouteCrossConnections/peerings/read | Obtém uma via expressa cruzada ligação Peering |
> | Acção | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Obtém uma via expressa cruzada ligação Peering da tabela de rotas |
> | Acção | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Obtém uma via expressa cruzada resumo de tabela da rota de Peering da ligação |
> | Acção | Microsoft.Network/expressRouteCrossConnections/peerings/write | Cria um Cross ligação Peering do Expressroute ou atualiza uma existente em várias ligação Peering do Expressroute |
> | Acção | Microsoft.Network/expressRouteCrossConnections/read | Obter o Express Route cruzada de ligação |
> | Acção | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Elimina uma ligação de Expressroute |
> | Acção | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Obtém uma ligação de Expressroute |
> | Acção | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Cria uma ligação Expressroute ou atualiza uma existente ligação de Expressroute |
> | Acção | Microsoft.Network/expressRouteGateways/join/action | Associa um Gateway do Expressroute. Não. o alerta. |
> | Acção | Microsoft.Network/expressRouteGateways/read | Obter o Gateway do Express Route |
> | Acção | Microsoft.Network/expressRoutePorts/delete | Elimina ExpressRoutePorts |
> | Acção | Microsoft.Network/expressRoutePorts/join/action | Associa as portas de Express Route. Não. o alerta. |
> | Acção | Microsoft.Network/expressRoutePorts/links/read | Obtém ExpressRouteLink |
> | Acção | Microsoft.Network/expressRoutePorts/read | Obtém ExpressRoutePorts |
> | Acção | Microsoft.Network/expressRoutePorts/write | Cria ou atualiza ExpressRoutePorts |
> | Acção | Microsoft.Network/expressRoutePortsLocations/read | Get Express Route portas locais |
> | Acção | Microsoft.Network/expressRouteServiceProviders/read | Obtém os fornecedores de serviços de Express Route |
> | Acção | Microsoft.Network/frontDoors/backendPools/delete | Elimina um conjunto de back-end |
> | Acção | Microsoft.Network/frontDoors/backendPools/read | Obtém um conjunto de back-end |
> | Acção | Microsoft.Network/frontDoors/backendPools/write | Cria ou atualiza um conjunto de back-end |
> | Acção | Microsoft.Network/frontDoors/delete | Elimina uma porta de entrada |
> | Acção | Microsoft.Network/frontDoors/frontendEndpoints/delete | Elimina um ponto de extremidade de front-end |
> | Acção | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Desativa o HTTPS num ponto de extremidade de front-end |
> | Acção | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Permite que o HTTPS num ponto de extremidade de front-end |
> | Acção | Microsoft.Network/frontDoors/frontendEndpoints/read | Obtém um ponto de extremidade de front-end |
> | Acção | Microsoft.Network/frontDoors/frontendEndpoints/write | Cria ou atualiza um ponto de extremidade de front-end |
> | Acção | Microsoft.Network/frontDoors/healthProbeSettings/delete | Elimina as definições de sonda de estado de funcionamento |
> | Acção | Microsoft.Network/frontDoors/healthProbeSettings/read | Obtém as definições de sonda de estado de funcionamento |
> | Acção | Microsoft.Network/frontDoors/healthProbeSettings/write | Cria ou atualiza as definições de sonda de estado de funcionamento |
> | Acção | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Cria ou atualiza as definições de balanceamento de carga |
> | Acção | Microsoft.Network/frontDoors/loadBalancingSettings/read | Obtém o balanceamento de carga definições |
> | Acção | Microsoft.Network/frontDoors/loadBalancingSettings/write | Cria ou atualiza as definições de balanceamento de carga |
> | Acção | Microsoft.Network/frontDoors/purge/action | Limpar o conteúdo em cache a partir de uma porta de entrada |
> | Acção | Microsoft.Network/frontDoors/read | Obtém uma porta de entrada |
> | Acção | Microsoft.Network/frontDoors/routingRules/delete | Elimina uma regra de encaminhamento |
> | Acção | Microsoft.Network/frontDoors/routingRules/read | Obtém uma regra de encaminhamento |
> | Acção | Microsoft.Network/frontDoors/routingRules/write | Cria ou atualiza uma regra de encaminhamento |
> | Acção | Microsoft.Network/frontDoors/validateCustomDomain/action | Valida um ponto de extremidade de front-end para uma porta de entrada |
> | Acção | Microsoft.Network/frontDoors/write | Cria ou atualiza uma porta de entrada |
> | Acção | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Elimina uma política de Firewall de aplicações Web |
> | Acção | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Obtém uma política de Firewall de aplicações Web |
> | Acção | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Cria ou atualiza uma política de Firewall de aplicações Web |
> | Acção | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Associa um conjunto de endereços de back-end de Balanceador de carga. Não. o alerta. |
> | Acção | Microsoft.Network/loadBalancers/backendAddressPools/read | Obtém uma definição de conjunto de endereços de back-end de Balanceador de carga |
> | Acção | Microsoft.Network/loadBalancers/delete | Elimina um balanceador de carga |
> | Acção | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Associa uma configuração de IP de front-end de Balanceador de carga. Não. o alerta. |
> | Acção | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Obtém uma definição de configuração de IP de front-end de Balanceador de carga |
> | Acção | Microsoft.Network/loadBalancers/inboundNatPools/join/action | É associado um balanceador de carga conjunto NAT de entrada. Não. o alerta. |
> | Acção | Microsoft.Network/loadBalancers/inboundNatPools/read | Obtém um balanceador de carga a definição do conjunto nat de entrada |
> | Acção | Microsoft.Network/loadBalancers/inboundNatRules/delete | Elimina uma regra de nat de entrada do Balanceador de carga |
> | Acção | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Associa uma regra de nat de entrada do Balanceador de carga. Não. o alerta. |
> | Acção | Microsoft.Network/loadBalancers/inboundNatRules/read | Obtém um balanceador de carga definição da regra nat de entrada |
> | Acção | Microsoft.Network/loadBalancers/inboundNatRules/write | Cria uma regra de nat de entrada do Balanceador de carga ou atualiza uma regra nat de entrada do Balanceador de carga existente |
> | Acção | Microsoft.Network/loadBalancers/loadBalancingRules/read | Obtém uma definição de regra balanceamento do carregamento Balanceador de carga |
> | Acção | Microsoft.Network/loadBalancers/networkInterfaces/read | Obtém as referências a todas as interfaces de rede num Balanceador de carga |
> | Acção | Microsoft.Network/loadBalancers/outboundRules/read | Obtém uma definição de regra de saída do Balanceador de carga |
> | Acção | Microsoft.Network/loadBalancers/probes/join/action | Permite o uso de sondas do Balanceador de carga. Por exemplo, com esta propriedade healthProbe de permissão de dimensionamento de VMS conjunto pode referenciar a sonda. Não. o alerta. |
> | Acção | Microsoft.Network/loadBalancers/probes/read | Obtém uma sonda de Balanceador de carga |
> | Acção | Microsoft.Network/loadBalancers/read | Obtém uma definição de Balanceador de carga |
> | Acção | Microsoft.Network/loadBalancers/virtualMachines/read | Obtém as referências a todas as máquinas virtuais num Balanceador de carga |
> | Acção | Microsoft.Network/loadBalancers/write | Cria um balanceador de carga ou atualiza um balanceador de carga existente |
> | Acção | Microsoft.Network/localnetworkgateways/delete | Elimina o LocalNetworkGateway |
> | Acção | Microsoft.Network/localnetworkgateways/read | Obtém o LocalNetworkGateway |
> | Acção | Microsoft.Network/localnetworkgateways/write | Cria ou atualiza um LocalNetworkGateway existente |
> | Acção | Microsoft.Network/locations/availableDelegations/read | Obtém as delegações disponíveis |
> | Acção | Microsoft.Network/locations/availablePrivateEndpointResources/read | Obtém o ponto final privado disponíveis recursos |
> | Acção | Microsoft.Network/locations/bareMetalTenants/action | Aloca ou valida um inquilino do bare-Metal |
> | Acção | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Verifica o suporte de redes aceleradas |
> | Acção | Microsoft.Network/locations/checkDnsNameAvailability/read | Verifica se a etiqueta de dns está disponível na localização especificada |
> | Acção | Microsoft.Network/locations/operationResults/read | Obtém o resultado da operação de um async mensagem ou operação de eliminação |
> | Acção | Microsoft.Network/locations/operations/read | Obtém o recurso de operação que representa o estado de uma operação assíncrona |
> | Acção | Microsoft.Network/locations/serviceTags/read | Obter etiquetas de serviço |
> | Acção | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Obtém suporte tamanhos de máquinas virtuais |
> | Acção | Microsoft.Network/locations/usages/read | Obtém os recursos de métrica de utilização |
> | Acção | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Obtém uma lista de serviços de ponto final de rede virtuais disponíveis |
> | Acção | Microsoft.Network/networkIntentPolicies/delete | Elimina uma política de intenção de rede |
> | Acção | Microsoft.Network/networkIntentPolicies/read | Obtém uma descrição da intenção da política de rede |
> | Acção | Microsoft.Network/networkIntentPolicies/write | Cria uma política de intenção de rede ou atualiza uma política de intenção de rede existente |
> | Acção | Microsoft.Network/networkInterfaces/delete | Elimina uma interface de rede |
> | Acção | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Obter grupos de segurança de rede configurado na rede Interface da Vm |
> | Acção | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Obter a tabela de rotas configuradas na Interface de rede da Vm |
> | Acção | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Associa uma configuração de IP da Interface de rede. Não. o alerta. |
> | Acção | Microsoft.Network/networkInterfaces/ipconfigurations/read | Obtém uma definição de configuração de ip de interface de rede.  |
> | Acção | Microsoft.Network/networkInterfaces/join/action | Associa uma Máquina Virtual a uma interface de rede. Não. o alerta. |
> | Acção | Microsoft.Network/networkInterfaces/loadBalancers/read | Obtém todos os balanceadores de carga que a interface de rede faz parte do |
> | Acção | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Acção | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Elimina uma configuração de toque de Interface de rede. |
> | Acção | Microsoft.Network/networkInterfaces/tapConfigurations/read | Obtém uma configuração de toque de Interface de rede. |
> | Acção | Microsoft.Network/networkInterfaces/tapConfigurations/write | Cria uma configuração de toque de Interface de rede ou atualiza uma Interface de toque em configuração da rede existente. |
> | Acção | Microsoft.Network/networkInterfaces/write | Cria uma interface de rede ou atualiza uma interface de rede existente.  |
> | Acção | Microsoft.Network/networkProfiles/delete | Elimina um perfil de rede |
> | Acção | Microsoft.Network/networkProfiles/read | Obtém um perfil de rede |
> | Acção | Microsoft.Network/networkProfiles/removeContainers/action | Remove os contentores |
> | Acção | Microsoft.Network/networkProfiles/setContainers/action | Contentores de conjuntos |
> | Acção | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Define as Interfaces de rede do contentor |
> | Acção | Microsoft.Network/networkProfiles/write | Cria ou atualiza um perfil de rede |
> | Acção | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Obtém uma definição de regra de segurança padrão |
> | Acção | Microsoft.Network/networkSecurityGroups/delete | Elimina um grupo de segurança de rede |
> | Acção | Microsoft.Network/networkSecurityGroups/join/action | Associa um grupo de segurança de rede. Não. o alerta. |
> | Acção | Microsoft.Network/networkSecurityGroups/read | Obtém uma definição de grupo de segurança de rede |
> | Acção | Microsoft.Network/networkSecurityGroups/securityRules/delete | Elimina uma regra de segurança |
> | Acção | Microsoft.Network/networkSecurityGroups/securityRules/read | Obtém uma definição de regra de segurança |
> | Acção | Microsoft.Network/networkSecurityGroups/securityRules/write | Cria uma regra de segurança ou atualiza uma regra de segurança existente |
> | Acção | Microsoft.Network/networkSecurityGroups/write | Cria um grupo de segurança de rede ou atualiza um grupo de segurança de rede existente |
> | Acção | Microsoft.Network/networkWatchers/availableProvidersList/action | Devolve a internet disponível fornecedores de serviços para uma região do Azure especificada. |
> | Acção | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Devolve a classificação de latência relativa para a internet fornecedores de serviços de uma localização especificada para regiões do Azure. |
> | Acção | Microsoft.Network/networkWatchers/configureFlowLog/action | Configura o registo do fluxo para um recurso de destino. |
> | Acção | Microsoft.Network/networkWatchers/connectionMonitors/delete | Elimina um Monitor de ligação |
> | Acção | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Monitorizar a conectividade entre pontos finais especificados de consulta |
> | Acção | Microsoft.Network/networkWatchers/connectionMonitors/read | Obter os detalhes de Monitor de ligação |
> | Acção | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Comece a monitorizar a conectividade entre pontos finais especificados |
> | Acção | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Parar/pausar, monitorizar a conectividade entre pontos finais especificados |
> | Acção | Microsoft.Network/networkWatchers/connectionMonitors/write | Cria um Monitor de ligação |
> | Acção | Microsoft.Network/networkWatchers/connectivityCheck/action | Verifica a possibilidade de estabelecer uma ligação TCP direta de uma máquina virtual para um determinado ponto de extremidade incluindo outra VM ou um servidor remoto arbitrário. |
> | Acção | Microsoft.Network/networkWatchers/delete | Elimina um observador de rede |
> | Acção | Microsoft.Network/networkWatchers/ipFlowVerify/action | Devolve se o pacote é permitido ou negado para ou de um destino específico. |
> | Acção | Microsoft.Network/networkWatchers/lenses/delete | Elimina uma lente |
> | Acção | Microsoft.Network/networkWatchers/lenses/query/action | Monitorizar o tráfego de rede num ponto de extremidade especificado de consulta |
> | Acção | Microsoft.Network/networkWatchers/lenses/read | Obter os detalhes de lente |
> | Acção | Microsoft.Network/networkWatchers/lenses/start/action | Iniciar a monitorização de tráfego de rede num ponto de extremidade especificado |
> | Acção | Microsoft.Network/networkWatchers/lenses/stop/action | Parar/pausar a monitorizar o tráfego de rede num ponto de extremidade especificado |
> | Acção | Microsoft.Network/networkWatchers/lenses/write | Cria uma lente |
> | Acção | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnóstico de configuração de rede. |
> | Acção | Microsoft.Network/networkWatchers/nextHop/action | Para um destino especificado e o endereço IP de destino, retornar o tipo de próximo salto e esperamos junto de endereço IP. |
> | Acção | Microsoft.Network/networkWatchers/packetCaptures/delete | Elimina uma captura de pacotes |
> | Acção | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Obtém informações sobre as propriedades e o estado de um recurso de captura de pacotes. |
> | Acção | Microsoft.Network/networkWatchers/packetCaptures/read | Obter a definição de captura de pacotes |
> | Acção | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Pare a sessão de captura de pacote em execução. |
> | Acção | Microsoft.Network/networkWatchers/packetCaptures/write | Cria uma captura de pacotes |
> | Acção | Microsoft.Network/networkWatchers/pingMeshes/delete | Elimina um PingMesh |
> | Acção | Microsoft.Network/networkWatchers/pingMeshes/read | Obter os detalhes de PingMesh |
> | Acção | Microsoft.Network/networkWatchers/pingMeshes/start/action | Iniciar PingMesh entre VMs especificadas |
> | Acção | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Parar PingMesh entre VMs especificadas |
> | Acção | Microsoft.Network/networkWatchers/pingMeshes/write | Cria um PingMesh |
> | Acção | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Monitorizar a conectividade entre pontos finais especificados de consulta de batch |
> | Acção | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Obtém o estado do fluxo de início de sessão um recurso. |
> | Acção | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Obtém o resultado de resolução de problemas de execução anterior ou atualmente em execução a operação de resolução de problemas. |
> | Acção | Microsoft.Network/networkWatchers/read | Obter a definição do observador de rede |
> | Acção | Microsoft.Network/networkWatchers/securityGroupView/action | Ver as regras de grupo de segurança de rede configurado e eficaz aplicadas numa VM. |
> | Acção | Microsoft.Network/networkWatchers/topology/action | Obtém uma vista de nível de rede de recursos e seus relacionamentos num grupo de recursos. |
> | Acção | Microsoft.Network/networkWatchers/troubleshoot/action | Inicia a resolução de problemas num recurso de sistema de rede no Azure. |
> | Acção | Microsoft.Network/networkWatchers/write | Cria um observador de rede ou atualiza um observador de rede existente |
> | Acção | Microsoft.Network/operations/read | Obter operações disponíveis |
> | Acção | Microsoft.Network/p2sVpnGateways/delete | Elimina um P2SVpnGateway. |
> | Acção | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Gerar o perfil de Vpn para P2SVpnGateway |
> | Acção | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Obtém um Estado de funcionamento da ligação de Vpn P2S para P2SVpnGateway |
> | Acção | Microsoft.Network/p2sVpnGateways/read | Obtém um P2SVpnGateway. |
> | Acção | Microsoft.Network/p2sVpnGateways/write | Coloca um P2SVpnGateway. |
> | Acção | Microsoft.Network/privateEndpoints/delete | Elimina um recurso de ponto final privado. |
> | Acção | Microsoft.Network/privateEndpoints/read | Obtém um recurso de ponto final privado. |
> | Acção | Microsoft.Network/privateEndpoints/write | Cria um novo ponto final privado ou atualiza um ponto de extremidade privado existente. |
> | Acção | Microsoft.Network/privateLinkServices/delete | Elimina um recurso de serviço de ligação privada. |
> | Acção | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Elimina uma ligação de ponto final privado. |
> | Acção | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Obtém uma definição de ligação de ponto final privado. |
> | Acção | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Cria uma nova ligação de ponto final privado ou atualiza uma ligação de ponto final privado existente. |
> | Acção | Microsoft.Network/privateLinkServices/read | Obtém um recurso de serviço de ligação privada. |
> | Acção | Microsoft.Network/privateLinkServices/write | Cria um novo serviço de ligação privada ou atualiza um serviço de ligação privada existente. |
> | Acção | Microsoft.Network/publicIPAddresses/delete | Elimina um endereço Ip público. |
> | Acção | Microsoft.Network/publicIPAddresses/join/action | Associa um endereço ip público. Não. o alerta. |
> | Acção | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço ip público. |
> | Acção | Microsoft.Network/publicIPAddresses/write | Cria um endereço Ip público ou atualiza um endereço Ip público existente.  |
> | Acção | Microsoft.Network/publicIPPrefixes/delete | Elimina um prefixo de Ip público |
> | Acção | Microsoft.Network/publicIPPrefixes/join/action | Associa um PublicIPPrefix. Não. o alerta. |
> | Acção | Microsoft.Network/publicIPPrefixes/read | Obtém uma definição de prefixo de Ip público |
> | Acção | Microsoft.Network/publicIPPrefixes/write | Cria um prefixo de Ip público ou atualiza um prefixo de Ip público existente |
> | Acção | Microsoft.Network/register/action | Regista a subscrição |
> | Acção | Microsoft.Network/routeFilters/delete | Elimina uma definição de filtro de rota |
> | Acção | Microsoft.Network/routeFilters/join/action | Associa um filtro de rota. Não. o alerta. |
> | Acção | Microsoft.Network/routeFilters/read | Obtém uma definição de filtro de rota |
> | Acção | Microsoft.Network/routeFilters/routeFilterRules/delete | Elimina uma definição de regra de filtro de rota |
> | Acção | Microsoft.Network/routeFilters/routeFilterRules/read | Obtém uma definição de regra de filtro de rota |
> | Acção | Microsoft.Network/routeFilters/routeFilterRules/write | Cria uma regra de filtro de rota ou atualiza uma regra de filtro de rota existente |
> | Acção | Microsoft.Network/routeFilters/write | Cria um filtro de rota ou atualiza um filtro de rota existente |
> | Acção | Microsoft.Network/routeTables/delete | Elimina uma definição de tabela de rota |
> | Acção | Microsoft.Network/routeTables/join/action | Associa uma tabela de rotas. Não. o alerta. |
> | Acção | Microsoft.Network/routeTables/read | Obtém uma definição de tabela de rota |
> | Acção | Microsoft.Network/routeTables/routes/delete | Elimina uma definição de rota |
> | Acção | Microsoft.Network/routeTables/routes/read | Obtém uma definição de rota |
> | Acção | Microsoft.Network/routeTables/routes/write | Cria uma rota ou atualiza uma rota existente |
> | Acção | Microsoft.Network/routeTables/write | Cria uma tabela de rotas ou atualiza uma tabela de rota existente |
> | Acção | Microsoft.Network/securegateways/delete | Eliminar Gateway seguro |
> | Acção | Microsoft.Network/securegateways/read | Obter o Gateway seguro |
> | Acção | Microsoft.Network/securegateways/write | Cria ou atualiza um Gateway seguro |
> | Acção | Microsoft.Network/serviceEndpointPolicies/delete | Elimina uma política de ponto final de serviço |
> | Acção | Microsoft.Network/serviceEndpointPolicies/join/action | Junta-se uma política de ponto final de serviço. Não. o alerta. |
> | Acção | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Associa uma sub-rede para políticas de ponto final de serviço. Não. o alerta. |
> | Acção | Microsoft.Network/serviceEndpointPolicies/read | Obtém uma descrição de política de ponto final de serviço |
> | Acção | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Elimina uma definição de política de ponto final de serviço |
> | Acção | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Obtém uma descrição de definição de política de ponto final de serviço |
> | Acção | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Cria uma definição de política de ponto final de serviço ou atualiza uma definição de política de ponto final de serviço existente |
> | Acção | Microsoft.Network/serviceEndpointPolicies/write | Cria uma política de ponto final de serviço ou atualiza uma política de ponto final de serviço existente |
> | Acção | Microsoft.Network/trafficManagerGeographicHierarchies/read | Obtém a hierarquia de Geographic Traffic Manager que contém regiões em que podem ser utilizados com o método de encaminhamento de tráfego geográfico |
> | Acção | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Elimina um ponto de final do Azure a partir de um perfil existente do Gestor de tráfego. O Gestor de tráfego deixará de encaminhamento de tráfego para o ponto de final do Azure eliminada. |
> | Acção | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Obtém um ponto de extremidade do Azure que pertence a um perfil do Gestor de tráfego, incluindo todas as propriedades do ponto de extremidade do Azure. |
> | Acção | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Adicionar um novo ponto de final do Azure num perfil existente do Gestor de tráfego ou atualizar as propriedades de um ponto de final do Azure existente daquele perfil do Gestor de tráfego. |
> | Acção | Microsoft.Network/trafficManagerProfiles/delete | Elimine o perfil do Gestor de tráfego. Associados ao perfil do Gestor de tráfego de todas as definições serão perdidas e o perfil já não pode ser utilizado para encaminhar o tráfego. |
> | Acção | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Elimina um ponto final externo a partir de um perfil existente do Gestor de tráfego. O Gestor de tráfego deixará de encaminhamento de tráfego para o ponto final externo foi eliminada. |
> | Acção | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Obtém um ponto final externo que pertence a um perfil do Gestor de tráfego, incluindo todas as propriedades do ponto de extremidade externo. |
> | Acção | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Adicionar um novo ponto final externo num perfil existente do Gestor de tráfego ou atualizar as propriedades de um ponto final externo existente daquele perfil do Gestor de tráfego. |
> | Acção | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Obtém o mapa térmico do Gestor de tráfego para o perfil do Traffic Manager determinado que contém os dados de contagens e latência de consulta por IP de origem e de localização. |
> | Acção | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Elimina um ponto de extremidade aninhada de um perfil existente do Gestor de tráfego. O Gestor de tráfego deixará de encaminhamento de tráfego para o ponto de extremidade aninhados eliminados. |
> | Acção | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Obtém um ponto de extremidade aninhados, que pertence a um perfil do Gestor de tráfego, incluindo todas as propriedades do ponto de extremidade aninhados. |
> | Acção | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Adicionar um novo ponto de final aninhados num perfil existente do Gestor de tráfego ou atualizar as propriedades de um aninhados ponto final existente daquele perfil do Gestor de tráfego. |
> | Acção | Microsoft.Network/trafficManagerProfiles/read | Obter a configuração de perfil do Gestor de tráfego. Isto inclui definições de DNS, definições de encaminhamento de tráfego, as definições de monitorização do ponto final e a lista de pontos de extremidade encaminhado por este perfil do Gestor de tráfego. |
> | Acção | Microsoft.Network/trafficManagerProfiles/write | Criar um perfil do Gestor de tráfego ou modificar a configuração de um perfil existente do Gestor de tráfego.<br>Isto inclui ativar ou desativar um perfil e modificar as definições de DNS, definições de encaminhamento de tráfego ou as definições de monitorização do ponto final.<br>Pontos finais encaminhados pelo perfil do Gestor de tráfego podem ser adicionados, removidos, ativados ou desativados. |
> | Acção | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Elimina a chave de nível de subscrição utilizada para a recolha de métricas de utilizador em tempo real. |
> | Acção | Microsoft.Network/trafficManagerUserMetricsKeys/read | Obtém a chave de nível de subscrição utilizada para a recolha de métricas de utilizador em tempo real. |
> | Acção | Microsoft.Network/trafficManagerUserMetricsKeys/write | Cria uma nova chave de nível de assinatura a ser utilizado para a recolha de métricas de utilizador em tempo real. |
> | Acção | Microsoft.Network/unregister/action | Anula o registo da subscrição |
> | Acção | Microsoft.Network/virtualHubs/delete | Elimina um Hub Virtual |
> | Acção | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Elimina um HubVirtualNetworkConnection |
> | Acção | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Obter um HubVirtualNetworkConnection |
> | Acção | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Criar ou atualizar um HubVirtualNetworkConnection |
> | Acção | Microsoft.Network/virtualHubs/read | Obter um Hub Virtual |
> | Acção | Microsoft.Network/virtualHubs/write | Criar ou atualizar um Hub Virtual |
> | Acção | microsoft.network/virtualnetworkgateways/connections/read | Obter VirtualNetworkGatewayConnection |
> | Acção | Microsoft.Network/virtualNetworkGateways/delete | Elimina um virtualNetworkGateway |
> | Acção | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Gerar o pacote do VpnClient para virtualNetworkGateway |
> | Acção | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Gerar o pacote de VpnProfile para o VirtualNetworkGateway |
> | Acção | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Obtém virtualNetworkGateway anunciados rotas |
> | Acção | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Obtém o estado de ponto a ponto do bgp virtualNetworkGateway |
> | Acção | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Obtém as rotas aprendida de virtualnetworkgateway |
> | Acção | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Obter por Estado de funcionamento de ligação de cliente Vpn para o VirtualNetworkGateway |
> | Acção | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Obter Vpnclient Ipsec parâmetros para o cliente de VirtualNetworkGateway P2S. |
> | Acção | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Obtém o URL de um pacote de perfil do cliente de vpn gerado |
> | Acção | Microsoft.Network/virtualNetworkGateways/read | Obtém um VirtualNetworkGateway |
> | Acção | microsoft.network/virtualnetworkgateways/reset/action | Repõe um virtualNetworkGateway |
> | Acção | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Repor a chave partilhada do Vpnclient para cliente VirtualNetworkGateway P2S. |
> | Acção | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Defina parâmetros para o cliente de VirtualNetworkGateway P2S de Vpnclient Ipsec. |
> | Acção | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Suporte de listas de dispositivos Vpn |
> | Acção | Microsoft.Network/virtualNetworkGateways/write | Cria ou atualiza um VirtualNetworkGateway |
> | Acção | Microsoft.Network/virtualNetworks/BastionHosts/action | Obtém as referências de anfitrião de bastião numa rede Virtual. |
> | Acção | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Obtém as referências de anfitrião de bastião numa rede Virtual. |
> | Acção | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Verifique se o endereço Ip está disponível na rede virtual especificada |
> | Acção | Microsoft.Network/virtualNetworks/delete | Elimina uma rede virtual |
> | Acção | Microsoft.Network/virtualNetworks/peer/action | Elementos de uma rede virtual com outra rede virtual |
> | Acção | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Acção | Microsoft.Network/virtualNetworks/subnets/delete | Elimina uma sub-rede de rede virtual |
> | Acção | Microsoft.Network/virtualNetworks/subnets/join/action | Junta-se a uma rede virtual. Não. o alerta. |
> | Acção | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Associa o recurso, como a conta de armazenamento ou base de dados SQL a uma sub-rede. Não. o alerta. |
> | Acção | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Prepara uma sub-rede ao aplicar políticas de rede necessários |
> | Acção | Microsoft.Network/virtualNetworks/subnets/read | Obtém uma definição de sub-rede de rede virtual |
> | Acção | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Obtém as referências a todas as máquinas virtuais na sub-rede de rede virtual |
> | Acção | Microsoft.Network/virtualNetworks/subnets/write | Cria uma sub-rede de rede virtual ou atualiza uma sub-rede de rede virtual existente |
> | Acção | Microsoft.Network/virtualNetworks/usages/read | Obter as utilizações IP para cada sub-rede da rede virtual |
> | Acção | Microsoft.Network/virtualNetworks/virtualMachines/read | Obtém as referências a todas as máquinas virtuais numa rede virtual |
> | Acção | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Elimina um peering de rede virtual |
> | Acção | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Obtém uma definição de peering de rede virtual |
> | Acção | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Cria um peering de rede virtual ou atualiza um peering de rede virtual existente |
> | Acção | Microsoft.Network/virtualNetworks/write | Cria uma rede virtual ou atualiza uma rede virtual existente |
> | Acção | Microsoft.Network/virtualNetworkTaps/delete | Eliminar o Tap da rede Virtual |
> | Acção | Microsoft.Network/virtualNetworkTaps/join/action | Associa um toque de rede virtual. Não. o alerta. |
> | Acção | Microsoft.Network/virtualNetworkTaps/read | Obter o Tap da rede Virtual |
> | Acção | Microsoft.Network/virtualNetworkTaps/write | Criar ou atualizar o Tap da rede Virtual |
> | Acção | Microsoft.Network/virtualWans/delete | Elimina uma Virtual Wan |
> | Acção | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Elimina um P2SVpnServerConfiguration Wan virtual |
> | Acção | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Obtém um P2SVpnServerConfiguration Wan virtual |
> | Acção | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Cria um P2SVpnServerConfiguration Wan virtual ou atualiza uma existente P2SVpnServerConfiguration de Wan virtual |
> | Acção | Microsoft.Network/virtualWans/read | Obtenção de um Virtual Wan |
> | Acção | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Obtém suporte VirtualWan fornecedores de segurança. |
> | Acção | Microsoft.Network/virtualWans/virtualHubs/read | Obtém todos os Hubs de Virtual que façam referência a uma Wan Virtual. |
> | Acção | Microsoft.Network/virtualwans/vpnconfiguration/action | Obtém uma configuração de Vpn |
> | Acção | Microsoft.Network/virtualWans/vpnSites/read | Obtém todos os Sites de VPN que fazem referência a uma Wan Virtual. |
> | Acção | Microsoft.Network/virtualWans/write | Criar ou atualizar uma Wan Virtual |
> | Acção | Microsoft.Network/vpnGateways/delete | Elimina um VpnGateway. |
> | Acção | microsoft.network/vpngateways/listvpnconnectionshealth/action | Obtém o estado de funcionamento de ligação para todos ou um subconjunto de ligações num VpnGateway |
> | Acção | Microsoft.Network/vpnGateways/read | Obtém um VpnGateway. |
> | Acção | microsoft.network/vpngateways/reset/action | Repõe um VpnGateway |
> | Acção | microsoft.network/vpnGateways/vpnConnections/delete | Elimina um VpnConnection. |
> | Acção | microsoft.network/vpnGateways/vpnConnections/read | Obtém um VpnConnection. |
> | Acção | microsoft.network/vpnGateways/vpnConnections/write | Coloca um VpnConnection. |
> | Acção | Microsoft.Network/vpnGateways/write | Coloca um VpnGateway. |
> | Acção | Microsoft.Network/vpnsites/delete | Elimina um recurso de Vpn de Site. |
> | Acção | Microsoft.Network/vpnsites/read | Obtém um recurso de Vpn de Site. |
> | Acção | Microsoft.Network/vpnsites/write | Cria ou atualiza um recurso de Vpn de Site. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Verifica se determinado nome de recurso do Espaço de nomes está ou não disponível dentro do serviço NotificationHub. |
> | Acção | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Obter a lista de descrição das Regras de Autorização de Espaços de nomes. |
> | Acção | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Elimine regra de autorização de espaço de nomes. Não é possível eliminar a regra de autorização de espaço de nomes predefinido.  |
> | Acção | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Obter Cadeia de Ligação para o Espaço de nomes |
> | Acção | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Obter a lista de descrição das Regras de Autorização de Espaços de nomes. |
> | Acção | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Chave Primária/Secundária de Regeneração de Regra de Autorização de Espaço de nomes, especifica a Chave que tem de ser regenerada |
> | Acção | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Criar regras de autorização ao nível de espaço de nomes e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o principal e as chaves secundárias podem ser atualizadas. |
> | Acção | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Verifica se determinado nome do NotificationHub está disponível dentro do Espaço de nomes. |
> | Acção | Microsoft.NotificationHubs/Namespaces/Delete | Eliminar Recurso de Espaço de nomes |
> | Acção | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Obter lista de Regras de Autorização do Hub de Notificações |
> | Acção | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Eliminar Regras de Autorização do Hub de Notificações |
> | Acção | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Obter Cadeia de Ligação para o Hub de Notificações |
> | Acção | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Obter lista de Regras de Autorização do Hub de Notificações |
> | Acção | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Regra de Autorização do Hub de Notificações Regenerar Chave Primária/Secundária, especifica a Chave que tem de ser regenerada |
> | Acção | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Criar regras de autorização do Hub de notificações e atualizar as respetivas propriedades. Os direitos de acesso de regras de autorização, o principal e as chaves secundárias podem ser atualizadas. |
> | Acção | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Enviar notificação push de teste. |
> | Acção | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Eliminar Recurso do Hub de Notificações |
> | Acção | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Obter lista de descrições de recursos do espaço de nomes métricas |
> | Acção | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Obtenha todas as credenciais PNS do Hub de notificação. Isto inclui, credenciais WNS, MPNS, APNS, GCM e Baidu |
> | Acção | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Obter lista de Descrições de Recursos do Hub de Notificações |
> | Acção | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Criar um Hub de notificação e atualizar as respetivas propriedades. Suas propriedades incluem, principalmente, credenciais PNS. Regras de autorização e TTL |
> | Acção | Microsoft.NotificationHubs/Namespaces/read | Obter a lista de Descrição de Recursos de Espaço de nomes |
> | Acção | Microsoft.NotificationHubs/Namespaces/write | Criar um recurso de espaço de nomes e Atualize as respetivas propriedades. As etiquetas e a capacidade do espaço de nomes são propriedades que podem ser atualizadas. |
> | Acção | Microsoft.NotificationHubs/operationResults/read | Devolve os resultados da operação para o fornecedor de Hubs de Notificação |
> | Acção | Microsoft.NotificationHubs/operations/read | Devolve uma lista de operações suportadas para o fornecedor de Hubs de Notificação |
> | Acção | Microsoft.NotificationHubs/register/action | Regista a subscrição para o fornecedor de recursos de Notification hubs e permite a criação de espaços de nomes e os Notification hubs |
> | Acção | Microsoft.NotificationHubs/unregister/action | Anula o registo da subscrição para o fornecedor de recursos de Notification hubs e permite a criação de espaços de nomes e os Notification hubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.OffAzure/HyperVSites/clusters/read | Obtém as propriedades de um cluster Hyper-V |
> | Acção | Microsoft.OffAzure/HyperVSites/clusters/write | Cria ou atualiza o cluster de Hyper-V |
> | Acção | Microsoft.OffAzure/HyperVSites/delete | Elimina o site Hyper-V |
> | Acção | Microsoft.OffAzure/HyperVSites/hosts/read | Obtém as propriedades de um anfitrião Hyper-V |
> | Acção | Microsoft.OffAzure/HyperVSites/hosts/write | Cria ou atualiza o anfitrião de Hyper-V |
> | Acção | Microsoft.OffAzure/HyperVSites/jobs/read | Obtém as propriedades de tarefas de Hyper-V |
> | Acção | Microsoft.OffAzure/HyperVSites/machines/read | Obtém as propriedades de um máquinas Hyper-V |
> | Acção | Microsoft.OffAzure/HyperVSites/machines/start/action | Iniciar máquinas Hyper-V |
> | Acção | Microsoft.OffAzure/HyperVSites/machines/stop/action | Para as máquinas o Hyper-V |
> | Acção | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Obtém as propriedades de um Estado da operação de Hyper-V |
> | Acção | Microsoft.OffAzure/HyperVSites/read | Obtém as propriedades de um site de Hyper-V |
> | Acção | Microsoft.OffAzure/HyperVSites/refresh/action | Atualiza os objetos dentro de um site de Hyper-V |
> | Acção | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Obtém as propriedades de um contas run as do Hyper-V |
> | Acção | Microsoft.OffAzure/HyperVSites/usage/read | Obtém as utilizações de um site de Hyper-V |
> | Acção | Microsoft.OffAzure/HyperVSites/write | Cria ou atualiza o site Hyper-V |
> | Acção | Microsoft.OffAzure/Operations/read | Lê as operações expostas |
> | Acção | Microsoft.OffAzure/register/action | Regista a subscrição no fornecedor de recursos de Microsoft.OffAzure |
> | Acção | Microsoft.OffAzure/VMwareSites/delete | Elimina o site de VMware |
> | Acção | Microsoft.OffAzure/VMwareSites/jobs/read | Obtém as propriedades de tarefas de VMware |
> | Acção | Microsoft.OffAzure/VMwareSites/machines/read | Obtém as propriedades de um máquinas do VMware |
> | Acção | Microsoft.OffAzure/VMwareSites/machines/start/action | Iniciar máquinas do VMware |
> | Acção | Microsoft.OffAzure/VMwareSites/machines/stop/action | Para as máquinas do VMware |
> | Acção | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Obtém as propriedades de um Estado de operação do VMware |
> | Acção | Microsoft.OffAzure/VMwareSites/read | Obtém as propriedades de um site de VMware |
> | Acção | Microsoft.OffAzure/VMwareSites/refresh/action | Atualiza os objetos dentro de um site de VMware |
> | Acção | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Obtém as propriedades de um VMware contas run as |
> | Acção | Microsoft.OffAzure/VMwareSites/usage/read | Obtém as utilizações de um site de VMware |
> | Acção | Microsoft.OffAzure/VMwareSites/vcenters/read | Obtém as propriedades de um VMware vCenter |
> | Acção | Microsoft.OffAzure/VMwareSites/vcenters/write | Cria ou atualiza o vCenter do VMware |
> | Acção | Microsoft.OffAzure/VMwareSites/write | Cria ou atualiza o site de VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.OperationalInsights/linkTargets/read | Apresenta uma lista de contas existentes que não estão associadas uma subscrição do Azure. Para ligar esta subscrição do Azure a uma área de trabalho, utilize um id de cliente devolvido por esta operação na propriedade de id de cliente da operação Criar área de trabalho. |
> | Acção | microsoft.operationalinsights/operations/read | Apresenta uma lista de todas as operações de API de Rest de OperationalInsights disponíveis. |
> | Acção | microsoft.operationalinsights/register/action | Rergisters a subscrição. |
> | Acção | Microsoft.OperationalInsights/register/action | Registe uma subscrição de um fornecedor de recursos. |
> | Acção | microsoft.operationalinsights/unregister/action | Anula o registo da subscrição. |
> | Acção | Microsoft.OperationalInsights/workspaces/analytics/query/action | Usando o novo mecanismo de pesquisa. |
> | Acção | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Obter esquema de pesquisa V2. |
> | Acção | Microsoft.OperationalInsights/workspaces/api/query/action | Usando o novo mecanismo de pesquisa. |
> | Acção | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Obter esquema de pesquisa V2. |
> | Acção | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Eliminar âmbito da configuração |
> | Acção | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Obter âmbito da configuração |
> | Acção | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Definir o âmbito da configuração |
> | Acção | Microsoft.OperationalInsights/workspaces/datasources/delete | Elimine origens de dados numa área de trabalho. |
> | Acção | Microsoft.OperationalInsights/workspaces/datasources/read | Obter origens de dados numa área de trabalho. |
> | Acção | Microsoft.OperationalInsights/workspaces/datasources/write | Criar/atualizar origens de dados numa área de trabalho. |
> | Acção | Microsoft.OperationalInsights/workspaces/delete | Elimina uma área de trabalho. Se a área de trabalho estava associada a uma área de trabalho existente no momento da criação da área de trabalho que estava associada não é eliminada. |
> | Acção | Microsoft.OperationalInsights/workspaces/gateways/delete | Remove um gateway configurado para a área de trabalho. |
> | Acção | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Gera o certificado de registo para a área de trabalho. Este certificado é utilizado para ligar o Microsoft System Center Operation Manager à área de trabalho. |
> | Acção | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Desativa um pacote de informações de uma área de trabalho específica. |
> | Acção | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Permite que um pacote de informações de uma área de trabalho específica. |
> | Acção | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Apresenta uma lista de todos os "intelligence packs" que estão visíveis para uma determinada área de trabalho e também indica se o pacote está ativado ou desativado para essa área de trabalho. |
> | Acção | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Eliminar serviços ligados na área de trabalho especificada. |
> | Acção | Microsoft.OperationalInsights/workspaces/linkedServices/read | Obter serviços ligados na área de trabalho especificada. |
> | Acção | Microsoft.OperationalInsights/workspaces/linkedServices/write | Criar/atualizar serviços ligados na área de trabalho especificada. |
> | Acção | Microsoft.OperationalInsights/workspaces/listKeys/action | Obtém as chaves de lista para a área de trabalho. Estas chaves são utilizadas para ligar os agentes de informações operacionais da Microsoft à área de trabalho. |
> | Acção | Microsoft.OperationalInsights/workspaces/listKeys/read | Obtém as chaves de lista para a área de trabalho. Estas chaves são utilizadas para ligar os agentes de informações operacionais da Microsoft à área de trabalho. |
> | Acção | Microsoft.OperationalInsights/workspaces/managementGroups/read | Obtém os nomes e os metadados para grupos de gestão do System Center Operations Manager ligados a esta área de trabalho. |
> | Acção | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Obter definições de métrica na área de trabalho |
> | Acção | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Elimine definições de notificação do utilizador para a área de trabalho. |
> | Acção | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Obter definições de notificação do utilizador para a área de trabalho. |
> | Acção | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Defina definições de notificação do utilizador para a área de trabalho. |
> | Acção | microsoft.operationalinsights/workspaces/operations/read | Obtém o estado de uma operação de área de trabalho OperationalInsights. |
> | Acção | Microsoft.OperationalInsights/workspaces/purge/action | Eliminar dados especificados a partir da área de trabalho |
> | Acção | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Ler dados da tabela AADDomainServicesAccountLogon |
> | Acção | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Ler dados da tabela AADDomainServicesLogonLogoff |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Ler dados da tabela ADAssessmentRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Ler dados da tabela ADReplicationResult |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Ler dados da tabela ADSecurityAssessmentRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/Alert/read | Ler dados da tabela de Alertas |
> | Acção | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Ler dados da tabela AlertHistory |
> | Acção | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Ler dados da tabela AppCenterError |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Ler dados da tabela ApplicationInsights |
> | Acção | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Ler dados da tabela AuditLogs |
> | Acção | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Ler dados da tabela AutoscaleEvaluationsLog |
> | Acção | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Ler dados da tabela AutoscaleScaleActionsLog |
> | Acção | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Ler dados da tabela AWSCloudTrail |
> | Acção | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Ler dados da tabela AzureActivity |
> | Acção | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Ler dados da tabela AzureMetrics |
> | Acção | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Ler dados da tabela BoundPort |
> | Acção | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Ler dados da tabela CommonSecurityLog |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Ler dados da tabela ComputerGroup |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Ler dados da tabela ConfigurationChange |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Ler dados da tabela ConfigurationData |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Ler dados da tabela ContainerImageInventory |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Ler dados da tabela ContainerInventory |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Ler dados da tabela ContainerLog |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Ler dados da tabela ContainerNodeInventory |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Ler dados da tabela ContainerServiceLog |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Ler dados da tabela DatabricksAccounts |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | Ler dados da tabela DatabricksClusters |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | Ler dados da tabela DatabricksDBFS |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Ler dados da tabela DatabricksJobs |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | Ler dados da tabela DatabricksNotebook |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Ler dados da tabela DatabricksSecrets |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Ler dados da tabela DatabricksSQLPermissions |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | Ler dados da tabela DatabricksSSH |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | Ler dados da tabela DatabricksTables |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Ler dados da tabela DatabricksWorkspace |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Ler dados da tabela DeviceAppCrash |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Ler dados da tabela DeviceAppLaunch |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Ler dados da tabela DeviceCalendar |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Ler dados da tabela DeviceCleanup |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Ler dados da tabela DeviceConnectSession |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Ler dados da tabela DeviceEtw |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Ler dados da tabela DeviceHardwareHealth |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Ler dados da tabela DeviceHealth |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Ler dados da tabela DeviceHeartbeat |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Ler dados da tabela DeviceSkypeHeartbeat |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Ler dados da tabela DeviceSkypeSignIn |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Ler dados da tabela DeviceSleepState |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Ler dados da tabela DHAppFailure |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Ler dados da tabela DHAppReliability |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Ler dados da tabela DHDriverReliability |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Ler dados da tabela DHLogonFailures |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Ler dados da tabela DHLogonMetrics |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Ler dados da tabela DHOSCrashData |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Ler dados da tabela DHOSReliability |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Ler dados da tabela DHWipAppLearning |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Ler dados da tabela DnsEvents |
> | Acção | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Ler dados da tabela DnsInventory |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Ler dados da tabela ETWEvent |
> | Acção | Microsoft.OperationalInsights/workspaces/query/Event/read | Ler dados da tabela de Eventos |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Ler dados da tabela ExchangeAssessmentRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Ler dados da tabela ExchangeOnlineAssessmentRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Ler dados da tabela Heartbeat |
> | Acção | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Ler dados da tabela HuntingBookmark |
> | Acção | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Ler dados da tabela IISAssessmentRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Ler dados da tabela InboundConnection |
> | Acção | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Ler dados da tabela InsightsMetrics |
> | Acção | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Ler dados da tabela IntuneAuditLogs |
> | Acção | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Ler dados da tabela IntuneOperationalLogs |
> | Acção | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Ler dados da tabela KubeEvents |
> | Acção | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Ler dados da tabela KubeNodeInventory |
> | Acção | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Ler dados da tabela KubePodInventory |
> | Acção | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Ler dados da tabela KubeServices |
> | Acção | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Ler dados da tabela LinuxAuditLog |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Ler dados da tabela MAApplication |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Ler dados da tabela MAApplicationHealth |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Ler dados da tabela MAApplicationHealthAlternativeVersions |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Ler dados da tabela MAApplicationHealthIssues |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Ler dados da tabela MAApplicationInstance |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Ler dados da tabela MAApplicationInstanceReadiness |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Ler dados da tabela MAApplicationReadiness |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Ler dados da tabela MADeploymentPlan |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Ler dados da tabela MADevice |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Ler dados da tabela MADeviceNotEnrolled |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Ler dados da tabela MADeviceNRT |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Ler dados da tabela MADevicePnPHealth |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Ler dados da tabela MADevicePnPHealthIssues |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Ler dados da tabela MADeviceReadiness |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Ler dados da tabela MADriverInstanceReadiness |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Ler dados da tabela MADriverReadiness |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Ler dados da tabela MAOfficeAddin |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Ler dados da tabela MAOfficeAddinEntityHealth |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Ler dados da tabela MAOfficeAddinHealth |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Ler dados da tabela MAOfficeAddinHealthEventNRT |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Ler dados da tabela MAOfficeAddinHealthIssues |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Ler dados da tabela MAOfficeAddinInstance |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Ler dados da tabela MAOfficeAddinReadiness |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Ler dados da tabela MAOfficeApp |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Ler dados da tabela MAOfficeAppCrashesNRT |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Ler dados da tabela MAOfficeAppHealth |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Ler dados da tabela MAOfficeAppInstance |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Ler dados da tabela MAOfficeAppInstanceHealth |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Ler dados da tabela MAOfficeAppReadiness |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Ler dados da tabela MAOfficeAppSessionsNRT |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Ler dados da tabela MAOfficeBuildInfo |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Ler dados da tabela MAOfficeCurrencyAssessment |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Ler dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Ler dados da tabela MAOfficeDeploymentStatus |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Ler dados da tabela MAOfficeDeploymentStatusNRT |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Ler dados da tabela MAOfficeMacroErrorNRT |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Ler dados da tabela MAOfficeMacroGlobalHealth |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Ler dados da tabela MAOfficeMacroHealth |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Ler dados da tabela MAOfficeMacroHealthIssues |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Ler dados da tabela MAOfficeMacroIssueInstanceReadiness |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Ler dados da tabela MAOfficeMacroIssueReadiness |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Ler dados da tabela MAOfficeMacroSummary |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Ler dados da tabela MAOfficeSuite |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Ler dados da tabela MAOfficeSuiteInstance |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Ler dados da tabela MAProposedPilotDevices |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Ler dados da tabela MAWindowsBuildInfo |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Ler dados da tabela MAWindowsCurrencyAssessment |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Ler dados da tabela MAWindowsCurrencyAssessmentDailyCounts |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Ler dados da tabela MAWindowsDeploymentStatus |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Ler dados da tabela MAWindowsDeploymentStatusNRT |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | Acção | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebW3CLog/read | Ler dados da tabela MicrosoftWebW3CLog |
> | Acção | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Ler dados da tabela NetworkMonitoring |
> | Acção | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Ler dados da tabela OfficeActivity |
> | Acção | Microsoft.OperationalInsights/workspaces/query/Operation/read | Ler dados da tabela de Operação |
> | Acção | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Ler dados da tabela OutboundConnection |
> | Acção | Microsoft.OperationalInsights/workspaces/query/Perf/read | Ler dados da tabela de Desempenho |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Ler dados da tabela ProtectionStatus |
> | Acção | Microsoft.OperationalInsights/workspaces/query/read | Executar consultas através dos dados na área de trabalho |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Ler dados da tabela ReservedAzureCommonFields |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Ler dados da tabela ReservedCommonFields |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Ler dados da tabela SCCMAssessmentRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Ler dados da tabela SCOMAssessmentRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Ler dados da tabela SecurityAlert |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Ler dados da tabela SecurityBaseline |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Ler dados da tabela SecurityBaselineSummary |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Ler dados da tabela SecurityDetection |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Ler dados da tabela SecurityEvent |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Ler dados da tabela SecurityIoTRawEvent |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Ler dados da tabela SecurityRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Ler dados da tabela ServiceFabricOperationalEvent |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Ler dados da tabela ServiceFabricReliableActorEvent |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Ler dados da tabela ServiceFabricReliableServiceEvent |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Ler dados da tabela SfBAssessmentRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Ler dados da tabela SfBOnlineAssessmentRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Ler dados da tabela SharePointOnlineAssessmentRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Ler dados da tabela SigninLogs |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Ler dados da tabela SPAssessmentRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Ler dados da tabela SQLAssessmentRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Ler dados da tabela SQLQueryPerformance |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Ler dados da tabela SqlThreatProtectionLoginAudits |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Ler dados da tabela SqlVulnerabilityAssessmentResult |
> | Acção | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Ler dados da tabela Syslog |
> | Acção | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Ler dados da tabela SysmonEvent |
> | Acção | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | A ler dados de registos personalizados |
> | Acção | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Ler dados da tabela ThreatIntelligenceIndicator |
> | Acção | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Ler dados da tabela UAApp |
> | Acção | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Ler dados da tabela UAComputer |
> | Acção | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Ler dados da tabela UAComputerRank |
> | Acção | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Ler dados da tabela UADriver |
> | Acção | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Ler dados da tabela UADriverProblemCodes |
> | Acção | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Ler dados da tabela UAFeedback |
> | Acção | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Ler dados da tabela UAHardwareSecurity |
> | Acção | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Ler dados da tabela UAIESiteDiscovery |
> | Acção | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Ler dados da tabela UAOfficeAddIn |
> | Acção | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Ler dados da tabela UAProposedActionPlan |
> | Acção | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Ler dados da tabela UASysReqIssue |
> | Acção | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Ler dados da tabela UAUpgradedComputer |
> | Acção | Microsoft.OperationalInsights/workspaces/query/Update/read | Ler dados da tabela de Atualização |
> | Acção | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Ler dados da tabela UpdateRunProgress |
> | Acção | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Ler dados da tabela UpdateSummary |
> | Acção | Microsoft.OperationalInsights/workspaces/query/Usage/read | Ler dados da tabela de Utilização |
> | Acção | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Ler dados da tabela VMBoundPort |
> | Acção | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Ler dados da tabela VMConnection |
> | Acção | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Ler dados da tabela W3CIISLog |
> | Acção | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Ler dados da tabela WaaSDeploymentStatus |
> | Acção | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Ler dados da tabela WaaSInsiderStatus |
> | Acção | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Ler dados da tabela WaaSUpdateStatus |
> | Acção | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Ler dados da tabela WDAVStatus |
> | Acção | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Ler dados da tabela WDAVThreat |
> | Acção | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Ler dados da tabela WindowsClientAssessmentRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Ler dados da tabela WindowsEvent |
> | Acção | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Ler dados da tabela WindowsFirewall |
> | Acção | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Ler dados da tabela WindowsServerAssessmentRecommendation |
> | Acção | Microsoft.OperationalInsights/workspaces/query/WireData/read | Ler dados da tabela WireData |
> | Acção | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Ler dados da tabela WorkloadMonitoringPerf |
> | Acção | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Ler dados da tabela WUDOAggregatedStatus |
> | Acção | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Ler dados da tabela WUDOStatus |
> | Acção | Microsoft.OperationalInsights/workspaces/read | Obtém uma área de trabalho existente |
> | Acção | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Regenera a chave partilhada de área de trabalho especificada |
> | Acção | microsoft.operationalinsights/workspaces/rules/read | Obtenha todas as regras de alerta. |
> | Acção | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Elimina uma consulta de pesquisa guardada |
> | Acção | Microsoft.OperationalInsights/workspaces/savedSearches/read | Obtém uma consulta de pesquisa guardada |
> | Acção | microsoft.operationalinsights/workspaces/savedsearches/results/read | Serão salvos de resultados de pesquisas. Preterido |
> | Acção | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Elimine ações de pesquisa agendada. |
> | Acção | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Obter ações de pesquisa agendada. |
> | Acção | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Criar ou atualizar ações de pesquisa agendada. |
> | Acção | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Elimine procuras agendadas. |
> | Acção | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Obtenha pesquisas agendadas. |
> | Acção | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Criar ou atualizar pesquisas agendadas. |
> | Acção | Microsoft.OperationalInsights/workspaces/savedSearches/write | Cria uma consulta de pesquisa guardada |
> | Acção | Microsoft.OperationalInsights/workspaces/schema/read | Obtém o esquema de pesquisa para a área de trabalho.  Esquema de pesquisa inclui os campos expostos e os respetivos tipos. |
> | Acção | Microsoft.OperationalInsights/workspaces/search/action | Executa uma consulta de pesquisa |
> | Acção | microsoft.operationalinsights/workspaces/search/read | Obtenha os resultados da pesquisa. Preterido. |
> | Acção | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Obtém as chaves partilhadas para a área de trabalho. Estas chaves são utilizadas para ligar os agentes de informações operacionais da Microsoft à área de trabalho. |
> | Acção | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Obtém as chaves partilhadas para a área de trabalho. Estas chaves são utilizadas para ligar os agentes de informações operacionais da Microsoft à área de trabalho. |
> | Acção | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Elimina uma configuração de armazenamento. Isso deixa de informações operacionais da Microsoft de ler dados da conta de armazenamento. |
> | Acção | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Obtém uma configuração de armazenamento. |
> | Acção | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Cria uma nova configuração de armazenamento. Estas configurações são usadas para extrair dados de uma localização de uma conta de armazenamento existente. |
> | Acção | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Obter o registo de falhas de tradução de atualização de pesquisa para a área de trabalho |
> | Acção | Microsoft.OperationalInsights/workspaces/usages/read | Obtém os dados de utilização de uma área de trabalho incluindo a quantidade de dados lidos pela área de trabalho. |
> | Acção | Microsoft.OperationalInsights/workspaces/write | Cria uma nova área de trabalho ou ligações para uma área de trabalho existente fornecendo o id de cliente da área de trabalho existente. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.OperationsManagement/managementAssociations/delete | Eliminar a associação de gestão existente |
> | Acção | Microsoft.OperationsManagement/managementAssociations/read | Obtenha a associação de gestão existente |
> | Acção | Microsoft.OperationsManagement/managementAssociations/write | Criar uma nova associação de gestão |
> | Acção | Microsoft.OperationsManagement/managementConfigurations/delete | Eliminar configuração de gestão existente |
> | Acção | Microsoft.OperationsManagement/managementConfigurations/read | Obter configuração de gestão existente |
> | Acção | Microsoft.OperationsManagement/managementConfigurations/write | Criar uma nova configuração de gestão |
> | Acção | Microsoft.OperationsManagement/register/action | Registe uma subscrição de um fornecedor de recursos. |
> | Acção | Microsoft.OperationsManagement/solutions/delete | Eliminar a solução de OMS existente |
> | Acção | Microsoft.OperationsManagement/solutions/read | Obter a solução de OMS a sair |
> | Acção | Microsoft.OperationsManagement/solutions/write | Criar nova solução OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.PolicyInsights/asyncOperationResults/read | Obtém o resultado da operação assíncrona. |
> | Acção | Microsoft.PolicyInsights/operations/read | Obtém suporte operações no espaço de nomes de policyinsights |
> | Acção | Microsoft.PolicyInsights/policyEvents/queryResults/action | Informações de consulta sobre eventos de política. |
> | Acção | Microsoft.PolicyInsights/policyEvents/queryResults/read | Informações de consulta sobre eventos de política. |
> | Acção | Microsoft.PolicyInsights/policyStates/queryResults/action | Informações de consulta sobre estados de política. |
> | Acção | Microsoft.PolicyInsights/policyStates/queryResults/read | Informações de consulta sobre estados de política. |
> | Acção | Microsoft.PolicyInsights/policyStates/summarize/action | Informações de resumo de consulta sobre os estados de política mais recentes. |
> | Acção | Microsoft.PolicyInsights/policyStates/summarize/read | Informações de resumo de consulta sobre os estados de política mais recentes. |
> | Acção | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Desencadeia uma nova avaliação de conformidade para o âmbito selecionado. |
> | Acção | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | As informações de consulta sobre recursos necessária pelas políticas de DeployIfNotExists. |
> | Acção | Microsoft.PolicyInsights/register/action | Regista o fornecedor de recursos do Microsoft Policy Insights e ativa ações na mesma. |
> | Acção | Microsoft.PolicyInsights/remediations/cancel/action | Cancele remediações de Microsoft Policy em curso. |
> | Acção | Microsoft.PolicyInsights/remediations/delete | Elimine remediações de política. |
> | Acção | Microsoft.PolicyInsights/remediations/listDeployments/read | Lista as implementações necessárias para por uma remediação de política. |
> | Acção | Microsoft.PolicyInsights/remediations/read | Obter remediações de política. |
> | Acção | Microsoft.PolicyInsights/remediations/write | Criar ou atualizar remediações de Microsoft Policy. |
> | Acção | Microsoft.PolicyInsights/unregister/action | Anula o registo do fornecedor de recursos do Microsoft Policy Insights. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Portal/consoles/delete | Remove a instância da Cloud Shell. |
> | Acção | Microsoft.Portal/consoles/read | Lê a instância da Cloud Shell. |
> | Acção | Microsoft.Portal/consoles/write | Crie ou atualize uma instância da Cloud Shell. |
> | Acção | Microsoft.Portal/dashboards/delete | Remove o dashboard da subscrição. |
> | Acção | Microsoft.Portal/dashboards/read | Lê os dashboards da subscrição. |
> | Acção | Microsoft.Portal/dashboards/write | Adicionar ou modificar o dashboard para uma subscrição. |
> | Acção | Microsoft.Portal/register/action | Registar no Portal |
> | Acção | Microsoft.Portal/usersettings/delete | Remova as definições de utilizador da Cloud Shell. |
> | Acção | Microsoft.Portal/usersettings/read | Lê as definições de utilizador da Cloud Shell. |
> | Acção | Microsoft.Portal/usersettings/write | Crie ou atualize a definição de utilizador da Cloud Shell. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.PowerBIDedicated/capacities/delete | Elimina o Power BI dedicado a capacidade. |
> | Acção | Microsoft.PowerBIDedicated/capacities/read | Obtém as informações do Power BI dedicado capacidade especificada. |
> | Acção | Microsoft.PowerBIDedicated/capacities/resume/action | Retoma a capacidade. |
> | Acção | Microsoft.PowerBIDedicated/capacities/skus/read | Obter informações do SKU disponíveis para a capacidade |
> | Acção | Microsoft.PowerBIDedicated/capacities/suspend/action | Suspende a capacidade. |
> | Acção | Microsoft.PowerBIDedicated/capacities/write | Cria ou atualiza o Power BI dedicado capacidade especificada. |
> | Acção | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Verificações de que nome da capacidade do Power BI dedicado é válido e não em utilização. |
> | Acção | Microsoft.PowerBIDedicated/locations/operationresults/read | Obtém as informações do resultado da operação especificado. |
> | Acção | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Obtém as informações de estado da operação especificado. |
> | Acção | Microsoft.PowerBIDedicated/operations/read | Obtém as informações de operações |
> | Acção | Microsoft.PowerBIDedicated/register/action | Regista o fornecedor de recursos do Power BI dedicado. |
> | Acção | Microsoft.PowerBIDedicated/skus/read | Obtém as informações dos Skus |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | Acção | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna utilizada pelo serviço |
> | Acção | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Acção | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | Criar um Item de cópia de segurança protegido |
> | Acção | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | Devolve a lista de todos os Itens Protegidos. |
> | Acção | Microsoft.RecoveryServices/Locations/backupStatus/action | Verificar o estado de cópia de segurança para cofres dos serviços de recuperação |
> | Acção | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Validar funcionalidades |
> | Acção | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Verificar a que disponibilidade do nome do recurso é uma API para verificar se o nome do recurso está disponível |
> | Acção | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o estado da operação a uma determinada operação |
> | Acção | Microsoft.RecoveryServices/operations/read | Operação devolve a lista de operações para um fornecedor de recursos |
> | Acção | Microsoft.RecoveryServices/register/action | Tendo em conta subscrição de registos para o fornecedor de recursos |
> | Acção | Microsoft.RecoveryServices/Vaults/backupconfig/read | Devolve a configuração para a recuperação Cofre de serviços. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupconfig/write | Cofre de serviços de configuração de atualizações para a recuperação. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupEngines/read | Devolve todos os servidores de gestão de cópia de segurança registados no cofre. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Eliminar uma intenção de proteção de cópia de segurança |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obtenha uma intenção de proteção de cópia de segurança |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma intenção de proteção de cópia de segurança |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Devolve o estado da operação |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obter todos os contentores protegíveis |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Elimina o contentor registado |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Execute a consulta para cargas de trabalho dentro de um contêiner |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obter todos os itens num contentor |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtém o resultado da Operação efetuada no Contentor de Proteção. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Executa a Cópia de Segurança do Item Protegido. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Elimina o Item protegido |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtém o Resultado da Operação Efetuada nos Itens Protegidos. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Devolve o estado da Operação efetuada nos Itens Protegidos. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devolve os detalhes de objeto do Item protegido |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Aprovisionar recuperação de Item instantâneo para Item protegido |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obter Pontos de Recuperação de Itens Protegidos. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurar Pontos de Recuperação de Itens Protegidos. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revogar recuperação de Item instantâneo para Item protegido |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um Item de cópia de segurança protegido |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Devolve todos os contentores registados |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Cria um contentor registado |
> | Acção | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualiza a lista de contentores |
> | Acção | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Cancelar a tarefa |
> | Acção | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Devolve o Resultado da Operação de Tarefa. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupJobs/read | Devolve todos os objetos de trabalho |
> | Acção | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar tarefas |
> | Acção | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Devolve o Resultado da Operação da Cópia de Segurança do Cofre de Serviços de Recuperação. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupOperations/read | Devolve a operação de cópia de segurança Cofre de serviços de estado de recuperação. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Eliminar uma política de proteção |
> | Acção | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter Resultados da Operação de Política. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter o estado da operação da política. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Devolve todas as políticas de proteção |
> | Acção | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Cria a política de proteção |
> | Acção | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Devolve a lista de todos os Itens Susceptíveis de Proteção. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Devolve a lista de todos os Itens Protegidos. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Devolve todos os contentores pertencentes à subscrição |
> | Acção | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Lista de todos os objetivos de proteção de cópia de segurança |
> | Acção | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Devolve o PIN de segurança Cofre de serviços de informações de recuperação. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Devolve a configuração de armazenamento para a recuperação Cofre de serviços. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Cofre de serviços de configuração de armazenamento de atualizações de recuperação. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Resumos de utilização dos itens e servidores protegidos para dos serviços de recuperação. |
> | Acção | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar a operação no Item protegido |
> | Acção | Microsoft.RecoveryServices/Vaults/certificates/write | A operação atualizar certificado do recurso atualiza o certificado de credencial do recurso/cofre. |
> | Acção | Microsoft.RecoveryServices/Vaults/delete | A operação eliminar cofre elimina o recurso do Azure especificado do tipo "Cofre" |
> | Acção | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Acção | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Acção | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Acção | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter alertas para o Cofre dos serviços de recuperação. |
> | Acção | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | Acção | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Obtém a configuração de notificação de Cofre de serviços de recuperação. |
> | Acção | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Configura notificações de e-mail para cofre dos serviços de recuperação. |
> | Acção | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre obtém um objeto que representa o recurso do Azure do tipo "Cofre" |
> | Acção | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | A operação de anular o registo de contentor pode ser utilizada para anular o registo de um contentor. |
> | Acção | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação obter resultados da operação pode ser utilizada obter o estado da operação e o resultado da operação submetida assincronamente |
> | Acção | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A obter contentores pode ser utilizada a operação obter os contentores registados para um recurso. |
> | Acção | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A operação registar contentor de serviços pode ser utilizada para registar um contentor com o serviço de recuperação. |
> | Acção | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler quaisquer definições de alertas |
> | Acção | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Criar ou atualizar as definições de alertas |
> | Acção | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler todos os eventos |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verifica a Consistência dos Recursos de infraestrutura |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Eliminar quaisquer recursos de infraestrutura |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Implementar a imagem de servidor de processo |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrar recursos de infraestrutura para o AAD |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler quaisquer recursos de infraestrutura |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociar Gateway |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Remover recursos de infraestrutura |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renovar o certificado para recursos de infraestrutura |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Ler quaisquer redes lógicas |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer redes |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Eliminar quaisquer mapeamentos de rede |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Criar ou atualizar quaisquer mapeamentos de rede |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Detetar Item Protegível |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Ler quaisquer contentores de proteção |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Remover o contentor de proteção |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Eliminar quaisquer itens de migração |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migrar o Item |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Ler quaisquer pontos de recuperação de migração |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Ler quaisquer itens de migração |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Migrar de teste |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Teste migrar limpeza |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Criar ou atualizar quaisquer itens de migração |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Ler todos os itens susceptíveis de proteção |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Adicionar discos |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplicar o ponto de recuperação |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Eliminar todos os itens protegidos |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Consolidação de ativação pós-falha |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Ativação Pós-falha Planeada |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler todos os itens protegidos |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler quaisquer pontos de recuperação de replicação |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Remover Item protegido |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Remova os discos |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparar replicação |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Voltar a proteger o Item protegido |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Submeter Comentários |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Ler quaisquer tamanhos de computação de destino |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Ativação Pós-falha de Teste |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Limpeza da ativação pós-falha de teste |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Ativação pós-falha |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Atualização do serviço de mobilidade |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Criar ou atualizar todos os itens protegidos |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Eliminar quaisquer mapeamentos do contentor de proteção |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler quaisquer mapeamentos do contentor de proteção |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Remover mapeamento de contentor de proteção |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Criar ou atualizar quaisquer mapeamentos do contentor de proteção |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Contentor de proteção do comutador |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Criar ou atualizar quaisquer contentores de proteção |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Eliminar quaisquer fornecedores de serviços de recuperação |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler quaisquer fornecedores de serviços de recuperação |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Atualizar fornecedor |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Remover fornecedor de serviços de recuperação |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Criar ou atualizar quaisquer fornecedores de serviços de recuperação |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Ler qualquer classificações de armazenamento |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Eliminar quaisquer mapeamentos de classificação de armazenamento |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Criar ou atualizar quaisquer mapeamentos de classificação de armazenamento |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Eliminar qualquer vCenters |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler qualquer vCenters |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Criar ou atualizar qualquer vCenters |
> | Acção | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Criar ou atualizar quaisquer recursos de infraestrutura |
> | Acção | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Cancelar Tarefa |
> | Acção | Microsoft.RecoveryServices/vaults/replicationJobs/read | Ler todas as tarefas |
> | Acção | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Reiniciar tarefa |
> | Acção | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Retomar tarefa |
> | Acção | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Ler quaisquer itens de migração |
> | Acção | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Acção | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Ler quaisquer redes |
> | Acção | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Eliminar todas as políticas |
> | Acção | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler todas as políticas |
> | Acção | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Criar ou atualizar todas as políticas |
> | Acção | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Ler todos os itens protegidos |
> | Acção | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Ler quaisquer mapeamentos do contentor de proteção |
> | Acção | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Ler quaisquer contentores de proteção |
> | Acção | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Eliminar os planos de recuperação |
> | Acção | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plano de recuperação de consolidação de ativação pós-falha |
> | Acção | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plano de recuperação de ativação pós-falha planeada |
> | Acção | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler quaisquer planos de recuperação |
> | Acção | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Voltar a proteger o plano de recuperação |
> | Acção | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plano de recuperação de ativação pós-falha de teste |
> | Acção | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plano de recuperação de limpeza da ativação pós-falha de teste |
> | Acção | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plano de recuperação de ativação pós-falha |
> | Acção | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Criar ou atualizar qualquer planos de recuperação |
> | Acção | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Ler quaisquer fornecedores de serviços de recuperação |
> | Acção | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Acção | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Ler qualquer classificações de armazenamento |
> | Acção | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Ler qualquer  |
> | Acção | Microsoft.RecoveryServices/vaults/replicationUsages/read | Ler quaisquer utilizações de replicação do Cofre |
> | Acção | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Ler qualquer Estado de funcionamento de replicação do Cofre |
> | Acção | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Atualizar o estado de funcionamento do Cofre |
> | Acção | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Ler qualquer  |
> | Acção | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Criar ou atualizar qualquer  |
> | Acção | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Ler qualquer vCenters |
> | Acção | Microsoft.RecoveryServices/vaults/usages/read | Ler quaisquer utilizações do Cofre |
> | Acção | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização de um Cofre de Serviços de Recuperação. |
> | Acção | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de Token de cofre pode ser utilizada para obter o Token de cofre para operações de back-end ao nível do cofre. |
> | Acção | Microsoft.RecoveryServices/Vaults/write | A operação Criar Cofre cria um recurso do tipo "cofre" do Azure |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Relay/checkNameAvailability/action | Verifica a disponibilidade do espaço de nomes em determinada subscrição. |
> | Acção | Microsoft.Relay/checkNamespaceAvailability/action | Verifica a disponibilidade do espaço de nomes em determinada subscrição. Esta API foi preterida utilize CheckNameAvailability em vez disso. |
> | Acção | Microsoft.Relay/namespaces/authorizationRules/action | Regra de autorização de espaço de nomes de atualizações. Esta API foi preterida. Utilize uma chamada PUT para atualizar a regra de autorização de espaço de nomes em vez disso,.... Esta operação não é suportada na versão 2017-04-01 de API. |
> | Acção | Microsoft.Relay/namespaces/authorizationRules/delete | Elimine regra de autorização de espaço de nomes. Não é possível eliminar a regra de autorização de espaço de nomes predefinido.  |
> | Acção | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Obter Cadeia de Ligação para o Espaço de nomes |
> | Acção | Microsoft.Relay/namespaces/authorizationRules/read | Obter a lista de descrição das Regras de Autorização de Espaços de nomes. |
> | Acção | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Acção | Microsoft.Relay/namespaces/authorizationRules/write | Criar regras de autorização ao nível de espaço de nomes e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o principal e as chaves secundárias podem ser atualizadas. |
> | Acção | Microsoft.Relay/namespaces/Delete | Eliminar Recurso de Espaço de nomes |
> | Acção | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtém as chaves das regras de autorização do espaço de nomes principal da Recuperação Após Desastre |
> | Acção | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter as Regras de Autorização do Espaço de Nomes Principal da Recuperação Após Desastre |
> | Acção | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Desativa a Recuperação Após Desastre e para a replicação de alterações de espaço de nomes principais para secundários. |
> | Acção | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica a disponibilidade do alias do espaço de nomes numa determinada subscrição. |
> | Acção | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Elimina a configuração de recuperação após desastre associada no espaço de nomes. Esta operação só pode ser chamada por meio do espaço de nomes primário. |
> | Acção | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Invoca uma ativação pós-falha GEO DR e reconfigura o alias de espaço de nomes para apontar para o espaço de nomes secundário. |
> | Acção | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Obtém a configuração da Recuperação Após Desastre associada ao espaço de nomes. |
> | Acção | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Cria ou Atualiza a configuração da Recuperação Após Desastre associada ao espaço de nomes. |
> | Acção | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operação para atualizar HybridConnection. Esta operação não é suportada na versão 2017-04-01 de API. Regras de autorização. Utilize uma chamada PUT para atualizar a regra de autorização. |
> | Acção | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operação para eliminar regras de autorização de HybridConnection |
> | Acção | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Obter a cadeia de ligação para HybridConnection |
> | Acção | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Obter a lista de regras de autorização de HybridConnection |
> | Acção | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Acção | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Criar regras de autorização de HybridConnection e atualizar as respetivas propriedades. Os direitos de acesso de regras de autorização podem ser atualizados. |
> | Acção | Microsoft.Relay/namespaces/HybridConnections/Delete | Operação para eliminar o recurso de HybridConnection |
> | Acção | Microsoft.Relay/namespaces/HybridConnections/read | Obter lista de descrições de recursos de HybridConnection |
> | Acção | Microsoft.Relay/namespaces/HybridConnections/write | Criar ou propriedades de HybridConnection de atualização. |
> | Acção | Microsoft.Relay/namespaces/messagingPlan/read | Obtém o plano de mensagens para um espaço de nomes.<br>Esta API foi preterida.<br>As propriedades expostas por intermédio do recurso MessagingPlan são movidas para o recurso de espaço de nomes em versões de API posteriores do (principal)....<br>Esta operação não é suportada na versão 2017-04-01 de API. |
> | Acção | Microsoft.Relay/namespaces/messagingPlan/write | Atualiza o plano de mensagens para um espaço de nomes.<br>Esta API foi preterida.<br>As propriedades expostas por intermédio do recurso MessagingPlan são movidas para o recurso de espaço de nomes em versões de API posteriores do (principal)....<br>Esta operação não é suportada na versão 2017-04-01 de API. |
> | Acção | Microsoft.Relay/namespaces/operationresults/read | Obter o estado da operação de Espaço de nomes |
> | Acção | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de descrições de recursos do espaço de nomes métricas |
> | Acção | Microsoft.Relay/namespaces/read | Obter a lista de Descrição de Recursos de Espaço de nomes |
> | Acção | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Remova o espaço de nomes do ACS |
> | Acção | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operação para atualizar WcfRelay. Esta operação não é suportada na versão 2017-04-01 de API. Regras de autorização. Utilize uma chamada PUT para atualizar a regra de autorização. |
> | Acção | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operação para eliminar regras de autorização de WcfRelay |
> | Acção | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Obter a cadeia de ligação para WcfRelay |
> | Acção | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Obter a lista de regras de autorização de WcfRelay |
> | Acção | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Acção | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Criar regras de autorização de WcfRelay e atualizar as respetivas propriedades. Os direitos de acesso de regras de autorização podem ser atualizados. |
> | Acção | Microsoft.Relay/namespaces/WcfRelays/Delete | Operação para eliminar o recurso de WcfRelay |
> | Acção | Microsoft.Relay/namespaces/WcfRelays/read | Obter lista de descrições de recursos de WcfRelay |
> | Acção | Microsoft.Relay/namespaces/WcfRelays/write | Criar ou propriedades de WcfRelay de atualização. |
> | Acção | Microsoft.Relay/namespaces/write | Criar um recurso de espaço de nomes e Atualize as respetivas propriedades. As etiquetas e a capacidade do espaço de nomes são propriedades que podem ser atualizadas. |
> | Acção | Microsoft.Relay/operations/read | Obter Operações |
> | Acção | Microsoft.Relay/register/action | Regista a subscrição do fornecedor de recursos de Reencaminhamento e ativa a criação de recursos de Reencaminhamento |
> | Acção | Microsoft.Relay/unregister/action | Regista a subscrição do fornecedor de recursos de Reencaminhamento e ativa a criação de recursos de Reencaminhamento |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Obtém o estado de disponibilidade para o recurso especificado. |
> | Acção | Microsoft.ResourceHealth/AvailabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Acção | Microsoft.ResourceHealth/events/read | Obter Eventos de Estado de Funcionamento do Serviço da subscrição indicada |
> | Acção | Microsoft.Resourcehealth/healthevent/action | Indica a alteração no estado de funcionamento para o recurso especificado |
> | Acção | Microsoft.Resourcehealth/healthevent/Activated/action | Indica a alteração no estado de funcionamento para o recurso especificado |
> | Acção | Microsoft.Resourcehealth/healthevent/InProgress/action | Indica a alteração no estado de funcionamento para o recurso especificado |
> | Acção | Microsoft.Resourcehealth/healthevent/Pending/action | Indica a alteração no estado de funcionamento para o recurso especificado |
> | Acção | Microsoft.Resourcehealth/healthevent/Resolved/action | Indica a alteração no estado de funcionamento para o recurso especificado |
> | Acção | Microsoft.Resourcehealth/healthevent/Updated/action | Indica a alteração no estado de funcionamento para o recurso especificado |
> | Acção | Microsoft.ResourceHealth/impactedResources/read | Obter Recursos Afetados da subscrição indicada |
> | Acção | Microsoft.ResourceHealth/Operations/read | Obter as operações disponíveis para o ResourceHealth Microsoft |
> | Acção | Microsoft.ResourceHealth/register/action | Regista a subscrição para o Microsoft ResourceHealth |
> | Acção | Microsoft.ResourceHealth/unregister/action | Anular o registo da subscrição ResourceHealth Microsoft |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Resources/checkPolicyCompliance/action | Verifique o estado de conformidade de um dado recurso em relação a políticas de recursos. |
> | Acção | Microsoft.Resources/checkResourceName/action | Verificar a validade do nome do recurso. |
> | Acção | Microsoft.Resources/deployments/cancel/action | Cancela uma implementação. |
> | Acção | Microsoft.Resources/deployments/delete | Elimina uma implementação. |
> | Acção | Microsoft.Resources/deployments/operations/read | Obtém ou lista as operações de implementação. |
> | Acção | Microsoft.Resources/deployments/read | Obtém ou lista implementações. |
> | Acção | Microsoft.Resources/deployments/validate/action | Valida uma implementação. |
> | Acção | Microsoft.Resources/deployments/whatIf/action | Prevê a alterações de implementação do modelo. |
> | Acção | Microsoft.Resources/deployments/write | Cria ou atualiza uma implementação. |
> | Acção | Microsoft.Resources/links/delete | Elimina uma ligação de recursos. |
> | Acção | Microsoft.Resources/links/read | Obtém ou lista ligações de recursos. |
> | Acção | Microsoft.Resources/links/write | Cria ou atualiza uma ligação de recursos. |
> | Acção | Microsoft.Resources/marketplace/purchase/action | Adquire um recurso do mercado. |
> | Acção | Microsoft.Resources/providers/read | Obter a lista de fornecedores. |
> | Acção | Microsoft.Resources/resources/read | Obter a lista de recursos com base em filtros. |
> | Acção | Microsoft.Resources/subscriptions/locations/read | Obtém a lista de localizações suportadas. |
> | Acção | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação de subscrição. |
> | Acção | Microsoft.Resources/subscriptions/providers/read | Obtém ou lista fornecedores de recursos. |
> | Acção | Microsoft.Resources/subscriptions/read | Obtém a lista de subscrições. |
> | Acção | Microsoft.Resources/subscriptions/resourceGroups/delete | Elimina um grupo de recursos e respetivos recursos. |
> | Acção | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Obtém ou lista as operações de implementação. |
> | Acção | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Obtém ou lista os estados da operação de implementação. |
> | Acção | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Obtém ou lista implementações. |
> | Acção | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Cria ou atualiza uma implementação. |
> | Acção | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Move os recursos de um grupo de recursos para outro. |
> | Acção | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Acção | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Obtém os recursos do grupo de recursos. |
> | Acção | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Validar a movimentação de recursos de um grupo de recursos para outro. |
> | Acção | Microsoft.Resources/subscriptions/resourceGroups/write | Cria ou atualiza um grupo de recursos. |
> | Acção | Microsoft.Resources/subscriptions/resources/read | Obtém os recursos de uma subscrição. |
> | Acção | Microsoft.Resources/subscriptions/tagNames/delete | Elimina uma etiqueta de subscrição. |
> | Acção | Microsoft.Resources/subscriptions/tagNames/read | Obtém ou lista etiquetas de subscrição. |
> | Acção | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Elimina um valor de etiqueta de subscrição. |
> | Acção | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Obtém ou lista valores de etiqueta de subscrição. |
> | Acção | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Adiciona um valor de etiqueta de subscrição. |
> | Acção | Microsoft.Resources/subscriptions/tagNames/write | Adiciona uma etiqueta de subscrição. |
> | Acção | Microsoft.Resources/tags/delete | Remove todas as marcas num recurso. |
> | Acção | Microsoft.Resources/tags/read | Obtém todas as marcas num recurso. |
> | Acção | Microsoft.Resources/tags/write | Atualiza as etiquetas num recurso por substituição ou intercalação etiquetas existentes com um novo conjunto de etiquetas ou remover etiquetas existentes. |
> | Acção | Microsoft.Resources/tenants/read | Obtém a lista de inquilinos. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Scheduler/jobcollections/delete | Elimina a coleção de tarefas. |
> | Acção | Microsoft.Scheduler/jobcollections/disable/action | Desativa a coleção de tarefas. |
> | Acção | Microsoft.Scheduler/jobcollections/enable/action | Ativa a coleção de tarefas. |
> | Acção | Microsoft.Scheduler/jobcollections/jobs/delete | Elimina a tarefa. |
> | Acção | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Gera a definição da Aplicação Lógica com base numa Tarefa do Scheduler. |
> | Acção | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Obtém o histórico de tarefas. |
> | Acção | Microsoft.Scheduler/jobcollections/jobs/read | Obtém a tarefa. |
> | Acção | Microsoft.Scheduler/jobcollections/jobs/run/action | Executa a tarefa. |
> | Acção | Microsoft.Scheduler/jobcollections/jobs/write | Cria ou atualiza a tarefa. |
> | Acção | Microsoft.Scheduler/jobcollections/read | Obter Coleção de Tarefas |
> | Acção | Microsoft.Scheduler/jobcollections/write | Cria ou atualiza a coleção de tarefas. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Search/checkNameAvailability/action | Verifica a disponibilidade do nome do serviço. |
> | Acção | Microsoft.Search/operations/read | Apresenta uma lista de todas as operações disponíveis do fornecedor Microsoft.Search. |
> | Acção | Microsoft.Search/register/action | Regista a subscrição para o fornecedor de recursos de pesquisa e permite a criação de serviços de pesquisa. |
> | Acção | Microsoft.Search/searchServices/createQueryKey/action | Cria a chave de consulta. |
> | Acção | Microsoft.Search/searchServices/delete | Elimina o serviço de pesquisa. |
> | Acção | Microsoft.Search/searchServices/deleteQueryKey/delete | Elimina a chave de consulta. |
> | Acção | Microsoft.Search/searchServices/listAdminKeys/action | Lê chaves de administração. |
> | Acção | Microsoft.Search/searchServices/listQueryKeys/read | Devolve a lista das chaves de consulta API para o determinado serviço de Azure Search. |
> | Acção | Microsoft.Search/searchServices/read | Lê o serviço de pesquisa. |
> | Acção | Microsoft.Search/searchServices/regenerateAdminKey/action | Regenera a chave de administrador. |
> | Acção | Microsoft.Search/searchServices/start/action | Inicia o serviço de pesquisa. |
> | Acção | Microsoft.Search/searchServices/stop/action | Para o serviço de pesquisa. |
> | Acção | Microsoft.Search/searchServices/write | Cria ou atualiza o serviço de pesquisa. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Impõe o tráfego de determinado regras de proteção através da criação de regras de segurança correspondente nos grupos de segurança de rede especificado |
> | Acção | Microsoft.Security/adaptiveNetworkHardenings/read | Recurso protegido de recomendações de sistema de proteção de rede adaptável obtém do Azure |
> | Acção | Microsoft.Security/advancedThreatProtectionSettings/read | Obtém as definições de proteção de ameaças avançadas para o recurso |
> | Acção | Microsoft.Security/advancedThreatProtectionSettings/write | As definições de proteção avançada contra ameaças para o recurso de atualizações |
> | Acção | Microsoft.Security/alerts/read | Obtém todos os alertas de segurança disponíveis |
> | Acção | Microsoft.Security/applicationWhitelistings/read | Obtém o whitelistings de aplicação |
> | Acção | Microsoft.Security/applicationWhitelistings/write | Cria uma lista de permissões de aplicação nova ou atualiza um já existente |
> | Acção | Microsoft.Security/complianceResults/read | Obtém os resultados de compatibilidade para o recurso |
> | Acção | Microsoft.Security/informationProtectionPolicies/read | Obtém as informações de políticas de proteção para o recurso |
> | Acção | Microsoft.Security/informationProtectionPolicies/write | As políticas de proteção de informações para o recurso de atualizações |
> | Acção | Microsoft.Security/locations/alerts/activate/action | Ativar um alerta de segurança |
> | Acção | Microsoft.Security/locations/alerts/dismiss/action | Dispensar um alerta de segurança |
> | Acção | Microsoft.Security/locations/alerts/read | Obtém todos os alertas de segurança disponíveis |
> | Acção | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Elimina a política de acesso de rede just-in-time |
> | Acção | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Inicia um pedido de política de acesso de rede just-in-time |
> | Acção | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Obtém as políticas de acesso de rede just-in-time |
> | Acção | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Cria uma nova política de acesso de rede just-in-time ou atualiza um já existente |
> | Acção | Microsoft.Security/locations/read | Obtém a localização de dados de segurança |
> | Acção | Microsoft.Security/locations/tasks/activate/action | Ativar uma recomendação de segurança |
> | Acção | Microsoft.Security/locations/tasks/dismiss/action | Dispensar uma recomendação de segurança |
> | Acção | Microsoft.Security/locations/tasks/read | Obtém todas as recomendações de segurança disponíveis |
> | Acção | Microsoft.Security/locations/tasks/resolve/action | Resolver uma recomendação de segurança |
> | Acção | Microsoft.Security/locations/tasks/start/action | Iniciar uma recomendação de segurança |
> | Acção | Microsoft.Security/policies/read | Obtém a política de segurança |
> | Acção | Microsoft.Security/policies/write | Atualiza a política de segurança |
> | Acção | Microsoft.Security/pricings/delete | Elimina as definições de preços para o âmbito |
> | Acção | Microsoft.Security/pricings/read | Obtém as definições de preços para o âmbito |
> | Acção | Microsoft.Security/pricings/write | Atualiza as definições de preços para o âmbito |
> | Acção | Microsoft.Security/register/action | Regista a subscrição para o Centro de segurança do Azure |
> | Acção | Microsoft.Security/securityContacts/delete | Elimina o contacto de segurança |
> | Acção | Microsoft.Security/securityContacts/read | Obtém o contacto de segurança |
> | Acção | Microsoft.Security/securityContacts/write | Atualiza o contacto de segurança |
> | Acção | Microsoft.Security/securitySolutions/delete | Elimina uma solução de segurança |
> | Acção | Microsoft.Security/securitySolutions/read | Obtém as soluções de segurança |
> | Acção | Microsoft.Security/securitySolutions/write | Cria uma nova solução de segurança ou atualiza um já existente |
> | Acção | Microsoft.Security/securitySolutionsReferenceData/read | Obtém as soluções de segurança de dados de referência |
> | Acção | Microsoft.Security/securityStatuses/read | Obtém a segurança Estados de estado de funcionamento de recursos do Azure |
> | Acção | Microsoft.Security/securityStatusesSummaries/read | Obtém a segurança resumos de Estados para o âmbito |
> | Acção | Microsoft.Security/settings/read | Obtém as definições para o âmbito |
> | Acção | Microsoft.Security/settings/write | Atualiza as definições para o âmbito |
> | Acção | Microsoft.Security/tasks/read | Obtém todas as recomendações de segurança disponíveis |
> | Acção | Microsoft.Security/unregister/action | Anula o registo da subscrição do Centro de segurança do Azure |
> | Acção | Microsoft.Security/webApplicationFirewalls/delete | Elimina uma firewall de aplicações web |
> | Acção | Microsoft.Security/webApplicationFirewalls/read | Obtém a web firewalls de aplicações |
> | Acção | Microsoft.Security/webApplicationFirewalls/write | Cria uma nova firewall de aplicações web ou atualiza um já existente |
> | Acção | Microsoft.Security/workspaceSettings/connect/action | Alterar as definições de restabelecimento de ligação de definições de área de trabalho |
> | Acção | Microsoft.Security/workspaceSettings/delete | Elimina as definições de área de trabalho |
> | Acção | Microsoft.Security/workspaceSettings/read | Obtém as definições de área de trabalho |
> | Acção | Microsoft.Security/workspaceSettings/write | Atualiza as definições de área de trabalho |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.SecurityGraph/diagnosticsettings/delete | A eliminar uma definição de diagnóstico |
> | Acção | Microsoft.SecurityGraph/diagnosticsettings/read | Ler uma definição de diagnóstico |
> | Acção | Microsoft.SecurityGraph/diagnosticsettings/write | Escrever uma definição de diagnóstico |
> | Acção | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Ler um categorias de definição de diagnóstico |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ServiceBus/checkNameAvailability/action | Verifica a disponibilidade do espaço de nomes em determinada subscrição. |
> | Acção | Microsoft.ServiceBus/checkNamespaceAvailability/action | Verifica a disponibilidade do espaço de nomes em determinada subscrição. Esta API foi preterida utilize CheckNameAvailability em vez disso. |
> | Acção | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Elimina as regras de VNet no Fornecedor de Recursos de ServiceBus para a VNet especificada |
> | Acção | Microsoft.ServiceBus/namespaces/authorizationRules/action | Regra de autorização de espaço de nomes de atualizações. Esta API foi preterida. Utilize uma chamada PUT para atualizar a regra de autorização de espaço de nomes em vez disso,.... Esta operação não é suportada na versão 2017-04-01 de API. |
> | Acção | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Elimine regra de autorização de espaço de nomes. Não é possível eliminar a regra de autorização de espaço de nomes predefinido.  |
> | Acção | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Obter Cadeia de Ligação para o Espaço de nomes |
> | Acção | Microsoft.ServiceBus/namespaces/authorizationRules/read | Obter a lista de descrição das Regras de Autorização de Espaços de nomes. |
> | Acção | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Acção | Microsoft.ServiceBus/namespaces/authorizationRules/write | Criar regras de autorização ao nível de espaço de nomes e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o principal e as chaves secundárias podem ser atualizadas. |
> | Acção | Microsoft.ServiceBus/namespaces/Delete | Eliminar Recurso de Espaço de nomes |
> | Acção | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtém as chaves das regras de autorização do espaço de nomes principal da Recuperação Após Desastre |
> | Acção | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter as Regras de Autorização do Espaço de Nomes Principal da Recuperação Após Desastre |
> | Acção | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Desativa a Recuperação Após Desastre e para a replicação de alterações de espaço de nomes principais para secundários. |
> | Acção | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica a disponibilidade do alias do espaço de nomes numa determinada subscrição. |
> | Acção | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Elimina a configuração de recuperação após desastre associada no espaço de nomes. Esta operação só pode ser chamada por meio do espaço de nomes primário. |
> | Acção | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Invoca uma ativação pós-falha GEO DR e reconfigura o alias de espaço de nomes para apontar para o espaço de nomes secundário. |
> | Acção | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Obtém a configuração da Recuperação Após Desastre associada ao espaço de nomes. |
> | Acção | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Cria ou Atualiza a configuração da Recuperação Após Desastre associada ao espaço de nomes. |
> | Acção | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Elimina o filtro do Event Grid associado ao espaço de nomes. |
> | Acção | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Obtém o filtro do Event Grid associado ao espaço de nomes. |
> | Acção | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Cria ou Atualiza o filtro do Event Grid associado ao espaço de nomes. |
> | Acção | Microsoft.ServiceBus/namespaces/eventhubs/read | Obter lista de descrições de recursos do EventHub |
> | Acção | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Eliminar Recurso de Filtro de IP |
> | Acção | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Obter o Recurso de Filtro de IP |
> | Acção | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Criar Recurso de Filtro de IP |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/browse/action | Procurar messges |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/defer/action | Diferir as mensagens |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Receber mensagens |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/schedule/action | Mensagens de Shedule |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Enviar mensagens |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/setstate/action | Estado da sessão de conjunto |
> | Acção | Microsoft.ServiceBus/namespaces/messagingPlan/read | Obtém o plano de mensagens para um espaço de nomes.<br>Esta API foi preterida.<br>As propriedades expostas por intermédio do recurso MessagingPlan são movidas para o recurso de espaço de nomes em versões de API posteriores do (principal)....<br>Esta operação não é suportada na versão 2017-04-01 de API. |
> | Acção | Microsoft.ServiceBus/namespaces/messagingPlan/write | Atualiza o plano de mensagens para um espaço de nomes.<br>Esta API foi preterida.<br>As propriedades expostas por intermédio do recurso MessagingPlan são movidas para o recurso de espaço de nomes em versões de API posteriores do (principal)....<br>Esta operação não é suportada na versão 2017-04-01 de API. |
> | Acção | Microsoft.ServiceBus/namespaces/migrate/action | Migrar operação de espaço de nomes |
> | Acção | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Elimina a configuração da Migração. |
> | Acção | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Obtém a configuração da Migração, o que indica o estado da migração e as operações de replicação pendentes |
> | Acção | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Reverte a migração do espaço de nomes padrão para o premium |
> | Acção | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Atribui o DNS que está associado ao espaço de nomes padrão ao espaço de nomes premium, o que conclui a migração e para a sincronização de recursos do espaço de nomes padrão para o premium |
> | Acção | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Cria ou de configuração de migração de atualizações. Esta ação iniciará a sincronização de recursos do padrão para o espaço de nomes premium |
> | Acção | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Eliminar Recurso de Regra de VNET |
> | Acção | Microsoft.ServiceBus/namespaces/networkrulesets/read | Obtém o recurso de NetworkRuleSet |
> | Acção | Microsoft.ServiceBus/namespaces/networkrulesets/write | Criar Recurso de Regra de VNET |
> | Acção | Microsoft.ServiceBus/namespaces/operationresults/read | Obter o estado da operação de Espaço de nomes |
> | Acção | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obter lista de descrições de recursos do espaço de nomes das definições de diagnóstico |
> | Acção | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obter lista de descrições de recursos do espaço de nomes das definições de diagnóstico |
> | Acção | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obter lista de registos de espaço de nomes descrições de recursos |
> | Acção | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de descrições de recursos do espaço de nomes métricas |
> | Acção | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operação para atualizar a fila. Esta operação não é suportada na versão 2017-04-01 de API. Regras de autorização. Utilize uma chamada PUT para atualizar a regra de autorização. |
> | Acção | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operação para eliminar regras de autorização de fila |
> | Acção | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Obter a cadeia de ligação para a fila |
> | Acção | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Obter a lista de regras de autorização de fila |
> | Acção | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Acção | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Criar regras de autorização de fila e atualizar as respetivas propriedades. Os direitos de acesso de regras de autorização podem ser atualizados. |
> | Acção | Microsoft.ServiceBus/namespaces/queues/Delete | Operação para eliminar o recurso de fila |
> | Acção | Microsoft.ServiceBus/namespaces/queues/read | Obter lista de descrições de recursos da fila |
> | Acção | Microsoft.ServiceBus/namespaces/queues/write | Criar ou propriedades de fila de atualizações. |
> | Acção | Microsoft.ServiceBus/namespaces/read | Obter a lista de Descrição de Recursos de Espaço de nomes |
> | Acção | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Remova o espaço de nomes do ACS |
> | Acção | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Operação para atualizar o tópico. Esta operação não é suportada na versão 2017-04-01 de API. Regras de autorização. Utilize uma chamada PUT para atualizar a regra de autorização. |
> | Acção | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Operação para eliminar regras de autorização de tópico |
> | Acção | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Obter a cadeia de ligação para o tópico |
> | Acção | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Obter a lista de regras de autorização de tópico |
> | Acção | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Acção | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Criar regras de autorização de tópico e atualizar as respetivas propriedades. Os direitos de acesso de regras de autorização podem ser atualizados. |
> | Acção | Microsoft.ServiceBus/namespaces/topics/Delete | Operação para eliminar o recurso de tópico |
> | Acção | Microsoft.ServiceBus/namespaces/topics/read | Obter lista de descrições de recursos de tópico |
> | Acção | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operação para eliminar o recurso de TopicSubscription |
> | Acção | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Obter lista de descrições de recursos de TopicSubscription |
> | Acção | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operação para eliminar o recurso de regra |
> | Acção | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Obter lista de descrições de recursos de regra |
> | Acção | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Criar ou propriedades da regra de atualização. |
> | Acção | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Criar ou propriedades de TopicSubscription de atualização. |
> | Acção | Microsoft.ServiceBus/namespaces/topics/write | Criar ou propriedades de tópico de atualização. |
> | Acção | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Eliminar Recurso de Regra de VNET |
> | Acção | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Obtém o Recurso de Regra de VNET |
> | Acção | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Criar Recurso de Regra de VNET |
> | Acção | Microsoft.ServiceBus/namespaces/write | Criar um recurso de espaço de nomes e Atualize as respetivas propriedades. As etiquetas e a capacidade do espaço de nomes são propriedades que podem ser atualizadas. |
> | Acção | Microsoft.ServiceBus/operations/read | Obter Operações |
> | Acção | Microsoft.ServiceBus/register/action | Regista a subscrição do fornecedor de recursos do ServiceBus e ativa a criação de recursos do ServiceBus |
> | Acção | Microsoft.ServiceBus/sku/read | Obter lista de descrições de recursos de Sku |
> | Acção | Microsoft.ServiceBus/sku/regions/read | Obter lista de descrições de recursos de SkuRegions |
> | Acção | Microsoft.ServiceBus/unregister/action | Regista a subscrição do fornecedor de recursos do ServiceBus e ativa a criação de recursos do ServiceBus |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.ServiceFabric/clusters/applications/delete | Eliminar qualquer Aplicação |
> | Acção | Microsoft.ServiceFabric/clusters/applications/read | Ler qualquer Aplicação |
> | Acção | Microsoft.ServiceFabric/clusters/applications/services/delete | Eliminar qualquer Serviço |
> | Acção | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Ler qualquer Partição |
> | Acção | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Ler qualquer Réplica |
> | Acção | Microsoft.ServiceFabric/clusters/applications/services/read | Ler qualquer Serviço |
> | Acção | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Ler qualquer Estado do Serviço |
> | Acção | Microsoft.ServiceFabric/clusters/applications/services/write | Criar ou Atualizar qualquer Serviço |
> | Acção | Microsoft.ServiceFabric/clusters/applications/write | Criar ou Atualizar qualquer Aplicação |
> | Acção | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Eliminar qualquer Tipo de Aplicação |
> | Acção | Microsoft.ServiceFabric/clusters/applicationTypes/read | Ler qualquer Tipo de Aplicação |
> | Acção | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Eliminar qualquer Versão do Tipo de Aplicação |
> | Acção | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Ler qualquer Versão do Tipo de Aplicação |
> | Acção | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Criar ou Atualizar qualquer Versão do Tipo de Aplicação |
> | Acção | Microsoft.ServiceFabric/clusters/applicationTypes/write | Criar ou Atualizar qualquer Tipo de Aplicação |
> | Acção | Microsoft.ServiceFabric/clusters/delete | Eliminar qualquer Cluster |
> | Acção | Microsoft.ServiceFabric/clusters/nodes/read | Ler qualquer Nó |
> | Acção | Microsoft.ServiceFabric/clusters/read | Ler qualquer Cluster |
> | Acção | Microsoft.ServiceFabric/clusters/statuses/read | Ler qualquer Estado do Cluster |
> | Acção | Microsoft.ServiceFabric/clusters/write | Criar ou Atualizar qualquer Cluster |
> | Acção | Microsoft.ServiceFabric/locations/clusterVersions/read | Ler qualquer Versão de Cluster |
> | Acção | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Ler qualquer Versão de Cluster de um ambiente específico |
> | Acção | Microsoft.ServiceFabric/locations/operationresults/read | Ler quaisquer Resultados da Operação |
> | Acção | Microsoft.ServiceFabric/locations/operations/read | Ler quaisquer Operações por localização |
> | Acção | Microsoft.ServiceFabric/operations/read | Ler quaisquer Operações Disponíveis |
> | Acção | Microsoft.ServiceFabric/register/action | Registar qualquer Ação |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.SignalRService/locations/checknameavailability/action | Verifica se um nome está disponível para utilização com um novo serviço SignalR |
> | Acção | Microsoft.SignalRService/locations/operationresults/signalr/read | Consultar o estado de uma operação de asynchorous |
> | Acção | Microsoft.SignalRService/locations/operationstatus/operationId/read |  |
> | Acção | Microsoft.SignalRService/locations/usages/read | Obter as utilizações de quota para o serviço Azure SignalR |
> | Acção | Microsoft.SignalRService/operationresults/read | Consultar o estado de uma operação de asynchorous |
> | Acção | Microsoft.SignalRService/operationstatus/read |  |
> | Acção | Microsoft.SignalRService/register/action | Regista o fornecedor de recursos de 'Microsoft.SignalRService' com uma subscrição |
> | Acção | Microsoft.SignalRService/SignalR/delete | Eliminar todo o serviço SignalR |
> | Acção | Microsoft.SignalRService/SignalR/listFeatures/read | Lista personalizada FeatureFlags de um serviço de SignalR. Valores excluídos do padrão. |
> | Acção | Microsoft.SignalRService/SignalR/listkeys/action | Ver o valor das chaves de acesso do SignalR, no portal de gestão ou por meio da API |
> | Acção | Microsoft.SignalRService/SignalR/read | Ver definições e configurações do SignalR, no portal de gestão ou por meio da API |
> | Acção | Microsoft.SignalRService/SignalR/regeneratekey/action | Altere o valor das chaves de acesso do SignalR no portal de gestão ou por meio da API |
> | Acção | Microsoft.SignalRService/SignalR/restart/action | Para reiniciar um serviço Azure SignalR no portal de gestão ou por meio da API. Haverá um determinado período de indisponibilidade. |
> | Acção | Microsoft.SignalRService/SignalR/switchFeatures/action | Alternar/desativar SignalR FeatureFlags com propriedades suportadas no portal de gestão ou por meio da API. |
> | Acção | Microsoft.SignalRService/SignalR/write | Modificar as definições e configurações no portal de gestão ou por meio da API do SignalR |
> | Acção | Microsoft.SignalRService/unregister/action | Anula o registo do fornecedor de recursos de 'Microsoft.SignalRService' com uma subscrição |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Lista artefactos da aplicação de definição da aplicação. |
> | Acção | Microsoft.Solutions/applicationDefinitions/delete | Remove uma definição de aplicação. |
> | Acção | Microsoft.Solutions/applicationDefinitions/read | Obtém uma lista de definições de aplicação. |
> | Acção | Microsoft.Solutions/applicationDefinitions/write | Adicionar ou modificar uma definição de aplicação. |
> | Acção | Microsoft.Solutions/applications/applicationArtifacts/read | Lista artefactos da aplicação. |
> | Acção | Microsoft.Solutions/applications/delete | Remove uma aplicação. |
> | Acção | Microsoft.Solutions/applications/read | Obtém uma lista de aplicações. |
> | Acção | Microsoft.Solutions/applications/refreshPermissions/action | Atualiza a aplicação permissões. |
> | Acção | Microsoft.Solutions/applications/updateAccess/action | Atualiza o acesso à aplicação. |
> | Acção | Microsoft.Solutions/applications/write | Cria uma aplicação. |
> | Acção | Microsoft.Solutions/jitRequests/delete | Remover um JitRequest |
> | Acção | Microsoft.Solutions/jitRequests/read | Obtém uma lista de JitRequests |
> | Acção | Microsoft.Solutions/jitRequests/write | Cria um JitRequest |
> | Acção | Microsoft.Solutions/locations/operationStatuses/read | Lê o estado da operação do recurso. |
> | Acção | Microsoft.Solutions/register/action | Registe-se nas Soluções. |
> | Acção | Microsoft.Solutions/unregister/action | Anula o registo de Soluções. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Sql/checkNameAvailability/action | Certifique-se de que se fornecido server nome está disponível para o aprovisionamento em todo o mundo para uma determinada subscrição. |
> | Acção | Microsoft.Sql/instancePools/delete | Elimina um conjunto de instância |
> | Acção | Microsoft.Sql/instancePools/read | Obtém um conjunto de instância |
> | Acção | Microsoft.Sql/instancePools/usages/read | Obtém as informações de utilização de um conjunto de instância |
> | Acção | Microsoft.Sql/instancePools/write | Cria ou atualiza um conjunto de instância |
> | Acção | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Obter o resultado do blob de servidor expandida operação de definição de política de auditoria |
> | Acção | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Obter Resultado da operação de definição de política de auditoria o blob de servidor |
> | Acção | Microsoft.Sql/locations/capabilities/read | Obtém os recursos para esta subscrição numa localização determinada |
> | Acção | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Obtém o estado de uma operação de base de dados. |
> | Acção | Microsoft.Sql/locations/databaseOperationResults/read | Obtém o estado de uma operação de base de dados. |
> | Acção | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Obtém em curso operações no servidor foi eliminado |
> | Acção | Microsoft.Sql/locations/deletedServerOperationResults/read | Obtém em curso operações no servidor foi eliminado |
> | Acção | Microsoft.Sql/locations/deletedServers/read | Devolve a lista de servidores foi eliminadas ou obtém as propriedades para o servidor especificado foi eliminado. |
> | Acção | Microsoft.Sql/locations/deletedServers/recover/action | Recuperar um servidor foi eliminado |
> | Acção | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Obtém a operação assíncrona do azure para uma operação assíncrona do conjunto elástico |
> | Acção | Microsoft.Sql/locations/elasticPoolOperationResults/read | Obtém o resultado de uma operação de conjunto elástico. |
> | Acção | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Obtém em curso operações no protetor de encriptação de encriptação de dados transparente |
> | Acção | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Obtém em curso operações no protetor de encriptação de encriptação de dados transparente |
> | Acção | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Obter o resultado do blob de servidor expandida operação de definição de política de auditoria |
> | Acção | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Obter o resultado do blob de servidor expandida operação de definição de política de auditoria |
> | Acção | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Obtém o estado de uma operação de regra de firewall. |
> | Acção | Microsoft.Sql/locations/firewallRulesOperationResults/read | Obtém o estado de uma operação de regra de firewall. |
> | Acção | Microsoft.Sql/locations/instanceFailoverGroups/delete | Elimina um grupo de ativação pós-falha de instância existente. |
> | Acção | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Executa a ativação pós-falha planeada num grupo de ativação pós-falha de instância existente. |
> | Acção | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Executa a ativação pós-falha forçada num grupo de ativação pós-falha de instância existente. |
> | Acção | Microsoft.Sql/locations/instanceFailoverGroups/read | Devolve a lista de ativação pós-falha de instância de grupos ou obtém as propriedades para o grupo de ativação pós-falha de instância especificada. |
> | Acção | Microsoft.Sql/locations/instanceFailoverGroups/write | Cria um grupo de ativação pós-falha de instância com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o grupo de ativação pós-falha de instância especificada. |
> | Acção | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Obtém o estado de uma operação de agrupamento de instância |
> | Acção | Microsoft.Sql/locations/instancePoolOperationResults/read | Obtém o resultado de uma operação de agrupamento de instância |
> | Acção | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Devolve os detalhes de um ponto final de interface específica operação assíncrona do Azure |
> | Acção | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Devolve os detalhes da operação de perfil do ponto final de interface especificada |
> | Acção | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Obtém o estado de uma operação do agente de tarefa. |
> | Acção | Microsoft.Sql/locations/jobAgentOperationResults/read | Obtém o resultado de uma operação do agente de tarefa. |
> | Acção | Microsoft.Sql/locations/longTermRetentionBackups/read | Lista as cópias de segurança de retenção de longo prazo para cada base de dados em cada servidor num local |
> | Acção | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Lista as cópias de segurança de retenção de longo prazo para cada base de dados num servidor |
> | Acção | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Elimina uma cópia de segurança de retenção de longo prazo |
> | Acção | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Lista as cópias de segurança de retenção de longo prazo para uma base de dados |
> | Acção | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Conclusão da operação de restauro de base de dados gerida |
> | Acção | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Obtém em curso operações no protetor de encriptação da instância gerida de encriptação de dados transparente |
> | Acção | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Obtém em curso operações no protetor de encriptação da instância gerida de encriptação de dados transparente |
> | Acção | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Obtém em curso operações na encriptação de dados transparente gerida chaves de instância |
> | Acção | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Obtém em curso operações na encriptação de dados transparente gerida chaves de instância |
> | Acção | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Obtém em curso operações de criptografia de dados transparente de base de dados gerida |
> | Acção | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Obtém em curso operações de criptografia de dados transparente de base de dados gerida |
> | Acção | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Obtém o resultado de uma operação de proxy de ligação de ponto final privado |
> | Acção | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Obtém o resultado de uma operação de proxy de ligação de ponto final privado |
> | Acção | Microsoft.Sql/locations/read | Obtém as localizações disponíveis para uma determinada subscrição |
> | Acção | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Obtém em curso operações sobre chaves de servidor de encriptação de dados transparente |
> | Acção | Microsoft.Sql/locations/serverKeyOperationResults/read | Obtém em curso operações sobre chaves de servidor de encriptação de dados transparente |
> | Acção | Microsoft.Sql/locations/syncAgentOperationResults/read | Obter Resultado da operação de recursos do agente de sincronização |
> | Acção | Microsoft.Sql/locations/syncDatabaseIds/read | Obter os ids de base de dados de sincronização para uma determinada região e subscrição |
> | Acção | Microsoft.Sql/locations/syncGroupOperationResults/read | Obter Resultado da operação de recursos do grupo de sincronização |
> | Acção | Microsoft.Sql/locations/syncMemberOperationResults/read | Obter Resultado da operação de recursos de membro de sincronização |
> | Acção | Microsoft.Sql/locations/usages/read | Obtém uma coleção de métrica de utilização para esta subscrição numa localização |
> | Acção | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Devolve os detalhes das regras de rede virtual especificada operação assíncrona do azure  |
> | Acção | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Devolve os detalhes da operação de regras de rede virtual especificada  |
> | Acção | Microsoft.Sql/managedInstances/administrators/delete | Elimina um administrador existente de instância gerida. |
> | Acção | Microsoft.Sql/managedInstances/administrators/read | Obtém uma lista de administradores de instância gerida. |
> | Acção | Microsoft.Sql/managedInstances/administrators/write | Cria ou atualiza o administrador de instância gerida com os parâmetros especificados. |
> | Acção | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para uma base de dados gerida |
> | Acção | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para uma base de dados gerida |
> | Acção | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Etiquetas de sensibilidade da lista de um determinado banco de dados |
> | Acção | Microsoft.Sql/managedInstances/databases/delete | Elimina uma base de dados gerida existente |
> | Acção | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Acção | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Acção | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para bases de dados de instância gerida |
> | Acção | Microsoft.Sql/managedInstances/databases/read | Obtém existente a base de dados gerida |
> | Acção | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Etiquetas de sensibilidade da lista de um determinado banco de dados |
> | Acção | Microsoft.Sql/managedInstances/databases/schemas/read | Obter um esquema de base de dados gerido. |
> | Acção | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Obtenha uma coluna de base de dados gerida |
> | Acção | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Eliminar a etiqueta de sensibilidade de uma determinada coluna |
> | Acção | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Desativar as recomendações de sensibilidade sobre uma determinada coluna |
> | Acção | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Ativar as recomendações de sensibilidade sobre uma determinada coluna |
> | Acção | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Obter a etiqueta de sensibilidade de uma determinada coluna |
> | Acção | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Criar ou atualizar a etiqueta de sensibilidade de uma determinada coluna |
> | Acção | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Obter uma tabela de base de dados gerida |
> | Acção | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Obter uma lista de políticas de deteção de ameaças de base de dados gerida configurado para um determinado servidor |
> | Acção | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Alterar a política de deteção de ameaças da base de dados para uma determinada base de dados gerida |
> | Acção | Microsoft.Sql/managedInstances/databases/securityEvents/read | Obtém os eventos de segurança da base de dados gerida |
> | Acção | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Etiquetas de sensibilidade da lista de um determinado banco de dados |
> | Acção | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Obter os detalhes da base de dados transparente de encriptação de dados numa determinada base de dados gerida |
> | Acção | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Alterar a base de dados transparente de dados de encriptação para uma determinada base de dados gerida |
> | Acção | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidades para um determinado banco de dados |
> | Acção | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Obter as políticas de avaliação de vulnerabilidade num givendatabase |
> | Acção | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Remover a linha de base da regra de avaliação vulnerabilidade para um determinado banco de dados |
> | Acção | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Obter a linha de base de regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Acção | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Altere a linha de base da regra de avaliação vulnerabilidade para um determinado banco de dados |
> | Acção | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Converta um resultado de análise existente para um formato legível humano. Se já existe nada acontece |
> | Acção | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Execute a análise de base de dados de avaliação de vulnerabilidade. |
> | Acção | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Devolver a lista de vulnerabilidade de banco de dados de registos de análise de avaliação ou obter o registro de análise para o ID especificado de análise. |
> | Acção | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidades para um determinado banco de dados |
> | Acção | Microsoft.Sql/managedInstances/databases/write | Cria uma nova base de dados ou atualiza a base de dados existente. |
> | Acção | Microsoft.Sql/managedInstances/delete | Elimina uma instância gerida existente. |
> | Acção | Microsoft.Sql/managedInstances/encryptionProtector/read | Devolve uma lista de protetores de encriptação do servidor ou obtém as propriedades para o servidor especificado protetor de encriptação. |
> | Acção | Microsoft.Sql/managedInstances/encryptionProtector/write | Atualize as propriedades para o Protetor de encriptação de servidor especificado. |
> | Acção | Microsoft.Sql/managedInstances/keys/delete | Elimina uma chave de instância gerida do SQL do Azure existente. |
> | Acção | Microsoft.Sql/managedInstances/keys/read | Devolve a lista de instância gerida de chaves ou obtém as propriedades da chave de instância gerida especificada. |
> | Acção | Microsoft.Sql/managedInstances/keys/write | Cria uma chave com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a chave de instância gerida especificada. |
> | Acção | Microsoft.Sql/managedInstances/metricDefinitions/read | Obter definições das métricas de instância gerida |
> | Acção | Microsoft.Sql/managedInstances/metrics/read | Obter métricas de instância gerida |
> | Acção | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Acção | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Acção | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para instâncias geridas |
> | Acção | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para instâncias geridas |
> | Acção | Microsoft.Sql/managedInstances/read | Devolve a lista de instâncias geridas ou obtém as propriedades para a instância gerida especificada. |
> | Acção | Microsoft.Sql/managedInstances/recoverableDatabases/read | Devolve uma lista de bases de dados geridas recuperáveis |
> | Acção | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para uma base de dados gerida |
> | Acção | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para uma base de dados gerida |
> | Acção | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Devolve que uma lista de restaurável remover bases de dados geridas. |
> | Acção | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Obter uma lista de políticas de deteção de ameaças do servidor gerido configurado para um determinado servidor |
> | Acção | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Alterar a política de deteção de ameaças do servidor gerido para um determinado servidor gerido |
> | Acção | Microsoft.Sql/managedInstances/tdeCertificates/action | Certificado de TDE criar/atualizar |
> | Acção | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidades para uma determinada instância gerida |
> | Acção | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Obter as políticas de avaliação de vulnerabilidade numa dada instância gerida |
> | Acção | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidades para uma determinada instância gerida |
> | Acção | Microsoft.Sql/managedInstances/write | Cria uma instância gerida com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a instância gerida especificada. |
> | Acção | Microsoft.Sql/operations/read | Obtém as operações de REST disponíveis |
> | Acção | Microsoft.Sql/register/action | Regista a subscrição para o fornecedor de recursos da base de dados do Microsoft SQL e permite a criação de bases de dados do Microsoft SQL. |
> | Acção | Microsoft.Sql/servers/administratorOperationResults/read | Obtém em curso operações em administradores de servidor |
> | Acção | Microsoft.Sql/servers/administrators/delete | Eliminar o administrador do servidor |
> | Acção | Microsoft.Sql/servers/administrators/read | Obter detalhes de administrador do servidor |
> | Acção | Microsoft.Sql/servers/administrators/write | Criar ou atualizar o administrador do servidor |
> | Acção | Microsoft.Sql/servers/advisors/read | Devolve a lista de assistentes disponíveis para o servidor |
> | Acção | Microsoft.Sql/servers/advisors/recommendedActions/read | Devolve a lista de ações recomendadas do advisor especificado para o servidor |
> | Acção | Microsoft.Sql/servers/advisors/recommendedActions/write | Aplicar a ação recomendada no servidor |
> | Acção | Microsoft.Sql/servers/advisors/write | Atualizações de autom-execução estado de um assistente no nível do servidor. |
> | Acção | Microsoft.Sql/servers/auditingPolicies/read | Obter os detalhes da tabela de servidor padrão configurada num determinado servidor de política de auditoria |
> | Acção | Microsoft.Sql/servers/auditingPolicies/write | Alterar a predefinição servidor auditoria de tabelas para um determinado servidor |
> | Acção | Microsoft.Sql/servers/auditingSettings/operationResults/read | Obter Resultado da operação de definição de política de auditoria o blob de servidor |
> | Acção | Microsoft.Sql/servers/auditingSettings/read | Obter os detalhes da política auditoria de BLOBs de servidor configurado num determinado servidor |
> | Acção | Microsoft.Sql/servers/auditingSettings/write | Alterar a auditoria de BLOBs de servidor para um determinado servidor |
> | Acção | Microsoft.Sql/servers/automaticTuning/read | Devolve as definições de sintonização automática para o servidor |
> | Acção | Microsoft.Sql/servers/automaticTuning/write | Atualizações de definições de sintonização automática para o servidor e devolve as definições atualizadas |
> | Acção | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Elimina um cofre de arquivamento de cópia de segurança existente. |
> | Acção | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Esta operação é utilizada para obter um cofre de retenção de cópia de segurança de longo prazo. Devolve informações sobre o Cofre registado este servidor |
> | Acção | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Esta operação é utilizada para registar uma retenção de cópia de segurança de longo prazo cofre para um servidor |
> | Acção | Microsoft.Sql/servers/communicationLinks/delete | Elimina uma ligação de comunicação de servidor existente. |
> | Acção | Microsoft.Sql/servers/communicationLinks/read | Devolve a lista de ligações de comunicação de um servidor especificado. |
> | Acção | Microsoft.Sql/servers/communicationLinks/write | Criar ou atualizar uma ligação de comunicação do servidor. |
> | Acção | Microsoft.Sql/servers/connectionPolicies/read | Devolve a lista de políticas de ligação do servidor de um servidor especificado. |
> | Acção | Microsoft.Sql/servers/connectionPolicies/write | Criar ou atualizar uma política de ligação do servidor. |
> | Acção | Microsoft.Sql/servers/databases/advisors/read | Devolve a lista de assistentes disponíveis para a base de dados |
> | Acção | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Devolve a lista de ações recomendadas do advisor especificado para a base de dados |
> | Acção | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Aplicar a ação recomendada na base de dados |
> | Acção | Microsoft.Sql/servers/databases/advisors/write | Atualização automática-executar o estado de um assistente no nível da base de dados. |
> | Acção | Microsoft.Sql/servers/databases/auditingPolicies/read | Obter os detalhes da política de auditoria de tabela configurados numa determinada base de dados |
> | Acção | Microsoft.Sql/servers/databases/auditingPolicies/write | Alterar a política de auditoria de tabela para uma determinada base de dados |
> | Acção | Microsoft.Sql/servers/databases/auditingSettings/read | Obter os detalhes da política de auditoria de BLOBs configurados numa determinada base de dados |
> | Acção | Microsoft.Sql/servers/databases/auditingSettings/write | Alterar a política de auditoria de BLOBs para uma determinada base de dados |
> | Acção | Microsoft.Sql/servers/databases/auditRecords/read | Obter os registos de auditoria de Blobs do banco de dados |
> | Acção | Microsoft.Sql/servers/databases/automaticTuning/read | Devolve as definições de sintonização automática para uma base de dados |
> | Acção | Microsoft.Sql/servers/databases/automaticTuning/write | Atualizações de definições de sintonização automática para uma base de dados e retorna as definições atualizadas |
> | Acção | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Obtém o estado de uma operação de base de dados. |
> | Acção | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Devolve a lista de políticas de arquivamento de cópia de segurança de uma base de dados especificada. |
> | Acção | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Criar ou atualizar uma política de arquivo de cópia de segurança da base de dados. |
> | Acção | Microsoft.Sql/servers/databases/connectionPolicies/read | Obter os detalhes da política de ligação configurada num determinado banco de dados |
> | Acção | Microsoft.Sql/servers/databases/connectionPolicies/write | Alterar a política de ligação de um determinado banco de dados |
> | Acção | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Etiquetas de sensibilidade da lista de um determinado banco de dados |
> | Acção | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Devolve a lista de políticas de máscara de dados de base de dados. |
> | Acção | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Eliminar regra de política para um determinado banco de dados de máscara de dados |
> | Acção | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Obter os detalhes dos regra de política configurada num determinado banco de dados de máscara de dados |
> | Acção | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Regra de política para um determinado banco de dados de máscara de dados de alteração |
> | Acção | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Alterar a política para um determinado banco de dados de máscara de dados |
> | Acção | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Devolve as informações de passo de consulta distribuída da consulta de armazém de dados para o ID do passo selecionado |
> | Acção | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Devolve as informações da consulta de distribuição de armazém de dados para o ID de consulta selecionada |
> | Acção | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Obtém as atividades do utilizador de uma instância do SQL Data Warehouse que inclui as consultas em execução e suspensas |
> | Acção | Microsoft.Sql/servers/databases/delete | Elimina uma base de dados existente. |
> | Acção | Microsoft.Sql/servers/databases/export/action | Exportar base de dados SQL do Azure |
> | Acção | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Obter os detalhes da política de auditoria de blob expandido configurados numa determinada base de dados |
> | Acção | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Alterar a política de auditoria de blob expandido para uma determinada base de dados |
> | Acção | Microsoft.Sql/servers/databases/extensions/read | Obtém uma coleção de extensões para a base de dados. |
> | Acção | Microsoft.Sql/servers/databases/extensions/write | Altere a extensão para uma determinada base de dados |
> | Acção | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Obter as políticas de cópia de segurança geo para uma determinada base de dados |
> | Acção | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Criar ou atualizar uma política de geobackup de base de dados |
> | Acção | Microsoft.Sql/servers/databases/importExportOperationResults/read | Obtém em curso operações de importação/exportação |
> | Acção | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Obtém uma lista de janelas de manutenção disponíveis para uma base de dados selecionada. |
> | Acção | Microsoft.Sql/servers/databases/maintenanceWindows/read | Obtém a manutenção definições do windows para uma base de dados selecionada. |
> | Acção | Microsoft.Sql/servers/databases/maintenanceWindows/write | Define configurações do windows para uma base de dados selecionada de manutenção. |
> | Acção | Microsoft.Sql/servers/databases/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados |
> | Acção | Microsoft.Sql/servers/databases/metrics/read | Métricas de retornadas de bases de dados |
> | Acção | Microsoft.Sql/servers/databases/move/action | Altere o nome da base de dados existente. |
> | Acção | Microsoft.Sql/servers/databases/operationResults/read | Obtém o estado de uma operação de base de dados. |
> | Acção | Microsoft.Sql/servers/databases/operations/cancel/action | Cancela a base de dados do Azure SQL pendentes operação assíncrona que ainda não tenha terminada. |
> | Acção | Microsoft.Sql/servers/databases/operations/read | Devolver a lista de operações executadas na base de dados |
> | Acção | Microsoft.Sql/servers/databases/pause/action | Base de dados de armazém de dados SQL do Azure de colocar em pausa |
> | Acção | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Acção | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Acção | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para bases de dados |
> | Acção | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados |
> | Acção | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Devolve a coleção de textos de consulta que correspondem aos parâmetros especificados. |
> | Acção | Microsoft.Sql/servers/databases/queryStore/read | Devolve os valores atuais das definições de consulta Store para a base de dados. |
> | Acção | Microsoft.Sql/servers/databases/queryStore/write | Atualizações de definição de consulta Store para a base de dados |
> | Acção | Microsoft.Sql/servers/databases/read | Devolve a lista de bases de dados ou obtém as propriedades da base de dados especificado. |
> | Acção | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Etiquetas de sensibilidade da lista de um determinado banco de dados |
> | Acção | Microsoft.Sql/servers/databases/replicationLinks/delete | Terminar a relação de replicação forçadamente e com potencial perda de dados |
> | Acção | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Ativação pós-falha após a sincronização de tudo é alterado de primário, tornar esta base de dados em relationship\u0027s os replicação primário e tornar remoto primário para uma secundária |
> | Acção | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Ativação pós-falha imediatamente com potencial perda de dados, tornando esta base de dados em relationship\u0027s os replicação primária e tornar remoto primário para uma secundária |
> | Acção | Microsoft.Sql/servers/databases/replicationLinks/read | Devolve a lista de replicação liga ou obtém as propriedades para as ligações de replicação especificada. |
> | Acção | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Terminar a relação de replicação forçadamente ou após a sincronização com o parceiro |
> | Acção | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Atualizar o modo de replicação para a ligação para o modo síncrono ou assíncrono |
> | Acção | Microsoft.Sql/servers/databases/restorePoints/action | Cria um novo ponto de restauro |
> | Acção | Microsoft.Sql/servers/databases/restorePoints/delete | Elimina um ponto de restauro da base de dados. |
> | Acção | Microsoft.Sql/servers/databases/restorePoints/read | Devolve os pontos para a base de dados de restauro. |
> | Acção | Microsoft.Sql/servers/databases/resume/action | Base de dados de armazém de dados SQL do Azure de retomar |
> | Acção | Microsoft.Sql/servers/databases/schemas/read | Obter um esquema de banco de dados. |
> | Acção | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Obtenha uma coluna de base de dados. |
> | Acção | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Eliminar a etiqueta de sensibilidade de uma determinada coluna |
> | Acção | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Desativar as recomendações de sensibilidade sobre uma determinada coluna |
> | Acção | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Ativar as recomendações de sensibilidade sobre uma determinada coluna |
> | Acção | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Obter a etiqueta de sensibilidade de uma determinada coluna |
> | Acção | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Criar ou atualizar a etiqueta de sensibilidade de uma determinada coluna |
> | Acção | Microsoft.Sql/servers/databases/schemas/tables/read | Obter uma tabela de base de dados. |
> | Acção | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Obter a lista de recomendações de índice numa base de dados |
> | Acção | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Aplicar a recomendação do índice |
> | Acção | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Obter uma lista de políticas de deteção de ameaças da base de dados configurada para um determinado servidor |
> | Acção | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Alterar a política de deteção de ameaças da base de dados para um determinado banco de dados |
> | Acção | Microsoft.Sql/servers/databases/securityMetrics/read | Obtém uma coleção de métricas de segurança da base de dados |
> | Acção | Microsoft.Sql/servers/databases/sensitivityLabels/read | Etiquetas de sensibilidade da lista de um determinado banco de dados |
> | Acção | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Devolver a sugestão sobre o dimensionamento da base de dados ou diminua verticalmente com base em estatísticas de execução de consulta para melhorar o desempenho ou reduzir os custos |
> | Acção | Microsoft.Sql/servers/databases/skus/read | Obtém uma coleção de skus disponíveis para uma base de dados |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Cancelar Sincronização do grupo de sincronização |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/delete | Elimina um grupo de sincronização existente. |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Devolver a lista de sincronização de esquemas de banco de dados de hub |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/logs/read | Devolver a lista de registos do grupo de sincronização |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/read | Devolve a lista de sincronização de grupos ou obtém as propriedades para o grupo de sincronização especificado. |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Atualizar o esquema de banco de dados do hub de sincronização |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Obter Resultado da operação de atualização de esquema de sincronização hub |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Elimina um membro de sincronização existente. |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Devolve a lista de membros de sincronização ou obtém as propriedades para o membro de sincronização especificada. |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Atualizar o esquema de membro de sincronização |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Obter Resultado da operação de atualização do esquema de membro de sincronização |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Devolver a lista de sincronização de esquemas de banco de dados do membro |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Cria um membro de sincronização com os parâmetros especificados ou atualizar as propriedades para o membro de sincronização especificada. |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Sincronização do grupo de sincronização de Acionador |
> | Acção | Microsoft.Sql/servers/databases/syncGroups/write | Cria um grupo de sincronização com os parâmetros especificados ou atualiza as propriedades para o grupo de sincronização especificada. |
> | Acção | Microsoft.Sql/servers/databases/topQueries/queryText/action | Devolve o texto de Transact-SQL para o ID de consulta selecionada |
> | Acção | Microsoft.Sql/servers/databases/topQueries/read | Devolve agregado estatísticas de tempo de execução de consulta selecionada no período de tempo selecionado |
> | Acção | Microsoft.Sql/servers/databases/topQueries/statistics/read | Devolve agregado estatísticas de tempo de execução de consulta selecionada no período de tempo selecionado |
> | Acção | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Obtém em curso operações de criptografia de dados transparente |
> | Acção | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Obter o estado e os detalhes do recurso de segurança de criptografia transparente de dados para um determinado banco de dados |
> | Acção | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Alterar o estado de encriptação de dados transparente |
> | Acção | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Atualizar a base de dados do armazém de dados SQL do Azure |
> | Acção | Microsoft.Sql/servers/databases/usages/read | Obtém as informações de utilizações de base de dados do Azure SQL |
> | Acção | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidades para um determinado banco de dados |
> | Acção | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Obter as políticas de avaliação de vulnerabilidade num givendatabase |
> | Acção | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Remover a linha de base da regra de avaliação vulnerabilidade para um determinado banco de dados |
> | Acção | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Obter a linha de base de regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Acção | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Altere a linha de base da regra de avaliação vulnerabilidade para um determinado banco de dados |
> | Acção | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Converta um resultado de análise existente para um formato legível humano. Se já existe nada acontece |
> | Acção | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Execute a análise de base de dados de avaliação de vulnerabilidade. |
> | Acção | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Devolver a lista de vulnerabilidade de banco de dados de registos de análise de avaliação ou obter o registro de análise para o ID especificado de análise. |
> | Acção | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidades para um determinado banco de dados |
> | Acção | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Execute a análise de base de dados de avaliação de vulnerabilidade. |
> | Acção | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Obter o resultado da análise de avaliação de vulnerabilidade da base de dados a operação de execução |
> | Acção | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Obter os detalhes da avaliação de vulnerabilidades configurados numa determinada base de dados |
> | Acção | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Alterar a avaliação de vulnerabilidades para um determinado banco de dados |
> | Acção | Microsoft.Sql/servers/databases/write | Cria uma base de dados com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a base de dados especificada. |
> | Acção | Microsoft.Sql/servers/delete | Elimina um servidor existente. |
> | Acção | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Elimina um configurações de recuperação após desastre existente para um determinado servidor |
> | Acção | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Ativação pós-falha de um DisasterRecoveryConfiguration |
> | Acção | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Forçar a ativação pós-falha um DisasterRecoveryConfiguration |
> | Acção | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Obtém uma coleção de desastre configurações de recuperação que incluem este servidor |
> | Acção | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Alterar a configuração de recuperação de desastres server |
> | Acção | Microsoft.Sql/servers/elasticPoolEstimates/read | Devolve a lista de estimativas de conjunto elástico já criada para este servidor |
> | Acção | Microsoft.Sql/servers/elasticPoolEstimates/write | Cria nova estimativa de conjunto elástico para obter a lista de bases de dados fornecido |
> | Acção | Microsoft.Sql/servers/elasticPools/advisors/read | Devolve a lista de assistentes disponíveis para o conjunto elástico |
> | Acção | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Devolve a lista de ações recomendadas do advisor especificado para o conjunto elástico |
> | Acção | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Aplicar a ação recomendada no conjunto elástico |
> | Acção | Microsoft.Sql/servers/elasticPools/advisors/write | Atualização automática-executar o estado de um assistente no nível de conjunto elástico. |
> | Acção | Microsoft.Sql/servers/elasticPools/databases/read | Obtém uma lista de bases de dados de um conjunto elástico |
> | Acção | Microsoft.Sql/servers/elasticPools/delete | Eliminar o conjunto elástico existente |
> | Acção | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Obter as atividades e detalhes sobre um conjunto de bases de dados elásticas determinado |
> | Acção | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Obter as atividades e detalhes sobre um determinado banco de dados que faz parte do conjunto de bases de dados elásticas |
> | Acção | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados elásticas |
> | Acção | Microsoft.Sql/servers/elasticPools/metrics/read | Métricas de retornadas de conjuntos de bases de dados elásticas |
> | Acção | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Cancela o conjunto elástico de SQL do Azure pendentes operação assíncrona que ainda não tenha terminado. |
> | Acção | Microsoft.Sql/servers/elasticPools/operations/read | Devolver a lista de operações executadas no conjunto elástico |
> | Acção | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Acção | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Acção | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados elásticas |
> | Acção | Microsoft.Sql/servers/elasticPools/read | Obter os detalhes de um conjunto elástico num determinado servidor |
> | Acção | Microsoft.Sql/servers/elasticPools/skus/read | Obtém uma coleção de skus disponíveis para um conjunto elástico |
> | Acção | Microsoft.Sql/servers/elasticPools/write | Criar um novo ou alterar as propriedades do conjunto elástico existente |
> | Acção | Microsoft.Sql/servers/encryptionProtector/read | Devolve uma lista de protetores de encriptação do servidor ou obtém as propriedades para o servidor especificado protetor de encriptação. |
> | Acção | Microsoft.Sql/servers/encryptionProtector/write | Atualize as propriedades para o Protetor de encriptação de servidor especificado. |
> | Acção | Microsoft.Sql/servers/extendedAuditingSettings/read | Obter os detalhes do blob de servidor expandida configurada num determinado servidor de política de auditoria |
> | Acção | Microsoft.Sql/servers/extendedAuditingSettings/write | Alterar a auditoria de BLOBs de servidor expandida para um determinado servidor |
> | Acção | Microsoft.Sql/servers/failoverGroups/delete | Elimina um grupo de ativação pós-falha existente. |
> | Acção | Microsoft.Sql/servers/failoverGroups/failover/action | Executa a ativação pós-falha planeada num grupo de ativação pós-falha existente. |
> | Acção | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Executa a ativação pós-falha forçada num grupo de ativação pós-falha existente. |
> | Acção | Microsoft.Sql/servers/failoverGroups/read | Devolve a lista de ativação pós-falha de grupos ou obtém as propriedades para o grupo de ativação pós-falha especificado. |
> | Acção | Microsoft.Sql/servers/failoverGroups/write | Cria um grupo de ativação pós-falha com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o grupo de ativação pós-falha especificado. |
> | Acção | Microsoft.Sql/servers/firewallRules/delete | Elimina uma regra de firewall do servidor existente. |
> | Acção | Microsoft.Sql/servers/firewallRules/read | Devolve a lista de firewall do servidor de regras ou obtém as propriedades para o servidor especificado de regra de firewall. |
> | Acção | Microsoft.Sql/servers/firewallRules/write | Cria uma regra de firewall do servidor com os parâmetros especificados, atualizar as propriedades para a regra especificada ou substituir todas as regras existentes com a nova regra ou regras de firewall de servidor. |
> | Acção | Microsoft.Sql/servers/import/action | Criar uma nova base de dados no servidor e implementar o esquema e dados a partir de um pacote de DacPac |
> | Acção | Microsoft.Sql/servers/importExportOperationResults/read | Obtém em curso operações de importação/exportação |
> | Acção | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Elimina o perfil de ponto final da interface especificada |
> | Acção | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Devolve as propriedades para o perfil de ponto final da interface especificada |
> | Acção | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Cria um perfil de ponto final de interface com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o ponto final de interface especificada |
> | Acção | Microsoft.Sql/servers/jobAgents/delete | Elimina um agente de tarefa de BD SQL do Azure |
> | Acção | Microsoft.Sql/servers/jobAgents/read | Obtém um agente de tarefa de BD SQL do Azure |
> | Acção | Microsoft.Sql/servers/jobAgents/write | Cria ou atualiza um agente de tarefa de BD SQL do Azure |
> | Acção | Microsoft.Sql/servers/keys/delete | Elimina uma chave de servidor existente. |
> | Acção | Microsoft.Sql/servers/keys/read | Devolve a lista do servidor de chaves ou obtém as propriedades da chave de servidor especificado. |
> | Acção | Microsoft.Sql/servers/keys/write | Cria uma chave com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a chave de servidor especificado. |
> | Acção | Microsoft.Sql/servers/operationResults/read | Obtém em curso operações de servidor |
> | Acção | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Elimina um proxy de ligação de ponto final privado existente |
> | Acção | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Devolve a lista de ponto final privado proxies de ligação ou obtém as propriedades para o proxy de ligação de ponto final privado especificado. |
> | Acção | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Valida uma privada chamada de criar a ligação de ponto de extremidade do lado NRP |
> | Acção | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Cria um proxy de ligação de ponto final privado com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o proxy de ligação de ponto final privado especificado. |
> | Acção | Microsoft.Sql/servers/privateEndpointConnections/delete | Elimina uma ligação de ponto final privado existente |
> | Acção | Microsoft.Sql/servers/privateEndpointConnections/read | Devolve a lista de ligações de ponto final privado ou obtém as propriedades para a ligação de ponto final privado especificado. |
> | Acção | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Tipos de métricas que estão disponíveis para os servidores de retorno |
> | Acção | Microsoft.Sql/servers/read | Devolve a lista de servidores ou obtém as propriedades para o servidor especificado. |
> | Acção | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Obter métricas para os conjuntos de bases de dados elásticas recomendados para um determinado servidor |
> | Acção | Microsoft.Sql/servers/recommendedElasticPools/read | Obter a recomendação para conjuntos de bases de dados elásticas reduzir o custo ou melhorar o desempenho com base na utilização de recursos histórica |
> | Acção | Microsoft.Sql/servers/recoverableDatabases/read | Esta operação é utilizada para recuperação após desastre da base de dados em direto para restaurar a base de dados para o último conhecido bom ponto de cópia de segurança. Devolve informações sobre a última cópia de segurança de boa, mas ele doesn\u0027t realmente restaurar a base de dados. |
> | Acção | Microsoft.Sql/servers/replicationLinks/read | Devolve a lista de replicação liga ou obtém as propriedades para as ligações de replicação especificada. |
> | Acção | Microsoft.Sql/servers/restorableDroppedDatabases/read | Obter uma lista de bases de dados que foram ignorados num determinado servidor ainda dentro de política de retenção. |
> | Acção | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Obter resultados da operação de escrita de política de deteção de ameaças de servidor |
> | Acção | Microsoft.Sql/servers/securityAlertPolicies/read | Obter uma lista de políticas de deteção de ameaças do servidor configurada para um determinado servidor |
> | Acção | Microsoft.Sql/servers/securityAlertPolicies/write | Alterar a política de deteção de ameaças do servidor para um determinado servidor |
> | Acção | Microsoft.Sql/servers/serviceObjectives/read | Obter a lista de serviço os objetivos de nível (também conhecido como escalões de desempenho) disponíveis num determinado servidor |
> | Acção | Microsoft.Sql/servers/syncAgents/delete | Elimina um agente de sincronização existente. |
> | Acção | Microsoft.Sql/servers/syncAgents/generateKey/action | Gerar a chave de registo do agente de sincronização |
> | Acção | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Devolver a lista de sincronizar bases de dados do agente ligado |
> | Acção | Microsoft.Sql/servers/syncAgents/read | Devolve a lista de agentes de sincronização ou obtém as propriedades para o agente de sincronização especificado. |
> | Acção | Microsoft.Sql/servers/syncAgents/write | Cria um agente de sincronização com os parâmetros especificados ou atualiza as propriedades para o agente de sincronização especificado. |
> | Acção | Microsoft.Sql/servers/tdeCertificates/action | Certificado de TDE criar/atualizar |
> | Acção | Microsoft.Sql/servers/usages/read | Devolver a quota de DTU de servidor e consuption DTU atual por todas as bases de dados dentro do servidor |
> | Acção | Microsoft.Sql/servers/virtualNetworkRules/delete | Elimina uma regra de rede Virtual existente |
> | Acção | Microsoft.Sql/servers/virtualNetworkRules/read | Devolve a lista de rede virtual de regras ou obtém as propriedades para a regra de rede virtual especificada. |
> | Acção | Microsoft.Sql/servers/virtualNetworkRules/write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a regra de rede virtual especificada. |
> | Acção | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidades para um determinado servidor |
> | Acção | Microsoft.Sql/servers/vulnerabilityAssessments/read | Obter as políticas de avaliação de vulnerabilidade num determinado servidor |
> | Acção | Microsoft.Sql/servers/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidades para um determinado servidor |
> | Acção | Microsoft.Sql/servers/write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o servidor especificado. |
> | Acção | Microsoft.Sql/unregister/action | Anula o registo da subscrição para o fornecedor de recursos da base de dados do Microsoft SQL e permite a criação de bases de dados do Microsoft SQL. |
> | Acção | Microsoft.Sql/virtualClusters/delete | Elimina um cluster virtual existente. |
> | Acção | Microsoft.Sql/virtualClusters/read | Devolve a lista de clusters virtuais ou obtém as propriedades para o cluster virtual especificado. |
> | Acção | Microsoft.Sql/virtualClusters/write | Atualiza as etiquetas de virtual cluster. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Storage/checknameavailability/read | Verifica se o nome da conta é válido e se não está já a ser utilizado. |
> | Acção | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft.Storage que a rede ou sub-rede virtual está a ser eliminada |
> | Acção | Microsoft.Storage/locations/usages/read | Devolve o limite e a contagem de utilização atual de recursos na subscrição especificada |
> | Acção | Microsoft.Storage/operations/read | Consulta o estado de uma operação assíncrona. |
> | Acção | Microsoft.Storage/register/action | Regista a subscrição do fornecedor de recursos de armazenamento e ativa a criação de contas de armazenamento. |
> | Acção | Microsoft.Storage/skus/read | Lista os Skus suportados pelo Microsoft.Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Devolve o resultado da adição de conteúdo de blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Devolve o resultado da eliminação de um blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | Devolve o resultado da eliminação de um instantâneo automático |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Devolve a lista de blobs sob uma conta com um filtro de tags correspondentes |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Devolve um blob ou uma lista de blobs |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Devolve o resultado da escrita de tags de blobs |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Devolve o resultado da escrita de tags de blobs |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Devolve o resultado de escrever um blob |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Limpar o arquivo legal do contentor de blobs |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Devolve o resultado da eliminação de um contentor |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Eliminar a política de imutabilidade do contentor de blobs |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Expandir a política de imutabilidade do contentor de blobs |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Bloquear a política de imutabilidade do contentor de blobs |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Obter a política de imutabilidade do contentor de blobs |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Colocar a política de imutabilidade do contentor de blobs |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Devolve o resultado do contentor de blobs de concessão |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/containers/read | Devolve um contentor |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/containers/read | Devolve a lista de contentores |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Definir o arquivo legal do contentor de blobs |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/containers/write | Devolve o resultado do contentor de Blobs do patch |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/containers/write | Devolve o resultado do contentor de BLOBs colocado |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Devolve uma chave de delegação de utilizadores para o serviço de blob |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/read | Devolve propriedades ou estatísticas do serviço Blob |
> | Acção | Microsoft.Storage/storageAccounts/blobServices/write | Devolve o resultado das propriedades do serviço de blobs colocado |
> | Acção | Microsoft.Storage/storageAccounts/delete | Elimina uma conta de armazenamento existente. |
> | Acção | Microsoft.Storage/storageAccounts/failover/action | O cliente é capaz de controlar a ativação pós-falha em caso de problemas de disponibilidade |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write |  |
> | Acção | Microsoft.Storage/storageAccounts/fileServices/read | Obter propriedades do serviço do ficheiro |
> | Acção | Microsoft.Storage/storageAccounts/listAccountSas/action | Devolve o token de SAS de Conta para a conta de armazenamento especificada. |
> | Acção | Microsoft.Storage/storageAccounts/listkeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | Acção | Microsoft.Storage/storageAccounts/listServiceSas/action | Devolve o token de SAS de Serviço para a conta de armazenamento especificada. |
> | Acção | Microsoft.Storage/storageAccounts/managementPolicies/delete | Eliminar políticas de gestão da conta de armazenamento |
> | Acção | Microsoft.Storage/storageAccounts/managementPolicies/read | Obter políticas de contas de gestão de armazenamento |
> | Acção | Microsoft.Storage/storageAccounts/managementPolicies/write | Colocar políticas de gestão de conta de armazenamento |
> | Acção | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Devolve o resultado da eliminação de uma fila |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Devolve o resultado da adição de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Devolve o resultado da eliminação de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Devolve o resultado do processamento de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Devolve uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Devolve o resultado de escrever uma mensagem |
> | Acção | Microsoft.Storage/storageAccounts/queueServices/queues/read | Devolve uma fila ou uma lista de filas. |
> | Acção | Microsoft.Storage/storageAccounts/queueServices/queues/write | Devolve o resultado da escrita de uma fila |
> | Acção | Microsoft.Storage/storageAccounts/queueServices/read | Obter propriedades do serviço de Fila |
> | Acção | Microsoft.Storage/storageAccounts/queueServices/read | Propriedades ou estatistas do serviço Fila de devolução. |
> | Acção | Microsoft.Storage/storageAccounts/queueServices/write | Devolve o resultado de definir as propriedades do serviço Fila |
> | Acção | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Acção | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenera as chaves de acesso da conta de armazenamento especificada. |
> | Acção | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Revogar todas as chaves de delegação do utilizador para a conta de armazenamento especificada. |
> | Acção | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Criar/atualizar definições de diagnóstico da conta de armazenamento. |
> | Acção | Microsoft.Storage/storageAccounts/tableServices/read | Obter propriedades do serviço de Tabela |
> | Acção | Microsoft.Storage/storageAccounts/write | Cria uma conta de armazenamento com os parâmetros especificados ou atualiza as propriedades ou etiquetas, ou adiciona um domínio personalizado à conta de armazenamento especificada. |
> | Acção | Microsoft.Storage/usages/read | Devolve o limite e a contagem de utilização atual de recursos na subscrição especificada |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | microsoft.storagesync/locations/checkNameAvailability/action | Verifica esse nome de serviço de sincronização de armazenamento é válido e não está em utilização. |
> | Acção | microsoft.storagesync/locations/workflows/operations/read | Obtém o estado de uma operação assíncrona |
> | Acção | microsoft.storagesync/storageSyncServices/delete | Eliminar quaisquer serviços de sincronização de armazenamento |
> | Acção | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para os serviços de sincronização de armazenamento |
> | Acção | microsoft.storagesync/storageSyncServices/read | Ler quaisquer serviços de sincronização de armazenamento |
> | Acção | microsoft.storagesync/storageSyncServices/registeredServers/delete | Eliminar qualquer servidor registado |
> | Acção | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para o servidor registado |
> | Acção | microsoft.storagesync/storageSyncServices/registeredServers/read | Ler qualquer servidor registado |
> | Acção | microsoft.storagesync/storageSyncServices/registeredServers/write | Criar ou atualizar qualquer servidor registado |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Eliminar quaisquer pontos finais da Cloud |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Obtém o estado de uma operação assíncrona de cópia de segurança/restauro |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Chamar esta ação depois de cópia de segurança |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Chamar esta ação depois de restauro |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Chamar esta ação antes de cópia de segurança |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Chamar esta ação antes de restauro |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Ler quaisquer pontos finais da Cloud |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Restaurar heartbeat |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Criar ou atualizar qualquer pontos finais da Cloud |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/delete | Eliminar grupos de sincronização |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para grupos de sincronização |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/read | Ler todos os grupos de sincronização |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Eliminar quaisquer pontos de extremidade do servidor |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para pontos finais do servidor |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Ler quaisquer pontos de extremidade do servidor |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Chamar esta ação de recuperar ficheiros a um servidor |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Criar ou atualizar qualquer pontos finais do servidor |
> | Acção | microsoft.storagesync/storageSyncServices/syncGroups/write | Criar ou atualizar os grupos de sincronização |
> | Acção | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Obtém o estado de uma operação assíncrona |
> | Acção | microsoft.storagesync/storageSyncServices/workflows/operations/read | Obtém o estado de uma operação assíncrona |
> | Acção | microsoft.storagesync/storageSyncServices/workflows/read | Fluxos de trabalho de leitura |
> | Acção | microsoft.storagesync/storageSyncServices/write | Criar ou atualizar quaisquer serviços de sincronização de armazenamento |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.StorSimple/managers/accessControlRecords/delete | Elimina os registos de controlo de acesso |
> | Acção | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/accessControlRecords/read | Apresenta uma lista ou obtém os registos de controlo de acesso |
> | Acção | Microsoft.StorSimple/managers/accessControlRecords/write | Criar ou atualizar os registos de controlo de acesso |
> | Acção | Microsoft.StorSimple/managers/alerts/read | Apresenta uma lista ou obter alertas |
> | Acção | Microsoft.StorSimple/managers/backups/read | Apresenta uma lista ou obtém o conjunto de cópia de segurança |
> | Acção | Microsoft.StorSimple/managers/bandwidthSettings/delete | Elimina uma existente as definições de largura de banda (8000 Series apenas) |
> | Acção | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Lista os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/bandwidthSettings/read | Lista as definições de largura de banda (apenas na 8000 série) |
> | Acção | Microsoft.StorSimple/managers/bandwidthSettings/write | Cria um novo ou atualiza as definições de largura de banda (8000 Series apenas) |
> | Acção | Microsoft.StorSimple/managers/certificates/write | Criar ou atualizar os certificados |
> | Acção | Microsoft.StorSimple/Managers/certificates/write | A operação atualizar certificado do recurso atualiza o certificado de credencial do recurso/cofre. |
> | Acção | Microsoft.StorSimple/managers/clearAlerts/action | Limpe todos os alertas associados com o Gestor de dispositivos. |
> | Acção | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Configurações suportadas de lista a aplicação da Cloud |
> | Acção | Microsoft.StorSimple/managers/configureDevice/action | Configura um dispositivo |
> | Acção | Microsoft.StorSimple/managers/delete | Elimina os gestores de dispositivos |
> | Acção | Microsoft.StorSimple/Managers/delete | A operação eliminar cofre elimina o recurso do Azure especificado do tipo "Cofre" |
> | Acção | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/alertSettings/read | Apresenta uma lista ou obtém as definições de alerta |
> | Acção | Microsoft.StorSimple/managers/devices/alertSettings/write | Criar ou atualizar as definições de alerta |
> | Acção | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Autorizar o Rollover de chave de encriptação de serviço de dispositivos |
> | Acção | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Efetuar uma cópia de segurança manual para criar um pedido a cópia de segurança de todos os volumes protegidos pela política. |
> | Acção | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Elimina um políticas de cópia de segurança existente (8000 Series apenas) |
> | Acção | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Lista os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/backupPolicies/read | As políticas de lista a cópia de segurança (apenas na 8000 série) |
> | Acção | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Elimina um agendas existentes |
> | Acção | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Lista os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Lista as agendas |
> | Acção | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Cria um novo ou agendas de atualizações |
> | Acção | Microsoft.StorSimple/managers/devices/backupPolicies/write | Cria um novo ou atualiza as políticas de cópia de segurança (8000 Series apenas) |
> | Acção | Microsoft.StorSimple/managers/devices/backups/delete | Elimina o conjunto de cópia de segurança |
> | Acção | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Clone um compartilhamento ou volume com um elemento de cópia de segurança. |
> | Acção | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/backups/read | Apresenta uma lista ou obtém o conjunto de cópia de segurança |
> | Acção | Microsoft.StorSimple/managers/devices/backups/restore/action | Restaure todos os volumes a partir de um conjunto de cópias de segurança. |
> | Acção | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Elimina os grupos de agenda de cópia de segurança |
> | Acção | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Apresenta uma lista ou obtém os grupos de agenda de cópia de segurança |
> | Acção | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Criar ou atualizar os grupos de agenda de cópia de segurança |
> | Acção | Microsoft.StorSimple/managers/devices/chapSettings/delete | Elimina as definições de Chap |
> | Acção | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/chapSettings/read | Apresenta uma lista ou obtém as definições de Chap |
> | Acção | Microsoft.StorSimple/managers/devices/chapSettings/write | Criar ou atualizar as definições de Chap |
> | Acção | Microsoft.StorSimple/managers/devices/deactivate/action | Desativa um dispositivo. |
> | Acção | Microsoft.StorSimple/managers/devices/delete | Elimina os dispositivos |
> | Acção | Microsoft.StorSimple/managers/devices/disks/read | Apresenta uma lista ou obtém os discos |
> | Acção | Microsoft.StorSimple/managers/devices/download/action | Transfira as atualizações para um dispositivo. |
> | Acção | Microsoft.StorSimple/managers/devices/failover/action | Ativação pós-falha do dispositivo. |
> | Acção | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/failoverTargets/read | Apresenta uma lista ou obtém os destinos de ativação pós-falha dos dispositivos |
> | Acção | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Efetuar cópia de segurança de um servidor de ficheiros. |
> | Acção | Microsoft.StorSimple/managers/devices/fileservers/delete | Elimina os servidores de ficheiros |
> | Acção | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Apresenta uma lista ou obtém as métricas |
> | Acção | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Apresenta uma lista ou obtém as definições das métricas |
> | Acção | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/fileservers/read | Apresenta uma lista ou obtém os servidores de ficheiros |
> | Acção | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Elimina as partilhas |
> | Acção | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Apresenta uma lista ou obtém as métricas |
> | Acção | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Apresenta uma lista ou obtém as definições das métricas |
> | Acção | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Apresenta uma lista ou obtém as partilhas |
> | Acção | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Criar ou atualizar as partilhas |
> | Acção | Microsoft.StorSimple/managers/devices/fileservers/write | Criar ou atualizar os servidores de ficheiros |
> | Acção | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Alterar Estado de energia do controlador de grupos de componentes de hardware |
> | Acção | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Lista os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Listar os grupos de componentes de Hardware |
> | Acção | Microsoft.StorSimple/managers/devices/install/action | Instale atualizações num dispositivo. |
> | Acção | Microsoft.StorSimple/managers/devices/installUpdates/action | Instala atualizações nos dispositivos (8000 Series apenas). |
> | Acção | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Efetuar cópia de segurança de um servidor iSCSI. |
> | Acção | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Elimina o iSCSI de servidores |
> | Acção | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Elimina os discos |
> | Acção | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Apresenta uma lista ou obtém as métricas |
> | Acção | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Apresenta uma lista ou obtém as definições das métricas |
> | Acção | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Apresenta uma lista ou obtém os discos |
> | Acção | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Criar ou atualizar os discos |
> | Acção | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Apresenta uma lista ou obtém as métricas |
> | Acção | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Apresenta uma lista ou obtém as definições das métricas |
> | Acção | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/iscsiservers/read | Apresenta uma lista ou obtém o iSCSI de servidores |
> | Acção | Microsoft.StorSimple/managers/devices/iscsiservers/write | Criar ou atualizar o iSCSI de servidores |
> | Acção | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Cancelar uma tarefa em execução |
> | Acção | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Lista os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/jobs/read | Apresenta uma lista ou obtém as tarefas |
> | Acção | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Liste os conjuntos de ativação pós-falha para um dispositivo existente (8000 Series apenas). |
> | Acção | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Destinos de ativação pós-falha de lista dos dispositivos (8000 Series apenas). |
> | Acção | Microsoft.StorSimple/managers/devices/metrics/read | Apresenta uma lista ou obtém as métricas |
> | Acção | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Apresenta uma lista ou obtém as definições das métricas |
> | Acção | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Confirma que uma migração com êxito e confirmá-lo. |
> | Acção | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Lista a confirmar o estado de migração |
> | Acção | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Obter o estado de confirmar da migração. |
> | Acção | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Obter o estado da tarefa de migração estimativa. |
> | Acção | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Obter o estado para a migração. |
> | Acção | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importar configurações de origem para migração |
> | Acção | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Listar a estimativa de migração |
> | Acção | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Liste o estado de migração |
> | Acção | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Lista os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Iniciar Migração usando configurações de origem |
> | Acção | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Inicie uma tarefa para calcular a duração do processo de migração. |
> | Acção | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Lista os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/networkSettings/read | Apresenta uma lista ou obtém as definições de rede |
> | Acção | Microsoft.StorSimple/managers/devices/networkSettings/write | Cria um novo ou atualiza as definições de rede |
> | Acção | Microsoft.StorSimple/managers/devices/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Chave de encriptação pública de lista do Gestor de dispositivos |
> | Acção | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publica o pacote de suporte para um dispositivo existente. Um pacote de suporte do StorSimple é um mecanismo fácil de usar que recolhe todos os registos relevantes para ajudar a resolver quaisquer problemas de dispositivos do StorSimple Support da Microsoft. |
> | Acção | Microsoft.StorSimple/managers/devices/read | Apresenta uma lista ou obtém os dispositivos |
> | Acção | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Procure as atualizações num dispositivo. |
> | Acção | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/securitySettings/read | Lista as definições de segurança |
> | Acção | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Sincronize o certificado de gestão remota para um dispositivo. |
> | Acção | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Atualize as definições de segurança. |
> | Acção | Microsoft.StorSimple/managers/devices/securitySettings/write | Cria um novo ou atualiza as definições de segurança |
> | Acção | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Envie e-mail de alerta de teste aos destinatários de correio eletrónico configurado. |
> | Acção | Microsoft.StorSimple/managers/devices/shares/read | Apresenta uma lista ou obtém as partilhas |
> | Acção | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Lista os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/timeSettings/read | Apresenta uma lista ou obtém as definições de hora |
> | Acção | Microsoft.StorSimple/managers/devices/timeSettings/write | Cria um novo ou atualiza as definições de hora |
> | Acção | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/updateSummary/read | Apresenta uma lista ou obtém o resumo de atualização |
> | Acção | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Elimina um contentores de volumes existentes (8000 Series apenas) |
> | Acção | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Listar as métricas |
> | Acção | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Lista as definições das métricas |
> | Acção | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Lista os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/volumeContainers/read | Listar os contentores de Volume (apenas na 8000 série) |
> | Acção | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Elimina um Volumes existentes |
> | Acção | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Listar as métricas |
> | Acção | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Lista as definições das métricas |
> | Acção | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Lista os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Listar os Volumes |
> | Acção | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Cria um novo ou atualiza a Volumes |
> | Acção | Microsoft.StorSimple/managers/devices/volumeContainers/write | Cria um novo ou atualiza a contentores de volumes (8000 Series apenas) |
> | Acção | Microsoft.StorSimple/managers/devices/volumes/read | Listar os Volumes |
> | Acção | Microsoft.StorSimple/managers/devices/write | Criar ou atualizar os dispositivos |
> | Acção | Microsoft.StorSimple/managers/encryptionSettings/read | Apresenta uma lista ou obtém as definições de encriptação |
> | Acção | Microsoft.StorSimple/managers/extendedInformation/delete | Elimina as informações do cofre expandida |
> | Acção | Microsoft.StorSimple/Managers/extendedInformation/delete | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Acção | Microsoft.StorSimple/managers/extendedInformation/read | Apresenta uma lista ou obtém as informações do cofre expandido |
> | Acção | Microsoft.StorSimple/Managers/extendedInformation/read | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Acção | Microsoft.StorSimple/managers/extendedInformation/write | Criar ou atualizar as informações do cofre expandido |
> | Acção | Microsoft.StorSimple/Managers/extendedInformation/write | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Acção | Microsoft.StorSimple/managers/features/read | Listar os recursos |
> | Acção | Microsoft.StorSimple/managers/fileservers/read | Apresenta uma lista ou obtém os servidores de ficheiros |
> | Acção | Microsoft.StorSimple/managers/getEncryptionKey/action | Obter chave de encriptação para o Gestor de dispositivos. |
> | Acção | Microsoft.StorSimple/managers/iscsiservers/read | Apresenta uma lista ou obtém o iSCSI de servidores |
> | Acção | Microsoft.StorSimple/managers/jobs/read | Apresenta uma lista ou obtém as tarefas |
> | Acção | Microsoft.StorSimple/managers/listActivationKey/action | Obtém a chave de ativação do StorSimple Device Manager. |
> | Acção | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Listar chaves de encriptação pública de um Gestor de dispositivos do StorSimple. |
> | Acção | Microsoft.StorSimple/managers/metrics/read | Apresenta uma lista ou obtém as métricas |
> | Acção | Microsoft.StorSimple/managers/metricsDefinitions/read | Apresenta uma lista ou obtém as definições das métricas |
> | Acção | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrar de clássico para Resource Manager de Mangers |
> | Acção | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Lista as configurações de origem de migração (apenas na 8000 série) |
> | Acção | Microsoft.StorSimple/managers/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Crie uma nova aplicação de cloud. |
> | Acção | Microsoft.StorSimple/managers/read | Apresenta uma lista ou obtém os gestores de dispositivos |
> | Acção | Microsoft.StorSimple/Managers/read | A operação obter cofre obtém um objeto que representa o recurso do Azure do tipo "Cofre" |
> | Acção | Microsoft.StorSimple/managers/regenerateActivationKey/action | Regenere a chave de ativação para uma existente StorSimple Device Manager. |
> | Acção | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Elimina as credenciais da conta de armazenamento |
> | Acção | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/storageAccountCredentials/read | Apresenta uma lista ou obtém as credenciais da conta de armazenamento |
> | Acção | Microsoft.StorSimple/managers/storageAccountCredentials/write | Criar ou atualizar as credenciais da conta de armazenamento |
> | Acção | Microsoft.StorSimple/managers/storageDomains/delete | Elimina os domínios de armazenamento |
> | Acção | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Acção | Microsoft.StorSimple/managers/storageDomains/read | Apresenta uma lista ou obtém os domínios de armazenamento |
> | Acção | Microsoft.StorSimple/managers/storageDomains/write | Criar ou atualizar os domínios de armazenamento |
> | Acção | Microsoft.StorSimple/managers/write | Criar ou atualizar os gestores de dispositivos |
> | Acção | Microsoft.StorSimple/Managers/write | A operação Criar Cofre cria um recurso do tipo "cofre" do Azure |
> | Acção | Microsoft.StorSimple/operations/read | Apresenta uma lista ou obtém as operações |
> | Acção | Microsoft.StorSimple/register/action | Registar fornecedor Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.StreamAnalytics/locations/quotas/Read | Quota de subscrição do leitura Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/operations/Read | Operações de leitura de Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/Register/action | Registar a subscrição no fornecedor de recursos do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/Delete | Eliminar tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Eliminar função de tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Resultados da operação de leitura para a função de tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Função de tarefa de análise de Stream de leitura |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Obter a predefinição de uma função de tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Função de trabalho do teste Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Escrever a função de tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Eliminar a entrada da tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Resultados da operação de leitura de entrada da tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Entrada da tarefa leitura Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Entrada da tarefa do exemplo Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Entrada da tarefa do teste Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Escrever a entrada da tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Ler definições de métrica |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Resultados da operação de leitura para a tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Eliminar a saída de tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Resultados da operação de leitura de saída de tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Resultado da tarefa leitura Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Resultado da tarefa do teste Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Escrever a saída de tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Ler definição de diagnóstico. |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Escreva definição de diagnóstico. |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para streamingjobs |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para streamingjobs |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/Read | Leitura tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/Start/action | Iniciar a tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Parar a tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Eliminar a transformação da tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Transformação da tarefa leitura Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Escrever a transformação da tarefa do Stream Analytics |
> | Acção | Microsoft.StreamAnalytics/streamingjobs/Write | Escrever a tarefa do Stream Analytics |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Subscription/CreateSubscription/action | Crie uma subscrição do Azure |
> | Acção | Microsoft.Subscription/register/action | Regista a subscrição no fornecedor de recursos de Microsoft.Subscription |
> | Acção | Microsoft.Subscription/SubscriptionDefinitions/read | Obter uma definição de subscrição do Azure dentro de um grupo de gestão. |
> | Acção | Microsoft.Subscription/SubscriptionDefinitions/write | Criar uma definição de subscrição do Azure |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.Support/register/action | Regista para o Fornecedor de Recursos de Suporte |
> | Acção | Microsoft.Support/supportTickets/read | Obtém os detalhes do Pedido de Suporte (incluindo o estado, gravidade, detalhes de contacto e comunicações) ou obtém a lista dos Pedidos de Suporte nas subscrições. |
> | Acção | Microsoft.Support/supportTickets/write | Cria ou Atualiza um Pedido de Suporte. Pode criar um Pedido de Suporte para problemas relacionados com a Gestão Técnica, de Faturação, de Quotas ou de Subscrição. Pode atualizar a gravidade, os detalhes de contacto e as comunicações para pedidos de suporte existentes. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Elimina a política de acesso. |
> | Acção | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Obter as propriedades de uma política de acesso. |
> | Acção | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Cria uma nova política de acesso para um ambiente ou atualiza uma política de acesso existente. |
> | Acção | Microsoft.TimeSeriesInsights/environments/delete | Elimina o ambiente. |
> | Acção | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Elimina a origem do evento. |
> | Acção | Microsoft.TimeSeriesInsights/environments/eventsources/read | Obter as propriedades de uma origem de evento. |
> | Acção | Microsoft.TimeSeriesInsights/environments/eventsources/write | Cria uma nova origem de eventos para um ambiente ou atualiza uma origem de evento existente. |
> | Acção | Microsoft.TimeSeriesInsights/environments/read | Obter as propriedades de um ambiente. |
> | Acção | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Elimina o conjunto de dados de referência. |
> | Acção | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Obter as propriedades de um conjunto de dados de referência. |
> | Acção | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Cria um novo conjunto de dados de referência para um ambiente ou atualiza um conjunto de dados de referência existente. |
> | Acção | Microsoft.TimeSeriesInsights/environments/status/read | Obtenha o estado do ambiente, o estado das suas operações associadas, como entrada. |
> | Acção | Microsoft.TimeSeriesInsights/environments/write | Cria um novo ambiente ou atualiza um ambiente existente. |
> | Acção | Microsoft.TimeSeriesInsights/register/action | Regista a subscrição para o fornecedor de recursos do Time Series Insights e permite a criação de ambientes do Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.VisualStudio/Account/Delete | Eliminar Conta |
> | Acção | Microsoft.VisualStudio/Account/Extension/Read | Ler/extensão da conta |
> | Acção | Microsoft.VisualStudio/Account/Project/Read | Ler conta/projeto |
> | Acção | Microsoft.VisualStudio/Account/Project/Write | Definir conta/projeto |
> | Acção | Microsoft.VisualStudio/Account/Read | Ler conta |
> | Acção | Microsoft.VisualStudio/Account/Write | Definir conta |
> | Acção | Microsoft.VisualStudio/Extension/Delete | Eliminar extensão |
> | Acção | Microsoft.VisualStudio/Extension/Read | Extensão de leitura |
> | Acção | Microsoft.VisualStudio/Extension/Write | Extensão do conjunto |
> | Acção | Microsoft.VisualStudio/Project/Delete | Eliminar Projeto |
> | Acção | Microsoft.VisualStudio/Project/Read | Projeto de leitura |
> | Acção | Microsoft.VisualStudio/Project/Write | Definir o projeto |
> | Acção | Microsoft.VisualStudio/Register/Action | Registar a subscrição do Azure com o fornecedor de Microsoft.VisualStudio |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | microsoft.web/apimanagementaccounts/apiacls/read | Obtenha Apiacls de contas de gestão de Api. |
> | Acção | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Elimine Apiacls de APIs de contas de gestão de Api. |
> | Acção | microsoft.web/apimanagementaccounts/apis/apiacls/read | Obtenha Apiacls de APIs de contas de gestão de Api. |
> | Acção | microsoft.web/apimanagementaccounts/apis/apiacls/write | Atualize Apiacls de APIs de contas de gestão de Api. |
> | Acção | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Obtenha Connectionacls de APIs de contas de gestão de Api. |
> | Acção | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Certifique-se ligações de APIs de contas de gestão Api código consentimento. |
> | Acção | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Elimine Api Management contas APIs ligações Connectionacls. |
> | Acção | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Obter a Api Management contas APIs ligações Connectionacls. |
> | Acção | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Atualize a Api Management contas APIs ligações Connectionacls. |
> | Acção | microsoft.web/apimanagementaccounts/apis/connections/delete | Elimine ligações de APIs de contas de gestão de Api. |
> | Acção | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Obtenha ligações de consentimento para ligações de APIs de contas de gestão de Api. |
> | Acção | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Lista ligação chaves gestão contas APIs ligações de Api. |
> | Acção | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Lista segredos gestão contas APIs ligações de Api. |
> | Acção | microsoft.web/apimanagementaccounts/apis/connections/read | Obtenha ligações de APIs de contas de gestão de Api. |
> | Acção | microsoft.web/apimanagementaccounts/apis/connections/write | Atualize ligações de APIs de contas de gestão de Api. |
> | Acção | microsoft.web/apimanagementaccounts/apis/delete | Elimine as APIs de contas de gestão de Api. |
> | Acção | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Gestão de Api de eliminar definições de localizados de APIs de contas. |
> | Acção | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Gestão de Api de obter definições de localizados de APIs de contas. |
> | Acção | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | APIs de contas de gestão de Api de atualização localizada definições. |
> | Acção | microsoft.web/apimanagementaccounts/apis/read | Obtenha as APIs de contas de gestão de Api. |
> | Acção | microsoft.web/apimanagementaccounts/apis/write | Atualize as APIs de contas de gestão de Api. |
> | Acção | microsoft.web/apimanagementaccounts/connectionacls/read | Obtenha Connectionacls de contas de gestão de Api. |
> | Acção | microsoft.web/availablestacks/read | Obtenha pilhas disponíveis. |
> | Acção | Microsoft.Web/certificates/Delete | Elimine um certificado existente. |
> | Acção | Microsoft.Web/certificates/Read | Obter a lista de certificados. |
> | Acção | Microsoft.Web/certificates/Write | Adicionar um novo certificado ou atualizar um já existente. |
> | Acção | microsoft.web/checknameavailability/read | Verifique se o nome do recurso está disponível. |
> | Acção | microsoft.web/classicmobileservices/read | Obtenha os serviços móveis clássico. |
> | Acção | Microsoft.Web/connectionGateways/Delete | Elimina um Gateway de ligação. |
> | Acção | Microsoft.Web/connectionGateways/Join/Action | Associa um Gateway de ligação. |
> | Acção | Microsoft.Web/connectionGateways/ListStatus/Action | Apresenta uma lista de estado de um Gateway de ligação. |
> | Acção | Microsoft.Web/connectionGateways/Move/Action | Move um Gateway de ligação. |
> | Acção | Microsoft.Web/connectionGateways/Read | Obter a lista de Gateways de ligação. |
> | Acção | Microsoft.Web/connectionGateways/Write | Cria ou atualiza um Gateway de ligação. |
> | Acção | microsoft.web/connections/confirmconsentcode/action | Confirme o código de consentimento de ligações. |
> | Acção | Microsoft.Web/connections/Delete | Elimina uma ligação. |
> | Acção | Microsoft.Web/connections/Join/Action | Associa uma ligação. |
> | Acção | microsoft.web/connections/listconsentlinks/action | Ligações de consentimento da lista de ligações. |
> | Acção | Microsoft.Web/connections/Move/Action | Move uma ligação. |
> | Acção | Microsoft.Web/connections/Read | Obter a lista de ligações. |
> | Acção | Microsoft.Web/connections/Write | Cria ou atualiza uma ligação. |
> | Acção | Microsoft.Web/customApis/Delete | Elimina uma API personalizada. |
> | Acção | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extrai a definição de API de um WSDL. |
> | Acção | Microsoft.Web/customApis/Join/Action | Associa uma API personalizada. |
> | Acção | Microsoft.Web/customApis/listWsdlInterfaces/Action | Apresenta uma lista de interfaces WSDL para uma API personalizada. |
> | Acção | Microsoft.Web/customApis/Move/Action | Move uma API personalizada. |
> | Acção | Microsoft.Web/customApis/Read | Obter a lista de API personalizada. |
> | Acção | Microsoft.Web/customApis/Write | Cria ou atualiza uma API personalizada. |
> | Acção | Microsoft.Web/deletedSites/Read | Obter as propriedades de uma aplicação de Web eliminado |
> | Acção | microsoft.web/deploymentlocations/read | Obter as localizações de implementação. |
> | Acção | Microsoft.Web/geoRegions/Read | Obter a lista de regiões de Georreplicação. |
> | Acção | microsoft.web/hostingenvironments/capacities/read | Obtenha as capacidades de ambientes de alojamento. |
> | Acção | Microsoft.Web/hostingEnvironments/Delete | Eliminar um ambiente de serviço de aplicações |
> | Acção | microsoft.web/hostingenvironments/detectors/read | Obter os detectores de ambientes de alojamento. |
> | Acção | microsoft.web/hostingenvironments/diagnostics/read | Obtenha diagnósticos de ambientes de alojamento. |
> | Acção | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Obtenha os pontos de extremidade de rede de todas as dependências de entrada. |
> | Acção | Microsoft.Web/hostingEnvironments/Join/Action | Associa um ambiente de serviço de aplicações |
> | Acção | microsoft.web/hostingenvironments/metricdefinitions/read | Obter definições de métricas de ambientes de alojamento. |
> | Acção | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Obter definições de métricas de multifunções conjuntos de ambientes de alojamento. |
> | Acção | microsoft.web/hostingenvironments/multirolepools/metrics/read | Obter métricas de multifunções conjuntos de ambientes de alojamento. |
> | Acção | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Obter as propriedades de um conjunto de front-end num ambiente de serviço de aplicações |
> | Acção | microsoft.web/hostingenvironments/multirolepools/skus/read | Obter SKUs de multifunções conjuntos de ambientes de alojamento. |
> | Acção | microsoft.web/hostingenvironments/multirolepools/usages/read | Obter utilizações de multifunções conjuntos de ambientes de alojamento. |
> | Acção | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Criar um novo conjunto de front-end num ambiente de serviço de aplicações ou atualizar um já existente |
> | Acção | microsoft.web/hostingenvironments/operations/read | Obter operações de ambientes de alojamento. |
> | Acção | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Obtenha os pontos de extremidade de rede de todas as dependências de saída. |
> | Acção | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Aprovar ligações de ponto de extremidade privados |
> | Acção | Microsoft.Web/hostingEnvironments/Read | Obter as propriedades de um ambiente de serviço de aplicações |
> | Acção | Microsoft.Web/hostingEnvironments/reboot/Action | Reinicie todas as máquinas num ambiente de serviço de aplicações |
> | Acção | microsoft.web/hostingenvironments/resume/action | Retomar a ambientes de alojamento. |
> | Acção | microsoft.web/hostingenvironments/serverfarms/read | Obtenha planos de serviço de aplicações de ambientes de alojamento. |
> | Acção | microsoft.web/hostingenvironments/sites/read | Obter aplicações da Web de ambientes de alojamento. |
> | Acção | microsoft.web/hostingenvironments/suspend/action | Suspenda a ambientes de alojamento. |
> | Acção | microsoft.web/hostingenvironments/usages/read | Obter utilizações de ambientes de alojamento. |
> | Acção | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Obter definições de métrica de Workerpools de ambientes de alojamento. |
> | Acção | microsoft.web/hostingenvironments/workerpools/metrics/read | Obter métricas de Workerpools de ambientes de alojamento. |
> | Acção | Microsoft.Web/hostingEnvironments/workerPools/Read | Obter as propriedades de um conjunto de trabalho num ambiente de serviço de aplicações |
> | Acção | microsoft.web/hostingenvironments/workerpools/skus/read | Obter SKUs de Workerpools de ambientes de alojamento. |
> | Acção | microsoft.web/hostingenvironments/workerpools/usages/read | Obter utilizações de Workerpools de ambientes de alojamento. |
> | Acção | Microsoft.Web/hostingEnvironments/workerPools/Write | Criar um novo conjunto de trabalho num ambiente de serviço de aplicações ou atualizar um já existente |
> | Acção | Microsoft.Web/hostingEnvironments/Write | Criar um ambiente de serviço de aplicações novo ou atualizar um já existente |
> | Acção | microsoft.web/ishostingenvironmentnameavailable/read | Get se o nome do ambiente de alojamento está disponível. |
> | Acção | microsoft.web/ishostnameavailable/read | Verifique se o nome de anfitrião está disponível. |
> | Acção | microsoft.web/isusernameavailable/read | Verifique se o nome de utilizador está disponível. |
> | Acção | Microsoft.Web/listSitesAssignedToHostName/Read | Obter nomes de sites atribuídos ao nome de anfitrião. |
> | Acção | microsoft.web/locations/apioperations/read | Obter operações de API de localizações. |
> | Acção | microsoft.web/locations/connectiongatewayinstallations/read | Obter instalações de gateways de ligação de locais. |
> | Acção | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Vnet ou sub-rede notificação de eliminação para localizações. |
> | Acção | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extrair a definição de Api do WSDL para localizações. |
> | Acção | microsoft.web/locations/listwsdlinterfaces/action | Interfaces WSDL de lista de localizações. |
> | Acção | microsoft.web/locations/managedapis/apioperations/read | Obter operações de API gerido de localizações. |
> | Acção | Microsoft.Web/locations/managedapis/Join/Action | Associa uma API gerenciada. |
> | Acção | microsoft.web/locations/managedapis/read | Obter localizações de APIs gerenciadas. |
> | Acção | microsoft.web/operations/read | Obter operações. |
> | Acção | microsoft.web/publishingusers/read | Obter utilizadores de publicação. |
> | Acção | microsoft.web/publishingusers/write | Atualize utilizadores de publicação. |
> | Acção | Microsoft.Web/recommendations/Read | Obter a lista de recomendações para as subscrições. |
> | Acção | microsoft.web/register/action | Registe o fornecedor de recursos de Microsoft. Web para a subscrição. |
> | Acção | microsoft.web/resourcehealthmetadata/read | Obter metadados de estado de funcionamento do recurso. |
> | Acção | microsoft.web/serverfarms/capabilities/read | Obtenha capacidades de planos de serviço de aplicações. |
> | Acção | Microsoft.Web/serverfarms/Delete | Eliminar um plano de serviço de aplicações existentes |
> | Acção | microsoft.web/serverfarms/firstpartyapps/settings/delete | Elimine definições de aplicações de terceiros de primeiro em planos de serviço de aplicações. |
> | Acção | microsoft.web/serverfarms/firstpartyapps/settings/read | Obter definições de aplicações de terceiros de primeiro em planos de serviço de aplicações. |
> | Acção | microsoft.web/serverfarms/firstpartyapps/settings/write | Atualize definições de aplicações de terceiros de primeiro de planos do serviço de aplicações. |
> | Acção | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Elimine reencaminhamentos de espaços de nomes de ligação do serviço de aplicações planos híbrida. |
> | Acção | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Obtenha os reencaminhamentos de espaços de nomes de ligação do serviço de aplicações planos híbrida. |
> | Acção | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Obter planos de serviço de aplicações reencaminhamentos para espaços de nomes de ligação híbrida Web de aplicações. |
> | Acção | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Obtenha os limites do plano de ligação híbrida do serviço de aplicações planos. |
> | Acção | microsoft.web/serverfarms/hybridconnectionrelays/read | Obtenha planos de serviço de aplicações híbridas reencaminhamentos de ligação. |
> | Acção | microsoft.web/serverfarms/metricdefinitions/read | Obter as definições de métricas de planos do serviço de aplicações. |
> | Acção | microsoft.web/serverfarms/metrics/read | Obter métricas de planos de serviço de aplicações. |
> | Acção | microsoft.web/serverfarms/operationresults/read | Obter resultados da operação de planos de serviço de aplicações. |
> | Acção | Microsoft.Web/serverfarms/Read | Obter as propriedades num plano do serviço de aplicações |
> | Acção | Microsoft.Web/serverfarms/restartSites/Action | Reinicie todas as aplicações Web num plano do serviço de aplicações |
> | Acção | microsoft.web/serverfarms/sites/read | Obter aplicações de Web de planos de serviço de aplicações. |
> | Acção | microsoft.web/serverfarms/skus/read | Obter os SKUs de planos de serviço de aplicações. |
> | Acção | microsoft.web/serverfarms/usages/read | Obter utilizações de planos de serviço de aplicações. |
> | Acção | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Atualize os Gateways de ligações de rede Virtual de planos de serviço de aplicações. |
> | Acção | microsoft.web/serverfarms/virtualnetworkconnections/read | Obtenha ligações de rede Virtual de planos de serviço de aplicações. |
> | Acção | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Elimine rotas de ligações de rede Virtual de planos de serviço de aplicações. |
> | Acção | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Obter as rotas de ligações de rede Virtual de planos de serviço de aplicações. |
> | Acção | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Atualize rotas de ligações de rede Virtual de planos de serviço de aplicações. |
> | Acção | microsoft.web/serverfarms/workers/reboot/action | Reinicie os trabalhos de planos de serviço de aplicações. |
> | Acção | Microsoft.Web/serverfarms/Write | Criar um plano de serviço de aplicações novo ou atualizar um já existente |
> | Acção | microsoft.web/sites/analyzecustomhostname/read | Analise o nome de anfitrião personalizado. |
> | Acção | Microsoft.Web/sites/applySlotConfig/Action | Aplicar a configuração de ranhura de aplicação do web da ranhura de destino para a aplicação web atual |
> | Acção | Microsoft.Web/sites/backup/Action | Criar uma nova cópia de segurança de aplicação de web |
> | Acção | microsoft.web/sites/backup/read | Obtenha cópia de segurança de aplicações de Web. |
> | Acção | microsoft.web/sites/backup/write | Atualize a cópia de segurança de aplicações de Web. |
> | Acção | microsoft.web/sites/backups/action | Deteta uma cópia de segurança de aplicação existente que pode ser restaurada a partir de um blob no armazenamento do Azure. |
> | Acção | microsoft.web/sites/backups/delete | Elimine cópias de segurança de aplicações de Web. |
> | Acção | microsoft.web/sites/backups/list/action | Backups de aplicações Web de lista. |
> | Acção | Microsoft.Web/sites/backups/Read | Obter as propriedades de cópia de segurança de uma aplicação web |
> | Acção | microsoft.web/sites/backups/restore/action | Restaure cópias de segurança de aplicações de Web. |
> | Acção | microsoft.web/sites/backups/write | Atualize a cópias de segurança de aplicações Web. |
> | Acção | microsoft.web/sites/config/delete | Elimine configuração de aplicações Web. |
> | Acção | Microsoft.Web/sites/config/list/Action | Lista confidenciais configurações de segurança da aplicação Web, como a publicação de credenciais, as definições da aplicação e as cadeias de ligação |
> | Acção | Microsoft.Web/sites/config/Read | Obter definições de configuração de aplicação Web |
> | Acção | microsoft.web/sites/config/snapshots/read | Obter instantâneos de configuração de aplicações Web. |
> | Acção | Microsoft.Web/sites/config/Write | Atualizar as definições de configuração da aplicação Web |
> | Acção | microsoft.web/sites/containerlogs/action | Obter comprimido registos de contentor para a aplicação Web. |
> | Acção | microsoft.web/sites/containerlogs/download/action | Transferir registos de contentor de aplicações Web. |
> | Acção | microsoft.web/sites/continuouswebjobs/delete | Elimine tarefas de Web contínua de aplicações Web. |
> | Acção | microsoft.web/sites/continuouswebjobs/read | Obter tarefas de Web contínua de aplicações Web. |
> | Acção | microsoft.web/sites/continuouswebjobs/start/action | Inicie tarefas de Web contínua de aplicações Web. |
> | Acção | microsoft.web/sites/continuouswebjobs/stop/action | Pare tarefas de Web contínua de aplicações Web. |
> | Acção | Microsoft.Web/sites/Delete | Eliminar uma aplicação Web existente |
> | Acção | microsoft.web/sites/deployments/delete | Elimine as implementações de aplicações Web. |
> | Acção | microsoft.web/sites/deployments/log/read | Obter registos de implementações de aplicações Web. |
> | Acção | microsoft.web/sites/deployments/read | Obter implementações de aplicações Web. |
> | Acção | microsoft.web/sites/deployments/write | Atualize as implementações de aplicações Web. |
> | Acção | microsoft.web/sites/detectors/read | Obtenha os detectores de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/analyses/execute/Action | Execute a análise de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/analyses/read | Obtenha a análise de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/aspnetcore/read | Obtenha o diagnóstico de aplicações Web para a aplicação ASP.NET Core. |
> | Acção | microsoft.web/sites/diagnostics/autoheal/read | Obtenha o Autoheal de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/deployment/read | Obter a implementação de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/deployments/read | Obter implementações de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/detectors/execute/Action | Execute o detetor de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/detectors/read | Obtenha o detetor de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Obter pedidos falhados de diagnóstico de aplicações Web por Uri. |
> | Acção | microsoft.web/sites/diagnostics/frebanalysis/read | Obtenha a análise FREB de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/loganalyzer/read | Obtenha o analisador de registos de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/read | Obter categorias de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/runtimeavailability/read | Obtenha disponibilidade de tempo de execução do diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/servicehealth/read | Obter o estado de funcionamento de serviço de diagnóstico de aplicações de Web. |
> | Acção | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Obtenha a análise de CPU de Site de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/sitecrashes/read | Obter falhas de Site de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/sitelatency/read | Obtenha a latência de Site de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Obtenha a análise de memória do Site de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Obtenha a atualização de definição de reinício de Site diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Obter iniciado dos utilizador de reinício de Site para o diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/siteswap/read | Obtenha a troca de Site de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/threadcount/read | Obter a contagem de threads de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/workeravailability/read | Obtenha Workeravailability de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Obtenha a Reciclagem de processo de trabalho de diagnóstico de aplicações Web. |
> | Acção | microsoft.web/sites/domainownershipidentifiers/read | Obter identificadores de propriedade do domínio de aplicações Web. |
> | Acção | microsoft.web/sites/domainownershipidentifiers/write | Atualize os identificadores de propriedade do domínio de aplicações Web. |
> | Acção | microsoft.web/sites/extensions/delete | Elimine extensões de Site de aplicações Web. |
> | Acção | microsoft.web/sites/extensions/read | Obtenha as extensões de Site de aplicações Web. |
> | Acção | microsoft.web/sites/extensions/write | Atualize extensões de Site de aplicações Web. |
> | Acção | microsoft.web/sites/functions/action | Aplicações Web de funções. |
> | Acção | microsoft.web/sites/functions/delete | Elimine funções de aplicações Web. |
> | Acção | microsoft.web/sites/functions/listsecrets/action | Funções de aplicações de Web de segredos de lista. |
> | Acção | microsoft.web/sites/functions/masterkey/read | Obtenha Masterkey de funções de aplicações Web. |
> | Acção | microsoft.web/sites/functions/read | Obter funções de aplicações Web. |
> | Acção | microsoft.web/sites/functions/token/read | Token de funções de aplicações de Web de GET. |
> | Acção | microsoft.web/sites/functions/write | Atualize funções de aplicações Web. |
> | Acção | microsoft.web/sites/hostnamebindings/delete | Elimine enlaces de nome de anfitrião de aplicações Web. |
> | Acção | microsoft.web/sites/hostnamebindings/read | Obter os enlaces de nome de anfitrião de aplicações Web. |
> | Acção | microsoft.web/sites/hostnamebindings/write | Atualize os enlaces de nome de anfitrião de aplicações Web. |
> | Acção | microsoft.web/sites/hostruntime/functions/keys/read | Obtenha chaves de funções de Hostruntime de aplicações Web. |
> | Acção | Microsoft.Web/sites/hostruntime/host/_master/read | Obter a chave mestra da aplicação de função para operações de administração |
> | Acção | Microsoft.Web/sites/hostruntime/host/action | Execute a aplicação de funções, como uma ação de tempo de execução acionadores de sincronização, adicionar funções, invocar funções, eliminar as funções etc. |
> | Acção | microsoft.web/sites/hostruntime/host/read | Obter anfitrião de Hostruntime de aplicações Web. |
> | Acção | microsoft.web/sites/hybridconnection/delete | Elimine ligação de aplicações híbridas de Web. |
> | Acção | microsoft.web/sites/hybridconnection/read | Obter ligação de aplicações híbridas de Web. |
> | Acção | microsoft.web/sites/hybridconnection/write | Atualize a ligação de aplicações híbridas de Web. |
> | Acção | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Elimine reencaminhamentos de espaços de nomes de ligação de aplicações híbridas de Web. |
> | Acção | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Lista as chaves Web aplicações híbridas ligação espaços de nomes reencaminhamentos. |
> | Acção | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Obtenha os reencaminhamentos de espaços de nomes de ligação de aplicações híbridas de Web. |
> | Acção | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Atualize reencaminhamentos de espaços de nomes de ligação de aplicações híbridas de Web. |
> | Acção | microsoft.web/sites/hybridconnectionrelays/read | Obtenha os reencaminhamentos de ligação de aplicações híbridas de Web. |
> | Acção | microsoft.web/sites/instances/deployments/delete | Elimine as implementações de instâncias de aplicações Web. |
> | Acção | microsoft.web/sites/instances/deployments/read | Obter implementações de instâncias de aplicações Web. |
> | Acção | microsoft.web/sites/instances/extensions/log/read | Obter registos de extensões de instâncias de aplicações Web. |
> | Acção | microsoft.web/sites/instances/extensions/processes/read | Obter processos de extensões de instâncias de aplicações Web. |
> | Acção | microsoft.web/sites/instances/extensions/read | Obtenha as extensões de instâncias de aplicações Web. |
> | Acção | microsoft.web/sites/instances/processes/delete | Elimine processos de instâncias de aplicações Web. |
> | Acção | microsoft.web/sites/instances/processes/modules/read | Obter os módulos de processos de instâncias de aplicações Web. |
> | Acção | microsoft.web/sites/instances/processes/read | Obter processos de instâncias de aplicações Web. |
> | Acção | microsoft.web/sites/instances/processes/threads/read | Obtenha os Threads de processos de instâncias de aplicações Web. |
> | Acção | microsoft.web/sites/instances/read | Obter instâncias de aplicações Web. |
> | Acção | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Lista sincronização função acionador Estado aplicações Web. |
> | Acção | microsoft.web/sites/metricdefinitions/read | Obter definições de métrica de aplicações de Web. |
> | Acção | microsoft.web/sites/metrics/read | Obter métricas de aplicações Web. |
> | Acção | microsoft.web/sites/metricsdefinitions/read | Obter definições de métricas de aplicações Web. |
> | Acção | microsoft.web/sites/migratemysql/action | Migre aplicações Web do MySql. |
> | Acção | microsoft.web/sites/migratemysql/read | Obter as Web Apps migrar MySql. |
> | Acção | microsoft.web/sites/networktrace/action | Aplicações de Web de rastreio de rede. |
> | Acção | microsoft.web/sites/networktraces/operationresults/read | Obter resultados de operação de rastreio de rede de aplicações Web. |
> | Acção | microsoft.web/sites/newpassword/action | Aplicações Web de newpassword. |
> | Acção | microsoft.web/sites/operationresults/read | Obter resultados da operação de aplicações Web. |
> | Acção | microsoft.web/sites/operations/read | Obter operações de aplicações Web. |
> | Acção | microsoft.web/sites/perfcounters/read | Obter os contadores de desempenho de aplicações Web. |
> | Acção | microsoft.web/sites/premieraddons/delete | Elimine complementos de Premier de aplicações Web. |
> | Acção | microsoft.web/sites/premieraddons/read | Obtenha suplementos de Premier de aplicações Web. |
> | Acção | microsoft.web/sites/premieraddons/write | Atualize complementos de Premier de aplicações Web. |
> | Acção | microsoft.web/sites/privateaccess/read | Obter dados em torno de ativação de acesso de sites privados e redes virtuais autorizadas, que pode aceder ao site. |
> | Acção | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | Aprovar ligações de ponto de extremidade privados |
> | Acção | microsoft.web/sites/processes/modules/read | Obter módulos de processos de aplicações Web. |
> | Acção | microsoft.web/sites/processes/read | Obter processos de aplicações Web. |
> | Acção | microsoft.web/sites/processes/threads/read | Obtenha os Threads de processos de aplicações Web. |
> | Acção | microsoft.web/sites/publiccertificates/delete | Elimine certificados de aplicações públicas da Web. |
> | Acção | microsoft.web/sites/publiccertificates/read | Obter certificados de aplicações públicas da Web. |
> | Acção | microsoft.web/sites/publiccertificates/write | Atualize certificados de aplicações públicas da Web. |
> | Acção | Microsoft.Web/sites/publish/Action | Publicar uma aplicação Web |
> | Acção | Microsoft.Web/sites/publishxml/Action | Obter o xml do perfil de publicação para uma aplicação Web |
> | Acção | microsoft.web/sites/publishxml/read | Obter aplicações Web XML de publicação. |
> | Acção | Microsoft.Web/sites/Read | Obter as propriedades de uma aplicação Web |
> | Acção | microsoft.web/sites/recommendationhistory/read | Obter histórico de recomendações de aplicações Web. |
> | Acção | microsoft.web/sites/recommendations/disable/action | Desative as recomendações de aplicações Web. |
> | Acção | Microsoft.Web/sites/recommendations/Read | Obter a lista de recomendações para a aplicação web. |
> | Acção | microsoft.web/sites/recover/action | Recupere a aplicações Web. |
> | Acção | Microsoft.Web/sites/resetSlotConfig/Action | Repor a configuração de aplicações web |
> | Acção | microsoft.web/sites/resourcehealthmetadata/read | Obter metadados de estado de funcionamento do recurso de aplicações Web. |
> | Acção | Microsoft.Web/sites/restart/Action | Reiniciar uma aplicação Web |
> | Acção | microsoft.web/sites/restore/read | Obtenha o restauro de aplicações Web. |
> | Acção | microsoft.web/sites/restore/write | Restaure aplicações Web. |
> | Acção | microsoft.web/sites/restorefrombackupblob/action | Restaure aplicação Web a partir do Blob de cópia de segurança. |
> | Acção | microsoft.web/sites/restorefromdeletedwebapp/action | Restaure aplicações Web da aplicação eliminada. |
> | Acção | microsoft.web/sites/restoresnapshot/action | Restaure instantâneos de aplicações Web. |
> | Acção | microsoft.web/sites/siteextensions/delete | Elimine extensões de Site de aplicações Web. |
> | Acção | microsoft.web/sites/siteextensions/read | Obtenha as extensões de Site de aplicações Web. |
> | Acção | microsoft.web/sites/siteextensions/write | Atualize extensões de Site de aplicações Web. |
> | Acção | microsoft.web/sites/slots/analyzecustomhostname/read | Obter as Web Apps ranhuras analisar nome de anfitrião personalizado. |
> | Acção | Microsoft.Web/sites/slots/applySlotConfig/Action | Aplica a configuração de ranhura de aplicação do web da ranhura de destino para a ranhura atual. |
> | Acção | Microsoft.Web/sites/slots/backup/Action | Crie nova cópia de segurança da ranhura de aplicação Web. |
> | Acção | microsoft.web/sites/slots/backup/read | Obtenha cópia de segurança de ranhuras de aplicações de Web. |
> | Acção | microsoft.web/sites/slots/backup/write | Atualize a cópia de segurança de ranhuras de aplicações de Web. |
> | Acção | microsoft.web/sites/slots/backups/action | Detete cópias de segurança de ranhuras de aplicações de Web. |
> | Acção | microsoft.web/sites/slots/backups/delete | Elimine cópias de segurança de ranhuras de aplicações de Web. |
> | Acção | microsoft.web/sites/slots/backups/list/action | Cópias de segurança da ranhuras de aplicações do Web de lista. |
> | Acção | Microsoft.Web/sites/slots/backups/Read | Obter as propriedades de cópia de segurança dos blocos de aplicação uma web |
> | Acção | microsoft.web/sites/slots/backups/restore/action | Restaure Backups de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/config/delete | Elimine configuração de ranhuras de aplicações Web. |
> | Acção | Microsoft.Web/sites/slots/config/list/Action | Lista confidenciais configurações de segurança da ranhura de aplicação Web, como a publicação de credenciais, as definições da aplicação e as cadeias de ligação |
> | Acção | Microsoft.Web/sites/slots/config/Read | Obter definições de configuração da ranhura de aplicação Web |
> | Acção | Microsoft.Web/sites/slots/config/Write | Atualizar as definições de configuração da ranhura de aplicação Web |
> | Acção | microsoft.web/sites/slots/containerlogs/action | Obter comprimido registos de contentor para a ranhura de aplicação Web. |
> | Acção | microsoft.web/sites/slots/containerlogs/download/action | Transferir registos de contentor de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/continuouswebjobs/delete | Elimine tarefas de Web contínua de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/continuouswebjobs/read | Obtenha as Web Apps ranhuras Webjobs contínuos. |
> | Acção | microsoft.web/sites/slots/continuouswebjobs/start/action | Inicie tarefas de Web contínua de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/continuouswebjobs/stop/action | Pare tarefas de Web contínua de ranhuras de aplicações Web. |
> | Acção | Microsoft.Web/sites/slots/Delete | Eliminar uma ranhura de aplicação Web existente |
> | Acção | microsoft.web/sites/slots/deployments/delete | Elimine as implementações de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/deployments/log/read | Obter registos de implementações de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/deployments/read | Obter implementações de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/deployments/write | Atualize as implementações de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/detectors/read | Obtenha os detectores de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Execute a análise de diagnóstico de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/analyses/read | Obtenha a análise de diagnóstico de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Obtenha diagnósticos de ranhuras de aplicações Web para a aplicação ASP.NET Core. |
> | Acção | microsoft.web/sites/slots/diagnostics/autoheal/read | Obtenha o Autoheal de diagnóstico de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/deployment/read | Obter a implementação de diagnóstico de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/deployments/read | Obter implementações de diagnóstico de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Execute o detetor de diagnóstico de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/detectors/read | Obtenha o detetor de diagnóstico de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Obter análises de FREB de diagnóstico de ranhuras de aplicações de Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Obtenha o analisador de Log de diagnóstico de ranhuras de aplicações de Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/read | Obtenha diagnósticos de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Obtenha disponibilidade de tempo de execução do diagnóstico de ranhuras de aplicações da Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/servicehealth/read | Obter o estado de funcionamento do Web Apps ranhuras diagnóstico serviço. |
> | Acção | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Obtenha a análise de CPU de Site de diagnóstico ranhuras aplicações Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Obter falhas de Site do diagnóstico de ranhuras de aplicações da Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/sitelatency/read | Obtenha a latência de Site de diagnóstico de ranhuras de aplicações de Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Obtenha a análise de memória de Site de diagnóstico ranhuras aplicações Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Obter blocos de aplicativos de Web diagnóstico atualização de definição de reinício de Site. |
> | Acção | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Obtenha as Web Apps ranhuras diagnóstico Site reinício iniciada pelo utilizador. |
> | Acção | microsoft.web/sites/slots/diagnostics/siteswap/read | Obtenha a troca de Site de diagnóstico de ranhuras aplicações Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/threadcount/read | Obter a contagem de threads de diagnóstico de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/workeravailability/read | Obtenha Workeravailability de diagnóstico de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Obtenha os Reciclagem do processo de trabalho do diagnóstico de ranhuras do Web Apps. |
> | Acção | microsoft.web/sites/slots/domainownershipidentifiers/read | Obter identificadores de propriedade de domínio de ranhuras de aplicações de Web. |
> | Acção | microsoft.web/sites/slots/functions/listsecrets/action | Funções de ranhuras do lista segredos Web aplicações. |
> | Acção | microsoft.web/sites/slots/functions/read | Obter funções de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/hostnamebindings/delete | Elimine enlaces de nome de anfitrião de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/hostnamebindings/read | Obter os enlaces de nome de anfitrião de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/hostnamebindings/write | Atualize os enlaces de nome de anfitrião de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/hybridconnection/delete | Elimine a ligação de híbrida de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/hybridconnection/read | Obter ligação de híbrida de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/hybridconnection/write | Atualize a ligação de híbrida de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Elimine reencaminhamentos de espaços de nomes de ligação do Web Apps ranhuras híbrida. |
> | Acção | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Atualize reencaminhamentos de espaços de nomes de ligação do Web Apps ranhuras híbrida. |
> | Acção | microsoft.web/sites/slots/hybridconnectionrelays/read | Obtenha as Web Apps ranhuras híbrida ligação reencaminhamentos. |
> | Acção | microsoft.web/sites/slots/instances/deployments/read | Obter implementações de instâncias de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/instances/processes/delete | Elimine processos de instâncias de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/instances/processes/read | Obter processos de instâncias de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/instances/read | Obter instâncias de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/metricdefinitions/read | Obter definições de métricas de ranhuras de aplicações de Web. |
> | Acção | microsoft.web/sites/slots/metrics/read | Obter métricas de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/migratemysql/read | Obter Web blocos de aplicativos migrar MySql. |
> | Acção | microsoft.web/sites/slots/networktrace/action | Blocos de aplicativos de Web de rastreio de rede. |
> | Acção | microsoft.web/sites/slots/networktraces/operationresults/read | Obter resultados de operação de rastreio de rede ranhuras aplicações Web. |
> | Acção | microsoft.web/sites/slots/newpassword/action | Ranhuras de aplicações Web de newpassword. |
> | Acção | microsoft.web/sites/slots/operationresults/read | Obter resultados de operação de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/operations/read | Obter operações de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/perfcounters/read | Obter os contadores de desempenho de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/phplogging/read | Obtenha Phplogging de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/premieraddons/delete | Elimine complementos de Premier de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/premieraddons/read | Obtenha suplementos de Premier de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/premieraddons/write | Atualize complementos de Premier de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/processes/read | Obter processos de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/publiccertificates/delete | Elimine certificados públicos de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/publiccertificates/read | Obter certificados públicos de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/publiccertificates/write | Criar ou atualizar certificados públicos de ranhuras de aplicações Web. |
> | Acção | Microsoft.Web/sites/slots/publish/Action | Publicar uma ranhura de aplicação Web |
> | Acção | Microsoft.Web/sites/slots/publishxml/Action | Obter o xml do perfil de publicação para a ranhura de aplicação Web |
> | Acção | Microsoft.Web/sites/slots/Read | Obter as propriedades de um bloco de implementação de aplicação Web |
> | Acção | microsoft.web/sites/slots/recover/action | Recupere ranhuras de aplicações Web. |
> | Acção | Microsoft.Web/sites/slots/resetSlotConfig/Action | Repor a configuração do bloco de aplicação web |
> | Acção | microsoft.web/sites/slots/resourcehealthmetadata/read | Obter metadados de estado de funcionamento de recursos de ranhuras das aplicações Web. |
> | Acção | Microsoft.Web/sites/slots/restart/Action | Reiniciar uma ranhura de aplicação Web |
> | Acção | microsoft.web/sites/slots/restore/read | Obtenha o restauro de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/restore/write | Restaure ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/restorefrombackupblob/action | Restaure a ranhura de aplicações Web do Blob de cópia de segurança. |
> | Acção | microsoft.web/sites/slots/restorefromdeletedwebapp/action | Restaure ranhuras de aplicação Web da aplicação eliminada. |
> | Acção | microsoft.web/sites/slots/restoresnapshot/action | Restaure instantâneos de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/siteextensions/delete | Elimine extensões de Site de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/siteextensions/read | Obtenha as extensões de Site de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/siteextensions/write | Atualize extensões de Site de ranhuras de aplicações Web. |
> | Acção | Microsoft.Web/sites/slots/slotsdiffs/Action | Obter as diferenças na configuração entre a aplicação web e ranhuras |
> | Acção | Microsoft.Web/sites/slots/slotsswap/Action | Trocar as ranhuras de implementação de aplicação Web |
> | Acção | microsoft.web/sites/slots/snapshots/read | Obter instantâneos de ranhuras de aplicações Web. |
> | Acção | Microsoft.Web/sites/slots/sourcecontrols/Delete | Eliminar definições de configuração de controlo de origem da ranhura de aplicação Web |
> | Acção | Microsoft.Web/sites/slots/sourcecontrols/Read | Obter definições de configuração de controlo de código fonte da ranhura de aplicação Web |
> | Acção | Microsoft.Web/sites/slots/sourcecontrols/Write | Atualizar definições de configuração de controlo de origem da ranhura de aplicação Web |
> | Acção | Microsoft.Web/sites/slots/start/Action | Iniciar um bloco de aplicação Web |
> | Acção | Microsoft.Web/sites/slots/stop/Action | Parar uma ranhura de aplicação Web |
> | Acção | microsoft.web/sites/slots/sync/action | Ranhuras de aplicações Web de sincronização. |
> | Acção | microsoft.web/sites/slots/triggeredwebjobs/delete | Elimine aplicações/ranhuras WebJobs Acionadas da Web. |
> | Acção | microsoft.web/sites/slots/triggeredwebjobs/read | Obter aplicações/ranhuras WebJobs Acionadas da Web. |
> | Acção | microsoft.web/sites/slots/triggeredwebjobs/run/action | Ranhuras de aplicações Web de execução os WebJobs acionados. |
> | Acção | microsoft.web/sites/slots/usages/read | Obter utilizações de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/virtualnetworkconnections/delete | Elimine ligações de rede Virtual de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Atualize os Gateways de ligações de rede Virtual de ranhuras aplicações Web. |
> | Acção | microsoft.web/sites/slots/virtualnetworkconnections/read | Obtenha ligações de rede Virtual de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/virtualnetworkconnections/write | Atualize ligações de rede Virtual de ranhuras de aplicações Web. |
> | Acção | microsoft.web/sites/slots/webjobs/read | Obtenha o WebJobs de ranhuras de aplicações Web. |
> | Acção | Microsoft.Web/sites/slots/Write | Criar uma nova ranhura de aplicação Web ou atualizar um já existente |
> | Acção | Microsoft.Web/sites/slotsdiffs/Action | Obter as diferenças na configuração entre a aplicação web e ranhuras |
> | Acção | Microsoft.Web/sites/slotsswap/Action | Trocar as ranhuras de implementação de aplicação Web |
> | Acção | microsoft.web/sites/snapshots/read | Obter instantâneos de aplicações Web. |
> | Acção | Microsoft.Web/sites/sourcecontrols/Delete | Eliminar definições de configuração de controlo de origem da aplicação Web |
> | Acção | Microsoft.Web/sites/sourcecontrols/Read | Obter definições de configuração de controlo de código fonte da aplicação Web |
> | Acção | Microsoft.Web/sites/sourcecontrols/Write | Atualizar definições de configuração de controlo de origem da aplicação Web |
> | Acção | Microsoft.Web/sites/start/Action | Iniciar uma aplicação Web |
> | Acção | Microsoft.Web/sites/stop/Action | Parar uma aplicação Web |
> | Acção | microsoft.web/sites/sync/action | Sync Web Apps. |
> | Acção | microsoft.web/sites/syncfunctiontriggers/action | Acionadores de função de sincronização para aplicações Web. |
> | Acção | microsoft.web/sites/triggeredwebjobs/delete | Elimine WebJobs Acionadas de aplicações Web. |
> | Acção | microsoft.web/sites/triggeredwebjobs/history/read | Obter histórico de WebJobs Acionadas aplicações Web. |
> | Acção | microsoft.web/sites/triggeredwebjobs/read | Obtenha o WebJobs Acionadas de aplicações Web. |
> | Acção | microsoft.web/sites/triggeredwebjobs/run/action | Aplicações Web de execução os WebJobs acionados. |
> | Acção | microsoft.web/sites/usages/read | Obter utilizações de aplicações Web. |
> | Acção | microsoft.web/sites/virtualnetworkconnections/delete | Elimine ligações de rede Virtual de aplicações Web. |
> | Acção | microsoft.web/sites/virtualnetworkconnections/gateways/read | Obter Gateways de ligações de rede Virtual de aplicações Web. |
> | Acção | microsoft.web/sites/virtualnetworkconnections/gateways/write | Atualize os Gateways de ligações de rede Virtual de aplicações Web. |
> | Acção | microsoft.web/sites/virtualnetworkconnections/read | Obtenha ligações de rede Virtual de aplicações Web. |
> | Acção | microsoft.web/sites/virtualnetworkconnections/write | Atualize ligações de rede Virtual de aplicações Web. |
> | Acção | microsoft.web/sites/webjobs/read | Obtenha o WebJobs de aplicações Web. |
> | Acção | Microsoft.Web/sites/Write | Criar uma nova aplicação Web ou atualizar um já existente |
> | Acção | microsoft.web/skus/read | Obter SKUs. |
> | Acção | microsoft.web/sourcecontrols/read | Obtenha controles da fonte. |
> | Acção | microsoft.web/sourcecontrols/write | Controles da fonte de atualização. |
> | Acção | microsoft.web/unregister/action | Anular o registo do fornecedor de recursos de Microsoft. Web para a subscrição. |
> | Acção | microsoft.web/validate/action | Valide. |
> | Acção | microsoft.web/verifyhostingenvironmentvnet/action | Certifique-se de Vnet do ambiente de alojamento. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Acção | Microsoft.WorkloadMonitor/components/read | Obtém os componentes para o recurso |
> | Acção | Microsoft.WorkloadMonitor/componentsSummary/read | Obtém o resumo dos componentes |
> | Acção | Microsoft.WorkloadMonitor/monitorInstances/read | Obtém as instâncias de monitores para o recurso |
> | Acção | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Obtém o resumo de instâncias do monitor |
> | Acção | Microsoft.WorkloadMonitor/monitors/read | Obtém os monitores para o recurso |
> | Acção | Microsoft.WorkloadMonitor/monitors/write | Configure o monitor para o recurso |
> | Acção | Microsoft.WorkloadMonitor/notificationSettings/read | Obtém as definições de notificação para o recurso |
> | Acção | Microsoft.WorkloadMonitor/notificationSettings/write | Configurar definições de notificação para o recurso |
> | Acção | Microsoft.WorkloadMonitor/operations/read | Obtém as operações suportadas |

## <a name="next-steps"></a>Passos Seguintes

- [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Built-in roles for Azure resources](built-in-roles.md) (Funções incorporadas para recursos do Azure)
