# [Documentação do Service Fabric](/azure/service-fabric)
# Descrição geral
## [O que é o Service Fabric?](service-fabric-overview.md)

# Inícios rápidos
## [Criar uma aplicação .NET](service-fabric-quickstart-dotnet.md)
## [Implementar uma aplicação do contentor do Linux](service-fabric-quickstart-containers-linux.md)
## [Implementar uma aplicação do contentor do Windows](service-fabric-quickstart-containers.md)
## Inícios Rápidos de Java
### [Implementar uma aplicação Spring Boot](service-fabric-quickstart-java-spring-boot.md)
### [Implementar uma aplicação do Reliable Services](service-fabric-quickstart-java-reliable-services.md)

# Tutoriais
## Implementar uma aplicação .NET
### [1- Criar uma aplicação .NET](service-fabric-tutorial-create-dotnet-app.md)
### [2- Implementar a aplicação](service-fabric-tutorial-deploy-app-to-party-cluster.md)
### [3 - Configurar CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
### [4- Monitorizar e Diagnosticar](service-fabric-tutorial-monitoring-aspnet.md)

## Colocar uma aplicação .NET existente num contentor
### [1- Implementar uma aplicação .NET com o Docker Compose](service-fabric-host-app-in-a-container.md)
### [2- Monitorizar o seu contentor](service-fabric-tutorial-monitoring-wincontainers.md)

## Criar uma aplicação de contentor do Linux
### [1- Criar imagens de contentor](service-fabric-tutorial-create-container-images.md)
### [2- Encapsular e implementar contentores](service-fabric-tutorial-package-containers.md)
### [3- Realizar ativação pós-falha e dimensionar](service-fabric-tutorial-containers-failover.md)

## Criar e gerir um cluster
### 1- Criar um cluster no Azure
#### [1a - Criar um cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
#### [1a - Criar um cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
### [2- Dimensionar o cluster](service-fabric-tutorial-scale-cluster.md)
### [3- Atualizar o runtime do cluster](service-fabric-tutorial-upgrade-cluster.md)
### [4- Implementar a Gestão de API com o Service Fabric](service-fabric-tutorial-deploy-api-management.md)

# Amostras
## [Exemplos de código](https://azure.microsoft.com/resources/samples/?service=service-fabric)
## [Azure PowerShell](service-fabric-powershell-samples.md)
## [CLI do Service Fabric](samples-cli.md)

# Conceitos
## [Compreender os microsserviços](service-fabric-overview-microservices.md)
## [Panorama geral](service-fabric-content-roadmap.md)
## [Cenários de aplicações](service-fabric-application-scenarios.md)
## [Padrões e cenários](service-fabric-patterns-and-scenarios.md)
## [Arquitetura](service-fabric-architecture.md)
## [Terminologia](service-fabric-technical-overview.md)

## [Modelos de programação suportados](service-fabric-choose-framework.md)
### [Contentores](service-fabric-containers-overview.md)
#### [Docker Compose (pré-visualização)](service-fabric-docker-compose.md)
#### [Governação de recursos](service-fabric-resource-governance.md)
### [Reliable Services](service-fabric-reliable-services-introduction.md)
#### [Ciclo de vida do Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
#### [Ciclo de vida do Reliable Services - Java](service-fabric-reliable-services-lifecycle-java.md)
#### [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
#### [Diretrizes e recomendações das Reliable Collections](service-fabric-reliable-services-reliable-collections-guidelines.md)
#### [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
#### [Transações e bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
#### [Fila do Reliable Concurrent](service-fabric-reliable-services-reliable-concurrent-queue.md)
#### [Serialização das Reliable Collections](service-fabric-reliable-services-reliable-collections-serialization.md)
#### [Elementos internos do Reliable State Manager e das Reliable Collections](service-fabric-reliable-services-reliable-collections-internals.md)
#### [Utilização avançada](service-fabric-reliable-services-advanced-usage.md)

