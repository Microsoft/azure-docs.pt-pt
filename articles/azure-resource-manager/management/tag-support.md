---
title: Suporte de etiquetas para recursos
description: Mostra quais os tipos de suporte dos tipos de recursos Azure. Fornece detalhes para todos os serviços azure.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 09dd71ef8c3ac4803a988dffbdca47116c967a0e
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207932"
---
# <a name="tag-support-for-azure-resources"></a>Suporte de etiquetas para recursos Azure
Este artigo descreve se um tipo de recurso suporta [etiquetas](tag-resources.md). As etiquetas de suporte com a etiqueta da coluna com **etiquetas** indica se o tipo de recurso tem uma propriedade para a etiqueta. O relatório de **custos** da coluna rotulada tag indica se esse tipo de recurso passa a etiqueta para o relatório de custos. Pode visualizar os custos através de tags na análise de custos de Gestão de [Custos](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) e na [fatura de faturação Azure e dados de utilização diária.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)

Para obter os mesmos dados que um ficheiro de valores separados de vírem, descarregue [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Salte para um espaço de nome do fornecedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automação](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Genebra](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacidade](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.Serviços Cognitivos](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumo](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Dispositivos](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genómica](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Notebooks](#microsoftnotebooks)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Recursos](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Soluções](#microsoftsolutions)
> - [Microsoft.SpoolService](#microsoftspoolservice)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscrição](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | DomainServices | Sim | Sim |
> | Serviços de Domínio / oucontentor | Não | Não |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | fornecedores de suporte | Não | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | adsupportcases | Não | Não |
> | adiciona serviços | Não | Não |
> | agentes | Não | Não |
> | utilizadores anónimos | Não | Não |
> | configuração | Não | Não |
> | registos | Não | Não |
> | relatórios | Não | Não |
> | servicehealthmetrics | Não | Não |
> | services | Não | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | configurações | Não | Não |
> | gerando Recomendações | Não | Não |
> | do IdP | Não | Não |
> | recomendações | Não | Não |
> | supressões | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | actionRules | Sim | Sim |
> | alerts | Não | Não |
> | alertasList | Não | Não |
> | alertasMetaData | Não | Não |
> | alertasResumo | Não | Não |
> | alertasSummaryList | Não | Não |
> | feedback | Não | Não |
> | smartDetectorAlertRules | Sim | Sim |
> | smartDetectorRuntimeEnvironments | Não | Não |
> | smartGroups | Não | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | servidores | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | relatórioFeedback | Não | Não |
> | serviço | Sim | Sim |
> | validateServiceName | Não | Não |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | configuraçãoLojas | Sim | Sim |
> | configuraçãoStores / eventoSRedesFiltros | Não | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Spring | Sim | Sim |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Não | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | administradores clássicos | Não | Não |
> | dataAliases | Não | Não |
> | negarAtribuições | Não | Não |
> | elevateAccess | Não | Não |
> | encontrarAtribuiçãos DeRoleDeórfãos | Não | Não |
> | fechaduras | Não | Não |
> | permissões | Não | Não |
> | políticasAtribuis | Não | Não |
> | definições políticas | Não | Não |
> | policySetDefinitions | Não | Não |
> | providerOperations | Não | Não |
> | roleAssignments | Não | Não |
> | roleAssignmentsUsageMetrics | Não | Não |
> | roleDefinitions | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | automatizaçõesContas | Sim | Sim |
> | automatizaçãoContas / configurações | Sim | Sim |
> | automaçãoContas / empregos | Não | Não |
> | automaçãoContas / livros de execução | Sim | Sim |
> | automatizaçõesContas / softwareUpdateConfiguras | Não | Não |
> | automatizaçãoContas / webhooks | Não | Não |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | configuraçãoLojas | Sim | Sim |
> | configuraçãoStores / eventoSRedesFiltros | Não | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | ambientes | Não | Não |
> | ambientes / contas | Não | Não |
> | ambientes / contas / espaços de nome | Não | Não |
> | ambientes / contas / espaços de nome / configurações | Não | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Sim | Não |
> | b2ctenants | Não | Não |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | híbridoDataManagers | Sim | Sim |
> | postgresInstances | Sim | Sim |
> | sqlBigDataClusters | Sim | Sim |
> | sqlInstances | Sim | Sim |
> | sqlServerRegistrations | Sim | Sim |
> | sqlServerRegistrations / sqlServerS | Não | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | registos | Sim | Sim |
> | registos / assinaturas de clientes | Não | Não |
> | registos / produtos | Não | Não |
> | verificaçãoKeys | Não | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Sim | Sim |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Contas de Faturação | Não | Não |
> | contas/acordos | Não | Não |
> | billingAccounts / billingPermissions | Não | Não |
> | contas de faturação / billingPerfis | Não | Não |
> | billingAccounts / billingPerfis / billingPermissions | Não | Não |
> | billingAccounts / billingPerfis / billingRoleAssignments | Não | Não |
> | billingAccounts / billingPerfis / billingRoleDefinitions | Não | Não |
> | contas de faturação / faturaçãoPerfis / faturaçõesAssinaturas | Não | Não |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Não | Não |
> | contas de faturação / faturaçãoPerfis / clientes | Não | Não |
> | contas de faturação / faturaçãoPerfis / instruções | Não | Não |
> | contas de faturação / faturaçãoPerfis / faturas | Não | Não |
> | faturaçãoContas / faturaçãoPerfis / faturas / folha de preço | Não | Não |
> | faturaçãoContas / faturaçãoPerfis / faturas / transações | Não | Não |
> | faturasContas / faturaçãoPerfis / faturasSecs | Não | Não |
> | billingAccounts / faturaçãoPerfis / faturaS / billingPermissions | Não | Não |
> | billingAccounts / faturaçãoPerfis / faturaS / billingRoleAssignments | Não | Não |
> | billingAccounts / faturaçãoPerfis / faturaS / faturaçõesRoleDefinições | Não | Não |
> | faturasContas / faturaçãoPerfis / faturaS / faturaçõesAssinaturas | Não | Não |
> | billingAccounts / faturaçãoPerfis / faturaS / criarBillingRoleAssignment | Não | Não |
> | faturasContas / faturaçãoPerfis / faturaS / iniciarTransferência | Não | Não |
> | faturasContas / faturaçãoPerfis / faturaS / produtos | Não | Não |
> | faturasContas / faturaçãoPerfis / faturaS / produtos / transferência | Não | Não |
> | faturaçãoContas / faturaçãoPerfis / faturaS / produtos / actualizaçõesAutoRenew | Não | Não |
> | faturasContas / faturaçãoPerfis / faturaS /transações | Não | Não |
> | faturasContas / faturaçãoPerfis / faturaS / transferências | Não | Não |
> | contas de faturação / BillingProfiles / patchOperations | Não | Não |
> | contas de faturação / faturaçãoPerfis / pagamentoMétodos | Não | Não |
> | billingAccounts / billingPerfis / políticas | Não | Não |
> | contas de faturação / faturaçãoPerfis / folha de preço | Não | Não |
> | faturaçãoContas / faturaçãoPerfis / folha de preçoDownloadOperations | Não | Não |
> | contas de faturação / faturaçãoPerfis / produtos | Não | Não |
> | contas de faturação / faturaçãoPerfis / transações | Não | Não |
> | billingAccounts / billingRoleAssignments | Não | Não |
> | billingAccounts / faturaçãoRoleDefinis | Não | Não |
> | contas de faturação / assinaturas de faturação | Não | Não |
> | faturasContas / faturaçãoSubscrições / faturas | Não | Não |
> | billingAccounts / criarBillingRoleAssignment | Não | Não |
> | contas de faturação / criaçãoDeOperações deSecção de Faturas | Não | Não |
> | contas / clientes | Não | Não |
> | contas de faturação / clientes / permissões de faturação | Não | Não |
> | faturaçãoContas / clientes / faturaçõesAssinaturas | Não | Não |
> | faturaçãoContas / clientes / iniciarTransferência | Não | Não |
> | faturasContas / clientes / políticas | Não | Não |
> | faturasContas / clientes / produtos | Não | Não |
> | contas / clientes / transações | Não | Não |
> | faturasContas / clientes / transferências | Não | Não |
> | contas/departamentos | Não | Não |
> | contas de faturação /inscriçõesContas | Não | Não |
> | contas de faturação / faturas | Não | Não |
> | contas de faturação / faturasSecs | Não | Não |
> | faturaS / faturaS / faturaçãoSubscriptionMoveOperations | Não | Não |
> | contas de faturação / faturaS / faturaçõesAssinaturas | Não | Não |
> | faturasContas / faturaS / faturaçãoSubscrições / transferência | Não | Não |
> | contas de faturas / faturaS / elevação | Não | Não |
> | faturasContas / faturaS / iniciarTransferência | Não | Não |
> | faturasContas / faturaS / patchOperations | Não | Não |
> | faturasContas / faturaS / produtoMoveOperations | Não | Não |
> | contas de faturas / faturaS / produtos | Não | Não |
> | faturasContas / faturaS / produtos / transferência | Não | Não |
> | faturaS / faturaS / produtos / actualizaçãoAutoRenovar | Não | Não |
> | contas de faturas / faturaS / transações | Não | Não |
> | faturasContas / faturaS / transferências | Não | Não |
> | contas de faturação / linhaOfCredit | Não | Não |
> | contas de faturação / patchOperations | Não | Não |
> | contas de faturação / pagamentoMétodos | Não | Não |
> | contas de faturação / produtos | Não | Não |
> | contas/transações | Não | Não |
> | períodos de faturação | Não | Não |
> | billingPermissions | Não | Não |
> | billingProperty | Não | Não |
> | billingRoleAssignments | Não | Não |
> | billingRoleDefinis | Não | Não |
> | criar BillingRoleAssignment | Não | Não |
> | Departamentos | Não | Não |
> | enrollmentAccounts | Não | Não |
> | faturas | Não | Não |
> | Transferências | Não | Não |
> | transferências / aceitarTransferência | Não | Não |
> | transferências /declínioTransferência | Não | Não |
> | transferências / operaçãoStatus | Não | Não |
> | transferências / validaçãoTransferência | Não | Não |
> | validaendereçoAddress | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | mapApis | Sim | Sim |
> | updateCommunicationPreference | Não | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | blockchainMembros | Sim | Sim |
> | cordaMembros | Sim | Sim |
> | observadores | Sim | Sim |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Não | Não |
> | blueprintAssignments / atribuiçãoOperações | Não | Não |
> | blueprintAssignments / operações | Não | Não |
> | plantas | Não | Não |
> | plantas / artefactos | Não | Não |
> | plantas / versões | Não | Não |
> | plantas / versões / artefactos | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | botServices | Sim | Sim |
> | botServices / canais | Não | Não |
> | botServiços / conexões | Não | Não |
> | Idiomas | Não | Não |
> | modelos | Não | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Redis | Sim | Sim |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicaçõesReservas | Não | Não |
> | autoCotIncrease | Não | Não |
> | calcularTroca | Não | Não |
> | calcularPreço | Não | Não |
> | calcularComprarPreço | Não | Não |
> | catálogos | Não | Não |
> | commercialReservationOrders | Não | Não |
> | troca | Não | Não |
> | placeBuyOrder | Não | Não |
> | reservasOrdens | Não | Não |
> | reservasOrdens / calcular Reembolso | Não | Não |
> | reservasOrdens / fusão | Não | Não |
> | reservasEncomendas / reservas | Não | Não |
> | reservasEncomendas / reservas / revisões | Não | Não |
> | reservasOrdens / devolução | Não | Não |
> | reservasOrdens / divisão | Não | Não |
> | reservasOrdens / troca | Não | Não |
> | reservas | Não | Não |
> | recursosFornecedores | Não | Não |
> | recursos | Não | Não |
> | validateReservationOrder | Não | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Não | Não |
> | CdnWebApplicationFirewallPolicies | Sim | Sim |
> | talhenos | Não | Não |
> | profiles | Sim | Sim |
> | perfis / pontos finais | Sim | Sim |
> | perfis / pontos finais / domínios personalizados | Não | Não |
> | perfis / pontos finais / origens | Não | Não |
> | validateProbe | Não | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | certificadoSOrdens | Sim | Sim |
> | certificadoS / certificados | Não | Não |
> | validateCertificateRegistrationInformation | Não | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | capacidades | Não | Não |
> | domainNames | Não | Não |
> | domínioNomes / capacidades | Não | Não |
> | domínioNames / InternalLoadBalancers | Não | Não |
> | domínioNomes / serviçoCertificados | Não | Não |
> | domínioNomes / slots | Não | Não |
> | domínioNomes / slots / funções | Não | Não |
> | domínioNomes / slots / funções / definições métricas | Não | Não |
> | domínioNomes / slots / papéis / métricas | Não | Não |
> | moveSubscriptionResources | Não | Não |
> | operatingSystemFamilies | Não | Não |
> | operatingSystems | Não | Não |
> | quotas | Não | Não |
> | recursosTipos | Não | Não |
> | validateSubscriptionMoveAvailability | Não | Não |
> | máquinas virtuais | Não | Não |
> | virtualMáquinas / diagnósticoDefinições | Não | Não |
> | virtualMáquinas / definições métricas | Não | Não |
> | máquinas virtuais / métricas | Não | Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | recursos clássicos Infraestruturas | Não | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | capacidades | Não | Não |
> | expressRouteCrossConnections | Não | Não |
> | expressRouteCrossConnections / peerings | Não | Não |
> | gatewayDispositivos suportados | Não | Não |
> | networkSecurityGroups | Não | Não |
> | quotas | Não | Não |
> | reservedIps | Não | Não |
> | redes virtuais | Não | Não |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Não | Não |
> | redes virtuais / virtualNetworkPeerings | Não | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | capacidades | Não | Não |
> | discos | Não | Não |
> | imagens | Não | Não |
> | osImages | Não | Não |
> | osPlatformImages | Não | Não |
> | publicImages | Não | Não |
> | quotas | Não | Não |
> | storageAccounts | Não | Não |
> | armazenamentoContas / blobServices | Não | Não |
> | armazenamentoContas / ficheirosServiços | Não | Não |
> | armazenamentoContas / definições métricas | Não | Não |
> | armazenamentoContas / métricas | Não | Não |
> | armazenamentoContas / filaServiços | Não | Não |
> | armazenamentoContas / serviços | Não | Não |
> | armazenamentoContas / serviços / diagnósticoDefinições | Não | Não |
> | armazenamentoContas / serviços / definições métricas | Não | Não |
> | armazenamentoContas / serviços / métricas | Não | Não |
> | armazenamentoContas / tabelaServiços | Não | Não |
> | armazenamentoContas / vmImages | Não | Não |
> | vmImages | Não | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | RateCard | Não | Não |
> | UsageAggregates | Não | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | disponibilidadeConjuntos | Sim | Sim |
> | diskEncryptionSets | Sim | Sim |
> | discos | Sim | Sim |
> | galerias | Sim | Sim |
> | galerias / aplicações | Não | Não |
> | galerias / aplicações / versões | Não | Não |
> | galerias / imagens | Não | Não |
> | galerias / imagens / versões | Não | Não |
> | grupos de anfitriões | Sim | Sim |
> | grupos de anfitriões / anfitriões | Sim | Sim |
> | imagens | Sim | Sim |
> | proximidadeGrupoS | Sim | Sim |
> | restorePointCollections | Sim | Sim |
> | restaurarPointCollections / restaurarPontos | Não | Não |
> | sharedVMImages | Sim | Sim |
> | vMImages /versões partilhadas | Não | Não |
> | instantâneos | Sim | Sim |
> | máquinas virtuais | Sim | Sim |
> | virtualMáquinas / extensões | Sim | Sim |
> | virtualMáquinas / definições métricas | Não | Não |
> | virtualMachineScaleSets | Sim | Sim |
> | virtualMachineScaleSets / extensões | Não | Não |
> | virtualMachineScaleSets / NetworkInterfaces | Não | Não |
> | virtualMachineScaleSets / publicIPAddresses | Não | Não |
> | virtualMachineScaleSets / virtualMachines | Não | Não |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Não | Não |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | AgregadoCost | Não | Não |
> | Saldos | Não | Não |
> | Orçamentos | Não | Não |
> | Cobranças | Não | Não |
> | CostTags | Não | Não |
> | créditos | Não | Não |
> | eventos | Não | Não |
> | Previsões | Não | Não |
> | lotes | Não | Não |
> | Marketplaces | Não | Não |
> | Tabelas de preços | Não | Não |
> | produtos | Não | Não |
> | ReservationDetails | Não | Não |
> | ReservationRecommendations | Não | Não |
> | ReservasResumos | Não | Não |
> | ReservationTransactions | Não | Não |
> | Etiquetas | Não | Não |
> | inquilinos | Não | Não |
> | Termos | Não | Não |
> | UsageDetails | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | grupos de contentores | Sim | Sim |
> | serviceAssociationLinks | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | registos | Sim | Sim |
> | registos / construções | Não | Não |
> | registos / construções / cancelamento | Não | Não |
> | registos / construções / getLogLink | Não | Não |
> | registos / buildTasks | Sim | Sim |
> | registos / construçãoTarefas / passos | Não | Não |
> | registos / eventoSGridFilters | Não | Não |
> | registos / geraçõesCredenciais | Não | Não |
> | registos / getBuildSourceUploadUrl | Não | Não |
> | registos / GetCredenciais | Não | Não |
> | registos / importaçãoImagem | Não | Não |
> | registos / privateEndpointConnectionProxies | Não | Não |
> | registos / privateEndpointConnectionProxies / validar | Não | Não |
> | registos / privateLinkResources | Não | Não |
> | registos / filaConstruir | Não | Não |
> | registos / regeneraçãoCredential | Não | Não |
> | registos / regenerações Credenciais | Não | Não |
> | registos / replicações | Sim | Sim |
> | registos / execuções | Não | Não |
> | registos / execuções / cancelar | Não | Não |
> | registos / horárioSRun | Não | Não |
> | registos / scopeMaps | Não | Não |
> | registos / taskRuns | Sim | Sim |
> | registos / tarefas | Sim | Sim |
> | registos / fichas | Não | Não |
> | registos / atualizaçãoPolíticas | Não | Não |
> | registos / webhooks | Sim | Sim |
> | registos / webhooks / getCallbackConfig | Não | Não |
> | registos / webhooks / ping | Não | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | containerServices | Sim | Sim |
> | clusters geridos | Sim | Sim |
> | openShiftManagedClusters | Sim | Sim |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Alertas | Não | Não |
> | Contas de Faturação | Não | Não |
> | Orçamentos | Não | Não |
> | CloudConnectors | Não | Não |
> | Conectores | Sim | Sim |
> | Departamentos | Não | Não |
> | Dimensões | Não | Não |
> | Contas de Inscrição | Não | Não |
> | Port | Não | Não |
> | Contas de Faturação Externa | Não | Não |
> | Contas /Alertas de Faturação Externa | Não | Não |
> | Contas /Dimensões de Faturação Externa | Não | Não |
> | Contas de Faturação Externa / Previsão | Não | Não |
> | Contas de Faturação Externa / Consulta | Não | Não |
> | Subscrições Externas | Não | Não |
> | Subscrições Externas / Alertas | Não | Não |
> | Subscrições Externas / Dimensões | Não | Não |
> | Subscrições Externas / Previsão | Não | Não |
> | Subscrições Externas / Consulta | Não | Não |
> | Previsão | Não | Não |
> | Consulta | Não | Não |
> | registar | Não | Não |
> | Reportconfigs | Não | Não |
> | Relatórios | Não | Não |
> | Definições | Não | Não |
> | showbackRules | Não | Não |
> | Vistas | Não | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | pedidos | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | associações | Não | Não |
> | recursosFornecedores | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Empregos | Sim | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Sim | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | espaços de trabalho | Sim | Não |
> | espaços de trabalho / dbWorkspaces | Não | Não |
> | espaços de trabalho / armazenamentoEncriptação | Não | Não |
> | espaços de trabalho / virtualNetworkPeerings | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | catálogos | Sim | Sim |
> | catálogos de dados | Sim | Sim |
> | datacatalogs /datasources | Não | Não |
> | datacatalogs / datasources / digitalizações | Não | Não |
> | datacatalogs / datasources / digitalizações / conjuntos de dados | Não | Não |
> | datacatalogs / datasources / digitalizações / gatilhos | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | dataFábricas | Sim | Não |
> | dataFactories / diagnósticoDefinições | Não | Não |
> | dataFábricas / definições métricas | Não | Não |
> | dataFactorySchema | Não | Não |
> | fábricas | Sim | Não |
> | fábricas / integraçãoTempos | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas / dadosLakeStoreAccounts | Não | Não |
> | contas / armazenamentoContas | Não | Não |
> | contas / armazenamentoContas / contentores | Não | Não |
> | contas / transferênciaAnalyticsUnits | Não | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas / eventoSGridFilters | Não | Não |
> | contas / firewallRules | Não | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | services | Não | Não |
> | serviços / projetos | Não | Não |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas / ações | Não | Não |
> | contas / ações / conjuntos de dados | Não | Não |
> | contas / ações / convites | Não | Não |
> | contas / ações / assinaturas de partilhas de fornecedores | Não | Não |
> | contas / ações / sincronizaçãoDefinições | Não | Não |
> | contas / subscrições de ações | Não | Não |
> | contas / subscrições de ações / consumerSourceDataSets | Não | Não |
> | contas / subscrições de ações / datasetmappings | Não | Não |
> | contas / subscrições de ações / gatilhos | Não | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | servidores | Sim | Sim |
> | servidores /conselheiros | Não | Não |
> | servidores / teclas | Não | Não |
> | servidores / privateEndpointConnectionProxies | Não | Não |
> | servidores / privateEndpointConnections | Não | Não |
> | servidores / privateLinkResources | Não | Não |
> | servidores / consultaTextos | Não | Não |
> | servidores / Servidores recuperáveis | Não | Não |
> | servidores / topQueryStatistics | Não | Não |
> | servidores / virtualNetworkRules | Não | Não |
> | servidores / estatísticas de espera | Não | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | servidores | Sim | Sim |
> | servidores /conselheiros | Não | Não |
> | servidores / teclas | Não | Não |
> | servidores / privateEndpointConnectionProxies | Não | Não |
> | servidores / privateEndpointConnections | Não | Não |
> | servidores / privateLinkResources | Não | Não |
> | servidores / consultaTextos | Não | Não |
> | servidores / Servidores recuperáveis | Não | Não |
> | servidores / topQueryStatistics | Não | Não |
> | servidores / virtualNetworkRules | Não | Não |
> | servidores / estatísticas de espera | Não | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | servidorGrupos | Sim | Sim |
> | servidores | Sim | Sim |
> | servidores /conselheiros | Não | Não |
> | servidores / teclas | Não | Não |
> | servidores / privateEndpointConnectionProxies | Não | Não |
> | servidores / privateEndpointConnections | Não | Não |
> | servidores / privateLinkResources | Não | Não |
> | servidores / consultaTextos | Não | Não |
> | servidores / Servidores recuperáveis | Não | Não |
> | servidores / topQueryStatistics | Não | Não |
> | servidores / virtualNetworkRules | Não | Não |
> | servidores / estatísticas de espera | Não | Não |
> | serversv2 | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | artefactoFonteS | Sim | Sim |
> | lançamentos | Sim | Sim |
> | serviçoTopos de tempo | Sim | Sim |
> | serviçoSTopologia / serviços | Sim | Sim |
> | serviçoTopos / serviços / serviçoSUnidades | Sim | Sim |
> | passos | Sim | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | grupos de aplicações | Sim | Sim |
> | grupos de aplicações/aplicações | Não | Não |
> | grupos de aplicações / desktops | Não | Não |
> | grupos de aplicações / startmenuitems | Não | Não |
> | piscinas de acolhimento | Sim | Sim |
> | hostpools / anfitriões de sessões | Não | Não |
> | hostpools / sessões de sessões / usersessions | Não | Não |
> | hostpools / usersessions | Não | Não |
> | espaços de trabalho | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Piscinas Elásticas | Sim | Sim |
> | ElasticPools / IotHubTenants | Sim | Sim |
> | ElasticPools / IotHubTenants / securitySettings | Não | Não |
> | IotHubs | Sim | Sim |
> | IotHubs / eventoSGridFilters | Não | Não |
> | IotHubs / definições de segurança | Não | Não |
> | serviços de provisionamento | Sim | Sim |
> | utilizações | Não | Não |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | oleodutos | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | controladores | Sim | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | centros de laboratório | Sim | Sim |
> | laboratórios | Sim | Sim |
> | laboratórios / ambientes | Sim | Sim |
> | laboratórios / serviceRunners | Sim | Sim |
> | laboratórios / máquinas virtuais | Sim | Sim |
> | horários | Sim | Sim |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Não | Não |
> | databaseAccounts | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | domínios | Sim | Sim |
> | domínios / domainOwnershipIdentifiers | Não | Não |
> | generateSsoRequest | Não | Não |
> | topLevelDomains | Não | Não |
> | validateDomainRegistrationInformation | Não | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | projetos de lcsprojects | Não | Não |
> | lcsprojects / clouddeployments | Não | Não |
> | lcsprojects / conectores | Não | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | domínios | Sim | Sim |
> | domínios / tópicos | Não | Não |
> | eventoSSubscrições | Não | Não |
> | extensionTopics | Não | Não |
> | partnerNamespaces | Sim | Sim |
> | partnerNamespaces / eventoChannels | Não | Não |
> | parceiroSRegistrations | Sim | Sim |
> | parceiroTópicos | Sim | Sim |
> | sistemaTópicos | Sim | Sim |
> | sistemaTópicos / eventosSubscrições | Não | Não |
> | tópicos | Sim | Sim |
> | topicTypes | Não | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Sim | Sim |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / regras de autorização | Não | Não |
> | espaços de nomes / desastres recoveryconfigs | Não | Não |
> | espaços de nomes / centros de eventos | Não | Não |
> | espaços de nome / eventhubs / regras de autorização | Não | Não |
> | espaços de nome / eventhubs / grupos de consumidores | Não | Não |
> | espaços de nome / conjuntos de regras de rede | Não | Não |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | características | Não | Não |
> | Fornecedores | Não | Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | inscrever | Não | Não |
> | galeriaitas | Não | Não |
> | generateartifactaccessuri | Não | Não |
> | myareas | Não | Não |
> | myares / áreas | Não | Não |
> | myares / áreas / áreas | Não | Não |
> | myares / áreas / áreas / galeriaites | Não | Não |
> | myareas / áreas / galleryitems | Não | Não |
> | myareas / galleryitems | Não | Não |
> | registar | Não | Não |
> | recursos | Não | Não |
> | recuperrecursoside | Não | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | autoGeridosAccounts | Sim | Sim |
> | autoGeridoVmConfigurationPerfis | Sim | Sim |
> | configuraçãoPerfilAssignments | Não | Não |
> | guestConfigurationAssignments | Não | Não |
> | software | Não | Não |
> | softwareUpdateProfile | Não | Não |
> | softwareUpdates | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Sim | Sim |
> | sapMonitors | Sim | Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | hsms dedicados | Sim | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Sim | Sim |
> | clusters / aplicações | Não | Não |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | services | Sim | Sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | máquinas | Sim | Sim |
> | máquinas / extensões | Sim | Sim |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | gestores de dados | Sim | Sim |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | componentes | Sim | Sim |
> | redeScopes | Sim | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Empregos | Sim | Sim |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | diagnósticoDefinições | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | modelos de aplicativos | Não | Não |
> | IoTApps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Graph | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Não | Não |
> | hsmPools | Sim | Sim |
> | cofres | Sim | Sim |
> | cofres / accessPolicies | Não | Não |
> | cofres / eventoSGridFilters | Não | Não |
> | cofres / segredos | Não | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Sim | Sim |
> | clusters / configurações de bases de dados anexadas | Não | Não |
> | clusters / bases de dados | Não | Não |
> | clusters / bases de dados / ligações de dados | Não | Não |
> | clusters / bases de dados / eventhubconnections | Não | Não |
> | clusters / bases de dados / principais atribuições | Não | Não |
> | clusters / principados | Não | Não |
> | clusters / identidades partilhadas | Não | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | contas de laboratório | Sim | Sim |
> | utilizadores | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | hospedagemAmbientes | Sim | Sim |
> | integraçõesContas | Sim | Sim |
> | integraçãoServiçosAmbientes | Sim | Sim |
> | integraçãoServiceEnvironments / managedApis | Sim | Sim |
> | ambientes isolados | Sim | Sim |
> | fluxos de trabalho | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | compromissoPlanos | Sim | Sim |
> | webServices | Sim | Sim |
> | Áreas de Trabalho | Sim | Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | espaços de trabalho | Sim | Sim |
> | espaços de trabalho / computação | Não | Não |
> | espaços de trabalho / eventoSGridFilters | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Identities | Não | Não |
> | userAssignedIdentities | Sim | Sim |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinições | Não | Não |
> | inscriçõesAtribuições | Não | Não |
> | inscriçõesDefinições | Não | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | getEntities | Não | Não |
> | grupos de gestão | Não | Não |
> | recursos | Não | Não |
> | startTenantBackfill | Não | Não |
> | inquilinoBackfillStatus | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas / eventoSGridFilters | Não | Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | ofertas | Não | Não |
> | ofertaTipos | Não | Não |
> | ofertaTipos / editores | Não | Não |
> | ofertaTipos / editores / ofertas | Não | Não |
> | ofertaTipos / editores / ofertas / planos | Não | Não |
> | ofertaTipos / editores / ofertas / planos / acordos | Não | Não |
> | ofertaTipos / editores / ofertas / planos / configs | Não | Não |
> | ofertaTipos / editores / ofertas / planos / configs / importImage | Não | Não |
> | privategalleryitems | Não | Não |
> | privateStoreClient | Não | Não |
> | produtos | Não | Não |
> | editores | Não | Não |
> | editoras / ofertas | Não | Não |
> | editores / ofertas / alterações | Não | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Sim | Sim |
> | updateCommunicationPreference | Não | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | acordos | Não | Não |
> | ofertas | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | serviços de media | Sim | Sim |
> | mediaservices / contaFilters | Não | Não |
> | serviços de media / ativos | Não | Não |
> | mediaservices / ativos / activosFilters | Não | Não |
> | mediaservices / contentKeyPolicies | Não | Não |
> | mediaservices / eventoSGridFilters | Não | Não |
> | mediaservices / liveEventOperations | Não | Não |
> | mediaservices / liveEvents | Sim | Sim |
> | mediaservices / liveEvents / liveOutputs | Não | Não |
> | mediaservices / liveOutputOperations | Não | Não |
> | mediaservices / mediaGraphs | Não | Não |
> | mediaservices / streamingEndpointOperations | Não | Não |
> | mediaservices / streamingPontos de finalidade | Sim | Sim |
> | mediaservices / streamingLocators | Não | Não |
> | mediaservices / streamingPolíticas | Não | Não |
> | mediaservices / transforma | Não | Não |
> | mediaservices / transforma /empregos | Não | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | appClusters | Sim | Sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | avaliaçãoProjetos | Sim | Sim |
> | projetos migratórios | Sim | Sim |
> | moveCollections | Sim | Sim |
> | projetos | Sim | Sim |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | holográficosBroadcastAccounts | Sim | Sim |
> | objetoUnderstandingAccounts | Sim | Sim |
> | remotoSRenderingAccounts | Sim | Sim |
> | spatialAnchorsAccounts | Sim | Sim |
> | surfaceReconstructionAccounts | Sim | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Sim | Não |
> | netAppAccounts / capacityPools | Sim | Não |
> | netAppAccounts / capacityPools / volumes | Sim | Não |
> | netAppAccounts / capacityPools / volumes / mountTargets | Sim | Não |
> | netAppAccounts / capacityPools / volumes / instantâneos | Sim | Não |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Sim | Sim |
> | aplicaçãoGatewayWebApplicationFirewallPolicies | Sim | Sim |
> | applicationSecurityGroups | Sim | Sim |
> | azureFirewallFqdnTags | Não | Não |
> | azureFirewalls | Sim | Não |
> | bastionHosts | Sim | Sim |
> | bgpServiceCommunities | Não | Não |
> | conexões | Sim | Sim |
> | ddosCustomPolicies | Sim | Sim |
> | ddosProtectionPlans | Sim | Sim |
> | dnsOperationStatuses | Não | Não |
> | zonas de dnszone | Sim | Sim |
> | zonas de dnszones / A | Não | Não |
> | dnszones / AAAA | Não | Não |
> | zonas de dnszones / todos | Não | Não |
> | dnszones / CAA | Não | Não |
> | dnszones / CNAME | Não | Não |
> | dnszones / MX | Não | Não |
> | dnszones / NS | Não | Não |
> | dnszones / PTR | Não | Não |
> | dnszones / conjuntos de recordes | Não | Não |
> | dnszones / SOA | Não | Não |
> | dnszones / SRV | Não | Não |
> | dnszones / TXT | Não | Não |
> | expressRouteCircuits | Sim | Sim |
> | expressRouteCrossConnections | Sim | Sim |
> | expressRouteGateways | Sim | Sim |
> | expressRoutePorts | Sim | Sim |
> | expressRouteServiceProviders | Não | Não |
> | firewallPolíticas | Sim | Sim |
> | portas da frente | Sim, mas limitado (ver [nota abaixo)](#frontdoor) | Sim |
> | frontdoorWebApplicationFirewallManagedRuleSets | Sim, mas limitado (ver [nota abaixo)](#frontdoor) | Não |
> | frontdoorWebApplicationFirewallPolicies | Sim, mas limitado (ver [nota abaixo)](#frontdoor) | Sim |
> | getDnsResourceReference | Não | Não |
> | internalNotify | Não | Não |
> | loadBalancers | Sim | Não |
> | localNetworkGateways | Sim | Sim |
> | natGateways | Sim | Sim |
> | networkIntentPolicies | Sim | Sim |
> | networkInterfaces | Sim | Sim |
> | perfis de rede | Sim | Sim |
> | networkSecurityGroups | Sim | Sim |
> | networkWatchers | Sim | Não |
> | networkWatchers / connectionMonitors | Sim | Não |
> | networkWatchers / lentes | Sim | Não |
> | networkWatchers / pingMeshes | Sim | Não |
> | p2sVpnGateways | Sim | Sim |
> | privateDnsOperationStatuses | Não | Não |
> | privateDnsZones | Sim | Sim |
> | privateDnsZones / A | Não | Não |
> | privateDnsZones / AAAAA | Não | Não |
> | privateDnsZones / todos | Não | Não |
> | privateDnsZones / CNAME | Não | Não |
> | privateDnsZones / MX | Não | Não |
> | privateDnsZones / PTR | Não | Não |
> | privateDnsZones / SOA | Não | Não |
> | privateDnsZones / SRV | Não | Não |
> | privateDnsZones / TXT | Não | Não |
> | privateDnsZones / virtualNetworkLinks | Sim | Sim |
> | privateEndpoints | Sim | Sim |
> | privateLinkServices | Sim | Sim |
> | publicIPAddresses | Sim | Sim |
> | publicIPPrefixes | Sim | Sim |
> | routeFilters | Sim | Sim |
> | routeTables | Sim | Sim |
> | serviceEndpointPolicies | Sim | Sim |
> | trafficManagerGeographicHierarchies | Não | Não |
> | perfis de gestor de tráfego | Sim | Sim |
> | trafficmanagerprofiles/heatMaps | Não | Não |
> | tráfegoManagerUserMetricsKeys | Não | Não |
> | virtualHubs | Sim | Sim |
> | virtualNetworkGateways | Sim | Sim |
> | redes virtuais | Sim | Sim |
> | virtualNetworkTaps | Sim | Sim |
> | virtualWans | Sim | Sim |
> | vpnGateways | Sim | Não |
> | vpnSites | Sim | Sim |
> | webApplicationFirewallPolicies | Sim | Sim |

<a id="frontdoor" />

> [!NOTE]
> Para o Serviço de Porta Frontal Azure, pode aplicar etiquetas na criação do recurso, mas atualizar ou adicionar tags não é suportado atualmente.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | espaços de nome | Sim | Não |
> | espaços de nome / notificationHubs | Sim | Não |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | espaços osName | Sim | Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Hipervsites | Sim | Sim |
> | Sites de Importação | Sim | Sim |
> | Sites de servidores | Sim | Sim |
> | Sites VMware | Sim | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Sim | Sim |
> | linkTargets | Não | Não |
> | storageInsightConfigs | Não | Não |
> | espaços de trabalho | Sim | Sim |
> | espaços de trabalho / dadosExportações | Não | Não |
> | espaços de trabalho / dataFontes | Não | Não |
> | espaços de trabalho / serviços ligados | Não | Não |
> | espaços de trabalho / privateEndpointConnectionProxies | Não | Não |
> | espaços de trabalho / privateEndpointConnections | Não | Não |
> | espaços de trabalho / privateLinkResources | Não | Não |
> | espaços de trabalho / consulta | Não | Não |
> | espaços de trabalho / PrivateLinkProxies com âmbito | Não | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | associações de gestão | Não | Não |
> | configurações de gestão | Sim | Sim |
> | soluções | Sim | Sim |
> | Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Não | Não |
> | peerAsns | Não | Não |
> | os pares | Sim | Sim |
> | peeringServiceProviders | Não | Não |
> | peeringServices | Sim | Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | políticaEventos | Não | Não |
> | políticaMetametada | Não | Não |
> | políticasEstados | Não | Não |
> | policyTrackedResources | Não | Não |
> | reparações | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | consolas | Não | Não |
> | dashboards | Sim | Sim |
> | userSettings | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | capacidades | Sim | Sim |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Não | Não |
> | cofres | Sim | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / regras de autorização | Não | Não |
> | espaços de nome / ligações híbridas | Não | Não |
> | espaços de nome / ligações híbridas / regras de autorização | Não | Não |
> | espaços de nome / wcfrelays | Não | Não |
> | espaços de nome / wcfrelays / regras de autorização | Não | Não |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Não | Não |
> | Coleções | Sim | Sim |
> | coleções/aplicações | Não | Não |
> | coleções /diretores de segurança | Não | Não |
> | modeloImages | Não | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | consultas de | Sim | Sim |
> | recursosChangeDetails | Não | Não |
> | recursosAlterações | Não | Não |
> | recursos | Não | Não |
> | recursosHistória | Não | Não |
> | subscriptionsStatus | Não | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | disponibilidadeStatuses | Não | Não |
> | childAvailabilityStatuses | Não | Não |
> | recursos infantis | Não | Não |
> | questões emergentes | Não | Não |
> | eventos | Não | Não |
> | impactedResources | Não | Não |
> | do IdP | Não | Não |
> | notificações | Não | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | implementações | Sim | Não |
> | destacamentos /operações | Não | Não |
> | implementaçãoScripts | Sim | Sim |
> | implementaçãoScripts / registos | Não | Não |
> | ligações | Não | Não |
> | notifyResourceJobs | Não | Não |
> | Fornecedores | Não | Não |
> | recursosGrupos | Sim | Não |
> | assinaturas | Sim | Não |
> | inquilinos | Não | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicações | Sim | Sim |
> | recursos saasrecursos | Não | Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | recolhas de emprego | Sim | Sim |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Não | Não |
> | searchServices | Sim | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | adaptaçõesNetworkHardenings | Não | Não |
> | advancedThreatProtectionSettings | Não | Não |
> | alerts | Não | Não |
> | conexões permitidas | Não | Não |
> | aplicaçõesWhitelistings | Não | Não |
> | avaliaçãoMetas | Não | Não |
> | avaliações | Não | Não |
> | autoDismissAlertsRules | Não | Não |
> | automações | Sim | Sim |
> | Definições de AutoProvisionamento | Não | Não |
> | Conformidades | Não | Não |
> | dataCollectionAgents | Não | Não |
> | dispositivoGrupos de Segurança | Não | Não |
> | discoveredSecuritySolutions | Não | Não |
> | externalSecuritySolutions | Não | Não |
> | Políticas de Proteção de Informação | Não | Não |
> | iotSecuritySolutions | Sim | Sim |
> | iotSecuritySolutions / analyticsModels | Não | Não |
> | iotSecuritySolutions / analyticsModels / agregadAlerts | Não | Não |
> | iotSecuritySolutions / analyticsModels / recomendações agregadas | Não | Não |
> | jitNetworkAccessPolicies | Não | Não |
> | redeData | Não | Não |
> | políticas | Não | Não |
> | preços | Não | Não |
> | regulamentareComplianceStandards | Não | Não |
> | regulamentareComplianceStandards / regulatoryComplianceControls | Não | Não |
> | regulamentareComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Não | Não |
> | securityContacts | Não | Não |
> | securitySolutions | Não | Não |
> | securitySolutionsReferenceData | Não | Não |
> | segurançaEstatutos | Não | Não |
> | segurançaStatusesResumos | Não | Não |
> | servidorVulnerabilidadeAvalias | Não | Não |
> | settings | Não | Não |
> | subAvaliações | Não | Não |
> | tarefas | Não | Não |
> | topologies | Não | Não |
> | workspaceSettings | Não | Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | diagnósticoDefinições | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | agregações | Não | Não |
> | alertaRegras | Não | Não |
> | modelos de regras de alerta | Não | Não |
> | marcadores | Não | Não |
> | casos | Não | Não |
> | dataCotes | Não | Não |
> | dataConnectorsCheckRequisitos | Não | Não |
> | entidades | Não | Não |
> | entidadeQueries | Não | Não |
> | Incidentes | Não | Não |
> | officeConsents | Não | Não |
> | settings | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | espaços de nome | Sim | Não |
> | espaços de nomes / regras de autorização | Não | Não |
> | espaços de nomes / desastres recoveryconfigs | Não | Não |
> | espaços de nome / filtros de eventgrid | Não | Não |
> | espaços de nome / conjuntos de regras de rede | Não | Não |
> | espaços de nome / filas | Não | Não |
> | espaços de nome / filas / regras de autorização | Não | Não |
> | espaços de nome / tópicos | Não | Não |
> | espaços de nome / tópicos / regras de autorização | Não | Não |
> | espaços de nome / tópicos / subscrições | Não | Não |
> | espaços de nome / tópicos / subscrições / regras | Não | Não |
> | premiumMessagingRegions | Não | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicações | Sim | Sim |
> | aglomerados | Sim | Sim |
> | clusters / aplicações | Não | Não |
> | grupos de contentores | Sim | Sim |
> | conjuntos de grupos de contentores | Sim | Sim |
> | aglomerados de bordas | Sim | Sim |
> | aglomerados de bordas / aplicações | Não | Não |
> | clusters geridos | Sim | Sim |
> | clusters geridos / nós | Não | Não |
> | redes | Sim | Sim |
> | lojas secretas | Sim | Sim |
> | lojas secretas / certificados | Não | Não |
> | lojas secretas / segredos | Não | Não |
> | volumes | Sim | Sim |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicações | Sim | Sim |
> | grupos de contentores | Sim | Sim |
> | gateways | Sim | Sim |
> | redes | Sim | Sim |
> | segredos | Sim | Sim |
> | volumes | Sim | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Não | Não |
> | fornecedorRegistrations / recursosTypeRegistrations | Não | Não |
> | lançamentos | Sim | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | SignalR | Sim | Sim |
> | SignalR / eventoSFiltros GridFilters | Não | Não |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Sim | Sim |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | híbridoUseBenefits | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicaçõesDefinições | Sim | Sim |
> | aplicações | Sim | Sim |
> | jitRequests | Sim | Sim |

## <a name="microsoftspoolservice"></a>Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | assinaturas registadas | Não | Não |
> | bobinas | Sim | Sim |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | managedInstances | Sim | Sim |
> | managedCasos / bases de dados | Sim (ver [nota abaixo)](#sqlnote) | Sim |
> | managedCasos / bases de dados / backupShortTermRetentionPolicies | Não | Não |
> | managedCasos / bases de dados / schemas / tabelas / colunas / sensibilidadeEtiquetas | Não | Não |
> | managedCasos / bases de dados / vulnerabilidadeAvaliações | Não | Não |
> | managedCasos / bases de dados / vulnerabilidadeAvaliações / regras / linhas de base | Não | Não |
> | managedInstances / encriptaçãoProtector | Não | Não |
> | managedInstances / teclas | Não | Não |
> | managedInstances / restorableDropDatabases / backupShortTermRetentionPolicies | Não | Não |
> | managedCasos / vulnerabilidadeAvaliações | Não | Não |
> | servidores | Sim | Sim |
> | servidores / administradores | Não | Não |
> | servidores / comunicaçõesLinks | Não | Não |
> | servidores / bases de dados | Sim (ver [nota abaixo)](#sqlnote) | Sim |
> | servidores / encriptaçãoProtector | Não | Não |
> | servidores / firewallRules | Não | Não |
> | servidores / teclas | Não | Não |
> | servidores / RestorableDropDatabases | Não | Não |
> | servidores / objetivos de serviço | Não | Não |
> | servidores / tdeCertificados | Não | Não |
> | clusters virtuais | Não | Não |

<a id="sqlnote" />

> [!NOTE]
> A base de dados Master não suporta etiquetas, mas outras bases de dados, incluindo bases de dados do Armazém de Dados Azure SQL, etiquetas de suporte. As bases de dados do Armazém de Dados Azure SQL devem estar em estado Ativo (não pausado).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Sim | Sim |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Não | Não |
> | Máquinas SqlVirtual | Sim | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Sim | Sim |
> | armazenamentoContas / blobServices | Não | Não |
> | armazenamentoContas / ficheirosServiços | Não | Não |
> | armazenamentoContas / filaServiços | Não | Não |
> | armazenamentoContas / serviços | Não | Não |
> | armazenamentoContas / serviços / definições métricas | Não | Não |
> | armazenamentoContas / tabelaServiços | Não | Não |
> | utilizações | Não | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | caches | Sim | Sim |
> | caches / armazenamentoAlvos | Não | Não |
> | utilizaçãoModelos | Não | Não |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | grupos de replicação | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | storageSyncServices / RegisteredServers | Não | Não |
> | armazenamentoSyncServices / syncGroups | Não | Não |
> | armazenamentoSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | armazenamentoSyncServices / syncGroups / serverEndpoints | Não | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | storageSyncServices / RegisteredServers | Não | Não |
> | armazenamentoSyncServices / syncGroups | Não | Não |
> | armazenamentoSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | armazenamentoSyncServices / syncGroups / serverEndpoints | Não | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | storageSyncServices / RegisteredServers | Não | Não |
> | armazenamentoSyncServices / syncGroups | Não | Não |
> | armazenamentoSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | armazenamentoSyncServices / syncGroups / serverEndpoints | Não | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | gestores | Sim | Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Sim (ver nota abaixo) | Sim |

> [!NOTE]
> Não se pode adicionar uma etiqueta quando os trabalhos de streaming estão a funcionar. Pare o recurso para adicionar uma etiqueta.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Cancelar | Não | Não |
> | CreateSubscription | Não | Não |
> | permitir | Não | Não |
> | renome | Não | Não |
> | Definições de Assinaturas | Não | Não |
> | SubscriptionOperations | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | ambientes | Sim | Não |
> | ambientes / accessPolicies | Não | Não |
> | ambientes / fontes de eventos | Sim | Não |
> | ambientes / referênciaDataSets | Sim | Não |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | dedicadoSCloudNodes | Sim | Sim |
> | dedicadoSServiços Cloud | Sim | Sim |
> | máquinas virtuais | Sim | Sim |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | dispositivos | Sim | Sim |
> | fornecedores | Não | Não |
> | fornecedores / skus | Não | Não |
> | fornecedores / vnfs | Não | Não |
> | vnfs | Sim | Sim |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Não | Não |
> | apiGeGeAccounts / apiAcls | Não | Não |
> | apiGeGeAccounts / apis | Não | Não |
> | apiGeGeAccounts / apis / apiAcls | Não | Não |
> | apiGeGeContas / apis / conexõesAcls | Não | Não |
> | apiGeGeContas / apis / conexões | Não | Não |
> | apiGeGeContas / apis / conexões / conexõesAcls | Não | Não |
> | apiGegeContas / apis / definições localizadas | Não | Não |
> | apiGeGeContas / conexõesAcls | Não | Não |
> | apiGeGeContas / conexões | Não | Não |
> | contadores de faturação | Não | Não |
> | certificados | Sim | Sim |
> | connectionGateways | Sim | Sim |
> | conexões | Sim | Sim |
> | customApis | Sim | Sim |
> | deletedSites | Não | Não |
> | hospedagemAmbientes | Sim | Sim |
> | hospedagemAmbientes / eventoSGridFilters | Não | Não |
> | hospedagemAmbientes / multiRolePools | Não | Não |
> | hospedagemAmbientes / trabalhadorEsPools | Não | Não |
> | publishingUsers | Não | Não |
> | recomendações | Não | Não |
> | resourceHealthMetadata | Não | Não |
> | tempos de execução | Não | Não |
> | serverFarms | Sim | Sim |
> | servidorFarms / eventoSGridFilters | Não | Não |
> | sites | Sim | Sim |
> | sites / config  | Não | Não |
> | sites / eventoSGridFilters | Não | Não |
> | sites / hostNameBindings | Não | Não |
> | sites / redeConfig | Não | Não |
> | sites / premieraddons | Sim | Sim |
> | sites / slots | Sim | Sim |
> | sites / slots / eventoSGridFilters | Não | Não |
> | sites / slots / hostNameBindings | Não | Não |
> | sites / slots / redeConfig | Não | Não |
> | sourceControls | Não | Não |
> | sites estáticas | Sim | Sim |
> | validar | Não | Não |
> | verifyHostingEnvironmentVnet | Não | Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | diagnósticoDefinições | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Serviços de Dispositivos | Sim | Sim |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Etiquetas de suporte | Tag no relatório de custos |
> | ------------- | ----------- | ----------- |
> | componentes | Não | Não |
> | componentesSSumário | Não | Não |
> | monitorInstances | Não | Não |
> | monitorInstancesSummary | Não | Não |
> | monitores | Não | Não |
> | notificationSettings | Não | Não |

## <a name="next-steps"></a>Passos seguintes

Para aprender a aplicar tags aos recursos, consulte [Use tags para organizar os seus recursos Azure.](tag-resources.md)
