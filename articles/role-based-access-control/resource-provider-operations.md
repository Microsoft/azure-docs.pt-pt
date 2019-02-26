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
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c296ed4011b874eb9b5635735d46b3f63a79a2be
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807563"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operações de fornecedor de recursos do Azure Resource Manager

Este artigo lista as operações disponíveis para cada fornecedor de recursos do Azure Resource Manager. Estas operações podem ser utilizadas em [funções personalizadas](custom-roles.md) para fornecer granular [controlo de acesso baseado em funções (RBAC)](overview.md) aos recursos no Azure. Cadeias de caracteres de operação tem o seguinte formato: `{Company}.{ProviderName}/{resourceType}/{action}`

As operações de fornecedor de recursos estão sempre a evoluir. Para obter as operações mais recente, utilize [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) ou [lista de operação de fornecedores de az](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.AAD/domainServices/delete | Eliminar o serviço de domínio |
> | Ação | Microsoft.AAD/domainServices/oucontainer/delete | Eliminar o contentor de UO |
> | Ação | Microsoft.AAD/domainServices/oucontainer/read | Leu os contentores de UO |
> | Ação | Microsoft.AAD/domainServices/oucontainer/write | Escrever o contentor de UO |
> | Ação | Microsoft.AAD/domainServices/read | Serviços de domínio de leitura |
> | Ação | Microsoft.AAD/domainServices/write | Escrever o serviço de domínio |
> | Ação | Microsoft.AAD/locations/operationresults/read |  |
> | Ação | Microsoft.AAD/Operations/read |  |
> | Ação | Microsoft.AAD/register/action | Registar o serviço de domínio |
> | Ação | Microsoft.AAD/unregister/action | Anular o registo do serviço de domínio |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | microsoft.aadiam/diagnosticsettings/delete | A eliminar uma definição de diagnóstico |
> | Ação | microsoft.aadiam/diagnosticsettings/read | Ler uma definição de diagnóstico |
> | Ação | microsoft.aadiam/diagnosticsettings/write | Escrever uma definição de diagnóstico |
> | Ação | microsoft.aadiam/diagnosticsettingscategories/read | Ler um categorias de definição de diagnóstico |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Addons/operations/read | Obtém suporte operações de RP. |
> | Ação | Microsoft.Addons/register/action | Registar a subscrição especificada com addons |
> | Ação | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Lista as informações de plano de suporte atual para a subscrição especificada. |
> | Ação | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Remove o plano de suporte Canonical especificado |
> | Ação | Microsoft.Addons/supportProviders/supportPlanTypes/read | Obter o estado de plano de suporte da Canonical especificado. |
> | Ação | Microsoft.Addons/supportProviders/supportPlanTypes/write | Adiciona o tipo de plano de suporte Canonical especificado. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ADHybridHealthService/addsservices/action | Crie uma nova floresta para o inquilino. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Obtém todos os servidores para o nome de serviço especificado. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/alerts/read | Obtém os detalhes de alertas para a data de floresta como alertid, alerta gerada, última detetada, alerta descrição do alerta, último atualizado, alerta nível, alerta Estado, alerta de resolução de problemas de ligações, etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/configuration/read | Obtém a configuração de serviço para a floresta. Exemplo - nome da floresta, nível funcional, a função FSMO de mestre de nomenclatura domínio, a função FSMO do mestre de esquema etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/delete | Elimina um serviço e isso servidores, juntamente com dados de estado de funcionamento. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Obtém os detalhes de domínios e sites para a floresta. Estado de funcionamento de exemplo, alertas ativos, resolver alertas, propriedades, como o nível funcional do domínio, floresta, mestre de infraestrutura, PDC, etc de mestre de RID.  |
> | Ação | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Obtém a definição da preferência de utilizador para a floresta.<br>Exemplo - MetricCounterName como ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Definições para o etc de gráficos de interface do Usuário. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Obtém floresta resumo de determinada floresta, como o nome da floresta, número de domínios nesta floresta, o número de sites e detalhes de sites etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Obtém a lista de métricas suportadas para um determinado serviço.<br>Para bloqueios de conta de Extranet de exemplo, Total de pedidos falhados, pedidos de Token pendentes (Proxy), Token pedidos por segundo etc para o serviço do AD FS.<br>NTLM autenticações/seg, LDAP bem-sucedida "binds" / seg, tempo de enlace de LDAP, Threads ativos do LDAP, Kerberos autenticações/seg, ATQ etc Total de Threads para ADDomainService.<br>Execute a latência de perfil, estabelecer ligações de TCP, Bytes privados do agente de informações, estatísticas de exportar para o Azure AD para o serviço ADSync. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Devido um serviço, esta API obtém as informações de métricas.<br>Por exemplo, esta API pode ser utilizada para obter informações relacionadas com a: Extranet bloqueios de conta, Total de pedidos falhados, Token pedidos pendentes (Proxy), Token pedidos por segundo etc para o serviço de ADFederation.<br>NTLM autenticações/seg, LDAP bem-sucedida "binds" / seg, tempo de enlace de LDAP, Threads ativos do LDAP, Kerberos autenticações/seg, ATQ etc Total de Threads para o serviço de ADDomain.<br>Latência de perfil de execução, o TCP ligações estabelecidas, Insights agente Private Bytes, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Esta API obtém a lista de todos os carregadas ADDomainServices para um inquilino de premium. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/read | Obtém os detalhes do serviço para o nome de serviço especificado. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Obtém os detalhes de replicação para todos os servidores para o nome de serviço especificado. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Obtém o número de controladores de domínio e os erros de replicação se houver. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Obtém conclua a lista de controlador de domínio, juntamente com detalhes de replicação para a floresta especificada. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Adicione uma instância de servidor para o serviço. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Durante o registo do servidor de ADDomainService, esta api é chamada para obter as credenciais para a integração de novos servidores. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Elimina um servidor para um determinado serviço e inquilino. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/write | Cria ou atualiza a instância de ADDomainService para o inquilino. |
> | Ação | Microsoft.ADHybridHealthService/configuration/action | Atualiza a configuração de inquilino. |
> | Ação | Microsoft.ADHybridHealthService/configuration/read | Lê a configuração do inquilino. |
> | Ação | Microsoft.ADHybridHealthService/configuration/write | Cria uma configuração de inquilino. |
> | Ação | Microsoft.ADHybridHealthService/logs/contents/read | Obtém o conteúdo de instalação do agente e os registos de registo armazenados no blob. |
> | Ação | Microsoft.ADHybridHealthService/logs/read | Obtém os registos de registo e de instalação do agente para o inquilino. |
> | Ação | Microsoft.ADHybridHealthService/operations/read | Obtém a lista de operações suportadas pelo sistema. |
> | Ação | Microsoft.ADHybridHealthService/register/action | Regista o fornecedor de recursos do serviço de estado de funcionamento ADHybrid e permite a criação do recurso de serviço de estado de funcionamento de ADHybrid. |
> | Ação | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Obtém a lista de regiões disponíveis, utilizado por DevOps para oferecer suporte a incidentes de cliente. |
> | Ação | Microsoft.ADHybridHealthService/reports/badpassword/read | Obtém a lista de tentativas de palavra-passe incorreta para todos os utilizadores no serviço de Federação do Active Directory. |
> | Ação | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Obtém o URI de SAS do Blob que contém a Estado e eventual resultado da tarefa para relatório de colocados em fila recentemente para a frequência de nome de utilizador/palavra-passe incorreta tenta por ID de utilizador por IPAddress por dia para um determinado inquilino. |
> | Ação | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Obtém a lista de DevOps consentidas por inquilinos. Normalmente utilizado para suporte ao cliente. |
> | Ação | Microsoft.ADHybridHealthService/reports/isdevops/read | Obtém um valor que indica se o inquilino seja o consentimento de DevOps ou não. |
> | Ação | Microsoft.ADHybridHealthService/reports/riskyIp/blobUri/action | Gera o relatório de IP em risco e devolve um URI apontando para ele. |
> | Ação | Microsoft.ADHybridHealthService/reports/riskyIp/blobUris/read | Obtém o relatório de IP em risco todos os URIs dos últimos 7 dias. |
> | Ação | Microsoft.ADHybridHealthService/reports/riskyIp/GetAllBlobUri/read | Obtém o uri de blob para o relatório de transferência de ip duvidoso pedido para um determinado serviço nos últimos 7 dias. |
> | Ação | Microsoft.ADHybridHealthService/reports/riskyIp/GetBlobUri/read | Obtém apenas o relatório de transferência de pedido de ip duvidoso atual para um determinado serviço. |
> | Ação | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Userid(objectid) de atualizações para o inquilino de ops dev selecionado. |
> | Ação | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Obtém a implementação selecionada para o inquilino especificado. |
> | Ação | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Tendo em conta um id de inquilino obtém a localização de armazenamento do inquilino. |
> | Ação | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Obtém a geo localização a partir do qual serão possível aceder aos dados. |
> | Ação | Microsoft.ADHybridHealthService/services/action | Atualiza uma instância de serviço no inquilino. |
> | Ação | Microsoft.ADHybridHealthService/services/alerts/read | Lê os alertas para um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/alerts/read | Lê os alertas para um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Devido um recurso nome verifica se um serviço tem tudo o que deve usar essa funcionalidade. |
> | Ação | Microsoft.ADHybridHealthService/services/delete | Elimina uma instância de serviço no inquilino. |
> | Ação | Microsoft.ADHybridHealthService/services/exporterrors/read | Obtém os erros de exportação para um serviço de sincronização especificada. |
> | Ação | Microsoft.ADHybridHealthService/services/exportstatus/read | Obtém o estado de exportação para um determinado serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Obtém comentários de alertas para um determinado serviço e o servidor. |
> | Ação | Microsoft.ADHybridHealthService/services/metricmetadata/read | Obtém a lista de métricas suportadas para um determinado serviço.<br>Para bloqueios de conta de Extranet de exemplo, Total de pedidos falhados, pedidos de Token pendentes (Proxy), Token pedidos por segundo etc para o serviço do AD FS.<br>NTLM autenticações/seg, LDAP bem-sucedida "binds" / seg, tempo de enlace de LDAP, Threads ativos do LDAP, Kerberos autenticações/seg, ATQ etc Total de Threads para ADDomainService.<br>Execute a latência de perfil, estabelecer ligações de TCP, Bytes privados do agente de informações, estatísticas de exportar para o Azure AD para o serviço ADSync. |
> | Ação | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Devido um serviço, esta API obtém a média de métricas para um determinado serviço.<br>Por exemplo, esta API pode ser utilizada para obter informações relacionadas com a: Extranet bloqueios de conta, Total de pedidos falhados, Token pedidos pendentes (Proxy), Token pedidos por segundo etc para o serviço de ADFederation.<br>NTLM autenticações/seg, LDAP bem-sucedida "binds" / seg, tempo de enlace de LDAP, Threads ativos do LDAP, Kerberos autenticações/seg, ATQ etc Total de Threads para o serviço de ADDomain.<br>Latência de perfil de execução, o TCP ligações estabelecidas, Insights agente Private Bytes, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/metrics/groups/read | Devido um serviço, esta API obtém as informações de métricas.<br>Por exemplo, esta API pode ser utilizada para obter informações relacionadas com a: Extranet bloqueios de conta, Total de pedidos falhados, Token pedidos pendentes (Proxy), Token pedidos por segundo etc para o serviço de ADFederation.<br>NTLM autenticações/seg, LDAP bem-sucedida "binds" / seg, tempo de enlace de LDAP, Threads ativos do LDAP, Kerberos autenticações/seg, ATQ etc Total de Threads para o serviço de ADDomain.<br>Latência de perfil de execução, o TCP ligações estabelecidas, Insights agente Private Bytes, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Devido um serviço, esta API obtém a vista de agregados de métricas para um determinado serviço.<br>Por exemplo, esta API pode ser utilizada para obter informações relacionadas com a: Extranet bloqueios de conta, Total de pedidos falhados, Token pedidos pendentes (Proxy), Token pedidos por segundo etc para o serviço de ADFederation.<br>NTLM autenticações/seg, LDAP bem-sucedida "binds" / seg, tempo de enlace de LDAP, Threads ativos do LDAP, Kerberos autenticações/seg, ATQ etc Total de Threads para o serviço de ADDomain.<br>Latência de perfil de execução, o TCP ligações estabelecidas, Insights agente Private Bytes, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Adicionar ou atualiza a configuração de monitorização para um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Obtém as configurações de monitorização para um determinado serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Adicionar ou configurações de monitorização para um serviço de atualizações. |
> | Ação | Microsoft.ADHybridHealthService/services/premiumcheck/read | Esta API obtém a lista de todos os serviços de integração para um inquilino de premium. |
> | Ação | Microsoft.ADHybridHealthService/services/read | Lê as instâncias de serviço no inquilino. |
> | Ação | Microsoft.ADHybridHealthService/services/reports/details/read | Relatório obtém dos 50 principais utilizadores com erros de palavra-passe incorreta dos últimos 7 dias |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/action | Cria uma instância de servidor no serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Lê os alertas para um servidor. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Durante o registo do servidor, esta api é chamada para obter as credenciais para a integração de novos servidores. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Para um determinado servidor, esta API obtém uma lista de tipos de dados que estão a ser carregados, os servidores e a última hora para cada carregamento. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/delete | Elimina uma instância de servidor no serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Obtém os detalhes do erro de exportação de sincronização para um determinado serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Devido um serviço, esta API obtém as informações de métricas.<br>Por exemplo, esta API pode ser utilizada para obter informações relacionadas com a: Extranet bloqueios de conta, Total de pedidos falhados, Token pedidos pendentes (Proxy), Token pedidos por segundo etc para o serviço de ADFederation.<br>NTLM autenticações/seg, LDAP bem-sucedida "binds" / seg, tempo de enlace de LDAP, Threads ativos do LDAP, Kerberos autenticações/seg, ATQ etc Total de Threads para o serviço de ADDomain.<br>Latência de perfil de execução, o TCP ligações estabelecidas, Insights agente Private Bytes, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/read | Lê a instância de servidor no serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Obtém a configuração de serviço para um determinado inquilino. |
> | Ação | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Obtém o estado de listas de permissões de funcionalidade para um determinado inquilino. |
> | Ação | Microsoft.ADHybridHealthService/services/write | Cria uma instância de serviço no inquilino. |
> | Ação | Microsoft.ADHybridHealthService/unregister/action | Anula o registo da subscrição para o fornecedor de recursos de serviço de estado de funcionamento de ADHybrid. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Advisor/configurations/read | Obter configurações |
> | Ação | Microsoft.Advisor/configurations/write | Cria/atualiza a configuração |
> | Ação | Microsoft.Advisor/generateRecommendations/action | Gere recomendações |
> | Ação | Microsoft.Advisor/generateRecommendations/read | Obtém gerar estado de recomendações |
> | Ação | Microsoft.Advisor/operations/read | Obtém as operações para o Microsoft Advisor |
> | Ação | Microsoft.Advisor/recommendations/available/action | Nova recomendação está disponível no advisor da Microsoft |
> | Ação | Microsoft.Advisor/recommendations/read | Recomendações de leituras |
> | Ação | Microsoft.Advisor/recommendations/suppressions/delete | Elimina a supressão |
> | Ação | Microsoft.Advisor/recommendations/suppressions/read | Obtém a supressão |
> | Ação | Microsoft.Advisor/recommendations/suppressions/write | Cria/atualizações supressões |
> | Ação | Microsoft.Advisor/register/action | Regista a subscrição para o Microsoft Advisor |
> | Ação | Microsoft.Advisor/suppressions/delete | Elimina a supressão |
> | Ação | Microsoft.Advisor/suppressions/read | Obtém a supressão |
> | Ação | Microsoft.Advisor/suppressions/write | Cria/atualizações supressões |
> | Ação | Microsoft.Advisor/unregister/action | Anula o registo da subscrição para o Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.AlertsManagement/actionRules/delete | Elimine regra de ação numa determinada subscrição. |
> | Ação | Microsoft.AlertsManagement/actionRules/read | Obtenha todas as regras de ação para os filtros de entrada. |
> | Ação | Microsoft.AlertsManagement/actionRules/write | Criar ou atualizar regra de ação numa determinada subscrição |
> | Ação | Microsoft.AlertsManagement/alerts/changestate/action | Altere o estado do alerta. |
> | Ação | Microsoft.AlertsManagement/alerts/diagnostics/read | Obter todos os diagnósticos para o alerta |
> | Ação | Microsoft.AlertsManagement/alerts/history/read | Obter histórico do alerta |
> | Ação | Microsoft.AlertsManagement/alerts/read | Obter todos os alertas para os filtros de entrada. |
> | Ação | Microsoft.AlertsManagement/alertsList/read | Obter todos os alertas para os filtros de entrada entre subscrições |
> | Ação | Microsoft.AlertsManagement/alertsSummary/read | Obtenha o resumo de alertas |
> | Ação | Microsoft.AlertsManagement/alertsSummaryList/read | Obtenha o resumo de alertas entre subscrições |
> | Ação | Microsoft.AlertsManagement/Operations/read | Lê as operações fornecidas |
> | Ação | Microsoft.AlertsManagement/register/action | Regista a subscrição para a gestão de alertas da Microsoft |
> | Ação | Microsoft.AlertsManagement/smartGroups/changestate/action | Alterar o estado do grupo inteligente |
> | Ação | Microsoft.AlertsManagement/smartGroups/history/read | Obter histórico do grupo inteligente |
> | Ação | Microsoft.AlertsManagement/smartGroups/read | Obter todos os grupos de inteligentes para os filtros de entrada |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Verifica se do Analysis Server fornecido nome é válido e não em utilização. |
> | Ação | Microsoft.AnalysisServices/locations/operationresults/read | Obtém as informações do resultado da operação especificado. |
> | Ação | Microsoft.AnalysisServices/locations/operationstatuses/read | Obtém as informações de estado da operação especificado. |
> | Ação | Microsoft.AnalysisServices/operations/read | Obtém as informações de operações |
> | Ação | Microsoft.AnalysisServices/register/action | Regista o fornecedor de recursos de Analysis Services. |
> | Ação | Microsoft.AnalysisServices/servers/delete | Elimina o Analysis Server. |
> | Ação | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Liste o estado do gateway associado ao servidor. |
> | Ação | Microsoft.AnalysisServices/servers/read | Obtém as informações de que o Analysis Server especificado. |
> | Ação | Microsoft.AnalysisServices/servers/resume/action | Retoma o Analysis Server. |
> | Ação | Microsoft.AnalysisServices/servers/skus/read | Obter informações do SKU disponíveis para o servidor |
> | Ação | Microsoft.AnalysisServices/servers/suspend/action | Suspende o Analysis Server. |
> | Ação | Microsoft.AnalysisServices/servers/write | Cria ou atualiza o Analysis Server especificado. |
> | Ação | Microsoft.AnalysisServices/skus/read | Obtém as informações dos Skus |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ApiManagement/checkNameAvailability/read | Verifica se for indicado o nome do serviço está disponível |
> | Ação | Microsoft.ApiManagement/operations/read | Ler todas as operações API disponíveis para o recurso de Microsoft.ApiManagement |
> | Ação | Microsoft.ApiManagement/register/action | Registar subscrição para o fornecedor de recursos de Microsoft.ApiManagement |
> | Ação | Microsoft.ApiManagement/reports/read | Obter relatórios agregados por períodos de tempo, região geográfica, desenvolvedores, produtos, APIs, operações, subscrição e byRequest. |
> | Ação | Microsoft.ApiManagement/service/apis/delete | Remover a API existente |
> | Ação | Microsoft.ApiManagement/service/apis/diagnostics/delete | Remover o diagnóstico existente |
> | Ação | Microsoft.ApiManagement/service/apis/diagnostics/read | Obter lista de diagnóstico ou Obtenha detalhes de diagnóstico |
> | Ação | Microsoft.ApiManagement/service/apis/diagnostics/write | Adicionar novo diagnóstico ou atualizar detalhes do diagnóstico existente |
> | Ação | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Remove existente anexo |
> | Ação | Microsoft.ApiManagement/service/apis/issues/attachments/read | Obter anexos de problema ou problema de gestão de API obtém os detalhes de anexo |
> | Ação | Microsoft.ApiManagement/service/apis/issues/attachments/write | Adicionar anexo do problema de api |
> | Ação | Microsoft.ApiManagement/service/apis/issues/comments/delete | Remove existente comentário |
> | Ação | Microsoft.ApiManagement/service/apis/issues/comments/read | Obtém emite comentários ou problema de gestão de API obtém os detalhes de comentário |
> | Ação | Microsoft.ApiManagement/service/apis/issues/comments/write | Adicionar comentário de problema de api |
> | Ação | Microsoft.ApiManagement/service/apis/issues/delete | Remove existente do problema |
> | Ação | Microsoft.ApiManagement/service/apis/issues/read | Obter problemas associados à API ou gestão de API obtém detalhes do problema |
> | Ação | Microsoft.ApiManagement/service/apis/issues/write | Adicionar api problema ou atualizar o problema de api |
> | Ação | Microsoft.ApiManagement/service/apis/operations/delete | Remover a operação de API existente |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policies/delete | Remover configuração de política de políticas de operação de API |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policies/read | Obter políticas para a operação de API ou detalhes de configuração de política de Get para a operação de API |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policies/write | Definir os detalhes de configuração de política para operação de API |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policy/delete | Remover configuração de política de operação |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policy/read | Obter detalhes de configuração de política para operação |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policy/write | Definir os detalhes de configuração de política para a operação |
> | Ação | Microsoft.ApiManagement/service/apis/operations/read | Obter lista de operações de API existentes ou obter os detalhes da operação de API |
> | Ação | Microsoft.ApiManagement/service/apis/operations/tags/delete | Eliminar a associação de uma etiqueta existente com a operação existente |
> | Ação | Microsoft.ApiManagement/service/apis/operations/tags/read | Obter as marcas associadas com os detalhes da operação ou obter etiqueta |
> | Ação | Microsoft.ApiManagement/service/apis/operations/tags/write | Associar a etiqueta existente com a operação existente |
> | Ação | Microsoft.ApiManagement/service/apis/operations/write | Criar nova operação de API ou atualizar a operação de API existente |
> | Ação | Microsoft.ApiManagement/service/apis/operationsByTags/read | Obter lista de associações de Tag/operação |
> | Ação | Microsoft.ApiManagement/service/apis/policies/delete | Remover configuração de política de políticas de API |
> | Ação | Microsoft.ApiManagement/service/apis/policies/read | Obter políticas para detalhes de configuração de política de API ou Get para a API |
> | Ação | Microsoft.ApiManagement/service/apis/policies/write | Detalhes de configuração de política de conjunto de API |
> | Ação | Microsoft.ApiManagement/service/apis/policy/delete | Remover configuração de política de API |
> | Ação | Microsoft.ApiManagement/service/apis/policy/read | Obter detalhes de configuração de política de API |
> | Ação | Microsoft.ApiManagement/service/apis/policy/write | Detalhes de configuração de política de conjunto de API |
> | Ação | Microsoft.ApiManagement/service/apis/products/read | Obter todos os produtos que a API faz parte do |
> | Ação | Microsoft.ApiManagement/service/apis/read | Obter lista de todos os registados APIs ou Get detalhes da API |
> | Ação | Microsoft.ApiManagement/service/apis/releases/delete | Remove todas as versões da versão de API ou remover API |
> | Ação | Microsoft.ApiManagement/service/apis/releases/read | Obtenha versões para uma API ou Get detalhes da versão de API |
> | Ação | Microsoft.ApiManagement/service/apis/releases/write | Criar nova versão de API ou atualizar a versão de API existente |
> | Ação | Microsoft.ApiManagement/service/apis/revisions/delete | Remove todas as revisões de uma API |
> | Ação | Microsoft.ApiManagement/service/apis/revisions/read | Obter revisões que pertencem a uma API |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/delete | Remove existente de esquema |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/document/read | Obter o documento que descreve o esquema |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/document/write | Atualize o documento que descreve o esquema |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/read | Obtém todos os esquemas para uma determinada API ou obtém os esquemas usados pela API |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/write | Define os esquemas usados pela API |
> | Ação | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Remover descrição da etiqueta da API |
> | Ação | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Obter as descrições de etiquetas no âmbito da API ou a Tag de obter descrição no âmbito da API |
> | Ação | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Descrição da etiqueta de criar/alterar no âmbito da API |
> | Ação | Microsoft.ApiManagement/service/apis/tags/delete | Remover a associação de API/etiqueta existente |
> | Ação | Microsoft.ApiManagement/service/apis/tags/read | Obter todas as API/etiqueta associação para os detalhes de API ou Get de associação API/etiqueta |
> | Ação | Microsoft.ApiManagement/service/apis/tags/write | Adicionar nova associação de API/etiqueta |
> | Ação | Microsoft.ApiManagement/service/apis/write | Criar nova API ou atualizar os detalhes de API existentes |
> | Ação | Microsoft.ApiManagement/service/apisByTags/read | Obter lista de associações de API/etiqueta |
> | Ação | Microsoft.ApiManagement/service/apiVersionSets/delete | Remover VersionSet existente |
> | Ação | Microsoft.ApiManagement/service/apiVersionSets/read | Obter lista de entidades do grupo de versão ou obtém detalhes de um VersionSet |
> | Ação | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Obter lista de entidades de versão |
> | Ação | Microsoft.ApiManagement/service/apiVersionSets/write | Criar novo VersionSet ou atualizar os detalhes de VersionSet existentes |
> | Ação | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Atualiza os recursos de Microsoft.ApiManagement executados na rede Virtual para escolher as definições de rede atualizado. |
> | Ação | Microsoft.ApiManagement/service/authorizationServers/delete | Remover servidor de autorização existente |
> | Ação | Microsoft.ApiManagement/service/authorizationServers/read | Obter lista de servidores de autorização ou obter os detalhes do servidor de autorização |
> | Ação | Microsoft.ApiManagement/service/authorizationServers/write | Criar um novo servidor de autorização ou detalhes da atualização de um servidor de autorização existente |
> | Ação | Microsoft.ApiManagement/service/backends/delete | Remover o back-end existente |
> | Ação | Microsoft.ApiManagement/service/backends/read | Obter lista de back-ends ou obter os detalhes de back-end |
> | Ação | Microsoft.ApiManagement/service/backends/reconnect/action | Criar um pedido de restabelecimento de ligação |
> | Ação | Microsoft.ApiManagement/service/backends/write | Adicionar um novo back-end ou atualizar os detalhes de back-end existentes |
> | Ação | Microsoft.ApiManagement/service/backup/action | Serviço de gestão de API de cópia de segurança no contentor especificado num utilizador forneceu a conta de armazenamento |
> | Ação | Microsoft.ApiManagement/service/certificates/delete | Remover o certificado existente |
> | Ação | Microsoft.ApiManagement/service/certificates/read | Obter a lista de certificados ou obter os detalhes do certificado |
> | Ação | Microsoft.ApiManagement/service/certificates/write | Adicionar novo certificado |
> | Ação | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Remove especificado o item de conteúdo. |
> | Ação | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Devolve a lista de itens de conteúdo ou detalhes do item de conteúdo de devolve |
> | Ação | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Cria o novo item de conteúdo ou atualiza o item de conteúdo especificado |
> | Ação | Microsoft.ApiManagement/service/contentTypes/read | Devolve a lista de tipos de conteúdo |
> | Ação | Microsoft.ApiManagement/service/delete | Eliminar a instância de serviço de gestão de API |
> | Ação | Microsoft.ApiManagement/service/diagnostics/delete | Remover o diagnóstico existente |
> | Ação | Microsoft.ApiManagement/service/diagnostics/read | Obter lista de diagnóstico ou Obtenha detalhes de diagnóstico |
> | Ação | Microsoft.ApiManagement/service/diagnostics/write | Adicionar novo diagnóstico ou atualizar detalhes do diagnóstico existente |
> | Ação | Microsoft.ApiManagement/service/getssotoken/action | Obtém o token de SSO que pode ser utilizado para iniciar sessão no portal de legado de serviço de gestão de API como administrador |
> | Ação | Microsoft.ApiManagement/service/groups/delete | Remover o grupo existente |
> | Ação | Microsoft.ApiManagement/service/groups/read | Obter lista de grupos ou obtém detalhes de um grupo |
> | Ação | Microsoft.ApiManagement/service/groups/users/delete | Remover utilizador existente do grupo existente |
> | Ação | Microsoft.ApiManagement/service/groups/users/read | Obter lista de utilizadores do grupo |
> | Ação | Microsoft.ApiManagement/service/groups/users/write | Adicionar utilizador existente para o grupo existente |
> | Ação | Microsoft.ApiManagement/service/groups/write | Criar novo grupo ou atualizar detalhes do grupo existente |
> | Ação | Microsoft.ApiManagement/service/identityProviders/delete | Remover fornecedor de identidade existente |
> | Ação | Microsoft.ApiManagement/service/identityProviders/read | Obter lista de fornecedores de identidade ou Obtenha detalhes do fornecedor de identidade |
> | Ação | Microsoft.ApiManagement/service/identityProviders/write | Criar um novo detalhes do fornecedor de identidade ou de atualização de um fornecedor de identidade existente |
> | Ação | Microsoft.ApiManagement/service/locations/networkstatus/read | Obtém o estado de acesso de rede de recursos no qual o serviço depende na localização. |
> | Ação | Microsoft.ApiManagement/service/loggers/delete | Remover agente de log existente |
> | Ação | Microsoft.ApiManagement/service/loggers/read | Obter lista de agentes ou obter os detalhes do agente de log |
> | Ação | Microsoft.ApiManagement/service/loggers/write | Adicionar novo logger ou atualizar os detalhes do agente de log existente |
> | Ação | Microsoft.ApiManagement/service/managedeployments/action | Alterar o SKU/unidades, adicionar ou remover implementações regionais do serviço de gestão de API |
> | Ação | Microsoft.ApiManagement/service/networkstatus/read | Obtém o estado de acesso de rede de recursos no qual o serviço depende. |
> | Ação | Microsoft.ApiManagement/service/notifications/action | Envia a notificação de um utilizador especificado |
> | Ação | Microsoft.ApiManagement/service/notifications/read | Obtém todas as notificações de editor de gestão de API ou Editor de gestão de API de obter os detalhes de notificação |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Remove existente associado a uma notificação de E-Mail |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Obtenha os destinatários de E-mail associado à notificação de editor de gestão de API |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Criar novo de E-Mail de destinatário da notificação |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Remove o utilizador associado para os destinatários de notificação |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Fazer com que os utilizadores de destinatário associadas com a notificação |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Adicionar o utilizador para os destinatários de notificação |
> | Ação | Microsoft.ApiManagement/service/notifications/write | Criar ou notificação de editor de gestão de API de atualização |
> | Ação | Microsoft.ApiManagement/service/openidConnectProviders/delete | Remover fornecedor de ligação OpenID existente |
> | Ação | Microsoft.ApiManagement/service/openidConnectProviders/read | Obter lista de fornecedores de OpenID Connect ou Obtenha detalhes do fornecedor de ligação OpenID |
> | Ação | Microsoft.ApiManagement/service/openidConnectProviders/write | Criar um novo detalhes do fornecedor de ligação OpenID ou atualização de um fornecedor de ligação do OpenID existente |
> | Ação | Microsoft.ApiManagement/service/operationresults/read | Obtém o estado atual da operação de longa |
> | Ação | Microsoft.ApiManagement/service/policies/delete | Remover configuração de política de políticas de inquilino |
> | Ação | Microsoft.ApiManagement/service/policies/read | Obter políticas para detalhes de configuração de política de inquilino ou Get para o inquilino |
> | Ação | Microsoft.ApiManagement/service/policies/write | Definir os detalhes de configuração de política para o inquilino |
> | Ação | Microsoft.ApiManagement/service/policySnippets/read | Obter todos os fragmentos de política |
> | Ação | Microsoft.ApiManagement/service/portalsettings/read | Obter definições de inscrição para o Portal ou Get inscrever-se nas definições para o Portal ou obter as definições de delegação para o Portal |
> | Ação | Microsoft.ApiManagement/service/portalsettings/write | Atualizar as definições de inscrever-se ou as definições de atualização inscrever-se ou as definições de atualização de sessão ou as definições de atualização de sessão ou as definições de delegação de atualização ou as definições de delegação de atualização |
> | Ação | Microsoft.ApiManagement/service/products/apis/delete | Remover a API existente de produto existente |
> | Ação | Microsoft.ApiManagement/service/products/apis/read | Obter lista de APIs adicionadas ao produto existente |
> | Ação | Microsoft.ApiManagement/service/products/apis/write | Adicionar API existente ao produto existente |
> | Ação | Microsoft.ApiManagement/service/products/delete | Remover produto existente |
> | Ação | Microsoft.ApiManagement/service/products/groups/delete | Eliminar a associação de grupo existente do desenvolvedor com o produto existente |
> | Ação | Microsoft.ApiManagement/service/products/groups/read | Obter lista de grupos de desenvolvedor associados ao produto |
> | Ação | Microsoft.ApiManagement/service/products/groups/write | Associar o grupo existente do desenvolvedor com o produto existente |
> | Ação | Microsoft.ApiManagement/service/products/policies/delete | Remover configuração de política de políticas de produto |
> | Ação | Microsoft.ApiManagement/service/products/policies/read | Obter políticas para detalhes de configuração de política do produto ou Get para o produto |
> | Ação | Microsoft.ApiManagement/service/products/policies/write | Definir os detalhes de configuração de política para o produto |
> | Ação | Microsoft.ApiManagement/service/products/policy/delete | Remover configuração de política de produto existente |
> | Ação | Microsoft.ApiManagement/service/products/policy/read | Obter configuração de política de produto existente |
> | Ação | Microsoft.ApiManagement/service/products/policy/write | Definir política de configuração para o produto existente |
> | Ação | Microsoft.ApiManagement/service/products/read | Obter a lista de produtos ou obter os detalhes do produto |
> | Ação | Microsoft.ApiManagement/service/products/subscriptions/read | Obter a lista de subscrições do produto |
> | Ação | Microsoft.ApiManagement/service/products/tags/delete | Eliminar a associação de uma etiqueta existente com o produto existente |
> | Ação | Microsoft.ApiManagement/service/products/tags/read | Obter as marcas associadas com os detalhes do produto ou obter etiqueta |
> | Ação | Microsoft.ApiManagement/service/products/tags/write | Associar a etiqueta existente com o produto existente |
> | Ação | Microsoft.ApiManagement/service/products/write | Criar novo produto ou atualizar detalhes do produto existente |
> | Ação | Microsoft.ApiManagement/service/productsByTags/read | Obter lista de associações de produto/etiqueta |
> | Ação | Microsoft.ApiManagement/service/properties/delete | Remove a propriedade de existente |
> | Ação | Microsoft.ApiManagement/service/properties/read | Obtém a lista de todas as propriedades ou obtém os detalhes de propriedade especificada |
> | Ação | Microsoft.ApiManagement/service/properties/write | Cria uma nova propriedade ou atualiza o valor para a propriedade especificada |
> | Ação | Microsoft.ApiManagement/service/quotas/periods/read | Obter o valor do contador de quota para o período |
> | Ação | Microsoft.ApiManagement/service/quotas/periods/write | Defina o valor atual de contador de quota |
> | Ação | Microsoft.ApiManagement/service/quotas/read | Obter os valores para a quota |
> | Ação | Microsoft.ApiManagement/service/quotas/write | Defina o valor atual de contador de quota |
> | Ação | Microsoft.ApiManagement/service/read | Ler metadados para uma instância de serviço de gestão de API |
> | Ação | Microsoft.ApiManagement/service/reports/read | Obter relatório agregado por períodos de tempo ou de relatório de Get agregados por região geográfica ou relatório Get agregados por desenvolvedores.<br>ou obtenha relatórios agregados por produtos.<br>ou obtenha relatórios agregados pelo relatório APIs ou Get agregado por operações ou o relatório de Get agregados por subscrição.<br>ou obter pedidos de dados de relatórios |
> | Ação | Microsoft.ApiManagement/service/restore/action | Restaurar o serviço de gestão de API do contêiner especificado um utilizador fornecido a conta de armazenamento |
> | Ação | Microsoft.ApiManagement/service/subscriptions/delete | Elimine subscrição. Esta operação pode ser utilizada para eliminar a subscrição |
> | Ação | Microsoft.ApiManagement/service/subscriptions/read | Obter uma lista de subscrições de produto ou obter os detalhes da subscrição do produto |
> | Ação | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Regenerar a chave primária da subscrição |
> | Ação | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Regenerar chave secundária da subscrição |
> | Ação | Microsoft.ApiManagement/service/subscriptions/write | Subscrever um utilizador existente a um produto existente ou atualizar os detalhes da subscrição existente. Esta operação pode ser utilizada para renovar a subscrição |
> | Ação | Microsoft.ApiManagement/service/tagResources/read | Obter a lista de etiquetas com recursos associados |
> | Ação | Microsoft.ApiManagement/service/tags/delete | Remover etiqueta existente |
> | Ação | Microsoft.ApiManagement/service/tags/read | Obter a lista de etiquetas ou obter os detalhes da marca |
> | Ação | Microsoft.ApiManagement/service/tags/write | Adicionar etiqueta nova ou atualizar os detalhes da etiqueta existentes |
> | Ação | Microsoft.ApiManagement/service/templates/delete | Repor o modelo de correio eletrónico de gestão de API de predefinição |
> | Ação | Microsoft.ApiManagement/service/templates/read | Obtém todos os modelos de e-mail ou correio eletrónico de gestão de API obtém os detalhes do modelo |
> | Ação | Microsoft.ApiManagement/service/templates/write | Criar ou atualizar o modelo de correio eletrónico de gestão de API ou o modelo de correio eletrónico de gestão de API de atualizações |
> | Ação | Microsoft.ApiManagement/service/tenant/delete | Remover configuração de política para o inquilino |
> | Ação | Microsoft.ApiManagement/service/tenant/deploy/action | Executa uma tarefa de implementação para aplicar as alterações da ramificação git especificado para a configuração na base de dados. |
> | Ação | Microsoft.ApiManagement/service/tenant/operationResults/read | Obter lista de resultados da operação ou obter o resultado de uma operação específica |
> | Ação | Microsoft.ApiManagement/service/tenant/read | Obter configuração de política para o inquilino ou Get os detalhes de informações de acesso |
> | Ação | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Regenerar chave de acesso primária |
> | Ação | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Regenerar chave de acesso secundária |
> | Ação | Microsoft.ApiManagement/service/tenant/save/action | Cria a consolidação com instantâneo de configuração para o ramo especificado no repositório |
> | Ação | Microsoft.ApiManagement/service/tenant/syncState/read | Obter o estado da última sincronização do git |
> | Ação | Microsoft.ApiManagement/service/tenant/validate/action | Valida as alterações da ramificação git especificado |
> | Ação | Microsoft.ApiManagement/service/tenant/write | Definir política de configuração para o inquilino ou detalhes de informações de acesso de inquilino de atualização |
> | Ação | Microsoft.ApiManagement/service/updatecertificate/action | Carregar o certificado SSL para um serviço de gestão de API |
> | Ação | Microsoft.ApiManagement/service/updatehostname/action | Configurar, atualizar ou remover nomes de domínio personalizado para um serviço de gestão de API |
> | Ação | Microsoft.ApiManagement/service/users/action | Registar um novo utilizador |
> | Ação | Microsoft.ApiManagement/service/users/applications/attachments/delete | Remove um anexo |
> | Ação | Microsoft.ApiManagement/service/users/applications/attachments/read | Obtém os anexos de aplicativo ou obtém anexo |
> | Ação | Microsoft.ApiManagement/service/users/applications/attachments/write | Adicionar anexo à aplicação |
> | Ação | Microsoft.ApiManagement/service/users/applications/delete | Remove existente da aplicação |
> | Ação | Microsoft.ApiManagement/service/users/applications/read | Obter lista de todos os aplicativos de usuário ou detalhes da aplicação obtém a gestão de API |
> | Ação | Microsoft.ApiManagement/service/users/applications/write | Registra um aplicativo para a gestão de API ou atualizações de detalhes da aplicação |
> | Ação | Microsoft.ApiManagement/service/users/confirmations/send/action | Envia a confirmação |
> | Ação | Microsoft.ApiManagement/service/users/delete | Remover conta de utilizador |
> | Ação | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Gere o URL de SSO. O URL pode ser utilizado para aceder ao portal de administração |
> | Ação | Microsoft.ApiManagement/service/users/groups/read | Obter lista de grupos de utilizadores |
> | Ação | Microsoft.ApiManagement/service/users/keys/read | Obter lista de chaves de utilizador |
> | Ação | Microsoft.ApiManagement/service/users/read | Obter uma lista de usuários registrados ou obter os detalhes da conta de um utilizador |
> | Ação | Microsoft.ApiManagement/service/users/subscriptions/read | Obter lista de subscrições do utilizador |
> | Ação | Microsoft.ApiManagement/service/users/token/action | Obter token token de acesso para um utilizador |
> | Ação | Microsoft.ApiManagement/service/users/write | Registar um novo utilizador ou os detalhes da conta de atualização de um utilizador existente |
> | Ação | Microsoft.ApiManagement/service/write | Criar uma nova instância do serviço de gestão de API |
> | Ação | Microsoft.ApiManagement/unregister/action | Anular o registo subscrição para o fornecedor de recursos de Microsoft.ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Authorization/classicAdministrators/delete | Remove o administrador da subscrição. |
> | Ação | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Obtém os estados de funcionamento do administrador da subscrição. |
> | Ação | Microsoft.Authorization/classicAdministrators/read | Lê os administradores da subscrição. |
> | Ação | Microsoft.Authorization/classicAdministrators/write | Adiciona ou modifica o administrador de uma subscrição. |
> | Ação | Microsoft.Authorization/denyAssignments/delete | Elimine uma atribuição de negação no âmbito especificado. |
> | Ação | Microsoft.Authorization/denyAssignments/read | Obtenha informações sobre uma atribuição de negação. |
> | Ação | Microsoft.Authorization/denyAssignments/write | Crie uma atribuição de negação no âmbito especificado. |
> | Ação | Microsoft.Authorization/elevateAccess/action | Garante ao chamador Administrador de Acesso dos Utilizadores acesso ao âmbito do inquilino |
> | Ação | Microsoft.Authorization/locks/delete | Elimina bloqueios no âmbito especificado. |
> | Ação | Microsoft.Authorization/locks/read | Obtém bloqueios no âmbito especificado. |
> | Ação | Microsoft.Authorization/locks/write | Adiciona bloqueios ao âmbito especificado. |
> | Ação | Microsoft.Authorization/operations/read | Obtém a lista de operações |
> | Ação | Microsoft.Authorization/permissions/read | Lista todas as permissões que o chamador tem num determinado âmbito. |
> | Ação | Microsoft.Authorization/policyAssignments/delete | Elimine uma atribuição de política no âmbito especificado. |
> | Ação | Microsoft.Authorization/policyAssignments/read | Obtenha informação sobre uma atribuição de política. |
> | Ação | Microsoft.Authorization/policyAssignments/write | Crie uma atribuição de política no âmbito especificado. |
> | Ação | Microsoft.Authorization/policyDefinitions/delete | Elimine uma definição de política. |
> | Ação | Microsoft.Authorization/policyDefinitions/read | Obtenha informação sobre uma definição de política. |
> | Ação | Microsoft.Authorization/policyDefinitions/write | Crie uma definição de política personalizada. |
> | Ação | Microsoft.Authorization/policySetDefinitions/delete | Elimine uma definição do conjunto de políticas. |
> | Ação | Microsoft.Authorization/policySetDefinitions/read | Obter informações sobre uma definição do conjunto de políticas. |
> | Ação | Microsoft.Authorization/policySetDefinitions/write | Crie uma definição do conjunto de políticas personalizada. |
> | Ação | Microsoft.Authorization/providerOperations/read | Obter operações para todos os fornecedores de recursos que possam ser utilizadas em definições de funções. |
> | Ação | Microsoft.Authorization/roleAssignments/delete | Elimina uma atribuição de função no âmbito especificado. |
> | Ação | Microsoft.Authorization/roleAssignments/read | Obtém informações sobre uma atribuição de função. |
> | Ação | Microsoft.Authorization/roleAssignments/write | Cria uma atribuição de função no âmbito especificado. |
> | Ação | Microsoft.Authorization/roleDefinitions/delete | Eliminar a definição de função personalizada especificada. |
> | Ação | Microsoft.Authorization/roleDefinitions/read | Obtém informação sobre uma definição de função. |
> | Ação | Microsoft.Authorization/roleDefinitions/write | Criar ou atualizar uma definição de função personalizada com permissões especificadas e âmbitos atribuíveis. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Ler as informações de registo do DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Escreve um pedido para voltar a gerar chaves de DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/certificates/delete | Elimina um recurso de certificado da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/certificates/getCount/action | Lê a contagem de certificados |
> | Ação | Microsoft.Automation/automationAccounts/certificates/read | Obtém um recurso de certificado da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/certificates/write | Cria ou atualiza um recurso de certificado da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/read | Lê a compilação do DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/read | Lê a compilação do DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/write | Escreve a compilação do DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/write | Escreve a compilação do DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/content/read | Lê o conteúdo do suporte de dados de configuração |
> | Ação | Microsoft.Automation/automationAccounts/configurations/delete | Elimina o conteúdo do DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/getCount/action | Lê a contagem de conteúdo do DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/read | Obtém o conteúdo do DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/write | Escreve o conteúdo do DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connections/delete | Elimina um recurso de ligação da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connections/getCount/action | Lê a contagem de ligações |
> | Ação | Microsoft.Automation/automationAccounts/connections/read | Obtém um recurso de ligação da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connections/write | Cria ou atualiza um recurso de ligação da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connectionTypes/delete | Elimina um recurso de tipo de ligação da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connectionTypes/read | Obtém um recurso de tipo de ligação da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connectionTypes/write | Cria um recurso de tipo de ligação da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/credentials/delete | Elimina um recurso de credencial da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/credentials/getCount/action | Lê a contagem de credenciais |
> | Ação | Microsoft.Automation/automationAccounts/credentials/read | Obtém um recurso de credencial da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/credentials/write | Cria ou atualiza um recurso de credenciais da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/delete | Elimina uma conta de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Elimina recursos de trabalho de Runbook híbrida |
> | Ação | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lê recursos de trabalho de Runbook híbrida |
> | Ação | Microsoft.Automation/automationAccounts/jobs/output/read | Obtém o resultado de uma tarefa |
> | Ação | Microsoft.Automation/automationAccounts/jobs/read | Obtém uma tarefa da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/resume/action | Retoma uma tarefa da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Obtém o conteúdo do runbook de automatização do Azure no momento da execução da tarefa |
> | Ação | Microsoft.Automation/automationAccounts/jobs/stop/action | Para uma tarefa da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtém um fluxo de tarefas de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtém um fluxo de tarefas de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende uma tarefa da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/write | Cria uma tarefa da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobSchedules/delete | Elimina um plano de trabalho de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobSchedules/read | Obtém um plano de trabalho de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobSchedules/write | Cria um plano de trabalho de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtém a área de trabalho ligada à conta de automatização |
> | Ação | Microsoft.Automation/automationAccounts/listKeys/action | Lê as chaves da conta de automatização |
> | Ação | Microsoft.Automation/automationAccounts/modules/activities/read | Obtém as actividades de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/modules/delete | Elimina um módulo do Powershell da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/modules/getCount/action | Obtém a contagem de módulos do Powershell na conta de automatização |
> | Ação | Microsoft.Automation/automationAccounts/modules/read | Obtém um módulo do Powershell da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/modules/write | Cria ou atualiza um módulo do Powershell da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Elimina a configuração do nó do DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Lê o conteúdo de configuração do nó do DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Lê a configuração do nó do DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Escreve a configuração do nó do DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodecounts/read | Lê o resumo de contagem de nós para o tipo especificado |
> | Ação | Microsoft.Automation/automationAccounts/nodes/delete | Elimina nós de DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/read | Lê nós de DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Lê o conteúdo do relatório de DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/reports/read | Lê os relatórios de DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/write | Cria ou atualiza nós de DSC de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Obtém TypeFields de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python2Packages/delete | Elimina um pacote de Python 2 automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python2Packages/read | Obtém um pacote de Python 2 automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python2Packages/write | Cria ou atualiza um pacote de Python 2 automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python3Packages/delete | Elimina um pacote de Python 3 automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python3Packages/read | Obtém um pacote de Python 3 automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python3Packages/write | Cria ou atualiza um pacote de Python 3 automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/read | Obtém uma conta de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/content/read | Obtém o conteúdo de um runbook da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/delete | Elimina um runbook da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Cria o conteúdo de um rascunho de runbook da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Obtém os resultados da operação de rascunho de runbook da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/read | Obtém um rascunho de runbook da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Obtém uma tarefa de teste de rascunho de runbook de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Retoma uma tarefa de teste de rascunho de runbook de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Parar uma tarefa de teste de rascunho de runbook de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Suspende uma tarefa de teste de rascunho de runbook de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Cria uma tarefa de teste de rascunho de runbook de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Anular edições feitas um rascunho de runbook da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Obtém o número de runbooks da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publica um rascunho de runbook da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/read | Obtém um runbook da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/write | Cria ou atualiza um runbook da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/delete | Elimina um recurso de agenda da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/getCount/action | Obtém a contagem de agendas da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/read | Obtém um recurso de agenda da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/write | Cria ou atualiza um recurso de agenda da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Elimina uma configuração de atualização de Software de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Obtém uma configuração de atualização de Software de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Cria ou atualiza a configuração de atualização de Software de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/statistics/read | Obtém estatísticas de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Obter um computador de implementação de atualização de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Obtém uma tarefa de patch de gestão de atualização de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/usages/read | Obtém a utilização da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/variables/delete | Elimina um recurso de variável da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/variables/read | Lê um recurso de variável da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/variables/write | Cria ou atualiza um recurso de variável da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/delete | Eliminar uma tarefa de observador de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/read | Obtém uma tarefa de observador de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/start/action | Iniciar uma tarefa de observador de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/stop/action | Parar uma tarefa de observador de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/streams/read | Obtém um fluxo de tarefas do observador de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Eliminar um ações de tarefas do observador de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Obtém um observador de automatização do Azure ações de tarefas |
> | Ação | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Criar ações de tarefas de um observador de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/write | Cria uma tarefa de observador de automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/action | Gera um URI para um webhook da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/delete | Elimina um webhook da automatização do Azure  |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/read | Lê um webhook da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/write | Cria ou atualiza um webhook da automatização do Azure |
> | Ação | Microsoft.Automation/automationAccounts/write | Cria ou atualiza uma conta de automatização do Azure |
> | Ação | Microsoft.Automation/operations/read | Obtém as operações disponíveis para os recursos de automatização do Azure |
> | Ação | Microsoft.Automation/register/action | Regista a subscrição da automatização do Azure |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Eliminar o recurso do Diretório B2C |
> | Ação | Microsoft.AzureActiveDirectory/b2cDirectories/read | Visualizar o recurso do Diretório B2C |
> | Ação | Microsoft.AzureActiveDirectory/b2cDirectories/write | Criar ou atualizar o recurso de diretório B2C |
> | Ação | Microsoft.AzureActiveDirectory/operations/read | Ler todas as operações de API disponíveis para o fornecedor de recursos de Microsoft.AzureActiveDirectory |
> | Ação | Microsoft.AzureActiveDirectory/register/action | Registar subscrição para o fornecedor de recursos de Microsoft.AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.AzureStack/Operations/read | Obtém as propriedades de uma operação de fornecedor de recursos |
> | Ação | Microsoft.AzureStack/register/action | Regista a subscrição no fornecedor de recursos de Microsoft.AzureStack |
> | Ação | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Elimina uma subscrição de cliente do Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/customerSubscriptions/read | Obtém as propriedades de uma subscrição de cliente do Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/customerSubscriptions/write | Cria ou atualiza uma subscrição de cliente do Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/delete | Elimina um registo do Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/getActivationKey/action | Obtém a chave de ativação mais recente do Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/products/listDetails/action | Obtém estendido detalhes para um produto do Azure Stack Marketplace |
> | Ação | Microsoft.AzureStack/registrations/products/read | Obtém as propriedades de um produto do Azure Stack Marketplace |
> | Ação | Microsoft.AzureStack/registrations/read | Obtém as propriedades de um registo do Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/write | Cria ou atualiza um registo do Azure Stack |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Batch/batchAccounts/applications/delete | Elimina uma aplicação |
> | Ação | Microsoft.Batch/batchAccounts/applications/read | Apresenta uma lista de aplicações ou obtém as propriedades de uma aplicação |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Ativa um pacote de aplicação |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/delete | Elimina um pacote de aplicação |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/read | Obtém as propriedades de um pacote de aplicação |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/write | Cria um novo pacote de aplicações ou atualiza um pacote de aplicação existente |
> | Ação | Microsoft.Batch/batchAccounts/applications/write | Cria uma nova aplicação ou atualiza uma aplicação existente |
> | Ação | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Obtém os resultados de uma operação de certificado de longa execução numa conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Cancela a falha na eliminação de um certificado de uma conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/certificates/delete | Elimina um certificado de uma conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/certificates/read | Apresenta uma lista de certificados numa conta do Batch ou obtém as propriedades de um certificado |
> | Ação | Microsoft.Batch/batchAccounts/certificates/write | Cria um novo certificado numa conta do Batch ou atualiza um certificado existente |
> | Ação | Microsoft.Batch/batchAccounts/delete | Elimina uma conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/listkeys/action | Listas de aceder a chaves para uma conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/operationResults/read | Obtém os resultados de uma longa operação de conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/poolOperationResults/read | Obtém os resultados de uma operação de agrupamento de longa execução numa conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/pools/delete | Elimina um agrupamento de uma conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Desativa o dimensionamento automático para um conjunto de conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/pools/read | Apresenta uma lista de agrupamentos de uma conta do Batch ou obtém as propriedades de um agrupamento |
> | Ação | Microsoft.Batch/batchAccounts/pools/stopResize/action | Redimensionar de paragens em curso uma operação num conjunto de conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/pools/write | Cria um novo conjunto numa conta do Batch ou atualiza um conjunto existente |
> | Ação | Microsoft.Batch/batchAccounts/read | Apresenta uma lista de contas do Batch ou obtém as propriedades de uma conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/regeneratekeys/action | Regenera chaves para uma conta do Batch de acesso |
> | Ação | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Sincroniza as chaves de acesso para a conta de armazenamento automática configuradas para uma conta do Batch |
> | Ação | Microsoft.Batch/batchAccounts/write | Cria uma nova conta do Batch ou atualiza uma conta existente do Batch |
> | Ação | Microsoft.Batch/locations/accountOperationResults/read | Obtém os resultados de uma longa operação de conta do Batch |
> | Ação | Microsoft.Batch/locations/checkNameAvailability/action | Verifica se o nome da conta é válido e não está em utilização. |
> | Ação | Microsoft.Batch/locations/quotas/read | Obtém as quotas de lote da subscrição especificada na região do Azure especificada |
> | Ação | Microsoft.Batch/operations/read | Lista as operações disponíveis no fornecedor de recursos do batch |
> | Ação | Microsoft.Batch/register/action | Regista a subscrição para o fornecedor de recursos do Batch e permite a criação de contas do Batch |
> | Ação | Microsoft.Batch/unregister/action | Anula o registo da subscrição para o fornecedor de recursos do Batch que impede a criação de contas do Batch |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Billing/billingAccounts/departments/read | Lista de todos os departamentos num âmbito de conta de faturação |
> | Ação | Microsoft.Billing/billingAccounts/enrollmentAccounts/read | Listar todas as contas de inscrição num âmbito de conta de faturação |
> | Ação | Microsoft.Billing/billingAccounts/read | Listar todas as contas de faturas que utilizador tem acesso à |
> | Ação | Microsoft.Billing/billingPeriods/read | Apresenta uma lista de períodos de faturação disponíveis |
> | Ação | Microsoft.Billing/departments/read | Lista de todos os departamentos que utilizador tem acesso à |
> | Ação | Microsoft.Billing/invoices/read | Notas fiscais disponíveis de listas |
> | Ação | Microsoft.Billing/register/action | Regista a subscrição no fornecedor de recursos de Microsoft.Billing |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.BingMaps/mapApis/Delete | Operação de Eliminação |
> | Ação | Microsoft.BingMaps/mapApis/listSecrets/action | Listar os Segredos |
> | Ação | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Ler Token de Autorização de Início de Sessão Único para o Recurso |
> | Ação | Microsoft.BingMaps/mapApis/Read | Operação de Leitura |
> | Ação | Microsoft.BingMaps/mapApis/regenerateKey/action | Regenera a Chave |
> | Ação | Microsoft.BingMaps/mapApis/Write | Operação de Escrita |
> | Ação | Microsoft.BingMaps/Operations/read | Descrição da operação. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | Ler quaisquer artefactos de esquemas |
> | Ação | Microsoft.Blueprint/blueprintAssignments/delete | Eliminar quaisquer artefactos de esquema |
> | Ação | Microsoft.Blueprint/blueprintAssignments/read | Ler quaisquer artefactos de esquemas |
> | Ação | Microsoft.Blueprint/blueprintAssignments/write | Criar ou atualizar artefactos de esquema |
> | Ação | Microsoft.Blueprint/blueprints/artifacts/delete | Eliminar quaisquer artefactos de esquema |
> | Ação | Microsoft.Blueprint/blueprints/artifacts/read | Ler quaisquer artefactos de esquemas |
> | Ação | Microsoft.Blueprint/blueprints/artifacts/write | Criar ou atualizar artefactos de esquema |
> | Ação | Microsoft.Blueprint/blueprints/delete | Eliminar quaisquer esquemas |
> | Ação | Microsoft.Blueprint/blueprints/read | Ler quaisquer esquemas |
> | Ação | Microsoft.Blueprint/blueprints/versions/artifacts/read | Ler quaisquer artefactos de esquemas |
> | Ação | Microsoft.Blueprint/blueprints/versions/delete | Eliminar quaisquer esquemas |
> | Ação | Microsoft.Blueprint/blueprints/versions/read | Ler quaisquer esquemas |
> | Ação | Microsoft.Blueprint/blueprints/versions/write | Criar ou atualizar quaisquer esquemas |
> | Ação | Microsoft.Blueprint/blueprints/write | Criar ou atualizar quaisquer esquemas |
> | Ação | Microsoft.Blueprint/register/action | Regista o Fornecedor de Recursos de Esquemas do Azure |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.BotService/botServices/channels/delete | Eliminar um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/channels/read | Ler um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/channels/write | Escrever um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/connections/delete | Eliminar um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/connections/read | Ler um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/connections/write | Escrever um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/delete | Eliminar um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/read | Ler um serviço de Bot |
> | Ação | Microsoft.BotService/botServices/write | Escrever um serviço de Bot |
> | Ação | Microsoft.BotService/locations/operationresults/read | Ler o estado de uma operação assíncrona |
> | Ação | Microsoft.BotService/Operations/read | Operações para todos os tipos de recursos de leitura |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Cache/checknameavailability/action | Verifica se um nome está disponível para utilização com uma nova Cache de Redis |
> | Ação | Microsoft.Cache/locations/operationresults/read | Obtém o resultado de uma operação de longa duração da qual o cabeçalho "Localização" foi devolvido anteriormente ao cliente |
> | Ação | Microsoft.Cache/operations/read | Lista as operações que o fornecedor "Microsoft.Cache" suporta. |
> | Ação | Microsoft.Cache/redis/delete | Eliminar a Cache de Redis completa |
> | Ação | Microsoft.Cache/redis/export/action | Exportar dados Redis para os blobs de armazenamento com prefixo no formato especificado |
> | Ação | Microsoft.Cache/redis/firewallRules/delete | Eliminar as regras de firewall do IP da Cache de Redis |
> | Ação | Microsoft.Cache/redis/firewallRules/read | Obter as regras de firewall do IP da Cache de Redis |
> | Ação | Microsoft.Cache/redis/firewallRules/write | Editar regras de firewall do IP de uma Cache de Redis |
> | Ação | Microsoft.Cache/redis/forceReboot/action | Forçar reinício de uma instância da cache, potencialmente com perda de dados. |
> | Ação | Microsoft.Cache/redis/import/action | Importar dados de um formato especificado a partir de vários blobs para Redis |
> | Ação | Microsoft.Cache/redis/linkedservers/delete | Eliminar Servidor Ligado de uma Cache de Redis |
> | Ação | Microsoft.Cache/redis/linkedservers/read | Obter Servidores Ligados associados a uma cache de redis. |
> | Ação | Microsoft.Cache/redis/linkedservers/write | Adicionar Servidor Ligado a uma Cache de Redis |
> | Ação | Microsoft.Cache/redis/listKeys/action | Ver o valor das chaves de acesso da Cache de Redis no portal de gestão |
> | Ação | Microsoft.Cache/redis/listUpgradeNotifications/read | Listar as Notificações de Atualização mais recentes para o inquilino da cache. |
> | Ação | Microsoft.Cache/redis/metricDefinitions/read | Obtem as métricas disponíveis para uma Cache de Redis |
> | Ação | Microsoft.Cache/redis/patchSchedules/delete | Eliminar a agenda de correção da Cache de Redis |
> | Ação | Microsoft.Cache/redis/patchSchedules/read | Obtém a agenda de correções de uma Cache de Redis |
> | Ação | Microsoft.Cache/redis/patchSchedules/write | Modificar a agenda de correções de uma Cache de Redis |
> | Ação | Microsoft.Cache/redis/read | Ver as definições e a configuração da Cache de Redis no portal de gestão |
> | Ação | Microsoft.Cache/redis/regenerateKey/action | Alterar o valor das chaves de acesso da Cache de Redis no portal de gestão |
> | Ação | Microsoft.Cache/redis/start/action | Iniciar uma instância de cache. |
> | Ação | Microsoft.Cache/redis/stop/action | Parar uma instância de cache. |
> | Ação | Microsoft.Cache/redis/write | Modificar as definições e a configuração da Cache de Redis no portal de gestão |
> | Ação | Microsoft.Cache/register/action | Regista o fornecedor de recursos "Microsoft.Cache" com uma subscrição |
> | Ação | Microsoft.Cache/unregister/action | Anula o registo do fornecedor de recursos "Microsoft.Cache" com uma subscrição |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Capacity/appliedreservations/read | Ler todas as reservas |
> | Ação | Microsoft.Capacity/calculateprice/action | Calcular preço qualquer reserva |
> | Ação | Microsoft.Capacity/catalogs/read | Catálogo de leitura da reserva |
> | Ação | Microsoft.Capacity/checkoffers/action | Verifique quaisquer ofertas de subscrição |
> | Ação | Microsoft.Capacity/checkscopes/action | Verificar qualquer subscrição |
> | Ação | Microsoft.Capacity/commercialreservationorders/read | Obter as ordens de reserva criados em qualquer inquilino |
> | Ação | Microsoft.Capacity/operations/read | Qualquer operação de leitura |
> | Ação | Microsoft.Capacity/register/action | Regista o fornecedor de recursos de capacidade e ativa a criação de recursos de capacidade. |
> | Ação | Microsoft.Capacity/reservationorders/action | Atualizar qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/availablescopes/action | Localizar qualquer âmbito disponível |
> | Ação | Microsoft.Capacity/reservationorders/delete | Eliminar qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/merge/action | Intercalar qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/read | Ler todas as reservas |
> | Ação | Microsoft.Capacity/reservationorders/reservations/action | Atualizar qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/reservations/delete | Eliminar qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/reservations/read | Ler todas as reservas |
> | Ação | Microsoft.Capacity/reservationorders/reservations/revisions/read | Ler todas as reservas |
> | Ação | Microsoft.Capacity/reservationorders/reservations/write | Criar reserva de qualquer |
> | Ação | Microsoft.Capacity/reservationorders/return/action | Devolver qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/split/action | Dividir qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/swap/action | Trocar qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/write | Criar reserva de qualquer |
> | Ação | Microsoft.Capacity/tenants/register/action | Registar qualquer inquilino |
> | Ação | Microsoft.Capacity/unregister/action | Anular o registo de qualquer inquilino |
> | Ação | Microsoft.Capacity/validatereservationorder/action | Validar qualquer reserva |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Ação | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/edgenodes/delete |  |
> | Ação | Microsoft.Cdn/edgenodes/read |  |
> | Ação | Microsoft.Cdn/edgenodes/write |  |
> | Ação | Microsoft.Cdn/operationresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Ação | Microsoft.Cdn/operationresults/read |  |
> | Ação | Microsoft.Cdn/operationresults/write |  |
> | Ação | Microsoft.Cdn/operations/read |  |
> | Ação | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/profiles/delete |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/read |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Ação | Microsoft.Cdn/profiles/endpoints/write |  |
> | Ação | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Ação | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Ação | Microsoft.Cdn/profiles/read |  |
> | Ação | Microsoft.Cdn/profiles/write |  |
> | Ação | Microsoft.Cdn/register/action | Regista a subscrição para o fornecedor de recursos CDN e ativa a criação de perfis CDN. |
> | Ação | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Eliminar um certificado existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Obter a lista de certificados |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Adicionar um novo certificado ou atualizar um já existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/Delete | Eliminar um AppServiceCertificate existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/Read | Obter a lista de CertificateOrders |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Volte a emitir uma existente certificateorder |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Renovar um existente certificateorder |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Reenviar correio eletrónico de certificado |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Reenviar pedido e-mails para outro endereço de e-mail |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Obter selar de site para um certificado de serviço de aplicações emitido |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Obter a lista de ações de certificado |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Obter histórico de e-mails de certificado |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Verificar a propriedade do domínio |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/Write | Adicionar um novo certificateOrder ou atualizar um já existente |
> | Ação | Microsoft.CertificateRegistration/operations/Read | Listar todas as operações de registo de certificados do serviço de aplicações |
> | Ação | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Principal de serviço de aprovisionamento para o principal da aplicação de serviço |
> | Ação | Microsoft.CertificateRegistration/register/action | Registar o fornecedor de recursos do Microsoft Certificates para a subscrição |
> | Ação | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Validar o objeto de compra do certificado sem enviá-lo |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ClassicCompute/capabilities/read | Mostra as capacidades |
> | Ação | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Verifica a disponibilidade de um nome de domínio fornecido. |
> | Ação | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Verifica a disponibilidade de um nome de domínio específico. |
> | Ação | Microsoft.ClassicCompute/domainNames/active/write | Define o nome do domínio ativo. |
> | Ação | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Mostra o conjunto de disponibilidade do recurso. |
> | Ação | Microsoft.ClassicCompute/domainNames/capabilities/read | Mostra as funções de nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/delete | Remove os nomes de domínio dos recursos. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Mostra os blocos de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Obter a função no bloco de implementação do nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Obter a instância de função no bloco de implementação do nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Obter o estado do bloco de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Adicionar o estado do bloco de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Obter o domínio de atualização para o bloco de implementação no nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Atualizar domínio de atualização para o bloco de implementação no nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Cria ou atualiza a implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/delete | Remova as extensões do nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Lê o estado da operação das extensões de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/read | Devolve as extensões do nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/write | Adicione as extensões do nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Remove um novo balanceador de carga interno. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Lê o estado da operação dos balanceadores de carga internos de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Obtém os balanceadores de carga internos. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Cria um novo balanceador de carga interno. |
> | Ação | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Lê o estado da operação dos conjuntos de pontos finais com balanceamento de carga de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Obter os conjuntos de pontos finais com balanceamento de carga. |
> | Ação | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Adicionar o conjunto de pontos finais com balanceamento de carga. |
> | Ação | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Obter o estado da operação do nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Lê o estado da operação das extensões de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/read | Devolve os nomes de domínio dos recursos. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Elimina os certificados de serviço utilizados. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Lê o estado da operação dos certificados de serviço de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Devolve os certificados de serviço utilizados. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Adiciona ou modifica os certificados de serviço utilizados. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Aborta a migração de um bloco de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Consolida a migração de um bloco de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/delete | Elimina um bloco de implementação fornecido. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Lê o estado da operação das ranhuras de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Prepara a migração de um bloco de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/read | Mostra os blocos de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Remova a referência da extensão para a função de bloco de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Lê o estado da operação das referências de extensão de funções de ranhuras de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Devolve a referência da extensão para a função de bloco de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Adicionar ou modificar a referência da extensão para a função de bloco de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Obter a definição de métrica de função para o nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Obter métrica de função para o nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Obter o estado de operação para a função de bloco de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Obtém as definições de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Adiciona ou modifica as definições de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Obtém as definições das métricas. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/read | Obtém a função do bloco de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Transfere o ficheiro de ligação de ambiente de trabalho remoto para a instância de função na função de bloco de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Obtém o estado de operação para a instância de função na função de bloco de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Obtém a instância de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Recria a instância de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Recria a imagem da instância de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Reinicia instâncias de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Obter o SKU de função para o bloco de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/write | Adicionar função para bloco de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/start/action | Inicia um bloco de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Altera o estado do bloco de implementação para parado. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Altera o estado do bloco de implementação para iniciado. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/stop/action | Suspende o bloco de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Percorre a atualização do domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Valida a migração de um bloco de implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/write | Cria ou atualiza a implementação. |
> | Ação | Microsoft.ClassicCompute/domainNames/swap/action | Troca o bloco de teste pelo bloco de produção. |
> | Ação | Microsoft.ClassicCompute/domainNames/write | Adiciona ou modifica os nomes de domínio dos recursos. |
> | Ação | Microsoft.ClassicCompute/moveSubscriptionResources/action | Mover todos os recursos clássicos para uma subscrição diferente. |
> | Ação | Microsoft.ClassicCompute/operatingSystemFamilies/read | Lista as famílias de sistemas operativos convidadas disponíveis no Microsoft Azure, e lista as versões de sistema operativo disponíveis para cada família. |
> | Ação | Microsoft.ClassicCompute/operatingSystems/read | Lista as versões dos sistemas operativos convidados que estão, de momento, disponíveis no Microsoft Azure. |
> | Ação | Microsoft.ClassicCompute/operations/read | Obtém a lista de operações. |
> | Ação | Microsoft.ClassicCompute/operationStatuses/read | Lê o estado da operação do recurso. |
> | Ação | Microsoft.ClassicCompute/quotas/read | Obtém a quota da subscrição. |
> | Ação | Microsoft.ClassicCompute/register/action | Registar na Computação Clássica |
> | Ação | Microsoft.ClassicCompute/resourceTypes/skus/read | Obtém a lista de SKUs para tipos de recurso suportados. |
> | Ação | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Valide a disponibilidade da subscrição para operação de movimentação clássica. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Elimina o grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Lê o estado da operação dos grupos de segurança de rede associados a máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Obtém o grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Obtém as operações assíncronas possíveis |
> | Ação | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Anexa um disco de dados a uma máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/capture/action | Capturar uma máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/delete | Remove máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Desanexa um disco de dados da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Obter as definições de diagnóstico de máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/disks/read | Obtém a lista de discos de dados |
> | Ação | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Transfere o ficheiro RDP da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Lê o estado da operação das extensões de máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/extensions/read | Obtém a extensão da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/extensions/write | Coloca a extensão da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Obter a definição de métrica da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/metrics/read | Obtém as métricas. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Elimina o grupo de segurança de rede associado à interface de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Lê o estado da operação dos grupos de segurança de rede associados a máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Obtém o grupo de segurança de rede associado à interface de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à interface de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Lê o estado da operação das máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Executa a manutenção na máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Obtém as definições de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Adiciona ou modifica as definições de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Obtém as definições das métricas. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/read | Obtém a lista de máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Reimplementa a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/restart/action | Reinicia máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Encerra a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/start/action | Inicia a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/stop/action | Para a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/write | Adiciona ou modifica máquinas virtuais. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Obter um estado de operação de ligação cruzada do express route. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Eliminar o peering de ligação cruzada do express route. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Obter um estado de operação de peering de ligação cruzada do express route. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Obter peering de ligação cruzada do express route. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Adicionar peering de ligação cruzada do Express Route. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Obter ligações cruzadas do express route. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Adicionar ligações cruzadas do Express Route. |
> | Ação | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Obtém a lista de dispositivos suportados. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Elimina o grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Lê o estado da operação do grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Obtém as Definições de Diagnóstico dos Grupos de Segurança de Rede |
> | Ação | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza os grupos de segurança de rede das definições de diagnóstico, esta operação é complementada pelo fornecedor de recursos de informações. |
> | Ação | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Obtém os eventos do grupo de segurança de rede |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/read | Obtém o grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Elimina a regra de segurança. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Lê o estado da operação das regras de segurança do grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Obtém a regra de segurança. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Adiciona ou atualiza uma regra de segurança. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/write | Adiciona um novo grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/operations/read | Obter operações de rede clássica. |
> | Ação | Microsoft.ClassicNetwork/quotas/read | Obtém a quota da subscrição. |
> | Ação | Microsoft.ClassicNetwork/register/action | Registar na Rede Clássica |
> | Ação | Microsoft.ClassicNetwork/reservedIps/delete | Elimina um IP reservado. |
> | Ação | Microsoft.ClassicNetwork/reservedIps/join/action | Associar um IP reservado |
> | Ação | Microsoft.ClassicNetwork/reservedIps/link/action | Ligar um IP reservado |
> | Ação | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Lê o estado da operação dos IPs reservados. |
> | Ação | Microsoft.ClassicNetwork/reservedIps/read | Obtém os IPs reservados |
> | Ação | Microsoft.ClassicNetwork/reservedIps/write | Adiciona um novo IP reservado |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Aborta a migração de uma Rede Virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Mostra as capacidades |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Verifica a disponibilidade de um determinado endereço IP numa rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Consolida a migração de uma Rede Virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/delete | Elimina a rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Anula a revogação de um certificado de cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Lê os certificados de cliente revogados. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Revoga um certificado de cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Elimina o certificado de cliente do gateway da rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Transfere o certificado por thumbprint. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Lista o pacote de certificado do gateway da rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Localiza os certificados de raiz de cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Carrega um novo certificado de raiz de cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Estabelece uma ligação de gateway site para site. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Desliga uma ligação de gateway site para site. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Obtém a lista de ligações. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Testa uma ligação de gateway site para site. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Elimina o gateway da rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Transfere o script de configuração do dispositivo. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Transfere o diagnóstico do gateway. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Obtém a chave do serviço de circuito. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Lista o pacote do gateway da rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Lê o estado da operação dos gateways de redes virtuais. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Obtém o pacote do gateway da rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Obtém os gateways da rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Inicia o diagnóstico para o gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Interrompe o diagnóstico para o gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Adiciona um gateway da rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/join/action | Associa à rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Lê o estado da operação das redes virtuais. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Elementos de uma rede virtual com outra rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Prepara a migração de uma Rede Virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/read | Obtém a rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Elimina o proxy de peering da rede virtual remota. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Obtém o proxy de peering da rede virtual remota. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Adiciona ou atualiza o proxy de peering da rede virtual remota. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Elimina o grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Lê o estado da operação para o grupo de segurança de rede associado de sub-rede de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Obtém o grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Valida a migração de uma Rede Virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Obtém o peering da rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/write | Adiciona uma nova rede virtual. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ClassicStorage/capabilities/read | Mostra as capacidades |
> | Ação | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Verifica a disponibilidade de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Obter a disponibilidade de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/disks/read | Devolve o disco de conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/images/operationstatuses/read | Obtém o Estado de Operação de Imagem. |
> | Ação | Microsoft.ClassicStorage/images/read | Devolve a imagem. |
> | Ação | Microsoft.ClassicStorage/operations/read | Obtém operações de armazenamento clássico |
> | Ação | Microsoft.ClassicStorage/osImages/read | Devolve a imagem de sistema operativo. |
> | Ação | Microsoft.ClassicStorage/osPlatformImages/read | Obtém a imagem de plataforma de sistema operativo. |
> | Ação | Microsoft.ClassicStorage/publicImages/read | Obtém a imagem pública da máquina virtual. |
> | Ação | Microsoft.ClassicStorage/quotas/read | Obtém a quota da subscrição. |
> | Ação | Microsoft.ClassicStorage/register/action | Registar para Armazenamento Clássico |
> | Ação | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Aborta a migração de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Consolida a migração de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/delete | Elimina a conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/delete | Elimina um determinado disco de conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Lê o estado da operação do recurso. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/read | Devolve o disco de conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/write | Adiciona um disco de conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/images/delete | Elimina uma imagem de conta de armazenamento fornecida. (Preterido. Use 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Ação | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Devolve o estado de operação de imagem de conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/images/read | Devolve a imagem de conta de armazenamento. (Preterido. Use 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Ação | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Lê o estado da operação do recurso. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Elimina uma imagem do sistema operativo de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/osImages/read | Devolve a imagem do sistema operativo da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/osImages/write | Adiciona uma determinada imagem de sistema operativo de conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Prepara a migração de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/read | Devolve a conta de armazenamento com a conta fornecida. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Volta a gerar as chaves de acesso existentes para a conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Obtém as definições de diagnóstico. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Adiciona ou modifica as definições de diagnóstico. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Obtém as definições das métricas. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Obtém as métricas. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/read | Obtém os serviços disponíveis. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Valida a migração de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Elimina uma imagem da máquina virtual especificada. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Obtém um estado da operação de imagem numa máquina virtual especificada. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Devolve a imagem da máquina virtual. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Adiciona uma imagem da máquina virtual especificada. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/write | Adiciona uma nova conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/vmImages/read | Lista imagens da máquina virtual. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/analyze/action | Esta operação extrai um conjunto avançado de recursos visuais com base no conteúdo de imagem.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | Esta operação devolve uma caixa delimitadora em torno da área mais importante da imagem. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | Esta operação gera uma descrição de uma imagem na linguagem legível humana com sentenças completas.<br> A descrição baseia-se uma coleção de marcas de conteúdo, que também são devolvidas pela operação.<br>Pode ser gerada mais do que uma descrição para cada imagem.<br> As descrições são ordenadas pelo respetiva pontuação de confiança.<br>Todas as descrições são em inglês. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | Esta operação Performs deteção de objetos na imagem especificada.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | Esta operação gera uma imagem em miniatura com a largura especificadas pelo utilizador e a altura.<br> Por predefinição, o serviço analisa a imagem, identifica a região de interesse (ROI) e gera as coordenadas de recorte inteligentes com base no ROI.<br> Corte inteligente ajuda quando especifica uma proporção de aspecto que é diferente do que a imagem de entrada |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Esta operação reconhece o conteúdo dentro de uma imagem ao aplicar um modelo de domínio específico.<br> A lista de modelos de domínios específicos que são suportados pela API de visão do computador pode ser obtida usando a solicitação GET /models.<br> Atualmente, a API fornece modelos de domínios específicos a seguir: celebridades, pontos de referência. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | Esta operação devolve a lista de modelos de domínios específicos que são suportados pela API de visão do computador.  Atualmente, a API suporta modelos de domínios específicos a seguir: reconhecedor celebridade, reconhecedor de ponto de referência. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | Reconhecimento Ótico de carateres (OCR) Deteta texto numa imagem e extrai os carateres reconhecidos para um fluxo de carateres máquina utilizável.    |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | Use essa interface para obter o resultado de uma operação de reconhecer texto. Quando utiliza a interface de reconhecer texto, a resposta contém um campo chamado "Localização da operação". O campo de "Localização da operação" contém o URL que tem de utilizar para a operação obter Resultado da operação texto reconhecer. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | Esta operação gera uma lista de palavras ou etiquetas, o que são relevantes para o conteúdo da imagem fornecido.<br>A API de imagem digitalizada pode devolver etiquetas com base nos objetos, está a viver seres, paisagens ou ações encontradas nas imagens.<br>Ao contrário de categorias, as etiquetas não estão organizadas de acordo com um sistema de classificação hierárquica, mas correspondem ao conteúdo da imagem.<br>As etiquetas podem conter sugestões para evitar ambiguidade ou fornecer contexto, por exemplo violoncelo"tag" pode ser acompanhado a sugestão "instrumento musical".<br>Todas as etiquetas são em inglês. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | Essa interface é utilizada para apresentar a reconhecer o resultado da operação de texto. O URL para essa interface deve ser obtido a partir <b>"Localização da operação"</b> campo devolvido da interface de reconhecer texto. |
> | Ação | Microsoft.CognitiveServices/accounts/delete | Elimina contas da API |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/detect/action | Detete rostos humanos numa imagem, retorno retângulos e, opcionalmente, com faceIds, pontos de referência e atributos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | Elimine uma lista de rostos especificado. As imagens de face relacionados na lista de face serão eliminadas, demasiado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Elimine um rosto de uma lista de face ao faceListId especificado e persisitedFaceId. A imagem da face relacionados será eliminada, demasiado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Adicione um face a uma lista de rostos especificado, até 1000 rostos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Obter uma lista de face faceListId, nome, userData e rostos na lista de rostos.
Listar listas de rostos faceListId, nome e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Crie uma lista vazia de rostos com faceListId especificado pelo utilizador, nome e uma userData opcional. Até 64 rostos listas são permitidas
Atualize as informações de uma lista de rostos, incluindo o nome e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | Dado o face ID do rosto de consulta, para procurar os rostos de aspeto semelhante de uma matriz de face ID, uma lista de face ou uma lista de face grandes. faceId |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/group/action | Divida o candidato rostos em grupos com base na semelhança de rostos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/identify/action | 1-para-muitos identificação para encontrar as correspondências mais próximas do Mostrador de pessoa da consulta específica a partir de um grupo de pessoas ou o grupo de pessoas grandes. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Elimine uma lista de face de grandes especificado. As imagens de face relacionados na lista de face de grandes dimensões serão eliminadas, demasiado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | Elimine um rosto de uma lista de grandes face ao largeFaceListId especificado e persisitedFaceId. A imagem da face relacionados será eliminada, demasiado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Obter rostos persistentes na lista de grandes face ao largeFaceListId e persistedFaceId.
Lista dos rostos persistedFaceId e userData numa lista de face de grandes especificado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | Adicione um face a uma lista de face de grandes especificado, até 1 000 000 rostos.
Atualize o campo de userData de um rosto especificado numa lista grande face ao seu persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Obter largeFaceListId de uma lista de face de grandes dimensões, o nome, userData.
Liste informações de listas de rostos grandes de largeFaceListId, nome e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | Submeta uma tarefa de treinamento de lista de face de grandes dimensões. O treinamento é um passo crucial que apenas uma lista de face de grandes treinado que pode ser utilizada por |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | Para verificar o estado de treinamento de lista face grandes conclusão ou ainda em curso. Treinamento de LargeFaceList é uma operação assíncrona |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Crie uma lista vazia face grandes com largeFaceListId especificado pelo utilizador, nome e uma userData opcional.
Atualize as informações de uma lista de face de grandes dimensões, incluindo o nome e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | Elimine um grupo de pessoas grandes existente com personGroupId especificado. Dados persistentes neste grupo de pessoa grandes serão eliminados. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | Crie uma nova pessoa num grupo de pessoas grandes especificado. Para adicionar face a esta pessoa, chamar |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | Elimine uma pessoa existente de um grupo de pessoa grandes. Todos armazenados dados person e imagens de rosto na entrada de pessoa vão ser eliminadas. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Elimine um rosto de uma pessoa num grupo de pessoas grandes. Dados de rostos e relacionados com esta entrada de face de imagem também serão eliminados. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Obter as informações de face da pessoa. O mostrador da pessoa persistente é especificado pela respetiva largePersonGroupId, personId e persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | Adicione uma imagem da face a uma pessoa num grupo de pessoas grandes para identificação facial ou de verificação. Para lidar com a imagem de
Atualização de uma pessoa persistentes campo de userData do rosto. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Obtenha o nome de uma pessoa e userData e o faceIds persistente que representa a imagem de face da pessoa registada.
Liste informações de todas as pessoas no grupo especificado pessoa grandes, incluindo personId, nome, userData e persistedFaceIds. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | Nome da atualização ou userData de uma pessoa. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | Obter as informações de um grupo de pessoas grandes, incluindo o respetivo nome e userData. Esta API devolve pessoa grandes informações de grupo
Liste do todos os existentes grandes grupos de pessoas largePesonGroupId, nome e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | Submeta uma tarefa de treinamento do grupo de pessoa grandes. O treinamento é um passo crucial que pode ser usado apenas um grupo de pessoas de grandes preparado |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | Para verificar o estado de treinamento para grupo de pessoa grandes conclusão ou ainda em curso. Treinamento de LargePersonGroup é uma operação assíncrona |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | Crie um novo grupo de pessoa grandes com largePersonGroupId especificado pelo utilizador, nome e userData opcional.
Atualize o nome de um grupo grande de pessoa existente e userData. As propriedades mantenha inalteradas, se não estiverem no corpo do pedido. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | Elimine um grupo de pessoas existente com personGroupId especificado. Dados persistentes neste grupo de pessoas vão ser eliminados. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | Crie uma nova pessoa num grupo de pessoas especificado. Para adicionar face a esta pessoa, chamar |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | Elimine uma pessoa existente de um grupo de pessoas. Todos armazenados dados person e imagens de rosto na entrada de pessoa vão ser eliminadas. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | Elimine um rosto de uma pessoa num grupo de pessoas. Dados de rostos e relacionados com esta entrada de face de imagem também serão eliminados. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | Obter as informações de face da pessoa. O mostrador da pessoa persistente é especificado pela respetiva personGroupId, personId e persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | Adicione uma imagem da face a uma pessoa num grupo de pessoas para identificação facial ou de verificação. Para lidar com a imagem de vários
Atualização de uma pessoa persistentes campo de userData do rosto. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | Obtenha o nome de uma pessoa e userData e o faceIds persistente que representa a imagem de face da pessoa registada.
Listar informações de todas as pessoas no grupo de pessoa específica, incluindo personId, nome, userData e persistedFaceIds de registado. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | Nome da atualização ou userData de uma pessoa. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/read | Obter o nome do grupo de pessoa e userData. Para obter informações de person sob este personGroup, utilize
Listar pesonGroupId, o nome e o userData de grupos de pessoas. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | Submeta uma tarefa de treinamento do grupo de pessoas. O treinamento é um passo crucial que apenas um grupo de pessoas com formação pode ser utilizado por |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | Para verificar o estado de treinamento para grupo de pessoa conclusão ou ainda em curso. Treinamento de PersonGroup é uma operação assíncrona acionada |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/write | Crie um novo grupo de pessoas com personGroupId especificado, o nome e userData fornecidos pelo usuário.
Atualize o nome de um grupo pessoa existente e userData. As propriedades mantenha inalteradas, se não estiverem no corpo do pedido. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/verify/action | Certifique-se de que se dois rostos pertencerem a uma mesma pessoa ou se um rosto pertence a uma pessoa. |
> | Ação | Microsoft.CognitiveServices/accounts/listKeys/action | Listar Chaves |
> | DataAction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Obtém a predição de ponto de extremidade publicados para a consulta indicada. |
> | Ação | Microsoft.CognitiveServices/accounts/read | Lê contas da API. |
> | Ação | Microsoft.CognitiveServices/accounts/regenerateKey/action | Regenerar Chave |
> | Ação | Microsoft.CognitiveServices/accounts/skus/read | Lê os SKUs disponíveis para um recurso existente. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | A API devolve uma lista de entidades conhecidas e geral com o nome entidades (\"pessoa\", \"localização\", \"organização\" etc) num determinado documento. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | A API devolve uma lista de cadeias que denotam os principais pontos de conversa no texto introduzido. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | A API devolve o idioma detetado e uma pontuação numérica entre 0 e 1. As pontuações próximas de 1 indicam uma certeza de 100% de que o idioma detetado é verdadeiro. É suportado um total de 120 idiomas. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | A API devolve uma pontuação numérica entre 0 e 1.<br>As pontuações próximas de 1 indicam um sentimento positivo, enquanto as pontuações próximas de 0 indicam um sentimento negativo.<br>Uma pontuação de 0,5 indica a falta de sensibilidade (por exemplo, uma instrução de factoid). |
> | Ação | Microsoft.CognitiveServices/accounts/usages/read | Obter a utilização de quota para um recurso existente. |
> | Ação | Microsoft.CognitiveServices/accounts/write | Escreve Contas da API. |
> | Ação | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Lê SKUs disponíveis para uma subscrição. |
> | Ação | Microsoft.CognitiveServices/Operations/read | Listar todas as operações disponíveis |
> | Ação | Microsoft.CognitiveServices/register/action | Regista a Subscrição nos Serviços Cognitivos |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Commerce/RateCard/read | Devolve oferece dados, os metadados de recurso/medidor e taxas para a subscrição fornecida. |
> | Ação | Microsoft.Commerce/UsageAggregates/read | Obtém o consumo do Microsoft Azure por uma subscrição. O resultado contém agrega dados de utilização, recursos e subscrição informações relacionadas com o, num intervalo de tempo específico. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Compute/availabilitySets/delete | Elimina o conjunto de disponibilidade |
> | Ação | Microsoft.Compute/availabilitySets/read | Obter as propriedades de um conjunto de disponibilidade |
> | Ação | Microsoft.Compute/availabilitySets/vmSizes/read | Listar tamanhos disponíveis para criar ou atualizar uma máquina virtual no conjunto de disponibilidade |
> | Ação | Microsoft.Compute/availabilitySets/write | Cria um novo conjunto de disponibilidade ou atualiza um já existente |
> | Ação | Microsoft.Compute/disks/beginGetAccess/action | Obter o URI de SAS do Disco para acesso do blob |
> | Ação | Microsoft.Compute/disks/delete | Elimina o Disco |
> | Ação | Microsoft.Compute/disks/endGetAccess/action | Revogar o URI de SAS do Disco |
> | Ação | Microsoft.Compute/disks/read | Obter as propriedades de um Disco |
> | Ação | Microsoft.Compute/disks/write | Cria um novo Disco ou atualiza um já existente |
> | Ação | Microsoft.Compute/galleries/delete | Elimina a Galeria |
> | Ação | Microsoft.Compute/galleries/images/delete | Elimina a Imagem de Galeria |
> | Ação | Microsoft.Compute/galleries/images/read | Obtém as propriedades da Imagem de Galeria |
> | Ação | Microsoft.Compute/galleries/images/versions/delete | Elimina a Versão da Imagem de Galeria |
> | Ação | Microsoft.Compute/galleries/images/versions/read | Obtém as propriedades da Versão da Imagem de Galeria |
> | Ação | Microsoft.Compute/galleries/images/versions/write | Cria uma nova Versão de Imagem de Galeria ou atualiza uma existente |
> | Ação | Microsoft.Compute/galleries/images/write | Cria uma nova Imagem de Galeria ou atualiza uma existente |
> | Ação | Microsoft.Compute/galleries/read | Obtém as propriedades da Galeria |
> | Ação | Microsoft.Compute/galleries/write | Cria uma nova Galeria ou atualiza uma existente |
> | Ação | Microsoft.Compute/images/delete | Elimina a imagem |
> | Ação | Microsoft.Compute/images/read | Obter as propriedades da Imagem |
> | Ação | Microsoft.Compute/images/write | Cria uma nova Imagem ou atualiza uma existente |
> | Ação | Microsoft.Compute/locations/capsOperations/read | Obtém o estado de uma operação de Limite assíncrona |
> | Ação | Microsoft.Compute/locations/diskOperations/read | Obtém o estado de uma operação assíncrona do disco |
> | Ação | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Crie registos para mostrar o número total de pedidos por intervalo de tempo de modo a promover a limitação de diagnósticos. |
> | Ação | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Crie registos para mostrar agregados de pedidos limitados agrupados por NomeDoRecurso, NomeDaOperação ou pela Política de Limitação aplicada. |
> | Ação | Microsoft.Compute/locations/operations/read | Obtém o estado de uma operação assíncrona |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Obter as propriedades de uma Oferta de Imagem da Plataforma |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Obter as propriedades de uma SKU da Imagem de Plataforma |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Obter as propriedades de uma Versão da Imagem de Plataforma |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Obter as propriedades de um Tipo de VMExtension |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Obter as propriedades de uma Versão do VMExtension |
> | Ação | Microsoft.Compute/locations/publishers/read | Obter as propriedades de um Publicador |
> | Ação | Microsoft.Compute/locations/runCommands/read | Lista os comandos de execução disponíveis na localização |
> | Ação | Microsoft.Compute/locations/usages/read | Obtém os limites de serviço e as quantidades de utilização atuais dos recursos de computação da subscrição numa localização |
> | Ação | Microsoft.Compute/locations/vmSizes/read | Lista os tamanhos de máquina virtual disponíveis numa localização |
> | Ação | Microsoft.Compute/operations/read | Lista as operações disponíveis no fornecedor de recursos Microsoft.Compute |
> | Ação | Microsoft.Compute/register/action | Regista a Subscrição no fornecedor de recursos Microsoft.Compute |
> | Ação | Microsoft.Compute/restorePointCollections/delete | Elimina a coleção de ponto de restauro e pontos de restauro contidos |
> | Ação | Microsoft.Compute/restorePointCollections/read | Obter as propriedades de uma coleção de ponto de restauro |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/delete | Elimina o ponto de restauro |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/read | Obter as propriedades de um ponto de restauro |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Obter as propriedades de um ponto de restauro juntamente com os URIs de SAS do blob |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/write | Cria um novo ponto de restauro |
> | Ação | Microsoft.Compute/restorePointCollections/write | Cria uma nova coleção de ponto de restauro ou atualiza uma existente |
> | Ação | Microsoft.Compute/sharedVMImages/delete | Elimina o SharedVMImage |
> | Ação | Microsoft.Compute/sharedVMImages/read | Obter as propriedades de um SharedVMImage |
> | Ação | Microsoft.Compute/sharedVMImages/versions/delete | Eliminar um SharedVMImageVersion |
> | Ação | Microsoft.Compute/sharedVMImages/versions/read | Obter as propriedades de um SharedVMImageVersion |
> | Ação | Microsoft.Compute/sharedVMImages/versions/replicate/action | Replicar uma SharedVMImageVersion para regiões de destino |
> | Ação | Microsoft.Compute/sharedVMImages/versions/write | Criar um novo SharedVMImageVersion ou atualizar um já existente |
> | Ação | Microsoft.Compute/sharedVMImages/write | Cria uma nova SharedVMImage ou atualiza uma existente |
> | Ação | Microsoft.Compute/skus/read | Obtém a lista das SKUs do Microsoft.Compute disponíveis para a sua Subscrição |
> | Ação | Microsoft.Compute/snapshots/beginGetAccess/action | Obter o URI de SAS do instantâneo para acesso do blob |
> | Ação | Microsoft.Compute/snapshots/delete | Eliminar um Instantâneo |
> | Ação | Microsoft.Compute/snapshots/endGetAccess/action | Revogar o URI de SAS do instantâneo |
> | Ação | Microsoft.Compute/snapshots/read | Obter as propriedades de um Instantâneo |
> | Ação | Microsoft.Compute/snapshots/write | Criar um novo Instantâneo ou atualizar um já existente |
> | Ação | Microsoft.Compute/virtualMachines/capture/action | Recolhe a máquina virtual, copiando os discos rígidos virtuais, e gera um modelo que pode ser utilizado para criar máquinas virtuais semelhantes |
> | Ação | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Converte os discos baseados em blob da máquina virtual em discos geridos |
> | Ação | Microsoft.Compute/virtualMachines/deallocate/action | Desliga a máquina virtual e liberta os recursos de computação |
> | Ação | Microsoft.Compute/virtualMachines/delete | Elimina a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/extensions/delete | Elimina a extensão da máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/extensions/read | Obter as propriedades de uma extensão da máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/extensions/write | Cria uma nova extensão da máquina virtual ou atualiza uma já existente |
> | Ação | Microsoft.Compute/virtualMachines/generalize/action | Define o estado da máquina virtual como Generalizado e prepara-a para a captura |
> | Ação | Microsoft.Compute/virtualMachines/instanceView/read | Obtém o estado de runtime detalhado da máquina virtual e dos respetivos recursos |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Iniciar sessão numa máquina virtual como um utilizador normal |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Inicie sessão para uma máquina virtual com o administrador do Windows ou privilégios de utilizador de raiz do Linux |
> | Ação | Microsoft.Compute/virtualMachines/performMaintenance/action | Realiza a Operação de Manutenção na VM. |
> | Ação | Microsoft.Compute/virtualMachines/powerOff/action | Desliga a máquina virtual. Tenha em atenção que a máquina virtual irá continuar a ser faturados. |
> | Ação | Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/redeploy/action | Reimplementa a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/reimage/action | Recria a imagem da máquina virtual que está a utilizar o disco de diferenciação. |
> | Ação | Microsoft.Compute/virtualMachines/restart/action | Reinicia a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/runCommand/action | Executa um script predefinido na máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/start/action | Inicia a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/vmSizes/read | Lista os tamanhos disponíveis para os quais a máquina virtual pode ser atualizada |
> | Ação | Microsoft.Compute/virtualMachines/write | Cria uma nova máquina virtual ou atualiza uma máquina virtual existente |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Desliga e liberta os recursos de computação para as instâncias do Conjunto de Dimensionamento da Máquina Virtual  |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/delete | Elimina o Conjunto de Dimensionamento da Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/delete/action | Elimina as instâncias do Conjunto de Dimensionamento da Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Elimina a Extensão do Conjunto de Dimensionamento da Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Obtém as propriedades de uma Extensão do Conjunto de Dimensionamento da Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Cria uma nova Extensão do Conjunto de Dimensionamento da Máquina Virtual ou atualiza uma existente |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Percorrer manualmente os domínios de atualização da plataforma de um Conjunto de Dimensionamento de Máquinas Virtuais do Service Fabric para concluir uma atualização pendente que está parada |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Obtém a vista de instância do Conjunto de Dimensionamento da Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Atualiza manualmente as instâncias para o modelo mais recente do Conjunto de Dimensionamento da Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Obter as propriedades de todas as interfaces de rede de um Conjunto de Dimensionamento de Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Inicia uma atualização para mover todas as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais para a versão de SO de Imagem de Plataforma mais recente disponível. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Obtém o histórico de atualizações do SO de um Conjunto de Dimensionamento de Máquinas Virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Executa a manutenção planeada nas instâncias do Conjunto de Dimensionamento de Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Desliga as instâncias do Conjunto de Dimensionamento da Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Obter as propriedade de todos os endereços IP públicos de um Conjunto de Dimensionamento de Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/read | Obter as propriedades de um Conjunto de Dimensionamento da Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Implementar novamente as instâncias do Conjunto de Dimensionamento de Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Recria imagens de instâncias do Conjunto de Dimensionamento da Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Recria a imagem de todos os discos (Discos de SO e Discos de Dados) para as instâncias de um Conjunto de Dimensionamento de Máquinas Virtuais  |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/restart/action | Reinicia as instâncias do Conjunto de Dimensionamento da Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Cancela a atualização gradual de um Conjunto de Dimensionamento da Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Obtenha o estado mais recente da Atualização Gradual para um Conjunto de Dimensionamento da Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/scale/action | Verificar se um Conjunto de Dimensionamento da Máquina Virtual existente pode ser Reduzido/Aumentado Horizontalmente para a contagem de instâncias especificada |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/skus/read | Lista os SKUs válidos de um Conjunto de Dimensionamento da Máquina Virtual existente |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/start/action | Inicia as instâncias do Conjunto de Dimensionamento da Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Desativa e liberta os recursos de computação de uma Máquina Virtual num Conjunto de Dimensionamento da VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Eliminar uma Máquina Virtual específica num Conjunto de Dimensionamento da VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Obtém a vista de instância de uma Máquina Virtual num Conjunto de Dimensionamento da VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Obter propriedades de endereço IP público criado através do conjunto de dimensionamento de Máquina Virtual. O conjunto de dimensionamento de máquinas virtuais, pode criar no máximo, um IP público por ipconfiguration (IP privado) |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Obter propriedades de uma ou todas as configurações de IP de uma interface de rede criada através do conjunto de dimensionamento de Máquina Virtual. Configurações de IP representam IPs privados |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Obter as propriedades de uma ou todas as interfaces de rede de uma máquina virtual criada através do Conjunto de Dimensionamento da Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Executa a manutenção planeada numa instância de Máquina Virtual num Conjunto de Dimensionamento de Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Desativa uma instância de Máquina Virtual num Conjunto de Dimensionamento da VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Obtém as propriedades de uma Máquina Virtual num Conjunto de Dimensionamento da VM |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Implementa novamente uma instância de Máquina Virtual num Conjunto de Dimensionamento de Máquina Virtual |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Recria imagens de uma instância de Máquina Virtual num Conjunto de Dimensionamento de Máquina Virtual. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Recria a imagem de todos os discos (Discos de SO e Discos de Dados) para a instância de Máquina Virtual num Conjunto de Dimensionamento de Máquinas Virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Reinicia uma instância de Máquina Virtual num Conjunto de Dimensionamento da VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Executa um script predefinido numa instância de Máquina Virtual num Conjunto de Dimensionamento de Máquina Virtual. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Inicia uma instância de Máquina Virtual num Conjunto de Dimensionamento da VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Atualiza as propriedades de uma Máquina Virtual num Conjunto de Dimensionamento de VM |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/write | Cria um novo Conjunto de Dimensionamento da Máquina Virtual ou atualiza um já existente |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Consumption/balances/read | Liste a utilização de resumo para um período de faturação para um grupo de gestão. |
> | Ação | Microsoft.Consumption/budgets/delete | Elimine orçamentos por uma subscrição ou um grupo de gestão. |
> | Ação | Microsoft.Consumption/budgets/read | Liste os orçamentos por uma subscrição ou um grupo de gestão. |
> | Ação | Microsoft.Consumption/budgets/write | Cria e atualizar os orçamentos por uma subscrição ou um grupo de gestão. |
> | Ação | Microsoft.Consumption/charges/read | Custos de lista |
> | Ação | Microsoft.Consumption/credits/read | Créditos de lista |
> | Ação | Microsoft.Consumption/events/read | Listar os eventos |
> | Ação | Microsoft.Consumption/forecasts/read | Previsões de lista |
> | Ação | Microsoft.Consumption/lots/read | Muitos de lista |
> | Ação | Microsoft.Consumption/marketplaces/read | Liste os detalhes de utilização de recursos de mercado para um âmbito para subscrições EA e WebDirect. |
> | Ação | Microsoft.Consumption/operationresults/read | Lista operationresults |
> | Ação | Microsoft.Consumption/operations/read | Liste operações todos suportadas pelo fornecedor de recursos de Microsoft.Consumption. |
> | Ação | Microsoft.Consumption/operationstatus/read | Lista operationstatus |
> | Ação | Microsoft.Consumption/pricesheets/read | Liste os dados de Pricesheets para uma subscrição ou um grupo de gestão. |
> | Ação | Microsoft.Consumption/register/action | Registre-se para consumo RP |
> | Ação | Microsoft.Consumption/reservationDetails/read | Liste os detalhes de utilização de instâncias reservadas por grupos de gestão ou de ordem de reserva. Os dados de detalhes são por instância por nível do dia. |
> | Ação | Microsoft.Consumption/reservationRecommendations/read | Lista únicas ou partilhadas recomendações para as instâncias reservadas para uma subscrição. |
> | Ação | Microsoft.Consumption/reservationSummaries/read | Liste a utilização de resumo para as instâncias reservadas por grupos de gestão ou de ordem de reserva. Os dados de resumo estão no nível diário ou mensal. |
> | Ação | Microsoft.Consumption/reservationTransactions/read | Liste o histórico de transações para as instâncias reservadas por grupos de gestão. |
> | Ação | Microsoft.Consumption/tags/read | Listar etiquetas para subscrições e EA. |
> | Ação | Microsoft.Consumption/tenants/read | Inquilinos de lista |
> | Ação | Microsoft.Consumption/tenants/register/action | Registre-se a ação de âmbito do Microsoft.Consumption por um inquilino. |
> | Ação | Microsoft.Consumption/terms/read | Liste os termos de uma subscrição ou um grupo de gestão. |
> | Ação | Microsoft.Consumption/usageDetails/read | Liste os detalhes de utilização para um âmbito para subscrições EA e WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Exec para um contêiner específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Obtenha registos para um contentor específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/delete | Elimine o grupo de contentores específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição do diagnóstico para o grupo de contentores. |
> | Ação | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição de diagnóstico para o grupo de recursos. |
> | Ação | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para o grupo de contentores. |
> | Ação | Microsoft.ContainerInstance/containerGroups/read | Obter todos os grupos de contentores. |
> | Ação | Microsoft.ContainerInstance/containerGroups/restart/action | Reinicia um grupo de contentor específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/start/action | Inicia um grupo de contentores específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/stop/action | Deixa de um grupo de contentores específico. Serão possível desalocar a recursos e a faturação irá parar de computação. |
> | Ação | Microsoft.ContainerInstance/containerGroups/write | Crie ou atualize um grupo de contentores específico. |
> | Ação | Microsoft.ContainerInstance/locations/cachedImages/read | Obtém as imagens em cache para a subscrição numa região. |
> | Ação | Microsoft.ContainerInstance/locations/capabilities/read | Obtenha os recursos para uma região. |
> | Ação | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Notifica Microsoft.ContainerInstance que rede ou sub-rede virtual está a ser eliminado. |
> | Ação | Microsoft.ContainerInstance/locations/usages/read | Obter a utilização para uma região específica. |
> | Ação | Microsoft.ContainerInstance/register/action | Regista a subscrição do fornecedor de recursos de instância de contentor e ativa a criação de contas de contentores. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ContainerRegistry/checkNameAvailability/read | Verifica se o nome do container registry está disponível para utilização. |
> | Ação | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft. containerregistry que rede ou sub-rede virtual está a ser eliminada |
> | Ação | Microsoft.ContainerRegistry/locations/operationResults/read | Obtém um resultado da operação assíncrona |
> | Ação | Microsoft.ContainerRegistry/operations/read | Apresenta uma lista de todas as operações de API de REST de registo de contentor do Azure disponíveis |
> | Ação | Microsoft.ContainerRegistry/register/action | Regista a subscrição para o fornecedor de recursos de registo de contentor e permite a criação de registos de contentores. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/cancel/action | Cancela uma compilação existente. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Obtém uma ligação para transferir os registos de compilação. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/read | Obtém as propriedades da compilação especificada ou lista todas as compilações para o registo de contentor especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/write | Atualiza uma compilação para um registo de contentores com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/delete | Elimina uma tarefa de compilação de um registo de contentor. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Lista as propriedades de controle de origem para uma tarefa de compilação. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/read | Obtém as propriedades da tarefa de compilação especificado ou lista todas as tarefas de compilação para o registo de contentor especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Elimina um passo de compilação a partir de uma tarefa de compilação. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Apresenta os argumentos de compilação para um passo de compilação, incluindo os argumentos secretos. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Obtém as propriedades do passo de compilação especificado ou lista todos os passos de compilação para a tarefa de compilação especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Cria ou atualiza um passo de compilação para uma tarefa de compilação com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/write | Cria ou atualiza uma tarefa de compilação para um registo de contentores com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/delete | Elimina um registo de contentor. |
> | Ação | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Elimina um filtro de grelha de eventos a partir de um registo de contentor. |
> | Ação | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Obtém as propriedades do filtro de grelha de eventos especificado ou lista todos os filtros de grelha de eventos para o registo de contentor especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Cria ou atualiza um filtro de grelha de eventos para um registo de contentores com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Obtém a localização de carregamento para o usuário seja capaz de carregar a origem. |
> | Ação | Microsoft.ContainerRegistry/registries/importImage/action | Importe imagem para o registo de contentor com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Obter origem de carregar a localização de url para um registo de contentor. |
> | Ação | Microsoft.ContainerRegistry/registries/listCredentials/action | Lista as credenciais de início de sessão para o registo de contentor especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/listPolicies/read | Lista as políticas para o registo de contentor especificado |
> | Ação | Microsoft.ContainerRegistry/registries/listUsages/read | Lista as utilizações de quota para o registo de contentor especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/operationStatuses/read | Obtém o estado da operação de async um registo |
> | Ação | Microsoft.ContainerRegistry/registries/pull/read | Solicitar ou obter imagens a partir de um registo de contentor. |
> | Ação | Microsoft.ContainerRegistry/registries/push/write | Push ou escrever as imagens para um registo de contentor. |
> | Ação | Microsoft.ContainerRegistry/registries/quarantineRead/read | Solicitar ou obter imagens em quarentena do container registry |
> | Ação | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Escrita/modificar o estado de quarentena de imagens em quarentena |
> | Ação | Microsoft.ContainerRegistry/registries/queueBuild/action | Cria uma nova compilação com base nos parâmetros de pedido e adicioná-lo para a fila de compilação. |
> | Ação | Microsoft.ContainerRegistry/registries/read | Obtém as propriedades de registo de contentor especificado ou apresenta uma lista de todos os registos de contentores sob a subscrição ou grupo de recursos especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Regenera um das credenciais de início de sessão para o registo de contentor especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/replications/delete | Elimina uma replicação de um registo de contentor. |
> | Ação | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Obtém um Estado de operação de async de replicação |
> | Ação | Microsoft.ContainerRegistry/registries/replications/read | Obtém as propriedades da replicação especificada ou lista todas as replicações para o registo de contentor especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/replications/write | Cria ou atualiza uma replicação para um registo de contentores com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/runs/cancel/action | Cancele uma execução existente. |
> | Ação | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Obtém o registo de URL de SAS para uma execução. |
> | Ação | Microsoft.ContainerRegistry/registries/runs/read | Obtém as propriedades de uma execução de um registo de contentor ou lista de execuções. |
> | Ação | Microsoft.ContainerRegistry/registries/runs/write | Atualiza uma execução. |
> | Ação | Microsoft.ContainerRegistry/registries/scheduleRun/action | Agende uma execução de um registo de contentor. |
> | Ação | Microsoft.ContainerRegistry/registries/sign/write | Push/Pull metadados de confiança de conteúdo para um registo de contentor. |
> | Ação | Microsoft.ContainerRegistry/registries/tasks/delete | Elimina uma tarefa para um registo de contentor. |
> | Ação | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Liste todos os detalhes de uma tarefa para um registo de contentor. |
> | Ação | Microsoft.ContainerRegistry/registries/tasks/read | Obtém uma tarefa para um registo de contentor ou uma lista de todas as tarefas. |
> | Ação | Microsoft.ContainerRegistry/registries/tasks/write | Cria ou atualiza uma tarefa para um registo de contentor. |
> | Ação | Microsoft.ContainerRegistry/registries/updatePolicies/write | Atualiza as políticas para o registo de contentor especificado |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/delete | Elimina um webhook a partir de um registo de contentor. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Obtém a configuração de URI de serviço e os cabeçalhos personalizados para o webhook. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Apresenta uma lista de eventos recentes do webhook especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Obtém o estado da operação de async um webhook |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Aciona um evento de ping para serem enviados para o webhook. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/read | Obtém as propriedades do webhook especificado ou lista todos os webhooks para o registo de contentor especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/write | Cria ou atualiza um webhook para um registo de contentores com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/write | Cria ou atualiza um registo de contentores com os parâmetros especificados. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ContainerService/containerServices/delete | Elimina um serviço de contentor |
> | Ação | Microsoft.ContainerService/containerServices/read | Obter um serviço de contentor |
> | Ação | Microsoft.ContainerService/containerServices/write | Cria um novo serviço de contentor ou atualiza um existente |
> | Ação | Microsoft.ContainerService/locations/operationresults/read | Obtém o estado de um resultado de operação assíncrona |
> | Ação | Microsoft.ContainerService/locations/operations/read | Obtém o estado de uma operação assíncrona |
> | Ação | Microsoft.ContainerService/locations/orchestrators/read | Lista os orquestradores suportados |
> | Ação | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Obter um perfil de acesso de cluster gerido por nome de função com a credencial da lista |
> | Ação | Microsoft.ContainerService/managedClusters/accessProfiles/read | Obter um perfil de acesso de cluster gerido por nome de função |
> | Ação | Microsoft.ContainerService/managedClusters/delete | Elimina um cluster gerido |
> | Ação | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Listar a credencial clusterAdmin de um cluster gerido |
> | Ação | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Listar a credencial clusterUser de um cluster gerido |
> | Ação | Microsoft.ContainerService/managedClusters/read | Obter um cluster gerido |
> | Ação | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Repor o perfil do AAD de um cluster gerido |
> | Ação | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Repor o perfil de principal de serviço de um cluster gerido |
> | Ação | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | Obtém o perfil de atualização do cluster |
> | Ação | Microsoft.ContainerService/managedClusters/write | Cria um novo cluster gerido ou atualiza um existente |
> | Ação | Microsoft.ContainerService/openShiftClusters/delete | Eliminar um Cluster de Open Shift |
> | Ação | Microsoft.ContainerService/openShiftClusters/read | Um cluster de Open Shift |
> | Ação | Microsoft.ContainerService/openShiftClusters/write | Cria um novo Cluster de Shift aberto ou atualiza um já existente |
> | Ação | Microsoft.ContainerService/openShiftManagedClusters/delete | Eliminar um Cluster gerido Open Shift |
> | Ação | Microsoft.ContainerService/openShiftManagedClusters/read | Obter um Cluster gerido Open Shift |
> | Ação | Microsoft.ContainerService/openShiftManagedClusters/write | Cria um novo Open Shift geridos Cluster ou atualiza um já existente |
> | Ação | Microsoft.ContainerService/operations/read | Lista as operações disponíveis no fornecedor de recursos Microsoft.ContainerService |
> | Ação | Microsoft.ContainerService/register/action | Regista a Subscrição no fornecedor de recursos Microsoft.ContainerService |
> | Ação | Microsoft.ContainerService/unregister/action | Anula a Subscrição com o fornecedor de recursos Microsoft.ContainerService |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ContentModerator/applications/delete | Operação de Eliminação |
> | Ação | Microsoft.ContentModerator/applications/listSecrets/action | Listar Segredos |
> | Ação | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Ler os Tokens de início de sessão único |
> | Ação | Microsoft.ContentModerator/applications/read | Operação de Leitura |
> | Ação | Microsoft.ContentModerator/applications/write | Operação de Escrita |
> | Ação | Microsoft.ContentModerator/applications/write | Operação de Escrita |
> | Ação | Microsoft.ContentModerator/listCommunicationPreference/action | Listar preferência de comunicação |
> | Ação | Microsoft.ContentModerator/operations/read | operações de leitura |
> | Ação | Microsoft.ContentModerator/updateCommunicationPreference/action | Atualizar preferência de comunicação |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.CostManagement/dimensions/read | Liste dimensões todos suportadas por um âmbito. |
> | Ação | Microsoft.CostManagement/exports/action | Execute a exportação especificada. |
> | Ação | Microsoft.CostManagement/exports/delete | Elimine a exportação especificada. |
> | Ação | Microsoft.CostManagement/exports/read | Liste as exportações pelo âmbito de aplicação. |
> | Ação | Microsoft.CostManagement/exports/write | Criar ou atualizar a exportação especificada. |
> | Ação | Microsoft.CostManagement/query/action | Consultar dados de utilização por um âmbito. |
> | Ação | Microsoft.CostManagement/query/read | Consultar dados de utilização por um âmbito. |
> | Ação | Microsoft.CostManagement/reports/action | Agendar relatórios nos dados de utilização por um âmbito. |
> | Ação | Microsoft.CostManagement/reports/read | Agendar relatórios nos dados de utilização por um âmbito. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.CustomerInsights/hubs/adobemetadata/action | Criar ou atualizar quaisquer metadados de Adobe de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/adobemetadata/read | Ler quaisquer metadados de Adobe de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Eliminar qualquer política de assinatura de acesso partilhado do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Leia a que política de assinatura de acesso de partilhadas quaisquer informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Regenerar a chave primária da política de assinatura de acesso de partilhado informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Regenerar chave secundária da política de assinatura de acesso de partilhado informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Criar ou atualizar qualquer política de assinatura de acesso partilhado do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/activate/action | Ativar qualquer conector de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/activate/action | Ativar qualquer conector de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/delete | Eliminar qualquer conector de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Obter qualquer Estado de runtime do conector de informações de cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Ativar qualquer mapeamento de conector de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Eliminar qualquer mapeamento de conector de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Ler qualquer resultado da operação de mapeamento conector de informações de cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Ler qualquer mapeamento de conector de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Criar ou atualizar qualquer mapeamento de conector de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/operations/read | Ler qualquer resultado da operação de conector de informações de cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/read | Ler qualquer conector de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Criar ou atualizar qualquer as de informações de autenticação de conector de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/update/action | Atualizar qualquer conector de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/connectors/write | Criar ou atualizar qualquer conector de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/crmmetadata/action | Criar ou atualizar quaisquer metadados de Crm do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/crmmetadata/read | Ler quaisquer metadados de Crm do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/delete | Eliminar qualquer Hub de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/gdpr/delete | Eliminar qualquer Gdpr de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/gdpr/read | Ler qualquer Gdpr de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/gdpr/write | Criar ou atualizar qualquer Gdpr de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Obtenha créditos de faturação de Hub do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Obter histórico de faturação de Hub do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/images/delete | Eliminar qualquer imagem de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/images/read | Ler qualquer imagem de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/images/write | Criar ou atualizar qualquer imagem de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/interactions/delete | Eliminar qualquer interações de informações do cliente do azure |
> | Ação | Microsoft.CustomerInsights/hubs/interactions/operations/read | Ler qualquer resultado da operação de interação de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/interactions/read | Ler qualquer interação do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Sugerir Links de relação para qualquer interações de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/interactions/write | Criar ou atualizar qualquer interação do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/kpi/delete | Eliminar qualquer indicador de chave de desempenho do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/kpi/operations/read | Ler qualquer resultado da operação indicadores chave de desempenho do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/kpi/read | Ler qualquer indicador de chave de desempenho do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Reprocessar qualquer indicadores de chave de desempenho do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/kpi/write | Criar ou atualizar qualquer indicador de chave de desempenho do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/links/delete | Eliminar todas as ligações dos clientes do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/links/operations/read | Ler qualquer resultado da operação de ligações de informações de cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/links/read | Leia as ligações do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/links/write | Criar ou atualizar as ligações de cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/msemetadata/action | Criar ou atualizar quaisquer metadados de Mse de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/msemetadata/read | Ler quaisquer metadados de Mse de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/operationresults/read | Obter resultados da operação do cliente do Azure Insights Hub |
> | Ação | Microsoft.CustomerInsights/hubs/predictions/delete | Eliminar qualquer previsões de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/predictions/operations/read | Ler qualquer resultado da operação de previsões de informações de cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/predictions/read | Ler qualquer previsões de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/predictions/write | Criar ou atualizar qualquer previsões de cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Eliminar quaisquer políticas de correspondência preditiva de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Ler qualquer resultado da operação de políticas de correspondência do Azure ao cliente informações Preditivas |
> | Ação | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Ler quaisquer políticas de correspondência preditiva de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Criar ou atualizar as políticas de correspondência preditiva de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/profiles/delete | Eliminar qualquer perfil de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/profiles/operations/read | Ler qualquer resultado de operação do perfil de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/profiles/read | Ler qualquer perfil de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/profiles/write | Escrever qualquer perfil de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Ação | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Ação | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para o recurso |
> | Ação | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para o recurso |
> | Ação | Microsoft.CustomerInsights/hubs/read | Ler qualquer Hub de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Eliminar as ligações de relação de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Ler qualquer resultado da operação de ligações de relação de informações de cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Leia as ligações de relação de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Criar ou atualizar quaisquer Links de relação do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/relationships/delete | Eliminar quaisquer relações de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/relationships/operations/read | Ler qualquer resultado da operação de relações de informações de clientes do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/relationships/read | Ler quaisquer relações de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/relationships/write | Criar ou atualizar as relações de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Eliminar qualquer atribuição de Rbac do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Ler qualquer resultado da operação de atribuição de Rbac do Azure Customer Insights |
> | Ação | Microsoft.CustomerInsights/hubs/roleAssignments/read | Ler qualquer atribuição de Rbac do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/roleAssignments/write | Criar ou atualizar qualquer atribuição de Rbac do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/roles/read | Ler quaisquer funções de Rbac do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Criar ou atualizar quaisquer metadados de SalesForce de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Ler quaisquer metadados de SalesForce de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/segments/delete | Eliminar qualquer segmento de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Segmentos de qualquer dinâmica de informações do cliente do Azure de gestão |
> | Ação | Microsoft.CustomerInsights/hubs/segments/read | Ler qualquer segmento de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/segments/static/action | Segmentos de qualquer estática de informações do cliente do Azure de gestão |
> | Ação | Microsoft.CustomerInsights/hubs/segments/write | Criar ou atualizar qualquer segmento de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Eliminar qualquer SqlConnectionStrings de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Ler qualquer SqlConnectionStrings de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Criar ou atualizar qualquer SqlConnectionStrings de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Gerar o esquema do tipo de sugerir a partir de dados de exemplo |
> | Ação | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Gerir as definições do hub de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/hubs/views/delete | Eliminar qualquer vista de aplicação do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/views/read | Ler qualquer vista de aplicação do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/views/write | Criar ou atualizar qualquer vista de aplicação do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/widgettypes/read | Ler quaisquer tipos de Widget de aplicação do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/hubs/write | Criar ou atualizar qualquer Hub de informações do cliente do Azure |
> | Ação | Microsoft.CustomerInsights/operations/read | Ler Metadatas de Api do cliente do Azure Insights |
> | Ação | Microsoft.CustomerInsights/register/action | Regista a subscrição para o fornecedor de recursos de informações do cliente e permite a criação de recursos de informações do cliente |
> | Ação | Microsoft.CustomerInsights/unregister/action | Anula o registo da subscrição para o fornecedor de recursos de informações do cliente |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Permite marcar uma recolha para envios de devolução. |
> | Ação | Microsoft.DataBox/jobs/cancel/action | Cancela uma encomenda em curso. |
> | Ação | Microsoft.DataBox/jobs/delete | Eliminar os pedidos |
> | Ação | Microsoft.DataBox/jobs/listCredentials/action | Lista as credenciais não encriptadas relativas à encomenda. |
> | Ação | Microsoft.DataBox/jobs/read | Listar ou obter os pedidos |
> | Ação | Microsoft.DataBox/jobs/write | Criar ou atualizar os pedidos |
> | Ação | Microsoft.DataBox/locations/availableSkus/action | Este método devolve a lista de SKUs disponíveis. |
> | Ação | Microsoft.DataBox/locations/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.DataBox/locations/validateAddress/action | Valida o endereço de envio e fornece endereços alternativos, caso existam. |
> | Ação | Microsoft.DataBox/register/action | Registar fornecedor Microsoft.Databox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Apresenta uma lista ou obter alertas |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Apresenta uma lista ou obter alertas |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Elimina as agendas de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Apresenta uma lista ou obtém as agendas de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Apresenta uma lista ou obtém as agendas de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Cria ou atualiza as agendas de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Elimina os dispositivos de limite de caixa de dados |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Transferir Atualizações no dispositivo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/extendedInformation/action | Obtém o recurso de outras informações sobre o |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | ArmApiDesc_action_getExtendedInformation_dataBoxEdgeDevices |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Instalar Atualizações no dispositivo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Apresenta uma lista ou obtém as tarefas |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Apresenta uma lista ou obtém as definições de rede do dispositivo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Elimina os pedidos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Apresenta uma lista ou obtém os pedidos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Apresenta uma lista ou obtém os pedidos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Cria ou atualiza os pedidos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Apresenta uma lista ou obtém os dispositivos de limite de caixa de dados |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Apresenta uma lista ou obtém os dispositivos de limite de caixa de dados |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Apresenta uma lista ou obtém os dispositivos de limite de caixa de dados |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Elimina as funções |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Apresenta uma lista ou obtém as funções |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Apresenta uma lista ou obtém as funções |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Cria ou atualiza as funções |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Procurar atualizações |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Atualizar definições de segurança |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Elimina as partilhas |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Apresenta uma lista ou obtém as partilhas |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Apresenta uma lista ou obtém as partilhas |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | ArmApiDesc_action_refresh_shares |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Cria ou atualiza as partilhas |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Elimina as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Apresenta uma lista ou obtém as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Apresenta uma lista ou obtém as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Cria ou atualiza as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Elimina os disparadores |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Apresenta uma lista ou obtém os disparadores |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Apresenta uma lista ou obtém os disparadores |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Cria ou atualiza os disparadores |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Apresenta uma lista ou obtém a atualização do resumo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Carregar o certificado para o registo do dispositivo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Elimina os utilizadores da partilha |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Apresenta uma lista ou obtém os utilizadores da partilha |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Apresenta uma lista ou obtém os utilizadores da partilha |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Cria ou atualiza os utilizadores da partilha |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Cria ou atualiza os dispositivos de limite de caixa de dados |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Cria ou atualiza os dispositivos de limite de caixa de dados |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Databricks/register/action | Registar-se na Databricks. |
> | Ação | Microsoft.Databricks/workspaces/delete | Remove uma área de trabalho da Databricks. |
> | Ação | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Define as definições de diagnósticos disponíveis para a área de trabalho do Databricks |
> | Ação | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Adiciona ou modifica as definições de diagnóstico. |
> | Ação | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Obtém as definições de registo disponíveis para a área de trabalho do Databricks |
> | Ação | Microsoft.Databricks/workspaces/read | Obtém uma lista de áreas de trabalho da Databricks. |
> | Ação | Microsoft.Databricks/workspaces/write | Cria uma área de trabalho da Databricks. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DataCatalog/catalogs/delete | Elimina o catálogo. |
> | Ação | Microsoft.DataCatalog/catalogs/read | Obter as propriedades para o catálogo ou catálogos sob a subscrição ou grupo de recursos. |
> | Ação | Microsoft.DataCatalog/catalogs/write | Cria o catálogo ou atualiza as etiquetas e propriedades para o catálogo. |
> | Ação | Microsoft.DataCatalog/checkNameAvailability/action | Verifica a disponibilidade de nome de catálogo para o inquilino. |
> | Ação | Microsoft.DataCatalog/operations/read | Lista as operações disponíveis no fornecedor de recursos de Microsoft.DataCatalog. |
> | Ação | Microsoft.DataCatalog/register/action | Regista a subscrição no fornecedor de recursos de Microsoft.DataCatalog. |
> | Ação | Microsoft.DataCatalog/unregister/action | Anula o registo da subscrição do fornecedor de recursos de Microsoft.DataCatalog. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Verifica se o nome da fábrica de dados está disponível para utilização. |
> | Ação | Microsoft.DataFactory/datafactories/activitywindows/read | Lê o Windows de atividade na fábrica de dados com os parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Lê o Windows de atividade para a atividade do Pipeline com os parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Lê o Windows de atividade para o Pipeline com os parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/delete | Elimina qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Coloca em pausa qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/read | Lê qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Retoma qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/update/action | Atualiza qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/write | Cria ou atualiza qualquer Pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Lê o Windows de atividade para o conjunto de dados com os parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/delete | Elimina a qualquer conjunto de dados. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/read | Lê qualquer conjunto de dados. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Lê a execução de setor de dados para determinado conjunto de dados com a hora de início especificada. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/slices/read | Obtém os setores de dados em determinado período. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/slices/write | Atualize o estado do setor de dados. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/write | Cria ou atualiza qualquer conjunto de dados. |
> | Ação | Microsoft.DataFactory/datafactories/delete | Elimina a fábrica de dados. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Lê as informações de ligação para qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/delete | Elimina qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Lista as chaves de autenticação para qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/read | Lê qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Regenera as chaves de autenticação para qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/write | Cria ou atualiza qualquer Gateway. |
> | Ação | Microsoft.DataFactory/datafactories/linkedServices/delete | Elimina a qualquer serviço ligado. |
> | Ação | Microsoft.DataFactory/datafactories/linkedServices/read | Lê a qualquer serviço ligado. |
> | Ação | Microsoft.DataFactory/datafactories/linkedServices/write | Cria ou atualiza a qualquer serviço ligado. |
> | Ação | Microsoft.DataFactory/datafactories/read | Lê a fábrica de dados. |
> | Ação | Microsoft.DataFactory/datafactories/runs/loginfo/read | Lê um URI de SAS para um contentor de BLOBs que contém os registos. |
> | Ação | Microsoft.DataFactory/datafactories/tables/delete | Elimina a qualquer conjunto de dados. |
> | Ação | Microsoft.DataFactory/datafactories/tables/read | Lê qualquer conjunto de dados. |
> | Ação | Microsoft.DataFactory/datafactories/tables/write | Cria ou atualiza qualquer conjunto de dados. |
> | Ação | Microsoft.DataFactory/datafactories/write | Cria ou atualiza a fábrica de dados. |
> | Ação | Microsoft.DataFactory/factories/cancelpipelinerun/action | Cancela o pipeline de execução especificado pelo ID de execução. |
> | Ação | Microsoft.DataFactory/factories/dataflows/delete | Elimina o fluxo de dados. |
> | Ação | Microsoft.DataFactory/factories/dataflows/read | Lê o fluxo de dados. |
> | Ação | Microsoft.DataFactory/factories/dataflows/write | Criar ou atualizar o fluxo de dados |
> | Ação | Microsoft.DataFactory/factories/datasets/delete | Elimina a qualquer conjunto de dados. |
> | Ação | Microsoft.DataFactory/factories/datasets/read | Lê qualquer conjunto de dados. |
> | Ação | Microsoft.DataFactory/factories/datasets/write | Cria ou atualiza qualquer conjunto de dados. |
> | Ação | Microsoft.DataFactory/factories/delete | Elimina a fábrica de dados. |
> | Ação | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Dispõe do acesso de GitHub token. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/delete | Elimina a qualquer tempo de execução de integração. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Lê as informações de ligação do Runtime de integração. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Obter metadados de Runtime de integração do SSIS para o Runtime de integração especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Lê o estado de Runtime de integração. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Crie a referência do Runtime de integração ligada no Runtime de integração partilhado especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Lista as chaves de autenticação para qualquer Runtime de integração. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Obtém os dados de monitorização para qualquer Runtime de integração. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Elimina o nó para o Runtime de integração especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Devolve o endereço IP para o nó especificado do Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Lê o nó para o Runtime de integração especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Atualiza um nó do Integration Runtime autoalojado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/read | Lê qualquer Runtime de integração. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Atualize os metadados de Runtime de integração do SSIS para o Runtime de integração especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Regenera as chaves de autenticação para o Runtime de integração especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Remove ligado referências de Runtime de integração do Runtime de integração especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/start/action | Inicia qualquer Runtime de integração. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Deixa de qualquer Runtime de integração. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Sincroniza as credenciais para o Runtime de integração especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Atualiza o Runtime de integração especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/write | Cria ou atualiza qualquer Runtime de integração. |
> | Ação | Microsoft.DataFactory/factories/linkedServices/delete | Elimina o serviço ligado. |
> | Ação | Microsoft.DataFactory/factories/linkedServices/read | Leituras de serviço ligado. |
> | Ação | Microsoft.DataFactory/factories/linkedServices/write | Criar ou atualizar o serviço ligado |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Lê que a atividade é executada para o ID de execução de pipeline especificado |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Cancela o pipeline de execução especificado pelo ID de execução. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | A atividade é executada para o pipeline especificado de consultas executadas ID. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Lê que o resultado da atividade de consulta é executada para o ID de execução de pipeline especificado |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/read | Lê as execuções de Pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/createrun/action | Cria uma execução do pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/delete | Elimina o Pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Obtém o progresso das execuções de atividade. |
> | Ação | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Lê a execução de Pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/read | Lê o Pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/write | Criar ou atualizar o Pipeline |
> | Ação | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Consulta as execuções de Pipeline de depuração. |
> | Ação | Microsoft.DataFactory/factories/querypipelineruns/action | Consulta as execuções de Pipeline. |
> | Ação | Microsoft.DataFactory/factories/querypipelineruns/read | Lê o resultado das execuções de Pipeline de consulta. |
> | Ação | Microsoft.DataFactory/factories/querytriggerruns/action | Consulta as execuções do acionador. |
> | Ação | Microsoft.DataFactory/factories/querytriggerruns/read | Lê o resultado de execuções do acionador. |
> | Ação | Microsoft.DataFactory/factories/read | Lê a fábrica de dados. |
> | Ação | Microsoft.DataFactory/factories/triggerruns/read | Lê as execuções do acionador. |
> | Ação | Microsoft.DataFactory/factories/triggers/delete | Elimina qualquer acionador. |
> | Ação | Microsoft.DataFactory/factories/triggers/read | Lê qualquer acionador. |
> | Ação | Microsoft.DataFactory/factories/triggers/start/action | Inicia qualquer acionador. |
> | Ação | Microsoft.DataFactory/factories/triggers/stop/action | Deixa de qualquer acionador. |
> | Ação | Microsoft.DataFactory/factories/triggers/triggerruns/read | Lê as execuções do acionador. |
> | Ação | Microsoft.DataFactory/factories/triggers/write | Cria ou atualiza qualquer acionador. |
> | Ação | Microsoft.DataFactory/factories/write | Criar ou atualizar a fábrica de dados |
> | Ação | Microsoft.DataFactory/locations/configureFactoryRepo/action | Configura o repositório para a fábrica. |
> | Ação | Microsoft.DataFactory/locations/getDataPlaneAccess/action | Obtém acesso ao serviço de plano de dados do ADF. |
> | Ação | Microsoft.DataFactory/locations/getDataPlaneAccess/read | Acesso de leitura ao serviço de plano de dados do ADF. |
> | Ação | Microsoft.DataFactory/locations/getFeatureValue/action | Obter o valor de funcionalidade de controlo de exposição para a localização específica. |
> | Ação | Microsoft.DataFactory/locations/getFeatureValue/read | Lê o valor de funcionalidade de controlo de exposição para a localização específica. |
> | Ação | Microsoft.DataFactory/operations/read | Lê todas as operações no fornecedor de fábrica de dados do Microsoft. |
> | Ação | Microsoft.DataFactory/register/action | Regista a subscrição para o fornecedor de recursos de fábrica de dados. |
> | Ação | Microsoft.DataFactory/unregister/action | Anula o registo da subscrição para o fornecedor de recursos de fábrica de dados. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Elimine uma política de computação. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Obtenha informações sobre uma política de computação. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Criar ou atualizar uma política de computação. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Desassocie a uma conta de DataLakeStore de uma conta no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Obtenha informações sobre uma conta ligada do DataLakeStore de uma conta no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Criar ou atualizar uma conta ligada do DataLakeStore de uma conta no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/delete | Elimine uma conta no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Elimine uma regra de firewall. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Obtenha informações sobre uma regra de firewall. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Criar ou atualizar uma regra de firewall. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Obtenha o resultado de uma operação de conta no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/read | Obtenha informações sobre uma conta no DataLakeAnalytics existente. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Tokens SAS de lista de contentores de armazenamento de uma conta de armazenamento ligada de uma conta no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Obter contentores de uma conta de armazenamento ligada de uma conta no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Desassocie a uma conta de armazenamento de uma conta no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Obtenha informações sobre contas de armazenamento ligadas de uma conta no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Criar ou atualizar uma conta de armazenamento ligada de uma conta no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Conceder permissões para cancelar as tarefas submetidas por outros utilizadores. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Transferi SystemMaxAnalyticsUnits entre contas no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Elimine uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Obtenha informações sobre uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Criar ou atualizar uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/write | Criar ou atualizar uma conta no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/capability/read | Obtenha informações de capacidade de uma subscrição relativamente à utilização no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Verifica a disponibilidade de um nome de conta no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/operationResults/read | Obtenha o resultado de uma operação de conta no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/usages/read | Obtenha informações de utilizações de quota de uma subscrição relativamente à utilização no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/operations/read | Obter operações disponíveis no DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/register/action | Registe a subscrição no datalakeanalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DataLakeStore/accounts/delete | Elimine uma conta do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Ative o Cofre de chaves para uma conta do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Elimine um filtro de EventGrid. |
> | Ação | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Obtenha um filtro de EventGrid. |
> | Ação | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Criar ou atualizar um filtro de EventGrid. |
> | Ação | Microsoft.DataLakeStore/accounts/firewallRules/delete | Elimine uma regra de firewall. |
> | Ação | Microsoft.DataLakeStore/accounts/firewallRules/read | Obtenha informações sobre uma regra de firewall. |
> | Ação | Microsoft.DataLakeStore/accounts/firewallRules/write | Criar ou atualizar uma regra de firewall. |
> | Ação | Microsoft.DataLakeStore/accounts/operationResults/read | Obtenha o resultado de uma operação de conta do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/read | Obtenha informações sobre uma conta existente do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/Superuser/action | Conceder Superutilizador no Data Lake Store quando concedido com Microsoft.Authorization/roleAssignments/write. |
> | Ação | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Elimine um fornecedor de identidade fidedignos. |
> | Ação | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Obtenha informações sobre um fornecedor de identidade fidedignos. |
> | Ação | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Criar ou atualizar um fornecedor de identidade fidedignos. |
> | Ação | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Elimine uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Obtenha informações sobre uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Criar ou atualizar uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeStore/accounts/write | Criar ou atualizar uma conta do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/capability/read | Obtenha informações de capacidade de uma subscrição relativamente à utilização DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Verifica a disponibilidade de um nome de conta do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/operationResults/read | Obtenha o resultado de uma operação de conta do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/usages/read | Obtenha informações de utilizações de quota de uma subscrição relativamente à utilização DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/operations/read | Obter operações disponíveis do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/register/action | Registe a subscrição no DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DataMigration/locations/operationResults/read | Obter o estado de uma operação prolongada relacionada com uma resposta Aceite 202 |
> | Ação | Microsoft.DataMigration/locations/operationStatuses/read | Obter o estado de uma operação prolongada relacionada com uma resposta Aceite 202 |
> | Ação | Microsoft.DataMigration/register/action | Regista a subscrição com o Azure Database Migration Service |
> | Ação | Microsoft.DataMigration/services/checkStatus/action | Verificar se o serviço está implementado e em execução |
> | Ação | Microsoft.DataMigration/services/delete | Elimina um recurso e respetivos subordinados |
> | Ação | Microsoft.DataMigration/services/projects/accessArtifacts/action | Gerar um URL que possa ser utilizado para OBTER ou COLOCAR artefactos de projeto |
> | Ação | Microsoft.DataMigration/services/projects/delete | Elimina um recurso e respetivos subordinados |
> | Ação | Microsoft.DataMigration/services/projects/files/delete | Elimina um recurso e respetivos subordinados |
> | Ação | Microsoft.DataMigration/services/projects/files/read | Ler informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/projects/files/read/action | Obtenha um URL que pode ser utilizado para ler o conteúdo do ficheiro |
> | Ação | Microsoft.DataMigration/services/projects/files/readWrite/action | Obtenha um URL que pode ser utilizado para ler ou escrever o conteúdo do ficheiro |
> | Ação | Microsoft.DataMigration/services/projects/files/write | Criar ou atualizar recursos e respetivas propriedades |
> | Ação | Microsoft.DataMigration/services/projects/read | Ler informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/projects/tasks/cancel/action | Cancelar a tarefa se estiver atualmente em execução |
> | Ação | Microsoft.DataMigration/services/projects/tasks/delete | Elimina um recurso e respetivos subordinados |
> | Ação | Microsoft.DataMigration/services/projects/tasks/read | Ler informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/projects/tasks/write | Executar tarefas das tarefas do Azure Database Migration Service |
> | Ação | Microsoft.DataMigration/services/projects/write | Executar tarefas das tarefas do Azure Database Migration Service |
> | Ação | Microsoft.DataMigration/services/read | Ler informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/slots/delete | Elimina um recurso e respetivos subordinados |
> | Ação | Microsoft.DataMigration/services/slots/read | Ler informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/slots/write | Criar ou atualizar recursos e respetivas propriedades |
> | Ação | Microsoft.DataMigration/services/start/action | Inicie o serviço DMS para permitir que o mesmo processe migrações novamente |
> | Ação | Microsoft.DataMigration/services/stop/action | Pare o serviço DMS para minimizar os respetivos custos |
> | Ação | Microsoft.DataMigration/services/write | Criar ou atualizar recursos e respetivas propriedades |
> | Ação | Microsoft.DataMigration/skus/read | Obtenha uma lista de SKUs suportados por recursos DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DBforMariaDB/locations/performanceTiers/read | Devolve a lista de escalões de desempenho disponíveis. |
> | Ação | Microsoft.DBforMariaDB/performanceTiers/read | Devolve a lista de escalões de desempenho disponíveis. |
> | Ação | Microsoft.DBforMariaDB/servers/configurations/read | Devolve a lista de configurações para um servidor ou obtém as propriedades para a configuração especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Ação | Microsoft.DBforMariaDB/servers/delete | Elimina um servidor existente. |
> | Ação | Microsoft.DBforMariaDB/servers/firewallRules/delete | Elimina uma regra de firewall existente. |
> | Ação | Microsoft.DBforMariaDB/servers/firewallRules/read | Devolve a lista de firewall de regras para um servidor ou obtém as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/firewallRules/write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição disagnostic para o recurso |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para os servidores de MariaDB |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados |
> | Ação | Microsoft.DBforMariaDB/servers/read | Devolve a lista de servidores ou obtém as propriedades para o servidor especificado. |
> | Ação | Microsoft.DBforMariaDB/servers/recoverableServers/read | Devolver as informações de servidor MariaDB recuperáveis |
> | Ação | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Obter os detalhes da política de deteção de ameaças do servidor configurada num determinado servidor |
> | Ação | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Alterar a política de deteção de ameaças do servidor para um determinado servidor |
> | Ação | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Configurações de atualização para o servidor especificado |
> | Ação | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Elimina uma regra de rede Virtual existente |
> | Ação | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Devolve a lista de rede virtual de regras ou obtém as propriedades para a regra de rede virtual especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a regra de rede virtual especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o servidor especificado. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DBforMySQL/locations/performanceTiers/read | Devolve a lista de escalões de desempenho disponíveis. |
> | Ação | Microsoft.DBforMySQL/performanceTiers/read | Devolve a lista de escalões de desempenho disponíveis. |
> | Ação | Microsoft.DBforMySQL/servers/configurations/read | Devolve a lista de configurações para um servidor ou obtém as propriedades para a configuração especificada. |
> | Ação | Microsoft.DBforMySQL/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Ação | Microsoft.DBforMySQL/servers/delete | Elimina um servidor existente. |
> | Ação | Microsoft.DBforMySQL/servers/firewallRules/delete | Elimina uma regra de firewall existente. |
> | Ação | Microsoft.DBforMySQL/servers/firewallRules/read | Devolve a lista de firewall de regras para um servidor ou obtém as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforMySQL/servers/firewallRules/write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição disagnostic para o recurso |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para servidores MySQL |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados |
> | Ação | Microsoft.DBforMySQL/servers/queryTexts/action | Devolver os textos para obter uma lista de consultas |
> | Ação | Microsoft.DBforMySQL/servers/read | Devolve a lista de servidores ou obtém as propriedades para o servidor especificado. |
> | Ação | Microsoft.DBforMySQL/servers/recoverableServers/read | Devolver as informações do servidor MySQL recuperáveis |
> | Ação | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Obter os detalhes da política de deteção de ameaças do servidor configurada num determinado servidor |
> | Ação | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Alterar a política de deteção de ameaças do servidor para um determinado servidor |
> | Ação | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Devolve a lista de estatísticas de consulta para as consultas principais. |
> | Ação | Microsoft.DBforMySQL/servers/updateConfigurations/action | Configurações de atualização para o servidor especificado |
> | Ação | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Elimina uma regra de rede Virtual existente |
> | Ação | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Devolve a lista de rede virtual de regras ou obtém as propriedades para a regra de rede virtual especificada. |
> | Ação | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a regra de rede virtual especificada. |
> | Ação | Microsoft.DBforMySQL/servers/write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o servidor especificado. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Devolve a lista de escalões de desempenho disponíveis. |
> | Ação | Microsoft.DBforPostgreSQL/performanceTiers/read | Devolve a lista de escalões de desempenho disponíveis. |
> | Ação | Microsoft.DBforPostgreSQL/servers/advisors/read | Devolver a lista de consultores |
> | Ação | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Devolver a lista de ações recomendadas |
> | Ação | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Fazer recomendações |
> | Ação | Microsoft.DBforPostgreSQL/servers/configurations/read | Devolve a lista de configurações para um servidor ou obtém as propriedades para a configuração especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/configurations/write | Atualize o valor para a configuração especificada |
> | Ação | Microsoft.DBforPostgreSQL/servers/delete | Elimina um servidor existente. |
> | Ação | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Elimina uma regra de firewall existente. |
> | Ação | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Devolve a lista de firewall de regras para um servidor ou obtém as propriedades para a regra de firewall especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição disagnostic para o recurso |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para os servidores de Postgres |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados |
> | Ação | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Devolver o texto de uma consulta |
> | Ação | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Devolver os textos para obter uma lista de consultas |
> | Ação | Microsoft.DBforPostgreSQL/servers/read | Devolve a lista de servidores ou obtém as propriedades para o servidor especificado. |
> | Ação | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Devolver as informações do servidor PostgreSQL recuperáveis |
> | Ação | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Obter os detalhes da política de deteção de ameaças do servidor configurada num determinado servidor |
> | Ação | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Alterar a política de deteção de ameaças do servidor para um determinado servidor |
> | Ação | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Devolve a lista de estatísticas de consulta para as consultas principais. |
> | Ação | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Configurações de atualização para o servidor especificado |
> | Ação | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Elimina uma regra de rede Virtual existente |
> | Ação | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Devolve a lista de rede virtual de regras ou obtém as propriedades para a regra de rede virtual especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a regra de rede virtual especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Devolver as estatísticas de espera para uma instância |
> | Ação | Microsoft.DBforPostgreSQL/servers/write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o servidor especificado. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Devices/checkNameAvailability/Action | Verifique se IotHub name está disponível |
> | Ação | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Verifique o nome do serviço de aprovisionamento se está disponível |
> | Ação | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Ação | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Elimina o certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Obtém o certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Verifique se o recurso de certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Criar ou atualizar o certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Eliminar o recurso de inquilino do IotHub |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Eliminar grupo de consumidor de EventHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Obter grupo de consumidor de EventHub (s) |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Criar grupo de consumidores do EventHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Exportar dispositivos |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Obtém o inquilino IotHub recurso de estatísticas |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importar dispositivos |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Obtém a chave de inquilino do IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Obter as tarefas detalhes submetidas numa determinada IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Obtém as chaves de inquilino do IotHub |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Obtém as definições de registo disponíveis para o serviço do IotHub |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Obtém as métricas disponíveis para o serviço do IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Obter métricas de Quota |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/Read | Obtém o recurso de inquilino do IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Testar uma mensagem em relação a todas as rotas existentes |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testar uma mensagem em relação a uma rota de teste fornecido |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Obtém o estado de funcionamento de todos os pontos finais de encaminhamento para um IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/Write | Criar ou atualizar o recurso de inquilino do IotHub |
> | Ação | Microsoft.Devices/ElasticPools/metricDefinitions/read | Obtém as métricas disponíveis para o serviço do IotHub |
> | Ação | Microsoft.Devices/iotHubs/certificates/Delete | Elimina o certificado |
> | Ação | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft.Devices/iotHubs/certificates/Read | Obtém o certificado |
> | Ação | Microsoft.Devices/iotHubs/certificates/verify/Action | Verifique se o recurso de certificado |
> | Ação | Microsoft.Devices/iotHubs/certificates/Write | Criar ou atualizar o certificado |
> | Ação | Microsoft.Devices/iotHubs/Delete | Eliminar recurso do IotHub |
> | Ação | Microsoft.Devices/IotHubs/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Ação | Microsoft.Devices/IotHubs/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Ação | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Elimina o filtro do Event Grid |
> | Ação | Microsoft.Devices/iotHubs/eventGridFilters/Read | Obtém o filtro do Event Grid |
> | Ação | Microsoft.Devices/iotHubs/eventGridFilters/Write | Criar um novo ou atualizar o filtro do Event Grid existente |
> | Ação | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Eliminar grupo de consumidor de EventHub |
> | Ação | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Obter grupo de consumidor de EventHub (s) |
> | Ação | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Criar grupo de consumidores do EventHub |
> | Ação | Microsoft.Devices/iotHubs/exportDevices/Action | Exportar dispositivos |
> | Ação | Microsoft.Devices/iotHubs/importDevices/Action | Importar dispositivos |
> | Ação | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Obtenha a chave de IotHub para o nome fornecido |
> | Ação | Microsoft.Devices/iotHubs/iotHubStats/Read | Obter estatísticas do IotHub |
> | Ação | Microsoft.Devices/iotHubs/jobs/Read | Obter as tarefas detalhes submetidas numa determinada IotHub |
> | Ação | Microsoft.Devices/iotHubs/listkeys/Action | Obter todas as chaves de IotHub |
> | Ação | Microsoft.Devices/IotHubs/logDefinitions/read | Obtém as definições de registo disponíveis para o serviço do IotHub |
> | Ação | Microsoft.Devices/IotHubs/metricDefinitions/read | Obtém as métricas disponíveis para o serviço do IotHub |
> | Ação | Microsoft.Devices/iotHubs/operationresults/Read | Obter Resultado da operação (obsoleto API) |
> | Ação | Microsoft.Devices/iotHubs/quotaMetrics/Read | Obter métricas de Quota |
> | Ação | Microsoft.Devices/iotHubs/Read | Obtém os recursos do IotHub |
> | Ação | Microsoft.Devices/iotHubs/routing/$testall/Action | Testar uma mensagem em relação a todas as rotas existentes |
> | Ação | Microsoft.Devices/iotHubs/routing/$testnew/Action | Testar uma mensagem em relação a uma rota de teste fornecido |
> | Ação | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Obtém o estado de funcionamento de todos os pontos finais de encaminhamento para um IotHub |
> | Ação | Microsoft.Devices/iotHubs/skus/Read | Obter Skus válidos do IotHub |
> | Ação | Microsoft.Devices/iotHubs/Write | Criar ou atualizar o recurso do IotHub |
> | Ação | Microsoft.Devices/locations/operationresults/Read | Obter Resultado da operação do Location based |
> | Ação | Microsoft.Devices/operationresults/Read | Obter Resultado da Operação |
> | Ação | Microsoft.Devices/operations/Read | Obter todas as operações de ResourceProvider |
> | Ação | Microsoft.Devices/provisioningServices/certificates/Delete | Elimina o certificado |
> | Ação | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft.Devices/provisioningServices/certificates/Read | Obtém o certificado |
> | Ação | Microsoft.Devices/provisioningServices/certificates/verify/Action | Verifique se o recurso de certificado |
> | Ação | Microsoft.Devices/provisioningServices/certificates/Write | Criar ou atualizar o certificado |
> | Ação | Microsoft.Devices/provisioningServices/Delete | Eliminar recurso IotDps |
> | Ação | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Ação | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Ação | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Obter chaves de IotDps para o nome da chave |
> | Ação | Microsoft.Devices/provisioningServices/listkeys/Action | Obter todas as chaves de IotDps |
> | Ação | Microsoft.Devices/provisioningServices/logDefinitions/read | Obtém as definições de registo disponíveis para o serviço de aprovisionamento |
> | Ação | Microsoft.Devices/provisioningServices/metricDefinitions/read | Obtém as métricas disponíveis para o serviço de aprovisionamento |
> | Ação | Microsoft.Devices/provisioningServices/operationresults/Read | Obter Resultado da operação de DPS |
> | Ação | Microsoft.Devices/provisioningServices/Read | Obter recurso de IotDps |
> | Ação | Microsoft.Devices/provisioningServices/skus/Read | Obter Skus válidos de IotDps |
> | Ação | Microsoft.Devices/provisioningServices/Write | Criar recurso IotDps |
> | Ação | Microsoft.Devices/register/action | Registre-se a subscrição para o fornecedor de recursos de IotHub e permite a criação de recursos do IotHub |
> | Ação | Microsoft.Devices/register/action | Registre-se a subscrição para o fornecedor de recursos de IotHub e permite a criação de recursos do IotHub |
> | Ação | Microsoft.Devices/usages/Read | Obter detalhes de utilização de subscrição para este fornecedor. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DevSpaces/controllers/delete | Eliminar os serviços de controlador de espaços de desenvolvimento do Azure e o plano de dados |
> | Ação | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Lista detalhes de ligação para a infraestrutura do Azure Dev espaços do controlador |
> | Ação | Microsoft.DevSpaces/controllers/read | Propriedades de leitura do Azure Dev espaços controlador |
> | Ação | Microsoft.DevSpaces/controllers/rotateAuthKey/action | Rodar chave de autenticação para o controlador de espaços de desenvolvimento do Azure e voltar a implementar os serviços |
> | Ação | Microsoft.DevSpaces/controllers/write | Criar ou propriedades de atualização do Azure Dev espaços controlador |
> | Ação | Microsoft.DevSpaces/register/action | Registar o fornecedor de recursos de espaços de desenvolvimento da Microsoft com uma subscrição |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DevTestLab/labCenters/delete | Elimine centros do laboratório. |
> | Ação | Microsoft.DevTestLab/labCenters/read | Centros do laboratório de leitura. |
> | Ação | Microsoft.DevTestLab/labCenters/write | Adiciona ou modifica centros do laboratório. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Leia modelos do Gestor de recursos do azure. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Gera um modelo ARM para o artefacto de determinado, carrega os ficheiros necessários para uma conta de armazenamento e valida o artefacto gerado. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Ler artefactos. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/delete | Elimine origens de artefactos. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/read | Leia as origens de artefactos. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/write | Adiciona ou modifica as origens de artefactos. |
> | Ação | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Declarar uma máquina de virtual reclamáveis aleatória no laboratório. |
> | Ação | Microsoft.DevTestLab/labs/costs/read | Leia os custos. |
> | Ação | Microsoft.DevTestLab/labs/costs/write | Adiciona ou modifica os custos. |
> | Ação | Microsoft.DevTestLab/labs/CreateEnvironment/action | Crie máquinas virtuais num laboratório. |
> | Ação | Microsoft.DevTestLab/labs/customImages/delete | Elimine imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/customImages/read | Leia as imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/customImages/write | Adiciona ou modifica as imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/delete | Elimine laboratórios. |
> | Ação | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exporta a utilização de recursos de laboratório para uma conta de armazenamento |
> | Ação | Microsoft.DevTestLab/labs/formulas/delete | Elimine as fórmulas. |
> | Ação | Microsoft.DevTestLab/labs/formulas/read | Leia fórmulas. |
> | Ação | Microsoft.DevTestLab/labs/formulas/write | Adiciona ou modifica as fórmulas. |
> | Ação | Microsoft.DevTestLab/labs/galleryImages/read | Leia as imagens da galeria. |
> | Ação | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Gere um URI para carregar as imagens de disco personalizada a um laboratório. |
> | Ação | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importe uma máquina virtual para um laboratório diferente. |
> | Ação | Microsoft.DevTestLab/labs/ListVhds/action | Listar imagens de disco disponível para criação de imagem personalizada. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/delete | Elimine notificationchannels. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Envie notificação de canal fornecida. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/read | Leia notificationchannels. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/write | Adiciona ou modifica notificationchannels. |
> | Ação | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Avalia a política de laboratório. |
> | Ação | Microsoft.DevTestLab/labs/policySets/policies/delete | Elimine políticas. |
> | Ação | Microsoft.DevTestLab/labs/policySets/policies/read | Leia as políticas. |
> | Ação | Microsoft.DevTestLab/labs/policySets/policies/write | Adicionar ou modificar as políticas. |
> | Ação | Microsoft.DevTestLab/labs/policySets/read | Leia os conjuntos de políticas. |
> | Ação | Microsoft.DevTestLab/labs/read | Laboratórios de leitura. |
> | Ação | Microsoft.DevTestLab/labs/schedules/delete | Elimine agendas. |
> | Ação | Microsoft.DevTestLab/labs/schedules/Execute/action | Execute uma agenda. |
> | Ação | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Apresenta uma lista de todas as agendas aplicável |
> | Ação | Microsoft.DevTestLab/labs/schedules/read | Agenda de leitura. |
> | Ação | Microsoft.DevTestLab/labs/schedules/write | Adicione ou modifique as Programações. |
> | Ação | Microsoft.DevTestLab/labs/serviceRunners/delete | Elimine concorrentes de serviço. |
> | Ação | Microsoft.DevTestLab/labs/serviceRunners/read | Leia concorrentes de serviço. |
> | Ação | Microsoft.DevTestLab/labs/serviceRunners/write | Adiciona ou modifica concorrentes de serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/delete | Elimine perfis de utilizador. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/Attach/action | Anexar e criar a concessão do disco para a máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/delete | Elimine discos. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/Detach/action | Desanexar e interromper a concessão do disco anexado à máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/read | Leitura de discos. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/write | Adiciona ou modifica os discos. |
> | Ação | Microsoft.DevTestLab/labs/users/environments/delete | Elimine ambientes. |
> | Ação | Microsoft.DevTestLab/labs/users/environments/read | Ambientes de leitura. |
> | Ação | Microsoft.DevTestLab/labs/users/environments/write | Adiciona ou modifica os ambientes. |
> | Ação | Microsoft.DevTestLab/labs/users/read | Ler perfis de utilizador. |
> | Ação | Microsoft.DevTestLab/labs/users/secrets/delete | Elimine segredos. |
> | Ação | Microsoft.DevTestLab/labs/users/secrets/read | Leia os segredos. |
> | Ação | Microsoft.DevTestLab/labs/users/secrets/write | Adiciona ou modifica os segredos. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Elimine recursos de infraestrutura do serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Lista as agendas de iniciar/parar aplicável, se aplicável. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Leia os recursos de infraestrutura do serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Elimine agendas. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Execute uma agenda. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Agenda de leitura. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Adicione ou modifique as Programações. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Inicie o service fabric. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Zastaví service fabric. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Adiciona ou modifica os recursos de infraestrutura do serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/write | Adicionar ou modificar perfis de utilizador. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Anexe um disco de dados nova ou existente à máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Aplicam-se de artefactos a máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Obter a propriedade de uma máquina virtual existente |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/delete | Elimine máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Desanexe o disco especificado da máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Obtém uma cadeia de caracteres que representa o conteúdo do ficheiro RDP para a máquina virtual |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Lista as agendas de iniciar/parar aplicável, se aplicável. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/read | Leia as máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Voltar a implementar uma máquina virtual |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Redimensione a Máquina Virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Reinicie uma máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Elimine agendas. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Execute uma agenda. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Agenda de leitura. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Adicione ou modifique as Programações. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Inicie uma máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Parar uma máquina virtual |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Transfere todos os discos de dados ligados à máquina virtual que é detido pelo usuário atual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Propriedade de lançamento de uma máquina virtual existente |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/write | Adiciona ou modifica máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualNetworks/delete | Elimine redes virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualNetworks/read | Leia as redes virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualNetworks/write | Adiciona ou modifica as redes virtuais. |
> | Ação | Microsoft.DevTestLab/labs/vmPools/delete | Elimine agrupamentos de máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/vmPools/read | Leia os agrupamentos de máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/vmPools/write | Adicionar ou modificar agrupamentos de máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/write | Adicionar ou modificar laboratórios. |
> | Ação | Microsoft.DevTestLab/locations/operations/read | Operações de leitura. |
> | Ação | Microsoft.DevTestLab/register/action | Regista a subscrição |
> | Ação | Microsoft.DevTestLab/schedules/delete | Elimine agendas. |
> | Ação | Microsoft.DevTestLab/schedules/Execute/action | Execute uma agenda. |
> | Ação | Microsoft.DevTestLab/schedules/read | Agenda de leitura. |
> | Ação | Microsoft.DevTestLab/schedules/Retarget/action | ID de recurso de destino de uma agenda de atualizações |
> | Ação | Microsoft.DevTestLab/schedules/write | Adicione ou modifique as Programações. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DocumentDB/databaseAccountNames/read | Verifica a existência de disponibilidade do nome. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/backup/action | Submeter um pedido para configurar a cópia de segurança |
> | Ação | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Alterar o grupo de recursos de uma conta de base de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Lê a coleção de definições de métricas. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Lê as métricas de coleção. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Ler a base de dados conta partição nível métricas-chave |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Ler métricas ao nível da base de dados conta partição |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Leia as partições de conta de base de dados de uma coleção |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Ler conta da base de dados utilizações de nível de partição |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Lê as utilizações de coleção. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Lê as definições de métricas de base de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Lê as métricas de base de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Lê as utilizações de base de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/delete | Elimina as contas de base de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Alterar as prioridades de ativação pós-falha de regiões de uma conta de base de dados. Isto é utilizado para efetuar a operação de ativação pós-falha manual |
> | Ação | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Obter as cadeias de ligação para uma conta de base de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Listar chaves de uma conta de base de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Lê a base de dados de definições de métricas da conta. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/metrics/read | Lê as métricas da conta de base de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Offline uma região de uma conta de base de dados.  |
> | Ação | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Online uma região de uma conta de base de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Ler o status da operação assíncrona |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Métricas de latência de leitura |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/read | Ler percentis das latências de replicação |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Ler métricas de latência para uma região de origem e de destino específica |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Métricas de latência de leitura para uma região de destino específico |
> | Ação | Microsoft.DocumentDB/databaseAccounts/read | Lê uma conta de base de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Lê a base de dados de chaves da conta só de leitura. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Lê a base de dados de chaves da conta só de leitura. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Rodar chaves de uma conta de base de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Lê as métricas de coleção regional. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Ler a base de dados regionais conta partição nível métricas-chave |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Ler métricas ao nível da base de dados regionais conta partição |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Leia as partições de conta de base de dados regionais numa coleção |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Lê as métricas da conta de base de dados e região. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/restore/action | Submeter um pedido de restauro |
> | Ação | Microsoft.DocumentDB/databaseAccounts/usages/read | Lê a base de dados utilizações de conta. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/write | Atualize um contas de base de dados. |
> | Ação | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Notifica Microsoft.DocumentDB que VirtualNetwork ou sub-rede está a ser eliminada |
> | Ação | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Ler o Status de deleteVirtualNetworkOrSubnets operação assíncrona |
> | Ação | Microsoft.DocumentDB/operationResults/read | Ler o status da operação assíncrona |
> | Ação | Microsoft.DocumentDB/operations/read | Ler operações disponíveis para a Microsoft DocumentDB  |
> | Ação | Microsoft.DocumentDB/register/action |  Registar o fornecedor de recursos do Microsoft DocumentDB para a subscrição |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DomainRegistration/checkDomainAvailability/Action | Verifique se um domínio está disponível para compra |
> | Ação | Microsoft.DomainRegistration/domains/Delete | Elimine um domínio existente. |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Eliminar o identificador de propriedade |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Identificadores de propriedade de lista |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Obter o identificador de propriedade |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Criar ou atualizar o identificador |
> | Ação | Microsoft.DomainRegistration/domains/operationresults/Read | Operação de obtenção de um domínio |
> | Ação | Microsoft.DomainRegistration/domains/Read | Obter a lista de domínios |
> | Ação | Microsoft.DomainRegistration/domains/Read | Obter o domínio |
> | Ação | Microsoft.DomainRegistration/domains/renew/Action | Renove um domínio existente. |
> | Ação | Microsoft.DomainRegistration/domains/Write | Adicionar um novo domínio ou atualizar um já existente |
> | Ação | Microsoft.DomainRegistration/generateSsoRequest/Action | Gere um pedido para iniciar sessão no Centro de controlo de domínio. |
> | Ação | Microsoft.DomainRegistration/listDomainRecommendations/Action | Obter as recomendações de domínio de lista com base em palavras-chave |
> | Ação | Microsoft.DomainRegistration/operations/Read | Listar todas as operações de registo de domínio do serviço de aplicações |
> | Ação | Microsoft.DomainRegistration/register/action | Registar o fornecedor de recursos do Microsoft Domains para a subscrição |
> | Ação | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Ação de contrato de lista |
> | Ação | Microsoft.DomainRegistration/topLevelDomains/Read | Obter toplevel domínios |
> | Ação | Microsoft.DomainRegistration/topLevelDomains/Read | Obter toplevel domínio |
> | Ação | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Validar o objeto de compra de domínio sem enviá-lo |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.EventGrid/eventSubscriptions/delete | Eliminar um eventSubscription |
> | Ação | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Obter o url completo para a subscrição de evento |
> | Ação | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico para subscrições de eventos |
> | Ação | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição de diagnóstico para subscrições de eventos |
> | Ação | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para eventSubscriptions |
> | Ação | Microsoft.EventGrid/eventSubscriptions/read | Ler um eventSubscription |
> | Ação | Microsoft.EventGrid/eventSubscriptions/write | Criar ou atualizar um eventSubscription |
> | Ação | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico para tópicos |
> | Ação | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição de diagnóstico para os tópicos |
> | Ação | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para os tópicos |
> | Ação | Microsoft.EventGrid/extensionTopics/read | Leia um extensionTopic. |
> | Ação | Microsoft.EventGrid/locations/eventSubscriptions/read | Subscrições de eventos regionais de lista |
> | Ação | Microsoft.EventGrid/locations/operationResults/read | Leia o resultado de uma operação regional |
> | Ação | Microsoft.EventGrid/locations/operationsStatus/read | Ler o estado de uma operação regional |
> | Ação | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Subscrições de eventos regionais de lista por topictype |
> | Ação | Microsoft.EventGrid/operationResults/read | O resultado de uma operação de leitura |
> | Ação | Microsoft.EventGrid/operations/read | EventGrid operações de lista. |
> | Ação | Microsoft.EventGrid/operationsStatus/read | O estado de uma operação de leitura |
> | Ação | Microsoft.EventGrid/register/action | Regista a subscrição para o fornecedor de recursos de EventGrid. |
> | Ação | Microsoft.EventGrid/topics/delete | Eliminar um tópico |
> | Ação | Microsoft.EventGrid/topics/listKeys/action | Listar chaves para um tópico |
> | Ação | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico para tópicos |
> | Ação | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição de diagnóstico para os tópicos |
> | Ação | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para os tópicos |
> | Ação | Microsoft.EventGrid/topics/read | Leia um tópico |
> | Ação | Microsoft.EventGrid/topics/regenerateKey/action | Regenerar chave para um tópico |
> | Ação | Microsoft.EventGrid/topics/write | Criar ou atualizar um tópico |
> | Ação | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Lista de subscrições de eventos global por tipo de tópico |
> | Ação | Microsoft.EventGrid/topictypes/eventtypes/read | Ler eventtypes suportado por um topictype |
> | Ação | Microsoft.EventGrid/topictypes/read | Ler um topictype |
> | Ação | Microsoft.EventGrid/unregister/action | Anula o registo da subscrição para o fornecedor de recursos de EventGrid. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.EventHub/checkNameAvailability/action | Verifica a disponibilidade do espaço de nomes em determinada subscrição. |
> | Ação | Microsoft.EventHub/checkNamespaceAvailability/action | Verifica a disponibilidade do espaço de nomes em determinada subscrição. Esta API foi preterida utilize CheckNameAvailability em vez disso. |
> | Ação | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obter a lista de descrições de recursos do Cluster métricas |
> | Ação | Microsoft.EventHub/clusters/read | Obtém a Descrição de Recurso de Cluster |
> | Ação | Microsoft.EventHub/clusters/write | Obtém a Descrição de Recurso de Cluster |
> | Ação | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Elimina as regras de VNet no Fornecedor de Recursos de EventHub para a VNet especificada |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/action | Regra de autorização de espaço de nomes de atualizações. Esta API foi preterida. Utilize uma chamada PUT para atualizar a regra de autorização de espaço de nomes em vez disso,.... Esta operação não é suportada na versão 2017-04-01 de API. |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/delete | Elimine regra de autorização de espaço de nomes. Não é possível eliminar a regra de autorização de espaço de nomes predefinido.  |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Obter Cadeia de Ligação para o Espaço de nomes |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/read | Obter a lista de descrição das Regras de Autorização de Espaços de nomes. |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/write | Criar regras de autorização ao nível de espaço de nomes e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o principal e as chaves secundárias podem ser atualizadas. |
> | Ação | Microsoft.EventHub/namespaces/Delete | Eliminar Recurso de Espaço de nomes |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtém as chaves das regras de autorização do espaço de nomes principal da Recuperação Após Desastre |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter as Regras de Autorização do Espaço de Nomes Principal da Recuperação Após Desastre |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Desativa a Recuperação Após Desastre e para a replicação de alterações de espaço de nomes principais para secundários. |
> | Ação | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica a disponibilidade do alias do espaço de nomes numa determinada subscrição. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Elimina a configuração de recuperação após desastre associada no espaço de nomes. Esta operação só pode ser chamada por meio do espaço de nomes primário. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Invoca uma ativação pós-falha GEO DR e reconfigura o alias de espaço de nomes para apontar para o espaço de nomes secundário. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Obtém a configuração da Recuperação Após Desastre associada ao espaço de nomes. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Cria ou Atualiza a configuração da Recuperação Após Desastre associada ao espaço de nomes. |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Operação para atualizar o EventHub. Esta operação não é suportada na versão 2017-04-01 de API. Regras de autorização. Utilize uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Operação para eliminar regras de autorização de EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Obter cadeia de ligação de EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Obter a lista de regras de autorização de EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Criar regras de autorização de EventHub e atualizar as respetivas propriedades. Os direitos de acesso de regras de autorização podem ser atualizados. |
> | Ação | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operação para eliminar o recurso de ConsumerGroup |
> | Ação | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Obter lista de descrições de recursos de ConsumerGroup |
> | Ação | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Criar ou propriedades de ConsumerGroup de atualização. |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/Delete | Operação para eliminar os recursos do EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/read | Obter lista de descrições de recursos do EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/write | Criar ou propriedades de EventHub de atualização. |
> | Ação | Microsoft.EventHub/namespaces/ipFilterRules/delete | Eliminar Recurso de Filtro de IP |
> | Ação | Microsoft.EventHub/namespaces/ipFilterRules/read | Obter o Recurso de Filtro de IP |
> | Ação | Microsoft.EventHub/namespaces/ipFilterRules/write | Criar Recurso de Filtro de IP |
> | Ação | Microsoft.EventHub/namespaces/messagingPlan/read | Obtém o plano de mensagens para um espaço de nomes.<br>Esta API foi preterida.<br>As propriedades expostas por intermédio do recurso MessagingPlan são movidas para o recurso de espaço de nomes em versões de API posteriores do (principal)....<br>Esta operação não é suportada na versão 2017-04-01 de API. |
> | Ação | Microsoft.EventHub/namespaces/messagingPlan/write | Atualiza o plano de mensagens para um espaço de nomes.<br>Esta API foi preterida.<br>As propriedades expostas por intermédio do recurso MessagingPlan são movidas para o recurso de espaço de nomes em versões de API posteriores do (principal)....<br>Esta operação não é suportada na versão 2017-04-01 de API. |
> | Ação | Microsoft.EventHub/namespaces/operationresults/read | Obter o estado da operação de Espaço de nomes |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obter lista de descrições de recursos do espaço de nomes das definições de diagnóstico |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obter lista de descrições de recursos do espaço de nomes das definições de diagnóstico |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obter lista de registos de espaço de nomes descrições de recursos |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de descrições de recursos do espaço de nomes métricas |
> | Ação | Microsoft.EventHub/namespaces/read | Obter a lista de Descrição de Recursos de Espaço de nomes |
> | Ação | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Remova o espaço de nomes do ACS |
> | Ação | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Eliminar Recurso de Regra de VNET |
> | Ação | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Obtém o Recurso de Regra de VNET |
> | Ação | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Criar Recurso de Regra de VNET |
> | Ação | Microsoft.EventHub/namespaces/write | Criar um recurso de espaço de nomes e Atualize as respetivas propriedades. As etiquetas e a capacidade do espaço de nomes são propriedades que podem ser atualizadas. |
> | Ação | Microsoft.EventHub/operations/read | Obter Operações |
> | Ação | Microsoft.EventHub/register/action | Regista a subscrição do fornecedor de recursos do EventHub e ativa a criação de recursos do EventHub |
> | Ação | Microsoft.EventHub/sku/read | Obter lista de descrições de recursos de Sku |
> | Ação | Microsoft.EventHub/sku/regions/read | Obter lista de descrições de recursos de SkuRegions |
> | Ação | Microsoft.EventHub/unregister/action | Regista o Fornecedor de Recursos do EventHub |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Features/features/read | Obtém as funcionalidades de uma subscrição. |
> | Ação | Microsoft.Features/operations/read | Obtém a lista de operações. |
> | Ação | Microsoft.Features/providers/features/read | Obtém a funcionalidade de uma subscrição de um fornecedor de recursos específico. |
> | Ação | Microsoft.Features/providers/features/register/action | Regista a funcionalidade para uma subscrição de um fornecedor de recursos específico. |
> | Ação | Microsoft.Features/providers/features/unregister/action | Anula o registo da funcionalidade para uma subscrição de um fornecedor de recursos específico. |
> | Ação | Microsoft.Features/register/action | Regista a funcionalidade de uma subscrição. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | A atribuição de configuração de convidado de GET. |
> | Ação | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Relatório de atribuição de configuração de convidado de GET. |
> | Ação | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Crie nova atribuição de configuração de convidado. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.HDInsight/clusters/applications/delete | Eliminar a aplicação para o Cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/applications/read | Obter a aplicação para o Cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/applications/write | Criar ou atualizar a aplicação para o Cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/changerdpsetting/action | Alterar a definição de RDP para o Cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/configurations/action | Atualizar a configuração de Cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/configurations/read | Obter configurações de Cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/delete | Eliminar um Cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico para o recurso de Cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição de diagnóstico para o recurso de Cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para o Cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/read | Obter detalhes sobre o Cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/roles/resize/action | Redimensionar um Cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/write | Criar ou atualizar o Cluster do HDInsight |
> | Ação | Microsoft.HDInsight/locations/capabilities/read | Obter capacidades de subscrição |
> | Ação | Microsoft.HDInsight/locations/checkNameAvailability/read | Verificar Disponibilidade do Nome |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ImportExport/jobs/delete | Elimina uma tarefa existente. |
> | Ação | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Obtém as chaves do BitLocker para o trabalho especificado. |
> | Ação | Microsoft.ImportExport/jobs/read | Obtém as propriedades para o trabalho especificado ou devolve a lista de tarefas. |
> | Ação | Microsoft.ImportExport/jobs/write | Cria uma tarefa com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o trabalho especificado. |
> | Ação | Microsoft.ImportExport/locations/read | Obtém as propriedades para a localização especificada ou devolve a lista de localizações. |
> | Ação | Microsoft.ImportExport/register/action | Regista a subscrição para o fornecedor de recursos de importação/exportação e permite a criação de tarefas de importação/exportação. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Insights/ActionGroups/Delete | Eliminar um grupo de ações |
> | Ação | Microsoft.Insights/ActionGroups/Read | Ler um grupo de ações |
> | Ação | Microsoft.Insights/ActionGroups/Write | Criar ou atualizar um grupo de ações |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Alerta do Registo de Atividade ativado |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Delete | Eliminar um alerta de registo de atividade |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Read | Ler um alerta do registo de atividade |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Write | Criar ou atualizar um alerta de registo de atividade |
> | Ação | Microsoft.Insights/AlertRules/Activated/Action | Alerta de métrica clássica ativado |
> | Ação | Microsoft.Insights/AlertRules/Delete | Eliminar um alerta de métrica clássico |
> | Ação | Microsoft.Insights/AlertRules/Incidents/Read | Ler um incidente de alerta de métrica clássica |
> | Ação | Microsoft.Insights/AlertRules/Read | Ler um alerta de métrica clássica |
> | Ação | Microsoft.Insights/AlertRules/Resolved/Action | Alerta de métrica clássica resolvido |
> | Ação | Microsoft.Insights/AlertRules/Throttled/Action | Regra de alerta de métrica clássica limitada |
> | Ação | Microsoft.Insights/AlertRules/Write | Criar ou atualizar um alerta de métrica clássico |
> | Ação | Microsoft.Insights/AutoscaleSettings/Delete | Eliminar uma definição de dimensionamento automático |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Ler uma definição de diagnóstico do recurso |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Criar ou atualizar uma definição de diagnóstico de recurso |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Ler definições de registo |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Ler definições de métricas |
> | Ação | Microsoft.Insights/AutoscaleSettings/Read | Ler uma definição de dimensionamento automático |
> | Ação | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Redução do Dimensionamento Automático iniciada |
> | Ação | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Redução do Dimensionamento Automático concluída |
> | Ação | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Aumento do dimensionamento automático iniciado |
> | Ação | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Aumento do dimensionamento automático concluído |
> | Ação | Microsoft.Insights/AutoscaleSettings/Write | Criar ou atualizar uma definição de dimensionamento automático |
> | Ação | Microsoft.Insights/Baseline/Read | Leia uma linha de base em métrica (pré-visualização) |
> | Ação | Microsoft.Insights/CalculateBaseline/Read | Calcular a linha de base para valores de métrica (pré-visualização) |
> | Ação | Microsoft.Insights/Components/AnalyticsItems/Delete | Eliminar um item de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsItems/Read | A ler um item de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsItems/Write | Escrever um item de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Action | Ação de tabela de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Delete | Eliminar um esquema de tabela de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Read | A ler um esquema de tabela de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Write | A escrever um esquema de tabela de análise do Application Insights |
> | Ação | Microsoft.Insights/Components/Annotations/Delete | Eliminar uma anotação do Application Insights |
> | Ação | Microsoft.Insights/Components/Annotations/Read | A ler uma anotação do Application Insights |
> | Ação | Microsoft.Insights/Components/Annotations/Write | A escrever uma anotação do Application Insights |
> | Ação | Microsoft.Insights/Components/Api/Read | A ler a API de dados de componente do Application Insights |
> | Ação | Microsoft.Insights/Components/ApiKeys/Action | Gerar uma chave de API do Application Insights |
> | Ação | Microsoft.Insights/Components/ApiKeys/Delete | Eliminar uma chave de API do Application Insights |
> | Ação | Microsoft.Insights/Components/ApiKeys/Read | A ler uma chave de API do Application Insights |
> | Ação | Microsoft.Insights/Components/BillingPlanForComponent/Read | A ler um plano de faturação para o componente do Application Insights |
> | Ação | Microsoft.Insights/Components/CurrentBillingFeatures/Read | A ler as atuais funcionalidades de faturação para componente do Application Insights |
> | Ação | Microsoft.Insights/Components/CurrentBillingFeatures/Write | A escrever as atuais funcionalidades de faturação para componente do Application Insights |
> | Ação | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | A ler uma configuração de integração de ALM predefinida do Application Insights |
> | Ação | Microsoft.Insights/Components/Delete | A eliminar uma configuração de componentes de informações da aplicação |
> | Ação | Microsoft.Insights/Components/Events/Read | Obter registos do Application Insights com o formato de consulta do OData |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Action | Ação de definições de exportação do Application Insights |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Delete | A eliminar definições de exportação do Application Insights |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Read | A ler configurações de exportação do Application Insights |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Write | A escrever definições de exportação do Application Insights |
> | Ação | Microsoft.Insights/Components/ExtendQueries/Read | A ler os resultados de consulta estendida de componente do Application Insights |
> | Ação | Microsoft.Insights/Components/Favorites/Delete | Eliminar um favorito do Application Insights |
> | Ação | Microsoft.Insights/Components/Favorites/Read | A ler um favorito do Application Insights |
> | Ação | Microsoft.Insights/Components/Favorites/Write | A escrever um favorito do Application Insights |
> | Ação | Microsoft.Insights/Components/FeatureCapabilities/Read | A ler as funcionalidades de componente do Application Insights |
> | Ação | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | A ler as funcionalidades de faturaçao disponíveis de componente do Application Insights |
> | Ação | Microsoft.Insights/Components/GetToken/Read | A ler um token de componente do Application Insights |
> | Ação | Microsoft.Insights/Components/MetricDefinitions/Read | A ler definições métricas de componente do Application Insights |
> | Ação | Microsoft.Insights/Components/Metrics/Read | A ler métricas de componente do Application Insights |
> | Ação | Microsoft.Insights/Components/Move/Action | Mover um Componente do Application Insights para outro grupo de recursos ou subscrição |
> | Ação | Microsoft.Insights/Components/MyAnalyticsItems/Delete | A eliminar um item de análise pessoal do Application Insights |
> | Ação | Microsoft.Insights/Components/MyAnalyticsItems/Read | A ler um item de análise pessoal do Application Insights |
> | Ação | Microsoft.Insights/Components/MyAnalyticsItems/Write | Escrever um item de análise pessoal do Application Insights |
> | Ação | Microsoft.Insights/Components/MyFavorites/Read | A ler um favorito pessoal do Application Insights |
> | Ação | Microsoft.Insights/Components/Operations/Read | Obter o estado de operações de execução longa no Application Insights |
> | Ação | Microsoft.Insights/Components/PricingPlans/Read | A ler um plano de preços de componentes do Application Insights |
> | Ação | Microsoft.Insights/Components/PricingPlans/Write | A escrever um plano de preços de componente do Application Insights |
> | Ação | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | A ler configuração de deteção proativa do Application Insights |
> | Ação | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | A escrever configuração de deteção proativa do Application Insights |
> | Ação | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Ler definições de métricas |
> | Ação | Microsoft.Insights/Components/Purge/Action | A remover os dados do Application Insights |
> | Ação | Microsoft.Insights/Components/Query/Read | Executar consultas aos registos do Application Insights |
> | Ação | Microsoft.Insights/Components/QuotaStatus/Read | A ler estado de quota de componente do Application Insights |
> | Ação | Microsoft.Insights/Components/Read | A ler uma configuração de componentes de informações da aplicação |
> | Ação | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | A ler as localizações de webtest do Application Insights |
> | Ação | Microsoft.Insights/Components/Webtests/Read | A ler uma configuração de webtest |
> | Ação | Microsoft.Insights/Components/WorkItemConfigs/Delete | Eliminar uma configuração de integração de ALM do Application Insights |
> | Ação | Microsoft.Insights/Components/WorkItemConfigs/Read | A ler uma configuração de integração de ALM do Application Insights |
> | Ação | Microsoft.Insights/Components/WorkItemConfigs/Write | A escrever uma configuração de integração de ALM predefinida do Application Insights |
> | Ação | Microsoft.Insights/Components/Write | A escrever para uma configuração de componentes de informações da aplicação |
> | Ação | Microsoft.Insights/DiagnosticSettings/Delete | Eliminar uma definição de diagnóstico de recursos |
> | Ação | Microsoft.Insights/DiagnosticSettings/Read | Ler uma definição de diagnóstico do recurso |
> | Ação | Microsoft.Insights/DiagnosticSettings/Write | Criar ou atualizar uma definição de diagnóstico de recurso |
> | Ação | Microsoft.Insights/EventCategories/Read | Ler as categorias disponíveis de eventos do Registo de Atividades |
> | Ação | Microsoft.Insights/eventtypes/digestevents/Read | Ler resumo do tipo de eventos de gestão |
> | Ação | Microsoft.Insights/eventtypes/values/Read | Ler eventos de Registo de Atividades |
> | Ação | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Eliminar uma definição do diagnóstico do registo do fluxo de rede |
> | Ação | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Ler uma definição do diagnóstico do registo do fluxo de rede |
> | Ação | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Criar ou atualizar uma definição do diagnóstico do registo de fluxo de rede |
> | Ação | Microsoft.Insights/ListMigrationDate/Action | Voltar a obter a data de migração da Subscrição |
> | Ação | Microsoft.Insights/ListMigrationDate/Read | Voltar a obter a data de migração da subscrição |
> | Ação | Microsoft.Insights/LogDefinitions/Read | Ler definições de registo |
> | Ação | Microsoft.Insights/LogProfiles/Delete | Eliminar um perfil de registo do Registo de Atividades |
> | Ação | Microsoft.Insights/LogProfiles/Read | Ler um perfil de registo do Registo de Atividades |
> | Ação | Microsoft.Insights/LogProfiles/Write | Criar ou atualizar um perfil de registo do Registo de Atividades |
> | Ação | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Ler dados da tabela ADAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/ADReplicationResult/Read | Ler dados da tabela ADReplicationResult |
> | Ação | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Ler dados da tabela ADSecurityAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/Alert/Read | Ler dados da tabela de Alertas |
> | Ação | Microsoft.Insights/Logs/AlertHistory/Read | Ler dados da tabela AlertHistory |
> | Ação | Microsoft.Insights/Logs/ApplicationInsights/Read | Ler dados da tabela ApplicationInsights |
> | Ação | Microsoft.Insights/Logs/AzureActivity/Read | Ler dados da tabela AzureActivity |
> | Ação | Microsoft.Insights/Logs/AzureMetrics/Read | Ler dados da tabela AzureMetrics |
> | Ação | Microsoft.Insights/Logs/BoundPort/Read | Ler dados da tabela BoundPort |
> | Ação | Microsoft.Insights/Logs/CommonSecurityLog/Read | Ler dados da tabela CommonSecurityLog |
> | Ação | Microsoft.Insights/Logs/ComputerGroup/Read | Ler dados da tabela ComputerGroup |
> | Ação | Microsoft.Insights/Logs/ConfigurationChange/Read | Ler dados da tabela ConfigurationChange |
> | Ação | Microsoft.Insights/Logs/ConfigurationData/Read | Ler dados da tabela ConfigurationData |
> | Ação | Microsoft.Insights/Logs/ContainerImageInventory/Read | Ler dados da tabela ContainerImageInventory |
> | Ação | Microsoft.Insights/Logs/ContainerInventory/Read | Ler dados da tabela ContainerInventory |
> | Ação | Microsoft.Insights/Logs/ContainerLog/Read | Ler dados da tabela ContainerLog |
> | Ação | Microsoft.Insights/Logs/ContainerServiceLog/Read | Ler dados da tabela ContainerServiceLog |
> | Ação | Microsoft.Insights/Logs/DeviceAppCrash/Read | Ler dados da tabela DeviceAppCrash |
> | Ação | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Ler dados da tabela DeviceAppLaunch |
> | Ação | Microsoft.Insights/Logs/DeviceCalendar/Read | Ler dados da tabela DeviceCalendar |
> | Ação | Microsoft.Insights/Logs/DeviceCleanup/Read | Ler dados da tabela DeviceCleanup |
> | Ação | Microsoft.Insights/Logs/DeviceConnectSession/Read | Ler dados da tabela DeviceConnectSession |
> | Ação | Microsoft.Insights/Logs/DeviceEtw/Read | Ler dados da tabela DeviceEtw |
> | Ação | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Ler dados da tabela DeviceHardwareHealth |
> | Ação | Microsoft.Insights/Logs/DeviceHealth/Read | Ler dados da tabela DeviceHealth |
> | Ação | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Ler dados da tabela DeviceHeartbeat |
> | Ação | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Ler dados da tabela DeviceSkypeHeartbeat |
> | Ação | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Ler dados da tabela DeviceSkypeSignIn |
> | Ação | Microsoft.Insights/Logs/DeviceSleepState/Read | Ler dados da tabela DeviceSleepState |
> | Ação | Microsoft.Insights/Logs/DHAppFailure/Read | Ler dados da tabela DHAppFailure |
> | Ação | Microsoft.Insights/Logs/DHAppReliability/Read | Ler dados da tabela DHAppReliability |
> | Ação | Microsoft.Insights/Logs/DHDriverReliability/Read | Ler dados da tabela DHDriverReliability |
> | Ação | Microsoft.Insights/Logs/DHLogonFailures/Read | Ler dados da tabela DHLogonFailures |
> | Ação | Microsoft.Insights/Logs/DHLogonMetrics/Read | Ler dados da tabela DHLogonMetrics |
> | Ação | Microsoft.Insights/Logs/DHOSCrashData/Read | Ler dados da tabela DHOSCrashData |
> | Ação | Microsoft.Insights/Logs/DHOSReliability/Read | Ler dados da tabela DHOSReliability |
> | Ação | Microsoft.Insights/Logs/DHWipAppLearning/Read | Ler dados da tabela DHWipAppLearning |
> | Ação | Microsoft.Insights/Logs/DnsEvents/Read | Ler dados da tabela DnsEvents |
> | Ação | Microsoft.Insights/Logs/DnsInventory/Read | Ler dados da tabela DnsInventory |
> | Ação | Microsoft.Insights/Logs/ETWEvent/Read | Ler dados da tabela ETWEvent |
> | Ação | Microsoft.Insights/Logs/Event/Read | Ler dados da tabela de Eventos |
> | Ação | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Ler dados da tabela ExchangeAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Ler dados da tabela ExchangeOnlineAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/Heartbeat/Read | Ler dados da tabela Heartbeat |
> | Ação | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Ler dados da tabela IISAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/InboundConnection/Read | Ler dados da tabela InboundConnection |
> | Ação | Microsoft.Insights/Logs/KubeNodeInventory/Read | Ler dados da tabela KubeNodeInventory |
> | Ação | Microsoft.Insights/Logs/KubePodInventory/Read | Ler dados da tabela KubePodInventory |
> | Ação | Microsoft.Insights/Logs/LinuxAuditLog/Read | Ler dados da tabela LinuxAuditLog |
> | Ação | Microsoft.Insights/Logs/MAApplication/Read | Ler dados da tabela MAApplication |
> | Ação | Microsoft.Insights/Logs/MAApplicationHealth/Read | Ler dados da tabela MAApplicationHealth |
> | Ação | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Ler dados da tabela MAApplicationHealthAlternativeVersions |
> | Ação | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Ler dados da tabela MAApplicationHealthIssues |
> | Ação | Microsoft.Insights/Logs/MAApplicationInstance/Read | Ler dados da tabela MAApplicationInstance |
> | Ação | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Ler dados da tabela MAApplicationInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Ler dados da tabela MAApplicationReadiness |
> | Ação | Microsoft.Insights/Logs/MADeploymentPlan/Read | Ler dados da tabela MADeploymentPlan |
> | Ação | Microsoft.Insights/Logs/MADevice/Read | Ler dados da tabela MADevice |
> | Ação | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Ler dados da tabela MADevicePnPHealth |
> | Ação | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | Ação | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Ler dados da tabela MADevicePnPHealthIssues |
> | Ação | Microsoft.Insights/Logs/MADeviceReadiness/Read | Ler dados da tabela MADeviceReadiness |
> | Ação | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Ler dados da tabela MADriverInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/MADriverReadiness/Read | Ler dados da tabela MADriverReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddin/Read | Ler dados da tabela MAOfficeAddin |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Ler dados da tabela MAOfficeAddinHealth |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Ler dados da tabela MAOfficeAddinHealthIssues |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Ler dados da tabela MAOfficeAddinInstance |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Ler dados da tabela MAOfficeAddinReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeApp/Read | Ler dados da tabela MAOfficeApp |
> | Ação | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Ler dados da tabela MAOfficeAppHealth |
> | Ação | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Ler dados da tabela MAOfficeAppInstance |
> | Ação | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Ler dados da tabela MAOfficeAppReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Ler dados da tabela MAOfficeBuildInfo |
> | Ação | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Ler dados da tabela MAOfficeCurrencyAssessment |
> | Ação | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Ler dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | Ação | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Ler dados da tabela MAOfficeDeploymentStatus |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Ler dados da tabela MAOfficeMacroHealth |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Ler dados da tabela MAOfficeMacroHealthIssues |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Ler dados da tabela MAOfficeMacroIssueInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Ler dados da tabela MAOfficeMacroIssueReadiness |
> | Ação | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Ler dados da tabela MAOfficeMacroSummary |
> | Ação | Microsoft.Insights/Logs/MAOfficeSuite/Read | Ler dados da tabela MAOfficeSuite |
> | Ação | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Ler dados da tabela MAOfficeSuiteInstance |
> | Ação | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Ler dados da tabela MAProposedPilotDevices |
> | Ação | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Ler dados da tabela MAWindowsBuildInfo |
> | Ação | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Ler dados da tabela MAWindowsCurrencyAssessment |
> | Ação | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Ler dados da tabela MAWindowsCurrencyAssessmentDailyCounts |
> | Ação | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Ler dados da tabela MAWindowsDeploymentStatus |
> | Ação | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | Ação | Microsoft.Insights/Logs/NetworkMonitoring/Read | Ler dados da tabela NetworkMonitoring |
> | Ação | Microsoft.Insights/Logs/OfficeActivity/Read | Ler dados da tabela OfficeActivity |
> | Ação | Microsoft.Insights/Logs/Operation/Read | Ler dados da tabela de Operação |
> | Ação | Microsoft.Insights/Logs/OutboundConnection/Read | Ler dados da tabela OutboundConnection |
> | Ação | Microsoft.Insights/Logs/Perf/Read | Ler dados da tabela de Desempenho |
> | Ação | Microsoft.Insights/Logs/ProtectionStatus/Read | Ler dados da tabela ProtectionStatus |
> | Ação | Microsoft.Insights/Logs/Read | A ler dados de todos os seus registos |
> | Ação | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Ler dados da tabela ReservedAzureCommonFields |
> | Ação | Microsoft.Insights/Logs/ReservedCommonFields/Read | Ler dados da tabela ReservedCommonFields |
> | Ação | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Ler dados da tabela SCCMAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Ler dados da tabela SCOMAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SecurityAlert/Read | Ler dados da tabela SecurityAlert |
> | Ação | Microsoft.Insights/Logs/SecurityBaseline/Read | Ler dados da tabela SecurityBaseline |
> | Ação | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Ler dados da tabela SecurityBaselineSummary |
> | Ação | Microsoft.Insights/Logs/SecurityDetection/Read | Ler dados da tabela SecurityDetection |
> | Ação | Microsoft.Insights/Logs/SecurityEvent/Read | Ler dados da tabela SecurityEvent |
> | Ação | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Ler dados da tabela ServiceFabricOperationalEvent |
> | Ação | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Ler dados da tabela ServiceFabricReliableActorEvent |
> | Ação | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Ler dados da tabela ServiceFabricReliableServiceEvent |
> | Ação | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Ler dados da tabela SfBAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Ler dados da tabela SfBOnlineAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Ler dados da tabela SharePointOnlineAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Ler dados da tabela SPAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Ler dados da tabela SQLAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Ler dados da tabela SQLQueryPerformance |
> | Ação | Microsoft.Insights/Logs/Syslog/Read | Ler dados da tabela Syslog |
> | Ação | Microsoft.Insights/Logs/SysmonEvent/Read | Ler dados da tabela SysmonEvent |
> | Ação | Microsoft.Insights/Logs/UAApp/Read | Ler dados da tabela UAApp |
> | Ação | Microsoft.Insights/Logs/UAComputer/Read | Ler dados da tabela UAComputer |
> | Ação | Microsoft.Insights/Logs/UAComputerRank/Read | Ler dados da tabela UAComputerRank |
> | Ação | Microsoft.Insights/Logs/UADriver/Read | Ler dados da tabela UADriver |
> | Ação | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Ler dados da tabela UADriverProblemCodes |
> | Ação | Microsoft.Insights/Logs/UAFeedback/Read | Ler dados da tabela UAFeedback |
> | Ação | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Ler dados da tabela UAHardwareSecurity |
> | Ação | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Ler dados da tabela UAIESiteDiscovery |
> | Ação | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Ler dados da tabela UAOfficeAddIn |
> | Ação | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Ler dados da tabela UAProposedActionPlan |
> | Ação | Microsoft.Insights/Logs/UASysReqIssue/Read | Ler dados da tabela UASysReqIssue |
> | Ação | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Ler dados da tabela UAUpgradedComputer |
> | Ação | Microsoft.Insights/Logs/Update/Read | Ler dados da tabela de Atualização |
> | Ação | Microsoft.Insights/Logs/UpdateRunProgress/Read | Ler dados da tabela UpdateRunProgress |
> | Ação | Microsoft.Insights/Logs/UpdateSummary/Read | Ler dados da tabela UpdateSummary |
> | Ação | Microsoft.Insights/Logs/Usage/Read | Ler dados da tabela de Utilização |
> | Ação | Microsoft.Insights/Logs/W3CIISLog/Read | Ler dados da tabela W3CIISLog |
> | Ação | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Ler dados da tabela WaaSDeploymentStatus |
> | Ação | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Ler dados da tabela WaaSInsiderStatus |
> | Ação | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Ler dados da tabela WaaSUpdateStatus |
> | Ação | Microsoft.Insights/Logs/WDAVStatus/Read | Ler dados da tabela WDAVStatus |
> | Ação | Microsoft.Insights/Logs/WDAVThreat/Read | Ler dados da tabela WDAVThreat |
> | Ação | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Ler dados da tabela WindowsClientAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/WindowsFirewall/Read | Ler dados da tabela WindowsFirewall |
> | Ação | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Ler dados da tabela WindowsServerAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/WireData/Read | Ler dados da tabela WireData |
> | Ação | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Ler dados da tabela WUDOAggregatedStatus |
> | Ação | Microsoft.Insights/Logs/WUDOStatus/Read | Ler dados da tabela WUDOStatus |
> | Ação | Microsoft.Insights/MetricAlerts/Delete | Eliminar um alerta de métrica |
> | Ação | Microsoft.Insights/MetricAlerts/Read | Escrever um alerta de métrica |
> | Ação | Microsoft.Insights/MetricAlerts/Status/Read | Ler o estado de alerta de métrica |
> | Ação | Microsoft.Insights/MetricAlerts/Write | Criar ou atualizar um alerta de métrica |
> | Ação | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Ler definições de métricas |
> | Ação | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Ler definições de métricas |
> | Ação | Microsoft.Insights/MetricDefinitions/Read | Ler definições de métricas |
> | Ação | Microsoft.Insights/Metrics/Action | Ações de métricas |
> | Ação | Microsoft.Insights/Metrics/providers/Metrics/Read | Ler métricas |
> | Ação | Microsoft.Insights/Metrics/Read | Ler métricas |
> | DataAction | Microsoft.Insights/Metrics/Write | Escrever métricas |
> | Ação | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrar subscrição para o novo modelo de preços |
> | Ação | Microsoft.Insights/Operations/Read | Operações de leitura |
> | Ação | Microsoft.Insights/Register/Action | Registar o fornecedor do Microsoft Insights |
> | Ação | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Anular subscrição para o modelo de preços legado |
> | Ação | Microsoft.Insights/ScheduledQueryRules/Delete | A eliminar uma regra de consulta agendada |
> | Ação | Microsoft.Insights/ScheduledQueryRules/Read | A ler uma regra de consulta agendada |
> | Ação | Microsoft.Insights/ScheduledQueryRules/Write | A escrever uma regra de consulta agendada |
> | Ação | Microsoft.Insights/Tenants/Register/Action | Inicia o fornecedor do Microsoft Insights |
> | Ação | Microsoft.Insights/Unregister/Action | Registar o fornecedor do Microsoft Insights |
> | Ação | Microsoft.Insights/Webtests/Delete | A eliminar uma configuração de webtest |
> | Ação | Microsoft.Insights/Webtests/GetToken/Read | A ler um token de webtest |
> | Ação | Microsoft.Insights/Webtests/MetricDefinitions/Read | A ler definições de métrica de webtest |
> | Ação | Microsoft.Insights/Webtests/Metrics/Read | A ler métricas de webtest |
> | Ação | Microsoft.Insights/Webtests/Read | A ler uma configuração de webtest |
> | Ação | Microsoft.Insights/Webtests/Write | A escrever para uma configuração de webtest |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Intune/diagnosticsettings/delete | A eliminar uma definição de diagnóstico |
> | Ação | Microsoft.Intune/diagnosticsettings/read | Ler uma definição de diagnóstico |
> | Ação | Microsoft.Intune/diagnosticsettings/write | Escrever uma definição de diagnóstico |
> | Ação | Microsoft.Intune/diagnosticsettingscategories/read | Ler um categorias de definição de diagnóstico |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.IoTCentral/checkNameAvailability/action | Verifica se um nome de aplicação de IoT Central está disponível |
> | Ação | Microsoft.IoTCentral/checkSubdomainAvailability/action | Verifica se um subdomínio de aplicação de IoT Central está disponível |
> | Ação | Microsoft.IoTCentral/IoTApps/delete | Elimina um aplicações de centro de IoT |
> | Ação | Microsoft.IoTCentral/IoTApps/read | Obtém um único aplicativo de Central de IoT |
> | Ação | Microsoft.IoTCentral/IoTApps/write | Cria ou atualiza um aplicações de centro de IoT |
> | Ação | Microsoft.IoTCentral/operations/read | Obtém todas as operações disponíveis em aplicações de IoT Central |
> | Ação | Microsoft.IoTCentral/register/action | Registar a subscrição para o fornecedor de recursos do Azure IoT Central |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.IoTSpaces/Graph/delete | Elimina o recurso de gráfico Microsoft.IoTSpaces |
> | Ação | Microsoft.IoTSpaces/Graph/read | Obtém os recursos de Microsoft.IoTSpaces Graph |
> | Ação | Microsoft.IoTSpaces/Graph/write | Criar recurso Microsoft.IoTSpaces Graph |
> | Ação | Microsoft.IoTSpaces/register/action | Registar subscrição para o fornecedor de recursos de gráfico Microsoft.IoTSpaces permitir a criação de recursos |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.KeyVault/checkNameAvailability/read | Verifica se um nome de cofre de chaves é válido e se não está em utilização |
> | Ação | Microsoft.KeyVault/deletedVaults/read | Visualizar as propriedades dos cofres de chaves eliminados de forma recuperável |
> | Ação | Microsoft.KeyVault/hsmPools/delete | Eliminar um conjunto de HSM |
> | Ação | Microsoft.KeyVault/hsmPools/joinVault/action | Associar um cofre de chaves a um conjunto de HSM |
> | Ação | Microsoft.KeyVault/hsmPools/read | Visualizar as propriedades de um conjunto HSM |
> | Ação | Microsoft.KeyVault/hsmPools/write | Criar um novo conjunto de HSM de atualização das propriedades de um conjunto de HSM existente |
> | Ação | Microsoft.KeyVault/locations/deletedVaults/purge/action | Remover um cofre de chaves eliminado de forma recuperável |
> | Ação | Microsoft.KeyVault/locations/deletedVaults/read | Visualizar as propriedades de um cofre de chaves eliminado de forma recuperável |
> | Ação | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft.KeyVault de que uma rede virtual ou sub-rede está a ser eliminada |
> | Ação | Microsoft.KeyVault/locations/operationResults/read | Verificar o resultado de uma operação de execução longa |
> | Ação | Microsoft.KeyVault/operations/read | Lista as operações disponíveis no fornecedor de recursos Microsoft.KeyVault |
> | Ação | Microsoft.KeyVault/register/action | Regista uma subscrição |
> | Ação | Microsoft.KeyVault/unregister/action | Cancela o registo de uma subscrição |
> | Ação | Microsoft.KeyVault/vaults/accessPolicies/write | Atualize uma política de acesso existente através da intercalação ou substituição, ou adicione uma nova política de acesso ao cofre. |
> | Ação | Microsoft.KeyVault/vaults/delete | Eliminar um cofre de chaves |
> | Ação | Microsoft.KeyVault/vaults/deploy/action | Permite o acesso a segredos num cofre de chaves ao implementar os recursos do Azure |
> | Ação | Microsoft.KeyVault/vaults/read | Ver as propriedades de um cofre de chaves |
> | Ação | Microsoft.KeyVault/vaults/secrets/read | Ver as propriedades de um segredo, mas não o seu valor |
> | Ação | Microsoft.KeyVault/vaults/secrets/write | Crie um novo segredo ou atualize o valor de um segredo existente |
> | Ação | Microsoft.KeyVault/vaults/write | Criar um novo cofre de chaves ou atualizar as propriedades de um cofre de chaves existente |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Kusto/Clusters/Activate/action | O início do cluster. |
> | Ação | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Verifica a disponibilidade de nome de cluster. |
> | Ação | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Adiciona entidades de base de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Elimina um recurso de ligações de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Lê um recurso de ligações de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Escreve um recurso de ligações de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Valida a ligação de dados de base de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/delete | Elimina um recurso de base de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Elimina um recurso de ligações de Hub de eventos. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Lê um recurso de ligações de Hub de eventos. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Escreve um recurso de ligações de Hub de eventos. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Valida a ligação do Hub de eventos de base de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Listas de base de dados principais. |
> | Ação | Microsoft.Kusto/Clusters/Databases/read | Lê um recurso de base de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Remove de base de dados principais. |
> | Ação | Microsoft.Kusto/Clusters/Databases/write | Escreve um recurso de base de dados. |
> | Ação | Microsoft.Kusto/Clusters/Deactivate/action | Interrompe o cluster. |
> | Ação | Microsoft.Kusto/Clusters/delete | Elimina um recurso de cluster. |
> | Ação | Microsoft.Kusto/Clusters/read | Lê um recurso de cluster. |
> | Ação | Microsoft.Kusto/Clusters/SKUs/read | Lê um recurso SKU do cluster. |
> | Ação | Microsoft.Kusto/Clusters/Start/action | O início do cluster. |
> | Ação | Microsoft.Kusto/Clusters/Stop/action | Interrompe o cluster. |
> | Ação | Microsoft.Kusto/Clusters/write | Escreve um recurso de cluster. |
> | Ação | Microsoft.Kusto/Locations/CheckNameAvailability/action | Verifica a disponibilidade de nome de recurso. |
> | Ação | Microsoft.Kusto/locations/operationresults/read | Lê recursos de operações |
> | Ação | Microsoft.Kusto/Operations/read | Lê recursos de operações |
> | Ação | Microsoft.Kusto/Register/action | Regista a subscrição para o fornecedor de recursos do Kusto. |
> | Ação | Microsoft.Kusto/SKUs/read | Lê um recurso SKU. |
> | Ação | Microsoft.Kusto/Unregister/action | Anula o registo da subscrição para o fornecedor de recursos do Kusto. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.LabServices/labAccounts/CreateLab/action | Crie um laboratório numa conta do laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/delete | Elimine contas de laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/galleryImages/delete | Elimine imagens da galeria. |
> | Ação | Microsoft.LabServices/labAccounts/galleryImages/read | Leia as imagens da galeria. |
> | Ação | Microsoft.LabServices/labAccounts/galleryImages/write | Adiciona ou modifica as imagens da galeria. |
> | Ação | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Obtenha informações de disponibilidade regional para cada categoria de tamanho configurada com uma conta de laboratório |
> | Ação | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Adicionar utilizadores a um laboratório |
> | Ação | Microsoft.LabServices/labAccounts/labs/delete | Elimine laboratórios. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Um ambiente aleatório para um utilizador nas definições de ambiente de afirmações |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Elimine definição do ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | O ambiente de afirmações e o atribui ao utilizador |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Elimine ambientes. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Ambientes de leitura. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Repõe a palavra-passe do utilizador num ambiente |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Começa um ambiente a partir de todos os recursos dentro do ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Um ambiente é interrompido devido ao parar todos os recursos dentro do ambiente |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Adiciona ou modifica os ambientes. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Aprovisiona/desprovisiona necessários recursos para um definição de ambiente com base no estado atual da definição/ambiente de laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Ler definição de ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Elimine agendas. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Agenda de leitura. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Adicione ou modifique as Programações. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Começa um modelo a partir de todos os recursos dentro do modelo. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Interrompe um modelo, parando todos os recursos dentro do modelo. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/Upload/action | Carrega a imagem de modelo atual para a Galeria partilhada na conta de laboratório |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Adicionar ou modificar a definição do ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/read | Laboratórios de leitura. |
> | Ação | Microsoft.LabServices/labAccounts/labs/Register/action | Registre-se para o laboratório gerido. |
> | Ação | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Enviar um e-mail com a ligação de registo para o laboratório |
> | Ação | Microsoft.LabServices/labAccounts/labs/users/delete | Elimine utilizadores. |
> | Ação | Microsoft.LabServices/labAccounts/labs/users/read | Leia os utilizadores. |
> | Ação | Microsoft.LabServices/labAccounts/labs/users/write | Adiciona ou modifica os utilizadores. |
> | Ação | Microsoft.LabServices/labAccounts/labs/write | Adicionar ou modificar laboratórios. |
> | Ação | Microsoft.LabServices/labAccounts/read | Leia as contas de laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Elimine sharedgalleries. |
> | Ação | Microsoft.LabServices/labAccounts/sharedGalleries/read | Leia sharedgalleries. |
> | Ação | Microsoft.LabServices/labAccounts/sharedGalleries/write | Adiciona ou modifica sharedgalleries. |
> | Ação | Microsoft.LabServices/labAccounts/sharedImages/delete | Elimine sharedimages. |
> | Ação | Microsoft.LabServices/labAccounts/sharedImages/read | Leia sharedimages. |
> | Ação | Microsoft.LabServices/labAccounts/sharedImages/write | Adiciona ou modifica sharedimages. |
> | Ação | Microsoft.LabServices/labAccounts/write | Adiciona ou modifica contas de laboratório. |
> | Ação | Microsoft.LabServices/locations/operations/read | Operações de leitura. |
> | Ação | Microsoft.LabServices/register/action | Regista a subscrição |
> | Ação | Microsoft.LabServices/users/GetEnvironment/action | Obtém os detalhes da máquina virtual |
> | Ação | Microsoft.LabServices/users/GetOperationBatchStatus/action | Obter estado da operação de batch |
> | Ação | Microsoft.LabServices/users/GetOperationStatus/action | Obtém o estado da operação de longa |
> | Ação | Microsoft.LabServices/users/GetPersonalPreferences/action | Obter preferências pessoais de um utilizador |
> | Ação | Microsoft.LabServices/users/ListEnvironments/action | Ambientes de lista para o utilizador |
> | Ação | Microsoft.LabServices/users/ListLabs/action | Laboratórios de lista para o utilizador. |
> | Ação | Microsoft.LabServices/users/Register/action | Registe-se um utilizador a um laboratório gerido |
> | Ação | Microsoft.LabServices/users/ResetPassword/action | Repõe a palavra-passe do utilizador num ambiente |
> | Ação | Microsoft.LabServices/users/StartEnvironment/action | Começa um ambiente a partir de todos os recursos dentro do ambiente. |
> | Ação | Microsoft.LabServices/users/StopEnvironment/action | Um ambiente é interrompido devido ao parar todos os recursos dentro do ambiente |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.LocationBasedServices/accounts/delete | (Preterido: Utilize /providers/Microsoft.Maps) eliminar uma conta do Location Based Services. |
> | Ação | Microsoft.LocationBasedServices/accounts/listKeys/action | (Preterido: Utilize as chaves de conta do Location Based Services /providers/Microsoft.Maps)List |
> | Ação | Microsoft.LocationBasedServices/accounts/read | (Preterido: Utilize /providers/Microsoft.Maps) obter uma conta do Location Based Services. |
> | Ação | Microsoft.LocationBasedServices/accounts/regenerateKey/action | (Preterido: Utilize /providers/Microsoft.Maps) gerar nova conta do Location Based Services chave primária ou secundária |
> | Ação | Microsoft.LocationBasedServices/accounts/write | (Preterido: Utilize /providers/Microsoft.Maps) criar ou atualizar uma conta do Location Based Services. |
> | Ação | Microsoft.LocationBasedServices/register/action | (Preterido: Utilize /providers/Microsoft.Maps) para registar o fornecedor |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.LocationServices/accounts/delete | (Preterido: Utilize /providers/Microsoft.Maps) eliminar uma conta de serviços de localização. |
> | Ação | Microsoft.LocationServices/accounts/listKeys/action | (Preterido: Utilize as chaves de conta do Location Based Services /providers/Microsoft.Maps)List |
> | Ação | Microsoft.LocationServices/accounts/read | (Preterido: Utilize /providers/Microsoft.Maps) obter uma conta de serviços de localização. |
> | Ação | Microsoft.LocationServices/accounts/regenerateKey/action | (Preterido: Utilize /providers/Microsoft.Maps) gerar nova conta do Location Based Services chave primária ou secundária |
> | Ação | Microsoft.LocationServices/accounts/write | (Preterido: Utilize /providers/Microsoft.Maps) criar ou atualizar uma conta de serviços de localização. |
> | Ação | Microsoft.LocationServices/register/action | (Preterido: Utilize /providers/Microsoft.Maps) para registar o fornecedor |

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
> | Ação | Microsoft.LogAnalytics/logs/read | A ler dados de todos os seus registos |
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
> | Ação | Microsoft.Logic/integrationAccounts/agreements/delete | Elimina o acordo na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Obtém o URL de chamada de retorno para o conteúdo do acordo na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/read | Lê o acordo na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/write | Cria ou atualiza o acordo na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/delete | Elimina a assemblagem na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Obtém o URL de chamada de retorno para o conteúdo da assemblagem na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/read | Lê a assemblagem na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/write | Cria ou atualiza a assemblagem na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Elimina a configuração do lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Lê a configuração do lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Cria ou atualiza a configuração de lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/certificates/delete | Elimina o certificado na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/certificates/read | Lê o certificado na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/certificates/write | Cria ou atualiza o certificado na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/delete | Elimina a conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/join/action | Adere à Conta de Integração. |
> | Ação | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Obtém o URL de chamada de retorno para a conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Obtém as chaves no cofre de chaves. |
> | Ação | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Regista os eventos de rastreio na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/delete | Elimina o mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Obtém o URL de chamada de retorno para o conteúdo do mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/read | Lê o mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/write | Cria ou atualiza o mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/delete | Elimina o parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Obtém o URL de chamada de retorno para o conteúdo de parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/read | Lê o parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/write | Cria ou atualiza o parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Lê as definições de registo da Conta de Integração. |
> | Ação | Microsoft.Logic/integrationAccounts/read | Lê a conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/delete | Elimina o esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Obtém o URL de chamada de retorno para o conteúdo do esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/read | Lê o esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/write | Cria ou atualiza o esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/sessions/delete | Elimina a sessão na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/sessions/read | Lê a configuração do lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/sessions/write | Cria ou atualiza a sessão na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/write | Cria ou atualiza a conta de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/delete | Elimina o ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/join/action | Associa o Ambiente de Serviço de Integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Lê a operação da API gerida pelo ambiente de serviço de integração |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Lê a API gerida pelo ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Lê os estados da operação do ambiente de serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Lê as definições de métrica do ambiente de serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/read | Lê o ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/write | Cria ou atualiza o ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Obtém os grupos de operação recomendados do fluxo de trabalho. |
> | Ação | Microsoft.Logic/locations/workflows/validate/action | Valida o fluxo de trabalho. |
> | Ação | Microsoft.Logic/operations/read | Obtém a operação. |
> | Ação | Microsoft.Logic/register/action | Regista o fornecedor de recursos de Microsoft.Logic para uma determinada subscrição. |
> | Ação | Microsoft.Logic/workflows/accessKeys/delete | Elimina a chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/list/action | Lista os segredos da chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/read | Lê a chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/regenerate/action | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/write | Cria ou atualiza a chave de acesso. |
> | Ação | Microsoft.Logic/workflows/delete | Elimina o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/disable/action | Desativa o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/enable/action | Ativa o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/listCallbackUrl/action | Obtém o URL de chamada de retorno do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/listSwagger/action | Obtém as definições do swagger do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/move/action | Move o Fluxo de trabalho do ID de subscrição, grupo de recursos e/ou nome existentes para um ID de subscrição, grupo de recursos e/ou nome diferentes. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Lê as definições de diagnóstico do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza as definições de diagnóstico do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Lê as definições de registo do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Lê as definições de métrica do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/read | Lê o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/regenerateAccessKey/action | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft.Logic/workflows/run/action | Inicia a execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Obtém os rastreios de expressão da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/read | Lê a ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Obtém os rastreios de expressão da repetição da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/repetitions/read | Lê a repetição da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Lê o histórico de pedidos de ação de repetição da execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Lê o histórico de pedidos de ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Lê a repetição do âmbito da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/cancel/action | Cancela a execução de um fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/delete | Elimina uma execução de um fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/operations/read | Lê o estado da operação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/read | Lê a execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/suspend/action | Suspende o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/triggers/histories/read | Lê o histórico do acionador. |
> | Ação | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Submete novamente o acionador do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Obtém o URL de chamada de retorno para o acionador. |
> | Ação | Microsoft.Logic/workflows/triggers/read | Lê o acionador. |
> | Ação | Microsoft.Logic/workflows/triggers/reset/action | Repõe o acionador. |
> | Ação | Microsoft.Logic/workflows/triggers/run/action | Executa o acionador. |
> | Ação | Microsoft.Logic/workflows/triggers/setState/action | Define o estado do acionador. |
> | Ação | Microsoft.Logic/workflows/validate/action | Valida o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/versions/read | Lê a versão de fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Obtém o URL de chamada de retorno para o acionador. |
> | Ação | Microsoft.Logic/workflows/write | Cria ou atualiza o fluxo de trabalho. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Mover qualquer associação de plano de compromisso de aprendizagem |
> | Ação | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Ler qualquer associação de plano de compromisso de aprendizagem |
> | Ação | Microsoft.MachineLearning/commitmentPlans/delete | Eliminar qualquer compromisso de plano de Aprendizado de máquina |
> | Ação | Microsoft.MachineLearning/commitmentPlans/join/action | Junte-se a qualquer máquina compromisso plano de aprendizagem |
> | Ação | Microsoft.MachineLearning/commitmentPlans/read | Ler qualquer compromisso de plano de Aprendizado de máquina |
> | Ação | Microsoft.MachineLearning/commitmentPlans/write | Criar ou atualizar qualquer plano de compromisso do Machine Learning |
> | Ação | Microsoft.MachineLearning/locations/operationresults/read | Obter o resultado de uma operação do Machine Learning |
> | Ação | Microsoft.MachineLearning/locations/operationsstatus/read | Obter o estado de uma operação de Aprendizado de máquina em curso |
> | Ação | Microsoft.MachineLearning/operations/read | Obter operações de aprendizagem automática |
> | Ação | Microsoft.MachineLearning/register/action | Regista a subscrição para a fornecedor de recursos do serviço web de aprendizagem automática e permite a criação de serviços da web. |
> | Ação | Microsoft.MachineLearning/skus/read | Obter SKUs de plano de compromisso de aprendizagem automática |
> | Ação | Microsoft.MachineLearning/webServices/action | Criar as propriedades do serviço Web regionais para regiões suportadas |
> | Ação | Microsoft.MachineLearning/webServices/delete | Eliminar qualquer serviço Web do Machine Learning |
> | Ação | Microsoft.MachineLearning/webServices/listkeys/read | Obter chaves para um serviço Web do Machine Learning |
> | Ação | Microsoft.MachineLearning/webServices/read | Ler qualquer serviço Web do Machine Learning |
> | Ação | Microsoft.MachineLearning/webServices/write | Criar ou atualizar qualquer serviço Web do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/delete | Eliminar qualquer área de trabalho de aprendizagem |
> | Ação | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Listar chaves para uma área de trabalho do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/read | Ler qualquer área de trabalho de aprendizagem |
> | Ação | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Ressincronizar chaves de conta de armazenamento configurada para uma área de trabalho do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/write | Criar ou atualizar qualquer área de trabalho de aprendizagem |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Verifique se as atualizações estão disponíveis para os serviços de sistema para o cluster de operacionalização |
> | Ação | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Eliminar qualquer conta de alojamento |
> | Ação | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Listar as chaves associadas com cluster de operacionalização |
> | Ação | Microsoft.MachineLearningCompute/operationalizationClusters/read | Ler qualquer conta de alojamento |
> | Ação | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Atualizar os serviços do sistema num cluster de operacionalização |
> | Ação | Microsoft.MachineLearningCompute/operationalizationClusters/write | Criar ou atualizar qualquer conta de alojamento |
> | Ação | Microsoft.MachineLearningCompute/register/action | Regista o ID de subscrição para o fornecedor de recursos e permite a criação de uma máquina de recursos de computação de aprendizagem |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.MachineLearningModelManagement/accounts/delete | Eliminar qualquer conta de alojamento |
> | Ação | Microsoft.MachineLearningModelManagement/accounts/read | Ler qualquer conta de alojamento |
> | Ação | Microsoft.MachineLearningModelManagement/accounts/write | Criar ou atualizar qualquer conta de alojamento |
> | Ação | Microsoft.MachineLearningModelManagement/register/action | Regista o ID de subscrição para o fornecedor de recursos e permite a criação de uma conta de alojamento |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Obtém o estado de uma operação de computação específica |
> | Ação | Microsoft.MachineLearningServices/locations/usages/read | Relatório de utilização para aml os recursos numa subscrição de computação |
> | Ação | Microsoft.MachineLearningServices/locations/vmsizes/read | Obter os tamanhos de vm suportadas |
> | Ação | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Obtém o estado de uma operação de espaço de trabalho específico |
> | Ação | Microsoft.MachineLearningServices/register/action | Regista a subscrição para o fornecedor de recursos de serviços do Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/delete | Elimina os recursos de computação nas áreas de trabalho do Machine Learning Services |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Listar segredos para recursos de computação na área de trabalho do Machine Learning Services |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Lista de nós para o recurso de computação na área de trabalho do Machine Learning Services |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/read | Obtém os recursos de computação nas áreas de trabalho do Machine Learning Services |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/write | Cria ou atualiza os recursos de computação nas áreas de trabalho do Machine Learning Services |
> | Ação | Microsoft.MachineLearningServices/workspaces/delete | Elimina o Machine Learning Services áreas de trabalho |
> | Ação | Microsoft.MachineLearningServices/workspaces/listKeys/action | Lista os segredos para uma área de trabalho de serviços do Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/read | Obtém o Machine Learning Services áreas de trabalho |
> | Ação | Microsoft.MachineLearningServices/workspaces/write | Cria ou atualiza a áreas de trabalho de serviços do Machine Learning |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ManagedIdentity/register/action | Regista a subscrição para o fornecedor de recursos de identidade gerida |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Ação de RBAC para atribuir um utilizador existente a um recurso a identidade atribuída |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Elimina um identidade atribuída ao utilizador existente |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/read | Obtém um identidade atribuída ao utilizador existente |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/write | Cria um nova identidade atribuída ao utilizador ou atualiza as marcas associadas com um identidade atribuída ao utilizador existente |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ManagedLab/labAccounts/CreateLab/action | Crie um laboratório numa conta do laboratório. |
> | Ação | Microsoft.ManagedLab/labAccounts/delete | Elimine contas de laboratório. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/delete | Elimine laboratórios. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Elimine definição do ambiente. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Elimine ambientes. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Ambientes de leitura. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Adiciona ou modifica os ambientes. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Ler definição de ambiente. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Adicionar ou modificar a definição do ambiente. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Elimine máquinas virtuais do laboratório. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Leia as máquinas virtuais do laboratório. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Adiciona ou modifica máquinas virtuais do laboratório. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/read | Laboratórios de leitura. |
> | Ação | Microsoft.ManagedLab/labAccounts/labs/write | Adicionar ou modificar laboratórios. |
> | Ação | Microsoft.ManagedLab/labAccounts/read | Leia as contas de laboratório. |
> | Ação | Microsoft.ManagedLab/labAccounts/write | Adiciona ou modifica contas de laboratório. |
> | Ação | Microsoft.ManagedLab/locations/operations/read | Operações de leitura. |
> | Ação | Microsoft.ManagedLab/register/action | Regista a subscrição |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Management/checkNameAvailability/action | Verifica se o nome do grupo de gestão especificado é válido e exclusivo. |
> | Ação | Microsoft.Management/getEntities/action | Liste todas as entidades (grupos de gestão, subscrições, etc.) para o usuário autenticado. |
> | Ação | Microsoft.Management/managementGroups/delete | Elimine grupo de gestão. |
> | Ação | Microsoft.Management/managementGroups/read | Lista os grupos de gestão para o usuário autenticado. |
> | Ação | Microsoft.Management/managementGroups/subscriptions/delete | Associa anular a subscrição do grupo de gestão. |
> | Ação | Microsoft.Management/managementGroups/subscriptions/write | Associa existente subscrição com o grupo de gestão. |
> | Ação | Microsoft.Management/managementGroups/write | Criar ou atualizar um grupo de gestão. |
> | Ação | Microsoft.Management/register/action | Registar a subscrição especificada com Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Concede acesso de leitura de dados para uma conta de mapas. |
> | Ação | Microsoft.Maps/accounts/delete | Elimine uma conta de mapas. |
> | Ação | Microsoft.Maps/accounts/eventGridFilters/delete | Eliminar um filtro do Event Grid |
> | Ação | Microsoft.Maps/accounts/eventGridFilters/read | Obter um filtro do Event Grid |
> | Ação | Microsoft.Maps/accounts/eventGridFilters/write | Criar ou atualizar um filtro do Event Grid |
> | Ação | Microsoft.Maps/accounts/listKeys/action | Listar chaves de conta de mapas |
> | Ação | Microsoft.Maps/accounts/read | Obter uma conta de mapas. |
> | Ação | Microsoft.Maps/accounts/regenerateKey/action | Gerar nova chave de principal ou secundário de conta de mapas |
> | Ação | Microsoft.Maps/accounts/write | Criar ou atualizar uma conta de mapas. |
> | Ação | Microsoft.Maps/register/action | Registar o fornecedor |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Devolve um Contrato. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Aceita um contrato assinado. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importa uma imagem para o ACR de utilizador final. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Devolve uma configuração. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Guarda uma configuração. |
> | Ação | Microsoft.Marketplace/register/action | Regista o fornecedor de recursos Microsoft.Marketplace na subscrição. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/delete | Faz uma operação de eliminação num recurso de serviço de dev clássicos. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Obtém um dev clássicos chaves de gestão de recursos de serviço. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Obtém o início de sessão único na URL para um serviço de dev clássicos. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/read | Faz uma operação de obtenção de um serviço de dev clássicos. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Gera um chaves de gestão de recursos de serviço de dev clássicos. |
> | Ação | Microsoft.MarketplaceApps/Operations/read | Operações para todos os tipos de recursos de leitura. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Cancelar um acordo para um item do mercado especificado |
> | Ação | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Devolver um acordo para um item do mercado especificado |
> | Ação | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Assinar um contrato para um item do mercado especificado |
> | Ação | Microsoft.MarketplaceOrdering/agreements/read | Devolver todos os contratos em determinada subscrição |
> | Ação | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Obter um acordo para um item de máquina virtual do mercado especificado |
> | Ação | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Inicie sessão ou cancelar um acordo para um item de máquina virtual do mercado especificado |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Media/checknameavailability/action | Verifica se um nome de conta de Media Services está disponível |
> | Ação | Microsoft.Media/locations/checkNameAvailability/action | Verifica se um nome de conta de Media Services está disponível |
> | Ação | Microsoft.Media/mediaservices/accountfilters/delete | Eliminar qualquer filtro de conta |
> | Ação | Microsoft.Media/mediaservices/accountfilters/read | Ler qualquer filtro de conta |
> | Ação | Microsoft.Media/mediaservices/accountfilters/write | Criar ou atualizar qualquer filtro de conta |
> | Ação | Microsoft.Media/mediaservices/assets/assetfilters/delete | Eliminar qualquer filtro de elemento |
> | Ação | Microsoft.Media/mediaservices/assets/assetfilters/read | Ler qualquer filtro de elemento |
> | Ação | Microsoft.Media/mediaservices/assets/assetfilters/write | Criar ou atualizar qualquer filtro de elemento |
> | Ação | Microsoft.Media/mediaservices/assets/delete | Eliminar qualquer recurso |
> | Ação | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Obter chave de encriptação ativo |
> | Ação | Microsoft.Media/mediaservices/assets/listContainerSas/action | URLs SAS de contentor de elemento de lista |
> | Ação | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Lista os localizadores de transmissão em fluxo para o recurso |
> | Ação | Microsoft.Media/mediaservices/assets/read | Ler qualquer recurso |
> | Ação | Microsoft.Media/mediaservices/assets/write | Criar ou atualizar qualquer recurso |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Eliminar qualquer política de chave de conteúdo |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Obter as propriedades de política de segredos |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/read | Ler qualquer política de chave de conteúdo |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/write | Criar ou atualizar qualquer política de chave de conteúdo |
> | Ação | Microsoft.Media/mediaservices/delete | Eliminar qualquer conta de Media Services |
> | Ação | Microsoft.Media/mediaservices/eventGridFilters/delete | Eliminar qualquer filtro do Event Grid |
> | Ação | Microsoft.Media/mediaservices/eventGridFilters/read | Ler qualquer filtro do Event Grid |
> | Ação | Microsoft.Media/mediaservices/eventGridFilters/write | Criar ou atualizar qualquer filtro do Event Grid |
> | Ação | Microsoft.Media/mediaservices/liveEventOperations/read | Ler qualquer operação de evento em direto |
> | Ação | Microsoft.Media/mediaservices/liveEvents/delete | Eliminar qualquer evento em direto |
> | Ação | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Eliminar quaisquer dados em direto |
> | Ação | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Ler quaisquer dados em direto |
> | Ação | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Criar ou atualizar qualquer saída em direto |
> | Ação | Microsoft.Media/mediaservices/liveEvents/read | Ler qualquer evento em direto |
> | Ação | Microsoft.Media/mediaservices/liveEvents/reset/action | Repor qualquer operação de evento em direto |
> | Ação | Microsoft.Media/mediaservices/liveEvents/start/action | Iniciar a qualquer operação de evento em direto |
> | Ação | Microsoft.Media/mediaservices/liveEvents/stop/action | Parar qualquer operação de evento em direto |
> | Ação | Microsoft.Media/mediaservices/liveEvents/write | Criar ou atualizar qualquer evento em direto |
> | Ação | Microsoft.Media/mediaservices/liveOutputOperations/read | Ler qualquer operação de saída em direto |
> | Ação | Microsoft.Media/mediaservices/read | Ler qualquer conta de Media Services |
> | Ação | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Ler qualquer operação de ponto final de transmissão em fluxo |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/delete | Eliminar qualquer ponto de final de transmissão em fluxo |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/read | Ler qualquer ponto de final de transmissão em fluxo |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Dimensionar qualquer operação de ponto final de transmissão em fluxo |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Iniciar qualquer operação de ponto final de transmissão em fluxo |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Parar qualquer operação de ponto final de transmissão em fluxo |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/write | Criar ou atualizar qualquer ponto de final de transmissão em fluxo |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/delete | Eliminar qualquer localizador de transmissão em fluxo |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Listar chaves de conteúdo |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Caminhos de lista |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/read | Ler qualquer localizador de transmissão em fluxo |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/write | Criar ou atualizar qualquer localizador de transmissão em fluxo |
> | Ação | Microsoft.Media/mediaservices/streamingPolicies/delete | Eliminar qualquer política de transmissão em fluxo |
> | Ação | Microsoft.Media/mediaservices/streamingPolicies/read | Ler qualquer política de transmissão em fluxo |
> | Ação | Microsoft.Media/mediaservices/streamingPolicies/write | Criar ou atualizar qualquer política de transmissão em fluxo |
> | Ação | Microsoft.Media/mediaservices/syncStorageKeys/action | Sincronizar as chaves de armazenamento para uma conta de armazenamento do Azure anexada |
> | Ação | Microsoft.Media/mediaservices/transforms/delete | Eliminar qualquer transformação |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Cancelar Tarefa |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/delete | Eliminar todas as tarefas |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/read | Ler qualquer tarefa |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/write | Criar ou atualizar todas as tarefas |
> | Ação | Microsoft.Media/mediaservices/transforms/read | Ler qualquer transformação |
> | Ação | Microsoft.Media/mediaservices/transforms/write | Criar ou atualizar qualquer transformação |
> | Ação | Microsoft.Media/mediaservices/write | Criar ou atualizar qualquer conta de Media Services |
> | Ação | Microsoft.Media/operations/read | Obter operações disponíveis |
> | Ação | Microsoft.Media/register/action | Regista a subscrição para o fornecedor de recursos de serviços de multimédia e permite a criação de contas de serviços de multimédia |
> | Ação | Microsoft.Media/unregister/action | Anula o registo da subscrição para o fornecedor de recursos de serviços de multimédia |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Migrate/locations/assessmentOptions/read | Obtém as opções de avaliação que estão disponíveis na localização especificada |
> | Ação | Microsoft.Migrate/locations/checknameavailability/action | Verifica a disponibilidade do nome do recurso para a subscrição fornecida na localização especificada |
> | Ação | Microsoft.Migrate/migrateprojects/read | Obtém as propriedades do projeto de migração. |
> | Ação | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Obtém a configuração da solução do projeto de migração |
> | Ação | Microsoft.Migrate/migrateprojects/solutions/read | Obtém as propriedades da solução do projeto de migração. |
> | Ação | Microsoft.Migrate/Operations/read | Lista as operações disponíveis no fornecedor de recursos Microsoft.Migrate |
> | Ação | Microsoft.Migrate/projects/assessments/read | Lista avaliações dentro de um projeto |
> | Ação | Microsoft.Migrate/projects/delete | Elimina o projeto |
> | Ação | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Obter as propriedades de uma máquina avaliada |
> | Ação | Microsoft.Migrate/projects/groups/assessments/delete | Elimina uma avaliação |
> | Ação | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Transfere um URL de um relatório de avaliação |
> | Ação | Microsoft.Migrate/projects/groups/assessments/read | Obtém as propriedades de uma avaliação |
> | Ação | Microsoft.Migrate/projects/groups/assessments/write | Cria uma nova avaliação ou atualiza uma existente |
> | Ação | Microsoft.Migrate/projects/groups/delete | Elimina um grupo |
> | Ação | Microsoft.Migrate/projects/groups/read | Obter as propriedades de um grupo |
> | Ação | Microsoft.Migrate/projects/groups/write | Cria um novo grupo ou atualiza um grupo existente |
> | Ação | Microsoft.Migrate/projects/keys/action | Obtém as chaves partilhadas para o projeto |
> | Ação | Microsoft.Migrate/projects/machines/read | Obter as propriedades de um computador |
> | Ação | Microsoft.Migrate/projects/read | Obtém as propriedades de um projeto |
> | Ação | Microsoft.Migrate/projects/write | Cria um novo projeto ou atualiza um existente |
> | Ação | Microsoft.Migrate/register/action | Regista a Subscrição no fornecedor de recursos Microsoft.Migrate |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.MixedReality/register/action | Regista uma subscrição para o fornecedor de recursos de realidade mista. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Eliminar as âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Detetar próximas âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obter propriedades de âncoras espaciais |
> | Ação | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico para Microsoft.MixedReality/SpatialAnchorsAccounts |
> | Ação | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição de diagnóstico para Microsoft.MixedReality/SpatialAnchorsAccounts |
> | Ação | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para Microsoft.MixedReality/SpatialAnchorsAccounts |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localize as âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Atualizar propriedades de âncoras espaciais |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.NetApp/locations/operationresults/read | Lê um recurso de resultado da operação. |
> | Ação | Microsoft.NetApp/locations/read | Leituras uma disponibilidade verifique recursos. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Elimina um recurso de conjunto. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/read | Lê um recurso de conjunto. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Elimina um recurso de volume. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Lê um recurso de destino de montagem. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Lê um recurso de volume. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Elimina um recurso de instantâneo. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Lê um recurso de instantâneo. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Escreve um recurso de instantâneo. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Escreve um recurso de volume. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/write | Escreve um recurso de conjunto. |
> | Ação | Microsoft.NetApp/netAppAccounts/delete | Elimina um recurso de conta. |
> | Ação | Microsoft.NetApp/netAppAccounts/read | Lê um recurso de conta. |
> | Ação | Microsoft.NetApp/netAppAccounts/write | Escreve um recurso de conta. |
> | Ação | Microsoft.NetApp/Operations/read | Lê uma operação de recursos. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Obter o Gateway de aplicação disponíveis cabeçalhos de pedido |
> | Ação | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Obter o cabeçalho de resposta do Gateway de aplicação disponíveis |
> | Ação | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Obter as variáveis de servidor disponíveis do Gateway de aplicação |
> | Ação | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Ssl de Gateway de aplicação predefinidas política |
> | Ação | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Opções de Ssl de disponíveis de Gateway de aplicação |
> | Ação | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Obtém conjuntos de regras disponível do Waf de Gateway de aplicação |
> | Ação | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Associa um conjunto de endereços de back-end de gateway de aplicação. Não. o alerta. |
> | Ação | Microsoft.Network/applicationGateways/backendhealth/action | Obtém um Estado de funcionamento de back-end de gateway de aplicação |
> | Ação | Microsoft.Network/applicationGateways/delete | Elimina um gateway de aplicação |
> | Ação | Microsoft.Network/applicationGateways/read | Obtém um gateway de aplicação |
> | Ação | Microsoft.Network/applicationGateways/start/action | Inicia um gateway de aplicação |
> | Ação | Microsoft.Network/applicationGateways/stop/action | Interrompe um gateway de aplicação |
> | Ação | Microsoft.Network/applicationGateways/write | Cria um gateway de aplicação ou atualiza um gateway de aplicação |
> | Ação | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Elimina uma política de WAF do Gateway de aplicação |
> | Ação | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Obtém uma política de WAF do Gateway de aplicação |
> | Ação | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Cria uma política de WAF do Gateway de aplicação ou atualiza uma política de WAF do Gateway de aplicação |
> | Ação | Microsoft.Network/applicationSecurityGroups/delete | Elimina um grupo de segurança de aplicações |
> | Ação | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Associa uma configuração de IP para grupos de segurança do aplicativo. Não. o alerta. |
> | Ação | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Associa uma regra de segurança para grupos de segurança do aplicativo. Não. o alerta. |
> | Ação | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Listas de configurações de IP no ApplicationSecurityGroup |
> | Ação | Microsoft.Network/applicationSecurityGroups/read | Obtém um ID de grupo de segurança de aplicações. |
> | Ação | Microsoft.Network/applicationSecurityGroups/write | Cria um grupo de segurança de aplicações ou atualiza um grupo de segurança de aplicação existente. |
> | Ação | Microsoft.Network/azureFirewallFqdnTags/read | Obtém as etiquetas FQDN de Firewall do Azure |
> | Ação | Microsoft.Network/azurefirewalls/delete | Eliminar a Firewall do Azure |
> | Ação | Microsoft.Network/azurefirewalls/read | Obter o Firewall do Azure |
> | Ação | Microsoft.Network/azurefirewalls/write | Cria ou atualiza uma Firewall do Azure |
> | Ação | Microsoft.Network/bastionHosts/delete | Elimina o anfitrião de bastião |
> | Ação | Microsoft.Network/bastionHosts/read | Obtém o anfitrião de bastião |
> | Ação | Microsoft.Network/bastionHosts/write | Criar ou atualizar o anfitrião de bastião |
> | Ação | Microsoft.Network/bgpServiceCommunities/read | Obter Comunidades de Bgp de serviço |
> | Ação | Microsoft.Network/checkFrontDoorNameAvailability/action | Verifica se um nome de porta de entrada está disponível |
> | Ação | Microsoft.Network/checkTrafficManagerNameAvailability/action | Verifica a disponibilidade de um nome DNS do Gestor de tráfego relativo. |
> | Ação | Microsoft.Network/connections/delete | Elimina o VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/read | Obtém o VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/revoke/action | Marca um Estado de ligação de Expressroute como revogadas |
> | Ação | Microsoft.Network/connections/sharedkey/action | Obter VirtualNetworkGatewayConnection SharedKey |
> | Ação | Microsoft.Network/connections/sharedKey/read | Obtém o VirtualNetworkGatewayConnection SharedKey |
> | Ação | Microsoft.Network/connections/sharedKey/write | Cria ou atualiza uma existente SharedKey de VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Obtém a configuração do dispositivo de Vpn de VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/write | Cria ou atualiza uma existente VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/ddosCustomPolicies/delete | Elimina um DDoS de política personalizada |
> | Ação | Microsoft.Network/ddosCustomPolicies/read | Obtém um DDoS personalizados a definição de definição de política |
> | Ação | Microsoft.Network/ddosCustomPolicies/write | Cria uma política personalizada de DDoS ou atualiza uma política de DDoS personalizado existente |
> | Ação | Microsoft.Network/ddosProtectionPlans/delete | Elimina um plano de proteção DDoS |
> | Ação | Microsoft.Network/ddosProtectionPlans/join/action | Associa um plano de proteção contra DDoS. Não. o alerta. |
> | Ação | Microsoft.Network/ddosProtectionPlans/read | Obtém um plano de proteção DDoS |
> | Ação | Microsoft.Network/ddosProtectionPlans/write | Cria um plano de proteção de DDoS ou atualiza um plano de proteção de DDoS  |
> | Ação | Microsoft.Network/dnsoperationresults/read | Obtém os resultados de uma operação de DNS |
> | Ação | Microsoft.Network/dnsoperationstatuses/read | Obtém o estado de uma operação de DNS  |
> | Ação | Microsoft.Network/dnszones/A/delete | Remover o conjunto de registos de um determinado nome e tipo A partir de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/A/read | Obtenha o conjunto de registos do tipo "A", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o valor de TTL, marcas e etag. |
> | Ação | Microsoft.Network/dnszones/A/write | Criar ou atualizar um conjunto de registos do tipo "A" dentro de uma zona DNS. Os registos especificados substituirá os atuais registos no conjunto de registos. |
> | Ação | Microsoft.Network/dnszones/AAAA/delete | Remover o conjunto de registos de um determinado nome e tipo 'AAAA' de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/AAAA/read | Obtenha o conjunto de registos do tipo 'AAAA', no formato JSON. O conjunto de registos contém uma lista de registos, bem como o valor de TTL, marcas e etag. |
> | Ação | Microsoft.Network/dnszones/AAAA/write | Criar ou atualizar um conjunto de registos do tipo 'AAAA' dentro de uma zona DNS. Os registos especificados substituirá os atuais registos no conjunto de registos. |
> | Ação | Microsoft.Network/dnszones/all/read | Obtém conjuntos de registos DNS em todos os tipos |
> | Ação | Microsoft.Network/dnszones/CAA/delete | Remover o conjunto de registos de um determinado nome e tipo CAA de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/CAA/read | Obtenha o conjunto de registos do tipo "CAA", no formato JSON. O conjunto de registos contém o valor de TTL, marcas e etag. |
> | Ação | Microsoft.Network/dnszones/CAA/write | Criar ou atualizar um conjunto de registos do tipo "CAA" dentro de uma zona DNS. Os registos especificados substituirá os atuais registos no conjunto de registos. |
> | Ação | Microsoft.Network/dnszones/CNAME/delete | Remover o conjunto de registos de um determinado nome e tipo CNAME de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/CNAME/read | Obtenha o conjunto de registos do tipo "CNAME", no formato JSON. O conjunto de registos contém o valor de TTL, marcas e etag. |
> | Ação | Microsoft.Network/dnszones/CNAME/write | Criar ou atualizar um conjunto de registos do tipo "CNAME" dentro de uma zona DNS. Os registos especificados substituirá os atuais registos no conjunto de registos. |
> | Ação | Microsoft.Network/dnszones/delete | Elimine a zona DNS, no formato JSON. As propriedades de zona incluem marcas, etag, numberOfRecordSets e maxNumberOfRecordSets. |
> | Ação | Microsoft.Network/dnszones/MX/delete | Remover o conjunto de registos de um determinado nome e tipo "MX" de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/MX/read | Obtenha o conjunto de registos do tipo "MX", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o valor de TTL, marcas e etag. |
> | Ação | Microsoft.Network/dnszones/MX/write | Criar ou atualizar um conjunto de registos do tipo "MX" dentro de uma zona DNS. Os registos especificados substituirá os atuais registos no conjunto de registos. |
> | Ação | Microsoft.Network/dnszones/NS/delete | Elimina o conjunto de registos de DNS do tipo NS |
> | Ação | Microsoft.Network/dnszones/NS/read | Obtém o conjunto de registos de DNS do tipo NS |
> | Ação | Microsoft.Network/dnszones/NS/write | Cria ou atualiza o conjunto de registos de DNS do tipo NS |
> | Ação | Microsoft.Network/dnszones/PTR/delete | Remover o conjunto de registos de um determinado nome e tipo PTR de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/PTR/read | Obtenha o conjunto de registos do tipo "PTR", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o valor de TTL, marcas e etag. |
> | Ação | Microsoft.Network/dnszones/PTR/write | Criar ou atualizar um conjunto de registos do tipo "PTR" dentro de uma zona DNS. Os registos especificados substituirá os atuais registos no conjunto de registos. |
> | Ação | Microsoft.Network/dnszones/read | Obtenha a zona DNS, no formato JSON. As propriedades de zona incluem marcas, etag, numberOfRecordSets e maxNumberOfRecordSets. Tenha em atenção que este comando não obter conjuntos de registos contidos na zona. |
> | Ação | Microsoft.Network/dnszones/recordsets/read | Obtém conjuntos de registos DNS em todos os tipos |
> | Ação | Microsoft.Network/dnszones/SOA/read | Obtém o conjunto de registos de DNS do tipo SOA |
> | Ação | Microsoft.Network/dnszones/SOA/write | Cria ou atualiza o conjunto de registos de DNS do tipo SOA |
> | Ação | Microsoft.Network/dnszones/SRV/delete | Remover o conjunto de registos de um determinado nome e tipo SRV de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/SRV/read | Obtenha o conjunto de registos do tipo "SRV", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o valor de TTL, marcas e etag. |
> | Ação | Microsoft.Network/dnszones/SRV/write | Criar ou atualizar conjunto de registos do tipo SRV |
> | Ação | Microsoft.Network/dnszones/TXT/delete | Remover o conjunto de registos de um determinado nome e tipo TXT de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/TXT/read | Obtenha o conjunto de registos do tipo "TXT", no formato JSON. O conjunto de registos contém uma lista de registos, bem como o valor de TTL, marcas e etag. |
> | Ação | Microsoft.Network/dnszones/TXT/write | Criar ou atualizar um conjunto de registos do tipo "TXT" dentro de uma zona DNS. Os registos especificados substituirá os atuais registos no conjunto de registos. |
> | Ação | Microsoft.Network/dnszones/write | Criar ou atualizar uma zona DNS dentro de um grupo de recursos.  Utilizado para atualizar as etiquetas num recurso de zona DNS. Tenha em atenção que este comando não pode ser utilizado para criar ou atualizar conjuntos de registos na zona. |
> | Ação | Microsoft.Network/expressRouteCircuits/authorizations/delete | Elimina uma autorização de circuito do Expressroute |
> | Ação | Microsoft.Network/expressRouteCircuits/authorizations/read | Obtém uma autorização de circuito do Expressroute |
> | Ação | Microsoft.Network/expressRouteCircuits/authorizations/write | Cria ou atualiza uma autorização de circuito do Expressroute existente |
> | Ação | Microsoft.Network/expressRouteCircuits/delete | Elimina um circuito do Expressroute |
> | Ação | Microsoft.Network/expressRouteCircuits/join/action | Associa um circuito do Expressroute. Não. o alerta. |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Obtém um ArpTable de Peering do circuito do Expressroute |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Elimina uma ligação do circuito do Expressroute |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Obtém uma ligação do circuito do Expressroute |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Cria ou atualiza um recurso de ligação do circuito do Expressroute existente |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/delete | Elimina um Peering de circuito do Expressroute |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Obtém a ligação do circuito Express Route de ponto a ponto |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/read | Obtém um Peering de circuito do Expressroute |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Obtém um RouteTable de Peering do circuito do Expressroute |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Obtém um resumo de RouteTable do Peering de circuito do Expressroute |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Obtém um Peering de circuito do Expressroute Stat |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/write | Cria ou atualiza um Peering de circuito do Expressroute existente |
> | Ação | Microsoft.Network/expressRouteCircuits/read | Obter um circuito do Expressroute |
> | Ação | Microsoft.Network/expressRouteCircuits/stats/read | Obtém uma estatística de circuito do Expressroute |
> | Ação | Microsoft.Network/expressRouteCircuits/write | Cria ou atualiza um circuito do Expressroute existente |
> | Ação | Microsoft.Network/expressRouteCrossConnections/join/action | Associações uma via expressa cruzam ligação. Não. o alerta. |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Obtém uma via expressa entre a tabela de Arp Peering da ligação |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Elimina uma via expressa cruzada ligação Peering |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/read | Obtém uma via expressa cruzada ligação Peering |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Obtém uma via expressa cruzada ligação Peering da tabela de rotas |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Obtém uma via expressa cruzada resumo de tabela da rota de Peering da ligação |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/write | Cria um Cross ligação Peering do Expressroute ou atualiza uma existente em várias ligação Peering do Expressroute |
> | Ação | Microsoft.Network/expressRouteCrossConnections/read | Obter o Express Route cruzada de ligação |
> | Ação | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Elimina uma ligação de Expressroute |
> | Ação | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Obtém uma ligação de Expressroute |
> | Ação | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Cria uma ligação Expressroute ou atualiza uma existente ligação de Expressroute |
> | Ação | Microsoft.Network/expressRouteGateways/join/action | Associa um Gateway do Expressroute. Não. o alerta. |
> | Ação | Microsoft.Network/expressRouteGateways/read | Obter o Gateway do Express Route |
> | Ação | Microsoft.Network/expressRoutePorts/delete | Elimina ExpressRoutePorts |
> | Ação | Microsoft.Network/expressRoutePorts/join/action | Associa as portas de Express Route. Não. o alerta. |
> | Ação | Microsoft.Network/expressRoutePorts/links/read | Obtém ExpressRouteLink |
> | Ação | Microsoft.Network/expressRoutePorts/read | Obtém ExpressRoutePorts |
> | Ação | Microsoft.Network/expressRoutePorts/write | Cria ou atualiza ExpressRoutePorts |
> | Ação | Microsoft.Network/expressRoutePortsLocations/read | Get Express Route portas locais |
> | Ação | Microsoft.Network/expressRouteServiceProviders/read | Obtém os fornecedores de serviços de Express Route |
> | Ação | Microsoft.Network/frontDoors/backendPools/delete | Elimina um conjunto de back-end |
> | Ação | Microsoft.Network/frontDoors/backendPools/read | Obtém um conjunto de back-end |
> | Ação | Microsoft.Network/frontDoors/backendPools/write | Cria ou atualiza um conjunto de back-end |
> | Ação | Microsoft.Network/frontDoors/delete | Elimina uma porta de entrada |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/delete | Elimina um ponto de extremidade de front-end |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Desativa o HTTPS num ponto de extremidade de front-end |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Permite que o HTTPS num ponto de extremidade de front-end |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/read | Obtém um ponto de extremidade de front-end |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/write | Cria ou atualiza um ponto de extremidade de front-end |
> | Ação | Microsoft.Network/frontDoors/healthProbeSettings/delete | Elimina as definições de sonda de estado de funcionamento |
> | Ação | Microsoft.Network/frontDoors/healthProbeSettings/read | Obtém as definições de sonda de estado de funcionamento |
> | Ação | Microsoft.Network/frontDoors/healthProbeSettings/write | Cria ou atualiza as definições de sonda de estado de funcionamento |
> | Ação | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Cria ou atualiza as definições de balanceamento de carga |
> | Ação | Microsoft.Network/frontDoors/loadBalancingSettings/read | Obtém o balanceamento de carga definições |
> | Ação | Microsoft.Network/frontDoors/loadBalancingSettings/write | Cria ou atualiza as definições de balanceamento de carga |
> | Ação | Microsoft.Network/frontDoors/purge/action | Limpar o conteúdo em cache a partir de uma porta de entrada |
> | Ação | Microsoft.Network/frontDoors/read | Obtém uma porta de entrada |
> | Ação | Microsoft.Network/frontDoors/routingRules/delete | Elimina uma regra de encaminhamento |
> | Ação | Microsoft.Network/frontDoors/routingRules/read | Obtém uma regra de encaminhamento |
> | Ação | Microsoft.Network/frontDoors/routingRules/write | Cria ou atualiza uma regra de encaminhamento |
> | Ação | Microsoft.Network/frontDoors/validateCustomDomain/action | Valida um ponto de extremidade de front-end para uma porta de entrada |
> | Ação | Microsoft.Network/frontDoors/write | Cria ou atualiza uma porta de entrada |
> | Ação | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Elimina uma política de Firewall de aplicações Web |
> | Ação | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Obtém uma política de Firewall de aplicações Web |
> | Ação | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Cria ou atualiza uma política de Firewall de aplicações Web |
> | Ação | Microsoft.Network/interfaceEndpoints/delete | Elimina um recurso de ponto final de interface. |
> | Ação | Microsoft.Network/interfaceEndpoints/read | Obtém um recurso de ponto final de interface. |
> | Ação | Microsoft.Network/interfaceEndpoints/write | Cria um novo ponto final da interface ou atualiza um ponto de extremidade de interface existentes. |
> | Ação | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Associa um conjunto de endereços de back-end de Balanceador de carga. Não. o alerta. |
> | Ação | Microsoft.Network/loadBalancers/backendAddressPools/read | Obtém uma definição de conjunto de endereços de back-end de Balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/delete | Elimina um balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Associa uma configuração de IP de front-end de Balanceador de carga. Não. o alerta. |
> | Ação | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Obtém uma definição de configuração de IP de front-end de Balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatPools/join/action | É associado um balanceador de carga conjunto NAT de entrada. Não. o alerta. |
> | Ação | Microsoft.Network/loadBalancers/inboundNatPools/read | Obtém um balanceador de carga a definição do conjunto nat de entrada |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/delete | Elimina uma regra de nat de entrada do Balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Associa uma regra de nat de entrada do Balanceador de carga. Não. o alerta. |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/read | Obtém um balanceador de carga definição da regra nat de entrada |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/write | Cria uma regra de nat de entrada do Balanceador de carga ou atualiza uma regra nat de entrada do Balanceador de carga existente |
> | Ação | Microsoft.Network/loadBalancers/loadBalancingRules/read | Obtém uma definição de regra balanceamento do carregamento Balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/networkInterfaces/read | Obtém as referências a todas as interfaces de rede num Balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/outboundRules/read | Obtém uma definição de regra de saída do Balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/probes/join/action | Permite o uso de sondas do Balanceador de carga. Por exemplo, com esta propriedade healthProbe de permissão de dimensionamento de VMS conjunto pode referenciar a sonda. Não. o alerta. |
> | Ação | Microsoft.Network/loadBalancers/probes/read | Obtém uma sonda de Balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/read | Obtém uma definição de Balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/virtualMachines/read | Obtém as referências a todas as máquinas virtuais num Balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/write | Cria um balanceador de carga ou atualiza um balanceador de carga existente |
> | Ação | Microsoft.Network/localnetworkgateways/delete | Elimina o LocalNetworkGateway |
> | Ação | Microsoft.Network/localnetworkgateways/read | Obtém o LocalNetworkGateway |
> | Ação | Microsoft.Network/localnetworkgateways/write | Cria ou atualiza um LocalNetworkGateway existente |
> | Ação | Microsoft.Network/locations/availableDelegations/read | Obtém as delegações disponíveis |
> | Ação | Microsoft.Network/locations/bareMetalTenants/action | Aloca ou valida um inquilino do bare-Metal |
> | Ação | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Verifica o suporte de redes aceleradas |
> | Ação | Microsoft.Network/locations/checkDnsNameAvailability/read | Verifica se a etiqueta de dns está disponível na localização especificada |
> | Ação | Microsoft.Network/locations/operationResults/read | Obtém o resultado da operação de um async mensagem ou operação de eliminação |
> | Ação | Microsoft.Network/locations/operations/read | Obtém o recurso de operação que representa o estado de uma operação assíncrona |
> | Ação | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Obtém suporte tamanhos de máquinas virtuais |
> | Ação | Microsoft.Network/locations/usages/read | Obtém os recursos de métrica de utilização |
> | Ação | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Obtém uma lista de serviços de ponto final de rede virtuais disponíveis |
> | Ação | Microsoft.Network/networkIntentPolicies/delete | Elimina uma política de intenção de rede |
> | Ação | Microsoft.Network/networkIntentPolicies/read | Obtém uma descrição da intenção da política de rede |
> | Ação | Microsoft.Network/networkIntentPolicies/write | Cria uma política de intenção de rede ou atualiza uma política de intenção de rede existente |
> | Ação | Microsoft.Network/networkInterfaces/delete | Elimina uma interface de rede |
> | Ação | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Obter grupos de segurança de rede configurado na rede Interface da Vm |
> | Ação | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Obter a tabela de rotas configuradas na Interface de rede da Vm |
> | Ação | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Associa uma configuração de IP da Interface de rede. Não. o alerta. |
> | Ação | Microsoft.Network/networkInterfaces/ipconfigurations/read | Obtém uma definição de configuração de ip de interface de rede.  |
> | Ação | Microsoft.Network/networkInterfaces/join/action | Associa uma Máquina Virtual a uma interface de rede. Não. o alerta. |
> | Ação | Microsoft.Network/networkInterfaces/loadBalancers/read | Obtém todos os balanceadores de carga que a interface de rede faz parte do |
> | Ação | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Ação | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Elimina uma configuração de toque de Interface de rede. |
> | Ação | Microsoft.Network/networkInterfaces/tapConfigurations/read | Obtém uma configuração de toque de Interface de rede. |
> | Ação | Microsoft.Network/networkInterfaces/tapConfigurations/write | Cria uma configuração de toque de Interface de rede ou atualiza uma Interface de toque em configuração da rede existente. |
> | Ação | Microsoft.Network/networkInterfaces/write | Cria uma interface de rede ou atualiza uma interface de rede existente.  |
> | Ação | Microsoft.Network/networkProfiles/delete | Elimina um perfil de rede |
> | Ação | Microsoft.Network/networkProfiles/read | Obtém um perfil de rede |
> | Ação | Microsoft.Network/networkProfiles/removeContainers/action | Remove os contentores |
> | Ação | Microsoft.Network/networkProfiles/setContainers/action | Contentores de conjuntos |
> | Ação | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Define as Interfaces de rede do contentor |
> | Ação | Microsoft.Network/networkProfiles/write | Cria ou atualiza um perfil de rede |
> | Ação | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Obtém uma definição de regra de segurança padrão |
> | Ação | Microsoft.Network/networkSecurityGroups/delete | Elimina um grupo de segurança de rede |
> | Ação | Microsoft.Network/networkSecurityGroups/join/action | Associa um grupo de segurança de rede. Não. o alerta. |
> | Ação | Microsoft.Network/networkSecurityGroups/read | Obtém uma definição de grupo de segurança de rede |
> | Ação | Microsoft.Network/networkSecurityGroups/securityRules/delete | Elimina uma regra de segurança |
> | Ação | Microsoft.Network/networkSecurityGroups/securityRules/read | Obtém uma definição de regra de segurança |
> | Ação | Microsoft.Network/networkSecurityGroups/securityRules/write | Cria uma regra de segurança ou atualiza uma regra de segurança existente |
> | Ação | Microsoft.Network/networkSecurityGroups/write | Cria um grupo de segurança de rede ou atualiza um grupo de segurança de rede existente |
> | Ação | Microsoft.Network/networkWatchers/availableProvidersList/action | Devolve a internet disponível fornecedores de serviços para uma região do Azure especificada. |
> | Ação | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Devolve a classificação de latência relativa para a internet fornecedores de serviços de uma localização especificada para regiões do Azure. |
> | Ação | Microsoft.Network/networkWatchers/configureFlowLog/action | Configura o registo do fluxo para um recurso de destino. |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/delete | Elimina um Monitor de ligação |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Monitorizar a conectividade entre pontos finais especificados de consulta |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/read | Obter os detalhes de Monitor de ligação |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Comece a monitorizar a conectividade entre pontos finais especificados |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Parar/pausar, monitorizar a conectividade entre pontos finais especificados |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/write | Cria um Monitor de ligação |
> | Ação | Microsoft.Network/networkWatchers/connectivityCheck/action | Verifica a possibilidade de estabelecer uma ligação TCP direta de uma máquina virtual para um determinado ponto de extremidade incluindo outra VM ou um servidor remoto arbitrário. |
> | Ação | Microsoft.Network/networkWatchers/delete | Elimina um observador de rede |
> | Ação | Microsoft.Network/networkWatchers/ipFlowVerify/action | Devolve se o pacote é permitido ou negado para ou de um destino específico. |
> | Ação | Microsoft.Network/networkWatchers/lenses/delete | Elimina uma lente |
> | Ação | Microsoft.Network/networkWatchers/lenses/query/action | Monitorizar o tráfego de rede num ponto de extremidade especificado de consulta |
> | Ação | Microsoft.Network/networkWatchers/lenses/read | Obter os detalhes de lente |
> | Ação | Microsoft.Network/networkWatchers/lenses/start/action | Iniciar a monitorização de tráfego de rede num ponto de extremidade especificado |
> | Ação | Microsoft.Network/networkWatchers/lenses/stop/action | Parar/pausar a monitorizar o tráfego de rede num ponto de extremidade especificado |
> | Ação | Microsoft.Network/networkWatchers/lenses/write | Cria uma lente |
> | Ação | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnóstico de configuração de rede. |
> | Ação | Microsoft.Network/networkWatchers/nextHop/action | Para um destino especificado e o endereço IP de destino, retornar o tipo de próximo salto e esperamos junto de endereço IP. |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/delete | Elimina uma captura de pacotes |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Obtém informações sobre as propriedades e o estado de um recurso de captura de pacotes. |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/read | Obter a definição de captura de pacotes |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Pare a sessão de captura de pacote em execução. |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/write | Cria uma captura de pacotes |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/delete | Elimina um PingMesh |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/read | Obter os detalhes de PingMesh |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/start/action | Iniciar PingMesh entre VMs especificadas |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Parar PingMesh entre VMs especificadas |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/write | Cria um PingMesh |
> | Ação | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Monitorizar a conectividade entre pontos finais especificados de consulta de batch |
> | Ação | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Obtém o estado do fluxo de início de sessão um recurso. |
> | Ação | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Obtém o resultado de resolução de problemas de execução anterior ou atualmente em execução a operação de resolução de problemas. |
> | Ação | Microsoft.Network/networkWatchers/read | Obter a definição do observador de rede |
> | Ação | Microsoft.Network/networkWatchers/securityGroupView/action | Ver as regras de grupo de segurança de rede configurado e eficaz aplicadas numa VM. |
> | Ação | Microsoft.Network/networkWatchers/topology/action | Obtém uma vista de nível de rede de recursos e seus relacionamentos num grupo de recursos. |
> | Ação | Microsoft.Network/networkWatchers/troubleshoot/action | Inicia a resolução de problemas num recurso de sistema de rede no Azure. |
> | Ação | Microsoft.Network/networkWatchers/write | Cria um observador de rede ou atualiza um observador de rede existente |
> | Ação | Microsoft.Network/operations/read | Obter operações disponíveis |
> | Ação | Microsoft.Network/p2sVpnGateways/delete | Elimina um P2SVpnGateway. |
> | Ação | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Gerar o perfil de Vpn para P2SVpnGateway |
> | Ação | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Obtém um Estado de funcionamento da ligação de Vpn P2S para P2SVpnGateway |
> | Ação | Microsoft.Network/p2sVpnGateways/read | Obtém um P2SVpnGateway. |
> | Ação | Microsoft.Network/p2sVpnGateways/write | Coloca um P2SVpnGateway. |
> | Ação | Microsoft.Network/privateLinkServices/delete | Elimina um recurso de serviço de ligação privada. |
> | Ação | Microsoft.Network/privateLinkServices/interfaceEndpointConnections/delete | Elimina uma ligação de ponto final de interface. |
> | Ação | Microsoft.Network/privateLinkServices/interfaceEndpointConnections/read | Obtém uma definição de ligação de ponto final de interface. |
> | Ação | Microsoft.Network/privateLinkServices/interfaceEndpointConnections/write | Cria uma nova ligação de ponto final de interface ou atualiza uma ligação de ponto final de interface existente. |
> | Ação | Microsoft.Network/privateLinkServices/read | Obtém um recurso de serviço de ligação privada. |
> | Ação | Microsoft.Network/privateLinkServices/write | Cria um novo serviço de ligação privada ou atualiza um serviço de ligação privada existente. |
> | Ação | Microsoft.Network/publicIPAddresses/delete | Elimina um endereço Ip público. |
> | Ação | Microsoft.Network/publicIPAddresses/join/action | Associa um endereço ip público. Não. o alerta. |
> | Ação | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço ip público. |
> | Ação | Microsoft.Network/publicIPAddresses/write | Cria um endereço Ip público ou atualiza um endereço Ip público existente.  |
> | Ação | Microsoft.Network/publicIPPrefixes/delete | Elimina um prefixo de Ip público |
> | Ação | Microsoft.Network/publicIPPrefixes/join/action | Associa um PublicIPPrefix. Não. o alerta. |
> | Ação | Microsoft.Network/publicIPPrefixes/read | Obtém uma definição de prefixo de Ip público |
> | Ação | Microsoft.Network/publicIPPrefixes/write | Cria um prefixo de Ip público ou atualiza um prefixo de Ip público existente |
> | Ação | Microsoft.Network/register/action | Regista a subscrição |
> | Ação | Microsoft.Network/routeFilters/delete | Elimina uma definição de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/join/action | Associa um filtro de rota. Não. o alerta. |
> | Ação | Microsoft.Network/routeFilters/read | Obtém uma definição de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/routeFilterRules/delete | Elimina uma definição de regra de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/routeFilterRules/read | Obtém uma definição de regra de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/routeFilterRules/write | Cria uma regra de filtro de rota ou atualiza uma regra de filtro de rota existente |
> | Ação | Microsoft.Network/routeFilters/write | Cria um filtro de rota ou atualiza um filtro de rota existente |
> | Ação | Microsoft.Network/routeTables/delete | Elimina uma definição de tabela de rota |
> | Ação | Microsoft.Network/routeTables/join/action | Associa uma tabela de rotas. Não. o alerta. |
> | Ação | Microsoft.Network/routeTables/read | Obtém uma definição de tabela de rota |
> | Ação | Microsoft.Network/routeTables/routes/delete | Elimina uma definição de rota |
> | Ação | Microsoft.Network/routeTables/routes/read | Obtém uma definição de rota |
> | Ação | Microsoft.Network/routeTables/routes/write | Cria uma rota ou atualiza uma rota existente |
> | Ação | Microsoft.Network/routeTables/write | Cria uma tabela de rotas ou atualiza uma tabela de rota existente |
> | Ação | Microsoft.Network/securegateways/delete | Eliminar Gateway seguro |
> | Ação | Microsoft.Network/securegateways/read | Obter o Gateway seguro |
> | Ação | Microsoft.Network/securegateways/write | Cria ou atualiza um Gateway seguro |
> | Ação | Microsoft.Network/serviceEndpointPolicies/delete | Elimina uma política de ponto final de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/join/action | Junta-se uma política de ponto final de serviço. Não. o alerta. |
> | Ação | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Associa uma sub-rede para políticas de ponto final de serviço. Não. o alerta. |
> | Ação | Microsoft.Network/serviceEndpointPolicies/read | Obtém uma descrição de política de ponto final de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Elimina uma definição de política de ponto final de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Obtém uma descrição de definição de política de ponto final de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Cria uma definição de política de ponto final de serviço ou atualiza uma definição de política de ponto final de serviço existente |
> | Ação | Microsoft.Network/serviceEndpointPolicies/write | Cria uma política de ponto final de serviço ou atualiza uma política de ponto final de serviço existente |
> | Ação | Microsoft.Network/trafficManagerGeographicHierarchies/read | Obtém a hierarquia de Geographic Traffic Manager que contém regiões em que podem ser utilizados com o método de encaminhamento de tráfego geográfico |
> | Ação | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Elimina um ponto de final do Azure a partir de um perfil existente do Gestor de tráfego. O Gestor de tráfego deixará de encaminhamento de tráfego para o ponto de final do Azure eliminada. |
> | Ação | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Obtém um ponto de extremidade do Azure que pertence a um perfil do Gestor de tráfego, incluindo todas as propriedades do ponto de extremidade do Azure. |
> | Ação | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Adicionar um novo ponto de final do Azure num perfil existente do Gestor de tráfego ou atualizar as propriedades de um ponto de final do Azure existente daquele perfil do Gestor de tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/delete | Elimine o perfil do Gestor de tráfego. Associados ao perfil do Gestor de tráfego de todas as definições serão perdidas e o perfil já não pode ser utilizado para encaminhar o tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Elimina um ponto final externo a partir de um perfil existente do Gestor de tráfego. O Gestor de tráfego deixará de encaminhamento de tráfego para o ponto final externo foi eliminada. |
> | Ação | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Obtém um ponto final externo que pertence a um perfil do Gestor de tráfego, incluindo todas as propriedades do ponto de extremidade externo. |
> | Ação | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Adicionar um novo ponto final externo num perfil existente do Gestor de tráfego ou atualizar as propriedades de um ponto final externo existente daquele perfil do Gestor de tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Obtém o mapa térmico do Gestor de tráfego para o perfil do Traffic Manager determinado que contém os dados de contagens e latência de consulta por IP de origem e de localização. |
> | Ação | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Elimina um ponto de extremidade aninhada de um perfil existente do Gestor de tráfego. O Gestor de tráfego deixará de encaminhamento de tráfego para o ponto de extremidade aninhados eliminados. |
> | Ação | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Obtém um ponto de extremidade aninhados, que pertence a um perfil do Gestor de tráfego, incluindo todas as propriedades do ponto de extremidade aninhados. |
> | Ação | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Adicionar um novo ponto de final aninhados num perfil existente do Gestor de tráfego ou atualizar as propriedades de um aninhados ponto final existente daquele perfil do Gestor de tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/read | Obter a configuração de perfil do Gestor de tráfego. Isto inclui definições de DNS, definições de encaminhamento de tráfego, as definições de monitorização do ponto final e a lista de pontos de extremidade encaminhado por este perfil do Gestor de tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/write | Criar um perfil do Gestor de tráfego ou modificar a configuração de um perfil existente do Gestor de tráfego.<br>Isto inclui ativar ou desativar um perfil e modificar as definições de DNS, definições de encaminhamento de tráfego ou as definições de monitorização do ponto final.<br>Pontos finais encaminhados pelo perfil do Gestor de tráfego podem ser adicionados, removidos, ativados ou desativados. |
> | Ação | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Elimina a chave de nível de subscrição utilizada para a recolha de métricas de utilizador em tempo real. |
> | Ação | Microsoft.Network/trafficManagerUserMetricsKeys/read | Obtém a chave de nível de subscrição utilizada para a recolha de métricas de utilizador em tempo real. |
> | Ação | Microsoft.Network/trafficManagerUserMetricsKeys/write | Cria uma nova chave de nível de assinatura a ser utilizado para a recolha de métricas de utilizador em tempo real. |
> | Ação | Microsoft.Network/unregister/action | Anula o registo da subscrição |
> | Ação | Microsoft.Network/virtualHubs/delete | Elimina um Hub Virtual |
> | Ação | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Elimina um HubVirtualNetworkConnection |
> | Ação | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Obter um HubVirtualNetworkConnection |
> | Ação | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Criar ou atualizar um HubVirtualNetworkConnection |
> | Ação | Microsoft.Network/virtualHubs/read | Obter um Hub Virtual |
> | Ação | Microsoft.Network/virtualHubs/write | Criar ou atualizar um Hub Virtual |
> | Ação | microsoft.network/virtualnetworkgateways/connections/read | Obter VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/virtualNetworkGateways/delete | Elimina um virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Gerar o pacote do VpnClient para virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Gerar o pacote de VpnProfile para o VirtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Obtém virtualNetworkGateway anunciados rotas |
> | Ação | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Obtém o estado de ponto a ponto do bgp virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Obtém as rotas aprendida de virtualnetworkgateway |
> | Ação | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Obter por Estado de funcionamento de ligação de cliente Vpn para o VirtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Obter Vpnclient Ipsec parâmetros para o cliente de VirtualNetworkGateway P2S. |
> | Ação | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Obtém o URL de um pacote de perfil do cliente de vpn gerado |
> | Ação | Microsoft.Network/virtualNetworkGateways/read | Obtém um VirtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/reset/action | Repõe um virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Repor a chave partilhada do Vpnclient para cliente VirtualNetworkGateway P2S. |
> | Ação | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Defina parâmetros para o cliente de VirtualNetworkGateway P2S de Vpnclient Ipsec. |
> | Ação | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Suporte de listas de dispositivos Vpn |
> | Ação | Microsoft.Network/virtualNetworkGateways/write | Cria ou atualiza um VirtualNetworkGateway |
> | Ação | Microsoft.Network/virtualNetworks/BastionHosts/action | Obtém as referências de anfitrião de bastião numa rede Virtual. |
> | Ação | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Verifique se o endereço Ip está disponível na rede virtual especificada |
> | Ação | Microsoft.Network/virtualNetworks/delete | Elimina uma rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/peer/action | Elementos de uma rede virtual com outra rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/subnets/delete | Elimina uma sub-rede de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/subnets/join/action | Junta-se a uma rede virtual. Não. o alerta. |
> | Ação | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Associa o recurso, como a conta de armazenamento ou base de dados SQL a uma sub-rede. Não. o alerta. |
> | Ação | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Prepara uma sub-rede ao aplicar políticas de rede necessários |
> | Ação | Microsoft.Network/virtualNetworks/subnets/read | Obtém uma definição de sub-rede de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Obtém as referências a todas as máquinas virtuais na sub-rede de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/subnets/write | Cria uma sub-rede de rede virtual ou atualiza uma sub-rede de rede virtual existente |
> | Ação | Microsoft.Network/virtualNetworks/usages/read | Obter as utilizações IP para cada sub-rede da rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualMachines/read | Obtém as referências a todas as máquinas virtuais numa rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Elimina um peering de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Obtém uma definição de peering de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Cria um peering de rede virtual ou atualiza um peering de rede virtual existente |
> | Ação | Microsoft.Network/virtualNetworks/write | Cria uma rede virtual ou atualiza uma rede virtual existente |
> | Ação | Microsoft.Network/virtualNetworkTaps/delete | Eliminar o Tap da rede Virtual |
> | Ação | Microsoft.Network/virtualNetworkTaps/join/action | Associa um toque de rede virtual. Não. o alerta. |
> | Ação | Microsoft.Network/virtualNetworkTaps/read | Obter o Tap da rede Virtual |
> | Ação | Microsoft.Network/virtualNetworkTaps/write | Criar ou atualizar o Tap da rede Virtual |
> | Ação | Microsoft.Network/virtualWans/delete | Elimina uma Virtual Wan |
> | Ação | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Elimina um P2SVpnServerConfiguration Wan virtual |
> | Ação | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Obtém um P2SVpnServerConfiguration Wan virtual |
> | Ação | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Cria um P2SVpnServerConfiguration Wan virtual ou atualiza uma existente P2SVpnServerConfiguration de Wan virtual |
> | Ação | Microsoft.Network/virtualWans/read | Obtenção de um Virtual Wan |
> | Ação | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Obtém suporte VirtualWan fornecedores de segurança. |
> | Ação | Microsoft.Network/virtualWans/virtualHubs/read | Obtém todos os Hubs de Virtual que façam referência a uma Wan Virtual. |
> | Ação | Microsoft.Network/virtualwans/vpnconfiguration/action | Obtém uma configuração de Vpn |
> | Ação | Microsoft.Network/virtualWans/vpnSites/read | Obtém todos os Sites de VPN que fazem referência a uma Wan Virtual. |
> | Ação | Microsoft.Network/virtualWans/write | Criar ou atualizar uma Wan Virtual |
> | Ação | Microsoft.Network/vpnGateways/delete | Elimina um VpnGateway. |
> | Ação | microsoft.network/vpngateways/listvpnconnectionshealth/action | Obtém o estado de funcionamento de ligação para todos ou um subconjunto de ligações num VpnGateway |
> | Ação | Microsoft.Network/vpnGateways/read | Obtém um VpnGateway. |
> | Ação | microsoft.network/vpngateways/reset/action | Repõe um VpnGateway |
> | Ação | microsoft.network/vpnGateways/vpnConnections/delete | Elimina um VpnConnection. |
> | Ação | microsoft.network/vpnGateways/vpnConnections/read | Obtém um VpnConnection. |
> | Ação | microsoft.network/vpnGateways/vpnConnections/write | Coloca um VpnConnection. |
> | Ação | Microsoft.Network/vpnGateways/write | Coloca um VpnGateway. |
> | Ação | Microsoft.Network/vpnsites/delete | Elimina um recurso de Vpn de Site. |
> | Ação | Microsoft.Network/vpnsites/read | Obtém um recurso de Vpn de Site. |
> | Ação | Microsoft.Network/vpnsites/write | Cria ou atualiza um recurso de Vpn de Site. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Verifica se determinado nome de recurso do Espaço de nomes está ou não disponível dentro do serviço NotificationHub. |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Obter a lista de descrição das Regras de Autorização de Espaços de nomes. |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Elimine regra de autorização de espaço de nomes. Não é possível eliminar a regra de autorização de espaço de nomes predefinido.  |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Obter Cadeia de Ligação para o Espaço de nomes |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Obter a lista de descrição das Regras de Autorização de Espaços de nomes. |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Chave Primária/Secundária de Regeneração de Regra de Autorização de Espaço de nomes, especifica a Chave que tem de ser regenerada |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Criar regras de autorização ao nível de espaço de nomes e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o principal e as chaves secundárias podem ser atualizadas. |
> | Ação | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Verifica se determinado nome do NotificationHub está disponível dentro do Espaço de nomes. |
> | Ação | Microsoft.NotificationHubs/Namespaces/Delete | Eliminar Recurso de Espaço de nomes |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Obter lista de Regras de Autorização do Hub de Notificações |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Eliminar Regras de Autorização do Hub de Notificações |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Obter Cadeia de Ligação para o Hub de Notificações |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Obter lista de Regras de Autorização do Hub de Notificações |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Regra de Autorização do Hub de Notificações Regenerar Chave Primária/Secundária, especifica a Chave que tem de ser regenerada |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Criar regras de autorização do Hub de notificações e atualizar as respetivas propriedades. Os direitos de acesso de regras de autorização, o principal e as chaves secundárias podem ser atualizadas. |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Enviar notificação push de teste. |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Eliminar Recurso do Hub de Notificações |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Obter lista de descrições de recursos do espaço de nomes métricas |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Obtenha todas as credenciais PNS do Hub de notificação. Isto inclui, credenciais WNS, MPNS, APNS, GCM e Baidu |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Obter lista de Descrições de Recursos do Hub de Notificações |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Criar um Hub de notificação e atualizar as respetivas propriedades. Suas propriedades incluem, principalmente, credenciais PNS. Regras de autorização e TTL |
> | Ação | Microsoft.NotificationHubs/Namespaces/read | Obter a lista de Descrição de Recursos de Espaço de nomes |
> | Ação | Microsoft.NotificationHubs/Namespaces/write | Criar um recurso de espaço de nomes e Atualize as respetivas propriedades. As etiquetas e a capacidade do espaço de nomes são propriedades que podem ser atualizadas. |
> | Ação | Microsoft.NotificationHubs/operationResults/read | Devolve os resultados da operação para o fornecedor de Hubs de Notificação |
> | Ação | Microsoft.NotificationHubs/operations/read | Devolve uma lista de operações suportadas para o fornecedor de Hubs de Notificação |
> | Ação | Microsoft.NotificationHubs/register/action | Regista a subscrição para o fornecedor de recursos de Notification hubs e permite a criação de espaços de nomes e os Notification hubs |
> | Ação | Microsoft.NotificationHubs/unregister/action | Anula o registo da subscrição para o fornecedor de recursos de Notification hubs e permite a criação de espaços de nomes e os Notification hubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.OffAzure/HyperVSites/clusters/read | Obtém as propriedades de um cluster Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/clusters/read | Obtém as propriedades de um cluster Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/clusters/write | Cria ou atualiza o cluster de Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/clusters/write | Cria ou atualiza o cluster de Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/delete | Elimina o site Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/delete | Elimina o site Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/hosts/read | Obtém as propriedades de um anfitrião Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/hosts/read | Obtém as propriedades de um anfitrião Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/hosts/write | Cria ou atualiza o anfitrião de Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/hosts/write | Cria ou atualiza o anfitrião de Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/jobs/read | Obtém as propriedades de tarefas de Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/jobs/read | Obtém as propriedades de tarefas de Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/machines/read | Obtém as propriedades de um máquinas Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/machines/read | Obtém as propriedades de um máquinas Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/machines/start/action | Iniciar máquinas Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/machines/start/action | Iniciar máquinas Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/machines/stop/action | Para as máquinas o Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/machines/stop/action | Para as máquinas o Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Obtém as propriedades de um Estado da operação de Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Obtém as propriedades de um Estado da operação de Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/read | Obtém as propriedades de um site de Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/read | Obtém as propriedades de um site de Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/refresh/action | Atualiza os objetos dentro de um site de Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/refresh/action | Atualiza os objetos dentro de um site de Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Obtém as propriedades de um contas run as do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Obtém as propriedades de um contas run as do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/usage/read | Obtém as utilizações de um site de Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/usage/read | Obtém as utilizações de um site de Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/write | Cria ou atualiza o site Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/write | Cria ou atualiza o site Hyper-V |
> | Ação | Microsoft.OffAzure/Operations/read | Lê as operações expostas |
> | Ação | Microsoft.OffAzure/register/action | Regista a subscrição no fornecedor de recursos de Microsoft.OffAzure |
> | Ação | Microsoft.OffAzure/register/action | Regista a subscrição no fornecedor de recursos de Microsoft.OffAzure |
> | Ação | Microsoft.OffAzure/VMwareSites/delete | Elimina o site de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/delete | Elimina o site de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/jobs/read | Obtém as propriedades de tarefas de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/jobs/read | Obtém as propriedades de tarefas de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/read | Obtém as propriedades de um máquinas do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/read | Obtém as propriedades de um máquinas do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/start/action | Iniciar máquinas do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/start/action | Iniciar máquinas do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/stop/action | Para as máquinas do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/stop/action | Para as máquinas do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Obtém as propriedades de um Estado de operação do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Obtém as propriedades de um Estado de operação do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/read | Obtém as propriedades de um site de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/read | Obtém as propriedades de um site de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/refresh/action | Atualiza os objetos dentro de um site de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/refresh/action | Atualiza os objetos dentro de um site de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Obtém as propriedades de um VMware contas run as |
> | Ação | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Obtém as propriedades de um VMware contas run as |
> | Ação | Microsoft.OffAzure/VMwareSites/usage/read | Obtém as utilizações de um site de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/usage/read | Obtém as utilizações de um site de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/vcenters/read | Obtém as propriedades de um VMware vCenter |
> | Ação | Microsoft.OffAzure/VMwareSites/vcenters/read | Obtém as propriedades de um VMware vCenter |
> | Ação | Microsoft.OffAzure/VMwareSites/vcenters/write | Cria ou atualiza o vCenter do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/vcenters/write | Cria ou atualiza o vCenter do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/write | Cria ou atualiza o site de VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/write | Cria ou atualiza o site de VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.OperationalInsights/linkTargets/read | Apresenta uma lista de contas existentes que não estão associadas uma subscrição do Azure. Para ligar esta subscrição do Azure a uma área de trabalho, utilize um id de cliente devolvido por esta operação na propriedade de id de cliente da operação Criar área de trabalho. |
> | Ação | microsoft.operationalinsights/operations/read | Apresenta uma lista de todas as operações de API de Rest de OperationalInsights disponíveis. |
> | Ação | Microsoft.OperationalInsights/register/action | Registe uma subscrição de um fornecedor de recursos. |
> | Ação | Microsoft.OperationalInsights/workspaces/analytics/query/action | Usando o novo mecanismo de pesquisa. |
> | Ação | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Obter esquema de pesquisa V2. |
> | Ação | Microsoft.OperationalInsights/workspaces/api/query/action | Usando o novo mecanismo de pesquisa. |
> | Ação | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Obter esquema de pesquisa V2. |
> | Ação | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Eliminar âmbito da configuração |
> | Ação | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Obter âmbito da configuração |
> | Ação | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Definir o âmbito da configuração |
> | Ação | Microsoft.OperationalInsights/workspaces/datasources/delete | Elimine origens de dados numa área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/datasources/read | Obter origens de dados numa área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/datasources/write | Criar/atualizar origens de dados numa área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/delete | Elimina uma área de trabalho. Se a área de trabalho estava associada a uma área de trabalho existente no momento da criação da área de trabalho que estava associada não é eliminada. |
> | Ação | Microsoft.OperationalInsights/workspaces/gateways/delete | Remove um gateway configurado para a área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Gera o certificado de registo para a área de trabalho. Este certificado é utilizado para ligar o Microsoft System Center Operation Manager à área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Desativa um pacote de informações de uma área de trabalho específica. |
> | Ação | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Permite que um pacote de informações de uma área de trabalho específica. |
> | Ação | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Apresenta uma lista de todos os "intelligence packs" que estão visíveis para uma determinada área de trabalho e também indica se o pacote está ativado ou desativado para essa área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Eliminar serviços ligados na área de trabalho especificada. |
> | Ação | Microsoft.OperationalInsights/workspaces/linkedServices/read | Obter serviços ligados na área de trabalho especificada. |
> | Ação | Microsoft.OperationalInsights/workspaces/linkedServices/write | Criar/atualizar serviços ligados na área de trabalho especificada. |
> | Ação | Microsoft.OperationalInsights/workspaces/listKeys/action | Obtém as chaves de lista para a área de trabalho. Estas chaves são utilizadas para ligar os agentes de informações operacionais da Microsoft à área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/listKeys/read | Obtém as chaves de lista para a área de trabalho. Estas chaves são utilizadas para ligar os agentes de informações operacionais da Microsoft à área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/managementGroups/read | Obtém os nomes e os metadados para grupos de gestão do System Center Operations Manager ligados a esta área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Obter definições de métrica na área de trabalho |
> | Ação | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Elimine definições de notificação do utilizador para a área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Obter definições de notificação do utilizador para a área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Defina definições de notificação do utilizador para a área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/purge/action | Eliminar dados especificados a partir da área de trabalho |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Ler dados da tabela ADAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Ler dados da tabela ADReplicationResult |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Ler dados da tabela ADSecurityAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Alert/read | Ler dados da tabela de Alertas |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Ler dados da tabela AlertHistory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Ler dados da tabela AppCenterError |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Ler dados da tabela ApplicationInsights |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Ler dados da tabela AuditLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Ler dados da tabela AutoscaleEvaluationsLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Ler dados da tabela AutoscaleScaleActionsLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Ler dados da tabela AWSCloudTrail |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Ler dados da tabela AzureActivity |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Ler dados da tabela AzureMetrics |
> | Ação | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Ler dados da tabela BoundPort |
> | Ação | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Ler dados da tabela CommonSecurityLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Ler dados da tabela ComputerGroup |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Ler dados da tabela ConfigurationChange |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Ler dados da tabela ConfigurationData |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Ler dados da tabela ContainerImageInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Ler dados da tabela ContainerInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Ler dados da tabela ContainerLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Ler dados da tabela ContainerNodeInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Ler dados da tabela ContainerServiceLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Ler dados da tabela DeviceAppCrash |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Ler dados da tabela DeviceAppLaunch |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Ler dados da tabela DeviceCalendar |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Ler dados da tabela DeviceCleanup |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Ler dados da tabela DeviceConnectSession |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Ler dados da tabela DeviceEtw |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Ler dados da tabela DeviceHardwareHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Ler dados da tabela DeviceHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Ler dados da tabela DeviceHeartbeat |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Ler dados da tabela DeviceSkypeHeartbeat |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Ler dados da tabela DeviceSkypeSignIn |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Ler dados da tabela DeviceSleepState |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Ler dados da tabela DHAppFailure |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Ler dados da tabela DHAppReliability |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Ler dados da tabela DHDriverReliability |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Ler dados da tabela DHLogonFailures |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Ler dados da tabela DHLogonMetrics |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Ler dados da tabela DHOSCrashData |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Ler dados da tabela DHOSReliability |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Ler dados da tabela DHWipAppLearning |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Ler dados da tabela DnsEvents |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Ler dados da tabela DnsInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Ler dados da tabela ETWEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Event/read | Ler dados da tabela de Eventos |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Ler dados da tabela ExchangeAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Ler dados da tabela ExchangeOnlineAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Ler dados da tabela Heartbeat |
> | Ação | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Ler dados da tabela HuntingBookmark |
> | Ação | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Ler dados da tabela IISAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Ler dados da tabela InboundConnection |
> | Ação | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Ler dados da tabela IntuneAuditLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Ler dados da tabela IntuneOperationalLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Ler dados da tabela KubeEvents |
> | Ação | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Ler dados da tabela KubeNodeInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Ler dados da tabela KubePodInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Ler dados da tabela KubeServices |
> | Ação | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Ler dados da tabela LinuxAuditLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Ler dados da tabela MAApplication |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Ler dados da tabela MAApplicationHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Ler dados da tabela MAApplicationHealthAlternativeVersions |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Ler dados da tabela MAApplicationHealthIssues |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Ler dados da tabela MAApplicationInstance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Ler dados da tabela MAApplicationInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Ler dados da tabela MAApplicationReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Ler dados da tabela MADeploymentPlan |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Ler dados da tabela MADevice |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Ler dados da tabela MADeviceNotEnrolled |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Ler dados da tabela MADevicePnPHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Ler dados da tabela MADevicePnPHealthIssues |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Ler dados da tabela MADeviceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Ler dados da tabela MADriverInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Ler dados da tabela MADriverReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Ler dados da tabela MAOfficeAddin |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Ler dados da tabela MAOfficeAddinHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Ler dados da tabela MAOfficeAddinHealthIssues |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Ler dados da tabela MAOfficeAddinInstance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Ler dados da tabela MAOfficeAddinReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Ler dados da tabela MAOfficeApp |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Ler dados da tabela MAOfficeAppHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Ler dados da tabela MAOfficeAppInstance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Ler dados da tabela MAOfficeAppReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Ler dados da tabela MAOfficeBuildInfo |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Ler dados da tabela MAOfficeCurrencyAssessment |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Ler dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Ler dados da tabela MAOfficeDeploymentStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Ler dados da tabela MAOfficeMacroHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Ler dados da tabela MAOfficeMacroHealthIssues |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Ler dados da tabela MAOfficeMacroIssueInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Ler dados da tabela MAOfficeMacroIssueReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Ler dados da tabela MAOfficeMacroSummary |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Ler dados da tabela MAOfficeSuite |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Ler dados da tabela MAOfficeSuiteInstance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Ler dados da tabela MAProposedPilotDevices |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Ler dados da tabela MAWindowsBuildInfo |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Ler dados da tabela MAWindowsCurrencyAssessment |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Ler dados da tabela MAWindowsCurrencyAssessmentDailyCounts |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Ler dados da tabela MAWindowsDeploymentStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Ler dados da tabela NetworkMonitoring |
> | Ação | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Ler dados da tabela OfficeActivity |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Operation/read | Ler dados da tabela de Operação |
> | Ação | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Ler dados da tabela OutboundConnection |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Perf/read | Ler dados da tabela de Desempenho |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Ler dados da tabela ProtectionStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/read | Executar consultas através dos dados na área de trabalho |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Ler dados da tabela ReservedAzureCommonFields |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Ler dados da tabela ReservedCommonFields |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Ler dados da tabela SCCMAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Ler dados da tabela SCOMAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Ler dados da tabela SecurityAlert |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Ler dados da tabela SecurityBaseline |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Ler dados da tabela SecurityBaselineSummary |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Ler dados da tabela SecurityDetection |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Ler dados da tabela SecurityEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Ler dados da tabela SecurityIoTRawEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Ler dados da tabela SecurityRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Ler dados da tabela ServiceFabricOperationalEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Ler dados da tabela ServiceFabricReliableActorEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Ler dados da tabela ServiceFabricReliableServiceEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Ler dados da tabela SfBAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Ler dados da tabela SfBOnlineAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Ler dados da tabela SharePointOnlineAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Ler dados da tabela SigninLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Ler dados da tabela SPAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Ler dados da tabela SQLAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Ler dados da tabela SQLQueryPerformance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Ler dados da tabela Syslog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Ler dados da tabela SysmonEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | A ler dados de registos personalizados |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Ler dados da tabela ThreatIntelligenceIndicator |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Ler dados da tabela UAApp |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Ler dados da tabela UAComputer |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Ler dados da tabela UAComputerRank |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Ler dados da tabela UADriver |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Ler dados da tabela UADriverProblemCodes |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Ler dados da tabela UAFeedback |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Ler dados da tabela UAHardwareSecurity |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Ler dados da tabela UAIESiteDiscovery |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Ler dados da tabela UAOfficeAddIn |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Ler dados da tabela UAProposedActionPlan |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Ler dados da tabela UASysReqIssue |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Ler dados da tabela UAUpgradedComputer |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Update/read | Ler dados da tabela de Atualização |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Ler dados da tabela UpdateRunProgress |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Ler dados da tabela UpdateSummary |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Usage/read | Ler dados da tabela de Utilização |
> | Ação | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Ler dados da tabela VMBoundPort |
> | Ação | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Ler dados da tabela VMConnection |
> | Ação | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Ler dados da tabela W3CIISLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Ler dados da tabela WaaSDeploymentStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Ler dados da tabela WaaSInsiderStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Ler dados da tabela WaaSUpdateStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Ler dados da tabela WDAVStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Ler dados da tabela WDAVThreat |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Ler dados da tabela WindowsClientAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Ler dados da tabela WindowsEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Ler dados da tabela WindowsFirewall |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Ler dados da tabela WindowsServerAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WireData/read | Ler dados da tabela WireData |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Ler dados da tabela WorkloadMonitoringPerf |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Ler dados da tabela WUDOAggregatedStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Ler dados da tabela WUDOStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/read | Obtém uma área de trabalho existente |
> | Ação | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Regenera a chave partilhada de área de trabalho especificada |
> | Ação | microsoft.operationalinsights/workspaces/rules/read | Obtenha todas as regras de alerta. |
> | Ação | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Elimina uma consulta de pesquisa guardada |
> | Ação | Microsoft.OperationalInsights/workspaces/savedSearches/read | Obtém uma consulta de pesquisa guardada |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/results/read | Serão salvos de resultados de pesquisas. Preterido |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Elimine ações de pesquisa agendada. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Obter ações de pesquisa agendada. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Criar ou atualizar ações de pesquisa agendada. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Elimine procuras agendadas. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Obtenha pesquisas agendadas. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Criar ou atualizar pesquisas agendadas. |
> | Ação | Microsoft.OperationalInsights/workspaces/savedSearches/write | Cria uma consulta de pesquisa guardada |
> | Ação | Microsoft.OperationalInsights/workspaces/schema/read | Obtém o esquema de pesquisa para a área de trabalho.  Esquema de pesquisa inclui os campos expostos e os respetivos tipos. |
> | Ação | Microsoft.OperationalInsights/workspaces/search/action | Executa uma consulta de pesquisa |
> | Ação | microsoft.operationalinsights/workspaces/search/read | Obtenha os resultados da pesquisa. Preterido. |
> | Ação | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Obtém as chaves partilhadas para a área de trabalho. Estas chaves são utilizadas para ligar os agentes de informações operacionais da Microsoft à área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Obtém as chaves partilhadas para a área de trabalho. Estas chaves são utilizadas para ligar os agentes de informações operacionais da Microsoft à área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Elimina uma configuração de armazenamento. Isso deixa de informações operacionais da Microsoft de ler dados da conta de armazenamento. |
> | Ação | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Obtém uma configuração de armazenamento. |
> | Ação | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Cria uma nova configuração de armazenamento. Estas configurações são usadas para extrair dados de uma localização de uma conta de armazenamento existente. |
> | Ação | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Obter o registo de falhas de tradução de atualização de pesquisa para a área de trabalho |
> | Ação | Microsoft.OperationalInsights/workspaces/usages/read | Obtém os dados de utilização de uma área de trabalho incluindo a quantidade de dados lidos pela área de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/write | Cria uma nova área de trabalho ou ligações para uma área de trabalho existente fornecendo o id de cliente da área de trabalho existente. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.OperationsManagement/managementAssociations/delete | Eliminar a associação de gestão existente |
> | Ação | Microsoft.OperationsManagement/managementAssociations/read | Obtenha a associação de gestão existente |
> | Ação | Microsoft.OperationsManagement/managementAssociations/write | Criar uma nova associação de gestão |
> | Ação | Microsoft.OperationsManagement/managementConfigurations/delete | Eliminar configuração de gestão existente |
> | Ação | Microsoft.OperationsManagement/managementConfigurations/read | Obter configuração de gestão existente |
> | Ação | Microsoft.OperationsManagement/managementConfigurations/write | Criar uma nova configuração de gestão |
> | Ação | Microsoft.OperationsManagement/register/action | Registe uma subscrição de um fornecedor de recursos. |
> | Ação | Microsoft.OperationsManagement/solutions/delete | Eliminar a solução de OMS existente |
> | Ação | Microsoft.OperationsManagement/solutions/read | Obter a solução de OMS a sair |
> | Ação | Microsoft.OperationsManagement/solutions/write | Criar nova solução OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.PolicyInsights/asyncOperationResults/read | Obtém o resultado da operação assíncrona. |
> | Ação | Microsoft.PolicyInsights/policyEvents/queryResults/action | Informações de consulta sobre eventos de política. |
> | Ação | Microsoft.PolicyInsights/policyEvents/queryResults/read | Informações de consulta sobre eventos de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/queryResults/action | Informações de consulta sobre estados de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/queryResults/read | Informações de consulta sobre estados de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/summarize/action | Informações de resumo de consulta sobre os estados de política mais recentes. |
> | Ação | Microsoft.PolicyInsights/policyStates/summarize/read | Informações de resumo de consulta sobre os estados de política mais recentes. |
> | Ação | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Desencadeia uma nova avaliação de conformidade para o âmbito selecionado. |
> | Ação | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | As informações de consulta sobre recursos necessária pelas políticas de DeployIfNotExists. |
> | Ação | Microsoft.PolicyInsights/register/action | Regista o fornecedor de recursos de informações de política e permite ações no mesmo. |
> | Ação | Microsoft.PolicyInsights/remediations/cancel/action | Cancele as remediações de política em curso. |
> | Ação | Microsoft.PolicyInsights/remediations/delete | Elimine remediações de política. |
> | Ação | Microsoft.PolicyInsights/remediations/listDeployments/read | Lista as implementações necessárias para por uma remediação de política. |
> | Ação | Microsoft.PolicyInsights/remediations/read | Obter remediações de política. |
> | Ação | Microsoft.PolicyInsights/remediations/write | Criar ou atualizar as remediações de política. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Portal/consoles/delete | Remove a instância da cloud shell. |
> | Ação | Microsoft.Portal/consoles/write | Criar ou atualizar uma instância da cloud shell. |
> | Ação | Microsoft.Portal/dashboards/delete | Remove o dashboard da subscrição. |
> | Ação | Microsoft.Portal/dashboards/read | Lê os dashboards da subscrição. |
> | Ação | Microsoft.Portal/dashboards/write | Adicionar ou modificar o dashboard para uma subscrição. |
> | Ação | Microsoft.Portal/register/action | Registar no Portal |
> | Ação | Microsoft.Portal/usersettings/delete | Remova as definições de utilizador da cloud shell. |
> | Ação | Microsoft.Portal/usersettings/read | Lê as definições de utilizador da cloud shell. |
> | Ação | Microsoft.Portal/usersettings/write | Crie ou atualize definição de utilizador da cloud shell. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.PowerBIDedicated/capacities/delete | Elimina o Power BI dedicado a capacidade. |
> | Ação | Microsoft.PowerBIDedicated/capacities/read | Obtém as informações do Power BI dedicado capacidade especificada. |
> | Ação | Microsoft.PowerBIDedicated/capacities/resume/action | Retoma a capacidade. |
> | Ação | Microsoft.PowerBIDedicated/capacities/skus/read | Obter informações do SKU disponíveis para a capacidade |
> | Ação | Microsoft.PowerBIDedicated/capacities/suspend/action | Suspende a capacidade. |
> | Ação | Microsoft.PowerBIDedicated/capacities/write | Cria ou atualiza o Power BI dedicado capacidade especificada. |
> | Ação | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Verificações de que nome da capacidade do Power BI dedicado é válido e não em utilização. |
> | Ação | Microsoft.PowerBIDedicated/locations/operationresults/read | Obtém as informações do resultado da operação especificado. |
> | Ação | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Obtém as informações de estado da operação especificado. |
> | Ação | Microsoft.PowerBIDedicated/operations/read | Obtém as informações de operações |
> | Ação | Microsoft.PowerBIDedicated/register/action | Regista o fornecedor de recursos do Power BI dedicado. |
> | Ação | Microsoft.PowerBIDedicated/skus/read | Obtém as informações dos Skus |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna utilizada pelo serviço |
> | Ação | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna utilizada pelo serviço |
> | Ação | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Ação | Microsoft.RecoveryServices/Locations/backupStatus/action | Verificar o estado de cópia de segurança para cofres dos serviços de recuperação |
> | Ação | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Validar funcionalidades |
> | Ação | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Verificar a que disponibilidade do nome do recurso é uma API para verificar se o nome do recurso está disponível |
> | Ação | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o estado da operação a uma determinada operação |
> | Ação | Microsoft.RecoveryServices/operations/read | Operação devolve a lista de operações para um fornecedor de recursos |
> | Ação | Microsoft.RecoveryServices/register/action | Tendo em conta subscrição de registos para o fornecedor de recursos |
> | Ação | Microsoft.RecoveryServices/Vaults/backupconfig/read | Devolve a configuração para a recuperação Cofre de serviços. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupconfig/write | Cofre de serviços de configuração de atualizações para a recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupEngines/read | Devolve todos os servidores de gestão de cópia de segurança registados no cofre. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Eliminar uma intenção de proteção de cópia de segurança |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obtenha uma intenção de proteção de cópia de segurança |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma intenção de proteção de cópia de segurança |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Devolve o estado da operação |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obter todos os contentores protegíveis |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Elimina o contentor registado |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Execute a consulta para cargas de trabalho dentro de um contêiner |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obter todos os itens num contentor |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtém o resultado da Operação efetuada no Contentor de Proteção. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Executa a Cópia de Segurança do Item Protegido. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Elimina o Item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtém o Resultado da Operação Efetuada nos Itens Protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Devolve o estado da Operação efetuada nos Itens Protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Devolve os detalhes de objeto do Item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Aprovisionar recuperação de Item instantâneo para Item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obter Pontos de Recuperação de Itens Protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurar Pontos de Recuperação de Itens Protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revogar recuperação de Item instantâneo para Item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um Item de cópia de segurança protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Devolve todos os contentores registados |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Cria um contentor registado |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualiza a lista de contentores |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Cancelar a tarefa |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Devolve o Resultado da Operação de Tarefa. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobs/read | Devolve todos os objetos de trabalho |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar tarefas |
> | Ação | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Devolve o Resultado da Operação da Cópia de Segurança do Cofre de Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupOperations/read | Devolve a operação de cópia de segurança Cofre de serviços de estado de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Eliminar uma política de proteção |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter Resultados da Operação de Política. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter o estado da operação da política. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Devolve todas as políticas de proteção |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Cria a política de proteção |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Devolve a lista de todos os Itens Susceptíveis de Proteção. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Devolve a lista de todos os Itens Protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Devolve todos os contentores pertencentes à subscrição |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Lista de todos os objetivos de proteção de cópia de segurança |
> | Ação | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Devolve o PIN de segurança Cofre de serviços de informações de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Devolve a configuração de armazenamento para a recuperação Cofre de serviços. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Cofre de serviços de configuração de armazenamento de atualizações de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Resumos de utilização dos itens e servidores protegidos para dos serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar a operação no Item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/certificates/write | A operação atualizar certificado do recurso atualiza o certificado de credencial do recurso/cofre. |
> | Ação | Microsoft.RecoveryServices/Vaults/delete | A operação eliminar cofre elimina o recurso do Azure especificado do tipo "Cofre" |
> | Ação | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Ação | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Ação | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter alertas para o Cofre dos serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Obtém a configuração de notificação de Cofre de serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Configura notificações de e-mail para cofre dos serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre obtém um objeto que representa o recurso do Azure do tipo "Cofre" |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | A operação de anular o registo de contentor pode ser utilizada para anular o registo de um contentor. |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação obter resultados da operação pode ser utilizada obter o estado da operação e o resultado da operação submetida assincronamente |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A obter contentores pode ser utilizada a operação obter os contentores registados para um recurso. |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A operação registar contentor de serviços pode ser utilizada para registar um contentor com o serviço de recuperação. |
> | Ação | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler quaisquer definições de alertas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Criar ou atualizar as definições de alertas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler todos os eventos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verifica a Consistência dos Recursos de infraestrutura |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Eliminar quaisquer recursos de infraestrutura |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Implementar a imagem de servidor de processo |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrar recursos de infraestrutura para o AAD |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler quaisquer recursos de infraestrutura |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociar Gateway |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Remover recursos de infraestrutura |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renovar o certificado para recursos de infraestrutura |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Ler quaisquer redes lógicas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer redes |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Eliminar quaisquer mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Criar ou atualizar quaisquer mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Detetar Item Protegível |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Ler quaisquer contentores de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Remover o contentor de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Eliminar quaisquer itens de migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migrar o Item |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Ler quaisquer pontos de recuperação de migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Ler quaisquer itens de migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Migrar de teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Teste migrar limpeza |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Criar ou atualizar quaisquer itens de migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Ler todos os itens susceptíveis de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplicar o ponto de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Eliminar todos os itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Consolidação de ativação pós-falha |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Ativação Pós-falha Planeada |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler todos os itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler quaisquer pontos de recuperação de replicação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Remover Item protegido |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparar replicação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Voltar a proteger o Item protegido |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Submeter Comentários |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Ler quaisquer tamanhos de computação de destino |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Ativação Pós-falha de Teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Limpeza da ativação pós-falha de teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Ativação pós-falha |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Atualização do serviço de mobilidade |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Criar ou atualizar todos os itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Eliminar quaisquer mapeamentos do contentor de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler quaisquer mapeamentos do contentor de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Remover mapeamento de contentor de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Criar ou atualizar quaisquer mapeamentos do contentor de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Contentor de proteção do comutador |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Criar ou atualizar quaisquer contentores de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Eliminar quaisquer fornecedores de serviços de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler quaisquer fornecedores de serviços de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Atualizar fornecedor |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Remover fornecedor de serviços de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Criar ou atualizar quaisquer fornecedores de serviços de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Ler qualquer classificações de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Eliminar quaisquer mapeamentos de classificação de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Criar ou atualizar quaisquer mapeamentos de classificação de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Eliminar qualquer vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler qualquer vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Criar ou atualizar qualquer vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Criar ou atualizar quaisquer recursos de infraestrutura |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Cancelar Tarefa |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/read | Ler todas as tarefas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Reiniciar tarefa |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Retomar tarefa |
> | Ação | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Ler quaisquer itens de migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Ler quaisquer redes |
> | Ação | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Eliminar todas as políticas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler todas as políticas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Criar ou atualizar todas as políticas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Ler todos os itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Ler quaisquer mapeamentos do contentor de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Ler quaisquer contentores de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Eliminar os planos de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plano de recuperação de consolidação de ativação pós-falha |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plano de recuperação de ativação pós-falha planeada |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler quaisquer planos de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Voltar a proteger o plano de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plano de recuperação de ativação pós-falha de teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plano de recuperação de limpeza da ativação pós-falha de teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plano de recuperação de ativação pós-falha |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Criar ou atualizar qualquer planos de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Ler quaisquer fornecedores de serviços de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Ler qualquer classificações de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Ler qualquer  |
> | Ação | Microsoft.RecoveryServices/vaults/replicationUsages/read | Ler quaisquer utilizações de replicação do Cofre |
> | Ação | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Ler qualquer Estado de funcionamento de replicação do Cofre |
> | Ação | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Atualizar o estado de funcionamento do Cofre |
> | Ação | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Ler qualquer vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/usages/read | Ler quaisquer utilizações do Cofre |
> | Ação | Microsoft.RecoveryServices/Vaults/usages/read | Devolve detalhes de utilização de um Cofre de Serviços de Recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de Token de cofre pode ser utilizada para obter o Token de cofre para operações de back-end ao nível do cofre. |
> | Ação | Microsoft.RecoveryServices/Vaults/write | A operação Criar Cofre cria um recurso do tipo "cofre" do Azure |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Relay/checkNameAvailability/action | Verifica a disponibilidade do espaço de nomes em determinada subscrição. |
> | Ação | Microsoft.Relay/checkNamespaceAvailability/action | Verifica a disponibilidade do espaço de nomes em determinada subscrição. Esta API foi preterida utilize CheckNameAvailability em vez disso. |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/action | Regra de autorização de espaço de nomes de atualizações. Esta API foi preterida. Utilize uma chamada PUT para atualizar a regra de autorização de espaço de nomes em vez disso,.... Esta operação não é suportada na versão 2017-04-01 de API. |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/delete | Elimine regra de autorização de espaço de nomes. Não é possível eliminar a regra de autorização de espaço de nomes predefinido.  |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Obter Cadeia de Ligação para o Espaço de nomes |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/read | Obter a lista de descrição das Regras de Autorização de Espaços de nomes. |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/write | Criar regras de autorização ao nível de espaço de nomes e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o principal e as chaves secundárias podem ser atualizadas. |
> | Ação | Microsoft.Relay/namespaces/Delete | Eliminar Recurso de Espaço de nomes |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtém as chaves das regras de autorização do espaço de nomes principal da Recuperação Após Desastre |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter as Regras de Autorização do Espaço de Nomes Principal da Recuperação Após Desastre |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Desativa a Recuperação Após Desastre e para a replicação de alterações de espaço de nomes principais para secundários. |
> | Ação | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica a disponibilidade do alias do espaço de nomes numa determinada subscrição. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Elimina a configuração de recuperação após desastre associada no espaço de nomes. Esta operação só pode ser chamada por meio do espaço de nomes primário. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Invoca uma ativação pós-falha GEO DR e reconfigura o alias de espaço de nomes para apontar para o espaço de nomes secundário. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Obtém a configuração da Recuperação Após Desastre associada ao espaço de nomes. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Cria ou Atualiza a configuração da Recuperação Após Desastre associada ao espaço de nomes. |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operação para atualizar HybridConnection. Esta operação não é suportada na versão 2017-04-01 de API. Regras de autorização. Utilize uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operação para eliminar regras de autorização de HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Obter a cadeia de ligação para HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Obter a lista de regras de autorização de HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Criar regras de autorização de HybridConnection e atualizar as respetivas propriedades. Os direitos de acesso de regras de autorização podem ser atualizados. |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/Delete | Operação para eliminar o recurso de HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/read | Obter lista de descrições de recursos de HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/write | Criar ou propriedades de HybridConnection de atualização. |
> | Ação | Microsoft.Relay/namespaces/messagingPlan/read | Obtém o plano de mensagens para um espaço de nomes.<br>Esta API foi preterida.<br>As propriedades expostas por intermédio do recurso MessagingPlan são movidas para o recurso de espaço de nomes em versões de API posteriores do (principal)....<br>Esta operação não é suportada na versão 2017-04-01 de API. |
> | Ação | Microsoft.Relay/namespaces/messagingPlan/write | Atualiza o plano de mensagens para um espaço de nomes.<br>Esta API foi preterida.<br>As propriedades expostas por intermédio do recurso MessagingPlan são movidas para o recurso de espaço de nomes em versões de API posteriores do (principal)....<br>Esta operação não é suportada na versão 2017-04-01 de API. |
> | Ação | Microsoft.Relay/namespaces/operationresults/read | Obter o estado da operação de Espaço de nomes |
> | Ação | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de descrições de recursos do espaço de nomes métricas |
> | Ação | Microsoft.Relay/namespaces/read | Obter a lista de Descrição de Recursos de Espaço de nomes |
> | Ação | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Remova o espaço de nomes do ACS |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operação para atualizar WcfRelay. Esta operação não é suportada na versão 2017-04-01 de API. Regras de autorização. Utilize uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operação para eliminar regras de autorização de WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Obter a cadeia de ligação para WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Obter a lista de regras de autorização de WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Criar regras de autorização de WcfRelay e atualizar as respetivas propriedades. Os direitos de acesso de regras de autorização podem ser atualizados. |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/Delete | Operação para eliminar o recurso de WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/read | Obter lista de descrições de recursos de WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/write | Criar ou propriedades de WcfRelay de atualização. |
> | Ação | Microsoft.Relay/namespaces/write | Criar um recurso de espaço de nomes e Atualize as respetivas propriedades. As etiquetas e a capacidade do espaço de nomes são propriedades que podem ser atualizadas. |
> | Ação | Microsoft.Relay/operations/read | Obter Operações |
> | Ação | Microsoft.Relay/register/action | Regista a subscrição do fornecedor de recursos de Reencaminhamento e ativa a criação de recursos de Reencaminhamento |
> | Ação | Microsoft.Relay/unregister/action | Regista a subscrição do fornecedor de recursos de Reencaminhamento e ativa a criação de recursos de Reencaminhamento |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Obtém o estado de disponibilidade para o recurso especificado. |
> | Ação | Microsoft.ResourceHealth/AvailabilityStatuses/read | Obtenha os estados de disponibilidade para todos os recursos no âmbito especificado |
> | Ação | Microsoft.ResourceHealth/events/read | Obter Eventos de Estado de Funcionamento do Serviço da subscrição indicada |
> | Ação | Microsoft.Resourcehealth/healthevent/action | Indica a alteração no estado de funcionamento para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/Activated/action | Indica a alteração no estado de funcionamento para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/InProgress/action | Indica a alteração no estado de funcionamento para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/Pending/action | Indica a alteração no estado de funcionamento para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/Resolved/action | Indica a alteração no estado de funcionamento para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/Updated/action | Indica a alteração no estado de funcionamento para o recurso especificado |
> | Ação | Microsoft.ResourceHealth/impactedResources/read | Obter Recursos Afetados da subscrição indicada |
> | Ação | Microsoft.ResourceHealth/Operations/read | Obter as operações disponíveis para o ResourceHealth Microsoft |
> | Ação | Microsoft.ResourceHealth/register/action | Regista a subscrição para o Microsoft ResourceHealth |
> | Ação | Microsoft.ResourceHealth/unregister/action | Anular o registo da subscrição ResourceHealth Microsoft |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Resources/checkPolicyCompliance/action | Verifique o estado de conformidade de um dado recurso em relação a políticas de recursos. |
> | Ação | Microsoft.Resources/checkResourceName/action | Verificar a validade do nome do recurso. |
> | Ação | Microsoft.Resources/deployments/cancel/action | Cancela uma implementação. |
> | Ação | Microsoft.Resources/deployments/delete | Elimina uma implementação. |
> | Ação | Microsoft.Resources/deployments/operations/read | Obtém ou lista as operações de implementação. |
> | Ação | Microsoft.Resources/deployments/read | Obtém ou lista implementações. |
> | Ação | Microsoft.Resources/deployments/validate/action | Valida uma implementação. |
> | Ação | Microsoft.Resources/deployments/write | Cria ou atualiza uma implementação. |
> | Ação | Microsoft.Resources/links/delete | Elimina uma ligação de recursos. |
> | Ação | Microsoft.Resources/links/read | Obtém ou lista ligações de recursos. |
> | Ação | Microsoft.Resources/links/write | Cria ou atualiza uma ligação de recursos. |
> | Ação | Microsoft.Resources/marketplace/purchase/action | Adquire um recurso do mercado. |
> | Ação | Microsoft.Resources/providers/read | Obter a lista de fornecedores. |
> | Ação | Microsoft.Resources/resources/read | Obter a lista de recursos com base em filtros. |
> | Ação | Microsoft.Resources/subscriptions/locations/read | Obtém a lista de localizações suportadas. |
> | Ação | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação de subscrição. |
> | Ação | Microsoft.Resources/subscriptions/providers/read | Obtém ou lista fornecedores de recursos. |
> | Ação | Microsoft.Resources/subscriptions/read | Obtém a lista de subscrições. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/delete | Elimina um grupo de recursos e respetivos recursos. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Obtém ou lista as operações de implementação. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Obtém ou lista os estados da operação de implementação. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Obtém ou lista implementações. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Cria ou atualiza uma implementação. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Move os recursos de um grupo de recursos para outro. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Obtém os recursos do grupo de recursos. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Validar a movimentação de recursos de um grupo de recursos para outro. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/write | Cria ou atualiza um grupo de recursos. |
> | Ação | Microsoft.Resources/subscriptions/resources/read | Obtém os recursos de uma subscrição. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/delete | Elimina uma etiqueta de subscrição. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/read | Obtém ou lista etiquetas de subscrição. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Elimina um valor de etiqueta de subscrição. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Obtém ou lista valores de etiqueta de subscrição. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Adiciona um valor de etiqueta de subscrição. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/write | Adiciona uma etiqueta de subscrição. |
> | Ação | Microsoft.Resources/tenants/read | Obtém a lista de inquilinos. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Scheduler/jobcollections/delete | Elimina a coleção de tarefas. |
> | Ação | Microsoft.Scheduler/jobcollections/disable/action | Desativa a coleção de tarefas. |
> | Ação | Microsoft.Scheduler/jobcollections/enable/action | Ativa a coleção de tarefas. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/delete | Elimina a tarefa. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Gera a definição da Aplicação Lógica com base numa Tarefa do Scheduler. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Obtém o histórico de tarefas. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/read | Obtém a tarefa. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/run/action | Executa a tarefa. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/write | Cria ou atualiza a tarefa. |
> | Ação | Microsoft.Scheduler/jobcollections/read | Obter Coleção de Tarefas |
> | Ação | Microsoft.Scheduler/jobcollections/write | Cria ou atualiza a coleção de tarefas. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Search/checkNameAvailability/action | Verifica a disponibilidade do nome do serviço. |
> | Ação | Microsoft.Search/operations/read | Apresenta uma lista de todas as operações disponíveis do fornecedor Microsoft.Search. |
> | Ação | Microsoft.Search/register/action | Regista a subscrição para o fornecedor de recursos de pesquisa e permite a criação de serviços de pesquisa. |
> | Ação | Microsoft.Search/searchServices/createQueryKey/action | Cria a chave de consulta. |
> | Ação | Microsoft.Search/searchServices/delete | Elimina o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/deleteQueryKey/delete | Elimina a chave de consulta. |
> | Ação | Microsoft.Search/searchServices/listAdminKeys/action | Lê chaves de administração. |
> | Ação | Microsoft.Search/searchServices/listQueryKeys/read | Devolve a lista das chaves de consulta API para o determinado serviço de Azure Search. |
> | Ação | Microsoft.Search/searchServices/read | Lê o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/regenerateAdminKey/action | Regenera a chave de administrador. |
> | Ação | Microsoft.Search/searchServices/start/action | Inicia o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/stop/action | Para o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/write | Cria ou atualiza o serviço de pesquisa. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Security/advancedThreatProtectionSettings/read | Obtém as definições de proteção de ameaças avançadas para o recurso |
> | Ação | Microsoft.Security/advancedThreatProtectionSettings/write | As definições de proteção avançada contra ameaças para o recurso de atualizações |
> | Ação | Microsoft.Security/alerts/read | Obtém todos os alertas de segurança disponíveis |
> | Ação | Microsoft.Security/applicationWhitelistings/read | Obtém o whitelistings de aplicação |
> | Ação | Microsoft.Security/applicationWhitelistings/write | Cria uma lista de permissões de aplicação nova ou atualiza um já existente |
> | Ação | Microsoft.Security/complianceResults/read | Obtém os resultados de compatibilidade para o recurso |
> | Ação | Microsoft.Security/informationProtectionPolicies/read | Obtém as informações de políticas de proteção para o recurso |
> | Ação | Microsoft.Security/informationProtectionPolicies/write | As políticas de proteção de informações para o recurso de atualizações |
> | Ação | Microsoft.Security/locations/alerts/activate/action | Ativar um alerta de segurança |
> | Ação | Microsoft.Security/locations/alerts/dismiss/action | Dispensar um alerta de segurança |
> | Ação | Microsoft.Security/locations/alerts/read | Obtém todos os alertas de segurança disponíveis |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Elimina a política de acesso de rede just-in-time |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Inicia um pedido de política de acesso de rede just-in-time |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Obtém as políticas de acesso de rede just-in-time |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Cria uma nova política de acesso de rede just-in-time ou atualiza um já existente |
> | Ação | Microsoft.Security/locations/read | Obtém a localização de dados de segurança |
> | Ação | Microsoft.Security/locations/tasks/activate/action | Ativar uma recomendação de segurança |
> | Ação | Microsoft.Security/locations/tasks/dismiss/action | Dispensar uma recomendação de segurança |
> | Ação | Microsoft.Security/locations/tasks/read | Obtém todas as recomendações de segurança disponíveis |
> | Ação | Microsoft.Security/locations/tasks/resolve/action | Resolver uma recomendação de segurança |
> | Ação | Microsoft.Security/locations/tasks/start/action | Iniciar uma recomendação de segurança |
> | Ação | Microsoft.Security/policies/read | Obtém a política de segurança |
> | Ação | Microsoft.Security/policies/write | Atualiza a política de segurança |
> | Ação | Microsoft.Security/pricings/delete | Elimina as definições de preços para o âmbito |
> | Ação | Microsoft.Security/pricings/read | Obtém as definições de preços para o âmbito |
> | Ação | Microsoft.Security/pricings/write | Atualiza as definições de preços para o âmbito |
> | Ação | Microsoft.Security/register/action | Regista a subscrição para o Centro de segurança do Azure |
> | Ação | Microsoft.Security/securityContacts/delete | Elimina o contacto de segurança |
> | Ação | Microsoft.Security/securityContacts/read | Obtém o contacto de segurança |
> | Ação | Microsoft.Security/securityContacts/write | Atualiza o contacto de segurança |
> | Ação | Microsoft.Security/securitySolutions/delete | Elimina uma solução de segurança |
> | Ação | Microsoft.Security/securitySolutions/read | Obtém as soluções de segurança |
> | Ação | Microsoft.Security/securitySolutions/write | Cria uma nova solução de segurança ou atualiza um já existente |
> | Ação | Microsoft.Security/securitySolutionsReferenceData/read | Obtém as soluções de segurança de dados de referência |
> | Ação | Microsoft.Security/securityStatuses/read | Obtém a segurança Estados de estado de funcionamento de recursos do Azure |
> | Ação | Microsoft.Security/securityStatusesSummaries/read | Obtém a segurança resumos de Estados para o âmbito |
> | Ação | Microsoft.Security/settings/read | Obtém as definições para o âmbito |
> | Ação | Microsoft.Security/settings/write | Atualiza as definições para o âmbito |
> | Ação | Microsoft.Security/tasks/read | Obtém todas as recomendações de segurança disponíveis |
> | Ação | Microsoft.Security/unregister/action | Anula o registo da subscrição do Centro de segurança do Azure |
> | Ação | Microsoft.Security/webApplicationFirewalls/delete | Elimina uma firewall de aplicações web |
> | Ação | Microsoft.Security/webApplicationFirewalls/read | Obtém a web firewalls de aplicações |
> | Ação | Microsoft.Security/webApplicationFirewalls/write | Cria uma nova firewall de aplicações web ou atualiza um já existente |
> | Ação | Microsoft.Security/workspaceSettings/connect/action | Alterar as definições de restabelecimento de ligação de definições de área de trabalho |
> | Ação | Microsoft.Security/workspaceSettings/delete | Elimina as definições de área de trabalho |
> | Ação | Microsoft.Security/workspaceSettings/read | Obtém as definições de área de trabalho |
> | Ação | Microsoft.Security/workspaceSettings/write | Atualiza as definições de área de trabalho |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.SecurityGraph/diagnosticsettings/delete | A eliminar uma definição de diagnóstico |
> | Ação | Microsoft.SecurityGraph/diagnosticsettings/read | Ler uma definição de diagnóstico |
> | Ação | Microsoft.SecurityGraph/diagnosticsettings/write | Escrever uma definição de diagnóstico |
> | Ação | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Ler um categorias de definição de diagnóstico |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ServiceBus/checkNameAvailability/action | Verifica a disponibilidade do espaço de nomes em determinada subscrição. |
> | Ação | Microsoft.ServiceBus/checkNamespaceAvailability/action | Verifica a disponibilidade do espaço de nomes em determinada subscrição. Esta API foi preterida utilize CheckNameAvailability em vez disso. |
> | Ação | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Elimina as regras de VNet no Fornecedor de Recursos de ServiceBus para a VNet especificada |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/action | Regra de autorização de espaço de nomes de atualizações. Esta API foi preterida. Utilize uma chamada PUT para atualizar a regra de autorização de espaço de nomes em vez disso,.... Esta operação não é suportada na versão 2017-04-01 de API. |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Elimine regra de autorização de espaço de nomes. Não é possível eliminar a regra de autorização de espaço de nomes predefinido.  |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Obter Cadeia de Ligação para o Espaço de nomes |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/read | Obter a lista de descrição das Regras de Autorização de Espaços de nomes. |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/write | Criar regras de autorização ao nível de espaço de nomes e Atualize as respetivas propriedades. Os direitos de acesso de regras de autorização, o principal e as chaves secundárias podem ser atualizadas. |
> | Ação | Microsoft.ServiceBus/namespaces/Delete | Eliminar Recurso de Espaço de nomes |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obtém as chaves das regras de autorização do espaço de nomes principal da Recuperação Após Desastre |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter as Regras de Autorização do Espaço de Nomes Principal da Recuperação Após Desastre |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Desativa a Recuperação Após Desastre e para a replicação de alterações de espaço de nomes principais para secundários. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica a disponibilidade do alias do espaço de nomes numa determinada subscrição. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Elimina a configuração de recuperação após desastre associada no espaço de nomes. Esta operação só pode ser chamada por meio do espaço de nomes primário. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Invoca uma ativação pós-falha GEO DR e reconfigura o alias de espaço de nomes para apontar para o espaço de nomes secundário. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Obtém a configuração da Recuperação Após Desastre associada ao espaço de nomes. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Cria ou Atualiza a configuração da Recuperação Após Desastre associada ao espaço de nomes. |
> | Ação | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Elimina o filtro do Event Grid associado ao espaço de nomes. |
> | Ação | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Obtém o filtro do Event Grid associado ao espaço de nomes. |
> | Ação | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Cria ou Atualiza o filtro do Event Grid associado ao espaço de nomes. |
> | Ação | Microsoft.ServiceBus/namespaces/eventhubs/read | Obter lista de descrições de recursos do EventHub |
> | Ação | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Eliminar Recurso de Filtro de IP |
> | Ação | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Obter o Recurso de Filtro de IP |
> | Ação | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Criar Recurso de Filtro de IP |
> | Ação | Microsoft.ServiceBus/namespaces/messagingPlan/read | Obtém o plano de mensagens para um espaço de nomes.<br>Esta API foi preterida.<br>As propriedades expostas por intermédio do recurso MessagingPlan são movidas para o recurso de espaço de nomes em versões de API posteriores do (principal)....<br>Esta operação não é suportada na versão 2017-04-01 de API. |
> | Ação | Microsoft.ServiceBus/namespaces/messagingPlan/write | Atualiza o plano de mensagens para um espaço de nomes.<br>Esta API foi preterida.<br>As propriedades expostas por intermédio do recurso MessagingPlan são movidas para o recurso de espaço de nomes em versões de API posteriores do (principal)....<br>Esta operação não é suportada na versão 2017-04-01 de API. |
> | Ação | Microsoft.ServiceBus/namespaces/migrate/action | Migrar operação de espaço de nomes |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Elimina a configuração da Migração. |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Obtém a configuração da Migração, o que indica o estado da migração e as operações de replicação pendentes |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Reverte a migração do espaço de nomes padrão para o premium |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Atribui o DNS que está associado ao espaço de nomes padrão ao espaço de nomes premium, o que conclui a migração e para a sincronização de recursos do espaço de nomes padrão para o premium |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Cria ou de configuração de migração de atualizações. Esta ação iniciará a sincronização de recursos do padrão para o espaço de nomes premium |
> | Ação | Microsoft.ServiceBus/namespaces/operationresults/read | Obter o estado da operação de Espaço de nomes |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obter lista de descrições de recursos do espaço de nomes das definições de diagnóstico |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obter lista de descrições de recursos do espaço de nomes das definições de diagnóstico |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obter lista de registos de espaço de nomes descrições de recursos |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de descrições de recursos do espaço de nomes métricas |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operação para atualizar a fila. Esta operação não é suportada na versão 2017-04-01 de API. Regras de autorização. Utilize uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operação para eliminar regras de autorização de fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Obter a cadeia de ligação para a fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Obter a lista de regras de autorização de fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Criar regras de autorização de fila e atualizar as respetivas propriedades. Os direitos de acesso de regras de autorização podem ser atualizados. |
> | Ação | Microsoft.ServiceBus/namespaces/queues/Delete | Operação para eliminar o recurso de fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/read | Obter lista de descrições de recursos da fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/write | Criar ou propriedades de fila de atualizações. |
> | Ação | Microsoft.ServiceBus/namespaces/read | Obter a lista de Descrição de Recursos de Espaço de nomes |
> | Ação | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Remova o espaço de nomes do ACS |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Operação para atualizar o tópico. Esta operação não é suportada na versão 2017-04-01 de API. Regras de autorização. Utilize uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Operação para eliminar regras de autorização de tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Obter a cadeia de ligação para o tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Obter a lista de regras de autorização de tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Regenerar a chave Primária ou Secundária do Recurso |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Criar regras de autorização de tópico e atualizar as respetivas propriedades. Os direitos de acesso de regras de autorização podem ser atualizados. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/Delete | Operação para eliminar o recurso de tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/read | Obter lista de descrições de recursos de tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operação para eliminar o recurso de TopicSubscription |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Obter lista de descrições de recursos de TopicSubscription |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operação para eliminar o recurso de regra |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Obter lista de descrições de recursos de regra |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Criar ou propriedades da regra de atualização. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Criar ou propriedades de TopicSubscription de atualização. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/write | Criar ou propriedades de tópico de atualização. |
> | Ação | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Eliminar Recurso de Regra de VNET |
> | Ação | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Obtém o Recurso de Regra de VNET |
> | Ação | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Criar Recurso de Regra de VNET |
> | Ação | Microsoft.ServiceBus/namespaces/write | Criar um recurso de espaço de nomes e Atualize as respetivas propriedades. As etiquetas e a capacidade do espaço de nomes são propriedades que podem ser atualizadas. |
> | Ação | Microsoft.ServiceBus/operations/read | Obter Operações |
> | Ação | Microsoft.ServiceBus/register/action | Regista a subscrição do fornecedor de recursos do ServiceBus e ativa a criação de recursos do ServiceBus |
> | Ação | Microsoft.ServiceBus/sku/read | Obter lista de descrições de recursos de Sku |
> | Ação | Microsoft.ServiceBus/sku/regions/read | Obter lista de descrições de recursos de SkuRegions |
> | Ação | Microsoft.ServiceBus/unregister/action | Regista a subscrição do fornecedor de recursos do ServiceBus e ativa a criação de recursos do ServiceBus |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ServiceFabric/clusters/applications/delete | Eliminar qualquer Aplicação |
> | Ação | Microsoft.ServiceFabric/clusters/applications/read | Ler qualquer Aplicação |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/delete | Eliminar qualquer Serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Ler qualquer Partição |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Ler qualquer Réplica |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/read | Ler qualquer Serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Ler qualquer Estado do Serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/write | Criar ou Atualizar qualquer Serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/write | Criar ou Atualizar qualquer Aplicação |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Eliminar qualquer Tipo de Aplicação |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/read | Ler qualquer Tipo de Aplicação |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Eliminar qualquer Versão do Tipo de Aplicação |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Ler qualquer Versão do Tipo de Aplicação |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Criar ou Atualizar qualquer Versão do Tipo de Aplicação |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/write | Criar ou Atualizar qualquer Tipo de Aplicação |
> | Ação | Microsoft.ServiceFabric/clusters/delete | Eliminar qualquer Cluster |
> | Ação | Microsoft.ServiceFabric/clusters/nodes/read | Ler qualquer Nó |
> | Ação | Microsoft.ServiceFabric/clusters/read | Ler qualquer Cluster |
> | Ação | Microsoft.ServiceFabric/clusters/statuses/read | Ler qualquer Estado do Cluster |
> | Ação | Microsoft.ServiceFabric/clusters/write | Criar ou Atualizar qualquer Cluster |
> | Ação | Microsoft.ServiceFabric/locations/clusterVersions/read | Ler qualquer Versão de Cluster |
> | Ação | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Ler qualquer Versão de Cluster de um ambiente específico |
> | Ação | Microsoft.ServiceFabric/locations/operationresults/read | Ler quaisquer Resultados da Operação |
> | Ação | Microsoft.ServiceFabric/locations/operations/read | Ler quaisquer Operações por localização |
> | Ação | Microsoft.ServiceFabric/operations/read | Ler quaisquer Operações Disponíveis |
> | Ação | Microsoft.ServiceFabric/register/action | Registar qualquer Ação |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.SignalRService/locations/checknameavailability/action | Verifica se um nome está disponível para utilização com um novo serviço SignalR |
> | Ação | Microsoft.SignalRService/locations/operationresults/signalr/read | Consultar o estado de uma operação de asynchorous |
> | Ação | Microsoft.SignalRService/locations/usages/read | Obter as utilizações de quota para o serviço Azure SignalR |
> | Ação | Microsoft.SignalRService/operationresults/read | Consultar o estado de uma operação de asynchorous |
> | Ação | Microsoft.SignalRService/register/action | Regista o fornecedor de recursos de 'Microsoft.SignalRService' com uma subscrição |
> | Ação | Microsoft.SignalRService/SignalR/delete | Eliminar todo o serviço SignalR |
> | Ação | Microsoft.SignalRService/SignalR/listFeatures/read | Lista personalizada FeatureFlags de um serviço de SignalR. Valores excluídos do padrão. |
> | Ação | Microsoft.SignalRService/SignalR/listkeys/action | Ver o valor das chaves de acesso do SignalR, no portal de gestão ou por meio da API |
> | Ação | Microsoft.SignalRService/SignalR/read | Ver definições e configurações do SignalR, no portal de gestão ou por meio da API |
> | Ação | Microsoft.SignalRService/SignalR/regeneratekey/action | Altere o valor das chaves de acesso do SignalR no portal de gestão ou por meio da API |
> | Ação | Microsoft.SignalRService/SignalR/restart/action | Para reiniciar um serviço Azure SignalR no portal de gestão ou por meio da API. Haverá um determinado período de indisponibilidade. |
> | Ação | Microsoft.SignalRService/SignalR/switchFeatures/action | Alternar/desativar SignalR FeatureFlags com propriedades suportadas no portal de gestão ou por meio da API. |
> | Ação | Microsoft.SignalRService/SignalR/write | Modificar as definições e configurações no portal de gestão ou por meio da API do SignalR |
> | Ação | Microsoft.SignalRService/unregister/action | Anula o registo do fornecedor de recursos de 'Microsoft.SignalRService' com uma subscrição |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Solutions/applicationDefinitions/delete | Remove uma definição de aplicação. |
> | Ação | Microsoft.Solutions/applicationDefinitions/read | Obtém uma lista de definições de aplicação. |
> | Ação | Microsoft.Solutions/applicationDefinitions/write | Adicionar ou modificar uma definição de aplicação. |
> | Ação | Microsoft.Solutions/applications/delete | Remove uma aplicação. |
> | Ação | Microsoft.Solutions/applications/read | Obtém uma lista de aplicações. |
> | Ação | Microsoft.Solutions/applications/write | Cria uma aplicação. |
> | Ação | Microsoft.Solutions/jitRequests/delete | Remover um JitRequest |
> | Ação | Microsoft.Solutions/jitRequests/read | Obtém uma lista de JitRequests |
> | Ação | Microsoft.Solutions/jitRequests/write | Cria um JitRequest |
> | Ação | Microsoft.Solutions/locations/operationStatuses/read | Lê o estado da operação do recurso. |
> | Ação | Microsoft.Solutions/register/action | Registe-se nas Soluções. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Sql/checkNameAvailability/action | Certifique-se de que se fornecido server nome está disponível para o aprovisionamento em todo o mundo para uma determinada subscrição. |
> | Ação | Microsoft.Sql/instancePools/delete | Elimina um conjunto de instância |
> | Ação | Microsoft.Sql/instancePools/read | Obtém um conjunto de instância |
> | Ação | Microsoft.Sql/instancePools/usages/read | Obtém uma informação de utilizações de agrupamento de instância. |
> | Ação | Microsoft.Sql/instancePools/write | Cria ou atualiza um conjunto de instância |
> | Ação | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Obter o resultado do blob de servidor expandida operação de definição de política de auditoria |
> | Ação | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Obter Resultado da operação de definição de política de auditoria o blob de servidor |
> | Ação | Microsoft.Sql/locations/capabilities/read | Obtém os recursos para esta subscrição numa localização determinada |
> | Ação | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Obtém o estado de uma operação de base de dados. |
> | Ação | Microsoft.Sql/locations/databaseOperationResults/read | Obtém o estado de uma operação de base de dados. |
> | Ação | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Obtém em curso operações no servidor foi eliminado |
> | Ação | Microsoft.Sql/locations/deletedServerOperationResults/read | Obtém em curso operações no servidor foi eliminado |
> | Ação | Microsoft.Sql/locations/deletedServers/read | Devolve a lista de servidores foi eliminadas ou obtém as propriedades para o servidor especificado foi eliminado. |
> | Ação | Microsoft.Sql/locations/deletedServers/recover/action | Recuperar um servidor foi eliminado |
> | Ação | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Obtém a operação assíncrona do azure para uma operação assíncrona do conjunto elástico |
> | Ação | Microsoft.Sql/locations/elasticPoolOperationResults/read | Obtém o resultado de uma operação de conjunto elástico. |
> | Ação | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Obter o resultado do blob de servidor expandida operação de definição de política de auditoria |
> | Ação | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Obter o resultado do blob de servidor expandida operação de definição de política de auditoria |
> | Ação | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Obtém o estado de uma operação de regra de firewall. |
> | Ação | Microsoft.Sql/locations/firewallRulesOperationResults/read | Obtém o estado de uma operação de regra de firewall. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/delete | Elimina um grupo de ativação pós-falha de instância existente. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Executa a ativação pós-falha planeada num grupo de ativação pós-falha de instância existente. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Executa a ativação pós-falha forçada num grupo de ativação pós-falha de instância existente. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/read | Devolve a lista de ativação pós-falha de instância de grupos ou obtém as propriedades para o grupo de ativação pós-falha de instância especificada. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/write | Cria um grupo de ativação pós-falha de instância com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o grupo de ativação pós-falha de instância especificada. |
> | Ação | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Obtém o estado de uma operação de agrupamento de instância. |
> | Ação | Microsoft.Sql/locations/instancePoolOperationResults/read | Obtém o resultado de uma operação de agrupamento de instância. |
> | Ação | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Devolve os detalhes de um ponto final de interface específica operação assíncrona do Azure |
> | Ação | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Devolve os detalhes da operação de perfil do ponto final de interface especificada |
> | Ação | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Obtém o estado de uma operação do agente de tarefa. |
> | Ação | Microsoft.Sql/locations/jobAgentOperationResults/read | Obtém o resultado de uma operação do agente de tarefa. |
> | Ação | Microsoft.Sql/locations/longTermRetentionBackups/read | Lista as cópias de segurança de retenção de longo prazo para cada base de dados em cada servidor num local |
> | Ação | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Lista as cópias de segurança de retenção de longo prazo para cada base de dados num servidor |
> | Ação | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Elimina uma cópia de segurança de retenção de longo prazo |
> | Ação | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Lista as cópias de segurança de retenção de longo prazo para uma base de dados |
> | Ação | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Conclusão da operação de restauro de base de dados gerida |
> | Ação | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Obtém em curso operações de criptografia de dados transparente de base de dados gerida |
> | Ação | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Obtém em curso operações de criptografia de dados transparente de base de dados gerida |
> | Ação | Microsoft.Sql/locations/read | Obtém as localizações disponíveis para uma determinada subscrição |
> | Ação | Microsoft.Sql/locations/syncAgentOperationResults/read | Obter Resultado da operação de recursos do agente de sincronização |
> | Ação | Microsoft.Sql/locations/syncDatabaseIds/read | Obter os ids de base de dados de sincronização para uma determinada região e subscrição |
> | Ação | Microsoft.Sql/locations/syncGroupOperationResults/read | Obter Resultado da operação de recursos do grupo de sincronização |
> | Ação | Microsoft.Sql/locations/syncMemberOperationResults/read | Obter Resultado da operação de recursos de membro de sincronização |
> | Ação | Microsoft.Sql/locations/usages/read | Obtém uma coleção de métrica de utilização para esta subscrição numa localização |
> | Ação | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Devolve os detalhes das regras de rede virtual especificada operação assíncrona do azure  |
> | Ação | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Devolve os detalhes da operação de regras de rede virtual especificada  |
> | Ação | Microsoft.Sql/managedInstances/administrators/delete | Elimina um administrador existente de instância gerida. |
> | Ação | Microsoft.Sql/managedInstances/administrators/read | Obtém uma lista de administradores de instância gerida. |
> | Ação | Microsoft.Sql/managedInstances/administrators/write | Cria ou atualiza o administrador de instância gerida com os parâmetros especificados. |
> | Ação | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para uma base de dados gerida |
> | Ação | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para uma base de dados gerida |
> | Ação | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Etiquetas de sensibilidade da lista de um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/delete | Elimina uma base de dados gerida existente |
> | Ação | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Ação | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Ação | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para bases de dados de instância gerida |
> | Ação | Microsoft.Sql/managedInstances/databases/read | Obtém existente a base de dados gerida |
> | Ação | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Etiquetas de sensibilidade da lista de um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Eliminar a etiqueta de sensibilidade de uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Desativar as recomendações de sensibilidade sobre uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Ativar as recomendações de sensibilidade sobre uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Obter a etiqueta de sensibilidade de uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Criar ou atualizar a etiqueta de sensibilidade de uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Obter os detalhes da política de deteção de ameaças da base de dados configuradas numa determinada base de dados gerida |
> | Ação | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Alterar a política de deteção de ameaças da base de dados para uma determinada base de dados gerida |
> | Ação | Microsoft.Sql/managedInstances/databases/securityEvents/read | Obtém os eventos de segurança da base de dados gerida |
> | Ação | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Etiquetas de sensibilidade da lista de um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Obter os detalhes da base de dados transparente de encriptação de dados numa determinada base de dados gerida |
> | Ação | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Alterar a base de dados transparente de dados de encriptação para uma determinada base de dados gerida |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidades para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Obter as políticas de avaliação de vulnerabilidade num givendatabase |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Remover a linha de base da regra de avaliação vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Obter a linha de base de regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Altere a linha de base da regra de avaliação vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Converta um resultado de análise existente para um formato legível humano. Se já existe nada acontece |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Execute a análise de base de dados de avaliação de vulnerabilidade. |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Devolver a lista de vulnerabilidade de banco de dados de registos de análise de avaliação ou obter o registro de análise para o ID especificado de análise. |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidades para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/write | Cria uma nova base de dados ou atualiza a base de dados existente. |
> | Ação | Microsoft.Sql/managedInstances/delete | Elimina uma instância gerida existente. |
> | Ação | Microsoft.Sql/managedInstances/encryptionProtector/read | Devolve uma lista de protetores de encriptação do servidor ou obtém as propriedades para o servidor especificado protetor de encriptação. |
> | Ação | Microsoft.Sql/managedInstances/encryptionProtector/write | Atualize as propriedades para o Protetor de encriptação de servidor especificado. |
> | Ação | Microsoft.Sql/managedInstances/keys/delete | Elimina uma chave de instância gerida do SQL do Azure existente. |
> | Ação | Microsoft.Sql/managedInstances/keys/read | Devolve a lista de instância gerida de chaves ou obtém as propriedades da chave de instância gerida especificada. |
> | Ação | Microsoft.Sql/managedInstances/keys/write | Cria uma chave com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a chave de instância gerida especificada. |
> | Ação | Microsoft.Sql/managedInstances/metricDefinitions/read | Obter definições das métricas de instância gerida |
> | Ação | Microsoft.Sql/managedInstances/metrics/read | Obter métricas de instância gerida |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para instâncias geridas |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para instâncias geridas |
> | Ação | Microsoft.Sql/managedInstances/read | Devolve a lista de instâncias geridas ou obtém as propriedades para a instância gerida especificada. |
> | Ação | Microsoft.Sql/managedInstances/recoverableDatabases/read | Devolve uma lista de bases de dados geridas recuperáveis |
> | Ação | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para uma base de dados gerida |
> | Ação | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para uma base de dados gerida |
> | Ação | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Devolve que uma lista de restaurável remover bases de dados geridas. |
> | Ação | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Obter os detalhes da política de deteção de ameaças do servidor gerido configurado num determinado servidor gerido |
> | Ação | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Alterar a política de deteção de ameaças do servidor gerido para um determinado servidor gerido |
> | Ação | Microsoft.Sql/managedInstances/tdeCertificates/action | Certificado de TDE criar/atualizar |
> | Ação | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidades para uma determinada instância gerida |
> | Ação | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Obter as políticas de avaliação de vulnerabilidade numa dada instância gerida |
> | Ação | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidades para uma determinada instância gerida |
> | Ação | Microsoft.Sql/managedInstances/write | Cria uma instância gerida com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a instância gerida especificada. |
> | Ação | Microsoft.Sql/operations/read | Obtém as operações de REST disponíveis |
> | Ação | Microsoft.Sql/register/action | Regista a subscrição para o fornecedor de recursos da base de dados do Microsoft SQL e permite a criação de bases de dados do Microsoft SQL. |
> | Ação | Microsoft.Sql/servers/administratorOperationResults/read | Obtém em curso operações em administradores de servidor |
> | Ação | Microsoft.Sql/servers/administrators/delete | Eliminar o administrador do servidor |
> | Ação | Microsoft.Sql/servers/administrators/read | Obter detalhes de administrador do servidor |
> | Ação | Microsoft.Sql/servers/administrators/write | Criar ou atualizar o administrador do servidor |
> | Ação | Microsoft.Sql/servers/advisors/read | Devolve a lista de assistentes disponíveis para o servidor |
> | Ação | Microsoft.Sql/servers/advisors/recommendedActions/read | Devolve a lista de ações recomendadas do advisor especificado para o servidor |
> | Ação | Microsoft.Sql/servers/advisors/recommendedActions/write | Aplicar a ação recomendada no servidor |
> | Ação | Microsoft.Sql/servers/advisors/write | Atualizações de autom-execução estado de um assistente no nível do servidor. |
> | Ação | Microsoft.Sql/servers/auditingPolicies/read | Obter os detalhes da tabela de servidor padrão configurada num determinado servidor de política de auditoria |
> | Ação | Microsoft.Sql/servers/auditingPolicies/write | Alterar a predefinição servidor auditoria de tabelas para um determinado servidor |
> | Ação | Microsoft.Sql/servers/auditingSettings/operationResults/read | Obter Resultado da operação de definição de política de auditoria o blob de servidor |
> | Ação | Microsoft.Sql/servers/auditingSettings/read | Obter os detalhes da política auditoria de BLOBs de servidor configurado num determinado servidor |
> | Ação | Microsoft.Sql/servers/auditingSettings/write | Alterar a auditoria de BLOBs de servidor para um determinado servidor |
> | Ação | Microsoft.Sql/servers/automaticTuning/read | Devolve as definições de sintonização automática para o servidor |
> | Ação | Microsoft.Sql/servers/automaticTuning/write | Atualizações de definições de sintonização automática para o servidor e devolve as definições atualizadas |
> | Ação | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Elimina um cofre de arquivamento de cópia de segurança existente. |
> | Ação | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Esta operação é utilizada para obter um cofre de retenção de cópia de segurança de longo prazo. Devolve informações sobre o Cofre registado este servidor |
> | Ação | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Esta operação é utilizada para registar uma retenção de cópia de segurança de longo prazo cofre para um servidor |
> | Ação | Microsoft.Sql/servers/communicationLinks/delete | Elimina uma ligação de comunicação de servidor existente. |
> | Ação | Microsoft.Sql/servers/communicationLinks/read | Devolve a lista de ligações de comunicação de um servidor especificado. |
> | Ação | Microsoft.Sql/servers/communicationLinks/write | Criar ou atualizar uma ligação de comunicação do servidor. |
> | Ação | Microsoft.Sql/servers/connectionPolicies/read | Devolve a lista de políticas de ligação do servidor de um servidor especificado. |
> | Ação | Microsoft.Sql/servers/connectionPolicies/write | Criar ou atualizar uma política de ligação do servidor. |
> | Ação | Microsoft.Sql/servers/databases/advisors/read | Devolve a lista de assistentes disponíveis para a base de dados |
> | Ação | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Devolve a lista de ações recomendadas do advisor especificado para a base de dados |
> | Ação | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Aplicar a ação recomendada na base de dados |
> | Ação | Microsoft.Sql/servers/databases/advisors/write | Atualização automática-executar o estado de um assistente no nível da base de dados. |
> | Ação | Microsoft.Sql/servers/databases/auditingPolicies/read | Obter os detalhes da política de auditoria de tabela configurados numa determinada base de dados |
> | Ação | Microsoft.Sql/servers/databases/auditingPolicies/write | Alterar a política de auditoria de tabela para uma determinada base de dados |
> | Ação | Microsoft.Sql/servers/databases/auditingSettings/read | Obter os detalhes da política de auditoria de BLOBs configurados numa determinada base de dados |
> | Ação | Microsoft.Sql/servers/databases/auditingSettings/write | Alterar a política de auditoria de BLOBs para uma determinada base de dados |
> | Ação | Microsoft.Sql/servers/databases/auditRecords/read | Obter os registos de auditoria de Blobs do banco de dados |
> | Ação | Microsoft.Sql/servers/databases/automaticTuning/read | Devolve as definições de sintonização automática para uma base de dados |
> | Ação | Microsoft.Sql/servers/databases/automaticTuning/write | Atualizações de definições de sintonização automática para uma base de dados e retorna as definições atualizadas |
> | Ação | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Obtém o estado de uma operação de base de dados. |
> | Ação | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Devolve a lista de políticas de arquivamento de cópia de segurança de uma base de dados especificada. |
> | Ação | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Criar ou atualizar uma política de arquivo de cópia de segurança da base de dados. |
> | Ação | Microsoft.Sql/servers/databases/connectionPolicies/read | Obter os detalhes da política de ligação configurada num determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/connectionPolicies/write | Alterar a política de ligação de um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Etiquetas de sensibilidade da lista de um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Devolve a lista de políticas de máscara de dados de base de dados. |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Eliminar regra de política para um determinado banco de dados de máscara de dados |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Obter os detalhes dos regra de política configurada num determinado banco de dados de máscara de dados |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Regra de política para um determinado banco de dados de máscara de dados de alteração |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Alterar a política para um determinado banco de dados de máscara de dados |
> | Ação | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Devolve as informações de passo de consulta distribuída da consulta de armazém de dados para o ID do passo selecionado |
> | Ação | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Devolve as informações da consulta de distribuição de armazém de dados para o ID de consulta selecionada |
> | Ação | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Obtém as atividades do utilizador de uma instância do SQL Data Warehouse que inclui as consultas em execução e suspensas |
> | Ação | Microsoft.Sql/servers/databases/delete | Elimina uma base de dados existente. |
> | Ação | Microsoft.Sql/servers/databases/export/action | Exportar base de dados SQL do Azure |
> | Ação | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Obter os detalhes da política de auditoria de blob expandido configurados numa determinada base de dados |
> | Ação | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Alterar a política de auditoria de blob expandido para uma determinada base de dados |
> | Ação | Microsoft.Sql/servers/databases/extensions/read | Obtém uma coleção de extensões para a base de dados. |
> | Ação | Microsoft.Sql/servers/databases/extensions/write | Altere a extensão para uma determinada base de dados |
> | Ação | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Obter as políticas de cópia de segurança geo para uma determinada base de dados |
> | Ação | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Criar ou atualizar uma política de geobackup de base de dados |
> | Ação | Microsoft.Sql/servers/databases/importExportOperationResults/read | Obtém em curso operações de importação/exportação |
> | Ação | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Obtém uma lista de janelas de manutenção disponíveis para uma base de dados selecionada. |
> | Ação | Microsoft.Sql/servers/databases/maintenanceWindows/read | Obtém a manutenção definições do windows para uma base de dados selecionada. |
> | Ação | Microsoft.Sql/servers/databases/maintenanceWindows/write | Define configurações do windows para uma base de dados selecionada de manutenção. |
> | Ação | Microsoft.Sql/servers/databases/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados |
> | Ação | Microsoft.Sql/servers/databases/metrics/read | Métricas de retornadas de bases de dados |
> | Ação | Microsoft.Sql/servers/databases/move/action | Mudar o nome da base de dados SQL do Azure |
> | Ação | Microsoft.Sql/servers/databases/operationResults/read | Obtém o estado de uma operação de base de dados. |
> | Ação | Microsoft.Sql/servers/databases/operations/cancel/action | Cancela a base de dados do Azure SQL pendentes operação assíncrona que ainda não tenha terminada. |
> | Ação | Microsoft.Sql/servers/databases/operations/read | Devolver a lista de operações executadas na base de dados |
> | Ação | Microsoft.Sql/servers/databases/pause/action | Base de dados de armazém de dados SQL do Azure de colocar em pausa |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para bases de dados |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados |
> | Ação | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Devolve a coleção de textos de consulta que correspondem aos parâmetros especificados. |
> | Ação | Microsoft.Sql/servers/databases/queryStore/read | Devolve os valores atuais das definições de consulta Store para a base de dados. |
> | Ação | Microsoft.Sql/servers/databases/queryStore/write | Atualizações de definição de consulta Store para a base de dados |
> | Ação | Microsoft.Sql/servers/databases/read | Devolve a lista de bases de dados ou obtém as propriedades da base de dados especificado. |
> | Ação | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Etiquetas de sensibilidade da lista de um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/delete | Terminar a relação de replicação forçadamente e com potencial perda de dados |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Ativação pós-falha após a sincronização de tudo é alterado de primário, tornar esta base de dados em relationship\u0027s os replicação primário e tornar remoto primário para uma secundária |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Ativação pós-falha imediatamente com potencial perda de dados, tornando esta base de dados em relationship\u0027s os replicação primária e tornar remoto primário para uma secundária |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/read | Devolve a lista de replicação liga ou obtém as propriedades para as ligações de replicação especificada. |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Terminar a relação de replicação forçadamente ou após a sincronização com o parceiro |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Atualizar o modo de replicação para a ligação para o modo síncrono ou assíncrono |
> | Ação | Microsoft.Sql/servers/databases/restorePoints/action | Cria um novo ponto de restauro |
> | Ação | Microsoft.Sql/servers/databases/restorePoints/delete | Elimina um ponto de restauro da base de dados. |
> | Ação | Microsoft.Sql/servers/databases/restorePoints/read | Devolve os pontos para a base de dados de restauro. |
> | Ação | Microsoft.Sql/servers/databases/resume/action | Base de dados de armazém de dados SQL do Azure de retomar |
> | Ação | Microsoft.Sql/servers/databases/schemas/read | Obter a lista de esquemas das bases de dados |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Obter a lista de colunas de uma tabela |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Eliminar a etiqueta de sensibilidade de uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Desativar as recomendações de sensibilidade sobre uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Ativar as recomendações de sensibilidade sobre uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Obter a etiqueta de sensibilidade de uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Criar ou atualizar a etiqueta de sensibilidade de uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/read | Obter a lista de tabelas de uma base de dados |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Obter a lista de recomendações de índice numa base de dados |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Aplicar a recomendação do índice |
> | Ação | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Obter os detalhes da política de deteção de ameaças da base de dados configuradas num determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Alterar a política de deteção de ameaças da base de dados para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/securityMetrics/read | Obtém uma coleção de métricas de segurança da base de dados |
> | Ação | Microsoft.Sql/servers/databases/sensitivityLabels/read | Etiquetas de sensibilidade da lista de um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Devolver a sugestão sobre o dimensionamento da base de dados ou diminua verticalmente com base em estatísticas de execução de consulta para melhorar o desempenho ou reduzir os custos |
> | Ação | Microsoft.Sql/servers/databases/skus/read | Obtém uma coleção de skus disponíveis para uma base de dados |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Cancelar Sincronização do grupo de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/delete | Elimina um grupo de sincronização existente. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Devolver a lista de sincronização de esquemas de banco de dados de hub |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/logs/read | Devolver a lista de registos do grupo de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/read | Devolve a lista de sincronização de grupos ou obtém as propriedades para o grupo de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Atualizar o esquema de banco de dados do hub de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Obter Resultado da operação de atualização de esquema de sincronização hub |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Elimina um membro de sincronização existente. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Devolve a lista de membros de sincronização ou obtém as propriedades para o membro de sincronização especificada. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Atualizar o esquema de membro de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Obter Resultado da operação de atualização do esquema de membro de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Devolver a lista de sincronização de esquemas de banco de dados do membro |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Cria um membro de sincronização com os parâmetros especificados ou atualizar as propriedades para o membro de sincronização especificada. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Sincronização do grupo de sincronização de Acionador |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/write | Cria um grupo de sincronização com os parâmetros especificados ou atualiza as propriedades para o grupo de sincronização especificada. |
> | Ação | Microsoft.Sql/servers/databases/topQueries/queryText/action | Devolve o texto de Transact-SQL para o ID de consulta selecionada |
> | Ação | Microsoft.Sql/servers/databases/topQueries/read | Devolve agregado estatísticas de tempo de execução de consulta selecionada no período de tempo selecionado |
> | Ação | Microsoft.Sql/servers/databases/topQueries/statistics/read | Devolve agregado estatísticas de tempo de execução de consulta selecionada no período de tempo selecionado |
> | Ação | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Obtém em curso operações de criptografia de dados transparente |
> | Ação | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Obter o estado e os detalhes do recurso de segurança de criptografia transparente de dados para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Alterar o estado de encriptação de dados transparente |
> | Ação | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Atualizar a base de dados do armazém de dados SQL do Azure |
> | Ação | Microsoft.Sql/servers/databases/usages/read | Obtém as informações de utilizações de base de dados do Azure SQL |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidades para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Obter as políticas de avaliação de vulnerabilidade num givendatabase |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Remover a linha de base da regra de avaliação vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Obter a linha de base de regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Altere a linha de base da regra de avaliação vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Converta um resultado de análise existente para um formato legível humano. Se já existe nada acontece |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Execute a análise de base de dados de avaliação de vulnerabilidade. |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Devolver a lista de vulnerabilidade de banco de dados de registos de análise de avaliação ou obter o registro de análise para o ID especificado de análise. |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidades para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Execute a análise de base de dados de avaliação de vulnerabilidade. |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Obter o resultado da análise de avaliação de vulnerabilidade da base de dados a operação de execução |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Obter os detalhes da avaliação de vulnerabilidades configurados numa determinada base de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Alterar a avaliação de vulnerabilidades para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/write | Cria uma base de dados com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a base de dados especificada. |
> | Ação | Microsoft.Sql/servers/delete | Elimina um servidor existente. |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Elimina um configurações de recuperação após desastre existente para um determinado servidor |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Ativação pós-falha de um DisasterRecoveryConfiguration |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Forçar a ativação pós-falha um DisasterRecoveryConfiguration |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Obtém uma coleção de desastre configurações de recuperação que incluem este servidor |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Alterar a configuração de recuperação de desastres server |
> | Ação | Microsoft.Sql/servers/elasticPoolEstimates/read | Devolve a lista de estimativas de conjunto elástico já criada para este servidor |
> | Ação | Microsoft.Sql/servers/elasticPoolEstimates/write | Cria nova estimativa de conjunto elástico para obter a lista de bases de dados fornecido |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/read | Devolve a lista de assistentes disponíveis para o conjunto elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Devolve a lista de ações recomendadas do advisor especificado para o conjunto elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Aplicar a ação recomendada no conjunto elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/write | Atualização automática-executar o estado de um assistente no nível de conjunto elástico. |
> | Ação | Microsoft.Sql/servers/elasticPools/databases/read | Obtém uma lista de bases de dados de um conjunto elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/delete | Eliminar o conjunto elástico existente |
> | Ação | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Obter as atividades e detalhes sobre um conjunto de bases de dados elásticas determinado |
> | Ação | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Obter as atividades e detalhes sobre um determinado banco de dados que faz parte do conjunto de bases de dados elásticas |
> | Ação | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados elásticas |
> | Ação | Microsoft.Sql/servers/elasticPools/metrics/read | Métricas de retornadas de conjuntos de bases de dados elásticas |
> | Ação | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Cancela o conjunto elástico de SQL do Azure pendentes operação assíncrona que ainda não tenha terminado. |
> | Ação | Microsoft.Sql/servers/elasticPools/operations/read | Devolver a lista de operações executadas no conjunto elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a definição de diagnóstico do recurso |
> | Ação | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a definição do diagnóstico para o recurso |
> | Ação | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Tipos de retorno da métricas que estão disponíveis para bases de dados elásticas |
> | Ação | Microsoft.Sql/servers/elasticPools/read | Obter os detalhes de um conjunto elástico num determinado servidor |
> | Ação | Microsoft.Sql/servers/elasticPools/skus/read | Obtém uma coleção de skus disponíveis para um conjunto elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/write | Criar um novo ou alterar as propriedades do conjunto elástico existente |
> | Ação | Microsoft.Sql/servers/encryptionProtector/read | Devolve uma lista de protetores de encriptação do servidor ou obtém as propriedades para o servidor especificado protetor de encriptação. |
> | Ação | Microsoft.Sql/servers/encryptionProtector/write | Atualize as propriedades para o Protetor de encriptação de servidor especificado. |
> | Ação | Microsoft.Sql/servers/extendedAuditingSettings/read | Obter os detalhes do blob de servidor expandida configurada num determinado servidor de política de auditoria |
> | Ação | Microsoft.Sql/servers/extendedAuditingSettings/write | Alterar a auditoria de BLOBs de servidor expandida para um determinado servidor |
> | Ação | Microsoft.Sql/servers/failoverGroups/delete | Elimina um grupo de ativação pós-falha existente. |
> | Ação | Microsoft.Sql/servers/failoverGroups/failover/action | Executa a ativação pós-falha planeada num grupo de ativação pós-falha existente. |
> | Ação | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Executa a ativação pós-falha forçada num grupo de ativação pós-falha existente. |
> | Ação | Microsoft.Sql/servers/failoverGroups/read | Devolve a lista de ativação pós-falha de grupos ou obtém as propriedades para o grupo de ativação pós-falha especificado. |
> | Ação | Microsoft.Sql/servers/failoverGroups/write | Cria um grupo de ativação pós-falha com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o grupo de ativação pós-falha especificado. |
> | Ação | Microsoft.Sql/servers/firewallRules/delete | Elimina uma regra de firewall do servidor existente. |
> | Ação | Microsoft.Sql/servers/firewallRules/read | Devolve a lista de firewall do servidor de regras ou obtém as propriedades para o servidor especificado de regra de firewall. |
> | Ação | Microsoft.Sql/servers/firewallRules/write | Cria uma regra de firewall do servidor com os parâmetros especificados, atualizar as propriedades para a regra especificada ou substituir todas as regras existentes com a nova regra ou regras de firewall de servidor. |
> | Ação | Microsoft.Sql/servers/import/action | Criar uma nova base de dados no servidor e implementar o esquema e dados a partir de um pacote de DacPac |
> | Ação | Microsoft.Sql/servers/importExportOperationResults/read | Obtém em curso operações de importação/exportação |
> | Ação | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Elimina o perfil de ponto final da interface especificada |
> | Ação | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Devolve as propriedades para o perfil de ponto final da interface especificada |
> | Ação | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Cria um perfil de ponto final de interface com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o ponto final de interface especificada |
> | Ação | Microsoft.Sql/servers/jobAgents/delete | Elimina um agente de tarefa de BD SQL do Azure |
> | Ação | Microsoft.Sql/servers/jobAgents/read | Obtém um agente de tarefa de BD SQL do Azure |
> | Ação | Microsoft.Sql/servers/jobAgents/write | Cria ou atualiza um agente de tarefa de BD SQL do Azure |
> | Ação | Microsoft.Sql/servers/keys/delete | Elimina uma chave de servidor existente. |
> | Ação | Microsoft.Sql/servers/keys/read | Devolve a lista do servidor de chaves ou obtém as propriedades da chave de servidor especificado. |
> | Ação | Microsoft.Sql/servers/keys/write | Cria uma chave com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a chave de servidor especificado. |
> | Ação | Microsoft.Sql/servers/operationResults/read | Obtém em curso operações de servidor |
> | Ação | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Tipos de métricas que estão disponíveis para os servidores de retorno |
> | Ação | Microsoft.Sql/servers/read | Devolve a lista de servidores ou obtém as propriedades para o servidor especificado. |
> | Ação | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Obter métricas para os conjuntos de bases de dados elásticas recomendados para um determinado servidor |
> | Ação | Microsoft.Sql/servers/recommendedElasticPools/read | Obter a recomendação para conjuntos de bases de dados elásticas reduzir o custo ou melhorar o desempenho com base na utilização de recursos histórica |
> | Ação | Microsoft.Sql/servers/recoverableDatabases/read | Esta operação é utilizada para recuperação após desastre da base de dados em direto para restaurar a base de dados para o último conhecido bom ponto de cópia de segurança. Devolve informações sobre a última cópia de segurança de boa, mas ele doesn\u0027t realmente restaurar a base de dados. |
> | Ação | Microsoft.Sql/servers/replicationLinks/read | Devolve a lista de replicação liga ou obtém as propriedades para as ligações de replicação especificada. |
> | Ação | Microsoft.Sql/servers/restorableDroppedDatabases/read | Obter uma lista de bases de dados que foram ignorados num determinado servidor ainda dentro de política de retenção. |
> | Ação | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Obter resultados da operação de escrita de política de deteção de ameaças de servidor |
> | Ação | Microsoft.Sql/servers/securityAlertPolicies/read | Obter os detalhes da política de deteção de ameaças do servidor configurada num determinado servidor |
> | Ação | Microsoft.Sql/servers/securityAlertPolicies/write | Alterar a política de deteção de ameaças do servidor para um determinado servidor |
> | Ação | Microsoft.Sql/servers/serviceObjectives/read | Obter a lista de serviço os objetivos de nível (também conhecido como escalões de desempenho) disponíveis num determinado servidor |
> | Ação | Microsoft.Sql/servers/syncAgents/delete | Elimina um agente de sincronização existente. |
> | Ação | Microsoft.Sql/servers/syncAgents/generateKey/action | Gerar a chave de registo do agente de sincronização |
> | Ação | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Devolver a lista de sincronizar bases de dados do agente ligado |
> | Ação | Microsoft.Sql/servers/syncAgents/read | Devolve a lista de agentes de sincronização ou obtém as propriedades para o agente de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/syncAgents/write | Cria um agente de sincronização com os parâmetros especificados ou atualiza as propriedades para o agente de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/tdeCertificates/action | Certificado de TDE criar/atualizar |
> | Ação | Microsoft.Sql/servers/usages/read | Devolver a quota de DTU de servidor e consuption DTU atual por todas as bases de dados dentro do servidor |
> | Ação | Microsoft.Sql/servers/virtualNetworkRules/delete | Elimina uma regra de rede Virtual existente |
> | Ação | Microsoft.Sql/servers/virtualNetworkRules/read | Devolve a lista de rede virtual de regras ou obtém as propriedades para a regra de rede virtual especificada. |
> | Ação | Microsoft.Sql/servers/virtualNetworkRules/write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou etiquetas para a regra de rede virtual especificada. |
> | Ação | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidades para um determinado servidor |
> | Ação | Microsoft.Sql/servers/vulnerabilityAssessments/read | Obter as políticas de avaliação de vulnerabilidade num determinado servidor |
> | Ação | Microsoft.Sql/servers/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidades para um determinado servidor |
> | Ação | Microsoft.Sql/servers/write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou etiquetas para o servidor especificado. |
> | Ação | Microsoft.Sql/unregister/action | Anula o registo da subscrição para o fornecedor de recursos da base de dados do Microsoft SQL e permite a criação de bases de dados do Microsoft SQL. |
> | Ação | Microsoft.Sql/virtualClusters/delete | Elimina um cluster virtual existente. |
> | Ação | Microsoft.Sql/virtualClusters/read | Devolve a lista de clusters virtuais ou obtém as propriedades para o cluster virtual especificado. |
> | Ação | Microsoft.Sql/virtualClusters/write | Atualiza as etiquetas de virtual cluster. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Storage/checknameavailability/read | Verifica se o nome da conta é válido e se não está já a ser utilizado. |
> | Ação | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft.Storage que a rede ou sub-rede virtual está a ser eliminada |
> | Ação | Microsoft.Storage/locations/usages/read | Devolve o limite e a contagem de utilização atual de recursos na subscrição especificada |
> | Ação | Microsoft.Storage/operations/read | Consulta o estado de uma operação assíncrona. |
> | Ação | Microsoft.Storage/register/action | Regista a subscrição do fornecedor de recursos de armazenamento e ativa a criação de contas de armazenamento. |
> | Ação | Microsoft.Storage/skus/read | Lista os Skus suportados pelo Microsoft.Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Devolve o resultado da adição de conteúdo de blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Devolve o resultado da eliminação de um blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | Devolve o resultado da eliminação de um instantâneo automático |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Devolve um blob ou uma lista de blobs |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | Devolve o resultado do comando de blobs |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Devolve o resultado de escrever um blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Limpar o arquivo legal do contentor de blobs |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Devolve o resultado da eliminação de um contentor |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Eliminar a política de imutabilidade do contentor de blobs |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Expandir a política de imutabilidade do contentor de blobs |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Bloquear a política de imutabilidade do contentor de blobs |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Obter a política de imutabilidade do contentor de blobs |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Colocar a política de imutabilidade do contentor de blobs |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Devolve o resultado do contentor de blobs de concessão |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/read | Devolve um contentor |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/read | Devolve a lista de contentores |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Definir o arquivo legal do contentor de blobs |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/write | Devolve o resultado do contentor de Blobs do patch |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/write | Devolve o resultado do contentor de BLOBs colocado |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Devolve uma chave de delegação de utilizadores para o serviço de blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/read | Devolve propriedades ou estatísticas do serviço Blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/write | Devolve o resultado das propriedades do serviço de blobs colocado |
> | Ação | Microsoft.Storage/storageAccounts/delete | Elimina uma conta de armazenamento existente. |
> | Ação | Microsoft.Storage/storageAccounts/fileServices/fileShare/delete | Permite ao utilizador eliminar a partilha de ficheiros |
> | Ação | Microsoft.Storage/storageAccounts/fileServices/fileShare/read | Permite ao utilizador ler a partilha de ficheiros |
> | Ação | Microsoft.Storage/storageAccounts/fileServices/fileShare/write | Permite ao utilizador escrever numa partilha de ficheiros |
> | Ação | Microsoft.Storage/storageAccounts/listAccountSas/action | Devolve o token de SAS de Conta para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/listkeys/action | Devolve as chaves de acesso da conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/listServiceSas/action | Devolve o token de SAS de Serviço para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Devolve o resultado da eliminação de uma fila |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Devolve o resultado da adição de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Devolve o resultado da eliminação de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Devolve o resultado do processamento de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Devolve uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Devolve o resultado de escrever uma mensagem |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/queues/read | Devolve uma fila ou uma lista de filas. |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/queues/write | Devolve o resultado da escrita de uma fila |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/read | Propriedades ou estatistas do serviço Fila de devolução. |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/write | Devolve o resultado de definir as propriedades do serviço Fila |
> | Ação | Microsoft.Storage/storageAccounts/read | Devolve a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenera as chaves de acesso da conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Revogar todas as chaves de delegação do utilizador para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Criar/atualizar definições de diagnóstico da conta de armazenamento. |
> | Ação | Microsoft.Storage/storageAccounts/write | Cria uma conta de armazenamento com os parâmetros especificados ou atualiza as propriedades ou etiquetas, ou adiciona um domínio personalizado à conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/usages/read | Devolve o limite e a contagem de utilização atual de recursos na subscrição especificada |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | microsoft.storagesync/locations/checkNameAvailability/action | Verifica esse nome de serviço de sincronização de armazenamento é válido e não está em utilização. |
> | Ação | microsoft.storagesync/locations/workflows/operations/read | Obtém o estado de uma operação assíncrona |
> | Ação | microsoft.storagesync/storageSyncServices/delete | Eliminar quaisquer serviços de sincronização de armazenamento |
> | Ação | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para os serviços de sincronização de armazenamento |
> | Ação | microsoft.storagesync/storageSyncServices/read | Ler quaisquer serviços de sincronização de armazenamento |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/delete | Eliminar qualquer servidor registado |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para o servidor registado |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/read | Ler qualquer servidor registado |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/write | Criar ou atualizar qualquer servidor registado |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Eliminar quaisquer pontos finais da Cloud |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Obtém o estado de uma operação assíncrona de cópia de segurança/restauro |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Chamar esta ação depois de cópia de segurança |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Chamar esta ação depois de restauro |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Chamar esta ação antes de cópia de segurança |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Chamar esta ação antes de restauro |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Ler quaisquer pontos finais da Cloud |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Restaurar heartbeat |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Criar ou atualizar qualquer pontos finais da Cloud |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/delete | Eliminar grupos de sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para grupos de sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/read | Ler todos os grupos de sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Eliminar quaisquer pontos de extremidade do servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para pontos finais do servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Ler quaisquer pontos de extremidade do servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Chamar esta ação de recuperar ficheiros a um servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Criar ou atualizar qualquer pontos finais do servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/write | Criar ou atualizar os grupos de sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Obtém o estado de uma operação assíncrona |
> | Ação | microsoft.storagesync/storageSyncServices/workflows/operations/read | Obtém o estado de uma operação assíncrona |
> | Ação | microsoft.storagesync/storageSyncServices/workflows/read | Fluxos de trabalho de leitura |
> | Ação | microsoft.storagesync/storageSyncServices/write | Criar ou atualizar quaisquer serviços de sincronização de armazenamento |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/delete | Elimina os registos de controlo de acesso |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/read | Apresenta uma lista ou obtém os registos de controlo de acesso |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/write | Criar ou atualizar os registos de controlo de acesso |
> | Ação | Microsoft.StorSimple/managers/alerts/read | Apresenta uma lista ou obter alertas |
> | Ação | Microsoft.StorSimple/managers/backups/read | Apresenta uma lista ou obtém o conjunto de cópia de segurança |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/delete | Elimina uma existente as definições de largura de banda (8000 Series apenas) |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/read | Lista as definições de largura de banda (apenas na 8000 série) |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/write | Cria um novo ou atualiza as definições de largura de banda (8000 Series apenas) |
> | Ação | Microsoft.StorSimple/managers/certificates/write | Criar ou atualizar os certificados |
> | Ação | Microsoft.StorSimple/Managers/certificates/write | A operação atualizar certificado do recurso atualiza o certificado de credencial do recurso/cofre. |
> | Ação | Microsoft.StorSimple/managers/clearAlerts/action | Limpe todos os alertas associados com o Gestor de dispositivos. |
> | Ação | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Configurações suportadas de lista a aplicação da Cloud |
> | Ação | Microsoft.StorSimple/managers/configureDevice/action | Configura um dispositivo |
> | Ação | Microsoft.StorSimple/managers/delete | Elimina os gestores de dispositivos |
> | Ação | Microsoft.StorSimple/Managers/delete | A operação eliminar cofre elimina o recurso do Azure especificado do tipo "Cofre" |
> | Ação | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/alertSettings/read | Apresenta uma lista ou obtém as definições de alerta |
> | Ação | Microsoft.StorSimple/managers/devices/alertSettings/write | Criar ou atualizar as definições de alerta |
> | Ação | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Autorizar o Rollover de chave de encriptação de serviço de dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Efetuar uma cópia de segurança manual para criar um pedido a cópia de segurança de todos os volumes protegidos pela política. |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Elimina um políticas de cópia de segurança existente (8000 Series apenas) |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/read | As políticas de lista a cópia de segurança (apenas na 8000 série) |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Elimina um agendas existentes |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Lista as agendas |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Cria um novo ou agendas de atualizações |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/write | Cria um novo ou atualiza as políticas de cópia de segurança (8000 Series apenas) |
> | Ação | Microsoft.StorSimple/managers/devices/backups/delete | Elimina o conjunto de cópia de segurança |
> | Ação | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Clone um compartilhamento ou volume com um elemento de cópia de segurança. |
> | Ação | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backups/read | Apresenta uma lista ou obtém o conjunto de cópia de segurança |
> | Ação | Microsoft.StorSimple/managers/devices/backups/restore/action | Restaure todos os volumes a partir de um conjunto de cópias de segurança. |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Elimina os grupos de agenda de cópia de segurança |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Apresenta uma lista ou obtém os grupos de agenda de cópia de segurança |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Criar ou atualizar os grupos de agenda de cópia de segurança |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/delete | Elimina as definições de Chap |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/read | Apresenta uma lista ou obtém as definições de Chap |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/write | Criar ou atualizar as definições de Chap |
> | Ação | Microsoft.StorSimple/managers/devices/deactivate/action | Desativa um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/delete | Elimina os dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/disks/read | Apresenta uma lista ou obtém os discos |
> | Ação | Microsoft.StorSimple/managers/devices/download/action | Transfira as atualizações para um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/failover/action | Ativação pós-falha do dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/failoverTargets/read | Apresenta uma lista ou obtém os destinos de ativação pós-falha dos dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Efetuar cópia de segurança de um servidor de ficheiros. |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/delete | Elimina os servidores de ficheiros |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Apresenta uma lista ou obtém as métricas |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Apresenta uma lista ou obtém as definições das métricas |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/read | Apresenta uma lista ou obtém os servidores de ficheiros |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Elimina as partilhas |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Apresenta uma lista ou obtém as métricas |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Apresenta uma lista ou obtém as definições das métricas |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Apresenta uma lista ou obtém as partilhas |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Criar ou atualizar as partilhas |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/write | Criar ou atualizar os servidores de ficheiros |
> | Ação | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Alterar Estado de energia do controlador de grupos de componentes de hardware |
> | Ação | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Listar os grupos de componentes de Hardware |
> | Ação | Microsoft.StorSimple/managers/devices/install/action | Instale atualizações num dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/installUpdates/action | Instala atualizações nos dispositivos (8000 Series apenas). |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Efetuar cópia de segurança de um servidor iSCSI. |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Elimina o iSCSI de servidores |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Elimina os discos |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Apresenta uma lista ou obtém as métricas |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Apresenta uma lista ou obtém as definições das métricas |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Apresenta uma lista ou obtém os discos |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Criar ou atualizar os discos |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Apresenta uma lista ou obtém as métricas |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Apresenta uma lista ou obtém as definições das métricas |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/read | Apresenta uma lista ou obtém o iSCSI de servidores |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/write | Criar ou atualizar o iSCSI de servidores |
> | Ação | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Cancelar uma tarefa em execução |
> | Ação | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/jobs/read | Apresenta uma lista ou obtém as tarefas |
> | Ação | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Liste os conjuntos de ativação pós-falha para um dispositivo existente (8000 Series apenas). |
> | Ação | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Destinos de ativação pós-falha de lista dos dispositivos (8000 Series apenas). |
> | Ação | Microsoft.StorSimple/managers/devices/metrics/read | Apresenta uma lista ou obtém as métricas |
> | Ação | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Apresenta uma lista ou obtém as definições das métricas |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Confirma que uma migração com êxito e confirmá-lo. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Lista a confirmar o estado de migração |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Obter o estado de confirmar da migração. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Obter o estado da tarefa de migração estimativa. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Obter o estado para a migração. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importar configurações de origem para migração |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Listar a estimativa de migração |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Liste o estado de migração |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Iniciar Migração usando configurações de origem |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Inicie uma tarefa para calcular a duração do processo de migração. |
> | Ação | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/networkSettings/read | Apresenta uma lista ou obtém as definições de rede |
> | Ação | Microsoft.StorSimple/managers/devices/networkSettings/write | Cria um novo ou atualiza as definições de rede |
> | Ação | Microsoft.StorSimple/managers/devices/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Chave de encriptação pública de lista do Gestor de dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publica o pacote de suporte para um dispositivo existente. Um pacote de suporte do StorSimple é um mecanismo fácil de usar que recolhe todos os registos relevantes para ajudar a resolver quaisquer problemas de dispositivos do StorSimple Support da Microsoft. |
> | Ação | Microsoft.StorSimple/managers/devices/read | Apresenta uma lista ou obtém os dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Procure as atualizações num dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/read | Lista as definições de segurança |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Sincronize o certificado de gestão remota para um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Atualize as definições de segurança. |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/write | Cria um novo ou atualiza as definições de segurança |
> | Ação | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Envie e-mail de alerta de teste aos destinatários de correio eletrónico configurado. |
> | Ação | Microsoft.StorSimple/managers/devices/shares/read | Apresenta uma lista ou obtém as partilhas |
> | Ação | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/timeSettings/read | Apresenta uma lista ou obtém as definições de hora |
> | Ação | Microsoft.StorSimple/managers/devices/timeSettings/write | Cria um novo ou atualiza as definições de hora |
> | Ação | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/updateSummary/read | Apresenta uma lista ou obtém o resumo de atualização |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Elimina um contentores de volumes existentes (8000 Series apenas) |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Listar as métricas |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Lista as definições das métricas |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/read | Listar os contentores de Volume (apenas na 8000 série) |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Elimina um Volumes existentes |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Listar as métricas |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Lista as definições das métricas |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Lista os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Listar os Volumes |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Cria um novo ou atualiza a Volumes |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/write | Cria um novo ou atualiza a contentores de volumes (8000 Series apenas) |
> | Ação | Microsoft.StorSimple/managers/devices/volumes/read | Listar os Volumes |
> | Ação | Microsoft.StorSimple/managers/devices/write | Criar ou atualizar os dispositivos |
> | Ação | Microsoft.StorSimple/managers/encryptionSettings/read | Apresenta uma lista ou obtém as definições de encriptação |
> | Ação | Microsoft.StorSimple/managers/extendedInformation/delete | Elimina as informações do cofre expandida |
> | Ação | Microsoft.StorSimple/Managers/extendedInformation/delete | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Ação | Microsoft.StorSimple/managers/extendedInformation/read | Apresenta uma lista ou obtém as informações do cofre expandido |
> | Ação | Microsoft.StorSimple/Managers/extendedInformation/read | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Ação | Microsoft.StorSimple/managers/extendedInformation/write | Criar ou atualizar as informações do cofre expandido |
> | Ação | Microsoft.StorSimple/Managers/extendedInformation/write | A operação Obter Informações Detalhadas obtém as Informações Detalhadas de um objeto que representa o tipo de recurso ?vault? do Azure |
> | Ação | Microsoft.StorSimple/managers/features/read | Listar os recursos |
> | Ação | Microsoft.StorSimple/managers/fileservers/read | Apresenta uma lista ou obtém os servidores de ficheiros |
> | Ação | Microsoft.StorSimple/managers/getEncryptionKey/action | Obter chave de encriptação para o Gestor de dispositivos. |
> | Ação | Microsoft.StorSimple/managers/iscsiservers/read | Apresenta uma lista ou obtém o iSCSI de servidores |
> | Ação | Microsoft.StorSimple/managers/jobs/read | Apresenta uma lista ou obtém as tarefas |
> | Ação | Microsoft.StorSimple/managers/listActivationKey/action | Obtém a chave de ativação do StorSimple Device Manager. |
> | Ação | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Listar chaves de encriptação pública de um Gestor de dispositivos do StorSimple. |
> | Ação | Microsoft.StorSimple/managers/metrics/read | Apresenta uma lista ou obtém as métricas |
> | Ação | Microsoft.StorSimple/managers/metricsDefinitions/read | Apresenta uma lista ou obtém as definições das métricas |
> | Ação | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrar de clássico para Resource Manager de Mangers |
> | Ação | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Lista as configurações de origem de migração (apenas na 8000 série) |
> | Ação | Microsoft.StorSimple/managers/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Crie uma nova aplicação de cloud. |
> | Ação | Microsoft.StorSimple/managers/read | Apresenta uma lista ou obtém os gestores de dispositivos |
> | Ação | Microsoft.StorSimple/Managers/read | A operação obter cofre obtém um objeto que representa o recurso do Azure do tipo "Cofre" |
> | Ação | Microsoft.StorSimple/managers/regenerateActivationKey/action | Regenere a chave de ativação para uma existente StorSimple Device Manager. |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Elimina as credenciais da conta de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/read | Apresenta uma lista ou obtém as credenciais da conta de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/write | Criar ou atualizar as credenciais da conta de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageDomains/delete | Elimina os domínios de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Apresenta uma lista ou obtém os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/storageDomains/read | Apresenta uma lista ou obtém os domínios de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageDomains/write | Criar ou atualizar os domínios de armazenamento |
> | Ação | Microsoft.StorSimple/managers/write | Criar ou atualizar os gestores de dispositivos |
> | Ação | Microsoft.StorSimple/Managers/write | A operação Criar Cofre cria um recurso do tipo "cofre" do Azure |
> | Ação | Microsoft.StorSimple/operations/read | Apresenta uma lista ou obtém as operações |
> | Ação | Microsoft.StorSimple/register/action | Registar fornecedor Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.StreamAnalytics/locations/quotas/Read | Quota de subscrição do leitura Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/operations/Read | Operações de leitura de Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/Register/action | Registar a subscrição no fornecedor de recursos do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Delete | Eliminar tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Eliminar função de tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Resultados da operação de leitura para a função de tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Função de tarefa de análise de Stream de leitura |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Obter a predefinição de uma função de tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Função de trabalho do teste Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Escrever a função de tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Eliminar a entrada da tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Resultados da operação de leitura de entrada da tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Entrada da tarefa leitura Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Entrada da tarefa do exemplo Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Entrada da tarefa do teste Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Escrever a entrada da tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Resultados da operação de leitura para a tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Eliminar a saída de tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Resultados da operação de leitura de saída de tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Resultado da tarefa leitura Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Resultado da tarefa do teste Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Escrever a saída de tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Ler definição de diagnóstico. |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Escreva definição de diagnóstico. |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Obtém os registos disponíveis para streamingjobs |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para streamingjobs |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Read | Leitura tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Start/action | Iniciar a tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Parar a tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Eliminar a transformação da tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Transformação da tarefa leitura Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Escrever a transformação da tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Write | Escrever a tarefa do Stream Analytics |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Subscription/CreateSubscription/action | Criar uma subscrição do Azure |
> | Ação | Microsoft.Subscription/register/action | Regista a subscrição no fornecedor de recursos de Microsoft.Subscription |
> | Ação | Microsoft.Subscription/SubscriptionDefinitions/read | Obter uma definição de subscrição do Azure dentro de um grupo de gestão. |
> | Ação | Microsoft.Subscription/SubscriptionDefinitions/write | Criar uma definição de subscrição do Azure |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Support/register/action | Regista para o Fornecedor de Recursos de Suporte |
> | Ação | Microsoft.Support/supportTickets/read | Obtém os detalhes do Pedido de Suporte (incluindo o estado, gravidade, detalhes de contacto e comunicações) ou obtém a lista dos Pedidos de Suporte nas subscrições. |
> | Ação | Microsoft.Support/supportTickets/write | Cria ou Atualiza um Pedido de Suporte. Pode criar um Pedido de Suporte para problemas relacionados com a Gestão Técnica, de Faturação, de Quotas ou de Subscrição. Pode atualizar a gravidade, os detalhes de contacto e as comunicações para pedidos de suporte existentes. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Elimina a política de acesso. |
> | Ação | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Obter as propriedades de uma política de acesso. |
> | Ação | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Cria uma nova política de acesso para um ambiente ou atualiza uma política de acesso existente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/delete | Elimina o ambiente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Elimina a origem do evento. |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/read | Obter as propriedades de uma origem de evento. |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/write | Cria uma nova origem de eventos para um ambiente ou atualiza uma origem de evento existente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/read | Obter as propriedades de um ambiente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Elimina o conjunto de dados de referência. |
> | Ação | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Obter as propriedades de um conjunto de dados de referência. |
> | Ação | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Cria um novo conjunto de dados de referência para um ambiente ou atualiza um conjunto de dados de referência existente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/status/read | Obtenha o estado do ambiente, o estado das suas operações associadas, como entrada. |
> | Ação | Microsoft.TimeSeriesInsights/environments/write | Cria um novo ambiente ou atualiza um ambiente existente. |
> | Ação | Microsoft.TimeSeriesInsights/register/action | Regista a subscrição para o fornecedor de recursos do Time Series Insights e permite a criação de ambientes do Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.VisualStudio/Account/Delete | Eliminar Conta |
> | Ação | Microsoft.VisualStudio/Account/Extension/Read | Ler/extensão da conta |
> | Ação | Microsoft.VisualStudio/Account/Project/Read | Ler conta/projeto |
> | Ação | Microsoft.VisualStudio/Account/Project/Write | Definir conta/projeto |
> | Ação | Microsoft.VisualStudio/Account/Read | Ler conta |
> | Ação | Microsoft.VisualStudio/Account/Write | Definir conta |
> | Ação | Microsoft.VisualStudio/Extension/Delete | Eliminar extensão |
> | Ação | Microsoft.VisualStudio/Extension/Read | Extensão de leitura |
> | Ação | Microsoft.VisualStudio/Extension/Write | Extensão do conjunto |
> | Ação | Microsoft.VisualStudio/Project/Delete | Eliminar Projeto |
> | Ação | Microsoft.VisualStudio/Project/Read | Projeto de leitura |
> | Ação | Microsoft.VisualStudio/Project/Write | Definir o projeto |
> | Ação | Microsoft.VisualStudio/Register/Action | Registar a subscrição do Azure com o fornecedor de Microsoft.VisualStudio |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | microsoft.web/apimanagementaccounts/apiacls/read | Obtenha Apiacls de contas de gestão de Api. |
> | Ação | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Elimine Apiacls de APIs de contas de gestão de Api. |
> | Ação | microsoft.web/apimanagementaccounts/apis/apiacls/read | Obtenha Apiacls de APIs de contas de gestão de Api. |
> | Ação | microsoft.web/apimanagementaccounts/apis/apiacls/write | Atualize Apiacls de APIs de contas de gestão de Api. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Obtenha Connectionacls de APIs de contas de gestão de Api. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Certifique-se ligações de APIs de contas de gestão Api código consentimento. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Elimine Api Management contas APIs ligações Connectionacls. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Obter a Api Management contas APIs ligações Connectionacls. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Atualize a Api Management contas APIs ligações Connectionacls. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/delete | Elimine ligações de APIs de contas de gestão de Api. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Obtenha ligações de consentimento para ligações de APIs de contas de gestão de Api. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Lista ligação chaves gestão contas APIs ligações de Api. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Lista segredos gestão contas APIs ligações de Api. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/read | Obtenha ligações de APIs de contas de gestão de Api. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/write | Atualize ligações de APIs de contas de gestão de Api. |
> | Ação | microsoft.web/apimanagementaccounts/apis/delete | Elimine as APIs de contas de gestão de Api. |
> | Ação | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Gestão de Api de eliminar definições de localizados de APIs de contas. |
> | Ação | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Gestão de Api de obter definições de localizados de APIs de contas. |
> | Ação | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | APIs de contas de gestão de Api de atualização localizada definições. |
> | Ação | microsoft.web/apimanagementaccounts/apis/read | Obtenha as APIs de contas de gestão de Api. |
> | Ação | microsoft.web/apimanagementaccounts/apis/write | Atualize as APIs de contas de gestão de Api. |
> | Ação | microsoft.web/apimanagementaccounts/connectionacls/read | Obtenha Connectionacls de contas de gestão de Api. |
> | Ação | microsoft.web/availablestacks/read | Obtenha pilhas disponíveis. |
> | Ação | Microsoft.Web/certificates/Delete | Elimine um certificado existente. |
> | Ação | Microsoft.Web/certificates/Read | Obter a lista de certificados. |
> | Ação | Microsoft.Web/certificates/Write | Adicionar um novo certificado ou atualizar um já existente. |
> | Ação | microsoft.web/checknameavailability/read | Verifique se o nome do recurso está disponível. |
> | Ação | microsoft.web/classicmobileservices/read | Obtenha os serviços móveis clássico. |
> | Ação | Microsoft.Web/connectionGateways/Delete | Elimina um Gateway de ligação. |
> | Ação | Microsoft.Web/connectionGateways/Join/Action | Associa um Gateway de ligação. |
> | Ação | Microsoft.Web/connectionGateways/ListStatus/Action | Apresenta uma lista de estado de um Gateway de ligação. |
> | Ação | Microsoft.Web/connectionGateways/Move/Action | Move um Gateway de ligação. |
> | Ação | Microsoft.Web/connectionGateways/Read | Obter a lista de Gateways de ligação. |
> | Ação | Microsoft.Web/connectionGateways/Write | Cria ou atualiza um Gateway de ligação. |
> | Ação | microsoft.web/connections/confirmconsentcode/action | Confirme o código de consentimento de ligações. |
> | Ação | Microsoft.Web/connections/Delete | Elimina uma ligação. |
> | Ação | Microsoft.Web/connections/Join/Action | Associa uma ligação. |
> | Ação | microsoft.web/connections/listconsentlinks/action | Ligações de consentimento da lista de ligações. |
> | Ação | Microsoft.Web/connections/Move/Action | Move uma ligação. |
> | Ação | Microsoft.Web/connections/Read | Obter a lista de ligações. |
> | Ação | Microsoft.Web/connections/Write | Cria ou atualiza uma ligação. |
> | Ação | Microsoft.Web/customApis/Delete | Elimina uma API personalizada. |
> | Ação | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extrai a definição de API de um WSDL. |
> | Ação | Microsoft.Web/customApis/Join/Action | Associa uma API personalizada. |
> | Ação | Microsoft.Web/customApis/listWsdlInterfaces/Action | Apresenta uma lista de interfaces WSDL para uma API personalizada. |
> | Ação | Microsoft.Web/customApis/Move/Action | Move uma API personalizada. |
> | Ação | Microsoft.Web/customApis/Read | Obter a lista de API personalizada. |
> | Ação | Microsoft.Web/customApis/Write | Cria ou atualiza uma API personalizada. |
> | Ação | Microsoft.Web/deletedSites/Read | Obter as propriedades de uma aplicação de Web eliminado |
> | Ação | microsoft.web/deploymentlocations/read | Obter as localizações de implementação. |
> | Ação | Microsoft.Web/geoRegions/Read | Obter a lista de regiões de Georreplicação. |
> | Ação | microsoft.web/hostingenvironments/capacities/read | Obtenha as capacidades de ambientes de alojamento. |
> | Ação | Microsoft.Web/hostingEnvironments/Delete | Eliminar um ambiente de serviço de aplicações |
> | Ação | microsoft.web/hostingenvironments/detectors/read | Obter os detectores de ambientes de alojamento. |
> | Ação | microsoft.web/hostingenvironments/diagnostics/read | Obtenha diagnósticos de ambientes de alojamento. |
> | Ação | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Obtenha os pontos de extremidade de rede de todas as dependências de entrada. |
> | Ação | microsoft.web/hostingenvironments/metricdefinitions/read | Obter definições de métricas de ambientes de alojamento. |
> | Ação | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Obter definições de métricas de multifunções conjuntos de ambientes de alojamento. |
> | Ação | microsoft.web/hostingenvironments/multirolepools/metrics/read | Obter métricas de multifunções conjuntos de ambientes de alojamento. |
> | Ação | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Obter as propriedades de um conjunto de front-end num ambiente de serviço de aplicações |
> | Ação | microsoft.web/hostingenvironments/multirolepools/skus/read | Obter SKUs de multifunções conjuntos de ambientes de alojamento. |
> | Ação | microsoft.web/hostingenvironments/multirolepools/usages/read | Obter utilizações de multifunções conjuntos de ambientes de alojamento. |
> | Ação | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Criar um novo conjunto de front-end num ambiente de serviço de aplicações ou atualizar um já existente |
> | Ação | microsoft.web/hostingenvironments/operations/read | Obter operações de ambientes de alojamento. |
> | Ação | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Obtenha os pontos de extremidade de rede de todas as dependências de saída. |
> | Ação | Microsoft.Web/hostingEnvironments/Read | Obter as propriedades de um ambiente de serviço de aplicações |
> | Ação | Microsoft.Web/hostingEnvironments/reboot/Action | Reinicie todas as máquinas num ambiente de serviço de aplicações |
> | Ação | microsoft.web/hostingenvironments/resume/action | Retomar a ambientes de alojamento. |
> | Ação | microsoft.web/hostingenvironments/serverfarms/read | Obtenha planos de serviço de aplicações de ambientes de alojamento. |
> | Ação | microsoft.web/hostingenvironments/sites/read | Obter aplicações da Web de ambientes de alojamento. |
> | Ação | microsoft.web/hostingenvironments/suspend/action | Suspenda a ambientes de alojamento. |
> | Ação | microsoft.web/hostingenvironments/usages/read | Obter utilizações de ambientes de alojamento. |
> | Ação | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Obter definições de métrica de Workerpools de ambientes de alojamento. |
> | Ação | microsoft.web/hostingenvironments/workerpools/metrics/read | Obter métricas de Workerpools de ambientes de alojamento. |
> | Ação | Microsoft.Web/hostingEnvironments/workerPools/Read | Obter as propriedades de um conjunto de trabalho num ambiente de serviço de aplicações |
> | Ação | microsoft.web/hostingenvironments/workerpools/skus/read | Obter SKUs de Workerpools de ambientes de alojamento. |
> | Ação | microsoft.web/hostingenvironments/workerpools/usages/read | Obter utilizações de Workerpools de ambientes de alojamento. |
> | Ação | Microsoft.Web/hostingEnvironments/workerPools/Write | Criar um novo conjunto de trabalho num ambiente de serviço de aplicações ou atualizar um já existente |
> | Ação | Microsoft.Web/hostingEnvironments/Write | Criar um ambiente de serviço de aplicações novo ou atualizar um já existente |
> | Ação | microsoft.web/ishostingenvironmentnameavailable/read | Get se o nome do ambiente de alojamento está disponível. |
> | Ação | microsoft.web/ishostnameavailable/read | Verifique se o nome de anfitrião está disponível. |
> | Ação | microsoft.web/isusernameavailable/read | Verifique se o nome de utilizador está disponível. |
> | Ação | Microsoft.Web/listSitesAssignedToHostName/Read | Obter nomes de sites atribuídos ao nome de anfitrião. |
> | Ação | microsoft.web/locations/apioperations/read | Obter operações de API de localizações. |
> | Ação | microsoft.web/locations/connectiongatewayinstallations/read | Obter instalações de gateways de ligação de locais. |
> | Ação | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Vnet ou sub-rede notificação de eliminação para localizações. |
> | Ação | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extrair a definição de Api do WSDL para localizações. |
> | Ação | microsoft.web/locations/listwsdlinterfaces/action | Interfaces WSDL de lista de localizações. |
> | Ação | microsoft.web/locations/managedapis/apioperations/read | Obter operações de API gerido de localizações. |
> | Ação | Microsoft.Web/locations/managedapis/Join/Action | Associa uma API gerenciada. |
> | Ação | microsoft.web/locations/managedapis/read | Obter localizações de APIs gerenciadas. |
> | Ação | microsoft.web/operations/read | Obter operações. |
> | Ação | microsoft.web/publishingusers/read | Obter utilizadores de publicação. |
> | Ação | microsoft.web/publishingusers/write | Atualize utilizadores de publicação. |
> | Ação | Microsoft.Web/recommendations/Read | Obter a lista de recomendações para as subscrições. |
> | Ação | microsoft.web/register/action | Registe o fornecedor de recursos de Microsoft. Web para a subscrição. |
> | Ação | microsoft.web/resourcehealthmetadata/read | Obter metadados de estado de funcionamento do recurso. |
> | Ação | microsoft.web/serverfarms/capabilities/read | Obtenha capacidades de planos de serviço de aplicações. |
> | Ação | Microsoft.Web/serverfarms/Delete | Eliminar um plano de serviço de aplicações existentes |
> | Ação | microsoft.web/serverfarms/firstpartyapps/settings/delete | Elimine definições de aplicações de terceiros de primeiro em planos de serviço de aplicações. |
> | Ação | microsoft.web/serverfarms/firstpartyapps/settings/read | Obter definições de aplicações de terceiros de primeiro em planos de serviço de aplicações. |
> | Ação | microsoft.web/serverfarms/firstpartyapps/settings/write | Atualize definições de aplicações de terceiros de primeiro de planos do serviço de aplicações. |
> | Ação | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Elimine reencaminhamentos de espaços de nomes de ligação do serviço de aplicações planos híbrida. |
> | Ação | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Obtenha os reencaminhamentos de espaços de nomes de ligação do serviço de aplicações planos híbrida. |
> | Ação | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Obter planos de serviço de aplicações reencaminhamentos para espaços de nomes de ligação híbrida Web de aplicações. |
> | Ação | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Obtenha os limites do plano de ligação híbrida do serviço de aplicações planos. |
> | Ação | microsoft.web/serverfarms/hybridconnectionrelays/read | Obtenha planos de serviço de aplicações híbridas reencaminhamentos de ligação. |
> | Ação | microsoft.web/serverfarms/metricdefinitions/read | Obter as definições de métricas de planos do serviço de aplicações. |
> | Ação | microsoft.web/serverfarms/metrics/read | Obter métricas de planos de serviço de aplicações. |
> | Ação | microsoft.web/serverfarms/operationresults/read | Obter resultados da operação de planos de serviço de aplicações. |
> | Ação | Microsoft.Web/serverfarms/Read | Obter as propriedades num plano do serviço de aplicações |
> | Ação | Microsoft.Web/serverfarms/restartSites/Action | Reinicie todas as aplicações Web num plano do serviço de aplicações |
> | Ação | microsoft.web/serverfarms/sites/read | Obter aplicações de Web de planos de serviço de aplicações. |
> | Ação | microsoft.web/serverfarms/skus/read | Obter os SKUs de planos de serviço de aplicações. |
> | Ação | microsoft.web/serverfarms/usages/read | Obter utilizações de planos de serviço de aplicações. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Atualize os Gateways de ligações de rede Virtual de planos de serviço de aplicações. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/read | Obtenha ligações de rede Virtual de planos de serviço de aplicações. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Elimine rotas de ligações de rede Virtual de planos de serviço de aplicações. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Obter as rotas de ligações de rede Virtual de planos de serviço de aplicações. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Atualize rotas de ligações de rede Virtual de planos de serviço de aplicações. |
> | Ação | microsoft.web/serverfarms/workers/reboot/action | Reinicie os trabalhos de planos de serviço de aplicações. |
> | Ação | Microsoft.Web/serverfarms/Write | Criar um plano de serviço de aplicações novo ou atualizar um já existente |
> | Ação | microsoft.web/sites/analyzecustomhostname/read | Analise o nome de anfitrião personalizado. |
> | Ação | Microsoft.Web/sites/applySlotConfig/Action | Aplicar a configuração de ranhura de aplicação do web da ranhura de destino para a aplicação web atual |
> | Ação | Microsoft.Web/sites/backup/Action | Criar uma nova cópia de segurança de aplicação de web |
> | Ação | microsoft.web/sites/backup/read | Obtenha cópia de segurança de aplicações de Web. |
> | Ação | microsoft.web/sites/backup/write | Atualize a cópia de segurança de aplicações de Web. |
> | Ação | microsoft.web/sites/backups/action | Deteta uma cópia de segurança de aplicação existente que pode ser restaurada a partir de um blob no armazenamento do Azure. |
> | Ação | microsoft.web/sites/backups/delete | Elimine cópias de segurança de aplicações de Web. |
> | Ação | microsoft.web/sites/backups/list/action | Backups de aplicações Web de lista. |
> | Ação | Microsoft.Web/sites/backups/Read | Obter as propriedades de cópia de segurança de uma aplicação web |
> | Ação | microsoft.web/sites/backups/restore/action | Restaure cópias de segurança de aplicações de Web. |
> | Ação | microsoft.web/sites/backups/write | Atualize a cópias de segurança de aplicações Web. |
> | Ação | microsoft.web/sites/config/delete | Elimine configuração de aplicações Web. |
> | Ação | Microsoft.Web/sites/config/list/Action | Lista confidenciais configurações de segurança da aplicação Web, como a publicação de credenciais, as definições da aplicação e as cadeias de ligação |
> | Ação | Microsoft.Web/sites/config/Read | Obter definições de configuração de aplicação Web |
> | Ação | microsoft.web/sites/config/snapshots/read | Obter instantâneos de configuração de aplicações Web. |
> | Ação | Microsoft.Web/sites/config/Write | Atualizar as definições de configuração da aplicação Web |
> | Ação | microsoft.web/sites/containerlogs/action | Obter comprimido registos de contentor para a aplicação Web. |
> | Ação | microsoft.web/sites/containerlogs/download/action | Transferir registos de contentor de aplicações Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/delete | Elimine tarefas de Web contínua de aplicações Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/read | Obter tarefas de Web contínua de aplicações Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/start/action | Inicie tarefas de Web contínua de aplicações Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/stop/action | Pare tarefas de Web contínua de aplicações Web. |
> | Ação | Microsoft.Web/sites/Delete | Eliminar uma aplicação Web existente |
> | Ação | microsoft.web/sites/deployments/delete | Elimine as implementações de aplicações Web. |
> | Ação | microsoft.web/sites/deployments/log/read | Obter registos de implementações de aplicações Web. |
> | Ação | microsoft.web/sites/deployments/read | Obter implementações de aplicações Web. |
> | Ação | microsoft.web/sites/deployments/write | Atualize as implementações de aplicações Web. |
> | Ação | microsoft.web/sites/detectors/read | Obtenha os detectores de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/analyses/execute/Action | Execute a análise de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/analyses/read | Obtenha a análise de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/aspnetcore/read | Obtenha o diagnóstico de aplicações Web para a aplicação ASP.NET Core. |
> | Ação | microsoft.web/sites/diagnostics/autoheal/read | Obtenha o Autoheal de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/deployment/read | Obter a implementação de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/deployments/read | Obter implementações de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/detectors/execute/Action | Execute o detetor de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/detectors/read | Obtenha o detetor de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Obter pedidos falhados de diagnóstico de aplicações Web por Uri. |
> | Ação | microsoft.web/sites/diagnostics/frebanalysis/read | Obtenha a análise FREB de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/loganalyzer/read | Obtenha o analisador de registos de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/read | Obter categorias de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/runtimeavailability/read | Obtenha disponibilidade de tempo de execução do diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/servicehealth/read | Obter o estado de funcionamento de serviço de diagnóstico de aplicações de Web. |
> | Ação | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Obtenha a análise de CPU de Site de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/sitecrashes/read | Obter falhas de Site de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/sitelatency/read | Obtenha a latência de Site de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Obtenha a análise de memória do Site de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Obtenha a atualização de definição de reinício de Site diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Obter iniciado dos utilizador de reinício de Site para o diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/siteswap/read | Obtenha a troca de Site de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/threadcount/read | Obter a contagem de threads de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/workeravailability/read | Obtenha Workeravailability de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Obtenha a Reciclagem de processo de trabalho de diagnóstico de aplicações Web. |
> | Ação | microsoft.web/sites/domainownershipidentifiers/read | Obter identificadores de propriedade do domínio de aplicações Web. |
> | Ação | microsoft.web/sites/domainownershipidentifiers/write | Atualize os identificadores de propriedade do domínio de aplicações Web. |
> | Ação | microsoft.web/sites/functions/action | Aplicações Web de funções. |
> | Ação | microsoft.web/sites/functions/delete | Elimine funções de aplicações Web. |
> | Ação | microsoft.web/sites/functions/listsecrets/action | Funções de aplicações de Web de segredos de lista. |
> | Ação | microsoft.web/sites/functions/masterkey/read | Obtenha Masterkey de funções de aplicações Web. |
> | Ação | microsoft.web/sites/functions/read | Obter funções de aplicações Web. |
> | Ação | microsoft.web/sites/functions/token/read | Token de funções de aplicações de Web de GET. |
> | Ação | microsoft.web/sites/functions/write | Atualize funções de aplicações Web. |
> | Ação | microsoft.web/sites/hostnamebindings/delete | Elimine enlaces de nome de anfitrião de aplicações Web. |
> | Ação | microsoft.web/sites/hostnamebindings/read | Obter os enlaces de nome de anfitrião de aplicações Web. |
> | Ação | microsoft.web/sites/hostnamebindings/write | Atualize os enlaces de nome de anfitrião de aplicações Web. |
> | Ação | microsoft.web/sites/hostruntime/functions/keys/read | Obtenha chaves de funções de Hostruntime de aplicações Web. |
> | Ação | Microsoft.Web/sites/hostruntime/host/_master/read | Obter a chave mestra da aplicação de função para operações de administração |
> | Ação | Microsoft.Web/sites/hostruntime/host/action | Execute a aplicação de funções, como uma ação de tempo de execução acionadores de sincronização, adicionar funções, invocar funções, eliminar as funções etc. |
> | Ação | microsoft.web/sites/hostruntime/host/read | Obter anfitrião de Hostruntime de aplicações Web. |
> | Ação | microsoft.web/sites/hybridconnection/delete | Elimine ligação de aplicações híbridas de Web. |
> | Ação | microsoft.web/sites/hybridconnection/read | Obter ligação de aplicações híbridas de Web. |
> | Ação | microsoft.web/sites/hybridconnection/write | Atualize a ligação de aplicações híbridas de Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Elimine reencaminhamentos de espaços de nomes de ligação de aplicações híbridas de Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Lista as chaves Web aplicações híbridas ligação espaços de nomes reencaminhamentos. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Obtenha os reencaminhamentos de espaços de nomes de ligação de aplicações híbridas de Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Atualize reencaminhamentos de espaços de nomes de ligação de aplicações híbridas de Web. |
> | Ação | microsoft.web/sites/hybridconnectionrelays/read | Obtenha os reencaminhamentos de ligação de aplicações híbridas de Web. |
> | Ação | microsoft.web/sites/instances/deployments/delete | Elimine as implementações de instâncias de aplicações Web. |
> | Ação | microsoft.web/sites/instances/deployments/read | Obter implementações de instâncias de aplicações Web. |
> | Ação | microsoft.web/sites/instances/extensions/log/read | Obter registos de extensões de instâncias de aplicações Web. |
> | Ação | microsoft.web/sites/instances/extensions/processes/read | Obter processos de extensões de instâncias de aplicações Web. |
> | Ação | microsoft.web/sites/instances/extensions/read | Obtenha as extensões de instâncias de aplicações Web. |
> | Ação | microsoft.web/sites/instances/processes/delete | Elimine processos de instâncias de aplicações Web. |
> | Ação | microsoft.web/sites/instances/processes/modules/read | Obter os módulos de processos de instâncias de aplicações Web. |
> | Ação | microsoft.web/sites/instances/processes/read | Obter processos de instâncias de aplicações Web. |
> | Ação | microsoft.web/sites/instances/processes/threads/read | Obtenha os Threads de processos de instâncias de aplicações Web. |
> | Ação | microsoft.web/sites/instances/read | Obter instâncias de aplicações Web. |
> | Ação | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Lista sincronização função acionador Estado aplicações Web. |
> | Ação | microsoft.web/sites/metricdefinitions/read | Obter definições de métrica de aplicações de Web. |
> | Ação | microsoft.web/sites/metrics/read | Obter métricas de aplicações Web. |
> | Ação | microsoft.web/sites/metricsdefinitions/read | Obter definições de métricas de aplicações Web. |
> | Ação | microsoft.web/sites/migratemysql/action | Migre aplicações Web do MySql. |
> | Ação | microsoft.web/sites/migratemysql/read | Obter as Web Apps migrar MySql. |
> | Ação | microsoft.web/sites/networktrace/action | Aplicações de Web de rastreio de rede. |
> | Ação | microsoft.web/sites/newpassword/action | Aplicações Web de newpassword. |
> | Ação | microsoft.web/sites/operationresults/read | Obter resultados da operação de aplicações Web. |
> | Ação | microsoft.web/sites/operations/read | Obter operações de aplicações Web. |
> | Ação | microsoft.web/sites/perfcounters/read | Obter os contadores de desempenho de aplicações Web. |
> | Ação | microsoft.web/sites/premieraddons/delete | Elimine complementos de Premier de aplicações Web. |
> | Ação | microsoft.web/sites/premieraddons/read | Obtenha suplementos de Premier de aplicações Web. |
> | Ação | microsoft.web/sites/premieraddons/write | Atualize complementos de Premier de aplicações Web. |
> | Ação | microsoft.web/sites/privateaccess/read | Obter dados em torno de ativação de acesso de sites privados e redes virtuais autorizadas, que pode aceder ao site. |
> | Ação | microsoft.web/sites/processes/read | Obter processos de aplicações Web. |
> | Ação | microsoft.web/sites/publiccertificates/delete | Elimine certificados de aplicações públicas da Web. |
> | Ação | microsoft.web/sites/publiccertificates/read | Obter certificados de aplicações públicas da Web. |
> | Ação | microsoft.web/sites/publiccertificates/write | Atualize certificados de aplicações públicas da Web. |
> | Ação | Microsoft.Web/sites/publish/Action | Publicar uma aplicação Web |
> | Ação | Microsoft.Web/sites/publishxml/Action | Obter o xml do perfil de publicação para uma aplicação Web |
> | Ação | microsoft.web/sites/publishxml/read | Obter aplicações Web XML de publicação. |
> | Ação | Microsoft.Web/sites/Read | Obter as propriedades de uma aplicação Web |
> | Ação | microsoft.web/sites/recommendationhistory/read | Obter histórico de recomendações de aplicações Web. |
> | Ação | microsoft.web/sites/recommendations/disable/action | Desative as recomendações de aplicações Web. |
> | Ação | Microsoft.Web/sites/recommendations/Read | Obter a lista de recomendações para a aplicação web. |
> | Ação | microsoft.web/sites/recover/action | Recupere a aplicações Web. |
> | Ação | Microsoft.Web/sites/resetSlotConfig/Action | Repor a configuração de aplicações web |
> | Ação | microsoft.web/sites/resourcehealthmetadata/read | Obter metadados de estado de funcionamento do recurso de aplicações Web. |
> | Ação | Microsoft.Web/sites/restart/Action | Reiniciar uma aplicação Web |
> | Ação | microsoft.web/sites/restore/read | Obtenha o restauro de aplicações Web. |
> | Ação | microsoft.web/sites/restore/write | Restaure aplicações Web. |
> | Ação | microsoft.web/sites/restorefrombackupblob/action | Restaure aplicação Web a partir do Blob de cópia de segurança. |
> | Ação | microsoft.web/sites/restorefromdeletedwebapp/action | Restaure aplicações Web da aplicação eliminada. |
> | Ação | microsoft.web/sites/restoresnapshot/action | Restaure instantâneos de aplicações Web. |
> | Ação | microsoft.web/sites/siteextensions/delete | Elimine extensões de Site de aplicações Web. |
> | Ação | microsoft.web/sites/siteextensions/read | Obtenha as extensões de Site de aplicações Web. |
> | Ação | microsoft.web/sites/siteextensions/write | Atualize extensões de Site de aplicações Web. |
> | Ação | microsoft.web/sites/slots/analyzecustomhostname/read | Obter as Web Apps ranhuras analisar nome de anfitrião personalizado. |
> | Ação | Microsoft.Web/sites/slots/applySlotConfig/Action | Aplica a configuração de ranhura de aplicação do web da ranhura de destino para a ranhura atual. |
> | Ação | Microsoft.Web/sites/slots/backup/Action | Crie nova cópia de segurança da ranhura de aplicação Web. |
> | Ação | microsoft.web/sites/slots/backup/read | Obtenha cópia de segurança de ranhuras de aplicações de Web. |
> | Ação | microsoft.web/sites/slots/backup/write | Atualize a cópia de segurança de ranhuras de aplicações de Web. |
> | Ação | microsoft.web/sites/slots/backups/action | Detete cópias de segurança de ranhuras de aplicações de Web. |
> | Ação | microsoft.web/sites/slots/backups/delete | Elimine cópias de segurança de ranhuras de aplicações de Web. |
> | Ação | microsoft.web/sites/slots/backups/list/action | Cópias de segurança da ranhuras de aplicações do Web de lista. |
> | Ação | Microsoft.Web/sites/slots/backups/Read | Obter as propriedades de cópia de segurança dos blocos de aplicação uma web |
> | Ação | microsoft.web/sites/slots/backups/restore/action | Restaure Backups de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/config/delete | Elimine configuração de ranhuras de aplicações Web. |
> | Ação | Microsoft.Web/sites/slots/config/list/Action | Lista confidenciais configurações de segurança da ranhura de aplicação Web, como a publicação de credenciais, as definições da aplicação e as cadeias de ligação |
> | Ação | Microsoft.Web/sites/slots/config/Read | Obter definições de configuração da ranhura de aplicação Web |
> | Ação | Microsoft.Web/sites/slots/config/Write | Atualizar as definições de configuração da ranhura de aplicação Web |
> | Ação | microsoft.web/sites/slots/containerlogs/action | Obter comprimido registos de contentor para a ranhura de aplicação Web. |
> | Ação | microsoft.web/sites/slots/containerlogs/download/action | Transferir registos de contentor de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/delete | Elimine tarefas de Web contínua de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/read | Obtenha as Web Apps ranhuras Webjobs contínuos. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/start/action | Inicie tarefas de Web contínua de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/stop/action | Pare tarefas de Web contínua de ranhuras de aplicações Web. |
> | Ação | Microsoft.Web/sites/slots/Delete | Eliminar uma ranhura de aplicação Web existente |
> | Ação | microsoft.web/sites/slots/deployments/delete | Elimine as implementações de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/deployments/log/read | Obter registos de implementações de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/deployments/read | Obter implementações de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/deployments/write | Atualize as implementações de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/detectors/read | Obtenha os detectores de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Execute a análise de diagnóstico de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/analyses/read | Obtenha a análise de diagnóstico de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Obtenha diagnósticos de ranhuras de aplicações Web para a aplicação ASP.NET Core. |
> | Ação | microsoft.web/sites/slots/diagnostics/autoheal/read | Obtenha o Autoheal de diagnóstico de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/deployment/read | Obter a implementação de diagnóstico de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/deployments/read | Obter implementações de diagnóstico de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Execute o detetor de diagnóstico de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/detectors/read | Obtenha o detetor de diagnóstico de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Obter análises de FREB de diagnóstico de ranhuras de aplicações de Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Obtenha o analisador de Log de diagnóstico de ranhuras de aplicações de Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/read | Obtenha diagnósticos de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Obtenha disponibilidade de tempo de execução do diagnóstico de ranhuras de aplicações da Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/servicehealth/read | Obter o estado de funcionamento do Web Apps ranhuras diagnóstico serviço. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Obtenha a análise de CPU de Site de diagnóstico ranhuras aplicações Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Obter falhas de Site do diagnóstico de ranhuras de aplicações da Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitelatency/read | Obtenha a latência de Site de diagnóstico de ranhuras de aplicações de Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Obtenha a análise de memória de Site de diagnóstico ranhuras aplicações Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Obter blocos de aplicativos de Web diagnóstico atualização de definição de reinício de Site. |
> | Ação | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Obtenha as Web Apps ranhuras diagnóstico Site reinício iniciada pelo utilizador. |
> | Ação | microsoft.web/sites/slots/diagnostics/siteswap/read | Obtenha a troca de Site de diagnóstico de ranhuras aplicações Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/threadcount/read | Obter a contagem de threads de diagnóstico de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/workeravailability/read | Obtenha Workeravailability de diagnóstico de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Obtenha os Reciclagem do processo de trabalho do diagnóstico de ranhuras do Web Apps. |
> | Ação | microsoft.web/sites/slots/domainownershipidentifiers/read | Obter identificadores de propriedade de domínio de ranhuras de aplicações de Web. |
> | Ação | microsoft.web/sites/slots/functions/listsecrets/action | Funções de ranhuras do lista segredos Web aplicações. |
> | Ação | microsoft.web/sites/slots/functions/read | Obter funções de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/hostnamebindings/delete | Elimine enlaces de nome de anfitrião de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/hostnamebindings/read | Obter os enlaces de nome de anfitrião de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/hostnamebindings/write | Atualize os enlaces de nome de anfitrião de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/hybridconnection/delete | Elimine a ligação de híbrida de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/hybridconnection/read | Obter ligação de híbrida de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/hybridconnection/write | Atualize a ligação de híbrida de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Elimine reencaminhamentos de espaços de nomes de ligação do Web Apps ranhuras híbrida. |
> | Ação | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Atualize reencaminhamentos de espaços de nomes de ligação do Web Apps ranhuras híbrida. |
> | Ação | microsoft.web/sites/slots/hybridconnectionrelays/read | Obtenha as Web Apps ranhuras híbrida ligação reencaminhamentos. |
> | Ação | microsoft.web/sites/slots/instances/deployments/read | Obter implementações de instâncias de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/instances/processes/delete | Elimine processos de instâncias de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/instances/processes/read | Obter processos de instâncias de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/instances/read | Obter instâncias de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/metricdefinitions/read | Obter definições de métricas de ranhuras de aplicações de Web. |
> | Ação | microsoft.web/sites/slots/metrics/read | Obter métricas de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/migratemysql/read | Obter Web blocos de aplicativos migrar MySql. |
> | Ação | microsoft.web/sites/slots/networktrace/action | Blocos de aplicativos de Web de rastreio de rede. |
> | Ação | microsoft.web/sites/slots/newpassword/action | Ranhuras de aplicações Web de newpassword. |
> | Ação | microsoft.web/sites/slots/operationresults/read | Obter resultados de operação de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/operations/read | Obter operações de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/perfcounters/read | Obter os contadores de desempenho de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/phplogging/read | Obtenha Phplogging de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/premieraddons/delete | Elimine complementos de Premier de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/premieraddons/read | Obtenha suplementos de Premier de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/premieraddons/write | Atualize complementos de Premier de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/processes/read | Obter processos de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/publiccertificates/delete | Elimine certificados públicos de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/publiccertificates/read | Obter certificados públicos de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/publiccertificates/write | Criar ou atualizar certificados públicos de ranhuras de aplicações Web. |
> | Ação | Microsoft.Web/sites/slots/publish/Action | Publicar uma ranhura de aplicação Web |
> | Ação | Microsoft.Web/sites/slots/publishxml/Action | Obter o xml do perfil de publicação para a ranhura de aplicação Web |
> | Ação | Microsoft.Web/sites/slots/Read | Obter as propriedades de um bloco de implementação de aplicação Web |
> | Ação | microsoft.web/sites/slots/recover/action | Recupere ranhuras de aplicações Web. |
> | Ação | Microsoft.Web/sites/slots/resetSlotConfig/Action | Repor a configuração do bloco de aplicação web |
> | Ação | microsoft.web/sites/slots/resourcehealthmetadata/read | Obter metadados de estado de funcionamento de recursos de ranhuras das aplicações Web. |
> | Ação | Microsoft.Web/sites/slots/restart/Action | Reiniciar uma ranhura de aplicação Web |
> | Ação | microsoft.web/sites/slots/restore/read | Obtenha o restauro de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/restore/write | Restaure ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/restorefrombackupblob/action | Restaure a ranhura de aplicações Web do Blob de cópia de segurança. |
> | Ação | microsoft.web/sites/slots/restorefromdeletedwebapp/action | Restaure ranhuras de aplicação Web da aplicação eliminada. |
> | Ação | microsoft.web/sites/slots/restoresnapshot/action | Restaure instantâneos de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/siteextensions/delete | Elimine extensões de Site de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/siteextensions/read | Obtenha as extensões de Site de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/siteextensions/write | Atualize extensões de Site de ranhuras de aplicações Web. |
> | Ação | Microsoft.Web/sites/slots/slotsdiffs/Action | Obter as diferenças na configuração entre a aplicação web e ranhuras |
> | Ação | Microsoft.Web/sites/slots/slotsswap/Action | Trocar as ranhuras de implementação de aplicação Web |
> | Ação | microsoft.web/sites/slots/snapshots/read | Obter instantâneos de ranhuras de aplicações Web. |
> | Ação | Microsoft.Web/sites/slots/sourcecontrols/Delete | Eliminar definições de configuração de controlo de origem da ranhura de aplicação Web |
> | Ação | Microsoft.Web/sites/slots/sourcecontrols/Read | Obter definições de configuração de controlo de código fonte da ranhura de aplicação Web |
> | Ação | Microsoft.Web/sites/slots/sourcecontrols/Write | Atualizar definições de configuração de controlo de origem da ranhura de aplicação Web |
> | Ação | Microsoft.Web/sites/slots/start/Action | Iniciar um bloco de aplicação Web |
> | Ação | Microsoft.Web/sites/slots/stop/Action | Parar uma ranhura de aplicação Web |
> | Ação | microsoft.web/sites/slots/sync/action | Ranhuras de aplicações Web de sincronização. |
> | Ação | microsoft.web/sites/slots/triggeredwebjobs/delete | Elimine aplicações/ranhuras WebJobs Acionadas da Web. |
> | Ação | microsoft.web/sites/slots/triggeredwebjobs/read | Obter aplicações/ranhuras WebJobs Acionadas da Web. |
> | Ação | microsoft.web/sites/slots/triggeredwebjobs/run/action | Ranhuras de aplicações Web de execução os WebJobs acionados. |
> | Ação | microsoft.web/sites/slots/usages/read | Obter utilizações de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/delete | Elimine ligações de rede Virtual de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Atualize os Gateways de ligações de rede Virtual de ranhuras aplicações Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/read | Obtenha ligações de rede Virtual de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/write | Atualize ligações de rede Virtual de ranhuras de aplicações Web. |
> | Ação | microsoft.web/sites/slots/webjobs/read | Obtenha o WebJobs de ranhuras de aplicações Web. |
> | Ação | Microsoft.Web/sites/slots/Write | Criar uma nova ranhura de aplicação Web ou atualizar um já existente |
> | Ação | Microsoft.Web/sites/slotsdiffs/Action | Obter as diferenças na configuração entre a aplicação web e ranhuras |
> | Ação | Microsoft.Web/sites/slotsswap/Action | Trocar as ranhuras de implementação de aplicação Web |
> | Ação | microsoft.web/sites/snapshots/read | Obter instantâneos de aplicações Web. |
> | Ação | Microsoft.Web/sites/sourcecontrols/Delete | Eliminar definições de configuração de controlo de origem da aplicação Web |
> | Ação | Microsoft.Web/sites/sourcecontrols/Read | Obter definições de configuração de controlo de código fonte da aplicação Web |
> | Ação | Microsoft.Web/sites/sourcecontrols/Write | Atualizar definições de configuração de controlo de origem da aplicação Web |
> | Ação | Microsoft.Web/sites/start/Action | Iniciar uma aplicação Web |
> | Ação | Microsoft.Web/sites/stop/Action | Parar uma aplicação Web |
> | Ação | microsoft.web/sites/sync/action | Sync Web Apps. |
> | Ação | microsoft.web/sites/syncfunctiontriggers/action | Acionadores de função de sincronização para aplicações Web. |
> | Ação | microsoft.web/sites/triggeredwebjobs/delete | Elimine WebJobs Acionadas de aplicações Web. |
> | Ação | microsoft.web/sites/triggeredwebjobs/history/read | Obter histórico de WebJobs Acionadas aplicações Web. |
> | Ação | microsoft.web/sites/triggeredwebjobs/read | Obtenha o WebJobs Acionadas de aplicações Web. |
> | Ação | microsoft.web/sites/triggeredwebjobs/run/action | Aplicações Web de execução os WebJobs acionados. |
> | Ação | microsoft.web/sites/usages/read | Obter utilizações de aplicações Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/delete | Elimine ligações de rede Virtual de aplicações Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/gateways/read | Obter Gateways de ligações de rede Virtual de aplicações Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/gateways/write | Atualize os Gateways de ligações de rede Virtual de aplicações Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/read | Obtenha ligações de rede Virtual de aplicações Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/write | Atualize ligações de rede Virtual de aplicações Web. |
> | Ação | microsoft.web/sites/webjobs/read | Obtenha o WebJobs de aplicações Web. |
> | Ação | Microsoft.Web/sites/Write | Criar uma nova aplicação Web ou atualizar um já existente |
> | Ação | microsoft.web/skus/read | Obter SKUs. |
> | Ação | microsoft.web/sourcecontrols/read | Obtenha controles da fonte. |
> | Ação | microsoft.web/sourcecontrols/write | Controles da fonte de atualização. |
> | Ação | microsoft.web/unregister/action | Anular o registo do fornecedor de recursos de Microsoft. Web para a subscrição. |
> | Ação | microsoft.web/validate/action | Valide. |
> | Ação | microsoft.web/verifyhostingenvironmentvnet/action | Certifique-se de Vnet do ambiente de alojamento. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de Acção | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.WorkloadMonitor/components/read | Obtém os componentes para o recurso |
> | Ação | Microsoft.WorkloadMonitor/componentsSummary/read | Obtém o resumo dos componentes |
> | Ação | Microsoft.WorkloadMonitor/monitorInstances/read | Obtém as instâncias de monitores para o recurso |
> | Ação | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Obtém o resumo de instâncias do monitor |
> | Ação | Microsoft.WorkloadMonitor/monitors/read | Obtém os monitores para o recurso |
> | Ação | Microsoft.WorkloadMonitor/monitors/write | Configure o monitor para o recurso |
> | Ação | Microsoft.WorkloadMonitor/notificationSettings/read | Obtém as definições de notificação para o recurso |
> | Ação | Microsoft.WorkloadMonitor/notificationSettings/write | Configurar definições de notificação para o recurso |
> | Ação | Microsoft.WorkloadMonitor/operations/read | Obtém as operações suportadas |

## <a name="next-steps"></a>Passos Seguintes

- [Funções personalizadas para recursos do Azure](custom-roles.md)
- [Built-in roles for Azure resources](built-in-roles.md) (Funções incorporadas para recursos do Azure)
