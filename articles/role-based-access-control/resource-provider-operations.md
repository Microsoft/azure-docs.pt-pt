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
ms.date: 09/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 092c3b4ac6ce163e9fcf8aaad9e74f398559e9e2
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546327"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operações do provedor de recursos Azure Resource Manager

Este artigo lista as operações disponíveis para cada provedor de recursos de Azure Resource Manager. Essas operações podem ser usadas em [funções personalizadas](custom-roles.md) para fornecer [RBAC (controle de acesso baseado em função)](overview.md) granular para recursos no Azure. As cadeias de caracteres de operação têm o seguinte formato: `{Company}.{ProviderName}/{resourceType}/{action}`. Para obter uma lista de como os namespaces do provedor de recursos são mapeados para os serviços do Azure, consulte [corresponder provedor de recursos ao serviço](../azure-resource-manager/azure-services-resource-providers.md).

As operações do provedor de recursos estão sempre em evolução. Para obter as operações mais recentes, use a lista de operações [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) ou [AZ Provider](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. AAD/nomedodomínio/Delete | Excluir serviço de domínio |
> | Ação | Microsoft. AAD/nomedodomínio/oucontainer/Delete | Excluir contêiner de UO |
> | Ação | Microsoft. AAD/DomainServices/oucontainer/Read | Ler contêineres de UO |
> | Ação | Microsoft. AAD/DomainServices/oucontainer/Write | Gravar contêiner de UO |
> | Ação | Microsoft. AAD/DomainServices/leitura | Ler serviços de domínio |
> | Ação | Microsoft. AAD/nomedodomínio/replicaSets/Delete | Excluir site de cluster |
> | Ação | Microsoft. AAD/DomainServices/replicaSets/Read | Ler site do cluster |
> | Ação | Microsoft. AAD/DomainServices/replicaSets/Write | Gravar site de cluster |
> | Ação | Microsoft. AAD/DomainServices/Write | Serviço de domínio de gravação |
> | Ação | Microsoft. AAD/Locations/operationresults/Read |  |
> | Ação | Microsoft. AAD/Operations/Read |  |
> | Ação | Microsoft. AAD/registro/ação | Registrar serviço de domínio |
> | Ação | Microsoft. AAD/cancelar registro/ação | Cancelar o registro do serviço de domínio |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. aadiam/DiagnosticSettings/Delete | Excluindo uma configuração de diagnóstico |
> | Ação | Microsoft. aadiam/DiagnosticSettings/Read | Lendo uma configuração de diagnóstico |
> | Ação | Microsoft. aadiam/DiagnosticSettings/Write | Escrevendo uma configuração de diagnóstico |
> | Ação | Microsoft. aadiam/diagnosticsettingscategories/Read | Lendo categorias de configuração de diagnóstico |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. addons/Operations/Read | Obtém as operações RP com suporte. |
> | Ação | Microsoft. Complementos/registro/ação | Registrar a assinatura especificada com Microsoft. Complementos |
> | Ação | Microsoft. Complementos/supportProviders/listsupportplaninfo/Action | Lista informações do plano de suporte atual para a assinatura especificada. |
> | Ação | Microsoft. Complementos/supportProviders/supportPlanTypes/Delete | Remove o plano de suporte canônico especificado |
> | Ação | Microsoft. addons/supportProviders/supportPlanTypes/Read | Obter o estado do plano de suporte canônico especificado. |
> | Ação | Microsoft. addons/supportProviders/supportPlanTypes/Write | Adiciona o tipo de plano de suporte canônico especificado. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ADHybridHealthService/ADDS/ação | Crie uma nova floresta para o locatário. |
> | Ação | Microsoft. ADHybridHealthService/addsservices/addomainservicemembers/Read | Obtém todos os servidores para o nome de serviço especificado. |
> | Ação | Microsoft. ADHybridHealthService/ADDS/alertas/leitura | Obtém detalhes de alertas para a floresta, como Alertid, data de geração do alerta, último alerta detectado, descrição do alerta, última atualização, nível de alerta, estado do alerta, links de solução de problemas de alerta, etc. |
> | Ação | Microsoft. ADHybridHealthService/ADDS/configuração/leitura | Obtém a configuração de serviço para a floresta. Exemplo-nome da floresta, nível funcional, função FSMO do mestre de nomeação de domínio, função FSMO do mestre de esquema, etc. |
> | Ação | Microsoft. ADHybridHealthService/ADDS/excluir | Exclui um serviço e seus servidores juntamente com os dados de integridade. |
> | Ação | Microsoft. ADHybridHealthService/ADDS/dimensões/leitura | Obtém os detalhes de domínios e sites para a floresta. Exemplo-status de integridade, alertas ativos, alertas resolvidos, propriedades como nível funcional do domínio, floresta, mestre de infraestrutura, PDC, mestre de RID etc.  |
> | Ação | Microsoft. ADHybridHealthService/addsservices/recursos/userpreferência/leitura | Obtém a configuração de preferência do usuário para a floresta.<br>Exemplo-MetricCounterName como ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Configurações para os gráficos de interface do usuário, etc. |
> | Ação | Microsoft. ADHybridHealthService/addsservices/forestsummary/Read | Obtém o resumo da floresta para a floresta determinada, como o nome da floresta, o número de domínios nesta floresta, o número de sites e os detalhes de sites, etc. |
> | Ação | Microsoft. ADHybridHealthService/addsservices/metricmetadata/Read | Obtém a lista de métricas com suporte para um determinado serviço.<br>Por exemplo, bloqueios de conta de extranet, total de solicitações com falha, solicitações de token pendentes (proxy), solicitações de token/s etc para o serviço ADFS.<br>Autenticações NTLM/s, associações com êxito de LDAP/s, tempo de ligação LDAP, Threads ativos LDAP, autenticações Kerberos/s, total de threads do ATQ etc. para ADDomainService.<br>Execute a latência do perfil, conexões TCP estabelecidas, bytes privados do agente do insights, exportar estatísticas para o Azure AD para o serviço ADSync. |
> | Ação | Microsoft. ADHybridHealthService/ADDS/métricas/grupos/leitura | Dado um serviço, essa API Obtém as informações de métricas.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas ao: bloqueios de conta de extranet, total de solicitações com falha, solicitações de token pendentes (proxy), solicitações de token/s, etc. para o serviço ADFederation.<br>Autenticações NTLM/s, associações com êxito de LDAP/s, tempo de ligação LDAP, Threads ativos LDAP, autenticações Kerberos/s, total de threads do ATQ, etc para o serviço ADDomain.<br>Execute a latência do perfil, conexões TCP estabelecidas, bytes privados do agente do insights, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft. ADHybridHealthService/addsservices/premiumcheck/Read | Essa API Obtém a lista de todos os ADDomainServices integrados para um locatário Premium. |
> | Ação | Microsoft. ADHybridHealthService/ADDS/leitura | Obtém detalhes do serviço para o nome do serviço especificado. |
> | Ação | Microsoft. ADHybridHealthService/addsservices/replicationdetails/Read | Obtém detalhes de replicação para todos os servidores para o nome de serviço especificado. |
> | Ação | Microsoft. ADHybridHealthService/addsservices/ReplicationStatus/Read | Obtém o número de controladores de domínio e seus erros de replicação, se houver. |
> | Ação | Microsoft. ADHybridHealthService/addsservices/replicationsummary/Read | Obtém a lista de controladores de domínio completos junto com os detalhes de replicação para a floresta especificada. |
> | Ação | Microsoft. ADHybridHealthService/addsservices/permembers/Action | Adicione uma instância de servidor ao serviço. |
> | Ação | Microsoft. ADHybridHealthService/addsservices/objectmembers/Credentials/Read | Durante o registro do servidor de ADDomainService, essa API é chamada para obter as credenciais para a integração de novos servidores. |
> | Ação | Microsoft. ADHybridHealthService/addsservices/usermembers/Delete | Exclui um servidor para um determinado serviço e locatário. |
> | Ação | Microsoft. ADHybridHealthService/ADDS/gravação | Cria ou atualiza a instância de ADDomainService para o locatário. |
> | Ação | Microsoft. ADHybridHealthService/configuração/ação | Atualiza a configuração do locatário. |
> | Ação | Microsoft. ADHybridHealthService/configuração/leitura | Lê a configuração do locatário. |
> | Ação | Microsoft. ADHybridHealthService/configuração/gravação | Cria uma configuração de locatário. |
> | Ação | Microsoft. ADHybridHealthService/logs/conteúdo/leitura | Obtém o conteúdo dos logs de instalação e registro do agente armazenados no BLOB. |
> | Ação | Microsoft. ADHybridHealthService/logs/ler | Obtém os logs de instalação e registro do agente para o locatário. |
> | Ação | Microsoft. ADHybridHealthService/operações/leitura | Obtém a lista de operações com suporte pelo sistema. |
> | Ação | Microsoft. ADHybridHealthService/registro/ação | Registra o provedor de recursos de Serviço de Integridade do ADHybrid e habilita a criação do recurso de Serviço de Integridade do ADHybrid. |
> | Ação | Microsoft. ADHybridHealthService/Reports/availabledeployments/Read | Obtém a lista de regiões disponíveis, usada pelo DevOps para dar suporte a incidentes de clientes. |
> | Ação | Microsoft. ADHybridHealthService/Reports/badpassword/Read | Obtém a lista de tentativas de senha inválidos para todos os usuários no Active Directory Serviço de Federação. |
> | Ação | Microsoft. ADHybridHealthService/Reports/badpassworduseridipfrequency/Read | Obtém o URI de SAS de BLOB que contém o status e o resultado eventual do trabalho de relatório recentemente enfileirado para frequência de tentativas de nome de usuário/senha inválidos por UserId por IPAddress por dia para um determinado locatário. |
> | Ação | Microsoft. ADHybridHealthService/Reports/consentedtodevopstenants/Read | Obtém a lista de locatários consentidos DevOps. Normalmente usado para atendimento ao cliente. |
> | Ação | Microsoft. ADHybridHealthService/Reports/isdevops/Read | Obtém um valor que indica se o locatário é DevOps consentido ou não. |
> | Ação | Microsoft. ADHybridHealthService/Reports/selectdevopstenant/Read | Atualiza UserID (ObjectID) para o locatário de ops de desenvolvimento selecionado. |
> | Ação | Microsoft. ADHybridHealthService/Reports/selecteddeployment/Read | Obtém a implantação selecionada para o locatário fornecido. |
> | Ação | Microsoft. ADHybridHealthService/Reports/tenantassigneddeployment/Read | Uma ID de locatário fornecida Obtém o local de armazenamento do locatário. |
> | Ação | Microsoft. ADHybridHealthService/Reports/updateselecteddeployment/Read | Obtém o local geográfico do qual os dados serão acessados. |
> | Ação | Microsoft. ADHybridHealthService/serviços/ação | Atualiza uma instância de serviço no locatário. |
> | Ação | Microsoft. ADHybridHealthService/serviços/alertas/ler | Lê os alertas de um serviço. |
> | Ação | Microsoft. ADHybridHealthService/serviços/alertas/ler | Lê os alertas de um serviço. |
> | Ação | Microsoft. ADHybridHealthService/Services/checkservicefeatureavailibility/Read | Dado um nome de recurso verifica se um serviço tem tudo o que é necessário para usar esse recurso. |
> | Ação | Microsoft. ADHybridHealthService/serviços/excluir | Exclui uma instância de serviço no locatário. |
> | Ação | Microsoft. ADHybridHealthService/Services/exporterrors/Read | Obtém os erros de exportação para um determinado serviço de sincronização. |
> | Ação | Microsoft. ADHybridHealthService/Services/exportstatus/Read | Obtém o status de exportação de um determinado serviço. |
> | Ação | Microsoft. ADHybridHealthService/Services/feedbacktype/feedback/leitura | Obtém comentários de alertas para um determinado serviço e servidor. |
> | Ação | Microsoft. ADHybridHealthService/Services/metricmetadata/Read | Obtém a lista de métricas com suporte para um determinado serviço.<br>Por exemplo, bloqueios de conta de extranet, total de solicitações com falha, solicitações de token pendentes (proxy), solicitações de token/s etc para o serviço ADFS.<br>Autenticações NTLM/s, associações com êxito de LDAP/s, tempo de ligação LDAP, Threads ativos LDAP, autenticações Kerberos/s, total de threads do ATQ etc. para ADDomainService.<br>Execute a latência do perfil, conexões TCP estabelecidas, bytes privados do agente do insights, exportar estatísticas para o Azure AD para o serviço ADSync. |
> | Ação | Microsoft. ADHybridHealthService/serviços/métricas/grupos/média/leitura | Dado um serviço, essa API Obtém a média de métricas para um determinado serviço.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas ao: bloqueios de conta de extranet, total de solicitações com falha, solicitações de token pendentes (proxy), solicitações de token/s, etc. para o serviço ADFederation.<br>Autenticações NTLM/s, associações com êxito de LDAP/s, tempo de ligação LDAP, Threads ativos LDAP, autenticações Kerberos/s, total de threads do ATQ, etc para o serviço ADDomain.<br>Execute a latência do perfil, conexões TCP estabelecidas, bytes privados do agente do insights, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft. ADHybridHealthService/serviços/métricas/grupos/leitura | Dado um serviço, essa API Obtém as informações de métricas.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas ao: bloqueios de conta de extranet, total de solicitações com falha, solicitações de token pendentes (proxy), solicitações de token/s, etc. para o serviço ADFederation.<br>Autenticações NTLM/s, associações com êxito de LDAP/s, tempo de ligação LDAP, Threads ativos LDAP, autenticações Kerberos/s, total de threads do ATQ, etc para o serviço ADDomain.<br>Execute a latência do perfil, conexões TCP estabelecidas, bytes privados do agente do insights, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft. ADHybridHealthService/serviços/métricas/grupos/soma/leitura | Dado um serviço, essa API Obtém a exibição agregada para métricas para um determinado serviço.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas ao: bloqueios de conta de extranet, total de solicitações com falha, solicitações de token pendentes (proxy), solicitações de token/s, etc. para o serviço ADFederation.<br>Autenticações NTLM/s, associações com êxito de LDAP/s, tempo de ligação LDAP, Threads ativos LDAP, autenticações Kerberos/s, total de threads do ATQ, etc para o serviço ADDomain.<br>Execute a latência do perfil, conexões TCP estabelecidas, bytes privados do agente do insights, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft. ADHybridHealthService/Services/monitoringconfiguration/Write | Adicionar ou atualizar a configuração de monitoramento de um serviço. |
> | Ação | Microsoft. ADHybridHealthService/Services/monitoringconfigurations/Read | Obtém as configurações de monitoramento para um determinado serviço. |
> | Ação | Microsoft. ADHybridHealthService/Services/monitoringconfigurations/Write | Adicionar ou atualizar configurações de monitoramento para um serviço. |
> | Ação | Microsoft. ADHybridHealthService/Services/premiumcheck/Read | Essa API Obtém a lista de todos os serviços integrados para um locatário Premium. |
> | Ação | Microsoft. ADHybridHealthService/serviços/ler | Lê as instâncias de serviço no locatário. |
> | Ação | Microsoft. ADHybridHealthService/Services/Reports/blobUris/Read | Obtém todos os URIs de relatório IP arriscados nos últimos 7 dias. |
> | Ação | Microsoft. ADHybridHealthService/serviços/relatórios/detalhes/ler | Obtém o relatório dos principais usuários 50 com erros de senha inválidos nos últimos 7 dias |
> | Ação | Microsoft. ADHybridHealthService/Services/Reports/generateBlobUri/Action | Gera um relatório IP arriscado e retorna um URI apontando para ele. |
> | Ação | Microsoft. ADHybridHealthService/Services/webmembers/Action | Cria uma instância de servidor no serviço. |
> | Ação | Microsoft. ADHybridHealthService/Services/webmembers/alertas/leitura | Lê os alertas de um servidor. |
> | Ação | Microsoft. ADHybridHealthService/Services/webmembers/Credentials/Read | Durante o registro do servidor, essa API é chamada para obter as credenciais para a integração de novos servidores. |
> | Ação | Microsoft. ADHybridHealthService/Services/inmembers/datafreshity/Read | Para um determinado servidor, essa API Obtém uma lista de tipos de dados que estão sendo carregados pelos servidores e a hora mais recente para cada carregamento. |
> | Ação | Microsoft. ADHybridHealthService/Services/inmembers/Delete | Exclui uma instância de servidor no serviço. |
> | Ação | Microsoft. ADHybridHealthService/Services/webmembers/exportstatus/Read | Obtém os detalhes do erro de exportação de sincronização para um determinado serviço de sincronização. |
> | Ação | Microsoft. ADHybridHealthService/Services/serviços/métricas/grupos/leitura | Dado um serviço, essa API Obtém as informações de métricas.<br>Por exemplo, essa API pode ser usada para obter informações relacionadas ao: bloqueios de conta de extranet, total de solicitações com falha, solicitações de token pendentes (proxy), solicitações de token/s, etc. para o serviço ADFederation.<br>Autenticações NTLM/s, associações com êxito de LDAP/s, tempo de ligação LDAP, Threads ativos LDAP, autenticações Kerberos/s, total de threads do ATQ, etc para o serviço ADDomain.<br>Execute a latência do perfil, conexões TCP estabelecidas, bytes privados do agente do insights, exportar estatísticas para o Azure AD para o serviço de sincronização. |
> | Ação | Microsoft. ADHybridHealthService/Services/inmembers/métricas/leitura | Obtém a lista de conectores e executa nomes de perfil para o serviço e o membro de serviço fornecidos. |
> | Ação | Microsoft. ADHybridHealthService/Services/webmembers/Read | Lê a instância de servidor no serviço. |
> | Ação | Microsoft. ADHybridHealthService/Services/webmembers/serviço/leitura | Obtém a configuração de serviço para um determinado locatário. |
> | Ação | Microsoft. ADHybridHealthService/Services/tenantwhitelisting/Read | Obtém o status da lista de permissões de recursos para um determinado locatário. |
> | Ação | Microsoft. ADHybridHealthService/Services/Write | Cria uma instância de serviço no locatário. |
> | Ação | Microsoft. ADHybridHealthService/cancelar registro/ação | Cancela o registro da assinatura para ADHybrid Serviço de Integridade provedor de recursos. |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Advisor/Configurations/ler | Obter configurações |
> | Ação | Microsoft. Advisor/configurações/gravação | Cria/atualiza a configuração |
> | Ação | Microsoft. Advisor/generateRecommendations/Action | Gera recomendações |
> | Ação | Microsoft. Advisor/generateRecommendations/Read | Obtém o status de recomendações de geração |
> | Ação | Microsoft. Advisor/Metadata/Read | Obter metadados |
> | Ação | Microsoft. Advisor/Operations/Read | Obter as operações para o Microsoft Advisor |
> | Ação | Microsoft. Advisor/Recommendations/disponível/ação | A nova recomendação está disponível no Microsoft Advisor |
> | Ação | Microsoft. Advisor/Recommendations/Read | Leituras de recomendações |
> | Ação | Microsoft. Advisor/Recommendations/supressões/Delete | Exclui a supressão |
> | Ação | Microsoft. Advisor/Recommendations/supressões/Read | Obter supressões |
> | Ação | Microsoft. Advisor/Recommendations/supressões/Write | Cria/atualiza supressões |
> | Ação | Microsoft. Advisor/registro/ação | Registra a assinatura do Microsoft Advisor |
> | Ação | Microsoft. Advisor/supressões/Delete | Exclui a supressão |
> | Ação | Microsoft. Advisor/supressões/leitura | Obter supressões |
> | Ação | Microsoft. Advisor/supressões/Write | Cria/atualiza supressões |
> | Ação | Microsoft. Advisor/cancelar registro/ação | Cancela o registro da assinatura do Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. AlertsManagement/actionRules/Delete | Excluir regra de ação em uma determinada assinatura. |
> | Ação | Microsoft. AlertsManagement/actionRules/Read | Obtenha todas as regras de ação para os filtros de entrada. |
> | Ação | Microsoft. AlertsManagement/actionRules/Write | Criar ou atualizar regra de ação em uma determinada assinatura |
> | Ação | Microsoft. AlertsManagement/Alerts/alterstate/Action | Altere o estado do alerta. |
> | Ação | Microsoft. AlertsManagement/alertas/diagnóstico/leitura | Obter todos os diagnósticos para o alerta |
> | Ação | Microsoft. AlertsManagement/alertas/histórico/leitura | Obter o histórico do alerta |
> | Ação | Microsoft. AlertsManagement/alertas/ler | Obtenha todos os alertas para os filtros de entrada. |
> | Ação | Microsoft. AlertsManagement/alertlist/ler | Obter todos os alertas para os filtros de entrada nas assinaturas |
> | Ação | Microsoft. AlertsManagement/alertsMetaData/Read | Obter metadados de alertas para o parâmetro de entrada. |
> | Ação | Microsoft. AlertsManagement/alertsSummary/Read | Obter o resumo dos alertas |
> | Ação | Microsoft. AlertsManagement/alertsSummaryList/Read | Obter o resumo de alertas entre assinaturas |
> | Ação | Microsoft. AlertsManagement/operações/leitura | Lê as operações fornecidas |
> | Ação | Microsoft. AlertsManagement/registro/ação | Registra a assinatura para o gerenciamento de alertas da Microsoft |
> | Ação | Microsoft. AlertsManagement/smartDetectorAlertRules/Delete | Excluir regra de alerta do detector inteligente em uma determinada assinatura |
> | Ação | Microsoft. AlertsManagement/smartDetectorAlertRules/Read | Obter todas as regras de alerta do detector inteligente para os filtros de entrada |
> | Ação | Microsoft. AlertsManagement/smartDetectorAlertRules/Write | Criar ou atualizar a regra de alerta do detector inteligente em uma determinada assinatura |
> | Ação | Microsoft. AlertsManagement/smartGroups/ChangeState/ação | Alterar o estado do grupo inteligente |
> | Ação | Microsoft. AlertsManagement/smartGroups/History/Read | Obter o histórico do grupo inteligente |
> | Ação | Microsoft. AlertsManagement/smartGroups/Read | Obter todos os grupos inteligentes para os filtros de entrada |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. AnalysisServices/Locations/checkNameAvailability/Action | Verifica se o nome de Analysis Server fornecido é válido e não está em uso. |
> | Ação | Microsoft. AnalysisServices/Locations/operationresults/Read | Recupera as informações do resultado da operação especificada. |
> | Ação | Microsoft. AnalysisServices/Locations/operationstatuses/Read | Recupera as informações do status de operação especificado. |
> | Ação | Microsoft. AnalysisServices/Operations/Read | Recupera as informações de operações |
> | Ação | Microsoft. AnalysisServices/registro/ação | Registra Analysis Services provedor de recursos. |
> | Ação | Microsoft. AnalysisServices/Servers/Delete | Exclui o Analysis Server. |
> | Ação | Microsoft. AnalysisServices/Servers/listGatewayStatus/Action | Liste o status do gateway associado ao servidor. |
> | Ação | Microsoft. AnalysisServices/Servers/Read | Recupera as informações do Analysis Server especificado. |
> | Ação | Microsoft. AnalysisServices/Servers/retomar/ação | Retoma o Analysis Server. |
> | Ação | Microsoft. AnalysisServices/Servers/SKUs/Read | Recuperar informações de SKU disponíveis para o servidor |
> | Ação | Microsoft. AnalysisServices/Servers/suspender/ação | Suspende o Analysis Server. |
> | Ação | Microsoft. AnalysisServices/Servers/Write | Cria ou atualiza o Analysis Server especificado. |
> | Ação | Microsoft. AnalysisServices/SKUs/Read | Recupera as informações de SKUs |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ApiManagement/checkNameAvailability/Read | Verifica se o nome do serviço fornecido está disponível |
> | Ação | Microsoft. ApiManagement/operações/leitura | Ler todas as operações de API disponíveis para o recurso Microsoft. ApiManagement |
> | Ação | Microsoft. ApiManagement/registro/ação | Registrar a assinatura para o provedor de recursos Microsoft. ApiManagement |
> | Ação | Microsoft. ApiManagement/Reports/Read | Obtenha relatórios agregados por períodos de tempo, região geográfica, desenvolvedores, produtos, APIs, operações, assinatura e byRequest. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Delete | Exclui a API especificada da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Diagnostics/Delete | Exclui o diagnóstico especificado de uma API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Diagnostics/Read | Lista todos os diagnósticos de uma API. ou obtém os detalhes do diagnóstico para uma API especificada por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Diagnostics/Write | Cria um novo diagnóstico para uma API ou atualiza um existente. ou atualiza os detalhes do diagnóstico para uma API especificada por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/APIs/issues/Attachments/Delete | Exclui o comentário especificado de um problema. |
> | Ação | Microsoft. ApiManagement/Service/APIs/issues/Attachments/Read | Lista todos os anexos para o problema associado à API especificada. ou obtém os detalhes do anexo do problema para uma API especificada por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/APIs/issues/Attachments/Write | Cria um novo anexo para o problema em uma API ou atualiza um existente. |
> | Ação | Microsoft. ApiManagement/Service/APIs/issues/Comments/Delete | Exclui o comentário especificado de um problema. |
> | Ação | Microsoft. ApiManagement/Service/APIs/issues/Comments/Read | Lista todos os comentários para o problema associado à API especificada. ou obtém os detalhes do comentário do problema para uma API especificada por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/APIs/issues/Comments/Write | Cria um novo comentário para o problema em uma API ou atualiza um existente. |
> | Ação | Microsoft. ApiManagement/Service/APIs/issues/Delete | Exclui o problema especificado de uma API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/issues/Read | Lista todos os problemas associados à API especificada. ou obtém os detalhes do problema para uma API especificada por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/APIs/issues/Write | Cria um novo problema para uma API ou atualiza um existente. ou atualiza um problema existente para uma API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Operations/Delete | Exclui a operação especificada na API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Operations/Policies/Delete | Exclui a configuração de política na operação de API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Operations/Policies/Read | Obtenha a lista de configurações de política no nível de operação da API. ou obtenha a configuração de política no nível de operação da API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Operations/Policies/Write | Cria ou atualiza a configuração de política para o nível de operação da API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/operações/política/excluir | Excluir a configuração de política no nível de operação |
> | Ação | Microsoft. ApiManagement/Service/APIs/Operations/Policy/Read | Obter a configuração de política no nível de operação |
> | Ação | Microsoft. ApiManagement/Service/APIs/Operations/Policy/Write | Criar configuração de política no nível de operação |
> | Ação | Microsoft. ApiManagement/Service/APIs/Operations/Read | Lista uma coleção das operações para a API especificada. ou obtém os detalhes da operação de API especificada por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Operations/Tags/Delete | Desanexe a marca da operação. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Operations/Tags/Read | Lista todas as marcas associadas à operação. ou obter marca associada à operação. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Operations/Tags/Write | Atribua a marcação à operação. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Operations/Write | Cria uma nova operação na API ou atualiza uma existente. ou atualiza os detalhes da operação na API especificada por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/APIs/operationsByTags/Read | Lista uma coleção de operações associadas a marcas. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Policies/Delete | Exclui a configuração de política na API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Policies/Read | Obtenha a configuração de política no nível da API. ou obtenha a configuração de política no nível da API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Policies/Write | Cria ou atualiza a configuração de política para a API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/política/excluir | Excluir a configuração de política no nível da API |
> | Ação | Microsoft. ApiManagement/Service/APIs/Policy/Read | Obter a configuração de política no nível da API |
> | Ação | Microsoft. ApiManagement/Service/APIs/Policy/Write | Criar configuração de política no nível da API |
> | Ação | Microsoft. ApiManagement/Service/APIs/Products/Read | Lista todos os produtos dos quais a API faz parte. |
> | Ação | Microsoft. ApiManagement/Service/APIs/leitura | Lista todas as APIs da instância do serviço de gerenciamento de API. ou obtém os detalhes da API especificada por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/APIs/releases/Delete | Remove todas as versões da API ou exclui a versão especificada na API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/versões/leitura | Lista todas as versões de uma API.<br>Uma versão de API é criada ao tornar a revisão da API atual.<br>As versões também são usadas para reverter para revisões anteriores.<br>Os resultados serão paginados e poderão ser restritos pelos parâmetros $top e $skip.<br>ou retorna os detalhes de uma versão de API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/releases/Write | Cria uma nova versão para a API. ou atualiza os detalhes da versão da API especificada por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/APIs/revisões/excluir | Remove todas as revisões de uma API |
> | Ação | Microsoft. ApiManagement/Service/APIs/revisões/leitura | Lista todas as revisões de uma API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/schemas/Delete | Exclui a configuração de esquema na API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/schemas/Read | Obtenha a configuração do esquema no nível da API. ou obtenha a configuração do esquema no nível da API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/schemas/Write | Cria ou atualiza a configuração de esquema para a API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/tagDescriptions/Delete | Exclua a descrição da marca para a API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/tagDescriptions/Read | Lista todas as descrições de marcas no escopo da API. O modelo semelhante ao Swagger-tagDescription é definido no nível da API, mas a marca pode ser atribuída às operações ou obter a descrição da marca no escopo da API |
> | Ação | Microsoft. ApiManagement/Service/APIs/tagDescriptions/Write | Criar/atualizar a descrição da marca no escopo da API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Tags/Delete | Desanexe a marca da API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Tags/Read | Lista todas as marcas associadas à API. ou obter marca associada à API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Tags/Write | Atribua a marca à API. |
> | Ação | Microsoft. ApiManagement/Service/APIs/Write | Cria novas ou atualiza a API especificada existente da instância do serviço de gerenciamento de API. ou atualiza a API especificada da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/Service/apisByTags/Read | Lista uma coleção de APIs associadas a marcas. |
> | Ação | Microsoft. ApiManagement/Service/apiVersionSets/Delete | Exclui o conjunto de versão de API específico. |
> | Ação | Microsoft. ApiManagement/Service/apiVersionSets/Read | Lista uma coleção de conjuntos de versão de API na instância de serviço especificada. ou obtém os detalhes do conjunto de versão de API especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/apiVersionSets/Versions/Read | Obter lista de entidades de versão |
> | Ação | Microsoft. ApiManagement/Service/apiVersionSets/Write | Cria ou atualiza um conjunto de versões de API. ou atualiza os detalhes do Versionset de API especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/applynetworkconfigurationupdates/Action | Atualiza os recursos Microsoft. ApiManagement em execução na rede virtual para escolher as configurações de rede atualizadas. |
> | Ação | Microsoft. ApiManagement/Service/authorizationServers/Delete | Exclui instância de servidor de autorização específica. |
> | Ação | Microsoft. ApiManagement/Service/authorizationServers/Read | Lista uma coleção de servidores de autorização definidos em uma instância de serviço. ou obtém os detalhes do servidor de autorização especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/authorizationServers/Write | Cria um novo servidor de autorização ou atualiza um servidor de autorização existente. ou atualiza os detalhes do servidor de autorização especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/back-ends/excluir | Exclui o back-end especificado. |
> | Ação | Microsoft. ApiManagement/serviço/back-ends/leitura | Lista uma coleção de back-ends na instância de serviço especificada. ou obtém os detalhes do back-end especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/back-ends/reconexão/ação | Notifica o proxy APIM para criar uma nova conexão com o back-end após o tempo limite especificado. Se nenhum tempo limite for especificado, o tempo limite de 2 minutos será usado. |
> | Ação | Microsoft. ApiManagement/Service/back-ends/gravação | Cria ou atualiza um back-end. ou atualiza um back-end existente. |
> | Ação | Microsoft. ApiManagement/Service/backup/Action | Serviço de gerenciamento de API de backup para o contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Ação | Microsoft. ApiManagement/Service/caches/Delete | Exclui o cache específico. |
> | Ação | Microsoft. ApiManagement/Service/caches/Read | Lista uma coleção de todos os caches externos na instância de serviço especificada. ou obtém os detalhes do cache especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/caches/Write | Cria ou atualiza um cache externo a ser usado na instância de gerenciamento de API. ou atualiza os detalhes do cache especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/Certificates/Delete | Exclui um certificado específico. |
> | Ação | Microsoft. ApiManagement/serviço/certificados/ler | Lista uma coleção de todos os certificados na instância de serviço especificada. ou obtém os detalhes do certificado especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/Certificates/Write | Cria ou atualiza o certificado que está sendo usado para autenticação com o back-end. |
> | Ação | Microsoft. ApiManagement/Service/contentTypes/contentItems/Delete | Remove o item de conteúdo especificado. |
> | Ação | Microsoft. ApiManagement/Service/contentTypes/contentItems/Read | Retorna a lista de itens de conteúdo ou retorna detalhes do item de conteúdo |
> | Ação | Microsoft. ApiManagement/Service/contentTypes/contentItems/Write | Cria um novo item de conteúdo ou atualiza o item de conteúdo especificado |
> | Ação | Microsoft. ApiManagement/Service/contentTypes/Read | Retorna a lista de tipos de conteúdo |
> | Ação | Microsoft. ApiManagement/Service/Delete | Excluir instância do serviço de gerenciamento de API |
> | Ação | Microsoft. ApiManagement/Service/Diagnostics/Delete | Exclui o diagnóstico especificado. |
> | Ação | Microsoft. ApiManagement/serviço/diagnóstico/leitura | Lista todos os diagnósticos da instância do serviço de gerenciamento de API. ou obtém os detalhes do diagnóstico especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/Diagnostics/Write | Cria um novo diagnóstico ou atualiza um existente. ou atualiza os detalhes do diagnóstico especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/serviço/gateways/ação | Recupera a configuração do gateway. ou atualiza a pulsação do gateway. |
> | Ação | Microsoft. ApiManagement/Service/gateways/Delete | Exclui gateway específico. |
> | Ação | Microsoft. ApiManagement/serviço/gateways/chaves/ação | Recupera as chaves de gateway. |
> | Ação | Microsoft. ApiManagement/Service/gateways/leitura | Lista uma coleção de gateways registrados com a instância de serviço. ou obtém os detalhes do gateway especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/gateways/regeneratePrimaryKey/Action | Regenera a chave primária do gateway invalidationg todos os tokens criados com ele. |
> | Ação | Microsoft. ApiManagement/Service/gateways/regenerateSecondaryKey/Action | Regenera a chave do gateway secundário invalidationg quaisquer tokens criados com ele. |
> | Ação | Microsoft. ApiManagement/Service/gateways/token/ação | Obtém o token de autorização de acesso compartilhado para o gateway. |
> | Ação | Microsoft. ApiManagement/Service/gateways/Write | Cria ou atualiza um gateway a ser usado na instância de gerenciamento de API. ou atualiza os detalhes do gateway especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/getssotoken/Action | Obtém o token SSO que pode ser usado para fazer logon no portal herdado do serviço de gerenciamento de API como administrador |
> | Ação | Microsoft. ApiManagement/Service/groups/Delete | Exclui o grupo específico da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/Service/groups/Read | Lista uma coleção de grupos definidos em uma instância de serviço. ou obtém os detalhes do grupo especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/groups/Users/Delete | Remover usuário existente do grupo existente. |
> | Ação | Microsoft. ApiManagement/Service/groups/Users/Read | Lista uma coleção de entidades de usuário associadas ao grupo. |
> | Ação | Microsoft. ApiManagement/Service/groups/Users/Write | Adicionar usuário existente a um grupo existente |
> | Ação | Microsoft. ApiManagement/Service/groups/Write | Cria ou atualiza um grupo. ou atualiza os detalhes do grupo especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/identityProviders/Delete | Exclui a configuração do provedor de identidade especificado. |
> | Ação | Microsoft. ApiManagement/Service/identityProviders/Read | Lista uma coleção de provedor de identidade configurada na instância de serviço especificada. ou obtém os detalhes de configuração do provedor de identidade configurado na instância de serviço especificada. |
> | Ação | Microsoft. ApiManagement/Service/identityProviders/Write | Cria ou atualiza a configuração Identityprovider. ou atualiza uma configuração existente do Identityprovider. |
> | Ação | Microsoft. ApiManagement/serviço/problemas/ler | Lista uma coleção de problemas na instância de serviço especificada. ou obtém detalhes do problema de gerenciamento de API |
> | Ação | Microsoft. ApiManagement/Service/Locations/networkstatus/Read | Obtém o status de acesso à rede dos recursos nos quais o serviço depende no local. |
> | Ação | Microsoft. ApiManagement/Service/loggers/Delete | Exclui o agente de log especificado. |
> | Ação | Microsoft. ApiManagement/Service/loggers/Read | Lista uma coleção de agentes de log na instância de serviço especificada. ou obtém os detalhes do agente de log especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/loggers/Write | Cria ou atualiza um agente de log. ou atualiza um agente de log existente. |
> | Ação | Microsoft. ApiManagement/Service/managedeployments/Action | Alterar SKU/unidades, adicionar/remover implantações regionais do serviço de gerenciamento de API |
> | Ação | Microsoft. ApiManagement/Service/networkstatus/Read | Obtém o status de acesso à rede dos recursos nos quais o serviço depende. |
> | Ação | Microsoft. ApiManagement/Service/Notifications/Action | Envia a notificação para um usuário especificado |
> | Ação | Microsoft. ApiManagement/serviço/notificações/leitura | Lista uma coleção de propriedades definidas em uma instância de serviço. ou obtém os detalhes da notificação especificada por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/Notifications/recipientEmails/Delete | Remove o email da lista de notificações. |
> | Ação | Microsoft. ApiManagement/Service/Notifications/recipientEmails/Read | Obtém a lista dos emails de destinatário da notificação inscritos em uma notificação. |
> | Ação | Microsoft. ApiManagement/Service/Notifications/recipientEmails/Write | Adiciona o endereço de email à lista de destinatários da notificação. |
> | Ação | Microsoft. ApiManagement/Service/Notifications/recipientUsers/Delete | Remove o usuário de gerenciamento de API da lista de notificações. |
> | Ação | Microsoft. ApiManagement/Service/Notifications/recipientUsers/Read | Obtém a lista do usuário do destinatário da notificação inscrito na notificação. |
> | Ação | Microsoft. ApiManagement/Service/Notifications/recipientUsers/Write | Adiciona o usuário de gerenciamento de API à lista de destinatários da notificação. |
> | Ação | Microsoft. ApiManagement/Service/Notifications/Write | Criar ou atualizar a notificação do editor de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/Service/openidConnectProviders/Delete | Exclui o provedor do OpenID Connect específico da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/Service/openidConnectProviders/Read | Listas de todos os provedores do OpenId Connect. ou Obtém um provedor do OpenID Connect específico. |
> | Ação | Microsoft. ApiManagement/Service/openidConnectProviders/Write | Cria ou atualiza o provedor do OpenID Connect. ou atualiza o provedor do OpenID Connect específico. |
> | Ação | Microsoft. ApiManagement/Service/operationresults/Read | Obtém o status atual da operação de longa execução |
> | Ação | Microsoft. ApiManagement/Service/Policies/Delete | Exclui a configuração de política global do serviço de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/Service/Policies/Read | Lista todas as definições de política global do serviço de gerenciamento de API. ou obtenha a definição de política global do serviço de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/Service/Policies/Write | Cria ou atualiza a configuração de política global do serviço de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/serviço/política/exclusão | Excluir a configuração de política no nível do locatário |
> | Ação | Microsoft. ApiManagement/serviço/política/leitura | Obter a configuração de política no nível do locatário |
> | Ação | Microsoft. ApiManagement/Service/Policy/Write | Criar configuração de política no nível do locatário |
> | Ação | Microsoft. ApiManagement/Service/policyDescriptions/Read | Lista todas as descrições de política. |
> | Ação | Microsoft. ApiManagement/Service/policySnippets/Read | Lista todos os trechos de política. |
> | Ação | Microsoft. ApiManagement/Service/portalsettings/Read | Obtenha configurações de entrada para o portal ou obtenha configurações de inscrição para o portal ou obtenha configurações de delegação para o Portal. |
> | Ação | Microsoft. ApiManagement/Service/portalsettings/Write | Atualize as configurações de entrada. ou criar ou atualizar configurações de entrada. ou atualize as configurações de inscrição ou atualize as configurações de inscrição ou atualize as configurações de delegação. ou criar ou atualizar configurações de delegação. |
> | Ação | Microsoft. ApiManagement/Service/Products/APIs/Delete | Exclui a API especificada do produto especificado. |
> | Ação | Microsoft. ApiManagement/Service/Products/APIs/Read | Lista uma coleção das APIs associadas a um produto. |
> | Ação | Microsoft. ApiManagement/Service/Products/APIs/Write | Adiciona uma API ao produto especificado. |
> | Ação | Microsoft. ApiManagement/Service/Products/Delete | Excluir produto. |
> | Ação | Microsoft. ApiManagement/Service/Products/groups/Delete | Exclui a associação entre o grupo e o produto especificados. |
> | Ação | Microsoft. ApiManagement/serviço/produtos/grupos/ler | Lista a coleção de grupos de desenvolvedores associados ao produto especificado. |
> | Ação | Microsoft. ApiManagement/Service/Products/groups/Write | Adiciona a associação entre o grupo de desenvolvedores especificado com o produto especificado. |
> | Ação | Microsoft. ApiManagement/Service/Products/Policies/Delete | Exclui a configuração de política no produto. |
> | Ação | Microsoft. ApiManagement/serviço/produtos/políticas/leitura | Obtenha a configuração de política no nível do produto. ou obtenha a configuração de política no nível do produto. |
> | Ação | Microsoft. ApiManagement/Service/Products/Policies/Write | Cria ou atualiza a configuração de política para o produto. |
> | Ação | Microsoft. ApiManagement/serviço/produtos/política/excluir | Excluir a configuração de política no nível do produto |
> | Ação | Microsoft. ApiManagement/serviço/produtos/política/leitura | Obter a configuração de política no nível do produto |
> | Ação | Microsoft. ApiManagement/Service/Products/Policy/Write | Criar configuração de política no nível do produto |
> | Ação | Microsoft. ApiManagement/serviço/produtos/leitura | Lista uma coleção de produtos na instância de serviço especificada. ou obtém os detalhes do produto especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/Products/subscriptions/Read | Lista a coleção de assinaturas para o produto especificado. |
> | Ação | Microsoft. ApiManagement/Service/Products/Tags/Delete | Desanexe a marca do produto. |
> | Ação | Microsoft. ApiManagement/serviço/produtos/marcas/leitura | Lista todas as marcas associadas ao produto. ou obtenha a marca associada ao produto. |
> | Ação | Microsoft. ApiManagement/Service/Products/Tags/Write | Atribua a marca ao produto. |
> | Ação | Microsoft. ApiManagement/Service/Products/Write | Cria ou atualiza um produto. ou atualize os detalhes do produto existente. |
> | Ação | Microsoft. ApiManagement/Service/productsByTags/Read | Lista uma coleção de produtos associados às marcas. |
> | Ação | Microsoft. ApiManagement/serviço/Propriedades/excluir | Exclui a propriedade específica da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/serviço/Propriedades/ler | Lista uma coleção de propriedades definidas em uma instância de serviço. ou obtém os detalhes da propriedade especificada por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/Properties/Write | Cria ou atualiza uma propriedade. ou atualiza a propriedade específica. |
> | Ação | Microsoft. ApiManagement/serviço/cotas/períodos/leitura | Obter valor do contador de cotas para o período |
> | Ação | Microsoft. ApiManagement/serviço/cotas/períodos/gravação | Definir valor atual do contador de cota |
> | Ação | Microsoft. ApiManagement/serviço/cotas/leitura | Obter valores para cota |
> | Ação | Microsoft. ApiManagement/Service/cotas/gravação | Definir valor atual do contador de cota |
> | Ação | Microsoft. ApiManagement/serviço/leitura | Ler metadados para uma instância do serviço de gerenciamento de API |
> | Ação | Microsoft. ApiManagement/Service/regions/Read | Lista todas as regiões do Azure nas quais o serviço existe. |
> | Ação | Microsoft. ApiManagement/Service/Reports/Read | Obtenha o relatório agregado por períodos de tempo ou obtenha o relatório agregado por região geográfica ou obtenha um relatório agregado pelos desenvolvedores.<br>ou obter relatório agregado por produtos.<br>ou obter relatório agregado por APIs ou obter relatório agregado por operações ou obter relatório agregado por assinatura.<br>ou obter dados de relatórios de solicitações |
> | Ação | Microsoft. ApiManagement/Service/Restore/Action | Restaurar o serviço de gerenciamento de API do contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Ação | Microsoft. ApiManagement/Service/subscriptions/Delete | Exclui a assinatura especificada. |
> | Ação | Microsoft. ApiManagement/Service/subscriptions/Read | Lista todas as assinaturas da instância do serviço de gerenciamento de API. ou obtém a entidade de assinatura especificada. |
> | Ação | Microsoft. ApiManagement/Service/subscriptions/regeneratePrimaryKey/Action | Regenera a chave primária da assinatura existente da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/Service/subscriptions/regenerateSecondaryKey/Action | Regenera a chave secundária da assinatura existente da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/Service/subscriptions/Write | Cria ou atualiza a assinatura do usuário especificado para o produto especificado. ou atualiza os detalhes de uma assinatura especificada por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/tagResources/Read | Lista uma coleção de recursos associados com marcas. |
> | Ação | Microsoft. ApiManagement/Service/Tags/Delete | Exclui a marca específica da instância do serviço de gerenciamento de API. |
> | Ação | Microsoft. ApiManagement/serviço/marcas/leitura | Lista uma coleção de marcas definidas em uma instância de serviço. ou obtém os detalhes da marca especificada por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/Tags/Write | Cria uma marca. ou atualiza os detalhes da marca especificada por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/templates/Delete | Redefinir modelo de email de gerenciamento de API padrão |
> | Ação | Microsoft. ApiManagement/Service/templates/Read | Obtém todos os modelos de email ou obtém detalhes do modelo de email de gerenciamento de API |
> | Ação | Microsoft. ApiManagement/Service/templates/Write | Criar ou atualizar modelo de email de gerenciamento de API ou atualizações de modelo de email de gerenciamento de API |
> | Ação | Microsoft. ApiManagement/Service/locatário/Delete | Remover configuração de política para o locatário |
> | Ação | Microsoft. ApiManagement/serviço/locatário/implantar/ação | Executa uma tarefa de implantação para aplicar as alterações da ramificação git especificada para a configuração no banco de dados. |
> | Ação | Microsoft. ApiManagement/Service/Tenant/operationResults/Read | Obter lista de resultados da operação ou obter resultado de uma operação específica |
> | Ação | Microsoft. ApiManagement/serviço/locatário/leitura | Obtenha a definição de política global do serviço de gerenciamento de API. ou obter detalhes de informações de acesso do locatário |
> | Ação | Microsoft. ApiManagement/Service/locatário/regeneratePrimaryKey/Action | Regenerar chave de acesso primária |
> | Ação | Microsoft. ApiManagement/Service/locatário/regenerateSecondaryKey/Action | Regenerar chave de acesso secundária |
> | Ação | Microsoft. ApiManagement/serviço/locatário/salvar/ação | Cria a confirmação com o instantâneo de configuração para a ramificação especificada no repositório |
> | Ação | Microsoft. ApiManagement/Service/locatário/SyncState/Read | Obter o status da última sincronização git |
> | Ação | Microsoft. ApiManagement/serviço/locatário/validar/ação | Valida as alterações do Branch git especificado |
> | Ação | Microsoft. ApiManagement/Service/Tenant/Write | Definir a configuração de política para o locatário ou atualizar detalhes de informações de acesso do locatário |
> | Ação | Microsoft. ApiManagement/Service/updatecertificate/Action | Carregar o certificado SSL para um serviço de gerenciamento de API |
> | Ação | Microsoft. ApiManagement/Service/updatehostname/Action | Configurar, atualizar ou remover nomes de domínio personalizados para um serviço de gerenciamento de API |
> | Ação | Microsoft. ApiManagement/Service/Users/Action | Registrar um novo usuário |
> | Ação | Microsoft. ApiManagement/serviço/usuários/confirmações/enviar/ação | Envia confirmação |
> | Ação | Microsoft. ApiManagement/Service/Users/Delete | Exclui um usuário específico. |
> | Ação | Microsoft. ApiManagement/Service/Users/generateSsoUrl/Action | Recupera uma URL de redirecionamento que contém um token de autenticação para assinar um determinado usuário no portal do desenvolvedor. |
> | Ação | Microsoft. ApiManagement/Service/Users/groups/Read | Lista todos os grupos de usuários. |
> | Ação | Microsoft. ApiManagement/Service/Users/Identities/Read | Lista de todas as identidades de usuário. |
> | Ação | Microsoft. ApiManagement/Service/Users/Keys/Read | Obter chaves associadas ao usuário |
> | Ação | Microsoft. ApiManagement/serviço/usuários/ler | Lista uma coleção de usuários registrados na instância de serviço especificada. ou obtém os detalhes do usuário especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/Users/subscriptions/Read | Lista a coleção de assinaturas do usuário especificado. |
> | Ação | Microsoft. ApiManagement/Service/Users/token/Action | Obtém o token de autorização de acesso compartilhado para o usuário. |
> | Ação | Microsoft. ApiManagement/Service/Users/Write | Cria ou atualiza um usuário. ou atualiza os detalhes do usuário especificado por seu identificador. |
> | Ação | Microsoft. ApiManagement/Service/Write | Criar uma nova instância do serviço de gerenciamento de API |
> | Ação | Microsoft. ApiManagement/cancelar registro/ação | Cancelar registro de assinatura para o provedor de recursos Microsoft. ApiManagement |

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Authorization/classicAdministrators/Delete | Remove o administrador da assinatura. |
> | Ação | Microsoft. Authorization/classicAdministrators/operationstatuses/Read | Obtém os status da operação do administrador da assinatura. |
> | Ação | Microsoft. Authorization/classicAdministrators/Read | Lê os administradores da assinatura. |
> | Ação | Microsoft. Authorization/classicAdministrators/Write | Adicionar ou modificar o administrador de uma assinatura. |
> | Ação | Microsoft. Authorization/denyAssignments/Delete | Excluir uma atribuição de negação no escopo especificado. |
> | Ação | Microsoft. Authorization/denyAssignments/Read | Obter informações sobre uma atribuição de negação. |
> | Ação | Microsoft. Authorization/denyAssignments/Write | Criar uma atribuição de negação no escopo especificado. |
> | Ação | Microsoft. Authorization/elevateAccess/Action | Concede ao usuário do chamador acesso de administrador no escopo do locatário |
> | Ação | Microsoft. Authorization/Locks/Delete | Excluir bloqueios no escopo especificado. |
> | Ação | Microsoft. Authorization/Locks/Read | Obtém bloqueios no escopo especificado. |
> | Ação | Microsoft. Authorization/Locks/Write | Adicionar bloqueios no escopo especificado. |
> | Ação | Microsoft. Authorization/Operations/Read | Obtém a lista de operações |
> | Ação | Microsoft. Authorization/Permissions/Read | Lista todas as permissões que o chamador tem em um determinado escopo. |
> | Ação | Microsoft. Authorization/Policies/Audit/Action | Ação executada como resultado da avaliação de Azure Policy com o efeito de ' auditoria ' |
> | Ação | Microsoft. Authorization/Policies/auditIfNotExists/Action | Ação executada como resultado da avaliação de Azure Policy com o efeito ' auditIfNotExists ' |
> | Ação | Microsoft. Authorization/Policies/Deny/Action | Ação executada como resultado da avaliação de Azure Policy com o efeito ' negar ' |
> | Ação | Microsoft. Authorization/Policies/deployIfNotExists/Action | Ação executada como resultado da avaliação de Azure Policy com o efeito ' deployIfNotExists ' |
> | Ação | Microsoft. Authorization/policyAssignments/Delete | Excluir uma atribuição de política no escopo especificado. |
> | Ação | Microsoft. Authorization/policyAssignments/Read | Obter informações sobre uma atribuição de política. |
> | Ação | Microsoft. Authorization/policyAssignments/Write | Crie uma atribuição de política no escopo especificado. |
> | Ação | Microsoft. Authorization/policyDefinitions/Delete | Excluir uma definição de política. |
> | Ação | Microsoft. Authorization/policyDefinitions/Read | Obter informações sobre uma definição de política. |
> | Ação | Microsoft. Authorization/policyDefinitions/Write | Crie uma definição de política personalizada. |
> | Ação | Microsoft. Authorization/policySetDefinitions/Delete | Excluir uma definição de conjunto de políticas. |
> | Ação | Microsoft. Authorization/policySetDefinitions/Read | Obter informações sobre uma definição de conjunto de políticas. |
> | Ação | Microsoft. Authorization/policySetDefinitions/Write | Crie uma definição de conjunto de políticas personalizadas. |
> | Ação | Microsoft. Authorization/providerOperations/Read | Obtenha operações para todos os provedores de recursos que podem ser usados em definições de função. |
> | Ação | Microsoft. Authorization/roleAssignments/Delete | Excluir uma atribuição de função no escopo especificado. |
> | Ação | Microsoft. Authorization/roleAssignments/Read | Obter informações sobre uma atribuição de função. |
> | Ação | Microsoft.Authorization/roleAssignments/write | Crie uma atribuição de função no escopo especificado. |
> | Ação | Microsoft. Authorization/roleDefinitions/Delete | Exclua a definição de função personalizada especificada. |
> | Ação | Microsoft. Authorization/roleDefinitions/Read | Obter informações sobre uma definição de função. |
> | Ação | Microsoft. Authorization/roleDefinitions/Write | Crie ou atualize uma definição de função personalizada com permissões especificadas e escopos atribuíveis. |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Automation/automationAccounts/agentRegistrationInformation/Read | Ler as informações de registro de DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/agentRegistrationInformation/regenerateKey/Action | Grava uma solicitação para regenerar as chaves de DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Certificates/Delete | Exclui um ativo de certificado de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Certificates/GetCount/Action | Lê a contagem de certificados |
> | Ação | Microsoft. Automation/automationAccounts/Certificates/Read | Obter um ativo de certificado de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Certificates/Write | Criar ou atualizar um ativo de certificado de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/compilationjobs/Read | Lê uma compilação de DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/compilationjobs/Read | Lê uma compilação de DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/compilationjobs/Write | Grava a compilação de um DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/compilationjobs/Write | Grava a compilação de um DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/configurações/conteúdo/leitura | Lê o conteúdo da mídia de configuração |
> | Ação | Microsoft. Automation/automationAccounts/Configurations/Delete | Exclui o conteúdo de um DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Configurations/getconte/ação | Lê a contagem de conteúdo de um DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Configurations/ler | Obtém o conteúdo de um DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/configurações/gravação | Grava o conteúdo de um DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Connections/Delete | Exclui um ativo de conexão de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Connections/GetCount/Action | Lê a contagem de conexões |
> | Ação | Microsoft. Automation/automationAccounts/Connections/Read | Obter um ativo de conexão de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Connections/Write | Criar ou atualizar um ativo de conexão de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/ConnectionName/Delete | Exclui um ativo de tipo de conexão de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/ConnectionName/leitura | Obtém um ativo de tipo de conexão de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/ConnectionName/Write | Cria um ativo de tipo de conexão de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Credentials/Delete | Exclui um ativo de credencial de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Credentials/GetCount/Action | Lê a contagem de credenciais |
> | Ação | Microsoft. Automation/automationAccounts/Credentials/Read | Obter um ativo de credencial de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Credentials/Write | Criar ou atualizar um ativo de credencial de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Delete | Excluir uma conta de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Delete | Exclui Hybrid Runbook Worker recursos |
> | Ação | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Read | Lê Hybrid Runbook Worker recursos |
> | Ação | Microsoft. Automation/automationAccounts/Jobs/saída/leitura | Obtém a saída de um trabalho |
> | Ação | Microsoft. Automation/automationAccounts/Jobs/ler | Obter um trabalho de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Jobs/retomar/ação | Retoma um trabalho de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Jobs/runbookContent/ação | Obtém o conteúdo do runbook de automação do Azure no momento da execução do trabalho |
> | Ação | Microsoft. Automation/automationAccounts/Jobs/parar/ação | Para um trabalho de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Jobs/streams/Read | Obter um fluxo de trabalho de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Jobs/streams/Read | Obter um fluxo de trabalho de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Jobs/suspender/ação | Suspende um trabalho de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Jobs/Write | Cria um trabalho de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/jobSchedules/Delete | Excluir uma agenda de trabalho de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/jobSchedules/Read | Obter um plano de trabalho de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/jobSchedules/Write | Cria um plano de trabalho de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/linkedWorkspace/Read | Obtém o espaço de trabalho vinculado à conta de automação |
> | Ação | Microsoft. Automation/automationAccounts/listKeys/Action | Lê as chaves para a conta de automação |
> | Ação | Microsoft. Automation/automationAccounts/modules/Activities/Read | Obter atividades de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/modules/Delete | Exclui um módulo do PowerShell de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/modules/GetCount/Action | Obtém a contagem de módulos do PowerShell dentro da conta de automação |
> | Ação | Microsoft. Automation/automationAccounts/modules/Read | Obter um módulo do PowerShell de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/modules/Write | Criar ou atualizar um módulo do PowerShell de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/nodeConfigurations/Delete | Exclui a configuração de nó de um DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/nodeConfigurations/rawContent/Action | Lê um conteúdo de configuração de nó do DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/nodeConfigurations/Read | Lê uma configuração de nó do DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/nodeConfigurations/Write | Grava a configuração de nó de um DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/nodecounts/Read | Lê o resumo de contagem de nós para o tipo especificado |
> | Ação | Microsoft. Automation/automationAccounts/Nodes/Delete | Exclui os nós de DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/nós/leitura | Lê os nós de DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/nós/relatórios/conteúdo/leitura | Lê o conteúdo do relatório de DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/nós/relatórios/ler | Lê relatórios de DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Nodes/Write | Criar ou atualizar nós de DSC de Automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/objectdatatypes/Fields/Read | Obter TypeFields de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/python2Packages/Delete | Exclui um pacote Python 2 de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/python2Packages/Read | Obter um pacote do Python 2 de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/python2Packages/Write | Criar ou atualizar um pacote Python 2 de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/python3Packages/Delete | Exclui um pacote do Python 3 de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/python3Packages/Read | Obter um pacote do Python 3 de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/python3Packages/Write | Criar ou atualizar um pacote do Python 3 de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Read | Obter uma conta de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/conteúdo/leitura | Obter o conteúdo de um runbook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/excluir | Exclui um runbook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/rascunho/conteúdo/gravação | Cria o conteúdo de um rascunho do runbook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/rascunho/operationResults/ler | Obter resultados da operação de rascunho de runbook da automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/rascunho/leitura | Obter um rascunho do runbook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/rascunho/testJob/ler | Obter um trabalho de teste de rascunho de runbook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/rascunho/testJob/retomar/ação | Retoma um trabalho de teste de rascunho de runbook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/rascunho/testJob/Stop/Action | Interrompe um trabalho de teste de rascunho de runbook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/rascunho/testJob/suspender/ação | Suspende um trabalho de teste de rascunho do runbook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/rascunho/testJob/gravação | Cria um trabalho de teste de rascunho de runbook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/rascunho/undoEdit/ação | Desfazer edições em um rascunho do runbook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/getconte/ação | Obtém a contagem de runbooks de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/publicação/ação | Publica um rascunho do runbook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/ler | Obter um runbook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/runbooks/gravar | Criar ou atualizar um runbook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Schedules/Delete | Exclui um ativo de agendamento de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Schedules/getconte/ação | Obtém a contagem de agendamentos de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Schedules/Read | Obter um ativo de agendamento de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Schedules/Write | Criar ou atualizar um ativo de agendamento de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/Read | Obter uma execução da máquina de configuração de atualização de software de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/Read | Obter uma execução de configuração de atualização de software de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Delete | Exclui uma configuração de atualização de software de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Delete | Exclui uma configuração de atualização de software de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Read | Obter uma configuração de atualização de software de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Read | Obter uma configuração de atualização de software de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Write | Criar ou atualizar a configuração de atualização de software de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/softwareUpdateConfigurations/Write | Criar ou atualizar a configuração de atualização de software de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Statistics/Read | Obter estatísticas de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/updateDeploymentMachineRuns/Read | Obter uma máquina de implantação de atualização de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/updateManagementPatchJob/Read | Obter um trabalho de patch de gerenciamento de atualização da automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Usages/Read | Obtém o uso da automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Variables/Delete | Exclui um ativo variável de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Variables/Read | Lê um ativo variável de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Variables/Write | Criar ou atualizar um ativo de variável de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/inspetores/excluir | Excluir um trabalho do observador de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/inspetores/leitura | Obter um trabalho do observador de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/inspetores/iniciar/ação | Iniciar um trabalho do observador de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/inspetores/Stop/Action | Parar um trabalho do observador de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Watchers/streams/Read | Obter um fluxo de trabalho do observador de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/inspetores/watcherActions/Delete | Excluir ações de trabalho do observador de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Watchers/watcherActions/Read | Obter uma ação de trabalho do observador de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Watchers/watcherActions/Write | Criar ações de trabalho do observador de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/inspetores/gravação | Cria um trabalho do observador de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/WebHooks/ação | Gera um URI para um webhook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/WebHooks/excluir | Exclui um webhook de automação do Azure  |
> | Ação | Microsoft. Automation/automationAccounts/WebHooks/leitura | Lê um webhook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/WebHooks/gravação | Criar ou atualizar um webhook de automação do Azure |
> | Ação | Microsoft. Automation/automationAccounts/Write | Criar ou atualizar uma conta de automação do Azure |
> | Ação | Microsoft. Automation/Operations/Read | Obter operações disponíveis para recursos de automação do Azure |
> | Ação | Microsoft. Automation/registrar/ação | Registra a assinatura na automação do Azure |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. AzureActiveDirectory/b2cDirectories/Delete | Excluir recurso de diretório do B2C |
> | Ação | Microsoft. AzureActiveDirectory/b2cDirectories/Read | Exibir recurso de diretório do B2C |
> | Ação | Microsoft. AzureActiveDirectory/b2cDirectories/Write | Criar ou atualizar o recurso de diretório do B2C |
> | Ação | Microsoft. AzureActiveDirectory/b2ctenants/Read | Lista todos os locatários B2C nos quais o usuário é membro |
> | Ação | Microsoft. AzureActiveDirectory/operações/leitura | Ler todas as operações de API disponíveis para o provedor de recursos Microsoft. AzureActiveDirectory |
> | Ação | Microsoft. AzureActiveDirectory/registro/ação | Registrar a assinatura para o provedor de recursos Microsoft. AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. AzureStack/operações/leitura | Obtém as propriedades de uma operação do provedor de recursos |
> | Ação | Microsoft. AzureStack/registro/ação | Registra a assinatura com o provedor de recursos Microsoft. AzureStack |
> | Ação | Microsoft. AzureStack/registros/customerSubscriptions/Delete | Exclui uma assinatura de cliente do Azure Stack |
> | Ação | Microsoft. AzureStack/Registration/customerSubscriptions/Read | Obter as propriedades de uma assinatura de cliente do Azure Stack |
> | Ação | Microsoft. AzureStack/registrations/customerSubscriptions/Write | Criar ou atualizar uma assinatura de cliente do Azure Stack |
> | Ação | Microsoft. AzureStack/registros/excluir | Exclui um registro de Azure Stack |
> | Ação | Microsoft. AzureStack/inscrições/getActivationKey/ação | Obtém a chave de ativação de Azure Stack mais recente |
> | Ação | Microsoft. AzureStack/inscrições/produtos/listDetails/ação | Recupera detalhes estendidos para um produto Azure Stack Marketplace |
> | Ação | Microsoft. AzureStack/registros/produtos/leitura | Obter as propriedades de um produto Azure Stack Marketplace |
> | Ação | Microsoft. AzureStack/inscrições/produtos/uploadProductLog/ação | Registrar Azure Stack status de operação do produto Marketplace e carimbo de data/hora |
> | Ação | Microsoft. AzureStack/registros/leitura | Obtém as propriedades de um registro de Azure Stack |
> | Ação | Microsoft. AzureStack/registros/gravação | Criar ou atualizar um registro de Azure Stack |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. batch/batchAccounts/Applications/Delete | Exclui um aplicativo |
> | Ação | Microsoft. batch/batchAccounts/Applications/Read | Listar aplicativos ou obter as propriedades de um aplicativo |
> | Ação | Microsoft. batch/batchAccounts/Applications/Versions/Activate/Action | Ativa um pacote de aplicativos |
> | Ação | Microsoft. batch/batchAccounts/Applications/Versions/Delete | Exclui um pacote de aplicativos |
> | Ação | Microsoft. batch/batchAccounts/Applications/Versions/Read | Obtém as propriedades de um pacote de aplicativos |
> | Ação | Microsoft. batch/batchAccounts/Applications/Versions/Write | Cria um novo pacote de aplicativos ou atualiza um pacote de aplicativos existente |
> | Ação | Microsoft. batch/batchAccounts/Applications/Write | Cria um novo aplicativo ou atualiza um aplicativo existente |
> | Ação | Microsoft. batch/batchAccounts/certificateOperationResults/Read | Obtém os resultados de uma operação de certificado de execução longa em uma conta do lote |
> | Ação | Microsoft. batch/batchAccounts/Certificates/cancelDelete/Action | Cancela a exclusão com falha de um certificado em uma conta do lote |
> | Ação | Microsoft. batch/batchAccounts/Certificates/Delete | Exclui um certificado de uma conta do lote |
> | Ação | Microsoft. batch/batchAccounts/Certificates/Read | Listar certificados em uma conta do lote ou obter as propriedades de um certificado |
> | Ação | Microsoft. batch/batchAccounts/Certificates/Write | Cria um novo certificado em uma conta do lote ou atualiza um certificado existente |
> | Ação | Microsoft. batch/batchAccounts/Delete | Exclui uma conta do lote |
> | Dataaction | Microsoft. batch/batchAccounts/trabalhos/excluir | Exclui um trabalho de uma conta do lote |
> | Dataaction | Microsoft. batch/batchAccounts/trabalhos/ler | Listar trabalhos em uma conta do lote ou obter as propriedades de um trabalho |
> | Dataaction | Microsoft. batch/batchAccounts/Jobs/Write | Cria um novo trabalho em uma conta do lote ou atualiza um trabalho existente |
> | Dataaction | Microsoft. batch/batchAccounts/jobSchedules/Delete | Excluir um plano de trabalho de uma conta do lote |
> | Dataaction | Microsoft. batch/batchAccounts/jobSchedules/Read | Listar agendas de trabalho em uma conta do lote ou obter as propriedades de uma agenda de trabalho |
> | Dataaction | Microsoft. batch/batchAccounts/jobSchedules/Write | Cria uma nova agenda de trabalho em uma conta do lote ou atualiza uma agenda de trabalho existente |
> | Ação | Microsoft. batch/batchAccounts/listkeys/Action | Lista as chaves de acesso para uma conta do lote |
> | Ação | Microsoft. batch/batchAccounts/operationResults/Read | Obtém os resultados de uma operação de conta de lote de longa execução |
> | Ação | Microsoft. batch/batchAccounts/poolOperationResults/Read | Obtém os resultados de uma operação de pool de longa execução em uma conta do lote |
> | Ação | Microsoft. batch/batchAccounts/pools/Delete | Exclui um pool de uma conta do lote |
> | Ação | Microsoft. batch/batchAccounts/pools/disableAutoscale/Action | Desabilita o dimensionamento automático para um pool de contas do lote |
> | Ação | Microsoft. batch/batchAccounts/pools/leitura | Lista os pools em uma conta do lote ou obtém as propriedades de um pool |
> | Ação | Microsoft. batch/batchAccounts/pools/stopResize/Action | Interrompe uma operação de redimensionamento em andamento em um pool de contas do lote |
> | Ação | Microsoft. batch/batchAccounts/pools/gravação | Cria um novo pool em uma conta do lote ou atualiza um pool existente |
> | Ação | Microsoft. batch/batchAccounts/Read | Listar contas do lote ou obter as propriedades de uma conta do lote |
> | Ação | Microsoft. batch/batchAccounts/regeneratekeys/Action | Regenera as chaves de acesso para uma conta do lote |
> | Ação | Microsoft. batch/batchAccounts/syncAutoStorageKeys/Action | Sincroniza chaves de acesso para a conta de armazenamento automático configurada para uma conta do lote |
> | Ação | Microsoft. batch/batchAccounts/Write | Cria uma nova conta do lote ou atualiza uma conta do lote existente |
> | Ação | Microsoft. batch/Locations/accountOperationResults/Read | Obtém os resultados de uma operação de conta de lote de longa execução |
> | Ação | Microsoft. batch/Locations/checkNameAvailability/Action | Verifica se o nome da conta é válido e não está em uso. |
> | Ação | Microsoft. batch/localizações/cotas/leitura | Obtém as cotas de lote da assinatura especificada na região do Azure especificada |
> | Ação | Microsoft. batch/Operations/Read | Lista as operações disponíveis no provedor de recursos Microsoft. batch |
> | Ação | Microsoft. batch/registrar/ação | Registra a assinatura para o provedor de recursos do lote e habilita a criação de contas do lote |
> | Ação | Microsoft. batch/cancelar registro/ação | Cancela o registro da assinatura para o provedor de recursos do lote que impede a criação de contas do lote |

## <a name="microsoftbilling"></a>Microsoft. billing

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
> | Ação | Microsoft. billing/billingAccounts/departamentos/leitura |  |
> | Ação | Microsoft. billing/billingAccounts/enrollmentAccounts/billingPermissions/Read |  |
> | Ação | Microsoft. billing/billingAccounts/enrollmentAccounts/Read |  |
> | Ação | Microsoft. billing/billingAccounts/enrollmentDepartments/billingPermissions/Read |  |
> | Ação | Microsoft. billing/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/Action |  |
> | Ação | Microsoft. billing/billingAccounts/Products/Read |  |
> | Ação | Microsoft. billing/billingAccounts/Read |  |
> | Ação | Microsoft. billing/billingAccounts/Write |  |
> | Ação | Microsoft. billing/departamentos/leitura |  |
> | Ação | Microsoft. cobrança/registro/ação |  |
> | Ação | Microsoft. billing/validateAddress/Action |  |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. BingMaps/mapApis/Delete | Operação de exclusão |
> | Ação | Microsoft. BingMaps/mapApis/listSecrets/Action | Listar os segredos |
> | Ação | Microsoft. BingMaps/mapApis/listSingleSignOnToken/Action | Ler o token de autorização de logon único para o recurso |
> | Ação | Microsoft. BingMaps/mapApis/Read | Operação de leitura |
> | Ação | Microsoft. BingMaps/mapApis/regenerateKey/Action | Regenera a chave |
> | Ação | Microsoft. BingMaps/mapApis/Write | Operação de gravação |
> | Ação | Microsoft. BingMaps/operações/leitura | Descrição da operação. |

## <a name="microsoftblockchain"></a>Microsoft. Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Blockchain/blockchainMembers/Delete | Exclui um membro Blockchain existente. |
> | Ação | Microsoft. Blockchain/blockchainMembers/listApiKeys/Action | Obtém ou lista as chaves de API de membro Blockchain existentes. |
> | Ação | Microsoft. Blockchain/blockchainMembers/Read | Obtém ou lista os membros Blockchain existentes. |
> | Dataaction | Microsoft. Blockchain/blockchainMembers/transactionNodes/Connect/Action | Conecta-se a um nó de transação de membro Blockchain. |
> | Ação | Microsoft. Blockchain/blockchainMembers/transactionNodes/Delete | Exclui um nó de transação de membro Blockchain existente. |
> | Ação | Microsoft. Blockchain/blockchainMembers/transactionNodes/listApiKeys/Action | Obtém ou lista as chaves de API do nó de transação de membro Blockchain existente. |
> | Ação | Microsoft. Blockchain/blockchainMembers/transactionNodes/Read | Obtém ou lista os nós de transação de membro Blockchain existentes. |
> | Ação | Microsoft. Blockchain/blockchainMembers/transactionNodes/Write | Cria ou atualiza um nó de transação de membro Blockchain. |
> | Ação | Microsoft. Blockchain/blockchainMembers/Write | Cria ou atualiza um membro Blockchain. |
> | Ação | Microsoft. Blockchain/Locations/blockchainMemberOperationResults/Read | Obtém os resultados da operação de membros do Blockchain. |
> | Ação | Microsoft. Blockchain/Locations/checkNameAvailability/Action | Verifica se o nome do recurso é válido e não está em uso. |
> | Ação | Microsoft. Blockchain/operações/leitura | Listar todas as operações no provedor de recursos do Microsoft Blockchain. |
> | Ação | Microsoft. Blockchain/registro/ação | Registra a assinatura para o provedor de recursos Blockchain. |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Blueprint/blueprintAssignments/assignmentOperations/Read | Ler todos os artefatos do Blueprint |
> | Ação | Microsoft. Blueprint/blueprintAssignments/Delete | Excluir todos os artefatos do Blueprint |
> | Ação | Microsoft. Blueprint/blueprintAssignments/Read | Ler todos os artefatos do Blueprint |
> | Ação | Microsoft. Blueprint/blueprintAssignments/whoisblueprint/ação | Obtenha a ID de objeto da entidade de serviço de plantas do Azure. |
> | Ação | Microsoft. Blueprint/blueprintAssignments/Write | Criar ou atualizar artefatos de Blueprint |
> | Ação | Microsoft. Blueprint/plantas/artefatos/excluir | Excluir todos os artefatos do Blueprint |
> | Ação | Microsoft. Blueprint/plantas/artefatos/leitura | Ler todos os artefatos do Blueprint |
> | Ação | Microsoft. Blueprint/plantas/artefatos/gravação | Criar ou atualizar artefatos de Blueprint |
> | Ação | Microsoft. Blueprint/plantas/exclusão | Excluir qualquer Blueprint |
> | Ação | Microsoft. Blueprint/plantas/leitura | Ler planos gráficos |
> | Ação | Microsoft. Blueprint/plantas/versões/artefatos/leitura | Ler todos os artefatos do Blueprint |
> | Ação | Microsoft. Blueprint/plantas/versões/exclusão | Excluir qualquer Blueprint |
> | Ação | Microsoft. Blueprint/plantas/versões/leitura | Ler planos gráficos |
> | Ação | Microsoft. Blueprint/plantas/versões/gravação | Criar ou atualizar planos gráficos |
> | Ação | Microsoft. Blueprint/plantas/gravação | Criar ou atualizar planos gráficos |
> | Ação | Microsoft. Blueprint/registro/ação | Registra o provedor de recursos de plantas do Azure |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. BotService/botServices/Channels/Delete | Excluir um serviço de bot |
> | Ação | Microsoft. BotService/botServices/canais/leitura | Ler um serviço de bot |
> | Ação | Microsoft. BotService/botServices/Channels/Write | Escrever um serviço de bot |
> | Ação | Microsoft. BotService/botServices/Connections/Delete | Excluir um serviço de bot |
> | Ação | Microsoft. BotService/botServices/Connections/Read | Ler um serviço de bot |
> | Ação | Microsoft. BotService/botServices/Connections/Write | Escrever um serviço de bot |
> | Ação | Microsoft. BotService/botServices/Delete | Excluir um serviço de bot |
> | Ação | Microsoft. BotService/botServices/Read | Ler um serviço de bot |
> | Ação | Microsoft. BotService/botServices/Write | Escrever um serviço de bot |
> | Ação | Microsoft. BotService/Locations/operationresults/Read | Ler o status de uma operação assíncrona |
> | Ação | Microsoft. BotService/operações/leitura | Ler as operações para todos os tipos de recursos |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. cache/checknameavailability/Action | Verifica se um nome está disponível para uso com um novo cache Redis |
> | Ação | Microsoft. cache/Locations/operationresults/Read | Obtém o resultado de uma operação de execução longa para a qual o cabeçalho ' local ' foi retornado anteriormente para o cliente |
> | Ação | Microsoft. cache/operações/leitura | Lista as operações às quais o provedor ' Microsoft. cache ' oferece suporte. |
> | Ação | Microsoft. cache/Redis/Delete | Excluir todo o cache Redis |
> | Ação | Microsoft. cache/Redis/exportar/ação | Exportar dados do Redis para BLOBs de armazenamento prefixados no formato especificado |
> | Ação | Microsoft. cache/Redis/firewallRules/Delete | Excluir regras de firewall de IP de um cache Redis |
> | Ação | Microsoft. cache/Redis/firewallRules/Read | Obter as regras de firewall de IP de um cache Redis |
> | Ação | Microsoft. cache/Redis/firewallRules/Write | Editar as regras de firewall de IP de um cache Redis |
> | Ação | Microsoft. cache/Redis/forceReboot/Action | Forçar a reinicialização de uma instância de cache, potencialmente com perda de dados. |
> | Ação | Microsoft. cache/Redis/importação/ação | Importar dados de um formato especificado de vários BLOBs para o Redis |
> | Ação | Microsoft. cache/Redis/LinkedServers/Delete | Excluir o servidor vinculado de um cache Redis |
> | Ação | Microsoft. cache/Redis/LinkedServers/Read | Obter servidores vinculados associados a um cache Redis. |
> | Ação | Microsoft. cache/Redis/LinkedServers/Write | Adicionar um servidor vinculado a um cache Redis |
> | Ação | Microsoft. cache/Redis/listKeys/Action | Exibir o valor das chaves de acesso do cache Redis no portal de gerenciamento |
> | Ação | Microsoft. cache/Redis/listUpgradeNotifications/Read | Liste as notificações de atualização mais recentes para o locatário de cache. |
> | Ação | Microsoft. cache/Redis/metricDefinitions/Read | Obter as métricas disponíveis para um cache Redis |
> | Ação | Microsoft. cache/Redis/patchSchedules/Delete | Excluir o agendamento de patch de um cache Redis |
> | Ação | Microsoft. cache/Redis/patchSchedules/Read | Obter a agenda de aplicação de patch de um cache Redis |
> | Ação | Microsoft. cache/Redis/patchSchedules/Write | Modificar a agenda de aplicação de patch de um cache Redis |
> | Ação | Microsoft. cache/Redis/Read | Exibir as configurações e a configuração do cache Redis no portal de gerenciamento |
> | Ação | Microsoft. cache/Redis/regenerateKey/Action | Alterar o valor das chaves de acesso do cache Redis no portal de gerenciamento |
> | Ação | Microsoft. cache/Redis/Start/Action | Inicie uma instância de cache. |
> | Ação | Microsoft. cache/Redis/Stop/Action | Interrompa uma instância de cache. |
> | Ação | Microsoft. cache/Redis/Write | Modificar as configurações e a configuração do cache Redis no portal de gerenciamento |
> | Ação | Microsoft. cache/registro/ação | Registra o provedor de recursos ' Microsoft. cache ' com uma assinatura |
> | Ação | Microsoft. cache/cancelar registro/ação | Cancela o registro do provedor de recursos ' Microsoft. cache ' com uma assinatura |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Capacity/appliedreservations/Read | Ler todas as reservas |
> | Ação | Microsoft. Capacity/calculateexchange/Action | Computa a quantidade e o preço do Exchange de novos erros de compra e de política de retorno. |
> | Ação | Microsoft. Capacity/CalculatePrice/Action | Calcular qualquer preço de reserva |
> | Ação | Microsoft. capacidade/catálogos/leitura | Ler catálogo de reserva |
> | Ação | Microsoft. Capacity/checkoffers/Action | Verificar as ofertas de assinatura |
> | Ação | Microsoft. Capacity/checkscopes/Action | Verificar qualquer assinatura |
> | Ação | Microsoft. Capacity/commercialreservationorders/Read | Obter ordens de reserva criadas em qualquer locatário |
> | Ação | Microsoft. Capacity/Exchange/Action | Trocar qualquer reserva |
> | Ação | Microsoft. Capacity/Operations/Read | Ler qualquer operação |
> | Ação | Microsoft. Capacity/registro/ação | Registra o provedor de recursos de capacidade e habilita a criação de recursos de capacidade. |
> | Ação | Microsoft. Capacity/reservationorders/Action | Atualizar qualquer reserva |
> | Ação | Microsoft. Capacity/reservationorders/availablescopes/Action | Localizar qualquer escopo disponível |
> | Ação | Microsoft. Capacity/reservationorders/calculaterefund/Action | Computa a quantidade de restituição e o preço de novos erros de compra e de política de retorno. |
> | Ação | Microsoft. Capacity/reservationorders/Delete | Excluir qualquer reserva |
> | Ação | Microsoft. Capacity/reservationorders/mesclagem/ação | Mesclar qualquer reserva |
> | Ação | Microsoft. Capacity/reservationorders/Read | Ler todas as reservas |
> | Ação | Microsoft. Capacity/reservationorders/reservas/Action | Atualizar qualquer reserva |
> | Ação | Microsoft. Capacity/reservationorders/reservas/availablescopes/Action | Localizar qualquer escopo disponível |
> | Ação | Microsoft. Capacity/reservationorders/reservas/Delete | Excluir qualquer reserva |
> | Ação | Microsoft. Capacity/reservationorders/reservas/Read | Ler todas as reservas |
> | Ação | Microsoft. Capacity/reservationorders/reservas/revisões/leitura | Ler todas as reservas |
> | Ação | Microsoft. Capacity/reservationorders/reservas/Write | Criar qualquer reserva |
> | Ação | Microsoft. Capacity/reservationorders/retorno/ação | Retornar qualquer reserva |
> | Ação | Microsoft. Capacity/reservationorders/divisão/ação | Dividir qualquer reserva |
> | Ação | Microsoft. Capacity/reservationorders/permuta/ação | Alternar qualquer reserva |
> | Ação | Microsoft. Capacity/reservationorders/Write | Criar qualquer reserva |
> | Ação | Microsoft. Capacity/locatários/registro/ação | Registrar qualquer locatário |
> | Ação | Microsoft. Capacity/cancelamento/registro/ação | Cancelar o registro de qualquer locatário |
> | Ação | Microsoft. Capacity/validatereservationorder/Action | Validar qualquer reserva |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. CDN/CheckNameAvailability/Action |  |
> | Ação | Microsoft. CDN/CheckResourceUsage/Action |  |
> | Ação | Microsoft. CDN/edgenodes/Delete |  |
> | Ação | Microsoft. CDN/edgenodes/Read |  |
> | Ação | Microsoft. CDN/edgenodes/Write |  |
> | Ação | Microsoft. CDN/operationresults/Delete |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/CheckResourceUsage/Action |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/Delete |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/CheckResourceUsage/Action |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/Delete |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/Action |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/Action |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/Read |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/customdomainresults/Write |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/Delete |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/carga/ação |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/Delete |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/Read |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/Write |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/limpeza/ação |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/Read |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/Start/Action |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/Stop/Action |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/ValidateCustomDomain/Action |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/endpointresults/Write |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/GenerateSsoUri/Action |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/GetSupportedOptimizationTypes/Action |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/Read |  |
> | Ação | Microsoft. CDN/operationresults/profileresults/Write |  |
> | Ação | Microsoft. CDN/operationresults/Read |  |
> | Ação | Microsoft. CDN/operationresults/Write |  |
> | Ação | Microsoft. CDN/Operations/Read |  |
> | Ação | Microsoft. CDN/Profiles/CheckResourceUsage/Action |  |
> | Ação | Microsoft. CDN/Profiles/Delete |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/CheckResourceUsage/ação |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/customdomains/excluir |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/customdomains/DisableCustomHttps/ação |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/customdomains/EnableCustomHttps/ação |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/customdomains/leitura |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/customdomains/gravação |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/excluir |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/carga/ação |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/origens/excluir |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/origens/leitura |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/origens/gravação |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/limpeza/ação |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/leitura |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/iniciar/ação |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/parar/ação |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/ValidateCustomDomain/ação |  |
> | Ação | Microsoft. CDN/perfis/pontos de extremidade/gravação |  |
> | Ação | Microsoft. CDN/Profiles/GenerateSsoUri/Action |  |
> | Ação | Microsoft. CDN/Profiles/GetSupportedOptimizationTypes/Action |  |
> | Ação | Microsoft. CDN/Profiles/Read |  |
> | Ação | Microsoft. CDN/Profiles/Write |  |
> | Ação | Microsoft. CDN/registro/ação | Registra a assinatura para o provedor de recursos da CDN e habilita a criação de perfis CDN. |
> | Ação | Microsoft. CDN/ValidateProbe/Action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. CertificateRegistration/certificateOrders/certificados/excluir | Excluir um certificado existente |
> | Ação | Microsoft. CertificateRegistration/certificateOrders/certificados/ler | Obter a lista de certificados |
> | Ação | Microsoft. CertificateRegistration/certificateOrders/certificados/gravação | Adicionar um novo certificado ou atualizar um existente |
> | Ação | Microsoft. CertificateRegistration/certificateOrders/Delete | Excluir um AppServiceCertificate existente |
> | Ação | Microsoft. CertificateRegistration/certificateOrders/Read | Obter a lista de CertificateOrders |
> | Ação | Microsoft. CertificateRegistration/certificateOrders/reemissão/ação | Reemitir um certificateorder existente |
> | Ação | Microsoft. CertificateRegistration/certificateOrders/renovar/ação | Renovar um certificateorder existente |
> | Ação | Microsoft. CertificateRegistration/certificateOrders/resendEmail/Action | Reenviar o email do certificado |
> | Ação | Microsoft. CertificateRegistration/certificateOrders/resendRequestEmails/Action | Reenviar emails de solicitação para outro endereço de email |
> | Ação | Microsoft. CertificateRegistration/certificateOrders/resendRequestEmails/Action | Recuperar o selo do site para um Certificado do Serviço de Aplicativo emitido |
> | Ação | Microsoft. CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Recuperar a lista de ações de certificado |
> | Ação | Microsoft. CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Recuperar histórico de email de certificado |
> | Ação | Microsoft. CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Verificar a propriedade do domínio |
> | Ação | Microsoft. CertificateRegistration/certificateOrders/Write | Adicionar um novo certificateOrder ou atualizar um existente |
> | Ação | Microsoft. CertificateRegistration/operações/leitura | Listar todas as operações do registro de certificado do serviço de aplicativo |
> | Ação | Microsoft. CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Provisionar entidade de serviço para entidade de segurança do aplicativo de serviço |
> | Ação | Microsoft. CertificateRegistration/registro/ação | Registrar o provedor de recursos de certificados da Microsoft para a assinatura |
> | Ação | Microsoft. CertificateRegistration/validateCertificateRegistrationInformation/Action | Validar objeto de compra de certificado sem enviá-lo |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ClassicCompute/Capabilities/leitura | Mostra os recursos |
> | Ação | Microsoft. ClassicCompute/checkDomainNameAvailability/Action | Verifica a disponibilidade de um determinado nome de domínio. |
> | Ação | Microsoft. ClassicCompute/checkDomainNameAvailability/Read | Obtém a disponibilidade de um determinado nome de domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/ativo/gravação | Define o nome de domínio ativo. |
> | Ação | Microsoft. ClassicCompute/nomedodomínio/availabilitySets/Read | Mostre o conjunto de disponibilidade para o recurso. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/recursos/leitura | Mostra os recursos de nome de domínio |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/excluir | Remova os nomes de domínio dos recursos. |
> | Ação | Microsoft. ClassicCompute/nomedodomínio/deploymentslots/Read | Mostra os slots de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/deploymentslots/funções/leitura | Obter função no slot de implantação do nome de domínio |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/deploymentslots/Roles/roleinstances/Read | Obter a instância de função para a função no slot de implantação do nome de domínio |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/deploymentslots/State/Read | Obtenha o estado do slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/deploymentslots/State/Write | Adicione o estado do slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/deploymentslots/upgradedomain/leitura | Obter o domínio de atualização para o slot de implantação no nome de domínio |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/deploymentslots/upgradedomain/Write | Atualizar o domínio de atualização para o slot de implantação no nome de domínio |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/deploymentslots/Write | Cria ou atualiza a implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/extensões/excluir | Remova as extensões de nome de domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/extensões/operationStatuses/leitura | Lê o status da operação para as extensões de nomes de domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/extensões/leitura | Retorna as extensões de nome de domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/extensões/gravação | Adicione as extensões de nome de domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/internalLoadBalancers/Delete | Remova um novo balanceamento de carga interno. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/internalLoadBalancers/operationStatuses/leitura | Lê o status da operação dos balanceadores de carga internos de nomes de domínio. |
> | Ação | Microsoft. ClassicCompute/nomedodomínio/internalLoadBalancers/Read | Obtém os balanceadores de carga internos. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/internalLoadBalancers/Write | Cria um novo balanceamento de carga interno. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/loadBalancedEndpointSets/operationStatuses/leitura | Lê o status da operação dos conjuntos de pontos de extremidade com balanceamento de carga dos nomes de domínio. |
> | Ação | Microsoft. ClassicCompute/nomedodomínio/loadBalancedEndpointSets/Read | Obter os conjuntos de pontos de extremidade com balanceamento de carga. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/loadBalancedEndpointSets/Write | Adicione o conjunto de pontos de extremidade com balanceamento de carga. |
> | Ação | Microsoft. ClassicCompute/nomedodomínio/operationstatuses/Read | Obter o status da operação do nome de domínio. |
> | Ação | Microsoft. ClassicCompute/nomedodomínio/operationStatuses/Read | Lê o status da operação para as extensões de nomes de domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/leitura | Retornar os nomes de domínio para os recursos. |
> | Ação | Microsoft. ClassicCompute/DomainNames/iscertificates/Delete | Exclua os certificados de serviço usados. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/certificados/operationStatuses/leitura | Lê o status da operação para os certificados de serviço de nomes de domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/certificados/não lidos | Retorna os certificados de serviço usados. |
> | Ação | Microsoft. ClassicCompute/DomainNames/certificados/gravação | Adicione ou modifique os certificados de serviço usados. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/abortMigration/ação | Anula a migração de um slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/commitMigration/ação | Confirma a migração de um slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/excluir | Exclui um determinado slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/operationStatuses/leitura | Lê o status da operação para os slots de nomes de domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/prepareMigration/ação | Prepara a migração de um slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/leitura | Mostra os slots de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/extensionReferences/Delete | Remova a referência de extensão para a função de slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/extensionReferences/operationStatuses/leitura | Lê o status da operação para as referências de extensão de funções de Slots de nomes de domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/extensionReferences/leitura | Retorna a referência de extensão para a função do slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/extensionReferences/gravação | Adicione ou modifique a referência de extensão para a função do slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/metricdefinitions/leitura | Obtenha a definição de métrica de função para o nome de domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/métricas/leitura | Obtenha a métrica de função para o nome de domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/operationstatuses/leitura | Obtenha o status da operação para a função de slot de nomes de domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/provedores/Microsoft. insights/diagnosticSettings/Read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/provedores/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/provedores/Microsoft. insights/metricDefinitions/Read | Obtém as definições de métricas. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/leitura | Obtenha a função do slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/roleInstances/downloadremotedesktopconnectionfile/Action | Baixa o arquivo de conexão de área de trabalho remota para a instância de função na função de slot de nome de domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/roleInstances/operationStatuses/leitura | Obtém o status da operação da instância de função na função de slot de nomes de domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/roleInstances/leitura | Obter a instância de função. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/roleInstances/Rebuild/ação | Recria a instância de função. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/roleInstances/refazer imagem/ação | Recria a imagem da instância de função. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/roleInstances/Restart/Action | Reinicia as instâncias de função. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/SKUs/leitura | Obtenha a SKU da função para o slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/funções/gravação | Adicione a função para o slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/início/ação | Inicia um slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/estado/início/gravação | Altera o estado do slot de implantação para parado. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/estado/parar/gravar | Altera o estado do slot de implantação para iniciado. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/parada/ação | Suspende o slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/upgradeDomain/gravação | Passo atualizar o domínio. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/validateMigration/ação | Valida a migração de um slot de implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/Slots/gravação | Cria ou atualiza a implantação. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/troca/ação | Permuta o slot de preparo para o slot de produção. |
> | Ação | Microsoft. ClassicCompute/nome_do_domínio/gravação | Adicione ou modifique os nomes de domínio dos recursos. |
> | Ação | Microsoft. ClassicCompute/moveSubscriptionResources/Action | Mova todos os recursos clássicos para uma assinatura diferente. |
> | Ação | Microsoft. ClassicCompute/operatingSystemFamilies/Read | Lista as famílias de sistema operacional convidado disponíveis em Microsoft Azure e também lista as versões de sistema operacional disponíveis para cada família. |
> | Ação | Microsoft. ClassicCompute/operatingSystems/Read | Lista as versões do sistema operacional convidado que estão disponíveis no momento no Microsoft Azure. |
> | Ação | Microsoft. ClassicCompute/operações/leitura | Obtém a lista de operações. |
> | Ação | Microsoft. ClassicCompute/operationStatuses/Read | Lê o status da operação para o recurso. |
> | Ação | Microsoft. ClassicCompute/cotas/leitura | Obtenha a cota da assinatura. |
> | Ação | Microsoft. ClassicCompute/registro/ação | Registrar-se na computação clássica |
> | Ação | Microsoft. ClassicCompute/resourceType/SKUs/Read | Obtém a lista de SKUs para os tipos de recursos com suporte. |
> | Ação | Microsoft. ClassicCompute/validateSubscriptionMoveAvailability/Action | Valide a disponibilidade da assinatura para a operação de movimentação clássica. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/Delete | Exclui o grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/Read | Lê o status da operação para os grupos de segurança de rede associados às máquinas virtuais. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/Read | Obtém o grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/Write | Adiciona um grupo de segurança de rede associado à máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/asyncOperations/Read | Obtém as operações assíncronas possíveis |
> | Ação | Microsoft. ClassicCompute/virtualMachines/attachDisk/Action | Anexa um disco de dados a uma máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/captura/ação | Capturar uma máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/Delete | Remove máquinas virtuais. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/detachDisk/Action | Desanexa um disco de dados da máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/DiagnosticSettings/Read | Obter configurações de diagnóstico de máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/discos/ler | Recupera a lista de discos de dados |
> | Ação | Microsoft. ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/Action | Baixa o arquivo RDP para a máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/Extensions/operationStatuses/Read | Lê o status da operação para as extensões de máquinas virtuais. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/Extensions/Read | Obtém a extensão da máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/Extensions/Write | Coloca a extensão da máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/metricdefinitions/Read | Obtenha a definição de métrica da máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/métricas/leitura | Obtém as métricas. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/Delete | Exclui o grupo de segurança de rede associado à interface de rede. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/Read | Lê o status da operação para os grupos de segurança de rede associados às máquinas virtuais. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/Read | Obtém o grupo de segurança de rede associado à interface de rede. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/Write | Adiciona um grupo de segurança de rede associado à interface de rede. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/operationStatuses/Read | Lê o status da operação para as máquinas virtuais. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/performMaintenance/Action | Executa a manutenção na máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. insights/diagnosticSettings/Read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as definições de métricas. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/Read | Recupera a lista de máquinas virtuais. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/reimplantar/ação | Reimplanta a máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/Restart/Action | Reinicia as máquinas virtuais. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/desligamento/ação | Desligue a máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/Start/Action | Inicie a máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/Stop/Action | Interrompe a máquina virtual. |
> | Ação | Microsoft. ClassicCompute/virtualMachines/Write | Adicionar ou modificar máquinas virtuais. |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ClassicNetwork/expressroutecrossconnections/operationstatuses/Read | Obter um status de operação de conexão cruzada de rota expressa. |
> | Ação | Microsoft. ClassicNetwork/expressroutecrossconnections/emparelhamentos/exclusão | Excluir emparelhamento de conexão cruzada de rota expressa. |
> | Ação | Microsoft. ClassicNetwork/expressroutecrossconnections/emparelhamentos/operationstatuses/leitura | Obtenha um status de operação de emparelhamento de conexão cruzada de rota expressa. |
> | Ação | Microsoft. ClassicNetwork/expressroutecrossconnections/emparelhamentos/leitura | Obter emparelhamento de conexão cruzada de rota expressa. |
> | Ação | Microsoft. ClassicNetwork/expressroutecrossconnections/emparelhamentos/gravação | Adicionar emparelhamento de conexão cruzada de rota expressa. |
> | Ação | Microsoft. ClassicNetwork/expressroutecrossconnections/Read | Obter conexões cruzadas de rota expressa. |
> | Ação | Microsoft. ClassicNetwork/expressroutecrossconnections/Write | Adicione conexões cruzadas de rota expressa. |
> | Ação | Microsoft. ClassicNetwork/gatewaySupportedDevices/Read | Recupera a lista de dispositivos com suporte. |
> | Ação | Microsoft. ClassicNetwork/networkSecurityGroups/Delete | Exclui o grupo de segurança de rede. |
> | Ação | Microsoft. ClassicNetwork/networkSecurityGroups/operationStatuses/Read | Lê o status da operação para o grupo de segurança de rede. |
> | Ação | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. insights/diagnosticSettings/Read | Obter as configurações de diagnóstico de grupos de segurança de rede |
> | Ação | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza as configurações de diagnóstico de grupos de segurança de rede; essa operação é complementada pelo provedor de recursos de informações. |
> | Ação | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. insights/logDefinitions/Read | Obter os eventos para o grupo de segurança de rede |
> | Ação | Microsoft. ClassicNetwork/networkSecurityGroups/Read | Obtém o grupo de segurança de rede. |
> | Ação | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Delete | Exclui a regra de segurança. |
> | Ação | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/Read | Lê o status da operação para as regras de segurança do grupo de segurança de rede. |
> | Ação | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Read | Obtém a regra de segurança. |
> | Ação | Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Write | Adiciona ou atualiza uma regra de segurança. |
> | Ação | Microsoft. ClassicNetwork/networkSecurityGroups/Write | Adiciona um novo grupo de segurança de rede. |
> | Ação | Microsoft. ClassicNetwork/operações/leitura | Obter operações de rede clássicas. |
> | Ação | Microsoft. ClassicNetwork/cotas/leitura | Obtenha a cota da assinatura. |
> | Ação | Microsoft. ClassicNetwork/registro/ação | Registrar na rede clássica |
> | Ação | Microsoft. ClassicNetwork/reservedIps/Delete | Excluir um IP reservado. |
> | Ação | Microsoft. ClassicNetwork/reservedIps/junção/ação | Ingressar em um IP reservado |
> | Ação | Microsoft. ClassicNetwork/reservedIps/link/ação | Vincular um IP reservado |
> | Ação | Microsoft. ClassicNetwork/reservedIps/operationStatuses/Read | Lê o status da operação para os IPs reservados. |
> | Ação | Microsoft. ClassicNetwork/reservedIps/Read | Obter os IPs reservados |
> | Ação | Microsoft. ClassicNetwork/reservedIps/Write | Adicionar um novo IP reservado |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/abortMigration/Action | Anula a migração de uma rede virtual |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/Capabilities/ler | Mostra os recursos |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/checkIPAddressAvailability/Action | Verifica a disponibilidade de um determinado endereço IP em uma rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/commitMigration/Action | Confirma a migração de uma rede virtual |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/Delete | Exclui a rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/Delete | Cancela a revogação de um certificado de cliente. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/Read | Ler os certificados de cliente revogados. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/Write | Revoga um certificado de cliente. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/Delete | Exclui o certificado de cliente de gateway de rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/ação | Baixa o certificado por impressão digital. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/ação | Lista o pacote de certificado de gateway de rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/Read | Localize os certificados raiz do cliente. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/Write | Carrega um novo certificado raiz do cliente. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/conexões/conexão/ação | Conecta uma conexão de gateway site a site. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/conexões/desconexão/ação | Desconecta uma conexão de gateway site a site. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/conexões/leitura | Recupera a lista de conexões. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/conexões/teste/ação | Testa uma conexão de gateway site a site. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/excluir | Exclui o gateway de rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/Action | Baixa o script de configuração do dispositivo. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/Action | Baixa o diagnóstico do gateway. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/Action | Recupera a chave de serviço do circuito. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/listPackage/Action | Lista o pacote de gateway de rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/operationStatuses/Read | Lê o status da operação para os gateways de redes virtuais. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/pacotes/leitura | Obtém o pacote de gateway de rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/leitura | Obtém os gateways de rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/startDiagnostics/Action | Inicia o diagnóstico para o gateway de rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/Action | Interrompe o diagnóstico do gateway de rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/gateways/gravação | Adiciona um gateway de rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/junção/ação | Une a rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/operationStatuses/Read | Lê o status da operação para as redes virtuais. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/ponto/ação | Os pares de uma rede virtual com outra rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/prepareMigration/Action | Prepara a migração de uma rede virtual |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/Read | Obter a rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/Delete | Exclui o proxy de emparelhamento de rede virtual remota. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/Read | Obtém o proxy de emparelhamento de rede virtual remota. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/Write | Adiciona ou atualiza o proxy de emparelhamento de rede virtual remota. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/sub-redes/associatedNetworkSecurityGroups/Delete | Exclui o grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/sub-redes/associatedNetworkSecurityGroups/operationStatuses/ler | Lê o status da operação do grupo de segurança de rede associado à sub-rede da rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/sub-redes/associatedNetworkSecurityGroups/ler | Obtém o grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/sub-redes/associatedNetworkSecurityGroups/gravação | Adiciona um grupo de segurança de rede associado à sub-rede. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/validateMigration/Action | Valida a migração de uma rede virtual |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/virtualNetworkPeerings/Read | Obtém o emparelhamento de rede virtual. |
> | Ação | Microsoft. ClassicNetwork/virtualNetworks/Write | Adicione uma nova rede virtual. |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ClassicStorage/Capabilities/leitura | Mostra os recursos |
> | Ação | Microsoft. ClassicStorage/checkStorageAccountAvailability/Action | Verifica a disponibilidade de uma conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/checkStorageAccountAvailability/Read | Obtenha a disponibilidade de uma conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/discos/ler | Retorna o disco da conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/images/operationstatuses/Read | Obtém o status da operação de imagem. |
> | Ação | Microsoft. ClassicStorage/imagens/leitura | Retorna a imagem. |
> | Ação | Microsoft. ClassicStorage/operações/leitura | Obtém operações de armazenamento clássico |
> | Ação | Microsoft. ClassicStorage/osImages/Read | Retorna a imagem do sistema operacional. |
> | Ação | Microsoft. ClassicStorage/osPlatformImages/Read | Obtém a imagem da plataforma do sistema operacional. |
> | Ação | Microsoft. ClassicStorage/publicImages/Read | Obtém a imagem da máquina virtual pública. |
> | Ação | Microsoft. ClassicStorage/cotas/leitura | Obtenha a cota da assinatura. |
> | Ação | Microsoft. ClassicStorage/registro/ação | Registrar no armazenamento clássico |
> | Ação | Microsoft. ClassicStorage/storageAccounts/abortMigration/Action | Anula a migração de uma conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/blobservices/Providers/Microsoft. insights/diagnosticSettings/Read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/blobservices/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/blobservices/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as definições de métricas. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/commitMigration/Action | Confirma a migração de uma conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Delete | Exclua a conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/discos/excluir | Exclui um determinado disco da conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/discos/operationStatuses/leitura | Lê o status da operação para o recurso. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/discos/ler | Retorna o disco da conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/discos/gravação | Adiciona um disco de conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/fileservices/Providers/Microsoft. insights/diagnosticSettings/Read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/fileservices/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/fileservices/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as definições de métricas. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/images/Delete | Exclui uma determinada imagem de conta de armazenamento. Preterido. Use ' Microsoft. ClassicStorage/storageAccounts/vmImages ') |
> | Ação | Microsoft. ClassicStorage/storageAccounts/images/operationstatuses/Read | Retorna o status da operação da imagem da conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/images/Read | Retorna a imagem da conta de armazenamento. Preterido. Use ' Microsoft. ClassicStorage/storageAccounts/vmImages ') |
> | Ação | Microsoft. ClassicStorage/storageAccounts/listKeys/Action | Lista as chaves de acesso para as contas de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/operationStatuses/Read | Lê o status da operação para o recurso. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/osImages/Delete | Exclui uma determinada imagem do sistema operacional da conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/osImages/Read | Retorna a imagem do sistema operacional da conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/osImages/Write | Adiciona uma determinada imagem do sistema operacional da conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/prepareMigration/Action | Prepara a migração de uma conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. insights/diagnosticSettings/Read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as definições de métricas. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/queueservices/Providers/Microsoft. insights/diagnosticSettings/Read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/queueservices/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/queueservices/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as definições de métricas. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Read | Retorne a conta de armazenamento com a conta especificada. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/regenerateKey/Action | Regenera as chaves de acesso existentes para a conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Services/diagnosticSettings/Read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Services/diagnosticSettings/Write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Services/metricDefinitions/Read | Obtém as definições de métricas. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/serviços/métricas/leitura | Obtém as métricas. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/serviços/ler | Obtenha os serviços disponíveis. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/tableservices/Providers/Microsoft. insights/diagnosticSettings/Read | Obtenha as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/tableservices/Providers/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/tableservices/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as definições de métricas. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/validateMigration/Action | Valida a migração de uma conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/vmImages/Delete | Exclui uma determinada imagem de máquina virtual. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/vmImages/operationstatuses/Read | Obtém um determinado status de operação de imagem de máquina virtual. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/vmImages/Read | Retorna a imagem da máquina virtual. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/vmImages/Write | Adiciona uma imagem de máquina virtual específica. |
> | Ação | Microsoft. ClassicStorage/storageAccounts/Write | Adiciona uma nova conta de armazenamento. |
> | Ação | Microsoft. ClassicStorage/vmImages/Read | Lista imagens de máquinas virtuais. |

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitivaservices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Dataaction | Microsoft. Cognitivaservices/contas/autosugestão/pesquisa/ação | Esta operação fornece sugestões para uma determinada consulta ou consulta parcial. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/ComputerVision/Analyze/Action | Esta operação extrai um conjunto avançado de recursos visuais com base no conteúdo da imagem.  |
> | Dataaction | Microsoft. Cognitivaservices/accounts/ComputerVision/areaofinterest/Action | Esta operação retorna uma caixa delimitadora em volta da área mais importante da imagem. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/ComputerVision/describe/Action | Esta operação gera uma descrição de uma imagem no idioma legível por humanos com sentenças completas.<br> A descrição se baseia em uma coleção de marcas de conteúdo, que também são retornadas pela operação.<br>Mais de uma descrição pode ser gerada para cada imagem.<br> As descrições são ordenadas por sua pontuação de confiança.<br>Todas as descrições estão em inglês. |
> | Dataaction | Microsoft. Cognitivaservices/contas/ComputerVision/detectar/ação | Esta operação executa a detecção de objetos na imagem especificada.  |
> | Dataaction | Microsoft. Cognitivaservices/accounts/ComputerVision/generatethumbnail/Action | Esta operação gera uma imagem em miniatura com a largura e a altura especificadas pelo usuário.<br> Por padrão, o serviço analisa a imagem, identifica a região de interesse (ROI) e gera coordenadas de corte inteligente com base no ROI.<br> O corte inteligente ajuda quando você especifica uma taxa de proporção que difere da imagem de entrada |
> | Dataaction | Microsoft. Cognitivaservices/accounts/ComputerVision/Models/Analyze/Action | Essa operação reconhece o conteúdo em uma imagem aplicando um modelo específico de domínio.<br> A lista de modelos específicos de domínio com suporte pelo API da Pesquisa Visual Computacional pode ser recuperada usando a solicitação GET/Models.<br> Atualmente, a API fornece os seguintes modelos específicos de domínio: celebridades, pontos de referência. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/ComputerVision/Models/Read | Essa operação retorna a lista de modelos específicos de domínio com suporte pelo API da Pesquisa Visual Computacional.  Atualmente, a API dá suporte aos seguintes modelos específicos de domínio: celebridade Recognizer, Recognizer de pontos de referência. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/ComputerVision/OCR/Action | O OCR (reconhecimento óptico de caracteres) detecta o texto em uma imagem e extrai os caracteres reconhecidos em um fluxo de caracteres utilizável por máquina.    |
> | Dataaction | Microsoft. Cognitivaservices/accounts/ComputerVision/recognizetext/Action | Use essa interface para obter o resultado de uma operação de Reconhecimento de Texto. Quando você usa a interface Reconhecimento de Texto, a resposta contém um campo chamado "Operation-Location". O campo "operação-local" contém a URL que você deve usar para a operação obter Reconhecimento de Texto resultado da operação. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/ComputerVision/tag/action | Esta operação gera uma lista de palavras ou marcas que são relevantes para o conteúdo da imagem fornecida.<br>O API da Pesquisa Visual Computacional pode retornar marcas com base em objetos, seres de vida, cenário ou ações encontradas em imagens.<br>Diferentemente das categorias, as marcas não são organizadas de acordo com um sistema de classificação hierárquica, mas correspondem ao conteúdo da imagem.<br>As marcas podem conter dicas para evitar ambigüidade ou fornecer contexto, por exemplo, a marca "Cello" pode ser acompanhada pela dica "instrumento musical".<br>Todas as marcas estão em inglês. |
> | Dataaction | Microsoft. Cognitivaservices/contas/ComputerVision/textoperations/Read | Essa interface é usada para obter o resultado da operação de reconhecimento de texto. A URL para essa interface deve ser recuperada do campo <b>"Operation-Location"</b> retornado da interface reconhecimento de texto. |
> | Ação | Microsoft. Cognitivaservices/contas/excluir | Exclui contas de API |
> | Dataaction | Microsoft. Cognitivaservices/accounts/EntitySearch/Search/Action | Obter entidades e colocar os resultados para uma determinada consulta. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/detecção/ação | Detecte faces humanas em uma imagem, retorne retângulos de face e, opcionalmente, com faceIds, pontos de referência e atributos. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/face/facelists/Delete | Excluir uma lista de face especificada. As imagens de face relacionadas na lista de face também serão excluídas. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/face/facelists/persistedfaces/Delete | Excluir uma face de uma lista de rosto por faceListId e persisitedFaceId especificados. A imagem de face relacionada também será excluída. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/face/facelists/persistedfaces/Write | Adicione uma face a uma lista de face especificada, até 1.000 rostos. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/facelists/leitura | Recupere faceListId, Name, userData e faces da lista facial na lista de face. Liste as listas de face ' faceListId, Name e userData. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/facelists/gravação | Crie uma lista de face vazia com faceListId especificado pelo usuário, o nome e um userData opcional. Até 64 listas de rosto são permitidas informações de atualização de uma lista de face, incluindo nome e userData. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/findsimilars/ação | Tabela de face de consulta fornecida para pesquisar os rostos de aparência semelhante de uma matriz faceid, uma lista de face ou uma lista de rosto grande. faceId |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/grupo/ação | Divida os rostos candidatas em grupos com base na semelhança de face. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/identificação/ação | identificação de um-para-muitos para localizar as correspondências mais próximas da pessoa de consulta específica que se encontram de um grupo de pessoas ou grupo de pessoas grandes. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/face/largefacelists/Delete | Excluir uma lista de face grande especificada. As imagens de face relacionadas na lista de rosto grande também serão excluídas. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/face/largefacelists/persistedfaces/Delete | Exclua uma face de uma lista de rosto grande por largeFaceListId e persisitedFaceId especificados. A imagem de face relacionada também será excluída. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largefacelists/persistedfaces/leitura | Recuperar a face persistente em uma lista de face grande por largeFaceListId e persistedFaceId. Liste faces ' persistedFaceId e userData ' em uma lista de face grande especificada. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/face/largefacelists/persistedfaces/Write | Adicione uma face a uma lista de face grande especificada, até 1 milhão rostos. Atualizar o campo userData de uma face especificada em uma lista de rosto grande por seu persistedFaceId. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largefacelists/leitura | Recupere largeFaceListId, Name, userData de uma lista de rosto grande. Listar as informações de grandes listas ' de largeFaceListId, Name e userData. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largefacelists/treinamento/ação | Envie uma tarefa de treinamento de lista de rosto grande. O treinamento é uma etapa crucial que só pode ser usada por uma lista de rosto grande treinada. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largefacelists/treinamento/leitura | Para verificar se o status de treinamento da lista de rosto grande foi concluído ou ainda está em andamento. O treinamento do LargeFaceList é uma operação assíncrona |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largefacelists/gravação | Crie uma lista de face grande vazia com largeFaceListId especificado pelo usuário, o nome e um userData opcional. Atualize as informações de uma lista de rosto grande, incluindo Name e userData. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/face/largepersongroups/Delete | Excluir um grupo de pessoas grandes existente com personGroupId especificado. Os dados persistentes neste grupo de pessoas grandes serão excluídos. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largepersongroups/pessoas/ação | Crie uma nova pessoa em um grupo de pessoas grandes especificado. Para adicionar a face a essa pessoa, entre em contato com |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largepersongroups/pessoas/excluir | Excluir uma pessoa existente de um grupo de pessoas grandes. Todos os dados da pessoa armazenada e imagens de face na entrada Person serão excluídos. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/face/largepersongroups/Persons/persistedfaces/Delete | Excluir uma face de uma pessoa em um grupo de pessoas grandes. Os dados de face e a imagem relacionados a essa entrada de rosto também serão excluídos. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largepersongroups/pessoas/persistedfaces/leitura | Recupere informações de face da pessoa. A face da pessoa persistente é especificada por seu largePersonGroupId, PersonID e persistedFaceId. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largepersongroups/pessoas/persistedfaces/gravação | Adicione uma imagem de face a uma pessoa em um grupo de pessoas grandes para identificação ou verificação facial. Para lidar com a imagem da atualização, o campo userData da face de uma pessoa persiste. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largepersongroups/pessoas/leitura | Recupere o nome e o userData de uma pessoa e o faceIds persistente que representa a imagem de face da pessoa registrada. Listar todas as informações de todas as pessoas no grupo de pessoas grandes especificado, incluindo PERSONID, Name, userData e persistedFaceIds. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largepersongroups/pessoas/gravação | Atualizar nome ou userData de uma pessoa. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largepersongroups/leitura | Recupere as informações de um grupo de pessoas grandes, incluindo seu nome e userData. Essa API retorna a lista de informações de grupo de pessoas grandes todos os grupos de pessoas grandes existentes largePesonGroupId, Name e userData. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largepersongroups/treinamento/ação | Envie uma tarefa de treinamento de grupo de pessoas grandes. O treinamento é uma etapa crucial que só pode ser usada por um grupo treinado de grande pessoa. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largepersongroups/treinamento/leitura | Para verificar se o status de treinamento de grupo de pessoa grande foi concluído ou ainda está em andamento. O treinamento do LargePersonGroup é uma operação assíncrona |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/largepersongroups/gravação | Crie um novo grupo de pessoas grandes com largePersonGroupId especificado pelo usuário, nome e userData opcional. Atualizar nome e userData de um grupo de pessoas grandes existentes. As propriedades permanecerão inalteradas se não estiverem no corpo da solicitação. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/face/persongroups/Delete | Excluir um grupo de pessoas existente com personGroupId especificado. Os dados persistentes neste grupo de pessoas serão excluídos. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/persongroups/pessoas/ação | Criar uma nova pessoa em um grupo de pessoas especificado. Para adicionar a face a essa pessoa, entre em contato com |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/persongroups/pessoas/excluir | Excluir uma pessoa existente de um grupo de pessoas. Todos os dados da pessoa armazenada e imagens de face na entrada Person serão excluídos. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/face/persongroups/Persons/persistedfaces/Delete | Excluir uma face de uma pessoa em um grupo de pessoas. Os dados de face e a imagem relacionados a essa entrada de rosto também serão excluídos. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/persongroups/pessoas/persistedfaces/leitura | Recupere informações de face da pessoa. A face da pessoa persistente é especificada por seu personGroupId, PersonID e persistedFaceId. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/persongroups/pessoas/persistedfaces/gravação | Adicione uma imagem de face a uma pessoa em um grupo de pessoas para identificação ou verificação facial. Para lidar com a imagem de várias atualizações, o campo userData da face de uma pessoa persiste. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/persongroups/pessoas/leitura | Recupere o nome e o userData de uma pessoa e o faceIds persistente que representa a imagem de face da pessoa registrada. Listar as informações de todas as pessoas no grupo de pessoas especificado, incluindo PERSONID, Name, userData e persistedFaceIds de Registered. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/persongroups/pessoas/gravação | Atualizar nome ou userData de uma pessoa. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/persongroups/leitura | Recupere o nome e o userData do grupo de pessoas. Para obter informações de pessoas sob esse grupo de pessoas, use listar pesonGroupId, nome e userData de grupos de pessoas. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/persongroups/treinamento/ação | Envie uma tarefa de treinamento de grupo Person. O treinamento é uma etapa crucial que apenas um grupo de pessoas treinado pode usar. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/persongroups/treinamento/leitura | Para verificar se o status de treinamento do grupo de pessoas foi concluído ou ainda está em andamento. O treinamento do Person é uma operação assíncrona disparada |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/persongroups/gravação | Crie um novo grupo de pessoas com personGroupId especificado, nome e userData fornecido pelo usuário. Atualize o nome e o userData de um grupo de pessoas existente. As propriedades permanecerão inalteradas se não estiverem no corpo da solicitação. |
> | Dataaction | Microsoft. Cognitivaservices/contas/face/verificação/ação | Verifique se duas faces pertencem a uma mesma pessoa ou se uma face pertence a uma pessoa. |
> | Dataaction | Microsoft. Cognitivaservices/contas/ImageSearch/detalhes/ação | Retorna informações sobre uma imagem, como páginas da Web que incluem a imagem. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/ImageSearch/Search/Action | Obter imagens relevantes para uma determinada consulta. |
> | Dataaction | Microsoft. Cognitivaservices/contas/ImageSearch/tendência/ação | Obtenha imagens em tendências no momento. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/ImmersiveReader/getcontentmodelforreader/Action | Cria uma sessão de leitor de imersão |
> | Dataaction | Microsoft. Cognitivaservices/accounts/InkRecognizer/Recognize/Action | Dado um conjunto de dados de traço analisa o conteúdo e gera uma lista de entidades reconhecidas, incluindo texto reconhecido. |
> | Ação | Microsoft. Cognitivaservices/accounts/listKeys/Action | Listar chaves |
> | Dataaction | Microsoft. Cognitivaservices/accounts/LUIS/predição/ação | Obtém a previsão de ponto de extremidade publicado para a consulta fornecida. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/NewsSearch/categorysearch/Action | Retorna notícias para uma categoria fornecida. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/NewsSearch/Search/Action | Obtenha artigos de notícias relevantes para uma determinada consulta. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/NewsSearch/trendingtopics/Action | Obtenha tópicos de tendência identificados pelo Bing. Esses são os mesmos tópicos mostrados na faixa na parte inferior do home page do Bing. |
> | Ação | Microsoft. Cognitivaservices/contas/leitura | Lê as contas de API. |
> | Ação | Microsoft. Cognitivaservices/accounts/regenerateKey/Action | Regenerar chave |
> | Ação | Microsoft. Cognitivaservices/contas/SKUs/ler | Lê os SKUs disponíveis para um recurso existente. |
> | Dataaction | Microsoft. Cognitivaservices/contas/verificação ortográfica/verificação ortográfica/ação | Obtenha o resultado de uma consulta de verificação ortográfica por meio de GET ou POST. |
> | Dataaction | Microsoft. Cognitivaservices/contas/textanalytics/entidades/ação | A API retorna uma lista de entidades conhecidas e entidades nomeadas gerais (\"Person\", \"local\", \"Organization\", etc) em um determinado documento. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/xmlanalytics/keyfrases/ação | A API devolve uma lista de cadeias que denotam os principais pontos de conversa no texto introduzido. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/textanalytics/Languages/Action | A API devolve o idioma detetado e uma pontuação numérica entre 0 e 1. As pontuações próximas de 1 indicam uma certeza de 100% de que o idioma detetado é verdadeiro. É suportado um total de 120 idiomas. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/textanalytics/sentimentos/Action | A API devolve uma pontuação numérica entre 0 e 1.<br>As pontuações próximas de 1 indicam uma opinião positiva, enquanto as pontuações próximas de 0 indicam uma opinião negativa.<br>Uma pontuação de 0,5 indica a falta de sentimentos (por exemplo,<br>uma instrução factoname). |
> | Ação | Microsoft. Cognitivaservices/contas/usos/leitura | Obtenha o uso de cota para um recurso existente. |
> | Dataaction | Microsoft. Cognitivaservices/contas/VideoSearch/detalhes/ação | Obtenha informações sobre um vídeo, como vídeos relacionados. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/VideoSearch/Search/Action | Obter vídeos relevantes para uma determinada consulta. |
> | Dataaction | Microsoft. Cognitivaservices/contas/VideoSearch/tendência/ação | Obtenha vídeos de tendência no momento. |
> | Dataaction | Microsoft. Cognitivaservices/accounts/VisualSearch/Search/Action | Retorna uma lista de marcas relevantes para a imagem fornecida |
> | Dataaction | Microsoft. Cognitivaservices/contas/WebSearch/pesquisa/ação | Obtenha resultados da Web, de imagem, de notícias & vídeos para uma determinada consulta. |
> | Ação | Microsoft. Cognitivaservices/contas/gravação | Grava contas de API. |
> | Ação | Microsoft. Cognitivaservices/checkDomainAvailability/Action | Lê os SKUs disponíveis para uma assinatura. |
> | Ação | Microsoft. Cognitivaservices/Locations/checkSkuAvailability/Action | Lê os SKUs disponíveis para uma assinatura. |
> | Ação | Microsoft. Cognitivaservices/Locations/checkSkuAvailability/Action | Lê os SKUs disponíveis para uma assinatura. |
> | Ação | Microsoft. Cognitivaservices/Locations/deleteVirtualNetworkOrSubnets/Action | Notificação da Microsoft. Network de exclusão de VirtualNetworks ou sub-redes. |
> | Ação | Microsoft. Cognitivaservices/Operations/Read | Listar todas as operações disponíveis |
> | Ação | Microsoft. Cognitivaservices/registro/ação | Registra a assinatura para serviços cognitivas |
> | Ação | Microsoft. Cognitivaservices/registro/ação | Registra a assinatura para serviços cognitivas |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Commerce/RateCard/Read | Retorna dados de oferta, metadados de recurso/medidor e taxas para a assinatura fornecida. |
> | Ação | Microsoft. Commerce/UsageAggregates/Read | Recupera o consumo de Microsoft Azure por uma assinatura. O resultado contém agregações de dados de uso, assinatura e informações relacionadas a recursos, em um intervalo de tempo específico. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Compute/availabilitySets/Delete | Exclui o conjunto de disponibilidade |
> | Ação | Microsoft. Compute/availabilitySets/Read | Obter as propriedades de um conjunto de disponibilidade |
> | Ação | Microsoft. Compute/availabilitySets/vmSizes/Read | Listar os tamanhos disponíveis para criar ou atualizar uma máquina virtual no conjunto de disponibilidade |
> | Ação | Microsoft. Compute/availabilitySets/Write | Cria um novo conjunto de disponibilidade ou atualiza um existente |
> | Ação | Microsoft. Compute/diskEncryptionSets/Delete | Excluir um conjunto de criptografia de disco |
> | Ação | Microsoft. Compute/diskEncryptionSets/Read | Obter as propriedades de um conjunto de criptografia de disco |
> | Ação | Microsoft. Compute/diskEncryptionSets/Write | Criar um novo conjunto de criptografia de disco ou atualizar um existente |
> | Ação | Microsoft. Compute/disks/beginGetAccess/Action | Obter o URI de SAS do disco para acesso ao blob |
> | Ação | Microsoft. Compute/disks/Delete | Exclui o disco |
> | Ação | Microsoft. Compute/disks/endGetAccess/Action | Revogar o URI de SAS do disco |
> | Ação | Microsoft. Compute/disks/Read | Obter as propriedades de um disco |
> | Ação | Microsoft. Compute/disks/Write | Cria um novo disco ou atualiza um existente |
> | Ação | Microsoft. Compute/galerias/excluir | Exclui a Galeria |
> | Ação | Microsoft. Compute/galerias/imagens/excluir | Exclui a imagem da Galeria |
> | Ação | Microsoft. Compute/galerias/imagens/leitura | Obtém as propriedades da imagem da Galeria |
> | Ação | Microsoft. Compute/galerias/imagens/versões/excluir | Exclui a versão da imagem da Galeria |
> | Ação | Microsoft. Compute/galerias/imagens/versões/leitura | Obtém as propriedades da versão da imagem da Galeria |
> | Ação | Microsoft. Compute/galerias/imagens/versões/gravação | Cria uma nova versão da imagem da galeria ou atualiza uma existente |
> | Ação | Microsoft. Compute/galerias/imagens/gravação | Cria uma nova imagem da galeria ou atualiza uma existente |
> | Ação | Microsoft. Compute/galerias/ler | Obter as propriedades da Galeria |
> | Ação | Microsoft. Compute/galerias/gravar | Cria uma nova galeria ou atualiza uma existente |
> | Ação | Microsoft. Compute/hosts/Delete | Exclui o grupo de hosts |
> | Ação | Microsoft. Compute/hosts/hosts/Delete | Exclui o host |
> | Ação | Microsoft. Compute/hosts/hosts/Read | Obter as propriedades de um host |
> | Ação | Microsoft. Compute/hosts/hosts/Write | Cria um novo host ou atualiza um host existente |
> | Ação | Microsoft. Compute/hosts/Read | Obter as propriedades de um grupo de hosts |
> | Ação | Microsoft. Compute/hosts/Write | Cria um novo grupo de hosts ou atualiza um grupo de hosts existente |
> | Ação | Microsoft. Compute/images/Delete | Exclui a imagem |
> | Ação | Microsoft. Compute/images/Read | Obter as propriedades da imagem |
> | Ação | Microsoft. Compute/images/Write | Cria uma nova imagem ou atualiza uma existente |
> | Ação | Microsoft. Compute/Locations/capsOperations/Read | Obtém o status de uma operação de Caps assíncronas |
> | Ação | Microsoft. Compute/Locations/diskOperations/Read | Obtém o status de uma operação de disco assíncrono |
> | Ação | Microsoft. Compute/Locations/logAnalytics/getRequestRateByInterval/Action | Crie logs para mostrar o total de solicitações por intervalo de tempo para auxiliar o diagnóstico de limitação. |
> | Ação | Microsoft. Compute/Locations/logAnalytics/getThrottledRequests/Action | Crie logs para mostrar agregações de solicitações limitadas agrupadas por resourceName, OperationName ou a política de limitação aplicada. |
> | Ação | Microsoft. Compute/Locations/Operations/Read | Obtém o status de uma operação assíncrona |
> | Ação | Microsoft. Compute/Locations/Publishers/artifacttypes/offers/Read | Obter as propriedades de uma oferta de imagem de plataforma |
> | Ação | Microsoft. Compute/Locations/Publishers/artifacttypes/offers/SKUs/Read | Obter as propriedades de uma SKU de imagem de plataforma |
> | Ação | Microsoft. Compute/Locations/Publishers/artifacttypes/offers/SKUs/Versions/Read | Obter as propriedades de uma versão de imagem de plataforma |
> | Ação | Microsoft. Compute/Locations/Publishers/artifacttypes/Types/Read | Obter as propriedades de um tipo VMExtension |
> | Ação | Microsoft. Compute/Locations/Publishers/artifacttypes/Types/Versions/Read | Obter as propriedades de uma versão do VMExtension |
> | Ação | Microsoft. Compute/Locations/Publishers/Read | Obter as propriedades de um Publicador |
> | Ação | Microsoft. Compute/Locations/runCommands/Read | Lista os comandos de execução disponíveis no local |
> | Ação | Microsoft. Compute/localizações/usos/leitura | Obtém os limites de serviço e as quantidades de uso atuais para os recursos de computação da assinatura em um local |
> | Ação | Microsoft. Compute/Locations/vmSizes/Read | Lista os tamanhos de máquina virtual disponíveis em um local |
> | Ação | Microsoft. Compute/Operations/Read | Lista as operações disponíveis no provedor de recursos Microsoft. Compute |
> | Ação | Microsoft. Compute/proximityPlacementGroups/Delete | Exclui o grupo de posicionamento de proximidade |
> | Ação | Microsoft. Compute/proximityPlacementGroups/Read | Obter as propriedades de um grupo de posicionamento de proximidade |
> | Ação | Microsoft. Compute/proximityPlacementGroups/Write | Cria um novo grupo de posicionamento de proximidade ou atualiza um existente |
> | Ação | Microsoft. Compute/registro/ação | Registra a assinatura com o provedor de recursos Microsoft. Compute |
> | Ação | Microsoft. Compute/restorePointCollections/Delete | Exclui a coleção de pontos de restauração e os pontos de restauração contidos |
> | Ação | Microsoft. Compute/restorePointCollections/Read | Obter as propriedades de uma coleção de pontos de restauração |
> | Ação | Microsoft. Compute/restorePointCollections/restorePoints/Delete | Exclui o ponto de restauração |
> | Ação | Microsoft. Compute/restorePointCollections/restorePoints/Read | Obter as propriedades de um ponto de restauração |
> | Ação | Microsoft. Compute/restorePointCollections/restorePoints/retrieveSasUris/Action | Obter as propriedades de um ponto de restauração juntamente com URIs de SAS de BLOB |
> | Ação | Microsoft. Compute/restorePointCollections/restorePoints/Write | Cria um novo ponto de restauração |
> | Ação | Microsoft. Compute/restorePointCollections/Write | Cria uma nova coleção de pontos de restauração ou atualiza uma existente |
> | Ação | Microsoft. Compute/sharedVMImages/Delete | Exclui o SharedVMImage |
> | Ação | Microsoft. Compute/sharedVMImages/Read | Obter as propriedades de um SharedVMImage |
> | Ação | Microsoft. Compute/sharedVMImages/Versions/Delete | Excluir um SharedVMImageVersion |
> | Ação | Microsoft. Compute/sharedVMImages/Versions/Read | Obter as propriedades de um SharedVMImageVersion |
> | Ação | Microsoft. Compute/sharedVMImages/Versions/replicate/Action | Replicar um SharedVMImageVersion para as regiões de destino |
> | Ação | Microsoft. Compute/sharedVMImages/versões/gravação | Criar um novo SharedVMImageVersion ou atualizar um existente |
> | Ação | Microsoft. Compute/sharedVMImages/Write | Cria um novo SharedVMImage ou atualiza um existente |
> | Ação | Microsoft. Compute/SKUs/Read | Obtém a lista de SKUs Microsoft. Compute disponíveis para sua assinatura |
> | Ação | Microsoft. Compute/instantâneos/beginGetAccess/Action | Obter o URI de SAS do instantâneo para acesso ao blob |
> | Ação | Microsoft. Compute/instantâneos/excluir | Excluir um instantâneo |
> | Ação | Microsoft. Compute/instantâneos/endGetAccess/Action | Revogar o URI de SAS do instantâneo |
> | Ação | Microsoft. Compute/instantâneos/leitura | Obter as propriedades de um instantâneo |
> | Ação | Microsoft. Compute/Snapshots/Write | Criar um novo instantâneo ou atualizar um existente |
> | Ação | Microsoft. Compute/cancelar registro/ação | Cancela o registro da assinatura com o provedor de recursos Microsoft. Compute |
> | Ação | Microsoft. Compute/virtualMachines/captura/ação | Captura a máquina virtual copiando discos rígidos virtuais e gera um modelo que pode ser usado para criar máquinas virtuais semelhantes |
> | Ação | Microsoft. Compute/virtualMachines/convertToManagedDisks/Action | Converte os discos baseados em BLOB da máquina virtual em discos gerenciados |
> | Ação | Microsoft. Compute/virtualMachines/desallocate/ação | Desliga a máquina virtual e libera os recursos de computação |
> | Ação | Microsoft. Compute/virtualMachines/Delete | Exclui a máquina virtual |
> | Ação | Microsoft. Compute/virtualMachines/extensões/Delete | Exclui a extensão da máquina virtual |
> | Ação | Microsoft. Compute/virtualMachines/Extensions/Read | Obter as propriedades de uma extensão de máquina virtual |
> | Ação | Microsoft. Compute/virtualMachines/Extensions/Write | Cria uma nova extensão de máquina virtual ou atualiza uma existente |
> | Ação | Microsoft. Compute/virtualMachines/generalizate/ação | Define o estado da máquina virtual como generalizado e prepara a máquina virtual para captura |
> | Ação | Microsoft. Compute/virtualMachines/instanceView/Read | Obtém o status de tempo de execução detalhado da máquina virtual e seus recursos |
> | Dataaction | Microsoft. Compute/virtualMachines/logon/ação | Fazer logon em uma máquina virtual como um usuário normal |
> | Dataaction | Microsoft. Compute/virtualMachines/loginAsAdmin/Action | Fazer logon em uma máquina virtual com privilégios de administrador do Windows ou de usuário raiz do Linux |
> | Ação | Microsoft. Compute/virtualMachines/performMaintenance/Action | Executa a operação de manutenção na VM. |
> | Ação | Microsoft. Compute/virtualMachines/estado desligado/Action | Desliga a máquina virtual. Observe que a máquina virtual continuará a ser cobrada. |
> | Ação | Microsoft. Compute/virtualMachines/Read | Obter as propriedades de uma máquina virtual |
> | Ação | Microsoft. Compute/virtualMachines/reimplantar/ação | Reimplanta a máquina virtual |
> | Ação | Microsoft. Compute/virtualMachines/reimagem/ação | Recria a imagem da máquina virtual que está usando o disco diferencial. |
> | Ação | Microsoft. Compute/virtualMachines/Restart/Action | Reinicia a máquina virtual |
> | Ação | Microsoft. Compute/virtualMachines/ExecutarComando/Action | Executa um script predefinido na máquina virtual |
> | Ação | Microsoft. Compute/virtualMachines/iniciar/ação | Inicia a máquina virtual |
> | Ação | Microsoft. Compute/virtualMachines/vmSizes/Read | Lista os tamanhos disponíveis para os quais a máquina virtual pode ser atualizada |
> | Ação | Microsoft. Compute/virtualMachines/Write | Cria uma nova máquina virtual ou atualiza uma máquina virtual existente |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/desallocate/ação | Desliga e libera os recursos de computação para as instâncias do conjunto de dimensionamento de máquinas virtuais  |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/Delete | Exclui o conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/exclusão/ação | Exclui as instâncias do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/extensões/Delete | Exclui a extensão do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/Extensions/Read | Obter as propriedades de uma extensão de conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/Extensions/Write | Cria uma nova extensão de conjunto de dimensionamento de máquinas virtuais ou atualiza uma existente |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/Action | Oriente manualmente os domínios de atualização de plataforma de um conjunto de dimensionamento de máquinas virtuais do Service Fabric para concluir uma atualização pendente que está paralisada |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/instanceView/Read | Obtém a exibição de instância do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/manualUpgrade/Action | Atualiza manualmente as instâncias para o modelo mais recente do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/networkInterfaces/Read | Obter propriedades de todas as interfaces de rede de um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/osRollingUpgrade/Action | Inicia uma atualização sem interrupção para mover todas as instâncias do conjunto de dimensionamento de máquinas virtuais para a versão mais recente do sistema operacional da imagem de plataforma disponível. |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/osUpgradeHistory/Read | Obter o histórico de atualizações do sistema operacional para um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/performMaintenance/Action | Executa a manutenção planejada nas instâncias do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/estado desligado/Action | Desliga as instâncias do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/publicIPAddresses/Read | Obter propriedades de todos os endereços IP públicos de um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/Read | Obter as propriedades de um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/reimplantar/ação | Reimplantar as instâncias do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/reimagem/ação | Refazer a imagem das instâncias do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/reimageAll/Action | Recria a imagem de todos os discos (disco do so e discos de dados) para as instâncias de um conjunto de dimensionamento de máquinas virtuais  |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/Restart/Action | Reinicia as instâncias do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/rollingUpgrades/cancelamento/ação | Cancela a atualização sem interrupção de um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/rollingUpgrades/Read | Obter o status de atualização sem interrupção mais recente para um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/escala/ação | Verificar se um conjunto de dimensionamento de máquinas virtuais existente pode ser dimensionado/reScale Out para a contagem de instâncias especificada |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/SKUs/Read | Lista os SKUs válidos para um conjunto de dimensionamento de máquinas virtuais existente |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/iniciar/ação | Inicia as instâncias do conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/desallocate/ação | Desliga e libera os recursos de computação para uma máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/Delete | Exclua uma máquina virtual específica em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/instanceView/Read | Recupera a exibição de instância de uma máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/Read | Obtenha as propriedades do endereço IP público criado usando o conjunto de dimensionamento de máquinas virtuais. O conjunto de dimensionamento de máquinas virtuais pode criar no máximo um IP público por ipconfiguration (IP privado) |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/Read | Obter propriedades de uma ou todas as configurações de IP de uma interface de rede criada usando o conjunto de dimensionamento de máquinas virtuais. As configurações de IP representam IPs privados |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/networkInterface/Read | Obter propriedades de uma ou de todas as interfaces de rede de uma máquina virtual criada usando o conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/Action | Executa a manutenção planejada em uma instância de máquina virtual em um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/estado desligado/Action | Desliga uma instância de máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/Read | Recupera as propriedades de uma máquina virtual em um conjunto de dimensionamento de VM |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/reimplantar/ação | Reimplanta uma instância de máquina virtual em um conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/refazer imagem/ação | Recria uma imagem de uma instância de máquina virtual em um conjunto de dimensionamento de máquinas virtuais. |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/reimageAll/Action | Recria a imagem de todos os discos (disco do so e discos de dados) para a instância de máquina virtual em um conjunto de dimensionamento de máquinas virtuais. |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/Restart/Action | Reinicia uma instância de máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/ExecutarComando/Action | Executa um script predefinido em uma instância de máquina virtual em um conjunto de dimensionamento de máquinas virtuais. |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/iniciar/ação | Inicia uma instância de máquina virtual em um conjunto de dimensionamento de VM. |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/Write | Atualiza as propriedades de uma máquina virtual em um conjunto de dimensionamento de VM |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/vmSizes/Read | Listar os tamanhos disponíveis para criar ou atualizar uma máquina virtual no conjunto de dimensionamento de máquinas virtuais |
> | Ação | Microsoft. Compute/virtualMachineScaleSets/Write | Cria um novo conjunto de dimensionamento de máquinas virtuais ou atualiza um existente |

## <a name="microsoftconsumption"></a>Microsoft. consumo

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. consumo/saldos/leitura | Listar o resumo de utilização de um período de cobrança para um grupo de gerenciamento. |
> | Ação | Microsoft. consumo/orçamentos/exclusão | Exclua os orçamentos por uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft. consumo/orçamentos/leitura | Listar os orçamentos por uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft. consumo/orçamentos/gravação | Cria e atualiza os orçamentos por uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft. consumo/encargos/leitura | Listar encargos |
> | Ação | Microsoft. consumo/créditos/leitura | Listar créditos |
> | Ação | Microsoft. consumo/eventos/leitura | Listar eventos |
> | Ação | Microsoft. consumo/previsões/leitura | Listar previsões |
> | Ação | Microsoft. consumo/muitos/lidos | Listar muitos |
> | Ação | Microsoft. consumo/Marketplaces/leitura | Liste os detalhes de uso de recursos do Marketplace para um escopo para assinaturas EA e webdirect. |
> | Ação | Microsoft. consumo/operationresults/leitura | Listar operationresults |
> | Ação | Microsoft. consumo/operações/leitura | Listar todas as operações com suporte pelo provedor de recursos Microsoft. consumo. |
> | Ação | Microsoft. consumo/OperationStatus/leitura | Listar OperationStatus |
> | Ação | Microsoft. consumo/pricesheets/leitura | Listar os dados de Pricesheets para uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft. consumo/registro/ação | Registrar no RP de consumo |
> | Ação | Microsoft. consumo/reservationDetails/leitura | Liste os detalhes de utilização das instâncias reservadas por ordem de reserva ou grupos de gerenciamento. Os dados de detalhes são por instância por nível de dia. |
> | Ação | Microsoft. consumo/reservationRecommendations/leitura | Listar recomendações únicas ou compartilhadas para instâncias reservadas para uma assinatura. |
> | Ação | Microsoft. consumo/reservationSummaries/leitura | Liste o resumo de utilização das instâncias reservadas por ordem de reserva ou grupos de gerenciamento. Os dados de resumo estão em um nível mensal ou diário. |
> | Ação | Microsoft. consumo/reservationTransactions/leitura | Listar o histórico de transações para instâncias reservadas por grupos de gerenciamento. |
> | Ação | Microsoft. consumo/marcas/leitura | Listar marcas para EA e assinaturas. |
> | Ação | Microsoft. consumo/locatários/leitura | Listar locatários |
> | Ação | Microsoft. consumo/locatários/registro/ação | Registre a ação para o escopo do Microsoft. consumo por um locatário. |
> | Ação | Microsoft. consumo/termos/leitura | Listar os termos de uma assinatura ou um grupo de gerenciamento. |
> | Ação | Microsoft. consumo/usageDetails/leitura | Liste os detalhes de uso de um escopo para assinaturas EA e webdirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ContainerInstance/containerGroups/containers/exec/Action | Exec em um contêiner específico. |
> | Ação | Microsoft. ContainerInstance/containerGroups/containers/logs/leitura | Obter logs para um contêiner específico. |
> | Ação | Microsoft. ContainerInstance/containerGroups/Delete | Exclua o grupo de contêineres específico. |
> | Ação | Microsoft. ContainerInstance/containerGroups/operationResults/Read | Obter resultado da operação assíncrona |
> | Ação | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. insights/diagnosticSettings/Read | Obtém a configuração de diagnóstico para o grupo de contêineres. |
> | Ação | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o grupo de contêineres. |
> | Ação | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as métricas disponíveis para o grupo de contêineres. |
> | Ação | Microsoft. ContainerInstance/containerGroups/Read | Obter todos os grupos de contêineres. |
> | Ação | Microsoft. ContainerInstance/containerGroups/Restart/Action | Reinicia um grupo de contêineres específico. |
> | Ação | Microsoft. ContainerInstance/containerGroups/Start/Action | Inicia um grupo de contêineres específico. |
> | Ação | Microsoft. ContainerInstance/containerGroups/Stop/Action | Para um grupo de contêineres específico. Os recursos de computação serão desalocados e a cobrança será interrompida. |
> | Ação | Microsoft. ContainerInstance/containerGroups/Write | Criar ou atualizar um grupo de contêineres específico. |
> | Ação | Microsoft. ContainerInstance/Locations/cachedImages/Read | Obtém as imagens armazenadas em cache para a assinatura em uma região. |
> | Ação | Microsoft. ContainerInstance/locais/recursos/leitura | Obtenha os recursos para uma região. |
> | Ação | Microsoft. ContainerInstance/Locations/deleteVirtualNetworkOrSubnets/Action | Notifica o Microsoft. ContainerInstance que a rede virtual ou sub-rede está sendo excluída. |
> | Ação | Microsoft. ContainerInstance/localizações/operações/leitura | Listar as operações para o serviço de instância de contêiner do Azure. |
> | Ação | Microsoft. ContainerInstance/localizações/usos/leitura | Obtenha o uso de uma região específica. |
> | Ação | Microsoft. ContainerInstance/operações/leitura | Listar as operações para o serviço de instância de contêiner do Azure. |
> | Ação | Microsoft. ContainerInstance/registro/ação | Registra a assinatura para o provedor de recursos da instância de contêiner e habilita a criação de grupos de contêineres. |
> | Ação | Microsoft. ContainerInstance/serviceassociationlinks/Delete | Exclua o link de associação de serviço criado pelo provedor de recursos da instância de contêiner do Azure em uma sub-rede. |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ContainerRegistry/checkNameAvailability/Read | Verifica se o nome do registro de contêiner está disponível para uso. |
> | Ação | Microsoft. ContainerRegistry/Locations/deleteVirtualNetworkOrSubnets/Action | Notifica o Microsoft. ContainerRegistry que a rede virtual ou sub-rede está sendo excluída |
> | Ação | Microsoft. ContainerRegistry/Locations/operationResults/Read | Obtém um resultado de operação assíncrona |
> | Ação | Microsoft. ContainerRegistry/operações/leitura | Lista todas as operações da API REST do registro de contêiner do Azure disponíveis |
> | Ação | Microsoft. ContainerRegistry/registro/ação | Registra a assinatura para o provedor de recursos de registro de contêiner e habilita a criação de registros de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/artefatos/excluir | Exclua o artefato em um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/Builds/cancelamento/ação | Cancela uma compilação existente. |
> | Ação | Microsoft. ContainerRegistry/registros/Builds/getLogLink/Action | Obtém um link para baixar os logs de compilação. |
> | Ação | Microsoft. ContainerRegistry/registros/compilações/leitura | Obtém as propriedades da compilação especificada ou lista todas as compilações para o registro de contêiner especificado. |
> | Ação | Microsoft. ContainerRegistry/registros/compilações/gravação | Atualiza uma compilação para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft. ContainerRegistry/registros/buildTasks/Delete | Exclui uma tarefa de compilação de um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/buildTasks/listSourceRepositoryProperties/Action | Lista as propriedades de controle do código-fonte para uma tarefa de compilação. |
> | Ação | Microsoft. ContainerRegistry/registros/buildTasks/ler | Obtém as propriedades da tarefa de compilação especificada ou lista todas as tarefas de compilação para o registro de contêiner especificado. |
> | Ação | Microsoft. ContainerRegistry/registros/buildTasks/etapas/excluir | Exclui uma etapa de compilação de uma tarefa de compilação. |
> | Ação | Microsoft. ContainerRegistry/registros/buildTasks/Steps/listBuildArguments/Action | Lista os argumentos de compilação para uma etapa de compilação, incluindo os argumentos secretos. |
> | Ação | Microsoft. ContainerRegistry/registros/buildTasks/etapas/ler | Obtém as propriedades da etapa de compilação especificada ou lista todas as etapas de compilação para a tarefa de compilação especificada. |
> | Ação | Microsoft. ContainerRegistry/registros/buildTasks/etapas/gravação | Cria ou atualiza uma etapa de compilação para uma tarefa de compilação com os parâmetros especificados. |
> | Ação | Microsoft. ContainerRegistry/registros/buildTasks/gravação | Cria ou atualiza uma tarefa de compilação para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft. ContainerRegistry/registros/excluir | Exclui um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/eventGridFilters/Delete | Exclui um filtro de grade de eventos de um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/eventGridFilters/ler | Obtém as propriedades do filtro de grade de eventos especificado ou lista todos os filtros de grade de eventos para o registro de contêiner especificado. |
> | Ação | Microsoft. ContainerRegistry/registros/eventGridFilters/gravação | Cria ou atualiza um filtro de grade de eventos para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft. ContainerRegistry/registros/getBuildSourceUploadUrl/Action | Obtém o local de carregamento para o usuário poder carregar a origem. |
> | Ação | Microsoft. ContainerRegistry/registros/importImage/Action | Importe a imagem para o registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft. ContainerRegistry/registros/listBuildSourceUploadUrl/Action | Obter local da URL de carregamento de origem para um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/listCredentials/Action | Lista as credenciais de logon para o registro de contêiner especificado. |
> | Ação | Microsoft. ContainerRegistry/registros/listPolicies/ler | Lista as políticas para o registro de contêiner especificado |
> | Ação | Microsoft. ContainerRegistry/registros/listUsages/ler | Lista os usos de cota para o registro de contêiner especificado. |
> | Ação | Microsoft. ContainerRegistry/registros/metadados/leitura | Obtém os metadados de um repositório específico para um registro de contêiner |
> | Ação | Microsoft. ContainerRegistry/registros/metadados/gravação | Atualiza os metadados de um repositório para um registro de contêiner |
> | Ação | Microsoft. ContainerRegistry/registros/operationStatuses/ler | Obter um status de operação assíncrona do registro |
> | Ação | Microsoft. ContainerRegistry/registros/pull/leitura | Efetuar pull ou obter imagens de um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/Push/gravação | Enviar por Push ou gravar imagens em um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/quarantineRead/ler | Efetuar pull ou obter imagens em quarentena do registro de contêiner |
> | Ação | Microsoft. ContainerRegistry/registros/quarantineWrite/gravação | Gravar/modificar o estado de quarentena de imagens em quarentena |
> | Ação | Microsoft. ContainerRegistry/registros/queueBuild/Action | Cria uma nova compilação com base nos parâmetros de solicitação e adiciona-a à fila de compilação. |
> | Ação | Microsoft. ContainerRegistry/registros/leitura | Obtém as propriedades do registro de contêiner especificado ou lista todos os registros de contêiner no grupo de recursos ou na assinatura especificada. |
> | Ação | Microsoft. ContainerRegistry/registros/regenerateCredential/Action | Regenera uma das credenciais de logon para o registro de contêiner especificado. |
> | Ação | Microsoft. ContainerRegistry/registros/replicações/excluir | Exclui uma replicação de um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/replicações/operationStatuses/leitura | Obter um status de operação assíncrona de replicação |
> | Ação | Microsoft. ContainerRegistry/registros/replicações/leitura | Obtém as propriedades da replicação especificada ou lista todas as replicações para o registro de contêiner especificado. |
> | Ação | Microsoft. ContainerRegistry/registros/replicações/gravação | Cria ou atualiza uma replicação para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft. ContainerRegistry/registros/execuções/cancelamento/ação | Cancelar uma execução existente. |
> | Ação | Microsoft. ContainerRegistry/registros/execuções/listLogSasUrl/ação | Obtém a URL de SAS do log para uma execução. |
> | Ação | Microsoft. ContainerRegistry/registros/execuções/leitura | Obtém as propriedades de uma execução em relação a um registro de contêiner ou a uma lista de execuções. |
> | Ação | Microsoft. ContainerRegistry/registros/execuções/gravação | Atualiza uma execução. |
> | Ação | Microsoft. ContainerRegistry/registros/scheduleRun/Action | Agendar uma execução em um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/sinal/gravação | Metadados de confiança de conteúdo de push/pull para um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/tarefas/excluir | Exclui uma tarefa para um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/tarefas/listDetails/ação | Lista todos os detalhes de uma tarefa para um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/tarefas/leitura | Obtém uma tarefa para um registro de contêiner ou lista todas as tarefas. |
> | Ação | Microsoft. ContainerRegistry/registros/tarefas/gravação | Cria ou atualiza uma tarefa para um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/updatePolicies/gravação | Atualiza as políticas para o registro de contêiner especificado |
> | Ação | Microsoft. ContainerRegistry/registros/WebHooks/excluir | Exclui um webhook de um registro de contêiner. |
> | Ação | Microsoft. ContainerRegistry/registros/WebHooks/getCallbackConfig/ação | Obtém a configuração de URI de serviço e cabeçalhos personalizados para o webhook. |
> | Ação | Microsoft. ContainerRegistry/registros/WebHooks/listEvents/ação | Lista os eventos recentes para o webhook especificado. |
> | Ação | Microsoft. ContainerRegistry/registros/WebHooks/operationStatuses/leitura | Obter um status de operação assíncrona de webhook |
> | Ação | Microsoft. ContainerRegistry/registros/WebHooks/ping/ação | Dispara um evento de ping a ser enviado ao webhook. |
> | Ação | Microsoft. ContainerRegistry/registros/WebHooks/leitura | Obtém as propriedades do webhook especificado ou lista todos os WebHooks do registro de contêiner especificado. |
> | Ação | Microsoft. ContainerRegistry/registros/WebHooks/gravação | Cria ou atualiza um webhook para um registro de contêiner com os parâmetros especificados. |
> | Ação | Microsoft. ContainerRegistry/registros/gravação | Cria ou atualiza um registro de contêiner com os parâmetros especificados. |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ContainerService/contêinerservices/excluir | Exclui um serviço de contêiner |
> | Ação | Microsoft. ContainerService/contêinerservices/leitura | Obter um serviço de contêiner |
> | Ação | Microsoft. ContainerService/containerservices/Write | Cria um novo serviço de contêiner ou atualiza um existente |
> | Ação | Microsoft. ContainerService/Locations/operationresults/Read | Obtém o status de um resultado de operação assíncrona |
> | Ação | Microsoft. ContainerService/localizações/operações/leitura | Obtém o status de uma operação assíncrona |
> | Ação | Microsoft. ContainerService/Locations/Orchestrator/Read | Lista os orquestradores com suporte |
> | Ação | Microsoft. ContainerService/managedClusters/accessProfiles/listCredential/Action | Obter um perfil de acesso de cluster gerenciado por nome de função usando a credencial de lista |
> | Ação | Microsoft. ContainerService/managedClusters/accessProfiles/Read | Obter um perfil de acesso de cluster gerenciado por nome de função |
> | Ação | Microsoft. ContainerService/managedClusters/agentPools/Delete | Exclui um pool de agentes |
> | Ação | Microsoft. ContainerService/managedClusters/agentPools/Read | Obter um pool de agentes |
> | Ação | Microsoft. ContainerService/managedClusters/agentPools/upgradeProfiles/Read | Obtém o perfil de atualização do pool de agentes |
> | Ação | Microsoft. ContainerService/managedClusters/agentPools/Write | Cria um novo pool de agentes ou atualiza um existente |
> | Ação | Microsoft. ContainerService/managedClusters/Delete | Excluir um cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/detectores/leitura | Obter o detector de cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/diagnosticsstate/Read | Obtém o estado de diagnóstico do cluster |
> | Ação | Microsoft. ContainerService/managedClusters/listClusterAdminCredential/Action | Listar a credencial clusterAdmin de um cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/listClusterUserCredential/Action | Listar a credencial clusterUser de um cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/privateEndpointConnectionsApproval/Action | Determina se o usuário tem permissão para aprovar uma conexão de ponto de extremidade privada |
> | Ação | Microsoft. ContainerService/managedClusters/Read | Obter um cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/resetAADProfile/Action | Redefinir o perfil do AAD de um cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/resetServicePrincipalProfile/Action | Redefinir o perfil de entidade de serviço de um cluster gerenciado |
> | Ação | Microsoft. ContainerService/managedClusters/upgradeProfiles/Read | Obtém o perfil de atualização do cluster |
> | Ação | Microsoft. ContainerService/managedClusters/Write | Cria um novo cluster gerenciado ou atualiza um existente |
> | Ação | Microsoft. ContainerService/openShiftClusters/Delete | Excluir um cluster de turno aberto |
> | Ação | Microsoft. ContainerService/openShiftClusters/Read | Obter um cluster de turno aberto |
> | Ação | Microsoft. ContainerService/openShiftClusters/Write | Cria um novo cluster de turnos aberto ou atualiza um existente |
> | Ação | Microsoft. ContainerService/openShiftManagedClusters/Delete | Excluir um cluster gerenciado de turno aberto |
> | Ação | Microsoft. ContainerService/openShiftManagedClusters/Read | Obter um cluster gerenciado de turno aberto |
> | Ação | Microsoft. ContainerService/openShiftManagedClusters/Write | Cria um novo cluster gerenciado de turno aberto ou atualiza um existente |
> | Ação | Microsoft. ContainerService/operações/leitura | Lista as operações disponíveis no provedor de recursos Microsoft. ContainerService |
> | Ação | Microsoft. ContainerService/registro/ação | Registra a assinatura com o provedor de recursos Microsoft. ContainerService |
> | Ação | Microsoft. ContainerService/cancelar registro/ação | Cancela o registro da assinatura com o provedor de recursos Microsoft. ContainerService |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ContentModerator/Applications/Delete | Operação de exclusão |
> | Ação | Microsoft. ContentModerator/Applications/listSecrets/Action | Listar segredos |
> | Ação | Microsoft. ContentModerator/Applications/listSingleSignOnToken/Action | Ler tokens de logon único |
> | Ação | Microsoft. ContentModerator/aplicativos/ler | Operação de leitura |
> | Ação | Microsoft. ContentModerator/Applications/Write | Operação de gravação |
> | Ação | Microsoft. ContentModerator/Applications/Write | Operação de gravação |
> | Ação | Microsoft. ContentModerator/listCommunicationPreference/Action | Listar preferência de comunicação |
> | Ação | Microsoft. ContentModerator/operações/leitura | operações de leitura |
> | Ação | Microsoft. ContentModerator/updateCommunicationPreference/Action | Atualizar preferência de comunicação |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. CostManagement/cloudConnectors/Delete | Exclua o Cloudconnector for especificado. |
> | Ação | Microsoft. CostManagement/cloudConnectors/Read | Liste o cloudConnectors para o usuário autenticado. |
> | Ação | Microsoft. CostManagement/cloudConnectors/Write | Criar ou atualizar o Cloudconnector for especificado. |
> | Ação | Microsoft. CostManagement/Dimensions/Read | Lista todas as dimensões com suporte por um escopo. |
> | Ação | Microsoft. CostManagement/Exports/Action | Execute a exportação especificada. |
> | Ação | Microsoft. CostManagement/Exports/Delete | Excluir a exportação especificada. |
> | Ação | Microsoft. CostManagement/Exports/Read | Liste as exportações por escopo. |
> | Ação | Microsoft. CostManagement/exportações/execução/ação | Execute exportações. |
> | Ação | Microsoft. CostManagement/Exports/Write | Criar ou atualizar a exportação especificada. |
> | Ação | Microsoft. CostManagement/externalBillingAccounts/externalSubscriptions/Read | Liste o externalSubscriptions em um externalBillingAccount para o usuário autenticado. |
> | Ação | Microsoft. CostManagement/externalBillingAccounts/Read | Liste o externalBillingAccounts para o usuário autenticado. |
> | Ação | Microsoft. CostManagement/externalSubscriptions/Read | Liste o externalSubscriptions para o usuário autenticado. |
> | Ação | Microsoft. CostManagement/externalSubscriptions/Write | Atualizar grupo de gerenciamento associado de externalSubscription |
> | Ação | Microsoft. CostManagement/consulta/ação | Consultar dados de uso por um escopo. |
> | Ação | Microsoft. CostManagement/consulta/leitura | Consultar dados de uso por um escopo. |
> | Ação | Microsoft. CostManagement/registro/ação | Registrar a ação para o escopo do Microsoft. CostManagement por uma assinatura. |
> | Ação | Microsoft. CostManagement/Reports/Action | Agendar relatórios sobre dados de uso por um escopo. |
> | Ação | Microsoft. CostManagement/Reports/Read | Agendar relatórios sobre dados de uso por um escopo. |
> | Ação | Microsoft. CostManagement/locatários/registro/ação | Registrar a ação para o escopo do Microsoft. CostManagement por um locatário. |

## <a name="microsoftdatabox"></a>Microsoft. Data Box

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Data Box/Jobs/bookShipmentPickUp/ação | Permite agendar uma retirada para remessas de retorno. |
> | Ação | Microsoft. Data Box/Jobs/cancelamento/ação | Cancela um pedido em andamento. |
> | Ação | Microsoft. Data Box/Jobs/excluir | Excluir os pedidos |
> | Ação | Microsoft. Data Box/Jobs/listCredentials/ação | Lista as credenciais não criptografadas relacionadas ao pedido. |
> | Ação | Microsoft. Data Box/Jobs/ler | Listar ou obter os pedidos |
> | Ação | Microsoft. Data Box/Jobs/Write | Criar ou atualizar os pedidos |
> | Ação | Microsoft. Data Box/Locations/availableSkus/Action | Esse método retorna a lista de SKUs disponíveis. |
> | Ação | Microsoft. Data Box/Locations/availableSkus/Read | Listar ou obter os SKUs disponíveis |
> | Ação | Microsoft. Data Box/Locations/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. Data Box/Locations/regionConfiguration/Action | Esse método retorna as configurações para a região. |
> | Ação | Microsoft. Data Box/Locations/validateAddress/Action | Valida o endereço de envio e fornece endereços alternativos, se houver. |
> | Ação | Microsoft. Data Box/Locations/validateInputs/Action | Esse método faz todo o tipo de validações. |
> | Ação | Microsoft. Data Box/operações/leitura | Listar ou obter as operações |
> | Ação | Microsoft. Data Box/registro/ação | Registrar provedor Microsoft. Data Box |
> | Ação | Microsoft. Data Box/subscriptions/resourceGroups/moveResources/Action | Esse método executa a movimentação de recursos. |
> | Ação | Microsoft. Data Box/subscriptions/resourceGroups/validateMoveResources/Action | Esse método valida se a movimentação de recursos é permitida ou não. |
> | Ação | Microsoft. Data Box/cancelar registro/ação | Provedor de cancelamento de registro Microsoft. Data Box |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/alertas/ler | Listar ou obter os alertas |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/alertas/ler | Listar ou obter os alertas |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Delete | Exclui os agendamentos de largura de banda |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/Read | Listar ou obter o resultado da operação |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Read | Listar ou obter as agendas de largura de banda |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Read | Listar ou obter as agendas de largura de banda |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Write | Cria ou atualiza os agendamentos de largura de banda |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Delete | Exclui os dispositivos Data Box Edge |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/Action | Baixar atualizações no dispositivo |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/Action | Recupera informações estendidas do recurso |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/installUpdates/Action | Instalar atualizações no dispositivo |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Jobs/ler | Listar ou obter os trabalhos |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/networkSettings/Read | Listar ou obter as configurações de rede do dispositivo |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/nós/leitura | Listar ou obter os nós |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/operationResults/Read | Listar ou obter o resultado da operação |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/operationsStatus/Read | Listar ou obter o status da operação |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Delete | Exclui os pedidos |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/operationResults/Read | Listar ou obter o resultado da operação |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Read | Listar ou obter os pedidos |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Read | Listar ou obter os pedidos |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Write | Criar ou atualizar os pedidos |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Listar ou obter os dispositivos Data Box Edge |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Listar ou obter os dispositivos Data Box Edge |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Listar ou obter os dispositivos Data Box Edge |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Roles/Delete | Exclui as funções |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Roles/operationResults/Read | Listar ou obter o resultado da operação |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Roles/Read | Listar ou obter as funções |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Roles/Read | Listar ou obter as funções |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/funções/gravação | Cria ou atualiza as funções |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/Action | Verificar se há atualizações |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/Read | Listar ou obter o resultado da operação |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/securitySettings/UPDATE/Action | Atualizar configurações de segurança |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/compartilhamentos/excluir | Exclui os compartilhamentos |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/compartilhamentos/operationResults/leitura | Listar ou obter o resultado da operação |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/compartilhamentos/leitura | Listar ou obter os compartilhamentos |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/compartilhamentos/leitura | Listar ou obter os compartilhamentos |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/compartilhamentos/atualização/ação | Atualizar os metadados de compartilhamento com os dados da nuvem |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/compartilhamentos/gravação | Criar ou atualizar os compartilhamentos |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Delete | Exclui as credenciais da conta de armazenamento |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/Read | Listar ou obter o resultado da operação |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Read | Listar ou obter as credenciais da conta de armazenamento |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Read | Listar ou obter as credenciais da conta de armazenamento |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Write | Criar ou atualizar as credenciais da conta de armazenamento |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggers/Delete | Exclui os gatilhos |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/Read | Listar ou obter o resultado da operação |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggers/Read | Listar ou obter os gatilhos |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggers/Read | Listar ou obter os gatilhos |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggers/Write | Criar ou atualizar os gatilhos |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/updateSummary/Read | Listar ou obter o resumo de atualizações |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/Action | Carregar certificado para registro de dispositivo |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/usuários/excluir | Exclui os usuários do compartilhamento |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/operationResults/Read | Listar ou obter o resultado da operação |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/usuários/ler | Listar ou obter os usuários do compartilhamento |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/usuários/ler | Listar ou obter os usuários do compartilhamento |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/usuários/gravação | Criar ou atualizar os usuários do compartilhamento |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Write | Cria ou atualiza os dispositivos Data Box Edge |
> | Ação | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Write | Cria ou atualiza os dispositivos Data Box Edge |

## <a name="microsoftdatabricks"></a>Microsoft. databricks

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. databricks/Locations/getNetworkPolicies/Action | Obter políticas de intenção de rede para uma sub-rede com base no local usado pelo NRP |
> | Ação | Microsoft. databricks/registro/ação | Registre-se no databricks. |
> | Ação | Microsoft. databricks/espaços de trabalho/excluir | Remove um espaço de trabalho do databricks. |
> | Ação | Microsoft. databricks/espaços de trabalho/provedores/Microsoft. insights/diagnosticSettings/Read | Define as configurações de diagnóstico disponíveis para o espaço de trabalho do databricks |
> | Ação | Microsoft. databricks/espaços de trabalho/provedores/Microsoft. insights/diagnosticSettings/Write | Adicionar ou modificar as configurações de diagnóstico. |
> | Ação | Microsoft. databricks/espaços de trabalho/provedores/Microsoft. insights/logDefinitions/Read | Obtém as definições de log disponíveis para o espaço de trabalho do databricks |
> | Ação | Microsoft. databricks/espaços de trabalho/leitura | Recupera uma lista de espaços de trabalho do databricks. |
> | Ação | Microsoft. databricks/espaços de trabalho/gravação | Cria um espaço de trabalho do databricks. |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. datacatalog/catálogos/excluir | Excluir recurso de catálogos para o provedor de recursos do catálogo de dados. |
> | Ação | Microsoft. datacatalog/catálogos/leitura | Ler recurso de catálogos para provedor de recursos do catálogo de dados. |
> | Ação | Microsoft. datacatalog/catálogos/gravação | Gravar recurso de catálogos para o provedor de recursos do catálogo de dados. |
> | Ação | Microsoft. datacatalog/checkNameAvailability/Read | Verifique a disponibilidade do nome do catálogo para o provedor de recursos do catálogo de dados. |
> | Ação | Microsoft. datacatalog/datacatalogs/excluir | Excluir recurso datacatalog do provedor de recursos do catálogo de dados. |
> | Ação | Microsoft. datacatalog/datacatalogs/leitura | Ler recurso datacatalog para provedor de recursos do catálogo de dados. |
> | Ação | Microsoft. datacatalog/datacatalogs/Write | Gravar recurso do datacatalog para o provedor de recursos do catálogo de dados. |
> | Ação | Microsoft. datacatalog/Operations/Read | Lê todas as operações disponíveis no provedor de recursos do catálogo de dados. |
> | Ação | Microsoft. datacatalog/registro/ação | Registrar a assinatura para o provedor de recursos do catálogo de dados |
> | Ação | Microsoft. datacatalog/cancelar registro/ação | Cancelar o registro da assinatura do provedor de recursos do catálogo de dados |

## <a name="microsoftdatafactory"></a>Microsoft. datafactory

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. datafactory/checkazuredatafactorynameavailability/Read | Verifica se o nome do Data Factory está disponível para uso. |
> | Ação | Microsoft. datafactory/datafactorings/activitywindows/Read | Lê as janelas de atividades no Data Factory com os parâmetros especificados. |
> | Ação | Microsoft. datafactory/datafactorings/datapipelines/atividades/activitywindows/Read | Lê as janelas de atividades para a atividade pipeline com os parâmetros especificados. |
> | Ação | Microsoft. datafactory/datafactorings/datapipelines/activitywindows/Read | Lê as janelas de atividades para o pipeline com os parâmetros especificados. |
> | Ação | Microsoft. datafactory/datafactorings/datapipelines/excluir | Exclui qualquer pipeline. |
> | Ação | Microsoft. datafactory/datafactorings/datapipelines/pausa/ação | Pausa qualquer pipeline. |
> | Ação | Microsoft. datafactory/datafactorings/datapipelines/leitura | Lê qualquer pipeline. |
> | Ação | Microsoft. datafactory/datafactorings/datapipelines/retomar/ação | Retoma qualquer pipeline. |
> | Ação | Microsoft. datafactory/datafactorings/datapipelines/atualização/ação | Atualiza qualquer pipeline. |
> | Ação | Microsoft. datafactory/datafactorings/datapipelines/gravação | Cria ou atualiza qualquer pipeline. |
> | Ação | Microsoft. datafactory/datafactorings/DataSets/activitywindows/Read | Lê as janelas de atividades para o conjunto de um com os parâmetros especificados. |
> | Ação | Microsoft. datafactory/datafactorings/DataSets/Delete | Exclui qualquer conjunto de banco de qualquer. |
> | Ação | Microsoft. datafactory/datafactorings/conjuntos de valores/leitura | Lê qualquer DataSet. |
> | Ação | Microsoft. datafactory/datafactorings/DataSets/sliceruns/Read | Lê a execução da fatia de dados para o determinado DataSet com a hora de início fornecida. |
> | Ação | Microsoft. datafactory/datafactorings/conjuntos de valores/fatias/leitura | Obtém as fatias de dados no período determinado. |
> | Ação | Microsoft. datafactory/datafactorings/DataSets/Slices/Write | Atualize o status da fatia de dados. |
> | Ação | Microsoft. datafactory/datafactorings/conjuntos de valores/gravação | Cria ou atualiza qualquer conjunto de todos os conjuntos de qualquer. |
> | Ação | Microsoft. datafactory/datafactorings/excluir | Exclui o Data Factory. |
> | Ação | Microsoft. datafactory/datafactorings/gateways/ConnectionInfo/Action | Lê as informações de conexão para qualquer gateway. |
> | Ação | Microsoft. datafactory/datafactorings/gateways/excluir | Exclui qualquer gateway. |
> | Ação | Microsoft. datafactory/datafactorings/gateways/listauthkeys/ação | Lista as chaves de autenticação para qualquer gateway. |
> | Ação | Microsoft. datafactory/datafactorings/gateways/leitura | Lê qualquer gateway. |
> | Ação | Microsoft. datafactory/datafactorings/gateways/regenerateauthkey/ação | Regenera as chaves de autenticação para qualquer gateway. |
> | Ação | Microsoft. datafactory/datafactorings/gateways/gravação | Cria ou atualiza qualquer gateway. |
> | Ação | Microsoft. datafactory/datafactorings/linkedservices/Delete | Exclui qualquer serviço vinculado. |
> | Ação | Microsoft. datafactory/datafactorings/linkedservices/Read | Lê qualquer serviço vinculado. |
> | Ação | Microsoft. datafactory/datafactorings/linkedservices/Write | Cria ou atualiza qualquer serviço vinculado. |
> | Ação | Microsoft. datafactory/datafactorings/leitura | Lê o Data Factory. |
> | Ação | Microsoft. datafactory/datafactorings/execuções/loginfo/Read | Lê um URI de SAS para um contêiner de BLOB que contém os logs. |
> | Ação | Microsoft. datafactory/datafactorings/Tables/Delete | Exclui qualquer conjunto de banco de qualquer. |
> | Ação | Microsoft. datafactory/datafactorings/tabelas/leitura | Lê qualquer DataSet. |
> | Ação | Microsoft. datafactory/datafactorings/tabelas/gravação | Cria ou atualiza qualquer conjunto de todos os conjuntos de qualquer. |
> | Ação | Microsoft. datafactory/datafactorings/gravação | Cria ou atualiza o Data Factory. |
> | Ação | Microsoft. datafactory/fábricas/cancelpipelinerun/Action | Cancela a execução de pipeline especificada pela ID de execução. |
> | Ação | Microsoft. datafactory/fábricas/cancelSandboxPipelineRun/Action | Cancela uma execução de depuração para o pipeline. |
> | Ação | Microsoft. datafactory/fábricas/createdataflowdebugsession/Action | Cria uma sessão de depuração de fluxo de dados. |
> | Ação | Microsoft. datafactory/fábricas/Dataflows/excluir | Exclui o fluxo de dados. |
> | Ação | Microsoft. datafactory/fábricas/Dataflows/leitura | Lê o fluxo de dados. |
> | Ação | Microsoft. datafactory/fábricas/Dataflows/Write | Criar ou atualizar o fluxo de dados |
> | Ação | Microsoft. datafactory/factories/DataSets/Delete | Exclui qualquer conjunto de banco de qualquer. |
> | Ação | Microsoft. datafactory/fábricas/conjuntos de valores/leitura | Lê qualquer DataSet. |
> | Ação | Microsoft. datafactory/fábricas/conjuntos de valores/gravação | Cria ou atualiza qualquer conjunto de todos os conjuntos de qualquer. |
> | Ação | Microsoft. datafactory/fábricas/debugpipelineruns/cancelamento/ação | Cancela uma execução de depuração para o pipeline. |
> | Ação | Microsoft. datafactory/fábricas/Delete | Exclui Data Factory. |
> | Ação | Microsoft. datafactory/fábricas/deletedataflowdebugsession/Action | Exclui uma sessão de depuração de fluxo de dados. |
> | Ação | Microsoft. datafactory/fábricas/getDataPlaneAccess/Action | Obtém acesso ao serviço de plano de dataplane do ADF. |
> | Ação | Microsoft. datafactory/fábricas/getDataPlaneAccess/Read | Lê o acesso ao serviço de plano de dataplane do ADF. |
> | Ação | Ação de Microsoft. datafactory/fábricas/getfeaturevalue | Obter o valor do recurso de controle de exposição para o local específico. |
> | Ação | Microsoft. datafactory/fábricas/getfeaturevalue/Read | Lê o valor do recurso de controle de exposição para o local específico. |
> | Ação | Microsoft. datafactory/fábricas/getGitHubAccessToken/Action | Obtém o token de acesso do GitHub. |
> | Ação | Microsoft. datafactory/fábricas/integrationruntimes/Delete | Exclui qualquer Integration Runtime. |
> | Ação | Microsoft. datafactory/factories/integrationruntimes/GetConnectionInfo/Read | Lê Integration Runtime informações de conexão. |
> | Ação | Microsoft. datafactory/factories/integrationruntimes/getObjectMetadata/Action | Obtenha os metadados do SSIS Integration Runtime para o Integration Runtime especificado. |
> | Ação | Microsoft. datafactory/factories/integrationruntimes/GetStatus/Read | Lê Integration Runtime status. |
> | Ação | Microsoft. datafactory/factories/integrationruntimes/linkedIntegrationRuntime/Action | Criar referência de Integration Runtime vinculada no Integration Runtime compartilhado especificado. |
> | Ação | Microsoft. datafactory/factories/integrationruntimes/listauthkeys/Read | Lista as chaves de autenticação para qualquer Integration Runtime. |
> | Ação | Microsoft. datafactory/factories/integrationruntimes/monitoringdata/Read | Obtém os dados de monitoramento para qualquer Integration Runtime. |
> | Ação | Microsoft. datafactory/factories/integrationruntimes/Nodes/Delete | Exclui o nó para o Integration Runtime especificado. |
> | Ação | Microsoft. datafactory/factories/integrationruntimes/Nodes/ipAddress/Action | Retorna o endereço IP para o nó especificado do Integration Runtime. |
> | Ação | Microsoft. datafactory/fábricas/integrationruntimes/nós/leitura | Lê o nó para o Integration Runtime especificado. |
> | Ação | Microsoft. datafactory/fábricas/integrationruntimes/nós/gravação | Atualiza um nó de Integration Runtime hospedado internamente. |
> | Ação | Microsoft. datafactory/fábricas/integrationruntimes/Read | Lê qualquer Integration Runtime. |
> | Ação | Microsoft. datafactory/factories/integrationruntimes/refreshObjectMetadata/Action | Atualize os metadados do SSIS Integration Runtime para o Integration Runtime especificado. |
> | Ação | Microsoft. datafactory/factories/integrationruntimes/regenerateauthkey/Action | Regenera as chaves de autenticação para o Integration Runtime especificado. |
> | Ação | Microsoft. datafactory/factories/integrationruntimes/removelinks/Action | Remove as referências Integration Runtime vinculadas da Integration Runtime especificada. |
> | Ação | Microsoft. datafactory/fábricas/integrationruntimes/Start/Action | Inicia qualquer Integration Runtime. |
> | Ação | Microsoft. datafactory/fábricas/integrationruntimes/Stop/Action | Interrompe qualquer Integration Runtime. |
> | Ação | Microsoft. datafactory/factories/integrationruntimes/SYNCCREDENTIALS/Action | Sincroniza as credenciais para o Integration Runtime especificado. |
> | Ação | Microsoft. datafactory/factories/integrationruntimes/upgrade/Action | Atualiza o Integration Runtime especificado. |
> | Ação | Microsoft. datafactory/factories/integrationruntimes/Write | Cria ou atualiza qualquer Integration Runtime. |
> | Ação | Microsoft. datafactory/factories/linkedservices/Delete | Exclui o serviço vinculado. |
> | Ação | Microsoft. datafactory/fábricas/linkedservices/leitura | Lê o serviço vinculado. |
> | Ação | Microsoft. datafactory/factories/linkedservices/Write | Criar ou atualizar serviço vinculado |
> | Ação | Microsoft. datafactory/factories/pipelineruns/activityruns/Read | Lê as execuções de atividade para a ID de execução de pipeline especificada. |
> | Ação | Microsoft. datafactory/fábricas/pipelineruns/cancelamento/ação | Cancela a execução de pipeline especificada pela ID de execução. |
> | Ação | Microsoft. datafactory/factories/pipelineruns/queryactivityruns/Action | Consulta as execuções de atividade para a ID de execução de pipeline especificada. |
> | Ação | Microsoft. datafactory/factories/pipelineruns/queryactivityruns/Read | Lê o resultado das execuções de atividade de consulta para a ID de execução de pipeline especificada. |
> | Ação | Microsoft. datafactory/fábricas/pipelineruns/Read | Lê as execuções de pipeline. |
> | Ação | Microsoft. datafactory/fábricas/pipelines/createrun/ação | Cria uma execução para o pipeline. |
> | Ação | Microsoft. datafactory/fábricas/pipelines/excluir | Exclui o pipeline. |
> | Ação | Microsoft. datafactory/fábricas/pipelines/pipelineruns/activityruns/progresso/leitura | Obtém o progresso das execuções de atividade. |
> | Ação | Microsoft. datafactory/factories/pipelines/pipelineruns/Read | Lê a execução do pipeline. |
> | Ação | Microsoft. datafactory/fábricas/pipelines/leitura | Lê o pipeline. |
> | Ação | Microsoft. datafactory/fábricas/pipelines/área restrita/ação | Cria um ambiente de execução de depuração para o pipeline. |
> | Ação | Microsoft. datafactory/fábricas/pipelines/área restrita/criação/ação | Cria um ambiente de execução de depuração para o pipeline. |
> | Ação | Microsoft. datafactory/fábricas/pipelines/área restrita/execução/ação | Cria uma execução de depuração para o pipeline. |
> | Ação | Microsoft. datafactory/fábricas/pipelines/gravação | Criar ou atualizar o pipeline |
> | Ação | Microsoft. datafactory/fábricas/querydebugpipelineruns/Action | Consulta as execuções do pipeline de depuração. |
> | Ação | Microsoft. datafactory/fábricas/querypipelineruns/Action | Consulta as execuções de pipeline. |
> | Ação | Microsoft. datafactory/fábricas/querypipelineruns/Read | Lê o resultado das execuções do pipeline de consulta. |
> | Ação | Microsoft. datafactory/fábricas/querytriggerruns/Action | Consulta o gatilho é executado. |
> | Ação | Microsoft. datafactory/fábricas/querytriggerruns/Read | Lê o resultado de execuções de gatilho. |
> | Ação | Microsoft. datafactory/fábricas/leitura | Lê Data Factory. |
> | Ação | Microsoft. datafactory/factories/sandboxpipelineruns/sandboxActivityRuns/Read | Obtém as informações de execução de depuração para a atividade. |
> | Ação | Microsoft. datafactory/fábricas/startdataflowdebugsession/Action | Inicia uma sessão de depuração de fluxo de dados. |
> | Ação | Microsoft. datafactory/fábricas/submitDataFlowForPreview/Action | Enviar o fluxo de dados para obter a visualização de dados usando uma sessão de depuração. |
> | Ação | Microsoft. datafactory/fábricas/triggerruns/Read | Lê as execuções do gatilho. |
> | Ação | Microsoft. datafactory/factories/triggers/Delete | Exclui qualquer gatilho. |
> | Ação | Microsoft. datafactory/factories/triggers/Read | Lê qualquer gatilho. |
> | Ação | Microsoft. datafactory/factories/triggers/Start/Action | Inicia qualquer gatilho. |
> | Ação | Microsoft. datafactory/factories/triggers/Stop/Action | Interrompe qualquer gatilho. |
> | Ação | Microsoft. datafactory/factories/triggers/triggerruns/Read | Lê as execuções do gatilho. |
> | Ação | Microsoft. datafactory/factories/triggers/Write | Cria ou atualiza qualquer gatilho. |
> | Ação | Microsoft. datafactory/fábricas/gravação | Criar ou atualizar Data Factory |
> | Ação | Microsoft. datafactory/Locations/configureFactoryRepo/Action | Configura o repositório para a fábrica. |
> | Ação | Microsoft. datafactory/Locations/getfeatureaction/Action | Obter o valor do recurso de controle de exposição para o local específico. |
> | Ação | Microsoft. datafactory/Locations/getfeaturevalue/Read | Lê o valor do recurso de controle de exposição para o local específico. |
> | Ação | Microsoft. datafactory/operações/leitura | Lê todas as operações no provedor do Microsoft Data Factory. |
> | Ação | Microsoft. datafactory/registro/ação | Registra a assinatura para o provedor de recursos de Data Factory. |
> | Ação | Microsoft. datafactory/cancelamento/registro/ação | Cancela o registro da assinatura para o provedor de recursos de Data Factory. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. DataLakeAnalytics/accounts/computePolicies/Delete | Excluir uma política de computação. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/computePolicies/Read | Obter informações sobre uma política de computação. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/computePolicies/Write | Criar ou atualizar uma política de computação. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Desvincular uma conta do DataLakeStore de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Read | Obter informações sobre uma conta DataLakeStore vinculada de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Criar ou atualizar uma conta DataLakeStore vinculada de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/contas/excluir | Excluir uma conta do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/firewallRules/Delete | Excluir uma regra de firewall. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/firewallRules/Read | Obter informações sobre uma regra de firewall. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/firewallRules/Write | Criar ou atualizar uma regra de firewall. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/operationResults/Read | Obtenha o resultado de uma operação de conta do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/contas/ler | Obter informações sobre uma conta existente do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/storageAccounts/containers/listSasTokens/Action | Listar tokens SAS para contêineres de armazenamento de uma conta de armazenamento vinculada de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/storageAccounts/containers/Read | Obter contêineres de uma conta de armazenamento vinculada de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Delete | Desvincular uma conta de armazenamento de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Read | Obtenha informações sobre uma conta de armazenamento vinculada de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Write | Criar ou atualizar uma conta de armazenamento vinculada de uma conta do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/TakeOwnership/Action | Conceda permissões para cancelar trabalhos enviados por outros usuários. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/transferAnalyticsUnits/Action | Transferir SystemMaxAnalyticsUnits entre contas do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/virtualNetworkRules/Delete | Excluir uma regra de rede virtual. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/virtualNetworkRules/Read | Obter informações sobre uma regra de rede virtual. |
> | Ação | Microsoft. DataLakeAnalytics/accounts/virtualNetworkRules/Write | Criar ou atualizar uma regra de rede virtual. |
> | Ação | Microsoft. DataLakeAnalytics/contas/gravação | Criar ou atualizar uma conta do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/locais/capacidade/leitura | Obtenha informações de funcionalidade de uma assinatura em relação ao uso do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/Locations/checkNameAvailability/Action | Verifique a disponibilidade de um nome de conta do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/Locations/operationResults/Read | Obtenha o resultado de uma operação de conta do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/localizações/usos/leitura | Obter informações de usos de cota de uma assinatura em relação ao uso de DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/operações/leitura | Obter as operações disponíveis do DataLakeAnalytics. |
> | Ação | Microsoft. DataLakeAnalytics/registro/ação | Registre a assinatura no DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. DataLakeStore/contas/excluir | Excluir uma conta do DataLakeStore. |
> | Ação | Microsoft. DataLakeStore/accounts/enableKeyVault/Action | Habilite o keyvault para uma conta do DataLakeStore. |
> | Ação | Microsoft. DataLakeStore/accounts/eventGridFilters/Delete | Excluir um filtro EventGrid. |
> | Ação | Microsoft. DataLakeStore/accounts/eventGridFilters/Read | Obter um filtro EventGrid. |
> | Ação | Microsoft. DataLakeStore/accounts/eventGridFilters/Write | Criar ou atualizar um filtro EventGrid. |
> | Ação | Microsoft. DataLakeStore/accounts/firewallRules/Delete | Excluir uma regra de firewall. |
> | Ação | Microsoft. DataLakeStore/accounts/firewallRules/Read | Obter informações sobre uma regra de firewall. |
> | Ação | Microsoft. DataLakeStore/accounts/firewallRules/Write | Criar ou atualizar uma regra de firewall. |
> | Ação | Microsoft. DataLakeStore/accounts/operationResults/Read | Obtenha o resultado de uma operação de conta do DataLakeStore. |
> | Ação | Microsoft. DataLakeStore/contas/ler | Obter informações sobre uma conta existente do DataLakeStore. |
> | Ação | Microsoft. DataLakeStore/contas/compartilhamentos/excluir | Excluir um compartilhamento. |
> | Ação | Microsoft. DataLakeStore/contas/compartilhamentos/leitura | Obter informações sobre um compartilhamento. |
> | Ação | Microsoft. DataLakeStore/contas/compartilhamentos/gravação | Criar ou atualizar um compartilhamento. |
> | Ação | Microsoft.DataLakeStore/accounts/Superuser/action | Conceda o superusuário em Data Lake Store quando concedido com Microsoft. Authorization/roleAssignments/Write. |
> | Ação | Microsoft. DataLakeStore/accounts/trustedIdProviders/Delete | Excluir um provedor de identidade confiável. |
> | Ação | Microsoft. DataLakeStore/accounts/trustedIdProviders/Read | Obtenha informações sobre um provedor de identidade confiável. |
> | Ação | Microsoft. DataLakeStore/accounts/trustedIdProviders/Write | Criar ou atualizar um provedor de identidade confiável. |
> | Ação | Microsoft. DataLakeStore/accounts/virtualNetworkRules/Delete | Excluir uma regra de rede virtual. |
> | Ação | Microsoft. DataLakeStore/accounts/virtualNetworkRules/Read | Obter informações sobre uma regra de rede virtual. |
> | Ação | Microsoft. DataLakeStore/accounts/virtualNetworkRules/Write | Criar ou atualizar uma regra de rede virtual. |
> | Ação | Microsoft. DataLakeStore/contas/gravação | Criar ou atualizar uma conta do DataLakeStore. |
> | Ação | Microsoft. DataLakeStore/locais/capacidade/leitura | Obtenha informações de funcionalidade de uma assinatura em relação ao uso do DataLakeStore. |
> | Ação | Microsoft. DataLakeStore/Locations/checkNameAvailability/Action | Verifique a disponibilidade de um nome de conta do DataLakeStore. |
> | Ação | Microsoft. DataLakeStore/Locations/operationResults/Read | Obtenha o resultado de uma operação de conta do DataLakeStore. |
> | Ação | Microsoft. DataLakeStore/localizações/usos/leitura | Obter informações de usos de cota de uma assinatura em relação ao uso de DataLakeStore. |
> | Ação | Microsoft. DataLakeStore/operações/leitura | Obter as operações disponíveis do DataLakeStore. |
> | Ação | Microsoft. DataLakeStore/registro/ação | Registre a assinatura no DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft. datamigration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. datamigration/Locations/operationResults/Read | Obter o status de uma operação de execução longa relacionada a uma resposta 202 aceita |
> | Ação | Microsoft. datamigration/Locations/operationStatuses/Read | Obter o status de uma operação de execução longa relacionada a uma resposta 202 aceita |
> | Ação | Microsoft. datamigration/registro/ação | Registra a assinatura com o provedor de serviços de migração de banco de dados do Azure |
> | Ação | Microsoft. datamigration/Services/addtrabalhoer/ação | Adiciona um trabalho do DMS aos trabalhos de WDS disponível do serviço |
> | Ação | Microsoft. datamigration/Services/checkStatus/Action | Verificar se o serviço está implantado e em execução |
> | Ação | Microsoft. datamigration/Services/configureWorker/Action | Configura um trabalho do DMS para os trabalhos de WDS disponível do serviço |
> | Ação | Microsoft. datamigration/serviços/excluir | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft. datamigration/Services/Projects/accessArtifacts/Action | Gerar uma URL que pode ser usada para obter ou colocar artefatos do projeto |
> | Ação | Microsoft. datamigration/serviços/projetos/excluir | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft. datamigration/Services/Projects/files/Delete | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft. datamigration/Services/Projects/files/Read | Ler informações sobre recursos |
> | Ação | Microsoft. datamigration/Services/Projects/files/Read/Action | Obter uma URL que pode ser usada para ler o conteúdo do arquivo |
> | Ação | Microsoft. datamigration/Services/Projects/files/readWrite/Action | Obter uma URL que pode ser usada para ler ou gravar o conteúdo do arquivo |
> | Ação | Microsoft. datamigration/Services/Projects/files/Write | Criar ou atualizar recursos e suas propriedades |
> | Ação | Microsoft. datamigration/serviços/projetos/ler | Ler informações sobre recursos |
> | Ação | Microsoft. datamigration/serviços/projetos/tarefas/cancelar/ação | Cancelar a tarefa se ela estiver em execução no momento |
> | Ação | Microsoft. datamigration/serviços/projetos/tarefas/excluir | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft. datamigration/serviços/projetos/tarefas/ler | Ler informações sobre recursos |
> | Ação | Microsoft. datamigration/serviços/projetos/tarefas/gravação | Tarefas de execução do serviço de migração de banco de dados do Azure |
> | Ação | Microsoft. datamigration/Services/Projects/Write | Tarefas de execução do serviço de migração de banco de dados do Azure |
> | Ação | Microsoft. datamigration/serviços/leitura | Ler informações sobre recursos |
> | Ação | Microsoft. datamigration/Services/removeWorker/Action | Remove um trabalho DMS para os trabalhos de WDS disponível do serviço |
> | Ação | Microsoft. datamigration/Services/serviço/cancelamento/ação | Cancelar a tarefa se ela estiver em execução no momento |
> | Ação | Microsoft. datamigration/serviços/serviço/exclusão | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft. datamigration/serviços/serviço/leitura | Ler informações sobre recursos |
> | Ação | Microsoft. datamigration/Services/serviços de istask/Write | Tarefas de execução do serviço de migração de banco de dados do Azure |
> | Ação | Microsoft. datamigration/Services/Slots/excluir | Exclui um recurso e todos os seus filhos |
> | Ação | Microsoft. datamigration/Services/Slots/leitura | Ler informações sobre recursos |
> | Ação | Microsoft. datamigration/Services/Slots/gravação | Criar ou atualizar recursos e suas propriedades |
> | Ação | Microsoft. datamigration/Services/iniciar/ação | Inicie o serviço DMS para permitir que ele processe as migrações novamente |
> | Ação | Microsoft. datamigration/Services/Stop/Action | Parar o serviço DMS para minimizar seu custo |
> | Ação | Microsoft. datamigration/Services/updateAgentConfig/Action | Atualiza a configuração do agente DMS com os valores fornecidos. |
> | Ação | Microsoft. datamigration/Services/Write | Criar ou atualizar recursos e suas propriedades |
> | Ação | Microsoft. datamigration/SKUs/Read | Obtenha uma lista de SKUs com suporte dos recursos do DMS. |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. DBforMariaDB/checkNameAvailability/Action | Verifique se o nome do servidor fornecido está disponível para provisionamento em todo o mundo para uma determinada assinatura. |
> | Ação | Microsoft. DBforMariaDB/Locations/azureAsyncOperation/Read | Retornar resultados da operação do servidor MariaDB |
> | Ação | Microsoft. DBforMariaDB/Locations/operationResults/Read | Retornar os resultados da operação do servidor MariaDB com base em resourcegroup |
> | Ação | Microsoft. DBforMariaDB/Locations/operationResults/Read | Retornar resultados da operação do servidor MariaDB |
> | Ação | Microsoft. DBforMariaDB/Locations/performanceTiers/Read | Retorna a lista de níveis de desempenho disponíveis. |
> | Ação | Microsoft. DBforMariaDB/Locations/securityAlertPoliciesAzureAsyncOperation/Read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft. DBforMariaDB/Locations/securityAlertPoliciesOperationResults/Read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft. DBforMariaDB/operações/leitura | Retornar a lista de operações de MariaDB. |
> | Ação | Microsoft. DBforMariaDB/performanceTiers/Read | Retorna a lista de níveis de desempenho disponíveis. |
> | Ação | Microsoft. DBforMariaDB/registro/ação | Registrar provedor de recursos MariaDB |
> | Ação | Microsoft. DBforMariaDB/servidores/administradores/excluir | Exclui um administrador existente do MariaDB Server. |
> | Ação | Microsoft. DBforMariaDB/servidores/administradores/leitura | Obtém uma lista de administradores do servidor MariaDB. |
> | Ação | Microsoft. DBforMariaDB/servidores/administradores/gravação | Cria ou atualiza o administrador do servidor MariaDB com os parâmetros especificados. |
> | Ação | Microsoft. DBforMariaDB/servidores/Advisor/createRecommendedActionSession/Action | Criar uma nova sessão de ação de recomendação |
> | Ação | Microsoft. DBforMariaDB/servidores/consultores/leitura | Retornar a lista de consultores |
> | Ação | Microsoft. DBforMariaDB/servidores/consultores/leitura | Retornar um supervisor |
> | Ação | Microsoft. DBforMariaDB/servidores/Advisors/recommendedActions/Read | Retornar a lista de ações recomendadas |
> | Ação | Microsoft. DBforMariaDB/servidores/Advisors/recommendedActions/Read | Retornar a lista de ações recomendadas |
> | Ação | Microsoft. DBforMariaDB/servidores/Advisors/recommendedActions/Read | Retornar uma ação recomendada |
> | Ação | Microsoft. DBforMariaDB/servidores/configurações/leitura | Retornar a lista de configurações para um servidor ou obter as propriedades para a configuração especificada. |
> | Ação | Microsoft. DBforMariaDB/servidores/configurações/gravação | Atualizar o valor para a configuração especificada |
> | Ação | Microsoft. DBforMariaDB/servidores/bancos de dados/excluir | Exclui um banco de dados MariaDB existente. |
> | Ação | Microsoft. DBforMariaDB/servidores/bancos de dados/leitura | Retornar a lista de bancos de dados MariaDB ou obter as propriedades do banco de dados especificado. |
> | Ação | Microsoft. DBforMariaDB/servidores/bancos de dados/gravação | Cria um banco de dados MariaDB com os parâmetros especificados ou atualiza as propriedades do banco de dados especificado. |
> | Ação | Microsoft. DBforMariaDB/servidores/excluir | Exclui um servidor existente. |
> | Ação | Microsoft. DBforMariaDB/Servers/firewallRules/Delete | Exclui uma regra de firewall existente. |
> | Ação | Microsoft. DBforMariaDB/Servers/firewallRules/Read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades da regra de firewall especificada. |
> | Ação | Microsoft. DBforMariaDB/Servers/firewallRules/Write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Ação | Microsoft. DBforMariaDB/servidores/logFiles/ler | Retornar a lista de arquivos de log MariaDB. |
> | Ação | Microsoft. DBforMariaDB/Servers/Providers/Microsoft. insights/diagnosticSettings/Read | Obtém a configuração desindependente do recurso |
> | Ação | Microsoft. DBforMariaDB/Servers/Providers/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. DBforMariaDB/Servers/Providers/Microsoft. insights/logDefinitions/Read | Obter os logs disponíveis para servidores MariaDB |
> | Ação | Microsoft. DBforMariaDB/Servers/Providers/Microsoft. insights/metricDefinitions/Read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft. DBforMariaDB/Servers/queryTexts/Action | Retornar os textos de uma lista de consultas |
> | Ação | Microsoft. DBforMariaDB/Servers/queryTexts/Action | Retornar o texto de uma consulta |
> | Ação | Microsoft. DBforMariaDB/servidores/ler | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft. DBforMariaDB/Servers/recoverableServers/Read | Retornar as informações recuperáveis do servidor MariaDB |
> | Ação | Microsoft. DBforMariaDB/servidores/réplicas/leitura | Obter réplicas de leitura de um servidor MariaDB |
> | Ação | Microsoft. DBforMariaDB/Servers/Restart/Action | Reinicia um servidor específico. |
> | Ação | Microsoft. DBforMariaDB/Servers/securityAlertPolicies/Read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em um determinado servidor |
> | Ação | Microsoft. DBforMariaDB/Servers/securityAlertPolicies/Write | Alterar a política de detecção de ameaças do servidor de um determinado servidor |
> | Ação | Microsoft. DBforMariaDB/Servers/topQueryStatistics/Read | Retornar a lista de estatísticas de consulta para as principais consultas. |
> | Ação | Microsoft. DBforMariaDB/Servers/topQueryStatistics/Read | Retornar uma estatística de consulta |
> | Ação | Microsoft. DBforMariaDB/Servers/updateConfigurations/Action | Atualizar configurações para o servidor especificado |
> | Ação | Microsoft. DBforMariaDB/Servers/virtualNetworkRules/Delete | Exclui uma regra de rede virtual existente |
> | Ação | Microsoft. DBforMariaDB/Servers/virtualNetworkRules/Read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft. DBforMariaDB/Servers/virtualNetworkRules/Write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou marcas para a regra de rede virtual especificada. |
> | Ação | Microsoft. DBforMariaDB/Servers/waitStatistics/Read | Estatísticas de espera de retorno para uma instância |
> | Ação | Microsoft. DBforMariaDB/Servers/waitStatistics/Read | Retornar uma estatística de espera |
> | Ação | Microsoft. DBforMariaDB/Servers/Write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. DBforMySQL/checkNameAvailability/Action | Verifique se o nome do servidor fornecido está disponível para provisionamento em todo o mundo para uma determinada assinatura. |
> | Ação | Microsoft. DBforMySQL/Locations/azureAsyncOperation/Read | Retornar resultados da operação do servidor MySQL |
> | Ação | Microsoft. DBforMySQL/Locations/operationResults/Read | Retornar resultados da operação do servidor MySQL com base no resourcegroup |
> | Ação | Microsoft. DBforMySQL/Locations/operationResults/Read | Retornar resultados da operação do servidor MySQL |
> | Ação | Microsoft. DBforMySQL/Locations/performanceTiers/Read | Retorna a lista de níveis de desempenho disponíveis. |
> | Ação | Microsoft. DBforMySQL/Locations/securityAlertPoliciesAzureAsyncOperation/Read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft. DBforMySQL/Locations/securityAlertPoliciesOperationResults/Read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft. DBforMySQL/operações/leitura | Retornar a lista de operações do MySQL. |
> | Ação | Microsoft. DBforMySQL/performanceTiers/Read | Retorna a lista de níveis de desempenho disponíveis. |
> | Ação | Microsoft. DBforMySQL/registro/ação | Registrar provedor de recursos MySQL |
> | Ação | Microsoft. DBforMySQL/servidores/administradores/excluir | Exclui um administrador existente do MySQL Server. |
> | Ação | Microsoft. DBforMySQL/servidores/administradores/leitura | Obtém uma lista de administradores do servidor MySQL. |
> | Ação | Microsoft. DBforMySQL/servidores/administradores/gravação | Cria ou atualiza o administrador do servidor MySQL com os parâmetros especificados. |
> | Ação | Microsoft. DBforMySQL/servidores/Advisor/createRecommendedActionSession/Action | Criar uma nova sessão de ação de recomendação |
> | Ação | Microsoft. DBforMySQL/servidores/consultores/leitura | Retornar a lista de consultores |
> | Ação | Microsoft. DBforMySQL/servidores/consultores/leitura | Retornar um supervisor |
> | Ação | Microsoft. DBforMySQL/servidores/Advisors/recommendedActions/Read | Retornar a lista de ações recomendadas |
> | Ação | Microsoft. DBforMySQL/servidores/Advisors/recommendedActions/Read | Retornar a lista de ações recomendadas |
> | Ação | Microsoft. DBforMySQL/servidores/Advisors/recommendedActions/Read | Retornar uma ação recomendada |
> | Ação | Microsoft. DBforMySQL/servidores/configurações/leitura | Retornar a lista de configurações para um servidor ou obter as propriedades para a configuração especificada. |
> | Ação | Microsoft. DBforMySQL/servidores/configurações/gravação | Atualizar o valor para a configuração especificada |
> | Ação | Microsoft. DBforMySQL/servidores/bancos de dados/excluir | Exclui um banco de dados MySQL existente. |
> | Ação | Microsoft. DBforMySQL/servidores/bancos de dados/leitura | Retornar a lista de bancos de dados MySQL ou obter as propriedades do banco de dados especificado. |
> | Ação | Microsoft. DBforMySQL/servidores/bancos de dados/gravação | Cria um banco de dados MySQL com os parâmetros especificados ou atualiza as propriedades do banco de dados especificado. |
> | Ação | Microsoft. DBforMySQL/servidores/excluir | Exclui um servidor existente. |
> | Ação | Microsoft. DBforMySQL/Servers/firewallRules/Delete | Exclui uma regra de firewall existente. |
> | Ação | Microsoft. DBforMySQL/Servers/firewallRules/Read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades da regra de firewall especificada. |
> | Ação | Microsoft. DBforMySQL/Servers/firewallRules/Write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Ação | Microsoft. DBforMySQL/servidores/logFiles/ler | Retornar a lista de arquivos de log PostgreSQL. |
> | Ação | Microsoft. DBforMySQL/Servers/Providers/Microsoft. insights/diagnosticSettings/Read | Obtém a configuração desindependente do recurso |
> | Ação | Microsoft. DBforMySQL/Servers/Providers/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. DBforMySQL/Servers/Providers/Microsoft. insights/logDefinitions/Read | Obter os logs disponíveis para servidores MySQL |
> | Ação | Microsoft. DBforMySQL/Servers/Providers/Microsoft. insights/metricDefinitions/Read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft. DBforMySQL/Servers/queryTexts/Action | Retornar os textos de uma lista de consultas |
> | Ação | Microsoft. DBforMySQL/Servers/queryTexts/Action | Retornar o texto de uma consulta |
> | Ação | Microsoft. DBforMySQL/servidores/ler | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft. DBforMySQL/Servers/recoverableServers/Read | Retornar as informações recuperáveis do MySQL Server |
> | Ação | Microsoft. DBforMySQL/servidores/réplicas/leitura | Obter réplicas de leitura de um servidor MySQL |
> | Ação | Microsoft. DBforMySQL/Servers/Restart/Action | Reinicia um servidor específico. |
> | Ação | Microsoft. DBforMySQL/Servers/securityAlertPolicies/Read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em um determinado servidor |
> | Ação | Microsoft. DBforMySQL/Servers/securityAlertPolicies/Write | Alterar a política de detecção de ameaças do servidor de um determinado servidor |
> | Ação | Microsoft. DBforMySQL/Servers/topQueryStatistics/Read | Retornar a lista de estatísticas de consulta para as principais consultas. |
> | Ação | Microsoft. DBforMySQL/Servers/topQueryStatistics/Read | Retornar uma estatística de consulta |
> | Ação | Microsoft. DBforMySQL/Servers/updateConfigurations/Action | Atualizar configurações para o servidor especificado |
> | Ação | Microsoft. DBforMySQL/Servers/virtualNetworkRules/Delete | Exclui uma regra de rede virtual existente |
> | Ação | Microsoft. DBforMySQL/Servers/virtualNetworkRules/Read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft. DBforMySQL/Servers/virtualNetworkRules/Write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou marcas para a regra de rede virtual especificada. |
> | Ação | Microsoft. DBforMySQL/Servers/waitStatistics/Read | Estatísticas de espera de retorno para uma instância |
> | Ação | Microsoft. DBforMySQL/Servers/waitStatistics/Read | Retornar uma estatística de espera |
> | Ação | Microsoft. DBforMySQL/Servers/Write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. DBforPostgreSQL/checkNameAvailability/Action | Verifique se o nome do servidor fornecido está disponível para provisionamento em todo o mundo para uma determinada assinatura. |
> | Ação | Microsoft. DBforPostgreSQL/Locations/azureAsyncOperation/Read | Retornar resultados da operação do servidor PostgreSQL |
> | Ação | Microsoft. DBforPostgreSQL/Locations/operationResults/Read | Retornar resultados da operação do servidor PostgreSQL com base no resourcegroup |
> | Ação | Microsoft. DBforPostgreSQL/Locations/operationResults/Read | Retornar resultados da operação do servidor PostgreSQL |
> | Ação | Microsoft. DBforPostgreSQL/Locations/performanceTiers/Read | Retorna a lista de níveis de desempenho disponíveis. |
> | Ação | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionOperationResults/Read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft. DBforPostgreSQL/Locations/securityAlertPoliciesAzureAsyncOperation/Read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft. DBforPostgreSQL/Locations/securityAlertPoliciesOperationResults/Read | Retornar a lista de resultados da operação de detecção de ameaças do servidor. |
> | Ação | Microsoft. DBforPostgreSQL/operações/leitura | Retornar a lista de operações PostgreSQL. |
> | Ação | Microsoft. DBforPostgreSQL/performanceTiers/Read | Retorna a lista de níveis de desempenho disponíveis. |
> | Ação | Microsoft. DBforPostgreSQL/registro/ação | Registrar provedor de recursos PostgreSQL |
> | Ação | Microsoft. DBforPostgreSQL/servidores/administradores/excluir | Exclui um administrador existente do servidor PostgreSQL. |
> | Ação | Microsoft. DBforPostgreSQL/servidores/administradores/leitura | Obtém uma lista de administradores do servidor PostgreSQL. |
> | Ação | Microsoft. DBforPostgreSQL/servidores/administradores/gravação | Cria ou atualiza o administrador do servidor PostgreSQL com os parâmetros especificados. |
> | Ação | Microsoft. DBforPostgreSQL/servidores/consultores/leitura | Retornar a lista de consultores |
> | Ação | Microsoft. DBforPostgreSQL/servidores/Advisors/recommendedActions/Read | Retornar a lista de ações recomendadas |
> | Ação | Microsoft. DBforPostgreSQL/servidores/Advisor/recommendedActionSessions/Action | Fazer recomendações |
> | Ação | Microsoft. DBforPostgreSQL/servidores/configurações/leitura | Retornar a lista de configurações para um servidor ou obter as propriedades para a configuração especificada. |
> | Ação | Microsoft. DBforPostgreSQL/servidores/configurações/gravação | Atualizar o valor para a configuração especificada |
> | Ação | Microsoft. DBforPostgreSQL/servidores/bancos de dados/excluir | Exclui um banco de dados PostgreSQL existente. |
> | Ação | Microsoft. DBforPostgreSQL/servidores/bancos de dados/leitura | Retornar a lista de bancos de dados PostgreSQL ou obter as propriedades do banco de dados especificado. |
> | Ação | Microsoft. DBforPostgreSQL/servidores/bancos de dados/gravação | Cria um banco de dados PostgreSQL com os parâmetros especificados ou atualiza as propriedades do banco de dados especificado. |
> | Ação | Microsoft. DBforPostgreSQL/servidores/excluir | Exclui um servidor existente. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/firewallRules/Delete | Exclui uma regra de firewall existente. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/firewallRules/Read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades da regra de firewall especificada. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/firewallRules/Write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Ação | Microsoft. DBforPostgreSQL/servidores/logFiles/ler | Retornar a lista de arquivos de log PostgreSQL. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Delete | Exclui um proxy de conexão de ponto de extremidade privado existente |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Read | Retorna a lista de proxies de conexão de ponto de extremidade privado ou obtém as propriedades para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Validate/Action | Valida uma conexão de ponto de extremidade privada criar chamada do lado do NRP |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Write | Cria um proxy de conexão de ponto de extremidade privado com os parâmetros especificados ou atualiza as propriedades ou marcas para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Delete | Exclui uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Read | Retorna a lista de conexões de ponto de extremidade privado ou obtém as propriedades para a conexão de ponto de extremidade particular especificada. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Write | Aprova ou rejeita uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft. DBforPostgreSQL/Servers/privateLinkResources/Read | Obter os recursos de link privado para o servidor PostgreSQL correspondente |
> | Ação | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. insights/diagnosticSettings/Read | Obtém a configuração desindependente do recurso |
> | Ação | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. insights/logDefinitions/Read | Obter os logs disponíveis para servidores postgres |
> | Ação | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. insights/metricDefinitions/Read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft. DBforPostgreSQL/Servers/queryTexts/Action | Retornar o texto de uma consulta |
> | Ação | Microsoft. DBforPostgreSQL/Servers/queryTexts/Read | Retornar os textos de uma lista de consultas |
> | Ação | Microsoft. DBforPostgreSQL/servidores/ler | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/recoverableServers/Read | Retornar as informações recuperáveis do servidor PostgreSQL |
> | Ação | Microsoft. DBforPostgreSQL/servidores/réplicas/leitura | Obter réplicas de leitura de um servidor PostgreSQL |
> | Ação | Microsoft. DBforPostgreSQL/Servers/Restart/Action | Reinicia um servidor específico. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/securityAlertPolicies/Read | Recuperar detalhes da política de detecção de ameaças do servidor configurada em um determinado servidor |
> | Ação | Microsoft. DBforPostgreSQL/Servers/securityAlertPolicies/Write | Alterar a política de detecção de ameaças do servidor de um determinado servidor |
> | Ação | Microsoft. DBforPostgreSQL/Servers/topQueryStatistics/Read | Retornar a lista de estatísticas de consulta para as principais consultas. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/updateConfigurations/Action | Atualizar configurações para o servidor especificado |
> | Ação | Microsoft. DBforPostgreSQL/Servers/virtualNetworkRules/Delete | Exclui uma regra de rede virtual existente |
> | Ação | Microsoft. DBforPostgreSQL/Servers/virtualNetworkRules/Read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/virtualNetworkRules/Write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou marcas para a regra de rede virtual especificada. |
> | Ação | Microsoft. DBforPostgreSQL/Servers/waitStatistics/Read | Estatísticas de espera de retorno para uma instância |
> | Ação | Microsoft. DBforPostgreSQL/Servers/Write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou marcas para o servidor especificado. |
> | Ação | Microsoft. DBforPostgreSQL/serversv2/configurações/leitura | Retornar a lista de configurações para um servidor ou obter as propriedades para a configuração especificada. |
> | Ação | Microsoft. DBforPostgreSQL/serversv2/configurações/gravação | Atualizar o valor para a configuração especificada |
> | Ação | Microsoft. DBforPostgreSQL/serversv2/Delete | Exclui um servidor existente. |
> | Ação | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Delete | Exclui uma regra de firewall existente. |
> | Ação | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Read | Retornar a lista de regras de firewall para um servidor ou obter as propriedades da regra de firewall especificada. |
> | Ação | Microsoft. DBforPostgreSQL/serversv2/firewallRules/Write | Cria uma regra de firewall com os parâmetros especificados ou atualiza uma regra existente. |
> | Ação | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. insights/diagnosticSettings/Read | Obtém a configuração desindependente do recurso |
> | Ação | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. insights/logDefinitions/Read | Obter os logs disponíveis para servidores postgres |
> | Ação | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. insights/metricDefinitions/Read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft. DBforPostgreSQL/serversv2/Read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft. DBforPostgreSQL/serversv2/updateConfigurations/Action | Atualizar configurações para o servidor especificado |
> | Ação | Microsoft. DBforPostgreSQL/serversv2/Write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou marcas para o servidor especificado. |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Devices/Account/diagnosticSettings/Read | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/Account/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/Account/logDefinitions/Read | Obter as definições de log disponíveis para o serviço IotHub |
> | Ação | Microsoft. Devices/Account/metricDefinitions/Read | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft. Devices/checkNameAvailability/Action | Verifique se o nome do IotHub está disponível |
> | Ação | Microsoft. Devices/checkNameAvailability/Action | Verifique se o nome do IotHub está disponível |
> | Ação | Microsoft. Devices/checkProvisioningServiceNameAvailability/Action | Verifique se o nome do serviço de provisionamento está disponível |
> | Ação | Microsoft. Devices/checkProvisioningServiceNameAvailability/Action | Verifique se o nome do serviço de provisionamento está disponível |
> | Ação | Microsoft. Devices/digitalTwins/Delete | Excluir uma conta gêmeos digital existente e todos os seus filhos |
> | Ação | Microsoft. Devices/digitalTwins/operationresults/ler | Obter o status de uma operação em uma conta do gêmeos digital |
> | Ação | Microsoft. Devices/digitalTwins/Read | Obtém uma lista das contas do digital gêmeos associadas a uma assinatura |
> | Ação | Microsoft. Devices/digitalTwins/SKUs/leitura | Obter uma lista de SKUs válidos para contas do digital gêmeos |
> | Ação | Microsoft. Devices/digitalTwins/Write | Criar uma nova conta de gêmeos de dígito |
> | Ação | Microsoft. Devices/ElasticPools/diagnosticSettings/ler | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/ElasticPools/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/certificados/excluir | Exclui o certificado |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/Certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/certificados/leitura | Obtém o certificado |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/certificados/verificar/ação | Verificar o recurso de certificado |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/certificados/gravação | Criar ou atualizar certificado |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/Delete | Excluir o recurso de locatário IotHub |
> | Ação | Microsoft. Devices/ElasticPools/IotHubTenants/diagnosticSettings/leitura | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/ElasticPools/IotHubTenants/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Excluir grupo de consumidores do EventHub |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/ler | Obter grupo (s) de consumidores do EventHub |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/gravação | Criar grupo de consumidores do EventHub |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/exportDevices/Action | Exportar dispositivos |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/GetStats/Read | Obter o recurso de estatísticas de locatário IotHub |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/importDevices/Action | Importar dispositivos |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Obtém a chave de locatário IotHub |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/Jobs/leitura | Obter detalhes de trabalhos enviados em determinado IotHub |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/listKeys/Action | Obter chaves de locatário IotHub |
> | Ação | Microsoft. Devices/ElasticPools/IotHubTenants/logDefinitions/leitura | Obter as definições de log disponíveis para o serviço IotHub |
> | Ação | Microsoft. Devices/ElasticPools/IotHubTenants/metricDefinitions/leitura | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/quotaMetrics/leitura | Obter métricas de cota |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/ler | Obter o recurso de locatário IotHub |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/roteamento/rotas/$testall/Action | Testar uma mensagem em todas as rotas existentes |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/roteamento/rotas/$testnew/Action | Testar uma mensagem em relação a uma rota de teste fornecida |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/routingEndpointsHealth/leitura | Obtém a integridade de todos os pontos de extremidade de roteamento para um IotHub |
> | Ação | Microsoft. Devices/elasticPools/iotHubTenants/Write | Criar ou atualizar o recurso de locatário IotHub |
> | Ação | Microsoft. Devices/ElasticPools/metricDefinitions/ler | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft. Devices/iotHubs/Certificates/Delete | Exclui o certificado |
> | Ação | Microsoft. Devices/iotHubs/Certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft. Devices/iotHubs/Certificates/Read | Obtém o certificado |
> | Ação | Microsoft. Devices/iotHubs/certificados/verificar/ação | Verificar o recurso de certificado |
> | Ação | Microsoft. Devices/iotHubs/Certificates/Write | Criar ou atualizar certificado |
> | Ação | Microsoft. Devices/iotHubs/Delete | Excluir recurso IotHub |
> | Ação | Microsoft. Devices/IotHubs/diagnosticSettings/ler | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/IotHubs/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/iotHubs/eventGridFilters/Delete | Excluir o filtro de grade de eventos |
> | Ação | Microsoft. Devices/iotHubs/eventGridFilters/ler | Obter o filtro de grade de eventos |
> | Ação | Microsoft. Devices/iotHubs/eventGridFilters/Write | Criar novo ou atualizar filtro de grade de eventos existente |
> | Ação | Microsoft. Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Excluir grupo de consumidores do EventHub |
> | Ação | Microsoft. Devices/iotHubs/eventHubEndpoints/consumerGroups/leitura | Obter grupo (s) de consumidores do EventHub |
> | Ação | Microsoft. Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Criar grupo de consumidores do EventHub |
> | Ação | Microsoft. Devices/iotHubs/exportDevices/Action | Exportar dispositivos |
> | Ação | Microsoft. Devices/iotHubs/importDevices/Action | Importar dispositivos |
> | Ação | Microsoft. Devices/iotHubs/iotHubKeys/listkeys/Action | Obter a chave IotHub para o nome fornecido |
> | Ação | Microsoft. Devices/iotHubs/iotHubStats/ler | Obter estatísticas do IotHub |
> | Ação | Microsoft. Devices/iotHubs/Jobs/ler | Obter detalhes de trabalhos enviados em determinado IotHub |
> | Ação | Microsoft. Devices/iotHubs/listkeys/Action | Obter todas as chaves IotHub |
> | Ação | Microsoft. Devices/IotHubs/logDefinitions/ler | Obter as definições de log disponíveis para o serviço IotHub |
> | Ação | Microsoft. Devices/IotHubs/metricDefinitions/ler | Obter as métricas disponíveis para o serviço IotHub |
> | Ação | Microsoft. Devices/iotHubs/operationresults/ler | Obter resultado da operação (API obsoleta) |
> | Ação | Microsoft. Devices/iotHubs/quotaMetrics/ler | Obter métricas de cota |
> | Ação | Microsoft. Devices/iotHubs/Read | Obter os recursos IotHub |
> | Ação | Microsoft. Devices/iotHubs/Routing/$testall/Action | Testar uma mensagem em todas as rotas existentes |
> | Ação | Microsoft. Devices/iotHubs/Routing/$testnew/Action | Testar uma mensagem em relação a uma rota de teste fornecida |
> | Ação | Microsoft. Devices/iotHubs/routingEndpointsHealth/ler | Obtém a integridade de todos os pontos de extremidade de roteamento para um IotHub |
> | Ação | Microsoft. Devices/iotHubs/SKUs/leitura | Obter SKUs IotHub válidas |
> | Ação | Microsoft. Devices/iotHubs/Write | Criar ou atualizar recurso IotHub |
> | Ação | Microsoft. Devices/Locations/operationresults/Read | Obter resultado da operação baseada na localização |
> | Ação | Microsoft. Devices/operationresults/Read | Obter resultado da operação |
> | Ação | Microsoft. dispositivos/operações/leitura | Obter todas as operações resourceprovider |
> | Ação | Microsoft. Devices/provisioningServices/Certificates/Delete | Exclui o certificado |
> | Ação | Microsoft. Devices/provisioningServices/Certificates/generateVerificationCode/Action | Gerar código de verificação |
> | Ação | Microsoft. Devices/provisioningServices/Certificates/Read | Obtém o certificado |
> | Ação | Microsoft. Devices/provisioningServices/certificados/verificar/ação | Verificar o recurso de certificado |
> | Ação | Microsoft. Devices/provisioningServices/Certificates/Write | Criar ou atualizar certificado |
> | Ação | Microsoft. Devices/provisioningServices/Delete | Excluir recurso IotDps |
> | Ação | Microsoft. Devices/provisioningServices/diagnosticSettings/ler | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/provisioningServices/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. Devices/provisioningServices/Keys/listkeys/ação | Obter chaves IotDps para o nome da chave |
> | Ação | Microsoft. Devices/provisioningServices/listkeys/Action | Obter todas as chaves IotDps |
> | Ação | Microsoft. Devices/provisioningServices/logDefinitions/ler | Obter as definições de log disponíveis para o serviço de provisionamento |
> | Ação | Microsoft. Devices/provisioningServices/metricDefinitions/ler | Obter as métricas disponíveis para o serviço de provisionamento |
> | Ação | Microsoft. Devices/provisioningServices/operationresults/ler | Obter resultado da operação de DPS |
> | Ação | Microsoft. Devices/provisioningServices/Read | Obter recurso IotDps |
> | Ação | Microsoft. Devices/provisioningServices/SKUs/leitura | Obter SKUs IotDps válidas |
> | Ação | Microsoft. Devices/provisioningServices/Write | Criar recurso IotDps |
> | Ação | Microsoft. Devices/registro/ação | Registrar a assinatura para o provedor de recursos IotHub e habilitar a criação de recursos IotHub |
> | Ação | Microsoft. Devices/usos/leitura | Obter detalhes de uso da assinatura para este provedor. |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. DevSpaces/Controllers/Delete | Excluir Azure Dev Spaces controlador e os serviços de dataplane |
> | Ação | Microsoft. DevSpaces/Controllers/listConnectionDetails/Action | Listar detalhes de conexão para a infraestrutura do controlador de Azure Dev Spaces |
> | Ação | Microsoft. DevSpaces/Controllers/Read | Ler Azure Dev Spaces Propriedades do controlador |
> | Ação | Microsoft. DevSpaces/Controllers/Write | Criar ou atualizar propriedades do controlador de Azure Dev Spaces |
> | Ação | Microsoft. DevSpaces/Locations/checkContainerHostMapping/Action | Verificar o mapeamento de controlador existente para um host de contêiner |
> | Ação | Microsoft. DevSpaces/Locations/operationresults/Read | Ler o status de uma operação assíncrona |
> | Ação | Microsoft. DevSpaces/registro/ação | Registrar o provedor de recursos do Microsoft dev Spaces com uma assinatura |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. DevTestLab/labCenters/Delete | Excluir centros de laboratório. |
> | Ação | Microsoft. DevTestLab/labCenters/Read | Leia as centrais de laboratório. |
> | Ação | Microsoft. DevTestLab/labCenters/Write | Adicionar ou modificar as centrais de laboratório. |
> | Ação | Microsoft. DevTestLab/Labs/artifactSources/armTemplates/Read | Ler modelos do Azure Resource Manager. |
> | Ação | Microsoft. DevTestLab/Labs/artifactSources/artefatos/GenerateArmTemplate/Action | Gera um modelo de ARM para o artefato fornecido, carrega os arquivos necessários para uma conta de armazenamento e valida o artefato gerado. |
> | Ação | Microsoft. DevTestLab/Labs/artifactSources/artefatos/leitura | Ler artefatos. |
> | Ação | Microsoft. DevTestLab/Labs/artifactSources/Delete | Excluir fontes de artefato. |
> | Ação | Microsoft. DevTestLab/Labs/artifactSources/Read | Ler as fontes de artefato. |
> | Ação | Microsoft. DevTestLab/Labs/artifactSources/Write | Adicionar ou modificar fontes de artefato. |
> | Ação | Microsoft. DevTestLab/Labs/ClaimAnyVm/Action | Declare uma máquina virtual de declaração aleatória no laboratório. |
> | Ação | Microsoft. DevTestLab/Labs/custos/ler | Leia os custos. |
> | Ação | Microsoft. DevTestLab/Labs/custos/gravação | Adicionar ou modificar custos. |
> | Ação | Microsoft. DevTestLab/Labs/createambiente/ação | Crie máquinas virtuais em um laboratório. |
> | Ação | Microsoft. DevTestLab/Labs/customImages/Delete | Excluir imagens personalizadas. |
> | Ação | Microsoft. DevTestLab/Labs/customImages/Read | Ler imagens personalizadas. |
> | Ação | Microsoft. DevTestLab/Labs/customImages/Write | Adicionar ou modificar imagens personalizadas. |
> | Ação | Microsoft. DevTestLab/Labs/Delete | Excluir laboratórios. |
> | Ação | Microsoft. DevTestLab/Labs/EnsureCurrentUserProfile/Action | Verifique se o usuário atual tem um perfil válido no laboratório. |
> | Ação | Microsoft. DevTestLab/Labs/ExportResourceUsage/Action | Exporta o uso de recursos de laboratório para uma conta de armazenamento |
> | Ação | Microsoft. DevTestLab/Labs/fórmulas/excluir | Excluir fórmulas. |
> | Ação | Microsoft. DevTestLab/Labs/fórmulas/ler | Ler fórmulas. |
> | Ação | Microsoft. DevTestLab/Labs/fórmulas/gravar | Adicionar ou modificar fórmulas. |
> | Ação | Microsoft. DevTestLab/Labs/galleryImages/Read | Ler imagens da galeria. |
> | Ação | Microsoft. DevTestLab/Labs/GenerateUploadUri/Action | Gere um URI para carregar imagens de disco personalizadas em um laboratório. |
> | Ação | Microsoft. DevTestLab/Labs/ImportVirtualMachine/Action | Importe uma máquina virtual para um laboratório diferente. |
> | Ação | Microsoft. DevTestLab/Labs/ListVhds/Action | Listar imagens de disco disponíveis para a criação de imagens personalizadas. |
> | Ação | Microsoft. DevTestLab/Labs/notificationChannels/Delete | Excluir canais de notificação. |
> | Ação | Microsoft. DevTestLab/Labs/notificationChannels/notificar/ação | Enviar notificação para o canal fornecido. |
> | Ação | Microsoft. DevTestLab/Labs/notificationChannels/Read | Ler canais de notificação. |
> | Ação | Microsoft. DevTestLab/Labs/notificationChannels/Write | Adicionar ou modificar canais de notificação. |
> | Ação | Microsoft. DevTestLab/Labs/policySets/EvaluatePolicies/Action | Avalia a política de laboratório. |
> | Ação | Microsoft. DevTestLab/Labs/policySets/Policies/Delete | Excluir políticas. |
> | Ação | Microsoft. DevTestLab/Labs/policySets/políticas/leitura | Ler políticas. |
> | Ação | Microsoft. DevTestLab/Labs/policySets/Policies/Write | Adicionar ou modificar políticas. |
> | Ação | Microsoft. DevTestLab/Labs/policySets/Read | Ler conjuntos de políticas. |
> | Ação | Microsoft. DevTestLab/Labs/ler | Leia os laboratórios. |
> | Ação | Microsoft. DevTestLab/Labs/Schedules/Delete | Excluir agendas. |
> | Ação | Microsoft. DevTestLab/Labs/Schedules/execute/Action | Executar uma agenda. |
> | Ação | Microsoft. DevTestLab/Labs/Schedules/ListApplicable/Action | Lista todas as agendas aplicáveis |
> | Ação | Microsoft. DevTestLab/Labs/Schedules/Read | Ler agendas. |
> | Ação | Microsoft. DevTestLab/Labs/Schedules/Write | Adicionar ou modificar agendas. |
> | Ação | Microsoft. DevTestLab/Labs/perrunners/excluir | Excluir executores de serviço. |
> | Ação | Microsoft. DevTestLab/Labs/perrunners/ler | Ler executores de serviço. |
> | Ação | Microsoft. DevTestLab/Labs/perrunners/gravar | Adicionar ou modificar executores de serviço. |
> | Ação | Microsoft. DevTestLab/Labs/sharedGalleries/Delete | Excluir galerias compartilhadas. |
> | Ação | Microsoft. DevTestLab/Labs/sharedGalleries/Read | Ler galerias compartilhadas. |
> | Ação | Microsoft. DevTestLab/Labs/sharedGalleries/sharedImages/Delete | Excluir imagens compartilhadas. |
> | Ação | Microsoft. DevTestLab/Labs/sharedGalleries/sharedImages/Read | Ler imagens compartilhadas. |
> | Ação | Microsoft. DevTestLab/Labs/sharedGalleries/sharedImages/Write | Adicionar ou modificar imagens compartilhadas. |
> | Ação | Microsoft. DevTestLab/Labs/sharedGalleries/Write | Adicionar ou modificar galerias compartilhadas. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/excluir | Excluir perfis de usuário. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/discos/anexar/ação | Anexe e crie a concessão do disco para a máquina virtual. |
> | Ação | Microsoft. DevTestLab/Labs/Users/disks/Delete | Excluir discos. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/discos/desanexar/ação | Desanexe e quebre a concessão do disco anexado à máquina virtual. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/discos/ler | Ler discos. |
> | Ação | Microsoft. DevTestLab/Labs/Users/disks/Write | Adicionar ou modificar discos. |
> | Ação | Microsoft. DevTestLab/Labs/Users/Environments/Delete | Excluir ambientes. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/ambientes/ler | Ler ambientes. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/ambientes/gravação | Adicionar ou modificar ambientes. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/ler | Ler perfis de usuário. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/segredos/excluir | Exclua os segredos. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/segredos/ler | Ler segredos. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/segredos/gravar | Adicionar ou modificar segredos. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/perfabrics/excluir | Excluir malhas de serviço. |
> | Ação | Microsoft. DevTestLab/Labs/Users/perfabrics/ListApplicableSchedules/Action | Lista as agendas de início/parada aplicáveis, se houver. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/perfabrics/ler | Ler malhas de serviço. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/perfabrics/agendas/excluir | Excluir agendas. |
> | Ação | Microsoft. DevTestLab/Labs/Users/perfabrics/Schedules/execute/Action | Executar uma agenda. |
> | Ação | Microsoft. DevTestLab/Labs/Users/perfabrics/Schedules/Read | Ler agendas. |
> | Ação | Microsoft. DevTestLab/Labs/Users/perfabrics/Schedules/Write | Adicionar ou modificar agendas. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/perfabrics/iniciar/ação | Inicie um Service Fabric. |
> | Ação | Microsoft. DevTestLab/Labs/usuários/perfabrics/parar/ação | Parar um Service Fabric |
> | Ação | Microsoft. DevTestLab/Labs/Users/perfabrics/Write | Adicionar ou modificar malhas de serviço. |
> | Ação | Microsoft. DevTestLab/Labs/Users/Write | Adicionar ou modificar perfis de usuário. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/AddDataDisk/Action | Anexe um disco de dados novo ou existente à máquina virtual. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/ApplyArtifacts/Action | Aplicar artefatos à máquina virtual. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/declaração/ação | Apropriar-se de uma máquina virtual existente |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/Delete | Excluir máquinas virtuais. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/DetachDataDisk/Action | Desanexar o disco especificado da máquina virtual. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/GetRdpFileContents/Action | Obtém uma cadeia de caracteres que representa o conteúdo do arquivo RDP para a máquina virtual |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/ListApplicableSchedules/Action | Lista as agendas de início/parada aplicáveis, se houver. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/Read | Ler máquinas virtuais. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/reimplantação/ação | Reimplantar uma máquina virtual |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/redimensionar/ação | Redimensione a máquina virtual. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/Restart/Action | Reinicie uma máquina virtual. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/Schedules/Delete | Excluir agendas. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/Schedules/execute/Action | Executar uma agenda. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/Schedules/Read | Ler agendas. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/Schedules/Write | Adicionar ou modificar agendas. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/Start/Action | Inicie uma máquina virtual. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/Stop/Action | Parar uma máquina virtual |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/TransferDisks/Action | Transfere todos os discos de dados anexados à máquina virtual para que sejam de Propriedade do usuário atual. |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/desdeclaração/ação | Liberar a propriedade de uma máquina virtual existente |
> | Ação | Microsoft. DevTestLab/Labs/virtualMachines/Write | Adicionar ou modificar máquinas virtuais. |
> | Ação | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Delete | Exclua bastionhosts. |
> | Ação | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Read | Leia bastionhosts. |
> | Ação | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Write | Adicionar ou modificar bastionhosts. |
> | Ação | Microsoft. DevTestLab/Labs/virtualNetworks/Delete | Excluir redes virtuais. |
> | Ação | Microsoft. DevTestLab/Labs/virtualNetworks/Read | Ler redes virtuais. |
> | Ação | Microsoft. DevTestLab/Labs/virtualNetworks/Write | Adicionar ou modificar redes virtuais. |
> | Ação | Microsoft. DevTestLab/Labs/vmPools/Delete | Exclua os pools de máquina virtual. |
> | Ação | Microsoft. DevTestLab/Labs/vmPools/Read | Ler pools de máquinas virtuais. |
> | Ação | Microsoft. DevTestLab/Labs/vmPools/Write | Adicionar ou modificar pools de máquinas virtuais. |
> | Ação | Microsoft. DevTestLab/Labs/Write | Adicionar ou modificar laboratórios. |
> | Ação | Microsoft. DevTestLab/localizações/operações/leitura | Operações de leitura. |
> | Ação | Microsoft. DevTestLab/registro/ação | Registra a assinatura |
> | Ação | Microsoft. DevTestLab/Schedules/Delete | Excluir agendas. |
> | Ação | Microsoft. DevTestLab/Schedules/execute/Action | Executar uma agenda. |
> | Ação | Microsoft. DevTestLab/Schedules/Read | Ler agendas. |
> | Ação | Microsoft. DevTestLab/agendas/redirecionamento/ação | Atualiza a ID de recurso de destino de um agendamento. |
> | Ação | Microsoft. DevTestLab/Schedules/Write | Adicionar ou modificar agendas. |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. DocumentDB/databaseAccountNames/Read | Verifica a disponibilidade do nome. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/coleções/excluir | Excluir uma coleção. Aplicável somente a tipos de API: ' MongoDB '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/coleções/operationResults/Read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' MongoDB '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/coleções/leitura | Ler uma coleção ou listar todas as coleções. Aplicável somente a tipos de API: ' MongoDB '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/coleções/configurações/operationResults/leitura | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' MongoDB '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/coleções/configurações/leitura | Ler uma taxa de transferência de coleção. Aplicável somente a tipos de API: ' MongoDB '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/coleções/configurações/gravação | Atualizar uma taxa de transferência de coleção. Aplicável somente a tipos de API: ' MongoDB '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/coleções/gravação | Criar ou atualizar uma coleção. Aplicável somente a tipos de API: ' MongoDB '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/contêineres/excluir | Excluir um contêiner. Aplicável somente a tipos de API: ' SQL '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/contêineres/operationResults/Read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/contêineres/leitura | Ler um contêiner ou listar todos os contêineres. Aplicável somente a tipos de API: ' SQL '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/contêineres/configurações/operationResults/leitura | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/contêineres/configurações/leitura | Ler uma taxa de transferência de contêiner. Aplicável somente a tipos de API: ' SQL '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/contêineres/configurações/gravação | Atualizar uma taxa de transferência de contêiner. Aplicável somente a tipos de API: ' SQL '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/contêineres/gravação | Criar ou atualizar um contêiner. Aplicável somente a tipos de API: ' SQL '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/excluir | Excluir um banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/grafos/excluir | Excluir um grafo. Aplicável somente a tipos de API: ' gremlin'. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/grafos/operationResults/Read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' gremlin'. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/grafos/leitura | Ler um grafo ou listar todos os grafos. Aplicável somente a tipos de API: ' gremlin'. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/grafos/configurações/operationResults/leitura | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/grafos/configurações/leitura | Ler uma taxa de transferência de grafo. Aplicável somente a tipos de API: ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/grafos/configurações/gravação | Atualizar uma taxa de transferência de grafo. Aplicável somente a tipos de API: ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/grafos/gravação | Criar ou atualizar um grafo. Aplicável somente a tipos de API: ' gremlin'. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/operationResults/leitura | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/leitura | Ler um banco de dados ou listar todos os bancos. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/configurações/operationResults/ler | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/configurações/leitura | Ler uma taxa de transferência de banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/configurações/gravação | Atualizar uma taxa de transferência de banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/bancos de dados/gravação | Crie um banco de dados. Aplicável somente a tipos de API: ' SQL ', ' MongoDB ', ' gremlin'. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/keyspaces/excluir | Excluir um keyspace. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/keyspaces/operationResults/Read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/keyspaces/leitura | Ler um keyspace ou listar todos os keyspaces. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/keyspaces/Settings/operationResults/Read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/keyspaces/configurações/leitura | Ler uma taxa de transferência de keyspace. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/keyspaces/configurações/gravação | Atualize uma taxa de transferência de keyspace. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/keyespaços/tabelas/excluir | Excluir uma tabela. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/keyspaces/Tables/operationResults/Read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/keyspaces/Tables/Read | Ler uma tabela ou listar todas as tabelas. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/keyspaces/Tables/Settings/operationResults/Read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/keyspaces/tabelas/configurações/leitura | Ler uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/keyspaces/Tables/Settings/Write | Atualize uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Cassandra '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/keyspaces/Tables/Write | Criar ou atualizar uma tabela. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/keyspaces/Write | Crie um keyspace. Aplicável somente a tipos de API: ' Cassandra '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/Tables/Delete | Excluir uma tabela. Aplicável somente a tipos de API: ' Table '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/Tables/operationResults/Read | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Table '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/tabelas/leitura | Ler uma tabela ou listar todas as tabelas. Aplicável somente a tipos de API: ' Table '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/tabelas/configurações/operationResults/leitura | Status de leitura da operação assíncrona. Aplicável somente a tipos de API: ' Table '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/tabelas/configurações/leitura | Ler uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Table '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/tabelas/configurações/gravação | Atualize uma taxa de transferência de tabela. Aplicável somente a tipos de API: ' Table '. Aplicável somente para tipos de configuração: ' produtividade '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/APIs/tabelas/gravação | Criar ou atualizar uma tabela. Aplicável somente a tipos de API: ' Table '. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/backup/ação | Enviar uma solicitação para configurar o backup |
> | Ação | Microsoft. DocumentDB/databaseAccounts/changeResourceGroup/Action | Alterar grupo de recursos de uma conta de banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/bancos de dados/coleções/metricDefinitions/leitura | Lê as definições de métrica da coleção. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/bancos de dados/coleções/métricas/leitura | Lê as métricas da coleção. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/bancos de dados/coleções/partitionKeyRangeId/métricas/leitura | Ler métricas de nível de chave de partição de conta de banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/bancos de dados/coleções/partições/métricas/leitura | Ler métricas de nível de partição de conta de banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/bancos de dados/coleções/partições/leitura | Ler partições de conta de banco de dados em uma coleção |
> | Ação | Microsoft. DocumentDB/databaseAccounts/bancos de dados/coleções/partições/usos/leitura | Ler usos de nível de partição de conta de banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/bancos de dados/coleções/usos/leitura | Lê os usos da coleção. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/bancos de dados/metricDefinitions/leitura | Lê as definições de métricas de banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/bancos de dados/métricas/leitura | Lê as métricas do banco de dados. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/bancos de dados/usos/leitura | Lê os usos do banco de dados. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/Delete | Exclui as contas de banco de dados. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/failoverPriorityChange/Action | Alterar as prioridades de failover de regiões de uma conta de banco de dados. Isso é usado para executar a operação de failover manual |
> | Ação | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/Action | Obter a política de backup da conta do banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/listConnectionStrings/Action | Obter as cadeias de conexão para uma conta de banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/listKeys/Action | Listar chaves de uma conta de banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/metricDefinitions/Read | Lê as definições de métricas da conta de banco de dados. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/métricas/leitura | Lê as métricas da conta do banco de dados. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/offlineRegion/Action | Offline uma região de uma conta de banco de dados.  |
> | Ação | Microsoft. DocumentDB/databaseAccounts/onlineRegion/Action | Online uma região de uma conta de banco de dados. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/operationResults/Read | Ler o status da operação assíncrona |
> | Ação | Microsoft. DocumentDB/databaseAccounts/percentil/métricas/leitura | Métricas de latência de leitura |
> | Ação | Microsoft. DocumentDB/databaseAccounts/percentil/leitura | Ler percentuais de latências de replicação |
> | Ação | Microsoft. DocumentDB/databaseAccounts/percentil/sourceRegion/targetRegion/métricas/leitura | Ler métricas de latência para uma região específica de origem e de destino |
> | Ação | Microsoft. DocumentDB/databaseAccounts/percentil/targetRegion/métricas/leitura | Ler métricas de latência para uma região de destino específica |
> | Ação | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Delete | Excluir um proxy de conexão de ponto de extremidade privado da conta de banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Read | Ler um proxy de conexão de ponto de extremidade privado da conta do banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validação/ação | Validar um proxy de conexão de ponto de extremidade privado da conta de banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Write | Criar ou atualizar um proxy de conexão de ponto de extremidade privado da conta do banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/Read | Lê uma conta de banco de dados. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/readonlykeys/Action | Lê as chaves somente leitura da conta do banco de dados. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/readonlykeys/Read | Lê as chaves somente leitura da conta do banco de dados. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/regenerateKey/Action | Girar as chaves de uma conta de banco de dados |
> | Ação | Microsoft. DocumentDB/databaseAccounts/região/bancos de dados/coleções/métricas/leitura | Lê as métricas da coleção regional. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/região/bancos de dados/coleções/partitionKeyRangeId/métricas/leitura | Ler métricas de nível de chave de partição de conta de banco de dados regional |
> | Ação | Microsoft. DocumentDB/databaseAccounts/região/bancos de dados/coleções/partições/métricas/leitura | Ler métricas de nível de partição da conta do banco de dados regional |
> | Ação | Microsoft. DocumentDB/databaseAccounts/região/bancos de dados/coleções/partições/leitura | Ler partições de conta de banco de dados regional em uma coleção |
> | Ação | Microsoft. DocumentDB/databaseAccounts/região/métricas/leitura | Lê as métricas de conta de banco de dados e região. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Enviar uma solicitação de restauração |
> | Ação | Microsoft. DocumentDB/databaseAccounts/usos/leitura | Lê os usos da conta do banco de dados. |
> | Ação | Microsoft. DocumentDB/databaseAccounts/Write | Atualizar contas de banco de dados. |
> | Ação | Microsoft. DocumentDB/Locations/deleteVirtualNetworkOrSubnets/Action | Notifica o Microsoft. DocumentDB que o VirtualNetwork ou a sub-rede está sendo excluída |
> | Ação | Microsoft. DocumentDB/Locations/deleteVirtualNetworkOrSubnets/operationResults/Read | Ler o status da operação assíncrona deleteVirtualNetworkOrSubnets |
> | Ação | Microsoft. DocumentDB/Locations/operationsStatus/Read | Lê o status de operações assíncronas |
> | Ação | Microsoft. DocumentDB/operationResults/Read | Ler o status da operação assíncrona |
> | Ação | Microsoft. DocumentDB/operações/leitura | Operações de leitura disponíveis para o Microsoft DocumentDB  |
> | Ação | Microsoft. DocumentDB/registro/ação |  Registrar o provedor de recursos do Microsoft DocumentDB para a assinatura |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. DomainRegistration/checkDomainAvailability/Action | Verificar se um domínio está disponível para compra |
> | Ação | Microsoft. DomainRegistration/domínios/excluir | Excluir um domínio existente. |
> | Ação | Microsoft. DomainRegistration/Domains/domainownershipidentifiers/Delete | Excluir identificador de propriedade |
> | Ação | Microsoft. DomainRegistration/Domains/domainownershipidentifiers/Read | Listar identificadores de propriedade |
> | Ação | Microsoft. DomainRegistration/Domains/domainownershipidentifiers/Read | Obter identificador de propriedade |
> | Ação | Microsoft. DomainRegistration/Domains/domainownershipidentifiers/Write | Criar ou atualizar identificador |
> | Ação | Microsoft. DomainRegistration/Domains/operationresults/Read | Obter uma operação de domínio |
> | Ação | Microsoft. DomainRegistration/domínios/ler | Obter a lista de domínios |
> | Ação | Microsoft. DomainRegistration/domínios/ler | Obter domínio |
> | Ação | Microsoft. DomainRegistration/domínios/renovar/ação | Renovar um domínio existente. |
> | Ação | Microsoft. DomainRegistration/Domains/Write | Adicionar um novo domínio ou atualizar um existente |
> | Ação | Microsoft. DomainRegistration/generateSsoRequest/Action | Gerar uma solicitação para entrar no centro de controle de domínio. |
> | Ação | Microsoft. DomainRegistration/listDomainRecommendations/Action | Recuperar as recomendações de domínio da lista com base em palavras-chave |
> | Ação | Microsoft. DomainRegistration/operações/leitura | Listar todas as operações do registro de domínio do serviço de aplicativo |
> | Ação | Microsoft. DomainRegistration/registro/ação | Registrar o provedor de recursos de domínios Microsoft para a assinatura |
> | Ação | Microsoft. DomainRegistration/topLevelDomains/listAgreements/Action | Listar ação de contrato |
> | Ação | Microsoft. DomainRegistration/topLevelDomains/Read | Obter domínios do TopLevel |
> | Ação | Microsoft. DomainRegistration/topLevelDomains/Read | Obter domínio TopLevel |
> | Ação | Microsoft. DomainRegistration/validateDomainRegistrationInformation/Action | Validar o objeto de compra de domínio sem enviá-lo |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. EventGrid/domínios/excluir | Excluir um domínio |
> | Ação | Microsoft. EventGrid/domínios/listKeys/Action | Listar chaves para um domínio |
> | Ação | Microsoft. EventGrid/Domains/Providers/Microsoft. insights/metricDefinitions/Read | Obter as métricas disponíveis para domínios |
> | Ação | Microsoft. EventGrid/domínios/ler | Ler um domínio |
> | Ação | Microsoft. EventGrid/domínios/regenerateKey/Action | Regenerar chave para um domínio |
> | Ação | Microsoft. EventGrid/Domains/topics/Delete | Tópico excluir um domínio |
> | Ação | Microsoft. EventGrid/domínios/tópicos/ler | Ler um tópico de domínio |
> | Ação | Microsoft. EventGrid/domínios/tópicos/gravação | Criar ou atualizar um tópico de domínio |
> | Ação | Microsoft. EventGrid/Domains/Write | Criar ou atualizar um domínio |
> | Ação | Microsoft. EventGrid/eventSubscriptions/Delete | Excluir um eventSubscription |
> | Ação | Microsoft. EventGrid/eventSubscriptions/getFullUrl/Action | Obter URL completa para a assinatura de evento |
> | Ação | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. insights/diagnosticSettings/Read | Obter a configuração de diagnóstico para assinaturas de evento |
> | Ação | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para assinaturas de evento |
> | Ação | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. insights/metricDefinitions/Read | Obter as métricas disponíveis para eventSubscriptions |
> | Ação | Microsoft. EventGrid/eventSubscriptions/Read | Ler um eventSubscription |
> | Ação | Microsoft. EventGrid/eventSubscriptions/Write | Criar ou atualizar um eventSubscription |
> | Ação | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. insights/diagnosticSettings/Read | Obtém a configuração de diagnóstico para tópicos |
> | Ação | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. insights/diagnosticSettings/Write | Criar ou atualizar a configuração de diagnóstico para tópicos |
> | Ação | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. insights/metricDefinitions/Read | Obter as métricas disponíveis para tópicos |
> | Ação | Microsoft. EventGrid/extensionTopics/Read | Ler um extensionTopic. |
> | Ação | Microsoft. EventGrid/Locations/eventSubscriptions/Read | Listar assinaturas de eventos regionais |
> | Ação | Microsoft. EventGrid/Locations/operationResults/Read | Ler o resultado de uma operação regional |
> | Ação | Microsoft. EventGrid/Locations/operationsStatus/Read | Ler o status de uma operação regional |
> | Ação | Microsoft. EventGrid/Locations/topictypes/eventSubscriptions/Read | Listar assinaturas de evento regional por topictype |
> | Ação | Microsoft. EventGrid/operationResults/Read | Ler o resultado de uma operação |
> | Ação | Microsoft. EventGrid/operações/leitura | Liste as operações EventGrid. |
> | Ação | Microsoft. EventGrid/operationsStatus/Read | Ler o status de uma operação |
> | Ação | Microsoft. EventGrid/registro/ação | Registra a assinatura para o provedor de recursos EventGrid. |
> | Ação | Microsoft. EventGrid/topics/Delete | Excluir um tópico |
> | Ação | Microsoft. EventGrid/topics/listKeys/Action | Listar chaves para um tópico |
> | Ação | Microsoft. EventGrid/tópicos/Providers/Microsoft. insights/diagnosticSettings/Read | Obtém a configuração de diagnóstico para tópicos |
> | Ação | Microsoft. EventGrid/tópicos/Providers/Microsoft. insights/diagnosticSettings/Write | Criar ou atualizar a configuração de diagnóstico para tópicos |
> | Ação | Microsoft. EventGrid/tópicos/Providers/Microsoft. insights/metricDefinitions/Read | Obter as métricas disponíveis para tópicos |
> | Ação | Microsoft. EventGrid/tópicos/ler | Ler um tópico |
> | Ação | Microsoft. EventGrid/topics/regenerateKey/Action | Regenerar chave para um tópico |
> | Ação | Microsoft. EventGrid/tópicos/gravar | Criar ou atualizar um tópico |
> | Ação | Microsoft. EventGrid/topictypes/eventSubscriptions/Read | Listar assinaturas de evento global por tipo de tópico |
> | Ação | Microsoft. EventGrid/topictypes/EventTypes/Read | Ler EventTypes com suporte de um topictype |
> | Ação | Microsoft. EventGrid/topictypes/Read | Ler um topictype |
> | Ação | Microsoft. EventGrid/cancelar registro/ação | Cancela o registro da assinatura para o provedor de recursos EventGrid. |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. EventHub/availableClusterRegions/Read | Operação de leitura para listar clusters pré-configurados disponíveis pela região do Azure. |
> | Ação | Microsoft. EventHub/checkNameAvailability/Action | Verifica a disponibilidade do namespace sob determinada assinatura. |
> | Ação | Microsoft. EventHub/checkNamespaceAvailability/Action | Verifica a disponibilidade do namespace sob determinada assinatura. Esta API foi preterida, use CheckNameAvailability em vez disso. |
> | Ação | Microsoft. EventHub/clusters/excluir | Exclui um recurso de cluster existente. |
> | Ação | Microsoft. EventHub/clusters/namespaces/leitura | Listar IDs de ARM de namespace para namespaces em um cluster. |
> | Ação | Microsoft. EventHub/clusters/operationresults/Read | Obtenha o status de uma operação de cluster assíncrona. |
> | Ação | Microsoft. EventHub/clusters/Providers/Microsoft. insights/metricDefinitions/Read | Obter lista de descrições de recursos de métricas de cluster |
> | Ação | Microsoft. EventHub/clusters/leitura | Obtém a descrição do recurso de cluster |
> | Ação | Microsoft. EventHub/clusters/Write | Cria ou modifica um recurso de cluster existente. |
> | Ação | Microsoft. EventHub/Locations/deleteVirtualNetworkOrSubnets/Action | Exclui as regras de VNet no provedor de recursos do EventHub para a VNet especificada |
> | Ação | Microsoft. EventHub/namespaces/authorizationRules/Action | Regra de autorização de namespace de atualizações. Esta API foi preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace em vez disso. Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft. EventHub/namespaces/authorizationRules/Delete | Excluir regra de autorização de namespace. A regra de autorização de namespace padrão não pode ser excluída.  |
> | Ação | Microsoft. EventHub/namespaces/authorizationRules/listkeys/Action | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft. EventHub/namespaces/authorizationRules/Read | Obtenha a lista de descrições de regras de autorização de namespaces. |
> | Ação | Microsoft. EventHub/namespaces/authorizationRules/regenerateKeys/Action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft. EventHub/namespaces/authorizationRules/Write | Crie regras de autorização de nível de namespace e atualize suas propriedades. Os direitos de acesso às regras de autorização, as chaves primária e secundária podem ser atualizadas. |
> | Ação | Microsoft. EventHub/namespaces/excluir | Excluir recurso de namespace |
> | Ação | Microsoft. EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/Action | Obter as chaves de regras de autorização para o namespace primário de recuperação de desastre |
> | Ação | Microsoft. EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/Read | Obter regras de autorização do namespace primário de recuperação de desastre |
> | Ação | Microsoft. EventHub/namespaces/disasterRecoveryConfigs/breakPairing/Action | Desabilita a recuperação de desastres e interrompe a replicação de alterações de namespaces primários para secundários. |
> | Ação | Microsoft. EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/Action | Verifica a disponibilidade do alias de namespace na assinatura fornecida. |
> | Ação | Microsoft. EventHub/namespaces/disasterRecoveryConfigs/Delete | Exclui a configuração de recuperação de desastre associada ao namespace. Esta operação só pode ser invocada por meio do namespace primário. |
> | Ação | Microsoft. EventHub/namespaces/disasterRecoveryConfigs/failover/ação | Invoca um failover de DR geográfica e reconfigura o alias de namespace para apontar para o namespace secundário. |
> | Ação | Microsoft. EventHub/namespaces/disasterRecoveryConfigs/Read | Obtém a configuração de recuperação de desastre associada ao namespace. |
> | Ação | Microsoft. EventHub/namespaces/disasterRecoveryConfigs/Write | Cria ou atualiza a configuração de recuperação de desastre associada ao namespace. |
> | Ação | Microsoft. EventHub/namespaces/Eventhubs/authorizationRules/Action | Operação para atualizar o EventHub. Não há suporte para esta operação na versão de API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft. EventHub/namespaces/Eventhubs/authorizationRules/Delete | Operação para excluir regras de autorização do EventHub |
> | Ação | Microsoft. EventHub/namespaces/Eventhubs/authorizationRules/listkeys/Action | Obter a cadeia de conexão para o EventHub |
> | Ação | Microsoft. EventHub/namespaces/Eventhubs/authorizationRules/Read |  Obter a lista de regras de autorização do EventHub |
> | Ação | Microsoft. EventHub/namespaces/Eventhubs/authorizationRules/regenerateKeys/Action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft. EventHub/namespaces/Eventhubs/authorizationRules/Write | Crie regras de autorização do EventHub e atualize suas propriedades. Os direitos de acesso das regras de autorização podem ser atualizados. |
> | Ação | Microsoft. EventHub/namespaces/eventHubs/consumergroups/Delete | Operação para excluir o recurso de um consumidor |
> | Ação | Microsoft. EventHub/namespaces/eventHubs/consumergroups/Read | Obter lista de descrições de recursos do The Consumership |
> | Ação | Microsoft. EventHub/namespaces/eventHubs/consumergroups/Write | Crie ou atualize as propriedades de um dos consumidores. |
> | Ação | Microsoft. EventHub/namespaces/Eventhubs/Delete | Operação para excluir o recurso do EventHub |
> | Ação | Microsoft. EventHub/namespaces/Eventhubs/Read | Obter lista de descrições de recursos do EventHub |
> | Ação | Microsoft. EventHub/namespaces/Eventhubs/Write | Criar ou atualizar propriedades do EventHub. |
> | Ação | Microsoft. EventHub/namespaces/ipFilterRules/Delete | Excluir recurso de filtro IP |
> | Ação | Microsoft. EventHub/namespaces/ipFilterRules/Read | Obter recurso de filtro IP |
> | Ação | Microsoft. EventHub/namespaces/ipFilterRules/Write | Criar recurso de filtro IP |
> | Dataaction | Microsoft. EventHub/namespaces/mensagens/recebimento/ação | Receber mensagens |
> | Dataaction | Microsoft. EventHub/namespaces/mensagens/enviar/ação | Enviar mensagens |
> | Ação | Microsoft. EventHub/namespaces/messagingPlan/Read | Obtém o plano de mensagens para um namespace.<br>Esta API foi preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso de namespace (pai) em versões de API posteriores.<br>Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft. EventHub/namespaces/messagingPlan/Write | Atualiza o plano de mensagens para um namespace.<br>Esta API foi preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso de namespace (pai) em versões de API posteriores.<br>Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft. EventHub/namespaces/networkruleset/Delete | Excluir recurso de regra de VNET |
> | Ação | Microsoft. EventHub/namespaces/networkruleset/Read | Obtém o recurso NetworkRuleSet |
> | Ação | Microsoft. EventHub/namespaces/networkruleset/Write | Criar recurso de regra de VNET |
> | Ação | Microsoft. EventHub/namespaces/networkrulesets/Delete | Excluir recurso de regra de VNET |
> | Ação | Microsoft. EventHub/namespaces/networkrulesets/Read | Obtém o recurso NetworkRuleSet |
> | Ação | Microsoft. EventHub/namespaces/networkrulesets/Write | Criar recurso de regra de VNET |
> | Ação | Microsoft. EventHub/namespaces/operationresults/Read | Obter o status da operação de namespace |
> | Ação | Microsoft. EventHub/namespaces/Providers/Microsoft. insights/diagnosticSettings/Read | Obter lista de descrições de recursos das configurações de diagnóstico de namespace |
> | Ação | Microsoft. EventHub/namespaces/Providers/Microsoft. insights/diagnosticSettings/Write | Obter lista de descrições de recursos das configurações de diagnóstico de namespace |
> | Ação | Microsoft. EventHub/namespaces/Providers/Microsoft. insights/logDefinitions/Read | Obter lista de descrições de recursos de logs de namespace |
> | Ação | Microsoft. EventHub/namespaces/Providers/Microsoft. insights/metricDefinitions/Read | Obter lista de descrições de recursos de métricas de namespace |
> | Ação | Microsoft. EventHub/namespaces/leitura | Obter a lista de descrição de recurso de namespace |
> | Ação | Microsoft. EventHub/namespaces/removeAcsNamepsace/Action | Remover namespace do ACS |
> | Ação | Microsoft. EventHub/namespaces/virtualNetworkRules/Delete | Excluir recurso de regra de VNET |
> | Ação | Microsoft. EventHub/namespaces/virtualNetworkRules/Read | Obter recurso de regra de VNET |
> | Ação | Microsoft. EventHub/namespaces/virtualNetworkRules/Write | Criar recurso de regra de VNET |
> | Ação | Microsoft. EventHub/namespaces/gravação | Crie um recurso de namespace e atualize suas propriedades. As marcas e a capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft. EventHub/operações/leitura | Obter operações |
> | Ação | Microsoft. EventHub/registro/ação | Registra a assinatura para o provedor de recursos do EventHub e habilita a criação de recursos do EventHub |
> | Ação | Microsoft. EventHub/SKU/leitura | Obter lista de descrições de recursos de SKU |
> | Ação | Microsoft. EventHub/SKU/regions/Read | Obter lista de descrições de recursos SkuRegions |
> | Ação | Microsoft. EventHub/cancelamento/registro/ação | Registra o provedor de recursos do EventHub |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. recursos/recursos/ler | Obtém os recursos de uma assinatura. |
> | Ação | Microsoft. recursos/operações/leitura | Obtém a lista de operações. |
> | Ação | Microsoft. Features/Providers/Features/Read | Obtém o recurso de uma assinatura em um determinado provedor de recursos. |
> | Ação | Microsoft. recursos/provedores/recursos/registro/ação | Registra o recurso para uma assinatura em um determinado provedor de recursos. |
> | Ação | Microsoft. recursos/provedores/recursos/cancelar registro/ação | Cancela o registro do recurso para uma assinatura em um determinado provedor de recursos. |
> | Ação | Microsoft. Features/registro/ação | Registra o recurso de uma assinatura. |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. GuestConfiguration/guestConfigurationAssignments/Delete | Excluir atribuição de configuração de convidado. |
> | Ação | Microsoft. GuestConfiguration/guestConfigurationAssignments/Read | Obter atribuição de configuração de convidado. |
> | Ação | Microsoft. GuestConfiguration/guestConfigurationAssignments/Reports/Read | Obter relatório de atribuição de configuração de convidado. |
> | Ação | Microsoft. GuestConfiguration/guestConfigurationAssignments/Write | Criar nova atribuição de configuração de convidado. |
> | Ação | Microsoft. GuestConfiguration/operações/leitura | Obtém as operações para o provedor de recursos Microsoft. GuestConfiguration |
> | Ação | Microsoft. GuestConfiguration/registro/ação | Registra a assinatura para o provedor de recursos Microsoft. GuestConfiguration. |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. HDInsight/clusters/aplicativos/excluir | Excluir aplicativo para o cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/aplicativos/leitura | Obter aplicativo para o cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/aplicativos/gravação | Criar ou atualizar aplicativo para o cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/changerdpsetting/ação | Alterar configuração de RDP para cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/Configurations/Action | Atualizar a configuração do cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/Configurations/Action | Obter configurações de cluster do HDInsight |
> | Ação | Microsoft. HDInsight/clusters/configurações/leitura | Obter configurações de cluster do HDInsight |
> | Ação | Microsoft. HDInsight/clusters/excluir | Excluir um cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/extensões/excluir | Excluir a extensão de cluster do cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/extensões/leitura | Obter a extensão do cluster para o cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/extensões/gravação | Criar extensão de cluster para o cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/getGatewaySettings/ação | Obter configurações de gateway para o cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/Providers/Microsoft. insights/diagnosticSettings/Read | Obter a configuração de diagnóstico para o cluster HDInsight de recursos |
> | Ação | Microsoft. HDInsight/clusters/Providers/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o cluster HDInsight de recurso |
> | Ação | Microsoft. HDInsight/clusters/Providers/Microsoft. insights/metricDefinitions/Read | Obter as métricas disponíveis para o cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/leitura | Obter detalhes sobre o cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/funções/redimensionamento/ação | Redimensionar um cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/updateGatewaySettings/ação | Atualizar as configurações do gateway para o cluster HDInsight |
> | Ação | Microsoft. HDInsight/clusters/Write | Criar ou atualizar o cluster HDInsight |
> | Ação | Microsoft. HDInsight/Locations/Capabilities/Read | Obter recursos de assinatura |
> | Ação | Microsoft. HDInsight/Locations/checkNameAvailability/Read | Verificar a disponibilidade do nome |
> | Ação | Microsoft. HDInsight/registro/ação | Registrar o provedor de recursos do HDInsight para a assinatura |
> | Ação | Microsoft. HDInsight/cancelar registro/ação | Cancelar registro do provedor de recursos do HDInsight para a assinatura |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ImportExport/Jobs/excluir | Exclui um trabalho existente. |
> | Ação | Microsoft. ImportExport/Jobs/listBitLockerKeys/ação | Obtém as chaves do BitLocker para o trabalho especificado. |
> | Ação | Microsoft. ImportExport/Jobs/ler | Obtém as propriedades do trabalho especificado ou retorna a lista de trabalhos. |
> | Ação | Microsoft. ImportExport/Jobs/Write | Cria um trabalho com os parâmetros especificados ou atualiza as propriedades ou marcas para o trabalho especificado. |
> | Ação | Microsoft. ImportExport/localizações/leitura | Obtém as propriedades para o local especificado ou retorna a lista de locais. |
> | Ação | Microsoft. ImportExport/operações/leitura | Obtém as operações com suporte do provedor de recursos. |
> | Ação | Microsoft. ImportExport/registro/ação | Registra a assinatura para o provedor de recursos de importação/exportação e habilita a criação de trabalhos de importação/exportação. |

## <a name="microsoftinsights"></a>Microsoft. insights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. insights/ActionGroups/Delete | Excluir um grupo de ações |
> | Ação | Microsoft. insights/ActionGroups/Read | Ler um grupo de ação |
> | Ação | Microsoft. insights/ActionGroups/Write | Criar ou atualizar um grupo de ações |
> | Ação | Microsoft. insights/ActivityLogAlerts/ativado/ação | Alerta do log de atividades ativado |
> | Ação | Microsoft. insights/ActivityLogAlerts/Delete | Excluir um alerta do log de atividades |
> | Ação | Microsoft. insights/ActivityLogAlerts/Read | Ler um alerta do log de atividades |
> | Ação | Microsoft. insights/ActivityLogAlerts/Write | Criar ou atualizar um alerta do log de atividades |
> | Ação | Microsoft. insights/AlertRules/ativado/ação | Alerta de métrica clássico ativado |
> | Ação | Microsoft. insights/AlertRules/Delete | Excluir um alerta de métrica clássico |
> | Ação | Microsoft. insights/AlertRules/incidents/Read | Ler um incidente de alerta de métrica clássico |
> | Ação | Microsoft. insights/AlertRules/Read | Ler um alerta de métrica clássico |
> | Ação | Microsoft. insights/AlertRules/resolvê/Action | Alerta de métrica clássico resolvido |
> | Ação | Microsoft. insights/AlertRules/restrição/ação | Regra de alerta de métrica clássica limitada |
> | Ação | Microsoft. insights/AlertRules/Write | Criar ou atualizar um alerta de métrica clássico |
> | Ação | Microsoft. insights/AutoscaleSettings/Delete | Excluir uma configuração de dimensionamento automático |
> | Ação | Microsoft. insights/AutoscaleSettings/Providers/Microsoft. insights/diagnosticSettings/Read | Ler uma configuração de diagnóstico de recurso |
> | Ação | Microsoft. insights/AutoscaleSettings/Providers/Microsoft. insights/diagnosticSettings/Write | Criar ou atualizar uma configuração de diagnóstico de recurso |
> | Ação | Microsoft. insights/AutoscaleSettings/Providers/Microsoft. insights/logDefinitions/Read | Ler definições de log |
> | Ação | Microsoft. insights/AutoscaleSettings/Providers/Microsoft. insights/MetricDefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft. insights/AutoscaleSettings/Read | Ler uma configuração de dimensionamento automático |
> | Ação | Microsoft. insights/AutoscaleSettings/scaledown/ação | Redução de escala automática iniciada |
> | Ação | Microsoft. insights/AutoscaleSettings/ScaledownResult/ação | Redução de escala automática concluída |
> | Ação | Microsoft. insights/AutoscaleSettings/escalável/ação | Escala vertical redimensionada iniciada |
> | Ação | Microsoft. insights/AutoscaleSettings/ScaleupResult/ação | Dimensionamento automático dimensionado concluído |
> | Ação | Microsoft. insights/AutoscaleSettings/Write | Criar ou atualizar uma configuração de dimensionamento automático |
> | Ação | Microsoft. insights/linha de base/leitura | Ler uma linha de base de métrica (versão prévia) |
> | Ação | Microsoft. insights/CalculateBaseline/Read | Calcular a linha de base para valores de métrica (versão prévia) |
> | Ação | Microsoft. insights/componentes/AnalyticsItems/Delete | Excluindo um item de análise de Application Insights |
> | Ação | Microsoft. insights/Components/AnalyticsItems/Read | Lendo um item de análise de Application Insights |
> | Ação | Microsoft. insights/Components/AnalyticsItems/Write | Escrevendo um item de análise de Application Insights |
> | Ação | Microsoft. insights/componentes/AnalyticsTables/Action | Ação da tabela de análise de Application Insights |
> | Ação | Microsoft. insights/componentes/AnalyticsTables/Delete | Excluindo um esquema de tabela de análise de Application Insights |
> | Ação | Microsoft. insights/Components/AnalyticsTables/Read | Lendo um esquema de tabela de análise de Application Insights |
> | Ação | Microsoft. insights/Components/AnalyticsTables/Write | Escrevendo um esquema de tabela de análise de Application Insights |
> | Ação | Microsoft. insights/componentes/anotações/excluir | Excluindo uma anotação de Application Insights |
> | Ação | Microsoft. insights/componentes/anotações/leitura | Lendo uma anotação de Application Insights |
> | Ação | Microsoft. insights/componentes/anotações/gravação | Escrevendo uma anotação de Application Insights |
> | Ação | Microsoft. insights/componentes/API/leitura | Lendo Application Insights API de dados de componente |
> | Ação | Microsoft. insights/componentes/ApiKeys/Action | Gerando uma chave de API de Application Insights |
> | Ação | Microsoft. insights/componentes/ApiKeys/Delete | Excluindo uma chave de API de Application Insights |
> | Ação | Microsoft. insights/Components/ApiKeys/Read | Lendo uma chave de API de Application Insights |
> | Ação | Microsoft. insights/Components/BillingPlanForComponent/Read | Lendo um plano de cobrança para o componente Application Insights |
> | Ação | Microsoft. insights/Components/CurrentBillingFeatures/Read | Lendo os recursos de cobrança atuais para o componente Application Insights |
> | Ação | Microsoft. insights/Components/CurrentBillingFeatures/Write | Escrevendo recursos de cobrança atuais para o componente Application Insights |
> | Ação | Microsoft. insights/componentes/DailyCapReached/Action | Atingido o limite diário para o componente Application Insights |
> | Ação | Microsoft. insights/componentes/DailyCapWarningThresholdReached/Action | Limite de aviso de limite diário atingido para o componente Application Insights |
> | Ação | Microsoft. insights/Components/DefaultWorkItemConfig/Read | Lendo uma configuração de integração de ALM Application Insights padrão |
> | Ação | Microsoft. insights/componentes/excluir | Excluindo uma configuração de componente do Application insights |
> | Ação | Microsoft. insights/componentes/eventos/leitura | Obter logs de Application Insights usando o formato de consulta OData |
> | Ação | Microsoft. insights/componentes/ExportConfiguration/Action | Ação Application Insights exportar configurações |
> | Ação | Microsoft. insights/componentes/ExportConfiguration/Delete | Excluindo Application Insights configurações de exportação |
> | Ação | Microsoft. insights/Components/ExportConfiguration/Read | Lendo Application Insights configurações de exportação |
> | Ação | Microsoft. insights/Components/ExportConfiguration/Write | Gravando Application Insights configurações de exportação |
> | Ação | Microsoft. insights/Components/ExtendQueries/Read | Lendo Application Insights resultados da consulta estendida do componente |
> | Ação | Microsoft. insights/componentes/favoritos/excluir | Excluindo um Application Insights favorito |
> | Ação | Microsoft. insights/componentes/favoritos/leitura | Lendo um Application Insights favorito |
> | Ação | Microsoft. insights/componentes/favoritos/gravação | Escrevendo um Application Insights favorito |
> | Ação | Microsoft. insights/Components/FeatureCapabilities/Read | Lendo recursos de recurso de componente Application Insights |
> | Ação | Microsoft. insights/Components/GetAvailableBillingFeatures/Read | Lendo os recursos de cobrança disponíveis do componente Application Insights |
> | Ação | Microsoft. insights/componentes/GetToken/leitura | Lendo um token de componente de Application Insights |
> | Ação | Microsoft. insights/Components/MetricDefinitions/Read | Lendo definições de métrica de componente Application Insights |
> | Ação | Microsoft. insights/componentes/métricas/leitura | Lendo Application Insights métricas de componente |
> | Ação | Microsoft. insights/componentes/mover/ação | Mover um componente Application Insights para outro grupo de recursos ou assinatura |
> | Ação | Microsoft. insights/componentes/MyAnalyticsItems/Delete | Excluindo um Application Insights item de análise pessoal |
> | Ação | Microsoft. insights/Components/MyAnalyticsItems/Read | Lendo um Application Insights item de análise pessoal |
> | Ação | Microsoft. insights/Components/MyAnalyticsItems/Write | Escrevendo um item de análise pessoal de Application Insights |
> | Ação | Microsoft. insights/componentes/myfavoritass/Read | Lendo um Application Insights favorito pessoal |
> | Ação | Microsoft. insights/componentes/operações/leitura | Obter o status das operações de execução longa no Application Insights |
> | Ação | Microsoft. insights/Components/PricingPlans/Read | Lendo um plano de preços do componente Application Insights |
> | Ação | Microsoft. insights/Components/PricingPlans/Write | Escrevendo um plano de preços de componente Application Insights |
> | Ação | Microsoft. insights/Components/ProactiveDetectionConfigs/Read | Lendo Application Insights configuração de detecção proativa |
> | Ação | Microsoft. insights/Components/ProactiveDetectionConfigs/Write | Gravando Application Insights configuração de detecção proativa |
> | Ação | Microsoft. insights/Components/Providers/Microsoft. insights/MetricDefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft. insights/componentes/limpeza/ação | Limpando dados de Application Insights |
> | Ação | Microsoft. insights/componentes/consulta/leitura | Executar consultas em logs de Application Insights |
> | Ação | Microsoft. insights/Components/QuotaStatus/Read | Lendo Application Insights status de cota do componente |
> | Ação | Microsoft. insights/componentes/ler | Lendo uma configuração de componente do Application insights |
> | Ação | Microsoft. insights/Components/SyntheticMonitorLocations/Read | Lendo Application Insights locais de WebTest |
> | Ação | Microsoft. insights/componentes/webtesters/Read | Lendo uma configuração de WebTest |
> | Ação | Microsoft. insights/componentes/WorkItemConfigs/Delete | Excluindo uma configuração de integração do ALM Application Insights |
> | Ação | Microsoft. insights/Components/WorkItemConfigs/Read | Lendo uma configuração de integração do ALM Application Insights |
> | Ação | Microsoft. insights/Components/WorkItemConfigs/Write | Escrevendo uma configuração de integração do ALM Application Insights |
> | Ação | Microsoft. insights/componentes/gravação | Gravando em uma configuração de componente do Application insights |
> | Ação | Microsoft. insights/DiagnosticSettings/Delete | Excluir uma configuração de diagnóstico de recurso |
> | Ação | Microsoft. insights/DiagnosticSettings/Read | Ler uma configuração de diagnóstico de recurso |
> | Ação | Microsoft. insights/DiagnosticSettings/Write | Criar ou atualizar uma configuração de diagnóstico de recurso |
> | Ação | Microsoft. insights/EventCategories/Read | Ler categorias de eventos do log de atividades disponíveis |
> | Ação | Microsoft. insights/EventTypes/digestevents/Read | Ler Resumo do tipo de evento de gerenciamento |
> | Ação | Microsoft. insights/EventTypes/valores/leitura | Ler eventos do log de atividades |
> | Ação | Microsoft. insights/ExtendedDiagnosticSettings/Delete | Excluir uma configuração de diagnóstico de log de fluxo de rede |
> | Ação | Microsoft. insights/ExtendedDiagnosticSettings/Read | Ler uma configuração de diagnóstico do log de fluxo de rede |
> | Ação | Microsoft. insights/ExtendedDiagnosticSettings/Write | Criar ou atualizar uma configuração de diagnóstico de log de fluxo de rede |
> | Ação | Microsoft. insights/ListMigrationDate/ação | Obter a data de migração da assinatura |
> | Ação | Microsoft. insights/ListMigrationDate/Read | Obter a data de migração da assinatura |
> | Ação | Microsoft. insights/LogDefinitions/Read | Ler definições de log |
> | Ação | Microsoft. insights/LogProfiles/Delete | Excluir um perfil de log de atividades |
> | Ação | Microsoft. insights/LogProfiles/Read | Ler um perfil de log de atividades |
> | Ação | Microsoft. insights/LogProfiles/Write | Criar ou atualizar um perfil de log de atividades |
> | Ação | Microsoft. insights/logs/ADAssessmentRecommendation/leitura | Ler dados da tabela ADAssessmentRecommendation |
> | Ação | Microsoft. insights/logs/ADReplicationResult/leitura | Ler dados da tabela ADReplicationResult |
> | Ação | Microsoft. insights/logs/ADSecurityAssessmentRecommendation/leitura | Ler dados da tabela ADSecurityAssessmentRecommendation |
> | Ação | Microsoft. insights/logs/alerta/leitura | Ler dados da tabela de alertas |
> | Ação | Microsoft. insights/logs/AlertHistory/leitura | Ler dados da tabela AlertHistory |
> | Ação | Microsoft. insights/logs/ApplicationInsights/leitura | Ler dados da tabela ApplicationInsights |
> | Ação | Microsoft. insights/logs/AzureActivity/leitura | Ler dados da tabela AzureActivity |
> | Ação | Microsoft. insights/logs/AzureMetrics/leitura | Ler dados da tabela AzureMetrics |
> | Ação | Microsoft. insights/logs/BoundPort/leitura | Ler dados da tabela BoundPort |
> | Ação | Microsoft. insights/logs/CommonSecurityLog/leitura | Ler dados da tabela CommonSecurityLog |
> | Ação | Microsoft. insights/logs/computador/ler | Ler dados da tabela do computador |
> | Ação | Microsoft. insights/logs/ConfigurationChange/leitura | Ler dados da tabela ConfigurationChange |
> | Ação | Microsoft. insights/logs/ConfigurationData/leitura | Ler dados da tabela ConfigurationData |
> | Ação | Microsoft. insights/logs/ContainerImageInventory/leitura | Ler dados da tabela ContainerImageInventory |
> | Ação | Microsoft. insights/logs/ContainerInventory/leitura | Ler dados da tabela ContainerInventory |
> | Ação | Microsoft. insights/logs/ContainerLog/leitura | Ler dados da tabela ContainerLog |
> | Ação | Microsoft. insights/logs/ContainerServiceLog/leitura | Ler dados da tabela ContainerServiceLog |
> | Ação | Microsoft. insights/logs/DeviceAppCrash/leitura | Ler dados da tabela DeviceAppCrash |
> | Ação | Microsoft. insights/logs/DeviceAppLaunch/leitura | Ler dados da tabela DeviceAppLaunch |
> | Ação | Microsoft. insights/logs/DeviceCalendar/leitura | Ler dados da tabela DeviceCalendar |
> | Ação | Microsoft. insights/logs/DeviceCleanup/leitura | Ler dados da tabela DeviceCleanup |
> | Ação | Microsoft. insights/logs/DeviceConnectSession/leitura | Ler dados da tabela DeviceConnectSession |
> | Ação | Microsoft. insights/logs/DeviceEtw/leitura | Ler dados da tabela DeviceEtw |
> | Ação | Microsoft. insights/logs/DeviceHardwareHealth/leitura | Ler dados da tabela DeviceHardwareHealth |
> | Ação | Microsoft. insights/logs/DeviceHealth/leitura | Ler dados da tabela DeviceHealth |
> | Ação | Microsoft. insights/logs/DeviceHeartbeat/leitura | Ler dados da tabela DeviceHeartbeat |
> | Ação | Microsoft. insights/logs/DeviceSkypeHeartbeat/leitura | Ler dados da tabela DeviceSkypeHeartbeat |
> | Ação | Microsoft. insights/logs/DeviceSkypeSignIn/leitura | Ler dados da tabela DeviceSkypeSignIn |
> | Ação | Microsoft. insights/logs/DeviceSleepState/leitura | Ler dados da tabela DeviceSleepState |
> | Ação | Microsoft. insights/logs/DHAppFailure/leitura | Ler dados da tabela DHAppFailure |
> | Ação | Microsoft. insights/logs/DHAppReliability/leitura | Ler dados da tabela DHAppReliability |
> | Ação | Microsoft. insights/logs/DHDriverReliability/leitura | Ler dados da tabela DHDriverReliability |
> | Ação | Microsoft. insights/logs/DHLogonFailures/leitura | Ler dados da tabela DHLogonFailures |
> | Ação | Microsoft. insights/logs/DHLogonMetrics/leitura | Ler dados da tabela DHLogonMetrics |
> | Ação | Microsoft. insights/logs/DHOSCrashData/leitura | Ler dados da tabela DHOSCrashData |
> | Ação | Microsoft. insights/logs/DHOSReliability/leitura | Ler dados da tabela DHOSReliability |
> | Ação | Microsoft. insights/logs/DHWipAppLearning/leitura | Ler dados da tabela DHWipAppLearning |
> | Ação | Microsoft. insights/logs/DnsEvents/leitura | Ler dados da tabela DnsEvents |
> | Ação | Microsoft. insights/logs/DnsInventory/leitura | Ler dados da tabela DnsInventory |
> | Ação | Microsoft. insights/logs/ETWEvent/leitura | Ler dados da tabela ETWEvent |
> | Ação | Microsoft. insights/logs/evento/leitura | Ler dados da tabela de eventos |
> | Ação | Microsoft. insights/logs/ExchangeAssessmentRecommendation/leitura | Ler dados da tabela ExchangeAssessmentRecommendation |
> | Ação | Microsoft. insights/logs/ExchangeOnlineAssessmentRecommendation/leitura | Ler dados da tabela ExchangeOnlineAssessmentRecommendation |
> | Ação | Microsoft. insights/logs/pulsação/leitura | Ler dados da tabela de pulsação |
> | Ação | Microsoft. insights/logs/IISAssessmentRecommendation/leitura | Ler dados da tabela IISAssessmentRecommendation |
> | Ação | Microsoft. insights/logs/InboundConnection/leitura | Ler dados da tabela InboundConnection |
> | Ação | Microsoft. insights/logs/KubeNodeInventory/leitura | Ler dados da tabela KubeNodeInventory |
> | Ação | Microsoft. insights/logs/KubePodInventory/leitura | Ler dados da tabela KubePodInventory |
> | Ação | Microsoft. insights/logs/LinuxAuditLog/leitura | Ler dados da tabela LinuxAuditLog |
> | Ação | Microsoft. insights/logs/MAApplication/leitura | Ler dados da tabela MAApplication |
> | Ação | Microsoft. insights/logs/MAApplicationHealth/leitura | Ler dados da tabela MAApplicationHealth |
> | Ação | Microsoft. insights/logs/MAApplicationHealthAlternativeVersions/leitura | Ler dados da tabela MAApplicationHealthAlternativeVersions |
> | Ação | Microsoft. insights/logs/MAApplicationHealthIssues/leitura | Ler dados da tabela MAApplicationHealthIssues |
> | Ação | Microsoft. insights/logs/MAApplicationInstance/leitura | Ler dados da tabela MAApplicationInstance |
> | Ação | Microsoft. insights/logs/MAApplicationInstanceReadiness/leitura | Ler dados da tabela MAApplicationInstanceReadiness |
> | Ação | Microsoft. insights/logs/MAApplicationReadiness/leitura | Ler dados da tabela MAApplicationReadiness |
> | Ação | Microsoft. insights/logs/MADeploymentPlan/leitura | Ler dados da tabela MADeploymentPlan |
> | Ação | Microsoft. insights/logs/MADevice/leitura | Ler dados da tabela MADevice |
> | Ação | Microsoft. insights/logs/MADevicePnPHealth/leitura | Ler dados da tabela MADevicePnPHealth |
> | Ação | Microsoft. insights/logs/MADevicePnPHealthAlternativeVersions/leitura | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | Ação | Microsoft. insights/logs/MADevicePnPHealthIssues/leitura | Ler dados da tabela MADevicePnPHealthIssues |
> | Ação | Microsoft. insights/logs/MADeviceReadiness/leitura | Ler dados da tabela MADeviceReadiness |
> | Ação | Microsoft. insights/logs/MADriverInstanceReadiness/leitura | Ler dados da tabela MADriverInstanceReadiness |
> | Ação | Microsoft. insights/logs/MADriverReadiness/leitura | Ler dados da tabela MADriverReadiness |
> | Ação | Microsoft. insights/logs/MAOfficeAddin/leitura | Ler dados da tabela MAOfficeAddin |
> | Ação | Microsoft. insights/logs/MAOfficeAddinHealth/leitura | Ler dados da tabela MAOfficeAddinHealth |
> | Ação | Microsoft. insights/logs/MAOfficeAddinHealthIssues/leitura | Ler dados da tabela MAOfficeAddinHealthIssues |
> | Ação | Microsoft. insights/logs/MAOfficeAddinInstance/leitura | Ler dados da tabela MAOfficeAddinInstance |
> | Ação | Microsoft. insights/logs/MAOfficeAddinInstanceReadiness/leitura | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | Ação | Microsoft. insights/logs/MAOfficeAddinReadiness/leitura | Ler dados da tabela MAOfficeAddinReadiness |
> | Ação | Microsoft. insights/logs/MAOfficeApp/leitura | Ler dados da tabela MAOfficeApp |
> | Ação | Microsoft. insights/logs/MAOfficeAppHealth/leitura | Ler dados da tabela MAOfficeAppHealth |
> | Ação | Microsoft. insights/logs/MAOfficeAppInstance/leitura | Ler dados da tabela MAOfficeAppInstance |
> | Ação | Microsoft. insights/logs/MAOfficeAppReadiness/leitura | Ler dados da tabela MAOfficeAppReadiness |
> | Ação | Microsoft. insights/logs/MAOfficeBuildInfo/leitura | Ler dados da tabela MAOfficeBuildInfo |
> | Ação | Microsoft. insights/logs/MAOfficeCurrencyAssessment/leitura | Ler dados da tabela MAOfficeCurrencyAssessment |
> | Ação | Microsoft. insights/logs/MAOfficeCurrencyAssessmentDailyCounts/leitura | Ler dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | Ação | Microsoft. insights/logs/MAOfficeDeploymentStatus/leitura | Ler dados da tabela MAOfficeDeploymentStatus |
> | Ação | Microsoft. insights/logs/MAOfficeMacroHealth/leitura | Ler dados da tabela MAOfficeMacroHealth |
> | Ação | Microsoft. insights/logs/MAOfficeMacroHealthIssues/leitura | Ler dados da tabela MAOfficeMacroHealthIssues |
> | Ação | Microsoft. insights/logs/MAOfficeMacroIssueInstanceReadiness/leitura | Ler dados da tabela MAOfficeMacroIssueInstanceReadiness |
> | Ação | Microsoft. insights/logs/MAOfficeMacroIssueReadiness/leitura | Ler dados da tabela MAOfficeMacroIssueReadiness |
> | Ação | Microsoft. insights/logs/MAOfficeMacroSummary/leitura | Ler dados da tabela MAOfficeMacroSummary |
> | Ação | Microsoft. insights/logs/MAOfficeSuite/leitura | Ler dados da tabela MAOfficeSuite |
> | Ação | Microsoft. insights/logs/MAOfficeSuiteInstance/leitura | Ler dados da tabela MAOfficeSuiteInstance |
> | Ação | Microsoft. insights/logs/MAProposedPilotDevices/leitura | Ler dados da tabela MAProposedPilotDevices |
> | Ação | Microsoft. insights/logs/MAWindowsBuildInfo/leitura | Ler dados da tabela MAWindowsBuildInfo |
> | Ação | Microsoft. insights/logs/MAWindowsCurrencyAssessment/leitura | Ler dados da tabela MAWindowsCurrencyAssessment |
> | Ação | Microsoft. insights/logs/MAWindowsCurrencyAssessmentDailyCounts/leitura | Ler dados da tabela MAWindowsCurrencyAssessmentDailyCounts |
> | Ação | Microsoft. insights/logs/MAWindowsDeploymentStatus/leitura | Ler dados da tabela MAWindowsDeploymentStatus |
> | Ação | Microsoft. insights/logs/MAWindowsSysReqInstanceReadiness/leitura | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | Ação | Microsoft. insights/logs/NetworkMonitoring/leitura | Ler dados da tabela NetworkMonitoring |
> | Ação | Microsoft. insights/logs/OfficeActivity/leitura | Ler dados da tabela OfficeActivity |
> | Ação | Microsoft. insights/logs/operação/leitura | Ler dados da tabela de operações |
> | Ação | Microsoft. insights/logs/OutboundConnection/leitura | Ler dados da tabela OutboundConnection |
> | Ação | Microsoft. insights/logs/desempenho/leitura | Ler dados da tabela perf |
> | Ação | Microsoft. insights/logs/ProtectionStatus/leitura | Ler dados da tabela ProtectionStatus |
> | Ação | Microsoft. insights/logs/leitura | Lendo dados de todos os seus logs |
> | Ação | Microsoft. insights/logs/ReservedAzureCommonFields/leitura | Ler dados da tabela ReservedAzureCommonFields |
> | Ação | Microsoft. insights/logs/ReservedCommonFields/leitura | Ler dados da tabela ReservedCommonFields |
> | Ação | Microsoft. insights/logs/SCCMAssessmentRecommendation/leitura | Ler dados da tabela SCCMAssessmentRecommendation |
> | Ação | Microsoft. insights/logs/SCOMAssessmentRecommendation/leitura | Ler dados da tabela SCOMAssessmentRecommendation |
> | Ação | Microsoft. insights/logs/SecurityAlert/leitura | Ler dados da tabela SecurityAlert |
> | Ação | Microsoft. insights/logs/SecurityBaseline/leitura | Ler dados da tabela SecurityBaseline |
> | Ação | Microsoft. insights/logs/SecurityBaselineSummary/leitura | Ler dados da tabela SecurityBaselineSummary |
> | Ação | Microsoft. insights/logs/SecurityDetection/leitura | Ler dados da tabela SecurityDetection |
> | Ação | Microsoft. insights/logs/SecurityEvent/leitura | Ler dados da tabela SecurityEvent |
> | Ação | Microsoft. insights/logs/ServiceFabricOperationalEvent/leitura | Ler dados da tabela ServiceFabricOperationalEvent |
> | Ação | Microsoft. insights/logs/ServiceFabricReliableActorEvent/leitura | Ler dados da tabela ServiceFabricReliableActorEvent |
> | Ação | Microsoft. insights/logs/ServiceFabricReliableServiceEvent/leitura | Ler dados da tabela ServiceFabricReliableServiceEvent |
> | Ação | Microsoft. insights/logs/SfBAssessmentRecommendation/leitura | Ler dados da tabela SfBAssessmentRecommendation |
> | Ação | Microsoft. insights/logs/SfBOnlineAssessmentRecommendation/leitura | Ler dados da tabela SfBOnlineAssessmentRecommendation |
> | Ação | Microsoft. insights/logs/SharePointOnlineAssessmentRecommendation/leitura | Ler dados da tabela SharePointOnlineAssessmentRecommendation |
> | Ação | Microsoft. insights/logs/SPAssessmentRecommendation/leitura | Ler dados da tabela SPAssessmentRecommendation |
> | Ação | Microsoft. insights/logs/SQLAssessmentRecommendation/leitura | Ler dados da tabela SQLAssessmentRecommendation |
> | Ação | Microsoft. insights/logs/SQLQueryPerformance/leitura | Ler dados da tabela SQLQueryPerformance |
> | Ação | Microsoft. insights/logs/syslog/leitura | Ler dados da tabela syslog |
> | Ação | Microsoft. insights/logs/SysmonEvent/leitura | Ler dados da tabela SysmonEvent |
> | Ação | Microsoft. insights/logs/tabelas. personalizado/lido | Lendo dados de qualquer log personalizado |
> | Ação | Microsoft. insights/logs/UAApp/leitura | Ler dados da tabela UAApp |
> | Ação | Microsoft. insights/logs/UAComputer/leitura | Ler dados da tabela UAComputer |
> | Ação | Microsoft. insights/logs/UAComputerRank/leitura | Ler dados da tabela UAComputerRank |
> | Ação | Microsoft. insights/logs/UADriver/leitura | Ler dados da tabela UADriver |
> | Ação | Microsoft. insights/logs/UADriverProblemCodes/leitura | Ler dados da tabela UADriverProblemCodes |
> | Ação | Microsoft. insights/logs/UAFeedback/leitura | Ler dados da tabela UAFeedback |
> | Ação | Microsoft. insights/logs/UAHardwareSecurity/leitura | Ler dados da tabela UAHardwareSecurity |
> | Ação | Microsoft. insights/logs/UAIESiteDiscovery/leitura | Ler dados da tabela UAIESiteDiscovery |
> | Ação | Microsoft. insights/logs/UAOfficeAddIn/leitura | Ler dados da tabela UAOfficeAddIn |
> | Ação | Microsoft. insights/logs/UAProposedActionPlan/leitura | Ler dados da tabela UAProposedActionPlan |
> | Ação | Microsoft. insights/logs/UASysReqIssue/leitura | Ler dados da tabela UASysReqIssue |
> | Ação | Microsoft. insights/logs/UAUpgradedComputer/leitura | Ler dados da tabela UAUpgradedComputer |
> | Ação | Microsoft. insights/logs/atualização/leitura | Ler dados da tabela de atualização |
> | Ação | Microsoft. insights/logs/UpdateRunProgress/leitura | Ler dados da tabela UpdateRunProgress |
> | Ação | Microsoft. insights/logs/UpdateSummary/leitura | Ler dados da tabela UpdateSummary |
> | Ação | Microsoft. insights/logs/uso/leitura | Ler dados da tabela de uso |
> | Ação | Microsoft. insights/logs/W3CIISLog/leitura | Ler dados da tabela W3CIISLog |
> | Ação | Microsoft. insights/logs/WaaSDeploymentStatus/leitura | Ler dados da tabela WaaSDeploymentStatus |
> | Ação | Microsoft. insights/logs/WaaSInsiderStatus/leitura | Ler dados da tabela WaaSInsiderStatus |
> | Ação | Microsoft. insights/logs/WaaSUpdateStatus/leitura | Ler dados da tabela WaaSUpdateStatus |
> | Ação | Microsoft. insights/logs/WDAVStatus/leitura | Ler dados da tabela WDAVStatus |
> | Ação | Microsoft. insights/logs/WDAVThreat/leitura | Ler dados da tabela WDAVThreat |
> | Ação | Microsoft. insights/logs/WindowsClientAssessmentRecommendation/leitura | Ler dados da tabela WindowsClientAssessmentRecommendation |
> | Ação | Microsoft. insights/logs/WindowsFirewall/leitura | Ler dados da tabela WindowsFirewall |
> | Ação | Microsoft. insights/logs/WindowsServerAssessmentRecommendation/leitura | Ler dados da tabela WindowsServerAssessmentRecommendation |
> | Ação | Microsoft. insights/logs/WireData/leitura | Ler dados da tabela WireData |
> | Ação | Microsoft. insights/logs/WUDOAggregatedStatus/leitura | Ler dados da tabela WUDOAggregatedStatus |
> | Ação | Microsoft. insights/logs/WUDOStatus/leitura | Ler dados da tabela WUDOStatus |
> | Ação | Microsoft. insights/MetricAlerts/Delete | Excluir um alerta de métrica |
> | Ação | Microsoft. insights/MetricAlerts/Read | Ler um alerta de métrica |
> | Ação | Microsoft. insights/MetricAlerts/status/leitura | Ler status de alerta de métrica |
> | Ação | Microsoft. insights/MetricAlerts/Write | Criar ou atualizar um alerta de métrica |
> | Ação | Microsoft. insights/MetricBaselines/Read | Ler linhas de base de métrica |
> | Ação | Microsoft. insights/MetricDefinitions/Microsoft. insights/Read | Ler definições de métrica |
> | Ação | Microsoft. insights/MetricDefinitions/Providers/Microsoft. insights/Read | Ler definições de métrica |
> | Ação | Microsoft. insights/MetricDefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft. insights/Metricnamespaces/Read | Ler namespaces de métrica |
> | Ação | Microsoft. insights/métricas/ação | Ação de métrica |
> | Ação | Microsoft. insights/métricas/Microsoft. insights/ler | Ler métricas |
> | Ação | Microsoft. insights/métricas/provedores/métricas/leitura | Ler métricas |
> | Ação | Microsoft. insights/métricas/leitura | Ler métricas |
> | Dataaction | Microsoft. insights/métricas/gravação | Métricas de gravação |
> | Ação | Microsoft. insights/MigrateToNewpricingModel/ação | Migrar assinatura para novo modelo de preços |
> | Ação | Microsoft. insights/operações/leitura | Operações de leitura |
> | Ação | Microsoft. insights/registro/ação | Registrar o provedor do Microsoft insights |
> | Ação | Microsoft. insights/RollbackToLegacyPricingModel/ação | Reverter assinatura para modelo de preço herdado |
> | Ação | Microsoft. insights/ScheduledQueryRules/Delete | Excluindo uma regra de consulta agendada |
> | Ação | Microsoft. insights/ScheduledQueryRules/Read | Lendo uma regra de consulta agendada |
> | Ação | Microsoft. insights/ScheduledQueryRules/Write | Escrevendo uma regra de consulta agendada |
> | Ação | Microsoft. insights/locatários/registro/ação | Inicializa o provedor do Microsoft insights |
> | Ação | Microsoft. insights/cancelar registro/ação | Registrar o provedor do Microsoft insights |
> | Ação | Microsoft. percepções/webtestes/excluir | Excluindo uma configuração de WebTest |
> | Ação | Microsoft. insights/webtesters/GetToken/leitura | Lendo um token do WebTest |
> | Ação | Microsoft. insights/webtests/MetricDefinitions/Read | Lendo definições de métrica de WebTest |
> | Ação | Microsoft. insights/webtestes/métricas/leitura | Lendo uma métrica de WebTest |
> | Ação | Microsoft. insights/webtesters/Read | Lendo uma configuração de WebTest |
> | Ação | Microsoft. insights/webtests/Write | Gravando em uma configuração de WebTest |
> | Ação | Microsoft. insights/pastas de trabalho/exclusão | Excluir uma pasta de trabalho |
> | Ação | Microsoft. insights/pastas de trabalho/leitura | Ler uma pasta de trabalho |
> | Ação | Microsoft. insights/pastas de trabalho/gravação | Criar ou atualizar uma pasta de trabalho |

## <a name="microsoftintune"></a>Microsoft. Intune

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Intune/DiagnosticSettings/Delete | Excluindo uma configuração de diagnóstico |
> | Ação | Microsoft. Intune/DiagnosticSettings/Read | Lendo uma configuração de diagnóstico |
> | Ação | Microsoft. Intune/DiagnosticSettings/Write | Escrevendo uma configuração de diagnóstico |
> | Ação | Microsoft. Intune/diagnosticsettingscategories/Read | Lendo categorias de configuração de diagnóstico |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. IoTCentral/appTemplates/Action | Obtém todos os modelos de aplicativo disponíveis no Azure IoT Central |
> | Ação | Microsoft. IoTCentral/checkNameAvailability/Action | Verifica se um nome de aplicativo IoT Central está disponível |
> | Ação | Microsoft. IoTCentral/checkSubdomainAvailability/Action | Verifica se um subdomínio de aplicativo IoT Central está disponível |
> | Ação | Microsoft. IoTCentral/IoTApps/Delete | Exclui um IoT Central aplicativos |
> | Ação | Microsoft. IoTCentral/IoTApps/Read | Obtém um único aplicativo IoT Central |
> | Ação | Microsoft. IoTCentral/IoTApps/Write | Criar ou atualizar um IoT Central aplicativos |
> | Ação | Microsoft. IoTCentral/operações/leitura | Obtém todas as operações disponíveis em aplicativos IoT Central |
> | Ação | Microsoft. IoTCentral/registro/ação | Registrar a assinatura do provedor de recursos de IoT Central do Azure |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. IoTSpaces/Graph/Delete | Exclui o recurso de grafo Microsoft. IoTSpaces |
> | Ação | Microsoft. IoTSpaces/grafo/leitura | Obter os recursos do grafo Microsoft. IoTSpaces |
> | Ação | Microsoft. IoTSpaces/Graph/Write | Criar recurso de grafo Microsoft. IoTSpaces |
> | Ação | Microsoft. IoTSpaces/registro/ação | Registrar a assinatura para o provedor de recursos do Microsoft. IoTSpaces Graph para habilitar a criação de recursos |

## <a name="microsoftkeyvault"></a>Microsoft. keyvault

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. keyvault/checkNameAvailability/Read | Verifica se um nome de cofre de chaves é válido e não está em uso |
> | Ação | Microsoft. keyvault/deletedVaults/Read | Exibir as propriedades de cofres de chaves com exclusão reversível |
> | Ação | Microsoft. keyvault/hsmPools/Delete | Excluir um pool do HSM |
> | Ação | Microsoft. keyvault/hsmPools/joinVault/ação | Unir um cofre de chaves a um pool do HSM |
> | Ação | Microsoft. keyvault/hsmPools/Read | Exibir as propriedades de um pool do HSM |
> | Ação | Microsoft. keyvault/hsmPools/Write | Criar um novo pool de HSM de atualização das propriedades de um pool existente do HSM |
> | Ação | Microsoft. keyvault/Locations/deletedVaults/limpeza/ação | Limpar um cofre de chaves com exclusão reversível |
> | Ação | Microsoft. keyvault/Locations/deletedVaults/Read | Exibir as propriedades de um cofre de chaves com exclusão reversível |
> | Ação | Microsoft. keyvault/Locations/deleteVirtualNetworkOrSubnets/Action | Notifica o Microsoft. keyvault que uma rede virtual ou sub-rede está sendo excluída |
> | Ação | Microsoft. keyvault/Locations/operationResults/Read | Verificar o resultado de uma operação de longa execução |
> | Ação | Microsoft. keyvault/operações/leitura | Lista as operações disponíveis no provedor de recursos Microsoft. keyvault |
> | Ação | Microsoft. keyvault/registro/ação | Registra uma assinatura |
> | Ação | Microsoft. keyvault/cancelar registro/ação | Cancela o registro de uma assinatura |
> | Ação | Microsoft. keyvault/cofres/accessPolicies/Write | Atualize uma política de acesso existente mesclando ou substituindo ou adicione uma nova política de acesso a um cofre. |
> | Ação | Microsoft. keyvault/cofres/excluir | Eliminar um cofre de chaves |
> | Ação | Microsoft. keyvault/cofres/implantar/ação | Habilita o acesso a segredos em um cofre de chaves ao implantar recursos do Azure |
> | Ação | Microsoft. keyvault/vaultes/eventGridFilters/Delete | Notifica o Microsoft. keyvault que uma assinatura do EventGrid para Key Vault está sendo excluída |
> | Ação | Microsoft. keyvault/cofres/eventGridFilters/Read | Notifica o Microsoft. keyvault que uma assinatura do EventGrid para Key Vault está sendo exibida |
> | Ação | Microsoft. keyvault/cofres/eventGridFilters/Write | Notifica o Microsoft. keyvault de que uma nova assinatura do EventGrid para Key Vault está sendo criada |
> | Ação | Microsoft. keyvault/cofres/leitura | Exibir as propriedades de um cofre de chaves |
> | Ação | Microsoft. keyvault/cofres/segredos/ler | Exiba as propriedades de um segredo, mas não seu valor. |
> | Ação | Microsoft. keyvault/cofres/segredos/gravação | Crie um novo segredo ou atualize o valor de um segredo existente. |
> | Ação | Microsoft. keyvault/cofres/gravação | Criar um novo cofre de chaves ou atualizar as propriedades de um cofre de chaves existente |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Kusto/clusters/ativar/ação | Inicia o cluster. |
> | Ação | Microsoft. Kusto/clusters/AttachedDatabaseConfigurations/Delete | Exclui um resourceCopy de configuração de banco de dados anexado. |
> | Ação | Microsoft. Kusto/clusters/AttachedDatabaseConfigurations/Read | Lê um resourceCopy de configuração de banco de dados anexado. |
> | Ação | Microsoft. Kusto/clusters/AttachedDatabaseConfigurations/Write | Grava um resourceCopy de configuração de banco de dados anexado. |
> | Ação | Microsoft. Kusto/clusters/CheckNameAvailability/ação | Verifica a disponibilidade do nome do cluster. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/adddirigetions/Action | Adiciona entidades de banco de dados. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/CheckNameAvailability/ação | Verifica a disponibilidade do nome de um determinado tipo. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/DataConnections/excluir | Exclui uma resourceCopy de conexões de dados. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/DataConnections/leitura | Lê uma resourceCopy de conexões de dados. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/conexões/gravação | Grava um resourceCopy de conexões de dados. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/DataConnectionValidation/ação | Valida a conexão de dados do banco de dado. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/excluir | Exclui um banco de dados resourceCopy. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/EventHubConnections/Delete | Exclui uma resourceCopy de conexões do hub de eventos. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/EventHubConnections/leitura | Lê uma resourceCopy de conexões do hub de eventos. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/EventHubConnections/gravação | Grava um resourceCopy de conexões do hub de eventos. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/EventHubConnectionValidation/ação | Valida a conexão do hub de eventos do banco de dados. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/ListPrincipals/ação | Lista as entidades de segurança do banco de dados. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/leitura | Lê um banco de dados resourceCopy. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/RemovePrincipals/ação | Remove as entidades de segurança do banco de dados. |
> | Ação | Microsoft. Kusto/clusters/bancos de dados/gravação | Grava um banco de dados resourceCopy. |
> | Ação | Microsoft. Kusto/clusters/Deactivate/Action | Interrompe o cluster. |
> | Ação | Microsoft. Kusto/clusters/excluir | Exclui um resourceCopy de cluster. |
> | Ação | Microsoft. Kusto/clusters/leitura | Lê um resourceCopy de cluster. |
> | Ação | Microsoft. Kusto/clusters/SKUs/leitura | Lê um resourceCopy de SKU de cluster. |
> | Ação | Microsoft. Kusto/clusters/iniciar/ação | Inicia o cluster. |
> | Ação | Microsoft. Kusto/clusters/parar/ação | Interrompe o cluster. |
> | Ação | Microsoft. Kusto/clusters/gravação | Grava um resourceCopy de cluster. |
> | Ação | Microsoft. Kusto/DetachFollowerDatabases/Action | Desanexa os bancos de dados do seguidor. |
> | Ação | Microsoft. Kusto/ListFollowerDatabases/Action | Lista os bancos de dados do seguidor. |
> | Ação | Microsoft. Kusto/Locations/CheckNameAvailability/Action | Verifica a disponibilidade do nome do resourceCopy. |
> | Ação | Microsoft. Kusto/Locations/operationresults/Read | ResourceCopys operações de leitura |
> | Ação | Microsoft. Kusto/operações/leitura | ResourceCopys operações de leitura |
> | Ação | Microsoft. Kusto/registro/ação | Ação de registro de assinatura |
> | Ação | Microsoft. Kusto/registro/ação | Registra a assinatura no provedor de recursos Kusto. |
> | Ação | Microsoft. Kusto/SKUs/Read | Lê um SKU resourceCopy. |
> | Ação | Microsoft. Kusto/cancelar registro/ação | Cancela o registro da assinatura para o provedor de recursos Kusto. |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. LabServices/labAccounts/CreateLab/Action | Crie um laboratório em uma conta de laboratório. |
> | Ação | Microsoft. LabServices/labAccounts/Delete | Excluir contas de laboratório. |
> | Ação | Microsoft. LabServices/labAccounts/galleryImages/Delete | Excluir imagens da galeria. |
> | Ação | Microsoft. LabServices/labAccounts/galleryImages/Read | Ler imagens da galeria. |
> | Ação | Microsoft. LabServices/labAccounts/galleryImages/Write | Adicionar ou modificar imagens da galeria. |
> | Ação | Microsoft. LabServices/labAccounts/GetRegionalAvailability/Action | Obter informações de disponibilidade regional para cada categoria de tamanho configurada em uma conta de laboratório |
> | Ação | Microsoft. LabServices/labAccounts/Labs/AddUsers/Action | Adicionar usuários a um laboratório |
> | Ação | Microsoft. LabServices/labAccounts/Labs/Delete | Excluir laboratórios. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Delete | Excluir configuração de ambiente. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ambientes/excluir | Excluir ambientes. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ambientes/leitura | Ler ambientes. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/ResetPassword/Action | Redefine a senha do usuário em um ambiente |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ambientes/iniciar/ação | Inicia um ambiente iniciando todos os recursos dentro do ambiente. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Environments/Stop/Action | Para um ambiente parando todos os recursos dentro do ambiente |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ambientes/gravação | Adicionar ou modificar ambientes. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/publicação/ação | Provisiona/desprovisiona os recursos necessários para uma configuração de ambiente com base no estado atual da configuração de laboratório/ambiente. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Read | Ler configuração de ambiente. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ResetPassword/Action | Redefine a senha na máquina virtual do modelo. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/SaveImage/Action | Salva a imagem de modelo atual na Galeria compartilhada na conta do laboratório |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Schedules/Delete | Excluir agendas. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Schedules/Read | Ler agendas. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Schedules/Write | Adicionar ou modificar agendas. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/iniciar/ação | Inicia um modelo iniciando todos os recursos dentro do modelo. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Stop/Action | Interrompe um modelo interrompendo todos os recursos dentro do modelo. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/environmentSettings/Write | Adicionar ou modificar configuração de ambiente. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/ler | Leia os laboratórios. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/SendEmail/Action | Enviar email com link de registro para o laboratório |
> | Ação | Microsoft. LabServices/labAccounts/Labs/usuários/excluir | Excluir usuários. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/usuários/ler | Ler usuários. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/usuários/gravação | Adicionar ou modificar usuários. |
> | Ação | Microsoft. LabServices/labAccounts/Labs/Write | Adicionar ou modificar laboratórios. |
> | Ação | Microsoft. LabServices/labAccounts/Read | Leia as contas do laboratório. |
> | Ação | Microsoft. LabServices/labAccounts/sharedGalleries/Delete | Exclua sharedgalleries. |
> | Ação | Microsoft. LabServices/labAccounts/sharedGalleries/Read | Leia sharedgalleries. |
> | Ação | Microsoft. LabServices/labAccounts/sharedGalleries/Write | Adicionar ou modificar sharedgalleries. |
> | Ação | Microsoft. LabServices/labAccounts/sharedImages/Delete | Exclua SharedImages. |
> | Ação | Microsoft. LabServices/labAccounts/sharedImages/Read | Leia SharedImages. |
> | Ação | Microsoft. LabServices/labAccounts/sharedImages/Write | Adicionar ou modificar SharedImages. |
> | Ação | Microsoft. LabServices/labAccounts/Write | Adicionar ou modificar contas de laboratório. |
> | Ação | Microsoft. LabServices/localizações/operações/leitura | Operações de leitura. |
> | Ação | Microsoft. LabServices/registro/ação | Registra a assinatura |
> | Ação | Microsoft. LabServices/Users/GetOperationBatchStatus/Action | Obter status da operação de lote |
> | Ação | Microsoft. LabServices/Users/GetOperationStatus/Action | Obtém o status da operação de execução longa |
> | Ação | Microsoft. LabServices/Users/GetPersonalPreferences/Action | Obter preferências pessoais para um usuário |
> | Ação | Microsoft. LabServices/Users/ListAllEnvironments/Action | Listar todos os ambientes do usuário |
> | Ação | Microsoft. LabServices/usuários/registro/ação | Registrar um usuário em um laboratório gerenciado |
> | Ação | Microsoft. LabServices/Users/ResetPassword/Action | Redefine a senha do usuário em um ambiente |
> | Ação | Microsoft. LabServices/Users/StartEnvironment/Action | Inicia um ambiente iniciando todos os recursos dentro do ambiente. |
> | Ação | Microsoft. LabServices/Users/StopEnvironment/Action | Para um ambiente parando todos os recursos dentro do ambiente |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Logic/integrationAccounts/Agreements/Delete | Exclui o contrato na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Agreements/listContentCallbackUrl/Action | Obtém a URL de retorno de chamada para o conteúdo do contrato na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Agreements/Read | Lê o contrato na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Agreements/Write | Cria ou atualiza o contrato na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/assemblies/Delete | Exclui o assembly na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/assemblies/listContentCallbackUrl/Action | Obtém a URL de retorno de chamada para o conteúdo do assembly na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/assemblies/ler | Lê o assembly na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/assemblies/gravar | Cria ou atualiza o assembly na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/batchConfigurations/Delete | Exclui a configuração do lote na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/batchConfigurations/Read | Lê a configuração do lote na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/batchConfigurations/Write | Cria ou atualiza a configuração do lote na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Certificates/Delete | Exclui o certificado na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Certificates/Read | Lê o certificado na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Certificates/Write | Cria ou atualiza o certificado na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Delete | Exclui a conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Join/Action | Une a conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/listCallbackUrl/Action | Obtém a URL de retorno de chamada para a conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/listKeyVaultKeys/Action | Obtém as chaves no cofre de chaves. |
> | Ação | Microsoft. Logic/integrationAccounts/logTrackingEvents/Action | Registra os eventos de rastreamento na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Maps/Delete | Exclui o mapa na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Maps/listContentCallbackUrl/ação | Obtém a URL de retorno de chamada para o conteúdo do mapa na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Maps/Read | Lê o mapa na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Maps/Write | Cria ou atualiza o mapa na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Partners/excluir | Exclui o parceiro na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Partners/listContentCallbackUrl/Action | Obtém a URL de retorno de chamada para o conteúdo do parceiro na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Partners/Read | Lê o parceiro na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Partners/Write | Cria ou atualiza o parceiro na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Providers/Microsoft. insights/logDefinitions/Read | Lê as definições de log da conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Read | Lê a conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/regenerateAccessKey/Action | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft. Logic/integrationAccounts/schemas/Delete | Exclui o esquema na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/schemas/listContentCallbackUrl/Action | Obtém a URL de retorno de chamada para o conteúdo do esquema na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/schemas/Read | Lê o esquema na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/schemas/Write | Cria ou atualiza o esquema na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Sessions/Delete | Exclui a sessão na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/sessões/ler | Lê a configuração do lote na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Sessions/Write | Cria ou atualiza a sessão na conta de integração. |
> | Ação | Microsoft. Logic/integrationAccounts/Write | Cria ou atualiza a conta de integração. |
> | Ação | Microsoft. Logic/integrationServiceEnvironments/Delete | Exclui o ambiente do serviço de integração. |
> | Ação | Microsoft. Logic/integrationServiceEnvironments/Join/Action | Une o Ambiente de Serviço de Integração. |
> | Ação | Microsoft. Logic/integrationServiceEnvironments/managedApis/apiOperations/Read | Lê a operação da API gerenciada do ambiente do serviço de integração. |
> | Ação | Microsoft. Logic/integrationServiceEnvironments/managedApis/Read | Lê a API gerenciada do ambiente do serviço de integração. |
> | Ação | Microsoft. Logic/integrationServiceEnvironments/operationStatuses/Read | Lê os status da operação de ambiente do serviço de integração. |
> | Ação | Microsoft. Logic/integrationServiceEnvironments/Providers/Microsoft. insights/metricDefinitions/Read | Lê as definições de métrica de ambiente do serviço de integração. |
> | Ação | Microsoft. Logic/integrationServiceEnvironments/Read | Lê o ambiente do serviço de integração. |
> | Ação | Microsoft. Logic/integrationServiceEnvironments/Write | Cria ou atualiza o ambiente do serviço de integração. |
> | Ação | Microsoft. Logic/Locations/fluxos de trabalho/recommendOperationGroups/Action | Obtém os grupos de operações recomendados do fluxo de trabalho. |
> | Ação | Microsoft. Logic/Locations/fluxos de trabalho/validação/ação | Valida o fluxo de trabalho. |
> | Ação | Microsoft. Logic/Operations/Read | Obtém a operação. |
> | Ação | Microsoft. Logic/Register/Action | Registra o provedor de recursos Microsoft. Logic para uma determinada assinatura. |
> | Ação | Microsoft. Logic/fluxos de trabalho/accessKey/Delete | Exclui a chave de acesso. |
> | Ação | Microsoft. Logic/fluxos de trabalho/accessKeys/lista/ação | Lista os segredos da chave de acesso. |
> | Ação | Microsoft. Logic/workflows/accessKeys/Read | Lê a chave de acesso. |
> | Ação | Microsoft. Logic/fluxos de trabalho/accessKeys/regenerar/ação | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft. Logic/workflows/accessKeys/Write | Cria ou atualiza a chave de acesso. |
> | Ação | Microsoft. Logic/workflows/Delete | Exclui o fluxo de trabalho. |
> | Ação | Microsoft. Logic/fluxos de trabalho/desabilitar/ação | Desabilita o fluxo de trabalho. |
> | Ação | Microsoft. Logic/fluxos de trabalho/habilitar/ação | Habilita o fluxo de trabalho. |
> | Ação | Microsoft. Logic/fluxos de trabalho/listCallbackUrl/Action | Obtém a URL de retorno de chamada para o fluxo de trabalho. |
> | Ação | Microsoft. Logic/fluxos de trabalho/listSwagger/Action | Obtém as definições de Swagger do fluxo de trabalho. |
> | Ação | Microsoft. Logic/fluxos de trabalho/mover/ação | Move o fluxo de trabalho de sua ID de assinatura, grupo de recursos e/ou nome existente para uma ID de assinatura, grupo de recursos e/ou nome diferente. |
> | Ação | Microsoft. Logic/workflows/Providers/Microsoft. insights/diagnosticSettings/Read | Lê as configurações de diagnóstico do fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/Providers/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico do fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/Providers/Microsoft. insights/logDefinitions/Read | Lê as definições de log de fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/Providers/Microsoft. insights/metricDefinitions/Read | Lê as definições de métrica de fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/Read | Lê o fluxo de trabalho. |
> | Ação | Microsoft. Logic/fluxos de trabalho/regenerateAccessKey/Action | Regenera os segredos da chave de acesso. |
> | Ação | Microsoft. Logic/fluxos de trabalho/execução/ação | Inicia uma execução do fluxo de trabalho. |
> | Ação | Microsoft. Logic/fluxos de trabalho/execuções/ações/listExpressionTraces/ação | Obtém os rastreamentos de expressão de ação de execução do fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/executes/Actions/Read | Lê a ação de execução do fluxo de trabalho. |
> | Ação | Microsoft. Logic/fluxos de trabalho/execuções/ações/repetições/listExpressionTraces/ação | Obtém os rastreamentos de expressão de repetição da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/execuções/ações/repetições/leitura | Lê a repetição da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/executes/Actions/repetições/requestHistories/Read | Lê o histórico de solicitações de ação de repetição executar fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/executes/Actions/requestHistories/Read | Lê o histórico de solicitações de ação de execução do fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/executes/Actions/scoperepetitions/Read | Lê a repetição do escopo da ação de execução do fluxo de trabalho. |
> | Ação | Microsoft. Logic/fluxos de trabalho/execuções/cancelamento/ação | Cancela a execução de um fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/execute/Delete | Exclui uma execução de um fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/executes/Operations/Read | Lê o status da operação de execução do fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/Execute/Read | Lê a execução do fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/suspender/ação | Suspende o fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/triggers/históricos/Read | Lê os históricos de gatilho. |
> | Ação | Microsoft. Logic/fluxos de trabalho/gatilhos/históricos/reenviar/ação | Reenvia o gatilho de fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/disparadores/listCallbackUrl/Action | Obtém a URL de retorno de chamada para o gatilho. |
> | Ação | Microsoft. Logic/workflows/triggers/Read | Lê o gatilho. |
> | Ação | Microsoft. Logic/workflows/triggers/redefine/Action | Redefine o gatilho. |
> | Ação | Microsoft. Logic/fluxos de trabalho/gatilhos/execução/ação | Executa o gatilho. |
> | Ação | Microsoft. Logic/fluxos de trabalho/disparadores/estado/ação | Define o estado do gatilho. |
> | Ação | Microsoft. Logic/workflows/valida/Action | Valida o fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/Versions/Read | Lê a versão do fluxo de trabalho. |
> | Ação | Microsoft. Logic/workflows/Versions/triggers/listCallbackUrl/Action | Obtém a URL de retorno de chamada para o gatilho. |
> | Ação | Microsoft. Logic/workflows/Write | Cria ou atualiza o fluxo de trabalho. |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. MachineLearning/commitmentPlans/commitmentAssociations/move/ação | Mover qualquer Machine Learning Associação de plano de compromisso |
> | Ação | Microsoft. MachineLearning/commitmentPlans/commitmentAssociations/Read | Ler qualquer associação de plano de compromisso Machine Learning |
> | Ação | Microsoft. MachineLearning/commitmentPlans/Delete | Excluir qualquer Machine Learning plano de compromisso |
> | Ação | Microsoft. MachineLearning/commitmentPlans/junção/ação | Junte-se a qualquer Machine Learning plano de compromisso |
> | Ação | Microsoft. MachineLearning/commitmentPlans/Read | Ler qualquer plano de compromisso Machine Learning |
> | Ação | Microsoft. MachineLearning/commitmentPlans/Write | Criar ou atualizar qualquer plano de compromisso de Machine Learning |
> | Ação | Microsoft. MachineLearning/Locations/operationresults/Read | Obter resultado de uma operação de Machine Learning |
> | Ação | Microsoft. MachineLearning/Locations/operationsstatus/Read | Obter o status de uma operação de Machine Learning em andamento |
> | Ação | Microsoft. MachineLearning/operações/leitura | Obter Machine Learning operações |
> | Ação | Microsoft. MachineLearning/registro/ação | Registra a assinatura para o provedor de recursos do serviço Web do Machine Learning e habilita a criação de serviços Web. |
> | Ação | Microsoft. MachineLearning/SKUs/Read | Obter Machine Learning SKUs do plano de compromisso |
> | Ação | Microsoft. MachineLearning/WebServices/Action | Criar propriedades do serviço Web regional para regiões com suporte |
> | Ação | Microsoft. MachineLearning/WebServices/Delete | Excluir qualquer serviço Web Machine Learning |
> | Ação | Microsoft. MachineLearning/WebServices/listkeys/Read | Obter chaves para um serviço Web Machine Learning |
> | Ação | Microsoft. MachineLearning/WebServices/leitura | Ler qualquer serviço Web Machine Learning |
> | Ação | Microsoft. MachineLearning/WebServices/Write | Criar ou atualizar qualquer serviço Web Machine Learning |
> | Ação | Microsoft. MachineLearning/espaços de trabalho/excluir | Excluir qualquer Workspace do Machine Learning |
> | Ação | Microsoft. MachineLearning/Workspaces/listworkspacekeys/Action | Listar chaves para um Workspace do Machine Learning |
> | Ação | Microsoft. MachineLearning/Workspaces/Read | Ler qualquer Workspace do Machine Learning |
> | Ação | Microsoft. MachineLearning/Workspaces/resyncstoragekeys/Action | Ressincronizar chaves da conta de armazenamento configurada para um Workspace do Machine Learning |
> | Ação | Microsoft. MachineLearning/Workspaces/Write | Criar ou atualizar qualquer Workspace do Machine Learning |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. MachineLearningServices/Locations/computeoperationsstatus/Read | Obtém o status de uma operação de computação específica |
> | Ação | Microsoft. MachineLearningServices/localizações/usos/leitura | Relatório de uso para recursos de computação AML em uma assinatura |
> | Ação | Microsoft. MachineLearningServices/Locations/vmsizes/Read | Obter tamanhos de VM com suporte |
> | Ação | Microsoft. MachineLearningServices/Locations/workspaceOperationsStatus/Read | Obtém o status de uma operação de espaço de trabalho específica |
> | Ação | Microsoft. MachineLearningServices/registro/ação | Registra a assinatura para o provedor de recursos de Serviços de Machine Learning |
> | Ação | Microsoft. MachineLearningServices/espaços de trabalho/computações/exclusão | Exclui os recursos de computação em Serviços de Machine Learning espaços de trabalho |
> | Ação | Microsoft. MachineLearningServices/espaços de trabalho/computações/listKeys/ação | Listar segredos para recursos de computação no espaço de trabalho Serviços de Machine Learning |
> | Ação | Microsoft. MachineLearningServices/espaços de trabalho/computações/listNodes/ação | Listar nós para recurso de computação no espaço de trabalho Serviços de Machine Learning |
> | Ação | Microsoft. MachineLearningServices/espaços de trabalho/computações/leitura | Obter os recursos de computação em Serviços de Machine Learning espaços de trabalho |
> | Ação | Microsoft. MachineLearningServices/espaços de trabalho/computações/gravação | Criar ou atualizar os recursos de computação em Serviços de Machine Learning espaços de trabalho |
> | Ação | Microsoft. MachineLearningServices/espaços de trabalho/excluir | Exclui os espaços de trabalho Serviços de Machine Learning |
> | Dataaction | Microsoft. MachineLearningServices/espaços de trabalho/experimentos/excluir | Exclui experimentos em Serviços de Machine Learning espaços de trabalho |
> | Dataaction | Microsoft. MachineLearningServices/espaços de trabalho/experimentos/leitura | Obtém experimentos em Serviços de Machine Learning espaços de trabalho |
> | Dataaction | Microsoft. MachineLearningServices/Workspaces/experimentos/Write | Cria ou atualiza experimentos em Serviços de Machine Learning espaço (s) de trabalho |
> | Ação | Microsoft. MachineLearningServices/Workspaces/listKeys/Action | Listar segredos para um espaço de trabalho Serviços de Machine Learning |
> | Ação | Microsoft. MachineLearningServices/Workspaces/Read | Obter o Serviços de Machine Learning espaço de trabalho |
> | Ação | Microsoft. MachineLearningServices/Workspaces/Write | Cria ou atualiza um ou mais espaços de trabalho do Serviços de Machine Learning |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ManagedIdentity/Identities/Read | Obtém uma identidade atribuída do sistema existente |
> | Ação | Microsoft. ManagedIdentity/registro/ação | Registra a assinatura para o provedor de recursos de identidade gerenciada |
> | Ação | Microsoft. ManagedIdentity/userAssignedIdentities/atribuir/ação | Ação RBAC para atribuir uma identidade de usuário existente atribuída a um recurso |
> | Ação | Microsoft. ManagedIdentity/userAssignedIdentities/Delete | Exclui uma identidade atribuída pelo usuário existente |
> | Ação | Microsoft. ManagedIdentity/userAssignedIdentities/Read | Obtém uma identidade atribuída ao usuário existente |
> | Ação | Microsoft. ManagedIdentity/userAssignedIdentities/Write | Cria uma nova identidade atribuída ao usuário ou atualiza as marcas associadas a uma identidade atribuída ao usuário existente |

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

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Management/checkNameAvailability/Action | Verifica se o nome do grupo de gerenciamento especificado é válido e exclusivo. |
> | Ação | Microsoft. Management/getentities/Action | Listar todas as entidades (Grupos de Gerenciamento, assinaturas, etc.) para o usuário autenticado. |
> | Ação | Microsoft. Management/managementGroups/Delete | Excluir grupo de gerenciamento. |
> | Ação | Microsoft. Management/managementGroups/Read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Ação | Microsoft. Management/managementGroups/assinaturas/excluir | Desassocia a assinatura do grupo de gerenciamento. |
> | Ação | Microsoft. Management/managementGroups/assinaturas/gravação | Associa a assinatura existente ao grupo de gerenciamento. |
> | Ação | Microsoft. Management/managementGroups/Write | Criar ou atualizar um grupo de gerenciamento. |
> | Ação | Microsoft. Management/registrar/ação | Registrar a assinatura especificada com Microsoft. Management |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Dataaction | Microsoft. Maps/contas/dados/ler | Concede acesso de leitura de dados a uma conta do Maps. |
> | Ação | Microsoft. Maps/contas/excluir | Excluir uma conta do Maps. |
> | Ação | Microsoft. Maps/accounts/eventGridFilters/Delete | Excluir um filtro de grade de eventos |
> | Ação | Microsoft. Maps/accounts/eventGridFilters/Read | Obter um filtro de grade de eventos |
> | Ação | Microsoft. Maps/accounts/eventGridFilters/Write | Criar ou atualizar um filtro de grade de eventos |
> | Ação | Microsoft. Maps/accounts/listKeys/Action | Listar chaves de conta do Maps |
> | Ação | Microsoft. Maps/contas/leitura | Obtenha uma conta do Maps. |
> | Ação | Microsoft. Maps/accounts/regenerateKey/Action | Gerar nova chave primária ou secundária de conta de mapas |
> | Ação | Microsoft. Maps/contas/gravação | Criar ou atualizar uma conta do Maps. |
> | Ação | Microsoft. Maps/operações/leitura | Ler as operações do provedor |
> | Ação | Microsoft. Maps/registro/ação | Registrar o provedor |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Marketplace/offerTypes/Publicadores/ofertas/planos/contratos/leitura | Retorna um contrato. |
> | Ação | Microsoft. Marketplace/offerTypes/Publicadores/ofertas/planos/contratos/gravação | Aceita um contrato assinado. |
> | Ação | Microsoft. Marketplace/offerTypes/Publishers/ofertas/planos/configurações/importImage/ação | Importa uma imagem para o ACR do usuário final. |
> | Ação | Microsoft. Marketplace/offerTypes/Publishers/ofertas/planos/configurações/leitura | Retorna uma configuração. |
> | Ação | Microsoft. Marketplace/offerTypes/Publishers/ofertas/planos/configurações/gravação | Salva uma configuração. |
> | Ação | Microsoft. Marketplace/registro/ação | Registra o provedor de recursos Microsoft. Marketplace na assinatura. |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. MarketplaceApps/ClassicDevServices/Delete | Faz uma operação de exclusão em um recurso de serviço de desenvolvimento clássico. |
> | Ação | Microsoft. MarketplaceApps/ClassicDevServices/listSecrets/Action | Obtém as chaves de gerenciamento de recursos do serviço de desenvolvimento clássico. |
> | Ação | Microsoft. MarketplaceApps/ClassicDevServices/listSingleSignOnToken/Action | Obtém a URL de logon único para um serviço de desenvolvimento clássico. |
> | Ação | Microsoft. MarketplaceApps/ClassicDevServices/Read | Faz uma operação GET em um serviço de desenvolvimento clássico. |
> | Ação | Microsoft. MarketplaceApps/ClassicDevServices/regenerateKey/Action | Gera uma chave de gerenciamento de recursos do serviço de desenvolvimento clássico. |
> | Ação | Microsoft. MarketplaceApps/operações/leitura | Leia as operações para todos os tipos de recurso. |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. MarketplaceOrdering/contratos/ofertas/planos/cancelar/ação | Cancelar um contrato para um determinado item do Marketplace |
> | Ação | Microsoft. MarketplaceOrdering/contratos/ofertas/planos/leitura | Retornar um contrato para um determinado item do Marketplace |
> | Ação | Microsoft. MarketplaceOrdering/contratos/ofertas/planos/sinal/ação | Assinar um contrato para um determinado item do Marketplace |
> | Ação | Microsoft. MarketplaceOrdering/contratos/ler | Retornar todos os contratos sob determinada assinatura |
> | Ação | Microsoft. MarketplaceOrdering/offertypes/Publishers/ofertas/planos/contratos/leitura | Obter um contrato para um determinado item de máquina virtual do Marketplace |
> | Ação | Microsoft. MarketplaceOrdering/offertypes/Publishers/ofertas/planos/contratos/gravação | Assinar ou cancelar um contrato para um determinado item de máquina virtual do Marketplace |
> | Ação | Microsoft. MarketplaceOrdering/operações/leitura | Listar todas as operações possíveis na API |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Media/checknameavailability/Action | Verifica se um nome de conta dos serviços de mídia está disponível |
> | Ação | Microsoft. Media/Locations/checkNameAvailability/Action | Verifica se um nome de conta dos serviços de mídia está disponível |
> | Ação | Microsoft. Media/mediaservices/accountfilters/Delete | Excluir qualquer filtro de conta |
> | Ação | Microsoft. Media/mediaservices/accountfilters/Read | Ler qualquer filtro de conta |
> | Ação | Microsoft. Media/mediaservices/accountfilters/Write | Criar ou atualizar um filtro de conta |
> | Ação | Microsoft. Media/mediaservices/assets/assetfilters/Delete | Excluir qualquer filtro de ativo |
> | Ação | Microsoft. Media/mediaservices/assets/assetfilters/Read | Ler qualquer filtro de ativo |
> | Ação | Microsoft. Media/mediaservices/assets/assetfilters/Write | Criar ou atualizar qualquer filtro de ativo |
> | Ação | Microsoft. Media/mediaservices/assets/Delete | Excluir qualquer ativo |
> | Ação | Microsoft. Media/mediaservices/assets/getEncryptionKey/Action | Obter chave de criptografia de ativo |
> | Ação | Microsoft. Media/mediaservices/assets/listContainerSas/Action | Listar URLs de SAS do contêiner de ativos |
> | Ação | Microsoft. Media/mediaservices/assets/listStreamingLocators/Action | Listar localizadores de streaming para ativo |
> | Ação | Microsoft. Media/mediaservices/assets/Read | Ler qualquer ativo |
> | Ação | Microsoft. Media/mediaservices/assets/Write | Criar ou atualizar qualquer ativo |
> | Ação | Microsoft. Media/mediaservices/contentKeyPolicies/Delete | Excluir qualquer política de chave de conteúdo |
> | Ação | Microsoft. Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/Action | Obter propriedades de política com segredos |
> | Ação | Microsoft. Media/mediaservices/contentKeyPolicies/Read | Ler qualquer política de chave de conteúdo |
> | Ação | Microsoft. Media/mediaservices/contentKeyPolicies/Write | Criar ou atualizar qualquer política de chave de conteúdo |
> | Ação | Microsoft. Media/mediaservices/Delete | Excluir qualquer conta de serviços de mídia |
> | Ação | Microsoft. Media/mediaservices/eventGridFilters/Delete | Excluir qualquer filtro de grade de eventos |
> | Ação | Microsoft. Media/mediaservices/eventGridFilters/Read | Ler qualquer filtro de grade de eventos |
> | Ação | Microsoft. Media/mediaservices/eventGridFilters/Write | Criar ou atualizar qualquer filtro de grade de eventos |
> | Ação | Microsoft. Media/mediaservices/liveEventOperations/Read | Ler qualquer operação de evento ao vivo |
> | Ação | Microsoft. Media/mediaservices/liveEvents/Delete | Excluir qualquer evento ao vivo |
> | Ação | Microsoft. Media/mediaservices/liveEvents/liveOutputs/Delete | Excluir qualquer saída ao vivo |
> | Ação | Microsoft. Media/mediaservices/liveEvents/liveOutputs/Read | Ler qualquer saída ao vivo |
> | Ação | Microsoft. Media/mediaservices/liveEvents/liveOutputs/Write | Criar ou atualizar qualquer saída ao vivo |
> | Ação | Microsoft. Media/mediaservices/liveEvents/Read | Ler qualquer evento ao vivo |
> | Ação | Microsoft. Media/mediaservices/liveEvents/redefinir/ação | Redefinir qualquer operação de evento ao vivo |
> | Ação | Microsoft. Media/mediaservices/liveEvents/iniciar/ação | Iniciar qualquer operação de evento ao vivo |
> | Ação | Microsoft. Media/mediaservices/liveEvents/Stop/Action | Parar qualquer operação de evento ao vivo |
> | Ação | Microsoft. Media/mediaservices/liveEvents/Write | Criar ou atualizar qualquer evento ao vivo |
> | Ação | Microsoft. Media/mediaservices/liveOutputOperations/Read | Ler qualquer operação de saída ao vivo |
> | Ação | Microsoft. Media/mediaservices/Read | Ler qualquer conta dos serviços de mídia |
> | Ação | Microsoft. Media/mediaservices/streamingEndpointOperations/Read | Ler qualquer operação de ponto de extremidade de streaming |
> | Ação | Microsoft. Media/mediaservices/streamingEndpoints/Delete | Excluir qualquer ponto de extremidade de streaming |
> | Ação | Microsoft. Media/mediaservices/streamingEndpoints/Read | Ler qualquer ponto de extremidade de streaming |
> | Ação | Microsoft. Media/mediaservices/streamingEndpoints/escala/ação | Dimensionar qualquer operação de ponto de extremidade de streaming |
> | Ação | Microsoft. Media/mediaservices/streamingEndpoints/iniciar/ação | Iniciar qualquer operação de ponto de extremidade de streaming |
> | Ação | Microsoft. Media/mediaservices/streamingEndpoints/Stop/Action | Parar qualquer operação de ponto de extremidade de streaming |
> | Ação | Microsoft. Media/mediaservices/streamingEndpoints/Write | Criar ou atualizar qualquer ponto de extremidade de streaming |
> | Ação | Microsoft. Media/mediaservices/streamingLocators/Delete | Excluir qualquer localizador de streaming |
> | Ação | Microsoft. Media/mediaservices/streamingLocators/listContentKeys/Action | Listar chaves de conteúdo |
> | Ação | Microsoft. Media/mediaservices/streamingLocators/listPaths/Action | Listar caminhos |
> | Ação | Microsoft. Media/mediaservices/streamingLocators/Read | Ler qualquer localizador de streaming |
> | Ação | Microsoft. Media/mediaservices/streamingLocators/Write | Criar ou atualizar um localizador de streaming |
> | Ação | Microsoft. Media/mediaservices/streamingPolicies/Delete | Excluir qualquer política de streaming |
> | Ação | Microsoft. Media/mediaservices/streamingPolicies/Read | Ler qualquer política de streaming |
> | Ação | Microsoft. Media/mediaservices/streamingPolicies/Write | Criar ou atualizar uma política de streaming |
> | Ação | Microsoft. Media/mediaservices/syncStorageKeys/Action | Sincronizar as chaves de armazenamento para uma conta de armazenamento do Azure anexada |
> | Ação | Microsoft. Media/mediaservices/transformações/exclusão | Excluir qualquer transformação |
> | Ação | Microsoft. Media/mediaservices/Transformations/Jobs/cancelJob/ação | Cancelar trabalho |
> | Ação | Microsoft. Media/mediaservices/transformações/trabalhos/excluir | Excluir qualquer trabalho |
> | Ação | Microsoft. Media/mediaservices/Transformations/Jobs/Read | Ler qualquer trabalho |
> | Ação | Microsoft. Media/mediaservices/Transformations/Jobs/Write | Criar ou atualizar qualquer trabalho |
> | Ação | Microsoft. Media/mediaservices/transformações/leitura | Ler qualquer transformação |
> | Ação | Microsoft. Media/mediaservices/transformações/gravação | Criar ou atualizar qualquer transformação |
> | Ação | Microsoft. Media/mediaservices/Write | Criar ou atualizar qualquer conta de serviços de mídia |
> | Ação | Microsoft. Media/Operations/Read | Obter operações disponíveis |
> | Ação | Microsoft. Media/registro/ação | Registra a assinatura para o provedor de recursos de serviços de mídia e habilita a criação de contas de serviços de mídia |
> | Ação | Microsoft. Media/cancelar registro/ação | Cancela o registro da assinatura para o provedor de recursos dos serviços de mídia |

## <a name="microsoftmigrate"></a>Microsoft. migrar

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Migration/assessmentprojects/assessmentoptions/Read | Obtém as opções de avaliação que estão disponíveis no local especificado |
> | Ação | Microsoft. Migrations/assessmentprojects/avaliações/leitura | Lista avaliações em um projeto |
> | Ação | Microsoft. Migrate/assessmentprojects/Delete | Excluir o projeto de avaliação |
> | Ação | Microsoft. migre/assessmentprojects/grupos/avaliações/assessedmachines/leitura | Obter as propriedades de uma máquina avaliada |
> | Ação | Microsoft. Migrations/assessmentprojects/grupos/avaliações/exclusão | Exclui uma avaliação |
> | Ação | Microsoft. Migrations/assessmentprojects/grupos/avaliações/DownloadURL/Action | Baixa a URL de um relatório de avaliação |
> | Ação | Microsoft. Migrations/assessmentprojects/grupos/avaliações/leitura | Obtém as propriedades de uma avaliação |
> | Ação | Microsoft. migre/assessmentprojects/grupos/avaliações/gravação | Cria uma nova avaliação ou atualiza uma avaliação existente |
> | Ação | Microsoft. Migrate/assessmentprojects/groups/Delete | Exclui um grupo |
> | Ação | Microsoft. migre/assessmentprojects/groups/Read | Obter as propriedades de um grupo |
> | Ação | Microsoft. Migrate/assessmentprojects/groups/updateMachines/Action | Atualizar grupo adicionando ou removendo computadores |
> | Ação | Microsoft. migrar/assessmentprojects/groups/Write | Cria um novo grupo ou atualiza um grupo existente |
> | Ação | Microsoft. Migrate/assessmentprojects/hypervcollectors/Delete | Exclui o coletor HyperV |
> | Ação | Microsoft. Migrate/assessmentprojects/hypervcollectors/Read | Obter as propriedades do coletor HyperV |
> | Ação | Microsoft. Migrate/assessmentprojects/hypervcollectors/Write | Cria um novo coletor do HyperV ou atualiza um coletor HyperV existente |
> | Ação | Microsoft. Migrate/assessmentprojects/Machines/Read | Obtém as propriedades de um computador |
> | Ação | Microsoft. Migrate/assessmentprojects/Read | Obter as propriedades do projeto de avaliação |
> | Ação | Microsoft. Migrate/assessmentprojects/vmwarecollectors/Delete | Exclui o coletor do VMware |
> | Ação | Microsoft. Migrate/assessmentprojects/vmwarecollectors/Read | Obter as propriedades do coletor do VMware |
> | Ação | Microsoft. Migrate/assessmentprojects/vmwarecollectors/Write | Cria um novo coletor VMware ou atualiza um coletor VMware existente |
> | Ação | Microsoft. Migrate/assessmentprojects/Write | Cria um novo projeto de avaliação ou atualiza um projeto de avaliação existente |
> | Ação | Microsoft. Migration/Locations/assessmentoptions/Read | Obtém as opções de avaliação que estão disponíveis no local especificado |
> | Ação | Microsoft. Migrate/Locations/checknameavailability/Action | Verifica a disponibilidade do nome do recurso para a assinatura especificada no local especificado |
> | Ação | Microsoft. Migrate/migrateprojects/DatabaseInstances/Read | Obtém as propriedades de uma instância de banco de dados |
> | Ação | Microsoft. Migrations/migrateprojects/bancos de dados/leitura | Obtém as propriedades de um banco de dados |
> | Ação | Microsoft. Migrate/migrateprojects/Delete | Excluir um projeto de migração |
> | Ação | Microsoft. Migrate/migrateprojects/Machines/Read | Obtém as propriedades de um computador |
> | Ação | Microsoft. Migrate/migrateprojects/MigrateEvents/Delete | Exclui um evento de migração |
> | Ação | Microsoft. Migrate/migrateprojects/MigrateEvents/Read | Obtém as propriedades de um evento de migração. |
> | Ação | Microsoft. Migrate/migrateprojects/Read | Obtém as propriedades do projeto de migração |
> | Ação | Microsoft. Migrate/migrateprojects/RefreshSummary/Action | Atualiza o resumo do projeto de migração |
> | Ação | Microsoft. Migrate/migrateprojects/registerTool/Action | Registra a ferramenta para um projeto de migração |
> | Ação | Microsoft. Migrate/migrateprojects/Solutions/cleanupData/Action | Limpar os dados de solução de projeto de migração |
> | Ação | Microsoft. Migrate/migrateprojects/Solutions/Delete | Exclui uma solução de migração de projeto |
> | Ação | Microsoft. Migrate/migrateprojects/Solutions/GetConfig/Action | Obter a configuração de solução de projeto de migração |
> | Ação | Microsoft. migrar/migrateprojects/soluções/ler | Obtém as propriedades da solução migrar projeto |
> | Ação | Microsoft. migrar/migrateprojects/Solutions/Write | Cria uma nova solução de migração de projeto ou atualiza uma solução de projeto de migração existente |
> | Ação | Microsoft. Migrate/migrateprojects/Write | Cria um novo projeto de migração ou atualiza um projeto de migração existente |
> | Ação | Microsoft. migrar/operações/ler | Lista as operações disponíveis no provedor de recursos Microsoft. Migrate |
> | Ação | Microsoft. migrar/projetos/avaliações/leitura | Lista avaliações em um projeto |
> | Ação | Microsoft. migrar/Projects/Delete | Exclui o projeto |
> | Ação | Microsoft. migrar/projetos/grupos/avaliações/assessedmachines/ler | Obter as propriedades de uma máquina avaliada |
> | Ação | Microsoft. migrar/projetos/grupos/avaliações/excluir | Exclui uma avaliação |
> | Ação | Microsoft. migrar/projetos/grupos/avaliações/DownloadURL/ação | Baixa a URL de um relatório de avaliação |
> | Ação | Microsoft. migrar/projetos/grupos/avaliações/leitura | Obtém as propriedades de uma avaliação |
> | Ação | Microsoft. migrar/projetos/grupos/avaliações/gravação | Cria uma nova avaliação ou atualiza uma avaliação existente |
> | Ação | Microsoft. Migrate/Projects/groups/Delete | Exclui um grupo |
> | Ação | Microsoft. migrar/projetos/grupos/ler | Obter as propriedades de um grupo |
> | Ação | Microsoft. migrar/projetos/grupos/gravar | Cria um novo grupo ou atualiza um grupo existente |
> | Ação | Microsoft. migrar/projetos/chaves/ação | Obtém chaves compartilhadas para o projeto |
> | Ação | Microsoft. migrar/projetos/computadores/ler | Obtém as propriedades de um computador |
> | Ação | Microsoft. migrar/projetos/ler | Obtém as propriedades de um projeto |
> | Ação | Microsoft. migrar/projetos/gravar | Cria um novo projeto ou atualiza um projeto existente |
> | Ação | Microsoft. migrar/registrar/ação | Registra a assinatura com o provedor de recursos Microsoft. Migrate |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. MixedReality/registro/ação | Registra uma assinatura para o provedor de recursos de realidade misturada. |
> | Dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/criar/ação | Criar âncoras espaciais |
> | Dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/Delete | Excluir âncoras espaciais |
> | Dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/descoberta/leitura | Descobrir âncoras espaciais próximas |
> | Dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/Properties/Read | Obter propriedades de âncoras espaciais |
> | Ação | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. insights/diagnosticSettings/Read | Obtém a configuração de diagnóstico para Microsoft. MixedReality/spatialAnchorsAccounts |
> | Ação | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para Microsoft. MixedReality/spatialAnchorsAccounts |
> | Ação | Microsoft. MixedReality/spatialAnchorsAccounts/Providers/Microsoft. insights/metricDefinitions/Read | Obtém as métricas disponíveis para Microsoft. MixedReality/spatialAnchorsAccounts |
> | Dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/consulta/leitura | Localizar âncoras espaciais |
> | Dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | Dataaction | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Atualizar propriedades de âncoras espaciais |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. NetApp/Locations/checkfilepathavailability/Action | Verificar se o caminho do arquivo está disponível |
> | Ação | Microsoft. NetApp/Locations/checknameavailability/Action | Verifique se o nome do recurso está disponível |
> | Ação | Microsoft. NetApp/Locations/operationresults/Read | Lê um recurso de resultado de operação. |
> | Ação | Microsoft. NetApp/localizações/leitura | Lê um recurso de verificação de disponibilidade. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/Delete | Exclui um recurso de pool. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/Read | Lê um recurso de pool. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/volumes/Delete | Exclui um recurso de volume. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/volumes/mountTargets/Read | Lê um recurso de destino de montagem. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/volumes/leitura | Lê um recurso de volume. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/volumes/instantâneos/excluir | Exclui um recurso de instantâneo. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/volumes/instantâneos/leitura | Lê um recurso de instantâneo. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/volumes/instantâneos/gravação | Grava um recurso de instantâneo. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/volumes/gravação | Grava um recurso de volume. |
> | Ação | Microsoft. NetApp/netAppAccounts/capacityPools/Write | Grava um recurso de pool. |
> | Ação | Microsoft. NetApp/netAppAccounts/Delete | Exclui um recurso de conta. |
> | Ação | Microsoft. NetApp/netAppAccounts/Read | Lê um recurso de conta. |
> | Ação | Microsoft. NetApp/netAppAccounts/Write | Grava um recurso de conta. |
> | Ação | Microsoft. NetApp/Operations/Read | Lê um recurso de operação. |
> | Ação | Microsoft. NetApp/registro/ação | Registra a assinatura com o provedor de recursos Microsoft. NetApp |
> | Ação | Microsoft. NetApp/cancelar registro/ação | Cancela o registro de assinatura com o provedor de recursos Microsoft. NetApp |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Network/applicationGatewayAvailableRequestHeaders/Read | Obter cabeçalhos de solicitação disponíveis do gateway de aplicativo |
> | Ação | Microsoft. Network/applicationGatewayAvailableResponseHeaders/Read | Obter cabeçalho de resposta disponível do gateway de aplicativo |
> | Ação | Microsoft. Network/applicationGatewayAvailableServerVariables/Read | Obter variáveis de servidor disponíveis do gateway de aplicativo |
> | Ação | Microsoft. Network/applicationGatewayAvailableSslOptions/predefinedPolicies/Read | Política predefinida SSL do gateway de aplicativo |
> | Ação | Microsoft. Network/applicationGatewayAvailableSslOptions/Read | Opções de SSL disponíveis do gateway de aplicativo |
> | Ação | Microsoft. Network/applicationGatewayAvailableWafRuleSets/Read | Obter conjuntos de regras WAF disponíveis do gateway de aplicativo |
> | Ação | Microsoft. Network/applicationGateways/backendAddressPools/junção/ação | Une um pool de endereços de back-end do gateway de aplicativo. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/applicationGateways/backendhealth/Action | Obter uma integridade de back-end do gateway de aplicativo |
> | Ação | Microsoft. Network/applicationGateways/Delete | Excluir um gateway de aplicativo |
> | Ação | Microsoft. Network/applicationGateways/getBackendHealthOnDemand/Action | Obtém uma integridade de back-end do gateway de aplicativo sob demanda para determinada configuração de http e pool de back-end |
> | Ação | Microsoft. Network/applicationGateways/Read | Obter um gateway de aplicativo |
> | Ação | Microsoft. Network/applicationGateways/Start/Action | Inicia um gateway de aplicativo |
> | Ação | Microsoft. Network/applicationGateways/Stop/Action | Para um gateway de aplicativo |
> | Ação | Microsoft. Network/applicationGateways/Write | Cria um gateway de aplicativo ou atualiza um gateway de aplicativo |
> | Ação | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Delete | Exclui uma política de WAF do gateway de aplicativo |
> | Ação | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Read | Obter uma política de WAF do gateway de aplicativo |
> | Ação | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Write | Cria uma política de WAF do gateway de aplicativo ou atualiza uma política de WAF do gateway de aplicativo |
> | Ação | Microsoft. Network/applicationSecurityGroups/Delete | Exclui um grupo de segurança de aplicativo |
> | Ação | Microsoft. Network/applicationSecurityGroups/joinIpConfiguration/Action | Une uma configuração de IP aos grupos de segurança do aplicativo. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/applicationSecurityGroups/joinNetworkSecurityRule/Action | Une uma regra de segurança aos grupos de segurança do aplicativo. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/applicationSecurityGroups/listIpConfigurations/Action | Lista as configurações de IP no ApplicationSecurityGroup |
> | Ação | Microsoft. Network/applicationSecurityGroups/Read | Obtém uma ID do grupo de segurança do aplicativo. |
> | Ação | Microsoft. Network/applicationSecurityGroups/Write | Cria um grupo de segurança de aplicativo ou atualiza um grupo de segurança de aplicativo existente. |
> | Ação | Microsoft. Network/azureFirewallFqdnTags/Read | Obter marcas de FQDN do firewall do Azure |
> | Ação | Microsoft. Network/azurefirewalls/Delete | Excluir o Firewall do Azure |
> | Ação | Microsoft. Network/azurefirewalls/Read | Obter o Firewall do Azure |
> | Ação | Microsoft. Network/azurefirewalls/Write | Criar ou atualizar um firewall do Azure |
> | Ação | Microsoft. Network/bastionHosts/Delete | Exclui um host bastião |
> | Ação | Microsoft. Network/bastionHosts/Read | Obter um host bastião |
> | Ação | Microsoft. Network/bastionHosts/Write | Criar ou atualizar um host bastião |
> | Ação | Microsoft. Network/bgpServiceCommunities/Read | Obter comunidades de serviço BGP |
> | Ação | Microsoft. Network/checkFrontDoorNameAvailability/Action | Verifica se um nome de porta frontal está disponível |
> | Ação | Microsoft. Network/checkTrafficManagerNameAvailability/Action | Verifica a disponibilidade de um nome DNS relativo do Gerenciador de tráfego. |
> | Ação | Microsoft. Network/Connections/Delete | Exclui VirtualNetworkGatewayConnection |
> | Ação | Microsoft. Network/Connections/Read | Obtém VirtualNetworkGatewayConnection |
> | Ação | Microsoft. Network/Connections/REVOKE/Action | Marca um status de conexão de rota expressa como revogado |
> | Ação | Microsoft. Network/Connections/SharedKey/Action | Obter VirtualNetworkGatewayConnection SharedKey |
> | Ação | Microsoft. Network/Connections/sharedKey/Read | Obtém VirtualNetworkGatewayConnection SharedKey |
> | Ação | Microsoft. Network/Connections/sharedKey/Write | Criar ou atualizar um VirtualNetworkGatewayConnection SharedKey existente |
> | Ação | Microsoft. Network/Connections/vpndeviceconfigurationscript/Action | Obtém a configuração do dispositivo VPN de VirtualNetworkGatewayConnection |
> | Ação | Microsoft. Network/Connections/Write | Criar ou atualizar um VirtualNetworkGatewayConnection existente |
> | Ação | Microsoft. Network/ddosCustomPolicies/Delete | Exclui uma política personalizada de DDoS |
> | Ação | Microsoft. Network/ddosCustomPolicies/Read | Obter uma definição de definição de política personalizada de DDoS |
> | Ação | Microsoft. Network/ddosCustomPolicies/Write | Cria uma política personalizada de DDoS ou atualiza uma política personalizada de DDoS existente |
> | Ação | Microsoft. Network/ddosProtectionPlans/Delete | Excluir um plano de proteção contra DDoS |
> | Ação | Microsoft. Network/ddosProtectionPlans/junção/ação | Une um plano de proteção contra DDoS. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/ddosProtectionPlans/Read | Obter um plano de proteção contra DDoS |
> | Ação | Microsoft. Network/ddosProtectionPlans/Write | Cria um plano de proteção contra DDoS ou atualiza um plano de proteção contra DDoS  |
> | Ação | Microsoft. Network/dnsoperationresults/Read | Obtém os resultados de uma operação DNS |
> | Ação | Microsoft. Network/dnsoperationstatuses/Read | Obtém o status de uma operação DNS  |
> | Ação | Microsoft. Network/dnszones/A/Delete | Remova o conjunto de registros de um determinado nome e digite ' A ' de uma zona DNS. |
> | Ação | Microsoft. Network/dnszones/A/Read | Obter o conjunto de registros do tipo ' A ', no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft. Network/dnszones/A/Write | Criar ou atualizar um conjunto de registros do tipo ' A ' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft. Network/dnszones/AAAA/Delete | Remova o conjunto de registros de um determinado nome e digite ' AAAA ' de uma zona DNS. |
> | Ação | Microsoft. Network/dnszones/AAAA/Read | Obter o conjunto de registros do tipo ' AAAA ' no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft. Network/dnszones/AAAA/Write | Criar ou atualizar um conjunto de registros do tipo ' AAAA ' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft. Network/dnszones/All/Read | Obtém conjuntos de registros DNS entre tipos |
> | Ação | Microsoft. Network/dnszones/CAA/Delete | Remova o conjunto de registros de um determinado nome e digite ' CAA ' de uma zona DNS. |
> | Ação | Microsoft. Network/dnszones/CAA/Read | Obter o conjunto de registros do tipo ' CAA ' no formato JSON. O conjunto de registros contém o TTL, as marcas e a ETag. |
> | Ação | Microsoft. Network/dnszones/CAA/Write | Criar ou atualizar um conjunto de registros do tipo ' CAA ' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft. Network/dnszones/CNAME/Delete | Remova o conjunto de registros de um determinado nome e digite ' CNAME ' de uma zona DNS. |
> | Ação | Microsoft. Network/dnszones/CNAME/Read | Obter o conjunto de registros do tipo ' CNAME ' no formato JSON. O conjunto de registros contém o TTL, as marcas e a ETag. |
> | Ação | Microsoft. Network/dnszones/CNAME/Write | Criar ou atualizar um conjunto de registros do tipo ' CNAME ' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft. Network/dnszones/Delete | Exclua a zona DNS, no formato JSON. As propriedades de zona incluem marcas, ETag, numberOfRecordSets e maxNumberOfRecordSets. |
> | Ação | Microsoft. Network/dnszones/MX/Delete | Remova o conjunto de registros de um determinado nome e digite "MX" de uma zona DNS. |
> | Ação | Microsoft. Network/dnszones/MX/Read | Obter o conjunto de registros do tipo ' MX ' no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft. Network/dnszones/MX/Write | Criar ou atualizar um conjunto de registros do tipo ' MX ' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft. Network/dnszones/NS/Delete | Exclui o conjunto de registros DNS do tipo NS |
> | Ação | Microsoft. Network/dnszones/NS/Read | Obtém o conjunto de registros DNS do tipo NS |
> | Ação | Microsoft. Network/dnszones/NS/Write | Cria ou atualiza o conjunto de registros DNS do tipo NS |
> | Ação | Microsoft. Network/dnszones/PTR/Delete | Remova o conjunto de registros de um determinado nome e digite ' PTR ' de uma zona DNS. |
> | Ação | Microsoft. Network/dnszones/PTR/Read | Obter o conjunto de registros do tipo ' PTR ' no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft. Network/dnszones/PTR/Write | Criar ou atualizar um conjunto de registros do tipo ' PTR ' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft. Network/dnszones/Read | Obtenha a zona DNS no formato JSON. As propriedades de zona incluem marcas, ETag, numberOfRecordSets e maxNumberOfRecordSets. Observe que esse comando não recupera os conjuntos de registros contidos na zona. |
> | Ação | Microsoft. Network/dnszones/Recordsets/Read | Obtém conjuntos de registros DNS entre tipos |
> | Ação | Microsoft. Network/dnszones/SOA/Read | Obtém o conjunto de registros DNS do tipo SOA |
> | Ação | Microsoft. Network/dnszones/SOA/Write | Cria ou atualiza o conjunto de registros DNS do tipo SOA |
> | Ação | Microsoft. Network/dnszones/SRV/excluir | Remova o conjunto de registros de um determinado nome e digite ' SRV ' de uma zona DNS. |
> | Ação | Microsoft. Network/dnszones/SRV/leitura | Obter o conjunto de registros do tipo ' SRV ' no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft. Network/dnszones/SRV/gravação | Criar ou atualizar conjunto de registros do tipo SRV |
> | Ação | Microsoft. Network/dnszones/TXT/Delete | Remova o conjunto de registros de um determinado nome e digite ' TXT ' de uma zona DNS. |
> | Ação | Microsoft. Network/dnszones/TXT/ler | Obter o conjunto de registros do tipo ' TXT ' no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft. Network/dnszones/TXT/Write | Criar ou atualizar um conjunto de registros do tipo ' TXT ' em uma zona DNS. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft. Network/dnszones/Write | Criar ou atualizar uma zona DNS dentro de um grupo de recursos.  Usado para atualizar as marcas em um recurso de zona DNS. Observe que esse comando não pode ser usado para criar ou atualizar conjuntos de registros dentro da zona. |
> | Ação | Microsoft. Network/expressRouteCircuits/autorizations/Delete | Exclui uma autorização de ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/autorizações/leitura | Obter uma autorização de ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/autorizations/Write | Criar ou atualizar uma autorização de ExpressRouteCircuit existente |
> | Ação | Microsoft. Network/expressRouteCircuits/Delete | Exclui um ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/junção/ação | Une um circuito de rota expressa. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/arpTables/leitura | Obter um ArpTable de emparelhamento ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/conexões/exclusão | Exclui uma conexão ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/conexões/leitura | Obter uma conexão ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/conexões/gravação | Criar ou atualizar um recurso de conexão ExpressRouteCircuit existente |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/exclusão | Exclui um emparelhamento ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/peerConnections/leitura | Obtém conexão de circuito de rota expressa do par |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/leitura | Obter um emparelhamento ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/routeTables/leitura | Obter uma rota de emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/routeTablesSummary/leitura | Obter um resumo de rota de emparelhamento de ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/estatísticas/leitura | Obter uma estatística de emparelhamento ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/emparelhamentos/gravação | Criar ou atualizar um emparelhamento de ExpressRouteCircuit existente |
> | Ação | Microsoft. Network/expressRouteCircuits/Read | Obter um ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/stats/Read | Obtém uma estatística ExpressRouteCircuit |
> | Ação | Microsoft. Network/expressRouteCircuits/Write | Criar ou atualizar um ExpressRouteCircuit existente |
> | Ação | Microsoft. Network/expressRouteCrossConnections/junção/ação | Une uma conexão cruzada de rota expressa. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/expressRouteCrossConnections/emparelhamentos/arpTables/leitura | Obter uma tabela ARP de emparelhamento de conexão cruzada de rota expressa |
> | Ação | Microsoft. Network/expressRouteCrossConnections/emparelhamentos/exclusão | Exclui um emparelhamento de conexão cruzada de rota expressa |
> | Ação | Microsoft. Network/expressRouteCrossConnections/emparelhamentos/leitura | Obter um emparelhamento de conexão cruzada de rota expressa |
> | Ação | Microsoft. Network/expressRouteCrossConnections/emparelhamentos/routeTables/leitura | Obter uma tabela de rota de emparelhamento de conexão cruzada de rota expressa |
> | Ação | Microsoft. Network/expressRouteCrossConnections/emparelhamentos/routeTableSummary/leitura | Obter um resumo da tabela de rotas de emparelhamento de conexão cruzada de rota expressa |
> | Ação | Microsoft. Network/expressRouteCrossConnections/emparelhamentos/gravação | Cria um emparelhamento de conexão cruzada de rota expressa ou atualiza um emparelhamento de conexão cruzada de rota expressa existente |
> | Ação | Microsoft. Network/expressRouteCrossConnections/Read | Obter conexão cruzada de rota expressa |
> | Ação | Microsoft. Network/expressRouteGateways/expressRouteConnections/Delete | Excluir uma conexão de rota expressa |
> | Ação | Microsoft. Network/expressRouteGateways/expressRouteConnections/Read | Obter uma conexão de rota expressa |
> | Ação | Microsoft. Network/expressRouteGateways/expressRouteConnections/Write | Cria uma conexão de rota expressa ou atualiza uma conexão de rota expressa existente |
> | Ação | Microsoft. Network/expressRouteGateways/junção/ação | Une um gateway de rota expressa. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/expressRouteGateways/Read | Obter gateway de rota expressa |
> | Ação | Microsoft. Network/expressRoutePorts/Delete | Exclui ExpressRoutePorts |
> | Ação | Microsoft. Network/expressRoutePorts/junção/ação | Une portas de rota expressa. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/expressRoutePorts/links/leitura | Obtém ExpressRouteLink |
> | Ação | Microsoft. Network/expressRoutePorts/Read | Obtém ExpressRoutePorts |
> | Ação | Microsoft. Network/expressRoutePorts/Write | Criar ou atualizar ExpressRoutePorts |
> | Ação | Microsoft. Network/expressRoutePortsLocations/Read | Obter locais de portas de rota expressa |
> | Ação | Microsoft. Network/expressRouteServiceProviders/Read | Obter provedores de Serviço de Roteiros expressos |
> | Ação | Microsoft. Network/firewallPolicies/Delete | Exclui uma política de firewall |
> | Ação | Microsoft. Network/firewallPolicies/junção/ação | Une uma política de firewall. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/firewallPolicies/Read | Obter uma política de firewall |
> | Ação | Microsoft. Network/firewallPolicies/grupo/Delete | Excluir um grupo de regras de política de firewall |
> | Ação | Microsoft. Network/firewallPolicies/grupo/Read | Obter um grupo de regras de política de firewall |
> | Ação | Microsoft. Network/firewallPolicies/grupo/Write | Cria um grupo de regras de política de firewall ou atualiza um grupo de regras de política de firewall existente |
> | Ação | Microsoft. Network/firewallPolicies/Write | Cria uma política de firewall ou atualiza uma política de firewall existente |
> | Ação | Microsoft. Network/frontDoors/backendPools/Delete | Exclui um pool de back-end |
> | Ação | Microsoft. Network/frontDoors/backendPools/Read | Obter um pool de back-end |
> | Ação | Microsoft. Network/frontDoors/backendPools/Write | Criar ou atualizar um pool de back-end |
> | Ação | Microsoft. Network/frontDoors/Delete | Exclui uma porta frontal |
> | Ação | Microsoft. Network/frontDoors/frontendEndpoints/Delete | Exclui um ponto de extremidade de front-end |
> | Ação | Microsoft. Network/frontDoors/frontendEndpoints/disableHttps/Action | Desabilita HTTPS em um ponto de extremidade de front-end |
> | Ação | Microsoft. Network/frontDoors/frontendEndpoints/enableHttps/Action | Habilita HTTPS em um ponto de extremidade de front-end |
> | Ação | Microsoft. Network/frontDoors/frontendEndpoints/Read | Obtém um ponto de extremidade de front-end |
> | Ação | Microsoft. Network/frontDoors/frontendEndpoints/Write | Cria ou atualiza um ponto de extremidade de front-end |
> | Ação | Microsoft. Network/frontDoors/healthProbeSettings/Delete | Exclui as configurações de investigação de integridade |
> | Ação | Microsoft. Network/frontDoors/healthProbeSettings/Read | Obtém as configurações de investigação de integridade |
> | Ação | Microsoft. Network/frontDoors/healthProbeSettings/Write | Criar ou atualizar configurações de investigação de integridade |
> | Ação | Microsoft. Network/frontDoors/loadBalancingSettings/Delete | Criar ou atualizar configurações de balanceamento de carga |
> | Ação | Microsoft. Network/frontDoors/loadBalancingSettings/Read | Obtém as configurações de balanceamento de carga |
> | Ação | Microsoft. Network/frontDoors/loadBalancingSettings/Write | Criar ou atualizar configurações de balanceamento de carga |
> | Ação | Microsoft. Network/frontDoors/limpeza/ação | Limpar o conteúdo armazenado em cache de uma porta frontal |
> | Ação | Microsoft. Network/frontDoors/Read | Obter uma porta frontal |
> | Ação | Microsoft. Network/frontDoors/routingRules/Delete | Excluir uma regra de roteamento |
> | Ação | Microsoft. Network/frontDoors/routingRules/Read | Obter uma regra de roteamento |
> | Ação | Microsoft. Network/frontDoors/routingRules/Write | Criar ou atualizar uma regra de roteamento |
> | Ação | Microsoft. Network/frontDoors/validateCustomDomain/Action | Valida um ponto de extremidade de front-end para uma porta frontal |
> | Ação | Microsoft. Network/frontDoors/Write | Criar ou atualizar uma porta frontal |
> | Ação | Microsoft. Network/frontDoorWebApplicationFirewallManagedRuleSets/Read | Obter conjuntos de regras gerenciadas pelo firewall do aplicativo Web |
> | Ação | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Delete | Exclui uma política de firewall do aplicativo Web |
> | Ação | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Read | Obter uma política de firewall do aplicativo Web |
> | Ação | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Write | Criar ou atualizar uma política de firewall do aplicativo Web |
> | Ação | Microsoft. Network/balancers/backendAddressPools/junção/ação | Une um pool de endereços de back-end do balanceador de carga. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/balancers/backendAddressPools/Read | Obter uma definição de pool de endereços de back-end do balanceador de carga |
> | Ação | Microsoft. Network/loadbalanceadores/excluir | Exclui um balanceador de carga |
> | Ação | Microsoft. Network/balancers/frontendIPConfigurations/junção/ação | Une uma configuração de IP de front-end Load Balancer. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/balancers/frontendIPConfigurations/Read | Obter uma definição de configuração de IP de front-end do balanceador de carga |
> | Ação | Microsoft. Network/balancers/inboundNatPools/junção/ação | Une um pool de NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/balancers/inboundNatPools/Read | Obter uma definição de pool de NAT de entrada do balanceador de carga |
> | Ação | Microsoft. Network/loadbalanceadores/inboundNatRules/Delete | Exclui uma regra NAT de entrada do balanceador de carga |
> | Ação | Microsoft. Network/balancers/inboundNatRules/junção/ação | Une uma regra NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/balancers/inboundNatRules/Read | Obter uma definição de regra NAT de entrada do balanceador de carga |
> | Ação | Microsoft. Network/balancers/inboundNatRules/Write | Cria uma regra NAT de entrada do balanceador de carga ou atualiza uma regra NAT de entrada do balanceador de carga existente |
> | Ação | Microsoft. Network/balancers/loadBalancingRules/Read | Obter uma definição de regra de balanceamento de carga do balanceador de carga |
> | Ação | Microsoft. Network/loadbalanceadores/networkInterfaces/leitura | Obtém referências a todas as interfaces de rede em um balanceador de carga |
> | Ação | Microsoft. Network/balancers/outboundRules/Read | Obter uma definição de regra de saída do balanceador de carga |
> | Ação | Microsoft. Network/balancers/investigações/junção/ação | Permite o uso de investigações de um balanceador de carga. Por exemplo, com essa permissão, a propriedade healthProbe do conjunto de dimensionamento de VM pode fazer referência à investigação. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/loadbalanceadores/investigações/leitura | Obter uma investigação do balanceador de carga |
> | Ação | Microsoft. Network/balancers/Read | Obter uma definição de balanceador de carga |
> | Ação | Microsoft. Network/balancers/virtualMachines/Read | Obtém referências a todas as máquinas virtuais em um balanceador de carga |
> | Ação | Microsoft. Network/balancers/Write | Cria um balanceador de carga ou atualiza um balanceador de carga existente |
> | Ação | Microsoft. Network/localnetworkgateways/Delete | Exclui LocalNetworkGateway |
> | Ação | Microsoft. Network/localnetworkgateways/Read | Obtém LocalNetworkGateway |
> | Ação | Microsoft. Network/localnetworkgateways/Write | Criar ou atualizar um LocalNetworkGateway existente |
> | Ação | Microsoft. Network/Locations/autoApprovedPrivateLinkServices/Read | Obter serviços de vínculo privado aprovados automaticamente |
> | Ação | Microsoft. Network/Locations/availableDelegations/Read | Obtém as delegações disponíveis |
> | Ação | Microsoft. Network/Locations/availablePrivateEndpointTypes/Read | Obtém recursos de ponto de extremidade privados disponíveis |
> | Ação | Microsoft. Network/Locations/availableServiceAliases/Read | Obtém os aliases de serviço disponíveis |
> | Ação | Microsoft. Network/Locations/bareMetalTenants/Action | Aloca ou valida um locatário bare-metal |
> | Ação | Microsoft. Network/Locations/checkAcceleratedNetworkingSupport/Action | Verifica o suporte à rede acelerada |
> | Ação | Microsoft. Network/Locations/checkDnsNameAvailability/Read | Verifica se o rótulo DNS está disponível no local especificado |
> | Ação | Microsoft. Network/Locations/checkPrivateLinkServiceVisibility/Action | Verifica a visibilidade do serviço de vínculo privado |
> | Ação | Microsoft. Network/Locations/operationResults/Read | Obtém o resultado da operação de uma operação de POSTAgem assíncrona ou de exclusão |
> | Ação | Microsoft. Network/Locations/Operations/Read | Obtém o recurso de operação que representa o status de uma operação assíncrona |
> | Ação | Microsoft. Network/Locations/objecttags/leitura | Obter marcas de serviço |
> | Ação | Microsoft. Network/Locations/supportedVirtualMachineSizes/Read | Obtém tamanhos de máquinas virtuais com suporte |
> | Ação | Microsoft. Network/Locations/usos/leitura | Obtém as métricas de uso de recursos |
> | Ação | Microsoft. Network/Locations/virtualNetworkAvailableEndpointServices/Read | Obter uma lista de serviços de ponto de extremidade de rede virtual disponíveis |
> | Ação | Microsoft. Network/networkIntentPolicies/Delete | Excluir uma política de intenção de rede |
> | Ação | Microsoft. Network/networkIntentPolicies/Read | Obtém uma descrição da política de intenção de rede |
> | Ação | Microsoft. Network/networkIntentPolicies/Write | Cria uma política de intenção de rede ou atualiza uma política de tentativa de rede existente |
> | Ação | Microsoft. Network/networkInterfaces/excluir | Exclui uma interface de rede |
> | Ação | Microsoft. Network/networkInterfaces/effectiveNetworkSecurityGroups/Action | Obter grupos de segurança de rede configurados na interface de rede da VM |
> | Ação | Microsoft. Network/networkInterfaces/effectiveRouteTable/Action | Obter tabela de rotas configurada na interface de rede da VM |
> | Ação | Microsoft. Network/networkInterfaces/ipconfigurations/junção/ação | Une uma configuração de IP de interface de rede. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/networkInterfaces/ipconfigurations/Read | Obtém uma definição de configuração de IP de interface de rede.  |
> | Ação | Microsoft. Network/networkInterfaces/junção/ação | Une uma máquina virtual a uma interface de rede. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/networkInterfaces/balanceadores/leitura | Obtém todos os balanceadores de carga dos quais a interface de rede faz parte |
> | Ação | Microsoft. Network/networkInterfaces/Read | Obtém uma definição de interface de rede.  |
> | Ação | Microsoft. Network/networkInterfaces/tapConfigurations/Delete | Exclui uma configuração de toque de interface de rede. |
> | Ação | Microsoft. Network/networkInterfaces/tapConfigurations/Read | Obtém uma configuração de toque de interface de rede. |
> | Ação | Microsoft. Network/networkInterfaces/tapConfigurations/Write | Cria uma interface de rede toque em configuração ou atualiza uma interface de rede existente toque em configuração. |
> | Ação | Microsoft. Network/networkInterfaces/Write | Cria uma interface de rede ou atualiza uma interface de rede existente.  |
> | Ação | Microsoft. Network/networkProfiles/Delete | Exclui um perfil de rede |
> | Ação | Microsoft. Network/networkProfiles/Read | Obter um perfil de rede |
> | Ação | Microsoft. Network/networkProfiles/removeContainers/Action | Remove contêineres |
> | Ação | Microsoft. Network/networkProfiles/setcontainers/Action | Define contêineres |
> | Ação | Microsoft. Network/networkProfiles/setnetworkinterfaces/ação | Define as interfaces de rede do contêiner |
> | Ação | Microsoft. Network/networkProfiles/Write | Criar ou atualizar um perfil de rede |
> | Ação | Microsoft. Network/networkSecurityGroups/defaultSecurityRules/Read | Obtém uma definição de regra de segurança padrão |
> | Ação | Microsoft. Network/networkSecurityGroups/Delete | Excluir um grupo de segurança de rede |
> | Ação | Microsoft. Network/networkSecurityGroups/junção/ação | Une um grupo de segurança de rede. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/networkSecurityGroups/Read | Obter uma definição de grupo de segurança de rede |
> | Ação | Microsoft. Network/networkSecurityGroups/securityRules/Delete | Excluir uma regra de segurança |
> | Ação | Microsoft. Network/networkSecurityGroups/securityRules/Read | Obter uma definição de regra de segurança |
> | Ação | Microsoft. Network/networkSecurityGroups/securityRules/Write | Cria uma regra de segurança ou atualiza uma regra de segurança existente |
> | Ação | Microsoft. Network/networkSecurityGroups/Write | Cria um grupo de segurança de rede ou atualiza um grupo de segurança de rede existente |
> | Ação | Microsoft. Network/networkWatchers/availableProvidersList/Action | Retorna todos os provedores de serviços de Internet disponíveis para uma região do Azure especificada. |
> | Ação | Microsoft. Network/networkWatchers/azureReachabilityReport/Action | Retorna a pontuação de latência relativa para provedores de serviços de Internet de um local especificado para regiões do Azure. |
> | Ação | Microsoft. Network/networkWatchers/configureFlowLog/Action | Configura o log de fluxo para um recurso de destino. |
> | Ação | Microsoft. Network/networkWatchers/connectionMonitors/Delete | Exclui um monitor de conexão |
> | Ação | Microsoft. Network/networkWatchers/connectionMonitors/consulta/ação | Consultar a conectividade de monitoramento entre os pontos de extremidade especificados |
> | Ação | Microsoft. Network/networkWatchers/connectionMonitors/Read | Obter detalhes do monitor de conexão |
> | Ação | Microsoft. Network/networkWatchers/connectionMonitors/Start/Action | Iniciar Monitoramento de conectividade entre os pontos de extremidade especificados |
> | Ação | Microsoft. Network/networkWatchers/connectionMonitors/Stop/Action | Parar/pausar a conectividade de monitoramento entre os pontos de extremidade especificados |
> | Ação | Microsoft. Network/networkWatchers/connectionMonitors/Write | Cria um monitor de conexão |
> | Ação | Microsoft. Network/networkWatchers/connectivityCheck/Action | Verifica a possibilidade de estabelecer uma conexão TCP direta de uma máquina virtual com um determinado ponto de extremidade, incluindo outra VM ou um servidor remoto arbitrário. |
> | Ação | Microsoft. Network/networkWatchers/Delete | Excluir um observador de rede |
> | Ação | Microsoft. Network/networkWatchers/ipFlowVerify/Action | Retorna se o pacote é permitido ou negado para ou de um destino específico. |
> | Ação | Microsoft. Network/networkWatchers/lentes/Delete | Exclui uma lente |
> | Ação | Microsoft. Network/networkWatchers/lentes/consulta/ação | Consultar o monitoramento de tráfego de rede em um ponto de extremidade especificado |
> | Ação | Microsoft. Network/networkWatchers/lentes/leitura | Obter detalhes da lente |
> | Ação | Microsoft. Network/networkWatchers/lentes/iniciar/ação | Iniciar o monitoramento do tráfego de rede em um ponto de extremidade especificado |
> | Ação | Microsoft. Network/networkWatchers/lentes/Stop/Action | Parar/pausar o monitoramento do tráfego de rede em um ponto de extremidade especificado |
> | Ação | Microsoft. Network/networkWatchers/lentes/gravação | Cria uma lente |
> | Ação | Microsoft. Network/networkWatchers/networkConfigurationDiagnostic/Action | Diagnóstico da configuração de rede. |
> | Ação | Microsoft. Network/networkWatchers/nextHop/Action | Para um endereço IP de destino e destino especificado, retorne o tipo do próximo salto e o próximo endereço IP da esperança. |
> | Ação | Microsoft. Network/networkWatchers/packetCaptures/Delete | Exclui uma captura de pacote |
> | Ação | Microsoft. Network/networkWatchers/packetCaptures/queryStatus/Action | Obtém informações sobre as propriedades e o status de um recurso de captura de pacote. |
> | Ação | Microsoft. Network/networkWatchers/packetCaptures/Read | Obter a definição de captura de pacote |
> | Ação | Microsoft. Network/networkWatchers/packetCaptures/Stop/Action | Pare a sessão de captura de pacote em execução. |
> | Ação | Microsoft. Network/networkWatchers/packetCaptures/Write | Cria uma captura de pacote |
> | Ação | Microsoft. Network/networkWatchers/pingMeshes/Delete | Exclui um PingMesh |
> | Ação | Microsoft. Network/networkWatchers/pingMeshes/Read | Obter detalhes do PingMesh |
> | Ação | Microsoft. Network/networkWatchers/pingMeshes/Start/Action | Iniciar PingMesh entre as VMs especificadas |
> | Ação | Microsoft. Network/networkWatchers/pingMeshes/Stop/Action | Parar PingMesh entre as VMs especificadas |
> | Ação | Microsoft. Network/networkWatchers/pingMeshes/Write | Cria um PingMesh |
> | Ação | Microsoft. Network/networkWatchers/queryConnectionMonitors/Action | Consulta do lote monitorando a conectividade entre os pontos de extremidade especificados |
> | Ação | Microsoft. Network/networkWatchers/queryFlowLogStatus/Action | Obtém o status do log de fluxo em um recurso. |
> | Ação | Microsoft. Network/networkWatchers/queryTroubleshootResult/Action | Obtém o resultado da solução de problemas da operação de solução de problema executada anteriormente ou em execução no momento. |
> | Ação | Microsoft. Network/networkWatchers/Read | Obter a definição do observador de rede |
> | Ação | Microsoft. Network/networkWatchers/securityGroupView/Action | Exiba as regras de grupo de segurança de rede configuradas e efetivas aplicadas em uma VM. |
> | Ação | Microsoft. Network/networkWatchers/topologia/ação | Obtém uma exibição no nível da rede de recursos e suas relações em um grupo de recursos. |
> | Ação | Microsoft. Network/networkWatchers/solução de problemas/ação | Inicia a solução de problemas em um recurso de rede no Azure. |
> | Ação | Microsoft. Network/networkWatchers/Write | Cria um observador de rede ou atualiza um observador de rede existente |
> | Ação | Microsoft. Network/Operations/Read | Obter operações disponíveis |
> | Ação | Microsoft. Network/p2sVpnGateways/Delete | Exclui um P2SVpnGateway. |
> | Ação | Microsoft. Network/p2sVpnGateways/generatevpnprofile/Action | Gerar perfil VPN para P2SVpnGateway |
> | Ação | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealth/Action | Obter uma integridade de conexão VPN P2S para P2SVpnGateway |
> | Ação | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/Action | Obter uma integridade de conexão VPN P2S detalhada para P2SVpnGateway |
> | Ação | Microsoft. Network/p2sVpnGateways/Read | Obtém um P2SVpnGateway. |
> | Ação | Microsoft. Network/p2sVpnGateways/Write | Coloca um P2SVpnGateway. |
> | Ação | Microsoft. Network/privateDnsOperationResults/Read | Obtém os resultados de uma operação de DNS privado |
> | Ação | Microsoft. Network/privateDnsOperationStatuses/Read | Obtém o status de uma operação de DNS privado |
> | Ação | Microsoft. Network/privateDnsZones/A/Delete | Remova o conjunto de registros de um determinado nome e digite ' A ' de uma zona de DNS privado. |
> | Ação | Microsoft. Network/privateDnsZones/A/Read | Obter o conjunto de registros do tipo ' A ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft. Network/privateDnsZones/A/Write | Criar ou atualizar um conjunto de registros do tipo ' A ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft. Network/privateDnsZones/AAAA/Delete | Remova o conjunto de registros de um determinado nome e digite ' AAAA ' de uma zona de DNS privado. |
> | Ação | Microsoft. Network/privateDnsZones/AAAA/Read | Obtenha o conjunto de registros do tipo ' AAAA ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft. Network/privateDnsZones/AAAA/Write | Criar ou atualizar um conjunto de registros do tipo ' AAAA ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft. Network/privateDnsZones/ALL/Read | Obtém DNS privado conjuntos de registros entre tipos |
> | Ação | Microsoft. Network/privateDnsZones/CNAME/Delete | Remova o conjunto de registros de um determinado nome e digite ' CNAME ' de uma zona de DNS privado. |
> | Ação | Microsoft. Network/privateDnsZones/CNAME/Read | Obtenha o conjunto de registros do tipo ' CNAME ' dentro de uma zona de DNS privado, no formato JSON. |
> | Ação | Microsoft. Network/privateDnsZones/CNAME/Write | Criar ou atualizar um conjunto de registros do tipo ' CNAME ' em uma zona DNS privado. |
> | Ação | Microsoft. Network/privateDnsZones/Delete | Excluir uma zona de DNS privado. |
> | Ação | Microsoft. Network/privateDnsZones/MX/Delete | Remova o conjunto de registros de um determinado nome e digite "MX" de uma zona DNS privado. |
> | Ação | Microsoft. Network/privateDnsZones/MX/Read | Obtenha o conjunto de registros do tipo ' MX ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft. Network/privateDnsZones/MX/Write | Criar ou atualizar um conjunto de registros do tipo ' MX ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft. Network/privateDnsZones/PTR/Delete | Remova o conjunto de registros de um determinado nome e digite ' PTR ' de uma zona de DNS privado. |
> | Ação | Microsoft. Network/privateDnsZones/PTR/Read | Obter o conjunto de registros do tipo ' PTR ' dentro de uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft. Network/privateDnsZones/PTR/Write | Criar ou atualizar um conjunto de registros do tipo ' PTR ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft. Network/privateDnsZones/Read | Obtenha as propriedades da zona de DNS privado, no formato JSON. Observe que esse comando não recupera as redes virtuais às quais a zona de DNS privado está vinculada ou os conjuntos de registros contidos na zona. |
> | Ação | Microsoft. Network/privateDnsZones/Recordsets/Read | Obtém DNS privado conjuntos de registros entre tipos |
> | Ação | Microsoft. Network/privateDnsZones/SOA/Read | Obter o conjunto de registros do tipo ' SOA ' em uma zona de DNS privado, no formato JSON. |
> | Ação | Microsoft. Network/privateDnsZones/SOA/Write | Atualizar um conjunto de registros do tipo ' SOA ' em uma zona DNS privado. |
> | Ação | Microsoft. Network/privateDnsZones/SRV/excluir | Remova o conjunto de registros de um determinado nome e digite ' SRV ' de uma zona de DNS privado. |
> | Ação | Microsoft. Network/privateDnsZones/SRV/leitura | Obtenha o conjunto de registros do tipo ' SRV ' em uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft. Network/privateDnsZones/SRV/gravação | Criar ou atualizar um conjunto de registros do tipo ' SRV ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft. Network/privateDnsZones/TXT/Delete | Remova o conjunto de registros de um determinado nome e digite ' TXT ' de uma zona DNS privado. |
> | Ação | Microsoft. Network/privateDnsZones/TXT/ler | Obtenha o conjunto de registros do tipo ' TXT ' dentro de uma zona de DNS privado, no formato JSON. O conjunto de registros contém uma lista de registros, bem como TTL, marcas e ETag. |
> | Ação | Microsoft. Network/privateDnsZones/TXT/Write | Criar ou atualizar um conjunto de registros do tipo ' TXT ' em uma zona DNS privado. Os registros especificados substituirão os registros atuais no conjunto de registros. |
> | Ação | Microsoft. Network/privateDnsZones/virtualNetworkLinks/Delete | Exclua um link de zona de DNS privado para a rede virtual. |
> | Ação | Microsoft. Network/privateDnsZones/virtualNetworkLinks/Read | Obtenha o link de zona de DNS privado para as propriedades de rede virtual, no formato JSON. |
> | Ação | Microsoft. Network/privateDnsZones/virtualNetworkLinks/Write | Crie ou atualize um link de zona de DNS privado para a rede virtual. |
> | Ação | Microsoft. Network/privateDnsZones/Write | Criar ou atualizar uma zona de DNS privado dentro de um grupo de recursos. Observe que esse comando não pode ser usado para criar ou atualizar links de rede virtual ou conjuntos de registros dentro da zona. |
> | Ação | Microsoft. Network/privateEndpointRedirectMaps/Read | Obtém um ponto de extremidade privado RedirectMap |
> | Ação | Microsoft. Network/privateEndpointRedirectMaps/Write | Cria um ponto de extremidade privado RedirectMap ou atualiza um ponto de extremidade privado existente RedirectMap |
> | Ação | Microsoft. Network/privateEndpoints/Delete | Exclui um recurso de ponto de extremidade privado. |
> | Ação | Microsoft. Network/privateEndpoints/Read | Obtém um recurso de ponto de extremidade privado. |
> | Ação | Microsoft. Network/privateEndpoints/Write | Cria um novo ponto de extremidade privado ou atualiza um ponto de extremidade privado existente. |
> | Ação | Microsoft. Network/privateLinkServices/Delete | Exclui um recurso de serviço de vínculo privado. |
> | Ação | Microsoft. Network/privateLinkServices/privateEndpointConnections/Delete | Exclui uma conexão de ponto de extremidade privado. |
> | Ação | Microsoft. Network/privateLinkServices/privateEndpointConnections/Read | Obtém uma definição de conexão de ponto de extremidade particular. |
> | Ação | Microsoft. Network/privateLinkServices/privateEndpointConnections/Write | Cria uma nova conexão de ponto de extremidade privado ou atualiza uma conexão de ponto de extremidade privada existente. |
> | Ação | Microsoft. Network/privateLinkServices/Read | Obtém um recurso de serviço de vínculo privado. |
> | Ação | Microsoft. Network/privateLinkServices/Write | Cria um novo serviço de vínculo privado ou atualiza um serviço de vínculo privado existente. |
> | Ação | Microsoft. Network/publicIPAddresses/Delete | Exclui um endereço IP público. |
> | Ação | Microsoft. Network/publicIPAddresses/junção/ação | Une um endereço IP público. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/publicIPAddresses/Read | Obtém uma definição de endereço IP público. |
> | Ação | Microsoft. Network/publicIPAddresses/Write | Cria um endereço IP público ou atualiza um endereço IP público existente.  |
> | Ação | Microsoft. Network/publicIPPrefixes/Delete | Exclui um prefixo IP público |
> | Ação | Microsoft. Network/publicIPPrefixes/junção/ação | Une um PublicIPPrefix. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/publicIPPrefixes/Read | Obter uma definição de prefixo de IP público |
> | Ação | Microsoft. Network/publicIPPrefixes/Write | Cria um prefixo IP público ou atualiza um prefixo IP público existente |
> | Ação | Microsoft. Network/registro/ação | Registra a assinatura |
> | Ação | Microsoft. Network/routeFilters/Delete | Exclui uma definição de filtro de rota |
> | Ação | Microsoft. Network/routeFilters/junção/ação | Une um filtro de rota. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/routeFilters/Read | Obter uma definição de filtro de rota |
> | Ação | Microsoft. Network/routeFilters/routeFilterRules/Delete | Exclui uma definição de regra de filtro de rota |
> | Ação | Microsoft. Network/routeFilters/routeFilterRules/Read | Obter uma definição de regra de filtro de rota |
> | Ação | Microsoft. Network/routeFilters/routeFilterRules/Write | Cria uma regra de filtro de rota ou atualiza uma regra de filtro de rota existente |
> | Ação | Microsoft. Network/routeFilters/Write | Cria um filtro de rota ou atualiza um filtro de rota existente |
> | Ação | Microsoft. Network/routeTables/Delete | Exclui uma definição de tabela de rotas |
> | Ação | Microsoft. Network/routeTables/junção/ação | Une uma tabela de rotas. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/routeTables/Read | Obter uma definição de tabela de rotas |
> | Ação | Microsoft. Network/routeTables/Routes/Delete | Exclui uma definição de rota |
> | Ação | Microsoft. Network/routeTables/Routes/Read | Obter uma definição de rota |
> | Ação | Microsoft. Network/routeTables/Routes/Write | Cria uma rota ou atualiza uma rota existente |
> | Ação | Microsoft. Network/routeTables/Write | Cria uma tabela de rotas ou atualiza uma tabela de rotas existente |
> | Ação | Microsoft. Network/securegateways/Delete | Excluir gateway seguro |
> | Ação | Microsoft. Network/securegateways/Read | Obter gateway seguro |
> | Ação | Microsoft. Network/securegateways/Write | Criar ou atualizar um gateway seguro |
> | Ação | Microsoft. Network/serviceEndpointPolicies/Delete | Exclui uma política de ponto de extremidade de serviço |
> | Ação | Microsoft. Network/serviceEndpointPolicies/junção/ação | Une uma política de ponto de extremidade de serviço. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/serviceEndpointPolicies/joinSubnet/Action | Une uma sub-rede a políticas de ponto de extremidade de serviço. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/serviceEndpointPolicies/Read | Obtém uma descrição da política de ponto de extremidade de serviço |
> | Ação | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Delete | Exclui uma definição de política de ponto de extremidade de serviço |
> | Ação | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Read | Obtém uma descrição de definição de política de ponto de extremidade de serviço |
> | Ação | Microsoft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/Write | Cria uma definição de política de ponto de extremidade de serviço ou atualiza uma definição de política de ponto de extremidade de serviço |
> | Ação | Microsoft. Network/serviceEndpointPolicies/Write | Cria uma política de ponto de extremidade de serviço ou atualiza uma política de ponto de extremidade de serviço existente |
> | Ação | Microsoft. Network/trafficManagerGeographicHierarchies/Read | Obtém a hierarquia geográfica do Gerenciador de tráfego que contém regiões que podem ser usadas com o método de roteamento de tráfego geográfico |
> | Ação | Microsoft. Network/trafficManagerProfiles/azureEndpoints/Delete | Exclui um Ponto de Extremidade do Azure de um perfil do Gerenciador de tráfego existente. O Gerenciador de tráfego deixará de rotear o tráfego para o Ponto de Extremidade do Azure excluído. |
> | Ação | Microsoft. Network/trafficManagerProfiles/azureEndpoints/Read | Obtém um Ponto de Extremidade do Azure que pertence a um perfil do Gerenciador de tráfego, incluindo todas as propriedades desse Ponto de Extremidade do Azure. |
> | Ação | Microsoft. Network/trafficManagerProfiles/azureEndpoints/Write | Adicione um novo Ponto de Extremidade do Azure em um perfil existente do Gerenciador de tráfego ou atualize as propriedades de um Ponto de Extremidade do Azure existente nesse perfil do Gerenciador de tráfego. |
> | Ação | Microsoft. Network/trafficManagerProfiles/Delete | Exclua o perfil do Gerenciador de tráfego. Todas as configurações associadas ao perfil do Gerenciador de tráfego serão perdidas e o perfil não poderá mais ser usado para rotear o tráfego. |
> | Ação | Microsoft. Network/trafficManagerProfiles/externalEndpoints/Delete | Exclui um ponto de extremidade externo de um perfil do Gerenciador de tráfego existente. O Gerenciador de tráfego deixará de rotear o tráfego para o ponto de extremidade externo excluído. |
> | Ação | Microsoft. Network/trafficManagerProfiles/externalEndpoints/Read | Obtém um ponto de extremidade externo que pertence a um perfil do Gerenciador de tráfego, incluindo todas as propriedades desse ponto de extremidade externo. |
> | Ação | Microsoft. Network/trafficManagerProfiles/externalEndpoints/Write | Adicione um novo ponto de extremidade externo em um perfil existente do Gerenciador de tráfego ou atualize as propriedades de um ponto de extremidade externo existente nesse perfil do Gerenciador de tráfego. |
> | Ação | Microsoft. Network/trafficManagerProfiles/calor/Read | Obtém o mapa de calor do Gerenciador de tráfego para o perfil do Gerenciador de tráfego fornecido que contém contagens de consulta e dados de latência por local e IP de origem. |
> | Ação | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/Delete | Exclui um ponto de extremidade aninhado de um perfil do Gerenciador de tráfego existente. O Gerenciador de tráfego irá parar o roteamento do tráfego para o ponto de extremidade aninhado excluído. |
> | Ação | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/Read | Obtém um ponto de extremidade aninhado que pertence a um perfil do Gerenciador de tráfego, incluindo todas as propriedades desse ponto de extremidade aninhado. |
> | Ação | Microsoft. Network/trafficManagerProfiles/nestedEndpoints/Write | Adicione um novo ponto de extremidade aninhado em um perfil existente do Gerenciador de tráfego ou atualize as propriedades de um ponto de extremidade aninhado existente no perfil do Gerenciador de tráfego. |
> | Ação | Microsoft. Network/trafficManagerProfiles/Read | Obtenha a configuração do perfil do Gerenciador de tráfego. Isso inclui configurações de DNS, configurações de roteamento de tráfego, configurações de monitoramento de ponto de extremidade e a lista de pontos de extremidade roteados por esse perfil do Gerenciador de tráfego. |
> | Ação | Microsoft. Network/trafficManagerProfiles/Write | Criar um perfil do Gerenciador de tráfego ou modificar a configuração de um perfil existente do Gerenciador de tráfego.<br>Isso inclui habilitar ou desabilitar um perfil e modificar configurações de DNS, configurações de roteamento de tráfego ou configurações de monitoramento de ponto de extremidade.<br>Os pontos de extremidade roteados pelo perfil do Gerenciador de tráfego podem ser adicionados, removidos, habilitados ou desabilitados. |
> | Ação | Microsoft. Network/trafficManagerUserMetricsKeys/Delete | Exclui a chave de nível de assinatura usada para a coleta de métricas de usuário em tempo real. |
> | Ação | Microsoft. Network/trafficManagerUserMetricsKeys/Read | Obtém a chave de nível de assinatura usada para a coleta de métricas de usuário em tempo real. |
> | Ação | Microsoft. Network/trafficManagerUserMetricsKeys/Write | Cria uma nova chave de nível de assinatura a ser usada para a coleta de métricas de usuário em tempo real. |
> | Ação | Microsoft. Network/cancelar registro/ação | Cancela o registro da assinatura |
> | Ação | Microsoft. Network/virtualHubs/Delete | Excluir um hub virtual |
> | Ação | Microsoft. Network/virtualHubs/hubVirtualNetworkConnections/Delete | Exclui um HubVirtualNetworkConnection |
> | Ação | Microsoft. Network/virtualHubs/hubVirtualNetworkConnections/Read | Obter um HubVirtualNetworkConnection |
> | Ação | Microsoft. Network/virtualHubs/hubVirtualNetworkConnections/Write | Criar ou atualizar um HubVirtualNetworkConnection |
> | Ação | Microsoft. Network/virtualHubs/Read | Obter um hub virtual |
> | Ação | Microsoft. Network/virtualHubs/routeTables/Delete | Excluir um VirtualHubRouteTableV2 |
> | Ação | Microsoft. Network/virtualHubs/routeTables/Read | Obter um VirtualHubRouteTableV2 |
> | Ação | Microsoft. Network/virtualHubs/routeTables/Write | Criar ou atualizar um VirtualHubRouteTableV2 |
> | Ação | Microsoft. Network/virtualHubs/Write | Criar ou atualizar um hub virtual |
> | Ação | Microsoft. Network/virtualnetworkgateways/Connections/Read | Obter VirtualNetworkGatewayConnection |
> | Ação | Microsoft. Network/virtualNetworkGateways/Delete | Exclui um virtualNetworkGateway |
> | Ação | Microsoft. Network/virtualnetworkgateways/generatevpnclientpackage/Action | Gerar pacote VpnClient para virtualNetworkGateway |
> | Ação | Microsoft. Network/virtualnetworkgateways/generatevpnprofile/Action | Gerar pacote VpnProfile para VirtualNetworkGateway |
> | Ação | Microsoft. Network/virtualnetworkgateways/getadvertisedroutes/Action | Obtém rotas anunciadas do virtualNetworkGateway |
> | Ação | Microsoft. Network/virtualnetworkgateways/getbgppeerstatus/Action | Obtém o status do par BGP virtualNetworkGateway |
> | Ação | Microsoft. Network/virtualnetworkgateways/getlearnedroutes/Action | Obtém rotas aprendidas do virtualnetworkgateway |
> | Ação | Microsoft. Network/virtualnetworkgateways/getvpnclientconnectionhealth/Action | Obter integridade de conexão de cliente VPN para VirtualNetworkGateway |
> | Ação | Microsoft. Network/virtualnetworkgateways/getvpnclientipsecparameters/Action | Obter parâmetros de IPSec vpnclient para o cliente VirtualNetworkGateway P2S. |
> | Ação | Microsoft. Network/virtualnetworkgateways/getvpnprofilepackageurl/Action | Obtém a URL de um pacote de perfil de cliente VPN gerado previamente |
> | Ação | Microsoft. Network/virtualNetworkGateways/Read | Obter um VirtualNetworkGateway |
> | Ação | Microsoft. Network/virtualnetworkgateways/redefinir/ação | Redefine um virtualNetworkGateway |
> | Ação | Microsoft. Network/virtualnetworkgateways/resetvpnclientsharedkey/Action | Redefina a chave compartilhada vpnclient para o cliente VirtualNetworkGateway P2S. |
> | Ação | Microsoft. Network/virtualnetworkgateways/setvpnclientipsecparameters/Action | Defina os parâmetros IPsec do vpnclient para o cliente VirtualNetworkGateway P2S. |
> | Ação | Microsoft. Network/virtualnetworkgateways/supportedvpndevices/Action | Lista dispositivos VPN com suporte |
> | Ação | Microsoft. Network/virtualNetworkGateways/Write | Criar ou atualizar um VirtualNetworkGateway |
> | Ação | Microsoft. Network/virtualNetworks/BastionHosts/Action | Obtém referências de host bastião em uma rede virtual. |
> | Ação | Microsoft. Network/virtualNetworks/bastionHosts/default/Action | Obtém referências de host bastião em uma rede virtual. |
> | Ação | Microsoft. Network/virtualNetworks/checkIpAddressAvailability/Read | Verificar se o endereço IP está disponível na rede virtual especificada |
> | Ação | Microsoft. Network/virtualNetworks/Delete | Excluir uma rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/junção/ação | Une uma rede virtual. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/virtualNetworks/ponto/ação | Os pares de uma rede virtual com outra rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/Read | Obter a definição de rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/contextualServiceEndpointPolicies/Delete | Exclui uma política de ponto de extremidade de serviço contextual |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/contextualServiceEndpointPolicies/Read | Obtém políticas de ponto de extremidade de serviço contextual |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/contextualServiceEndpointPolicies/Write | Cria uma política de ponto de extremidade de serviço contextual ou atualiza uma política de ponto de extremidade de serviço contextual existente |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/excluir | Excluir uma sub-rede de rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/junção/ação | Une uma rede virtual. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/joinViaServiceEndpoint/Action | Une recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/prepareNetworkPolicies/Action | Prepara uma sub-rede aplicando as políticas de rede necessárias |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/leitura | Obter uma definição de sub-rede de rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/unprepareNetworkPolicies/Action | Despreparar uma sub-rede removendo as políticas de rede aplicadas |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/virtualMachines/Read | Obtém referências a todas as máquinas virtuais em uma sub-rede de rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/sub-redes/gravação | Cria uma sub-rede de rede virtual ou atualiza uma sub-rede de rede virtual existente |
> | Ação | Microsoft. Network/virtualNetworks/Usages/Read | Obter os usos de IP para cada sub-rede da rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/virtualMachines/Read | Obtém referências a todas as máquinas virtuais em uma rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/virtualNetworkPeerings/Delete | Exclui um emparelhamento de rede virtual |
> | Ação | Microsoft. Network/virtualNetworks/virtualNetworkPeerings/Read | Obter uma definição de emparelhamento de rede virtual |
> | Ação | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Cria um emparelhamento de rede virtual ou atualiza um emparelhamento de rede virtual existente |
> | Ação | Microsoft. Network/virtualNetworks/Write | Cria uma rede virtual ou atualiza uma rede virtual existente |
> | Ação | Microsoft. Network/virtualNetworkTaps/Delete | Excluir toque de rede virtual |
> | Ação | Microsoft. Network/virtualNetworkTaps/junção/ação | Une um toque de rede virtual. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/virtualNetworkTaps/Read | Obter toque de rede virtual |
> | Ação | Microsoft. Network/virtualNetworkTaps/Write | Criar ou atualizar toque de rede virtual |
> | Ação | Microsoft. Network/virtualRouters/Delete | Exclui um VirtualRouter |
> | Ação | Microsoft. Network/virtualRouters/junção/ação | Une um VirtualRouter. Não é possível alertá-lo. |
> | Ação | Microsoft. Network/virtualRouters/Read | Obter um VirtualRouter |
> | Ação | Microsoft. Network/virtualRouters/virtualRouterPeerings/Delete | Exclui um VirtualRouterPeering |
> | Ação | Microsoft. Network/virtualRouters/virtualRouterPeerings/Read | Obter um VirtualRouterPeering |
> | Ação | Microsoft. Network/virtualRouters/virtualRouterPeerings/Write | Cria um VirtualRouterPeering ou atualiza um VirtualRouterPeering existente |
> | Ação | Microsoft. Network/virtualRouters/Write | Cria um VirtualRouter ou atualiza um VirtualRouter existente |
> | Ação | Microsoft. Network/virtualWans/Delete | Exclui uma WAN virtual |
> | Ação | Microsoft. Network/virtualwans/generateVpnProfile/Action | Gerar VirtualWanVpnServerConfiguration VpnProfile |
> | Ação | Microsoft. Network/virtualWans/Read | Obter uma WAN virtual |
> | Ação | Microsoft. Network/virtualwans/supportedSecurityProviders/Read | Obtém provedores de segurança VirtualWan com suporte. |
> | Ação | Microsoft. Network/virtualWans/virtualHubs/Read | Obtém todos os hubs virtuais que fazem referência a uma WAN virtual. |
> | Ação | Microsoft. Network/virtualwans/vpnconfiguration/Action | Obter uma configuração de VPN |
> | Ação | Microsoft. Network/virtualwans/vpnServerConfigurations/Action | Obter VirtualWanVpnServerConfigurations |
> | Ação | Microsoft. Network/virtualWans/vpnSites/Read | Obtém todos os sites VPN que fazem referência a uma WAN virtual. |
> | Ação | Microsoft. Network/virtualWans/Write | Criar ou atualizar uma WAN virtual |
> | Ação | Microsoft. Network/vpnGateways/Delete | Exclui um VpnGateway. |
> | Ação | Microsoft. Network/vpngateways/listvpnconnectionshealth/Action | Obtém a integridade da conexão para todos ou um subconjunto de conexões em um VpnGateway |
> | Ação | Microsoft. Network/vpnGateways/Read | Obtém um VpnGateway. |
> | Ação | Microsoft. Network/vpngateways/redefinir/ação | Redefine um VpnGateway |
> | Ação | Microsoft. Network/vpnGateways/vpnConnections/Delete | Exclui um VpnConnection. |
> | Ação | Microsoft. Network/vpnGateways/vpnConnections/Read | Obtém um VpnConnection. |
> | Ação | Microsoft. Network/vpnGateways/vpnConnections/vpnLinkConnections/Read | Obter uma conexão de link VPN |
> | Ação | Microsoft. Network/vpnGateways/vpnConnections/Write | Coloca um VpnConnection. |
> | Ação | Microsoft. Network/vpnGateways/Write | Coloca um VpnGateway. |
> | Ação | Microsoft. Network/vpnServerConfigurations/Delete | Excluir VpnServerConfiguration |
> | Ação | Microsoft. Network/vpnServerConfigurations/Read | Obter VpnServerConfiguration |
> | Ação | Microsoft. Network/vpnServerConfigurations/Write | Criar ou atualizar VpnServerConfiguration |
> | Ação | Microsoft. Network/vpnsites/Delete | Exclui um recurso de site VPN. |
> | Ação | Microsoft. Network/vpnsites/Read | Obtém um recurso de site VPN. |
> | Ação | Microsoft. Network/vpnSites/vpnSiteLinks/Read | Obter um link de site VPN |
> | Ação | Microsoft. Network/vpnsites/Write | Cria ou atualiza um recurso de site de VPN. |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. NotificationHubs/CheckNamespaceAvailability/Action | Verifica se um determinado nome de recurso de namespace está disponível no serviço NotificationHub. |
> | Ação | Microsoft. NotificationHubs/namespaces/authorizationRules/ação | Obtenha a lista de descrições de regras de autorização de namespaces. |
> | Ação | Microsoft. NotificationHubs/namespaces/authorizationRules/Delete | Excluir regra de autorização de namespace. A regra de autorização de namespace padrão não pode ser excluída.  |
> | Ação | Microsoft. NotificationHubs/namespaces/authorizationRules/listkeys/ação | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft. NotificationHubs/namespaces/authorizationRules/Read | Obtenha a lista de descrições de regras de autorização de namespaces. |
> | Ação | Microsoft. NotificationHubs/namespaces/authorizationRules/regenerateKeys/ação | Regra de autorização de namespace regenerar Primary/SecondaryKey, especifique a chave que precisa ser regenerada |
> | Ação | Microsoft. NotificationHubs/namespaces/authorizationRules/Write | Crie regras de autorização de nível de namespace e atualize suas propriedades. Os direitos de acesso às regras de autorização, as chaves primária e secundária podem ser atualizadas. |
> | Ação | Microsoft. NotificationHubs/namespaces/CheckNotificationHubAvailability/ação | Verifica se um determinado nome de NotificationHub está disponível dentro de um namespace. |
> | Ação | Microsoft. NotificationHubs/namespaces/excluir | Excluir recurso de namespace |
> | Ação | Microsoft. NotificationHubs/namespaces/NotificationHubs/authorizationRules/ação | Obter a lista de regras de autorização do hub de notificação |
> | Ação | Microsoft. NotificationHubs/namespaces/NotificationHubs/authorizationRules/Delete | Excluir regras de autorização do hub de notificação |
> | Ação | Microsoft. NotificationHubs/namespaces/NotificationHubs/authorizationRules/listkeys/ação | Obter a cadeia de conexão para o Hub de notificação |
> | Ação | Microsoft. NotificationHubs/namespaces/NotificationHubs/authorizationRules/leitura | Obter a lista de regras de autorização do hub de notificação |
> | Ação | Microsoft. NotificationHubs/namespaces/NotificationHubs/authorizationRules/regenerateKeys/ação | Regra de autorização de Hub de notificação regenerar Primary/SecondaryKey, especifique a chave que precisa ser regenerada |
> | Ação | Microsoft. NotificationHubs/namespaces/NotificationHubs/authorizationRules/Write | Crie regras de autorização do hub de notificação e atualize suas propriedades. Os direitos de acesso às regras de autorização, as chaves primária e secundária podem ser atualizadas. |
> | Ação | Microsoft. NotificationHubs/namespaces/NotificationHubs/debugSend/ação | Enviar uma notificação por push de teste. |
> | Ação | Microsoft. NotificationHubs/namespaces/NotificationHubs/Delete | Excluir recurso do hub de notificação |
> | Ação | Microsoft. NotificationHubs/namespaces/NotificationHubs/metricDefinitions/leitura | Obter lista de descrições de recursos de métricas de namespace |
> | Ação | Microsoft. NotificationHubs/namespaces/NotificationHubs/pnsCredentials/ação | Obter todas as credenciais de PNS do hub de notificação. Isso inclui as credenciais WNS, MPNS, APNS, GCM e Baidu |
> | Ação | Microsoft. NotificationHubs/namespaces/NotificationHubs/Read | Obter lista de descrições de recursos do hub de notificação |
> | Ação | Microsoft. NotificationHubs/namespaces/NotificationHubs/Write | Crie um hub de notificação e atualize suas propriedades. Suas propriedades incluem principalmente as credenciais PNS. Regras de autorização e TTL |
> | Ação | Microsoft. NotificationHubs/namespaces/leitura | Obter a lista de descrição de recurso de namespace |
> | Ação | Microsoft. NotificationHubs/namespaces/gravação | Crie um recurso de namespace e atualize suas propriedades. As marcas e a capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft. NotificationHubs/operationResults/Read | Retorna os resultados da operação para o provedor de hubs de notificação |
> | Ação | Microsoft. NotificationHubs/operações/leitura | Retorna uma lista de operações com suporte para o provedor de hubs de notificação |
> | Ação | Microsoft. NotificationHubs/registro/ação | Registra a assinatura para o provedor de recursos NotificationHubs e habilita a criação de namespaces e NotificationHubs |
> | Ação | Microsoft. NotificationHubs/cancelar registro/ação | Cancela o registro da assinatura para o provedor de recursos NotificationHubs e habilita a criação de namespaces e NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. OffAzure/HyperVSites/clusters/leitura | Obter as propriedades de um cluster Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/clusters/gravação | Criar ou atualizar o cluster do Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/Delete | Exclui o site do Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/healthsummary/Read | Obter o resumo de integridade do recurso Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/hosts/leitura | Obter as propriedades de um host Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/hosts/gravação | Criar ou atualizar o host Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/Jobs/ler | Obter as propriedades de um trabalho do Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/Machines/Read | Obter as propriedades de uma máquina Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/operationsstatus/Read | Obtém as propriedades de um status de operação do Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/Read | Obter as propriedades de um site do Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/atualização/ação | Atualiza os objetos em um site do Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/runasaccounts/Read | Obtém as propriedades de uma conta Executar como do Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/uso/leitura | Obtém os usos de um site do Hyper-V |
> | Ação | Microsoft. OffAzure/HyperVSites/Write | Criar ou atualizar o site do Hyper-V |
> | Ação | Microsoft. OffAzure/operações/leitura | Lê as operações expostas |
> | Ação | Microsoft. OffAzure/registro/ação | Registra a assinatura com o provedor de recursos Microsoft. OffAzure |
> | Ação | Microsoft. OffAzure/ServerSites/Jobs/ler | Obtém as propriedades de um servidor de trabalhos |
> | Ação | Microsoft. OffAzure/ServerSites/Machines/Read | Obter as propriedades de uma máquina de servidor |
> | Ação | Microsoft. OffAzure/ServerSites/Machines/Write | Gravar as propriedades de uma máquina de servidor |
> | Ação | Microsoft. OffAzure/ServerSites/operationsstatus/Read | Obtém as propriedades de um status de operação do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/Read | Obtém as propriedades de um site do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/atualização/ação | Atualiza os objetos em um site do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/runasaccounts/Read | Obtém as propriedades de uma conta Executar como do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/uso/leitura | Obtém os usos de um site do servidor |
> | Ação | Microsoft. OffAzure/ServerSites/Write | Criar ou atualizar o site do servidor |
> | Ação | Microsoft. OffAzure/VMwareSites/Delete | Exclui o site do VMware |
> | Ação | Microsoft. OffAzure/VMwareSites/healthsummary/Read | Obter o resumo de integridade do recurso do VMware |
> | Ação | Microsoft. OffAzure/VMwareSites/Jobs/ler | Obter as propriedades de um trabalho do VMware |
> | Ação | Microsoft. OffAzure/VMwareSites/Machines/Read | Obter as propriedades de uma máquina VMware |
> | Ação | Microsoft. OffAzure/VMwareSites/computadores/iniciar/ação | Iniciar máquinas VMware |
> | Ação | Microsoft. OffAzure/VMwareSites/Machines/Stop/Action | Para as máquinas VMware |
> | Ação | Microsoft. OffAzure/VMwareSites/operationsstatus/Read | Obtém as propriedades de um status de operação do VMware |
> | Ação | Microsoft. OffAzure/VMwareSites/Read | Obter as propriedades de um site VMware |
> | Ação | Microsoft. OffAzure/VMwareSites/atualização/ação | Atualiza os objetos em um site VMware |
> | Ação | Microsoft. OffAzure/VMwareSites/runasaccounts/Read | Obter as propriedades de uma conta Executar como do VMware |
> | Ação | Microsoft. OffAzure/VMwareSites/uso/leitura | Obtém os usos de um site VMware |
> | Ação | Microsoft. OffAzure/VMwareSites/vcenters/Read | Obter as propriedades de um VMware vCenter |
> | Ação | Microsoft. OffAzure/VMwareSites/vcenters/Write | Criar ou atualizar o VMware vCenter |
> | Ação | Microsoft. OffAzure/VMwareSites/Write | Cria ou atualiza o site do VMware |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. OperationalInsights/linkTargets/Read | Lista as contas existentes que não estão associadas a uma assinatura do Azure. Para vincular essa assinatura do Azure a um espaço de trabalho, use uma ID de cliente retornada por essa operação na propriedade ID do cliente da operação criar espaço de trabalho. |
> | Ação | Microsoft. operationalinsights/operações/leitura | Lista todas as operações de API REST do OperationalInsights disponíveis. |
> | Ação | Microsoft. operationalinsights/registro/ação | Rergisters a assinatura. |
> | Ação | Microsoft. OperationalInsights/registro/ação | Registrar uma assinatura em um provedor de recursos. |
> | Ação | Microsoft. operationalinsights/cancelar registro/ação | Cancela o registro da assinatura. |
> | Ação | Microsoft. OperationalInsights/Workspaces/Analytics/consulta/ação | Pesquisar usando o novo mecanismo. |
> | Ação | Microsoft. OperationalInsights/Workspaces/Analytics/consulta/esquema/leitura | Obter o esquema de pesquisa v2. |
> | Ação | Microsoft. OperationalInsights/Workspaces/API/consulta/ação | Pesquisar usando o novo mecanismo. |
> | Ação | Microsoft. OperationalInsights/Workspaces/API/consulta/esquema/leitura | Obter o esquema de pesquisa v2. |
> | Ação | Microsoft. OperationalInsights/espaços de trabalho/configurationScopes/Delete | Excluir escopo de configuração |
> | Ação | Microsoft. OperationalInsights/Workspaces/configurationScopes/Read | Obter escopo de configuração |
> | Ação | Microsoft. OperationalInsights/Workspaces/configurationScopes/Write | Definir escopo de configuração |
> | Ação | Microsoft. OperationalInsights/Workspaces/fontes de trabalho/exclusão | Exclua fontes de fonte de um espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/fontes de trabalho/leitura | Obter fontes de origem em um espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/fontes de trabalho/gravação | Criar/atualizar fontes de origem em um espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/espaços de trabalho/excluir | Exclui um espaço de trabalho. Se o espaço de trabalho tiver sido vinculado a um espaço de trabalho existente no momento da criação, o espaço de trabalho ao qual ele estava vinculado não será excluído. |
> | Ação | Microsoft. OperationalInsights/espaços de trabalho/gateways/excluir | Remove um gateway configurado para o espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/generateregistrationcertificate/Action | Gera o certificado de registro para o espaço de trabalho. Esse certificado é usado para conectar o Gerenciador de operações do Microsoft System Center ao espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/espaços de trabalho/intelligencepacks/desabilitar/ação | Desabilita um pacote de inteligência para um determinado espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/intelligencepacks/habilitar/ação | Habilita um pacote de inteligência para um determinado espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/intelligencepacks/Read | Lista todos os pacotes de inteligência visíveis para um determinado espaço de trabalho e também lista se o pacote está habilitado ou desabilitado para esse espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/linkservices/excluir | Exclua os serviços vinculados em determinado espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/aslinkservices/Read | Obter serviços vinculados em determinado espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/aslinkservices/Write | Criar/atualizar serviços vinculados em determinado espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/listKeys/Action | Recupera as chaves de lista para o espaço de trabalho. Essas chaves são usadas para conectar agentes do insights operacionais da Microsoft ao espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/listKeys/Read | Recupera as chaves de lista para o espaço de trabalho. Essas chaves são usadas para conectar agentes do insights operacionais da Microsoft ao espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/managementGroups/Read | Obtém os nomes e os metadados para System Center Operations Manager grupos de gerenciamento conectados a este espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/metricDefinitions/Read | Obter definições de métrica no espaço de trabalho |
> | Ação | Microsoft. OperationalInsights/espaços de trabalho/notificationSettings/Delete | Exclua as configurações de notificação do usuário para o espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/notificationSettings/Read | Obtenha as configurações de notificação do usuário para o espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/notificationSettings/Write | Defina as configurações de notificação do usuário para o espaço de trabalho. |
> | Ação | Microsoft. operationalinsights/Workspaces/Operations/Read | Obtém o status de uma operação de espaço de trabalho OperationalInsights. |
> | Ação | Microsoft. OperationalInsights/espaços de trabalho/limpeza/ação | Excluir dados especificados do espaço de trabalho |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesAccountLogon/Read | Ler dados da tabela AADDomainServicesAccountLogon |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesAccountManagement/Read | Ler dados da tabela AADDomainServicesAccountManagement |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesDirectoryServiceAccess/Read | Ler dados da tabela AADDomainServicesDirectoryServiceAccess |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesLogonLogoff/Read | Ler dados da tabela AADDomainServicesLogonLogoff |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesPolicyChange/Read | Ler dados da tabela AADDomainServicesPolicyChange |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesPrivilegeUse/Read | Ler dados da tabela AADDomainServicesPrivilegeUse |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AADDomainServicesSystemSecurity/Read | Ler dados da tabela AADDomainServicesSystemSecurity |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ADAssessmentRecommendation/Read | Ler dados da tabela ADAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupAlerts/Read | Ler dados da tabela AddonAzureBackupAlerts |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupJobs/Read | Ler dados da tabela AddonAzureBackupJobs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupPolicy/Read | Ler dados da tabela AddonAzureBackupPolicy |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupProtectedInstance/Read | Ler dados da tabela AddonAzureBackupProtectedInstance |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AddonAzureBackupStorage/Read | Ler dados da tabela AddonAzureBackupStorage |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ADFActivityRun/Read | Ler dados da tabela ADFActivityRun |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ADFPipelineRun/Read | Ler dados da tabela ADFPipelineRun |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ADFTriggerRun/Read | Ler dados da tabela ADFTriggerRun |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ADReplicationResult/Read | Ler dados da tabela ADReplicationResult |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ADSecurityAssessmentRecommendation/Read | Ler dados da tabela ADSecurityAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/consulta/alerta/leitura | Ler dados da tabela de alertas |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AlertHistory/Read | Ler dados da tabela AlertHistory |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeClusterEvent/Read | Ler dados da tabela AmlComputeClusterEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeClusterNodeEvent/Read | Ler dados da tabela AmlComputeClusterNodeEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AmlComputeJobEvent/Read | Ler dados da tabela AmlComputeJobEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ApiManagementGatewayLogs/Read | Ler dados da tabela ApiManagementGatewayLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AppCenterError/Read | Ler dados da tabela AppCenterError |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ApplicationInsights/Read | Ler dados da tabela ApplicationInsights |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AppPlatformLogsforSpring/Read | Ler dados da tabela AppPlatformLogsforSpring |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AppServiceEnvironmentPlatformLogs/Read | Ler dados da tabela AppServiceEnvironmentPlatformLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AuditLogs/Read | Ler dados da tabela AuditLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AutoscaleEvaluationsLog/Read | Ler dados da tabela AutoscaleEvaluationsLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AutoscaleScaleActionsLog/Read | Ler dados da tabela AutoscaleScaleActionsLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AWSCloudTrail/Read | Ler dados da tabela AWSCloudTrail |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AzureActivity/Read | Ler dados da tabela AzureActivity |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AzureAssessmentRecommendation/Read | Ler dados da tabela AzureAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/AzureMetrics/Read | Ler dados da tabela AzureMetrics |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/BaiClusterEvent/Read | Ler dados da tabela BaiClusterEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/BaiClusterNodeEvent/Read | Ler dados da tabela BaiClusterNodeEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/BaiJobEvent/Read | Ler dados da tabela BaiJobEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/BlockchainApplicationLog/Read | Ler dados da tabela BlockchainApplicationLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/BlockchainProxyLog/Read | Ler dados da tabela BlockchainProxyLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/BoundPort/Read | Ler dados da tabela BoundPort |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/CommonSecurityLog/Read | Ler dados da tabela CommonSecurityLog |
> | Ação | Microsoft. OperationalInsights/espaços de trabalho/consulta/computador/ler | Ler dados da tabela do computador |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ConfigurationChange/Read | Ler dados da tabela ConfigurationChange |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ConfigurationData/Read | Ler dados da tabela ConfigurationData |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ContainerImageInventory/Read | Ler dados da tabela ContainerImageInventory |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ContainerInventory/Read | Ler dados da tabela ContainerInventory |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ContainerLog/Read | Ler dados da tabela ContainerLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ContainerNodeInventory/Read | Ler dados da tabela ContainerNodeInventory |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ContainerRegistryLoginEvents/Read | Ler dados da tabela ContainerRegistryLoginEvents |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ContainerRegistryRepositoryEvents/Read | Ler dados da tabela ContainerRegistryRepositoryEvents |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ContainerServiceLog/Read | Ler dados da tabela ContainerServiceLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/CoreAzureBackup/Read | Ler dados da tabela CoreAzureBackup |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DatabricksAccounts/Read | Ler dados da tabela DatabricksAccounts |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DatabricksClusters/Read | Ler dados da tabela DatabricksClusters |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DatabricksDBFS/Read | Ler dados da tabela DatabricksDBFS |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DatabricksInstancePools/Read | Ler dados da tabela DatabricksInstancePools |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DatabricksJobs/Read | Ler dados da tabela DatabricksJobs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DatabricksNotebook/Read | Ler dados da tabela DatabricksNotebook |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DatabricksSecrets/Read | Ler dados da tabela DatabricksSecrets |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DatabricksSQLPermissions/Read | Ler dados da tabela DatabricksSQLPermissions |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DatabricksSSH/Read | Ler dados da tabela DatabricksSSH |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DatabricksTables/Read | Ler dados da tabela DatabricksTables |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DatabricksWorkspace/Read | Ler dados da tabela DatabricksWorkspace |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DeviceAppCrash/Read | Ler dados da tabela DeviceAppCrash |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DeviceAppLaunch/Read | Ler dados da tabela DeviceAppLaunch |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DeviceCalendar/Read | Ler dados da tabela DeviceCalendar |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DeviceCleanup/Read | Ler dados da tabela DeviceCleanup |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DeviceConnectSession/Read | Ler dados da tabela DeviceConnectSession |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DeviceEtw/Read | Ler dados da tabela DeviceEtw |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DeviceHardwareHealth/Read | Ler dados da tabela DeviceHardwareHealth |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DeviceHealth/Read | Ler dados da tabela DeviceHealth |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DeviceHeartbeat/Read | Ler dados da tabela DeviceHeartbeat |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DeviceSkypeHeartbeat/Read | Ler dados da tabela DeviceSkypeHeartbeat |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DeviceSkypeSignIn/Read | Ler dados da tabela DeviceSkypeSignIn |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DeviceSleepState/Read | Ler dados da tabela DeviceSleepState |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DHAppFailure/Read | Ler dados da tabela DHAppFailure |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DHAppReliability/Read | Ler dados da tabela DHAppReliability |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DHDriverReliability/Read | Ler dados da tabela DHDriverReliability |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DHLogonFailures/Read | Ler dados da tabela DHLogonFailures |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DHLogonMetrics/Read | Ler dados da tabela DHLogonMetrics |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DHOSCrashData/Read | Ler dados da tabela DHOSCrashData |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DHOSReliability/Read | Ler dados da tabela DHOSReliability |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DHWipAppLearning/Read | Ler dados da tabela DHWipAppLearning |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DnsEvents/Read | Ler dados da tabela DnsEvents |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/DnsInventory/Read | Ler dados da tabela DnsInventory |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ETWEvent/Read | Ler dados da tabela ETWEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/consulta/evento/leitura | Ler dados da tabela de eventos |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ExchangeAssessmentRecommendation/Read | Ler dados da tabela ExchangeAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ExchangeOnlineAssessmentRecommendation/Read | Ler dados da tabela ExchangeOnlineAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/FailedIngestion/Read | Ler dados da tabela FailedIngestion |
> | Ação | Microsoft. OperationalInsights/espaços de trabalho/consulta/pulsação/leitura | Ler dados da tabela de pulsação |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/HuntingBookmark/Read | Ler dados da tabela HuntingBookmark |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/IISAssessmentRecommendation/Read | Ler dados da tabela IISAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/InboundConnection/Read | Ler dados da tabela InboundConnection |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/InsightsMetrics/Read | Ler dados da tabela InsightsMetrics |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/IntuneAuditLogs/Read | Ler dados da tabela IntuneAuditLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/IntuneOperationalLogs/Read | Ler dados da tabela IntuneOperationalLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/KubeEvents/Read | Ler dados da tabela KubeEvents |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/KubeHealth/Read | Ler dados da tabela KubeHealth |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/KubeNodeInventory/Read | Ler dados da tabela KubeNodeInventory |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/KubePodInventory/Read | Ler dados da tabela KubePodInventory |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/KubeServices/Read | Ler dados da tabela KubeServices |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/LinuxAuditLog/Read | Ler dados da tabela LinuxAuditLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAApplication/Read | Ler dados da tabela MAApplication |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealth/Read | Ler dados da tabela MAApplicationHealth |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealthAlternativeVersions/Read | Ler dados da tabela MAApplicationHealthAlternativeVersions |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationHealthIssues/Read | Ler dados da tabela MAApplicationHealthIssues |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationInstance/Read | Ler dados da tabela MAApplicationInstance |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationInstanceReadiness/Read | Ler dados da tabela MAApplicationInstanceReadiness |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAApplicationReadiness/Read | Ler dados da tabela MAApplicationReadiness |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MADeploymentPlan/Read | Ler dados da tabela MADeploymentPlan |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MADevice/Read | Ler dados da tabela MADevice |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MADeviceNotEnrolled/Read | Ler dados da tabela MADeviceNotEnrolled |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MADeviceNRT/Read | Ler dados da tabela MADeviceNRT |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MADevicePnPHealth/Read | Ler dados da tabela MADevicePnPHealth |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MADevicePnPHealthAlternativeVersions/Read | Ler dados da tabela MADevicePnPHealthAlternativeVersions |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MADevicePnPHealthIssues/Read | Ler dados da tabela MADevicePnPHealthIssues |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MADeviceReadiness/Read | Ler dados da tabela MADeviceReadiness |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MADriverInstanceReadiness/Read | Ler dados da tabela MADriverInstanceReadiness |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MADriverReadiness/Read | Ler dados da tabela MADriverReadiness |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddin/Read | Ler dados da tabela MAOfficeAddin |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinEntityHealth/Read | Ler dados da tabela MAOfficeAddinEntityHealth |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinHealth/Read | Ler dados da tabela MAOfficeAddinHealth |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinHealthEventNRT/Read | Ler dados da tabela MAOfficeAddinHealthEventNRT |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinHealthIssues/Read | Ler dados da tabela MAOfficeAddinHealthIssues |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinInstance/Read | Ler dados da tabela MAOfficeAddinInstance |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinInstanceReadiness/Read | Ler dados da tabela MAOfficeAddinInstanceReadiness |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAddinReadiness/Read | Ler dados da tabela MAOfficeAddinReadiness |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeApp/Read | Ler dados da tabela MAOfficeApp |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppCrashesNRT/Read | Ler dados da tabela MAOfficeAppCrashesNRT |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppHealth/Read | Ler dados da tabela MAOfficeAppHealth |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppInstance/Read | Ler dados da tabela MAOfficeAppInstance |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppInstanceHealth/Read | Ler dados da tabela MAOfficeAppInstanceHealth |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppReadiness/Read | Ler dados da tabela MAOfficeAppReadiness |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeAppSessionsNRT/Read | Ler dados da tabela MAOfficeAppSessionsNRT |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeBuildInfo/Read | Ler dados da tabela MAOfficeBuildInfo |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeCurrencyAssessment/Read | Ler dados da tabela MAOfficeCurrencyAssessment |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeCurrencyAssessmentDailyCounts/Read | Ler dados da tabela MAOfficeCurrencyAssessmentDailyCounts |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeDeploymentStatus/Read | Ler dados da tabela MAOfficeDeploymentStatus |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeDeploymentStatusNRT/Read | Ler dados da tabela MAOfficeDeploymentStatusNRT |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroErrorNRT/Read | Ler dados da tabela MAOfficeMacroErrorNRT |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroGlobalHealth/Read | Ler dados da tabela MAOfficeMacroGlobalHealth |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroHealth/Read | Ler dados da tabela MAOfficeMacroHealth |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroHealthIssues/Read | Ler dados da tabela MAOfficeMacroHealthIssues |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroIssueInstanceReadiness/Read | Ler dados da tabela MAOfficeMacroIssueInstanceReadiness |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroIssueReadiness/Read | Ler dados da tabela MAOfficeMacroIssueReadiness |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeMacroSummary/Read | Ler dados da tabela MAOfficeMacroSummary |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeSuite/Read | Ler dados da tabela MAOfficeSuite |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAOfficeSuiteInstance/Read | Ler dados da tabela MAOfficeSuiteInstance |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAProposedPilotDevices/Read | Ler dados da tabela MAProposedPilotDevices |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsBuildInfo/Read | Ler dados da tabela MAWindowsBuildInfo |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsCurrencyAssessment/Read | Ler dados da tabela MAWindowsCurrencyAssessment |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsCurrencyAssessmentDailyCounts/Read | Ler dados da tabela MAWindowsCurrencyAssessmentDailyCounts |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsDeploymentStatus/Read | Ler dados da tabela MAWindowsDeploymentStatus |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsDeploymentStatusNRT/Read | Ler dados da tabela MAWindowsDeploymentStatusNRT |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MAWindowsSysReqInstanceReadiness/Read | Ler dados da tabela MAWindowsSysReqInstanceReadiness |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/McasShadowItReporting/Read | Ler dados da tabela McasShadowItReporting |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftDataShareShareLog/Read | Ler dados da tabela MicrosoftDataShareShareLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftHealthcareApisAuditLogs/Read | Ler dados da tabela MicrosoftHealthcareApisAuditLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftInsightsAzureActivityLog/Read | Ler dados da tabela MicrosoftInsightsAzureActivityLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebApplicationLog/Read | Ler dados da tabela MicrosoftWebApplicationLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebFunctionExecutionLogs/Read | Ler dados da tabela MicrosoftWebFunctionExecutionLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebStdOutStdErrLog/Read | Ler dados da tabela MicrosoftWebStdOutStdErrLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/MicrosoftWebW3CLog/Read | Ler dados da tabela MicrosoftWebW3CLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/NetworkMonitoring/Read | Ler dados da tabela NetworkMonitoring |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/OfficeActivity/Read | Ler dados da tabela OfficeActivity |
> | Ação | Microsoft. OperationalInsights/Workspaces/consulta/operação/leitura | Ler dados da tabela de operações |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/OutboundConnection/Read | Ler dados da tabela OutboundConnection |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/perf/Read | Ler dados da tabela perf |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ProtectionStatus/Read | Ler dados da tabela ProtectionStatus |
> | Ação | Microsoft. OperationalInsights/Workspaces/consulta/leitura | Executar consultas sobre os dados no espaço de trabalho |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ReservedAzureCommonFields/Read | Ler dados da tabela ReservedAzureCommonFields |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ReservedCommonFields/Read | Ler dados da tabela ReservedCommonFields |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SCCMAssessmentRecommendation/Read | Ler dados da tabela SCCMAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SCOMAssessmentRecommendation/Read | Ler dados da tabela SCOMAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SecurityAlert/Read | Ler dados da tabela SecurityAlert |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SecurityBaseline/Read | Ler dados da tabela SecurityBaseline |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SecurityBaselineSummary/Read | Ler dados da tabela SecurityBaselineSummary |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SecurityDetection/Read | Ler dados da tabela SecurityDetection |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SecurityEvent/Read | Ler dados da tabela SecurityEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SecurityIoTRawEvent/Read | Ler dados da tabela SecurityIoTRawEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SecurityRecommendation/Read | Ler dados da tabela SecurityRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ServiceFabricOperationalEvent/Read | Ler dados da tabela ServiceFabricOperationalEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ServiceFabricReliableActorEvent/Read | Ler dados da tabela ServiceFabricReliableActorEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ServiceFabricReliableServiceEvent/Read | Ler dados da tabela ServiceFabricReliableServiceEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SfBAssessmentRecommendation/Read | Ler dados da tabela SfBAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SfBOnlineAssessmentRecommendation/Read | Ler dados da tabela SfBOnlineAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SharePointOnlineAssessmentRecommendation/Read | Ler dados da tabela SharePointOnlineAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SigninLogs/Read | Ler dados da tabela SigninLogs |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SPAssessmentRecommendation/Read | Ler dados da tabela SPAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SQLAssessmentRecommendation/Read | Ler dados da tabela SQLAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SQLQueryPerformance/Read | Ler dados da tabela SQLQueryPerformance |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SqlThreatProtectionLoginAudits/Read | Ler dados da tabela SqlThreatProtectionLoginAudits |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SqlVulnerabilityAssessmentResult/Read | Ler dados da tabela SqlVulnerabilityAssessmentResult |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SucceededIngestion/Read | Ler dados da tabela SucceededIngestion |
> | Ação | Microsoft. OperationalInsights/Workspaces/consulta/syslog/leitura | Ler dados da tabela syslog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/SysmonEvent/Read | Ler dados da tabela SysmonEvent |
> | Ação | Microsoft. OperationalInsights/espaços de trabalho/consulta/tabelas. personalizado/lido | Lendo dados de qualquer log personalizado |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/ThreatIntelligenceIndicator/Read | Ler dados da tabela ThreatIntelligenceIndicator |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/UAApp/Read | Ler dados da tabela UAApp |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/UAComputer/Read | Ler dados da tabela UAComputer |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/UAComputerRank/Read | Ler dados da tabela UAComputerRank |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/UADriver/Read | Ler dados da tabela UADriver |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/UADriverProblemCodes/Read | Ler dados da tabela UADriverProblemCodes |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/UAFeedback/Read | Ler dados da tabela UAFeedback |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/UAHardwareSecurity/Read | Ler dados da tabela UAHardwareSecurity |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/UAIESiteDiscovery/Read | Ler dados da tabela UAIESiteDiscovery |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/UAOfficeAddIn/Read | Ler dados da tabela UAOfficeAddIn |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/UAProposedActionPlan/Read | Ler dados da tabela UAProposedActionPlan |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/UASysReqIssue/Read | Ler dados da tabela UASysReqIssue |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/UAUpgradedComputer/Read | Ler dados da tabela UAUpgradedComputer |
> | Ação | Microsoft. OperationalInsights/Workspaces/consulta/atualização/leitura | Ler dados da tabela de atualização |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/UpdateRunProgress/Read | Ler dados da tabela UpdateRunProgress |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/UpdateSummary/Read | Ler dados da tabela UpdateSummary |
> | Ação | Microsoft. OperationalInsights/espaços de trabalho/consulta/uso/leitura | Ler dados da tabela de uso |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/VMBoundPort/Read | Ler dados da tabela VMBoundPort |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/VMConnection/Read | Ler dados da tabela VMConnection |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/W3CIISLog/Read | Ler dados da tabela W3CIISLog |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/WaaSDeploymentStatus/Read | Ler dados da tabela WaaSDeploymentStatus |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/WaaSInsiderStatus/Read | Ler dados da tabela WaaSInsiderStatus |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/WaaSUpdateStatus/Read | Ler dados da tabela WaaSUpdateStatus |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/WDAVStatus/Read | Ler dados da tabela WDAVStatus |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/WDAVThreat/Read | Ler dados da tabela WDAVThreat |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/WindowsClientAssessmentRecommendation/Read | Ler dados da tabela WindowsClientAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/WindowsEvent/Read | Ler dados da tabela WindowsEvent |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/WindowsFirewall/Read | Ler dados da tabela WindowsFirewall |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/WindowsServerAssessmentRecommendation/Read | Ler dados da tabela WindowsServerAssessmentRecommendation |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/WireData/Read | Ler dados da tabela WireData |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/WorkloadMonitoringPerf/Read | Ler dados da tabela WorkloadMonitoringPerf |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/WUDOAggregatedStatus/Read | Ler dados da tabela WUDOAggregatedStatus |
> | Ação | Microsoft. OperationalInsights/Workspaces/Query/WUDOStatus/Read | Ler dados da tabela WUDOStatus |
> | Ação | Microsoft. OperationalInsights/Workspaces/Read | Obter um espaço de trabalho existente |
> | Ação | Microsoft. OperationalInsights/Workspaces/regeneratesharedkey/Action | Regenera a chave compartilhada do espaço de trabalho especificada |
> | Ação | Microsoft. operationalinsights/Workspaces/Rules/Read | Obter todas as regras de alerta. |
> | Ação | Microsoft. OperationalInsights/espaços de trabalho/savedSearches/Delete | Exclui uma consulta de pesquisa salva |
> | Ação | Microsoft. OperationalInsights/Workspaces/savedSearches/Read | Obtém uma consulta de pesquisa salva |
> | Ação | Microsoft. operationalinsights/Workspaces/savedsearches/Results/Read | Obter resultados de pesquisas salvas. Preterido |
> | Ação | Microsoft. operationalinsights/Workspaces/savedsearches/agenda/ações/excluir | Exclua as ações de pesquisa agendadas. |
> | Ação | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Actions/Read | Obter ações de pesquisa agendada. |
> | Ação | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Actions/Write | Criar ou atualizar ações de pesquisa agendadas. |
> | Ação | Microsoft. operationalinsights/Workspaces/savedsearches/Schedule/Delete | Excluir pesquisas agendadas. |
> | Ação | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Read | Obter pesquisas agendadas. |
> | Ação | Microsoft. operationalinsights/Workspaces/savedsearches/Schedules/Write | Criar ou atualizar pesquisas agendadas. |
> | Ação | Microsoft. OperationalInsights/Workspaces/savedSearches/Write | Cria uma consulta de pesquisa salva |
> | Ação | Microsoft. OperationalInsights/Workspaces/esquema/leitura | Obtém o esquema de pesquisa para o espaço de trabalho.  O esquema de pesquisa inclui os campos expostos e seus tipos. |
> | Ação | Microsoft. OperationalInsights/Workspaces/pesquisa/ação | Executa uma consulta de pesquisa |
> | Ação | Microsoft. operationalinsights/Workspaces/Pesquisar/ler | Obter resultados da pesquisa. Preterido. |
> | Ação | Microsoft. OperationalInsights/Workspaces/sharedKeys/Action | Recupera as chaves compartilhadas para o espaço de trabalho. Essas chaves são usadas para conectar agentes do insights operacionais da Microsoft ao espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/sharedKeys/Read | Recupera as chaves compartilhadas para o espaço de trabalho. Essas chaves são usadas para conectar agentes do insights operacionais da Microsoft ao espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/espaços de trabalho/storageinsightconfigs/Delete | Exclui uma configuração de armazenamento. Isso interromperá a leitura dos dados da conta de armazenamento pelos insights operacionais da Microsoft. |
> | Ação | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/Read | Obtém uma configuração de armazenamento. |
> | Ação | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/Write | Cria uma nova configuração de armazenamento. Essas configurações são usadas para efetuar pull de dados de um local em uma conta de armazenamento existente. |
> | Ação | Microsoft. OperationalInsights/Workspaces/upgradetranslationfailures/Read | Obter log de falhas de conversão de atualização de pesquisa para o espaço de trabalho |
> | Ação | Microsoft. OperationalInsights/espaços de trabalho/usos/leitura | Obtém dados de uso para um espaço de trabalho, incluindo a quantidade de dados lidos pelo espaço de trabalho. |
> | Ação | Microsoft. OperationalInsights/Workspaces/Write | Cria um novo espaço de trabalho ou links para um espaço de trabalho existente fornecendo a ID do cliente do espaço de trabalho existente. |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. OperationsManagement/managementAssociations/Delete | Excluir Associação de gerenciamento existente |
> | Ação | Microsoft. OperationsManagement/managementAssociations/Read | Obter Associação de gerenciamento existente |
> | Ação | Microsoft. OperationsManagement/managementAssociations/Write | Criar uma nova associação de gerenciamento |
> | Ação | Microsoft. OperationsManagement/managementConfigurations/Delete | Excluir configuração de gerenciamento existente |
> | Ação | Microsoft. OperationsManagement/managementConfigurations/Read | Obter configuração de gerenciamento existente |
> | Ação | Microsoft. OperationsManagement/managementConfigurations/Write | Criar uma nova configuração de gerenciamento |
> | Ação | Microsoft. OperationsManagement/registro/ação | Registrar uma assinatura em um provedor de recursos. |
> | Ação | Microsoft. OperationsManagement/Solutions/Delete | Excluir solução OMS existente |
> | Ação | Microsoft. OperationsManagement/soluções/leitura | Obter solução OMS de saída |
> | Ação | Microsoft. OperationsManagement/soluções/gravação | Criar nova solução do OMS |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. PolicyInsights/asyncOperationResults/Read | Obtém o resultado da operação assíncrona. |
> | Dataaction | Microsoft. PolicyInsights/checkDataPolicyCompliance/Action | Verifique o status de conformidade de um determinado componente em relação a políticas de dados. |
> | Ação | Microsoft. PolicyInsights/operações/leitura | Obtém operações com suporte no namespace Microsoft. PolicyInsights |
> | Dataaction | Microsoft. PolicyInsights/policyEvents/logDataEvents/Action | Registre os eventos de política do componente de recurso. |
> | Ação | Microsoft. PolicyInsights/policyEvents/queryResults/Action | Consultar informações sobre eventos de política. |
> | Ação | Microsoft. PolicyInsights/policyEvents/queryResults/Read | Consultar informações sobre eventos de política. |
> | Ação | Microsoft. PolicyInsights/policyStates/queryResults/Action | Consultar informações sobre Estados de política. |
> | Ação | Microsoft. PolicyInsights/policyStates/queryResults/Read | Consultar informações sobre Estados de política. |
> | Ação | Microsoft. PolicyInsights/policyStates/resume/ação | Informações de resumo da consulta sobre os Estados mais recentes da política. |
> | Ação | Microsoft. PolicyInsights/policyStates/resumir/ler | Informações de resumo da consulta sobre os Estados mais recentes da política. |
> | Ação | Microsoft. PolicyInsights/policyStates/triggerEvaluation/Action | Dispara uma nova avaliação de conformidade para o escopo selecionado. |
> | Ação | Microsoft. PolicyInsights/policyTrackedResources/queryResults/Read | Consultar informações sobre os recursos exigidos pelas políticas de DeployIfNotExists. |
> | Ação | Microsoft. PolicyInsights/registro/ação | Registra o provedor de recursos de informações da política da Microsoft e habilita ações nele. |
> | Ação | Microsoft. PolicyInsights/remediations/cancelamento/ação | Cancelar as correções de política da Microsoft em andamento. |
> | Ação | Microsoft. PolicyInsights/correções/excluir | Excluir correções de política. |
> | Ação | Microsoft. PolicyInsights/remediations/listDeployments/Read | Lista as implantações exigidas por uma atualização de política. |
> | Ação | Microsoft. PolicyInsights/correções/leitura | Obter correções de política. |
> | Ação | Microsoft. PolicyInsights/correções/gravação | Criar ou atualizar as correções da política da Microsoft. |
> | Ação | Microsoft. PolicyInsights/cancelar registro/ação | Cancela o registro do provedor de recursos de informações da política da Microsoft. |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Portal/consoles/excluir | Remove a instância de Cloud Shell. |
> | Ação | Microsoft. Portal/consoles/leitura | Lê a instância de Cloud Shell. |
> | Ação | Microsoft. Portal/consoles/gravação | Criar ou atualizar uma instância de Cloud Shell. |
> | Ação | Microsoft. Portal/dashboards/excluir | Remove o painel da assinatura. |
> | Ação | Microsoft. Portal/dashboards/leitura | Lê os painéis para a assinatura. |
> | Ação | Microsoft. Portal/dashboards/gravação | Adicionar ou modificar o painel em uma assinatura. |
> | Ação | Microsoft. Portal/registro/ação | Registrar no portal |
> | Ação | Microsoft. Portal/UserSettings/Delete | Remove as configurações de usuário do Cloud Shell. |
> | Ação | Microsoft. Portal/UserSettings/Read | Lê as configurações de usuário do Cloud Shell. |
> | Ação | Microsoft. Portal/UserSettings/Write | Criar ou atualizar Cloud Shell configuração de usuário. |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. PowerBIDedicated/capacidades/excluir | Exclui a Power BI capacidade dedicada. |
> | Ação | Microsoft. PowerBIDedicated/capacidades/leitura | Recupera as informações da capacidade Power BI dedicada especificada. |
> | Ação | Microsoft. PowerBIDedicated/capacidades/retomar/ação | Retoma a capacidade. |
> | Ação | Microsoft. PowerBIDedicated/capacidades/SKUs/leitura | Recuperar informações de SKU disponíveis para a capacidade |
> | Ação | Microsoft. PowerBIDedicated/capacidades/suspensão/ação | Suspende a capacidade. |
> | Ação | Microsoft. PowerBIDedicated/capacidades/gravação | Cria ou atualiza a capacidade especificada Power BI dedicada. |
> | Ação | Microsoft. PowerBIDedicated/Locations/checkNameAvailability/Action | Verifica se o nome de capacidade dedicado fornecido Power BI é válido e não está em uso. |
> | Ação | Microsoft. PowerBIDedicated/Locations/operationresults/Read | Recupera as informações do resultado da operação especificada. |
> | Ação | Microsoft. PowerBIDedicated/Locations/operationstatuses/Read | Recupera as informações do status de operação especificado. |
> | Ação | Microsoft. PowerBIDedicated/operações/leitura | Recupera as informações de operações |
> | Ação | Microsoft. PowerBIDedicated/registro/ação | Registra Power BI provedor de recursos dedicado. |
> | Ação | Microsoft. PowerBIDedicated/SKUs/Read | Recupera as informações de SKUs |

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Recoveryservices/Locations/allocatedStamp/Read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Ação | Microsoft. Recoveryservices/Locations/allocateStamp/Action | AllocateStamp é uma operação interna usada pelo serviço |
> | Ação | Microsoft. Recoveryservices/Locations/backupPreValidateProtection/Action |  |
> | Ação | Microsoft. Recoveryservices/Locations/backupProtectedItem/Write | Criar um item protegido de backup |
> | Ação | Microsoft. Recoveryservices/Locations/backupProtectedItems/Read | Retorna a lista de todos os itens protegidos. |
> | Ação | Microsoft. Recoveryservices/Locations/backupStatus/Action | Verificar o status do backup dos cofres dos serviços de recuperação |
> | Ação | Microsoft. Recoveryservices/Locations/backupValidateFeatures/Action | Validar recursos |
> | Ação | Microsoft. Recoveryservices/Locations/checkNameAvailability/Action | Verificar a disponibilidade do nome do recurso é uma API para verificar se o nome do recurso está disponível |
> | Ação | Microsoft. Recoveryservices/Locations/operationStatus/Read | Obtém o status da operação para uma determinada operação |
> | Ação | Microsoft. Recoveryservices/Operations/Read | A operação retorna a lista de operações para um provedor de recursos |
> | Ação | Microsoft. Recoveryservices/registro/ação | Registra a assinatura para o provedor de recursos fornecido |
> | Ação | Microsoft. Recoveryservices/Vaults/BackupConfig/Read | Retorna a configuração do cofre dos serviços de recuperação. |
> | Ação | Microsoft. Recoveryservices/Vaults/BackupConfig/Write | Atualiza a configuração do cofre dos serviços de recuperação. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupEngines/Read | Retorna todos os servidores de gerenciamento de backup registrados com o cofre. |
> | Ação | Microsoft. Recoveryservices/Vaultes/backupFabrics/backupProtectionIntent/Delete | Excluir uma intenção de proteção de backup |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/backupProtectionIntent/Read | Obter uma intenção de proteção de backup |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/backupProtectionIntent/Write | Criar uma intenção de proteção de backup |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/operationResults/Read | Retorna o status da operação |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/operationsStatus/Read | Retorna o status da operação |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectableContainers/Read | Obter todos os contêineres protegíveis |
> | Ação | Microsoft. Recoveryservices/Vaultes/backupFabrics/protectionContainers/Delete | Exclui o contêiner registrado |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/inquire/Action | Fazer consulta para cargas de trabalho dentro de um contêiner |
> | Ação | Microsoft. Recoveryservices/cofres/backupFabrics/protectionContainers/itens/ler | Obter todos os itens em um contêiner |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/operationResults/Read | Obtém o resultado da operação executada no contêiner de proteção. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/operationsStatus/Read | Obtém o status da operação executada no contêiner de proteção. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/ação | Executa o backup para o item protegido. |
> | Ação | Microsoft. Recoveryservices/Vaultes/backupFabrics/protectionContainers/protectedItems/Delete | Excluir item protegido |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Obtém o resultado da operação executada em itens protegidos. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Retorna o status da operação executada nos itens protegidos. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Retorna os detalhes do objeto do item protegido |
> | Ação | Microsoft. Recoveryservices/Vaultes/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Provisionar a recuperação instantânea de item para o item protegido |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Obter pontos de recuperação para itens protegidos. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Restore/Action | Restaurar pontos de recuperação para itens protegidos. |
> | Ação | Microsoft. Recoveryservices/Vaultes/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Revogar a recuperação instantânea de item para o item protegido |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Criar um item protegido de backup |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/Read | Retorna todos os contêineres registrados |
> | Ação | Microsoft. Recoveryservices/Vaults/backupFabrics/protectionContainers/Write | Cria um contêiner registrado |
> | Ação | Microsoft. Recoveryservices/cofres/backupFabrics/refreshContainers/ação | Atualiza a lista de contêineres |
> | Ação | Microsoft. Recoveryservices/cofres/backupJobs/cancelamento/ação | Cancelar o trabalho |
> | Ação | Microsoft. Recoveryservices/Vaults/backupJobs/operationResults/Read | Retorna o resultado da operação de trabalho. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupJobs/operationsStatus/Read | Retorna o status da operação de trabalho. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupJobs/Read | Retorna todos os objetos de trabalho |
> | Ação | Microsoft. Recoveryservices/Vaultes/backupJobsExport/Action | Exportar trabalhos |
> | Ação | Microsoft. Recoveryservices/Vaults/backupOperationResults/Read | Retorna o resultado da operação de backup para o cofre dos serviços de recuperação. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupOperations/Read | Retorna o status da operação de backup do cofre dos serviços de recuperação. |
> | Ação | Microsoft. Recoveryservices/Vaultes/backupPolicies/Delete | Excluir uma política de proteção |
> | Ação | Microsoft. Recoveryservices/Vaults/backupPolicies/operationResults/Read | Obter resultados da operação de política. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupPolicies/Operations/Read | Obter o status da operação de política. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupPolicies/Read | Retorna todas as políticas de proteção |
> | Ação | Microsoft. Recoveryservices/Vaults/backupPolicies/Write | Criar política de proteção |
> | Ação | Microsoft. Recoveryservices/Vaults/backupProtectableItems/Read | Retorna a lista de todos os itens protegíveis. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupProtectedItems/Read | Retorna a lista de todos os itens protegidos. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupProtectionContainers/Read | Retorna todos os contêineres que pertencem à assinatura |
> | Ação | Microsoft. Recoveryservices/Vaults/backupProtectionIntents/Read | Listar todas as tentativas de proteção de backup |
> | Ação | Microsoft. Recoveryservices/Vaultes/backupSecurityPIN/Action | Retorna informações de PIN de segurança para o cofre dos serviços de recuperação. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupstorageconfig/Read | Retorna a configuração de armazenamento para o cofre dos serviços de recuperação. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupstorageconfig/Write | Atualiza a configuração de armazenamento para o cofre dos serviços de recuperação. |
> | Ação | Microsoft. Recoveryservices/Vaults/backupUsageSummaries/Read | Retorna resumos de itens protegidos e servidores protegidos para serviços de recuperação. |
> | Ação | Microsoft. Recoveryservices/Vaultes/backupValidateOperation/Action | Validar operação no item protegido |
> | Ação | Microsoft. Recoveryservices/cofres/certificados/gravação | A operação atualizar certificado de recurso atualiza o certificado de credencial do cofre/recurso. |
> | Ação | Microsoft. Recoveryservices/cofres/excluir | A operação excluir cofre exclui o recurso do Azure especificado do tipo ' cofre ' |
> | Ação | Microsoft. Recoveryservices/Vaultes/extendedInformation/Delete | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Ação | Microsoft. Recoveryservices/Vaults/extendedInformation/Read | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Ação | Microsoft. Recoveryservices/Vaults/extendedInformation/Write | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Ação | Microsoft. Recoveryservices/Vaults/monitoringAlerts/Read | Obtém os alertas para o cofre dos serviços de recuperação. |
> | Ação | Microsoft. Recoveryservices/Vaults/monitoringAlerts/Write | Resolve o alerta. |
> | Ação | Microsoft. Recoveryservices/Vaults/monitoringConfigurations/Read | Obtém a configuração de notificação do cofre dos serviços de recuperação. |
> | Ação | Microsoft. Recoveryservices/Vaults/monitoringConfigurations/Write | Configura notificações de email para o cofre dos serviços de recuperação. |
> | Ação | Microsoft. Recoveryservices/cofres/leitura | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Ação | Microsoft. Recoveryservices/Vaultes/registeredIdentities/Delete | A operação cancelar registro do contêiner pode ser usada para cancelar o registro de um contêiner. |
> | Ação | Microsoft. Recoveryservices/Vaults/registeredIdentities/operationResults/Read | A operação obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Ação | Microsoft. Recoveryservices/Vaults/registeredIdentities/Read | A operação obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Ação | Microsoft. Recoveryservices/Vaults/registeredIdentities/Write | A operação registrar contêiner de serviço pode ser usada para registrar um contêiner com o serviço de recuperação. |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationAlertSettings/Read | Ler as configurações de alertas |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationAlertSettings/Write | Criar ou atualizar as configurações de alertas |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationEvents/Read | Ler todos os eventos |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/checkConsistency/ação | Verifica a consistência da malha |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/Delete | Excluir todas as malhas |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/deployProcessServerImage/ação | Implantar imagem do servidor de processo |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/migratetoaad/ação | Migrar malha para o AAD |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nas malhas de recursos |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/Read | Ler malhas |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/reassociateGateway/ação | Reassociar gateway |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/remove/Action | Remover malha |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/renewcertificate/ação | Renovar certificado para malha |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationLogicalNetworks/Read | Ler redes lógicas |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationNetworks/Read | Ler redes |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationNetworks/replicationNetworkMappings/Delete | Excluir todos os mapeamentos de rede |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Ler mapeamentos de rede |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Write | Criar ou atualizar mapeamentos de rede |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/Action | Descobrir item de proteção |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos contêineres de proteção de recurso |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/Read | Ler contêineres de proteção |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationProtectionContainers/remove/Action | Remover contêiner de proteção |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Delete | Excluir todos os itens de migração |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrar/ação | Migrar item |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/Read | Ler pontos de recuperação de migração |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos itens de migração de recursos |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Read | Ler todos os itens de migração |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/ação | Migrar teste |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/ação | Limpeza de migração de teste |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/Write | Criar ou atualizar itens de migração |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Ler itens protegíveis |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/adddisks/Action | Adicionar discos |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/ação | Aplicar ponto de recuperação |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Delete | Excluir todos os itens protegidos |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/ação | Confirmação de failover |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos itens protegidos por recurso |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/ação | Failover planejado |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Ler itens protegidos |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Ler pontos de recuperação de replicação |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remover/ação | Remover item protegido |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/ação | Remover discos |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/ação | Reparar replicação |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/nova proteção/ação | Proteger novamente o item protegido |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/ação |  |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/ação | Enviar comentários |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/Read | Ler os tamanhos de computação de destino |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/ação | Ativação Pós-falha de Teste |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/ação | Limpeza do failover de teste |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/ação | Ativação Pós-Falha |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/ação | Atualizar serviço de mobilidade |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Write | Criar ou atualizar itens protegidos |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Delete | Excluir todos os mapeamentos de contêiner de proteção |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos mapeamentos de contêineres de proteção de recurso |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Ler mapeamentos de contêiner de proteção |
> | Ação | Microsoft. Recoveryservices/cofres/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remover/ação | Remover mapeamento de contêiner de proteção |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Write | Criar ou atualizar mapeamentos de contêiner de proteção |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationProtectionContainers/switchprotection/Action | Alternar contêiner de proteção |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationProtectionContainers/Write | Criar ou atualizar contêineres de proteção |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationRecoveryServicesProviders/Delete | Excluir qualquer provedor de serviços de recuperação |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationRecoveryServicesProviders/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos provedores de serviços de recuperação de recursos |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Ler provedores de serviços de recuperação |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationRecoveryServicesProviders/reualizarprovider/Action | Atualizar provedor |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationRecoveryServicesProviders/remove/Action | Remover provedor de serviços de recuperação |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Write | Criar ou atualizar provedores de serviços de recuperação |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationStorageClassifications/Read | Ler classificações de armazenamento |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Delete | Excluir todos os mapeamentos de classificação de armazenamento |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos mapeamentos de classificação de armazenamento de recursos |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Ler mapeamentos de classificação de armazenamento |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Write | Criar ou atualizar mapeamentos de classificação de armazenamento |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationFabrics/replicationvCenters/Delete | Excluir qualquer vCenters |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationvCenters/operationresults/Read | Acompanhar os resultados de uma operação assíncrona no recurso vCenters |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationvCenters/Read | Ler qualquer vCenters |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/replicationvCenters/Write | Criar ou atualizar qualquer vCenters |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationFabrics/Write | Criar ou atualizar malhas |
> | Ação | Microsoft. Recoveryservices/cofres/replicationJobs/cancelamento/ação | Cancelar trabalho |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationJobs/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos trabalhos de recursos |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationJobs/Read | Ler trabalhos |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationJobs/Restart/Action | Reiniciar trabalho |
> | Ação | Microsoft. Recoveryservices/cofres/replicationJobs/retomar/ação | Retomar trabalho |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationMigrationItems/Read | Ler todos os itens de migração |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationNetworkMappings/Read | Ler mapeamentos de rede |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationNetworks/Read | Ler redes |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationPolicies/Delete | Excluir todas as políticas |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationPolicies/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nas políticas de recursos |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationPolicies/Read | Ler políticas |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationPolicies/Write | Criar ou atualizar qualquer política |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationProtectedItems/Read | Ler itens protegidos |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationProtectionContainerMappings/Read | Ler mapeamentos de contêiner de proteção |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationProtectionContainers/Read | Ler contêineres de proteção |
> | Ação | Microsoft. Recoveryservices/vaultes/replicationRecoveryPlans/Delete | Excluir qualquer plano de recuperação |
> | Ação | Microsoft. Recoveryservices/cofres/replicationRecoveryPlans/failoverCommit/ação | Plano de recuperação de confirmação de failover |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationRecoveryPlans/operationresults/Read | Acompanhar os resultados de uma operação assíncrona nos planos de recuperação de recursos |
> | Ação | Microsoft. Recoveryservices/cofres/replicationRecoveryPlans/plannedFailover/ação | Plano de recuperação de failover planejado |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationRecoveryPlans/Read | Ler planos de recuperação |
> | Ação | Microsoft. Recoveryservices/cofres/replicationRecoveryPlans/nova proteção/ação | Reproteger plano de recuperação |
> | Ação | Microsoft. Recoveryservices/cofres/replicationRecoveryPlans/testFailover/ação | Plano de recuperação de failover de teste |
> | Ação | Microsoft. Recoveryservices/cofres/replicationRecoveryPlans/testFailoverCleanup/ação | Plano de recuperação de limpeza do failover de teste |
> | Ação | Microsoft. Recoveryservices/cofres/replicationRecoveryPlans/unplannedFailover/ação | Plano de recuperação de failover |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationRecoveryPlans/Write | Criar ou atualizar planos de recuperação |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationRecoveryServicesProviders/Read | Ler provedores de serviços de recuperação |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationStorageClassificationMappings/Read | Ler mapeamentos de classificação de armazenamento |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationStorageClassifications/Read | Ler classificações de armazenamento |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationSupportedOperatingSystems/Read | Ler qualquer  |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationUsages/Read | Ler usos de replicação de cofre |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationVaultHealth/operationresults/Read | Acompanhar os resultados de uma operação assíncrona na integridade da replicação do cofre de recursos |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationVaultHealth/Read | Ler integridade da replicação do cofre |
> | Ação | Microsoft. Recoveryservices/cofres/replicationVaultHealth/atualização/ação | Atualizar integridade do cofre |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationVaultSettings/Read | Ler qualquer  |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationVaultSettings/Write | Criar ou atualizar qualquer  |
> | Ação | Microsoft. Recoveryservices/Vaults/replicationvCenters/Read | Ler qualquer vCenters |
> | Ação | Microsoft. Recoveryservices/cofres/usos/leitura | Ler os usos do cofre |
> | Ação | Microsoft. Recoveryservices/cofres/usos/leitura | Retorna os detalhes de uso de um cofre dos serviços de recuperação. |
> | Ação | Microsoft. Recoveryservices/Vaults/vaultTokens/Read | A operação de token do cofre pode ser usada para obter o token do cofre para operações de back-end no nível do cofre. |
> | Ação | Microsoft. Recoveryservices/cofres/gravação | A operação criar cofre cria um recurso do Azure do tipo ' cofre ' |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Relay/checkNameAvailability/Action | Verifica a disponibilidade do namespace sob determinada assinatura. |
> | Ação | Microsoft. Relay/checkNamespaceAvailability/Action | Verifica a disponibilidade do namespace sob determinada assinatura. Esta API foi preterida, use CheckNameAvailability em vez disso. |
> | Ação | Microsoft. Relay/namespaces/authorizationRules/ação | Regra de autorização de namespace de atualizações. Esta API foi preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace em vez disso. Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft. Relay/namespaces/authorizationRules/Delete | Excluir regra de autorização de namespace. A regra de autorização de namespace padrão não pode ser excluída.  |
> | Ação | Microsoft. Relay/namespaces/authorizationRules/listkeys/ação | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft. Relay/namespaces/authorizationRules/Read | Obtenha a lista de descrições de regras de autorização de namespaces. |
> | Ação | Microsoft. Relay/namespaces/authorizationRules/regenerateKeys/ação | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft. Relay/namespaces/authorizationRules/Write | Crie regras de autorização de nível de namespace e atualize suas propriedades. Os direitos de acesso às regras de autorização, as chaves primária e secundária podem ser atualizadas. |
> | Ação | Microsoft. Relay/namespaces/excluir | Excluir recurso de namespace |
> | Ação | Microsoft. Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/ação | Obter as chaves de regras de autorização para o namespace primário de recuperação de desastre |
> | Ação | Microsoft. Relay/namespaces/disasterRecoveryConfigs/authorizationRules/Read | Obter regras de autorização do namespace primário de recuperação de desastre |
> | Ação | Microsoft. Relay/namespaces/disasterRecoveryConfigs/breakPairing/ação | Desabilita a recuperação de desastres e interrompe a replicação de alterações de namespaces primários para secundários. |
> | Ação | Microsoft. Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/ação | Verifica a disponibilidade do alias de namespace na assinatura fornecida. |
> | Ação | Microsoft. Relay/namespaces/disasterRecoveryConfigs/Delete | Exclui a configuração de recuperação de desastre associada ao namespace. Esta operação só pode ser invocada por meio do namespace primário. |
> | Ação | Microsoft. Relay/namespaces/disasterRecoveryConfigs/failover/ação | Invoca um failover de DR geográfica e reconfigura o alias de namespace para apontar para o namespace secundário. |
> | Ação | Microsoft. Relay/namespaces/disasterRecoveryConfigs/Read | Obtém a configuração de recuperação de desastre associada ao namespace. |
> | Ação | Microsoft. Relay/namespaces/disasterRecoveryConfigs/Write | Cria ou atualiza a configuração de recuperação de desastre associada ao namespace. |
> | Ação | Microsoft. Relay/namespaces/HybridConnections/authorizationRules/ação | Operação para atualizar HybridConnection. Não há suporte para esta operação na versão de API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft. Relay/namespaces/HybridConnections/authorizationRules/Delete | Operação para excluir regras de autorização HybridConnection |
> | Ação | Microsoft. Relay/namespaces/HybridConnections/authorizationRules/listkeys/ação | Obter a cadeia de conexão para HybridConnection |
> | Ação | Microsoft. Relay/namespaces/HybridConnections/authorizationRules/Read |  Obter a lista de regras de autorização do HybridConnection |
> | Ação | Microsoft. Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/ação | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft. Relay/namespaces/HybridConnections/authorizationRules/Write | Crie regras de autorização HybridConnection e atualize suas propriedades. Os direitos de acesso das regras de autorização podem ser atualizados. |
> | Ação | Microsoft. Relay/namespaces/HybridConnections/Delete | Operação para excluir o recurso HybridConnection |
> | Ação | Microsoft. Relay/namespaces/HybridConnections/Read | Obter lista de descrições de recursos HybridConnection |
> | Ação | Microsoft. Relay/namespaces/HybridConnections/Write | Criar ou atualizar Propriedades HybridConnection. |
> | Ação | Microsoft. Relay/namespaces/messagingPlan/Read | Obtém o plano de mensagens para um namespace.<br>Esta API foi preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso de namespace (pai) em versões de API posteriores.<br>Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft. Relay/namespaces/messagingPlan/Write | Atualiza o plano de mensagens para um namespace.<br>Esta API foi preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso de namespace (pai) em versões de API posteriores.<br>Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft. Relay/namespaces/operationresults/Read | Obter o status da operação de namespace |
> | Ação | Microsoft. Relay/namespaces/Providers/Microsoft. insights/diagnosticSettings/Read | Obter lista de descrições de recursos das configurações de diagnóstico de namespace |
> | Ação | Microsoft. Relay/namespaces/Providers/Microsoft. insights/diagnosticSettings/Write | Obter lista de descrições de recursos das configurações de diagnóstico de namespace |
> | Ação | Microsoft. Relay/namespaces/Providers/Microsoft. insights/logDefinitions/Read | Obter lista de descrições de recursos de logs de namespace |
> | Ação | Microsoft. Relay/namespaces/Providers/Microsoft. insights/metricDefinitions/Read | Obter lista de descrições de recursos de métricas de namespace |
> | Ação | Microsoft. Relay/namespaces/leitura | Obter a lista de descrição de recurso de namespace |
> | Ação | Microsoft. Relay/namespaces/removeAcsNamepsace/ação | Remover namespace do ACS |
> | Ação | Microsoft. Relay/namespaces/WcfRelays/authorizationRules/ação | Operação para atualizar WcfRelay. Não há suporte para esta operação na versão de API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft. Relay/namespaces/WcfRelays/authorizationRules/Delete | Operação para excluir regras de autorização WcfRelay |
> | Ação | Microsoft. Relay/namespaces/WcfRelays/authorizationRules/listkeys/ação | Obter a cadeia de conexão para WcfRelay |
> | Ação | Microsoft. Relay/namespaces/WcfRelays/authorizationRules/Read |  Obter a lista de regras de autorização do WcfRelay |
> | Ação | Microsoft. Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/ação | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft. Relay/namespaces/WcfRelays/authorizationRules/Write | Crie regras de autorização WcfRelay e atualize suas propriedades. Os direitos de acesso das regras de autorização podem ser atualizados. |
> | Ação | Microsoft. Relay/namespaces/WcfRelays/Delete | Operação para excluir o recurso WcfRelay |
> | Ação | Microsoft. Relay/namespaces/WcfRelays/Read | Obter lista de descrições de recursos WcfRelay |
> | Ação | Microsoft. Relay/namespaces/WcfRelays/Write | Criar ou atualizar Propriedades WcfRelay. |
> | Ação | Microsoft. Relay/namespaces/gravação | Crie um recurso de namespace e atualize suas propriedades. As marcas e a capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft. Relay/operações/leitura | Obter operações |
> | Ação | Microsoft. Relay/registro/ação | Registra a assinatura para o provedor de recursos de retransmissão e habilita a criação de recursos de retransmissão |
> | Ação | Microsoft. Relay/cancelar registro/ação | Registra a assinatura para o provedor de recursos de retransmissão e habilita a criação de recursos de retransmissão |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ResourceHealth/AvailabilityStatuses/Current/Read | Obtém o status de disponibilidade para o recurso especificado |
> | Ação | Microsoft. ResourceHealth/AvailabilityStatuses/Read | Obtém os status de disponibilidade de todos os recursos no escopo especificado |
> | Ação | Microsoft. ResourceHealth/eventos/ler | Obter eventos de integridade do serviço para determinada assinatura |
> | Ação | Microsoft. Resourcehealth/healthevent/Action | Denota a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft. Resourcehealth/healthevent/Activated/ação | Denota a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft. Resourcehealth/healthevent/InProgress/ação | Denota a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft. Resourcehealth/healthevent/pendente/ação | Denota a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft. Resourcehealth/healthevent/resolvida/ação | Denota a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft. Resourcehealth/healthevent/atualização/ação | Denota a alteração no estado de integridade para o recurso especificado |
> | Ação | Microsoft. ResourceHealth/impactedResources/Read | Obter recursos afetados para determinada assinatura |
> | Ação | Microsoft. ResourceHealth/metadados/leitura | Obtém metadados |
> | Ação | Microsoft. ResourceHealth/operações/leitura | Obter as operações disponíveis para o Microsoft ResourceHealth |
> | Ação | Microsoft. ResourceHealth/registro/ação | Registra a assinatura para o Microsoft ResourceHealth |
> | Ação | Microsoft. ResourceHealth/cancelar registro/ação | Cancela o registro da assinatura para o Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Resources/calculateTemplateHash/Action | Calcule o hash do modelo fornecido. |
> | Ação | Microsoft. Resources/checkPolicyCompliance/Action | Verifique o status de conformidade de um determinado recurso em relação a políticas de recursos. |
> | Ação | Microsoft. Resources/checkResourceName/Action | Verifique a validade do nome do recurso. |
> | Ação | Microsoft. Resources/Implantations/cancelamento/ação | Cancela uma implantação. |
> | Ação | Microsoft. Resources/Implantations/Delete | Exclui uma implantação. |
> | Ação | Microsoft. Resources/Implantations/exportatemplate/ação | Exportar modelo para uma implantação |
> | Ação | Microsoft. Resources/implantações/operações/leitura | Obtém ou lista operações de implantação. |
> | Ação | Microsoft. Resources/Implantations/operationstatuses/Read | Obtém ou lista os status da operação de implantação. |
> | Ação | Microsoft. Resources/implantações/leitura | Obtém ou lista implantações. |
> | Ação | Microsoft. Resources/Implantations/validação/ação | Valida uma implantação. |
> | Ação | Microsoft. Resources/Implantations/whatIf/Action | Prevê alterações de implantação de modelo. |
> | Ação | Microsoft. Resources/Implantations/Write | Cria ou atualiza uma implantação. |
> | Ação | Microsoft. Resources/links/Delete | Exclui um link de recurso. |
> | Ação | Microsoft. Resources/links/Read | Obtém ou lista links de recursos. |
> | Ação | Microsoft. Resources/links/Write | Cria ou atualiza um link de recurso. |
> | Ação | Microsoft. Resources/Marketplace/compra/ação | Adquire um recurso do Marketplace. |
> | Ação | Microsoft. Resources/Providers/Read | Obtenha a lista de provedores. |
> | Ação | Microsoft. Resources/Resources/Read | Obtenha a lista de recursos com base nos filtros. |
> | Ação | Microsoft. Resources/subscriptions/Locations/Read | Obtém a lista de locais com suporte. |
> | Ação | Microsoft. Resources/subscriptions/operationresults/Read | Obter os resultados da operação de assinatura. |
> | Ação | Microsoft. Resources/subscriptions/Providers/Read | Obtém ou lista os provedores de recursos. |
> | Ação | Microsoft. Resources/subscriptions/Read | Obtém a lista de assinaturas. |
> | Ação | Microsoft. Resources/subscriptions/resourceGroups/Delete | Exclui um grupo de recursos e todos os seus recursos. |
> | Ação | Microsoft. Resources/subscriptions/resourcegroups/Implantations/Operations/Read | Obtém ou lista operações de implantação. |
> | Ação | Microsoft. Resources/subscriptions/resourcegroups/Implantations/operationstatuses/Read | Obtém ou lista os status da operação de implantação. |
> | Ação | Microsoft. Resources/subscriptions/resourcegroups/implantações/leitura | Obtém ou lista implantações. |
> | Ação | Microsoft. Resources/subscriptions/resourcegroups/implantações/gravação | Cria ou atualiza uma implantação. |
> | Ação | Microsoft. Resources/subscriptions/resourceGroups/moveResources/Action | Move os recursos de um grupo de recursos para outro. |
> | Ação | Microsoft. Resources/subscriptions/resourceGroups/Read | Obtém ou lista os grupos de recursos. |
> | Ação | Microsoft. Resources/subscriptions/resourcegroups/Resources/Read | Obtém os recursos para o grupo de recursos. |
> | Ação | Microsoft. Resources/subscriptions/resourceGroups/validateMoveResources/Action | Validar a movimentação de recursos de um grupo de recursos para outro. |
> | Ação | Microsoft. Resources/subscriptions/resourceGroups/Write | Cria ou atualiza um grupo de recursos. |
> | Ação | Microsoft. Resources/subscriptions/Resources/Read | Obtém recursos de uma assinatura. |
> | Ação | Microsoft. Resources/subscriptions/tagNames/Delete | Exclui uma marca de assinatura. |
> | Ação | Microsoft. Resources/subscriptions/tagNames/Read | Obtém ou lista as marcas de assinatura. |
> | Ação | Microsoft. Resources/subscriptions/tagNames/tagValues/Delete | Exclui um valor de marca de assinatura. |
> | Ação | Microsoft. Resources/subscriptions/tagNames/tagValues/Read | Obtém ou lista os valores de marca de assinatura. |
> | Ação | Microsoft. Resources/subscriptions/tagNames/tagValues/Write | Adiciona um valor de marca de assinatura. |
> | Ação | Microsoft. Resources/subscriptions/tagNames/Write | Adiciona uma marca de assinatura. |
> | Ação | Microsoft. Resources/Tags/Delete | Remove todas as marcas em um recurso. |
> | Ação | Microsoft. Resources/Tags/Read | Obtém todas as marcas em um recurso. |
> | Ação | Microsoft. Resources/Tags/Write | Atualiza as marcas em um recurso substituindo ou mesclando as marcas existentes por um novo conjunto de marcas ou removendo as marcas existentes. |
> | Ação | Microsoft. Resources/locatários/Read | Obtém a lista de locatários. |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Scheduler/gratuitas/Delete | Exclui a coleção de trabalhos. |
> | Ação | Microsoft. Scheduler/gratuitas/desabilitar/ação | Desabilita a coleção de trabalhos. |
> | Ação | Microsoft. Scheduler/gratuitas/Enable/Action | Habilita a coleta de trabalhos. |
> | Ação | Microsoft. Scheduler/gratuitas/Jobs/excluir | Exclui o trabalho. |
> | Ação | Microsoft. Scheduler/gratuitas/Jobs/generateLogicAppDefinition/ação | Gera a definição do aplicativo lógico com base em um trabalho do Agendador. |
> | Ação | Microsoft. Scheduler/gratuitas/Jobs/jobhistories/Read | Obtém o histórico do trabalho. |
> | Ação | Microsoft. Scheduler/gratuitas/Jobs/ler | Obtém o trabalho. |
> | Ação | Microsoft. Scheduler/gratuitas/Jobs/executar/ação | Executa o trabalho. |
> | Ação | Microsoft. Scheduler/gratuitas/Jobs/Write | Cria ou atualiza o trabalho. |
> | Ação | Microsoft. Scheduler/gratuitas/Read | Obter coleção de trabalhos |
> | Ação | Microsoft. Scheduler/gratuitas/Write | Cria ou atualiza a coleção de trabalhos. |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Search/checkNameAvailability/Action | Verifica a disponibilidade do nome do serviço. |
> | Ação | Microsoft. Search/Operations/Read | Lista todas as operações disponíveis do provedor Microsoft. Search. |
> | Ação | Microsoft. Search/registro/ação | Registra a assinatura para o provedor de recursos de pesquisa e habilita a criação de serviços de pesquisa. |
> | Ação | Microsoft. Search/SearchServices/createQueryKey/Action | Cria a chave de consulta. |
> | Ação | Microsoft. Search/SearchServices/Delete | Exclui o serviço de pesquisa. |
> | Ação | Microsoft. Search/SearchServices/deleteQueryKey/Delete | Exclui a chave de consulta. |
> | Ação | Microsoft. Search/SearchServices/listAdminKeys/Action | Lê as chaves de administração. |
> | Ação | Microsoft. Search/SearchServices/listQueryKeys/Action | Retorna a lista de chaves de API de consulta para o serviço de Pesquisa Cognitiva do Azure fornecido. |
> | Ação | Microsoft. Search/SearchServices/listQueryKeys/Read | Retorna a lista de chaves de API de consulta para o serviço de Pesquisa Cognitiva do Azure fornecido. |
> | Ação | Microsoft. Search/SearchServices/Read | Lê o serviço de pesquisa. |
> | Ação | Microsoft. Search/SearchServices/regenerateAdminKey/Action | Regenera a chave de administração. |
> | Ação | Microsoft. Search/SearchServices/iniciar/ação | Inicia o serviço de pesquisa. |
> | Ação | Microsoft. Search/SearchServices/Stop/Action | Interrompe o serviço de pesquisa. |
> | Ação | Microsoft. Search/SearchServices/Write | Cria ou atualiza o serviço de pesquisa. |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Security/adaptiveNetworkHardenings/Force/Action | Impõe as regras de proteção de tráfego determinadas criando regras de segurança correspondentes nos grupos de segurança de rede determinados |
> | Ação | Microsoft. Security/adaptiveNetworkHardenings/Read | Obtém recomendações de proteção de rede adaptável de um recurso protegido do Azure |
> | Ação | Microsoft. Security/advancedThreatProtectionSettings/Read | Obter as configurações de proteção avançada contra ameaças para o recurso |
> | Ação | Microsoft. Security/advancedThreatProtectionSettings/Write | Atualiza as configurações de proteção avançada contra ameaças para o recurso |
> | Ação | Microsoft. Security/Alerts/Read | Obtém todos os alertas de segurança disponíveis |
> | Ação | Microsoft. Security/applicationWhitelistings/Read | Obter as listas de permissões do aplicativo |
> | Ação | Microsoft. Security/applicationWhitelistings/Write | Criar uma nova lista de permissões de aplicativo ou atualizar uma existente |
> | Ação | Microsoft. Security/assessmentMetadata/Read | Obter metadados de avaliação de segurança disponíveis em sua assinatura |
> | Ação | Microsoft. Security/assessmentMetadata/Write | Criar ou atualizar metadados de avaliação de segurança |
> | Ação | Microsoft. Security/avaliações/leitura | Obtenha avaliações de segurança em sua assinatura |
> | Ação | Microsoft. Security/avaliações/gravação | Criar ou atualizar avaliações de segurança em sua assinatura |
> | Ação | Microsoft. Security/complianceResults/Read | Obter os resultados de conformidade para o recurso |
> | Ação | Microsoft. Security/informationProtectionPolicies/Read | Obter as políticas de proteção de informações para o recurso |
> | Ação | Microsoft. Security/informationProtectionPolicies/Write | Atualiza as políticas de proteção de informações para o recurso |
> | Ação | Microsoft. Security/Locations/Alerts/Activate/Action | Ativar um alerta de segurança |
> | Ação | Microsoft. Security/Locations/Alerts/ignore/Action | Ignorar um alerta de segurança |
> | Ação | Microsoft. Security/Locations/Alerts/Read | Obtém todos os alertas de segurança disponíveis |
> | Ação | Microsoft. Security/Locations/jitNetworkAccessPolicies/Delete | Exclui a política de acesso à rede just-in-time |
> | Ação | Microsoft. Security/Locations/jitNetworkAccessPolicies/initiate/Action | Inicia uma solicitação de política de acesso à rede just-in-time |
> | Ação | Microsoft. Security/Locations/jitNetworkAccessPolicies/Read | Obtém as políticas de acesso à rede just-in-time |
> | Ação | Microsoft. Security/Locations/jitNetworkAccessPolicies/Write | Cria uma nova política de acesso de rede just-in-time ou atualiza uma existente |
> | Ação | Microsoft. Security/Locations/Read | Obtém o local dos dados de segurança |
> | Ação | Microsoft. Security/Locations/Tasks/Activate/Action | Ativar uma recomendação de segurança |
> | Ação | Microsoft. Security/Locations/Tasks/ignore/Action | Ignorar uma recomendação de segurança |
> | Ação | Microsoft. Security/Locations/Tasks/Read | Obtém todas as recomendações de segurança disponíveis |
> | Ação | Microsoft. Security/Locations/Tasks/resolve/Action | Resolver uma recomendação de segurança |
> | Ação | Microsoft. Security/Locations/Tasks/Start/Action | Iniciar uma recomendação de segurança |
> | Ação | Microsoft. Security/Policies/Read | Obter a política de segurança |
> | Ação | Microsoft. Security/Policies/Write | Atualiza a política de segurança |
> | Ação | Microsoft. Security/preços/Delete | Excluir as configurações de preço para o escopo |
> | Ação | Microsoft. Security/preços/leitura | Obter as configurações de preço para o escopo |
> | Ação | Microsoft. Security/preços/gravação | Atualiza as configurações de preços para o escopo |
> | Ação | Microsoft. Security/Register/Action | Registra a assinatura da central de segurança do Azure |
> | Ação | Microsoft. Security/securityContacts/Delete | Exclui o contato de segurança |
> | Ação | Microsoft. Security/securityContacts/Read | Obter o contato de segurança |
> | Ação | Microsoft. Security/securityContacts/Write | Atualiza o contato de segurança |
> | Ação | Microsoft. Security/securitySolutions/Delete | Excluir uma solução de segurança |
> | Ação | Microsoft. Security/securitySolutions/Read | Obter as soluções de segurança |
> | Ação | Microsoft. Security/securitySolutions/Write | Cria uma nova solução de segurança ou atualiza uma existente |
> | Ação | Microsoft. Security/securitySolutionsReferenceData/Read | Obter os dados de referência de soluções de segurança |
> | Ação | Microsoft. Security/securityStatuses/Read | Obter os status de integridade de segurança para recursos do Azure |
> | Ação | Microsoft. Security/securityStatusesSummaries/Read | Obter os resumos de status de segurança para o escopo |
> | Ação | Microsoft. Security/Settings/Read | Obtém as configurações para o escopo |
> | Ação | Microsoft. Security/Settings/Write | Atualiza as configurações para o escopo |
> | Ação | Microsoft. Security/Tasks/Read | Obtém todas as recomendações de segurança disponíveis |
> | Ação | Microsoft. Security/cancelar registro/ação | Cancela o registro da assinatura na central de segurança do Azure |
> | Ação | Microsoft. Security/webApplicationFirewalls/Delete | Exclui um firewall do aplicativo Web |
> | Ação | Microsoft. Security/webApplicationFirewalls/Read | Obter os firewalls do aplicativo Web |
> | Ação | Microsoft. Security/webApplicationFirewalls/Write | Cria um novo Firewall do aplicativo Web ou atualiza um existente |
> | Ação | Microsoft. Security/workspaceSettings/Connect/Action | Alterar configurações de reconexão de configurações do espaço de trabalho |
> | Ação | Microsoft. Security/workspaceSettings/Delete | Exclui as configurações do espaço de trabalho |
> | Ação | Microsoft. Security/workspaceSettings/Read | Obter as configurações do espaço de trabalho |
> | Ação | Microsoft. Security/workspaceSettings/Write | Atualiza as configurações do espaço de trabalho |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. SecurityGraph/DiagnosticSettings/Delete | Excluindo uma configuração de diagnóstico |
> | Ação | Microsoft. SecurityGraph/DiagnosticSettings/Read | Lendo uma configuração de diagnóstico |
> | Ação | Microsoft. SecurityGraph/DiagnosticSettings/Write | Escrevendo uma configuração de diagnóstico |
> | Ação | Microsoft. SecurityGraph/diagnosticsettingscategories/Read | Lendo categorias de configuração de diagnóstico |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. ServiceBus/checkNameAvailability/Action | Verifica a disponibilidade do namespace sob determinada assinatura. |
> | Ação | Microsoft. ServiceBus/checkNamespaceAvailability/Action | Verifica a disponibilidade do namespace sob determinada assinatura. Esta API foi preterida, use CheckNameAvailability em vez disso. |
> | Ação | Microsoft. ServiceBus/Locations/deleteVirtualNetworkOrSubnets/Action | Exclui as regras de VNet no provedor de recursos do ServiceBus para a VNet especificada |
> | Ação | Microsoft. ServiceBus/namespaces/authorizationRules/ação | Regra de autorização de namespace de atualizações. Esta API foi preterida. Use uma chamada PUT para atualizar a regra de autorização de namespace em vez disso. Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft. ServiceBus/namespaces/authorizationRules/Delete | Excluir regra de autorização de namespace. A regra de autorização de namespace padrão não pode ser excluída.  |
> | Ação | Microsoft. ServiceBus/namespaces/authorizationRules/listkeys/ação | Obter a cadeia de conexão para o namespace |
> | Ação | Microsoft. ServiceBus/namespaces/authorizationRules/Read | Obtenha a lista de descrições de regras de autorização de namespaces. |
> | Ação | Microsoft. ServiceBus/namespaces/authorizationRules/regenerateKeys/ação | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft. ServiceBus/namespaces/authorizationRules/Write | Crie regras de autorização de nível de namespace e atualize suas propriedades. Os direitos de acesso às regras de autorização, as chaves primária e secundária podem ser atualizadas. |
> | Ação | Microsoft. ServiceBus/namespaces/excluir | Excluir recurso de namespace |
> | Ação | Microsoft. ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/ação | Obter as chaves de regras de autorização para o namespace primário de recuperação de desastre |
> | Ação | Microsoft. ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/Read | Obter regras de autorização do namespace primário de recuperação de desastre |
> | Ação | Microsoft. ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/ação | Desabilita a recuperação de desastres e interrompe a replicação de alterações de namespaces primários para secundários. |
> | Ação | Microsoft. ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/ação | Verifica a disponibilidade do alias de namespace na assinatura fornecida. |
> | Ação | Microsoft. ServiceBus/namespaces/disasterRecoveryConfigs/Delete | Exclui a configuração de recuperação de desastre associada ao namespace. Esta operação só pode ser invocada por meio do namespace primário. |
> | Ação | Microsoft. ServiceBus/namespaces/disasterRecoveryConfigs/failover/ação | Invoca um failover de DR geográfica e reconfigura o alias de namespace para apontar para o namespace secundário. |
> | Ação | Microsoft. ServiceBus/namespaces/disasterRecoveryConfigs/Read | Obtém a configuração de recuperação de desastre associada ao namespace. |
> | Ação | Microsoft. ServiceBus/namespaces/disasterRecoveryConfigs/Write | Cria ou atualiza a configuração de recuperação de desastre associada ao namespace. |
> | Ação | Microsoft. ServiceBus/namespaces/eventGridFilters/Delete | Exclui o filtro de grade de eventos associado ao namespace. |
> | Ação | Microsoft. ServiceBus/namespaces/eventGridFilters/Read | Obtém o filtro de grade de eventos associado ao namespace. |
> | Ação | Microsoft. ServiceBus/namespaces/eventGridFilters/Write | Cria ou atualiza o filtro de grade de eventos associado ao namespace. |
> | Ação | Microsoft. ServiceBus/namespaces/Eventhubs/Read | Obter lista de descrições de recursos do EventHub |
> | Ação | Microsoft. ServiceBus/namespaces/ipFilterRules/Delete | Excluir recurso de filtro IP |
> | Ação | Microsoft. ServiceBus/namespaces/ipFilterRules/Read | Obter recurso de filtro IP |
> | Ação | Microsoft. ServiceBus/namespaces/ipFilterRules/Write | Criar recurso de filtro IP |
> | Dataaction | Microsoft. ServiceBus/namespaces/mensagens/recebimento/ação | Receber mensagens |
> | Dataaction | Microsoft. ServiceBus/namespaces/mensagens/enviar/ação | Enviar mensagens |
> | Ação | Microsoft. ServiceBus/namespaces/messagingPlan/Read | Obtém o plano de mensagens para um namespace.<br>Esta API foi preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso de namespace (pai) em versões de API posteriores.<br>Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft. ServiceBus/namespaces/messagingPlan/Write | Atualiza o plano de mensagens para um namespace.<br>Esta API foi preterida.<br>As propriedades expostas por meio do recurso MessagingPlan são movidas para o recurso de namespace (pai) em versões de API posteriores.<br>Não há suporte para esta operação na versão de API 2017-04-01. |
> | Ação | Microsoft. ServiceBus/namespaces/migrar/ação | Migrar operação de namespace |
> | Ação | Microsoft. ServiceBus/namespaces/migrationConfigurations/Delete | Exclui a configuração de migração. |
> | Ação | Microsoft. ServiceBus/namespaces/migrationConfigurations/Read | Obtém a configuração de migração que indica o estado da migração e as operações de replicação pendentes |
> | Ação | Microsoft. ServiceBus/namespaces/migrationConfigurations/REVERT/Action | Reverte a migração de namespace padrão para Premium |
> | Ação | Microsoft. ServiceBus/namespaces/migrationConfigurations/atualização/ação | Atribui o DNS associado ao namespace Standard ao namespace Premium que conclui a migração e interrompe os recursos de sincronização do namespace Standard para Premium |
> | Ação | Microsoft. ServiceBus/namespaces/migrationConfigurations/Write | Cria ou atualiza a configuração de migração. Isso iniciará a sincronização de recursos do padrão para o namespace Premium |
> | Ação | Microsoft. ServiceBus/namespaces/networkruleset/Delete | Excluir recurso de regra de VNET |
> | Ação | Microsoft. ServiceBus/namespaces/networkruleset/Read | Obtém o recurso NetworkRuleSet |
> | Ação | Microsoft. ServiceBus/namespaces/networkruleset/Write | Criar recurso de regra de VNET |
> | Ação | Microsoft. ServiceBus/namespaces/networkrulesets/Delete | Excluir recurso de regra de VNET |
> | Ação | Microsoft. ServiceBus/namespaces/networkrulesets/Read | Obtém o recurso NetworkRuleSet |
> | Ação | Microsoft. ServiceBus/namespaces/networkrulesets/Write | Criar recurso de regra de VNET |
> | Ação | Microsoft. ServiceBus/namespaces/operationresults/Read | Obter o status da operação de namespace |
> | Ação | Microsoft. ServiceBus/namespaces/Providers/Microsoft. insights/diagnosticSettings/Read | Obter lista de descrições de recursos das configurações de diagnóstico de namespace |
> | Ação | Microsoft. ServiceBus/namespaces/Providers/Microsoft. insights/diagnosticSettings/Write | Obter lista de descrições de recursos das configurações de diagnóstico de namespace |
> | Ação | Microsoft. ServiceBus/namespaces/Providers/Microsoft. insights/logDefinitions/Read | Obter lista de descrições de recursos de logs de namespace |
> | Ação | Microsoft. ServiceBus/namespaces/Providers/Microsoft. insights/metricDefinitions/Read | Obter lista de descrições de recursos de métricas de namespace |
> | Ação | Microsoft. ServiceBus/namespaces/filas/authorizationRules/Action | Operação para atualizar a fila. Não há suporte para esta operação na versão de API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft. ServiceBus/namespaces/filas/authorizationRules/Delete | Operação para excluir regras de autorização de fila |
> | Ação | Microsoft. ServiceBus/namespaces/filas/authorizationRules/listkeys/ação | Obter a cadeia de conexão para a fila |
> | Ação | Microsoft. ServiceBus/namespaces/filas/authorizationRules/leitura |  Obter a lista de regras de autorização de fila |
> | Ação | Microsoft. ServiceBus/namespaces/filas/authorizationRules/regenerateKeys/ação | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft. ServiceBus/namespaces/filas/authorizationRules/gravação | Crie regras de autorização de fila e atualize suas propriedades. Os direitos de acesso das regras de autorização podem ser atualizados. |
> | Ação | Microsoft. ServiceBus/namespaces/filas/excluir | Operação para excluir o recurso de fila |
> | Ação | Microsoft. ServiceBus/namespaces/filas/leitura | Obter lista de descrições de recursos de fila |
> | Ação | Microsoft. ServiceBus/namespaces/filas/gravação | Criar ou atualizar as propriedades da fila. |
> | Ação | Microsoft. ServiceBus/namespaces/leitura | Obter a lista de descrição de recurso de namespace |
> | Ação | Microsoft. ServiceBus/namespaces/removeAcsNamepsace/ação | Remover namespace do ACS |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/authorizationRules/Action | Operação para atualizar o tópico. Não há suporte para esta operação na versão de API 2017-04-01. Regras de autorização. Use uma chamada PUT para atualizar a regra de autorização. |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/authorizationRules/Delete | Operação para excluir regras de autorização de tópico |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/authorizationRules/listkeys/Action | Obter a cadeia de conexão para o tópico |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/authorizationRules/Read |  Obter a lista de regras de autorização de tópico |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/authorizationRules/regenerateKeys/Action | Regenerar a chave primária ou secundária para o recurso |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/authorizationRules/Write | Crie regras de autorização de tópico e atualize suas propriedades. Os direitos de acesso das regras de autorização podem ser atualizados. |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/excluir | Operação para excluir o recurso de tópico |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/leitura | Obter lista de descrições de recursos de tópico |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/assinaturas/excluir | Operação para excluir o recurso TopicSubscription |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/assinaturas/leitura | Obter lista de descrições de recursos TopicSubscription |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/assinaturas/regras/excluir | Operação para excluir o recurso de regra |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/assinaturas/regras/leitura | Obter lista de descrições de recursos de regra |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/assinaturas/regras/gravação | Criar ou atualizar propriedades de regra. |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/assinaturas/gravação | Criar ou atualizar Propriedades TopicSubscription. |
> | Ação | Microsoft. ServiceBus/namespaces/tópicos/gravação | Criar ou atualizar propriedades de tópico. |
> | Ação | Microsoft. ServiceBus/namespaces/virtualNetworkRules/Delete | Excluir recurso de regra de VNET |
> | Ação | Microsoft. ServiceBus/namespaces/virtualNetworkRules/Read | Obter recurso de regra de VNET |
> | Ação | Microsoft. ServiceBus/namespaces/virtualNetworkRules/Write | Criar recurso de regra de VNET |
> | Ação | Microsoft. ServiceBus/namespaces/gravação | Crie um recurso de namespace e atualize suas propriedades. As marcas e a capacidade do namespace são as propriedades que podem ser atualizadas. |
> | Ação | Microsoft. ServiceBus/operações/leitura | Obter operações |
> | Ação | Microsoft. ServiceBus/registro/ação | Registra a assinatura para o provedor de recursos do ServiceBus e habilita a criação de recursos do ServiceBus |
> | Ação | Microsoft. ServiceBus/SKU/ler | Obter lista de descrições de recursos de SKU |
> | Ação | Microsoft. ServiceBus/SKU/regiões/leitura | Obter lista de descrições de recursos SkuRegions |
> | Ação | Microsoft. ServiceBus/cancelar registro/ação | Registra a assinatura para o provedor de recursos do ServiceBus e habilita a criação de recursos do ServiceBus |

## <a name="microsoftservicefabric"></a>Microsoft. infabric

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. infabric/clusters/aplicativos/excluir | Excluir qualquer aplicativo |
> | Ação | Microsoft. infabric/clusters/aplicativos/leitura | Ler qualquer aplicativo |
> | Ação | Microsoft. infabric/clusters/aplicativos/serviços/excluir | Excluir qualquer serviço |
> | Ação | Microsoft. infabric/clusters/aplicativos/serviços/partições/leitura | Ler qualquer partição |
> | Ação | Microsoft. infabric/clusters/aplicativos/serviços/partições/réplicas/leitura | Ler qualquer réplica |
> | Ação | Microsoft. infabric/clusters/aplicativos/serviços/leitura | Ler qualquer serviço |
> | Ação | Microsoft. infabric/clusters/aplicativos/serviços/status/leitura | Ler qualquer status de serviço |
> | Ação | Microsoft. infabric/clusters/aplicativos/serviços/gravação | Criar ou atualizar qualquer serviço |
> | Ação | Microsoft. infabric/clusters/aplicativos/gravação | Criar ou atualizar qualquer aplicativo |
> | Ação | Microsoft. perfabric/clusters/applicationTypes/Delete | Excluir qualquer tipo de aplicativo |
> | Ação | Microsoft. infabric/clusters/applicationTypes/Read | Ler qualquer tipo de aplicativo |
> | Ação | Microsoft. infabric/clusters/applicationTypes/versões/excluir | Excluir qualquer versão do tipo de aplicativo |
> | Ação | Microsoft. infabric/clusters/applicationTypes/versões/leitura | Ler qualquer versão do tipo de aplicativo |
> | Ação | Microsoft. infabric/clusters/applicationTypes/versões/gravação | Criar ou atualizar qualquer versão de tipo de aplicativo |
> | Ação | Microsoft. perfabric/clusters/applicationTypes/Write | Criar ou atualizar qualquer tipo de aplicativo |
> | Ação | Microsoft. infabric/clusters/excluir | Excluir qualquer cluster |
> | Ação | Microsoft. infabric/clusters/nós/leitura | Ler qualquer nó |
> | Ação | Microsoft. infabric/clusters/leitura | Ler qualquer cluster |
> | Ação | Microsoft. infabric/clusters/status/leitura | Ler qualquer status de cluster |
> | Ação | Microsoft. infabric/clusters/gravação | Criar ou atualizar qualquer cluster |
> | Ação | Microsoft. infabric/Locations/clusterVersions/Read | Ler qualquer versão do cluster |
> | Ação | Microsoft. infabric/Locations/Environments/clusterVersions/Read | Ler qualquer versão de cluster para um ambiente específico |
> | Ação | Microsoft. infabric/Locations/operationresults/Read | Ler os resultados da operação |
> | Ação | Microsoft. infabric/locais/operações/leitura | Ler todas as operações por local |
> | Ação | Microsoft. infabric/operações/leitura | Ler todas as operações disponíveis |
> | Ação | Microsoft. infabric/registro/ação | Registrar qualquer ação |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. SignalRService/Locations/checknameavailability/Action | Verifica se um nome está disponível para uso com um novo serviço Signalr |
> | Ação | Microsoft. SignalRService/Locations/operationresults/signalr/Read | Consultar o status de uma operação assíncrona |
> | Ação | Microsoft. SignalRService/Locations/operationStatuses/operationId/Read | Consultar o status de uma operação assíncrona |
> | Ação | Microsoft. SignalRService/localizações/usos/leitura | Obter os usos de cota do serviço de Signaler do Azure |
> | Ação | Microsoft. SignalRService/operationresults/Read | Consultar o status de uma operação assíncrona |
> | Ação | Microsoft. SignalRService/operações/leitura | Listar as operações do serviço de Signaler do Azure. |
> | Ação | Microsoft. SignalRService/OperationStatus/Read | Consultar o status de uma operação assíncrona |
> | Ação | Microsoft. SignalRService/registro/ação | Registra o provedor de recursos ' Microsoft. SignalRService ' com uma assinatura |
> | Ação | Microsoft. SignalRService/Signalr/excluir | Excluir o serviço de sinalização inteiro |
> | Ação | Microsoft. SignalRService/Signalr/eventGridFilters/Delete | Excluir um filtro de grade de eventos de um Signalr. |
> | Ação | Microsoft. SignalRService/Signalr/eventGridFilters/Read | Obtenha as propriedades do filtro de grade de eventos especificado ou liste todos os filtros de grade de eventos para o Signalr especificado. |
> | Ação | Microsoft. SignalRService/Signalr/eventGridFilters/Write | Crie ou atualize um filtro de grade de eventos para um Signalr com os parâmetros especificados. |
> | Ação | Microsoft. SignalRService/Signalr/listkeys/Action | Exibir o valor das chaves de acesso do Signalr no portal de gerenciamento ou por meio da API |
> | Ação | Microsoft. SignalRService/Signalr/ler | Exibir as configurações do Signalr e as configurações no portal de gerenciamento ou por meio da API |
> | Ação | Microsoft. SignalRService/Signalr/regeneratekey/Action | Alterar o valor das chaves de acesso do Signalr no portal de gerenciamento ou por meio da API |
> | Ação | Microsoft. SignalRService/Signalr/Restart/Action | Para reiniciar um serviço de Signaler do Azure no portal de gerenciamento ou por meio da API. Haverá determinado tempo de inatividade. |
> | Ação | Microsoft. SignalRService/Signalr/Write | Modificar as configurações do Signalr e as configurações no portal de gerenciamento ou por meio da API |
> | Ação | Microsoft. SignalRService/cancelar registro/ação | Cancela o registro do provedor de recursos ' Microsoft. SignalRService ' com uma assinatura |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Solutions/applicationDefinitions/applicationArtifacts/Read | Lista os artefatos de aplicativo da definição de aplicativo. |
> | Ação | Microsoft. Solutions/applicationDefinitions/Delete | Remove uma definição de aplicativo. |
> | Ação | Microsoft. Solutions/applicationDefinitions/Read | Recupera uma lista de definições de aplicativo. |
> | Ação | Microsoft. Solutions/applicationDefinitions/Write | Adicionar ou modificar uma definição de aplicativo. |
> | Ação | Microsoft. Solutions/Applications/applicationArtifacts/Read | Lista os artefatos de aplicativo. |
> | Ação | Microsoft. Solutions/Applications/Delete | Remove um aplicativo. |
> | Ação | Microsoft. Solutions/Applications/Read | Recupera uma lista de aplicativos. |
> | Ação | Microsoft. Solutions/Applications/refreshPermissions/Action | Atualiza permissão (ões) de aplicativo. |
> | Ação | Microsoft. Solutions/Applications/updateAccess/Action | Atualiza o acesso ao aplicativo. |
> | Ação | Microsoft. Solutions/Applications/Write | Cria uma aplicação. |
> | Ação | Microsoft. Solutions/jitRequests/Delete | Remover um JitRequest |
> | Ação | Microsoft. Solutions/jitRequests/Read | Recupera uma lista de JitRequests |
> | Ação | Microsoft. Solutions/jitRequests/Write | Cria um JitRequest |
> | Ação | Microsoft. Solutions/Locations/operationStatuses/Read | Lê o status da operação para o recurso. |
> | Ação | Microsoft. Solutions/Register/Action | Registre-se em soluções. |
> | Ação | Microsoft. Solutions/cancelar registro/ação | Cancela o registro de soluções. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. SQL/checkNameAvailability/Action | Verifique se o nome do servidor fornecido está disponível para provisionamento em todo o mundo para uma determinada assinatura. |
> | Ação | Microsoft. SQL/instancePools/Delete | Exclui um pool de instâncias |
> | Ação | Microsoft. SQL/instancePools/Read | Obtém um pool de instâncias |
> | Ação | Microsoft. SQL/instancePools/usos/leitura | Obtém as informações de uso do pool de instâncias |
> | Ação | Microsoft. SQL/instancePools/Write | Criar ou atualizar um pool de instâncias |
> | Ação | Microsoft. SQL/Locations/auditingSettingsAzureAsyncOperation/Read | Recuperar o resultado da operação de definição de política de auditoria de blob de servidor estendido |
> | Ação | Microsoft. SQL/Locations/auditingSettingsOperationResults/Read | Recuperar o resultado da operação de definição de política de auditoria de blob do servidor |
> | Ação | Microsoft. SQL/locais/recursos/leitura | Obter os recursos para esta assinatura em um determinado local |
> | Ação | Microsoft. SQL/Locations/databaseAzureAsyncOperation/Read | Obtém o status de uma operação de banco de dados. |
> | Ação | Microsoft. SQL/Locations/databaseOperationResults/Read | Obtém o status de uma operação de banco de dados. |
> | Ação | Microsoft. SQL/Locations/deletedServerAsyncOperation/Read | Obter operações em andamento no servidor excluído |
> | Ação | Microsoft. SQL/Locations/deletedServerOperationResults/Read | Obter operações em andamento no servidor excluído |
> | Ação | Microsoft. SQL/Locations/deletedServers/Read | Retornar a lista de servidores excluídos ou obter as propriedades para o servidor excluído especificado. |
> | Ação | Microsoft. SQL/Locations/deletedServers/Recover/Action | Recuperar um servidor excluído |
> | Ação | Microsoft. SQL/Locations/elasticPoolAzureAsyncOperation/Read | Obtém a operação assíncrona do Azure para uma operação assíncrona de pool elástico |
> | Ação | Microsoft. SQL/Locations/elasticPoolOperationResults/Read | Obtém o resultado de uma operação de pool elástico. |
> | Ação | Microsoft. SQL/Locations/encryptionProtectorAzureAsyncOperation/Read | Obter operações em andamento no protetor de criptografia de criptografia de dados transparente |
> | Ação | Microsoft. SQL/Locations/encryptionProtectorOperationResults/Read | Obter operações em andamento no protetor de criptografia de criptografia de dados transparente |
> | Ação | Microsoft. SQL/Locations/extendedAuditingSettingsAzureAsyncOperation/Read | Recuperar o resultado da operação de definição de política de auditoria de blob de servidor estendido |
> | Ação | Microsoft. SQL/Locations/extendedAuditingSettingsOperationResults/Read | Recuperar o resultado da operação de definição de política de auditoria de blob de servidor estendido |
> | Ação | Microsoft. SQL/Locations/firewallRulesAzureAsyncOperation/Read | Obtém o status de uma operação de regra de firewall. |
> | Ação | Microsoft. SQL/Locations/firewallRulesOperationResults/Read | Obtém o status de uma operação de regra de firewall. |
> | Ação | Microsoft. SQL/Locations/instanceFailoverGroups/Delete | Exclui um grupo de failover de instância existente. |
> | Ação | Microsoft. SQL/Locations/instanceFailoverGroups/failover/ação | Executa o failover planejado em um grupo de failover de instância existente. |
> | Ação | Microsoft. SQL/Locations/instanceFailoverGroups/forceFailoverAllowDataLoss/Action | Executa o failover forçado em um grupo de failover de instância existente. |
> | Ação | Microsoft. SQL/Locations/instanceFailoverGroups/Read | Retorna a lista de grupos de failover de instância ou obtém as propriedades para o grupo de failover de instância especificado. |
> | Ação | Microsoft. SQL/Locations/instanceFailoverGroups/Write | Cria um grupo de failover de instância com os parâmetros especificados ou atualiza as propriedades ou marcas para o grupo de failover de instância especificado. |
> | Ação | Microsoft. SQL/Locations/instancePoolAzureAsyncOperation/Read | Obtém o status de uma operação de pool de instâncias |
> | Ação | Microsoft. SQL/Locations/instancePoolOperationResults/Read | Obtém o resultado de uma operação de pool de instâncias |
> | Ação | Microsoft. SQL/Locations/interfaceEndpointProfileAzureAsyncOperation/Read | Retorna os detalhes de uma operação assíncrona do ponto de extremidade de interface específico do Azure |
> | Ação | Microsoft. SQL/Locations/interfaceEndpointProfileOperationResults/Read | Retorna os detalhes da operação de perfil de ponto de extremidade de interface especificada |
> | Ação | Microsoft. SQL/Locations/jobAgentAzureAsyncOperation/Read | Obtém o status de uma operação de agente de trabalho. |
> | Ação | Microsoft. SQL/Locations/jobAgentOperationResults/Read | Obtém o resultado de uma operação de agente de trabalho. |
> | Ação | Microsoft. SQL/Locations/longTermRetentionBackups/Read | Lista os backups de retenção de longo prazo para cada banco de dados em cada servidor em um local |
> | Ação | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read | Lista os backups de retenção de longo prazo para cada banco de dados em um servidor |
> | Ação | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Delete | Exclui um backup de retenção de longo prazo |
> | Ação | Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Read | Lista os backups de retenção de longo prazo para um banco de dados |
> | Ação | Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/Action | Conclui a operação de restauração do banco de dados gerenciado |
> | Ação | Microsoft. SQL/Locations/managedInstanceEncryptionProtectorAzureAsyncOperation/Read | Obter operações em andamento no protetor de criptografia de instância gerenciada da Transparent Data Encryption |
> | Ação | Microsoft. SQL/Locations/managedInstanceEncryptionProtectorOperationResults/Read | Obter operações em andamento no protetor de criptografia de instância gerenciada da Transparent Data Encryption |
> | Ação | Microsoft. SQL/Locations/managedInstanceKeyAzureAsyncOperation/Read | Obter operações em andamento em chaves de instância gerenciada da Transparent Data Encryption |
> | Ação | Microsoft. SQL/Locations/managedInstanceKeyOperationResults/Read | Obter operações em andamento em chaves de instância gerenciada da Transparent Data Encryption |
> | Ação | Microsoft. SQL/Locations/managedTransparentDataEncryptionAzureAsyncOperation/Read | Obter operações em andamento em Transparent Data Encryption de banco de dados gerenciado |
> | Ação | Microsoft. SQL/Locations/managedTransparentDataEncryptionOperationResults/Read | Obter operações em andamento em Transparent Data Encryption de banco de dados gerenciado |
> | Ação | Microsoft. SQL/Locations/privateEndpointConnectionAzureAsyncOperation/Read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft. SQL/Locations/privateEndpointConnectionOperationResults/Read | Obtém o resultado de uma operação de conexão de ponto de extremidade particular |
> | Ação | Microsoft. SQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft. SQL/Locations/privateEndpointConnectionProxyOperationResults/Read | Obtém o resultado de uma operação de proxy de conexão de ponto de extremidade privada |
> | Ação | Microsoft. SQL/localizações/leitura | Obter os locais disponíveis para uma determinada assinatura |
> | Ação | Microsoft. SQL/Locations/serverKeyAzureAsyncOperation/Read | Obter operações em andamento em chaves de servidor de Transparent Data Encryption |
> | Ação | Microsoft. SQL/Locations/serverKeyOperationResults/Read | Obter operações em andamento em chaves de servidor de Transparent Data Encryption |
> | Ação | Microsoft. SQL/Locations/syncAgentOperationResults/Read | Recuperar o resultado da operação de recurso do agente de sincronização |
> | Ação | Microsoft. SQL/Locations/syncDatabaseIds/Read | Recuperar as IDs de banco de dados de sincronização para uma determinada região e assinatura |
> | Ação | Microsoft. SQL/Locations/syncGroupOperationResults/Read | Recuperar o resultado da operação de recurso do grupo de sincronização |
> | Ação | Microsoft. SQL/Locations/syncMemberOperationResults/Read | Recuperar o resultado da operação de recurso de membro de sincronização |
> | Ação | Microsoft. SQL/localizações/usos/leitura | Obtém uma coleção de métricas de uso para esta assinatura em um local |
> | Ação | Microsoft. SQL/Locations/virtualNetworkRulesAzureAsyncOperation/Read | Retorna os detalhes da operação assíncrona de regras de rede virtual especificada do Azure  |
> | Ação | Microsoft. SQL/Locations/virtualNetworkRulesOperationResults/Read | Retorna os detalhes da operação de regras de rede virtual especificada  |
> | Ação | Microsoft. SQL/managedInstances/Administrators/Delete | Exclui um administrador existente da instância gerenciada. |
> | Ação | Microsoft. SQL/managedInstances/Administrators/Read | Obtém uma lista de administradores de instância gerenciada. |
> | Ação | Microsoft. SQL/managedInstances/Administrators/Write | Cria ou atualiza o administrador da instância gerenciada com os parâmetros especificados. |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/backupShortTermRetentionPolicies/leitura | Obtém uma política de retenção de curto prazo para um banco de dados gerenciado |
> | Ação | Microsoft. SQL/managedInstances/databases/backupShortTermRetentionPolicies/Write | Atualiza uma política de retenção de curto prazo para um banco de dados gerenciado |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/colunas/leitura | Retornar uma lista de colunas para um banco de dados gerenciado |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/currentSensitivityLabels/leitura | Listar rótulos de sensibilidade de um determinado banco de dados |
> | Ação | Microsoft. SQL/managedInstances/databases/currentSensitivityLabels/Write | Rótulos de sensibilidade de atualização do lote |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/excluir | Exclui um banco de dados gerenciado existente |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/provedores/Microsoft. insights/diagnosticSettings/Read | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/provedores/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/provedores/Microsoft. insights/logDefinitions/Read | Obtém os logs disponíveis para bancos de dados de instância gerenciada |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/leitura | Obtém o banco de dados gerenciado existente |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/recommendedSensitivityLabels/leitura | Listar rótulos de sensibilidade de um determinado banco de dados |
> | Ação | Microsoft. SQL/managedInstances/databases/recommendedSensitivityLabels/Write | Rótulos de sensibilidade recomendados da atualização do lote |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/restoreDetails/leitura | Retorna detalhes da restauração do banco de dados gerenciado enquanto a restauração está em andamento. |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/esquemas/leitura | Obtenha um esquema de banco de dados gerenciado. |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/esquemas/tabelas/colunas/leitura | Obter uma coluna de banco de dados gerenciado |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/Delete | Excluir o rótulo de sensibilidade de uma determinada coluna |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/desabilitar/ação | Desabilitar recomendações de sensibilidade em uma determinada coluna |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/habilitar/ação | Habilitar recomendações de sensibilidade em uma determinada coluna |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/leitura | Obter o rótulo de sensibilidade de uma determinada coluna |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/gravação | Criar ou atualizar o rótulo de sensibilidade de uma determinada coluna |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/esquemas/tabelas/leitura | Obter uma tabela de banco de dados gerenciado |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/securityAlertPolicies/leitura | Recuperar uma lista de políticas de detecção de ameaças de banco de dados gerenciadas configuradas para um determinado servidor |
> | Ação | Microsoft. SQL/managedInstances/databases/securityAlertPolicies/Write | Alterar a política de detecção de ameaças do banco de dados para um determinado banco de dados gerenciado |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/securityEvents/leitura | Recupera os eventos de segurança do banco de dados gerenciado |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/sensitivityLabels/leitura | Listar rótulos de sensibilidade de um determinado banco de dados |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/transparentDataEncryption/leitura | Recuperar detalhes do Transparent Data Encryption do banco de dados em um determinado banco de dados gerenciado |
> | Ação | Microsoft. SQL/managedInstances/databases/transparentDataEncryption/Write | Alterar o Transparent Data Encryption do banco de dados para um determinado banco de dados gerenciado |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/vulnerabilityAssessments/Delete | Remover a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/vulnerabilityAssessments/leitura | Recuperar as políticas de avaliação de vulnerabilidade em um givendatabase |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/vulnerabilityAssessments/regras/linhas de base/exclusão | Remover a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/vulnerabilityAssessments/regras/linhas de base/leitura | Obter a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/vulnerabilityAssessments/regras/linhas de base/gravação | Alterar a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/vulnerabilityAssessments/exames/exportação/ação | Converta um resultado de exame existente em um formato legível por humanos. Se já existir, nada acontecerá |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/vulnerabilityAssessments/exames/initiateScan/Action | Executar verificação de banco de dados de avaliação de vulnerabilidade. |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/vulnerabilityAssessments/verificações/leitura | Retorne a lista de registros de verificação de avaliação de vulnerabilidade de banco de dados ou obtenha o registro de verificação para a ID de verificação especificada. |
> | Ação | Microsoft. SQL/managedInstances/databases/vulnerabilityAssessments/Write | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft. SQL/managedInstances/bancos de dados/gravação | Cria um novo banco de dados ou atualiza um banco de dados existente. |
> | Ação | Microsoft. SQL/managedInstances/Delete | Exclui uma instância gerenciada existente. |
> | Ação | Microsoft. SQL/managedInstances/encryptionProtector/Read | Retorna uma lista de protetores de criptografia de servidor ou obtém as propriedades para o protetor de criptografia do servidor especificado. |
> | Ação | Microsoft. SQL/managedInstances/encryptionProtector/revalidate/Action | Atualize as propriedades do protetor de criptografia do servidor especificado. |
> | Ação | Microsoft. SQL/managedInstances/encryptionProtector/Write | Atualize as propriedades do protetor de criptografia do servidor especificado. |
> | Ação | Microsoft. SQL/managedInstances/Keys/Delete | Exclui uma chave de Instância Gerenciada SQL do Azure existente. |
> | Ação | Microsoft. SQL/managedInstances/Keys/Read | Retornar a lista de chaves de instância gerenciada ou obter as propriedades da chave de instância gerenciada especificada. |
> | Ação | Microsoft. SQL/managedInstances/Keys/Write | Cria uma chave com os parâmetros especificados ou atualiza as propriedades ou marcas da chave de instância gerenciada especificada. |
> | Ação | Microsoft. SQL/managedInstances/metricDefinitions/Read | Obter definições de métrica de instância gerenciada |
> | Ação | Microsoft. SQL/managedInstances/métricas/leitura | Obter métricas de instância gerenciada |
> | Ação | Microsoft. SQL/managedInstances/Providers/Microsoft. insights/diagnosticSettings/Read | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. SQL/managedInstances/Providers/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. SQL/managedInstances/Providers/Microsoft. insights/logDefinitions/Read | Obtém os logs disponíveis para instâncias gerenciadas |
> | Ação | Microsoft. SQL/managedInstances/Providers/Microsoft. insights/metricDefinitions/Read | Retornar tipos de métricas que estão disponíveis para instâncias gerenciadas |
> | Ação | Microsoft. SQL/managedInstances/Read | Retornar a lista de instâncias gerenciadas ou obter as propriedades da instância gerenciada especificada. |
> | Ação | Microsoft. SQL/managedInstances/recoverableDatabases/Read | Retorna uma lista de bancos de dados gerenciados recuperáveis |
> | Ação | Microsoft. SQL/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/Read | Obtém uma política de retenção de curto prazo para um banco de dados gerenciado Descartado |
> | Ação | Microsoft. SQL/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/Write | Atualiza uma política de retenção de curto prazo para um banco de dados gerenciado Descartado |
> | Ação | Microsoft. SQL/managedInstances/restorableDroppedDatabases/Read | Retorna uma lista de bancos de dados gerenciados retirados restauráveis. |
> | Ação | Microsoft. SQL/managedInstances/securityAlertPolicies/Read | Recuperar uma lista de políticas de detecção de ameaças do servidor gerenciado configuradas para um determinado servidor |
> | Ação | Microsoft. SQL/managedInstances/securityAlertPolicies/Write | Alterar a política de detecção de ameaças do servidor gerenciado para um determinado servidor gerenciado |
> | Ação | Microsoft. SQL/managedInstances/tdeCertificates/Action | Criar/atualizar certificado TDE |
> | Ação | Microsoft. SQL/managedInstances/vulnerabilityAssessments/Delete | Remover a avaliação de vulnerabilidade para uma determinada instância gerenciada |
> | Ação | Microsoft. SQL/managedInstances/vulnerabilityAssessments/Read | Recuperar as políticas de avaliação de vulnerabilidade em uma determinada instância gerenciada |
> | Ação | Microsoft. SQL/managedInstances/vulnerabilityAssessments/Write | Alterar a avaliação de vulnerabilidade para uma determinada instância gerenciada |
> | Ação | Microsoft. SQL/managedInstances/Write | Cria uma instância gerenciada com os parâmetros especificados ou atualiza as propriedades ou marcas para a instância gerenciada especificada. |
> | Ação | Microsoft. SQL/Operations/Read | Obter operações REST disponíveis |
> | Ação | Microsoft. SQL/privateEndpointConnectionsApproval/Action | Determina se o usuário tem permissão para aprovar uma conexão de ponto de extremidade privada |
> | Ação | Microsoft. SQL/registro/ação | Registra a assinatura para o provedor de recursos do banco de dados SQL da Microsoft e habilita a criação de banco de dados Microsoft SQL. |
> | Ação | Microsoft. SQL/servidores/administradores/excluir | Exclui um objeto de administrador de Azure Active Directory específico |
> | Ação | Microsoft. SQL/Servers/Administrators/Read | Obtém um objeto de administrador de Azure Active Directory específico |
> | Ação | Microsoft. SQL/Servers/Administrators/Write | Adiciona ou atualiza um objeto de administrador de Azure Active Directory específico |
> | Ação | Microsoft. SQL/Servers/Advisor/Read | Retorna a lista de supervisores disponíveis para o servidor |
> | Ação | Microsoft. SQL/Servers/Advisors/recommendedActions/Read | Retorna a lista de ações recomendadas do Orientador especificado para o servidor |
> | Ação | Microsoft. SQL/Servers/Advisors/recommendedActions/Write | Aplicar a ação recomendada no servidor |
> | Ação | Microsoft. SQL/Servers/Advisor/Write | Atualiza o status de execução automática de um supervisor no nível do servidor. |
> | Ação | Microsoft. SQL/Servers/auditingPolicies/Read | Recuperar detalhes da política de auditoria de tabela de servidor padrão configurada em um determinado servidor |
> | Ação | Microsoft. SQL/Servers/auditingPolicies/Write | Alterar a auditoria de tabela de servidor padrão para um determinado servidor |
> | Ação | Microsoft. SQL/Servers/auditingSettings/operationResults/Read | Recuperar o resultado da operação de definição de política de auditoria de blob do servidor |
> | Ação | Microsoft. SQL/Servers/auditingSettings/Read | Recuperar detalhes da política de auditoria de blob do servidor configurada em um determinado servidor |
> | Ação | Microsoft. SQL/Servers/auditingSettings/Write | Alterar a auditoria de blob do servidor para um determinado servidor |
> | Ação | Microsoft. SQL/Servers/automaticTuning/Read | Retorna configurações de ajuste automático para o servidor |
> | Ação | Microsoft. SQL/Servers/automaticTuning/Write | Atualiza as configurações de ajuste automático para o servidor e retorna as configurações atualizadas |
> | Ação | Microsoft. SQL/Servers/backupLongTermRetentionVaults/Delete | Exclui um cofre de arquivamento de backup existente. |
> | Ação | Microsoft. SQL/Servers/backupLongTermRetentionVaults/Read | Esta operação é usada para obter um cofre de retenção de longo prazo de backup. Ele retorna informações sobre o cofre registrado para este servidor |
> | Ação | Microsoft. SQL/Servers/backupLongTermRetentionVaults/Write | Esta operação é usada para registrar um cofre de retenção de longo prazo de backup em um servidor |
> | Ação | Microsoft. SQL/Servers/communicationLinks/Delete | Exclui um link de comunicação do servidor existente. |
> | Ação | Microsoft. SQL/Servers/communicationLinks/Read | Retornar a lista de links de comunicação de um servidor especificado. |
> | Ação | Microsoft. SQL/Servers/communicationLinks/Write | Criar ou atualizar um link de comunicação do servidor. |
> | Ação | Microsoft. SQL/Servers/connectionPolicies/Read | Retornar a lista de políticas de conexão de servidor de um servidor especificado. |
> | Ação | Microsoft. SQL/Servers/connectionPolicies/Write | Criar ou atualizar uma política de conexão do servidor. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/consultores/leitura | Retorna a lista de supervisores disponíveis para o banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/consultores/recommendedActions/leitura | Retorna a lista de ações recomendadas do supervisor especificado para o banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/consultores/recommendedActions/gravação | Aplicar a ação recomendada no banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/consultores/gravação | Atualizar o status de execução automática de um supervisor no nível do banco de dados. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/auditingPolicies/leitura | Recuperar detalhes da política de auditoria de tabela configurada em um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/auditingPolicies/gravação | Alterar a política de auditoria de tabela para um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/auditingSettings/leitura | Recuperar detalhes da política de auditoria de blob configurada em um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/auditingSettings/gravação | Alterar a política de auditoria de BLOB para um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/auditRecords/leitura | Recuperar os registros de auditoria de blob de banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/automaticTuning/leitura | Retorna configurações de ajuste automático para um banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/automaticTuning/gravação | Atualiza as configurações de ajuste automático de um banco de dados e retorna configurações atualizadas |
> | Ação | Microsoft. SQL/servidores/bancos de dados/azureAsyncOperation/leitura | Obtém o status de uma operação de banco de dados. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/backupLongTermRetentionPolicies/leitura | Retornar a lista de políticas de arquivamento de backup de um banco de dados especificado. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/backupLongTermRetentionPolicies/gravação | Criar ou atualizar uma política de arquivamento de backup de banco de dados. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/backupShortTermRetentionPolicies/leitura | Obtém uma política de retenção de curto prazo para um banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/backupShortTermRetentionPolicies/gravação | Atualiza uma política de retenção de curto prazo para um banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/colunas/leitura | Retornar uma lista de colunas para um banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/connectionPolicies/leitura | Recuperar detalhes da política de conexão configurada em um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/connectionPolicies/gravação | Alterar a política de conexão para um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/currentSensitivityLabels/leitura | Listar rótulos de sensibilidade de um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/currentSensitivityLabels/gravação | Rótulos de sensibilidade de atualização do lote |
> | Ação | Microsoft. SQL/servidores/bancos de dados/dataMaskingPolicies/leitura | Retornar a lista de políticas de mascaramento de dados do banco. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/dataMaskingPolicies/regras/excluir | Excluir regra de política de mascaramento de dados para um determinado banco |
> | Ação | Microsoft. SQL/servidores/bancos de dados/dataMaskingPolicies/regras/leitura | Recuperar detalhes da regra de política de mascaramento de dados configurada em um determinado banco de dado |
> | Ação | Microsoft. SQL/servidores/bancos de dados/dataMaskingPolicies/regras/gravação | Alterar regra de política de mascaramento de dados para determinado banco |
> | Ação | Microsoft. SQL/servidores/bancos de dados/dataMaskingPolicies/gravação | Alterar a política de mascaramento de dados para um determinado banco |
> | Ação | Microsoft. SQL/servidores/bancos de dados/dataWarehouseQueries/dataWarehouseQuerySteps/leitura | Retorna as informações da etapa de consulta distribuída da consulta data warehouse para a ID da etapa selecionada |
> | Ação | Microsoft. SQL/servidores/bancos de dados/dataWarehouseQueries/leitura | Retorna as informações de consulta de distribuição de data warehouse para a ID de consulta selecionada |
> | Ação | Microsoft. SQL/servidores/bancos de dados/dataWarehouseUserActivities/leitura | Recupera as atividades do usuário de uma instância de SQL Data Warehouse que inclui consultas em execução e suspensas |
> | Ação | Microsoft. SQL/Servers/bancos de dados/excluir | Exclui um banco de dados existente. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/exportação/ação | Exportar banco de dados SQL do Azure |
> | Ação | Microsoft. SQL/servidores/bancos de dados/extendedAuditingSettings/leitura | Recuperar detalhes da política de auditoria de blob estendida configurada em um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/extendedAuditingSettings/gravação | Alterar a política de auditoria de blob estendido para um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/extensões/leitura | Obtém uma coleção de extensões para o banco de dados. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/extensões/gravação | Alterar a extensão de um determinado banco de dados |
> | Ação | Microsoft. SQL/Servers/bancos de dados/failover/ação | Failover de banco de dados iniciado pelo cliente. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/geoBackupPolicies/leitura | Recuperar políticas de backup geográfico para um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/geoBackupPolicies/gravação | Criar ou atualizar uma política de geobackup de banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/importExportOperationResults/leitura | Obter operações de importação/exportação em andamento |
> | Ação | Microsoft. SQL/servidores/bancos de dados/maintenanceWindowOptions/leitura | Obtém uma lista de janelas de manutenção disponíveis para um banco de dados selecionado. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/maintenanceWindows/leitura | Obtém as configurações do Windows de manutenção para um banco de dados selecionado. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/maintenanceWindows/gravação | Define as configurações do Windows de manutenção para um banco de dados selecionado. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/metricDefinitions/leitura | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/métricas/leitura | Retornar métricas para bancos de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/mover/ação | Altere o nome de um banco de dados existente. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/operationResults/leitura | Obtém o status de uma operação de banco de dados. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/operações/cancelar/ação | Cancela a operação assíncrona pendente do banco de dados SQL do Azure que ainda não foi concluída. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/operações/leitura | Retornar a lista de operações executadas no banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/pausar/ação | Pausar banco de dados de DataWarehouse do SQL do Azure |
> | Ação | Microsoft. SQL/servidores/bancos de dados/provedores/Microsoft. insights/diagnosticSettings/Read | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. SQL/servidores/bancos de dados/provedores/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. SQL/servidores/bancos de dados/provedores/Microsoft. insights/logDefinitions/Read | Obtém os logs disponíveis para bancos de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/provedores/Microsoft. insights/metricDefinitions/Read | Retornar tipos de métricas que estão disponíveis para bancos de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/A querystore/queryTexts/leitura | Retorna a coleção de textos de consulta que correspondem aos parâmetros especificados. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/A querystore/leitura | Retorna os valores atuais das configurações de Repositório de Consultas para o banco de dados. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/A querystore/gravação | Atualiza Repositório de Consultas configuração do banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/leitura | Retornar a lista de bancos de dados ou obter as propriedades do banco de dados especificado. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/recommendedSensitivityLabels/leitura | Listar rótulos de sensibilidade de um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/recommendedSensitivityLabels/gravação | Rótulos de sensibilidade recomendados da atualização do lote |
> | Ação | Microsoft. SQL/servidores/bancos de dados/replicationLinks/Delete | Encerrar a relação de replicação de modo forçado e com potencial perda de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/replicationLinks/failover/ação | Failover após a sincronização de todas as alterações do primário, tornando esse banco de dados na \ u0027s de replicação primário e tornando o primário remoto em um secundário |
> | Ação | Microsoft. SQL/servidores/bancos de dados/replicationLinks/forceFailoverAllowDataLoss/ação | Failover imediatamente com potencial perda de dados, tornando esse banco de dado na \ u0027s de replicação primário e tornando o primário remoto em um secundário |
> | Ação | Microsoft. SQL/servidores/bancos de dados/replicationLinks/leitura | Retornar a lista de links de replicação ou obter as propriedades dos links de replicação especificados. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/replicationLinks/desvincular/ação | Encerrar a relação de replicação de modo forçado ou após a sincronização com o parceiro |
> | Ação | Microsoft. SQL/servidores/bancos de dados/replicationLinks/updateReplicationMode/ação | Atualizar o modo de replicação para o link para o modo síncrono ou assíncrono |
> | Ação | Microsoft. SQL/servidores/bancos de dados/restorePoints/ação | Cria um novo ponto de restauração |
> | Ação | Microsoft. SQL/servidores/bancos de dados/restorePoints/Delete | Exclui um ponto de restauração para o banco de dados. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/restorePoints/leitura | Retorna pontos de restauração para o banco de dados. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/currículo/ação | Continuar banco de dados de DataWarehouse do SQL do Azure |
> | Ação | Microsoft. SQL/servidores/bancos de dados/esquemas/leitura | Obtenha um esquema de banco de dados. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/esquemas/tabelas/colunas/leitura | Obter uma coluna de banco de dados. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/excluir | Excluir o rótulo de sensibilidade de uma determinada coluna |
> | Ação | Microsoft. SQL/servidores/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/desabilitar/ação | Desabilitar recomendações de sensibilidade em uma determinada coluna |
> | Ação | Microsoft. SQL/servidores/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/habilitar/ação | Habilitar recomendações de sensibilidade em uma determinada coluna |
> | Ação | Microsoft. SQL/servidores/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/leitura | Obter o rótulo de sensibilidade de uma determinada coluna |
> | Ação | Microsoft. SQL/servidores/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/gravação | Criar ou atualizar o rótulo de sensibilidade de uma determinada coluna |
> | Ação | Microsoft. SQL/servidores/bancos de dados/esquemas/tabelas/leitura | Obter uma tabela de banco de dados. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/esquemas/tabelas/recommendedIndexes/leitura | Recuperar lista de recomendações de índice em um banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/esquemas/tabelas/recommendedIndexes/gravação | Aplicar recomendação de índice |
> | Ação | Microsoft. SQL/servidores/bancos de dados/securityAlertPolicies/leitura | Recuperar uma lista de políticas de detecção de ameaças de banco de dados configuradas para um determinado servidor |
> | Ação | Microsoft. SQL/servidores/bancos de dados/securityAlertPolicies/gravação | Alterar a política de detecção de ameaças do banco de dados para um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/securityMetrics/leitura | Obter uma coleção de métricas de segurança de banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/sensitivityLabels/leitura | Listar rótulos de sensibilidade de um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/serviceTierAdvisors/leitura | Retornar sugestões sobre o dimensionamento ou redução do banco de dados com base nas estatísticas de execução da consulta para melhorar o desempenho ou reduzir o custo |
> | Ação | Microsoft. SQL/servidores/bancos de dados/SKUs/leitura | Obtém uma coleção de SKUs disponíveis para um banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/cancelSync/ação | Cancelar sincronização do grupo de sincronização |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/Delete | Exclui um grupo de sincronização existente. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/hubSchemas/leitura | Retornar a lista de esquemas de banco de dados do hub de sincronização |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/logs/leitura | Retornar a lista de logs do grupo de sincronização |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/leitura | Retornar a lista de grupos de sincronização ou obter as propriedades do grupo de sincronização especificado. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/refreshHubSchema/ação | Atualizar esquema de banco de dados do hub de sincronização |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/refreshHubSchemaOperationResults/leitura | Recuperar o resultado da operação de atualização do esquema do hub de sincronização |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/syncMembers/Delete | Exclui um membro de sincronização existente. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/syncMembers/leitura | Retornar a lista de membros de sincronização ou obter as propriedades para o membro de sincronização especificado. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/syncMembers/refreshSchema/Action | Atualizar esquema de membro de sincronização |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/syncMembers/refreshSchemaOperationResults/leitura | Recuperar o resultado da operação de atualização de esquema de membro de sincronização |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/syncMembers/esquemas/leitura | Retornar a lista de esquemas de banco de dados do membro de sincronização |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/syncMembers/gravação | Cria um membro de sincronização com os parâmetros especificados ou atualiza as propriedades para o membro de sincronização especificado. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/triggerSync/ação | Disparar a sincronização do grupo de sincronização |
> | Ação | Microsoft. SQL/servidores/bancos de dados/syncGroups/gravação | Cria um grupo de sincronização com os parâmetros especificados ou atualiza as propriedades do grupo de sincronização especificado. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/topQueries/queryText/ação | Retorna o texto Transact-SQL para a ID de consulta selecionada |
> | Ação | Microsoft. SQL/servidores/bancos de dados/topQueries/leitura | Retorna estatísticas de tempo de execução agregadas para a consulta selecionada no período de tempo selecionado |
> | Ação | Microsoft. SQL/servidores/bancos de dados/topQueries/estatísticas/leitura | Retorna estatísticas de tempo de execução agregadas para a consulta selecionada no período de tempo selecionado |
> | Ação | Microsoft. SQL/servidores/bancos de dados/transparentDataEncryption/operationResults/leitura | Obter operações em andamento na Transparent Data Encryption |
> | Ação | Microsoft. SQL/servidores/bancos de dados/transparentDataEncryption/leitura | Recuperar o status e detalhes do recurso de segurança de Transparent Data Encryption para um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/transparentDataEncryption/gravação | Alterar estado de Transparent Data Encryption |
> | Ação | Microsoft. SQL/servidores/bancos de dados/upgradeDataWarehouse/ação | Atualizar banco de dados de DataWarehouse do SQL do Azure |
> | Ação | Microsoft. SQL/servidores/bancos de dados/usos/leitura | Obter as informações de uso do banco de dados SQL do Azure |
> | Ação | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessments/Delete | Remover a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessments/leitura | Recuperar as políticas de avaliação de vulnerabilidade em um givendatabase |
> | Ação | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessments/regras/linhas de base/exclusão | Remover a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessments/regras/linhas de base/leitura | Obter a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessments/regras/linhas de base/gravação | Alterar a linha de base da regra de avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessments/verificação/exportação/ação | Converta um resultado de exame existente em um formato legível por humanos. Se já existir, nada acontecerá |
> | Ação | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessments/exames/initiateScan/ação | Executar verificação de banco de dados de avaliação de vulnerabilidade. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessments/verificações/leitura | Retorne a lista de registros de verificação de avaliação de vulnerabilidade de banco de dados ou obtenha o registro de verificação para a ID de verificação especificada. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessments/gravação | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessmentScans/ação | Executar verificação de banco de dados de avaliação de vulnerabilidade. |
> | Ação | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessmentScans/operationResults/leitura | Recuperar o resultado da operação de execução da verificação de avaliação de vulnerabilidade do banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessmentSettings/leitura | Recuperar detalhes da avaliação de vulnerabilidade configurada em um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/vulnerabilityAssessmentSettings/gravação | Alterar a avaliação de vulnerabilidade para um determinado banco de dados |
> | Ação | Microsoft. SQL/servidores/bancos de dados/gravação | Cria um banco de dados com os parâmetros especificados ou atualiza as propriedades ou marcas do banco de dados especificado. |
> | Ação | Microsoft. SQL/Servers/Delete | Exclui um servidor existente. |
> | Ação | Microsoft. SQL/Servers/disasterRecoveryConfiguration/Delete | Exclui uma configuração de recuperação de desastre existente para um determinado servidor |
> | Ação | Microsoft. SQL/Servers/disasterRecoveryConfiguration/failover/ação | Failover de um DisasterRecoveryConfiguration |
> | Ação | Microsoft. SQL/Servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/Action | Forçar failover de um DisasterRecoveryConfiguration |
> | Ação | Microsoft. SQL/Servers/disasterRecoveryConfiguration/Read | Obtém uma coleção de configurações de recuperação de desastres que incluem este servidor |
> | Ação | Microsoft. SQL/Servers/disasterRecoveryConfiguration/Write | Alterar configuração de recuperação de desastre do servidor |
> | Ação | Microsoft. SQL/Servers/elasticPoolEstimates/Read | Retorna a lista de estimativas de pool elástico já criados para este servidor |
> | Ação | Microsoft. SQL/Servers/elasticPoolEstimates/Write | Cria uma nova estimativa de pool elástico para a lista de bancos de dados fornecidos |
> | Ação | Microsoft. SQL/Servers/elasticPools/Advisor/Read | Retorna a lista de consultores disponíveis para o pool elástico |
> | Ação | Microsoft. SQL/Servers/elasticPools/Advisor/recommendedActions/Read | Retorna a lista de ações recomendadas do supervisor especificado para o pool elástico |
> | Ação | Microsoft. SQL/Servers/elasticPools/Advisor/recommendedActions/Write | Aplicar a ação recomendada no pool elástico |
> | Ação | Microsoft. SQL/Servers/elasticPools/Advisor/Write | Atualizar o status de execução automática de um supervisor no nível do pool elástico. |
> | Ação | Microsoft. SQL/Servers/elasticPools/bancos de dados/leitura | Obter uma lista de bancos de dados para um pool elástico |
> | Ação | Microsoft. SQL/Servers/elasticPools/Delete | Excluir pool elástico existente |
> | Ação | Microsoft. SQL/Servers/elasticPools/elasticPoolActivity/Read | Recuperar atividades e detalhes em um determinado pool de banco de dados elástico |
> | Ação | Microsoft. SQL/Servers/elasticPools/elasticPoolDatabaseActivity/Read | Recuperar atividades e detalhes em um determinado banco de dados que faz parte do pool de banco de dados elástico |
> | Ação | Microsoft. SQL/Servers/elasticPools/failover/ação | Failover de pool elástico iniciado pelo cliente. |
> | Ação | Microsoft. SQL/Servers/elasticPools/metricDefinitions/Read | Retornar tipos de métricas que estão disponíveis para pools de banco de dados elástico |
> | Ação | Microsoft. SQL/Servers/elasticPools/métricas/leitura | Retornar métricas para pools de banco de dados elástico |
> | Ação | Microsoft. SQL/Servers/elasticPools/Operations/cancelamento/ação | Cancela a operação assíncrona pendente do pool elástico do SQL do Azure que ainda não foi concluída. |
> | Ação | Microsoft. SQL/Servers/elasticPools/operações/leitura | Retornar a lista de operações executadas no pool elástico |
> | Ação | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. insights/diagnosticSettings/Read | Obtém a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. insights/diagnosticSettings/Write | Cria ou atualiza a configuração de diagnóstico para o recurso |
> | Ação | Microsoft. SQL/Servers/elasticPools/Providers/Microsoft. insights/metricDefinitions/Read | Retornar tipos de métricas que estão disponíveis para pools de banco de dados elástico |
> | Ação | Microsoft. SQL/Servers/elasticPools/Read | Recuperar detalhes do pool elástico em um determinado servidor |
> | Ação | Microsoft. SQL/Servers/elasticPools/SKUs/Read | Obtém uma coleção de SKUs disponíveis para um pool elástico |
> | Ação | Microsoft. SQL/Servers/elasticPools/Write | Criar uma nova propriedade ou alterar as propriedades de um pool elástico existente |
> | Ação | Microsoft. SQL/Servers/encryptionProtector/Read | Retorna uma lista de protetores de criptografia de servidor ou obtém as propriedades para o protetor de criptografia do servidor especificado. |
> | Ação | Microsoft. SQL/Servers/encryptionProtector/revalidate/Action | Atualize as propriedades do protetor de criptografia do servidor especificado. |
> | Ação | Microsoft. SQL/Servers/encryptionProtector/Write | Atualize as propriedades do protetor de criptografia do servidor especificado. |
> | Ação | Microsoft. SQL/Servers/extendedAuditingSettings/Read | Recuperar detalhes da política de auditoria de blob de servidor estendida configurada em um determinado servidor |
> | Ação | Microsoft. SQL/Servers/extendedAuditingSettings/Write | Alterar a auditoria de blob de servidor estendida para um determinado servidor |
> | Ação | Microsoft. SQL/Servers/failoverGroups/Delete | Exclui um grupo de failover existente. |
> | Ação | Microsoft. SQL/Servers/failoverGroups/failover/ação | Executa o failover planejado em um grupo de failover existente. |
> | Ação | Microsoft. SQL/Servers/failoverGroups/forceFailoverAllowDataLoss/Action | Executa o failover forçado em um grupo de failover existente. |
> | Ação | Microsoft. SQL/Servers/failoverGroups/Read | Retorna a lista de grupos de failover ou obtém as propriedades para o grupo de failover especificado. |
> | Ação | Microsoft. SQL/Servers/failoverGroups/Write | Cria um grupo de failover com os parâmetros especificados ou atualiza as propriedades ou marcas para o grupo de failover especificado. |
> | Ação | Microsoft. SQL/Servers/firewallRules/Delete | Exclui uma regra de firewall de servidor existente. |
> | Ação | Microsoft. SQL/Servers/firewallRules/Read | Retornar a lista de regras de firewall do servidor ou obter as propriedades da regra de firewall do servidor especificada. |
> | Ação | Microsoft. SQL/Servers/firewallRules/Write | Cria uma regra de firewall de servidor com os parâmetros especificados, atualiza as propriedades da regra especificada ou substitui todas as regras existentes por novas regras de firewall do servidor. |
> | Ação | Microsoft. SQL/Servers/importar/ação | Criar um novo banco de dados no servidor e implantar o esquema e o dado de um pacote DacPac |
> | Ação | Microsoft. SQL/Servers/importExportOperationResults/Read | Obter operações de importação/exportação em andamento |
> | Ação | Microsoft. SQL/Servers/interfaceEndpointProfiles/Delete | Exclui o perfil de ponto de extremidade de interface especificado |
> | Ação | Microsoft. SQL/Servers/interfaceEndpointProfiles/Read | Retorna as propriedades do perfil de ponto de extremidade de interface especificado |
> | Ação | Microsoft. SQL/Servers/interfaceEndpointProfiles/Write | Cria um perfil de ponto de extremidade de interface com os parâmetros especificados ou atualiza as propriedades ou marcas para o ponto de extremidade de interface especificado |
> | Ação | Microsoft. SQL/Servers/jobAgents/Delete | Exclui um agente de trabalho do BD SQL do Azure |
> | Ação | Microsoft. SQL/Servers/jobAgents/Read | Obter um agente de trabalho do BD SQL do Azure |
> | Ação | Microsoft. SQL/Servers/jobAgents/Write | Criar ou atualizar um agente de trabalho do BD SQL do Azure |
> | Ação | Microsoft. SQL/Servers/Keys/Delete | Exclui uma chave de servidor existente. |
> | Ação | Microsoft. SQL/Servers/Keys/Read | Retornar a lista de chaves de servidor ou obter as propriedades para a chave de servidor especificada. |
> | Ação | Microsoft. SQL/Servers/Keys/Write | Cria uma chave com os parâmetros especificados ou atualiza as propriedades ou marcas para a chave de servidor especificada. |
> | Ação | Microsoft. SQL/Servers/operationResults/Read | Obter operações de servidor em andamento |
> | Ação | Microsoft. SQL/Servers/privateEndpointConnectionProxies/Delete | Exclui um proxy de conexão de ponto de extremidade privado existente |
> | Ação | Microsoft. SQL/Servers/privateEndpointConnectionProxies/Read | Retorna a lista de proxies de conexão de ponto de extremidade privado ou obtém as propriedades para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft. SQL/Servers/privateEndpointConnectionProxies/Validate/Action | Valida uma conexão de ponto de extremidade privada criar chamada do lado do NRP |
> | Ação | Microsoft. SQL/Servers/privateEndpointConnectionProxies/Write | Cria um proxy de conexão de ponto de extremidade privado com os parâmetros especificados ou atualiza as propriedades ou marcas para o proxy de conexão de ponto de extremidade particular especificado. |
> | Ação | Microsoft. SQL/Servers/privateEndpointConnections/Delete | Exclui uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft. SQL/Servers/privateEndpointConnections/Read | Retorna a lista de conexões de ponto de extremidade privado ou obtém as propriedades para a conexão de ponto de extremidade particular especificada. |
> | Ação | Microsoft. SQL/Servers/privateEndpointConnections/Write | Aprova ou rejeita uma conexão de ponto de extremidade privada existente |
> | Ação | Microsoft. SQL/Servers/privateEndpointConnectionsApproval/Action | Determina se o usuário tem permissão para aprovar uma conexão de ponto de extremidade privada |
> | Ação | Microsoft. SQL/Servers/privateLinkResources/Read | Obter os recursos de link privado para o SQL Server correspondente |
> | Ação | Microsoft. SQL/Servers/Providers/Microsoft. insights/metricDefinitions/Read | Retornar tipos de métricas que estão disponíveis para servidores |
> | Ação | Microsoft. SQL/Servers/Read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Ação | Microsoft. SQL/Servers/recommendedElasticPools/bancos de dados/leitura | Recuperar métricas para pools de banco de dados elástico recomendados para um determinado servidor |
> | Ação | Microsoft. SQL/Servers/recommendedElasticPools/Read | Recupere a recomendação para pools de banco de dados elástico para reduzir o custo ou melhorar o desempenho com base na utilização histórica de recursos |
> | Ação | Microsoft. SQL/Servers/recoverableDatabases/Read | Esta operação é usada para recuperação de desastre do banco de dados em tempo real para restaurar o banco de dados para o último ponto de backup bom conhecido. Ele retorna informações sobre o último backup bom, mas ele doesn\u0027t realmente restaurar o banco de dados. |
> | Ação | Microsoft. SQL/Servers/replicationLinks/Read | Retornar a lista de links de replicação ou obter as propriedades dos links de replicação especificados. |
> | Ação | Microsoft. SQL/Servers/restorableDroppedDatabases/Read | Obtenha uma lista de bancos de dados que foram descartados em um determinado servidor que ainda estão na política de retenção. |
> | Ação | Microsoft. SQL/Servers/securityAlertPolicies/operationResults/Read | Recuperar resultados da operação de gravação da política de detecção de ameaças do servidor |
> | Ação | Microsoft. SQL/Servers/securityAlertPolicies/Read | Recuperar uma lista de políticas de detecção de ameaças do servidor configuradas para um determinado servidor |
> | Ação | Microsoft. SQL/Servers/securityAlertPolicies/Write | Alterar a política de detecção de ameaças do servidor de um determinado servidor |
> | Ação | Microsoft. SQL/Servers/perobjections/Read | Recuperar a lista de objetivos de nível de serviço (também conhecidos como camadas de desempenho) disponíveis em um determinado servidor |
> | Ação | Microsoft. SQL/Servers/syncAgents/Delete | Exclui um agente de sincronização existente. |
> | Ação | Microsoft. SQL/Servers/syncAgents/generateKey/Action | Gerar chave de registro do agente de sincronização |
> | Ação | Microsoft. SQL/Servers/syncAgents/linkedDatabases/Read | Retornar a lista de bancos de dados vinculados do agente de sincronização |
> | Ação | Microsoft. SQL/Servers/syncAgents/Read | Retornar a lista de agentes de sincronização ou obter as propriedades para o agente de sincronização especificado. |
> | Ação | Microsoft. SQL/Servers/syncAgents/Write | Cria um agente de sincronização com os parâmetros especificados ou atualiza as propriedades para o agente de sincronização especificado. |
> | Ação | Microsoft. SQL/Servers/tdeCertificates/Action | Criar/atualizar certificado TDE |
> | Ação | Microsoft. SQL/servidores/usos/leitura | Retornar a cota de DTU do servidor e o consumo atual de DTU por todos os bancos de dados no servidor |
> | Ação | Microsoft. SQL/Servers/virtualNetworkRules/Delete | Exclui uma regra de rede virtual existente |
> | Ação | Microsoft. SQL/Servers/virtualNetworkRules/Read | Retornar a lista de regras de rede virtual ou obter as propriedades da regra de rede virtual especificada. |
> | Ação | Microsoft. SQL/Servers/virtualNetworkRules/Write | Cria uma regra de rede virtual com os parâmetros especificados ou atualiza as propriedades ou marcas para a regra de rede virtual especificada. |
> | Ação | Microsoft. SQL/Servers/vulnerabilityAssessments/Delete | Remover a avaliação de vulnerabilidade para um determinado servidor |
> | Ação | Microsoft. SQL/Servers/vulnerabilityAssessments/Read | Recuperar as políticas de avaliação de vulnerabilidade em um determinado servidor |
> | Ação | Microsoft. SQL/Servers/vulnerabilityAssessments/Write | Alterar a avaliação de vulnerabilidade para um determinado servidor |
> | Ação | Microsoft. SQL/Servers/Write | Cria um servidor com os parâmetros especificados ou atualiza as propriedades ou marcas para o servidor especificado. |
> | Ação | Microsoft. SQL/Cancelar registro/ação | Cancela o registro da assinatura para o provedor de recursos do banco de dados SQL da Microsoft e habilita a criação de banco de dados Microsoft SQL. |
> | Ação | Microsoft. SQL/virtualClusters/Delete | Exclui um cluster virtual existente. |
> | Ação | Microsoft. SQL/virtualClusters/Read | Retornar a lista de clusters virtuais ou obter as propriedades para o cluster virtual especificado. |
> | Ação | Microsoft. SQL/virtualClusters/Write | Atualiza as marcas do cluster virtual. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Storage/checknameavailability/Read | Verifica se o nome da conta é válido e não está em uso. |
> | Ação | Microsoft. Storage/Locations/checknameavailability/Read | Verifica se o nome da conta é válido e não está em uso. |
> | Ação | Microsoft. Storage/Locations/deleteVirtualNetworkOrSubnets/Action | Notifica o Microsoft. Storage que a rede virtual ou sub-rede está sendo excluída |
> | Ação | Microsoft. Storage/locais/usos/leitura | Retorna o limite e a contagem de uso atual dos recursos na assinatura especificada |
> | Ação | Microsoft. Storage/Operations/Read | Sonda o status de uma operação assíncrona. |
> | Ação | Microsoft. Storage/registro/ação | Registra a assinatura para o provedor de recursos de armazenamento e habilita a criação de contas de armazenamento. |
> | Ação | Microsoft. Storage/SKUs/Read | Lista os SKUs com suporte no Microsoft. Storage. |
> | Dataaction | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/Adicionar/ação | Retorna o resultado da adição de conteúdo de BLOB |
> | Dataaction | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/excluir | Retorna o resultado da exclusão de um blob |
> | Dataaction | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/deleteAutomaticSnapshot/ação | Retorna o resultado da exclusão de um instantâneo automático |
> | Dataaction | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/filtro/ação | Retorna a lista de BLOBs em uma conta com filtro de marcas correspondentes |
> | Dataaction | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/leitura | Retorna um BLOB ou uma lista de BLOBs |
> | Dataaction | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/runAsSuperUser/ação | Retorna o resultado do comando blob |
> | Dataaction | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/marcas/leitura | Retorna o resultado da leitura de marcas de BLOB |
> | Dataaction | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/marcas/gravação | Retorna o resultado da gravação de marcas de BLOB |
> | Dataaction | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/gravação | Retorna o resultado da gravação de um blob |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/containers/clearLegalHold/Action | Limpar a retenção legal do contêiner de BLOB |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/containers/Delete | Retorna o resultado da exclusão de um contêiner |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/containers/immutabilityPolicies/Delete | Excluir a política de imutabilidade do contêiner de BLOBs |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/containers/immutabilityPolicies/Extend/Action | Estender a política de imutabilidade do contêiner de BLOBs |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/containers/immutabilityPolicies/Lock/Action | Bloquear a política de imutabilidade do contêiner de BLOB |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/containers/immutabilityPolicies/Read | Obter política de imutabilidade do contêiner de BLOB |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/containers/immutabilityPolicies/Write | Colocar a política de imutabilidade do contêiner de BLOBs |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/contêineres/concessão/ação | Retorna o resultado do contêiner de blob de concessão |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/contêineres/leitura | Retorna um contêiner |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/contêineres/leitura | Retorna a lista de contêineres |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/containers/setLegalHold/Action | Definir retenção legal de contêiner de BLOB |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/containers/Write | Retorna o resultado do contêiner de blob de patch |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/containers/Write | Retorna o resultado do contêiner de blob put |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey/Action | Retorna uma chave de delegação de usuário para o serviço blob |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/leitura |  |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/leitura | Retorna propriedades ou estatísticas do serviço blob |
> | Ação | Microsoft. Storage/storageAccounts/blobservices/Write | Retorna o resultado das propriedades do serviço de blob put |
> | Ação | Microsoft. Storage/storageAccounts/Delete | Exclui uma conta de armazenamento existente. |
> | Ação | Microsoft. Storage/storageAccounts/encryptionScopes/Read |  |
> | Ação | Microsoft. Storage/storageAccounts/encryptionScopes/Write |  |
> | Ação | Microsoft. Storage/storageAccounts/failover/ação | O cliente é capaz de controlar o failover em caso de problemas de disponibilidade |
> | Dataaction | Microsoft. Storage/storageAccounts/fileservices/arquivo compartilhado/arquivos/actassuperuser/Action | Obter privilégios de administrador de arquivos |
> | Dataaction | Microsoft. Storage/storageAccounts/fileservices/arquivos/compartilhamento/exclusão | Retorna o resultado da exclusão de um arquivo/pasta |
> | Dataaction | Microsoft. Storage/storageAccounts/fileservices/arquivo compartilhado/arquivos/modifypermissions/Action | Retorna o resultado da permissão de modificação em um arquivo/pasta |
> | Dataaction | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/arquivos/leitura | Retorna um arquivo/pasta ou uma lista de arquivos/pastas |
> | Dataaction | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/arquivos/gravação | Retorna o resultado da gravação de um arquivo ou da criação de uma pasta |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/Read |  |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/Read | Obter propriedades do serviço de arquivo |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/shares/Delete |  |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/leitura |  |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/leitura |  |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/shares/Write |  |
> | Ação | Microsoft. Storage/storageAccounts/fileservices/Write |  |
> | Ação | Microsoft. Storage/storageAccounts/listAccountSas/Action | Retorna o token SAS da conta para a conta de armazenamento especificada. |
> | Ação | Microsoft. Storage/storageAccounts/listkeys/Action | Retorna as chaves de acesso para a conta de armazenamento especificada. |
> | Ação | Microsoft. Storage/storageAccounts/listServiceSas/Action | Retorna o token SAS do serviço para a conta de armazenamento especificada. |
> | Ação | Microsoft. Storage/storageAccounts/managementPolicies/Delete | Excluir políticas de gerenciamento de conta de armazenamento |
> | Ação | Microsoft. Storage/storageAccounts/managementPolicies/Read | Obter políticas de conta de gerenciamento de armazenamento |
> | Ação | Microsoft. Storage/storageAccounts/managementPolicies/Write | Políticas de gerenciamento de conta de armazenamento put |
> | Ação | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Delete |  |
> | Ação | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Read |  |
> | Ação | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Write |  |
> | Ação | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Delete | Excluir proxies de conexão de ponto de extremidade privado |
> | Ação | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Read | Obter proxy de conexão de ponto de extremidade privado |
> | Ação | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Write | Colocar proxies de conexão de ponto de extremidade privado |
> | Ação | Microsoft. Storage/storageAccounts/privateEndpointConnections/Delete | Excluir conexão de ponto de extremidade particular |
> | Ação | Microsoft. Storage/storageAccounts/privateEndpointConnections/Read | Obter conexão de ponto de extremidade privado |
> | Ação | Microsoft. Storage/storageAccounts/privateEndpointConnections/Write | Colocar conexão de ponto de extremidade particular |
> | Ação | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/Action | Aprovar conexões de ponto de extremidade privado |
> | Ação | Microsoft. Storage/storageAccounts/privateLinkResources/Read | Obter StorageAccount groupids |
> | Ação | Microsoft. Storage/storageAccounts/queueservices/filas/excluir | Retorna o resultado da exclusão de uma fila |
> | Dataaction | Microsoft. Storage/storageAccounts/queueservices/filas/mensagens/Adicionar/ação | Retorna o resultado da adição de uma mensagem |
> | Dataaction | Microsoft. Storage/storageAccounts/queueservices/filas/mensagens/excluir | Retorna o resultado da exclusão de uma mensagem |
> | Dataaction | Microsoft. Storage/storageAccounts/queueservices/filas/mensagens/processo/ação | Retorna o resultado do processamento de uma mensagem |
> | Dataaction | Microsoft. Storage/storageAccounts/queueservices/filas/mensagens/leitura | Retorna uma mensagem |
> | Dataaction | Microsoft. Storage/storageAccounts/queueservices/filas/mensagens/gravação | Retorna o resultado da gravação de uma mensagem |
> | Ação | Microsoft. Storage/storageAccounts/queueservices/filas/leitura | Retorna uma fila ou uma lista de filas. |
> | Ação | Microsoft. Storage/storageAccounts/queueservices/filas/gravação | Retorna o resultado da gravação de uma fila |
> | Ação | Microsoft. Storage/storageAccounts/queueservices/leitura | Obter serviço Fila Propriedades |
> | Ação | Microsoft. Storage/storageAccounts/queueservices/leitura | Retorna propriedades ou estatísticas do serviço fila. |
> | Ação | Microsoft. Storage/storageAccounts/queueservices/Write | Retorna o resultado da configuração das propriedades do serviço fila |
> | Ação | Microsoft. Storage/storageAccounts/Read | Retorna a lista de contas de armazenamento ou obtém as propriedades da conta de armazenamento especificada. |
> | Ação | Microsoft. Storage/storageAccounts/regeneratekey/Action | Regenera as chaves de acesso para a conta de armazenamento especificada. |
> | Ação | Microsoft. Storage/storageAccounts/restoreBlobRanges/Action | Restaurar intervalos de BLOB para o estado do tempo especificado |
> | Ação | Microsoft. Storage/storageAccounts/revokeUserDelegationKeys/Action | Revoga todas as chaves de delegação de usuário para a conta de armazenamento especificada. |
> | Ação | Microsoft. Storage/storageAccounts/Services/diagnosticSettings/Write | Criar/atualizar configurações de diagnóstico da conta de armazenamento. |
> | Ação | Microsoft. Storage/storageAccounts/tabelaservices/leitura | Obter propriedades do serviço tabela |
> | Ação | Microsoft. Storage/storageAccounts/Write | Cria uma conta de armazenamento com os parâmetros especificados ou atualiza as propriedades ou marcas ou adiciona um domínio personalizado para a conta de armazenamento especificada. |
> | Ação | Microsoft. Storage/Usages/Read | Retorna o limite e a contagem de uso atual dos recursos na assinatura especificada |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. storagesync/Locations/checkNameAvailability/Action | Verifica se o nome do serviço de sincronização de armazenamento é válido e não está em uso. |
> | Ação | Microsoft. storagesync/locais/fluxos de trabalho/operações/leitura | Obtém o status de uma operação assíncrona |
> | Ação | Microsoft. storagesync/operações/leitura | Obtém uma lista das operações com suporte |
> | Ação | Microsoft. storagesync/registro/ação | Registra a assinatura para o provedor de sincronização de armazenamento |
> | Ação | Microsoft. storagesync/storageSyncServices/Delete | Excluir qualquer serviço de sincronização de armazenamento |
> | Ação | Microsoft. storagesync/storageSyncServices/Providers/Microsoft. insights/metricDefinitions/Read | Obter as métricas disponíveis para os serviços de sincronização de armazenamento |
> | Ação | Microsoft. storagesync/storageSyncServices/Read | Ler serviços de sincronização de armazenamento |
> | Ação | Microsoft. storagesync/storageSyncServices/registeredServers/Delete | Excluir qualquer servidor registrado |
> | Ação | Microsoft. storagesync/storageSyncServices/registeredServers/Providers/Microsoft. insights/metricDefinitions/Read | Obter as métricas disponíveis para o servidor registrado |
> | Ação | Microsoft. storagesync/storageSyncServices/registeredServers/Read | Ler qualquer servidor registrado |
> | Ação | Microsoft. storagesync/storageSyncServices/registeredServers/Write | Criar ou atualizar qualquer servidor registrado |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/Delete | Excluir qualquer ponto de extremidade de nuvem |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/Read | Obtém o status de uma operação de backup/restauração assíncrona |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/rebackup/ação | Chamar esta ação após o backup |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/createrestore/Action | Chamar esta ação após a restauração |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/ação | Chamar esta ação antes do backup |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/Action | Chamar esta ação antes da restauração |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/Read | Ler pontos de extremidade de nuvem |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/Action | Restaurar pulsação |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/gravação | Criar ou atualizar pontos de extremidade de nuvem |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/Delete | Excluir todos os grupos de sincronização |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/Providers/Microsoft. insights/metricDefinitions/Read | Obter as métricas disponíveis para grupos de sincronização |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/Read | Ler todos os grupos de sincronização |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Delete | Excluir qualquer ponto de extremidade do servidor |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Providers/Microsoft. insights/metricDefinitions/Read | Obter as métricas disponíveis para pontos de extremidade do servidor |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Read | Ler qualquer ponto de extremidade do servidor |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/recallaction/Action | Chamar esta ação para recuperar arquivos em um servidor |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/gravação | Criar ou atualizar pontos de extremidade de servidor |
> | Ação | Microsoft. storagesync/storageSyncServices/syncGroups/Write | Criar ou atualizar grupos de sincronização |
> | Ação | Microsoft. storagesync/storageSyncServices/fluxos de trabalho/operationresults/leitura | Obtém o status de uma operação assíncrona |
> | Ação | Microsoft. storagesync/storageSyncServices/fluxos de trabalho/operações/leitura | Obtém o status de uma operação assíncrona |
> | Ação | Microsoft. storagesync/storageSyncServices/fluxos de trabalho/leitura | Ler fluxos de trabalho |
> | Ação | Microsoft. storagesync/storageSyncServices/Write | Criar ou atualizar serviços de sincronização de armazenamento |
> | Ação | Microsoft. storagesync/cancelar registro/ação | Cancela o registro da assinatura para o provedor de sincronização de armazenamento |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. StorSimple/Managers/accessControlRecords/Delete | Exclui os registros de controle de acesso |
> | Ação | Microsoft. StorSimple/Managers/accessControlRecords/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/accessControlRecords/Read | Listar ou obter os registros de controle de acesso |
> | Ação | Microsoft. StorSimple/Managers/accessControlRecords/Write | Criar ou atualizar os registros de controle de acesso |
> | Ação | Microsoft. StorSimple/Managers/Alerts/Read | Listar ou obter os alertas |
> | Ação | Microsoft. StorSimple/Managers/backups/ler | Listar ou obter o conjunto de backup |
> | Ação | Microsoft. StorSimple/Managers/bandwidthSettings/Delete | Exclui as configurações de largura de banda existentes (somente série 8000) |
> | Ação | Microsoft. StorSimple/Managers/bandwidthSettings/operationResults/Read | Listar os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/bandwidthSettings/Read | Listar as configurações de largura de banda (somente série 8000) |
> | Ação | Microsoft. StorSimple/Managers/bandwidthSettings/Write | Cria uma nova ou atualiza as configurações de largura de banda (somente série 8000) |
> | Ação | Microsoft. StorSimple/Managers/Certificates/Write | Criar ou atualizar os certificados |
> | Ação | Microsoft. StorSimple/Managers/Certificates/Write | A operação atualizar certificado de recurso atualiza o certificado de credencial do cofre/recurso. |
> | Ação | Microsoft. StorSimple/Managers/clearAlerts/Action | Desmarque todos os alertas associados ao Gerenciador de dispositivos. |
> | Ação | Microsoft. StorSimple/Managers/cloudApplianceConfigurations/Read | Listar as configurações com suporte do dispositivo de nuvem |
> | Ação | Microsoft. StorSimple/Managers/configureDevice/Action | Configura um dispositivo |
> | Ação | Microsoft. StorSimple/Managers/Delete | Exclui os gerenciadores de dispositivos |
> | Ação | Microsoft. StorSimple/Managers/Delete | A operação excluir cofre exclui o recurso do Azure especificado do tipo ' cofre ' |
> | Ação | Microsoft. StorSimple/Managers/Devices/alertSettings/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/alertSettings/Read | Listar ou obter as configurações de alerta |
> | Ação | Microsoft. StorSimple/Managers/Devices/alertSettings/Write | Criar ou atualizar as configurações de alerta |
> | Ação | Microsoft. StorSimple/Managers/Devices/authorizeForServiceEncryptionKeyRollover/Action | Autorizar a substituição da chave de criptografia de serviço dos dispositivos |
> | Ação | Microsoft. StorSimple/Managers/Devices/backupPolicies/backup/ação | Faça um backup manual para criar um backup sob demanda de todos os volumes protegidos pela política. |
> | Ação | Microsoft. StorSimple/Managers/Devices/backupPolicies/Delete | Excluir políticas de backup existentes (somente série 8000) |
> | Ação | Microsoft. StorSimple/Managers/Devices/backupPolicies/operationResults/Read | Listar os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/backupPolicies/Read | Listar as políticas de backup (somente série 8000) |
> | Ação | Microsoft. StorSimple/Managers/Devices/backupPolicies/Schedule/Delete | Exclui um agendamento existente |
> | Ação | Microsoft. StorSimple/Managers/Devices/backupPolicies/Schedules/operationResults/Read | Listar os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/backupPolicies/Schedules/Read | Listar as agendas |
> | Ação | Microsoft. StorSimple/Managers/Devices/backupPolicies/Schedules/Write | Cria um novo ou atualiza agendamentos |
> | Ação | Microsoft. StorSimple/Managers/Devices/backupPolicies/Write | Cria uma nova ou atualiza as políticas de backup (somente série 8000) |
> | Ação | Microsoft. StorSimple/Managers/Devices/backups/Delete | Exclui o conjunto de backup |
> | Ação | Microsoft. StorSimple/Managers/Devices/backups/Elements/clone/Action | Clone um compartilhamento ou volume usando um elemento de backup. |
> | Ação | Microsoft. StorSimple/Managers/Devices/backups/Elements/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/backups/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/backups/Read | Listar ou obter o conjunto de backup |
> | Ação | Microsoft. StorSimple/Managers/Devices/backups/restore/Action | Restaure todos os volumes de um conjunto de backup. |
> | Ação | Microsoft. StorSimple/Managers/Devices/backupScheduleGroups/Delete | Exclui os grupos de agendamento de backup |
> | Ação | Microsoft. StorSimple/Managers/Devices/backupScheduleGroups/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/backupScheduleGroups/Read | Listar ou obter os grupos de agendamento de backup |
> | Ação | Microsoft. StorSimple/Managers/Devices/backupScheduleGroups/Write | Criar ou atualizar os grupos de agendamento de backup |
> | Ação | Microsoft. StorSimple/Managers/Devices/chapSettings/Delete | Exclui as configurações de CHAP |
> | Ação | Microsoft. StorSimple/Managers/Devices/chapSettings/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/chapSettings/Read | Listar ou obter as configurações de CHAP |
> | Ação | Microsoft. StorSimple/Managers/Devices/chapSettings/Write | Criar ou atualizar as configurações de CHAP |
> | Ação | Microsoft. StorSimple/Managers/Devices/Deactivate/Action | Desativa um dispositivo. |
> | Ação | Microsoft. StorSimple/Managers/Devices/Delete | Exclui os dispositivos |
> | Ação | Microsoft. StorSimple/Managers/dispositivos/discos/leitura | Listar ou obter os discos |
> | Ação | Microsoft. StorSimple/Managers/Devices/Download/ação | Baixar atualizações para um dispositivo. |
> | Ação | Microsoft. StorSimple/Managers/Devices/failover/Action | Failover do dispositivo. |
> | Ação | Microsoft. StorSimple/Managers/Devices/failover/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/failoverTargets/Read | Listar ou obter os destinos de failover dos dispositivos |
> | Ação | Microsoft. StorSimple/Managers/Devices/servidor de servidores/backup/ação | Faça backup de um servidor de arquivos. |
> | Ação | Microsoft. StorSimple/Managers/Devices/servidores de servidor/excluir | Exclui os servidores de arquivos |
> | Ação | Microsoft. StorSimple/Managers/Devices/servidores de servidor/métricas/leitura | Listar ou obter as métricas |
> | Ação | Microsoft. StorSimple/Managers/Devices/serverfiles/metricsDefinitions/Read | Listar ou obter as definições de métricas |
> | Ação | Microsoft. StorSimple/Managers/Devices/serverfiles/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/servidores de servidor/leitura | Listar ou obter os servidores de arquivos |
> | Ação | Microsoft. StorSimple/Managers/Devices/servidores de servidor/compartilhamentos/exclusão | Exclui os compartilhamentos |
> | Ação | Microsoft. StorSimple/Managers/Devices/Server/compartilhamentos/métricas/leitura | Listar ou obter as métricas |
> | Ação | Microsoft. StorSimple/Managers/Devices/servidores de e/ou compartilhamentos/metricsDefinitions/leitura | Listar ou obter as definições de métricas |
> | Ação | Microsoft. StorSimple/Managers/Devices/servidores de e/ou compartilhamentos/operationResults/leitura | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/servidores de os/compartilhamentos/leitura | Listar ou obter os compartilhamentos |
> | Ação | Microsoft. StorSimple/Managers/Devices/servidores de servidor/compartilhamentos/gravação | Criar ou atualizar os compartilhamentos |
> | Ação | Microsoft. StorSimple/Managers/Devices/Servers/Write | Criar ou atualizar os servidores de arquivos |
> | Ação | Microsoft. StorSimple/Managers/Devices/hardwareComponentGroups/changeControllerPowerState/Action | Alterar o estado de energia do controlador de grupos de componentes de hardware |
> | Ação | Microsoft. StorSimple/Managers/Devices/hardwareComponentGroups/operationResults/Read | Listar os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/hardwareComponentGroups/Read | Listar os grupos de componentes de hardware |
> | Ação | Microsoft. StorSimple/Managers/Devices/instalação/ação | Instalar atualizações em um dispositivo. |
> | Ação | Microsoft. StorSimple/Managers/Devices/installUpdates/Action | Instala atualizações nos dispositivos (somente série 8000). |
> | Ação | Microsoft. StorSimple/Managers/Devices/iscsiservers/backup/ação | Faça backup de um servidor iSCSI. |
> | Ação | Microsoft. StorSimple/Managers/Devices/iscsiservers/Delete | Exclui os servidores iSCSI |
> | Ação | Microsoft. StorSimple/Managers/Devices/iscsiservers/disks/Delete | Exclui os discos |
> | Ação | Microsoft. StorSimple/Managers/Devices/iscsiservers/discos/métricas/leitura | Listar ou obter as métricas |
> | Ação | Microsoft. StorSimple/Managers/Devices/iscsiservers/disks/metricsDefinitions/Read | Listar ou obter as definições de métricas |
> | Ação | Microsoft. StorSimple/Managers/Devices/iscsiservers/disks/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/iscsiservers/disks/Read | Listar ou obter os discos |
> | Ação | Microsoft. StorSimple/Managers/Devices/iscsiservers/disks/Write | Criar ou atualizar os discos |
> | Ação | Microsoft. StorSimple/Managers/Devices/iscsiservers/métricas/leitura | Listar ou obter as métricas |
> | Ação | Microsoft. StorSimple/Managers/Devices/iscsiservers/metricsDefinitions/Read | Listar ou obter as definições de métricas |
> | Ação | Microsoft. StorSimple/Managers/Devices/iscsiservers/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/iscsiservers/Read | Listar ou obter os servidores iSCSI |
> | Ação | Microsoft. StorSimple/Managers/Devices/iscsiservers/Write | Criar ou atualizar os servidores iSCSI |
> | Ação | Microsoft. StorSimple/Managers/Devices/Jobs/cancelamento/ação | Cancelar um trabalho em execução |
> | Ação | Microsoft. StorSimple/Managers/Devices/Jobs/operationResults/Read | Listar os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/Jobs/Read | Listar ou obter os trabalhos |
> | Ação | Microsoft. StorSimple/Managers/Devices/listFailoverSets/Action | Listar os conjuntos de failover para um dispositivo existente (somente série 8000). |
> | Ação | Microsoft. StorSimple/Managers/Devices/listFailoverTargets/Action | Listar destinos de failover dos dispositivos (somente série 8000). |
> | Ação | Microsoft. StorSimple/Managers/dispositivos/métricas/leitura | Listar ou obter as métricas |
> | Ação | Microsoft. StorSimple/Managers/Devices/metricsDefinitions/Read | Listar ou obter as definições de métricas |
> | Ação | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/confirmMigration/Action | Confirma uma migração bem-sucedida e confirmá-la. |
> | Ação | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/confirmMigrationStatus/Read | Listar o status de confirmação de migração |
> | Ação | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/Action | Busque o status de confirmação da migração. |
> | Ação | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/fetchMigrationEstimate/Action | Busque o status do trabalho de estimativa de migração. |
> | Ação | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/fetchMigrationStatus/Action | Busque o status da migração. |
> | Ação | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/importação/ação | Importar configurações de origem para migração |
> | Ação | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/migrationEstimate/Read | Listar a estimativa de migração |
> | Ação | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/migrationStatus/Read | Listar o status de migração |
> | Ação | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/operationResults/Read | Listar os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/startMigration/Action | Iniciar a migração usando as configurações de origem |
> | Ação | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/startMigrationEstimate/Action | Inicie um trabalho para estimar a duração do processo de migração. |
> | Ação | Microsoft. StorSimple/Managers/Devices/networkSettings/operationResults/Read | Listar os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/networkSettings/Read | Listar ou obter as configurações de rede |
> | Ação | Microsoft. StorSimple/Managers/Devices/networkSettings/Write | Cria uma nova ou atualiza as configurações de rede |
> | Ação | Microsoft. StorSimple/Managers/Devices/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/publicEncryptionKey/Action | Listar a chave de criptografia pública do Gerenciador de dispositivos |
> | Ação | Microsoft. StorSimple/Managers/Devices/publishSupportPackage/Action | Publicar o pacote de suporte para um dispositivo existente. Um pacote de suporte do StorSimple é um mecanismo fácil de usar que coleta todos os logs relevantes para auxiliar Suporte da Microsoft com a solução de problemas de dispositivos StorSimple. |
> | Ação | Microsoft. StorSimple/Managers/Devices/Read | Listar ou obter os dispositivos |
> | Ação | Microsoft. StorSimple/Managers/Devices/scanForUpdates/Action | Verificar se há atualizações em um dispositivo. |
> | Ação | Microsoft. StorSimple/Managers/Devices/securitySettings/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/securitySettings/Read | Listar as configurações de segurança |
> | Ação | Microsoft. StorSimple/Managers/Devices/securitySettings/syncRemoteManagementCertificate/Action | Sincronizar o certificado de gerenciamento remoto para um dispositivo. |
> | Ação | Microsoft. StorSimple/Managers/Devices/securitySettings/UPDATE/Action | Atualize as configurações de segurança. |
> | Ação | Microsoft. StorSimple/Managers/Devices/securitySettings/Write | Cria uma nova ou atualiza configurações de segurança |
> | Ação | Microsoft. StorSimple/Managers/Devices/sendTestAlertEmail/Action | Enviar email de alerta de teste para destinatários de email configurados. |
> | Ação | Microsoft. StorSimple/Managers/Devices/shares/Read | Listar ou obter os compartilhamentos |
> | Ação | Microsoft. StorSimple/Managers/Devices/timesettings/operationResults/Read | Listar os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/timesettings/Read | Listar ou obter as configurações de tempo |
> | Ação | Microsoft. StorSimple/Managers/Devices/Configurations/Write | Cria uma nova ou atualiza as configurações de hora |
> | Ação | Microsoft. StorSimple/Managers/Devices/updates/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/updateSummary/Read | Listar ou obter o resumo de atualizações |
> | Ação | Microsoft. StorSimple/Managers/Devices/volumeContainers/Delete | Exclui um contêiner de volume existente (somente série 8000) |
> | Ação | Microsoft. StorSimple/Managers/Devices/volumeContainers/métricas/leitura | Listar as métricas |
> | Ação | Microsoft. StorSimple/Managers/Devices/volumeContainers/metricsDefinitions/Read | Listar as definições de métricas |
> | Ação | Microsoft. StorSimple/Managers/Devices/volumeContainers/operationResults/Read | Listar os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/volumeContainers/Read | Listar os contêineres de volume (somente série 8000) |
> | Ação | Microsoft. StorSimple/Managers/Devices/volumeContainers/volumes/Delete | Exclui um volume existente |
> | Ação | Microsoft. StorSimple/Managers/Devices/volumeContainers/volumes/métricas/leitura | Listar as métricas |
> | Ação | Microsoft. StorSimple/Managers/Devices/volumeContainers/volumes/metricsDefinitions/Read | Listar as definições de métricas |
> | Ação | Microsoft. StorSimple/Managers/Devices/volumeContainers/volumes/operationResults/Read | Listar os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/Devices/volumeContainers/volumes/leitura | Listar os volumes |
> | Ação | Microsoft. StorSimple/Managers/Devices/volumeContainers/volumes/gravação | Cria um novo ou atualiza volumes |
> | Ação | Microsoft. StorSimple/Managers/Devices/volumeContainers/Write | Cria um novo ou atualiza contêineres de volume (somente série 8000) |
> | Ação | Microsoft. StorSimple/Managers/Devices/volumes/Read | Listar os volumes |
> | Ação | Microsoft. StorSimple/Managers/Devices/Write | Criar ou atualizar os dispositivos |
> | Ação | Microsoft. StorSimple/Managers/encryptionSettings/Read | Listar ou obter as configurações de criptografia |
> | Ação | Microsoft. StorSimple/Managers/extendedInformation/Delete | Exclui as informações do cofre estendido |
> | Ação | Microsoft. StorSimple/Managers/extendedInformation/Delete | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Ação | Microsoft. StorSimple/Managers/extendedInformation/Read | Listar ou obter as informações do cofre estendido |
> | Ação | Microsoft. StorSimple/Managers/extendedInformation/Read | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Ação | Microsoft. StorSimple/Managers/extendedInformation/Write | Criar ou atualizar as informações do cofre estendido |
> | Ação | Microsoft. StorSimple/Managers/extendedInformation/Write | A operação obter informações estendidas Obtém as informações estendidas de um objeto que representam o recurso do Azure do tipo? cofre? |
> | Ação | Microsoft. StorSimple/Managers/Features/Read | Listar os recursos |
> | Ação | Microsoft. StorSimple/Managers/servidores de os/leitura | Listar ou obter os servidores de arquivos |
> | Ação | Microsoft. StorSimple/Managers/getEncryptionKey/Action | Obtenha a chave de criptografia para o Gerenciador de dispositivos. |
> | Ação | Microsoft. StorSimple/Managers/iscsiservers/Read | Listar ou obter os servidores iSCSI |
> | Ação | Microsoft. StorSimple/Managers/Jobs/ler | Listar ou obter os trabalhos |
> | Ação | Microsoft. StorSimple/Managers/listActivationKey/Action | Obtém a chave de ativação do Device Manager StorSimple. |
> | Ação | Microsoft. StorSimple/Managers/listPublicEncryptionKey/Action | Listar chaves de criptografia pública de um Device Manager StorSimple. |
> | Ação | Microsoft. StorSimple/Managers/métricas/leitura | Listar ou obter as métricas |
> | Ação | Microsoft. StorSimple/Managers/metricsDefinitions/Read | Listar ou obter as definições de métricas |
> | Ação | Microsoft. StorSimple/Managers/migrateClassicToResourceManager/Action | Migrar o clássico para o Gerenciador de recursos dos gerenciadores |
> | Ação | Microsoft. StorSimple/Managers/migrationSourceConfigurations/Read | Listar as configurações de origem de migração (somente série 8000) |
> | Ação | Microsoft. StorSimple/Managers/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/provisionCloudAppliance/Action | Crie um novo dispositivo de nuvem. |
> | Ação | Microsoft. StorSimple/Managers/Read | Listar ou obter os gerenciadores de dispositivos |
> | Ação | Microsoft. StorSimple/Managers/Read | A operação obter cofre Obtém um objeto que representa o recurso do Azure do tipo ' cofre ' |
> | Ação | Microsoft. StorSimple/Managers/regenerateActivationKey/Action | Regenerar a chave de ativação para um Device Manager StorSimple existente. |
> | Ação | Microsoft. StorSimple/Managers/storageAccountCredentials/Delete | Exclui as credenciais da conta de armazenamento |
> | Ação | Microsoft. StorSimple/Managers/storageAccountCredentials/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/storageAccountCredentials/Read | Listar ou obter as credenciais da conta de armazenamento |
> | Ação | Microsoft. StorSimple/Managers/storageAccountCredentials/Write | Criar ou atualizar as credenciais da conta de armazenamento |
> | Ação | Microsoft. StorSimple/Managers/storageDomains/Delete | Exclui os domínios de armazenamento |
> | Ação | Microsoft. StorSimple/Managers/storageDomains/operationResults/Read | Listar ou obter os resultados da operação |
> | Ação | Microsoft. StorSimple/Managers/storageDomains/Read | Listar ou obter os domínios de armazenamento |
> | Ação | Microsoft. StorSimple/Managers/storageDomains/Write | Criar ou atualizar os domínios de armazenamento |
> | Ação | Microsoft. StorSimple/Managers/Write | Criar ou atualizar os gerenciadores de dispositivos |
> | Ação | Microsoft. StorSimple/Managers/Write | A operação criar cofre cria um recurso do Azure do tipo ' cofre ' |
> | Ação | Microsoft. StorSimple/Operations/Read | Listar ou obter as operações |
> | Ação | Microsoft. StorSimple/registro/ação | Registrar provedor Microsoft. StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. StreamAnalytics/localizações/cotas/leitura | Ler Stream Analytics cota de assinatura |
> | Ação | Microsoft. StreamAnalytics/operações/leitura | Ler Stream Analytics operações |
> | Ação | Microsoft. StreamAnalytics/registro/ação | Registrar assinatura com provedor de recursos Stream Analytics |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Delete | Excluir trabalho de Stream Analytics |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Functions/Delete | Excluir função de trabalho Stream Analytics |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Functions/operationresults/Read | Ler resultados da operação para Stream Analytics função de trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Functions/Read | Ler Stream Analytics função de trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Functions/RetrieveDefaultDefinition/Action | Recuperar a definição padrão de uma função de trabalho Stream Analytics |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Functions/Test/Action | Testar função de trabalho de Stream Analytics |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Functions/Write | Gravar Stream Analytics função de trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/entradas/excluir | Excluir Stream Analytics entrada de trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/inputs/operationresults/Read | Ler resultados da operação para Stream Analytics entrada do trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/entradas/leitura | Ler Stream Analytics entrada do trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/entradas/amostra/ação | Exemplo de entrada de trabalho de Stream Analytics |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/inputs/Test/Action | Testar Stream Analytics entrada do trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/inputs/Write | Gravar Stream Analytics entrada de trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/metricdefinitions/Read | Ler definições de métrica |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/operationresults/Read | Ler resultados da operação para Stream Analytics trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Outputs/Delete | Excluir Stream Analytics saída de trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Outputs/operationresults/Read | Ler resultados da operação para Stream Analytics saída do trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Outputs/Read | Ler Stream Analytics saída do trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Outputs/Test/Action | Testar a saída do trabalho de Stream Analytics |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Outputs/Write | Gravar Stream Analytics saída de trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. insights/diagnosticSettings/Read | Ler configuração de diagnóstico. |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. insights/diagnosticSettings/Write | Gravar configuração de diagnóstico. |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. insights/logDefinitions/Read | Obter os logs disponíveis para streamingjobs |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. insights/metricDefinitions/Read | Obter as métricas disponíveis para streamingjobs |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Read | Ler Stream Analytics trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Start/Action | Iniciar Stream Analytics trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Stop/Action | Parar Stream Analytics trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/transformações/exclusão | Excluir Stream Analytics transformação de trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/transformações/leitura | Ler Stream Analytics transformação de trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/transformações/gravação | Gravar Stream Analytics transformação de trabalho |
> | Ação | Microsoft. StreamAnalytics/streamingjobs/Write | Gravar Stream Analytics trabalho |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Subscription/cancelamento/ação | Cancela a assinatura |
> | Ação | Microsoft. Subscription/createassinatura/ação | Criar uma subscrição do Azure |
> | Ação | Microsoft. Subscription/registro/ação | Registra a assinatura com o provedor de recursos Microsoft. Subscription |
> | Ação | Microsoft. Subscription/renomear/ação | Renomeia a assinatura |
> | Ação | Microsoft. Subscription/SubscriptionDefinitions/Read | Obtenha uma definição de assinatura do Azure dentro de um grupo de gerenciamento. |
> | Ação | Microsoft. Subscription/SubscriptionDefinitions/Write | Criar uma definição de assinatura do Azure |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft.Support/register/action | Regista para o Fornecedor de Recursos de Suporte |
> | Ação | Microsoft.Support/supportTickets/read | Obtém os detalhes do Pedido de Suporte (incluindo o estado, gravidade, detalhes de contacto e comunicações) ou obtém a lista dos Pedidos de Suporte nas subscrições. |
> | Ação | Microsoft.Support/supportTickets/write | Cria ou Atualiza um Pedido de Suporte. Pode criar um Pedido de Suporte para problemas relacionados com a Gestão Técnica, de Faturação, de Quotas ou de Subscrição. Pode atualizar a gravidade, os detalhes de contacto e as comunicações para pedidos de suporte existentes. |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. TimeSeriesInsights/Environments/accesspolicies/Delete | Exclui a política de acesso. |
> | Ação | Microsoft. TimeSeriesInsights/ambientes/accesspolicies/leitura | Obter as propriedades de uma política de acesso. |
> | Ação | Microsoft. TimeSeriesInsights/Environments/accesspolicies/Write | Cria uma nova política de acesso para um ambiente ou atualiza uma política de acesso existente. |
> | Ação | Microsoft. TimeSeriesInsights/ambientes/excluir | Exclui o ambiente. |
> | Ação | Microsoft. TimeSeriesInsights/ambientes/EventSources/excluir | Exclui a origem do evento. |
> | Ação | Microsoft. TimeSeriesInsights/ambientes/EventSources/leitura | Obter as propriedades de uma origem de evento. |
> | Ação | Microsoft. TimeSeriesInsights/ambientes/EventSources/gravação | Cria uma nova origem de evento para um ambiente ou atualiza uma origem de evento existente. |
> | Ação | Microsoft. TimeSeriesInsights/ambientes/leitura | Obter as propriedades de um ambiente. |
> | Ação | Microsoft. TimeSeriesInsights/Environments/referencedatasets/Delete | Exclui o conjunto de dados de referência. |
> | Ação | Microsoft. TimeSeriesInsights/ambientes/referencedatasets/leitura | Obter as propriedades de um conjunto de dados de referência. |
> | Ação | Microsoft. TimeSeriesInsights/Environments/referencedatasets/Write | Cria um novo conjunto de dados de referência para um ambiente ou atualiza um conjunto de dados de referência existente. |
> | Ação | Microsoft. TimeSeriesInsights/ambientes/status/leitura | Obtenha o status do ambiente, o estado de suas operações associadas como entrada. |
> | Ação | Microsoft. TimeSeriesInsights/ambientes/gravação | Cria um novo ambiente ou atualiza um ambiente existente. |
> | Ação | Microsoft. TimeSeriesInsights/registro/ação | Registra a assinatura para o provedor de recursos de Time Series Insights e permite a criação de ambientes de Time Series Insights. |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. VisualStudio/Account/Delete | Excluir conta |
> | Ação | Microsoft. VisualStudio/conta/extensão/leitura | Ler conta/extensão |
> | Ação | Microsoft. VisualStudio/Account/Project/Read | Ler conta/projeto |
> | Ação | Microsoft. VisualStudio/Account/Project/Write | Definir conta/projeto |
> | Ação | Microsoft. VisualStudio/Account/Read | Ler conta |
> | Ação | Microsoft. VisualStudio/Account/Write | Definir conta |
> | Ação | Microsoft. VisualStudio/Extension/Delete | Excluir extensão |
> | Ação | Microsoft. VisualStudio/Extension/Read | Ler extensão |
> | Ação | Microsoft. VisualStudio/Extension/Write | Definir extensão |
> | Ação | Microsoft. VisualStudio/Project/Delete | Excluir projeto |
> | Ação | Microsoft. VisualStudio/Project/Read | Ler projeto |
> | Ação | Microsoft. VisualStudio/Project/Write | Definir projeto |
> | Ação | Microsoft. VisualStudio/registro/ação | Registrar a assinatura do Azure com o provedor Microsoft. VisualStudio |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. Web/apimanagementaccounts/apiacls/ler | Obter contas de gerenciamento de API Apiacls. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/apiacls/Delete | Excluir APIs de contas de gerenciamento de API Apiacls. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/apiacls/Read | Obter APIs de contas de gerenciamento de API Apiacls. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/apiacls/Write | Atualizar APIs de contas de gerenciamento de API Apiacls. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/connectionacls/Read | Obter APIs de contas de gerenciamento de API Connectionacls. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/Connections/confirmconsentcode/Action | Confirme o código de consentimento gerenciamento de API contas de APIs conexões. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/Connections/connectionacls/Delete | Excluir as contas de gerenciamento de API Connectionacls conexões de APIs. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/conexões/connectionacls/leitura | Obter conexões de APIs de contas de gerenciamento de API Connectionacls. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/conexões/connectionacls/gravação | Atualizar as contas de gerenciamento de API conexões de APIs Connectionacls. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/Connections/Delete | Excluir conexões de APIs de contas de gerenciamento de API. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/Connections/getconsentlinks/Action | Obtenha links de autorização para conexões de APIs de contas de gerenciamento de API. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/Connections/listconnectionkeys/Action | Listar chaves de conexão contas de gerenciamento de API conexões de APIs. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/Connections/listsecrets/Action | Listar segredos gerenciamento de API contas conexões de APIs. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/conexões/leitura | Obter conexões de APIs de contas de gerenciamento de API. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/conexões/gravação | Atualizar conexões de APIs de contas de gerenciamento de API. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/Delete | Excluir APIs de contas de gerenciamento de API. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/localizeddefinitions/Delete | Exclua as definições localizadas das APIs de contas de gerenciamento de API. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/localizeddefinitions/Read | Obter definições localizadas de APIs de contas de gerenciamento de API. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/localizeddefinitions/Write | Atualize as definições localizadas das APIs de contas de gerenciamento de API. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/ler | Obter APIs de contas de gerenciamento de API. |
> | Ação | Microsoft. Web/apimanagementaccounts/APIs/Write | Atualizar APIs de contas de gerenciamento de API. |
> | Ação | Microsoft. Web/apimanagementaccounts/connectionacls/ler | Obter contas de gerenciamento de API Connectionacls. |
> | Ação | Microsoft. Web/availablestacks/leitura | Obter pilhas disponíveis. |
> | Ação | Microsoft. Web/certificados/excluir | Excluir um certificado existente. |
> | Ação | Microsoft. Web/certificados/leitura | Obter a lista de certificados. |
> | Ação | Microsoft. Web/Certificates/Write | Adicione um novo certificado ou atualize um existente. |
> | Ação | Microsoft. Web/checknameavailability/leitura | Verifique se o nome do recurso está disponível. |
> | Ação | Microsoft. Web/classicmobileservices/leitura | Obtenha serviços móveis clássicos. |
> | Ação | Microsoft. Web/connectionGateways/Delete | Exclui um gateway de conexão. |
> | Ação | Microsoft. Web/connectionGateways/junção/ação | Une um gateway de conexão. |
> | Ação | Microsoft. Web/connectionGateways/ListStatus/Action | Lista o status de um gateway de conexão. |
> | Ação | Microsoft. Web/connectionGateways/mover/ação | Move um gateway de conexão. |
> | Ação | Microsoft. Web/connectionGateways/leitura | Obtenha a lista de gateways de conexão. |
> | Ação | Microsoft. Web/connectionGateways/Write | Cria ou atualiza um gateway de conexão. |
> | Ação | Microsoft. Web/Connections/confirmconsentcode/Action | Confirme o código de consentimento de conexões. |
> | Ação | Microsoft. Web/Connections/Delete | Exclui uma conexão. |
> | Ação | Microsoft. Web/conexões/junção/ação | Une uma conexão. |
> | Ação | Microsoft. Web/Connections/listconsentlinks/Action | Listar links de consentimento para conexões. |
> | Ação | Microsoft. Web/Connections/mover/ação | Move uma conexão. |
> | Ação | Microsoft. Web/Connections/Read | Obtenha a lista de conexões. |
> | Ação | Microsoft. Web/Connections/Write | Cria ou atualiza uma conexão. |
> | Ação | Microsoft. Web/customApis/Delete | Exclui uma API personalizada. |
> | Ação | Microsoft. Web/customApis/extractApiDefinitionFromWsdl/Action | Extrai a definição de API de um WSDL. |
> | Ação | Microsoft. Web/customApis/junção/ação | Une uma API personalizada. |
> | Ação | Microsoft. Web/customApis/listWsdlInterfaces/Action | Lista as interfaces WSDL para uma API personalizada. |
> | Ação | Microsoft. Web/customApis/mover/ação | Move uma API personalizada. |
> | Ação | Microsoft. Web/customApis/leitura | Obtenha a lista de API personalizada. |
> | Ação | Microsoft. Web/customApis/Write | Cria ou atualiza uma API personalizada. |
> | Ação | Microsoft. Web/deletedSites/leitura | Obter as propriedades de um aplicativo Web excluído |
> | Ação | Microsoft. Web/deploymentlocations/leitura | Obter locais de implantação. |
> | Ação | Microsoft. Web/georegionals/leitura | Obtenha a lista de regiões geográficas. |
> | Ação | Microsoft. Web/hostingenvironments/capacidades/leitura | Obter as capacidades dos ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingEnvironments/excluir | Excluir um Ambiente do Serviço de Aplicativo |
> | Ação | Microsoft. Web/hostingenvironments/detectores/leitura | Obter detectores de ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingenvironments/diagnóstico/leitura | Obter diagnóstico de ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingenvironments/inboundnetworkdependenciesendpoints/Read | Obter os pontos de extremidade de rede de todas as dependências de entrada. |
> | Ação | Microsoft. Web/hostingEnvironments/junção/ação | Une um Ambiente do Serviço de Aplicativo |
> | Ação | Microsoft. Web/hostingenvironments/metricdefinitions/Read | Obter definições de métrica de ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingenvironments/multirolepools/metricdefinitions/Read | Obter definições de métrica de pools de multifunção de ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingenvironments/multirolepools/métricas/leitura | Obter métricas de pools de multifunção de ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingEnvironments/multiRolePools/Read | Obter as propriedades de um pool de front-end em um Ambiente do Serviço de Aplicativo |
> | Ação | Microsoft. Web/hostingenvironments/multirolepools/SKUs/Read | Obter SKUs de pools de multifunção de ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingenvironments/multirolepools/usos/leitura | Obter usos de pools de multifunção de ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingEnvironments/multiRolePools/Write | Criar um novo pool de front-end em um Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Ação | Microsoft. Web/hostingenvironments/operações/leitura | Obter operações de ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingenvironments/outboundnetworkdependenciesendpoints/Read | Obter os pontos de extremidade de rede de todas as dependências de saída. |
> | Ação | Microsoft. Web/hostingEnvironments/PrivateEndpointConnectionsApproval/ação | Aprovar conexões de ponto de extremidade privado |
> | Ação | Microsoft. Web/hostingEnvironments/leitura | Obter as propriedades de um Ambiente do Serviço de Aplicativo |
> | Ação | Microsoft. Web/hostingEnvironments/reboot/Action | Reinicializar todos os computadores em um Ambiente do Serviço de Aplicativo |
> | Ação | Microsoft. Web/hostingenvironments/retomar/ação | Retome os ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingenvironments/serverfarms/Read | Obter planos de serviço de aplicativo dos ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingenvironments/sites/leitura | Obter aplicativos Web de ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingenvironments/suspender/ação | Suspender ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingenvironments/usos/leitura | Obter usos de ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingenvironments/pools/metricdefinitions/Read | Obter os ambientes de hospedagem definições de métrica pools. |
> | Ação | Microsoft. Web/hostingenvironments/pools/métricas/leitura | Obter métricas de pools de ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingEnvironments/pools/Read | Obter as propriedades de um pool de trabalho em um Ambiente do Serviço de Aplicativo |
> | Ação | Microsoft. Web/hostingenvironments/pools/SKUs/Read | Obter pools SKUs de ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingenvironments/pools/usos/leitura | Obter pools usos de ambientes de hospedagem. |
> | Ação | Microsoft. Web/hostingEnvironments/pools/Write | Criar um novo pool de trabalho em um Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Ação | Microsoft. Web/hostingEnvironments/Write | Criar um novo Ambiente do Serviço de Aplicativo ou atualizar um existente |
> | Ação | Microsoft. Web/ishostingenvironmentnameavailable/leitura | Obter se o nome do ambiente de hospedagem estiver disponível. |
> | Ação | Microsoft. Web/ishostnameavailable/leitura | Verifique se o nome do host está disponível. |
> | Ação | Microsoft. Web/isusernameavailable/leitura | Verifique se o nome de usuário está disponível. |
> | Ação | Microsoft. Web/listSitesAssignedToHostName/leitura | Obter nomes de sites atribuídos ao nome do host. |
> | Ação | Microsoft. Web/Locations/apioperations/Read | Obter operações de API de locais. |
> | Ação | Microsoft. Web/Locations/connectiongatewayinstallations/Read | Obter instalações de gateway de conexão de locais. |
> | Ação | Microsoft. Web/Locations/deleteVirtualNetworkOrSubnets/Action | Notificação de exclusão de sub-rede ou vnet para locais. |
> | Ação | Microsoft. Web/Locations/extractapidefinitionfromwsdl/Action | Extraia a definição de API do WSDL para locais. |
> | Ação | Microsoft. Web/Locations/listwsdlinterfaces/Action | Listar interfaces WSDL para locais. |
> | Ação | Microsoft. Web/Locations/managedapis/apioperations/Read | Obter operações de API gerenciadas de locais. |
> | Ação | Microsoft. Web/Locations/managedapis/junção/ação | Une uma API gerenciada. |
> | Ação | Microsoft. Web/Locations/managedapis/Read | Obter APIs gerenciadas de locais. |
> | Ação | Microsoft. Web/Locations/operationResults/Read | Obter operações. |
> | Ação | Microsoft. Web/Locations/Operations/Read | Obter operações. |
> | Ação | Microsoft. Web/operações/leitura | Obter operações. |
> | Ação | Microsoft. Web/publishingusers/leitura | Obter usuários de publicação. |
> | Ação | Microsoft. Web/publishingusers/Write | Atualizar usuários de publicação. |
> | Ação | Microsoft. Web/Recommendations/Read | Obtenha a lista de recomendações para assinaturas. |
> | Ação | Microsoft. Web/registro/ação | Registre o provedor de recursos Microsoft. Web para a assinatura. |
> | Ação | Microsoft. Web/resourcehealthmetadata/leitura | Obter Resource Health metadados. |
> | Ação | Microsoft. Web/serverfarms/Capabilities/ler | Obter recursos de planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/Delete | Excluir um plano do serviço de aplicativo existente |
> | Ação | Microsoft. Web/serverfarms/eventGridFilters/Delete | Excluir filtro de grade de eventos no farm de servidores. |
> | Ação | Microsoft. Web/serverfarms/eventGridFilters/ler | Obter filtro de grade de eventos no farm de servidores. |
> | Ação | Microsoft. Web/serverfarms/eventGridFilters/Write | Colocar filtro de grade de eventos no farm de servidores. |
> | Ação | Microsoft. Web/serverfarms/firstpartyapps/configurações/excluir | Excluir configurações de aplicativos de primeira parte dos planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/firstpartyapps/configurações/leitura | Obter configurações de aplicativos de primeira parte dos planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/firstpartyapps/configurações/gravação | Atualize as configurações de aplicativos de primeira parte dos planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/hybridconnectionnamespaces/retransmissões/excluir | Excluir retransmissões de namespaces de conexão híbrida dos planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/hybridconnectionnamespaces/retransmissões/leitura | Obter retransmissões de namespaces de conexão híbrida dos planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/hybridconnectionnamespaces/retransmissões/sites/leitura | Obter os namespaces de conexão híbrida dos planos do serviço de aplicativo retransmite aplicativos Web. |
> | Ação | Microsoft. Web/serverfarms/hybridconnectionplanlimits/ler | Obter limites do plano de conexão híbrida dos planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/hybridconnectionrelays/ler | Obter retransmissões de conexão híbrida dos planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/metricdefinitions/ler | Obter definições de métrica de planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/métricas/leitura | Obter métricas de planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/operationresults/ler | Obter resultados da operação de planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/leitura | Obter as propriedades em um plano do serviço de aplicativo |
> | Ação | Microsoft. Web/serverfarms/restartSites/Action | Reiniciar todos os aplicativos Web em um plano do serviço de aplicativo |
> | Ação | Microsoft. Web/serverfarms/sites/ler | Obter aplicativos Web de planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/SKUs/leitura | Obter SKUs de planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/usos/leitura | Obter usos de planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/virtualnetworkconnections/gateways/gravação | Atualizar gateways de conexões de rede virtual dos planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/virtualnetworkconnections/ler | Obter conexões de rede virtual dos planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/virtualnetworkconnections/Routes/Delete | Excluir rotas de conexões de rede virtual dos planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/virtualnetworkconnections/Routes/Read | Obter rotas de conexões de rede virtual dos planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/virtualnetworkconnections/Routes/Write | Atualizar rotas de conexões de rede virtual dos planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/Works/reinicialização/ação | Reinicialize os funcionários dos planos do serviço de aplicativo. |
> | Ação | Microsoft. Web/serverfarms/Write | Criar um novo plano do serviço de aplicativo ou atualizar um existente |
> | Ação | Microsoft. Web/sites/analyzecustomhostname/ler | Analise o nome de host personalizado. |
> | Ação | Microsoft. Web/sites/applySlotConfig/Action | Aplicar a configuração de slot do aplicativo Web do slot de destino ao aplicativo Web atual |
> | Ação | Microsoft. Web/sites/backup/ação | Criar um novo backup de aplicativo Web |
> | Ação | Microsoft. Web/sites/backup/leitura | Obter backup de aplicativos Web. |
> | Ação | Microsoft. Web/sites/backup/gravação | Atualize o backup de aplicativos Web. |
> | Ação | Microsoft. Web/sites/backups/ação | Descobre um backup de aplicativo existente que pode ser restaurado de um blob no armazenamento do Azure. |
> | Ação | Microsoft. Web/sites/backups/excluir | Excluir backups de aplicativos Web. |
> | Ação | Microsoft. Web/sites/backups/lista/ação | Listar backups de aplicativos Web. |
> | Ação | Microsoft. Web/sites/backups/leitura | Obter as propriedades do backup de um aplicativo Web |
> | Ação | Microsoft. Web/sites/backups/restauração/ação | Restaurar backups de aplicativos Web. |
> | Ação | Microsoft. Web/sites/backups/gravação | Atualizar backups de aplicativos Web. |
> | Ação | Microsoft. Web/sites/config/Delete | Excluir a configuração de aplicativos Web. |
> | Ação | Microsoft. Web/sites/config/lista/ação | Listar as configurações confidenciais de segurança do aplicativo Web, como credenciais de publicação, configurações de aplicativo e cadeias de conexão |
> | Ação | Microsoft. Web/sites/config/ler | Obter definições de configuração do aplicativo Web |
> | Ação | Microsoft. Web/sites/config/instantâneos/leitura | Obter instantâneos de configuração de aplicativos Web. |
> | Ação | Microsoft. Web/sites/config/Write | Atualizar as definições de configuração do aplicativo Web |
> | Ação | Microsoft. Web/sites/containerlogs/Action | Obter logs de contêiner compactados para o aplicativo Web. |
> | Ação | Microsoft. Web/sites/containerlogs/download/ação | Baixar logs de contêiner de aplicativos Web. |
> | Ação | Microsoft. Web/sites/continuouswebjobs/Delete | Excluir trabalhos Web contínuos de aplicativos Web. |
> | Ação | Microsoft. Web/sites/continuouswebjobs/ler | Obter trabalhos Web contínuos de aplicativos Web. |
> | Ação | Microsoft. Web/sites/continuouswebjobs/iniciar/ação | Inicie trabalhos Web contínuos de aplicativos Web. |
> | Ação | Microsoft. Web/sites/continuouswebjobs/Stop/Action | Parar trabalhos Web contínuos de aplicativos Web. |
> | Ação | Microsoft. Web/sites/excluir | Excluir um aplicativo Web existente |
> | Ação | Microsoft. Web/sites/implantações/excluir | Excluir implantações de aplicativos Web. |
> | Ação | Microsoft. Web/sites/implantações/log/leitura | Obter log de implantações de aplicativos Web. |
> | Ação | Microsoft. Web/sites/implantações/leitura | Obter implantações de aplicativos Web. |
> | Ação | Microsoft. Web/sites/implantações/gravação | Atualizar implantações de aplicativos Web. |
> | Ação | Microsoft. Web/sites/detectores/leitura | Obter detectores de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/análises/execute/Action | Executar análise de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/diagnóstico/análise/leitura | Obter análise de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/aspnetcore/Read | Obtenha o diagnóstico de aplicativos Web para ASP.NET Core aplicativo. |
> | Ação | Microsoft. Web/sites/Diagnostics/autoreparo/leitura | Obter autoreparo de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/diagnóstico/implantação/leitura | Obter implantação de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/diagnóstico/implantações/leitura | Obter implantações de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/detectores/execute/Action | Executar o detector de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/detectores/leitura | Obter o detector de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/failedrequestsperuri/Read | Obter solicitações de diagnóstico de aplicativos Web com falha por URI. |
> | Ação | Microsoft. Web/sites/Diagnostics/frebanalysis/Read | Obter análise de FREB de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/LogAnalyzer/Read | Obtenha o analisador de log de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/diagnóstico/leitura | Obter categorias de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/runtimeavailability/Read | Obter disponibilidade de tempo de execução de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/imhealth/Read | Obter a integridade do serviço de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/sitecpuanalysis/Read | Obter análise de CPU do site de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/sitecrashes/Read | Obter falhas do site de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/sitelatency/Read | Obter latência de site de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/sitememoryanalysis/Read | Obter análise de memória do site de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/siterestartsettingupdate/Read | Obter reinicialização do site de diagnóstico de aplicativos Web configuração de atualização. |
> | Ação | Microsoft. Web/sites/Diagnostics/siterestartuserinitiated/Read | Obter reinicialização iniciada pelo usuário do site de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/siteswap/Read | Obter troca de site de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/ThreadCount/Read | Obter contagem de threads de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/disponibilidade/Read | Obter disponibilidade de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Diagnostics/workerprocessrecycle/Read | Obter reciclagem do processo de trabalho de diagnóstico de aplicativos Web. |
> | Ação | Microsoft. Web/sites/domainownershipidentifiers/ler | Obter identificadores de propriedade de domínio de aplicativos Web. |
> | Ação | Microsoft. Web/sites/domainownershipidentifiers/Write | Atualizar identificadores de propriedade de domínio de aplicativos Web. |
> | Ação | Microsoft. Web/sites/eventGridFilters/Delete | Excluir filtro de grade de eventos no aplicativo Web. |
> | Ação | Microsoft. Web/sites/eventGridFilters/ler | Obter filtro de grade de eventos no aplicativo Web. |
> | Ação | Microsoft. Web/sites/eventGridFilters/Write | Colocar filtro de grade de eventos no aplicativo Web. |
> | Ação | Microsoft. Web/sites/extensões/excluir | Excluir extensões de site de aplicativos Web. |
> | Ação | Microsoft. Web/sites/extensões/leitura | Obter extensões de site de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Extensions/Write | Atualizar extensões de site de aplicativos Web. |
> | Ação | Microsoft. Web/sites/funções/ação | Aplicativos Web do functions. |
> | Ação | Microsoft. Web/sites/funções/excluir | Excluir funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/funções/chaves/excluir | Excluir chaves de função. |
> | Ação | Microsoft. Web/sites/funções/chaves/gravação | Atualizar chaves de função. |
> | Ação | Microsoft. Web/sites/funções/listkeys/ação | Listar chaves de função. |
> | Ação | Microsoft. Web/sites/funções/listsecrets/ação | Listar segredos de função. |
> | Ação | Microsoft. Web/sites/funções/MasterKey/leitura | Obter funções de aplicativos Web MasterKey. |
> | Ação | Microsoft. Web/sites/funções/Propriedades/leitura | Ler propriedades de funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/funções/Propriedades/gravação | Atualizar propriedades de funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/funções/leitura | Obter funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/funções/token/leitura | Obter token de funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/funções/gravação | Atualizar funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/host/FunctionKeys/Delete | Excluir funções chaves de função de host. |
> | Ação | Microsoft. Web/sites/host/FunctionKeys/gravação | Funções de atualização hospedam chaves de função. |
> | Ação | Microsoft. Web/sites/host/listkeys/ação | Listar chaves de host do functions. |
> | Ação | Microsoft. Web/sites/host/listsyncstatus/ação | Listar status de gatilhos de função de sincronização. |
> | Ação | Microsoft. Web/sites/host/Propriedades/leitura | Ler propriedades do host de funções de aplicativos Web. |
> | Ação | Microsoft. Web/sites/host/sincronização/ação | Gatilhos de função de sincronização. |
> | Ação | Microsoft. Web/sites/host/systemkeys/Delete | Exclua as funções do sistema host. |
> | Ação | Microsoft. Web/sites/host/systemkeys/gravação | Funções de atualização hospedam chaves do sistema. |
> | Ação | Microsoft. Web/sites/hostnamebindings/Delete | Excluir associações de nome de host de aplicativos Web. |
> | Ação | Microsoft. Web/sites/hostnamebindings/ler | Obter associações de nome de host de aplicativos Web. |
> | Ação | Microsoft. Web/sites/hostnamebindings/Write | Atualizar associações de nome de host de aplicativos Web. |
> | Ação | Microsoft. Web/sites/hostruntime/funções/chaves/leitura | Obter chaves de funções do Hostruntime de aplicativos Web. |
> | Ação | Microsoft. Web/sites/hostruntime/host/_master/Read | Obter a chave mestra de Aplicativo de funções para operações de administração |
> | Ação | Microsoft. Web/sites/hostruntime/host/ação | Execute Aplicativo de funções ação de tempo de execução como gatilhos de sincronização, adicionar funções, invocar funções, excluir funções, etc. |
> | Ação | Microsoft. Web/sites/hostruntime/host/leitura | Obter host do Hostruntime de aplicativos Web. |
> | Ação | Microsoft. Web/sites/hybridconnection/Delete | Excluir a conexão híbrida de aplicativos Web. |
> | Ação | Microsoft. Web/sites/hybridconnection/ler | Obter conexão híbrida de aplicativos Web. |
> | Ação | Microsoft. Web/sites/hybridconnection/Write | Atualize a conexão híbrida de aplicativos Web. |
> | Ação | Microsoft. Web/sites/hybridconnectionnamespaces/retransmissões/excluir | Excluir retransmissões de namespaces de conexão híbrida de aplicativos Web. |
> | Ação | Microsoft. Web/sites/hybridconnectionnamespaces/retransmissões/listkeys/ação | Listar chaves de namespaces de conexão híbrida de aplicativos Web. |
> | Ação | Microsoft. Web/sites/hybridconnectionnamespaces/retransmissões/leitura | Obter retransmissões de namespaces de conexão híbrida de aplicativos Web. |
> | Ação | Microsoft. Web/sites/hybridconnectionnamespaces/retransmissões/gravação | Atualizar retransmissões de namespaces de conexão híbrida de aplicativos Web. |
> | Ação | Microsoft. Web/sites/hybridconnectionrelays/ler | Obter retransmissões de conexão híbrida de aplicativos Web. |
> | Ação | Microsoft. Web/sites/instâncias/implantações/excluir | Excluir implantações de instâncias de aplicativos Web. |
> | Ação | Microsoft. Web/sites/instâncias/implantações/leitura | Obter implantações de instâncias de aplicativos Web. |
> | Ação | Microsoft. Web/sites/instâncias/extensões/log/leitura | Obter o log de extensões de instâncias de aplicativos Web. |
> | Ação | Microsoft. Web/sites/instâncias/extensões/processos/leitura | Obter processos de extensões de instâncias de aplicativos Web. |
> | Ação | Microsoft. Web/sites/instâncias/extensões/leitura | Obter extensões de instâncias de aplicativos Web. |
> | Ação | Microsoft. Web/sites/instâncias/processos/excluir | Excluir processos de instâncias de aplicativos Web. |
> | Ação | Microsoft. Web/sites/instâncias/processos/módulos/leitura | Obter módulos de aplicativos Web processos de instâncias. |
> | Ação | Microsoft. Web/sites/instâncias/processos/leitura | Obter processos de instâncias de aplicativos Web. |
> | Ação | Microsoft. Web/sites/instâncias/processos/threads/leitura | Obter instâncias de aplicativos Web processa threads. |
> | Ação | Microsoft. Web/sites/instâncias/leitura | Obter instâncias de aplicativos Web. |
> | Ação | Microsoft. Web/sites/listsyncfunctiontriggerstatus/Action | Listar status do gatilho de função de sincronização. |
> | Ação | Microsoft. Web/sites/metricdefinitions/ler | Obter definições de métrica de aplicativos Web. |
> | Ação | Microsoft. Web/sites/métricas/leitura | Obter métricas de aplicativos Web. |
> | Ação | Microsoft. Web/sites/metricsdefinitions/ler | Obter definições de métricas de aplicativos Web. |
> | Ação | Microsoft. Web/sites/migratemysql/Action | Migrar aplicativos Web MySql. |
> | Ação | Microsoft. Web/sites/migratemysql/ler | Obter MySql migrar aplicativos Web. |
> | Ação | Microsoft. Web/sites/networktrace/Action | Aplicativos Web de rastreamento de rede. |
> | Ação | Microsoft. Web/sites/networktraces/operationresults/leitura | Obter resultados da operação de rastreamento de rede de aplicativos Web. |
> | Ação | Microsoft. Web/sites/newPassword/Action | Aplicativos Web de NewPassword. |
> | Ação | Microsoft. Web/sites/operationresults/ler | Obter resultados da operação de aplicativos Web. |
> | Ação | Microsoft. Web/sites/operações/leitura | Obter operações de aplicativos Web. |
> | Ação | Microsoft. Web/sites/perfcounters/ler | Obter contadores de desempenho de aplicativos Web. |
> | Ação | Microsoft. Web/sites/premieraddons/Delete | Excluir Complementos Premier de aplicativos Web. |
> | Ação | Microsoft. Web/sites/premieraddons/ler | Obter Complementos Premier de aplicativos Web. |
> | Ação | Microsoft. Web/sites/premieraddons/Write | Atualizar complementos Premier de aplicativos Web. |
> | Ação | Microsoft. Web/sites/privateaccess/ler | Obtenha dados sobre a habilitação de acesso do site privado e redes virtuais autorizadas que podem acessar o site. |
> | Ação | Microsoft. Web/sites/PrivateEndpointConnectionsApproval/Action | Aprovar conexões de ponto de extremidade privado |
> | Ação | Microsoft. Web/sites/processos/módulos/ler | Obter módulos de processos de aplicativos Web. |
> | Ação | Microsoft. Web/sites/processos/leitura | Obter processos de aplicativos Web. |
> | Ação | Microsoft. Web/sites/processos/threads/leitura | Obter threads de processos de aplicativos Web. |
> | Ação | Microsoft. Web/sites/publiccertificates/Delete | Excluir certificados públicos de aplicativos Web. |
> | Ação | Microsoft. Web/sites/publiccertificates/ler | Obter certificados públicos de aplicativos Web. |
> | Ação | Microsoft. Web/sites/publiccertificates/Write | Atualizar certificados públicos de aplicativos Web. |
> | Ação | Microsoft. Web/sites/publicação/ação | Publicar um aplicativo Web |
> | Ação | Microsoft. Web/sites/PublishXml/Action | Obter XML do perfil de publicação para um aplicativo Web |
> | Ação | Microsoft. Web/sites/PublishXml/ler | Obter XML de publicação de aplicativos Web. |
> | Ação | Microsoft. Web/sites/ler | Obter as propriedades de um aplicativo Web |
> | Ação | Microsoft. Web/sites/recommendationhistory/ler | Obter o histórico de recomendação de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Recommendations/desabilitar/ação | Desabilitar recomendações de aplicativos Web. |
> | Ação | Microsoft. Web/sites/recomendações/leitura | Obtenha a lista de recomendações para o aplicativo Web. |
> | Ação | Microsoft. Web/sites/recuperação/ação | Recuperar aplicativos Web. |
> | Ação | Microsoft. Web/sites/resetSlotConfig/Action | Redefinir a configuração do aplicativo Web |
> | Ação | Microsoft. Web/sites/resourcehealthmetadata/ler | Obter metadados de Resource Health de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Restart/Action | Reiniciar um aplicativo Web |
> | Ação | Microsoft. Web/sites/restaurar/ler | Obter restauração de aplicativos Web. |
> | Ação | Microsoft. Web/sites/restaurar/gravar | Restaurar aplicativos Web. |
> | Ação | Microsoft. Web/sites/restorefrombackupblob/Action | Restaure o aplicativo Web do blob de backup. |
> | Ação | Microsoft. Web/sites/restorefromdeletedapp/Action | Restaurar aplicativos Web do aplicativo excluído. |
> | Ação | Microsoft. Web/sites/restoresnapshot/Action | Restaurar instantâneos de aplicativos Web. |
> | Ação | Microsoft. Web/sites/siteextensions/Delete | Excluir extensões de site de aplicativos Web. |
> | Ação | Microsoft. Web/sites/siteextensions/ler | Obter extensões de site de aplicativos Web. |
> | Ação | Microsoft. Web/sites/siteextensions/Write | Atualizar extensões de site de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/analyzecustomhostname/leitura | Obter slots de aplicativos Web analisar nome de host personalizado. |
> | Ação | Microsoft. Web/sites/Slots/applySlotConfig/Action | Aplique a configuração de slot do aplicativo Web do slot de destino ao slot atual. |
> | Ação | Microsoft. Web/sites/Slots/backup/ação | Crie um novo backup de slot de aplicativo Web. |
> | Ação | Microsoft. Web/sites/Slots/backup/leitura | Obter backup de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/backup/gravação | Atualizar backup de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/backups/ação | Descobrir backups de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/backups/excluir | Excluir backups de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/backups/lista/ação | Listar backups de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/backups/leitura | Obter as propriedades de um backup de Slots de um aplicativo Web |
> | Ação | Microsoft. Web/sites/Slots/backups/restauração/ação | Restaurar backups de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/config/Delete | Excluir configuração de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/config/lista/ação | Listar as configurações confidenciais de segurança do slot do aplicativo Web, como credenciais de publicação, configurações do aplicativo e cadeias de conexão |
> | Ação | Microsoft. Web/sites/Slots/configuração/leitura | Obter definições de configuração do slot do aplicativo Web |
> | Ação | Microsoft. Web/sites/Slots/config/Write | Atualizar definições de configuração do slot do aplicativo Web |
> | Ação | Microsoft. Web/sites/Slots/containerlogs/Action | Obter logs de contêiner compactados para o slot do aplicativo Web. |
> | Ação | Microsoft. Web/sites/Slots/containerlogs/download/ação | Baixar logs de contêiner de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/continuouswebjobs/Delete | Excluir trabalhos Web contínuos de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/continuouswebjobs/leitura | Obter trabalhos Web contínuos de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/continuouswebjobs/iniciar/ação | Iniciar trabalhos Web contínuos de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/continuouswebjobs/Stop/Action | Parar trabalhos Web contínuos de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/excluir | Excluir um slot de aplicativo Web existente |
> | Ação | Microsoft. Web/sites/Slots/implantações/excluir | Excluir implantações de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/implantações/log/leitura | Obter o log de implantações de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/implantações/leitura | Obter implantações de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/implantações/gravação | Atualizar implantações de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/detectores/leitura | Obter detectores de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/análise/execução/ação | Executar análise de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/análise/leitura | Obter análise de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/aspnetcore/leitura | Obter diagnóstico de Slots de aplicativos Web para ASP.NET Core aplicativo. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/autoreparo/leitura | Obter o diagnóstico de Slots de aplicativos Web para a rereparo. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/implantação/leitura | Obter implantação de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/implantações/leitura | Obter implantações de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/detectores/execute/Action | Executar o detector de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/detectores/leitura | Obter o detector de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/frebanalysis/leitura | Obter análise de FREB de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/LogAnalyzer/leitura | Obter analisador de log de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/leitura | Obter diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/runtimeavailability/leitura | Obter disponibilidade de tempo de execução de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/perhealth/leitura | Obter integridade do serviço de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/sitecpuanalysis/leitura | Obter análise de CPU do site de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/sitecrashes/leitura | Obter falhas do site de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/sitelatency/leitura | Obter latência do site de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/sitememoryanalysis/leitura | Obter análise de memória do site de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/siterestartsettingupdate/leitura | Obter diagnóstico de Slots de aplicativos Web configurações de reinicialização do site atualizar. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/siterestartuserinitiated/leitura | Obter diagnóstico de Slots de aplicativos Web reiniciar o usuário iniciado. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/siteswap/leitura | Obter troca de site de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/ThreadCount/leitura | Obter contagem de threads de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/disponibilidade/leitura | Obter disponibilidade de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/diagnóstico/workerprocessrecycle/leitura | Obter reciclagem de processo do trabalho de diagnóstico de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/domainownershipidentifiers/leitura | Obter identificadores de propriedade de domínio de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/funções/listsecrets/ação | Listar segredos funções de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/funções/leitura | Obter funções de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/hostnamebindings/Delete | Excluir associações de nome de host de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/hostnamebindings/leitura | Obter associações de nome de host de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/hostnamebindings/gravação | Atualizar associações de nome de host de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/hybridconnection/Delete | Excluir conexão híbrida de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/hybridconnection/leitura | Obter conexão híbrida de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/hybridconnection/gravação | Atualizar a conexão híbrida de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/hybridconnectionnamespaces/retransmissões/excluir | Excluir retransmissões de namespaces de conexão híbrida de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/hybridconnectionnamespaces/retransmissões/gravação | Atualizar retransmissões de namespaces de conexão híbrida de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/hybridconnectionrelays/leitura | Obter retransmissões de conexão híbrida de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/instâncias/implantações/leitura | Obter implantações de instâncias de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/instâncias/processos/excluir | Excluir processos de instâncias de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/instâncias/processos/leitura | Obter processos de instâncias de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/instâncias/leitura | Obter instâncias de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/metricdefinitions/leitura | Obter definições de métrica de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/métricas/leitura | Obter métricas de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/migratemysql/leitura | Obter o MySql migrar slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/networktrace/Action | Slots de aplicativos Web de rastreamento de rede. |
> | Ação | Microsoft. Web/sites/Slots/networktraces/operationresults/leitura | Obter resultados de operação de rastreamento de rede de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/newPassword/Action | Slots de aplicativos Web de NewPassword. |
> | Ação | Microsoft. Web/sites/Slots/operationresults/leitura | Obter resultados da operação de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/operações/leitura | Obter operações de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/perfcounters/leitura | Obter contadores de desempenho de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/phplogging/leitura | Obter Phplogging de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/premieraddons/Delete | Excluir Complementos Premier de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/premieraddons/leitura | Obter Complementos Premier de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/premieraddons/gravação | Atualizar complementos Premier de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/processos/leitura | Obter processos de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/publiccertificates/Delete | Excluir certificados públicos de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/publiccertificates/leitura | Obter certificados públicos de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/publiccertificates/gravação | Criar ou atualizar certificados públicos de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/publicação/ação | Publicar um slot de aplicativo Web |
> | Ação | Microsoft. Web/sites/Slots/PublishXml/Action | Obter XML do perfil de publicação para o slot do aplicativo Web |
> | Ação | Microsoft. Web/sites/Slots/leitura | Obter as propriedades de um slot de implantação de aplicativo Web |
> | Ação | Microsoft. Web/sites/Slots/recuperação/ação | Recuperar slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/resetSlotConfig/Action | Redefinir a configuração do slot do aplicativo Web |
> | Ação | Microsoft. Web/sites/Slots/resourcehealthmetadata/leitura | Obter slots de aplicativos Web Resource Health metadados. |
> | Ação | Microsoft. Web/sites/Slots/reiniciar/ação | Reiniciar um slot de aplicativo Web |
> | Ação | Microsoft. Web/sites/Slots/restaurar/ler | Obter restauração de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/restaurar/gravar | Restaurar slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/restorefrombackupblob/Action | Restaure o slot de aplicativos Web do blob de backup. |
> | Ação | Microsoft. Web/sites/Slots/restorefromdeletedapp/Action | Restaure os slots do aplicativo Web do aplicativo excluído. |
> | Ação | Microsoft. Web/sites/Slots/restoresnapshot/Action | Restaurar instantâneos de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/siteextensions/Delete | Excluir extensões de site de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/siteextensions/leitura | Obter extensões de site de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/siteextensions/gravação | Atualizar extensões de site de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/slotsdiffs/Action | Obter diferenças na configuração entre o aplicativo Web e os slots |
> | Ação | Microsoft. Web/sites/Slots/slotsswap/Action | Trocar slots de implantação de aplicativo Web |
> | Ação | Microsoft. Web/sites/Slots/instantâneos/leitura | Obter instantâneos de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/sourcecontrols/Delete | Excluir definições de configuração de controle do código-fonte do slot do aplicativo Web |
> | Ação | Microsoft. Web/sites/Slots/sourcecontrols/leitura | Obter definições de configuração de controle do código-fonte do slot do aplicativo Web |
> | Ação | Microsoft. Web/sites/Slots/sourcecontrols/gravação | Atualizar definições de configuração de controle do código-fonte do slot do aplicativo Web |
> | Ação | Microsoft. Web/sites/Slots/iniciar/ação | Iniciar um slot de aplicativo Web |
> | Ação | Microsoft. Web/sites/Slots/parar/ação | Parar um slot de aplicativo Web |
> | Ação | Microsoft. Web/sites/Slots/sincronização/ação | Sincronizar slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/triggeredwebjobs/Delete | Excluir os slots de aplicativos Web disparados. |
> | Ação | Microsoft. Web/sites/Slots/triggeredwebjobs/leitura | Obter webjobs disparados de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/triggeredwebjobs/execução/ação | Executar webjobs disparados de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/usos/leitura | Obter usos de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/virtualnetworkconnections/Delete | Excluir conexões de rede virtual de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/virtualnetworkconnections/gateways/gravação | Atualizar gateways de conexões de rede virtual de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/virtualnetworkconnections/leitura | Obter conexões de rede virtual de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/virtualnetworkconnections/gravação | Atualizar conexões de rede virtual de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/trabalhos Web/leitura | Obter webjobs de Slots de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Slots/gravação | Criar um novo slot de aplicativo Web ou atualizar um existente |
> | Ação | Microsoft. Web/sites/slotsdiffs/Action | Obter diferenças na configuração entre o aplicativo Web e os slots |
> | Ação | Microsoft. Web/sites/slotsswap/Action | Trocar slots de implantação de aplicativo Web |
> | Ação | Microsoft. Web/sites/instantâneos/leitura | Obter instantâneos de aplicativos Web. |
> | Ação | Microsoft. Web/sites/sourcecontrols/Delete | Excluir definições de configuração de controle do código-fonte do aplicativo Web |
> | Ação | Microsoft. Web/sites/sourcecontrols/ler | Obter definições de configuração de controle do código-fonte do aplicativo Web |
> | Ação | Microsoft. Web/sites/sourcecontrols/Write | Atualizar as definições de configuração de controle do código-fonte do aplicativo Web |
> | Ação | Microsoft. Web/sites/iniciar/ação | Iniciar um aplicativo Web |
> | Ação | Microsoft. Web/sites/parar/ação | Parar um aplicativo Web |
> | Ação | Microsoft. Web/sites/sincronização/ação | Sincronizar aplicativos Web. |
> | Ação | Microsoft. Web/sites/syncfunctiontriggers/Action | Gatilhos de função de sincronização. |
> | Ação | Microsoft. Web/sites/triggeredwebjobs/Delete | Excluir webjobs disparados de aplicativos Web. |
> | Ação | Microsoft. Web/sites/triggeredwebjobs/histórico/leitura | Obter o histórico de trabalhos Web disparados por aplicativos. |
> | Ação | Microsoft. Web/sites/triggeredwebjobs/ler | Obter webjobs disparados por aplicativos Web. |
> | Ação | Microsoft. Web/sites/triggeredwebjobs/executar/ação | Executar webjobs disparados de aplicativos Web. |
> | Ação | Microsoft. Web/sites/usos/leitura | Obter usos de aplicativos Web. |
> | Ação | Microsoft. Web/sites/virtualnetworkconnections/Delete | Excluir conexões de rede virtual de aplicativos Web. |
> | Ação | Microsoft. Web/sites/virtualnetworkconnections/gateways/leitura | Obter gateways de conexões de rede virtual de aplicativos Web. |
> | Ação | Microsoft. Web/sites/virtualnetworkconnections/gateways/gravação | Atualizar gateways de conexões de rede virtual de aplicativos Web. |
> | Ação | Microsoft. Web/sites/virtualnetworkconnections/ler | Obter conexões de rede virtual de aplicativos Web. |
> | Ação | Microsoft. Web/sites/virtualnetworkconnections/Write | Atualizar conexões de rede virtual de aplicativos Web. |
> | Ação | Microsoft. Web/sites/trabalhos Web/leitura | Obter webjobs de aplicativos Web. |
> | Ação | Microsoft. Web/sites/Write | Criar um novo aplicativo Web ou atualizar um existente |
> | Ação | Microsoft. Web/SKUs/leitura | Obter SKUs. |
> | Ação | Microsoft. Web/sourcecontrols/leitura | Obter controles de origem. |
> | Ação | Microsoft. Web/sourcecontrols/Write | Atualizar controles de origem. |
> | Ação | Microsoft. Web/cancelar registro/ação | Cancele o registro do provedor de recursos Microsoft. Web para a assinatura. |
> | Ação | Microsoft. Web/validar/ação | Verifica. |
> | Ação | Microsoft. Web/verifyhostingenvironmentvnet/Action | Verifique vnet do ambiente de hospedagem. |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Tipo de ação | Operação | Descrição |
> | --- | --- | --- |
> | Ação | Microsoft. WorkloadMonitor/componentes/leitura | Obtém os componentes para o recurso |
> | Ação | Microsoft. WorkloadMonitor/componentsSummary/Read | Obtém o resumo dos componentes |
> | Ação | Microsoft. WorkloadMonitor/monitorInstances/Read | Obtém instâncias de monitores para o recurso |
> | Ação | Microsoft. WorkloadMonitor/monitorInstancesSummary/Read | Obtém o resumo das instâncias do monitor |
> | Ação | Microsoft. WorkloadMonitor/monitores/leitura | Obtém monitores para o recurso |
> | Ação | Microsoft. WorkloadMonitor/monitores/gravação | Configurar o monitor para o recurso |
> | Ação | Microsoft. WorkloadMonitor/notificationSettings/Read | Obtém as configurações de notificação para o recurso |
> | Ação | Microsoft. WorkloadMonitor/notificationSettings/Write | Definir configurações de notificação para o recurso |
> | Ação | Microsoft. WorkloadMonitor/operações/leitura | Obter as operações com suporte |

## <a name="next-steps"></a>Passos seguintes

- [Corresponder provedor de recursos ao serviço](../azure-resource-manager/azure-services-resource-providers.md)
- [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Built-in roles for Azure resources](built-in-roles.md) (Funções incorporadas para recursos do Azure)
