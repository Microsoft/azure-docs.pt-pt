---
title: Eliminação completa de modo
description: Mostra como os tipos de recursos lidam com a eliminação completa do modo nos modelos do Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 10/06/2020
ms.openlocfilehash: 72303a7916aec39c05f9b4fa2cbc77de18b7fb3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766722"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Supressão dos recursos da Azure para implementações completas do modo

Este artigo descreve como os tipos de recursos lidam com a eliminação quando não estão num modelo que é implantado em modo completo.

Os tipos de recursos marcados com **Sim** são eliminados quando o tipo não está no modelo implantado com o modo completo.

Os tipos de recursos marcados com **No** não são automaticamente eliminados quando não estão no modelo; no entanto, são eliminados se o recurso dos pais for eliminado. Para obter uma descrição completa do comportamento, consulte os modos de [implementação do Gestor de Recursos Azure](deployment-modes.md).

Se implementar para [mais de um grupo de recursos num modelo,](cross-scope-deployment.md)os recursos do grupo de recursos especificados na operação de implantação podem ser eliminados. Os recursos nos grupos de recursos secundários não são eliminados.

Os recursos são listados pelo espaço de nome do fornecedor de recursos. Para combinar um espaço de nome do fornecedor de recursos com o seu nome de serviço Azure, consulte [os fornecedores de recursos para serviços Azure](../management/azure-services-resource-providers.md).

