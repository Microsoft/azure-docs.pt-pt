---
title: Eliminação completa de modo
description: Mostra como os tipos de recursos lidam com a eliminação completa do modo nos modelos do Gestor de Recursos Do Azure.
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 5f797974212636460306c6a17869d6b8380545ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664411"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Eliminação dos recursos do Azure para implementações completas de modos

Este artigo descreve como os tipos de recursos lidam com a eliminação quando não estão num modelo que é implantado em modo completo.

Os tipos de recursos marcados com **Sim** são eliminados quando o tipo não está no modelo implantado com o modo completo.

Os tipos de recursos marcados com **Não** não são automaticamente eliminados quando não estão no modelo; no entanto, são apagados se o recurso-mãe for eliminado. Para uma descrição completa do comportamento, consulte os modos de implementação do Gestor de [Recursos Do Azure](deployment-modes.md).

Se se implantar em mais de um grupo de [recursos num modelo,](cross-resource-group-deployment.md)os recursos do grupo de recursos especificados na operação de implantação são elegíveis para serem eliminados. Os recursos nos grupos de recursos secundários não são eliminados.

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
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Serviços de Domínio | Sim |
> | Serviços de Domínio / oucontentor | Não |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | fornecedores de suporte | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | adsupportcases | Não |
> | adiciona serviços | Não |
> | agentes | Não |
> | utilizadores anónimos | Não |
> | configuração | Não |
> | registos | Não |
> | relatórios | Não |
> | servicehealthmetrics | Não |
> | services | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | configurações | Não |
> | gerando Recomendações | Não |
> | do IdP | Não |
> | recomendações | Não |
> | supressões | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | açãoRegras | Sim |
> | alerts | Não |
> | alertasList | Não |
> | alertasMetaData | Não |
> | alertasResumo | Não |
> | alertasSummaryList | Não |
> | feedback | Não |
> | smartDetectorAlertRules | Sim |
> | smartDetectorRuntimeEnvironments | Não |
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
> | relatórioFeedback | Não |
> | serviço | Sim |
> | validanome ServiceName | Não |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | configuraçãoLojas | Sim |
> | configuraçãoStores / eventoSRedesFiltros | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Spring | Sim |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | attestationProviders | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | administradores clássicos | Não |
> | dataAliases | Não |
> | negarAtribuições | Não |
> | elevateAccess | Não |
> | encontrarAtribuiçãos DeRoleDeórfãos | Não |
> | fechaduras | Não |
> | permissões | Não |
> | políticasAtribuis | Não |
> | definições políticas | Não |
> | definições políticasSetDefinições | Não |
> | fornecedorOperações | Não |
> | roleAssignments | Não |
> | roleAssignmentsUsageMetrics | Não |
> | definições de papel | Não |