### [Reliable Actors](service-fabric-reliable-actors-introduction.md)
#### [Arquitetura](service-fabric-reliable-actors-platform.md)
#### [Ciclo de vida e libertação da memória](service-fabric-reliable-actors-lifecycle.md)
#### [Gestão de estados](service-fabric-reliable-actors-state-management.md)
#### [Polimorfismo](service-fabric-reliable-actors-polymorphism.md)
#### [Reentrada](service-fabric-reliable-actors-reentrancy.md)
#### [Serialização do tipo](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

## Aplicações e serviços
### [Modelo de aplicação](service-fabric-application-model.md)
### [Aplicação e manifestos de serviço](service-fabric-application-and-service-manifests.md)
### [Modelo de alojamento](service-fabric-hosting-model.md)

### [Estado do serviço](service-fabric-concepts-state.md)
### [Criação de partições do serviço](service-fabric-concepts-partitioning.md)
### [Escalabilidade das aplicações](service-fabric-concepts-scalability.md)
### [Disponibilidade dos serviços](service-fabric-availability-services.md)
### [Ciclo de vida de réplica e de instância](service-fabric-concepts-replica-lifecycle.md)
### [Reconfiguração](service-fabric-concepts-reconfiguration.md)

### [Comunicação de serviços](service-fabric-connect-and-communicate-with-services.md)
#### [Serviço DNS](service-fabric-dnsservice.md)
#### [Proxy inverso](service-fabric-reverseproxy.md)
#### [Configurar o proxy inverso para uma comunicação segura](service-fabric-reverseproxy-configure-secure-communication.md)
#### [Diagnóstico de proxy inverso](service-fabric-reverse-proxy-diagnostics.md)
#### [Núcleo do ASP.NET](service-fabric-reliable-services-communication-aspnetcore.md)

### [Ciclo de vida da aplicação](service-fabric-application-lifecycle.md)
#### [Atualização da aplicação](service-fabric-application-upgrade.md)
##### [Configuração](service-fabric-visualstudio-configure-upgrade.md)
##### [Parâmetros da atualização da aplicação](service-fabric-application-upgrade-parameters.md)
##### [Serialização de dados em atualizações da aplicação](service-fabric-application-upgrade-data-serialization.md)
##### [Tópicos avançados das atualizações da aplicação](service-fabric-application-upgrade-advanced.md)
#### [Testar aplicações com a análise de falhas](service-fabric-testability-overview.md)
#### [A definição ImageStoreConnectionString](service-fabric-image-store-connection-string.md)

### [Recursos do serviço](service-fabric-service-manifest-resources.md)

## [Clusters](service-fabric-deploy-anywhere.md)
### [Segurança do cluster](service-fabric-cluster-security.md)
### [Diferenças de funcionalidades entre o Linux e o Windows](service-fabric-linux-windows-differences.md)
### Clusters no Azure
#### [Tipos de nós e Conjuntos de Dimensionamento de VMs](service-fabric-cluster-nodetypes.md)
#### [Padrões de redes de cluster](service-fabric-patterns-networking.md)
### [Gestor de recursos de cluster](service-fabric-cluster-resource-manager-introduction.md)
#### [Arquitetura](service-fabric-cluster-resource-manager-architecture.md)
#### [Descrever um cluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Descrição geral dos grupos de aplicações](service-fabric-cluster-resource-manager-application-groups.md)
#### [Configurar as definições do Resource Manager do Cluster](service-fabric-cluster-resource-manager-configure-services.md)
#### [Métricas de consumo de recursos](service-fabric-cluster-resource-manager-metrics.md)
#### [Utilizar as afinidades de serviço](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [Políticas de posicionamento de serviços](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [Gerir um cluster](service-fabric-cluster-resource-manager-management-integration.md)
#### [Desfragmentação do cluster](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [Balancear um cluster](service-fabric-cluster-resource-manager-balancing.md)
#### [Limitação](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [Movimento de serviço](service-fabric-cluster-resource-manager-movement-cost.md)

