---
title: Suporte de etiquetas para recursos
description: Mostra quais os tipos de recursos Azure que suportam tags. Fornece detalhes para todos os serviços da Azure.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 27e25b1048bb759a3b38859788c27c03f1cf0447
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371546"
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
> | Serviços de Domínio | Sim | Sim |
> | Serviços de Domínio / oucontainer | Não | Não |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | apoiarProviders | Não | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Não | Não |
> | adds serviços | Não | Não |
> | agentes | Não | Não |
> | anónimos | Não | Não |
> | configuração | Não | Não |
> | registos | Não | Não |
> | relatórios | Não | Não |
> | serviços de saúde | Não | Não |
> | services | Não | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | advisorScore | Não | Não |
> | configurações | Não | Não |
> | gerarRecommendações | Não | Não |
> | do IdP | Não | Não |
> | recomendações | Não | Não |
> | supressões | Não | Não |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | farmBeats | Sim | Sim |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | actionRules | Sim | Sim |
> | alerts | Não | Não |
> | alertasList | Não | Não |
> | alertasMetaData | Não | Não |
> | alertasSummary | Não | Não |
> | alertaSummaryList | Não | Não |
> | smartDetectorAlertRules | Sim | Sim |
> | smartGroups | Não | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | servidores | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Não | Não |
> | serviço | Sim | Sim |
> | validar Nome de Serviço | Não | Não |

> [!NOTE]
> A Azure API Management só suporta a criação de um máximo de 15 pares de nome/valor para cada serviço.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | configuraçõesStores | Sim | Sim |
> | configuraçõesStores / eventGridFilters | Não | Não |
> | configuraçõesStores / keyValues | Não | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Spring | Sim | Sim |
> | Primavera / aplicativos | Não | Não |
> | Primavera / apps / implementações | Não | Não |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Sim | Sim |
> | predefinidosProviders | Não | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | acessoReviewScheduleDefinitions | Não | Não |
> | acessoReviewScheduleSettings | Não | Não |
> | clássicos Administradores | Não | Não |
> | dataAliases | Não | Não |
> | negar Assignments | Não | Não |
> | elevateAccess | Não | Não |
> | findOrphanRoleAssignments | Não | Não |
> | fechaduras | Não | Não |
> | permissões | Não | Não |
> | políticasAssinsagens | Não | Não |
> | políticasDefinições | Não | Não |
> | políticasExemptions | Não | Não |
> | políticasSetDefinitions | Não | Não |
> | privateLinkAssociations | Não | Não |
> | fornecedorOperações | Não | Não |
> | recursosManagementPrivateLinks | Sim | Sim |
> | papéAs de assinaturas | Não | Não |
> | papelAssignmentsUsageMetrics | Não | Não |
> | funçõesDefinitions | Não | Não |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | configuraçãoProfileAssignments | Não | Não |
> | configuraçãoProfilePreferences | Sim | Sim |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | automação Contas | Sim | Sim |
> | automação Contas/ configurações | Sim | Sim |
> | automação Contas / empregos | Não | Não |
> | automaçãoAcontas / privateEndpointConnectionProxies | Não | Não |
> | automaçãoAs contas / privateEndpointConnections | Não | Não |
> | automação Contas / privateLinkResources | Não | Não |
> | automação Contas / runbooks | Sim | Sim |
> | automaçãoCotas / softwareUpdateConfigurations | Não | Não |
> | automaçãoAcontas / webhooks | Não | Não |