## <a name="microsoftautomation"></a>Microsoft.Automação

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | automatizaçõesContas | Sim |
> | automatizaçãoContas / configurações | Sim |
> | automaçãoContas / empregos | Não |
> | automatizaçãoContas / privateEndpointConnectionProxies | Não |
> | automatizaçõesContas / privateEndpointConnections | Não |
> | automatizaçãoContas / privateLinkResources | Não |
> | automaçãoContas / livros de execução | Sim |
> | automatizaçõesContas / softwareUpdateConfiguras | Não |
> | automatizaçãoContas / webhooks | Não |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | configuraçãoLojas | Sim |
> | configuraçãoStores / eventoSRedesFiltros | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Genebra

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
> | b2cDirectories | Sim |
> | b2ctenants | Não |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | híbridoDataManagers | Sim |
> | postgresInstances | Sim |
> | sqlBigDataClusters | Sim |
> | sqlInstances | Sim |
> | sqlServerRegistrations | Sim |
> | sqlServerRegistrations / sqlServerS | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | registos | Sim |
> | registos / assinaturas de clientes | Não |
> | registos / produtos | Não |
> | verificaçãoKeys | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | lotContas | Sim |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | contas de faturação | Não |
> | contas/acordos | Não |
> | billingAccounts / billingPermissions | Não |
> | contas de faturação / billingPerfis | Não |
> | billingAccounts / billingPerfis / billingPermissions | Não |
> | billingAccounts / billingPerfis / billingRoleAssignments | Não |
> | billingAccounts / billingPerfis / billingRoleDefinitions | Não |
> | contas de faturação / faturaçãoPerfis / faturaçõesAssinaturas | Não |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Não |
> | contas de faturação / faturaçãoPerfis / clientes | Não |
> | contas de faturação / faturaçãoPerfis / instruções | Não |
> | contas de faturação / faturaçãoPerfis / faturas | Não |
> | faturaçãoContas / faturaçãoPerfis / faturas / folha de preço | Não |
> | faturaçãoContas / faturaçãoPerfis / faturas / transações | Não |
> | faturasContas / faturaçãoPerfis / faturasSecs | Não |
> | billingAccounts / faturaçãoPerfis / faturaS / billingPermissions | Não |
> | billingAccounts / faturaçãoPerfis / faturaS / billingRoleAssignments | Não |
> | billingAccounts / faturaçãoPerfis / faturaS / faturaçõesRoleDefinições | Não |
> | faturasContas / faturaçãoPerfis / faturaS / faturaçõesAssinaturas | Não |
> | billingAccounts / faturaçãoPerfis / faturaS / criarBillingRoleAssignment | Não |
> | faturasContas / faturaçãoPerfis / faturaS / iniciarTransferência | Não |
> | faturasContas / faturaçãoPerfis / faturaS / produtos | Não |
> | faturasContas / faturaçãoPerfis / faturaS / produtos / transferência | Não |
> | faturaçãoContas / faturaçãoPerfis / faturaS / produtos / actualizaçõesAutoRenew | Não |
> | faturasContas / faturaçãoPerfis / faturaS /transações | Não |
> | faturasContas / faturaçãoPerfis / faturaS / transferências | Não |
> | contas de faturação / BillingProfiles / patchOperations | Não |
> | contas de faturação / faturaçãoPerfis / pagamentoMétodos | Não |
> | billingAccounts / billingPerfis / políticas | Não |
> | contas de faturação / faturaçãoPerfis / folha de preço | Não |
> | faturaçãoContas / faturaçãoPerfis / folha de preçoDownloadOperations | Não |
> | contas de faturação / faturaçãoPerfis / produtos | Não |
> | contas de faturação / faturaçãoPerfis / transações | Não |
> | billingAccounts / billingRoleAssignments | Não |
> | billingAccounts / faturaçãoRoleDefinis | Não |
> | contas de faturação / assinaturas de faturação | Não |
> | faturasContas / faturaçãoSubscrições / faturas | Não |
> | billingAccounts / criarBillingRoleAssignment | Não |
> | contas de faturação / criaçãoDeOperações deSecção de Faturas | Não |
> | contas / clientes | Não |
> | contas de faturação / clientes / permissões de faturação | Não |
> | faturaçãoContas / clientes / faturaçõesAssinaturas | Não |
> | faturaçãoContas / clientes / iniciarTransferência | Não |
> | faturasContas / clientes / políticas | Não |
> | faturasContas / clientes / produtos | Não |
> | contas / clientes / transações | Não |
> | faturasContas / clientes / transferências | Não |
> | contas/departamentos | Não |
> | contas de faturação /inscriçõesContas | Não |
> | contas de faturação / faturas | Não |
> | contas de faturação / faturasSecs | Não |
> | faturaS / faturaS / faturaçãoSubscriptionMoveOperations | Não |
> | contas de faturação / faturaS / faturaçõesAssinaturas | Não |
> | faturasContas / faturaS / faturaçãoSubscrições / transferência | Não |
> | contas de faturas / faturaS / elevação | Não |
> | faturasContas / faturaS / iniciarTransferência | Não |
> | faturasContas / faturaS / patchOperations | Não |
> | faturasContas / faturaS / produtoMoveOperations | Não |
> | contas de faturas / faturaS / produtos | Não |
> | faturasContas / faturaS / produtos / transferência | Não |
> | faturaS / faturaS / produtos / actualizaçãoAutoRenovar | Não |
> | contas de faturas / faturaS / transações | Não |
> | faturasContas / faturaS / transferências | Não |
> | contas de faturação / linhaOfCredit | Não |
> | contas de faturação / patchOperations | Não |
> | contas de faturação / pagamentoMétodos | Não |
> | contas de faturação / produtos | Não |
> | contas/transações | Não |
> | períodos de faturação | Não |
> | billingPermissions | Não |
> | billingProperty | Não |
> | billingRoleAssignments | Não |
> | billingRoleDefinis | Não |
> | criar BillingRoleAssignment | Não |
> | departamentos | Não |
> | inscriçõesContas | Não |
> | faturas | Não |
> | transferências | Não |
> | transferências / aceitarTransferência | Não |
> | transferências /declínioTransferência | Não |
> | transferências / operaçãoStatus | Não |
> | transferências / validaçãoTransferência | Não |
> | validaendereçoAddress | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | mapApis | Sim |
> | atualizarCommunicationPreference | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | blockchainMembros | Sim |
> | cordaMembros | Sim |
> | observadores | Sim |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | blueprintAssignments | Não |
> | blueprintAssignments / atribuiçãoOperações | Não |
> | blueprintAssignments / operações | Não |
> | plantas | Não |
> | plantas / artefactos | Não |
> | plantas / versões | Não |
> | plantas / versões / artefactos | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | botServiços | Sim |
> | botServices / canais | Não |
> | botServiços / conexões | Não |
> | linguagens | Não |
> | modelos | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Redis | Sim |

