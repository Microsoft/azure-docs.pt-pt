---
title: Operações do provedor de recursos Azure Resource Manager | Microsoft Docs
description: Lista as operações disponíveis para os provedores de recursos do Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b00e699eb49d88e41dff18d6c86024c9b0537e50
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872320"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operações do provedor de recursos Azure Resource Manager

Este artigo lista as operações disponíveis para cada provedor de recursos de Azure Resource Manager. Essas operações podem ser usadas em [funções personalizadas](custom-roles.md) para fornecer [RBAC (controle de acesso baseado em função)](overview.md) granular para recursos no Azure. As cadeias de caracteres de operação têm o seguinte formato: `{Company}.{ProviderName}/{resourceType}/{action}`. Para obter uma lista de como os namespaces do provedor de recursos são mapeados para os serviços do Azure, consulte [corresponder provedor de recursos ao serviço](../azure-resource-manager/azure-services-resource-providers.md).

As operações do provedor de recursos estão sempre em evolução. Para obter as operações mais recentes, use a lista de operações [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) ou [AZ Provider](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.AAD/domainServices/delete | Excluir serviço de domínio |
> | Ação | Microsoft.AAD/domainServices/oucontainer/delete | Excluir contêiner de UO |
> | Ação | Microsoft.AAD/domainServices/oucontainer/read | Ler contêineres de UO |
> | Ação | Microsoft.AAD/domainServices/oucontainer/write | Gravar contêiner de UO |
> | Ação | Microsoft.AAD/domainServices/read | Ler serviços de domínio |
> | Ação | Microsoft.AAD/domainServices/write | Serviço de domínio de gravação |
> | Ação | Microsoft.AAD/locations/operationresults/read |  |
> | Ação | Microsoft.AAD/Operations/read |  |
> | Ação | Microsoft.AAD/register/action | Registrar serviço de domínio |
> | Ação | Microsoft.AAD/unregister/action | Cancelar o registro do serviço de domínio |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | microsoft.aadiam/diagnosticsettings/delete | Excluindo uma configuração de diagnóstico |
> | Ação | microsoft.aadiam/diagnosticsettings/read | Lendo uma configuração de diagnóstico |
> | Ação | microsoft.aadiam/diagnosticsettings/write | Escrevendo uma configuração de diagnóstico |
> | Ação | microsoft.aadiam/diagnosticsettingscategories/read | Lendo categorias de configuração de diagnóstico |
> | Ação | Microsoft. aadiam/metricDefinitions/Read | Lendo definições de métrica no nível do locatário |
> | Ação | Microsoft. aadiam/métricas/leitura | Lendo métricas no nível do locatário |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Addons/operations/read | Obtém as operações RP com suporte. |
> | Ação | Microsoft.Addons/register/action | Registrar a assinatura especificada com Microsoft. Complementos |
> | Ação | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Lista informações do plano de suporte atual para a assinatura especificada. |
> | Ação | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Remove o plano de suporte canônico especificado |
> | Ação | Microsoft.Addons/supportProviders/supportPlanTypes/read | Obter o estado do plano de suporte canônico especificado. |
> | Ação | Microsoft.Addons/supportProviders/supportPlanTypes/write | Adiciona o tipo de plano de suporte canônico especificado. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ADHybridHealthService/addsservices/action | Crie uma nova floresta para o locatário. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Obtém todos os servidores para o nome de serviço especificado. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/alerts/read | Obtém detalhes de alertas para a floresta, como Alertid, data de geração do alerta, último alerta detectado, descrição do alerta, última atualização, nível de alerta, estado do alerta, links de solução de problemas de alerta, etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/configuration/read | Obtém a configuração de serviço para a floresta. Exemplo-nome da floresta, nível funcional, função FSMO do mestre de nomeação de domínio, função FSMO do mestre de esquema, etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/delete | Exclui um serviço e seus servidores juntamente com os dados de integridade. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Obtém os detalhes de domínios e sites para a floresta. Exemplo-status de integridade, alertas ativos, alertas resolvidos, propriedades como nível funcional do domínio, floresta, mestre de infraestrutura, PDC, mestre de RID etc.  |
> | Ação | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Obtém a configuração de preferência do usuário para a floresta.<br>Exemplo-MetricCounterName como ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Configurações para os gráficos de interface do usuário, etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Obtém o resumo da floresta para a floresta determinada, como o nome da floresta, o número de domínios nesta floresta, o número de sites e os detalhes de sites, etc. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Obtém a lista de métricas com suporte para um determinado serviço.<br>Por exemplo, bloqueios de conta de extranet, total de solicitações com falha, solicitações de token pendentes (proxy), solicitações de token/s etc para o serviço ADFS.<br>Autenticações NTLM/s, associações com êxito de LDAP/s, tempo de ligação LDAP, Threads ativos LDAP, autenticações Kerberos/s, total de threads do ATQ etc. para ADDomainService.<br>Execute a latência do perfil, conexões TCP estabelecidas, bytes privados do agente do insights, exportar estatísticas para o Azure AD para o serviço ADSync. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Dado um serviço, essa API Obtém as informações de métricas.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas ao: bloqueios de conta de extranet, total de solicitações com falha, solicitações de token pendentes (proxy), solicitações de token/s, etc. para o serviço ADFederation.<br>Autenticações NTLM/s, associações com êxito de LDAP/s, tempo de ligação LDAP, Threads ativos LDAP, autenticações Kerberos/s, total de threads do ATQ, etc para o serviço ADDomain.<br>Execute a latência do perfil, conexões TCP estabelecidas, bytes privados do agente do insights, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Essa API Obtém a lista de todos os ADDomainServices integrados para um locatário Premium. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/read | Obtém detalhes do serviço para o nome do serviço especificado. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Obtém detalhes de replicação para todos os servidores para o nome de serviço especificado. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Obtém o número de controladores de domínio e seus erros de replicação, se houver. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Obtém a lista de controladores de domínio completos junto com os detalhes de replicação para a floresta especificada. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Adicione uma instância de servidor ao serviço. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Durante o registro do servidor de ADDomainService, essa API é chamada para obter as credenciais para a integração de novos servidores. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Exclui um servidor para um determinado serviço e locatário. |
> | Ação | Microsoft.ADHybridHealthService/addsservices/write | Cria ou atualiza a instância de ADDomainService para o locatário. |
> | Ação | Microsoft.ADHybridHealthService/configuration/action | Atualiza a configuração do locatário. |
> | Ação | Microsoft.ADHybridHealthService/configuration/read | Lê a configuração do locatário. |
> | Ação | Microsoft.ADHybridHealthService/configuration/write | Cria uma configuração de locatário. |
> | Ação | Microsoft.ADHybridHealthService/logs/contents/read | Obtém o conteúdo dos logs de instalação e registro do agente armazenados no BLOB. |
> | Ação | Microsoft.ADHybridHealthService/logs/read | Obtém os logs de instalação e registro do agente para o locatário. |
> | Ação | Microsoft.ADHybridHealthService/operations/read | Obtém a lista de operações com suporte pelo sistema. |
> | Ação | Microsoft.ADHybridHealthService/register/action | Registra o provedor de recursos de Serviço de Integridade do ADHybrid e habilita a criação do recurso de Serviço de Integridade do ADHybrid. |
> | Ação | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Obtém a lista de regiões disponíveis, usada pelo DevOps para dar suporte a incidentes de clientes. |
> | Ação | Microsoft.ADHybridHealthService/reports/badpassword/read | Obtém a lista de tentativas de senha inválidos para todos os usuários no Active Directory Serviço de Federação. |
> | Ação | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Obtém o URI de SAS de BLOB que contém o status e o resultado eventual do trabalho de relatório recentemente enfileirado para frequência de tentativas de nome de usuário/senha inválidos por UserId por IPAddress por dia para um determinado locatário. |
> | Ação | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Obtém a lista de locatários consentidos DevOps. Normalmente usado para atendimento ao cliente. |
> | Ação | Microsoft.ADHybridHealthService/reports/isdevops/read | Obtém um valor que indica se o locatário é DevOps consentido ou não. |
> | Ação | Microsoft. ADHybridHealthService/Reports/selectdevopstenant/Read | Atualiza UserID (ObjectID) para o locatário de ops de desenvolvimento selecionado. |
> | Ação | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Obtém a implantação selecionada para o locatário fornecido. |
> | Ação | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Uma ID de locatário fornecida Obtém o local de armazenamento do locatário. |
> | Ação | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Obtém o local geográfico do qual os dados serão acessados. |
> | Ação | Microsoft.ADHybridHealthService/services/action | Atualiza uma instância de serviço no locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/alerts/read | Lê os alertas de um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/alerts/read | Lê os alertas de um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Dado um nome de recurso verifica se um serviço tem tudo o que é necessário para usar esse recurso. |
> | Ação | Microsoft.ADHybridHealthService/services/delete | Exclui uma instância de serviço no locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/exporterrors/read | Obtém os erros de exportação para um determinado serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/exportstatus/read | Obtém o status de exportação de um determinado serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Obtém comentários de alertas para um determinado serviço e servidor. |
> | Ação | Microsoft.ADHybridHealthService/services/metricmetadata/read | Obtém a lista de métricas com suporte para um determinado serviço.<br>Por exemplo, bloqueios de conta de extranet, total de solicitações com falha, solicitações de token pendentes (proxy), solicitações de token/s etc para o serviço ADFS.<br>Autenticações NTLM/s, associações com êxito de LDAP/s, tempo de ligação LDAP, Threads ativos LDAP, autenticações Kerberos/s, total de threads do ATQ etc. para ADDomainService.<br>Execute a latência do perfil, conexões TCP estabelecidas, bytes privados do agente do insights, exportar estatísticas para o Azure AD para o serviço ADSync. |
> | Ação | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Dado um serviço, essa API Obtém a média de métricas para um determinado serviço.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas ao: bloqueios de conta de extranet, total de solicitações com falha, solicitações de token pendentes (proxy), solicitações de token/s, etc. para o serviço ADFederation.<br>Autenticações NTLM/s, associações com êxito de LDAP/s, tempo de ligação LDAP, Threads ativos LDAP, autenticações Kerberos/s, total de threads do ATQ, etc para o serviço ADDomain.<br>Execute a latência do perfil, conexões TCP estabelecidas, bytes privados do agente do insights, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/metrics/groups/read | Dado um serviço, essa API Obtém as informações de métricas.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas ao: bloqueios de conta de extranet, total de solicitações com falha, solicitações de token pendentes (proxy), solicitações de token/s, etc. para o serviço ADFederation.<br>Autenticações NTLM/s, associações com êxito de LDAP/s, tempo de ligação LDAP, Threads ativos LDAP, autenticações Kerberos/s, total de threads do ATQ, etc para o serviço ADDomain.<br>Execute a latência do perfil, conexões TCP estabelecidas, bytes privados do agente do insights, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Dado um serviço, essa API Obtém a exibição agregada para métricas para um determinado serviço.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas ao: bloqueios de conta de extranet, total de solicitações com falha, solicitações de token pendentes (proxy), solicitações de token/s, etc. para o serviço ADFederation.<br>Autenticações NTLM/s, associações com êxito de LDAP/s, tempo de ligação LDAP, Threads ativos LDAP, autenticações Kerberos/s, total de threads do ATQ, etc para o serviço ADDomain.<br>Execute a latência do perfil, conexões TCP estabelecidas, bytes privados do agente do insights, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Adicionar ou atualizar a configuração de monitoramento de um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Obtém as configurações de monitoramento para um determinado serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Adicionar ou atualizar configurações de monitoramento para um serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/premiumcheck/read | Essa API Obtém a lista de todos os serviços integrados para um locatário Premium. |
> | Ação | Microsoft.ADHybridHealthService/services/read | Lê as instâncias de serviço no locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Obtém todos os URIs de relatório IP arriscados nos últimos 7 dias. |
> | Ação | Microsoft.ADHybridHealthService/services/reports/details/read | Obtém o relatório dos principais usuários 50 com erros de senha inválidos nos últimos 7 dias |
> | Ação | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Gera um relatório IP arriscado e retorna um URI apontando para ele. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/action | Cria uma instância de servidor no serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Lê os alertas de um servidor. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Durante o registro do servidor, essa API é chamada para obter as credenciais para a integração de novos servidores. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Para um determinado servidor, essa API Obtém uma lista de tipos de dados que estão sendo carregados pelos servidores e a hora mais recente para cada carregamento. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/delete | Exclui uma instância de servidor no serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Obtém os detalhes do erro de exportação de sincronização para um determinado serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Dado um serviço, essa API Obtém as informações de métricas.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas ao: bloqueios de conta de extranet, total de solicitações com falha, solicitações de token pendentes (proxy), solicitações de token/s, etc. para o serviço ADFederation.<br>Autenticações NTLM/s, associações com êxito de LDAP/s, tempo de ligação LDAP, Threads ativos LDAP, autenticações Kerberos/s, total de threads do ATQ, etc para o serviço ADDomain.<br>Execute a latência do perfil, conexões TCP estabelecidas, bytes privados do agente do insights, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | Obtém a lista de conectores e executa nomes de perfil para o serviço e o membro de serviço fornecidos. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/read | Lê a instância de servidor no serviço. |
> | Ação | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Obtém a configuração de serviço para um determinado locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Obtém o status da lista de permissões de recursos para um determinado locatário. |
> | Ação | Microsoft.ADHybridHealthService/services/write | Cria uma instância de serviço no locatário. |
> | Ação | Microsoft.ADHybridHealthService/unregister/action | Cancela o registro da assinatura para ADHybrid Serviço de Integridade provedor de recursos. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Advisor/configurations/read | Obter configurações |
> | Ação | Microsoft.Advisor/configurations/write | Cria/atualiza a configuração |
> | Ação | Microsoft.Advisor/generateRecommendations/action | Obtém o status de recomendações de geração |
> | Ação | Microsoft.Advisor/generateRecommendations/read | Obtém o status de recomendações de geração |
> | Ação | Microsoft.Advisor/metadata/read | Obter metadados |
> | Ação | Microsoft.Advisor/operations/read | Obter as operações para o Microsoft Advisor |
> | Ação | Microsoft.Advisor/recommendations/available/action | A nova recomendação está disponível no Microsoft Advisor |
> | Ação | Microsoft.Advisor/recommendations/read | Leituras de recomendações |
> | Ação | Microsoft.Advisor/recommendations/suppressions/delete | Exclui a supressão |
> | Ação | Microsoft.Advisor/recommendations/suppressions/read | Obter supressões |
> | Ação | Microsoft.Advisor/recommendations/suppressions/write | Cria/atualiza supressões |
> | Ação | Microsoft.Advisor/register/action | Registra a assinatura do Microsoft Advisor |
> | Ação | Microsoft.Advisor/suppressions/delete | Exclui a supressão |
> | Ação | Microsoft.Advisor/suppressions/read | Obter supressões |
> | Ação | Microsoft.Advisor/suppressions/write | Cria/atualiza supressões |
> | Ação | Microsoft.Advisor/unregister/action | Cancela o registro da assinatura do Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.AlertsManagement/actionRules/delete | Excluir regra de ação em uma determinada assinatura. |
> | Ação | Microsoft.AlertsManagement/actionRules/read | Obtenha todas as regras de ação para os filtros de entrada. |
> | Ação | Microsoft.AlertsManagement/actionRules/write | Criar ou atualizar regra de ação em uma determinada assinatura |
> | Ação | Microsoft.AlertsManagement/alerts/changestate/action | Altere o estado do alerta. |
> | Ação | Microsoft.AlertsManagement/alerts/diagnostics/read | Obter todos os diagnósticos para o alerta |
> | Ação | Microsoft.AlertsManagement/alerts/history/read | Obter o histórico do alerta |
> | Ação | Microsoft.AlertsManagement/alerts/read | Obtenha todos os alertas para os filtros de entrada. |
> | Ação | Microsoft.AlertsManagement/alertsList/read | Obter todos os alertas para os filtros de entrada nas assinaturas |
> | Ação | Microsoft. AlertsManagement/alertsMetaData/Read | Obter metadados de alertas para o parâmetro de entrada. |
> | Ação | Microsoft.AlertsManagement/alertsSummary/read | Obter o resumo dos alertas |
> | Ação | Microsoft.AlertsManagement/alertsSummaryList/read | Obter o resumo de alertas entre assinaturas |
> | Ação | Microsoft.AlertsManagement/Operations/read | Lê as operações fornecidas |
> | Ação | Microsoft.AlertsManagement/register/action | Registra a assinatura para o gerenciamento de alertas da Microsoft |
> | Ação | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Excluir regra de alerta do detector inteligente em uma determinada assinatura |
> | Ação | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Obter todas as regras de alerta do detector inteligente para os filtros de entrada |
> | Ação | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Criar ou atualizar a regra de alerta do detector inteligente em uma determinada assinatura |
> | Ação | Microsoft.AlertsManagement/smartGroups/changestate/action | Alterar o estado do grupo inteligente |
> | Ação | Microsoft.AlertsManagement/smartGroups/history/read | Obter o histórico do grupo inteligente |
> | Ação | Microsoft.AlertsManagement/smartGroups/read | Obter todos os grupos inteligentes para os filtros de entrada |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Verifica se o nome de Analysis Server fornecido é válido e não está em uso. |
> | Ação | Microsoft.AnalysisServices/locations/operationresults/read | Recupera as informações do resultado da operação especificada. |
> | Ação | Microsoft.AnalysisServices/locations/operationstatuses/read | Recupera as informações do status de operação especificado. |
> | Ação | Microsoft.AnalysisServices/operations/read | Recupera as informações de operações |
> | Ação | Microsoft.AnalysisServices/register/action | Registra Analysis Services provedor de recursos. |
> | Ação | Microsoft.AnalysisServices/servers/delete | Exclui o Analysis Server. |
> | Ação | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Liste o status do gateway associado ao servidor. |
> | Ação | Microsoft.AnalysisServices/servers/read | Recupera as informações do Analysis Server especificado. |
> | Ação | Microsoft.AnalysisServices/servers/resume/action | Retoma o Analysis Server. |
> | Ação | Microsoft.AnalysisServices/servers/skus/read | Recuperar informações de SKU disponíveis para o servidor |
> | Ação | Microsoft.AnalysisServices/servers/suspend/action | Suspende o Analysis Server. |
> | Ação | Microsoft.AnalysisServices/servers/write | Cria ou atualiza o Analysis Server especificado. |
> | Ação | Microsoft.AnalysisServices/skus/read | Recupera as informações de SKUs |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ApiManagement/checkNameAvailability/read | Verifica se o nome do serviço fornecido está disponível |
> | Ação | Microsoft.ApiManagement/operations/read | Ler todas as operações de API disponíveis para o recurso Microsoft. ApiManagement |
> | Ação | Microsoft.ApiManagement/register/action | Registrar a assinatura para o provedor de recursos Microsoft. ApiManagement |
> | Ação | Microsoft.ApiManagement/reports/read | Obtenha relatórios agregados por períodos de tempo, região geográfica, desenvolvedores, produtos, APIs, operações, assinatura e byRequest. |
> | Ação | Microsoft.ApiManagement/service/apis/delete | Exclui a API especificada da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/apis/diagnostics/delete | Exclui o diagnóstico especificado de uma API. |
> | Ação | Microsoft.ApiManagement/service/apis/diagnostics/read | Lista todos os diagnósticos de uma API. ou obtém os detalhes do diagnóstico para uma API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/diagnostics/write | Cria um novo diagnóstico para uma API ou atualiza um existente. ou atualiza os detalhes do diagnóstico para uma API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Exclui o comentário especificado de um problema. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/attachments/read | Lista todos os anexos para o problema associado à API especificada. ou obtém os detalhes do anexo do problema para uma API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/attachments/write | Cria um novo anexo para o problema em uma API ou atualiza um existente. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/comments/delete | Exclui o comentário especificado de um problema. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/comments/read | Lista todos os comentários para o problema associado à API especificada. ou obtém os detalhes do comentário do problema para uma API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/comments/write | Cria um novo comentário para o problema em uma API ou atualiza um existente. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/delete | Exclui o problema especificado de uma API. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/read | Lista todos os problemas associados à API especificada. ou obtém os detalhes do problema para uma API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/issues/write | Cria um novo problema para uma API ou atualiza um existente. ou atualiza um problema existente para uma API. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/delete | Exclui a operação especificada na API. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policies/delete | Exclui a configuração de política na operação de API. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policies/read | Obtenha a lista de configurações de política no nível de operação da API. ou obtenha a configuração de política no nível de operação da API. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policies/write | Cria ou atualiza a configuração de política para o nível de operação da API. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policy/delete | Excluir a configuração de política no nível de operação |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policy/read | Obter a configuração de política no nível de operação |
> | Ação | Microsoft.ApiManagement/service/apis/operations/policy/write | Criar configuração de política no nível de operação |
> | Ação | Microsoft.ApiManagement/service/apis/operations/read | Lista uma coleção das operações para a API especificada. ou obtém os detalhes da operação de API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/tags/delete | Desanexe a marca da operação. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/tags/read | Lista todas as marcas associadas à operação. ou obter marca associada à operação. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/tags/write | Atribua a marcação à operação. |
> | Ação | Microsoft.ApiManagement/service/apis/operations/write | Cria uma nova operação na API ou atualiza uma existente. ou atualiza os detalhes da operação na API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/operationsByTags/read | Lista uma coleção de operações associadas a marcas. |
> | Ação | Microsoft.ApiManagement/service/apis/policies/delete | Exclui a configuração de política na API. |
> | Ação | Microsoft.ApiManagement/service/apis/policies/read | Obtenha a configuração de política no nível da API. ou obtenha a configuração de política no nível da API. |
> | Ação | Microsoft.ApiManagement/service/apis/policies/write | Cria ou atualiza a configuração de política para a API. |
> | Ação | Microsoft.ApiManagement/service/apis/policy/delete | Excluir a configuração de política no nível da API |
> | Ação | Microsoft.ApiManagement/service/apis/policy/read | Obter a configuração de política no nível da API |
> | Ação | Microsoft.ApiManagement/service/apis/policy/write | Criar configuração de política no nível da API |
> | Ação | Microsoft.ApiManagement/service/apis/products/read | Lista todos os produtos dos quais a API faz parte. |
> | Ação | Microsoft.ApiManagement/service/apis/read | Lista todas as APIs da instância do serviço de gerenciamento de API. ou obtém os detalhes da API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/releases/delete | Remove todas as versões da API ou exclui a versão especificada na API. |
> | Ação | Microsoft.ApiManagement/service/apis/releases/read | Lista todas as versões de uma API.<br>Uma versão de API é criada ao tornar a revisão da API atual.<br>As versões também são usadas para reverter para revisões anteriores.<br>Os resultados serão paginados e poderão ser restritos pelos parâmetros $top e $skip.<br>ou retorna os detalhes de uma versão de API. |
> | Ação | Microsoft.ApiManagement/service/apis/releases/write | Cria uma nova versão para a API. ou atualiza os detalhes da versão da API especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apis/revisions/delete | Remove todas as revisões de uma API |
> | Ação | Microsoft.ApiManagement/service/apis/revisions/read | Lista todas as revisões de uma API. |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/delete | Exclui a configuração de esquema na API. |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/read | Obtenha a configuração do esquema no nível da API. ou obtenha a configuração do esquema no nível da API. |
> | Ação | Microsoft.ApiManagement/service/apis/schemas/write | Cria ou atualiza a configuração de esquema para a API. |
> | Ação | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Exclua a descrição da marca para a API. |
> | Ação | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Lista todas as descrições de marcas no escopo da API. O modelo semelhante ao Swagger-tagDescription é definido no nível da API, mas a marca pode ser atribuída às operações ou obter a descrição da marca no escopo da API |
> | Ação | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Criar/atualizar a descrição da marca no escopo da API. |
> | Ação | Microsoft.ApiManagement/service/apis/tags/delete | Desanexe a marca da API. |
> | Ação | Microsoft.ApiManagement/service/apis/tags/read | Lista todas as marcas associadas à API. ou obter marca associada à API. |
> | Ação | Microsoft.ApiManagement/service/apis/tags/write | Atribua a marca à API. |
> | Ação | Microsoft.ApiManagement/service/apis/write | Cria novas ou atualiza a API especificada existente da instância do serviço de gerenciamento de API. ou atualiza a API especificada da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/apisByTags/read | Lista uma coleção de APIs associadas a marcas. |
> | Ação | Microsoft.ApiManagement/service/apiVersionSets/delete | Exclui o conjunto de versão de API específico. |
> | Ação | Microsoft.ApiManagement/service/apiVersionSets/read | Lista uma coleção de conjuntos de versão de API na instância de serviço especificada. ou obtém os detalhes do conjunto de versão de API especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Obter lista de entidades de versão |
> | Ação | Microsoft.ApiManagement/service/apiVersionSets/write | Cria ou atualiza um conjunto de versões de API. ou atualiza os detalhes do Versionset de API especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Atualiza os recursos Microsoft. ApiManagement em execução na rede virtual para escolher as configurações de rede atualizadas. |
> | Ação | Microsoft.ApiManagement/service/authorizationServers/delete | Exclui instância de servidor de autorização específica. |
> | Ação | Microsoft. ApiManagement/Service/authorizationServers/listSecrets/Action | Obtém segredos para o servidor de autorização. |
> | Ação | Microsoft.ApiManagement/service/authorizationServers/read | Lista uma coleção de servidores de autorização definidos em uma instância de serviço. ou obtém os detalhes do servidor de autorização sem segredos. |
> | Ação | Microsoft.ApiManagement/service/authorizationServers/write | Cria um novo servidor de autorização ou atualiza um servidor de autorização existente. ou atualiza os detalhes do servidor de autorização especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/backends/delete | Exclui o back-end especificado. |
> | Ação | Microsoft.ApiManagement/service/backends/read | Lista uma coleção de back-ends na instância de serviço especificada. ou obtém os detalhes do back-end especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/backends/reconnect/action | Notifica o proxy APIM para criar uma nova conexão com o back-end após o tempo limite especificado. Se nenhum tempo limite for especificado, o tempo limite de 2 minutos será usado. |
> | Ação | Microsoft.ApiManagement/service/backends/write | Cria ou atualiza um back-end. ou atualiza um back-end existente. |
> | Ação | Microsoft.ApiManagement/service/backup/action | Serviço de gerenciamento de API de backup para o contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Ação | Microsoft.ApiManagement/service/caches/delete | Exclui o cache específico. |
> | Ação | Microsoft.ApiManagement/service/caches/read | Lista uma coleção de todos os caches externos na instância de serviço especificada. ou obtém os detalhes do cache especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/caches/write | Cria ou atualiza um cache externo a ser usado na instância de gerenciamento de API. ou atualiza os detalhes do cache especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/certificates/delete | Exclui um certificado específico. |
> | Ação | Microsoft.ApiManagement/service/certificates/read | Lista uma coleção de todos os certificados na instância de serviço especificada. ou obtém os detalhes do certificado especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/certificates/write | Cria ou atualiza o certificado que está sendo usado para autenticação com o back-end. |
> | Ação | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Remove o item de conteúdo especificado. |
> | Ação | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Retorna a lista de itens de conteúdo ou retorna detalhes do item de conteúdo |
> | Ação | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Cria um novo item de conteúdo ou atualiza o item de conteúdo especificado |
> | Ação | Microsoft.ApiManagement/service/contentTypes/read | Retorna a lista de tipos de conteúdo |
> | Ação | Microsoft.ApiManagement/service/delete | Excluir instância do serviço de gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/diagnostics/delete | Exclui o diagnóstico especificado. |
> | Ação | Microsoft.ApiManagement/service/diagnostics/read | Lista todos os diagnósticos da instância do serviço de gerenciamento de API. ou obtém os detalhes do diagnóstico especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/diagnostics/write | Cria um novo diagnóstico ou atualiza um existente. ou atualiza os detalhes do diagnóstico especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/serviço/gateways/ação | Recupera a configuração do gateway. ou atualiza a pulsação do gateway. |
> | Ação | Microsoft. ApiManagement/Service/gateways/APIs/excluir | Exclui a API especificada do gateway especificado. |
> | Ação | Microsoft. ApiManagement/Service/gateways/APIs/leitura | Lista uma coleção das APIs associadas a um gateway. |
> | Ação | Microsoft. ApiManagement/Service/gateways/APIs/Write | Adiciona uma API ao gateway especificado. |
> | Ação | Microsoft. ApiManagement/Service/gateways/Delete | Exclui gateway específico. |
> | Ação | Microsoft. ApiManagement/Service/gateways/hostnameConfigurations/Read | Lista a coleção de configurações de nome de host para o gateway especificado. |
> | Ação | Microsoft. ApiManagement/serviço/gateways/chaves/ação | Recupera as chaves de gateway. |
> | Ação | Microsoft. ApiManagement/Service/gateways/leitura | Lista uma coleção de gateways registrados com a instância de serviço. ou obtém os detalhes do gateway especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/gateways/regeneratePrimaryKey/Action | Regenera a chave primária do gateway invalidationg todos os tokens criados com ele. |
> | Ação | Microsoft. ApiManagement/Service/gateways/regenerateSecondaryKey/Action | Regenera a chave do gateway secundário invalidationg quaisquer tokens criados com ele. |
> | Ação | Microsoft. ApiManagement/Service/gateways/token/ação | Obtém o token de autorização de acesso compartilhado para o gateway. |
> | Ação | Microsoft. ApiManagement/Service/gateways/Write | Cria ou atualiza um gateway a ser usado na instância de gerenciamento de API. ou atualiza os detalhes do gateway especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/getssotoken/action | Obtém o token SSO que pode ser usado para fazer logon no portal herdado do serviço de gerenciamento de API como administrador |
> | Ação | Microsoft.ApiManagement/service/groups/delete | Exclui o grupo específico da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/groups/read | Lista uma coleção de grupos definidos em uma instância de serviço. ou obtém os detalhes do grupo especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/groups/users/delete | Remover usuário existente do grupo existente. |
> | Ação | Microsoft.ApiManagement/service/groups/users/read | Lista uma coleção de entidades de usuário associadas ao grupo. |
> | Ação | Microsoft.ApiManagement/service/groups/users/write | Adicionar usuário existente a um grupo existente |
> | Ação | Microsoft.ApiManagement/service/groups/write | Cria ou atualiza um grupo. ou atualiza os detalhes do grupo especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/identityProviders/delete | Exclui a configuração do provedor de identidade especificado. |
> | Ação | Microsoft. ApiManagement/Service/identityProviders/listSecrets/Action | Obtém segredos do provedor de identidade. |
> | Ação | Microsoft.ApiManagement/service/identityProviders/read | Lista uma coleção de provedor de identidade configurada na instância de serviço especificada. ou obtém os detalhes de configuração do provedor de identidade sem segredos. |
> | Ação | Microsoft.ApiManagement/service/identityProviders/write | Cria ou atualiza a configuração Identityprovider. ou atualiza uma configuração existente do Identityprovider. |
> | Ação | Microsoft.ApiManagement/service/issues/read | Lista uma coleção de problemas na instância de serviço especificada. ou obtém detalhes do problema de gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/locations/networkstatus/read | Obtém o status de acesso à rede dos recursos nos quais o serviço depende no local. |
> | Ação | Microsoft.ApiManagement/service/loggers/delete | Exclui o agente de log especificado. |
> | Ação | Microsoft.ApiManagement/service/loggers/read | Lista uma coleção de agentes de log na instância de serviço especificada. ou obtém os detalhes do agente de log especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/loggers/write | Cria ou atualiza um agente de log. ou atualiza um agente de log existente. |
> | Ação | Microsoft.ApiManagement/service/managedeployments/action | Alterar SKU/unidades, adicionar/remover implantações regionais do serviço de gerenciamento de API |
> | Ação | Microsoft. ApiManagement/Service/namedValues/Delete | Exclui o valor nomeado específico da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/Service/namedValues/listSecrets/Action | Obtém os segredos do valor nomeado especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/namedValues/Read | Lista uma coleção de valores nomeados definidos em uma instância de serviço. ou obtém os detalhes do valor nomeado especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/namedValues/Write | Cria ou atualiza o valor nomeado. ou atualiza o valor nomeado específico. |
> | Ação | Microsoft.ApiManagement/service/networkstatus/read | Obtém o status de acesso à rede dos recursos nos quais o serviço depende. |
> | Ação | Microsoft.ApiManagement/service/notifications/action | Envia a notificação para um usuário especificado |
> | Ação | Microsoft.ApiManagement/service/notifications/read | Lista uma coleção de propriedades definidas em uma instância de serviço. ou obtém os detalhes da notificação especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Remove o email da lista de notificações. |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Obtém a lista dos emails de destinatário da notificação inscritos em uma notificação. |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Adiciona o endereço de email à lista de destinatários da notificação. |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Remove o usuário de gerenciamento de API da lista de notificações. |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Obtém a lista do usuário do destinatário da notificação inscrito na notificação. |
> | Ação | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Adiciona o usuário de gerenciamento de API à lista de destinatários da notificação. |
> | Ação | Microsoft.ApiManagement/service/notifications/write | Criar ou atualizar a notificação do editor de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/openidConnectProviders/delete | Exclui o provedor do OpenID Connect específico da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/Service/openidConnectProviders/listSecrets/Action | Obtém segredos do provedor do OpenID Connect específico. |
> | Ação | Microsoft.ApiManagement/service/openidConnectProviders/read | Listas de todos os provedores do OpenId Connect. ou Obtém um provedor do OpenID Connect específico sem segredos. |
> | Ação | Microsoft.ApiManagement/service/openidConnectProviders/write | Cria ou atualiza o provedor do OpenID Connect. ou atualiza o provedor do OpenID Connect específico. |
> | Ação | Microsoft.ApiManagement/service/operationresults/read | Obtém o status atual da operação de longa execução |
> | Ação | Microsoft.ApiManagement/service/policies/delete | Exclui a configuração de política global do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/policies/read | Lista todas as definições de política global do serviço de gerenciamento de API. ou obtenha a definição de política global do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/policies/write | Cria ou atualiza a configuração de política global do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/policy/delete | Excluir a configuração de política no nível do locatário |
> | Ação | Microsoft.ApiManagement/service/policy/read | Obter a configuração de política no nível do locatário |
> | Ação | Microsoft.ApiManagement/service/policy/write | Criar configuração de política no nível do locatário |
> | Ação | Microsoft. ApiManagement/Service/policyDescriptions/Read | Lista todas as descrições de política. |
> | Ação | Microsoft.ApiManagement/service/policySnippets/read | Lista todos os trechos de política. |
> | Ação | Microsoft.ApiManagement/service/portalsettings/read | Obtenha configurações de entrada para o portal ou obtenha configurações de inscrição para o portal ou obtenha configurações de delegação para o Portal. |
> | Ação | Microsoft.ApiManagement/service/portalsettings/write | Atualize as configurações de entrada. ou criar ou atualizar configurações de entrada. ou atualize as configurações de inscrição ou atualize as configurações de inscrição ou atualize as configurações de delegação. ou criar ou atualizar configurações de delegação. |
> | Ação | Microsoft.ApiManagement/service/products/apis/delete | Exclui a API especificada do produto especificado. |
> | Ação | Microsoft.ApiManagement/service/products/apis/read | Lista uma coleção das APIs associadas a um produto. |
> | Ação | Microsoft.ApiManagement/service/products/apis/write | Adiciona uma API ao produto especificado. |
> | Ação | Microsoft.ApiManagement/service/products/delete | Excluir produto. |
> | Ação | Microsoft.ApiManagement/service/products/groups/delete | Exclui a associação entre o grupo e o produto especificados. |
> | Ação | Microsoft.ApiManagement/service/products/groups/read | Lista a coleção de grupos de desenvolvedores associados ao produto especificado. |
> | Ação | Microsoft.ApiManagement/service/products/groups/write | Adiciona a associação entre o grupo de desenvolvedores especificado com o produto especificado. |
> | Ação | Microsoft.ApiManagement/service/products/policies/delete | Exclui a configuração de política no produto. |
> | Ação | Microsoft.ApiManagement/service/products/policies/read | Obtenha a configuração de política no nível do produto. ou obtenha a configuração de política no nível do produto. |
> | Ação | Microsoft.ApiManagement/service/products/policies/write | Cria ou atualiza a configuração de política para o produto. |
> | Ação | Microsoft.ApiManagement/service/products/policy/delete | Excluir a configuração de política no nível do produto |
> | Ação | Microsoft.ApiManagement/service/products/policy/read | Obter a configuração de política no nível do produto |
> | Ação | Microsoft.ApiManagement/service/products/policy/write | Criar configuração de política no nível do produto |
> | Ação | Microsoft.ApiManagement/service/products/read | Lista uma coleção de produtos na instância de serviço especificada. ou obtém os detalhes do produto especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/products/subscriptions/read | Lista a coleção de assinaturas para o produto especificado. |
> | Ação | Microsoft.ApiManagement/service/products/tags/delete | Desanexe a marca do produto. |
> | Ação | Microsoft.ApiManagement/service/products/tags/read | Lista todas as marcas associadas ao produto. ou obtenha a marca associada ao produto. |
> | Ação | Microsoft.ApiManagement/service/products/tags/write | Atribua a marca ao produto. |
> | Ação | Microsoft.ApiManagement/service/products/write | Cria ou atualiza um produto. ou atualize os detalhes do produto existente. |
> | Ação | Microsoft.ApiManagement/service/productsByTags/read | Lista uma coleção de produtos associados às marcas. |
> | Ação | Microsoft.ApiManagement/service/properties/delete | Exclui a propriedade específica da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/Service/Properties/listSecrets/Action | Obtém os segredos da propriedade especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/properties/read | Lista uma coleção de propriedades definidas em uma instância de serviço. ou obtém os detalhes da propriedade especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/properties/write | Cria ou atualiza uma propriedade. ou atualiza a propriedade específica. |
> | Ação | Microsoft.ApiManagement/service/quotas/periods/read | Obter valor do contador de cotas para o período |
> | Ação | Microsoft.ApiManagement/service/quotas/periods/write | Definir valor atual do contador de cota |
> | Ação | Microsoft.ApiManagement/service/quotas/read | Obter valores para cota |
> | Ação | Microsoft.ApiManagement/service/quotas/write | Definir valor atual do contador de cota |
> | Ação | Microsoft.ApiManagement/service/read | Ler metadados para uma instância do serviço de gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/regions/read | Lista todas as regiões do Azure nas quais o serviço existe. |
> | Ação | Microsoft.ApiManagement/service/reports/read | Obtenha o relatório agregado por períodos de tempo ou obtenha o relatório agregado por região geográfica ou obtenha um relatório agregado pelos desenvolvedores.<br>ou obter relatório agregado por produtos.<br>ou obter relatório agregado por APIs ou obter relatório agregado por operações ou obter relatório agregado por assinatura.<br>ou obter dados de relatórios de solicitações |
> | Ação | Microsoft.ApiManagement/service/restore/action | Restaurar o serviço de gerenciamento de API do contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Ação | Microsoft.ApiManagement/service/subscriptions/delete | Exclui a assinatura especificada. |
> | Ação | Microsoft. ApiManagement/Service/subscriptions/listSecrets/Action | Obtém as chaves de assinatura especificadas. |
> | Ação | Microsoft.ApiManagement/service/subscriptions/read | Lista todas as assinaturas da instância do serviço de gerenciamento de API. ou obtém a entidade de assinatura especificada (sem chaves). |
> | Ação | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Regenera a chave primária da assinatura existente da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Regenera a chave secundária da assinatura existente da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/subscriptions/write | Cria ou atualiza a assinatura do usuário especificado para o produto especificado. ou atualiza os detalhes de uma assinatura especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/tagResources/read | Lista uma coleção de recursos associados com marcas. |
> | Ação | Microsoft.ApiManagement/service/tags/delete | Exclui a marca específica da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft.ApiManagement/service/tags/read | Lista uma coleção de marcas definidas em uma instância de serviço. ou obtém os detalhes da marca especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/tags/write | Cria uma marca. ou atualiza os detalhes da marca especificada por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/templates/delete | Redefinir modelo de email de gerenciamento de API padrão |
> | Ação | Microsoft.ApiManagement/service/templates/read | Obtém todos os modelos de email ou obtém detalhes do modelo de email de gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/templates/write | Criar ou atualizar modelo de email de gerenciamento de API ou atualizações de modelo de email de gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/tenant/delete | Remover configuração de política para o locatário |
> | Ação | Microsoft.ApiManagement/service/tenant/deploy/action | Executa uma tarefa de implantação para aplicar as alterações da ramificação git especificada para a configuração no banco de dados. |
> | Ação | Microsoft. ApiManagement/Service/locatário/listSecrets/Action | Obter detalhes de informações de acesso do locatário |
> | Ação | Microsoft.ApiManagement/service/tenant/operationResults/read | Obter lista de resultados da operação ou obter resultado de uma operação específica |
> | Ação | Microsoft.ApiManagement/service/tenant/read | Obtenha a definição de política global do serviço de gerenciamento de API. ou obter detalhes de informações de acesso do locatário |
> | Ação | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Regenerar chave de acesso primária |
> | Ação | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Regenerar chave de acesso secundária |
> | Ação | Microsoft.ApiManagement/service/tenant/save/action | Cria a confirmação com o instantâneo de configuração para a ramificação especificada no repositório |
> | Ação | Microsoft.ApiManagement/service/tenant/syncState/read | Obter o status da última sincronização git |
> | Ação | Microsoft.ApiManagement/service/tenant/validate/action | Valida as alterações do Branch git especificado |
> | Ação | Microsoft.ApiManagement/service/tenant/write | Definir a configuração de política para o locatário ou atualizar detalhes de informações de acesso do locatário |
> | Ação | Microsoft.ApiManagement/service/updatecertificate/action | Carregar o certificado SSL para um serviço de gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/updatehostname/action | Configurar, atualizar ou remover nomes de domínio personalizados para um serviço de gerenciamento de API |
> | Ação | Microsoft.ApiManagement/service/users/action | Registrar um novo usuário |
> | Ação | Microsoft.ApiManagement/service/users/confirmations/send/action | Envia confirmação |
> | Ação | Microsoft.ApiManagement/service/users/delete | Exclui um usuário específico. |
> | Ação | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Recupera uma URL de redirecionamento que contém um token de autenticação para assinar um determinado usuário no portal do desenvolvedor. |
> | Ação | Microsoft.ApiManagement/service/users/groups/read | Lista todos os grupos de usuários. |
> | Ação | Microsoft.ApiManagement/service/users/identities/read | Lista de todas as identidades de usuário. |
> | Ação | Microsoft.ApiManagement/service/users/keys/read | Obter chaves associadas ao usuário |
> | Ação | Microsoft.ApiManagement/service/users/read | Lista uma coleção de usuários registrados na instância de serviço especificada. ou obtém os detalhes do usuário especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/users/subscriptions/read | Lista a coleção de assinaturas do usuário especificado. |
> | Ação | Microsoft.ApiManagement/service/users/token/action | Obtém o token de autorização de acesso compartilhado para o usuário. |
> | Ação | Microsoft.ApiManagement/service/users/write | Cria ou atualiza um usuário. ou atualiza os detalhes do usuário especificado por seu identificador. |
> | Ação | Microsoft.ApiManagement/service/write | Criar uma nova instância do serviço de gerenciamento de API |
> | Ação | Microsoft.ApiManagement/unregister/action | Cancelar registro de assinatura para o provedor de recursos Microsoft. ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Authorization/classicAdministrators/delete | Remove o administrador da assinatura. |
> | Ação | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Obtém os status da operação do administrador da assinatura. |
> | Ação | Microsoft.Authorization/classicAdministrators/read | Lê os administradores da assinatura. |
> | Ação | Microsoft.Authorization/classicAdministrators/write | Adicionar ou modificar o administrador de uma assinatura. |
> | Ação | Microsoft.Authorization/denyAssignments/delete | Excluir uma atribuição de negação no escopo especificado. |
> | Ação | Microsoft.Authorization/denyAssignments/read | Obter informações sobre uma atribuição de negação. |
> | Ação | Microsoft.Authorization/denyAssignments/write | Criar uma atribuição de negação no escopo especificado. |
> | Ação | Microsoft.Authorization/elevateAccess/action | Concede ao chamador o acesso de Administrador de Acesso de Utilizador no âmbito do inquilino |
> | Ação | Microsoft.Authorization/locks/delete | Excluir bloqueios no escopo especificado. |
> | Ação | Microsoft.Authorization/locks/read | Obtém bloqueios no escopo especificado. |
> | Ação | Microsoft.Authorization/locks/write | Adicionar bloqueios no escopo especificado. |
> | Ação | Microsoft.Authorization/operations/read | Obtém a lista de operações |
> | Ação | Microsoft.Authorization/permissions/read | Lista todas as permissões que o chamador tem em um determinado escopo. |
> | Ação | Microsoft. Authorization/Policies/Audit/Action | Ação executada como resultado da avaliação de Azure Policy com o efeito de ' auditoria ' |
> | Ação | Microsoft. Authorization/Policies/auditIfNotExists/Action | Ação executada como resultado da avaliação de Azure Policy com o efeito ' auditIfNotExists ' |
> | Ação | Microsoft. Authorization/Policies/Deny/Action | Ação executada como resultado da avaliação de Azure Policy com o efeito ' negar ' |
> | Ação | Microsoft. Authorization/Policies/deployIfNotExists/Action | Ação executada como resultado da avaliação de Azure Policy com o efeito ' deployIfNotExists ' |
> | Ação | Microsoft.Authorization/policyAssignments/delete | Excluir uma atribuição de política no escopo especificado. |
> | Ação | Microsoft.Authorization/policyAssignments/read | Obter informações sobre uma atribuição de política. |
> | Ação | Microsoft.Authorization/policyAssignments/write | Crie uma atribuição de política no escopo especificado. |
> | Ação | Microsoft.Authorization/policyDefinitions/delete | Excluir uma definição de política. |
> | Ação | Microsoft.Authorization/policyDefinitions/read | Obter informações sobre uma definição de política. |
> | Ação | Microsoft.Authorization/policyDefinitions/write | Crie uma definição de política personalizada. |
> | Ação | Microsoft.Authorization/policySetDefinitions/delete | Excluir uma definição de conjunto de políticas. |
> | Ação | Microsoft.Authorization/policySetDefinitions/read | Obter informações sobre uma definição de conjunto de políticas. |
> | Ação | Microsoft.Authorization/policySetDefinitions/write | Crie uma definição de conjunto de políticas personalizadas. |
> | Ação | Microsoft.Authorization/providerOperations/read | Obtenha operações para todos os provedores de recursos que podem ser usados em definições de função. |
> | Ação | Microsoft.Authorization/roleAssignments/delete | Excluir uma atribuição de função no escopo especificado. |
> | Ação | Microsoft.Authorization/roleAssignments/read | Obter informações sobre uma atribuição de função. |
> | Ação | Microsoft.Authorization/roleAssignments/write | Crie uma atribuição de função no escopo especificado. |
> | Ação | Microsoft.Authorization/roleDefinitions/delete | Exclua a definição de função personalizada especificada. |
> | Ação | Microsoft.Authorization/roleDefinitions/read | Obter informações sobre uma definição de função. |
> | Ação | Microsoft.Authorization/roleDefinitions/write | Crie ou atualize uma definição de função personalizada com permissões especificadas e escopos atribuíveis. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Ler as informações de registro de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Grava uma solicitação para regenerar as chaves de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/certificates/delete | Exclui um ativo de certificado de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/certificates/getCount/action | Lê a contagem de certificados |
> | Ação | Microsoft.Automation/automationAccounts/certificates/read | Obter um ativo de certificado de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/certificates/write | Criar ou atualizar um ativo de certificado de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/read | Lê uma compilação de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/read | Lê uma compilação de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/write | Grava a compilação de um DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/compilationjobs/write | Grava a compilação de um DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/content/read | Lê o conteúdo da mídia de configuração |
> | Ação | Microsoft.Automation/automationAccounts/configurations/delete | Exclui o conteúdo de um DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/getCount/action | Lê a contagem de conteúdo de um DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/read | Obtém o conteúdo de um DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/configurations/write | Grava o conteúdo de um DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connections/delete | Exclui um ativo de conexão de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connections/getCount/action | Lê a contagem de conexões |
> | Ação | Microsoft.Automation/automationAccounts/connections/read | Obter um ativo de conexão de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connections/write | Criar ou atualizar um ativo de conexão de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connectionTypes/delete | Exclui um ativo de tipo de conexão de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connectionTypes/read | Obtém um ativo de tipo de conexão de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/connectionTypes/write | Cria um ativo de tipo de conexão de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/credentials/delete | Exclui um ativo de credencial de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/credentials/getCount/action | Lê a contagem de credenciais |
> | Ação | Microsoft.Automation/automationAccounts/credentials/read | Obter um ativo de credencial de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/credentials/write | Criar ou atualizar um ativo de credencial de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/delete | Excluir uma conta de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Exclui Hybrid Runbook Worker recursos |
> | Ação | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lê Hybrid Runbook Worker recursos |
> | Ação | Microsoft.Automation/automationAccounts/jobs/output/read | Obtém a saída de um trabalho |
> | Ação | Microsoft.Automation/automationAccounts/jobs/read | Obter um trabalho de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/resume/action | Retoma um trabalho de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Obtém o conteúdo do runbook de automação do Azure no momento da execução do trabalho |
> | Ação | Microsoft.Automation/automationAccounts/jobs/stop/action | Para um trabalho de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende um trabalho de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobs/write | Cria um trabalho de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobSchedules/delete | Excluir uma agenda de trabalho de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobSchedules/read | Obter um plano de trabalho de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/jobSchedules/write | Cria um plano de trabalho de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtém o espaço de trabalho vinculado à conta de automação |
> | Ação | Microsoft.Automation/automationAccounts/listKeys/action | Lê as chaves para a conta de automação |
> | Ação | Microsoft.Automation/automationAccounts/modules/activities/read | Obter atividades de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/modules/delete | Exclui um módulo do PowerShell de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/modules/getCount/action | Obtém a contagem de módulos do PowerShell dentro da conta de automação |
> | Ação | Microsoft.Automation/automationAccounts/modules/read | Obter um módulo do PowerShell de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/modules/write | Criar ou atualizar um módulo do PowerShell de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Exclui a configuração de nó de um DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Lê um conteúdo de configuração de nó do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Lê uma configuração de nó do DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Grava a configuração de nó de um DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodecounts/read | Lê o resumo de contagem de nós para o tipo especificado |
> | Ação | Microsoft.Automation/automationAccounts/nodes/delete | Exclui os nós de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/read | Lê os nós de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Lê o conteúdo do relatório de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/reports/read | Lê relatórios de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/nodes/write | Criar ou atualizar nós de DSC de Automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Obter TypeFields de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python2Packages/delete | Exclui um pacote Python 2 de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python2Packages/read | Obter um pacote do Python 2 de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python2Packages/write | Criar ou atualizar um pacote Python 2 de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python3Packages/delete | Exclui um pacote do Python 3 de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python3Packages/read | Obter um pacote do Python 3 de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/python3Packages/write | Criar ou atualizar um pacote do Python 3 de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/read | Obter uma conta de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/content/read | Obter o conteúdo de um runbook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/delete | Exclui um runbook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Cria o conteúdo de um rascunho do runbook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Obter resultados da operação de rascunho de runbook da automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/read | Obter um rascunho do runbook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Obter um trabalho de teste de rascunho de runbook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Retoma um trabalho de teste de rascunho de runbook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Interrompe um trabalho de teste de rascunho de runbook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Suspende um trabalho de teste de rascunho do runbook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Cria um trabalho de teste de rascunho de runbook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Desfazer edições em um rascunho do runbook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Obtém a contagem de runbooks de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publica um rascunho do runbook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/read | Obter um runbook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/runbooks/write | Criar ou atualizar um runbook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/delete | Exclui um ativo de agendamento de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/getCount/action | Obtém a contagem de agendamentos de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/read | Obter um ativo de agendamento de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/schedules/write | Criar ou atualizar um ativo de agendamento de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/Read | Obter uma execução da máquina de configuração de atualização de software de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/Read | Obter uma execução de configuração de atualização de software de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Exclui uma configuração de atualização de software de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Exclui uma configuração de atualização de software de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Obter uma configuração de atualização de software de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Obter uma configuração de atualização de software de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Criar ou atualizar a configuração de atualização de software de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Criar ou atualizar a configuração de atualização de software de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/statistics/read | Obter estatísticas de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Obter uma máquina de implantação de atualização de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Obter um trabalho de patch de gerenciamento de atualização da automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/usages/read | Obtém o uso da automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/variables/delete | Exclui um ativo variável de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/variables/read | Lê um ativo variável de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/variables/write | Criar ou atualizar um ativo de variável de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/delete | Excluir um trabalho do observador de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/read | Obter um trabalho do observador de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/start/action | Iniciar um trabalho do observador de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/stop/action | Parar um trabalho do observador de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/streams/read | Obter um fluxo de trabalho do observador de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Excluir ações de trabalho do observador de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Obter uma ação de trabalho do observador de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Criar ações de trabalho do observador de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/watchers/write | Cria um trabalho do observador de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/action | Gera um URI para um webhook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/delete | Exclui um webhook de automação do Azure  |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/read | Lê um webhook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/webhooks/write | Criar ou atualizar um webhook de automação do Azure |
> | Ação | Microsoft.Automation/automationAccounts/write | Criar ou atualizar uma conta de automação do Azure |
> | Ação | Microsoft.Automation/operations/read | Obter operações disponíveis para recursos de automação do Azure |
> | Ação | Microsoft.Automation/register/action | Registra a assinatura na automação do Azure |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Excluir recurso de diretório do B2C |
> | Ação | Microsoft.AzureActiveDirectory/b2cDirectories/read | Exibir recurso de diretório do B2C |
> | Ação | Microsoft.AzureActiveDirectory/b2cDirectories/write | Criar ou atualizar o recurso de diretório do B2C |
> | Ação | Microsoft.AzureActiveDirectory/b2ctenants/read | Lista todos os locatários B2C nos quais o usuário é membro |
> | Ação | Microsoft.AzureActiveDirectory/operations/read | Ler todas as operações de API disponíveis para o provedor de recursos Microsoft. AzureActiveDirectory |
> | Ação | Microsoft.AzureActiveDirectory/register/action | Registrar a assinatura para o provedor de recursos Microsoft. AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.AzureStack/Operations/read | Obtém as propriedades de uma operação do provedor de recursos |
> | Ação | Microsoft.AzureStack/register/action | Registra a assinatura com o provedor de recursos Microsoft. AzureStack |
> | Ação | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Exclui uma assinatura de cliente do Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/customerSubscriptions/read | Obter as propriedades de uma assinatura de cliente do Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/customerSubscriptions/write | Criar ou atualizar uma assinatura de cliente do Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/delete | Exclui um registro de Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/getActivationKey/action | Obtém a chave de ativação de Azure Stack mais recente |
> | Ação | Microsoft. AzureStack/registros/produtos/getproduto/ação | Recupera Azure Stack produto do Marketplace |
> | Ação | Microsoft. AzureStack/inscrições/produtos/GetProducts/Action | Recupera uma lista de produtos Azure Stack Marketplace |
> | Ação | Microsoft.AzureStack/registrations/products/listDetails/action | Recupera detalhes estendidos para um produto Azure Stack Marketplace |
> | Ação | Microsoft.AzureStack/registrations/products/read | Obter as propriedades de um produto Azure Stack Marketplace |
> | Ação | Microsoft. AzureStack/inscrições/produtos/uploadProductLog/ação | Registrar Azure Stack status de operação do produto Marketplace e carimbo de data/hora |
> | Ação | Microsoft.AzureStack/registrations/read | Obtém as propriedades de um registro de Azure Stack |
> | Ação | Microsoft.AzureStack/registrations/write | Criar ou atualizar um registro de Azure Stack |
> | Ação | Microsoft. AzureStack/verificationKeys/getCurrentKey/Action | Obtém a versão atual da chave pública de assinatura do Azure Stack |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Batch/batchAccounts/applications/delete | Exclui um aplicativo |
> | Ação | Microsoft.Batch/batchAccounts/applications/read | Listar aplicativos ou obter as propriedades de um aplicativo |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Ativa um pacote de aplicativos |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/delete | Exclui um pacote de aplicativos |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/read | Obtém as propriedades de um pacote de aplicativos |
> | Ação | Microsoft.Batch/batchAccounts/applications/versions/write | Cria um novo pacote de aplicativos ou atualiza um pacote de aplicativos existente |
> | Ação | Microsoft.Batch/batchAccounts/applications/write | Cria um novo aplicativo ou atualiza um aplicativo existente |
> | Ação | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Obtém os resultados de uma operação de certificado de execução longa em uma conta do lote |
> | Ação | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Cancela a exclusão com falha de um certificado em uma conta do lote |
> | Ação | Microsoft.Batch/batchAccounts/certificates/delete | Exclui um certificado de uma conta do lote |
> | Ação | Microsoft.Batch/batchAccounts/certificates/read | Listar certificados em uma conta do lote ou obter as propriedades de um certificado |
> | Ação | Microsoft.Batch/batchAccounts/certificates/write | Cria um novo certificado em uma conta do lote ou atualiza um certificado existente |
> | Ação | Microsoft.Batch/batchAccounts/delete | Exclui uma conta do lote |
> | DataAction | Microsoft. batch/batchAccounts/trabalhos/excluir | Exclui um trabalho de uma conta do lote |
> | DataAction | Microsoft. batch/batchAccounts/trabalhos/ler | Listar trabalhos em uma conta do lote ou obter as propriedades de um trabalho |
> | DataAction | Microsoft. batch/batchAccounts/Jobs/Write | Cria um novo trabalho em uma conta do lote ou atualiza um trabalho existente |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Delete | Excluir um plano de trabalho de uma conta do lote |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Read | Listar agendas de trabalho em uma conta do lote ou obter as propriedades de uma agenda de trabalho |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Write | Cria uma nova agenda de trabalho em uma conta do lote ou atualiza uma agenda de trabalho existente |
> | Ação | Microsoft.Batch/batchAccounts/listkeys/action | Lista as chaves de acesso para uma conta do lote |
> | Ação | Microsoft.Batch/batchAccounts/operationResults/read | Obtém os resultados de uma operação de conta de lote de longa execução |
> | Ação | Microsoft.Batch/batchAccounts/poolOperationResults/read | Obtém os resultados de uma operação de pool de longa execução em uma conta do lote |
> | Ação | Microsoft.Batch/batchAccounts/pools/delete | Exclui um pool de uma conta do lote |
> | Ação | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Desabilita o dimensionamento automático para um pool de contas do lote |
> | Ação | Microsoft.Batch/batchAccounts/pools/read | Lista os pools em uma conta do lote ou obtém as propriedades de um pool |
> | Ação | Microsoft.Batch/batchAccounts/pools/stopResize/action | Interrompe uma operação de redimensionamento em andamento em um pool de contas do lote |
> | Ação | Microsoft.Batch/batchAccounts/pools/write | Cria um novo pool em uma conta do lote ou atualiza um pool existente |
> | Ação | Microsoft.Batch/batchAccounts/read | Listar contas do lote ou obter as propriedades de uma conta do lote |
> | Ação | Microsoft.Batch/batchAccounts/regeneratekeys/action | Regenera as chaves de acesso para uma conta do lote |
> | Ação | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Sincroniza chaves de acesso para a conta de armazenamento automático configurada para uma conta do lote |
> | Ação | Microsoft.Batch/batchAccounts/write | Cria uma nova conta do lote ou atualiza uma conta do lote existente |
> | Ação | Microsoft.Batch/locations/accountOperationResults/read | Obtém os resultados de uma operação de conta de lote de longa execução |
> | Ação | Microsoft.Batch/locations/checkNameAvailability/action | Verifica se o nome da conta é válido e não está em uso. |
> | Ação | Microsoft.Batch/locations/quotas/read | Obtém as cotas de lote da assinatura especificada na região do Azure especificada |
> | Ação | Microsoft.Batch/operations/read | Lista as operações disponíveis no provedor de recursos Microsoft. batch |
> | Ação | Microsoft.Batch/register/action | Registra a assinatura para o provedor de recursos do lote e habilita a criação de contas do lote |
> | Ação | Microsoft.Batch/unregister/action | Cancela o registro da assinatura para o provedor de recursos do lote que impede a criação de contas do lote |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. billing/billingAccounts/Agreements/Read |  |
> | Ação | Microsoft. billing/billingAccounts/billingPermissions/Read |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/billingPermissions/Read |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/Customers/Read |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/faturas/pricesheet/download/ação |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/billingPermissions/Read |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/move/Action |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/transferência/ação |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/Action |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/Products/move/Action |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/Products/transferência/ação |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/Products/validateMoveEligibility/Action |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/Read |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/invoiceSections/Write |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/pricesheet/download/ação |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/Read |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/Write |  |
> | Ação | Microsoft. billing/billingAccounts/billingProfiles/Write |  |
> | Ação | Microsoft. billing/billingAccounts/billingSubscriptions/Read |  |
> | Ação | Microsoft. billing/billingAccounts/Customers/billingPermissions/Read |  |
> | Ação | Microsoft. billing/billingAccounts/Customers/Read |  |
> | Ação | Microsoft.Billing/billingAccounts/departments/read |  |
> | Ação | Microsoft. billing/billingAccounts/enrollmentAccounts/billingPermissions/Read |  |
> | Ação | Microsoft.Billing/billingAccounts/enrollmentAccounts/read |  |
> | Ação | Microsoft. billing/billingAccounts/enrollmentDepartments/billingPermissions/Read |  |
> | Ação | Microsoft. billing/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/Action |  |
> | Ação | Microsoft. billing/billingAccounts/Products/Read |  |
> | Ação | Microsoft.Billing/billingAccounts/read |  |
> | Ação | Microsoft. billing/billingAccounts/Write |  |
> | Ação | Microsoft.Billing/departments/read |  |
> | Ação | Microsoft. cobrança/faturas/download/ação | Baixar a fatura usando o link de download da lista |
> | Ação | Microsoft.Billing/invoices/read |  |
> | Ação | Microsoft.Billing/register/action |  |
> | Ação | Microsoft. billing/validateAddress/Action |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.BingMaps/mapApis/Delete | Operação de exclusão |
> | Ação | Microsoft.BingMaps/mapApis/listSecrets/action | Listar os segredos |
> | Ação | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Ler o token de autorização de logon único para o recurso |
> | Ação | Microsoft.BingMaps/mapApis/Read | Operação de leitura |
> | Ação | Microsoft.BingMaps/mapApis/regenerateKey/action | Regenera a chave |
> | Ação | Microsoft.BingMaps/mapApis/Write | Operação de gravação |
> | Ação | Microsoft.BingMaps/Operations/read | Descrição da operação. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Blockchain/blockchainMembers/delete | Exclui um membro Blockchain existente. |
> | Ação | Microsoft.Blockchain/blockchainMembers/listApiKeys/action | Obtém ou lista as chaves de API de membro Blockchain existentes. |
> | Ação | Microsoft.Blockchain/blockchainMembers/read | Obtém ou lista os membros Blockchain existentes. |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Conecta-se a um nó de transação de membro Blockchain. |
> | Ação | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Exclui um nó de transação de membro Blockchain existente. |
> | Ação | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | Obtém ou lista as chaves de API do nó de transação de membro Blockchain existente. |
> | Ação | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Obtém ou lista os nós de transação de membro Blockchain existentes. |
> | Ação | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Cria ou atualiza um nó de transação de membro Blockchain. |
> | Ação | Microsoft.Blockchain/blockchainMembers/write | Cria ou atualiza um membro Blockchain. |
> | Ação | Microsoft. Blockchain/cordaMembers/Delete | Exclui um membro Blockchain corda existente. |
> | Ação | Microsoft. Blockchain/cordaMembers/Read | Obtém ou lista os membros Blockchain corda existentes. |
> | Ação | Microsoft. Blockchain/cordaMembers/Write | Cria ou atualiza um membro Blockchain corda. |
> | Ação | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Obtém os resultados da operação de membros do Blockchain. |
> | Ação | Microsoft.Blockchain/locations/checkNameAvailability/action | Verifica se o nome do recurso é válido e não está em uso. |
> | Ação | Microsoft.Blockchain/operations/read | Listar todas as operações no provedor de recursos do Microsoft Blockchain. |
> | Ação | Microsoft. Blockchain/registro/ação | Registra a assinatura para o provedor de recursos Blockchain. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | Ler todos os artefatos do Blueprint |
> | Ação | Microsoft.Blueprint/blueprintAssignments/delete | Eliminar todos os artefactos de esquema |
> | Ação | Microsoft.Blueprint/blueprintAssignments/read | Ler todos os artefatos do Blueprint |
> | Ação | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | Obtenha a ID de objeto da entidade de serviço de plantas do Azure. |
> | Ação | Microsoft.Blueprint/blueprintAssignments/write | Criar ou atualizar artefactos de esquema |
> | Ação | Microsoft.Blueprint/blueprints/artifacts/delete | Eliminar todos os artefactos de esquema |
> | Ação | Microsoft.Blueprint/blueprints/artifacts/read | Ler todos os artefatos do Blueprint |
> | Ação | Microsoft.Blueprint/blueprints/artifacts/write | Criar ou atualizar artefactos de esquema |
> | Ação | Microsoft.Blueprint/blueprints/delete | Excluir qualquer Blueprint |
> | Ação | Microsoft.Blueprint/blueprints/read | Ler planos gráficos |
> | Ação | Microsoft.Blueprint/blueprints/versions/artifacts/read | Ler todos os artefatos do Blueprint |
> | Ação | Microsoft.Blueprint/blueprints/versions/delete | Excluir qualquer Blueprint |
> | Ação | Microsoft.Blueprint/blueprints/versions/read | Ler planos gráficos |
> | Ação | Microsoft.Blueprint/blueprints/versions/write | Criar ou atualizar planos gráficos |
> | Ação | Microsoft.Blueprint/blueprints/write | Criar ou atualizar planos gráficos |
> | Ação | Microsoft.Blueprint/register/action | Registra o provedor de recursos de plantas do Azure |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.BotService/botServices/channels/delete | Excluir um serviço de bot |
> | Ação | Microsoft.BotService/botServices/channels/read | Ler um serviço de bot |
> | Ação | Microsoft.BotService/botServices/channels/write | Escrever um serviço de bot |
> | Ação | Microsoft.BotService/botServices/connections/delete | Excluir um serviço de bot |
> | Ação | Microsoft.BotService/botServices/connections/read | Ler um serviço de bot |
> | Ação | Microsoft.BotService/botServices/connections/write | Escrever um serviço de bot |
> | Ação | Microsoft.BotService/botServices/delete | Excluir um serviço de bot |
> | Ação | Microsoft.BotService/botServices/read | Ler um serviço de bot |
> | Ação | Microsoft.BotService/botServices/write | Escrever um serviço de bot |
> | Ação | Microsoft.BotService/locations/operationresults/read | Ler o status de uma operação assíncrona |
> | Ação | Microsoft.BotService/Operations/read | Ler as operações para todos os tipos de recursos |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Cache/checknameavailability/action | Verifica se um nome está disponível para uso com um novo cache Redis |
> | Ação | Microsoft.Cache/locations/operationresults/read | Obtém o resultado de uma operação de execução longa para a qual o cabeçalho ' local ' foi retornado anteriormente para o cliente |
> | Ação | Microsoft.Cache/operations/read | Lista as operações às quais o provedor ' Microsoft. cache ' oferece suporte. |
> | Ação | Microsoft.Cache/redis/delete | Excluir todo o cache Redis |
> | Ação | Microsoft.Cache/redis/export/action | Exportar dados do Redis para BLOBs de armazenamento prefixados no formato especificado |
> | Ação | Microsoft.Cache/redis/firewallRules/delete | Excluir regras de firewall de IP de um cache Redis |
> | Ação | Microsoft.Cache/redis/firewallRules/read | Obter as regras de firewall de IP de um cache Redis |
> | Ação | Microsoft.Cache/redis/firewallRules/write | Editar as regras de firewall de IP de um cache Redis |
> | Ação | Microsoft.Cache/redis/forceReboot/action | Forçar a reinicialização de uma instância de cache, potencialmente com perda de dados. |
> | Ação | Microsoft.Cache/redis/import/action | Importar dados de um formato especificado de vários BLOBs para o Redis |
> | Ação | Microsoft.Cache/redis/linkedservers/delete | Excluir o servidor vinculado de um cache Redis |
> | Ação | Microsoft.Cache/redis/linkedservers/read | Obter servidores vinculados associados a um cache Redis. |
> | Ação | Microsoft.Cache/redis/linkedservers/write | Adicionar um servidor vinculado a um cache Redis |
> | Ação | Microsoft.Cache/redis/listKeys/action | Exibir o valor das chaves de acesso do cache Redis no portal de gerenciamento |
> | Ação | Microsoft.Cache/redis/listUpgradeNotifications/read | Liste as notificações de atualização mais recentes para o locatário de cache. |
> | Ação | Microsoft.Cache/redis/metricDefinitions/read | Obter as métricas disponíveis para um cache Redis |
> | Ação | Microsoft.Cache/redis/patchSchedules/delete | Excluir o agendamento de patch de um cache Redis |
> | Ação | Microsoft.Cache/redis/patchSchedules/read | Obter a agenda de aplicação de patch de um cache Redis |
> | Ação | Microsoft.Cache/redis/patchSchedules/write | Modificar a agenda de aplicação de patch de um cache Redis |
> | Ação | Microsoft.Cache/redis/read | Exibir as configurações e a configuração do cache Redis no portal de gerenciamento |
> | Ação | Microsoft.Cache/redis/regenerateKey/action | Alterar o valor das chaves de acesso do cache Redis no portal de gerenciamento |
> | Ação | Microsoft.Cache/redis/start/action | Inicie uma instância de cache. |
> | Ação | Microsoft.Cache/redis/stop/action | Interrompa uma instância de cache. |
> | Ação | Microsoft.Cache/redis/write | Modificar as configurações e a configuração do cache Redis no portal de gerenciamento |
> | Ação | Microsoft.Cache/register/action | Registra o provedor de recursos ' Microsoft. cache ' com uma assinatura |
> | Ação | Microsoft.Cache/unregister/action | Cancela o registro do provedor de recursos ' Microsoft. cache ' com uma assinatura |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Capacity/appliedreservations/read | Ler todas as reservas |
> | Ação | Microsoft.Capacity/calculateexchange/action | Computa a quantidade e o preço do Exchange de novos erros de compra e de política de retorno. |
> | Ação | Microsoft.Capacity/calculateprice/action | Calcular qualquer preço de reserva |
> | Ação | Microsoft.Capacity/catalogs/read | Ler catálogo de reserva |
> | Ação | Microsoft.Capacity/checkoffers/action | Verificar as ofertas de assinatura |
> | Ação | Microsoft.Capacity/checkscopes/action | Verificar qualquer assinatura |
> | Ação | Microsoft.Capacity/commercialreservationorders/read | Obter ordens de reserva criadas em qualquer locatário |
> | Ação | Microsoft.Capacity/exchange/action | Trocar qualquer reserva |
> | Ação | Microsoft.Capacity/operations/read | Ler qualquer operação |
> | Ação | Microsoft.Capacity/register/action | Registra o provedor de recursos de capacidade e habilita a criação de recursos de capacidade. |
> | Ação | Microsoft.Capacity/reservationorders/action | Atualizar qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/availablescopes/action | Localizar qualquer escopo disponível |
> | Ação | Microsoft.Capacity/reservationorders/calculaterefund/action | Computa a quantidade de restituição e o preço de novos erros de compra e de política de retorno. |
> | Ação | Microsoft.Capacity/reservationorders/delete | Excluir qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/merge/action | Mesclar qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/read | Ler todas as reservas |
> | Ação | Microsoft.Capacity/reservationorders/reservations/action | Atualizar qualquer reserva |
> | Ação | Microsoft. Capacity/reservationorders/reservas/availablescopes/Action | Localizar qualquer escopo disponível |
> | Ação | Microsoft.Capacity/reservationorders/reservations/delete | Excluir qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/reservations/read | Ler todas as reservas |
> | Ação | Microsoft.Capacity/reservationorders/reservations/revisions/read | Ler todas as reservas |
> | Ação | Microsoft.Capacity/reservationorders/reservations/write | Criar qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/return/action | Retornar qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/split/action | Dividir qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/swap/action | Alternar qualquer reserva |
> | Ação | Microsoft.Capacity/reservationorders/write | Criar qualquer reserva |
> | Ação | Microsoft.Capacity/tenants/register/action | Registrar qualquer locatário |
> | Ação | Microsoft.Capacity/unregister/action | Cancelar o registro de qualquer locatário |
> | Ação | Microsoft.Capacity/validatereservationorder/action | Validar qualquer reserva |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/Delete |  |
> | Ação | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/Read |  |
> | Ação | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/Write |  |
> | Ação | Microsoft. CDN/cdnwebapplicationfirewallpolicies/Delete |  |
> | Ação | Microsoft. CDN/cdnwebapplicationfirewallpolicies/Read |  |
> | Ação | Microsoft. CDN/cdnwebapplicationfirewallpolicies/Write |  |
> | Ação | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Ação | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Ação | Microsoft.Cdn/edgenodes/delete |  |
> | Ação | Microsoft.Cdn/edgenodes/read |  |
> | Ação | Microsoft.Cdn/edgenodes/write |  |
> | Ação | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/Delete |  |
> | Ação | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/Read |  |
> | Ação | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/Write |  |
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
> | Ação | Microsoft.Cdn/register/action | Registra a assinatura para o provedor de recursos da CDN e habilita a criação de perfis CDN. |
> | Ação | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Excluir um certificado existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Obter a lista de certificados |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Adicionar um novo certificado ou atualizar um existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/Delete | Excluir um AppServiceCertificate existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/Read | Obter a lista de CertificateOrders |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Reemitir um certificateorder existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Renovar um certificateorder existente |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Reenviar o email do certificado |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Reenviar emails de solicitação para outro endereço de email |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Recuperar o selo do site para um Certificado do Serviço de Aplicativo emitido |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Recuperar a lista de ações de certificado |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Recuperar histórico de email de certificado |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Verificar a propriedade do domínio |
> | Ação | Microsoft.CertificateRegistration/certificateOrders/Write | Adicionar um novo certificateOrder ou atualizar um existente |
> | Ação | Microsoft.CertificateRegistration/operations/Read | Listar todas as operações do registro de certificado do serviço de aplicativo |
> | Ação | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Provisionar entidade de serviço para entidade de segurança do aplicativo de serviço |
> | Ação | Microsoft.CertificateRegistration/register/action | Registrar o provedor de recursos de certificados da Microsoft para a assinatura |
> | Ação | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Validar objeto de compra de certificado sem enviá-lo |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ClassicCompute/capabilities/read | Mostra os recursos |
> | Ação | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Verifica a disponibilidade de um determinado nome de domínio. |
> | Ação | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Obtém a disponibilidade de um determinado nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/active/write | Define o nome de domínio ativo. |
> | Ação | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Mostre o conjunto de disponibilidade para o recurso. |
> | Ação | Microsoft.ClassicCompute/domainNames/capabilities/read | Mostra os recursos de nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/delete | Remova os nomes de domínio dos recursos. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Mostra os slots de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Obter função no slot de implantação do nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Obter a instância de função para a função no slot de implantação do nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Obtenha o estado do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Adicione o estado do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Obter o domínio de atualização para o slot de implantação no nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Atualizar o domínio de atualização para o slot de implantação no nome de domínio |
> | Ação | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Cria ou atualiza a implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/delete | Remova as extensões de nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Lê o status da operação para as extensões de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/read | Retorna as extensões de nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/extensions/write | Adicione as extensões de nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Remova um novo balanceamento de carga interno. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Lê o status da operação dos balanceadores de carga internos de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Obtém os balanceadores de carga internos. |
> | Ação | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Cria um novo balanceamento de carga interno. |
> | Ação | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Lê o status da operação dos conjuntos de pontos de extremidade com balanceamento de carga dos nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Obter os conjuntos de pontos de extremidade com balanceamento de carga. |
> | Ação | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Adicione o conjunto de pontos de extremidade com balanceamento de carga. |
> | Ação | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Obter o status da operação do nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Lê o status da operação para as extensões de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/read | Retornar os nomes de domínio para os recursos. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Exclua os certificados de serviço usados. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Lê o status da operação para os certificados de serviço de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Retorna os certificados de serviço usados. |
> | Ação | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Adicione ou modifique os certificados de serviço usados. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Anula a migração de um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Confirma a migração de um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/delete | Exclui um determinado slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Lê o status da operação para os slots de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Prepara a migração de um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/read | Mostra os slots de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Remova a referência de extensão para a função de slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Lê o status da operação para as referências de extensão de funções de Slots de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Retorna a referência de extensão para a função do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Adicione ou modifique a referência de extensão para a função do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Obtenha a definição de métrica de função para o nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Obtenha a métrica de função para o nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Obtenha o status da operação para a função de slot de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Obtém as definições de métricas. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/read | Obtenha a função do slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Baixa o arquivo de conexão de área de trabalho remota para a instância de função na função de slot de nome de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Obtém o status da operação da instância de função na função de slot de nomes de domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Obter a instância de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Recria a instância de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Recria a imagem da instância de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Reinicia as instâncias de função. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Obtenha a SKU da função para o slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/roles/write | Adicione a função para o slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/start/action | Inicia um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Altera o estado do slot de implantação para parado. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Altera o estado do slot de implantação para iniciado. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/stop/action | Suspende o slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Passo atualizar o domínio. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Valida a migração de um slot de implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/slots/write | Cria ou atualiza a implantação. |
> | Ação | Microsoft.ClassicCompute/domainNames/swap/action | Permuta o slot de preparo para o slot de produção. |
> | Ação | Microsoft.ClassicCompute/domainNames/write | Adicione ou modifique os nomes de domínio dos recursos. |
> | Ação | Microsoft.ClassicCompute/moveSubscriptionResources/action | Mova todos os recursos clássicos para uma assinatura diferente. |
> | Ação | Microsoft.ClassicCompute/operatingSystemFamilies/read | Lista as famílias de sistema operacional convidado disponíveis em Microsoft Azure e também lista as versões de sistema operacional disponíveis para cada família. |
> | Ação | Microsoft.ClassicCompute/operatingSystems/read | Lista as versões do sistema operacional convidado que estão disponíveis no momento no Microsoft Azure. |
> | Ação | Microsoft.ClassicCompute/operations/read | Obtém a lista de operações. |
> | Ação | Microsoft.ClassicCompute/operationStatuses/read | Lê o status da operação para o recurso. |
> | Ação | Microsoft.ClassicCompute/quotas/read | Obtenha a cota da assinatura. |
> | Ação | Microsoft.ClassicCompute/register/action | Registrar-se na computação clássica |
> | Ação | Microsoft.ClassicCompute/resourceTypes/skus/read | Obtém a lista de SKUs para os tipos de recursos com suporte. |
> | Ação | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Valide a disponibilidade da assinatura para a operação de movimentação clássica. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Exclui o grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Lê o status da operação para os grupos de segurança de rede associados às máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Obtém o grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Obtém as operações assíncronas possíveis |
> | Ação | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Anexa um disco de dados a uma máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/capture/action | Capturar uma máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/delete | Remove máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Desanexa um disco de dados da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Obter configurações de diagnóstico de máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/disks/read | Recupera a lista de discos de dados |
> | Ação | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Baixa o arquivo RDP para a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Lê o status da operação para as extensões de máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/extensions/read | Obtém a extensão da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/extensions/write | Coloca a extensão da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Obtenha a definição de métrica da máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/metrics/read | Obtém as métricas. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Exclui o grupo de segurança de rede associado à interface de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Lê o status da operação para os grupos de segurança de rede associados às máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Obtém o grupo de segurança de rede associado à interface de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à interface de rede. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Lê o status da operação para as máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Executa a manutenção na máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Obtém as definições de métricas. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/read | Recupera a lista de máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Reimplanta a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/restart/action | Reinicia as máquinas virtuais. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Desligue a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/start/action | Inicie a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/stop/action | Interrompe a máquina virtual. |
> | Ação | Microsoft.ClassicCompute/virtualMachines/write | Adicionar ou modificar máquinas virtuais. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Obter um status de operação de conexão cruzada de rota expressa. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Excluir emparelhamento de conexão cruzada de rota expressa. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Obtenha um status de operação de emparelhamento de conexão cruzada de rota expressa. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Obter emparelhamento de conexão cruzada de rota expressa. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Adicionar emparelhamento de conexão cruzada de rota expressa. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Obter conexões cruzadas de rota expressa. |
> | Ação | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Adicione conexões cruzadas de rota expressa. |
> | Ação | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Recupera a lista de dispositivos com suporte. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Exclui o grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Lê o status da operação para o grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Obter as configurações de diagnóstico de grupos de segurança de rede |
> | Ação | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza as configurações de diagnóstico de grupos de segurança de rede; essa operação é complementada pelo provedor de recursos de informações. |
> | Ação | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Obter os eventos para o grupo de segurança de rede |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/read | Obtém o grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Exclui a regra de segurança. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Lê o status da operação para as regras de segurança do grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Obtém a regra de segurança. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Adiciona ou atualiza uma regra de segurança. |
> | Ação | Microsoft.ClassicNetwork/networkSecurityGroups/write | Adiciona um novo grupo de segurança de rede. |
> | Ação | Microsoft.ClassicNetwork/operations/read | Obter operações de rede clássicas. |
> | Ação | Microsoft.ClassicNetwork/quotas/read | Obtenha a cota da assinatura. |
> | Ação | Microsoft.ClassicNetwork/register/action | Registrar na rede clássica |
> | Ação | Microsoft.ClassicNetwork/reservedIps/delete | Excluir um IP reservado. |
> | Ação | Microsoft.ClassicNetwork/reservedIps/join/action | Ingressar em um IP reservado |
> | Ação | Microsoft.ClassicNetwork/reservedIps/link/action | Vincular um IP reservado |
> | Ação | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Lê o status da operação para os IPs reservados. |
> | Ação | Microsoft.ClassicNetwork/reservedIps/read | Obter os IPs reservados |
> | Ação | Microsoft.ClassicNetwork/reservedIps/write | Adicionar um novo IP reservado |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Anula a migração de uma rede virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Mostra os recursos |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Verifica a disponibilidade de um determinado endereço IP em uma rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Confirma a migração de uma rede virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/delete | Exclui a rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Cancela a revogação de um certificado de cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Ler os certificados de cliente revogados. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Revoga um certificado de cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Exclui o certificado de cliente de gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Baixa o certificado por impressão digital. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Lista o pacote de certificado de gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Localize os certificados raiz do cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Carrega um novo certificado raiz do cliente. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Conecta uma conexão de gateway site a site. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Desconecta uma conexão de gateway site a site. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Recupera a lista de conexões. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Testa uma conexão de gateway site a site. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Exclui o gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Baixa o script de configuração do dispositivo. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Baixa o diagnóstico do gateway. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Recupera a chave de serviço do circuito. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Lista o pacote de gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Lê o status da operação para os gateways de redes virtuais. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Obtém o pacote de gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Obtém os gateways de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Inicia o diagnóstico para o gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Interrompe o diagnóstico do gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Adiciona um gateway de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/join/action | Une a rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Lê o status da operação para as redes virtuais. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Os pares de uma rede virtual com outra rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Prepara a migração de uma rede virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/read | Obter a rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Exclui o proxy de emparelhamento de rede virtual remota. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Obtém o proxy de emparelhamento de rede virtual remota. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Adiciona ou atualiza o proxy de emparelhamento de rede virtual remota. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Exclui o grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Lê o status da operação do grupo de segurança de rede associado à sub-rede da rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Obtém o grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Adiciona um grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Valida a migração de uma rede virtual |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Obtém o emparelhamento de rede virtual. |
> | Ação | Microsoft.ClassicNetwork/virtualNetworks/write | Adicione uma nova rede virtual. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ClassicStorage/capabilities/read | Mostra os recursos |
> | Ação | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Verifica a disponibilidade de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Obtenha a disponibilidade de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/disks/read | Retorna o disco da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/images/operationstatuses/read | Obtém o status da operação de imagem. |
> | Ação | Microsoft.ClassicStorage/images/read | Retorna a imagem. |
> | Ação | Microsoft.ClassicStorage/operations/read | Obtém operações de armazenamento clássico |
> | Ação | Microsoft.ClassicStorage/osImages/read | Retorna a imagem do sistema operacional. |
> | Ação | Microsoft.ClassicStorage/osPlatformImages/read | Obtém a imagem da plataforma do sistema operacional. |
> | Ação | Microsoft.ClassicStorage/publicImages/read | Obtém a imagem da máquina virtual pública. |
> | Ação | Microsoft.ClassicStorage/quotas/read | Obtenha a cota da assinatura. |
> | Ação | Microsoft.ClassicStorage/register/action | Registrar no armazenamento clássico |
> | Ação | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Anula a migração de uma conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/blobservices/Providers/Microsoft. insights/diagnosticSettings/Read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/blobservices/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/blobservices/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as definições de métricas. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Confirma a migração de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/delete | Exclua a conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/delete | Exclui um determinado disco da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Lê o status da operação para o recurso. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/read | Retorna o disco da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/disks/write | Adiciona um disco de conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/fileservices/Providers/Microsoft. insights/diagnosticSettings/Read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/fileservices/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/fileservices/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as definições de métricas. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/images/delete | Exclui uma determinada imagem de conta de armazenamento. Preterido. Use ' Microsoft. ClassicStorage/storageAccounts/vmImages ') |
> | Ação | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Retorna o status da operação da imagem da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/images/read | Retorna a imagem da conta de armazenamento. Preterido. Use ' Microsoft. ClassicStorage/storageAccounts/vmImages ') |
> | Ação | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista as chaves de acesso para as contas de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Lê o status da operação para o recurso. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Exclui uma determinada imagem do sistema operacional da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/osImages/read | Retorna a imagem do sistema operacional da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/osImages/write | Adiciona uma determinada imagem do sistema operacional da conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Prepara a migração de uma conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. insights/diagnosticSettings/Read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as definições de métricas. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/queueservices/Providers/Microsoft. insights/diagnosticSettings/Read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/queueservices/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/queueservices/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as definições de métricas. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/read | Retorne a conta de armazenamento com a conta especificada. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Regenera as chaves de acesso existentes para a conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Obtém as definições de métricas. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Obtém as métricas. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/services/read | Obtenha os serviços disponíveis. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/tableservices/Providers/Microsoft. insights/diagnosticSettings/Read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/tableservices/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/tableservices/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as definições de métricas. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Valida a migração de uma conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Exclui uma determinada imagem de máquina virtual. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Obtém um determinado status de operação de imagem de máquina virtual. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Retorna a imagem da máquina virtual. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Adiciona uma imagem de máquina virtual específica. |
> | Ação | Microsoft.ClassicStorage/storageAccounts/write | Adiciona uma nova conta de armazenamento. |
> | Ação | Microsoft.ClassicStorage/vmImages/read | Lista imagens de máquinas virtuais. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | DataAction | Microsoft. Cognitivaservices/accounts/AnomalyDetector/timeseries/inteiro/detectar/ação | Esta operação gera um modelo usando uma série inteira, cada ponto é detectado com o mesmo modelo.<br>Com esse método, os pontos antes e depois de um determinado ponto são usados para determinar se é uma anomalia.<br>A detecção inteira pode dar ao usuário um status geral da série temporal. |
> | DataAction | Microsoft. Cognitivaservices/accounts/AnomalyDetector/timeseries/Last/Detect/Action | Esta operação gera um modelo usando pontos antes do mais recente. Com esse método, somente pontos históricos são usados para determinar se o ponto de destino é uma anomalia. O ponto mais recente detectando corresponde ao cenário de monitoramento em tempo real de métricas de negócios. |
> | DataAction | Microsoft. Cognitivaservices/contas/autosugestão/pesquisa/ação | Esta operação fornece sugestões para uma determinada consulta ou consulta parcial. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/analyze/action | Esta operação extrai um conjunto avançado de recursos visuais com base no conteúdo da imagem.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | Esta operação retorna uma caixa delimitadora em volta da área mais importante da imagem. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | Esta operação gera uma descrição de uma imagem no idioma legível por humanos com sentenças completas.<br> A descrição se baseia em uma coleção de marcas de conteúdo, que também são retornadas pela operação.<br>Mais de uma descrição pode ser gerada para cada imagem.<br> As descrições são ordenadas por sua pontuação de confiança.<br>Todas as descrições estão em inglês. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | Esta operação executa a detecção de objetos na imagem especificada.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | Esta operação gera uma imagem em miniatura com a largura e a altura especificadas pelo usuário.<br> Por predefinição, o serviço analisa a imagem, identifica a região de interesse (ROI) e gera coordenadas de recorte inteligentes com base no ROI.<br> O corte inteligente ajuda quando você especifica uma taxa de proporção que difere da imagem de entrada |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Essa operação reconhece o conteúdo em uma imagem aplicando um modelo específico de domínio.<br> A lista de modelos específicos de domínio com suporte pelo API da Pesquisa Visual Computacional pode ser recuperada usando a solicitação GET/Models.<br> Atualmente, a API fornece os seguintes modelos específicos de domínio: celebridades, pontos de referência. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | Essa operação retorna a lista de modelos específicos de domínio com suporte pelo API da Pesquisa Visual Computacional.  Atualmente, a API dá suporte aos seguintes modelos específicos de domínio: celebridade Recognizer, Recognizer de pontos de referência. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | O OCR (reconhecimento óptico de caracteres) detecta o texto em uma imagem e extrai os caracteres reconhecidos em um fluxo de caracteres utilizável por máquina.    |
> | DataAction | Microsoft. Cognitivaservices/accounts/ComputerVision/Read/Analyze/Action | Use essa interface para executar uma operação de leitura, empregando os algoritmos de OCR (reconhecimento óptico de caracteres) de última geração otimizados para documentos com texto pesado.<br>Ele pode lidar com documentos escritos, impressos ou mistos.<br>Quando você usa a interface de leitura, a resposta contém um cabeçalho chamado ' Operation-location '.<br>O cabeçalho ' Operation-location ' contém a URL que você deve usar para a operação obter resultado da leitura para acessar os resultados do OCR. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ComputerVision/Read/analyzeresults/Read | Use essa interface para recuperar o status e o resultado do OCR de uma operação de leitura.  A URL que contém o ' operationId ' é retornada no cabeçalho de resposta ' Operation-location ' da operação de leitura. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ComputerVision/Read/Core/asyncbatchanalyze/Action | Use essa interface para obter o resultado de uma operação de arquivo de leitura em lotes, empregando o caractere óptico de última geração |
> | DataAction | Microsoft. Cognitivaservices/accounts/ComputerVision/Read/Operations/Read | Essa interface é usada para obter os resultados de OCR da operação de leitura. A URL para essa interface deve ser recuperada do campo <b>"Operation-Location"</b> retornado da interface do arquivo de leitura em lote. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | Use essa interface para obter o resultado de uma operação de Reconhecimento de Texto. Quando você usa a interface Reconhecimento de Texto, a resposta contém um campo chamado "Operation-Location". O campo "operação-local" contém a URL que você deve usar para a operação obter Reconhecimento de Texto resultado da operação. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | Esta operação gera uma lista de palavras ou marcas que são relevantes para o conteúdo da imagem fornecida.<br>O API da Pesquisa Visual Computacional pode retornar marcas com base em objetos, seres de vida, cenário ou ações encontradas em imagens.<br>Diferentemente das categorias, as marcas não são organizadas de acordo com um sistema de classificação hierárquica, mas correspondem ao conteúdo da imagem.<br>As marcas podem conter dicas para evitar ambigüidade ou fornecer contexto, por exemplo, a marca "Cello" pode ser acompanhada pela dica "instrumento musical".<br>Todas as marcas estão em inglês. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | Essa interface é usada para obter o resultado da operação de reconhecimento de texto. A URL para essa interface deve ser recuperada do campo <b>"Operation-Location"</b> retornado da interface reconhecimento de texto. |
> | DataAction | Microsoft. Cognitivaservices/contas/ContentModerator/imagelists/ação | Criar lista de imagens. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/ImageList/Delete | Listas de imagens-excluir |
> | DataAction | Microsoft. Cognitivaservices/contas/ContentModerator/imagelists/imagens/excluir | Exclua uma imagem da sua lista de imagens. A lista de imagens pode ser usada para fazer correspondência difusa em relação a outras imagens ao usar a API de imagem/correspondência. Exclua todas as imagens da lista. A lista de imagens pode ser usada para fazer correspondência difusa em relação a outras imagens ao usar a API de imagem/correspondência. * |
> | DataAction | Microsoft. Cognitivaservices/contas/ContentModerator/imagelists/imagens/leitura | Imagem-obter todas as IDs de imagem |
> | DataAction | Microsoft. Cognitivaservices/contas/ContentModerator/imagelists/imagens/gravação | Adicione uma imagem à sua lista de imagens. A lista de imagens pode ser usada para fazer correspondência difusa em relação a outras imagens ao usar a API de imagem/correspondência. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/ImageList/Read | Listas de imagens-obter detalhes-listas de imagens-obter tudo |
> | DataAction | Microsoft. Cognitivaservices/contas/ContentModerator/imagelists/refreshindex/ação | Listas de imagens – atualizar índice de pesquisa |
> | DataAction | Microsoft. Cognitivaservices/contas/ContentModerator/imagelists/gravação | Listas de imagens-detalhes da atualização |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/ProcessImage/Evaluate/Action | Retorna as probabilidades da imagem que contém o conteúdo erótico ou adulto. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/ProcessImage/findfaces/Action | Encontre rostos em imagens. |
> | DataAction | Microsoft. Cognitivaservices/contas/ContentModerator/ProcessImage/correspondência/ação | Fuzzily corresponder a uma imagem em uma de suas listas de imagens personalizadas.<br>Você pode criar e gerenciar suas listas de imagens personalizadas usando essa API.<br> |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/ProcessImage/OCR/Action | Retorna qualquer texto encontrado na imagem para o idioma especificado. Se nenhum idioma for especificado na entrada, a detecção padrão será o inglês. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/processtext/DetectLanguage/Action | Esta operação detectará o idioma do conteúdo de entrada fornecido.<br>Retorna o código ISO 639-3 para o idioma predominante que compõe o texto enviado.<br>Mais de 110 idiomas com suporte. |
> | DataAction | Microsoft. Cognitivaservices/contas/ContentModerator/processtext/tela/ação | The operation detects profanity in more than 100 languages and match against custom and shared blacklists. |
> | DataAction | Microsoft. Cognitivaservices/contas/ContentModerator/equipes/trabalhos/ação | Uma ID de trabalho será retornada para o conteúdo da imagem Postado nesse ponto de extremidade.  |
> | DataAction | Microsoft. Cognitivaservices/contas/ContentModerator/equipes/trabalhos/leitura | Obtenha os detalhes do trabalho para uma ID de trabalho. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/Teams/Reviews/AccessKey/Read | Obtenha a chave de acesso de conteúdo de revisão para sua equipe. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/Teams/Reviews/Action | As revisões criadas seriam mostradas para os revisores da sua equipe. À medida que os revisores concluírem a revisão, os resultados da análise serão POSTADOs (ou seja, HTTP POST) no CallBackEndpoint especificado. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/Times/Reviews/frames/Read | *Não definido* |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/Times/Reviews/frames/Write | Use este método para adicionar quadros para uma revisão de vídeo. |
> | DataAction | Microsoft. Cognitivaservices/contas/ContentModerator/equipes/revisões/publicação/ação | As revisões de vídeo são inicialmente criadas em um estado não publicado, o que significa que ela não está disponível para revisores em sua equipe para revisar ainda. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/Times/Reviews/Read | Retorna os detalhes da revisão da ID de revisão aprovada. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/Teams/Reviews/transcrição/Action | Essa API adiciona um arquivo de transcrição (versão de texto de todas as palavras faladas em um vídeo) a uma revisão de vídeo. O arquivo deve ser um formato WebVTT válido. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/Teams/Reviews/transcriptmoderationresult/Action | Essa API adiciona um arquivo de resultado de texto da tela de transcrição para uma revisão de vídeo. O arquivo de resultado de texto da tela de transcrição é resultado da API de texto da tela. Para gerar o arquivo de resultado de texto da tela de transcrição, um arquivo de transcrição precisa ser filtrado em caso de profanação usando a API de texto de tela. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/Teams/Settings/templates/Delete | Excluir um modelo em sua equipe |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/Teams/Settings/templates/Read | Retorna uma matriz de modelos de revisão provisionados nesta equipe. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/Teams/Settings/templates/Write | Cria ou atualiza o modelo especificado |
> | DataAction | Microsoft. Cognitivaservices/contas/ContentModerator/equipes/fluxos de trabalho/leitura | Obtenha os detalhes de um fluxo de trabalho específico em sua equipe. Obtenha todos os fluxos de trabalho disponíveis para sua equipe * |
> | DataAction | Microsoft. Cognitivaservices/contas/ContentModerator/equipes/fluxos de trabalho/gravação | Crie um novo fluxo de trabalho ou atualize um existente. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/termlists/Action | Criar lista de termos. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/termlists/bulkupdate/Action | Listas de termos – atualização em massa |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/termlists/Delete | Listas de termos – excluir |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/termlists/Read | Listas de termos – obter listas de todos os termos-obter detalhes |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/termlists/refreshindex/Action | Listas de termos – índice de pesquisa de atualização |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/termlists/terms/Delete | Termo-excluir-termo-excluir todos os termos |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/termlists/terms/Read | Termo-obter todos os termos |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/termlists/terms/Write | Termo-adicionar termo |
> | DataAction | Microsoft. Cognitivaservices/accounts/ContentModerator/termlists/Write | Listas de termos – detalhes da atualização |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/classificação/iterações/imagem/ação | Classifique uma imagem e salve o resultado. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/classificação/iterações/imagem/NoStore/ação | Classifique uma imagem sem salvar o resultado. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/classificação/iterações/URL/ação | Classifique uma URL de imagem e salve o resultado. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/classificação/iterações/URL/NoStore/ação | Classifique uma URL de imagem sem salvar o resultado. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/detecção/iterações/imagem/ação | Detectar objetos em uma imagem e salvar o resultado. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/detecção/iterações/imagem/NoStore/ação | Detectar objetos em uma imagem sem salvar o resultado. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/detecção/iterações/URL/ação | Detectar objetos em uma URL de imagem e salvar o resultado. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/detecção/iterações/URL/NoStore/ação | Detectar objetos em uma URL de imagem sem salvar o resultado. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/domínios/leitura | Obter informações sobre um domínio específico. Obtenha uma lista dos domínios disponíveis. * |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/labelproposals/configuração/ação | Defina o tamanho do pool da proposta de rótulo. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/labelproposals/configuração/leitura | Obtenha o tamanho do pool da proposta de rótulo para este projeto. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projeto/migração/ação | *Não definido* |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/ação | Crie um projeto. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/excluir | Excluir um projeto específico. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/ação | Essa API aceita o conteúdo do corpo como multipart/Forms-Data e application/octet-stream. Ao usar várias partes |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/excluir | Exclua imagens do conjunto de imagens de treinamento. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/arquivos/ação | Essa API aceita um lote de arquivos e, opcionalmente, marcas para criar imagens. Há um limite de 64 imagens e 20 etiquetas. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/ID/leitura | Essa API retornará um conjunto de imagens para as marcas especificadas e, opcionalmente, a iteração. Se nenhuma iteração for especificada, o |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/previsões/ação | Essa API cria um lote de imagens de imagens previstas especificadas. Há um limite de 64 imagens e 20 etiquetas. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/regionproposals/ação | Essa API obterá propostas de região para uma imagem juntamente com confianças para a região. Ele retornará uma matriz vazia se nenhuma proposta for encontrada. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/regiões/ação | Essa API aceita um lote de regiões de imagem e, opcionalmente, marcas para atualizar imagens existentes com informações de região. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/regiões/excluir | Excluir um conjunto de regiões de imagem. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/sugestão/ação | Esta API buscará imagens não marcadas filtradas por IDs de marcas sugeridas. Ele retornará uma matriz vazia se nenhuma imagem for encontrada. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/sugestão/contagem/ação | Essa API leva em tagIds para obter a contagem de imagens não marcadas por marcas sugeridas para um determinado limite. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/marcados/contagem/leitura | A filtragem está em uma relação e/ou. Por exemplo, se as IDs de marca fornecidas forem para o "cachorro" e |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/marcados/lidos | Esta API dá suporte à seleção de intervalo e lote. Por padrão, ele retornará apenas as primeiras 50 imagens que correspondem a imagens. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/marcas/ação | Associe um conjunto de imagens a um conjunto de marcas. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/marcas/excluir | Remova um conjunto de marcas de um conjunto de imagens. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/não marcado/contagem/leitura | Essa API retorna as imagens que não têm nenhuma marca para um determinado projeto e, opcionalmente, uma iteração. Se nenhuma iteração for especificada, o |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/não marcado/lido | Esta API dá suporte à seleção de intervalo e lote. Por padrão, ele retornará apenas as primeiras 50 imagens que correspondem a imagens. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/imagens/URLs/ação | Essa API aceita um lote de URLs e, opcionalmente, marcas para criar imagens. Há um limite de 64 imagens e 20 etiquetas. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/iterações/exclusão | Excluir uma iteração específica de um projeto. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/iterações/exportação/ação | Exportar uma iteração treinada. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/iterações/exportação/leitura | Obter a lista de exportações para uma iteração específica. |
> | DataAction | Microsoft. Cognitivaservices/contas/CustomVision. previsão/projetos/iterações/desempenho/imagens/contagem/leitura | A filtragem está em uma relação e/ou. Por exemplo, se as IDs de marca fornecidas forem para o "cachorro" e |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/iterações/desempenho/imagens/leitura | Esta API dá suporte à seleção de intervalo e lote. Por padrão, ele retornará apenas as primeiras 50 imagens que correspondem a imagens. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/iterações/desempenho/leitura | Obtenha informações detalhadas de desempenho sobre uma iteração. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/iterações/publicação/ação | Publicar uma iteração específica. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/iterações/publicação/exclusão | Cancelar a publicação de uma iteração específica. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/iterações/leitura | Obtenha uma iteração específica. Obter iterações para o projeto. * |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/iterações/gravação | Atualizar uma iteração específica. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/previsões/exclusão | Exclua um conjunto de imagens previstas e seus resultados de previsão associados. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/previsões/consulta/ação | Obtenha imagens que foram enviadas ao ponto de extremidade de previsão. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/QuickTest/imagem/ação | Teste rápido uma imagem. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/QuickTest/URL/ação | Teste rápido de uma URL de imagem. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/ler | Obtenha um projeto específico. Obtenha seus projetos. * |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/marcas/ação | Crie uma marca para o projeto. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/marcas/excluir | Exclua uma marca do projeto. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/marcas/leitura | Obter informações sobre uma marca específica. Obter as marcas para um determinado projeto e iteração. * |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/marcas/gravação | Atualizar uma marca. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/tagsandregions/sugestões/ação | Essa API receberá marcas e regiões sugeridas para uma matriz/lote de imagens não marcadas, juntamente com confianças para as marcas. Ele retornará uma matriz vazia se nenhuma marca for encontrada. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/treinamento/ação | Projeto de filas para treinamento. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/projetos/gravação | Atualize um projeto específico. |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/cota/ação | *Não definido* |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/cota/exclusão | *Não definido* |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/cota/atualização/gravação | *Não definido* |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/uso/previsão/usuário/leitura | Obter o uso do recurso de previsão para o usuário Oxford |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/uso/treinamento/recurso/camada/leitura | Obter o uso do recurso de treinamento para o usuário do Azure |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/uso/treinamento/usuário/leitura | Obter o uso do recurso de treinamento para o usuário do Oxford |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/usuário/ação | *Não definido* |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/usuário/excluir | *Não definido* |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/usuário/estado/gravação | Atualizar estado do usuário |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/usuário/camada/gravação | *Não definido* |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/usuários/leitura | *Não definido* |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/lista de permissões/exclusão | Exclui um usuário com lista de permissões com funcionalidade específica |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/lista de permissões/leitura | Obter uma lista de usuários na lista de permissões com funcionalidade específica |
> | DataAction | Microsoft. Cognitivaservices/accounts/CustomVision. predição/lista de permissões/gravação | Atualiza ou cria um usuário na lista de permissões com funcionalidade específica |
> | Ação | Microsoft.CognitiveServices/accounts/delete | Exclui contas de API |
> | DataAction | Microsoft. Cognitivaservices/accounts/EntitySearch/Search/Action | Obter entidades e colocar os resultados para uma determinada consulta. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/detect/action | Detecte faces humanas em uma imagem, retorne retângulos de face e, opcionalmente, com faceIds, pontos de referência e atributos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | Excluir uma lista de face especificada. As imagens de face relacionadas na lista de face também serão excluídas. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Excluir uma face de uma lista de rosto por faceListId e persisitedFaceId especificados. A imagem de face relacionada também será excluída. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Adicione uma face a uma lista de face especificada, até 1.000 rostos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Recupere faceListId, Name, userData e faces da lista facial na lista de face. Liste as listas de face ' faceListId, Name e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Crie uma lista de face vazia com faceListId especificado pelo usuário, o nome e um userData opcional. Até 64 listas de rosto são permitidas informações de atualização de uma lista de face, incluindo nome e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | Tabela de face de consulta fornecida para pesquisar os rostos de aparência semelhante de uma matriz faceid, uma lista de face ou uma lista de rosto grande. faceId |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/group/action | Divida os rostos candidatas em grupos com base na semelhança de face. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/identify/action | identificação de um-para-muitos para localizar as correspondências mais próximas da pessoa de consulta específica que se encontram de um grupo de pessoas ou grupo de pessoas grandes. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Excluir uma lista de face grande especificada. As imagens de face relacionadas na lista de rosto grande também serão excluídas. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | Exclua uma face de uma lista de rosto grande por largeFaceListId e persisitedFaceId especificados. A imagem de face relacionada também será excluída. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Recuperar a face persistente em uma lista de face grande por largeFaceListId e persistedFaceId. Liste faces ' persistedFaceId e userData ' em uma lista de face grande especificada. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | Adicione uma face a uma lista de face grande especificada, até 1 milhão rostos. Atualizar o campo userData de uma face especificada em uma lista de rosto grande por seu persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Recupere largeFaceListId, Name, userData de uma lista de rosto grande. Listar as informações de grandes listas ' de largeFaceListId, Name e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | Envie uma tarefa de treinamento de lista de rosto grande. O treinamento é uma etapa crucial que só pode ser usada por uma lista de rosto grande treinada. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | Para verificar se o status de treinamento da lista de rosto grande foi concluído ou ainda está em andamento. O treinamento do LargeFaceList é uma operação assíncrona |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Crie uma lista de face grande vazia com largeFaceListId especificado pelo usuário, o nome e um userData opcional. Atualize as informações de uma lista de rosto grande, incluindo Name e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | Excluir um grupo de pessoas grandes existente com personGroupId especificado. Os dados persistentes neste grupo de pessoas grandes serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | Crie uma nova pessoa em um grupo de pessoas grandes especificado. Para adicionar a face a essa pessoa, entre em contato com |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | Excluir uma pessoa existente de um grupo de pessoas grandes. Todos os dados da pessoa armazenada e imagens de face na entrada Person serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Excluir uma face de uma pessoa em um grupo de pessoas grandes. Os dados de face e a imagem relacionados a essa entrada de rosto também serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Recupere informações de face da pessoa. A face da pessoa persistente é especificada por seu largePersonGroupId, PersonID e persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | Adicione uma imagem de face a uma pessoa em um grupo de pessoas grandes para identificação ou verificação facial. Para lidar com a imagem da atualização, o campo userData da face de uma pessoa persiste. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Recupere o nome e o userData de uma pessoa e o faceIds persistente que representa a imagem de face da pessoa registrada. Listar todas as informações de todas as pessoas no grupo de pessoas grandes especificado, incluindo PERSONID, Name, userData e persistedFaceIds. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | Atualizar nome ou userData de uma pessoa. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | Recupere as informações de um grupo de pessoas grandes, incluindo seu nome e userData. Essa API retorna a lista de informações de grupo de pessoas grandes todos os grupos de pessoas grandes existentes largePesonGroupId, Name e userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | Envie uma tarefa de treinamento de grupo de pessoas grandes. O treinamento é uma etapa crucial que só pode ser usada por um grupo treinado de grande pessoa. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | Para verificar se o status de treinamento de grupo de pessoa grande foi concluído ou ainda está em andamento. O treinamento do LargePersonGroup é uma operação assíncrona |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | Crie um novo grupo de pessoas grandes com largePersonGroupId especificado pelo usuário, nome e userData opcional. Atualizar nome e userData de um grupo de pessoas grandes existentes. As propriedades permanecerão inalteradas se não estiverem no corpo da solicitação. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | Excluir um grupo de pessoas existente com personGroupId especificado. Os dados persistentes neste grupo de pessoas serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | Criar uma nova pessoa em um grupo de pessoas especificado. Para adicionar a face a essa pessoa, entre em contato com |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | Excluir uma pessoa existente de um grupo de pessoas. Todos os dados da pessoa armazenada e imagens de face na entrada Person serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | Excluir uma face de uma pessoa em um grupo de pessoas. Os dados de face e a imagem relacionados a essa entrada de rosto também serão excluídos. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | Recupere informações de face da pessoa. A face da pessoa persistente é especificada por seu personGroupId, PersonID e persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | Adicione uma imagem de face a uma pessoa em um grupo de pessoas para identificação ou verificação facial. Para lidar com a imagem de várias atualizações, o campo userData da face de uma pessoa persiste. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | Recupere o nome e o userData de uma pessoa e o faceIds persistente que representa a imagem de face da pessoa registrada. Listar as informações de todas as pessoas no grupo de pessoas especificado, incluindo PERSONID, Name, userData e persistedFaceIds de Registered. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | Atualizar nome ou userData de uma pessoa. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/read | Recupere o nome e o userData do grupo de pessoas. Para obter informações de pessoas sob esse grupo de pessoas, use listar pesonGroupId, nome e userData de grupos de pessoas. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | Envie uma tarefa de treinamento de grupo Person. O treinamento é uma etapa crucial que apenas um grupo de pessoas treinado pode usar. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | Para verificar se o status de treinamento do grupo de pessoas foi concluído ou ainda está em andamento. O treinamento do Person é uma operação assíncrona disparada |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/write | Crie um novo grupo de pessoas com personGroupId especificado, nome e userData fornecido pelo usuário. Atualize o nome e o userData de um grupo de pessoas existente. As propriedades permanecerão inalteradas se não estiverem no corpo da solicitação. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/verify/action | Verifique se duas faces pertencem a uma mesma pessoa ou se uma face pertence a uma pessoa. |
> | DataAction | Microsoft. Cognitivaservices/accounts/FormRecognizer/Custom/Models/Analyze/Action | Extrair pares de chave-valor de um determinado documento. O documento de entrada deve ser de um dos tipos de conteúdo com suporte-' Application/PDF ', ' image/jpeg ' ou ' image/png '. Uma resposta de êxito é retornada em JSON. |
> | DataAction | Microsoft. Cognitivaservices/accounts/FormRecognizer/Custom/Models/Delete | Excluir artefatos de modelo. |
> | DataAction | Microsoft. Cognitivaservices/accounts/FormRecognizer/Custom/Models/Keys/Read | Recupere as chaves do modelo. |
> | DataAction | Microsoft. Cognitivaservices/accounts/FormRecognizer/Custom/Models/Read | Obter informações sobre um modelo. Obter informações sobre todos os modelos personalizados treinados * |
> | DataAction | Microsoft. Cognitivaservices/accounts/FormRecognizer/Custom/Train/Action | Crie e treine um modelo personalizado.<br>A solicitação de treinamento deve incluir um parâmetro de origem que seja um URI de contêiner de blob de armazenamento do Azure acessível externamente (preferivelmente um URI de assinatura de acesso compartilhado) ou um caminho válido para uma pasta de dados em uma unidade montada localmente.<br>Quando caminhos locais são especificados, eles devem seguir o formato de caminho Linux/Unix e ser um caminho absoluto com raiz para a configuração de montagem de entrada |
> | DataAction | Microsoft. Cognitivaservices/accounts/FormRecognizer/precompilado/confirmation/asyncbatchanalyze/Action | Extrair o texto do campo e os valores semânticos de um determinado documento de recebimento. O documento de imagem de entrada deve ser um dos tipos de conteúdo com suporte – JPEG, PNG, BMP, PDF ou TIFF. Uma resposta de êxito é um JSON que contém um campo chamado ' Operation-location ', que contém a URL para a operação get recibo Result para recuperar os resultados de forma assíncrona. |
> | DataAction | Microsoft. Cognitivaservices/accounts/FormRecognizer/precompilado/recibo/operações/ação | Consulte o status e recupere o resultado de uma operação de confirmação de análise. A URL para essa interface pode ser obtida do cabeçalho ' Operation-location ' na resposta de confirmação de recebimento. |
> | DataAction | Microsoft. Cognitivaservices/contas/ImageSearch/detalhes/ação | Retorna informações sobre uma imagem, como páginas da Web que incluem a imagem. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ImageSearch/Search/Action | Obter imagens relevantes para uma determinada consulta. |
> | DataAction | Microsoft. Cognitivaservices/contas/ImageSearch/tendência/ação | Obtenha imagens em tendências no momento. |
> | DataAction | Microsoft. Cognitivaservices/accounts/ImmersiveReader/getcontentmodelforreader/Action | Cria uma sessão de leitor de imersão |
> | DataAction | Microsoft. Cognitivaservices/accounts/InkRecognizer/Recognize/Action | Dado um conjunto de dados de traço analisa o conteúdo e gera uma lista de entidades reconhecidas, incluindo texto reconhecido. |
> | Ação | Microsoft.CognitiveServices/accounts/listKeys/action | Listar chaves |
> | DataAction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Obtém a previsão de ponto de extremidade publicado para a consulta fornecida. |
> | DataAction | Microsoft. Cognitivaservices/accounts/NewsSearch/categorysearch/Action | Retorna notícias para uma categoria fornecida. |
> | DataAction | Microsoft. Cognitivaservices/accounts/NewsSearch/Search/Action | Obtenha artigos de notícias relevantes para uma determinada consulta. |
> | DataAction | Microsoft. Cognitivaservices/accounts/NewsSearch/trendingtopics/Action | Obtenha tópicos de tendência identificados pelo Bing. Esses são os mesmos tópicos mostrados na faixa na parte inferior do home page do Bing. |
> | Ação | Microsoft.CognitiveServices/accounts/read | Lê as contas de API. |
> | Ação | Microsoft.CognitiveServices/accounts/regenerateKey/action | Regenerar chave |
> | Ação | Microsoft.CognitiveServices/accounts/skus/read | Lê os SKUs disponíveis para um recurso existente. |
> | DataAction | Microsoft. Cognitivaservices/contas/verificação ortográfica/verificação ortográfica/ação | Obtenha o resultado de uma consulta de verificação ortográfica por meio de GET ou POST. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | A API retorna uma lista de entidades conhecidas e entidades nomeadas gerais (\"Person\", \"local\", \"Organization\", etc) em um determinado documento. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | A API devolve uma lista de cadeias que denotam os principais pontos de conversa no texto introduzido. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | A API devolve o idioma detetado e uma pontuação numérica entre 0 e 1. As pontuações próximas de 1 indicam uma certeza de 100% de que o idioma detetado é verdadeiro. É suportado um total de 120 idiomas. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | A API devolve uma pontuação numérica entre 0 e 1.<br>As pontuações próximas de 1 indicam um sentimento positivo, enquanto as pontuações próximas de 0 indicam um sentimento negativo.<br>Uma pontuação de 0,5 indica a falta de sentimentos (por exemplo,<br>uma instrução factoname). |
> | Ação | Microsoft.CognitiveServices/accounts/usages/read | Obtenha o uso de cota para um recurso existente. |
> | DataAction | Microsoft. Cognitivaservices/contas/VideoSearch/detalhes/ação | Obtenha informações sobre um vídeo, como vídeos relacionados. |
> | DataAction | Microsoft. Cognitivaservices/accounts/VideoSearch/Search/Action | Obter vídeos relevantes para uma determinada consulta. |
> | DataAction | Microsoft. Cognitivaservices/contas/VideoSearch/tendência/ação | Obtenha vídeos de tendência no momento. |
> | DataAction | Microsoft. Cognitivaservices/accounts/VisualSearch/Search/Action | Retorna uma lista de marcas relevantes para a imagem fornecida |
> | DataAction | Microsoft. Cognitivaservices/contas/WebSearch/pesquisa/ação | Obtenha resultados da Web, de imagem, de notícias & vídeos para uma determinada consulta. |
> | Ação | Microsoft.CognitiveServices/accounts/write | Grava contas de API. |
> | Ação | Microsoft.CognitiveServices/checkDomainAvailability/action | Lê os SKUs disponíveis para uma assinatura. |
> | Ação | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Lê os SKUs disponíveis para uma assinatura. |
> | Ação | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Lê os SKUs disponíveis para uma assinatura. |
> | Ação | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Notificação da Microsoft. Network de exclusão de VirtualNetworks ou sub-redes. |
> | Ação | Microsoft. Cognitivaservices/Locations/operationresults/Read | Ler o status de uma operação assíncrona. |
> | Ação | Microsoft.CognitiveServices/Operations/read | Listar todas as operações disponíveis |
> | Ação | Microsoft.CognitiveServices/register/action | Registra a assinatura para serviços cognitivas |
> | Ação | Microsoft.CognitiveServices/register/action | Registra a assinatura para serviços cognitivas |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Commerce/RateCard/read | Retorna dados de oferta, metadados de recurso/medidor e taxas para a assinatura fornecida. |
> | Ação | Microsoft. Commerce/registro/ação | Registrar a assinatura para o Microsoft Commerce UsageAggregate |
> | Ação | Microsoft. Commerce/cancelar registro/ação | Cancelar o registro da assinatura do Microsoft Commerce UsageAggregate |
> | Ação | Microsoft.Commerce/UsageAggregates/read | Recupera o consumo de Microsoft Azure por uma assinatura. O resultado contém agregações de dados de uso, assinatura e informações relacionadas a recursos, em um intervalo de tempo específico. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Compute/availabilitySets/delete | Exclui o conjunto de disponibilidade |
> | Ação | Microsoft.Compute/availabilitySets/read | Obter as propriedades de um conjunto de disponibilidade |
> | Ação | Microsoft.Compute/availabilitySets/vmSizes/read | Listar os tamanhos disponíveis para criar ou atualizar uma máquina virtual no conjunto de disponibilidade |
> | Ação | Microsoft.Compute/availabilitySets/write | Cria um novo conjunto de disponibilidade ou atualiza um existente |
> | Ação | Microsoft. Compute/diskEncryptionSets/Delete | Excluir um conjunto de criptografia de disco |
> | Ação | Microsoft. Compute/diskEncryptionSets/Read | Obter as propriedades de um conjunto de criptografia de disco |
> | Ação | Microsoft. Compute/diskEncryptionSets/Write | Criar um novo conjunto de criptografia de disco ou atualizar um existente |
> | Ação | Microsoft.Compute/disks/beginGetAccess/action | Obter o URI de SAS do disco para acesso ao blob |
> | Ação | Microsoft.Compute/disks/delete | Exclui o disco |
> | Ação | Microsoft.Compute/disks/endGetAccess/action | Revogar o URI de SAS do disco |
> | Ação | Microsoft.Compute/disks/read | Obter as propriedades de um disco |
> | Ação | Microsoft.Compute/disks/write | Cria um novo disco ou atualiza um existente |
> | Ação | Microsoft. Compute/galerias/aplicativos/excluir | Excluir o aplicativo da Galeria |
> | Ação | Microsoft. Compute/galerias/aplicativos/ler | Obtém as propriedades do aplicativo da Galeria |
> | Ação | Microsoft. Compute/galerias/aplicativos/versões/excluir | Exclui a versão do aplicativo da Galeria |
> | Ação | Microsoft. Compute/galerias/aplicativos/versões/leitura | Obtém as propriedades da versão do aplicativo da Galeria |
> | Ação | Microsoft. Compute/galerias/aplicativos/versões/gravação | Cria uma nova versão de aplicativo da galeria ou atualiza uma existente |
> | Ação | Microsoft. Compute/galerias/aplicativos/gravação | Cria um novo aplicativo de galeria ou atualiza um existente |
> | Ação | Microsoft.Compute/galleries/delete | Exclui a Galeria |
> | Ação | Microsoft.Compute/galleries/images/delete | Exclui a imagem da Galeria |
> | Ação | Microsoft.Compute/galleries/images/read | Obtém as propriedades da imagem da Galeria |
> | Ação | Microsoft.Compute/galleries/images/versions/delete | Exclui a versão da imagem da Galeria |
> | Ação | Microsoft.Compute/galleries/images/versions/read | Obtém as propriedades da versão da imagem da Galeria |
> | Ação | Microsoft.Compute/galleries/images/versions/write | Cria uma nova versão da imagem da galeria ou atualiza uma existente |
> | Ação | Microsoft.Compute/galleries/images/write | Cria uma nova imagem da galeria ou atualiza uma existente |
> | Ação | Microsoft.Compute/galleries/read | Obter as propriedades da Galeria |
> | Ação | Microsoft.Compute/galleries/write | Cria uma nova galeria ou atualiza uma existente |
> | Ação | Microsoft.Compute/hostGroups/delete | Exclui o grupo de hosts |
> | Ação | Microsoft. Compute/hosts/hosts/Delete | Exclui o host |
> | Ação | Microsoft. Compute/hosts/hosts/Read | Obter as propriedades de um host |
> | Ação | Microsoft. Compute/hosts/hosts/Write | Cria um novo host ou atualiza um host existente |
> | Ação | Microsoft.Compute/hostGroups/read | Obter as propriedades de um grupo de hosts |
> | Ação | Microsoft.Compute/hostGroups/write | Cria um novo grupo de hosts ou atualiza um grupo de hosts existente |
> | Ação | Microsoft.Compute/images/delete | Exclui a imagem |
> | Ação | Microsoft.Compute/images/read | Obter as propriedades da imagem |
> | Ação | Microsoft.Compute/images/write | Cria uma nova imagem ou atualiza uma existente |
> | Ação | Microsoft.Compute/locations/capsOperations/read | Obtém o status de uma operação de Caps assíncronas |
> | Ação | Microsoft.Compute/locations/diskOperations/read | Obtém o status de uma operação de disco assíncrono |
> | Ação | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Crie logs para mostrar o total de solicitações por intervalo de tempo para auxiliar o diagnóstico de limitação. |
> | Ação | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Crie logs para mostrar agregações de solicitações limitadas agrupadas por resourceName, OperationName ou a política de limitação aplicada. |
> | Ação | Microsoft.Compute/locations/operations/read | Obtém o status de uma operação assíncrona |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Obter as propriedades de uma oferta de imagem de plataforma |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Obter as propriedades de uma SKU de imagem de plataforma |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Obter as propriedades de uma versão de imagem de plataforma |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Obter as propriedades de um tipo VMExtension |
> | Ação | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Obter as propriedades de uma versão do VMExtension |
> | Ação | Microsoft.Compute/locations/publishers/read | Obter as propriedades de um Publicador |
> | Ação | Microsoft.Compute/locations/runCommands/read | Lista os comandos de execução disponíveis no local |
> | Ação | Microsoft.Compute/locations/usages/read | Obtém os limites de serviço e as quantidades de uso atuais para os recursos de computação da assinatura em um local |
> | Ação | Microsoft.Compute/locations/vmSizes/read | Lista os tamanhos de máquina virtual disponíveis em um local |
> | Ação | Microsoft. Compute/Locations/vsmOperations/Read | Obtém o status de uma operação assíncrona para o conjunto de dimensionamento de máquinas virtuais com a extensão de serviço de tempo de execução de máquina virtual |
> | Ação | Microsoft.Compute/operations/read | Lista as operações disponíveis no provedor de recursos Microsoft. Compute |
> | Ação | Microsoft.Compute/proximityPlacementGroups/delete | Exclui o grupo de posicionamento de proximidade |
> | Ação | Microsoft.Compute/proximityPlacementGroups/read | Obter as propriedades de um grupo de posicionamento de proximidade |
> | Ação | Microsoft.Compute/proximityPlacementGroups/write | Cria um novo grupo de posicionamento de proximidade ou atualiza um existente |
> | Ação | Microsoft.Compute/register/action | Registra a assinatura com o provedor de recursos Microsoft. Compute |
> | Ação | Microsoft.Compute/restorePointCollections/delete | Exclui a coleção de pontos de restauração e os pontos de restauração contidos |
> | Ação | Microsoft.Compute/restorePointCollections/read | Obter as propriedades de uma coleção de pontos de restauração |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/delete | Exclui o ponto de restauração |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/read | Obter as propriedades de um ponto de restauração |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Obter as propriedades de um ponto de restauração juntamente com URIs de SAS de BLOB |
> | Ação | Microsoft.Compute/restorePointCollections/restorePoints/write | Cria um novo ponto de restauração |
> | Ação | Microsoft.Compute/restorePointCollections/write | Cria uma nova coleção de pontos de restauração ou atualiza uma existente |
> | Ação | Microsoft.Compute/sharedVMImages/delete | Exclui o SharedVMImage |
> | Ação | Microsoft.Compute/sharedVMImages/read | Obter as propriedades de um SharedVMImage |
> | Ação | Microsoft.Compute/sharedVMImages/versions/delete | Excluir um SharedVMImageVersion |
> | Ação | Microsoft.Compute/sharedVMImages/versions/read | Obter as propriedades de um SharedVMImageVersion |
> | Ação | Microsoft.Compute/sharedVMImages/versions/replicate/action | Replicar um SharedVMImageVersion para as regiões de destino |
> | Ação | Microsoft.Compute/sharedVMImages/versions/write | Criar um novo SharedVMImageVersion ou atualizar um existente |
> | Ação | Microsoft.Compute/sharedVMImages/write | Cria um novo SharedVMImage ou atualiza um existente |
> | Ação | Microsoft.Compute/skus/read | Obtém a lista de SKUs Microsoft. Compute disponíveis para sua assinatura |
> | Ação | Microsoft.Compute/snapshots/beginGetAccess/action | Obter o URI de SAS do instantâneo para acesso ao blob |
> | Ação | Microsoft.Compute/snapshots/delete | Excluir um instantâneo |
> | Ação | Microsoft.Compute/snapshots/endGetAccess/action | Revogar o URI de SAS do instantâneo |
> | Ação | Microsoft.Compute/snapshots/read | Obter as propriedades de um instantâneo |
> | Ação | Microsoft.Compute/snapshots/write | Criar um novo instantâneo ou atualizar um existente |
> | Ação | Microsoft.Compute/unregister/action | Cancela o registro da assinatura com o provedor de recursos Microsoft. Compute |
> | Ação | Microsoft.Compute/virtualMachines/capture/action | Captura a máquina virtual copiando discos rígidos virtuais e gera um modelo que pode ser usado para criar máquinas virtuais semelhantes |
> | Ação | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Converte os discos baseados em BLOB da máquina virtual em discos gerenciados |
> | Ação | Microsoft.Compute/virtualMachines/deallocate/action | Desliga a máquina virtual e libera os recursos de computação |
> | Ação | Microsoft.Compute/virtualMachines/delete | Exclui a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/extensions/delete | Exclui a extensão da máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/extensions/read | Obter as propriedades de uma extensão de máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/extensions/write | Cria uma nova extensão de máquina virtual ou atualiza uma existente |
> | Ação | Microsoft.Compute/virtualMachines/generalize/action | Define o estado da máquina virtual como generalizado e prepara a máquina virtual para captura |
> | Ação | Microsoft.Compute/virtualMachines/instanceView/read | Obtém o status de tempo de execução detalhado da máquina virtual e seus recursos |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Fazer logon em uma máquina virtual como um usuário normal |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Fazer logon em uma máquina virtual com privilégios de administrador do Windows ou de usuário raiz do Linux |
> | Ação | Microsoft.Compute/virtualMachines/performMaintenance/action | Executa a operação de manutenção na VM. |
> | Ação | Microsoft.Compute/virtualMachines/powerOff/action | Desliga a máquina virtual. Observe que a máquina virtual continuará a ser cobrada. |
> | Ação | Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/redeploy/action | Reimplanta a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/reimage/action | Recria a imagem da máquina virtual que está usando o disco diferencial. |
> | Ação | Microsoft.Compute/virtualMachines/restart/action | Reinicia a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/runCommand/action | Executa um script predefinido na máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/start/action | Inicia a máquina virtual |
> | Ação | Microsoft.Compute/virtualMachines/vmSizes/read | Lista os tamanhos disponíveis para os quais a máquina virtual pode ser atualizada |
> | Ação | Microsoft.Compute/virtualMachines/write | Cria uma nova máquina virtual ou atualiza uma máquina virtual existente |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Desliga e libera os recursos de computação para as instâncias do conjunto de dimensionamento de máquinas virtuais  |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/delete | Exclui o conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/delete/action | Exclui as instâncias do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Exclui a extensão do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Obter as propriedades de uma extensão de conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Cria uma nova extensão de conjunto de dimensionamento de máquinas virtuais ou atualiza uma existente |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Oriente manualmente os domínios de atualização de plataforma de um conjunto de dimensionamento de máquinas virtuais do Service Fabric para concluir uma atualização pendente que está paralisada |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Obtém a exibição de instância do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Atualiza manualmente as instâncias para o modelo mais recente do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Obter propriedades de todas as interfaces de rede de um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Inicia uma atualização sem interrupção para mover todas as instâncias do conjunto de dimensionamento de máquinas virtuais para a versão mais recente do sistema operacional da imagem de plataforma disponível. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Obter o histórico de atualizações do sistema operacional para um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Executa a manutenção planejada nas instâncias do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Desliga as instâncias do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Obter propriedades de todos os endereços IP públicos de um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/read | Obter as propriedades de um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Reimplantar as instâncias do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Refazer a imagem das instâncias do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Recria a imagem de todos os discos (disco do so e discos de dados) para as instâncias de um conjunto de dimensionamento de máquinas virtuais  |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/restart/action | Reinicia as instâncias do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Cancela a atualização sem interrupção de um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Obter o status de atualização sem interrupção mais recente para um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/scale/action | Verificar se um conjunto de dimensionamento de máquinas virtuais existente pode ser dimensionado/reScale Out para a contagem de instâncias especificada |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/skus/read | Lista os SKUs válidos para um conjunto de dimensionamento de máquinas virtuais existente |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/start/action | Inicia as instâncias do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Desliga e libera os recursos de computação para uma máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Exclua uma máquina virtual específica em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Recupera a exibição de instância de uma máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Obtenha as propriedades do endereço IP público criado usando o conjunto de dimensionamento de máquinas virtuais. O conjunto de dimensionamento de máquinas virtuais pode criar no máximo um IP público por ipconfiguration (IP privado) |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Obter propriedades de uma ou todas as configurações de IP de uma interface de rede criada usando o conjunto de dimensionamento de máquinas virtuais. As configurações de IP representam IPs privados |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Obter propriedades de uma ou de todas as interfaces de rede de uma máquina virtual criada usando o conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Executa a manutenção planejada em uma instância de máquina virtual em um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Desliga uma instância de máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Recupera as propriedades de uma máquina virtual em um conjunto de dimensionamento de VM |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Reimplanta uma instância de máquina virtual em um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Recria uma imagem de uma instância de máquina virtual em um conjunto de dimensionamento de máquinas virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Recria a imagem de todos os discos (disco do so e discos de dados) para a instância de máquina virtual em um conjunto de dimensionamento de máquinas virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Reinicia uma instância de máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Executa um script predefinido em uma instância de máquina virtual em um conjunto de dimensionamento de máquinas virtuais. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Inicia uma instância de máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Atualiza as propriedades de uma máquina virtual em um conjunto de dimensionamento de VM |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | Listar os tamanhos disponíveis para criar ou atualizar uma máquina virtual no conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft.Compute/virtualMachineScaleSets/write | Cria um novo conjunto de dimensionamento de máquinas virtuais ou atualiza um existente |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. consumo/aggregatedcost/leitura | Liste AggregatedCost para o grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/balances/read | Listar o resumo de utilização de um período de cobrança para um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/budgets/delete | Exclua os orçamentos por uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/budgets/read | Listar os orçamentos por uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/budgets/write | Cria e atualiza os orçamentos por uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/charges/read | Listar encargos |
> | Ação | Microsoft.Consumption/credits/read | Listar créditos |
> | Ação | Microsoft. consumo/eventos/leitura | Listar eventos |
> | Ação | Microsoft. consumo/externalBillingAccounts/marcas/leitura | Listar marcas para EA e assinaturas. |
> | Ação | Microsoft. consumo/externalSubscriptions/marcas/leitura | Listar marcas para EA e assinaturas. |
> | Ação | Microsoft.Consumption/forecasts/read | Listar previsões |
> | Ação | Microsoft.Consumption/lots/read | Listar muitos |
> | Ação | Microsoft.Consumption/marketplaces/read | Liste os detalhes de uso de recursos do Marketplace para um escopo para assinaturas EA e webdirect. |
> | Ação | Microsoft.Consumption/operationresults/read | Listar operationresults |
> | Ação | Microsoft.Consumption/operations/read | Listar todas as operações com suporte pelo provedor de recursos Microsoft. consumo. |
> | Ação | Microsoft.Consumption/operationstatus/read | Listar OperationStatus |
> | Ação | Microsoft.Consumption/pricesheets/read | Listar os dados de Pricesheets para uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/register/action | Registrar no RP de consumo |
> | Ação | Microsoft.Consumption/reservationDetails/read | Liste os detalhes de utilização das instâncias reservadas por ordem de reserva ou grupos de gerenciamento. Os dados de detalhes são por instância por nível de dia. |
> | Ação | Microsoft.Consumption/reservationRecommendations/read | Listar recomendações únicas ou compartilhadas para instâncias reservadas para uma assinatura. |
> | Ação | Microsoft.Consumption/reservationSummaries/read | Liste o resumo de utilização das instâncias reservadas por ordem de reserva ou grupos de gerenciamento. Os dados de resumo estão em um nível mensal ou diário. |
> | Ação | Microsoft.Consumption/reservationTransactions/read | Listar o histórico de transações para instâncias reservadas por grupos de gerenciamento. |
> | Ação | Microsoft.Consumption/tags/read | Listar marcas para EA e assinaturas. |
> | Ação | Microsoft.Consumption/tenants/read | Listar locatários |
> | Ação | Microsoft.Consumption/tenants/register/action | Registre a ação para o escopo do Microsoft. consumo por um locatário. |
> | Ação | Microsoft.Consumption/terms/read | Listar os termos de uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft.Consumption/usageDetails/read | Liste os detalhes de uso de um escopo para assinaturas EA e webdirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ContainerInstance/containerGroups/containers/buildlogs/Read | Obter logs de compilação para um contêiner específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Exec em um contêiner específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Obter logs para um contêiner específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/delete | Exclua o grupo de contêineres específico. |
> | Ação | Microsoft. ContainerInstance/containerGroups/operationResults/Read | Obter resultado da operação assíncrona |
> | Ação | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico para o grupo de contêineres. |
> | Ação | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o grupo de contêineres. |
> | Ação | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Obtém as métricas disponíveis para o grupo de contêineres. |
> | Ação | Microsoft.ContainerInstance/containerGroups/read | Obter todos os grupos de contêineres. |
> | Ação | Microsoft.ContainerInstance/containerGroups/restart/action | Reinicia um grupo de contêineres específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/start/action | Inicia um grupo de contêineres específico. |
> | Ação | Microsoft.ContainerInstance/containerGroups/stop/action | Para um grupo de contêineres específico. Os recursos de computação serão desalocados e a cobrança será interrompida. |
> | Ação | Microsoft.ContainerInstance/containerGroups/write | Criar ou atualizar um grupo de contêineres específico. |
> | Ação | Microsoft.ContainerInstance/locations/cachedImages/read | Obtém as imagens armazenadas em cache para a assinatura em uma região. |
> | Ação | Microsoft.ContainerInstance/locations/capabilities/read | Obtenha os recursos para uma região. |
> | Ação | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft. ContainerInstance que a rede virtual ou sub-rede está sendo excluída. |
> | Ação | Microsoft. ContainerInstance/localizações/operações/leitura | Listar as operações para o serviço de instância de contêiner do Azure. |
> | Ação | Microsoft.ContainerInstance/locations/usages/read | Obtenha o uso de uma região específica. |
> | Ação | Microsoft. ContainerInstance/operações/leitura | Listar as operações para o serviço de instância de contêiner do Azure. |
> | Ação | Microsoft.ContainerInstance/register/action | Registra a assinatura para o provedor de recursos da instância de contêiner e habilita a criação de grupos de contêineres. |
> | Ação | Microsoft. ContainerInstance/serviceassociationlinks/Delete | Exclua o link de associação de serviço criado pelo provedor de recursos da instância de contêiner do Azure em uma sub-rede. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ContainerRegistry/checkNameAvailability/read | Verifica se o nome do registro de contêiner está disponível para uso. |
> | Ação | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft. ContainerRegistry que a rede virtual ou sub-rede está sendo excluída |
> | Ação | Microsoft.ContainerRegistry/locations/operationResults/read | Obtém um resultado de operação assíncrona |
> | Ação | Microsoft.ContainerRegistry/operations/read | Lista todas as operações da API REST do registro de contêiner do Azure disponíveis |
> | Ação | Microsoft.ContainerRegistry/register/action | Registra a assinatura para o provedor de recursos de registro de contêiner e habilita a criação de registros de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/artifacts/delete | Exclua o artefato em um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/cancel/action | Cancela uma compilação existente. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Obtém um link para baixar os logs de compilação. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/read | Obtém as propriedades da compilação especificada ou lista todas as compilações para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/builds/write | Atualiza uma compilação para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/delete | Exclui uma tarefa de compilação de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Lista as propriedades de controle do código-fonte para uma tarefa de compilação. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/read | Obtém as propriedades da tarefa de compilação especificada ou lista todas as tarefas de compilação para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Exclui uma etapa de compilação de uma tarefa de compilação. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Lista os argumentos de compilação para uma etapa de compilação, incluindo os argumentos secretos. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Obtém as propriedades da etapa de compilação especificada ou lista todas as etapas de compilação para a tarefa de compilação especificada. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Cria ou atualiza uma etapa de compilação para uma tarefa de compilação com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/buildTasks/write | Cria ou atualiza uma tarefa de compilação para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/delete | Exclui um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Exclui um filtro de grade de eventos de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Obtém as propriedades do filtro de grade de eventos especificado ou lista todos os filtros de grade de eventos para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Cria ou atualiza um filtro de grade de eventos para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft. ContainerRegistry/registros/generateCredentials/Action | Gerar chaves para um token de um registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Obtém o local de carregamento para o usuário poder carregar a origem. |
> | Ação | Microsoft.ContainerRegistry/registries/importImage/action | Importe a imagem para o registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Obter local da URL de carregamento de origem para um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/listCredentials/action | Lista as credenciais de logon para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/listPolicies/read | Lista as políticas para o registro de contêiner especificado |
> | Ação | Microsoft.ContainerRegistry/registries/listUsages/read | Lista os usos de cota para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/metadata/read | Obtém os metadados de um repositório específico para um registro de contêiner |
> | Ação | Microsoft.ContainerRegistry/registries/metadata/write | Atualiza os metadados de um repositório para um registro de contêiner |
> | Ação | Microsoft.ContainerRegistry/registries/operationStatuses/read | Obter um status de operação assíncrona do registro |
> | Ação | Microsoft.ContainerRegistry/registries/pull/read | Efetuar pull ou obter imagens de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/push/write | Enviar por Push ou gravar imagens em um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/quarentena/leitura | Efetuar pull ou obter imagens em quarentena do registro de contêiner |
> | Ação | Microsoft. ContainerRegistry/registros/quarentena/gravação | Gravar/modificar o estado de quarentena de imagens em quarentena |
> | Ação | Microsoft.ContainerRegistry/registries/queueBuild/action | Cria uma nova compilação com base nos parâmetros de solicitação e adiciona-a à fila de compilação. |
> | Ação | Microsoft.ContainerRegistry/registries/read | Obtém as propriedades do registro de contêiner especificado ou lista todos os registros de contêiner no grupo de recursos ou na assinatura especificada. |
> | Ação | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Regenera uma das credenciais de logon para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/replications/delete | Exclui uma replicação de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Obter um status de operação assíncrona de replicação |
> | Ação | Microsoft.ContainerRegistry/registries/replications/read | Obtém as propriedades da replicação especificada ou lista todas as replicações para o registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/replications/write | Cria ou atualiza uma replicação para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/runs/cancel/action | Cancelar uma execução existente. |
> | Ação | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Obtém a URL de SAS do log para uma execução. |
> | Ação | Microsoft.ContainerRegistry/registries/runs/read | Obtém as propriedades de uma execução em relação a um registro de contêiner ou a uma lista de execuções. |
> | Ação | Microsoft.ContainerRegistry/registries/runs/write | Atualiza uma execução. |
> | Ação | Microsoft.ContainerRegistry/registries/scheduleRun/action | Agendar uma execução em um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/scopeMaps/Delete | Exclui um mapa de escopo de um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/scopeMaps/operationStatuses/leitura | Obtém um status de operação assíncrona do mapa de escopo. |
> | Ação | Microsoft. ContainerRegistry/registros/scopeMaps/ler | Obtém as propriedades do mapa de escopo especificado ou lista todos os mapas de escopo para o registro de contêiner especificado. |
> | Ação | Microsoft. ContainerRegistry/registros/scopeMaps/gravação | Cria ou atualiza um mapa de escopo para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/sign/write | Metadados de confiança de conteúdo de push/pull para um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/tasks/delete | Exclui uma tarefa para um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Lista todos os detalhes de uma tarefa para um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/tasks/read | Obtém uma tarefa para um registro de contêiner ou lista todas as tarefas. |
> | Ação | Microsoft.ContainerRegistry/registries/tasks/write | Cria ou atualiza uma tarefa para um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/tokens/excluir | Exclui um token de um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/tokens/operationStatuses/Read | Obtém um status de operação assíncrona de token. |
> | Ação | Microsoft. ContainerRegistry/registros/tokens/leitura | Obtém as propriedades do token especificado ou lista todos os tokens para o registro de contêiner especificado. |
> | Ação | Microsoft. ContainerRegistry/registros/tokens/gravação | Cria ou atualiza um token para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/updatePolicies/write | Atualiza as políticas para o registro de contêiner especificado |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/delete | Exclui um webhook de um registro de contêiner. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Obtém a configuração de URI de serviço e cabeçalhos personalizados para o webhook. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Lista os eventos recentes para o webhook especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Obter um status de operação assíncrona de webhook |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Dispara um evento de ping a ser enviado ao webhook. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/read | Obtém as propriedades do webhook especificado ou lista todos os WebHooks do registro de contêiner especificado. |
> | Ação | Microsoft.ContainerRegistry/registries/webhooks/write | Cria ou atualiza um webhook para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft.ContainerRegistry/registries/write | Cria ou atualiza um registro de contêiner com os parâmetros especificados. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ContainerService/containerServices/delete | Exclui um serviço de contêiner |
> | Ação | Microsoft.ContainerService/containerServices/read | Obter um serviço de contêiner |
> | Ação | Microsoft.ContainerService/containerServices/write | Cria um novo serviço de contêiner ou atualiza um existente |
> | Ação | Microsoft.ContainerService/locations/operationresults/read | Obtém o status de um resultado de operação assíncrona |
> | Ação | Microsoft.ContainerService/locations/operations/read | Obtém o status de uma operação assíncrona |
> | Ação | Microsoft.ContainerService/locations/orchestrators/read | Lista os orquestradores com suporte |
> | Ação | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Obter um perfil de acesso de cluster gerenciado por nome de função usando a credencial de lista |
> | Ação | Microsoft.ContainerService/managedClusters/accessProfiles/read | Obter um perfil de acesso de cluster gerenciado por nome de função |
> | Ação | Microsoft.ContainerService/managedClusters/agentPools/delete | Exclui um pool de agentes |
> | Ação | Microsoft.ContainerService/managedClusters/agentPools/read | Obter um pool de agentes |
> | Ação | Microsoft. ContainerService/managedClusters/agentPools/upgradeProfiles/Read | Obtém o perfil de atualização do pool de agentes |
> | Ação | Microsoft.ContainerService/managedClusters/agentPools/write | Cria um novo pool de agentes ou atualiza um existente |
> | Ação | Microsoft. ContainerService/managedClusters/availableAgentPoolVersions/Read | Obter as versões de pool de agente disponíveis do cluster |
> | Ação | Microsoft.ContainerService/managedClusters/delete | Excluir um cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/detectores/leitura | Obter o detector de cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/diagnosticsstate/Read | Obtém o estado de diagnóstico do cluster |
> | Ação | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Listar a credencial clusterAdmin de um cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/listClusterMonitoringUserCredential/Action | Listar a credencial clusterMonitoringUser de um cluster gerenciado |
> | Ação | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Listar a credencial clusterUser de um cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/privateEndpointConnectionsApproval/Action | Determina se o usuário tem permissão para aprovar uma conexão de ponto de extremidade privada |
> | Ação | Microsoft.ContainerService/managedClusters/read | Obter um cluster gerenciado |
> | Ação | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Redefinir o perfil do AAD de um cluster gerenciado |
> | Ação | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Redefinir o perfil de entidade de serviço de um cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/rotateClusterCertificates/Action | Girar certificados de um cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/upgradeProfiles/Read | Obtém o perfil de atualização do cluster |
> | Ação | Microsoft.ContainerService/managedClusters/write | Cria um novo cluster gerenciado ou atualiza um existente |
> | Ação | Microsoft.ContainerService/openShiftClusters/delete | Excluir um cluster de turno aberto |
> | Ação | Microsoft.ContainerService/openShiftClusters/read | Obter um cluster de turno aberto |
> | Ação | Microsoft.ContainerService/openShiftClusters/write | Cria um novo cluster de turnos aberto ou atualiza um existente |
> | Ação | Microsoft.ContainerService/openShiftManagedClusters/delete | Excluir um cluster gerenciado de turno aberto |
> | Ação | Microsoft.ContainerService/openShiftManagedClusters/read | Obter um cluster gerenciado de turno aberto |
> | Ação | Microsoft.ContainerService/openShiftManagedClusters/write | Cria um novo cluster gerenciado de turno aberto ou atualiza um existente |
> | Ação | Microsoft.ContainerService/operations/read | Lista as operações disponíveis no provedor de recursos Microsoft. ContainerService |
> | Ação | Microsoft.ContainerService/register/action | Registra a assinatura com o provedor de recursos Microsoft. ContainerService |
> | Ação | Microsoft.ContainerService/unregister/action | Cancela o registro da assinatura com o provedor de recursos Microsoft. ContainerService |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. CostManagement/Alerts/Write | Atualizar alertas. |
> | Ação | Microsoft.CostManagement/cloudConnectors/delete | Exclua o Cloudconnector for especificado. |
> | Ação | Microsoft.CostManagement/cloudConnectors/read | Liste o cloudConnectors para o usuário autenticado. |
> | Ação | Microsoft.CostManagement/cloudConnectors/write | Criar ou atualizar o Cloudconnector for especificado. |
> | Ação | Microsoft.CostManagement/dimensions/read | Lista todas as dimensões com suporte por um escopo. |
> | Ação | Microsoft.CostManagement/exports/action | Execute a exportação especificada. |
> | Ação | Microsoft.CostManagement/exports/delete | Excluir a exportação especificada. |
> | Ação | Microsoft.CostManagement/exports/read | Liste as exportações por escopo. |
> | Ação | Microsoft.CostManagement/exports/run/action | Execute exportações. |
> | Ação | Microsoft.CostManagement/exports/write | Criar ou atualizar a exportação especificada. |
> | Ação | Microsoft. CostManagement/externalBillingAccounts/dimensão/leitura | Lista todas as dimensões com suporte para BillingAccounts externas. |
> | Ação | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Liste o externalSubscriptions em um externalBillingAccount para o usuário autenticado. |
> | Ação | Microsoft. CostManagement/externalBillingAccounts/previsão/ação | Prever dados de uso para BillingAccounts externos. |
> | Ação | Microsoft. CostManagement/externalBillingAccounts/Forecast/Read | Prever dados de uso para BillingAccounts externos. |
> | Ação | Microsoft. CostManagement/externalBillingAccounts/consulta/ação | Consultar dados de uso para BillingAccounts externos. |
> | Ação | Microsoft. CostManagement/externalBillingAccounts/consulta/leitura | Consultar dados de uso para BillingAccounts externos. |
> | Ação | Microsoft.CostManagement/externalBillingAccounts/read | Liste o externalBillingAccounts para o usuário autenticado. |
> | Ação | Microsoft. CostManagement/externalSubscriptions/Dimensions/Read | Lista todas as dimensões com suporte para a assinatura externa. |
> | Ação | Microsoft. CostManagement/externalSubscriptions/previsão/ação | Prever dados de uso para BillingAccounts externos. |
> | Ação | Microsoft. CostManagement/externalSubscriptions/Forecast/Read | Prever dados de uso para BillingAccounts externos. |
> | Ação | Microsoft. CostManagement/externalSubscriptions/consulta/ação | Consultar dados de uso para assinatura externa. |
> | Ação | Microsoft. CostManagement/externalSubscriptions/consulta/leitura | Consultar dados de uso para assinatura externa. |
> | Ação | Microsoft.CostManagement/externalSubscriptions/read | Liste o externalSubscriptions para o usuário autenticado. |
> | Ação | Microsoft.CostManagement/externalSubscriptions/write | Atualizar grupo de gerenciamento associado de externalSubscription |
> | Ação | Microsoft. CostManagement/previsão/ação | Prever dados de uso por um escopo. |
> | Ação | Microsoft. CostManagement/previsão/leitura | Prever dados de uso por um escopo. |
> | Ação | Microsoft. CostManagement/operações/leitura | Listar todas as operações com suporte pelo provedor de recursos Microsoft. CostManagement. |
> | Ação | Microsoft.CostManagement/query/action | Consultar dados de uso por um escopo. |
> | Ação | Microsoft.CostManagement/query/read | Consultar dados de uso por um escopo. |
> | Ação | Microsoft.CostManagement/register/action | Registrar a ação para o escopo do Microsoft. CostManagement por uma assinatura. |
> | Ação | Microsoft.CostManagement/reports/action | Agendar relatórios sobre dados de uso por um escopo. |
> | Ação | Microsoft.CostManagement/reports/read | Agendar relatórios sobre dados de uso por um escopo. |
> | Ação | Microsoft.CostManagement/tenants/register/action | Registrar a ação para o escopo do Microsoft. CostManagement por um locatário. |
> | Ação | Microsoft. CostManagement/views/Action | Criar modo de exibição. |
> | Ação | Microsoft. CostManagement/views/Delete | Excluir exibições salvas. |
> | Ação | Microsoft. CostManagement/views/Read | Listar todas as exibições salvas. |
> | Ação | Microsoft. CostManagement/views/Write | Atualizar exibição. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Permite agendar uma retirada para remessas de retorno. |
> | Ação | Microsoft.DataBox/jobs/cancel/action | Cancela um pedido em andamento. |
> | Ação | Microsoft.DataBox/jobs/delete | Excluir os pedidos |
> | Ação | Microsoft.DataBox/jobs/listCredentials/action | Lista as credenciais não criptografadas relacionadas ao pedido. |
> | Ação | Microsoft.DataBox/jobs/read | Listar ou obter os pedidos |
> | Ação | Microsoft.DataBox/jobs/write | Criar ou atualizar os pedidos |
> | Ação | Microsoft.DataBox/locations/availableSkus/action | Esse método retorna a lista de SKUs disponíveis. |
> | Ação | Microsoft. Data Box/Locations/availableSkus/Read | Listar ou obter os SKUs disponíveis |
> | Ação | Microsoft.DataBox/locations/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. Data Box/Locations/regionConfiguration/Action | Esse método retorna as configurações para a região. |
> | Ação | Microsoft.DataBox/locations/validateAddress/action | Valida o endereço de envio e fornece endereços alternativos, se houver. |
> | Ação | Microsoft. Data Box/Locations/validateInputs/Action | Esse método faz todo o tipo de validações. |
> | Ação | Microsoft. Data Box/operações/leitura | Listar ou obter as operações |
> | Ação | Microsoft.DataBox/register/action | Registrar provedor Microsoft. Data Box |
> | Ação | Microsoft. Data Box/subscriptions/resourceGroups/moveResources/Action | Esse método executa a movimentação de recursos. |
> | Ação | Microsoft. Data Box/subscriptions/resourceGroups/validateMoveResources/Action | Esse método valida se a movimentação de recursos é permitida ou não. |
> | Ação | Microsoft. Data Box/cancelar registro/ação | Provedor de cancelamento de registro Microsoft. Data Box |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Listar ou obter os alertas |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Exclui os agendamentos de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Listar ou obter as agendas de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Listar ou obter as agendas de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Cria ou atualiza os agendamentos de largura de banda |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Exclui os dispositivos Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Baixar atualizações no dispositivo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Recupera informações estendidas do recurso |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Instalar atualizações no dispositivo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Listar ou obter os trabalhos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Listar ou obter as configurações de rede do dispositivo |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/nós/leitura | Listar ou obter os nós |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | Listar ou obter o status da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Exclui os pedidos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Listar ou obter os pedidos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Listar ou obter os pedidos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Criar ou atualizar os pedidos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Listar ou obter os dispositivos Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Listar ou obter os dispositivos Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Listar ou obter os dispositivos Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Exclui as funções |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Listar ou obter as funções |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Listar ou obter as funções |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Cria ou atualiza as funções |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Verificar se há atualizações |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Atualizar configurações de segurança |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Exclui os compartilhamentos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Listar ou obter os compartilhamentos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Listar ou obter os compartilhamentos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Atualizar os metadados de compartilhamento com os dados da nuvem |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Criar ou atualizar os compartilhamentos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Exclui as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Listar ou obter as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Listar ou obter as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Criar ou atualizar as credenciais da conta de armazenamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Exclui os gatilhos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Listar ou obter os gatilhos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Listar ou obter os gatilhos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Criar ou atualizar os gatilhos |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Listar ou obter o resumo de atualizações |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Carregar certificado para registro de dispositivo |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Exclui os usuários do compartilhamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Listar ou obter o resultado da operação |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Listar ou obter os usuários do compartilhamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Listar ou obter os usuários do compartilhamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Criar ou atualizar os usuários do compartilhamento |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Cria ou atualiza os dispositivos Data Box Edge |
> | Ação | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Cria ou atualiza os dispositivos Data Box Edge |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Databricks/locations/getNetworkPolicies/action | Obter políticas de intenção de rede para uma sub-rede com base no local usado pelo NRP |
> | Ação | Microsoft. databricks/Locations/operationstatuses/Read | Lê o status da operação para o recurso. |
> | Ação | Microsoft. databricks/operações/leitura | Obtém a lista de operações. |
> | Ação | Microsoft.Databricks/register/action | Registre-se no databricks. |
> | Ação | Microsoft.Databricks/workspaces/delete | Remove um espaço de trabalho do databricks. |
> | Ação | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Define as configurações de diagnóstico disponíveis para o espaço de trabalho do databricks |
> | Ação | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Obtém as definições de log disponíveis para o espaço de trabalho do databricks |
> | Ação | Microsoft.Databricks/workspaces/read | Recupera uma lista de espaços de trabalho do databricks. |
> | Ação | Microsoft. databricks/espaços de trabalho/refreshPermissions/Action | Atualizar permissões para um espaço de trabalho |
> | Ação | Microsoft. databricks/espaços de trabalho/updateDenyAssignment/Action | Atualizar a atribuição de negação não ações para um grupo de recursos gerenciados de um espaço de trabalho |
> | Ação | Microsoft. databricks/espaços de trabalho/virtualNetworkPeerings/Delete | Exclui um emparelhamento de rede virtual |
> | Ação | Microsoft. databricks/espaços de trabalho/virtualNetworkPeerings/leitura | Obtém o emparelhamento de rede virtual. |
> | Ação | Microsoft. databricks/espaços de trabalho/virtualNetworkPeerings/Write | Adicionar ou modificar o emparelhamento de rede virtual |
> | Ação | Microsoft.Databricks/workspaces/write | Cria um espaço de trabalho do databricks. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DataCatalog/catalogs/delete | Excluir recurso de catálogos para o provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/catalogs/read | Ler recurso de catálogos para provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/catalogs/write | Gravar recurso de catálogos para o provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/checkNameAvailability/read | Verifique a disponibilidade do nome do catálogo para o provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/datacatalogs/delete | Excluir recurso datacatalog do provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/datacatalogs/read | Ler recurso datacatalog para provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/datacatalogs/write | Gravar recurso do datacatalog para o provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/operations/read | Lê todas as operações disponíveis no provedor de recursos do catálogo de dados. |
> | Ação | Microsoft.DataCatalog/register/action | Registrar a assinatura para o provedor de recursos do catálogo de dados |
> | Ação | Microsoft.DataCatalog/unregister/action | Cancelar o registro da assinatura do provedor de recursos do catálogo de dados |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Verifica se o nome do Data Factory está disponível para uso. |
> | Ação | Microsoft.DataFactory/datafactories/activitywindows/read | Lê as janelas de atividades no Data Factory com os parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Lê as janelas de atividades para a atividade pipeline com os parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Lê as janelas de atividades para o pipeline com os parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/delete | Exclui qualquer pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Pausa qualquer pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/read | Lê qualquer pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Retoma qualquer pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/update/action | Atualiza qualquer pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datapipelines/write | Cria ou atualiza qualquer pipeline. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Lê as janelas de atividades para o conjunto de um com os parâmetros especificados. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/delete | Exclui qualquer conjunto de banco de qualquer. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/read | Lê qualquer DataSet. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Lê a execução da fatia de dados para o determinado DataSet com a hora de início fornecida. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/slices/read | Obtém as fatias de dados no período determinado. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/slices/write | Atualize o status da fatia de dados. |
> | Ação | Microsoft.DataFactory/datafactories/datasets/write | Cria ou atualiza qualquer conjunto de todos os conjuntos de qualquer. |
> | Ação | Microsoft.DataFactory/datafactories/delete | Exclui o Data Factory. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Lê as informações de conexão para qualquer gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/delete | Exclui qualquer gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Lista as chaves de autenticação para qualquer gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/read | Lê qualquer gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Regenera as chaves de autenticação para qualquer gateway. |
> | Ação | Microsoft.DataFactory/datafactories/gateways/write | Cria ou atualiza qualquer gateway. |
> | Ação | Microsoft.DataFactory/datafactories/linkedServices/delete | Exclui qualquer serviço vinculado. |
> | Ação | Microsoft.DataFactory/datafactories/linkedServices/read | Lê qualquer serviço vinculado. |
> | Ação | Microsoft.DataFactory/datafactories/linkedServices/write | Cria ou atualiza qualquer serviço vinculado. |
> | Ação | Microsoft.DataFactory/datafactories/read | Lê o Data Factory. |
> | Ação | Microsoft.DataFactory/datafactories/runs/loginfo/read | Lê um URI de SAS para um contêiner de BLOB que contém os logs. |
> | Ação | Microsoft.DataFactory/datafactories/tables/delete | Exclui qualquer conjunto de banco de qualquer. |
> | Ação | Microsoft.DataFactory/datafactories/tables/read | Lê qualquer DataSet. |
> | Ação | Microsoft.DataFactory/datafactories/tables/write | Cria ou atualiza qualquer conjunto de todos os conjuntos de qualquer. |
> | Ação | Microsoft.DataFactory/datafactories/write | Cria ou atualiza o Data Factory. |
> | Ação | Microsoft. datafactory/fábricas/addDataFlowToDebugSession/Action | Adicione o fluxo de dados à sessão de depuração para visualização. |
> | Ação | Microsoft.DataFactory/factories/cancelpipelinerun/action | Cancela a execução de pipeline especificada pela ID de execução. |
> | Ação | Microsoft. datafactory/fábricas/cancelSandboxPipelineRun/Action | Cancela uma execução de depuração para o pipeline. |
> | Ação | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Cria uma sessão de depuração de fluxo de dados. |
> | Ação | Microsoft.DataFactory/factories/dataflows/delete | Exclui o fluxo de dados. |
> | Ação | Microsoft.DataFactory/factories/dataflows/read | Lê o fluxo de dados. |
> | Ação | Microsoft.DataFactory/factories/dataflows/write | Criar ou atualizar o fluxo de dados |
> | Ação | Microsoft.DataFactory/factories/datasets/delete | Exclui qualquer conjunto de banco de qualquer. |
> | Ação | Microsoft.DataFactory/factories/datasets/read | Lê qualquer DataSet. |
> | Ação | Microsoft.DataFactory/factories/datasets/write | Cria ou atualiza qualquer conjunto de todos os conjuntos de qualquer. |
> | Ação | Microsoft. datafactory/fábricas/debugpipelineruns/cancelamento/ação | Cancela uma execução de depuração para o pipeline. |
> | Ação | Microsoft.DataFactory/factories/delete | Exclui Data Factory. |
> | Ação | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Exclui uma sessão de depuração de fluxo de dados. |
> | Ação | Microsoft. datafactory/fábricas/executeDataFlowDebugCommand/Action | Executar comando de depuração de fluxo de dados. |
> | Ação | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Obtém acesso ao serviço de plano de dataplane do ADF. |
> | Ação | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Lê o acesso ao serviço de plano de dataplane do ADF. |
> | Ação | Microsoft.DataFactory/factories/getFeatureValue/action | Obter o valor do recurso de controle de exposição para o local específico. |
> | Ação | Microsoft.DataFactory/factories/getFeatureValue/read | Lê o valor do recurso de controle de exposição para o local específico. |
> | Ação | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Obtém o token de acesso do GitHub. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/delete | Exclui qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Lê Integration Runtime informações de conexão. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Obtenha os metadados do SSIS Integration Runtime para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Lê Integration Runtime status. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Criar referência de Integration Runtime vinculada no Integration Runtime compartilhado especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Lista as chaves de autenticação para qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Obtém os dados de monitoramento para qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Exclui o nó para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Retorna o endereço IP para o nó especificado do Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Lê o nó para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Atualiza um nó de Integration Runtime hospedado internamente. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/read | Lê qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Atualize os metadados do SSIS Integration Runtime para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Regenera as chaves de autenticação para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Remove as referências Integration Runtime vinculadas da Integration Runtime especificada. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/start/action | Inicia qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Interrompe qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Sincroniza as credenciais para o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Atualiza o Integration Runtime especificado. |
> | Ação | Microsoft.DataFactory/factories/integrationruntimes/write | Cria ou atualiza qualquer Integration Runtime. |
> | Ação | Microsoft.DataFactory/factories/linkedServices/delete | Exclui o serviço vinculado. |
> | Ação | Microsoft.DataFactory/factories/linkedServices/read | Lê o serviço vinculado. |
> | Ação | Microsoft.DataFactory/factories/linkedServices/write | Criar ou atualizar serviço vinculado |
> | Ação | Microsoft. datafactory/fábricas/operationResults/Read | Obtém os resultados da operação. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Lê as execuções de atividade para a ID de execução de pipeline especificada. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Cancela a execução de pipeline especificada pela ID de execução. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | Consulta as execuções de atividade para a ID de execução de pipeline especificada. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Lê o resultado das execuções de atividade de consulta para a ID de execução de pipeline especificada. |
> | Ação | Microsoft.DataFactory/factories/pipelineruns/read | Lê as execuções de pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/createrun/action | Cria uma execução para o pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/delete | Exclui o pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Obtém o progresso das execuções de atividade. |
> | Ação | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Lê a execução do pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/read | Lê o pipeline. |
> | Ação | Microsoft. datafactory/fábricas/pipelines/área restrita/ação | Cria um ambiente de execução de depuração para o pipeline. |
> | Ação | Microsoft. datafactory/fábricas/pipelines/área restrita/criação/ação | Cria um ambiente de execução de depuração para o pipeline. |
> | Ação | Microsoft. datafactory/fábricas/pipelines/área restrita/execução/ação | Cria uma execução de depuração para o pipeline. |
> | Ação | Microsoft.DataFactory/factories/pipelines/write | Criar ou atualizar o pipeline |
> | Ação | Microsoft. datafactory/fábricas/querydataflowdebugsessions/Action | Consulta uma sessão de depuração de fluxo de dados. |
> | Ação | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Consulta as execuções do pipeline de depuração. |
> | Ação | Microsoft.DataFactory/factories/querypipelineruns/action | Consulta as execuções de pipeline. |
> | Ação | Microsoft.DataFactory/factories/querypipelineruns/read | Lê o resultado das execuções do pipeline de consulta. |
> | Ação | Microsoft.DataFactory/factories/querytriggerruns/action | Consulta o gatilho é executado. |
> | Ação | Microsoft.DataFactory/factories/querytriggerruns/read | Lê o resultado de execuções de gatilho. |
> | Ação | Microsoft.DataFactory/factories/read | Lê Data Factory. |
> | Ação | Microsoft. datafactory/fábricas/sandboxpipelineruns/Action | Consulta as execuções do pipeline de depuração. |
> | Ação | Microsoft. datafactory/fábricas/sandboxpipelineruns/Read | Obtém as informações de execução de depuração para o pipeline. |
> | Ação | Microsoft. datafactory/factories/sandboxpipelineruns/sandboxActivityRuns/Read | Obtém as informações de execução de depuração para a atividade. |
> | Ação | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Inicia uma sessão de depuração de fluxo de dados. |
> | Ação | Microsoft.DataFactory/factories/triggerruns/read | Lê as execuções do gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/delete | Exclui qualquer gatilho. |
> | Ação | Microsoft. datafactory/factories/triggers/geteventsubscriptionstatus/Action | Status da assinatura do evento. |
> | Ação | Microsoft.DataFactory/factories/triggers/read | Lê qualquer gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/start/action | Inicia qualquer gatilho. |
> | Ação | Microsoft.DataFactory/factories/triggers/stop/action | Interrompe qualquer gatilho. |
> | Ação | Microsoft. datafactory/factories/triggers/SubscribeToEvents/Action | Assinar eventos. |
> | Ação | Microsoft.DataFactory/factories/triggers/triggerruns/read | Lê as execuções do gatilho. |
> | Ação | Microsoft. datafactory/factories/triggers/UnsubscribeFromEvents/Action | Cancelar a assinatura de eventos. |
> | Ação | Microsoft.DataFactory/factories/triggers/write | Cria ou atualiza qualquer gatilho. |
> | Ação | Microsoft.DataFactory/factories/write | Criar ou atualizar Data Factory |
> | Ação | Microsoft.DataFactory/locations/configureFactoryRepo/action | Configura o repositório para a fábrica. |
> | Ação | Microsoft.DataFactory/locations/getFeatureValue/action | Obter o valor do recurso de controle de exposição para o local específico. |
> | Ação | Microsoft.DataFactory/locations/getFeatureValue/read | Lê o valor do recurso de controle de exposição para o local específico. |
> | Ação | Microsoft.DataFactory/operations/read | Lê todas as operações no provedor do Microsoft Data Factory. |
> | Ação | Microsoft.DataFactory/register/action | Registra a assinatura para o provedor de recursos de Data Factory. |
> | Ação | Microsoft.DataFactory/unregister/action | Cancela o registro da assinatura para o provedor de recursos de Data Factory. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Excluir uma política de computação. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Obter informações sobre uma política de computação. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Criar ou atualizar uma política de computação. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Desvincular uma conta do DataLakeStore de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Obter informações sobre uma conta DataLakeStore vinculada de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Criar ou atualizar uma conta DataLakeStore vinculada de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/delete | Excluir uma conta do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Excluir uma regra de firewall. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Obter informações sobre uma regra de firewall. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Criar ou atualizar uma regra de firewall. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Obtenha o resultado de uma operação de conta do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/read | Obter informações sobre uma conta existente do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Listar tokens SAS para contêineres de armazenamento de uma conta de armazenamento vinculada de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Obter contêineres de uma conta de armazenamento vinculada de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Desvincular uma conta de armazenamento de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Obtenha informações sobre uma conta de armazenamento vinculada de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Criar ou atualizar uma conta de armazenamento vinculada de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Conceda permissões para cancelar trabalhos enviados por outros usuários. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Transferir SystemMaxAnalyticsUnits entre contas do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Excluir uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Obter informações sobre uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Criar ou atualizar uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeAnalytics/accounts/write | Criar ou atualizar uma conta do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/capability/read | Obtenha informações de funcionalidade de uma assinatura em relação ao uso do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Verifique a disponibilidade de um nome de conta do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/operationResults/read | Obtenha o resultado de uma operação de conta do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/locations/usages/read | Obter informações de usos de cota de uma assinatura em relação ao uso de DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/operations/read | Obter as operações disponíveis do DataLakeAnalytics. |
> | Ação | Microsoft.DataLakeAnalytics/register/action | Registre a assinatura no DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DataLakeStore/accounts/delete | Excluir uma conta do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Habilite o keyvault para uma conta do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Excluir um filtro EventGrid. |
> | Ação | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Obter um filtro EventGrid. |
> | Ação | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Criar ou atualizar um filtro EventGrid. |
> | Ação | Microsoft.DataLakeStore/accounts/firewallRules/delete | Excluir uma regra de firewall. |
> | Ação | Microsoft.DataLakeStore/accounts/firewallRules/read | Obter informações sobre uma regra de firewall. |
> | Ação | Microsoft.DataLakeStore/accounts/firewallRules/write | Criar ou atualizar uma regra de firewall. |
> | Ação | Microsoft.DataLakeStore/accounts/operationResults/read | Obtenha o resultado de uma operação de conta do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/accounts/read | Obter informações sobre uma conta existente do DataLakeStore. |
> | Ação | Microsoft. DataLakeStore/contas/compartilhamentos/excluir | Excluir um compartilhamento. |
> | Ação | Microsoft. DataLakeStore/contas/compartilhamentos/leitura | Obter informações sobre um compartilhamento. |
> | Ação | Microsoft. DataLakeStore/contas/compartilhamentos/gravação | Criar ou atualizar um compartilhamento. |
> | Ação | Microsoft.DataLakeStore/accounts/Superuser/action | Conceda o superusuário em Data Lake Store quando concedido com Microsoft. Authorization/roleAssignments/Write. |
> | Ação | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Excluir um provedor de identidade confiável. |
> | Ação | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Obtenha informações sobre um provedor de identidade confiável. |
> | Ação | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Criar ou atualizar um provedor de identidade confiável. |
> | Ação | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Excluir uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Obter informações sobre uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Criar ou atualizar uma regra de rede virtual. |
> | Ação | Microsoft.DataLakeStore/accounts/write | Criar ou atualizar uma conta do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/capability/read | Obtenha informações de funcionalidade de uma assinatura em relação ao uso do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Verifique a disponibilidade de um nome de conta do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/operationResults/read | Obtenha o resultado de uma operação de conta do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/locations/usages/read | Obter informações de usos de cota de uma assinatura em relação ao uso de DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/operations/read | Obter as operações disponíveis do DataLakeStore. |
> | Ação | Microsoft.DataLakeStore/register/action | Registre a assinatura no DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DataMigration/locations/operationResults/read | Obter o status de uma operação de execução longa relacionada a uma resposta 202 aceita |
> | Ação | Microsoft.DataMigration/locations/operationStatuses/read | Obter o status de uma operação de execução longa relacionada a uma resposta 202 aceita |
> | Ação | Microsoft.DataMigration/register/action | Registra a assinatura com o provedor de serviços de migração de banco de dados do Azure |
> | Ação | Microsoft.DataMigration/services/addWorker/action | Adiciona um trabalho do DMS aos trabalhos de WDS disponível do serviço |
> | Ação | Microsoft.DataMigration/services/checkStatus/action | Verificar se o serviço está implantado e em execução |
> | Ação | Microsoft.DataMigration/services/configureWorker/action | Configura um trabalho do DMS para os trabalhos de WDS disponível do serviço |
> | Ação | Microsoft.DataMigration/services/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/projects/accessArtifacts/action | Gerar uma URL que pode ser usada para obter ou colocar artefatos do projeto |
> | Ação | Microsoft.DataMigration/services/projects/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/projects/files/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/projects/files/read | Ler informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/projects/files/read/action | Obter uma URL que pode ser usada para ler o conteúdo do arquivo |
> | Ação | Microsoft.DataMigration/services/projects/files/readWrite/action | Obter uma URL que pode ser usada para ler ou gravar o conteúdo do arquivo |
> | Ação | Microsoft.DataMigration/services/projects/files/write | Criar ou atualizar recursos e suas propriedades |
> | Ação | Microsoft.DataMigration/services/projects/read | Ler informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/projects/tasks/cancel/action | Cancelar a tarefa se ela estiver em execução no momento |
> | Ação | Microsoft.DataMigration/services/projects/tasks/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/projects/tasks/read | Ler informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/projects/tasks/write | Tarefas de execução do serviço de migração de banco de dados do Azure |
> | Ação | Microsoft.DataMigration/services/projects/write | Tarefas de execução do serviço de migração de banco de dados do Azure |
> | Ação | Microsoft.DataMigration/services/read | Ler informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/removeWorker/action | Remove um trabalho DMS para os trabalhos de WDS disponível do serviço |
> | Ação | Microsoft.DataMigration/services/serviceTasks/cancel/action | Cancelar a tarefa se ela estiver em execução no momento |
> | Ação | Microsoft.DataMigration/services/serviceTasks/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/serviceTasks/read | Ler informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/serviceTasks/write | Tarefas de execução do serviço de migração de banco de dados do Azure |
> | Ação | Microsoft.DataMigration/services/slots/delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft.DataMigration/services/slots/read | Ler informações sobre recursos |
> | Ação | Microsoft.DataMigration/services/slots/write | Criar ou atualizar recursos e suas propriedades |
> | Ação | Microsoft.DataMigration/services/start/action | Inicie o serviço DMS para permitir que ele processe as migrações novamente |
> | Ação | Microsoft.DataMigration/services/stop/action | Parar o serviço DMS para minimizar seu custo |
> | Ação | Microsoft.DataMigration/services/updateAgentConfig/action | Atualiza a configuração do agente DMS com os valores fornecidos. |
> | Ação | Microsoft.DataMigration/services/write | Criar ou atualizar recursos e suas propriedades |
> | Ação | Microsoft.DataMigration/skus/read | Obtenha uma lista de SKUs com suporte dos recursos do DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DBforMariaDB/checkNameAvailability/action | Verifique se o nome do servidor fornecido está disponível para provisionamento em todo o mundo para uma determinada assinatura. |
> | Ação | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Retornar resultados da operação do servidor MariaDB |
> | Ação | Microsoft.DBforMariaDB/locations/operationResults/read | Retornar os resultados da operação do servidor MariaDB com base em resourcegroup |
> | Ação | Microsoft.DBforMariaDB/locations/operationResults/read | Retornar resultados da operação do servidor MariaDB |
> | Ação | Microsoft.DBforMariaDB/locations/performanceTiers/read | Retorna a lista de níveis de desempenho disponíveis. |
> | Ação | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionOperationResults/Read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft. DBforMariaDB/Locations/privateEndpointConnectionProxyOperationResults/Read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft. DBforMariaDB/Locations/serverKeyAzureAsyncOperation/Read | Obter operações em andamento em chaves de servidor de Transparent Data Encryption |
> | Ação | Microsoft. DBforMariaDB/Locations/serverKeyOperationResults/Read | Obter operações em andamento em chaves de servidor de Transparent Data Encryption |
> | Ação | Microsoft.DBforMariaDB/operations/read | Retornar a lista de operações de MariaDB. |
> | Ação | Microsoft.DBforMariaDB/performanceTiers/read | Retorna a lista de níveis de desempenho disponíveis. |
> | Ação | Microsoft.DBforMariaDB/register/action | Registrar provedor de recursos MariaDB |
> | Ação | Microsoft.DBforMariaDB/servers/administrators/delete | Exclui um administrador existente do MariaDB Server. |
> | Ação | Microsoft.DBforMariaDB/servers/administrators/read | Obtém uma lista de administradores do servidor MariaDB. |
> | Ação | Microsoft.DBforMariaDB/servers/administrators/write | Cria ou atualiza o administrador do servidor MariaDB com os parâmetros especificados. |
> | Ação | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | Criar uma nova sessão de ação de recomendação |
> | Ação | Microsoft.DBforMariaDB/servers/advisors/read | Retornar a lista de consultores |
> | Ação | Microsoft.DBforMariaDB/servers/advisors/read | Retornar um supervisor |
> | Ação | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Retornar a lista de ações recomendadas |
> | Ação | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Retornar a lista de ações recomendadas |
> | Ação | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Retornar uma ação recomendada |
> | Ação | Microsoft.DBforMariaDB/servers/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a configuração especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/configurations/write | Atualizar o valor para a configuração especificada |
> | Ação | Microsoft.DBforMariaDB/servers/databases/delete | Exclui um banco de dados MariaDB existente. |
> | Ação | Microsoft.DBforMariaDB/servers/databases/read | Retornar a lista de bancos de dados MariaDB ou obter as propriedades do banco de dados especificado. |
> | Ação | Microsoft.DBforMariaDB/servers/databases/write | Cria um banco de dados MariaDB com os parâmetros especificados ou atualiza as propriedades do banco de dados especificado. |
> | Ação | Microsoft.DBforMariaDB/servers/delete | Exclui um servidor existente. |
> | Ação | Microsoft.DBforMariaDB/servers/firewallRules/delete | Exclui uma regra de firewall existente. |
> | Ação | Microsoft.DBforMariaDB/servers/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades da regra de firewall especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/firewallRules/write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Ação | Microsoft. DBforMariaDB/servidores/chaves/excluir | Exclui uma chave de servidor existente. |
> | Ação | Microsoft. DBforMariaDB/servidores/chaves/leitura | Retornar a lista de chaves de servidor ou obter as propriedades para a chave de servidor especificada. |
> | Ação | Microsoft. DBforMariaDB/servidores/chaves/gravação | Cria uma chave com os parâmetros especificados ou atualiza as propriedades ou marcas para a chave de servidor especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/logFiles/read | Retornar a lista de arquivos de log MariaDB. |
> | Ação | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/Delete | Exclui um proxy de conexão de ponto de extremidade privado existente |
> | Ação | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/Read | Retorna a lista de proxies de conexão de ponto de extremidade privado ou obtém as propriedades para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/Validate/Action | Valida uma conexão de ponto de extremidade privada criar chamada do lado do NRP |
> | Ação | Microsoft. DBforMariaDB/Servers/privateEndpointConnectionProxies/Write | Cria um proxy de conexão de ponto de extremidade privado com os parâmetros especificados ou atualiza as propriedades ou marcas para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft. DBforMariaDB/Servers/privateEndpointConnections/Delete | Exclui uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft. DBforMariaDB/Servers/privateEndpointConnections/Read | Retorna a lista de conexões de ponto de extremidade privado ou obtém as propriedades para a conexão de ponto de extremidade particular especificada. |
> | Ação | Microsoft. DBforMariaDB/Servers/privateEndpointConnections/Write | Aprova ou rejeita uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft. DBforMariaDB/Servers/privateLinkResources/Read | Obter os recursos de link privado para o servidor MariaDB correspondente |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração desindependente do recurso |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para servidores MariaDB |
> | Ação | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.DBforMariaDB/servers/queryTexts/action | Retornar os textos de uma lista de consultas |
> | Ação | Microsoft.DBforMariaDB/servers/queryTexts/action | Retornar o texto de uma consulta |
> | Ação | Microsoft.DBforMariaDB/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft.DBforMariaDB/servers/recoverableServers/read | Retornar as informações recuperáveis do servidor MariaDB |
> | Ação | Microsoft.DBforMariaDB/servers/replicas/read | Obter réplicas de leitura de um servidor MariaDB |
> | Ação | Microsoft.DBforMariaDB/servers/restart/action | Reinicia um servidor específico. |
> | Ação | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em um determinado servidor |
> | Ação | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor de um determinado servidor |
> | Ação | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Retornar a lista de estatísticas de consulta para as principais consultas. |
> | Ação | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Retornar uma estatística de consulta |
> | Ação | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Atualizar configurações para o servidor especificado |
> | Ação | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Exclui uma regra de rede virtual existente |
> | Ação | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou marcas para a regra de rede virtual especificada. |
> | Ação | Microsoft.DBforMariaDB/servers/waitStatistics/read | Estatísticas de espera de retorno para uma instância |
> | Ação | Microsoft.DBforMariaDB/servers/waitStatistics/read | Retornar uma estatística de espera |
> | Ação | Microsoft.DBforMariaDB/servers/write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DBforMySQL/checkNameAvailability/action | Verifique se o nome do servidor fornecido está disponível para provisionamento em todo o mundo para uma determinada assinatura. |
> | Ação | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Retornar resultados da operação do servidor MySQL |
> | Ação | Microsoft.DBforMySQL/locations/operationResults/read | Retornar resultados da operação do servidor MySQL com base no resourcegroup |
> | Ação | Microsoft.DBforMySQL/locations/operationResults/read | Retornar resultados da operação do servidor MySQL |
> | Ação | Microsoft.DBforMySQL/locations/performanceTiers/read | Retorna a lista de níveis de desempenho disponíveis. |
> | Ação | Microsoft. DBforMySQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft. DBforMySQL/Locations/privateEndpointConnectionOperationResults/Read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft. DBforMySQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft. DBforMySQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft. DBforMySQL/Locations/serverKeyAzureAsyncOperation/Read | Obter operações em andamento em chaves de servidor de Transparent Data Encryption |
> | Ação | Microsoft. DBforMySQL/Locations/serverKeyOperationResults/Read | Obter operações em andamento em chaves de servidor de Transparent Data Encryption |
> | Ação | Microsoft.DBforMySQL/operations/read | Retornar a lista de operações do MySQL. |
> | Ação | Microsoft.DBforMySQL/performanceTiers/read | Retorna a lista de níveis de desempenho disponíveis. |
> | Ação | Microsoft.DBforMySQL/register/action | Registrar provedor de recursos MySQL |
> | Ação | Microsoft.DBforMySQL/servers/administrators/delete | Exclui um administrador existente do MySQL Server. |
> | Ação | Microsoft.DBforMySQL/servers/administrators/read | Obtém uma lista de administradores do servidor MySQL. |
> | Ação | Microsoft.DBforMySQL/servers/administrators/write | Cria ou atualiza o administrador do servidor MySQL com os parâmetros especificados. |
> | Ação | Microsoft.DBforMySQL/servers/advisors/createRecommendedActionSession/action | Criar uma nova sessão de ação de recomendação |
> | Ação | Microsoft.DBforMySQL/servers/advisors/read | Retornar a lista de consultores |
> | Ação | Microsoft.DBforMySQL/servers/advisors/read | Retornar um supervisor |
> | Ação | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Retornar a lista de ações recomendadas |
> | Ação | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Retornar a lista de ações recomendadas |
> | Ação | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Retornar uma ação recomendada |
> | Ação | Microsoft.DBforMySQL/servers/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a configuração especificada. |
> | Ação | Microsoft.DBforMySQL/servers/configurations/write | Atualizar o valor para a configuração especificada |
> | Ação | Microsoft.DBforMySQL/servers/databases/delete | Exclui um banco de dados MySQL existente. |
> | Ação | Microsoft.DBforMySQL/servers/databases/read | Retornar a lista de bancos de dados MySQL ou obter as propriedades do banco de dados especificado. |
> | Ação | Microsoft.DBforMySQL/servers/databases/write | Cria um banco de dados MySQL com os parâmetros especificados ou atualiza as propriedades do banco de dados especificado. |
> | Ação | Microsoft.DBforMySQL/servers/delete | Exclui um servidor existente. |
> | Ação | Microsoft.DBforMySQL/servers/firewallRules/delete | Exclui uma regra de firewall existente. |
> | Ação | Microsoft.DBforMySQL/servers/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades da regra de firewall especificada. |
> | Ação | Microsoft.DBforMySQL/servers/firewallRules/write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Ação | Microsoft. DBforMySQL/servidores/chaves/excluir | Exclui uma chave de servidor existente. |
> | Ação | Microsoft. DBforMySQL/servidores/chaves/leitura | Retornar a lista de chaves de servidor ou obter as propriedades para a chave de servidor especificada. |
> | Ação | Microsoft. DBforMySQL/servidores/chaves/gravação | Cria uma chave com os parâmetros especificados ou atualiza as propriedades ou marcas para a chave de servidor especificada. |
> | Ação | Microsoft.DBforMySQL/servers/logFiles/read | Retornar a lista de arquivos de log do MySQL. |
> | Ação | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/Delete | Exclui um proxy de conexão de ponto de extremidade privado existente |
> | Ação | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/Read | Retorna a lista de proxies de conexão de ponto de extremidade privado ou obtém as propriedades para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/Validate/Action | Valida uma conexão de ponto de extremidade privada criar chamada do lado do NRP |
> | Ação | Microsoft. DBforMySQL/Servers/privateEndpointConnectionProxies/Write | Cria um proxy de conexão de ponto de extremidade privado com os parâmetros especificados ou atualiza as propriedades ou marcas para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft. DBforMySQL/Servers/privateEndpointConnections/Delete | Exclui uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft. DBforMySQL/Servers/privateEndpointConnections/Read | Retorna a lista de conexões de ponto de extremidade privado ou obtém as propriedades para a conexão de ponto de extremidade particular especificada. |
> | Ação | Microsoft. DBforMySQL/Servers/privateEndpointConnections/Write | Aprova ou rejeita uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft. DBforMySQL/Servers/privateLinkResources/Read | Obter os recursos de link privado para o servidor MySQL correspondente |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração desindependente do recurso |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para servidores MySQL |
> | Ação | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.DBforMySQL/servers/queryTexts/action | Retornar os textos de uma lista de consultas |
> | Ação | Microsoft.DBforMySQL/servers/queryTexts/action | Retornar o texto de uma consulta |
> | Ação | Microsoft.DBforMySQL/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft.DBforMySQL/servers/recoverableServers/read | Retornar as informações recuperáveis do MySQL Server |
> | Ação | Microsoft.DBforMySQL/servers/replicas/read | Obter réplicas de leitura de um servidor MySQL |
> | Ação | Microsoft.DBforMySQL/servers/restart/action | Reinicia um servidor específico. |
> | Ação | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em um determinado servidor |
> | Ação | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor de um determinado servidor |
> | Ação | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Retornar a lista de estatísticas de consulta para as principais consultas. |
> | Ação | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Retornar uma estatística de consulta |
> | Ação | Microsoft.DBforMySQL/servers/updateConfigurations/action | Atualizar configurações para o servidor especificado |
> | Ação | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Exclui uma regra de rede virtual existente |
> | Ação | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou marcas para a regra de rede virtual especificada. |
> | Ação | Microsoft.DBforMySQL/servers/waitStatistics/read | Estatísticas de espera de retorno para uma instância |
> | Ação | Microsoft.DBforMySQL/servers/waitStatistics/read | Retornar uma estatística de espera |
> | Ação | Microsoft.DBforMySQL/servers/write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DBforPostgreSQL/checkNameAvailability/action | Verifique se o nome do servidor fornecido está disponível para provisionamento em todo o mundo para uma determinada assinatura. |
> | Ação | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Retornar resultados da operação do servidor PostgreSQL |
> | Ação | Microsoft.DBforPostgreSQL/locations/operationResults/read | Retornar resultados da operação do servidor PostgreSQL com base no resourcegroup |
> | Ação | Microsoft.DBforPostgreSQL/locations/operationResults/read | Retornar resultados da operação do servidor PostgreSQL |
> | Ação | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Retorna a lista de níveis de desempenho disponíveis. |
> | Ação | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionOperationResults/Read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft. DBforPostgreSQL/Locations/serverKeyAzureAsyncOperation/Read | Obter operações em andamento em chaves de servidor de Transparent Data Encryption |
> | Ação | Microsoft. DBforPostgreSQL/Locations/serverKeyOperationResults/Read | Obter operações em andamento em chaves de servidor de Transparent Data Encryption |
> | Ação | Microsoft.DBforPostgreSQL/operations/read | Retornar a lista de operações PostgreSQL. |
> | Ação | Microsoft.DBforPostgreSQL/performanceTiers/read | Retorna a lista de níveis de desempenho disponíveis. |
> | Ação | Microsoft.DBforPostgreSQL/register/action | Registrar provedor de recursos PostgreSQL |
> | Ação | Microsoft.DBforPostgreSQL/servers/administrators/delete | Exclui um administrador existente do servidor PostgreSQL. |
> | Ação | Microsoft.DBforPostgreSQL/servers/administrators/read | Obtém uma lista de administradores do servidor PostgreSQL. |
> | Ação | Microsoft.DBforPostgreSQL/servers/administrators/write | Cria ou atualiza o administrador do servidor PostgreSQL com os parâmetros especificados. |
> | Ação | Microsoft.DBforPostgreSQL/servers/advisors/read | Retornar a lista de consultores |
> | Ação | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Retornar a lista de ações recomendadas |
> | Ação | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Fazer recomendações |
> | Ação | Microsoft.DBforPostgreSQL/servers/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a configuração especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/configurations/write | Atualizar o valor para a configuração especificada |
> | Ação | Microsoft.DBforPostgreSQL/servers/databases/delete | Exclui um banco de dados PostgreSQL existente. |
> | Ação | Microsoft.DBforPostgreSQL/servers/databases/read | Retornar a lista de bancos de dados PostgreSQL ou obter as propriedades do banco de dados especificado. |
> | Ação | Microsoft.DBforPostgreSQL/servers/databases/write | Cria um banco de dados PostgreSQL com os parâmetros especificados ou atualiza as propriedades do banco de dados especificado. |
> | Ação | Microsoft.DBforPostgreSQL/servers/delete | Exclui um servidor existente. |
> | Ação | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Exclui uma regra de firewall existente. |
> | Ação | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades da regra de firewall especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Ação | Microsoft. DBforPostgreSQL/servidores/chaves/excluir | Exclui uma chave de servidor existente. |
> | Ação | Microsoft. DBforPostgreSQL/servidores/chaves/leitura | Retornar a lista de chaves de servidor ou obter as propriedades para a chave de servidor especificada. |
> | Ação | Microsoft. DBforPostgreSQL/servidores/chaves/gravação | Cria uma chave com os parâmetros especificados ou atualiza as propriedades ou marcas para a chave de servidor especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/logFiles/read | Retornar a lista de arquivos de log PostgreSQL. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Delete | Exclui um proxy de conexão de ponto de extremidade privado existente |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Read | Retorna a lista de proxies de conexão de ponto de extremidade privado ou obtém as propriedades para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Validate/Action | Valida uma conexão de ponto de extremidade privada criar chamada do lado do NRP |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Write | Cria um proxy de conexão de ponto de extremidade privado com os parâmetros especificados ou atualiza as propriedades ou marcas para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Delete | Exclui uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Read | Retorna a lista de conexões de ponto de extremidade privado ou obtém as propriedades para a conexão de ponto de extremidade particular especificada. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Write | Aprova ou rejeita uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateLinkResources/Read | Obter os recursos de link privado para o servidor PostgreSQL correspondente |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração desindependente do recurso |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para servidores postgres |
> | Ação | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Retornar o texto de uma consulta |
> | Ação | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Retornar os textos de uma lista de consultas |
> | Ação | Microsoft.DBforPostgreSQL/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Retornar as informações recuperáveis do servidor PostgreSQL |
> | Ação | Microsoft.DBforPostgreSQL/servers/replicas/read | Obter réplicas de leitura de um servidor PostgreSQL |
> | Ação | Microsoft.DBforPostgreSQL/servers/restart/action | Reinicia um servidor específico. |
> | Ação | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em um determinado servidor |
> | Ação | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor de um determinado servidor |
> | Ação | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Retornar a lista de estatísticas de consulta para as principais consultas. |
> | Ação | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Atualizar configurações para o servidor especificado |
> | Ação | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Exclui uma regra de rede virtual existente |
> | Ação | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou marcas para a regra de rede virtual especificada. |
> | Ação | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Estatísticas de espera de retorno para uma instância |
> | Ação | Microsoft.DBforPostgreSQL/servers/write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou marcas para o servidor especificado. |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Retornar a lista de configurações para um servidor ou obter as propriedades para a configuração especificada. |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Atualizar o valor para a configuração especificada |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/delete | Exclui um servidor existente. |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Exclui uma regra de firewall existente. |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades da regra de firewall especificada. |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração desindependente do recurso |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para servidores postgres |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Atualizar configurações para o servidor especificado |
> | Ação | Microsoft.DBforPostgreSQL/serversv2/write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Devices/Account/diagnosticSettings/Read | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/Account/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/Account/logDefinitions/Read | Obter as definições de log disponíveis para o serviço IotHub |
> | Ação | Microsoft. Devices/Account/metricDefinitions/Read | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/checkNameAvailability/Action | Verifique se o nome do IotHub está disponível |
> | Ação | Microsoft.Devices/checkNameAvailability/Action | Verifique se o nome do IotHub está disponível |
> | Ação | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Verifique se o nome do serviço de provisionamento está disponível |
> | Ação | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Verifique se o nome do serviço de provisionamento está disponível |
> | Ação | Microsoft. Devices/digitalTwins/Delete | Excluir uma conta gêmeos digital existente e todos os seus filhos |
> | Ação | Microsoft. Devices/digitalTwins/operationresults/ler | Obter o status de uma operação em uma conta do gêmeos digital |
> | Ação | Microsoft. Devices/digitalTwins/Read | Obtém uma lista das contas do digital gêmeos associadas a uma assinatura |
> | Ação | Microsoft. Devices/digitalTwins/SKUs/leitura | Obter uma lista de SKUs válidos para contas do digital gêmeos |
> | Ação | Microsoft. Devices/digitalTwins/Write | Criar uma nova conta de gêmeos de dígito |
> | Ação | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/elasticPools/eventGridFilters/Delete | Exclui o Pool Elástico filtro de grade de eventos |
> | Ação | Microsoft. Devices/elasticPools/eventGridFilters/ler | Obter o Pool Elástico filtro de grade de eventos |
> | Ação | Microsoft. Devices/elasticPools/eventGridFilters/Write | Criar novo ou atualizar filtro de grade de eventos de Pool Elástico existente |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Exclui o certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Obtém o certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Verificar o recurso de certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Criar ou atualizar certificado |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Excluir o recurso de locatário IotHub |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Excluir grupo de consumidores do EventHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Obter grupo (s) de consumidores do EventHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Criar grupo de consumidores do EventHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Exportar dispositivos |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Obter o recurso de estatísticas de locatário IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importar dispositivos |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Obtém a chave de locatário IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Obter detalhes de trabalhos enviados em determinado IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Obter chaves de locatário IotHub |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Obter as definições de log disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Obter métricas de cota |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/Read | Obter o recurso de locatário IotHub |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Testar uma mensagem em todas as rotas existentes |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testar uma mensagem em relação a uma rota de teste fornecida |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Obtém a integridade de todos os pontos de extremidade de roteamento para um IotHub |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/operationResults/ler | Obter o resultado da operação assíncrona Put para SecuritySettings do hub de locatário IOT |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/leitura | Obter as configurações da central de segurança do Azure no Hub de locatário IOT |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Write | Atualizar as configurações da central de segurança do Azure no Hub de locatário IOT |
> | Ação | Microsoft.Devices/elasticPools/iotHubTenants/Write | Criar ou atualizar o recurso de locatário IotHub |
> | Ação | Microsoft.Devices/ElasticPools/metricDefinitions/read | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/iotHubs/certificates/Delete | Exclui o certificado |
> | Ação | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft.Devices/iotHubs/certificates/Read | Obtém o certificado |
> | Ação | Microsoft.Devices/iotHubs/certificates/verify/Action | Verificar o recurso de certificado |
> | Ação | Microsoft.Devices/iotHubs/certificates/Write | Criar ou atualizar certificado |
> | Ação | Microsoft.Devices/iotHubs/Delete | Excluir recurso IotHub |
> | Ação | Microsoft.Devices/IotHubs/diagnosticSettings/read | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/IotHubs/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Excluir o filtro de grade de eventos |
> | Ação | Microsoft.Devices/iotHubs/eventGridFilters/Read | Obter o filtro de grade de eventos |
> | Ação | Microsoft.Devices/iotHubs/eventGridFilters/Write | Criar novo ou atualizar filtro de grade de eventos existente |
> | Ação | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Excluir grupo de consumidores do EventHub |
> | Ação | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Obter grupo (s) de consumidores do EventHub |
> | Ação | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Criar grupo de consumidores do EventHub |
> | Ação | Microsoft.Devices/iotHubs/exportDevices/Action | Exportar dispositivos |
> | Ação | Microsoft.Devices/iotHubs/importDevices/Action | Importar dispositivos |
> | Ação | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Obter a chave IotHub para o nome fornecido |
> | Ação | Microsoft.Devices/iotHubs/iotHubStats/Read | Obter estatísticas do IotHub |
> | Ação | Microsoft.Devices/iotHubs/jobs/Read | Obter detalhes de trabalhos enviados em determinado IotHub |
> | Ação | Microsoft.Devices/iotHubs/listkeys/Action | Obter todas as chaves IotHub |
> | Ação | Microsoft.Devices/IotHubs/logDefinitions/read | Obter as definições de log disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/IotHubs/metricDefinitions/read | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft.Devices/iotHubs/operationresults/Read | Obter resultado da operação (API obsoleta) |
> | Ação | Microsoft.Devices/iotHubs/quotaMetrics/Read | Obter métricas de cota |
> | Ação | Microsoft.Devices/iotHubs/Read | Obter os recursos IotHub |
> | Ação | Microsoft.Devices/iotHubs/routing/$testall/Action | Testar uma mensagem em todas as rotas existentes |
> | Ação | Microsoft.Devices/iotHubs/routing/$testnew/Action | Testar uma mensagem em relação a uma rota de teste fornecida |
> | Ação | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Obtém a integridade de todos os pontos de extremidade de roteamento para um IotHub |
> | Ação | Microsoft. Devices/iotHubs/securitySettings/operationResults/leitura | Obter o resultado da operação assíncrona Put para SecuritySettings do Hub IOT |
> | Ação | Microsoft. Devices/iotHubs/securitySettings/ler | Obter as configurações da central de segurança do Azure no Hub IOT |
> | Ação | Microsoft. Devices/iotHubs/securitySettings/Write | Atualizar as configurações da central de segurança do Azure no Hub IOT |
> | Ação | Microsoft.Devices/iotHubs/skus/Read | Obter SKUs IotHub válidas |
> | Ação | Microsoft.Devices/iotHubs/Write | Criar ou atualizar recurso IotHub |
> | Ação | Microsoft.Devices/locations/operationresults/Read | Obter resultado da operação baseada na localização |
> | Ação | Microsoft.Devices/operationresults/Read | Obter resultado da operação |
> | Ação | Microsoft.Devices/operations/Read | Obter todas as operações resourceprovider |
> | Ação | Microsoft.Devices/provisioningServices/certificates/Delete | Exclui o certificado |
> | Ação | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft.Devices/provisioningServices/certificates/Read | Obtém o certificado |
> | Ação | Microsoft.Devices/provisioningServices/certificates/verify/Action | Verificar o recurso de certificado |
> | Ação | Microsoft.Devices/provisioningServices/certificates/Write | Criar ou atualizar certificado |
> | Ação | Microsoft.Devices/provisioningServices/Delete | Excluir recurso IotDps |
> | Ação | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Obter chaves IotDps para o nome da chave |
> | Ação | Microsoft.Devices/provisioningServices/listkeys/Action | Obter todas as chaves IotDps |
> | Ação | Microsoft.Devices/provisioningServices/logDefinitions/read | Obter as definições de log disponíveis para o serviço de provisionamento |
> | Ação | Microsoft.Devices/provisioningServices/metricDefinitions/read | Obter as métricas disponíveis para o serviço de provisionamento |
> | Ação | Microsoft.Devices/provisioningServices/operationresults/Read | Obter resultado da operação de DPS |
> | Ação | Microsoft.Devices/provisioningServices/Read | Obter recurso IotDps |
> | Ação | Microsoft.Devices/provisioningServices/skus/Read | Obter SKUs IotDps válidas |
> | Ação | Microsoft.Devices/provisioningServices/Write | Criar recurso IotDps |
> | Ação | Microsoft.Devices/register/action | Registrar a assinatura para o provedor de recursos IotHub e habilitar a criação de recursos IotHub |
> | Ação | Microsoft.Devices/usages/Read | Obter detalhes de uso da assinatura para este provedor. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DevSpaces/controllers/delete | Excluir Azure Dev Spaces controlador e os serviços de dataplane |
> | Ação | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Listar detalhes de conexão para a infraestrutura do controlador de Azure Dev Spaces |
> | Ação | Microsoft.DevSpaces/controllers/read | Ler Azure Dev Spaces Propriedades do controlador |
> | Ação | Microsoft.DevSpaces/controllers/write | Criar ou atualizar propriedades do controlador de Azure Dev Spaces |
> | Ação | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Verificar o mapeamento de controlador existente para um host de contêiner |
> | Ação | Microsoft.DevSpaces/locations/operationresults/read | Ler o status de uma operação assíncrona |
> | Ação | Microsoft.DevSpaces/register/action | Registrar o provedor de recursos do Microsoft dev Spaces com uma assinatura |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DevTestLab/labCenters/delete | Excluir centros de laboratório. |
> | Ação | Microsoft.DevTestLab/labCenters/read | Leia as centrais de laboratório. |
> | Ação | Microsoft.DevTestLab/labCenters/write | Adicionar ou modificar as centrais de laboratório. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Ler modelos do Azure Resource Manager. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Gera um modelo de Azure Resource Manager para o artefato fornecido, carrega os arquivos necessários para uma conta de armazenamento e valida o artefato gerado. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Ler artefatos. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/delete | Excluir fontes de artefato. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/read | Ler as fontes de artefato. |
> | Ação | Microsoft.DevTestLab/labs/artifactSources/write | Adicionar ou modificar fontes de artefato. |
> | Ação | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Declare uma máquina virtual de declaração aleatória no laboratório. |
> | Ação | Microsoft.DevTestLab/labs/costs/read | Leia os custos. |
> | Ação | Microsoft.DevTestLab/labs/costs/write | Adicionar ou modificar custos. |
> | Ação | Microsoft.DevTestLab/labs/CreateEnvironment/action | Crie máquinas virtuais em um laboratório. |
> | Ação | Microsoft.DevTestLab/labs/customImages/delete | Excluir imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/customImages/read | Ler imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/customImages/write | Adicionar ou modificar imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/delete | Excluir laboratórios. |
> | Ação | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Verifique se o usuário atual tem um perfil válido no laboratório. |
> | Ação | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exporta o uso de recursos de laboratório para uma conta de armazenamento |
> | Ação | Microsoft.DevTestLab/labs/formulas/delete | Excluir fórmulas. |
> | Ação | Microsoft.DevTestLab/labs/formulas/read | Ler fórmulas. |
> | Ação | Microsoft.DevTestLab/labs/formulas/write | Adicionar ou modificar fórmulas. |
> | Ação | Microsoft.DevTestLab/labs/galleryImages/read | Ler imagens da galeria. |
> | Ação | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Gere um URI para carregar imagens de disco personalizadas em um laboratório. |
> | Ação | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importe uma máquina virtual para um laboratório diferente. |
> | Ação | Microsoft.DevTestLab/labs/ListVhds/action | Listar imagens de disco disponíveis para a criação de imagens personalizadas. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/delete | Excluir canais de notificação. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Enviar notificação para o canal fornecido. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/read | Ler canais de notificação. |
> | Ação | Microsoft.DevTestLab/labs/notificationChannels/write | Adicionar ou modificar canais de notificação. |
> | Ação | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Avalia a política de laboratório. |
> | Ação | Microsoft.DevTestLab/labs/policySets/policies/delete | Excluir políticas. |
> | Ação | Microsoft.DevTestLab/labs/policySets/policies/read | Ler políticas. |
> | Ação | Microsoft.DevTestLab/labs/policySets/policies/write | Adicionar ou modificar políticas. |
> | Ação | Microsoft.DevTestLab/labs/policySets/read | Ler conjuntos de políticas. |
> | Ação | Microsoft.DevTestLab/labs/read | Leia os laboratórios. |
> | Ação | Microsoft.DevTestLab/labs/schedules/delete | Excluir agendas. |
> | Ação | Microsoft.DevTestLab/labs/schedules/Execute/action | Executar uma agenda. |
> | Ação | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Lista todas as agendas aplicáveis |
> | Ação | Microsoft.DevTestLab/labs/schedules/read | Ler agendas. |
> | Ação | Microsoft.DevTestLab/labs/schedules/write | Adicionar ou modificar agendas. |
> | Ação | Microsoft.DevTestLab/labs/serviceRunners/delete | Excluir executores de serviço. |
> | Ação | Microsoft.DevTestLab/labs/serviceRunners/read | Ler executores de serviço. |
> | Ação | Microsoft.DevTestLab/labs/serviceRunners/write | Adicionar ou modificar executores de serviço. |
> | Ação | Microsoft.DevTestLab/labs/sharedGalleries/delete | Excluir galerias compartilhadas. |
> | Ação | Microsoft.DevTestLab/labs/sharedGalleries/read | Ler galerias compartilhadas. |
> | Ação | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Excluir imagens compartilhadas. |
> | Ação | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Ler imagens compartilhadas. |
> | Ação | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Adicionar ou modificar imagens compartilhadas. |
> | Ação | Microsoft.DevTestLab/labs/sharedGalleries/write | Adicionar ou modificar galerias compartilhadas. |
> | Ação | Microsoft.DevTestLab/labs/users/delete | Excluir perfis de usuário. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/Attach/action | Anexe e crie a concessão do disco para a máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/delete | Excluir discos. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/Detach/action | Desanexe e quebre a concessão do disco anexado à máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/read | Ler discos. |
> | Ação | Microsoft.DevTestLab/labs/users/disks/write | Adicionar ou modificar discos. |
> | Ação | Microsoft.DevTestLab/labs/users/environments/delete | Excluir ambientes. |
> | Ação | Microsoft.DevTestLab/labs/users/environments/read | Ler ambientes. |
> | Ação | Microsoft.DevTestLab/labs/users/environments/write | Adicionar ou modificar ambientes. |
> | Ação | Microsoft.DevTestLab/labs/users/read | Ler perfis de usuário. |
> | Ação | Microsoft.DevTestLab/labs/users/secrets/delete | Exclua os segredos. |
> | Ação | Microsoft.DevTestLab/labs/users/secrets/read | Ler segredos. |
> | Ação | Microsoft.DevTestLab/labs/users/secrets/write | Adicionar ou modificar segredos. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Excluir malhas de serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Lista as agendas de início/parada aplicáveis, se houver. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Ler malhas de serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Excluir agendas. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Executar uma agenda. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Ler agendas. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Adicionar ou modificar agendas. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Inicie um Service Fabric. |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Parar um Service Fabric |
> | Ação | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Adicionar ou modificar malhas de serviço. |
> | Ação | Microsoft.DevTestLab/labs/users/write | Adicionar ou modificar perfis de usuário. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Anexe um disco de dados novo ou existente à máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Aplicar artefatos à máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Apropriar-se de uma máquina virtual existente |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/ClearArtifactResults/Action | Limpa os resultados de artefato da máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/delete | Excluir máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Desanexar o disco especificado da máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Obtém uma cadeia de caracteres que representa o conteúdo do arquivo RDP para a máquina virtual |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Lista as agendas de início/parada aplicáveis, se houver. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/read | Ler máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Reimplantar uma máquina virtual |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Redimensione a máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Reinicie uma máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Excluir agendas. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Executar uma agenda. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Ler agendas. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Adicionar ou modificar agendas. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Inicie uma máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Parar uma máquina virtual |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Transfere todos os discos de dados anexados à máquina virtual para que sejam de Propriedade do usuário atual. |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Liberar a propriedade de uma máquina virtual existente |
> | Ação | Microsoft.DevTestLab/labs/virtualMachines/write | Adicionar ou modificar máquinas virtuais. |
> | Ação | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Delete | Exclua bastionhosts. |
> | Ação | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Read | Leia bastionhosts. |
> | Ação | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Write | Adicionar ou modificar bastionhosts. |
> | Ação | Microsoft.DevTestLab/labs/virtualNetworks/delete | Excluir redes virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualNetworks/read | Ler redes virtuais. |
> | Ação | Microsoft.DevTestLab/labs/virtualNetworks/write | Adicionar ou modificar redes virtuais. |
> | Ação | Microsoft.DevTestLab/labs/vmPools/delete | Exclua os pools de máquina virtual. |
> | Ação | Microsoft.DevTestLab/labs/vmPools/read | Ler pools de máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/vmPools/write | Adicionar ou modificar pools de máquinas virtuais. |
> | Ação | Microsoft.DevTestLab/labs/write | Adicionar ou modificar laboratórios. |
> | Ação | Microsoft.DevTestLab/locations/operations/read | Operações de leitura. |
> | Ação | Microsoft.DevTestLab/register/action | Registra a assinatura |
> | Ação | Microsoft.DevTestLab/schedules/delete | Excluir agendas. |
> | Ação | Microsoft.DevTestLab/schedules/Execute/action | Executar uma agenda. |
> | Ação | Microsoft.DevTestLab/schedules/read | Ler agendas. |
> | Ação | Microsoft.DevTestLab/schedules/Retarget/action | Atualiza a ID de recurso de destino de um agendamento. |
> | Ação | Microsoft.DevTestLab/schedules/write | Adicionar ou modificar agendas. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DocumentDB/databaseAccountNames/read | Verifica a disponibilidade do nome. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | Excluir uma coleção. Aplicável somente a tipos de API: ' MongoDB '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' MongoDB '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | Ler uma coleção ou listar todas as coleções. Aplicável somente a tipos de API: ' MongoDB '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' MongoDB '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | Ler uma taxa de transferência de coleção. Aplicável somente a tipos de API: ' MongoDB '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | Atualizar uma taxa de transferência de coleção. Aplicável somente a tipos de API: ' MongoDB '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | Criar ou atualizar uma coleção. Aplicável somente a tipos de API: ' MongoDB '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | Excluir um contêiner. Aplicável somente a tipos de API: ' SQL '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | Ler um contêiner ou listar todos os contêineres. Aplicável somente a tipos de API: ' SQL '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | Ler uma taxa de transferência de contêiner. Aplicável somente a tipos de API: ' SQL '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | Atualizar uma taxa de transferência de contêiner. Aplicável somente a tipos de API: ' SQL '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | Criar ou atualizar um contêiner. Aplicável somente a tipos de API: ' SQL '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | Excluir um banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Excluir um grafo. Aplicável somente a tipos de API: ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Ler um grafo ou listar todos os grafos. Aplicável somente a tipos de API: ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | Ler uma taxa de transferência de grafo. Aplicável somente a tipos de API: ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | Atualizar uma taxa de transferência de grafo. Aplicável somente a tipos de API: ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Criar ou atualizar um grafo. Aplicável somente a tipos de API: ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Ler um banco de dados ou listar todos os bancos. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | Ler uma taxa de transferência de banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | Atualizar uma taxa de transferência de banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Crie um banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Excluir um keyspace. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | Ler um keyspace ou listar todos os keyspaces. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | Ler uma taxa de transferência de keyspace. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | Atualize uma taxa de transferência de keyspace. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Excluir uma tabela. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Ler uma tabela ou listar todas as tabelas. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | Ler uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | Atualize uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Criar ou atualizar uma tabela. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Crie um keyspace. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Excluir uma tabela. Aplicável somente a tipos de API: ' Table '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Table '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Ler uma tabela ou listar todas as tabelas. Aplicável somente a tipos de API: ' Table '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Table '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | Ler uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Table '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | Atualize uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Table '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Criar ou atualizar uma tabela. Aplicável somente a tipos de API: ' Table '. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/backup/action | Enviar uma solicitação para configurar o backup |
> | Ação | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Alterar grupo de recursos de uma conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Lê as definições de métrica da coleção. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Lê as métricas da coleção. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Ler métricas de nível de chave de partição de conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Ler métricas de nível de partição de conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Ler partições de conta de banco de dados em uma coleção |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Ler usos de nível de partição de conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Lê os usos da coleção. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Lê as definições de métricas de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Lê as métricas do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Lê os usos do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/delete | Exclui as contas de banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Alterar as prioridades de failover de regiões de uma conta de banco de dados. Isso é usado para executar a operação de failover manual |
> | Ação | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/Action | Obter a política de backup da conta do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Obter as cadeias de conexão para uma conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Listar chaves de uma conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Lê as definições de métricas da conta de banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/metrics/read | Lê as métricas da conta do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Offline uma região de uma conta de banco de dados.  |
> | Ação | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Online uma região de uma conta de banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Ler o status da operação assíncrona |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Métricas de latência de leitura |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/read | Ler percentuais de latências de replicação |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Ler métricas de latência para uma região específica de origem e de destino |
> | Ação | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Ler métricas de latência para uma região de destino específica |
> | Ação | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Excluir um proxy de conexão de ponto de extremidade privado da conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/read | Ler o status da operação assíncrona do proxy de conexão de ponto de extremidade privado |
> | Ação | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Ler um proxy de conexão de ponto de extremidade privado da conta do banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Validar um proxy de conexão de ponto de extremidade privado da conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Criar ou atualizar um proxy de conexão de ponto de extremidade privado da conta do banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Delete | Excluir uma conexão de ponto de extremidade privada de uma conta de banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/operationResults/Read | Ler o status da operação assíncrona privateEndpointConnenctions |
> | Ação | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Read | Ler uma conexão de ponto de extremidade particular ou listar todas as conexões de ponto de extremidade privado de uma conta de banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Write | Criar ou atualizar uma conexão de ponto de extremidade privada de uma conta de banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/privateLinkResources/Read | Ler um recurso de link privado ou listar todos os recursos de link privado de uma conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/read | Lê uma conta de banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Lê as chaves somente leitura da conta do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Lê as chaves somente leitura da conta do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Girar as chaves de uma conta de banco de dados |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Lê as métricas da coleção regional. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Ler métricas de nível de chave de partição de conta de banco de dados regional |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Ler métricas de nível de partição da conta do banco de dados regional |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Ler partições de conta de banco de dados regional em uma coleção |
> | Ação | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Lê as métricas de conta de banco de dados e região. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/restore/action | Enviar uma solicitação de restauração |
> | Ação | Microsoft.DocumentDB/databaseAccounts/usages/read | Lê os usos da conta do banco de dados. |
> | Ação | Microsoft.DocumentDB/databaseAccounts/write | Atualizar contas de banco de dados. |
> | Ação | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft. DocumentDB que o VirtualNetwork ou a sub-rede está sendo excluída |
> | Ação | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Ler o status da operação assíncrona deleteVirtualNetworkOrSubnets |
> | Ação | Microsoft.DocumentDB/locations/operationsStatus/read | Lê o status de operações assíncronas |
> | Ação | Microsoft.DocumentDB/operationResults/read | Ler o status da operação assíncrona |
> | Ação | Microsoft.DocumentDB/operations/read | Operações de leitura disponíveis para o Microsoft DocumentDB  |
> | Ação | Microsoft.DocumentDB/register/action |  Registrar o provedor de recursos do Microsoft DocumentDB para a assinatura |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.DomainRegistration/checkDomainAvailability/Action | Verificar se um domínio está disponível para compra |
> | Ação | Microsoft.DomainRegistration/domains/Delete | Excluir um domínio existente. |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Excluir identificador de propriedade |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Listar identificadores de propriedade |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Obter identificador de propriedade |
> | Ação | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Criar ou atualizar identificador |
> | Ação | Microsoft.DomainRegistration/domains/operationresults/Read | Obter uma operação de domínio |
> | Ação | Microsoft.DomainRegistration/domains/Read | Obter a lista de domínios |
> | Ação | Microsoft.DomainRegistration/domains/Read | Obter domínio |
> | Ação | Microsoft.DomainRegistration/domains/renew/Action | Renovar um domínio existente. |
> | Ação | Microsoft.DomainRegistration/domains/Write | Adicionar um novo domínio ou atualizar um existente |
> | Ação | Microsoft.DomainRegistration/generateSsoRequest/Action | Gerar uma solicitação para entrar no centro de controle de domínio. |
> | Ação | Microsoft.DomainRegistration/listDomainRecommendations/Action | Recuperar as recomendações de domínio da lista com base em palavras-chave |
> | Ação | Microsoft.DomainRegistration/operations/Read | Listar todas as operações do registro de domínio do serviço de aplicativo |
> | Ação | Microsoft.DomainRegistration/register/action | Registrar o provedor de recursos de domínios Microsoft para a assinatura |
> | Ação | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Listar ação de contrato |
> | Ação | Microsoft.DomainRegistration/topLevelDomains/Read | Obter domínios do TopLevel |
> | Ação | Microsoft.DomainRegistration/topLevelDomains/Read | Obter domínio TopLevel |
> | Ação | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Validar o objeto de compra de domínio sem enviá-lo |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.EventGrid/domains/delete | Excluir um domínio |
> | Ação | Microsoft.EventGrid/domains/listKeys/action | Listar chaves para um domínio |
> | Ação | Microsoft. EventGrid/Domains/Providers/Microsoft. insights/logDefinitions/Read | Permite o acesso aos logs de diagnóstico |
> | Ação | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para domínios |
> | Ação | Microsoft.EventGrid/domains/read | Ler um domínio |
> | Ação | Microsoft.EventGrid/domains/regenerateKey/action | Regenerar chave para um domínio |
> | Ação | Microsoft. EventGrid/Domains/topics/Delete | Tópico excluir um domínio |
> | Ação | Microsoft.EventGrid/domains/topics/read | Ler um tópico de domínio |
> | Ação | Microsoft. EventGrid/domínios/tópicos/gravação | Criar ou atualizar um tópico de domínio |
> | Ação | Microsoft.EventGrid/domains/write | Criar ou atualizar um domínio |
> | Ação | Microsoft.EventGrid/eventSubscriptions/delete | Excluir um eventSubscription |
> | Ação | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Obter URL completa para a assinatura de evento |
> | Ação | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para assinaturas de evento |
> | Ação | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para assinaturas de evento |
> | Ação | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para eventSubscriptions |
> | Ação | Microsoft.EventGrid/eventSubscriptions/read | Ler um eventSubscription |
> | Ação | Microsoft.EventGrid/eventSubscriptions/write | Criar ou atualizar um eventSubscription |
> | Ação | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico para tópicos |
> | Ação | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para tópicos |
> | Ação | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para tópicos |
> | Ação | Microsoft.EventGrid/extensionTopics/read | Ler um extensionTopic. |
> | Ação | Microsoft.EventGrid/locations/eventSubscriptions/read | Listar assinaturas de eventos regionais |
> | Ação | Microsoft.EventGrid/locations/operationResults/read | Ler o resultado de uma operação regional |
> | Ação | Microsoft.EventGrid/locations/operationsStatus/read | Ler o status de uma operação regional |
> | Ação | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Listar assinaturas de evento regional por topictype |
> | Ação | Microsoft.EventGrid/operationResults/read | Ler o resultado de uma operação |
> | Ação | Microsoft.EventGrid/operations/read | Liste as operações EventGrid. |
> | Ação | Microsoft.EventGrid/operationsStatus/read | Ler o status de uma operação |
> | Ação | Microsoft.EventGrid/register/action | Registra a assinatura para o provedor de recursos EventGrid. |
> | Ação | Microsoft.EventGrid/topics/delete | Excluir um tópico |
> | Ação | Microsoft.EventGrid/topics/listKeys/action | Listar chaves para um tópico |
> | Ação | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico para tópicos |
> | Ação | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Criar ou atualizar a configuração de diagnóstico para tópicos |
> | Ação | Microsoft. EventGrid/tópicos/Providers/Microsoft. insights/logDefinitions/Read | Permite o acesso aos logs de diagnóstico |
> | Ação | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para tópicos |
> | Ação | Microsoft.EventGrid/topics/read | Ler um tópico |
> | Ação | Microsoft.EventGrid/topics/regenerateKey/action | Regenerar chave para um tópico |
> | Ação | Microsoft.EventGrid/topics/write | Criar ou atualizar um tópico |
> | Ação | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Listar assinaturas de evento global por tipo de tópico |
> | Ação | Microsoft.EventGrid/topictypes/eventtypes/read | Ler EventTypes com suporte de um topictype |
> | Ação | Microsoft.EventGrid/topictypes/read | Ler um topictype |
> | Ação | Microsoft.EventGrid/unregister/action | Cancela o registro da assinatura para o provedor de recursos EventGrid. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.EventHub/availableClusterRegions/read | Operação de leitura para listar clusters pré-configurados disponíveis pela região do Azure. |
> | Ação | Microsoft.EventHub/checkNameAvailability/action | Verifica a disponibilidade do namespace sob determinada assinatura. |
> | Ação | Microsoft.EventHub/checkNamespaceAvailability/action | Verifica a disponibilidade do namespace sob determinada assinatura. Esta API foi preterida, use CheckNameAvailability em vez disso. |
> | Ação | Microsoft.EventHub/clusters/delete | Exclui um recurso de cluster existente. |
> | Ação | Microsoft.EventHub/clusters/namespaces/read | Listar IDs de Azure Resource Manager de namespace para namespaces em um cluster. |
> | Ação | Microsoft.EventHub/clusters/operationresults/read | Obtenha o status de uma operação de cluster assíncrona. |
> | Ação | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de descrições de recursos de métricas de cluster |
> | Ação | Microsoft.EventHub/clusters/read | Obtém a descrição do recurso de cluster |
> | Ação | Microsoft.EventHub/clusters/write | Cria ou modifica um recurso de cluster existente. |
> | Ação | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Exclui as regras de VNet no provedor de recursos do EventHub para a VNet especificada |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/action | Regra de autorização de namespace de atualizações. Esta API foi preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace em vez disso. Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização de namespace padrão não pode ser excluída.  |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/read | Obtenha a lista de descrições de regras de autorização de namespaces. |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.EventHub/namespaces/authorizationRules/write | Crie regras de autorização de nível de namespace e atualize suas propriedades. Os direitos de acesso às regras de autorização, as chaves primária e secundária podem ser atualizadas. |
> | Ação | Microsoft.EventHub/namespaces/Delete | Excluir recurso de namespace |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obter as chaves de regras de autorização para o namespace primário de recuperação de desastre |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter regras de autorização do namespace primário de recuperação de desastre |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Desabilita a recuperação de desastres e interrompe a replicação de alterações de namespaces primários para secundários. |
> | Ação | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica a disponibilidade do alias de namespace na assinatura fornecida. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Exclui a configuração de recuperação de desastre associada ao namespace. Esta operação só pode ser invocada por meio do namespace primário. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Invoca um failover de DR geográfica e reconfigura o alias de namespace para apontar para o namespace secundário. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Obtém a configuração de recuperação de desastre associada ao namespace. |
> | Ação | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Cria ou atualiza a configuração de recuperação de desastre associada ao namespace. |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Operação para atualizar o EventHub. Não há suporte para esta operação na versão de API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Operação para excluir regras de autorização do EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Obter a cadeia de conexão para o EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Obter a lista de regras de autorização do EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Crie regras de autorização do EventHub e atualize suas propriedades. Os direitos de acesso das regras de autorização podem ser atualizados. |
> | Ação | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Operação para excluir o recurso de um consumidor |
> | Ação | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Obter lista de descrições de recursos do The Consumership |
> | Ação | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Crie ou atualize as propriedades de um dos consumidores. |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/Delete | Operação para excluir o recurso do EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/read | Obter lista de descrições de recursos do EventHub |
> | Ação | Microsoft.EventHub/namespaces/eventhubs/write | Criar ou atualizar propriedades do EventHub. |
> | Ação | Microsoft.EventHub/namespaces/ipFilterRules/delete | Excluir recurso de filtro IP |
> | Ação | Microsoft.EventHub/namespaces/ipFilterRules/read | Obter recurso de filtro IP |
> | Ação | Microsoft.EventHub/namespaces/ipFilterRules/write | Criar recurso de filtro IP |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | Receber mensagens |
> | DataAction | Microsoft.EventHub/namespaces/messages/send/action | Enviar mensagens |
> | Ação | Microsoft.EventHub/namespaces/messagingPlan/read | Obtém o plano de mensagens para um namespace.<br>Esta API foi preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso de namespace (pai) em versões de API posteriores.<br>Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft.EventHub/namespaces/messagingPlan/write | Atualiza o plano de mensagens para um namespace.<br>Esta API foi preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso de namespace (pai) em versões de API posteriores.<br>Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft. EventHub/namespaces/networkruleset/Delete | Excluir recurso de regra de VNET |
> | Ação | Microsoft. EventHub/namespaces/networkruleset/Read | Obtém o recurso NetworkRuleSet |
> | Ação | Microsoft. EventHub/namespaces/networkruleset/Write | Criar recurso de regra de VNET |
> | Ação | Microsoft.EventHub/namespaces/networkrulesets/delete | Excluir recurso de regra de VNET |
> | Ação | Microsoft.EventHub/namespaces/networkrulesets/read | Obtém o recurso NetworkRuleSet |
> | Ação | Microsoft.EventHub/namespaces/networkrulesets/write | Criar recurso de regra de VNET |
> | Ação | Microsoft.EventHub/namespaces/operationresults/read | Obter o status da operação de namespace |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obter lista de descrições de recursos das configurações de diagnóstico de namespace |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obter lista de descrições de recursos das configurações de diagnóstico de namespace |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obter lista de descrições de recursos de logs de namespace |
> | Ação | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de descrições de recursos de métricas de namespace |
> | Ação | Microsoft.EventHub/namespaces/read | Obter a lista de descrição de recurso de namespace |
> | Ação | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Remover namespace do ACS |
> | Ação | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Excluir recurso de regra de VNET |
> | Ação | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Obter recurso de regra de VNET |
> | Ação | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Criar recurso de regra de VNET |
> | Ação | Microsoft.EventHub/namespaces/write | Crie um recurso de namespace e atualize suas propriedades. As marcas e a capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft.EventHub/operations/read | Obter operações |
> | Ação | Microsoft.EventHub/register/action | Registra a assinatura para o provedor de recursos do EventHub e habilita a criação de recursos do EventHub |
> | Ação | Microsoft.EventHub/sku/read | Obter lista de descrições de recursos de SKU |
> | Ação | Microsoft.EventHub/sku/regions/read | Obter lista de descrições de recursos SkuRegions |
> | Ação | Microsoft.EventHub/unregister/action | Registra o provedor de recursos do EventHub |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Features/features/read | Obtém os recursos de uma assinatura. |
> | Ação | Microsoft.Features/operations/read | Obtém a lista de operações. |
> | Ação | Microsoft.Features/providers/features/read | Obtém o recurso de uma assinatura em um determinado provedor de recursos. |
> | Ação | Microsoft.Features/providers/features/register/action | Registra o recurso para uma assinatura em um determinado provedor de recursos. |
> | Ação | Microsoft.Features/providers/features/unregister/action | Cancela o registro do recurso para uma assinatura em um determinado provedor de recursos. |
> | Ação | Microsoft.Features/register/action | Registra o recurso de uma assinatura. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | Excluir atribuição de configuração de convidado. |
> | Ação | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Obter atribuição de configuração de convidado. |
> | Ação | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Obter relatório de atribuição de configuração de convidado. |
> | Ação | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Criar nova atribuição de configuração de convidado. |
> | Ação | Microsoft. GuestConfiguration/operações/leitura | Obtém as operações para o provedor de recursos Microsoft. GuestConfiguration |
> | Ação | Microsoft.GuestConfiguration/register/action | Registra a assinatura para o provedor de recursos Microsoft. GuestConfiguration. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.HDInsight/clusters/applications/delete | Excluir aplicativo para o cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/applications/read | Obter aplicativo para o cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/applications/write | Criar ou atualizar aplicativo para o cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/changerdpsetting/action | Alterar configuração de RDP para cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/configurations/action | Atualizar a configuração do cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/configurations/action | Obter configurações de cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/configurations/read | Obter configurações de cluster do HDInsight |
> | Ação | Microsoft.HDInsight/clusters/delete | Excluir um cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/extensões/excluir | Excluir a extensão de cluster do cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/extensões/leitura | Obter a extensão do cluster para o cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/extensões/gravação | Criar extensão de cluster para o cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/getGatewaySettings/action | Obter configurações de gateway para o cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Obter a configuração de diagnóstico para o cluster HDInsight de recursos |
> | Ação | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o cluster HDInsight de recurso |
> | Ação | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/read | Obter detalhes sobre o cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/roles/resize/action | Redimensionar um cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Atualizar as configurações do gateway para o cluster HDInsight |
> | Ação | Microsoft.HDInsight/clusters/write | Criar ou atualizar o cluster HDInsight |
> | Ação | Microsoft.HDInsight/locations/capabilities/read | Obter recursos de assinatura |
> | Ação | Microsoft.HDInsight/locations/checkNameAvailability/read | Verificar a disponibilidade do nome |
> | Ação | Microsoft. HDInsight/registro/ação | Registrar o provedor de recursos do HDInsight para a assinatura |
> | Ação | Microsoft. HDInsight/cancelar registro/ação | Cancelar registro do provedor de recursos do HDInsight para a assinatura |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ImportExport/jobs/delete | Exclui um trabalho existente. |
> | Ação | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Obtém as chaves do BitLocker para o trabalho especificado. |
> | Ação | Microsoft.ImportExport/jobs/read | Obtém as propriedades do trabalho especificado ou retorna a lista de trabalhos. |
> | Ação | Microsoft.ImportExport/jobs/write | Cria um trabalho com os parâmetros especificados ou atualiza as propriedades ou marcas para o trabalho especificado. |
> | Ação | Microsoft.ImportExport/locations/read | Obtém as propriedades para o local especificado ou retorna a lista de locais. |
> | Ação | Microsoft. ImportExport/operações/leitura | Obtém as operações com suporte do provedor de recursos. |
> | Ação | Microsoft.ImportExport/register/action | Registra a assinatura para o provedor de recursos de importação/exportação e habilita a criação de trabalhos de importação/exportação. |

## <a name="microsoftinsights"></a>Microsoft. insights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Insights/ActionGroups/Delete | Excluir um grupo de ações |
> | Ação | Microsoft.Insights/ActionGroups/Read | Ler um grupo de ação |
> | Ação | Microsoft.Insights/ActionGroups/Write | Criar ou atualizar um grupo de ações |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Alerta do log de atividades ativado |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Delete | Excluir um alerta do log de atividades |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Read | Ler um alerta do log de atividades |
> | Ação | Microsoft.Insights/ActivityLogAlerts/Write | Criar ou atualizar um alerta do log de atividades |
> | Ação | Microsoft.Insights/AlertRules/Activated/Action | Alerta de métrica clássico ativado |
> | Ação | Microsoft.Insights/AlertRules/Delete | Excluir um alerta de métrica clássico |
> | Ação | Microsoft.Insights/AlertRules/Incidents/Read | Ler um incidente de alerta de métrica clássico |
> | Ação | Microsoft.Insights/AlertRules/Read | Ler um alerta de métrica clássico |
> | Ação | Microsoft.Insights/AlertRules/Resolved/Action | Alerta de métrica clássico resolvido |
> | Ação | Microsoft.Insights/AlertRules/Throttled/Action | Regra de alerta de métrica clássica limitada |
> | Ação | Microsoft.Insights/AlertRules/Write | Criar ou atualizar um alerta de métrica clássico |
> | Ação | Microsoft.Insights/AutoscaleSettings/Delete | Excluir uma configuração de dimensionamento automático |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Ler uma configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Criar ou atualizar uma configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Ler definições de log |
> | Ação | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/AutoscaleSettings/Read | Ler uma configuração de dimensionamento automático |
> | Ação | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Redução de escala automática iniciada |
> | Ação | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Redução de escala automática concluída |
> | Ação | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Escala vertical redimensionada iniciada |
> | Ação | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Dimensionamento automático dimensionado concluído |
> | Ação | Microsoft.Insights/AutoscaleSettings/Write | Criar ou atualizar uma configuração de dimensionamento automático |
> | Ação | Microsoft.Insights/Baseline/Read | Ler uma linha de base de métrica (versão prévia) |
> | Ação | Microsoft.Insights/CalculateBaseline/Read | Calcular a linha de base para valores de métrica (versão prévia) |
> | Ação | Microsoft.Insights/Components/AnalyticsItems/Delete | Excluindo um item de análise de Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsItems/Read | Lendo um item de análise de Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsItems/Write | Escrevendo um item de análise de Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Action | Ação da tabela de análise de Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Delete | Excluindo um esquema de tabela de análise de Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Read | Lendo um esquema de tabela de análise de Application Insights |
> | Ação | Microsoft.Insights/Components/AnalyticsTables/Write | Escrevendo um esquema de tabela de análise de Application Insights |
> | Ação | Microsoft.Insights/Components/Annotations/Delete | Excluindo uma anotação de Application Insights |
> | Ação | Microsoft.Insights/Components/Annotations/Read | Lendo uma anotação de Application Insights |
> | Ação | Microsoft.Insights/Components/Annotations/Write | Escrevendo uma anotação de Application Insights |
> | Ação | Microsoft.Insights/Components/Api/Read | Lendo Application Insights API de dados de componente |
> | Ação | Microsoft.Insights/Components/ApiKeys/Action | Gerando uma chave de API de Application Insights |
> | Ação | Microsoft.Insights/Components/ApiKeys/Delete | Excluindo uma chave de API de Application Insights |
> | Ação | Microsoft.Insights/Components/ApiKeys/Read | Lendo uma chave de API de Application Insights |
> | Ação | Microsoft.Insights/Components/BillingPlanForComponent/Read | Lendo um plano de cobrança para o componente Application Insights |
> | Ação | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Lendo os recursos de cobrança atuais para o componente Application Insights |
> | Ação | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Escrevendo recursos de cobrança atuais para o componente Application Insights |
> | Ação | Microsoft. insights/componentes/DailyCapReached/Action | Atingido o limite diário para o componente Application Insights |
> | Ação | Microsoft. insights/componentes/DailyCapWarningThresholdReached/Action | Limite de aviso de limite diário atingido para o componente Application Insights |
> | Ação | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Lendo uma configuração de integração de ALM Application Insights padrão |
> | Ação | Microsoft.Insights/Components/Delete | Excluindo uma configuração de componente do Application insights |
> | Ação | Microsoft.Insights/Components/Events/Read | Obter logs de Application Insights usando o formato de consulta OData |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Action | Ação Application Insights exportar configurações |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Delete | Excluindo Application Insights configurações de exportação |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Read | Lendo Application Insights configurações de exportação |
> | Ação | Microsoft.Insights/Components/ExportConfiguration/Write | Gravando Application Insights configurações de exportação |
> | Ação | Microsoft.Insights/Components/ExtendQueries/Read | Lendo Application Insights resultados da consulta estendida do componente |
> | Ação | Microsoft.Insights/Components/Favorites/Delete | Excluindo um Application Insights favorito |
> | Ação | Microsoft.Insights/Components/Favorites/Read | Lendo um Application Insights favorito |
> | Ação | Microsoft.Insights/Components/Favorites/Write | Escrevendo um Application Insights favorito |
> | Ação | Microsoft.Insights/Components/FeatureCapabilities/Read | Lendo recursos de recurso de componente Application Insights |
> | Ação | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Lendo os recursos de cobrança disponíveis do componente Application Insights |
> | Ação | Microsoft.Insights/Components/GetToken/Read | Lendo um token de componente de Application Insights |
> | Ação | Microsoft.Insights/Components/MetricDefinitions/Read | Lendo definições de métrica de componente Application Insights |
> | Ação | Microsoft.Insights/Components/Metrics/Read | Lendo Application Insights métricas de componente |
> | Ação | Microsoft.Insights/Components/Move/Action | Mover um componente Application Insights para outro grupo de recursos ou assinatura |
> | Ação | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Excluindo um Application Insights item de análise pessoal |
> | Ação | Microsoft.Insights/Components/MyAnalyticsItems/Read | Lendo um Application Insights item de análise pessoal |
> | Ação | Microsoft.Insights/Components/MyAnalyticsItems/Write | Escrevendo um item de análise pessoal de Application Insights |
> | Ação | Microsoft.Insights/Components/MyFavorites/Read | Lendo um Application Insights favorito pessoal |
> | Ação | Microsoft.Insights/Components/Operations/Read | Obter o status das operações de execução longa no Application Insights |
> | Ação | Microsoft.Insights/Components/PricingPlans/Read | Lendo um plano de preços do componente Application Insights |
> | Ação | Microsoft.Insights/Components/PricingPlans/Write | Escrevendo um plano de preços de componente Application Insights |
> | Ação | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Lendo Application Insights configuração de detecção proativa |
> | Ação | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Gravando Application Insights configuração de detecção proativa |
> | Ação | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/Components/Purge/Action | Limpando dados de Application Insights |
> | Ação | Microsoft.Insights/Components/Query/Read | Executar consultas em logs de Application Insights |
> | Ação | Microsoft.Insights/Components/QuotaStatus/Read | Lendo Application Insights status de cota do componente |
> | Ação | Microsoft.Insights/Components/Read | Lendo uma configuração de componente do Application insights |
> | Ação | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Lendo Application Insights locais de WebTest |
> | Ação | Microsoft.Insights/Components/Webtests/Read | Lendo uma configuração de WebTest |
> | Ação | Microsoft.Insights/Components/WorkItemConfigs/Delete | Excluindo uma configuração de integração do ALM Application Insights |
> | Ação | Microsoft.Insights/Components/WorkItemConfigs/Read | Lendo uma configuração de integração do ALM Application Insights |
> | Ação | Microsoft.Insights/Components/WorkItemConfigs/Write | Escrevendo uma configuração de integração do ALM Application Insights |
> | Ação | Microsoft.Insights/Components/Write | Gravando em uma configuração de componente do Application insights |
> | Ação | Microsoft. insights/DataCollectionRuleAssociations/Delete | Excluir a associação de um recurso com uma regra de coleta de dados |
> | Ação | Microsoft. insights/DataCollectionRuleAssociations/Read | Ler a associação de um recurso com uma regra de coleta de dados |
> | Ação | Microsoft. insights/DataCollectionRuleAssociations/Write | Criar ou atualizar a associação de um recurso com uma regra de coleta de dados |
> | DataAction | Microsoft. insights/DataCollectionRules/data/Write | Enviar dados para uma regra de coleta de dados |
> | Ação | Microsoft. insights/DataCollectionRules/Delete | Excluir uma regra de coleta de dados |
> | Ação | Microsoft. insights/DataCollectionRules/Read | Ler uma regra de coleta de dados |
> | Ação | Microsoft. insights/DataCollectionRules/Write | Criar ou atualizar uma regra de coleta de dados |
> | Ação | Microsoft.Insights/DiagnosticSettings/Delete | Excluir uma configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/DiagnosticSettings/Read | Ler uma configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/DiagnosticSettings/Write | Criar ou atualizar uma configuração de diagnóstico de recurso |
> | Ação | Microsoft.Insights/EventCategories/Read | Ler categorias de eventos do log de atividades disponíveis |
> | Ação | Microsoft.Insights/eventtypes/digestevents/Read | Ler Resumo do tipo de evento de gerenciamento |
> | Ação | Microsoft.Insights/eventtypes/values/Read | Ler eventos do log de atividades |
> | Ação | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Excluir uma configuração de diagnóstico de log de fluxo de rede |
> | Ação | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Ler uma configuração de diagnóstico do log de fluxo de rede |
> | Ação | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Criar ou atualizar uma configuração de diagnóstico de log de fluxo de rede |
> | Ação | Microsoft.Insights/ListMigrationDate/Action | Obter a data de migração da assinatura |
> | Ação | Microsoft.Insights/ListMigrationDate/Read | Obter a data de migração da assinatura |
> | Ação | Microsoft.Insights/LogDefinitions/Read | Ler definições de log |
> | Ação | Microsoft.Insights/LogProfiles/Delete | Excluir um perfil de log de atividades |
> | Ação | Microsoft.Insights/LogProfiles/Read | Ler um perfil de log de atividades |
> | Ação | Microsoft.Insights/LogProfiles/Write | Criar ou atualizar um perfil de log de atividades |
> | Ação | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Ler dados da tabela ADAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/ADReplicationResult/Read | Ler dados da tabela ADReplicationResult |
> | Ação | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Ler dados da tabela ADSecurityAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/Alert/Read | Ler dados da tabela de alertas |
> | Ação | Microsoft.Insights/Logs/AlertHistory/Read | Ler dados da tabela AlertHistory |
> | Ação | Microsoft.Insights/Logs/ApplicationInsights/Read | Ler dados da tabela ApplicationInsights |
> | Ação | Microsoft.Insights/Logs/AzureActivity/Read | Ler dados da tabela AzureActivity |
> | Ação | Microsoft.Insights/Logs/AzureMetrics/Read | Ler dados da tabela AzureMetrics |
> | Ação | Microsoft.Insights/Logs/BoundPort/Read | Ler dados da tabela BoundPort |
> | Ação | Microsoft.Insights/Logs/CommonSecurityLog/Read | Ler dados da tabela CommonSecurityLog |
> | Ação | Microsoft.Insights/Logs/ComputerGroup/Read | Ler dados da tabela do computador |
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
> | Ação | Microsoft.Insights/Logs/Event/Read | Ler dados da tabela de eventos |
> | Ação | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Ler dados da tabela ExchangeAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Ler dados da tabela ExchangeOnlineAssessmentRecommendation |
> | Ação | Microsoft.Insights/Logs/Heartbeat/Read | Ler dados da tabela de pulsação |
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
> | Ação | Microsoft.Insights/Logs/Operation/Read | Ler dados da tabela de operações |
> | Ação | Microsoft.Insights/Logs/OutboundConnection/Read | Ler dados da tabela OutboundConnection |
> | Ação | Microsoft.Insights/Logs/Perf/Read | Ler dados da tabela perf |
> | Ação | Microsoft.Insights/Logs/ProtectionStatus/Read | Ler dados da tabela ProtectionStatus |
> | Ação | Microsoft.Insights/Logs/Read | Lendo dados de todos os seus logs |
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
> | Ação | Microsoft.Insights/Logs/Syslog/Read | Ler dados da tabela syslog |
> | Ação | Microsoft.Insights/Logs/SysmonEvent/Read | Ler dados da tabela SysmonEvent |
> | Ação | Microsoft. insights/logs/tabelas. personalizado/lido | Lendo dados de qualquer log personalizado |
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
> | Ação | Microsoft.Insights/Logs/Update/Read | Ler dados da tabela de atualização |
> | Ação | Microsoft.Insights/Logs/UpdateRunProgress/Read | Ler dados da tabela UpdateRunProgress |
> | Ação | Microsoft.Insights/Logs/UpdateSummary/Read | Ler dados da tabela UpdateSummary |
> | Ação | Microsoft.Insights/Logs/Usage/Read | Ler dados da tabela de uso |
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
> | Ação | Microsoft.Insights/MetricAlerts/Delete | Excluir um alerta de métrica |
> | Ação | Microsoft.Insights/MetricAlerts/Read | Ler um alerta de métrica |
> | Ação | Microsoft.Insights/MetricAlerts/Status/Read | Ler status de alerta de métrica |
> | Ação | Microsoft.Insights/MetricAlerts/Write | Criar ou atualizar um alerta de métrica |
> | Ação | Microsoft.Insights/MetricBaselines/Read | Ler linhas de base de métrica |
> | Ação | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/MetricDefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft.Insights/Metricnamespaces/Read | Ler namespaces de métrica |
> | Ação | Microsoft.Insights/Metrics/Action | Ação de métrica |
> | Ação | Microsoft.Insights/Metrics/Microsoft.Insights/Read | Ler métricas |
> | Ação | Microsoft.Insights/Metrics/providers/Metrics/Read | Ler métricas |
> | Ação | Microsoft.Insights/Metrics/Read | Ler métricas |
> | DataAction | Microsoft.Insights/Metrics/Write | Métricas de gravação |
> | Ação | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrar assinatura para novo modelo de preços |
> | Ação | Microsoft.Insights/Operations/Read | Operações de leitura |
> | Ação | Microsoft.Insights/Register/Action | Registrar o provedor do Microsoft insights |
> | Ação | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Reverter assinatura para modelo de preço herdado |
> | Ação | Microsoft.Insights/ScheduledQueryRules/Delete | Excluindo uma regra de consulta agendada |
> | Ação | Microsoft.Insights/ScheduledQueryRules/Read | Lendo uma regra de consulta agendada |
> | Ação | Microsoft.Insights/ScheduledQueryRules/Write | Escrevendo uma regra de consulta agendada |
> | Ação | Microsoft.Insights/Tenants/Register/Action | Inicializa o provedor do Microsoft insights |
> | Ação | Microsoft.Insights/Unregister/Action | Registrar o provedor do Microsoft insights |
> | Ação | Microsoft.Insights/Webtests/Delete | Excluindo uma configuração de WebTest |
> | Ação | Microsoft.Insights/Webtests/GetToken/Read | Lendo um token do WebTest |
> | Ação | Microsoft.Insights/Webtests/MetricDefinitions/Read | Lendo definições de métrica de WebTest |
> | Ação | Microsoft.Insights/Webtests/Metrics/Read | Lendo uma métrica de WebTest |
> | Ação | Microsoft.Insights/Webtests/Read | Lendo uma configuração de WebTest |
> | Ação | Microsoft.Insights/Webtests/Write | Gravando em uma configuração de WebTest |
> | Ação | Microsoft. insights/pastas de trabalho/exclusão | Eliminar um livro |
> | Ação | Microsoft. insights/pastas de trabalho/leitura | Ler uma pasta de trabalho |
> | Ação | Microsoft. insights/pastas de trabalho/gravação | Criar ou atualizar uma pasta de trabalho |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Intune/diagnosticsettings/delete | Excluindo uma configuração de diagnóstico |
> | Ação | Microsoft.Intune/diagnosticsettings/read | Lendo uma configuração de diagnóstico |
> | Ação | Microsoft.Intune/diagnosticsettings/write | Escrevendo uma configuração de diagnóstico |
> | Ação | Microsoft.Intune/diagnosticsettingscategories/read | Lendo categorias de configuração de diagnóstico |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.IoTCentral/appTemplates/action | Obtém todos os modelos de aplicativo disponíveis no Azure IoT Central |
> | Ação | Microsoft.IoTCentral/checkNameAvailability/action | Verifica se um nome de aplicativo IoT Central está disponível |
> | Ação | Microsoft.IoTCentral/checkSubdomainAvailability/action | Verifica se um subdomínio de aplicativo IoT Central está disponível |
> | Ação | Microsoft.IoTCentral/IoTApps/delete | Exclui um IoT Central aplicativos |
> | Ação | Microsoft.IoTCentral/IoTApps/read | Obtém um único aplicativo IoT Central |
> | Ação | Microsoft. IoTCentral/IoTApps/Write | Criar ou atualizar um IoT Central aplicativos |
> | Ação | Microsoft.IoTCentral/operations/read | Obtém todas as operações disponíveis em aplicativos IoT Central |
> | Ação | Microsoft.IoTCentral/register/action | Registrar a assinatura do provedor de recursos de IoT Central do Azure |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. IoTSpaces/Graph/Delete | Exclui o recurso de grafo Microsoft. IoTSpaces |
> | Ação | Microsoft. IoTSpaces/grafo/leitura | Obter os recursos do grafo Microsoft. IoTSpaces |
> | Ação | Microsoft. IoTSpaces/Graph/Write | Criar recurso de grafo Microsoft. IoTSpaces |
> | Ação | Microsoft.IoTSpaces/register/action | Registrar a assinatura para o provedor de recursos do Microsoft. IoTSpaces Graph para habilitar a criação de recursos |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.KeyVault/checkNameAvailability/read | Verifica se um nome de cofre de chaves é válido e não está em uso |
> | Ação | Microsoft.KeyVault/deletedVaults/read | Exibir as propriedades de cofres de chaves com exclusão reversível |
> | Ação | Microsoft.KeyVault/hsmPools/delete | Excluir um pool do HSM |
> | Ação | Microsoft.KeyVault/hsmPools/joinVault/action | Unir um cofre de chaves a um pool do HSM |
> | Ação | Microsoft.KeyVault/hsmPools/read | Exibir as propriedades de um pool do HSM |
> | Ação | Microsoft.KeyVault/hsmPools/write | Criar um novo pool de HSM de atualização das propriedades de um pool existente do HSM |
> | Ação | Microsoft.KeyVault/locations/deletedVaults/purge/action | Limpar um cofre de chaves com exclusão reversível |
> | Ação | Microsoft.KeyVault/locations/deletedVaults/read | Exibir as propriedades de um cofre de chaves com exclusão reversível |
> | Ação | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft. keyvault que uma rede virtual ou sub-rede está sendo excluída |
> | Ação | Microsoft.KeyVault/locations/operationResults/read | Verificar o resultado de uma operação de longa execução |
> | Ação | Microsoft.KeyVault/operations/read | Lista as operações disponíveis no provedor de recursos Microsoft. keyvault |
> | Ação | Microsoft.KeyVault/register/action | Registra uma assinatura |
> | Ação | Microsoft.KeyVault/unregister/action | Cancela o registro de uma assinatura |
> | Ação | Microsoft.KeyVault/vaults/accessPolicies/write | Atualize uma política de acesso existente mesclando ou substituindo ou adicione uma nova política de acesso a um cofre. |
> | Ação | Microsoft.KeyVault/vaults/delete | Eliminar um cofre de chaves |
> | Ação | Microsoft.KeyVault/vaults/deploy/action | Habilita o acesso a segredos em um cofre de chaves ao implantar recursos do Azure |
> | Ação | Microsoft.KeyVault/vaults/eventGridFilters/delete | Notifica o Microsoft. keyvault que uma assinatura do EventGrid para Key Vault está sendo excluída |
> | Ação | Microsoft.KeyVault/vaults/eventGridFilters/read | Notifica o Microsoft. keyvault que uma assinatura do EventGrid para Key Vault está sendo exibida |
> | Ação | Microsoft.KeyVault/vaults/eventGridFilters/write | Notifica o Microsoft. keyvault de que uma nova assinatura do EventGrid para Key Vault está sendo criada |
> | Ação | Microsoft.KeyVault/vaults/read | Exibir as propriedades de um cofre de chaves |
> | Ação | Microsoft.KeyVault/vaults/secrets/read | Exiba as propriedades de um segredo, mas não seu valor. |
> | Ação | Microsoft.KeyVault/vaults/secrets/write | Crie um novo segredo ou atualize o valor de um segredo existente. |
> | Ação | Microsoft.KeyVault/vaults/write | Criar um novo cofre de chaves ou atualizar as propriedades de um cofre de chaves existente |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Kusto/Clusters/Activate/action | Inicia o cluster. |
> | Ação | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Exclui um resourceCopy de configuração de banco de dados anexado. |
> | Ação | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Lê um resourceCopy de configuração de banco de dados anexado. |
> | Ação | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | Grava um resourceCopy de configuração de banco de dados anexado. |
> | Ação | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Verifica a disponibilidade do nome do cluster. |
> | Ação | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Adiciona entidades de banco de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | Verifica a disponibilidade do nome de um determinado tipo. |
> | Ação | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Exclui uma resourceCopy de conexões de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Lê uma resourceCopy de conexões de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Grava uma resourceCopy de conexões de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Valida a conexão de dados do banco de dado. |
> | Ação | Microsoft.Kusto/Clusters/Databases/delete | Exclui um banco de dados resourceCopy. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Exclui uma resourceCopy de conexões do hub de eventos. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Lê uma resourceCopy de conexões do hub de eventos. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Grava um resourceCopy de conexões do hub de eventos. |
> | Ação | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Valida a conexão do hub de eventos do banco de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Lista as entidades de segurança do banco de dados. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/PrincipalAssignments/Delete | Exclui uma resourceCopy de atribuições de entidade de banco de dados. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/PrincipalAssignments/leitura | Lê um banco de dados de atribuições de entidade resourceCopy. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/PrincipalAssignments/gravação | Grava uma resourceCopy de atribuições de entidade de banco de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/read | Lê um banco de dados resourceCopy. |
> | Ação | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Remove as entidades de segurança do banco de dados. |
> | Ação | Microsoft.Kusto/Clusters/Databases/write | Grava um banco de dados resourceCopy. |
> | Ação | Microsoft.Kusto/Clusters/Deactivate/action | Interrompe o cluster. |
> | Ação | Microsoft.Kusto/Clusters/delete | Exclui um resourceCopy de cluster. |
> | Ação | Microsoft. Kusto/clusters/DetachFollowerDatabases/ação | Desanexa os bancos de dados do seguidor. |
> | Ação | Microsoft. Kusto/clusters/DiagnoseVirtualNetwork/ação | Diagnostica o status de conectividade de rede para recursos externos nos quais o serviço está depedent. |
> | Ação | Microsoft. Kusto/clusters/ListFollowerDatabases/ação | Lista os bancos de dados do seguidor. |
> | Ação | Microsoft.Kusto/Clusters/read | Lê um resourceCopy de cluster. |
> | Ação | Microsoft.Kusto/Clusters/SKUs/read | Lê um resourceCopy de SKU de cluster. |
> | Ação | Microsoft. Kusto/clusters/iniciar/ação | Inicia o cluster. |
> | Ação | Microsoft. Kusto/clusters/parar/ação | Interrompe o cluster. |
> | Ação | Microsoft.Kusto/Clusters/write | Grava um resourceCopy de cluster. |
> | Ação | Microsoft.Kusto/Locations/CheckNameAvailability/action | Verifica a disponibilidade do nome do resourceCopy. |
> | Ação | Microsoft. Kusto/Locations/GetNetworkPolicies/Action | Obter políticas de intenção de rede |
> | Ação | Microsoft.Kusto/locations/operationresults/read | ResourceCopys operações de leitura |
> | Ação | Microsoft.Kusto/Operations/read | ResourceCopys operações de leitura |
> | Ação | Microsoft. Kusto/registro/ação | Ação de registro de assinatura |
> | Ação | Microsoft. Kusto/registro/ação | Registra a assinatura no provedor de recursos Kusto. |
> | Ação | Microsoft.Kusto/SKUs/read | Lê um SKU resourceCopy. |
> | Ação | Microsoft. Kusto/cancelar registro/ação | Cancela o registro da assinatura para o provedor de recursos Kusto. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.LabServices/labAccounts/CreateLab/action | Crie um laboratório em uma conta de laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/delete | Excluir contas de laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/galleryImages/delete | Excluir imagens da galeria. |
> | Ação | Microsoft.LabServices/labAccounts/galleryImages/read | Ler imagens da galeria. |
> | Ação | Microsoft.LabServices/labAccounts/galleryImages/write | Adicionar ou modificar imagens da galeria. |
> | Ação | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Obter informações de disponibilidade regional para cada categoria de tamanho configurada em uma conta de laboratório |
> | Ação | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Adicionar usuários a um laboratório |
> | Ação | Microsoft.LabServices/labAccounts/labs/delete | Excluir laboratórios. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Excluir configuração de ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Excluir ambientes. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Ler ambientes. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Redefine a senha do usuário em um ambiente |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Inicia um ambiente iniciando todos os recursos dentro do ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Para um ambiente parando todos os recursos dentro do ambiente |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Adicionar ou modificar ambientes. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Provisiona/desprovisiona os recursos necessários para uma configuração de ambiente com base no estado atual da configuração de laboratório/ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Ler configuração de ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Redefine a senha na máquina virtual do modelo. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Salva a imagem de modelo atual na Galeria compartilhada na conta do laboratório |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Excluir agendas. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Ler agendas. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Adicionar ou modificar agendas. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Inicia um modelo iniciando todos os recursos dentro do modelo. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Interrompe um modelo interrompendo todos os recursos dentro do modelo. |
> | Ação | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Adicionar ou modificar configuração de ambiente. |
> | Ação | Microsoft.LabServices/labAccounts/labs/read | Leia os laboratórios. |
> | Ação | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Enviar email com link de registro para o laboratório |
> | Ação | Microsoft.LabServices/labAccounts/labs/users/delete | Excluir usuários. |
> | Ação | Microsoft.LabServices/labAccounts/labs/users/read | Ler usuários. |
> | Ação | Microsoft.LabServices/labAccounts/labs/users/write | Adicionar ou modificar usuários. |
> | Ação | Microsoft.LabServices/labAccounts/labs/write | Adicionar ou modificar laboratórios. |
> | Ação | Microsoft.LabServices/labAccounts/read | Leia as contas do laboratório. |
> | Ação | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Exclua sharedgalleries. |
> | Ação | Microsoft.LabServices/labAccounts/sharedGalleries/read | Leia sharedgalleries. |
> | Ação | Microsoft.LabServices/labAccounts/sharedGalleries/write | Adicionar ou modificar sharedgalleries. |
> | Ação | Microsoft.LabServices/labAccounts/sharedImages/delete | Exclua SharedImages. |
> | Ação | Microsoft.LabServices/labAccounts/sharedImages/read | Leia SharedImages. |
> | Ação | Microsoft.LabServices/labAccounts/sharedImages/write | Adicionar ou modificar SharedImages. |
> | Ação | Microsoft.LabServices/labAccounts/write | Adicionar ou modificar contas de laboratório. |
> | Ação | Microsoft.LabServices/locations/operations/read | Operações de leitura. |
> | Ação | Microsoft.LabServices/register/action | Registra a assinatura |
> | Ação | Microsoft.LabServices/users/ListAllEnvironments/action | Listar todos os ambientes do usuário |
> | Ação | Microsoft.LabServices/users/Register/action | Registrar um usuário em um laboratório gerenciado |
> | Ação | Microsoft.LabServices/users/ResetPassword/action | Redefine a senha do usuário em um ambiente |
> | Ação | Microsoft.LabServices/users/StartEnvironment/action | Inicia um ambiente iniciando todos os recursos dentro do ambiente. |
> | Ação | Microsoft.LabServices/users/StopEnvironment/action | Para um ambiente parando todos os recursos dentro do ambiente |
> | Ação | Microsoft. LabServices/usuários/UserSettings/Action | Atualiza e retorna configurações de usuário pessoal. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/delete | Exclui o contrato na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do contrato na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/read | Lê o contrato na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/agreements/write | Cria ou atualiza o contrato na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/delete | Exclui o assembly na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do assembly na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/read | Lê o assembly na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/assemblies/write | Cria ou atualiza o assembly na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Exclui a configuração do lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Lê a configuração do lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Cria ou atualiza a configuração do lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/certificates/delete | Exclui o certificado na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/certificates/read | Lê o certificado na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/certificates/write | Cria ou atualiza o certificado na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/delete | Exclui a conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/join/action | Une a conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Obtém a URL de retorno de chamada para a conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Obtém as chaves no cofre de chaves. |
> | Ação | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Registra os eventos de rastreamento na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Maps/Delete | Exclui o mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/read | Lê o mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/maps/write | Cria ou atualiza o mapa na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/delete | Exclui o parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/read | Lê o parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/partners/write | Cria ou atualiza o parceiro na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Lê as definições de log da conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/read | Lê a conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft. Logic/integrationAccounts/rosettaNetProcessConfigurations/Delete | Exclui a configuração do processo RosettaNet na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/rosettaNetProcessConfigurations/Read | Lê a configuração do processo RosettaNet na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/rosettaNetProcessConfigurations/Write | Cria ou atualiza a configuração do processo RosettaNet na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/delete | Exclui o esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Obtém a URL de retorno de chamada para o conteúdo do esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/read | Lê o esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/schemas/write | Cria ou atualiza o esquema na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/sessions/delete | Exclui a sessão na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/sessions/read | Lê a configuração do lote na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/sessions/write | Cria ou atualiza a sessão na conta de integração. |
> | Ação | Microsoft.Logic/integrationAccounts/write | Cria ou atualiza a conta de integração. |
> | Ação | Microsoft. Logic/integrationServiceEnvironments/availableManagedApis/Read | Lê as APIs gerenciadas disponíveis do ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/delete | Exclui o ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/join/action | Une o Ambiente de Serviço de Integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Lê a operação da API gerenciada do ambiente do serviço de integração. |
> | Ação | Microsoft. Logic/integrationServiceEnvironments/managedApis/junção/ação | Une a API gerenciada Ambiente de Serviço de Integração. |
> | Ação | Microsoft. Logic/integrationServiceEnvironments/managedApis/operationStatuses/Read | Lê os status da operação da API gerenciada do ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Lê a API gerenciada do ambiente do serviço de integração. |
> | Ação | Microsoft. Logic/integrationServiceEnvironments/managedApis/Write | Cria ou atualiza a API gerenciada do ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Lê os status da operação de ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Lê as definições de métrica de ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/read | Lê o ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/integrationServiceEnvironments/write | Cria ou atualiza o ambiente do serviço de integração. |
> | Ação | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Obtém os grupos de operações recomendados do fluxo de trabalho. |
> | Ação | Microsoft.Logic/locations/workflows/validate/action | Valida o fluxo de trabalho. |
> | Ação | Microsoft.Logic/operations/read | Obtém a operação. |
> | Ação | Microsoft.Logic/register/action | Registra o provedor de recursos Microsoft. Logic para uma determinada assinatura. |
> | Ação | Microsoft.Logic/workflows/accessKeys/delete | Exclui a chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/list/action | Lista os segredos da chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/read | Lê a chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/regenerate/action | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft.Logic/workflows/accessKeys/write | Cria ou atualiza a chave de acesso. |
> | Ação | Microsoft. Logic/workflows/Delete | Exclui o fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/detectores/leitura | Lê o detector de fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/disable/action | Desabilita o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/enable/action | Ativa o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/listCallbackUrl/action | Obtém a URL de retorno de chamada para o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/listSwagger/action | Obtém as definições de Swagger do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/move/action | Move o fluxo de trabalho de sua ID de assinatura, grupo de recursos e/ou nome existente para uma ID de assinatura, grupo de recursos e/ou nome diferente. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Lê as configurações de diagnóstico do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Lê as definições de log de fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Lê as definições de métrica de fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/read | Lê o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/regenerateAccessKey/action | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft. Logic/fluxos de trabalho/execução/ação | Inicia uma execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Obtém os rastreamentos de expressão de ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/read | Lê a ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Obtém os rastreamentos de expressão de repetição da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/repetitions/read | Lê a repetição da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Lê o histórico de solicitações de ação de repetição executar fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Lê o histórico de solicitações de ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Lê a repetição do escopo da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/cancel/action | Cancela a execução de um fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/delete | Exclui uma execução de um fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/operations/read | Lê o status da operação de execução do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/runs/read | Lê a execução do fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/suspender/ação | Suspende o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/triggers/histories/read | Lê os históricos de gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Reenvia o gatilho de fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Obtém a URL de retorno de chamada para o gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/read | Lê o gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/reset/action | Redefine o gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/run/action | Executa o gatilho. |
> | Ação | Microsoft.Logic/workflows/triggers/setState/action | Define o estado do gatilho. |
> | Ação | Microsoft.Logic/workflows/validate/action | Valida o fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/versions/read | Lê a versão do fluxo de trabalho. |
> | Ação | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Obtém a URL de retorno de chamada para o gatilho. |
> | Ação | Microsoft. Logic/workflows/Write | Cria ou atualiza o fluxo de trabalho. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Mover qualquer Machine Learning Associação de plano de compromisso |
> | Ação | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Ler qualquer associação de plano de compromisso Machine Learning |
> | Ação | Microsoft.MachineLearning/commitmentPlans/delete | Excluir qualquer Machine Learning plano de compromisso |
> | Ação | Microsoft.MachineLearning/commitmentPlans/join/action | Junte-se a qualquer Machine Learning plano de compromisso |
> | Ação | Microsoft.MachineLearning/commitmentPlans/read | Ler qualquer plano de compromisso Machine Learning |
> | Ação | Microsoft.MachineLearning/commitmentPlans/write | Criar ou atualizar qualquer plano de compromisso de Machine Learning |
> | Ação | Microsoft.MachineLearning/locations/operationresults/read | Obter resultado de uma operação de Machine Learning |
> | Ação | Microsoft.MachineLearning/locations/operationsstatus/read | Obter o status de uma operação de Machine Learning em andamento |
> | Ação | Microsoft.MachineLearning/operations/read | Obter Machine Learning operações |
> | Ação | Microsoft.MachineLearning/register/action | Registra a assinatura para o provedor de recursos do serviço Web do Machine Learning e habilita a criação de serviços Web. |
> | Ação | Microsoft.MachineLearning/skus/read | Obter Machine Learning SKUs do plano de compromisso |
> | Ação | Microsoft.MachineLearning/webServices/action | Criar propriedades do serviço Web regional para regiões com suporte |
> | Ação | Microsoft.MachineLearning/webServices/delete | Excluir qualquer serviço Web Machine Learning |
> | Ação | Microsoft.MachineLearning/webServices/listkeys/read | Obter chaves para um serviço Web Machine Learning |
> | Ação | Microsoft.MachineLearning/webServices/read | Ler qualquer serviço Web Machine Learning |
> | Ação | Microsoft.MachineLearning/webServices/write | Criar ou atualizar qualquer serviço Web Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/delete | Excluir qualquer Workspace do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Listar chaves para um Workspace do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/read | Ler qualquer Workspace do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Ressincronizar chaves da conta de armazenamento configurada para um Workspace do Machine Learning |
> | Ação | Microsoft.MachineLearning/Workspaces/write | Criar ou atualizar qualquer Workspace do Machine Learning |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Obtém o status de uma operação de computação específica |
> | Ação | Microsoft. MachineLearningServices/localizações/cotas/leitura | Obtém as cotas de espaço de trabalho atualmente atribuídas com base em VMFamily. |
> | Ação | Microsoft. MachineLearningServices/Locations/updateQuotas/Action | Atualize a cota para cada família de VMs no espaço de trabalho. |
> | Ação | Microsoft.MachineLearningServices/locations/usages/read | Relatório de uso para recursos de computação AML em uma assinatura |
> | Ação | Microsoft.MachineLearningServices/locations/vmsizes/read | Obter tamanhos de VM com suporte |
> | Ação | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Obtém o status de uma operação de espaço de trabalho específica |
> | Ação | Microsoft.MachineLearningServices/register/action | Registra a assinatura para o provedor de recursos de Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/delete | Exclui os recursos de computação em Serviços de Machine Learning espaços de trabalho |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Listar segredos para recursos de computação no espaço de trabalho Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Listar nós para recurso de computação no espaço de trabalho Serviços de Machine Learning |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/read | Obter os recursos de computação em Serviços de Machine Learning espaços de trabalho |
> | Ação | Microsoft.MachineLearningServices/workspaces/computes/write | Criar ou atualizar os recursos de computação em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/datadriftdetectors/Read | Obtém os detectores de descompasso de dados nos espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/datadriftdetectors/Write | Cria ou atualiza os detectores de descompasso de dados nos espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/registrado/excluído | Exclui conjuntos de dataregistered em Serviços de Machine Learning espaço (s) de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/registrado/visualização/leitura | Obtém a visualização do conjunto de valores para conjuntos de trabalho registrados em Serviços de Machine Learning espaço (s) |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/registrados/perfil/leitura | Obtém os perfis de conjunto de trabalho para conjuntos de valores registrados no (s) espaço (ns) Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/registrado/perfil/gravação | Cria ou atualiza perfis de conjunto de Serviços de Machine Learning em espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/registrado/lido | Obtém conjuntos de itens registrados em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/registrado/gravação | Cria ou atualiza conjuntos de dataregisters em Serviços de Machine Learning espaço (s) de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/unregistered/Delete | Exclui conjuntos de valores não registrados em espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/datasets/não registrado/visualização/leitura | Obtém a visualização do conjunto de linhas para conjuntos de valores não registrados em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/unregisterd/Profile/Read | Obtém os perfis do conjunto de linhas para conjuntos de valores não registrados em Serviços de Machine Learning espaço (s) de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/datasets/não registrado/lido | Obtém conjuntos de valores não registrados em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/DataSets/unregisterd/Write | Cria ou atualiza conjuntos de valores não registrados em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/repositórios de armazenamento/exclusão | Exclui os repositórios de armazenamento nos espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/repositórios de armazenamento/leitura | Obtém repositórios de armazenamento em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/repositórios de armazenamento/gravação | Cria ou atualiza repositórios de armazenamento em Serviços de Machine Learning espaços de trabalho |
> | Ação | Microsoft.MachineLearningServices/workspaces/delete | Exclui os espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/pontos de extremidade/pipelines/leitura | Obtém os pipelines publicados e os pontos de extremidade do pipeline em Serviços de Machine Learning espaço de trabalho |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/pontos de extremidade/pipelines/gravação | Cria ou atualiza pipelines publicados e pontos de extremidade de pipeline em Serviços de Machine Learning espaço (s) de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/ambientes/Build/ação | Cria ambientes em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/ambientes/leitura | Obtém ambientes em Serviços de Machine Learning espaço de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/ambientes/readSecrets/Action | Obtém os ambientes com segredos em Serviços de Machine Learning espaço de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/ambientes/gravação | Cria ou atualiza ambientes em Serviços de Machine Learning espaço (s) de trabalho |
> | Ação | Microsoft. MachineLearningServices/Workspaces/eventGridFilters/Read | Obter um filtro de grade de eventos para um espaço de trabalho específico |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/experimentos/excluir | Exclui experimentos em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/experimentos/leitura | Obtém experimentos em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/experimentos/execuções/leitura | É executado em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/experimentos/execuções/scriptRun/enviar/ação | Cria ou atualiza execuções de script em Serviços de Machine Learning espaço (s) de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/experimentos/execuções/gravação | Cria ou atualiza execuções em Serviços de Machine Learning espaço (s) de trabalho |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | Cria ou atualiza experimentos em Serviços de Machine Learning espaço (s) de trabalho |
> | Ação | Microsoft.MachineLearningServices/workspaces/listKeys/action | Listar segredos para um espaço de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/metadados/artefatos/excluir | Exclui artefatos em espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/metadados/artefatos/leitura | Obtém artefatos em espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/metadados/artefatos/gravação | Cria ou atualiza artefatos em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/metadados/instantâneos/excluir | Exclui instantâneos em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/metadados/instantâneos/leitura | Obtém instantâneos em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/metadados/instantâneos/gravação | Cria ou atualiza instantâneos em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/modelos/excluir | Exclui modelos em espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Models/download/ação | Baixa modelos em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/modelos/pacote/ação | Modelos de pacotes em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Models/Read | Obtém os modelos nos espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Models/Write | Cria ou atualiza modelos em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/modules/Read | Obtém os módulos nos espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/modules/Write | Cria ou atualiza o módulo nos espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/pipelinedrafts/Delete | Exclui rascunhos de pipeline em Serviços de Machine Learning espaço (s) de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/pipelinedrafts/Read | Obtém rascunhos de pipeline em Serviços de Machine Learning espaço (s) de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/pipelinedrafts/Write | Cria ou atualiza rascunhos de pipeline em Serviços de Machine Learning espaço (s) de trabalho |
> | Ação | Microsoft.MachineLearningServices/workspaces/read | Obter o Serviços de Machine Learning espaço de trabalho |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/serviços/ACI/excluir | Exclui os serviços ACIs nos espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/ACI/listkeys/Action | Lista as chaves para os serviços ACIs em Serviços de Machine Learning espaço (s) de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/ACI/Write | Criar ou atualizar serviços ACIs em Serviços de Machine Learning espaços de trabalho |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKs/DevTest/Delete | Exclui os serviços DevTest AKS nos espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKs/DevTest/listkeys/ação | Lista as chaves para serviços DevTest AKS em Serviços de Machine Learning espaço de trabalho |
> | DataAction | Microsoft. MachineLearningServices/espaços de trabalho/serviços/AKs/DevTest/Pontuação/ação | Pontua DevTest os serviços AKS no espaço de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKs/DevTest/Write | Cria ou atualiza os serviços DevTest AKS nos espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKs/prod/Delete | Exclui os serviços AKS de produção nos espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKs/prod/listkeys/Action | Lista as chaves para os serviços AKS de produção nos espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKs/prod/pontuation/Action | Pontuações AKS serviços de produção em espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/AKs/prod/Write | Criar ou atualizar os serviços AKS de produção nos espaços de trabalho Serviços de Machine Learning |
> | DataAction | Microsoft. MachineLearningServices/Workspaces/Services/Read | Obtém serviços em Serviços de Machine Learning espaços de trabalho |
> | Ação | Microsoft.MachineLearningServices/workspaces/write | Cria ou atualiza um ou mais espaços de trabalho do Serviços de Machine Learning |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ManagedIdentity/Identities/Read | Obtém uma identidade atribuída do sistema existente |
> | Ação | Microsoft. ManagedIdentity/operações/leitura | Lista as operações disponíveis no provedor de recursos Microsoft. ManagedIdentity |
> | Ação | Microsoft.ManagedIdentity/register/action | Registra a assinatura para o provedor de recursos de identidade gerenciada |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Ação RBAC para atribuir uma identidade de usuário existente atribuída a um recurso |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Exclui uma identidade atribuída pelo usuário existente |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/read | Obtém uma identidade atribuída ao usuário existente |
> | Ação | Microsoft.ManagedIdentity/userAssignedIdentities/write | Cria uma nova identidade atribuída ao usuário ou atualiza as marcas associadas a uma identidade atribuída ao usuário existente |

## <a name="microsoftmanagedservices"></a>Microsoft. Managedservices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Managedservices/marketplaceRegistrationDefinitions/Read | Recupera uma lista de definições de registro de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/operações/leitura | Recupera uma lista de operações de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/operationStatuses/Read | Lê o status da operação para o recurso. |
> | Ação | Microsoft. Managedservices/registrar/ação | Registre-se nos serviços gerenciados. |
> | Ação | Microsoft. Managedservices/registrationAssignments/Delete | Remove a atribuição de registro de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/registrationAssignments/Read | Recupera uma lista de atribuições de registro de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/registrationAssignments/Write | Adicionar ou modificar a atribuição de registro de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/registrationDefinitions/Delete | Remove a definição de registro de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/registrationDefinitions/Read | Recupera uma lista de definições de registro de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/registrationDefinitions/Write | Adicionar ou modificar a definição de registro de serviços gerenciados. |
> | Ação | Microsoft. Managedservices/cancelar registro/ação | Cancelar o registro dos serviços gerenciados. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Management/checkNameAvailability/action | Verifica se o nome do grupo de gerenciamento especificado é válido e exclusivo. |
> | Ação | Microsoft.Management/getEntities/action | Listar todas as entidades (Grupos de Gerenciamento, assinaturas, etc.) para o usuário autenticado. |
> | Ação | Microsoft.Management/managementGroups/delete | Excluir grupo de gerenciamento. |
> | Ação | Microsoft. Management/managementGroups/descendentes/leitura | Obtém todos os descendentes (Grupos de Gerenciamento, assinaturas) de um grupo de gerenciamento. |
> | Ação | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Ação | Microsoft.Management/managementGroups/subscriptions/delete | Desassocia a assinatura do grupo de gerenciamento. |
> | Ação | Microsoft.Management/managementGroups/subscriptions/write | Associa a assinatura existente ao grupo de gerenciamento. |
> | Ação | Microsoft.Management/managementGroups/write | Criar ou atualizar um grupo de gerenciamento. |
> | Ação | Microsoft.Management/register/action | Registrar a assinatura especificada com Microsoft. Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Concede acesso de leitura de dados a uma conta do Maps. |
> | Ação | Microsoft.Maps/accounts/delete | Excluir uma conta do Maps. |
> | Ação | Microsoft.Maps/accounts/eventGridFilters/delete | Excluir um filtro de grade de eventos |
> | Ação | Microsoft.Maps/accounts/eventGridFilters/read | Obter um filtro de grade de eventos |
> | Ação | Microsoft.Maps/accounts/eventGridFilters/write | Criar ou atualizar um filtro de grade de eventos |
> | Ação | Microsoft.Maps/accounts/listKeys/action | Listar chaves de conta do Maps |
> | Ação | Microsoft.Maps/accounts/read | Obtenha uma conta do Maps. |
> | Ação | Microsoft.Maps/accounts/regenerateKey/action | Gerar nova chave primária ou secundária de conta de mapas |
> | Ação | Microsoft.Maps/accounts/write | Criar ou atualizar uma conta do Maps. |
> | Ação | Microsoft.Maps/operations/read | Ler as operações do provedor |
> | Ação | Microsoft.Maps/register/action | Registrar o provedor |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Retorna um contrato. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Aceita um contrato assinado. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importa uma imagem para o ACR do usuário final. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Retorna uma configuração. |
> | Ação | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Salva uma configuração. |
> | Ação | Microsoft.Marketplace/register/action | Registra o provedor de recursos Microsoft. Marketplace na assinatura. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/delete | Faz uma operação de exclusão em um recurso de serviço de desenvolvimento clássico. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Obtém as chaves de gerenciamento de recursos do serviço de desenvolvimento clássico. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Obtém a URL de logon único para um serviço de desenvolvimento clássico. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/read | Faz uma operação GET em um serviço de desenvolvimento clássico. |
> | Ação | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Gera uma chave de gerenciamento de recursos do serviço de desenvolvimento clássico. |
> | Ação | Microsoft.MarketplaceApps/Operations/read | Leia as operações para todos os tipos de recurso. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Cancelar um contrato para um determinado item do Marketplace |
> | Ação | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Retornar um contrato para um determinado item do Marketplace |
> | Ação | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Assinar um contrato para um determinado item do Marketplace |
> | Ação | Microsoft.MarketplaceOrdering/agreements/read | Retornar todos os contratos sob determinada assinatura |
> | Ação | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Obter um contrato para um determinado item de máquina virtual do Marketplace |
> | Ação | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Assinar ou cancelar um contrato para um determinado item de máquina virtual do Marketplace |
> | Ação | Microsoft.MarketplaceOrdering/operations/read | Listar todas as operações possíveis na API |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Media/checknameavailability/action | Verifica se um nome de conta dos serviços de mídia está disponível |
> | Ação | Microsoft.Media/mediaservices/accountfilters/delete | Excluir qualquer filtro de conta |
> | Ação | Microsoft.Media/mediaservices/accountfilters/read | Ler qualquer filtro de conta |
> | Ação | Microsoft.Media/mediaservices/accountfilters/write | Criar ou atualizar um filtro de conta |
> | Ação | Microsoft.Media/mediaservices/assets/assetfilters/delete | Excluir qualquer filtro de ativo |
> | Ação | Microsoft.Media/mediaservices/assets/assetfilters/read | Ler qualquer filtro de ativo |
> | Ação | Microsoft.Media/mediaservices/assets/assetfilters/write | Criar ou atualizar qualquer filtro de ativo |
> | Ação | Microsoft.Media/mediaservices/assets/delete | Excluir qualquer ativo |
> | Ação | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Obter chave de criptografia de ativo |
> | Ação | Microsoft.Media/mediaservices/assets/listContainerSas/action | Listar URLs de SAS do contêiner de ativos |
> | Ação | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Listar localizadores de streaming para ativo |
> | Ação | Microsoft.Media/mediaservices/assets/read | Ler qualquer ativo |
> | Ação | Microsoft.Media/mediaservices/assets/write | Criar ou atualizar qualquer ativo |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Excluir qualquer política de chave de conteúdo |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Obter propriedades de política com segredos |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/read | Ler qualquer política de chave de conteúdo |
> | Ação | Microsoft.Media/mediaservices/contentKeyPolicies/write | Criar ou atualizar qualquer política de chave de conteúdo |
> | Ação | Microsoft.Media/mediaservices/delete | Excluir qualquer conta de serviços de mídia |
> | Ação | Microsoft.Media/mediaservices/eventGridFilters/delete | Excluir qualquer filtro de grade de eventos |
> | Ação | Microsoft.Media/mediaservices/eventGridFilters/read | Ler qualquer filtro de grade de eventos |
> | Ação | Microsoft.Media/mediaservices/eventGridFilters/write | Criar ou atualizar qualquer filtro de grade de eventos |
> | Ação | Microsoft.Media/mediaservices/liveEventOperations/read | Ler qualquer operação de evento ao vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/delete | Excluir qualquer evento ao vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Excluir qualquer saída ao vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Ler qualquer saída ao vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Criar ou atualizar qualquer saída ao vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/read | Ler qualquer evento ao vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/reset/action | Redefinir qualquer operação de evento ao vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/start/action | Iniciar qualquer operação de evento ao vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/stop/action | Parar qualquer operação de evento ao vivo |
> | Ação | Microsoft.Media/mediaservices/liveEvents/write | Criar ou atualizar qualquer evento ao vivo |
> | Ação | Microsoft.Media/mediaservices/liveOutputOperations/read | Ler qualquer operação de saída ao vivo |
> | Ação | Microsoft.Media/mediaservices/read | Ler qualquer conta dos serviços de mídia |
> | Ação | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Ler qualquer operação de ponto de extremidade de streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/delete | Excluir qualquer ponto de extremidade de streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/read | Ler qualquer ponto de extremidade de streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Dimensionar qualquer operação de ponto de extremidade de streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Iniciar qualquer operação de ponto de extremidade de streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Parar qualquer operação de ponto de extremidade de streaming |
> | Ação | Microsoft.Media/mediaservices/streamingEndpoints/write | Criar ou atualizar qualquer ponto de extremidade de streaming |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/delete | Excluir qualquer localizador de streaming |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Listar chaves de conteúdo |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Listar caminhos |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/read | Ler qualquer localizador de streaming |
> | Ação | Microsoft.Media/mediaservices/streamingLocators/write | Criar ou atualizar um localizador de streaming |
> | Ação | Microsoft.Media/mediaservices/streamingPolicies/delete | Excluir qualquer política de streaming |
> | Ação | Microsoft.Media/mediaservices/streamingPolicies/read | Ler qualquer política de streaming |
> | Ação | Microsoft.Media/mediaservices/streamingPolicies/write | Criar ou atualizar uma política de streaming |
> | Ação | Microsoft.Media/mediaservices/syncStorageKeys/action | Sincronizar as chaves de armazenamento para uma conta de armazenamento do Azure anexada |
> | Ação | Microsoft.Media/mediaservices/transforms/delete | Excluir qualquer transformação |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Cancelar trabalho |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/delete | Excluir qualquer trabalho |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/read | Ler qualquer trabalho |
> | Ação | Microsoft.Media/mediaservices/transforms/jobs/write | Criar ou atualizar qualquer trabalho |
> | Ação | Microsoft.Media/mediaservices/transforms/read | Ler qualquer transformação |
> | Ação | Microsoft.Media/mediaservices/transforms/write | Criar ou atualizar qualquer transformação |
> | Ação | Microsoft.Media/mediaservices/write | Criar ou atualizar qualquer conta de serviços de mídia |
> | Ação | Microsoft.Media/operations/read | Obter operações disponíveis |
> | Ação | Microsoft.Media/register/action | Registra a assinatura para o provedor de recursos de serviços de mídia e habilita a criação de contas de serviços de mídia |
> | Ação | Microsoft.Media/unregister/action | Cancela o registro da assinatura para o provedor de recursos dos serviços de mídia |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Migration/assessmentprojects/assessmentoptions/Read | Obtém as opções de avaliação que estão disponíveis no local especificado |
> | Ação | Microsoft.Migrate/assessmentprojects/assessments/read | Lista avaliações em um projeto |
> | Ação | Microsoft.Migrate/assessmentprojects/delete | Excluir o projeto de avaliação |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | Obter as propriedades de uma máquina avaliada |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | Exclui uma avaliação |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | Baixa a URL de um relatório de avaliação |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/assessments/read | Obtém as propriedades de uma avaliação |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/assessments/write | Cria uma nova avaliação ou atualiza uma avaliação existente |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/delete | Exclui um grupo |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/read | Obter as propriedades de um grupo |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Atualizar grupo adicionando ou removendo computadores |
> | Ação | Microsoft.Migrate/assessmentprojects/groups/write | Cria um novo grupo ou atualiza um grupo existente |
> | Ação | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | Exclui o coletor HyperV |
> | Ação | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | Obter as propriedades do coletor HyperV |
> | Ação | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | Cria um novo coletor do HyperV ou atualiza um coletor HyperV existente |
> | Ação | Microsoft. Migrate/assessmentprojects/importcollectors/Delete | Exclui o coletor de importação |
> | Ação | Microsoft. Migrate/assessmentprojects/importcollectors/Read | Obtém as propriedades do coletor de importação |
> | Ação | Microsoft. Migrate/assessmentprojects/importcollectors/Write | Cria um novo coletor de importação ou atualiza um coletor de importação existente |
> | Ação | Microsoft.Migrate/assessmentprojects/machines/read | Obtém as propriedades de um computador |
> | Ação | Microsoft.Migrate/assessmentprojects/read | Obter as propriedades do projeto de avaliação |
> | Ação | Microsoft. Migrate/assessmentprojects/servercollectors/Read | Obtém as propriedades do coletor do servidor |
> | Ação | Microsoft. Migrate/assessmentprojects/servercollectors/Write | Cria um novo coletor de servidor ou atualiza um coletor de servidor existente |
> | Ação | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | Exclui o coletor do VMware |
> | Ação | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | Obter as propriedades do coletor do VMware |
> | Ação | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Cria um novo coletor VMware ou atualiza um coletor VMware existente |
> | Ação | Microsoft.Migrate/assessmentprojects/write | Cria um novo projeto de avaliação ou atualiza um projeto de avaliação existente |
> | Ação | Microsoft.Migrate/locations/assessmentOptions/read | Obtém as opções de avaliação que estão disponíveis no local especificado |
> | Ação | Microsoft.Migrate/locations/checknameavailability/action | Verifica a disponibilidade do nome do recurso para a assinatura especificada no local especificado |
> | Ação | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Obtém as propriedades de uma instância de banco de dados |
> | Ação | Microsoft.Migrate/migrateprojects/Databases/read | Obtém as propriedades de um banco de dados |
> | Ação | Microsoft.Migrate/migrateprojects/delete | Excluir um projeto de migração |
> | Ação | Microsoft.Migrate/migrateprojects/machines/read | Obtém as propriedades de um computador |
> | Ação | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | Exclui um evento de migração |
> | Ação | Microsoft.Migrate/migrateprojects/MigrateEvents/read | Obtém as propriedades de um evento de migração. |
> | Ação | Microsoft.Migrate/migrateprojects/read | Obtém as propriedades do projeto de migração |
> | Ação | Microsoft. Migrate/migrateprojects/RefreshSummary/Action | Atualiza o resumo do projeto de migração |
> | Ação | Microsoft.Migrate/migrateprojects/registerTool/action | Registra a ferramenta para um projeto de migração |
> | Ação | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | Limpar os dados de solução de projeto de migração |
> | Ação | Microsoft.Migrate/migrateprojects/solutions/Delete | Exclui uma solução de migração de projeto |
> | Ação | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Obter a configuração de solução de projeto de migração |
> | Ação | Microsoft.Migrate/migrateprojects/solutions/read | Obtém as propriedades da solução migrar projeto |
> | Ação | Microsoft.Migrate/migrateprojects/solutions/write | Cria uma nova solução de migração de projeto ou atualiza uma solução de projeto de migração existente |
> | Ação | Microsoft.Migrate/migrateprojects/write | Cria um novo projeto de migração ou atualiza um projeto de migração existente |
> | Ação | Microsoft.Migrate/Operations/read | Lista as operações disponíveis no provedor de recursos Microsoft. Migrate |
> | Ação | Microsoft.Migrate/projects/assessments/read | Lista avaliações em um projeto |
> | Ação | Microsoft.Migrate/projects/delete | Exclui o projeto |
> | Ação | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Obter as propriedades de uma máquina avaliada |
> | Ação | Microsoft.Migrate/projects/groups/assessments/delete | Exclui uma avaliação |
> | Ação | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Baixa a URL de um relatório de avaliação |
> | Ação | Microsoft.Migrate/projects/groups/assessments/read | Obtém as propriedades de uma avaliação |
> | Ação | Microsoft.Migrate/projects/groups/assessments/write | Cria uma nova avaliação ou atualiza uma avaliação existente |
> | Ação | Microsoft.Migrate/projects/groups/delete | Exclui um grupo |
> | Ação | Microsoft.Migrate/projects/groups/read | Obter as propriedades de um grupo |
> | Ação | Microsoft.Migrate/projects/groups/write | Cria um novo grupo ou atualiza um grupo existente |
> | Ação | Microsoft.Migrate/projects/keys/action | Obtém chaves compartilhadas para o projeto |
> | Ação | Microsoft.Migrate/projects/machines/read | Obtém as propriedades de um computador |
> | Ação | Microsoft.Migrate/projects/read | Obtém as propriedades de um projeto |
> | Ação | Microsoft.Migrate/projects/write | Cria um novo projeto ou atualiza um projeto existente |
> | Ação | Microsoft.Migrate/register/action | Registra a assinatura com o provedor de recursos Microsoft. Migrate |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.MixedReality/register/action | Registra uma assinatura para o provedor de recursos de realidade misturada. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Excluir âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Descobrir âncoras espaciais próximas |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obter propriedades de âncoras espaciais |
> | Ação | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. insights/diagnosticSettings/Read | Obtém a configuração de diagnóstico para Microsoft. MixedReality/spatialAnchorsAccounts |
> | Ação | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para Microsoft. MixedReality/spatialAnchorsAccounts |
> | Ação | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as métricas disponíveis para Microsoft. MixedReality/spatialAnchorsAccounts |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localizar âncoras espaciais |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Atualizar propriedades de âncoras espaciais |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. NetApp/Locations/checkfilepathavailability/Action | Verificar se o caminho do arquivo está disponível |
> | Ação | Microsoft. NetApp/Locations/checknameavailability/Action | Verifique se o nome do recurso está disponível |
> | Ação | Microsoft.NetApp/locations/operationresults/read | Lê um recurso de resultado de operação. |
> | Ação | Microsoft.NetApp/locations/read | Lê um recurso de verificação de disponibilidade. |
> | Ação | Microsoft. NetApp/netAppAccounts/backupPolicies/Delete | Exclui um recurso de política de backup. |
> | Ação | Microsoft. NetApp/netAppAccounts/backupPolicies/Read | Lê um recurso de política de backup. |
> | Ação | Microsoft. NetApp/netAppAccounts/backupPolicies/Write | Grava um recurso de política de backup. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Exclui um recurso de pool. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/read | Lê um recurso de pool. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/volumes/backups/excluir | Exclui um recurso de backup. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/volumes/backups/leitura | Lê um recurso de backup. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/volumes/backups/gravação | Grava um recurso de backup. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/volumes/intervalo/ação | Relações de replicação de volume de interrupção |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Exclui um recurso de volume. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Lê um recurso de destino de montagem. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Lê um recurso de volume. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/volumes/ReplicationStatus/Action | Lê os status da replicação do volume. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/volumes/REVERT/Action | Reverter volume para instantâneo específico |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Exclui um recurso de instantâneo. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Lê um recurso de instantâneo. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Grava um recurso de instantâneo. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Grava um recurso de volume. |
> | Ação | Microsoft.NetApp/netAppAccounts/capacityPools/write | Grava um recurso de pool. |
> | Ação | Microsoft.NetApp/netAppAccounts/delete | Exclui um recurso de conta. |
> | Ação | Microsoft.NetApp/netAppAccounts/read | Lê um recurso de conta. |
> | Ação | Microsoft. NetApp/netAppAccounts/cofres/leitura | Lê um recurso de cofre. |
> | Ação | Microsoft.NetApp/netAppAccounts/write | Grava um recurso de conta. |
> | Ação | Microsoft.NetApp/Operations/read | Lê um recurso de operação. |
> | Ação | Microsoft. NetApp/registro/ação | Registra a assinatura com o provedor de recursos Microsoft. NetApp |
> | Ação | Microsoft. NetApp/cancelar registro/ação | Cancela o registro de assinatura com o provedor de recursos Microsoft. NetApp |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Obter cabeçalhos de solicitação disponíveis do gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Obter cabeçalho de resposta disponível do gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Obter variáveis de servidor disponíveis do gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Política predefinida SSL do gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Opções de SSL disponíveis do gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Obter conjuntos de regras WAF disponíveis do gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Une um pool de endereços de back-end do gateway de aplicativo. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/applicationGateways/backendhealth/action | Obter uma integridade de back-end do gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGateways/delete | Excluir um gateway de aplicativo |
> | Ação | Microsoft. Network/applicationGateways/getBackendHealthOnDemand/Action | Obtém uma integridade de back-end do gateway de aplicativo sob demanda para determinada configuração de http e pool de back-end |
> | Ação | Microsoft.Network/applicationGateways/read | Obter um gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGateways/start/action | Inicia um gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGateways/stop/action | Para um gateway de aplicativo |
> | Ação | Microsoft.Network/applicationGateways/write | Cria um gateway de aplicativo ou atualiza um gateway de aplicativo |
> | Ação | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Exclui uma política de WAF do gateway de aplicativo |
> | Ação | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Obter uma política de WAF do gateway de aplicativo |
> | Ação | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Cria uma política de WAF do gateway de aplicativo ou atualiza uma política de WAF do gateway de aplicativo |
> | Ação | Microsoft.Network/applicationSecurityGroups/delete | Exclui um grupo de segurança de aplicativo |
> | Ação | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Une uma configuração de IP aos grupos de segurança do aplicativo. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Une uma regra de segurança aos grupos de segurança do aplicativo. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Lista as configurações de IP no ApplicationSecurityGroup |
> | Ação | Microsoft.Network/applicationSecurityGroups/read | Obtém uma ID do grupo de segurança do aplicativo. |
> | Ação | Microsoft.Network/applicationSecurityGroups/write | Cria um grupo de segurança de aplicativo ou atualiza um grupo de segurança de aplicativo existente. |
> | Ação | Microsoft.Network/azureFirewallFqdnTags/read | Obter marcas de FQDN do firewall do Azure |
> | Ação | Microsoft.Network/azurefirewalls/delete | Excluir o Firewall do Azure |
> | Ação | Microsoft.Network/azurefirewalls/read | Obter o Firewall do Azure |
> | Ação | Microsoft.Network/azurefirewalls/write | Criar ou atualizar um firewall do Azure |
> | Ação | Microsoft. Network/bastionHosts/createbsl/Action | Cria URLs compartilháveis para as VMs em uma bastiões e retorna as URLs |
> | Ação | Microsoft.Network/bastionHosts/delete | Exclui um host bastião |
> | Ação | Microsoft. Network/bastionHosts/deletebsl/Action | Exclui URLs compartilháveis para as VMs fornecidas em uma bastiões |
> | Ação | Microsoft. Network/bastionHosts/disconnectactivesessions/Action | Desconectar determinadas sessões ativas no host de bastiões |
> | Ação | Microsoft. Network/bastionHosts/getactivesessions/Action | Obter sessões ativas no host de bastiões |
> | Ação | Microsoft. Network/bastionHosts/getbsl/Action | Retorna as URLs compartilháveis para as VMs especificadas em uma sub-rede de bastiões, desde que suas URLs sejam criadas |
> | Ação | Microsoft.Network/bastionHosts/read | Obter um host bastião |
> | Ação | Microsoft.Network/bastionHosts/write | Criar ou atualizar um host bastião |
> | Ação | Microsoft.Network/bgpServiceCommunities/read | Obter comunidades de serviço BGP |
> | Ação | Microsoft.Network/checkFrontDoorNameAvailability/action | Verifica se um nome de porta frontal está disponível |
> | Ação | Microsoft.Network/checkTrafficManagerNameAvailability/action | Verifica a disponibilidade de um nome DNS relativo do Gerenciador de tráfego. |
> | Ação | Microsoft.Network/connections/delete | Exclui VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/read | Obtém VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/revoke/action | Marca um status de conexão de rota expressa como revogado |
> | Ação | Microsoft.Network/connections/sharedkey/action | Obter VirtualNetworkGatewayConnection SharedKey |
> | Ação | Microsoft.Network/connections/sharedKey/read | Obtém VirtualNetworkGatewayConnection SharedKey |
> | Ação | Microsoft.Network/connections/sharedKey/write | Criar ou atualizar um VirtualNetworkGatewayConnection SharedKey existente |
> | Ação | Microsoft. Network/Connections/startpacketcapture/Action | Inicia uma captura de pacote de conexão de gateway de rede virtual. |
> | Ação | Microsoft. Network/Connections/stoppacketcapture/Action | Interrompe uma captura de pacote de conexão de gateway de rede virtual. |
> | Ação | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Obtém a configuração do dispositivo VPN de VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/connections/write | Criar ou atualizar um VirtualNetworkGatewayConnection existente |
> | Ação | Microsoft.Network/ddosCustomPolicies/delete | Exclui uma política personalizada de DDoS |
> | Ação | Microsoft.Network/ddosCustomPolicies/read | Obter uma definição de definição de política personalizada de DDoS |
> | Ação | Microsoft.Network/ddosCustomPolicies/write | Cria uma política personalizada de DDoS ou atualiza uma política personalizada de DDoS existente |
> | Ação | Microsoft.Network/ddosProtectionPlans/delete | Excluir um plano de proteção contra DDoS |
> | Ação | Microsoft.Network/ddosProtectionPlans/join/action | Une um plano de proteção contra DDoS. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/ddosProtectionPlans/read | Obter um plano de proteção contra DDoS |
> | Ação | Microsoft.Network/ddosProtectionPlans/write | Cria um plano de proteção contra DDoS ou atualiza um plano de proteção contra DDoS  |
> | Ação | Microsoft.Network/dnsoperationresults/read | Obtém os resultados de uma operação DNS |
> | Ação | Microsoft.Network/dnsoperationstatuses/read | Obtém o status de uma operação DNS  |
> | Ação | Microsoft.Network/dnszones/A/delete | Remova o conjunto de registros de um determinado nome e digite ' A ' de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/A/read | Obter o conjunto de registros do tipo ' A ', no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft.Network/dnszones/A/write | Criar ou atualizar um conjunto de registros do tipo ' A ' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/AAAA/delete | Remova o conjunto de registros de um determinado nome e digite ' AAAA ' de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/AAAA/read | Obter o conjunto de registros do tipo ' AAAA ' no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft.Network/dnszones/AAAA/write | Criar ou atualizar um conjunto de registros do tipo ' AAAA ' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/all/read | Obtém conjuntos de registros DNS entre tipos |
> | Ação | Microsoft.Network/dnszones/CAA/delete | Remova o conjunto de registros de um determinado nome e digite ' CAA ' de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/CAA/read | Obter o conjunto de registros do tipo ' CAA ' no formato JSON. O conjunto de registros contém o TTL, as marcas e a ETag. |
> | Ação | Microsoft.Network/dnszones/CAA/write | Criar ou atualizar um conjunto de registros do tipo ' CAA ' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/CNAME/delete | Remova o conjunto de registros de um determinado nome e digite ' CNAME ' de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/CNAME/read | Obter o conjunto de registros do tipo ' CNAME ' no formato JSON. O conjunto de registros contém o TTL, as marcas e a ETag. |
> | Ação | Microsoft.Network/dnszones/CNAME/write | Criar ou atualizar um conjunto de registros do tipo ' CNAME ' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/delete | Exclua a zona DNS, no formato JSON. As propriedades de zona incluem marcas, ETag, numberOfRecordSets e maxNumberOfRecordSets. |
> | Ação | Microsoft.Network/dnszones/MX/delete | Remova o conjunto de registros de um determinado nome e digite "MX" de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/MX/read | Obter o conjunto de registros do tipo ' MX ' no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft.Network/dnszones/MX/write | Criar ou atualizar um conjunto de registros do tipo ' MX ' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/NS/delete | Exclui o conjunto de registros DNS do tipo NS |
> | Ação | Microsoft.Network/dnszones/NS/read | Obtém o conjunto de registros DNS do tipo NS |
> | Ação | Microsoft.Network/dnszones/NS/write | Cria ou atualiza o conjunto de registros DNS do tipo NS |
> | Ação | Microsoft.Network/dnszones/PTR/delete | Remova o conjunto de registros de um determinado nome e digite ' PTR ' de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/PTR/read | Obter o conjunto de registros do tipo ' PTR ' no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft.Network/dnszones/PTR/write | Criar ou atualizar um conjunto de registros do tipo ' PTR ' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/read | Obtenha a zona DNS no formato JSON. As propriedades de zona incluem marcas, ETag, numberOfRecordSets e maxNumberOfRecordSets. Observe que esse comando não recupera os conjuntos de registros contidos na zona. |
> | Ação | Microsoft.Network/dnszones/recordsets/read | Obtém conjuntos de registros DNS entre tipos |
> | Ação | Microsoft.Network/dnszones/SOA/read | Obtém o conjunto de registros DNS do tipo SOA |
> | Ação | Microsoft.Network/dnszones/SOA/write | Cria ou atualiza o conjunto de registros DNS do tipo SOA |
> | Ação | Microsoft.Network/dnszones/SRV/delete | Remova o conjunto de registros de um determinado nome e digite ' SRV ' de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/SRV/read | Obter o conjunto de registros do tipo ' SRV ' no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft.Network/dnszones/SRV/write | Criar ou atualizar conjunto de registros do tipo SRV |
> | Ação | Microsoft.Network/dnszones/TXT/delete | Remova o conjunto de registros de um determinado nome e digite ' TXT ' de uma zona DNS. |
> | Ação | Microsoft.Network/dnszones/TXT/read | Obter o conjunto de registros do tipo ' TXT ' no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft.Network/dnszones/TXT/write | Criar ou atualizar um conjunto de registros do tipo ' TXT ' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/dnszones/write | Criar ou atualizar uma zona DNS dentro de um grupo de recursos.  Usado para atualizar as marcas em um recurso de zona DNS. Observe que esse comando não pode ser usado para criar ou atualizar conjuntos de registros dentro da zona. |
> | Ação | Microsoft.Network/expressRouteCircuits/authorizations/delete | Exclui uma autorização de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/authorizations/read | Obter uma autorização de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/authorizations/write | Criar ou atualizar uma autorização de ExpressRouteCircuit existente |
> | Ação | Microsoft.Network/expressRouteCircuits/delete | Exclui um ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/join/action | Une um circuito de rota expressa. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/arpTables/leitura | Obter um ArpTable de emparelhamento ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Exclui uma conexão ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Obter uma conexão ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Criar ou atualizar um recurso de conexão ExpressRouteCircuit existente |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/delete | Exclui um emparelhamento ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Obtém conexão de circuito de rota expressa do par |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/read | Obter um emparelhamento ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/routeTables/leitura | Obter uma rota de emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/routeTablesSummary/leitura | Obter um resumo de rota de emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Obter uma estatística de emparelhamento ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/peerings/write | Criar ou atualizar um emparelhamento de ExpressRouteCircuit existente |
> | Ação | Microsoft.Network/expressRouteCircuits/read | Obter um ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/stats/read | Obtém uma estatística ExpressRouteCircuit |
> | Ação | Microsoft.Network/expressRouteCircuits/write | Criar ou atualizar um ExpressRouteCircuit existente |
> | Ação | Microsoft.Network/expressRouteCrossConnections/join/action | Une uma conexão cruzada de rota expressa. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/expressRouteCrossConnections/emparelhamentos/arpTables/leitura | Obter uma tabela ARP de emparelhamento de conexão cruzada de rota expressa |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Exclui um emparelhamento de conexão cruzada de rota expressa |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/read | Obter um emparelhamento de conexão cruzada de rota expressa |
> | Ação | Microsoft. Network/expressRouteCrossConnections/emparelhamentos/routeTables/leitura | Obter uma tabela de rota de emparelhamento de conexão cruzada de rota expressa |
> | Ação | Microsoft. Network/expressRouteCrossConnections/emparelhamentos/routeTableSummary/leitura | Obter um resumo da tabela de rotas de emparelhamento de conexão cruzada de rota expressa |
> | Ação | Microsoft.Network/expressRouteCrossConnections/peerings/write | Cria um emparelhamento de conexão cruzada de rota expressa ou atualiza um emparelhamento de conexão cruzada de rota expressa existente |
> | Ação | Microsoft.Network/expressRouteCrossConnections/read | Obter conexão cruzada de rota expressa |
> | Ação | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Excluir uma conexão de rota expressa |
> | Ação | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Obter uma conexão de rota expressa |
> | Ação | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Cria uma conexão de rota expressa ou atualiza uma conexão de rota expressa existente |
> | Ação | Microsoft.Network/expressRouteGateways/join/action | Une um gateway de rota expressa. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/expressRouteGateways/read | Obter gateway de rota expressa |
> | Ação | Microsoft.Network/expressRoutePorts/delete | Exclui ExpressRoutePorts |
> | Ação | Microsoft.Network/expressRoutePorts/join/action | Une portas de rota expressa. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/expressRoutePorts/links/read | Obtém ExpressRouteLink |
> | Ação | Microsoft.Network/expressRoutePorts/read | Obtém ExpressRoutePorts |
> | Ação | Microsoft.Network/expressRoutePorts/write | Criar ou atualizar ExpressRoutePorts |
> | Ação | Microsoft.Network/expressRoutePortsLocations/read | Obter locais de portas de rota expressa |
> | Ação | Microsoft.Network/expressRouteServiceProviders/read | Obter provedores de Serviço de Roteiros expressos |
> | Ação | Microsoft. Network/firewallPolicies/Delete | Exclui uma política de firewall |
> | Ação | Microsoft. Network/firewallPolicies/junção/ação | Une uma política de firewall. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/firewallPolicies/Read | Obter uma política de firewall |
> | Ação | Microsoft. Network/firewallPolicies/grupo/Delete | Excluir um grupo de regras de política de firewall |
> | Ação | Microsoft. Network/firewallPolicies/grupo/Read | Obter um grupo de regras de política de firewall |
> | Ação | Microsoft. Network/firewallPolicies/grupo/Write | Cria um grupo de regras de política de firewall ou atualiza um grupo de regras de política de firewall existente |
> | Ação | Microsoft. Network/firewallPolicies/Write | Cria uma política de firewall ou atualiza uma política de firewall existente |
> | Ação | Microsoft.Network/frontDoors/backendPools/delete | Exclui um pool de back-end |
> | Ação | Microsoft.Network/frontDoors/backendPools/read | Obter um pool de back-end |
> | Ação | Microsoft.Network/frontDoors/backendPools/write | Criar ou atualizar um pool de back-end |
> | Ação | Microsoft.Network/frontDoors/delete | Exclui uma porta frontal |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/delete | Exclui um ponto de extremidade de front-end |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Desabilita HTTPS em um ponto de extremidade de front-end |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Habilita HTTPS em um ponto de extremidade de front-end |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/read | Obtém um ponto de extremidade de front-end |
> | Ação | Microsoft.Network/frontDoors/frontendEndpoints/write | Cria ou atualiza um ponto de extremidade de front-end |
> | Ação | Microsoft.Network/frontDoors/healthProbeSettings/delete | Exclui as configurações de investigação de integridade |
> | Ação | Microsoft.Network/frontDoors/healthProbeSettings/read | Obtém as configurações de investigação de integridade |
> | Ação | Microsoft.Network/frontDoors/healthProbeSettings/write | Criar ou atualizar configurações de investigação de integridade |
> | Ação | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Criar ou atualizar configurações de balanceamento de carga |
> | Ação | Microsoft.Network/frontDoors/loadBalancingSettings/read | Obtém as configurações de balanceamento de carga |
> | Ação | Microsoft.Network/frontDoors/loadBalancingSettings/write | Criar ou atualizar configurações de balanceamento de carga |
> | Ação | Microsoft.Network/frontDoors/purge/action | Limpar o conteúdo armazenado em cache de uma porta frontal |
> | Ação | Microsoft.Network/frontDoors/read | Obter uma porta frontal |
> | Ação | Microsoft.Network/frontDoors/routingRules/delete | Excluir uma regra de roteamento |
> | Ação | Microsoft.Network/frontDoors/routingRules/read | Obter uma regra de roteamento |
> | Ação | Microsoft.Network/frontDoors/routingRules/write | Criar ou atualizar uma regra de roteamento |
> | Ação | Microsoft.Network/frontDoors/validateCustomDomain/action | Valida um ponto de extremidade de front-end para uma porta frontal |
> | Ação | Microsoft.Network/frontDoors/write | Criar ou atualizar uma porta frontal |
> | Ação | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Obter conjuntos de regras gerenciadas pelo firewall do aplicativo Web |
> | Ação | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Exclui uma política de firewall do aplicativo Web |
> | Ação | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/junção/ação | Une uma política de firewall do aplicativo Web. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Obter uma política de firewall do aplicativo Web |
> | Ação | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Criar ou atualizar uma política de firewall do aplicativo Web |
> | Ação | Microsoft. Network/ipGroups/Delete | Exclui um IpGroup |
> | Ação | Microsoft. Network/ipGroups/junção/ação | Une um IpGroup. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/ipGroups/Read | Obter um IpGroup |
> | Ação | Microsoft. Network/ipGroups/Write | Cria um IpGroup ou atualiza um IpGroups existente |
> | Ação | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Une um pool de endereços de back-end do balanceador de carga. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/loadBalancers/backendAddressPools/read | Obter uma definição de pool de endereços de back-end do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/delete | Exclui um balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Une uma configuração de IP de front-end Load Balancer. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Obter uma definição de configuração de IP de front-end do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Une um pool de NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/loadBalancers/inboundNatPools/read | Obter uma definição de pool de NAT de entrada do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/delete | Exclui uma regra NAT de entrada do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Une uma regra NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/read | Obter uma definição de regra NAT de entrada do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/inboundNatRules/write | Cria uma regra NAT de entrada do balanceador de carga ou atualiza uma regra NAT de entrada do balanceador de carga existente |
> | Ação | Microsoft.Network/loadBalancers/loadBalancingRules/read | Obter uma definição de regra de balanceamento de carga do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/networkInterfaces/read | Obtém referências a todas as interfaces de rede em um balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/outboundRules/read | Obter uma definição de regra de saída do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/probes/join/action | Permite o uso de investigações de um balanceador de carga. Por exemplo, com essa permissão, a propriedade healthProbe do conjunto de dimensionamento de VM pode fazer referência à investigação. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/loadBalancers/probes/read | Obter uma investigação do balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/read | Obter uma definição de balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/virtualMachines/read | Obtém referências a todas as máquinas virtuais em um balanceador de carga |
> | Ação | Microsoft.Network/loadBalancers/write | Cria um balanceador de carga ou atualiza um balanceador de carga existente |
> | Ação | Microsoft.Network/localnetworkgateways/delete | Exclui LocalNetworkGateway |
> | Ação | Microsoft.Network/localnetworkgateways/read | Obtém LocalNetworkGateway |
> | Ação | Microsoft.Network/localnetworkgateways/write | Criar ou atualizar um LocalNetworkGateway existente |
> | Ação | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Obter serviços de vínculo privado aprovados automaticamente |
> | Ação | Microsoft.Network/locations/availableDelegations/read | Obtém as delegações disponíveis |
> | Ação | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Obtém recursos de ponto de extremidade privados disponíveis |
> | Ação | Microsoft. Network/Locations/availableServiceAliases/Read | Obtém os aliases de serviço disponíveis |
> | Ação | Microsoft.Network/locations/bareMetalTenants/action | Aloca ou valida um locatário bare-metal |
> | Ação | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Verifica o suporte à rede acelerada |
> | Ação | Microsoft.Network/locations/checkDnsNameAvailability/read | Verifica se o rótulo DNS está disponível no local especificado |
> | Ação | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Verifica a visibilidade do serviço de vínculo privado |
> | Ação | Microsoft.Network/locations/operationResults/read | Obtém o resultado da operação de uma operação de POSTAgem assíncrona ou de exclusão |
> | Ação | Microsoft.Network/locations/operations/read | Obtém o recurso de operação que representa o status de uma operação assíncrona |
> | Ação | Microsoft.Network/locations/serviceTags/read | Obter marcas de serviço |
> | Ação | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Obtém tamanhos de máquinas virtuais com suporte |
> | Ação | Microsoft.Network/locations/usages/read | Obtém as métricas de uso de recursos |
> | Ação | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Obter uma lista de serviços de ponto de extremidade de rede virtual disponíveis |
> | Ação | Microsoft.Network/networkIntentPolicies/delete | Excluir uma política de intenção de rede |
> | Ação | Microsoft.Network/networkIntentPolicies/read | Obtém uma descrição da política de intenção de rede |
> | Ação | Microsoft.Network/networkIntentPolicies/write | Cria uma política de intenção de rede ou atualiza uma política de tentativa de rede existente |
> | Ação | Microsoft.Network/networkInterfaces/delete | Exclui uma interface de rede |
> | Ação | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Obter grupos de segurança de rede configurados na interface de rede da VM |
> | Ação | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Obter tabela de rotas configurada na interface de rede da VM |
> | Ação | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Une uma configuração de IP de interface de rede. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/networkInterfaces/ipconfigurations/read | Obtém uma definição de configuração de IP de interface de rede.  |
> | Ação | Microsoft.Network/networkInterfaces/join/action | Une uma máquina virtual a uma interface de rede. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/networkInterfaces/loadBalancers/read | Obtém todos os balanceadores de carga dos quais a interface de rede faz parte |
> | Ação | Microsoft.Network/networkInterfaces/read | Obtém uma definição de interface de rede.  |
> | Ação | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Exclui uma configuração de toque de interface de rede. |
> | Ação | Microsoft.Network/networkInterfaces/tapConfigurations/read | Obtém uma configuração de toque de interface de rede. |
> | Ação | Microsoft.Network/networkInterfaces/tapConfigurations/write | Cria uma interface de rede toque em configuração ou atualiza uma interface de rede existente toque em configuração. |
> | Ação | Microsoft.Network/networkInterfaces/write | Cria uma interface de rede ou atualiza uma interface de rede existente.  |
> | Ação | Microsoft.Network/networkProfiles/delete | Exclui um perfil de rede |
> | Ação | Microsoft.Network/networkProfiles/read | Obter um perfil de rede |
> | Ação | Microsoft.Network/networkProfiles/removeContainers/action | Remove contêineres |
> | Ação | Microsoft.Network/networkProfiles/setContainers/action | Define contêineres |
> | Ação | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Define as interfaces de rede do contêiner |
> | Ação | Microsoft.Network/networkProfiles/write | Criar ou atualizar um perfil de rede |
> | Ação | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Obtém uma definição de regra de segurança padrão |
> | Ação | Microsoft.Network/networkSecurityGroups/delete | Excluir um grupo de segurança de rede |
> | Ação | Microsoft.Network/networkSecurityGroups/join/action | Une um grupo de segurança de rede. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/networkSecurityGroups/read | Obter uma definição de grupo de segurança de rede |
> | Ação | Microsoft.Network/networkSecurityGroups/securityRules/delete | Excluir uma regra de segurança |
> | Ação | Microsoft.Network/networkSecurityGroups/securityRules/read | Obter uma definição de regra de segurança |
> | Ação | Microsoft.Network/networkSecurityGroups/securityRules/write | Cria uma regra de segurança ou atualiza uma regra de segurança existente |
> | Ação | Microsoft.Network/networkSecurityGroups/write | Cria um grupo de segurança de rede ou atualiza um grupo de segurança de rede existente |
> | Ação | Microsoft.Network/networkWatchers/availableProvidersList/action | Retorna todos os provedores de serviços de Internet disponíveis para uma região do Azure especificada. |
> | Ação | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Retorna a pontuação de latência relativa para provedores de serviços de Internet de um local especificado para regiões do Azure. |
> | Ação | Microsoft.Network/networkWatchers/configureFlowLog/action | Configura o log de fluxo para um recurso de destino. |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/delete | Exclui um monitor de conexão |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Consultar a conectividade de monitoramento entre os pontos de extremidade especificados |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/read | Obter detalhes do monitor de conexão |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Iniciar Monitoramento de conectividade entre os pontos de extremidade especificados |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Parar/pausar a conectividade de monitoramento entre os pontos de extremidade especificados |
> | Ação | Microsoft.Network/networkWatchers/connectionMonitors/write | Cria um monitor de conexão |
> | Ação | Microsoft.Network/networkWatchers/connectivityCheck/action | Verifica a possibilidade de estabelecer uma conexão TCP direta de uma máquina virtual com um determinado ponto de extremidade, incluindo outra VM ou um servidor remoto arbitrário. |
> | Ação | Microsoft.Network/networkWatchers/delete | Excluir um observador de rede |
> | Ação | Microsoft. Network/networkWatchers/flowLogs/Delete | Exclui um log de fluxo |
> | Ação | Microsoft. Network/networkWatchers/flowLogs/Read | Obter detalhes do log de fluxo |
> | Ação | Microsoft. Network/networkWatchers/flowLogs/Write | Cria um log de fluxo |
> | Ação | Microsoft.Network/networkWatchers/ipFlowVerify/action | Retorna se o pacote é permitido ou negado para ou de um destino específico. |
> | Ação | Microsoft.Network/networkWatchers/lenses/delete | Exclui uma lente |
> | Ação | Microsoft.Network/networkWatchers/lenses/query/action | Consultar o monitoramento de tráfego de rede em um ponto de extremidade especificado |
> | Ação | Microsoft.Network/networkWatchers/lenses/read | Obter detalhes da lente |
> | Ação | Microsoft.Network/networkWatchers/lenses/start/action | Iniciar o monitoramento do tráfego de rede em um ponto de extremidade especificado |
> | Ação | Microsoft.Network/networkWatchers/lenses/stop/action | Parar/pausar o monitoramento do tráfego de rede em um ponto de extremidade especificado |
> | Ação | Microsoft.Network/networkWatchers/lenses/write | Cria uma lente |
> | Ação | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnóstico da configuração de rede. |
> | Ação | Microsoft.Network/networkWatchers/nextHop/action | Para um endereço IP de destino e destino especificado, retorne o tipo do próximo salto e o próximo endereço IP da esperança. |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/delete | Exclui uma captura de pacote |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Obtém informações sobre as propriedades e o status de um recurso de captura de pacote. |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/read | Obter a definição de captura de pacote |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Pare a sessão de captura de pacote em execução. |
> | Ação | Microsoft.Network/networkWatchers/packetCaptures/write | Cria uma captura de pacote |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/delete | Exclui um PingMesh |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/read | Obter detalhes do PingMesh |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/start/action | Iniciar PingMesh entre as VMs especificadas |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Parar PingMesh entre as VMs especificadas |
> | Ação | Microsoft.Network/networkWatchers/pingMeshes/write | Cria um PingMesh |
> | Ação | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Consulta do lote monitorando a conectividade entre os pontos de extremidade especificados |
> | Ação | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Obtém o status do log de fluxo em um recurso. |
> | Ação | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Obtém o resultado da solução de problemas da operação de solução de problema executada anteriormente ou em execução no momento. |
> | Ação | Microsoft.Network/networkWatchers/read | Obter a definição do observador de rede |
> | Ação | Microsoft.Network/networkWatchers/securityGroupView/action | Exiba as regras de grupo de segurança de rede configuradas e efetivas aplicadas em uma VM. |
> | Ação | Microsoft.Network/networkWatchers/topology/action | Obtém uma exibição no nível da rede de recursos e suas relações em um grupo de recursos. |
> | Ação | Microsoft.Network/networkWatchers/troubleshoot/action | Inicia a solução de problemas em um recurso de rede no Azure. |
> | Ação | Microsoft.Network/networkWatchers/write | Cria um observador de rede ou atualiza um observador de rede existente |
> | Ação | Microsoft.Network/operations/read | Obter operações disponíveis |
> | Ação | Microsoft.Network/p2sVpnGateways/delete | Exclui um P2SVpnGateway. |
> | Ação | Microsoft. Network/p2sVpnGateways/disconnectp2svpnconnections/Action | Desconectar conexões VPN P2s |
> | Ação | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Gerar perfil VPN para P2SVpnGateway |
> | Ação | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Obter uma integridade de conexão VPN P2S para P2SVpnGateway |
> | Ação | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/Action | Obter uma integridade de conexão VPN P2S detalhada para P2SVpnGateway |
> | Ação | Microsoft.Network/p2sVpnGateways/read | Obtém um P2SVpnGateway. |
> | Ação | Microsoft.Network/p2sVpnGateways/write | Coloca um P2SVpnGateway. |
> | Ação | Microsoft.Network/privateDnsOperationResults/read | Obtém os resultados de uma operação de DNS privado |
> | Ação | Microsoft.Network/privateDnsOperationStatuses/read | Obtém o status de uma operação de DNS privado |
> | Ação | Microsoft.Network/privateDnsZones/A/delete | Remova o conjunto de registros de um determinado nome e digite ' A ' de uma zona de DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/A/read | Obter o conjunto de registros do tipo ' A ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft.Network/privateDnsZones/A/write | Criar ou atualizar um conjunto de registros do tipo ' A ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/privateDnsZones/AAAA/delete | Remova o conjunto de registros de um determinado nome e digite ' AAAA ' de uma zona de DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/AAAA/read | Obtenha o conjunto de registros do tipo ' AAAA ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft.Network/privateDnsZones/AAAA/write | Criar ou atualizar um conjunto de registros do tipo ' AAAA ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/privateDnsZones/ALL/read | Obtém DNS privado conjuntos de registros entre tipos |
> | Ação | Microsoft.Network/privateDnsZones/CNAME/delete | Remova o conjunto de registros de um determinado nome e digite ' CNAME ' de uma zona de DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/CNAME/read | Obtenha o conjunto de registros do tipo ' CNAME ' dentro de uma zona de DNS privado, no formato JSON. |
> | Ação | Microsoft.Network/privateDnsZones/CNAME/write | Criar ou atualizar um conjunto de registros do tipo ' CNAME ' em uma zona DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/delete | Excluir uma zona de DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/MX/delete | Remova o conjunto de registros de um determinado nome e digite "MX" de uma zona DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/MX/read | Obtenha o conjunto de registros do tipo ' MX ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft.Network/privateDnsZones/MX/write | Criar ou atualizar um conjunto de registros do tipo ' MX ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/privateDnsZones/PTR/delete | Remova o conjunto de registros de um determinado nome e digite ' PTR ' de uma zona de DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/PTR/read | Obter o conjunto de registros do tipo ' PTR ' dentro de uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft.Network/privateDnsZones/PTR/write | Criar ou atualizar um conjunto de registros do tipo ' PTR ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/privateDnsZones/read | Obtenha as propriedades da zona de DNS privado, no formato JSON. Observe que esse comando não recupera as redes virtuais às quais a zona de DNS privado está vinculada ou os conjuntos de registros contidos na zona. |
> | Ação | Microsoft.Network/privateDnsZones/recordsets/read | Obtém DNS privado conjuntos de registros entre tipos |
> | Ação | Microsoft.Network/privateDnsZones/SOA/read | Obter o conjunto de registros do tipo ' SOA ' em uma zona de DNS privado, no formato JSON. |
> | Ação | Microsoft.Network/privateDnsZones/SOA/write | Atualizar um conjunto de registros do tipo ' SOA ' em uma zona DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/SRV/delete | Remova o conjunto de registros de um determinado nome e digite ' SRV ' de uma zona de DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/SRV/read | Obtenha o conjunto de registros do tipo ' SRV ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft.Network/privateDnsZones/SRV/write | Criar ou atualizar um conjunto de registros do tipo ' SRV ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/privateDnsZones/TXT/delete | Remova o conjunto de registros de um determinado nome e digite ' TXT ' de uma zona DNS privado. |
> | Ação | Microsoft.Network/privateDnsZones/TXT/read | Obtenha o conjunto de registros do tipo ' TXT ' dentro de uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft.Network/privateDnsZones/TXT/write | Criar ou atualizar um conjunto de registros do tipo ' TXT ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Exclua um link de zona de DNS privado para a rede virtual. |
> | Ação | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Obtenha o link de zona de DNS privado para as propriedades de rede virtual, no formato JSON. |
> | Ação | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Crie ou atualize um link de zona de DNS privado para a rede virtual. |
> | Ação | Microsoft.Network/privateDnsZones/write | Criar ou atualizar uma zona de DNS privado dentro de um grupo de recursos. Observe que esse comando não pode ser usado para criar ou atualizar links de rede virtual ou conjuntos de registros dentro da zona. |
> | Ação | Microsoft. Network/privateEndpointRedirectMaps/Read | Obtém um ponto de extremidade privado RedirectMap |
> | Ação | Microsoft. Network/privateEndpointRedirectMaps/Write | Cria um ponto de extremidade privado RedirectMap ou atualiza um ponto de extremidade privado existente RedirectMap |
> | Ação | Microsoft.Network/privateEndpoints/delete | Exclui um recurso de ponto de extremidade privado. |
> | Ação | Microsoft.Network/privateEndpoints/read | Obtém um recurso de ponto de extremidade privado. |
> | Ação | Microsoft.Network/privateEndpoints/write | Cria um novo ponto de extremidade privado ou atualiza um ponto de extremidade privado existente. |
> | Ação | Microsoft.Network/privateLinkServices/delete | Exclui um recurso de serviço de vínculo privado. |
> | Ação | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Exclui uma conexão de ponto de extremidade privado. |
> | Ação | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Obtém uma definição de conexão de ponto de extremidade particular. |
> | Ação | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Cria uma nova conexão de ponto de extremidade privado ou atualiza uma conexão de ponto de extremidade privada existente. |
> | Ação | Microsoft.Network/privateLinkServices/read | Obtém um recurso de serviço de vínculo privado. |
> | Ação | Microsoft.Network/privateLinkServices/write | Cria um novo serviço de vínculo privado ou atualiza um serviço de vínculo privado existente. |
> | Ação | Microsoft.Network/publicIPAddresses/delete | Exclui um endereço IP público. |
> | Ação | Microsoft.Network/publicIPAddresses/join/action | Une um endereço IP público. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/publicIPAddresses/read | Obtém uma definição de endereço IP público. |
> | Ação | Microsoft.Network/publicIPAddresses/write | Cria um endereço IP público ou atualiza um endereço IP público existente.  |
> | Ação | Microsoft.Network/publicIPPrefixes/delete | Exclui um prefixo IP público |
> | Ação | Microsoft.Network/publicIPPrefixes/join/action | Une um PublicIPPrefix. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/publicIPPrefixes/read | Obter uma definição de prefixo de IP público |
> | Ação | Microsoft.Network/publicIPPrefixes/write | Cria um prefixo IP público ou atualiza um prefixo IP público existente |
> | Ação | Microsoft.Network/register/action | Registra a assinatura |
> | Ação | Microsoft.Network/routeFilters/delete | Exclui uma definição de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/join/action | Une um filtro de rota. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/routeFilters/read | Obter uma definição de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/routeFilterRules/delete | Exclui uma definição de regra de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/routeFilterRules/read | Obter uma definição de regra de filtro de rota |
> | Ação | Microsoft.Network/routeFilters/routeFilterRules/write | Cria uma regra de filtro de rota ou atualiza uma regra de filtro de rota existente |
> | Ação | Microsoft.Network/routeFilters/write | Cria um filtro de rota ou atualiza um filtro de rota existente |
> | Ação | Microsoft.Network/routeTables/delete | Exclui uma definição de tabela de rotas |
> | Ação | Microsoft.Network/routeTables/join/action | Une uma tabela de rotas. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/routeTables/read | Obter uma definição de tabela de rotas |
> | Ação | Microsoft.Network/routeTables/routes/delete | Exclui uma definição de rota |
> | Ação | Microsoft.Network/routeTables/routes/read | Obter uma definição de rota |
> | Ação | Microsoft.Network/routeTables/routes/write | Cria uma rota ou atualiza uma rota existente |
> | Ação | Microsoft.Network/routeTables/write | Cria uma tabela de rotas ou atualiza uma tabela de rotas existente |
> | Ação | Microsoft.Network/serviceEndpointPolicies/delete | Exclui uma política de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/join/action | Une uma política de ponto de extremidade de serviço. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Une uma sub-rede a políticas de ponto de extremidade de serviço. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/serviceEndpointPolicies/read | Obtém uma descrição da política de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Exclui uma definição de política de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Obtém uma descrição de definição de política de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Cria uma definição de política de ponto de extremidade de serviço ou atualiza uma definição de política de ponto de extremidade de serviço |
> | Ação | Microsoft.Network/serviceEndpointPolicies/write | Cria uma política de ponto de extremidade de serviço ou atualiza uma política de ponto de extremidade de serviço existente |
> | Ação | Microsoft.Network/trafficManagerGeographicHierarchies/read | Obtém a hierarquia geográfica do Gerenciador de tráfego que contém regiões que podem ser usadas com o método de roteamento de tráfego geográfico |
> | Ação | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Exclui um Ponto de Extremidade do Azure de um perfil do Gerenciador de tráfego existente. O Gerenciador de tráfego deixará de rotear o tráfego para o Ponto de Extremidade do Azure excluído. |
> | Ação | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Obtém um Ponto de Extremidade do Azure que pertence a um perfil do Gerenciador de tráfego, incluindo todas as propriedades desse Ponto de Extremidade do Azure. |
> | Ação | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Adicione um novo Ponto de Extremidade do Azure em um perfil existente do Gerenciador de tráfego ou atualize as propriedades de um Ponto de Extremidade do Azure existente nesse perfil do Gerenciador de tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/delete | Exclua o perfil do Gerenciador de tráfego. Todas as configurações associadas ao perfil do Gerenciador de tráfego serão perdidas e o perfil não poderá mais ser usado para rotear o tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Exclui um ponto de extremidade externo de um perfil do Gerenciador de tráfego existente. O Gerenciador de tráfego deixará de rotear o tráfego para o ponto de extremidade externo excluído. |
> | Ação | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Obtém um ponto de extremidade externo que pertence a um perfil do Gerenciador de tráfego, incluindo todas as propriedades desse ponto de extremidade externo. |
> | Ação | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Adicione um novo ponto de extremidade externo em um perfil existente do Gerenciador de tráfego ou atualize as propriedades de um ponto de extremidade externo existente nesse perfil do Gerenciador de tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Obtém o mapa de calor do Gerenciador de tráfego para o perfil do Gerenciador de tráfego fornecido que contém contagens de consulta e dados de latência por local e IP de origem. |
> | Ação | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Exclui um ponto de extremidade aninhado de um perfil do Gerenciador de tráfego existente. O Gerenciador de tráfego irá parar o roteamento do tráfego para o ponto de extremidade aninhado excluído. |
> | Ação | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Obtém um ponto de extremidade aninhado que pertence a um perfil do Gerenciador de tráfego, incluindo todas as propriedades desse ponto de extremidade aninhado. |
> | Ação | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Adicione um novo ponto de extremidade aninhado em um perfil existente do Gerenciador de tráfego ou atualize as propriedades de um ponto de extremidade aninhado existente no perfil do Gerenciador de tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/read | Obtenha a configuração do perfil do Gerenciador de tráfego. Isso inclui configurações de DNS, configurações de roteamento de tráfego, configurações de monitoramento de ponto de extremidade e a lista de pontos de extremidade roteados por esse perfil do Gerenciador de tráfego. |
> | Ação | Microsoft.Network/trafficManagerProfiles/write | Criar um perfil do Gerenciador de tráfego ou modificar a configuração de um perfil existente do Gerenciador de tráfego.<br>Isso inclui habilitar ou desabilitar um perfil e modificar configurações de DNS, configurações de roteamento de tráfego ou configurações de monitoramento de ponto de extremidade.<br>Os pontos de extremidade roteados pelo perfil do Gerenciador de tráfego podem ser adicionados, removidos, habilitados ou desabilitados. |
> | Ação | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Exclui a chave de nível de assinatura usada para a coleta de métricas de usuário em tempo real. |
> | Ação | Microsoft.Network/trafficManagerUserMetricsKeys/read | Obtém a chave de nível de assinatura usada para a coleta de métricas de usuário em tempo real. |
> | Ação | Microsoft.Network/trafficManagerUserMetricsKeys/write | Cria uma nova chave de nível de assinatura a ser usada para a coleta de métricas de usuário em tempo real. |
> | Ação | Microsoft.Network/unregister/action | Cancela o registro da assinatura |
> | Ação | Microsoft.Network/virtualHubs/delete | Excluir um hub virtual |
> | Ação | Microsoft. Network/virtualHubs/effectiveRoutes/Action | Obtém a rota efetiva configurada no Hub virtual |
> | Ação | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Exclui um HubVirtualNetworkConnection |
> | Ação | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Obter um HubVirtualNetworkConnection |
> | Ação | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Criar ou atualizar um HubVirtualNetworkConnection |
> | Ação | Microsoft.Network/virtualHubs/read | Obter um hub virtual |
> | Ação | Microsoft. Network/virtualHubs/routeTables/Delete | Excluir um VirtualHubRouteTableV2 |
> | Ação | Microsoft. Network/virtualHubs/routeTables/Read | Obter um VirtualHubRouteTableV2 |
> | Ação | Microsoft. Network/virtualHubs/routeTables/Write | Criar ou atualizar um VirtualHubRouteTableV2 |
> | Ação | Microsoft.Network/virtualHubs/write | Criar ou atualizar um hub virtual |
> | Ação | microsoft.network/virtualnetworkgateways/connections/read | Obter VirtualNetworkGatewayConnection |
> | Ação | Microsoft.Network/virtualNetworkGateways/delete | Exclui um virtualNetworkGateway |
> | Ação | Microsoft. Network/virtualnetworkgateways/disconnectvirtualnetworkgatewayvpnconnections/Action | Desconectar conexões VPN do gateway de rede virtual |
> | Ação | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Gerar pacote VpnClient para virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Gerar pacote VpnProfile para VirtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Obtém rotas anunciadas do virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Obtém o status do par BGP virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Obtém rotas aprendidas do virtualnetworkgateway |
> | Ação | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Obter integridade de conexão de cliente VPN para VirtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Obter parâmetros de IPSec vpnclient para o cliente VirtualNetworkGateway P2S. |
> | Ação | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Obtém a URL de um pacote de perfil de cliente VPN gerado previamente |
> | Ação | Microsoft.Network/virtualNetworkGateways/read | Obter um VirtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/reset/action | Redefine um virtualNetworkGateway |
> | Ação | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Redefina a chave compartilhada vpnclient para o cliente VirtualNetworkGateway P2S. |
> | Ação | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Defina os parâmetros IPsec do vpnclient para o cliente VirtualNetworkGateway P2S. |
> | Ação | Microsoft. Network/virtualnetworkgateways/startpacketcapture/Action | Inicia uma captura de pacotes do gateway de rede virtual. |
> | Ação | Microsoft. Network/virtualnetworkgateways/stoppacketcapture/Action | Interrompe uma captura de pacotes do gateway de rede virtual. |
> | Ação | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Lista dispositivos VPN com suporte |
> | Ação | Microsoft.Network/virtualNetworkGateways/write | Criar ou atualizar um VirtualNetworkGateway |
> | Ação | Microsoft.Network/virtualNetworks/BastionHosts/action | Obtém referências de host bastião em uma rede virtual. |
> | Ação | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Obtém referências de host bastião em uma rede virtual. |
> | Ação | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Verificar se o endereço IP está disponível na rede virtual especificada |
> | Ação | Microsoft.Network/virtualNetworks/delete | Excluir uma rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/junção/ação | Une uma rede virtual. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/virtualNetworks/peer/action | Os pares de uma rede virtual com outra rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/contextualServiceEndpointPolicies/Delete | Exclui uma política de ponto de extremidade de serviço contextual |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/contextualServiceEndpointPolicies/Read | Obtém políticas de ponto de extremidade de serviço contextual |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/contextualServiceEndpointPolicies/Write | Cria uma política de ponto de extremidade de serviço contextual ou atualiza uma política de ponto de extremidade de serviço contextual existente |
> | Ação | Microsoft.Network/virtualNetworks/subnets/delete | Excluir uma sub-rede de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/subnets/join/action | Une uma rede virtual. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Une recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Prepara uma sub-rede aplicando as políticas de rede necessárias |
> | Ação | Microsoft.Network/virtualNetworks/subnets/read | Obter uma definição de sub-rede de rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/unprepareNetworkPolicies/Action | Despreparar uma sub-rede removendo as políticas de rede aplicadas |
> | Ação | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Obtém referências a todas as máquinas virtuais em uma sub-rede de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/subnets/write | Cria uma sub-rede de rede virtual ou atualiza uma sub-rede de rede virtual existente |
> | Ação | Microsoft.Network/virtualNetworks/usages/read | Obter os usos de IP para cada sub-rede da rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualMachines/read | Obtém referências a todas as máquinas virtuais em uma rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Exclui um emparelhamento de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Obter uma definição de emparelhamento de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Cria um emparelhamento de rede virtual ou atualiza um emparelhamento de rede virtual existente |
> | Ação | Microsoft.Network/virtualNetworks/write | Cria uma rede virtual ou atualiza uma rede virtual existente |
> | Ação | Microsoft.Network/virtualNetworkTaps/delete | Excluir toque de rede virtual |
> | Ação | Microsoft.Network/virtualNetworkTaps/join/action | Une um toque de rede virtual. Não é possível alertá-lo. |
> | Ação | Microsoft.Network/virtualNetworkTaps/read | Obter toque de rede virtual |
> | Ação | Microsoft.Network/virtualNetworkTaps/write | Criar ou atualizar toque de rede virtual |
> | Ação | Microsoft. Network/virtualRouters/Delete | Exclui um VirtualRouter |
> | Ação | Microsoft. Network/virtualRouters/junção/ação | Une um VirtualRouter. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/virtualRouters/emparelhamentos/exclusão | Exclui um VirtualRouterPeering |
> | Ação | Microsoft. Network/virtualRouters/emparelhamentos/leitura | Obter um VirtualRouterPeering |
> | Ação | Microsoft. Network/virtualRouters/emparelhamentos/gravação | Cria um VirtualRouterPeering ou atualiza um VirtualRouterPeering existente |
> | Ação | Microsoft. Network/virtualRouters/Read | Obter um VirtualRouter |
> | Ação | Microsoft. Network/virtualRouters/Write | Cria um VirtualRouter ou atualiza um VirtualRouter existente |
> | Ação | Microsoft.Network/virtualWans/delete | Exclui uma WAN virtual |
> | Ação | Microsoft. Network/virtualwans/generateVpnProfile/Action | Gerar VirtualWanVpnServerConfiguration VpnProfile |
> | Ação | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Exclui um P2SVpnServerConfiguration de WAN virtual |
> | Ação | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Obter um P2SVpnServerConfiguration de WAN virtual |
> | Ação | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Cria um P2SVpnServerConfiguration de WAN virtual ou atualiza um P2SVpnServerConfiguration de WAN virtual existente |
> | Ação | Microsoft.Network/virtualWans/read | Obter uma WAN virtual |
> | Ação | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Obtém provedores de segurança VirtualWan com suporte. |
> | Ação | Microsoft.Network/virtualWans/virtualHubs/read | Obtém todos os hubs virtuais que fazem referência a uma WAN virtual. |
> | Ação | Microsoft.Network/virtualwans/vpnconfiguration/action | Obter uma configuração de VPN |
> | Ação | Microsoft. Network/virtualwans/vpnServerConfigurations/Action | Obter VirtualWanVpnServerConfigurations |
> | Ação | Microsoft.Network/virtualWans/vpnSites/read | Obtém todos os sites VPN que fazem referência a uma WAN virtual. |
> | Ação | Microsoft.Network/virtualWans/write | Criar ou atualizar uma WAN virtual |
> | Ação | Microsoft.Network/vpnGateways/delete | Exclui um VpnGateway. |
> | Ação | microsoft.network/vpngateways/listvpnconnectionshealth/action | Obtém a integridade da conexão para todos ou um subconjunto de conexões em um VpnGateway |
> | Ação | Microsoft.Network/vpnGateways/read | Obtém um VpnGateway. |
> | Ação | microsoft.network/vpngateways/reset/action | Redefine um VpnGateway |
> | Ação | microsoft.network/vpnGateways/vpnConnections/delete | Exclui um VpnConnection. |
> | Ação | microsoft.network/vpnGateways/vpnConnections/read | Obtém um VpnConnection. |
> | Ação | Microsoft. Network/vpnGateways/vpnConnections/vpnLinkConnections/Read | Obter uma conexão de link VPN |
> | Ação | microsoft.network/vpnGateways/vpnConnections/write | Coloca um VpnConnection. |
> | Ação | Microsoft.Network/vpnGateways/write | Coloca um VpnGateway. |
> | Ação | Microsoft. Network/vpnServerConfigurations/Delete | Excluir VpnServerConfiguration |
> | Ação | Microsoft. Network/vpnServerConfigurations/Read | Obter VpnServerConfiguration |
> | Ação | Microsoft. Network/vpnServerConfigurations/Write | Criar ou atualizar VpnServerConfiguration |
> | Ação | Microsoft.Network/vpnsites/delete | Exclui um recurso de site VPN. |
> | Ação | Microsoft.Network/vpnsites/read | Obtém um recurso de site VPN. |
> | Ação | Microsoft. Network/vpnSites/vpnSiteLinks/Read | Obter um link de site VPN |
> | Ação | Microsoft.Network/vpnsites/write | Cria ou atualiza um recurso de site de VPN. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Verifica se um determinado nome de recurso de namespace está disponível no serviço NotificationHub. |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Obtenha a lista de descrições de regras de autorização de namespaces. |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização de namespace padrão não pode ser excluída.  |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Obtenha a lista de descrições de regras de autorização de namespaces. |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Regra de autorização de namespace regenerar Primary/SecondaryKey, especifique a chave que precisa ser regenerada |
> | Ação | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Crie regras de autorização de nível de namespace e atualize suas propriedades. Os direitos de acesso às regras de autorização, as chaves primária e secundária podem ser atualizadas. |
> | Ação | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Verifica se um determinado nome de NotificationHub está disponível dentro de um namespace. |
> | Ação | Microsoft.NotificationHubs/Namespaces/Delete | Excluir recurso de namespace |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Obter a lista de regras de autorização do hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Excluir regras de autorização do hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Obter a cadeia de conexão para o Hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Obter a lista de regras de autorização do hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Regra de autorização de Hub de notificação regenerar Primary/SecondaryKey, especifique a chave que precisa ser regenerada |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Crie regras de autorização do hub de notificação e atualize suas propriedades. Os direitos de acesso às regras de autorização, as chaves primária e secundária podem ser atualizadas. |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Enviar uma notificação por push de teste. |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Excluir recurso do hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Obter lista de descrições de recursos de métricas de namespace |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Obter todas as credenciais de PNS do hub de notificação. Isso inclui as credenciais WNS, MPNS, APNS, GCM e Baidu |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Obter lista de descrições de recursos do hub de notificação |
> | Ação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Crie um hub de notificação e atualize suas propriedades. Suas propriedades incluem principalmente as credenciais PNS. Regras de autorização e TTL |
> | Ação | Microsoft.NotificationHubs/Namespaces/read | Obter a lista de descrição de recurso de namespace |
> | Ação | Microsoft.NotificationHubs/Namespaces/write | Crie um recurso de namespace e atualize suas propriedades. As marcas e a capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft.NotificationHubs/operationResults/read | Retorna os resultados da operação para o provedor de hubs de notificação |
> | Ação | Microsoft.NotificationHubs/operations/read | Retorna uma lista de operações com suporte para o provedor de hubs de notificação |
> | Ação | Microsoft.NotificationHubs/register/action | Registra a assinatura para o provedor de recursos NotificationHubs e habilita a criação de namespaces e NotificationHubs |
> | Ação | Microsoft.NotificationHubs/unregister/action | Cancela o registro da assinatura para o provedor de recursos NotificationHubs e habilita a criação de namespaces e NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.OffAzure/HyperVSites/clusters/read | Obter as propriedades de um cluster Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/clusters/write | Criar ou atualizar o cluster do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/delete | Exclui o site do Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/healthsummary/Read | Obter o resumo de integridade do recurso Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/hosts/read | Obter as propriedades de um host Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/hosts/write | Criar ou atualizar o host Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/jobs/read | Obter as propriedades de um trabalho do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/machines/read | Obter as propriedades de uma máquina Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Obtém as propriedades de um status de operação do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/read | Obter as propriedades de um site do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/refresh/action | Atualiza os objetos em um site do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Obtém as propriedades de uma conta Executar como do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/usage/read | Obtém os usos de um site do Hyper-V |
> | Ação | Microsoft.OffAzure/HyperVSites/write | Criar ou atualizar o site do Hyper-V |
> | Ação | Microsoft.OffAzure/Operations/read | Lê as operações expostas |
> | Ação | Microsoft.OffAzure/register/action | Registra a assinatura com o provedor de recursos Microsoft. OffAzure |
> | Ação | Microsoft. OffAzure/ServerSites/Jobs/ler | Obtém as propriedades de um servidor de trabalhos |
> | Ação | Microsoft. OffAzure/ServerSites/Machines/Read | Obter as propriedades de uma máquina de servidor |
> | Ação | Microsoft. OffAzure/ServerSites/Machines/Write | Gravar as propriedades de uma máquina de servidor |
> | Ação | Microsoft. OffAzure/ServerSites/operationsstatus/Read | Obtém as propriedades de um status de operação do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/Read | Obtém as propriedades de um site do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/atualização/ação | Atualiza os objetos em um site do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/runasaccounts/Read | Obtém as propriedades de uma conta Executar como do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/uso/leitura | Obtém os usos de um site do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/Write | Criar ou atualizar o site do servidor |
> | Ação | Microsoft.OffAzure/VMwareSites/delete | Exclui o site do VMware |
> | Ação | Microsoft. OffAzure/VMwareSites/healthsummary/Read | Obter o resumo de integridade do recurso do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/jobs/read | Obter as propriedades de um trabalho do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/read | Obter as propriedades de uma máquina VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/start/action | Iniciar máquinas VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/machines/stop/action | Para as máquinas VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Obtém as propriedades de um status de operação do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/read | Obter as propriedades de um site VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/refresh/action | Atualiza os objetos em um site VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Obter as propriedades de uma conta Executar como do VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/usage/read | Obtém os usos de um site VMware |
> | Ação | Microsoft.OffAzure/VMwareSites/vcenters/read | Obter as propriedades de um VMware vCenter |
> | Ação | Microsoft.OffAzure/VMwareSites/vcenters/write | Criar ou atualizar o VMware vCenter |
> | Ação | Microsoft.OffAzure/VMwareSites/write | Cria ou atualiza o site do VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.OperationalInsights/linkTargets/read | Lista as contas existentes que não estão associadas a uma assinatura do Azure. Para vincular essa assinatura do Azure a um espaço de trabalho, use uma ID de cliente retornada por essa operação na propriedade ID do cliente da operação criar espaço de trabalho. |
> | Ação | microsoft.operationalinsights/operations/read | Lista todas as operações de API REST do OperationalInsights disponíveis. |
> | Ação | microsoft.operationalinsights/register/action | Rergisters a assinatura. |
> | Ação | Microsoft.OperationalInsights/register/action | Registrar uma assinatura em um provedor de recursos. |
> | Ação | microsoft.operationalinsights/unregister/action | Cancela o registro da assinatura. |
> | Ação | Microsoft.OperationalInsights/workspaces/analytics/query/action | Pesquisar usando o novo mecanismo. |
> | Ação | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Obter o esquema de pesquisa v2. |
> | Ação | Microsoft.OperationalInsights/workspaces/api/query/action | Pesquisar usando o novo mecanismo. |
> | Ação | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Obter o esquema de pesquisa v2. |
> | Ação | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Excluir escopo de configuração |
> | Ação | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Obter escopo de configuração |
> | Ação | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Definir escopo de configuração |
> | Ação | Microsoft.OperationalInsights/workspaces/datasources/delete | Exclua fontes de fonte de um espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/datasources/read | Obter fontes de origem em um espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/datasources/write | Criar/atualizar fontes de origem em um espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/delete | Exclui um espaço de trabalho. Se o espaço de trabalho tiver sido vinculado a um espaço de trabalho existente no momento da criação, o espaço de trabalho ao qual ele estava vinculado não será excluído. |
> | Ação | Microsoft.OperationalInsights/workspaces/gateways/delete | Remove um gateway configurado para o espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Gera o certificado de registro para o espaço de trabalho. Esse certificado é usado para conectar o Gerenciador de operações do Microsoft System Center ao espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Desabilita um pacote de inteligência para um determinado espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Habilita um pacote de inteligência para um determinado espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Lista todos os pacotes de inteligência visíveis para um determinado espaço de trabalho e também lista se o pacote está habilitado ou desabilitado para esse espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Exclua os serviços vinculados em determinado espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/linkedServices/read | Obter serviços vinculados em determinado espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/linkedServices/write | Criar/atualizar serviços vinculados em determinado espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/listKeys/action | Recupera as chaves de lista para o espaço de trabalho. Essas chaves são usadas para conectar agentes do insights operacionais da Microsoft ao espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/listKeys/read | Recupera as chaves de lista para o espaço de trabalho. Essas chaves são usadas para conectar agentes do insights operacionais da Microsoft ao espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/managementGroups/read | Obtém os nomes e os metadados para System Center Operations Manager grupos de gerenciamento conectados a este espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Obter definições de métrica no espaço de trabalho |
> | Ação | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Exclua as configurações de notificação do usuário para o espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Obtenha as configurações de notificação do usuário para o espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Defina as configurações de notificação do usuário para o espaço de trabalho. |
> | Ação | microsoft.operationalinsights/workspaces/operations/read | Obtém o status de uma operação de espaço de trabalho OperationalInsights. |
> | Ação | Microsoft.OperationalInsights/workspaces/purge/action | Excluir dados especificados do espaço de trabalho |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Ler dados da tabela AADDomainServicesAccountLogon |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | Ler dados da tabela AADDomainServicesAccountManagement |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Ler dados da tabela AADDomainServicesDirectoryServiceAccess |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Ler dados da tabela AADDomainServicesLogonLogoff |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | Ler dados da tabela AADDomainServicesPolicyChange |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Ler dados da tabela AADDomainServicesPrivilegeUse |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Ler dados da tabela AADDomainServicesSystemSecurity |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Ler dados da tabela ADAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupAlerts/Read | Ler dados da tabela AddonAzureBackupAlerts |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupJobs/Read | Ler dados da tabela AddonAzureBackupJobs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupPolicy/Read | Ler dados da tabela AddonAzureBackupPolicy |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupProtectedInstance/Read | Ler dados da tabela AddonAzureBackupProtectedInstance |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupStorage/Read | Ler dados da tabela AddonAzureBackupStorage |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Ler dados da tabela ADFActivityRun |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Ler dados da tabela ADFPipelineRun |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Ler dados da tabela ADFTriggerRun |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Ler dados da tabela ADReplicationResult |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Ler dados da tabela ADSecurityAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AegDeliveryFailureLogs/Read | Ler dados da tabela AegDeliveryFailureLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AegPublishFailureLogs/Read | Ler dados da tabela AegPublishFailureLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Alert/read | Ler dados da tabela de alertas |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Ler dados da tabela AlertHistory |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeClusterEvent/Read | Ler dados da tabela AmlComputeClusterEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeClusterNodeEvent/Read | Ler dados da tabela AmlComputeClusterNodeEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeJobEvent/Read | Ler dados da tabela AmlComputeJobEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ApiManagementGatewayLogs/Read | Ler dados da tabela ApiManagementGatewayLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Ler dados da tabela AppCenterError |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Ler dados da tabela ApplicationInsights |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AppPlatformLogsforSpring/Read | Ler dados da tabela AppPlatformLogsforSpring |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AppPlatformSystemLogs/Read | Ler dados da tabela AppPlatformSystemLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AppServiceAppLogs/Read | Ler dados da tabela AppServiceAppLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AppServiceAuditLogs/Read | Ler dados da tabela AppServiceAuditLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AppServiceConsoleLogs/Read | Ler dados da tabela AppServiceConsoleLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AppServiceEnvironmentPlatformLogs/Read | Ler dados da tabela AppServiceEnvironmentPlatformLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AppServiceHTTPLogs/Read | Ler dados da tabela AppServiceHTTPLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Ler dados da tabela AuditLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Ler dados da tabela AutoscaleEvaluationsLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Ler dados da tabela AutoscaleScaleActionsLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Ler dados da tabela AWSCloudTrail |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Ler dados da tabela AzureActivity |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Ler dados da tabela AzureAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Ler dados da tabela AzureMetrics |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/BaiClusterEvent/Read | Ler dados da tabela BaiClusterEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/BaiClusterNodeEvent/Read | Ler dados da tabela BaiClusterNodeEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/BaiJobEvent/Read | Ler dados da tabela BaiJobEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | Ler dados da tabela BlockchainApplicationLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | Ler dados da tabela BlockchainProxyLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Ler dados da tabela BoundPort |
> | Ação | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Ler dados da tabela CommonSecurityLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Ler dados da tabela do computador |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Ler dados da tabela ConfigurationChange |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Ler dados da tabela ConfigurationData |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Ler dados da tabela ContainerImageInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Ler dados da tabela ContainerInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Ler dados da tabela ContainerLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Ler dados da tabela ContainerNodeInventory |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ContainerRegistryLoginEvents/Read | Ler dados da tabela ContainerRegistryLoginEvents |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ContainerRegistryRepositoryEvents/Read | Ler dados da tabela ContainerRegistryRepositoryEvents |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Ler dados da tabela ContainerServiceLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/CoreAzureBackup/Read | Ler dados da tabela CoreAzureBackup |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Ler dados da tabela DatabricksAccounts |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | Ler dados da tabela DatabricksClusters |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | Ler dados da tabela DatabricksDBFS |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DatabricksInstancePools/Read | Ler dados da tabela DatabricksInstancePools |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Ler dados da tabela DatabricksJobs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | Ler dados da tabela DatabricksNotebook |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Ler dados da tabela DatabricksSecrets |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Ler dados da tabela DatabricksSQLPermissions |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | Ler dados da tabela DatabricksSSH |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | Ler dados da tabela DatabricksTables |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Ler dados da tabela DatabricksWorkspace |
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
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DHCPActivity/Read | Ler dados da tabela DHCPActivity |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Ler dados da tabela DHDriverReliability |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Ler dados da tabela DHLogonFailures |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Ler dados da tabela DHLogonMetrics |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Ler dados da tabela DHOSCrashData |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Ler dados da tabela DHOSReliability |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Ler dados da tabela DHWipAppLearning |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Ler dados da tabela DnsEvents |
> | Ação | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Ler dados da tabela DnsInventory |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Ler dados da tabela ETWEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Event/read | Ler dados da tabela de eventos |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Ler dados da tabela ExchangeAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Ler dados da tabela ExchangeOnlineAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/FailedIngestion/Read | Ler dados da tabela FailedIngestion |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/FunctionAppLogs/Read | Ler dados da tabela FunctionAppLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Ler dados da tabela de pulsação |
> | Ação | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Ler dados da tabela HuntingBookmark |
> | Ação | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Ler dados da tabela IISAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Ler dados da tabela InboundConnection |
> | Ação | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Ler dados da tabela InsightsMetrics |
> | Ação | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Ler dados da tabela IntuneAuditLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/IntuneDeviceComplianceOrg/Read | Ler dados da tabela IntuneDeviceComplianceOrg |
> | Ação | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Ler dados da tabela IntuneOperationalLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Ler dados da tabela KubeEvents |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/KubeHealth/Read | Ler dados da tabela KubeHealth |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/KubeMonAgentEvents/Read | Ler dados da tabela KubeMonAgentEvents |
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
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Ler dados da tabela MADeviceNRT |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Ler dados da tabela MADevicePnPHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Ler dados da tabela MADevicePnPHealthIssues |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Ler dados da tabela MADeviceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Ler dados da tabela MADriverInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Ler dados da tabela MADriverReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Ler dados da tabela MAOfficeAddin |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Ler dados da tabela MAOfficeAddinEntityHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Ler dados da tabela MAOfficeAddinHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Ler dados da tabela MAOfficeAddinHealthEventNRT |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Ler dados da tabela MAOfficeAddinHealthIssues |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Ler dados da tabela MAOfficeAddinInstance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Ler dados da tabela MAOfficeAddinReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Ler dados da tabela MAOfficeApp |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Ler dados da tabela MAOfficeAppCrashesNRT |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Ler dados da tabela MAOfficeAppHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Ler dados da tabela MAOfficeAppInstance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Ler dados da tabela MAOfficeAppInstanceHealth |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Ler dados da tabela MAOfficeAppReadiness |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Ler dados da tabela MAOfficeAppSessionsNRT |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Ler dados da tabela MAOfficeBuildInfo |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Ler dados da tabela MAOfficeCurrencyAssessment |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Ler dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Ler dados da tabela MAOfficeDeploymentStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Ler dados da tabela MAOfficeDeploymentStatusNRT |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Ler dados da tabela MAOfficeMacroErrorNRT |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Ler dados da tabela MAOfficeMacroGlobalHealth |
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
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Ler dados da tabela MAWindowsDeploymentStatusNRT |
> | Ação | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/McasShadowItReporting/Read | Ler dados da tabela McasShadowItReporting |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftAzureBastionAuditLogs/Read | Ler dados da tabela MicrosoftAzureBastionAuditLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDataShareReceivedSnapshotLog/Read | Ler dados da tabela MicrosoftDataShareReceivedSnapshotLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDataShareSentSnapshotLog/Read | Ler dados da tabela MicrosoftDataShareSentSnapshotLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDataShareShareLog/Read | Ler dados da tabela MicrosoftDataShareShareLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDynamicsTelemetryPerformanceLogs/Read | Ler dados da tabela MicrosoftDynamicsTelemetryPerformanceLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDynamicsTelemetrySystemMetricsLogs/Read | Ler dados da tabela MicrosoftDynamicsTelemetrySystemMetricsLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftHealthcareApisAuditLogs/Read | Ler dados da tabela MicrosoftHealthcareApisAuditLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftInsightsAzureActivityLog/Read | Ler dados da tabela MicrosoftInsightsAzureActivityLog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Ler dados da tabela NetworkMonitoring |
> | Ação | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Ler dados da tabela OfficeActivity |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Operation/read | Ler dados da tabela de operações |
> | Ação | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Ler dados da tabela OutboundConnection |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Perf/read | Ler dados da tabela perf |
> | Ação | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Ler dados da tabela ProtectionStatus |
> | Ação | Microsoft.OperationalInsights/workspaces/query/read | Executar consultas sobre os dados no espaço de trabalho |
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
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SignalRServiceDiagnosticLogs/Read | Ler dados da tabela SignalRServiceDiagnosticLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Ler dados da tabela SigninLogs |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Ler dados da tabela SPAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Ler dados da tabela SQLAssessmentRecommendation |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Ler dados da tabela SQLQueryPerformance |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Ler dados da tabela SqlThreatProtectionLoginAudits |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Ler dados da tabela SqlVulnerabilityAssessmentResult |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/StorageBlobLogs/Read | Ler dados da tabela StorageBlobLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/StorageFileLogs/Read | Ler dados da tabela StorageFileLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/StorageQueueLogs/Read | Ler dados da tabela StorageQueueLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/StorageTableLogs/Read | Ler dados da tabela StorageTableLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SucceededIngestion/Read | Ler dados da tabela SucceededIngestion |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Ler dados da tabela syslog |
> | Ação | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Ler dados da tabela SysmonEvent |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Lendo dados de qualquer log personalizado |
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
> | Ação | Microsoft.OperationalInsights/workspaces/query/Update/read | Ler dados da tabela de atualização |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Ler dados da tabela UpdateRunProgress |
> | Ação | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Ler dados da tabela UpdateSummary |
> | Ação | Microsoft.OperationalInsights/workspaces/query/Usage/read | Ler dados da tabela de uso |
> | Ação | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Ler dados da tabela VMBoundPort |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/VMComputer/Read | Ler dados da tabela VMComputer |
> | Ação | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Ler dados da tabela VMConnection |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/VMProcess/Read | Ler dados da tabela VMProcess |
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
> | Ação | Microsoft.OperationalInsights/workspaces/read | Obter um espaço de trabalho existente |
> | Ação | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Regenera a chave compartilhada do espaço de trabalho especificada |
> | Ação | microsoft.operationalinsights/workspaces/rules/read | Obter todas as regras de alerta. |
> | Ação | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Exclui uma consulta de pesquisa salva |
> | Ação | Microsoft.OperationalInsights/workspaces/savedSearches/read | Obtém uma consulta de pesquisa salva |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/results/read | Obter resultados de pesquisas salvas. Preterido |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Exclua as ações de pesquisa agendadas. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Obter ações de pesquisa agendada. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Criar ou atualizar ações de pesquisa agendadas. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Excluir pesquisas agendadas. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Obter pesquisas agendadas. |
> | Ação | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Criar ou atualizar pesquisas agendadas. |
> | Ação | Microsoft.OperationalInsights/workspaces/savedSearches/write | Cria uma consulta de pesquisa salva |
> | Ação | Microsoft.OperationalInsights/workspaces/schema/read | Obtém o esquema de pesquisa para o espaço de trabalho.  O esquema de pesquisa inclui os campos expostos e seus tipos. |
> | Ação | Microsoft.OperationalInsights/workspaces/search/action | Executa uma consulta de pesquisa |
> | Ação | microsoft.operationalinsights/workspaces/search/read | Obter resultados da pesquisa. Preterido. |
> | Ação | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Recupera as chaves compartilhadas para o espaço de trabalho. Essas chaves são usadas para conectar agentes do insights operacionais da Microsoft ao espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera as chaves compartilhadas para o espaço de trabalho. Essas chaves são usadas para conectar agentes do insights operacionais da Microsoft ao espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Exclui uma configuração de armazenamento. Isso interromperá a leitura dos dados da conta de armazenamento pelos insights operacionais da Microsoft. |
> | Ação | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Obtém uma configuração de armazenamento. |
> | Ação | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Cria uma nova configuração de armazenamento. Essas configurações são usadas para efetuar pull de dados de um local em uma conta de armazenamento existente. |
> | Ação | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Obter log de falhas de conversão de atualização de pesquisa para o espaço de trabalho |
> | Ação | Microsoft.OperationalInsights/workspaces/usages/read | Obtém dados de uso para um espaço de trabalho, incluindo a quantidade de dados lidos pelo espaço de trabalho. |
> | Ação | Microsoft.OperationalInsights/workspaces/write | Cria um novo espaço de trabalho ou links para um espaço de trabalho existente fornecendo a ID do cliente do espaço de trabalho existente. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.OperationsManagement/managementAssociations/delete | Excluir Associação de gerenciamento existente |
> | Ação | Microsoft.OperationsManagement/managementAssociations/read | Obter Associação de gerenciamento existente |
> | Ação | Microsoft.OperationsManagement/managementAssociations/write | Criar uma nova associação de gerenciamento |
> | Ação | Microsoft.OperationsManagement/managementConfigurations/delete | Excluir configuração de gerenciamento existente |
> | Ação | Microsoft.OperationsManagement/managementConfigurations/read | Obter configuração de gerenciamento existente |
> | Ação | Microsoft.OperationsManagement/managementConfigurations/write | Criar uma nova configuração de gerenciamento |
> | Ação | Microsoft.OperationsManagement/register/action | Registrar uma assinatura em um provedor de recursos. |
> | Ação | Microsoft.OperationsManagement/solutions/delete | Excluir solução OMS existente |
> | Ação | Microsoft.OperationsManagement/solutions/read | Obter solução OMS de saída |
> | Ação | Microsoft.OperationsManagement/solutions/write | Criar nova solução do OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.PolicyInsights/asyncOperationResults/read | Obtém o resultado da operação assíncrona. |
> | DataAction | Microsoft. PolicyInsights/checkDataPolicyCompliance/Action | Verifique o status de conformidade de um determinado componente em relação a políticas de dados. |
> | Ação | Microsoft.PolicyInsights/operations/read | Obtém operações com suporte no namespace Microsoft. PolicyInsights |
> | DataAction | Microsoft. PolicyInsights/policyEvents/logDataEvents/Action | Registre os eventos de política do componente de recurso. |
> | Ação | Microsoft.PolicyInsights/policyEvents/queryResults/action | Consultar informações sobre eventos de política. |
> | Ação | Microsoft.PolicyInsights/policyEvents/queryResults/read | Consultar informações sobre eventos de política. |
> | Ação | Microsoft. PolicyInsights/policyMetadata/Read | Obter recursos de metadados de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/queryResults/action | Consultar informações sobre Estados de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/queryResults/read | Consultar informações sobre Estados de política. |
> | Ação | Microsoft.PolicyInsights/policyStates/summarize/action | Informações de resumo da consulta sobre os Estados mais recentes da política. |
> | Ação | Microsoft.PolicyInsights/policyStates/summarize/read | Informações de resumo da consulta sobre os Estados mais recentes da política. |
> | Ação | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Dispara uma nova avaliação de conformidade para o escopo selecionado. |
> | Ação | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Consultar informações sobre os recursos exigidos pelas políticas de DeployIfNotExists. |
> | Ação | Microsoft.PolicyInsights/register/action | Registra o provedor de recursos de informações da política da Microsoft e habilita ações nele. |
> | Ação | Microsoft.PolicyInsights/remediations/cancel/action | Cancelar as correções de política da Microsoft em andamento. |
> | Ação | Microsoft.PolicyInsights/remediations/delete | Excluir correções de política. |
> | Ação | Microsoft.PolicyInsights/remediations/listDeployments/read | Lista as implantações exigidas por uma atualização de política. |
> | Ação | Microsoft.PolicyInsights/remediations/read | Obter correções de política. |
> | Ação | Microsoft.PolicyInsights/remediations/write | Criar ou atualizar as correções da política da Microsoft. |
> | Ação | Microsoft.PolicyInsights/unregister/action | Cancela o registro do provedor de recursos de informações da política da Microsoft. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Portal/consoles/delete | Remove a instância de Cloud Shell. |
> | Ação | Microsoft.Portal/consoles/read | Lê a instância de Cloud Shell. |
> | Ação | Microsoft.Portal/consoles/write | Criar ou atualizar uma instância de Cloud Shell. |
> | Ação | Microsoft.Portal/dashboards/delete | Remove o painel da assinatura. |
> | Ação | Microsoft.Portal/dashboards/read | Lê os painéis para a assinatura. |
> | Ação | Microsoft.Portal/dashboards/write | Adicionar ou modificar o painel em uma assinatura. |
> | Ação | Microsoft.Portal/register/action | Registrar no portal |
> | Ação | Microsoft.Portal/usersettings/delete | Remove as configurações de usuário do Cloud Shell. |
> | Ação | Microsoft.Portal/usersettings/read | Lê as configurações de usuário do Cloud Shell. |
> | Ação | Microsoft.Portal/usersettings/write | Criar ou atualizar Cloud Shell configuração de usuário. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.PowerBIDedicated/capacities/delete | Exclui a Power BI capacidade dedicada. |
> | Ação | Microsoft.PowerBIDedicated/capacities/read | Recupera as informações da capacidade Power BI dedicada especificada. |
> | Ação | Microsoft.PowerBIDedicated/capacities/resume/action | Retoma a capacidade. |
> | Ação | Microsoft.PowerBIDedicated/capacities/skus/read | Recuperar informações de SKU disponíveis para a capacidade |
> | Ação | Microsoft.PowerBIDedicated/capacities/suspend/action | Suspende a capacidade. |
> | Ação | Microsoft.PowerBIDedicated/capacities/write | Cria ou atualiza a capacidade especificada Power BI dedicada. |
> | Ação | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Verifica se o nome de capacidade dedicado fornecido Power BI é válido e não está em uso. |
> | Ação | Microsoft.PowerBIDedicated/locations/operationresults/read | Recupera as informações do resultado da operação especificada. |
> | Ação | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Recupera as informações do status de operação especificado. |
> | Ação | Microsoft.PowerBIDedicated/operations/read | Recupera as informações de operações |
> | Ação | Microsoft.PowerBIDedicated/register/action | Registra Power BI provedor de recursos dedicado. |
> | Ação | Microsoft.PowerBIDedicated/skus/read | Recupera as informações de SKUs |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Ação | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna usada pelo serviço |
> | Ação | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Ação | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | Criar um item protegido de backup |
> | Ação | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | Retorna a lista de todos os itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Locations/backupStatus/action | Verificar o status do backup dos cofres dos serviços de recuperação |
> | Ação | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Validar recursos |
> | Ação | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Verificar a disponibilidade do nome do recurso é uma API para verificar se o nome do recurso está disponível |
> | Ação | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o status da operação para uma determinada operação |
> | Ação | Microsoft.RecoveryServices/operations/read | A operação retorna a lista de operações para um provedor de recursos |
> | Ação | Microsoft.RecoveryServices/register/action | Registra a assinatura para o provedor de recursos fornecido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupconfig/read | Retorna a configuração do cofre dos serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupconfig/write | Atualiza a configuração do cofre dos serviços de recuperação. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupEncryptionConfigs/Read | Obtém a configuração de criptografia do recurso de backup. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupEncryptionConfigs/Write | Atualiza a configuração de criptografia do recurso de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retorna todos os servidores de gerenciamento de backup registrados com o cofre. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Excluir uma intenção de proteção de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obter uma intenção de proteção de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma intenção de proteção de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Retorna o status da operação |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/operationsStatus/Read | Retorna o status da operação |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obter todos os contêineres protegíveis |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Exclui o contêiner registrado |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Fazer consulta para cargas de trabalho dentro de um contêiner |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obter todos os itens em um contêiner |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtém o resultado da operação executada no contêiner de proteção. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/operationsStatus/Read | Obtém o status da operação executada no contêiner de proteção. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Executa o backup para o item protegido. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Excluir item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtém o resultado da operação executada em itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Retorna o status da operação executada nos itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retorna os detalhes do objeto do item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Provisionar a recuperação instantânea de item para o item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obter pontos de recuperação para itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurar pontos de recuperação para itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revogar a recuperação instantânea de item para o item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item protegido de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Retorna todos os contêineres registrados |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Cria um contêiner registrado |
> | Ação | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualiza a lista de contêineres |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Cancelar o trabalho |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Retorna o resultado da operação de trabalho. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupJobs/operationsStatus/Read | Retorna o status da operação de trabalho. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobs/read | Retorna todos os objetos de trabalho |
> | Ação | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
> | Ação | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Retorna o resultado da operação de backup para o cofre dos serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retorna o status da operação de backup do cofre dos serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Excluir uma política de proteção |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter resultados da operação de política. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter o status da operação de política. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retorna todas as políticas de proteção |
> | Ação | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Criar política de proteção |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Retorna a lista de todos os itens protegíveis. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Retorna a lista de todos os itens protegidos. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Retorna todos os contêineres que pertencem à assinatura |
> | Ação | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listar todas as tentativas de proteção de backup |
> | Ação | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Retorna informações de PIN de segurança para o cofre dos serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Retorna a configuração de armazenamento para o cofre dos serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Atualiza a configuração de armazenamento para o cofre dos serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retorna resumos de itens protegidos e servidores protegidos para serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar operação no item protegido |
> | Ação | Microsoft.RecoveryServices/Vaults/certificates/write | A operação atualizar certificado de recurso atualiza o certificado de credencial do cofre/recurso. |
> | Ação | Microsoft.RecoveryServices/Vaults/delete | A operação excluir cofre exclui o recurso do Azure especificado do tipo ' cofre ' |
> | Ação | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Ação | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Ação | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtém os alertas para o cofre dos serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolve o alerta. |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Obtém a configuração de notificação do cofre dos serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Configura notificações de email para o cofre dos serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/read | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | A operação cancelar registro do contêiner pode ser usada para cancelar o registro de um contêiner. |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Ação | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A operação registrar contêiner de serviço pode ser usada para registrar um contêiner com o serviço de recuperação. |
> | Ação | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler as configurações de alertas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Criar ou atualizar as configurações de alertas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler todos os eventos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verifica a consistência da malha |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Excluir todas as malhas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Implantar imagem do servidor de processo |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrar malha para o AAD |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nas malhas de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler malhas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociar gateway |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Remover malha |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renovar certificado para malha |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Ler redes lógicas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler redes |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Excluir todos os mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Ler mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Criar ou atualizar mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Descobrir item de proteção |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos contêineres de proteção de recurso |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Ler contêineres de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Remover contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Excluir todos os itens de migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migrar item |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Ler pontos de recuperação de migração |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos itens de migração de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Ler todos os itens de migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Migrar teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Limpeza de migração de teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Criar ou atualizar itens de migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Ler itens protegíveis |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Adicionar discos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplicar ponto de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Excluir todos os itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Confirmação de failover |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos itens protegidos por recurso |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Ativação Pós-falha Planeada |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler pontos de recuperação de replicação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Remover item protegido |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Remover discos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparar replicação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Proteger novamente o item protegido |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Enviar Comentários |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Ler os tamanhos de computação de destino |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Ativação Pós-falha de Teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Limpeza do failover de teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Ativação Pós-Falha |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Atualizar serviço de mobilidade |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Criar ou atualizar itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Excluir todos os mapeamentos de contêiner de proteção |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos mapeamentos de contêineres de proteção de recurso |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler mapeamentos de contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Remover mapeamento de contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Criar ou atualizar mapeamentos de contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Alternar contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Criar ou atualizar contêineres de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Excluir qualquer provedor de serviços de recuperação |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationRecoveryServicesProviders/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos provedores de serviços de recuperação de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler provedores de serviços de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Atualizar provedor |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Remover provedor de serviços de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Criar ou atualizar provedores de serviços de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Ler classificações de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Excluir todos os mapeamentos de classificação de armazenamento |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos mapeamentos de classificação de armazenamento de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler mapeamentos de classificação de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Criar ou atualizar mapeamentos de classificação de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Excluir qualquer vCenters |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationvCenters/operationresults/Read | Acompanhar os resultados de uma operação assíncrona no recurso vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler qualquer vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Criar ou atualizar qualquer vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Criar ou atualizar malhas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Cancelar trabalho |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationJobs/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos trabalhos de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/read | Ler trabalhos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Reiniciar trabalho |
> | Ação | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Retomar trabalho |
> | Ação | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Ler todos os itens de migração |
> | Ação | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Ler mapeamentos de rede |
> | Ação | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Ler redes |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationOperationStatus/Read | Ler o status da operação de replicação do cofre |
> | Ação | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Excluir todas as políticas |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationPolicies/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nas políticas de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler políticas |
> | Ação | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Criar ou atualizar qualquer política |
> | Ação | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Ler itens protegidos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Ler mapeamentos de contêiner de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Ler contêineres de proteção |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Excluir qualquer plano de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plano de recuperação de confirmação de failover |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationRecoveryPlans/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos planos de recuperação de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plano de recuperação de failover planejado |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler planos de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Reproteger plano de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plano de recuperação de failover de teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plano de recuperação de limpeza do failover de teste |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plano de recuperação de failover |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Criar ou atualizar planos de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Ler provedores de serviços de recuperação |
> | Ação | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Ler mapeamentos de classificação de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Ler classificações de armazenamento |
> | Ação | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Ler qualquer  |
> | Ação | Microsoft.RecoveryServices/vaults/replicationUsages/read | Ler usos de replicação de cofre |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationVaultHealth/operationresults/Read | Acompanhar os resultados de uma operação assíncrona na integridade da replicação do cofre de recursos |
> | Ação | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Ler integridade da replicação do cofre |
> | Ação | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Atualizar integridade do cofre |
> | Ação | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Ler qualquer  |
> | Ação | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Criar ou atualizar qualquer  |
> | Ação | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Ler qualquer vCenters |
> | Ação | Microsoft.RecoveryServices/vaults/usages/read | Ler os usos do cofre |
> | Ação | Microsoft.RecoveryServices/Vaults/usages/read | Retorna os detalhes de uso de um cofre dos serviços de recuperação. |
> | Ação | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada para obter o token do cofre para operações de back-end no nível do cofre. |
> | Ação | Microsoft.RecoveryServices/Vaults/write | A operação criar cofre cria um recurso do Azure do tipo ' cofre ' |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Relay/checkNameAvailability/action | Verifica a disponibilidade do namespace sob determinada assinatura. |
> | Ação | Microsoft.Relay/checkNamespaceAvailability/action | Verifica a disponibilidade do namespace sob determinada assinatura. Esta API foi preterida, use CheckNameAvailability em vez disso. |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/action | Regra de autorização de namespace de atualizações. Esta API foi preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace em vez disso. Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização de namespace padrão não pode ser excluída.  |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/read | Obtenha a lista de descrições de regras de autorização de namespaces. |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.Relay/namespaces/authorizationRules/write | Crie regras de autorização de nível de namespace e atualize suas propriedades. Os direitos de acesso às regras de autorização, as chaves primária e secundária podem ser atualizadas. |
> | Ação | Microsoft.Relay/namespaces/Delete | Excluir recurso de namespace |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obter as chaves de regras de autorização para o namespace primário de recuperação de desastre |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter regras de autorização do namespace primário de recuperação de desastre |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Desabilita a recuperação de desastres e interrompe a replicação de alterações de namespaces primários para secundários. |
> | Ação | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica a disponibilidade do alias de namespace na assinatura fornecida. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Exclui a configuração de recuperação de desastre associada ao namespace. Esta operação só pode ser invocada por meio do namespace primário. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Invoca um failover de DR geográfica e reconfigura o alias de namespace para apontar para o namespace secundário. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Obtém a configuração de recuperação de desastre associada ao namespace. |
> | Ação | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Cria ou atualiza a configuração de recuperação de desastre associada ao namespace. |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operação para atualizar HybridConnection. Não há suporte para esta operação na versão de API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operação para excluir regras de autorização HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Obter a cadeia de conexão para HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Obter a lista de regras de autorização do HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Crie regras de autorização HybridConnection e atualize suas propriedades. Os direitos de acesso das regras de autorização podem ser atualizados. |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/Delete | Operação para excluir o recurso HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/read | Obter lista de descrições de recursos HybridConnection |
> | Ação | Microsoft.Relay/namespaces/HybridConnections/write | Criar ou atualizar Propriedades HybridConnection. |
> | Ação | Microsoft.Relay/namespaces/messagingPlan/read | Obtém o plano de mensagens para um namespace.<br>Esta API foi preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso de namespace (pai) em versões de API posteriores.<br>Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft.Relay/namespaces/messagingPlan/write | Atualiza o plano de mensagens para um namespace.<br>Esta API foi preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso de namespace (pai) em versões de API posteriores.<br>Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft.Relay/namespaces/operationresults/read | Obter o status da operação de namespace |
> | Ação | Microsoft. Relay/namespaces/Providers/Microsoft. insights/diagnosticSettings/Read | Obter lista de descrições de recursos das configurações de diagnóstico de namespace |
> | Ação | Microsoft. Relay/namespaces/Providers/Microsoft. insights/diagnosticSettings/Write | Obter lista de descrições de recursos das configurações de diagnóstico de namespace |
> | Ação | Microsoft. Relay/namespaces/Providers/Microsoft. insights/logDefinitions/Read | Obter lista de descrições de recursos de logs de namespace |
> | Ação | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de descrições de recursos de métricas de namespace |
> | Ação | Microsoft.Relay/namespaces/read | Obter a lista de descrição de recurso de namespace |
> | Ação | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Remover namespace do ACS |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operação para atualizar WcfRelay. Não há suporte para esta operação na versão de API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operação para excluir regras de autorização WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Obter a cadeia de conexão para WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Obter a lista de regras de autorização do WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Crie regras de autorização WcfRelay e atualize suas propriedades. Os direitos de acesso das regras de autorização podem ser atualizados. |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/Delete | Operação para excluir o recurso WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/read | Obter lista de descrições de recursos WcfRelay |
> | Ação | Microsoft.Relay/namespaces/WcfRelays/write | Criar ou atualizar Propriedades WcfRelay. |
> | Ação | Microsoft.Relay/namespaces/write | Crie um recurso de namespace e atualize suas propriedades. As marcas e a capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft.Relay/operations/read | Obter operações |
> | Ação | Microsoft.Relay/register/action | Registra a assinatura para o provedor de recursos de retransmissão e habilita a criação de recursos de retransmissão |
> | Ação | Microsoft.Relay/unregister/action | Registra a assinatura para o provedor de recursos de retransmissão e habilita a criação de recursos de retransmissão |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Obtém o status de disponibilidade para o recurso especificado |
> | Ação | Microsoft.ResourceHealth/AvailabilityStatuses/read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Ação | Microsoft.ResourceHealth/events/read | Obter eventos de integridade do serviço para determinada assinatura |
> | Ação | Microsoft.Resourcehealth/healthevent/action | Denota a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft. Resourcehealth/healthevent/Activated/ação | Denota a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/InProgress/action | Denota a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft. Resourcehealth/healthevent/pendente/ação | Denota a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft. Resourcehealth/healthevent/resolvida/ação | Denota a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.Resourcehealth/healthevent/Updated/action | Denota a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft.ResourceHealth/impactedResources/read | Obter recursos afetados para determinada assinatura |
> | Ação | Microsoft.ResourceHealth/metadata/read | Obtém metadados |
> | Ação | Microsoft. ResourceHealth/Notifications/ler | Recebe notificações de Azure Resource Manager |
> | Ação | Microsoft.ResourceHealth/Operations/read | Obter as operações disponíveis para o Microsoft ResourceHealth |
> | Ação | Microsoft.ResourceHealth/register/action | Registra a assinatura para o Microsoft ResourceHealth |
> | Ação | Microsoft.ResourceHealth/unregister/action | Cancela o registro da assinatura para o Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Resources/calculateTemplateHash/Action | Calcule o hash do modelo fornecido. |
> | Ação | Microsoft. Resources/checkPolicyCompliance/Read | Verifique o status de conformidade de um determinado recurso em relação a políticas de recursos. |
> | Ação | Microsoft.Resources/checkResourceName/action | Verifique a validade do nome do recurso. |
> | Ação | Microsoft.Resources/deployments/cancel/action | Cancela uma implantação. |
> | Ação | Microsoft.Resources/deployments/delete | Exclui uma implantação. |
> | Ação | Microsoft. Resources/Implantations/exportatemplate/ação | Exportar modelo para uma implantação |
> | Ação | Microsoft.Resources/deployments/operations/read | Obtém ou lista operações de implantação. |
> | Ação | Microsoft. Resources/Implantations/operationstatuses/Read | Obtém ou lista os status da operação de implantação. |
> | Ação | Microsoft.Resources/deployments/read | Obtém ou lista implantações. |
> | Ação | Microsoft.Resources/deployments/validate/action | Valida uma implantação. |
> | Ação | Microsoft.Resources/deployments/whatIf/action | Prevê alterações de implantação de modelo. |
> | Ação | Microsoft.Resources/deployments/write | Cria ou atualiza uma implantação. |
> | Ação | Microsoft. Resources/deploymentScripts/Delete | Exclui um script de implantação |
> | Ação | Microsoft. Resources/deploymentScripts/logs/leitura | Obtém ou lista os logs de script de implantação |
> | Ação | Microsoft. Resources/deploymentScripts/Read | Obtém ou lista os scripts de implantação |
> | Ação | Microsoft. Resources/deploymentScripts/Write | Cria ou atualiza um script de implantação |
> | Ação | Microsoft.Resources/links/delete | Exclui um link de recurso. |
> | Ação | Microsoft.Resources/links/read | Obtém ou lista links de recursos. |
> | Ação | Microsoft.Resources/links/write | Cria ou atualiza um link de recurso. |
> | Ação | Microsoft.Resources/marketplace/purchase/action | Adquire um recurso do Marketplace. |
> | Ação | Microsoft.Resources/providers/read | Obtenha a lista de provedores. |
> | Ação | Microsoft.Resources/resources/read | Obtenha a lista de recursos com base nos filtros. |
> | Ação | Microsoft.Resources/subscriptions/locations/read | Obtém a lista de locais com suporte. |
> | Ação | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/providers/read | Obtém ou lista os provedores de recursos. |
> | Ação | Microsoft.Resources/subscriptions/read | Obtém a lista de assinaturas. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/delete | Exclui um grupo de recursos e todos os seus recursos. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Obtém ou lista operações de implantação. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Obtém ou lista os status da operação de implantação. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Obtém ou lista implantações. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Cria ou atualiza uma implantação. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Move os recursos de um grupo de recursos para outro. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/read | Obtém ou lista os grupos de recursos. |
> | Ação | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Obtém os recursos para o grupo de recursos. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Validar a movimentação de recursos de um grupo de recursos para outro. |
> | Ação | Microsoft.Resources/subscriptions/resourceGroups/write | Cria ou atualiza um grupo de recursos. |
> | Ação | Microsoft.Resources/subscriptions/resources/read | Obtém recursos de uma assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/delete | Exclui uma marca de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/read | Obtém ou lista as marcas de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Exclui um valor de marca de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Obtém ou lista os valores de marca de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Adiciona um valor de marca de assinatura. |
> | Ação | Microsoft.Resources/subscriptions/tagNames/write | Adiciona uma marca de assinatura. |
> | Ação | Microsoft.Resources/tags/delete | Remove todas as marcas em um recurso. |
> | Ação | Microsoft.Resources/tags/read | Obtém todas as marcas em um recurso. |
> | Ação | Microsoft.Resources/tags/write | Atualiza as marcas em um recurso substituindo ou mesclando as marcas existentes por um novo conjunto de marcas ou removendo as marcas existentes. |
> | Ação | Microsoft.Resources/tenants/read | Obtém a lista de locatários. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Scheduler/jobcollections/delete | Exclui a coleção de trabalhos. |
> | Ação | Microsoft.Scheduler/jobcollections/disable/action | Desabilita a coleção de trabalhos. |
> | Ação | Microsoft.Scheduler/jobcollections/enable/action | Habilita a coleta de trabalhos. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/delete | Exclui o trabalho. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Gera a definição do aplicativo lógico com base em um trabalho do Agendador. |
> | Ação | Microsoft. Scheduler/gratuitas/Jobs/jobhistories/Read | Obtém o histórico do trabalho. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/read | Obtém o trabalho. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/run/action | Executa o trabalho. |
> | Ação | Microsoft.Scheduler/jobcollections/jobs/write | Cria ou atualiza o trabalho. |
> | Ação | Microsoft.Scheduler/jobcollections/read | Obter coleção de trabalhos |
> | Ação | Microsoft.Scheduler/jobcollections/write | Cria ou atualiza a coleção de trabalhos. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Search/checkNameAvailability/action | Verifica a disponibilidade do nome do serviço. |
> | Ação | Microsoft.Search/operations/read | Lista todas as operações disponíveis do provedor Microsoft. Search. |
> | Ação | Microsoft.Search/register/action | Registra a assinatura para o provedor de recursos de pesquisa e habilita a criação de serviços de pesquisa. |
> | Ação | Microsoft.Search/searchServices/createQueryKey/action | Cria a chave de consulta. |
> | Ação | Microsoft.Search/searchServices/delete | Exclui o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/deleteQueryKey/delete | Exclui a chave de consulta. |
> | Ação | Microsoft.Search/searchServices/listAdminKeys/action | Lê as chaves de administração. |
> | Ação | Microsoft. Search/SearchServices/listQueryKeys/Action | Retorna a lista de chaves de API de consulta para o serviço de Azure Search determinado. |
> | Ação | Microsoft.Search/searchServices/listQueryKeys/read | Retorna a lista de chaves de API de consulta para o serviço de Azure Search determinado. |
> | Ação | Microsoft. Search/SearchServices/privateEndpointConnectionProxies/Delete | Exclui um proxy de conexão de ponto de extremidade privado existente |
> | Ação | Microsoft. Search/SearchServices/privateEndpointConnectionProxies/Read | Retorna a lista de proxies de conexão de ponto de extremidade privado ou obtém as propriedades para o proxy de conexão de ponto de extremidade particular especificado |
> | Ação | Microsoft. Search/SearchServices/privateEndpointConnectionProxies/valida/Action | Valida uma conexão de ponto de extremidade privada criar chamada do lado do NRP |
> | Ação | Microsoft. Search/SearchServices/privateEndpointConnectionProxies/Write | Cria um proxy de conexão de ponto de extremidade privado com os parâmetros especificados ou atualiza as propriedades ou marcas para o proxy de conexão de ponto de extremidade particular especificado |
> | Ação | Microsoft.Search/searchServices/read | Lê o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/regenerateAdminKey/action | Regenera a chave de administração. |
> | Ação | Microsoft.Search/searchServices/start/action | Inicia o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/stop/action | Interrompe o serviço de pesquisa. |
> | Ação | Microsoft.Search/searchServices/write | Cria ou atualiza o serviço de pesquisa. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Impõe as regras de proteção de tráfego determinadas criando regras de segurança correspondentes nos grupos de segurança de rede determinados |
> | Ação | Microsoft.Security/adaptiveNetworkHardenings/read | Obtém recomendações de proteção de rede adaptável de um recurso protegido do Azure |
> | Ação | Microsoft.Security/advancedThreatProtectionSettings/read | Obter as configurações de proteção avançada contra ameaças para o recurso |
> | Ação | Microsoft.Security/advancedThreatProtectionSettings/write | Atualiza as configurações de proteção avançada contra ameaças para o recurso |
> | Ação | Microsoft.Security/alerts/read | Obtém todos os alertas de segurança disponíveis |
> | Ação | Microsoft.Security/applicationWhitelistings/read | Obter as listas de permissões do aplicativo |
> | Ação | Microsoft.Security/applicationWhitelistings/write | Criar uma nova lista de permissões de aplicativo ou atualizar uma existente |
> | Ação | Microsoft. Security/assessmentMetadata/Read | Obter metadados de avaliação de segurança disponíveis em sua assinatura |
> | Ação | Microsoft. Security/assessmentMetadata/Write | Criar ou atualizar metadados de avaliação de segurança |
> | Ação | Microsoft. Security/avaliações/leitura | Obtenha avaliações de segurança em sua assinatura |
> | Ação | Microsoft. Security/avaliações/gravação | Criar ou atualizar avaliações de segurança em sua assinatura |
> | Ação | Microsoft.Security/complianceResults/read | Obter os resultados de conformidade para o recurso |
> | Ação | Microsoft.Security/informationProtectionPolicies/read | Obter as políticas de proteção de informações para o recurso |
> | Ação | Microsoft.Security/informationProtectionPolicies/write | Atualiza as políticas de proteção de informações para o recurso |
> | Ação | Microsoft.Security/locations/alerts/activate/action | Ativar um alerta de segurança |
> | Ação | Microsoft.Security/locations/alerts/dismiss/action | Ignorar um alerta de segurança |
> | Ação | Microsoft.Security/locations/alerts/read | Obtém todos os alertas de segurança disponíveis |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Exclui a política de acesso à rede just-in-time |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Inicia uma solicitação de política de acesso à rede just-in-time |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Obtém as políticas de acesso à rede just-in-time |
> | Ação | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Cria uma nova política de acesso de rede just-in-time ou atualiza uma existente |
> | Ação | Microsoft.Security/locations/read | Obtém o local dos dados de segurança |
> | Ação | Microsoft.Security/locations/tasks/activate/action | Ativar uma recomendação de segurança |
> | Ação | Microsoft.Security/locations/tasks/dismiss/action | Ignorar uma recomendação de segurança |
> | Ação | Microsoft.Security/locations/tasks/read | Obtém todas as recomendações de segurança disponíveis |
> | Ação | Microsoft.Security/locations/tasks/resolve/action | Resolver uma recomendação de segurança |
> | Ação | Microsoft.Security/locations/tasks/start/action | Iniciar uma recomendação de segurança |
> | Ação | Microsoft.Security/policies/read | Obter a política de segurança |
> | Ação | Microsoft.Security/policies/write | Atualiza a política de segurança |
> | Ação | Microsoft.Security/pricings/delete | Excluir as configurações de preço para o escopo |
> | Ação | Microsoft.Security/pricings/read | Obter as configurações de preço para o escopo |
> | Ação | Microsoft.Security/pricings/write | Atualiza as configurações de preços para o escopo |
> | Ação | Microsoft.Security/register/action | Registra a assinatura da central de segurança do Azure |
> | Ação | Microsoft.Security/securityContacts/delete | Exclui o contato de segurança |
> | Ação | Microsoft.Security/securityContacts/read | Obter o contato de segurança |
> | Ação | Microsoft.Security/securityContacts/write | Atualiza o contato de segurança |
> | Ação | Microsoft.Security/securitySolutions/delete | Excluir uma solução de segurança |
> | Ação | Microsoft.Security/securitySolutions/read | Obter as soluções de segurança |
> | Ação | Microsoft.Security/securitySolutions/write | Cria uma nova solução de segurança ou atualiza uma existente |
> | Ação | Microsoft.Security/securitySolutionsReferenceData/read | Obter os dados de referência de soluções de segurança |
> | Ação | Microsoft.Security/securityStatuses/read | Obter os status de integridade de segurança para recursos do Azure |
> | Ação | Microsoft.Security/securityStatusesSummaries/read | Obter os resumos de status de segurança para o escopo |
> | Ação | Microsoft.Security/settings/read | Obtém as configurações para o escopo |
> | Ação | Microsoft.Security/settings/write | Atualiza as configurações para o escopo |
> | Ação | Microsoft.Security/tasks/read | Obtém todas as recomendações de segurança disponíveis |
> | Ação | Microsoft.Security/unregister/action | Cancela o registro da assinatura na central de segurança do Azure |
> | Ação | Microsoft.Security/webApplicationFirewalls/delete | Exclui um firewall do aplicativo Web |
> | Ação | Microsoft.Security/webApplicationFirewalls/read | Obter os firewalls do aplicativo Web |
> | Ação | Microsoft.Security/webApplicationFirewalls/write | Cria um novo Firewall do aplicativo Web ou atualiza um existente |
> | Ação | Microsoft.Security/workspaceSettings/connect/action | Alterar configurações de reconexão de configurações do espaço de trabalho |
> | Ação | Microsoft.Security/workspaceSettings/delete | Exclui as configurações do espaço de trabalho |
> | Ação | Microsoft.Security/workspaceSettings/read | Obter as configurações do espaço de trabalho |
> | Ação | Microsoft.Security/workspaceSettings/write | Atualiza as configurações do espaço de trabalho |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.SecurityGraph/diagnosticsettings/delete | Excluindo uma configuração de diagnóstico |
> | Ação | Microsoft.SecurityGraph/diagnosticsettings/read | Lendo uma configuração de diagnóstico |
> | Ação | Microsoft.SecurityGraph/diagnosticsettings/write | Escrevendo uma configuração de diagnóstico |
> | Ação | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Lendo categorias de configuração de diagnóstico |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. SecurityInsights/agregações/leitura | Obtém informações agregadas |
> | Ação | Microsoft. SecurityInsights/alertRules/Actions/Delete | Excluir as ações de resposta de uma regra de alerta |
> | Ação | Microsoft. SecurityInsights/alertRules/Actions/Read | Obter as ações de resposta de uma regra de alerta |
> | Ação | Microsoft. SecurityInsights/alertRules/Actions/Write | Atualiza as ações de resposta de uma regra de alerta |
> | Ação | Microsoft. SecurityInsights/alertRules/Delete | Excluir regras de alerta |
> | Ação | Microsoft. SecurityInsights/alertRules/Read | Obter as regras de alerta |
> | Ação | Microsoft. SecurityInsights/alertRules/Write | Regras de alerta de atualizações |
> | Ação | Microsoft. SecurityInsights/indicadores/excluir | Exclui indicadores |
> | Ação | Microsoft. SecurityInsights/indicadores/expandir/ação | Obtém entidades relacionadas de uma entidade por uma expansão específica |
> | Ação | Microsoft. SecurityInsights/indicadores/leitura | Obtém indicadores |
> | Ação | Microsoft. SecurityInsights/indicadores/gravação | Atualiza indicadores |
> | Ação | Microsoft. SecurityInsights/casos/comentários/leitura | Obter os comentários do caso |
> | Ação | Microsoft. SecurityInsights/casos/comentários/gravação | Cria os comentários de caso |
> | Ação | Microsoft. SecurityInsights/casos/excluir | Excluir um caso |
> | Ação | Microsoft. SecurityInsights/cases/investigações/ler | Obter as investigações de caso |
> | Ação | Microsoft. SecurityInsights/casos/investigações/gravação | Atualiza os metadados de um caso |
> | Ação | Microsoft. SecurityInsights/casos/leitura | Obtém um caso |
> | Ação | Microsoft. SecurityInsights/casos/gravação | Atualiza um caso |
> | Ação | Microsoft. SecurityInsights/dataconnecters/Delete | Exclui um conector de dados |
> | Ação | Microsoft. SecurityInsights/dataconnecters/leitura | Obter os conectores de dados |
> | Ação | Microsoft. SecurityInsights/dataconnecters/Write | Atualiza um conector de dados |
> | Ação | Microsoft. SecurityInsights/registro/ação | Registra a assinatura no Azure Sentinel |
> | Ação | Microsoft. SecurityInsights/configurações/leitura | Obtém as configurações |
> | Ação | Microsoft. SecurityInsights/Settings/Write | Definições das atualizações |
> | Ação | Microsoft. SecurityInsights/cancelar registro/ação | Cancela o registro da assinatura do Sentinela do Azure |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ServiceBus/checkNameAvailability/action | Verifica a disponibilidade do namespace sob determinada assinatura. |
> | Ação | Microsoft.ServiceBus/checkNamespaceAvailability/action | Verifica a disponibilidade do namespace sob determinada assinatura. Esta API foi preterida, use CheckNameAvailability em vez disso. |
> | Ação | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Exclui as regras de VNet no provedor de recursos do ServiceBus para a VNet especificada |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/action | Regra de autorização de namespace de atualizações. Esta API foi preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace em vez disso. Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Excluir regra de autorização de namespace. A regra de autorização de namespace padrão não pode ser excluída.  |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/read | Obtenha a lista de descrições de regras de autorização de namespaces. |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.ServiceBus/namespaces/authorizationRules/write | Crie regras de autorização de nível de namespace e atualize suas propriedades. Os direitos de acesso às regras de autorização, as chaves primária e secundária podem ser atualizadas. |
> | Ação | Microsoft.ServiceBus/namespaces/Delete | Excluir recurso de namespace |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Obter as chaves de regras de autorização para o namespace primário de recuperação de desastre |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Obter regras de autorização do namespace primário de recuperação de desastre |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Desabilita a recuperação de desastres e interrompe a replicação de alterações de namespaces primários para secundários. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Verifica a disponibilidade do alias de namespace na assinatura fornecida. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Exclui a configuração de recuperação de desastre associada ao namespace. Esta operação só pode ser invocada por meio do namespace primário. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Invoca um failover de DR geográfica e reconfigura o alias de namespace para apontar para o namespace secundário. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Obtém a configuração de recuperação de desastre associada ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Cria ou atualiza a configuração de recuperação de desastre associada ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Exclui o filtro de grade de eventos associado ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Obtém o filtro de grade de eventos associado ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Cria ou atualiza o filtro de grade de eventos associado ao namespace. |
> | Ação | Microsoft.ServiceBus/namespaces/eventhubs/read | Obter lista de descrições de recursos do EventHub |
> | Ação | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Excluir recurso de filtro IP |
> | Ação | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Obter recurso de filtro IP |
> | Ação | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Criar recurso de filtro IP |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Receber mensagens |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Enviar mensagens |
> | Ação | Microsoft.ServiceBus/namespaces/messagingPlan/read | Obtém o plano de mensagens para um namespace.<br>Esta API foi preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso de namespace (pai) em versões de API posteriores.<br>Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft.ServiceBus/namespaces/messagingPlan/write | Atualiza o plano de mensagens para um namespace.<br>Esta API foi preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso de namespace (pai) em versões de API posteriores.<br>Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft.ServiceBus/namespaces/migrate/action | Migrar operação de namespace |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Exclui a configuração de migração. |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Obtém a configuração de migração que indica o estado da migração e as operações de replicação pendentes |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Reverte a migração de namespace padrão para Premium |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Atribui o DNS associado ao namespace Standard ao namespace Premium que conclui a migração e interrompe os recursos de sincronização do namespace Standard para Premium |
> | Ação | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Cria ou atualiza a configuração de migração. Isso iniciará a sincronização de recursos do padrão para o namespace Premium |
> | Ação | Microsoft. ServiceBus/namespaces/networkruleset/Delete | Excluir recurso de regra de VNET |
> | Ação | Microsoft. ServiceBus/namespaces/networkruleset/Read | Obtém o recurso NetworkRuleSet |
> | Ação | Microsoft. ServiceBus/namespaces/networkruleset/Write | Criar recurso de regra de VNET |
> | Ação | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Excluir recurso de regra de VNET |
> | Ação | Microsoft.ServiceBus/namespaces/networkrulesets/read | Obtém o recurso NetworkRuleSet |
> | Ação | Microsoft.ServiceBus/namespaces/networkrulesets/write | Criar recurso de regra de VNET |
> | Ação | Microsoft.ServiceBus/namespaces/operationresults/read | Obter o status da operação de namespace |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Obter lista de descrições de recursos das configurações de diagnóstico de namespace |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Obter lista de descrições de recursos das configurações de diagnóstico de namespace |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Obter lista de descrições de recursos de logs de namespace |
> | Ação | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Obter lista de descrições de recursos de métricas de namespace |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operação para atualizar a fila. Não há suporte para esta operação na versão de API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operação para excluir regras de autorização de fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Obter a cadeia de conexão para a fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Obter a lista de regras de autorização de fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Crie regras de autorização de fila e atualize suas propriedades. Os direitos de acesso das regras de autorização podem ser atualizados. |
> | Ação | Microsoft.ServiceBus/namespaces/queues/Delete | Operação para excluir o recurso de fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/read | Obter lista de descrições de recursos de fila |
> | Ação | Microsoft.ServiceBus/namespaces/queues/write | Criar ou atualizar as propriedades da fila. |
> | Ação | Microsoft.ServiceBus/namespaces/read | Obter a lista de descrição de recurso de namespace |
> | Ação | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Remover namespace do ACS |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Operação para atualizar o tópico. Não há suporte para esta operação na versão de API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Operação para excluir regras de autorização de tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Obter a cadeia de conexão para o tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Obter a lista de regras de autorização de tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Crie regras de autorização de tópico e atualize suas propriedades. Os direitos de acesso das regras de autorização podem ser atualizados. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/Delete | Operação para excluir o recurso de tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/read | Obter lista de descrições de recursos de tópico |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Operação para excluir o recurso TopicSubscription |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Obter lista de descrições de recursos TopicSubscription |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Operação para excluir o recurso de regra |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Obter lista de descrições de recursos de regra |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Criar ou atualizar propriedades de regra. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Criar ou atualizar Propriedades TopicSubscription. |
> | Ação | Microsoft.ServiceBus/namespaces/topics/write | Criar ou atualizar propriedades de tópico. |
> | Ação | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Excluir recurso de regra de VNET |
> | Ação | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Obter recurso de regra de VNET |
> | Ação | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Criar recurso de regra de VNET |
> | Ação | Microsoft.ServiceBus/namespaces/write | Crie um recurso de namespace e atualize suas propriedades. As marcas e a capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft.ServiceBus/operations/read | Obter operações |
> | Ação | Microsoft.ServiceBus/register/action | Registra a assinatura para o provedor de recursos do ServiceBus e habilita a criação de recursos do ServiceBus |
> | Ação | Microsoft.ServiceBus/sku/read | Obter lista de descrições de recursos de SKU |
> | Ação | Microsoft.ServiceBus/sku/regions/read | Obter lista de descrições de recursos SkuRegions |
> | Ação | Microsoft.ServiceBus/unregister/action | Registra a assinatura para o provedor de recursos do ServiceBus e habilita a criação de recursos do ServiceBus |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.ServiceFabric/clusters/applications/delete | Excluir qualquer aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applications/read | Ler qualquer aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/delete | Excluir qualquer serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Ler qualquer partição |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Ler qualquer réplica |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/read | Ler qualquer serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Ler qualquer status de serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/services/write | Criar ou atualizar qualquer serviço |
> | Ação | Microsoft.ServiceFabric/clusters/applications/write | Criar ou atualizar qualquer aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Excluir qualquer tipo de aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/read | Ler qualquer tipo de aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Excluir qualquer versão do tipo de aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Ler qualquer versão do tipo de aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Criar ou atualizar qualquer versão de tipo de aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/applicationTypes/write | Criar ou atualizar qualquer tipo de aplicativo |
> | Ação | Microsoft.ServiceFabric/clusters/delete | Excluir qualquer cluster |
> | Ação | Microsoft.ServiceFabric/clusters/nodes/read | Ler qualquer nó |
> | Ação | Microsoft.ServiceFabric/clusters/read | Ler qualquer cluster |
> | Ação | Microsoft.ServiceFabric/clusters/statuses/read | Ler qualquer status de cluster |
> | Ação | Microsoft.ServiceFabric/clusters/write | Criar ou atualizar qualquer cluster |
> | Ação | Microsoft.ServiceFabric/locations/clusterVersions/read | Ler qualquer versão do cluster |
> | Ação | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Ler qualquer versão de cluster para um ambiente específico |
> | Ação | Microsoft.ServiceFabric/locations/operationresults/read | Ler os resultados da operação |
> | Ação | Microsoft.ServiceFabric/locations/operations/read | Ler todas as operações por local |
> | Ação | Microsoft.ServiceFabric/operations/read | Ler todas as operações disponíveis |
> | Ação | Microsoft.ServiceFabric/register/action | Registrar qualquer ação |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.SignalRService/locations/checknameavailability/action | Verifica se um nome está disponível para uso com um novo serviço Signalr |
> | Ação | Microsoft.SignalRService/locations/operationresults/signalr/read | Consultar o status de uma operação assíncrona |
> | Ação | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Consultar o status de uma operação assíncrona |
> | Ação | Microsoft.SignalRService/locations/usages/read | Obter os usos de cota do serviço de Signaler do Azure |
> | Ação | Microsoft.SignalRService/operationresults/read | Consultar o status de uma operação assíncrona |
> | Ação | Microsoft. SignalRService/operações/leitura | Listar as operações do serviço de Signaler do Azure. |
> | Ação | Microsoft.SignalRService/operationstatus/read | Consultar o status de uma operação assíncrona |
> | Ação | Microsoft.SignalRService/register/action | Registra o provedor de recursos ' Microsoft. SignalRService ' com uma assinatura |
> | Ação | Microsoft.SignalRService/SignalR/delete | Excluir o serviço de sinalização inteiro |
> | Ação | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Excluir um filtro de grade de eventos de um Signalr. |
> | Ação | Microsoft.SignalRService/SignalR/eventGridFilters/read | Obtenha as propriedades do filtro de grade de eventos especificado ou liste todos os filtros de grade de eventos para o Signalr especificado. |
> | Ação | Microsoft.SignalRService/SignalR/eventGridFilters/write | Crie ou atualize um filtro de grade de eventos para um Signalr com os parâmetros especificados. |
> | Ação | Microsoft.SignalRService/SignalR/listkeys/action | Exibir o valor das chaves de acesso do Signalr no portal de gerenciamento ou por meio da API |
> | Ação | Microsoft. SignalRService/Signalr/privateEndpointConnectionProxies/Delete | Excluir um proxy de conexão de ponto de extremidade privado |
> | Ação | Microsoft. SignalRService/Signalr/privateEndpointConnectionProxies/Read | Ler um proxy de ponto de extremidade privado |
> | Ação | Microsoft. SignalRService/Signalr/privateEndpointConnectionProxies/valida/Action | Validar um proxy de conexão de ponto de extremidade privado |
> | Ação | Microsoft. SignalRService/Signalr/privateEndpointConnectionProxies/Write | Criar um proxy de conexão de ponto de extremidade privado |
> | Ação | Microsoft. SignalRService/Signalr/privateEndpointConnections/Read | Ler uma conexão de ponto de extremidade particular |
> | Ação | Microsoft. SignalRService/Signalr/privateEndpointConnections/Write | Aprovar ou rejeitar uma conexão de ponto de extremidade particular |
> | Ação | Microsoft. SignalRService/Signalr/privateLinkResources/Read | Listar todos os recursos de link privado do Signalr |
> | Ação | Microsoft.SignalRService/SignalR/read | Exibir as configurações do Signalr e as configurações no portal de gerenciamento ou por meio da API |
> | Ação | Microsoft.SignalRService/SignalR/regeneratekey/action | Alterar o valor das chaves de acesso do Signalr no portal de gerenciamento ou por meio da API |
> | Ação | Microsoft.SignalRService/SignalR/restart/action | Para reiniciar um serviço de Signaler do Azure no portal de gerenciamento ou por meio da API. Haverá determinado tempo de inatividade. |
> | Ação | Microsoft.SignalRService/SignalR/write | Modificar as configurações do Signalr e as configurações no portal de gerenciamento ou por meio da API |
> | Ação | Microsoft.SignalRService/unregister/action | Cancela o registro do provedor de recursos ' Microsoft. SignalRService ' com uma assinatura |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Lista os artefatos de aplicativo da definição de aplicativo. |
> | Ação | Microsoft.Solutions/applicationDefinitions/delete | Remove uma definição de aplicativo. |
> | Ação | Microsoft.Solutions/applicationDefinitions/read | Recupera uma lista de definições de aplicativo. |
> | Ação | Microsoft.Solutions/applicationDefinitions/write | Adicionar ou modificar uma definição de aplicativo. |
> | Ação | Microsoft.Solutions/applications/applicationArtifacts/read | Lista os artefatos de aplicativo. |
> | Ação | Microsoft.Solutions/applications/delete | Remove um aplicativo. |
> | Ação | Microsoft.Solutions/applications/read | Recupera uma lista de aplicativos. |
> | Ação | Microsoft.Solutions/applications/refreshPermissions/action | Atualiza permissão (ões) de aplicativo. |
> | Ação | Microsoft.Solutions/applications/updateAccess/action | Atualiza o acesso ao aplicativo. |
> | Ação | Microsoft.Solutions/applications/write | Cria uma aplicação. |
> | Ação | Microsoft.Solutions/jitRequests/delete | Remover um JitRequest |
> | Ação | Microsoft.Solutions/jitRequests/read | Recupera uma lista de JitRequests |
> | Ação | Microsoft.Solutions/jitRequests/write | Cria um JitRequest |
> | Ação | Microsoft.Solutions/locations/operationStatuses/read | Lê o status da operação para o recurso. |
> | Ação | Microsoft. Solutions/Operations/Read | Obtém a lista de operações. |
> | Ação | Microsoft.Solutions/register/action | Registre-se em soluções. |
> | Ação | Microsoft.Solutions/unregister/action | Cancela o registro de soluções. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Sql/checkNameAvailability/action | Verifique se o nome do servidor fornecido está disponível para provisionamento em todo o mundo para uma determinada assinatura. |
> | Ação | Microsoft.Sql/instancePools/delete | Exclui um pool de instâncias |
> | Ação | Microsoft.Sql/instancePools/read | Obtém um pool de instâncias |
> | Ação | Microsoft.Sql/instancePools/usages/read | Obtém as informações de uso do pool de instâncias |
> | Ação | Microsoft.Sql/instancePools/write | Criar ou atualizar um pool de instâncias |
> | Ação | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Recuperar o resultado da operação de definição de política de auditoria de blob de servidor estendido |
> | Ação | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Recuperar o resultado da operação de definição de política de auditoria de blob do servidor |
> | Ação | Microsoft.Sql/locations/capabilities/read | Obter os recursos para esta assinatura em um determinado local |
> | Ação | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Obtém o status de uma operação de banco de dados. |
> | Ação | Microsoft.Sql/locations/databaseOperationResults/read | Obtém o status de uma operação de banco de dados. |
> | Ação | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Obter operações em andamento no servidor excluído |
> | Ação | Microsoft.Sql/locations/deletedServerOperationResults/read | Obter operações em andamento no servidor excluído |
> | Ação | Microsoft.Sql/locations/deletedServers/read | Retornar a lista de servidores excluídos ou obter as propriedades para o servidor excluído especificado. |
> | Ação | Microsoft.Sql/locations/deletedServers/recover/action | Recuperar um servidor excluído |
> | Ação | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Obtém a operação assíncrona do Azure para uma operação assíncrona de pool elástico |
> | Ação | Microsoft.Sql/locations/elasticPoolOperationResults/read | Obtém o resultado de uma operação de pool elástico. |
> | Ação | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Obter operações em andamento no protetor de criptografia de criptografia de dados transparente |
> | Ação | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Obter operações em andamento no protetor de criptografia de criptografia de dados transparente |
> | Ação | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Recuperar o resultado da operação de definição de política de auditoria de blob de servidor estendido |
> | Ação | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Recuperar o resultado da operação de definição de política de auditoria de blob de servidor estendido |
> | Ação | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Obtém o status de uma operação de regra de firewall. |
> | Ação | Microsoft.Sql/locations/firewallRulesOperationResults/read | Obtém o status de uma operação de regra de firewall. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/delete | Exclui um grupo de failover de instância existente. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Executa o failover planejado em um grupo de failover de instância existente. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Executa o failover forçado em um grupo de failover de instância existente. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/read | Retorna a lista de grupos de failover de instância ou obtém as propriedades para o grupo de failover de instância especificado. |
> | Ação | Microsoft.Sql/locations/instanceFailoverGroups/write | Cria um grupo de failover de instância com os parâmetros especificados ou atualiza as propriedades ou marcas para o grupo de failover de instância especificado. |
> | Ação | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Obtém o status de uma operação de pool de instâncias |
> | Ação | Microsoft.Sql/locations/instancePoolOperationResults/read | Obtém o resultado de uma operação de pool de instâncias |
> | Ação | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Retorna os detalhes de uma operação assíncrona do ponto de extremidade de interface específico do Azure |
> | Ação | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Retorna os detalhes da operação de perfil de ponto de extremidade de interface especificada |
> | Ação | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Obtém o status de uma operação de agente de trabalho. |
> | Ação | Microsoft.Sql/locations/jobAgentOperationResults/read | Obtém o resultado de uma operação de agente de trabalho. |
> | Ação | Microsoft.Sql/locations/longTermRetentionBackups/read | Lista os backups de retenção de longo prazo para cada banco de dados em cada servidor em um local |
> | Ação | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Lista os backups de retenção de longo prazo para cada banco de dados em um servidor |
> | Ação | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Exclui um backup de retenção de longo prazo |
> | Ação | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Lista os backups de retenção de longo prazo para um banco de dados |
> | Ação | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Conclui a operação de restauração do banco de dados gerenciado |
> | Ação | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Obter operações em andamento no protetor de criptografia de instância gerenciada da Transparent Data Encryption |
> | Ação | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Obter operações em andamento no protetor de criptografia de instância gerenciada da Transparent Data Encryption |
> | Ação | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Obter operações em andamento em chaves de instância gerenciada da Transparent Data Encryption |
> | Ação | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Obter operações em andamento em chaves de instância gerenciada da Transparent Data Encryption |
> | Ação | Microsoft. SQL/Locations/managedInstanceLongTermRetentionPolicyAzureAsyncOperation/Read | Obtém o status de uma operação de política de retenção de longo prazo para um banco de dados gerenciado |
> | Ação | Microsoft. SQL/Locations/managedInstanceLongTermRetentionPolicyOperationResults/Read | Obtém o status de uma operação de política de retenção de longo prazo para um banco de dados gerenciado |
> | Ação | Microsoft. SQL/Locations/managedShortTermRetentionPolicyOperationResults/Read | Obtém o status de uma operação de política de retenção de curto prazo |
> | Ação | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Obter operações em andamento em Transparent Data Encryption de banco de dados gerenciado |
> | Ação | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Obter operações em andamento em Transparent Data Encryption de banco de dados gerenciado |
> | Ação | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft.Sql/locations/read | Obter os locais disponíveis para uma determinada assinatura |
> | Ação | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Obter operações em andamento em chaves de servidor de Transparent Data Encryption |
> | Ação | Microsoft.Sql/locations/serverKeyOperationResults/read | Obter operações em andamento em chaves de servidor de Transparent Data Encryption |
> | Ação | Microsoft. SQL/Locations/shortTermRetentionPolicyOperationResults/Read | Obtém o status de uma operação de política de retenção de curto prazo |
> | Ação | Microsoft.Sql/locations/syncAgentOperationResults/read | Recuperar o resultado da operação de recurso do agente de sincronização |
> | Ação | Microsoft.Sql/locations/syncDatabaseIds/read | Recuperar as IDs de banco de dados de sincronização para uma determinada região e assinatura |
> | Ação | Microsoft.Sql/locations/syncGroupOperationResults/read | Recuperar o resultado da operação de recurso do grupo de sincronização |
> | Ação | Microsoft.Sql/locations/syncMemberOperationResults/read | Recuperar o resultado da operação de recurso de membro de sincronização |
> | Ação | Microsoft.Sql/locations/usages/read | Obtém uma coleção de métricas de uso para esta assinatura em um local |
> | Ação | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Retorna os detalhes da operação assíncrona de regras de rede virtual especificada do Azure  |
> | Ação | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Retorna os detalhes da operação de regras de rede virtual especificada  |
> | Ação | Microsoft.Sql/managedInstances/administrators/delete | Exclui um administrador existente da instância gerenciada. |
> | Ação | Microsoft.Sql/managedInstances/administrators/read | Obtém uma lista de administradores de instância gerenciada. |
> | Ação | Microsoft.Sql/managedInstances/administrators/write | Cria ou atualiza o administrador da instância gerenciada com os parâmetros especificados. |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/backupLongTermRetentionPolicies/leitura | Obtém uma política de retenção de longo prazo para um banco de dados gerenciado |
> | Ação | Microsoft. SQL/managedInstances/databases/backupLongTermRetentionPolicies/Write | Atualiza uma política de retenção de longo prazo para um banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para um banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para um banco de dados gerenciado |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/colunas/leitura | Retornar uma lista de colunas para um banco de dados gerenciado |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/completeRestore/Action | Conclui a operação de restauração do banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Listar rótulos de sensibilidade de um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | Rótulos de sensibilidade de atualização do lote |
> | Ação | Microsoft.Sql/managedInstances/databases/delete | Exclui um banco de dados gerenciado existente |
> | Ação | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para bancos de dados de instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/databases/read | Obtém o banco de dados gerenciado existente |
> | Ação | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Listar rótulos de sensibilidade de um determinado banco de dados |
> | Ação | Microsoft. SQL/managedInstances/databases/recommendedSensitivityLabels/Write | Rótulos de sensibilidade recomendados da atualização do lote |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/restoreDetails/leitura | Retorna detalhes da restauração do banco de dados gerenciado enquanto a restauração está em andamento. |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/read | Obtenha um esquema de banco de dados gerenciado. |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Obter uma coluna de banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Excluir o rótulo de sensibilidade de uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Desabilitar recomendações de sensibilidade em uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Habilitar recomendações de sensibilidade em uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Obter o rótulo de sensibilidade de uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Criar ou atualizar o rótulo de sensibilidade de uma determinada coluna |
> | Ação | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Obter uma tabela de banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Recuperar uma lista de políticas de detecção de ameaças de banco de dados gerenciadas configuradas para um determinado servidor |
> | Ação | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Alterar a política de detecção de ameaças do banco de dados para um determinado banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/securityEvents/read | Recupera os eventos de segurança do banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Listar rótulos de sensibilidade de um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Recuperar detalhes do Transparent Data Encryption do banco de dados em um determinado banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Alterar o Transparent Data Encryption do banco de dados para um determinado banco de dados gerenciado |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Recuperar as políticas de avaliação de vulnerabilidade em um givendatabase |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Remover a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Obter a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Alterar a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Converta um resultado de exame existente em um formato legível por humanos. Se já existir, nada acontecerá |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Executar verificação de banco de dados de avaliação de vulnerabilidade. |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Retorne a lista de registros de verificação de avaliação de vulnerabilidade de banco de dados ou obtenha o registro de verificação para a ID de verificação especificada. |
> | Ação | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/managedInstances/databases/write | Cria um novo banco de dados ou atualiza um banco de dados existente. |
> | Ação | Microsoft.Sql/managedInstances/delete | Exclui uma instância gerenciada existente. |
> | Ação | Microsoft.Sql/managedInstances/encryptionProtector/read | Retorna uma lista de protetores de criptografia de servidor ou obtém as propriedades para o protetor de criptografia do servidor especificado. |
> | Ação | Microsoft. SQL/managedInstances/encryptionProtector/revalidate/Action | Atualize as propriedades do protetor de criptografia do servidor especificado. |
> | Ação | Microsoft.Sql/managedInstances/encryptionProtector/write | Atualize as propriedades do protetor de criptografia do servidor especificado. |
> | Ação | Microsoft. SQL/managedInstances/inaccessibleManagedDatabases/Read | Obter uma lista de bancos de dados gerenciados inacessíveis em uma instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/keys/delete | Exclui uma chave de Instância Gerenciada SQL do Azure existente. |
> | Ação | Microsoft.Sql/managedInstances/keys/read | Retornar a lista de chaves de instância gerenciada ou obter as propriedades da chave de instância gerenciada especificada. |
> | Ação | Microsoft.Sql/managedInstances/keys/write | Cria uma chave com os parâmetros especificados ou atualiza as propriedades ou marcas da chave de instância gerenciada especificada. |
> | Ação | Microsoft.Sql/managedInstances/metricDefinitions/read | Obter definições de métrica de instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/metrics/read | Obter métricas de instância gerenciada |
> | Ação | Microsoft. SQL/managedInstances/Operations/cancelamento/ação | Cancela o Azure SQL Instância Gerenciada operação assíncrona pendente que ainda não foi concluída. |
> | Ação | Microsoft. SQL/managedInstances/operações/leitura | Obter operações de instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para instâncias gerenciadas |
> | Ação | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para instâncias gerenciadas |
> | Ação | Microsoft.Sql/managedInstances/read | Retornar a lista de instâncias gerenciadas ou obter as propriedades da instância gerenciada especificada. |
> | Ação | Microsoft.Sql/managedInstances/recoverableDatabases/read | Retorna uma lista de bancos de dados gerenciados recuperáveis |
> | Ação | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para um banco de dados gerenciado Descartado |
> | Ação | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para um banco de dados gerenciado Descartado |
> | Ação | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Retorna uma lista de bancos de dados gerenciados retirados restauráveis. |
> | Ação | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Recuperar uma lista de políticas de detecção de ameaças do servidor gerenciado configuradas para um determinado servidor |
> | Ação | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor gerenciado para um determinado servidor gerenciado |
> | Ação | Microsoft.Sql/managedInstances/tdeCertificates/action | Criar/atualizar certificado TDE |
> | Ação | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade para uma determinada instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Recuperar as políticas de avaliação de vulnerabilidade em uma determinada instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade para uma determinada instância gerenciada |
> | Ação | Microsoft.Sql/managedInstances/write | Cria uma instância gerenciada com os parâmetros especificados ou atualiza as propriedades ou marcas para a instância gerenciada especificada. |
> | Ação | Microsoft.Sql/operations/read | Obter operações REST disponíveis |
> | Ação | Microsoft. SQL/privateEndpointConnectionsApproval/Action | Determina se o usuário tem permissão para aprovar uma conexão de ponto de extremidade privada |
> | Ação | Microsoft.Sql/register/action | Registra a assinatura para o provedor de recursos do banco de dados SQL da Microsoft e habilita a criação de banco de dados Microsoft SQL. |
> | Ação | Microsoft.Sql/servers/administrators/delete | Exclui um objeto de administrador de Azure Active Directory específico |
> | Ação | Microsoft.Sql/servers/administrators/read | Obtém um objeto de administrador de Azure Active Directory específico |
> | Ação | Microsoft.Sql/servers/administrators/write | Adiciona ou atualiza um objeto de administrador de Azure Active Directory específico |
> | Ação | Microsoft.Sql/servers/advisors/read | Retorna a lista de supervisores disponíveis para o servidor |
> | Ação | Microsoft.Sql/servers/advisors/recommendedActions/read | Retorna a lista de ações recomendadas do Orientador especificado para o servidor |
> | Ação | Microsoft.Sql/servers/advisors/recommendedActions/write | Aplicar a ação recomendada no servidor |
> | Ação | Microsoft.Sql/servers/advisors/write | Atualiza o status de execução automática de um supervisor no nível do servidor. |
> | Ação | Microsoft.Sql/servers/auditingPolicies/read | Recuperar detalhes da política de auditoria de tabela de servidor padrão configurada em um determinado servidor |
> | Ação | Microsoft.Sql/servers/auditingPolicies/write | Alterar a auditoria de tabela de servidor padrão para um determinado servidor |
> | Ação | Microsoft.Sql/servers/auditingSettings/operationResults/read | Recuperar o resultado da operação de definição de política de auditoria de blob do servidor |
> | Ação | Microsoft.Sql/servers/auditingSettings/read | Recuperar detalhes da política de auditoria de blob do servidor configurada em um determinado servidor |
> | Ação | Microsoft.Sql/servers/auditingSettings/write | Alterar a auditoria de blob do servidor para um determinado servidor |
> | Ação | Microsoft.Sql/servers/automaticTuning/read | Retorna configurações de ajuste automático para o servidor |
> | Ação | Microsoft.Sql/servers/automaticTuning/write | Atualiza as configurações de ajuste automático para o servidor e retorna as configurações atualizadas |
> | Ação | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Exclui um cofre de arquivamento de backup existente. |
> | Ação | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Esta operação é usada para obter um cofre de retenção de longo prazo de backup. Ele retorna informações sobre o cofre registrado para este servidor |
> | Ação | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Esta operação é usada para registrar um cofre de retenção de longo prazo de backup em um servidor |
> | Ação | Microsoft.Sql/servers/communicationLinks/delete | Exclui um link de comunicação do servidor existente. |
> | Ação | Microsoft.Sql/servers/communicationLinks/read | Retornar a lista de links de comunicação de um servidor especificado. |
> | Ação | Microsoft.Sql/servers/communicationLinks/write | Criar ou atualizar um link de comunicação do servidor. |
> | Ação | Microsoft.Sql/servers/connectionPolicies/read | Retornar a lista de políticas de conexão de servidor de um servidor especificado. |
> | Ação | Microsoft.Sql/servers/connectionPolicies/write | Criar ou atualizar uma política de conexão do servidor. |
> | Ação | Microsoft.Sql/servers/databases/advisors/read | Retorna a lista de supervisores disponíveis para o banco de dados |
> | Ação | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Retorna a lista de ações recomendadas do supervisor especificado para o banco de dados |
> | Ação | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Aplicar a ação recomendada no banco de dados |
> | Ação | Microsoft.Sql/servers/databases/advisors/write | Atualizar o status de execução automática de um supervisor no nível do banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/auditingPolicies/read | Recuperar detalhes da política de auditoria de tabela configurada em um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/auditingPolicies/write | Alterar a política de auditoria de tabela para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/auditingSettings/read | Recuperar detalhes da política de auditoria de blob configurada em um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/auditingSettings/write | Alterar a política de auditoria de BLOB para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/auditRecords/read | Recuperar os registros de auditoria de blob de banco de dados |
> | Ação | Microsoft.Sql/servers/databases/automaticTuning/read | Retorna configurações de ajuste automático para um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/automaticTuning/write | Atualiza as configurações de ajuste automático de um banco de dados e retorna configurações atualizadas |
> | Ação | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Obtém o status de uma operação de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Retornar a lista de políticas de arquivamento de backup de um banco de dados especificado. |
> | Ação | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Criar ou atualizar uma política de arquivamento de backup de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Obtém uma política de retenção de curto prazo para um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Atualiza uma política de retenção de curto prazo para um banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/colunas/leitura | Retornar uma lista de colunas para um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/connectionPolicies/read | Recuperar detalhes da política de conexão configurada em um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/connectionPolicies/write | Alterar a política de conexão para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Listar rótulos de sensibilidade de um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Rótulos de sensibilidade de atualização do lote |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Retornar a lista de políticas de mascaramento de dados do banco. |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Excluir regra de política de mascaramento de dados para um determinado banco |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Recuperar detalhes da regra de política de mascaramento de dados configurada em um determinado banco de dado |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Alterar regra de política de mascaramento de dados para determinado banco |
> | Ação | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Alterar a política de mascaramento de dados para um determinado banco |
> | Ação | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Retorna as informações da etapa de consulta distribuída da consulta data warehouse para a ID da etapa selecionada |
> | Ação | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Retorna as informações de consulta de distribuição de data warehouse para a ID de consulta selecionada |
> | Ação | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Recupera as atividades do usuário de uma instância de SQL Data Warehouse que inclui consultas em execução e suspensas |
> | Ação | Microsoft.Sql/servers/databases/delete | Exclui um banco de dados existente. |
> | Ação | Microsoft.Sql/servers/databases/export/action | Exportar banco de dados SQL do Azure |
> | Ação | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob estendida configurada em um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Alterar a política de auditoria de blob estendido para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/extensions/read | Obtém uma coleção de extensões para o banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/extensions/write | Alterar a extensão de um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/failover/action | Failover de banco de dados iniciado pelo cliente. |
> | Ação | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Recuperar políticas de backup geográfico para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Criar ou atualizar uma política de geobackup de banco de dados |
> | Ação | Microsoft.Sql/servers/databases/importExportOperationResults/read | Obter operações de importação/exportação em andamento |
> | Ação | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Obtém uma lista de janelas de manutenção disponíveis para um banco de dados selecionado. |
> | Ação | Microsoft.Sql/servers/databases/maintenanceWindows/read | Obtém as configurações do Windows de manutenção para um banco de dados selecionado. |
> | Ação | Microsoft.Sql/servers/databases/maintenanceWindows/write | Define as configurações do Windows de manutenção para um banco de dados selecionado. |
> | Ação | Microsoft.Sql/servers/databases/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.Sql/servers/databases/metrics/read | Retornar métricas para bancos de dados |
> | Ação | Microsoft.Sql/servers/databases/move/action | Altere o nome de um banco de dados existente. |
> | Ação | Microsoft.Sql/servers/databases/operationResults/read | Obtém o status de uma operação de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/operations/cancel/action | Cancela a operação assíncrona pendente do banco de dados SQL do Azure que ainda não foi concluída. |
> | Ação | Microsoft.Sql/servers/databases/operations/read | Retornar a lista de operações executadas no banco de dados |
> | Ação | Microsoft.Sql/servers/databases/pause/action | Pausar banco de dados de DataWarehouse do SQL do Azure |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Obtém os logs disponíveis para bancos de dados |
> | Ação | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Retorna a coleção de textos de consulta que correspondem aos parâmetros especificados. |
> | Ação | Microsoft.Sql/servers/databases/queryStore/read | Retorna os valores atuais das configurações de Repositório de Consultas para o banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/queryStore/write | Atualiza Repositório de Consultas configuração do banco de dados |
> | Ação | Microsoft.Sql/servers/databases/read | Retornar a lista de bancos de dados ou obter as propriedades do banco de dados especificado. |
> | Ação | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Listar rótulos de sensibilidade de um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/recommendedSensitivityLabels/gravação | Rótulos de sensibilidade recomendados da atualização do lote |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/delete | Encerrar a relação de replicação de modo forçado e com potencial perda de dados |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Failover após a sincronização de todas as alterações do primário, tornando esse banco de dados na \ u0027s de replicação primário e tornando o primário remoto em um secundário |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Failover imediatamente com potencial perda de dados, tornando esse banco de dado na \ u0027s de replicação primário e tornando o primário remoto em um secundário |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/read | Retornar a lista de links de replicação ou obter as propriedades dos links de replicação especificados. |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Encerrar a relação de replicação de modo forçado ou após a sincronização com o parceiro |
> | Ação | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Atualizar o modo de replicação para o link para o modo síncrono ou assíncrono |
> | Ação | Microsoft.Sql/servers/databases/restorePoints/action | Cria um novo ponto de restauração |
> | Ação | Microsoft.Sql/servers/databases/restorePoints/delete | Exclui um ponto de restauração para o banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/restorePoints/read | Retorna pontos de restauração para o banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/resume/action | Continuar banco de dados de DataWarehouse do SQL do Azure |
> | Ação | Microsoft.Sql/servers/databases/schemas/read | Obtenha um esquema de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Obter uma coluna de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Excluir o rótulo de sensibilidade de uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Desabilitar recomendações de sensibilidade em uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Habilitar recomendações de sensibilidade em uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Obter o rótulo de sensibilidade de uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Criar ou atualizar o rótulo de sensibilidade de uma determinada coluna |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/read | Obter uma tabela de banco de dados. |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Recuperar lista de recomendações de índice em um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Aplicar recomendação de índice |
> | Ação | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Recuperar uma lista de políticas de detecção de ameaças de banco de dados configuradas para um determinado servidor |
> | Ação | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Alterar a política de detecção de ameaças do banco de dados para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/securityMetrics/read | Obter uma coleção de métricas de segurança de banco de dados |
> | Ação | Microsoft.Sql/servers/databases/sensitivityLabels/read | Listar rótulos de sensibilidade de um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Retornar sugestões sobre o dimensionamento ou redução do banco de dados com base nas estatísticas de execução da consulta para melhorar o desempenho ou reduzir o custo |
> | Ação | Microsoft.Sql/servers/databases/skus/read | Obtém uma coleção de SKUs disponíveis para um banco de dados |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Cancelar sincronização do grupo de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/delete | Exclui um grupo de sincronização existente. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Retornar a lista de esquemas de banco de dados do hub de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/logs/read | Retornar a lista de logs do grupo de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/read | Retornar a lista de grupos de sincronização ou obter as propriedades do grupo de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Atualizar esquema de banco de dados do hub de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Recuperar o resultado da operação de atualização do esquema do hub de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Exclui um membro de sincronização existente. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Retornar a lista de membros de sincronização ou obter as propriedades para o membro de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Atualizar esquema de membro de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Recuperar o resultado da operação de atualização de esquema de membro de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Retornar a lista de esquemas de banco de dados do membro de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Cria um membro de sincronização com os parâmetros especificados ou atualiza as propriedades para o membro de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Disparar a sincronização do grupo de sincronização |
> | Ação | Microsoft.Sql/servers/databases/syncGroups/write | Cria um grupo de sincronização com os parâmetros especificados ou atualiza as propriedades do grupo de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/databases/topQueries/queryText/action | Retorna o texto Transact-SQL para a ID de consulta selecionada |
> | Ação | Microsoft.Sql/servers/databases/topQueries/read | Retorna estatísticas de tempo de execução agregadas para a consulta selecionada no período de tempo selecionado |
> | Ação | Microsoft.Sql/servers/databases/topQueries/statistics/read | Retorna estatísticas de tempo de execução agregadas para a consulta selecionada no período de tempo selecionado |
> | Ação | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Obter operações em andamento na Transparent Data Encryption |
> | Ação | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Recuperar o status e detalhes do recurso de segurança de Transparent Data Encryption para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Alterar estado de Transparent Data Encryption |
> | Ação | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Atualizar banco de dados de DataWarehouse do SQL do Azure |
> | Ação | Microsoft.Sql/servers/databases/usages/read | Obter as informações de uso do banco de dados SQL do Azure |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Recuperar as políticas de avaliação de vulnerabilidade em um givendatabase |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Remover a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Obter a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Alterar a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Converta um resultado de exame existente em um formato legível por humanos. Se já existir, nada acontecerá |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Executar verificação de banco de dados de avaliação de vulnerabilidade. |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Retorne a lista de registros de verificação de avaliação de vulnerabilidade de banco de dados ou obtenha o registro de verificação para a ID de verificação especificada. |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Executar verificação de banco de dados de avaliação de vulnerabilidade. |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Recuperar o resultado da operação de execução da verificação de avaliação de vulnerabilidade do banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Recuperar detalhes da avaliação de vulnerabilidade configurada em um determinado banco de dados |
> | Ação | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/workloadGroups/Delete | Descarta um grupo de carga de trabalho específico. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/workloadGroups/leitura | Lista os grupos de cargas de trabalho para um banco de dados selecionado. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/workloadGroups/workloadClassifiers/Delete | Descarta um classificador de carga de trabalho específico. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/workloadGroups/workloadClassifiers/leitura | Lista os classificadores de carga de trabalho para um banco de dados selecionado. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/workloadGroups/workloadClassifiers/gravação | Define as propriedades para um classificador de carga de trabalho específico. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/workloadGroups/gravação | Define as propriedades de um grupo de carga de trabalho específico. |
> | Ação | Microsoft.Sql/servers/databases/write | Cria um banco de dados com os parâmetros especificados ou atualiza as propriedades ou marcas do banco de dados especificado. |
> | Ação | Microsoft.Sql/servers/delete | Exclui um servidor existente. |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Exclui uma configuração de recuperação de desastre existente para um determinado servidor |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Failover de um DisasterRecoveryConfiguration |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Forçar failover de um DisasterRecoveryConfiguration |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Obtém uma coleção de configurações de recuperação de desastres que incluem este servidor |
> | Ação | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Alterar configuração de recuperação de desastre do servidor |
> | Ação | Microsoft.Sql/servers/elasticPoolEstimates/read | Retorna a lista de estimativas de pool elástico já criados para este servidor |
> | Ação | Microsoft.Sql/servers/elasticPoolEstimates/write | Cria uma nova estimativa de pool elástico para a lista de bancos de dados fornecidos |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/read | Retorna a lista de consultores disponíveis para o pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Retorna a lista de ações recomendadas do supervisor especificado para o pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Aplicar a ação recomendada no pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/advisors/write | Atualizar o status de execução automática de um supervisor no nível do pool elástico. |
> | Ação | Microsoft.Sql/servers/elasticPools/databases/read | Obter uma lista de bancos de dados para um pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/delete | Excluir pool elástico existente |
> | Ação | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Recuperar atividades e detalhes em um determinado pool de banco de dados elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Recuperar atividades e detalhes em um determinado banco de dados que faz parte do pool de banco de dados elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/failover/action | Failover de pool elástico iniciado pelo cliente. |
> | Ação | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para pools de banco de dados elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/metrics/read | Retornar métricas para pools de banco de dados elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Cancela a operação assíncrona pendente do pool elástico do SQL do Azure que ainda não foi concluída. |
> | Ação | Microsoft.Sql/servers/elasticPools/operations/read | Retornar a lista de operações executadas no pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para pools de banco de dados elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/read | Recuperar detalhes do pool elástico em um determinado servidor |
> | Ação | Microsoft.Sql/servers/elasticPools/skus/read | Obtém uma coleção de SKUs disponíveis para um pool elástico |
> | Ação | Microsoft.Sql/servers/elasticPools/write | Criar uma nova propriedade ou alterar as propriedades de um pool elástico existente |
> | Ação | Microsoft.Sql/servers/encryptionProtector/read | Retorna uma lista de protetores de criptografia de servidor ou obtém as propriedades para o protetor de criptografia do servidor especificado. |
> | Ação | Microsoft. SQL/Servers/encryptionProtector/revalidate/Action | Atualize as propriedades do protetor de criptografia do servidor especificado. |
> | Ação | Microsoft.Sql/servers/encryptionProtector/write | Atualize as propriedades do protetor de criptografia do servidor especificado. |
> | Ação | Microsoft.Sql/servers/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob de servidor estendida configurada em um determinado servidor |
> | Ação | Microsoft.Sql/servers/extendedAuditingSettings/write | Alterar a auditoria de blob de servidor estendida para um determinado servidor |
> | Ação | Microsoft.Sql/servers/failoverGroups/delete | Exclui um grupo de failover existente. |
> | Ação | Microsoft.Sql/servers/failoverGroups/failover/action | Executa o failover planejado em um grupo de failover existente. |
> | Ação | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Executa o failover forçado em um grupo de failover existente. |
> | Ação | Microsoft.Sql/servers/failoverGroups/read | Retorna a lista de grupos de failover ou obtém as propriedades para o grupo de failover especificado. |
> | Ação | Microsoft.Sql/servers/failoverGroups/write | Cria um grupo de failover com os parâmetros especificados ou atualiza as propriedades ou marcas para o grupo de failover especificado. |
> | Ação | Microsoft.Sql/servers/firewallRules/delete | Exclui uma regra de firewall de servidor existente. |
> | Ação | Microsoft.Sql/servers/firewallRules/read | Retornar a lista de regras de firewall do servidor ou obter as propriedades da regra de firewall do servidor especificada. |
> | Ação | Microsoft.Sql/servers/firewallRules/write | Cria uma regra de firewall de servidor com os parâmetros especificados, atualiza as propriedades da regra especificada ou substitui todas as regras existentes por novas regras de firewall do servidor. |
> | Ação | Microsoft.Sql/servers/import/action | Criar um novo banco de dados no servidor e implantar o esquema e o dado de um pacote DacPac |
> | Ação | Microsoft.Sql/servers/importExportOperationResults/read | Obter operações de importação/exportação em andamento |
> | Ação | Microsoft. SQL/Servers/inaccessibleDatabases/Read | Retornar uma lista de banco (s) de dados inacessíveis em um servidor lógico. |
> | Ação | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Exclui o perfil de ponto de extremidade de interface especificado |
> | Ação | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Retorna as propriedades do perfil de ponto de extremidade de interface especificado |
> | Ação | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Cria um perfil de ponto de extremidade de interface com os parâmetros especificados ou atualiza as propriedades ou marcas para o ponto de extremidade de interface especificado |
> | Ação | Microsoft.Sql/servers/jobAgents/delete | Exclui um agente de trabalho do BD SQL do Azure |
> | Ação | Microsoft.Sql/servers/jobAgents/read | Obter um agente de trabalho do BD SQL do Azure |
> | Ação | Microsoft.Sql/servers/jobAgents/write | Criar ou atualizar um agente de trabalho do BD SQL do Azure |
> | Ação | Microsoft.Sql/servers/keys/delete | Exclui uma chave de servidor existente. |
> | Ação | Microsoft.Sql/servers/keys/read | Retornar a lista de chaves de servidor ou obter as propriedades para a chave de servidor especificada. |
> | Ação | Microsoft.Sql/servers/keys/write | Cria uma chave com os parâmetros especificados ou atualiza as propriedades ou marcas para a chave de servidor especificada. |
> | Ação | Microsoft.Sql/servers/operationResults/read | Obter operações de servidor em andamento |
> | Ação | Microsoft. SQL/Servers/Operations/Read | Retornar a lista de operações executadas no servidor |
> | Ação | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Exclui um proxy de conexão de ponto de extremidade privado existente |
> | Ação | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Retorna a lista de proxies de conexão de ponto de extremidade privado ou obtém as propriedades para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Valida uma conexão de ponto de extremidade privada criar chamada do lado do NRP |
> | Ação | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Cria um proxy de conexão de ponto de extremidade privado com os parâmetros especificados ou atualiza as propriedades ou marcas para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft.Sql/servers/privateEndpointConnections/delete | Exclui uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft.Sql/servers/privateEndpointConnections/read | Retorna a lista de conexões de ponto de extremidade privado ou obtém as propriedades para a conexão de ponto de extremidade particular especificada. |
> | Ação | Microsoft.Sql/servers/privateEndpointConnections/write | Aprova ou rejeita uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft. SQL/Servers/privateEndpointConnectionsApproval/Action | Determina se o usuário tem permissão para aprovar uma conexão de ponto de extremidade privada |
> | Ação | Microsoft.Sql/servers/privateLinkResources/read | Obter os recursos de link privado para o SQL Server correspondente |
> | Ação | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Retornar tipos de métricas que estão disponíveis para servidores |
> | Ação | Microsoft.Sql/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Recuperar métricas para pools de banco de dados elástico recomendados para um determinado servidor |
> | Ação | Microsoft.Sql/servers/recommendedElasticPools/read | Recupere a recomendação para pools de banco de dados elástico para reduzir o custo ou melhorar o desempenho com base na utilização histórica de recursos |
> | Ação | Microsoft.Sql/servers/recoverableDatabases/read | Esta operação é usada para recuperação de desastre do banco de dados em tempo real para restaurar o banco de dados para o último ponto de backup bom conhecido. Ele retorna informações sobre o último backup bom, mas ele doesn\u0027t realmente restaurar o banco de dados. |
> | Ação | Microsoft.Sql/servers/replicationLinks/read | Retornar a lista de links de replicação ou obter as propriedades dos links de replicação especificados. |
> | Ação | Microsoft.Sql/servers/restorableDroppedDatabases/read | Obtenha uma lista de bancos de dados que foram descartados em um determinado servidor que ainda estão na política de retenção. |
> | Ação | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Recuperar resultados da operação de gravação da política de detecção de ameaças do servidor |
> | Ação | Microsoft.Sql/servers/securityAlertPolicies/read | Recuperar uma lista de políticas de detecção de ameaças do servidor configuradas para um determinado servidor |
> | Ação | Microsoft.Sql/servers/securityAlertPolicies/write | Alterar a política de detecção de ameaças do servidor de um determinado servidor |
> | Ação | Microsoft.Sql/servers/serviceObjectives/read | Recuperar a lista de objetivos de nível de serviço (também conhecidos como camadas de desempenho) disponíveis em um determinado servidor |
> | Ação | Microsoft.Sql/servers/syncAgents/delete | Exclui um agente de sincronização existente. |
> | Ação | Microsoft.Sql/servers/syncAgents/generateKey/action | Gerar chave de registro do agente de sincronização |
> | Ação | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Retornar a lista de bancos de dados vinculados do agente de sincronização |
> | Ação | Microsoft.Sql/servers/syncAgents/read | Retornar a lista de agentes de sincronização ou obter as propriedades para o agente de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/syncAgents/write | Cria um agente de sincronização com os parâmetros especificados ou atualiza as propriedades para o agente de sincronização especificado. |
> | Ação | Microsoft.Sql/servers/tdeCertificates/action | Criar/atualizar certificado TDE |
> | Ação | Microsoft.Sql/servers/usages/read | Retornar a cota de DTU do servidor e o consumo atual de DTU por todos os bancos de dados no servidor |
> | Ação | Microsoft.Sql/servers/virtualNetworkRules/delete | Exclui uma regra de rede virtual existente |
> | Ação | Microsoft.Sql/servers/virtualNetworkRules/read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft.Sql/servers/virtualNetworkRules/write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou marcas para a regra de rede virtual especificada. |
> | Ação | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Remover a avaliação de vulnerabilidade para um determinado servidor |
> | Ação | Microsoft.Sql/servers/vulnerabilityAssessments/read | Recuperar as políticas de avaliação de vulnerabilidade em um determinado servidor |
> | Ação | Microsoft.Sql/servers/vulnerabilityAssessments/write | Alterar a avaliação de vulnerabilidade para um determinado servidor |
> | Ação | Microsoft.Sql/servers/write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou marcas para o servidor especificado. |
> | Ação | Microsoft.Sql/unregister/action | Cancela o registro da assinatura para o provedor de recursos do banco de dados SQL da Microsoft e habilita a criação de banco de dados Microsoft SQL. |
> | Ação | Microsoft.Sql/virtualClusters/delete | Exclui um cluster virtual existente. |
> | Ação | Microsoft.Sql/virtualClusters/read | Retornar a lista de clusters virtuais ou obter as propriedades para o cluster virtual especificado. |
> | Ação | Microsoft.Sql/virtualClusters/write | Atualiza as marcas do cluster virtual. |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. SqlVirtualMachine/Locations/availabilityGroupListenerOperationResults/Read | Obter o resultado de uma operação de ouvinte de grupo de disponibilidade |
> | Ação | Microsoft. SqlVirtualMachine/Locations/sqlVirtualMachineGroupOperationResults/Read | Obter resultado de uma operação de grupo de máquinas virtuais do SQL |
> | Ação | Microsoft. SqlVirtualMachine/Locations/sqlVirtualMachineOperationResults/Read | Obter resultado da operação de máquina virtual do SQL |
> | Ação | Microsoft. SqlVirtualMachine/operações/leitura |  |
> | Ação | Microsoft. SqlVirtualMachine/registro/ação | Registrar a assinatura com o provedor de recursos Microsoft. SqlVirtualMachine |
> | Ação | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Delete | Excluir ouvinte do grupo de disponibilidade existente |
> | Ação | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Read | Recuperar detalhes do ouvinte do grupo de disponibilidade do SQL em um determinado grupo de máquinas virtuais do SQL |
> | Ação | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Write | Criar uma nova ou alterar as propriedades do ouvinte do grupo de disponibilidade do SQL existente |
> | Ação | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/Delete | Excluir grupo de máquinas virtuais SQL existente |
> | Ação | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/Read | Detalhes de RETR do grupo de máquinas virtuais do SQL |
> | Ação | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/sqlVirtualMachines/Read | Listar máquinas virtuais do SQL por um grupo de máquina virtual virtual do SQL específico |
> | Ação | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/Write | Criar uma nova propriedade ou alterar as propriedades do grupo de máquinas virtuais SQL existente |
> | Ação | Microsoft. SqlVirtualMachine/sqlVirtualMachines/Delete | Excluir máquina virtual SQL existente |
> | Ação | Microsoft. SqlVirtualMachine/sqlVirtualMachines/Read | Recuperar detalhes da máquina virtual do SQL |
> | Ação | Microsoft. SqlVirtualMachine/sqlVirtualMachines/Write | Criar uma nova propriedade ou alterar as propriedades de uma máquina virtual SQL existente |
> | Ação | Microsoft. SqlVirtualMachine/cancelar registro/ação | Cancelar o registro de assinatura com o provedor de recursos Microsoft. SqlVirtualMachine |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Storage/checknameavailability/read | Verifica se o nome da conta é válido e não está em uso. |
> | Ação | Microsoft.Storage/locations/checknameavailability/read | Verifica se o nome da conta é válido e não está em uso. |
> | Ação | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Notifica o Microsoft. Storage que a rede virtual ou sub-rede está sendo excluída |
> | Ação | Microsoft.Storage/locations/usages/read | Retorna o limite e a contagem de uso atual dos recursos na assinatura especificada |
> | Ação | Microsoft.Storage/operations/read | Sonda o status de uma operação assíncrona. |
> | Ação | Microsoft.Storage/register/action | Registra a assinatura para o provedor de recursos de armazenamento e habilita a criação de contas de armazenamento. |
> | Ação | Microsoft.Storage/skus/read | Lista os SKUs com suporte no Microsoft. Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Retorna o resultado da adição de conteúdo de BLOB |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Retorna o resultado da exclusão de um blob |
> | DataAction | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/deleteBlobVersion/ação | Retorna o resultado da exclusão de uma versão de BLOB |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Retorna a lista de BLOBs em uma conta com filtro de marcas correspondentes |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retorna um BLOB ou uma lista de BLOBs |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | Retorna o resultado do comando blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Retorna o resultado da leitura de marcas de BLOB |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Retorna o resultado da gravação de marcas de BLOB |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Retorna o resultado da gravação de um blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Limpar a retenção legal do contêiner de BLOB |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Retorna o resultado da exclusão de um contêiner |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Excluir a política de imutabilidade do contêiner de BLOBs |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Estender a política de imutabilidade do contêiner de BLOBs |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Bloquear a política de imutabilidade do contêiner de BLOB |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Obter política de imutabilidade do contêiner de BLOB |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Colocar a política de imutabilidade do contêiner de BLOBs |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Retorna o resultado do contêiner de blob de concessão |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retorna um contêiner |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retorna a lista de contêineres |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Definir retenção legal de contêiner de BLOB |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retorna o resultado do contêiner de blob de patch |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retorna o resultado do contêiner de blob put |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retorna uma chave de delegação de usuário para o serviço blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/read | Retorna propriedades ou estatísticas do serviço blob |
> | Ação | Microsoft.Storage/storageAccounts/blobServices/write | Retorna o resultado das propriedades do serviço de blob put |
> | Ação | Microsoft.Storage/storageAccounts/delete | Exclui uma conta de armazenamento existente. |
> | Ação | Microsoft. Storage/storageAccounts/encryptionScopes/Read |  |
> | Ação | Microsoft. Storage/storageAccounts/encryptionScopes/Read |  |
> | Ação | Microsoft. Storage/storageAccounts/encryptionScopes/Write |  |
> | Ação | Microsoft. Storage/storageAccounts/encryptionScopes/Write |  |
> | Ação | Microsoft.Storage/storageAccounts/failover/action | O cliente é capaz de controlar o failover em caso de problemas de disponibilidade |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action | Obter privilégios de administrador de arquivos |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Retorna o resultado da exclusão de um arquivo/pasta |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Retorna o resultado da permissão de modificação em um arquivo/pasta |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retorna um arquivo/pasta ou uma lista de arquivos/pastas |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Retorna o resultado da gravação de um arquivo ou da criação de uma pasta |
> | Ação | Microsoft.Storage/storageAccounts/fileServices/read |  |
> | Ação | Microsoft.Storage/storageAccounts/fileServices/read | Obter propriedades do serviço de arquivo |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/shares/Delete |  |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/leitura |  |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/leitura |  |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/shares/Write |  |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/Write |  |
> | Ação | Microsoft.Storage/storageAccounts/listAccountSas/action | Retorna o token SAS da conta para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/listkeys/action | Retorna as chaves de acesso para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/listServiceSas/action | Retorna o token SAS do serviço para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/managementPolicies/delete | Excluir políticas de gerenciamento de conta de armazenamento |
> | Ação | Microsoft.Storage/storageAccounts/managementPolicies/read | Obter políticas de conta de gerenciamento de armazenamento |
> | Ação | Microsoft.Storage/storageAccounts/managementPolicies/write | Políticas de gerenciamento de conta de armazenamento put |
> | Ação | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Delete |  |
> | Ação | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Read |  |
> | Ação | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Write |  |
> | Ação | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Excluir proxies de conexão de ponto de extremidade privado |
> | Ação | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Read | Obter proxy de conexão de ponto de extremidade privado |
> | Ação | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | Colocar proxies de conexão de ponto de extremidade privado |
> | Ação | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | Excluir conexão de ponto de extremidade particular |
> | Ação | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Obter conexão de ponto de extremidade privado |
> | Ação | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Colocar conexão de ponto de extremidade particular |
> | Ação | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/Action | Aprovar conexões de ponto de extremidade privado |
> | Ação | Microsoft.Storage/storageAccounts/privateLinkResources/read | Obter StorageAccount groupids |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Retorna o resultado da exclusão de uma fila |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Retorna o resultado da adição de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Retorna o resultado da exclusão de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Retorna o resultado do processamento de uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Retorna uma mensagem |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Retorna o resultado da gravação de uma mensagem |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retorna uma fila ou uma lista de filas. |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/queues/write | Retorna o resultado da gravação de uma fila |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/read | Obter serviço Fila Propriedades |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/read | Retorna propriedades ou estatísticas do serviço fila. |
> | Ação | Microsoft.Storage/storageAccounts/queueServices/write | Retorna o resultado da configuração das propriedades do serviço fila |
> | Ação | Microsoft.Storage/storageAccounts/read | Retorna a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenera as chaves de acesso para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | Restaurar intervalos de BLOB para o estado do tempo especificado |
> | Ação | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Revoga todas as chaves de delegação de usuário para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Criar/atualizar configurações de diagnóstico da conta de armazenamento. |
> | Ação | Microsoft.Storage/storageAccounts/tableServices/read | Obter propriedades do serviço tabela |
> | Ação | Microsoft.Storage/storageAccounts/write | Cria uma conta de armazenamento com os parâmetros especificados ou atualiza as propriedades ou marcas ou adiciona um domínio personalizado para a conta de armazenamento especificada. |
> | Ação | Microsoft.Storage/usages/read | Retorna o limite e a contagem de uso atual dos recursos na assinatura especificada |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | microsoft.storagesync/locations/checkNameAvailability/action | Verifica se o nome do serviço de sincronização de armazenamento é válido e não está em uso. |
> | Ação | microsoft.storagesync/locations/workflows/operations/read | Obtém o status de uma operação assíncrona |
> | Ação | Microsoft. storagesync/operações/leitura | Obtém uma lista das operações com suporte |
> | Ação | Microsoft. storagesync/registro/ação | Registra a assinatura para o provedor de sincronização de armazenamento |
> | Ação | microsoft.storagesync/storageSyncServices/delete | Excluir qualquer serviço de sincronização de armazenamento |
> | Ação | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para os serviços de sincronização de armazenamento |
> | Ação | microsoft.storagesync/storageSyncServices/read | Ler serviços de sincronização de armazenamento |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/delete | Excluir qualquer servidor registrado |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para o servidor registrado |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/read | Ler qualquer servidor registrado |
> | Ação | microsoft.storagesync/storageSyncServices/registeredServers/write | Criar ou atualizar qualquer servidor registrado |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Excluir qualquer ponto de extremidade de nuvem |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Obtém o status de uma operação de backup/restauração assíncrona |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Chamar esta ação após o backup |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Chamar esta ação após a restauração |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Chamar esta ação antes do backup |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Chamar esta ação antes da restauração |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Ler pontos de extremidade de nuvem |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Restaurar pulsação |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/triggerChangeDetection/Action | Chamar esta ação para disparar a detecção de alterações no compartilhamento de arquivos de um ponto de extremidade de nuvem |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Criar ou atualizar pontos de extremidade de nuvem |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/delete | Excluir todos os grupos de sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para grupos de sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/read | Ler todos os grupos de sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Excluir qualquer ponto de extremidade do servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para pontos de extremidade do servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Ler qualquer ponto de extremidade do servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Chamar esta ação para recuperar arquivos em um servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Criar ou atualizar pontos de extremidade de servidor |
> | Ação | microsoft.storagesync/storageSyncServices/syncGroups/write | Criar ou atualizar grupos de sincronização |
> | Ação | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Obtém o status de uma operação assíncrona |
> | Ação | microsoft.storagesync/storageSyncServices/workflows/operations/read | Obtém o status de uma operação assíncrona |
> | Ação | microsoft.storagesync/storageSyncServices/workflows/read | Ler fluxos de trabalho |
> | Ação | microsoft.storagesync/storageSyncServices/write | Criar ou atualizar serviços de sincronização de armazenamento |
> | Ação | Microsoft. storagesync/cancelar registro/ação | Cancela o registro da assinatura para o provedor de sincronização de armazenamento |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/delete | Exclui os registros de controle de acesso |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/read | Listar ou obter os registros de controle de acesso |
> | Ação | Microsoft.StorSimple/managers/accessControlRecords/write | Criar ou atualizar os registros de controle de acesso |
> | Ação | Microsoft.StorSimple/managers/alerts/read | Listar ou obter os alertas |
> | Ação | Microsoft.StorSimple/managers/backups/read | Listar ou obter o conjunto de backup |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/delete | Exclui as configurações de largura de banda existentes (somente série 8000) |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Listar os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/read | Listar as configurações de largura de banda (somente série 8000) |
> | Ação | Microsoft.StorSimple/managers/bandwidthSettings/write | Cria uma nova ou atualiza as configurações de largura de banda (somente série 8000) |
> | Ação | Microsoft.StorSimple/managers/certificates/write | Criar ou atualizar os certificados |
> | Ação | Microsoft.StorSimple/Managers/certificates/write | A operação atualizar certificado de recurso atualiza o certificado de credencial do cofre/recurso. |
> | Ação | Microsoft.StorSimple/managers/clearAlerts/action | Desmarque todos os alertas associados ao Gerenciador de dispositivos. |
> | Ação | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Listar as configurações com suporte do dispositivo de nuvem |
> | Ação | Microsoft.StorSimple/managers/configureDevice/action | Configura um dispositivo |
> | Ação | Microsoft.StorSimple/managers/delete | Exclui os gerenciadores de dispositivos |
> | Ação | Microsoft.StorSimple/Managers/delete | A operação excluir cofre exclui o recurso do Azure especificado do tipo ' cofre ' |
> | Ação | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/alertSettings/read | Listar ou obter as configurações de alerta |
> | Ação | Microsoft.StorSimple/managers/devices/alertSettings/write | Criar ou atualizar as configurações de alerta |
> | Ação | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Autorizar a substituição da chave de criptografia de serviço dos dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Faça um backup manual para criar um backup sob demanda de todos os volumes protegidos pela política. |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Excluir políticas de backup existentes (somente série 8000) |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Listar os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/read | Listar as políticas de backup (somente série 8000) |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Exclui um agendamento existente |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Listar os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Listar as agendas |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Cria um novo ou atualiza agendamentos |
> | Ação | Microsoft.StorSimple/managers/devices/backupPolicies/write | Cria uma nova ou atualiza as políticas de backup (somente série 8000) |
> | Ação | Microsoft.StorSimple/managers/devices/backups/delete | Exclui o conjunto de backup |
> | Ação | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Clone um compartilhamento ou volume usando um elemento de backup. |
> | Ação | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backups/read | Listar ou obter o conjunto de backup |
> | Ação | Microsoft.StorSimple/managers/devices/backups/restore/action | Restaure todos os volumes de um conjunto de backup. |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Exclui os grupos de agendamento de backup |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Listar ou obter os grupos de agendamento de backup |
> | Ação | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Criar ou atualizar os grupos de agendamento de backup |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/delete | Exclui as configurações de CHAP |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/read | Listar ou obter as configurações de CHAP |
> | Ação | Microsoft.StorSimple/managers/devices/chapSettings/write | Criar ou atualizar as configurações de CHAP |
> | Ação | Microsoft.StorSimple/managers/devices/deactivate/action | Desativa um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/delete | Exclui os dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/disks/read | Listar ou obter os discos |
> | Ação | Microsoft.StorSimple/managers/devices/download/action | Baixar atualizações para um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/failover/action | Failover do dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/failoverTargets/read | Listar ou obter os destinos de failover dos dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Faça backup de um servidor de arquivos. |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/delete | Exclui os servidores de arquivos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Listar ou obter as métricas |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Listar ou obter as definições de métricas |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/read | Listar ou obter os servidores de arquivos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Exclui os compartilhamentos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Listar ou obter as métricas |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Listar ou obter as definições de métricas |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Listar ou obter os compartilhamentos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Criar ou atualizar os compartilhamentos |
> | Ação | Microsoft.StorSimple/managers/devices/fileservers/write | Criar ou atualizar os servidores de arquivos |
> | Ação | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Alterar o estado de energia do controlador de grupos de componentes de hardware |
> | Ação | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Listar os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Listar os grupos de componentes de hardware |
> | Ação | Microsoft.StorSimple/managers/devices/install/action | Instalar atualizações em um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/installUpdates/action | Instala atualizações nos dispositivos (somente série 8000). |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Faça backup de um servidor iSCSI. |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Exclui os servidores iSCSI |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Exclui os discos |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Listar ou obter as métricas |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Listar ou obter as definições de métricas |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Listar ou obter os discos |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Criar ou atualizar os discos |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Listar ou obter as métricas |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Listar ou obter as definições de métricas |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/read | Listar ou obter os servidores iSCSI |
> | Ação | Microsoft.StorSimple/managers/devices/iscsiservers/write | Criar ou atualizar os servidores iSCSI |
> | Ação | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Cancelar um trabalho em execução |
> | Ação | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Listar os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/jobs/read | Listar ou obter os trabalhos |
> | Ação | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Listar os conjuntos de failover para um dispositivo existente (somente série 8000). |
> | Ação | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Listar destinos de failover dos dispositivos (somente série 8000). |
> | Ação | Microsoft.StorSimple/managers/devices/metrics/read | Listar ou obter as métricas |
> | Ação | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Listar ou obter as definições de métricas |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Confirma uma migração bem-sucedida e confirmá-la. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Listar o status de confirmação de migração |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Busque o status de confirmação da migração. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Busque o status do trabalho de estimativa de migração. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Busque o status da migração. |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importar configurações de origem para migração |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Listar a estimativa de migração |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Listar o status de migração |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Listar os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Iniciar a migração usando as configurações de origem |
> | Ação | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Inicie um trabalho para estimar a duração do processo de migração. |
> | Ação | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Listar os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/networkSettings/read | Listar ou obter as configurações de rede |
> | Ação | Microsoft.StorSimple/managers/devices/networkSettings/write | Cria uma nova ou atualiza as configurações de rede |
> | Ação | Microsoft.StorSimple/managers/devices/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Listar a chave de criptografia pública do Gerenciador de dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publicar o pacote de suporte para um dispositivo existente. Um pacote de suporte do StorSimple é um mecanismo fácil de usar que coleta todos os logs relevantes para auxiliar Suporte da Microsoft com a solução de problemas de dispositivos StorSimple. |
> | Ação | Microsoft.StorSimple/managers/devices/read | Listar ou obter os dispositivos |
> | Ação | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Verificar se há atualizações em um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/read | Listar as configurações de segurança |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Sincronizar o certificado de gerenciamento remoto para um dispositivo. |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Atualize as configurações de segurança. |
> | Ação | Microsoft.StorSimple/managers/devices/securitySettings/write | Cria uma nova ou atualiza configurações de segurança |
> | Ação | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Enviar email de alerta de teste para destinatários de email configurados. |
> | Ação | Microsoft.StorSimple/managers/devices/shares/read | Listar ou obter os compartilhamentos |
> | Ação | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Listar os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/timeSettings/read | Listar ou obter as configurações de tempo |
> | Ação | Microsoft.StorSimple/managers/devices/timeSettings/write | Cria uma nova ou atualiza as configurações de hora |
> | Ação | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/updateSummary/read | Listar ou obter o resumo de atualizações |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Exclui um contêiner de volume existente (somente série 8000) |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Listar as métricas |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Listar as definições de métricas |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Listar os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/read | Listar os contêineres de volume (somente série 8000) |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Exclui um volume existente |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Listar as métricas |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Listar as definições de métricas |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Listar os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Listar os volumes |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Cria um novo ou atualiza volumes |
> | Ação | Microsoft.StorSimple/managers/devices/volumeContainers/write | Cria um novo ou atualiza contêineres de volume (somente série 8000) |
> | Ação | Microsoft.StorSimple/managers/devices/volumes/read | Listar os volumes |
> | Ação | Microsoft.StorSimple/managers/devices/write | Criar ou atualizar os dispositivos |
> | Ação | Microsoft.StorSimple/managers/encryptionSettings/read | Listar ou obter as configurações de criptografia |
> | Ação | Microsoft.StorSimple/managers/extendedInformation/delete | Exclui as informações do cofre estendido |
> | Ação | Microsoft.StorSimple/Managers/extendedInformation/delete | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Ação | Microsoft.StorSimple/managers/extendedInformation/read | Listar ou obter as informações do cofre estendido |
> | Ação | Microsoft.StorSimple/Managers/extendedInformation/read | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Ação | Microsoft.StorSimple/managers/extendedInformation/write | Criar ou atualizar as informações do cofre estendido |
> | Ação | Microsoft.StorSimple/Managers/extendedInformation/write | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Ação | Microsoft.StorSimple/managers/features/read | Listar os recursos |
> | Ação | Microsoft.StorSimple/managers/fileservers/read | Listar ou obter os servidores de arquivos |
> | Ação | Microsoft.StorSimple/managers/getEncryptionKey/action | Obtenha a chave de criptografia para o Gerenciador de dispositivos. |
> | Ação | Microsoft.StorSimple/managers/iscsiservers/read | Listar ou obter os servidores iSCSI |
> | Ação | Microsoft.StorSimple/managers/jobs/read | Listar ou obter os trabalhos |
> | Ação | Microsoft.StorSimple/managers/listActivationKey/action | Obtém a chave de ativação do Device Manager StorSimple. |
> | Ação | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Listar chaves de criptografia pública de um Device Manager StorSimple. |
> | Ação | Microsoft.StorSimple/managers/metrics/read | Listar ou obter as métricas |
> | Ação | Microsoft.StorSimple/managers/metricsDefinitions/read | Listar ou obter as definições de métricas |
> | Ação | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrar o clássico para o Gerenciador de recursos dos gerenciadores |
> | Ação | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Listar as configurações de origem de migração (somente série 8000) |
> | Ação | Microsoft.StorSimple/managers/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Crie um novo dispositivo de nuvem. |
> | Ação | Microsoft.StorSimple/managers/read | Listar ou obter os gerenciadores de dispositivos |
> | Ação | Microsoft.StorSimple/Managers/read | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Ação | Microsoft.StorSimple/managers/regenerateActivationKey/action | Regenerar a chave de ativação para um Device Manager StorSimple existente. |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Exclui as credenciais da conta de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/read | Listar ou obter as credenciais da conta de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageAccountCredentials/write | Criar ou atualizar as credenciais da conta de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageDomains/delete | Exclui os domínios de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Listar ou obter os resultados da operação |
> | Ação | Microsoft.StorSimple/managers/storageDomains/read | Listar ou obter os domínios de armazenamento |
> | Ação | Microsoft.StorSimple/managers/storageDomains/write | Criar ou atualizar os domínios de armazenamento |
> | Ação | Microsoft.StorSimple/managers/write | Criar ou atualizar os gerenciadores de dispositivos |
> | Ação | Microsoft.StorSimple/Managers/write | A operação criar cofre cria um recurso do Azure do tipo ' cofre ' |
> | Ação | Microsoft.StorSimple/operations/read | Listar ou obter as operações |
> | Ação | Microsoft.StorSimple/register/action | Registrar provedor Microsoft. StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.StreamAnalytics/locations/quotas/Read | Ler Stream Analytics cota de assinatura |
> | Ação | Microsoft.StreamAnalytics/operations/Read | Ler Stream Analytics operações |
> | Ação | Microsoft.StreamAnalytics/Register/action | Registrar assinatura com provedor de recursos Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Delete | Excluir trabalho de Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Excluir função de trabalho Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Ler resultados da operação para Stream Analytics função de trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Ler Stream Analytics função de trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Recuperar a definição padrão de uma função de trabalho Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Testar função de trabalho de Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Gravar Stream Analytics função de trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Excluir Stream Analytics entrada de trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Ler resultados da operação para Stream Analytics entrada do trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Ler Stream Analytics entrada do trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Exemplo de entrada de trabalho de Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Testar Stream Analytics entrada do trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Gravar Stream Analytics entrada de trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Ler resultados da operação para Stream Analytics trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Excluir Stream Analytics saída de trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Ler resultados da operação para Stream Analytics saída do trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Ler Stream Analytics saída do trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Testar a saída do trabalho de Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Gravar Stream Analytics saída de trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Ler configuração de diagnóstico. |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Gravar configuração de diagnóstico. |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Obter os logs disponíveis para streamingjobs |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Obter as métricas disponíveis para streamingjobs |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/PublishEdgePackage/Action | Publicar pacote do Edge para trabalho Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Read | Ler Stream Analytics trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/escala/ação | Dimensionar Stream Analytics trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Start/action | Iniciar Tarefa do Stream Analytics |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Parar Stream Analytics trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Excluir Stream Analytics transformação de trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Ler Stream Analytics transformação de trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Gravar Stream Analytics transformação de trabalho |
> | Ação | Microsoft.StreamAnalytics/streamingjobs/Write | Gravar Stream Analytics trabalho |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Subscription/cancelamento/ação | Cancela a assinatura |
> | Ação | Microsoft.Subscription/CreateSubscription/action | Criar uma subscrição do Azure |
> | Ação | Microsoft.Subscription/register/action | Registra a assinatura com o provedor de recursos Microsoft. Subscription |
> | Ação | Microsoft. Subscription/renomear/ação | Renomeia a assinatura |
> | Ação | Microsoft.Subscription/SubscriptionDefinitions/read | Obtenha uma definição de assinatura do Azure dentro de um grupo de gerenciamento. |
> | Ação | Microsoft.Subscription/SubscriptionDefinitions/write | Criar uma definição de assinatura do Azure |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Support/register/action | Regista para o Fornecedor de Recursos de Suporte |
> | Ação | Microsoft.Support/supportTickets/read | Obtém os detalhes do Pedido de Suporte (incluindo o estado, gravidade, detalhes de contacto e comunicações) ou obtém a lista dos Pedidos de Suporte nas subscrições. |
> | Ação | Microsoft.Support/supportTickets/write | Cria ou Atualiza um Pedido de Suporte. Pode criar um Pedido de Suporte para problemas relacionados com a Gestão Técnica, de Faturação, de Quotas ou de Subscrição. Pode atualizar a gravidade, os detalhes de contacto e as comunicações para pedidos de suporte existentes. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Exclui a política de acesso. |
> | Ação | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Obter as propriedades de uma política de acesso. |
> | Ação | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Cria uma nova política de acesso para um ambiente ou atualiza uma política de acesso existente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/delete | Exclui o ambiente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Exclui a origem do evento. |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/read | Obter as propriedades de uma origem de evento. |
> | Ação | Microsoft.TimeSeriesInsights/environments/eventsources/write | Cria uma nova origem de evento para um ambiente ou atualiza uma origem de evento existente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/read | Obter as propriedades de um ambiente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Exclui o conjunto de dados de referência. |
> | Ação | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Obter as propriedades de um conjunto de dados de referência. |
> | Ação | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Cria um novo conjunto de dados de referência para um ambiente ou atualiza um conjunto de dados de referência existente. |
> | Ação | Microsoft.TimeSeriesInsights/environments/status/read | Obtenha o status do ambiente, o estado de suas operações associadas como entrada. |
> | Ação | Microsoft.TimeSeriesInsights/environments/write | Cria um novo ambiente ou atualiza um ambiente existente. |
> | Ação | Microsoft.TimeSeriesInsights/register/action | Registra a assinatura para o provedor de recursos de Time Series Insights e permite a criação de ambientes de Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.VisualStudio/Account/Delete | Eliminar Conta |
> | Ação | Microsoft.VisualStudio/Account/Extension/Read | Ler conta/extensão |
> | Ação | Microsoft.VisualStudio/Account/Project/Read | Ler conta/projeto |
> | Ação | Microsoft.VisualStudio/Account/Project/Write | Definir conta/projeto |
> | Ação | Microsoft.VisualStudio/Account/Read | Ler conta |
> | Ação | Microsoft.VisualStudio/Account/Write | Definir conta |
> | Ação | Microsoft.VisualStudio/Extension/Delete | Excluir extensão |
> | Ação | Microsoft.VisualStudio/Extension/Read | Ler extensão |
> | Ação | Microsoft.VisualStudio/Extension/Write | Definir extensão |
> | Ação | Microsoft.VisualStudio/Project/Delete | Excluir projeto |
> | Ação | Microsoft.VisualStudio/Project/Read | Ler projeto |
> | Ação | Microsoft.VisualStudio/Project/Write | Definir projeto |
> | Ação | Microsoft.VisualStudio/Register/Action | Registrar a assinatura do Azure com o provedor Microsoft. VisualStudio |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | microsoft.web/apimanagementaccounts/apiacls/read | Obter contas de gerenciamento de API Apiacls. |
> | Ação | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Excluir APIs de contas de gerenciamento de API Apiacls. |
> | Ação | microsoft.web/apimanagementaccounts/apis/apiacls/read | Obter APIs de contas de gerenciamento de API Apiacls. |
> | Ação | microsoft.web/apimanagementaccounts/apis/apiacls/write | Atualizar APIs de contas de gerenciamento de API Apiacls. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Obter APIs de contas de gerenciamento de API Connectionacls. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Confirme o código de consentimento gerenciamento de API contas de APIs conexões. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Excluir as contas de gerenciamento de API Connectionacls conexões de APIs. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Obter conexões de APIs de contas de gerenciamento de API Connectionacls. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Atualizar as contas de gerenciamento de API conexões de APIs Connectionacls. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/delete | Excluir conexões de APIs de contas de gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Obtenha links de autorização para conexões de APIs de contas de gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Listar chaves de conexão contas de gerenciamento de API conexões de APIs. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Listar segredos gerenciamento de API contas conexões de APIs. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/read | Obter conexões de APIs de contas de gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/connections/write | Atualizar conexões de APIs de contas de gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/delete | Excluir APIs de contas de gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Exclua as definições localizadas das APIs de contas de gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Obter definições localizadas de APIs de contas de gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Atualize as definições localizadas das APIs de contas de gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/read | Obter APIs de contas de gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/apis/write | Atualizar APIs de contas de gerenciamento de API. |
> | Ação | microsoft.web/apimanagementaccounts/connectionacls/read | Obter contas de gerenciamento de API Connectionacls. |
> | Ação | microsoft.web/availablestacks/read | Obter pilhas disponíveis. |
> | Ação | Microsoft.Web/certificates/Delete | Excluir um certificado existente. |
> | Ação | Microsoft.Web/certificates/Read | Obter a lista de certificados. |
> | Ação | Microsoft.Web/certificates/Write | Adicione um novo certificado ou atualize um existente. |
> | Ação | microsoft.web/checknameavailability/read | Verifique se o nome do recurso está disponível. |
> | Ação | microsoft.web/classicmobileservices/read | Obtenha serviços móveis clássicos. |
> | Ação | Microsoft.Web/connectionGateways/Delete | Exclui um gateway de conexão. |
> | Ação | Microsoft.Web/connectionGateways/Join/Action | Une um gateway de conexão. |
> | Ação | Microsoft.Web/connectionGateways/ListStatus/Action | Lista o status de um gateway de conexão. |
> | Ação | Microsoft.Web/connectionGateways/Move/Action | Move um gateway de conexão. |
> | Ação | Microsoft.Web/connectionGateways/Read | Obtenha a lista de gateways de conexão. |
> | Ação | Microsoft.Web/connectionGateways/Write | Cria ou atualiza um gateway de conexão. |
> | Ação | microsoft.web/connections/confirmconsentcode/action | Confirme o código de consentimento de conexões. |
> | Ação | Microsoft.Web/connections/Delete | Exclui uma conexão. |
> | Ação | Microsoft.Web/connections/Join/Action | Une uma conexão. |
> | Ação | microsoft.web/connections/listconsentlinks/action | Listar links de consentimento para conexões. |
> | Ação | Microsoft.Web/connections/Move/Action | Move uma conexão. |
> | Ação | Microsoft.Web/connections/Read | Obtenha a lista de conexões. |
> | Ação | Microsoft.Web/connections/Write | Cria ou atualiza uma conexão. |
> | Ação | Microsoft.Web/customApis/Delete | Exclui uma API personalizada. |
> | Ação | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extrai a definição de API de um WSDL. |
> | Ação | Microsoft.Web/customApis/Join/Action | Une uma API personalizada. |
> | Ação | Microsoft.Web/customApis/listWsdlInterfaces/Action | Lista as interfaces WSDL para uma API personalizada. |
> | Ação | Microsoft.Web/customApis/Move/Action | Move uma API personalizada. |
> | Ação | Microsoft.Web/customApis/Read | Obtenha a lista de API personalizada. |
> | Ação | Microsoft.Web/customApis/Write | Cria ou atualiza uma API personalizada. |
> | Ação | Microsoft.Web/deletedSites/Read | Obter as propriedades de um aplicativo Web excluído |
> | Ação | microsoft.web/deploymentlocations/read | Obter locais de implantação. |
> | Ação | Microsoft.Web/geoRegions/Read | Obtenha a lista de regiões geográficas. |
> | Ação | microsoft.web/hostingenvironments/capacities/read | Obter as capacidades dos ambientes de hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/Delete | Excluir um Ambiente do Serviço de Aplicativo |
> | Ação | microsoft.web/hostingenvironments/detectors/read | Obter detectores de ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/diagnostics/read | Obter diagnóstico de ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Obter os pontos de extremidade de rede de todas as dependências de entrada. |
> | Ação | Microsoft.Web/hostingEnvironments/Join/Action | Une um Ambiente do Serviço de Aplicativo |
> | Ação | microsoft.web/hostingenvironments/metricdefinitions/read | Obter definições de métrica de ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Obter definições de métrica de pools de multifunção de ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/multirolepools/metrics/read | Obter métricas de pools de multifunção de ambientes de hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Obter as propriedades de um pool de front-end em um Ambiente do Serviço de Aplicativo |
> | Ação | microsoft.web/hostingenvironments/multirolepools/skus/read | Obter SKUs de pools de multifunção de ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/multirolepools/usages/read | Obter usos de pools de multifunção de ambientes de hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Criar um novo pool de front-end em um Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Ação | microsoft.web/hostingenvironments/operations/read | Obter operações de ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Obter os pontos de extremidade de rede de todas as dependências de saída. |
> | Ação | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Aprovar conexões de ponto de extremidade privado |
> | Ação | Microsoft.Web/hostingEnvironments/Read | Obter as propriedades de um Ambiente do Serviço de Aplicativo |
> | Ação | Microsoft.Web/hostingEnvironments/reboot/Action | Reinicializar todos os computadores em um Ambiente do Serviço de Aplicativo |
> | Ação | microsoft.web/hostingenvironments/resume/action | Retome os ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/serverfarms/read | Obter planos de serviço de aplicativo dos ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/sites/read | Obter aplicativos Web de ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/suspend/action | Suspender ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/usages/read | Obter usos de ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Obter os ambientes de hospedagem definições de métrica pools. |
> | Ação | microsoft.web/hostingenvironments/workerpools/metrics/read | Obter métricas de pools de ambientes de hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/workerPools/Read | Obter as propriedades de um pool de trabalho em um Ambiente do Serviço de Aplicativo |
> | Ação | microsoft.web/hostingenvironments/workerpools/skus/read | Obter pools SKUs de ambientes de hospedagem. |
> | Ação | microsoft.web/hostingenvironments/workerpools/usages/read | Obter pools usos de ambientes de hospedagem. |
> | Ação | Microsoft.Web/hostingEnvironments/workerPools/Write | Criar um novo pool de trabalho em um Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Ação | Microsoft.Web/hostingEnvironments/Write | Criar um novo Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Ação | microsoft.web/ishostingenvironmentnameavailable/read | Obter se o nome do ambiente de hospedagem estiver disponível. |
> | Ação | microsoft.web/ishostnameavailable/read | Verifique se o nome do host está disponível. |
> | Ação | microsoft.web/isusernameavailable/read | Verifique se o nome de usuário está disponível. |
> | Ação | Microsoft.Web/listSitesAssignedToHostName/Read | Obter nomes de sites atribuídos ao nome do host. |
> | Ação | microsoft.web/locations/apioperations/read | Obter operações de API de locais. |
> | Ação | microsoft.web/locations/connectiongatewayinstallations/read | Obter instalações de gateway de conexão de locais. |
> | Ação | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Notificação de exclusão de sub-rede ou vnet para locais. |
> | Ação | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extraia a definição de API do WSDL para locais. |
> | Ação | microsoft.web/locations/listwsdlinterfaces/action | Listar interfaces WSDL para locais. |
> | Ação | microsoft.web/locations/managedapis/apioperations/read | Obter operações de API gerenciadas de locais. |
> | Ação | Microsoft.Web/locations/managedapis/Join/Action | Une uma API gerenciada. |
> | Ação | microsoft.web/locations/managedapis/read | Obter APIs gerenciadas de locais. |
> | Ação | Microsoft. Web/Locations/operationResults/Read | Obter operações. |
> | Ação | Microsoft. Web/Locations/Operations/Read | Obter operações. |
> | Ação | microsoft.web/operations/read | Obter operações. |
> | Ação | microsoft.web/publishingusers/read | Obter usuários de publicação. |
> | Ação | microsoft.web/publishingusers/write | Atualizar usuários de publicação. |
> | Ação | Microsoft.Web/recommendations/Read | Obtenha a lista de recomendações para assinaturas. |
> | Ação | microsoft.web/register/action | Registre o provedor de recursos Microsoft. Web para a assinatura. |
> | Ação | microsoft.web/resourcehealthmetadata/read | Obter Resource Health metadados. |
> | Ação | microsoft.web/serverfarms/capabilities/read | Obter recursos de planos do serviço de aplicativo. |
> | Ação | Microsoft.Web/serverfarms/Delete | Excluir um plano do serviço de aplicativo existente |
> | Ação | Microsoft. Web/serverfarms/eventGridFilters/Delete | Excluir filtro de grade de eventos no farm de servidores. |
> | Ação | Microsoft. Web/serverfarms/eventGridFilters/ler | Obter filtro de grade de eventos no farm de servidores. |
> | Ação | Microsoft. Web/serverfarms/eventGridFilters/Write | Colocar filtro de grade de eventos no farm de servidores. |
> | Ação | microsoft.web/serverfarms/firstpartyapps/settings/delete | Excluir configurações de aplicativos de primeira parte dos planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/firstpartyapps/settings/read | Obter configurações de aplicativos de primeira parte dos planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/firstpartyapps/settings/write | Atualize as configurações de aplicativos de primeira parte dos planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Excluir retransmissões de namespaces de conexão híbrida dos planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Obter retransmissões de namespaces de conexão híbrida dos planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Obter os namespaces de conexão híbrida dos planos do serviço de aplicativo retransmite aplicativos Web. |
> | Ação | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Obter limites do plano de conexão híbrida dos planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/hybridconnectionrelays/read | Obter retransmissões de conexão híbrida dos planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/metricdefinitions/read | Obter definições de métrica de planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/metrics/read | Obter métricas de planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/operationresults/read | Obter resultados da operação de planos do serviço de aplicativo. |
> | Ação | Microsoft.Web/serverfarms/Read | Obter as propriedades em um plano do serviço de aplicativo |
> | Ação | Microsoft.Web/serverfarms/restartSites/Action | Reiniciar todos os aplicativos Web em um plano do serviço de aplicativo |
> | Ação | microsoft.web/serverfarms/sites/read | Obter aplicativos Web de planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/skus/read | Obter SKUs de planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/usages/read | Obter usos de planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Atualizar gateways de conexões de rede virtual dos planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/read | Obter conexões de rede virtual dos planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Excluir rotas de conexões de rede virtual dos planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Obter rotas de conexões de rede virtual dos planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Atualizar rotas de conexões de rede virtual dos planos do serviço de aplicativo. |
> | Ação | microsoft.web/serverfarms/workers/reboot/action | Reinicialize os funcionários dos planos do serviço de aplicativo. |
> | Ação | Microsoft.Web/serverfarms/Write | Criar um novo plano do serviço de aplicativo ou atualizar um existente |
> | Ação | microsoft.web/sites/analyzecustomhostname/read | Analise o nome de host personalizado. |
> | Ação | Microsoft.Web/sites/applySlotConfig/Action | Aplicar a configuração de slot do aplicativo Web do slot de destino ao aplicativo Web atual |
> | Ação | Microsoft.Web/sites/backup/Action | Criar um novo backup de aplicativo Web |
> | Ação | microsoft.web/sites/backup/read | Obter backup de aplicativos Web. |
> | Ação | microsoft.web/sites/backup/write | Atualize o backup de aplicativos Web. |
> | Ação | microsoft.web/sites/backups/action | Descobre um backup de aplicativo existente que pode ser restaurado de um blob no armazenamento do Azure. |
> | Ação | microsoft.web/sites/backups/delete | Excluir backups de aplicativos Web. |
> | Ação | microsoft.web/sites/backups/list/action | Listar backups de aplicativos Web. |
> | Ação | Microsoft.Web/sites/backups/Read | Obter as propriedades do backup de um aplicativo Web |
> | Ação | microsoft.web/sites/backups/restore/action | Restaurar backups de aplicativos Web. |
> | Ação | microsoft.web/sites/backups/write | Atualizar backups de aplicativos Web. |
> | Ação | microsoft.web/sites/config/delete | Excluir a configuração de aplicativos Web. |
> | Ação | Microsoft.Web/sites/config/list/Action | Listar as configurações confidenciais de segurança do aplicativo Web, como credenciais de publicação, configurações de aplicativo e cadeias de conexão |
> | Ação | Microsoft.Web/sites/config/Read | Obter definições de configuração do aplicativo Web |
> | Ação | microsoft.web/sites/config/snapshots/read | Obter instantâneos de configuração de aplicativos Web. |
> | Ação | Microsoft.Web/sites/config/Write | Atualizar as definições de configuração do aplicativo Web |
> | Ação | microsoft.web/sites/containerlogs/action | Obter logs de contêiner compactados para o aplicativo Web. |
> | Ação | microsoft.web/sites/containerlogs/download/action | Baixar logs de contêiner de aplicativos Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/delete | Excluir trabalhos Web contínuos de aplicativos Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/read | Obter trabalhos Web contínuos de aplicativos Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/start/action | Inicie trabalhos Web contínuos de aplicativos Web. |
> | Ação | microsoft.web/sites/continuouswebjobs/stop/action | Parar trabalhos Web contínuos de aplicativos Web. |
> | Ação | Microsoft.Web/sites/Delete | Excluir um aplicativo Web existente |
> | Ação | microsoft.web/sites/deployments/delete | Excluir implantações de aplicativos Web. |
> | Ação | microsoft.web/sites/deployments/log/read | Obter log de implantações de aplicativos Web. |
> | Ação | microsoft.web/sites/deployments/read | Obter implantações de aplicativos Web. |
> | Ação | microsoft.web/sites/deployments/write | Atualizar implantações de aplicativos Web. |
> | Ação | microsoft.web/sites/detectors/read | Obter detectores de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/analyses/execute/Action | Executar análise de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/analyses/read | Obter análise de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/aspnetcore/read | Obtenha o diagnóstico de aplicativos Web para ASP.NET Core aplicativo. |
> | Ação | microsoft.web/sites/diagnostics/autoheal/read | Obter autoreparo de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/deployment/read | Obter implantação de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/deployments/read | Obter implantações de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/detectors/execute/Action | Executar o detector de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/detectors/read | Obter o detector de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Obter solicitações de diagnóstico de aplicativos Web com falha por URI. |
> | Ação | microsoft.web/sites/diagnostics/frebanalysis/read | Obter análise de FREB de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/loganalyzer/read | Obtenha o analisador de log de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/read | Obter categorias de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/runtimeavailability/read | Obter disponibilidade de tempo de execução de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/servicehealth/read | Obter a integridade do serviço de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Obter análise de CPU do site de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/sitecrashes/read | Obter falhas do site de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/sitelatency/read | Obter latência de site de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Obter análise de memória do site de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Obter reinicialização do site de diagnóstico de aplicativos Web configuração de atualização. |
> | Ação | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Obter reinicialização iniciada pelo usuário do site de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/siteswap/read | Obter troca de site de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/threadcount/read | Obter contagem de threads de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/workeravailability/read | Obter disponibilidade de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Obter reciclagem do processo de trabalho de diagnóstico de aplicativos Web. |
> | Ação | microsoft.web/sites/domainownershipidentifiers/read | Obter identificadores de propriedade de domínio de aplicativos Web. |
> | Ação | microsoft.web/sites/domainownershipidentifiers/write | Atualizar identificadores de propriedade de domínio de aplicativos Web. |
> | Ação | Microsoft. Web/sites/eventGridFilters/Delete | Excluir filtro de grade de eventos no aplicativo Web. |
> | Ação | Microsoft. Web/sites/eventGridFilters/ler | Obter filtro de grade de eventos no aplicativo Web. |
> | Ação | Microsoft. Web/sites/eventGridFilters/Write | Colocar filtro de grade de eventos no aplicativo Web. |
> | Ação | microsoft.web/sites/extensions/delete | Excluir extensões de site de aplicativos Web. |
> | Ação | microsoft.web/sites/extensions/read | Obter extensões de site de aplicativos Web. |
> | Ação | microsoft.web/sites/extensions/write | Atualizar extensões de site de aplicativos Web. |
> | Ação | microsoft.web/sites/functions/action | Aplicativos Web do functions. |
> | Ação | microsoft.web/sites/functions/delete | Excluir funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/funções/chaves/excluir | Excluir chaves de função. |
> | Ação | Microsoft. Web/sites/funções/chaves/gravação | Atualizar chaves de função. |
> | Ação | Microsoft. Web/sites/funções/listkeys/ação | Listar chaves de função. |
> | Ação | microsoft.web/sites/functions/listsecrets/action | Listar segredos de função. |
> | Ação | microsoft.web/sites/functions/masterkey/read | Obter funções de aplicativos Web MasterKey. |
> | Ação | Microsoft. Web/sites/funções/Propriedades/leitura | Ler propriedades de funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/funções/Propriedades/gravação | Atualizar propriedades de funções de aplicativos Web. |
> | Ação | microsoft.web/sites/functions/read | Obter funções de aplicativos Web. |
> | Ação | microsoft.web/sites/functions/token/read | Obter token de funções de aplicativos Web. |
> | Ação | microsoft.web/sites/functions/write | Atualizar funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/host/FunctionKeys/Delete | Excluir funções chaves de função de host. |
> | Ação | Microsoft. Web/sites/host/FunctionKeys/gravação | Funções de atualização hospedam chaves de função. |
> | Ação | Microsoft. Web/sites/host/listkeys/ação | Listar chaves de host do functions. |
> | Ação | Microsoft. Web/sites/host/listsyncstatus/ação | Listar status de gatilhos de função de sincronização. |
> | Ação | Microsoft. Web/sites/host/Propriedades/leitura | Ler propriedades do host de funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/host/sincronização/ação | Gatilhos de função de sincronização. |
> | Ação | Microsoft. Web/sites/host/systemkeys/Delete | Exclua as funções do sistema host. |
> | Ação | Microsoft. Web/sites/host/systemkeys/gravação | Funções de atualização hospedam chaves do sistema. |
> | Ação | microsoft.web/sites/hostnamebindings/delete | Excluir associações de nome de host de aplicativos Web. |
> | Ação | microsoft.web/sites/hostnamebindings/read | Obter associações de nome de host de aplicativos Web. |
> | Ação | microsoft.web/sites/hostnamebindings/write | Atualizar associações de nome de host de aplicativos Web. |
> | Ação | microsoft.web/sites/hostruntime/functions/keys/read | Obter chaves de funções do Hostruntime de aplicativos Web. |
> | Ação | Microsoft.Web/sites/hostruntime/host/_master/read | Obter a chave mestra de Aplicativo de funções para operações de administração |
> | Ação | Microsoft.Web/sites/hostruntime/host/action | Execute Aplicativo de funções ação de tempo de execução como gatilhos de sincronização, adicionar funções, invocar funções, excluir funções, etc. |
> | Ação | microsoft.web/sites/hostruntime/host/read | Obter host do Hostruntime de aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnection/delete | Excluir a conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnection/read | Obter conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnection/write | Atualize a conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Excluir retransmissões de namespaces de conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Listar chaves de namespaces de conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Obter retransmissões de namespaces de conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Atualizar retransmissões de namespaces de conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/hybridconnectionrelays/read | Obter retransmissões de conexão híbrida de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/deployments/delete | Excluir implantações de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/deployments/read | Obter implantações de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/extensions/log/read | Obter o log de extensões de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/extensions/processes/read | Obter processos de extensões de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/extensions/read | Obter extensões de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/processes/delete | Excluir processos de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/processes/modules/read | Obter módulos de aplicativos Web processos de instâncias. |
> | Ação | microsoft.web/sites/instances/processes/read | Obter processos de instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/instances/processes/threads/read | Obter instâncias de aplicativos Web processa threads. |
> | Ação | microsoft.web/sites/instances/read | Obter instâncias de aplicativos Web. |
> | Ação | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Listar status do gatilho de função de sincronização. |
> | Ação | microsoft.web/sites/metricdefinitions/read | Obter definições de métrica de aplicativos Web. |
> | Ação | microsoft.web/sites/metrics/read | Obter métricas de aplicativos Web. |
> | Ação | microsoft.web/sites/metricsdefinitions/read | Obter definições de métricas de aplicativos Web. |
> | Ação | microsoft.web/sites/migratemysql/action | Migrar aplicativos Web MySql. |
> | Ação | microsoft.web/sites/migratemysql/read | Obter MySql migrar aplicativos Web. |
> | Ação | microsoft.web/sites/networktrace/action | Aplicativos Web de rastreamento de rede. |
> | Ação | microsoft.web/sites/networktraces/operationresults/read | Obter resultados da operação de rastreamento de rede de aplicativos Web. |
> | Ação | microsoft.web/sites/newpassword/action | Aplicativos Web de NewPassword. |
> | Ação | microsoft.web/sites/operationresults/read | Obter resultados da operação de aplicativos Web. |
> | Ação | microsoft.web/sites/operations/read | Obter operações de aplicativos Web. |
> | Ação | microsoft.web/sites/perfcounters/read | Obter contadores de desempenho de aplicativos Web. |
> | Ação | microsoft.web/sites/premieraddons/delete | Excluir Complementos Premier de aplicativos Web. |
> | Ação | microsoft.web/sites/premieraddons/read | Obter Complementos Premier de aplicativos Web. |
> | Ação | microsoft.web/sites/premieraddons/write | Atualizar complementos Premier de aplicativos Web. |
> | Ação | microsoft.web/sites/privateaccess/read | Obtenha dados sobre a habilitação de acesso do site privado e redes virtuais autorizadas que podem acessar o site. |
> | Ação | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | Aprovar conexões de ponto de extremidade privado |
> | Ação | microsoft.web/sites/processes/modules/read | Obter módulos de processos de aplicativos Web. |
> | Ação | microsoft.web/sites/processes/read | Obter processos de aplicativos Web. |
> | Ação | microsoft.web/sites/processes/threads/read | Obter threads de processos de aplicativos Web. |
> | Ação | microsoft.web/sites/publiccertificates/delete | Excluir certificados públicos de aplicativos Web. |
> | Ação | microsoft.web/sites/publiccertificates/read | Obter certificados públicos de aplicativos Web. |
> | Ação | microsoft.web/sites/publiccertificates/write | Atualizar certificados públicos de aplicativos Web. |
> | Ação | Microsoft.Web/sites/publish/Action | Publicar um aplicativo Web |
> | Ação | Microsoft.Web/sites/publishxml/Action | Obter XML do perfil de publicação para um aplicativo Web |
> | Ação | microsoft.web/sites/publishxml/read | Obter XML de publicação de aplicativos Web. |
> | Ação | Microsoft.Web/sites/Read | Obter as propriedades de um aplicativo Web |
> | Ação | microsoft.web/sites/recommendationhistory/read | Obter o histórico de recomendação de aplicativos Web. |
> | Ação | microsoft.web/sites/recommendations/disable/action | Desabilitar recomendações de aplicativos Web. |
> | Ação | Microsoft.Web/sites/recommendations/Read | Obtenha a lista de recomendações para o aplicativo Web. |
> | Ação | microsoft.web/sites/recover/action | Recuperar aplicativos Web. |
> | Ação | Microsoft.Web/sites/resetSlotConfig/Action | Redefinir a configuração do aplicativo Web |
> | Ação | microsoft.web/sites/resourcehealthmetadata/read | Obter metadados de Resource Health de aplicativos Web. |
> | Ação | Microsoft.Web/sites/restart/Action | Reiniciar um aplicativo Web |
> | Ação | microsoft.web/sites/restore/read | Obter restauração de aplicativos Web. |
> | Ação | microsoft.web/sites/restore/write | Restaurar aplicativos Web. |
> | Ação | microsoft.web/sites/restorefrombackupblob/action | Restaure o aplicativo Web do blob de backup. |
> | Ação | Microsoft. Web/sites/restorefromdeletedapp/Action | Restaurar aplicativos Web do aplicativo excluído. |
> | Ação | microsoft.web/sites/restoresnapshot/action | Restaurar instantâneos de aplicativos Web. |
> | Ação | microsoft.web/sites/siteextensions/delete | Excluir extensões de site de aplicativos Web. |
> | Ação | microsoft.web/sites/siteextensions/read | Obter extensões de site de aplicativos Web. |
> | Ação | microsoft.web/sites/siteextensions/write | Atualizar extensões de site de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/analyzecustomhostname/read | Obter slots de aplicativos Web analisar nome de host personalizado. |
> | Ação | Microsoft.Web/sites/slots/applySlotConfig/Action | Aplique a configuração de slot do aplicativo Web do slot de destino ao slot atual. |
> | Ação | Microsoft.Web/sites/slots/backup/Action | Crie um novo backup de slot de aplicativo Web. |
> | Ação | microsoft.web/sites/slots/backup/read | Obter backup de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/backup/write | Atualizar backup de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/backups/action | Descobrir backups de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/backups/delete | Excluir backups de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/backups/list/action | Listar backups de Slots de aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/backups/Read | Obter as propriedades de um backup de Slots de um aplicativo Web |
> | Ação | microsoft.web/sites/slots/backups/restore/action | Restaurar backups de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/config/delete | Excluir configuração de Slots de aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/config/list/Action | Listar as configurações confidenciais de segurança do slot do aplicativo Web, como credenciais de publicação, configurações do aplicativo e cadeias de conexão |
> | Ação | Microsoft.Web/sites/slots/config/Read | Obter definições de configuração do slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/config/Write | Atualizar definições de configuração do slot do aplicativo Web |
> | Ação | microsoft.web/sites/slots/containerlogs/action | Obter logs de contêiner compactados para o slot do aplicativo Web. |
> | Ação | microsoft.web/sites/slots/containerlogs/download/action | Baixar logs de contêiner de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/delete | Excluir trabalhos Web contínuos de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/read | Obter trabalhos Web contínuos de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/start/action | Iniciar trabalhos Web contínuos de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/continuouswebjobs/stop/action | Parar trabalhos Web contínuos de Slots de aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/Delete | Excluir um slot de aplicativo Web existente |
> | Ação | microsoft.web/sites/slots/deployments/delete | Excluir implantações de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/deployments/log/read | Obter o log de implantações de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/deployments/read | Obter implantações de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/deployments/write | Atualizar implantações de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/detectors/read | Obter detectores de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Executar análise de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/analyses/read | Obter análise de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Obter diagnóstico de Slots de aplicativos Web para ASP.NET Core aplicativo. |
> | Ação | microsoft.web/sites/slots/diagnostics/autoheal/read | Obter o diagnóstico de Slots de aplicativos Web para a rereparo. |
> | Ação | microsoft.web/sites/slots/diagnostics/deployment/read | Obter implantação de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/deployments/read | Obter implantações de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Executar o detector de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/detectors/read | Obter o detector de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Obter análise de FREB de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Obter analisador de log de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/read | Obter diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Obter disponibilidade de tempo de execução de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/servicehealth/read | Obter integridade do serviço de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Obter análise de CPU do site de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Obter falhas do site de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitelatency/read | Obter latência do site de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Obter análise de memória do site de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Obter diagnóstico de Slots de aplicativos Web configurações de reinicialização do site atualizar. |
> | Ação | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Obter diagnóstico de Slots de aplicativos Web reiniciar o usuário iniciado. |
> | Ação | microsoft.web/sites/slots/diagnostics/siteswap/read | Obter troca de site de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/threadcount/read | Obter contagem de threads de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/workeravailability/read | Obter disponibilidade de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Obter reciclagem de processo do trabalho de diagnóstico de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/domainownershipidentifiers/read | Obter identificadores de propriedade de domínio de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/functions/listsecrets/action | Listar segredos funções de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/functions/read | Obter funções de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hostnamebindings/delete | Excluir associações de nome de host de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hostnamebindings/read | Obter associações de nome de host de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hostnamebindings/write | Atualizar associações de nome de host de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnection/delete | Excluir conexão híbrida de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnection/read | Obter conexão híbrida de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnection/write | Atualizar a conexão híbrida de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Excluir retransmissões de namespaces de conexão híbrida de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Atualizar retransmissões de namespaces de conexão híbrida de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/hybridconnectionrelays/read | Obter retransmissões de conexão híbrida de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/instances/deployments/read | Obter implantações de instâncias de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/instances/processes/delete | Excluir processos de instâncias de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/instances/processes/read | Obter processos de instâncias de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/instances/read | Obter instâncias de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/metricdefinitions/read | Obter definições de métrica de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/metrics/read | Obter métricas de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/migratemysql/read | Obter o MySql migrar slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/networktrace/action | Slots de aplicativos Web de rastreamento de rede. |
> | Ação | microsoft.web/sites/slots/networktraces/operationresults/read | Obter resultados de operação de rastreamento de rede de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/newpassword/action | Slots de aplicativos Web de NewPassword. |
> | Ação | microsoft.web/sites/slots/operationresults/read | Obter resultados da operação de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/operations/read | Obter operações de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/perfcounters/read | Obter contadores de desempenho de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/phplogging/read | Obter Phplogging de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/premieraddons/delete | Excluir Complementos Premier de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/premieraddons/read | Obter Complementos Premier de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/premieraddons/write | Atualizar complementos Premier de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/processes/read | Obter processos de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/publiccertificates/delete | Excluir certificados públicos de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/publiccertificates/read | Obter certificados públicos de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/publiccertificates/write | Criar ou atualizar certificados públicos de Slots de aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/publish/Action | Publicar um slot de aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/publishxml/Action | Obter XML do perfil de publicação para o slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/Read | Obter as propriedades de um slot de implantação de aplicativo Web |
> | Ação | microsoft.web/sites/slots/recover/action | Recuperar slots de aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/resetSlotConfig/Action | Redefinir a configuração do slot do aplicativo Web |
> | Ação | microsoft.web/sites/slots/resourcehealthmetadata/read | Obter slots de aplicativos Web Resource Health metadados. |
> | Ação | Microsoft.Web/sites/slots/restart/Action | Reiniciar um slot de aplicativo Web |
> | Ação | microsoft.web/sites/slots/restore/read | Obter restauração de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/restore/write | Restaurar slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/restorefrombackupblob/action | Restaure o slot de aplicativos Web do blob de backup. |
> | Ação | Microsoft. Web/sites/Slots/restorefromdeletedapp/Action | Restaure os slots do aplicativo Web do aplicativo excluído. |
> | Ação | microsoft.web/sites/slots/restoresnapshot/action | Restaurar instantâneos de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/siteextensions/delete | Excluir extensões de site de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/siteextensions/read | Obter extensões de site de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/siteextensions/write | Atualizar extensões de site de Slots de aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/slotsdiffs/Action | Obter diferenças na configuração entre o aplicativo Web e os slots |
> | Ação | Microsoft.Web/sites/slots/slotsswap/Action | Trocar slots de implantação de aplicativo Web |
> | Ação | microsoft.web/sites/slots/snapshots/read | Obter instantâneos de Slots de aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/sourcecontrols/Delete | Excluir definições de configuração de controle do código-fonte do slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/sourcecontrols/Read | Obter definições de configuração de controle do código-fonte do slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/sourcecontrols/Write | Atualizar definições de configuração de controle do código-fonte do slot do aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/start/Action | Iniciar um slot de aplicativo Web |
> | Ação | Microsoft.Web/sites/slots/stop/Action | Parar um slot de aplicativo Web |
> | Ação | microsoft.web/sites/slots/sync/action | Sincronizar slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/triggeredwebjobs/delete | Excluir os slots de aplicativos Web disparados. |
> | Ação | microsoft.web/sites/slots/triggeredwebjobs/read | Obter webjobs disparados de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/triggeredwebjobs/run/action | Executar webjobs disparados de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/usages/read | Obter usos de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/delete | Excluir conexões de rede virtual de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Atualizar gateways de conexões de rede virtual de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/read | Obter conexões de rede virtual de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/virtualnetworkconnections/write | Atualizar conexões de rede virtual de Slots de aplicativos Web. |
> | Ação | microsoft.web/sites/slots/webjobs/read | Obter webjobs de Slots de aplicativos Web. |
> | Ação | Microsoft.Web/sites/slots/Write | Criar um novo slot de aplicativo Web ou atualizar um existente |
> | Ação | Microsoft.Web/sites/slotsdiffs/Action | Obter diferenças na configuração entre o aplicativo Web e os slots |
> | Ação | Microsoft.Web/sites/slotsswap/Action | Trocar slots de implantação de aplicativo Web |
> | Ação | microsoft.web/sites/snapshots/read | Obter instantâneos de aplicativos Web. |
> | Ação | Microsoft.Web/sites/sourcecontrols/Delete | Excluir definições de configuração de controle do código-fonte do aplicativo Web |
> | Ação | Microsoft.Web/sites/sourcecontrols/Read | Obter definições de configuração de controle do código-fonte do aplicativo Web |
> | Ação | Microsoft.Web/sites/sourcecontrols/Write | Atualizar as definições de configuração de controle do código-fonte do aplicativo Web |
> | Ação | Microsoft.Web/sites/start/Action | Iniciar um aplicativo Web |
> | Ação | Microsoft.Web/sites/stop/Action | Parar um aplicativo Web |
> | Ação | microsoft.web/sites/sync/action | Sincronizar aplicativos Web. |
> | Ação | microsoft.web/sites/syncfunctiontriggers/action | Gatilhos de função de sincronização. |
> | Ação | microsoft.web/sites/triggeredwebjobs/delete | Excluir webjobs disparados de aplicativos Web. |
> | Ação | microsoft.web/sites/triggeredwebjobs/history/read | Obter o histórico de trabalhos Web disparados por aplicativos. |
> | Ação | microsoft.web/sites/triggeredwebjobs/read | Obter webjobs disparados por aplicativos Web. |
> | Ação | microsoft.web/sites/triggeredwebjobs/run/action | Executar webjobs disparados de aplicativos Web. |
> | Ação | microsoft.web/sites/usages/read | Obter usos de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/delete | Excluir conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/gateways/read | Obter gateways de conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/gateways/write | Atualizar gateways de conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/read | Obter conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/virtualnetworkconnections/write | Atualizar conexões de rede virtual de aplicativos Web. |
> | Ação | microsoft.web/sites/webjobs/read | Obter webjobs de aplicativos Web. |
> | Ação | Microsoft.Web/sites/Write | Criar um novo aplicativo Web ou atualizar um existente |
> | Ação | microsoft.web/skus/read | Obter SKUs. |
> | Ação | microsoft.web/sourcecontrols/read | Obter controles de origem. |
> | Ação | microsoft.web/sourcecontrols/write | Atualizar controles de origem. |
> | Ação | microsoft.web/unregister/action | Cancele o registro do provedor de recursos Microsoft. Web para a assinatura. |
> | Ação | microsoft.web/validate/action | Verifica. |
> | Ação | microsoft.web/verifyhostingenvironmentvnet/action | Verifique vnet do ambiente de hospedagem. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.WorkloadMonitor/components/read | Obtém os componentes para o recurso |
> | Ação | Microsoft.WorkloadMonitor/componentsSummary/read | Obtém o resumo dos componentes |
> | Ação | Microsoft.WorkloadMonitor/monitorInstances/read | Obtém instâncias de monitores para o recurso |
> | Ação | Microsoft. WorkloadMonitor/monitorInstancesSummary/Read | Obtém o resumo das instâncias do monitor |
> | Ação | Microsoft. WorkloadMonitor/monitores/leitura | Obtém monitores para o recurso |
> | Ação | Microsoft. WorkloadMonitor/monitores/gravação | Configurar o monitor para o recurso |
> | Ação | Microsoft.WorkloadMonitor/notificationSettings/read | Obtém as configurações de notificação para o recurso |
> | Ação | Microsoft. WorkloadMonitor/notificationSettings/Write | Definir configurações de notificação para o recurso |
> | Ação | Microsoft.WorkloadMonitor/operations/read | Obter as operações com suporte |

## <a name="next-steps"></a>Passos seguintes

- [Corresponder provedor de recursos ao serviço](../azure-resource-manager/azure-services-resource-providers.md)
- [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Built-in roles for Azure resources](built-in-roles.md) (Funções incorporadas para recursos do Azure)