> [!NOTE]
> A Azure Automation só suporta a criação de um máximo de 15 pares de nome/valor para cada recurso Demôm automação.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | privateClouds | Sim | Sim |
> | privateClouds / autorizações | Não | Não |
> | privateClouds / clusters | Não | Não |
> | privateClouds / globalReachConnections | Não | Não |
> | privateClouds / hcxEnterpriseSites | Não | Não |
> | privateClouds / workloadNetworks | Não | Não |
> | privateClouds / workloadNetworks / dhcpConfigurations | Não | Não |
> | privateClouds / workloadNetworks / gateways | Não | Não |
> | privateClouds / workloadNetworks / portMirroringProfiles | Não | Não |
> | privateClouds / workloadNetworks / segmentos | Não | Não |
> | privateClouds / workloadNetworks / virtualMachines | Não | Não |
> | privateClouds / workloadNetworks / vmGroups | Não | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | ambientes | Não | Não |
> | ambientes / contas | Não | Não |
> | ambientes / contas / espaços de nome | Não | Não |
> | ambientes / contas / espaços de nome / configurações | Não | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | b2cDirectes | Sim | Não |
> | b2ctenants | Não | Não |
> | guestUsages | Sim | Sim |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | dataControllers | Sim | Sim |
> | pós-gresInstances | Sim | Sim |
> | sqlManagedInstances | Sim | Sim |
> | sqlServerInstances | Sim | Sim |
> | sqlServerRegistrations | Sim | Sim |
> | sqlServerRegistrations / SqlServers | Não | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Não | Não |
> | edgeSubscriptions | Sim | Sim |
> | linkedSubscriptions | Sim | Sim |
> | registos | Sim | Sim |
> | registos / clienteSubscrições | Não | Não |
> | registos / produtos | Não | Não |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Sim | Sim |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | lotAcons | Sim | Sim |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | faturaçãoAconse de contas | Não | Não |
> | faturaçãoAconssolhos/acordos | Não | Não |
> | faturaçãoAs contas / faturaçõesPermissions | Não | Não |
> | faturaçãoAcounts / billingProfiles | Não | Não |
> | faturação Encomendas / faturaçãoProfiles / faturaçãoPermissions | Não | Não |
> | faturação Encomendas / faturaçãoProfiles / billingRoleAssignments | Não | Não |
> | billingAcounts / faturaçãoProfiles / billingRoleDefinitions | Não | Não |
> | faturação Encomendas / faturaçãoProfiles / faturaçãoSubscrições | Não | Não |
> | billingAcounts / faturaçãoProfiles / createBillingRoleAssignment | Não | Não |
> | faturaçãoAstros /faturaçãoProfiles / clientes | Não | Não |
> | faturaçãoAstros /faturaçãoProfiles / instruções | Não | Não |
> | faturação Encomendas / faturas | Não | Não |
> | faturação Encomendas / faturas / faturas / folha de preços | Não | Não |
> | faturação Encomendas / faturas / faturas / transações | Não | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções | Não | Não |
> | faturaçãoAstros /faturaçãoProfiles / faturaSecções / faturasPermissions | Não | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / billingRoleAssignments | Não | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / billingRoleDefinitions | Não | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / faturaSubscrições | Não | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / createBillingRoleAssignment | Não | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / iniciarTransfer | Não | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / produtos | Não | Não |
> | faturação Encomendas / faturaçãoProfiles / faturaSecções / produtos / transferência | Não | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / produtos / updateAutoRenew | Não | Não |
> | faturação Encomendas / faturaçãoProfiles / faturaSecções / transações | Não | Não |
> | faturação Encomendas / faturaçãoProfiles / faturaSecções / transferências | Não | Não |
> | faturação Encomendas / BillingProfiles / patchOperações | Não | Não |
> | faturação Encomendas / faturaçãoProfiles / pagamentosMes | Não | Não |
> | faturaçãoAcounts / faturaçãoProfiles / políticas | Não | Não |
> | faturaçãoAs contas / faturaçãoProfiles / folha de preços | Não | Não |
> | faturação Encomendas / faturaçãoProfiles / pricesheetDownloadOperations | Não | Não |
> | faturaçãoAstros /faturaçãoProfiles / produtos | Não | Não |
> | faturaçãoAstros /faturaçãoProfiles / reservas | Não | Não |
> | faturação Encomendas / faturaçãoProfiles / transações | Não | Não |
> | faturaçãoAcounts / faturaçãoProfiles / validarDetachPaymentMethodEligibility | Não | Não |
> | billingAcounts / billingRoleAssignments | Não | Não |
> | billingAccounts / billingRoleDefinitions | Não | Não |
> | faturaçãoAcounts / faturaçãoSubscrições | Não | Não |
> | faturaçãoAs contas / faturas/ faturas | Não | Não |
> | billingAcounts / createBillingRoleAssignment | Não | Não |
> | billingAcounts / criarInvoiceSectionsOperations | Não | Não |
> | faturaçãoAsconse de clientes | Não | Não |
> | faturaçãoAs contas / clientes / faturaçõesPermissions | Não | Não |
> | faturaçãoAs contas / clientes / faturaçãoSubscrições | Não | Não |
> | faturaçãoAconssocamos / clientes / iniciarTransfer | Não | Não |
> | faturaçãoAconse de clientes / políticas | Não | Não |
> | faturaçãoAconse de clientes / produtos | Não | Não |
> | faturaçãoAs contas / clientes / transações | Não | Não |
> | faturaçãoAs contas / clientes / transferes | Não | Não |
> | faturaçãoAconsse de departamentos | Não | Não |
> | billingAcounts / departamentos / faturaçõesPermissions | Não | Não |
> | billingAcounts / departamentos / billingRoleAssignments | Não | Não |
> | billingAcounts / departamentos / billingRoleDefinitions | Não | Não |
> | faturaçãoAconse de matrículasAconseções | Não | Não |
> | faturaçãoAs contas / matrículasContas / faturações | Não | Não |
> | faturaçãoAcounts / matrículasContas / billingRoleAssignments | Não | Não |
> | billingAcounts / matrículasContas / billingRoleDefinitions | Não | Não |
> | faturação | Não | Não |
> | faturas / faturas / transações | Não | Não |
> | faturaçãoS / faturaSecções | Não | Não |
> | faturaçãoS / faturaSecções / faturaÇãoSubscriçãoMoveOperações | Não | Não |
> | faturaçãoS / faturaSecções / faturaSubscrições | Não | Não |
> | faturaçãoS / faturaSecções / faturaSubscrições / transferência | Não | Não |
> | faturaçãoS / faturaSecções / elevação | Não | Não |
> | faturaÇãoS / faturaSecções / iniciarTransfer | Não | Não |
> | faturações / faturaSecções / patchOperações | Não | Não |
> | faturações / faturaSecções / produtoMoveOperações | Não | Não |
> | faturaÇãoS / faturaSecções / produtos | Não | Não |
> | faturaÇões / faturaSecções / produtos / transferência | Não | Não |
> | faturaÇões / faturaSecções / produtos / updateAutoRenew | Não | Não |
> | faturaçãoS / faturaSecções / transações | Não | Não |
> | faturaÇões / faturaSecções / transferências | Não | Não |
> | faturaçãoAs contas / linhaOfCredit | Não | Não |
> | faturaçãoOperações / PatchOperações | Não | Não |
> | faturação Contas / pagamentosMes | Não | Não |
> | faturaçãoAconsecos / produtos | Não | Não |
> | faturaçãoAsconse de reservas | Não | Não |
> | faturaçãoAs/transações | Não | Não |
> | faturaçãoPeriods | Não | Não |
> | faturaçãoPermissions | Não | Não |
> | faturaçãoProperty | Não | Não |
> | billingRoleAssignments | Não | Não |
> | billingRoleDefinitions | Não | Não |
> | createBillingRoleAssignment | Não | Não |
> | departamentos | Não | Não |
> | matrículasCotas | Não | Não |
> | faturas | Não | Não |
> | transferências | Não | Não |
> | transferências / aceitarTransfer | Não | Não |
> | transferências / declínioTransfer | Não | Não |
> | transferências / operaçãoStatus | Não | Não |
> | transferências / validarTransfer | Não | Não |
> | validarAddress | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | mapApis | Sim | Sim |
> | actualizaçãoCommunicationPreferência | Não | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Sim | Sim |
> | cordaMembers | Sim | Sim |
> | observadores | Sim | Sim |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | TokenServices | Sim | Sim |
> | TokenServices / BlockchainNetworks | Não | Não |
> | TokenServices / Grupos | Não | Não |
> | TokenServices / Grupos / Contas | Não | Não |
> | TokenServices / TokenTemplates | Não | Não |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | blueprintAsignments | Não | Não |
> | blueprintAssignments / atribuiçãoOperações | Não | Não |
> | blueprintAssignments /operações | Não | Não |
> | plantas | Não | Não |
> | plantas / artefactos | Não | Não |
> | plantas / versões | Não | Não |
> | plantas / versões / artefactos | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | botServs | Sim | Sim |
> | botServices / canais | Não | Não |
> | botServices / conexões | Não | Não |
> | linguagens | Não | Não |
> | modelos | Não | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Redis | Sim | Sim |
> | Redis / EventGridFilters | Não | Não |
> | Redis / privateEndpointConnectionProxies | Não | Não |
> | Redis / privateEndpointConnectionProxies / validar | Não | Não |
> | Redis / privateEndpointConnections | Não | Não |
> | Redis / privateLinkResources | Não | Não |
> | redisEnterprise | Sim | Sim |
> | RedisEnterprise / privateEndpointConnectionProxies | Não | Não |
> | RedisEnterprise / privateEndpointConnectionProxies / validar | Não | Não |
> | RedisEnterprise / privateEndpointConnections | Não | Não |
> | RedisEnterprise / privateLinkResources | Não | Não |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicaçõesReservações | Não | Não |
> | autoQuotaIncrease | Não | Não |
> | calcularExchange | Não | Não |
> | calcularPrimá-lo | Não | Não |
> | calcular Prémio Depurchase | Não | Não |
> | catálogos | Não | Não |
> | comerciaisReservationOrders | Não | Não |
> | troca | Não | Não |
> | própriasreservações | Não | Não |
> | placePurchaseOrder | Não | Não |
> | pedidos de reservas | Não | Não |
> | pedidos de reservas / calcularRefund | Não | Não |
> | reservasOrdeiros / fusão | Não | Não |
> | reservasOrdeiros / Reservas | Não | Não |
> | reservasOrdeiros / reservas / revisões | Não | Não |
> | pedidos de reservas / devolução | Não | Não |
> | pedidos de reservas / divisão | Não | Não |
> | pedidos de reservas / swap | Não | Não |
> | reservas | Não | Não |
> | recursosProviders | Não | Não |
> | resources | Não | Não |
> | validar ReservationOrder | Não | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Não | Não |
> | CdnWebApplicationFirewallPolicies | Sim | Sim |
> | edgenodes | Não | Não |
> | perfis | Sim | Sim |
> | perfis / pontos finais | Sim | Sim |
> | perfis / pontos finais / personaldomains | Não | Não |
> | perfis / pontos finais / grupos de origem | Não | Não |
> | perfis / pontos finais / origens | Não | Não |
> | validarProbe | Não | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | certificadoRes | Sim | Sim |
> | certificadoRes/certificados | Não | Não |
> | validar Informação DeRegistrationRegistrationCertificate | Não | Não |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | perfil | Não | Não |
> | recursosChanges | Não | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | capacidades | Não | Não |
> | domínioNames | Não | Não |
> | domínioS de identidades / capacidades | Não | Não |
> | domainNames / InternaLoadBalancers | Não | Não |
> | domínioNamis / serviçoScertificados | Não | Não |
> | domínioNames / slots | Não | Não |
> | domínioNames / slots / funções | Não | Não |
> | domínioNames / slots / funções / métricasDefinitions | Não | Não |
> | domínioNames / slots / funções / métricas | Não | Não |
> | moveSubscriptionResources | Não | Não |
> | operatingSystemFamilies | Não | Não |
> | sistemas operativos | Não | Não |
> | quotas | Não | Não |
> | recursosTipos | Não | Não |
> | validarSubscriptionMoveAvailability | Não | Não |
> | virtualMachines | Não | Não |
> | virtualMachines / diagnósticoSettings | Não | Não |
> | virtualMachines / métricasDefinitions | Não | Não |
> | virtualMachines / métricas | Não | Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Não | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | capacidades | Não | Não |
> | expressRouteCrossConnections | Não | Não |
> | expressRouteCrossConnections / peerings | Não | Não |
> | gatewaySupportouDevices | Não | Não |
> | networkSecurityGroups | Não | Não |
> | quotas | Não | Não |
> | reservedIps | Não | Não |
> | virtualNetworks | Não | Não |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Não | Não |
> | virtualNetworks / virtualNetworkPeerings | Não | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | capacidades | Não | Não |
> | discos | Não | Não |
> | imagens | Não | Não |
> | osImages | Não | Não |
> | osPlatformImages | Não | Não |
> | publicImages | Não | Não |
> | quotas | Não | Não |
> | armazenamento Contas | Não | Não |
> | armazenamentoCotas / blobServices | Não | Não |
> | armazenamentoSCotas / serviços de ficheiros | Não | Não |
> | armazenamentoCotas / métricasDefinitions | Não | Não |
> | armazenamentoCotas /métricas | Não | Não |
> | armazenamentoSCotas / filas Serviços | Não | Não |
> | armazenamentoCotas /serviços | Não | Não |
> | armazenamentoS / serviços / diagnósticoSettings | Não | Não |
> | armazenamentoCotas / serviços / métricasDefinitions | Não | Não |
> | armazenamentoCotas / serviços / métricas | Não | Não |
> | armazenamentoCotas / tableServices | Não | Não |
> | armazenamentoCotas / vmImages | Não | Não |
> | vmImages | Não | Não |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | planos | Sim | Sim |
> | registoS de subscrições | Não | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas / privateEndpointConnectionProxies | Não | Não |
> | contas / privateEndpointConnections | Não | Não |
> | contas / privateLinkResources | Não | Não |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | RateCard | Não | Não |
> | UsageAggregates | Não | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | disponibilidadeSets | Sim | Sim |
> | cloudServs | Sim | Sim |
> | cloudServices / networkInterfaces | Não | Não |
> | cloudServices / publicIPAddresses | Não | Não |
> | cloudServs / roleInstances | Não | Não |
> | cloudServices / roleInstances / networkInterfaces | Não | Não |
> | cloudServs / funções | Não | Não |
> | diskAccesses | Sim | Sim |
> | DiskEncryptionSets | Sim | Sim |
> | discos | Sim | Sim |
> | galerias | Sim | Sim |
> | galerias / aplicações | Não | Não |
> | galerias / aplicações / versões | Não | Não |
> | galerias / imagens | Não | Não |
> | galerias / imagens / versões | Não | Não |
> | grupos de anfitriões | Sim | Sim |
> | hostGroups / anfitriões | Sim | Sim |
> | imagens | Sim | Sim |
> | grupos de posições de proximidade | Sim | Sim |
> | restaurarPointCollections | Sim | Sim |
> | restaurarPointCollections / restaurarPonts | Não | Não |
> | sharedVMExtensions | Sim | Sim |
> | sharedVMExtensions / versões | Não | Não |
> | sharedVMImages | Sim | Sim |
> | sharedVMImages / versões | Não | Não |
> | instantâneos | Sim | Sim |
> | sshPublicKeys | Sim | Sim |
> | virtualMachines | Sim | Sim |
> | virtualMachines / extensões | Sim | Sim |
> | virtualMachines / métricasDefinitions | Não | Não |
> | virtualMachines / runCommands | Sim | Sim |
> | virtualMachineScaleSets | Sim | Sim |
> | virtualMachineScaleSets / extensões | Não | Não |
> | virtualMachineScaleSets / networkInterfaces | Não | Não |
> | virtualMachineScaleSets / publicIPAddresses | Sim | Não |
> | virtualMachineScaleSets / virtualMachines | Não | Não |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Não | Não |