## <a name="microsoftcapacity"></a>Microsoft.Capacidade

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicaçõesReservas | Não |
> | autoCotIncrease | Não |
> | calcularTroca | Não |
> | calcularPreço | Não |
> | calcularComprarPreço | Não |
> | catálogos | Não |
> | ordens comerciais Reservas | Não |
> | troca | Não |
> | placeBuyOrder | Não |
> | reservasOrdens | Não |
> | reservasOrdens / calcular Reembolso | Não |
> | reservasOrdens / fusão | Não |
> | reservasEncomendas / reservas | Não |
> | reservasEncomendas / reservas / revisões | Não |
> | reservasOrdens / devolução | Não |
> | reservasOrdens / divisão | Não |
> | reservasOrdens / troca | Não |
> | reservas | Não |
> | recursosFornecedores | Não |
> | resources | Não |
> | validarAOrdem de Reserva | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Não |
> | CdnWebApplicationFirewallPolicies | Sim |
> | talhenos | Não |
> | perfis | Sim |
> | perfis / pontos finais | Sim |
> | perfis / pontos finais / domínios personalizados | Não |
> | perfis / pontos finais / origens | Não |
> | validateProbe | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | certificadoSOrdens | Sim |
> | certificadoS / certificados | Não |
> | validaçãoInformação de Registode Certificados | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | capacidades | Não |
> | domínioNomes | Sim |
> | domínioNomes / capacidades | Não |
> | domínioNames / InternalLoadBalancers | Não |
> | domínioNomes / serviçoCertificados | Não |
> | domínioNomes / slots | Não |
> | domínioNomes / slots / funções | Não |
> | domínioNomes / slots / funções / definições métricas | Não |
> | domínioNomes / slots / papéis / métricas | Não |
> | moverRecursos de Subscrição | Não |
> | operativoSystemFamílias | Não |
> | sistemas operativos | Não |
> | quotas | Não |
> | recursosTipos | Não |
> | validaçãoSubscriÇãoMoveDisponibilidade | Não |
> | máquinas virtuais | Sim |
> | virtualMáquinas / diagnósticoDefinições | Não |
> | virtualMáquinas / definições métricas | Não |
> | máquinas virtuais / métricas | Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | recursos clássicos Infraestruturas | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | capacidades | Não |
> | expressRouteCrossConnections | Não |
> | expressRouteCrossConnections / peerings | Não |
> | gatewayDispositivos suportados | Não |
> | networkSecurityGroups | Sim |
> | quotas | Não |
> | reservadoIps | Sim |
> | redes virtuais | Sim |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Não |
> | redes virtuais / virtualNetworkPeerings | Não |

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
> | armazenamentoContas | Sim |
> | armazenamentoContas / blobServices | Não |
> | armazenamentoContas / ficheirosServiços | Não |
> | armazenamentoContas / definições métricas | Não |
> | armazenamentoContas / métricas | Não |
> | armazenamentoContas / filaServiços | Não |
> | armazenamentoContas / serviços | Não |
> | armazenamentoContas / serviços / diagnósticoDefinições | Não |
> | armazenamentoContas / serviços / definições métricas | Não |
> | armazenamentoContas / serviços / métricas | Não |
> | armazenamentoContas / tabelaServiços | Não |
> | armazenamentoContas / vmImages | Não |
> | vmImages | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.Serviços Cognitivos

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | RateCard | Não |
> | Agregados de Utilização | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | disponibilidadeConjuntos | Sim |
> | diskEncryptionSets | Sim |
> | discos | Sim |
> | galerias | Sim |
> | galerias / aplicações | Não |
> | galerias / aplicações / versões | Não |
> | galerias / imagens | Não |
> | galerias / imagens / versões | Não |
> | grupos de anfitriões | Sim |
> | grupos de anfitriões / anfitriões | Sim |
> | imagens | Sim |
> | proximidadeGrupoS | Sim |
> | restaurarPointCollections | Sim |
> | restaurarPointCollections / restaurarPontos | Não |
> | partilhou VMImages | Sim |
> | vMImages /versões partilhadas | Não |
> | instantâneos | Sim |
> | máquinas virtuais | Sim |
> | virtualMáquinas / extensões | Sim |
> | virtualMáquinas / definições métricas | Não |
> | conjuntos virtualMachineScaleSets | Sim |
> | virtualMachineScaleSets / extensões | Não |
> | virtualMachineScaleSets / NetworkInterfaces | Não |
> | virtualMachineScaleSets / publicIPAddresses | Não |
> | virtualMachineScaleSets / virtualMachines | Não |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Não |

