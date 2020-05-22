---
title: Mover suporte de operação por tipo de recurso
description: Lista os tipos de recursos Azure que podem ser transferidos para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 4f2ed7f1cb24b9896b533fb5d18ac4e57db48e2c
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780332"
---
# <a name="move-operation-support-for-resources"></a>Suporte da operação de movimentação para recursos

Este artigo enumera se um tipo de recurso Azure suporta a operação de movimento. Também fornece informações sobre condições especiais a considerar ao mover um recurso.

Salte para um espaço de nome do fornecedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automação](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Batchai](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacidade](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.Cognição](#microsoftcognition)
> - [Microsoft.Serviços Cognitivos](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumo](#microsoftconsumption)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CostManagementExports](#microsoftcostmanagementexports)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.dataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
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
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentação](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
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
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguração](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperacionalização](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Manutenção](#microsoftmaintenance)
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
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Recursos](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Soluções](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscrição](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de domínio | Não | Não |
> | serviços de domínio / oucontentor | Não | Não |
> | locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | privatelinkforazuread | Não | Não |
> | inquilinos | Não | Não |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | resultados de operação | Não | Não |
> | operações | Não | Não |
> | fornecedores de apoio | Não | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | adsupportcases | Não | Não |
> | adiciona serviços | Não | Não |
> | agentes | Não | Não |
> | utilizadores anónimos | Não | Não |
> | configuração | Não | Não |
> | registos | Não | Não |
> | operações | Não | Não |
> | relatórios | Não | Não |
> | servicehealthmetrics | Não | Não |
> | services | Não | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | configurações | Não | Não |
> | gerarecomendações | Não | Não |
> | do IdP | Não | Não |
> | operações | Não | Não |
> | recomendações | Não | Não |
> | supressões | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | regras de ação | Sim | Sim |
> | alerts | Não | Não |
> | alerta | Não | Não |
> | alertasmetametametas | Não | Não |
> | alerta | Não | Não |
> | alertassummarylist | Não | Não |
> | operações | Não | Não |
> | smartdetectoralertrules | Sim | Sim |
> | grupos inteligentes | Não | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | locais | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / estados de funcionamento | Não | Não |
> | operações | Não | Não |
> | servidores | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checkfeedback necessário | Não | Não |
> | checknamedisponibilidade | Não | Não |
> | checkservicenamedisponibilidade | Não | Não |
> | operações | Não | Não |
> | relatório feedback | Não | Não |
> | serviço | Sim | Sim |
> | validando o nome de serviço | Não | Não |

> [!IMPORTANT]
> Um serviço de Gestão API que está definido para o SKU de Consumo não pode ser movido.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | lojas de configuração | Sim | Sim |
> | lojas de configuração / filtros de eventgrid | Não | Não |
> | locais | Não | Não |
> | locais / estado de operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | locais | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / estado de funcionamento | Não | Não |
> | operações | Não | Não |
> | primavera | Sim | Sim |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | apiapps | Não | Não |
> | appidentities | Não | Não |
> | gateways | Não | Não |

> [!IMPORTANT]
> Consulte [a orientação de movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | fornecedores de attestations | Sim | Sim |
> | operações | Não | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | check-access | Não | Não |
> | administradores clássicos | Não | Não |
> | dataaliases | Não | Não |
> | negar atribuições | Não | Não |
> | elevar o acesso | Não | Não |
> | encontrar tarefas de rolemóórfão | Não | Não |
> | fechaduras | Não | Não |
> | operações | Não | Não |
> | permissões | Não | Não |
> | atribuições políticas | Não | Não |
> | definições políticas | Não | Não |
> | definições políticas | Não | Não |
> | operações de prestadores | Não | Não |
> | roleassignments | Não | Não |
> | roleassignmentsusagemetrics | Não | Não |
> | definições de roledefinitions | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automação

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de automação | Sim | Sim |
> | contas/configurações de automação | Sim | Sim |
> | contas de automação / empregos | Não | Não |
> | contas de automação / privateendpointconnectionproxies | Não | Não |
> | contas de automação / privateendpointconnections | Não | Não |
> | contas de automação / privatelinkresources | Não | Não |
> | contas de automação / livros de execução | Sim | Sim |
> | automatizações de contas / configurações de atualizações de software | Não | Não |
> | contas de automação / webhooks | Não | Não |
> | operações | Não | Não |

> [!IMPORTANT]
> Os livros de execução devem existir no mesmo grupo de recursos que a Conta de Automação.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | locais | Não | Não |
> | localizações /checkcotdisponibilidade | Não | Não |
> | operações | Não | Não |
> | nuvens privadas | Sim | Sim |
> | nuvens privadas / aglomerados | Não | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | b2cdirecties | Sim | Sim |
> | b2ctenants | Não | Não |
> | checknamedisponibilidade | Não | Não |
> | operações | Não | Não |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | controladores de dados | Não | Não |
> | gestores de dados híbridos | Não | Não |
> | operações | Não | Não |
> | postgresinstances | Não | Não |
> | sqlinstâncias | Não | Não |
> | sqlmanagedinstâncias | Não | Não |
> | casos de sqlserver | Não | Não |
> | registos sqlserver | Sim | Sim |
> | registos sqlserver / sqlserverservers | Não | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cloudmanifestofiles | Não | Não |
> | operações | Não | Não |
> | registos | Sim | Sim |
> | registos / assinaturas de clientes | Não | Não |
> | registos / produtos | Não | Não |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Não | Não |
> | operações | Não | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de lote | Sim | Sim |
> | locais | Não | Não |
> | localizações /resultados de operações de conta | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | localizações / quotas | Não | Não |
> | operações | Não | Não |

## <a name="microsoftbatchai"></a>Microsoft.Batchai

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Não | Não |
> | servidores de ficheiros | Não | Não |
> | empregos | Não | Não |
> | operações | Não | Não |
> | áreas de trabalho | Não | Não |
> | espaços de trabalho / aglomerados | Não | Não |
> | espaços de trabalho / experiências | Não | Não |
> | espaços de trabalho / experiências / empregos | Não | Não |
> | espaços de trabalho / fileservers | Não | Não |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de faturação | Não | Não |
> | contas/acordos de faturação | Não | Não |
> | contas de faturação / permissões de faturação | Não | Não |
> | contas de faturação / perfis de faturação | Não | Não |
> | contas de faturação / perfis de faturação / saldo disponível | Não | Não |
> | contas de faturação / perfis de faturação / permissões de faturação | Não | Não |
> | contas de faturação / perfis de faturação / atribuição de faturações | Não | Não |
> | contas de faturação / perfis de faturação / definições de billingrole | Não | Não |
> | contas de faturação / perfis de faturação / assinaturas de faturação | Não | Não |
> | contas de faturação / perfis de faturação / atribuição de roleta de faturação | Não | Não |
> | contas de faturação / perfis de faturação / clientes | Não | Não |
> | contas de faturação / perfis de faturação / instruções | Não | Não |
> | contas de faturação / perfis de faturação / faturas | Não | Não |
> | contas de faturação / perfis de faturação / faturas / folha de preço | Não | Não |
> | contas de faturação / perfis de faturação / faturas / transações | Não | Não |
> | contas de faturação / perfis de faturação / secções de faturas | Não | Não |
> | contas de faturação / perfis de faturação / secções de faturas / permissões de faturação | Não | Não |
> | contas de faturação / perfis de faturação / secções de faturas / billingroleassignments | Não | Não |
> | contas de faturação / perfis de faturação / secções de faturas / definições de billingrole | Não | Não |
> | contas de faturação / perfis de faturação / secções de faturas / assinaturas de faturas | Não | Não |
> | contas de faturação / perfis de faturação / secções de faturas / atribuição de roleta de faturação | Não | Não |
> | contas de faturação / perfis de faturação / secções de faturas / transferência iniciada | Não | Não |
> | contas de faturação / perfis de faturação / secções de faturas / produtos | Não | Não |
> | contas de faturação / perfis de faturação / secções de faturas / produtos / transferência | Não | Não |
> | contas de faturação / perfis de faturação / secções de faturas / produtos / atualização automática | Não | Não |
> | contas de faturação / perfis de faturação / secções de faturas / transações | Não | Não |
> | contas de faturação / perfis de faturação / secções de faturas / transferências | Não | Não |
> | contas de faturação / perfis de faturação / patchoperations | Não | Não |
> | contas de faturação / perfis de faturação / métodos de pagamento | Não | Não |
> | contas de faturação / perfis de faturação / políticas | Não | Não |
> | contas de faturação / perfis de faturação / folha de preço | Não | Não |
> | contas de faturação / perfis de faturação / operações de descarregamento de folhas de preço | Não | Não |
> | contas de faturação / perfis de faturação / produtos | Não | Não |
> | contas de faturação / perfis de faturação / transações | Não | Não |
> | contas de faturação / atribuição de faturações | Não | Não |
> | contas de faturação / definições de billingrole | Não | Não |
> | contas de faturação / assinaturas de faturação | Não | Não |
> | contas de faturação / assinaturas de faturação / faturas | Não | Não |
> | contas de faturação / atribuição de roleta de faturação | Não | Não |
> | contas de faturação / operações de criação de secções de voz | Não | Não |
> | contas de faturação / clientes | Não | Não |
> | contas de faturação / clientes / permissões de faturação | Não | Não |
> | contas de faturação / clientes / assinaturas de faturação | Não | Não |
> | contas de faturação / clientes / transferência iniciada | Não | Não |
> | contas de faturação / clientes / políticas | Não | Não |
> | contas de faturação / clientes / produtos | Não | Não |
> | contas de faturação / clientes / transações | Não | Não |
> | contas de faturação / clientes / transferências | Não | Não |
> | contas de faturação / departamentos | Não | Não |
> | contas de faturação/contas de matrículas | Não | Não |
> | contas de faturação / faturas | Não | Não |
> | contas de faturação / secções de faturas | Não | Não |
> | contas de faturação / secções de faturas / operações de billingsubscriptionmove | Não | Não |
> | contas de faturação / faturas / assinaturas de faturação | Não | Não |
> | contas de faturação / faturas / assinaturas de faturação / transferência | Não | Não |
> | contas de faturação / secções de faturas / elevação | Não | Não |
> | contas de faturação / secções de faturas / transferência iniciada | Não | Não |
> | contas de faturação / secções de faturas / patchoperations | Não | Não |
> | contas de faturação / secções de faturas / operações de movimento de produtos | Não | Não |
> | contas de faturação / secções de faturas / produtos | Não | Não |
> | contas de faturação / faturas / produtos / transferência | Não | Não |
> | contas de faturação / faturas / produtos / atualização automática | Não | Não |
> | contas de faturação / secções de faturas / resultados de transferências de produtos | Não | Não |
> | contas de faturação / faturas / transações | Não | Não |
> | contas de faturação / secções de faturas / transferências | Não | Não |
> | contas de faturação / lineofcredit | Não | Não |
> | contas de faturação / listinvoicesectionswithcreatesubscriptionpermission | Não | Não |
> | contas de faturação / resultados de operações | Não | Não |
> | contas de faturação / patchoperations | Não | Não |
> | contas de faturação / métodos de pagamento | Não | Não |
> | contas de faturação / produtos | Não | Não |
> | contas de faturação /transações | Não | Não |
> | períodos de faturação | Não | Não |
> | permissões de faturação | Não | Não |
> | propriedade de faturação | Não | Não |
> | atribuição de faturações | Não | Não |
> | definições de billingrole | Não | Não |
> | criação de roleassignment de faturação | Não | Não |
> | departamentos | Não | Não |
> | contas de matrículas | Não | Não |
> | faturas | Não | Não |
> | resultados de operação | Não | Não |
> | operações | Não | Não |
> | estado de funcionamento | Não | Não |
> | transferências | Não | Não |
> | transferências / aceitação transferência | Não | Não |
> | transferências /transferência seleto | Não | Não |
> | transferências / estado de funcionamento | Não | Não |
> | transferências / validação de transferências | Não | Não |
> | validação | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | preferência por listcomunhões | Não | Não |
> | mapapis | Não | Não |
> | operações | Não | Não |
> | preferência de atualização de comunicações | Não | Não |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | biztalk | Não | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Não | Não |
> | cordamembers | Não | Não |
> | locais | Não | Não |
> | localizações / blockchainmemberoperationresults | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | localizações /listconsortiums | Não | Não |
> | locais /resultados de operações de observadores | Não | Não |
> | operações | Não | Não |
> | observadores | Não | Não |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |
> | tokenservices | Não | Não |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Não | Não |
> | blueprintassignments / operações de atribuição | Não | Não |
> | blueprintassignments / operações | Não | Não |
> | plantas | Não | Não |
> | plantas / artefactos | Não | Não |
> | plantas / versões | Não | Não |
> | plantas / versões / artefactos | Não | Não |
> | operações | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços bots | Sim | Sim |
> | botservices / canais | Não | Não |
> | botservices / conexões | Não | Não |
> | checknamedisponibilidade | Não | Não |
> | Fornecedores de serviços de listauthservices | Não | Não |
> | operações | Não | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | locais / estado de operações | Não | Não |
> | operações | Não | Não |
> | redis | Sim | Sim |
> | redis / privateendpointconnections | Não | Não |
> | redis / privatelinkresources | Não | Não |
> | redisenterprise | Não | Não |

> [!IMPORTANT]
> Se o caso Azure Cache for Redis estiver configurado com uma rede virtual, a instância não pode ser transferida para uma subscrição diferente. Ver [limitações](./move-limitations/networking-move-limitations.md)de movimento em rede .

## <a name="microsoftcapacity"></a>Microsoft.Capacidade

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | reservas aplicadas | Não | Não |
> | calcular troca | Não | Não |
> | calcular preço | Não | Não |
> | calcular preço de compra | Não | Não |
> | catálogos | Não | Não |
> | cheques | Não | Não |
> | check-status de compra | Não | Não |
> | cheques | Não | Não |
> | ordens de reservas comerciais | Não | Não |
> | troca | Não | Não |
> | benefícios de lista | Não | Não |
> | operações | Não | Não |
> | placebuyorder | Não | Não |
> | ordens de reserva | Não | Não |
> | ordens de reserva / disponíveiscópios | Não | Não |
> | ordens de reserva / calcular o reembolso | Não | Não |
> | ordens de reserva / fusão | Não | Não |
> | pedidos de reservas / reservas | Não | Não |
> | pedidos de reservas / reservas / disponíveiscópios | Não | Não |
> | pedidos de reservas / reservas / revisões | Não | Não |
> | ordens de reserva / devolução | Não | Não |
> | ordens de reserva / divisão | Não | Não |
> | ordens de reserva / troca | Não | Não |
> | reservas | Não | Não |
> | resources | Não | Não |
> | validar ordem de reservas | Não | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Não | Não |
> | cdnwebapplicationfirewallpolicies | Sim | Sim |
> | checknamedisponibilidade | Não | Não |
> | checkresource | Não | Não |
> | talhenos | Não | Não |
> | resultados de operação | Não | Não |
> | resultados de operações / perfis | Não | Não |
> | resultados de operações / resultados de perfis / resultados finais | Não | Não |
> | resultados de operações / resultados de perfis / resultados finais / resultados de domínio personalizado | Não | Não |
> | resultados de funcionamento / resultados de perfis / resultados finais / origingroupresults | Não | Não |
> | resultados de funcionamento / resultados de perfis / resultados finais / resultados de origens | Não | Não |
> | operações | Não | Não |
> | perfis | Sim | Sim |
> | perfis / pontos finais | Sim | Sim |
> | perfis / pontos finais / domínios personalizados | Não | Não |
> | perfis / pontos finais / grupos de origens | Não | Não |
> | perfis / pontos finais / origens | Não | Não |
> | validação sonda | Não | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | certificados | Sim | Sim |
> | certificados/ certificados | Não | Não |
> | operações | Não | Não |
> | validação de informação sobre registo de certificados | Não | Não |

> [!IMPORTANT]
> Consulte [a orientação de movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | Não | Não |
> | checkdomínionamedisponibilidade | Não | Não |
> | nomes de domínio | Sim | Não |
> | nomes de domínio / capacidades | Não | Não |
> | nomes de domínio / desequilibradores internos | Não | Não |
> | nomes de domínio / certificados de serviço | Não | Não |
> | nomes de domínio / slots | Não | Não |
> | nomes de domínio / slots / funções | Não | Não |
> | nomes de domínio / slots / papéis / definições métricas | Não | Não |
> | nomes de domínio / slots / papéis / métricas | Não | Não |
> | mover recursos de subscrição | Não | Não |
> | famílias de sistemas operativos | Não | Não |
> | sistemas operativos | Não | Não |
> | operações | Não | Não |
> | estados de funcionamento | Não | Não |
> | quotas | Não | Não |
> | tipos de recursos | Não | Não |
> | validando a disponibilidade de movimentos de subscrição | Não | Não |
> | máquinas virtuais | Sim | Não |
> | virtualmachines / definições de diagnóstico | Não | Não |
> | virtualmachines / definições métricas | Não | Não |
> | máquinas virtuais / métricas | Não | Não |

> [!IMPORTANT]
> Consulte a orientação de movimento de movimento de [implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | recursos clássicos de infraestruturas | Não | Não |

> [!IMPORTANT]
> Consulte a orientação de movimento de movimento de [implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | Não | Não |
> | conexões de vias cruzadas expressroute | Não | Não |
> | conexões /espreitamentos expressroutecross | Não | Não |
> | dispositivos suportados gateway | Não | Não |
> | networksecuritygroups | Não | Não |
> | operações | Não | Não |
> | quotas | Não | Não |
> | reservas | Não | Não |
> | redes virtuais | Não | Não |
> | redes virtuais / proxies de redes virtuais remotas | Não | Não |
> | redes virtuais / virtualnetworkpeerings | Não | Não |

> [!IMPORTANT]
> Consulte a orientação de movimento de movimento de [implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | Não | Não |
> | checkstorage accountdisponibilidade | Não | Não |
> | discos | Não | Não |
> | imagens | Não | Não |
> | operações | Não | Não |
> | imagens | Não | Não |
> | imagens de asplataforma | Não | Não |
> | imagens públicas | Não | Não |
> | quotas | Não | Não |
> | contas de armazenamento | Sim | Não |
> | contas de armazenamento / blobservices | Não | Não |
> | contas de armazenamento / serviços de arquivo | Não | Não |
> | contas de armazenamento / definições métricas | Não | Não |
> | contas de armazenamento / métricas | Não | Não |
> | contas de armazenamento / serviços de fila | Não | Não |
> | contas de armazenamento / serviços | Não | Não |
> | contas de armazenamento / serviços / definições de diagnóstico | Não | Não |
> | contas de armazenamento / serviços / definições métricas | Não | Não |
> | contas de armazenamento / serviços / métricas | Não | Não |
> | contas de armazenamento / serviços de mesa | Não | Não |
> | contas de armazenamento / vmimages | Não | Não |
> | vmimages | Não | Não |

> [!IMPORTANT]
> Consulte a orientação de movimento de movimento de [implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |

> [!IMPORTANT]
> Consulte a orientação de movimento de movimento de [implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

## <a name="microsoftcognition"></a>Microsoft.Cognição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas sintéticas | Não | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.Serviços Cognitivos

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | checkdomíniodisponibilidade | Não | Não |
> | locais | Não | Não |
> | localizações /checkskudisponibilidade | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |
> | cartão de taxa | Não | Não |
> | agregados de uso | Não | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | conjuntos de disponibilidade | Sim | Sim |
> | acessos de discos | Não | Não |
> | conjuntos de encriptação de discos | Não | Não |
> | discos | Sim | Sim |
> | galerias | Não | Não |
> | galerias / imagens | Não | Não |
> | galerias / imagens / versões | Não | Não |
> | grupos de anfitriões | Não | Não |
> | grupos de anfitriões / anfitriões | Não | Não |
> | imagens | Sim | Sim |
> | locais | Não | Não |
> | locais / editores de artefactos | Não | Não |
> | localizações / operações de caps | Não | Não |
> | localizações /operações de discos | Não | Não |
> | localizações / loganalytics | Não | Não |
> | locais/operações | Não | Não |
> | localizações /editores | Não | Não |
> | localizações / comandos de execução | Não | Não |
> | localizações / utilizações | Não | Não |
> | localizações / máquinas virtuais | Não | Não |
> | localizações / vmsizes | Não | Não |
> | localizações / vsmoperations | Não | Não |
> | operações | Não | Não |
> | grupos de proximidade | Sim | Sim |
> | restaurospointcoles | Não | Não |
> | restaurosdepontos / pontos de restauro | Não | Não |
> | extensões partilhadas | Não | Não |
> | sharedvmimages | Não | Não |
> | sharedvmimages / versões | Não | Não |
> | instantâneos | Sim | Sim |
> | sshpublickeys | Não | Não |
> | máquinas virtuais | Sim | Sim |
> | virtualmachines / extensões | Sim | Sim |
> | virtualmachines / definições métricas | Não | Não |
> | máquinas virtuais / comandos de execução | Não | Não |
> | conjuntos de escala seleções virtuais | Sim | Sim |
> | conjuntos de escalas de máquinas virtuais / extensões | Não | Não |
> | virtualmachinescalesets / networkinterfaces | Não | Não |
> | conjuntos de escalas virtualmachine / publicipaddresss | Não | Não |
> | virtualmachinescalesets / máquinas virtuais | Não | Não |
> | virtualmachinescalesets / virtualmachines / networkinterfaces | Não | Não |

> [!IMPORTANT]
> Ver [Máquinas Virtuais mover orientação](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Consumo

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | custo agregado | Não | Não |
> | equilíbrios | Não | Não |
> | orçamentos | Não | Não |
> | encargos | Não | Não |
> | costtags | Não | Não |
> | créditos | Não | Não |
> | eventos | Não | Não |
> | previsões | Não | Não |
> | lotes | Não | Não |
> | mercados | Não | Não |
> | resultados de operação | Não | Não |
> | operações | Não | Não |
> | estado de funcionamento | Não | Não |
> | folhas de preços | Não | Não |
> | produtos | Não | Não |
> | detalhes da reserva | Não | Não |
> | detalhes da recomendação de reservas | Não | Não |
> | recomendações de reservas | Não | Não |
> | resumos de reservas | Não | Não |
> | transações de reservas | Não | Não |
> | etiquetas | Não | Não |
> | inquilinos | Não | Não |
> | termos | Não | Não |
> | detalhes de uso | Não | Não |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de contentores | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de contentores | Não | Não |
> | locais | Não | Não |
> | localizações / imagens em cache | Não | Não |
> | localizações / capacidades | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | locais/operações | Não | Não |
> | localizações / utilizações | Não | Não |
> | operações | Não | Não |
> | ligações de associação de serviços | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | locais | Não | Não |
> | localizações / autorizar | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / configuração | Não | Não |
> | operações | Não | Não |
> | registos | Sim | Sim |
> | registos / grupos de agentes | Sim | Sim |
> | registos / listade piscinas / listqueuestatus | Não | Não |
> | registos / construções | Não | Não |
> | registos / construções / cancelamento | Não | Não |
> | registos / construções / getloglink | Não | Não |
> | registos / tarefas de construção | Sim | Sim |
> | registos / tarefas de construção / listsourcerepositoryproperties | Não | Não |
> | registos / tarefas de construção / passos | Não | Não |
> | registos / tarefas de construção / passos / listbuildarguments | Não | Não |
> | registos / filtros de eventgrid | Não | Não |
> | registos / oleodutos de exportação | Não | Não |
> | registos / credenciais de geração | Não | Não |
> | registos / getbuildsourceuploadurl | Não | Não |
> | registos / getcredenciais | Não | Não |
> | registos / importimage | Não | Não |
> | registos / oleodutos de importação | Não | Não |
> | registos / listbuildsourceuploadurl | Não | Não |
> | registos / credenciais de lista | Não | Não |
> | registos / políticas de listas | Não | Não |
> | registos / listusages | Não | Não |
> | registos / pipelineruns | Não | Não |
> | registos / privateendpointconnectionproxies | Não | Não |
> | registos / privateendpointconnectionproxies / validar | Não | Não |
> | registos / privateendpointconnections | Não | Não |
> | registos / privatelinkresources | Não | Não |
> | registos / queuebuild | Não | Não |
> | registos / regeneratecredential | Não | Não |
> | registos / credenciais regeneradas | Não | Não |
> | registos / replicações | Sim | Sim |
> | registos / execuções | Não | Não |
> | registos / execuções / cancelar | Não | Não |
> | registos / executões / listlogsasurl | Não | Não |
> | registos / programação | Não | Não |
> | registos / mapas de âmbito | Não | Não |
> | registos / taskruns | Não | Não |
> | registos / taskruns / listdetails | Não | Não |
> | registos / tarefas | Sim | Sim |
> | registos / tarefas / dados de listas | Não | Não |
> | registos / fichas | Não | Não |
> | registos / políticas de atualização | Não | Não |
> | registos / webhooks | Sim | Sim |
> | registos / webhooks / getcallbackconfig | Não | Não |
> | registos / webhooks / eventos de listas | Não | Não |
> | registos / webhooks / ping | Não | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de contentores | Não | Não |
> | locais | Não | Não |
> | localizações / aglomerados de turno aberto | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | locais/operações | Não | Não |
> | locais / orquestradores | Não | Não |
> | clusters geridos | Não | Não |
> | openshiftmanagedclusters | Não | Não |
> | operações | Não | Não |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Não | Não |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | alerts | Não | Não |
> | contas de faturação | Não | Não |
> | orçamentos | Não | Não |
> | conectores de nuvem | Não | Não |
> | conectores | Sim | Sim |
> | departamentos | Não | Não |
> | dimensões | Não | Não |
> | contas de matrículas | Não | Não |
> | exportações | Não | Não |
> | contas de faturação externa | Não | Não |
> | contas/alertas de faturação externa | Não | Não |
> | contas/dimensões de faturação externa | Não | Não |
> | contas de faturação externa /previsão | Não | Não |
> | contas de faturação externa / consulta | Não | Não |
> | subscrições externas | Não | Não |
> | subscrições/alertas externos | Não | Não |
> | subscrições/dimensões externas | Não | Não |
> | subscrições externas /previsão | Não | Não |
> | subscrições externas / consulta | Não | Não |
> | forecast | Não | Não |
> | operações | Não | Não |
> | consulta | Não | Não |
> | registar | Não | Não |
> | reportconfigs | Não | Não |
> | relatórios | Não | Não |
> | settings | Não | Não |
> | regras de showback | Não | Não |
> | Modos de exibição | Não | Não |

## <a name="microsoftcostmanagementexports"></a>Microsoft.CostManagementExports

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hubs | Não | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |
> | pedidos | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | associações | Não | Não |
> | locais | Não | Não |
> | localizações / estados de funcionamento | Não | Não |
> | operações | Não | Não |
> | fornecedores de recursos | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | empregos | Não | Não |
> | locais | Não | Não |
> | localizações / availableskus | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / configuração de regiões | Não | Não |
> | localizações / validação | Não | Não |
> | localizações / validações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dispositivos de databoxedge | Não | Não |
> | dispositivos de databoxedge / checknamedisponibilidade | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | locais | Não | Não |
> | localizações / obterpolíticas de rede | Não | Não |
> | localizações / estados de funcionamento | Não | Não |
> | operações | Não | Não |
> | áreas de trabalho | Não | Não |
> | espaços de trabalho / dbworkspaces | Não | Não |
> | espaços de trabalho / virtualnetworkpeerings | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | catálogos | Sim | Sim |
> | checknamedisponibilidade | Não | Não |
> | catálogos de dados | Não | Não |
> | locais | Não | Não |
> | localizações / empregos | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdataconnect"></a>Microsoft.dataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gestores de conexão | Não | Não |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | pacotes | Não | Não |
> | planos | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checkazuredatadisponibilidade de nomes de fábrica | Não | Não |
> | checkdatafactorynamedisponibilidade | Não | Não |
> | fábricas de dados | Sim | Sim |
> | fábricas de dados / definições de diagnóstico | Não | Não |
> | fábricas de dados / definições métricas | Não | Não |
> | datafactoryschema | Não | Não |
> | fábricas | Sim | Sim |
> | fábricas / integrações runtime | Não | Não |
> | locais | Não | Não |
> | localizações / configurefactoryrepo | Não | Não |
> | localizações / getfeaturevalue | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | datalakecontas | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | contas / contas datalakestore | Não | Não |
> | contas /contas de armazenamento | Não | Não |
> | contas / contas de armazenamento / contentores | Não | Não |
> | contas / contas de armazenamento / contentores / listastokens | Não | Não |
> | locais | Não | Não |
> | localizações / capacidade | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / utilizações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | contas / filtros de eventgrid | Não | Não |
> | contas / firewallrules | Não | Não |
> | locais | Não | Não |
> | localizações / capacidade | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / utilizações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | locais | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / estados de funcionamento | Não | Não |
> | operações | Não | Não |
> | services | Não | Não |
> | serviços / projetos | Não | Não |
> | slots | Não | Não |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | backupvaults | Não | Não |
> | locais | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | contas / ações | Não | Não |
> | contas / ações / conjuntos de dados | Não | Não |
> | contas / ações / convites | Não | Não |
> | contas / ações / assinaturas de partilhas de fornecedores | Não | Não |
> | contas / ações / sincronizações | Não | Não |
> | contas / subscrições de ações | Não | Não |
> | contas / subscrições de ações / conjuntos de dados de fontes de consumo | Não | Não |
> | contas / subscrições de ações / datasetmappings | Não | Não |
> | contas / subscrições de ações / gatilhos | Não | Não |
> | listconvites | Não | Não |
> | locais | Não | Não |
> | locais / convites para consumidores | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / convite rejeitado | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | locais | Não | Não |
> | localizações / operação azureasync | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / performancetiers | Não | Não |
> | localizações / privateendpointconnectionazureasyncoperation | Não | Não |
> | localizações / privateendpointconnectionresultados | Não | Não |
> | localizações / privateendpointconnectionproxyazureasyncoperation | Não | Não |
> | localizações / privateendpointconnectionproxyoperationresults | Não | Não |
> | localizações / recomendadoactionsessionsazureasyncoperation | Não | Não |
> | locais / resultados recomendados de sessões de ação | Não | Não |
> | localizações /segurançaalertapolíticasazureasyncoperation | Não | Não |
> | localizações /segurançaalertapoliciesresultados | Não | Não |
> | localizações / servidorkeyazureasyncoperation | Não | Não |
> | localizações / resultados de operações de servidor | Não | Não |
> | operações | Não | Não |
> | servidores | Sim | Sim |
> | servidores /conselheiros | Não | Não |
> | servidores / privateendpointconnectionproxies | Não | Não |
> | servidores / privateendpointconnections | Não | Não |
> | servidores / privatelinkresources | Não | Não |
> | servidores / textos de consulta | Não | Não |
> | servidores / servidores recuperáveis | Não | Não |
> | servidores / estatísticas topquery | Não | Não |
> | servidores / regras de rede virtual | Não | Não |
> | servidores / estatísticas de espera | Não | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | locais | Não | Não |
> | localizações /administradorazureasyncoperation | Não | Não |
> | localizações /administradores resultados de operação | Não | Não |
> | localizações / operação azureasync | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / performancetiers | Não | Não |
> | localizações / privateendpointconnectionazureasyncoperation | Não | Não |
> | localizações / privateendpointconnectionresultados | Não | Não |
> | localizações / privateendpointconnectionproxyazureasyncoperation | Não | Não |
> | localizações / privateendpointconnectionproxyoperationresults | Não | Não |
> | localizações / recomendadoactionsessionsazureasyncoperation | Não | Não |
> | locais / resultados recomendados de sessões de ação | Não | Não |
> | localizações /segurançaalertapolíticasazureasyncoperation | Não | Não |
> | localizações /segurançaalertapoliciesresultados | Não | Não |
> | localizações / servidorkeyazureasyncoperation | Não | Não |
> | localizações / resultados de operações de servidor | Não | Não |
> | operações | Não | Não |
> | servidores | Sim | Sim |
> | servidores /conselheiros | Não | Não |
> | servidores / teclas | Não | Não |
> | servidores / privateendpointconnectionproxies | Não | Não |
> | servidores / privateendpointconnections | Não | Não |
> | servidores / privatelinkresources | Não | Não |
> | servidores / textos de consulta | Não | Não |
> | servidores / servidores recuperáveis | Não | Não |
> | servidores / estatísticas topquery | Não | Não |
> | servidores / regras de rede virtual | Não | Não |
> | servidores / estatísticas de espera | Não | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | locais | Não | Não |
> | localizações /administradorazureasyncoperation | Não | Não |
> | localizações /administradores resultados de operação | Não | Não |
> | localizações / operação azureasync | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / performancetiers | Não | Não |
> | localizações / privateendpointconnectionazureasyncoperation | Não | Não |
> | localizações / privateendpointconnectionresultados | Não | Não |
> | localizações / privateendpointconnectionproxyazureasyncoperation | Não | Não |
> | localizações / privateendpointconnectionproxyoperationresults | Não | Não |
> | localizações / recomendadoactionsessionsazureasyncoperation | Não | Não |
> | locais / resultados recomendados de sessões de ação | Não | Não |
> | localizações /segurançaalertapolíticasazureasyncoperation | Não | Não |
> | localizações /segurançaalertapoliciesresultados | Não | Não |
> | localizações / servidorkeyazureasyncoperation | Não | Não |
> | localizações / resultados de operações de servidor | Não | Não |
> | operações | Não | Não |
> | grupos de servidores | Não | Não |
> | servidores | Sim | Sim |
> | servidores /conselheiros | Não | Não |
> | servidores / teclas | Não | Não |
> | servidores / privateendpointconnectionproxies | Não | Não |
> | servidores / privateendpointconnections | Não | Não |
> | servidores / privatelinkresources | Não | Não |
> | servidores / textos de consulta | Não | Não |
> | servidores / servidores recuperáveis | Não | Não |
> | servidores / estatísticas topquery | Não | Não |
> | servidores / regras de rede virtual | Não | Não |
> | servidores / estatísticas de espera | Não | Não |
> | servidorsv2 | Sim | Sim |
> | servidores únicos | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | fontes de artefactos | Sim | Sim |
> | resultados de operação | Não | Não |
> | operações | Não | Não |
> | lançamentos | Sim | Sim |
> | servicetopologies | Sim | Sim |
> | serviços /serviços | Sim | Sim |
> | serviços / serviços / unidades de serviço | Sim | Sim |
> | passos | Sim | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de aplicações | Sim | Sim |
> | grupos de aplicações/aplicações | Não | Não |
> | grupos de aplicações / desktops | Não | Não |
> | grupos de aplicações / startmenuitems | Não | Não |
> | piscinas de acolhimento | Sim | Sim |
> | hostpools / anfitriões de sessões | Não | Não |
> | hostpools / sessões de sessões / usersessions | Não | Não |
> | hostpools / usersessions | Não | Não |
> | operações | Não | Não |
> | áreas de trabalho | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft.Dispositivos

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | check-provisioningservicenamedisponibilidade | Não | Não |
> | elásticos | Não | Não |
> | elasticpools / iothubtenants | Não | Não |
> | iothubs | Sim | Sim |
> | iothubs / filtros de eventgrid | Não | Não |
> | resultados de operação | Não | Não |
> | operações | Não | Não |
> | serviços de prestação | Sim | Sim |
> | utilizações | Não | Não |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | oleodutos | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | controladores | Sim | Sim |
> | controladores / dados de ligação de listas | Não | Não |
> | locais | Não | Não |
> | localizações / checkcontainerhostmapping | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | centros de laboratório | Não | Não |
> | laboratórios | Sim | Não |
> | laboratórios / ambientes | Sim | Sim |
> | laboratórios / corredores de serviço | Sim | Sim |
> | laboratórios / máquinas virtuais | Sim | Não |
> | locais | Não | Não |
> | locais/operações | Não | Não |
> | operações | Não | Não |
> | horários | Sim | Sim |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | instâncias digitaltwins | Não | Não |
> | locais | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | nomes de contas de bases de dados | Não | Não |
> | contas de bases de dados | Sim | Sim |
> | locais | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | locais / estado de operações | Não | Não |
> | resultados de operação | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checkdomíniodisponibilidade | Não | Não |
> | domínios | Sim | Sim |
> | domínios / identificadores de domínio | Não | Não |
> | gerassopedido | Não | Não |
> | recomendações de domínio de lista | Não | Não |
> | operações | Não | Não |
> | domínios de nível superior | Não | Não |
> | validaçãodeinformação de registode domínio | Não | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |
> | services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | domínios | Sim | Sim |
> | eventoSSubscrições | Não - não pode ser movido independentemente, mas automaticamente movido com recurso subscrito. | Não - não pode ser movido independentemente, mas automaticamente movido com recurso subscrito. |
> | domínios / tópicos | Não | Não |
> | assinaturas de eventos | Não - não pode ser movido independentemente, mas automaticamente movido com recurso subscrito. | Não - não pode ser movido independentemente, mas automaticamente movido com recurso subscrito. |
> | tópicos de extensão | Não | Não |
> | locais | Não | Não |
> | localizações / assinaturas de eventos | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | locais / estado de operações | Não | Não |
> | localizações / tópicos | Não | Não |
> | resultados de operação | Não | Não |
> | operações | Não | Não |
> | estado de operações | Não | Não |
> | espaços de nome de parceiro | Sim | Sim |
> | espaços de nome de parceiros / canais de eventos | Não | Não |
> | registos de parceiros | Não | Não |
> | tópicos parceiro | Sim | Sim |
> | partnertopics / assinaturas de eventos | Não | Não |
> | tópicos sistematópicas | Sim | Sim |
> | sistematópicos / assinaturas de eventos | Não | Não |
> | tópicos | Sim | Sim |
> | tópicos | Não | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | regiões de aglomerados disponíveis | Não | Não |
> | checknamedisponibilidade | Não | Não |
> | checknamespaceavailabilit | Não | Não |
> | aglomerados | Sim | Sim |
> | locais | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / regras de autorização | Não | Não |
> | espaços de nomes / desastres recoveryconfigs | Não | Não |
> | espaços de nome / desastresrecoveryconfigs / checknamedisponibilidade | Não | Não |
> | espaços de nomes / centros de eventos | Não | Não |
> | espaços de nome / eventhubs / regras de autorização | Não | Não |
> | espaços de nome / eventhubs / grupos de consumidores | Não | Não |
> | espaços de nome / conjuntos de regras de rede | Não | Não |
> | operações | Não | Não |
> | sku | Não | Não |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentação

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | experimentosespaços | Não | Não |
> | locais | Não | Não |
> | locais/operações | Não | Não |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços de nome | Sim | Sim |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | fornecedores de recursos | Não | Não |
> | funcionalidades | Não | Não |
> | operações | Não | Não |
> | fornecedores | Não | Não |
> | registos de recursos de subscrição | Não | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas autogeridas | Não | Não |
> | perfis de reconfiguração autogeredm | Não | Não |
> | atribuições de guestconfigurações | Não | Não |
> | operações | Não | Não |
> | software | Não | Não |
> | softwareupdateperfil | Não | Não |
> | atualizações de software | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hanainstâncias | Não | Não |
> | locais | Não | Não |
> | locais/operações | Não | Não |
> | locais / estado de operações | Não | Não |
> | operações | Não | Não |
> | sapmonitors | Sim | Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dedicados | Não | Não |
> | locais | Não | Não |
> | operações | Não | Não |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Sim | Sim |
> | clusters / aplicações | Não | Não |
> | clusters / resultados de operação | Não | Não |
> | locais | Não | Não |
> | localizações / operações azureasyncoperations | Não | Não |
> | localizações / billingspecs | Não | Não |
> | localizações / capacidades | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / utilizações | Não | Não |
> | localizações / validação de pedido | Não | Não |
> | operações | Não | Não |

> [!IMPORTANT]
> Pode mover os clusters HDInsight para uma nova subscrição ou grupo de recursos. No entanto, não é possível mover-se através de subscrições dos recursos de rede ligados ao cluster HDInsight (como a rede virtual, NIC ou balanceador de carga). Além disso, não é possível mover-se para um novo grupo de recursos um NIC que está ligado a uma máquina virtual para o cluster.
>
> Ao mover um cluster HDInsight para uma nova subscrição, primeiro mova outros recursos (como a conta de armazenamento). Em seguida, mova o cluster HDInsight por si só.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |
> | services | Sim | Sim |
> | serviços / privateendpointconnections | Não | Não |
> | serviços / privatelinkresources | Não | Não |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | máquinas | Sim | Sim |
> | máquinas / extensões | Não | Não |
> | operações | Não | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gestores de dados | Sim | Sim |
> | operações | Não | Não |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | componentes | Não | Não |
> | locais | Não | Não |
> | cóscopos de rede | Não | Não |
> | operações | Não | Não |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | empregos | Sim | Sim |
> | locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de ação | Sim | Sim |
> | alertas de atividade | Não | Não |
> | regras de alerta | Sim | Sim |
> | definições de escala automática | Sim | Sim |
> | linha de base | Não | Não |
> | calcular base | Não | Não |
> | componentes | Sim | Sim |
> | componentes / eventos | Não | Não |
> | componentes / contas de armazenamento ligados | Não | Não |
> | componentes / métricas | Não | Não |
> | componentes / planos de preços | Não | Não |
> | componentes / consulta | Não | Não |
> | regras de recolha de dados | Não | Não |
> | definições de diagnóstico | Não | Não |
> | categorias de definições de diagnóstico | Não | Não |
> | categorias de eventos | Não | Não |
> | tipos de eventos | Não | Não |
> | definições de diagnóstico alargadas | Não | Não |
> | definições de diagnóstico de convidados | Não | Não |
> | listademigração | Não | Não |
> | locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | definições de log | Não | Não |
> | perfis de registo | Não | Não |
> | registos | Não | Não |
> | alertas métricos | Não | Não |
> | linhas de base métricas | Não | Não |
> | metricbatch | Não | Não |
> | definições métricas | Não | Não |
> | espaços de nomemétrico | Não | Não |
> | metrics | Não | Não |
> | regras de alerta migratório | Não | Não |
> | modelo de emigração de novos preços | Não | Não |
> | meus livros | Não | Não |
> | grupos de notificações | Não | Não |
> | operações | Não | Não |
> | privatelinkscopeoperationoperationstatuses | Não | Não |
> | privatelinkscopes | Não | Não |
> | privatelinkscopes / privateendpointconnectionproxies | Não | Não |
> | privatelinkscopes / privateendpointconnections | Não | Não |
> | privatelinkscopes / recursos de âmbito | Não | Não |
> | modelo de preços de retrocesso | Não | Não |
> | regras de agendamento | Sim | Sim |
> | topology | Não | Não |
> | transações | Não | Não |
> | vminsightsboardingstatuses | Não | Não |
> | webtestes | Sim | Sim |
> | livros | Sim | Sim |
> | modelos de livro | Sim | Sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para uma nova subscrição não excede [as quotas de subscrição.](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | modelos de aplicações | Não | Não |
> | checknamedisponibilidade | Não | Não |
> | checksubdomíniodisponibilidade | Não | Não |
> | iotapps | Sim | Sim |
> | operações | Não | Não |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Sim | Sim |
> | gráfico | Sim | Sim |
> | operações | Não | Não |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | apaga-se cofres | Não | Não |
> | hsmpools | Não | Não |
> | locais | Não | Não |
> | localizações / cofres apagados | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |
> | cofres | Sim | Sim |
> | cofres / políticas de acesso | Não | Não |
> | cofres / filtros de eventgrid | Não | Não |
> | cofres / segredos | Não | Não |

> [!IMPORTANT]
> Os Cofres-Chave utilizados para encriptação de discos não podem ser transferidos para um grupo de recursos na mesma subscrição ou através de subscrições.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | clusters conectados | Sim | Sim |
> | locais | Não | Não |
> | localizações / estados de funcionamento | Não | Não |
> | operações | Não | Não |
> | subscrições registadas | Não | Não |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguração

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | configurações de controlo de fontes | Não | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Sim | Sim |
> | clusters / configurações de bases de dados anexadas | Não | Não |
> | clusters / bases de dados | Não | Não |
> | clusters / bases de dados / ligações de dados | Não | Não |
> | clusters / bases de dados / eventhubconnections | Não | Não |
> | clusters / bases de dados / principais atribuições | Não | Não |
> | clusters / principados | Não | Não |
> | locais | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de laboratório | Não | Não |
> | locais | Não | Não |
> | locais/operações | Não | Não |
> | operações | Não | Não |
> | utilizadores | Não | Não |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | ambientes de hospedagem | Não | Não |
> | contas de integração | Sim | Sim |
> | integraçõesambientes de serviços | Sim | Não |
> | integraçõesambientes de serviços / managedapis | Sim | Não |
> | ambientes isolados | Não | Não |
> | locais | Não | Não |
> | localizações / fluxos de trabalho | Não | Não |
> | operações | Não | Não |
> | fluxos de trabalho | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | planos de compromisso | Não | Não |
> | locais | Não | Não |
> | locais/operações | Não | Não |
> | locais / estado de operações | Não | Não |
> | operações | Não | Não |
> | webservices | Sim | Não |
> | áreas de trabalho | Sim | Sim |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | clusters de operacionalização | Não | Não |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | contas / espaços de trabalho | Não | Não |
> | contas / espaços de trabalho / projetos | Não | Não |
> | contas de equipa | Não | Não |
> | contas de equipas / espaços de trabalho | Não | Não |
> | teamaccounts / espaços de trabalho / projetos | Não | Não |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperacionalização

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de hospedagem | Não | Não |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | locais | Não | Não |
> | localizações / computação | Não | Não |
> | localizações / quotas | Não | Não |
> | localizações / quotas de atualização | Não | Não |
> | localizações / utilizações | Não | Não |
> | localizações / vmsizes | Não | Não |
> | localizações / workspaceoperationsstatus | Não | Não |
> | operações | Não | Não |
> | áreas de trabalho | Não | Não |
> | espaços de trabalho / computação | Não | Não |
> | espaços de trabalho / filtros de eventgrid | Não | Não |

## <a name="microsoftmaintenance"></a>Microsoft.Manutenção

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicar atualizações | Não | Não |
> | atribuições de configuração | Não | Não |
> | configurações de manutenção | Sim | Sim |
> | atualizações | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | identidades | Não | Não |
> | operações | Não | Não |
> | userassignedidentidades | Não | Não |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | redes geridas | Não | Não |
> | redes geridas /grupos de redes geridas | Não | Não |
> | redes geridas / políticas geridas de networkpeering | Não | Não |
> | notificação | Não | Não |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | definições de registo de marketplace | Não | Não |
> | operações | Não | Não |
> | estados de funcionamento | Não | Não |
> | atribuição de inscrições | Não | Não |
> | definições de registo | Não | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | getentidades | Não | Não |
> | managementgroups | Não | Não |
> | grupos de gestão / configurações | Não | Não |
> | resultados de operação | Não | Não |
> | resultados de operação / operação de sincronia | Não | Não |
> | operações | Não | Não |
> | resources | Não | Não |
> | starttenantbackfill | Não | Não |
> | inquilinopres | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | contas / filtros de eventgrid | Não | Não |
> | contas / privateatlases | Sim | Sim |
> | operações | Não | Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | Não | Não |
> | ofertas | Não | Não |
> | ofertas | Não | Não |
> | ofertas / editores | Não | Não |
> | ofertas / editores / ofertas | Não | Não |
> | ofertas / editores / ofertas / planos | Não | Não |
> | ofertas / editores / ofertas / planos / acordos | Não | Não |
> | ofertas / editores / ofertas / planos / configs | Não | Não |
> | ofertas / editores / ofertas / planos / configs / importimage | Não | Não |
> | operações | Não | Não |
> | privategalleryitems | Não | Não |
> | cliente de privatestore | Não | Não |
> | lojas privadas | Não | Não |
> | lojas privadas / ofertas | Não | Não |
> | produtos | Não | Não |
> | editores | Não | Não |
> | editoras / ofertas | Não | Não |
> | editores / ofertas / alterações | Não | Não |
> | registar | Não | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços clássicos | Não | Não |
> | preferência por listcomunhões | Não | Não |
> | operações | Não | Não |
> | preferência de atualização de comunicações | Não | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | acordos | Não | Não |
> | ofertas | Não | Não |
> | operações | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | locais | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | serviços de media | Sim | Sim |
> | mediaservices / filtros de conta | Não | Não |
> | serviços de media / ativos | Não | Não |
> | mediaservices / ativos / filtros de ativos | Não | Não |
> | mediaservices / contentkeypolicies | Não | Não |
> | mediaservices / eventgridfilters | Não | Não |
> | mediaservices / liveeventoperations | Não | Não |
> | mediaservices / liveevents | Sim | Sim |
> | mediaservices / liveevents / liveoutputs | Não | Não |
> | mediaservices / liveoutputoperations | Não | Não |
> | serviços de media / streamingendpointoperations | Não | Não |
> | mediaservices / streamingendpoints | Sim | Sim |
> | mediaservices / streaminglocators | Não | Não |
> | mediaservices / políticas de streaming | Não | Não |
> | mediaservices / transforma | Não | Não |
> | mediaservices / transforma /empregos | Não | Não |
> | operações | Não | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | appclusters | Não | Não |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | projetos de avaliação | Sim | Sim |
> | locais | Não | Não |
> | localizações/opções de avaliação | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | projetos migratórios | Sim | Sim |
> | move-secoles | Não | Não |
> | operações | Não | Não |
> | projetos | Não | Não |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas holográficas de transmissão | Não | Não |
> | locais | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | contas de entendimento de objetos | Não | Não |
> | operações | Não | Não |
> | contas de renderização remota | Sim | Sim |
> | contas de âncoras espaciais | Sim | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Não | Não |
> | netappaccounts / backuppolicies | Não | Não |
> | netappaccounts / pools de capacidade | Não | Não |
> | netappaccounts / capacitypools / volumes | Não | Não |
> | netappaccounts / capacitypools / volumes / mounttargets | Não | Não |
> | netappaccounts / capacitypools / volumes / instantâneos | Não | Não |
> | operações | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | applicationgatewaydisponíveisrequestheaders | Não | Não |
> | applicationgatewaydisponíveisresponseheaders | Não | Não |
> | applicationgatewayavailableservervariáves | Não | Não |
> | aplicagatewayavailablesloptions | Não | Não |
> | aplicagatewayavailablewafrulesets | Não | Não |
> | applicationgateways | Não | Não |
> | aplicaçõesgatewaywebapplicationfirewallpolicies | Não | Não |
> | grupos de segurança de aplicações | Sim | Sim |
> | azurefirewallfqdntags | Não | Não |
> | azurefirewalls | Não | Não |
> | bastionários | Não | Não |
> | comunidades de serviços bgpservice | Não | Não |
> | checkfrontdoornamedisponibilidade | Não | Não |
> | checktrafficmanagernamedisponibilidade | Não | Não |
> | conexões | Sim | Sim |
> | ddoscustompolíticas | Sim | Sim |
> | ddosprotectionplans | Não | Não |
> | resultados de operação | Não | Não |
> | dnsoperationstatuses | Não | Não |
> | zonas de dnszone | Sim | Sim |
> | dnszones / a | Não | Não |
> | dnszones / aaaa | Não | Não |
> | zonas de dnszones / todos | Não | Não |
> | dnszones / caa | Não | Não |
> | dnszones / cname | Não | Não |
> | dnszones / mx | Não | Não |
> | dnszones / ns | Não | Não |
> | dnszones / ptr | Não | Não |
> | dnszones / conjuntos de recordes | Não | Não |
> | dnszones / soa | Não | Não |
> | dnszones / srv | Não | Não |
> | dnszones / txt | Não | Não |
> | circuitos de rotação expresso | Não | Não |
> | expressroutegateways | Não | Não |
> | prestadores de serviços de rotação expressroute | Não | Não |
> | firewallpolíticas | Sim | Sim |
> | frontdooroperationresultados | Não | Não |
> | portas da frente | Não | Não |
> | frontdoorwebapplicationfirewallmanagedrulesets | Não | Não |
> | frontdoorwebapplicationapplicationfirewallpolicies | Não | Não |
> | obterdnsresourcereference | Não | Não |
> | notificação interna | Não | Não |
> | ipgroups | Sim | Sim |
> | loadbalancers | Sim - SKU Básico<br>Não - Standard SKU | Sim - SKU Básico<br>Não - Standard SKU |
> | locaisdenetworkgateways | Sim | Sim |
> | locais | Não | Não |
> | localizações / serviços de privatelink autoaprovados | Não | Não |
> | localizações /delegações disponíveis | Não | Não |
> | localizações / pontos finais privados disponíveis | Não | Não |
> | localizações / disponíveisservicealiases | Não | Não |
> | localizações / inquilinos baremetal | Não | Não |
> | localizações / batchnotifyprivateendpointsforresource | Não | Não |
> | localizações / batchvalidateprivateendpointsforresourcemove | Não | Não |
> | localizações / checkacceleratednetworkingsupport | Não | Não |
> | localizações /checkdnsnameavailabilit | Não | Não |
> | localizações / checkprivatelinkservicevisibilidade | Não | Não |
> | localizações / comprometerinternalazurenetworkmanagerreconfigurção | Não | Não |
> | localizações / propriedade eficaz de recursos | Não | Não |
> | localizações / resultados de nfvoperation | Não | Não |
> | localizações / nfvoperations | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | locais/operações | Não | Não |
> | localizações / etiquetas de serviço | Não | Não |
> | localizações / setresourceownership | Não | Não |
> | localizações /suportevirtualsizes | Não | Não |
> | localizações / utilizações | Não | Não |
> | localizações / validação de recursos | Não | Não |
> | localizações / serviços de endpointdisponíveis virtual | Não | Não |
> | natgateways | Sim | Sim |
> | perfis de experiência de rede | Não | Não |
> | políticas de intenções de rede | Sim | Sim |
> | interfaces de rede | Sim | Sim |
> | perfis de rede | Não | Não |
> | networksecuritygroups | Sim | Sim |
> | observadores de rede | Sim | Não |
> | networkwatchers / monitores de ligação | Sim | Não |
> | networkwatchers / flowlogs | Sim | Não |
> | networkwatchers / pingmeshes | Sim | Não |
> | operações | Não | Não |
> | p2svpngateways | Não | Não |
> | resultados privados de operação | Não | Não |
> | privatednsoperationstatuses | Não | Não |
> | zonas privadas | Sim | Sim |
> | privatednszones / a | Não | Não |
> | privatednszones / aaaaa | Não | Não |
> | privatednszones / todos | Não | Não |
> | privatednszones / cname | Não | Não |
> | privatednszones / mx | Não | Não |
> | privatednszones / ptr | Não | Não |
> | privatednszones / soa | Não | Não |
> | privatednszones / srv | Não | Não |
> | privatednszones / txt | Não | Não |
> | privatednszones / virtualnetworklinks | Sim | Sim |
> | privatednszonesinternal | Não | Não |
> | mapas de privateendpointredirectmaps | Não | Não |
> | pontos de privateend | Sim | Sim |
> | serviços privatelink | Não | Não |
> | publicipaddresss | Sim - SKU Básico<br>Não - Standard SKU | Sim - SKU Básico<br>Não - Standard SKU |
> | publicipprefixes | Sim | Sim |
> | filtros de rotas | Não | Não |
> | rotações | Sim | Sim |
> | prestadores de parceiros de segurança | Sim | Sim |
> | políticas de pontos de serviço | Sim | Sim |
> | hierarquias geográficas de trafficmanager | Não | Não |
> | perfis de gestor de tráfego | Sim | Sim |
> | perfis de gestor de tráfego / mapas de calor | Não | Não |
> | trafficmanagerusermetricskeys | Não | Não |
> | virtualhubs | Não | Não |
> | virtualnetworkgateways | Sim | Sim |
> | redes virtuais | Sim | Sim |
> | virtualnetworktaps | Não | Não |
> | routers virtuais | Sim | Sim |
> | virtualwans | Não | Não |
> | vpngateways (Virtual WAN) | Não | Não |
> | vpnserverconfiguras | Não | Não |
> | vpnsites (Virtual WAN) | Não | Não |

> [!IMPORTANT]
> Consulte [a orientação](./move-limitations/networking-move-limitations.md)do movimento em rede .

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | checknamespaceavailabilit | Não | Não |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / centros de notificações | Sim | Sim |
> | resultados de operação | Não | Não |
> | operações | Não | Não |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços de osnome | Sim | Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hipervsites | Sim | Sim |
> | locais de importação | Sim | Sim |
> | operações | Não | Não |
> | servidores | Sim | Sim |
> | vmwaresites | Sim | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Não | Não |
> | espaços de trabalho apagados | Não | Não |
> | linktargets | Não | Não |
> | locais | Não | Não |
> | localizações / estados de funcionamento | Não | Não |
> | operações | Não | Não |
> | armazenamentoinsightconfigs | Não | Não |
> | áreas de trabalho | Sim | Sim |
> | espaços de trabalho / fontes de dados | Não | Não |
> | espaços de trabalho / serviços ligados | Não | Não |
> | espaços de trabalho / contas de armazenamento ligados | Não | Não |
> | espaços de trabalho / metadados | Não | Não |
> | espaços de trabalho / consulta | Não | Não |
> | espaços de trabalho / scopedprivatelinkproxies | Não | Não |

> [!IMPORTANT]
> Certifique-se de que a mudança para uma nova subscrição não excede as quotas de [subscrição.](azure-subscription-service-limits.md#azure-monitor-limits)
>
> Espaços de trabalho que têm uma conta de automação ligada não podem ser movidos. Antes de iniciar uma operação de movimento, certifique-se de desligar quaisquer contas de automação.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | associações de gestão | Não | Não |
> | configurações de gestão | Sim | Sim |
> | operações | Não | Não |
> | soluções | Sim | Sim |
> | Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checkserviceproviderdisponibilidade | Não | Não |
> | legacypeerings | Não | Não |
> | operações | Não | Não |
> | peerasns | Não | Não |
> | peeringlocations | Não | Não |
> | os pares | Sim | Sim |
> | peeringservicepaíses | Não | Não |
> | peeringservicelocations | Não | Não |
> | peeringserviceproviders | Não | Não |
> | serviços de peering | Não | Não |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | resultados de operações de asincronia | Não | Não |
> | operações | Não | Não |
> | eventos políticos | Não | Não |
> | estados políticos | Não | Não |
> | recursos de política | Não | Não |
> | reparações | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Consolas do | Não | Não |
> | dashboards | Sim | Sim |
> | locais | Não | Não |
> | localizações / consolas | Não | Não |
> | localizações / definições de utilizadores | Não | Não |
> | operações | Não | Não |
> | definições de utilizadores | Não | Não |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | recursos radiculares | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | locais | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | workspacecollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | Sim | Sim |
> | locais | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / estados de funcionamento | Não | Não |
> | operações | Não | Não |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | checknamedisponibilidade | Não | Não |
> | operações | Não | Não |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas disponíveis | Não | Não |
> | registos de fornecedores | Não | Não |
> | registos de fornecedores / recursos | Não | Não |
> | lançamentos | Não | Não |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | locais | Não | Não |
> | localizações / estados de funcionamento | Não | Não |
> | operações | Não | Não |
> | áreas de trabalho | Não | Não |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Não | Não |
> | locais | Não | Não |
> | localizações /carimbo atribuído | Não | Não |
> | localizações / carimbo de atribuição | Não | Não |
> | localizações / backupaadproperties | Não | Não |
> | localizações / backupcrossregionrestore | Não | Não |
> | localizações / backupcrrjob | Não | Não |
> | localizações / backupcrrjobs | Não | Não |
> | localizações / backupcrroperationresults | Não | Não |
> | localizações / backupcrroperationsstatus | Não | Não |
> | localizações / backupprevalidateprotection | Não | Não |
> | localizações / backupstatus | Não | Não |
> | localizações / backupvalidatefeatures | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | operações | Não | Não |
> | resultados de elegibilidade de replicação | Não | Não |
> | cofres | Sim | Sim |

> [!IMPORTANT]
> Consulte [os Serviços de Recuperação a mover orientação.](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | locais / estado de operações | Não | Não |
> | openshiftclusters | Não | Não |
> | operações | Não | Não |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / regras de autorização | Não | Não |
> | espaços de nome / ligações híbridas | Não | Não |
> | espaços de nome / ligações híbridas / regras de autorização | Não | Não |
> | espaços de nome / privateendpointconnections | Não | Não |
> | espaços de nome / wcfrelays | Não | Não |
> | espaços de nome / wcfrelays / regras de autorização | Não | Não |
> | operações | Não | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |
> | consultas | Sim | Sim |
> | detalhes de mudança de recursos | Não | Não |
> | mudanças de recursos | Não | Não |
> | resources | Não | Não |
> | recursoshistória | Não | Não |
> | estatuto de subscrição | Não | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | estados de disponibilidade | Não | Não |
> | estados de disponibilidade de crianças | Não | Não |
> | recursos infantis | Não | Não |
> | questões emergentes | Não | Não |
> | eventos | Não | Não |
> | do IdP | Não | Não |
> | notificações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftresources"></a>Microsoft.Recursos

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | verificação de conformidade política | Não | Não |
> | nome de cheques | Não | Não |
> | implementações | Não | Não |
> | destacamentos /operações | Não | Não |
> | implantscripts | Não | Não |
> | implantscripts / registos | Não | Não |
> | ligações | Não | Não |
> | locais | Não | Não |
> | localizações /implementaçãoscriptresultados | Não | Não |
> | notificarempregos | Não | Não |
> | resultados de operação | Não | Não |
> | operações | Não | Não |
> | fornecedores | Não | Não |
> | grupos de recursos | Não | Não |
> | resources | Não | Não |
> | assinaturas | Não | Não |
> | assinaturas /localizações | Não | Não |
> | assinaturas / resultados de operações | Não | Não |
> | assinaturas /fornecedores | Não | Não |
> | assinaturas / grupos de recursos | Não | Não |
> | assinaturas / grupos de recursos / recursos | Não | Não |
> | assinaturas / recursos | Não | Não |
> | assinaturas / tagnames | Não | Não |
> | assinaturas / tagnames / tagvalues | Não | Não |
> | etiquetas | Não | Não |
> | modelos | Não | Não |
> | modelos / versões | Não | Não |
> | inquilinos | Não | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Sim | Não |
> | checkmodernidade | Não | Não |
> | checknamedisponibilidade | Não | Não |
> | resultados de operação | Não | Não |
> | operações | Não | Não |
> | recursos saasrecursos | Não | Não |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | checkservicenamedisponibilidade | Não | Não |
> | operações | Não | Não |
> | recursossshealthmetametais | Não | Não |
> | serviços de pesquisa | Sim | Sim |

> [!IMPORTANT]
> Não se pode mover vários recursos de busca em diferentes regiões numa só operação. Em vez disso, movê-los em operações separadas.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Não | Não |
> | definições avançadas de proteção contra ameaças | Não | Não |
> | alerts | Não | Não |
> | conexões permitidas | Não | Não |
> | aplicaçõeswhitelistings | Não | Não |
> | metadados de avaliação | Não | Não |
> | avaliações | Não | Não |
> | regras de alertas auto-dismiss | Não | Não |
> | automações | Sim | Sim |
> | definições de autoprovisionamento | Não | Não |
> | resultados de conformidade | Não | Não |
> | cumprimentos | Não | Não |
> | datacollectionagents | Não | Não |
> | grupos de segurança de dispositivos | Não | Não |
> | descobertas soluções de segurança | Não | Não |
> | soluções de segurança externa | Não | Não |
> | políticas de proteção de informação | Não | Não |
> | soluções iotsecurity | Sim | Sim |
> | iotsecuritysolutions / analyticsmodels | Não | Não |
> | soluções iotsecuritysolutions / analyticsmodels / agregadalerts | Não | Não |
> | soluções iotsecuritysolutions / analíticos / recomendações agregadas | Não | Não |
> | jitnetworkaccesspolíticas | Não | Não |
> | locais | Não | Não |
> | locais / alertas | Não | Não |
> | localizações / conexões permitidas | Não | Não |
> | localizações /listas de aplicações whitelisting | Não | Não |
> | localizações / descobertas soluções de segurança | Não | Não |
> | localizações / soluções de segurança externa | Não | Não |
> | localizações / políticas de acesso a rede jitnetwork | Não | Não |
> | localizações / soluções de segurança | Não | Não |
> | localizações / securitysolutionsreferencedatadatadata | Não | Não |
> | localizações / tarefas | Não | Não |
> | localizações / topoologias | Não | Não |
> | operações | Não | Não |
> | políticas | Não | Não |
> | preços | Não | Não |
> | normas de conformidade regulamentar | Não | Não |
> | normas de conformidade regulamentar / controlos de conformidade regulamentar | Não | Não |
> | normas de conformidade regulamentar / controlos regulamentares / avaliações de conformidade regulamentar | Não | Não |
> | contactos de segurança | Não | Não |
> | soluções de segurança | Não | Não |
> | dados de referência soluções de segurança | Não | Não |
> | seguranças | Não | Não |
> | segurançasstatusesresumos | Não | Não |
> | avaliações de vulnerabilidade de servidores | Não | Não |
> | settings | Não | Não |
> | subavaliações | Não | Não |
> | tarefas | Não | Não |
> | topologies | Não | Não |
> | ambientes de espaço de trabalho | Não | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | agregações | Não | Não |
> | regras de alerta | Não | Não |
> | modelos de regras de alerta | Não | Não |
> | marcadores | Não | Não |
> | casos | Não | Não |
> | ligação de dados | Não | Não |
> | requisitos de verificação de conectores de dados | Não | Não |
> | entidades | Não | Não |
> | entidades | Não | Não |
> | incidentes | Não | Não |
> | consentimentos de escritório | Não | Não |
> | operações | Não | Não |
> | settings | Não | Não |
> | ameaça inteligência | Não | Não |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de consola | Não | Não |
> | locais | Não | Não |
> | localizações / consolas | Não | Não |
> | operações | Não | Não |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gateways | Não | Não |
> | nódoas | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | checknamespaceavailabilit | Não | Não |
> | locais | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / regras de autorização | Não | Não |
> | espaços de nomes / desastres recoveryconfigs | Não | Não |
> | espaços de nome / desastresrecoveryconfigs / checknamedisponibilidade | Não | Não |
> | espaços de nome / filtros de eventgrid | Não | Não |
> | espaços de nome / conjuntos de regras de rede | Não | Não |
> | espaços de nome / filas | Não | Não |
> | espaços de nome / filas / regras de autorização | Não | Não |
> | espaços de nome / tópicos | Não | Não |
> | espaços de nome / tópicos / regras de autorização | Não | Não |
> | espaços de nome / tópicos / subscrições | Não | Não |
> | espaços de nome / tópicos / subscrições / regras | Não | Não |
> | operações | Não | Não |
> | regiões de mensagens premium | Não | Não |
> | sku | Não | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Não | Não |
> | aglomerados | Sim | Sim |
> | clusters / aplicações | Não | Não |
> | grupos de contentores | Não | Não |
> | conjuntos de grupos de contentores | Não | Não |
> | aglomerados de bordas | Não | Não |
> | locais | Não | Não |
> | localizações / clusterversions | Não | Não |
> | localizações / ambientes | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | locais/operações | Não | Não |
> | clusters geridos | Não | Não |
> | redes | Não | Não |
> | operações | Não | Não |
> | lojas secretas | Não | Não |
> | volumes | Não | Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Sim | Sim |
> | grupos de contentores | Não | Não |
> | gateways | Sim | Sim |
> | locais | Não | Não |
> | localizações /operações de aplicação | Não | Não |
> | localizações / operações de gateway | Não | Não |
> | localizações / operações de rede | Não | Não |
> | localizações / operações secretas | Não | Não |
> | localizações / operações de volume | Não | Não |
> | redes | Sim | Sim |
> | operações | Não | Não |
> | segredos | Sim | Sim |
> | volumes | Sim | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | registos de fornecedores | Não | Não |
> | registos de fornecedores / recursos | Não | Não |
> | lançamentos | Não | Não |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | locais | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / estados de funcionamento | Não | Não |
> | localizações / utilizações | Não | Não |
> | operações | Não | Não |
> | signalr | Sim | Sim |
> | filtros de sinalizador / eventgrid | Não | Não |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | benefícios híbridos | Não | Não |
> | operações | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Soluções

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | definições de aplicações | Não | Não |
> | aplicações | Não | Não |
> | jitrequests | Não | Não |
> | locais | Não | Não |
> | localizações / estados de funcionamento | Não | Não |
> | operações | Não | Não |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | instâncias | Não | Não |
> | locais | Sim | Sim |
> | locais | Não | Não |
> | localizações /administradorazureasyncoperation | Não | Não |
> | localizações /administradores resultados de operação | Não | Não |
> | localizações /auditoriasdefiniçõesazureasyncoperation | Não | Não |
> | localizações /auditorias resultados de operação | Não | Não |
> | localizações / capacidades | Não | Não |
> | localizações /base de dadosazureasyncoperation | Não | Não |
> | localizações /bases de dadosresultados de operação | Não | Não |
> | localizações /base de dadosrestoreazureasyncoperation | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / deletevirtualnetworkorsubnetsazureasyncoperation | Não | Não |
> | localizações / eliminar resultados de operações de redes virtuais | Não | Não |
> | localizações / dnsaliasasyncoperation | Não | Não |
> | locais / resultados de operações | Não | Não |
> | localizações / operação elásticapoolazureasync | Não | Não |
> | localizações / resultados de operação elástica | Não | Não |
> | localizações / encriptaçãoprotectorazureasync | Não | Não |
> | localizações / encriptaçãoprotectoraresultados | Não | Não |
> | localizações / ampliação de definições de auditoriaazureasyncoperation | Não | Não |
> | localizações / resultados de auditoria seleção alargada | Não | Não |
> | localizações / failovergroupazureasyncoperation | Não | Não |
> | localizações / failovergroupoperationresults | Não | Não |
> | localizações /firewallrulesazureasyncoperation | Não | Não |
> | localizações / firewallrulesoperationresults | Não | Não |
> | localizações / instânciasovergroupazureasyncoperation | Não | Não |
> | localizações / instânciasfailovergroupoperationresultados | Não | Não |
> | localizações / instânciasfailovergroups | Não | Não |
> | localizações / instânciaspoolazureasyncoperation | Não | Não |
> | localizações / instânciasresultados de operação | Não | Não |
> | localizações / jobagentazureasyncoperation | Não | Não |
> | localizações /resultados de operações de agente de emprego | Não | Não |
> | localizações / Operação de backupazureasynceasync | Não | Não |
> | localizações / Resultados de operações de backup de longo prazo | Não | Não |
> | localizações / backups de longo prazo | Não | Não |
> | localizações / Operações de retenção de longo prazogeridasbackupazureasync | Não | Não |
> | localizações / Resultados de operações de backupdes geridos por longo prazo | Não | Não |
> | localizações / backups de instâncias geridas por longo prazo | Não | Não |
> | localizações / instâncias geridas a longo prazo | Não | Não |
> | localizações / Operação de política de longo prazoazureasync | Não | Não |
> | localizações / Resultados de operações de política de longo prazo | Não | Não |
> | localizações / servidores de retenção a longo prazo | Não | Não |
> | localizações /geridosoperação azureasync | Não | Não |
> | localizações /geridaoperação de restauroscompletesazureasync | Não | Não |
> | localizações / resultados de operações de restauro completes de base de dados geridas | Não | Não |
> | localizações /resultados geridos de operações de base de dados | Não | Não |
> | localizações /geridaoperação de restauraçãoazureasync | Não | Não |
> | localizações /resultados geridos de operações de restauro de bases de dados | Não | Não |
> | localizações /geridosoperação de sincronina | Não | Não |
> | localizações / managedinstancecryptonprotectorazureasyncoperation | Não | Não |
> | localizações /resultados geridos de encriptaçãodeencriptação | Não | Não |
> | localizações /geridosoperação keyazureasync | Não | Não |
> | localizações /resultados geridos de operações key | Não | Não |
> | localizações / gestão de longo prazosoperaçõesdeinosssincronização | Não | Não |
> | localizações / resultados geridos de longo prazoderesdespolíticas | Não | Não |
> | localizações /resultados geridos de operações | Não | Não |
> | localizações /geridosinstancetdecertazureasyncoperation | Não | Não |
> | localizações /resultados geridos de operações | Não | Não |
> | localizações /geridoservidorssegurançapolíticasazureasync | Não | Não |
> | localizações /resultados geridos de políticas de alerta de segurança do servidor | Não | Não |
> | localizações / geridoshorttermretentionpolicyazureasyncoperation | Não | Não |
> | localizações / resultados geridos de política de retenção de curto prazo | Não | Não |
> | localizações /operação notificaazureasync | Não | Não |
> | localizações / privateendpointconnectionazureasyncoperation | Não | Não |
> | localizações / privateendpointconnectionresultados | Não | Não |
> | localizações / privateendpointconnectionproxyazureasyncoperation | Não | Não |
> | localizações / privateendpointconnectionproxyoperationresults | Não | Não |
> | localizações /segurançaalertapolíticasazureasyncoperation | Não | Não |
> | localizações /segurançaalertapoliciesresultados | Não | Não |
> | localizações /servidoradministradorazureasyncoperation | Não | Não |
> | localizações /resultados de operação de administrador de servidores | Não | Não |
> | localizações / servidorazureasyncoperation | Não | Não |
> | localizações / servidorkeyazureasyncoperation | Não | Não |
> | localizações / resultados de operações de servidor | Não | Não |
> | localizações /resultados de operação de servidores | Não | Não |
> | localizações / operação de curto prazo | Não | Não |
> | localizações / resultados de operações de política de retenção de curto prazo | Não | Não |
> | localizações /resultados de operações de sintetário | Não | Não |
> | localizações / sincopeids | Não | Não |
> | localizações /resultados de sincopamento | Não | Não |
> | localizações /resultados de sincopamento | Não | Não |
> | localizações / operação tdecertazureasync | Não | Não |
> | localizações / tdecertoperationresults | Não | Não |
> | localizações / utilizações | Não | Não |
> | localizações / operação virtualclusterazureasync | Não | Não |
> | localizações / resultados de operações de clustervirtual | Não | Não |
> | localizações / virtualnetworkrulesazureasyncoperation | Não | Não |
> | localizações / resultados de operações de redes virtuais | Não | Não |
> | localizações /vulnerabilidadesavaliaçõescanazureasync | Não | Não |
> | localizações /vulnerabilidadesavaliaçõesresultados de operação | Não | Não |
> | casos geridos | Não | Não |
> | casos geridos /administradores | Não | Não |
> | casos geridos / bases de dados | Não | Não |
> | casos geridos / bases de dados / backuplongretentionpolicies | Não | Não |
> | casos geridos / bases de dados / avaliações de vulnerabilidades | Não | Não |
> | casos geridos / definições métricas | Não | Não |
> | casos geridos / métricas | Não | Não |
> | casos geridos / bases de dados recuperáveis | Não | Não |
> | casos geridos / tdecertificates | Não | Não |
> | casos geridos / avaliações de vulnerabilidades | Não | Não |
> | operações | Não | Não |
> | servidores | Sim | Sim |
> | servidores / administradores resultados de operação | Não | Não |
> | servidores / administradores | Não | Não |
> | servidores /conselheiros | Não | Não |
> | servidores / base de dados agregada | Não | Não |
> | servidores / políticas de auditoria | Não | Não |
> | servidores / definições de auditoria | Não | Não |
> | servidores / afinação automática | Não | Não |
> | servidores / ligações de comunicação | Não | Não |
> | servidores / políticas de conexão | Não | Não |
> | servidores / bases de dados | Sim | Sim |
> | servidores / bases de dados / conselheiros | Não | Não |
> | servidores / bases de dados / políticas de auditoria | Não | Não |
> | servidores / bases de dados / definições de auditoria | Não | Não |
> | servidores / bases de dados / registos de auditoria | Não | Não |
> | servidores / bases de dados / afinação automática | Não | Não |
> | servidores / bases de dados / backuplongretentionpolicies | Não | Não |
> | servidores / bases de dados / backupshorttermretentionpolicies | Não | Não |
> | servidores / bases de dados / políticas de conexão | Não | Não |
> | servidores / bases de dados / políticas de masking de dados | Não | Não |
> | servidores / bases de dados / datamaskingpolicies / regras | Não | Não |
> | servidores / bases de dados / extensões | Não | Não |
> | servidores / bases de dados / geopolítica | Não | Não |
> | servidores / bases de dados / definições métricas | Não | Não |
> | servidores / bases de dados / métricas | Não | Não |
> | servidores / bases de dados / rótulos de sensibilidade recomendados | Não | Não |
> | servidores / bases de dados / políticas de alerta de segurança | Não | Não |
> | servidores / bases de dados / syncgroups | Não | Não |
> | servidores / bases de dados / syncgroups / syncmembers | Não | Não |
> | servidores / bases de dados / topqueries | Não | Não |
> | servidores / bases de dados / topqueries / questiontext | Não | Não |
> | servidores / bases de dados / encriptação transparente de dataencriptação | Não | Não |
> | servidores / bases de dados / avaliação de vulnerabilidade | Não | Não |
> | servidores / bases de dados / avaliações de vulnerabilidades | Não | Não |
> | servidores / bases de dados / vulnerabilidades | Não | Não |
> | servidores / bases de dados / definições de avaliação de vulnerabilidades | Não | Não |
> | servidores / bases de dados / grupos de carga | Não | Não |
> | servidores / políticas de segurança de base de dados | Não | Não |
> | servidores / configuração de recuperação de desastres | Não | Não |
> | servidores / dnsaliases | Não | Não |
> | servidores / elasticpoolestimates | Não | Não |
> | servidores / elásticos | Sim | Sim |
> | servidores / elásticos / conselheiros | Não | Não |
> | servidores / elásticos / definições métricas | Não | Não |
> | servidores / elásticos / métricas | Não | Não |
> | servidores / encriptaçãoprotetor | Não | Não |
> | servidores / definições de auditoria alargada | Não | Não |
> | servidores / failovergroups | Não | Não |
> | servidores / importação | Não | Não |
> | servidores / resultados de operações de exportação de importações | Não | Não |
> | servidores / contas de emprego | Sim | Sim |
> | servidores / agentes de trabalho | Sim | Sim |
> | servidores / agentes de trabalho / empregos | Não | Não |
> | servidores / agentes de trabalho / empregos / execuções | Não | Não |
> | servidores / agentes de trabalho / empregos / passos | Não | Não |
> | servidores / teclas | Não | Não |
> | servidores / resultados de operação | Não | Não |
> | servidores / piscinas elásticas recomendadas | Não | Não |
> | servidores / bases de dados recuperáveis | Não | Não |
> | servidores / restorabledropbases bases de dados | Não | Não |
> | servidores / políticas de alerta de segurança | Não | Não |
> | servidores / objetivos de serviço | Não | Não |
> | servidores / sincronia | Não | Não |
> | servidores / tdecertificates | Não | Não |
> | servidores / utilizações | Não | Não |
> | servidores / regras de rede virtual | Não | Não |
> | servidores / avaliações de vulnerabilidade | Não | Não |
> | aglomerados virtuais | Sim | Sim |

> [!IMPORTANT]
> Uma base de dados e um servidor devem estar no mesmo grupo de recursos. Quando se move um servidor SQL, todas as suas bases de dados também são movidas. Este comportamento aplica-se às bases de dados azure SQL e Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | locais | Não | Não |
> | localizações /disponibilidadegrouplisteneroperationresultados | Não | Não |
> | localizações / tipos de operação | Não | Não |
> | localizações / sqlvirtualmachinegroupoperationresults | Não | Não |
> | localizações / resultados de operação sqlvirtualmachineoperation | Não | Não |
> | operações | Não | Não |
> | grupos de máquinas sqlvirtual | Sim | Sim |
> | grupos de máquinas sqlvirtual / ouvintes do grupo de disponibilidade | Não | Não |
> | máquinas sqlvirtual | Sim | Sim |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dwvm | Não | Não |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | locais | Não | Não |
> | localizações / operações de sincronia | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / utilizações | Não | Não |
> | operações | Não | Não |
> | contas de armazenamento | Sim | Sim |
> | contas de armazenamento / blobservices | Não | Não |
> | contas de armazenamento / serviços de arquivo | Não | Não |
> | contas de armazenamento / listaccountsas | Não | Não |
> | contas de armazenamento / listservicesas | Não | Não |
> | contas de armazenamento / serviços de fila | Não | Não |
> | contas de armazenamento / serviços | Não | Não |
> | contas de armazenamento / serviços / definições métricas | Não | Não |
> | contas de armazenamento / serviços de mesa | Não | Não |
> | utilizações | Não | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | caches | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | locais | Não | Não |
> | localizações / checknamedisponibilidade | Não | Não |
> | localizações / fluxos de trabalho | Não | Não |
> | operações | Não | Não |
> | serviços de armazenamentos | Sim | Sim |
> | serviços de armazenamento /servidores registados | Não | Não |
> | serviços de armazenamento/syncgroups | Não | Não |
> | serviços de armazenamento / syncgroups / cloudendpoints | Não | Não |
> | serviços de armazenamento / syncgroups / pontos de terminação de servidores | Não | Não |
> | serviços de armazenamento / fluxos de trabalho | Não | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de armazenamentos | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de armazenamentos | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gestores | Não | Não |
> | operações | Não | Não |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Não | Não |
> | locais | Não | Não |
> | localizações / quotas | Não | Não |
> | operações | Não | Não |
> | streamingjobs | Sim | Sim |

> [!IMPORTANT]
> Os trabalhos da Stream Analytics não podem ser movidos quando estão em estado de gestão.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | ambientes | Não | Não |
> | ambientes / fontes de eventos | Não | Não |
> | instâncias | Não | Não |
> | instâncias / ambientes | Não | Não |
> | instâncias / ambientes / fontes de eventos | Não | Não |

## <a name="microsoftsubscription"></a>Microsoft.Subscrição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cancelar | Não | Não |
> | criar subscrição | Não | Não |
> | ativar | Não | Não |
> | resultados de operação | Não | Não |
> | operações | Não | Não |
> | mudar o nome | Não | Não |
> | definições de assinaturas | Não | Não |
> | operações de subscrição | Não | Não |
> | assinaturas | Não | Não |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | resultados de operação | Não | Não |
> | operações | Não | Não |
> | estado de operações | Não | Não |
> | services | Não | Não |
> | serviços /classificações de problemas | Não | Não |
> | bilhetes de apoio | Não | Não |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Não | Não |
> | operações | Não | Não |
> | áreas de trabalho | Sim | Sim |
> | espaços de trabalho / bigdatapools | Sim | Sim |
> | espaços de trabalho / resultados de operações | Não | Não |
> | espaços de trabalho / estados de funcionamento | Não | Não |
> | espaços de trabalho / sqlpools | Sim | Sim |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | registos de fornecedores | Não | Não |
> | resources | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | ambientes | Sim | Sim |
> | ambientes / políticas de acesso | Não | Não |
> | ambientes / fontes de eventos | Sim | Sim |
> | ambientes / conjuntos de dados de referência | Sim | Sim |
> | operações | Não | Não |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | lojas | Sim | Sim |
> | lojas / políticas de acesso | Não | Não |
> | lojas / serviços | Não | Não |
> | lojas / serviços / fichas | Não | Não |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | modelos de imagem | Não | Não |
> | operações | Não | Não |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | conta | Não | Não |
> | conta /extensão | Não | Não |
> | conta / projeto | Não | Não |
> | checknamedisponibilidade | Não | Não |
> | operações | Não | Não |

> [!IMPORTANT]
> Para alterar a subscrição do Azure DevOps, consulte [a alteração da subscrição Azure utilizada para a faturação](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | arczones | Não | Não |
> | locais | Não | Não |
> | localizações / estados de funcionamento | Não | Não |
> | operações | Não | Não |
> | recursos pools | Não | Não |
> | vcentros | Não | Não |
> | máquinas virtuais | Não | Não |
> | modelos de máquinas virtuais | Não | Não |
> | redes virtuais | Não | Não |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dedicados cloudnodes | Não | Não |
> | serviços dedicados em nuvem | Não | Não |
> | locais | Não | Não |
> | localizações / disponibilidades | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | locais / nuvens privadas | Não | Não |
> | localizações / nuvens privadas / recursos | Não | Não |
> | localizações / nuvens privadas / modelos de máquinas virtuais | Não | Não |
> | localizações / nuvens privadas / redes virtuais | Não | Não |
> | localizações / utilizações | Não | Não |
> | operações | Não | Não |
> | máquinas virtuais | Não | Não |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dispositivos | Não | Não |
> | locais | Não | Não |
> | localizações / estados de funcionamento | Não | Não |
> | operações | Não | Não |
> | vnfs | Não | Não |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | operações | Não | Não |
> | planos | Não | Não |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | pilhas disponíveis | Não | Não |
> | contadores de faturação | Não | Não |
> | certificados | Não | Sim |
> | checknamedisponibilidade | Não | Não |
> | connectiongateways | Sim | Sim |
> | conexões | Sim | Sim |
> | prismapa | Sim | Sim |
> | sites eliminados | Não | Não |
> | implantações | Não | Não |
> | geo-regiões | Não | Não |
> | ambientes de hospedagem | Não | Não |
> | ambientes de hospedagem / filtros de eventgrid | Não | Não |
> | ambientes de hospedagem / multirolepools | Não | Não |
> | ambientes de hospedagem / piscinas de trabalhadores | Não | Não |
> | ishostingenvironmentname disponível | Não | Não |
> | ishostname disponível | Não | Não |
> | isusername disponível | Não | Não |
> | kubeenvironments | Sim | Sim |
> | listsitesatribuídostohostname | Não | Não |
> | locais | Não | Não |
> | localizações / apioperations | Não | Não |
> | localizações / instalações de conexãogateway | Não | Não |
> | localizações / locais eliminados | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / extratosdefinições fromwsdl | Não | Não |
> | localizações / obterpolíticas de rede | Não | Não |
> | localizações / listwsdlinterfaces | Não | Não |
> | localizações / managedapis | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | locais/operações | Não | Não |
> | localizações / tempos de execução | Não | Não |
> | operações | Não | Não |
> | utilizadores editoriais | Não | Não |
> | recomendações | Não | Não |
> | recursossshealthmetametais | Não | Não |
> | tempos de execução | Não | Não |
> | servidores fazendas | Sim | Sim |
> | serverfarms / eventgridfilters | Não | Não |
> | sites | Sim | Sim |
> | sites / eventgridfilters | Não | Não |
> | sites / hostnamebindings | Não | Não |
> | sites / networkconfig | Não | Não |
> | sites / premieraddons | Sim | Sim |
> | sites / slots | Sim | Sim |
> | sites / slots / eventgridfilters | Não | Não |
> | sites / slots / hostnamebindings | Não | Não |
> | sites / slots / networkconfig | Não | Não |
> | controlos de fontes | Não | Não |
> | estaticos | Não | Não |
> | validar | Não | Não |
> | verificar hospedagemambientevnet | Não | Não |

> [!IMPORTANT]
> Consulte [a orientação de movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | locais | Não | Não |
> | localizações / estados de funcionamento | Não | Não |
> | múltiplas teclas de ativação | Não | Não |
> | operações | Não | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de dispositivos | Não | Não |
> | operações | Não | Não |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | componentes | Não | Não |
> | componentesssssumário | Não | Não |
> | monitorinstâncias | Não | Não |
> | monitorinstânciassummary | Não | Não |
> | monitores | Não | Não |
> | definições de notificações | Não | Não |
> | operações | Não | Não |

## <a name="third-party-services"></a>Serviços de terceiros

Atualmente, os serviços de terceiros não apoiam a operação de mudança.

## <a name="next-steps"></a>Passos seguintes
Para que os comandos movam recursos, consulte [mover recursos para um novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

Para obter os mesmos dados que um ficheiro de valores separados de vírem, descarregue [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