## Monitorização e diagnóstico
### [Monitorizar e diagnosticar aplicações](service-fabric-diagnostics-overview.md)
### Gerar eventos
#### [Gerar eventos de nível de plataforma](service-fabric-diagnostics-event-generation-infra.md)
##### [Canal operacional](service-fabric-diagnostics-event-generation-operational.md)
##### [Eventos do Reliable Services](service-fabric-reliable-services-diagnostics.md)
##### [Eventos de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
##### [Métricas de desempenho](service-fabric-diagnostics-event-generation-perf.md)
##### [Serviço de Monitorização Remota](service-fabric-reliable-serviceremoting-diagnostics.md)
#### [Gerar eventos ao nível de aplicação](service-fabric-diagnostics-event-generation-app.md)
### Inspecionar estado de funcionamento da aplicação e do cluster
#### [Monitorizar o estado de funcionamento do Service Fabric](service-fabric-health-introduction.md)
#### [Comunicar e verificar o estado de funcionamento dos serviços](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
#### [Adicionar relatórios de estado de funcionamento personalizados](service-fabric-report-health.md)
#### [Resolver problemas com relatórios de estado de funcionamento do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
#### [Ver relatórios de estado de funcionamento](service-fabric-view-entities-aggregated-health.md)
### Agregar eventos
#### [Agregar eventos com o EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)
#### Agregar eventos com o Diagnóstico do Azure
##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
### Analisar eventos
#### [Analisar eventos com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
#### [Analisar eventos com o OMS](service-fabric-diagnostics-event-analysis-oms.md)
### [Resolver problemas do seu cluster local](service-fabric-troubleshoot-local-cluster-setup.md)

## [Integrar com a Gestão de API](service-fabric-api-management-overview.md)

# Guias de procedimentos
## Configurar o ambiente de desenvolvimento
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
### [Configurar a CLI do Service Fabric](service-fabric-cli.md)

## Planear e preparar
### [Planear a capacidade do cluster](service-fabric-cluster-capacity.md)
### [Plano para a implementação de clusters autónomos](service-fabric-cluster-standalone-deployment-preparation.md)
### [Preparar para a recuperação após desastre](service-fabric-disaster-recovery.md)
### [Planear a capacidade da aplicação](service-fabric-capacity-planning.md)

## Criar a sua primeira...
### [Aplicação C# no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
### [Aplicação de contentor do Windows](service-fabric-get-started-containers.md)
### [Aplicação de contentor do Linux](service-fabric-get-started-containers-linux.md)
### [Aplicação de Reliable Services em C# no Windows](service-fabric-reliable-services-quick-start.md)
### [Aplicação de Reliable Services em Java no Linux](service-fabric-reliable-services-quick-start-java.md)
### [Aplicação de Reliable Services em C# no Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
### [Aplicação de Reliable Actors em C# no Windows](service-fabric-reliable-actors-get-started.md)
### [Aplicação de Reliable Actors em Java no Linux](service-fabric-create-your-first-linux-application-with-java.md)
### [Aplicação executável de convidado no Windows](quickstart-guest-app.md)
### [Cluster autónomo](service-fabric-get-started-standalone-cluster.md)

## Criar uma aplicação

### Criar um serviço executável convidado
#### [Implementar um executável convidado](service-fabric-deploy-existing-app.md)
#### [Implementar vários executáveis convidados](service-fabric-deploy-multiple-apps.md)
### Criar um serviço de contentor
#### [Segurança do contentor](service-fabric-securing-containers.md)
#### [Docker Compose (pré-visualização)](service-fabric-docker-compose.md)
#### [Governação de recursos para contentores e serviços](service-fabric-resource-governance.md)
#### [Controladores de volume e registo](service-fabric-containers-volume-logging-drivers.md)
#### [Serviços no interior de contentores](service-fabric-services-inside-containers.md)
#### [Modos de funcionamento em rede do contentor](service-fabric-networking-modes.md)