## <a name="microsoftconsumption"></a>Microsoft.Consumo

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
> | Detalhes da reserva | Não |
> | Recomendações de Reservas | Não |
> | ReservasResumos | Não |
> | ReservasTransacções | Não |
> | Etiquetas | Não |
> | inquilinos | Não |
> | Termos | Não |
> | UsageDetails | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | grupos de contentores | Sim |
> | serviçoAssociações | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | registos | Sim |
> | registos / construções | Não |
> | registos / construções / cancelamento | Não |
> | registos / construções / getLogLink | Não |
> | registos / buildTasks | Sim |
> | registos / construçãoTarefas / passos | Não |
> | registos / eventoSGridFilters | Não |
> | registos / geraçõesCredenciais | Não |
> | registos / getBuildSourceUploadUrl | Não |
> | registos / GetCredenciais | Não |
> | registos / importaçãoImagem | Não |
> | registos / privateEndpointConnectionProxies | Não |
> | registos / privateEndpointConnectionProxies / validar | Não |
> | registos / privateEndpointConnections | Não |
> | registos / privateLinkResources | Não |
> | registos / filaConstruir | Não |
> | registos / regeneraçãoCredential | Não |
> | registos / regenerações Credenciais | Não |
> | registos / replicações | Sim |
> | registos / execuções | Não |
> | registos / execuções / cancelar | Não |
> | registos / horárioSRun | Não |
> | registos / scopeMaps | Não |
> | registos / taskRuns | Sim |
> | registos / tarefas | Sim |
> | registos / fichas | Não |
> | registos / atualizaçãoPolíticas | Não |
> | registos / webhooks | Sim |
> | registos / webhooks / getCallbackConfig | Não |
> | registos / webhooks / ping | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | serviços de contentores | Sim |
> | clusters geridos | Sim |
> | openShiftManagedClusters | Sim |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Alertas | Não |
> | Contas de Faturação | Não |
> | Orçamentos | Não |
> | CloudConnectors | Não |
> | Conectores | Sim |
> | Departamentos | Não |
> | Dimensões | Não |
> | Contas de Inscrição | Não |
> | Exportações | Não |
> | Contas de Faturação Externa | Não |
> | Contas /Alertas de Faturação Externa | Não |
> | Contas /Dimensões de Faturação Externa | Não |
> | Contas de Faturação Externa / Previsão | Não |
> | Contas de Faturação Externa / Consulta | Não |
> | Subscrições Externas | Não |
> | Subscrições Externas / Alertas | Não |
> | Subscrições Externas / Dimensões | Não |
> | Subscrições Externas / Previsão | Não |
> | Subscrições Externas / Consulta | Não |
> | Previsão | Não |
> | Consulta | Não |
> | registar | Não |
> | Reportconfigs | Não |
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
> | recursosFornecedores | Sim |

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
> | espaços de trabalho / armazenamentoEncriptação | Não |
> | espaços de trabalho / virtualNetworkPeerings | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | catálogos | Sim |
> | catálogos de dados | Sim |
> | datacatalogs /datasources | Não |
> | datacatalogs / datasources / digitalizações | Não |
> | datacatalogs / datasources / digitalizações / conjuntos de dados | Não |
> | datacatalogs / datasources / digitalizações / gatilhos | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dataFábricas | Sim |
> | dataFactories / diagnósticoDefinições | Não |
> | dataFábricas / definições métricas | Não |
> | dataFactorySchema | Não |
> | fábricas | Sim |
> | fábricas / integraçãoTempos | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas / dadosLakeStoreAccounts | Não |
> | contas / armazenamentoContas | Não |
> | contas / armazenamentoContas / contentores | Não |
> | contas / transferênciaAnalyticsUnits | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas / eventoSGridFilters | Não |
> | contas / firewallRules | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | services | Sim |
> | serviços / projetos | Sim |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas / ações | Não |
> | contas / ações / conjuntos de dados | Não |
> | contas / ações / convites | Não |
> | contas / ações / assinaturas de partilhas de fornecedores | Não |
> | contas / ações / sincronizaçãoDefinições | Não |
> | contas / subscrições de ações | Não |
> | contas / subscrições de ações / consumerSourceDataSets | Não |
> | contas / subscrições de ações / datasetmappings | Não |
> | contas / subscrições de ações / gatilhos | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | servidores | Sim |
> | servidores /conselheiros | Não |
> | servidores / teclas | Não |
> | servidores / privateEndpointConnectionProxies | Não |
> | servidores / privateEndpointConnections | Não |
> | servidores / privateLinkResources | Não |
> | servidores / consultaTextos | Não |
> | servidores / Servidores recuperáveis | Não |
> | servidores / topQueryStatistics | Não |
> | servidores / virtualNetworkRules | Não |
> | servidores / estatísticas de espera | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | servidores | Sim |
> | servidores /conselheiros | Não |
> | servidores / teclas | Não |
> | servidores / privateEndpointConnectionProxies | Não |
> | servidores / privateEndpointConnections | Não |
> | servidores / privateLinkResources | Não |
> | servidores / consultaTextos | Não |
> | servidores / Servidores recuperáveis | Não |
> | servidores / topQueryStatistics | Não |
> | servidores / virtualNetworkRules | Não |
> | servidores / estatísticas de espera | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | servidorGrupos | Sim |
> | servidores | Sim |
> | servidores /conselheiros | Não |
> | servidores / teclas | Não |
> | servidores / privateEndpointConnectionProxies | Não |
> | servidores / privateEndpointConnections | Não |
> | servidores / privateLinkResources | Não |
> | servidores / consultaTextos | Não |
> | servidores / Servidores recuperáveis | Não |
> | servidores / topQueryStatistics | Não |
> | servidores / virtualNetworkRules | Não |
> | servidores / estatísticas de espera | Não |
> | servidorsv2 | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | artefactoFonteS | Sim |
> | lançamentos | Sim |
> | serviçoTopos de tempo | Sim |
> | serviçoSTopologia / serviços | Sim |
> | serviçoTopos / serviços / serviçoSUnidades | Sim |
> | passos | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | grupos de aplicações | Sim |
> | grupos de aplicações/aplicações | Não |
> | grupos de aplicações / desktops | Não |
> | grupos de aplicações / startmenuitems | Não |
> | piscinas de acolhimento | Sim |
> | hostpools / anfitriões de sessões | Não |
> | hostpools / sessões de sessões / usersessions | Não |
> | hostpools / usersessions | Não |
> | áreas de trabalho | Sim |