> [!NOTE]
> Utilize sempre o [funcionamento do "e se"](template-deploy-what-if.md) antes de colocar um modelo em modo completo. E se lhe mostrar quais os recursos que serão criados, eliminados ou modificados. Use o que se para evitar apagar involuntariamente recursos.

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
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Serviços de Domínio | Sim |
> | Serviços de Domínio / oucontainer | Não |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | apoiarProviders | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aadsupportcases | Não |
> | adds serviços | Não |
> | agentes | Não |
> | anónimos | Não |
> | configuração | Não |
> | registos | Não |
> | relatórios | Não |
> | serviços de saúde | Não |
> | services | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | advisorScore | Não |
> | configurações | Não |
> | gerarRecommendações | Não |
> | do IdP | Não |
> | recomendações | Não |
> | supressões | Não |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | farmBeats | Sim |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | actionRules | Sim |
> | alerts | Não |
> | alertasList | Não |
> | alertasMetaData | Não |
> | alertasSummary | Não |
> | alertaSummaryList | Não |
> | smartDetectorAlertRules | Sim |
> | smartGroups | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | servidores | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | reportFeedback | Não |
> | serviço | Sim |
> | validar Nome de Serviço | Não |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | configuraçõesStores | Sim |
> | configuraçõesStores / eventGridFilters | Não |
> | configuraçõesStores / keyValues | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Spring | Sim |
> | Primavera / aplicativos | Não |
> | Primavera / apps / implementações | Não |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | attestationProviders | Sim |
> | predefinidosProviders | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | acessoReviewScheduleDefinitions | Não |
> | acessoReviewScheduleSettings | Não |
> | clássicos Administradores | Não |
> | dataAliases | Não |
> | negar Assignments | Não |
> | elevateAccess | Não |
> | findOrphanRoleAssignments | Não |
> | fechaduras | Não |
> | permissões | Não |
> | políticasAssinsagens | Não |
> | políticasDefinições | Não |
> | políticasExemptions | Não |
> | políticasSetDefinitions | Não |
> | privateLinkAssociations | Não |
> | fornecedorOperações | Não |
> | recursosManagementPrivateLinks | Sim |
> | papéAs de assinaturas | Não |
> | papelAssignmentsUsageMetrics | Não |
> | funçõesDefinitions | Não |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | configuraçãoProfileAssignments | Não |
> | configuraçãoProfilePreferences | Sim |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | automação Contas | Sim |
> | automação Contas/ configurações | Sim |
> | automação Contas / empregos | Não |
> | automaçãoAcontas / privateEndpointConnectionProxies | Não |
> | automaçãoAs contas / privateEndpointConnections | Não |
> | automação Contas / privateLinkResources | Não |
> | automação Contas / runbooks | Sim |
> | automaçãoCotas / softwareUpdateConfigurations | Não |
> | automaçãoAcontas / webhooks | Não |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | privateClouds | Sim |
> | privateClouds / autorizações | Não |
> | privateClouds / clusters | Não |
> | privateClouds / globalReachConnections | Não |
> | privateClouds / hcxEnterpriseSites | Não |
> | privateClouds / workloadNetworks | Não |
> | privateClouds / workloadNetworks / dhcpConfigurations | Não |
> | privateClouds / workloadNetworks / gateways | Não |
> | privateClouds / workloadNetworks / portMirroringProfiles | Não |
> | privateClouds / workloadNetworks / segmentos | Não |
> | privateClouds / workloadNetworks / virtualMachines | Não |
> | privateClouds / workloadNetworks / vmGroups | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | ambientes | Não |
> | ambientes / contas | Não |
> | ambientes / contas / espaços de nome | Não |
> | ambientes / contas / espaços de nome / configurações | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | b2cDirectes | Sim |
> | b2ctenants | Não |
> | guestUsages | Sim |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dataControllers | Sim |
> | pós-gresInstances | Sim |
> | sqlManagedInstances | Sim |
> | sqlServerInstances | Sim |
> | sqlServerRegistrations | Sim |
> | sqlServerRegistrations / SqlServers | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | cloudManifestFiles | Não |
> | edgeSubscriptions | Sim |
> | linkedSubscriptions | Sim |
> | registos | Sim |
> | registos / clienteSubscrições | Não |
> | registos / produtos | Não |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Sim |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | lotAcons | Sim |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | faturaçãoAconse de contas | Não |
> | faturaçãoAconssolhos/acordos | Não |
> | faturaçãoAs contas / faturaçõesPermissions | Não |
> | faturaçãoAcounts / billingProfiles | Não |
> | faturação Encomendas / faturaçãoProfiles / faturaçãoPermissions | Não |
> | faturação Encomendas / faturaçãoProfiles / billingRoleAssignments | Não |
> | billingAcounts / faturaçãoProfiles / billingRoleDefinitions | Não |
> | faturação Encomendas / faturaçãoProfiles / faturaçãoSubscrições | Não |
> | billingAcounts / faturaçãoProfiles / createBillingRoleAssignment | Não |
> | faturaçãoAstros /faturaçãoProfiles / clientes | Não |
> | faturaçãoAstros /faturaçãoProfiles / instruções | Não |
> | faturação Encomendas / faturas | Não |
> | faturação Encomendas / faturas / faturas / folha de preços | Não |
> | faturação Encomendas / faturas / faturas / transações | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções | Não |
> | faturaçãoAstros /faturaçãoProfiles / faturaSecções / faturasPermissions | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / billingRoleAssignments | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / billingRoleDefinitions | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / faturaSubscrições | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / createBillingRoleAssignment | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / iniciarTransfer | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / produtos | Não |
> | faturação Encomendas / faturaçãoProfiles / faturaSecções / produtos / transferência | Não |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / produtos / updateAutoRenew | Não |
> | faturação Encomendas / faturaçãoProfiles / faturaSecções / transações | Não |
> | faturação Encomendas / faturaçãoProfiles / faturaSecções / transferências | Não |
> | faturação Encomendas / BillingProfiles / patchOperações | Não |
> | faturação Encomendas / faturaçãoProfiles / pagamentosMes | Não |
> | faturaçãoAcounts / faturaçãoProfiles / políticas | Não |
> | faturaçãoAs contas / faturaçãoProfiles / folha de preços | Não |
> | faturação Encomendas / faturaçãoProfiles / pricesheetDownloadOperations | Não |
> | faturaçãoAstros /faturaçãoProfiles / produtos | Não |
> | faturaçãoAstros /faturaçãoProfiles / reservas | Não |
> | faturação Encomendas / faturaçãoProfiles / transações | Não |
> | faturaçãoAcounts / faturaçãoProfiles / validarDetachPaymentMethodEligibility | Não |
> | billingAcounts / billingRoleAssignments | Não |
> | billingAccounts / billingRoleDefinitions | Não |
> | faturaçãoAcounts / faturaçãoSubscrições | Não |
> | faturaçãoAs contas / faturas/ faturas | Não |
> | billingAcounts / createBillingRoleAssignment | Não |
> | billingAcounts / criarInvoiceSectionsOperations | Não |
> | faturaçãoAsconse de clientes | Não |
> | faturaçãoAs contas / clientes / faturaçõesPermissions | Não |
> | faturaçãoAs contas / clientes / faturaçãoSubscrições | Não |
> | faturaçãoAconssocamos / clientes / iniciarTransfer | Não |
> | faturaçãoAconse de clientes / políticas | Não |
> | faturaçãoAconse de clientes / produtos | Não |
> | faturaçãoAs contas / clientes / transações | Não |
> | faturaçãoAs contas / clientes / transferes | Não |
> | faturaçãoAconsse de departamentos | Não |
> | billingAcounts / departamentos / faturaçõesPermissions | Não |
> | billingAcounts / departamentos / billingRoleAssignments | Não |
> | billingAcounts / departamentos / billingRoleDefinitions | Não |
> | faturaçãoAconse de matrículasAconseções | Não |
> | faturaçãoAs contas / matrículasContas / faturações | Não |
> | faturaçãoAcounts / matrículasContas / billingRoleAssignments | Não |
> | billingAcounts / matrículasContas / billingRoleDefinitions | Não |
> | faturação | Não |
> | faturas / faturas / transações | Não |
> | faturaçãoS / faturaSecções | Não |
> | faturaçãoS / faturaSecções / faturaÇãoSubscriçãoMoveOperações | Não |
> | faturaçãoS / faturaSecções / faturaSubscrições | Não |
> | faturaçãoS / faturaSecções / faturaSubscrições / transferência | Não |
> | faturaçãoS / faturaSecções / elevação | Não |
> | faturaÇãoS / faturaSecções / iniciarTransfer | Não |
> | faturações / faturaSecções / patchOperações | Não |
> | faturações / faturaSecções / produtoMoveOperações | Não |
> | faturaÇãoS / faturaSecções / produtos | Não |
> | faturaÇões / faturaSecções / produtos / transferência | Não |
> | faturaÇões / faturaSecções / produtos / updateAutoRenew | Não |
> | faturaçãoS / faturaSecções / transações | Não |
> | faturaÇões / faturaSecções / transferências | Não |
> | faturaçãoAs contas / linhaOfCredit | Não |
> | faturaçãoOperações / PatchOperações | Não |
> | faturação Contas / pagamentosMes | Não |
> | faturaçãoAconsecos / produtos | Não |
> | faturaçãoAsconse de reservas | Não |
> | faturaçãoAs/transações | Não |
> | faturaçãoPeriods | Não |
> | faturaçãoPermissions | Não |
> | faturaçãoProperty | Não |
> | billingRoleAssignments | Não |
> | billingRoleDefinitions | Não |
> | createBillingRoleAssignment | Não |
> | departamentos | Não |
> | matrículasCotas | Não |
> | faturas | Não |
> | transferências | Não |
> | transferências / aceitarTransfer | Não |
> | transferências / declínioTransfer | Não |
> | transferências / operaçãoStatus | Não |
> | transferências / validarTransfer | Não |
> | validarAddress | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | mapApis | Sim |
> | actualizaçãoCommunicationPreferência | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | blockchainMembers | Sim |
> | cordaMembers | Sim |
> | observadores | Sim |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | TokenServices | Sim |
> | TokenServices / BlockchainNetworks | Não |
> | TokenServices / Grupos | Não |
> | TokenServices / Grupos / Contas | Não |
> | TokenServices / TokenTemplates | Não |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | blueprintAsignments | Não |
> | blueprintAssignments / atribuiçãoOperações | Não |
> | blueprintAssignments /operações | Não |
> | plantas | Não |
> | plantas / artefactos | Não |
> | plantas / versões | Não |
> | plantas / versões / artefactos | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | botServs | Sim |
> | botServices / canais | Não |
> | botServices / conexões | Não |
> | linguagens | Não |
> | modelos | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Redis | Sim |
> | Redis / EventGridFilters | Não |
> | Redis / privateEndpointConnectionProxies | Não |
> | Redis / privateEndpointConnectionProxies / validar | Não |
> | Redis / privateEndpointConnections | Não |
> | Redis / privateLinkResources | Não |
> | redisEnterprise | Sim |
> | RedisEnterprise / privateEndpointConnectionProxies | Não |
> | RedisEnterprise / privateEndpointConnectionProxies / validar | Não |
> | RedisEnterprise / privateEndpointConnections | Não |
> | RedisEnterprise / privateLinkResources | Não |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicaçõesReservações | Não |
> | autoQuotaIncrease | Não |
> | calcularExchange | Não |
> | calcularPrimá-lo | Não |
> | calcular Prémio Depurchase | Não |
> | catálogos | Não |
> | comerciaisReservationOrders | Não |
> | troca | Não |
> | própriasreservações | Não |
> | placePurchaseOrder | Não |
> | pedidos de reservas | Não |
> | pedidos de reservas / calcularRefund | Não |
> | reservasOrdeiros / fusão | Não |
> | reservasOrdeiros / Reservas | Não |
> | reservasOrdeiros / reservas / revisões | Não |
> | pedidos de reservas / devolução | Não |
> | pedidos de reservas / divisão | Não |
> | pedidos de reservas / swap | Não |
> | reservas | Não |
> | recursosProviders | Não |
> | resources | Não |
> | validar ReservationOrder | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Não |
> | CdnWebApplicationFirewallPolicies | Sim |
> | edgenodes | Não |
> | perfis | Sim |
> | perfis / pontos finais | Sim |
> | perfis / pontos finais / personaldomains | Não |
> | perfis / pontos finais / grupos de origem | Não |
> | perfis / pontos finais / origens | Não |
> | validarProbe | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | certificadoRes | Sim |
> | certificadoRes/certificados | Não |
> | validar Informação DeRegistrationRegistrationCertificate | Não |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | perfil | Não |
> | recursosChanges | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | capacidades | Não |
> | domínioNames | Sim |
> | domínioS de identidades / capacidades | Não |
> | domainNames / InternaLoadBalancers | Não |
> | domínioNamis / serviçoScertificados | Não |
> | domínioNames / slots | Não |
> | domínioNames / slots / funções | Não |
> | domínioNames / slots / funções / métricasDefinitions | Não |
> | domínioNames / slots / funções / métricas | Não |
> | moveSubscriptionResources | Não |
> | operatingSystemFamilies | Não |
> | sistemas operativos | Não |
> | quotas | Não |
> | recursosTipos | Não |
> | validarSubscriptionMoveAvailability | Não |
> | virtualMachines | Sim |
> | virtualMachines / diagnósticoSettings | Não |
> | virtualMachines / métricasDefinitions | Não |
> | virtualMachines / métricas | Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | classicInfrastructureResources | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | capacidades | Não |
> | expressRouteCrossConnections | Não |
> | expressRouteCrossConnections / peerings | Não |
> | gatewaySupportouDevices | Não |
> | networkSecurityGroups | Sim |
> | quotas | Não |
> | reservedIps | Sim |
> | virtualNetworks | Sim |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Não |
> | virtualNetworks / virtualNetworkPeerings | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | capacidades | Não |
> | discos | Não |
> | imagens | Não |
> | osImages | Não |
> | osPlatformImages | Não |
> | publicImages | Não |
> | quotas | Não |
> | armazenamento Contas | Sim |
> | armazenamentoCotas / blobServices | Não |
> | armazenamentoSCotas / serviços de ficheiros | Não |
> | armazenamentoCotas / métricasDefinitions | Não |
> | armazenamentoCotas /métricas | Não |
> | armazenamentoSCotas / filas Serviços | Não |
> | armazenamentoCotas /serviços | Não |
> | armazenamentoS / serviços / diagnósticoSettings | Não |
> | armazenamentoCotas / serviços / métricasDefinitions | Não |
> | armazenamentoCotas / serviços / métricas | Não |
> | armazenamentoCotas / tableServices | Não |
> | armazenamentoCotas / vmImages | Não |
> | vmImages | Não |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | planos | Sim |
> | registoS de subscrições | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas / privateEndpointConnectionProxies | Não |
> | contas / privateEndpointConnections | Não |
> | contas / privateLinkResources | Não |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | RateCard | Não |
> | UsageAggregates | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | disponibilidadeSets | Sim |
> | cloudServs | Sim |
> | cloudServices / networkInterfaces | Não |
> | cloudServices / publicIPAddresses | Não |
> | cloudServs / roleInstances | Não |
> | cloudServices / roleInstances / networkInterfaces | Não |
> | cloudServs / funções | Não |
> | diskAccesses | Sim |
> | DiskEncryptionSets | Sim |
> | discos | Sim |
> | galerias | Sim |
> | galerias / aplicações | Não |
> | galerias / aplicações / versões | Não |
> | galerias / imagens | Não |
> | galerias / imagens / versões | Não |
> | grupos de anfitriões | Sim |
> | hostGroups / anfitriões | Sim |
> | imagens | Sim |
> | grupos de posições de proximidade | Sim |
> | restaurarPointCollections | Sim |
> | restaurarPointCollections / restaurarPonts | Não |
> | sharedVMExtensions | Sim |
> | sharedVMExtensions / versões | Não |
> | sharedVMImages | Sim |
> | sharedVMImages / versões | Não |
> | instantâneos | Sim |
> | sshPublicKeys | Sim |
> | virtualMachines | Sim |
> | virtualMachines / extensões | Sim |
> | virtualMachines / métricasDefinitions | Não |
> | virtualMachines / runCommands | Sim |
> | virtualMachineScaleSets | Sim |
> | virtualMachineScaleSets / extensões | Não |
> | virtualMachineScaleSets / networkInterfaces | Não |
> | virtualMachineScaleSets / publicIPAddresses | Não |
> | virtualMachineScaleSets / virtualMachines | Não |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Não |

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | CacheNodes | Sim |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | AgregadoCost | Não |
> | Saldos | Não |
> | Orçamentos | Não |
> | Cobranças | Não |
> | CostTags | Não |
> | créditos | Não |
> | eventos | Não |
> | Previsões | Não |
> | lotes | Não |
> | Mercados | Não |
> | Folhas de preços | Não |
> | produtos | Não |
> | ReservasDetails | Não |
> | ReservasRecommendationDetails | Não |
> | Recommendações de Reservas | Não |
> | ReservasSumários | Não |
> | Transferências de Reservas | Não |
> | Etiquetas | Não |
> | inquilinos | Não |
> | Termos | Não |
> | UsageDetails | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | grupos de contentores | Sim |
> | serviceAssociationLinks | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | registos | Sim |
> | registos / agentesPools | Sim |
> | registos / construções | Não |
> | registos / construções / cancelar | Não |
> | registos / construções / getLogLink | Não |
> | registos / buildTasks | Sim |
> | registos / buildTasks / passos | Não |
> | registos / eventGridFilters | Não |
> | registos / exportaçõesPipelines | Não |
> | registos / gerarCredentis | Não |
> | registos / getBuildSourceUploadUrl | Não |
> | registos / GetCredentials | Não |
> | registos / importImage | Não |
> | registos / importapipelines | Não |
> | registos / pipelineRuns | Não |
> | registos / privateEndpointConnectionProxies | Não |
> | registos / privateEndpointConnectionProxies / validar | Não |
> | registos / privateEndpointConnections | Não |
> | registos / privateLinkResources | Não |
> | registos / filasBuild | Não |
> | registos / regeneraçãoCredential | Não |
> | registos / regeneraçãoCredentais | Não |
> | registos / replicações | Sim |
> | registos / corre | Não |
> | registos / corre / cancela | Não |
> | registos / horáriosRun | Não |
> | registos / scopeMaps | Não |
> | registos / taskRuns | Não |
> | registos / tarefas | Sim |
> | registos / fichas | Não |
> | registos / actualizaçãoPolícias | Não |
> | registos / webhooks | Sim |
> | registros / webhooks / getCallbackConfig | Não |
> | registos / webhooks / ping | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | serviços de contentores | Sim |
> | geridoClusters | Sim |
> | openShiftManagedClusters | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Alertas | Não |
> | Faturação | Não |
> | Orçamentos | Não |
> | CloudConnectors | Não |
> | Conectores | Sim |
> | costAllocationRules | Não |
> | Departamentos | Não |
> | Dimensões | Não |
> | MatrículasAconselhos | Não |
> | Exportações | Não |
> | Contas externas deBilling | Não |
> | ExternalBillingAccounts / Alertas | Não |
> | ExternalBillingAccounts / Dimensões | Não |
> | Contas externas deBilling / Previsão | Não |
> | ExternalBillingAccounts / Consulta | Não |
> | Subscrições externas | Não |
> | Subscrições externas / Alertas | Não |
> | ExternalSubscriptions / Dimensões | Não |
> | ExternalSubscriptions / Previsão | Não |
> | Subscrições externas / Consulta | Não |
> | Previsão | Não |
> | Informações | Não |
> | Consulta | Não |
> | registar | Não |
> | Configs relatório | Não |
> | Relatórios | Não |
> | Definições | Não |
> | showbackRules | Não |
> | Vistas | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | pedidos | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | associações | Não |
> | recursosProviders | Sim |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | casos | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | empregos | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | áreas de trabalho | Sim |
> | espaços de trabalho / dbWorkspaces | Não |
> | espaços de trabalho / armazenamentoEncryption | Não |
> | espaços de trabalho / virtualNetworkPeerings | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | catálogos | Sim |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dataFactories | Sim |
> | dataFactories / diagnosticSettings | Não |
> | dataFactories / métricasDefinitions | Não |
> | dataFactorySchema | Não |
> | fábricas | Sim |
> | fábricas / integraçãoRuntimes | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas / dadosLakeStoreAccounts | Não |
> | contas / armazenamentoCotações | Não |
> | contas / armazenamentoCotas / contentores | Não |
> | contas / transferAnlyticsUnits | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas / eventGridFilters | Não |
> | contas / firewallRules | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | services | Sim |
> | serviços / projetos | Sim |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | BackupVaults | Sim |
> | Fontes de recursos | Sim |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas / ações | Não |
> | contas / ações / conjuntos de dados | Não |
> | contas / ações / convites | Não |
> | contas / ações / subscrições de partilha de fornecedores | Não |
> | contas / ações / sincronizaçõesS | Não |
> | contas / subscrições de ações | Não |
> | contas / subscrições de ações / consumerSourceDataSets | Não |
> | contas / subscrições de ações / datasetmappings | Não |
> | contas / subscrições de ações / gatilhos | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | servidores | Sim |
> | servidores / assessores | Não |
> | servidores / chaves | Não |
> | servidores / privadosEndpointConnectionProxies | Não |
> | servidores / privateEndpointConnections | Não |
> | servidores / privateLinkResources | Não |
> | servidores / consultasTextos | Não |
> | servidores / recuperáveisServers | Não |
> | servidores / iniciar | Não |
> | servidores / parar | Não |
> | servidores / topQueryStatistics | Não |
> | servidores / virtualNetworkRules | Não |
> | servidores / esperaStatistics | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | flexibleServers | Sim |
> | servidores | Sim |
> | servidores / assessores | Não |
> | servidores / chaves | Não |
> | servidores / privadosEndpointConnectionProxies | Não |
> | servidores / privateEndpointConnections | Não |
> | servidores / privateLinkResources | Não |
> | servidores / consultasTextos | Não |
> | servidores / recuperáveisServers | Não |
> | servidores / iniciar | Não |
> | servidores / parar | Não |
> | servidores / topQueryStatistics | Não |
> | servidores / upgrade | Não |
> | servidores / virtualNetworkRules | Não |
> | servidores / esperaStatistics | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DbforPostgresql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | flexibleServers | Sim |
> | grupos de servidores | Sim |
> | servidores | Sim |
> | servidores / assessores | Não |
> | servidores / chaves | Não |
> | servidores / privadosEndpointConnectionProxies | Não |
> | servidores / privateEndpointConnections | Não |
> | servidores / privateLinkResources | Não |
> | servidores / consultasTextos | Não |
> | servidores / recuperáveisServers | Não |
> | servidores / topQueryStatistics | Não |
> | servidores / virtualNetworkRules | Não |
> | servidores / esperaStatistics | Não |
> | servidorv2 | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | artefactosSources | Sim |
> | rollouts | Sim |
> | serviceTopologias | Sim |
> | serviçoTopologias / serviços | Sim |
> | serviceTopologias / serviços / serviços Unidades | Sim |
> | passos | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | grupos de aplicações | Sim |
> | grupos de aplicações / aplicações | Não |
> | grupos de aplicações / desktops | Não |
> | grupos de aplicações / startmenuitems | Não |
> | hostpools | Sim |
> | hostpools / sessão | Não |
> | hostpools / sessionhosts / utilizadores | Não |
> | hostpools / utilizadores | Não |
> | áreas de trabalho | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Elásticos | Sim |
> | Elásticos / IotHubTenants | Sim |
> | Elásticos / IotHubTenants / securitySettings | Não |
> | IotHubs | Sim |
> | IotHubs / eventGridFilters | Não |
> | IotHubs / securitySettings | Não |
> | Serviços de Provisionamento | Sim |
> | usos | Não |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas / instâncias | Sim |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | oleodutos | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | controladores | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | centros de laboratório | Sim |
> | laboratórios | Sim |
> | laboratórios / ambientes | Sim |
> | laboratórios / serviceRunners | Sim |
> | laboratórios / virtualMachines | Sim |
> | horários | Sim |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | digitalTwinsInstances | Sim |
> | digitalTwinsInstances / endpoints | Não |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | base de dadosSes | Não |
> | bases de dadosAconses | Sim |
> | RestauraçãoDatabaseAccounts | Não |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | domínios | Sim |
> | domínios / domainOwnershipIdentifiers | Não |
> | gerarSsoRequest | Não |
> | topLevelDomains | Não |
> | validar aDomainRegistrationInformation | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | LCSprojects | Não |
> | LCSprojects / deplosagens de nuvens | Não |
> | LCSprojects / conectores | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | services | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | domínios | Sim |
> | domínios / tópicos | Não |
> | eventoSubscrições | Não |
> | extensãoTopics | Não |
> | parceiroNamespaces | Sim |
> | partnerNamespaces / eventChannels | Não |
> | partnerRegistrations | Sim |
> | partnerTopics | Sim |
> | partnerTopics / eventoSubscrições | Não |
> | systemTopics | Sim |
> | systemTopics / eventSubscrições | Não |
> | tópicos | Sim |
> | tópicotips | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Sim |
> | espaços de nome | Sim |
> | espaços de nomes / regras de autorização | Não |
> | espaços de nome / disasterrecoveryconfigs | Não |
> | espaços de nomes / eventhubs | Não |
> | espaços de nome / eventhubs / autorizações | Não |
> | espaços de nomes / eventhubs / grupos de consumidores | Não |
> | espaços de nome / redes de regras | Não |
> | espaços de nome / privateEndpointConnections | Não |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | experimentOs espaços de trabalho | Sim |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | espaços de nome | Sim |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | recursosProviders | Não |
> | funcionalidades | Não |
> | fornecedores | Não |
> | subscriçõesFesturaRegistrações | Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | inscrever | Não |
> | galleryitems | Não |
> | generateartifactacssuri | Não |
> | myareas | Não |
> | myareas / áreas | Não |
> | myareas / áreas / áreas | Não |
> | myareas / áreas / áreas / galleryitems | Não |
> | myareas / áreas / galleryitems | Não |
> | myareas / galleryitems | Não |
> | registar | Não |
> | resources | Não |
> | retrieveresourcesbyid | Não |