### Criar um serviço do Reliable Services
#### Reliable Collections
##### [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [Fila do Reliable Concurrent](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Serialização das Reliable Collections](service-fabric-reliable-services-reliable-collections-serialization.md)

#### Comunicar com serviços
##### [Comunicar com o Reliable Services](service-fabric-reliable-services-communication.md)

##### [Comunicação Remota do Serviço - C#](service-fabric-reliable-services-communication-remoting.md)
##### [Comunicação Remota do Serviço - Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Comunicações seguras - C#](service-fabric-reliable-services-secure-communication.md)
##### [Comunicações seguras - Java](service-fabric-reliable-services-secure-communication-java.md)

#### [Configurar](service-fabric-reliable-services-configuration.md)
#### [Enviar notificações](service-fabric-reliable-services-notifications.md)
#### [Backup e restauro](service-fabric-reliable-services-backup-restore.md)

### Criar um serviço do Reliable Actors
#### [Enviar notificações](service-fabric-reliable-actors-events.md)
#### [Definir temporizadores e lembretes](service-fabric-reliable-actors-timers-reminders.md)
#### [Configurar KvsActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Configurar dedinições de comunicações](service-fabric-reliable-actors-fabrictransportsettings.md)
#### [Configurar ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### [Migrar a aplicação de Java antiga para suportar Maven](service-fabric-migrate-old-javaapp-to-use-maven.md)

### [Configurar o proxy inverso para uma comunicação segura](service-fabric-reverseproxy-configure-secure-communication.md)

### [Criar um serviço ASP.NET Core](service-fabric-add-a-web-frontend.md)

### Configurar a segurança
#### [Gerir segredos da aplicação](service-fabric-application-secret-management.md)  
#### [Configurar políticas de segurança para a sua aplicação](service-fabric-application-runas-security.md)

## Trabalhar num ambiente de desenvolvimento do Windows/VS
### [Gerir aplicações no Visual Studio](service-fabric-manage-application-in-visual-studio.md)
### [Configurar ligações seguras no Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
### [Configurar a sua aplicação para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)
### [Depurar um serviço .NET no VS](service-fabric-debugging-your-application.md)
### [Erros comuns e exceções](service-fabric-errors-and-exceptions.md)
### [Monitorizar e diagnosticar localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
### [Configurar um cluster do Linux no Windows](service-fabric-local-linux-cluster-windows.md)

## Trabalhar num ambiente de desenvolvimento do Linux/Eclipse
### [Introdução ao plug-in do Eclipse para desenvolvimento Java](service-fabric-get-started-eclipse.md)
### [Depurar um serviço Java no Eclipse](service-fabric-debugging-your-application-java.md)
### [Monitorizar e diagnosticar localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## Migrar a partir dos Serviços Cloud
### [Comparar os Serviços Cloud com o Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Migrar para o Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [Práticas recomendadas](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Gerir o ciclo de vida da aplicação
### [Criar pacote de uma aplicação](service-fabric-package-apps.md)

### Implementar ou remover aplicações
#### [Implementar aplicações num cluster local](service-fabric-get-started-with-a-local-cluster.md)
#### [Azure Resource Manager](service-fabric-application-arm-resource.md)
#### [Azure PowerShell](service-fabric-deploy-remove-applications.md)
#### [CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md)
#### [APIs FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

### Atualizar aplicações
#### [Atualizar com o Azure PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Atualizar com o Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [Resolver problemas da atualização da aplicação](service-fabric-application-upgrade-troubleshooting.md)

### Testar aplicações e serviços
#### [Testar a comunicação entre serviços](service-fabric-testability-scenarios-service-communication.md)
#### Simular falhas
##### [Utilizar Chaos controlado](service-fabric-controlled-chaos.md)
##### [Utilizar ações de teste](service-fabric-testability-actions.md)
##### [Durante cargas de trabalho](service-fabric-testability-workload-tests.md)
##### [Utilizar cenários de teste](service-fabric-testability-scenarios.md)
##### [Utilizar as APIs de transição de nó](service-fabric-node-transition-apis.md)