## <a name="microsoftdevices"></a>Microsoft.Dispositivos

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Piscinas Elásticas | Sim |
> | ElasticPools / IotHubTenants | Sim |
> | ElasticPools / IotHubTenants / securitySettings | Não |
> | IotHubs | Sim |
> | IotHubs / eventoSGridFilters | Não |
> | IotHubs / definições de segurança | Não |
> | Serviços de Provisionamento | Sim |
> | utilizações | Não |

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
> | laboratórios / máquinas virtuais | Sim |
> | horários | Sim |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | base de dadosNomes de contas | Não |
> | bases de dadosContas | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | domínios | Sim |
> | domínios / domainOwnershipIdentifiers | Não |
> | geraSsoRequest | Não |
> | topLevelDomains | Não |
> | validarInformações deRegistodeDomínio | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | projetos de lcsprojects | Não |
> | lcsprojects / clouddeployments | Não |
> | lcsprojects / conectores | Não |

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
> | eventoSSubscrições | Não |
> | extensõesTópicos | Não |
> | partnerNamespaces | Sim |
> | partnerNamespaces / eventoChannels | Não |
> | parceiroSRegistrations | Sim |
> | parceiroTópicos | Sim |
> | partnerTopics / eventoSSubscrições | Não |
> | sistemaTópicos | Sim |
> | sistemaTópicos / eventosSubscrições | Não |
> | tópicos | Sim |
> | tópicoTipos | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Sim |
> | espaços de nome | Sim |
> | espaços de nomes / regras de autorização | Não |
> | espaços de nomes / desastres recoveryconfigs | Não |
> | espaços de nomes / centros de eventos | Não |
> | espaços de nome / eventhubs / regras de autorização | Não |
> | espaços de nome / eventhubs / grupos de consumidores | Não |
> | espaços de nome / conjuntos de regras de rede | Não |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | funcionalidades | Não |
> | fornecedores | Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | inscrever-se | Não |
> | galeriaitas | Não |
> | gerarartifactaccessuri | Não |
> | myareas | Não |
> | myares / áreas | Não |
> | myares / áreas / áreas | Não |
> | myares / áreas / áreas / galeriaites | Não |
> | myareas / áreas / galleryitems | Não |
> | myareas / galleryitems | Não |
> | registar | Não |
> | resources | Não |
> | recuperrecursoside | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | autoGeridosAccounts | Sim |
> | autoGeridoVmConfigurationPerfis | Sim |
> | configuraçãoPerfilAssignments | Não |
> | guestConfigurationAssignments | Não |
> | software | Não |
> | softwareUpdateProfile | Não |
> | softwareUpdates | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | hanaInstances | Sim |
> | sapMonitors | Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | hsms dedicados | Sim |

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

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | máquinas | Sim |
> | máquinas / extensões | Sim |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | gestores de dados | Sim |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | componentes | Sim |
> | redeScopes | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | empregos | Sim |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnósticoDefinições | Não |
> | diagnósticoDefiniçõesCategorias | Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | modelos de aplicativos | Não |
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
> | apagouOsCofres | Não |
> | hsmPools | Sim |
> | cofres | Sim |
> | cofres / accessPolicies | Não |
> | cofres / eventoSGridFilters | Não |
> | cofres / segredos | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Sim |
> | clusters / configurações de bases de dados anexadas | Não |
> | clusters / bases de dados | Não |
> | clusters / bases de dados / ligações de dados | Não |
> | clusters / bases de dados / eventhubconnections | Não |
> | clusters / bases de dados / principais atribuições | Não |
> | clusters / principados | Não |
> | clusters / identidades partilhadas | Não |

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
> | hospedagemAmbientes | Sim |
> | integraçõesContas | Sim |
> | integraçãoServiçosAmbientes | Sim |
> | integraçãoServiceEnvironments / managedApis | Sim |
> | ambientes isolados | Sim |
> | fluxos de trabalho | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | compromissoPlanos | Sim |
> | webServices | Sim |
> | Áreas de Trabalho | Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | áreas de trabalho | Sim |
> | espaços de trabalho / computação | Não |
> | espaços de trabalho / eventoSGridFilters | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Identidades | Não |
> | userAssignedIdentidades | Sim |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinições | Não |
> | inscriçõesAtribuições | Não |
> | inscriçõesDefinições | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | obter Entidades | Não |
> | grupos de gestão | Não |
> | grupos de gestão / configurações | Não |
> | resources | Não |
> | startTenantBackfill | Não |
> | inquilinoBackfillStatus | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas / eventoSGridFilters | Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | ofertas | Não |
> | ofertaTipos | Não |
> | ofertaTipos / editores | Não |
> | ofertaTipos / editores / ofertas | Não |
> | ofertaTipos / editores / ofertas / planos | Não |
> | ofertaTipos / editores / ofertas / planos / acordos | Não |
> | ofertaTipos / editores / ofertas / planos / configs | Não |
> | ofertaTipos / editores / ofertas / planos / configs / importImage | Não |
> | privategalleryitems | Não |
> | privateStoreClient | Não |
> | produtos | Não |
> | editores | Não |
> | editoras / ofertas | Não |
> | editores / ofertas / alterações | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | clássicoDevServices | Sim |
> | atualizarCommunicationPreference | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | acordos | Não |
> | ofertas | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | serviços de media | Sim |
> | mediaservices / contaFilters | Não |
> | serviços de media / ativos | Não |
> | mediaservices / ativos / activosFilters | Não |
> | mediaservices / contentKeyPolicies | Não |
> | mediaservices / eventoSGridFilters | Não |
> | mediaservices / liveEventOperations | Não |
> | mediaservices / liveEvents | Sim |
> | mediaservices / liveEvents / liveOutputs | Não |
> | mediaservices / liveOutputOperations | Não |
> | mediaservices / mediaGraphs | Não |
> | mediaservices / streamingEndpointOperations | Não |
> | mediaservices / streamingPontos de finalidade | Sim |
> | mediaservices / streamingLocators | Não |
> | mediaservices / streamingPolíticas | Não |
> | mediaservices / transforma | Não |
> | mediaservices / transforma /empregos | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | appClusters | Sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | avaliaçãoProjetos | Sim |
> | projetos migratórios | Sim |
> | moveCollections | Sim |
> | projetos | Sim |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | holográficosBroadcastAccounts | Sim |
> | objetoUnderstandingAccounts | Sim |
> | remotoSRenderingAccounts | Sim |
> | spatialAnchorsAccounts | Sim |
> | surfaceReconstructionAccounts | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | netAppAccounts | Sim |
> | netAppAccounts / capacityPools | Sim |
> | netAppAccounts / capacityPools / volumes | Sim |
> | netAppAccounts / capacityPools / volumes / mountTargets | Sim |
> | netAppAccounts / capacityPools / volumes / instantâneos | Sim |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | NotebookProxies | Não |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicaçõesGateways | Sim |
> | aplicaçãoGatewayWebApplicationFirewallPolicies | Sim |
> | aplicaçõesGrupos de Segurança | Sim |
> | azureFirewallFqdnTags | Não |
> | azureFirewalls | Sim |
> | bastionHosts | Sim |
> | bgpServiceComunidades | Não |
> | conexões | Sim |
> | ddosCustomPolicies | Sim |
> | ddosProtectionPlans | Sim |
> | dnsOperationStatuses | Não |
> | zonas de dnszone | Sim |
> | zonas de dnszones / A | Não |
> | dnszones / AAAA | Não |
> | zonas de dnszones / todos | Não |
> | dnszones / CAA | Não |
> | dnszones / CNAME | Não |
> | dnszones / MX | Não |
> | dnszones / NS | Não |
> | dnszones / PTR | Não |
> | dnszones / conjuntos de recordes | Não |
> | dnszones / SOA | Não |
> | dnszones / SRV | Não |
> | dnszones / TXT | Não |
> | expressRouteCircuits | Sim |
> | expressRouteCrossConnections | Sim |
> | expressRouteGateways | Sim |
> | expressRoutePorts | Sim |
> | expressRouteServiceProviders | Não |
> | firewallPolíticas | Sim |
> | portas da frente | Sim |
> | frontdoorWebApplicationFirewallManagedRuleSets | Não |
> | frontdoorWebApplicationFirewallPolicies | Sim |
> | obterDnsResourceReference | Não |
> | internalNotifica | Não |
> | loadBalancers | Sim |
> | networkgateways locais | Sim |
> | natGateways | Sim |
> | políticas de intenções de rede | Sim |
> | networkInterfaces | Sim |
> | perfis de rede | Sim |
> | networkSecurityGroups | Sim |
> | networkWatchers | Sim |
> | networkWatchers / connectionMonitors | Sim |
> | networkWatchers / lentes | Sim |
> | networkWatchers / pingMeshes | Sim |
> | p2sVpnGateways | Sim |
> | privateDnsOperationStatuses | Não |
> | privateDnsZones | Sim |
> | privateDnsZones / A | Não |
> | privateDnsZones / AAAAA | Não |
> | privateDnsZones / todos | Não |
> | privateDnsZones / CNAME | Não |
> | privateDnsZones / MX | Não |
> | privateDnsZones / PTR | Não |
> | privateDnsZones / SOA | Não |
> | privateDnsZones / SRV | Não |
> | privateDnsZones / TXT | Não |
> | privateDnsZones / virtualNetworkLinks | Sim |
> | privateEndpoints | Sim |
> | privateLinkServices | Sim |
> | endereços públicosIPAddresss | Sim |
> | publicipPrefixes | Sim |
> | rotaFiltros | Sim |
> | routeTables | Sim |
> | serviceEndpointPolicies | Sim |
> | tráfegoManagerGeographicHierarchies | Não |
> | perfis de gestor de tráfego | Sim |
> | perfis de gestor de tráfego / heatMaps | Não |
> | tráfegoManagerUserMetricsKeys | Não |
> | virtualHubs | Sim |
> | virtualNetworkGateways | Sim |
> | redes virtuais | Sim |
> | virtualNetworkTaps | Sim |
> | virtualWans | Sim |
> | vpnGateways | Sim |
> | vpnSites | Sim |
> | webApplicationFirewallPolicies | Sim |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | espaços de nome | Sim |
> | espaços de nome / notificationHubs | Sim |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | espaços osName | Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Hipervsites | Sim |
> | Sites de Importação | Sim |
> | Sites de servidores | Sim |
> | Sites VMware | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aglomerados | Sim |
> | linkTargets | Não |
> | armazenamentoInsightConfigs | Não |
> | áreas de trabalho | Sim |
> | espaços de trabalho / dadosExportações | Não |
> | espaços de trabalho / dataFontes | Não |
> | espaços de trabalho / serviços ligados | Não |
> | espaços de trabalho / privateEndpointConnectionProxies | Não |
> | espaços de trabalho / privateEndpointConnections | Não |
> | espaços de trabalho / privateLinkResources | Não |
> | espaços de trabalho / consulta | Não |
> | espaços de trabalho / PrivateLinkProxies com âmbito | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | associações de gestão | Não |
> | configurações de gestão | Sim |
> | soluções | Sim |
> | Modos de exibição | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | legacyPeerings | Não |
> | peerAsns | Não |
> | os pares | Sim |
> | peeringServiceCountries | Não |
> | peeringServiceProviders | Não |
> | peeringServices | Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | políticaEventos | Não |
> | políticaMetametada | Não |
> | políticasEstados | Não |
> | policyTrackedResources | Não |
> | reparações | Não |

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

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | backupProtectedItems | Não |
> | cofres | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | espaços de nome | Sim |
> | espaços de nomes / regras de autorização | Não |
> | espaços de nome / ligações híbridas | Não |
> | espaços de nome / ligações híbridas / regras de autorização | Não |
> | espaços de nome / wcfrelays | Não |
> | espaços de nome / wcfrelays / regras de autorização | Não |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Não |
> | coleções | Sim |
> | coleções/aplicações | Não |
> | coleções /diretores de segurança | Não |
> | modeloImages | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | consultas | Sim |
> | recursosChangeDetails | Não |
> | recursosAlterações | Não |
> | resources | Não |
> | recursosHistória | Não |
> | subscriçõesStatus | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | disponibilidadeStatuses | Não |
> | criançaDisponibilidadeStatuses | Não |
> | recursos infantis | Não |
> | questões emergentes | Não |
> | eventos | Não |
> | recursos impactados | Não |
> | do IdP | Não |
> | notificações | Não |

