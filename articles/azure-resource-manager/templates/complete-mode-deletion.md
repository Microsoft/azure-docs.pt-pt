---
title: Eliminação completa de modo
description: Mostra como os tipos de recursos lidam com a eliminação completa do modo nos modelos do Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 9b1c12c061149d60f1e279c78c141cd8b245218c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228032"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Supressão dos recursos da Azure para implementações completas do modo

Este artigo descreve como os tipos de recursos lidam com a eliminação quando não estão num modelo que é implantado em modo completo.

Os tipos de recursos marcados com **Sim** são eliminados quando o tipo não está no modelo implantado com o modo completo.

Os tipos de recursos marcados com **No** não são automaticamente eliminados quando não estão no modelo; no entanto, são eliminados se o recurso dos pais for eliminado. Para obter uma descrição completa do comportamento, consulte os modos de [implementação do Gestor de Recursos Azure](deployment-modes.md).

Se implementar para [mais de um grupo de recursos num modelo,](./deploy-to-resource-group.md)os recursos do grupo de recursos especificados na operação de implantação podem ser eliminados. Os recursos nos grupos de recursos secundários não são eliminados.

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
> - [Microsoft.AnyBuild](#microsoftanybuild)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppAssessment](#microsoftappassessment)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureArcData](#microsoftazurearcdata)
> - [Microsoft.AzureCIS](#microsoftazurecis)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureSphere](#microsoftazuresphere)
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
> - [Microsoft.Cascade](#microsoftcascade)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClusterStor](#microsoftclusterstor)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.ConnectedVehicle](#microsoftconnectedvehicle)
> - [Microsoft.ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
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
> - [Microsoft.EdgeOrder](#microsoftedgeorder)
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
> - [Microsoft.HealthBot](#microsofthealthbot)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSecurity](#microsoftiotsecurity)
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
> - [Microsoft.MobileNetwork](#microsoftmobilenetwork)
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
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatopenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceConnector](#microsoftresourceconnector)
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
> - [Microsoft.ServiceLinker](#microsoftservicelinker)
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
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Serviços de Domínio | Yes |
> | Serviços de Domínio / oucontainer | No |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | apoiarProviders | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aadsupportcases | No |
> | adds serviços | No |
> | agentes | No |
> | anónimos | No |
> | configuração | No |
> | registos | No |
> | relatórios | No |
> | serviços de saúde | No |
> | services | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | advisorScore | No |
> | configurações | No |
> | gerarRecommendações | No |
> | do IdP | No |
> | recomendações | No |
> | supressões | No |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | farmBeats | Yes |
> | farmBeats / eventGridFilters | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | actionRules | Yes |
> | alerts | No |
> | alertasList | No |
> | alertasMetaData | No |
> | alertasSummary | No |
> | alertaSummaryList | No |
> | migrarFromSmartDetection | No |
> | recursosHealthAlertRules | Yes |
> | smartDetectorAlertRules | Yes |
> | smartGroups | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | servidores | Yes |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Serviços apagados | No |
> | obterDomainOwnerIdentifier | No |
> | reportFeedback | No |
> | serviço | Yes |
> | validar Nome de Serviço | No |

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | migrarProjects | Yes |
> | migraçãoProjects/avaliações | No |
> | migraçãoProjects / avaliações / avaliações | No |
> | migraçãoProjects / avaliações / avaliados Aplicações /máquinas | No |
> | migraçãoProjects / avaliações / avaliadasMachines | No |
> | migraçãoProjects / avaliações / avaliadasMachines /aplicações | No |
> | migraçãoProjects / avaliações / máquinasToAssess | No |
> | migrarProjects / sites | No |
> | migraçãoProjects / sites / eletrodomésticosConfigurations | No |
> | migrarProjects / sites / máquinas | No |
> | asversões | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | configuraçõesStores | Yes |
> | configuraçõesStores / eventGridFilters | No |
> | configuraçõesStores / keyValues | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Spring | Yes |
> | Primavera / aplicativos | No |
> | Primavera / apps / implementações | No |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | attestationProviders | Yes |
> | predefinidosProviders | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | acessoReviewScheduleDefinitions | No |
> | acessoReviewScheduleSettings | No |
> | clássicos Administradores | No |
> | dataAliases | No |
> | dataPolicyManifests | No |
> | negar Assignments | No |
> | elevateAccess | No |
> | findOrphanRoleAssignments | No |
> | fechaduras | No |
> | permissões | No |
> | políticasAssinsagens | No |
> | políticasDefinições | No |
> | políticasExemptions | No |
> | políticasSetDefinitions | No |
> | privateLinkAssociations | No |
> | fornecedorOperações | No |
> | recursosManagementPrivateLinks | Yes |
> | roleAssignmentApprovals | No |
> | papéAs de assinaturas | No |
> | roleAssignmentScheduleInstances | No |
> | papelAssignmentScheduleReques | No |
> | papelAssignmentS | No |
> | papelAssignmentsUsageMetrics | No |
> | funçõesDefinitions | No |
> | roleEelegibilidadeSsses | No |
> | roleEelegibilidadeScheduleRequests | No |
> | roleEligibilityScheduls | No |
> | roleManagementPolicies | No |
> | roleManagementPolicyAssignments | No |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Yes |
> | configuraçãoProfileAssignments | No |
> | configuraçãoProfilePreferences | Yes |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | automação Contas | Yes |
> | automação Contas/ configurações | Yes |
> | automação Contas / empregos | No |
> | automaçãoAcontas / privateEndpointConnectionProxies | No |
> | automaçãoAs contas / privateEndpointConnections | No |
> | automação Contas / privateLinkResources | No |
> | automação Contas / runbooks | Yes |
> | automaçãoCotas / softwareUpdateConfigurations | No |
> | automaçãoAcontas / webhooks | No |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | privateClouds | Yes |
> | privateClouds / addons | No |
> | privateClouds / autorizações | No |
> | privateClouds / cloudLinks | No |
> | privateClouds / clusters | No |
> | privateClouds / clusters / datastores | No |
> | privateClouds / globalReachConnections | No |
> | privateClouds / hcxEnterpriseSites | No |
> | privateClouds / scriptExecutions | No |
> | privateClouds / scriptPackages | No |
> | privateClouds / scriptPackages / scriptCmdlets | No |
> | privateClouds / workloadNetworks | No |
> | privateClouds / workloadNetworks / dhcpConfigurations | No |
> | privateClouds / workloadNetworks / dnsServices | No |
> | privateClouds / workloadNetworks / dnsZones | No |
> | privateClouds / workloadNetworks / gateways | No |
> | privateClouds / workloadNetworks / portMirroringProfiles | No |
> | privateClouds / workloadNetworks / segmentos | No |
> | privateClouds / workloadNetworks / virtualMachines | No |
> | privateClouds / workloadNetworks / vmGroups | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | ambientes | No |
> | ambientes / contas | No |
> | ambientes / contas / espaços de nome | No |
> | ambientes / contas / espaços de nome / configurações | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | b2cDirectes | Yes |
> | b2ctenants | No |
> | guestUsages | Yes |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dataControllers | Yes |
> | dataWarehouseInstances | Yes |
> | pós-gresInstances | Yes |
> | sqlManagedInstances | Yes |
> | sqlServerInstances | Yes |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | autopilotEnvironments | Yes |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | sqlServerRegistrations | Yes |
> | sqlServerRegistrations / SqlServers | No |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | catálogos | Yes |
> | catálogos / produtos | Yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | cloudManifestFiles | No |
> | edgeSubscriptions | Yes |
> | linkedSubscriptions | Yes |
> | registos | Yes |
> | registos / clienteSubscrições | No |
> | registos / produtos | No |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Yes |
> | galeriaImagem | Yes |
> | networkInterfaces | Yes |
> | virtualHardDisks | Yes |
> | virtualMachines | Yes |
> | virtualNetworks | Yes |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | bareMetalInstances | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | lotAcons | Yes |
> | lotacos /certificados | No |
> | lotacos / piscinas | No |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | faturaçãoAconse de contas | No |
> | faturaçãoAconssolhos/acordos | No |
> | faturaçãoAs contas / faturaçõesPermissions | No |
> | faturaçãoAcounts / billingProfiles | No |
> | faturação Encomendas / faturaçãoProfiles / faturaçãoPermissions | No |
> | faturação Encomendas / faturaçãoProfiles / billingRoleAssignments | No |
> | billingAcounts / faturaçãoProfiles / billingRoleDefinitions | No |
> | faturação Encomendas / faturaçãoProfiles / faturaçãoSubscrições | No |
> | billingAcounts / faturaçãoProfiles / createBillingRoleAssignment | No |
> | faturaçãoAstros /faturaçãoProfiles / clientes | No |
> | faturaçãoAstros /faturaçãoProfiles / instruções | No |
> | faturação Encomendas / faturas | No |
> | faturação Encomendas / faturas / faturas / folha de preços | No |
> | faturação Encomendas / faturas / faturas / transações | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções | No |
> | faturaçãoAstros /faturaçãoProfiles / faturaSecções / faturasPermissions | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / billingRoleAssignments | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / billingRoleDefinitions | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / faturaSubscrições | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / createBillingRoleAssignment | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / iniciarTransfer | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / produtos | No |
> | faturação Encomendas / faturaçãoProfiles / faturaSecções / produtos / transferência | No |
> | faturaçãoAcounts / faturaçãoProfiles / faturaSecções / produtos / updateAutoRenew | No |
> | faturação Encomendas / faturaçãoProfiles / faturaSecções / transações | No |
> | faturação Encomendas / faturaçãoProfiles / faturaSecções / transferências | No |
> | faturaçãoAcounts / faturaProfiles / faturaSecções / validarDeleteInvoiceSectionEligibility | No |
> | faturação Encomendas / BillingProfiles / patchOperações | No |
> | faturação Encomendas / faturaçãoProfiles / pagamentosMes | No |
> | faturaçãoAcounts / faturaçãoProfiles / políticas | No |
> | faturaçãoAs contas / faturaçãoProfiles / folha de preços | No |
> | faturação Encomendas / faturaçãoProfiles / pricesheetDownloadOperations | No |
> | faturaçãoAstros /faturaçãoProfiles / produtos | No |
> | faturaçãoAstros /faturaçãoProfiles / reservas | No |
> | faturação Encomendas / faturaçãoProfiles / transações | No |
> | faturaçãoAstros /faturaçãoProfiles / validarDeleteBillingProfileligibility | No |
> | faturaçãoAcounts / faturaçãoProfiles / validarDetachPaymentMethodEligibility | No |
> | billingAcounts / billingRoleAssignments | No |
> | billingAccounts / billingRoleDefinitions | No |
> | faturaçãoAcounts / faturaçãoSubscrições | No |
> | faturaçãoAcounts / faturaçãoSubscrições / elevateRole | No |
> | faturaçãoAs contas / faturas/ faturas | No |
> | billingAcounts / createBillingRoleAssignment | No |
> | billingAcounts / criarInvoiceSectionsOperations | No |
> | faturaçãoAsconse de clientes | No |
> | faturaçãoAs contas / clientes / faturaçõesPermissions | No |
> | faturaçãoAs contas / clientes / faturaçãoSubscrições | No |
> | faturaçãoAconssocamos / clientes / iniciarTransfer | No |
> | faturaçãoAconse de clientes / políticas | No |
> | faturaçãoAconse de clientes / produtos | No |
> | faturaçãoAs contas / clientes / transações | No |
> | faturaçãoAs contas / clientes / transferes | No |
> | faturaçãoAconsse de departamentos | No |
> | billingAcounts / departamentos / faturaçõesPermissions | No |
> | billingAcounts / departamentos / billingRoleAssignments | No |
> | billingAcounts / departamentos / billingRoleDefinitions | No |
> | faturaçãoAcounts / departamentos / faturaçãoSubscrições | No |
> | faturaçãoAconse de matrículasAconseções | No |
> | faturaçãoAs contas / matrículasContas / faturações | No |
> | faturaçãoAcounts / matrículasContas / billingRoleAssignments | No |
> | billingAcounts / matrículasContas / billingRoleDefinitions | No |
> | faturaçãoAcounts / matrículasContas / faturaçãoSubscrições | No |
> | faturação | No |
> | faturas / faturas / transações | No |
> | faturas / faturas / transacçõesSummary | No |
> | faturaçãoS / faturaSecções | No |
> | faturaçãoS / faturaSecções / faturaÇãoSubscriçãoMoveOperações | No |
> | faturaçãoS / faturaSecções / faturaSubscrições | No |
> | faturaçãoS / faturaSecções / faturaSubscrições / transferência | No |
> | faturaçãoS / faturaSecções / elevação | No |
> | faturaÇãoS / faturaSecções / iniciarTransfer | No |
> | faturações / faturaSecções / patchOperações | No |
> | faturações / faturaSecções / produtoMoveOperações | No |
> | faturaÇãoS / faturaSecções / produtos | No |
> | faturaÇões / faturaSecções / produtos / transferência | No |
> | faturaÇões / faturaSecções / produtos / updateAutoRenew | No |
> | faturaçãoS / faturaSecções / transações | No |
> | faturaÇões / faturaSecções / transferências | No |
> | faturaçãoAs contas / linhaOfCredit | No |
> | faturaçãoOperações / PatchOperações | No |
> | faturaçãoAcounts / PayableOverage | No |
> | faturação Contas / pagamentosMes | No |
> | faturaçãoAs contas / payNow | No |
> | faturaçãoAconsecos / produtos | No |
> | faturaçãoAsconse de reservas | No |
> | faturaçãoAs/transações | No |
> | faturaçãoPeriods | No |
> | faturaçãoPermissions | No |
> | faturaçãoProperty | No |
> | billingRoleAssignments | No |
> | billingRoleDefinitions | No |
> | createBillingRoleAssignment | No |
> | departamentos | No |
> | matrículasCotas | No |
> | faturas | No |
> | promoções | No |
> | transferências | No |
> | transferências / aceitarTransfer | No |
> | transferências / declínioTransfer | No |
> | transferências / operaçãoStatus | No |
> | transferências / validarTransfer | No |
> | validarAddress | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | mapApis | Yes |
> | actualizaçãoCommunicationPreferência | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | blockchainMembers | Yes |
> | cordaMembers | Yes |
> | observadores | Yes |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | TokenServices | Yes |
> | TokenServices / BlockchainNetworks | No |
> | TokenServices / Grupos | No |
> | TokenServices / Grupos / Contas | No |
> | TokenServices / TokenTemplates | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | blueprintAsignments | No |
> | blueprintAssignments / atribuiçãoOperações | No |
> | blueprintAssignments /operações | No |
> | plantas | No |
> | plantas / artefactos | No |
> | plantas / versões | No |
> | plantas / versões / artefactos | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | botServs | Yes |
> | botServices / canais | No |
> | botServices / conexões | No |
> | hostSettings | No |
> | linguagens | No |
> | modelos | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Redis | Yes |
> | Redis / EventGridFilters | No |
> | Redis / privateEndpointConnectionProxies | No |
> | Redis / privateEndpointConnectionProxies / validar | No |
> | Redis / privateEndpointConnections | No |
> | Redis / privateLinkResources | No |
> | redisEnterprise | Yes |
> | redisEnterprise / bases de dados | No |
> | RedisEnterprise / privateEndpointConnectionProxies | No |
> | RedisEnterprise / privateEndpointConnectionProxies / validar | No |
> | RedisEnterprise / privateEndpointConnections | No |
> | RedisEnterprise / privateLinkResources | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicaçõesReservações | No |
> | autoQuotaIncrease | No |
> | calcularExchange | No |
> | calcularPrimá-lo | No |
> | calcular Prémio Depurchase | No |
> | catálogos | No |
> | comerciaisReservationOrders | No |
> | troca | No |
> | própriasreservações | No |
> | placePurchaseOrder | No |
> | pedidos de reservas | No |
> | pedidos de reservas / calcularRefund | No |
> | reservasOrdeiros / fusão | No |
> | reservasOrdeiros / Reservas | No |
> | reservasOrdeiros / reservas / revisões | No |
> | pedidos de reservas / devolução | No |
> | pedidos de reservas / divisão | No |
> | pedidos de reservas / swap | No |
> | reservas | No |
> | recursosProviders | No |
> | resources | No |
> | validar ReservationOrder | No |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | sites | Yes |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallPolicies | Yes |
> | edgenodes | No |
> | perfis | Yes |
> | perfis / afdendpoints | Yes |
> | perfis / afdendpoints / rotas | No |
> | perfis / personaldomains | No |
> | perfis / pontos finais | Yes |
> | perfis / pontos finais / personaldomains | No |
> | perfis / pontos finais / grupos de origem | No |
> | perfis / pontos finais / origens | No |
> | perfis / grupos de origem | No |
> | perfis / grupos de origem / origens | No |
> | perfis / regras | No |
> | perfis / regras / regras | No |
> | perfis / segredos | No |
> | perfis / seguranças | No |
> | validarProbe | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | certificadoRes | Yes |
> | certificadoRes/certificados | No |
> | validar Informação DeRegistrationRegistrationCertificate | No |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | alterações | No |
> | perfil | No |
> | recursosChanges | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | capacidades | No |
> | domínioNames | Yes |
> | domínioS de identidades / capacidades | No |
> | domainNames / InternaLoadBalancers | No |
> | domínioNamis / serviçoScertificados | No |
> | domínioNames / slots | No |
> | domínioNames / slots / funções | No |
> | domínioNames / slots / funções / métricasDefinitions | No |
> | domínioNames / slots / funções / métricas | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | sistemas operativos | No |
> | quotas | No |
> | recursosTipos | No |
> | validarSubscriptionMoveAvailability | No |
> | virtualMachines | Yes |
> | virtualMachines / diagnósticoSettings | No |
> | virtualMachines / métricasDefinitions | No |
> | virtualMachines / métricas | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | classicInfrastructureResources | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | capacidades | No |
> | expressRouteCrossConnections | No |
> | expressRouteCrossConnections / peerings | No |
> | gatewaySupportouDevices | No |
> | networkSecurityGroups | Yes |
> | quotas | No |
> | reservedIps | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | No |
> | virtualNetworks / virtualNetworkPeerings | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | capacidades | No |
> | discos | No |
> | imagens | No |
> | osImages | No |
> | osPlatformImages | No |
> | publicImages | No |
> | quotas | No |
> | armazenamento Contas | Yes |
> | armazenamentoCotas / blobServices | No |
> | armazenamentoSCotas / serviços de ficheiros | No |
> | armazenamentoCotas / métricasDefinitions | No |
> | armazenamentoCotas /métricas | No |
> | armazenamentoSCotas / filas Serviços | No |
> | armazenamentoCotas /serviços | No |
> | armazenamentoS / serviços / diagnósticoSettings | No |
> | armazenamentoCotas / serviços / métricasDefinitions | No |
> | armazenamentoCotas / serviços / métricas | No |
> | armazenamentoCotas / tableServices | No |
> | armazenamentoCotas / vmImages | No |
> | vmImages | No |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | osdes | Yes |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | planos | Yes |
> | registoS de subscrições | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Yes |
> | contas / privateEndpointConnectionProxies | No |
> | contas / privateEndpointConnections | No |
> | contas / privateLinkResources | No |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | RateCard | No |
> | UsageAggregates | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | disponibilidadeSets | Yes |
> | cloudServs | Yes |
> | cloudServices / networkInterfaces | No |
> | cloudServices / publicIPAddresses | No |
> | cloudServs / roleInstances | No |
> | cloudServices / roleInstances / networkInterfaces | No |
> | cloudServs / funções | No |
> | diskAccesses | Yes |
> | DiskEncryptionSets | Yes |
> | discos | Yes |
> | galerias | Yes |
> | galerias / aplicações | No |
> | galerias / aplicações / versões | No |
> | galerias / imagens | No |
> | galerias / imagens / versões | No |
> | grupos de anfitriões | Yes |
> | hostGroups / anfitriões | Yes |
> | imagens | Yes |
> | grupos de posições de proximidade | Yes |
> | restaurarPointCollections | Yes |
> | restaurarPointCollections / restaurarPonts | No |
> | sharedVMExtensions | Yes |
> | sharedVMExtensions / versões | No |
> | sharedVMImages | Yes |
> | sharedVMImages / versões | No |
> | instantâneos | Yes |
> | sshPublicKeys | Yes |
> | virtualMachines | Yes |
> | virtualMachines / extensões | Yes |
> | virtualMachines / métricasDefinitions | No |
> | virtualMachines / runCommands | Yes |
> | virtualMachineScaleSets | Yes |
> | virtualMachineScaleSets / extensões | No |
> | virtualMachineScaleSets / networkInterfaces | No |
> | virtualMachineScaleSets / publicIPAddresses | No |
> | virtualMachineScaleSets / virtualMachines | No |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | No |

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | CacheNodes | Yes |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | contas de plataforma | Yes |
> | registoS de subscrições | No |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | RecursosPools | Yes |
> | VCenters | Yes |
> | VCenters / InventoryItems | No |
> | VirtualMachines | Yes |
> | VirtualMachines / Extensões | Yes |
> | VirtualMachines / GuestAgents | No |
> | VirtualMachines / HybridIdentityMetadata | No |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | AgregadoCost | No |
> | Saldos | No |
> | Orçamentos | No |
> | Cobranças | No |
> | CostTags | No |
> | créditos | No |
> | eventos | No |
> | Previsões | No |
> | lotes | No |
> | Mercados | No |
> | Folhas de preços | No |
> | produtos | No |
> | ReservasDetails | No |
> | ReservasRecommendationDetails | No |
> | Recommendações de Reservas | No |
> | ReservasSumários | No |
> | Transferências de Reservas | Não |
> | Etiquetas | Não |
> | inquilinos | No |
> | Termos | No |
> | UsageDetails | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | grupos de contentores | Yes |
> | serviceAssociationLinks | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | registos | Yes |
> | registos / agentesPools | Yes |
> | registos / construções | No |
> | registos / construções / cancelar | No |
> | registos / construções / getLogLink | No |
> | registos / buildTasks | Yes |
> | registos / buildTasks / passos | No |
> | registos / connectedRegistries | No |
> | registos / connectedRegistries / desativar | No |
> | registos / eventGridFilters | No |
> | registos / exportaçõesPipelines | No |
> | registos / gerarCredentis | No |
> | registos / getBuildSourceUploadUrl | No |
> | registos / GetCredentials | No |
> | registos / importImage | No |
> | registos / importapipelines | No |
> | registos / pipelineRuns | No |
> | registos / privateEndpointConnectionProxies | No |
> | registos / privateEndpointConnectionProxies / validar | No |
> | registos / privateEndpointConnections | No |
> | registos / privateLinkResources | No |
> | registos / filasBuild | No |
> | registos / regeneraçãoCredential | No |
> | registos / regeneraçãoCredentais | No |
> | registos / replicações | Yes |
> | registos / corre | No |
> | registos / corre / cancela | No |
> | registos / horáriosRun | No |
> | registos / scopeMaps | No |
> | registos / taskRuns | No |
> | registos / tarefas | Yes |
> | registos / fichas | No |
> | registos / actualizaçãoPolícias | No |
> | registos / webhooks | Yes |
> | registros / webhooks / getCallbackConfig | No |
> | registos / webhooks / ping | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | serviços de contentores | Yes |
> | geridoClusters | Yes |
> | ManagedClusters / eventGridFilters | No |
> | openShiftManagedClusters | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Alertas | No |
> | Faturação | No |
> | Orçamentos | No |
> | CloudConnectors | No |
> | Conectores | Yes |
> | costAllocationRules | No |
> | Departamentos | No |
> | Dimensões | No |
> | MatrículasAconselhos | No |
> | Exportações | No |
> | Contas externas deBilling | No |
> | ExternalBillingAccounts / Alertas | No |
> | ExternalBillingAccounts / Dimensões | No |
> | Contas externas deBilling / Previsão | No |
> | ExternalBillingAccounts / Consulta | No |
> | Subscrições externas | No |
> | Subscrições externas / Alertas | No |
> | ExternalSubscriptions / Dimensões | No |
> | ExternalSubscriptions / Previsão | No |
> | Subscrições externas / Consulta | No |
> | buscarPrices | No |
> | Previsão | No |
> | GenerateDetailedCostReport | No |
> | Gerar ReservaçãoDetailsReport | No |
> | Informações | No |
> | Consulta | No |
> | registar | No |
> | Configs relatório | No |
> | Relatórios | No |
> | Horários | No |
> | Definições | No |
> | showbackRules | No |
> | Vistas | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | DisableLockbox | No |
> | EnableLockbox | No |
> | pedidos | No |
> | TenantOptedIn | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | associações | No |
> | recursosProviders | Yes |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | casos | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | empregos | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | áreas de trabalho | Yes |
> | espaços de trabalho / dbWorkspaces | No |
> | espaços de trabalho / virtualNetworkPeerings | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | catálogos | Yes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dataFactories | Yes |
> | dataFactories / diagnosticSettings | No |
> | dataFactories / métricasDefinitions | No |
> | dataFactorySchema | No |
> | fábricas | Yes |
> | fábricas / integraçãoRuntimes | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Yes |
> | contas / dadosLakeStoreAccounts | No |
> | contas / armazenamentoCotações | No |
> | contas / armazenamentoCotas / contentores | No |
> | contas / transferAnlyticsUnits | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Yes |
> | contas / eventGridFilters | No |
> | contas / firewallRules | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Imigrações de dados | No |
> | services | Yes |
> | serviços / projetos | Yes |
> | Serviços sqlMigration | Yes |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | BackupVaults | Yes |
> | Redes de Recursos | Yes |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Yes |
> | contas / ações | No |
> | contas / ações / conjuntos de dados | No |
> | contas / ações / convites | No |
> | contas / ações / subscrições de partilha de fornecedores | No |
> | contas / ações / sincronizaçõesS | No |
> | contas / subscrições de ações | No |
> | contas / subscrições de ações / consumerSourceDataSets | No |
> | contas / subscrições de ações / datasetmappings | No |
> | contas / subscrições de ações / gatilhos | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | servidores | Yes |
> | servidores / assessores | No |
> | servidores / chaves | No |
> | servidores / privadosEndpointConnectionProxies | No |
> | servidores / privateEndpointConnections | No |
> | servidores / privateLinkResources | No |
> | servidores / consultasTextos | No |
> | servidores / recuperáveisServers | No |
> | servidores / resetQueryPerformanceInsightData | No |
> | servidores / iniciar | No |
> | servidores / parar | No |
> | servidores / topQueryStatistics | No |
> | servidores / virtualNetworkRules | No |
> | servidores / esperaStatistics | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | flexibleServers | Yes |
> | servidores | Yes |
> | servidores / assessores | No |
> | servidores / chaves | No |
> | servidores / privadosEndpointConnectionProxies | No |
> | servidores / privateEndpointConnections | No |
> | servidores / privateLinkResources | No |
> | servidores / consultasTextos | No |
> | servidores / recuperáveisServers | No |
> | servidores / resetQueryPerformanceInsightData | No |
> | servidores / iniciar | No |
> | servidores / parar | No |
> | servidores / topQueryStatistics | No |
> | servidores / upgrade | No |
> | servidores / virtualNetworkRules | No |
> | servidores / esperaStatistics | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DbforPostgresql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | flexibleServers | Yes |
> | grupos de servidores | Yes |
> | serverGroupsv2 | Yes |
> | servidores | Yes |
> | servidores / assessores | No |
> | servidores / chaves | No |
> | servidores / privadosEndpointConnectionProxies | No |
> | servidores / privateEndpointConnections | No |
> | servidores / privateLinkResources | No |
> | servidores / consultasTextos | No |
> | servidores / recuperáveisServers | No |
> | servidores / resetQueryPerformanceInsightData | No |
> | servidores / topQueryStatistics | No |
> | servidores / virtualNetworkRules | No |
> | servidores / esperaStatistics | No |
> | servidorv2 | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | artefactosSources | Yes |
> | rollouts | Yes |
> | serviceTopologias | Yes |
> | serviçoTopologias / serviços | Yes |
> | serviceTopologias / serviços / serviços Unidades | Yes |
> | passos | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | grupos de aplicações | Yes |
> | grupos de aplicações / aplicações | No |
> | grupos de aplicações / desktops | No |
> | grupos de aplicações / startmenuitems | No |
> | hostpools | Yes |
> | hostpools / msixpackages | No |
> | hostpools / sessão | No |
> | hostpools / sessionhosts / utilizadores | No |
> | hostpools / utilizadores | No |
> | planos de escala | Yes |
> | áreas de trabalho | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Elásticos | Yes |
> | Elásticos / IotHubTenants | Yes |
> | Elásticos / IotHubTenants / securitySettings | No |
> | IotHubs | Yes |
> | IotHubs / eventGridFilters | No |
> | IotHubs / securitySettings | No |
> | Serviços de Provisionamento | Yes |
> | usos | No |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Yes |
> | contas / instâncias | Yes |
> | registoS de subscrições | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | oleodutos | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | controladores | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | centros de laboratório | Yes |
> | laboratórios | Yes |
> | laboratórios / ambientes | Yes |
> | laboratórios / serviceRunners | Yes |
> | laboratórios / virtualMachines | Yes |
> | horários | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | digitalTwinsInstances | Yes |
> | digitalTwinsInstances / endpoints | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | cassandraClusters | Yes |
> | base de dadosSes | No |
> | bases de dadosAconses | Yes |
> | RestauraçãoDatabaseAccounts | No |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | domínios | Yes |
> | domínios / domainOwnershipIdentifiers | No |
> | gerarSsoRequest | No |
> | topLevelDomains | No |
> | validar aDomainRegistrationInformation | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | LCSprojects | No |
> | LCSprojects / deplosagens de nuvens | No |
> | LCSprojects / conectores | No |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | endereços | Yes |
> | encomendasColelecções | Yes |
> | encomendas | Yes |
> | produtoFamiliesMetadata | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | services | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | domínios | Yes |
> | domínios / tópicos | No |
> | eventoSubscrições | No |
> | extensãoTopics | No |
> | parceiroNamespaces | Yes |
> | partnerNamespaces / eventChannels | No |
> | partnerRegistrations | Yes |
> | partnerTopics | Yes |
> | partnerTopics / eventoSubscrições | No |
> | systemTopics | Yes |
> | systemTopics / eventSubscrições | No |
> | tópicos | Yes |
> | tópicotips | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Yes |
> | espaços de nome | Yes |
> | espaços de nomes / regras de autorização | No |
> | espaços de nome / disasterrecoveryconfigs | No |
> | espaços de nomes / eventhubs | No |
> | espaços de nome / eventhubs / autorizações | No |
> | espaços de nomes / eventhubs / grupos de consumidores | No |
> | espaços de nome / redes de regras | No |
> | espaços de nome / privateEndpointConnections | No |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | experimentOs espaços de trabalho | Yes |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | espaços de nome | Yes |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | recursosConfigurations | No |
> | featureProviderNamespaces | No |
> | recursosProviders | No |
> | funcionalidades | No |
> | fornecedores | No |
> | subscriçõesFesturaRegistrações | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | inscrever | No |
> | galleryitems | No |
> | generateartifactacssuri | No |
> | myareas | No |
> | myareas / áreas | No |
> | myareas / áreas / áreas | No |
> | myareas / áreas / áreas / galleryitems | No |
> | myareas / áreas / galleryitems | No |
> | myareas / galleryitems | No |
> | registar | No |
> | resources | No |
> | retrieveresourcesbyid | No |

## <a name="microsoftgenomics"></a>Microsoft.Geómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | autoManagedAccounts | Yes |
> | autoManagedVmConfigurationProfiles | Yes |
> | configuraçãoProfileAssignments | No |
> | guestConfigurationAssignments | No |
> | software | No |
> | softwareUpdateProfile | No |
> | softwareUpdates | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | hanaInstances | Yes |
> | sapMonitores | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | DEDICADOHSMs | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | clusterPools | Yes |
> | clusterPools / clusters | Yes |
> | aglomerados | Yes |
> | clusters / aplicações | No |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | healthBots | Yes |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | services | Yes |
> | serviços / iomtconnectors | No |
> | serviços / iomtconnectors / conexões | No |
> | serviços / iomtconnectors / mapeamentos | No |
> | serviços / privateEndpointConnectionProxies | No |
> | serviços / privateEndpointConnections | No |
> | serviços / privateLinkResources | No |
> | áreas de trabalho | Yes |
> | espaços de trabalho / dicomservices | Yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | máquinas | Yes |
> | máquinas / avaliaçãoPatches | No |
> | máquinas / extensões | Yes |
> | máquinas / instalaçãoPatches | No |
> | máquinas / privateLinkScopes | No |
> | privateLinkScopes | Yes |
> | privateLinkScopes / privateEndpointConnectionProxies | No |
> | privateLinkScopes / privateEndpointConnections | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dataSmanagers | Yes |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dispositivos | Yes |
> | funções em rede | Yes |
> | redeFunctionVendors | No |
> | registoS de subscrições | No |
> | Fornecedores | No |
> | Fornecedores / fornecedoreskus | No |
> | Fornecedores / vendorskus / pré-visualizações | No |
> | virtualNetworkFunções | Yes |
> | virtualNetworkFunctionVendors | No |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | componentes | Yes |
> | networkScopes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | empregos | Yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnósticoS | No |
> | diagnósticoSettingsCategories | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | appTemplates | No |
> | IoTApps | Yes |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | defesaSettings | No |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Graph | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | eliminadosManagedHSMs | No |
> | deletedVaults | No |
> | hsmPools | Yes |
> | managedHSMs | Yes |
> | cofres | Yes |
> | cofres / acessosPolícias | No |
> | cofres / eventoSGridFiltros | No |
> | cofres / chaves | No |
> | cofres / chaves / versões | No |
> | cofres / segredos | No |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | clusters conectados | Yes |
> | registoS de subscrições | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | extensões | No |
> | fonteControlConfigurations | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Yes |
> | clusters / confinações de base de dados | No |
> | clusters / bases de dados | No |
> | clusters / bases de dados / ligações de dados | No |
> | clusters / bases de dados / ligações eventhub | No |
> | clusters / bases de dados / principais assinaturas | No |
> | clusters / bases de dados / scripts | No |
> | clusters / ligações de dados | No |
> | aglomerados / principais destacamentos | No |
> | clusters / semelhanças partilhadas | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | contas de laboratório | Yes |
> | labplans | Yes |
> | laboratórios | Yes |
> | utilizadores | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | hospedagem Ambientes | Yes |
> | integraçãoAconselhos | Yes |
> | integração ServiçoEnvironments | Yes |
> | integraçãoServiceEnvironments / managedApis | Yes |
> | isoladosEnvironments | Yes |
> | fluxos de trabalho | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | planos de compromissos | Yes |
> | webServices | Yes |
> | Áreas de Trabalho | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | modelos | Yes |
> | virtualclusters | Yes |
> | áreas de trabalho | Yes |
> | espaços de trabalho / lotendpoints | Yes |
> | espaços de trabalho / lotEndpoints / implementações | Yes |
> | espaços de trabalho / lotEndpoints / implementações / empregos | No |
> | espaços de trabalho / lotEndpoints / empregos | No |
> | espaços de trabalho / códigos | No |
> | espaços de trabalho / códigos / versões | No |
> | espaços de trabalho / computas | No |
> | espaços de trabalho / dados | No |
> | espaços de trabalho / datastores | No |
> | espaços de trabalho / ambientes | No |
> | espaços de trabalho / eventosGridFiltros | No |
> | espaços de trabalho / empregos | No |
> | espaços de trabalho / rotulagemJobs | No |
> | espaços de trabalho / linkedServices | No |
> | espaços de trabalho / modelos | No |
> | espaços de trabalho / modelos / versões | No |
> | espaços de trabalho / pontos de encontro online | Yes |
> | espaços de trabalho / onlineEndpoints / implementações | Yes |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicardados | No |
> | configuraçõesAssignments | No |
> | configuções de manutenção | Yes |
> | publicMaintenanceConfigurations | No |
> | atualizações | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Identidades | No |
> | nomeações para assediadas do utilizador | Yes |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | managedNetworks | Yes |
> | managedNetworks / managedNetworkGroups | Yes |
> | managedNetworks / managedNetworkPeeringPolicies | Yes |
> | notificação | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | No |
> | registosAssondos | No |
> | registoDefinições | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | obterEntidades | No |
> | grupos de gestão | No |
> | managementGroups / configurações | No |
> | resources | No |
> | startTenantBackfill | No |
> | inquilinoBackfillStatus | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Yes |
> | contas / criadores | Yes |
> | contas / eventGridFilters | No |
> | contas / privateAtlases | Yes |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | macc | No |
> | ofertas | No |
> | oferta deTipos | No |
> | oferta deTipos / editores | No |
> | oferta DeTipos / editores / ofertas | No |
> | oferta DeTipos / editores / ofertas / planos | No |
> | oferta DeTipos / editores / ofertas / planos / acordos | No |
> | ofertaTipos / editores / ofertas / planos / configs | No |
> | ofertaTipos / editoras / ofertas / planos / configs / importImage | No |
> | privadas | No |
> | privateStoreClient | No |
> | lojas privadas | No |
> | privateStores / AdminRequestApprovals | No |
> | privateStores / ofertas | No |
> | privateStores / ofertas / reconhecimentoNotificação | No |
> | privateStores / consultasNotificationsState | No |
> | privateStores / Pedidos Desemprovações | No |
> | privateStores / pedidoSrovais / consulta | No |
> | privateStores / pedidoProvações / retiradaPlane | No |
> | produtos | No |
> | editores | No |
> | editores / ofertas | No |
> | editores / ofertas / alterações | No |
> | registar | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | classicDevServices | Yes |
> | actualizaçãoCommunicationPreferência | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | acordos | No |
> | tipos de ofertas | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | mediaservices | Yes |
> | mediaservices / filtros de conta | No |
> | mediaserviços / ativos | No |
> | mediaservices / ativos / activosFiltros | No |
> | mediaservices / conteúdoKeyPolicies | No |
> | mediaservices / eventGridFilters | No |
> | mediaservices / graphInstances | No |
> | mediaservices / graftologies | No |
> | mediaservices / liveEventOperações | No |
> | mediaservices / liveEvents | Yes |
> | mediaservices / liveEvents / liveOutputs | No |
> | mediaservices / liveOutputOperations | No |
> | mediaservices / mediaGraphs | No |
> | mediaservices / privateEndpointConnectionOperations | No |
> | mediaservices / privateEndpointConnectionProxies | No |
> | mediaservices / privateEndpointConnections | No |
> | mediaservices / streamingEndpointOperations | No |
> | mediaservices / streamingEndpoints | Yes |
> | mediaservices / streamingLocators | No |
> | mediaservices / streamingPolicies | No |
> | mediaservices / transforma | No |
> | mediaservices / transforma / empregos | No |
> | videoAnalyzers | Yes |
> | videoAnalyzers / edgeModules | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | appClusters | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | avaliaçãoProjectos | Yes |
> | migrarprojects | Yes |
> | moveCollections | Yes |
> | projetos | Yes |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | holográficosBroadcastAccounts | Yes |
> | objetoAnchorsCocounts | Yes |
> | objetoUnderstandingAccounts | Yes |
> | remotorcoscos | Yes |
> | spatialAnchorsAccounts | Yes |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | redes | Yes |
> | redes / sites | Yes |
> | packetCores | Yes |
> | sims | Yes |
> | sims / simProfiles | Yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | netAppAccounts | Yes |
> | netAppAccounts / accountBackups | No |
> | netAppAccounts / capacityPools | Yes |
> | netAppAccounts / capacityPools / volumes | Yes |
> | netAppAccounts / capacityPools / volumes / snapshots | No |
> | netAppAccounts / volumeGrupos | No |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | applicationGateways | Yes |
> | aplicaçãoGatewayWebApplicationFirewallPolicies | Yes |
> | applicationSecurityGroups | Yes |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Yes |
> | bastionHosts | Yes |
> | bgpServiceCommunidades | No |
> | conexões | Yes |
> | ddosCustomPolicies | Yes |
> | ddosProtectionPlans | Yes |
> | dnsOperationStatuses | No |
> | dnszones | Yes |
> | dnszones / A | No |
> | dnszones / AAAA | No |
> | dnszones / todos os | No |
> | dnszones / CAA | No |
> | dnszones / CNAME | No |
> | dnszones / MX | No |
> | dnszones / NS | No |
> | dnszones / PTR | No |
> | dnszones / recordets | No |
> | dnszones / SOA | No |
> | dnszones / SRV | No |
> | dnszones / TXT | No |
> | expressRouteCircuits | Yes |
> | expressRouteCrossConnections | Yes |
> | expressRouteGateways | Yes |
> | expressRoutePorts | Yes |
> | expressRouteServiceProviders | No |
> | firewallPolicies | Yes |
> | frontdoors | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Yes |
> | obter Retecnretecencia | No |
> | internaNotificar | No |
> | ipGrupos | Yes |
> | loadBalancers | Yes |
> | localNetworkGateways | Yes |
> | natGateways | Yes |
> | networkIntentPolicies | Yes |
> | networkInterfaces | Yes |
> | redesProfiles | Yes |
> | networkSecurityGroups | Yes |
> | networkWatchers | Yes |
> | networkWatchers / connectionMonitors | Yes |
> | networkWatchers / flowLogs | Yes |
> | networkWatchers / lentes | Yes |
> | networkWatchers / pingMeshes | Yes |
> | p2sVpnGateways | Yes |
> | privateDnsOperationStatuses | No |
> | privateDnsZones | Yes |
> | privateDnsZones / A | No |
> | privateDnsZones / AAAA | No |
> | privateDnsZones / todos | No |
> | privateDnsZones / CNAME | No |
> | privateDnsZones / MX | No |
> | privateDnsZones / PTR | No |
> | privateDnsZones / SOA | No |
> | privateDnsZones / SRV | No |
> | privateDnsZones / TXT | No |
> | privateDnsZones / virtualNetworkLinks | Yes |
> | privadosEndpoints | Yes |
> | privateLinkServices | Yes |
> | publicIPAddresses | Yes |
> | publicIPPrefixos | Yes |
> | routeFilters | Yes |
> | routeTables | Yes |
> | serviceEndpointPolicies | Yes |
> | trafficManagerGeographicHierarchies | No |
> | trafficmanagerprofiles | Yes |
> | trafficmanagerprofiles / heatMaps | No |
> | trafficManagerUserMetricsKeys | No |
> | virtualHubs | Yes |
> | virtualNetworkGateways | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks / sub-redes | No |
> | virtualNetworkTaps | Yes |
> | virtualWans | Yes |
> | VPNGateways | Yes |
> | VPNSites | Yes |
> | webApplicationFirewallPolicies | Yes |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | CadernoProxies | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | espaços de nome | Yes |
> | espaços de nomes / notificaçõesHubs | Yes |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | osNamespaces | Yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | HiperVSites | Yes |
> | ImportaçõesSites | Yes |
> | MestresSites | Yes |
> | ServidorEsites | Yes |
> | VMwareSites | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Yes |
> | eliminadosEspaços de trabalho | No |
> | linkTargets | No |
> | pacotes de consultas | Yes |
> | storageInsightConfigs | No |
> | áreas de trabalho | Yes |
> | espaços de trabalho / dataExports | No |
> | espaços de trabalho / dataSources | No |
> | espaços de trabalho / linkedServices | No |
> | workspaces / linkedStorageAccounts | No |
> | espaços de trabalho / metadados | No |
> | espaços de trabalho / consulta | No |
> | espaços de trabalho / scopedPrivateLinkProxies | No |
> | espaços de trabalho / armazenamentoInsightConfigs | No |
> | espaços de trabalho / tabelas | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | sociedades de gestão | No |
> | configurações de gestão | Yes |
> | soluções | Yes |
> | Modos de exibição | Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | cdnPeeringPrefixes | No |
> | legacyPeerings | No |
> | peerAsns | No |
> | espreiteiros | Yes |
> | peeringServiceCountries | No |
> | peeringServiceProviders | No |
> | serviços de peering | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | atestados | No |
> | eventGridFiltros | No |
> | policyEvents | No |
> | policyMetadata | No |
> | estados de política | No |
> | policyTrackedResources | No |
> | remediações | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Consolas do | No |
> | dashboards | Yes |
> | inquilinos configurações | No |
> | userSettings | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Yes |
> | inquilinos | Yes |
> | inquilinos / espaços de trabalho | No |
> | workspaceCollections | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | autoScaleVCores | Yes |
> | capacidades | Yes |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | enterprisePolícias | Yes |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Yes |
> | contas eliminadas | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | fornecedorRegistrations | No |
> | fornecedorRegistrations / customRollouts | No |
> | fornecedorRegistrations /defaultRollouts | No |
> | fornecedorRegistrations / recursosTypeRegistrations | No |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Yes |
> | contas eliminadas | No |
> | obter Contagem de Relatórios | No |
> | removerDefaultA contagem | No |
> | setDefaultA contagem de votos | No |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Áreas de Trabalho | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | backupProtectedItems | No |
> | cofres | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatopenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | OpenShiftClusters | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | espaços de nome | Yes |
> | espaços de nomes / regras de autorização | No |
> | namespaces / ligações híbridas | No |
> | espaços de nome / ligações híbridas / regras de autorização | No |
> | espaços de nome / privateEndpointConnections | No |
> | espaços de nomes / wcfrelays | No |
> | espaços de nome / wcfrelays / autorizações | No |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aparelhos | Yes |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | consultas | Yes |
> | recursosChangeDetails | No |
> | recursosChanges | No |
> | resources | No |
> | recursosHistória | No |
> | subscriçõesStatus | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | disponibilidade Estatísticas | No |
> | criançaSetilabilityStatuses | No |
> | criançaresources | No |
> | emergentes | No |
> | eventos | No |
> | impactedResources | No |
> | do IdP | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | implementações | No |
> | implantações / operações | No |
> | implementaçõesScripts | Yes |
> | implementaçõesScripts / registos | No |
> | ligações | No |
> | fornecedores | No |
> | grupos de recursos | No |
> | assinaturas | No |
> | modeloSpecs | Yes |
> | modeloSpecs / versões | Yes |
> | inquilinos | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Yes |
> | resources | Yes |
> | saasresources | No |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | nuvens | Yes |
> | VirtualMachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |
> | vmmservers | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | recursoHealthMetadata | No |
> | serviços de pesquisa | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | No |
> | advancedThreatProtectionSettings | No |
> | alerts | No |
> | alertaSuppressãoRules | No |
> | permitidoConnections | No |
> | aplicaçãoWhitelistings | No |
> | avaliaçãoMetadata | No |
> | avaliações | No |
> | autoDismissAlertsRules | No |
> | automações | Yes |
> | AutoProvisioningSettings | No |
> | Cumprimentos | No |
> | conectores | No |
> | dataCollectionAgents | No |
> | dispositivos | No |
> | grupos de segurança de dispositivos | No |
> | descobertasSecuritySolutions | No |
> | sistemas de Segurança externos | No |
> | InformaçãoProtetionPolicies | No |
> | ingestionSettings | No |
> | insights | No |
> | iotAlerts | No |
> | iotAlertTypes | No |
> | iotDefenderSettings | No |
> | iotRecommendations | No |
> | iotRecommendationTypes | No |
> | iotSecuritySolutions | Yes |
> | iotSecuritySolutions / analyticsModels | No |
> | iotSecuritySolutions / analyticsModels / agregadosAlerts | No |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | No |
> | iotSecuritySolutions / iotAlerts | No |
> | iotSecuritySolutions / iotAlertTypes | No |
> | iotSecuritySolutions / iotRecommendations | No |
> | iotSecuritySolutions / iotRecommendationTypes | No |
> | iotSensores | No |
> | iotSites | No |
> | jitNetworkAccessPolicies | No |
> | jitPolicies | No |
> | onPremiseIotSensors | No |
> | políticas | No |
> | preços | No |
> | regulatórioComplianceStandards | No |
> | regulatórioComplianceStandards /regulatórioComplianceControls | No |
> | regulatórioComplianceStands / regulatórioComplianceControls / regulatórioComplianceAssess | No |
> | secureScoreControlDefinitions | No |
> | secureScoreControls | No |
> | secureScores | No |
> | secureScores / secureScoreControls | No |
> | securityContacts | No |
> | segurançaSoluções | No |
> | securitySolutionsReferenceData | No |
> | securityStatuses | No |
> | securityStatusesSummaries | No |
> | servidorVulnerabilityAssessments | No |
> | definições | No |
> | sqlVulnerabilityAssesments | No |
> | subAssessments | No |
> | tarefas | No |
> | topologias | No |
> | workspaceSettings | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnósticoS | No |
> | diagnósticoSettingsCategories | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | agregações | No |
> | alertaRs | No |
> | alertaRuleTemplates | No |
> | regras automation | No |
> | marcadores | No |
> | casos | No |
> | dataConnectors | No |
> | dataConnectorsCheckRequirements | No |
> | enriquecimento | No |
> | entidades | No |
> | entidadeQueries | No |
> | entidadeQueryTemplates | No |
> | Incidentes | No |
> | escritórioConsents | No |
> | definições | No |
> | ameaçaIntelligence | No |
> | listas de observação | No |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | consolas Serviços | No |
> | serialPorts | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | espaços de nome | Yes |
> | espaços de nomes / regras de autorização | No |
> | espaços de nome / disasterrecoveryconfigs | No |
> | espaços de nomes / filtros de eventos | No |
> | espaços de nome / redes de regras | No |
> | espaços de nome / privateEndpointConnections | No |
> | espaços de nomes / filas | No |
> | espaços de nome / filas / autorizações | No |
> | espaços de nomes / tópicos | No |
> | espaços de nome / tópicos / autorizações | No |
> | espaços de nomes / tópicos / subscrições | No |
> | espaços de nomes / tópicos / subscrições / regras | No |
> | premiumMessagingRegions | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Yes |
> | aglomerados | Yes |
> | clusters / aplicações | No |
> | grupos de contentores | Yes |
> | containerGroupSets | Yes |
> | edgeclusters | Yes |
> | edgeclusters / aplicações | No |
> | geremclusters | Yes |
> | gerenciouclusters / aplicações | No |
> | gerenciou / aplicações / serviços | No |
> | gerenciadores / aplicaçõesTipos | No |
> | gerenciadores / aplicaçõesTipos / versões | No |
> | gerenciouclusters / não desapoias | No |
> | redes | Yes |
> | lojas secretas | Yes |
> | lojas secretas / certificados | No |
> | lojas secretas / segredos | No |
> | volumes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Yes |
> | grupos de contentores | Yes |
> | portais | Yes |
> | redes | Yes |
> | segredos | Yes |
> | volumes | Yes |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | linkers | No |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | fornecedorRegistrations | No |
> | fornecedorRegistrations / recursosTypeRegistrations | No |
> | rollouts | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SignalR | Yes |
> | SignalR / eventGridFilters | No |
> | WebPubSub | Yes |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Yes |
> | contas / contaQuotaPolicies | No |
> | contas / grupoPolícias | No |
> | contas / empregos | No |
> | contas / armazenamentoContainers | No |
> | imagens | No |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | híbridoUseBenefits | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicaçãoDefinições | Yes |
> | aplicações | Yes |
> | jitRequests | Yes |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | managedInstances | Yes |
> | managedInstances / bases de dados | Yes |
> | managedInstances / bases de dados / backupShortTermRetentionPolicies | No |
> | managedInstances / bases de dados / esquemas / tabelas / colunas / sensibilidadeLabels | No |
> | managedInstances / bases de dados / vulnerabilidadesAssessments | No |
> | managedInstances / bases de dados / vulnerabilidadesAssessments / regras / linhas de base | No |
> | managedInstances / encryptionProtector | No |
> | managedInstances / chaves | No |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | No |
> | managedInstances / vulnerabilidadesAssessments | No |
> | servidores | Yes |
> | servidores / administradores | No |
> | servidores / comunicaçõesLinks | No |
> | servidores / bases de dados | Yes |
> | servidores / encriptaçãoProtector | No |
> | servidores / firewallRules | No |
> | servidores / chaves | No |
> | servidores / restauradoresDroppedDatabases | No |
> | servidores /objetivos de serviço | No |
> | servidores / tdeCertificados | No |
> | virtualClusters | No |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Yes |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | No |
> | SqlVirtualMachines | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | contas eliminadas | No |
> | armazenamento Contas | Yes |
> | armazenamentoCotas / blobServices | No |
> | armazenamentoSCotas / serviços de ficheiros | No |
> | armazenamentoSCotas / filas Serviços | No |
> | armazenamentoCotas /serviços | No |
> | armazenamentoCotas / serviços / métricasDefinitions | No |
> | armazenamentoCotas / tableServices | No |
> | usos | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | amlFilesystems | Yes |
> | caches | Yes |
> | caches / armazenamentoSTogets | No |
> | modelos de uso | No |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | grupos de replicação | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices / Registros registados | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | armazenamentoSyncServices / fluxos de trabalho | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices / Registros registados | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | armazenamentoSyncServices / fluxos de trabalho | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices / Registros registados | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | armazenamentoSyncServices / fluxos de trabalho | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | gestores | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Yes |
> | clusters / privateEndpoints | No |
> | streamingjobs | Yes |

## <a name="microsoftsubscription"></a>Microsoft.Subscrição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aceitarChangeTenant | No |
> | aceitar Propriedade | No |
> | acceptOwnershipStatus | No |
> | pseudónimos | No |
> | cancelar | No |
> | changeTenantRequest | No |
> | changeTenantStatus | No |
> | Criar Subscrição | No |
> | ativar | No |
> | políticas | No |
> | mudar o nome | No |
> | AssinaturaDefinitions | No |
> | Subscrições Operações | No |
> | assinaturas | No |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | privateLinkHubs | Yes |
> | áreas de trabalho | Yes |
> | espaços de trabalho / bigDataPools | Yes |
> | espaços de trabalho / operaçõesEstas | No |
> | espaços de trabalho / sqlDatabases | Yes |
> | espaços de trabalho / sqlPools | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | ambientes | Yes |
> | ambientes / acessosPolícias | No |
> | ambientes / fontes de eventos | Yes |
> | ambientes / privateEndpointConnectionProxies | No |
> | ambientes / privateEndpointConnections | No |
> | ambientes / privateLinkResources | No |
> | ambientes / referênciaSSets | Yes |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | lojas | Yes |
> | lojas / acessosPolícias | No |
> | lojas / serviços | No |
> | lojas / serviços / tokens | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | imagenSTas | Yes |
> | imagenTemplates / runOutputs | No |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | ArcZones | Yes |
> | RecursosPools | Yes |
> | VCenters | Yes |
> | VCenters / InventoryItems | No |
> | virtualmachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | DedicadoCloudNodes | Yes |
> | ServiçosCloud dedicados | Yes |
> | virtualMachines | Yes |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dispositivos | Yes |
> | registoS de subscrições | No |
> | fornecedores | No |
> | fornecedores / skus | No |
> | fornecedores / vnfs | No |
> | virtualNetworkFunctionSkus | No |
> | vnfs | Yes |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Yes |
> | planos | Yes |
> | registoS de subscrições | No |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | apiManagementAcounts | No |
> | apiManagementAcounts / apiAcls | No |
> | apiManagementAcounts / apis | No |
> | apiManagementAcounts / apis / apiAcls | No |
> | apiManagementAcounts / apis / connectionAcls | No |
> | apiManagementAcounts / apis / conexões | No |
> | apiManagementAcounts / apis / conexões / conexõesAcls | No |
> | apiManagementAccounts / apis / LocalizedDefinitions | No |
> | apiManagementAcounts / connectionAcls | No |
> | apiManagementAcounts / conexões | No |
> | billingMeters | No |
> | certificados | Yes |
> | connectionGateways | Yes |
> | conexões | Yes |
> | customApis | Yes |
> | deletedSites | No |
> | functionAppStacks | No |
> | gerarGithubAccessTokenForAppserviceCLI | No |
> | hospedagem Ambientes | Yes |
> | hospedarEnvironments / eventGridFilters | No |
> | hospedamentoEnvironments / multiRolePools | No |
> | hospedamentoEnvironments / workerPools | No |
> | kubeEnvironments | Yes |
> | editoresUsers | No |
> | recomendações | No |
> | recursoHealthMetadata | No |
> | tempos de execução | No |
> | serverFarms | Yes |
> | serverFarms / eventGridFilters | No |
> | serverFarms / firstPartyApps | No |
> | serverFarms / firstPartyApps / keyVaultSettings | No |
> | sites | Yes |
> | sites/config  | No |
> | sites / eventGridFilters | No |
> | sites / hostNameBindings | No |
> | sites / redeConfig | No |
> | sites / premieraddons | Yes |
> | sites / slots | Yes |
> | sites / slots / eventGridFilters | No |
> | sites / slots / hostNameBindings | No |
> | sites / slots / redeConfig | No |
> | fonteControls | No |
> | staticSites | Yes |
> | validar | No |
> | verificarHostingEnvironmentVnet | No |
> | webAppStacks | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnósticoS | No |
> | diagnósticoSettingsCategories | No |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | multipleActivationKeys | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Serviços de Dispositivos | Yes |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | migraçõesAgents | Yes |
> | cargas de trabalho | Yes |
> | cargas de trabalho / instâncias | No |
> | cargas de trabalho / versões | No |
> | cargas de trabalho / versões / artefactos | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | monitores | Não |

## <a name="next-steps"></a>Próximos passos

Para obter os mesmos dados que um ficheiro de valores separados por vírgula, faça o download [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
