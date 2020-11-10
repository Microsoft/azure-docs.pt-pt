---
title: Suporte de etiquetas para recursos
description: Mostra quais os tipos de recursos Azure que suportam tags. Fornece detalhes para todos os serviços da Azure.
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: fb81ed303cf27add3a033021f017c03f4b9e9750
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427773"
---
# <a name="tag-support-for-azure-resources"></a>Suporte de etiqueta para recursos Azure
Este artigo descreve se um tipo de recurso suporta [tags](tag-resources.md). As **etiquetas de suporte** da coluna rotuladas indicam se o tipo de recurso tem uma propriedade para a etiqueta. A coluna rotulada **Tag no relatório de custos** indica se esse tipo de recurso passa a etiqueta para o relatório de custos. Pode ver os custos por etiquetas na análise de custos de Gestão de [Custos](../../cost-management-billing/costs/group-filter.md) e na faturação da [Azure e dados de utilização diária.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)

Para obter os mesmos dados que um ficheiro de valores separados por vírgula, faça o download [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Salte para um espaço de nome de fornecedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DbforPostgresql](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Geómica](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
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
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
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
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatopenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Singularity](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
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
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Serviços de Domínio | Yes | Yes |
> | Serviços de Domínio / oucontainer | No | No |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | apoiarProviders | No | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | No | No |
> | adds serviços | No | No |
> | agentes | No | No |
> | anónimos | No | No |
> | configuração | No | No |
> | registos | No | No |
> | relatórios | No | No |
> | serviços de saúde | No | No |
> | services | No | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | advisorScore | No | No |
> | configurações | No | No |
> | gerarRecommendações | No | No |
> | do IdP | No | No |
> | recomendações | No | No |
> | supressões | No | No |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | farmBeats | Yes | Yes |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | actionRules | Yes | Yes |
> | alerts | No | No |
> | alertasList | No | No |
> | alertasMetaData | No | No |
> | alertasSummary | No | No |
> | alertaSummaryList | No | No |
> | smartDetectorAlertRules | Yes | Yes |
> | smartGroups | No | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | servidores | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | reportFeedback | No | No |
> | serviço | Yes | Yes |
> | validar Nome de Serviço | No | No |

> [!NOTE]
> A Azure API Management só suporta a criação de um máximo de 15 pares de nome/valor para cada serviço.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | configuraçõesStores | Yes | No |
> | configuraçõesStores / eventGridFilters | No | No |
> | configuraçõesStores / keyValues | No | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Spring | Yes | Yes |
> | Primavera / aplicativos | No | No |
> | Primavera / apps / implementações | No | No |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Yes | Yes |
> | predefinidosProviders | No | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | acessoReviewScheduleDefinitions | No | No |
> | acessoReviewScheduleSettings | No | No |
> | clássicos Administradores | No | No |
> | dataAliases | No | No |
> | negar Assignments | No | No |
> | elevateAccess | No | No |
> | findOrphanRoleAssignments | No | No |
> | fechaduras | No | No |
> | permissões | No | No |
> | políticasAssinsagens | No | No |
> | políticasDefinições | No | No |
> | políticasExemptions | No | No |
> | políticasSetDefinitions | No | No |
> | privateLinkAssociations | No | No |
> | fornecedorOperações | No | No |
> | recursosManagementPrivateLinks | Yes | Yes |
> | papéAs de assinaturas | No | No |
> | papelAssignmentsUsageMetrics | No | No |
> | funçõesDefinitions | No | No |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | configuraçãoProfileAssignments | No | No |
> | configuraçãoProfilePreferences | Yes | Yes |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | automação Contas | Yes | Yes |
> | automação Contas/ configurações | Yes | Yes |
> | automação Contas / empregos | No | No |
> | automaçãoAcontas / privateEndpointConnectionProxies | No | No |
> | automaçãoAs contas / privateEndpointConnections | No | No |
> | automação Contas / privateLinkResources | No | No |
> | automação Contas / runbooks | Yes | Yes |
> | automaçãoCotas / softwareUpdateConfigurations | No | No |
> | automaçãoAcontas / webhooks | No | No |

> [!NOTE]
> A Azure Automation só suporta a criação de um máximo de 15 pares de nome/valor para cada recurso Demôm automação.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | privateClouds | Yes | Yes |
> | privateClouds / addons | No | No |
> | privateClouds / autorizações | No | No |
> | privateClouds / clusters | No | No |
> | privateClouds / globalReachConnections | No | No |
> | privateClouds / hcxEnterpriseSites | No | No |
> | privateClouds / workloadNetworks | No | No |
> | privateClouds / workloadNetworks / dhcpConfigurations | No | No |
> | privateClouds / workloadNetworks / gateways | No | No |
> | privateClouds / workloadNetworks / portMirroringProfiles | No | No |
> | privateClouds / workloadNetworks / segmentos | No | No |
> | privateClouds / workloadNetworks / virtualMachines | No | No |
> | privateClouds / workloadNetworks / vmGroups | No | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | ambientes | No | No |
> | ambientes / contas | No | No |
> | ambientes / contas / espaços de nome | No | No |
> | ambientes / contas / espaços de nome / configurações | No | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | b2cDirectes | Yes | No |
> | b2ctenants | No | No |
> | guestUsages | Yes | Yes |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | dataControllers | Yes | Yes |
> | pós-gresInstances | Yes | Yes |
> | sqlManagedInstances | Yes | Yes |
> | sqlServerInstances | Yes | Yes |
> | sqlServerRegistrations | Yes | Yes |
> | sqlServerRegistrations / SqlServers | No | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | No | No |
> | edgeSubscriptions | Yes | Yes |
> | linkedSubscriptions | Yes | Yes |
> | registos | Yes | Yes |
> | registos / clienteSubscrições | No | No |
> | registos / produtos | No | No |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Yes | Yes |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | Yes | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | lotAcons | Yes | Yes |
> | lotacos /certificados | No | No |
> | lotacos / piscinas | No | No |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | faturaçãoAconse de contas | No | No |
> | faturaçãoAconssolhos/acordos | No | No |
> | faturaçãoAs contas / faturaçõesPermissions | No | No |
> | faturaçãoAcounts / billingProfiles | No | No |
> | faturação Encomendas / faturaçãoProfiles / faturaçãoPermissions | No | No |
> | faturação Encomendas / faturaçãoProfiles / billingRoleAssignments | No | No |
> | billingAcounts / faturaçãoProfiles / billingRoleDefinitions | No | No |
> | faturação Encomendas / faturaçãoProfiles / faturaçãoSubscrições | No | No |
> | billingAcounts / faturaçãoProfiles / createBillingRoleAssignment | No | No |
> | faturaçãoAstros /faturaçãoProfiles / clientes | No | No |
> | faturaçãoAstros /faturaçãoProfiles / instruções | No | No |
> | faturação Encomendas / faturas | No | No |
> | faturação Encomendas / faturas / faturas / folha de preços | No | No |
> | faturação Encomendas / faturas / faturas / transações | No | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções | No | No |
> | faturaçãoAstros /faturaçãoProfiles / faturaSecções / faturasPermissions | No | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / billingRoleAssignments | No | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / billingRoleDefinitions | No | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / faturaSubscrições | No | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / createBillingRoleAssignment | No | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / iniciarTransfer | No | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / produtos | No | No |
> | faturação Encomendas / faturaçãoProfiles / faturaSecções / produtos / transferência | No | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / produtos / updateAutoRenew | No | No |
> | faturação Encomendas / faturaçãoProfiles / faturaSecções / transações | No | No |
> | faturação Encomendas / faturaçãoProfiles / faturaSecções / transferências | No | No |
> | faturação Encomendas / BillingProfiles / patchOperações | No | No |
> | faturação Encomendas / faturaçãoProfiles / pagamentosMes | No | No |
> | faturaçãoAcounts / faturaçãoProfiles / políticas | No | No |
> | faturaçãoAs contas / faturaçãoProfiles / folha de preços | No | No |
> | faturação Encomendas / faturaçãoProfiles / pricesheetDownloadOperations | No | No |
> | faturaçãoAstros /faturaçãoProfiles / produtos | No | No |
> | faturaçãoAstros /faturaçãoProfiles / reservas | No | No |
> | faturação Encomendas / faturaçãoProfiles / transações | No | No |
> | faturaçãoAcounts / faturaçãoProfiles / validarDetachPaymentMethodEligibility | No | No |
> | billingAcounts / billingRoleAssignments | No | No |
> | billingAccounts / billingRoleDefinitions | No | No |
> | faturaçãoAcounts / faturaçãoSubscrições | No | No |
> | faturaçãoAs contas / faturas/ faturas | No | No |
> | billingAcounts / createBillingRoleAssignment | No | No |
> | billingAcounts / criarInvoiceSectionsOperations | No | No |
> | faturaçãoAsconse de clientes | No | No |
> | faturaçãoAs contas / clientes / faturaçõesPermissions | No | No |
> | faturaçãoAs contas / clientes / faturaçãoSubscrições | No | No |
> | faturaçãoAconssocamos / clientes / iniciarTransfer | No | No |
> | faturaçãoAconse de clientes / políticas | No | No |
> | faturaçãoAconse de clientes / produtos | No | No |
> | faturaçãoAs contas / clientes / transações | No | No |
> | faturaçãoAs contas / clientes / transferes | No | No |
> | faturaçãoAconsse de departamentos | No | No |
> | billingAcounts / departamentos / faturaçõesPermissions | No | No |
> | billingAcounts / departamentos / billingRoleAssignments | No | No |
> | billingAcounts / departamentos / billingRoleDefinitions | No | No |
> | faturaçãoAconse de matrículasAconseções | No | No |
> | faturaçãoAs contas / matrículasContas / faturações | No | No |
> | faturaçãoAcounts / matrículasContas / billingRoleAssignments | No | No |
> | billingAcounts / matrículasContas / billingRoleDefinitions | No | No |
> | faturação | No | No |
> | faturas / faturas / transações | No | No |
> | faturaçãoS / faturaSecções | No | No |
> | faturaçãoS / faturaSecções / faturaÇãoSubscriçãoMoveOperações | No | No |
> | faturaçãoS / faturaSecções / faturaSubscrições | No | No |
> | faturaçãoS / faturaSecções / faturaSubscrições / transferência | No | No |
> | faturaçãoS / faturaSecções / elevação | No | No |
> | faturaÇãoS / faturaSecções / iniciarTransfer | No | No |
> | faturações / faturaSecções / patchOperações | No | No |
> | faturações / faturaSecções / produtoMoveOperações | No | No |
> | faturaÇãoS / faturaSecções / produtos | No | No |
> | faturaÇões / faturaSecções / produtos / transferência | No | No |
> | faturaÇões / faturaSecções / produtos / updateAutoRenew | No | No |
> | faturaçãoS / faturaSecções / transações | No | No |
> | faturaÇões / faturaSecções / transferências | No | No |
> | faturaçãoAs contas / linhaOfCredit | No | No |
> | faturaçãoOperações / PatchOperações | No | No |
> | faturação Contas / pagamentosMes | No | No |
> | faturaçãoAconsecos / produtos | No | No |
> | faturaçãoAsconse de reservas | No | No |
> | faturaçãoAs/transações | No | No |
> | faturaçãoPeriods | No | No |
> | faturaçãoPermissions | No | No |
> | faturaçãoProperty | No | No |
> | billingRoleAssignments | No | No |
> | billingRoleDefinitions | No | No |
> | createBillingRoleAssignment | No | No |
> | departamentos | No | No |
> | matrículasCotas | No | No |
> | faturas | No | No |
> | transferências | No | No |
> | transferências / aceitarTransfer | No | No |
> | transferências / declínioTransfer | No | No |
> | transferências / operaçãoStatus | No | No |
> | transferências / validarTransfer | No | No |
> | validarAddress | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | mapApis | Yes | Yes |
> | actualizaçãoCommunicationPreferência | No | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Yes | Yes |
> | cordaMembers | Yes | Yes |
> | observadores | Yes | Yes |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | TokenServices | Yes | Yes |
> | TokenServices / BlockchainNetworks | No | No |
> | TokenServices / Grupos | No | No |
> | TokenServices / Grupos / Contas | No | No |
> | TokenServices / TokenTemplates | No | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | blueprintAsignments | No | No |
> | blueprintAssignments / atribuiçãoOperações | No | No |
> | blueprintAssignments /operações | No | No |
> | plantas | No | No |
> | plantas / artefactos | No | No |
> | plantas / versões | No | No |
> | plantas / versões / artefactos | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | botServs | Yes | Yes |
> | botServices / canais | No | No |
> | botServices / conexões | No | No |
> | linguagens | No | No |
> | modelos | No | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Redis | Yes | Yes |
> | Redis / EventGridFilters | No | No |
> | Redis / privateEndpointConnectionProxies | No | No |
> | Redis / privateEndpointConnectionProxies / validar | No | No |
> | Redis / privateEndpointConnections | No | No |
> | Redis / privateLinkResources | No | No |
> | redisEnterprise | Yes | Yes |
> | RedisEnterprise / privateEndpointConnectionProxies | No | No |
> | RedisEnterprise / privateEndpointConnectionProxies / validar | No | No |
> | RedisEnterprise / privateEndpointConnections | No | No |
> | RedisEnterprise / privateLinkResources | No | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicaçõesReservações | No | No |
> | autoQuotaIncrease | No | No |
> | calcularExchange | No | No |
> | calcularPrimá-lo | No | No |
> | calcular Prémio Depurchase | No | No |
> | catálogos | No | No |
> | comerciaisReservationOrders | No | No |
> | troca | No | No |
> | própriasreservações | No | No |
> | placePurchaseOrder | No | No |
> | pedidos de reservas | No | No |
> | pedidos de reservas / calcularRefund | No | No |
> | reservasOrdeiros / fusão | No | No |
> | reservasOrdeiros / Reservas | No | No |
> | reservasOrdeiros / reservas / revisões | No | No |
> | pedidos de reservas / devolução | No | No |
> | pedidos de reservas / divisão | No | No |
> | pedidos de reservas / swap | No | No |
> | reservas | No | No |
> | recursosProviders | No | No |
> | resources | No | No |
> | validar ReservationOrder | No | No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No | No |
> | CdnWebApplicationFirewallPolicies | Yes | Yes |
> | edgenodes | No | No |
> | perfis | Yes | Yes |
> | perfis / pontos finais | Yes | Yes |
> | perfis / pontos finais / personaldomains | No | No |
> | perfis / pontos finais / grupos de origem | No | No |
> | perfis / pontos finais / origens | No | No |
> | validarProbe | No | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | certificadoRes | Yes | Yes |
> | certificadoRes/certificados | No | No |
> | validar Informação DeRegistrationRegistrationCertificate | No | No |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | perfil | No | No |
> | recursosChanges | No | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | capacidades | No | No |
> | domínioNames | No | No |
> | domínioS de identidades / capacidades | No | No |
> | domainNames / InternaLoadBalancers | No | No |
> | domínioNamis / serviçoScertificados | No | No |
> | domínioNames / slots | No | No |
> | domínioNames / slots / funções | No | No |
> | domínioNames / slots / funções / métricasDefinitions | No | No |
> | domínioNames / slots / funções / métricas | No | No |
> | moveSubscriptionResources | No | No |
> | operatingSystemFamilies | No | No |
> | sistemas operativos | No | No |
> | quotas | No | No |
> | recursosTipos | No | No |
> | validarSubscriptionMoveAvailability | No | No |
> | virtualMachines | No | No |
> | virtualMachines / diagnósticoSettings | No | No |
> | virtualMachines / métricasDefinitions | No | No |
> | virtualMachines / métricas | No | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | No | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | capacidades | No | No |
> | expressRouteCrossConnections | No | No |
> | expressRouteCrossConnections / peerings | No | No |
> | gatewaySupportouDevices | No | No |
> | networkSecurityGroups | No | No |
> | quotas | No | No |
> | reservedIps | No | No |
> | virtualNetworks | No | No |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | No | No |
> | virtualNetworks / virtualNetworkPeerings | No | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | capacidades | No | No |
> | discos | No | No |
> | imagens | No | No |
> | osImages | No | No |
> | osPlatformImages | No | No |
> | publicImages | No | No |
> | quotas | No | No |
> | armazenamento Contas | No | No |
> | armazenamentoCotas / blobServices | No | No |
> | armazenamentoSCotas / serviços de ficheiros | No | No |
> | armazenamentoCotas / métricasDefinitions | No | No |
> | armazenamentoCotas /métricas | No | No |
> | armazenamentoSCotas / filas Serviços | No | No |
> | armazenamentoCotas /serviços | No | No |
> | armazenamentoS / serviços / diagnósticoSettings | No | No |
> | armazenamentoCotas / serviços / métricasDefinitions | No | No |
> | armazenamentoCotas / serviços / métricas | No | No |
> | armazenamentoCotas / tableServices | No | No |
> | armazenamentoCotas / vmImages | No | No |
> | vmImages | No | No |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | planos | Yes | No |
> | registoS de subscrições | No | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | contas / privateEndpointConnectionProxies | No | No |
> | contas / privateEndpointConnections | No | No |
> | contas / privateLinkResources | No | No |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | RateCard | No | No |
> | UsageAggregates | No | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | disponibilidadeSets | Yes | Yes |
> | cloudServs | Yes | Yes |
> | cloudServices / networkInterfaces | No | No |
> | cloudServices / publicIPAddresses | No | No |
> | cloudServs / roleInstances | No | No |
> | cloudServices / roleInstances / networkInterfaces | No | No |
> | cloudServs / funções | No | No |
> | diskAccesses | Yes | Yes |
> | DiskEncryptionSets | Yes | Yes |
> | discos | Yes | Yes |
> | galerias | Yes | Yes |
> | galerias / aplicações | No | No |
> | galerias / aplicações / versões | No | No |
> | galerias / imagens | No | No |
> | galerias / imagens / versões | No | No |
> | grupos de anfitriões | Yes | Yes |
> | hostGroups / anfitriões | Yes | Yes |
> | imagens | Yes | Yes |
> | grupos de posições de proximidade | Yes | Yes |
> | restaurarPointCollections | Yes | Yes |
> | restaurarPointCollections / restaurarPonts | No | No |
> | sharedVMExtensions | Yes | Yes |
> | sharedVMExtensions / versões | No | No |
> | sharedVMImages | Yes | Yes |
> | sharedVMImages / versões | No | No |
> | instantâneos | Yes | Yes |
> | sshPublicKeys | Yes | Yes |
> | virtualMachines | Yes | Yes |
> | virtualMachines / extensões | Yes | Yes |
> | virtualMachines / métricasDefinitions | No | No |
> | virtualMachines / runCommands | Yes | Yes |
> | virtualMachineScaleSets | Yes | Yes |
> | virtualMachineScaleSets / extensões | No | No |
> | virtualMachineScaleSets / networkInterfaces | No | No |
> | virtualMachineScaleSets / publicIPAddresses | Yes | No |
> | virtualMachineScaleSets / virtualMachines | No | No |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | No | No |

> [!NOTE]
> Não é possível adicionar uma etiqueta a uma máquina virtual que foi marcada como generalizada. Marca uma máquina virtual generalizada com [Set-AzVm -Generalização](/powershell/module/Az.Compute/Set-AzVM) ou [az vm generalizando](/cli/azure/vm#az-vm-generalize).

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | CacheNodes | Yes | Yes |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | AgregadoCost | No | No |
> | Saldos | No | No |
> | Orçamentos | No | No |
> | Cobranças | No | No |
> | CostTags | No | No |
> | créditos | No | No |
> | eventos | No | No |
> | Previsões | No | No |
> | lotes | No | No |
> | Mercados | No | No |
> | Folhas de preços | No | No |
> | produtos | No | No |
> | ReservasDetails | No | No |
> | ReservasRecommendationDetails | No | No |
> | Recommendações de Reservas | No | No |
> | ReservasSumários | No | No |
> | Transferências de Reservas | No | Não |
> | Etiquetas | Não | No |
> | inquilinos | No | No |
> | Termos | No | No |
> | UsageDetails | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | grupos de contentores | Yes | Yes |
> | serviceAssociationLinks | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | registos | Yes | Yes |
> | registos / agentesPools | Yes | Yes |
> | registos / construções | No | No |
> | registos / construções / cancelar | No | No |
> | registos / construções / getLogLink | No | No |
> | registos / buildTasks | Yes | Yes |
> | registos / buildTasks / passos | No | No |
> | registos / eventGridFilters | No | No |
> | registos / exportaçõesPipelines | No | No |
> | registos / gerarCredentis | No | No |
> | registos / getBuildSourceUploadUrl | No | No |
> | registos / GetCredentials | No | No |
> | registos / importImage | No | No |
> | registos / importapipelines | No | No |
> | registos / pipelineRuns | No | No |
> | registos / privateEndpointConnectionProxies | No | No |
> | registos / privateEndpointConnectionProxies / validar | No | No |
> | registos / privateEndpointConnections | No | No |
> | registos / privateLinkResources | No | No |
> | registos / filasBuild | No | No |
> | registos / regeneraçãoCredential | No | No |
> | registos / regeneraçãoCredentais | No | No |
> | registos / replicações | Yes | Yes |
> | registos / corre | No | No |
> | registos / corre / cancela | No | No |
> | registos / horáriosRun | No | No |
> | registos / scopeMaps | No | No |
> | registos / taskRuns | No | No |
> | registos / tarefas | Yes | Yes |
> | registos / fichas | No | No |
> | registos / actualizaçãoPolícias | No | No |
> | registos / webhooks | Yes | Yes |
> | registros / webhooks / getCallbackConfig | No | No |
> | registos / webhooks / ping | No | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | serviços de contentores | Yes | Yes |
> | geridoClusters | Yes | Yes |
> | openShiftManagedClusters | Yes | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Alertas | No | No |
> | Faturação | No | No |
> | Orçamentos | No | No |
> | CloudConnectors | No | No |
> | Conectores | Yes | Yes |
> | costAllocationRules | No | No |
> | Departamentos | No | No |
> | Dimensões | No | No |
> | MatrículasAconselhos | No | No |
> | Exportações | No | No |
> | Contas externas deBilling | No | No |
> | ExternalBillingAccounts / Alertas | No | No |
> | ExternalBillingAccounts / Dimensões | No | No |
> | Contas externas deBilling / Previsão | No | No |
> | ExternalBillingAccounts / Consulta | No | No |
> | Subscrições externas | No | No |
> | Subscrições externas / Alertas | No | No |
> | ExternalSubscriptions / Dimensões | No | No |
> | ExternalSubscriptions / Previsão | No | No |
> | Subscrições externas / Consulta | No | No |
> | Previsão | No | No |
> | Informações | No | No |
> | Consulta | No | No |
> | registar | No | No |
> | Configs relatório | No | No |
> | Relatórios | No | No |
> | Definições | No | No |
> | showbackRules | No | No |
> | Vistas | No | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | pedidos | No | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | associações | No | No |
> | recursosProviders | Yes | Yes |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | casos | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | empregos | Yes | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Yes | Yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | áreas de trabalho | Yes | Yes |
> | espaços de trabalho / dbWorkspaces | No | No |
> | espaços de trabalho / virtualNetworkPeerings | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | catálogos | Yes | Yes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | dataFactories | Yes | Yes |
> | dataFactories / diagnosticSettings | No | No |
> | dataFactories / métricasDefinitions | No | No |
> | dataFactorySchema | No | No |
> | fábricas | Yes | Yes |
> | fábricas / integraçãoRuntimes | No | No |

> [!NOTE]
> Se tiver tempos de integração Azure-SSIS na sua fábrica de dados, o seu custo de funcionamento será marcado com etiquetas de fábrica de dados. Os tempos de execução da integração Azure-SSIS devem ser interrompidos e reiniciados para que novas etiquetas de fábrica de dados sejam aplicadas ao seu custo de funcionamento.

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | contas / dadosLakeStoreAccounts | No | No |
> | contas / armazenamentoCotações | No | No |
> | contas / armazenamentoCotas / contentores | No | No |
> | contas / transferAnlyticsUnits | No | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | contas / eventGridFilters | No | No |
> | contas / firewallRules | No | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | services | No | No |
> | serviços / projetos | No | No |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Yes | Yes |
> | Fontes de recursos | Yes | Yes |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | contas / ações | No | No |
> | contas / ações / conjuntos de dados | No | No |
> | contas / ações / convites | No | No |
> | contas / ações / subscrições de partilha de fornecedores | No | No |
> | contas / ações / sincronizaçõesS | No | No |
> | contas / subscrições de ações | No | No |
> | contas / subscrições de ações / consumerSourceDataSets | No | No |
> | contas / subscrições de ações / datasetmappings | No | No |
> | contas / subscrições de ações / gatilhos | No | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | servidores | Yes | Yes |
> | servidores / assessores | No | No |
> | servidores / chaves | No | No |
> | servidores / privadosEndpointConnectionProxies | No | No |
> | servidores / privateEndpointConnections | No | No |
> | servidores / privateLinkResources | No | No |
> | servidores / consultasTextos | No | No |
> | servidores / recuperáveisServers | No | No |
> | servidores / iniciar | No | No |
> | servidores / parar | No | No |
> | servidores / topQueryStatistics | No | No |
> | servidores / virtualNetworkRules | No | No |
> | servidores / esperaStatistics | No | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Yes | Yes |
> | servidores | Yes | Yes |
> | servidores / assessores | No | No |
> | servidores / chaves | No | No |
> | servidores / privadosEndpointConnectionProxies | No | No |
> | servidores / privateEndpointConnections | No | No |
> | servidores / privateLinkResources | No | No |
> | servidores / consultasTextos | No | No |
> | servidores / recuperáveisServers | No | No |
> | servidores / iniciar | No | No |
> | servidores / parar | No | No |
> | servidores / topQueryStatistics | No | No |
> | servidores / upgrade | No | No |
> | servidores / virtualNetworkRules | No | No |
> | servidores / esperaStatistics | No | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DbforPostgresql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Yes | Yes |
> | grupos de servidores | Yes | Yes |
> | servidores | Yes | Yes |
> | servidores / assessores | No | No |
> | servidores / chaves | No | No |
> | servidores / privadosEndpointConnectionProxies | No | No |
> | servidores / privateEndpointConnections | No | No |
> | servidores / privateLinkResources | No | No |
> | servidores / consultasTextos | No | No |
> | servidores / recuperáveisServers | No | No |
> | servidores / topQueryStatistics | No | No |
> | servidores / virtualNetworkRules | No | No |
> | servidores / esperaStatistics | No | No |
> | servidorv2 | Yes | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | artefactosSources | Yes | Yes |
> | rollouts | Yes | Yes |
> | serviceTopologias | Yes | Yes |
> | serviçoTopologias / serviços | Yes | Yes |
> | serviceTopologias / serviços / serviços Unidades | Yes | Yes |
> | passos | Yes | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | grupos de aplicações | Yes | Yes |
> | grupos de aplicações / aplicações | No | No |
> | grupos de aplicações / desktops | No | No |
> | grupos de aplicações / startmenuitems | No | No |
> | hostpools | Yes | Yes |
> | hostpools / msixpackages | No | No |
> | hostpools / sessão | No | No |
> | hostpools / sessionhosts / utilizadores | No | No |
> | hostpools / utilizadores | No | No |
> | áreas de trabalho | Yes | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Elásticos | Yes | Yes |
> | Elásticos / IotHubTenants | Yes | Yes |
> | Elásticos / IotHubTenants / securitySettings | No | No |
> | IotHubs | Yes | Yes |
> | IotHubs / eventGridFilters | No | No |
> | IotHubs / securitySettings | No | No |
> | Serviços de Provisionamento | Yes | Yes |
> | usos | No | No |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | contas / instâncias | Yes | Yes |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | oleodutos | Yes | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | controladores | Yes | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | centros de laboratório | Yes | Yes |
> | laboratórios | Yes | Yes |
> | laboratórios / ambientes | Yes | Yes |
> | laboratórios / serviceRunners | Yes | Yes |
> | laboratórios / virtualMachines | Yes | Yes |
> | horários | Yes | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | Yes | Yes |
> | digitalTwinsInstances / endpoints | No | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | base de dadosSes | No | No |
> | bases de dadosAconses | Yes | Yes |
> | RestauraçãoDatabaseAccounts | No | No |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | domínios | Yes | Yes |
> | domínios / domainOwnershipIdentifiers | No | No |
> | gerarSsoRequest | No | No |
> | topLevelDomains | No | No |
> | validar aDomainRegistrationInformation | No | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | LCSprojects | No | No |
> | LCSprojects / deplosagens de nuvens | No | No |
> | LCSprojects / conectores | No | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | services | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | domínios | Yes | Yes |
> | domínios / tópicos | No | No |
> | eventoSubscrições | No | No |
> | extensãoTopics | No | No |
> | parceiroNamespaces | Yes | Yes |
> | partnerNamespaces / eventChannels | No | No |
> | partnerRegistrations | Yes | Yes |
> | partnerTopics | Yes | Yes |
> | partnerTopics / eventoSubscrições | No | No |
> | systemTopics | Yes | Yes |
> | systemTopics / eventSubscrições | No | No |
> | tópicos | Yes | Yes |
> | tópicotips | No | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Yes | Yes |
> | espaços de nome | Yes | Yes |
> | espaços de nomes / regras de autorização | No | No |
> | espaços de nome / disasterrecoveryconfigs | No | No |
> | espaços de nomes / eventhubs | No | No |
> | espaços de nome / eventhubs / autorizações | No | No |
> | espaços de nomes / eventhubs / grupos de consumidores | No | No |
> | espaços de nome / redes de regras | No | No |
> | espaços de nome / privateEndpointConnections | No | No |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | experimentOs espaços de trabalho | Yes | Yes |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | espaços de nome | Yes | Yes |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | recursosProviders | No | No |
> | funcionalidades | No | No |
> | fornecedores | No | No |
> | subscriçõesFesturaRegistrações | No | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | inscrever | No | No |
> | galleryitems | No | No |
> | generateartifactacssuri | No | No |
> | myareas | No | No |
> | myareas / áreas | No | No |
> | myareas / áreas / áreas | No | No |
> | myareas / áreas / áreas / galleryitems | No | No |
> | myareas / áreas / galleryitems | No | No |
> | myareas / galleryitems | No | No |
> | registar | No | No |
> | resources | No | No |
> | retrieveresourcesbyid | No | No |

## <a name="microsoftgenomics"></a>Microsoft.Geómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Yes | Yes |
> | autoManagedVmConfigurationProfiles | Yes | Yes |
> | configuraçãoProfileAssignments | No | No |
> | guestConfigurationAssignments | No | No |
> | software | No | No |
> | softwareUpdateProfile | No | No |
> | softwareUpdates | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Yes | Yes |
> | sapMonitores | Yes | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | DEDICADOHSMs | Yes | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Yes | Yes |
> | clusters / aplicações | No | No |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | services | Yes | Yes |
> | serviços / iomtconnectors | No | No |
> | serviços / iomtconnectors / conexões | No | No |
> | serviços / iomtconnectors / mapeamentos | No | No |
> | serviços / privateEndpointConnectionProxies | No | No |
> | serviços / privateEndpointConnections | No | No |
> | serviços / privateLinkResources | No | No |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | máquinas | Yes | Yes |
> | máquinas / avaliaçãoPatches | No | No |
> | máquinas / extensões | Yes | Yes |
> | máquinas / instalaçãoPatches | No | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | dataSmanagers | Yes | Yes |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | dispositivos | Yes | Yes |
> | redeFunções | Yes | Yes |
> | redeFunctionVendors | No | No |
> | registoS de subscrições | No | No |
> | fornecedores | No | No |
> | fornecedores / fornecedoresSkus | No | No |
> | fornecedores / fornecedoresSkus / pré-visualizaçãoS subscrições | No | No |
> | virtualNetworkFunções | Yes | Yes |
> | virtualNetworkFunctionVendors | No | No |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | componentes | Yes | Yes |
> | networkScopes | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | empregos | Yes | Yes |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | grupos de ação | Yes | Yes |
> | atividadeSalerts | Yes | Yes |
> | alertas | Yes | Yes |
> | autoescala | Yes | Yes |
> | componentes | Yes | Yes |
> | componentes / linkedStorageAccounts | No | No |
> | componentes / ProactiveDetectionConfigs | No | No |
> | diagnósticoS | No | No |
> | guestDiagnosticSettings | Yes | Yes |
> | guestDiagnosticSettingsAssociation | Yes | Yes |
> | logprofiles | Yes | Yes |
> | metricAlerts | Yes | Yes |
> | privateLinkScopes | Yes | Yes |
> | privateLinkScopes / privateEndpointConnections | No | No |
> | privateLinkScopes / scopedResources | No | No |
> | pacotes de consultas | Yes | Yes |
> | consultas de consultas | No | No |
> | agendadoQueryRules | Yes | Yes |
> | webtests | Yes | Yes |
> | livros | Yes | Yes |
> | estalagens de livros | Yes | Yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | diagnósticoS | No | No |
> | diagnósticoSettingsCategories | No | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | appTemplates | No | No |
> | IoTApps | Yes | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Graph | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | deletedVaults | No | No |
> | hsmPools | Yes | Yes |
> | managedHSMs | Yes | Yes |
> | cofres | Yes | Yes |
> | cofres / acessosPolícias | No | No |
> | cofres / eventoSGridFiltros | No | No |
> | cofres / chaves | No | No |
> | cofres / chaves / versões | No | No |
> | cofres / segredos | No | No |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | clusters conectados | Yes | Yes |
> | registoS de subscrições | No | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | extensões | No | No |
> | fonteControlConfigurations | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Yes | Yes |
> | clusters / confinações de base de dados | No | No |
> | clusters / bases de dados | No | No |
> | clusters / bases de dados / ligações de dados | No | No |
> | clusters / bases de dados / ligações eventhub | No | No |
> | clusters / bases de dados / principais assinaturas | No | No |
> | clusters / ligações de dados | No | No |
> | aglomerados / principais destacamentos | No | No |
> | clusters / semelhanças partilhadas | No | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | contas de laboratório | Yes | No |
> | utilizadores | No | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | hospedagem Ambientes | Yes | Yes |
> | integraçãoAconselhos | Yes | Yes |
> | integração ServiçoEnvironments | Yes | Yes |
> | integraçãoServiceEnvironments / managedApis | No | No |
> | isoladosEnvironments | Yes | Yes |
> | fluxos de trabalho | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | planos de compromissos | Yes | Yes |
> | webServices | Yes | Yes |
> | Áreas de trabalho | Yes | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | áreas de trabalho | Yes | Yes |
> | espaços de trabalho / lotendpoints | Yes | Yes |
> | espaços de trabalho / lotEndpoints / implementações | Yes | Yes |
> | espaços de trabalho / códigos | No | No |
> | espaços de trabalho / códigos / versões | No | No |
> | espaços de trabalho / computas | No | No |
> | espaços de trabalho / datastores | No | No |
> | espaços de trabalho / eventosGridFiltros | No | No |
> | espaços de trabalho / empregos | No | No |
> | espaços de trabalho / rotulagemJobs | No | No |
> | espaços de trabalho / linkedServices | No | No |
> | espaços de trabalho / modelos | No | No |
> | espaços de trabalho / modelos / versões | No | No |
> | espaços de trabalho / pontos de encontro online | Yes | Yes |
> | espaços de trabalho / onlineEndpoints / implementações | Yes | Yes |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicardados | No | No |
> | configuraçõesAssignments | No | No |
> | configuções de manutenção | Yes | Yes |
> | publicMaintenanceConfigurations | No | No |
> | atualizações | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Identidades | No | No |
> | nomeações para assediadas do utilizador | Yes | Yes |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | managedNetworks | Yes | Yes |
> | managedNetworks / managedNetworkGroups | Yes | Yes |
> | managedNetworks / managedNetworkPeeringPolicies | Yes | Yes |
> | notificação | Yes | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | No | No |
> | registosAssondos | No | No |
> | registoDefinições | No | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | obterEntidades | No | No |
> | grupos de gestão | No | No |
> | managementGroups / configurações | No | No |
> | resources | No | No |
> | startTenantBackfill | No | No |
> | inquilinoBackfillStatus | No | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | contas / eventGridFilters | No | No |
> | contas / privateAtlases | Yes | Yes |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | macc | No | No |
> | ofertas | No | No |
> | oferta deTipos | No | No |
> | oferta deTipos / editores | No | No |
> | oferta DeTipos / editores / ofertas | No | No |
> | oferta DeTipos / editores / ofertas / planos | No | No |
> | oferta DeTipos / editores / ofertas / planos / acordos | No | No |
> | ofertaTipos / editores / ofertas / planos / configs | No | No |
> | ofertaTipos / editoras / ofertas / planos / configs / importImage | No | No |
> | privadas | No | No |
> | privateStoreClient | No | No |
> | lojas privadas | No | No |
> | privateStores / ofertas | No | No |
> | produtos | No | No |
> | editores | No | No |
> | editores / ofertas | No | No |
> | editores / ofertas / alterações | No | No |
> | registar | No | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Yes | Yes |
> | actualizaçãoCommunicationPreferência | No | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | acordos | No | No |
> | tipos de ofertas | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | mediaservices | Yes | Yes |
> | mediaservices / filtros de conta | No | No |
> | mediaserviços / ativos | No | No |
> | mediaservices / ativos / activosFiltros | No | No |
> | mediaservices / conteúdoKeyPolicies | No | No |
> | mediaservices / eventGridFilters | No | No |
> | mediaservices / liveEventOperações | No | No |
> | mediaservices / liveEvents | Yes | Yes |
> | mediaservices / liveEvents / liveOutputs | No | No |
> | mediaservices / liveOutputOperations | No | No |
> | mediaservices / mediaGraphs | No | No |
> | mediaservices / privateEndpointConnectionOperations | No | No |
> | mediaservices / privateEndpointConnectionProxies | No | No |
> | mediaservices / privateEndpointConnections | No | No |
> | mediaservices / streamingEndpointOperations | No | No |
> | mediaservices / streamingEndpoints | Yes | Yes |
> | mediaservices / streamingLocators | No | No |
> | mediaservices / streamingPolicies | No | No |
> | mediaservices / transforma | No | No |
> | mediaservices / transforma / empregos | No | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | appClusters | Yes | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | avaliaçãoProjectos | Yes | Yes |
> | migrarprojects | Yes | Yes |
> | moveCollections | Yes | Yes |
> | projetos | Yes | Yes |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | holográficosBroadcastAccounts | Yes | Yes |
> | objetoUnderstandingAccounts | Yes | Yes |
> | remotorcoscos | Yes | Yes |
> | spatialAnchorsAccounts | Yes | Yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Yes | No |
> | netAppAccounts / accountBackups | No | No |
> | netAppAccounts / capacityPools | Yes | No |
> | netAppAccounts / capacityPools / volumes | Yes | No |
> | netAppAccounts / capacityPools / volumes / snapshots | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Yes | Yes |
> | aplicaçãoGatewayWebApplicationFirewallPolicies | Yes | Yes |
> | applicationSecurityGroups | Yes | Yes |
> | azureFirewallFqdnTags | No | No |
> | azureFirewalls | Yes | No |
> | bastionHosts | Yes | No |
> | bgpServiceCommunidades | No | No |
> | conexões | Yes | Yes |
> | ddosCustomPolicies | Yes | Yes |
> | ddosProtectionPlans | Yes | Yes |
> | dnsOperationStatuses | No | No |
> | dnszones | Yes | Yes |
> | dnszones / A | No | No |
> | dnszones / AAAA | No | No |
> | dnszones / todos os | No | No |
> | dnszones / CAA | No | No |
> | dnszones / CNAME | No | No |
> | dnszones / MX | No | No |
> | dnszones / NS | No | No |
> | dnszones / PTR | No | No |
> | dnszones / recordets | No | No |
> | dnszones / SOA | No | No |
> | dnszones / SRV | No | No |
> | dnszones / TXT | No | No |
> | expressRouteCircuits | Yes | Yes |
> | expressRouteCrossConnections | Yes | Yes |
> | expressRouteGateways | Yes | Yes |
> | expressRoutePorts | Yes | Yes |
> | expressRouteServiceProviders | No | No |
> | firewallPolicies | Yes | Yes |
> | frontdoors | Sim, mas limitado (ver [nota abaixo)](#frontdoor) | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | Sim, mas limitado (ver [nota abaixo)](#frontdoor) | No |
> | frontdoorWebApplicationFirewallPolicies | Sim, mas limitado (ver [nota abaixo)](#frontdoor) | Yes |
> | obter Retecnretecencia | No | No |
> | internaNotificar | No | No |
> | ipGrupos | Yes | Yes |
> | loadBalancers | Yes | Yes |
> | localNetworkGateways | Yes | Yes |
> | natGateways | Yes | Yes |
> | networkIntentPolicies | Yes | Yes |
> | networkInterfaces | Yes | Yes |
> | redesProfiles | Yes | Yes |
> | networkSecurityGroups | Yes | Yes |
> | networkWatchers | Yes | Yes |
> | networkWatchers / connectionMonitors | Yes | No |
> | networkWatchers / flowLogs | No | No |
> | networkWatchers / lentes | Yes | No |
> | networkWatchers / pingMeshes | Yes | No |
> | p2sVpnGateways | Yes | Yes |
> | privateDnsOperationStatuses | No | No |
> | privateDnsZones | Yes | Yes |
> | privateDnsZones / A | No | No |
> | privateDnsZones / AAAA | No | No |
> | privateDnsZones / todos | No | No |
> | privateDnsZones / CNAME | No | No |
> | privateDnsZones / MX | No | No |
> | privateDnsZones / PTR | No | No |
> | privateDnsZones / SOA | No | No |
> | privateDnsZones / SRV | No | No |
> | privateDnsZones / TXT | No | No |
> | privateDnsZones / virtualNetworkLinks | Yes | Yes |
> | privadosEndpoints | Yes | Yes |
> | privateLinkServices | Yes | Yes |
> | publicIPAddresses | Yes | Yes |
> | publicIPPrefixos | Yes | Yes |
> | routeFilters | Yes | Yes |
> | routeTables | Yes | Yes |
> | serviceEndpointPolicies | Yes | Yes |
> | trafficManagerGeographicHierarchies | No | No |
> | trafficmanagerprofiles | Yes | Yes |
> | trafficmanagerprofiles/heatMaps | No | No |
> | trafficManagerUserMetricsKeys | No | No |
> | virtualHubs | Yes | Yes |
> | virtualNetworkGateways | Yes | Yes |
> | virtualNetworks | Yes | Yes |
> | virtualNetworks / sub-redes | No | No |
> | virtualNetworkTaps | Yes | Yes |
> | virtualWans | Yes | No |
> | VPNGateways | Yes | Yes |
> | VPNSites | Yes | Yes |
> | webApplicationFirewallPolicies | Yes | Yes |

<a id="frontdoor"></a>

> [!NOTE]
> Para o Serviço de Porta Frontal Azure, pode aplicar tags ao criar o recurso, mas atualizar ou adicionar tags não é suportado atualmente.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | CadernoProxies | No | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | espaços de nome | Yes | No |
> | espaços de nomes / notificaçõesHubs | Yes | No |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Yes | Yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | HiperVSites | Yes | Yes |
> | ImportaçõesSites | Yes | Yes |
> | MestresSites | Yes | Yes |
> | ServidorEsites | Yes | Yes |
> | VMwareSites | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Yes | Yes |
> | eliminadosEspaços de trabalho | No | No |
> | linkTargets | No | No |
> | storageInsightConfigs | No | No |
> | áreas de trabalho | Yes | Yes |
> | espaços de trabalho / dataExports | No | No |
> | espaços de trabalho / dataSources | No | No |
> | espaços de trabalho / linkedServices | No | No |
> | workspaces / linkedStorageAccounts | No | No |
> | espaços de trabalho / metadados | No | No |
> | espaços de trabalho / consulta | No | No |
> | espaços de trabalho / scopedPrivateLinkProxies | No | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | sociedades de gestão | No | No |
> | configurações de gestão | Yes | Yes |
> | soluções | Yes | Yes |
> | Modos de exibição | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | No | No |
> | peerAsns | No | No |
> | espreiteiros | Yes | Yes |
> | peeringServiceCountries | No | No |
> | peeringServiceProviders | No | No |
> | serviços de peering | Yes | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | atestados | No | No |
> | policyEvents | No | No |
> | policyMetadata | No | No |
> | estados de política | No | No |
> | policyTrackedResources | No | No |
> | remediações | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Consolas do | No | No |
> | dashboards | Yes | Yes |
> | userSettings | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Yes | Yes |
> | inquilinos | Yes | Yes |
> | inquilinos / espaços de trabalho | No | No |
> | workspaceCollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | capacidades | Yes | Yes |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | contas eliminadas | No | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | fornecedorRegistrations | No | No |
> | fornecedorRegistrations /defaultRollouts | No | No |
> | fornecedorRegistrations / recursosTypeRegistrations | No | No |
> | rollouts | Yes | Yes |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Áreas de trabalho | Yes | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | No | No |
> | cofres | Yes | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatopenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Yes | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | espaços de nome | Yes | Yes |
> | espaços de nomes / regras de autorização | No | No |
> | namespaces / ligações híbridas | No | No |
> | espaços de nome / ligações híbridas / regras de autorização | No | No |
> | espaços de nome / privateEndpointConnections | No | No |
> | espaços de nomes / wcfrelays | No | No |
> | espaços de nome / wcfrelays / autorizações | No | No |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | consultas | Yes | Yes |
> | recursosChangeDetails | No | No |
> | recursosChanges | No | No |
> | resources | No | No |
> | recursosHistória | No | No |
> | subscriçõesStatus | No | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | disponibilidade Estatísticas | No | No |
> | criançaSetilabilityStatuses | No | No |
> | criançaresources | No | No |
> | emergentes | No | No |
> | eventos | No | No |
> | impactedResources | No | No |
> | do IdP | No | No |
> | notificações | No | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | calcularTemplateHash | No | No |
> | implementações | Yes | No |
> | implantações / operações | No | No |
> | implementaçõesScripts | Yes | Yes |
> | implementaçõesScripts / registos | No | No |
> | ligações | No | No |
> | notificarResourceJobs | No | No |
> | fornecedores | No | No |
> | grupos de recursos | Yes | No |
> | assinaturas | Yes | No |
> | modeloSpecs | Yes | Yes |
> | modeloSpecs / versões | Yes | Yes |
> | inquilinos | No | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicações | Yes | Yes |
> | saasresources | No | No |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | nuvens | Yes | Yes |
> | VirtualMachines | Yes | Yes |
> | VirtualMachineTemplates | Yes | Yes |
> | VirtualNetworks | Yes | Yes |
> | vmmservers | Yes | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | recursoHealthMetadata | No | No |
> | serviços de pesquisa | Yes | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | No | No |
> | advancedThreatProtectionSettings | No | No |
> | alerts | No | No |
> | alertaSuppressãoRules | No | No |
> | permitidoConnections | No | No |
> | aplicaçãoWhitelistings | No | No |
> | avaliaçãoMetadata | No | No |
> | avaliações | No | No |
> | autoDismissAlertsRules | No | No |
> | automações | Yes | Yes |
> | AutoProvisioningSettings | No | No |
> | Cumprimentos | No | No |
> | conectores | No | No |
> | dataCollectionAgents | No | No |
> | grupos de segurança de dispositivos | No | No |
> | descobertasSecuritySolutions | No | No |
> | sistemas de Segurança externos | No | No |
> | InformaçãoProtetionPolicies | No | No |
> | iotDefenderSettings | No | No |
> | iotSecuritySolutions | Yes | Yes |
> | iotSecuritySolutions / analyticsModels | No | No |
> | iotSecuritySolutions / analyticsModels / agregadosAlerts | No | No |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | No | No |
> | iotSecuritySolutions / iotAlerts | No | No |
> | iotSecuritySolutions / iotAlertTypes | No | No |
> | iotSecuritySolutions / iotRecommendations | No | No |
> | iotSecuritySolutions / iotRecommendationTypes | No | No |
> | iotSensores | No | No |
> | jitNetworkAccessPolicies | No | No |
> | jitPolicies | No | No |
> | políticas | No | No |
> | preços | No | No |
> | regulatórioComplianceStandards | No | No |
> | regulatórioComplianceStandards /regulatórioComplianceControls | No | No |
> | regulatórioComplianceStands / regulatórioComplianceControls / regulatórioComplianceAssess | No | No |
> | secureScoreControlDefinitions | No | No |
> | secureScoreControls | No | No |
> | secureScores | No | No |
> | secureScores / secureScoreControls | No | No |
> | securityContacts | No | No |
> | segurançaSoluções | No | No |
> | securitySolutionsReferenceData | No | No |
> | securityStatuses | No | No |
> | securityStatusesSummaries | No | No |
> | servidorVulnerabilityAssessments | No | No |
> | definições | No | No |
> | sqlVulnerabilityAssesments | No | No |
> | subAssessments | No | No |
> | tarefas | No | No |
> | topologias | No | No |
> | workspaceSettings | No | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | diagnósticoS | No | No |
> | diagnósticoSettingsCategories | No | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | agregações | No | No |
> | alertaRs | No | No |
> | alertaRuleTemplates | No | No |
> | regras automation | No | No |
> | marcadores | No | No |
> | casos | No | No |
> | dataConnectors | No | No |
> | dataConnectorsCheckRequirements | No | No |
> | entidades | No | No |
> | entidadeQueries | No | No |
> | Incidentes | No | No |
> | escritórioConsents | No | No |
> | definições | No | No |
> | ameaçaIntelligence | No | No |
> | listas de observação | No | No |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | consolas Serviços | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | espaços de nome | Yes | Yes |
> | espaços de nomes / regras de autorização | No | No |
> | espaços de nome / disasterrecoveryconfigs | No | No |
> | espaços de nomes / filtros de eventos | No | No |
> | espaços de nome / redes de regras | No | No |
> | espaços de nome / privateEndpointConnections | No | No |
> | espaços de nomes / filas | No | No |
> | espaços de nome / filas / autorizações | No | No |
> | espaços de nomes / tópicos | No | No |
> | espaços de nome / tópicos / autorizações | No | No |
> | espaços de nomes / tópicos / subscrições | No | No |
> | espaços de nomes / tópicos / subscrições / regras | No | No |
> | premiumMessagingRegions | No | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicações | Yes | Yes |
> | aglomerados | Yes | Yes |
> | clusters / aplicações | No | No |
> | grupos de contentores | Yes | Yes |
> | containerGroupSets | Yes | Yes |
> | edgeclusters | Yes | Yes |
> | edgeclusters / aplicações | No | No |
> | geremclusters | Yes | Yes |
> | gerenciouclusters / não desapoias | No | No |
> | redes | Yes | Yes |
> | lojas secretas | Yes | Yes |
> | lojas secretas / certificados | No | No |
> | lojas secretas / segredos | No | No |
> | volumes | Yes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicações | Yes | Yes |
> | grupos de contentores | Yes | Yes |
> | portais | Yes | Yes |
> | redes | Yes | Yes |
> | segredos | Yes | Yes |
> | volumes | Yes | Yes |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | fornecedorRegistrations | No | No |
> | fornecedorRegistrations / recursosTypeRegistrations | No | No |
> | rollouts | Yes | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | SignalR | Yes | Yes |
> | SignalR / eventGridFilters | No | No |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | contas / contaQuotaPolicies | No | No |
> | contas / grupoPolícias | No | No |
> | contas / empregos | No | No |
> | contas / armazenamentoContainers | No | No |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | híbridoUseBenefits | No | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicaçãoDefinições | Yes | Yes |
> | aplicações | Yes | Yes |
> | jitRequests | Yes | Yes |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | managedInstances | Yes | Yes |
> | managedInstances / bases de dados | Sim (ver [nota abaixo)](#sqlnote) | Yes |
> | managedInstances / bases de dados / backupShortTermRetentionPolicies | No | No |
> | managedInstances / bases de dados / esquemas / tabelas / colunas / sensibilidadeLabels | No | No |
> | managedInstances / bases de dados / vulnerabilidadesAssessments | No | No |
> | managedInstances / bases de dados / vulnerabilidadesAssessments / regras / linhas de base | No | No |
> | managedInstances / encryptionProtector | No | No |
> | managedInstances / chaves | No | No |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | No | No |
> | managedInstances / vulnerabilidadesAssessments | No | No |
> | servidores | Yes | Yes |
> | servidores / administradores | No | No |
> | servidores / comunicaçõesLinks | No | No |
> | servidores / bases de dados | Sim (ver [nota abaixo)](#sqlnote) | Yes |
> | servidores / encriptaçãoProtector | No | No |
> | servidores / firewallRules | No | No |
> | servidores / chaves | No | No |
> | servidores / restauradoresDroppedDatabases | No | No |
> | servidores /objetivos de serviço | No | No |
> | servidores / tdeCertificados | No | No |
> | virtualClusters | No | No |

<a id="sqlnote"></a>

> [!NOTE]
> A base de dados Master não suporta tags, mas outras bases de dados, incluindo bases de dados Azure Synapse Analytics, tags de suporte. As bases de dados Azure Synapse Analytics devem estar no estado ativo (não pausado).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Yes | Yes |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | No | No |
> | SqlVirtualMachines | Yes | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | contas eliminadas | No | No |
> | armazenamento Contas | Yes | Yes |
> | armazenamentoCotas / blobServices | No | No |
> | armazenamentoSCotas / serviços de ficheiros | No | No |
> | armazenamentoSCotas / filas Serviços | No | No |
> | armazenamentoCotas /serviços | No | No |
> | armazenamentoCotas / serviços / métricasDefinitions | No | No |
> | armazenamentoCotas / tableServices | No | No |
> | usos | No | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | caches | Yes | Yes |
> | caches / armazenamentoSTogets | No | No |
> | modelos de uso | No | No |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | grupos de replicação | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Yes | Yes |
> | storageSyncServices / Registros registados | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | armazenamentoSyncServices / fluxos de trabalho | No | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Yes | Yes |
> | storageSyncServices / Registros registados | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | armazenamentoSyncServices / fluxos de trabalho | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Yes | Yes |
> | storageSyncServices / Registros registados | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | armazenamentoSyncServices / fluxos de trabalho | No | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | gestores | Yes | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Yes | Yes |
> | clusters / privateEndpoints | No | No |
> | streamingjobs | Sim (ver nota abaixo) | Yes |

> [!NOTE]
> Não se pode adicionar uma etiqueta quando os trabalhos de streaming estão a funcionar. Pare o recurso para adicionar uma etiqueta.

## <a name="microsoftsubscription"></a>Microsoft.Subscrição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aceitarChangeTenant | No | No |
> | pseudónimos | No | No |
> | cancelar | No | No |
> | changeTenantRequest | No | No |
> | changeTenantStatus | No | No |
> | Criar Subscrição | No | No |
> | ativar | No | No |
> | mudar o nome | No | No |
> | AssinaturaDefinitions | No | No |
> | Subscrições Operações | No | No |
> | assinaturas | No | No |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | Yes | Yes |
> | áreas de trabalho | Yes | Yes |
> | espaços de trabalho / bigDataPools | Yes | Yes |
> | espaços de trabalho / operaçõesEstas | No | No |
> | espaços de trabalho / sqlDatabases | Yes | Yes |
> | espaços de trabalho / sqlPools | Yes | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | ambientes | Yes | No |
> | ambientes / acessosPolícias | No | No |
> | ambientes / fontes de eventos | Yes | No |
> | ambientes / referênciaSSets | Yes | No |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | lojas | Yes | Yes |
> | lojas / acessosPolícias | No | No |
> | lojas / serviços | No | No |
> | lojas / serviços / tokens | No | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | imagenSTas | Yes | Yes |
> | imagenTemplates / runOutputs | No | No |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | ArcZones | Yes | Yes |
> | RecursosPools | Yes | Yes |
> | VCenters | Yes | Yes |
> | VirtualMachines | Yes | Yes |
> | VirtualMachineTemplates | Yes | Yes |
> | VirtualNetworks | Yes | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | DedicadoCloudNodes | Yes | Yes |
> | ServiçosCloud dedicados | Yes | Yes |
> | virtualMachines | Yes | Yes |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | dispositivos | Yes | Yes |
> | registoS de subscrições | No | No |
> | fornecedores | No | No |
> | fornecedores / skus | No | No |
> | fornecedores / vnfs | No | No |
> | virtualNetworkFunctionSkus | No | No |
> | vnfs | Yes | Yes |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | No |
> | planos | Yes | No |
> | registoS de subscrições | No | No |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | apiManagementAcounts | No | No |
> | apiManagementAcounts / apiAcls | No | No |
> | apiManagementAcounts / apis | No | No |
> | apiManagementAcounts / apis / apiAcls | No | No |
> | apiManagementAcounts / apis / connectionAcls | No | No |
> | apiManagementAcounts / apis / conexões | No | No |
> | apiManagementAcounts / apis / conexões / conexõesAcls | No | No |
> | apiManagementAccounts / apis / LocalizedDefinitions | No | No |
> | apiManagementAcounts / connectionAcls | No | No |
> | apiManagementAcounts / conexões | No | No |
> | billingMeters | No | No |
> | certificados | Yes | Yes |
> | connectionGateways | Yes | Yes |
> | conexões | Yes | Yes |
> | customApis | Yes | Yes |
> | deletedSites | No | No |
> | hospedagem Ambientes | Yes | Yes |
> | hospedarEnvironments / eventGridFilters | No | No |
> | hospedamentoEnvironments / multiRolePools | No | No |
> | hospedamentoEnvironments / workerPools | No | No |
> | kubeEnvironments | Yes | Yes |
> | editoresUsers | No | No |
> | recomendações | No | No |
> | recursoHealthMetadata | No | No |
> | tempos de execução | No | No |
> | serverFarms | Yes | Yes |
> | serverFarms / eventGridFilters | No | No |
> | serverFarms / firstPartyApps | No | No |
> | serverFarms / firstPartyApps / keyVaultSettings | No | No |
> | sites | Yes | Yes |
> | sites / config  | No | No |
> | sites / eventGridFilters | No | No |
> | sites / hostNameBindings | No | No |
> | sites / redeConfig | No | No |
> | sites / premieraddons | Yes | Yes |
> | sites / slots | Yes | Yes |
> | sites / slots / eventGridFilters | No | No |
> | sites / slots / hostNameBindings | No | No |
> | sites / slots / redeConfig | No | No |
> | fonteControls | No | No |
> | staticSites | Yes | Yes |
> | validar | No | No |
> | verificarHostingEnvironmentVnet | No | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | diagnósticoS | No | No |
> | diagnósticoSettingsCategories | No | No |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | Yes | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Serviços de Dispositivos | Yes | Yes |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | cargas de trabalho | Yes | Yes |
> | cargas de trabalho / instâncias | No | No |
> | cargas de trabalho / versões | No | No |
> | cargas de trabalho / versões / artefactos | No | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | componentes | No | No |
> | componentesSummary | No | No |
> | monitorInstâncias | No | No |
> | monitorInstancesSummary | No | No |
> | monitores | No | No |
> | notificaçõesS | No | Não |

## <a name="next-steps"></a>Próximos passos

Para aprender a aplicar tags aos recursos, consulte [use tags para organizar os seus recursos Azure.](tag-resources.md)