## <a name="microsoftresources"></a>Microsoft.Recursos

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | implementações | Não |
> | destacamentos /operações | Não |
> | implementaçãoScripts | Sim |
> | implementaçãoScripts / registos | Não |
> | ligações | Não |
> | notificarResourceJobs | Não |
> | fornecedores | Não |
> | recursosGrupos | Não |
> | assinaturas | Não |
> | inquilinos | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Sim |
> | recursos saasrecursos | Não |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | recursosHealthMetadata | Não |
> | serviços de pesquisa | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | adaptaçõesNetworkHardenings | Não |
> | advancedThreatProtectionSettings | Não |
> | alerts | Não |
> | conexões permitidas | Não |
> | aplicaçõesWhitelistings | Não |
> | avaliaçãoMetas | Não |
> | avaliações | Não |
> | autoDismissAlertsRules | Não |
> | automações | Sim |
> | Definições de AutoProvisionamento | Não |
> | Conformidades | Não |
> | dataCollectionAgents | Não |
> | dispositivoGrupos de Segurança | Não |
> | descobertaSSegurançaS | Não |
> | externalSecuritySolutions | Não |
> | Políticas de Proteção de Informação | Não |
> | iotSecuritySolutions | Sim |
> | iotSecuritySolutions / analyticsModels | Não |
> | iotSecuritySolutions / analyticsModels / agregadAlerts | Não |
> | iotSecuritySolutions / analyticsModels / recomendações agregadas | Não |
> | jitNetworkAccessPolicies | Não |
> | redeData | Não |
> | políticas | Não |
> | preços | Não |
> | regulamentareComplianceStandards | Não |
> | regulamentareComplianceStandards / regulatoryComplianceControls | Não |
> | regulamentareComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Não |
> | segurançaContactos | Não |
> | segurançaSoluções | Não |
> | securitySolutionsReferenceData | Não |
> | segurançaEstatutos | Não |
> | segurançaStatusesResumos | Não |
> | servidorVulnerabilidadeAvalias | Não |
> | settings | Não |
> | subAvaliações | Não |
> | tarefas | Não |
> | topologies | Não |
> | workspaceDefinições | Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnósticoDefinições | Não |
> | diagnósticoDefiniçõesCategorias | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | agregações | Não |
> | alertaRegras | Não |
> | modelos de regras de alerta | Não |
> | marcadores | Não |
> | casos | Não |
> | dataCotes | Não |
> | dataConnectorsCheckRequisitos | Não |
> | entidades | Não |
> | entidadeQueries | Não |
> | incidentes | Não |
> | officeConsents | Não |
> | settings | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | espaços de nome | Sim |
> | espaços de nomes / regras de autorização | Não |
> | espaços de nomes / desastres recoveryconfigs | Não |
> | espaços de nome / filtros de eventgrid | Não |
> | espaços de nome / conjuntos de regras de rede | Não |
> | espaços de nome / filas | Não |
> | espaços de nome / filas / regras de autorização | Não |
> | espaços de nome / tópicos | Não |
> | espaços de nome / tópicos / regras de autorização | Não |
> | espaços de nome / tópicos / subscrições | Não |
> | espaços de nome / tópicos / subscrições / regras | Não |
> | premiumMensagens de mensagens | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Sim |
> | aglomerados | Sim |
> | clusters / aplicações | Não |
> | grupos de contentores | Sim |
> | conjuntos de grupos de contentores | Sim |
> | aglomerados de bordas | Sim |
> | aglomerados de bordas / aplicações | Não |
> | clusters geridos | Sim |
> | clusters geridos / nós | Não |
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
> | gateways | Sim |
> | redes | Sim |
> | segredos | Sim |
> | volumes | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | fornecedorRegistos | Não |
> | fornecedorRegistrations / recursosTypeRegistrations | Não |
> | lançamentos | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SignalR | Sim |
> | SignalR / eventoSFiltros GridFilters | Não |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SiteRecoveryVault | Sim |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | híbridoUseBenefits | Não |