> [!NOTE]
> Não é possível adicionar uma etiqueta a uma máquina virtual que foi marcada como generalizada. Marca uma máquina virtual generalizada com [Set-AzVm -Generalização](/powershell/module/Az.Compute/Set-AzVM) ou [az vm generalizando](/cli/azure/vm#az-vm-generalize).

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | CacheNodes | Sim | Sim |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
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
> | Mercados | Não | Não |
> | Folhas de preços | Não | Não |
> | produtos | Não | Não |
> | ReservasDetails | Não | Não |
> | ReservasRecommendationDetails | Não | Não |
> | Recommendações de Reservas | Não | Não |
> | ReservasSumários | Não | Não |
> | Transferências de Reservas | Não | Não |
> | Etiquetas | Não | Não |
> | inquilinos | Não | Não |
> | Termos | Não | Não |
> | UsageDetails | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | grupos de contentores | Sim | Sim |
> | serviceAssociationLinks | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | registos | Sim | Sim |
> | registos / agentesPools | Sim | Sim |
> | registos / construções | Não | Não |
> | registos / construções / cancelar | Não | Não |
> | registos / construções / getLogLink | Não | Não |
> | registos / buildTasks | Sim | Sim |
> | registos / buildTasks / passos | Não | Não |
> | registos / eventGridFilters | Não | Não |
> | registos / exportaçõesPipelines | Não | Não |
> | registos / gerarCredentis | Não | Não |
> | registos / getBuildSourceUploadUrl | Não | Não |
> | registos / GetCredentials | Não | Não |
> | registos / importImage | Não | Não |
> | registos / importapipelines | Não | Não |
> | registos / pipelineRuns | Não | Não |
> | registos / privateEndpointConnectionProxies | Não | Não |
> | registos / privateEndpointConnectionProxies / validar | Não | Não |
> | registos / privateEndpointConnections | Não | Não |
> | registos / privateLinkResources | Não | Não |
> | registos / filasBuild | Não | Não |
> | registos / regeneraçãoCredential | Não | Não |
> | registos / regeneraçãoCredentais | Não | Não |
> | registos / replicações | Sim | Sim |
> | registos / corre | Não | Não |
> | registos / corre / cancela | Não | Não |
> | registos / horáriosRun | Não | Não |
> | registos / scopeMaps | Não | Não |
> | registos / taskRuns | Não | Não |
> | registos / tarefas | Sim | Sim |
> | registos / fichas | Não | Não |
> | registos / actualizaçãoPolícias | Não | Não |
> | registos / webhooks | Sim | Sim |
> | registros / webhooks / getCallbackConfig | Não | Não |
> | registos / webhooks / ping | Não | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | serviços de contentores | Sim | Sim |
> | geridoClusters | Sim | Sim |
> | openShiftManagedClusters | Sim | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Alertas | Não | Não |
> | Faturação | Não | Não |
> | Orçamentos | Não | Não |
> | CloudConnectors | Não | Não |
> | Conectores | Sim | Sim |
> | costAllocationRules | Não | Não |
> | Departamentos | Não | Não |
> | Dimensões | Não | Não |
> | MatrículasAconselhos | Não | Não |
> | Exportações | Não | Não |
> | Contas externas deBilling | Não | Não |
> | ExternalBillingAccounts / Alertas | Não | Não |
> | ExternalBillingAccounts / Dimensões | Não | Não |
> | Contas externas deBilling / Previsão | Não | Não |
> | ExternalBillingAccounts / Consulta | Não | Não |
> | Subscrições externas | Não | Não |
> | Subscrições externas / Alertas | Não | Não |
> | ExternalSubscriptions / Dimensões | Não | Não |
> | ExternalSubscriptions / Previsão | Não | Não |
> | Subscrições externas / Consulta | Não | Não |
> | Previsão | Não | Não |
> | Informações | Não | Não |
> | Consulta | Não | Não |
> | registar | Não | Não |
> | Configs relatório | Não | Não |
> | Relatórios | Não | Não |
> | Definições | Não | Não |
> | showbackRules | Não | Não |
> | Vistas | Não | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | pedidos | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | associações | Não | Não |
> | recursosProviders | Sim | Sim |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | casos | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | empregos | Sim | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Sim | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | áreas de trabalho | Sim | Sim |
> | espaços de trabalho / dbWorkspaces | Não | Não |
> | espaços de trabalho / armazenamentoEncryption | Não | Não |
> | espaços de trabalho / virtualNetworkPeerings | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | catálogos | Sim | Sim |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | dataFactories | Sim | Sim |
> | dataFactories / diagnosticSettings | Não | Não |
> | dataFactories / métricasDefinitions | Não | Não |
> | dataFactorySchema | Não | Não |
> | fábricas | Sim | Sim |
> | fábricas / integraçãoRuntimes | Não | Não |

> [!NOTE]
> Se tiver tempos de integração Azure-SSIS na sua fábrica de dados, o seu custo de funcionamento será marcado com etiquetas de fábrica de dados. Os tempos de execução da integração Azure-SSIS devem ser interrompidos e reiniciados para que novas etiquetas de fábrica de dados sejam aplicadas ao seu custo de funcionamento.

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas / dadosLakeStoreAccounts | Não | Não |
> | contas / armazenamentoCotações | Não | Não |
> | contas / armazenamentoCotas / contentores | Não | Não |
> | contas / transferAnlyticsUnits | Não | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas / eventGridFilters | Não | Não |
> | contas / firewallRules | Não | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | services | Não | Não |
> | serviços / projetos | Não | Não |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Sim | Sim |
> | Fontes de recursos | Sim | Sim |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas / ações | Não | Não |
> | contas / ações / conjuntos de dados | Não | Não |
> | contas / ações / convites | Não | Não |
> | contas / ações / subscrições de partilha de fornecedores | Não | Não |
> | contas / ações / sincronizaçõesS | Não | Não |
> | contas / subscrições de ações | Não | Não |
> | contas / subscrições de ações / consumerSourceDataSets | Não | Não |
> | contas / subscrições de ações / datasetmappings | Não | Não |
> | contas / subscrições de ações / gatilhos | Não | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | servidores | Sim | Sim |
> | servidores / assessores | Não | Não |
> | servidores / chaves | Não | Não |
> | servidores / privadosEndpointConnectionProxies | Não | Não |
> | servidores / privateEndpointConnections | Não | Não |
> | servidores / privateLinkResources | Não | Não |
> | servidores / consultasTextos | Não | Não |
> | servidores / recuperáveisServers | Não | Não |
> | servidores / iniciar | Não | Não |
> | servidores / parar | Não | Não |
> | servidores / topQueryStatistics | Não | Não |
> | servidores / virtualNetworkRules | Não | Não |
> | servidores / esperaStatistics | Não | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Sim | Sim |
> | servidores | Sim | Sim |
> | servidores / assessores | Não | Não |
> | servidores / chaves | Não | Não |
> | servidores / privadosEndpointConnectionProxies | Não | Não |
> | servidores / privateEndpointConnections | Não | Não |
> | servidores / privateLinkResources | Não | Não |
> | servidores / consultasTextos | Não | Não |
> | servidores / recuperáveisServers | Não | Não |
> | servidores / iniciar | Não | Não |
> | servidores / parar | Não | Não |
> | servidores / topQueryStatistics | Não | Não |
> | servidores / upgrade | Não | Não |
> | servidores / virtualNetworkRules | Não | Não |
> | servidores / esperaStatistics | Não | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DbforPostgresql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Sim | Sim |
> | grupos de servidores | Sim | Sim |
> | servidores | Sim | Sim |
> | servidores / assessores | Não | Não |
> | servidores / chaves | Não | Não |
> | servidores / privadosEndpointConnectionProxies | Não | Não |
> | servidores / privateEndpointConnections | Não | Não |
> | servidores / privateLinkResources | Não | Não |
> | servidores / consultasTextos | Não | Não |
> | servidores / recuperáveisServers | Não | Não |
> | servidores / topQueryStatistics | Não | Não |
> | servidores / virtualNetworkRules | Não | Não |
> | servidores / esperaStatistics | Não | Não |
> | servidorv2 | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | artefactosSources | Sim | Sim |
> | rollouts | Sim | Sim |
> | serviceTopologias | Sim | Sim |
> | serviçoTopologias / serviços | Sim | Sim |
> | serviceTopologias / serviços / serviços Unidades | Sim | Sim |
> | passos | Sim | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | grupos de aplicações | Sim | Sim |
> | grupos de aplicações / aplicações | Não | Não |
> | grupos de aplicações / desktops | Não | Não |
> | grupos de aplicações / startmenuitems | Não | Não |
> | hostpools | Sim | Sim |
> | hostpools / sessão | Não | Não |
> | hostpools / sessionhosts / utilizadores | Não | Não |
> | hostpools / utilizadores | Não | Não |
> | áreas de trabalho | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Elásticos | Sim | Sim |
> | Elásticos / IotHubTenants | Sim | Sim |
> | Elásticos / IotHubTenants / securitySettings | Não | Não |
> | IotHubs | Sim | Sim |
> | IotHubs / eventGridFilters | Não | Não |
> | IotHubs / securitySettings | Não | Não |
> | Serviços de Provisionamento | Sim | Sim |
> | usos | Não | Não |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas / instâncias | Sim | Sim |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | oleodutos | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | controladores | Sim | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | centros de laboratório | Sim | Sim |
> | laboratórios | Sim | Sim |
> | laboratórios / ambientes | Sim | Sim |
> | laboratórios / serviceRunners | Sim | Sim |
> | laboratórios / virtualMachines | Sim | Sim |
> | horários | Sim | Sim |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | Sim | Sim |
> | digitalTwinsInstances / endpoints | Não | Não |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | base de dadosSes | Não | Não |
> | bases de dadosAconses | Sim | Sim |
> | RestauraçãoDatabaseAccounts | Não | Não |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | domínios | Sim | Sim |
> | domínios / domainOwnershipIdentifiers | Não | Não |
> | gerarSsoRequest | Não | Não |
> | topLevelDomains | Não | Não |
> | validar aDomainRegistrationInformation | Não | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | LCSprojects | Não | Não |
> | LCSprojects / deplosagens de nuvens | Não | Não |
> | LCSprojects / conectores | Não | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | domínios | Sim | Sim |
> | domínios / tópicos | Não | Não |
> | eventoSubscrições | Não | Não |
> | extensãoTopics | Não | Não |
> | parceiroNamespaces | Sim | Sim |
> | partnerNamespaces / eventChannels | Não | Não |
> | partnerRegistrations | Sim | Sim |
> | partnerTopics | Sim | Sim |
> | partnerTopics / eventoSubscrições | Não | Não |
> | systemTopics | Sim | Sim |
> | systemTopics / eventSubscrições | Não | Não |
> | tópicos | Sim | Sim |
> | tópicotips | Não | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Sim | Sim |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / regras de autorização | Não | Não |
> | espaços de nome / disasterrecoveryconfigs | Não | Não |
> | espaços de nomes / eventhubs | Não | Não |
> | espaços de nome / eventhubs / autorizações | Não | Não |
> | espaços de nomes / eventhubs / grupos de consumidores | Não | Não |
> | espaços de nome / redes de regras | Não | Não |
> | espaços de nome / privateEndpointConnections | Não | Não |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | experimentOs espaços de trabalho | Sim | Sim |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | espaços de nome | Sim | Sim |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | recursosProviders | Não | Não |
> | funcionalidades | Não | Não |
> | fornecedores | Não | Não |
> | subscriçõesFesturaRegistrações | Não | Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | inscrever | Não | Não |
> | galleryitems | Não | Não |
> | generateartifactacssuri | Não | Não |
> | myareas | Não | Não |
> | myareas / áreas | Não | Não |
> | myareas / áreas / áreas | Não | Não |
> | myareas / áreas / áreas / galleryitems | Não | Não |
> | myareas / áreas / galleryitems | Não | Não |
> | myareas / galleryitems | Não | Não |
> | registar | Não | Não |
> | resources | Não | Não |
> | retrieveresourcesbyid | Não | Não |

## <a name="microsoftgenomics"></a>Microsoft.Geómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Sim | Sim |
> | autoManagedVmConfigurationProfiles | Sim | Sim |
> | configuraçãoProfileAssignments | Não | Não |
> | guestConfigurationAssignments | Não | Não |
> | software | Não | Não |
> | softwareUpdateProfile | Não | Não |
> | softwareUpdates | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Sim | Sim |
> | sapMonitores | Sim | Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | DEDICADOHSMs | Sim | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Sim | Sim |
> | clusters / aplicações | Não | Não |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | services | Sim | Sim |
> | serviços / iomtconnectors | Não | Não |
> | serviços / iomtconnectors / conexões | Não | Não |
> | serviços / iomtconnectors / mapeamentos | Não | Não |
> | serviços / privateEndpointConnectionProxies | Não | Não |
> | serviços / privateEndpointConnections | Não | Não |
> | serviços / privateLinkResources | Não | Não |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | máquinas | Sim | Sim |
> | máquinas / avaliaçãoPatches | Não | Não |
> | máquinas / extensões | Sim | Sim |
> | máquinas / instalaçãoPatches | Não | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | dataSmanagers | Sim | Sim |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | dispositivos | Sim | Sim |
> | registoS de subscrições | Não | Não |
> | fornecedores | Não | Não |
> | fornecedores / fornecedoreskus | Não | Não |
> | fornecedores / vendorskus / pré-visualizaçãoSubscrições | Não | Não |
> | funções de rede virtual | Sim | Sim |
> | virtualnetworkfunctionvendores | Não | Não |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | componentes | Sim | Sim |
> | networkScopes | Sim | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | empregos | Sim | Sim |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | grupos de ação | Sim | Sim |
> | atividadeSalerts | Sim | Sim |
> | alertas | Sim | Sim |
> | autoescala | Sim | Sim |
> | componentes | Sim | Sim |
> | componentes / linkedStorageAccounts | Não | Não |
> | componentes / ProactiveDetectionConfigs | Não | Não |
> | diagnósticoS | Não | Não |
> | guestDiagnosticSettings | Sim | Sim |
> | guestDiagnosticSettingsAssociation | Sim | Sim |
> | logprofiles | Sim | Sim |
> | metricAlerts | Sim | Sim |
> | privateLinkScopes | Sim | Sim |
> | privateLinkScopes / privateEndpointConnections | Não | Não |
> | privateLinkScopes / scopedResources | Não | Não |
> | pacotes de consultas | Sim | Sim |
> | consultas de consultas | Não | Não |
> | agendadoQueryRules | Sim | Sim |
> | webtests | Sim | Sim |
> | livros | Sim | Sim |
> | estalagens de livros | Sim | Sim |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | diagnósticoS | Não | Não |
> | diagnósticoSettingsCategories | Não | Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | appTemplates | Não | Não |
> | IoTApps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Graph | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Não | Não |
> | hsmPools | Sim | Sim |
> | managedHSMs | Sim | Sim |
> | cofres | Sim | Sim |
> | cofres / acessosPolícias | Não | Não |
> | cofres / eventoSGridFiltros | Não | Não |
> | cofres / chaves | Não | Não |
> | cofres / chaves / versões | Não | Não |
> | cofres / segredos | Não | Não |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | clusters conectados | Sim | Sim |
> | registoS de subscrições | Não | Não |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | extensões | Não | Não |
> | fonteControlConfigurations | Não | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Sim | Sim |
> | clusters / confinações de base de dados | Não | Não |
> | clusters / bases de dados | Não | Não |
> | clusters / bases de dados / ligações de dados | Não | Não |
> | clusters / bases de dados / ligações eventhub | Não | Não |
> | clusters / bases de dados / principais assinaturas | Não | Não |
> | clusters / ligações de dados | Não | Não |
> | aglomerados / principais destacamentos | Não | Não |
> | clusters / semelhanças partilhadas | Não | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | contas de laboratório | Sim | Não |
> | utilizadores | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | hospedagem Ambientes | Sim | Sim |
> | integraçãoAconselhos | Sim | Sim |
> | integração ServiçoEnvironments | Sim | Sim |
> | integraçãoServiceEnvironments / managedApis | Não | Não |
> | isoladosEnvironments | Sim | Sim |
> | fluxos de trabalho | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | planos de compromissos | Sim | Sim |
> | webServices | Sim | Sim |
> | Áreas de trabalho | Sim | Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | áreas de trabalho | Sim | Sim |
> | espaços de trabalho / computas | Não | Não |
> | espaços de trabalho / eventosGridFiltros | Não | Não |
> | espaços de trabalho / linkedServices | Não | Não |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicardados | Não | Não |
> | configuraçõesAssignments | Não | Não |
> | configuções de manutenção | Sim | Sim |
> | publicMaintenanceConfigurations | Não | Não |
> | atualizações | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Identidades | Não | Não |
> | nomeações para assediadas do utilizador | Sim | Sim |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | managedNetworks | Sim | Sim |
> | managedNetworks / managedNetworkGroups | Sim | Sim |
> | managedNetworks / managedNetworkPeeringPolicies | Sim | Sim |
> | notificação | Sim | Sim |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Não | Não |
> | registosAssondos | Não | Não |
> | registoDefinições | Não | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | obterEntidades | Não | Não |
> | grupos de gestão | Não | Não |
> | managementGroups / configurações | Não | Não |
> | resources | Não | Não |
> | startTenantBackfill | Não | Não |
> | inquilinoBackfillStatus | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas / eventGridFilters | Não | Não |
> | contas / privateAtlases | Sim | Sim |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | macc | Não | Não |
> | ofertas | Não | Não |
> | oferta deTipos | Não | Não |
> | oferta deTipos / editores | Não | Não |
> | oferta DeTipos / editores / ofertas | Não | Não |
> | oferta DeTipos / editores / ofertas / planos | Não | Não |
> | oferta DeTipos / editores / ofertas / planos / acordos | Não | Não |
> | ofertaTipos / editores / ofertas / planos / configs | Não | Não |
> | ofertaTipos / editoras / ofertas / planos / configs / importImage | Não | Não |
> | privadas | Não | Não |
> | privateStoreClient | Não | Não |
> | lojas privadas | Não | Não |
> | privateStores / ofertas | Não | Não |
> | produtos | Não | Não |
> | editores | Não | Não |
> | editores / ofertas | Não | Não |
> | editores / ofertas / alterações | Não | Não |
> | registar | Não | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Sim | Sim |
> | actualizaçãoCommunicationPreferência | Não | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | acordos | Não | Não |
> | tipos de ofertas | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | mediaservices | Sim | Sim |
> | mediaservices / filtros de conta | Não | Não |
> | mediaserviços / ativos | Não | Não |
> | mediaservices / ativos / activosFiltros | Não | Não |
> | mediaservices / conteúdoKeyPolicies | Não | Não |
> | mediaservices / eventGridFilters | Não | Não |
> | mediaservices / liveEventOperações | Não | Não |
> | mediaservices / liveEvents | Sim | Sim |
> | mediaservices / liveEvents / liveOutputs | Não | Não |
> | mediaservices / liveOutputOperations | Não | Não |
> | mediaservices / mediaGraphs | Não | Não |
> | mediaservices / privateEndpointConnectionOperations | Não | Não |
> | mediaservices / privateEndpointConnectionProxies | Não | Não |
> | mediaservices / privateEndpointConnections | Não | Não |
> | mediaservices / streamingEndpointOperations | Não | Não |
> | mediaservices / streamingEndpoints | Sim | Sim |
> | mediaservices / streamingLocators | Não | Não |
> | mediaservices / streamingPolicies | Não | Não |
> | mediaservices / transforma | Não | Não |
> | mediaservices / transforma / empregos | Não | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | appClusters | Sim | Sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | avaliaçãoProjectos | Sim | Sim |
> | migrarprojects | Sim | Sim |
> | moveCollections | Sim | Sim |
> | projetos | Sim | Sim |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | holográficosBroadcastAccounts | Sim | Sim |
> | objetoUnderstandingAccounts | Sim | Sim |
> | remotorcoscos | Sim | Sim |
> | spatialAnchorsAccounts | Sim | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Sim | Não |
> | netAppAccounts / accountBackups | Não | Não |
> | netAppAccounts / capacityPools | Sim | Não |
> | netAppAccounts / capacityPools / volumes | Sim | Não |
> | netAppAccounts / capacityPools / volumes / snapshots | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Sim | Sim |
> | aplicaçãoGatewayWebApplicationFirewallPolicies | Sim | Sim |
> | applicationSecurityGroups | Sim | Sim |
> | azureFirewallFqdnTags | Não | Não |
> | azureFirewalls | Sim | Não |
> | bastionHosts | Sim | Não |
> | bgpServiceCommunidades | Não | Não |
> | conexões | Sim | Sim |
> | ddosCustomPolicies | Sim | Sim |
> | ddosProtectionPlans | Sim | Sim |
> | dnsOperationStatuses | Não | Não |
> | dnszones | Sim | Sim |
> | dnszones / A | Não | Não |
> | dnszones / AAAA | Não | Não |
> | dnszones / todos os | Não | Não |
> | dnszones / CAA | Não | Não |
> | dnszones / CNAME | Não | Não |
> | dnszones / MX | Não | Não |
> | dnszones / NS | Não | Não |
> | dnszones / PTR | Não | Não |
> | dnszones / recordets | Não | Não |
> | dnszones / SOA | Não | Não |
> | dnszones / SRV | Não | Não |
> | dnszones / TXT | Não | Não |
> | expressRouteCircuits | Sim | Sim |
> | expressRouteCrossConnections | Sim | Sim |
> | expressRouteGateways | Sim | Sim |
> | expressRoutePorts | Sim | Sim |
> | expressRouteServiceProviders | Não | Não |
> | firewallPolicies | Sim | Sim |
> | frontdoors | Sim, mas limitado (ver [nota abaixo)](#frontdoor) | Sim |
> | frontdoorWebApplicationFirewallManagedRuleSets | Sim, mas limitado (ver [nota abaixo)](#frontdoor) | Não |
> | frontdoorWebApplicationFirewallPolicies | Sim, mas limitado (ver [nota abaixo)](#frontdoor) | Sim |
> | obter Retecnretecencia | Não | Não |
> | internaNotificar | Não | Não |
> | ipGrupos | Sim | Sim |
> | loadBalancers | Sim | Sim |
> | localNetworkGateways | Sim | Sim |
> | natGateways | Sim | Sim |
> | networkIntentPolicies | Sim | Sim |
> | networkInterfaces | Sim | Sim |
> | redesProfiles | Sim | Sim |
> | networkSecurityGroups | Sim | Sim |
> | networkWatchers | Sim | Sim |
> | networkWatchers / connectionMonitors | Sim | Não |
> | networkWatchers / flowLogs | Não | Não |
> | networkWatchers / lentes | Sim | Não |
> | networkWatchers / pingMeshes | Sim | Não |
> | p2sVpnGateways | Sim | Sim |
> | privateDnsOperationStatuses | Não | Não |
> | privateDnsZones | Sim | Sim |
> | privateDnsZones / A | Não | Não |
> | privateDnsZones / AAAA | Não | Não |
> | privateDnsZones / todos | Não | Não |
> | privateDnsZones / CNAME | Não | Não |
> | privateDnsZones / MX | Não | Não |
> | privateDnsZones / PTR | Não | Não |
> | privateDnsZones / SOA | Não | Não |
> | privateDnsZones / SRV | Não | Não |
> | privateDnsZones / TXT | Não | Não |
> | privateDnsZones / virtualNetworkLinks | Sim | Sim |
> | privadosEndpoints | Sim | Sim |
> | privateLinkServices | Sim | Sim |
> | publicIPAddresses | Sim | Sim |
> | publicIPPrefixos | Sim | Sim |
> | routeFilters | Sim | Sim |
> | routeTables | Sim | Sim |
> | serviceEndpointPolicies | Sim | Sim |
> | trafficManagerGeographicHierarchies | Não | Não |
> | trafficmanagerprofiles | Sim | Sim |
> | trafficmanagerprofiles/heatMaps | Não | Não |
> | trafficManagerUserMetricsKeys | Não | Não |
> | virtualHubs | Sim | Sim |
> | virtualNetworkGateways | Sim | Sim |
> | virtualNetworks | Sim | Sim |
> | virtualNetworks / sub-redes | Não | Não |
> | virtualNetworkTaps | Sim | Sim |
> | virtualWans | Sim | Não |
> | VPNGateways | Sim | Sim |
> | VPNSites | Sim | Sim |
> | webApplicationFirewallPolicies | Sim | Sim |

<a id="frontdoor"></a>

> [!NOTE]
> Para o Serviço de Porta Frontal Azure, pode aplicar tags ao criar o recurso, mas atualizar ou adicionar tags não é suportado atualmente.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | CadernoProxies | Não | Não |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | espaços de nome | Sim | Não |
> | espaços de nomes / notificaçõesHubs | Sim | Não |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Sim | Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | HiperVSites | Sim | Sim |
> | ImportaçõesSites | Sim | Sim |
> | MestresSites | Sim | Sim |
> | ServidorEsites | Sim | Sim |
> | VMwareSites | Sim | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Sim | Sim |
> | eliminadosEspaços de trabalho | Não | Não |
> | linkTargets | Não | Não |
> | storageInsightConfigs | Não | Não |
> | áreas de trabalho | Sim | Sim |
> | espaços de trabalho / dataExports | Não | Não |
> | espaços de trabalho / dataSources | Não | Não |
> | espaços de trabalho / linkedServices | Não | Não |
> | workspaces / linkedStorageAccounts | Não | Não |
> | espaços de trabalho / metadados | Não | Não |
> | espaços de trabalho / consulta | Não | Não |
> | espaços de trabalho / scopedPrivateLinkProxies | Não | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | sociedades de gestão | Não | Não |
> | configurações de gestão | Sim | Sim |
> | soluções | Sim | Sim |
> | Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Não | Não |
> | peerAsns | Não | Não |
> | espreiteiros | Sim | Sim |
> | peeringServiceCountries | Não | Não |
> | peeringServiceProviders | Não | Não |
> | serviços de peering | Sim | Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | atestados | Não | Não |
> | policyEvents | Não | Não |
> | policyMetadata | Não | Não |
> | estados de política | Não | Não |
> | policyTrackedResources | Não | Não |
> | remediações | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Consolas do | Não | Não |
> | dashboards | Sim | Sim |
> | userSettings | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Sim | Sim |
> | inquilinos | Sim | Sim |
> | inquilinos / espaços de trabalho | Não | Não |
> | workspaceCollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | capacidades | Sim | Sim |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas eliminadas | Não | Não |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | fornecedorRegistrations | Não | Não |
> | fornecedorRegistrations /defaultRollouts | Não | Não |
> | fornecedorRegistrations / recursosTypeRegistrations | Não | Não |
> | rollouts | Sim | Sim |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Áreas de trabalho | Sim | Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Não | Não |
> | cofres | Sim | Sim |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatopenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Sim | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / regras de autorização | Não | Não |
> | namespaces / ligações híbridas | Não | Não |
> | espaços de nome / ligações híbridas / regras de autorização | Não | Não |
> | espaços de nome / privateEndpointConnections | Não | Não |
> | espaços de nomes / wcfrelays | Não | Não |
> | espaços de nome / wcfrelays / autorizações | Não | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | consultas | Sim | Sim |
> | recursosChangeDetails | Não | Não |
> | recursosChanges | Não | Não |
> | resources | Não | Não |
> | recursosHistória | Não | Não |
> | subscriçõesStatus | Não | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | disponibilidade Estatísticas | Não | Não |
> | criançaSetilabilityStatuses | Não | Não |
> | criançaresources | Não | Não |
> | emergentes | Não | Não |
> | eventos | Não | Não |
> | impactedResources | Não | Não |
> | do IdP | Não | Não |
> | notificações | Não | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | calcularTemplateHash | Não | Não |
> | implementações | Sim | Não |
> | implantações / operações | Não | Não |
> | implementaçõesScripts | Sim | Sim |
> | implementaçõesScripts / registos | Não | Não |
> | ligações | Não | Não |
> | notificarResourceJobs | Não | Não |
> | fornecedores | Não | Não |
> | grupos de recursos | Sim | Não |
> | assinaturas | Sim | Não |
> | modeloSpecs | Sim | Sim |
> | modeloSpecs / versões | Sim | Sim |
> | inquilinos | Não | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicações | Sim | Sim |
> | saasresources | Não | Não |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | nuvens | Sim | Sim |
> | VirtualMachines | Sim | Sim |
> | VirtualMachineTemplates | Sim | Sim |
> | VirtualNetworks | Sim | Sim |
> | vmmservers | Sim | Sim |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | recursoHealthMetadata | Não | Não |
> | serviços de pesquisa | Sim | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Não | Não |
> | advancedThreatProtectionSettings | Não | Não |
> | alerts | Não | Não |
> | alertaSuppressãoRules | Não | Não |
> | permitidoConnections | Não | Não |
> | aplicaçãoWhitelistings | Não | Não |
> | avaliaçãoMetadata | Não | Não |
> | avaliações | Não | Não |
> | autoDismissAlertsRules | Não | Não |
> | automações | Sim | Sim |
> | AutoProvisioningSettings | Não | Não |
> | Cumprimentos | Não | Não |
> | conectores | Não | Não |
> | dataCollectionAgents | Não | Não |
> | grupos de segurança de dispositivos | Não | Não |
> | descobertasSecuritySolutions | Não | Não |
> | sistemas de Segurança externos | Não | Não |
> | InformaçãoProtetionPolicies | Não | Não |
> | iotDefenderSettings | Não | Não |
> | iotSecuritySolutions | Sim | Sim |
> | iotSecuritySolutions / analyticsModels | Não | Não |
> | iotSecuritySolutions / analyticsModels / agregadosAlerts | Não | Não |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Não | Não |
> | iotSecuritySolutions / iotAlerts | Não | Não |
> | iotSecuritySolutions / iotAlertTypes | Não | Não |
> | iotSecuritySolutions / iotRecommendations | Não | Não |
> | iotSecuritySolutions / iotRecommendationTypes | Não | Não |
> | iotSensores | Não | Não |
> | jitNetworkAccessPolicies | Não | Não |
> | jitPolicies | Não | Não |
> | políticas | Não | Não |
> | preços | Não | Não |
> | regulatórioComplianceStandards | Não | Não |
> | regulatórioComplianceStandards /regulatórioComplianceControls | Não | Não |
> | regulatórioComplianceStands / regulatórioComplianceControls / regulatórioComplianceAssess | Não | Não |
> | secureScoreControlDefinitions | Não | Não |
> | secureScoreControls | Não | Não |
> | secureScores | Não | Não |
> | secureScores / secureScoreControls | Não | Não |
> | securityContacts | Não | Não |
> | segurançaSoluções | Não | Não |
> | securitySolutionsReferenceData | Não | Não |
> | securityStatuses | Não | Não |
> | securityStatusesSummaries | Não | Não |
> | servidorVulnerabilityAssessments | Não | Não |
> | definições | Não | Não |
> | sqlVulnerabilityAssesments | Não | Não |
> | subAssessments | Não | Não |
> | tarefas | Não | Não |
> | topologias | Não | Não |
> | workspaceSettings | Não | Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | diagnósticoS | Não | Não |
> | diagnósticoSettingsCategories | Não | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | agregações | Não | Não |
> | alertaRs | Não | Não |
> | alertaRuleTemplates | Não | Não |
> | regras automation | Não | Não |
> | marcadores | Não | Não |
> | casos | Não | Não |
> | dataConnectors | Não | Não |
> | dataConnectorsCheckRequirements | Não | Não |
> | entidades | Não | Não |
> | entidadeQueries | Não | Não |
> | Incidentes | Não | Não |
> | escritórioConsents | Não | Não |
> | definições | Não | Não |
> | ameaçaIntelligence | Não | Não |
> | listas de observação | Não | Não |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | consolas Serviços | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / regras de autorização | Não | Não |
> | espaços de nome / disasterrecoveryconfigs | Não | Não |
> | espaços de nomes / filtros de eventos | Não | Não |
> | espaços de nome / redes de regras | Não | Não |
> | espaços de nome / privateEndpointConnections | Não | Não |
> | espaços de nomes / filas | Não | Não |
> | espaços de nome / filas / autorizações | Não | Não |
> | espaços de nomes / tópicos | Não | Não |
> | espaços de nome / tópicos / autorizações | Não | Não |
> | espaços de nomes / tópicos / subscrições | Não | Não |
> | espaços de nomes / tópicos / subscrições / regras | Não | Não |
> | premiumMessagingRegions | Não | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicações | Sim | Sim |
> | aglomerados | Sim | Sim |
> | clusters / aplicações | Não | Não |
> | grupos de contentores | Sim | Sim |
> | containerGroupSets | Sim | Sim |
> | edgeclusters | Sim | Sim |
> | edgeclusters / aplicações | Não | Não |
> | geremclusters | Sim | Sim |
> | gerenciouclusters / não desapoias | Não | Não |
> | redes | Sim | Sim |
> | lojas secretas | Sim | Sim |
> | lojas secretas / certificados | Não | Não |
> | lojas secretas / segredos | Não | Não |
> | volumes | Sim | Sim |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicações | Sim | Sim |
> | grupos de contentores | Sim | Sim |
> | portais | Sim | Sim |
> | redes | Sim | Sim |
> | segredos | Sim | Sim |
> | volumes | Sim | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | fornecedorRegistrations | Não | Não |
> | fornecedorRegistrations / recursosTypeRegistrations | Não | Não |
> | rollouts | Sim | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | SignalR | Sim | Sim |
> | SignalR / eventGridFilters | Não | Não |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | híbridoUseBenefits | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicaçãoDefinições | Sim | Sim |
> | aplicações | Sim | Sim |
> | jitRequests | Sim | Sim |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | managedInstances | Sim | Sim |
> | managedInstances / bases de dados | Sim (ver [nota abaixo)](#sqlnote) | Sim |
> | managedInstances / bases de dados / backupShortTermRetentionPolicies | Não | Não |
> | managedInstances / bases de dados / esquemas / tabelas / colunas / sensibilidadeLabels | Não | Não |
> | managedInstances / bases de dados / vulnerabilidadesAssessments | Não | Não |
> | managedInstances / bases de dados / vulnerabilidadesAssessments / regras / linhas de base | Não | Não |
> | managedInstances / encryptionProtector | Não | Não |
> | managedInstances / chaves | Não | Não |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Não | Não |
> | managedInstances / vulnerabilidadesAssessments | Não | Não |
> | servidores | Sim | Sim |
> | servidores / administradores | Não | Não |
> | servidores / comunicaçõesLinks | Não | Não |
> | servidores / bases de dados | Sim (ver [nota abaixo)](#sqlnote) | Sim |
> | servidores / encriptaçãoProtector | Não | Não |
> | servidores / firewallRules | Não | Não |
> | servidores / chaves | Não | Não |
> | servidores / restauradoresDroppedDatabases | Não | Não |
> | servidores /objetivos de serviço | Não | Não |
> | servidores / tdeCertificados | Não | Não |
> | virtualClusters | Não | Não |

<a id="sqlnote"></a>

> [!NOTE]
> A base de dados Master não suporta tags, mas outras bases de dados, incluindo bases de dados Azure Synapse Analytics, tags de suporte. As bases de dados Azure Synapse Analytics devem estar no estado ativo (não pausado).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Sim | Sim |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Não | Não |
> | SqlVirtualMachines | Sim | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | contas eliminadas | Não | Não |
> | armazenamento Contas | Sim | Sim |
> | armazenamentoCotas / blobServices | Não | Não |
> | armazenamentoSCotas / serviços de ficheiros | Não | Não |
> | armazenamentoSCotas / filas Serviços | Não | Não |
> | armazenamentoCotas /serviços | Não | Não |
> | armazenamentoCotas / serviços / métricasDefinitions | Não | Não |
> | armazenamentoCotas / tableServices | Não | Não |
> | usos | Não | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | caches | Sim | Sim |
> | caches / armazenamentoSTogets | Não | Não |
> | modelos de uso | Não | Não |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | grupos de replicação | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | storageSyncServices / Registros registados | Não | Não |
> | storageSyncServices / syncGroups | Não | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | storageSyncServices / Registros registados | Não | Não |
> | storageSyncServices / syncGroups | Não | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | storageSyncServices / Registros registados | Não | Não |
> | storageSyncServices / syncGroups | Não | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | gestores | Sim | Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aglomerados | Sim | Sim |
> | streamingjobs | Sim (ver nota abaixo) | Sim |

> [!NOTE]
> Não se pode adicionar uma etiqueta quando os trabalhos de streaming estão a funcionar. Pare o recurso para adicionar uma etiqueta.

## <a name="microsoftsubscription"></a>Microsoft.Subscrição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | aceitarChangeTenant | Não | Não |
> | pseudónimos | Não | Não |
> | cancelar | Não | Não |
> | changeTenantRequest | Não | Não |
> | changeTenantStatus | Não | Não |
> | Criar Subscrição | Não | Não |
> | ativar | Não | Não |
> | mudar o nome | Não | Não |
> | AssinaturaDefinitions | Não | Não |
> | Subscrições Operações | Não | Não |
> | assinaturas | Não | Não |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | Sim | Sim |
> | áreas de trabalho | Sim | Sim |
> | espaços de trabalho / bigDataPools | Sim | Sim |
> | espaços de trabalho / operaçõesEstas | Não | Não |
> | espaços de trabalho / sqlDatabases | Sim | Sim |
> | espaços de trabalho / sqlPools | Sim | Sim |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | ambientes | Sim | Não |
> | ambientes / acessosPolícias | Não | Não |
> | ambientes / fontes de eventos | Sim | Não |
> | ambientes / referênciaSSets | Sim | Não |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | lojas | Sim | Sim |
> | lojas / acessosPolícias | Não | Não |
> | lojas / serviços | Não | Não |
> | lojas / serviços / tokens | Não | Não |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | imagenSTas | Sim | Sim |
> | imagenTemplates / runOutputs | Não | Não |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | ArcZones | Sim | Sim |
> | RecursosPools | Sim | Sim |
> | VCenters | Sim | Sim |
> | VirtualMachines | Sim | Sim |
> | VirtualMachineTemplates | Sim | Sim |
> | VirtualNetworks | Sim | Sim |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | DedicadoCloudNodes | Sim | Sim |
> | ServiçosCloud dedicados | Sim | Sim |
> | virtualMachines | Sim | Sim |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | dispositivos | Sim | Sim |
> | registoS de subscrições | Não | Não |
> | fornecedores | Não | Não |
> | fornecedores / skus | Não | Não |
> | fornecedores / vnfs | Não | Não |
> | virtualNetworkFunctionSkus | Não | Não |
> | vnfs | Sim | Sim |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | planos | Sim | Sim |
> | registoS de subscrições | Não | Não |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | apiManagementAcounts | Não | Não |
> | apiManagementAcounts / apiAcls | Não | Não |
> | apiManagementAcounts / apis | Não | Não |
> | apiManagementAcounts / apis / apiAcls | Não | Não |
> | apiManagementAcounts / apis / connectionAcls | Não | Não |
> | apiManagementAcounts / apis / conexões | Não | Não |
> | apiManagementAcounts / apis / conexões / conexõesAcls | Não | Não |
> | apiManagementAccounts / apis / LocalizedDefinitions | Não | Não |
> | apiManagementAcounts / connectionAcls | Não | Não |
> | apiManagementAcounts / conexões | Não | Não |
> | billingMeters | Não | Não |
> | certificados | Sim | Sim |
> | connectionGateways | Sim | Sim |
> | conexões | Sim | Sim |
> | customApis | Sim | Sim |
> | deletedSites | Não | Não |
> | hospedagem Ambientes | Sim | Sim |
> | hospedarEnvironments / eventGridFilters | Não | Não |
> | hospedamentoEnvironments / multiRolePools | Não | Não |
> | hospedamentoEnvironments / workerPools | Não | Não |
> | kubeEnvironments | Sim | Sim |
> | editoresUsers | Não | Não |
> | recomendações | Não | Não |
> | recursoHealthMetadata | Não | Não |
> | tempos de execução | Não | Não |
> | serverFarms | Sim | Sim |
> | serverFarms / eventGridFilters | Não | Não |
> | sites | Sim | Sim |
> | sites / config  | Não | Não |
> | sites / eventGridFilters | Não | Não |
> | sites / hostNameBindings | Não | Não |
> | sites / redeConfig | Não | Não |
> | sites / premieraddons | Sim | Sim |
> | sites / slots | Sim | Sim |
> | sites / slots / eventGridFilters | Não | Não |
> | sites / slots / hostNameBindings | Não | Não |
> | sites / slots / redeConfig | Não | Não |
> | fonteControls | Não | Não |
> | staticSites | Sim | Sim |
> | validar | Não | Não |
> | verificarHostingEnvironmentVnet | Não | Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | diagnósticoS | Não | Não |
> | diagnósticoSettingsCategories | Não | Não |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | Sim | Sim |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | Serviços de Dispositivos | Sim | Sim |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | cargas de trabalho | Sim | Sim |
> | cargas de trabalho / instâncias | Não | Não |
> | cargas de trabalho / versões | Não | Não |
> | cargas de trabalho / versões / artefactos | Não | Não |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporta tags | Etiqueta no relatório de custos |
> | ------------- | ----------- | ----------- |
> | componentes | Não | Não |
> | componentesSummary | Não | Não |
> | monitorInstâncias | Não | Não |
> | monitorInstancesSummary | Não | Não |
> | monitores | Não | Não |
> | notificaçõesS | Não | Não |

## <a name="next-steps"></a>Próximos passos

Para aprender a aplicar tags aos recursos, consulte [use tags para organizar os seus recursos Azure.](tag-resources.md)