## <a name="microsoftgenomics"></a>Microsoft.Geómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | autoManagedAccounts | Sim |
> | autoManagedVmConfigurationProfiles | Sim |
> | configuraçãoProfileAssignments | Não |
> | guestConfigurationAssignments | Não |
> | software | Não |
> | softwareUpdateProfile | Não |
> | softwareUpdates | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | hanaInstances | Sim |
> | sapMonitores | Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | DEDICADOHSMs | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Sim |
> | clusters / aplicações | Não |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | services | Sim |
> | serviços / iomtconnectors | Não |
> | serviços / iomtconnectors / conexões | Não |
> | serviços / iomtconnectors / mapeamentos | Não |
> | serviços / privateEndpointConnectionProxies | Não |
> | serviços / privateEndpointConnections | Não |
> | serviços / privateLinkResources | Não |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | máquinas | Sim |
> | máquinas / avaliaçãoPatches | Não |
> | máquinas / extensões | Sim |
> | máquinas / instalaçãoPatches | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dataSmanagers | Sim |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dispositivos | Sim |
> | registoS de subscrições | Não |
> | fornecedores | Não |
> | fornecedores / fornecedoreskus | Não |
> | fornecedores / vendorskus / pré-visualizaçãoSubscrições | Não |
> | funções de rede virtual | Sim |
> | virtualnetworkfunctionvendores | Não |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | componentes | Sim |
> | networkScopes | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | empregos | Sim |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnósticoS | Não |
> | diagnósticoSettingsCategories | Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | appTemplates | Não |
> | IoTApps | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Graph | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | deletedVaults | Não |
> | hsmPools | Sim |
> | managedHSMs | Sim |
> | cofres | Sim |
> | cofres / acessosPolícias | Não |
> | cofres / eventoSGridFiltros | Não |
> | cofres / chaves | Não |
> | cofres / chaves / versões | Não |
> | cofres / segredos | Não |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | clusters conectados | Sim |
> | registoS de subscrições | Não |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | extensões | Não |
> | fonteControlConfigurations | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Sim |
> | clusters / confinações de base de dados | Não |
> | clusters / bases de dados | Não |
> | clusters / bases de dados / ligações de dados | Não |
> | clusters / bases de dados / ligações eventhub | Não |
> | clusters / bases de dados / principais assinaturas | Não |
> | clusters / ligações de dados | Não |
> | aglomerados / principais destacamentos | Não |
> | clusters / semelhanças partilhadas | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | contas de laboratório | Sim |
> | utilizadores | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | hospedagem Ambientes | Sim |
> | integraçãoAconselhos | Sim |
> | integração ServiçoEnvironments | Sim |
> | integraçãoServiceEnvironments / managedApis | Sim |
> | isoladosEnvironments | Sim |
> | fluxos de trabalho | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | planos de compromissos | Sim |
> | webServices | Sim |
> | Áreas de trabalho | Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | áreas de trabalho | Sim |
> | espaços de trabalho / computas | Não |
> | espaços de trabalho / eventosGridFiltros | Não |
> | espaços de trabalho / linkedServices | Não |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicardados | Não |
> | configuraçõesAssignments | Não |
> | configuções de manutenção | Sim |
> | publicMaintenanceConfigurations | Não |
> | atualizações | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Identidades | Não |
> | nomeações para assediadas do utilizador | Sim |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | managedNetworks | Sim |
> | managedNetworks / managedNetworkGroups | Sim |
> | managedNetworks / managedNetworkPeeringPolicies | Sim |
> | notificação | Sim |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Não |
> | registosAssondos | Não |
> | registoDefinições | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | obterEntidades | Não |
> | grupos de gestão | Não |
> | managementGroups / configurações | Não |
> | resources | Não |
> | startTenantBackfill | Não |
> | inquilinoBackfillStatus | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas / eventGridFilters | Não |
> | contas / privateAtlases | Sim |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | macc | Não |
> | ofertas | Não |
> | oferta deTipos | Não |
> | oferta deTipos / editores | Não |
> | oferta DeTipos / editores / ofertas | Não |
> | oferta DeTipos / editores / ofertas / planos | Não |
> | oferta DeTipos / editores / ofertas / planos / acordos | Não |
> | ofertaTipos / editores / ofertas / planos / configs | Não |
> | ofertaTipos / editoras / ofertas / planos / configs / importImage | Não |
> | privadas | Não |
> | privateStoreClient | Não |
> | lojas privadas | Não |
> | privateStores / ofertas | Não |
> | produtos | Não |
> | editores | Não |
> | editores / ofertas | Não |
> | editores / ofertas / alterações | Não |
> | registar | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | classicDevServices | Sim |
> | actualizaçãoCommunicationPreferência | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | acordos | Não |
> | tipos de ofertas | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | mediaservices | Sim |
> | mediaservices / filtros de conta | Não |
> | mediaserviços / ativos | Não |
> | mediaservices / ativos / activosFiltros | Não |
> | mediaservices / conteúdoKeyPolicies | Não |
> | mediaservices / eventGridFilters | Não |
> | mediaservices / liveEventOperações | Não |
> | mediaservices / liveEvents | Sim |
> | mediaservices / liveEvents / liveOutputs | Não |
> | mediaservices / liveOutputOperations | Não |
> | mediaservices / mediaGraphs | Não |
> | mediaservices / privateEndpointConnectionOperations | Não |
> | mediaservices / privateEndpointConnectionProxies | Não |
> | mediaservices / privateEndpointConnections | Não |
> | mediaservices / streamingEndpointOperations | Não |
> | mediaservices / streamingEndpoints | Sim |
> | mediaservices / streamingLocators | Não |
> | mediaservices / streamingPolicies | Não |
> | mediaservices / transforma | Não |
> | mediaservices / transforma / empregos | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | appClusters | Sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | avaliaçãoProjectos | Sim |
> | migrarprojects | Sim |
> | moveCollections | Sim |
> | projetos | Sim |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | holográficosBroadcastAccounts | Sim |
> | objetoUnderstandingAccounts | Sim |
> | remotorcoscos | Sim |
> | spatialAnchorsAccounts | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | netAppAccounts | Sim |
> | netAppAccounts / accountBackups | Não |
> | netAppAccounts / capacityPools | Sim |
> | netAppAccounts / capacityPools / volumes | Sim |
> | netAppAccounts / capacityPools / volumes / snapshots | Não |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | applicationGateways | Sim |
> | aplicaçãoGatewayWebApplicationFirewallPolicies | Sim |
> | applicationSecurityGroups | Sim |
> | azureFirewallFqdnTags | Não |
> | azureFirewalls | Sim |
> | bastionHosts | Sim |
> | bgpServiceCommunidades | Não |
> | conexões | Sim |
> | ddosCustomPolicies | Sim |
> | ddosProtectionPlans | Sim |
> | dnsOperationStatuses | Não |
> | dnszones | Sim |
> | dnszones / A | Não |
> | dnszones / AAAA | Não |
> | dnszones / todos os | Não |
> | dnszones / CAA | Não |
> | dnszones / CNAME | Não |
> | dnszones / MX | Não |
> | dnszones / NS | Não |
> | dnszones / PTR | Não |
> | dnszones / recordets | Não |
> | dnszones / SOA | Não |
> | dnszones / SRV | Não |
> | dnszones / TXT | Não |
> | expressRouteCircuits | Sim |
> | expressRouteCrossConnections | Sim |
> | expressRouteGateways | Sim |
> | expressRoutePorts | Sim |
> | expressRouteServiceProviders | Não |
> | firewallPolicies | Sim |
> | frontdoors | Sim |
> | frontdoorWebApplicationFirewallManagedRuleSets | Não |
> | frontdoorWebApplicationFirewallPolicies | Sim |
> | obter Retecnretecencia | Não |
> | internaNotificar | Não |
> | ipGrupos | Sim |
> | loadBalancers | Sim |
> | localNetworkGateways | Sim |
> | natGateways | Sim |
> | networkIntentPolicies | Sim |
> | networkInterfaces | Sim |
> | redesProfiles | Sim |
> | networkSecurityGroups | Sim |
> | networkWatchers | Sim |
> | networkWatchers / connectionMonitors | Sim |
> | networkWatchers / flowLogs | Sim |
> | networkWatchers / lentes | Sim |
> | networkWatchers / pingMeshes | Sim |
> | p2sVpnGateways | Sim |
> | privateDnsOperationStatuses | Não |
> | privateDnsZones | Sim |
> | privateDnsZones / A | Não |
> | privateDnsZones / AAAA | Não |
> | privateDnsZones / todos | Não |
> | privateDnsZones / CNAME | Não |
> | privateDnsZones / MX | Não |
> | privateDnsZones / PTR | Não |
> | privateDnsZones / SOA | Não |
> | privateDnsZones / SRV | Não |
> | privateDnsZones / TXT | Não |
> | privateDnsZones / virtualNetworkLinks | Sim |
> | privadosEndpoints | Sim |
> | privateLinkServices | Sim |
> | publicIPAddresses | Sim |
> | publicIPPrefixos | Sim |
> | routeFilters | Sim |
> | routeTables | Sim |
> | serviceEndpointPolicies | Sim |
> | trafficManagerGeographicHierarchies | Não |
> | trafficmanagerprofiles | Sim |
> | trafficmanagerprofiles / heatMaps | Não |
> | trafficManagerUserMetricsKeys | Não |
> | virtualHubs | Sim |
> | virtualNetworkGateways | Sim |
> | virtualNetworks | Sim |
> | virtualNetworks / sub-redes | Não |
> | virtualNetworkTaps | Sim |
> | virtualWans | Sim |
> | VPNGateways | Sim |
> | VPNSites | Sim |
> | webApplicationFirewallPolicies | Sim |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | CadernoProxies | Não |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | espaços de nome | Sim |
> | espaços de nomes / notificaçõesHubs | Sim |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | osNamespaces | Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | HiperVSites | Sim |
> | ImportaçõesSites | Sim |
> | MestresSites | Sim |
> | ServidorEsites | Sim |
> | VMwareSites | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Sim |
> | eliminadosEspaços de trabalho | Não |
> | linkTargets | Não |
> | storageInsightConfigs | Não |
> | áreas de trabalho | Sim |
> | espaços de trabalho / dataExports | Não |
> | espaços de trabalho / dataSources | Não |
> | espaços de trabalho / linkedServices | Não |
> | workspaces / linkedStorageAccounts | Não |
> | espaços de trabalho / metadados | Não |
> | espaços de trabalho / consulta | Não |
> | espaços de trabalho / scopedPrivateLinkProxies | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | sociedades de gestão | Não |
> | configurações de gestão | Sim |
> | soluções | Sim |
> | Modos de exibição | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | legacyPeerings | Não |
> | peerAsns | Não |
> | espreiteiros | Sim |
> | peeringServiceCountries | Não |
> | peeringServiceProviders | Não |
> | serviços de peering | Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | atestados | Não |
> | policyEvents | Não |
> | policyMetadata | Não |
> | estados de política | Não |
> | policyTrackedResources | Não |
> | remediações | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Consolas do | Não |
> | dashboards | Sim |
> | userSettings | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Sim |
> | inquilinos | Sim |
> | inquilinos / espaços de trabalho | Não |
> | workspaceCollections | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | capacidades | Sim |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas eliminadas | Não |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | fornecedorRegistrations | Não |
> | fornecedorRegistrations /defaultRollouts | Não |
> | fornecedorRegistrations / recursosTypeRegistrations | Não |
> | rollouts | Sim |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Áreas de trabalho | Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | backupProtectedItems | Não |
> | cofres | Sim |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatopenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | OpenShiftClusters | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | espaços de nome | Sim |
> | espaços de nomes / regras de autorização | Não |
> | namespaces / ligações híbridas | Não |
> | espaços de nome / ligações híbridas / regras de autorização | Não |
> | espaços de nome / privateEndpointConnections | Não |
> | espaços de nomes / wcfrelays | Não |
> | espaços de nome / wcfrelays / autorizações | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | consultas | Sim |
> | recursosChangeDetails | Não |
> | recursosChanges | Não |
> | resources | Não |
> | recursosHistória | Não |
> | subscriçõesStatus | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | disponibilidade Estatísticas | Não |
> | criançaSetilabilityStatuses | Não |
> | criançaresources | Não |
> | emergentes | Não |
> | eventos | Não |
> | impactedResources | Não |
> | do IdP | Não |
> | notificações | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | calcularTemplateHash | Não |
> | implementações | Não |
> | implantações / operações | Não |
> | implementaçõesScripts | Sim |
> | implementaçõesScripts / registos | Não |
> | ligações | Não |
> | notificarResourceJobs | Não |
> | fornecedores | Não |
> | grupos de recursos | Não |
> | assinaturas | Não |
> | modeloSpecs | Sim |
> | modeloSpecs / versões | Sim |
> | inquilinos | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Sim |
> | saasresources | Não |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | nuvens | Sim |
> | VirtualMachines | Sim |
> | VirtualMachineTemplates | Sim |
> | VirtualNetworks | Sim |
> | vmmservers | Sim |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | recursoHealthMetadata | Não |
> | serviços de pesquisa | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Não |
> | advancedThreatProtectionSettings | Não |
> | alerts | Não |
> | alertaSuppressãoRules | Não |
> | permitidoConnections | Não |
> | aplicaçãoWhitelistings | Não |
> | avaliaçãoMetadata | Não |
> | avaliações | Não |
> | autoDismissAlertsRules | Não |
> | automações | Sim |
> | AutoProvisioningSettings | Não |
> | Cumprimentos | Não |
> | conectores | Não |
> | dataCollectionAgents | Não |
> | grupos de segurança de dispositivos | Não |
> | descobertasSecuritySolutions | Não |
> | sistemas de Segurança externos | Não |
> | InformaçãoProtetionPolicies | Não |
> | iotDefenderSettings | Não |
> | iotSecuritySolutions | Sim |
> | iotSecuritySolutions / analyticsModels | Não |
> | iotSecuritySolutions / analyticsModels / agregadosAlerts | Não |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Não |
> | iotSecuritySolutions / iotAlerts | Não |
> | iotSecuritySolutions / iotAlertTypes | Não |
> | iotSecuritySolutions / iotRecommendations | Não |
> | iotSecuritySolutions / iotRecommendationTypes | Não |
> | iotSensores | Não |
> | jitNetworkAccessPolicies | Não |
> | jitPolicies | Não |
> | políticas | Não |
> | preços | Não |
> | regulatórioComplianceStandards | Não |
> | regulatórioComplianceStandards /regulatórioComplianceControls | Não |
> | regulatórioComplianceStands / regulatórioComplianceControls / regulatórioComplianceAssess | Não |
> | secureScoreControlDefinitions | Não |
> | secureScoreControls | Não |
> | secureScores | Não |
> | secureScores / secureScoreControls | Não |
> | securityContacts | Não |
> | segurançaSoluções | Não |
> | securitySolutionsReferenceData | Não |
> | securityStatuses | Não |
> | securityStatusesSummaries | Não |
> | servidorVulnerabilityAssessments | Não |
> | definições | Não |
> | sqlVulnerabilityAssesments | Não |
> | subAssessments | Não |
> | tarefas | Não |
> | topologias | Não |
> | workspaceSettings | Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnósticoS | Não |
> | diagnósticoSettingsCategories | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | agregações | Não |
> | alertaRs | Não |
> | alertaRuleTemplates | Não |
> | regras automation | Não |
> | marcadores | Não |
> | casos | Não |
> | dataConnectors | Não |
> | dataConnectorsCheckRequirements | Não |
> | entidades | Não |
> | entidadeQueries | Não |
> | Incidentes | Não |
> | escritórioConsents | Não |
> | definições | Não |
> | ameaçaIntelligence | Não |
> | listas de observação | Não |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | consolas Serviços | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | espaços de nome | Sim |
> | espaços de nomes / regras de autorização | Não |
> | espaços de nome / disasterrecoveryconfigs | Não |
> | espaços de nomes / filtros de eventos | Não |
> | espaços de nome / redes de regras | Não |
> | espaços de nome / privateEndpointConnections | Não |
> | espaços de nomes / filas | Não |
> | espaços de nome / filas / autorizações | Não |
> | espaços de nomes / tópicos | Não |
> | espaços de nome / tópicos / autorizações | Não |
> | espaços de nomes / tópicos / subscrições | Não |
> | espaços de nomes / tópicos / subscrições / regras | Não |
> | premiumMessagingRegions | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Sim |
> | aglomerados | Sim |
> | clusters / aplicações | Não |
> | grupos de contentores | Sim |
> | containerGroupSets | Sim |
> | edgeclusters | Sim |
> | edgeclusters / aplicações | Não |
> | geremclusters | Sim |
> | gerenciouclusters / não desapoias | Não |
> | redes | Sim |
> | lojas secretas | Sim |
> | lojas secretas / certificados | Não |
> | lojas secretas / segredos | Não |
> | volumes | Sim |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Sim |
> | grupos de contentores | Sim |
> | portais | Sim |
> | redes | Sim |
> | segredos | Sim |
> | volumes | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | fornecedorRegistrations | Não |
> | fornecedorRegistrations / recursosTypeRegistrations | Não |
> | rollouts | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SignalR | Sim |
> | SignalR / eventGridFilters | Não |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | híbridoUseBenefits | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicaçãoDefinições | Sim |
> | aplicações | Sim |
> | jitRequests | Sim |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | managedInstances | Sim |
> | managedInstances / bases de dados | Sim |
> | managedInstances / bases de dados / backupShortTermRetentionPolicies | Não |
> | managedInstances / bases de dados / esquemas / tabelas / colunas / sensibilidadeLabels | Não |
> | managedInstances / bases de dados / vulnerabilidadesAssessments | Não |
> | managedInstances / bases de dados / vulnerabilidadesAssessments / regras / linhas de base | Não |
> | managedInstances / encryptionProtector | Não |
> | managedInstances / chaves | Não |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Não |
> | managedInstances / vulnerabilidadesAssessments | Não |
> | servidores | Sim |
> | servidores / administradores | Não |
> | servidores / comunicaçõesLinks | Não |
> | servidores / bases de dados | Sim |
> | servidores / encriptaçãoProtector | Não |
> | servidores / firewallRules | Não |
> | servidores / chaves | Não |
> | servidores / restauradoresDroppedDatabases | Não |
> | servidores /objetivos de serviço | Não |
> | servidores / tdeCertificados | Não |
> | virtualClusters | Não |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Sim |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Não |
> | SqlVirtualMachines | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | contas eliminadas | Não |
> | armazenamento Contas | Sim |
> | armazenamentoCotas / blobServices | Não |
> | armazenamentoSCotas / serviços de ficheiros | Não |
> | armazenamentoSCotas / filas Serviços | Não |
> | armazenamentoCotas /serviços | Não |
> | armazenamentoCotas / serviços / métricasDefinitions | Não |
> | armazenamentoCotas / tableServices | Não |
> | usos | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | caches | Sim |
> | caches / armazenamentoSTogets | Não |
> | modelos de uso | Não |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | grupos de replicação | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices / Registros registados | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices / Registros registados | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices / Registros registados | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | gestores | Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Sim |
> | streamingjobs | Sim |