## <a name="microsoftsolutions"></a>Microsoft.Soluções

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicaçõesDefinições | Sim |
> | aplicações | Sim |
> | jitRequests | Sim |

## <a name="microsoftspoolservice"></a>Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | assinaturas registadas | Não |
> | bobinas | Sim |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | casos geridos | Sim |
> | managedCasos / bases de dados | Sim |
> | managedCasos / bases de dados / backupShortTermRetentionPolicies | Não |
> | managedCasos / bases de dados / schemas / tabelas / colunas / sensibilidadeEtiquetas | Não |
> | managedCasos / bases de dados / vulnerabilidadeAvaliações | Não |
> | managedCasos / bases de dados / vulnerabilidadeAvaliações / regras / linhas de base | Não |
> | managedInstances / encriptaçãoProtector | Não |
> | managedInstances / teclas | Não |
> | managedInstances / restorableDropDatabases / backupShortTermRetentionPolicies | Não |
> | managedCasos / vulnerabilidadeAvaliações | Não |
> | servidores | Sim |
> | servidores / administradores | Não |
> | servidores / comunicaçõesLinks | Não |
> | servidores / bases de dados | Sim |
> | servidores / encriptaçãoProtector | Não |
> | servidores / firewallRules | Não |
> | servidores / teclas | Não |
> | servidores / RestorableDropDatabases | Não |
> | servidores / objetivos de serviço | Não |
> | servidores / tdeCertificados | Não |
> | clusters virtuais | Não |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Sim |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Não |
> | Máquinas SqlVirtual | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | armazenamentoContas | Sim |
> | armazenamentoContas / blobServices | Não |
> | armazenamentoContas / ficheirosServiços | Não |
> | armazenamentoContas / filaServiços | Não |
> | armazenamentoContas / serviços | Não |
> | armazenamentoContas / serviços / definições métricas | Não |
> | armazenamentoContas / tabelaServiços | Não |
> | utilizações | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | caches | Sim |
> | caches / armazenamentoAlvos | Não |
> | utilizaçãoModelos | Não |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | grupos de replicação | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | armazenamentoSyncServices | Sim |
> | storageSyncServices / RegisteredServers | Não |
> | armazenamentoSyncServices / syncGroups | Não |
> | armazenamentoSyncServices / syncGroups / cloudEndpoints | Não |
> | armazenamentoSyncServices / syncGroups / serverEndpoints | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | armazenamentoSyncServices | Sim |
> | storageSyncServices / RegisteredServers | Não |
> | armazenamentoSyncServices / syncGroups | Não |
> | armazenamentoSyncServices / syncGroups / cloudEndpoints | Não |
> | armazenamentoSyncServices / syncGroups / serverEndpoints | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | armazenamentoSyncServices | Sim |
> | storageSyncServices / RegisteredServers | Não |
> | armazenamentoSyncServices / syncGroups | Não |
> | armazenamentoSyncServices / syncGroups / cloudEndpoints | Não |
> | armazenamentoSyncServices / syncGroups / serverEndpoints | Não |
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
> | streamingjobs | Sim |