### Configurar a integração contínua
#### [Configurar a integração contínua com o VSTS](service-fabric-set-up-continuous-integration.md)
#### [Implementar as suas aplicações do Linux com o Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)

## Criar e gerir clusters
### Clusters no Azure
#### Criar
##### [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
#### Escala
##### [Manualmente](service-fabric-cluster-scale-up-down.md)
##### [Através de programação](service-fabric-cluster-programmatic-scaling.md)
#### [Atualizar](service-fabric-cluster-upgrade.md)
#### [Definir controlo de acesso](service-fabric-cluster-security-roles.md)
#### [Configurar](service-fabric-cluster-fabric-settings.md)
#### [Abrir uma porta no balanceador de carga](create-load-balancer-rule.md)
#### [Gerir certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)
#### [Eliminar](service-fabric-cluster-delete.md)

### Clusters autónomos
#### Criar
##### [Criar no local](service-fabric-cluster-creation-for-windows-server.md)
##### [Segurança através de certificados](service-fabric-windows-cluster-x509-security.md)  
##### [Segurança através da segurança do Windows](service-fabric-windows-cluster-windows-security.md)
##### [Conteúdo do pacote autónomo](service-fabric-cluster-standalone-package-contents.md)
#### [Dimensionamento](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Definir controlo de acesso](service-fabric-cluster-security-roles.md)
#### [Configurar](service-fabric-cluster-manifest.md)
#### [Atualizar](service-fabric-cluster-upgrade-windows-server.md)

### [Visualizar um cluster](service-fabric-visualizing-your-cluster.md)
### [Ligar a um cluster seguro](service-fabric-connect-to-secure-cluster.md)
### [Aplicar o patch em nós de cluster](service-fabric-patch-orchestration-application.md)

## Monitorizar e diagnosticar
### OMS
#### [Configurar o Log Analytics do OMS](service-fabric-diagnostics-oms-setup.md)
#### [Adicionar Agente do OMS](service-fabric-diagnostics-oms-agent.md)
#### [Contentores de monitor](service-fabric-diagnostics-oms-containers.md)
### Monitorização de desempenho
#### [Monitorização de desempenho com WAD](service-fabric-diagnostics-perf-wad.md)

# Referência
## [Azure PowerShell](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [CLI do Azure (az sf)](/cli/azure/sf)
## [CLI do Service Fabric (sfctl)](service-fabric-sfctl.md)
### [sfctl application](service-fabric-sfctl-application.md)
### [sfctl chaos](service-fabric-sfctl-chaos.md)
### [sfctl cluster](service-fabric-sfctl-cluster.md)
### [sfctl compose](service-fabric-sfctl-compose.md)
### [sfctl is](service-fabric-sfctl-is.md)
### [sfctl node](service-fabric-sfctl-node.md)
### [sfctl partition](service-fabric-sfctl-partition.md)
### [sfctl replica](service-fabric-sfctl-replica.md)
### [sfctl rpm](service-fabric-sfctl-rpm.md)
### [sfctl service](service-fabric-sfctl-service.md)
### [sfctl store](service-fabric-sfctl-store.md)
## [API de Java](/java/api/overview/azure/servicefabric)
## [.NET](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet)
## [REST](/rest/api/servicefabric)
## [Esquema XML do modelo de serviço](service-fabric-service-model-schema.md)

# Recursos
## [Mapa do Azure](https://azure.microsoft.com/roadmap/)
## [Perguntas comuns](service-fabric-common-questions.md)
## [Percurso de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Preços](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)
## [Código de exemplo](http://aka.ms/servicefabricsamples)
## [Opções de suporte](service-fabric-support.md)
## [Atualizações de Serviço](https://azure.microsoft.com/updates/?product=service-fabric)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)