## <a name="microsoftsubscription"></a>Microsoft.Subscrição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aceitarChangeTenant | Não |
> | pseudónimos | Não |
> | cancelar | Não |
> | changeTenantRequest | Não |
> | changeTenantStatus | Não |
> | Criar Subscrição | Não |
> | ativar | Não |
> | mudar o nome | Não |
> | AssinaturaDefinitions | Não |
> | Subscrições Operações | Não |
> | assinaturas | Não |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | privateLinkHubs | Sim |
> | áreas de trabalho | Sim |
> | espaços de trabalho / bigDataPools | Sim |
> | espaços de trabalho / operaçõesEstas | Não |
> | espaços de trabalho / sqlDatabases | Sim |
> | espaços de trabalho / sqlPools | Sim |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | ambientes | Sim |
> | ambientes / acessosPolícias | Não |
> | ambientes / fontes de eventos | Sim |
> | ambientes / referênciaSSets | Sim |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | lojas | Sim |
> | lojas / acessosPolícias | Não |
> | lojas / serviços | Não |
> | lojas / serviços / tokens | Não |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | imagenSTas | Sim |
> | imagenTemplates / runOutputs | Não |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | ArcZones | Sim |
> | RecursosPools | Sim |
> | VCenters | Sim |
> | VirtualMachines | Sim |
> | VirtualMachineTemplates | Sim |
> | VirtualNetworks | Sim |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | DedicadoCloudNodes | Sim |
> | ServiçosCloud dedicados | Sim |
> | virtualMachines | Sim |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dispositivos | Sim |
> | registoS de subscrições | Não |
> | fornecedores | Não |
> | fornecedores / skus | Não |
> | fornecedores / vnfs | Não |
> | virtualNetworkFunctionSkus | Não |
> | vnfs | Sim |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | planos | Sim |
> | registoS de subscrições | Não |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | apiManagementAcounts | Não |
> | apiManagementAcounts / apiAcls | Não |
> | apiManagementAcounts / apis | Não |
> | apiManagementAcounts / apis / apiAcls | Não |
> | apiManagementAcounts / apis / connectionAcls | Não |
> | apiManagementAcounts / apis / conexões | Não |
> | apiManagementAcounts / apis / conexões / conexõesAcls | Não |
> | apiManagementAccounts / apis / LocalizedDefinitions | Não |
> | apiManagementAcounts / connectionAcls | Não |
> | apiManagementAcounts / conexões | Não |
> | billingMeters | Não |
> | certificados | Sim |
> | connectionGateways | Sim |
> | conexões | Sim |
> | customApis | Sim |
> | deletedSites | Não |
> | hospedagem Ambientes | Sim |
> | hospedarEnvironments / eventGridFilters | Não |
> | hospedamentoEnvironments / multiRolePools | Não |
> | hospedamentoEnvironments / workerPools | Não |
> | kubeEnvironments | Sim |
> | editoresUsers | Não |
> | recomendações | Não |
> | recursoHealthMetadata | Não |
> | tempos de execução | Não |
> | serverFarms | Sim |
> | serverFarms / eventGridFilters | Não |
> | sites | Sim |
> | sites/config  | Não |
> | sites / eventGridFilters | Não |
> | sites / hostNameBindings | Não |
> | sites / redeConfig | Não |
> | sites / premieraddons | Sim |
> | sites / slots | Sim |
> | sites / slots / eventGridFilters | Não |
> | sites / slots / hostNameBindings | Não |
> | sites / slots / redeConfig | Não |
> | fonteControls | Não |
> | staticSites | Sim |
> | validar | Não |
> | verificarHostingEnvironmentVnet | Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnósticoS | Não |
> | diagnósticoSettingsCategories | Não |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | multipleActivationKeys | Sim |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Serviços de Dispositivos | Sim |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | cargas de trabalho | Sim |
> | cargas de trabalho / instâncias | Não |
> | cargas de trabalho / versões | Não |
> | cargas de trabalho / versões / artefactos | Não |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | componentes | Não |
> | componentesSummary | Não |
> | monitorInstâncias | Não |
> | monitorInstancesSummary | Não |
> | monitores | Não |
> | notificaçõesS | Não |

## <a name="next-steps"></a>Próximos passos

Para obter os mesmos dados que um ficheiro de valores separados por vírgula, faça o download [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