## <a name="microsoftsubscription"></a>Microsoft.Subscrição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | cancelar | Não |
> | Criar Subscrição | Não |
> | ativar | Não |
> | mudar o nome | Não |
> | Definições de Assinaturas | Não |
> | Operações de Subscrição | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | ambientes | Sim |
> | ambientes / accessPolicies | Não |
> | ambientes / fontes de eventos | Sim |
> | ambientes / referênciaDataSets | Sim |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dedicadoSCloudNodes | Sim |
> | dedicadoSServiços Cloud | Sim |
> | máquinas virtuais | Sim |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dispositivos | Sim |
> | assinaturas registadas | Não |
> | fornecedores | Não |
> | fornecedores / skus | Não |
> | fornecedores / vnfs | Não |
> | vnfs | Sim |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | apiGeGeAccounts | Não |
> | apiGeGeAccounts / apiAcls | Não |
> | apiGeGeAccounts / apis | Não |
> | apiGeGeAccounts / apis / apiAcls | Não |
> | apiGeGeContas / apis / conexõesAcls | Não |
> | apiGeGeContas / apis / conexões | Não |
> | apiGeGeContas / apis / conexões / conexõesAcls | Não |
> | apiGegeContas / apis / definições localizadas | Não |
> | apiGeGeContas / conexõesAcls | Não |
> | apiGeGeContas / conexões | Não |
> | contadores de faturação | Não |
> | certificados | Sim |
> | conexãoGateways | Sim |
> | conexões | Sim |
> | customApis | Sim |
> | sites eliminados | Não |
> | hospedagemAmbientes | Sim |
> | hospedagemAmbientes / eventoSGridFilters | Não |
> | hospedagemAmbientes / multiRolePools | Não |
> | hospedagemAmbientes / trabalhadorEsPools | Não |
> | publicarUtilizadores | Não |
> | recomendações | Não |
> | recursosHealthMetadata | Não |
> | tempos de execução | Não |
> | servidorFarms | Sim |
> | servidorFarms / eventoSGridFilters | Não |
> | sites | Sim |
> | sites/config  | Não |
> | sites / eventoSGridFilters | Não |
> | sites / hostNameBindings | Não |
> | sites / redeConfig | Não |
> | sites / premieraddons | Sim |
> | sites / slots | Sim |
> | sites / slots / eventoSGridFilters | Não |
> | sites / slots / hostNameBindings | Não |
> | sites / slots / redeConfig | Não |
> | sourceControls | Não |
> | sites estáticas | Sim |
> | validar | Não |
> | verificarHostingEnvironmentVnet | Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnósticoDefinições | Não |
> | diagnósticoDefiniçõesCategorias | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Serviços de Dispositivos | Sim |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | componentes | Não |
> | componentesSSumário | Não |
> | monitorInstâncias | Não |
> | monitorInstancesSummary | Não |
> | monitores | Não |
> | notificaçãoDefinições | Não |

## <a name="next-steps"></a>Passos seguintes

Para obter os mesmos dados que um ficheiro de valores separados pela vírem, baixe a [eliminação completa do modo-modo-eliminação.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
